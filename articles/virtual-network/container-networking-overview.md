---
title: Azure Sanal Ağ ile kapsayıcı ağı oluşturma | Microsoft Docs
description: Kapsayıcıları bir Azure Sanal Ağı kullanacak şekilde yapılandırmayı öğrenin.
services: virtual-network
documentationcenter: na
author: aanandr
manager: NarayanAnnamalai
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 9/18/2018
ms.author: aanandr
ms.custom: ''
ms.openlocfilehash: bdd364c097552d3a1b52073af97d33db70d78556
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/26/2020
ms.locfileid: "75647450"
---
# <a name="enable-containers-to-use-azure-virtual-network-capabilities"></a>Kapsayıcıların Azure Sanal Ağ özelliklerini kullanmasını sağlama

Sanal makinelerin kullandığı yazılımla tanımlanan ağ yığınını kullanarak gelişmiş Azure ağ özelliklerini kapsayıcılara ekleyin. Azure Sanal Ağ kapsayıcı ağı arabirimi (CNI) eklentisi bir Azure Sanal Makinesine yüklenir. Eklenti, sanal makinede oluşturulan kapsayıcılara bir sanal ağdan IP adresi atayarak onları sanal ağa ekler ve doğrudan diğer kapsayıcılar ile sanal ağ kaynaklarına bağlar. Eklenti, bağlantı için ağ katmanlarını veya yolları kullanmaz ve sanal makinelerle aynı performansı sunar. Eklentinin sunduğu özelliklerden en önemli olanları aşağıda verilmiştir:

- Her poda bir sanal ağ IP adresi atanır ve bir podda bir veya daha fazla kapsayıcı bulunabilir.
- Podlar eşlenen sanal ağlara veya ExpressRoute ya da siteden siteye VPN bağlantısı üzerinden şirket içi ağlara bağlanabilir. Eşlenen veya şirket içi ağlardan da podlara erişim sağlanabilir.
- Podlar sanal ağ hizmet uç noktalarıyla korunan Azure Depolama ve SQL Veritabanı gibi hizmetlere erişebilir.
- Ağ güvenlik grupları ve yollar doğrudan podlara uygulanabilir.
- Podlar sanal makineler gibi doğrudan bir Azure iç veya genel Yük Dengeleyici arkasına yerleştirilebilir.
- Podlara genel IP adresi atanarak doğrudan internet üzerinden erişim sağlanabilir. Podlar da internete erişebilir.
- Hizmetler, Giriş denetleyicileri ve Kube DNS gibi Kubernetes kaynaklarıyla sorunsuz bir şekilde çalışır. Bir Kubernetes Hizmeti, Azure Load Balancer aracılığıyla iç veya dış kullanıma sunulabilir.

Aşağıdaki resimde eklentinin podlara Azure Sanal Makinesi özelliklerini nasıl sağladığı gösterilmiştir:

![Kapsayıcı ağına genel bakış](./media/container-networking/container-networking-overview.png)

Eklenti hem Linux hem de Windows platformlarını destekler.

## <a name="connecting-pods-to-a-virtual-network"></a>Podları sanal ağa bağlama

Podlar sanal ağın parçası olan bir sanal makinede oluşturulur. Sanal makinenin ağ arabiriminde ikinci adres olarak podlar için bir IP adresi havuzu yapılandırılır. Azure CNI podlar için temel ağ bağlantısı ayarlarını yapar ve havuzdaki IP adreslerinin kullanımını yönetir. Sanal makinede bir pod oluşturulduğunda Azure CNI havuzdan kullanılabilir IP adresi atar ve podu sanal makinedeki yazılım köprüsüne bağlar. Pod sonlandırıldığında IP adresi havuza geri gönderilir. Aşağıdaki resimde podların sanal ağa nasıl bağlandığı gösterilmiştir:

![Kapsayıcı ağı ayrıntıları](./media/container-networking/container-networking-detail.png)

## <a name="internet-access"></a>İnternet erişimi

Podların internete erişmesini sağlamak için eklenti *iptables* kurallarını yapılandırarak podlardan internete giden trafikte ağ adresi çevirisi (NAT) gerçekleştirir. Paketin kaynak IP adresi sanal makine ağ arabiriminin birincil IP adresine çevrilir. Windows sanal makineleri otomatik olarak sanal makinenin bulunduğu alt ağın dışında bir IP adresine gönderilen NAT (SNAT) trafiği için kaynak oluşturur. Genellikle sanal ağın IP aralığının dışındaki bir IP adresine gönderilen tüm trafik çevrilir.

## <a name="limits"></a>Sınırlar

Eklenti sanal makine başına 250 pod ve bir sanal ağda 16.000 pod için destek sunar. Bu sınırlar [Azure Kubernetes Service](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-kubernetes-service-limits) için farklıdır.

## <a name="using-the-plug-in"></a>Eklentiyi kullanma

Eklenti, podlar veya Docker kapsayıcılarına temel sanal ağ bağlantısı sağlamak üzere şu şekillerde kullanılabilir:

- **Azure Kubernetes Service**: Eklenti Azure Kubernetes Service (AKS) ile tümleşiktir ve *Gelişmiş Ağ* seçeneği ile kullanılabilir. Gelişmiş Ağ, Kubernetes kümesini var olan veya yeni bir sanal ağa dağıtmanızı sağlar. Gelişmiş Ağ ve ayarlama adımları hakkında daha fazla bilgi için bkz. [AKS'de ağ yapılandırması](../aks/networking-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
- **AKS-Engine**: AKS-Engine, Azure'da bir Kubernetes kümesinin dağıtımı için bir Azure Kaynak Yöneticisi şablonu oluşturan bir araçtır. Ayrıntılı talimatlar [için, AKS-Engine Kubernetes kümeleri için eklentiyi dağıt'a](deploy-container-networking.md#deploy-the-azure-virtual-network-container-network-interface-plug-in)bakın.
- **Azure'da kendi Kubernetes kümenizi oluşturma**: Eklenti, AKS'ye veya AKS Motoru gibi araçlara güvenmeden, kendi başınıza dağıttığınız Kubernetes kümelerinde podlar için temel ağ sağlamak için kullanılabilir. Bu durumda eklentinin kümedeki tüm sanal makinelere yüklenmesi ve etkinleştirilmesi gerekir. Ayrıntılı yönergeler için bkz. [Eklentiyi kendi dağıttığınız Kubernetes kümesi için dağıtma](deploy-container-networking.md#deploy-plug-in-for-a-kubernetes-cluster).
- **Azure'daki Docker kapsayıcılarına sanal ağ ekleme**: Eklenti bir Kubernetes kümesi kullanmak istemediğiniz ve sanal makinelerde sanal ağa eklenmiş Docker kapsayıcısı oluşturmak istediğiniz durumlarda kullanılabilir. Ayrıntılı yönergeler için bkz. [Eklentiyi Docker için dağıtma](deploy-container-networking.md#deploy-plug-in-for-docker-containers).

## <a name="next-steps"></a>Sonraki adımlar

Kubernetes kümeleri veya Docker kapsayıcıları için [eklentiyi dağıtma](deploy-container-networking.md)
