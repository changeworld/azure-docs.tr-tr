---
title: Azure Ön Kapı - HTTP2 desteği | Microsoft Dokümanlar
description: Bu makale, Azure Ön Kapı'da HTTP/2 desteği hakkında bilgi edinmenize yardımcı olur
services: frontdoor
documentationcenter: ''
author: sharad4u
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/10/2018
ms.author: sharadag
ms.openlocfilehash: 8a3ae8065553b34a72528cb0f2681e327dc90097
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/09/2020
ms.locfileid: "80985193"
---
# <a name="http2-support-in-azure-front-door"></a>Azure Ön Kapı'da HTTP/2 desteği

Şu anda, HTTP/2 desteği tüm Azure Ön Kapı yapılandırmaları için etkindir. Müşterilerden başka bir işlem gerekmez.

HTTP/2 HTTP/1.1 için büyük bir revizyondur. Tanıdık HTTP yöntemlerini, durum kodlarını ve anlambilimini korurken daha hızlı web performansı, daha az yanıt süresi ve gelişmiş kullanıcı deneyimi sağlar. HTTP/2 HTTP ve HTTPS ile çalışmak üzere tasarlanmış olsa da, birçok istemci web tarayıcısı yalnızca Taşıma Katmanı Güvenliği (TLS) üzerinden HTTP/2'yi destekler.

> [!NOTE]
> HTTP/2 protokol desteği yalnızca müşterilerden Front Door'a gelen istekler için kullanılabilir. Ön Kapıdan arkaya iletişim arka uç havuzunda biter HTTP/1.1 üzerinden olur. 

### <a name="http2-benefits"></a>HTTP/2 avantajları

HTTP/2'nin yararları şunlardır:

*   **Çoklama ve eşzamanlılık**

    HTTP 1.1'i kullanarak, birden çok kaynak isteğinde bulunmak için birden çok TCP bağlantısı gerektirir ve her bağlantıyla ilişkili performans ek yükü vardır. HTTP/2, tek bir TCP bağlantısında birden çok kaynağın istenmesine izin verir.

*   **Üstbilgi sıkıştırma**

    Hizmet verilen kaynaklar için HTTP üstbilgilerini sıkıştırarak, kabloüzerindeki süre önemli ölçüde azalır.

*   **Akış bağımlılıkları**

    Akış bağımlılıkları istemcinin sunucuya hangi kaynakların önceliği olduğunu göstermesine olanak sağlar.


## <a name="http2-browser-support"></a>HTTP/2 tarayıcı desteği

Tüm büyük tarayıcılar mevcut sürümlerinde HTTP/2 desteği uyguladık. Desteklenmeyen tarayıcılar otomatik olarak HTTP/1.1'e geri döner.

|Tarayıcı|En Düşük Sürüm|
|-------------|------------|
|Microsoft Edge| 12|
|Google Chrome| 43|
|Mozilla Firefox| 38|
|Opera| 32|
|Safari| 9|

## <a name="next-steps"></a>Sonraki Adımlar

HTTP/2 hakkında daha fazla bilgi edinmek için aşağıdaki kaynakları ziyaret edin:

- [HTTP/2 belirtim ana sayfası](https://http2.github.io/)
- [Resmi HTTP/2 SSS](https://http2.github.io/faq/)
- [Front Door oluşturmayı](quickstart-create-front-door.md) öğrenin.
- [Front Door’un nasıl çalıştığını](front-door-routing-architecture.md) öğrenin.
