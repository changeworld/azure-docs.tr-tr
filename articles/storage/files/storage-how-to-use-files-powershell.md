---
title: Azure PowerShell ile Azure dosya paylaşımlarını yönetme hızlı başlangıcı
description: Azure PowerShell kullanarak Azure dosya paylaşımlarını yönetmeyi öğrenmek için bu hızlı başlangıcı kullanın.
author: roygara
ms.service: storage
ms.topic: quickstart
ms.date: 10/26/2018
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: c419c2127b1c5fe3aaa60c6e828ff0c5a6676c07
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/26/2020
ms.locfileid: "77598556"
---
# <a name="quickstart-create-and-manage-an-azure-file-share-with-azure-powershell"></a>Hızlı Başlangıç: Azure PowerShell ile Azure dosya paylaşımını oluşturma ve yönetme 
Bu kılavuzda, PowerShell kullanarak [Azure dosya paylaşımlarıyla](storage-files-introduction.md) çalışmanın temel bilgileri gösterilmektedir. Azure dosya paylaşımları diğer dosya paylaşımları gibidir, ancak bulutta depolanır ve Azure platformu tarafından desteklenir. Azure dosya paylaşımları endüstri standardı SMB protokolünü destekler ve birden çok makine, uygulama ve örnek arasında dosya paylaşmayı olanaklı kılar. 

Azure aboneliğiniz yoksa, başlamadan önce [ücretsiz](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) bir hesap oluşturun.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

PowerShell'i yerel olarak yüklemek ve kullanmak istiyorsanız, bu kılavuz Azure PowerShell modülü Az sürüm 0.7 veya daha sonra olmalıdır. Azure PowerShell modülünün hangi sürümünü çalıştırdığınızı öğrenmek için `Get-Module -ListAvailable Az` komutunu yürütün. Yükseltmeniz gerekirse, bkz. [Azure PowerShell modülünü yükleme](/powershell/azure/install-Az-ps). PowerShell'i yerel olarak çalıştırıyorsanız Azure hesabınızda oturum açmak için `Login-AzAccount` komutunu da çalıştırmanız gerekir.

## <a name="create-a-resource-group"></a>Kaynak grubu oluşturma
Kaynak grubu, Azure kaynaklarının dağıtıldığı ve yönetildiği bir mantıksal kapsayıcıdır. Zaten bir Azure kaynak grubunuz yoksa, [Yeni Kaynak Grubu](/powershell/module/az.resources/new-azresourcegroup) cmdlet ile yeni bir grup oluşturabilirsiniz. 

Aşağıdaki örnek, Batı ABD 2 bölgesinde *myResourceGroup* adında bir kaynak grubu oluşturur:

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$region = "westus2"

New-AzResourceGroup `
    -Name $resourceGroupName `
    -Location $region | Out-Null
```

## <a name="create-a-storage-account"></a>Depolama hesabı oluşturma
Depolama hesabı, Azure dosya paylaşımlarını dağıtmak için kullanabileceğiniz paylaşılan bir depolama havuzudur. Bir depolama hesabında sınırsız sayıda paylaşım olabilir; paylaşım da, depolama hesabının kapasite sınırları içinde sınırsız sayıda dosya depolayabilir. Bu örnek, sabit disk sürücüsü (HDD) döndürme ortamlarında standart Azure dosya paylaşımlarını veya blobs veya kuyruklar gibi diğer depolama kaynaklarını depolayabilen genel amaçlı bir sürüm 2 (GPv2 depolama hesabı) oluşturur. Azure Files ayrıca birinci sınıf düz durum lu disk sürücülerini (SSD'ler) destekler; premium Azure dosya paylaşımları FileStorage depolama hesaplarında oluşturulabilir.

Bu örnek, Yeni Depolama [Hesabı](/powershell/module/az.storage/new-azstorageaccount) cmdlet kullanarak bir depolama hesabı oluşturur. Depolama hesabı *mystorageaccount\<rasgele numarası>* olarak adlandırılır ve bu depolama hesabına bir başvuru değişken **$storageAcct**depolanır. Depolama hesabı adları benzersiz olmalıdır; bu nedenle `Get-Random` kullanarak adın sonuna bir sayı ekleyip adı benzersiz hale getirin. 

```azurepowershell-interactive 
$storageAccountName = "mystorageacct$(Get-Random)"

$storageAcct = New-AzStorageAccount `
    -ResourceGroupName $resourceGroupName `
    -Name $storageAccountName `
    -Location $region `
    -Kind StorageV2 `
    -SkuName Standard_ZRS `
    -EnableLargeFileShare
```

> [!Note]  
> 5 TiB'den büyük hisseler (hisse başına en fazla 100 TiB) yalnızca yerel yedekli (LRS) ve bölge yedekli (ZRS) depolama hesaplarında kullanılabilir. Coğrafi yedekli (GRS) veya coğrafi bölge yedekli (GZRS) depolama hesabı oluşturmak için `-EnableLargeFileShare` parametreyi kaldırın.

## <a name="create-an-azure-file-share"></a>Azure dosya paylaşımı oluşturma
Artık ilk Azure dosya paylaşımınızı oluşturabilirsiniz. [New-AzRmStorageShare](/powershell/module/az.storage/New-AzRmStorageShare) cmdlet'i kullanarak dosya paylaşımı oluşturabilirsiniz. Bu örnek, `myshare` adlı bir paylaşım oluşturur.

```azurepowershell-interactive
$shareName = "myshare"

New-AzRmStorageShare `
    -StorageAccount $storageAcct `
    -Name $shareName `
    -QuotaGiB 1024 | Out-Null
```

Paylaşım adları tümüyle küçük harf, sayı ve tek kısa çizgilerden oluşmalı ve kısa çizgiyle başlamamalıdır. Dosya paylaşımlarını ve dosyalarını adlandırma hakkında ayrıntılı bilgi için, [Paylaşımları, Dizinleri, Dosyaları ve Meta Verileri Adlandırma ve Başvurma bölümüne](https://docs.microsoft.com/rest/api/storageservices/Naming-and-Referencing-Shares--Directories--Files--and-Metadata)bakın.

## <a name="use-your-azure-file-share"></a>Azure dosya paylaşımınızı kullanma
Azure Dosyaları, Azure dosya paylaşımınızdaki dosya ve klasörler ile çalışmak için iki yöntem sunar: sektör standardı [Sunucu İleti Bloğu (SMB) protokolü](https://msdn.microsoft.com/library/windows/desktop/aa365233.aspx) ve [Dosya REST protokolü](https://docs.microsoft.com/rest/api/storageservices/file-service-rest-api). 

Bir dosya paylaşımını SMB ile bağlayabilmeniz için işletim sisteminize göre aşağıdaki belgeye bakın:
- [Windows](storage-how-to-use-files-windows.md)
- [Linux](storage-how-to-use-files-linux.md)
- [Macos](storage-how-to-use-files-mac.md)

### <a name="using-an-azure-file-share-with-the-file-rest-protocol"></a>Dosya REST protokolü ile bir Azure dosya paylaşımını kullanma 
Doğrudan Dosya REST protokolü ile çalışmak mümkündür (örneğin, el işçiliği REST HTTP kendiniz çağırır), ancak Dosya REST protokolünü kullanmanın en yaygın yolu Azure PowerShell modülünü, [Azure CLI](storage-how-to-use-files-cli.md)veya Azure Depolama SDK'sını kullanmaktır ve bunların hepsi seçtiğiniz komut dosyası/programlama dilinde Dosya REST protokolü nün etrafında güzel bir sarmalayıcı sağlar.  

Kullanabilmeyi umduğunuz uygulama ve araçlarını kullanmanıza izin vereceği için çoğu durumda Azure dosya paylaşımları ile SMP protokolü üzerinden çalışmanızı bekliyoruz, ancak SMB yerine Dosya REST API'si kullanmanın aşağıdaki gibi bazı avantajları bulunmaktadır:

- Dosya paylaşımınıza (SMB üzerinden dosya paylaşımı bağlayamayan) PowerShell Cloud Shell'den göz atıyorsanız.
- [Azure İşlevleri](../../azure-functions/functions-overview.md) gibi sunucusuz kaynaklardan yararlanıyorsanız. 
- Yedekleme veya virüsten koruma taraması yapmak gibi birçok Azure dosya paylaşımıyla etkileşimedebilen bir değer katma hizmeti oluşturuyorsunuz.

Aşağıdaki örnekler, Azure dosya paylaşımınızı Dosya REST protokolüyle işlemek için Azure PowerShell modülünün nasıl kullanılacağını gösterir. `-Context` Parametre, dosya paylaşımına karşı belirtilen eylemleri gerçekleştirmek için depolama hesabı anahtarını almak için kullanılır. Depolama hesabı anahtarını almak için depolama hesabında `Owner` RBAC rolüne sahip olmalısınız.

#### <a name="create-directory"></a>Dizin oluşturma
Azure dosya paylaşımınızın kökünde *myDirectory* adında yeni bir dizin oluşturmak için [Yeni Depolama Dizini](/powershell/module/az.storage/New-AzStorageDirectory) cmdlet'ini kullanın.

```azurepowershell-interactive
New-AzStorageDirectory `
   -Context $storageAcct.Context `
   -ShareName $shareName `
   -Path "myDirectory"
```

#### <a name="upload-a-file"></a>Dosyayı karşıya yükleme
[Set-AzStorageFileContent](/powershell/module/az.storage/Set-AzStorageFileContent) cmdlet'i kullanarak bir dosyayı nasıl yükleyebileceğimizi göstermek için öncelikle PowerShell Cloud Shell'inizin yükleme sürücünüzün içinde bir dosya oluşturmamız gerekir. 

Bu örnek, karalama sürücünüzdeki yeni bir dosyaya geçerli tarih ve saati ekler, sonra dosyayı dosya paylaşımına yükler.

```azurepowershell-interactive
# this expression will put the current date and time into a new file on your scratch drive
cd "~/CloudDrive/"
Get-Date | Out-File -FilePath "SampleUpload.txt" -Force

# this expression will upload that newly created file to your Azure file share
Set-AzStorageFileContent `
   -Context $storageAcct.Context `
   -ShareName $shareName `
   -Source "SampleUpload.txt" `
   -Path "myDirectory\SampleUpload.txt"
```   

PowerShell’i yerel olarak çalıştırıyorsanız, `~/CloudDrive/` bölümünün yerine makinenizde var olan bir yol kullanmalısınız.

Dosyayı yükledikten sonra, dosyanın Azure dosya paylaşımınıza yüklendiğinden emin olmak için [Get-AzStorageFile](/powershell/module/Az.Storage/Get-AzStorageFile) cmdlet'i kullanabilirsiniz. 

```azurepowershell-interactive
Get-AzStorageFile `
    -Context $storageAcct.Context `
    -ShareName $shareName `
    -Path "myDirectory\" 
```

#### <a name="download-a-file"></a>Dosya indirme
Az [DepolamaDosyaİçeriği](/powershell/module/az.storage/Get-AzStorageFilecontent) cmdlet'ini kullanarak Bulut Shell'inizin sıfırdan yüklenen dosyanın bir kopyasını indirebilirsiniz.

```azurepowershell-interactive
# Delete an existing file by the same name as SampleDownload.txt, if it exists because you've run this example before.
Remove-Item `
    -Path "SampleDownload.txt" `
    -Force `
    -ErrorAction SilentlyContinue

Get-AzStorageFileContent `
    -Context $storageAcct.Context `
    -ShareName $shareName `
    -Path "myDirectory\SampleUpload.txt" `
    -Destination "SampleDownload.txt"
```

Dosyayı indirdikten sonra, `Get-ChildItem` komutunu kullanarak dosyanın PowerShell Cloud Shell’inizin karalama sürücüsüne indirildiğini görebilirsiniz.

```azurepowershell-interactive
Get-ChildItem | Where-Object { $_.Name -eq "SampleDownload.txt" }
``` 

#### <a name="copy-files"></a>Dosyaları kopyalama
Yaygın bir görev, dosyaları bir dosya paylaşımından başka bir dosya paylaşımına kopyalamaktır. Bu işlevselliği göstermek için, yeni bir paylaşım oluşturabilir ve az önce yüklediğiniz dosyayı [Başlat-AzStorageFileCopy](/powershell/module/az.storage/Start-AzStorageFileCopy) cmdlet'i kullanarak bu yeni paylaşıma kopyalayabilirsiniz. 

```azurepowershell-interactive
$otherShareName = "myshare2"

New-AzRmStorageShare `
    -StorageAccount $storageAcct `
    -Name $otherShareName `
    -QuotaGiB 1024 | Out-Null
  
New-AzStorageDirectory `
   -Context $storageAcct.Context `
   -ShareName $otherShareName `
   -Path "myDirectory2"

Start-AzStorageFileCopy `
    -Context $storageAcct.Context `
    -SrcShareName $shareName `
    -SrcFilePath "myDirectory\SampleUpload.txt" `
    -DestShareName $otherShareName `
    -DestFilePath "myDirectory2\SampleCopy.txt" `
    -DestContext $storageAcct.Context
```

Şimdi, yeni paylaşımdaki dosyaları listelerseniz kopyalanan dosyanızı görmeniz gerekir.

```azurepowershell-interactive
Get-AzStorageFile `
    -Context $storageAcct.Context `
    -ShareName $otherShareName `
    -Path "myDirectory2" 
```

Cmdlet, Azure dosya paylaşımları arasında, geçişler ve daha büyük veri hareketleri için geçici `rsync` dosya hareketleri için kullanışlı olsa da, Windows ve macOS ve Linux'ta öneririz. `robocopy` `Start-AzStorageFileCopy` `robocopy`ve `rsync` FileREST API yerine veri hareketlerini gerçekleştirmek için SMB'yi kullanın.

## <a name="create-and-manage-share-snapshots"></a>Paylaşım anlık görüntülerini oluşturma ve yönetme
Azure dosya paylaşımıyla yerine getirebileceğiniz kullanışlı bir diğer görev de paylaşım anlık görüntüleri oluşturmaktır. Anlık görüntü, Azure dosya paylaşımının zamanın bir noktasındaki durumunu saklar. Paylaşım anlık görüntüleri, aşağıdakiler gibi zaten tanıyor olabileceğiniz işletim sistemi teknolojilerine benzer:

- NTFS ve ReFS gibi Windows dosya sistemleri için [Birim Gölge Kopyalama Hizmeti (VSS).](https://docs.microsoft.com/windows/desktop/VSS/volume-shadow-copy-service-portal)
- Linux sistemleri için [Mantıksal Birim Yöneticisi (LVM)](https://en.wikipedia.org/wiki/Logical_Volume_Manager_(Linux)#Basic_functionality) anlık görüntüleri.
- macOS için [Apple Dosya Sistemi (APFS)](https://developer.apple.com/library/content/documentation/FileManagement/Conceptual/APFS_Guide/Features/Features.html) anlık görüntüleri. 

[Get-AzStorageShare](/powershell/module/az.storage/Get-AzStorageShare) cmdlet ile `Snapshot` alınan bir dosya paylaşımı için PowerShell nesnesi üzerindeki yöntemi kullanarak bir paylaşım için bir paylaşım anlık görüntü oluşturabilirsiniz. 

```azurepowershell-interactive
$share = Get-AzStorageShare -Context $storageAcct.Context -Name $shareName
$snapshot = $share.Snapshot()
```

### <a name="browse-share-snapshots"></a>Paylaşım anlık görüntülerine göz atma
`Get-AzStorageFile` cmdlet’inin `-Share` parametresine anlık görüntü başvurusunu (`$snapshot`) geçirerek paylaşım anlık görüntüsünün içeriklerine göz atabilirsiniz.

```azurepowershell-interactive
Get-AzStorageFile -Share $snapshot
```

### <a name="list-share-snapshots"></a>Paylaşım anlık görüntülerini listeleme
Paylaşımınız için aldığınız anlık görüntülerin listesini aşağıdaki komutla görebilirsiniz.

```azurepowershell-interactive
Get-AzStorageShare `
        -Context $storageAcct.Context | `
    Where-Object { $_.Name -eq $shareName -and $_.IsSnapshot -eq $true }
```

### <a name="restore-from-a-share-snapshot"></a>Paylaşım anlık görüntüsünden geri yükleme
Daha önce kullandığımız `Start-AzStorageFileCopy` komutu kullanarak dosyayı geri yükleyebilirsiniz. Bu hızlı başlangıçta ilk olarak önceden karşıya yüklediğimiz `SampleUpload.txt` dosyamızı silerek anlık görüntüden bu dosyayı geri yükleyeceğiz.

```azurepowershell-interactive
# Delete SampleUpload.txt
Remove-AzStorageFile `
    -Context $storageAcct.Context `
    -ShareName $shareName `
    -Path "myDirectory\SampleUpload.txt"

# Restore SampleUpload.txt from the share snapshot
Start-AzStorageFileCopy `
    -SrcShare $snapshot `
    -SrcFilePath "myDirectory\SampleUpload.txt" `
    -DestContext $storageAcct.Context `
    -DestShareName $shareName `
    -DestFilePath "myDirectory\SampleUpload.txt"
```

### <a name="delete-a-share-snapshot"></a>Paylaşım anlık görüntüsünü silme
`-Share` Parametreye `$snapshot` başvuruyu içeren değişkenle [Remove-AzStorageShare](/powershell/module/az.storage/Remove-AzStorageShare) cmdlet'i kullanarak bir hisse anlık görüntüsünü silebilirsiniz.

```azurepowershell-interactive
Remove-AzStorageShare `
    -Share $snapshot `
    -Confirm:$false `
    -Force
```

## <a name="clean-up-resources"></a>Kaynakları temizleme
İşi bittiğinde, kaynak grubunu ve ilgili tüm kaynakları kaldırmak için [Kaldır-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) cmdlet'ini kullanabilirsiniz. 

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroup
```

Alternatif olarak kaynakları tek tek de kaldırabilirsiniz:

- Bu hızlı başlangıç için oluşturduğumuz Azure dosya paylaşımlarını kaldırmak için.

    ```azurepowershell-interactive
    Get-AzRmStorageShare -StorageAccount $storageAcct | Remove-AzRmStorageShare -Force
    ```

    > [!Note]  
    > Azure dosya paylaşımını silmeden önce oluşturduğunuz Azure dosya paylaşımlarının tüm paylaşım anlık görüntülerini silmeniz gerekir.

- Depolama hesabının kendisini kaldırmak için (bu işlem örtülü olarak hem oluşturduğumuz Azure dosya paylaşımlarını hem de oluşturmuş olabileceğiniz Azure Blob depolama kapsayıcısı gibi diğer depolama kaynaklarını kaldırır).

    ```azurepowershell-interactive
    Remove-AzStorageAccount `
        -ResourceGroupName $storageAcct.ResourceGroupName `
        -Name $storageAcct.StorageAccountName
    ```

## <a name="next-steps"></a>Sonraki adımlar
> [!div class="nextstepaction"]
> [Azure Dosyaları nedir?](storage-files-introduction.md)