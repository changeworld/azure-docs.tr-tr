---
title: Azure Röle bağlantı noktası ayarları | Microsoft Dokümanlar
description: Bu makalede, Azure Röle için bağlantı noktası değerleri için gerekli yapılandırmayı açıklayan bir tablo bulunur.
services: service-bus-relay
documentationcenter: na
author: spelluru
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-bus-relay
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/21/2020
ms.author: spelluru
ms.openlocfilehash: 532e3c297c42ea16b1f462a01699fc2fd71c6cce
ms.sourcegitcommit: c5661c5cab5f6f13b19ce5203ac2159883b30c0e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/01/2020
ms.locfileid: "80529119"
---
# <a name="azure-relay-port-settings"></a>Azure Röle bağlantı noktası ayarları

Aşağıdaki tabloda Azure Rölesi için bağlantı noktası değerleri için gerekli yapılandırma açıklanmaktadır.

## <a name="hybrid-connections"></a>Karma Bağlantılar

Karma Bağlantılar, yalnızca **HTTPS** kullanan temel aktarım mekanizması olarak TLS ile 443 bağlantı noktasındaki WebSockets'i kullanır. 

## <a name="wcf-relays"></a>WCF Geçişleri
  
|Bağlama|Taşıma Güvenliği|Bağlantı noktası|  
|-------------|------------------------|----------|  
|[TemelHttpRelayBinding Sınıfı](/dotnet/api/microsoft.servicebus.basichttprelaybinding) (istemci)|Evet|HTTPS| 
|" |Hayır|HTTP|  
|[TemelHttpRelayBinding Sınıfı](/dotnet/api/microsoft.servicebus.basichttprelaybinding) (hizmet)|Ya|9351/HTTP|  
|[NetEventRelayBinding Sınıfı](/dotnet/api/microsoft.servicebus.neteventrelaybinding) (istemci)|Evet|9351/HTTPS|  
|" |Hayır|9350/HTTP|  
|[NetEventRelayBinding Sınıfı](/dotnet/api/microsoft.servicebus.neteventrelaybinding) (servis)|Ya|9351/HTTP|  
|[NetTcpRelayBinding Sınıfı](/dotnet/api/microsoft.servicebus.nettcprelaybinding) (istemci/hizmet)|Ya|5671/9352/HTTP (9352/9353 hibrid kullanıyorsanız)|  
|[NetOnewayRelayBinding Sınıfı](/dotnet/api/microsoft.servicebus.netonewayrelaybinding) (istemci)|Evet|9351/HTTPS|  
|" |Hayır|9350/HTTP|  
|[NetOnewayRelayBinding Sınıfı](/dotnet/api/microsoft.servicebus.netonewayrelaybinding) (servis)|Ya|9351/HTTP|  
|[WebHttpRelayBinding Sınıfı](/dotnet/api/microsoft.servicebus.webhttprelaybinding) (istemci)|Evet|HTTPS|  
|" |Hayır|HTTP|  
|[WebHttpRelayBinding Sınıfı](/dotnet/api/microsoft.servicebus.webhttprelaybinding) (hizmet)|Ya|9351/HTTP|  
|[WS2007HttpRelayBinding Sınıfı](/dotnet/api/microsoft.servicebus.ws2007httprelaybinding) (istemci)|Evet|HTTPS|  
|" |Hayır|HTTP|  
|[WS2007HttpRelayBinding Sınıfı](/dotnet/api/microsoft.servicebus.ws2007httprelaybinding) (hizmet)|Ya|9351/HTTP|

## <a name="next-steps"></a>Sonraki adımlar
Azure Röle hakkında daha fazla bilgi edinmek için aşağıdaki bağlantıları ziyaret edin:
* [Azure Geçiş nedir?](relay-what-is-it.md)
* [Geçiş hakkında SSS](relay-faq.md)