---
title: Kapsayıcı desteği
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 7/5/2019
ms.author: dapine
ms.openlocfilehash: 3c42bf2b2acc2472741bd603ea9d653a314ecc40
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/28/2020
ms.locfileid: "73524096"
---
## <a name="create-a-speech-resource"></a>Konuşma kaynağı oluşturma

1. [Azure portalında](https://portal.azure.com) oturum açma
1. [ **Konuşma** Oluştur](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesSpeechServices) kaynağını tıklatın
1. Gerekli tüm ayarları girin:

    |Ayar|Değer|
    |--|--|
    |Adı|İstenilen ad (2-64 karakter)|
    |Abonelik|Uygun aboneliği seçin|
    |Konum|Yakındaki ve kullanılabilir herhangi bir konumu seçin|
    |Fiyatlandırma Katmanı|`F0`- minimum fiyatlandırma katmanı|
    |Kaynak Grubu|Kullanılabilir bir kaynak grubu seçme|

1. **Oluştur'u** tıklatın ve kaynağın oluşturulmasını bekleyin. Oluşturulduktan sonra kaynak sayfasına gidin
1. Yapılandırılmış `endpoint` ve api anahtarı toplamak:

    |Portaldaki Kaynak Sekmesi|Ayar|Değer|
    |--|--|--|
    |**Genel bakış**|Uç Nokta|Bitiş noktasını kopyalayın. Bu benzer görünüyor`https://speech.cognitiveservices.azure.com/sts/v1.0/issuetoken`|
    |**Anahtarlar**|API Anahtarı|İki anahtardan 1'ini kopyalayın. Boşluksuz veya tiresiz 32 alfanümerik karakterli bir `xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx`dizedir.|
