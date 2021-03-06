---
title: 'Bağlantı sağlayıcıları ve konumları: Azure ExpressRoute | Microsoft Docs'
description: Bu makale, sunulan hizmetlerin konumları ve Azure bölgelerine nasıl bağlanılacağı hakkında ayrıntılı bir genel bakış sağlar. Bağlantı sağlayıcısına göre sıralanır.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.workload: infrastructure-services
ms.date: 03/25/2020
ms.author: cherylmc
ms.openlocfilehash: 913bc16f7408acebb0e65359e8845ce0f6a3a2f4
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/14/2020
ms.locfileid: "81314258"
---
# <a name="expressroute-partners-and-peering-locations"></a>ExpressRoute ortakları ve eşleme konumları

> [!div class="op_single_selector"]
> * [Sağlayıcıya Göre Konumlar](expressroute-locations.md)
> * [Konuma Göre Sağlayıcılar](expressroute-locations-providers.md)


Bu makaledeki tablolar, ExpressRoute coğrafi kapsamı ve konumları, ExpressRoute bağlantı sağlayıcıları ve ExpressRoute Sistem Entegratörleri (SI) hakkında bilgi sağlar.

> [!Note]
> Azure bölgeleri ve ExpressRoute konumları iki farklı ve farklı kavramdır, ikisi arasındaki farkı anlamak Azure karma ağ bağlantısını keşfetmek için çok önemlidir. 
>
>

## <a name="azure-regions"></a>Azure bölgeleri
Azure bölgeleri, Azure bilgi işlem, ağ ve depolama kaynaklarının bulunduğu genel veri merkezleridir. Azure kaynağı oluştururken, müşterinin bir kaynak konumu seçmesi gerekir. Kaynak konumu, kaynağın hangi Azure veri merkezinde (veya kullanılabilirlik bölgesinde) oluşturulduğunu belirler.

## <a name="expressroute-locations"></a>ExpressRoute konumları
ExpressRoute konumları (bazen eşleme konumları veya buluşma konumları olarak da adlandırılır), Microsoft Enterprise Edge (MSEE) aygıtlarının bulunduğu ortak konum olanaklarıdır. ExpressRoute konumları Microsoft'un ağının giriş noktasıdır ve müşterilere microsoft'un dünya çapındaki ağına bağlanma fırsatı sağlayarak küresel olarak dağıtılır. Bu konumlar, ExpressRoute iş ortaklarının ve ExpressRoute Direct müşterilerinin Microsoft ağına çapraz bağlantı lar sayönettiği konumlardır. Genel olarak, ExpressRoute konumunun Azure bölgesiyle eşleşmesi gerekmez. Örneğin, bir müşteri *Seattle* Peering konumunda, *Doğu ABD,* kaynak konumu ile bir ExpressRoute devre oluşturabilirsiniz.

Coğrafi bölge içindeki en az bir ExpressRoute konumuna bağlanırsanız coğrafi bölge içindeki tüm bölgeler arasında Azure hizmetlerine erişebileceksiniz.

## <a name="azure-regions-to-expressroute-locations-within-a-geopolitical-region"></a><a name="locations"></a>Azure bölgeleri ile bir jeopolitik bölge içindeki ExpressRoute konumları.
Aşağıdaki tablo, coğrafi bölge içindeki Azure bölgeler ile ExpressRoute konumları arasında yapılan eşlemeyi sağlar.

| **Jeopolitik bölge** | **Azure bölgeleri** | **ExpressRoute konumları** |
| --- | --- | --- |
| **Australia Government** |Orta Avustralya, Orta Avustralya 2 |Kanberra, Kanberra2 |
| **Avrupa** | Fransa Merkez, Fransa Güney, Almanya Kuzey, Almanya Batı Orta, Kuzey Avrupa, Norveç Doğu, Norveç Batı, İsviçre Kuzey, İsviçre Batı, İngiltere Batı, İngiltere Güney, Batı Avrupa |Amsterdam, Amsterdam2, Kopenhag, Dublin, Frankfurt, Cenevre, Londra, Londra2, Marsilya, Milano, Münih, Newport(Galler), Oslo, Paris, Stavanger, Stockholm, Zürih |
| **Kuzey Amerika** |Doğu ABD, Batı ABD, Doğu ABD 2, Batı ABD 2, Orta ABD, Orta Güney ABD, Orta Kuzey ABD, Orta Batı ABD, Orta Kanada, Doğu Kanada |Atlanta, Chicago, Dallas, Denver, Las Vegas, Los Angeles, Miami, New York, San Antonio, Seattle, Silikon Vadisi, Silikon Vadisi2, Washington DC, Washington DC2, Montreal, Quebec City, Toronto, Vancouver |
| **Asya** | Doğu Asya, Güneydoğu Asya | Bangkok, Hong Kong, Hong Kong2, Jakarta, Kuala Lumpur, Singapur, Singapur2, Taipei |
| **Hindistan** | Hindistan Batı, Hindistan Orta, Hindistan Güney |Madras, Madras2, Bombay, Bombay2 |
| **Japonya** | Batı Japonya, Doğu Japonya |Osaka, Tokyo, Tokyo2 |
| **Okyanusya** | Güneydoğu Avustralya, Doğu Avustralya |Auckland, Melbourne, Perth, Sidney, Sidney2 |
| **Güney Kore** | Güney Kore - Orta, Güney Kore - Güney |Busan, Seul|
| **BAE** | BAE Merkez, BAE Kuzey | Dubai, Dubai2 |
| **Güney Afrika** | Güney Afrika Batı, Güney Afrika Kuzey |Cape Town, Johannesburg |
| **Güney Amerika** | Güney Brezilya |Sao Paulo |


## <a name="regions-and-geopolitical-boundaries-for-national-clouds"></a>Ulusal bulutlar için bölgeler ve coğrafi sınırlar
Aşağıdaki tablo ulusal bulutlar için bölgeler ve coğrafi sınırlar hakkında bilgi sağlar.

| **Jeopolitik bölge** | **Azure bölgeleri** | **ExpressRoute konumları** |
| --- | --- | --- |
| **ABD bulutu** |US Gov Arizona, US Gov Iowa, US Gov Teksas, US Gov Virginia, Orta US DoD, Doğu US DoD  |Atlanta, Chicago, Dallas, New York, Phoenix, San Antonio, Seattle, Silikon Vadisi, Washington DC |
| **Doğu Çin** |Doğu Çin, Doğu Çin2 |Şangay, Şangay2 |
| **Kuzey Çin** |Kuzey Çin, Kuzey Çin2 |Pekin, Pekin2 |
| **Almanya** |Orta Almanya, Almanya Doğu |Berlin, Frankfurt |

Coğrafi bölgeler arasındaki bağlantı standart ExpressRoute SKU’da desteklenmiyor. Genel bağlantıyı desteklemek için ExpressRoute premium eklentisini etkinleştirmeniz gerekir. Ulusal bulut ortamlarına bağlantı desteklenmiyor. Bu tür bir ihtiyaç ortaya çıkarsa bağlantı sağlayıcınız ile çalışabilirsiniz.

## <a name="expressroute-connectivity-providers"></a><a name="partners"></a>ExpressRoute bağlantı sağlayıcıları

Aşağıdaki tabloda hizmet sağlayıcısına göre konumlar gösterilmektedir. Kullanılabilir sağlayıcıları konuma göre görüntülemek istiyorsanız bkz. [Konuma göre hizmeti sağlayıcılar](expressroute-locations-providers.md).


### <a name="global-commercial-azure"></a>Küresel ticari Azure

| **Hizmet sağlayıcı** | **Microsoft Azure** | **Office 365**  | **Konumlar** |
| --- | --- | --- | --- |
| **[AARNet](https://www.aarnet.edu.au/network-and-services/connectivity-services/azure-expressroute)** |Destekleniyor |Destekleniyor |Melbourne, Sidney |
| **[Airtel](https://www.airtel.in/business/#/)** | Destekleniyor | Destekleniyor | Madras2, Madras2 |
| **[Aıs](https://business.ais.co.th/solution/en/azure-expressroute.html)** | Destekleniyor | Destekleniyor | Bangkok |
| **[Aryaka Networks](https://www.aryaka.com/)** |Destekleniyor |Destekleniyor |Amsterdam, Chicago, Dallas, Hong Kong SAR, Sao Paulo, Seattle, Silikon Vadisi, Singapur, Tokyo, Washington DC |
| **[Ascenty Data Centers](https://www.ascenty.com/en/cloud/microsoft-express-route)** |Destekleniyor |Destekleniyor |Sao Paulo |
| **[AT&T NetBond](https://www.synaptic.att.com/clouduser/html/productdetail/ATT_NetBond.htm)** |Destekleniyor |Destekleniyor |Amsterdam, Chicago, Dallas, Londra, Silikon Vadisi, Singapur, Sidney, Tokyo, Toronto, Washington DC |
| **[TOKYO'DA](https://www.attokyo.com/service/cloudsconnection/forazure.html)** | Destekleniyor | Destekleniyor | Tokyo2 |
| **[BBIX](https://www.bbix.net/en/service/ix/)** | Destekleniyor | Destekleniyor | Tokyo |
| **[BCX](https://www.bcx.co.za/solutions)** |Destekleniyor |Destekleniyor |Cape Town|
| **[Bell Canada](https://business.bell.ca/shop/enterprise/cloud-connect-access-to-cloud-partner-services)** |Destekleniyor |Destekleniyor |Montreal, Toronto, Quebec City |
| **[British Telecom](https://www.globalservices.bt.com/en/solutions/products/cloud-connect-azure)** |Destekleniyor |Destekleniyor |Amsterdam, Hong Kong SAR, Johannesburg, Londra, Newport(Galler), Sao Paulo, Silikon Vadisi, Singapur, Sidney, Tokyo, Washington DC |
| **[C3ntro](https://www.c3ntro.com/data1/express-route1.php)** |Destekleniyor |Destekleniyor |Miami |
| **CDC** | Destekleniyor | Destekleniyor | Kanberra, Kanberra2 |
| **[CenturyLink Cloud Connect](https://www.centurylink.com/cloudconnect)** |Destekleniyor |Destekleniyor |Amsterdam2, Chicago, Frankfurt, Hong Kong, Las Vegas, Londra2, New York, Paris, San Antonio, Silikon Vadisi, Tokyo, Toronto, Washington DC, Washington DC2 |
| **[Baş Telekom](https://www.chief.com.tw/dispPageBox/ct.aspx?ddsPageID=ENCLOUDSERVICE&dbid=4852937342)** |Destekleniyor |Destekleniyor |Hong Kong, Taipei |
| **Çin Mobil Uluslararası** |Destekleniyor |Destekleniyor | Singapur |
| **China Telecom Global** |Destekleniyor |Destekleniyor |Hong Kong |
| **Çin Unicom Global** |Destekleniyor |Destekleniyor | Singapur2 |
| **[Cologix](https://www.cologix.com/hyperscale/microsoft-azure/)** |Destekleniyor |Destekleniyor |Chicago, Dallas, Montreal, Toronto, Vancouver, Washington DC |
| **[Colt](https://www.colt.net/uk/en/news/colt-announces-dedicated-cloud-access-for-microsoft-azure-services-en.htm)** |Destekleniyor |Destekleniyor |Amsterdam, Amsterdam2, Chicago, Dublin, Frankfurt, Londra, Londra2, Newport, New York, Osaka, Paris, Silikon Vadisi, Silikon Vadisi2, Singapur2, Tokyo, Washington DC |
| **[Comcast](https://business.comcast.com/landingpage/microsoft-azure)** |Destekleniyor |Destekleniyor |Chicago, Silikon Vadisi, Washington DC |
| **[CoreSite](https://www.coresite.com/solutions/cloud-services/public-cloud-providers/microsoft-azure-expressroute)** |Destekleniyor |Destekleniyor |Chicago, Denver, Los Angeles, New York, Silikon Vadisi, Silikon Vadisi2, Washington DC, Washington DC2 |
| **[DE-CIX](https://www.de-cix.net/en/de-cix-service-world/cloud-exchange/find-a-cloud-service/detail/microsoft-azure)** | Destekleniyor |Destekleniyor |Amsterdam2, Frankfurt, Marsilya|
| **[Devoli](https://devoli.com/expressroute)** | Destekleniyor |Destekleniyor | Auckland, Melbourne, Sidney |
| **du datamena** |Destekleniyor |Destekleniyor | Dubai2 |
| **eir** |Destekleniyor |Destekleniyor |Dublin|
| **[Epsilon Global İletişim](https://www.epsilontel.com/solutions/direct-cloud-connect)** |Destekleniyor |Destekleniyor |Singapur, Singapur2 |
| **[Equinix](https://www.equinix.com/partners/microsoft-azure/)** |Destekleniyor |Destekleniyor |Amsterdam, Atlanta, Chicago, Dallas, Dublin, Frankfurt, Cenevre, Hong Kong SAR, Londra, Londra2, Los Angeles, Melbourne, Miami, New York, Osaka, Paris, Sao Paulo, Seattle, Silikon Vadisi, Singapur, Stockholm, Sidney, Tokyo, Toronto, Washington DC |
| **Etisalat BAE** |Destekleniyor |Destekleniyor |Dubai|
| **[euNetworks](https://eunetworks.com/services/solutions/cloud-connect/microsoft-azure-expressroute/)** |Destekleniyor |Destekleniyor |Amsterdam, Amsterdam2, Dublin, Londra |
| **FarEasTone** |Destekleniyor |Destekleniyor |Taipei|
| **GÉANT** |Destekleniyor |Destekleniyor |Amsterdam, Frankfurt, Marsilya |
| **[Genel Bulut Değişimi (GCX)](https://globalcloudxchange.com/cloud-platform/cloud-x-fusion/)** | Destekleniyor| Destekleniyor | Chennai, Mumbai |
| **ıntelsat** | Destekleniyor | Destekleniyor | Washington DC2 |
| **[InterCloud](https://www.intercloud.com/)** |Destekleniyor |Destekleniyor |Amsterdam, Chicago, Hong Kong, Londra, New York, Paris, Silikon Vadisi, Singapur, Washington DC, Zürih |
| **[İnternet2](https://www.internet2.edu/products-services/cloud-services-applications/microsoft-azure/#service-cloud-connect)** |Destekleniyor |Destekleniyor |Chicago, Dallas, Silikon Vadisi, Washington DC |
| **[İnternet Initiative Japan Inc. - IIJ](https://www.iij.ad.jp/en/news/pressrelease/2015/1216-2.html)** |Destekleniyor |Destekleniyor |Osaka, Tokyo |
| **[Internet Solutions - Cloud Connect](https://www.is.co.za/solution/cloud-connect/)** |Destekleniyor |Destekleniyor |Cape Town, Johannesburg, Londra |
| **[Interxion](https://www.interxion.com/why-interxion/colocate-with-the-clouds/Microsoft-Azure/)** |Destekleniyor |Destekleniyor |Amsterdam, Amsterdam2, Kopenhag, Dublin, Frankfurt, Londra, Marsilya, Paris, Zürih |
| **[IX Reach](https://www.ixreach.com/partners/cloud-partners/microsoft-azure/)**|Destekleniyor |Destekleniyor | Amsterdam, Londra2, Silikon Vadisi, Toronto, Washington DC |
| **Jaguar Ağı** |Destekleniyor |Destekleniyor |Marsilya|
| **[Jisc](https://www.jisc.ac.uk/microsoft-azure-expressroute)** |Destekleniyor |Destekleniyor |Londra |
| **[KINX](https://www.kinx.net/service/network/cloudhub/ms-expressroute/?lang=en)** |Destekleniyor |Destekleniyor |Seul |
| **[Kordia](https://www.kordia.co.nz/cloudconnect)** | Destekleniyor |Destekleniyor |Auckland, Sidney |
| **[KPN](https://www.kpn.com/zakelijk/cloud/connect.htm)** | Destekleniyor | Destekleniyor | Amsterdam |
| **[KT](https://cloud.kt.com/portal/ktcloudportal.epc.productintro.partnershipcloud_ConnectHub.html)** | Destekleniyor | Destekleniyor | Seul |
| **[Level 3 Communications](http://your.level3.com/LP=882?WT.tsrc=02192014LP882AzureVanityAzureText)** |Destekleniyor |Destekleniyor |Amsterdam, Chicago, Dallas, Londra, Newport (Galler), Sao Paulo, Seattle, Silikon Vadisi, Singapur, Washington DC |
| **LG CNS** |Destekleniyor |Destekleniyor |Busan, Seul |
| **[Liquid Telecom](https://www.liquidtelecom.com/products-and-services/cloud.html)** |Destekleniyor |Destekleniyor |Cape Town, Johannesburg |
| **[Megaport](https://www.megaport.com/services/microsoft-expressroute/)** |Destekleniyor |Destekleniyor |Amsterdam, Atlanta, Auckland, Chicago, Dallas, Denver, Dubai2, Dublin, Frankfurt, Cenevre, Hong Kong SAR, Las Vegas, Londra, Londra2, Los Angeles, Melbourne, Miami, Montreal, New York, Oslo, Perth, Quebec City, San Antonio, Seattle, Silikon Vadisi, Singapur, Singapur2, Sidney, Sidney2, Tokyo, Toronto, Washington DC, Zürih |
| **[Mtn](https://www.mtnbusiness.com/en/enterprise/Pages/microsoft-express-route.aspx)** |Destekleniyor |Destekleniyor |Londra |
| **[Neutrona Networks](https://www.neutrona.com/index.php/azure-expressroute/)** |Destekleniyor |Destekleniyor |Dallas, Los Angeles, Miami, Sao Paulo, Washington DC |
| **[Next Generation Data](https://www.nextgenerationdata.co.uk/ngd-cloud-gateway/)** |Destekleniyor |Destekleniyor |Newport(Galler) |
| **[NEXTDC](https://www.nextdc.com/services/axon-ethernet/microsoft-expressroute)** |Destekleniyor |Destekleniyor |Melbourne, Perth, Sidney, Sidney2 |
| **[NTT İletişim](https://www.ntt.com/en/services/network/virtual-private-network.html)** |Destekleniyor |Destekleniyor |Amsterdam, Hong Kong SAR, Londra, Los Angeles, Osaka, Singapur, Sidney, Tokyo, Washington DC |
| **[NTT EAST](https://flets.com/cloudgateway/crossconnect/)** |Destekleniyor |Destekleniyor |Tokyo |
| **[NTT SmartConnect](https://cloud.nttsmc.com/cxc/azure.html)** |Destekleniyor |Destekleniyor |Osaka |
| **[Ooredoo Bulut Bağlantısı](https://www.ooredoo.qa/portal/OoredooQatar/cloud-connect-expressroute)** |Destekleniyor |Destekleniyor |Marsilya |
| **[Optus](https://www.optus.com.au/enterprise/)** |Destekleniyor |Destekleniyor |Melbourne, Sidney |
| **[Orange](https://www.orange-business.com/en/products/business-vpn-galerie)** |Destekleniyor |Destekleniyor |Amsterdam, Amsterdam2, Dubai2, Frankfurt, Hong Kong SAR, Johannesburg, Londra, Paris, Sao Paulo, Silikon Vadisi, Singapur, Sidney, Tokyo, Washington DC |
| **[Orixcom](https://www.orixcom.com/cloud-solutions/)** | Destekleniyor | Destekleniyor | Dubai2 |
| **[PacketFabric](https://www.packetfabric.com/cloud-connectivity/microsoft-azure)** |Destekleniyor |Destekleniyor |Chicago, Silikon Vadisi, Washington DC |
| **[PCCW Global Limited](https://consoleconnect.com/clouds/#azureRegions)** |Destekleniyor |Destekleniyor |Chicago, Hong Kong SAR, Londra |
| **[Sejong Telecom](https://www.sejongtelecom.net/en/pages/service/cloud_ms)** |Destekleniyor |Destekleniyor |Seul |
| **[SES](https://www.ses.com/networks/signature-solutions/signature-cloud/ses-and-azure-expressroute)** | Destekleniyor |Destekleniyor | Washington DC |
| **[Yoğunlaştıracaktır](http://telecom.sify.com/azure-expressroute.html)** |Destekleniyor |Destekleniyor |Madras, Bombay2 |
| **[SingTel](https://www.singtel.com/about-us/news-releases/singtel-provide-secure-private-access-microsoft-azure-public-cloud)** |Destekleniyor |Destekleniyor |Singapur, Singapur2 |
| **[Softbank](https://www.softbank.jp/biz/cloud/cloud_access/direct_access_for_az/)** |Destekleniyor |Destekleniyor |Osaka, Tokyo |
| **[Kıvılcım NZ](https://www.sparkdigital.co.nz/solutions/connectivity/cloud-connect/)** |Destekleniyor |Destekleniyor |Auckland, Sidney |
| **[Sprint](https://business.sprint.com/solutions/cloud-networking/)** |Destekleniyor |Destekleniyor |Chicago, Silikon Vadisi, Washington DC |
| **[Swisscom](https://www.swisscom.ch/en/business/enterprise/offer/cloud-data-center/microsoft-cloud-services/microsoft-azure-von-swisscom.html)** | Destekleniyor | Destekleniyor | Zürih |
| **[Tata Communications](https://www.tatacommunications.com/lp/izo/azure/azure_index.html)** |Destekleniyor |Destekleniyor |Amsterdam, Chennai, Hong Kong SAR, Londra, Mumbai, Sao Paulo, Silikon Vadisi, Singapur, Washington DC |
| **[Telefonica](https://www.business-solutions.telefonica.com/es/enterprise/solutions/efficient-infrastructure/managed-voice-data-connectivity/)** |Destekleniyor |Destekleniyor |Amsterdam, Sao Paulo |
| **[Telehouse - KDDI](https://www.telehouse.net/solutions/cloud-services/cloud-link)** |Destekleniyor |Destekleniyor |Londra, Londra2 |
| **Telenor** |Destekleniyor |Destekleniyor |Amsterdam, Londra, Oslo |
| **[Telia Carrier](https://www.teliacarrier.com/)** | Destekleniyor | Destekleniyor |Amsterdam, Chicago, Dallas, Frankfurt, Hong Kong, Londra, Oslo, Paris, Silikon Vadisi, Stockholm, Washington DC |
| **Telmex Uninet**| Destekleniyor | Destekleniyor | Dallas |
| **[Telstra Corporation](https://www.telstra.com.au/business-enterprise/network-services/networks/cloud-direct-connect/)** |Destekleniyor |Destekleniyor |Melbourne, Singapur, Sidney |
| **[Telus](https://www.telus.com)** |Destekleniyor |Destekleniyor |Montreal, Seattle, Toronto |
| **[Teraco](https://www.teraco.co.za/services/africa-cloud-exchange/)** |Destekleniyor |Destekleniyor |Cape Town, Johannesburg |
| **[TIME dotCom](https://www.time.com.my/enterprise/connectivity/cloud-interconnect)** | Destekleniyor | Destekleniyor | Kuala Lumpur |
| **[Transtelco](https://transtelco.net/enterprise-services/)** |Destekleniyor |Destekleniyor |Dallas, Los Angeles|
| **[UOLDIVEO](https://www.uoldiveo.com.br/)** |Destekleniyor |Destekleniyor |Sao Paulo |
| **[Verizon](https://enterprise.verizon.com/products/network/application-enablement/secure-cloud-interconnect/)** |Destekleniyor |Destekleniyor |Amsterdam, Chicago, Dallas, Hong Kong SAR, Londra, Mumbai, Silikon Vadisi, Singapur, Sidney, Tokyo, Toronto, Washington DC |
| **[Viasat](http://www.directcloud.viasatbusiness.com/)** | Destekleniyor | Destekleniyor | Washington DC2 |
| **[Vocus Grup NZ](https://www.vocus.co.nz/business/cloud-data-centres)** | Destekleniyor | Destekleniyor | Auckland, Sidney |
| **[Vodafone](https://www.vodafone.com/business/global-enterprise/global-connectivity/vodafone-ip-vpn-cloud-connect)** |Destekleniyor |Destekleniyor |Amsterdam2, Londra, Singapur |
| **[Vodafone Fikir](https://discover.vodafone.in/business/enterprise-solutions/connectivity/vpn-extended-connect)** | Destekleniyor | Destekleniyor | Mumbai, Mumbai2 |
| **[Zayo](https://www.zayo.com/solutions/industries/cloud-connectivity/microsoft-expressroute)** |Destekleniyor |Destekleniyor |Amsterdam, Chicago, Dallas, Denver, Londra, Los Angeles, Montreal, New York, Paris, Seattle, Silikon Vadisi, Toronto, Washington DC, Washington DC2 |

 **+** yakında gösterir

### <a name="national-cloud-environment"></a>Ulusal bulut ortamı

Azure ulusal bulutları birbirinden ve genel ticari Azure'dan izole edilmiştir. Bir Azure bulutu için ExpressRoute diğerlerinde Azure bölgelerine bağlanabilir. 

### <a name="us-government-cloud"></a>ABD bulutu

| **Hizmet sağlayıcı** | **Microsoft Azure** | **Office 365** | **Konumlar** |
| --- | --- | --- | --- |
| **[AT&T NetBond](https://www.synaptic.att.com/clouduser/html/productdetail/ATT_NetBond.htm)** |Destekleniyor |Destekleniyor |Chicago, Phoenix, Silikon Vadisi, Washington DC |
| **[CenturyLink Cloud Connect](https://www.centurylink.com/cloudconnect)** |Destekleniyor |Destekleniyor |New York, Phoenix, San Antonio, Washington DC |
| **[Equinix](https://www.equinix.com/partners/microsoft-azure/)** |Destekleniyor |Destekleniyor |Atlanta, Chicago, Dallas, New York, Seattle, Silikon Vadisi, Washington DC |
| **[Level 3 Communications](http://your.level3.com/LP=882?WT.tsrc=02192014LP882AzureVanityAzureText)** |Destekleniyor |Destekleniyor |Chicago, Silikon Vadisi, Washington DC |
| **[Megaport](https://www.megaport.com/services/microsoft-expressroute/)** |Destekleniyor | Destekleniyor | Chicago, Dallas, San Antonio, Seattle, Washington DC |
| **[Verizon](http://news.verizonenterprise.com/2014/04/secure-cloud-interconnect-solutions-enterprise/)** |Destekleniyor |Destekleniyor |Chicago, Dallas, New York, Silikon Vadisi, Washington DC |

### <a name="china"></a>Çin

| **Hizmet sağlayıcı** | **Microsoft Azure** | **Office 365** | **Konumlar** |
| --- | --- | --- | --- |
| **China Telecom** |Destekleniyor |Desteklenmiyor |Pekin, Pekin2, Şangay, Şangay2 |
| **Çin Unicom** | Destekleniyor | Desteklenmiyor | Pekin2 |
| **[Gds](http://www.gds-services.com/en/about_2.html)** |Destekleniyor |Desteklenmiyor |Pekin2, Şangay2 |

Daha fazla öğrenmek için, bkz. [Çin’de ExpressRoute](http://www.windowsazure.cn/home/features/expressroute/).

### <a name="germany"></a>Almanya

| **Hizmet sağlayıcı** | **Microsoft Azure** | **Office 365** | **Konumlar** |
| --- | --- | --- | --- |
| **[Colt](https://www.colt.net/uk/en/news/colt-announces-dedicated-cloud-access-for-microsoft-azure-services-en.htm)** |Destekleniyor |Desteklenmiyor |Frankfurt |
| **[Equinix](https://www.equinix.com/partners/microsoft-azure/)** |Destekleniyor |Desteklenmiyor |Frankfurt |
| **[e-shelter](https://www.e-shelter.de/en/microsoft-expressroutetm)** |Destekleniyor |Desteklenmiyor |Berlin |
| **Interxion** |Destekleniyor |Desteklenmiyor |Frankfurt |
| **[Megaport](https://www.megaport.com/services/microsoft-expressroute/)** |Destekleniyor  | Desteklenmiyor | Berlin |
| **T-Systems** |Destekleniyor |Desteklenmiyor |Berlin |

## <a name="connectivity-through-exchange-providers"></a>Exchange sağlayıcıları aracılığıyla bağlantı

Bağlantı sağlayıcınız önceki bölümlerde listelenmemişse hala bağlantı oluşturabilirsiniz.

* Yukarıdaki tabloda yer alan değişimlerin herhangi birine bağlı olup olmadığını görmek için bağlantı sağlayıcınıza başvurun. Değişim sağlayıcıları tarafından sunulan hizmetler hakkında daha fazla bilgi edinmek için aşağıdaki bağlantıları kontrol edebilirsiniz. Birkaç bağlantı sağlayıcı Ethernet değişimlerine zaten bağlı.
  * [Cologix](https://www.cologix.com/)
  * [CoreSite](https://www.coresite.com/)
  * [Equinix Cloud Exchange](https://www.equinix.com/services/interconnection-connectivity/cloud-exchange/)
  * [Interxion](https://www.interxion.com/products/interconnection/cloud-connect/)
  * [IX Reach](https://www.ixreach.com/partners/cloud-partners/microsoft-azure/)
  * [Megaport](https://www.megaport.com/services/microsoft-expressroute/)
  * [NextDC](https://www.nextdc.com/)
  * [PacketFabric](https://www.packetfabric.com/cloud-connectivity/microsoft-azure) 
  * [Teraco](https://www.teraco.co.za/platform-teraco/africa-cloud-exchange/)

* Bağlantı sağlayıcınızı, ağınızı seçtiğiniz eşleme konumuna genişletmesini sağlayın.
  * Bağlantı sağlayıcınızın bağlantınızı yüksek oranda kullanılabilir şekilde genişlettiğinden emin olun, böylece hiç tek nokta arızası olmaz.
* Microsoft’a bağlanmak için bağlantı sağlayınız olarak değişime sahip bir ExpressRoute bağlantı hattı sipariş edin.
  * Bağlantı kurmak için [ExpressRoute bağlantı hattı oluşturma](expressroute-howto-circuit-classic.md)’daki adımları izleyin.

## <a name="connectivity-through-satellite-operators"></a>Uydu operatörleri aracılığıyla bağlantı
Uzaktan uzaktaysanız ve fiber bağlantınız yoksa veya diğer bağlantı seçeneklerini keşfetmek istiyorsanız aşağıdaki uydu operatörlerini kontrol edebilirsiniz. 

* ıntelsat
* [SES](https://www.ses.com/networks/signature-solutions/signature-cloud/ses-and-azure-expressroute)
* [Viasat](http://www.directcloud.viasatbusiness.com/)

## <a name="connectivity-through-additional-service-providers"></a>Ek hizmet sağlayıcılar aracılığıyla bağlantı

| **Bağlantı sağlayıcısı** | **cihazlar** | **Konumlar** |
| --- | --- | --- |
| **[1CLOUDSTAR](https://www.1cloudstar.com/service/cloudconnect-azure-expressroute/)** |Equinix |Singapur |
| **[Airgate Technologies, Inc.](https://www.airgate.ca/expressroute)** | Equinix, Cologix | Toronto, Montreal |
| **[Alaska Communications](https://www.alaskacommunications.com/For-Your-Business/Direct-Cloud-Service)** |Equinix |Seattle |
| **[Altice Business](https://golightpath.com/transport)** |Equinix |New York, Washington DC |
| **[Arteria Networks Corporation](https://www.arteria-net.com/business/service/cloud/sca/)** |Equinix |Tokyo |
| **[Axtel](https://alestra.mx/landing/expressrouteazure/)** |Equinix |Dallas|
| **[Beanfield Metroconnect](https://www.beanfield.com/business/cloud-exchange)** |Megaport |Toronto|
| **[Bezeq International Ltd.](https://www.bezeqint.net/english)** | euNetworks | Londra |
| **[BICS](https://bics.com/bics-solutions-suite/cloud-connect/)** | Equinix | Amsterdam, Frankfurt, Londra, Singapur, Washington DC |
| **[BroadBand Tower, Inc.](https://www.bbtower.co.jp/product-service/data-center/network/dcconnect-for-azure/)** | Equinix | Tokyo |
| **[C3ntro Telecom](https://www.c3ntro.com/data/express-route)** | Equinix, Megaport | Dallas |
| **[Chief](https://www.chief.com.tw/dispPageBox/ct.aspx?ddsPageID=ENCLOUDSERVICE&dbid=4852937342)** | Equinix | Hong Kong ÖİB |
| **[Cinia](https://www.cinia.fi/en/services/connectivity-services/direct-public-cloud-connection.html)** | Equinix, Megaport | Frankfurt, Hamburg |
| **[CloudXpress](https://www2.telenet.be/fr/business/produits-services/internet/cloudxpress/)** | Equinix | Amsterdam | 
| **[CMC Telekom](https://cmctelecom.vn/san-pham/value-added-service-and-it/cmc-telecom-cloud-express-en/)** | Equinix | Singapur | 
| **[Aptum Teknolojileri](https://aptum.com/services/cloud/managed-azure/)**| Equinix | Montreal, Toronto |
| **[CoreAzure](http://www.coreazure.com/expressroute/)**| Equinix | Londra |
| **[Cox Business](https://www.cox.com/business/networking/cloud-connectivity.html)**| Equinix | Dallas, Silikon Vadisi, Washington DC |
| **[Taç Kalesi](https://fiber.crowncastle.com/solutions/added/cloud-connect)**| Equinix | Atlanta, Chicago, Dallas, Los Angeles, New York, Washington DC |
| **[Data Foundry](https://www.datafoundry.com/services/cloud-connect)** | Megaport | Dallas |
| **[Epsilon Telecommunications Limited](https://www.epsilontel.com/data-connectivity/cloud-access/)** | Equinix | Londra, Singapur, Washington DC |
| **[Eurofiber](https://eurofiber.nl/microsoft-azure/)** | Equinix | Amsterdam |
| **[Exponential E](https://www.exponential-e.com/services/connectivity-services/cloud-connect-exchange)** | Equinix | Londra |
| **[Fastweb S.p.A](https://www.fastweb.it/grandi-aziende/connessione-voce-e-wifi/scheda-prodotto/rete-privata-virtuale/)** | Equinix | Amsterdam |
| **[Fibrenoire](https://www.fibrenoire.ca/en/cloudextn)** | Megaport | Quebec City |
| **[Gtt Communications Inc](https://www.gtt.net)** |Equinix | Washington DC |
| **[Gulf Bridge Uluslararası](https://www.gbiinc.com/microsoft-azure-expressroute/)** | Equinix | Amsterdam |
| **[HSO](https://www.hso.co.uk/products/cloud-direct)** |Equinix | Londra, Slough |
| **[IVedha Inc](http://www.ivedha.com/cloud/manage-azure-cloud/express-route-4/)**| Equinix | Toronto |
| **[Kaalam Telekom Bahreyn B.S.C](http://www.kalaam-telecom.com/azure/)**| Level 3 Communications |Amsterdam |
| **LGA Telecom** |Equinix |Singapur|
| **[Macroview Telecom](http://www.macroview.com/en/scripts/catitem.php?catid=solution&sectionid=expressroute)** |Equinix |Hong Kong ÖİB 
| **[Macquarie Telecom Group](https://macquariegovernment.com/secure-cloud/secure-cloud-exchange/)** | Megaport | Sidney |
| **[MainOne](https://www.mainone.net/services/connectivity/cloud-connect/)** |Equinix | Amsterdam |
| **[Masergy](https://www.masergy.com/solutions/hybrid-networking/cloud-marketplace/microsoft-azure)** | Equinix | Washington DC |
| **[Mtn](https://www.mtnbusiness.com/en/enterprise/Pages/microsoft-express-route.aspx)** | Teraco | Cape Town, Johannesburg |
| **[NexGen Networks](https://www.nexgen-net.com/nexgen-networks-direct-connect-microsoft-azure-expressroute.html)** | Interxion | Londra |
| **[Nianet](https://nianet.dk/produkter/internet/microsoft-expressroute)** |Equinix | Amsterdam, Frankfurt |
| **[POST Telekom Lüksemburg](https://www.teralinksolutions.com/cloud-connectivity/cloudbridge-to-azure-expressroute/)**|Equinix | Amsterdam |
| **[Proximus](https://www.proximus.be/en/id_b_cl_proximus_external_cloud_connect/companies-and-public-sector/discover/magazines/expert-blog/proximus-external-cloud-connect.html)**|Equinix | Amsterdam, Dublin, Londra, Paris |
| **[QSC AG](https://www.qsc.de/de/produkte-loesungen/cloud-services-und-it-outsourcing/pure-enterprise-cloud/multi-cloud-management/azure-expressroute/)** |Interxion | Frankfurt |  
| **[RETN](https://retn.net/services/cloud-connect/)** | Equinix | Amsterdam |
| **Rogers** | Cologix, Equinix | Montreal, Toronto |
| **[Spektrum Kurumsal](https://enterprise.spectrum.com/services/cloud/cloud-connect.html)** | Equinix | Chicago, Dallas, Los Angeles, New York, Silikon Vadisi | 
| **[Tamares Telecom](https://www.tamarestelecom.com/our-services/#Connectivity)** | Equinix | Londra | 
| **[TDC Erhverv](https://tdc.dk/Produkter/cloudaccessplus)** | Equinix | Amsterdam | 
| **[Telecom Italia Sparkle](https://www.tisparkle.com/our-platform/corporate-platform/sparkle-cloud-connect#catalogue)**| Equinix | Amsterdam |
| **[Telekom Deutschland GmbH](https://cloud.telekom.de/de/infrastruktur/managed-it-services/managed-hybrid-infrastructure-mit-microsoft-azure)** | Interxion | Amsterdam, Frankfurt |
| **[Telia](https://www.telia.se/foretag/losningar/produkter-tjanster/datanet)** | Equinix | Amsterdam |
| **[ThinkTel](https://www.thinktel.ca/services/agile-ix-data/expressroute/)** | Equinix | Toronto | 
| **[United Information Highway (UIH)](https://www.uih.co.th/en/internet-solution/cloud-direct/uih-cloud-direct-for-microsoft-azure-expressroute)**| Equinix | Singapur |
| **[Venha Pra Nuvem](https://venhapranuvem.com.br/)** | Equinix | Sao Paulo |
| **[Webair](https://www.webair.com/microsoft-express-route-partnership/)**| Megaport | New York |
| **[Windstream](https://www.windstreambusiness.com/solutions/cloud-services/cloud-and-managed-hosting-services)**| Equinix | Chicago, Silikon Vadisi, Washington DC |
| **[X2nsat A.Ş.](https://www.x2nsat.com/expressroute/)** |Çekirdek Sitesi |Silikon Vadisi, Silikon Vadisi 2|
| **Zain** |Equinix |Londra|
| **[Zertia](https://www.zertia.es)**| Level 3 | Madrid |
| **[Zirro](https://zirro.com/services/)**| Cologix, Equinix | Montreal, Toronto |

## <a name="connectivity-through-datacenter-providers"></a>Veri merkezi sağlayıcıları aracılığıyla bağlantı

| **Sağlayıcı** | **cihazlar** |
| --- | --- |
| **[CyrusOne](https://cyrusone.com/enterprise-data-center-services/connectivity-and-interconnection/cloud-connectivity-reaching-amazon-microsoft-google-and-more/microsoft-azure-expressroute/?doing_wp_cron=1498512235.6733090877532958984375)** | Megaport, PacketFabric |
| **[Cyxtera](https://www.cyxtera.com/data-center-services/interconnection)** | Megaport, PacketFabric |
| **[Databank](https://www.databank.com/platforms/connectivity/cloud-direct-connect/)** | Megaport |
| **[DataFoundry](https://www.datafoundry.com/services/cloud-connect/)** | Megaport |
| **[Digital Realty](https://www.digitalrealty.com/services/interconnection/service-exchange/)** | IX Reach, Megaport PacketFabric |
| **[EdgeConnex](https://www.edgeconnex.com/services/edge-data-centers-proximity-matters/)** | Megaport, PacketFabric |
| **[Fleksesel](https://www.flexential.com/connectivity/cloud-connect-microsoft-azure-expressroute)** | IX Reach, Megaport, PacketFabric |
| **[QTS Veri Merkezleri](https://www.qtsdatacenters.com/hybrid-solutions/connectivity/azure-cloud )** | Megaport, PacketFabric |
| **[Akış Veri Merkezleri]( https://www.streamdatacenters.com/products-services/network-cloud/ )** | Megaport |
| **[RagingWire Data Centers](https://www.ragingwire.com/wholesale/wholesale-data-centers-worldwide-nexcenters)** | IX Reach, Megaport, PacketFabric |
| **[vXchnge](https://www.vxchnge.com/colocation-services/interconnection)** | IX Reach, Megaport |
| **[T5 Datacenters](https://t5datacenters.com/network-cloud-connect/)** | IX Reach |

## <a name="connectivity-through-national-research-and-education-networks-nren"></a>Ulusal Araştırma ve Eğitim Ağları (NREN) ile Bağlantı

| **Sağlayıcı**|
| --- |
| **AARNET**| 
| **GÉANT aracılığıyla DeIC**|
| **GÉANT aracılığıyla GARR**|
| **GÉANT**|
| **GÉANT aracılığıyla HEAnet**|
| **Internet2**|
| **JISC**|
| **GÉANT aracılığıyla RedIRIS**|
| **SINET**|
| **GÉANT aracılığıyla Surfnet**|

* Bağlantı sağlayıcınız bu listede yoksa, lütfen yukarıda listelenen ExpressRoute Exchange İş Ortaklarından herhangi birine bağlı olup olmadığınızı denetleyin.

## <a name="expressroute-system-integrators"></a>ExpressRoute sistem tümleştiricileri
İhtiyaçlarınıza uyan özel bağlantıyı etkinleştirme ağınızın ölçeğine bağlı olarak zorlu olabilir. ExpressRoute’a yönelik ekleme işleminde size yardımcı olmak üzere aşağıdaki tabloda listelenen herhangi bir sistem tümleştirici ile çalışabilirsiniz.

| **Sistem tümleştirici** | **Kıta** |
| --- | --- |
| **[Altogee](https://altogee.be/diensten/express-route/)** | Avrupa |
| **[Avanade Inc.](https://www.avanade.com/)** | Asya, Avrupa, Kuzey Amerika, Güney Amerika |
| **[Bright Skies GmbH](https://bskies.io/expressroute)** | Avrupa
| **[Ensyst](https://www.ensyst.com.au)** | Asya
| **[Equinix Professional Services](https://www.equinix.com/services/consulting/)** | Kuzey Amerika |
| **[FlexManage](https://www.flexmanage.com/cloud)** | Kuzey Amerika |
| **[Lightstream](https://www.lightstream.tech/partners/microsoft-azure/)** | Kuzey Amerika |
| **[The IT Consultancy Group](https://itconsult.com.au/)** | Avustralya |
| **[MOQdigital](https://www.moqdigital.com.au/insights/technical/network-connectivity-options-for-azure)** | Avustralya |
| **[MSG Services](https://www.msg-services.de/it-services/managed-services/cloud-outsourcing/)** | Avrupa (Almanya) |
| **[Nelite](https://www.exakis-nelite.com/offres/)** | Avrupa |
| **[New Signature](https://newsignature.com/technologies/express-route/)** | Avrupa |
| **[OneAs1a](https://www.oneas1a.com/connectivity.html)** | Asya |
| **[Orange Networks](https://orange-networks.com/blog/88-azureexpressroute)** | Avrupa |
| **[Perficient](https://www.perficient.com/Partners/Microsoft/Cloud/Azure-ExpressRoute)** | Kuzey Amerika |
| **[Presidio](https://info.presidio.com/microsoft-azure-expressroute)** | Kuzey Amerika |
| **[sol-tec](https://www.sol-tec.com/what-we-do/)** | Avrupa |
| **[Venha Pra Nuvem](https://venhapranuvem.com.br/)** | Güney Amerika |
| **[Vigilant.IT](https://vigilant.it/expressroute)** | Avustralya |

## <a name="next-steps"></a>Sonraki adımlar
* ExpressRoute hakkında daha fazla bilgi için, bkz. [ExpressRoute SSS](expressroute-faqs.md).
* Tüm önkoşulların sağlandığından emin olun. Bkz. [ExpressRoute önkoşulları](expressroute-prerequisites.md).

<!--Image References-->
[0]: ./media/expressroute-locations/expressroute-locations-map.png "Konum eşleme"
