---
title: Azure uygulama güvenlik gruplarına genel bakış
titlesuffix: Azure Virtual Network
description: Uygulama güvenlik gruplarının kullanımı hakkında bilgi edinin.
services: virtual-network
documentationcenter: na
author: KumudD
ms.service: virtual-network
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/27/2020
ms.author: kumud
ms.reviewer: kumud
ms.openlocfilehash: 775ef92a0ca486d1f8a6c44c78a4df04cd5ef467
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78274716"
---
# <a name="application-security-groups"></a>Uygulama güvenliği grupları

Uygulama güvenlik grupları ağ güvenliğini uygulamanın yapısının doğal bir uzantısı olarak yapılandırmanıza imkan vererek sanal makineleri gruplamanızı ve ağ güvenlik ilkelerini bu gruplara göre tanımlamanızı sağlar. Açık IP adreslerinin bakımını el ile yapmanıza gerek kalmadan güvenlik ilkesini farklı ölçeklerde yeniden kullanabilirsiniz. Platform açık IP adreslerinin ve birden fazla kural kümesinin karmaşık süreçlerini üstlenerek iş mantığınıza odaklanmanızı sağlar. Uygulama güvenlik gruplarını daha iyi anlamak için aşağıdaki örneği inceleyin:

![Uygulama güvenliği grupları](./media/security-groups/application-security-groups.png)

Yukarıdaki resimde *NIC1* ve *NIC2*, *AsgWeb* uygulama güvenlik grubunun üyeleridir. *NIC3*, *AsgLogic* uygulama güvenlik grubunun üyesidir. *NIC4*, *AsgDb* uygulama güvenlik grubunun üyesidir. Bu örnekteki tüm ağ arabirimleri tek bir uygulama güvenlik grubuna üye olsa da bir ağ arabirimi [Azure sınırları](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits) dahilinde birden fazla uygulama güvenlik grubuna üye olabilir. Ağ arabirimlerinin hiçbiri bir ağ güvenlik grubuyla ilişkilendirilmemiştir. *NSG1* her iki alt ağla ilişkilidir ve aşağıdaki kuralları içerir:

## <a name="allow-http-inbound-internet"></a>Allow-HTTP-Inbound-Internet

Bu kural, internetten Web sunucularına gelen trafiğe izin vermek için kullanılır. İnternetten gelen trafik, **DenyAllInbound** varsayılan güvenlik grubu tarafından reddedildiğinden *AsgLogic* veya *AsgDb* uygulama güvenlik grupları için ek kurala ihtiyaç duyulmaz.

|Öncelik|Kaynak|Kaynak bağlantı noktaları| Hedef | Hedef bağlantı noktaları | Protokol | Erişim |
|---|---|---|---|---|---|---|
| 100 | Internet | * | AsgWeb | 80 | TCP | İzin Ver |

## <a name="deny-database-all"></a>Deny-Database-All

**AllowVNetInBound** varsayılan güvenlik kuralı aynı sanal ağ içinde bulunan kaynaklar arasındaki tüm iletişime izin verdiğinden, tüm kaynaklardan gelen trafiği reddetmek için bu kurala ihtiyaç duyulur.

|Öncelik|Kaynak|Kaynak bağlantı noktaları| Hedef | Hedef bağlantı noktaları | Protokol | Erişim |
|---|---|---|---|---|---|---|
| 120 | * | * | AsgDb | 1433 | Herhangi biri | Reddet |

## <a name="allow-database-businesslogic"></a>Allow-Database-BusinessLogic

Bu kural *AsgLogic* uygulama güvenlik grubundan *AsgDb* uygulama güvenlik grubuna gelen trafiğe izin verir. Bu kuralın önceliği, *Deny-Database-All* kuralının önceliğinden daha yüksektir. Sonuç olarak bu kural, *Deny-Database-All* kuralından önce işlenir ve böylece *AsgLogic* uygulama güvenlik grubundan gelen trafiğe izin veriler ve diğer tüm trafik engellenir.

|Öncelik|Kaynak|Kaynak bağlantı noktaları| Hedef | Hedef bağlantı noktaları | Protokol | Erişim |
|---|---|---|---|---|---|---|
| 110 | AsgLogic | * | AsgDb | 1433 | TCP | İzin Ver |

Bir uygulama güvenlik grubunu kaynak veya hedef olarak belirten kurallar yalnızca uygulama güvenlik grubuna üye olan ağ arabirimlerine uygulanır. Ağ arabirimi bir uygulama güvenlik grubuna üye değilse, ağ güvenlik grubu alt ağ ile ilişkilendirilmiş olsa dahi kural ağ arabirimine uygulanmaz.

Uygulama güvenlik grupları aşağıdaki sınırlamalara sahiptir:

-    Bir abonelik içinde bulunabilecek uygulama güvenlik grubu sayısı sınırlıdır ve uygulama güvenlik gruplarıyla ilgili başka sınırlar da vardır. Ayrıntılar için [Azure limitleri](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits) makalesini inceleyin.
- Bir uygulama güvenlik grubunu bir güvenlik kuralında kaynak ve hedef olarak belirtebilirsiniz. Kaynak veya hedefte birden çok uygulama güvenlik grubu belirtemezsiniz.
- Bir uygulama güvenlik grubuna atanan tüm ağ arabirimleri, uygulama güvenlik grubuna atanmış ilk ağ arabirimiyle aynı sanal ağda olmalıdır. Örneğin, ilk ağ arabirimi *VNet1* adlı sanal ağdaki *AsgWeb* adlı bir uygulama güvenlik grubuna atanmışsa *ASGWeb*’e atanan sonraki tüm ağ arabirimleri *VNet1*’de olmalıdır. Bir uygulama güvenlik grubuna farklı ağlarda bulunan ağ arabirimlerini ekleyemezsiniz.
- Uygulama güvenlik grubunu bir güvenlik kuralında kaynak ve hedef olarak belirtirseniz iki uygulama güvenlik grubundaki ağ arabirimlerinin de aynı sanal ağda bulunması gerekir. Örneğin *AsgLogic* üzerinde *VNet1* içinde bulunan ağ arabirimleri, *AsgDb* üzerinde de *VNet2* içinde bulunan ağ arabirimleri varsa, bir kural içinde *AsgLogic* grubunu kaynak olarak ve *AsgDb* grubunu da hedef olarak belirleyemezsiniz. Hem kaynak hem de hedef uygulama güvenlik gruplarının tüm ağ arabirimlerinin aynı sanal ağ içinde bulunması gerekir.

> [!TIP]
> İhtiyacınız olan güvenlik kuralı sayısını ve kural değiştirme gereksinimini en aza indirmek için, gereken uygulama güvenlik gruplarını planlarken ve kuralları oluştururken tek IP adreslerini veya IP adresi aralıklarını kullanmak yerine hizmet etiketlerini ya da uygulama güvenlik gruplarını kullanın.

## <a name="next-steps"></a>Sonraki adımlar

* [Ağ güvenlik grubu oluşturmayı](tutorial-filter-network-traffic.md) öğrenin.
