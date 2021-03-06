---
title: Kotlin ve Maven ile Azure'da ilk işlevinizi oluşturun
description: Kotlin ve Maven ile Azure'da HTTP tetiklenen bir işlev oluşturun ve yayımlayın.
author: dglover
ms.service: azure-functions
ms.topic: quickstart
ms.date: 03/25/2020
ms.author: dglover
ms.openlocfilehash: e4ac4f669d38f07d9fe4edbd600cc06f135fac03
ms.sourcegitcommit: b129186667a696134d3b93363f8f92d175d51475
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2020
ms.locfileid: "80674107"
---
# <a name="quickstart-create-your-first-function-with-kotlin-and-maven"></a>Quickstart: Kotlin ve Maven ile ilk işlevinizi oluşturun

Bu makale, Azure İşlevleri için bir Kotlin işlev projesi oluşturmak ve yayımlamak için Maven komut satırı aracını kullanarak size yol gösterir. Tüm adımları tamamladığınızda işleviniz Azure'da [Tüketim Planı](functions-scale.md#consumption-plan)'nda çalışmaya başlar ve HTTP isteği kullanılarak tetiklenebilir.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Ön koşullar

Kotlin kullanarak işlevler geliştirmek için aşağıdakileri yüklemiş olmalısınız:

- [Java Developer Kit](https://aka.ms/azure-jdks), sürüm 8
- [Apache Maven](https://maven.apache.org), sürüm 3.0 veya üzeri
- [Azure CLI](https://docs.microsoft.com/cli/azure)
- [Azure İşlevler Çekirdek Araçları](./functions-run-local.md#v2) sürüm 2.6.666 veya üzeri

> [!IMPORTANT]
> Bu hızlı başlangıcın tamamlanabilmesi için JAVA_HOME ortam değişkeni JDK’nin yükleme konumu olarak ayarlanmalıdır.

## <a name="generate-a-new-functions-project"></a>Yeni İşlevler projesi oluşturma

İşlevler projesini bir [Maven arketipinden](https://maven.apache.org/guides/introduction/introduction-to-archetypes.html) oluşturmak için boş bir klasörde aşağıdaki komutu çalıştırın.

# <a name="bash"></a>[bash](#tab/bash)
```bash
mvn archetype:generate \
    -DarchetypeGroupId=com.microsoft.azure \
    -DarchetypeArtifactId=azure-functions-kotlin-archetype
```

> [!NOTE]
> Komutu çalıştırmayla ilgili sorunlar yaşıyorsanız, hangi `maven-archetype-plugin` sürümün kullanıldığına bir göz atın. Komutu dosyası olmayan `.pom` boş bir dizinde çalıştırdığınıziçin, Maven'inizi eski bir sürümden yükselttiyseniz eski sürümün `~/.m2/repository/org/apache/maven/plugins/maven-archetype-plugin` eklentisini kullanmaya çalışıyor olabilir. Bu durumda, dizini `maven-archetype-plugin` silmeyi ve komutu yeniden çalıştırmayı deneyin.

# <a name="powershell"></a>[PowerShell](#tab/powershell)
```powershell
mvn archetype:generate `
    "-DarchetypeGroupId=com.microsoft.azure" `
    "-DarchetypeArtifactId=azure-functions-kotlin-archetype"
```

# <a name="cmd"></a>[Cmd](#tab/cmd)
```cmd
mvn archetype:generate ^
    -DarchetypeGroupId=com.microsoft.azure ^
    -DarchetypeArtifactId=azure-functions-kotlin-archetype
```
---

Maven, projeyi oluşturmayı bitirmek için gereken değerleri sizden sorar. _groupId_, _artifactId_ ve _version_ değerleri için [Maven adlandırma kuralları](https://maven.apache.org/guides/mini/guide-naming-conventions.html) başvurusuna bakın. _appName_ değerinin Azure genelinde benzersiz olması gerektiğinden, Maven bir varsayılan olarak daha önce girilen _artifactId_’yi temel alarak bir uygulama adı oluşturur. _packageName_ değeri, oluşturulan işlev kodu için Kotlin paketini belirler.

Aşağıdaki `com.fabrikam.functions` ve `fabrikam-functions` tanımlayıcıları, örnek olarak ve bu hızlı başlangıcın ilerleyen kısımlarındaki adımların okunmasını kolaylaştırmak için kullanılır. Bu adımda Maven'e kendi değerlerinizi sağlamanız için cesaretlendirildiniz.

<pre>
[INFO] Parameter: groupId, Value: com.fabrikam.function
[INFO] Parameter: artifactId, Value: fabrikam-function
[INFO] Parameter: version, Value: 1.0-SNAPSHOT
[INFO] Parameter: package, Value: com.fabrikam.function
[INFO] Parameter: packageInPathFormat, Value: com/fabrikam/function
[INFO] Parameter: appName, Value: fabrikam-function-20190524171507291
[INFO] Parameter: resourceGroup, Value: java-functions-group
[INFO] Parameter: package, Value: com.fabrikam.function
[INFO] Parameter: version, Value: 1.0-SNAPSHOT
[INFO] Parameter: groupId, Value: com.fabrikam.function
[INFO] Parameter: appRegion, Value: westus
[INFO] Parameter: artifactId, Value: fabrikam-function
</pre>

Maven, şu örnekte _artifactId_ adlı yeni bir klasörde proje dosyalarını oluşturur: `fabrikam-functions`. Projedeki çalıştırılmaya hazır olarak oluşturulan bu kod, isteğin gövdesini yankılayan [HTTP tetiklemeli](/azure/azure-functions/functions-bindings-http-webhook) basit bir işlevdir:

```kotlin
class Function {

    /**
     * This function listens at endpoint "/api/HttpTrigger-Java". Two ways to invoke it using "curl" command in bash:
     * 1. curl -d "HTTP Body" {your host}/api/HttpTrigger-Java&code={your function key}
     * 2. curl "{your host}/api/HttpTrigger-Java?name=HTTP%20Query&code={your function key}"
     * Function Key is not needed when running locally, it is used to invoke function deployed to Azure.
     * More details: https://aka.ms/functions_authorization_keys
     */
    @FunctionName("HttpTrigger-Java")
    fun run(
            @HttpTrigger(
                    name = "req",
                    methods = [HttpMethod.GET, HttpMethod.POST],
                    authLevel = AuthorizationLevel.FUNCTION) request: HttpRequestMessage<Optional<String>>,
            context: ExecutionContext): HttpResponseMessage {

        context.logger.info("HTTP trigger processed a ${request.httpMethod.name} request.")

        val query = request.queryParameters["name"]
        val name = request.body.orElse(query)

        name?.let {
            return request
                    .createResponseBuilder(HttpStatus.OK)
                    .body("Hello, $name!")
                    .build()
        }

        return request
                .createResponseBuilder(HttpStatus.BAD_REQUEST)
                .body("Please pass a name on the query string or in the request body")
                .build()
    }
}
```

## <a name="run-the-function-locally"></a>İşlevi yerel olarak çalıştırma

Yeni oluşturulan proje klasörünün dizinine geçerek Maven ile işlevi derleyin ve çalıştırın:

```
cd fabrikam-function
mvn clean package
mvn azure-functions:run
```

> [!NOTE]
> Java 9 ile `javax.xml.bind.JAXBException` özel durumunu yaşıyorsanız, [GitHub](https://github.com/jOOQ/jOOQ/issues/6477)’daki geçici çözüme bakın.

İşlev sisteminizde yerel olarak çalıştırılırken ve HTTP isteklerine yanıt vermeye hazır olduğunda şu çıktıyı görürsünüz:

<pre>
Now listening on: http://0.0.0.0:7071
Application started. Press Ctrl+C to shut down.

Http Functions:

        HttpTrigger-Java: [GET,POST] http://localhost:7071/api/HttpTrigger-Java
</pre>

Yeni bir terminal penceresinde curl kullanarak komut satırından işlevi tetikleyin:

```
curl -w '\n' -d LocalFunction http://localhost:7071/api/HttpTrigger-Java
```

<pre>
Hello LocalFunction!
</pre>

İşlev kodunu durdurmak için terminalde `Ctrl-C` komutunu kullanın.

## <a name="deploy-the-function-to-azure"></a>İşlevi Azure’a dağıtma

Azure İşlevleri’ne dağıtım işlemi, Azure CLI’dan hesap kimlik bilgilerini kullanır. Devam etmeden önce [Azure CLI ile oturum açın.](/cli/azure/authenticate-azure-cli?view=azure-cli-latest)

```azurecli
az login
```

`azure-functions:deploy` Maven hedefini kullanarak kodunuzu yeni bir İşlev uygulamasına dağıtın.

> [!NOTE]
> İşlev uygulamanızı dağıtmak için Visual Studio Code'u kullandığınızda, ücretsiz olmayan bir abonelik seçmeyi unutmayın veya bir hata alırsınız. Aboneliğinizi IDE'nin sol tarafında izleyebilirsiniz.

```
mvn azure-functions:deploy
```

Dağıtım tamamlandığında, Azure işlev uygulamanıza erişmek için kullanabileceğiniz URL’yi görürsünüz:

<pre>
[INFO] Successfully deployed Function App with package.
[INFO] Deleting deployment package from Azure Storage...
[INFO] Successfully deleted deployment package fabrikam-function-20170920120101928.20170920143621915.zip
[INFO] Successfully deployed Function App at https://fabrikam-function-20170920120101928.azurewebsites.net
[INFO] ------------------------------------------------------------------------
</pre>

Azure’da çalışan işlev uygulamasını `cURL` kullanarak test edin. Önceki adımdan kendi işlev uygulamanız için dağıtılan URL ile eşleşmek üzere aşağıdaki örnekten URL’yi değiştirmeniz gerekir.

> [!NOTE]
> **Erişim haklarını** `Anonymous`' ya ayarladığınızdan emin olun `Function`Varsayılan düzeyini seçtiğinizde, işlev bitiş noktanıza erişmek için isteklerde [işlev anahtarını](functions-bindings-http-webhook-trigger.md#authorization-keys) sunmanız gerekir.

```
curl -w '\n' https://fabrikam-function-20170920120101928.azurewebsites.net/api/HttpTrigger-Java -d AzureFunctions
```

```Output
Hello AzureFunctions!
```

## <a name="make-changes-and-redeploy"></a>Değişiklik yapma ve yeniden dağıtma

İşlev uygulamanız tarafından döndürülen metinde değişiklikler yapmak için oluşturulan projedeki `src/main.../Function.java` kaynak dosyasını düzenleyin. Bu satırı değiştirin:

```kotlin
return request
        .createResponseBuilder(HttpStatus.OK)
        .body("Hello, $name!")
        .build()
```

Aşağıdaki koda:

```kotlin
return request
        .createResponseBuilder(HttpStatus.OK)
        .body("Hi, $name!")
        .build()
```

Önceki gibi terminalden `azure-functions:deploy` komutunu çalıştırarak değişiklikleri kaydedin ve yeniden dağıtın. İşlev uygulaması güncelleştirilir ve bu isteğin:

```
curl -w '\n' -d AzureFunctionsTest https://fabrikam-functions-20170920120101928.azurewebsites.net/api/HttpTrigger-Java
```

Güncelleştirilmiş çıktıyı görürsünüz:

<pre>
Hi, AzureFunctionsTest
</pre>


## <a name="reference-bindings"></a>Referans bağlamaları

HTTP tetikleyicisi ve Zamanlayıcı [tetikleyicisi dışındaki Işlevler tetikleyicileri ve bağlamalarla](functions-triggers-bindings.md) çalışmak için bağlama uzantıları yüklemeniz gerekir. Bu makale tarafından gerekli olmasa da, diğer bağlama türleri ile çalışırken uzantıları etkinleştirmeyi nasıl yapacağınızı bilmeniz gerekir.

[!INCLUDE [functions-extension-bundles](../../includes/functions-extension-bundles.md)]

## <a name="next-steps"></a>Sonraki adımlar

Basit bir HTTP tetikleyicisine sahip bir Kotlin işlev uygulaması oluşturdunuz ve Azure İşlevleri'ne dağıttınız.

- Java ve Kotlin işlevleri geliştirme hakkında daha fazla bilgi için [Java Fonksiyonları geliştirici kılavuzunu](functions-reference-java.md) inceleyin.
- `azure-functions:add` Maven hedefini kullanarak projenize farklı tetikleyicilere sahip ek işlevler ekleyin.
- [Visual Studio Code](https://code.visualstudio.com/docs/java/java-azurefunctions), [IntelliJ](functions-create-maven-intellij.md) ve [Eclipse](functions-create-maven-eclipse.md) ile yerel olarak işlev yazın ve işlevlerde hata ayıklayın. 
- Visual Studio Code ile Azure’da dağıtılan işlevlerde hata ayıklayın. Yönergeler için [sunucusuz Java uygulamaları](https://code.visualstudio.com/docs/java/java-serverless#_remote-debug-functions-running-in-the-cloud) belgelerine bakın.
