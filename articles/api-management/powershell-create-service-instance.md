---
title: PowerShell kullanarak Azure API Management örneği oluşturma | Microsoft Docs
description: Yeni bir Azure API Management örneği oluşturmak için bu öğreticideki adımları izleyin.
services: api-management
documentationcenter: ''
author: vladvino
manager: cflower
editor: ''
ms.service: api-management
ms.workload: integration
ms.topic: quickstart
ms.custom: mvc
ms.date: 11/15/2017
ms.author: apimpm
ms.openlocfilehash: 7cd4810059199ddfc88261e27bc910331abe096d
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/26/2020
ms.locfileid: "66730625"
---
# <a name="create-a-new-azure-api-management-service-instance"></a>Yeni bir Azure API Management hizmeti örneği oluşturma

Azure API Management (APIM), kuruluşların kendi veri ve hizmet potansiyellerini ortaya çıkarmak üzere API’leri dış, iş ortağı ve iç geliştiricilere yayımlamalarına yardımcı olur. API Management; geliştirici katılımı, iş öngörüleri, analizler, güvenlik ve koruma aracılığıyla başarılı bir API programı yürütmeye ilişkin temel uzmanlıklar sağlar. APIM, herhangi bir yerde barındırılan mevcut arka uç hizmetleri için modern API ağ geçitleri oluşturmanıza ve yönetmenize olanak sağlar. Daha fazla bilgi için [Genel Bakış](api-management-key-concepts.md) konusuna bakın.

Bu hızlı başlangıç, PowerShell betikleri kullanarak yeni bir API Management örneği oluşturma adımlarını açıklar. Bu hızlı başlangıçta, Azure portalından çalıştırabileceğiniz **Azure Cloud Shell**’i nasıl kullanabileceğiniz gösterilir.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="log-in-to-azure"></a>Azure'da oturum açma

https://portal.azure.com adresinden Azure portalında oturum açın.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

PowerShell'i yerel olarak yüklemeyi ve kullanmayı seçerseniz, bu öğretici için Azure PowerShell modülü sürüm 1.0 veya sonrası gerekir. Sürümü bulmak için `Get-Module -ListAvailable Az` komutunu çalıştırın. Yükseltmeniz gerekirse, bkz. [Azure PowerShell modülünü yükleme](/powershell/azure/install-Az-ps). PowerShell'i yerel olarak çalıştırıyorsanız Azure bağlantısı oluşturmak için `Connect-AzAccount` komutunu da çalıştırmanız gerekir.


## <a name="create-resource-group"></a>Kaynak grubu oluşturma

[Yeni-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup)ile bir Azure kaynak grubu oluşturun. Kaynak grubu, Azure kaynaklarının dağıtıldığı ve yönetildiği bir mantıksal kapsayıcıdır. 

```azurepowershell-interactive
New-AzResourceGroup -Name myResourceGroup -Location WestUS
```

## <a name="create-an-api-management-service"></a>API Management hizmeti oluşturma

Bu, uzun süren bir işlemdir 15 dakikaya kadar sürebilir.

```azurepowershell-interactive
New-AzApiManagement -ResourceGroupName "myResourceGroup" -Location "West US" -Name "apim-name" -Organization "myOrganization" -AdminEmail "myEmail" -Sku "Developer"
```

## <a name="clean-up-resources"></a>Kaynakları temizleme

Artık gerekmediğinde, kaynak grubunu ve ilgili tüm kaynakları kaldırmak için [Kaldır-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) komutunu kullanabilirsiniz.

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroup
```

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [İlk API’nizi içeri aktarma ve yayımlama](import-and-publish.md)
