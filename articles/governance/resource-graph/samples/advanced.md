---
title: Gelişmiş sorgu örnekleri
description: Sütunlarla çalışma, kullanılan etiketleri listeleme ve kaynakları normal ifadelerle eşleştirme gibi bazı gelişmiş sorguları çalıştırmak için Azure Kaynak Grafiği'ni kullanın.
ms.date: 03/20/2020
ms.topic: sample
ms.openlocfilehash: 3d0e7a9be37f9baf471584ee3fe26c52d13b3084
ms.sourcegitcommit: ea006cd8e62888271b2601d5ed4ec78fb40e8427
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/14/2020
ms.locfileid: "81381499"
---
# <a name="advanced-resource-graph-query-samples"></a>Gelişmiş Kaynak Grafiği sorgu örnekleri

Azure Kaynak Grafiği ile sorguları anlamanın il adımı, [Sorgu Dili](../concepts/query-language.md)’nin temel bir anlayışıdır. [Azure Veri Gezgini](/azure/data-explorer/data-explorer-overview)’ne önceden aşina değilseniz, aradığınız kaynaklar için isteklerin nasıl oluşturulacağını anlamak üzere temelleri gözden geçirmeniz önerilir.

Aşağıdaki gelişmiş sorguları inceleyeceğiz:

> [!div class="checklist"]
> - [Her kaynak türü için API sürümünü göster](#apiversion)
> - [Sanal makine ölçeği set kapasitesi ve boyutu alın](#vmss-capacity)
> - [Sütunları sonuçlardan kaldırma](#remove-column)
> - [Tüm etiket adlarını listele](#list-all-tags)
> - [Regex ile eşleşen sanal makineler](#vm-regex)
> - [Belirli yazma konumları ile Cosmos DB liste](#mvexpand-cosmosdb)
> - [Abonelik adı olan anahtar kasası](#join)
> - [SQL Veritabanlarını ve bunların esnek havuzlarını listele](#join-sql)
> - [Sanal makineleri ağ arabirimi ve genel IP ile listele](#join-vmpip)
> - [Kaynak grubunda belirli bir etiketi olan depolama hesaplarını bulma](#join-findstoragetag)
> - [İki sorgunun sonuçlarını tek bir sonuçta birleştirme](#unionresults)
> - [Görüntülü Adlarla kiracı ve abonelik adlarını ekleme](#displaynames)

Azure aboneliğiniz yoksa, başlamadan önce [ücretsiz](https://azure.microsoft.com/free) bir hesap oluşturun.

## <a name="language-support"></a>Dil desteği

Azure CLI (bir uzantı yoluyla) ve Azure PowerShell (bir modül yoluyla), Azure Kaynak Grafiği’ni destekler. Aşağıdaki sorgulardan herhangi birini çalıştırmadan önce ortamınızın hazır olduğundan emin olun. Seçtiğiniz kabuk ortamını yükleme ve doğrulama adımları için, bkz. [Azure CLI](../first-query-azurecli.md#add-the-resource-graph-extension) ve [Azure PowerShell](../first-query-powershell.md#add-the-resource-graph-module).

## <a name="show-resource-types-and-api-versions"></a><a name="apiversion" />Kaynak türlerini ve API sürümlerini göster

Kaynak Grafiği, bir güncelleştirme sırasında kaynak özellikleri için `GET` öncelikle kaynak sağlayıcısının API'sinin en son önizleme siz sürümünü kullanır. Bazı durumlarda, kullanılan API sürümü sonuçlarda daha güncel veya yaygın olarak kullanılan özellikleri sağlamak için geçersiz kılınmıştır. Aşağıdaki sorgu, her kaynak türünde özellikleri toplamak için kullanılan API sürümünü ayrıntıları:

```kusto
Resources
| distinct type, apiVersion
| where isnotnull(apiVersion)
| order by type asc
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az graph query -q "Resources | distinct type, apiVersion | where isnotnull(apiVersion) | order by type asc"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "Resources | distinct type, apiVersion | where isnotnull(apiVersion) | order by type asc"
```

# <a name="portal"></a>[Portal](#tab/azure-portal)

![Kaynak Grafiği gezgini simgesi](../media/resource-graph-small.png) Bu sorguyı Azure Kaynak Grafiği Gezgini'nde deneyin:

- Azure portalı: yeni pencere simgesinde bağlantıyı <a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20distinct%20type%2C%20apiVersion%20%7C%20where%20isnotnull(apiVersion)%20%7C%20order%20by%20type%20asc" target="_blank">aç portal.azure.com</a> ![](../../media/new-window.png)
- Azure Devlet portalı: yeni pencere simgesinde <a href="https://portal.azure.us/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20distinct%20type%2C%20apiVersion%20%7C%20where%20isnotnull(apiVersion)%20%7C%20order%20by%20type%20asc" target="_blank">bağlantıyı aç portal.azure.us</a> ![](../../media/new-window.png)
- Azure China portalı: <a href="https://portal.azure.cn/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20distinct%20type%2C%20apiVersion%20%7C%20where%20isnotnull(apiVersion)%20%7C%20order%20by%20type%20asc" target="_blank">portal.azure.cn</a> ![Yeni pencere simgesinde bağlantıyı aç](../../media/new-window.png)

---

## <a name="get-virtual-machine-scale-set-capacity-and-size"></a><a name="vmss-capacity" />Sanal makine ölçek kümesi kapasitesini ve boyutunu alma

Bu sorgu, sanal makine ölçek kümesi kaynaklarını arar ve sanal makine boyutu ve ölçek kümesinin kapasitesini içeren çeşitli ayrıntıları alır. Sorgu, sıralanabilmesi için ve kapasiteyi bir sayıya dönüştürmek amacıyla `toint()` işlevini kullanır. Son olarak sütunlar, özel olarak adlandırılmış özellikler kullanılarak yeniden adlandırılır.

```kusto
Resources
| where type=~ 'microsoft.compute/virtualmachinescalesets'
| where name contains 'contoso'
| project subscriptionId, name, location, resourceGroup, Capacity = toint(sku.capacity), Tier = sku.name
| order by Capacity desc
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az graph query -q "Resources | where type=~ 'microsoft.compute/virtualmachinescalesets' | where name contains 'contoso' | project subscriptionId, name, location, resourceGroup, Capacity = toint(sku.capacity), Tier = sku.name | order by Capacity desc"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where type=~ 'microsoft.compute/virtualmachinescalesets' | where name contains 'contoso' | project subscriptionId, name, location, resourceGroup, Capacity = toint(sku.capacity), Tier = sku.name | order by Capacity desc"
```

# <a name="portal"></a>[Portal](#tab/azure-portal)

![Kaynak Grafiği gezgini simgesi](../media/resource-graph-small.png) Bu sorguyı Azure Kaynak Grafiği Gezgini'nde deneyin:

- Azure portalı: yeni pencere simgesinde bağlantıyı <a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20where%20type%3D~%20'microsoft.compute%2Fvirtualmachinescalesets'%20%7C%20where%20name%20contains%20'contoso'%20%7C%20project%20subscriptionId%2C%20name%2C%20location%2C%20resourceGroup%2C%20Capacity%20%3D%20toint(sku.capacity)%2C%20Tier%20%3D%20sku.name%20%7C%20order%20by%20Capacity%20desc" target="_blank">aç portal.azure.com</a> ![](../../media/new-window.png)
- Azure Devlet portalı: yeni pencere simgesinde <a href="https://portal.azure.us/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20where%20type%3D~%20'microsoft.compute%2Fvirtualmachinescalesets'%20%7C%20where%20name%20contains%20'contoso'%20%7C%20project%20subscriptionId%2C%20name%2C%20location%2C%20resourceGroup%2C%20Capacity%20%3D%20toint(sku.capacity)%2C%20Tier%20%3D%20sku.name%20%7C%20order%20by%20Capacity%20desc" target="_blank">bağlantıyı aç portal.azure.us</a> ![](../../media/new-window.png)
- Azure China portalı: <a href="https://portal.azure.cn/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20where%20type%3D~%20'microsoft.compute%2Fvirtualmachinescalesets'%20%7C%20where%20name%20contains%20'contoso'%20%7C%20project%20subscriptionId%2C%20name%2C%20location%2C%20resourceGroup%2C%20Capacity%20%3D%20toint(sku.capacity)%2C%20Tier%20%3D%20sku.name%20%7C%20order%20by%20Capacity%20desc" target="_blank">portal.azure.cn</a> ![Yeni pencere simgesinde bağlantıyı aç](../../media/new-window.png)

---

## <a name="remove-columns-from-results"></a><a name="remove-column" />Sütunları sonuçlardan kaldırma

Aşağıdaki sorgu, `summarize` kaynakları aboneye `join` göre saymak, _Kaynak Kapsayıcıları_ tablosundaki abonelik ayrıntılarıyla birleştirmek ve sütunlardan `project-away` bazılarını kaldırmak için kullanılır.

```kusto
Resources
| summarize resourceCount=count() by subscriptionId
| join (ResourceContainers | where type=='microsoft.resources/subscriptions' | project SubName=name, subscriptionId) on subscriptionId
| project-away subscriptionId, subscriptionId1
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az graph query -q "Resources | summarize resourceCount=count() by subscriptionId | join (ResourceContainers | where type=='microsoft.resources/subscriptions' | project SubName=name, subscriptionId) on subscriptionId| project-away subscriptionId, subscriptionId1"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "Resources | summarize resourceCount=count() by subscriptionId | join (ResourceContainers | where type=='microsoft.resources/subscriptions' | project SubName=name, subscriptionId) on subscriptionId| project-away subscriptionId, subscriptionId1"
```

# <a name="portal"></a>[Portal](#tab/azure-portal)

![Kaynak Grafiği gezgini simgesi](../media/resource-graph-small.png) Bu sorguyı Azure Kaynak Grafiği Gezgini'nde deneyin:

- Azure portalı: yeni pencere simgesinde bağlantıyı <a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20summarize%20resourceCount%3Dcount()%20by%20subscriptionId%20%7C%20join%20(ResourceContainers%20%7C%20where%20type%3D%3D'microsoft.resources%2Fsubscriptions'%20%7C%20project%20SubName%3Dname%2C%20subscriptionId)%20on%20subscriptionId%7C%20project-away%20subscriptionId%2C%20subscriptionId1" target="_blank">aç portal.azure.com</a> ![](../../media/new-window.png)
- Azure Devlet portalı: yeni pencere simgesinde <a href="https://portal.azure.us/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20summarize%20resourceCount%3Dcount()%20by%20subscriptionId%20%7C%20join%20(ResourceContainers%20%7C%20where%20type%3D%3D'microsoft.resources%2Fsubscriptions'%20%7C%20project%20SubName%3Dname%2C%20subscriptionId)%20on%20subscriptionId%7C%20project-away%20subscriptionId%2C%20subscriptionId1" target="_blank">bağlantıyı aç portal.azure.us</a> ![](../../media/new-window.png)
- Azure China portalı: <a href="https://portal.azure.cn/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20summarize%20resourceCount%3Dcount()%20by%20subscriptionId%20%7C%20join%20(ResourceContainers%20%7C%20where%20type%3D%3D'microsoft.resources%2Fsubscriptions'%20%7C%20project%20SubName%3Dname%2C%20subscriptionId)%20on%20subscriptionId%7C%20project-away%20subscriptionId%2C%20subscriptionId1" target="_blank">portal.azure.cn</a> ![Yeni pencere simgesinde bağlantıyı aç](../../media/new-window.png)

---

## <a name="list-all-tag-names"></a><a name="list-all-tags" />Tüm etiket adlarını listeleme

Bu sorgu etiketle başlar ve tüm benzersiz etiket adlarını ve bunlara karşılık gelen türleri listeleyen bir JSON nesnesi oluşturur.

```kusto
Resources
| project tags
| summarize buildschema(tags)
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az graph query -q "Resources | project tags | summarize buildschema(tags)"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "Resources | project tags | summarize buildschema(tags)"
```

# <a name="portal"></a>[Portal](#tab/azure-portal)

![Kaynak Grafiği gezgini simgesi](../media/resource-graph-small.png) Bu sorguyı Azure Kaynak Grafiği Gezgini'nde deneyin:

- Azure portalı: yeni pencere simgesinde bağlantıyı <a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20project%20tags%20%7C%20summarize%20buildschema(tags)" target="_blank">aç portal.azure.com</a> ![](../../media/new-window.png)
- Azure Devlet portalı: yeni pencere simgesinde <a href="https://portal.azure.us/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20project%20tags%20%7C%20summarize%20buildschema(tags)" target="_blank">bağlantıyı aç portal.azure.us</a> ![](../../media/new-window.png)
- Azure China portalı: <a href="https://portal.azure.cn/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20project%20tags%20%7C%20summarize%20buildschema(tags)" target="_blank">portal.azure.cn</a> ![Yeni pencere simgesinde bağlantıyı aç](../../media/new-window.png)

---

## <a name="virtual-machines-matched-by-regex"></a><a name="vm-regex" />Normal ifade tarafından eşleştirilen sanal makineler

Bu sorgu, [normal ifadeyle](/dotnet/standard/base-types/regular-expression-language-quick-reference) (_regex_ olarak bilinir) eşleşen sanal makineleri arar. **Maçlar regex \@ ** bize maç için regex tanımlamak `^Contoso(.*)[0-9]+$`için izin verir, hangi .
Bu normal ifade tanımı şöyle açıklanmıştır:

- `^` - Eşleşme dizenin başında başlamalıdır.
- `Contoso` - Büyük/küçük harfe duyarlı dize.
- `(.*)` - Bir alt ifade eşleşmesi:
  - `.` - Herhangi bir tek karakterle eşleşir (yeni bir satır hariç).
  - `*` - Önceki öğeyle sıfır veya daha fazla kez eşleşir.
- `[0-9]` - 0’dan 9’a kadar olan sayılar için karakter grubu eşleşmesi.
- `+` - Önceki öğeyle bir veya daha fazla kez eşleşir.
- `$` - Önceki öğenin eşleşmesi dizenin sonunda gerçekleşmelidir.

Ada göre eşleşmeden sonra sorgu, adı ve siparişleri ada göre artan şekilde yansıtır.

```kusto
Resources
| where type =~ 'microsoft.compute/virtualmachines' and name matches regex @'^Contoso(.*)[0-9]+$'
| project name
| order by name asc
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az graph query -q "Resources | where type =~ 'microsoft.compute/virtualmachines' and name matches regex @'^Contoso(.*)[0-9]+$' | project name | order by name asc"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where type =~ 'microsoft.compute/virtualmachines' and name matches regex @'^Contoso(.*)[0-9]+$' | project name | order by name asc"
```

# <a name="portal"></a>[Portal](#tab/azure-portal)

![Kaynak Grafiği gezgini simgesi](../media/resource-graph-small.png) Bu sorguyı Azure Kaynak Grafiği Gezgini'nde deneyin:

- Azure portalı: yeni pencere simgesinde bağlantıyı <a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20where%20type%20%3D~%20'microsoft.compute%2Fvirtualmachines'%20and%20name%20matches%20regex%20%40'%5EContoso(.*)%5B0-9%5D%2B%24'%20%7C%20project%20name%20%7C%20order%20by%20name%20asc" target="_blank">aç portal.azure.com</a> ![](../../media/new-window.png)
- Azure Devlet portalı: yeni pencere simgesinde <a href="https://portal.azure.us/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20where%20type%20%3D~%20'microsoft.compute%2Fvirtualmachines'%20and%20name%20matches%20regex%20%40'%5EContoso(.*)%5B0-9%5D%2B%24'%20%7C%20project%20name%20%7C%20order%20by%20name%20asc" target="_blank">bağlantıyı aç portal.azure.us</a> ![](../../media/new-window.png)
- Azure China portalı: <a href="https://portal.azure.cn/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20where%20type%20%3D~%20'microsoft.compute%2Fvirtualmachines'%20and%20name%20matches%20regex%20%40'%5EContoso(.*)%5B0-9%5D%2B%24'%20%7C%20project%20name%20%7C%20order%20by%20name%20asc" target="_blank">portal.azure.cn</a> ![Yeni pencere simgesinde bağlantıyı aç](../../media/new-window.png)

---

## <a name="list-cosmos-db-with-specific-write-locations"></a><a name="mvexpand-cosmosdb" />Belirli yazma konumları ile Cosmos DB liste

Aşağıdaki sorgu Cosmos DB kaynakları `mv-expand` ile sınırlar, **properties.writeLocations**için özellik çantasını genişletmek için kullanır, sonra belirli alanları proje ve **properties.writeLocations.locationAd** değerleri 'Doğu ABD' veya 'Batı ABD' eşleşen daha fazla sınırlamak.

```kusto
Resources
| where type =~ 'microsoft.documentdb/databaseaccounts'
| project id, name, writeLocations = (properties.writeLocations)
| mv-expand writeLocations
| project id, name, writeLocation = tostring(writeLocations.locationName)
| where writeLocation in ('East US', 'West US')
| summarize by id, name
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az graph query -q "Resources | where type =~ 'microsoft.documentdb/databaseaccounts' | project id, name, writeLocations = (properties.writeLocations) | mv-expand writeLocations | project id, name, writeLocation = tostring(writeLocations.locationName) | where writeLocation in ('East US', 'West US') | summarize by id, name"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where type =~ 'microsoft.documentdb/databaseaccounts' | project id, name, writeLocations = (properties.writeLocations) | mv-expand writeLocations | project id, name, writeLocation = tostring(writeLocations.locationName) | where writeLocation in ('East US', 'West US') | summarize by id, name"
```

# <a name="portal"></a>[Portal](#tab/azure-portal)

![Kaynak Grafiği gezgini simgesi](../media/resource-graph-small.png) Bu sorguyı Azure Kaynak Grafiği Gezgini'nde deneyin:

- Azure portalı: yeni pencere simgesinde bağlantıyı <a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20where%20type%20%3D~%20'microsoft.documentdb%2Fdatabaseaccounts'%20%7C%20project%20id%2C%20name%2C%20writeLocations%20%3D%20(properties.writeLocations)%20%7C%20mv-expand%20writeLocations%20%7C%20project%20id%2C%20name%2C%20writeLocation%20%3D%20tostring(writeLocations.locationName)%20%7C%20where%20writeLocation%20in%20('East%20US'%2C%20'West%20US')%20%7C%20summarize%20by%20id%2C%20name" target="_blank">aç portal.azure.com</a> ![](../../media/new-window.png)
- Azure Devlet portalı: yeni pencere simgesinde <a href="https://portal.azure.us/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20where%20type%20%3D~%20'microsoft.documentdb%2Fdatabaseaccounts'%20%7C%20project%20id%2C%20name%2C%20writeLocations%20%3D%20(properties.writeLocations)%20%7C%20mv-expand%20writeLocations%20%7C%20project%20id%2C%20name%2C%20writeLocation%20%3D%20tostring(writeLocations.locationName)%20%7C%20where%20writeLocation%20in%20('East%20US'%2C%20'West%20US')%20%7C%20summarize%20by%20id%2C%20name" target="_blank">bağlantıyı aç portal.azure.us</a> ![](../../media/new-window.png)
- Azure China portalı: <a href="https://portal.azure.cn/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20where%20type%20%3D~%20'microsoft.documentdb%2Fdatabaseaccounts'%20%7C%20project%20id%2C%20name%2C%20writeLocations%20%3D%20(properties.writeLocations)%20%7C%20mv-expand%20writeLocations%20%7C%20project%20id%2C%20name%2C%20writeLocation%20%3D%20tostring(writeLocations.locationName)%20%7C%20where%20writeLocation%20in%20('East%20US'%2C%20'West%20US')%20%7C%20summarize%20by%20id%2C%20name" target="_blank">portal.azure.cn</a> ![Yeni pencere simgesinde bağlantıyı aç](../../media/new-window.png)

---

## <a name="key-vault-with-subscription-name"></a><a name="join" />Abonelik adı olan anahtar kasası

Aşağıdaki sorgu karmaşık bir `join`kullanımını gösterir. Sorgu, birleştirilmiş tabloyu abonelik kaynaklarıyla `project` ve yalnızca özgün alan _aboneliğiId'i_ ve _Alt Ad_olarak yeniden adlandırılan _ad_ alanını içerecek şekilde sınırlar. Alan yeniden adlandırma, `join` _alan kaynaklarda_zaten var _olduğundan, onu ad1_ olarak eklemeyi önler. Özgün tablo ile `where` filtrelenir ve `project` aşağıdaki her iki tablodan sütunlar içerir. Sorgu sonucu, türünü, anahtar kasasının adını ve içinde olduğu aboneliğin adını gösteren tek bir anahtar kasasıdır.

```kusto
Resources
| join (ResourceContainers | where type=='microsoft.resources/subscriptions' | project SubName=name, subscriptionId) on subscriptionId
| where type == 'microsoft.keyvault/vaults'
| project type, name, SubName
| limit 1
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az graph query -q "Resources | join (ResourceContainers | where type=='microsoft.resources/subscriptions' | project SubName=name, subscriptionId) on subscriptionId | where type == 'microsoft.keyvault/vaults' | project type, name, SubName| limit 1"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "Resources | join (ResourceContainers | where type=='microsoft.resources/subscriptions' | project SubName=name, subscriptionId) on subscriptionId | where type == 'microsoft.keyvault/vaults' | project type, name, SubName| limit 1"
```

# <a name="portal"></a>[Portal](#tab/azure-portal)

![Kaynak Grafiği gezgini simgesi](../media/resource-graph-small.png) Bu sorguyı Azure Kaynak Grafiği Gezgini'nde deneyin:

- Azure portalı: yeni pencere simgesinde bağlantıyı <a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20join%20(ResourceContainers%20%7C%20where%20type%3D%3D'microsoft.resources%2Fsubscriptions'%20%7C%20project%20SubName%3Dname%2C%20subscriptionId)%20on%20subscriptionId%20%7C%20where%20type%20%3D%3D%20'microsoft.keyvault%2Fvaults'%20%7C%20project%20type%2C%20name%2C%20SubName%7C%20limit%201" target="_blank">aç portal.azure.com</a> ![](../../media/new-window.png)
- Azure Devlet portalı: yeni pencere simgesinde <a href="https://portal.azure.us/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20join%20(ResourceContainers%20%7C%20where%20type%3D%3D'microsoft.resources%2Fsubscriptions'%20%7C%20project%20SubName%3Dname%2C%20subscriptionId)%20on%20subscriptionId%20%7C%20where%20type%20%3D%3D%20'microsoft.keyvault%2Fvaults'%20%7C%20project%20type%2C%20name%2C%20SubName%7C%20limit%201" target="_blank">bağlantıyı aç portal.azure.us</a> ![](../../media/new-window.png)
- Azure China portalı: <a href="https://portal.azure.cn/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20join%20(ResourceContainers%20%7C%20where%20type%3D%3D'microsoft.resources%2Fsubscriptions'%20%7C%20project%20SubName%3Dname%2C%20subscriptionId)%20on%20subscriptionId%20%7C%20where%20type%20%3D%3D%20'microsoft.keyvault%2Fvaults'%20%7C%20project%20type%2C%20name%2C%20SubName%7C%20limit%201" target="_blank">portal.azure.cn</a> ![Yeni pencere simgesinde bağlantıyı aç](../../media/new-window.png)

---

## <a name="list-sql-databases-and-their-elastic-pools"></a><a name="join-sql" />SQL Veritabanlarını ve bunların esnek havuzlarını listele

Aşağıdaki sorgu, varsa SQL Veritabanı kaynaklarını ve bunların ilgili elastik havuzlarını bir araya getirmek için **leftouter'ı** `join` kullanır.

```kusto
Resources
| where type =~ 'microsoft.sql/servers/databases'
| project databaseId = id, databaseName = name, elasticPoolId = tolower(tostring(properties.elasticPoolId))
| join kind=leftouter (
    Resources
    | where type =~ 'microsoft.sql/servers/elasticpools'
    | project elasticPoolId = tolower(id), elasticPoolName = name, elasticPoolState = properties.state)
on elasticPoolId
| project-away elasticPoolId1
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az graph query -q "Resources | where type =~ 'microsoft.sql/servers/databases' | project databaseId = id, databaseName = name, elasticPoolId = tolower(tostring(properties.elasticPoolId)) | join kind=leftouter ( Resources | where type =~ 'microsoft.sql/servers/elasticpools' | project elasticPoolId = tolower(id), elasticPoolName = name, elasticPoolState = properties.state) on elasticPoolId | project-away elasticPoolId1"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where type =~ 'microsoft.sql/servers/databases' | project databaseId = id, databaseName = name, elasticPoolId = tolower(tostring(properties.elasticPoolId)) | join kind=leftouter ( Resources | where type =~ 'microsoft.sql/servers/elasticpools' | project elasticPoolId = tolower(id), elasticPoolName = name, elasticPoolState = properties.state) on elasticPoolId | project-away elasticPoolId1"
```

# <a name="portal"></a>[Portal](#tab/azure-portal)

![Kaynak Grafiği gezgini simgesi](../media/resource-graph-small.png) Bu sorguyı Azure Kaynak Grafiği Gezgini'nde deneyin:

- Azure portalı: yeni pencere simgesinde bağlantıyı <a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20where%20type%20%3D~%20'microsoft.sql%2Fservers%2Fdatabases'%20%7C%20project%20databaseId%20%3D%20id%2C%20databaseName%20%3D%20name%2C%20elasticPoolId%20%3D%20tolower(tostring(properties.elasticPoolId))%20%7C%20join%20kind%3Dleftouter%20(%20Resources%20%7C%20where%20type%20%3D~%20'microsoft.sql%2Fservers%2Felasticpools'%20%7C%20project%20elasticPoolId%20%3D%20tolower(id)%2C%20elasticPoolName%20%3D%20name%2C%20elasticPoolState%20%3D%20properties.state)%20on%20elasticPoolId%20%7C%20project-away%20elasticPoolId1" target="_blank">aç portal.azure.com</a> ![](../../media/new-window.png)
- Azure Devlet portalı: yeni pencere simgesinde <a href="https://portal.azure.us/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20where%20type%20%3D~%20'microsoft.sql%2Fservers%2Fdatabases'%20%7C%20project%20databaseId%20%3D%20id%2C%20databaseName%20%3D%20name%2C%20elasticPoolId%20%3D%20tolower(tostring(properties.elasticPoolId))%20%7C%20join%20kind%3Dleftouter%20(%20Resources%20%7C%20where%20type%20%3D~%20'microsoft.sql%2Fservers%2Felasticpools'%20%7C%20project%20elasticPoolId%20%3D%20tolower(id)%2C%20elasticPoolName%20%3D%20name%2C%20elasticPoolState%20%3D%20properties.state)%20on%20elasticPoolId%20%7C%20project-away%20elasticPoolId1" target="_blank">bağlantıyı aç portal.azure.us</a> ![](../../media/new-window.png)
- Azure China portalı: <a href="https://portal.azure.cn/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20where%20type%20%3D~%20'microsoft.sql%2Fservers%2Fdatabases'%20%7C%20project%20databaseId%20%3D%20id%2C%20databaseName%20%3D%20name%2C%20elasticPoolId%20%3D%20tolower(tostring(properties.elasticPoolId))%20%7C%20join%20kind%3Dleftouter%20(%20Resources%20%7C%20where%20type%20%3D~%20'microsoft.sql%2Fservers%2Felasticpools'%20%7C%20project%20elasticPoolId%20%3D%20tolower(id)%2C%20elasticPoolName%20%3D%20name%2C%20elasticPoolState%20%3D%20properties.state)%20on%20elasticPoolId%20%7C%20project-away%20elasticPoolId1" target="_blank">portal.azure.cn</a> ![Yeni pencere simgesinde bağlantıyı aç](../../media/new-window.png)

---

## <a name="list-virtual-machines-with-their-network-interface-and-public-ip"></a><a name="join-vmpip" />Sanal makineleri ağ arabirimi ve genel IP ile listele

Bu sorgu, sanal makineleri, ilgili ağ arabirimlerini ve bu ağ arabirimleriyle ilgili tüm genel IP adreslerini bir araya getirmek için iki **leftouter** `join` komutu kullanır.

```kusto
Resources
| where type =~ 'microsoft.compute/virtualmachines'
| extend nics=array_length(properties.networkProfile.networkInterfaces) 
| mv-expand nic=properties.networkProfile.networkInterfaces 
| where nics == 1 or nic.properties.primary =~ 'true' or isempty(nic) 
| project vmId = id, vmName = name, vmSize=tostring(properties.hardwareProfile.vmSize), nicId = tostring(nic.id) 
| join kind=leftouter (
    Resources
    | where type =~ 'microsoft.network/networkinterfaces'
    | extend ipConfigsCount=array_length(properties.ipConfigurations) 
    | mv-expand ipconfig=properties.ipConfigurations 
    | where ipConfigsCount == 1 or ipconfig.properties.primary =~ 'true'
    | project nicId = id, publicIpId = tostring(ipconfig.properties.publicIPAddress.id))
on nicId
| project-away nicId1
| summarize by vmId, vmName, vmSize, nicId, publicIpId
| join kind=leftouter (
    Resources
    | where type =~ 'microsoft.network/publicipaddresses'
    | project publicIpId = id, publicIpAddress = properties.ipAddress)
on publicIpId
| project-away publicIpId1
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az graph query -q "Resources | where type =~ 'microsoft.compute/virtualmachines' | extend nics=array_length(properties.networkProfile.networkInterfaces) | mv-expand nic=properties.networkProfile.networkInterfaces | where nics == 1 or nic.properties.primary =~ 'true' or isempty(nic) | project vmId = id, vmName = name, vmSize=tostring(properties.hardwareProfile.vmSize), nicId = tostring(nic.id) | join kind=leftouter ( Resources | where type =~ 'microsoft.network/networkinterfaces' | extend ipConfigsCount=array_length(properties.ipConfigurations) | mv-expand ipconfig=properties.ipConfigurations | where ipConfigsCount == 1 or ipconfig.properties.primary =~ 'true' | project nicId = id, publicIpId = tostring(ipconfig.properties.publicIPAddress.id)) on nicId | project-away nicId1 | summarize by vmId, vmName, vmSize, nicId, publicIpId | join kind=leftouter ( Resources | where type =~ 'microsoft.network/publicipaddresses' | project publicIpId = id, publicIpAddress = properties.ipAddress) on publicIpId | project-away publicIpId1"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where type =~ 'microsoft.compute/virtualmachines' | extend nics=array_length(properties.networkProfile.networkInterfaces) | mv-expand nic=properties.networkProfile.networkInterfaces | where nics == 1 or nic.properties.primary =~ 'true' or isempty(nic) | project vmId = id, vmName = name, vmSize=tostring(properties.hardwareProfile.vmSize), nicId = tostring(nic.id) | join kind=leftouter ( Resources | where type =~ 'microsoft.network/networkinterfaces' | extend ipConfigsCount=array_length(properties.ipConfigurations) | mv-expand ipconfig=properties.ipConfigurations | where ipConfigsCount == 1 or ipconfig.properties.primary =~ 'true' | project nicId = id, publicIpId = tostring(ipconfig.properties.publicIPAddress.id)) on nicId | project-away nicId1 | summarize by vmId, vmName, vmSize, nicId, publicIpId | join kind=leftouter ( Resources | where type =~ 'microsoft.network/publicipaddresses' | project publicIpId = id, publicIpAddress = properties.ipAddress) on publicIpId | project-away publicIpId1"
```

# <a name="portal"></a>[Portal](#tab/azure-portal)

![Kaynak Grafiği gezgini simgesi](../media/resource-graph-small.png) Bu sorguyı Azure Kaynak Grafiği Gezgini'nde deneyin:

- Azure portalı: yeni pencere simgesinde bağlantıyı <a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20where%20type%20%3D~%20'microsoft.compute%2Fvirtualmachines'%20%7C%20extend%20nics%3Darray_length(properties.networkProfile.networkInterfaces)%20%7C%20mv-expand%20nic%3Dproperties.networkProfile.networkInterfaces%20%7C%20where%20nics%20%3D%3D%201%20or%20nic.properties.primary%20%3D~%20'true'%20or%20isempty(nic)%20%7C%20project%20vmId%20%3D%20id%2C%20vmName%20%3D%20name%2C%20vmSize%3Dtostring(properties.hardwareProfile.vmSize)%2C%20nicId%20%3D%20tostring(nic.id)%20%7C%20join%20kind%3Dleftouter%20(%20Resources%20%7C%20where%20type%20%3D~%20'microsoft.network%2Fnetworkinterfaces'%20%7C%20extend%20ipConfigsCount%3Darray_length(properties.ipConfigurations)%20%7C%20mv-expand%20ipconfig%3Dproperties.ipConfigurations%20%7C%20where%20ipConfigsCount%20%3D%3D%201%20or%20ipconfig.properties.primary%20%3D~%20'true'%20%7C%20project%20nicId%20%3D%20id%2C%20publicIpId%20%3D%20tostring(ipconfig.properties.publicIPAddress.id))%20on%20nicId%20%7C%20project-away%20nicId1%20%7C%20summarize%20by%20vmId%2C%20vmName%2C%20vmSize%2C%20nicId%2C%20publicIpId%20%7C%20join%20kind%3Dleftouter%20(%20Resources%20%7C%20where%20type%20%3D~%20'microsoft.network%2Fpublicipaddresses'%20%7C%20project%20publicIpId%20%3D%20id%2C%20publicIpAddress%20%3D%20properties.ipAddress)%20on%20publicIpId%20%7C%20project-away%20publicIpId1" target="_blank">aç portal.azure.com</a> ![](../../media/new-window.png)
- Azure Devlet portalı: yeni pencere simgesinde <a href="https://portal.azure.us/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20where%20type%20%3D~%20'microsoft.compute%2Fvirtualmachines'%20%7C%20extend%20nics%3Darray_length(properties.networkProfile.networkInterfaces)%20%7C%20mv-expand%20nic%3Dproperties.networkProfile.networkInterfaces%20%7C%20where%20nics%20%3D%3D%201%20or%20nic.properties.primary%20%3D~%20'true'%20or%20isempty(nic)%20%7C%20project%20vmId%20%3D%20id%2C%20vmName%20%3D%20name%2C%20vmSize%3Dtostring(properties.hardwareProfile.vmSize)%2C%20nicId%20%3D%20tostring(nic.id)%20%7C%20join%20kind%3Dleftouter%20(%20Resources%20%7C%20where%20type%20%3D~%20'microsoft.network%2Fnetworkinterfaces'%20%7C%20extend%20ipConfigsCount%3Darray_length(properties.ipConfigurations)%20%7C%20mv-expand%20ipconfig%3Dproperties.ipConfigurations%20%7C%20where%20ipConfigsCount%20%3D%3D%201%20or%20ipconfig.properties.primary%20%3D~%20'true'%20%7C%20project%20nicId%20%3D%20id%2C%20publicIpId%20%3D%20tostring(ipconfig.properties.publicIPAddress.id))%20on%20nicId%20%7C%20project-away%20nicId1%20%7C%20summarize%20by%20vmId%2C%20vmName%2C%20vmSize%2C%20nicId%2C%20publicIpId%20%7C%20join%20kind%3Dleftouter%20(%20Resources%20%7C%20where%20type%20%3D~%20'microsoft.network%2Fpublicipaddresses'%20%7C%20project%20publicIpId%20%3D%20id%2C%20publicIpAddress%20%3D%20properties.ipAddress)%20on%20publicIpId%20%7C%20project-away%20publicIpId1" target="_blank">bağlantıyı aç portal.azure.us</a> ![](../../media/new-window.png)
- Azure China portalı: <a href="https://portal.azure.cn/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20where%20type%20%3D~%20'microsoft.compute%2Fvirtualmachines'%20%7C%20extend%20nics%3Darray_length(properties.networkProfile.networkInterfaces)%20%7C%20mv-expand%20nic%3Dproperties.networkProfile.networkInterfaces%20%7C%20where%20nics%20%3D%3D%201%20or%20nic.properties.primary%20%3D~%20'true'%20or%20isempty(nic)%20%7C%20project%20vmId%20%3D%20id%2C%20vmName%20%3D%20name%2C%20vmSize%3Dtostring(properties.hardwareProfile.vmSize)%2C%20nicId%20%3D%20tostring(nic.id)%20%7C%20join%20kind%3Dleftouter%20(%20Resources%20%7C%20where%20type%20%3D~%20'microsoft.network%2Fnetworkinterfaces'%20%7C%20extend%20ipConfigsCount%3Darray_length(properties.ipConfigurations)%20%7C%20mv-expand%20ipconfig%3Dproperties.ipConfigurations%20%7C%20where%20ipConfigsCount%20%3D%3D%201%20or%20ipconfig.properties.primary%20%3D~%20'true'%20%7C%20project%20nicId%20%3D%20id%2C%20publicIpId%20%3D%20tostring(ipconfig.properties.publicIPAddress.id))%20on%20nicId%20%7C%20project-away%20nicId1%20%7C%20summarize%20by%20vmId%2C%20vmName%2C%20vmSize%2C%20nicId%2C%20publicIpId%20%7C%20join%20kind%3Dleftouter%20(%20Resources%20%7C%20where%20type%20%3D~%20'microsoft.network%2Fpublicipaddresses'%20%7C%20project%20publicIpId%20%3D%20id%2C%20publicIpAddress%20%3D%20properties.ipAddress)%20on%20publicIpId%20%7C%20project-away%20publicIpId1" target="_blank">portal.azure.cn</a> ![Yeni pencere simgesinde bağlantıyı aç](../../media/new-window.png)

---

## <a name="find-storage-accounts-with-a-specific-tag-on-the-resource-group"></a><a name="join-findstoragetag" />Kaynak grubunda belirli bir etiketi olan depolama hesaplarını bulma

Aşağıdaki sorgu, depolama hesaplarını belirli bir büyük/küçük harf duyarlı etiket adı ve etiket değerine sahip kaynak gruplarına bağlamak için bir **iç** `join` kullanır.

```kusto
Resources
| where type =~ 'microsoft.storage/storageaccounts'
| join kind=inner (
    ResourceContainers
    | where type =~ 'microsoft.resources/subscriptions/resourcegroups'
    | where tags['Key1'] =~ 'Value1'
    | project subscriptionId, resourceGroup)
on subscriptionId, resourceGroup
| project-away subscriptionId1, resourceGroup1
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az graph query -q "Resources | where type =~ 'microsoft.storage/storageaccounts' | join kind=inner ( ResourceContainers | where type =~ 'microsoft.resources/subscriptions/resourcegroups' | where tags['Key1'] =~ 'Value1' | project subscriptionId, resourceGroup) on subscriptionId, resourceGroup | project-away subscriptionId1, resourceGroup1"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where type =~ 'microsoft.storage/storageaccounts' | join kind=inner ( ResourceContainers | where type =~ 'microsoft.resources/subscriptions/resourcegroups' | where tags['Key1'] =~ 'Value1' | project subscriptionId, resourceGroup) on subscriptionId, resourceGroup | project-away subscriptionId1, resourceGroup1"
```

# <a name="portal"></a>[Portal](#tab/azure-portal)

![Kaynak Grafiği gezgini simgesi](../media/resource-graph-small.png) Bu sorguyı Azure Kaynak Grafiği Gezgini'nde deneyin:

- Azure portalı: yeni pencere simgesinde bağlantıyı <a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20where%20type%20%3D~%20'microsoft.storage%2Fstorageaccounts'%20%7C%20join%20kind%3Dinner%20(%20ResourceContainers%20%7C%20where%20type%20%3D~%20'microsoft.resources%2Fsubscriptions%2Fresourcegroups'%20%7C%20where%20tags%5B'Key1'%5D%20%3D~%20'Value1'%20%7C%20project%20subscriptionId%2C%20resourceGroup)%20on%20subscriptionId%2C%20resourceGroup%20%7C%20project-away%20subscriptionId1%2C%20resourceGroup1" target="_blank">aç portal.azure.com</a> ![](../../media/new-window.png)
- Azure Devlet portalı: yeni pencere simgesinde <a href="https://portal.azure.us/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20where%20type%20%3D~%20'microsoft.storage%2Fstorageaccounts'%20%7C%20join%20kind%3Dinner%20(%20ResourceContainers%20%7C%20where%20type%20%3D~%20'microsoft.resources%2Fsubscriptions%2Fresourcegroups'%20%7C%20where%20tags%5B'Key1'%5D%20%3D~%20'Value1'%20%7C%20project%20subscriptionId%2C%20resourceGroup)%20on%20subscriptionId%2C%20resourceGroup%20%7C%20project-away%20subscriptionId1%2C%20resourceGroup1" target="_blank">bağlantıyı aç portal.azure.us</a> ![](../../media/new-window.png)
- Azure China portalı: <a href="https://portal.azure.cn/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20where%20type%20%3D~%20'microsoft.storage%2Fstorageaccounts'%20%7C%20join%20kind%3Dinner%20(%20ResourceContainers%20%7C%20where%20type%20%3D~%20'microsoft.resources%2Fsubscriptions%2Fresourcegroups'%20%7C%20where%20tags%5B'Key1'%5D%20%3D~%20'Value1'%20%7C%20project%20subscriptionId%2C%20resourceGroup)%20on%20subscriptionId%2C%20resourceGroup%20%7C%20project-away%20subscriptionId1%2C%20resourceGroup1" target="_blank">portal.azure.cn</a> ![Yeni pencere simgesinde bağlantıyı aç](../../media/new-window.png)

---

Bir durumda duyarsız etiket adı ve etiket değeri aramak gerekirse, `mv-expand` **bagexpansion** parametresi ile kullanın. Bu sorgu önceki sorgudan daha fazla `mv-expand` kota kullanır, bu nedenle yalnızca gerekirse kullanın.

```kusto
Resources
| where type =~ 'microsoft.storage/storageaccounts'
| join kind=inner (
    ResourceContainers
    | where type =~ 'microsoft.resources/subscriptions/resourcegroups'
    | mv-expand bagexpansion=array tags
    | where isnotempty(tags)
    | where tags[0] =~ 'key1' and tags[1] =~ 'value1'
    | project subscriptionId, resourceGroup)
on subscriptionId, resourceGroup
| project-away subscriptionId1, resourceGroup1
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az graph query -q "Resources | where type =~ 'microsoft.storage/storageaccounts' | join kind=inner ( ResourceContainers | where type =~ 'microsoft.resources/subscriptions/resourcegroups' | mv-expand bagexpansion=array tags | where isnotempty(tags) | where tags[0] =~ 'key1' and tags[1] =~ 'value1' | project subscriptionId, resourceGroup) on subscriptionId, resourceGroup | project-away subscriptionId1, resourceGroup1"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where type =~ 'microsoft.storage/storageaccounts' | join kind=inner ( ResourceContainers | where type =~ 'microsoft.resources/subscriptions/resourcegroups' | mv-expand bagexpansion=array tags | where isnotempty(tags) | where tags[0] =~ 'key1' and tags[1] =~ 'value1' | project subscriptionId, resourceGroup) on subscriptionId, resourceGroup | project-away subscriptionId1, resourceGroup1"
```

# <a name="portal"></a>[Portal](#tab/azure-portal)

![Kaynak Grafiği gezgini simgesi](../media/resource-graph-small.png) Bu sorguyı Azure Kaynak Grafiği Gezgini'nde deneyin:

- Azure portalı: yeni pencere simgesinde bağlantıyı <a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20where%20type%20%3D~%20%27microsoft.storage%2Fstorageaccounts%27%20%7C%20join%20kind%3Dinner%20%28%20ResourceContainers%20%7C%20where%20type%20%3D~%20%27microsoft.resources%2Fsubscriptions%2Fresourcegroups%27%20%7C%20mv-expand%20bagexpansion%3Darray%20tags%20%7C%20where%20isnotempty%28tags%29%20%7C%20where%20tags%5B0%5D%20%3D~%20%27key1%27%20and%20tags%5B1%5D%20%3D~%20%27value1%27%20%7C%20project%20subscriptionId%2C%20resourceGroup%29%20on%20subscriptionId%2C%20resourceGroup%20%7C%20project-away%20subscriptionId1%2C%20resourceGroup1" target="_blank">aç portal.azure.com</a> ![](../../media/new-window.png)
- Azure Devlet portalı: yeni pencere simgesinde <a href="https://portal.azure.us/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20where%20type%20%3D~%20%27microsoft.storage%2Fstorageaccounts%27%20%7C%20join%20kind%3Dinner%20%28%20ResourceContainers%20%7C%20where%20type%20%3D~%20%27microsoft.resources%2Fsubscriptions%2Fresourcegroups%27%20%7C%20mv-expand%20bagexpansion%3Darray%20tags%20%7C%20where%20isnotempty%28tags%29%20%7C%20where%20tags%5B0%5D%20%3D~%20%27key1%27%20and%20tags%5B1%5D%20%3D~%20%27value1%27%20%7C%20project%20subscriptionId%2C%20resourceGroup%29%20on%20subscriptionId%2C%20resourceGroup%20%7C%20project-away%20subscriptionId1%2C%20resourceGroup1" target="_blank">bağlantıyı aç portal.azure.us</a> ![](../../media/new-window.png)
- Azure China portalı: <a href="https://portal.azure.cn/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20where%20type%20%3D~%20%27microsoft.storage%2Fstorageaccounts%27%20%7C%20join%20kind%3Dinner%20%28%20ResourceContainers%20%7C%20where%20type%20%3D~%20%27microsoft.resources%2Fsubscriptions%2Fresourcegroups%27%20%7C%20mv-expand%20bagexpansion%3Darray%20tags%20%7C%20where%20isnotempty%28tags%29%20%7C%20where%20tags%5B0%5D%20%3D~%20%27key1%27%20and%20tags%5B1%5D%20%3D~%20%27value1%27%20%7C%20project%20subscriptionId%2C%20resourceGroup%29%20on%20subscriptionId%2C%20resourceGroup%20%7C%20project-away%20subscriptionId1%2C%20resourceGroup1" target="_blank">portal.azure.cn</a> ![Yeni pencere simgesinde bağlantıyı aç](../../media/new-window.png)

---

## <a name="combine-results-from-two-queries-into-a-single-result"></a><a name="unionresults" />İki sorgunun sonuçlarını tek bir sonuçta birleştirme

Aşağıdaki sorgu, `union` _Kaynak Kapsayıcıları_ tablosundan sonuçları almak ve bunları _Kaynaklar_ tablosundaki sonuçlara eklemek için kullanılır.

```kusto
ResourceContainers
| where type=='microsoft.resources/subscriptions/resourcegroups' | project name, type  | limit 5
| union  (Resources | project name, type | limit 5)
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az graph query -q "ResourceContainers | where type=='microsoft.resources/subscriptions/resourcegroups' | project name, type  | limit 5 | union  (Resources | project name, type | limit 5)"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "ResourceContainers | where type=='microsoft.resources/subscriptions/resourcegroups' | project name, type  | limit 5 | union  (Resources | project name, type | limit 5)"
```

# <a name="portal"></a>[Portal](#tab/azure-portal)

![Kaynak Grafiği gezgini simgesi](../media/resource-graph-small.png) Bu sorguyı Azure Kaynak Grafiği Gezgini'nde deneyin:

- Azure portalı: yeni pencere simgesinde bağlantıyı <a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/ResourceContainers%20%7C%20where%20type%3D%3D'microsoft.resources%2Fsubscriptions%2Fresourcegroups'%20%7C%20project%20name%2C%20type%20%20%7C%20limit%205%20%7C%20union%20%20(Resources%20%7C%20project%20name%2C%20type%20%7C%20limit%205)" target="_blank">aç portal.azure.com</a> ![](../../media/new-window.png)
- Azure Devlet portalı: yeni pencere simgesinde <a href="https://portal.azure.us/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/ResourceContainers%20%7C%20where%20type%3D%3D'microsoft.resources%2Fsubscriptions%2Fresourcegroups'%20%7C%20project%20name%2C%20type%20%20%7C%20limit%205%20%7C%20union%20%20(Resources%20%7C%20project%20name%2C%20type%20%7C%20limit%205)" target="_blank">bağlantıyı aç portal.azure.us</a> ![](../../media/new-window.png)
- Azure China portalı: <a href="https://portal.azure.cn/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/ResourceContainers%20%7C%20where%20type%3D%3D'microsoft.resources%2Fsubscriptions%2Fresourcegroups'%20%7C%20project%20name%2C%20type%20%20%7C%20limit%205%20%7C%20union%20%20(Resources%20%7C%20project%20name%2C%20type%20%7C%20limit%205)" target="_blank">portal.azure.cn</a> ![Yeni pencere simgesinde bağlantıyı aç](../../media/new-window.png)

---

## <a name="include-the-tenant-and-subscription-names-with-displaynames"></a><a name="displaynames" />Görüntülü Adlarla kiracı ve abonelik adlarını ekleme

Bu sorgu, sonuçlara **abonelikDisplayName** ve **tenantDisplayName** eklemek için seçenek _Görüntülü Adları_ ile yeni **Ekle** parametresini kullanır. Bu parametre yalnızca Azure CLI ve Azure PowerShell için kullanılabilir.

```azurecli-interactive
az graph query -q "limit 1" --include displayNames
```

```azurepowershell-interactive
Search-AzGraph -Query "limit 1" -Include DisplayNames
```

Abonelik adını almanın `join` alternatifi, işleci kullanmak ve **ResourceContainers** tablosuna ve türüne `Microsoft.Resources/subscriptions` bağlanmaktır. `join`Azure CLI, Azure PowerShell, portal ve desteklenen tüm SDK'da çalışır. Örneğin, [bkz. Örnek - Abonelik adı içeren anahtar kasası.](#join)

> [!NOTE]
> Sorgu, döndürülen özellikleri belirtmek için **proje** yi kullanmıyorsa, **abonelikDisplayName** ve **tenantDisplayName** otomatik olarak sonuçlara dahil edilir.
> Sorgu **projeyi**kullanıyorsa, _DisplayName_ alanlarının her biri **açıkça projeye** dahil edilmelidir veya **Ekle** parametresi kullanılsa bile sonuçlarda döndürülmezler. **Ekle** parametresi [tablolarla](../concepts/query-language.md#resource-graph-tables)çalışmıyor.

## <a name="next-steps"></a>Sonraki adımlar

- [Başlangıç sorgularının örneklerine](starter.md)bakın.
- [Sorgu dili](../concepts/query-language.md)hakkında daha fazla bilgi edinin.
- Kaynakları nasıl [keşfedebilirsiniz](../concepts/explore-resources.md)hakkında daha fazla bilgi edinin.