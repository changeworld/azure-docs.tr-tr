---
title: 'Öğretici: Konteyner ile Eski uygulama (Önizleme)'
description: Özel bir Windows kapsayıcısını Azure Uygulama Hizmeti'ne nasıl geçirtecekve kapsayıcıda özel yazılım dağıtmayı öğrenin.
ms.topic: tutorial
ms.date: 10/22/2019
ms.custom: mvc, seodec18
ms.openlocfilehash: 74cb88bc1ace87155a35163ca8f9d3d6c4242ae0
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/24/2020
ms.locfileid: "80046611"
---
# <a name="migrate-an-aspnet-app-to-azure-app-service-using-a-windows-container-preview"></a>Windows kapsayıcısı kullanarak bir ASP.NET uygulamasını Azure App Service'e geçirme (Önizleme)

[Azure App Service](overview.md), Windows'da IIS üzerinde çalışan ASP.NET veya Node.js gibi önceden tanımlı uygulama yığınları sunar. Önceden yapılandırılmış Windows ortamı, işletim sistemini yönetimsel erişime, yazılım yüklemesine ve genel derleme önbelleğine ve benzeri uygulamalara karşı kilitler (bkz. [Azure App Service'teki işletim sistemi işlevleri](operating-system-functionality.md)). Ancak App Service içinde özel bir Windows kapsayıcısı kullanmak, uygulamanızın ihtiyaç duyduğu işletim sistemi değişikliklerini kolayca gerçekleştirmenizi sağlar. Bu sayede özel işletim sistemi ve yazılım yapılandırmasına ihtiyaç duyan bir şirket içi uygulamayı geçirmek oldukça kolaydır. Bu öğreticide Windows yazı tipi kitaplığında yüklü olan özel yazı tiplerini kullanan bir ASP.NET uygulamasını App Service'e geçirme adımları gösterilmektedir. Visual Studio'dan [Azure Container Registry](https://docs.microsoft.com/azure/container-registry/)'ye özel olarak yapılandırılmış bir Windows görüntüsü dağıtıp ardından bunu App Service'te çalıştıracaksınız.

![](media/app-service-web-tutorial-windows-containers-custom-fonts/app-running.png)

## <a name="prerequisites"></a>Ön koşullar

Bu öğreticiyi tamamlamak için:

- <a href="https://hub.docker.com/" target="_blank">Docker Hub hesabı için kaydolma</a>
- <a href="https://docs.docker.com/docker-for-windows/install/" target="_blank">Docker for Windows'u yükleyin</a>.
- <a href="https://docs.microsoft.com/virtualization/windowscontainers/quick-start/quick-start-windows-10" target="_blank">Windows kapsayıcılarını çalıştırmak için Docker’a geçiş yapın</a>.
- Visual <a href="https://www.visualstudio.com/downloads/" target="_blank">Studio 2019'u</a> **ASP.NET ve web geliştirme ve** Azure **geliştirme** iş yükleriyle yükleyin. Visual Studio 2019'u zaten yüklediyseniz:
    - **Güncellemeler için** **Yardım** > Denetimi'ni tıklatarak Visual Studio'daki en son güncellemeleri yükleyin.
    - **Araçlar** > **Al Araçları ve Özellikleri'ni**tıklatarak Visual Studio'daki iş yüklerini ekleyin.

## <a name="set-up-the-app-locally"></a>Uygulamayı yerel ortamda oluşturma

### <a name="download-the-sample"></a>Örneği indirme

Bu adımda yerel .NET projesini oluşturacaksınız.

- [Örnek projeyi indirin](https://github.com/Azure-Samples/custom-font-win-container/archive/master.zip).
- *custom-font-win-container.zip* dosyasını açın.

Örnek projede Windows yazı tipi kitaplığına yüklenmiş olan özel bir yazı tipini kullanan basit bir ASP.NET uygulaması bulunmaktadır. Yazı tiplerini yüklemenize gerek yoktur işletim sistemine tümleşik bir uygulama örneği olarak verilmiştir. Bu tür bir uygulamayı App Service'e geçirmek için kodunuzu yeniden düzenleyip tümleştirmeyi kaldırabilir veya özel Windows kapsayıcısında olduğu için olduğu şekilde geçirebilirsiniz.

### <a name="install-the-font"></a>Yazı tipini yükleme

Windows Gezgini'nde _custom-font-win-container-master/CustomFontSample_ dizinine gidin, _FrederickatheGreat-Regular.ttf_ dosyasına sağ tıklayın ve **Yükle**'yi seçin.

Bu yazı tipi [Google Fonts](https://fonts.google.com/specimen/Fredericka+the+Great) sayfasında genel kullanıma açık bir şekilde sunulmaktadır.

### <a name="run-the-app"></a>Uygulamayı çalıştırma

*custom-font-win-container/CustomFontSample.sln* dosyasını Visual Studio'da açın. 

Uygulamayı hata ayıklaması yapılmadan çalıştırmak için `Ctrl+F5` yazın. Uygulama varsayılan tarayıcınızda görüntülenir. 

![Yeni ASP.NET Projesi iletişim kutusu](media/app-service-web-tutorial-windows-containers-custom-fonts/local-app-in-browser.png)

Yüklenmiş olan bir yazı tipini kullandığından uygulama App Service korumalı alanında çalışmayacaktır. Ancak yazı tipini Windows kapsayıcısına yükleyebileceğiniz için uygulamayı Windows kapsayıcısı kullanarak dağıtabilirsiniz.

### <a name="configure-windows-container"></a>Windows kapsayıcısını yapılandırma

Çözüm Gezgini'nde **CustomFontSample** projesine sağ tıklayıp **Ekle** > **Kapsayıcı Düzenleme Desteği**'ne tıklayın.

![Yeni ASP.NET Projesi iletişim kutusu](media/app-service-web-tutorial-windows-containers-custom-fonts/enable-container-orchestration.png)

**Docker Compose** > **Ok'u**seçin.

Projeniz Windows kapsayıcısında çalışacak şekilde ayarlanır. **CustomFontSample** projesine bir _Dockerfile_ ve çözüme bir **docker-compose** projesi eklenir. 

Çözüm Gezgini'nden **Dockerfile** dosyasını açın.

[Desteklenen bir üst görüntü](app-service-web-get-started-windows-container.md#use-a-different-parent-image) kullanmanız gerekir. `FROM` satırını aşağıdaki kod ile değiştirerek üst görüntüyü değiştirin:

```Dockerfile
FROM mcr.microsoft.com/dotnet/framework/aspnet:4.7.2-windowsservercore-ltsc2019
```

Dosyanın en sonuna şu satırı ekleyin ve dosyayı kaydedin:

```Dockerfile
RUN ${source:-obj/Docker/publish/InstallFont.ps1}
```

_InstallFont.ps1_ dosyasını **CustomFontSample** projesinde bulabilirsiniz. Yazı tipini yükleyen basit bir betiktir. Betiğin daha karmaşık sürümünü [Betik Merkezi](https://gallery.technet.microsoft.com/scriptcenter/fb742f92-e594-4d0c-8b79-27564c575133) sayfasında bulabilirsiniz.

> [!NOTE]
> Windows kapsayıcısını yerel olarak sınamak için Docker'ın yerel makinenizde başlatıldıklıolduğundan emin olun.
>

## <a name="publish-to-azure-container-registry"></a>Azure Container Registry'de yayımlama

[Azure Container Registry](https://docs.microsoft.com/azure/container-registry/), kapsayıcı dağıtımlarınızın görüntülerini depolayabilir. App Service'i Azure Container Registry'de barındırılan görüntüleri kullanacak şekilde yapılandırabilirsiniz.

### <a name="open-publish-wizard"></a>Yayımlama sihirbazını açma

Çözüm Gezgini'nde **CustomFontSample** projesine sağ tıklayın ve **Yayımla**'yı seçin.

![Yeni ASP.NET Projesi iletişim kutusu](media/app-service-web-tutorial-windows-containers-custom-fonts/open-publish-wizard.png)

### <a name="create-registry-and-publish"></a>Kayıt defterini oluşturma ve yayımlama

Yayımlama sihirbazında, **Konteyner Kayıt Defteri** > Oluştur Yeni Azure Kapsayıcı Kayıt Defteri Yayımla'yı**Create New Azure Container Registry** > **Publish**seçin.

![Yeni ASP.NET Projesi iletişim kutusu](media/app-service-web-tutorial-windows-containers-custom-fonts/create-registry.png)

### <a name="sign-in-with-azure-account"></a>Azure hesabınızla oturum açın

**Yeni Azure Container Registry oluştur** iletişim kutusunda **Hesap ekle**’yi seçin ve Azure aboneliğinizde oturum açın. Oturumunuz zaten açıksa, açılan menüden istediğiniz aboneliği içeren hesabı seçin.

![Azure'da oturum açma](./media/app-service-web-tutorial-windows-containers-custom-fonts/add-an-account.png)

### <a name="configure-the-registry"></a>Kayıt defterini yapılandırma

Yeni kapsayıcı kayıt defterini aşağıdaki tabloda bulunan değerleri kullanarak yapılandırın. Tamamladığınızda **Oluştur**’a tıklayın.

| Ayar  | Önerilen değer | Daha fazla bilgi edinmek için |
| ----------------- | ------------ | ----|
|**DNS Ön Eki**| Oluşturulan kayıt defteri adını kullanın veya benzersiz bir adla değiştirin. |  |
|**Kaynak Grubu**| **Yeni**'ye tıklayın, **myResourceGroup** yazın ve **Tamam**'a tıklayın. |  |
|**Sku**| Temel | [Fiyatlandırma katmanları](https://azure.microsoft.com/pricing/details/container-registry/)|
|**Kayıt Defteri Konumu**| Batı Avrupa | |

![Azure Container Registry yapılandırması](./media/app-service-web-tutorial-windows-containers-custom-fonts/configure-registry.png)

Bir terminal penceresi açılır ve görüntü dağıtımı ilerleme durumunu görüntüler. Dağıtımın tamamlanmasını bekleyin.

## <a name="sign-in-to-azure"></a>Azure'da oturum açma

https://portal.azure.com adresinden Azure portalında oturum açın.

## <a name="create-a-web-app"></a>Web uygulaması oluşturma

Sol menüden**Kapsayıcılar için**kaynak > **Web** > Web Uygulaması **Oluştur'u**seçin.

### <a name="configure-app-basics"></a>Uygulama temellerini yapılandırma

Temel **Bilgiler** sekmesinde, ayarları aşağıdaki tabloya göre yapılandırın ve **ardından İleri: Docker'ı**tıklatın.

| Ayar  | Önerilen değer | Daha fazla bilgi edinmek için |
| ----------------- | ------------ | ----|
|**Abonelik**| Doğru aboneliğin seçildiğinden emin olun. |  |
|**Kaynak Grubu**| **Yeni Oluştur'u**seçin, **myResourceGroup**yazın ve **Tamam'ı**tıklatın. |  |
|**Adı**| Benzersiz bir ad yazın. | Web uygulamasının URL'si `http://<app-name>.azurewebsites.net` şeklindedir; burada `<app-name>`, uygulamanızın adıdır. |
|**Yayımlamak**| Docker konteyner | |
|**İşletim Sistemi**| Windows | |
|**Bölge**| Batı Avrupa | |
|**Windows Planı**| **Yeni Oluştur'u**seçin , **myAppServicePlan**yazın ve **Tamam'ı**tıklatın. | |

**Temel bilgiler** sekmeniz şu şekilde görünmelidir:

![](media/app-service-web-tutorial-windows-containers-custom-fonts/configure-app-basics.png)

### <a name="configure-windows-container"></a>Windows kapsayıcısını yapılandırma

**Docker** sekmesinde, aşağıdaki tabloda gösterildiği gibi özel Windows kapsayıcınızı yapılandırın ve **Gözden Geçir + oluştur'u**seçin.

| Ayar  | Önerilen değer |
| ----------------- | ------------ |
|**Görüntü Kaynağı**| Azure Kapsayıcı Kaydı |
|**Kayıt defteri**| [Daha önce oluşturduğunuz kayıt defterini](#publish-to-azure-container-registry)seçin. |
|**Görüntü**| customfontsample |
|**Etiket**| en son |

### <a name="complete-app-creation"></a>Uygulama oluşturmayı tamamlama

**Oluştur**'a tıklayın ve Azure'un gereken kaynakları oluşturmasını bekleyin.

## <a name="browse-to-the-web-app"></a>Web uygulamasına göz atma

Azure işlemi tamamlandığında bir bildirim kutusu görüntülenir.

![](media/app-service-web-tutorial-windows-containers-custom-fonts/portal-create-finished.png)

1. **Kaynağa git**'e tıklayın.

2. Uygulama sayfasında **URL**'nin altındaki bağlantıya tıklayın.

Aşağıdaki sayfayla yeni bir tarayıcı sayfası açılır:

![](media/app-service-web-tutorial-windows-containers-custom-fonts/app-starting.png)

Birkaç dakika bekleyin ve beklediğiniz güzel yazı tipine sahip giriş sayfası açılana kadar yeniden deneyin:

![](media/app-service-web-tutorial-windows-containers-custom-fonts/app-running.png)

**Tebrikler!** Bir ASP.NET uygulamasını Windows kapsayıcısında Azure App Service'e geçirdiniz.

## <a name="see-container-start-up-logs"></a>Kapsayıcı başlangıç günlüklerini inceleme

Windows kapsayıcısının yüklenmesi biraz zaman alabilir. İlerlemeyi görmek için, * \<uygulama adı>* uygulamanızın adını uygulamanızın adıyla değiştirerek aşağıdaki URL'ye gidin.
```
https://<app-name>.scm.azurewebsites.net/api/logstream
```

Akışı yapılan günlükler şuna benzer:

```
14/09/2018 23:16:19.889 INFO - Site: fonts-win-container - Creating container for image: customfontsample20180914115836.azurecr.io/customfontsample:latest.
14/09/2018 23:16:19.928 INFO - Site: fonts-win-container - Create container for image: customfontsample20180914115836.azurecr.io/customfontsample:latest succeeded. Container Id 329ecfedbe370f1d99857da7352a7633366b878607994ff1334461e44e6f5418
14/09/2018 23:17:23.405 INFO - Site: fonts-win-container - Start container succeeded. Container: 329ecfedbe370f1d99857da7352a7633366b878607994ff1334461e44e6f5418
14/09/2018 23:17:28.637 INFO - Site: fonts-win-container - Container ready
14/09/2018 23:17:28.637 INFO - Site: fonts-win-container - Configuring container
14/09/2018 23:18:03.823 INFO - Site: fonts-win-container - Container ready
14/09/2018 23:18:03.823 INFO - Site: fonts-win-container - Container start-up and configuration completed successfully
```

