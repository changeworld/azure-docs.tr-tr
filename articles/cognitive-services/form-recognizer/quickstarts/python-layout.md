---
title: 'Quickstart: Python kullanarak metin ve düzen bilgilerini ayıklayın - Form Tanıyın'
titleSuffix: Azure Cognitive Services
description: Bu hızlı başlangıçta, formlarınızdaki metin ve tablo verilerini okumak için Python ile Form Recognizer Layout REST API'yi kullanırsınız.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: quickstart
ms.date: 02/19/2020
ms.author: pafarley
ms.openlocfilehash: 342ae7e42c85ad661c04ba4ebb6629673f4af4dc
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77482285"
---
# <a name="quickstart-extract-text-and-layout-information-using-the-form-recognizer-rest-api-with-python"></a>Quickstart: Python ile Form Recognizer REST API kullanarak metin ve düzen bilgilerini ayıklama

Bu hızlı başlangıçta, metin düzeni bilgilerini ve tablo verilerini form belgelerinden ayıklamak için Python ile Azure Form Recognizer REST API'yi kullanırsınız.

Azure aboneliğiniz yoksa, başlamadan önce [ücretsiz](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) bir hesap oluşturun.

## <a name="prerequisites"></a>Ön koşullar

Bu hızlı başlangıcı tamamlamak için şunları yapmış olmalısınız:
- [Python](https://www.python.org/downloads/) yüklendi (örneği yerel olarak çalıştırmak istiyorsanız).
- Form belgesi. Bu hızlı başlangıç için [örnek veri kümesinden](https://go.microsoft.com/fwlink/?linkid=2090451) bir resim indirebilirsiniz.

## <a name="create-a-form-recognizer-resource"></a>Form Tanıyıcı kaynağı oluşturma

[!INCLUDE [create resource](../includes/create-resource.md)]

## <a name="analyze-the-form-layout"></a>Form düzenini analiz edin

Düzeni çözümlemeye başlamak için, aşağıdaki Python komut dosyasını kullanarak **[Çözümdüzen](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-preview/operations/AnalyzeLayoutAsync)** API'sini çağırırsınız. Komut dosyasını çalıştırmadan önce aşağıdaki değişiklikleri yapın:

1. Form `<Endpoint>` Recognizer aboneliğinizle elde ettiğiniz bitiş noktasıyla değiştirin.
1. Yerel `<path to your form>` form belgenize giden yol ile değiştirin.
1. Önceki `<subscription key>` adımdan kopyaladığınız abonelik anahtarıyla değiştirin.

    ```python
    ########### Python Form Recognizer Async Layout #############

    import json
    import time
    from requests import get, post
    
    # Endpoint URL
    endpoint = r"<Endpoint>"
    apim_key = "<Subscription Key>"
    post_url = endpoint + "/formrecognizer/v2.0-preview/Layout/analyze"
    source = r"<path to your form>"
    
    headers = {
        # Request headers
        'Content-Type': '<file type>',
        'Ocp-Apim-Subscription-Key': apim_key,
    }
    with open(source, "rb") as f:
        data_bytes = f.read()
    
    try:
        resp = post(url = post_url, data = data_bytes, headers = headers)
        if resp.status_code != 202:
            print("POST analyze failed:\n%s" % resp.text)
            quit()
        print("POST analyze succeeded:\n%s" % resp.headers)
        get_url = resp.headers["operation-location"]
    except Exception as e:
        print("POST analyze failed:\n%s" % str(e))
        quit()
    ```

1. Kodu .py uzantılı bir dosyaya kaydedin. Örneğin, *form-recognizer-layout.py.*
1. Bir komut istemi penceresi açın.
1. İstemde, örneği çalıştırmak için `python` komutunu kullanın. Örneğin, `python form-recognizer-layout.py`.

Komut dosyasının `202 (Success)` konsola yazdıracağı **İşlem-Konum** üstbilgisini içeren bir yanıt alırsınız. Bu üstbilgi, eşzamanlı işlemin durumunu sorgulamak ve sonuçları almak için kullanabileceğiniz bir işlem kimliği içerir. Aşağıdaki örnek değerde, sonraki `operations/` dize işlem kimliğidir.

```console
https://cognitiveservice/formrecognizer/v2.0-preview/layout/operations/54f0b076-4e38-43e5-81bd-b85b8835fdfb
```

## <a name="get-the-layout-results"></a>Düzen sonuçlarını alma

**Çözümle Düzen** API'sini aradıktan sonra, işlemin ve çıkarılan verilerin durumunu almak için Analiz Düzen Sonucu API'sini **[al'ı](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-preview/operations/GetAnalyzeLayoutResult)** ararsınız. Python komut dosyanızın altına aşağıdaki kodu ekleyin. Bu kod, yeni bir API çağrısında işlem kimliği değerini kullanır. Bu komut dosyası, sonuçlar kullanılabilir olana kadar API'yi düzenli aralıklarla çağırır. Bir veya daha fazla bir aralık öneririz.

```python
n_tries = 10
n_try = 0
wait_sec = 6
while n_try < n_tries:
    try:
        resp = get(url = get_url, headers = {"Ocp-Apim-Subscription-Key": apim_key})
        resp_json = json.loads(resp.text)
        if resp.status_code != 200:
            print("GET Layout results failed:\n%s" % resp_json)
            quit()
        status = resp_json["status"]
        if status == "succeeded":
            print("Layout Analysis succeeded:\n%s" % resp_json)
            quit()
        if status == "failed":
            print("Layout Analysis failed:\n%s" % resp_json)
            quit()
        # Analysis still running. Wait and retry.
        time.sleep(wait_sec)
        n_try += 1     
    except Exception as e:
        msg = "GET analyze results failed:\n%s" % str(e)
        print(msg)
        quit()
```

1. Senaryoyu kaydet.
1. Yine örneği `python` çalıştırmak için komutu kullanın. Örneğin, `python form-recognizer-layout.py`.

### <a name="examine-the-response"></a>Yanıtı inceleme

Komut dosyası, **Çözüm düzeni** işlemi tamamlanana kadar yanıtları konsola yazdırır. Daha sonra, çıkarılan verileri JSON biçiminde yazdırır. Düğüm, `"readResults"` sayfada ilgili sınırlayıcı kutusu yerleşimine sahip her metin satırını içerir. Alan, `"pageResults"` tablolar içindeki her bir metin parçasını, her biri sıra sütun koordinatı ile gösterir.

Aşağıdaki fatura resmine ve ilgili JSON çıktısına bakın. Çıktı basitlik için kısaltıldı.

> [!div class="mx-imgBorder"]
> ![Tablolu Contoso fatura belgesi](../media/contoso-invoice.png)

```json
{ 
  "status":"succeeded",
  "createdDateTime":"2019-11-12T19:55:36Z",
  "lastUpdatedDateTime":"2019-11-12T19:55:43Z",
  "analyzeResult":{ 
    "version":"2.0.0",
    "readResults":[ 
      { 
        "page":1,
        "language":"en",
        "angle":0,
        "width":8.5,
        "height":11,
        "unit":"inch",
        "lines":[ 
          { 
            "language":"en",
            "boundingBox":[ 
              0.5384,
              1.1583,
              1.4466,
              1.1583,
              1.4466,
              1.3534,
              0.5384,
              1.3534
            ],
            "text":"Contoso",
            "words":[ 
              { 
                "boundingBox":[ 
                  0.5384,
                  1.1583,
                  1.4466,
                  1.1583,
                  1.4466,
                  1.3534,
                  0.5384,
                  1.3534
                ],
                "text":"Contoso",
                "confidence":1
              }
            ]
          },
          { 
            "language":"en",
            "boundingBox":[ 
              0.7994,
              1.5143,
              1.3836,
              1.5143,
              1.3836,
              1.6154,
              0.7994,
              1.6154
            ],
            "text":"Address:",
            "words":[ 
              { 
                "boundingBox":[ 
                  0.7994,
                  1.5143,
                  1.3836,
                  1.5143,
                  1.3836,
                  1.6154,
                  0.7994,
                  1.6154
                ],
                "text":"Address:",
                "confidence":1
              }
            ]
          },
          ...
          { 
            "language":"en",
            "boundingBox":[ 
              6.2285,
              3.4114,
              6.3919,
              3.4114,
              6.3919,
              3.5119,
              6.2285,
              3.5119
            ],
            "text":"PT",
            "words":[ 
              { 
                "boundingBox":[ 
                  6.2285,
                  3.4114,
                  6.3919,
                  3.4114,
                  6.3919,
                  3.5119,
                  6.2285,
                  3.5119
                ],
                "text":"PT",
                "confidence":1
              }
            ]
          }
        ]
      }
    ],
    "pageResults":[ 
      { 
        "page":1,
        "tables":[ 
          { 
            "rows":2,
            "columns":6,
            "cells":[ 
              { 
                "rowIndex":0,
                "columnIndex":0,
                "text":"Invoice Number",
                "boundingBox":[ 
                  0.5075,
                  2.8088,
                  1.9061,
                  2.8088,
                  1.9061,
                  3.3219,
                  0.5075,
                  3.3219
                ],
                "elements":[ 
                  "#/readResults/0/lines/8/words/0",
                  "#/readResults/0/lines/8/words/1"
                ]
              },
              ...
            ]
          }
        ]
      }
    ]
  }
}
```

## <a name="next-steps"></a>Sonraki adımlar

Bu hızlı başlatmada, bir faturanın metin düzenini ayıklamak için Python ile Form Recognizer REST API'yi kullandınız. Ardından, Form Tanıyıcısı API'sini daha derinlemesine incelemek için başvuru belgelerine bakın.

> [!div class="nextstepaction"]
> [REST API başvuru belgeleri](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-preview/operations/AnalyzeLayoutAsync)
