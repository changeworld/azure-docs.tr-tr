---
title: 'Quickstart: Yazdırılan metni ayıklayın- REST, JavaScript'
titleSuffix: Azure Cognitive Services
description: Bu hızlı başlangıçta, JavaScript ile Görüntü İşleme API’si kullanarak bir görüntüden yazdırılan metni ayıklayacaksınız.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: quickstart
ms.date: 03/26/2020
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: 63680178c42854d9cb3b4b42d004da471f839c97
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/03/2020
ms.locfileid: "80656077"
---
# <a name="quickstart-extract-printed-text-ocr-using-the-computer-vision-rest-api-and-javascript"></a>Quickstart: Computer Vision REST API ve JavaScript kullanarak yazdırılan metni (OCR) ayıklayın

> [!NOTE]
> İngilizce metin ayıklıyorsanız, yeni Okuma [işlemini](https://docs.microsoft.com/azure/cognitive-services/computer-vision/concept-recognizing-text)kullanmayı düşünün. [Bir JavaScript quickstart](https://docs.microsoft.com/azure/cognitive-services/computer-vision/quickstarts/javascript-hand-text) kullanılabilir.

Bu hızlı başlangıçta, Computer Vision REST API'yi kullanarak bir görüntüden optik karakter tanıma (OCR) içeren basılı metinler ayıklanırsınız. [OCR](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fc) yöntemiyle, bir görüntüdeki yazdırılan metni algılayabilir ve tanınan karakterleri makine tarafından kullanılabilir bir karakter akışı halinde ayıklayabilirsiniz.

Azure aboneliğiniz yoksa, başlamadan önce [ücretsiz](https://azure.microsoft.com/free/ai/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=cognitive-services) bir hesap oluşturun.

## <a name="prerequisites"></a>Ön koşullar

Görüntü İşleme için bir abonelik anahtarınız olması gerekir. [Bilişsel Hizmetleri Deneyin](https://azure.microsoft.com/try/cognitive-services/?api=computer-vision)ücretsiz bir deneme anahtarı alabilirsiniz. Veya Computer Vision'a abone olmak ve anahtarınızı almak için [Bilişsel Hizmetler Oluştur hesabındaki](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) yönergeleri izleyin. Abonelik anahtarınızı ve bitiş noktası URL'nizi geçici bir konuma kaydedin.

## <a name="create-and-run-the-sample"></a>Örnek oluşturma ve çalıştırma

Örneği oluşturup çalıştırmak için aşağıdaki adımları uygulayın:

1. _Get-printed-text.html_adlı bir dosya oluşturun, metin düzenleyicisinde açın ve aşağıdaki kodu kopyalayın.
1. İsteğe bağlı olarak `inputImage` denetimi için `value` özniteliğinin değerini, analiz etmek istediğiniz başka bir görüntünün URL’si ile değiştirin.
1. Tarayıcı penceresini açın.
1. Tarayıcıda dosyayı tarayıcı penceresine sürükleyip bırakın.
1. Web sayfası tarayıcıda görüntülendiğinde, abonelik anahtarınızı ve bitiş noktası URL'nizi uygun giriş kutularına yapıştırın.
1. Görüntü **Oku** düğmesini seçin.

```html
<!DOCTYPE html>
<html>
<head>
    <title>OCR Sample</title>
    <script src="https://ajax.googleapis.com/ajax/libs/jquery/1.9.0/jquery.min.js"></script>
</head>
<body>

<script type="text/javascript">
    function processImage() {
        // **********************************************
        // *** Update or verify the following values. ***
        // **********************************************

        var subscriptionKey = document.getElementById("subscriptionKey").value;
        var endpoint = document.getElementById("endpointUrl").value;
        
        var uriBase = endpoint + "vision/v2.1/ocr";

        // Request parameters.
        var params = {
            "language": "unk",
            "detectOrientation": "true",
        };

        // Display the image.
        var sourceImageUrl = document.getElementById("inputImage").value;
        document.querySelector("#sourceImage").src = sourceImageUrl;

        // Perform the REST API call.
        $.ajax({
            url: uriBase + "?" + $.param(params),

            // Request headers.
            beforeSend: function(jqXHR){
                jqXHR.setRequestHeader("Content-Type","application/json");
                jqXHR.setRequestHeader("Ocp-Apim-Subscription-Key", subscriptionKey);
            },

            type: "POST",

            // Request body.
            data: '{"url": ' + '"' + sourceImageUrl + '"}',
        })

        .done(function(data) {
            // Show formatted JSON on webpage.
            $("#responseTextArea").val(JSON.stringify(data, null, 2));
        })

        .fail(function(jqXHR, textStatus, errorThrown) {
            // Display error message.
            var errorString = (errorThrown === "") ?
                "Error. " : errorThrown + " (" + jqXHR.status + "): ";
            errorString += (jqXHR.responseText === "") ? "" :
                (jQuery.parseJSON(jqXHR.responseText).message) ?
                    jQuery.parseJSON(jqXHR.responseText).message :
                    jQuery.parseJSON(jqXHR.responseText).error.message;
            alert(errorString);
        });
    };
</script>

<h1>Optical Character Recognition (OCR):</h1>
Enter the URL to an image of printed text, then
click the <strong>Read image</strong> button.
<br><br>
Subscription key: 
<input type="text" name="subscriptionKey" id="subscriptionKey"
    value="" /> 
Endpoint URL:
<input type="text" name="endpointUrl" id="endpointUrl"
    value="" />
<br><br>
Image to read:
<input type="text" name="inputImage" id="inputImage" 
    value="https://upload.wikimedia.org/wikipedia/commons/thumb/a/af/Atomist_quote_from_Democritus.png/338px-Atomist_quote_from_Democritus.png" />
<button onclick="processImage()">Read image</button>
<br><br>
<div id="wrapper" style="width:1020px; display:table;">
    <div id="jsonOutput" style="width:600px; display:table-cell;">
        Response:
        <br><br>
        <textarea id="responseTextArea" class="UIInput"
                  style="width:580px; height:400px;"></textarea>
    </div>
    <div id="imageDiv" style="width:420px; display:table-cell;">
        Source image:
        <br><br>
        <img id="sourceImage" width="400" />
    </div>
</div>
</body>
</html>
```

## <a name="examine-the-response"></a>Yanıtı inceleme

Başarılı bir yanıt JSON biçiminde döndürülür. Örnek web sayfası aşağıdaki örneğe benzer şekilde başarılı bir yanıtı ayrıştırıp tarayıcı penceresinde görüntüler:

```json
{
  "language": "en",
  "orientation": "Up",
  "textAngle": 0,
  "regions": [
    {
      "boundingBox": "21,16,304,451",
      "lines": [
        {
          "boundingBox": "28,16,288,41",
          "words": [
            {
              "boundingBox": "28,16,288,41",
              "text": "NOTHING"
            }
          ]
        },
        {
          "boundingBox": "27,66,283,52",
          "words": [
            {
              "boundingBox": "27,66,283,52",
              "text": "EXISTS"
            }
          ]
        },
        {
          "boundingBox": "27,128,292,49",
          "words": [
            {
              "boundingBox": "27,128,292,49",
              "text": "EXCEPT"
            }
          ]
        },
        {
          "boundingBox": "24,188,292,54",
          "words": [
            {
              "boundingBox": "24,188,292,54",
              "text": "ATOMS"
            }
          ]
        },
        {
          "boundingBox": "22,253,297,32",
          "words": [
            {
              "boundingBox": "22,253,105,32",
              "text": "AND"
            },
            {
              "boundingBox": "144,253,175,32",
              "text": "EMPTY"
            }
          ]
        },
        {
          "boundingBox": "21,298,304,60",
          "words": [
            {
              "boundingBox": "21,298,304,60",
              "text": "SPACE."
            }
          ]
        },
        {
          "boundingBox": "26,387,294,37",
          "words": [
            {
              "boundingBox": "26,387,210,37",
              "text": "Everything"
            },
            {
              "boundingBox": "249,389,71,27",
              "text": "else"
            }
          ]
        },
        {
          "boundingBox": "127,431,198,36",
          "words": [
            {
              "boundingBox": "127,431,31,29",
              "text": "is"
            },
            {
              "boundingBox": "172,431,153,36",
              "text": "opinion."
            }
          ]
        }
      ]
    }
  ]
}
```

## <a name="next-steps"></a>Sonraki adımlar

Optik karakter tanıma (OCR) gerçekleştirmek için Görüntü İşleme kullanan bir JavaScript uygulaması keşfedin. Akıllı kırpılmış küçük resimler oluşturun. Buna ek olarak, bir görüntüdeki yüzler gibi görsel özellikleri algılayın, kategorilere ayırın, etiketleyin ve açıklayın. Görüntü İşleme API'sini hızlı bir şekilde denemeniz için [Open API test konsolu](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa/console) konusuna bakın.

> [!div class="nextstepaction"]
> [Görüntü İşleme API'si JavaScript Öğreticisi](../Tutorials/javascript-tutorial.md)
