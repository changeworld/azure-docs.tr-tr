---
title: Azure PowerShell ve Azure Site Kurtarma'yı kullanarak Azure VM'leri için olağanüstü durum kurtarma
description: Azure PowerShell'i kullanarak Azure Site Kurtarma ile Azure sanal makineleri için olağanüstü durum kurtarma yı nasıl ayarlayamanızı öğrenin.
services: site-recovery
author: sujayt
manager: rochakm
ms.topic: article
ms.date: 3/29/2019
ms.author: sutalasi
ms.openlocfilehash: 583511194fb100add1d5fc4ea9c06a869cf652b5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77212274"
---
# <a name="set-up-disaster-recovery-for-azure-virtual-machines-using-azure-powershell"></a>Azure PowerShell'i kullanarak Azure sanal makineleri için olağanüstü durum kurtarma yı ayarlama

Bu makalede, Azure PowerShell kullanarak Azure sanal makineleri için olağanüstü durum kurtarmanın nasıl ayarlanıp test edilebildiğini görüyorsunuz.

Aşağıdakileri nasıl yapacağınızı öğrenirsiniz:

> [!div class="checklist"]
> - Kurtarma Hizmetleri kasası oluşturun.
> - PowerShell oturumu için kasa bağlamını ayarlayın.
> - Azure sanal makinelerini çoğaltmaya başlamak için kasayı hazırlayın.
> - Ağ eşlemeleri oluşturun.
> - Sanal makineleri çoğaltmak için depolama hesapları oluşturun.
> - Olağanüstü durum kurtarma için Azure sanal makinelerini kurtarma bölgesine çoğaltma.
> - Bir test başarısız, doğrulamak ve temizleme testi failover gerçekleştirin.
> - Kurtarma bölgesine geçeme.

> [!NOTE]
> Portal aracılığıyla kullanılabilen tüm senaryo özellikleri Azure PowerShell üzerinden kullanılamayabilir. Şu anda Azure PowerShell tarafından desteklenmeyen senaryo özelliklerinden bazıları şunlardır:
> - Sanal makinedeki tüm disklerin, sanal makinenin her diskini açıkça belirtmeye gerek kalmadan çoğaltılması gerektiğini belirtme yeteneği.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Ön koşullar

Başlamadan önce:
- [Senaryo mimarisini ve bileşenlerini ](azure-to-azure-architecture.md) anladığınızdan emin olun.
- Tüm bileşenler için [destek gereksinimlerini](azure-to-azure-support-matrix.md) gözden geçirin.
- Azure PowerShell `Az` modülüne sahipsiniz. Azure PowerShell'i yüklemeniz veya yükseltmeniz gerekiyorsa, [Azure PowerShell'i yüklemek ve yapılandırmak için](/powershell/azure/install-az-ps)bu Kılavuzu izleyin.

## <a name="sign-in-to-your-microsoft-azure-subscription"></a>Microsoft Azure aboneliğinizde oturum açın

`Connect-AzAccount` Cmdlet ile Azure aboneliğinizde oturum açın.

```azurepowershell
Connect-AzAccount
```

Azure aboneliğinizi seçin. Erişiminiz `Get-AzSubscription` olan Azure aboneliklerinin listesini almak için cmdlet'i kullanın. Cmdlet'i kullanarak çalışmak `Set-AzContext` için Azure aboneliğini seçin.

```azurepowershell
Set-AzContext -SubscriptionId "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
```

## <a name="get-details-of-the-virtual-machine-to-be-replicated"></a>Çoğaltılacak sanal makinenin ayrıntılarını alın

Bu makalede, Doğu ABD bölgesinde sanal bir makine çoğaltılır ve Batı ABD 2 bölgesinde kurtarıldı. Çoğaltılan sanal makinenin bir işletim sistemi diski ve tek bir veri diski vardır. Örnekte kullanılan sanal makinenin adıdır. `AzureDemoVM`

```azurepowershell
# Get details of the virtual machine
$VM = Get-AzVM -ResourceGroupName "A2AdemoRG" -Name "AzureDemoVM"

Write-Output $VM
```

```Output
ResourceGroupName  : A2AdemoRG
Id                 : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/A2AdemoRG/providers/Microsoft.Compute/virtualMachines/AzureDemoVM
VmId               : 1b864902-c7ea-499a-ad0f-65da2930b81b
Name               : AzureDemoVM
Type               : Microsoft.Compute/virtualMachines
Location           : eastus
Tags               : {}
DiagnosticsProfile : {BootDiagnostics}
HardwareProfile    : {VmSize}
NetworkProfile     : {NetworkInterfaces}
OSProfile          : {ComputerName, AdminUsername, WindowsConfiguration, Secrets}
ProvisioningState  : Succeeded
StorageProfile     : {ImageReference, OsDisk, DataDisks}
```

Sanal makinenin diskleri için disk ayrıntılarını alın. Disk ayrıntıları daha sonra sanal makine için çoğaltma başlatırken kullanılacaktır.

```azurepowershell
$OSDiskVhdURI = $VM.StorageProfile.OsDisk.Vhd
$DataDisk1VhdURI = $VM.StorageProfile.DataDisks[0].Vhd
```

## <a name="create-a-recovery-services-vault"></a>Kurtarma Hizmetleri kasası oluşturma

Kurtarma Hizmetleri kasasını oluşturmak için bir kaynak grubu oluşturun.

> [!IMPORTANT]
> * Kurtarma hizmetleri kasası ve korunan sanal makineler farklı Azure konumlarında olmalıdır.
> * Kurtarma hizmetleri kasasının kaynak grubu ve korunan sanal makinelerin farklı Azure konumlarında olması gerekir.
> * Kurtarma hizmetleri kasası ve ait olduğu kaynak grubu aynı Azure konumunda olabilir.

Bu makaledeki örnekte, korunan sanal makine Doğu ABD bölgesindedir. Olağanüstü durum kurtarma için seçilen kurtarma bölgesi Batı ABD 2 bölgesidir. Kurtarma hizmetleri kasası ve kasanın kaynak grubu, her ikisi de kurtarma bölgesi Olan Batı ABD 2'de.

```azurepowershell
#Create a resource group for the recovery services vault in the recovery Azure region
New-AzResourceGroup -Name "a2ademorecoveryrg" -Location "West US 2"
```

```Output
ResourceGroupName : a2ademorecoveryrg
Location          : westus2
ProvisioningState : Succeeded
Tags              :
ResourceId        : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/a2ademorecoveryrg
```

Kurtarma hizmetleri kasası oluşturun. Bu örnekte, Batı ABD `a2aDemoRecoveryVault` 2 bölgesinde adlandırılmış bir Kurtarma Hizmetleri kasası oluşturulur.

```azurepowershell
#Create a new Recovery services vault in the recovery region
$vault = New-AzRecoveryServicesVault -Name "a2aDemoRecoveryVault" -ResourceGroupName "a2ademorecoveryrg" -Location "West US 2"

Write-Output $vault
```

```Output
Name              : a2aDemoRecoveryVault
ID                : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/a2ademorecoveryrg/providers/Microsoft.RecoveryServices/vaults/a2aDemoRecoveryVault
Type              : Microsoft.RecoveryServices/vaults
Location          : westus2
ResourceGroupName : a2ademorecoveryrg
SubscriptionId    : xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
Properties        : Microsoft.Azure.Commands.RecoveryServices.ARSVaultProperties
```

## <a name="set-the-vault-context"></a>Kasa bağlamını ayarlama

PowerShell oturumunda kullanılmak üzere kasa bağlamını ayarlayın. Kasa bağlamı ayarlandıktan sonra, PowerShell oturumundaki Azure Site Kurtarma işlemleri seçili kasa bağlamında gerçekleştirilir.

```azurepowershell
#Setting the vault context.
Set-AzRecoveryServicesAsrVaultContext -Vault $vault
```

```Output
ResourceName         ResourceGroupName ResourceNamespace          ResourceType
------------         ----------------- -----------------          -----------
a2aDemoRecoveryVault a2ademorecoveryrg Microsoft.RecoveryServices Vaults
```

```azurepowershell
#Delete the downloaded vault settings file
Remove-Item -Path $Vaultsettingsfile.FilePath
```

Azure'dan Azure'a geçiş için kasa bağlamını yeni oluşturulan kasaya ayarlayabilirsiniz:

```azurepowershell
#Set the vault context for the PowerShell session.
Set-AzRecoveryServicesAsrVaultContext -Vault $vault
```

## <a name="prepare-the-vault-to-start-replicating-azure-virtual-machines"></a>Azure sanal makinelerini çoğaltmaya başlamak için kasayı hazırlayın

### <a name="create-a-site-recovery-fabric-object-to-represent-the-primary-source-region"></a>Birincil (kaynak) bölgeyi temsil edecek bir Site Kurtarma kumaş nesnesi oluşturma

Kasadaki kumaş nesnesi bir Azure bölgesini temsil eder. Birincil kumaş nesnesi, kasaya korunan sanal makinelerin ait olduğu Azure bölgesini temsil etmek üzere oluşturulur. Bu makaledeki örnekte, korunan sanal makine Doğu ABD bölgesindedir.

- Bölge başına yalnızca bir kumaş nesnesi oluşturulabilir.
- Azure portalında bir VM için Site Kurtarma çoğaltmasını daha önce etkinleştirdiyseniz, Site Kurtarma otomatik olarak bir kumaş nesnesi oluşturur. Bir bölge için bir kumaş nesnesi varsa, yeni bir nesne oluşturamazsınız.

Başlamadan önce, Site Kurtarma işlemlerinin eş senkronize bir şekilde yürütüldettiğini anlayın. Bir işlem başlattığınızda, bir Azure Site Kurtarma işi gönderilir ve bir iş izleme nesnesi döndürülür. İş için en son durumu almak ve`Get-AzRecoveryServicesAsrJob`işlemin durumunu izlemek için iş izleme nesnesini kullanın.

```azurepowershell
#Create Primary ASR fabric
$TempASRJob = New-AzRecoveryServicesAsrFabric -Azure -Location 'East US'  -Name "A2Ademo-EastUS"

# Track Job status to check for completion
while (($TempASRJob.State -eq "InProgress") -or ($TempASRJob.State -eq "NotStarted")){
        #If the job hasn't completed, sleep for 10 seconds before checking the job status again
        sleep 10;
        $TempASRJob = Get-AzRecoveryServicesAsrJob -Job $TempASRJob
}

#Check if the Job completed successfully. The updated job state of a successfully completed job should be "Succeeded"
Write-Output $TempASRJob.State

$PrimaryFabric = Get-AzRecoveryServicesAsrFabric -Name "A2Ademo-EastUS"
```

Birden çok Azure bölgesinden gelen sanal makineler aynı kasaya korunuyorsa, her kaynak Azure bölgesi için bir kumaş nesnesi oluşturun.

### <a name="create-a-site-recovery-fabric-object-to-represent-the-recovery-region"></a>Kurtarma bölgesini temsil etmek için Site Kurtarma kumaş nesnesi oluşturma

Kurtarma dokusu nesnesi kurtarma Azure konumunu temsil eder. Bir arıza varsa, sanal makineler çoğaltılır ve kurtarma dokusu tarafından temsil edilen kurtarma bölgesine kurtarılır. Bu örnekte kullanılan kurtarma Azure bölgesi West US 2'dir.

```azurepowershell
#Create Recovery ASR fabric
$TempASRJob = New-AzRecoveryServicesAsrFabric -Azure -Location 'West US 2'  -Name "A2Ademo-WestUS"

# Track Job status to check for completion
while (($TempASRJob.State -eq "InProgress") -or ($TempASRJob.State -eq "NotStarted")){
        sleep 10;
        $TempASRJob = Get-AzRecoveryServicesAsrJob -Job $TempASRJob
}

#Check if the Job completed successfully. The updated job state of a successfully completed job should be "Succeeded"
Write-Output $TempASRJob.State

$RecoveryFabric = Get-AzRecoveryServicesAsrFabric -Name "A2Ademo-WestUS"
```

### <a name="create-a-site-recovery-protection-container-in-the-primary-fabric"></a>Birincil kumaşta Site Kurtarma koruma kabı oluşturma

Koruma kapsayıcısı, çoğaltılan öğeleri bir kumaş içinde gruplandırmak için kullanılan bir kapsayıcıdır.

```azurepowershell
#Create a Protection container in the primary Azure region (within the Primary fabric)
$TempASRJob = New-AzRecoveryServicesAsrProtectionContainer -InputObject $PrimaryFabric -Name "A2AEastUSProtectionContainer"

#Track Job status to check for completion
while (($TempASRJob.State -eq "InProgress") -or ($TempASRJob.State -eq "NotStarted")){
        sleep 10;
        $TempASRJob = Get-AzRecoveryServicesAsrJob -Job $TempASRJob
}

Write-Output $TempASRJob.State

$PrimaryProtContainer = Get-AzRecoveryServicesAsrProtectionContainer -Fabric $PrimaryFabric -Name "A2AEastUSProtectionContainer"
```

### <a name="create-a-site-recovery-protection-container-in-the-recovery-fabric"></a>Kurtarma dokusunda Bir Site Kurtarma koruma kabı oluşturma

```azurepowershell
#Create a Protection container in the recovery Azure region (within the Recovery fabric)
$TempASRJob = New-AzRecoveryServicesAsrProtectionContainer -InputObject $RecoveryFabric -Name "A2AWestUSProtectionContainer"

#Track Job status to check for completion
while (($TempASRJob.State -eq "InProgress") -or ($TempASRJob.State -eq "NotStarted")){
        sleep 10;
        $TempASRJob = Get-AzRecoveryServicesAsrJob -Job $TempASRJob
}

#Check if the Job completed successfully. The updated job state of a successfully completed job should be "Succeeded"

Write-Output $TempASRJob.State

$RecoveryProtContainer = Get-AzRecoveryServicesAsrProtectionContainer -Fabric $RecoveryFabric -Name "A2AWestUSProtectionContainer"
```

### <a name="create-a-replication-policy"></a>Çoğaltma ilkesi oluşturma

```azurepowershell
#Create replication policy
$TempASRJob = New-AzRecoveryServicesAsrPolicy -AzureToAzure -Name "A2APolicy" -RecoveryPointRetentionInHours 24 -ApplicationConsistentSnapshotFrequencyInHours 4

#Track Job status to check for completion
while (($TempASRJob.State -eq "InProgress") -or ($TempASRJob.State -eq "NotStarted")){
        sleep 10;
        $TempASRJob = Get-AzRecoveryServicesAsrJob -Job $TempASRJob
}

#Check if the Job completed successfully. The updated job state of a successfully completed job should be "Succeeded"
Write-Output $TempASRJob.State

$ReplicationPolicy = Get-AzRecoveryServicesAsrPolicy -Name "A2APolicy"
```

### <a name="create-a-protection-container-mapping-between-the-primary-and-recovery-protection-container"></a>Birincil ve kurtarma koruma kapsayıcısı arasında bir koruma kapsayıcısı eşleme oluşturma

Koruma kapsayıcısı eşleme, birincil koruma kapsayıcısını kurtarma koruma kapsayıcısı ve çoğaltma ilkesiyle eşler. Sanal makineleri bir koruma kapsayıcısı çifti arasında çoğaltmak için kullanacağınız her çoğaltma ilkesi için bir eşleme oluşturun.

```azurepowershell
#Create Protection container mapping between the Primary and Recovery Protection Containers with the Replication policy
$TempASRJob = New-AzRecoveryServicesAsrProtectionContainerMapping -Name "A2APrimaryToRecovery" -Policy $ReplicationPolicy -PrimaryProtectionContainer $PrimaryProtContainer -RecoveryProtectionContainer $RecoveryProtContainer

#Track Job status to check for completion
while (($TempASRJob.State -eq "InProgress") -or ($TempASRJob.State -eq "NotStarted")){
        sleep 10;
        $TempASRJob = Get-AzRecoveryServicesAsrJob -Job $TempASRJob
}

#Check if the Job completed successfully. The updated job state of a successfully completed job should be "Succeeded"
Write-Output $TempASRJob.State

$EusToWusPCMapping = Get-AzRecoveryServicesAsrProtectionContainerMapping -ProtectionContainer $PrimaryProtContainer -Name "A2APrimaryToRecovery"
```

### <a name="create-a-protection-container-mapping-for-failback-reverse-replication-after-a-failover"></a>Failback için bir koruma kapsayıcısı eşleme oluşturma (bir hata dan sonra ters çoğaltma)

Bir arızadan sonra, başarısız olan sanal makineyi orijinal Azure bölgesine geri getirmeye hazır olduğunuzda, bir geri ödeme yaparsınız. Geri başarısız olmak için, sanal makine üzerinde başarısız olan hata, başarısız bölgeden orijinal bölgeye ters kopyalanır. Ters çoğaltma için özgün bölgenin rolleri ve kurtarma bölgesi anahtarı. Orijinal bölge artık yeni kurtarma bölgesi haline gelir ve başlangıçta kurtarma bölgesi olan bölge artık birincil bölge haline gelir. Ters çoğaltma için koruma kapsayıcısı eşlemesi, özgün ve kurtarma bölgelerinin anahtarlanmış rollerini temsil eder.

```azurepowershell
#Create Protection container mapping (for fail back) between the Recovery and Primary Protection Containers with the Replication policy
$TempASRJob = New-AzRecoveryServicesAsrProtectionContainerMapping -Name "A2ARecoveryToPrimary" -Policy $ReplicationPolicy -PrimaryProtectionContainer $RecoveryProtContainer -RecoveryProtectionContainer $PrimaryProtContainer

#Track Job status to check for completion
while (($TempASRJob.State -eq "InProgress") -or ($TempASRJob.State -eq "NotStarted")){
        sleep 10;
        $TempASRJob = Get-AzRecoveryServicesAsrJob -Job $TempASRJob
}

#Check if the Job completed successfully. The updated job state of a successfully completed job should be "Succeeded"
Write-Output $TempASRJob.State

$WusToEusPCMapping = Get-AzRecoveryServicesAsrProtectionContainerMapping -ProtectionContainer $RecoveryProtContainer -Name "A2ARecoveryToPrimary"
```

## <a name="create-cache-storage-account-and-target-storage-account"></a>Önbellek depolama hesabı ve hedef depolama hesabı oluşturma

Önbellek depolama hesabı, çoğaltılan sanal makineyle aynı Azure bölgesinde bulunan standart bir depolama hesabıdır. Önbellek depolama hesabı, değişiklikler kurtarma Azure bölgesine taşınmadan önce çoğaltma değişikliklerini geçici olarak tutmak için kullanılır. Sanal makinenin farklı diskleri için farklı önbellek depolama hesapları belirtmeyi seçebilirsiniz, ancak gerekli değildir.

```azurepowershell
#Create Cache storage account for replication logs in the primary region
$EastUSCacheStorageAccount = New-AzStorageAccount -Name "a2acachestorage" -ResourceGroupName "A2AdemoRG" -Location 'East US' -SkuName Standard_LRS -Kind Storage
```

**Yönetilen diskleri kullanmayan**sanal makineler için hedef depolama hesabı, sanal makinenin disklerinin çoğaltıldığı kurtarma bölgesindeki depolama hesabıdır. Hedef depolama hesabı standart bir depolama hesabı veya premium depolama hesabı olabilir. Diskler için veri değiştirme hızına (IO yazma oranı) ve depolama türü için Azure Site Kurtarma destekli karmaşa sınırlarına göre gereken depolama hesabı türünü seçin.

```azurepowershell
#Create Target storage account in the recovery region. In this case a Standard Storage account
$WestUSTargetStorageAccount = New-AzStorageAccount -Name "a2atargetstorage" -ResourceGroupName "a2ademorecoveryrg" -Location 'West US 2' -SkuName Standard_LRS -Kind Storage
```

## <a name="create-network-mappings"></a>Ağ eşlemeleri oluşturma

Ağ eşleme, birincil bölgedeki sanal ağları kurtarma bölgesindeki sanal ağlarla eşler. Ağ eşlemesi, kurtarma bölgesindeki Azure sanal ağını birincil sanal ağdaki sanal bir makinenin başarısız olması gerektiğini belirtir. Bir Azure sanal ağı, kurtarma bölgesindeki tek bir Azure sanal ağına eşlenebilir.

- Kurtarma bölgesinde başarısız olması için bir Azure sanal ağı oluşturun:

   ```azurepowershell
    #Create a Recovery Network in the recovery region
    $WestUSRecoveryVnet = New-AzVirtualNetwork -Name "a2arecoveryvnet" -ResourceGroupName "a2ademorecoveryrg" -Location 'West US 2' -AddressPrefix "10.0.0.0/16"

    Add-AzVirtualNetworkSubnetConfig -Name "default" -VirtualNetwork $WestUSRecoveryVnet -AddressPrefix "10.0.0.0/20" | Set-AzVirtualNetwork

    $WestUSRecoveryNetwork = $WestUSRecoveryVnet.Id
   ```

- Birincil sanal ağı alın. Sanal makinenin bağlı olduğu VNet:

   ```azurepowershell
    #Retrieve the virtual network that the virtual machine is connected to

    #Get first network interface card(nic) of the virtual machine
    $SplitNicArmId = $VM.NetworkProfile.NetworkInterfaces[0].Id.split("/")

    #Extract resource group name from the ResourceId of the nic
    $NICRG = $SplitNicArmId[4]

    #Extract resource name from the ResourceId of the nic
    $NICname = $SplitNicArmId[-1]

    #Get network interface details using the extracted resource group name and resource name
    $NIC = Get-AzNetworkInterface -ResourceGroupName $NICRG -Name $NICname

    #Get the subnet ID of the subnet that the nic is connected to
    $PrimarySubnet = $NIC.IpConfigurations[0].Subnet

    # Extract the resource ID of the Azure virtual network the nic is connected to from the subnet ID
    $EastUSPrimaryNetwork = (Split-Path(Split-Path($PrimarySubnet.Id))).Replace("\","/")
   ```

- Birincil sanal ağ ile kurtarma sanal ağı arasında ağ eşleme oluşturma:

   ```azurepowershell
    #Create an ASR network mapping between the primary Azure virtual network and the recovery Azure virtual network
    $TempASRJob = New-AzRecoveryServicesAsrNetworkMapping -AzureToAzure -Name "A2AEusToWusNWMapping" -PrimaryFabric $PrimaryFabric -PrimaryAzureNetworkId $EastUSPrimaryNetwork -RecoveryFabric $RecoveryFabric -RecoveryAzureNetworkId $WestUSRecoveryNetwork

    #Track Job status to check for completion
    while (($TempASRJob.State -eq "InProgress") -or ($TempASRJob.State -eq "NotStarted")){
            sleep 10;
            $TempASRJob = Get-AzRecoveryServicesAsrJob -Job $TempASRJob
    }

    #Check if the Job completed successfully. The updated job state of a successfully completed job should be "Succeeded"
    Write-Output $TempASRJob.State
   ```

- Ters yönde ağ eşleme oluşturma (geri başarısız):

    ```azurepowershell
    #Create an ASR network mapping for fail back between the recovery Azure virtual network and the primary Azure virtual network
    $TempASRJob = New-AzRecoveryServicesAsrNetworkMapping -AzureToAzure -Name "A2AWusToEusNWMapping" -PrimaryFabric $RecoveryFabric -PrimaryAzureNetworkId $WestUSRecoveryNetwork -RecoveryFabric $PrimaryFabric -RecoveryAzureNetworkId $EastUSPrimaryNetwork

    #Track Job status to check for completion
    while (($TempASRJob.State -eq "InProgress") -or ($TempASRJob.State -eq "NotStarted")){
            sleep 10;
            $TempASRJob = Get-AzRecoveryServicesAsrJob -Job $TempASRJob
    }

    #Check if the Job completed successfully. The updated job state of a successfully completed job should be "Succeeded"
    Write-Output $TempASRJob.State
    ```

## <a name="replicate-azure-virtual-machine"></a>Azure sanal makineyi çoğaltma

Azure sanal makinesini **yönetilen disklerle**çoğaltın.

```azurepowershell
#Get the resource group that the virtual machine must be created in when failed over.
$RecoveryRG = Get-AzResourceGroup -Name "a2ademorecoveryrg" -Location "West US 2"

#Specify replication properties for each disk of the VM that is to be replicated (create disk replication configuration)

#OsDisk
$OSdiskId = $vm.StorageProfile.OsDisk.ManagedDisk.Id
$RecoveryOSDiskAccountType = $vm.StorageProfile.OsDisk.ManagedDisk.StorageAccountType
$RecoveryReplicaDiskAccountType = $vm.StorageProfile.OsDisk.ManagedDisk.StorageAccountType

$OSDiskReplicationConfig = New-AzRecoveryServicesAsrAzureToAzureDiskReplicationConfig -ManagedDisk -LogStorageAccountId $EastUSCacheStorageAccount.Id `
         -DiskId $OSdiskId -RecoveryResourceGroupId  $RecoveryRG.ResourceId -RecoveryReplicaDiskAccountType  $RecoveryReplicaDiskAccountType `
         -RecoveryTargetDiskAccountType $RecoveryOSDiskAccountType

# Data disk
$datadiskId1 = $vm.StorageProfile.DataDisks[0].ManagedDisk.Id
$RecoveryReplicaDiskAccountType = $vm.StorageProfile.DataDisks[0].ManagedDisk.StorageAccountType
$RecoveryTargetDiskAccountType = $vm.StorageProfile.DataDisks[0].ManagedDisk.StorageAccountType

$DataDisk1ReplicationConfig  = New-AzRecoveryServicesAsrAzureToAzureDiskReplicationConfig -ManagedDisk -LogStorageAccountId $CacheStorageAccount.Id `
         -DiskId $datadiskId1 -RecoveryResourceGroupId $RecoveryRG.ResourceId -RecoveryReplicaDiskAccountType $RecoveryReplicaDiskAccountType `
         -RecoveryTargetDiskAccountType $RecoveryTargetDiskAccountType

#Create a list of disk replication configuration objects for the disks of the virtual machine that are to be replicated.
$diskconfigs = @()
$diskconfigs += $OSDiskReplicationConfig, $DataDisk1ReplicationConfig

#Start replication by creating replication protected item. Using a GUID for the name of the replication protected item to ensure uniqueness of name.
$TempASRJob = New-AzRecoveryServicesAsrReplicationProtectedItem -AzureToAzure -AzureVmId $VM.Id -Name (New-Guid).Guid -ProtectionContainerMapping $EusToWusPCMapping -AzureToAzureDiskReplicationConfiguration $diskconfigs -RecoveryResourceGroupId $RecoveryRG.ResourceId
```

Azure sanal makinesini **yönetilmeyen disklerle**çoğaltma.

```azurepowershell
#Specify replication properties for each disk of the VM that is to be replicated (create disk replication configuration)

#Disk replication configuration for the OS disk
$OSDiskReplicationConfig = New-AzRecoveryServicesAsrAzureToAzureDiskReplicationConfig -VhdUri $OSDiskVhdURI.Uri -LogStorageAccountId $EastUSCacheStorageAccount.Id -RecoveryAzureStorageAccountId $WestUSTargetStorageAccount.Id

#Disk replication configuration for data disk
$DataDisk1ReplicationConfig = New-AzRecoveryServicesAsrAzureToAzureDiskReplicationConfig -VhdUri $DataDisk1VhdURI.Uri -LogStorageAccountId $EastUSCacheStorageAccount.Id -RecoveryAzureStorageAccountId $WestUSTargetStorageAccount.Id

#Create a list of disk replication configuration objects for the disks of the virtual machine that are to be replicated.
$diskconfigs = @()
$diskconfigs += $OSDiskReplicationConfig, $DataDisk1ReplicationConfig

#Get the resource group that the virtual machine must be created in when failed over.
$RecoveryRG = Get-AzResourceGroup -Name "a2ademorecoveryrg" -Location "West US 2"

#Start replication by creating replication protected item. Using a GUID for the name of the replication protected item to ensure uniqueness of name.
$TempASRJob = New-AzRecoveryServicesAsrReplicationProtectedItem -AzureToAzure -AzureVmId $VM.Id -Name (New-Guid).Guid -ProtectionContainerMapping $EusToWusPCMapping -AzureToAzureDiskReplicationConfiguration $diskconfigs -RecoveryResourceGroupId $RecoveryRG.ResourceId

#Track Job status to check for completion
while (($TempASRJob.State -eq "InProgress") -or ($TempASRJob.State -eq "NotStarted")){
        sleep 10;
        $TempASRJob = Get-AzRecoveryServicesAsrJob -Job $TempASRJob
}

#Check if the Job completed successfully. The updated job state of a successfully completed job should be "Succeeded"
Write-Output $TempASRJob.State
```

Başlangıç çoğaltma işlemi başarılı olduğunda, sanal makine verileri kurtarma bölgesine çoğaltılır.

Çoğaltma işlemi başlangıçta kurtarma bölgesindeki sanal makinenin çoğaltma disklerinin bir kopyasını tohumlayarak başlar. Bu aşama, ilk çoğaltma aşaması olarak adlandırılır.

AFter ilk çoğaltma tamamlar, çoğaltma diferansiyel eşitleme aşamasına taşınır. Bu noktada, sanal makine korunur ve üzerinde bir test failover işlemi yapılabilir. Sanal makineyi temsil eden çoğaltılan öğenin çoğaltma durumu, ilk çoğaltma tamamlandıktan sonra **Korumalı** duruma gider.

Sanal makinenin çoğaltma durumunu ve çoğaltma durumunu, ona karşılık gelen çoğaltma korumalı öğenin ayrıntılarını alarak izleyin.

```azurepowershell
Get-AzRecoveryServicesAsrReplicationProtectedItem -ProtectionContainer $PrimaryProtContainer | Select FriendlyName, ProtectionState, ReplicationHealth
```

```
FriendlyName ProtectionState ReplicationHealth
------------ --------------- -----------------
AzureDemoVM  Protected       Normal
```

## <a name="do-a-test-failover-validate-and-cleanup-test-failover"></a>Bir test başarısız, doğrulamak ve temizleme testi failover yapın

Sanal makine için çoğaltma korumalı bir duruma ulaştıktan sonra, sanal makinede (sanal makinenin çoğaltma korumalı öğesiüzerinde) bir test başarısız işlemi gerçekleştirilebilir.

```azurepowershell
#Create a separate network for test failover (not connected to my DR network)
$TFOVnet = New-AzVirtualNetwork -Name "a2aTFOvnet" -ResourceGroupName "a2ademorecoveryrg" -Location 'West US 2' -AddressPrefix "10.3.0.0/16"

Add-AzVirtualNetworkSubnetConfig -Name "default" -VirtualNetwork $TFOVnet -AddressPrefix "10.3.0.0/20" | Set-AzVirtualNetwork

$TFONetwork= $TFOVnet.Id
```

Bir test başarısız yapın.

```azurepowershell
$ReplicationProtectedItem = Get-AzRecoveryServicesAsrReplicationProtectedItem -FriendlyName "AzureDemoVM" -ProtectionContainer $PrimaryProtContainer

$TFOJob = Start-AzRecoveryServicesAsrTestFailoverJob -ReplicationProtectedItem $ReplicationProtectedItem -AzureVMNetworkId $TFONetwork -Direction PrimaryToRecovery
```

Test başarısız işlemi tamamlamak için bekleyin.

```azurepowershell
Get-AzRecoveryServicesAsrJob -Job $TFOJob
```

```Output
Name             : 3dcb043e-3c6d-4e0e-a42e-8d4245668547
ID               : /Subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/a2ademorecoveryrg/providers/Microsoft.RecoveryServices/vaults/a2aDemoR
                   ecoveryVault/replicationJobs/3dcb043e-3c6d-4e0e-a42e-8d4245668547
Type             : Microsoft.RecoveryServices/vaults/replicationJobs
JobType          : TestFailover
DisplayName      : Test failover
ClientRequestId  : 1ef8515b-b130-4452-a44d-91aaf071931c ActivityId: 907bb2bc-ebe6-4732-8b66-77d0546eaba8
State            : Succeeded
StateDescription : Completed
StartTime        : 4/25/2018 4:29:43 AM
EndTime          : 4/25/2018 4:33:06 AM
TargetObjectId   : ce86206c-bd78-53b4-b004-39b722c1ac3a
TargetObjectType : ProtectionEntity
TargetObjectName : azuredemovm
AllowedActions   :
Tasks            : {Prerequisites check for test failover, Create test virtual machine, Preparing the virtual machine, Start the virtual machine}
Errors           : {}
```

Test başarısız işi başarıyla tamamlandıktan sonra, sanal makine üzerinden başarısız olan teste bağlanabilir ve test başarısızolmasını doğrulayabilirsiniz.

Sanal makine üzerinde test başarısız test tamamlandıktan sonra, temizleme testi failover işlemi başlatarak test kopyasını temizleyin. Bu işlem, test başarısızlığı tarafından oluşturulan sanal makinenin test kopyasını siler.

```azurepowershell
$Job_TFOCleanup = Start-AzRecoveryServicesAsrTestFailoverCleanupJob -ReplicationProtectedItem $ReplicationProtectedItem

Get-AzRecoveryServicesAsrJob -Job $Job_TFOCleanup | Select State
```

```Output
State
-----
Succeeded
```

## <a name="fail-over-to-azure"></a>Azure'a yük devretme

Belirli bir kurtarma noktasına sanal makine üzerinde başarısız.

```azurepowershell
$RecoveryPoints = Get-AzRecoveryServicesAsrRecoveryPoint -ReplicationProtectedItem $ReplicationProtectedItem

#The list of recovery points returned may not be sorted chronologically and will need to be sorted first, in order to be able to find the oldest or the latest recovery points for the virtual machine.
"{0} {1}" -f $RecoveryPoints[0].RecoveryPointType, $RecoveryPoints[-1].RecoveryPointTime
```

```Output
CrashConsistent 4/24/2018 11:10:25 PM
```

```azurepowershell
#Start the fail over job
$Job_Failover = Start-AzRecoveryServicesAsrUnplannedFailoverJob -ReplicationProtectedItem $ReplicationProtectedItem -Direction PrimaryToRecovery -RecoveryPoint $RecoveryPoints[-1]

do {
        $Job_Failover = Get-AzRecoveryServicesAsrJob -Job $Job_Failover;
        sleep 30;
} while (($Job_Failover.State -eq "InProgress") -or ($JobFailover.State -eq "NotStarted"))

$Job_Failover.State
```

```Output
Succeeded
```

Başarısız iş başarılı olduğunda, başarısız işlemi gerçekleştirebilirsiniz.

```azurepowershell
$CommitFailoverJOb = Start-AzRecoveryServicesAsrCommitFailoverJob -ReplicationProtectedItem $ReplicationProtectedItem

Get-AzRecoveryServicesAsrJob -Job $CommitFailoverJOb
```

```Output
Name             : 58afc2b7-5cfe-4da9-83b2-6df358c6e4ff
ID               : /Subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/a2ademorecoveryrg/providers/Microsoft.RecoveryServices/vaults/a2aDemoR
                   ecoveryVault/replicationJobs/58afc2b7-5cfe-4da9-83b2-6df358c6e4ff
Type             : Microsoft.RecoveryServices/vaults/replicationJobs
JobType          : CommitFailover
DisplayName      : Commit
ClientRequestId  : 10a95d6c-359e-4603-b7d9-b7ee3317ce94 ActivityId: 8751ada4-fc42-4238-8de6-a82618408fcf
State            : Succeeded
StateDescription : Completed
StartTime        : 4/25/2018 4:50:58 AM
EndTime          : 4/25/2018 4:51:01 AM
TargetObjectId   : ce86206c-bd78-53b4-b004-39b722c1ac3a
TargetObjectType : ProtectionEntity
TargetObjectName : azuredemovm
AllowedActions   :
Tasks            : {Prerequisite check, Commit}
Errors           : {}
```

## <a name="reprotect-and-fail-back-to-the-source-region"></a>Kaynak bölgeye yeniden koruma ve başarısız

Bir başarısızlıktan sonra, özgün bölgeye geri dönmeye hazır olduğunuzda, `Update-AzRecoveryServicesAsrProtectionDirection` cmdlet'i kullanarak çoğaltma korumalı öğe için ters çoğaltma başlatın.

```azurepowershell
#Create Cache storage account for replication logs in the primary region
$WestUSCacheStorageAccount = New-AzStorageAccount -Name "a2acachestoragewestus" -ResourceGroupName "A2AdemoRG" -Location 'West US' -SkuName Standard_LRS -Kind Storage
```

```azurepowershell
#Use the recovery protection container, new cache storage account in West US and the source region VM resource group
Update-AzRecoveryServicesAsrProtectionDirection -ReplicationProtectedItem $ReplicationProtectedItem -AzureToAzure
-ProtectionContainerMapping $WusToEusPCMapping -LogStorageAccountId $WestUSCacheStorageAccount.Id -RecoveryResourceGroupID $sourceVMResourcegroup.ResourceId
```

Yeniden koruma tamamlandıktan sonra, ters yönde, Batı ABD'den Doğu ABD'ye doğru başarısız olabilir ve kaynak bölgeye geri dönebilirsiniz.

## <a name="disable-replication"></a>Çoğaltmayı devre dışı bırakma

`Remove-AzRecoveryServicesAsrReplicationProtectedItem` Cmdlet ile çoğaltma devre dışı bırakabilirsiniz.

```azurepowershell
Remove-AzRecoveryServicesAsrReplicationProtectedItem -ReplicationProtectedItem $ReplicatedItem
```

## <a name="next-steps"></a>Sonraki adımlar

PowerShell ile kurtarma planları oluşturma ve kurtarma planlarının başarısız olmasını test etme gibi diğer görevleri nasıl yapabileceğinizi öğrenmek için [Azure Site Kurtarma PowerShell başvurusunu](/powershell/module/az.RecoveryServices) görüntüleyin.
