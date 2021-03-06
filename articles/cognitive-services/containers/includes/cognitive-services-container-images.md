---
title: Konteyner depoları ve görüntüleri
services: cognitive-services
author: aahill
manager: nitinme
description: Tüm Bilişsel Hizmet teklifleri için konteyner kayıtları, depolar ve görüntü adlarını temsil eden iki tablo.
ms.service: cognitive-services
ms.topic: include
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: a854a090af908da691e9b26f5b0714c6560fc0ba
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/08/2020
ms.locfileid: "80876853"
---
### <a name="container-repositories-and-images"></a>Konteyner depoları ve görüntüleri

Aşağıdaki tablolar, Azure Bilişsel Hizmetleri tarafından sunulan kullanılabilir kapsayıcı görüntülerinin bir listesidir. Kullanılabilir tüm kapsayıcı resim adlarının ve bunların kullanılabilir etiketlerinin tam listesi [için, Bkz. Bilişsel Hizmetler kapsayıcı resim etiketleri.](../container-image-tags.md) Şu anda, genellikle kullanılabilir (GA) bilişsel hizmetler kapsayıcıları vardır. Şimdilik, daha fazla duyuru yapılana kadar - konteynerler ya *Kamu Ungated* veya *Public Gated Önizleme*olarak kullanılabilir.

 - *Kamu Ungated*: konteynerler bir gating mekanizması olmadan kamuya açıktır.
 - *Public Gated Preview*: kapsayıcılar herkese açıktır, ancak önce kapsayıcı kayıt defterine erişmek için resmi istek gerektirir.

#### <a name="public-ungated-container-registry-mcrmicrosoftcom"></a>Genel "Ungated" (konteyner `mcr.microsoft.com`kayıt defteri: )

Microsoft Kapsayıcı Kayıt Defteri (MCR), Bilişsel Hizmetler için herkese açık tüm "ungated" kapsayıcılarını birbiri olarak sıralar. Konteynerler doğrudan [Docker hub'ından](https://hub.docker.com/_/microsoft-azure-cognitive-services)da temin edilebilir.

| Hizmet | Kapsayıcı | Konteyner Kayıt Defteri / Depo / Resim Adı |
|--|--|--|
| [LUIS](../../LUIS/luis-container-howto.md) | LUIS | `mcr.microsoft.com/azure-cogni'ive-services/luis` |
| [Metin Analizi](../../text-analytics/how-tos/text-analytics-how-to-install-containers.md) | Anahtar İfade Ayıklama | `mcr.microsoft.com/azure-cognitive-services/keyphrase` |
| [Metin Analizi](../../text-analytics/how-tos/text-analytics-how-to-install-containers.md) | Dil Algılama | `mcr.microsoft.com/azure-cognitive-services/language` |
| [Metin Analizi](../../text-analytics/how-tos/text-analytics-how-to-install-containers.md) | Yaklaşım Analizi | `mcr.microsoft.com/azure-cognitive-services/sentiment` |

#### <a name="public-gated-preview-container-registry-containerpreviewazurecrio"></a>Genel "Geçitli" Önizleme (konteyner `containerpreview.azurecr.io`kayıt defteri: )

Kapsayıcı Önizleme kayıt defteri, Bilişsel Hizmetler için herkese açık tüm "kapılı" kapsayıcıları barındırmaktadır. Bu kapsayıcılar, konteyner kayıt defteri üzerinden erişim için resmi bir istek gerektirir.

| Hizmet | Kapsayıcı | Konteyner Kayıt Defteri / Depo / Resim Adı |
|--|--|--|
| [Anomali dedektörü](../../anomaly-detector/anomaly-detector-container-howto.md) | Anomali Algılayıcısı | `containerpreview.azurecr.io/microsoft/cognitive-services-anomaly-detector` |
| [Görüntü İşleme](../../Computer-vision/computer-vision-how-to-install-containers.md) | Okuma | `containerpreview.azurecr.io/microsoft/cognitive-services-read` |
| [Yüz](../../face/face-how-to-install-containers.md) | Yüz | `containerpreview.azurecr.io/microsoft/cognitive-services-face` |
| [Form tanıyıcı](https://go.microsoft.com/fwlink/?linkid=2083826&clcid=0x409) | Form Tanıma | `containerpreview.azurecr.io/microsoft/cognitive-services-form-recognizer` |
| [Konuşma Hizmeti API’si](../../speech-service/speech-container-howto.md?tab=stt) | Konuşmayı metne dönüştürme | `containerpreview.azurecr.io/microsoft/cognitive-services-speech-to-text` |
| [Konuşma Hizmeti API’si](../../speech-service/speech-container-howto.md?tab=cstt) | Özel Konuşma-metin | `containerpreview.azurecr.io/microsoft/cognitive-services-custom-speech-to-text` |
| [Konuşma Hizmeti API’si](../../speech-service/speech-container-howto.md?tab=tts) | Metin okuma | `containerpreview.azurecr.io/microsoft/cognitive-services-text-to-speech` |
| [Konuşma Hizmeti API’si](../../speech-service/speech-container-howto.md?tab=ctts) | Özel Metinden konuşmaya | `containerpreview.azurecr.io/microsoft/cognitive-services-custom-text-to-speech` |
