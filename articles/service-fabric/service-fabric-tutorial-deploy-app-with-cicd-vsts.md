---
title: CI ve Azure Pipelines ile bir uygulama dağıtma
description: Bu eğitimde, Azure Pipelines kullanarak bir Hizmet Kumaşı uygulaması için sürekli tümleştirme ve dağıtım ayarlamayı öğrenirsiniz.
ms.topic: tutorial
ms.date: 07/22/2019
ms.custom: mvc
ms.openlocfilehash: 11485d22abcf0b8e1eb13d8123ff21c7fe0079f8
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/24/2020
ms.locfileid: "75614154"
---
# <a name="tutorial-deploy-an-application-with-cicd-to-a-service-fabric-cluster"></a>Öğretici: Service Fabric kümesine CI/CD ile uygulama dağıtma

Bu öğretici, bir serinin dördüncü bölümüdür ve Azure Pipelines kullanarak bir Azure Hizmet Kumaşı uygulaması için sürekli tümleştirme ve dağıtımın nasıl ayarlanır olduğunu açıklar.  Mevcut bir Service Fabric uygulaması gereklidir; örnek olarak [.NET uygulaması oluşturma](service-fabric-tutorial-create-dotnet-app.md) bölümünde oluşturulan uygulama kullanılır.

Serinin üçüncü bölümünde şunları öğrenirsiniz:

> [!div class="checklist"]
> * Projenize kaynak denetimi ekleme
> * Azure Pipelines’da derleme işlem hattı oluşturma
> * Azure Pipelines’da yayın işlem hattı oluşturma
> * Uygulamayı otomatik olarak dağıtma ve yükseltme

Bu öğretici dizisinde şunların nasıl yapıldığını öğrenirsiniz:
> [!div class="checklist"]
> * [.NET Service Fabric uygulaması oluşturma](service-fabric-tutorial-create-dotnet-app.md)
> * [Uygulamayı uzak kümeye dağıtma](service-fabric-tutorial-deploy-app-to-party-cluster.md)
> * [ASP.NET Core ön uç hizmetine BIR HTTPS bitiş noktası ekleme](service-fabric-tutorial-dotnet-app-enable-https-endpoint.md)
> * Azure Pipelines kullanarak CI/CD yapılandırma
> * [Uygulama için izleme ve tanılamayı ayarlama](service-fabric-tutorial-monitoring-aspnet.md)

## <a name="prerequisites"></a>Ön koşullar

Bu öğreticiye başlamadan önce:

* Azure aboneliğiniz yoksa [ücretsiz bir hesap](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) oluşturun
* [Visual Studio 2019'u yükleyin](https://www.visualstudio.com/) ve **Azure geliştirme** ve ASP.NET ve web geliştirme iş yüklerini **yükleyin.**
* [Servis Kumaşı SDK'yı yükleyin](service-fabric-get-started.md)
* Azure’da Windows Service Fabric kümesi oluşturun; örneğin, [bu öğreticiyi izleyin](service-fabric-tutorial-create-vnet-and-windows-cluster.md)
* [Azure DevOps kuruluşu](https://docs.microsoft.com/azure/devops/organizations/accounts/create-organization-msa-or-work-student) oluşturun. Bu, Azure DevOps'lerde bir proje oluşturmanıza ve Azure Ardışık Hatlar'ı kullanmanıza olanak tanır.

## <a name="download-the-voting-sample-application"></a>Voting örnek uygulamasını indirme

[Bu öğretici serinin birinci bölümünde](service-fabric-tutorial-create-dotnet-app.md)Oylama örnek uygulama inşa etmediyseniz, indirebilirsiniz. Komut penceresinde, örnek uygulama deposunu yerel makinenize kopyalamak için aşağıdaki komutu çalıştırın.

```git
git clone https://github.com/Azure-Samples/service-fabric-dotnet-quickstart
```

## <a name="prepare-a-publish-profile"></a>Yayımlama profili hazırlama

[Uygulamayı oluşturduğunuza](service-fabric-tutorial-create-dotnet-app.md) ve [uygulamayı Azure’a dağıttığınıza](service-fabric-tutorial-deploy-app-to-party-cluster.md) göre, artık sürekli tümleştirme ayarlamaya hazırsınız.  İlk olarak, Azure Pipelines içinde yürüten dağıtım işlemi tarafından kullanılmak üzere uygulamanız içinde bir yayımlama profili hazırlayın.  Yayımlama profili, daha önce oluşturduğunuz kümeyi hedefleyecek şekilde yapılandırılmalıdır.  Visual Studio’yu başlatın ve mevcut Service Fabric uygulaması projesini açın.  **Çözüm Gezgini**'nde uygulamaya sağ tıklayın ve **Yayımla...** öğesini seçin.

Sürekli tümleştirme iş akışınızda kullanmak üzere uygulama projenizin içinde bir hedef profil seçin (örneğin Bulut).  Küme bağlantısı uç noktasını belirtin.  Azure DevOps’daki her dağıtımda uygulamanızın yükseltilmesi için **Uygulamayı Yükselt** onay kutusunu işaretleyin.  Ayarları yayımlama profiline kaydetmek için **Kaydet** bağlantısına tıklayın ve ardından **İptal**’e tıklayarak iletişim kutusunu kapatın.

![Gönderim profili][publish-app-profile]

## <a name="share-your-visual-studio-solution-to-a-new-azure-devops-git-repo"></a>Visual Studio çözümünüzü yeni bir Azure DevOps Git deposunda paylaşma

Derlemeler oluşturabilmek için uygulamanızın kaynak dosyalarını Azure DevOps’daki bir projede paylaşın.

Visual Studio'nun sağ alt köşesindeki durum çubuğunda Kaynak Denetimi -> **ne** **ekle'yi**seçerek projeniz için yeni bir yerel Git reposu oluşturun.

**Takım Gezgini**’ndeki **Gönderim** görünümünde **Azure DevOps’a Gönder**’in altında yer alan **Git Deposunda Yayımla** düğmesini seçin.

![Git deposunu gönderme][push-git-repo]

E-postanızı doğrulayın ve **Azure DevOps Etki Alanı** açılır listesinde hesabınızı seçin. Deponuzun adını girin ve **Depoyu yayımla**’yı seçin.

![Git deposunu gönderme][publish-code]

Depoyu yayımlamak, hesabınızda yerel depoyla aynı ada sahip yeni bir proje oluşturur. Mevcut projede depoyu oluşturmak için, **Depo adının** yanındaki **Gelişmiş**’e tıklayın ve bir proje seçin. **Web üzerinde görüntüleyin**’i seçerek kodunuzu web’de görüntüleyebilirsiniz.

## <a name="configure-continuous-delivery-with-azure-pipelines"></a>Azure Pipelines ile Sürekli Teslimatı Yapılandırma

Azure Ardışık Hatları yapı, sırayla yürütülen bir yapı adım kümesinden oluşan bir iş akışını açıklar. Service Fabric kümenize dağıtmak üzere Service Fabric uygulama paketini ve diğer yapıtları üreten bir derleme işlem hattı oluşturun. [Azure Pipelines derleme işlem hatları](https://www.visualstudio.com/docs/build/define/create) hakkında daha fazla bilgi edinin. 

Bir Azure Pipelines yayın işlem hattı, kümeye uygulama paketi dağıtan bir iş akışını açıklar. Derleme işlem hattı ve yayın işlem hattı ile birlikte kullanıldığında kaynak dosyalardan başlayıp kümenizde çalışan bir uygulamada biten iş akışının tamamını yürütür. [Azure Ardışık Hatları sürümü için](https://www.visualstudio.com/docs/release/author-release-definition/more-release-definition)daha fazla bilgi edinin.

### <a name="create-a-build-pipeline"></a>Derleme işlem hattı oluşturma

Web tarayıcısını açın ve şu adresteki yeni projenize gidin: [https://&lt;myaccount&gt;.visualstudio.com/Voting/Voting%20Team/_git/Voting](https://myaccount.visualstudio.com/Voting/Voting%20Team/_git/Voting).

**Ardışık Hatlar** sekmesini seçin, sonra **Oluşturur,** ardından **Yeni Ardışık Hatlar'ı**tıklatın.

![Yeni İşlem Hattı][new-pipeline]

Kaynak olarak **Azure Repos** Git'i, **Oylama** Ekibi projesini, **Oylama** Deposu'nu ve el ile ve zamanlanmış yapılar için Varsayılan **dala'yı** seçin.  Ardından **Devam et'i**tıklatın.

![Repo'u seçin][select-repo]

**Şablon seç** alanında **Azure Service Fabric uygulaması** şablonunu seçin ve **Uygula**'ya tıklayın.

![Derleme şablonu seçme][select-build-template]

**Görevlerde**, "Barındırılan VS2017"yi **Agent havuzu**olarak girin.

![Görevleri seçme][save-and-queue]

**Tetikleyiciler**’in altında **Sürekli tümleştirmeyi etkinleştir**'i işaretleyerek sürekli tümleştirmeyi etkinleştirin. **Branch filtreleri**içinde, **Şube belirtimi** **varsayılan olarak master.** Derlemeyi el ile başlatmak için **Kaydet ve kuyruğa al**’ı seçin.

![Tetikleyicileri seçme][save-and-queue2]

Derlemeler gönderme veya iade işlemleriyle de tetiklenir. Yapı ilerlemenizi denetlemek için **Yapılar** sekmesine geçin.  Yapının başarılı bir şekilde yürütüldettiğini doğruladıktan sonra, uygulamanızı bir kümeye dağıtan bir sürüm ardışık hattı tanımlayın.

### <a name="create-a-release-pipeline"></a>Yayın işlem hattı oluşturma

**Ardışık Hatlar** sekmesini seçin, ardından **Serbest Bırakır**, sonra **+ Yeni ardışık.**  **Şablon seç** alanında, listeden **Azure Service Fabric Dağıtımı** şablonunu ve sonra da **Uygula**'yı seçin.

![Yayın şablonunu seçme][select-release-template]

Yeni küme bağlantısı eklemek için **Görevler**->**Ortamı 1** ve ardından **+Yeni'yi** seçin.

![Küme bağlantısı ekleme][add-cluster-connection]

**Yeni Service Fabric Bağlantısı ekle** görünümünde **Sertifika Tabanlı** veya **Azure Active Directory** kimlik doğrulamasını seçin.  Bağlantı adı olarak "mysftestcluster" ve küme uç noktası olarak "tcp://mysftestcluster.southcentralus.cloudapp.azure.com:19000" (veya dağıtım yaptığınız kümenin uç noktası) belirtin.

Sertifika tabanlı kimlik doğrulama için, kümeoluşturmak için kullanılan sunucu sertifikasının **Sunucu sertifikası parmak izini** ekleyin.  **İstemci sertifikası** alanında, istemci sertifika dosyasının base-64 kodlamasını ekleyin. Sertifikanın bu base-64 kodlamalı gösterimini nasıl alacağınızı öğrenmek için bu alanın yardım açılan kutusuna bakın. Ayrıca sertifika için **Parola** ekleyin.  Ayrı bir istemci sertifikanız yoksa, küme veya sunucu sertifikasını kullanabilirsiniz.

Azure Active Directory kimlik bilgileri için, kümeyi oluştururken kullanılan sunucu sertifikasının **Sunucu sertifikası parmak izi**'ni ve ayrıca **Kullanıcı adı** ve **Parola** alanlarına kümeye bağlanırken kullanmak istediğiniz kimlik bilgilerini girin.

**Ekle**'ye tıklayarak küme bağlantısını kaydedin.

Ardından, yayın işlem hattının derlemeden çıkışı bulabilmesi için işlem hattına bir derleme yapıtı ekleyin. **İşlem Hattı**'nı ve **Yapıtlar**->**+Ekle**'yi seçin.  **Kaynak (Derleme tanımı)** alanında, daha önce oluşturmuş olduğunuz derleme işlem hattını seçin.  **Ekle**’ye tıklayarak derleme yapıtını kaydedin.

![Yapıt ekleme][add-artifact]

Derleme tamamlandığında otomatik olarak bir yayın oluşturulması için sürekli dağıtım tetikleyicisini etkinleştirin. Yapıttaki şimşek simgesine tıklayın, tetikleyiciyi etkinleştirin ve **Kaydet**'e tıklayarak yayın işlem hattını kaydedin.

![Tetikleyici etkinleştirme][enable-trigger]

Bir sürümü el ile oluşturmak için**Sürüm** -> **Oluştur** ' u seçin **+ Yayın** -> Oluştur' u seçin. Yayının ilerleme durumunu **Yayınlar** sekmesinden takip edebilirsiniz.

Dağıtımın başarılı olduğunu ve uygulamanın kümede çalıştığını doğrulayın.  Bir web tarayıcısı açın ve `http://mysftestcluster.southcentralus.cloudapp.azure.com:19080/Explorer/` sayfasına gidin.  Uygulama sürümünü not alın (bu örnekte "1.0.0.20170616.3").

## <a name="commit-and-push-changes-trigger-a-release"></a>Değişiklikleri işleme ve gönderme, yayını tetikleme

Azure DevOps'da yapılan bazı kod değişikliklerini denetleyerek sürekli tümleştirme işlem hattının çalıştığını doğrulayın.

Siz kodunuzu yazarken, değişiklikleriniz Visual Studio tarafından otomatik olarak izlenir. Sağ alt kısımdaki durum çubuğunda bekleyen değişiklikler simgesini (![Beklemede][pending]) seçerek değişiklikleri yerel Git deponuza işleyin.

Takım Gezgini'ndeki **Değişiklikler** görünümünde, güncelleştirmenizi açıklayan bir ileti ekleyin ve değişikliklerinizi işleyin.

![Tümünü işleme][changes]

Yayımlanmamış değişiklikler durum çubuğu simgesini (![Yayımlanmamış değişiklikler][unpublished-changes]) veya Takım Gezgini'nde Eşitleme görünümünü seçin. Azure Pipelines'da kodunuzu güncelleştirmek için **Push'u** seçin.

![Değişiklikleri gönderme][push]

Değişiklikleri Azure Ardışık Larına zorlamak otomatik olarak bir yapıyı tetikler.  Derleme işlem hattı başarıyla tamamlandığında, otomatik olarak bir yayın oluşturulur ve kümede uygulamayı yükseltme işlemini başlatır.

Derlemenizin ilerleme durumunu denetlemek için, Visual Studio'nun **Takım Gezgini**'nde **Derlemeler** sekmesine geçin.  Derlemenin başarıyla yürütüldüğünü doğruladıktan sonra, uygulamanızı kümeye dağıtan bir yayın işlem hattı belirleyin.

Dağıtımın başarılı olduğunu ve uygulamanın kümede çalıştığını doğrulayın.  Bir web tarayıcısı açın ve `http://mysftestcluster.southcentralus.cloudapp.azure.com:19080/Explorer/` sayfasına gidin.  Uygulama sürümünü not alın (bu örnekte "1.0.0.20170815.3").

![Service Fabric Explorer][sfx1]

## <a name="update-the-application"></a>Uygulamayı güncelleştirme

Uygulamada kod değişikliklerini yapın.  Önceki adımları izleyerek değişiklikleri kaydedin ve işleyin.

Uygulamanın yükseltmesi başladığında, Service Fabric Explorer'da yükseltmenin ilerleme durumunu izleyebilirsiniz:

![Service Fabric Explorer][sfx2]

Uygulama yükseltmesi birkaç dakika sürebilir. Yükseltme tamamlandığında, uygulama bir sonraki sürümde çalışıyor olacaktır.  Bu örnekte "1.0.0.20170815.4".

![Service Fabric Explorer][sfx3]

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide, şunların nasıl yapıldığını öğrendiniz:

> [!div class="checklist"]
> * Projenize kaynak denetimi ekleme
> * Derleme işlem hattı oluşturma
> * Yayın işlem hattı oluşturma
> * Uygulamayı otomatik olarak dağıtma ve yükseltme

Sonraki öğreticiye ilerleyin:
> [!div class="nextstepaction"]
> [Uygulama için izleme ve tanılamayı ayarlama](service-fabric-tutorial-monitoring-aspnet.md)

<!-- Image References -->
[publish-app-profile]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/PublishAppProfile.png
[push-git-repo]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/PublishGitRepo.png
[publish-code]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/PublishCode.png
[new-pipeline]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/NewPipeline.png
[select-repo]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/SelectRepo.png
[select-build-template]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/SelectBuildTemplate.png
[save-and-queue]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/SaveAndQueue.png
[save-and-queue2]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/SaveAndQueue2.png
[select-release-template]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/SelectReleaseTemplate.png
[set-continuous-integration]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/SetContinuousIntegration.png
[add-cluster-connection]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/AddClusterConnection.png
[add-artifact]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/AddArtifact.png
[enable-trigger]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/EnableTrigger.png
[sfx1]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/SFX1.png
[sfx2]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/SFX2.png
[sfx3]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/SFX3.png
[pending]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/Pending.png
[changes]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/Changes.png
[unpublished-changes]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/UnpublishedChanges.png
[push]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/Push.png
[continuous-delivery-with-AzureDevOpsServices]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/VSTS-Dialog.png
[new-service-endpoint]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/NewServiceEndpoint.png
[new-service-endpoint-dialog]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/NewServiceEndpointDialog.png
