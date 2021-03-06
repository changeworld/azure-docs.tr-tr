---
title: Azure'da sunucusuz kapsayıcılar
description: Azure Kapsayıcı Örnekleri hizmeti, sanal makineleri yönetmek zorunda kalmadan ve daha üst düzey bir orkestratör benimsemek zorunda kalmadan Azure'da yalıtılmış kapsayıcıları çalıştırmanın en hızlı ve en basit yolunu sunar.
ms.topic: overview
ms.date: 04/25/2019
ms.custom: seodec18, mvc
ms.openlocfilehash: c871c09e29b64c4f0dcd107361154efdce306481
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/26/2020
ms.locfileid: "79240257"
---
# <a name="what-is-azure-container-instances"></a>Azure Container Instances nedir?

Bulut uygulamalarını paketlemek, dağıtmak ve yönetmek için kapsayıcılar her geçen gün daha fazla tercih edilmektedir. Azure Container Instances, herhangi bir sanal makineyi yönetmek ve daha yüksek düzeyde bir hizmeti benimsemek zorunda kalmadan Azure içinde kapsayıcı çalıştırmanızın en hızlı ve en kolay yoludur.

Yalıtılmış kapsayıcılarda çalışabileceğiniz senaryolar (basit uygulamalar, görev otomasyonu ve sürüm işleri gibi) için Azure Container Instances harika bir çözümdür. Eksiksiz bir kapsayıcı düzenlemesi gerektiren senaryolar (birden çok kapsayıcıda hizmet bulma, otomatik ölçeklendirme ve eşgüdümlü uygulama yükseltmeleri gibi) için [Azure Kubernetes Service (AKS)](../aks/index.yml) hizmetini öneririz.

## <a name="fast-startup-times"></a>Hızlı başlangıç süreleri

Kapsayıcılar, sanal makinelerde (VM) önemli başlangıç süresi avantajları sunar. Azure Container Instances, sanal makineleri sağlamaya ve yönetmeye gerek kalmadan saniyeler içinde Azure’da kapsayıcıları başlatabilir.

## <a name="container-access"></a>Konteyner erişimi

Azure Kapsayıcı Örnekleri, konteyner gruplarınızı IP adresi ve tam nitelikli bir etki alanı adı (FQDN) ile doğrudan internete maruz bırakmanızı sağlar. Bir kapsayıcı örneği oluşturduğunuzda, uygulamanızın *customlabel*.*azureregion*.azurecontainer.io üzerinden ulaşılabilir olmasını sağlamak amacıyla bir özel DNS ad etiketi belirleyebilirsiniz.

Azure Kapsayıcı Örnekleri, uygulama geliştirme ve sorun giderme konusunda yardımcı olmak için etkileşimli bir kabuk sağlayarak çalışan bir kapsayıcıda komut yürütmeyi de destekler. Access, istemci bağlantılarını güvence altına almak için TLS'yi kullanarak HTTPS üzerinden yer alır.

> [!IMPORTANT]
> Azure Kapsayıcı Örnekleri, 13 Ocak 2020'den itibaren sunuculardan ve uygulamalardan TLS 1.2'yi kullanmak için tüm güvenli bağlantıları gerektirir. TLS 1.0 ve 1.1 desteği emekli yedirilecektir.

## <a name="hypervisor-level-security"></a>Hiper yönetici düzeyinde güvenlik

Geçmişte kapsayıcılar, uygulama bağımlılığı yalıtımı ve kaynak idaresi olanakları sağlıyor ancak birden çok kiracılı zorlu kullanımlar için yeterli kabul edilmiyordu. Azure Container Instances, uygulamanızın bir kapsayıcıda, sanal makinedeki gibi yalıtılmasını sağlar.


## <a name="custom-sizes"></a>Özel boyutlar

Kapsayıcılar genellikle tek bir uygulamayı çalıştırmak üzere iyileştirilmiştir, ancak söz konusu uygulamaların tam gereksinimleri önemli ölçüde farklı olabilir. Azure Container Instances, CPU çekirdeklerinin ve belleğin tam belirtimlerine imkan vererek en iyi kullanımı sağlar. İhtiyaçlarınıza göre ödeme yapar ve saniye başına faturalandırılırsınız. Böylece harcamalarınızı ihtiyaçlarınıza uyacak şekilde ayarlayabilirsiniz.

Makine öğrenimi gibi bilgi işlem yoğun işlerde Azure Kapsayıcı Örnekleri, Linux kaplarını NVIDIA Tesla [GPU kaynaklarını](container-instances-gpu.md) (önizleme) kullanacak şekilde zamanlayabilir.

## <a name="persistent-storage"></a>Kalıcı depolama

Azure Kapsayıcı Örnekleri'ni almak ve devam etmek için Azure Depolama tarafından desteklenen [Azure Dosyaları hisselerinin](container-instances-mounting-azure-files-volume.md) doğrudan montajını sunuyoruz.

## <a name="linux-and-windows-containers"></a>Linux ve Windows kapsayıcıları

Azure Container Instances, aynı API ile hem Windows hem de Linux kapsayıcıları zamanlayabilir. [Kapsayıcı gruplarınızı](container-instances-container-groups.md) oluştururken işletim sistemi türünü belirmeniz yeterlidir.

Bazı özellikler şu anda Linux kapsayıcıları ile sınırlıdır:

* Kapsayıcı grubu başına birden çok kapsayıcı
* Ses montajı ([Azure Files](container-instances-volume-azure-files.md), [emptyDir](container-instances-volume-emptydir.md), [GitRepo](container-instances-volume-gitrepo.md), [gizli](container-instances-volume-secret.md))
* Azure Monitor ile [kaynak kullanım ölçümleri](container-instances-monitor.md)
* [Sanal ağ dağıtımı](container-instances-vnet.md)
* [GPU kaynakları](container-instances-gpu.md) (önizleme)

Windows kapsayıcı dağıtımları için, ortak [Windows temel görüntülerine](container-instances-faq.md#what-windows-base-os-images-are-supported)dayalı görüntüleri kullanın.

> [!NOTE]
> Azure Kapsayıcı Örnekleri'nde Windows Server 2019 tabanlı görüntülerin kullanımı önizlemededir.

## <a name="co-scheduled-groups"></a>Birlikte zamanlanmış gruplar

Azure Kapsayıcı Örnekleri, ana bilgisayar makinesini, yerel ağı, depolamayı ve yaşam döngüsünü paylaşan [çok kapsayıcılı grupların](container-instances-container-groups.md) zamanlanmasını destekler. Böylece ana uygulama kapsayıcınızı, günlüğe kaydetme yan bölmesi gibi diğer destekleyici kapsayıcılarla birleştirebilirsiniz.

## <a name="virtual-network-deployment"></a>Sanal ağ dağıtımı

Şu anda Azure bölgelerinin bir alt kümesinde üretim iş yüklerinde kullanılabilir olan Azure Kapsayıcı Örnekleri'nin bu özelliği, [kapsayıcı örneklerinin bir Azure sanal ağına dağıtılmasını](container-instances-vnet.md)sağlar. Kapsayıcı örneklerini sanal ağınızdaki bir alt ağa dağıtarak şirket içindekiler dahil olmak üzere ([VPN ağ geçidi](../vpn-gateway/vpn-gateway-about-vpngateways.md) veya [ExpressRoute](../expressroute/expressroute-introduction.md) aracılığıyla) sanal ağ içindeki diğer kaynaklarla güvenli bir şekilde iletişim kurmalarını sağlayabilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar

Hızlı başlangıç kılavuzumuzu kullanarak bir kapsayıcıyı tek bir komutla Azure’a dağıtmayı deneyin:

> [!div class="nextstepaction"]
> [Azure Container Instances Hızlı Başlangıç](container-instances-quickstart.md)

<!-- LINKS - External -->
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/
