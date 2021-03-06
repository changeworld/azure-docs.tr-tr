---
title: PowerShell'i kullanarak v2 özel kurallarını yapılandırma
titleSuffix: Azure Web Application Firewall
description: Azure PowerShell'i kullanarak WAF v2 özel kurallarını nasıl yapılandırıştırmayı öğrenin. Güvenlik duvarından geçen her istek için değerlendirilen kendi kurallarınızı oluşturabilirsiniz.
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.topic: article
ms.date: 11/16/2019
ms.author: victorh
ms.openlocfilehash: 4c50c4ce344a51a70f6849beb7c5d9d18a2b401d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77471644"
---
# <a name="configure-web-application-firewall-v2-on-application-gateway-with-a-custom-rule-using-azure-powershell"></a>Azure PowerShell'i kullanarak Uygulama Ağ Geçidi'nde Web Uygulaması Güvenlik Duvarı v2'yi özel bir kuralla yapılandırma

<!--- If you make any changes to the PowerShell in this article, also make the change in the corresponding Sample file: azure-docs-powershell-samples/application-gateway/waf-rules/waf-custom-rules.ps1 --->

Özel kurallar, Web Uygulama Güvenlik Duvarı (WAF) v2'den geçen her istek için değerlendirilen kendi kurallarınızı oluşturmanıza olanak sağlar. Bu kurallar, yönetilen kural kümelerinde yer alan diğer kurallardan daha yüksek bir önceliğe sahip. Özel kurallar, tam özelleştirmeye izin vermek için bir eylem (izin vermek veya engellemek için), bir eşleşme koşuluna ve bir işleçe sahiptir.

Bu makalede, özel bir kural kullanan bir Uygulama Ağ Geçidi WAF v2 oluşturur. İstek üstbilgisi Kullanıcı Temsilcisi *evilbot*içeriyorsa özel kural trafiği engeller.

Daha fazla özel kural örneği görmek için [bkz.](create-custom-waf-rules.md)

Azure PowerShell'i bu makalede kopyalayıp yapıştırabileceğiniz ve çalıştırabileceğiniz tek bir sürekli komut dosyasında çalıştırmak istiyorsanız, [Bkz. Azure Application Gateway PowerShell örnekleri.](powershell-samples.md)

## <a name="prerequisites"></a>Ön koşullar

### <a name="azure-powershell-module"></a>Azure PowerShell modülü

Azure PowerShell'i yerel olarak yüklemeyi ve kullanmayı seçerseniz, bu komut dosyası azure PowerShell modülü sürümü 2.1.0 veya daha sonra gerektirir.

1. Sürümü bulmak için `Get-Module -ListAvailable Az` komutunu çalıştırın. Yükseltmeniz gerekirse, bkz. [Azure PowerShell modülünü yükleme](/powershell/azure/install-az-ps).
2. Azure ile bağlantı oluşturmak `Connect-AzAccount`için çalıştırın.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="example-script"></a>Örnek betik

### <a name="set-up-variables"></a>Değişkenleri ayarlama

```azurepowershell
$rgname = "CustomRulesTest"

$location = "East US"

$appgwName = "WAFCustomRules"
```

### <a name="create-a-resource-group"></a>Kaynak grubu oluşturma

```azurepowershell
$resourceGroup = New-AzResourceGroup -Name $rgname -Location $location
```

### <a name="create-a-vnet"></a>Sanal ağ oluşturma

```azurepowershell
$sub1 = New-AzVirtualNetworkSubnetConfig -Name "appgwSubnet" -AddressPrefix "10.0.0.0/24"

$sub2 = New-AzVirtualNetworkSubnetConfig -Name "backendSubnet" -AddressPrefix "10.0.1.0/24"

$vnet = New-AzvirtualNetwork -Name "Vnet1" -ResourceGroupName $rgname -Location $location `
  -AddressPrefix "10.0.0.0/16" -Subnet @($sub1, $sub2)
```

### <a name="create-a-static-public-vip"></a>Statik Genel VIP Oluşturma

```azurepowershell
$publicip = New-AzPublicIpAddress -ResourceGroupName $rgname -name "AppGwIP" `
  -location $location -AllocationMethod Static -Sku Standard
```

### <a name="create-pool-and-frontend-port"></a>Havuz ve ön uç bağlantı noktası oluşturma

```azurepowershell
$gwSubnet = Get-AzVirtualNetworkSubnetConfig -Name "appgwSubnet" -VirtualNetwork $vnet

$gipconfig = New-AzApplicationGatewayIPConfiguration -Name "AppGwIpConfig" -Subnet $gwSubnet

$fipconfig01 = New-AzApplicationGatewayFrontendIPConfig -Name "fipconfig" -PublicIPAddress $publicip

$pool = New-AzApplicationGatewayBackendAddressPool -Name "pool1" `
  -BackendIPAddresses testbackend1.westus.cloudapp.azure.com, testbackend2.westus.cloudapp.azure.com

$fp01 = New-AzApplicationGatewayFrontendPort -Name "port1" -Port 80
```

### <a name="create-a-listener-http-setting-rule-and-autoscale"></a>Dinleyici oluşturma, http ayarı, kuralı ve otomatik ölçeklendirme

```azurepowershell
$listener01 = New-AzApplicationGatewayHttpListener -Name "listener1" -Protocol Http `
  -FrontendIPConfiguration $fipconfig01 -FrontendPort $fp01

$poolSetting01 = New-AzApplicationGatewayBackendHttpSettings -Name "setting1" -Port 80 `
  -Protocol Http -CookieBasedAffinity Disabled

$rule01 = New-AzApplicationGatewayRequestRoutingRule -Name "rule1" -RuleType basic `
  -BackendHttpSettings $poolSetting01 -HttpListener $listener01 -BackendAddressPool $pool

$autoscaleConfig = New-AzApplicationGatewayAutoscaleConfiguration -MinCapacity 3

$sku = New-AzApplicationGatewaySku -Name WAF_v2 -Tier WAF_v2
```

### <a name="create-two-custom-rules-and-apply-it-to-waf-policy"></a>İki özel kural oluşturun ve WAF ilkesine uygulayın

```azurepowershell
# Create WAF config
$wafConfig = New-AzApplicationGatewayWebApplicationFirewallConfiguration -Enabled $true -FirewallMode "Prevention" -RuleSetType "OWASP" -RuleSetVersion "3.0"
# Create a User-Agent header custom rule 
$variable = New-AzApplicationGatewayFirewallMatchVariable -VariableName RequestHeaders -Selector User-Agent
$condition = New-AzApplicationGatewayFirewallCondition -MatchVariable $variable -Operator Contains -MatchValue "evilbot" -Transform Lowercase -NegationCondition $False  
$rule = New-AzApplicationGatewayFirewallCustomRule -Name blockEvilBot -Priority 2 -RuleType MatchRule -MatchCondition $condition -Action Block
 
# Create a geo-match custom rule
$var2 = New-AzApplicationGatewayFirewallMatchVariable -VariableName RequestUri
$condition2 = New-AzApplicationGatewayFirewallCondition -MatchVariable $var2 -Operator GeoMatch -MatchValue "US"  -NegationCondition $False
$rule2 = New-AzApplicationGatewayFirewallCustomRule -Name allowUS -Priority 14 -RuleType MatchRule -MatchCondition $condition2 -Action Allow

# Create a firewall policy
$wafPolicy = New-AzApplicationGatewayFirewallPolicy -Name wafpolicyNew -ResourceGroup $rgname -Location $location -CustomRule $rule,$rule2
```

### <a name="create-the-application-gateway"></a>Uygulama Ağ Geçidi oluşturma

```azurepowershell
$appgw = New-AzApplicationGateway -Name $appgwName -ResourceGroupName $rgname `
  -Location $location -BackendAddressPools $pool `
  -BackendHttpSettingsCollection  $poolSetting01 `
  -GatewayIpConfigurations $gipconfig -FrontendIpConfigurations $fipconfig01 `
  -FrontendPorts $fp01 -HttpListeners $listener01 `
  -RequestRoutingRules $rule01 -Sku $sku -AutoscaleConfiguration $autoscaleConfig `
  -WebApplicationFirewallConfig $wafConfig `
  -FirewallPolicy $wafPolicy
```

## <a name="next-steps"></a>Sonraki adımlar

[Uygulama Ağ Geçidi'nde Web Uygulaması Güvenlik Duvarı hakkında daha fazla bilgi edinin](ag-overview.md)
