---
title: Yönetilen bir diski işletim sistemi diskine ekleyerek VM oluşturma - PowerShell Sample
description: Azure PowerShell Betik Örneği - Yönetilen bir diski işletim sistemi diski olarak ekleyerek sanal makine oluşturma
services: virtual-machines-linux
documentationcenter: virtual-machines
author: ramankumarlive
manager: kavithag
editor: ramankum
tags: azure-service-management
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: sample
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 05/10/2017
ms.author: ramankum
ms.custom: mvc
ms.openlocfilehash: 891c82c75c6e059e4ceeba110e1de5515755e71f
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/24/2020
ms.locfileid: "75463681"
---
# <a name="create-a-virtual-machine-using-an-existing-managed-os-disk-with-powershell"></a>PowerShell ile yönetilen bir diski işletim sistemi diski olarak ekleyerek sanal makine oluşturma

Bu betik, mevcut bir yönetilen diski işletim sistemi diski olarak ekleyerek bir sanal makine oluşturur. Yukarıdaki senaryolarda bu betiği kullanın:
* Farklı abonelikteki bir yönetilen diskten kopyalanmış mevcut bir yönetilen işletim sistemi diskinden VM oluşturma
* Özelleştirilmiş bir VHD dosyasından oluşturulmuş mevcut bir yönetilen diskten VM oluşturma 
* Anlık görüntüden oluşturulmuş mevcut bir yönetilen işletim sistemi diskinden VM oluşturma 

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

 

## <a name="sample-script"></a>Örnek betik

[!code-powershell[main](../../../powershell_scripts/virtual-machine/create-vm-from-snapshot/create-vm-from-snapshot.ps1 "Create VM from snapshot")]

## <a name="clean-up-deployment"></a>Dağıtımı temizleme

Kaynak grubunu, VM’yi ve ilgili tüm kaynakları kaldırmak için aşağıdaki komutu çalıştırın.

```powershell
Remove-AzResourceGroup -Name myResourceGroup
```

## <a name="script-explanation"></a>Betik açıklaması

Bu betik yönetilen disk özelliklerini almak, yeni bir VM’ye yönetilen bir disk eklemek ve bir VM oluşturmak için aşağıdaki komutları kullanır. Tablodaki her öğe, komuta özgü belgelere yönlendirir.

| Komut | Notlar |
|---|---|
| [AzDisk Al](https://docs.microsoft.com/powershell/module/az.compute/Get-AzDisk) | Diskin adına ve kaynak grubuna göre disk nesnesini alır. Döndürülen disk nesnesinin Id özelliği, diski yeni VM'ye bağlamak için kullanılır |
| [Yeni-AzVMConfig](https://docs.microsoft.com/powershell/module/az.compute/new-azvmconfig) | Sanal makine yapılandırması oluşturur. Bu yapılandırma; sanal makine adı, işletim sistemi ve yönetici kimlik bilgileri gibi bilgileri içerir. Yapılandırma, sanal makine oluşturulurken kullanılır. |
| [Set-AzVMOSDisk](https://docs.microsoft.com/powershell/module/az.compute/set-azvmosdisk) | Diskin Id özelliğini kullanarak yönetilen diski yeni sanal makineye işletim sistemi diski olarak ekler |
| [Yeni-AzPublicIpAddress](https://docs.microsoft.com/powershell/module/az.network/new-azpublicipaddress) | Genel bir IP adresi oluşturur. |
| [Yeni-AzNetworkInterface](https://docs.microsoft.com/powershell/module/az.network/new-aznetworkinterface) | Ağ arabirimi oluşturur. |
| [New-AzVM](https://docs.microsoft.com/powershell/module/az.compute/new-azvm) | Sanal makine oluşturur. |
|[Remove-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup) | Kaynak grubunu ve grubun içerdiği tüm kaynakları kaldırır. |

Pazar yeri görüntüleri için plan bilgilerini ayarlamak için [Set-AzVMPlan'ı](https://docs.microsoft.com/powershell/module/az.compute/set-azvmplan) kullanın.

```powershell
Set-AzVMPlan -VM $VirtualMachine -Publisher $Publisher -Product $Product -Name $Bame
```

## <a name="next-steps"></a>Sonraki adımlar

Azure PowerShell modülü hakkında daha fazla bilgi için bkz. [Azure PowerShell belgeleri](/powershell/azure/overview).

Ek sanal makine PowerShell betiği örnekleri, [Azure Linux VM belgeleri](../linux/powershell-samples.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) içinde bulunabilir.
