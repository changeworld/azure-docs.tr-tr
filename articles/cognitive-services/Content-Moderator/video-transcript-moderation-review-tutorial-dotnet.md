---
title: "Öğretici: .NET'te videoları ve transkriptleri denetleme - Content Moderator"
titleSuffix: Azure Cognitive Services
description: Bu öğretici, makine destekli ılımlılık ve döngü içinde insan incelemesi oluşturma ile tam bir video ve transkript ılımlılık çözümü oluşturmak için nasıl anlamanıza yardımcı olur.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: tutorial
ms.date: 12/05/2019
ms.author: pafarley
ms.openlocfilehash: 09569be88daa6295834e58da6b51656d0438bed4
ms.sourcegitcommit: 530e2d56fc3b91c520d3714a7fe4e8e0b75480c8
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/14/2020
ms.locfileid: "81270936"
---
# <a name="tutorial-video-and-transcript-moderation"></a>Öğretici: Video ve transkript denetimi

Bu eğitimde, makine destekli ılımlılık ve insan inceleme entegrasyonu ile tam bir video ve transkript ılımlılık çözümü oluşturmak için nasıl öğreneceksiniz.

Bu öğretici şunların nasıl yapıldığını gösterir:

> [!div class="checklist"]
> - Daha hızlı işlenmesi için giriş videolarını sıkıştırma
> - İçgörüler içeren çekimleri ve kareleri almak için videoyu denetleme
> - Küçük resimler (görüntüler) oluşturmak için kare zaman damgalarını kullanma
> - Video incelemeleri oluşturmak için zaman damgalarını ve küçük resimleri gönderme
> - Media Indexer API'siyle video konuşmasını metne (transkript) dönüştürme
> - Metin denetimi hizmetiyle transkripti denetleme
> - Denetlenen transkripti video incelemesine ekleme

## <a name="prerequisites"></a>Ön koşullar

- İçerik Moderatör [İnceleme aracı](https://contentmoderator.cognitive.microsoft.com/) web sitesine kaydolun ve özel etiketler oluşturun. Bu adımda yardıma ihtiyacınız varsa [Etiketleri Kullan'ı](Review-Tool-User-Guide/tags.md) görün.

    ![Video ılımlılığı özel etiketleri ekran görüntüsü](images/video-tutorial-custom-tags.png)
- Örnek uygulamayı çalıştırmak için bir Azure hesabı, bir Azure Medya Hizmetleri kaynağı, bir Azure İçerik Yöneticisi kaynağı ve Azure Etkin Dizin kimliği gerekir. Bu kaynakların nasıl alınacağına ilişkin talimatlar için [Video Moderasyon API](video-moderation-api.md) kılavuzuna bakın.
- GitHub'dan [Video inceleme konsolu uygulamasını](https://github.com/MicrosoftContentModerator/VideoReviewConsoleApp) indirin.

## <a name="enter-credentials"></a>Kimlik bilgilerini girin

Dosyayı `App.config` düzenleme ve Etkin Dizin kiracı adı, hizmet bitiş noktaları `#####`ve abonelik anahtarları tarafından belirtilen ekleyin. Aşağıdaki bilgiler gerekir:

|Anahtar|Açıklama|
|-|-|
|`AzureMediaServiceRestApiEndpoint`|Azure Media Services (AMS) API'si için uç nokta|
|`ClientSecret`|Azure Media Services için abonelik anahtarı|
|`ClientId`|Azure Media Services için istemci kimliği|
|`AzureAdTenantName`|Kuruluşunuzu temsil eden Active Directory kiracı adı|
|`ContentModeratorReviewApiSubscriptionKey`|Content Moderator inceleme API'si için abonelik anahtarı|
|`ContentModeratorApiEndpoint`|Content Moderator API’si için uç nokta|
|`ContentModeratorTeamId`|Content Moderator ekip kimliği|

## <a name="examine-the-main-code"></a>Ana kodu inceleyin

`Program.cs` içindeki `Program` sınıfı, video denetim uygulamasına ana giriş noktasıdır.

### <a name="methods-of-program-class"></a>Program sınıfı nın yöntemleri

|Yöntem|Açıklama|
|-|-|
|`Main`|Komut satırını ayrıştırır, kullanıcı girişlerini toplar ve işlemeyi başlatır.|
|`ProcessVideo`|Video incelemelerini sıkıştırır, karşıya yükler, denetler ve oluşturur.|
|`CreateVideoStreamingRequest`|Videoyu karşıya yüklemek için bir akış oluşturur|
|`GetUserInputs`|Kullanıcı girişlerini toplar; hiç komut satırı seçeneği olmadığında kullanılır|
|`Initialize`|Denetim işlemi için gereken nesneleri başlatır|

### <a name="the-main-method"></a>Main yöntemi

`Main()` yürütmenin başlatıldığı yerdir, dolayısıyla video denetleme işlemini anlamaya buradan başlanır.

[!code-csharp[Main](~/VideoReviewConsoleApp/Microsoft.ContentModerator.AMSComponent/AMSComponentClient/Program.cs?range=20-24,42-52,54-74)]

`Main()` aşağıdaki komut satırı bağımsız değişkenlerini işler:

- Denetleme için gönderilecek MPEG-4 video dosyalarını içeren dizinin yolu. Bu dizindeki ve onun alt dizinlerindeki tüm `*.mp4` dosyaları denetleme için gönderilir.
- İsteğe bağlı olarak, sesi denetlemek amacıyla metin transkriptlerinin oluşturulup oluşturulmayacağını gösteren bir Boole (doğru/yanlış) bayrağı.

Hiçbir komut satırı bağımsız değişkeni yoksa, `Main()``GetUserInputs()` çağrısı yapar. Bu yöntem kullanıcıdan tek bir video dosyasının yolunu girmesini ve metin transkriptinin oluşturulup oluşturulmayacağını belirtmesini ister.

> [!NOTE]
> Konsol uygulaması, yüklenen videonun ses parçasından transkript oluşturmak için [Azure Media Indexer API'sini](https://docs.microsoft.com/azure/media-services/media-services-process-content-with-indexer2) kullanır. Sonuçlar WebVTT formatında sağlanmaktadır. Bu biçimle ilgili daha fazla bilgi için bkz. [Web Video Metin Parçaları Biçimi](https://developer.mozilla.org/docs/Web/API/WebVTT_API).

### <a name="initialize-and-processvideo-methods"></a>Initialize ve ProcessVideo yöntemleri

Programın seçenekleri ister komut satırından ister etkileşimli kullanıcı girişinden gelsin, bundan sonra `Main()` aşağıdaki örnekleri oluşturmak için `Initialize()` yöntemini çağırır:

|Sınıf|Açıklama|
|-|-|
|`AMSComponent`|Video dosyalarını denetleme için göndermeden önce sıkıştırır.|
|`AMSconfigurations`|Uygulamanın `App.config` dosyasında bulunan yapılandırma verilerine arabirim oluşturur.|
|`VideoModerator`| AMS SDK'sı kullanılarak karşıya yükleme, kodlama, şifreleme ve denetleme|
|`VideoReviewApi`|Content Moderator hizmetindeki video incelemelerini yönetir|

Bu sınıflar (gayet anlaşılır olan `AMSConfigurations` dışında), bu öğreticinin gelecek bölümlerinde daha ayrıntılı olarak ele alınacaktır.

Son olarak, video dosyalarının her biri için `ProcessVideo()` yöntemi çağrılarak bu dosyalar birer birer işlenir.

[!code-csharp[ProcessVideo](~/VideoReviewConsoleApp/Microsoft.ContentModerator.AMSComponent/AMSComponentClient/Program.cs?range=76-118)]

`ProcessVideo()` yöntemi gayet kolay anlaşılır. Sırasıyla aşağıdaki işlemleri gerçekleştirir:

- Videoyu sıkıştırır
- Videoyu Azure Media Services varlığına yükler
- Videoyu denetlemek için bir AMS işi oluşturur
- Content Moderator'da bir video incelemesi oluşturur

Aşağıdaki bölümlerde `ProcessVideo()` tarafından çağrılan tek tek işlemler biraz daha ayrıntılı incelenecektir. 

## <a name="compress-the-video"></a>Videoyu sıkıştırın

Ağ trafiğini en aza indirmek için, uygulama video dosyalarını H.264 (MPEG-4 AVC) biçimine dönüştürür ve en çok 640 piksel genişliğe ölçeklendirir. Yüksek verimliliği (sıkıştırma oranı) nedeniyle H.264 codec bileşeni önerilir. Sıkıştırma, Visual Studio çözümünün `Lib` klasörüne eklenmiş olan ücretsiz `ffmpeg` komut satırı aracı kullanılarak yapılır. Giriş dosyaları, en yaygın kullanılan video dosyası biçimleri ve codec bileşenleri de dahil olmak üzere `ffmpeg` tarafından desteklenen herhangi bir biçimde olabilir.

> [!NOTE]
> Komut satırı seçeneklerini kullanarak programı başlattığınızda, denetim için gönderilecek vide dosyalarını içeren dizini belirtirsiniz. Bu dizinde yer alan ve `.mp4` dosya adı uzantısına sahip olan dosyaların tümü işlenir. Diğer dosya adı uzantılarını işlemek için, `Program.cs` dosyasında `Main()` yöntemini istenen uzantıları içerecek şekilde güncelleştirin.

Tek video dosyasını sıkıştıran kod `AMSComponent.cs` içinde `AmsComponent` sınıfıdır. Bu işlevden sorumlu olan yöntem, burada gösterilen `CompressVideo()` yöntemidir.

[!code-csharp[CompressVideo](~/VideoReviewConsoleApp/Microsoft.ContentModerator.AMSComponent/AMSComponentClient/AMSComponent.cs?range=31-59)]

Kod aşağıdaki adımları gerçekleştirir:

- `App.config` dosyasındaki yapılandırmayı denetleyerek tüm gerekli verileri içerdiğinden emin olur
- `ffmpeg` ikilisini denetleyerek var olduğundan emin olur
- `_c.mp4` öğesini temel dosya adına ekleyerek çıkış dosya adını oluşturur (örneğin, `Example.mp4` -> `Example_c.mp4`)
- Dönüştürmeyi gerçekleştirmek için bir komut satırı dizesi oluşturur
- Komut satırını kullanarak bir `ffmpeg` işlemi başlatır
- Videonun işlenmesini bekler

> [!NOTE]
> Videolarınızın zaten H.264 kullanılarak sıkıştırıldığını ve uygun boyutlara sahip olduğunu biliyorsanız, sıkıştırmayı atlamak için `CompressVideo()` yöntemini yeniden yazabilirsiniz.

Bu yöntem sıkıştırılmış çıkış dosyasının adını döndürür.

## <a name="upload-and-moderate-the-video"></a>Videoyu yükleme ve orta

Videonun Content Moderation hizmeti tarafından işlenebilmesi için önce Azure Media Services'de depolanması gerekir. `Program.cs` içindeki `Program` sınıfının, videoyu karşıya yüklemek için kullanılan akış isteğini temsil eden bir nesnenin döndürüldüğü kısa bir `CreateVideoStreamingRequest()` yöntemi vardır.

[!code-csharp[CreateVideoStreamingRequest](~/VideoReviewConsoleApp/Microsoft.ContentModerator.AMSComponent/AMSComponentClient/Program.cs?range=120-133)]

Sonuçta elde edilen `UploadVideoStreamRequest` nesnesi `UploadVideoStreamRequest.cs` içinde tanımlanır (ve üst öğesi olan `UploadVideoRequest` de `UploadVideoRequest.cs` içinde). Bu sınıflar burada gösterilmiyor; bunlar kısadır ve yalnızca sıkıştırılmış video verilerini ve onlar hakkındaki bilgileri barındırma işine yarar. Bir diğer salt veri sınıfı olan `UploadAssetResult` ise (`UploadAssetResult.cs`) karşıya yükleme işleminin sonuçlarını barındırır. Artık, `ProcessVideo()` içindeki şu satırları anlamak mümkün olacaktır:

[!code-csharp[ProcessVideoSnippet](~/VideoReviewConsoleApp/Microsoft.ContentModerator.AMSComponent/AMSComponentClient/Program.cs?range=91-104)]

Bu satırlar aşağıdaki görevleri gerçekleştirir:

- Sıkıştırılmış videoyu karşıya yüklemek için bir `UploadVideoStreamRequest` oluşturma
- Kullanıcı metin transkripti istediyse isteğin `GenerateVTT` bayrağını ayarlama
- Karşıya yüklemeyi gerçekleştirmek ve sonucu almak için `CreateAzureMediaServicesJobToModerateVideo()` çağrısı yapma

## <a name="examine-video-moderation-code"></a>Video moderasyon kodunu inceleme

`CreateAzureMediaServicesJobToModerateVideo()` yöntemi, Azure Media Services ile etkileşim kuran kodun büyük bölümünün yer aldığı `VideoModerator.cs` içindedir. Yöntemin kaynak kodu aşağıdaki alıntıda gösterilir.

[!code-csharp[CreateAzureMediaServicesJobToModerateVideo](~/VideoReviewConsoleApp/Microsoft.ContentModerator.AMSComponent/AMSComponentClient/VideoModerator.cs?range=230-283)]

Bu kod aşağıdaki görevleri gerçekleştirir:

- İşlemenin yapılması için bir AMS işi oluşturur
- Video dosyasını kodlama, denetleme ve metin transkripti oluşturma için görevler ekler
- Dosyayı karşıya yükleyerek ve işlemeye başlayarak işi gönderir
- Denetleme sonuçlarını, metin transkriptini (istendiyse) ve diğer bilgileri alır

## <a name="sample-video-moderation-output"></a>Örnek video moderasyon çıktısı

Video denetimi işinin sonucu (Bkz. [video denetimine hızlı başlangıç](video-moderation-api.md)), denetim sonuçlarını içeren bir JSON veri yapısıdır. Bu sonuçlarda, inceleme için bayrak eklenmiş anahtar karelerle olayları (klipleri) içeren video içindeki parçaların (çekimlerin) dökümü yer alır. Her anahtar kare, yetişkinlere yönelik veya müstehcen içerik bulundurma olasılığına göre puanlanır. Aşağıdaki örnekte JSON yanıtı gösterilir:

```json
{
    "version": 2,
    "timescale": 90000,
    "offset": 0,
    "framerate": 50,
    "width": 1280,
    "height": 720,
    "totalDuration": 18696321,
    "fragments": [
    {
        "start": 0,
        "duration": 18000
    },
    {
        "start": 18000,
        "duration": 3600,
        "interval": 3600,
        "events": [
        [
        {
            "reviewRecommended": false,
            "adultScore": 0.00001,
            "racyScore": 0.03077,
            "index": 5,
            "timestamp": 18000,
            "shotIndex": 0
        }
        ]
    ]
    },
    {
        "start": 18386372,
        "duration": 119149,
        "interval": 119149,
        "events": [
        [
        {
            "reviewRecommended": true,
            "adultScore": 0.00000,
            "racyScore": 0.91902,
            "index": 5085,
            "timestamp": 18386372,
            "shotIndex": 62
        }
    ]
    ]
    }
]
}
```

`GenerateVTT` bayrağı ayarlandığında videodan ses transkripsiyonu da oluşturulur.

> [!NOTE]
> Konsol uygulaması, yüklenen videonun ses parçasından transkript oluşturmak için [Azure Media Indexer API'sini](https://docs.microsoft.com/azure/media-services/media-services-process-content-with-indexer2) kullanır. Sonuçlar WebVTT formatında sağlanmaktadır. Bu biçimle ilgili daha fazla bilgi için bkz. [Web Video Metin Parçaları Biçimi](https://developer.mozilla.org/docs/Web/API/WebVTT_API).

## <a name="create-a-human-review"></a>İnsan incelemesi oluşturma

Denetleme işlemi videodaki anahtar karelerin listesini ve ses parçalarının transkriptini döndürür. Sonraki adım, insan denetleyiciler için Content Moderator inceleme aracında bir inceleme oluşturmaktır. `Program.cs` dosyasında `ProcessVideo()` yöntemine döndüğünüzde, `CreateVideoReviewInContentModerator()` yöntemine yapılan çağrıyı görürsünüz. Bu yöntem `VideoReviewAPI.cs` içinde yer alan `videoReviewApi` sınıfındadır ve aşağıda gösterilmiştir.

[!code-csharp[CreateVideoReviewInContentModerator](~/VideoReviewConsoleApp/Microsoft.ContentModerator.AMSComponent/AMSComponentClient/VideoReviewAPI.cs?range=42-69)]

`CreateVideoReviewInContentModerator()` aşağıdaki görevleri gerçekleştirmek için başka bazı yöntemleri çağırır:

> [!NOTE]
> Konsol uygulaması küçük resimleri oluşturmak için [FFmpeg](https://ffmpeg.org/) kitaplığını kullanır. Bu küçük resimler (görüntüler), video denetimi çıkışındaki kare zaman damgalarına karşılık gelir.

|Görev|Yöntemler|Dosya|
|-|-|-|
|Videodan anahtar kareleri ayıklama ve bunların küçük resim görüntülerini oluşturma|`CreateVideoFrames()`<br>`GenerateFrameImages()`|`FrameGeneratorServices.cs`|
|Yetişkinlere yönelik ve müstehcen sesi (varsa) bulmak için metin transkriptini tarama|`GenerateTextScreenProfanity()`| `VideoReviewAPI.cs`|
|İnsan incelemesi için video inceleme isteğini hazırlama ve gönderme|`CreateReviewRequestObject()`<br> `ExecuteCreateReviewApi()`<br>`CreateAndPublishReviewInContentModerator()`|`VideoReviewAPI.cs`|

Aşağıdaki ekranda önceki adımların sonuçları gösterilir.

![Video incelemesi varsayılan görünümü](images/video-tutorial-default-view.PNG)

## <a name="process-the-transcript"></a>Transkripti işleme

Şimdiye kadar, bu öğreticide tanıtılan kod görsel içeriğe odaklanmıştı. Konuşma içeriğinin incelenmesi ayrı ve isteğe bağlı bir işlemdir ve daha önce belirtildiği gibi sesten oluşturulan transkripti kullanır. Artık metin transkriptlerinin nasıl oluşturulduğunu ve inceleme işleminde nasıl kullanıldığını gözden geçirmenin zamanı geldi. Transkript oluşturma görevi, [Azure Media Indexer](https://docs.microsoft.com/azure/media-services/media-services-index-content) hizmetinin kapsamına girer.

Uygulama aşağıdaki görevleri gerçekleştirir:

|Görev|Yöntemler|Dosya|
|-|-|-|
|Metin transkriptlerinin oluşturulup oluşturulmayacağını belirleme|`Main()`<br>`GetUserInputs()`|`Program.cs`|
|Oluşturulacaksa, denetimin bir parçası olarak transkripsiyon işini gönderme|`ConfigureTranscriptTask()`|`VideoModerator.cs`|
|Transkriptin yerel kopyasını alma|`GenerateTranscript()`|`VideoModerator.cs`|
|Uygunsuz ses içeren video karelerine bayrak ekleme|`GenerateTextScreenProfanity()`<br>`TextScreen()`|`VideoReviewAPI.cs`|
|Sonuçları incelemeye ekleme|`UploadScreenTextResult()`<br>`ExecuteAddTranscriptSupportFile()`|`VideoReviewAPI.cs`|

### <a name="task-configuration"></a>Görev yapılandırması

Şimdi doğrudan transkripsiyon işini gönderme işlemine geçelim. `CreateAzureMediaServicesJobToModerateVideo()` (daha önce açıklanmıştı) `ConfigureTranscriptTask()` yöntemini çağırır.

[!code-csharp[ConfigureTranscriptTask](~/VideoReviewConsoleApp/Microsoft.ContentModerator.AMSComponent/AMSComponentClient/VideoModerator.cs?range=295-304)]

Transkript görevinin yapılandırması, çözümün `Lib` klasöründe yer alan `MediaIndexerConfig.json` dosyasından okunur. Yapılandırma dosyası için ve transkripsiyon işleminin çıkışı için AMS varlıkları oluşturulur. AMS işi çalıştırıldığında, bu görev video dosyasının ses parçasından metin transkriptini oluşturur.

> [!NOTE]
> Örnek uygulama yalnızca ABD İngilizcesi konuşmaları tanır.

### <a name="transcript-generation"></a>Transkript oluşturma

Transkript bir AMS varlığı olarak yayımlanır. Transkripti uygunsuz içerik için taramak amacıyla, uygulama varlığı Azure Media Services'den indirir. Dosyayı almak için `CreateAzureMediaServicesJobToModerateVideo()` burada gösterilen `GenerateTranscript()` yöntemini çağırır.

[!code-csharp[GenerateTranscript](~/VideoReviewConsoleApp/Microsoft.ContentModerator.AMSComponent/AMSComponentClient/VideoModerator.cs?range=351-370)]

Bazı gerekli AMS kurulum işlemlerinden sonra, indirme işlemi AMS varlığını yerel dosyaya kopyalayan genel `DownloadAssetToLocal()` işlevi çağrılarak gerçekleştirilir.

## <a name="moderate-the-transcript"></a>Transkriptin ortası

Transkript elinizin altında olduğundan, taranır ve incelemede kullanılır. İncelemeyi oluşturmak `CreateVideoReviewInContentModerator()` yönteminin konusudur ve bu işi yapmak için `GenerateTextScreenProfanity()` yöntemini çağırır. Bu yöntem de işlevlerin çoğunu içeren `TextScreen()` yöntemini çağırır.

`TextScreen()` aşağıdaki görevleri gerçekleştirir:

- Zaman damgaları ve açıklamalı alt yazılar için transkripti ayrıştırma
- Her açıklamalı alt yazıyı metin denetimi için gönderme
- Uygunsuz konuşma içeriği bulunuyor olabilecek tüm karelere bayrak ekleme

Şimdi bu görevleri daha ayrıntılı inceleyelim:

### <a name="initialize-the-code"></a>Kodu başlatma

İlk olarak tüm değişkenleri ve koleksiyonları başlatın.

[!code-csharp[TextScreen](~/VideoReviewConsoleApp/Microsoft.ContentModerator.AMSComponent/AMSComponentClient/VideoReviewAPI.cs?range=515-527)]

### <a name="parse-the-transcript-for-captions"></a>Açıklamalı alt yazılar için transkripti ayrıştırma

Ardından, VTT biçimindeki transkripti açıklamalı alt yazılar ve zaman damgaları için ayrıştırın. İnceleme aracı, video inceleme ekranının Transkript Sekmesinde bu açıklamalı alt yazıları görüntüler. Zaman damgaları açıklamalı alt yazıları ilgili video kareleriyle eşleştirmekte kullanılır.

[!code-csharp[TextScreen2](~/VideoReviewConsoleApp/Microsoft.ContentModerator.AMSComponent/AMSComponentClient/VideoReviewAPI.cs?range=528-567)]

### <a name="moderate-captions-with-the-text-moderation-service"></a>Metin denetimi hizmetiyle açıklamalı alt yazıları denetleme

Ardından, ayrıştırılmış metin açıklamalı alt yazıları Content Moderator'ın metin API'siyle tararız.

> [!NOTE]
> Content Moderator hizmet anahtarınızın saniyede istek sayısı (RPS) hız sınırı vardır. Sınırı aşarsanız, SDK 429 hata koduyla bir özel durum oluşturulur.
>
> Ücretsiz katmanı anahtarı bir RPS’lik hız sınırına sahiptir.

[!code-csharp[TextScreen3](~/VideoReviewConsoleApp/Microsoft.ContentModerator.AMSComponent/AMSComponentClient/VideoReviewAPI.cs?range=568-653)]

### <a name="text-moderation-breakdown"></a>Metin moderasyonu dökümü

`TextScreen()` önemli bir yöntemdir, bu nedenle şimdi onu bölümlerine ayıralım.

1. İlk olarak, yöntem transkript dosyasını satır satır okur. Boş satırları ve güvenilirlik puanı ile `NOTE` içeren satırları yoksayar. Dosyadaki *ipuçlarından* zaman damgalarını ve metin öğelerini ayıklar. İpucu, ses parçasındaki metinleri temsi eder ve başlangıç ile bitiş saatlerini içerir. İpucu, `-->` dizesini içeren zaman damgası satırıyla başlar. Bunu bir veya birden çok metin satırı izler.

1. Her ipucundan ayıklanan bilgileri tutmak için `CaptionScreentextResult` örnekleri (`TranscriptProfanity.cs` içinde tanımlanır) kullanılır.  Yeni bir zaman damgası satırı algılandığında veya metin uzunluğu üst sınırı olan 1024 karaktere ulaşıldığında, `csrList` içine yeni bir `CaptionScreentextResult` eklenir. 

1. Yöntem bundan sonra her ipucunu Metin Denetimi API'sine gönderir `Microsoft.Azure.CognitiveServices.ContentModerator` derlemesinde tanımlanan hem `ContentModeratorClient.TextModeration.DetectLanguageAsync()` hem de `ContentModeratorClient.TextModeration.ScreenTextWithHttpMessagesAsync()` yöntemini çağırır. Hız sınırlamasıyla karşılaşmayı önlemek için, yöntem her ipucunu göndermeden önce bir saniye duraklar.

1. Metin Denetimi hizmetinden sonuçları aldıktan sonra, yöntem güvenilirlik eşiklerine uyum uymadığını görmek için bunları analiz eder. Bu değerler `App.config` dosyasında `OffensiveTextThreshold`, `RacyTextThreshold` ve `AdultTextThreshold` olarak oluşturulur. Son olarak, uygunsuz terimler de depolanır. İpucunun zaman aralığı içinde kalan tüm kareler saldırgan, müstehcen ve/veya yetişkinlere yönelik metin içeriyor bayrağı ekler.

1. `TextScreen()`, bir bütün olarak videodan gelen metin denetimi sonucunu içeren bir `TranscriptScreenTextResult` örneği döndürür. Bu nesne çeşitli türlerdeki uygun içerik için bayraklar ve puanlar, ayrıca tüm uygunsuz terimlerin listesini içerir. Çağrıyı yapan (`CreateVideoReviewInContentModerator()`) incelemeyi yapan insanların kullanabilmesi için `UploadScreenTextResult()` çağrısı yaparak bu bilgiyi incelemeye ekler.

Aşağıdaki ekranda transkript oluşturma ve denetim adımlarının sonucu gösterilir.

![Video denetimi transkript görünümü](images/video-tutorial-transcript-view.PNG)

## <a name="program-output"></a>Program çıktısı

Programın aşağıdaki komut satırı çıkışı tamamlanan çeşitli görevleri gösterir. Buna ek olarak, özgün video dosyalarıyla aynı dizinde denetim sonucu (JSON biçiminde) ve konuşma transkripti de sağlanır.

```console
Microsoft.ContentModerator.AMSComponentClient
Enter the fully qualified local path for Uploading the video :
"Your File Name.MP4"
Generate Video Transcript? [y/n] : y

Video compression process started...
Video compression process completed...

Video moderation process started...
Video moderation process completed...

Video review process started...
Video Frames Creation inprogress...
Frames(83) created successfully.
Review Created Successfully and the review Id 201801va8ec2108d6e043229ba7a9e6373edec5
Video review successfully completed...

Total Elapsed Time: 00:05:56.8420355
```

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide, transkript içeriği&mdash;&mdash;de dahil olmak üzere video içeriğini denetleyen ve İnceleme aracında incelemeler oluşturan bir uygulama ayarlarsınız. Ardından, video moderasyonunun ayrıntıları hakkında daha fazla bilgi edinin.

> [!div class="nextstepaction"]
> [Video denetimi](./video-moderation-human-review.md)
