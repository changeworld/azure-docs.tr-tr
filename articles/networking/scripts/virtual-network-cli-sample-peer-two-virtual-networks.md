---
title: Azure CLI Script Örneği - Eş iki sanal ağ | Microsoft Dokümanlar
description: Azure CLI Script Örneği - Eş iki sanal ağ
services: virtual-network
documentationcenter: virtual-network
author: KumudD
manager: mtillman
ms.service: virtual-network
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: infrastructure
ms.date: 07/07/2017
ms.author: kumud
ms.openlocfilehash: 0e5aaf07b49546aca9f44dcb9a43a79127c82341
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/27/2020
ms.locfileid: "73890238"
---
# <a name="peer-two-virtual-networks"></a>İki sanal ağı eşleme

Bu komut dosyası, Azure ağı üzerinden aynı bölgedeki iki sanal ağ oluşturur ve bağlanır. Betiği çalıştırdıktan sonra iki sanal ağ arasında eşleme oluşturmuş olursunuz.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]


## <a name="sample-script"></a>Örnek betik

[!code-azurecli-interactive[main](../../../cli_scripts/virtual-network/peer-two-virtual-networks/peer-two-virtual-networks.sh "Peer two networks")]

## <a name="clean-up-deployment"></a>Dağıtımı temizleme 

Kaynak grubunu, VM’yi ve ilgili tüm kaynakları kaldırmak için aşağıdaki komutu çalıştırın.

```azurecli
az group delete --name myResourceGroup --yes
```

## <a name="script-explanation"></a>Betik açıklaması

Bu betik, bir kaynak grubu, sanal makine ve tüm ilgili kaynakları oluşturmak için aşağıdaki komutları kullanır. Tablodaki her komut, komuta özgü belgelere yönlendirir.

| Komut | Notlar |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group) | Tüm kaynakların depolandığı bir kaynak grubu oluşturur. |
| [az network vnet create](https://docs.microsoft.com/cli/azure/network/vnet) | Bir Azure sanal ağı ve alt ağ oluşturur. |
| [az network vnet peering create](https://docs.microsoft.com/cli/azure/network/vnet/peering) | İki sanal ağ arasında eşleme oluşturur.  |
| [az group delete](https://docs.microsoft.com/cli/azure/vm/extension) | Bir kaynak grubunu tüm iç içe geçmiş kaynaklar dahil siler. |

## <a name="next-steps"></a>Sonraki adımlar

Azure CLI hakkında daha fazla bilgi için bkz. [Azure CLI belgeleri](https://docs.microsoft.com/cli/azure).

Ek ağ CLI betiği örnekleri, [Azure Ağına Genel Bakış belgeleri](../cli-samples.md) içinde bulunabilir.
