---
title: Windows VM'ler için resim galerisiyle Azure Image Builder'ı kullanma (önizleme)
description: Azure Image Builder ve Azure PowerShell'i kullanarak Azure Paylaşılan Galeri resim sürümleri oluşturun.
author: cynthn
ms.author: cynthn
ms.date: 01/14/2020
ms.topic: article
ms.service: virtual-machines-windows
manager: gwallace
ms.openlocfilehash: d5856780d0d9f1a1943bca1c2f076bb3ec914e1d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76263362"
---
# <a name="preview-create-a-windows-image-and-distribute-it-to-a-shared-image-gallery"></a>Önizleme: Windows görüntüsü oluşturun ve Paylaşılan Resim Galerisi'ne dağıtın 

Bu makale, [Paylaşılan Resim Galerisi'nde](shared-image-galleries.md)bir resim sürümü oluşturmak ve ardından resmi genel olarak dağıtmak için Azure Image Builder ve Azure PowerShell'i nasıl kullanabileceğinizi göstermek içindir. Bunu [Azure CLI'yi](../linux/image-builder-gallery.md)kullanarak da yapabilirsiniz.

Görüntüyü yapılandırmak için bir .json şablonu kullanıyor olacağız. Kullandığımız .json dosyası burada: [armTemplateWinSIG.json](https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/1_Creating_a_Custom_Win_Shared_Image_Gallery_Image/armTemplateWinSIG.json). Şablonun yerel bir sürümünü indirip düzenleyeceğiz, bu nedenle bu makale yerel PowerShell oturumu kullanılarak yazılmıştır.

Görüntüyü Paylaşılan Resim Galerisi'ne dağıtmak için şablon, `distribute` şablonun bölümü için değer olarak [sharedImage](../linux/image-builder-json.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json#distribute-sharedimage) kullanır.

Azure Image Builder görüntüyü genelleştirmek için sysprep'i otomatik olarak çalıştırıyor, bu genel bir sysprep komutudur ve gerekirse [geçersiz kılınabilir.](https://github.com/danielsollondon/azvmimagebuilder/blob/master/troubleshootingaib.md#vms-created-from-aib-images-do-not-create-successfully) 

Özelleştirmeleri kaç kez katmana aldığınıza dikkat edin. Sysprep komutunu tek bir Windows görüntüsünde 8 defaya kadar çalıştırabilirsiniz. Sysprep'i 8 kez çalıştırdıktan sonra Windows resminizi yeniden oluşturmanız gerekir. Daha fazla bilgi için, [Sysprep'i kaç kez çalıştırabileceğinize ilişkin Sınırlar'a](https://docs.microsoft.com/windows-hardware/manufacture/desktop/sysprep--generalize--a-windows-installation#limits-on-how-many-times-you-can-run-sysprep)bakın. 

> [!IMPORTANT]
> Azure Image Builder şu anda genel önizlemede.
> Önizleme sürümü bir hizmet düzeyi sözleşmesi olmadan sağlanır ve üretim iş yüklerinde kullanılması önerilmez. Bazı özellikler desteklenmiyor olabileceği gibi özellikleri sınırlandırılmış da olabilir. Daha fazla bilgi için Microsoft [Azure Önizlemeleri için Ek Kullanım Koşulları'na](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)bakın.

## <a name="register-the-features"></a>Özellikleri kaydedin
Önizleme sırasında Azure Image Builder'ı kullanmak için yeni özelliği kaydetmeniz gerekir.

```powershell
Register-AzProviderFeature -FeatureName VirtualMachineTemplatePreview -ProviderNamespace Microsoft.VirtualMachineImages
```

Özellik kaydının durumunu denetleyin.

```powershell
Get-AzProviderFeature -FeatureName VirtualMachineTemplatePreview -ProviderNamespace Microsoft.VirtualMachineImages
```

Bir `RegistrationState` sonraki `Registered` adıma geçmeden önce kadar bekleyin.

Sağlayıcı kayıtlarınızı kontrol edin. Her biri `Registered`döner emin olun.

```powershell
Get-AzResourceProvider -ProviderNamespace Microsoft.VirtualMachineImages | Format-table -Property ResourceTypes,RegistrationState
Get-AzResourceProvider -ProviderNamespace Microsoft.Storage | Format-table -Property ResourceTypes,RegistrationState 
Get-AzResourceProvider -ProviderNamespace Microsoft.Compute | Format-table -Property ResourceTypes,RegistrationState
Get-AzResourceProvider -ProviderNamespace Microsoft.KeyVault | Format-table -Property ResourceTypes,RegistrationState
```

Geri dönmezlerse, `Registered`sağlayıcıları kaydetmek için aşağıdakileri kullanın:

```powershell
Register-AzResourceProvider -ProviderNamespace Microsoft.VirtualMachineImages
Register-AzResourceProvider -ProviderNamespace Microsoft.Storage
Register-AzResourceProvider -ProviderNamespace Microsoft.Compute
Register-AzResourceProvider -ProviderNamespace Microsoft.KeyVault
```

## <a name="create-variables"></a>Değişken oluşturma

Bazı bilgileri tekrar tekrar kullanarak bu bilgileri depolamak için bazı değişkenler oluşturacağız. Değişkenlerin değerlerini, örneğin `username` ve `vmpassword`kendi bilgilerinizle değiştirin.

```powershell
# Get existing context
$currentAzContext = Get-AzContext

# Get your current subscription ID. 
$subscriptionID=$currentAzContext.Subscription.Id

# Destination image resource group
$imageResourceGroup="aibwinsig"

# Location
$location="westus"

# Image distribution metadata reference name
$runOutputName="aibCustWinManImg02ro"

# Image template name
$imageTemplateName="helloImageTemplateWin02ps"

# Distribution properties object name (runOutput).
# This gives you the properties of the managed image on completion.
$runOutputName="winclientR01"
```



## <a name="create-the-resource-group"></a>Kaynak grubunu oluşturma

Bir kaynak grubu oluşturun ve Azure Image Builder'a bu kaynak grubunda kaynak oluşturma izni verin.

```powershell
New-AzResourceGroup `
   -Name $imageResourceGroup `
   -Location $location
New-AzRoleAssignment `
   -ObjectId ef511139-6170-438e-a6e1-763dc31bdf74 `
   -Scope /subscriptions/$subscriptionID/resourceGroups/$imageResourceGroup `
   -RoleDefinitionName Contributor
```



## <a name="create-the-shared-image-gallery"></a>Paylaşılan Resim Galerisini Oluşturma

Paylaşılan bir resim galerisiyle Image Builder'ı kullanmak için varolan bir resim galerisine ve resim tanımına sahip olmanız gerekir. Image Builder sizin için resim galerisi ve resim tanımı oluşturmaz.

Zaten kullanmak için bir galeri ve resim tanımı yoksa, bunları oluşturarak başlayın. İlk olarak, bir resim galerisi oluşturun.

```powershell
# Image gallery name
$sigGalleryName= "myIBSIG"

# Image definition name
$imageDefName ="winSvrimage"

# additional replication region
$replRegion2="eastus"

# Create the gallery
New-AzGallery `
   -GalleryName $sigGalleryName `
   -ResourceGroupName $imageResourceGroup  `
   -Location $location

# Create the image definition
New-AzGalleryImageDefinition `
   -GalleryName $sigGalleryName `
   -ResourceGroupName $imageResourceGroup `
   -Location $location `
   -Name $imageDefName `
   -OsState generalized `
   -OsType Windows `
   -Publisher 'myCompany' `
   -Offer 'WindowsServer' `
   -Sku 'WinSrv2019'
```



## <a name="download-and-configure-the-template"></a>Şablonu indirin ve yapılandırın

.json şablonunu indirin ve değişkenlerinizle yapılandırın.

```powershell

$templateFilePath = "armTemplateWinSIG.json"

Invoke-WebRequest `
   -Uri "https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/1_Creating_a_Custom_Win_Shared_Image_Gallery_Image/armTemplateWinSIG.json" `
   -OutFile $templateFilePath `
   -UseBasicParsing

(Get-Content -path $templateFilePath -Raw ) `
   -replace '<subscriptionID>',$subscriptionID | Set-Content -Path $templateFilePath
(Get-Content -path $templateFilePath -Raw ) `
   -replace '<rgName>',$imageResourceGroup | Set-Content -Path $templateFilePath
(Get-Content -path $templateFilePath -Raw ) `
   -replace '<runOutputName>',$runOutputName | Set-Content -Path $templateFilePath
(Get-Content -path $templateFilePath -Raw ) `
   -replace '<imageDefName>',$imageDefName | Set-Content -Path $templateFilePath
(Get-Content -path $templateFilePath -Raw ) `
   -replace '<sharedImageGalName>',$sigGalleryName | Set-Content -Path $templateFilePath
(Get-Content -path $templateFilePath -Raw ) `
   -replace '<region1>',$location | Set-Content -Path $templateFilePath
(Get-Content -path $templateFilePath -Raw ) `
   -replace '<region2>',$replRegion2 | Set-Content -Path $templateFilePath

```


## <a name="create-the-image-version"></a>Resim sürümünü oluşturma

Şablonunuz hizmete gönderilmelidir, bu komut dosyaları gibi bağımlı yapıları karşıdan yükleyecek ve bunları *IT_* önceden belirlenmiş evreleme Kaynak Grubunda depolayacak.

```powershell
New-AzResourceGroupDeployment `
   -ResourceGroupName $imageResourceGroup `
   -TemplateFile $templateFilePath `
   -api-version "2019-05-01-preview" `
   -imageTemplateName $imageTemplateName `
   -svclocation $location
```

Görüntüyü oluşturmak için şablona 'Çalıştır' diye çağırmanız gerekir.

```powershell
Invoke-AzResourceAction `
   -ResourceName $imageTemplateName `
   -ResourceGroupName $imageResourceGroup `
   -ResourceType Microsoft.VirtualMachineImages/imageTemplates `
   -ApiVersion "2019-05-01-preview" `
   -Action Run
```

Görüntüyü oluşturma ve her iki bölgeye çoğaltmak biraz zaman alabilir. VM oluşturmaya geçmeden önce bu bölümün bitmesini bekleyin.

Görüntü oluşturma durumunu otomatikleştirmek için seçenekler hakkında bilgi için GitHub'daki bu şablon için [Readme'a](https://github.com/danielsollondon/azvmimagebuilder/blob/master/quickquickstarts/1_Creating_a_Custom_Win_Shared_Image_Gallery_Image/readme.md#get-status-of-the-image-build-and-query) bakın.


## <a name="create-the-vm"></a>Sanal makine oluşturma

Azure Image Builder tarafından oluşturulan resim sürümünden bir VM oluşturun.

Oluşturduğunuz resim sürümünü alın.
```powershell
$imageVersion = Get-AzGalleryImageVersion `
   -ResourceGroupName $imageResourceGroup `
   -GalleryName $sigGalleryName `
   -GalleryImageDefinitionName $imageDefName
```

Görüntünün çoğaltıldığı ikinci bölgede VM'yi oluşturun.

```powershell
$vmResourceGroup = "myResourceGroup"
$vmName = "myVMfromImage"

# Create user object
$cred = Get-Credential -Message "Enter a username and password for the virtual machine."

# Create a resource group
New-AzResourceGroup -Name $vmResourceGroup -Location $replRegion2

# Network pieces
$subnetConfig = New-AzVirtualNetworkSubnetConfig -Name mySubnet -AddressPrefix 192.168.1.0/24
$vnet = New-AzVirtualNetwork -ResourceGroupName $vmResourceGroup -Location $replRegion2 `
  -Name MYvNET -AddressPrefix 192.168.0.0/16 -Subnet $subnetConfig
$pip = New-AzPublicIpAddress -ResourceGroupName $vmResourceGroup -Location $replRegion2 `
  -Name "mypublicdns$(Get-Random)" -AllocationMethod Static -IdleTimeoutInMinutes 4
$nsgRuleRDP = New-AzNetworkSecurityRuleConfig -Name myNetworkSecurityGroupRuleRDP  -Protocol Tcp `
  -Direction Inbound -Priority 1000 -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * `
  -DestinationPortRange 3389 -Access Allow
$nsg = New-AzNetworkSecurityGroup -ResourceGroupName $vmResourceGroup -Location $replRegion2 `
  -Name myNetworkSecurityGroup -SecurityRules $nsgRuleRDP
$nic = New-AzNetworkInterface -Name myNic -ResourceGroupName $vmResourceGroup -Location $replRegion2 `
  -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id -NetworkSecurityGroupId $nsg.Id

# Create a virtual machine configuration using $imageVersion.Id to specify the shared image
$vmConfig = New-AzVMConfig -VMName $vmName -VMSize Standard_D1_v2 | `
Set-AzVMOperatingSystem -Windows -ComputerName $vmName -Credential $cred | `
Set-AzVMSourceImage -Id $imageVersion.Id | `
Add-AzVMNetworkInterface -Id $nic.Id

# Create a virtual machine
New-AzVM -ResourceGroupName $vmResourceGroup -Location $replRegion2 -VM $vmConfig
```

## <a name="verify-the-customization"></a>Özelleştirmeyi doğrulama
VM'yi oluştururken belirlediğiniz kullanıcı adı ve parolayı kullanarak VM'ye Uzak Masaüstü bağlantısı oluşturun. VM'nin içinde cmd istemi ni açın ve yazın:

```console
dir c:\
```

Görüntü özelleştirme sırasında oluşturulan `buildActions` bir dizin görmeniz gerekir.


## <a name="clean-up-resources"></a>Kaynakları temizleme
Şimdi aynı görüntünün yeni bir sürümünü oluşturmak için resim sürümünü yeniden özelleştirmeyi denemek istiyorsanız, **bu adımı atlayın** ve [başka bir resim sürümü oluşturmak için Azure Image Builder'ı kullanın' a](image-builder-gallery-update-image-version.md)gidin.


Bu, oluşturulan tüm diğer kaynak dosyalarıyla birlikte oluşturulan görüntüyü siler. Kaynakları silmeden önce bu dağıtımı bitirdiğinden emin olun.

Önce kaynak grubu şablonu silin, aksi takdirde AIB tarafından kullanılan evreleme kaynak grubu *(IT_)* temizlenmez.

Resim şablonunun ResourceID'sini alın. 

```powerShell
$resTemplateId = Get-AzResource -ResourceName $imageTemplateName -ResourceGroupName $imageResourceGroup -ResourceType Microsoft.VirtualMachineImages/imageTemplates -ApiVersion "2019-05-01-preview"
```

Resim şablonu silin.

```powerShell
Remove-AzResource -ResourceId $resTemplateId.ResourceId -Force
```

kaynak grubunu silin.

```powerShell
Remove-AzResourceGroup $imageResourceGroup -Force
```

## <a name="next-steps"></a>Sonraki Adımlar

Oluşturduğunuz resim sürümünü nasıl güncelleştirdiğinizi öğrenmek [için başka bir resim sürümü oluşturmak için Azure Image Builder'ı kullanın'a](image-builder-gallery-update-image-version.md)bakın.
