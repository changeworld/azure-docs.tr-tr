---
title: Python'u kullanarak Azure Cosmos DB Tablo API'sini ve Azure Tablo depolama alanını kullanma
description: Azure Tablo Depolama veya Azure Cosmos DB Tablo API’sini kullanarak yapılandırılmış verileri bulutta depolayın.
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.devlang: python
ms.topic: sample
ms.date: 04/05/2018
author: sakash279
ms.author: akshanka
ms.reviewer: sngun
ms.openlocfilehash: b3a6f4397ca1b8c56f06f6d967804c94096ee308
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/24/2020
ms.locfileid: "76770999"
---
# <a name="get-started-with-azure-table-storage-and-the-azure-cosmos-db-table-api-using-python"></a>Python kullanarak Azure Tablo depolamayı ve Azure Cosmos DB Tablo API’sini kullanmaya başlama

[!INCLUDE [storage-selector-table-include](../../includes/storage-selector-table-include.md)]
[!INCLUDE [storage-table-applies-to-storagetable-and-cosmos](../../includes/storage-table-applies-to-storagetable-and-cosmos.md)]

Azure Tablo depolaması ve Azure Cosmos DB, yapısal NoSQL verilerini bulutta saklayarak bir anahtar/öznitelik deposunu şemasız bir tasarımla sunan hizmetlerdir. Tablo depolaması ve Azure Cosmos DB'si şemasız olduğu için uygulamanızın ihtiyaçları geliştikçe verilerinizi kolayca uyarlayabilirsiniz. Tablo depolamaya ve Tablo API verilerine erişim, çoğu uygulama türü için hızlı ve ekonomik olmanın yanı sıra benzer veri hacimleri için geleneksel SQL'e kıyasla genellikle daha düşük maliyetlidir.

Tablo depolamayı veya Azure Cosmos DB'yi Web uygulamaları için kullanıcı verileri, adres defterleri, cihaz bilgileri veya hizmetiniz için gerekli olan tüm diğer meta veri türleri gibi esnek veri kümelerini depolamak için kullanabilirsiniz. Bir tabloda istediğiniz kadar varlık depolayabilirsiniz ve bir depolama hesabı kapasite limitini dolduracak kadar tablo içerebilir.

### <a name="about-this-sample"></a>Bu örnek hakkında
Bu örnek, [Python için Azure Cosmos DB Tablosu SDK'sını](https://pypi.python.org/pypi/azure-cosmosdb-table/) tipik Azure Tablo depolama senaryolarında kullanmayı göstermektedir. Adı Azure Cosmos DB ile kullanılmaya yönelik olduğunu gösterse de SDK hem Azure Cosmos DB, hem Azure Tablo depolaması ile çalışır ve her hizmetin benzersiz bir uç noktası vardır. Bu senaryolarda aşağıdakileri yapmayı gösteren Python örnekleri kullanılır:
* Tablo oluşturma ve silme
* Varlık ekleme ve sorgulama
* Varlıkları değiştirme

Bu örnekteki senaryolarda çalışırken [Python API'si için Azure Cosmos DB SDK'sı belgelerine](https://docs.microsoft.com/python/api/overview/azure/cosmosdb?view=azure-python) başvurmanız önerilir.

## <a name="prerequisites"></a>Ön koşullar

Bu örneği başarıyla tamamlamak için aşağıdakiler gerekir:

- [Python](https://www.python.org/downloads/) 2.7, 3.3, 3.4, 3.5 ya da 3.6
- [Python için Azure Cosmos DB Tablo SDK](https://pypi.python.org/pypi/azure-cosmosdb-table/). Bu SDK, hem Azure Tablo depolaması, hem de Azure Cosmos DB Tablo API'sine bağlanır.
- [Azure Depolama hesabı](../storage/common/storage-account-create.md) veya [Azure Cosmos DB hesabı](https://azure.microsoft.com/try/cosmosdb/)

## <a name="create-an-azure-service-account"></a>Azure hizmet hesabı oluşturma
[!INCLUDE [cosmos-db-create-azure-service-account](../../includes/cosmos-db-create-azure-service-account.md)]

### <a name="create-an-azure-storage-account"></a>Azure Storage hesabı oluşturma
[!INCLUDE [cosmos-db-create-storage-account](../../includes/cosmos-db-create-storage-account.md)]

### <a name="create-an-azure-cosmos-db-table-api-account"></a>Azure Cosmos DB Tablo API’si hesabı oluşturma
[!INCLUDE [cosmos-db-create-tableapi-account](../../includes/cosmos-db-create-tableapi-account.md)]

## <a name="install-the-azure-cosmos-db-table-sdk-for-python"></a>Python için Azure Cosmos DB Tablosu SDK'sını yükleme

Bir Depolama hesabı oluşturduktan sonraki adımınız [Python için Microsoft Azure Cosmos DB Tablosu SDK'sını](https://pypi.python.org/pypi/azure-cosmosdb-table/) yüklemektir. SDK'yı yükleme adımları için GitHub'daki Python için Cosmos DB Tablo SDK'sı deposunda bulunan [README.rst](https://github.com/Azure/azure-cosmosdb-python/blob/master/azure-cosmosdb-table/README.rst) dosyasına bakın.

## <a name="import-the-tableservice-and-entity-classes"></a>TableService ve Entity sınıflarını içeri aktarma

Python'da Azure Tablo Depolama’daki varlıklarla çalışmak için [TableService][py_TableService] ve [Entity][py_Entity] sınıflarını kullanırsınız. Bunların ikisini de içeri aktarmak için şu kodu Python dosyanızın başına ekleyin:

```python
from azure.cosmosdb.table.tableservice import TableService
from azure.cosmosdb.table.models import Entity
```

## <a name="connect-to-azure-table-service"></a>Azure Tablo Depolama hizmetine bağlanma

Azure Depolama Tablo hizmetine bağlanmak için, bir [TableService][py_TableService] nesnesi oluşturun ve Depolama hesabınızın adını ve hesap anahtarını geçin. `myaccount` ve `mykey` ifadelerini hesap adınız ve anahtarınız ile değiştirin.

```python
table_service = TableService(account_name='myaccount', account_key='mykey')
```

## <a name="connect-to-azure-cosmos-db"></a>Azure Cosmos DB’ye bağlanma

Azure Cosmos DB'ye bağlanmak için, Azure portalından birincil bağlantı dizenizi kopyalayın ve kopyaladığınız bağlantı dizesini kullanarak bir [TableService][py_TableService] nesnesi oluşturun:

```python
table_service = TableService(connection_string='DefaultEndpointsProtocol=https;AccountName=myaccount;AccountKey=mykey;TableEndpoint=myendpoint;')
```

## <a name="create-a-table"></a>Bir tablo oluşturma

Tabloyu oluşturmak için [create_table][py_create_table] işlevini çağırın.

```python
table_service.create_table('tasktable')
```

## <a name="add-an-entity-to-a-table"></a>Tabloya bir varlık ekleme

Bir varlık eklemek için önce varlığınızı temsil eden bir nesne oluşturur, sonra bu nesneyi [TableService.insert_entity yöntemine][py_TableService] geçersiniz. Varlık nesnesi bir sözlük veya [Entity][py_Entity] türünde bir nesne olabilir ve varlığınızın özellik adlarını ve değerlerini tanımlar. Her varlık, tanımladığınız diğer özelliklere ek olarak zorunlu [PartitionKey ve RowKey](#partitionkey-and-rowkey) özelliklerini de içermelidir.

Bu örnek, bir varlığı temsil eden bir sözlük oluşturmakta, sonra bunu, tabloya eklemek amacıyla [insert_entity][py_insert_entity] yöntemine geçmektedir:

```python
task = {'PartitionKey': 'tasksSeattle', 'RowKey': '001',
        'description': 'Take out the trash', 'priority': 200}
table_service.insert_entity('tasktable', task)
```

Bu örnek bir [Entity][py_Entity] nesnesi oluşturmakta, sonra bunu, tabloya eklemek amacıyla [insert_entity][py_insert_entity] yöntemine geçmektedir:

```python
task = Entity()
task.PartitionKey = 'tasksSeattle'
task.RowKey = '002'
task.description = 'Wash the car'
task.priority = 100
table_service.insert_entity('tasktable', task)
```

### <a name="partitionkey-and-rowkey"></a>PartitionKey ve RowKey

Her varlık için bir **PartitionKey** ve bir de **RowKey** özelliği belirtmeniz gerekir. Bunlar, birlikte bir varlığın birincil anahtarını oluşturduklarından varlıklarınızın benzersiz tanımlayıcılarıdır. Yalnızca bu değerlerin dizini oluşturulduğundan, bu değerleri kullanarak, diğer varlık özellikleriyle yapabildiğinizden çok daha hızlı olarak sorgulama yapabilirsiniz.

Table hizmeti, tablo varlıklarını depolama düğümlerine akıllıca dağıtmak için **PartitionKey** değerini kullanır. Aynı **PartitionKey** değerine sahip olan varlıklar aynı düğüme depolanır. **RowKey**, varlığın ait olduğu bölümdeki benzersiz kimliğidir.

## <a name="update-an-entity"></a>Varlığı güncelleştirme

Bir varlığın tüm özellik değerlerini güncelleştirmek için, [update_entity][py_update_entity] yöntemini çağırın. Bu örnek, mevcut bir varlığın güncelleştirilmiş bir sürümle değiştirilmesini göstermektedir:

```python
task = {'PartitionKey': 'tasksSeattle', 'RowKey': '001',
        'description': 'Take out the garbage', 'priority': 250}
table_service.update_entity('tasktable', task)
```

Güncelleştirilen varlık zaten mevcut değilse, güncelleştirme işlemi başarısız olur. Bir varlığı mevcut olsun veya olmasın depolamak istiyorsanız, [insert_or_replace_entity][py_insert_or_replace_entity] kullanın. Aşağıdaki örnekte ilk çağrı mevcut varlığı başkasıyla değiştirmektedir. İkinci çağrı, belirtilen PartitionKey ve RowKey değerlerine sahip bir varlık tabloda olmadığından, yeni bir varlık eklemektedir.

```python
# Replace the entity created earlier
task = {'PartitionKey': 'tasksSeattle', 'RowKey': '001',
        'description': 'Take out the garbage again', 'priority': 250}
table_service.insert_or_replace_entity('tasktable', task)

# Insert a new entity
task = {'PartitionKey': 'tasksSeattle', 'RowKey': '003',
        'description': 'Buy detergent', 'priority': 300}
table_service.insert_or_replace_entity('tasktable', task)
```

> [!TIP]
> [update_entity][py_update_entity] yöntemi mevcut bir varlığın tüm özellik ve değerlerini başkalarıyla değiştirir ve mevcut bir varlıktan özellik kaldırma için de kullanılabilir. [merge_entity][py_merge_entity] yöntemini mevcut bir varlığı başkasıyla değiştirmeden yeni veya değiştirilmiş değerler ile güncelleştirmek için kullanabilirsiniz.

## <a name="modify-multiple-entities"></a>Birden çok varlığı değiştirme

Bir isteğin Table hizmeti tarafından atomik olarak işlendiğinden emin olmak için, birden çok işlemi toplu iş olarak gönderebilirsiniz. Önce birden çok işlemi tek bir toplu işe eklemek için [TableBatch][py_TableBatch] sınıfını kullanın. Ardından, işlemleri atomik bir işlemle göndermek için [TableService][py_TableService].[commit_batch][py_commit_batch] yöntemini çağırın. Toplu bir işlem ile değiştirilecek tüm varlıklar aynı bölümde olmalıdır.

Bu örnek iki varlığı tek bir toplu işe eklemektedir:

```python
from azure.cosmosdb.table.tablebatch import TableBatch
batch = TableBatch()
task004 = {'PartitionKey': 'tasksSeattle', 'RowKey': '004',
           'description': 'Go grocery shopping', 'priority': 400}
task005 = {'PartitionKey': 'tasksSeattle', 'RowKey': '005',
           'description': 'Clean the bathroom', 'priority': 100}
batch.insert_entity(task004)
batch.insert_entity(task005)
table_service.commit_batch('tasktable', batch)
```

Toplu işler bağlam yöneticisi söz dizimiyle de kullanılabilir:

```python
task006 = {'PartitionKey': 'tasksSeattle', 'RowKey': '006',
           'description': 'Go grocery shopping', 'priority': 400}
task007 = {'PartitionKey': 'tasksSeattle', 'RowKey': '007',
           'description': 'Clean the bathroom', 'priority': 100}

with table_service.batch('tasktable') as batch:
    batch.insert_entity(task006)
    batch.insert_entity(task007)
```

## <a name="query-for-an-entity"></a>Varlık sorgulama

Bir tablodaki bir varlığı sorgulamak için, varlığın PartitionKey ve RowKey değerlerini [TableService][py_TableService].[get_entity][py_get_entity] yöntemine geçin.

```python
task = table_service.get_entity('tasktable', 'tasksSeattle', '001')
print(task.description)
print(task.priority)
```

## <a name="query-a-set-of-entities"></a>Varlık kümesini sorgulama

Bir varlık kümesini **filter** parametresiyle bir filtre dizesi sağlayarak sorgulayabilirsiniz. Bu örnekte PartitionKey değerine bir filtre uygulanarak Seattle'daki tüm görevler bulunmaktadır:

```python
tasks = table_service.query_entities(
    'tasktable', filter="PartitionKey eq 'tasksSeattle'")
for task in tasks:
    print(task.description)
    print(task.priority)
```

## <a name="query-a-subset-of-entity-properties"></a>Giriş özellikleri alt kümesi sorgulama

Ayrıca bir sorguda her varlık için döndürülen özellikleri sınırlayabilirsiniz. *Projeksiyon* olarak adlandırılan bu yöntem bant genişliğini azaltır ve özellikle büyük varlıklar veya sonuç kümeleri için sorgu performansını iyileştirebilir. **Select** parametresini kullanarak istemciye döndürülmesini istediğiniz özelliklerin adlarını geçin.

Aşağıdaki kodda yer alan sorgu, tablodaki varlıkların yalnızca açıklamalarını döndürmektedir.

> [!NOTE]
> Aşağıdaki kod parçacığı yalnızca Azure Depolama üzerinde çalışır. Depolama öykünücüsü tarafından desteklenmez.

```python
tasks = table_service.query_entities(
    'tasktable', filter="PartitionKey eq 'tasksSeattle'", select='description')
for task in tasks:
    print(task.description)
```

## <a name="delete-an-entity"></a>Bir varlığı silme

Bir varlığı silmek için **PartitionKey** ve **RowKey** değerlerini [delete_entity][py_delete_entity] yöntemine geçin.

```python
table_service.delete_entity('tasktable', 'tasksSeattle', '001')
```

## <a name="delete-a-table"></a>Bir tablo silme

Bir tabloya veya tablodaki varlıklara artık ihtiyacınız yoksa, tabloyu Azure Depolama'dan kalıcı olarak silmek için [delete_table][py_delete_table] yöntemini çağırın.

```python
table_service.delete_table('tasktable')
```

## <a name="next-steps"></a>Sonraki adımlar

* [SSS - Tablo API'siyle geliştirme](https://docs.microsoft.com/azure/cosmos-db/faq)
* [Python API'si için Azure Cosmos DB SDK'sı belgeleri](https://docs.microsoft.com/python/api/overview/azure/cosmosdb?view=azure-python)
* [Python Geliştirici Merkezi](https://azure.microsoft.com/develop/python/)
* [Microsoft Azure Depolama Gezgini](../vs-azure-tools-storage-manage-with-storage-explorer.md): Windows'da, macOS'te ve Linux'ta Azure Depolama verileriyle görsel olarak çalışmak için ücretsiz, platformlar arası bir uygulama.
* [Visual Studio'da (Windows) Python ile çalışma](https://docs.microsoft.com/visualstudio/python/overview-of-python-tools-for-visual-studio)


[py_commit_batch]: https://docs.microsoft.com/python/api/azure-cosmosdb-table/azure.cosmosdb.table.tableservice.tableservice?view=azure-python
[py_create_table]: https://docs.microsoft.com/python/api/azure-cosmosdb-table/azure.cosmosdb.table.tableservice.tableservice?view=azure-python
[py_delete_entity]: https://docs.microsoft.com/python/api/azure-cosmosdb-table/azure.cosmosdb.table.tableservice.tableservice?view=azure-python
[py_get_entity]: https://docs.microsoft.com/python/api/azure-cosmosdb-table/azure.cosmosdb.table.tableservice.tableservice?view=azure-python
[py_insert_entity]: https://docs.microsoft.com/python/api/azure-cosmosdb-table/azure.cosmosdb.table.tableservice.tableservice?view=azure-python
[py_insert_or_replace_entity]: https://docs.microsoft.com/python/api/azure-cosmosdb-table/azure.cosmosdb.table.tableservice.tableservice?view=azure-python
[py_Entity]: https://docs.microsoft.com/python/api/azure-cosmosdb-table/azure.cosmosdb.table.models.entity?view=azure-python
[py_merge_entity]: https://docs.microsoft.com/python/api/azure-cosmosdb-table/azure.cosmosdb.table.tableservice.tableservice?view=azure-python
[py_update_entity]: https://docs.microsoft.com/python/api/azure-cosmosdb-table/azure.cosmosdb.table.tableservice.tableservice?view=azure-python
[py_delete_table]: https://docs.microsoft.com/python/api/azure-cosmosdb-table/azure.cosmosdb.table.tableservice.tableservice?view=azure-python
[py_TableService]: https://docs.microsoft.com/python/api/azure-cosmosdb-table/azure.cosmosdb.table.tableservice.tableservice?view=azure-python
[py_TableBatch]: https://docs.microsoft.com/python/api/azure-cosmosdb-table/azure.cosmosdb.table.tableservice.tableservice?view=azure-python
