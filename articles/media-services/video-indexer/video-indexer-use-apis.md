---
title: Video Indexer API'sini kullanma
titleSuffix: Azure Media Services
description: Bu makalede, Azure Medya Hizmetleri Video Dizinleyici API ile nasıl başlatılanın açıklanmaktadır.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 04/13/2020
ms.author: juliako
ms.openlocfilehash: 82bdb177cf4d9c400d1b13ba7178658089950557
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/14/2020
ms.locfileid: "81314345"
---
# <a name="tutorial-use-the-video-indexer-api"></a>Öğretici: Video Indexer API'sini kullanma

Video Indexer, Microsoft tarafından sunulan çeşitli ses ve video yapay zeka (AI) teknolojilerini tek bir entegre hizmette birleştirerek geliştirmeyi kolaylaştırır. API'ler, geliştiricilerin ölçek, küresel erişim, kullanılabilirlik ve bulut platformlarının güvenilirliği konusunda endişelenmeden Medya AI teknolojilerini tüketmeye odaklanmalarını sağlamak üzere tasarlanmıştır. Dosyalarınızı yüklemek, ayrıntılı video bilgileri almak, katıştırılabilir öngörüler ve oynatıcı widget'ları URL'leri almak ve daha fazlasını almak için API'yi kullanabilirsiniz.

Video Indexer hesabı oluştururken, ücretsiz bir deneme hesabı (belirli sayıda ücretsiz dizin oluşturma dakikası aldığınız) veya ücretli bir seçenek (kotayla sınırlı olmadığınız) seçebilirsiniz. Ücretsiz deneme sürümüyle Video Indexer, web sitesi kullanıcılarına 600 dakikaya kadar ücretsiz dizin oluşturma ve API kullanıcılarına 2400 dakikaya kadar ücretsiz dizin oluşturma sağlar. Ücretli bir seçenekle, [Azure aboneliğinize ve Azure Medya Hizmetleri hesabına bağlı bir](connect-to-azure.md)Video Dizinleyici hesabı oluşturursunuz. Dizin oluşturma faaliyeti yapılan dakika sayısının yanı sıra Azure Media Services hesabıyla ilgili ücretler için ödeme yaparsınız.

Bu makalede geliştiricilerin [Video Indexer API’sinden](https://api-portal.videoindexer.ai/) nasıl yararlanabileceği açıklanmaktadır.

## <a name="subscribe-to-the-api"></a>API’ye abone olma

1. [Video Indexer Geliştirici Portalı](https://api-portal.videoindexer.ai/)’nda oturum açın.
    
    ![Video Indexer Geliştirici Portalında Oturum Aç](./media/video-indexer-use-apis/video-indexer-api01.png)

   > [!Important]
   > * Video Indexer için kaydolurken kullandığınız sağlayıcıyı kullanmanız gerekir.
   > * Kişisel Google ve Microsoft (Outlook/Live) hesapları yalnızca deneme hesapları için kullanılabilir. Azure'a bağlı hesaplar için Azure Active Directory gerekir.
   > * E-posta başına yalnızca bir etkin hesap olabilir. Bir kullanıcı LinkedIn ve user@gmail.com daha sonra Google user@gmail.com için oturum açmaya çalışırsa, kullanıcının zaten var olduğunu söyleyerek bir hata sayfası görüntüler.

2. Abone olun.

    [Ürünler](https://api-portal.videoindexer.ai/products) sekmesini seçin. Ardından Yetkilendirme'yi seçin ve abone olun.
    
    ![Video Indexer Geliştirici Portalı'ndaki ürünler sekmesi](./media/video-indexer-use-apis/video-indexer-api02.png)

    > [!NOTE]
    > Yeni kullanıcılar otomatik olarak Yetkilendirme’ye abone edilir.
    
    Abone olduktan sonra aboneliğinizi ve birincil ve ikincil anahtarlarınızı görebilirsiniz. Anahtarlar korunmalıdır. Anahtarlar yalnızca sunucu kodunuz tarafından kullanılmalıdır. İstemci tarafında (.js, .html vb.) bulunmamalıdır.

    ![Video Indexer Geliştirici Portalı'nda abonelik ve anahtarlar](./media/video-indexer-use-apis/video-indexer-api03.png)

> [!TIP]
> Video Indexer kullanıcısı, tek bir abonelik anahtarını kullanarak birden çok Video Indexer hesabına bağlanabilir. Daha sonra bu Video Indexer hesaplarını farklı Media Services hesaplarına bağlayabilirsiniz.

## <a name="obtain-access-token-using-the-authorization-api"></a>Yetkilendirme API'sini kullanarak erişim belirtecini alma

Yetkilendirme API'sine abone olduktan sonra, erişim jetonları alabilirsiniz. Bu erişim belirteçleri, İşlemler API’sinde kimlik doğrulamak için kullanılır.

İşlemler API'sine yapılan her çağrı, çağrının yetkilendirme kapsamına uyan bir erişim belirteci ile ilişkilendirilmelidir.

- Kullanıcı düzeyi: Kullanıcı düzeyi erişim belirteçleri, **işlemleri kullanıcı** düzeyinde gerçekleştirmenize izin vermez. Örnek: ilişkili hesaplar alma.
- Hesap düzeyi: Hesap düzeyi erişim belirteçleri, **hesap** düzeyinde veya **video** düzeyinde işlem gerçekleştirmenize izin verebiliyor. Örneğin, video yükleyin, tüm videoları listeleyebilir, video istatistikleri edinin ve benzeri.
- Video düzeyi: Video düzeyi erişim belirteçleri belirli bir **video**üzerinde işlemleri gerçekleştirmenize izin. Örneğin, video istatistikleri, altyazı indirme, widget'lar ve benzeri bilgiler edinin.

Bu belirteçlerin salt okunup okunmadığını veya düzenlemeye izin verilip vermediklerini **allowEdit=true/false**'u belirterek denetleyebilirsiniz.

Sunucudan sunucuya senaryoların çoğunda, hem **hesap** işlemlerini hem de **video** işlemlerini kapsadığı için büyük olasılıkla aynı **hesap** belirteci kullanırsınız. Ancak, Video Indexer'a (örneğin JavaScript'ten) istemci tarafı aramaları yapmayı planlıyorsanız, istemcilerin tüm hesaba erişmesini önlemek için bir **video** erişim jetonu kullanmak isteyebilirsiniz. Bu aynı zamanda, istemcinize Video Indexer istemci kodunu katıştırırken (örneğin, Öngörüler Widget'ı edinin veya **Player Widget'ı** **Edinin'i** kullanarak) **bir video** erişim jetonu sağlamanız gerekir.

İşleri kolaylaştırmak için **Yetkilendirme** API’si > **GetAccounts**’u kullanarak ilk önce bir kullanıcı belirteci almaya gerek kalmadan hesaplarınızı alabilirsiniz. Ayrıca, hesapları geçerli belirteçlerle almayı da isteyebilirsiniz. Böylece hesap belirteci almak için yapılacak ek bir çağrıyı atlayabilirsiniz.

Erişim belirteçlerinin süresi 1 saatte dolar. İşlemler API'sini kullanmadan önce erişim belirtecinizin geçerli olduğundan emin olun. Süresi dolacaksa, yeni bir erişim belirteci almak için Yetkilendirme API'sini yeniden arayın.

API ile tümleştirmeye başlamaya hazırsınız. [Her bir Video Indexer REST API’nin ayrıntılı açıklamasına](https://api-portal.videoindexer.ai/) bakabilirsiniz.

## <a name="account-id"></a>Hesap Kimliği

Hesap Kimliği parametresi, işleme yönelik tüm API çağrıları için gereklidir. Hesap Kimliği, aşağıdaki yollardan biriyle alınabilen bir GUID'dir:

* Hesap Kimliğini almak için **Video Indexer web sitesini** kullanın:

    1. [Video Indexer](https://www.videoindexer.ai/) web sitesine gidip oturum açın.
    2. **Ayarlar** sayfasına gidin.
    3. Hesap kimliğini kopyalayın.

        ![Video Dizinleyici ayarları ve hesap kimliği](./media/video-indexer-use-apis/account-id.png)

* Hesap Kimliğini programlı olarak almak için **Video Indexer Geliştirici Portalı**’nı kullanın.

    Hesap API'sini [Al'ı](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Get-Account?) kullanın.

    > [!TIP]
    > `generateAccessTokens=true` parametresini tanımlayarak hesaplar için erişim belirteçleri oluşturabilirsiniz.

* Hesap Kimliğini hesabınızdaki bir yürütücü sayfasının URL'sinden alın.

    Video izlerken kimlik, `accounts` bölümünden sonra ve `videos` bölümünden önce görünür.

    ```
    https://www.videoindexer.ai/accounts/00000000-f324-4385-b142-f77dacb0a368/videos/d45bf160b5/
    ```

## <a name="recommendations"></a>Öneriler

Bu bölümde, Video Indexer API kullanırken işinize yarayacak bazı öneriler sunulmaktadır.

- Bir video yüklemeyi planlıyorsanız, dosyayı genel ağ konumuna (örneğin, OneDrive) yerleştirmeniz önerilir. Videonun bağlantısını alın ve URL'yi karşıya dosya yükleme parametresi olarak kullanın.

    Video Indexer’a sağlanan URL bir medya dosyasına (ses veya video) yönlendirmelidir. OneDrive tarafından oluşturulan bağlantıların bazıları, dosyayı içeren bir HTML sayfası içindir. URL için kolay bir doğrulama, dosya indirmeye başlarsa, büyük olasılıkla iyi bir URL'dir. Tarayıcı bazı görselleştirme render ise, büyük olasılıkla bir dosya ya da bir HTML sayfasına bir bağlantı değil.

- Belirtilen video için video içgörüleri alan API’yi çağırdığınızda yanıt içeriği olarak ayrıntılı bir JSON çıktısını alırsınız. [Döndürülen JSON hakkındaki ayrıntıları bu konuda görebilirsiniz](video-indexer-output-json-v2.md).

## <a name="code-sample"></a>Kod örneği

Aşağıdaki C# kod parçacığı, tüm Video Indexer API'lerinin kullanımını bir arada göstermektedir.

```csharp
var apiUrl = "https://api.videoindexer.ai";
var accountId = "..."; 
var location = "westus2";
var apiKey = "..."; 

System.Net.ServicePointManager.SecurityProtocol = System.Net.ServicePointManager.SecurityProtocol | System.Net.SecurityProtocolType.Tls12;

// create the http client
var handler = new HttpClientHandler(); 
handler.AllowAutoRedirect = false; 
var client = new HttpClient(handler);
client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", apiKey); 

// obtain account access token
var accountAccessTokenRequestResult = client.GetAsync($"{apiUrl}/auth/{location}/Accounts/{accountId}/AccessToken?allowEdit=true").Result;
var accountAccessToken = accountAccessTokenRequestResult.Content.ReadAsStringAsync().Result.Replace("\"", "");

client.DefaultRequestHeaders.Remove("Ocp-Apim-Subscription-Key");

// upload a video
var content = new MultipartFormDataContent();
Debug.WriteLine("Uploading...");
// get the video from URL
var videoUrl = "VIDEO_URL"; // replace with the video URL

// as an alternative to specifying video URL, you can upload a file.
// remove the videoUrl parameter from the query string below and add the following lines:
  //FileStream video =File.OpenRead(Globals.VIDEOFILE_PATH);
  //byte[] buffer = new byte[video.Length];
  //video.Read(buffer, 0, buffer.Length);
  //content.Add(new ByteArrayContent(buffer));

var uploadRequestResult = client.PostAsync($"{apiUrl}/{location}/Accounts/{accountId}/Videos?accessToken={accountAccessToken}&name=some_name&description=some_description&privacy=private&partition=some_partition&videoUrl={videoUrl}", content).Result;
var uploadResult = uploadRequestResult.Content.ReadAsStringAsync().Result;

// get the video id from the upload result
var videoId = JsonConvert.DeserializeObject<dynamic>(uploadResult)["id"];
Debug.WriteLine("Uploaded");
Debug.WriteLine("Video ID: " + videoId);           

// obtain video access token
client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", apiKey);
var videoTokenRequestResult = client.GetAsync($"{apiUrl}/auth/{location}/Accounts/{accountId}/Videos/{videoId}/AccessToken?allowEdit=true").Result;
var videoAccessToken = videoTokenRequestResult.Content.ReadAsStringAsync().Result.Replace("\"", "");

client.DefaultRequestHeaders.Remove("Ocp-Apim-Subscription-Key");

// wait for the video index to finish
while (true)
{
  Thread.Sleep(10000);

  var videoGetIndexRequestResult = client.GetAsync($"{apiUrl}/{location}/Accounts/{accountId}/Videos/{videoId}/Index?accessToken={videoAccessToken}&language=English").Result;
  var videoGetIndexResult = videoGetIndexRequestResult.Content.ReadAsStringAsync().Result;

  var processingState = JsonConvert.DeserializeObject<dynamic>(videoGetIndexResult)["state"];

  Debug.WriteLine("");
  Debug.WriteLine("State:");
  Debug.WriteLine(processingState);

  // job is finished
  if (processingState != "Uploaded" && processingState != "Processing")
  {
      Debug.WriteLine("");
      Debug.WriteLine("Full JSON:");
      Debug.WriteLine(videoGetIndexResult);
      break;
  }
}

// search for the video
var searchRequestResult = client.GetAsync($"{apiUrl}/{location}/Accounts/{accountId}/Videos/Search?accessToken={accountAccessToken}&id={videoId}").Result;
var searchResult = searchRequestResult.Content.ReadAsStringAsync().Result;
Debug.WriteLine("");
Debug.WriteLine("Search:");
Debug.WriteLine(searchResult);

// get insights widget url
var insightsWidgetRequestResult = client.GetAsync($"{apiUrl}/{location}/Accounts/{accountId}/Videos/{videoId}/InsightsWidget?accessToken={videoAccessToken}&widgetType=Keywords&allowEdit=true").Result;
var insightsWidgetLink = insightsWidgetRequestResult.Headers.Location;
Debug.WriteLine("Insights Widget url:");
Debug.WriteLine(insightsWidgetLink);

// get player widget url
var playerWidgetRequestResult = client.GetAsync($"{apiUrl}/{location}/Accounts/{accountId}/Videos/{videoId}/PlayerWidget?accessToken={videoAccessToken}").Result;
var playerWidgetLink = playerWidgetRequestResult.Headers.Location;
Debug.WriteLine("");
Debug.WriteLine("Player Widget url:");
Debug.WriteLine(playerWidgetLink);

```

## <a name="see-also"></a>Ayrıca bkz.

- [Video Indexer’a genel bakış](video-indexer-overview.md)
- [Bölgeler](https://azure.microsoft.com/global-infrastructure/services/?products=cognitive-services)

## <a name="next-steps"></a>Sonraki adımlar

- [Çıktı JSON ayrıntılarını inceleyin](video-indexer-output-json-v2.md)
- Video yüklemenin ve dizine eklenmenin önemli yönünü gösteren [örnek koda](https://github.com/Azure-Samples/media-services-video-indexer) göz atın. Kod wil'i takip etmek, TEMEL işlevler için API'mizi nasıl kullanacağınız hakkında iyi bir fikir verir. Satır satırlı yorumları okuduğunuzdan emin olun ve en iyi uygulama tavsiyelerimize dikkat edin.

