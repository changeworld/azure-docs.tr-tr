---
title: Azure Uygulama Öngörüleri ile Visual Studio'da Hata Ayıklama
description: Hata ayıklama ve üretim sırasında wen uygulaması performans analizi ve tanılama.
ms.topic: conceptual
author: NumberByColors
ms.author: daviste
ms.date: 07/07/2017
ms.reviewer: mbullwin
ms.custom: vs-azure
ms.openlocfilehash: 8905222214d58eeba24ecf50da768ffa1d65c39d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77670891"
---
# <a name="debug-your-applications-with-azure-application-insights-in-visual-studio"></a>Visual Studio'daki Azure Uygulama Öngörüleri ile uygulamalarınızı hata ayıklama
Visual Studio’da (2015 ve sonraki sürümler) hem hata ayıklama hem de üretim sırasında [Azure Application Insights](../../azure-monitor/app/app-insights-overview.md)’tan alınan telemetri verilerini kullanarak, ASP.NET web uygulamanızdaki performansı çözümleyebilir ve sorunları tanılayabilirsiniz.

ASP.NET web uygulamanızı Visual Studio 2017 veya sonraki bir sürümle oluşturduysanız, Application Insights SDK’sı zaten yüklüdür. Diğer sürümlerde, henüz yapmadıysanız [uygulamanıza Application Insights ekleyin](../../azure-monitor/app/asp-net.md).

Uygulamanızı canlı üretim sırasında izlemek için, normalde uyarlar ayarlayıp güçlü izleme araçları uygulayabileceğiniz [Azure portaldaki](https://portal.azure.com) Application Insights telemetrisini görüntülersiniz. Ancak, hata ayıklama için ayrıca Visual Studio’da telemetriyi arayıp çözümleyebilirsiniz. Visual Studio'yı hem üretim alanınızdan hem de geliştirme makinenizdeki hata ayıklama çalışmalarından telemetriyi analiz etmek için kullanabilirsiniz. İkinci durumda, SDK’yı henüz Azure portala telemetri gönderecek şekilde yapılandırmadıysanız bile hata ayıklama çalıştırmalarını çözümleyebilirsiniz. 

## <a name="debug-your-project"></a><a name="run"></a>Projenizi hata ayıklama
F5 kullanarak web uygulamanızı yerel hata ayıklama modunda çalıştırın. Farklı sayfalar açarak telemetri verileri oluşturun.

Visual Studio'da, projenizde Application Insights modülü tarafından günlüğe kaydedilmiş olayların sayısını görürsünüz.

![Visual Studio'da, hata ayıklama sırasında Application Insights düğmesi gösterilir.](./media/visual-studio/appinsights-09eventcount.png)

Telemetrinizde arama yapmak için bu düğmeye tıklayın. 

## <a name="application-insights-search"></a>Application Insights araması
Application Insights Arama penceresi günlüğe kaydedilmiş olayları gösterir. (Application Insights'ı ayarlarken Azure'da oturum açtıysanız, Azure portalında aynı etkinlikleri arayabilirsiniz.)

![Projeye sağ tıklayın ve Application Insights, Ara’yı seçin](./media/visual-studio/34.png)

> [!NOTE] 
> Filtreleri seçtikten veya seçimini kaldırdıktan sonra, metin arama alanının sonundaki Ara düğmesine tıklayın.
>

Serbest metin arama işlevi olaylardaki tüm alanlarda çalışır. Örneğin, bir sayfanın URL’sinin bir kısmını ya da istemcinin şehri gibi bir özelliğin değerini veya bir izleme günlüğündeki belirli kelimeleri arayın.

Ayrıntılı özelliklerini görmek için herhangi bir etkinliğe tıklayın.

Web uygulamanıza gönderilen istekler için koda tıklayabilirsiniz.

![İstek Ayrıntıları altındaki koda tıklayın](./media/visual-studio/31.png)

Başarısız isteklerin veya özel durumların tanılanmasına yardımcı olması için ilgili öğeleri de açabilirsiniz.

![İstek Ayrıntıları altında ilgili öğelere gidin](./media/visual-studio/41.png)

## <a name="view-exceptions-and-failed-requests"></a>Özel durumları ve başarısız istekleri görüntüleme
Arama penceresinde özel durum raporları gösterilir. (ASP.NET uygulamasının bazı eski türlerinde, çerçeve tarafından işlenen özel durumları görmek için [özel durum izlemeyi ayarlamanız](../../azure-monitor/app/asp-net-exceptions.md) gerekir.)

Yığın izlemesi almak için bir özel duruma tıklayın. Visual Studio’da uygulamanın kodu açıksa yığın izlemesinden tıklayarak ilgili kod satırına gidebilirsiniz.

![Özel durum yığın izlemesi](./media/visual-studio/17.png)

## <a name="view-request-and-exception-summaries-in-the-code"></a>Koddaki istek ve özel durum özetlerini görüntüleme
Her işleyici yönteminin üzerindeki Kod Lens satırında, application Insights tarafından son 24 saat içinde günlüğe kaydedilen istek ve özel durumların sayısını görürsünüz.

![Özel durum yığın izlemesi](./media/visual-studio/21.png)

> [!NOTE] 
> Kod Odağı, Application Insights verilerini yalnızca [uygulamanızı Application Insights portalına telemetri gönderecek şekilde yapılandırdıysanız](../../azure-monitor/app/asp-net.md) gösterir.
>

[Kod Odağı’nda Application Insights hakkında daha fazla bilgi](../../azure-monitor/app/visual-studio-codelens.md)

## <a name="trends"></a>Eğilimler
Eğilimler, uygulamanızın zaman içinde nasıl davrandığını görselleştirmeye yönelik bir araçtır. 

Application Insights araç çubuğu düğmesinden veya Application Insights Arama penceresinden **Telemetri Eğilimlerini Keşfet**’i seçin. Başlamak için beş genel sorgudan birini seçin. Telemetri türleri, zaman aralıkları ve diğer özelliklere göre farklı veri kümelerini çözümleyebilirsiniz. 

Verilerinizdeki anormallikleri bulmak için "Görünüm Türü" açılır listesi altındaki anormallik seçeneklerinden birini belirleyin. Pencerenin altındaki filtreleme seçenekleri, telemetrinizdeki belirli alt kümelere odaklanmayı kolaylaştırır.

![Eğilimler](./media/visual-studio/51.png)

[Eğilimler hakkında daha fazla bilgi](../../azure-monitor/app/visual-studio-trends.md).

## <a name="local-monitoring"></a>Yerel izleme
(Visual Studio 2015 Güncelleme 2) SDK'yı Uygulama Öngörüleri portalına telemetri gönderecek şekilde yapılandırmadıysanız (ApplicationInsights.config'de enstrümantasyon anahtarı olmaması için) tanılama penceresi en son hata ayıklama oturumunuzdaki telemetriyi görüntüler. 

Daha önce uygulamanızın önceki bir sürümünü yayımladıysanız bu iyi bir şeydir. Hata ayıklama oturumlarınızdan alınan telemetrinin, yayımlanan uygulamanın Application Insights portalındaki telemetriyle karışmasını istemezsiniz.

Telemetriyi portala göndermeden önce hatalarını ayıklamak istediğiniz [özel telemetri](../../azure-monitor/app/api-custom-events-metrics.md) verilerine sahip olmanız da yararlı olur.

* *İlk başta, portala telemetri göndermek için Uygulama Öngörüleri'ni tamamen yapılandırıldım. Ama şimdi telemetriyi sadece Visual Studio'da görmek istiyorum.*
  
  * Arama penceresinin Ayarlar bölümünde, uygulamanız portala telemetri gönderiyor olsa bile yerel tanılamalarda arama seçeneği vardır.
  * Telemetrinin portala gönderilmesini durdurmak için `<instrumentationkey>...` ApplicationInsights.config'den satırı kısayorum. Telemetriyi tekrar portala göndermeye hazır olduğunuzda, yorumyapmayı bırakın.


## <a name="next-steps"></a>Sonraki adımlar
|  |  |
| --- | --- |
| **[Daha fazla veri ekleme](../../azure-monitor/app/asp-net-more.md)**<br/>Kullanımı, kullanılabilirliği, bağımlılıkları, özel durumları izleyin. Günlük altyapılarından izlemeleri tümleştirin. Özel telemetri yazın. |![Visual studio](./media/visual-studio/64.png) |
| **[Application Insights portalıyla çalışma](../../azure-monitor/app/overview-dashboard.md)**<br/>Panoları, güçlü tanılama ve analitik araçları, uyarıları, uygulamanızın canlı bağımlılık haritasını ve dışa aktarılan telemetri verilerini görüntüleyin. |![Visual studio](./media/visual-studio/62.png) |

