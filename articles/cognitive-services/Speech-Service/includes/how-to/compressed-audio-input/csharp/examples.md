---
author: IEvangelist
ms.service: cognitive-services
ms.topic: include
ms.date: 03/09/2020
ms.author: dapine
ms.openlocfilehash: 0e8a6a0ffa451337b9e7ea7590cfe614fc1129a1
ms.sourcegitcommit: 27bbda320225c2c2a43ac370b604432679a6a7c0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/31/2020
ms.locfileid: "80409697"
---
Sıkıştırılmış ses biçiminde Konuşma hizmetine akış `PullAudioInputStream` yapmak `PushAudioInputStream`için, oluştur veya. Ardından, akış `AudioConfig` sınıfınızın sıkıştırma biçimini belirten bir örneğini oluşturun.

OPUS/OGG adlı `pushStream` bir giriş akışı sınıfınız olduğunu varsayalım. Kodunuz şu şekilde görünebilir:

```csharp
using Microsoft.CognitiveServices.Speech;
using Microsoft.CognitiveServices.Speech.Audio;

// ... omitted for brevity

var speechConfig =
    SpeechConfig.FromSubscription(
        "YourSubscriptionKey",
        "YourServiceRegion");

// Create an audio config specifying the compressed
// audio format and the instance of your input stream class.
var audioFormat =
    AudioStreamFormat.GetCompressedFormat(
        AudioStreamContainerFormat.OGG_OPUS);
var audioConfig =
    AudioConfig.FromStreamInput(
        pushStream,
        audioFormat);

using var recognizer = new SpeechRecognizer(speechConfig, audioConfig);
var result = await recognizer.RecognizeOnceAsync();

var text = result.Text;
```