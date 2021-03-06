---
title: Azure'da Hizmet Kumaşı kümesini silme
description: Bu öğreticide Azure'da barındırılan bir Service Fabric kümesini ve kaynaklarını silmeyi öğreneceksiniz. Kümeyi içeren kaynak grubunu ya da seçerek kaynakları silebilirsiniz.
ms.topic: tutorial
ms.date: 07/22/2019
ms.custom: mvc
ms.openlocfilehash: 802fdfc46344929930b1ffb015b364b4e2360cca
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/24/2020
ms.locfileid: "75465373"
---
# <a name="tutorial-remove-a-service-fabric-cluster-running-in-azure"></a>Öğretici: Azure'da çalışan bir Service Fabric kümesini kaldırma

Bu öğretici, bir serinin beşinci bölümüdür ve Azure'da çalışan bir Hizmet Kumaşı kümesini nasıl sildiğinizi gösterir. Bir Service Fabric kümesini tamamen silmek için küme tarafından kullanılan tüm kaynakları da silmeniz gerekir. İki seçeneğiniz vardır: kümenin bulunduğu kaynak grubunu silmek (küme kaynağını ve kaynak grubundaki diğer tüm kaynakları siler) veya özel olarak küme kaynağını ve (kaynak grubundaki diğer kaynakları silmeden) kümenin ilişkili kaynaklarını silmek.

Bu öğreticide şunların nasıl yapıldığını öğrenirsiniz:

> [!div class="checklist"]
> * Bir kaynak grubunu ve tüm kaynaklarını silmek
> * Kaynak grubundan seçerek kaynak silmek

Bu öğretici dizisinde şunların nasıl yapıldığını öğrenirsiniz:
> [!div class="checklist"]
> * Şablon kullanarak Azure'da güvenli bir [Windows kümesi](service-fabric-tutorial-create-vnet-and-windows-cluster.md) oluşturma
> * [Bir kümeyi izleme](service-fabric-tutorial-monitor-cluster.md)
> * [Bir kümenin ölçeğini daraltma veya genişletme](service-fabric-tutorial-scale-cluster.md)
> * [Bir kümenin çalışma zamanını yükseltme](service-fabric-tutorial-upgrade-cluster.md)
> * Küme silme


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Ön koşullar

Bu öğreticiye başlamadan önce:

* Azure aboneliğiniz yoksa [ücretsiz bir hesap](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) oluşturun
* [Azure Powershell](https://docs.microsoft.com/powershell/azure//install-Az-ps) veya [Azure CLI'yi yükleyin.](/cli/azure/install-azure-cli)
* Azure'da güvenli bir [Windows kümesi](service-fabric-tutorial-create-vnet-and-windows-cluster.md) oluşturma

## <a name="delete-the-resource-group-containing-the-service-fabric-cluster"></a>Service Fabric kümesini içeren kaynak grubunu silme
Kümeyi ve kullandığı tüm kaynakları silmenin en basit yolu, kaynak grubunun silinmesidir.

Azure'da oturum açın ve kümeyi kaldırmak istediğiniz abonelik kimliğini seçin.  Abonelik kimliğinizi, [Azure portalında](https://portal.azure.com) oturum açarak öğrenebilirsiniz. [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) cmdlet veya az group delete komutunu kullanarak kaynak grubunu ve tüm küme kaynaklarını [silin.](/cli/azure/group?view=azure-cli-latest)

```powershell
Connect-AzAccount
Set-AzContext -SubscriptionId <guid>
$groupname = "sfclustertutorialgroup"
Remove-AzResourceGroup -Name $groupname -Force
```

```azurecli
az login
az account set --subscription <guid>
ResourceGroupName="sfclustertutorialgroup"
az group delete --name $ResourceGroupName
```

## <a name="selectively-delete-the-cluster-resource-and-the-associated-resources"></a>Küme kaynağını ve ilişkili kaynakları seçerek silme
Kaynak grubunuzda sadece silmek istediğiniz Service Fabric kümesiyle ilgili kaynaklar varsa, kaynak grubunun tamamını silmek daha kolaydır. Kaynak grubunuzdaki kaynakları seçerek silmek ve kümeyle ilişkili olmayan kaynakları korumak istiyorsanız, bu durumda aşağıdaki adımları izleyin.

Kaynak grubundaki kaynakları listeleyin:

```powershell
Connect-AzAccount
Set-AzContext -SubscriptionId <guid>
$groupname = "sfclustertutorialgroup"
Get-AzResource -ResourceGroupName $groupname | ft
```

```azurecli
az login
az account set --subscription <guid>
ResourceGroupName="sfclustertutorialgroup"
az resource list --resource-group $ResourceGroupName
```

Silmek istediğiniz kaynaklardan her biri için aşağıdaki betiği çalıştırın:

```powershell
Remove-AzResource -ResourceName "<name of the Resource>" -ResourceType "<Resource Type>" -ResourceGroupName $groupname -Force
```

```azurecli
az resource delete --name "<name of the Resource>" --resource-type "<Resource Type>" --resource-group $ResourceGroupName
```

Küme kaynağını silmek için aşağıdaki betiği çalıştırın:

```powershell
Remove-AzResource -ResourceName "<name of the Resource>" -ResourceType "Microsoft.ServiceFabric/clusters" -ResourceGroupName $groupname -Force
```

```azurecli
az resource delete --name "<name of the Resource>" --resource-type "Microsoft.ServiceFabric/clusters" --resource-group $ResourceGroupName
```

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide, şunların nasıl yapıldığını öğrendiniz:

> [!div class="checklist"]
> * Bir kaynak grubunu ve tüm kaynaklarını silmek
> * Kaynak grubundan seçerek kaynak silmek

Bu öğreticiyi tamamladığınıza göre aşağıdakileri deneyebilirsiniz:
* [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md)'ı kullanarak Service Fabric kümesini inceleme ve yönetme hakkında bilgi edinin.
* Küme düğümlerinin Windows işletim sistemini nasıl [yamalayabildiğini](service-fabric-patch-orchestration-application.md) öğrenin.
* Küme olaylarını izlemek için [Windows kümeleri](service-fabric-diagnostics-event-aggregation-wad.md) ve kurulum Log Analytics için etkinlikleri nasıl toplayıp [toplayacaklarını](service-fabric-diagnostics-oms-setup.md) öğrenin.
