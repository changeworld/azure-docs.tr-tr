---
title: Azure'da API Yönetimini Hizmet Dokusuyla Tümleştir
description: Azure API Yönetimi'ne nasıl hızla başlayacaksınız ve Trafiği Service Fabric'te arka uç hizmetine yönlendirin.
ms.topic: conceptual
ms.date: 07/10/2019
ms.custom: mvc
ms.openlocfilehash: 7bd781a21a32ca29fe3f5dd2f4432dbf1e5ca411
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80292134"
---
# <a name="integrate-api-management-with-service-fabric-in-azure"></a>Azure'da API Yönetimini Hizmet Dokusuyla Tümleştir

Azure API Management'ı Service Fabric'le dağıtmak ileri düzey bir senaryodur.  Arka uç Service Fabric hizmetleriniz için zengin bir yönlendirme kuralları kümesiyle API'ler yayımlamanız gerektiğinde, API Management yararlı olur. Bulut uygulamalarının normalde kullanıcılar, cihazlar ve diğer uygulamalara tek giriş noktası sağlamak için bir ön uç ağ geçidine ihtiyacı vardır. Service Fabric'te, trafik girişi için tasarlanmış durum bilgisi olmayan ASP.NET Core uygulaması, Event Hubs, IoT Hub veya Azure API Management gibi herhangi bir hizmet ağ geçidi olabilir.

Bu makalede, Hizmeti Kumaş'taki trafiği arka uç hizmetine yönlendirmek için Hizmet Dokusu ile [Azure API Yönetimi'ni](../api-management/api-management-key-concepts.md) nasıl ayarlayabileceğinizgösterilmektedir.  Tamamladığınızda, API Management'ı VNET'e dağıtmış, trafiği durum bilgisi olmayan arka uç hizmetlerine göndermek için API işlemini yapılandırmış olursunuz. Service Fabric ile Azure API Management senaryoları hakkında daha fazla bilgi edinmek için, [genel bakış](service-fabric-api-management-overview.md) makalesine bakın.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="availability"></a>Kullanılabilirlik

> [!IMPORTANT]
> Bu özellik, gerekli sanal ağ desteği sayesinde API Management'ın **Premium** ve **Geliştirici** katmanlarında kullanılabilir.

## <a name="prerequisites"></a>Ön koşullar

Başlamadan önce:

* Azure aboneliğiniz yoksa [ücretsiz bir hesap](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) oluşturun
* [Azure Powershell](https://docs.microsoft.com/powershell/azure/install-Az-ps) veya [Azure CLI'yi yükleyin.](/cli/azure/install-azure-cli)
* Ağ güvenlik grubunda güvenli bir [Windows kümesi](service-fabric-tutorial-create-vnet-and-windows-cluster.md) oluşturun.
* Windows kümesi dağıtıyorsanız, bir Windows dağıtım ortamı ayarlayın. [Visual Studio 2019'u](https://www.visualstudio.com) ve **Azure geliştirmesini,** ASP.NET ve **web geliştirmeyi ve** **.NET Core platform ötesi geliştirme** iş yüklerini yükleyin.  Ardından bir [.NET dağıtım ortamı](service-fabric-get-started.md) ayarlayın.

## <a name="network-topology"></a>Ağ topolojisi

Azure'da güvenli bir [Windows kümeniz](service-fabric-tutorial-create-vnet-and-windows-cluster.md) olduğuna göre, API Yönetimi'ni alt ağdaki sanal ağa (VNET) ve API Yönetimi için atanmış NSG'ye dağıtın. Bu makale için, API Yönetim Kaynak Yöneticisi [şablonu, Windows küme öğreticisinde](service-fabric-tutorial-create-vnet-and-windows-cluster.md) ayarladığınız VNET, subnet ve NSG adlarını kullanacak şekilde önceden yapılandırılmıştır Bu makalede, API Yönetimi ve Hizmet Dokusu'nun aynı Sanal Ağ'ın alt ağlarında bulunduğu Azure'a aşağıdaki topolojiyi dağıtır:

 ![Resim yazısı][sf-apim-topology-overview]

## <a name="sign-in-to-azure-and-select-your-subscription"></a>Azure'da oturum açma ve aboneliğinizi seçme

Azure komutlarını yürütmeden önce Azure hesabınızda oturum açıp aboneliğinizi seçin.

```powershell
Connect-AzAccount
Get-AzSubscription
Set-AzContext -SubscriptionId <guid>
```

```azurecli
az login
az account set --subscription <guid>
```

## <a name="deploy-a-service-fabric-back-end-service"></a>Service Fabric arka uç hizmetini dağıtma

API Management'ı trafiği Service Fabric arka uç hizmetine yönlendirecek şekilde yapılandırmadan önce, istekleri kabul edecek, çalışan bir hizmete ihtiyacınız vardır.  

Varsayılan Web API proje şablonu kullanarak temel bir stateless ASP.NET Core Reliable Service oluşturun. Bu, hizmetiniz için Azure API Management aracılığıyla gösterdiğiniz bir HTTP uç noktası oluşturur.

Visual Studio'yu Yönetici olarak başlatın ve bir ASP.NET Core hizmeti oluşturun:

 1. Visual Studio'da Dosya -> Yeni Proje'yi seçin.
 2. Bulut'un altında Service Fabric Uygulama şablonunu seçin ve bu şablonu **"ApiApplication"** olarak adlandırın.
 3. Durum bilgisi olmayan ASP.NET Core hizmet şablonunu seçin ve projeyi **"WebApiService"** olarak adlandırın.
 4. Web API ASP.NET Core 2.1 proje şablonunu seçin.
 5. Proje oluşturulduktan sonra, `PackageRoot\ServiceManifest.xml` dosyasını açın ve uç nokta kaynak yapılandırmasından `Port` özniteliğini kaldırın:

    ```xml
    <Resources>
      <Endpoints>
        <Endpoint Protocol="http" Name="ServiceEndpoint" Type="Input" />
      </Endpoints>
    </Resources>
    ```

    Bağlantı noktasının kaldırılması, Service Fabric'in Cluster Resource Manager şablonundaki Ağ Güvenlik Grubu aracılığıyla açılan uygulama bağlantı noktası aralığından dinamik olarak bir bağlantı noktası belirtmesine ve trafiğin API Yönetimi'nden ona akmasını sağlamasına olanak tanır.

 6. Web API'nin yerel olarak kullanılabilir olduğunu doğrulamak için Visual Studio'da F5'e basın.

    Service Fabric Explorer'ı açın ve hizmetin dinlediği temel adresi görmek için ASP.NET Core hizmetinin belirli bir örneğinde detaya gidin. Temel adrese `/api/values` öğesini ekleyin ve bunu tarayıcıda açın; Web API şablonundaki ValuesController üzerinde Get yöntemi çağrılır. Şablon tarafından sağlanan varsayılan yanıtı döndürür. Bu yanıt iki dize içeren bir JSON dizisidir:

    ```json
    ["value1", "value2"]`
    ```

    Bu, Azure'da API Management aracılığıyla ortaya çıkardığınız uç noktadır.

 7. Son olarak, uygulamayı Azure'da kümenize dağıtın. Visual Studio'da, Uygulama projesine sağ tıklayın ve **Yayımla**'yı seçin. Azure'da uygulamayı Service Fabric kümenize dağıtmak için kümenizin uç noktasını (örneğin, `mycluster.southcentralus.cloudapp.azure.com:19000`) sağlayın.

Artık durum bilgisi olmayan `fabric:/ApiApplication/WebApiService` adlı bir ASP.NET Core hizmeti Azure'da Service Fabric kümenizde çalıştırılıyor olmalıdır.

## <a name="download-and-understand-the-resource-manager-templates"></a>Resource Manager şablonlarını indirme ve anlama

Şu Resource Manager şablon ve parametre dosyalarını indirin ve kaydedin:

* [network-apim.json][network-arm]
* [network-apim.parameters.json][network-parameters-arm]
* [apim.json][apim-arm]
* [apim.parameters.json][apim-parameters-arm]

*network-apim.json* şablonu, Service Fabric kümesinin dağıtıldığı sanal ağda yeni bir alt ağ ve ağ güvenliği grubunun dağıtımını yapar.

Aşağıdaki bölümlerde *apim.json* şablonu tarafından tanımlanan kaynaklar açıklanır. Daha fazla bilgi için, her bölümde verilen şablon başvuru belgelerinin bağlantılarını izleyin. *apim.parameters.json* parametre dosyasında tanımlanan yapılandırılabilir parametreler, bu makalenin devamında ayarlanacaktır.

### <a name="microsoftapimanagementservice"></a>Microsoft.ApiManagement/service

[Microsoft.ApiManagement/service](/azure/templates/microsoft.apimanagement/service), API Management hizmet örneğini açıklar: ad, SKU ve diğer katman, kaynak grubu konumu, yayımcı bilgileri ve sanal ağ.

### <a name="microsoftapimanagementservicecertificates"></a>Microsoft.ApiManagement/service/certificates

[Microsoft.ApiManagement/service/certificates](/azure/templates/microsoft.apimanagement/service/certificates), API Management güvenliğini yapılandırır. API Management'ın kümenize erişimi olan bir istemci sertifikası kullanarak hizmet kurtarma için Service Fabric kümenizle kimlik doğrulaması yapması gerekir. Bu makalede, varsayılan olarak kümenize erişmek için kullanılabilecek [Windows kümesi](service-fabric-tutorial-create-vnet-and-windows-cluster.md#createvaultandcert_anchor)oluşturulurken daha önce belirtilen aynı sertifika yı kullanır.

Bu makalede, istemci kimlik doğrulaması ve küme düğümü-düğüm güvenliği için aynı sertifika kullanır. Service Fabric kümenize erişim için yapılandırılmış ayrı bir istemci sertifikanız varsa, onu da kullanabilirsiniz. Service Fabric kümenizi oluştururken belirttiğiniz küme sertifikası özel anahtar dosyasının (.pfx) **adını**, **parolasını** ve **verilerini** (base-64 kodlama dizesi) sağlayın.

### <a name="microsoftapimanagementservicebackends"></a>Microsoft.ApiManagement/service/backends

[Microsoft.ApiManagement/service/backends](/azure/templates/microsoft.apimanagement/service/backends) trafiğin iletildiği arka uç hizmetini açıklar.

Service Fabric arka uçları için, belirli bir Service Fabric hizmeti yerine Service Fabric kümesi arka uçtur. Bu, tek bir ilkenin kümedeki birden çok hizmete yönlendirilmesine olanak tanır. Buradaki **url** alanı, arka uç ilkesinde hiçbir hizmet adı belirtilmezse varsayılan olarak tüm isteklerin yönlendirileceği kümenizdeki bir hizmetin tam hizmet adıdır. Bir geri dönüş hizmetinizin olmasını düşünmüyorsanız, "fabric:/sahte/hizmet" gibi sahte bir hizmet adı da kullanabilirsiniz. **ResourceId**, küme yönetim uç noktasını belirtir.  Kümeyle kimlik doğrulaması yapmak için **clientCertificateThumbprint** ve **serverCertificateThumbprints** kimlik sertifikaları kullanılır.

### <a name="microsoftapimanagementserviceproducts"></a>Microsoft.ApiManagement/service/products

[Microsoft.ApiManagement/service/products](/azure/templates/microsoft.apimanagement/service/products) bir ürün oluşturur. Azure API Management'ta, üründe bir veya birden çok API, ayrıca kullanım kotası ve kullanım koşulları bulunur. Ürün yayımlandığında, geliştiriciler ürüne abone olabilir ve ürünün API'lerini kullanmaya başlayabilir.

Ürün için açıklayıcı bir **displayName** ve **description** girin. Bu makale için abonelik gereklidir, ancak bir yönetici tarafından abonelik onayı gerekmez.  Bu ürün **durumu** "yayımlanır" ve aboneler tarafından görünür durumda olur.

### <a name="microsoftapimanagementserviceapis"></a>Microsoft.ApiManagement/service/apis

[Microsoft.ApiManagement/service/apis](/azure/templates/microsoft.apimanagement/service/apis) bir API oluşturur. API Management'taki API, istemci uygulamaları tarafından çağrılabilen bir dizi işlemi temsil eder. İşlemler eklendikten sonra, API ürüne eklenir ve yayımlanabilir. API yayımlandığında, geliştiriciler abone olabilir ve kullanabilir.

* **displayName**, API'niz için herhangi bir ad olabilir. Bu makale için "Service Fabric App" kullanın.
* **name**, API için "service-fabric-app" gibi benzersiz ve açıklayıcı bir ad sağlar. Geliştirici ve yayımcı portallarında görüntülenir.
* **serviceUrl**, API'yi gerçekleştiren HTTP hizmetine başvurur. API Management istekleri bu adrese iletir. Service Fabric arka uçları için bu URL değeri kullanılmaz. Buraya herhangi bir değer koyabilirsiniz. Bu makale için, örneğin\/"http: /servicefabric".
* **path**, API Management hizmeti için temel URL'nin sonuna eklenir. Temel URL, bir API Management hizmet örneği tarafından barındırılan tüm API'lerde ortaktır. API Management API'leri soneklerine bakarak ayırt eder; dolayısıyla belirli bir yayımcıdaki her API için sonekin benzersiz olması gerekir.
* **protocols**, API'ye erişmek için hangi protokollerin kullanılacağını belirler. Bu makale için, liste **http** ve **https**.
* **path**, API için bir sonektir. Bu makale için "myapp"ı kullanın.

### <a name="microsoftapimanagementserviceapisoperations"></a>Microsoft.ApiManagement/service/apis/operations

[Microsoft.ApiManagement/service/apis/operations](/azure/templates/microsoft.apimanagement/service/apis/operations) API Management'taki bir API'nin kullanılabilmesi için, API'ye işlemlerin eklenmesi gerekir.  Dış istemciler, Service Fabric kümesinde çalıştırılan, durum bilgisi olmayan bir ASP.NET Core hizmetiyle iletişim kurmak için işlem kullanır.

Ön uç API işlemi eklemek için şu değerleri doldurun:

* **displayName** ve **description** işlemi açıklar. Bu makale için "Değerler" kullanın.
* **method**, HTTP fiilini belirtir.  Bu makale için **GET'i**belirtin.
* **urlTemplate**, API'nin temel URL'sinin sonuna eklenir ve tek bir HTTP işlemini tanımlar.  Bu makale için `/api/values` ,.NET arka uç hizmetini `getMessage` eklediyseniz veya Java arka uç hizmetini eklediyseniz kullanın.  Varsayılan olarak, burada belirtilen URL yolu arka uç Service Fabric hizmetine gönderilen URL yoludur. Burada hizmetinizin kullandığı URL yolunun aynısını kullanırsanız, ("/api/values" gibi), işlem başka bir değişikliğe gerek kalmadan çalışır. Burada, arka uç Service Fabric hizmetinizin kullandığı URL yolundan farklı bir URL yolu da belirtebilirsiniz. Bu durumda, daha sonra işlem ilkenizde yol yeniden yazma belirtmeniz gerekir.

### <a name="microsoftapimanagementserviceapispolicies"></a>Microsoft.ApiManagement/service/apis/policies

[Microsoft.ApiManagement/service/apis/policies](/azure/templates/microsoft.apimanagement/service/apis/policies), her şeyi birbirine bağlayan bir arka uç ilkesi oluşturur. Burası, isteklerin yönlendirildiği arka uç Service Fabric hizmetini yapılandırdığınız yerdir. Bu ilkeyi herhangi bir API işlemine uygulayabilirsiniz.  Daha fazla bilgi için bkz. [İlkelere genel bakış](/azure/api-management/api-management-howto-policies).

[Service Fabric için arka uç yapılandırması](/azure/api-management/api-management-transformation-policies#SetBackendService) aşağıdaki istek yönlendirme denetimlerini sağlar:

* Sabit kodlanmış (örneğin `"fabric:/myapp/myservice"`) veya HTTP isteğinden oluşturulmuş (örneğin, `"fabric:/myapp/users/" + context.Request.MatchedParameters["name"]`) bir Service Fabric hizmet örneği adı belirterek hizmet örneği seçimi.
* Herhangi bir Service Fabric bölümleme şeması kullanıp bölümleme anahtarı oluşturarak bölümleme çözümlemesi.
* Durum bilgisi olan hizmetler için çoğaltma seçimi.
* Hizmet konumunu yeniden çözümlemenize ve isteği yeniden göndermenize olanak tanıyan çözümleme yeniden deneme koşulları.

**policyContent**, ilkenin Json kaçan XML içeriğidir.  Bu makale için, istekleri daha önce dağıtılan .NET veya Java sürücüsüz hizmetine yönlendirmek için bir arka uç ilkesi oluşturun. Gelen ilkelerin altına bir `set-backend-service` ilkesi ekleyin.  *sf-service-instance-name* değerini, daha önce .NET arka uç hizmetini dağıttıysanız `fabric:/ApiApplication/WebApiService` ile veya Java hizmetini dağıttıysanız `fabric:/EchoServerApplication/EchoServerService` ile değiştirin.  *backend-id* bir arka uç kaynağına, bu örnekte *apim.json* şablonunda tanımlanan `Microsoft.ApiManagement/service/backends` kaynağına başvurur. *backend-id*, API Management API'leri kullanılarak oluşturulan başka bir arka uç kaynağına da başvurabilir. Bu makale için, *arka uç kimliği* *service_fabric_backend_name* parametrenin değerine ayarlayın.

```xml
<policies>
  <inbound>
    <base/>
    <set-backend-service
        backend-id="servicefabric"
        sf-service-instance-name="service-name"
        sf-resolve-condition="@(context.LastError?.Reason == "BackendConnectionFailure")" />
  </inbound>
  <backend>
    <base/>
  </backend>
  <outbound>
    <base/>
  </outbound>
</policies>
```

Tüm Service Fabric arka uç ilkesi öznitelikleri için, [API Management arka uç belgelerine](https://docs.microsoft.com/azure/api-management/api-management-transformation-policies#SetBackendService) bakın

## <a name="set-parameters-and-deploy-api-management"></a>Parametreleri ayarlama ve API Management'ı dağıtma

Dağıtımınız için *apim.parameters.json*'da aşağıdaki boş parametreleri doldurun.

|Parametre|Değer|
|---|---|
|apimInstanceName|sf-apim|
|apimPublisherEmail|myemail@contosos.com|
|apimSku|Geliştirici|
|serviceFabricCertificateName|sfclustertutorialgroup320171031144217|
|certificatePassword|q6D7nN%6ck@6|
|serviceFabricCertificateThumbprint|C4C1E541AD512B8065280292A8BA6079C3F26F10 |
|serviceFabricCertificate|&lt;base-64 kodlama dizesi&gt;|
|url_path|/api/values|
|clusterHttpManagementEndpoint|`https://mysfcluster.southcentralus.cloudapp.azure.com:19080`|
|inbound_policy|&lt;XML dizesi&gt;|

*certificatePassword* ve *serviceFabricCertificateThumbprint*, kümeyi ayarlamak için kullanılan küme sertifikasıyla eşleşmelidir.

*serviceFabricCertificate*, bir base-64 kodlama dizesi olan sertifikadır ve şu betik kullanılarak oluşturulabilir:

```powershell
$bytes = [System.IO.File]::ReadAllBytes("C:\mycertificates\sfclustertutorialgroup220171109113527.pfx");
$b64 = [System.Convert]::ToBase64String($bytes);
[System.Io.File]::WriteAllText("C:\mycertificates\sfclustertutorialgroup220171109113527.txt", $b64);
```

*inbound_policy*'deki *sf-service-instance-name* değerini, daha önce .NET arka uç hizmetini dağıttıysanız `fabric:/ApiApplication/WebApiService` ile veya Java hizmetini dağıttıysanız `fabric:/EchoServerApplication/EchoServerService` ile değiştirin. *backend-id* bir arka uç kaynağına, bu örnekte *apim.json* şablonunda tanımlanan `Microsoft.ApiManagement/service/backends` kaynağına başvurur. *backend-id*, API Management API'leri kullanılarak oluşturulan başka bir arka uç kaynağına da başvurabilir. Bu makale için, *arka uç kimliği* *service_fabric_backend_name* parametrenin değerine ayarlayın.

```xml
<policies>
  <inbound>
    <base/>
    <set-backend-service
        backend-id="servicefabric"
        sf-service-instance-name="service-name"
        sf-resolve-condition="@(context.LastError?.Reason == "BackendConnectionFailure")" />
  </inbound>
  <backend>
    <base/>
  </backend>
  <outbound>
    <base/>
  </outbound>
</policies>
```

API Management'ın Resource Manager şablonunu ve parametre dosyalarını dağıtmak için aşağıdaki betiği kullanın:

```powershell
$groupname = "sfclustertutorialgroup"
$clusterloc="southcentralus"
$templatepath="C:\clustertemplates"

New-AzResourceGroupDeployment -ResourceGroupName $groupname -TemplateFile "$templatepath\network-apim.json" -TemplateParameterFile "$templatepath\network-apim.parameters.json" -Verbose

New-AzResourceGroupDeployment -ResourceGroupName $groupname -TemplateFile "$templatepath\apim.json" -TemplateParameterFile "$templatepath\apim.parameters.json" -Verbose
```

```azurecli
ResourceGroupName="sfclustertutorialgroup"
az group deployment create --name ApiMgmtNetworkDeployment --resource-group $ResourceGroupName --template-file network-apim.json --parameters @network-apim.parameters.json

az group deployment create --name ApiMgmtDeployment --resource-group $ResourceGroupName --template-file apim.json --parameters @apim.parameters.json
```

## <a name="test-it"></a>Test etme

Artık doğrudan [Azure Portal](https://portal.azure.com)'dan API Management aracılığıyla Service Fabric'teki arka uç hizmetinize istek göndermeyi deneyebilirsiniz.

 1. API Management hizmetinde **API**'yi seçin.
 2. Önceki adımlarda oluşturduğunuz **Service Fabric Uygulama** API'sinde **Test** sekmesini ve ardından **Values** işlemini seçin.
 3. **Gönder** düğmesine tıklayarak test isteğini arka uç hizmetine gönderin.  Şuna benzer bir HTTP yanıtı görmelisiniz:

    ```http
    HTTP/1.1 200 OK

    Transfer-Encoding: chunked

    Content-Type: application/json; charset=utf-8

    Vary: Origin

    Ocp-Apim-Trace-Location: https://apimgmtstodhwklpry2xgkdj.blob.core.windows.net/apiinspectorcontainer/PWSQOq_FCDjGcaI1rdMn8w2-2?sv=2015-07-08&sr=b&sig=MhQhzk%2FEKzE5odlLXRjyVsgzltWGF8OkNzAKaf0B1P0%3D&se=2018-01-28T01%3A04%3A44Z&sp=r&traceId=9f8f1892121e445ea1ae4d2bc8449ce4

    Date: Sat, 27 Jan 2018 01:04:44 GMT


    ["value1", "value2"]
    ```

## <a name="clean-up-resources"></a>Kaynakları temizleme

Küme, küme kaynağının yanı sıra diğer Azure kaynaklarından oluşur. Kümeyi ve kullandığı tüm kaynakları silmenin en basit yolu, kaynak grubunun silinmesidir.

Azure'da oturum açın ve kümeyi kaldırmak istediğiniz abonelik kimliğini seçin.  Abonelik kimliğinizi, [Azure portalında](https://portal.azure.com) oturum açarak öğrenebilirsiniz. [Remove-AzResourceGroup cmdlet'i](/en-us/powershell/module/az.resources/remove-azresourcegroup)kullanarak kaynak grubunu ve tüm küme kaynaklarını silin.

```powershell
$ResourceGroupName = "sfclustertutorialgroup"
Remove-AzResourceGroup -Name $ResourceGroupName -Force
```

```azurecli
ResourceGroupName="sfclustertutorialgroup"
az group delete --name $ResourceGroupName
```

## <a name="next-steps"></a>Sonraki adımlar

[API Yönetimi'ni](/azure/api-management/import-and-publish)kullanma hakkında daha fazla bilgi edinin.

[azure-powershell]: https://azure.microsoft.com/documentation/articles/powershell-install-configure/

[apim-arm]:https://github.com/Azure/service-fabric-scripts-and-templates/blob/master/templates/service-integration/apim.json
[apim-parameters-arm]:https://github.com/Azure/service-fabric-scripts-and-templates/blob/master/templates/service-integration/apim.parameters.json

[network-arm]: https://github.com/Azure/service-fabric-scripts-and-templates/blob/master/templates/service-integration/network-apim.json
[network-parameters-arm]: https://github.com/Azure/service-fabric-scripts-and-templates/blob/master/templates/service-integration/network-apim.parameters.json

<!-- pics -->
[sf-apim-topology-overview]: ./media/service-fabric-tutorial-deploy-api-management/sf-apim-topology-overview.png
vice-fabric-scripts-and-templates/blob/master/templates/service-integration/network-apim.parameters.jsonn

<!-- pics -->
[sf-apim-topology-overview]: ./media/service-fabric-tutorial-deploy-api-management/sf-apim-topology-overview.png
