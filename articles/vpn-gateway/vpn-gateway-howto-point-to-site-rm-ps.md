---
title: "Bilgisayardan Bir VNet'e bağlanma - P2S VPN ve yerel Azure sertifika kimlik doğrulaması: PowerShell"
description: P2S ve otomatik olarak imzalanan veya CA tarafından verilen sertifikaları kullanarak Windows ve Mac OS X istemcilerini bir Azure sanal ağa güvenli bir şekilde bağlayın. Bu makalede PowerShell kullanılmıştır.
titleSuffix: Azure VPN Gateway
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 01/15/2020
ms.author: cherylmc
ms.openlocfilehash: 49fbdf4a4090350cc0a6a5a1b938621b3cb08632
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76045094"
---
# <a name="configure-a-point-to-site-vpn-connection-to-a-vnet-using-native-azure-certificate-authentication-powershell"></a>Yerel Azure sertifika kimlik doğrulaması kullanarak Bir VNet'e Bir Noktadan Siteye VPN bağlantısı yapılandırma: PowerShell

Bu makale, Windows, Linux veya Mac OS X çalıştıran tek tek istemcileri azure vnet'e güvenli bir şekilde bağlamanıza yardımcı olur. Noktadan Siteye VPN bağlantıları, ev veya bir konferans gibi uzak bir noktadan Vnet'inize bağlanmak istediğinizde faydalıdır. Bir sanal ağa bağlanması gereken yalnızca birkaç istemciniz olduğunda Siteden Siteye VPN yerine P2S’yi de kullanabilirsiniz. Noktadan Siteye bağlantılar için bir VPN cihazına veya genel kullanıma yönelik bir IP adresine gerek yoktur. P2S, VPN bağlantısını SSTP (Güvenli Yuva Tünel Protokolü) veya IKEv2 üzerinden oluşturur. Noktadan Siteye VPN hakkında daha fazla bilgi edinmek için bkz. [Noktadan Siteye VPN hakkında](point-to-site-about.md).

![Bir bilgisayarı Azure sanal ağına bağlama - Noktadan Siteye bağlantı diyagramı](./media/vpn-gateway-howto-point-to-site-resource-manager-portal/p2snativeportal.png)

## <a name="architecture"></a>Mimari

Noktadan Siteye yerel Azure sertifika kimlik doğrulaması bağlantıları, bu alıştırmada yapılandıracağınız aşağıdaki öğeleri kullanır:

* RouteBased VPN ağ geçidi.
* Azure’a yüklenmiş bir kök sertifikanın ortak anahtarı (.cer dosyası). Sertifika karşıya yüklendikten sonra güvenilen bir sertifika olarak kabul edilir ve kimlik doğrulaması için kullanılır.
* Kök sertifikasından oluşturulan bir istemci sertifikası. Sanal ağa bağlanacak her istemci bilgisayarda yüklü olan istemci sertifikası. Bu sertifika, istemci kimlik doğrulaması için kullanılır.
* VPN istemcisi yapılandırması. VPN istemci yapılandırması dosyaları, istemcinin sanal ağa bağlanması için gerekli bilgileri içerir. Dosyalar, işletim sisteminde yerel olarak bulunan VPN istemcisini yapılandırır. Bağlanan her istemcinin yapılandırma dosyalarındaki ayarlar kullanılarak yapılandırılması gerekir.

## <a name="before-you-begin"></a>Başlamadan önce

Azure aboneliğiniz olduğunu doğrulayın. Henüz Azure aboneliğiniz yoksa [MSDN abonelik avantajlarınızı](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details) etkinleştirebilir veya [ücretsiz bir hesap](https://azure.microsoft.com/pricing/free-trial) için kaydolabilirsiniz.

### <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [powershell](../../includes/vpn-gateway-cloud-shell-powershell-about.md)]

>[!NOTE]
> Bu makaledeki adımların çoğu Azure Bulut Kabuğu'nu kullanabilir. Ancak, kök sertifika ortak anahtarını yüklemek için PowerShell'i yerel olarak veya Azure portalında kullanmanız gerekir.
>

### <a name="example-values"></a><a name="example"></a>Örnek değerler

Örnek değerleri kullanarak bir test ortamı oluşturabilir veya bu makaledeki örnekleri daha iyi anlamak için bu değerlere bakabilirsiniz. Değişkenler makalenin [1](#declare). bölümünde ayarlanır. İzlenecek yol olarak adımları kullanıp değerleri değiştirmeden uygulayabilir veya ortamınızı yansıtacak şekilde değiştirebilirsiniz.

* **Ad: VNet1**
* **Adres alanı: 192.168.0.0/16** ve **10.254.0.0/16**<br>Bu örnekte, bu yapılandırmanın birden çok adres alanıyla çalıştığını göstermek için birden fazla adres alanı kullanılır. Ancak, bu yapılandırma için birden çok adres alanı gerekli değildir.
* **Alt ağ adı: FrontEnd**
  * **Alt ağ adres aralığı: 192.168.1.0/24**
* **Alt ağ adı: BackEnd**
  * **Alt ağ adres aralığı: 10.254.1.0/24**
* **Alt ağ adı: GatewaySubnet**<br>VPN ağ geçidinin çalışması için Alt Ağ adı olarak *GatewaySubnet*'in kullanılması zorunludur.
  * **Ağ Geçidi Alt Ağ adres aralığı: 192.168.200.0/24** 
* **VPN istemcisi adres havuzu: 172.16.201.0/24**<br>Sanal ağa, bu Noktadan Siteye bağlantıyı kullanarak bağlanan VPN istemcileri, VPN istemci adresi havuzundan bir IP adresi alır.
* **Abonelik:** Birden fazla aboneliğiniz varsa doğru aboneliği kullandığınızdan emin olun.
* **Kaynak Grubu: TestRG**
* **Yer: Doğu ABD**
* DNS Sunucusu: Ad çözümlemesi için kullanmak istediğiniz **DNS sunucusunun IP adresi**. (isteğe bağlı)
* **Ağ Geçidi Adı: Vnet1GW**
* **Ortak IP adı: VNet1GWPIP**
* **VpnType: RouteBased** 

## <a name="1-sign-in-and-set-variables"></a><a name="declare"></a>1. Oturum açın ve değişkenleri ayarlayın

Bu bölümde, oturum açın ve bu yapılandırma için kullanılan değerleri bildirin. Belirtilen değerler örnek betiklerde kullanılır. Değerleri, ortamınızı yansıtacak şekilde değiştirin. Veya, bildirilen değerleri kullanın ve bir alıştırma olarak adımları uygulayın.

### <a name="sign-in"></a>Oturum aç

[!INCLUDE [sign in](../../includes/vpn-gateway-cloud-shell-ps-login.md)]

### <a name="declare-variables"></a>Değişkenleri bildir

Kullanmak istediğiniz değişkenleri bildirin. Aşağıdaki örneği kullanın ve gerektiğinde, değerleri kendi değerlerinizle değiştirin. Egzersiz sırasında PowerShell/Cloud Shell oturumunuzu herhangi bir noktada kapatırsanız, değişkenleri yeniden bildirmek için değerleri kopyalayıp yapıştırmanız gerekir.

  ```azurepowershell-interactive
  $VNetName  = "VNet1"
  $FESubName = "FrontEnd"
  $BESubName = "Backend"
  $GWSubName = "GatewaySubnet"
  $VNetPrefix1 = "192.168.0.0/16"
  $VNetPrefix2 = "10.254.0.0/16"
  $FESubPrefix = "192.168.1.0/24"
  $BESubPrefix = "10.254.1.0/24"
  $GWSubPrefix = "192.168.200.0/26"
  $VPNClientAddressPool = "172.16.201.0/24"
  $RG = "TestRG"
  $Location = "East US"
  $GWName = "VNet1GW"
  $GWIPName = "VNet1GWPIP"
  $GWIPconfName = "gwipconf"
  ```

## <a name="2-configure-a-vnet"></a><a name="ConfigureVNet"></a>2. Bir VNet yapılandırma

1. Bir kaynak grubu oluşturun.

   ```azurepowershell-interactive
   New-AzResourceGroup -Name $RG -Location $Location
   ```
2. Sanal ağ için alt ağ yapılandırmalarını oluşturup *FrontEnd*, *BackEnd* ve *GatewaySubnet* olarak adlandırın. Bu ön ekler bildirdiğiniz sanal adres alanının parçası olmalıdır.

   ```azurepowershell-interactive
   $fesub = New-AzVirtualNetworkSubnetConfig -Name $FESubName -AddressPrefix $FESubPrefix
   $besub = New-AzVirtualNetworkSubnetConfig -Name $BESubName -AddressPrefix $BESubPrefix
   $gwsub = New-AzVirtualNetworkSubnetConfig -Name $GWSubName -AddressPrefix $GWSubPrefix
   ```
3. Sanal ağı oluşturun.

   Bu örnekte, -DnsServer sunucu parametresi isteğe bağlıdır. Bir değer belirtildiğinde yeni bir DNS sunucusu oluşturulmaz. Belirttiğiniz DNS sunucusu IP adresi, sanal ağınızdan bağlandığınız kaynakların adlarını çözümleyebilen bir DNS sunucusu olmalıdır. Bu örnek için özel bir IP adresi kullanıldı, ancak bu, DNS sunucunuzun IP adresi olmayabilir. Kendi değerlerinizi kullandığınızdan emin olun. Belirttiğiniz değer, P2S bağlantısı veya VPN istemcisi tarafından değil, sanal ağa dağıttığınız kaynaklar tarafından kullanılıyor.

   ```azurepowershell-interactive
   New-AzVirtualNetwork -Name $VNetName -ResourceGroupName $RG -Location $Location -AddressPrefix $VNetPrefix1,$VNetPrefix2 -Subnet $fesub, $besub, $gwsub -DnsServer 10.2.1.3
   ```
4. Oluşturduğunuz sanal ağa ilişkin değişkenleri belirtin.

   ```azurepowershell-interactive
   $vnet = Get-AzVirtualNetwork -Name $VNetName -ResourceGroupName $RG
   $subnet = Get-AzVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet
   ```
5. Bir VPN ağ geçidinin genel bir IP adresi olmalıdır. İlk olarak IP adresi kaynağını istemeniz, sonra sanal ağ geçidinizi oluştururken bu kaynağa başvurmanız gerekir. VPN ağ geçidi oluşturulurken, IP adresi kaynağa dinamik olarak atanır. VPN Gateway hizmeti şu anda yalnızca *Dinamik* Genel IP adresi ayırmayı desteklemektedir. Statik bir Genel IP adresi ataması isteğinde bulunamazsınız. Ancak, bu durum IP adresinin VPN ağ geçidinize atandıktan sonra değiştiği anlamına gelmez. Genel IP adresi, yalnızca ağ geçidi silinip yeniden oluşturulduğunda değişir. VPN ağ geçidiniz üzerinde gerçekleştirilen yeniden boyutlandırma, sıfırlama veya diğer iç bakım/yükseltme işlemleri sırasında değişmez.

   Dinamik olarak atanan bir genel IP adresi isteyin.

   ```azurepowershell-interactive
   $pip = New-AzPublicIpAddress -Name $GWIPName -ResourceGroupName $RG -Location $Location -AllocationMethod Dynamic
   $ipconf = New-AzVirtualNetworkGatewayIpConfig -Name $GWIPconfName -Subnet $subnet -PublicIpAddress $pip
   ```

## <a name="3-create-the-vpn-gateway"></a><a name="creategateway"></a>3. VPN ağ geçidini oluşturun

VNet'iniz için sanal ağ geçidini yapılandırın ve oluşturun.

* -GatewayType değeri **Vpn** ve -VpnType değeri **RouteBased** olmalıdır.
* -VpnClientProtocol, etkinleştirmek istediğiniz tünel türlerini belirtmek için kullanılır. Tünel seçenekleri **OpenVPN, SSTP** ve **IKEv2**vardır. Bunlardan birini veya desteklenen herhangi bir kombinasyonu etkinleştirmeyi seçebilirsiniz. Birden çok türü etkinleştirmek istiyorsanız, virgülle ayrılmış adları belirtin. OpenVPN ve SSTP birlikte etkinleştirilemez. Android ve Linux üzerindeki strongSwan istemcisi ile iOS ve OSX üzerindeki yerel IKEv2 VPN istemcisi, bağlanmak için yalnızca IKEv2 tünelini kullanır. Windows istemcileri önce IKEv2’yi dener ve bağlanamazsa SSTP’ye döner. OpenVPN tünel türüne bağlanmak için OpenVPN istemcisini kullanabilirsiniz.
* Sanal ağ ağ geçidi 'Temel' SKU IKEv2, OpenVPN veya RADIUS kimlik doğrulamasını desteklemez. Mac istemcilerinin sanal ağınıza bağlanmasını planlıyorsanız, Temel SKU'yu kullanmayın.
* Bir VPN ağ geçidi işleminin tamamlanması, seçtiğiniz [ağ geçidi sku'suna](vpn-gateway-about-vpn-gateway-settings.md) bağlı olarak 45 dakikaya kadar sürebilir. Bu örnekte IKEv2 kullanılmıştır.

```azurepowershell-interactive
New-AzVirtualNetworkGateway -Name $GWName -ResourceGroupName $RG `
-Location $Location -IpConfigurations $ipconf -GatewayType Vpn `
-VpnType RouteBased -EnableBgp $false -GatewaySku VpnGw1 -VpnClientProtocol "IKEv2"
```

## <a name="4-add-the-vpn-client-address-pool"></a><a name="addresspool"></a>4. VPN istemci adres havuzunu ekleyin

VPN ağ geçidi oluşturduktan sonra VPN istemcisi adres havuzunu ekleyebilirsiniz. VPN istemcisi adres havuzu, VPN istemcilerinin bağlanırken bir IP adresi aldığı aralıktır. Bağlandığınız şirket içi konum veya bağlanmak istediğiniz sanal ağ ile çakışmayan özel bir IP adresi aralığı kullanın. Bu örnekte, VPN istemcisi adres havuzu 1. Adımda bir [değişken](#declare) olarak bildirilir.

```azurepowershell-interactive
$Gateway = Get-AzVirtualNetworkGateway -ResourceGroupName $RG -Name $GWName
Set-AzVirtualNetworkGateway -VirtualNetworkGateway $Gateway -VpnClientAddressPool $VPNClientAddressPool
```

## <a name="5-generate-certificates"></a><a name="Certificates"></a>5. Sertifika lar oluşturma

Noktadan Siteye VPN’lerde VPN istemcilerinin kimlik doğrulamasını yapmak için Azure tarafından sertifikalar kullanılır. Kök sertifikanın ortak anahtar bilgilerini Azure'a yükleyin. Bundan sonra ortak anahtar, 'güvenilir' olarak kabul edilir. Güvenilir kök sertifikadan istemci sertifikaları oluşturulmalı ve sonra Sertifikalar-Geçerli Kullanıcı/Kişisel sertifika deposundaki her bir istemci bilgisayara yüklenmelidir. Sertifika, sanal ağ ile bağlantı başlattığında istemcinin kimliğini doğrulamak için kullanılır. 

Otomatik olarak imzalanan sertifikalar kullanıyorsanız bu sertifikaların belirli parametreler kullanılarak oluşturulması gerekir. [PowerShell ve Windows 10](vpn-gateway-certificates-point-to-site.md)'a yönelik yönergeler yardımıyla, otomatik olarak imzalanan sertifika oluşturabilir veya Windows 10'a sahip değilseniz [MakeCert](vpn-gateway-certificates-point-to-site-makecert.md) kullanabilirsiniz. Otomatik olarak imzalanan kök sertifika ve istemci sertifikası oluştururken yönergelerdeki adımları uygulamanız önemlidir. Aksi halde, oluşturduğunuz sertifikalar P2S bağlantılarıyla uyumlu olmaz ve bağlantı hatası alırsınız.

### <a name="1-obtain-the-cer-file-for-the-root-certificate"></a><a name="cer"></a>1. Kök sertifika için .cer dosyasını edinin

[!INCLUDE [vpn-gateway-basic-vnet-rm-portal](../../includes/vpn-gateway-p2s-rootcert-include.md)]


### <a name="2-generate-a-client-certificate"></a><a name="generate"></a>2. İstemci sertifikası oluşturma

[!INCLUDE [vpn-gateway-basic-vnet-rm-portal](../../includes/vpn-gateway-p2s-clientcert-include.md)]

## <a name="6-upload-the-root-certificate-public-key-information"></a><a name="upload"></a>6. Kök sertifika ortak anahtar bilgilerini yükleyin

VPN ağ geçidini oluşturma işleminin tamamlandığını doğrulayın. İşlem tamamlandıktan sonra, güvenilen kök sertifikanın .cer dosyasını (ortak anahtar bilgilerini içerir) Azure’a yükleyebilirsiniz. Bir .cer dosyası karşıya yüklendikten sonra Azure, güvenilir kök sertifikadan oluşturulmuş bir istemci sertifikasının yüklü olduğu istemcilerin kimliklerini doğrulamak için bu dosyayı kullanabilir. Daha sonra gerekirse, toplam 20 adede kadar güvenilir kök sertifika dosyasını karşıya yükleyebilirsiniz.

>[!NOTE]
> .cer dosyasını Azure Cloud Shell'i kullanarak yükleyemezsiniz. PowerShell'i bilgisayarınızda yerel olarak veya Azure portal [adımlarını](vpn-gateway-howto-point-to-site-resource-manager-portal.md#uploadfile)kullanabilirsiniz.
>

1. Sertifika adınızın değişkenini tanımlamak için değeri kendi değerinizle değiştirin.

   ```azurepowershell
   $P2SRootCertName = "P2SRootCert.cer"
   ```
2. Dosya yolunu kendi dosya yolunuzla değiştirin ve ardından cmdlet'leri çalıştırın.

   ```azurepowershell
   $filePathForCert = "C:\cert\P2SRootCert.cer"
   $cert = new-object System.Security.Cryptography.X509Certificates.X509Certificate2($filePathForCert)
   $CertBase64 = [system.convert]::ToBase64String($cert.RawData)
   $p2srootcert = New-AzVpnClientRootCertificate -Name $P2SRootCertName -PublicCertData $CertBase64
   ```
3. Ortak anahtar bilgilerini Azure'a yükleyin. Sertifika bilgileri yüklendikten sonra Azure, sertifikayı güvenilir bir kök sertifika olarak kabul eder. Yükleme yaparken, PowerShell'i bilgisayarınızda yerel olarak çalıştırdığınızdan emin olun veya bunun yerine [Azure portal adımlarını](vpn-gateway-howto-point-to-site-resource-manager-portal.md#uploadfile)kullanabilirsiniz. Azure Bulut Uyp'ı kullanarak yük yapamazsınız.

   ```azurepowershell
   Add-AzVpnClientRootCertificate -VpnClientRootCertificateName $P2SRootCertName -VirtualNetworkGatewayname "VNet1GW" -ResourceGroupName "TestRG" -PublicCertData $CertBase64
   ```

## <a name="7-install-an-exported-client-certificate"></a><a name="clientcertificate"></a>7. Dışa aktarılan istemci sertifikasını yükleme

İstemci sertifikalarını oluşturmak için kullandığınız bilgisayardan farklı bir istemci bilgisayarda bir P2S bağlantı oluşturmak istiyorsanız, bir istemci sertifikası yüklemeniz gerekir. Bir istemci sertifikası yüklenirken, istemci sertifikası dışarı aktarılırken oluşturulan parola gerekir.

İstemci sertifikasının tüm sertifika zinciriyle birlikte .pfx dosyası olarak (varsayılan seçenek) dışarı aktarılmadığından emin olun. Aksi takdirde kök sertifika bilgileri istemci bilgisayarda bulunmaz ve istemci, kimliğini düzgün bir biçimde doğrulayamaz. 

Yükleme adımları için bkz. [İstemci sertifikası yükleme](point-to-site-how-to-vpn-client-install-azure-cert.md).

## <a name="8-configure-the-native-vpn-client"></a><a name="clientconfig"></a>8. Yerel VPN istemcisini yapılandırın

VPN istemcisi yapılandırma dosyaları, P2S bağlantısı üzerinden bir sanal ağa bağlanmak üzere cihazları yapılandırmak için gereken ayarları içerir. VPN istemcisi yapılandırma dosyalarını oluşturma ve yükleme yönergeleri için bkz. [Yerel Azure sertifika doğrulaması P2S yapılandırmaları için VPN istemci yapılandırma dosyalarını oluşturma ve yükleme](point-to-site-vpn-client-configuration-azure-cert.md).

## <a name="9-connect-to-azure"></a><a name="connect"></a>9. Azure'a bağlanın

### <a name="to-connect-from-a-windows-vpn-client"></a>Windows VPN istemcisinden bağlanmak için

>[!NOTE]
>Bağlanmakta olduğunuz Windows istemci bilgisayarında Yönetici haklarına sahip olmanız gerekir.
>
>

1. İstemci bilgisayarda sanal ağınıza bağlanmak için VPN bağlantılarında gezinin ve oluşturduğunuz VPN bağlantısını bulun. Bu VPN bağlantısı sanal ağınızla aynı ada sahiptir. **Bağlan**'a tıklayın. Sertifika kullanımına ilişkin bir açılır ileti görüntülenebilir. Yükseltilmiş ayrıcalıklar kullanmak için **Devam**’a tıklayın. 
2. **Bağlantı** durum sayfasında **Bağlan**'a tıklayarak bağlantıyı başlatın. Bir **Sertifika Seç** ekranı çıkarsa, gösterilen istemci sertifikasının bağlanmak için kullanmak istediğiniz sertifika olduğunu doğrulayın. Başka bir sertifika gösteriliyorsa, açılan liste okunu kullanarak doğru sertifikayı seçin ve **Tamam**’a tıklayın.

   ![VPN istemcisinin Azure’a bağlanması](./media/vpn-gateway-howto-point-to-site-rm-ps/clientconnect.png)
3. Bağlantınız kurulur.

   ![Bağlantı kuruldu](./media/vpn-gateway-howto-point-to-site-rm-ps/connected.png)

#### <a name="troubleshooting-windows-client-p2s-connections"></a>Windows istemcisi P2S bağlantı sorunlarını giderme

[!INCLUDE [client certificates](../../includes/vpn-gateway-certificates-verify-client-cert-include.md)]

### <a name="to-connect-from-a-mac-vpn-client"></a>Mac VPN istemcisinden bağlanmak için

Ağ iletişim kutusunda kullanmak istediğiniz istemci profilini bulup **Bağlan**’a tıklayın.
Ayrıntılı talimatlar için [Yükle - Mac (OS X)](https://docs.microsoft.com/azure/vpn-gateway/point-to-site-vpn-client-configuration-azure-cert#installmac) adresini kontrol edin. Bağlanmada sorun yaşıyorsanız, sanal ağ ağ geçidinin Temel SKU kullanmadığını doğrulayın. Temel SKU Mac istemcileri için desteklenmez.

  ![Mac bağlantısı](./media/vpn-gateway-howto-point-to-site-rm-ps/applyconnect.png)

## <a name="to-verify-your-connection"></a><a name="verify"></a>Bağlantınızı doğrulamak için

Bu yönergeler, Windows istemcileri için geçerlidir.

1. VPN bağlantınızın etkin olduğunu doğrulamak için, yükseltilmiş bir komut istemi açın ve *ipconfig/all* komutunu çalıştırın.
2. Sonuçlara bakın. Aldığınız IP adresinin, yapılandırmanızda belirttiğiniz Noktadan Siteye VPN İstemcisi Adres Havuzu'ndaki adreslerden biri olduğuna dikkat edin. Sonuçları şu örneğe benzer:

   ```
   PPP adapter VNet1:
      Connection-specific DNS Suffix .:
      Description.....................: VNet1
      Physical Address................:
      DHCP Enabled....................: No
      Autoconfiguration Enabled.......: Yes
      IPv4 Address....................: 172.16.201.3(Preferred)
      Subnet Mask.....................: 255.255.255.255
      Default Gateway.................:
      NetBIOS over Tcpip..............: Enabled
   ```

## <a name="to-connect-to-a-virtual-machine"></a><a name="connectVM"></a>Sanal makineye bağlanma

Bu yönergeler, Windows istemcileri için geçerlidir.

[!INCLUDE [Connect to a VM](../../includes/vpn-gateway-connect-vm-p2s-include.md)]

## <a name="to-add-or-remove-a-root-certificate"></a><a name="addremovecert"></a>Kök sertifika eklemek veya kaldırmak için

Azure’da güvenilen kök sertifikayı ekleyebilir veya kaldırabilirsiniz. Bir kök sertifikayı kaldırdığınızda, o kökten oluşturulmuş bir sertifikaya sahip istemciler kimlik doğrulaması yapamaz ve bağlantı kuramaz. Bir istemcinin kimlik doğrulaması yapmasını ve bağlanmasını istiyorsanız, Azure’da güvenilen (karşıya yüklenmiş) bir kök sertifikadan oluşturulmuş yeni bir istemci sertifikası yüklemeniz gerekir.

### <a name="to-add-a-trusted-root-certificate"></a><a name="addtrustedroot"></a>Güvenilen kök sertifika ekleme

Azure'a en fazla 20 kök sertifika .cer dosyası ekleyebilirsiniz. Aşağıdaki adımlar bir kök sertifika eklemenize yardımcı olur:

#### <a name="method-1"></a><a name="certmethod1"></a>Yöntem 1


Bu yöntem, kök sertifika yüklemenin en etkili yoludur. Bilgisayarınıza yerel olarak yüklenmiş Azure PowerShell cmdlets gerektirir (Azure Cloud Shell değil).

1. Karşıya yüklenecek .cer dosyasını hazırlayın:

   ```azurepowershell
   $filePathForCert = "C:\cert\P2SRootCert3.cer"
   $cert = new-object System.Security.Cryptography.X509Certificates.X509Certificate2($filePathForCert)
   $CertBase64_3 = [system.convert]::ToBase64String($cert.RawData)
   $p2srootcert = New-AzVpnClientRootCertificate -Name $P2SRootCertName -PublicCertData $CertBase64_3
   ```
2. Dosyayı karşıya yükleyin. Aynı anda yalnızca bir dosya yükleyebilirsiniz.

   ```azurepowershell
   Add-AzVpnClientRootCertificate -VpnClientRootCertificateName $P2SRootCertName -VirtualNetworkGatewayname "VNet1GW" -ResourceGroupName "TestRG" -PublicCertData $CertBase64_3
   ```

3. Sertifika dosyasının karşıya yüklendiğini doğrulamak için:

   ```azurepowershell
   Get-AzVpnClientRootCertificate -ResourceGroupName "TestRG" `
   -VirtualNetworkGatewayName "VNet1GW"
   ```

#### <a name="method-2---azure-portal"></a><a name="certmethod2"></a>Yöntem 2 - Azure portalı

Bu yöntemde 1. Yöntem'den daha fazla adım vardır, ancak aynı sonucu verir. Sertifika verilerini görüntülemenizi gerektirebilecek durumlar için dahil edilmiştir. Bilgisayarınıza yerel olarak yüklenmiş Azure PowerShell cmdlets gerektirir (Azure Cloud Shell değil).

1. Azure'a eklenecek yeni kök sertifikayı oluşturup hazırlayın. Ortak anahtarı Base-64 kodlanmış X.509 (.CER) olarak dışarı aktarın ve bir metin düzenleyicisi ile açın. Aşağıdaki örnekte gösterildiği gibi değerleri kopyalayın:

   ![sertifika](./media/vpn-gateway-howto-point-to-site-rm-ps/copycert.png)

   > [!NOTE]
   > Sertifika verilerini kopyalarken, metni satır başları ya da satır besleme karakterleri içermeyen tek ve kesintisiz bir satır şeklinde kopyaladığınızdan emin olun. Satır başlarını ve satır besleme karakterlerini görmek için metin düzenleyicisindeki görünümünüzü 'Sembolü Göster/Tüm karakterleri göster' olarak değiştirmeniz gerekebilir.
   >
   >

2. Sertifika adını ve anahtar bilgilerini bir değişken olarak belirtin. Bilgileri aşağıdaki örnekte gösterildiği gibi kendi bilgilerinizle değiştirin:

   ```azurepowershell
   $P2SRootCertName2 = "ARMP2SRootCert2.cer"
   $MyP2SCertPubKeyBase64_2 = "MIIC/zCCAeugAwIBAgIQKazxzFjMkp9JRiX+tkTfSzAJBgUrDgMCHQUAMBgxFjAUBgNVBAMTDU15UDJTUm9vdENlcnQwHhcNMTUxMjE5MDI1MTIxWhcNMzkxMjMxMjM1OTU5WjAYMRYwFAYDVQQDEw1NeVAyU1Jvb3RDZXJ0MIIBIjANBgkqhkiG9w0BAQEFAAOCAQ8AMIIBCgKCAQEAyjIXoWy8xE/GF1OSIvUaA0bxBjZ1PJfcXkMWsHPzvhWc2esOKrVQtgFgDz4ggAnOUFEkFaszjiHdnXv3mjzE2SpmAVIZPf2/yPWqkoHwkmrp6BpOvNVOpKxaGPOuK8+dql1xcL0eCkt69g4lxy0FGRFkBcSIgVTViS9wjuuS7LPo5+OXgyFkAY3pSDiMzQCkRGNFgw5WGMHRDAiruDQF1ciLNojAQCsDdLnI3pDYsvRW73HZEhmOqRRnJQe6VekvBYKLvnKaxUTKhFIYwuymHBB96nMFdRUKCZIiWRIy8Hc8+sQEsAML2EItAjQv4+fqgYiFdSWqnQCPf/7IZbotgQIDAQABo00wSzBJBgNVHQEEQjBAgBAkuVrWvFsCJAdK5pb/eoCNoRowGDEWMBQGA1UEAxMNTXlQMlNSb290Q2VydIIQKazxzFjMkp9JRiX+tkTfSzAJBgUrDgMCHQUAA4IBAQA223veAZEIar9N12ubNH2+HwZASNzDVNqspkPKD97TXfKHlPlIcS43TaYkTz38eVrwI6E0yDk4jAuPaKnPuPYFRj9w540SvY6PdOUwDoEqpIcAVp+b4VYwxPL6oyEQ8wnOYuoAK1hhh20lCbo8h9mMy9ofU+RP6HJ7lTqupLfXdID/XevI8tW6Dm+C/wCeV3EmIlO9KUoblD/e24zlo3YzOtbyXwTIh34T0fO/zQvUuBqZMcIPfM1cDvqcqiEFLWvWKoAnxbzckye2uk1gHO52d8AVL3mGiX8wBJkjc/pMdxrEvvCzJkltBmqxTM6XjDJALuVh16qFlqgTWCIcb7ju"
   ```
3. Yeni kök sertifikayı ekleyin. Tek seferde yalnızca bir sertifika ekleyebilirsiniz.

   ```azurepowershell
   Add-AzVpnClientRootCertificate -VpnClientRootCertificateName $P2SRootCertName2 -VirtualNetworkGatewayname "VNet1GW" -ResourceGroupName "TestRG" -PublicCertData $MyP2SCertPubKeyBase64_2
   ```
4. Aşağıdaki örneği kullanarak yeni sertifikanın düzgün şekilde eklendiğini doğrulayabilirsiniz:

   ```azurepowershell
   Get-AzVpnClientRootCertificate -ResourceGroupName "TestRG" `
   -VirtualNetworkGatewayName "VNet1GW"
   ```

### <a name="to-remove-a-root-certificate"></a><a name="removerootcert"></a>Kök sertifika kaldırmak için

1. Değişkenleri bildirin.

   ```azurepowershell-interactive
   $GWName = "Name_of_virtual_network_gateway"
   $RG = "Name_of_resource_group"
   $P2SRootCertName2 = "ARMP2SRootCert2.cer"
   $MyP2SCertPubKeyBase64_2 = "MIIC/zCCAeugAwIBAgIQKazxzFjMkp9JRiX+tkTfSzAJBgUrDgMCHQUAMBgxFjAUBgNVBAMTDU15UDJTUm9vdENlcnQwHhcNMTUxMjE5MDI1MTIxWhcNMzkxMjMxMjM1OTU5WjAYMRYwFAYDVQQDEw1NeVAyU1Jvb3RDZXJ0MIIBIjANBgkqhkiG9w0BAQEFAAOCAQ8AMIIBCgKCAQEAyjIXoWy8xE/GF1OSIvUaA0bxBjZ1PJfcXkMWsHPzvhWc2esOKrVQtgFgDz4ggAnOUFEkFaszjiHdnXv3mjzE2SpmAVIZPf2/yPWqkoHwkmrp6BpOvNVOpKxaGPOuK8+dql1xcL0eCkt69g4lxy0FGRFkBcSIgVTViS9wjuuS7LPo5+OXgyFkAY3pSDiMzQCkRGNFgw5WGMHRDAiruDQF1ciLNojAQCsDdLnI3pDYsvRW73HZEhmOqRRnJQe6VekvBYKLvnKaxUTKhFIYwuymHBB96nMFdRUKCZIiWRIy8Hc8+sQEsAML2EItAjQv4+fqgYiFdSWqnQCPf/7IZbotgQIDAQABo00wSzBJBgNVHQEEQjBAgBAkuVrWvFsCJAdK5pb/eoCNoRowGDEWMBQGA1UEAxMNTXlQMlNSb290Q2VydIIQKazxzFjMkp9JRiX+tkTfSzAJBgUrDgMCHQUAA4IBAQA223veAZEIar9N12ubNH2+HwZASNzDVNqspkPKD97TXfKHlPlIcS43TaYkTz38eVrwI6E0yDk4jAuPaKnPuPYFRj9w540SvY6PdOUwDoEqpIcAVp+b4VYwxPL6oyEQ8wnOYuoAK1hhh20lCbo8h9mMy9ofU+RP6HJ7lTqupLfXdID/XevI8tW6Dm+C/wCeV3EmIlO9KUoblD/e24zlo3YzOtbyXwTIh34T0fO/zQvUuBqZMcIPfM1cDvqcqiEFLWvWKoAnxbzckye2uk1gHO52d8AVL3mGiX8wBJkjc/pMdxrEvvCzJkltBmqxTM6XjDJALuVh16qFlqgTWCIcb7ju"
   ```
2. Sertifikayı kaldırın.

   ```azurepowershell-interactive
   Remove-AzVpnClientRootCertificate -VpnClientRootCertificateName $P2SRootCertName2 -VirtualNetworkGatewayName $GWName -ResourceGroupName $RG -PublicCertData $MyP2SCertPubKeyBase64_2
   ```
3. Sertifikanın başarıyla kaldırıldığını doğrulamak için aşağıdaki örneği kullanın.

   ```azurepowershell-interactive
   Get-AzVpnClientRootCertificate -ResourceGroupName "TestRG" `
   -VirtualNetworkGatewayName "VNet1GW"
   ```

## <a name="to-revoke-a-client-certificate"></a><a name="revoke"></a>İstemci sertifikasını iptal etmek için

İstemci sertifikalarını iptal edebilirsiniz. Sertifika iptal listesi sayesinde, ayrı istemci sertifikalarına göre Noktadan Siteye bağlantıyı seçmeli olarak reddedebilirsiniz. Bu, güvenilen kök sertifika kaldırma işleminden farklıdır. Azure’dan güvenilen kök sertifika .cer dosyasını kaldırırsanız iptal edilen kök sertifika tarafından oluşturulan/imzalanan tüm istemci sertifikaları reddedilir. Kök sertifika yerine istemci sertifikasını iptal etmek, kök sertifikadan oluşturulan diğer sertifikaların kimlik doğrulaması amacıyla kullanılmaya devam edilmesine olanak sağlar.

Genellikle ekip ve kuruluş düzeylerinde erişimi yönetmek için kök sertifika kullanılırken ayrı kullanıcılar üzerinde ayrıntılı erişim denetimi için iptal edilen istemci sertifikaları kullanılır.

### <a name="revoke-a-client-certificate"></a><a name="revokeclientcert"></a>İstemci sertifikasını iptal etme

1. İstemci sertifikasının parmak izini alın. Daha fazla bilgi için, [Sertifikanın Parmak İzi'ni nasıl alabildiğini](https://msdn.microsoft.com/library/ms734695.aspx)öğrenin.
2. Bilgileri bir metin düzenleyicisine kopyalayın ve sürekli bir dize haline getirmek için tüm boşlukları kaldırın. Bu dize sonraki adımda bir değişken olarak bildirilir.
3. Değişkenleri bildirin. Önceki adımda alınan parmak izini bildirdiğinizden emin olun.

   ```azurepowershell-interactive
   $RevokedClientCert1 = "NameofCertificate"
   $RevokedThumbprint1 = "‎51ab1edd8da4cfed77e20061c5eb6d2ef2f778c7"
   $GWName = "Name_of_virtual_network_gateway"
   $RG = "Name_of_resource_group"
   ```
4. Parmak izini, iptal edilen sertifika listesine ekleyin. Parmak izi eklendiğinde “Başarılı” ifadesini görürsünüz.

   ```azurepowershell-interactive
   Add-AzVpnClientRevokedCertificate -VpnClientRevokedCertificateName $RevokedClientCert1 `
   -VirtualNetworkGatewayName $GWName -ResourceGroupName $RG `
   -Thumbprint $RevokedThumbprint1
   ```
5. Parmak izinin, sertifika iptal listesine eklendiğini doğrulayın.

   ```azurepowershell-interactive
   Get-AzVpnClientRevokedCertificate -VirtualNetworkGatewayName $GWName -ResourceGroupName $RG
   ```
6. Parmak izi eklendikten sonra sertifika artık bağlanmak için kullanılamaz. Bu sertifikayı kullanarak bağlanmaya çalışan istemciler sertifikanın artık geçerli olmadığını belirten bir ileti alır.

### <a name="to-reinstate-a-client-certificate"></a><a name="reinstateclientcert"></a>İstemci sertifikasını yeniden devreye sokma

Parmak izini, iptal edilen istemci sertifikaları listesinden kaldırarak bir istemci sertifikasını yeniden devreye sokabilirsiniz.

1. Değişkenleri bildirin. Yeniden devreye sokmak istediğiniz sertifika için doğru parmak izini bildirdiğinizden emin olun.

   ```azurepowershell-interactive
   $RevokedClientCert1 = "NameofCertificate"
   $RevokedThumbprint1 = "‎51ab1edd8da4cfed77e20061c5eb6d2ef2f778c7"
   $GWName = "Name_of_virtual_network_gateway"
   $RG = "Name_of_resource_group"
   ```
2. Sertifika parmak izini sertifika iptal listesinden kaldırın.

   ```azurepowershell-interactive
   Remove-AzVpnClientRevokedCertificate -VpnClientRevokedCertificateName $RevokedClientCert1 `
   -VirtualNetworkGatewayName $GWName -ResourceGroupName $RG -Thumbprint $RevokedThumbprint1
   ```
3. Parmak izinin, iptal edilen parmak izi listesinden kaldırılıp kaldırılmadığını kontrol edin.

   ```azurepowershell-interactive
   Get-AzVpnClientRevokedCertificate -VirtualNetworkGatewayName $GWName -ResourceGroupName $RG
   ```

## <a name="point-to-site-faq"></a><a name="faq"></a>Noktadan Siteye SSS

[!INCLUDE [Point-to-Site FAQ](../../includes/vpn-gateway-faq-p2s-azurecert-include.md)]

## <a name="next-steps"></a>Sonraki adımlar
Bağlantınız tamamlandıktan sonra sanal ağlarınıza sanal makineler ekleyebilirsiniz. Daha fazla bilgi için bkz. [Sanal Makineler](https://docs.microsoft.com/azure/). Ağ ve sanal makineler hakkında daha fazla bilgi edinmek için, bkz. [Azure ve Linux VM ağına genel bakış](../virtual-machines/linux/azure-vm-network-overview.md).

P2S sorun giderme bilgileri için [Sorun giderme: Azure noktadan siteye bağlantısı sorunları](vpn-gateway-troubleshoot-vpn-point-to-site-connection-problems.md).
