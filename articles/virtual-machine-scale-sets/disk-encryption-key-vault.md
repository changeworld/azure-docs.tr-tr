---
title: Azure Disk Şifrelemesi için anahtar kasası oluşturma ve yapılandırma
description: Bu makale, Azure Disk Şifrelemesi ile kullanılmak üzere önemli bir kasa oluşturmak ve yapılandırmak için adımlar sağlar
ms.service: virtual-machine-scale-sets
ms.topic: conceptual
author: msmbaldwin
ms.author: mbaldwin
ms.date: 10/10/2019
ms.custom: seodec18
ms.openlocfilehash: eec5b42da709ab5e79da42f11600f6ffc81d247e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76279009"
---
# <a name="creating-and-configuring-a-key-vault-for-azure-disk-encryption"></a>Azure Disk Şifrelemesi için anahtar kasası oluşturma ve yapılandırma

Azure Disk Şifreleme, disk şifreleme anahtarlarını ve sırlarını kontrol etmek ve yönetmek için Azure Key Vault'u kullanır.  Anahtar kasaları hakkında daha fazla bilgi için Azure Key Vault ve [Secure anahtar kasanızı](../key-vault/key-vault-secure-your-key-vault.md) [ile işe başlayın.](../key-vault/key-vault-get-started.md)

Azure Disk Şifrelemesi ile kullanılmak üzere anahtar kasa oluşturma ve yapılandırma üç adım içerir:

1. Gerekirse bir kaynak grubu oluşturma.
2. Anahtar kasası oluşturuyor. 
3. Anahtar kasası gelişmiş erişim ilkeleri ayarlama.

Bu adımlar aşağıdaki hızlı başlangıçlarda gösterilmiştir:

Ayrıca, isterseniz bir anahtar şifreleme anahtarı (KEK) oluşturabilir veya içe aktarabilirsiniz.

## <a name="install-tools-and-connect-to-azure"></a>Araçları yükleyin ve Azure'a bağlanın

Bu makaledeki adımlar [Azure CLI](/cli/azure/), [Azure PowerShell Az modülü](/powershell/azure/overview)veya Azure [portalı](https://portal.azure.com)ile tamamlanabilir.

### <a name="connect-to-your-azure-account"></a>Azure hesabınıza bağlanma

Azure CLI veya Azure PowerShell'i kullanmadan önce öncelikle Azure aboneliğinize bağlanmanız gerekir. Bunu, [Azure CLI ile oturum açarak](/cli/azure/authenticate-azure-cli?view=azure-cli-latest), [Azure Powershell ile oturum açarak](/powershell/azure/authenticate-azureps?view=azps-2.5.0)veya istendiğinde kimlik bilgilerinizi Azure portalına sağlayarak yaparsınız.

```azurecli-interactive
az login
```

```azurepowershell-interactive
Connect-AzAccount
```

[!INCLUDE [disk-encryption-key-vault](../../includes/disk-encryption-key-vault.md)]
 
## <a name="next-steps"></a>Sonraki adımlar

- [Azure Disk Şifreleme'ye genel bakış](disk-encryption-overview.md)
- [Azure CLI'yi kullanarak sanal makine ölçeği kümelerini şifreleme](disk-encryption-cli.md)
- [Azure PowerShell'i kullanarak sanal makine ölçeği kümelerini şifreleme](disk-encryption-powershell.md)
