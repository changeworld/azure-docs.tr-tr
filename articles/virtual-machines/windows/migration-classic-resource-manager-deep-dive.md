---
title: Klasik - Azure Kaynak Yöneticisi geçiş teknik derin dalış
description: Klasik dağıtım modelinden Azure Kaynak Yöneticisi'ne platform destekli kaynak geçişine teknik derin dalış.
services: virtual-machines-windows
documentationcenter: ''
author: tanmaygore
manager: vashan
editor: ''
tags: azure-resource-manager
ms.assetid: 1ee40d32-a5e8-42a2-97d0-3232fd3cbb98
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: article
ms.date: 02/06/2020
ms.author: tagore
ms.openlocfilehash: f3b4a601fe65c4227a5d876fe4db16f253ca160b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77915478"
---
# <a name="technical-deep-dive-on-platform-supported-migration-from-classic-to-azure-resource-manager"></a>Klasik modelden Azure Resource Manager’a platform destekli geçişe ayrıntılı teknik bakış

> [!IMPORTANT]
> Bugün, IaaS VM'lerinin yaklaşık %90'ı [Azure Kaynak Yöneticisi](https://azure.microsoft.com/features/resource-manager/)kullanıyor. 28 Şubat 2020 itibariyle, klasik VM'ler amortismana kaldırılmıştır ve 1 Mart 2023'te tamamen emekliye ayırılacaktır. Bu amortisman ve sizi [nasıl etkilediği](https://docs.microsoft.com/azure/virtual-machines/classic-vm-deprecation#how-does-this-affect-me)hakkında [daha fazla bilgi edinin.]( https://aka.ms/classicvmretirement)

Azure klasik dağıtım modelinden Azure Kaynak Yöneticisi dağıtım modeline geçiş konusunda derin bir dalış yapalım. Azure platformunun kaynakları iki dağıtım modeli arasında nasıl geçirtiyi anlamanıza yardımcı olmak için kaynakları kaynak ve özellik düzeyinde inceleriz. Daha fazla bilgi için lütfen hizmet duyurusu makalesini okuyun: [IaaS kaynaklarının klasikten Azure Kaynak Yöneticisi'ne platform destekli geçişi.](migration-classic-resource-manager-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

[!INCLUDE [virtual-machines-common-migration-deep-dive](../../../includes/virtual-machines-common-classic-resource-manager-migration-deep-dive.md)]

## <a name="next-steps"></a>Sonraki adımlar

* [IaaS kaynaklarının klasikten Azure Kaynak Yöneticisi'ne platform destekli geçişine genel bakış](migration-classic-resource-manager-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [IaaS kaynaklarının Klasik’ten Azure Resource Manager’a geçişini planlama](migration-classic-resource-manager-plan.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [IaaS kaynaklarını klasikten Azure Kaynak Yöneticisi'ne geçirmek için PowerShell'i kullanın](migration-classic-resource-manager-ps.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [IaaS kaynaklarını klasikten Azure Kaynak Yöneticisi'ne geçirmek için CLI'yi kullanın](../linux/migration-classic-resource-manager-cli.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [KAYNAK Yöneticisi geçişine VPN Ağ Geçidi klasiği](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-classic-resource-manager-migration)
* [ExpressRoute devrelerini ve ilişkili sanal ağları klasikten Kaynak Yöneticisi dağıtım modeline geçirin](https://docs.microsoft.com/azure/expressroute/expressroute-migration-classic-resource-manager)
* [IaaS kaynaklarının klasikten Azure Kaynak Yöneticisi'ne geçişine yardımcı olmak için topluluk araçları](migration-classic-resource-manager-community-tools.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [En sık karşılaşılan geçiş hatalarını gözden geçirme](migration-classic-resource-manager-errors.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [IaaS kaynaklarını klasikten Azure Kaynak Yöneticisi'ne geçirme yle ilgili en sık sorulan soruları gözden geçirin](migration-classic-resource-manager-faq.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
