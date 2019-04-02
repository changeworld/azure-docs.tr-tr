---
title: Azure Application Gateway yenilikler
description: Azure Application Gateway ile yeni gibi bilinen sorunları, hata düzeltmeleri, en son sürüm notları, işlevler de kullanım dışı ve gelecek öğrenin değişiklikler.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: overview
ms.date: 4/1/2019
ms.author: victorh
ms.openlocfilehash: 053eac95a81492e79aee824b02383206c6702a84
ms.sourcegitcommit: 3341598aebf02bf45a2393c06b136f8627c2a7b8
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/01/2019
ms.locfileid: "58806691"
---
# <a name="whats-new-in-azure-application-gateway"></a>Azure Application Gateway yenilikler nelerdir?

Azure Application Gateway, sürekli olarak güncelleştirilir. İle en son gelişmeleri güncel kalmak için bu makalede, ile hakkında bilgi sağlar:

- En son sürümleri
- Bilinen sorunlar
- Hata düzeltmeleri
- Kullanım dışı işlev

## <a name="new-features"></a>Yeni Özellikler

|Özellik  |Açıklama  |Eklenme tarihi  |
|---------|---------|---------|
|Üst bilgi CRUD/yeniden yazma işlemleri     |HTTP üst bilgileri artık yazabilirsiniz. Bkz: [Öğreticisi: Application gateway oluşturma ve HTTP üst bilgilerini yeniden](tutorial-http-header-rewrite-powershell.md) daha fazla bilgi için.|Aralık 2018|
|WAF yapılandırmasını ve dışlama listesi     |WAF'nizi yapılandırmak ve hatalı pozitif sonuçları azaltmak için daha fazla seçenek ekledik. Bkz: [Web uygulaması güvenlik duvarı istek boyutu sınırları ve hariç tutma listeleri](application-gateway-waf-configuration.md) daha fazla bilgi için.|Aralık 2018|
|Otomatik ölçeklendirme, bölge artıklığı, statik VIP önizlemesi     |V2 SKU ile otomatik ölçeklendirme, Gelişmiş performans ve diğer birçok gelişme vardır. Bkz: [Azure Application Gateway nedir?](overview.md#autoscaling-public-preview) daha fazla bilgi için.|Eylül 2018|
|Bağlantı boşaltma     |Bağlantı boşaltma düzgün bir şekilde, arka uç havuzlarını üyeleri kaldırmanızı sağlar. Daha fazla bilgi için [bağlantı boşaltma](overview.md#connection-draining).|Eylül 2018|
|Özel hata sayfaları     |Özel hata sayfalarıyla sitelerinizi geri kalanını biçimi içinde bir hata sayfası oluşturabilirsiniz. Bunu etkinleştirmek için bkz: [uygulama ağ geçidi Oluştur özel hata sayfaları](custom-error.md).|Eylül 2018|
|Ölçümleri geliştirmeleri     |Gelişmiş ölçümler ile uygulama ağ geçidinizin durumunu daha iyi bir görünümünü elde edebilirsiniz. Ölçümleri, uygulama ağ geçidi üzerinde etkinleştirmek için bkz: [arka uç sistem durumu, tanılama günlükleri ve ölçümler için Application Gateway](application-gateway-diagnostics.md).|Haziran 2018|

## <a name="next-steps"></a>Sonraki adımlar

Azure Application Gateway hakkında daha fazla bilgi için bkz: [Azure Application Gateway nedir?](overview.md)