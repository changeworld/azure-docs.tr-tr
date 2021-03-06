---
title: Azure VPN Ağ Geçidi Hakkında
description: Bir VPN ağ geçidinin ne olduğunu ve Azure sanal ağlarına bağlanmak için VPN ağ geçidini nasıl kullanacağınızı öğrenin. IPsec/IKE Siteden Siteye şirketler arası ve Sanal Ağlar arası çözümlerin yanı sıra Noktadan Siteye VPN dahil.
services: vpn-gateway
author: cherylmc
Customer intent: As someone with a basic network background, but is new to Azure, I want to understand the capabilities of Azure VPN Gateway so that I can securely connect to my Azure virtual networks.
ms.service: vpn-gateway
ms.topic: overview
ms.date: 01/10/2020
ms.author: cherylmc
ms.openlocfilehash: c4a406961444845fef783c47942924b01b7aa646
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/26/2020
ms.locfileid: "79241461"
---
# <a name="what-is-vpn-gateway"></a>VPN Ağ Geçidi nedir?

VPN ağ geçidi, genel İnternet üzerinden bir Azure sanal ağı ile şirket içi konum arasında şifrelenmiş trafik göndermek için kullanılan belirli bir sanal ağ geçidi türüdür. Ayrıca VPN ağ geçidini Microsoft ağı üzerinden Azure sanal ağları arasında şifrelenmiş trafik göndermek için de kullanabilirsiniz. Her sanal ağın yalnızca bir VPN ağ geçidi olabilir. Ancak, aynı VPN ağ geçidi ile birden fazla bağlantı oluşturabilirsiniz. Aynı VPN ağ geçidiyle birden fazla bağlantı oluşturduğunuzda, tüm VPN tünelleri kullanılabilir ağ geçidi bant genişliğini paylaşır.

## <a name="what-is-a-virtual-network-gateway"></a><a name="whatis"></a>Sanal ağ geçidi nedir?

Sanal ağ ağ geçidi, ağ *geçidi alt ağı*adı verilen belirli bir alt ağa dağıtılan iki veya daha fazla VM'den oluşur. Sanal ağ ağ geçidi VM'leri yönlendirme tabloları içerir ve belirli ağ geçidi hizmetlerini çalıştırın. Bu SANAL Ağ ağ ağ geçidi oluşturduğunuzda bu Sanal M'ler oluşturulur. Sanal ağ ağ geçidinin bir parçası olan VM'leri doğrudan yapılandıramazsınız.

Sanal ağ ağ geçidi için yapılandırdığınız ayarlardan biri ağ geçidi türüdür. Ağ geçidi türü, sanal ağ ağ geçidinin nasıl kullanılacağını ve ağ geçidinin gerçekleştirdiği eylemleri belirtir. Ağ geçidi türü 'Vpn', oluşturulan sanal ağ ağ geçidinin türünün ExpressRoute ağ geçidi yerine bir 'VPN ağ geçidi' olduğunu belirtir. Bir sanal ağ iki sanal ağ ağ geçidine sahip olabilir; bir VPN ağ geçidi ve bir ExpressRoute ağ geçidi - [birlikte varolan](#coexisting) bağlantı yapılandırmalarında olduğu gibi. Daha fazla bilgi için [Ağ Geçidi türlerine](vpn-gateway-about-vpn-gateway-settings.md#gwtype)bakın.

VPN ağ geçitleri Azure Kullanılabilirlik Bölgelerinde dağıtılabilir. Bu seçenek, sanal ağ geçitlerine dayanıklılık, ölçeklenebilirlik ve daha yüksek kullanılabilirlik getirir. Ağ geçitlerini Azure Kullanılabilirlik Alanları içinde dağıtmak, bir bölge içindeki ağ geçitlerini fiziksel ve mantıksal olarak birbirinden ayırırken, Azure ile şirket içi ağ bağlantınızı alan düzeyindeki hatalardan korur. bkz. [Azure Kullanılabilirlik Bölgelerinde ki bölge yedekli sanal ağ ağ geçitleri hakkında](about-zone-redundant-vnet-gateways.md)

Bir sanal ağ geçidinin oluşturulması 45 dakika sürebilir. Bir sanal ağ geçidi oluşturduğunuzda ağ geçidi VM’leri ağ geçidi alt ağına dağıtılır ve belirttiğiniz ayarlarla yapılandırılır. Bir VPN ağ geçidi oluşturduktan sonra bu VPN ağ geçidi ile başka bir VPN ağ geçidi arasında bir IPsec/IKE VPN tüneli bağlantısı (Sanal Ağlar arası) oluşturabilir veya VPN ağ geçidi ile bir şirket içi VPN cihazı (Siteden Siteye) arasında IPsec/IKE VPN tünel bağlantısı oluşturabilirsiniz. Ayrıca, sanal ağınıza konferans veya evden gibi uzak bir konumdan bağlanmanızı sağlayan bir Noktaya Bağlantı VPN bağlantısı (OpenVPN, IKEv2 veya SSTP üzerinden VPN) da oluşturabilirsiniz.

## <a name="configuring-a-vpn-gateway"></a><a name="configuring"></a>VPN Gateway yapılandırma

VPN ağ geçidi bağlantısı belirli ayarlarla yapılandırılmış birden fazla kaynağı kullanır. Kaynakların birçoğu ayrı ayrı yapılandırılabilir, ancak bazı kaynakların belirli bir sırayla yapılandırılması gerekir.

### <a name="settings"></a><a name="settings"></a>Ayarlar

Her kaynak için seçtiğiniz ayarlar başarılı bir bağlantı oluşturmak için önemlidir. VPN Gateway’e ilişkin kaynaklar ve ayarlar hakkında bilgi için bkz. [VPN Gateway ayarları hakkında](vpn-gateway-about-vpn-gateway-settings.md). Makale; ağ geçidi türleri, ağ geçidi SKU’ları, VPN türleri, bağlantı türleri, ağ geçidi alt ağları, yerel ağ geçitleri ve kullanmayı düşünebileceğiniz diğer çeşitli ayarlar hakkında bilgi içerir.

### <a name="deployment-tools"></a><a name="tools"></a>Dağıtım araçları

Azure portalı gibi bir yapılandırma aracını kullanarak kaynakları oluşturmaya ve yapılandırmaya başlayabilirsiniz. Daha sonra ek kaynaklar yapılandırmak ya da uygun olduğunda var olan kaynakları değiştirmek için PowerShell gibi başka bir araca geçmeye karar verebilirsiniz. Şu anda Azure portalında her kaynağı ve kaynak ayarını yapılandıramazsınız. Her bağlantı topolojisine ilişkin makalelerdeki yönergelerde, belirli bir aracının ne zaman gerekli olduğu belirtilmektedir. 

### <a name="deployment-model"></a><a name="models"></a>Dağıtım modeli

Şu anda Azure için iki dağıtım modeli vardır. VPN Gateway'i yapılandırırken uygulayacağınız adımlar, sanal ağınızı oluşturmak için kullandığınız dağıtım modeline bağlıdır. Örneğin,VNet'inizi klasik dağıtım modeli kullanarak oluşturduysanız VPN ağ geçidi ayarlarınızı oluşturmak ve yapılandırmak için klasik dağıtım modeline ilişkin yönergeleri kullanırsınız. Dağıtım modelleri hakkında daha fazla bilgi için bkz. [Resource Manager’ı ve klasik dağıtım modellerini anlama](../azure-resource-manager/management/deployment-models.md).

### <a name="planning-table"></a><a name="planningtable"></a>Planlama tablosu

Aşağıdaki tablo çözümünüz için en iyi bağlantı seçeneğine karar vermenize yardımcı olabilir.

[!INCLUDE [cross-premises](../../includes/vpn-gateway-cross-premises-include.md)]

## <a name="gateway-skus"></a><a name="gwsku"></a>Ağ Geçidi SKU'ları

Bir sanal ağ geçidi oluşturduğunuzda, kullanmak istediğiniz ağ geçidi SKU’sunu belirtmelisiniz. İş yükü, aktarım hızı, özellik ve SLA türlerine bağlı olarak gereksinimlerinize uyan SKU’ları seçin.

* Desteklenen özellikler, üretim ve geliştirme testi ve yapılandırma adımları da dahil olmak üzere ağ geçidi [SUS'ları](vpn-gateway-about-vpn-gateway-settings.md#gwsku) hakkında daha fazla bilgi için VPN Ağ Geçidi Ayarları - Ağ Geçidi SKUs makalesine bakın.
* Eski SKU bilgileri için [bkz.](vpn-gateway-about-skus-legacy.md)

### <a name="gateway-skus-by-tunnel-connection-and-throughput"></a><a name="benchmark"></a>Tünele, bağlantıya ve performansa göre Ağ Geçidi SKU’ları

[!INCLUDE [Aggregated throughput by SKU](../../includes/vpn-gateway-table-gwtype-aggtput-include.md)]

## <a name="connection-topology-diagrams"></a><a name="diagrams"></a>Bağlantı topolojisi diyagramları

VPN ağ geçidi bağlantıları için kullanılabilecek farklı yapılandırmalar vardır. Gereksinimlerinize en uygun yapılandırmayı belirlemeniz gerekir. Aşağıdaki bölümlerde, aşağıdaki VPN ağ geçidi bağlantıları hakkında bilgi ve topoloji diyagramlarını görüntüleyebilirsiniz: Aşağıdaki bölümlerde şu listeleri içeren tablolar bulunur:

* Kullanılabilir dağıtım modeli
* Kullanılabilir yapılandırma araçları
* Varsa sizi doğrudan bir makaleye yönlendiren bağlantılar

Gereksinimlerinize uygun bağlantı topolojisini seçmenize yardımcı olması için diyagramları ve açıklamaları kullanabilirsiniz. Diyagramlarda temel topolojilerin başlıca olanları gösterilmektedir ancak diyagramları bir kılavuz olarak kullanıp daha karmaşık yapılandırmalar da oluşturabilirsiniz.

## <a name="site-to-site-and-multi-site-ipsecike-vpn-tunnel"></a><a name="s2smulti"></a>Siteden Siteye ve Çok Siteli (IPsec/IKE VPN tüneli)

### <a name="site-to-site"></a><a name="S2S"></a>Siteden Siteye

Siteden Siteye (S2S) VPN ağ geçidi bağlantısı, IPSec/IKE (IKEv1 veya IKEv2) VPN tüneli üzerinden kurulan bir bağlantıdır. S2S bağlantıları, şirket içi ve dışı yapılandırmalar ile birlikte karma yapılandırmalar için kullanılabilir. S2S bağlantısı, şirket içinde bulunan ve kendisine atanmış genel bir IP adresine sahip bir VPN aygıtı gerektirir. VPN cihazı seçme hakkında daha fazla bilgi için bkz. [VPN Gateway SSS - VPN cihazları](vpn-gateway-vpn-faq.md#s2s).

![Azure VPN Gateway Siteden Siteye bağlantı örneği](./media/vpn-gateway-about-vpngateways/vpngateway-site-to-site-connection-diagram.png)

### <a name="multi-site"></a><a name="Multi"></a>Çok Siteli

Bu türden bir bağlantı, Siteden Siteye bağlantının bir çeşididir. Sanal ağ geçidinizden genellikle birden fazla şirket içi siteye bağlanan birden fazla VPN bağlantısı oluşturursunuz. Birden fazla bağlantıyla çalışırken Yol Tabanlı VPN türü (klasik sanal ağlar ile çalışırken “dinamik ağ geçidi” adıyla kullanılır) kullanmanız gerekir. Her sanal ağın yalnızca bir VPN ağ geçidi olabileceğinden, ağ geçidi boyunca tüm bağlantılar mevcut bant genişliğini paylaşır. Bu bağlantı türü genellikle "çok siteli" bağlantı olarak adlandırılır.

![Azure VPN Gateway Çok Siteli bağlantı örneği](./media/vpn-gateway-about-vpngateways/vpngateway-multisite-connection-diagram.png)

### <a name="deployment-models-and-methods-for-site-to-site-and-multi-site"></a>Siteden Siteye ve Çok Siteli için dağıtım modelleri ve yöntemleri

[!INCLUDE [site-to-site and multi-site table](../../includes/vpn-gateway-table-site-to-site-include.md)]

## <a name="point-to-site-vpn"></a><a name="P2S"></a>Noktadan Siteye VPN

Noktadan Siteye (P2S) VPN ağ geçidi bağlantısı, ayrı bir istemci bilgisayardan sanal ağınıza güvenli bir bağlantı oluşturmanıza olanak sağlar. P2S bağlantısı, istemci bilgisayardan başlatılarak oluşturulur. Bu çözüm, Azure sanal ağlarına uzak bir konumdan (örneğin, evden veya bir konferanstan) bağlanmak isteyen uzaktan çalışan kişiler için kullanışlıdır. P2S VPN ayrıca, bir sanal ağa bağlanması gereken yalnızca birkaç istemciniz olduğunda S2S VPN yerine kullanabileceğiniz yararlı bir çözümüdür.

S2S bağlantılarının aksine, P2S bağlantıları için şirket içi genel kullanıma yönelik IP adresi veya VPN cihazı gerekmez. Her iki bağlantı için tüm yapılandırma gereksinimlerinin uyumlu olması şartıyla P2S bağlantıları aynı VPN ağ geçidi üzerinden S2S bağlantılarıyla birlikte kullanılabilir. Noktadan Siteye bağlantılar hakkında daha fazla bilgi edinmek için bkz. [Noktadan Siteye VPN hakkında](point-to-site-about.md).

![Azure VPN Gateway Noktadan Siteye bağlantı örneği](./media/vpn-gateway-about-vpngateways/point-to-site.png)

### <a name="deployment-models-and-methods-for-p2s"></a>P2S için dağıtım modelleri ve yöntemleri

[!INCLUDE [vpn-gateway-table-site-to-site](../../includes/vpn-gateway-table-point-to-site-include.md)]

## <a name="vnet-to-vnet-connections-ipsecike-vpn-tunnel"></a><a name="V2V"></a>Sanal Ağdan Sanal Ağa bağlantılar (IPsec/IKE VPN tüneli)

Bir sanal ağı başka bir sanal ağa bağlamak (VNet'ten VNet'e), bir VNet'i şirket içi site konumuna bağlamakla aynıdır. Her iki bağlantı türü de IPsec/IKE kullanarak güvenli bir tünel sunmak üzere bir VPN ağ geçidi kullanır. Hatta Sanal Ağdan Sanal Ağa iletişimini çok siteli bağlantı yapılandırmalarıyla bile birleştirebilirsiniz. Bu özellik şirket içi ve şirket dışı bağlantıyla ağ içi bağlantıyı birleştiren ağ topolojileri kurabilmenize olanak sağlar.

Bağladığınız VNet'ler,

* aynı veya farklı bölgelerde olabilir
* aynı veya farklı aboneliklerde olabilir 
* aynı veya farklı dağıtım modellerinde olabilir

![Azure VPN Gateway Sanal Ağdan Sanal Ağa bağlantı örneği](./media/vpn-gateway-about-vpngateways/vpngateway-vnet-to-vnet-connection-diagram.png)

### <a name="connections-between-deployment-models"></a>Dağıtım modelleri arasındaki bağlantılar

Azure'ın şu anda iki dağıtım modeli vardır: Klasik ve Resource Manager. Azure'ı bir süredir kullanıyorsanız klasik VNet'te çalışan Azure VM'leriniz ve örnek rollerinizin olması olasıdır. Daha yeni VM'leriniz ve rol örnekleriniz Resource Manager'da oluşturulan bir VNet'te çalışıyor olabilir. Bir VNet'teki kaynakların bir diğerindeki kaynaklarla doğrudan iletişim kurabilmesini sağlamak üzere VNet'ler arasında bir bağlantı oluşturabilirsiniz.

### <a name="vnet-peering"></a>VNet eşlemesi

Sanal ağınız belirli gereksinimleri karşılıyorsa bağlantınızı oluşturmak için VNet eşlemesini kullanabilirsiniz. VNet eşlemesi sanal ağ geçidini kullanmaz. Daha fazla bilgi için bkz. [VNet eşlemesi](../virtual-network/virtual-network-peering-overview.md).

### <a name="deployment-models-and-methods-for-vnet-to-vnet"></a>Sanal Ağdan Sanal Ağa dağıtım modelleri ve yöntemleri

[!INCLUDE [vpn-gateway-table-vnet-to-vnet](../../includes/vpn-gateway-table-vnet-to-vnet-include.md)]

## <a name="expressroute-private-connection"></a><a name="ExpressRoute"></a>ExpressRoute (özel bağlantı)

ExpressRoute, bağlantı sağlayıcı tarafından kolaylaştırılan özel bağlantı üzerinden şirket içi ağlarınızı Microsoft bulutuna genişletmenizi sağlar. ExpressRoute ile Microsoft Azure, Office 365 ve CRM Online gibi Microsoft bulut hizmetlerine bağlantı kurabilirsiniz. Ortak yerleşim tesisinde bağlantı sağlayıcısı üzerinden herhangi bir ağdan herhangi bir ağa (IP VPN), noktadan noktaya Ethernet ağı veya sanal çapraz bağlantısından bağlantı olabilir. 

ExpressRoute bağlantıları ortak İnternet üzerinden geçmemektedir. Bu, ExpressRoute bağlantılarına İnternet üzerindeki sıradan bağlantılara göre daha fazla güvenilirlik, yüksek hız, düşük gecikme ve normal bağlantılardan daha yüksek güvenlik sağlar.

ExpressRoute bağlantısı, zorunlu yapılandırmasının bir parçası olarak sanal ağ geçidi kullanır. Bir ExpressRoute bağlantısında sanal ağ geçidi 'Vpn' yerine 'ExpressRoute' ile yapılandırılır. Bir ExpressRoute devresi üzerinden geçen trafik varsayılan olarak şifrelenmiş olmasa da, bir ExpressRoute devresi üzerinden şifrelenmiş trafik göndermenize olanak tanıyan bir çözüm oluşturmanız mümkündür. ExpressRoute hakkında daha fazla bilgi için [ExpressRoute teknik genel bakış](../expressroute/expressroute-introduction.md)ına bakın.

## <a name="site-to-site-and-expressroute-coexisting-connections"></a><a name="coexisting"></a>Siteden Siteye ve ExpressRoute birlikte varolan bağlantılar

ExpressRoute, WAN bağlantınızdan (genel İnternet üzerinden değil) Azure dahil olmak üzere Microsoft Hizmetlerine doğrudan, özel olarak gerçekleştirilen bir bağlantıdır. Siteden Siteye VPN trafiği genel İnternet üzerinden şifrelenmiş olarak hareket eder. Aynı sanal ağ için Siteden Siteye VPN ve ExpressRoute bağlantıları yapılandırabiliyor olmanın çeşitli avantajları vardır.

ExpressRoute için güvenli bir yük devretme yolu olarak Siteden Siteye VPN yapılandırabilir veya ağınızın parçası olmayıp ExpressRoute üzerinden bağlanılan sitelere bağlanmak için Siteden Siteye VPN'ler kullanabilirsiniz. Bu yapılandırma, aynı sanal ağ için biri ‘Vpn’, diğeri ‘ExpressRoute’ ağ geçidi türünü kullanan iki sanal ağ geçidinin kullanılmasını gerektirir.

![ExpressRoute ve VPN Gateway eşzamanlı bağlantı örneği](./media/vpn-gateway-about-vpngateways/expressroute-vpngateway-coexisting-connections-diagram.png)

### <a name="deployment-models-and-methods-for-s2s-and-expressroute-coexist"></a>S2S ve ExpressRoute dağıtım modelleri ve yöntemleri aynı anda mevcut

[!INCLUDE [vpn-gateway-table-coexist](../../includes/vpn-gateway-table-coexist-include.md)]

## <a name="pricing"></a>Fiyatlandırma

[!INCLUDE [vpn-gateway-about-pricing-include](../../includes/vpn-gateway-about-pricing-include.md)]

VPN Gateway’e yönelik ağ geçidi SKU’ları hakkında bilgi için bkz. [Ağ geçidi SKU’ları](vpn-gateway-about-vpn-gateway-settings.md#gwsku).

## <a name="faq"></a><a name="faq"></a>SSS

VPN Gateway hakkında sık sorulan sorular için bkz. [VPN Gateway hakkında SSS](vpn-gateway-vpn-faq.md).

## <a name="next-steps"></a>Sonraki adımlar

- Daha fazla bilgi edinmek için [VPN Gateway ile ilgili SSS](vpn-gateway-vpn-faq.md) makalesini görüntüleyin.
- [Abonelik ve hizmet sınırlamaları](../azure-resource-manager/management/azure-subscription-service-limits.md#networking-limits) makalesini görüntüleyin.
- Azure'un diğer önemli [ağ özelliklerinden](../networking/networking-overview.md) bazıları hakkında bilgi edinin.
