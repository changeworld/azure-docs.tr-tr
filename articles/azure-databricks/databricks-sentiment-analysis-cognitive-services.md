---
title: Duyarlılık analizi için Azure Veri Tuğlaları'nı kullanma
description: Veri akışı yla ilgili duyarlılık çözümlemesi yapmak için Etkinlik Hub'ları ve Bilişsel Hizmetler API'si ile Azure Veri Tuğlaları'nı kullanmayı öğrenin.
services: azure-databricks
author: lenadroid
ms.author: alehall
ms.reviewer: mamccrea
ms.service: azure-databricks
ms.custom: mvc
ms.topic: tutorial
ms.date: 07/29/2019
ms.openlocfilehash: 382dff156c088f367200f0dd46c3758193ade189
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/24/2020
ms.locfileid: "75889238"
---
# <a name="tutorial-sentiment-analysis-on-streaming-data-using-azure-databricks"></a>Öğretici: Azure Databricks kullanarak akış verileri ile ilgili yaklaşım analizi

Bu eğitimde, Azure Veri Tuğlaları'nı kullanarak bir veri akışında duygu çözümlemesi nasıl çalıştırabileceğinizi neredeyse gerçek zamanlı olarak öğrenirsiniz. Azure Event Hubs’ı kullanarak veri alımı sistemini ayarlarsınız. Spark Event Hubs bağlayıcısını kullanarak Event Hubs’tan Azure Databricks’e iletileri kullanırsınız. Son olarak, akışlı veriler üzerinde duyarlılık çözümlemesi çalıştırmak için Bilişsel Hizmet API'lerini kullanırsınız.

Bu öğreticinin sonunda, Twitter’daki "Azure" terimini içeren tweet’leri alır ve bu tweet’ler ile ilgili yaklaşım analizi çalıştırmış olursunuz.

Aşağıdaki şekilde uygulama akışı gösterilmektedir:

![Etkinlik Hub'ları ve Bilişsel Hizmetlere sahip Azure Veri Tuğlaları](./media/databricks-sentiment-analysis-cognitive-services/databricks-cognitive-services-tutorial.png "Etkinlik Hub'ları ve Bilişsel Hizmetlere sahip Azure Veri Tuğlaları")

Bu öğretici aşağıdaki görevleri kapsar:

> [!div class="checklist"]
> * Azure Databricks çalışma alanı oluşturma
> * Azure Databricks’te Spark kümesi oluşturma
> * Akış verilerine erişmek için bir Twitter uygulaması oluşturma
> * Azure Databricks’te not defterleri oluşturma
> * Event Hubs ve Twitter API’si için kitaplıklar ekleme
> * Bilişsel Hizmetler hesabı oluşturun ve erişim anahtarını alın
> * Event Hubs’a tweet’ler gönderme
> * Event Hubs’tan tweet’leri okuma
> * Tweet’lerle ilgili yaklaşım analizi çalıştırma

Azure aboneliğiniz yoksa, başlamadan önce [ücretsiz bir hesap oluşturun.](https://azure.microsoft.com/free/?WT.mc_id=sparkeventhubs-docs-alehall)

> [!Note]
> Bu öğretici **Azure Ücretsiz Deneme Aboneliği**kullanılarak gerçekleştirilemez.
> Ücretsiz bir hesabınız varsa, profilinize gidin ve aboneliğinizi istediğiniz **kadar öde**olarak değiştirin. Daha fazla bilgi için bkz. [Ücretsiz Azure hesabı](https://azure.microsoft.com/free/). Ardından, [harcama sınırını kaldırın](https://docs.microsoft.com/azure/billing/billing-spending-limit#remove-the-spending-limit-in-azure-portal)ve bölgenizdeki VCPU'lar için kota artışı [isteyin.](https://docs.microsoft.com/azure/azure-portal/supportability/resource-manager-core-quotas-request) Azure Databricks çalışma alanınızı oluşturduğunuzda, çalışma alanına ücretsiz Premium Azure Databricks DBUs'a 14 gün süreyle erişim sağlamak için **Deneme (Premium - 14 Gün Ücretsiz DBUs)** fiyatlandırma katmanını seçebilirsiniz.

## <a name="prerequisites"></a>Ön koşullar

Bu öğreticiye başlamadan önce aşağıdaki gereksinimlerin karşılandığından emin olun:
- Azure Event Hubs ad alanı.
- Ad alanı içinde bir Olay Hub’ı.
- Event Hubs ad alanına erişmek için bağlantı dizesi. Bağlantı dizesi şuna benzer bir biçimde olmalıdır: `Endpoint=sb://<namespace>.servicebus.windows.net/;SharedAccessKeyName=<key name>;SharedAccessKey=<key value>`.
- Event Hubs için paylaşılan erişim ilkesi adı ve ilke anahtarı.

[Azure Event Hubs ad alanı ve olay hub’ı oluşturma](../event-hubs/event-hubs-create.md) başlıklı makaledeki adımları tamamlayarak bu gereksinimleri karşılayabilirsiniz.

## <a name="sign-in-to-the-azure-portal"></a>Azure portalında oturum açın

[Azure portalında](https://portal.azure.com/?WT.mc_id=sparkeventhubs-docs-alehall)oturum açın.

## <a name="create-an-azure-databricks-workspace"></a>Azure Databricks çalışma alanı oluşturma

Bu bölümde Azure portalını kullanarak bir Azure Databricks çalışma alanı oluşturursunuz.

1. Azure portalında **kaynak** > Veri Oluştur **+ Analitik** > **Azure Veri Tuğlaları'nı**seçin.

    ![Azure portalında veri tuğlaları](./media/databricks-sentiment-analysis-cognitive-services/azure-databricks-on-portal.png "Azure portalında veri tuğlaları")

3. **Azure Databricks Hizmeti** bölümünde, Databricks çalışma alanı oluşturmak için değerler sağlayın.

    ![Azure Databricks çalışma alanı oluşturma](./media/databricks-sentiment-analysis-cognitive-services/create-databricks-workspace.png "Azure Databricks çalışma alanı oluşturma")

    Aşağıdaki değerleri sağlayın:

    |Özellik  |Açıklama  |
    |---------|---------|
    |**Çalışma alanı adı**     | Databricks çalışma alanınız için bir ad sağlayın        |
    |**Abonelik**     | Açılan listeden Azure aboneliğinizi seçin.        |
    |**Kaynak grubu**     | Yeni bir kaynak grubu oluşturmayı veya mevcut bir kaynak grubunu kullanmayı seçin. Kaynak grubu, bir Azure çözümü için ilgili kaynakları bir arada tutan kapsayıcıdır. Daha fazla bilgi için bkz. [Azure Kaynak Grubuna genel bakış](../azure-resource-manager/management/overview.md). |
    |**Konum**     | **Doğu ABD 2**’yi seçin. Kullanılabilir diğer bölgeler için bkz. [Bölgeye göre kullanılabilir Azure hizmetleri](https://azure.microsoft.com/regions/services/?WT.mc_id=sparkeventhubs-docs-alehall).        |
    |**Fiyatlandırma Katmanı**     |  **Standart** veya **Premium** arasında seçim yapın. Bu katmanlar hakkında daha fazla bilgi için bkz. [Databricks fiyatlandırma sayfası](https://azure.microsoft.com/pricing/details/databricks/?WT.mc_id=sparkeventhubs-docs-alehall).       |

    **Panoya sabitle**’yi ve sonra **Oluştur**’u seçin.

4. Hesabın oluşturulması birkaç dakika sürer. Hesap oluşturma sırasında portal sağ tarafta **Azure Databricks için dağıtım gönderiliyor** kutucuğunu gösterir. Kutucuğu görmek için panonuzu sağa kaydırmanız gerekebilir. Ayrıca, ekranın üst kısmında gösterilen bir ilerleme çubuğu vardır. İlerleme durumu için her iki alanı da izleyebilirsiniz.

    ![Databricks dağıtım döşemesi](./media/databricks-sentiment-analysis-cognitive-services/databricks-deployment-tile.png "Databricks dağıtım döşemesi")

## <a name="create-a-spark-cluster-in-databricks"></a>Databricks’te Spark kümesi oluşturma

1. Azure portalında, oluşturduğunuz Databricks çalışma alanına gidin ve sonra **Çalışma Alanını Başlat**’ı seçin.

2. Azure Databricks portalına yönlendirilirsiniz. Portaldan **Küme**’yi seçin.

    ![Azure'da Veri Tuğlaları](./media/databricks-sentiment-analysis-cognitive-services/databricks-on-azure.png "Azure'da Veri Tuğlaları")

3. **Yeni küme** sayfasında, bir küme oluşturmak için değerleri girin.

    ![Azure'da Veri Tuğlaları Kıvılcım kümesi Oluşturma](./media/databricks-sentiment-analysis-cognitive-services/create-databricks-spark-cluster.png "Azure'da Veri Tuğlaları Kıvılcım kümesi Oluşturma")

    Aşağıdakiler dışında diğer tüm varsayılan değerleri kabul edin:

   * Küme için bir ad girin.
   * Bu makale için, **6.0** çalışma süresi olan bir küme oluşturun.
   * Dakikalar süren inaktivite onay kutusunun **ardından \_ \_ Sonlandırma'yı seçtiğinizden** emin olun. Küme kullanılmazsa kümenin sonlandırılması için biz süre (dakika cinsinden) belirtin.

   Teknik ölçütlerinize ve [bütçenize](https://azure.microsoft.com/pricing/details/databricks/?WT.mc_id=sparkeventhubs-docs-alehall)uygun küme işçi ve sürücü düğümü boyutunu seçin.

     **Küme oluştur**’u seçin. Küme çalışmaya başladıktan sonra kümeye not defterleri ekleyebilir ve Spark işleri çalıştırabilirsiniz.

## <a name="create-a-twitter-application"></a>Twitter uygulaması oluşturma

Tweet’lerin akışını almak için Twitter’da bir uygulama oluşturursunuz. Yönergeleri izleyerek bir Twitter uygulaması oluşturun ve bu öğreticiyi tamamlamak için ihtiyaç duyduğunuz değerleri kaydedin.

1. Bir web [tarayıcısından, Geliştiriciler için Twitter'a](https://developer.twitter.com/en/apps)gidin ve uygulama **oluştur'u**seçin. Bir Twitter geliştirici hesabına başvurmanız gerektiğini belirten bir ileti görebilirsiniz. Bunu yapmak için çekinmeyin ve başvurunuz onaylandıktan sonra bir onay e-postası görmelisiniz. Geliştirici hesabının onaylanması birkaç gün sürebilir.

    ![Twitter geliştirici hesap onayı](./media/databricks-sentiment-analysis-cognitive-services/databricks-twitter-dev-confirmation.png "Twitter geliştirici hesap onayı")

2. **Uygulama oluşturun** sayfasında yeni uygulamaya ilişkin ayrıntıları sağlayın ve **Kendi Twitter uygulamanızı oluşturun**’u seçin.

    ![Twitter uygulama detayları](./media/databricks-sentiment-analysis-cognitive-services/databricks-provide-twitter-app-details.png "Twitter uygulama detayları")

    ![Twitter uygulama detayları](./media/databricks-sentiment-analysis-cognitive-services/databricks-provide-twitter-app-details-create.png "Twitter uygulama detayları")

3. Uygulama sayfasında, **Anahtarlar ve Belirteçler** sekmesini seçin ve **Tüketici API Anahtarı** ve Tüketici **API Gizli Anahtarı**değerlerini kopyalayın. Ayrıca, erişim belirteçlerini oluşturmak için **Access Token ve Access Token Secret** altında **Oluştur'u** seçin. **Erişim Belirteci** ve**Erişim Belirteci Parolası** değerlerini kopyalayın.

    ![Twitter uygulama detayları](./media/databricks-sentiment-analysis-cognitive-services/twitter-app-key-secret.png "Twitter uygulama detayları")

Twitter uygulaması için aldığınız değerleri kaydedin. Öğreticinin sonraki bölümlerinde bu değerlere ihtiyacınız olacaktır.

## <a name="attach-libraries-to-spark-cluster"></a>Spark kümesine kitaplıklar ekleme

Bu öğreticide, Event Hubs’a tweet’ler göndermek için Twitter API’lerini kullanırsınız. Azure Event Hubs’a verileri okuyup yazmak için de [Apache Spark Event Hubs bağlayıcısını](https://github.com/Azure/azure-event-hubs-spark?WT.mc_id=sparkeventhubs-docs-alehall) kullanırsınız. Bu API'leri kümenizin bir parçası olarak kullanmak için, bunları Azure Databricks'e kitaplık olarak ekleyin ve Bunları Spark kümenizle ilişkilendirin. Aşağıdaki yönergeler kitaplık eklemeyi gösterir.

1. Azure Databricks çalışma alanında **Kümeler'i**seçin ve varolan Spark kümenizi seçin. Küme menüsünde **Kitaplıkları** seçin ve **Yeni Yükle'yi**tıklatın.

   ![Kitaplık iletişim kutusu ekleme](./media/databricks-sentiment-analysis-cognitive-services/databricks-add-library-locate-cluster.png "Kitaplık bulma kümesi ekle")

   ![Kitaplık iletişim kutusu ekleme](./media/databricks-sentiment-analysis-cognitive-services/databricks-add-library-install-new.png "Kitaplık ekle yeni yükle")

2. Yeni Kütüphane sayfasında, **Kaynak** için **Maven**seçin. **Koordinat**için, eklemek istediğiniz paket için **Arama Paketleri'ni** tıklatın. Bu öğreticide kullanılan kitaplıklar için Maven koordinatları aşağıdaki gibidir:

   * Spark Event Hubs bağlayıcısı - `com.microsoft.azure:azure-eventhubs-spark_2.11:2.3.10`
   * Twitter API’si - `org.twitter4j:twitter4j-core:4.0.7`

     ![Maven koordinatlarını sağlayın](./media/databricks-sentiment-analysis-cognitive-services/databricks-add-library-search.png "Maven koordinatlarını sağlayın")

     ![Maven koordinatlarını sağlayın](./media/databricks-sentiment-analysis-cognitive-services/databricks-add-library-search-dialogue.png "Maven koordinatlarını ara")

3. **Yükle**’yi seçin.

4. Küme menüsünde, her iki kitaplıkta da düzgün şekilde yüklendiğinden ve eklendiğinden emin olun.

    ![Kitaplıkları denetleme](./media/databricks-sentiment-analysis-cognitive-services/databricks-add-library-check.png "Kitaplıkları denetleme")

6. Twitter paketi için şu adımları yineleyin, `twitter4j-core:4.0.7`.

## <a name="get-a-cognitive-services-access-key"></a>Bilişsel Hizmetler erişim anahtarını alma

Bu eğitimde, bir tweet akışında neredeyse gerçek zamanlı olarak duyarlılık analizi yapmak için [Azure Bilişsel Hizmetler Metin Analizi API'lerini](../cognitive-services/text-analytics/overview.md) kullanırsınız. API'leri kullanmadan önce Azure'da bir Azure Bilişsel Hizmetler hesabı oluşturmanız ve Metin Analizi API'lerini kullanmak için bir erişim anahtarı almanız gerekir.

1. [Azure portalında](https://portal.azure.com/?WT.mc_id=sparkeventhubs-docs-alehall)oturum açın.

2. **+ Kaynak oluştur**’u seçin.

3. Azure Marketi **altında, AI + Bilişsel Hizmetler** > **Metin Analizi API'yi**seçin.

    ![Bilişsel hizmetler hesabı oluşturma](./media/databricks-sentiment-analysis-cognitive-services/databricks-cognitive-services-text-api.png "Bilişsel hizmetler hesabı oluşturma")

4. **Oluştur** iletişim kutusunda aşağıdaki değerleri sağlayın:

    ![Bilişsel hizmetler hesabı oluşturma](./media/databricks-sentiment-analysis-cognitive-services/create-cognitive-services-account.png "Bilişsel hizmetler hesabı oluşturma")

   - Bilişsel Hizmetler hesabı için bir ad girin.
   - Hesabın oluşturulacağı Azure aboneliğini seçin.
   - Bir Azure konumu seçin.
   - Hizmet için bir fiyatlandırma katmanı seçin. Bilişsel Hizmetler fiyatlandırması hakkında daha fazla bilgi için bkz. [fiyatlandırma sayfası](https://azure.microsoft.com/pricing/details/cognitive-services/?WT.mc_id=sparkeventhubs-docs-alehall).
   - Yeni bir kaynak grubu oluşturmak mı yoksa varolan bir tane seçmek mi istediğinizi belirtin.

     **Oluştur'u**seçin.

5. Hesap oluşturulduktan sonra **Genel Bakış** sekmesinden **Erişim tuşlarını göster'i**seçin.

    ![Erişim anahtarlarını göster](./media/databricks-sentiment-analysis-cognitive-services/cognitive-services-get-access-keys.png "Erişim anahtarlarını göster")

    Ayrıca ekran görüntüsünde gösterildiği gibi uç nokta URL’sinin bir kısmını kopyalayın. Öğreticide bu URL’ye ihtiyacınız olacaktır.

6. **Anahtarları yönet** bölümünde, kullanmak istediğiniz anahtarın karşısındaki kopyala simgesini seçin.

    ![Erişim anahtarlarını kopyalama](./media/databricks-sentiment-analysis-cognitive-services/cognitive-services-copy-access-keys.png "Erişim anahtarlarını kopyalama")

7. Uç nokta URL’si ve erişim anahtarı için bu adımda aldığınız değerleri kaydedin. Daha sonra bu öğreticide gerekli olacak.

## <a name="create-notebooks-in-databricks"></a>Databricks’te not defterleri oluşturma

Bu bölümde, Databricks çalışma alanında aşağıdaki adlarla iki not defteri oluşturacaksınız

- **SendTweetsToEventHub** - Bu, Twitter’dan tweet’ler almak ve bunları Event Hubs’ta akışa almak için kullandığınız bir üretici not defteridir.
- **AnalyzeTweetsFromEventHub** - Bu, Event Hubs’tan tweet’leri okumak ve yaklaşım analizi çalıştırmak için kullandığınız bir tüketici not defteridir.

1. Sol bölmede **Çalışma Alanı**’nı seçin. **Çalışma Alanı** açılır listesinden **Oluştur**’u ve sonra **Not Defteri**’ni seçin.

    ![Databricks'te not defteri oluşturma](./media/databricks-sentiment-analysis-cognitive-services/databricks-create-notebook.png "Databricks'te not defteri oluşturma")

2. **Not Defteri Oluştur** iletişim kutusuna **SendTweetsToEventHub** girin, dil olarak **Scala**’yı seçin ve daha önce oluşturduğunuz Spark kümesini seçin.

    ![Databricks'te not defteri oluşturma](./media/databricks-sentiment-analysis-cognitive-services/databricks-notebook-details.png "Databricks'te not defteri oluşturma")

    **Oluştur'u**seçin.

3. **AnalyzeTweetsFromEventHub** not defterini oluşturma adımlarını yineleyin.

## <a name="send-tweets-to-event-hubs"></a>Event Hubs’a tweet’ler gönderme

**SendTweetsToEventHub** not defterine, aşağıdaki kodu yapıştırın ve yer tutucuları daha önce oluşturduğunuz Etkinlik Hub'ları ad alanı ve Twitter uygulamanızın değerleriyle değiştirin. Bu not defteri, gerçek zamanlı olarak "Azure" anahtar sözcüğünü içeren tweet’leri Event Hubs’ta akışa alır.

> [!NOTE]
> Twitter API belirli istek kısıtlamaları ve [kotaları](https://developer.twitter.com/en/docs/basics/rate-limiting.html)vardır. Twitter API'sinde standart fiyat sınırlaması ile memnun değilseniz, bu örnekte Twitter API'si kullanmadan metin içeriği oluşturabilirsiniz. Bunu yapmak için, tercih `test` edilen `twitter` test girişi ile liste **testSource** yerine değişken **dataSource** ayarlayın ve doldurmak.

```scala
    import scala.collection.JavaConverters._
    import com.microsoft.azure.eventhubs._
    import java.util.concurrent._
    import scala.collection.immutable._
    import scala.concurrent.Future
    import scala.concurrent.ExecutionContext.Implicits.global

    val namespaceName = "<EVENT HUBS NAMESPACE>"
    val eventHubName = "<EVENT HUB NAME>"
    val sasKeyName = "<POLICY NAME>"
    val sasKey = "<POLICY KEY>"
    val connStr = new ConnectionStringBuilder()
                .setNamespaceName(namespaceName)
                .setEventHubName(eventHubName)
                .setSasKeyName(sasKeyName)
                .setSasKey(sasKey)

    val pool = Executors.newScheduledThreadPool(1)
    val eventHubClient = EventHubClient.create(connStr.toString(), pool)

    def sleep(time: Long): Unit = Thread.sleep(time)

    def sendEvent(message: String, delay: Long) = {
      sleep(delay)
      val messageData = EventData.create(message.getBytes("UTF-8"))
      eventHubClient.get().send(messageData)
      System.out.println("Sent event: " + message + "\n")
    }

    // Add your own values to the list
    val testSource = List("Azure is the greatest!", "Azure isn't working :(", "Azure is okay.")

    // Specify 'test' if you prefer to not use Twitter API and loop through a list of values you define in `testSource`
    // Otherwise specify 'twitter'
    val dataSource = "test"

    if (dataSource == "twitter") {

      import twitter4j._
      import twitter4j.TwitterFactory
      import twitter4j.Twitter
      import twitter4j.conf.ConfigurationBuilder

      // Twitter configuration!
      // Replace values below with you

      val twitterConsumerKey = "<CONSUMER API KEY>"
      val twitterConsumerSecret = "<CONSUMER API SECRET>"
      val twitterOauthAccessToken = "<ACCESS TOKEN>"
      val twitterOauthTokenSecret = "<TOKEN SECRET>"

      val cb = new ConfigurationBuilder()
        cb.setDebugEnabled(true)
        .setOAuthConsumerKey(twitterConsumerKey)
        .setOAuthConsumerSecret(twitterConsumerSecret)
        .setOAuthAccessToken(twitterOauthAccessToken)
        .setOAuthAccessTokenSecret(twitterOauthTokenSecret)

      val twitterFactory = new TwitterFactory(cb.build())
      val twitter = twitterFactory.getInstance()

      // Getting tweets with keyword "Azure" and sending them to the Event Hub in realtime!
      val query = new Query(" #Azure ")
      query.setCount(100)
      query.lang("en")
      var finished = false
      while (!finished) {
        val result = twitter.search(query)
        val statuses = result.getTweets()
        var lowestStatusId = Long.MaxValue
        for (status <- statuses.asScala) {
          if(!status.isRetweet()){
            sendEvent(status.getText(), 5000)
          }
          lowestStatusId = Math.min(status.getId(), lowestStatusId)
        }
        query.setMaxId(lowestStatusId - 1)
      }

    } else if (dataSource == "test") {
      // Loop through the list of test input data
      while (true) {
        testSource.foreach {
          sendEvent(_,5000)
        }
      }

    } else {
      System.out.println("Unsupported Data Source. Set 'dataSource' to \"twitter\" or \"test\"")
    }

    // Closing connection to the Event Hub
    eventHubClient.get().close()
```

Not defterlerini çalıştırmak için **SHIFT + ENTER** tuşlarına basın. Aşağıdaki kod parçacığına benzer bir çıktı görürsünüz. Çıktıdaki her olay, Event Hubs’a alınan ve “Azure” terimini içeren bir tweet’tir.

    Sent event: @Microsoft and @Esri launch Geospatial AI on Azure https://t.co/VmLUCiPm6q via @geoworldmedia #geoai #azure #gis #ArtificialIntelligence

    Sent event: Public preview of Java on App Service, built-in support for Tomcat and OpenJDK
    https://t.co/7vs7cKtvah
    #cloudcomputing #Azure

    Sent event: 4 Killer #Azure Features for #Data #Performance https://t.co/kpIb7hFO2j by @RedPixie

    Sent event: Migrate your databases to a fully managed service with Azure SQL Database Managed Instance | #Azure | #Cloud https://t.co/sJHXN4trDk

    Sent event: Top 10 Tricks to #Save Money with #Azure Virtual Machines https://t.co/F2wshBXdoz #Cloud

    ...
    ...

## <a name="read-tweets-from-event-hubs"></a>Event Hubs’tan tweet’leri okuma

**AnalyzeTweetsFromEventHub** not defterine aşağıdaki kodu yapıştırın ve yer tutucuyu, daha önce oluşturduğunuz Azure Event Hubs değerleriyle değiştirin. Bu not defteri, **SendTweetsToEventHub** not defterini kullanarak önceden Event Hubs’ta akışa alınan tweet’leri okur.

```scala

    import org.apache.spark.eventhubs._
    import com.microsoft.azure.eventhubs._

    // Build connection string with the above information
    val namespaceName = "<EVENT HUBS NAMESPACE>"
    val eventHubName = "<EVENT HUB NAME>"
    val sasKeyName = "<POLICY NAME>"
    val sasKey = "<POLICY KEY>"
    val connStr = new com.microsoft.azure.eventhubs.ConnectionStringBuilder()
                .setNamespaceName(namespaceName)
                .setEventHubName(eventHubName)
                .setSasKeyName(sasKeyName)
                .setSasKey(sasKey)

    val customEventhubParameters =
      EventHubsConf(connStr.toString())
      .setMaxEventsPerTrigger(5)

    val incomingStream = spark.readStream.format("eventhubs").options(customEventhubParameters.toMap).load()

    incomingStream.printSchema

    // Sending the incoming stream into the console.
    // Data comes in batches!
    incomingStream.writeStream.outputMode("append").format("console").option("truncate", false).start().awaitTermination()
```

Aşağıdaki çıkışı alırsınız:


    root
     |-- body: binary (nullable = true)
     |-- offset: long (nullable = true)
     |-- seqNumber: long (nullable = true)
     |-- enqueuedTime: long (nullable = true)
     |-- publisher: string (nullable = true)
     |-- partitionKey: string (nullable = true)

    -------------------------------------------
    Batch: 0
    -------------------------------------------
    +------+------+--------------+---------------+---------+------------+
    |body  |offset|sequenceNumber|enqueuedTime   |publisher|partitionKey|
    +------+------+--------------+---------------+---------+------------+
    |[50 75 62 6C 69 63 20 70 72 65 76 69 65 77 20 6F 66 20 4A 61 76 61 20 6F 6E 20 41 70 70 20 53 65 72 76 69 63 65 2C 20 62 75 69 6C 74 2D 69 6E 20 73 75 70 70 6F 72 74 20 66 6F 72 20 54 6F 6D 63 61 74 20 61 6E 64 20 4F 70 65 6E 4A 44 4B 0A 68 74 74 70 73 3A 2F 2F 74 2E 63 6F 2F 37 76 73 37 63 4B 74 76 61 68 20 0A 23 63 6C 6F 75 64 63 6F 6D 70 75 74 69 6E 67 20 23 41 7A 75 72 65]                              |0     |0             |2018-03-09 05:49:08.86 |null     |null        |
    |[4D 69 67 72 61 74 65 20 79 6F 75 72 20 64 61 74 61 62 61 73 65 73 20 74 6F 20 61 20 66 75 6C 6C 79 20 6D 61 6E 61 67 65 64 20 73 65 72 76 69 63 65 20 77 69 74 68 20 41 7A 75 72 65 20 53 51 4C 20 44 61 74 61 62 61 73 65 20 4D 61 6E 61 67 65 64 20 49 6E 73 74 61 6E 63 65 20 7C 20 23 41 7A 75 72 65 20 7C 20 23 43 6C 6F 75 64 20 68 74 74 70 73 3A 2F 2F 74 2E 63 6F 2F 73 4A 48 58 4E 34 74 72 44 6B]            |168   |1             |2018-03-09 05:49:24.752|null     |null        |
    +------+------+--------------+---------------+---------+------------+

    -------------------------------------------
    Batch: 1
    -------------------------------------------
    ...
    ...

Çıkış ikili modda olduğundan, bunu dizeye dönüştürmek için aşağıdaki kod parçacığını kullanın.

```scala
    import org.apache.spark.sql.types._
    import org.apache.spark.sql.functions._

    // Event Hub message format is JSON and contains "body" field
    // Body is binary, so we cast it to string to see the actual content of the message
    val messages =
      incomingStream
      .withColumn("Offset", $"offset".cast(LongType))
      .withColumn("Time (readable)", $"enqueuedTime".cast(TimestampType))
      .withColumn("Timestamp", $"enqueuedTime".cast(LongType))
      .withColumn("Body", $"body".cast(StringType))
      .select("Offset", "Time (readable)", "Timestamp", "Body")

    messages.printSchema

    messages.writeStream.outputMode("append").format("console").option("truncate", false).start().awaitTermination()
```

Çıkış şimdi aşağıdaki kod parçacığına benzer:

    root
     |-- Offset: long (nullable = true)
     |-- Time (readable): timestamp (nullable = true)
     |-- Timestamp: long (nullable = true)
     |-- Body: string (nullable = true)

    -------------------------------------------
    Batch: 0
    -------------------------------------------
    +------+-----------------+----------+-------+
    |Offset|Time (readable)  |Timestamp |Body
    +------+-----------------+----------+-------+
    |0     |2018-03-09 05:49:08.86 |1520574548|Public preview of Java on App Service, built-in support for Tomcat and OpenJDK
    https://t.co/7vs7cKtvah
    #cloudcomputing #Azure          |
    |168   |2018-03-09 05:49:24.752|1520574564|Migrate your databases to a fully managed service with Azure SQL Database Managed Instance | #Azure | #Cloud https://t.co/sJHXN4trDk    |
    |0     |2018-03-09 05:49:02.936|1520574542|@Microsoft and @Esri launch Geospatial AI on Azure https://t.co/VmLUCiPm6q via @geoworldmedia #geoai #azure #gis #ArtificialIntelligence|
    |176   |2018-03-09 05:49:20.801|1520574560|4 Killer #Azure Features for #Data #Performance https://t.co/kpIb7hFO2j by @RedPixie                                                    |
    +------+-----------------+----------+-------+
    -------------------------------------------
    Batch: 1
    -------------------------------------------
    ...
    ...

Artık Azure Etkinlik Hub'larından Azure Veri Tuğlaları'na alabildiğiniz verileri, Apache Spark'ın Etkinlik Hub'ları bağlayıcısını kullanarak neredeyse gerçek zamanlı olarak Azure Veri Tuğlaları'na aktarmış sınız. Spark için Event Hubs bağlayıcısını kullanma hakkında daha fazla bilgi için [bağlayıcı belgesi](https://github.com/Azure/azure-event-hubs-spark/tree/master/docs?WT.mc_id=sparkeventhubs-docs-alehall)ne başvurun.

## <a name="run-sentiment-analysis-on-tweets"></a>Tweet’lerle ilgili yaklaşım analizi çalıştırma

Bu bölümde, Twitter API’si kullanılarak alınan tweet’lerde yaklaşım analizi çalıştıracaksınız. Bu bölüm için, aynı **AnalyzeTweetsFromEventHub** not defterine kod parçacıklarını eklersiniz.

Not defterine yeni bir kod hücresi ekleyerek başlayın ve aşağıda verilen kod parçacığını yapıştırın. Bu kod parçacığı, Dil ve Yaklaşım API’si ile çalışmaya yönelik veri türlerini tanımlar.

```scala
import java.io._
import java.net._
import java.util._

case class Language(documents: Array[LanguageDocuments], errors: Array[Any]) extends Serializable
case class LanguageDocuments(id: String, detectedLanguages: Array[DetectedLanguages]) extends Serializable
case class DetectedLanguages(name: String, iso6391Name: String, score: Double) extends Serializable

case class Sentiment(documents: Array[SentimentDocuments], errors: Array[Any]) extends Serializable
case class SentimentDocuments(id: String, score: Double) extends Serializable

case class RequestToTextApi(documents: Array[RequestToTextApiDocument]) extends Serializable
case class RequestToTextApiDocument(id: String, text: String, var language: String = "") extends Serializable
```

Yeni bir kod hücresi ekleyin ve aşağıda sağlanan kod parçacığını yapıştırın. Bu kod parçacığı, dil algılama ve yaklaşım analizi çalıştırmak için Metin Analizi API’sini çağırma işlevlerini içeren bir nesneyi tanımlar. Yer tutucuyu `<PROVIDE ACCESS KEY HERE>` Bilişsel Hizmetler hesabınız için aldığınız değerle değiştirdiğinizden emin olun.

```scala
import javax.net.ssl.HttpsURLConnection
import com.google.gson.Gson
import com.google.gson.GsonBuilder
import com.google.gson.JsonObject
import com.google.gson.JsonParser
import scala.util.parsing.json._

object SentimentDetector extends Serializable {

    // Cognitive Services API connection settings
    val accessKey = "<PROVIDE ACCESS KEY HERE>"
    val host = "https://cognitive-docs.cognitiveservices.azure.com/"
    val languagesPath = "/text/analytics/v2.1/languages"
    val sentimentPath = "/text/analytics/v2.1/sentiment"
    val languagesUrl = new URL(host+languagesPath)
    val sentimenUrl = new URL(host+sentimentPath)
    val g = new Gson

    def getConnection(path: URL): HttpsURLConnection = {
        val connection = path.openConnection().asInstanceOf[HttpsURLConnection]
        connection.setRequestMethod("POST")
        connection.setRequestProperty("Content-Type", "text/json")
        connection.setRequestProperty("Ocp-Apim-Subscription-Key", accessKey)
        connection.setDoOutput(true)
        return connection
    }

    def prettify (json_text: String): String = {
        val parser = new JsonParser()
        val json = parser.parse(json_text).getAsJsonObject()
        val gson = new GsonBuilder().setPrettyPrinting().create()
        return gson.toJson(json)
    }

    // Handles the call to Cognitive Services API.
    def processUsingApi(request: RequestToTextApi, path: URL): String = {
        val requestToJson = g.toJson(request)
        val encoded_text = requestToJson.getBytes("UTF-8")
        val connection = getConnection(path)
        val wr = new DataOutputStream(connection.getOutputStream())
        wr.write(encoded_text, 0, encoded_text.length)
        wr.flush()
        wr.close()

        val response = new StringBuilder()
        val in = new BufferedReader(new InputStreamReader(connection.getInputStream()))
        var line = in.readLine()
        while (line != null) {
            response.append(line)
            line = in.readLine()
        }
        in.close()
        return response.toString()
    }

    // Calls the language API for specified documents.
    def getLanguage (inputDocs: RequestToTextApi): Option[Language] = {
        try {
            val response = processUsingApi(inputDocs, languagesUrl)
            // In case we need to log the json response somewhere
            val niceResponse = prettify(response)
            // Deserializing the JSON response from the API into Scala types
            val language = g.fromJson(niceResponse, classOf[Language])
            if (language.documents(0).detectedLanguages(0).iso6391Name == "(Unknown)")
                return None
            return Some(language)
        } catch {
            case e: Exception => return None
        }
    }

    // Calls the sentiment API for specified documents. Needs a language field to be set for each of them.
    def getSentiment (inputDocs: RequestToTextApi): Option[Sentiment] = {
        try {
            val response = processUsingApi(inputDocs, sentimenUrl)
            val niceResponse = prettify(response)
            // Deserializing the JSON response from the API into Scala types
            val sentiment = g.fromJson(niceResponse, classOf[Sentiment])
            return Some(sentiment)
        } catch {
            case e: Exception => return None
        }
    }
}
```

Duyarlılığı belirleyen bir Kıvılcım UDF (Kullanıcı tanımlı işlev) tanımlamak için başka bir hücre ekleyin.

```scala
// User Defined Function for processing content of messages to return their sentiment.
val toSentiment =
    udf((textContent: String) =>
        {
            val inputObject = new RequestToTextApi(Array(new RequestToTextApiDocument(textContent, textContent)))
            val detectedLanguage = SentimentDetector.getLanguage(inputObject)
            detectedLanguage match {
                case Some(language) =>
                    if(language.documents.size > 0) {
                        inputObject.documents(0).language = language.documents(0).detectedLanguages(0).iso6391Name
                        val sentimentDetected = SentimentDetector.getSentiment(inputObject)
                        sentimentDetected match {
                            case Some(sentiment) => {
                                if(sentiment.documents.size > 0) {
                                    sentiment.documents(0).score.toString()
                                }
                                else {
                                    "Error happened when getting sentiment: " + sentiment.errors(0).toString
                                }
                            }
                            case None => "Couldn't detect sentiment"
                        }
                    }
                    else {
                        "Error happened when getting language" + language.errors(0).toString
                    }
                case None => "Couldn't detect language"
            }
        }
    )
```

Tweet’in içeriği ve tweet ile ilişkili yaklaşım ile bir veri çerçevesi hazırlamak için son bir kod hücresi ekleyin.

```scala
// Prepare a dataframe with Content and Sentiment columns
val streamingDataFrame = incomingStream.selectExpr("cast (body as string) AS Content").withColumn("Sentiment", toSentiment($"Content"))

// Display the streaming data with the sentiment
streamingDataFrame.writeStream.outputMode("append").format("console").option("truncate", false).start().awaitTermination()
```

Aşağıdaki kod parçacığı gibi bir çıktı görmeniz gerekir:

    -------------------------------------------
    Batch: 0
    -------------------------------------------
    +--------------------------------+------------------+
    |Content                         |Sentiment         |
    +--------------------------------+------------------+
    |Public preview of Java on App Service, built-in support for Tomcat and OpenJDK
    https://t.co/7vs7cKtvah   #cloudcomputing #Azure          |0.7761918306350708|
    |Migrate your databases to a fully managed service with Azure SQL Database Managed Instance | #Azure | #Cloud https://t.co/sJHXN4trDk    |0.8558163642883301|
    |@Microsoft and @Esri launch Geospatial AI on Azure https://t.co/VmLUCiPm6q via @geoworldmedia #geoai #azure #gis #ArtificialIntelligence|0.5               |
    |4 Killer #Azure Features for #Data #Performance https://t.co/kpIb7hFO2j by @RedPixie                                                    |0.5               |
    +--------------------------------+------------------+

**Yaklaşım** sütununda bir değerin **1** değerine yakın olması, Azure deneyiminin mükemmel olduğunu ifade eder. Bir değerin **0** değerine yakın olması, kullanıcının Microsoft Azure ile çalışırken sorunlarla karşılaştığını ifade eder.

İşte bu kadar! Azure Veri Tuğlaları'nı kullanarak verileri Azure Etkinlik Hub'larına başarıyla aktarmış, Akış Verilerini Olay Hub'ları bağlayıcısını kullanarak tüketmiş ve ardından veri akışı nda neredeyse gerçek zamanlı olarak veri akışı üzerinde duyarlılık çözümlemesi çalıştırmışsınız.

## <a name="clean-up-resources"></a>Kaynakları temizleme

Öğreticiyi çalıştırdıktan sonra kümeyi sonlandırabilirsiniz. Bunu yapmak için Azure Databricks çalışma alanında sol bölmedeki **Kümeler**’i seçin. Sonlandırmak istediğiniz küme için imleci **Eylemler** sütunu altındaki üç noktanın üzerine taşıyın ve **Sonlandır** simgesini seçin.

![Databricks kümesini durdurma](./media/databricks-sentiment-analysis-cognitive-services/terminate-databricks-cluster.png "Databricks kümesini durdurma")

Kümeyi oluştururken **etkinlik dakikalarından \_ \_ sonra Sonlandırma** onay kutusunu seçmeniz koşuluyla, kümeyi el ile sonlandırmazsanız otomatik olarak durur. Böyle bir durumda, belirtilen süre boyunca etkin olmaması durumunda küme otomatik olarak durdurulur.

## <a name="next-steps"></a>Sonraki adımlar
Bu öğreticide, verileri Azure Event Hubs’ta akışa almak ve sonra gerçek zamanlı olarak Event Hubs’tan akış verilerini okumak için Azure Databricks’i kullanmayı öğrendiniz. Şunları öğrendiniz:
> [!div class="checklist"]
> * Azure Databricks çalışma alanı oluşturma
> * Azure Databricks’te Spark kümesi oluşturma
> * Akış verilerine erişmek için bir Twitter uygulaması oluşturma
> * Azure Databricks’te not defterleri oluşturma
> * Event Hubs ve Twitter API’si için kitaplıklar ekleme
> * Microsoft Bilişsel Hizmetler hesabı oluşturma ve erişim anahtarını alma
> * Event Hubs’a tweet’ler gönderme
> * Event Hubs’tan tweet’leri okuma
> * Tweet’lerle ilgili yaklaşım analizi çalıştırma

Azure Databricks kullanarak makine öğrenmesi görevleri gerçekleştirme hakkında bilgi edinmek için sonraki öğreticiye ilerleyin.

> [!div class="nextstepaction"]
>[Azure Databricks kullanarak Makine Öğrenimi](/azure/databricks/applications/machine-learning/mllib/decision-trees)

