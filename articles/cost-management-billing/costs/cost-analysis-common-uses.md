---
title: Azure Maliyet Yönetimi'ndeki yaygın maliyet analizi kullanımları
description: Bu makale, Azure Maliyet Yönetimi'ndeki yaygın maliyet analizi görevlerinden nasıl faydalanabileceğinizi açıklamaktadır.
author: bandersmsft
ms.author: banders
ms.date: 03/31/2020
ms.topic: conceptual
ms.service: cost-management-billing
ms.reviewer: adwise
ms.openlocfilehash: a333cad51e6fc089e7e6994c7b89210b12686cd5
ms.sourcegitcommit: b0ff9c9d760a0426fd1226b909ab943e13ade330
ms.translationtype: HT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/01/2020
ms.locfileid: "80520874"
---
# <a name="common-cost-analysis-uses"></a>Yaygın maliyet analizi kullanımları

Azure Maliyet Yönetimi kullanıcıları genellikle çoğu kullanıcının sorduğu soruların yanıtlarını bulmak ister. Bu makale, Maliyet Yönetimi'ndeki yaygın maliyet analizi görevlerinden sonuç elde etme konusunda size yol gösterecektir.

## <a name="view-cost-breakdown-by-azure-service"></a>Azure hizmetine göre maliyet dökümünü görüntüleme

Azure hizmetine göre maliyet dökümünü görüntülemek, altyapınızın en yüksek maliyetli bölümlerini daha iyi anlamanıza yardımcı olabilir. Örneğin VM işlem maliyetleri düşük olabilir. Ancak VM'lerden çıkan bilgi miktarı nedeniyle önemli düzeyde ağ maliyetleriyle karşı karşıya olabilirsiniz. Azure hizmetlerinizin temel maliyet noktalarını anlamak, hizmet kullanımında gerekli ayarlamaları yapabilme açısından önemlidir.

1. Azure portalında kapsamınıza ait maliyet analizine gidin. Örneğin: **Maliyet Yönetimi + Faturalama** > **Maliyet Yönetimi** > **Maliyet analizi**.
1. **Hizmete göre maliyet**'i seçip **Hizmet katmanı** ölçütüne göre gruplandırın.
1. Görünümü **Tablo** olarak değiştirin.

[![Azure hizmetine göre maliyet dökümü](./media/cost-analysis-common-uses/breakdown-by-service.png)](./media/cost-analysis-common-uses/breakdown-by-service.png#lightbox)

## <a name="view-cost-breakdown-by-azure-resource"></a>Azure kaynağına göre maliyet dökümünü görüntüleme

Hizmetleriniz, Azure kaynaklarıyla oluşturulur. Kaynaklara göre maliyetleri gözden geçirmek, temel maliyet noktalarını hızlıca tanımlamanıza yardımcı olabilir. Bir hizmette çok pahalı kaynaklar varsa tasarruf sağlamak için değişiklik yapabilirsiniz.

1. Azure portalında kapsamınıza ait maliyet analizine gidin. Örneğin: **Maliyet Yönetimi + Faturalama** > **Maliyet Yönetimi** > **Maliyet analizi**.
1. **Kaynağa göre maliyet**'i seçin.
1. Görünümü **Tablo** olarak değiştirin.

[![Azure kaynağına göre maliyet dökümünü görüntüleme](./media/cost-analysis-common-uses/cost-by-resource.png)](./media/cost-analysis-common-uses/cost-by-resource.png#lightbox)

## <a name="view-cost-breakdown-by-selected-dimensions"></a>Seçilen boyutlara göre maliyet dökümünü görüntüleme

Boyutlar, maliyetlerinizi ücretlerinizde gösterilen çeşitli meta verilere göre düzenlemenizi sağlar. Örneğin maliyetlerinizi konuma göre gruplandırabilirsiniz.

1. Azure portalında kapsamınıza ait maliyet analizine gidin. Örneğin: **Maliyet Yönetimi + Faturalama** > **Maliyet Yönetimi** > **Maliyet analizi**.
1. **Gruplandırma ölçütü** filtresini seçin.  
    [![Gruplandırma ölçütü seçin](./media/cost-analysis-common-uses/group-by.png)](./media/cost-analysis-common-uses/group-by.png#lightbox)
1. İsterseniz görünümü daha sonra kullanmak üzere kaydedebilirsiniz.
1. Daha ayrıntılı verileri görüntülemek için grafiğin altındaki pasta grafiğine tıklayın.  
    [![Seçilen boyutlara göre maliyet dökümünü görüntüleme](./media/cost-analysis-common-uses/drill-down.png)](./media/cost-analysis-common-uses/drill-down.png#lightbox)

## <a name="view-costs-per-day-or-by-month"></a>Güne veya aya göre maliyetleri görüntüleme

Günlük ve aylık maliyetleri incelemek, belirli bir haftada veya yılda maliyetlerinizin daha yüksek olduğu dönemi daha iyi anlamanıza yardımcı olabilir. Tatil döneminde daha fazla müşteri trafiği oluyorsa Azure maliyetleriniz de bununla birlikte yükseliyor mu? Cuma, Pazartesi gününden daha mı maliyetli?

1. Azure portalında kapsamınıza ait maliyet analizine gidin. Örneğin: **Maliyet Yönetimi + Faturalama** > **Maliyet Yönetimi** > **Maliyet analizi**.
1. **Ayrıntı düzeyi**'ni **Aylık** veya **Günlük** olarak ayarlayın.

[![Güne göre maliyetleri görüntüleme](./media/cost-analysis-common-uses/daily-granularity.png)](./media/cost-analysis-common-uses/daily-granularity.png#lightbox)


## <a name="view-your-spot-vm-charges"></a>Spot VM ücretlerinizi görüntüleme

Spot VM’ler, kesintilerle baş edebilen iş yükleri için büyük ölçüde maliyet tasarrufu sağlayabilir. İş yükleri, kullanılmayan Azure kapasitesi üzerinde çalıştırılır. Herhangi bir zaman çıkarılabileceklerinden, Spot VM’lerde önemli oranda indirimler bulunur. Spot VM ücretlerinizi görüntülemek için aşağıdaki adımları izleyin.

1. Azure portalında kapsamınıza ait maliyet analizine gidin. Örneğin, **Maliyet Yönetimi + Faturalama** > **Maliyet Yönetimi** > **Maliyet analizi**.
2. **Fiyatlandırma Modeli: Spot** için bir filtre ekleyin.

![Spot VM filtresini gösteren örnek](./media/cost-analysis-common-uses/spot-vm-filter.png)

Fiyatlandırma Modeli boyutu, isteğe bağlı ve rezervasyon ücretlerini görüntülemek için de kullanılır.

## <a name="view-your-reservation-charges"></a>Rezervasyon ücretlerinizi görüntüleme

Ayrılmış örnekler size Azure ile tasarruf etmenizi sağlayan bir yöntem sunar. Rezervasyonlar ile zaman içinde edineceğiniz pek çok kaynak için önden ödeme yapmanız gerekir. Maliyet analizi, faturanıza yansıtıldıkça ücretleri gösterir. Ücretler, gerçek maliyetler veya rezervasyon süreciniz boyunca amorti edilmiş maliyetler olarak gösterilir.

1. Azure portalında kapsamınıza ait maliyet analizine gidin. Örneğin, **Maliyet Yönetimi + Faturalama** > **Maliyet Yönetimi** > **Maliyet analizi**.
1. **Fiyatlandırma Modeli: Rezervasyon** için bir filtre ekleyin.
1. **Kapsam** altında ve gösterilen maliyetin yanında aşağı ok simgesine tıklayın ve **Gerçek maliyet** veya **Amorti edilmiş maliyet** ölçümünü seçin.

![Maliyet ölçümü seçme](./media/cost-analysis-common-uses/metric-cost.png)

Her ölçüm, rezervasyon ücretlerinize ait verilerin nasıl göründüğünü etkiler.

**Gerçek maliyet**: Satın alma işlemini, faturanıza yansıdığında gösterir. Örneğin, Ocak ayında 1200 ABD Doları değerinde bir yıllık rezervasyon satın aldıysanız maliyet analizi, rezervasyon için Ocak ayında 1200 ABD Doları maliyet gösterir. Yılın diğer ayları için rezervasyon maliyeti gösterilmez. Gerçek maliyetlerinizi VM’ye göre gruplandırırsanız belirli bir aya ait rezervasyon avantajını alan VM o ay için sıfır maliyete sahip olur.

**Amorti edilmiş maliyet**: Rezervasyon satın alma işlemini, rezervasyon süresi boyunca bölünen amorti edilmiş maliyet olarak gösterir. Ocak ayında 1200 ABD Doları değerinde bir yıllık rezervasyon satın alırsanız maliyet analizi yukarıdaki örneğin aynısını kullanarak yıl boyunca her ay 100 ABD Doları maliyet gösterir. Bu örnekte maliyetleri VM’ye göre gruplandırırsanız rezervasyon avantajını alan her VM’ye yansıtılmış maliyeti görürsünüz.

## <a name="view-your-reservation-utilization"></a>Rezervasyon kullanımınızı görüntüleme

Rezervasyon satın aldıktan sonra, ödediğiniz kadarını aldığınızdan emin olmak için kullanımını izlemek önemlidir. Örneğin, bir yıl için 10 VM satın aldınız ancak yalnızca beş tanesini kullanıyorsunuz. Bu durumda satın aldığınız sanal makinelerin yarısı harcanmış olur. Kullanımınızı değerlendirmek için iki farklı yol vardır:

### <a name="view-unused-ri-costs-in-cost-analysis"></a>Maliyet analizinde kullanılmayan RI maliyetlerini görüntüleme

Rezervasyon satın alımınızda her ay ne kadarlık maliyetin harcandığını belirlemek için aşağıdaki adımları izleyin.

1. Azure portalında rezervasyonunuzun uygulandığı kapsamı içeren maliyet analizine gidin. Örneğin, **Maliyet Yönetimi + Faturalama** > **Maliyet Yönetimi** > **Maliyet analizi**.
1. **Fiyatlandırma Modeli: Rezervasyon** için bir filtre ekleyin.
1. **Amorti Edilmiş Maliyet** görünümünü seçin.
1. Ayrıntı düzeyini **Aylık** olarak ayarlayın.
1. Zaman aralığını geçerli yıl veya rezervasyon döneminiz olarak ayarlayın.
1. Grafik türünü **Sütun (yığılmış)** olarak ayarlayın.
1. Ücretleri **Ücret Türü**’ne göre gruplandırın.
1. `unusedreservation` değerleri için sonuçları gözden geçirin.

[![Rezervasyon kullanımını gösteren örnek](./media/cost-analysis-common-uses/view-reservation-cost.png)](./media/cost-analysis-common-uses/view-reservation-cost.png#lightbox)

### <a name="view-utilization-in-reservations"></a>Rezervasyonlarda kullanımı görüntüleme

Ayrıntılı yönergeler için bkz. [Rezervasyon kullanımını iyileştirme](../reservations/manage-reserved-vm-instance.md#optimize-reservation-use).

## <a name="view-costs-for-a-specific-tag"></a>Belirli bir etikete göre maliyetleri görüntüleme

Çoğu Azure kullanıcısı, ücretleri daha iyi sınıflandırabilmek için kaynaklarına maliyet merkezi veya geliştirme ortamı (üretim ve test) gibi etiketler ekliyor. Etiketler, maliyet analizinde boyut olarak görünür. Özel etiketleme kategorileriniz hakkında içgörü elde etmek için ilgili boyutu kullanabilirsiniz.

Etiket desteği, etiket kaynağa uygulandıktan *sonra* bildirilen kullanımlar için geçerlidir. Etiketler, maliyet toplamları için geriye dönük olarak uygulanmaz.

1. Azure portalında kapsamınıza ait maliyet analizine gidin. Örneğin: **Maliyet Yönetimi + Faturalama** > **Maliyet Yönetimi** > **Maliyet analizi**.
1. Etiketiniz için **Gruplandırma ölçütü** seçin.

[![Belirli bir etikete göre maliyetleri görüntüleme](./media/cost-analysis-common-uses/tag.png)](./media/cost-analysis-common-uses/tag.png#lightbox)

## <a name="download-your-usage-details"></a>Kullanım verilerinizi indirme

CSV biçimindeki kullanım ayrıntıları dosyanız, bir faturaya dahil edilen tüm ücretlerle ilgili bir döküm sunar. Raporunuzu faturanızla karşılaştırabilir ve daha fazla bilgi sahibi olabilirsiniz. Faturanızdaki her bir ücret, kullanım raporunuzdaki ilgili ücret dökümlerine karşılık gelir.

1. Azure portalında bir ödeme hesabı veya abonelik için **Kullanım ve Ücretler** sekmesine gidin. Örneğin: **Maliyet Yönetimi + Faturalandırma** > **Faturalandırma** > **Kullanım + ücretler**.
1. İndirmek istediğiniz satır öğesini seçin ve ardından indirme simgesine tıklayın.  
    [![Kullanımı ve ücretleri indirme](./media/cost-analysis-common-uses/download1.png)](./media/cost-analysis-common-uses/download1.png#lightbox)
1.  İndirilecek kullanım dosyasını seçin.  
    ![İndirilecek kullanım dosyasını seçin](./media/cost-analysis-common-uses/download2.png)

## <a name="view-monthly-ea-cost-breakdown"></a>Aylık EA maliyet dökümünü görüntüleme

EA kaydınız, kuruluşun tamamına ait maliyetlerin tahakkuk ettiği yerdir. Maliyetlerin zaman içinde nasıl tahakkuk ettiğini ve faturalandığını anlamak, maliyetlerin doğru şekilde yönetilmesini sağlamak için uygun proje katılımcılarıyla etkileşim kurmanıza yardımcı olur.

Maliyetler yalnızca etkin kaydınız için gösterilir. Bir kaydı (etkin olmayan) yeni bir kayda (etkin) aktardıysanız, önceki kayda yönelik maliyetler Maliyet Yönetimi’nde gösterilmez.


1. Azure portalında **Maliyet Yönetimi + Faturalandırma** > **Genel Bakış**'a gidin.
1. Geçerli aya ait **Döküm**'e tıklayın ve parasal taahhüdünüzün nasıl tükendiğine bakın.  
    [![EA maliyetlerine genel bakış - döküm özeti](./media/cost-analysis-common-uses/breakdown1.png)](./media/cost-analysis-common-uses/breakdown1.png#lightbox)
1.  **Kullanım ve Ücretler** sekmesine tıklayıp seçili zaman aralığı için bir önceki aynı dökümünü görüntüleyin.  
    [![Kullanım ve ücretler sekmesi](./media/cost-analysis-common-uses/breakdown2.png)](./media/cost-analysis-common-uses/breakdown2.png#lightbox)

## <a name="view-enrollment-monthly-cost-by-term"></a>Döneme göre aylık kayıt maliyetini görüntüleme

Belirli bir döneme ait maliyet eğilimlerini ve fatura edilen tutarları anlamak için kaydınızın aylık maliyetlerini grafik üzerinde görüntüleyin.

1. Azure portalında kapsamınıza ait maliyet analizine gidin. Örneğin: **Maliyet Yönetimi + Faturalama** > **Maliyet Yönetimi** > **Maliyet analizi**.
1. Kaydınızı seçin ve kayıt dönemini belirtin.
1. Ayrıntı düzeyini ay olarak ayarlayın ve **Sütun (yığılmış)** görünümünü seçin.

Daha ayrıntılı bir analiz gerçekleştirmek için verilerinizde gruplandırma ve filtreleme gerçekleştirebilirsiniz.

[![Döneme göre aylık kayıt maliyeti](./media/cost-analysis-common-uses/enrollment-term1.png)](./media/cost-analysis-common-uses/enrollment-term1.png#lightbox)

## <a name="view-ea-enrollment-accumulated-costs"></a>EA kaydı birikmiş maliyetlerini görüntüleme

Kuruluşunuzun belirli bir döneme ait genel harcamalarını anlamak için zaman içindeki net birikmiş ücretleri görüntüleyebilirsiniz.

1. Azure portalında kapsamınıza ait maliyet analizine gidin. Örneğin: **Maliyet Yönetimi + Faturalama** > **Maliyet Yönetimi** > **Maliyet analizi**.
1. Kaydınızı seçin ve geçerli birikmiş maliyetlerinizi görüntüleyin.

[![Kayıt birikmiş maliyetleri](./media/cost-analysis-common-uses/cost-analysis-enrollment.png)](./media/cost-analysis-common-uses/cost-analysis-enrollment.png#lightbox)

## <a name="next-steps"></a>Sonraki adımlar
- Maliyet Yönetimi için ilk hızlı başlangıcı önceden tamamlamadıysanız, [Maliyetleri analiz etmeye başlama](quick-acm-cost-analysis.md) bölümünden bilgi edinin.
- Bkz. [Azure Maliyet Yönetimi belgeleri](../index.yml).
