---
title: V3 API'deki tahmin uç noktası değişiklikleri
description: Sorgu tahmini bitiş noktası V3 API'leri değişti. Sürüm 3 uç nokta API'lerine nasıl geçirilir anlamak için bu kılavuzu kullanın.
ms.topic: conceptual
ms.date: 03/11/2020
ms.author: diberry
ms.openlocfilehash: 9a8e8cb331dd11eebaddbcbf8f603c1148415aef
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79117377"
---
# <a name="prediction-endpoint-changes-for-v3"></a>V3 için tahmin uç noktası değişiklikleri

Sorgu tahmini bitiş noktası V3 API'leri değişti. Sürüm 3 uç nokta API'lerine nasıl geçirilir anlamak için bu kılavuzu kullanın.

[!INCLUDE [Waiting for LUIS portal refresh](./includes/wait-v3-upgrade.md)]

**Genel olarak kullanılabilir durum** - Bu V3 API V2 API önemli JSON istek ve yanıt değişiklikleri içerir.

V3 API aşağıdaki yeni özellikleri sağlar:

* [Dış varlıklar](#external-entities-passed-in-at-prediction-time)
* [Dinamik listeler](#dynamic-lists-passed-in-at-prediction-time)
* [Önceden oluşturulmuş varlık JSON değişiklikleri](#prebuilt-entity-changes)

Tahmin bitiş noktası [isteği](#request-changes) ve [yanıtı,](#response-changes) aşağıda belirtilen yeni özellikleri desteklemek için önemli değişiklikler ekidir:

* [Yanıt nesnesi değişiklikleri](#top-level-json-changes)
* [Varlık adı yerine varlık rol adı başvuruları](#entity-role-name-instead-of-entity-name)
* [Varlıkları deyişle işaretlemek için özellikler](#marking-placement-of-entities-in-utterances)

[V3](https://aka.ms/luis-api-v3) için referans belgeleri mevcuttur.

## <a name="v3-changes-from-preview-to-ga"></a>V3 önizlemeden GA'ya değişir

V3 GA için hareket in bir parçası olarak aşağıdaki değişiklikleri yaptı:

* Aşağıdaki önceden oluşturulmuş varlıklar farklı JSON yanıtları var:
    * [OrdinalV1](luis-reference-prebuilt-ordinal.md)
    * [CoğrafyaV2](luis-reference-prebuilt-geographyv2.md)
    * [DatetimeV2](luis-reference-prebuilt-datetimev2.md)
    * Ölçülebilir birim anahtar `units` adı`unit`

* İstek vücut JSON değişiklik:
    * dan `preferExternalEntities``preferExternalEntities`
    * dış `score` varlıklar için isteğe bağlı parametre

* Yanıt gövdesi JSON değişiklikleri:
    * `normalizedQuery`Kaldırıldı

## <a name="suggested-adoption-strategy"></a>Önerilen evlat edinme stratejisi

Bot Framework, Bing Spell Check V7 kullanıyorsanız veya yalnızca LUIS uygulamanızı geçirmek istiyorsanız, V2 bitiş noktasını kullanmaya devam edin.

İstemci uygulamanızın veya tümleştirmelerin (Bot Framework ve Bing Spell Check V7) hiçbirinin etkilenmediğinden ve LUIS uygulamanızın yazımını ve tahmin bitiş noktanızı aynı anda geçirmekonusunda rahatsanız, V3 tahmin bitiş noktasını kullanmaya başlayın. V2 tahmin bitiş noktası hala mevcut olacak ve iyi bir geri dönüş stratejisidir.


## <a name="not-supported"></a>Desteklenmiyor

### <a name="bing-spell-check"></a>Bing Yazım Denetimi

Bu API V3 tahmin bitiş noktasında desteklenmez - yazım düzeltmeleri için V2 API tahmin bitiş noktasını kullanmaya devam edin. V3 API kullanırken yazım düzeltmesi gerekiyorsa, istemci uygulamasının Metni LUIS API'ye göndermeden önce [Bing Yazım Denetimi](https://docs.microsoft.com/azure/cognitive-services/bing-spell-check/overview) API'sini aramasını ve metni doğru yazımla değiştirmesini sağlar.

## <a name="bot-framework-and-azure-bot-service-client-applications"></a>Bot Framework ve Azure Bot Hizmeti istemci uygulamaları

Bot Framework V4.7 serbest bırakılıkadar V2 API tahmin bitiş noktası kullanmaya devam edin.

## <a name="v2-api-deprecation"></a>V2 API Amortismanı

V2 tahmin API V3 önizleme sonra en az 9 ay için amortismana olmaz, Haziran 8, 2020.

## <a name="endpoint-url-changes"></a>Uç nokta URL değişiklikleri

### <a name="changes-by-slot-name-and-version-name"></a>Yuva adına ve sürüm adına göre değişiklikler

V3 bitiş noktası HTTP çağrısının biçimi değişti.

Sürüme göre sorgulama yapmak istiyorsanız, öncelikle [API](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c3b) `"directVersionPublish":true`üzerinden . Yuva adı yerine sürüm kimliğine başvuran bitiş noktasını sorgula.

|TAHMIN API SÜRÜMÜ|Yöntem|URL'si|
|--|--|--|
|V3|GET|https://<b>{REGION}</b>.api.cognitive.microsoft.com/luis/<b>tahmin</b>/<b>v3.0</b>/apps/<b>{APP-ID}</b>/yuvaları/<b>{SLOT-NAME}</b>/predict?query=<b>{QUERY}</b>|
|V3|POST|https://<b>{REGION}</b>.api.cognitive.microsoft.com/luis/<b>tahmin</b>/<b>v3.0</b>/apps/<b>{APP-ID}</b>/yuvaları/<b>{SLOT-NAME}</b>/predict|
|V2|GET|https://<b>{REGION}</b>.api.cognitive.microsoft.com/luis/<b>tahmin</b>/<b>v3.0</b>/apps/<b>{APP-ID}</b>/versions/<b>{VERSION-ID}</b>/predict?query=<b>{QUERY}</b>|
|V2|POST|https://<b>{REGION}</b>.api.cognitive.microsoft.com/luis/<b>tahmin</b>/<b>v3.0</b>/apps/<b>{APP-ID}</b>/versions/<b>{VERSION-ID}</b>/predict|

|Için geçerli değerler`SLOT-NAME`|
|--|
|`production`|
|`staging`|

## <a name="request-changes"></a>Değişiklikleri isteme

### <a name="query-string-changes"></a>Sorgu dize değişiklikleri

V3 API farklı sorgu dize parametreleri vardır.

|Param adı|Tür|Sürüm|Varsayılan|Amaç|
|--|--|--|--|--|
|`log`|boole|V2 & V3|yanlış|Sorguyağı günlük dosyasında saklayın. Varsayılan değer yanlıştır.|
|`query`|string|Yalnızca V3|Varsayılan yok - GET isteğinde gereklidir|**V2' de**, tahmin edilecek söyleyiş `q` parametrededir. <br><br>**V3'te**işlevsellik `query` parametrede geçirilir.|
|`show-all-intents`|boole|Yalnızca V3|yanlış|Tüm niyetleri **tahmin.intents** nesnesinde karşılık gelen puanla döndürün. Nesneler, bir üst `intents` nesnedeki nesne olarak döndürülür. Bu, bir dizide amacı bulmaya gerek kalmadan `prediction.intents.give`programatik erişim sağlar: . V2'de bunlar bir dizi olarak döndürüldü. |
|`verbose`|boole|V2 & V3|yanlış|**V2,** doğru ayarlandığında, tüm öngörülen niyetler iade edildi. Tüm öngörülen niyetlere ihtiyacınız varsa, V3 param kullanın. `show-all-intents`<br><br>**V3'te,** bu parametre yalnızca varlık tahmininin varlık meta veri ayrıntılarını sağlar.  |
|`timezoneOffset`|string|V2|-|DatetimeV2 varlıklarına uygulanan saat dilimi.|
|`datetimeReference`|string|V3|-|DatetimeV2 varlıklarına uygulanan [saat dilimi.](luis-concept-data-alteration.md#change-time-zone-of-prebuilt-datetimev2-entity) V2 `timezoneOffset` değiştirir.|


### <a name="v3-post-body"></a>V3 POST gövdesi

```JSON
{
    "query":"your utterance here",
    "options":{
        "datetimeReference": "2019-05-05T12:00:00",
        "preferExternalEntities": true
    },
    "externalEntities":[],
    "dynamicLists":[]
}
```

|Özellik|Tür|Sürüm|Varsayılan|Amaç|
|--|--|--|--|--|
|`dynamicLists`|array|Yalnızca V3|Gerek yok.|[Dinamik listeler,](#dynamic-lists-passed-in-at-prediction-time) LUIS uygulamasında bulunan mevcut eğitimli ve yayınlanmış bir liste varlığını genişletmenize olanak sağlar.|
|`externalEntities`|array|Yalnızca V3|Gerek yok.|[Dış varlıklar,](#external-entities-passed-in-at-prediction-time) LUIS uygulamanıza çalışma zamanı sırasında varlıkları tanımlama ve etiketleme olanağı verir ve bu da mevcut varlıklar için özellik olarak kullanılabilir. |
|`options.datetimeReference`|string|Yalnızca V3|Varsayılan yok|[DatetimeV2 mahsup](luis-concept-data-alteration.md#change-time-zone-of-prebuilt-datetimev2-entity)belirlemek için kullanılır. DatetimeReference'ın biçimi [ISO 8601'dir.](https://en.wikipedia.org/wiki/ISO_8601)|
|`options.preferExternalEntities`|boole|Yalnızca V3|yanlış|Kullanıcının [dış varlığının (varolan varlıkla aynı ada sahip)](#override-existing-model-predictions) kullanÝlýp kullanýlmayý veya modeldeki varolan varlığın tahmin için kullanýlýp kullanýlmayý belirtir. |
|`query`|string|Yalnızca V3|Gereklidir.|**V2' de**, tahmin edilecek söyleyiş `q` parametrededir. <br><br>**V3'te**işlevsellik `query` parametrede geçirilir.|



## <a name="response-changes"></a>Yanıt değişiklikleri

Sorgu yanıtı JSON en sık kullanılan verilere daha fazla programlı erişim sağlamak için değiştirildi.

### <a name="top-level-json-changes"></a>Üst düzey JSON değişiklikleri



V2 için üst JSON özellikleri, ne zaman `verbose` tüm niyet ve `intents` özellik puanları döndürür doğru ayarlanır:

```JSON
{
    "query":"this is your utterance you want predicted",
    "topScoringIntent":{},
    "intents":[],
    "entities":[],
    "compositeEntities":[]
}
```

V3 için üst JSON özellikleri şunlardır:

```JSON
{
    "query": "this is your utterance you want predicted",
    "prediction":{
        "topIntent": "intent-name-1",
        "intents": {},
        "entities":{}
    }
}
```

Nesne `intents` sıralanmamış bir listedir. İlk çocuğun `intents` karşılık gelen olduğunu `topIntent`düşünmeyin. Bunun yerine, `topIntent` puanı bulmak için değeri kullanın:

```nodejs
const topIntentName = response.prediction.topIntent;
const score = intents[topIntentName];
```

Yanıt JSON şema değişiklikleri için izin:

* Orijinal söyleyiş `query`ve geri dönen tahmin `prediction`arasındaki açık ayrım, .
* Tahmin edilen verilere daha kolay programlı erişim. V2'deki bir dizide sayısallama yerine, hem amaçlar hem de varlıklar için değerlere **ada** göre erişebilirsiniz. Öngörülen varlık rolleri için, tüm uygulamada benzersiz olduğundan rol adı döndürülür.
* Veri türlerine, belirlendiği takdirde, saygı duyulur. Sayısallar artık dizeleri olarak döndürülür.
* `$instance` Nesnede döndürülen ilk öncelik tahmin bilgileri ile ek meta veriler arasındaki ayrım.

### <a name="entity-response-changes"></a>Varlık yanıtı değişiklikleri

#### <a name="marking-placement-of-entities-in-utterances"></a>Varlıkların sözlerle yerleştirilmesinin işaretlenmesi

**V2 yılında**, bir varlık `startIndex` ve `endIndex`ile bir söyleyiş işaretlenmiştir.

**V3'te**varlık ile `startIndex` işaretlenir ve `entityLength`.

#### <a name="access-instance-for-entity-metadata"></a>Varlık `$instance` meta verileri için erişim

Varlık meta verilerine ihtiyacınız varsa, sorgu `verbose=true` dizesinin bayrağı kullanması gerekir `$instance` ve yanıt nesnedeki meta verileri içerir. Örnekler json yanıtlarında aşağıdaki bölümlerde gösterilmiştir.

#### <a name="each-predicted-entity-is-represented-as-an-array"></a>Her tahmin edilen varlık bir dizi olarak temsil edilir

Nesne `prediction.entities.<entity-name>` bir dizi içerir, çünkü her varlık söyleyişde birden fazla kez tahmin edilebilir.

<a name="prebuilt-entities-with-new-json"></a>

#### <a name="prebuilt-entity-changes"></a>Önceden oluşturulmuş varlık değişiklikleri

V3 yanıt nesnesi önceden oluşturulmuş varlıklarda değişiklikler içerir. Daha fazla bilgi edinmek için önceden oluşturulmuş varlıkları gözden [geçirin.](luis-reference-prebuilt-entities.md)

#### <a name="list-entity-prediction-changes"></a>Varlık tahmini değişikliklerini listele

Bir liste varlık tahmini için JSON dizileri bir dizi olarak değişti:

```JSON
"entities":{
    "my_list_entity":[
        ["canonical-form-1","canonical-form-2"],
        ["canonical-form-2"]
    ]
}
```
Her iç dizi, söyleyiş içindeki metne karşılık gelir. Aynı metin bir liste varlığının birden fazla alt listesinde görünebileceğinden, iç nesne bir dizidir.

`entities` Nesne ile `$instance` nesne arasında eşleme yapılırken, nesne sırası liste varlık tahminleri için korunur.

```nodejs
const item = 0; // order preserved, use same enumeration for both
const predictedCanonicalForm = entities.my_list_entity[item];
const associatedMetadata = entities.$instance.my_list_entity[item];
```

#### <a name="entity-role-name-instead-of-entity-name"></a>Varlık adı yerine varlık rol adı

V2'de `entities` dizi, varlık adı benzersiz tanımlayıcı olan tüm öngörülen varlıkları döndürdü. V3'te, varlık rolleri kullanıyorsa ve tahmin bir varlık rolü içinse, birincil tanımlayıcı rol adıdır. Varlık rol adları diğer model (niyet, varlık) adları da dahil olmak üzere tüm uygulama arasında benzersiz olması gerekir, çünkü bu mümkündür.

Aşağıdaki örnekte: metni içeren bir söyleyiş `Yellow Bird Lane`düşünün, . Bu metin, özel `Location` bir varlığın rolü `Destination`olarak tahmin edilir.

|Söyleyiş metni|Varlık adı|Rol adı|
|--|--|--|
|`Yellow Bird Lane`|`Location`|`Destination`|

V2'de varlık, nesnenin özelliği olarak rol alan _varlık adı_ ile tanımlanır:

```JSON
"entities":[
    {
        "entity": "Yellow Bird Lane",
        "type": "Location",
        "startIndex": 13,
        "endIndex": 20,
        "score": 0.786378264,
        "role": "Destination"
    }
]
```

V3'te, tahmin rol içinise varlık _rolüne_başvurulmuştur:

```JSON
"entities":{
    "Destination":[
        "Yellow Bird Lane"
    ]
}
```

V3'te, varlık meta `verbose` verilerini döndürmek için bayrakla aynı sonuç:

```JSON
"entities":{
    "Destination":[
        "Yellow Bird Lane"
    ],
    "$instance":{
        "Destination": [
            {
                "role": "Destination",
                "type": "Location",
                "text": "Yellow Bird Lane",
                "startIndex": 25,
                "length":16,
                "score": 0.9837309,
                "modelTypeId": 1,
                "modelType": "Entity Extractor"
            }
        ]
    }
}
```

## <a name="external-entities-passed-in-at-prediction-time"></a>Tahmin zamanında geçirilen dış varlıklar

Dış varlıklar, LUIS uygulamanıza çalışma zamanı sırasında varlıkları tanımlama ve etiketleme olanağı verir ve bu da mevcut varlıklar için özellik olarak kullanılabilir. Bu, tahmin bitiş noktanıza sorgu göndermeden önce kendi ayrı ve özel varlık çıkarıcılarınızı kullanmanıza olanak tanır. Bu sorgu tahmin bitiş noktasında yapıldığından, modelinizi yeniden e-yılve yayımlamanız gerekmez.

İstemci uygulaması, varlık eşleştirmesini yöneterek ve eşleşen varlığın söyleyerek konumunu belirleyerek ve bu bilgileri istekle birlikte göndererek kendi varlık çıkarıcısını sağlar.

Dış varlıklar, roller, bileşik ve diğerleri gibi diğer modellere sinyal olarak kullanılırken herhangi bir varlık türünü genişletme mekanizmasıdır.

Bu, yalnızca sorgu tahmini çalışma zamanında kullanılabilir veri olan bir varlık için yararlıdır. Bu tür verilere örnek olarak, kullanıcı başına sürekli veri veya belirli veriler değişir. Bir LUIS iletişim kuruluşunu, kullanıcının kişi listesindeki dış bilgilerle genişletebilirsiniz.

### <a name="entity-already-exists-in-app"></a>Varlık zaten uygulamada var

Dış varlık `entityName` için değeri, bitiş noktası istek POST gövdesi geçti, zaten istek yapıldığı anda eğitimli ve yayınlanan uygulamada mevcut olmalıdır. Varlık türü önemli değil, her türlü desteklenir.

### <a name="first-turn-in-conversation"></a>Konuşmada ilk dönüş

Bir kullanıcı aşağıdaki eksik bilgileri girer bir sohbet bot konuşma ilk söyleyerek düşünün:

`Send Hazem a new message`

Sohbet robotundan LUIS'e gelen istek, POST `Hazem` gövdesindeki bilgileri aktarabilir, böylece kullanıcının kişilerinden biri olarak doğrudan eşleşir.

```json
    "externalEntities": [
        {
            "entityName":"contacts",
            "startIndex": 5,
            "entityLength": 5,
            "resolution": {
                "employeeID": "05013",
                "preferredContactType": "TeamsChat"
            }
        }
    ]
```

Tahmin yanıtı, istekte tanımlandığı için, diğer tüm öngörülen varlıklarla birlikte dış varlığı içerir.

### <a name="second-turn-in-conversation"></a>Konuşmada ikinci dönüş

Sohbet bot içine bir sonraki kullanıcı söyleyerek daha belirsiz bir terim kullanır:

`Send him a calendar reminder for the party.`

Önceki söyleyiş, söyleyiş bir `him` referans olarak `Hazem`kullanır . Konuşma sohbet bot, POST gövdesinde, `him` varlık değeri ilk söyleyiş çıkarılan harita `Hazem`olabilir.

```json
    "externalEntities": [
        {
            "entityName":"contacts",
            "startIndex": 5,
            "entityLength": 3,
            "resolution": {
                "employeeID": "05013",
                "preferredContactType": "TeamsChat"
            }
        }
    ]
```

Tahmin yanıtı, istekte tanımlandığı için, diğer tüm öngörülen varlıklarla birlikte dış varlığı içerir.

### <a name="override-existing-model-predictions"></a>Varolan model tahminlerini geçersiz kılma

Seçenekler `preferExternalEntities` özelliği, kullanıcı nın aynı ada sahip tahmin edilen bir varlıkla çakışan bir dış varlık gönderirse, LUIS'in geçen varlığı veya modelde var olan varlığı seçtiğini belirtir.

Örneğin, sorguyu `today I'm free`göz önünde bulundurun. LUIS aşağıdaki `today` yanıt ile bir datetimeV2 olarak algılar:

```JSON
"datetimeV2": [
    {
        "type": "date",
        "values": [
            {
                "timex": "2019-06-21",
                "value": "2019-06-21"
            }
        ]
    }
]
```

Kullanıcı dış varlığı gönderirse:

```JSON
{
    "entityName": "datetimeV2",
    "startIndex": 0,
    "entityLength": 5,
    "resolution": {
        "date": "2019-06-21"
    }
}
```

`preferExternalEntities` Ayarlanan `false`, LUIS dış varlık gönderilmemiş gibi bir yanıt döndürür.

```JSON
"datetimeV2": [
    {
        "type": "date",
        "values": [
            {
                "timex": "2019-06-21",
                "value": "2019-06-21"
            }
        ]
    }
]
```

`true`Ayarlanmışsa, `preferExternalEntities` LUIS aşağıdakiler dahil olmak üzere bir yanıt verir:

```JSON
"datetimeV2": [
    {
        "date": "2019-06-21"
    }
]
```



#### <a name="resolution"></a>Çözüm

İsteğe _bağlı_ `resolution` özellik, dış varlıkla ilişkili meta verileri geçirmenize ve ardından yanıtta geri almanıza olanak tanıyan tahmin yanıtında geri döner.

Birincil amaç, önceden oluşturulmuş varlıkları genişletmektir, ancak bu varlık türüyle sınırlı değildir.

Özellik `resolution` bir sayı, bir dize, nesne veya bir dizi olabilir:

* "Dallas"
* {"text": "değer"}
* 12345
* ["a", "b", "c"]



## <a name="dynamic-lists-passed-in-at-prediction-time"></a>Tahmin zamanında geçirilen dinamik listeler

Dinamik listeler, LUIS uygulamasında bulunan mevcut eğitimli ve yayınlanmış bir liste varlığını genişletmenize olanak sağlar.

Liste varlık değerlerinizin düzenli aralıklarla değişmesi gerektiğinde bu özelliği kullanın. Bu özellik, önceden eğitilmiş ve yayınlanmış bir liste varlığını genişletmenize olanak tanır:

* Sorgu tahmin bitiş noktası isteği sırasında.
* Tek bir istek için.

Liste varlığı LUIS uygulamasında boş olabilir, ancak var olması gerekir. LUIS uygulamasındaki liste varlığı değiştirilmez, ancak bitiş noktasındaki tahmin yeteneği yaklaşık 1.000 öğeiçeren en fazla 2 liste içerecek şekilde genişletilir.

### <a name="dynamic-list-json-request-body"></a>Dinamik liste JSON istek gövdesi

Listeye eşanlamlılarla birlikte yeni bir alt liste eklemek için aşağıdaki JSON gövdesini gönderin ve `LUIS`sorgu `POST` tahmini isteğiyle metin için liste varlığını tahmin edin:

```JSON
{
    "query": "Send Hazem a message to add an item to the meeting agenda about LUIS.",
    "options":{
        "timezoneOffset": "-8:00"
    },
    "dynamicLists": [
        {
            "listEntity*":"ProductList",
            "requestLists":[
                {
                    "name": "Azure Cognitive Services",
                    "canonicalForm": "Azure-Cognitive-Services",
                    "synonyms":[
                        "language understanding",
                        "luis",
                        "qna maker"
                    ]
                }
            ]
        }
    ]
}
```

Tahmin yanıtı, istekte tanımlandığı için, diğer tüm öngörülen varlıklarla birlikte bu liste varlığını içerir.

## <a name="deprecation"></a>Kullanımdan kaldırma

V2 API, V3 önizlemeden sonra en az 9 ay boyunca amortismana alınmayacaktır.

## <a name="next-steps"></a>Sonraki adımlar

Varolan REST çağrılarını LUIS [uç noktası](https://aka.ms/luis-api-v3) API'lerine güncelleştirmek için V3 API belgelerini kullanın.
