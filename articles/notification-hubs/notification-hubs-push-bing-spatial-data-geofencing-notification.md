---
title: Azure Bildirim Hub'ları ve Bing Uzamsal Verileri ile anında iletme bildirimleri gönderme | Microsoft Dokümanlar
description: Bu öğreticide, Azure Notification Hubs ve Bing Uzamsal Veri ile konum temelli anında iletme bildirimleri göndermeyi öğreneceksiniz.
services: notification-hubs
documentationcenter: windows
keywords: push bildirimleri, push bildirimleri
author: sethmanheim
manager: femila
editor: jwargo
ms.assetid: f41beea1-0d62-4418-9ffc-c9d70607a1b7
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows-phone
ms.devlang: dotnet
ms.topic: tutorial
ms.custom: mvc
ms.date: 01/04/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 01/04/2019
ms.openlocfilehash: 9f8697332d34fc044c805db6c6595fdf067ed054
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/24/2020
ms.locfileid: "80127198"
---
# <a name="tutorial-send-location-based-push-notifications-with-notification-hubs-and-bing-spatial-data"></a>Öğretici: Bildirim Hub'ları ve Bing Uzamsal Verileri yle konum tabanlı anında iletme bildirimleri gönderme

Bu öğreticide, Azure Notification Hubs ve Bing Uzamsal Veri ile konum temelli anında iletme bildirimleri göndermeyi öğreneceksiniz.

Bu öğreticide, aşağıdaki adımları gerçekleştireceksiniz:

> [!div class="checklist"]
> * Veri kaynağını ayarlama
> * UWP uygulamasını ayarlama
> * Arka ucu ayarlama
> * Evrensel Windows Platformu (UWP) uygulamasında anında iletme bildirimlerini test etme

## <a name="prerequisites"></a>Ön koşullar

* **Azure aboneliği.** Azure aboneliğiniz yoksa, başlamadan önce [ücretsiz bir Azure hesabı oluşturun.](https://azure.microsoft.com/free/)
* [Visual Studio 2015 Güncelleştirme 1](https://www.visualstudio.com/downloads/download-visual-studio-vs.aspx) veya üzeri ([Community Edition](https://go.microsoft.com/fwlink/?LinkId=691978&clcid=0x409)).
* [Azure SDK](https://azure.microsoft.com/downloads/)'nın en son sürümü.
* [Bing Haritalar Geliştirme Merkezi hesabı](https://www.bingmapsportal.com/) (ücretsiz bir tane oluşturabilir ve Microsoft hesabınızla ilişkilendirebilirsiniz).

## <a name="set-up-the-data-source"></a>Veri kaynağını ayarlama

1. [Bing Haritalar Geliştirme Merkezi](https://www.bingmapsportal.com/)'nde oturum açın.
2. Üst gezinti çubuğunda **Veri kaynakları**'nı ve **Veri Kaynaklarını Yönet**'i seçin.

    ![](./media/notification-hubs-geofence/bing-maps-manage-data.png)
3. Bir veri kaynağınız yoksa, veri kaynağı oluşturma bağlantısını görürsünüz. **Veri kaynağı olarak yükle**’yi seçin. **Veri kaynakları** > **Yükleme veri** menüsünü de kullanabilirsiniz.

    ![](./media/notification-hubs-geofence/bing-maps-create-data.png)
4. Aşağıdaki içeriği `NotificationHubsGeofence.pipe` içeren sabit diskinizde bir dosya oluşturun: Bu öğreticide, San Francisco rıhtımının bir alanını çerçeveleyen örnek bir boru tabanlı dosya kullanırsınız:

    ```text
    Bing Spatial Data Services, 1.0, TestBoundaries
    EntityID(Edm.String,primaryKey)|Name(Edm.String)|Longitude(Edm.Double)|Latitude(Edm.Double)|Boundary(Edm.Geography)
    1|SanFranciscoPier|||POLYGON ((-122.389825 37.776598,-122.389438 37.773087,-122.381885 37.771849,-122.382186 37.777022,-122.389825 37.776598))
    ```

    Kanal dosyası şu varlığı temsil eder:

    ![](./media/notification-hubs-geofence/bing-maps-geofence.png)
5. **Veri kaynağını karşıya yükleyin** sayfasında aşağıdaki eylemleri yapın:
   1. **Veri biçimi** için **kanal**’ı seçin.
   2. Önceki adımda `NotificationHubGeofence.pipe` oluşturduğunuz dosyaya göz atın ve seçin.
   3. **Karşıya Yükle** düğmesini seçin.

      > [!NOTE]
      > **Sorgu Anahtarı**'ndan farklı olan **Ana Anahtar** için yeni bir anahtar belirtmeniz istenebilir. Pano aracılığıyla yeni bir anahtar oluşturun ve veri kaynağını karşıya yükleme sayfasını yenileyin.
6. Veri dosyasını karşıya yükledikten sonra, veri kaynağını yayımladığınızdan emin olmanız gerekir. **Veri kaynaklarını** -> daha önce yaptığınız gibi**Veri Kaynaklarını Yönet'i** seçin.
7. Listeden veri kaynağınızı seçin ve **Eylemler** sütunundaki **Yayımla** seçeneğini belirleyin.

    ![](./media/notification-hubs-geofence/publish-button.png)
8. **Yayımlanmış Veri Kaynakları** sekmesine geçin ve veri kaynağınızı listede gördüğünüzü onaylayın.

    ![](./media/notification-hubs-geofence/bing-maps-published-data.png)
9. **Edit'i**seçin. Verilerde sunulan konumları (bir bakışta) göreceksiniz.

    ![](./media/notification-hubs-geofence/bing-maps-data-details.png)

    Bu noktada portal, oluşturduğunuz bölge sınırına ait sınırları göstermez. İhtiyacınız olan tek şey, belirtilen konumun doğru yakın çevrede olduğunun onaylanmasıdır.
10. Şimdi veri kaynağı için tüm gereksinimlere sahipsiniz. API çağrısının istek URL'si hakkındaki ayrıntıları almak için, Bing Haritalar Geliştirme Merkezi'nde **Veri kaynakları**'nı ve **Veri Kaynağı Bilgisi**'ni seçin.

    ![](./media/notification-hubs-geofence/bing-maps-data-info.png)

    **Sorgu URL’si**, cihazın şu anda bir konum sınırları içinde olup olmadığını denetlemek için sorgular çalıştırabileceğiniz uç noktadır. Bu denetimi gerçekleştirmek için, aşağıdaki parametrelerin eklendiği sorgu URL'sinde bir GET çağrısı yapın:

    ```text
    ?spatialFilter=intersects(%27POINT%20LONGITUDE%20LATITUDE)%27)&$format=json&key=QUERY_KEY
    ```

    Bing Haritalar, cihazın bölge sınırı içinde olup olmadığını görmek için hesaplamaları otomatik olarak gerçekleştirir. Bir tarayıcı (veya cURL) aracılığıyla isteği yürüttükten sonra, standart bir JSON yanıtı alırsınız:

    ![](./media/notification-hubs-geofence/bing-maps-json.png)

    Bu yanıt yalnızca, söz konusu nokta belirlenen sınırlar içinde olduğunda gönderilir. Aksi takdirde boş bir **sonuçlar** demeti alırsınız:

    ![](./media/notification-hubs-geofence/bing-maps-nores.png)

## <a name="set-up-the-uwp-application"></a>UWP uygulamasını ayarlama

1. Visual Studio'da **Boş Uygulama (Evrensel Windows)** türü yeni bir proje başlatın.

    ![](./media/notification-hubs-geofence/notification-hubs-create-blank-app.png)

    Proje oluşturmayı tamamladıktan sonra, uygulamanın kendisi için donanımınız olması gerekir. Şimdi jeo-eskrim altyapısı için her şeyi ayarlayalım. Bu çözüm için Bing hizmetlerini kullanacağınızdan, belirli konum çerçevelerini sorgulamanıza izin veren bir ortak REST API uç noktası vardır:

    ```text
    http://spatial.virtualearth.net/REST/v1/data/
    ```
    Çalıştırmak için aşağıdaki parametreleri belirtin:

   * **Veri Kaynağı Kimliği** ve **Veri Kaynağı Adı**: Veri kaynakları, Bing Haritalar API'sinde konumlar ve çalışma saatleri gibi kümelenmiş çeşitli meta veriler içerir.  
   * **Varlık Adı**: Bildirim için bir başvuru noktası olarak kullanmak istediğiniz varlık.
   * **Bing Haritalar API'si Anahtarı**: Daha önce Bing Geliştirme Merkezi hesabı oluşturduğunuzda edindiğiniz anahtardır.

     Veri kaynağınız hazır olduktan sonra UWP uygulaması üzerinde çalışmaya başlayabilirsiniz.
2. Uygulamanız için konum hizmetlerini etkinleştirin. **Çözüm Gezgini**'nde `Package.appxmanifest` dosyasını açın.

    ![](./media/notification-hubs-geofence/vs-package-manifest.png)
3. Açtığınız paket özellikleri sekmesinde **Özellikler** sekmesine geçip **Konum**'u seçin.

    ![](./media/notification-hubs-geofence/vs-package-location.png)
4. Çözümünüz içinde `Core` adlı yeni bir klasör oluşturun ve içine `LocationHelper.cs` adlı yeni bir dosya ekleyin:

    ![](./media/notification-hubs-geofence/vs-location-helper.png)

    `LocationHelper` sınıfı, sistem API'si aracılığıyla kullanıcı konumunu almak için kod içerir:

    ```csharp
    using System;
    using System.Threading.Tasks;
    using Windows.Devices.Geolocation;

    namespace NotificationHubs.Geofence.Core
    {
        public class LocationHelper
        {
            private static readonly uint AppDesiredAccuracyInMeters = 10;

            public async static Task<Geoposition> GetCurrentLocation()
            {
                var accessStatus = await Geolocator.RequestAccessAsync();
                switch (accessStatus)
                {
                    case GeolocationAccessStatus.Allowed:
                        {
                            Geolocator geolocator = new Geolocator { DesiredAccuracyInMeters = AppDesiredAccuracyInMeters };

                            return await geolocator.GetGeopositionAsync();
                        }
                    default:
                        {
                            return null;
                        }
                }
            }

        }
    }
    ```

    UWP uygulamalarında kullanıcının konumunu alma hakkında daha fazla bilgi edinmek için[bkz.](https://msdn.microsoft.com/library/windows/apps/mt219698.aspx)
5. Konum almanın çalışıp çalışmadığını denetlemek için ana sayfanızın kod tarafını açın (`MainPage.xaml.cs`). `MainPage` oluşturucusunda `Loaded` olayı için yeni bir olay işleyicisi oluşturun.

    ```csharp
    public MainPage()
    {
        this.InitializeComponent();
        this.Loaded += MainPage_Loaded;
    }
    ```

    Olay işleyicisinin uygulaması aşağıdaki gibidir:

    ```csharp
    private async void MainPage_Loaded(object sender, RoutedEventArgs e)
    {
        var location = await LocationHelper.GetCurrentLocation();

        if (location != null)
        {
            Debug.WriteLine(string.Concat(location.Coordinate.Longitude,
                " ", location.Coordinate.Latitude));
        }
    }
    ```
6. Uygulamayı çalıştırın ve konumunuza erişmesine izin verin.

    ![](./media/notification-hubs-geofence/notification-hubs-location-access.png)
7. Uygulama başlatıldıktan sonra, **Çıkış** penceresinde koordinatları görebilmeniz gerekir:

    ![](./media/notification-hubs-geofence/notification-hubs-location-output.png)

    Artık konum ediniminin işe yaradığını biliyorsunuz, artık kullanamayacağınız için isterseniz Loaded olay işleyicisini kaldırabilirsiniz.
8. Sonraki adım konum değişikliklerini yakalamaktır. `LocationHelper` sınıfında `PositionChanged` için olay işleyicisini ekleyin:

    ```csharp
    geolocator.PositionChanged += Geolocator_PositionChanged;
    ```

    Uygulama, konum koordinatlarını **Çıkış** penceresinde gösterir:

    ```csharp
    private static async void Geolocator_PositionChanged(Geolocator sender, PositionChangedEventArgs args)
    {
        await CoreApplication.MainView.CoreWindow.Dispatcher.RunAsync(CoreDispatcherPriority.Normal, () =>
        {
            Debug.WriteLine(string.Concat(args.Position.Coordinate.Longitude, " ", args.Position.Coordinate.Latitude));
        });
    }
    ```

## <a name="set-up-the-backend"></a>Arka ucu ayarlama

1. [GitHub'dan .NET Arka Ucu Örneği](https://github.com/Azure/azure-notificationhubs-samples/tree/master/dotnet/NotifyUsers)'ni indirin.
2. İndirme işlemi tamamlandıktan sonra `NotifyUsers` klasörünü ve ardından Visual Studio’da `NotifyUsers.sln` dosyasını açın.
3. `AppBackend` projesini **Başlangıç Projesi** olarak ayarlayın ve başlatın.

    ![](./media/notification-hubs-geofence/vs-startup-project.png)

    Proje hedef cihazlara anında iletme bildirimleri göndermek için zaten yapılandırıldı. Bu nedenle yapmanız gereken yalnızca iki şey var: Bildirim hub'ı için doğru bağlantı dizesini belirtin ve sadece kullanıcı bölge sınırı içinde olduğunda bildirim göndermek için sınır kimliği ekleyin.

4. Bağlantı dizesini yapılandırmak için `Models` klasöründe `Notifications.cs` öğesini açın. `NotificationHubClient.CreateClientFromConnectionString` işlevi, [Azure portalından](https://portal.azure.com) alabileceğiniz bildirim hub'ınız hakkındaki bilgileri içermelidir (**Ayarlar**'da, **Erişim İlkeleri** sayfasının içine bakın). Güncelleştirilen yapılandırma dosyasını kaydedin.
5. Bing Haritalar API'si sonucu için bir model oluşturun. Bunu `Models` yapmanın en kolay yolu klasörü açmak ve**Sınıf** **Ekle'yi** > seçmektir. Bunu, `GeofenceBoundary.cs` olarak adlandırın. Bunu yaptıktan sonra, birinci bölümde elde ettiğiniz API yanıtından JSON dosyasını kopyalayın. Visual Studio'da, Sınıf olarak Yapıştır Özel > **Yapıştır JSON'ı** **edit.** > **Paste Special**

    Böylece, nesnenin istendiği gibi tamamen seri durumdan çıkarılacağından emin olursunuz. Elde edilen sınıf kümeniz aşağıdaki sınıfa benzemelidir:

    ```csharp
    namespace AppBackend.Models
    {
        public class Rootobject
        {
            public D d { get; set; }
        }

        public class D
        {
            public string __copyright { get; set; }
            public Result[] results { get; set; }
        }

        public class Result
        {
            public __Metadata __metadata { get; set; }
            public string EntityID { get; set; }
            public string Name { get; set; }
            public float Longitude { get; set; }
            public float Latitude { get; set; }
            public string Boundary { get; set; }
            public string Confidence { get; set; }
            public string Locality { get; set; }
            public string AddressLine { get; set; }
            public string AdminDistrict { get; set; }
            public string CountryRegion { get; set; }
            public string PostalCode { get; set; }
        }

        public class __Metadata
        {
            public string uri { get; set; }
        }
    }
    ```
6. Ardından `Controllers` > `NotificationsController.cs` öğesini açın. Hedef enlemini ve boylamını hesaba katmak için Post çağrısını güncelleştirin. Bunu yapmak için işlev imzasına iki dize ekleyin: `latitude` ve `longitude`.

    ```csharp
    public async Task<HttpResponseMessage> Post(string pns, [FromBody]string message, string to_tag, string latitude, string longitude)
    ```
7. Proje içinde `ApiHelper.cs` adlı yeni bir sınıf oluşturun. Bunu, nokta sınır kesişimlerini denetlemek amacıyla Bing'e bağlanmak için kullanacaksınız. Aşağıdaki kodda gösterildiği gibi bir `IsPointWithinBounds` işlevi uygulayın:

    ```csharp
    public class ApiHelper
    {
        public static readonly string ApiEndpoint = "{YOUR_QUERY_ENDPOINT}?spatialFilter=intersects(%27POINT%20({0}%20{1})%27)&$format=json&key={2}";
        public static readonly string ApiKey = "{YOUR_API_KEY}";

        public static bool IsPointWithinBounds(string longitude,string latitude)
        {
            var json = new WebClient().DownloadString(string.Format(ApiEndpoint, longitude, latitude, ApiKey));
            var result = JsonConvert.DeserializeObject<Rootobject>(json);
            if (result.d.results != null && result.d.results.Count() > 0)
            {
                return true;
            }
            else
            {
                return false;
            }
        }
    }
    ```

    > [!IMPORTANT]
    > API uç noktasını, daha önce Bing Geliştirme Merkezi'nden edindiğiniz sorgu URL'si ile değiştirdiğinizden emin olun (aynısı API anahtarı için de geçerlidir).

    Sorgunun sonuçları getirmesi belirtilen noktanın bölge sınırının içinde olduğu anlamına gelir, bu durumda işlev `true` döndürür. Sonuç yoksa Bing, noktanın arama çerçevesi dışında olduğunu belirtiyor demektir. Bu durumda işlev `false` döndürür.
8. `NotificationsController.cs` içinde, switch deyiminden hemen önce bir denetim oluşturun:

    ```csharp
    if (ApiHelper.IsPointWithinBounds(longitude, latitude))
    {
        switch (pns.ToLower())
        {
            case "wns":
                //// Windows 8.1 / Windows Phone 8.1
                var toast = @"<toast><visual><binding template=""ToastText01""><text id=""1"">" +
                            "From " + user + ": " + message + "</text></binding></visual></toast>";
                outcome = await Notifications.Instance.Hub.SendWindowsNativeNotificationAsync(toast, userTag);

                // Windows 10 specific Action Center support
                toast = @"<toast><visual><binding template=""ToastGeneric""><text id=""1"">" +
                            "From " + user + ": " + message + "</text></binding></visual></toast>";
                outcome = await Notifications.Instance.Hub.SendWindowsNativeNotificationAsync(toast, userTag);

                break;
        }
    }
    ```

## <a name="test-push-notifications-in-the-uwp-app"></a>UWP uygulamasında anında iletme bildirimlerini test etme

1. UWP uygulamasında artık bildirimleri test edebilmeniz gerekir. `LocationHelper` sınıfı içinde yeni bir işlev oluşturun (`SendLocationToBackend`):

    ```csharp
    public static async Task SendLocationToBackend(string pns, string userTag, string message, string latitude, string longitude)
    {
        var POST_URL = "http://localhost:8741/api/notifications?pns=" +
            pns + "&to_tag=" + userTag + "&latitude=" + latitude + "&longitude=" + longitude;

        using (var httpClient = new HttpClient())
        {
            try
            {
                await httpClient.PostAsync(POST_URL, new StringContent("\"" + message + "\"",
                    System.Text.Encoding.UTF8, "application/json"));
            }
            catch (Exception ex)
            {
                Debug.WriteLine(ex.Message);
            }
        }
    }
    ```

    > [!NOTE]
    > `POST_URL` değerini dağıtılmış web uygulamanızın konumuna ayarlayın. Şimdilik, yerel olarak çalıştırmak için tamam, ancak ortak bir sürümünü dağıtma üzerinde çalışırken, harici bir sağlayıcı ile barındırmak gerekir.
2. Anında iletme bildirimleri için UWP uygulamanızı kaydedin. Visual Studio'da,**mağazayla Project****Store** > Associate uygulamasını **seçin.** > 

    ![](./media/notification-hubs-geofence/vs-associate-with-store.png)
3. Geliştirici hesabınızda oturum açtıktan sonra, mevcut bir uygulamayı seçtiğinizden emin olun veya yeni bir tane oluşturun ve paketi bununla ilişkilendirin.
4. Geliştirme Merkezi'ne gidin ve oluşturduğunuz uygulamayı açın. **Hizmetleri** > **E-itme Bildirimleri** > Canlı Hizmetler**sitesini**seçin.

    ![](./media/notification-hubs-geofence/ms-live-services.png)
5. Sitede, **Uygulama Anahtarı** ve **Paket SID**'sini not edin. Her ikisine de Azure portalında ihtiyacınız var – bildirim merkezinizi açın, **Ayarlar** > **Bildirim Hizmetleri** > **Windows'unu (WNS)** seçin ve gerekli alanlara bilgileri girin.

    ![](./media/notification-hubs-geofence/notification-hubs-wns.png)
6. **Kaydet**'i seçin.
7. **Çözüm Gezgini**'nde **Başvurular**'ı açın ve **NuGet Paketlerini Yönet**'i seçin. **Microsoft Azure Service Bus yönetilen kitaplık**'a bir başvuru ekleyin. `WindowsAzure.Messaging.Managed` için arama yapın ve projenize ekleyin.

    ![](./media/notification-hubs-geofence/vs-nuget.png)
8. Test amacıyla, `MainPage_Loaded` olay işleyicisini yeniden oluşturun ve içine şu kod parçacığını ekleyin:

    ```csharp
    var channel = await PushNotificationChannelManager.CreatePushNotificationChannelForApplicationAsync();

    var hub = new NotificationHub("HUB_NAME", "HUB_LISTEN_CONNECTION_STRING");
    var result = await hub.RegisterNativeAsync(channel.Uri);

    // Displays the registration ID so you know it was successful
    if (result.RegistrationId != null)
    {
        Debug.WriteLine("Reg successful.");
    }
    ```

    Kod, uygulamayı bildirim hub'ına kaydeder. Artık başlamaya hazırsınız!
9. `LocationHelper` sınıfındaki `Geolocator_PositionChanged` işleyicisi içine, konumu bölge sınırının içine zorla yerleştirecek bir test kodu parçası ekleyebilirsiniz:

    ```csharp
    await LocationHelper.SendLocationToBackend("wns", "TEST_USER", "TEST", "37.7746", "-122.3858");
    ```

10. Gerçek koordinatları geçirmediğiniz (şu anda sınırlar içinde olmayabilirler) ve önceden tanımlanmış test değerleri kullandığınız için güncelleştirme üzerinde bir bildirim belirdiğini görürsünüz:

    ![](./media/notification-hubs-geofence/notification-hubs-test-notification.png)

## <a name="next-steps"></a>Sonraki adımlar

Çözümü üretime hazır hale getirmek için izlemeniz gerekebilecek birkaç adım vardır.

1. İlk olarak, bölge sınırlarının dinamik olduğundan emin olmanız gerekir. Bunun yapılması, yeni sınırları mevcut veri kaynağı içinde karşıya yükleyebilmek için Bing API'si ile bazı ek çalışmalar yapmayı gerektirir. Daha fazla bilgi için bkz. [Bing Uzamsal Veri Hizmetleri API’si belgeleri](https://msdn.microsoft.com/library/ff701734.aspx).
2. İkinci olarak, teslimin doğru katılımcılara yapıldığından emin olmaya çalıştığınızdan, onları [etiketleme](notification-hubs-tags-segment-push-message.md) yoluyla hedeflemek isteyebilirsiniz.

Bu öğreticide gösterilen çözümde, çok çeşitli hedef platformlara sahip olabileceğiniz bir senaryo açıklanmaktadır. Bu nedenle, bölge sınırlamasını sisteme özgü özelliklerle sınırlamaz. Bununla birlikte, Evrensel Windows Platformu [bölge sınırlarını hemen algılayan](https://msdn.microsoft.com/windows/uwp/maps-and-location/set-up-a-geofence) özellikler sunar.
