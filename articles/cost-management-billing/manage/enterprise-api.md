---
title: Azure Faturalama Kurumsal API'leri
description: Kurumsal Azure müşterilerinin program aracılığıyla tüketim verilerini almasını sağlayan Raporlama API’leri hakkında bilgi edinin.
author: mumami
tags: billing
ms.service: cost-management-billing
ms.topic: reference
ms.date: 02/14/2020
ms.author: banders
ms.openlocfilehash: 10275bac8cd9363939f9b6f298c49d7ef08ab7bf
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/24/2020
ms.locfileid: "79202922"
---
# <a name="overview-of-reporting-apis-for-enterprise-customers"></a>Kurumsal müşteriler için Raporlama API’lerine genel bakış
Raporlama API’leri, Kurumsal Azure müşterilerinin program aracılığıyla tüketim ve faturalama verilerini tercih ettikleri veri analizi aracına almasını sağlar. Kurumsal müşteriler, üzerinde anlaşılan parasal taahhütlerde bulunmak ve Azure kaynaklarının özel fiyatlandırmasına erişim elde etmek için Azure ile [Kurumsal Anlaşma (EA)](https://azure.microsoft.com/pricing/enterprise-agreement/) imzalamıştır.

API’ler için gereken tüm tarih ve saat parametrelerinin birleştirilmiş Eşgüdümlü Evrensel Saat (UTC) değerleri olarak belirtilmesi gerekir. API’lerin döndürdüğü değerler UTC biçiminde gösterilir.

## <a name="enabling-data-access-to-the-api"></a>API’ye veri erişimini etkinleştirme
* **API anahtarı oluşturma veya alma**: Kurumsal portalda oturum açın ve Raporlar > Kullanımı İndir > API Erişim Anahtarı seçeneklerine giderek API anahtarı oluşturun veya alın.
* **API’de anahtarları iletme**: Kimlik Doğrulaması ve Yetkilendirmeye ilişkin her bir çağrı için API anahtarının iletilmesi gerekir. Aşağıdaki özelliğin HTTP üst bilgilerine ilişkin olması gerekir

|İstek Üst Bilgisi Anahtarı | Değer|
|-|-|
|Yetkilendirme| Değeri şu biçimde belirtin: **taşıyıcı {API_KEY}** <br/> Örnek: taşıyıcı eyr....09|

## <a name="consumption-apis"></a>Tüketim API’leri
Aşağıda açıklanan API’ler için, [AutoRest](https://github.com/Azure/AutoRest) veya [Swagger CodeGen](https://swagger.io/swagger-codegen/) kullanılarak istemci SDK’ları oluşturma yeteneğini ve API’nin kolay iç denetimini etkinleştirmesi gereken bir Swagger uç noktasına [buradan](https://consumption.azure.com/swagger/ui/index) ulaşılabilir. 1 Mayıs 2014’ten itibaren veriler, bu API aracılığıyla kullanılabilir.

* **Bakiye ve Özet**: [Bakiye ve Özet API’si](/rest/api/billing/enterprise/billing-enterprise-api-balance-summary), bakiyeler, yeni satın almalar, Azure Market hizmeti ücretleri, düzeltmeler ve fazla kullanım ücretleri hakkındaki bilgilerin aylık özetini sunar.

* **Kullanım Ayrıntıları**: [Kullanım Ayrıntıları API’si](/rest/api/billing/enterprise/billing-enterprise-api-usage-detail), bir Kayıt tarafından tüketilen miktarların ve bunların tahmini ücretlerinin günlük dökümünü sunar. Sonuçlarda örnekler, ölçümler ve bölümler hakkında bilgiler de yer alır. API, Faturalama dönemine veya belirtilen bir başlangıç ve bitiş tarihine göre sorgulanabilir.

* **Market Mağaza Ücreti**: [Market Mağaza Ücreti API’si](/rest/api/billing/enterprise/billing-enterprise-api-marketplace-storecharge), kullanıma bağlı market ücretlerinin belirtilen Faturalama Dönemi veya başlangıç ve bitiş tarihleri için günlük dökümünü döndürür (bir kerelik ücretler dahil değildir).

* **Fiyat Listesi**: [Fiyat Listesi API’si](/rest/api/billing/enterprise/billing-enterprise-api-pricesheet), belirtilen Kayıt ve Faturalama Dönemi için her ölçümün geçerli fiyatını sağlar.

* **Ayrılmış Örnek Ayrıntıları**: [Ayrılmış Örnek kullanımı API’si](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-usage), Ayrılmış Örnek satın alımlarının kullanım bilgilerini döndürür. [Ayrılmış Örnek ücretleri API’si](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-usage), yapılan faturalama işlemlerini gösterir.

## <a name="data-freshness"></a>Veri Güncelliği
Yukarıdaki tüm API’lerin yanıtında ETag’ler döndürülür. ETag içindeki bir değişiklik, verilerin yenilendiğini gösterir.  Aynı parametreler kullanılarak aynı API’ye yapılan sonraki çağrılarda, http isteğinin üst bilgisinde “If-None-Match” anahtarıyla birlikte yakalanan ETag’i iletin. Veriler daha fazla yenilenmediyse yanıt durum kodu "NotModified" olur ve başka bir veri döndürülmez. Her ETag değişikliği olduğunda API, gerekli dönem için tam veri kümesini döndürür.

## <a name="helper-apis"></a>Yardımcı API’ler
 **Faturalama Dönemlerini Listele**: [Faturalama Dönemleri API’si](/rest/api/billing/enterprise/billing-enterprise-api-billing-periods), belirtilen Kayıt için ters kronolojik sırayla tüketim verilerini içeren bir faturalama dönemleri listesini döndürür. Her Dönemin dört veri kümesi (BalanceSummary, UsageDetails, Market Ücretleri ve Fiyat Listesi) için API yoluna işaret eden bir özelliği vardır.


## <a name="api-response-codes"></a>API Yanıt Kodları   
|Yanıt Durum Kodu|İleti|Açıklama|
|-|-|-|
|200| Tamam|Hata yok|
|401| Yetkisiz| API Anahtarı bulunamadı, Geçersiz, Süresi Doldu vb.|
|404| Kullanılamaz| Rapor uç noktası bulunamadı|
|400| Hatalı İstek| Geçersiz parametreler – Tarih aralıkları, Kurumsal Anlaşma numaraları vb.|
|500| Sunucu Hatası| İstek işlenirken beklenmeyen hata oluştu|
