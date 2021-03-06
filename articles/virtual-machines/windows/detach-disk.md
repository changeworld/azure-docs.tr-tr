---
title: Bir Windows VM'den veri diskini ayırma - Azure
description: Kaynak Yöneticisi dağıtım modelini kullanarak Azure'daki sanal bir makineden veri diskini ayırın.
services: virtual-machines-windows
author: cynthn
manager: gwallace
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.topic: article
ms.date: 01/08/2020
ms.author: cynthn
ms.subservice: disks
ms.openlocfilehash: 301f3abd26f702f3f29c8833c835ba7d0e41bcaf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75834597"
---
# <a name="how-to-detach-a-data-disk-from-a-windows-virtual-machine"></a>Bir Windows sanal makinesindeki veri diskini ayırma

Sanal makineye bağlı bir veri diskine ihtiyacınız olmadığında bunu kolayca ayırabilirsiniz. Bu, diski sanal makineden kaldırır, ancak depolama dan kaldırmaz.

> [!WARNING]
> Bir diski ayırın, otomatik olarak silinmez. Premium depolama alanına abone olduysanız, disk için depolama ücreti ödemeye devam erecektir. Daha fazla bilgi [için, Premium Depolama'yı kullanırken Fiyatlandırma ve Faturalandırma'ya](disks-types.md#billing)bakın.

Disk üzerinde var olan verileri yeniden kullanmak isterseniz bu verileri aynı sanal makineye veya başka birine yeniden ekleyebilirsiniz.

 

## <a name="detach-a-data-disk-using-powershell"></a>PowerShell'i kullanarak bir veri diskini ayırın

PowerShell kullanarak bir veri diskini *sıcak* bir şekilde kaldırabilirsiniz, ancak VM'den ayırmadan önce diski etkin olarak hiçbir şeyin kullanmadığından emin olun.

Bu örnekte, **myResourceGroup** kaynak grubunda **myDisk** adlı diski VM **myVM'den** kaldırırız. Önce [Remove-AzVMDataDisk](https://docs.microsoft.com/powershell/module/az.compute/remove-azvmdatadisk) cmdlet kullanarak diski çıkarın. Ardından, veri diskini kaldırma işlemini tamamlamak için [Update-AzVM](https://docs.microsoft.com/powershell/module/az.compute/update-azvm) cmdlet'i kullanarak sanal makinenin durumunu güncellersiniz.

```azurepowershell-interactive
$VirtualMachine = Get-AzVM `
   -ResourceGroupName "myResourceGroup" `
   -Name "myVM"
Remove-AzVMDataDisk `
   -VM $VirtualMachine `
   -Name "myDisk"
Update-AzVM `
   -ResourceGroupName "myResourceGroup" `
   -VM $VirtualMachine
```

Disk depolamada kalır, ancak artık sanal bir makineye bağlı değildir.

## <a name="detach-a-data-disk-using-the-portal"></a>Portalı kullanarak veri diski çıkarma

Bir veri diskini *sıcak* bir şekilde kaldırabilirsiniz, ancak Diski VM'den ayırmadan önce hiçbir şeyin diski etkin olarak kullandığından emin olun.

1. Sol menüde **Sanal Makineler'i**seçin.
1. Ayırmak istediğiniz veri diskine sahip sanal makineyi seçin.
1. **Ayarlar**'ın altında **Diskler**’i seçin.
1. **Diskler** bölmesinin üst **kısmında, Edit'i**seçin.
1. **Diskler** bölmesinde, ayırmak istediğiniz veri diskinin en sağında, **Ayır'ı**seçin.
1. Değişikliklerinizi kaydetmek için sayfanın üst kısmında **Kaydet'i** seçin.

Disk depolamada kalır, ancak artık sanal bir makineye bağlı değildir.

## <a name="next-steps"></a>Sonraki adımlar

Veri diskini yeniden kullanmak istiyorsanız, [başka bir VM'ye ekleyebilirsiniz](attach-managed-disk-portal.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
