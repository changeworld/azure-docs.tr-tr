---
title: Quickstart - Azure Service Fabric Mesh'e bir web uygulaması dağıtma
description: Bu hızlı başlangıç, visual studio kullanarak bir ASP.NET Core web sitesi oluşturmanızı ve Azure Service Fabric Mesh'te nasıl yayınlayacağınızı gösterir.
author: dkkapur
ms.topic: quickstart
ms.date: 07/17/2018
ms.author: dekapur
ms.custom: mvc, devcenter
ms.openlocfilehash: 276f8860f407693a7ff08048399fa6edf16aaf0a
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/26/2020
ms.locfileid: "75459076"
---
# <a name="quickstart-create-and-deploy-a-web-app-to-azure-service-fabric-mesh"></a>Hızlı Başlangıç: Web uygulaması oluşturup Azure Service Fabric Mesh’e dağıtma

Azure Service Fabric Mesh, geliştiricilerin sanal makineleri, depolama alanını veya ağ bileşenlerini yönetmeden mikro hizmet uygulamaları dağıtmasını sağlayan tam olarak yönetilen bir hizmettir.

Bu hızlı başlangıçta, ASP.NET Core web uygulamasından oluşan yeni bir Service Fabric Mesh uygulaması oluşturacak, bunu yerel geliştirme kümesinde çalıştıracak ve Azure üzerinde çalıştırmak üzere yayımlayacaksınız.

Bir Azure aboneliğine sahip olmanız gerekir. Azure aboneliğiniz yoksa başlamadan önce kolayca ücretsiz bir Azure aboneliği oluşturabilirsiniz [ücretsiz hesap oluşturun](https://azure.microsoft.com/free/). Ayrıca [geliştirici ortamınızı ayarlamanız](service-fabric-mesh-howto-setup-developer-environment-sdk.md) da gerekecektir

[!INCLUDE [preview note](./includes/include-preview-note.md)]

## <a name="create-a-service-fabric-mesh-project"></a>Service Fabric Mesh projesi oluşturma

Visual Studio'u açın ve **Dosya** > **Yeni** > **Projesi'ni seçin...**

**Yeni Proje** iletişim kutusunun en üstündeki **Ara** kutusuna `mesh` yazın. **Service Fabric Mesh Uygulaması** şablonunu seçin. (Bu şablonu görmüyorsanız [geliştirme ortamınızı ayarlama](service-fabric-mesh-howto-setup-developer-environment-sdk.md) bölümünde anlatılan şekilde Mesh SDK'sını ve VS araçları önizleme sürümünü yüklediğinizden emin olun.) 

**Ad** kutusuna **ServiceFabricMesh1** yazın ve **Konum** kutusunda projenin dosyalarının depolanacağı klasör yolunu ayarlayın.

**Çözüm için dizin oluştur** kutusunun işaretli olduğundan emin olun ve **Tamam**'a tıklayarak Service Fabric Mesh projesini oluşturun.

![Visual Studio yeni Service Fabric Mesh projesi iletişim kutusu](media/service-fabric-mesh-quickstart-dotnet-core/visual-studio-new-project.png)

### <a name="create-a-service"></a>Hizmet oluşturma

**Tamam**’a tıklamanızın ardından **Yeni Service Fabric Hizmeti** iletişim kutusu görüntülenir. **ASP.NET Core** proje türünü seçin, **Kapsayıcı işletim sistemi**’nin **Windows** olarak ayarlandığından emin olun ve **Tamam**’a tıklayarak ASP.NET Core projesini oluşturun. 

![Visual Studio yeni Service Fabric Mesh projesi iletişim kutusu](media/service-fabric-mesh-quickstart-dotnet-core/visual-studio-new-service-fabric-service.png)

**Yeni ASP.NET Core Web Uygulaması** iletişim kutusu görüntülenir. **Web Uygulaması**’nı seçin ve **Tamam**’a tıklayın.

![Visual Studio yeni ASP.NET Core uygulaması](media/service-fabric-mesh-quickstart-dotnet-core/visual-studio-new-aspnetcore-app.png)

Visual Studio, Service Fabric Mesh uygulama projesini ve ASP.NET Core projesini oluşturur.

## <a name="build-and-publish-to-your-local-cluster"></a>Derleme ve yerel kümenizde yayımlama

Projeniz yüklendiği anda Docker görüntüsü otomatik olarak derlenir ve yerel kümenizde yayımlanır. Bu işlem biraz zaman alabilir. **Çıktı** açılır penceresinde **Service Fabric Araçları** öğesini seçerek isterseniz **Çıktı** penceresinde Service Fabric araçlarının ilerleme durumunu izleyebilirsiniz. Docker görüntüsü dağıtılırken çalışmaya devam edebilirsiniz.

Proje oluşturulduktan sonra yerel olarak hizmetinizin hatalarını ayıklamak için **F5** tuşuna basın. Yerel dağıtım bittiğinde ve Visual Studio projenizi çalıştırırken bir tarayıcı penceresi örnek bir web sayfası ile açılır.

Dağıtılan hizmete göz atma işleminiz bittiğinde, Visual Studio’da **Shift+F5** tuşlarına basarak projenizin hata ayıklamasını durdurun.

## <a name="publish-to-azure"></a>Azure’da Yayımlama

Service Fabric Mesh projenizi Azure’da yayımlamak için Visual Studio’da **Service Fabric Mesh projesi**’ne sağ tıklayıp **Yayımla...** öğesini seçin.

![Visual Studio’da Service Fabric Mesh projesine sağ tıklayın](media/service-fabric-mesh-quickstart-dotnet-core/visual-studio-right-click-publish.png)

**Service Fabric Uygulamasını Yayımla** iletişim kutusunu göreceksiniz.

![Visual Studio Service Fabric Mesh yayımla iletişim kutusu](media/service-fabric-mesh-quickstart-dotnet-core/visual-studio-publish-dialog.png)

Azure hesabınızı ve aboneliğinizi seçin. **Konum**seçin. Bu makalede **Doğu ABD** kullanılmıştır.

**Kaynak grubu** bölümünde **\<Yeni Kaynak Grubu Oluştur...>** öğesini seçin. **Kaynak Grubu Oluştur** iletişim kutusu görüntülenir. **Kaynak grubu adı**’nı ve **Konum**’u ayarlayın.  Bu hızlı başlangıçta, **Doğu ABD** konumu kullanılmış ve grup **sfmeshTutorial1RG** olarak adlandırılmıştır (kuruluşunuzda aynı aboneliği kullanan birden fazla kişi varsa benzersiz bir kaynak grubu adı seçin).  **Oluştur**'a tıklayarak kaynak grubunu oluşturun ve yayımla iletişim kutusuna geri dönün.

![Visual Studio Service Fabric Mesh yeni kaynak grubu iletişim kutusu](media/service-fabric-mesh-quickstart-dotnet-core/visual-studio-publish-new-resource-group-dialog.png)

**Service Fabric Uygulamasını Yayımla** iletişim kutusunun **Azure Container Registry** bölümünde **\<Yeni Azure Container Registry oluştur...>** öğesini seçin. **Container Registry Oluştur** iletişim kutusunda **Container registry adı** için benzersiz bir ad girin. Bir **Konum** belirtin (bu hızlı başlangıçta **Doğu ABD** kullanılmıştır). Açılan menüden önceki adımda oluşturduğunuz **Kaynak grubu** adını seçin, örneğin **sfmeshTutorial1RG**. **SKU**’yu **Temel** olarak ayarlayın ve ardından **Oluştur**'a tıklayarak yayımla iletişim kutusuna geri dönün.

![Visual Studio Service Fabric Mesh yeni kaynak grubu iletişim kutusu](media/service-fabric-mesh-quickstart-dotnet-core/visual-studio-publish-new-container-registry-dialog.png)

Yayımla iletişim kutusunda **Yayımla** düğmesine basarak Service Fabric Mesh uygulamanızı Azure’a dağıtın.

Azure'da ilk kez uygulama yayımladığınızda, Docker görüntüsü Azure Container Registry (ACR) konumuna iletilir ve bu da görüntünün boyutuna göre zaman alabilir. Aynı projenin sonraki yayımlama işlemleri daha hızlı olacaktır. Visual Studio **Çıktı** açılır penceresinde **Service Fabric Araçları**’nı seçerek dağıtımın ilerleme durumunu izleyebilirsiniz. Dağıtım tamamlandıktan sonra **Service Fabric Araçları** çıktısı, uygulamanızın IP adresini ve bağlantı noktasını URL biçiminde gösterir.

```
Packaging Application...
Building Images...
Web1 -> C:\Code\ServiceFabricMesh1\Web1\bin\Any CPU\Release\netcoreapp2.0\Web1.dll
Uploading the images to Azure Container Registry...
Deploying application to remote endpoint...
The application was deployed successfully and it can be accessed at http://...
```

Bir web tarayıcısını açıp URL’ye giderek Azure’da çalışan web sitenizi görebilirsiniz:

![Service Fabric Mesh Web uygulamasını çalıştırma](media/service-fabric-mesh-tutorial-deploy-dotnetcore/deployed-web-project.png)

## <a name="clean-up-resources"></a>Kaynakları temizleme

Artık gerekli değilse, bu hızlı başlangıç için oluşturduğunuz tüm kaynakları silin. Hem ACR hem de Service Fabric Mesh hizmet kaynaklarını barındıracak yeni bir kaynak grubu oluşturduğunuzdan bu kaynak grubunu güvenle silebilirsiniz; bu, bununla ilişkili tüm kaynakları silmenin kolay bir yoludur.

```azurecli
az group delete --resource-group sfmeshTutorial1RG
```

```powershell
Connect-AzureRmAccount
Remove-AzureRmResourceGroup -Name sfmeshTutorial1RG
```

Alternatif olarak kaynak grubunu [Azure portaldan](https://portal.azure.com) silebilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar

Service Fabric Mesh uygulamalarını oluşturma ve dağıtma hakkında daha fazla bilgi edinmek için öğreticiye devam edin.
> [!div class="nextstepaction"]
> [Çok hizmet sunan bir web uygulaması oluşturup Service Fabric Mesh’e dağıtma ve bu uygulamada hata ayıklama](service-fabric-mesh-tutorial-create-dotnetcore.md)
