---
title: Microsoft Müşteri Sözleşmesi fiyat listenizdeki koşullar - Azure
description: Microsoft Müşteri Sözleşmesinin kullanımını ve faturasını okuma ve anlama hakkında bilgi edinin.
author: bandersmsft
tags: billing
ms.service: cost-management-billing
ms.topic: conceptual
ms.date: 02/13/2020
ms.author: banders
ms.openlocfilehash: 1e776e351fe723f0fbc5b1d6a8f5593a9fc975b7
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/24/2020
ms.locfileid: "77199628"
---
# <a name="terms-in-your-microsoft-customer-agreement-price-sheet"></a>Microsoft Müşteri Sözleşmesi fiyat listenizdeki koşullar

Bu makale, Microsoft Müşteri Sözleşmesi’ne ilişkin Azure ödeme hesabı için geçerlidir. [Microsoft Müşteri Sözleşmesi’ne erişiminiz olup olmadığını denetleyin](#check-access-to-a-microsoft-customer-agreement).

Faturalama profili Sahibi, Katkıda Bulunanı, Okuyucusu veya Fatura Yöneticisi iseniz kuruluşunuzun fiyat listesini Azure portalından indirebilirsiniz. Bkz. [Kuruluşunuzun fiyatlandırmasını görüntüleme ve indirme](ea-pricing.md).

## <a name="terms-and-descriptions-in-your-price-sheet"></a>Fiyat listenizdeki terimler ve açıklamalar

Aşağıdaki bölümde, Microsoft Müşteri Sözleşmesi fiyat listenizde gösterilen önemli terimler açıklanmaktadır.

| **Alan Adı**   | **Açıklama**   |
| --- | --- |
| basePrice  | Müşterinin oturum açtığı andaki pazar fiyatı veya oturum açıldıktan sonra hizmet ölçümünün başlatıldığı andaki pazar fiyatı.   |
| billingAccountId  | Ödeme hesabının benzersiz tanımlayıcısı.   |
| billingAccountName  | Ödeme hesabının adı.  |
| billingCurrency | Ücretlerin gönderildiği para birimi |
| billingProfileId  | Faturalama profilinin benzersiz tanımlayıcısı.   |
| billingProfileName  | Faturaları alması için ayarlanan faturalama profilinin adı. Fiyat listesindeki fiyatlar, bu faturalama profiliyle ilişkilidir. |
| currency | Tüm fiyatların yansıtıldığı para birimi. |
| discount | Uygun olduğunda Mezuniyet Katmanı, Ücretsiz Katman, Dahil Edilen Miktar veya Üzerinde anlaşılan indirimler için sunulan fiyat indirimi. Yüzde olarak gösterilir. |
| effectiveEndDate  | Geçerli fiyatın bitiş tarihi. |
| effectiveStartDate  | Fiyatın geçerli olduğu başlangıç tarihi. |
| includedQuantity | Belirli bir hizmette müşterinin artımlı ücretler uygulanmadan tüketme hakkı olan miktarlar. |
| marketPrice | Belirli bir hizmetin güncel ve geçerli piyasa fiyatı. |
| meterId  | Ölçümün benzersiz tanımlayıcısı. |
| meterCategory  | Ölçüm için sınıflandırma kategorisinin adı. Örneğin, _Bulut hizmetleri_, _Ağ iletişimi_ vb. |
| meterName  | Ölçümün adı. Ölçüm, bir Azure hizmetinin dağıtılabilir kaynağını temsil eder. |
| meterSubCategory  | Ölçüm alt sınıflandırma kategorisinin adı.  |
| meterType  |  Ölçüm türünün adı. |
| meterRegion  | Hizmet ölçümünün kullanılabilir olduğu bölgenin adı. Veri merkezi konumuna bağlı olarak ücretlendirilen belirli hizmetler için veri merkezinin konumunu belirtir.    |
| Ürün  | Ücret tahakkuk eden ürünün adı. Ör: Temel SQL DB ve Standart SQL DB  |
| productId  | Ölçümü kullanılan ürünün benzersiz tanımlayıcısı. |
| productOrderName  | Satın alınan ürün planının adı. |
| serviceFamily  | Azure hizmetinin türü. Ör: İşlem, Analiz, Güvenlik |
| tierMinimumUnits  | Fiyatları tanımlanan katman aralığının alt sınırını tanımlar. Örneğin, aralık 0 ila 100 arasıysa tierMinimumUnits 0 olur.  |
| unitOfMeasure  | Hizmet faturalaması için ölçü birimlerini tanımlar. Örneğin, işlem hizmetleri saat bazında faturalandırılır. |
| unitPrice  | Bir ölçüme ve ürün siparişi adına özgü olarak faturalama sırasındaki birim başına fiyat (geçerli karma fiyat değildir).  Not: Birim fiyat, katmanlar arasında farklı fiyatları bulunan hizmetler olması durumunda kullanım ayrıntıları indirmelerindeki geçerli fiyatla aynı değildir.  Çok katmanlı fiyatlandırması bulunan hizmetler olması durumunda geçerli fiyat, katmanlar genelindeki karma bir fiyattır ve katmana özgü birim fiyatı göstermez. Karma fiyat veya geçerli fiyat, birden çok katman genelinde yayılan (her bir katmanın belirli bir birim fiyatının olduğu), kullanılan miktarın net fiyatıdır. |


## <a name="check-access-to-a-microsoft-customer-agreement"></a>Microsoft Müşteri Sözleşmesi’ne erişimi denetleme
[!INCLUDE [billing-check-mca](../../../includes/billing-check-mca.md)]

## <a name="need-help-contact-us"></a>Yardıma mı ihtiyacınız var? Bize ulaşın.

Sorularınız varsa ya da yardıma gereksinim duyuyorsanız [destek isteği oluşturun](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Sonraki adımlar

- [Kuruluşunuzun fiyatlandırmasını görüntüleme ve indirme](ea-pricing.md)
