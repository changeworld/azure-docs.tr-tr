---
title: Azure CLI kullanarak Azure dosya paylaşımlarını yönetme için hızlı başlangıç
description: Bu hızlı başlangıcı Azure dosyalarını yönetmek için Azure CLI kullanma hakkında bilgi edinmek için kullanın.
author: roygara
ms.service: storage
ms.topic: quickstart
ms.date: 10/26/2018
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 95d7abca27ec9db46a72140bc8a61b2841c63fcb
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/26/2020
ms.locfileid: "77598604"
---
# <a name="quickstart-create-and-manage-azure-file-shares-using-azure-cli"></a>Hızlı Başlangıç: Azure CLI kullanarak Azure dosya paylaşımları oluşturma ve yönetme
Bu kılavuzda, Azure CLI kullanarak [Azure dosya paylaşımları](storage-files-introduction.md) ile çalışmanın temel kuralları gösterilmektedir. Azure dosya paylaşımları diğer dosya paylaşımları gibidir, ancak bulutta depolanır ve Azure platformu tarafından desteklenir. Azure dosya paylaşımları endüstri standardı SMB protokolünü destekler ve birden çok makine, uygulama ve örnek arasında dosya paylaşmayı olanaklı kılar. 

Azure aboneliğiniz yoksa, başlamadan önce [ücretsiz](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) bir hesap oluşturun.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Azure CLI’yı yerel olarak yükleyip kullanmaya karar verirseniz, bu makaledeki adımlar için Azure CLI 2.0.4 veya sonraki bir sürümü çalıştırıyor olmanız gerekir. Azure CLI sürümünüzü bulmak için **az --version** komutunu çalıştırın. Yüklemeniz veya yükseltmeniz gerekirse, bkz. [Azure CLI 2.0 yükleme](/cli/azure/install-azure-cli). 

Varsayılan olarak, Azure CLI komutları JavaScript Nesne Gösterimi (JSON) döndürür. JSON, REST API'lerinden ileti gönderip almanın standart yoludur. JSON yanıtlarıyla çalışmayı kolaylaştırmak için, bu kılavuzdaki bazı örneklerde Azure CLI komutları üzerinde *query* parametresi kullanılır. Bu parametre, JSON ayrıştırmak için [JMESPath sorgu dilini](http://jmespath.org/) kullanır. JMESPath sorgu dilini takip ederek Azure CLI komutlarının sonuçlarını kullanma hakkında daha fazla bilgi almak için bkz. [JMESPath öğreticisi](http://jmespath.org/tutorial.html).

## <a name="create-a-resource-group"></a>Kaynak grubu oluşturma
Kaynak grubu, Azure kaynaklarının dağıtıldığı ve yönetildiği bir mantıksal kapsayıcıdır. Henüz bir Azure kaynak grubunuz yoksa, [az group create](/cli/azure/group) komutunu kullanarak bir tane oluşturabilirsiniz. 

Aşağıdaki örnek, *Batı ABD 2* konumunda *myResourceGroup* adında bir kaynak grubu oluşturur:

```azurecli-interactive 
export resourceGroupName="myResourceGroup"
region="westus2"

az group create \
    --name $resourceGroupName \
    --location $region \
    --output none
```

## <a name="create-a-storage-account"></a>Depolama hesabı oluşturma
Depolama hesabı, Azure dosya paylaşımlarını veya bloblar veya sorgular gibi diğer depolama kaynaklarını dağıtabileceğiniz, paylaşılan bir depolama havuzudur. Bir depolama hesabında sınırsız sayıda dosya paylaşımı olabilir. Bir paylaşım, depolama hesabının kapasite limitlerine kadar sınırsız sayıda dosyayı depolayabilir.

Aşağıdaki örnek, [az depolama hesabı oluşturma](/cli/azure/storage/account) komutunu kullanarak bir depolama hesabı oluşturur. Depolama hesabı adları benzersiz olmalıdır; bu nedenle `$RANDOM` kullanarak adın sonuna bir sayı ekleyip adı benzersiz hale getirin.

```azurecli-interactive 
export storageAccountName="mystorageacct$RANDOM"

az storage account create \
    --resource-group $resourceGroupName \
    --name $storageAccountName \
    --location $region \
    --kind StorageV2 \
    --sku Standard_LRS \
    --enable-large-file-share \
    --output none
```

> [!Note]  
> 5 TiB'den büyük hisseler (hisse başına en fazla 100 TiB) yalnızca yerel yedekli (LRS) ve bölge yedekli (ZRS) depolama hesaplarında kullanılabilir. Coğrafi yedekli (GRS) veya coğrafi bölge yedekli (GZRS) depolama hesabı oluşturmak için `--enable-large-file-share` parametreyi kaldırın.

### <a name="get-the-storage-account-key"></a>Depolama hesabı anahtarını alma
Depolama hesabı anahtarları, depolama hesabındaki kaynaklara erişimi denetler. Bu anahtarlar, depolama hesabını oluşturduğunuzda otomatik olarak oluşturulur. [az storage account keys list](/cli/azure/storage/account/keys) komutunu kullanarak depolama hesabınızın depolama hesabı anahtarlarını alabilirsiniz: 

```azurecli-interactive 
export storageAccountKey=$(az storage account keys list \
    --resource-group $resourceGroupName \
    --account-name $storageAccountName \
    --query "[0].value" | tr -d '"')
```

## <a name="create-an-azure-file-share"></a>Azure dosya paylaşımı oluşturma
Artık ilk Azure dosya paylaşımınızı oluşturabilirsiniz. Dosya paylaşımlarını oluşturmak için [az storage share create](/cli/azure/storage/share) komutunu kullanın. Bu örnekte *myshare* adlı bir Azure dosya paylaşımı oluşturulur: 

```azurecli-interactive
shareName="myshare"

az storage share create \
    --account-name $storageAccountName \
    --account-key $storageAccountKey \
    --name $shareName \
    --quota 1024 \
    --output none
```

Paylaşım adları yalnızca küçük harf, sayı ve tek kısa çizgi içerebilir (ancak kısa çizgiyle başlayamaz). Dosya paylaşımlarının ve dosyaların adlandırılması hakkında tüm ayrıntılara ulaşmak için bkz. [Paylaşım, dizin, dosya ve meta verileri adlandırma ve bunlara başvuruda bulunma](https://docs.microsoft.com/rest/api/storageservices/Naming-and-Referencing-Shares--Directories--Files--and-Metadata).

## <a name="use-your-azure-file-share"></a>Azure dosya paylaşımınızı kullanma
Azure Dosyaları, Azure dosya paylaşımınızdaki dosya ve klasörler ile çalışmak için iki yöntem sunar: sektör standardı [Sunucu İleti Bloğu (SMB) protokolü](https://msdn.microsoft.com/library/windows/desktop/aa365233.aspx) ve [Dosya REST protokolü](https://docs.microsoft.com/rest/api/storageservices/file-service-rest-api). 

Bir dosya paylaşımını SMB ile bağlayabilmeniz için işletim sisteminize göre aşağıdaki belgeye bakın:
- [Linux](storage-how-to-use-files-linux.md)
- [Macos](storage-how-to-use-files-mac.md)
- [Windows](storage-how-to-use-files-windows.md)

### <a name="using-an-azure-file-share-with-the-file-rest-protocol"></a>Dosya REST protokolü ile bir Azure dosya paylaşımını kullanma 
Doğrudan Dosya REST protokolü ile doğrudan çalışmak mümkündür (el işçiliği REST HTTP kendiniz çağırır), ancak Dosya REST protokolünü kullanmanın en yaygın yolu Azure CLI' yi, [Azure PowerShell modülünü](storage-how-to-use-files-powershell.md)veya Azure Depolama SDK'sını kullanmaktır ve bunların hepsi seçtiğiniz komut dosyası/programlama dilinde Dosya REST protokolünün etrafında güzel bir sarmalayıcı sağlar.  

Kullanabilmeyi umdukları mevcut uygulama ve araçlarını kullanmalarına izin vereceği için Azure Dosyaları kullanıcılarının çoğunluğunun Azure dosya paylaşımları ile SMP protokolü üzerinden çalışmasını bekliyoruz, ancak SMB yerine Dosya REST API'si kullanmanın aşağıdaki gibi bazı avantajları bulunmaktadır:

- Dosya paylaşımınıza (SMB üzerinden dosya paylaşımı bağlayamayan) Azure Bash Cloud Shell'den göz atıyorsanız.
- [Azure İşlevleri](../../azure-functions/functions-overview.md) gibi sunucusuz kaynaklardan yararlanıyorsanız. 
- Yedekleme veya virüsten koruma taraması yapmak gibi birçok Azure dosya paylaşımıyla etkileşimedebilen bir değer katma hizmeti oluşturuyorsunuz.

Aşağıdaki örnekler, Azure dosya paylaşımınızı Dosya REST protokolüyle işlemek için Azure CLI'nin nasıl kullanılacağını gösterir. 

### <a name="create-a-directory"></a>Dizin oluşturma
Azure dosya paylaşımınızın kökünde *myDirectory* adında yeni bir dizin oluşturmak için aşağıdaki komutu [`az storage directory create`](/cli/azure/storage/directory) kullanın:

```azurecli-interactive
az storage directory create \
   --account-name $storageAccountName \
   --account-key $storageAccountKey \
   --share-name $shareName \
   --name "myDirectory" \
   --output none
```

### <a name="upload-a-file"></a>Dosyayı karşıya yükleme
Komutu kullanarak bir dosyanın [`az storage file upload`](/cli/azure/storage/file) nasıl yüklendiğini göstermek için, önce Cloud Shell scratch sürücüsüne yüklenmesi gereken bir dosya oluşturun. Aşağıdaki örnekte dosyayı oluşturup karşıya yüklersiniz:

```azurecli-interactive
cd ~/clouddrive/
date > SampleUpload.txt

az storage file upload \
    --account-name $storageAccountName \
    --account-key $storageAccountKey \
    --share-name $shareName \
    --source "SampleUpload.txt" \
    --path "myDirectory/SampleUpload.txt"
```

Azure CLI'yi yerel olarak çalıştırıyorsanız, `~/clouddrive` değerini makinenizde var olan bir yolla değiştirin.

Dosyayı yükledikten sonra, dosyanın [`az storage file list`](/cli/azure/storage/file) Azure dosya paylaşımınıza yüklendiğinden emin olmak için komutu kullanabilirsiniz:

```azurecli-interactive
az storage file list \
    --account-name $storageAccountName \
    --account-key $storageAccountKey \
    --share-name $shareName \
    --path "myDirectory" \
    --output table
```

### <a name="download-a-file"></a>Dosya indirme
Bulut Kabuğu [`az storage file download`](/cli/azure/storage/file) karalama sürücüsüne yüklediğiniz dosyanın bir kopyasını indirmek için komutu kullanabilirsiniz:

```azurecli-interactive
# Delete an existing file by the same name as SampleDownload.txt, if it exists, because you've run this example before
rm -f SampleDownload.txt

az storage file download \
    --account-name $storageAccountName \
    --account-key $storageAccountKey \
    --share-name $shareName \
    --path "myDirectory/SampleUpload.txt" \
    --dest "SampleDownload.txt" \
    --output none
```

### <a name="copy-files"></a>Dosyaları kopyalama
Yaygın bir görev, dosyaları bir dosya paylaşımından başka bir dosya paylaşımına kopyalamaktır. Bu işlevselliği göstermek için yeni bir paylaşım oluşturun. Bu yeni paylaşıma yüklediğiniz dosyayı [az storage file copy](/cli/azure/storage/file/copy) komutunu kullanarak kopyalayın: 

```azurecli-interactive
otherShareName="myshare2"

az storage share create \
    --account-name $storageAccountName \
    --account-key $storageAccountKey \
    --name $otherShareName \
    --quota 1024 \
    --output none

az storage directory create \
    --account-name $storageAccountName \
    --account-key $storageAccountKey \
    --share-name $otherShareName \
    --name "myDirectory2" \
    --output none

az storage file copy start \
    --account-name $storageAccountName \
    --account-key $storageAccountKey \
    --source-share $shareName \
    --source-path "myDirectory/SampleUpload.txt" \
    --destination-share $otherShareName \
    --destination-path "myDirectory2/SampleCopy.txt"
```

Şimdi, yeni paylaşımdaki dosyaları listelerseniz kopyalanan dosyanızı görmeniz gerekir:

```azurecli-interactive
az storage file list \
    --account-name $storageAccountName \
    --account-key $storageAccountKey \
    --share-name $otherShareName \
    --path "myDirectory2" \
    --output table
```

Komut, `az storage file copy start` Azure dosya paylaşımları arasında, geçişler ve daha büyük veri `rsync` hareketleri için dosya `robocopy` hareketleri için kullanışlı olsa da, macOS ve Linux ve Windows'da öneririz. `rsync`ve `robocopy` FileREST API yerine veri hareketlerini gerçekleştirmek için SMB'yi kullanın.

## <a name="create-and-manage-share-snapshots"></a>Paylaşım anlık görüntülerini oluşturma ve yönetme
Azure dosya paylaşımıyla yerine getirebileceğiniz bir diğer yararlı görev ise paylaşım anlık görüntüleri oluşturmaktır. Anlık görüntü, Azure dosya paylaşımının zamanın bir noktasındaki kopyasını saklar. Paylaşım anlık görüntüleri, zaten tanıyor olabileceğiniz bazı işletim sistemi teknolojilerine benzerdir:

- Linux sistemleri için [Mantıksal Birim Yöneticisi (LVM)](https://en.wikipedia.org/wiki/Logical_Volume_Manager_(Linux)#Basic_functionality) anlık görüntüleri.
- macOS için [Apple Dosya Sistemi (APFS)](https://developer.apple.com/library/content/documentation/FileManagement/Conceptual/APFS_Guide/Features/Features.html) anlık görüntüleri.
- NTFS ve ReFS gibi Windows dosya sistemleri için [Birim Gölge Kopyalama Hizmeti (VSS).](https://docs.microsoft.com/windows/desktop/VSS/volume-shadow-copy-service-portal)
 
[`az storage share snapshot`](/cli/azure/storage/share) Komutu kullanarak bir paylaşım anlık görüntüsü oluşturabilirsiniz:

```azurecli-interactive
snapshot=$(az storage share snapshot \
    --account-name $storageAccountName \
    --account-key $storageAccountKey \
    --name $shareName \
    --query "snapshot" | tr -d '"')
```

### <a name="browse-share-snapshot-contents"></a>Paylaşım anlık görüntüsünün içeriğine göz atma
`$snapshot` değişkeninde yakaladığınız paylaşım anlık görüntüsünün zaman damgasını `az storage file list` komutuna geçirerek, paylaşım anlık görüntüsünün içeriğine göz atabilirsiniz:

```azurecli-interactive
az storage file list \
    --account-name $storageAccountName \
    --account-key $storageAccountKey \
    --share-name $shareName \
    --snapshot $snapshot \
    --output table
```

### <a name="list-share-snapshots"></a>Paylaşım anlık görüntülerini listeleme
Paylaşımınız için aldığınız anlık görüntülerin listesini görmek için aşağıdaki komutu kullanın:

```azurecli-interactive
az storage share list \
    --account-name $storageAccountName \
    --account-key $storageAccountKey \
    --include-snapshot \
    --query "[? name== '$shareName' && snapshot!=null].snapshot" \
    --output tsv
```

### <a name="restore-from-a-share-snapshot"></a>Paylaşım anlık görüntüsünden geri yükleme
Daha önce kullandığınız `az storage file copy start` komutunu kullanarak dosyayı geri yükleyebilirsiniz. İlk olarak, anlık görüntüden geri yükleyebilmek için, karşıya yüklediğiniz SampleUpload.txt dosyasını silin:

```azurecli-interactive
# Delete SampleUpload.txt
az storage file delete \
    --account-name $storageAccountName \
    --account-key $storageAccountKey \
    --share-name $shareName \
    --path "myDirectory/SampleUpload.txt" \
    --output none

# Build the source URI for a snapshot restore
URI=$(az storage account show \
    --resource-group $resourceGroupName \
    --name $storageAccountName \
    --query "primaryEndpoints.file" | tr -d '"')

URI=$URI$shareName"/myDirectory/SampleUpload.txt?sharesnapshot="$snapshot

# Restore SampleUpload.txt from the share snapshot
az storage file copy start \
    --account-name $storageAccountName \
    --account-key $storageAccountKey \
    --source-uri $URI \
    --destination-share $shareName \
    --destination-path "myDirectory/SampleUpload.txt"
```

### <a name="delete-a-share-snapshot"></a>Paylaşım anlık görüntüsünü silme
[`az storage share delete`](/cli/azure/storage/share) Komutu kullanarak bir paylaşım anlık görüntüsünü silebilirsiniz. `--snapshot` parametresine `$SNAPSHOT` başvurusunu içeren değişkeni kullanın:

```azurecli-interactive
az storage share delete \
    --account-name $storageAccountName \
    --account-key $storageAccountKey \
    --name $shareName \
    --snapshot $snapshot \
    --output none
```

## <a name="clean-up-resources"></a>Kaynakları temizleme
İşi bittiğinde, kaynak grubunu [`az group delete`](/cli/azure/group) ve ilgili tüm kaynakları kaldırmak için komutu kullanabilirsiniz: 

```azurecli-interactive 
az group delete --name $resourceGroupName
```

Alternatif olarak, kaynakları ayrı ayrı kaldırabilirsiniz.
- Bu makale boyunca oluşturduğunuz Azure dosya paylaşımlarını kaldırmak için:

    ```azurecli-interactive
    az storage share list \
            --account-name $storageAccountName \
            --account-key $storageAccountKey \
            --query "[].name" \
            --output tsv | \
        xargs -L1 bash -ec '\
            az storage share delete \
                --account-name "$storageAccountName" \
                --account-key "$storageAccountKey" \
                --name $0 \
                --delete-snapshots include \
                --output none'
    ```

- Depolama hesabını kaldırmak için. (Bu işlem, oluşturduğunuz Azure dosya paylaşımlarını ve bir Azure Blob depolama kapsayıcısı gibi oluşturmuş olabileceğiniz diğer depolama kaynaklarını örtülü olarak kaldırır.)

    ```azurecli-interactive
    az storage account delete \
        --resource-group $resourceGroupName \
        --name $storageAccountName \
        --yes
    ```

## <a name="next-steps"></a>Sonraki adımlar
> [!div class="nextstepaction"]
> [Azure Dosyaları nedir?](storage-files-introduction.md)