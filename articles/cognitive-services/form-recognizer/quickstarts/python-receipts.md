---
title: 'Quickstart: Python kullanarak alma makbuzu verilerini ayıklayın - Form Tanıyın'
titleSuffix: Azure Cognitive Services
description: Bu hızlı başlangıçta, ABD satış makbuzlarının görüntülerinden veri ayıklamak için Python ile Form Recognizer REST API'yi kullanırsınız.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: quickstart
ms.date: 01/27/2020
ms.author: pafarley
ms.openlocfilehash: 2224ec64712ff9d1745231f39a1521ae941304ff
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77118760"
---
# <a name="quickstart-extract-receipt-data-using-the-form-recognizer-rest-api-with-python"></a>Quickstart: Python ile Form Recognizer REST API kullanarak alma makbuz verileri ayıklama

Bu hızlı başlangıçta, ABD satış makbuzlarında ilgili bilgileri ayıklamak ve tanımlamak için Python ile azure form recognizer REST API'sini kullanırsınız.

Azure aboneliğiniz yoksa, başlamadan önce [ücretsiz](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) bir hesap oluşturun.

## <a name="prerequisites"></a>Ön koşullar

Bu hızlı başlangıcı tamamlamak için şunları yapmış olmalısınız:
- [Python](https://www.python.org/downloads/) yüklendi (örneği yerel olarak çalıştırmak istiyorsanız).
- Makbuz resminin URL'si. Bu hızlı başlangıç için örnek bir [resim](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/curl/form-recognizer/contoso-allinone.jpg?raw=true) kullanabilirsiniz.

## <a name="create-a-form-recognizer-resource"></a>Form Tanıyıcı kaynağı oluşturma

[!INCLUDE [create resource](../includes/create-resource.md)]

## <a name="analyze-a-receipt"></a>Bir makbuzu analiz

Bir makbuzu çözümlemeye başlamak için, aşağıdaki Python komut dosyasını kullanarak **[Analiz Makbuzu](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-preview/operations/AnalyzeReceiptAsync)** API'sini ararsınız. Komut dosyasını çalıştırmadan önce aşağıdaki değişiklikleri yapın:

1. Form `<Endpoint>` Recognizer aboneliğinizle elde ettiğiniz bitiş noktasıyla değiştirin.
1. Makbuz `<your receipt URL>` görüntüsünün URL adresiyle değiştirin.
1. Önceki `<subscription key>` adımdan kopyaladığınız abonelik anahtarıyla değiştirin.

    ```python
    ########### Python Form Recognizer Async Layout #############

    import json
    import time
    from requests import get, post
    
    # Endpoint URL
    endpoint = r"<Endpoint>"
    apim_key = "<subscription key>"
    post_url = endpoint + "/formrecognizer/v2.0-preview/prebuilt/receipt/analyze"
    source = r"<path to your receipt>"
    
    headers = {
        # Request headers
        'Content-Type': '<file type>',
        'Ocp-Apim-Subscription-Key': apim_key,
    }
    
    params = {
        "includeTextDetails": True
    }
    
    with open(source, "rb") as f:
        data_bytes = f.read()
    
    try:
        resp = post(url = post_url, data = data_bytes, headers = headers, params = params)
        if resp.status_code != 202:
            print("POST analyze failed:\n%s" % resp.text)
            quit()
        print("POST analyze succeeded:\n%s" % resp.headers)
        get_url = resp.headers["operation-location"]
    except Exception as e:
        print("POST analyze failed:\n%s" % str(e))
        quit()
    ```

1. Kodu .py uzantılı bir dosyaya kaydedin. Örneğin, *form-recognizer-receipts.py.*
1. Bir komut istemi penceresi açın.
1. İstemde, örneği çalıştırmak için `python` komutunu kullanın. Örneğin, `python form-recognizer-receipts.py`.

Komut dosyasının `202 (Success)` konsola yazdıracağı **İşlem-Konum** üstbilgisini içeren bir yanıt alırsınız. Bu üstbilgi, eşzamanlı işlemin durumunu sorgulamak ve sonuçları almak için kullanabileceğiniz bir işlem kimliği içerir. Aşağıdaki örnek değerde, sonraki `operations/` dize işlem kimliğidir.

```console
https://cognitiveservice/formrecognizer/v2.0-preview/prebuilt/receipt/operations/54f0b076-4e38-43e5-81bd-b85b8835fdfb
```

## <a name="get-the-receipt-results"></a>Makbuz sonuçlarını alın

**Analiz Makbuzu** API'sini aradıktan sonra, işlemin durumunu ve çıkarılan verileri almak için **[Analiz Makbuzu Sonuç](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-preview/operations/GetAnalyzeReceiptResult)** API'sini alırsınız. Python komut dosyanızın altına aşağıdaki kodu ekleyin. Bu, yeni bir API çağrısında işlem kimliği değerini kullanır. Bu komut dosyası, sonuçlar kullanılabilir olana kadar API'yi düzenli aralıklarla çağırır. Bir veya daha fazla bir aralık öneririz.

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
            print("Analysis failed:\n%s" % resp_json)
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
1. Yine örneği `python` çalıştırmak için komutu kullanın. Örneğin, `python form-recognizer-receipts.py`.

### <a name="examine-the-response"></a>Yanıtı inceleme

Komut dosyası, **Çözümleme Girişi** işlemi tamamlanana kadar yanıtları konsola yazdırır. Daha sonra, çıkarılan metin verilerini JSON biçiminde yazdırır. Alan, `"recognitionResults"` fişten çıkarılan her metin satırını içerir `"understandingResults"` ve alan makbuzun en alakalı bölümleri için anahtar/değer bilgilerini içerir.

Aşağıdaki makbuz resmine ve ilgili JSON çıkışına bakın. Çıktı okunabilirlik için kısaltıldı.

![Contoso mağazasından bir makbuz](../media/contoso-allinone.jpg)

Düğüm, `"recognitionResults"` tanınan metnin tümlerini içerir. Metin sayfaya, sonra satıra, sonra da tek tek sözcüklerle düzenlenir. Düğüm, `"understandingResults"` modelin keşfettiği makbuza özgü değerleri içerir. Burada vergi, toplam, tüccar adresi ve benzeri gibi yararlı anahtar/ değer çiftleri bulacaksınız.

```json
{ 
  "status":"succeeded",
  "createdDateTime":"2019-12-17T04:11:24Z",
  "lastUpdatedDateTime":"2019-12-17T04:11:32Z",
  "analyzeResult":{ 
    "version":"2.0.0",
    "readResults":[ 
      { 
        "page":1,
        "angle":0.6893,
        "width":1688,
        "height":3000,
        "unit":"pixel",
        "language":"en",
        "lines":[ 
          { 
            "text":"Contoso",
            "boundingBox":[ 
              635,
              510,
              1086,
              461,
              1098,
              558,
              643,
              604
            ],
            "words":[ 
              { 
                "text":"Contoso",
                "boundingBox":[ 
                  639,
                  510,
                  1087,
                  461,
                  1098,
                  551,
                  646,
                  604
                ],
                "confidence":0.955
              }
            ]
          },
          ...
        ]
      }
    ],
    "documentResults":[ 
      { 
        "docType":"prebuilt:receipt",
        "pageRange":[ 
          1,
          1
        ],
        "fields":{ 
          "ReceiptType":{ 
            "type":"string",
            "valueString":"Itemized",
            "confidence":0.692
          },
          "MerchantName":{ 
            "type":"string",
            "valueString":"Contoso Contoso",
            "text":"Contoso Contoso",
            "boundingBox":[ 
              378.2,
              292.4,
              1117.7,
              468.3,
              1035.7,
              812.7,
              296.3,
              636.8
            ],
            "page":1,
            "confidence":0.613,
            "elements":[ 
              "#/readResults/0/lines/0/words/0",
              "#/readResults/0/lines/1/words/0"
            ]
          },
          "MerchantAddress":{ 
            "type":"string",
            "valueString":"123 Main Street Redmond, WA 98052",
            "text":"123 Main Street Redmond, WA 98052",
            "boundingBox":[ 
              302,
              675.8,
              848.1,
              793.7,
              809.9,
              970.4,
              263.9,
              852.5
            ],
            "page":1,
            "confidence":0.99,
            "elements":[ 
              "#/readResults/0/lines/2/words/0",
              "#/readResults/0/lines/2/words/1",
              "#/readResults/0/lines/2/words/2",
              "#/readResults/0/lines/3/words/0",
              "#/readResults/0/lines/3/words/1",
              "#/readResults/0/lines/3/words/2"
            ]
          },
          "MerchantPhoneNumber":{ 
            "type":"phoneNumber",
            "valuePhoneNumber":"+19876543210",
            "text":"987-654-3210",
            "boundingBox":[ 
              278,
              1004,
              656.3,
              1054.7,
              646.8,
              1125.3,
              268.5,
              1074.7
            ],
            "page":1,
            "confidence":0.99,
            "elements":[ 
              "#/readResults/0/lines/4/words/0"
            ]
          },
          "TransactionDate":{ 
            "type":"date",
            "valueDate":"2019-06-10",
            "text":"6/10/2019",
            "boundingBox":[ 
              265.1,
              1228.4,
              525,
              1247,
              518.9,
              1332.1,
              259,
              1313.5
            ],
            "page":1,
            "confidence":0.99,
            "elements":[ 
              "#/readResults/0/lines/5/words/0"
            ]
          },
          "TransactionTime":{ 
            "type":"time",
            "valueTime":"13:59:00",
            "text":"13:59",
            "boundingBox":[ 
              541,
              1248,
              677.3,
              1261.5,
              668.9,
              1346.5,
              532.6,
              1333
            ],
            "page":1,
            "confidence":0.977,
            "elements":[ 
              "#/readResults/0/lines/5/words/1"
            ]
          },
          "Items":{ 
            "type":"array",
            "valueArray":[ 
              { 
                "type":"object",
                "valueObject":{ 
                  "Quantity":{ 
                    "type":"number",
                    "text":"1",
                    "boundingBox":[ 
                      245.1,
                      1581.5,
                      300.9,
                      1585.1,
                      295,
                      1676,
                      239.2,
                      1672.4
                    ],
                    "page":1,
                    "confidence":0.92,
                    "elements":[ 
                      "#/readResults/0/lines/7/words/0"
                    ]
                  },
                  "Name":{ 
                    "type":"string",
                    "valueString":"Cappuccino",
                    "text":"Cappuccino",
                    "boundingBox":[ 
                      322,
                      1586,
                      654.2,
                      1601.1,
                      650,
                      1693,
                      317.8,
                      1678
                    ],
                    "page":1,
                    "confidence":0.923,
                    "elements":[ 
                      "#/readResults/0/lines/7/words/1"
                    ]
                  },
                  "TotalPrice":{ 
                    "type":"number",
                    "valueNumber":2.2,
                    "text":"$2.20",
                    "boundingBox":[ 
                      1107.7,
                      1584,
                      1263,
                      1574,
                      1268.3,
                      1656,
                      1113,
                      1666
                    ],
                    "page":1,
                    "confidence":0.918,
                    "elements":[ 
                      "#/readResults/0/lines/8/words/0"
                    ]
                  }
                }
              },
              ...
            ]
          },
          "Subtotal":{ 
            "type":"number",
            "valueNumber":11.7,
            "text":"11.70",
            "boundingBox":[ 
              1146,
              2221,
              1297.3,
              2223,
              1296,
              2319,
              1144.7,
              2317
            ],
            "page":1,
            "confidence":0.955,
            "elements":[ 
              "#/readResults/0/lines/13/words/1"
            ]
          },
          "Tax":{ 
            "type":"number",
            "valueNumber":1.17,
            "text":"1.17",
            "boundingBox":[ 
              1190,
              2359,
              1304,
              2359,
              1304,
              2456,
              1190,
              2456
            ],
            "page":1,
            "confidence":0.979,
            "elements":[ 
              "#/readResults/0/lines/15/words/1"
            ]
          },
          "Tip":{ 
            "type":"number",
            "valueNumber":1.63,
            "text":"1.63",
            "boundingBox":[ 
              1094,
              2479,
              1267.7,
              2485,
              1264,
              2591,
              1090.3,
              2585
            ],
            "page":1,
            "confidence":0.941,
            "elements":[ 
              "#/readResults/0/lines/17/words/1"
            ]
          },
          "Total":{ 
            "type":"number",
            "valueNumber":14.5,
            "text":"$14.50",
            "boundingBox":[ 
              1034.2,
              2617,
              1387.5,
              2638.2,
              1380,
              2763,
              1026.7,
              2741.8
            ],
            "page":1,
            "confidence":0.985,
            "elements":[ 
              "#/readResults/0/lines/19/words/0"
            ]
          }
        }
      }
    ]
  }
}
```

## <a name="next-steps"></a>Sonraki adımlar

Bu hızlı başlangıçta, bir satış makbuzunun içeriğini ayıklamak için Python ile Form Recognizer REST API'sini kullandınız. Ardından, Form Tanıyıcısı API'sini daha derinlemesine incelemek için başvuru belgelerine bakın.

> [!div class="nextstepaction"]
> [REST API başvuru belgeleri](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-preview/operations/AnalyzeReceiptAsync)
