---
title: 'Quickstart: Azure Blob depolama kitaplığı v12 - Java'
description: Bu hızlı başlangıçta, Blob (nesne) depolama alanında bir kapsayıcı ve bir blob oluşturmak için Java için Azure Blob depolama istemcisi kitaplık 12 sürümünü nasıl kullanacağınızı öğrenirsiniz. Ardından, blob’u yerel bilgisayarınıza indirmeyi ve bir kapsayıcıdaki tüm blobların listesini görüntülemeyi öğreneceksiniz.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 01/27/2020
ms.service: storage
ms.subservice: blobs
ms.topic: quickstart
ms.openlocfilehash: c883653754e1f69d3b2d79b256d57a036c70b58e
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/26/2020
ms.locfileid: "78330157"
---
# <a name="quickstart-manage-blobs-with-java-v12-sdk"></a>Quickstart: Java v12 SDK ile blobs yönetin

Bu hızlı başlangıçta, Java kullanarak lekeleri yönetmeyi öğrenirsiniz. Blobs, resimler, belgeler, akışlı ortam ve arşiv verileri de dahil olmak üzere büyük miktarda metin veya ikili veri tutabilen nesnelerdir. Blob'ları yükler, indirir ve listelersiniz ve kapsayıcılar oluşturup silebilirsiniz.

[API başvuru belgeleri](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-blob/12.0.0/index.html) | [Kütüphane kaynak kodu](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/storage/azure-storage-blob) | [Paketi (Maven)](https://mvnrepository.com/artifact/com.azure/azure-storage-blob?repo=jcenter) | [Örnekleri](https://docs.microsoft.com/azure/storage/common/storage-samples-java?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#blob-samples)

## <a name="prerequisites"></a>Ön koşullar

- Etkin bir aboneliği olan bir Azure hesabı. [Ücretsiz bir hesap oluşturun.](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)
- Azure Depolama hesabı. [Bir depolama hesabı oluşturun.](../common/storage-account-create.md)
- [Java Geliştirme Kiti (JDK)](/java/azure/jdk/?view=azure-java-stable) sürüm 8 veya üzeri.
- [Apache Maven](https://maven.apache.org/download.cgi).

> [!NOTE]
> Önceki SDK sürümüyle başlamak için [Quickstart: Java v8 SDK ile blobları yönetin.](storage-quickstart-blobs-java-legacy.md)

[!INCLUDE [storage-multi-protocol-access-preview](../../../includes/storage-multi-protocol-access-preview.md)]

## <a name="setting-up"></a>Ayarlama

Bu bölüm, Java için Azure Blob depolama istemcisi kitaplığı v12 ile çalışmak üzere bir proje hazırlamakonusunda size yol sunar.

### <a name="create-the-project"></a>Proje oluşturma

*blob-quickstart-v12*adlı bir Java uygulaması oluşturun.

1. Konsol penceresinde (cmd, PowerShell veya Bash gibi), *blob-quickstart-v12*adını taşıyan yeni bir konsol uygulaması oluşturmak için Maven'i kullanın. "Merhaba dünya!" oluşturmak için aşağıdaki **mvn** komutunu yazın. Java projesi.

   ```console
   mvn archetype:generate -DgroupId=com.blobs.quickstart \
                          -DartifactId=blob-quickstart-v12 \
                          -DarchetypeArtifactId=maven-archetype-quickstart \
                          -DarchetypeVersion=1.4 \
                          -DinteractiveMode=false
   ```

1. Proje oluşturma çıktısı şuna benzer:

    ```console
    [INFO] Scanning for projects...
    [INFO]
    [INFO] ------------------< org.apache.maven:standalone-pom >-------------------
    [INFO] Building Maven Stub Project (No POM) 1
    [INFO] --------------------------------[ pom ]---------------------------------
    [INFO]
    [INFO] >>> maven-archetype-plugin:3.1.2:generate (default-cli) > generate-sources @ standalone-pom >>>
    [INFO]
    [INFO] <<< maven-archetype-plugin:3.1.2:generate (default-cli) < generate-sources @ standalone-pom <<<
    [INFO]
    [INFO]
    [INFO] --- maven-archetype-plugin:3.1.2:generate (default-cli) @ standalone-pom ---
    [INFO] Generating project in Batch mode
    [INFO] ----------------------------------------------------------------------------
    [INFO] Using following parameters for creating project from Archetype: maven-archetype-quickstart:1.4
    [INFO] ----------------------------------------------------------------------------
    [INFO] Parameter: groupId, Value: com.blobs.quickstart
    [INFO] Parameter: artifactId, Value: blob-quickstart-v12
    [INFO] Parameter: version, Value: 1.0-SNAPSHOT
    [INFO] Parameter: package, Value: com.blobs.quickstart
    [INFO] Parameter: packageInPathFormat, Value: com/blobs/quickstart
    [INFO] Parameter: version, Value: 1.0-SNAPSHOT
    [INFO] Parameter: package, Value: com.blobs.quickstart
    [INFO] Parameter: groupId, Value: com.blobs.quickstart
    [INFO] Parameter: artifactId, Value: blob-quickstart-v12
    [INFO] Project created from Archetype in dir: C:\QuickStarts\blob-quickstart-v12
    [INFO] ------------------------------------------------------------------------
    [INFO] BUILD SUCCESS
    [INFO] ------------------------------------------------------------------------
    [INFO] Total time:  7.056 s
    [INFO] Finished at: 2019-10-23T11:09:21-07:00
    [INFO] ------------------------------------------------------------------------
        ```

1. Switch to the newly created *blob-quickstart-v12* folder.

   ```console
   cd blob-quickstart-v12
   ```

1. Yan *blob-quickstart-v12* dizin, *veri*adlı başka bir dizin oluşturun. Burada blob veri dosyaları oluşturulacak ve depolanır.

    ```console
    mkdir data
    ```

### <a name="install-the-package"></a>Paketi yükleyin

Metin düzenleyicinizdeki *pom.xml* dosyasını açın. Bağımlılıklar grubuna aşağıdaki bağımlılık öğesini ekleyin.

```xml
<dependency>
    <groupId>com.azure</groupId>
    <artifactId>azure-storage-blob</artifactId>
    <version>12.0.0</version>
</dependency>
```

### <a name="set-up-the-app-framework"></a>Uygulama çerçevesini ayarlama

Proje dizininden:

1. */src/main/java/com/blobs/quickstart* dizinine gidin
1. Editörünüzde *App.java* dosyasını açın
1. İfadeyi `System.out.println("Hello world!");` silme
1. `import` Yönergeekleme

İşte kod:

```java
package com.blobs.quickstart;

/**
 * Azure blob storage v12 SDK quickstart
 */
import com.azure.storage.blob.*;
import com.azure.storage.blob.models.*;
import java.io.*;

public class App
{
    public static void main( String[] args ) throws IOException
    {
    }
}
```

[!INCLUDE [storage-quickstart-credentials-include](../../../includes/storage-quickstart-credentials-include.md)]

## <a name="object-model"></a>Nesne modeli

Azure Blob depolama, büyük miktarda yapılandırılmamış veri depolamak için optimize edilebiyi sunar. Yapılandırılmamış veriler, metin veya ikili veriler gibi belirli bir veri modeline veya tanıma bağlı olmayan verilerdir. Blob depolama üç tür kaynak sunar:

* Depolama hesabı
* Depolama hesabındaki bir kapsayıcı
* Konteynerde bir leke

Aşağıdaki diyagramda bu kaynaklar arasındaki ilişki gösterilmektedir.

![Blob depolama mimarisi diyagramı](./media/storage-blobs-introduction/blob1.png)

Bu kaynaklarla etkileşimde kalmak için aşağıdaki Java sınıflarını kullanın:

* [BlobServiceClient](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-blob/12.0.0/com/azure/storage/blob/BlobServiceClient.html): `BlobServiceClient` Sınıf, Azure Depolama kaynaklarını ve blob kapsayıcılarını işlemenizi sağlar. Depolama hesabı Blob hizmeti için üst düzey ad alanı sağlar.
* [BlobServiceClientBuilder](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-blob/12.0.0/com/azure/storage/blob/BlobServiceClientBuilder.html): `BlobServiceClientBuilder` Sınıf, `BlobServiceClient` nesnelerin yapılandırmasına ve anlık haline yardımcı olmak için akıcı bir oluşturucu API sağlar.
* [BlobContainerClient](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-blob/12.0.0/com/azure/storage/blob/BlobContainerClient.html): `BlobContainerClient` Sınıf, Azure Depolama kapsayıcılarını ve bunların lekelerini işlemenizi sağlar.
* [BlobClient](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-blob/12.0.0/com/azure/storage/blob/BlobClient.html): `BlobClient` Sınıf, Azure Depolama lekelerini işlemenizi sağlar.
* [BlobItem](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-blob/12.0.0/com/azure/storage/blob/models/BlobItem.html): `BlobItem` Sınıf, bir çağrıdan döndürülen `listBlobsFlat`tek tek lekeleri temsil eder.

## <a name="code-examples"></a>Kod örnekleri

Bu örnek kod parçacıkları, Java için Azure Blob depolama istemcisi kitaplığıyla aşağıdakileri nasıl gerçekleştireceklerini gösterir:

* [Bağlantı dizesini alma](#get-the-connection-string)
* [Bir kapsayıcı oluşturma](#create-a-container)
* [Blob'ları bir kapsayıcıya yükleme](#upload-blobs-to-a-container)
* [Blob’ları bir kapsayıcıda listeleme](#list-the-blobs-in-a-container)
* [Blob’ları indirme](#download-blobs)
* [Kapsayıcı silme](#delete-a-container)

### <a name="get-the-connection-string"></a>Bağlantı dizesini alma

Aşağıdaki kod, [depolama bağlantı dizesi bölümüyapılanınoluşturulan](#configure-your-storage-connection-string) ortam değişkeninden depolama hesabının bağlantı dizesini alır.

Bu kodu yöntemin `Main` içine ekleyin:

```java
System.out.println("Azure Blob storage v12 - Java quickstart sample\n");

// Retrieve the connection string for use with the application. The storage
// connection string is stored in an environment variable on the machine
// running the application called AZURE_STORAGE_CONNECTION_STRING. If the environment variable
// is created after the application is launched in a console or with
// Visual Studio, the shell or application needs to be closed and reloaded
// to take the environment variable into account.
String connectStr = System.getenv("AZURE_STORAGE_CONNECTION_STRING");
```

### <a name="create-a-container"></a>Bir kapsayıcı oluşturma

Yeni kapsayıcı için bir ad belirleyin. Aşağıdaki kod, benzersiz olduğundan emin olmak için kapsayıcı adına bir UUID değerini ekler.

> [!IMPORTANT]
> Kapsayıcı adlarının küçük harfle yazılması gerekir. Kapsayıcıları ve blobları adlandırma hakkında daha fazla bilgi için bkz. [Kapsayıcıları, Blobları ve Meta Verileri Adlandırma ve Bunlara Başvurma](/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata).

Ardından, [BlobContainerClient](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-blob/12.0.0/com/azure/storage/blob/BlobContainerClient.html) sınıfının bir örneğini oluşturun ve ardından depolama hesabınızdaki kapsayıcıyı oluşturmak için [oluşturma](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-blob/12.0.0/com/azure/storage/blob/BlobContainerClient.html#create--) yöntemini arayın.

Bu kodu `Main` yöntemin sonuna ekleyin:

```java
// Create a BlobServiceClient object which will be used to create a container client
BlobServiceClient blobServiceClient = new BlobServiceClientBuilder().connectionString(connectStr).buildClient();

//Create a unique name for the container
String containerName = "quickstartblobs" + java.util.UUID.randomUUID();

// Create the container and return a container client object
BlobContainerClient containerClient = blobServiceClient.createBlobContainer(containerName);
```

### <a name="upload-blobs-to-a-container"></a>Blob'ları bir kapsayıcıya yükleme

Aşağıdaki kod parçacığı:

1. Yerel *veri* dizininde bir metin dosyası oluşturur.
1. Kapsayıcı daki [getBlobClient](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-blob/12.0.0/com/azure/storage/blob/BlobContainerClient.html#getBlobClient-java.lang.String-) yöntemini [bir kapsayıcı oluştur](#create-a-container) bölümünden arayarak [BlobClient](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-blob/12.0.0/com/azure/storage/blob/BlobClient.html) nesnesine başvuru alır.
1. [UploadFromFile](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-blob/12.0.0/com/azure/storage/blob/BlobClient.html#uploadFromFile-java.lang.String-) yöntemini arayarak yerel metin dosyasını blob'a yükler. Bu yöntem, zaten yoksa blob oluşturur, ancak varsa üzerine yazmaz.

Bu kodu `Main` yöntemin sonuna ekleyin:

```java
// Create a local file in the ./data/ directory for uploading and downloading
String localPath = "./data/";
String fileName = "quickstart" + java.util.UUID.randomUUID() + ".txt";
File localFile = new File(localPath + fileName);

// Write text to the file
FileWriter writer = new FileWriter(localPath + fileName, true);
writer.write("Hello, World!");
writer.close();

// Get a reference to a blob
BlobClient blobClient = containerClient.getBlobClient(fileName);

System.out.println("\nUploading to Blob storage as blob:\n\t" + blobClient.getBlobUrl());

// Upload the blob
blobClient.uploadFromFile(localPath + fileName);
```

### <a name="list-the-blobs-in-a-container"></a>Blob’ları bir kapsayıcıda listeleme

[ListeBlobs](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-blob/12.0.0/com/azure/storage/blob/BlobContainerClient.html#listBlobs--) yöntemini arayarak konteynerdeki lekeleri listeleyin. Bu durumda, kapsayıcıya yalnızca bir blob eklenmiştir, bu nedenle listeleme işlemi yalnızca bir blob döndürür.

Bu kodu `Main` yöntemin sonuna ekleyin:

```java
System.out.println("\nListing blobs...");

// List the blob(s) in the container.
for (BlobItem blobItem : containerClient.listBlobs()) {
    System.out.println("\t" + blobItem.getName());
}
```

### <a name="download-blobs"></a>Blob’ları indirme

[DownloadToFile](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-blob/12.0.0/com/azure/storage/blob/specialized/BlobClientBase.html#downloadToFile-java.lang.String-) yöntemini arayarak daha önce oluşturulmuş blob'u indirin. Örnek kod, yerel dosya sisteminde her iki dosyayı görebilmeniz için dosya adına "DOWNLOAD" eki ekler.

Bu kodu `Main` yöntemin sonuna ekleyin:

```java
// Download the blob to a local file
// Append the string "DOWNLOAD" before the .txt extension so that you can see both files.
String downloadFileName = fileName.replace(".txt", "DOWNLOAD.txt");
File downloadedFile = new File(localPath + downloadFileName);

System.out.println("\nDownloading blob to\n\t " + localPath + downloadFileName);

blobClient.downloadToFile(localPath + downloadFileName);
```

### <a name="delete-a-container"></a>Kapsayıcı silme

Aşağıdaki kod, [silme](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-blob/12.0.0/com/azure/storage/blob/BlobContainerClient.html#delete--) yöntemini kullanarak tüm kapsayıcıyı kaldırarak uygulamanın oluşturduğu kaynakları temizler. Ayrıca uygulama tarafından oluşturulan yerel dosyaları da siler.

Uygulama, blob, kapsayıcı ve `System.console().readLine()` yerel dosyaları silmeden önce arayarak kullanıcı girişi için duraklar. Bu, kaynakların silinmeden önce doğru oluşturulduğunu doğrulamak için iyi bir şanstır.

Bu kodu `Main` yöntemin sonuna ekleyin:

```java
// Clean up
System.out.println("\nPress the Enter key to begin clean up");
System.console().readLine();

System.out.println("Deleting blob container...");
containerClient.delete();

System.out.println("Deleting the local source and downloaded files...");
localFile.delete();
downloadedFile.delete();

System.out.println("Done");
```

## <a name="run-the-code"></a>Kodu çalıştırma

Bu uygulama yerel klasörünüzde bir test dosyası oluşturur ve Blob depolama alanına yükler. Örnek daha sonra kapsayıcıdaki lekeleri listeler ve eski ve yeni dosyaları karşılaştırabilmeniz için dosyayı yeni bir adla karşıdan yükler.

*pom.xml* dosyasını içeren dizine gidin ve aşağıdaki `mvn` komutu kullanarak projeyi derle.

```console
mvn compile
```

Sonra, paketi oluşturun.

```console
mvn package
```

Uygulamayı çalıştırmak `mvn` için aşağıdaki komutu çalıştırın.

```console
mvn exec:java -Dexec.mainClass="com.blobs.quickstart.App" -Dexec.cleanupDaemonThreads=false
```

Uygulamanın çıktısı aşağıdaki örneğe benzer:

```output
Azure Blob storage v12 - Java quickstart sample

Uploading to Blob storage as blob:
        https://mystorageacct.blob.core.windows.net/quickstartblobsf9aa68a5-260e-47e6-bea2-2dcfcfa1fd9a/quickstarta9c3a53e-ae9d-4863-8b34-f3d807992d65.txt

Listing blobs...
        quickstarta9c3a53e-ae9d-4863-8b34-f3d807992d65.txt

Downloading blob to
        ./data/quickstarta9c3a53e-ae9d-4863-8b34-f3d807992d65DOWNLOAD.txt

Press the Enter key to begin clean up

Deleting blob container...
Deleting the local source and downloaded files...
Done
```

Temizleme işlemine başlamadan önce, iki dosya için *veri* klasörünüzü denetleyin. Dosyaları açarak aynı olduklarını görebilirsiniz.

Dosyaları doğruladıktan sonra, test dosyalarını silmek ve demoyu bitirmek için **Enter** tuşuna basın.

## <a name="next-steps"></a>Sonraki adımlar

Bu hızlı başlangıçta, Java'yı kullanarak blob'ları yüklemeyi, indirmeyi ve listelemeyi öğrendiniz.

Blob depolama örnek uygulamalarını görmek için şunları yapmaya devam edin:

> [!div class="nextstepaction"]
> [Azure Blob depolama SDK v12 Java örnekleri](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/storage/azure-storage-blob/src/samples/java/com/azure/storage/blob)

* Daha fazla bilgi için [Java için Azure SDK'ya](https://github.com/Azure/azure-sdk-for-java/blob/master/README.md)bakın.
* Öğreticiler, örnekler, hızlı başlangıçlar ve diğer belgeler için Java bulut geliştiricileri için Azure'u ziyaret [edin.](/azure/java/)
