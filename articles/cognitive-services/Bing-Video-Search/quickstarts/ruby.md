---
title: 'Quickstart: REST API ve Ruby kullanarak video ara - Bing Video Arama'
titleSuffix: Azure Cognitive Services
description: Ruby'yi kullanarak Bing Video Search REST API'sine video arama istekleri göndermek için bu hızlı başlangıcı kullanın.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-video-search
ms.topic: quickstart
ms.date: 12/09/2019
ms.author: aahi
ms.openlocfilehash: 8f6022f03d28362e85fba3fd75e60c4d7032b41b
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75448375"
---
# <a name="quickstart-search-for-videos-using-the-bing-video-search-rest-api-and-ruby"></a>Quickstart: Bing Video Search REST API ve Ruby kullanarak video ara

Bing Video Arama API'sine ilk aramanızı yapmak ve JSON yanıtından bir arama sonucunu görüntülemek için bu hızlı başlangıcı kullanın. Bu basit Ruby uygulaması API'ye bir HTTP video arama sorgusu gönderir ve yanıtı görüntüler. Bu uygulama Python ile yazılmış olmakla birlikte API, çoğu programlama diliyle uyumlu bir RESTful Web hizmetidir. Bu örneğin kaynak kodu, ek hata işleme ve kod açıklama notları ile [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/ruby/Search/BingVideoSearchv7.rb)’da bulunabilir.

## <a name="prerequisites"></a>Ön koşullar

* Ruby 2.4 ya da daha yeni sürümü

[!INCLUDE [cognitive-services-bing-video-search-signup-requirements](../../../../includes/cognitive-services-bing-video-search-signup-requirements.md)]

## <a name="create-and-initialize-the-application"></a>Uygulamayı oluşturma ve başlatma

1. Aşağıdaki paketleri kod dosyanıza içeri aktarın.

    ```ruby
    require 'net/https'
    require 'uri'
    require 'json'
    ```

2. API bitiş noktası, video API arama yolu, abonelik anahtarınız ve arama teriminiz için değişkenler oluşturun. `uri`aşağıdaki genel bitiş noktası veya kaynağınız için Azure portalında görüntülenen [özel alt etki alanı](../../../cognitive-services/cognitive-services-custom-subdomains.md) bitiş noktası olabilir.

    ```ruby
    uri  = "https://api.cognitive.microsoft.com"
    path = "/bing/v7.0/videos/search"
    term = "kittens"
    accessKey = "your-subscription-key" 
    ```

## <a name="create-and-send-an-api-request"></a>API isteği oluşturma ve gönderme

1. İstek için bir arama URL'sini biçimlendirmek için son adımdaki değişkenleri kullanın. Uri ve yolunuzu birleştirin ve `?q=` parametreye eklemeden önce arama teriminizi url kodlayın.

    ```ruby
    uri = URI(uri + path + "?q=" + URI.escape(term))
    ```

2. İsteğe tam arama URL'sini ekleyin ve abonelik `Ocp-Apim-Subscription-Key` anahtarınızı üstbilgiye ekleyin.
    
    ``` ruby
    request = Net::HTTP::Get.new(uri)
    request['Ocp-Apim-Subscription-Key'] = accessKey
    ```

3. İsteği gönderin ve yanıtı kaydedin.
    
    ```ruby
    response = Net::HTTP.start(uri.host, uri.port, :use_ssl => uri.scheme == 'https') do |http|
        http.request(request)
    end
    ```

## <a name="process-and-view-the-response"></a>Yanıtı işleme ve görüntüleme

1. Yanıt alındıktan sonra JSON yanıtını yazdırabilirsiniz.

    ```ruby
    puts JSON::pretty_generate(JSON(response.body))
    ```

## <a name="json-response"></a>JSON yanıtı

Başarılı yanıt, aşağıdaki örnekte gösterildiği gibi JSON biçiminde döndürülür:

```json
{
    "_type": "Videos",
    "instrumentation": {},
    "readLink": "https://api.cognitive.microsoft.com/api/v7/videos/search?q=kittens",
    "webSearchUrl": "https://www.bing.com/videos/search?q=kittens",
    "totalEstimatedMatches": 1000,
    "value": [
        {
            "webSearchUrl": "https://www.bing.com/videos/search?q=kittens&view=...",
            "name": "Top 10 cute kitten videos compilation",
            "description": "HELP HOMELESS ANIMALS AND WIN A PRIZE BY CHOOSING...",
            "thumbnailUrl": "https://tse4.mm.bing.net/th?id=OVP.n1aE_Oikl4MtzBb...",
            "datePublished": "2014-11-12T22:47:36.0000000",
            "publisher": [
                {
                    "name": "Fabrikam"
                }
            ],
            "creator": {
                "name": "Marcus Appel"
            },
            "isAccessibleForFree": true,
            "contentUrl": "https://www.fabrikam.com/watch?v=8HVWitAW-Qg",
            "hostPageUrl": "https://www.fabrikam.com/watch?v=8HVWitAW-Qg",
            "encodingFormat": "h264",
            "hostPageDisplayUrl": "https://www.fabrikam.com/watch?v=8HVWitAW-Qg",
            "width": 480,
            "height": 360,
            "duration": "PT3M52S",
            "motionThumbnailUrl": "https://tse4.mm.bing.net/th?id=OM.j4QyJAENJphdZQ_1501386166&pid=Api",
            "embedHtml": "<iframe width=\"1280\" height=\"720\" src=\"https://www.fabrikam.com/embed/8HVWitAW-Qg?autoplay=1\" frameborder=\"0\" allowfullscreen></iframe>",
            "allowHttpsEmbed": true,
            "viewCount": 7513633,
            "thumbnail": {
                "width": 300,
                "height": 168
            },
            "videoId": "655D98260D012432848F6558260D012432848F",
            "allowMobileEmbed": true,
            "isSuperfresh": false
        },
        . . .
    ],
    "nextOffset": 36,
    "queryExpansions": [
        {
            "text": "Kittens Meowing",
            "displayText": "Meowing",
            "webSearchUrl": "https://www.bing.com/videos/search?q=Kittens+Meowing...",
            "searchLink": "https://api.cognitive.microsoft.com/api/v7/videos/search...",
            "thumbnail": {
                "thumbnailUrl": "https://tse3.mm.bing.net/th?q=Kittens+Meowing&pid..."
            }
        },
        {
            "text": "Funny Kittens",
            "displayText": "Funny",
            "webSearchUrl": "https://www.bing.com/videos/search?q=Funny+Kittens...",
            "searchLink": "https://api.cognitive.microsoft.com/api/v7/videos/search...",
            "thumbnail": {
                "thumbnailUrl": "https://tse3.mm.bing.net/th?q=Funny+Kittens&..."
            }
        },
        . . .
    ],
    "pivotSuggestions": [
        {
            "pivot": "kittens",
            "suggestions": [
                {
                    "text": "Cat",
                    "displayText": "Cat",
                    "webSearchUrl": "https://www.bing.com/videos/search?q=Cat...",
                    "searchLink": "https://api.cognitive.microsoft.com/api/v7/videos/search?...",
                    "thumbnail": {
                        "thumbnailUrl": "https://tse3.mm.bing.net/th?q=Cat&pid=Api..."
                    }
                },
                {
                    "text": "Feral Cat",
                    "displayText": "Feral Cat",
                    "webSearchUrl": "https://www.bing.com/videos/search?q=Feral+Cat...",
                    "searchLink": "https://api.cognitive.microsoft.com/api/v7/videos/search...",
                    "thumbnail": {
                        "thumbnailUrl": "https://tse3.mm.bing.net/th?q=Feral+Cat&pid=Api&..."
                    }
                }
            ]
        }
    ],
    "relatedSearches": [
        {
            "text": "Kittens Being Born",
            "displayText": "Kittens Being Born",
            "webSearchUrl": "https://www.bing.com/videos/search?q=Kittens+Being+Born...",
            "searchLink": "https://api.cognitive.microsoft.com/api/v7/videos/search?...",
            "thumbnail": {
                "thumbnailUrl": "https://tse1.mm.bing.net/th?q=Kittens+Being+Born&pid=..."
            }
        },
        . . .
    ]
}
```


## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [Tek bir sayfaweb uygulaması oluşturma](../tutorial-bing-video-search-single-page-app.md)

## <a name="see-also"></a>Ayrıca bkz. 

 [Bing Video Arama API'si nedir?](../overview.md)

