---
title: Azure Disk Şifrelemesi için anahtar kasası oluşturma ve yapılandırma
description: Bu makale, Azure Disk Şifrelemesi ile kullanılmak üzere önemli bir kasa oluşturmak ve yapılandırmak için adımlar sağlar
ms.service: virtual-machines-linux
ms.topic: article
author: msmbaldwin
ms.author: mbaldwin
ms.date: 08/06/2019
ms.custom: seodec18
ms.openlocfilehash: 5d9acb525f35da756a986826574082f1ecafedf5
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/14/2020
ms.locfileid: "81314108"
---
# <a name="creating-and-configuring-a-key-vault-for-azure-disk-encryption"></a>Azure Disk Şifrelemesi için anahtar kasası oluşturma ve yapılandırma

Azure Disk Şifreleme, disk şifreleme anahtarlarını ve sırlarını kontrol etmek ve yönetmek için Azure Key Vault'u kullanır.  Anahtar kasaları hakkında daha fazla bilgi için Azure Key Vault ve [Secure anahtar kasanızı](../../key-vault/key-vault-secure-your-key-vault.md) [ile işe başlayın.](../../key-vault/key-vault-get-started.md) 

> [!WARNING]
> - VM'yi şifrelemek için Azure AD ile Azure Disk Şifreleme'yi daha önce kullandıysanız, VM'nizi şifrelemek için bu seçeneği kullanmaya devam etmelisiniz. Ayrıntılar [için Azure AD (önceki sürüm) ile Azure Disk Şifreleme için önemli bir kasa oluşturma ve yapılandırma](disk-encryption-key-vault-aad.md) yada bkz.

Azure Disk Şifrelemesi ile kullanılmak üzere anahtar kasa oluşturma ve yapılandırma üç adım içerir:

1. Gerekirse bir kaynak grubu oluşturma.
2. Anahtar kasası oluşturuyor. 
3. Anahtar kasası gelişmiş erişim ilkeleri ayarlama.

Bu adımlar aşağıdaki hızlı başlangıçlarda gösterilmiştir:

- [Azure CLI ile Linux VM oluşturma ve şifreleme](disk-encryption-cli-quickstart.md)
- [Azure PowerShell ile Linux VM oluşturma ve şifreleme](disk-encryption-cli-quickstart.md)

Ayrıca, isterseniz bir anahtar şifreleme anahtarı (KEK) oluşturabilir veya içe aktarabilirsiniz.

> [!Note]
> Bu makaledeki [adımlar, Azure Disk Şifreleme önkoşullar CLI komut dosyası](https://github.com/ejarvi/ade-cli-getting-started) ve [Azure Disk Şifreleme önkoşullar PowerShell komut otomatiktir.](https://github.com/Azure/azure-powershell/tree/master/src/Compute/Compute/Extension/AzureDiskEncryption/Scripts)

## <a name="install-tools-and-connect-to-azure"></a>Araçları yükleyin ve Azure'a bağlanın

Bu makaledeki adımlar [Azure CLI](/cli/azure/), [Azure PowerShell Az modülü](/powershell/azure/overview)veya Azure [portalı](https://portal.azure.com)ile tamamlanabilir. 

Portala tarayıcınız üzerinden erişilebilir ken, Azure CLI ve Azure PowerShell yerel yükleme gerektirir; Bkz. [Linux için Azure Disk Şifrelemesi:](disk-encryption-linux.md#install-tools-and-connect-to-azure) Ayrıntılar için araçlar yükleyin.

### <a name="connect-to-your-azure-account"></a>Azure hesabınıza bağlanma

Azure CLI veya Azure PowerShell'i kullanmadan önce öncelikle Azure aboneliğinize bağlanmanız gerekir. Bunu, [Azure CLI ile oturum açarak](/cli/azure/authenticate-azure-cli?view=azure-cli-latest), [Azure Powershell ile oturum açarak](/powershell/azure/authenticate-azureps?view=azps-2.5.0)veya istendiğinde kimlik bilgilerinizi Azure portalına sağlayarak yaparsınız.

```azurecli-interactive
az login
```

```azurepowershell-interactive
Connect-AzAccount
```

[!INCLUDE [disk-encryption-key-vault](../../../includes/disk-encryption-key-vault.md)]
 ```
 
## Next steps

- [Azure Disk Encryption prerequisites CLI script](https://github.com/ejarvi/ade-cli-getting-started)
- [Azure Disk Encryption prerequisites PowerShell script](https://github.com/Azure/azure-powershell/tree/master/src/Compute/Compute/Extension/AzureDiskEncryption/Scripts)
- Learn [Azure Disk Encryption scenarios on Linux VMs](disk-encryption-linux.md)
- Learn how to [troubleshoot Azure Disk Encryption](disk-encryption-troubleshooting.md)
- Read the [Azure Disk Encryption sample scripts](disk-encryption-sample-scripts.md)
