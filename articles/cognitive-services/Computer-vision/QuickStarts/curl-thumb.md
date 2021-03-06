---
title: 'Quickstart: Küçük resim oluşturma - REST, cURL'
titleSuffix: Azure Cognitive Services
description: Bu hızlı başlangıçta, cURL ile Görüntü İşleme API’sini kullanarak bir görüntüden küçük resim oluşturacaksınız.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: quickstart
ms.date: 12/05/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: 6cb9dadc107c6907f1ccb28a876270e577f10395
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74977310"
---
# <a name="quickstart-generate-a-thumbnail-using-the-computer-vision-rest-api-and-curl"></a>Quickstart: Computer Vision REST API ve cURL kullanarak bir küçük resim oluşturun

Bu hızlı başlangıçta, Computer Vision REST API'yi kullanarak bir görüntüden küçük resim oluşturursunuz. Giriş görüntüsünden farklı boy oranı açısından farklılık gösteren istenen yükseklik ve genişliği belirtirsiniz. Computer Vision, ilgi alanını akıllıca belirlemek ve o bölgenin etrafında kırpma koordinatları oluşturmak için akıllı kırpma kullanır.

Azure aboneliğiniz yoksa, başlamadan önce [ücretsiz](https://azure.microsoft.com/free/ai/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=cognitive-services) bir hesap oluşturun.

## <a name="prerequisites"></a>Ön koşullar

- [cURL](https://curl.haxx.se/windows)’niz olmalıdır.
- Görüntü İşleme için bir abonelik anahtarınız olması gerekir. [Bilişsel Hizmetleri Deneyin](https://azure.microsoft.com/try/cognitive-services/?api=computer-vision)ücretsiz bir deneme anahtarı alabilirsiniz. Veya Computer Vision'a abone olmak ve anahtarınızı almak için [Bilişsel Hizmetler Oluştur hesabındaki](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) yönergeleri izleyin.

## <a name="get-thumbnail-request"></a>Küçük Resim Alma isteği

[Küçük Resim Alma yöntemi](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fb) ile, bir görüntünün küçük resmini oluşturabilirsiniz.

Örneği çalıştırmak için aşağıdaki adımları uygulayın:

1. Aşağıdaki kodu bir düzenleyicinin içine kopyalayın.
1. `<Subscription Key>` değerini geçerli abonelik anahtarınızla değiştirin.
1. `<File>` değerini küçük resmin kaydedileceği yol ve dosya adı ile değiştirin.
1. Gerekirse İstek URL’sini (`https://westcentralus.api.cognitive.microsoft.com/vision/v2.1`) abonelik anahtarlarınızı aldığınız konumu kullanacak şekilde değiştirin.
1. İsteğe bağlı olarak, analiz edilecek görüntüyü (`{\"url\":\"...`) değiştirin.
1. cURL yüklü bir bilgisayarda bir komut penceresi açın.
1. Kodu pencereye yapıştırıp komutu çalıştırın.

>[!NOTE]
>REST çağrınızda abonelik anahtarlarınızı almak için kullandığınız konumu kullanmanız gerekir. Örneğin, güvenlik anahtarlarınızı westus konumundan aldıysanız, aşağıdaki URL’de "westcentralus" değerini "westus" olarak değiştirin.

## <a name="create-and-run-the-sample-command"></a>Örnek komutu oluşturma ve çalıştırma

Örneği oluşturup çalıştırmak için aşağıdaki adımları uygulayın:

1. Aşağıdaki komutu bir metin düzenleyicisine kopyalayın.
1. Gerektiğinde komutta aşağıdaki değişiklikleri yapın:
    1. `<subscriptionKey>` değerini abonelik anahtarınızla değiştirin.
    1. `<thumbnailFile>` değerini, küçük resmin kaydedileceği dosyanın yolu ve adıyla değiştirin.
    1. İstek URL'sinin ilk bölümünü`westcentralus`( ) kendi bitiş noktası URL'nizdeki metinle değiştirin.
        [!INCLUDE [Custom subdomains notice](../../../../includes/cognitive-services-custom-subdomains-note.md)]
    1. İsteğe bağlı olarak, istek gövdesindeki görüntü URL’sini (`https://upload.wikimedia.org/wikipedia/commons/thumb/5/56/Shorkie_Poo_Puppy.jpg/1280px-Shorkie_Poo_Puppy.jpg\`) içinden küçük resim oluşturulacak farklı bir görüntünün URL’si ile değiştirin.
1. Bir komut istemi penceresi açın.
1. Metin düzenleyicisindeki komutu komut istemi penceresine yapıştırın ve komutu çalıştırın.

    ```bash
    curl -H "Ocp-Apim-Subscription-Key: <subscriptionKey>" -o <thumbnailFile> -H "Content-Type: application/json" "https://westcentralus.api.cognitive.microsoft.com/vision/v2.1/generateThumbnail?width=100&height=100&smartCropping=true" -d "{\"url\":\"https://upload.wikimedia.org/wikipedia/commons/thumb/5/56/Shorkie_Poo_Puppy.jpg/1280px-Shorkie_Poo_Puppy.jpg\"}"
    ```

## <a name="examine-the-response"></a>Yanıtı inceleme

Başarılı bir yanıt, küçük resim görüntüsünü `<thumbnailFile>` içinde belirtilen dosyaya yazar. İstek başarısız olursa yanıt, bir hata kodu ve nelerin yanlış gittiğini belirlemeye yardımcı olması için bir ileti içerir. İstek başarılı görünüyor, ancak oluşturulan küçük resim geçerli bir resim dosyası değilse, abonelik anahtarınız geçerli olmayabilir.

## <a name="next-steps"></a>Sonraki adımlar

Bir görüntüyü nasıl analiz edecek, ünlüleri ve simgeleri nasıl algılayabilen, küçük resim oluşturma ve basılı ve el yazısıyla yazılmış metinleri ayıklamak için BilgisayarLı Vizyon API'sini keşfedin. Görüntü İşleme API'sini hızlı bir şekilde denemeniz için [Open API test konsolu](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa/console) konusuna bakın.

> [!div class="nextstepaction"]
> [Görüntü İşleme API’sini keşfetme](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44)
