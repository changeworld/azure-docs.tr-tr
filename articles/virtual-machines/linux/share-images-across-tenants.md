---
title: Azure'da galeri resimlerini kiracılar arasında paylaşın
description: Paylaşılan Resim Galerileri'ni kullanarak VM görüntülerini Azure kiracıları arasında nasıl paylaşılamayın gerektiğini öğrenin.
services: virtual-machines-linux
author: cynthn
manager: gwallace
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: article
ms.date: 04/05/2019
ms.author: cynthn
ms.openlocfilehash: 18337620a6f9506e402149909667026e4a8ba7eb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74034984"
---
# <a name="share-gallery-vm-images-across-azure-tenants"></a>Galeri VM görüntülerini Azure kiracıları arasında paylaşın

Paylaşılan Resim Galerileri, RBAC kullanarak görüntüleri paylaşmanıza izin sağlar. RBAC'ı kiracınızın içindeki görüntüleri paylaşmak için ve hatta kiracınızın dışındaki kişilerle bile kullanabilirsiniz. Bu basit paylaşım seçeneği hakkında daha fazla bilgi için [galeriyi Paylaş'a](/azure/virtual-machines/linux/shared-images-portal#share-the-gallery)bakın.

[!INCLUDE [virtual-machines-share-images-across-tenants](../../../includes/virtual-machines-share-images-across-tenants.md)]

> [!IMPORTANT]
> Portalı, başka bir azure kiracıdaki görüntüden vm dağıtmak için kullanamazsınız. Kiracılar arasında paylaşılan bir resimden VM oluşturmak için Azure CLI veya [Powershell'i](../windows/share-images-across-tenants.md)kullanmanız gerekir.

## <a name="create-a-vm-using-azure-cli"></a>Azure CLI kullanarak VM oluşturma

AppID, uygulama anahtarı ve kiracı kimliği 1'i kullanarak kiracı 1 için hizmet ilkesini oturum açın. Gerekirse kiracı `az account show --query "tenantId"` nın doğrulanmış larını almak için kullanabilirsiniz.

```azurecli-interactive
az account clear
az login --service-principal -u '<app ID>' -p '<Secret>' --tenant '<tenant 1 ID>'
az account get-access-token 
```
 
AppID,uygulama anahtarı ve kiracı kimliği 2'yi kullanarak kiracı 2 için hizmet ilkesini oturum açın:

```azurecli-interactive
az login --service-principal -u '<app ID>' -p '<Secret>' --tenant '<tenant 2 ID>'
az account get-access-token
```

VM'yi oluşturun. Örnekteki bilgileri kendi bilginizle değiştirin.

```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVM \
  --image "/subscriptions/<Tenant 1 subscription>/resourceGroups/<Resource group>/providers/Microsoft.Compute/galleries/<Gallery>/images/<Image definition>/versions/<version>" \
  --admin-username azureuser \
  --generate-ssh-keys
```

## <a name="next-steps"></a>Sonraki adımlar

Herhangi bir sorunla karşınıza çıkarsa, [paylaşılan resim galerilerini sorun giderebilirsiniz.](troubleshooting-shared-images.md)