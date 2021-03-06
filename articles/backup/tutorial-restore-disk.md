---
title: Öğretici - Azure Yedekleme ile VM diski geri yükleme
description: Yedekleme ve Kurtarma Hizmetleri ile Azure’da bir diskin nasıl geri yükleneceğini ve kurtarılan bir sanal makinenin nasıl oluşturulacağını öğrenin.
ms.topic: tutorial
ms.date: 01/31/2019
ms.custom: mvc
ms.openlocfilehash: 8a66cee7e844f0049f2d2ca2f6841943aa267f3e
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/24/2020
ms.locfileid: "79238737"
---
# <a name="restore-a-disk-and-create-a-recovered-vm-in-azure"></a>Azure’da bir diski geri yükleme ve kurtarılan bir VM oluşturma

Azure Backup, coğrafi olarak yedekli kurtarma kasalarında depolanan kurtarma noktaları oluşturur. Bir kurtarma noktasından geri yüklediğinizde, tüm sanal makineyi veya tek tek dosyaları geri yükleyebilirsiniz. Bu makalede, CLI kullanarak tam bir sanal makinenin nasıl geri yükleneceği açıklanmaktadır. Bu öğreticide şunların nasıl yapıldığını öğrenirsiniz:

> [!div class="checklist"]
>
> * Kurtarma noktalarını listeleme ve seçme
> * Bir kurtarma noktasından diski geri yükleme
> * Geri yüklenen diskten sanal makine oluşturma

Disk geri yüklemek ve kurtarılmış bir VM oluşturmak üzere PowerShell kullanmayla ilgili bilgi edinmek için bkz. [PowerShell ile Azure VM’lerini yedekleme ve geri yükleme](backup-azure-vms-automation.md#restore-an-azure-vm).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

CLI'yi yerel olarak yükleyip kullanmayı tercih ederseniz bu öğretici için Azure CLI 2.0.18 veya sonraki bir sürümünü kullanmanız gerekir. Sürümü bulmak için `az --version` komutunu çalıştırın. Yükleme veya yükseltme yapmanız gerekirse bkz. [Azure CLI’yı yükleme]( /cli/azure/install-azure-cli).

## <a name="prerequisites"></a>Ön koşullar

Bu öğretici için Azure Backup ile korunmuş olan bir Linux sanal makinesi gerekir. Yanlışlıkla bir sanal makineyi silme ve kurtarma işleminin benzetimini yapmak için, bir kurtarma noktasındaki diskten bir sanal makine oluşturursunuz. Azure Backup ile korunan bir Linux sanal makinesine ihtiyacınız varsa bkz. [CLI ile Azure’da bir sanal makineyi yedekleme](quick-backup-vm-cli.md).

## <a name="backup-overview"></a>Backup’a genel bakış

Azure bir yedekleme başlattığında sanal makinedeki yedekleme uzantısı, belirli bir noktanın anlık görüntüsünü alır. İlk yedekleme istendiğinde sanal makineye yedekleme uzantısı yüklenir. Azure Backup, yedekleme gerçekleştiğinde sanal makine çalışmıyorsa temel depolamanın anlık görüntüsünü de alabilir.

Varsayılan olarak Azure Backup, bir dosya sisteminin tutarlı yedeklemesini alır. Azure Backup, anlık görüntüyü aldığında veriler Kurtarma Hizmetleri kasasına aktarılır. Verimliliği en üst düzeye çıkarmak için Azure Backup yalnızca önceki yedeklemeden itibaren değişmiş olan veri bloklarını belirler ve aktarır.

Veri aktarımı tamamlandığında, anlık görüntü kaldırılır ve bir kurtarma noktası oluşturulur.

## <a name="list-available-recovery-points"></a>Kullanılabilir kurtarma noktalarını listeleme

Bir diski geri yüklemek için, kurtarma verileri kaynağı olarak bir kurtarma noktası seçersiniz. Varsayılan ilke her gün bir kurtarma noktası oluşturup 30 gün boyunca bunları beklettiğinden, kurtarma için belirli bir nokta seçmenize olanak sağlayan bir kurtarma noktaları kümesini tutabilirsiniz.

Kullanılabilir kurtarma noktalarının listesini görmek için [az backup recoverypoint list](https://docs.microsoft.com/cli/azure/backup/recoverypoint?view=azure-cli-latest#az-backup-recoverypoint-list) komutunu kullanın. Diskleri kurtarmak için kurtarma noktası **adı** kullanılır. Bu öğreticide, kullanılabilir en son kurtarma noktasını istiyoruz. `--query [0].name` parametresi aşağıdaki şekilde en son kurtarma noktası adını seçer:

```azurecli-interactive
az backup recoverypoint list \
    --resource-group myResourceGroup \
    --vault-name myRecoveryServicesVault \
    --backup-management-type AzureIaasVM
    --container-name myVM \
    --item-name myVM \
    --query [0].name \
    --output tsv
```

## <a name="restore-a-vm-disk"></a>Sanal makine diskini geri yükleme

> [!IMPORTANT]
> Çok kuvvetle az CLI sürüm 2.0.74 veya daha sonra yönetilen disk geri yükleme de dahil olmak üzere hızlı bir geri yükleme tüm yararları elde etmek için kullanılması tavsiye edilir. Kullanıcı her zaman en son sürümü kullanırsa en iyisidir.

### <a name="managed-disk-restore"></a>Yönetilen disk geri yükleme

Yedeklenen VM diskleri yönetmişse ve amaç yönetilen diskleri kurtarma noktasından geri yüklemekse, önce bir Azure depolama hesabı sağlarsınız. Bu depolama hesabı, VM yapılandırmasını ve daha sonra geri yüklenen disklerden VM'yi dağıtmak için kullanılabilecek dağıtım şablonlarını depolamak için kullanılır. Ardından, yönetilen disklerin geri yüklenecek bir hedef kaynak grubu da sağlarsınız.

1. Depolama hesabı oluşturmak için [az storage account create](https://docs.microsoft.com/cli/azure/storage/account?view=azure-cli-latest#az-storage-account-create) komutunu kullanın. Depolama hesabı adı tamamen küçük harflerden oluşmalı ve genel olarak benzersiz olmalıdır. *mystorageaccount* değerini kendi benzersiz adınızla değiştirin:

    ```azurecli-interactive
    az storage account create \
        --resource-group myResourceGroup \
        --name mystorageaccount \
        --sku Standard_LRS
    ```

2. [az backup restore restore-disks](https://docs.microsoft.com/cli/azure/backup/restore?view=azure-cli-latest#az-backup-restore-restore-disks) komutuyla kurtarma noktanızdan diski geri yükleyin. *mystorageaccount* değerini, önceki komutta oluşturduğunuz depolama hesabının adıyla değiştirin. *MyRecoveryPointName'i* önceki [az yedekleme kurtarma noktası listesinden](https://docs.microsoft.com/cli/azure/backup/recoverypoint?view=azure-cli-latest#az-backup-recoverypoint-list) çıktıda elde ettiğiniz kurtarma noktası adı ile değiştirin. ***Ayrıca, yönetilen disklerin geri yüklendiği hedef kaynak grubunu da sağlayın.***

    ```azurecli-interactive
    az backup restore restore-disks \
        --resource-group myResourceGroup \
        --vault-name myRecoveryServicesVault \
        --container-name myVM \
        --item-name myVM \
        --storage-account mystorageaccount \
        --rp-name myRecoveryPointName
        --target-resource-group targetRG
    ```

> [!WARNING]
> Hedef kaynak grubu sağlanmazsa, yönetilen diskler verilen depolama hesabına yönetilmeyen diskler olarak geri yüklenir. Bu, diskleri geri yüklemek için geçen süre tamamen verilen depolama hesabına bağlı olduğundan geri yükleme süresi için önemli sonuçlar doğuracaktır.

### <a name="unmanaged-disks-restore"></a>Yönetilmeyen diskler geri yükleme

Yedeklenen VM'de yönetilmeyen diskler varsa ve amaç kurtarma noktasından diskleri geri yüklemekse, önce bir Azure depolama hesabı sağlarsınız. Bu depolama hesabı, VM yapılandırmasını ve daha sonra geri yüklenen disklerden VM'yi dağıtmak için kullanılabilecek dağıtım şablonlarını depolamak için kullanılır. Varsayılan olarak, yönetilmeyen diskler özgün depolama hesaplarına geri yüklenir. Kullanıcı tüm yönetilmeyen diskleri tek bir yere geri yüklemek isterse, verilen depolama hesabı da bu diskler için bir hazırlama konumu olarak kullanılabilir.

Ek adımlarda, sanal makine oluşturmak için geri yüklenen disk kullanılır.

1. Depolama hesabı oluşturmak için [az storage account create](https://docs.microsoft.com/cli/azure/storage/account?view=azure-cli-latest#az-storage-account-create) komutunu kullanın. Depolama hesabı adı tamamen küçük harflerden oluşmalı ve genel olarak benzersiz olmalıdır. *mystorageaccount* değerini kendi benzersiz adınızla değiştirin:

    ```azurecli-interactive
    az storage account create \
        --resource-group myResourceGroup \
        --name mystorageaccount \
        --sku Standard_LRS
    ```

2. [az backup restore restore-disks](https://docs.microsoft.com/cli/azure/backup/restore?view=azure-cli-latest#az-backup-restore-restore-disks) komutuyla kurtarma noktanızdan diski geri yükleyin. *mystorageaccount* değerini, önceki komutta oluşturduğunuz depolama hesabının adıyla değiştirin. *myRecoveryPointName* değerini, önceki [az backup recoverypoint list](https://docs.microsoft.com/cli/azure/backup/recoverypoint?view=azure-cli-latest#az-backup-recoverypoint-list) komutunun çıktısından elde ettiğiniz kurtarma noktası adıyla değiştirin:

    ```azurecli-interactive
    az backup restore restore-disks \
        --resource-group myResourceGroup \
        --vault-name myRecoveryServicesVault \
        --container-name myVM \
        --item-name myVM \
        --storage-account mystorageaccount \
        --rp-name myRecoveryPointName
    ```

Yukarıda belirtildiği gibi, yönetilmeyen diskler özgün depolama hesabına geri yüklenir. Bu en iyi geri yükleme performansı sağlar. Ancak, tüm yönetilmeyen disklerin verilen depolama hesabına geri yüklenmeleri gerekiyorsa, aşağıda gösterildiği gibi ilgili bayrağı kullanın.

```azurecli-interactive
    az backup restore restore-disks \
        --resource-group myResourceGroup \
        --vault-name myRecoveryServicesVault \
        --container-name myVM \
        --item-name myVM \
        --storage-account mystorageaccount \
        --rp-name myRecoveryPointName
        --restore-to-staging-storage-account
    ```

## Monitor the restore job

To monitor the status of restore job, use [az backup job list](https://docs.microsoft.com/cli/azure/backup/job?view=azure-cli-latest#az-backup-job-list):

```azurecli-interactive
az backup job list \
    --resource-group myResourceGroup \
    --vault-name myRecoveryServicesVault \
    --output table
```

Çıktı, geri yükleme işinin *İlerliyor* durumunda olduğunu gösteren aşağıdaki örneğe benzer olacaktır:

```output
Name      Operation        Status      Item Name    Start Time UTC       Duration
--------  ---------------  ----------  -----------  -------------------  --------------
7f2ad916  Restore          InProgress  myvm         2017-09-19T19:39:52  0:00:34.520850
a0a8e5e6  Backup           Completed   myvm         2017-09-19T03:09:21  0:15:26.155212
fe5d0414  ConfigureBackup  Completed   myvm         2017-09-19T03:03:57  0:00:31.191807
```

Geri yükleme iş raporlarının *Durumu* *Tamamlandığında,* gerekli bilgiler (VM yapılandırması ve dağıtım şablonu) depolama hesabına geri yüklenir.

## <a name="create-a-vm-from-the-restored-disk"></a>Geri yüklenen diskten sanal makine oluşturma

Son adım, geri yüklenen disklerden bir VM oluşturmaktır. VM'yi oluşturmak için verilen depolama hesabına indirilen dağıtım şablonundan yararlanabilirsiniz.

### <a name="fetch-the-job-details"></a>İş ayrıntılarını getir

Ortaya çıkan iş ayrıntıları, sorgulanabilen ve dağıtılabilen URI şablonuna verir. Tetiklenen geri yüklenen iş için daha fazla ayrıntı almak için iş gösterisi komutunu kullanın.

```azurecli-interactive
az backup job show \
    -v myRecoveryServicesVault \
    -g myResourceGroup \
    -n 1fc2d55d-f0dc-4ca6-ad48-aca0fe5d0414
```

Bu sorgunun çıktısı tüm ayrıntıları verecektir, ancak biz sadece depolama hesabı içeriği ile ilgileniyoruz. Azure CLI'nin [sorgu yeteneğini](https://docs.microsoft.com/cli/azure/query-azure-cli?view=azure-cli-latest) kullanarak ilgili ayrıntıları getirebiliriz

```azurecli-interactive
az backup job show \
    -v myRecoveryServicesVault \
    -g myResourceGroup \
    -n 1fc2d55d-f0dc-4ca6-ad48-aca0fe5d0414 \
    --query properties.extendedInfo.propertyBag

{
  "Config Blob Container Name": "myVM-daa1931199fd4a22ae601f46d8812276",
  "Config Blob Name": "config-myVM-1fc2d55d-f0dc-4ca6-ad48-aca0fe5d0414.json",
  "Config Blob Uri": "https://mystorageaccount.blob.core.windows.net/myVM-daa1931199fd4a22ae601f46d8812276/config-appvm8-1fc2d55d-f0dc-4ca6-ad48-aca0519c0232.json",
  "Job Type": "Recover disks",
  "Recovery point time ": "12/25/2019 10:07:11 PM",
  "Target Storage Account Name": "mystorageaccount",
  "Target resource group": "mystorageaccountRG",
  "Template Blob Uri": "https://mystorageaccount.blob.core.windows.net/myVM-daa1931199fd4a22ae601f46d8812276/azuredeploy1fc2d55d-f0dc-4ca6-ad48-aca0519c0232.json"
}
```

### <a name="fetch-the-deployment-template"></a>Dağıtım şablonuna getir

Müşterinin depolama hesabı ve verilen kapsayıcı altında olduğundan şablona doğrudan erişilemez. Bu şablona erişmek için tam URL'ye (geçici bir SAS belirteciyle birlikte) ihtiyacımız vardır.

İlk olarak, iş ayrıntılarından şablon blob Uri ayıklayın

```azurecli-interactive
az backup job show \
    -v myRecoveryServicesVault \
    -g myResourceGroup \
    -n 1fc2d55d-f0dc-4ca6-ad48-aca0fe5d0414 \
    --query properties.extendedInfo.propertyBag."""Template Blob Uri"""

"https://mystorageaccount.blob.core.windows.net/myVM-daa1931199fd4a22ae601f46d8812276/azuredeploy1fc2d55d-f0dc-4ca6-ad48-aca0519c0232.json"
```

Şablon blob Uri bu biçimde olacak ve şablon adı ayıklamak

```https
https://<storageAccountName.blob.core.windows.net>/<containerName>/<templateName>
```

Yani, yukarıdaki örnekteki şablon adı ```azuredeploy1fc2d55d-f0dc-4ca6-ad48-aca0519c0232.json``` olacak ve kapsayıcı adı```myVM-daa1931199fd4a22ae601f46d8812276```

Şimdi [burada](https://docs.microsoft.com/azure/azure-resource-manager/templates/secure-template-with-sas-token?tabs=azure-cli#provide-sas-token-during-deployment) ayrıntılı olarak bu kapsayıcı ve şablon için SAS belirteci alın

```azurecli-interactive
expiretime=$(date -u -d '30 minutes' +%Y-%m-%dT%H:%MZ)
connection=$(az storage account show-connection-string \
    --resource-group mystorageaccountRG \
    --name mystorageaccount \
    --query connectionString)
token=$(az storage blob generate-sas \
    --container-name myVM-daa1931199fd4a22ae601f46d8812276 \
    --name azuredeploy1fc2d55d-f0dc-4ca6-ad48-aca0519c0232.json \
    --expiry $expiretime \
    --permissions r \
    --output tsv \
    --connection-string $connection)
url=$(az storage blob url \
   --container-name myVM-daa1931199fd4a22ae601f46d8812276 \
    --name azuredeploy1fc2d55d-f0dc-4ca6-ad48-aca0519c0232.json \
    --output tsv \
    --connection-string $connection)
```

### <a name="deploy-the-template-to-create-the-vm"></a>VM'yi oluşturmak için şablonu dağıtma

Şimdi [burada](https://docs.microsoft.com/azure/azure-resource-manager/templates/deploy-cli)açıklandığı gibi VM oluşturmak için şablonu dağıtın.

```azurecli-interactive
az group deployment create \
  --resource-group ExampleGroup \
  --template-uri $url?$token
```

Kurtarılan diskinizden sanal makinenizin oluşturulduğunu onaylamak için [az vm list](/cli/azure/vm?view=azure-cli-latest#az-vm-list) komutuyla aşağıdaki şekilde kaynak grubunuzdaki sanal makineleri listeleyin:

```azurecli-interactive
az vm list --resource-group myResourceGroup --output table
```

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide, bir kurtarma noktasından bir diski geri yüklediniz ve sonra diskten bir sanal makine oluşturdunuz. Şunları öğrendiniz:

> [!div class="checklist"]
>
> * Kurtarma noktalarını listeleme ve seçme
> * Bir kurtarma noktasından diski geri yükleme
> * Geri yüklenen diskten sanal makine oluşturma

Bir kurtarma noktasından tek tek dosyaları geri yükleme hakkında bilgi edinmek için sonraki öğreticiye ilerleyin.

> [!div class="nextstepaction"]
> [Azure’da dosyaları sanal makineye geri yükleme](tutorial-restore-files.md)
