---
title: Giriş
description: Azure Uygulama Hizmet Ortamları'nın uygulamalarınızı tamamen yalıtılmış ve özel bir ortamda ölçeklendirmenize, güvenli hale getirmenize ve optimize edeyimize nasıl yardımcı olduğunu öğrenin.
author: ccompy
ms.assetid: 3c7eaefa-1850-4643-8540-428e8982b7cb
ms.topic: overview
ms.date: 04/19/2018
ms.author: ccompy
ms.custom: mvc, seodec18
ms.openlocfilehash: 9bc40e71c1da6eb60b62cd08c51df89a7f35af09
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/26/2020
ms.locfileid: "80057378"
---
# <a name="introduction-to-the-app-service-environments"></a>App Service Ortamlarına giriş #
 
## <a name="overview"></a>Genel Bakış ##

Azure App Service Ortamı, App Service uygulamalarını yüksek ölçekte güvenli olarak çalıştırmak için tamamen ayrı ve özel bir ortam sağlayan, bir Azure App Service özelliğidir. Bu özellik aşağıdaki öğelerinizi barındırabilir:

* Windows web uygulamaları
* Linux web uygulamaları 
* Docker kapsayıcıları
* Mobil uygulamalar
* İşlevler

App Service ortamları (ASE), şunları gerektiren uygulama iş yükleri için uygundur:

* Çok yüksek ölçek.
* Yalıtım ve güvenli ağ erişimi.
* Yüksek bellek kullanımı.

Müşteriler tek bir Azure bölgesinde veya birden fazla Azure bölgesi arasında birden çok ASE oluşturabilir. Bu esneklik ASE’leri yüksek RPS iş yüklerini desteklemek üzere durum bilgisi olmayan uygulama katmanlarını yatay yönde ölçeklendirmek için ideal hale getirir.

ASE’ler yalnızca tek bir müşterinin uygulamalarını çalıştırmak üzere yalıtılmıştır ve her zaman bir sanal ağa dağıtılır. Müşteriler gelen ve giden uygulama ağ trafiği üzerinde ayrıntılı denetime sahiptir. Uygulamalar VPN üzerinden şirket içi kurumsal kaynaklara yüksek hızda güvenli bağlantılar kurabilir.

* ASE kendi fiyatlandırma katmanıyla birlikte gelir. [Yalıtılmış teklifin](https://channel9.msdn.com/Shows/Azure-Friday/Security-and-Horsepower-with-App-Service-The-New-Isolated-Offering?term=app%20service%20environment) hiper ölçek ve güvenlik sağlamaya nasıl yardımcı olduğunu öğrenin.
* [App Service Ortamları v2](https://channel9.msdn.com/Blogs/Azure/Azure-Application-Service-Environments-v2-Private-PaaS-Environments-in-the-Cloud?term=app%20service%20environment) ağınızın bir alt ağında uygulamalarınızı çevreleyen bir koruma ve kendi özel Azure App Service dağıtımınızı sağlar.
* Yatay yönde ölçeklendirme için birden çok ASE kullanılabilir. Daha fazla bilgi için bkz. [Coğrafi olarak dağıtılmış bir uygulama ayak izi ayarlama](app-service-app-service-environment-geo-distributed-scale.md).
* ASE’ler, AzureCon Ayrıntılı Bakışında gösterildiği gibi güvenlik mimarisini yapılandırmak için kullanılabilir. AzureCon Ayrıntılı Bakışında gösterilen güvenlik mimarisinin nasıl yapılandırıldığını görmek için App Service ortamları ile [katmanlı güvenlik mimarisi uygulama makalesine](app-service-app-service-environment-layered-security.md) bakın.
* ASE’ler üzerinde çalışan uygulamalara erişim, web uygulaması güvenlik duvarları (WAF) gibi yukarı akış cihazları tarafından sağlanabilir. Daha fazla bilgi için bkz. [Web uygulaması güvenlik duvarı (WAF)][AppGW].
* Uygulama Hizmet Ortamları, bölge sabitleme kullanılarak Kullanılabilirlik Bölgeleri'ne (AZ) dağıtılabilir.  Daha fazla bilgi [için Kullanılabilirlik Bölgeleri için Uygulama Hizmeti Ortamı Desteği'ne][ASEAZ] bakın.

## <a name="dedicated-environment"></a>Ayrılmış ortam ##

Bir ASE yalnızca tek bir aboneliğe ayrılmıştır ve 100 App Service Planı örneği barındırabilir. Aralık tek bir App Service planında 100 örnek ile 100 tek örnekli App Service planı arasındaki bir değer ve bunların arasındaki her değer olabilir.

ASE, ön uçlar ve çalışanlardan oluşur. Ön uçlar HTTP/HTTPS sonlandırmadan ve bir ASE içindeki uygulama isteklerinin otomatik yük dengelemesinden sorumludur. ASE içindeki App Service planlarının ölçeği artırıldıkça ön uçlar otomatik olarak eklenir.

Çalışanlar, müşteri uygulamalarını barındıran rollerdir. Çalışanlar üç sabit boyutta mevcuttur:

* Bir vCPU/3,5 GB RAM
* İki vCPU/7 GB RAM
* Dört vCPU/14 GB RAM

Müşterilerin ön uçları ve çalışanları yönetmesi gerekmez. Müşteriler App Service planlarının ölçeğini genişlettikçe tüm altyapı otomatik olarak eklenir. Bir ASE’de App Service planları oluşturulduğunda veya ölçeklendirildiğinde, gerekli altyapı uygun şekilde eklenir veya kaldırılır.

ASE için altyapıya ilişkin ödeme yapan ve ASE’nin boyutuna göre değişmeyen sabit bir aylık fiyat mevcuttur. Ayrıca, App Service planı vCPU’su için bir maliyet mevcuttur. ASE'de barındırılan tüm uygulamalar, Yalıtılmış fiyatlandırma SKU’su içindedir. ASE fiyatlandırması hakkında bilgi için [App Service fiyatlandırma][Pricing] sayfasına bakın ve ASE’ler için kullanılabilir seçenekleri gözden geçirin.

## <a name="virtual-network-support"></a>Sanal ağ desteği ##

ASE özelliği, Azure Uygulama Hizmeti'nin doğrudan müşterinin Azure Kaynak Yöneticisi sanal ağına dağıtılmasıdır. Azure sanal ağları hakkında daha fazla bilgi için bkz. [Azure sanal ağları ile ilgili SSS](https://azure.microsoft.com/documentation/articles/virtual-networks-faq/). Bir ASE her zaman bir sanal ağda ve daha kesin bir şekilde bir sanal ağın alt ağında bulunur. Uygulamalarınıza ilişkin gelen ve giden ağ iletişimini denetlemek için sanal ağların güvenlik özelliklerini kullanabilirsiniz.

ASE bir genel IP adresi ile İnternet’e yönelik veya sadece bir Azure iç yük dengeleyici (ILB) adresi ile iç ağa yönelik olabilir.

[Ağ Güvenlik Grupları][NSGs], bir ASE’nin bulunduğu alt ağa gelen ağ iletişimini kısıtlar. WAF’ler ve ağ SaaS sağlayıcıları gibi yukarı akış cihazlarının ve hizmetlerinin arkasında uygulamaları çalıştırmak için NSG’leri kullanabilirsiniz.

Uygulamalar, iç veritabanları ve web hizmetleri gibi şirket kaynaklarına da sıklıkla erişmelidir. ASE’yi şirket içi ağınızla VPN bağlantısı olan bir sanal ağa dağıtırsanız, ASE’deki uygulamalar şirket içi kaynaklara erişebilir. Bu özellik, VPN’nin [siteden siteye](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-multi-site) veya [Azure ExpressRoute](https://azure.microsoft.com/services/expressroute/) VPN olmasından bağımsız olarak geçerli olabilir.

ASE’lerin sanal ağlar ve şirket ağlarla nasıl çalıştığı hakkında daha fazla bilgi için bkz. [App Service Ortamı ağ konuları][ASENetwork].

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Azure-Application-Service-Environments-v2-Private-PaaS-Environments-in-the-Cloud/player]

## <a name="app-service-environment-v1"></a>App Service Ortamı v1 ##

App Service Ortamının iki sürümü vardır: ASEv1 ve ASEv2. Yukarıdaki bilgiler ASEv2’yi temel alır. Bu bölümde ASEv1 ile ASEv2 arasındaki farklar gösterilmektedir. 

ASEv1’de tüm kaynakları el ile yönetmeniz gerekir. Buna ön uçlar, çalışanlar ve IP tabanlı SSL için kullanılan IP adresleri dahildir. App Service planınızın ölçeğini artırabilmeniz için, öncelikle planınızı barındırmak istediğiniz çalışan havuzunun ölçeğini artırmanız gerekir.

ASEv1, ASEv2’den farklı bir fiyatlandırma modeli kullanır. ASEv1’de ayrılmış her vCPU için ücret ödersiniz. Buna herhangi bir iş yükünü barındırmayan ön uçlar veya çalışanlar için kullanılan vCPU’lar dahildir. ASEv1’de bir ASE’nin varsayılan en büyük ölçek boyutu toplam 55 konaktır. Buna çalışanlar ve ön uçlar dahildir. ASEv1’in bir avantajı, klasik bir sanal ağa ve bir Resource Manager sanal ağına dağıtılabilmesidir. ASEv1 hakkında daha fazla bilgi için bkz. [App Service Ortamı v1’e giriş][ASEv1Intro].

<!--Links-->
[App Service Environments v2]: https://channel9.msdn.com/Blogs/Azure/Azure-Application-Service-Environments-v2-Private-PaaS-Environments-in-the-Cloud?term=app%20service%20environment
[Isolated offering]: https://channel9.msdn.com/Shows/Azure-Friday/Security-and-Horsepower-with-App-Service-The-New-Isolated-Offering?term=app%20service%20environment
[Intro]: ./intro.md
[MakeExternalASE]: ./create-external-ase.md
[MakeASEfromTemplate]: ./create-from-template.md
[MakeILBASE]: ./create-ilb-ase.md
[ASENetwork]: ./network-info.md
[UsingASE]: ./using-an-ase.md
[UDRs]: ../../virtual-network/virtual-networks-udr-overview.md
[NSGs]: ../../virtual-network/security-overview.md
[ConfigureASEv1]: app-service-web-configure-an-app-service-environment.md
[ASEv1Intro]: app-service-app-service-environment-intro.md
[webapps]: ../overview.md
[mobileapps]: ../../app-service-mobile/app-service-mobile-value-prop.md
[Functions]: ../../azure-functions/index.yml
[Pricing]: https://azure.microsoft.com/pricing/details/app-service/
[ARMOverview]: ../../azure-resource-manager/management/overview.md
[ConfigureSSL]: ../configure-ssl-certificate.md
[Kudu]: https://azure.microsoft.com/resources/videos/super-secret-kudu-debug-console-for-azure-web-sites/
[ASEWAF]: app-service-app-service-environment-web-application-firewall.md
[AppGW]: ../../application-gateway/waf-overview.md
[ASEAZ]: https://azure.github.io/AppService/2019/12/12/App-Service-Environment-Support-for-Availability-Zones.html
