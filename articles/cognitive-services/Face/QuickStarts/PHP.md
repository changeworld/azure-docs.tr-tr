---
title: 'Hızlı Başlangıç: REST API ve PHP kullanarak bir görüntüdeki yüzleri algılama'
titleSuffix: Azure Cognitive Services
description: Bu hızlı başlangıçta, PHP ile Face REST API'sini kullanarak görüntüdeki yüzleri algılarsınız.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: quickstart
ms.date: 12/05/2019
ms.author: pafarley
ms.openlocfilehash: 0f76ec4ce1072b247be7f46610d37190cd282a22
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76169750"
---
# <a name="quickstart-detect-faces-in-an-image-using-the-rest-api-and-php"></a>Hızlı Başlangıç: REST API ve PHP kullanarak bir görüntüdeki yüzleri algılama

Bu hızlı başlangıçta, görüntüdeki insan yüzlerini algılamak için PHP içeren Azure Yüz REST API'sini kullanırsınız.

## <a name="prerequisites"></a>Ön koşullar

- Yüz abonelik anahtarı. [Bilişsel Hizmetleri Deneyin](https://azure.microsoft.com/try/cognitive-services/?api=face-api)ücretsiz deneme abonelik anahtarı alabilirsiniz. Veya Face hizmetine abone olmak ve anahtarınızı almak için [Bilişsel Hizmetler Oluştur hesabındaki](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) yönergeleri izleyin.
- [Visual Studio Code](https://code.visualstudio.com/download)gibi bir kod düzenleyicisi.
- PHP [HTTP_Request2](https://pear.php.net/package/HTTP_Request2) paketi.
- PHP özellikli bir web tarayıcısı. Bunu ayarlamadıysanız, bunu makinenize [XAMPP'yi](https://www.apachefriends.org/) kurup kurarak yapabilirsiniz.

## <a name="initialize-the-html-file"></a>HTML dosyasını başlatma

Yeni bir HTML dosyası oluşturun, *Faces.html'i algılayın*ve aşağıdaki kodu ekleyin.

```html
<html>
    <head>
        <title>Face Detect Sample</title>
    </head>
    <body></body>
</html>
```

## <a name="write-the-php-script"></a>PHP komut dosyasını yazma

Belgenin öğesinin `body` içine aşağıdaki kodu ekleyin. Bu kod, URL alanı, **Analiz yüz** düğmesi, yanıt bölmesi ve görüntü görüntüleme bölmesi ile temel bir kullanıcı arabirimi ayarlar.

```php
<?php
// Replace <Subscription Key> with a valid subscription key.
$ocpApimSubscriptionKey = '<Subscription Key>';

// Replace <My Endpoint String> with the string in your endpoint URL.
$uriBase = 'https:/<My Endpoint String>.com/face/v1.0/';

$imageUrl =
    'https://upload.wikimedia.org/wikipedia/commons/3/37/Dagestani_man_and_woman.jpg';

// This sample uses the PHP5 HTTP_Request2 package
// (https://pear.php.net/package/HTTP_Request2).
require_once 'HTTP/Request2.php';

$request = new Http_Request2($uriBase . '/detect');
$url = $request->getUrl();

$headers = array(
    // Request headers
    'Content-Type' => 'application/json',
    'Ocp-Apim-Subscription-Key' => $ocpApimSubscriptionKey
);
$request->setHeader($headers);

$parameters = array(
    // Request parameters
    'returnFaceId' => 'true',
    'returnFaceLandmarks' => 'false',
    'returnFaceAttributes' => 'age,gender,headPose,smile,facialHair,glasses,' .
        'emotion,hair,makeup,occlusion,accessories,blur,exposure,noise');
$url->setQueryVariables($parameters);

$request->setMethod(HTTP_Request2::METHOD_POST);

// Request body parameters
$body = json_encode(array('url' => $imageUrl));

// Request body
$request->setBody($body);

try
{
    $response = $request->send();
    echo "<pre>" .
        json_encode(json_decode($response->getBody()), JSON_PRETTY_PRINT) . "</pre>";
}
catch (HttpException $ex)
{
    echo "<pre>" . $ex . "</pre>";
}
?>
```

`subscriptionKey` Alanı abonelik anahtarınızın değeriyle güncelleştirmeniz ve `uriBase` doğru bitiş noktası dizesini içermesi için dizeyi değiştirmeniz gerekir. Alan, `returnFaceAttributes` alınacak öznitelikleri n için belirtir; amacınıza bağlı olarak bu dizeyi değiştirmek isteyebilirsiniz.

[!INCLUDE [subdomains-note](../../../../includes/cognitive-services-custom-subdomains-note.md)]

## <a name="run-the-script"></a>Betiği çalıştırın

Dosyayı PHP özellikli bir web tarayıcısında açın. Aşağıdaki gibi Bir JSON Face veri dizisi almalısınız.

```json
[
    {
        "faceId": "e93e0db1-036e-4819-b5b6-4f39e0f73509",
        "faceRectangle": {
            "top": 621,
            "left": 616,
            "width": 195,
            "height": 195
        },
        "faceAttributes": {
            "smile": 0,
            "headPose": {
                "pitch": 0,
                "roll": 6.8,
                "yaw": 3.7
            },
            "gender": "male",
            "age": 37,
            "facialHair": {
                "moustache": 0.4,
                "beard": 0.4,
                "sideburns": 0.1
            },
            "glasses": "NoGlasses",
            "emotion": {
                "anger": 0,
                "contempt": 0,
                "disgust": 0,
                "fear": 0,
                "happiness": 0,
                "neutral": 0.999,
                "sadness": 0.001,
                "surprise": 0
            },
            "blur": {
                "blurLevel": "high",
                "value": 0.89
            },
            "exposure": {
                "exposureLevel": "goodExposure",
                "value": 0.51
            },
            "noise": {
                "noiseLevel": "medium",
                "value": 0.59
            },
            "makeup": {
                "eyeMakeup": true,
                "lipMakeup": false
            },
            "accessories": [],
            "occlusion": {
                "foreheadOccluded": false,
                "eyeOccluded": false,
                "mouthOccluded": false
            },
            "hair": {
                "bald": 0.04,
                "invisible": false,
                "hairColor": [
                    {
                        "color": "black",
                        "confidence": 0.98
                    },
                    {
                        "color": "brown",
                        "confidence": 0.87
                    },
                    {
                        "color": "gray",
                        "confidence": 0.85
                    },
                    {
                        "color": "other",
                        "confidence": 0.25
                    },
                    {
                        "color": "blond",
                        "confidence": 0.07
                    },
                    {
                        "color": "red",
                        "confidence": 0.02
                    }
                ]
            }
        }
    },
    {
        "faceId": "37c7c4bc-fda3-4d8d-94e8-b85b8deaf878",
        "faceRectangle": {
            "top": 693,
            "left": 1503,
            "width": 180,
            "height": 180
        },
        "faceAttributes": {
            "smile": 0.003,
            "headPose": {
                "pitch": 0,
                "roll": 2,
                "yaw": -2.2
            },
            "gender": "female",
            "age": 56,
            "facialHair": {
                "moustache": 0,
                "beard": 0,
                "sideburns": 0
            },
            "glasses": "NoGlasses",
            "emotion": {
                "anger": 0,
                "contempt": 0.001,
                "disgust": 0,
                "fear": 0,
                "happiness": 0.003,
                "neutral": 0.984,
                "sadness": 0.011,
                "surprise": 0
            },
            "blur": {
                "blurLevel": "high",
                "value": 0.83
            },
            "exposure": {
                "exposureLevel": "goodExposure",
                "value": 0.41
            },
            "noise": {
                "noiseLevel": "high",
                "value": 0.76
            },
            "makeup": {
                "eyeMakeup": false,
                "lipMakeup": false
            },
            "accessories": [],
            "occlusion": {
                "foreheadOccluded": false,
                "eyeOccluded": false,
                "mouthOccluded": false
            },
            "hair": {
                "bald": 0.06,
                "invisible": false,
                "hairColor": [
                    {
                        "color": "black",
                        "confidence": 0.99
                    },
                    {
                        "color": "gray",
                        "confidence": 0.89
                    },
                    {
                        "color": "other",
                        "confidence": 0.64
                    },
                    {
                        "color": "brown",
                        "confidence": 0.34
                    },
                    {
                        "color": "blond",
                        "confidence": 0.07
                    },
                    {
                        "color": "red",
                        "confidence": 0.03
                    }
                ]
            }
        }
    }
]
```

## <a name="next-steps"></a>Sonraki adımlar

Görüntüdeki insan yüzlerini algılamak, yüzleri dikdörtgenlerle ayırmak ve yaş ve cinsiyet gibi öznitelikleri döndürmek için kullanılan Yüz API'sini keşfedin.

> [!div class="nextstepaction"]
> [Yüz Tanıma API’leri](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236)
