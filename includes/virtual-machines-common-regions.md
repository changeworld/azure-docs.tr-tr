---
title: include dosyası
description: include dosyası
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 05/28/2019
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: 5e3f25727204343de107bacb9fc99d6cfb77d76f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76020970"
---
Sanal makinelerinizin (VM’ler) Azure’da nasıl ve hangi konumda çalıştığının yanı sıra performans, kullanılabilirlik ve yedekliliği artırmak için kullanabileceğiniz seçeneklerin de anlaşılması önemlidir. Bu makalede, Azure’un kullanılabilirlik ve yedeklilik özelliklerine genel bakış sunulmaktadır.


## <a name="what-are-azure-regions"></a>Azure bölgeleri nelerdir?
Azure, dünyanın dört bir yanındaki birden fazla veri merkezinde çalışmaktadır. Bu veri merkezleri, coğrafi bölgeler halinde gruplandırılarak uygulamalarınızı oluşturacağınız yeri seçme esnekliği tanır. 

Azure kaynaklarını 'Batı ABD', 'Kuzey Avrupa' veya 'Güneydoğu Asya' gibi tanımlanmış coğrafi bölgelerde oluşturursunuz. [Bölgeler ve konumlarının listesini](https://azure.microsoft.com/regions/) gözden geçirebilirsiniz. Her bölge içinde, yedeklilik ve kullanılabilirlik sağlayan birden fazla veri merkezi mevcuttur. Bu yaklaşım, kullanıcılarınıza en yakın VM'ler oluşturmak ve yasal, uyumluluk veya vergi amaçlarını karşılamak için uygulamaları tasarlarken size esneklik sağlar.

## <a name="special-azure-regions"></a>Özel Azure bölgeleri
Azure'un, uygulamalarınızı uyumluluk veya yasal amaçlarla yaparken kullanmak isteyebileceğibazı özel bölgeleri vardır. Bu özel bölgeleri şunlardır:

* **US Gov Virginia** ve **US Gov Iowa**
  * ABD kamu kuruluşları ve iş ortaklarına yönelik olarak ABD’de bulunan ve denetlenen kişilerce çalıştırılan fiziksel ve mantıksal ağdan yalıtılmış Azure örneği. [FedRAMP](https://www.microsoft.com/en-us/TrustCenter/Compliance/FedRAMP) ve [DISA](https://www.microsoft.com/en-us/TrustCenter/Compliance/DISA) gibi ek uyumluluk sertifikaları içerir. [Azure Kamu](https://azure.microsoft.com/features/gov/) hakkında daha fazla bilgi alın.
* **Doğu Çin** ve **Kuzey Çin**
  * Bu bölgeler, Microsoft ile 21Vianet arasında, Microsoft’un veri merkezlerini doğrudan yönetmediği benzersiz ortaklık ile kullanıma sunulmaktadır. [Azure China 21Vianet](https://www.windowsazure.cn/)hakkında daha fazla şey görün.
* **Orta Almanya** ve **Kuzeydoğu Almanya**
  * Bu bölgeler, alman veri mütevellisi olarak hareket eden bir Deutsche Telekom şirketi olan T-Systems'in kontrolü altında kalan bir veri mütevelli modeli aracılığıyla kullanılabilir.

## <a name="region-pairs"></a>Bölge çiftleri
Her Azure bölgesi aynı coğrafyadaki (ABD, Avrupa veya Asya) başka bir bölgeyle eşleştirilir. Bu yaklaşım her iki bölgeyi de aynı anda etkileyen bir doğal felaket, toplumsal karmaşa, güç kesintisi veya fiziksel ağ kesintisi olasılığını azaltması gereken bir coğrafyada VM depolama gibi kaynak çoğaltma işlemlerine olanak tanır. Bölge çiftlerinin diğer avantajları şunlardır:

* Daha geniş bir Azure kesintisi durumunda, uygulamalar için geri yükleme süresini azaltmak üzere her çift içinden bir bölgeye öncelik verilir. 
* Kapalı kalma süresini ve uygulama kesintisi riskini azaltmak amacıyla, planlı Azure güncelleştirmeleri, bölge çiftlerine tek tek uygulanır.
* Veriler, vergi ve yasa uygulama yetkisi bakımından çiftiyle aynı coğrafyada (Güney Brezilya hariç) bulunmaya devam eder.

Bölge çiftlerinin örnekleri şunlardır:

| Birincil | İkincil |
|:--- |:--- |
| Batı ABD |Doğu ABD |
| Kuzey Avrupa |Batı Avrupa |
| Güneydoğu Asya |Doğu Asya |

[Bölgesel çiftlerin tam listesini burada](../articles/best-practices-availability-paired-regions.md#what-are-paired-regions) görebilirsiniz.

## <a name="feature-availability"></a>Özellik kullanılabilirliği
Belirli VM boyutları ya da depolama türleri gibi bazı hizmetler veya VM özellikleri yalnızca belirli bölgelerde kullanılabilir. Ayrıca, belirli bir bölge seçmenizi gerektirmeyen [Azure Active Directory](../articles/active-directory/fundamentals/active-directory-whatis.md), [Traffic Manager](../articles/traffic-manager/traffic-manager-overview.md) veya [Azure DNS](../articles/dns/dns-overview.md) gibi bazı genel Azure hizmetleri de vardır. Uygulama ortamınızı tasarlamanıza yardımcı olmak üzere [her bölgedeki Azure hizmetleri kullanılabilirliğini](https://azure.microsoft.com/regions/#services) denetleyebilirsiniz. Ayrıca, [her bölgedeki desteklenen VM boyutlarını ve kısıtlamalarını programlı olarak](../articles/azure-resource-manager/templates/error-sku-not-available.md)sorgulayabilirsiniz.

## <a name="storage-availability"></a>Depolama kullanılabilirliği
Kullanılabilir çoğaltma seçenekleri düşünüldüğünde Azure bölge ve coğrafyalarının anlaşılması önemlidir. Depolama türüne bağlı olarak farklı çoğaltma seçenekleriniz vardır.

**Azure Yönetilen Diskleri**
* Yerel olarak yedekli depolama (LRS)
  * Depolama hesabınızı oluşturduğunuz bölge içinde verilerinizi üç kez çoğaltır.

**Depolama hesabı temelli diskler**
* Yerel olarak yedekli depolama (LRS)
  * Depolama hesabınızı oluşturduğunuz bölge içinde verilerinizi üç kez çoğaltır.
* Alanlar arası yedekli depolama (ZRS)
  * Tek bir bölge ya da iki bölgedeki iki veya üç tesiste verilerinizi üç kez çoğaltır.
* Coğrafi olarak yedekli depolama (GRS)
  * Verilerinizi birincil bölgeden yüzlerce kilometre uzaktaki bir ikincil bölgeye çoğaltır.
* Okuma erişimli coğrafi olarak yedekli depolama (RA-GRS)
  * GRS ile olduğu gibi verilerinizi ikincil bölgeye çoğaltır, ancak daha sonra ikincil konumdaki verilere salt okunur erişim sağlar.

Aşağıdaki tabloda, depolama çoğaltma türleri arasındaki farkları hızlı bir genel bakış sunulmaktadır:

| Çoğaltma stratejisi | LRS | ZRS | GRS | RA-GRS |
|:--- |:--- |:--- |:--- |:--- |
| Veriler birden çok tesis arasında çoğaltılır. |Hayır |Evet |Evet |Evet |
| Veriler ikincil konumdan ve birincil konumdan okunabilir. |Hayır |Hayır |Hayır |Evet |
| Ayrı düğümlerde tutulan veri kopyası sayısı. |3 |3 |6 |6 |

[Azure Depolama çoğaltma seçenekleri hakkında buradan](../articles/storage/common/storage-redundancy.md) daha fazla bilgi alabilirsiniz. Yönetilen diskler hakkında daha fazla bilgi için bkz. [Azure Yönetilen Disklere genel bakış](../articles/virtual-machines/windows/managed-disks-overview.md).

### <a name="storage-costs"></a>Depolama maliyetleri
Fiyatlar seçtiğiniz depolama türüne ve kullanılabilirliğe bağlı olarak değişir.

**Azure Yönetilen Diskleri**
* Premium Yönetilen Diskler Katı Hal Sürücüleri (SSD'ler) tarafından desteklenir ve Standart Yönetilen Diskler normal dönen diskler tarafından desteklenir. Hem Premium hem de Standart Yönetilen Diskler, diskin sağlanan kapasitesine göre ücretlendirilir.

**Yönetilmeyen diskler**
* Premium depolama Katı Hal Sürücüleri (SSD' ler) tarafından desteklenir ve diskin kapasitesine göre ücretlendirilir.
* Standart depolama, normal dönen disklerle desteklenir ve kullanımdaki kapasiteye ve istenen depolama kullanılabilirliğine göre ücretlendirilir.
  * RA-GRS için, verileri başka bir Azure bölgesine çoğaltmak için gereken bant genişliğine yönelik ek bir Coğrafi Çoğaltma Veri Aktarımı ücreti vardır.

Farklı depolama türleri ve kullanılabilirlik seçenekleri hakkında fiyatlandırma bilgileri için bkz. [Azure Depolama Fiyatlandırması](https://azure.microsoft.com/pricing/details/storage/).

