---
title: Azure Cosmos DB'nin MongoDB (3.2 sürümü) için api destekli özellikleri ve sözdizimi
description: Azure Cosmos DB'nin MongoDB (3,2 sürüm) destekli özellikler ve sözdizimi için API'si hakkında bilgi edinin.
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: overview
ms.date: 10/16/2019
author: sivethe
ms.author: sivethe
ms.openlocfilehash: 94b65b4e7947bc02b1fdaae90c8f774ec216e7bb
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/09/2020
ms.locfileid: "80981894"
---
# <a name="azure-cosmos-dbs-api-for-mongodb-32-version-supported-features-and-syntax"></a>MongoDB için Azure Cosmos DB’nin API’si (sürüm 3.2): desteklenen özellikler ve söz dizimi

Azure Cosmos DB, Microsoft'un genel olarak dağıtılmış çok modelli veritabanı hizmetidir. Açık kaynak MongoDB istemci [sürücülerinden](https://docs.mongodb.org/ecosystem/drivers)herhangi birini kullanarak Azure Cosmos DB'nin MongoDB api'si ile iletişim kurabilirsiniz. Azure Cosmos DB'nin MongoDB için API'si, MongoDB [tel protokolüne](https://docs.mongodb.org/manual/reference/mongodb-wire-protocol)bağlı kalarak varolan istemci sürücülerin kullanılmasını sağlar.

Azure Cosmos DB'nin MongoDB için API'sini kullanarak, Cosmos DB'nin sağladığı tüm kurumsal özelliklerle alışık olduğunuz MongoDB'nin avantajlarından yararlanabilirsiniz: [küresel dağıtım,](distribute-data-globally.md) [otomatik parçalama,](partition-data.md)kullanılabilirlik ve gecikme garantileri, her alanın otomatik dizinedahilliği, istirahatte şifreleme, yedeklemeler ve çok daha fazlası.

> [!NOTE]
> Bu makale, Azure Cosmos DB'nin MongoDB 3.2 için API'si içindir. MongoDB 3.6 sürümü için [MongoDB 3.6 desteklenen özellikler ve sözdizimi](mongodb-feature-support-36.md)bakın.

## <a name="protocol-support"></a>Protokol Desteği

Azure Cosmos DB'nin MongoDB için API'si için tüm yeni hesaplar MongoDB sunucu sürümü **3.6**ile uyumludur. Bu makalede MongoDB sürüm 3.2 ele alınır. Desteklenen işleçler ve tüm sınırlamalar veya özel durumlar aşağıda listelenmiştir. Bu protokolleri anlayan tüm istemci sürücüleri MongoDB için Azure Cosmos DB’nin API’sine bağlanabilmesi gerekir.

## <a name="query-language-support"></a>Dil desteğini sorgula

Azure Cosmos DB'nin MongoDB için API'si, MongoDB sorgu dili yapıları için kapsamlı destek sağlar. Aşağıda şu anda desteklenen işlem, işleç, aşama, komut ve seçeneklerin ayrıntılı bir listesini bulabilirsiniz.

## <a name="database-commands"></a>Veritabanı komutları

Azure Cosmos DB'nin MongoDB için API'si aşağıdaki veritabanı komutlarını destekler:

### <a name="query-and-write-operation-commands"></a>Sorgulama ve yazma işlemi komutları

- delete
- find
- findAndModify
- getLastError
- getMore
- insert
- update

### <a name="authentication-commands"></a>Kimlik doğrulama komutları

- logout
- authenticate
- getnonce

### <a name="administration-commands"></a>Yönetim komutları

- dropDatabase
- listCollections
- drop
- oluşturmaya
- filemd5
- createIndexes
- listIndexes
- dropIndexes
- connectionStatus
- reIndex

### <a name="diagnostics-commands"></a>Tanılama komutları

- buildInfo
- collStats
- dbStats
- hostInfo
- listDatabases
- whatsmyuri

<a name="aggregation-pipeline"/>

## <a name="aggregation-pipelinea"></a>Toplama ardışık düzeni</a>

Cosmos DB genel önizleme MongoDB 3.2 için toplama boru hattı destekler. Genel önizlemenin katılımı hakkında yönergeler için [Azure bloguna](https://azure.microsoft.com/blog/azure-cosmosdb-extends-support-for-mongodb-aggregation-pipeline-unique-indexes-and-more/) bakın.

### <a name="aggregation-commands"></a>Toplama komutları

- aggregate
- count
- distinct

### <a name="aggregation-stages"></a>Toplama aşamaları

- $project
- $match
- $limit
- $skip
- $unwind
- $group
- $sample
- $sort
- $lookup
- $out
- $count
- $addFields

### <a name="aggregation-expressions"></a>Toplama ifadeleri

#### <a name="boolean-expressions"></a>Mantıksal ifadeler

- $and
- $or
- $not

#### <a name="set-expressions"></a>Küme ifadeleri

- $setEquals
- $setIntersection
- $setUnion
- $setDifference
- $setIsSubset
- $anyElementTrue
- $allElementsTrue

#### <a name="comparison-expressions"></a>Karşılaştırma ifadeleri

- $cmp
- $eq
- $gt
- $gte
- $lt
- $lte
- $ne

#### <a name="arithmetic-expressions"></a>Aritmetik ifadeler

- $abs
- $add
- $ceil
- $divide
- $exp
- $floor
- $ln
- $log
- $log10
- $mod
- $multiply
- $pow
- $sqrt
- $subtract
- $trunc

#### <a name="string-expressions"></a>Dize ifadeleri

- $concat
- $indexOfBytes
- $indexOfCP
- $split
- $strLenBytes
- $strLenCP
- $strcasecmp
- $substr
- $substrBytes
- $substrCP
- $toLower
- $toUpper

#### <a name="array-expressions"></a>Dizi ifadeleri

- $arrayElemAt
- $concatArrays
- $filter
- $indexOfArray
- $isArray
- $range
- $reverseArray
- $size
- $slice
- $in

#### <a name="date-expressions"></a>Tarih ifadeleri

- $dayOfYear
- $dayOfMonth
- $dayOfWeek
- $year
- $month
- $week
- $hour
- $minute
- $second
- $millisecond
- $isoDayOfWeek
- $isoWeek

#### <a name="conditional-expressions"></a>Koşullu ifadeler

- $cond
- $ifNull

## <a name="aggregation-accumulators"></a>Toplama biriktiricileri

- $sum
- $avg
- $first
- $last
- $max
- $min
- $push
- $addToSet

## <a name="operators"></a>İşleçler

Desteklenen işleçler, bunların kullanımıyla ilgili örneklerle birlikte aşağıda verilmiştir. Sorgularda kullanılan bu örnek belgeyi göz önünde bulundurun:

```json
{
  "Volcano Name": "Rainier",
  "Country": "United States",
  "Region": "US-Washington",
  "Location": {
    "type": "Point",
    "coordinates": [
      -121.758,
      46.87
    ]
  },
  "Elevation": 4392,
  "Type": "Stratovolcano",
  "Status": "Dendrochronology",
  "Last Known Eruption": "Last known eruption from 1800-1899, inclusive"
}
```

İşleç | Örnek |
--- | --- |
$eq | `{ "Volcano Name": { $eq: "Rainier" } }` |  | -
$gt | `{ "Elevation": { $gt: 4000 } }` |  | -
$gte | `{ "Elevation": { $gte: 4392 } }` |  | -
$lt | `{ "Elevation": { $lt: 5000 } }` |  | -
$lte | `{ "Elevation": { $lte: 5000 } }` | | -
$ne | `{ "Elevation": { $ne: 1 } }` |  | -
$in | `{ "Volcano Name": { $in: ["St. Helens", "Rainier", "Glacier Peak"] } }` |  | -
$nin | `{ "Volcano Name": { $nin: ["Lassen Peak", "Hood", "Baker"] } }` | | -
$or | `{ $or: [ { Elevation: { $lt: 4000 } }, { "Volcano Name": "Rainier" } ] }` |  | -
$and | `{ $and: [ { Elevation: { $gt: 4000 } }, { "Volcano Name": "Rainier" } ] }` |  | -
$not | `{ "Elevation": { $not: { $gt: 5000 } } }`|  | -
$nor | `{ $nor: [ { "Elevation": { $lt: 4000 } }, { "Volcano Name": "Baker" } ] }` |  | -
$exists | `{ "Status": { $exists: true } }`|  | -
$type | `{ "Status": { $type: "string" } }`|  | -
$mod | `{ "Elevation": { $mod: [ 4, 0 ] } }` |  | -
$regex | `{ "Volcano Name": { $regex: "^Rain"} }`|  | -

### <a name="notes"></a>Notlar

Normal ifade ($regex) sorgularında, soldan başlayan ifadeler dizin aramasına izin verir. Ancak 'i' değiştiricisini (büyük/küçük harf duyarlığı) ve 'm' değiştiricisini (çok satırlılık) kullanmak, tüm ifadelerde koleksiyon taramasına neden olur.
'$' veya '|' karakterinin dahil edilmesinin gerektiği durumlarda iki (veya daha fazla) normal ifade sorgusu oluşturmak en iyisidir.
Örneğin şu özgün sorgu: ```find({x:{$regex: /^abc$/})``` şu şekilde değiştirilmelidir: ```find({x:{$regex: /^abc/, x:{$regex:/^abc$/}})```.
İlk kısım aramayı ^abc ile başlayan belgeler ile sınırlamak için dizini kullanır, ikinci kısım ise tam girişler ile eşleşir.
Çubuk işleci '|' "veya" işlevini görür - ```find({x:{$regex: /^abc|^def/})``` sorgusu 'x' alanının değerlerinin "abc" ile veya "def" ile başladığı belgelerle eşleşir. Dizinden yararlanmak için sorgunun $or işleci ile birleştirilen iki farklı sorguya bölünmesi gerekir: ```find( {$or : [{x: $regex: /^abc/}, {$regex: /^def/}] })```.

### <a name="update-operators"></a>Güncelleştirme işleçleri

#### <a name="field-update-operators"></a>Alan güncelleştirme işleçleri

- $inc
- $mul
- $rename
- $setOnInsert
- $set
- $unset
- $min
- $max
- $currentDate

#### <a name="array-update-operators"></a>Dizi güncelleştirme işleçleri

- $addToSet
- $pop
- $pullAll
- $pull (Not: koşulu $pull desteklenmez)
- $pushAll
- $push
- $each
- $slice
- $sort
- $position

#### <a name="bitwise-update-operator"></a>Bit düzeyinde güncelleştirme işleci

- $bit

### <a name="geospatial-operators"></a>Jeo-uzamsal işleçler

İşleç | Örnek | |
--- | --- | --- |
$geoWithin | ```{ "Location.coordinates": { $geoWithin: { $centerSphere: [ [ -121, 46 ], 5 ] } } }``` | Evet |
$geoIntersects |  ```{ "Location.coordinates": { $geoIntersects: { $geometry: { type: "Polygon", coordinates: [ [ [ -121.9, 46.7 ], [ -121.5, 46.7 ], [ -121.5, 46.9 ], [ -121.9, 46.9 ], [ -121.9, 46.7 ] ] ] } } } }``` | Evet |
$near | ```{ "Location.coordinates": { $near: { $geometry: { type: "Polygon", coordinates: [ [ [ -121.9, 46.7 ], [ -121.5, 46.7 ], [ -121.5, 46.9 ], [ -121.9, 46.9 ], [ -121.9, 46.7 ] ] ] } } } }``` | Evet |
$nearSphere | ```{ "Location.coordinates": { $nearSphere : [ -121, 46  ], $maxDistance: 0.50 } }``` | Evet |
$geometry | ```{ "Location.coordinates": { $geoWithin: { $geometry: { type: "Polygon", coordinates: [ [ [ -121.9, 46.7 ], [ -121.5, 46.7 ], [ -121.5, 46.9 ], [ -121.9, 46.9 ], [ -121.9, 46.7 ] ] ] } } } }``` | Evet |
$minDistance | ```{ "Location.coordinates": { $nearSphere : { $geometry: {type: "Point", coordinates: [ -121, 46 ]}, $minDistance: 1000, $maxDistance: 1000000 } } }``` | Evet |
$maxDistance | ```{ "Location.coordinates": { $nearSphere : [ -121, 46  ], $maxDistance: 0.50 } }``` | Evet |
$center | ```{ "Location.coordinates": { $geoWithin: { $center: [ [-121, 46], 1 ] } } }``` | Evet |
$centerSphere | ```{ "Location.coordinates": { $geoWithin: { $centerSphere: [ [ -121, 46 ], 5 ] } } }``` | Evet |
$box | ```{ "Location.coordinates": { $geoWithin: { $box:  [ [ 0, 0 ], [ -122, 47 ] ] } } }``` | Evet |
$polygon | ```{ "Location.coordinates": { $near: { $geometry: { type: "Polygon", coordinates: [ [ [ -121.9, 46.7 ], [ -121.5, 46.7 ], [ -121.5, 46.9 ], [ -121.9, 46.9 ], [ -121.9, 46.7 ] ] ] } } } }``` | Evet |

## <a name="sort-operations"></a>İşlemleri Sırala

`findOneAndUpdate` İşlem işlendirilirken, tek bir alandaki sınıf işleyişleri desteklenir, ancak birden fazla alandaki sıkılım işlemi desteklenmez.

## <a name="additional-operators"></a>Ek işleçler

İşleç | Örnek | Notlar
--- | --- | --- |
$all | ```{ "Location.coordinates": { $all: [-121.758, 46.87] } }``` |
$elemMatch | ```{ "Location.coordinates": { $elemMatch: {  $lt: 0 } } }``` |
$size | ```{ "Location.coordinates": { $size: 2 } }``` |
$comment |  ```{ "Location.coordinates": { $elemMatch: {  $lt: 0 } }, $comment: "Negative values"}``` |
$text |  | Desteklenmiyor. Bunun yerine $regex kullanın.

## <a name="unsupported-operators"></a>Desteklenmeyen işleçler

```$where``` ve ```$eval``` işleçleri, Azure Cosmos DB tarafından desteklenmiyor.

### <a name="methods"></a>Yöntemler

Aşağıdaki yöntemler desteklenir:

#### <a name="cursor-methods"></a>İmleç yöntemleri

Yöntem | Örnek | Notlar
--- | --- | --- |
cursor.sort() | ```cursor.sort({ "Elevation": -1 })``` | Sıralama anahtarı olmayan belgeler döndürülmez

## <a name="unique-indexes"></a>Benzersiz dizinler

Cosmos DB, varsayılan olarak veritabanına yazılan belgelerdeki her alanı dizinler. Benzersiz dizinler, varsayılan `_id` anahtarda benzersizliğin korunmasına benzer şekilde, belirli bir alanın koleksiyondaki tüm belgelerde yinelenen değerlere sahip olmamasını sağlar. 'Benzersiz' kısıtlama da dahil olmak üzere createIndex komutunu kullanarak Cosmos DB'de özel dizinler oluşturabilirsiniz.

Azure Cosmos DB'nin MongoDB için API'si kullanılarak tüm Cosmos hesapları için benzersiz dizinler kullanılabilir.

## <a name="time-to-live-ttl"></a>Etkin kalma süresi (TTL)

Cosmos DB, belgenin zaman damgasına dayalı bir zaman-to-live (TTL) destekler. Azure [portalına](https://portal.azure.com)giderek koleksiyonlar için TTL etkinleştirilebilir.

## <a name="user-and-role-management"></a>Kullanıcı ve rol yönetimi

Cosmos DB henüz kullanıcıları ve rolleri desteklemez. Ancak Cosmos DB, Rol Tabanlı erişim denetimini (RBAC) ve [Azure portalı](https://portal.azure.com) (Bağlantı String sayfası) üzerinden elde edilebilen salt okuma ve salt okunur parolaları/anahtarları destekler.

## <a name="replication"></a>Çoğaltma

Cosmos DB, en düşük katmanlarda otomatik, yerel çoğaltmayı destekler. Bu mantık, düşük gecikme süresi ve küresel çoğaltma elde etmek için genişletilir. Cosmos DB el ile çoğaltma komutlarını desteklemez.

## <a name="write-concern"></a>Yazma Sorunu

Bazı uygulamalar, yazma işlemi sırasında gereken yanıt sayısını belirten bir [Yazma Endişesi'ne](https://docs.mongodb.com/manual/reference/write-concern/) dayanır. Cosmos DB’nin arka planda çoğaltma işleme şekli nedeniyle varsayılan olarak tüm yazma işlemleri otomatik olarak çekirdektir. İstemci kodu tarafından belirtilen herhangi bir yazma endişesi yoksayılır. Daha fazla bilgi için bkz. [Kullanılabilirlik ve performansı en üst düzeye çıkarmak için tutarlılık düzeylerini kullanma](consistency-levels.md).

## <a name="sharding"></a>Parçalama

Azure Cosmos DB, otomatik, sunucu tarafı parçalamasını destekler. Parça oluşturmayı, yerleştirmeyi ve dengelemeyi otomatik olarak yönetir. Azure Cosmos DB el ile parçalama komutlarını desteklemez, bu da shardCollection, addShard, balancerStart, moveChunk gibi komutları çağırmak zorunda olmadığınız anlamına gelir. Kapsayıcıları oluştururken veya verileri sorgularken yalnızca parça anahtarını belirtmeniz gerekir.

## <a name="next-steps"></a>Sonraki adımlar

- Azure Cosmos DB'nin MongoDB için API'si ile [Studio 3T'yi](mongodb-mongochef.md) nasıl kullanacağınızı öğrenin.
- MongoDB için Azure Cosmos DB'nin API'si ile [Robo 3T'yi](mongodb-robomongo.md) nasıl kullanacağınızı öğrenin.
- Azure Cosmos DB'nin MongoDB için API'si ile MongoDB [örneklerini](mongodb-samples.md) keşfedin.

<sup>Not: Bu makalede, Azure Cosmos DB'nin MongoDB veritabanlarıyla tel protokolü uyumluluğu sağlayan bir özelliği açıklanmaktadır. Microsoft, bu hizmeti sağlamak için MongoDB veritabanlarını çalıştırmaz. Azure Cosmos DB, MongoDB, Inc. şirketine bağlı değildir.</sup>
