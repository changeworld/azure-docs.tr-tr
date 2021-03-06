---
title: "Hızlı başlangıç: Bir Düğüm.js MongoDB uygulamasını Azure Cosmos DB'ye bağlayın"
description: Bu hızlı başlangıç, Node.js ile yazılmış mevcut bir MongoDB uygulamasının Azure Cosmos DB'ye nasıl bağlanılabildiğini gösterir.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.devlang: nodejs
ms.topic: quickstart
ms.date: 05/21/2019
ms.custom: seo-javascript-september2019, seo-javascript-october2019
ms.openlocfilehash: de4a9324cd1cfaccec6dcca6a8dfc057d37275c0
ms.sourcegitcommit: bc738d2986f9d9601921baf9dded778853489b16
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/02/2020
ms.locfileid: "80619233"
---
# <a name="quickstart-migrate-an-existing-mongodb-nodejs-web-app-to-azure-cosmos-db"></a>Hızlı başlangıç: Mevcut bir MongoDB Node.js web uygulamasını Azure Cosmos DB'ye geçirin 

> [!div class="op_single_selector"]
> * [.NET](create-mongodb-dotnet.md)
> * [Java](create-mongodb-java.md)
> * [Node.js](create-mongodb-nodejs.md)
> * [Python](create-mongodb-flask.md)
> * [Xamarin](create-mongodb-xamarin.md)
> * [Golang](create-mongodb-golang.md)
>  

Bu hızlı başlangıçta, Azure Bulut Kabuğu'nu kullanarak ve GitHub'dan klonlanan BIR ORTALAMA (MongoDB, Express, Angular ve Node.js) uygulamasıyla Mongo DB API hesabı için bir Azure Cosmos DB oluşturur ve yönetirsiniz. Azure Cosmos DB, belge, tablo, anahtar değeri ve grafik veritabanlarını küresel dağıtım ve yatay ölçek özelliklerine sahip hızlı bir şekilde oluşturmanıza ve sorgulamanıza olanak tanıyan çok modelli bir veritabanı hizmetidir.

## <a name="prerequisites"></a>Ön koşullar
- Etkin bir aboneliği olan bir Azure hesabı. [Ücretsiz bir tane oluşturun.](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) Veya Azure aboneliği olmadan [Azure Cosmos DB'yi ücretsiz olarak deneyin.](https://azure.microsoft.com/try/cosmosdb/) Bağlantı dizesini `.mongodb://localhost:C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==@localhost:10255/admin?ssl=true`içeren [Azure Cosmos DB Emülatörü](https://aka.ms/cosmosdb-emulator) de kullanabilirsiniz.
- [Düğüm.js](https://nodejs.org/), ve Node.js bir çalışma bilgisi.
- [Git.](https://git-scm.com/downloads)
- Azure Bulut Kabuğu'nu kullanmak istemiyorsanız, [Azure CLI 2.0+](/cli/azure/install-azure-cli).

[!INCLUDE [cloud-shell-try-it](../../includes/cloud-shell-try-it.md)]

## <a name="clone-the-sample-application"></a>Örnek uygulamayı kopyalama

Örnek depoyu kopyalamak için aşağıdaki komutları çalıştırın. Bu örnek depo, varsayılan [MEAN.js](https://meanjs.org/) uygulamasını içerir.

1. Bir komut istemini açın, git-samples adlı yeni bir klasör oluşturun ve komut istemini kapatın.

    ```bash
    mkdir "C:\git-samples"
    ```

2. Git Bash gibi bir Git terminal penceresi açın ve örnek uygulamayı yüklemek üzere yeni bir klasör olarak değiştirmek için `cd` komutunu kullanın.

    ```bash
    cd "C:\git-samples"
    ```

3. Örnek depoyu kopyalamak için aşağıdaki komutu çalıştırın. Bu komut bilgisayarınızda örnek uygulamanın bir kopyasını oluşturur. 

    ```bash
    git clone https://github.com/prashanthmadi/mean
    ```

## <a name="run-the-application"></a>Uygulamayı çalıştırma

Node.js ile yazılmış bu MongoDB uygulaması, MongoDB istemcisini destekleyen Azure Cosmos DB veritabanınıza bağlanır. Başka bir deyişle, verilerin bir Azure Cosmos DB veritabanında depolanması uygulama için saydamdır.

Gereken paketleri yükleyip uygulamayı başlatın.

```bash
cd mean
npm install
npm start
```
Uygulama bir MongoDB kaynağına bağlanmayı deneyip başarısız olur, çıkış "[MongoError: connect ECONNREFUSED 127.0.0.1:27017]" döndürdüğünde uygulamadan çıkabilirsiniz.

## <a name="sign-in-to-azure"></a>Azure'da oturum açma

CLI'yi yerel olarak yükleyip kullanmayı tercih ederseniz bu konu başlığı için Azure CLI 2.0 veya sonraki bir sürümünü kullanmanız gerekir. Sürümü bulmak için `az --version` komutunu çalıştırın. Yüklemeniz veya yükseltmeniz gerekiyorsa[ Azure CLI'yi yükle" konusuna bakın. 

Yüklü bir Azure CLI kullanıyorsanız, [az giriş](/cli/azure/reference-index#az-login) komutuyla Azure aboneliğinizde oturum açın ve ekrandaki yönergeleri izleyin. Azure Cloud Shell'i kullanıyorsanız bu adımı atlayabilirsiniz.

```azurecli
az login 
``` 
   
## <a name="add-the-azure-cosmos-db-module"></a>Azure Cosmos DB modülü ekleme

Yüklenen bir Azure CLI kullanıyorsanız `az` komutunu çalıştırarak `cosmosdb` bileşeninin zaten yüklü olup olmadığını kontrol edin. `cosmosdb`, temel komutlar listesindeyse bir sonraki komuta geçin. Azure Cloud Shell'i kullanıyorsanız bu adımı atlayabilirsiniz.

`cosmosdb`, temel komutlar listesinde değilse [Azure CLI](/cli/azure/install-azure-cli)'yi yeniden yükleyin.

## <a name="create-a-resource-group"></a>Kaynak grubu oluşturma

az [grubu](../azure-resource-manager/management/overview.md) oluşturmak ile bir kaynak grubu [oluşturun.](/cli/azure/group#az-group-create) Azure kaynak grubu; web uygulamaları, veritabanları ve depolama hesapları gibi Azure kaynaklarının dağıtıldığı ve yönetildiği bir mantıksal kapsayıcıdır. 

Aşağıdaki örnekte Batı Avrupa bölgesinde bir kaynak grubu oluşturulmaktadır. Kaynak grubu için benzersiz bir ad seçin.

Azure Bulut Uyp'ı kullanıyorsanız, **Try It'i**seçin, oturum açmak için ekrandaki istemleri izleyin ve ardından komut istemine kopyalayın.

```azurecli-interactive
az group create --name myResourceGroup --location "West Europe"
```

## <a name="create-an-azure-cosmos-db-account"></a>Azure Cosmos DB hesabı oluşturma

[Az cosmosdb oluşturma](/cli/azure/cosmosdb#az-cosmosdb-create) komutu ile bir Cosmos hesabı oluşturun.

Aşağıdaki komutta, lütfen yer tutucuyu gördüğünüz yerin `<cosmosdb-name>` kendi benzersiz Cosmos hesap adınızı değiştirin. Bu benzersiz ad, Cosmos DB bitiş noktanızın`https://<cosmosdb-name>.documents.azure.com/`bir parçası olarak kullanılacaktır, bu nedenle adın Azure'daki tüm Cosmos hesaplarında benzersiz olması gerekir. 

```azurecli-interactive
az cosmosdb create --name <cosmosdb-name> --resource-group myResourceGroup --kind MongoDB
```

`--kind MongoDB` parametresi MongoDB istemci bağlantılarını etkinleştirir.

Azure Cosmos DB hesabı oluşturulduğunda Azure CLI, aşağıdaki örneğe benzer bilgiler gösterir. 

> [!NOTE]
> Bu örnek, varsayılan Azure CLI çıktı biçimi olarak JSON kullanır. Başka bir çıktı biçimi kullanmak için bkz. [Azure CLI komutları için çıktı biçimleri](https://docs.microsoft.com/cli/azure/format-output-azure-cli).

```json
{
  "databaseAccountOfferType": "Standard",
  "documentEndpoint": "https://<cosmosdb-name>.documents.azure.com:443/",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Document
DB/databaseAccounts/<cosmosdb-name>",
  "kind": "MongoDB",
  "location": "West Europe",
  "name": "<cosmosdb-name>",
  "readLocations": [
    {
      "documentEndpoint": "https://<cosmosdb-name>-westeurope.documents.azure.com:443/",
      "failoverPriority": 0,
      "id": "<cosmosdb-name>-westeurope",
      "locationName": "West Europe",
      "provisioningState": "Succeeded"
    }
  ],
  "resourceGroup": "myResourceGroup",
  "type": "Microsoft.DocumentDB/databaseAccounts",
  "writeLocations": [
    {
      "documentEndpoint": "https://<cosmosdb-name>-westeurope.documents.azure.com:443/",
      "failoverPriority": 0,
      "id": "<cosmosdb-name>-westeurope",
      "locationName": "West Europe",
      "provisioningState": "Succeeded"
    }
  ]
} 
```

## <a name="connect-your-nodejs-application-to-the-database"></a>Node.js uygulamanızı veritabanına bağlama

Bu adımda, MEAN.js örnek uygulamanızı yeni oluşturduğunuz Azure Cosmos DB veritabanı hesabına bağlarsınız. 

<a name="devconfig"></a>
## <a name="configure-the-connection-string-in-your-nodejs-application"></a>Node.js uygulamanızda bağlantı dizesini yapılandırma

MEAN.js deponuzda `config/env/local-development.js` dosyasını açın.

Bu dosyanın içeriğini aşağıdaki kodla değiştirin. Ayrıca cosmos hesap `<cosmosdb-name>` adı ile iki yer tutucuları değiştirmeyi unutmayın.

```javascript
'use strict';

module.exports = {
  db: {
    uri: 'mongodb://<cosmosdb-name>:<primary_master_key>@<cosmosdb-name>.documents.azure.com:10255/mean-dev?ssl=true&sslverifycertificate=false'
  }
};
```

## <a name="retrieve-the-key"></a>Anahtarı alma

Cosmos veritabanına bağlanmak için veritabanı anahtarına ihtiyacınız vardır. Birincil anahtarı almak için [az cosmosdb tuşları liste](/cli/azure/cosmosdb/keys#az-cosmosdb-keys-list) komutunu kullanın.

```azurecli-interactive
az cosmosdb keys list --name <cosmosdb-name> --resource-group myResourceGroup --query "primaryMasterKey"
```

Azure CLI aşağıdaki örneğe benzer bilgiler çıkarır. 

```json
"RUayjYjixJDWG5xTqIiXjC..."
```

`primaryMasterKey` değerini kopyalayın. `local-development.js` içinde bulunan `<primary_master_key>` üzerine yapıştırın.

Yaptığınız değişiklikleri kaydedin.

### <a name="run-the-application-again"></a>Uygulamayı yeniden çalıştırın.

`npm start` komutunu yeniden çalıştırın. 

```bash
npm start
```

Bir konsol iletisi, geliştirme ortamının çalışır durumda olduğunu söyleyecektir. 

Tarayıcıya `http://localhost:3000` gidin. Üst menüde **Kaydol'u** seçin ve iki sahte kullanıcı oluşturmayı deneyin. 

MEAN.js örnek uygulaması, kullanıcı verilerini veritabanında depolar. Başarılı olursanız ve MEAN.js oluşturulan kullanıcının oturumunu otomatik olarak açarsa, Azure Cosmos DB bağlantınız çalışıyordur. 

![MEAN.js, MongoDB’ye başarıyla bağlanır](./media/create-mongodb-nodejs/mongodb-connect-success.png)

## <a name="view-data-in-data-explorer"></a>Veri Gezgini’nde verileri görüntüleme

Cosmos veritabanında depolanan veriler Azure portalında görüntülenebilir ve sorgulanır.

Önceki adımda oluşturulan verileri görüntülemek, sorgulamak ve üzerinde çalışmak için web tarayıcınızda [Azure portalı](https://portal.azure.com) oturumunu açın.

Üstteki Arama kutusuna **Azure Cosmos DB**girin. Cosmos hesap bıçağınız açıldığında Cosmos hesabınızı seçin. Sol gezintide **Veri Gezgini'ni**seçin. Koleksiyonlar bölmesinde koleksiyonunuzu genişletin; bundan sonra koleksiyondaki belgeleri görüntüleyebilir, verileri sorgulayabilir ve hatta saklı yordam, tetikleyici ve UDF’ler oluşturup çalıştırabilirsiniz. 

![Azure portalında Veri Gezgini](./media/create-mongodb-nodejs/cosmosdb-connect-mongodb-data-explorer.png)


## <a name="deploy-the-nodejs-application-to-azure"></a>Node.js uygulamasını Azure’a dağıtma

Bu adımda, Düğüm.js uygulamanızı Cosmos DB'ye dağıtırsınız.

Daha önce değiştirdiğiniz yapılandırma dosyasının geliştirme ortamına (`/config/env/local-development.js`) yönelik olduğunu fark etmiş olabilirsiniz. Uygulamanızı App Service’e dağıttığınızda, varsayılan olarak üretim ortamında çalıştırılır. O halde şimdi, ilgili yapılandırma dosyasında aynı değişikliği yapmanız gerekir.

MEAN.js deponuzda `config/env/production.js` dosyasını açın.

`db` nesnesinde `uri` değerini aşağıdaki örnekte gösterilen şekilde değiştirin. Yer tutucuları daha önceki gibi değiştirdiğinizden emin olun.

```javascript
'mongodb://<cosmosdb-name>:<primary_master_key>@<cosmosdb-name>.documents.azure.com:10255/mean?ssl=true&sslverifycertificate=false',
```

> [!NOTE] 
> Bu `ssl=true` seçenek Cosmos DB gereksinimleri nedeniyle önemlidir. Daha fazla bilgi için [Bağlantı dize gereksinimlerine](connect-mongodb-account.md#connection-string-requirements)bakın.
>
>

Terminalde tüm değişikliklerinizi Git’e uygulayın. Her iki komutu birlikte çalıştırmak üzere kopyalayabilirsiniz.

```bash
git add .
git commit -m "configured MongoDB connection string"
```
## <a name="clean-up-resources"></a>Kaynakları temizleme

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>Sonraki adımlar

Bu hızlı başlangıçta, Azure Bulut Kabuğu'nu kullanarak bir Azure Cosmos DB MongoDB API hesabı oluşturmayı ve kullanıcıları hesaba eklemek için bir MEAN.js uygulaması oluşturmayı ve çalıştırmayı öğrendiniz. Şimdi Azure Cosmos DB hesabınıza ek veriler aktarabilirsiniz.

> [!div class="nextstepaction"]
> [Azure Cosmos DB’ye MongoDB verileri aktarma](mongodb-migrate.md)
