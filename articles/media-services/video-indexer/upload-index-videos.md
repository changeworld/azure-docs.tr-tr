---
title: Video Indexer ile videoları karşıya yükleme ve dizinleme
titleSuffix: Azure Media Services
description: Bu konuda, API'lerı kullanarak Video Indexer ile videolarınızı karşıya yükleme ve dizinleme gösterilmektedir.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 02/18/2020
ms.author: juliako
ms.openlocfilehash: 245eabdf4d77682c87062c2581239a554112d748
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77468771"
---
# <a name="upload-and-index-your-videos"></a>Videolarınızı karşıya yükleme ve dizinleme  

Video Indexer API ile video yüklerken aşağıdaki yükleme seçeneklerine sahipsiniz: 

* Videonuzu bir URL'den karşıya yükleyin (tercih edilir).
* istek gövdesinde bir bayt dizisi olarak video dosyası göndermek,
* [Varlık kimliğini](https://docs.microsoft.com/azure/media-services/latest/assets-concept) sağlayarak varolan Azure Medya Hizmetleri varlığını kullanın (yalnızca ücretli hesaplarda desteklenir).

Videonuz yüklendikten sonra Video Indexer (isteğe bağlı olarak) videoyu kodlar (makalede tartışılır). Video Indexer hesabınızı oluştururken ücretsiz bir deneme hesabı (belirli sayıda ücretsiz dizin oluşturma dakikası elde edersiniz) veya ücretli bir seçenek (kota sınırlaması olmaz) arasından seçim yapabilirsiniz. Ücretsiz deneme kullanıldığında Video Indexer, web sitesi kullanıcılarına 600 dakikaya kadar ve API kullanıcılarına ise 2400 dakikaya kadar ücretsiz dizin oluşturma olanağı sunar. Ücretli seçeneğiyle, [Azure aboneliğinize ve Azure Medya Hizmetleri hesabına bağlı bir](connect-to-azure.md)Video Dizinleyici hesabı oluşturursunuz. Dizin oluşturma faaliyeti yapılan dakika sayısının yanı sıra Medya Hesabı ile ilgili ücretler için ödeme yaparsınız. 

Makale, videolarınızı şu seçeneklerle nasıl yükleyip dizine ekleyip dizine eklediğinizi gösterir:

* [Video Indexer web sitesi](#website) 
* [Video Dizinleyici API'ler](#apis)

## <a name="uploading-considerations-and-limitations"></a>Dikkatleri ve sınırlamaları yükleme
 
- Videonun adı 80 karakterden büyük olmamalıdır.
- Videonuzu URL'ye (tercih edilen) yüklerken bitiş noktası TLS 1,2 (veya daha yüksek) ile güvence altına alınmalıdır.
- URL seçeneği ile yükleme boyutu 30GB ile sınırlıdır.
- İstek URL uzunluğu, sorgu dizesi URL uzunluğunun 4096 karakterle sınırlı olduğu 6144 karakterle sınırlıdır.
- Bayt dizi seçeneği ile yükleme boyutu 2GB ile sınırlıdır.
- Bayt dizisi seçeneği 30 dk sonra zaman ları dışarı.
- Param'da `videoURL` sağlanan URL'nin kodlanması gerekir.
- Medya Hizmetleri varlıklarını dizine alma, URL'den dizin oluşturmayla aynı sınırlamaya sahiptir.
- Video Dizinleyici, tek bir dosya için maksimum süre sınırına 4 saat tir.
- URL'nin erişilebilir olması gerekir (örneğin, genel bir URL). 

    Özel bir URL ise, erişim belirteci istek sağlanmalıdır.
- URL' nin geçerli bir medya dosyasını değil, `www.youtube.com` sayfaya bağlantı gibi bir web sayfasını işaret etmesi gerekiyor.
- Ücretli bir hesapta dakikada en fazla 50 film ve bir deneme hesabında dakikada 5 film yükleyebilirsiniz.

> [!Tip]
> .NET Framework 4.6.2 veya üzeri bir sürümünü kullanmanız önerilir. Eski .NET Framework sürümlerinde varsayılan olarak TLS 1.2 ayarı kullanılmaz.
>
> Eski .NET Framework sürümlerini kullanmanız gerekirse kodunuzda REST API çağrısı öncesine bir satır ekleyin:  <br/> System.Net.ServicePointManager.SecurityProtocol = SecurityProtocolType.Tls | SecurityProtocolType.Tls11 | SecurityProtocolType.Tls12;

## <a name="supported-file-formats-for-video-indexer"></a>Video Indexer için desteklenen dosya biçimleri

Video Indexer ile kullanabileceğiniz dosya biçimlerinin listesi için [giriş kapsayıcısı/dosya biçimleri](../latest/media-encoder-standard-formats.md#input-containerfile-formats) makalesine bakın.

## <a name="upload-and-index-a-video-using-the-video-indexer-website"></a><a id="website"/>Video Indexer web sitesini kullanarak video yükleme ve dizine

> [!NOTE]
> Videonun adı 80 karakterden büyük olmamalıdır.

1. [Video Indexer](https://www.videoindexer.ai/) web sitesinde oturum açın.
2. Karşıya video yüklemek için **Karşıya Yükle** düğme veya bağlantısına basın.

    ![Karşıya Yükle](./media/video-indexer-get-started/video-indexer-upload.png)

    Videonuz karşıya yüklendikten sonra Video Indexer videoyu dizinlemeye ve analiz etmeye başlar.

    ![Karşıya yüklendi](./media/video-indexer-get-started/video-indexer-uploaded.png) 

    Video Indexer analizi tamamladıktan sonra videonuzun bağlantısını ve videonuzda neler bulunduğunun kısa bir açıklamasını içeren bir bildirim alırsınız. Örnek: kişiler, konular, OCR’ler.

## <a name="upload-and-index-with-api"></a><a id="apis"/>API ile yükleme ve dizin

Videolarınızı bir URL'ye göre yüklemek ve dizine yüklemek için Video ApI'sini [yükleyin.](https://api-portal.videoindexer.ai/docs/services/operations/operations/Upload-video?) İzleyen kod örneği, bayt dizininin nasıl yüklendiğini gösteren yorumlanmış kodu içerir. 

### <a name="configurations-and-params"></a>Yapılandırmalar ve parametreler

Bu bölümde, isteğe bağlı parametrelerin bazıları ve ayarlanmalarının ne zaman gerekeceği açıklanmaktadır.

#### <a name="externalid"></a>externalID 

Bu parametre, video ile ilişkilendirilecek bir kimlik belirtmenize olanak sağlar. Bu kimlik, dış "Video İçerik Yönetimi" (VCM) sistemiyle tümleştirmede kullanılabilir. Video Indexer portalında bulunan videolar, belirtilen dış kimlik kullanılarak aranabilir.

#### <a name="callbackurl"></a>callbackUrl

Müşteriyi aşağıdaki olaylar hakkında bilgilendirmek (POST isteği kullanarak) için kullanılan bir URL:

- Durum değişikliğini dizine alma: 
    - Özellikler:    
    
        |Adı|Açıklama|
        |---|---|
        |id|Video kimliği|
        |durum|Video durumu|  
    - Örnek: https:\//test.com/notifyme?projectName=MyProject&id=1234abcd&durum=İşlenmiş
- Videoda tanımlanan kişi:
  - Özellikler
    
      |Adı|Açıklama|
      |---|---|
      |id| Video kimliği|
      |faceId|Video dizininde görünen yüz kimliği|
      |bilinen PersonId|Yüz modelinde benzersiz olan kişi kimliği|
      |kişiAdı|Kişinin adı|
        
    - Örnek: https:\//test.com/notifyme?projectName=MyProject&id=1234abcd&faceid=12&bilinenKişiId=CCA84350-89B7-4262-861C-3CAC796542A5&kişiAdı=Inigo_Montoya 

##### <a name="notes"></a>Notlar

- Video Indexer, özgün URL'de sağlanan varolan parametreleri döndürür.
- Sağlanan URL kodlanmalıdır.

#### <a name="indexingpreset"></a>indexingPreset

Ham veya dış kayıtlar arka plan gürültüsü içeriyorsa bu parametreyi kullanın. Bu parametre, dizinleme işlemini yapılandırmak için kullanılır. Aşağıdaki değerleri belirtebilirsiniz:

- `AudioOnly`: Yalnızca ses kullanarak (videoyu yok sayarak) öngörüler ayıklayın ve bunları dizinleyin
- `VideoOnly`- Yalnızca videoyu kullanarak dizin ve özü oluşturma bilgileri (sesi yok sayma)
- `Default`: Ses ve videoyu kullanarak öngörüler ayıklayın ve bunları dizinleyin
- `DefaultWithNoiseReduction`: Ses akışına gürültü azaltma algoritmaları uygulayarak ses ve videodan öngörüler ayıklayın ve bunları dizinleyin

> [!NOTE]
> Video Indexer ses iki parça kadar kapsar. Dosyada daha fazla ses parçası varsa, bunlar tek parça olarak kabul edilir.<br/>
Parçaları ayrı ayrı dizine almak istiyorsanız, ilgili ses dosyasını ayıklamanız ve `AudioOnly`dizine "' olarak dizine" oluşturmanız gerekir.

Fiyat, seçilen dizinleme seçeneğine bağlıdır.  

#### <a name="priority"></a>Öncelik

Videolar, Video Indexer tarafından önceliklerine göre dizine eklenir. Dizin önceliğini belirtmek için **öncelik** parametresini kullanın. Aşağıdaki değerler geçerlidir: **Düşük**, **Normal** (varsayılan) ve **Yüksek**.

**Öncelik** parametresi yalnızca ücretli hesaplar için desteklenir.

#### <a name="streamingpreset"></a>streamingPreset

Videonuz karşıya yüklendikten sonra Video Indexer, isteğe bağlı olarak videoyu kodlar. Video Indexer, ardından dizinlemeye ve videoyu analiz etmeye geçer. Video Indexer analizi tamamladığında video kimliğini içeren bir bildirim alırsınız.  

[Videoyu karşıya yükleme](https://api-portal.videoindexer.ai/docs/services/operations/operations/Upload-video?) veya [Videoyu Yeniden Dizinleme](https://api-portal.videoindexer.ai/docs/services/operations/operations/Re-index-video?) API'sini kullanırken isteğe bağlı parametrelerden biri de `streamingPreset` parametresidir. `streamingPreset` parametresini `Default`, `SingleBitrate` veya `AdaptiveBitrate` olarak ayarlarsanız kodlama işlemi tetiklenir. Dizinleme ve kodlama işleri tamamlandıktan sonra video yayımlanır. Böylece videonuzun akışını da yapabilirsiniz. Video akışı yapmak istediğiniz Akış Uç Noktası **Çalışıyor** durumunda olmalıdır.

Dizinleme ve kodlama işlerini çalıştırmak için [Video Indexer hesabınıza bağlı Azure Media Services hesabı](connect-to-azure.md) Ayrılmış Birimler gerektirir. Daha fazla bilgi için bkz. [Medya İşlemeyi Ölçeklendirme](https://docs.microsoft.com/azure/media-services/previous/media-services-scale-media-processing-overview). Bunlar işlem gücü kullanımı yoğun işler olduğundan S3 türü birimlerin kullanılması önemle tavsiye edilir. Ayrılmış birim sayısı, paralel olarak çalıştırılabilecek en fazla iş sayısını tanımlar. Önerilen temel kullanım 10 S3 ayrılmış birimdir. 

Videonuzu yalnızca dizinlemek istiyorsanız ve kodlamayacaksanız `streamingPreset` parametresini `NoStreaming` olarak ayarlayın.

#### <a name="videourl"></a>videoUrl

Dizinlenecek video/ses dosyasının URL'si. URL bir medya dosyasına yönlendirmelidir (HTML sayfaları desteklenmez). Dosya, URI'nin parçası olarak sunulan bir erişim belirteci tarafından korunabilir ve dosyayı sunan uç noktanın güvenliği TLS 1.2 veya üzeri bir sürümle sağlanmalıdır. URL’nin kodlanması gerekir. 

`videoUrl` belirtilmezse Video Indexer dosyayı çok parçalı/form gövde içeriği olarak geçirmenizi bekler.

### <a name="code-sample"></a>Kod örneği

Aşağıdaki C# kod parçacığı, tüm Video Indexer API'lerinin kullanımını bir arada göstermektedir.

#### <a name="instructions-for-running-this-code-sample"></a>Bu kod örneğini çalıştırma yönergeleri

Bu kodu geliştirme platformunuza kopyaladıktan sonra iki parametre sağlamanız gerekir: API Yönetimi kimlik doğrulama anahtarı ve video URL'si.

* API anahtarı – API anahtarı, Video Dizinleyici hesabınızda işlem gerçekleştirmek için bir erişim jetonu almanızı sağlayan kişisel API yönetim abonelik anahtarınızdır. 

    API anahtarınızı almak için şu akıştan geçin:

    * Gezinmehttps://api-portal.videoindexer.ai/
    * Oturum Aç
    * **Ürünler** -> **Yetkilendirme** -> **Yetkilendirme aboneliğine** gidin
    * Birincil **anahtarı** kopyalama
* Video URL' si – Dizine ekilecek video/ses dosyasının URL'si. URL bir medya dosyasına yönlendirmelidir (HTML sayfaları desteklenmez). Dosya, URI'nin parçası olarak sunulan bir erişim belirteci tarafından korunabilir ve dosyayı sunan uç noktanın güvenliği TLS 1.2 veya üzeri bir sürümle sağlanmalıdır. URL’nin kodlanması gerekir.

Kod örneğini başarıyla çalıştırmanın sonucu, sırasıyla yüklenen öngörüleri ve videoyu incelemenize olanak tanıyan bir kavrayış widget URL'si ve bir oynatıcı widget URL'si içerir. 


```csharp
public async Task Sample()
{
    var apiUrl = "https://api.videoindexer.ai";
    var apiKey = "..."; // replace with API key taken from https://aka.ms/viapi

    System.Net.ServicePointManager.SecurityProtocol =
        System.Net.ServicePointManager.SecurityProtocol | System.Net.SecurityProtocolType.Tls12;

    // create the http client
    var handler = new HttpClientHandler();
    handler.AllowAutoRedirect = false;
    var client = new HttpClient(handler);
    client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", apiKey);

    // obtain account information and access token
    string queryParams = CreateQueryString(
        new Dictionary<string, string>()
        {
            {"generateAccessTokens", "true"},
            {"allowEdit", "true"},
        });
    HttpResponseMessage result = await client.GetAsync($"{apiUrl}/auth/trial/Accounts?{queryParams}");
    var json = await result.Content.ReadAsStringAsync();
    var accounts = JsonConvert.DeserializeObject<AccountContractSlim[]>(json);
    
    // take the relevant account, here we simply take the first, 
    // you can also get the account via accounts.First(account => account.Id == <GUID>);
    var accountInfo = accounts.First();

    // we will use the access token from here on, no need for the apim key
    client.DefaultRequestHeaders.Remove("Ocp-Apim-Subscription-Key");

    // upload a video
    var content = new MultipartFormDataContent();
    Console.WriteLine("Uploading...");
    // get the video from URL
    var videoUrl = "VIDEO_URL"; // replace with the video URL

    // as an alternative to specifying video URL, you can upload a file.
    // remove the videoUrl parameter from the query params below and add the following lines:
    //FileStream video =File.OpenRead(Globals.VIDEOFILE_PATH);
    //byte[] buffer =new byte[video.Length];
    //video.Read(buffer, 0, buffer.Length);
    //content.Add(new ByteArrayContent(buffer));

    queryParams = CreateQueryString(
        new Dictionary<string, string>()
        {
            {"accessToken", accountInfo.AccessToken},
            {"name", "video_name"},
            {"description", "video_description"},
            {"privacy", "private"},
            {"partition", "partition"},
            {"videoUrl", videoUrl},
        });
    var uploadRequestResult = await client.PostAsync($"{apiUrl}/{accountInfo.Location}/Accounts/{accountInfo.Id}/Videos?{queryParams}", content);
    var uploadResult = await uploadRequestResult.Content.ReadAsStringAsync();

    // get the video ID from the upload result
    string videoId = JsonConvert.DeserializeObject<dynamic>(uploadResult)["id"];
    Console.WriteLine("Uploaded");
    Console.WriteLine("Video ID:");
    Console.WriteLine(videoId);

    // wait for the video index to finish
    while (true)
    {
        await Task.Delay(10000);

        queryParams = CreateQueryString(
            new Dictionary<string, string>()
            {
                {"accessToken", accountInfo.AccessToken},
                {"language", "English"},
            });

        var videoGetIndexRequestResult = await client.GetAsync($"{apiUrl}/{accountInfo.Location}/Accounts/{accountInfo.Id}/Videos/{videoId}/Index?{queryParams}");
        var videoGetIndexResult = await videoGetIndexRequestResult.Content.ReadAsStringAsync();

        string processingState = JsonConvert.DeserializeObject<dynamic>(videoGetIndexResult)["state"];

        Console.WriteLine("");
        Console.WriteLine("State:");
        Console.WriteLine(processingState);

        // job is finished
        if (processingState != "Uploaded" && processingState != "Processing")
        {
            Console.WriteLine("");
            Console.WriteLine("Full JSON:");
            Console.WriteLine(videoGetIndexResult);
            break;
        }
    }

    // search for the video
    queryParams = CreateQueryString(
        new Dictionary<string, string>()
        {
            {"accessToken", accountInfo.AccessToken},
            {"id", videoId},
        });

    var searchRequestResult = await client.GetAsync($"{apiUrl}/{accountInfo.Location}/Accounts/{accountInfo.Id}/Videos/Search?{queryParams}");
    var searchResult = await searchRequestResult.Content.ReadAsStringAsync();
    Console.WriteLine("");
    Console.WriteLine("Search:");
    Console.WriteLine(searchResult);

    // Generate video access token (used for get widget calls)
    client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", apiKey);
    var videoTokenRequestResult = await client.GetAsync($"{apiUrl}/auth/{accountInfo.Location}/Accounts/{accountInfo.Id}/Videos/{videoId}/AccessToken?allowEdit=true");
    var videoAccessToken = (await videoTokenRequestResult.Content.ReadAsStringAsync()).Replace("\"", "");
    client.DefaultRequestHeaders.Remove("Ocp-Apim-Subscription-Key");

    // get insights widget url
    queryParams = CreateQueryString(
        new Dictionary<string, string>()
        {
            {"accessToken", videoAccessToken},
            {"widgetType", "Keywords"},
            {"allowEdit", "true"},
        });
    var insightsWidgetRequestResult = await client.GetAsync($"{apiUrl}/{accountInfo.Location}/Accounts/{accountInfo.Id}/Videos/{videoId}/InsightsWidget?{queryParams}");
    var insightsWidgetLink = insightsWidgetRequestResult.Headers.Location;
    Console.WriteLine("Insights Widget url:");
    Console.WriteLine(insightsWidgetLink);

    // get player widget url
    queryParams = CreateQueryString(
        new Dictionary<string, string>()
        {
            {"accessToken", videoAccessToken},
        });
    var playerWidgetRequestResult = await client.GetAsync($"{apiUrl}/{accountInfo.Location}/Accounts/{accountInfo.Id}/Videos/{videoId}/PlayerWidget?{queryParams}");
    var playerWidgetLink = playerWidgetRequestResult.Headers.Location;
     Console.WriteLine("");
     Console.WriteLine("Player Widget url:");
     Console.WriteLine(playerWidgetLink);
     Console.WriteLine("\nPress Enter to exit...");
     String line = Console.ReadLine();
     if (line == "enter")
     {
         System.Environment.Exit(0);
     }

}

private string CreateQueryString(IDictionary<string, string> parameters)
{
    var queryParameters = HttpUtility.ParseQueryString(string.Empty);
    foreach (var parameter in parameters)
    {
        queryParameters[parameter.Key] = parameter.Value;
    }

    return queryParameters.ToString();
}

public class AccountContractSlim
{
    public Guid Id { get; set; }
    public string Name { get; set; }
    public string Location { get; set; }
    public string AccountType { get; set; }
    public string Url { get; set; }
    public string AccessToken { get; set; }
}
```

### <a name="common-errors"></a>Sık karşılaşılan hatalar

Upload işlemi aşağıdaki tabloda listelenen durum kodlarını döndürebilir.

|Durum kodu|ErrorType (yanıt gövdesinde)|Açıklama|
|---|---|---|
|409|VIDEO_INDEXING_IN_PROGRESS|Bu video zaten aynı hesapta işleniyor.|
|400|VIDEO_ALREADY_FAILED|Bu videonun işlenmesi 2 saatten daha kısa bir süre önce aynı hesapta başarısız oldu. API istemcilerin videoyu yeniden yüklemek için en az 2 saat beklemesi gerekir.|
|429||Deneme hesaplarına dakikada 5 yükleme izni verilir. Ücretli hesaplara dakikada 50 yükleme izni verilir.|

## <a name="next-steps"></a>Sonraki adımlar

[API tarafından üretilen Azure Video Dizinleyici çıktısını inceleyin](video-indexer-output-json-v2.md)
