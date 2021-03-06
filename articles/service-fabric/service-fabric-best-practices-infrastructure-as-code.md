---
title: Kod En İyi Uygulamalar olarak Azure Hizmet Kumaş altyapısı
description: Azure Hizmet Kumaşı'nı kod olarak altyapı olarak yönetmek için en iyi uygulamalar ve tasarım konuları.
author: peterpogorski
ms.topic: conceptual
ms.date: 01/23/2019
ms.author: pepogors
ms.openlocfilehash: 1c044d5fd973d3c577088a887f2fac413d2ab79d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75551837"
---
# <a name="infrastructure-as-code"></a>Kod olarak altyapı

Üretim senaryosunda, Kaynak Yöneticisi şablonlarını kullanarak Azure Hizmet Dokusu kümeleri oluşturun. Kaynak Yöneticisi şablonları kaynak özellikleri üzerinde daha fazla denetim sağlar ve tutarlı bir kaynak modeline sahip olduğundan emin olun.

Windows ve Linux için [GitHub'daki Azure örneklerinde](https://github.com/Azure-Samples/service-fabric-cluster-templates)örnek Kaynak Yöneticisi şablonları mevcuttur. Bu şablonlar küme şablonunuzun başlangıç noktası olarak kullanılabilir. Özel `azuredeploy.json` `azuredeploy.parameters.json` gereksinimlerinizi karşılamak için indirin ve bunları edin.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Yukarıda `azuredeploy.json` indirdiğiniz `azuredeploy.parameters.json` şablonları dağıtmak için aşağıdaki Azure CLI komutlarını kullanın:

```azurecli
ResourceGroupName="sfclustergroup"
Location="westus"

az group create --name $ResourceGroupName --location $Location 
az group deployment create --name $ResourceGroupName  --template-file azuredeploy.json --parameters @azuredeploy.parameters.json
```

Powershell kullanarak kaynak oluşturma

```powershell
$ResourceGroupName="sfclustergroup"
$Location="westus"
$Template="azuredeploy.json"
$Parameters="azuredeploy.parameters.json"

New-AzResourceGroup -Name $ResourceGroupName -Location $Location
New-AzResourceGroupDeployment -Name $ResourceGroupName -TemplateFile $Template -TemplateParameterFile $Parameters
```

## <a name="azure-service-fabric-resources"></a>Azure Hizmet Kumaşı kaynakları

Azure Kaynak Yöneticisi aracılığıyla uygulamaları ve hizmetleri Hizmet Kumaşı kümenize dağıtabilirsiniz. Ayrıntılar için [uygulamaları ve hizmetleri Azure Kaynak Yöneticisi kaynakları olarak yönet'e](https://docs.microsoft.com/azure/service-fabric/service-fabric-application-arm-resource) bakın. Aşağıda, Kaynak Yöneticisi şablon kaynaklarınıza dahil edilebilmek için hizmet kumaşuygulamasına özel kaynaklar uygulanmaktadır.

```json
{
    "apiVersion": "2019-03-01",
    "type": "Microsoft.ServiceFabric/clusters/applicationTypes",
    "name": "[concat(parameters('clusterName'), '/', parameters('applicationTypeName'))]",
    "location": "[variables('clusterLocation')]",
},
{
    "apiVersion": "2019-03-01",
    "type": "Microsoft.ServiceFabric/clusters/applicationTypes/versions",
    "name": "[concat(parameters('clusterName'), '/', parameters('applicationTypeName'), '/', parameters('applicationTypeVersion'))]",
    "location": "[variables('clusterLocation')]",
},
{
    "apiVersion": "2019-03-01",
    "type": "Microsoft.ServiceFabric/clusters/applications",
    "name": "[concat(parameters('clusterName'), '/', parameters('applicationName'))]",
    "location": "[variables('clusterLocation')]",
},
{
    "apiVersion": "2019-03-01",
    "type": "Microsoft.ServiceFabric/clusters/applications/services",
    "name": "[concat(parameters('clusterName'), '/', parameters('applicationName'), '/', parameters('serviceName'))]",
    "location": "[variables('clusterLocation')]"
}
```

Azure Kaynak Yöneticisi'ni kullanarak uygulamanızı dağıtmak için öncelikle [bir sfpkg](https://docs.microsoft.com/azure/service-fabric/service-fabric-package-apps#create-an-sfpkg) Hizmet Kumaş Uygulaması paketi oluşturmanız gerekir. Aşağıdaki python komut dosyası, bir sfpkg oluşturmak için nasıl bir örnektir:

```python
# Create SFPKG that needs to be uploaded to Azure Storage Blob Container
microservices_sfpkg = zipfile.ZipFile(
    self.microservices_app_package_name, 'w', zipfile.ZIP_DEFLATED)
package_length = len(self.microservices_app_package_path)

for root, dirs, files in os.walk(self.microservices_app_package_path):
    root_folder = root[package_length:]
    for file in files:
        microservices_sfpkg.write(os.path.join(
            root, file), os.path.join(root_folder, file))

microservices_sfpkg.close()
```

## <a name="azure-virtual-machine-operating-system-automatic-upgrade-configuration"></a>Azure Sanal Makine İşletim Sistemi Otomatik Yükseltme Yapılandırması 
Sanal makinelerinizi yükseltme, kullanıcı tarafından başlatılan bir işlemdir ve Azure Service Fabric kümeleri ana bilgisayar yama yönetimi için [Sanal Makine Ölçeği Set Otomatik İşletim Sistemi yükseltmesi](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-automatic-upgrade) kullanmanız önerilir; Patch Orchestration Application, Azure dışında barındırıldığında tasarlanan alternatif bir çözümdür, ancak POA Azure'da kullanılabilir ve Azure'da POA barındırma nın genel gideri SANAL Makine İşletim Sistemi Otomatik Yükseltme'yi POA'ya tercih etmek için yaygın bir nedendir. Otomatik İşletim Sistemi yükseltmesini etkinleştirmek için Bilgi İşlem Sanal Makine Ölçeği Kaynak Yöneticisi şablon özellikleri şunlardır:

```json
"upgradePolicy": {
   "mode": "Automatic",
   "automaticOSUpgradePolicy": {
        "enableAutomaticOSUpgrade": true,
        "disableAutomaticRollback": false
    }
},
```
Service Fabric ile Otomatik İşletim Sistemi Yükseltmeleri kullanılırken, yeni işletim sistemi görüntüsü, Service Fabric'te çalışan hizmetlerin yüksek kullanılabilirliğini korumak için bir defada bir Güncelleştirme Etki Alanı kullanıma sunulur. Hizmet Kumaşında Otomatik İşletim Sistemi Yükseltmeleri'ni kullanmak için kümenizin Gümüş Dayanıklılık Katmanı'nı veya daha yüksek ini kullanacak şekilde yapılandırılması gerekir.

Windows ana bilgisayar makinelerinizin koordinasyonsuz güncelleştirmeler başlatmasını önlemek için aşağıdaki kayıt defteri anahtarının yanlış olarak ayarlı olduğundan emin olun: HKEY_LOCAL_MACHINE\SOFTWARE\Policies\Microsoft\Windows\WindowsUpdate\AU.

WindowsUpdate kayıt defteri anahtarını yanlış olarak ayarlamak için Bilgi İşlem Sanal Makine Ölçeği Kaynak Yöneticisi şablon özellikleri şunlardır:
```json
"osProfile": {
        "computerNamePrefix": "{vmss-name}",
        "adminUsername": "{your-username}",
        "secrets": [],
        "windowsConfiguration": {
          "provisionVMAgent": true,
          "enableAutomaticUpdates": false
        }
      },
```

## <a name="azure-service-fabric-cluster-upgrade-configuration"></a>Azure Hizmet Kumaş Küme Yükseltme Yapılandırması
Otomatik yükseltmeyi etkinleştirmek için Hizmet Kumaşı küme Kaynak Yöneticisi şablon özelliği aşağıda verilmiştir:
```json
"upgradeMode": "Automatic",
```
Kümenizi el ile yükseltmek için, kabin/deb dağıtımını bir küme sanal makinesine indirin ve ardından aşağıdaki PowerShell'i çağırın:
```powershell
Copy-ServiceFabricClusterPackage -Code -CodePackagePath <"local_VM_path_to_msi"> -CodePackagePathInImageStore ServiceFabric.msi -ImageStoreConnectionString "fabric:ImageStore"
Register-ServiceFabricClusterPackage -Code -CodePackagePath "ServiceFabric.msi"
Start-ServiceFabricClusterUpgrade -Code -CodePackageVersion <"msi_code_version">
```

## <a name="next-steps"></a>Sonraki adımlar

* Windows Server çalıştıran VM'lerde veya bilgisayarlarda küme [oluşturma: Windows Server için Hizmet Kumaşı küme oluşturma](service-fabric-tutorial-create-vnet-and-windows-cluster.md)
* Linux çalıştıran VM'lerde veya bilgisayarlarda küme oluşturma: [Linux kümesi oluşturma](service-fabric-tutorial-create-vnet-and-linux-cluster.md)
* [Service Fabric destek seçenekleri](service-fabric-support.md) hakkında bilgi edinin
