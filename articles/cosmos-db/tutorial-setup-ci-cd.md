---
title: Azure Cosmos DB emülatörü oluşturma göreviyle CI/CD ardışık hattını ayarlama
description: Azure DevOps'ta Cosmos DB öykünücüsü derleme görevini kullanarak derleme ve yayın iş yükü ayarlama öğreticisi
author: deborahc
ms.service: cosmos-db
ms.topic: tutorial
ms.date: 01/28/2020
ms.author: dech
ms.reviewer: sngun
ms.openlocfilehash: 521d5d8d587b39cf573dedc37ea9f6fd53646e66
ms.sourcegitcommit: 27bbda320225c2c2a43ac370b604432679a6a7c0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/31/2020
ms.locfileid: "80410960"
---
# <a name="set-up-a-cicd-pipeline-with-the-azure-cosmos-db-emulator-build-task-in-azure-devops"></a>Azure DevOps'ta Azure Cosmos DB öykünücüsü derleme göreviyle CI/CD işlem hattı oluşturma

Azure Cosmos DB öykünücüsü, geliştirme amaçlı olarak Azure Cosmos DB hizmetine öykünen yerel bir ortam sağlar. Öykünücü sayesinde Azure aboneliği oluşturmadan veya masraf yapmadan uygulamanızı yerel ortamda geliştirip test edebilirsiniz. 

Azure DevOps için Azure Cosmos DB öykünücüsü derleme görevi, bu işlemi bir CI ortamında da gerçekleştirmenizi sağlar. Derleme göreviyle derleme ve yayın iş yüklerinizin bir parçası olarak öykünücüyle test çalıştırabilirsiniz. Bu görev öykünücünün çalıştığı bir Docker kapsayıcı başlatır ve derleme tanımının kalanı tarafından kullanılabilecek bir uç nokta sunar. İstediğiniz sayıda öykünücü örneği oluşturup başlatabilirsiniz ve oluşturduğunuz her örnek ayrı bir kapsayıcıda çalışır. 

Bu makalede Azure DevOps'ta test çalıştırmak için Cosmos DB öykünücüsü derleme görevini kullanan bir ASP.NET uygulaması için CI işlem hattı ayarlama adımları gösterilmektedir. Bir Düğüm.js veya Python uygulaması için BIR CI ardışık ayar yapmak için benzer bir yaklaşım kullanabilirsiniz. 

## <a name="install-the-emulator-build-task"></a>Öykünücü derleme görevini yükleme

Derleme görevini kullanmak için öncelikle Azure DevOps kuruluşunuza yüklemeniz gerekir. [Market](https://marketplace.visualstudio.com/items?itemName=azure-cosmosdb.emulator-public-preview) sayfasında **Azure Cosmos DB Emulator** uzantısını bulun ve **Ücretsiz edinin**'e tıklayın.

![Azure DevOps Market'te Azure Cosmos DB Emulator derleme görevini bulun ve yükleyin](./media/tutorial-setup-ci-cd/addExtension_1.png)

Ardından uzantının yükleneceği kuruluşu seçin. 

> [!NOTE]
> Bir Azure DevOps kuruluşuna uzantı yüklemek için bir hesap sahibi veya proje koleksiyonu yöneticisi olmalısınız. Gerekli izinlere sahip değilseniz ancak hesap üyesiyseniz uzantı isteyebilirsiniz. [Daha fazla bilgi edinin.](https://docs.microsoft.com/azure/devops/marketplace/faq-extensions?view=vsts)

![Uzantı yüklemek için bir Azure DevOps kuruluşu seçin](./media/tutorial-setup-ci-cd/addExtension_2.png)

## <a name="create-a-build-definition"></a>Derleme tanımı oluşturma

Artık uzantı yüklendiğine göre Azure DevOps hesabınızda oturum açın ve projeler panosundan projenizi bulun. Projenize bir [derleme işlem hattı](https://docs.microsoft.com/azure/devops/pipelines/get-started-designer?view=vsts&tabs=new-nav) ekleyebilir veya var olan derleme işlem hattını değiştirebilirsiniz. Bir derleme işlem hattınız varsa [Derleme tanımına Öykünücü derlemesi ekleme](#addEmulatorBuildTaskToBuildDefinition) bölümüne geçebilirsiniz.

1. Yeni bir derleme tanımı oluşturmak için Azure DevOps uygulamasının **Derlemeler** sekmesine gidin. **+Yeni**'yi seçin. \> **Yeni derleme işlem hattı**

   ![Yeni derleme işlem hattı oluşturma](./media/tutorial-setup-ci-cd/CreateNewBuildDef_1.png)

2. İstenen **kaynak**, **Takım projesi**, **Depo** ve **El ile ve zamanlanan derlemeler için varsayılan dal** değerlerini seçin. Gerekli seçenekleri belirttikten sonra **Devam**’ı seçin

   ![Derleme işlem hattı için takım projesini, depoyu ve dalı seçme](./media/tutorial-setup-ci-cd/CreateNewBuildDef_2.png)

3. Son olarak derleme işlem hattı için kullanmak istediğiniz şablonu belirleyin. Bu öğreticide **ASP.NET** şablonunu seçeceğiz. Artık Azure Cosmos DB emülatörü oluşturma görevini kullanmak üzere ayarlayabileceğiniz bir yapı ardışık hattınız var. 

> [!NOTE]
> Bu CI için seçilecek aracı havuzu, yükleme CI'nin bir parçası olarak önceki bir görevde el ile yapılmadığı sürece Windows için Docker yüklü olmalıdır. Temsilci havuzları seçimi için [Microsoft barındırılan aracılar](https://docs.microsoft.com/azure/devops/pipelines/agents/hosted?view=azure-devops&tabs=yaml) makalesine bakın; ile `Hosted VS2017`başlamanızı öneririz.

Azure Cosmos DB emülatörü şu anda barındırılan VS2019 aracı havuzuna destek vermiyor. Ancak, emülatör zaten VS2019 yüklü ile birlikte gelir ve aşağıdaki PowerShell cmdlets ile emülatör başlatarak kullanabilirsiniz. VS2019'u kullanırken herhangi bir sorunla karşılaştıysanız, yardım için [Azure DevOps](https://developercommunity.visualstudio.com/spaces/21/index.html) ekibine ulaşın:

```powershell
Import-Module "$env:ProgramFiles\Azure Cosmos DB Emulator\PSModules\Microsoft.Azure.CosmosDB.Emulator"
Start-CosmosDbEmulator
```

## <a name="add-the-task-to-a-build-pipeline"></a><a name="addEmulatorBuildTaskToBuildDefinition"></a>Görevi derleme işlem hattına ekleme

1. Derleme işlem hattına görev eklemeden önce aracı işi eklemelisiniz. Derleme işlem hattınıza gidin **...** öğesini ve **Aracı işi ekle**’yi seçin.

1. Sonra emülatör oluşturma görevi eklemek için aracı iş yanındaki **+** sembolü seçin. Arama kutusundan **cosmos** araması yapın, **Azure Cosmos DB Öykünücüsünü** seçin ve aracı işine ekleyin. Derleme görevi, üzerinde Cosmos DB öykünücüsünün bir örneğinin çalıştığı bir kapsayıcı başlatır. Azure Cosmos DB Öykünücüsü görevi, öykünücünün çalışır durumda olmasını gerektiren diğer görevlerden önce yerleştirilmelidir.

   ![Öykünücü derleme görevini derleme tanımına ekleme](./media/tutorial-setup-ci-cd/addExtension_3.png)

Bu öğreticide, testlerimiz çalıştırmadan önce öykünücünün kullanılabildiğinden emin olmak için görevi en başa ekleyeceksiniz.

## <a name="configure-tests-to-use-the-emulator"></a>Testleri öykünücüyü kullanacak şekilde yapılandırma

Şimdi testlerimizi öykünücüyü kullanacak şekilde yapılandıracağız. Öykünücü derleme görevi, derleme işlem hattındaki diğer görevlerin istek düzenleyebileceği "CosmosDbEmulator.Endpoint" ortam değişkenini dışarı aktarır. 

Bu öğreticide [Visual Studio Test görevini](https://github.com/Microsoft/azure-pipelines-tasks/blob/master/Tasks/VsTestV2/README.md) kullanarak **.runsettings** dosyasıyla yapılandırılmış birim testlerini çalıştıracağız. Birim testi kurulumu hakkında daha fazla bilgi edinmek için [belgeleri](https://docs.microsoft.com/visualstudio/test/configure-unit-tests-by-using-a-dot-runsettings-file?view=vs-2017) inceleyin. Bu belgede kullandığınız tam Todo uygulama kodu örneği [GitHub'da](https://github.com/Azure-Samples/documentdb-dotnet-todo-app) kullanılabilir

Uygulamanın birim testlerine iletilecek parametreleri tanımlayan örnek **.runsettings** dosyasını aşağıda görebilirsiniz. Kullanılan `authKey` değişkeninin öykünücü için [iyi bilinen anahtar](https://docs.microsoft.com/azure/cosmos-db/local-emulator#authenticating-requests) olduğuna dikkat edin. Bu `authKey`, öykünücü derleme görevi tarafından beklenen anahtardır ve **.runsettings** dosyanızda tanımlanmalıdır.

```csharp
<RunSettings>
    <TestRunParameters>
    <Parameter name="endpoint" value="https://localhost:8081" />
    <Parameter name="authKey" value="C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==" />
    <Parameter name="database" value="ToDoListTest" />
    <Parameter name="collection" value="ItemsTest" />
  </TestRunParameters>
</RunSettings>
```

Azure Cosmos DB'nin MongoDB apisini kullanan bir uygulama için bir CI/CD ardışık noktası kuruyorsanız, bağlantı dizesi varsayılan olarak 10255 bağlantı noktasını içerir. Ancak, bu bağlantı noktası şu anda açık değil, alternatif olarak, bağlantı kurmak için bağlantı 10250 kullanmanız gerekir. Desteklenen bağlantı noktası numarası 10255 yerine 10250 olması dışında Azure Cosmos DB'nin MongoDB bağlantı dizesi için API'si aynı kalır.

`TestRunParameters` parametrelerine uygulamanın test projesindeki bir `TestContext` özelliği aracılığıyla başvurulur. Burada Cosmos DB ile çalışan örnek bir testi görebilirsiniz.

```csharp
namespace todo.Tests
{
    [TestClass]
    public class TodoUnitTests
    {
        public TestContext TestContext { get; set; }

        [TestInitialize()]
        public void Initialize()
        {
            string endpoint = TestContext.Properties["endpoint"].ToString();
            string authKey = TestContext.Properties["authKey"].ToString();
            Console.WriteLine("Using endpoint: ", endpoint);
            DocumentDBRepository<Item>.Initialize(endpoint, authKey);
        }
        [TestMethod]
        public async Task TestCreateItemsAsync()
        {
            var item = new Item
            {
                Id = "1",
                Name = "testName",
                Description = "testDescription",
                Completed = false,
                Category = "testCategory"
            };

            // Create the item
            await DocumentDBRepository<Item>.CreateItemAsync(item);
            // Query for the item
            var returnedItem = await DocumentDBRepository<Item>.GetItemAsync(item.Id, item.Category);
            // Verify the item returned is correct.
            Assert.AreEqual(item.Id, returnedItem.Id);
            Assert.AreEqual(item.Category, returnedItem.Category);
        }

        [TestCleanup()]
        public void Cleanup()
        {
            DocumentDBRepository<Item>.Teardown();
        }
    }
}
```

Visual Studio Test görevindeki Execution Options (Yürütme Seçenekleri) bölümüne gidin. **Settings file** (Ayarlar dosyası) seçeneğinde testlerin **.runsettings** dosyasıyla yapılandırıldığını belirtin. **Override test run parameters** (Test çalıştırması parametrelerini geçersiz kıl) seçeneğine `-endpoint $(CosmosDbEmulator.Endpoint)` girişini ekleyin. Bunu yaptığınızda Test görevi **.runsettings** dosyasında tanımlanan uç noktanın yerine öykünücü derleme görevinin uç noktasına başvurur.  

![Uç noktası değişkenini öykünücü derleme görevi uç noktasıyla geçersiz kılma](./media/tutorial-setup-ci-cd/addExtension_5.png)

## <a name="run-the-build"></a>Derlemeyi çalıştırma

Şimdi, kaydet ve yapı **yı sıraya.** 

![Derlemeyi kaydetme ve çalıştırma](./media/tutorial-setup-ci-cd/runBuild_1.png)

Derleme başlatıldıktan sonra Cosmos DB öykünücüsü görevinin öykünücünün yüklü olduğu Docker görüntüsünü çekmeye başladığından emin olun. 

![Derlemeyi kaydetme ve çalıştırma](./media/tutorial-setup-ci-cd/runBuild_4.png)

Derleme tamamlandıktan sonra testlerinizin iletildiğinden ve tümünün derleme görevindeki Cosmos DB öykünücüsüyle çalıştığından emin olun!

![Derlemeyi kaydetme ve çalıştırma](./media/tutorial-setup-ci-cd/buildComplete_1.png)

## <a name="set-up-using-yaml"></a>YAML kullanarak ayarlama

YAML görevi kullanarak CI/CD ardışık hattını ayarlıyorsanız, YAML görevini aşağıdaki kodda gösterildiği gibi tanımlayabilirsiniz:

```yml
- task: azure-cosmosdb.emulator-public-preview.run-cosmosdbemulatorcontainer.CosmosDbEmulator@2
  displayName: 'Run Azure Cosmos DB Emulator'

- script: yarn test
  displayName: 'Run API tests (Cosmos DB)'
  env:
    HOST: $(CosmosDbEmulator.Endpoint)
    # Hardcoded key for emulator, not a secret
    AUTH_KEY: C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==
    # The emulator uses a self-signed cert, disable TLS auth errors
    NODE_TLS_REJECT_UNAUTHORIZED: '0'
```

## <a name="next-steps"></a>Sonraki adımlar

Yerel geliştirme ve test için öykünücü kullanımı hakkında daha fazla bilgi edinmek için bkz. [Yerel geliştirme ve test için Azure Cosmos DB Öykünücüsünü kullanma](https://docs.microsoft.com/azure/cosmos-db/local-emulator).

Emülatör TLS/SSL sertifikaları dışa aktarmak [için](https://docs.microsoft.com/azure/cosmos-db/local-emulator-export-ssl-certificates) bkz.
