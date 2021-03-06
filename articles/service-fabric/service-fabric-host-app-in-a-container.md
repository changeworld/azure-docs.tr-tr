---
title: Bir .NET uygulamasını bir kapsayıcıda Azure Hizmet Kumaşı'na dağıtma
description: Visual Studio'yu ve Service Fabric'teki hata ayıklama kapsayıcılarını yerel olarak kullanıp mevcut .NET uygulamasını kapsayıcılı hale getirmeyi öğrenin. Kapsayıcılı hale getirilen uygulama Azure Container Registry'ye gönderilir ve Service Fabric kümesine dağıtılır. Azure'a dağıtıldığında, verilerin kalıcı olmasını sağlamak için uygulama Azure SQL veritabanını kullanır.
ms.topic: tutorial
ms.date: 07/08/2019
ms.openlocfilehash: d1602d292af24d8c0bc9139debb3967aa7183a06
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/24/2020
ms.locfileid: "75463056"
---
# <a name="tutorial-deploy-a-net-application-in-a-windows-container-to-azure-service-fabric"></a>Öğretici: Azure Service Fabric’e Windows kapsayıcısındaki bir .NET uygulamasını dağıtma

Bu öğreticide mevcut ASP.NET uygulamasını kapsayıcılı hale getirme ve bir Service Fabric uygulaması olarak paketleme işlemleri gösterilir.  Kapsayıcıları yerel olarak Service Fabric geliştirme kümesinde çalıştırın ve ardından uygulamayı Azure'a dağıtın.  Uygulama verileri [Azure SQL Veritabanında](/azure/sql-database/sql-database-technical-overview) kalıcı olarak bulundurur. 

Bu öğreticide şunların nasıl yapıldığını öğrenirsiniz:

> [!div class="checklist"]
> * Visual Studio kullanarak mevcut uygulamayı kapsayıcılı hale getirme
> * Bir Azure SQL veritabanı oluşturma
> * Azure Container Registry oluşturma
> * Service Fabric uygulamasını Azure'a dağıtma


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Ön koşullar

1. Azure aboneliğiniz yoksa, ücretsiz [bir hesap oluşturun](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
2. Windows 10’da kapsayıcıları çalıştırabilmek için [Docker Windows CE](https://store.docker.com/editions/community/docker-ce-desktop-windows?tab=description)’yi yükleyin.
3. [Service Fabric çalışma zamanı sürümü 6.2 veya üstünü](service-fabric-get-started.md) ve [Service Fabric SDK sürüm 3.1](service-fabric-get-started.md) veya üstünü yükleyin.
4. [Visual Studio 2019 Sürüm 16.1'i](https://www.visualstudio.com/) veya daha sonra **Azure geliştirme** ve ASP.NET ve **web geliştirme** iş yükleriyle yükleyin.
5. [Azure PowerShell][link-azure-powershell-install]'i yükleyin
 

## <a name="download-and-run-fabrikam-fiber-callcenter"></a>Fabrikam Fiber CallCenter'ı indirme ve çalıştırma
[Fabrikam Fiber CallCenter][link-fabrikam-github] örnek uygulamasını indirin.  **Arşivi indir** bağlantısına tıklayın.  *fabrikam.zip* dosyasındaki *sourceCode* dizininden *sourceCode.zip* dosyasını ayıklayın ve ardından *VS2015* dizininizi bilgisayarınıza ayıklayın.

Fabrikam Fiber CallCenter uygulamasının hatasız derlendiğinden ve çalıştırıldığından emin olun.  Visual Studio’yu **yönetici** olarak başlatın ve [FabrikamFiber.CallCenter.sln][link-fabrikam-github] dosyasını açın.  Uygulamada hata ayıklaması yapmak ve uygulamayı çalıştırmak için F5'e basın.

![Fabrikam web örneği][fabrikam-web-page]

## <a name="containerize-the-application"></a>Uygulamayı kapsayıcılı hale getirme
**FabrikamFiber.Web** projesi > **Ekle** > **Container Orchestrator Desteği**'ne sağ tıklayın.  Kapsayıcı düzenleyicisi olarak **Service Fabric**'i seçin ve **Tamam**'a tıklayın.

Docker’ı şimdi Windows kapsayıcılarına geçirmek için **Evet**’e tıklayın.

Çözümde yeni bir Service Fabric uygulama projesi (**FabrikamFiber.CallCenterApplication**) oluşturulur.  Mevcut **FabrikamFiber.Web** projesine bir Dockerfile eklenir.  Ayrıca **FabrikamFiber.Web** projesine bir **PackageRoot** dizini de eklenir ve bu dizin yeni FabrikamFiber.Web hizmetinin hizmet bildirimiyle ayarlarını içerir. 

Artık kapsayıcı, Service Fabric uygulamasında oluşturulup paketlenmeye hazırdır. Makinenizde kapsayıcı görüntüsü oluşturulduğunda, bu görüntüyü herhangi bir kapsayıcı kayıt defterine gönderebilir ve çalıştırmak için herhangi bir ana bilgisayara çekebilirsiniz.

## <a name="create-an-azure-sql-db"></a>Azure SQL Veritabanı oluşturma
Fabrikam Fiber CallCenter uygulamasını üretim ortamında çalıştırırken, verilerin bir veritabanında kalıcı olarak bulunması gerekir. Şu anda kapsayıcıdaki verilerin kalıcı olmasını garanti altına alan bir yöntem olmadığından üretim verileriniz, kapsayıcıdaki bir SQL Server’da depolanamaz.

[Azure SQL Veritabanı](/azure/sql-database/sql-database-get-started-powershell)'nı öneririz. Azure'da yönetilen SQL Server Veritabanı ayarlamak ve çalıştırmak için aşağıdaki betiği çalıştırın.  Betik değişkenlerinde gerekli değişiklikleri yapın. *clientIP*, geliştirme bilgisayarınızın IP adresidir. Komut dosyası tarafından çıktı edilen sunucunun adını dikkate alın. 

```powershell
$subscriptionID="<subscription ID>"

# Sign in to your Azure account and select your subscription.
Login-AzAccount -SubscriptionId $subscriptionID 

# The data center and resource name for your resources.
$dbresourcegroupname = "fabrikam-fiber-db-group"
$location = "southcentralus"

# The logical server name: Use a random value or replace with your own value (do not capitalize).
$servername = "fab-fiber-$(Get-Random)"

# Set an admin login and password for your database.
# The login information for the server.
$adminlogin = "ServerAdmin"
$password = "Password@123"

# The IP address of your development computer that accesses the SQL DB.
$clientIP = "<client IP>"

# The database name.
$databasename = "call-center-db"

# Create a new resource group for your deployment and give it a name and a location.
New-AzResourceGroup -Name $dbresourcegroupname -Location $location

# Create the SQL server.
New-AzSqlServer -ResourceGroupName $dbresourcegroupname `
    -ServerName $servername `
    -Location $location `
    -SqlAdministratorCredentials $(New-Object -TypeName System.Management.Automation.PSCredential -ArgumentList $adminlogin, $(ConvertTo-SecureString -String $password -AsPlainText -Force))

# Create the firewall rule to allow your development computer to access the server.
New-AzSqlServerFirewallRule -ResourceGroupName $dbresourcegroupname `
    -ServerName $servername `
    -FirewallRuleName "AllowClient" -StartIpAddress $clientIP -EndIpAddress $clientIP

# Creeate the database in the server.
New-AzSqlDatabase  -ResourceGroupName $dbresourcegroupname `
    -ServerName $servername `
    -DatabaseName $databasename `
    -RequestedServiceObjectiveName "S0"

Write-Host "Server name is $servername"
```
> [!TIP]
> Bir şirket güvenlik duvarının arkasındaysanız, geliştirme bilgisayarınızın IP adresi İnternet'e gösterilen IP adresi olmayabilir. Veritabanının güvenlik duvarı kuralı için doğru IP adresine sahip olduğunun doğrulamak için [Azure portal](https://portal.azure.com)’a gidin ve SQL Veritabanları bölümünde veritabanınızı bulun. Adına tıklayın ve sonra Genel Bakış bölümünde “Sunucu güvenlik duvarını ayarla”ya tıklayın. "İstemci IP adresi", geliştirme makinenizin IP adresidir. "AllowClient" kuralındaki IP adresiyle eşleştiğinden emin olun.

## <a name="update-the-web-config"></a>Web yapılandırmasını güncelleştirme
**FabrikamFiber.Web** projesine geri dönüp **web.config** dosyasındaki bağlantı dizesini kapsayıcıdaki SQL Server noktası ile güncelleştirin.  Bağlantı dizesinin *Sunucu* bölümünü önceki komut dosyasıtarafından oluşturulan sunucu adı olarak güncelleştirin. "fab-fiber-751718376.database.windows.net" gibi bir şey olmalı.

```xml
<add name="FabrikamFiber-Express" connectionString="Server=<server name>,1433;Initial Catalog=call-center-db;Persist Security Info=False;User ID=ServerAdmin;Password=Password@123;MultipleActiveResultSets=False;Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;" providerName="System.Data.SqlClient" />
<add name="FabrikamFiber-DataWarehouse" connectionString="Server=<server name>,1433;Initial Catalog=call-center-db;Persist Security Info=False;User ID=ServerAdmin;Password=Password@123;MultipleActiveResultSets=False;Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;" providerName="System.Data.SqlClient" />
  
```
>[!NOTE]
>Ana bilgisayarınızdan erişilebilir olduğu sürece, yerel hata ayıklama için tercih ettiğiniz herhangi bir SQL Server’ı kullanabilirsiniz. Ancak **localdb**, `container -> host` iletişimini desteklemez. Web uygulamanızın sürüm derlemesini oluştururken farklı bir SQL veritabanı kullanmak isterseniz, *web.release.config* dosyasına başka bir bağlantı dizesi ekleyin.

## <a name="run-the-containerized-application-locally"></a>Kapsayıcılı hale getirilen uygulamayı yerel olarak çalıştırma
Uygulamayı yerel Service Fabric geliştirme kümesindeki bir kapsayıcıda çalıştırmak ve hatalarını ayıklamak için **F5** tuşuna basın. 'ServiceFabricAllowedUsers' grubuna, Visual Studio proje dizininize yönelik okuma ve yürütme izni verilmesini isteyen bir ileti kutusu sunulursa **Evet**’e tıklayın.

## <a name="create-a-container-registry"></a>Kapsayıcı kayıt defteri oluşturma
Artık uygulama yerel olarak çalıştırıldığına göre, Azure'a dağıtmak için hazırlamaya başlayın.  Kapsayıcı görüntülerinin bir kapsayıcı kayıt defterinde depolanması gerekir.  Aşağıdaki betiği kullanarak bir [Azure kapsayıcı kayıt defteri](/azure/container-registry/container-registry-intro) oluşturun. Kapsayıcı kayıt defteri adı diğer Azure aboneliklerine görünür olduğundan benzersiz olmalıdır.
Uygulamayı Azure'a dağıtmadan önce kapsayıcı görüntüsünü bu kayıt defterine gönderirsiniz.  Uygulama Azure'daki kümeye dağıtılırken kapsayıcı görüntüsü bu kayıt defterinden çekilir.

```powershell
# Variables
$acrresourcegroupname = "fabrikam-acr-group"
$location = "southcentralus"
$registryname="fabrikamregistry$(Get-Random)"

New-AzResourceGroup -Name $acrresourcegroupname -Location $location

$registry = New-AzContainerRegistry -ResourceGroupName $acrresourcegroupname -Name $registryname -EnableAdminUser -Sku Basic
```

## <a name="create-a-service-fabric-cluster-on-azure"></a>Azure’da Service Fabric kümesi oluşturma
Service Fabric uygulamaları, ağ bağlantılı sanal veya fiziksel makinelerin bulunduğu bir kümede çalışır.  Uygulamayı Azure'a dağıtmadan önce Azure'da bir Hizmet Dokusu kümesi oluşturun.

Şunları yapabilirsiniz:
- Visual Studio'dan test kümesi oluşturma. Bu seçenek doğrudan Visual Studio'dan tercih ettiğiniz yapılandırmalarla güvenli bir küme oluşturmanızı sağlar. 
- [Şablondan güvenli bir küme oluşturma](service-fabric-tutorial-create-vnet-and-windows-cluster.md)

Bu öğretici Visual Studio'dan bir küme oluşturur; bu test senaryoları için idealdir. Başka herhangi bir yolla küme oluşturursanız veya mevcut kümelerden birini kullanırsanız, bağlantı uç noktanızı kopyalayıp yapıştırabilir veya aboneliğinizden seçebilirsiniz. 

Başlamadan önce, Solution Explorer'da FabrikamFiber.Web->PackageRoot->ServiceManifest.xml'i açın. **Endpoint'te**listelenen web ön uç için bağlantı noktasını not alın. 

Küme oluşturulurken, 

1. Çözüm Gezgini'nde **FabrikamFiber.CallCenterApplication** uygulama projesine sağ tıklayın ve **Yayımla**’yı seçin.

2. Aboneliklerinize erişebilmek için Azure hesabınızı kullanarak oturum açın. 

3. **Bağlantı Uç Noktası**'nın açılan listesini seçin ve sonra da **Yeni Küme Oluştur...** seçeneğini belirtin.    
        
4. Küme **Oluştur** iletişim kutusunda aşağıdaki ayarları değiştirin:

    a. **Küme Adı** alanında kümenizin adını ve kullanmak istediğiniz aboneliği ve konumu belirtin. Küme kaynak grubunuzun adını dikkate alın.

    b. İsteğe bağlı: Düğüm sayısını değiştirebilirsiniz. Varsayılan olarak üç düğümünüz vardır; bu, Service Fabric senaryolarını test etmek için gereken en düşük sayıdır.

    c. **Sertifika** sekmesini seçin. Bu sekmede, kümenizin sertifikasını güvence altına almak için kullanmak üzere bir parola yazın. Bu sertifika, kümenizin güvenliğine yardımcı olur. Ayrıca sertifikayı kaydetmek istediğiniz yolu da değiştirebilirsiniz. Visual Studio sertifikayı sizin için içeri aktarabilir, çünkü uygulamayı kümeye yayımlarken bu gerekli bir adımdır.

    d. **VM Detay** sekmesini seçin. Kümeyi oluşturan Sanal Makineler (VM) için kullanmak istediğiniz parolayı belirtin. Kullanıcı adı ve parola, VM'lere uzaktan bağlanmak için kullanılabilir. Ayrıca VM makine boyutu da seçmelisiniz ve gerekirse VM görüntüsü değiştirebilirsiniz. 

    > [!IMPORTANT]
    >Kapsayıcıları çalıştıran bir SKU seçin. Küme düğümlerinizdeki Windows Server işletim sistemi, kapsayıcınızın Windows Server işletim sistemiyle uyumlu olmalıdır. Daha fazla bilgi için bkz. [Windows Server kapsayıcı işletim sistemi ve ana bilgisayar işletim sistemi uyumluluğu](service-fabric-get-started-containers.md#windows-server-container-os-and-host-os-compatibility). Varsayılan olarak bu öğretici, Windows Server 2016 LTSC’yi temel alan bir Docker görüntüsü oluşturur. Bu görüntüyü temel alan kapsayıcılar, Kapsayıcılar içeren Windows Server 2016 Veri Merkezi ile oluşturulan kümelerde çalıştırılır. Ancak bir küme oluşturur veya Kapsayıcılar içeren Windows Server Datacenter Core 1709’u temel alan mevcut bir kümeyi kullanırsanız, kapsayıcının temel aldığı Windows Server işletim sistemi görüntüsünü değiştirmeniz gerekir. **FabrikamFiber.Web** projesinde **Dockerfile** öğesini açın, mevcut `FROM` deyimini açıklama satırı yapın (`windowsservercore-ltsc` temelinde) ve `windowsservercore-1709` temelinde `FROM` deyiminin açıklamasını kaldırın. 

    e. **Gelişmiş** sekmesinde, küme dağıtılırken yük dengeleyicide açılacak uygulama bağlantı noktasını listeleyin. Bu, küme oluşturmaya başlamadan önce not aldığınız bağlantı noktasıdır. Ayrıca uygulama günlük dosyalarını yönlendirmek için kullanılacak mevcut bir Application Insights anahtarı ekleyebilirsiniz.

    f. Ayarları değiştirmeyi bitirdiğinizde **Oluştur** düğmesini seçin. 
1. Oluşturma işleminin tamamlanması birkaç dakika sürer; çıkış penceresinde kümenin ne zaman tam olarak oluşturulduğu gösterilir.
    

## <a name="allow-your-application-running-in-azure-to-access-the-sql-db"></a>Azure'da çalıştırılan uygulamanızın SQL veritabanına erişmesine izin verme
Daha önce, yerel olarak çalıştırılan uygulamanıza erişim vermek için bir SQL güvenlik duvarı kuralı oluşturmuştunuz.  Bundan sonra, Azure'da çalıştırılan uygulamanın SQL veritabanına erişimini etkinleştirmeniz gerekir.  Service Fabric kümesi için bir [sanal ağ hizmet uç noktası](/azure/sql-database/sql-database-vnet-service-endpoint-rule-overview) oluşturun ve ardından bu uç noktanın SQL veritabanına erişmesine izin verecek bir kural oluşturun. Küme oluştururken dikkat çektiğiniz küme kaynak grubu değişkenini belirttiğinden emin olun. 

```powershell
# Create a virtual network service endpoint
$clusterresourcegroup = "<cluster resource group>"
$resource = Get-AzResource -ResourceGroupName $clusterresourcegroup -ResourceType Microsoft.Network/virtualNetworks | Select-Object -first 1
$vnetName = $resource.Name

Write-Host 'Virtual network name: ' $vnetName 

# Get the virtual network by name.
$vnet = Get-AzVirtualNetwork `
  -ResourceGroupName $clusterresourcegroup `
  -Name              $vnetName

Write-Host "Get the subnet in the virtual network:"

# Get the subnet, assume the first subnet contains the Service Fabric cluster.
$subnet = Get-AzVirtualNetworkSubnetConfig -VirtualNetwork $vnet | Select-Object -first 1

$subnetName = $subnet.Name
$subnetID = $subnet.Id
$addressPrefix = $subnet.AddressPrefix

Write-Host "Subnet name: " $subnetName " Address prefix: " $addressPrefix " ID: " $subnetID

# Assign a Virtual Service endpoint 'Microsoft.Sql' to the subnet.
$vnet = Set-AzVirtualNetworkSubnetConfig `
  -Name            $subnetName `
  -AddressPrefix   $addressPrefix `
  -VirtualNetwork  $vnet `
  -ServiceEndpoint Microsoft.Sql | Set-AzVirtualNetwork

$vnet.Subnets[0].ServiceEndpoints;  # Display the first endpoint.

# Add a SQL DB firewall rule for the virtual network service endpoint
$subnet = Get-AzVirtualNetworkSubnetConfig `
  -Name           $subnetName `
  -VirtualNetwork $vnet;

$VNetRuleName="ServiceFabricClusterVNetRule"
$vnetRuleObject1 = New-AzSqlServerVirtualNetworkRule `
  -ResourceGroupName      $dbresourcegroupname `
  -ServerName             $servername `
  -VirtualNetworkRuleName $VNetRuleName `
  -VirtualNetworkSubnetId $subnetID;
```
## <a name="deploy-the-application-to-azure"></a>Uygulamayı Azure’a dağıtma
Uygulama hazır olduğuna göre, doğrudan Visual Studio'dan Azure'daki bir kümeye dağıtabilirsiniz.  Çözüm Gezgini'nde **FabrikamFiber.CallCenterApplication** uygulama projesine sağ tıklayın ve **Yayımla**'yı seçin.  **Bağlantı Uç Noktası**'nda, daha önce oluşturmuş olduğunuz kümenin uç noktasını seçin.  **Azure Container Registry**'de, daha önce oluşturmuş olduğunuz kapsayıcı kayıt defterini seçin.  Uygulamayı Azure'daki kümeye dağıtmak için **Yayımla**’ya tıklayın.

![Uygulama yayımlama][publish-app]

Çıkış penceresinde dağıtımın ilerleme durumunu izleyin.  Uygulama dağıtıldığında, tarayıcıyı açın ve küme adresiyle uygulama bağlantı noktasını yazın. Örneğin, http:\//fabrikamfibercallcenter.southcentralus.cloudapp.azure.com:8659/.

![Fabrikam web örneği][fabrikam-web-page-deployed]

## <a name="set-up-continuous-integration-and-deployment-cicd-with-a-service-fabric-cluster"></a>Service Fabric kümesi ile Sürekli Tümleştirme ve Dağıtımı (CI/CD) ayarlama
Service Fabric kümesinde CI/CD uygulama dağıtımını yapılandırmak için Azure DevOps kullanma hakkında bilgi edinmek için bkz. [Öğretici: CI/CD ile Service Fabric kümesine uygulama dağıtma](service-fabric-tutorial-deploy-app-with-cicd-vsts.md). Bu öğreticide anlatılan işlem, bu (FabrikamFiber) projesiyle aynıdır, tek yapmanız gereken Voting örneğini indirme adımını atlayıp depo adını Voting yerine FabrikamFiber olarak değiştirmektir.

## <a name="clean-up-resources"></a>Kaynakları temizleme
İşiniz bittiğinde, oluşturduğunuz tüm kaynakları kaldırmayı unutmayın.  Bunun en basit yolu Service Fabric kümesini, Azure SQL veritabanını ve Azure Container Registry'yi içeren kaynak gruplarını kaldırmaktır.

```powershell
$dbresourcegroupname = "fabrikam-fiber-db-group"
$acrresourcegroupname = "fabrikam-acr-group"
$clusterresourcegroupname="fabrikamcallcentergroup"

# Remove the Azure SQL DB
Remove-AzResourceGroup -Name $dbresourcegroupname

# Remove the container registry
Remove-AzResourceGroup -Name $acrresourcegroupname

# Remove the Service Fabric cluster
Remove-AzResourceGroup -Name $clusterresourcegroupname
```

## <a name="next-steps"></a>Sonraki adımlar
Bu öğreticide, şunların nasıl yapıldığını öğrendiniz:

> [!div class="checklist"]
> * Visual Studio kullanarak mevcut uygulamayı kapsayıcılı hale getirme
> * Bir Azure SQL veritabanı oluşturma
> * Azure Container Registry oluşturma
> * Service Fabric uygulamasını Azure'a dağıtma

Öğreticinin bir sonraki bölümünde, [Service Fabric kümesine CI/CD ile kapsayıcı uygulaması dağıtmayı](service-fabric-tutorial-deploy-container-app-with-cicd-vsts.md) öğreneceksiniz.

[link-fabrikam-github]: https://aka.ms/fabrikamcontainer
[link-azure-powershell-install]: /powershell/azure/install-Az-ps
[link-servicefabric-create-secure-clusters]: service-fabric-cluster-creation-via-arm.md
[link-visualstudio-cd-extension]: https://aka.ms/cd4vs
[link-servicefabric-containers]: service-fabric-get-started-containers.md
[link-azure-portal]: https://portal.azure.com
[link-sf-clustertemplate]: https://aka.ms/securepreviewonelineclustertemplate
[link-azure-pricing-calculator]: https://azure.microsoft.com/pricing/calculator/
[link-azure-subscription]: https://azure.microsoft.com/free/
[link-vsts-account]: https://www.visualstudio.com/team-services/pricing/
[link-azure-sql]: /azure/sql-database/

[fabrikam-web-page]: media/service-fabric-host-app-in-a-container/fabrikam-web-page.png
[fabrikam-web-page-deployed]: media/service-fabric-host-app-in-a-container/fabrikam-web-page-deployed.png
[publish-app]: media/service-fabric-host-app-in-a-container/publish-app.png
