---
title: Bir Internet etki alanını Trafik Yöneticisine noktala - Azure Trafik Yöneticisi
description: Bu makale, şirketinizin etki alanı adını Traffic Manager etki alanı adına yönlendirmenize yardımcı olacaktır.
services: traffic-manager
author: rohinkoul
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/11/2016
ms.author: rohink
ms.openlocfilehash: 6c5c5c185063caf8ca258ad70a70903c9b583e07
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80294852"
---
# <a name="point-a-company-internet-domain-to-an-azure-traffic-manager-domain"></a>Bir şirketin İnternet etki alanını Azure Traffic Manager etki alanına yönlendirme

Traffic Manager profili oluşturduğunuzda, Azure bu profil için otomatik olarak bir DNS adı atar. DNS bölgenizdeki bir adı kullanmak için Traffic Manager profilinizin etki alanı adıyla eşleşen bir CNAME DNS kaydı oluşturun. Traffic Manager etki alanı adını, Traffic Manager profilinin Yapılandırma sayfasındaki **Genel** bölümünde bulabilirsiniz.

Örneğin, `www.contoso.com` adıyla Traffic Manager `contoso.trafficmanager.net` DNS adına işaret etmek için, aşağıdaki DNS kaynak kaydını oluşturursunuz:

    `www.contoso.com IN CNAME contoso.trafficmanager.net`

*\.www contoso.com* tüm trafik istekleri *contoso.trafficmanager.net*yönlendirilir olsun.

> [!IMPORTANT]
> *contoso.com* gibi ikinci düzey bir etki alanını Traffic Manager etki alanına yönlendiremezsiniz. DNS protokolü standartları, ikinci düzey etki alanı adları için CNAME kayıtlarına izin vermez.

## <a name="next-steps"></a>Sonraki adımlar

* [Traffic Manager yönlendirme yöntemleri](traffic-manager-routing-methods.md)
* [Traffic Manager - Bir profili devre dışı bırakma, etkinleştirme veya silme](disable-enable-or-delete-a-profile.md)
* [Traffic Manager - Bir uç noktayı devre dışı bırakma veya etkinleştirme](disable-or-enable-an-endpoint.md)
