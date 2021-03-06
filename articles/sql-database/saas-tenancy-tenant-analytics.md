---
title: Ayıklanmış verileri kullanarak kiracılar arası analiz
description: Tek bir kiracı uygulamasında birden çok Azure SQL Veritabanı veritabanından çıkarılan verileri kullanarak kiracılar arası analiz sorguları.
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: anjangsh,billgib,genemi
ms.date: 12/18/2018
ms.openlocfilehash: c589d9619da8b5150d0fb4752625571c48393552
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/27/2020
ms.locfileid: "73826375"
---
# <a name="cross-tenant-analytics-using-extracted-data---single-tenant-app"></a>Çıkarılan verileri kullanarak çapraz kiracı analitiği - tek kiracıuygulaması
 
Bu öğreticide, tek bir kiracı uygulaması için tam bir analiz senaryosunda yürürsiniz. Senaryo, analitiğin işletmelerin akıllı kararlar almalarını nasıl mümkün kılabilir olduğunu gösterir. Her kiracı veritabanından çıkarılan verileri kullanarak, örnek Wingtip Tickets SaaS uygulamasını kullanmaları da dahil olmak üzere kiracı davranışı hakkında bilgi edinmek için analitiği kullanırsınız. Bu senaryo üç adım içerir: 

1.  Her kiracı veritabanından veri **ayıklayın** ve bir analiz deposuna **yükleyin.**
2.  Analiz işleme için **çıkarılan verileri dönüştürün.**
3.  Karar verme de yönlendirebilecek yararlı öngörüler ortaya çıkarmak için **iş zekası** araçlarını kullanın. 

Bu öğreticide şunların nasıl yapıldığını öğrenirsiniz:

> [!div class="checklist"]
> - Verileri ayıklamak için kiracı analiz deposunu oluşturun.
> - Her kiracı veritabanından analiz deposuna veri ayıklamak için esnek işleri kullanın.
> - Çıkarılan verileri optimize edin (yıldız şemasını yeniden düzenleyin).
> - Analitik veritabanını sorgulayın.
> - Kiracı verilerindeki eğilimleri vurgulamak ve iyileştirmeler için önerilerde bulunmak için veri görselleştirme için Power BI'yi kullanın.

![architectureOverView](media/saas-tenancy-tenant-analytics/architectureOverview.png)

## <a name="offline-tenant-analytics-pattern"></a>Çevrimdışı kiracı analiz deseni

Çok kiracılı SaaS uygulamaları genellikle bulutta depolanan büyük miktarda kiracı verisine sahiptir. Bu veriler, uygulamanızın çalışması ve kullanımı ve kiracılarınızın davranışları hakkında zengin bir öngörü kaynağı sağlar. Bu öngörüler özellik geliştirme, kullanılabilirlik iyileştirmeleri ve uygulama ve platformdaki diğer yatırımlara rehberlik edebilir.

Tüm veriler tek bir çok kiracıveritabanında olduğunda tüm kiracılar için verilere erişmek kolaydır. Ancak, potansiyel olarak binlerce veritabanları arasında ölçekte dağıtıldığında erişim daha karmaşıktır. Karmaşıklığı evcilleştirmenin ve analitik sorguların işlem verileri üzerindeki etkisini en aza indirmenin bir yolu, verileri özel tasarlanmış bir analitik veritabanına veya veri ambarına ayıklamaktır.

Bu öğretici Wingtip Tickets SaaS uygulaması için eksiksiz bir analiz senaryosu sunar. İlk olarak, *Elastik İşler* her kiracı veritabanından veri ayıklamak ve bir analiz deposunda hazırlama tablolarına yüklemek için kullanılır. Analiz deposu bir SQL Veritabanı veya SQL Veri Ambarı olabilir. Büyük ölçekli veri ayıklama için [Azure Veri Fabrikası](../data-factory/introduction.md) önerilir.

Daha sonra, toplanan veriler yıldız [şema](https://www.wikipedia.org/wiki/Star_schema) tabloları kümesine dönüştürülür. Tablolar merkezi bir olgu tablosu artı ilgili boyut tablolarından oluşur.  Wingtip Biletleri için:

- Yıldız şemasındaki merkezi gerçek tablosu bilet verilerini içerir.
- Boyut tabloları mekanları, olayları, müşterileri ve satın alma tarihlerini açıklar.

Merkezi gerçek ve boyut tabloları birlikte verimli analitik işleme sağlar. Bu öğreticide kullanılan yıldız şema aşağıdaki resimde gösterilmiştir:
 
![architectureOverView](media/saas-tenancy-tenant-analytics/StarSchema.png)

Son olarak, analiz mağazası, kiracı davranışı ve Wingtip Biletleri uygulamasını kullanma larına ilişkin öngörüleri vurgulamak için **PowerBI** kullanılarak sorgulanır. Şu sorguları çalıştırın:
 
- Her mekanın göreceli popülaritesini göster
- Farklı etkinlikler için bilet satışlarındaki desenleri vurgulayın
- Farklı mekanların etkinliklerini satmadaki göreceli başarısını göster

Her kiracının hizmeti nasıl kullandığını anlamak, hizmetten para kazanma ve kiracıların daha başarılı olması için hizmeti geliştirme seçeneklerini araştırmak için kullanılır. Bu öğretici, kiracı verilerinden elde edilebilen içgörü türlerinin temel örneklerini sağlar.

## <a name="setup"></a>Kurulum

### <a name="prerequisites"></a>Ön koşullar

Bu öğreticiyi tamamlamak için aşağıdaki ön koşulların karşılandığından emin olun:

- Wingtip Tickets SaaS Veritabanı Kiracı Başına uygulaması dağıtılır. Beş dakikadan kısa bir süre içinde dağıtmak için [Wingtip SaaS uygulamasını dağıt'a](saas-dbpertenant-get-started-deploy.md) bakın ve keşfedin
- Wingtip Biletleri SaaS Veritabanı Kiracı başına komut dosyaları ve uygulama [kaynak kodu](https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant/) GitHub indirilir. İndirme yönergelerine bakın. İçeriğini ayıklamadan önce *zip dosyasının engelini kaldırdıktan* emin olun. Wingtip Tickets SaaS komut dosyalarını indirmek ve engelini kaldırmak için gereken adımlar için [genel kılavuza](saas-tenancy-wingtip-app-guidance-tips.md) göz atın.
- Power BI Desktop yüklendi. [Power BI Desktop İndirin](https://powerbi.microsoft.com/downloads/)
- Ek kiracılar toplu sağlanmıştır, [**Provision kiracı öğretici**](saas-dbpertenant-provision-and-catalog.md)bakın.
- Bir iş hesabı ve iş hesabı veritabanı oluşturuldu. [**Şema yönetimi öğreticisinde**](saas-tenancy-schema-management.md#create-a-job-agent-database-and-new-job-agent)uygun adımları görün.

### <a name="create-data-for-the-demo"></a>Demo için veri oluşturma

Bu eğitimde, analiz bilet satış verileri üzerinde gerçekleştirilir. Geçerli adımda, tüm kiracılar için bilet verileri oluşturursunuz.  Daha sonra bu veriler analiz için ayıklanır. *Anlamlı miktarda veriye sahip olmak için kiracı toplu*işlerini daha önce açıklandığı gibi sağladığınızı sağlayın. Yeterince büyük miktarda veri farklı bilet satın alma desenleri bir dizi ortaya çıkarabilir.

1. PowerShell ISE'de açık *...\Öğrenme Modülleri\Operasyonel Analitik\Kiracı Analytics\Demo-TenantAnalytics.ps1*, ve aşağıdaki değeri ayarlayın:
    - **$DemoScenario** = **1** Tüm mekanlardaki etkinlikler için bilet satın alın
2. Komut dosyasını çalıştırmak ve her mekandaki her etkinlik için bilet satın alma geçmişi oluşturmak için **F5** tuşuna basın.  Senaryo, on binlerce bilet üretmek için birkaç dakika çalışır.

### <a name="deploy-the-analytics-store"></a>Analiz mağazasını dağıtma
Genellikle birlikte tüm kiracı verileri tutun çok sayıda işlem veritabanları vardır. Birçok işlem veritabanındaki kiracı verilerini tek bir analiz deposunda toplamanız gerekir. Toplama, verilerin verimli bir şekilde sorgulanmasını sağlar. Bu öğreticide, toplanan verileri depolamak için bir Azure SQL Veritabanı veritabanı kullanılır.

Aşağıdaki adımlarda, **kiracı analitiği**olarak adlandırılan analiz mağazasını dağıtırsınız. Ayrıca, öğreticide daha sonra doldurulan önceden tanımlanmış tabloları da dağıtAbilirsiniz:
1. PowerShell ISE'de açık *...\Öğrenme Modülleri\Operasyonel Analitik\Kiracı Analizi\Demo-TenantAnalytics.ps1* 
2. Komut dosyasındaki $DemoScenario değişkenini seçtiğiniz analiz deposuyla eşleşecek şekilde ayarlayın:
    - Sütun deposu olmadan SQL veritabanını kullanmak **için,** = **2** $DemoScenario ayarlayın
    - Sütun deposu ile SQL veritabanını kullanmak için,**3** **$DemoScenario** = ayarlayın  
3. Kiracı analiz mağazasını oluşturan demo komut dosyasını *\<(Deploy-TenantAnalytics XX>.ps1* komut dosyası olarak adlandırır) çalıştırmak için **F5** tuşuna basın. 

Şimdi uygulama dağıtılan ve ilginç kiracı verileri ile doldurdu, kiracıbağlamak için [SQL Server Management Studio (SSMS)](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) **kullanın1-dpt-&lt;Kullanıcı&gt; ** ve **katalog-dpt-&lt;Kullanıcı&gt; ** sunucuları Login = *geliştirici*kullanarak , Şifre = *P\@ssword1*. Daha fazla rehberlik için [giriş öğreticisi](saas-dbpertenant-wingtip-app-overview.md) bakın.

![architectureOverView](media/saas-tenancy-tenant-analytics/ssmsSignIn.png)

Nesne Gezgini'nde aşağıdaki adımları gerçekleştirin:

1. *Kiracı1-dpt-&lt;Kullanıcı&gt; * sunucusunu genişletin.
2. Veritabanları düğümlerini genişletin ve kiracı veritabanları listesine bakın.
3. *&lt;Katalog-dpt- Kullanıcı&gt; * sunucusunu genişletin.
4. Analitik deposunu ve iş hesabı veritabanını gördüğünüzden doğrulayın.

Analitik mağaza düğümini genişleterek SSMS Object Explorer'daki aşağıdaki veritabanı öğelerine bakın:

- Tablolar **TicketsRawData** ve **EventsRawData** kiracı veritabanlarından ham çıkarılan verileri tutar.
- Yıldız-şema tabloları **fact_Tickets**, **dim_Customers**, **dim_Venues**, **dim_Events**, ve **dim_Dates**.
- Depolanan yordam, ham veri tablolarından yıldız şema tablolarını doldurmak için kullanılır.

![architectureOverView](media/saas-tenancy-tenant-analytics/tenantAnalytics.png)

## <a name="data-extraction"></a>Veri ayıklama 

### <a name="create-target-groups"></a>Hedef gruplar oluşturma 

İşleme başlamadan önce, iş hesabı ve iş hesabı veritabanını dağıttığınızdan emin olun. Sonraki adım kümesinde, Elastik İşler her kiracı veritabanından veri ayıklamak ve verileri analiz deposunda depolamak için kullanılır. Sonra ikinci iş verileri parçalar ve yıldız şemasındaki tablolara saklar. Bu iki iş, **TenantGroup** ve **AnalyticsGroup**olmak üzere iki farklı hedef gruba karşı çalışır. Ayıklama işi, tüm kiracı veritabanlarını içeren TenantGroup'a karşı çalışır. Parçalama işi, yalnızca analiz mağazasını içeren AnalyticsGroup'a karşı çalışır. Aşağıdaki adımları kullanarak hedef grupları oluşturun:

1. SSMS'te, catalog-dpt-&lt;User'daki&gt;iş **hesabı** veritabanına bağlanın.
2. SSMS'te açık *...\Öğrenme Modülleri\Operasyonel Analitik\Kiracı Analizi\ TargetGroups.sql* 
3. Wingtip @User SaaS uygulamasını dağıtırken `<User>` kullanılan kullanıcı değerini değiştirerek komut dosyasının üst kısmındaki değişkeni değiştirin.
4. İki hedef grubu oluşturan komut dosyasını çalıştırmak için **F5** tuşuna basın.

### <a name="extract-raw-data-from-all-tenants"></a>Tüm kiracılardan ham veri ayıklama

Geniş veri *değişiklikleri, etkinlik ve mekan* verilerinden çok bilet *ve müşteri* verileri için daha sık oluşabilir. Bu nedenle, bilet ve müşteri verilerini ayrı ayrı ve daha sık ayıklama olayını ve mekan verilerini ayıklamaktan daha fazla düşünün. Bu bölümde, iki ayrı iş tanımlar ve zamanlarsınız:

- Bilet ve müşteri verilerini ayıklayın.
- Olay ve mekan verilerini ayıklayın.

Her iş verilerini ayıklar ve analiz deposuna gönderir. Ayrı bir iş, çıkarılan verileri analiz yıldızı şemasına ayırır.

1. SSMS'te, catalog-dpt-&lt;User&gt; sunucusundaki iş **hesabı** veritabanına bağlanın.
2. SSMS'te açık *...\Öğrenme Modülleri\Operasyonel Analitik\Kiracı Analitiği\ExtractTickets.sql*.
3. Komut @User dosyasının üst kısmında değiştirin ve Wingtip SaaS uygulamasını dağıttığınızda kullanılan kullanıcı adı ile değiştirin `<User>` 
4. Her kiracı veritabanından bilet ve müşteri verilerini ayıklayan işi oluşturan ve çalıştıran komut dosyasını çalıştırmak için F5 tuşuna basın. İş verileri analiz deposuna kaydeder.
5. Tablonun tüm kiracılardan gelen bilet bilgileriyle dolu olduğundan emin olmak için, kiracıların veri tabanındaki TicketsRawData tablosunu sorgulayın.

![biletÖzler](media/saas-tenancy-tenant-analytics/ticketExtracts.png)

Bu süre dışında önceki adımları yineleyin **,\ExtractTickets.sql'i** **2.**

İşi başarıyla çalıştıran olaylar, tüm kiracılardan gelen yeni etkinlikler ve mekanlar bilgileriyle birlikte analytics deposundaki EventsRawData tablosunu doldurur. 

## <a name="data-reorganization"></a>Veri yeniden düzenlenmesi

### <a name="shred-extracted-data-to-populate-star-schema-tables"></a>Yıldız şema tablolarını doldurmak için çıkarılan verileri parçalama

Bir sonraki adım, ayıklanan ham verileri analitik sorgular için optimize edilmiş bir tablo kümesine parçalamaktır. Bir yıldız şeması kullanılır. Merkezi bir durum tablosu nda tek tek bilet satış kayıtları yer ayırttı. Diğer tablolar, mekanlar, etkinlikler ve müşterilerle ilgili verilerle doldurulur. Ve zaman boyutu tabloları vardır. 

Öğreticinin bu bölümünde, çıkarılan ham verileri yıldız şema tablolarında bulunan verilerle birleştiren bir iş tanımlar ve çalıştırın. Birleştirme işi tamamlandıktan sonra, ham veriler silinir ve tabloları bir sonraki kiracı veri ayıklama işi tarafından doldurulmaya hazır bırakır.

1. SSMS'te, catalog-dpt-&lt;User'daki&gt;iş **hesabı** veritabanına bağlanın.
2. SSMS olarak, açık *...\Öğrenme Modülleri\Operasyonel Analitik\Kiracı Analytics\ShredRawExtractedData.sql*.
3. Analitik mağazasında sp_ShredRawExtractedData depolanan yordamı çağıran bir işi tanımlamak için komut dosyasını çalıştırmak için **F5** tuşuna basın.
4. İşin başarılı bir şekilde çalışması için yeterli zaman verin.
    - İşlerin **Yaşam Döngüsü** sütununa bakın.iş durumu için jobs_execution tablosu. Devam etmeden önce işin **Başarılı** olduğundan emin olun. Başarılı bir çalıştırma, aşağıdaki grafiğe benzer verileri görüntüler:

![Parçalama](media/saas-tenancy-tenant-analytics/shreddingJob.PNG)

## <a name="data-exploration"></a>Veri arama

### <a name="visualize-tenant-data"></a>Kiracı verilerini görselleştirin

Yıldız şema tablosundaki veriler, analiziniz için gereken tüm bilet satış verilerini sağlar. Büyük veri kümelerinde eğilimleri daha kolay görmek için, grafik olarak görselleştirmeniz gerekir.  Bu bölümde, ayıklamış ve düzenlediğiniz kiracı verilerini işlemek ve görselleştirmek için **Power BI'yi** nasıl kullanacağınızı öğrenirsiniz.

Power BI'ye bağlanmak ve daha önce oluşturduğunuz görünümleri almak için aşağıdaki adımları kullanın:

1. Power BI masaüstünü başlatın.
2. Ana sayfa şeridinden **Veri Al'ı**seçin ve **Daha Fazla...** menüden.
3. Veri **Al** penceresinde Azure SQL Veritabanı'nı seçin.
4. Veritabanı giriş penceresinde sunucu adınızı girin&lt;(katalog-dpt-&gt;Kullanıcı .database.windows.net). **Veri Bağlantısı Modu**için **İçe Aktar'ı** seçin ve ardından Tamam'ı tıklatın. 

    ![signinpowerbi](./media/saas-tenancy-tenant-analytics/powerBISignIn.PNG)

5. Sol bölmede **Veritabanı'nı** seçin, ardından kullanıcı adı = *geliştirici*girin ve parola = *P\@ssword1*girin. **Bağlan**'a tıklayın.  

    ![veritabanı signin](./media/saas-tenancy-tenant-analytics/databaseSignIn.PNG)

6. **Navigator** bölmesinde, analitik veritabanı altında, yıldız şema tablolarını seçin: fact_Tickets, dim_Events, dim_Venues, dim_Customers ve dim_Dates. Ardından **Yükle'yi**seçin. 

Tebrikler! Verileri Power BI'ye başarıyla yükledin. Artık kiracılarınıza bakış açısı kazandırmaya yardımcı olmak için ilginç görselleri keşfetmeye başlayabilirsiniz. Daha sonra, analitiğin Wingtip Tickets iş ekibine veri odaklı öneriler sunmanızı nasıl sağlayabileceğini gözden geçirirsiniz. Öneriler, iş modelini ve müşteri deneyimini optimize etmeye yardımcı olabilir.

Bilet satış verilerini analiz ederek başlangıç yaparak, mekanlardaki kullanım değişimini görürsünüz. Her mekan tarafından satılan toplam bilet sayısının bir çubuk grafiğini çizmek için Power BI'de aşağıdaki seçenekleri seçin. Bilet jeneratöründeki rastgele değişim nedeniyle, sonuçlarınız farklı olabilir.
 
![Toplam BiletByVenues](./media/saas-tenancy-tenant-analytics/TotalTicketsByVenues.PNG)

Yukarıdaki arsa her mekan tarafından satılan bilet sayısı değişir onaylar. Daha fazla bilet satan mekanlar, daha az bilet satan mekanlara göre hizmetinizi daha yoğun bir şekilde kullanır. Burada kaynak tahsisini farklı kiracı ihtiyaçlarına göre uyarlama fırsatı olabilir.

Bilet satışlarının zaman içinde nasıl değiştiğini görmek için verileri daha da analiz edebilirsiniz. Her gün 60 günlük bir süre için satılan toplam bilet sayısını çizmek için Power BI'de aşağıdaki seçenekleri seçin.
 
![SaleVersusDate](./media/saas-tenancy-tenant-analytics/SaleVersusDate.PNG)

Önceki grafikte bazı mekanlar için bilet satışlarının arttığı görüntüler. Bu ani artışlar, bazı mekanların sistem kaynaklarını orantısız bir şekilde tüketiyor olabileceği fikrini güçlendiriyor. Şimdiye kadar ani artışlar meydana geldiğinde belirgin bir desen yoktur.

Daha sonra bu pik satış gün önemini daha fazla araştırmak istiyorum. Biletler satışa çıktıktan sonra bu zirveler ne zaman gerçekleşir? Günde satılan biletleri planlamak için Power BI'de aşağıdaki seçenekleri seçin.

![SaleDayDistribution](./media/saas-tenancy-tenant-analytics/SaleDistributionPerDay.PNG)

Önceki arsa bazı mekanları satış ın ilk gününde bilet çok satmak gösterir. Bu mekanlarda biletler satışa gider gitmez, çılgın bir koşuşturma var gibi görünüyor. Birkaç mekanın bu etkinlik patlaması diğer kiracıların hizmetini etkileyebilir.

Bu çılgın koşuşturmanın bu mekanların barındırılan tüm etkinlikleri için doğru olup olmadığını görmek için verileri tekrar delebilirsiniz. Önceki arsalarda, Contoso Konser Salonu'nun çok fazla bilet sattığını ve Contoso'nun da belirli günlerde bilet satışlarında ani bir artış olduğunu gözlemlediniz. Contoso Konser Salonu için kümülatif bilet satışlarını planlamak için Power BI seçenekleriyle oynayın ve her bir etkinlik için satış trendlerine odaklanın. Tüm etkinlikler aynı satış modelini takip eder mi?

![ContosoSatış](media/saas-tenancy-tenant-analytics/EventSaleTrends.PNG)

Contoso Konser Salonu için önceki arsa deli acele tüm olaylar için olmaz gösterir. Diğer mekanların satış eğilimlerini görmek için filtre seçenekleriyle oynayın.

Bilet satış alışkanlıklarına ilişkin öngörüler Wingtip Tickets'in iş modellerini optimize etmesine yol açabilir. Tüm kiracıları eşit ücretlendirmek yerine, belki wingtip farklı işlem boyutlarına sahip hizmet katmanları sunmalıdır. Günde daha fazla bilet satması gereken daha büyük mekanlara daha yüksek bir hizmet düzeyi anlaşması (SLA) ile daha yüksek bir katman sunulabilir. Bu mekanların veritabanları veritabanı başına daha yüksek kaynak sınırları ile havuza yerleştirilmiş olabilir. Her hizmet katmanında, tahsisatı aşmak için ek ücretler tahsil edilen saatlik satış tahsisi olabilir. Periyodik satış patlamaları olan daha büyük mekanlar daha yüksek katmanlardan yararlanacak ve Wingtip Tickets hizmetlerinden daha verimli bir şekilde para kazanabilir.

Bu arada, bazı Wingtip Biletleri müşterileri, hizmet maliyetini haklı çıkarmak için yeterli bilet satmak için mücadele şikayet. Belki de bu anlayışlar orada düşük performanslı mekanlar için bilet satışlarını artırmak için bir fırsattır. Daha yüksek satışlar, hizmetin algılanan değerini artırır. Sağ tıklayın fact_Tickets ve **Yeni ölçü seçin.** **AverageTicketsSold**adlı yeni ölçü için aşağıdaki ifadeyi girin:

```
AverageTicketsSold = AVERAGEX( SUMMARIZE( TableName, TableName[Venue Name] ), CALCULATE( SUM(TableName[Tickets Sold] ) ) )
```

Göreceli başarılarını belirlemek için her mekan tarafından satılan yüzde biletleri çizmek için aşağıdaki görselleştirme seçeneklerini seçin.

![AvgTicketsByVenues](media/saas-tenancy-tenant-analytics/AvgTicketsByVenues.PNG)

Önceki arsa, çoğu mekan biletlerinin %80'inden fazlasını satsa da, bazılarının koltukların yarısından fazlasını doldurmak için mücadele ettiğini gösteriyor. Her mekan için satılan biletlerin maksimum veya minimum yüzdesini seçmek için Values Well ile oynayın.

Daha önce bilet satışları öngörülebilir desenleri takip eğilimindedir keşfetmek için analiz derinleştirilmiş. Bu keşif, Wingtip Tickets'in dinamik fiyatlandırma önererek düşük performans gösteren mekanların bilet satışlarını artırmasına yardımcı olabilir. Bu keşif, her etkinlik için bilet satışlarını tahmin etmek için makine öğrenimi tekniklerini kullanma fırsatını ortaya çıkarabilir. Bilet satışlarında indirim sunmanın geliri üzerindeki etkisi için de tahminler yapılabilir. Power BI Embedded bir olay yönetimi uygulamasına entegre edilebilir. Tümleştirme, tahmin edilen satışları ve farklı indirimlerin etkisini görselleştirmeye yardımcı olabilir. Uygulama, doğrudan analitik ekrandan uygulanacak optimum bir indirim tasarlamaya yardımcı olabilir.

WingTip uygulamasındaki kiracı verilerindeki eğilimleri gözlemlediniz. Uygulamanın SaaS uygulama satıcıları için iş kararlarını bildirmesinin başka yollarını da düşünebilirsiniz. Satıcılar daha iyi kendi kiracıların ihtiyaçlarına hitap edebilir. Umarım bu öğretici, işletmelerinizi veri odaklı kararlar almak için güçlendirmek için kiracı verileri üzerinde analitik gerçekleştirmek için gerekli araçlarla donatılmıştır.

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide, şunların nasıl yapıldığını öğrendiniz:

> [!div class="checklist"]
> - Önceden tanımlanmış yıldız şeması tabloları içeren bir kiracı analiz veritabanı dağıtılmış
> - Tüm kiracı veritabanından veri ayıklamak için kullanılan elastik işler
> - Analiz için tasarlanmış bir yıldız şemasında çıkarılan verileri tablolarda birleştirme
> - Bir analiz veritabanını sorgula 
> - Kiracı verilerindeki eğilimleri gözlemlemek için veri görselleştirme için Power BI'yi kullanın 

Tebrikler!

## <a name="additional-resources"></a>Ek kaynaklar

- [Wingtip SaaS uygulaması üzerine inşa](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)ek öğreticiler .
- [Elastik İşler](elastic-jobs-overview.md).
- [Çıkarılan verileri kullanarak çapraz kiracı analitiği - çok kiracılı uygulama](saas-multitenantdb-tenant-analytics.md)