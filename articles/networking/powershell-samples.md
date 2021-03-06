---
title: Azure PowerShell Örnekleri - Ağ
description: Azure PowerShell Örnekleri
services: virtual-network
documentationcenter: virtual-network
author: KumudD
manager: mtillman
editor: tysonn
tags: ''
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: infrastructure
ms.date: 05/24/2017
ms.author: gwallace
ms.openlocfilehash: d1e6027a0dc71f0962293143ca9bd3de67a659e6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76844913"
---
# <a name="azure-powershell-samples-for-networking"></a>Ağ için Azure PowerShell Örnekleri

Aşağıdaki tablo, Azure PowerShell kullanılarak oluşturulmuş komut dosyalarına bağlantılar içerir.

| | |
|-|-|
|**Azure kaynakları arasında bağlantı**||
| [Çok katmanlı uygulamalar için sanal ağ oluşturma](./scripts/virtual-network-powershell-sample-multi-tier-application.md?toc=%2fazure%2fnetworking%2ftoc.json) | Ön uç ve arka uç alt ağları ile sanal ağ oluşturur. 1433 numaralı bağlantı noktası için, ön uç alt ağına giden trafik HTTP ile sınırlıyken, arka uç alt ağına giden trafik SQL ile sınırlıdır. |
| [İki sanal ağı eşleme](./scripts/virtual-network-powershell-sample-peer-two-virtual-networks.md?toc=%2fazure%2fnetworking%2ftoc.json) | Aynı bölgede iki sanal ağ oluşturur ve bunları bağlar. |
| [Bir ağ sanal gereci yoluyla trafiği yönlendirme](./scripts/virtual-network-powershell-sample-route-traffic-through-nva.md?toc=%2fazure%2fnetworking%2ftoc.json) | İki alt ağ arasında trafiği yönlendirebilen bir sanal makine ve ön uç ve arka uç alt ağları içeren bir sanal ağ oluşturur. |
| [Gelen ve giden sanal makine ağ trafiğini filtreleme](./scripts/virtual-network-powershell-filter-network-traffic.md?toc=%2fazure%2fnetworking%2ftoc.json) | Ön uç ve arka uç alt ağları ile sanal ağ oluşturur. Ön uç alt ağına gelen ağ trafiği HTTP ve HTTPS ile sınırlıdır... Arka uç alt ağdan Internet'e giden trafiğe izin verilmez. |
|**Yük dengeleme ve trafik yönü**||
| [Yüksek kullanılabilirlik için VM'lere yük dengesi trafiği](./scripts/load-balancer-windows-powershell-sample-nlb.md?toc=%2fazure%2fnetworking%2ftoc.json) | Yüksek kullanılabilir ve yük dengeli yapılandırmabirkaç sanal makineler oluşturur. |
| [Yüksek uygulama kullanılabilirliği için birden fazla bölgede doğrudan trafik](./scripts/traffic-manager-powershell-websites-high-availability.md?toc=%2fazure%2fnetworking%2ftoc.json) |  İki uygulama hizmeti planı, iki web uygulaması, bir trafik yöneticisi profili ve iki trafik yöneticisi bitiş noktası oluşturur. |
| | |
