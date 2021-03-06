---
title: 'Quickstart: Synthesize konuşma, C# (.NET Core) - Konuşma hizmeti'
titleSuffix: Azure Cognitive Services
description: Konuşma SDK'sını kullanarak C# 'da .NET Core on Windows altında konuşmayı nasıl sentezleyebilirsiniz öğrenin
services: cognitive-services
author: yinhew
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 04/04/2020
ms.author: yinhew
ms.openlocfilehash: 91e06805b687e66c147b0904175ae20d01387acf
ms.sourcegitcommit: 530e2d56fc3b91c520d3714a7fe4e8e0b75480c8
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/14/2020
ms.locfileid: "81274836"
---
> [!NOTE]
> .NET Core, [.NET Standard](https://docs.microsoft.com/dotnet/standard/net-standard) teknik özelliklerini gerçekleştiren açık kaynaklı, platformlar arası bir .NET platformudur.

## <a name="prerequisites"></a>Ön koşullar

Başlamadan önce şunları unutmayın:

> [!div class="checklist"]
> * [Azure Konuşma Kaynağı Oluşturma](../../../../get-started.md)
> * [Geliştirme ortamınızı kurup boş bir proje oluşturun](../../../../quickstarts/setup-platform.md?tabs=dotnetcore&pivots=programming-language-csharp)

## <a name="add-sample-code"></a>Örnek kod ekleme

1. `Program.cs` dosyasını açın ve tüm kodu aşağıdakiyle değiştirin.

    [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/csharp/dotnetcore/text-to-speech/helloworld/Program.cs#code)]

1. Aynı dosyada, `YourSubscriptionKey` dizesini abonelik anahtarınızla değiştirin.

1. Ayrıca `YourServiceRegion` dizesini de aboneliğinizle ilişkili [bölge](~/articles/cognitive-services/Speech-Service/regions.md) ile (örneğin ücretsiz deneme aboneliğinde `westus`) değiştirin.

1. Proje üzerindeki değişiklikleri kaydedin.

## <a name="build-and-run-the-app"></a>Uygulamayı derleme ve çalıştırma

1. Uygulamayı derleyin. Menü çubuğundan **Yapı** > **Çözüm'üne**bakın. Kodun hatasız derlenmesi gerekir.

    ![Visual Studio uygulamasının, Çözümü Derle seçeneğinin vurgulandığı ekran görüntüsü](~/articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-dotnetcore-windows-05-build.png "Başarılı yapı")

1. Uygulamayı başlatın. Menü çubuğundan **Hata** > **Ayıklama Başlat'ı**seçin veya **F5 tuşuna**basın.

    ![Visual Studio uygulamasının, Hata Ayıklamayı Başlat seçeneğinin vurgulandığı ekran görüntüsü](~/articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-dotnetcore-windows-06-start-debugging.png "Uygulamayı hata ayıklamaya başlatın")

1. Bir konsol penceresi görüntülenir ve bazı metin yazmanızı ister. Birkaç kelime veya cümle yazın. Yazdığınız metin Konuşma hizmetine aktarılır ve konuşmacınızda çalan konuşmayla sentezlenir.

    ![Başarılı sentezden sonra konsol çıkışının ekran görüntüsü](~/articles/cognitive-services/Speech-Service/media/sdk/qs-tts-csharp-dotnet-windows-console-output.png "Başarılı sentezden sonra konsol çıkışı")

## <a name="next-steps"></a>Sonraki adımlar

[!INCLUDE [Speech synthesis basics](../../text-to-speech-next-steps.md)]

## <a name="see-also"></a>Ayrıca bkz.

- [Özel Ses Oluşturma](~/articles/cognitive-services/Speech-Service/how-to-custom-voice-create-voice.md)
- [Özel ses örneklerini kaydetme](~/articles/cognitive-services/Speech-Service/record-custom-voice-samples.md)
