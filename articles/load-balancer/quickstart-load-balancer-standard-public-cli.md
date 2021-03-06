---
title: Quickstart:Ortak Yük Dengeleyicisi Oluşturma - Azure CLI
titleSuffix: Azure Load Balancer
description: Bu hızlı başlangıçta, Azure CLI kullanarak genel bir yük dengeleyicinin nasıl oluşturulacağı gösterilmektedir
services: load-balancer
documentationcenter: na
author: asudbring
manager: twooley
tags: azure-resource-manager
Customer intent: I want to create a Load balancer so that I can load balance internet traffic to VMs.
ms.assetid: a8bcdd88-f94c-4537-8143-c710eaa86818
ms.service: load-balancer
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/25/2019
ms.author: allensu
ms.custom: mvc
ms.openlocfilehash: 1f6a05fdfc28adf412ffbd1402e37b69d1c51634
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/26/2020
ms.locfileid: "79477774"
---
# <a name="quickstart-create-a-standard-load-balancer-to-load-balance-vms-using-azure-cli"></a>Hızlı başlangıç: Azure CLI kullanarak bakiye VM'lerini yüklemek için standart yük dengeleyicisi oluşturun

Bu hızlı başlangıç, herkese açık bir Yük Dengeleyicisi oluşturmanızı gösterir. Yük dengeleyiciyi test etmek için, Ubuntu server çalıştıran iki sanal makine (VM) dağıtın ve iki sanal makine arasında bir web uygulamasının yük dengelemesini yapın.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)] 

CLI'yi yerel olarak yükleyip kullanmayı tercih ederseniz bu öğretici için Azure CLI 2.0.28 veya sonraki bir sürümünü kullanmanız gerekir. Sürümü bulmak için `az --version` komutunu çalıştırın. Yükleme veya yükseltme yapmanız gerekirse bkz. [Azure CLI’yı yükleme]( /cli/azure/install-azure-cli).

## <a name="create-a-resource-group"></a>Kaynak grubu oluşturma

[az group create](https://docs.microsoft.com/cli/azure/group) ile bir kaynak grubu oluşturun. Azure kaynak grubu, Azure kaynaklarının dağıtıldığı ve yönetildiği bir mantıksal kapsayıcıdır.

Aşağıdaki örnek *eastus* konumunda *myResourceGroupSLB* adlı bir kaynak grubu oluşturur:

```azurecli-interactive
  az group create \
    --name myResourceGroupSLB \
    --location eastus
```

## <a name="create-a-public-ip-address"></a>Genel IP adresi oluşturma

Web uygulamanıza İnternet’ten erişmek için yük dengeleyicinin genel IP adresi gereklidir. *myResourceGroupSLB'de* *myPublicIP* adlı standart bölge gereksiz Genel IP adresi oluşturmak için [az network public-ip oluşturmayı](https://docs.microsoft.com/cli/azure/network/public-ip) kullanın.

```azurecli-interactive
  az network public-ip create --resource-group myResourceGroupSLB --name myPublicIP --sku standard
```

Bölge 1'de bir zonal Public IP adresi oluşturmak için kullanın:

```azurecli-interactive
  az network public-ip create --resource-group myResourceGroupSLB --name myPublicIP --sku standard --zone 1
```

Temel `-SKU Basic` Genel IP oluşturmak için kullanın. Temel Genel IP'ler **Standart** yük dengeleyicisi ile uyumlu değildir. Microsoft, üretim iş yükleri için **Standart'ı** kullanmanızı önerir.

> [!IMPORTANT]
> Bu hızlı başlatmanın geri kalanı, yukarıdaki SKU seçim işlemi sırasında **Standart** SKU'nun seçildiğini varsayar.

## <a name="create-azure-load-balancer"></a>Azure Yük bakiyesi oluşturma

Bu bölümde yük dengeleyicinin aşağıdaki bileşenlerini nasıl oluşturabileceğiniz ve yapılandırabileceğiniz açıklanmaktadır:
  - Yük dengeleyicideki gelen ağ trafiğini alan bir ön uç IP havuzu.
  - Ön uç havuzunun yük dengelemesi yapılmış ağ trafiğini gönderdiği bir arka uç IP havuzu.
  - Arka uç sanal makine örneklerinin durumunu belirleyen bir durum araştırması.
  - Trafiğin sanal makinelere dağıtımını tanımlayan bir yük dengeleyici kuralı.

### <a name="create-the-load-balancer"></a>Yük dengeleyiciyi oluşturma

Önceki adımda oluşturduğunuz **myPublicIP** genel IP adresiyle ilişkilendirilmiş **myBackEndPool** adlı bir arka uç havuzunu ve **myFrontEnd** adlı bir ön uç havuzunu içeren **myLoadBalancer** adlı [az network lb create](https://docs.microsoft.com/cli/azure/network/lb?view=azure-cli-latest) ile genel bir Azure Load Balancer oluşturun. Temel `--sku basic` Genel IP oluşturmak için kullanın. Microsoft, üretim iş yükleri için Standart SKU önerir.

```azurecli-interactive
  az network lb create \
    --resource-group myResourceGroupSLB \
    --name myLoadBalancer \
    --sku standard \
    --public-ip-address myPublicIP \
    --frontend-ip-name myFrontEnd \
    --backend-pool-name myBackEndPool       
```

> [!IMPORTANT]
> Bu hızlı başlatmanın geri kalanı, yukarıdaki SKU seçim işlemi sırasında **Standart** SKU'nun seçildiğini varsayar.

### <a name="create-the-health-probe"></a>Durum araştırması oluşturma

Sistem durumu araştırması tüm sanal makine örneklerini denetleyerek ağ trafiği gönderdiklerinden emin olur. Sistem durumu denetimi başarısız olan sanal makine örnekleri tekrar çevrimiçi olana ve sistem durumu denetimi iyi olduğuna karar verene kadar yük dengeleyiciden kaldırılır. Sanal makinelerin durumunu izlemek için [az network lb probe create](https://docs.microsoft.com/cli/azure/network/lb/probe?view=azure-cli-latest) ile bir durum araştırması oluşturun. 

```azurecli-interactive
  az network lb probe create \
    --resource-group myResourceGroupSLB \
    --lb-name myLoadBalancer \
    --name myHealthProbe \
    --protocol tcp \
    --port 80   
```

### <a name="create-the-load-balancer-rule"></a>Yük dengeleyici kuralı oluşturma

Yük dengeleyici kuralı, gerekli kaynak ve hedef bağlantı noktalarının yanı sıra gelen trafik için ön uç IP yapılandırmasını ve trafiği almak için arka uç IP havuzunu tanımlar. *myFrontEnd* ön uç havuzunda 80 numaralı bağlantı noktasını dinlemek ve yine 80 numaralı bağlantı noktasını kullanarak *myBackEndPool* arka uç adres havuzuna yük dengelemesi yapılmış ağ trafiğini göndermek için [az network lb rule create](https://docs.microsoft.com/cli/azure/network/lb/rule?view=azure-cli-latest) ile *myLoadBalancerRuleWeb* yük dengeleyici kuralı oluşturun. 

```azurecli-interactive
  az network lb rule create \
    --resource-group myResourceGroupSLB \
    --lb-name myLoadBalancer \
    --name myHTTPRule \
    --protocol tcp \
    --frontend-port 80 \
    --backend-port 80 \
    --frontend-ip-name myFrontEnd \
    --backend-pool-name myBackEndPool \
    --probe-name myHealthProbe  
```

## <a name="configure-virtual-network"></a>Sanal ağ yapılandırma

VM’leri dağıtmadan ve dengeleyicinizi test etmeden önce yardımcı sanal ağ kaynaklarını oluşturun.

### <a name="create-a-virtual-network"></a>Sanal ağ oluşturma

[az network vnet create](https://docs.microsoft.com/cli/azure/network/vnet) komutunu kullanarak *myResourceGroup* içinde *mySubnet* adlı bir alt ağ ile *myVnet* adlı bir sanal ağ oluşturun.

```azurecli-interactive
  az network vnet create \
    --resource-group myResourceGroupSLB \
    --location eastus \
    --name myVnet \
    --subnet-name mySubnet
```

### <a name="create-a-network-security-group"></a>Ağ güvenlik grubu oluşturma

Standart Yük Dengeleyici için, arka uç adresindeki sanal makinelerin bir Ağ Güvenliği grubuna ait olan NIC’lere sahip olması gerekir. Sanal ağınıza gelen bağlantıları tanımlamak için ağ güvenlik grubu oluşturun.

```azurecli-interactive
  az network nsg create \
    --resource-group myResourceGroupSLB \
    --name myNetworkSecurityGroup
```

### <a name="create-a-network-security-group-rule"></a>Ağ güvenlik grubu kuralı oluşturma

80 numaralı bağlantı noktasından gelen bağlantılara izin vermek için bir ağ güvenlik grubu kuralı oluşturun.

```azurecli-interactive
  az network nsg rule create \
    --resource-group myResourceGroupSLB \
    --nsg-name myNetworkSecurityGroup \
    --name myNetworkSecurityGroupRuleHTTP \
    --protocol tcp \
    --direction inbound \
    --source-address-prefix '*' \
    --source-port-range '*' \
    --destination-address-prefix '*' \
    --destination-port-range 80 \
    --access allow \
    --priority 200
```

### <a name="create-nics"></a>NIC’leri oluşturma

[az network nic create](/cli/azure/network/nic#az-network-nic-create) ile üç ağ arabirimi oluşturun ve bunları Genel IP adresi ve ağ güvenlik grubu ile ilişkilendirin. 

```azurecli-interactive

  az network nic create \
    --resource-group myResourceGroupSLB \
    --name myNicVM1 \
    --vnet-name myVnet \
    --subnet mySubnet \
    --network-security-group myNetworkSecurityGroup \
    --lb-name myLoadBalancer \
    --lb-address-pools myBackEndPool

  az network nic create \
    --resource-group myResourceGroupSLB \
    --name myNicVM2 \
    --vnet-name myVnet \
    --subnet mySubnet \
    --network-security-group myNetworkSecurityGroup \
    --lb-name myLoadBalancer \
    --lb-address-pools myBackEndPool
  
  az network nic create \
    --resource-group myResourceGroupSLB \
    --name myNicVM3 \
    --vnet-name myVnet \
    --subnet mySubnet \
    --network-security-group myNetworkSecurityGroup \
    --lb-name myLoadBalancer \
    --lb-address-pools myBackEndPool

```

## <a name="create-backend-servers"></a>Arka uç sunucular oluşturma

Bu örnekte, yük dengeleyici için arka uç sunucular olarak kullanılacak üç sanal makine oluşturacaksınız. Yük dengeleyicinin başarıyla oluşturulduğunu doğrulamak için sanal makinelere NGINX de yükleyin.

Temel Genel IP'ye sahip bir Temel Yük Dengeleyicisi oluşturuyorsanız, sanal makinelerinizi eklemek için bir Kullanılabilirlik Kümesi oluşturmanız gerekir ([az vm availabilityset create.](/cli/azure/network/nic) Standart Yük Dengeleyicileri bu ek adımı gerektirmez. Microsoft, Standard'ı kullanmanızı önerir.

### <a name="create-three-virtual-machines"></a>Üç sanal makine oluşturun

cloud-init yapılandırma dosyasını kullanarak NGINX’i yükleyin ve Linux sanal makinesinde 'Merhaba Dünya' Node.js uygulaması çalıştırın. Geçerli kabuğunuzda cloud-init.txt adlı bir dosya oluşturup aşağıdaki yapılandırmayı kopyalayıp kabuğa yapıştırın. Başta birinci satır olmak üzere cloud-init dosyasının tamamını doğru bir şekilde kopyaladığınızdan emin olun:

```yaml
#cloud-config
package_upgrade: true
packages:
  - nginx
  - nodejs
  - npm
write_files:
  - owner: www-data:www-data
  - path: /etc/nginx/sites-available/default
    content: |
      server {
        listen 80;
        location / {
          proxy_pass http://localhost:3000;
          proxy_http_version 1.1;
          proxy_set_header Upgrade $http_upgrade;
          proxy_set_header Connection keep-alive;
          proxy_set_header Host $host;
          proxy_cache_bypass $http_upgrade;
        }
      }
  - owner: azureuser:azureuser
  - path: /home/azureuser/myapp/index.js
    content: |
      var express = require('express')
      var app = express()
      var os = require('os');
      app.get('/', function (req, res) {
        res.send('Hello World from host ' + os.hostname() + '!')
      })
      app.listen(3000, function () {
        console.log('Hello world app listening on port 3000!')
      })
runcmd:
  - service nginx restart
  - cd "/home/azureuser/myapp"
  - npm init
  - npm install express -y
  - nodejs index.js
```

[az vm create](/cli/azure/vm#az-vm-create) komutunu kullanarak sanal makineleri oluşturun.

```azurecli-interactive

  az vm create \
    --resource-group myResourceGroupSLB \
    --name myVM1 \
    --availability-set myAvailabilitySet \
    --nics myNicVM1 \
    --image UbuntuLTS \
    --generate-ssh-keys \
    --custom-data cloud-init.txt \
    --no-wait
   
  az vm create \
    --resource-group myResourceGroupSLB \
    --name myVM2 \
    --availability-set myAvailabilitySet \
    --nics myNicVM2 \
    --image UbuntuLTS \
    --generate-ssh-keys \
    --custom-data cloud-init.txt \
    --no-wait

   az vm create \
    --resource-group myResourceGroupSLB \
    --name myVM3 \
    --availability-set myAvailabilitySet \
    --nics myNicVM3 \
    --image UbuntuLTS \
    --generate-ssh-keys \
    --custom-data cloud-init.txt \
    --no-wait

```

Sanal makinelerin dağıtılması birkaç dakika sürebilir.

## <a name="test-the-load-balancer"></a>Yük dengeleyiciyi test etme

Yük dengeleyicinin genel IP adresini almak için [az network public-ip show](/cli/azure/network/public-ip#az-network-public-ip-show) komutunu kullanın. Genel IP adresini kopyalayıp tarayıcınızın adres çubuğuna yapıştırın.

```azurecli-interactive
  az network public-ip show \
    --resource-group myResourceGroupSLB \
    --name myPublicIP \
    --query [ipAddress] \
    --output tsv
```

   ![Yük dengeleyiciyi sınama](./media/load-balancer-standard-public-cli/running-nodejs-app.png)

## <a name="clean-up-resources"></a>Kaynakları temizleme

Artık gerekli değilse, [az group delete](/cli/azure/group#az-group-delete) komutunu kullanarak kaynak grubunu, yük dengeleyiciyi ve tüm ilgili kaynakları kaldırabilirsiniz.

```azurecli-interactive
  az group delete --name myResourceGroupSLB
```

## <a name="next-steps"></a>Sonraki adımlar
Bu hızlı başlangıçta, bir Standart Yük Dengeleyicisi oluşturdunuz, ona VM'ler iliştirdin, Yük Dengeleyicitrafik kuralını, sistem sondasını yapılandırdın ve ardından Yük Dengeleyicisini test ettiniz. Azure Yük Bakiyesi hakkında daha fazla bilgi edinmek için [Azure Yük Bakiyesi öğreticilerine](tutorial-load-balancer-standard-public-zone-redundant-portal.md)devam edin.

[Yük Dengeleyicisi ve Kullanılabilirlik bölgeleri](load-balancer-standard-availability-zones.md)hakkında daha fazla bilgi edinin.