---
title: Alt net trafik yönlendirmeyi yapılandırma - Azure Trafik Yöneticisi
description: Bu makalede, trafik belirli alt ağlardan yönlendirmek için Trafik Yöneticisi nasıl yapılandırılabildiğini açıklar.
services: traffic-manager
documentationcenter: ''
author: rohinkoul
manager: twooley
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/17/2018
ms.author: rohink
ms.openlocfilehash: 60cddce610d223433d0ffe1f6b9234625aca9881
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76938745"
---
# <a name="direct-traffic-to-specific-endpoints-based-on-user-subnet-using-traffic-manager"></a>Traffic Manager'ı kullanarak trafiği kullanıcı alt ağına göre belirli uç noktalara yönlendirme

Bu makalede alt ağ trafiği yönlendirme yöntemini yapılandırma adımları anlatılmaktadır. **Alt ağ** trafik yönlendirme yöntemi, bir IP adresi kümesini belirli uç noktalarla eşlemenizi sağlar. Traffic Manager'a bir istek geldiğinde kaynak IP adresi incelenir ve bu adresle ilişkilendirilmiş uç nokta döndürülür.

Bu makalede tartışılan senaryoda, kullanıcının sorgusunun IP adresine bağlı olarak alt ağ yönlendirmesi kullanılarak trafik bir dahili web sitesine veya üretim web sitesine yönlendirilir.

Azure aboneliğiniz yoksa, başlamadan önce [ücretsiz](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) bir hesap oluşturun.

## <a name="prerequisites"></a>Ön koşullar
Bu öğreticide Traffic Manager'ın çalışmasını uygulamalı olarak görmek için şu sistemleri dağıtmanız gerekir:
- farklı Azure bölgelerinde çalışan iki basit web sitesi: **Doğu ABD** (iç web sitesi olarak görev yapar) ve **Batı Avrupa** (üretim web sitesi olarak görev yapar).
- Traffic Manager'ı test etmek için iki test amaçlı VM: bir VM **Doğu ABD** bölgesinde, ikinci VM ise **Batı Avrupa** bölgesinde olmalıdır.

Test amaçlı VM'ler Traffic Manager'ın kullanıcı sorgusunun geldiği alt ağa göre kullanıcı trafiğini iç web sitesine veya üretim web sitesine nasıl yönlendirdiğini göstermek için kullanılır.

### <a name="sign-in-to-azure"></a>Azure'da oturum açma

https://portal.azure.com adresinden Azure portalında oturum açın.

### <a name="create-websites"></a>Web sitelerini oluşturma

Bu bölümde Traffic Manager profili için iki farklı Azure bölgesinde iki hizmet uç noktası sunan iki web sitesi örneği oluşturacaksınız. İki web sitesi oluşturmak için aşağıdaki adımları izleyin:
1. Biri **Doğu ABD** diğeri **Batı Avrupa** bölgesinde olmak üzere basit bir web sitesi çalıştıran iki VM oluşturun.
2. İki VM'de de IIS sunucusu yükleyin ve varsayılan web sitesi sayfasını web sitesini ziyaret eden kullanıcıların VM adını göreceği şekilde güncelleştirin.

#### <a name="create-vms-for-running-websites"></a>Web sitelerini çalıştırmak için VM oluşturma
Bu bölümde, **Doğu ABD** ve **Batı Avrupa** Azure bölgelerinde iki VMs *myEndpointVMEastUS* ve *myEndpointVMWEurope* oluşturursunuz.

1. Azure portalının sol üst köşesinde, **kaynak** > **Oluştur Windows** > **Server 2016 VM'yi**seçin.
2. **Temel Bilgiler** için aşağıdaki bilgileri girin veya seçin, kalan ayarlar için varsayılan değerleri kabul edin ve sonra **Oluştur**’u seçin:

    |Ayar|Değer|
    |---|---|
    |Adı|myIISVMEastUS|
    |Kullanıcı adı| Seçtiğiniz bir kullanıcı adını girin.|
    |Parola| Seçtiğiniz bir parolayı girin. Parola en az 12 karakter uzunluğunda olmalı ve [tanımlanmış karmaşıklık gereksinimlerini](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm) karşılamalıdır.|
    |Kaynak grubu| **Yeni'yi** seçin ve ardından *myResourceGroupTM1*yazın.|
    |Konum| **Doğu ABD**’yi seçin.|
    |||

4. **Boyut seçin** bölümünden bir sanal makine boyutu seçin.
5. **Ayarlar** için aşağıdaki değerleri seçin ve **Tamam**’a tıklayın:
    
    |Ayar|Değer|
    |---|---|
    |Sanal ağ| **Sanal ağ**'ı seçin, **Sanal ağ oluştur** bölümündeki **Ad** alanına *myVNet1*, alt ağ alanına da *mySubnet* yazın.|
    |Ağ Güvenliği Grubu|**Temel**'i seçin ve **Ortak gelen bağlantı noktası seçin** açılan menüsünde **HTTP** ve **RDP**'yi seçin. |
    |Önyükleme tanılamaları|**Devre dışı** girişini seçin.|
    |||

6. **Özet**’in **Oluştur** bölümünde **Oluştur**’u seçerek sanal makine dağıtımını başlatın.

7. Aşağıdaki değişikliklerle birlikte 1.-6. adımları tekrar tamamlayın:

    |Ayar|Değer|
    |---|---|
    |Kaynak grubu | **Yeni**'yi seçin ve *myResourceGroupTM2* yazın.|
    |Konum|Batı Avrupa|
    |VM Adı | myIISVMWEurope|
    |Sanal ağ | **Sanal ağ**'ı seçin ve **Sanal ağ oluştur** bölümündeki **Ad** alanına *myVNet2*, alt ağ alanına da *mySubnet* yazın.|
    |||

8. Sanal makinelerin oluşturulması birkaç dakika sürebilir. Her iki sanal makine de oluşturulmadan kalan adımlara devam etmeyin.

   ![VM oluşturma](./media/tutorial-traffic-manager-improve-website-response/createVM.png)

#### <a name="install-iis-and-customize-the-default-web-page"></a>IIS yükleme ve varsayılan web sayfasını özelleştirme

Bu bölümde, iIS sunucusunu iki VM'ye yüklersiniz - *myIISVMEastUS*  & *myIISVMWEurope*, ve sonra varsayılan web sitesi sayfasını güncellersiniz. Özelleştirilmiş web sitesi sayfası, web sitesini bir web tarayıcısından ziyaret ettiğinizde bağlandığınız VM'nin adını gösterecek.

1. Sol menüden **Tüm kaynaklar**’ı seçin ve kaynak listesinden, *myResourceGroupTM1* kaynak grubunda bulunan *myIISVMEastUS* öğesine tıklayın.
2. **Genel Bakış** sayfasında **Bağlan**'a tıklayın ve **Sanal makineye bağlanma** bölümünde **RDP dosyasını indir**'i seçin.
3. İndirilen rdp dosyasını açın. İstendiğinde **Bağlan**’ı seçin. Sanal makine oluştururken belirttiğiniz kullanıcı adını ve parolayı girin. Sanal makineyi oluştururken girdiğiniz kimlik bilgilerini belirtmek için **Diğer seçenekler**’i ve sonra **Farklı bir hesap kullan** seçeneğini belirlemeniz gerekebilir.
4. **Tamam'ı**seçin.
5. Oturum açma işlemi sırasında bir sertifika uyarısı alabilirsiniz. Uyarıyı alırsanız, bağlantıya devam etmek için **Evet** veya **Devam**et'i seçin.
6. Sunucu masaüstünde, **Windows Administrative Tools**>**Server Manager'a**gidin.
7. Windows PowerShell'i *myIISVMEastUS'ta* başlatın ve IIS sunucusunu yüklemek ve varsayılan HTM dosyasını güncelleştirmek için aşağıdaki komutları kullanarak.
    ```powershell-interactive
    # Install IIS
    Install-WindowsFeature -name Web-Server -IncludeManagementTools
    
    # Remove default htm file
    remove-item C:\inetpub\wwwroot\iisstart.htm
    
    #Add custom htm file
    Add-Content -Path "C:\inetpub\wwwroot\iisstart.htm" -Value $("Hello World from my test website server - " + $env:computername)
    ```
8. *myIISVMEastUS* ile RDP bağlantısını kapatın.
9. IIS'yi yüklemek ve varsayılan web sayfasını özelleştirmek için *myResourceGroupTM2* kaynak grubu içinde VM *myIISVMWEurope* ile bir RDP bağlantısı oluşturarak 1-6 adımlarını tekrarlayın.
10. Windows PowerShell'i *myIISVMWEurope'da* başlatın ve IIS sunucusunu yüklemek ve varsayılan HTM dosyasını güncelleştirmek için aşağıdaki komutları kullanarak.
    ```powershell-interactive
    # Install IIS
    Install-WindowsFeature -name Web-Server -IncludeManagementTools
    
    # Remove default htm file
    remove-item C:\inetpub\wwwroot\iisstart.htm
    
    #Add custom htm file
    Add-Content -Path "C:\inetpub\wwwroot\iisstart.htm" -Value $("Hello World from my production website server - " + $env:computername)
    ```

#### <a name="configure-dns-names-for-the-vms-running-iis"></a>IIS çalıştıran VM'lerin DNS adlarını yapılandırma

Traffic Manager, kullanıcı trafiğini hizmet uç noktalarının DNS adına göre yönlendirir. Bu bölümde, IIS sunucuları için DNS adlarını yapılandırırsınız - *myIISVMEastUS* ve *myIISVMWEurope.*

1. Sol menüden **Tüm kaynaklar**’a tıklayın ve kaynak listesinden, *myResourceGroupTM1* kaynak grubunda bulunan *myIISVMEastUS* öğesini seçin.
2. **Genel bakış** sayfasının **DNS adı** bölümünde **Yapılandır**'ı seçin.
3. **Yapılandır** sayfasının DNS adı bölümünde benzersiz bir ad ekleyip **Kaydet**'i seçin.
4. 1-3 arası adımları *myResourceGroupTM1* kaynak grubunda bulunan *myIISVMWEurope* adlı VM için tekrarlayın.

### <a name="create-test-vms"></a>Test amaçlı VM'leri oluşturma

Bu bölümde, her Azure bölgesinde **(Doğu ABD** ve **Batı Avrupa)** bir*VM (mVMEastUS* ve *myVMWestEurope)* oluşturursunuz. Bu VM'leri web sitesine göz attığınızda Traffic Manager'ın trafiği en yakın IIS sunucusuna nasıl yönlendirdiğini test etmek için kullanacaksınız.

1. Azure portalının sol üst köşesinde, **kaynak** > **Oluştur Windows** > **Server 2016 VM'yi**seçin.
2. **Temel Bilgiler** için aşağıdaki bilgileri girin veya seçin, kalan ayarlar için varsayılan değerleri kabul edin ve sonra **Oluştur**’u seçin:

    |Ayar|Değer|
    |---|---|
    |Adı|myVMEastUS|
    |Kullanıcı adı| Seçtiğiniz bir kullanıcı adını girin.|
    |Parola| Seçtiğiniz bir parolayı girin. Parola en az 12 karakter uzunluğunda olmalı ve [tanımlanmış karmaşıklık gereksinimlerini](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm) karşılamalıdır.|
    |Kaynak grubu| **Var olan**’ı seçin ve sonra *myResourceGroupTM1* öğesini seçin.|
    |||

4. **Boyut seçin** bölümünden bir sanal makine boyutu seçin.
5. **Ayarlar** için aşağıdaki değerleri seçin ve **Tamam**’a tıklayın:

    |Ayar|Değer|
    |---|---|
    |Sanal ağ| **Sanal ağ**'ı seçin ve **Sanal ağ oluştur** bölümündeki **Ad** alanına *myVNet3*, alt ağ alanına da *mySubnet3* yazın.|
    |Ağ Güvenliği Grubu|**Temel**'i seçin ve **Ortak gelen bağlantı noktası seçin** açılan menüsünde **HTTP** ve **RDP**'yi seçin. |
    |Önyükleme tanılamaları|**Devre dışı** girişini seçin.|
    |||

6. **Özet**’in **Oluştur** bölümünde **Oluştur**’u seçerek sanal makine dağıtımını başlatın.

7. Aşağıdaki değişikliklerle birlikte 1-5 arası adımları tekrar tamamlayın:

    |Ayar|Değer|
    |---|---|
    |VM Adı | *myVMWEurope*|
    |Kaynak grubu | **Var olan**’ı seçin ve sonra *myResourceGroupTM2* yazın.|
    |Sanal ağ | **Sanal ağ**'ı seçin ve **Sanal ağ oluştur** bölümündeki **Ad** alanına *myVNet4*, alt ağ alanına da *mySubnet4* yazın.|
    |||

8. Sanal makinelerin oluşturulması birkaç dakika sürebilir. Her iki sanal makine de oluşturulmadan kalan adımlara devam etmeyin.

## <a name="create-a-traffic-manager-profile"></a>Traffic Manager profili oluşturma
İsteğin kaynak IP adresine göre belirli uç noktalar döndürmenizi sağlayacak bir Traffic Manager profili oluşturun.

1. **Networking** > Ekranın sol üst tarafında, **kaynak** > Oluştur**Trafik Yöneticisi profili** > **Oluştur'u**seçin.
2. **Traffic Manager profili oluştur** ekranında aşağıdaki bilgileri girin veya seçin, kalan ayarlar için varsayılan değerleri kabul edin ve sonra **Oluştur**'u seçin:

    | Ayar                 | Değer                                              |
    | ---                     | ---                                                |
    | Adı                   | Bu adın trafficmanager.net bölgesinde benzersiz olması ve Traffic Manager profilinize erişmek için kullanılan trafficmanager.net DNS adı ile sonuçlanması gerekir.                                   |
    | Yönlendirme yöntemi          | **Alt ağ** yönlendirme yöntemini seçin.                                       |
    | Abonelik            | Aboneliğinizi seçin.                          |
    | Kaynak grubu          | **Var olan**’ı seçin ve sonra *myResourceGroupTM1* yazın. |
    | |                              |
    |

    ![Traffic Manager profili oluşturma](./media/traffic-manager-subnet-routing-method/create-traffic-manager-profile.png)

## <a name="add-traffic-manager-endpoints"></a>Traffic Manager uç noktalarını ekleme

Kullanıcı sorgusunun alt ağına göre kullanıcı trafiğini yönlendirmek için IIS sunucularını çalıştıran iki VM'yi ekleyin - *myIISVMEastUS* & *myIISVMWEurope.*

1. Portalın arama çubuğunda önceki bölümde oluşturduğunuz Traffic Manager profili adını arayın ve görüntülenen sonuçların arasından bu profili seçin.
2. **Traffic Manager profili** sayfasının **Ayarlar** bölümünde **Uç noktalar**'a ve ardından **Ekle**'ye tıklayın.
3. Aşağıdaki bilgileri girin veya seçin, kalan ayarlar için varsayılan değerleri kabul edin ve sonra **Tamam**’ı seçin:

    | Ayar                 | Değer                                              |
    | ---                     | ---                                                |
    | Tür                    | Azure uç noktası                                   |
    | Adı           | myTestWebSiteEndpoint                                        |
    | Hedef kaynak türü           | Genel IP Adresi                          |
    | Hedef kaynak          | Aynı abonelik altında Genel IP adreslerine sahip kaynakların listesini göstermek için **bir Genel IP adresi seçin.** **Kaynak** bölümünde *myIISVMEastUS-ip* adlı genel IP adresini seçin. Bu, Doğu ABD bölgesindeki IIS sunucusu VM'sinin IP adresidir.|
    |  Alt ağ yönlendirme ayarları    |   *myVMEastUS* test VM IP adresini ekleyin. Bu VM kaynaklanan herhangi bir kullanıcı sorgusu *myTestWebSiteEndpoint*yönlendirilir.    |

4. *MyIISVMWEurope*adlı IIS sunucusu VM ile ilişkili genel IP adresi *myIISVMWEurope-ip* için *myProductionEndpoint* adlı başka bir uç nokta eklemek için adımları 2 ve 3 tekrarlayın. **Subnet yönlendirme ayarları**için, test VM IP adresini ekleyin - *myVMWestEurope*. Bu test VM herhangi bir kullanıcı sorgusu bitiş noktasına yönlendirilecektir - *myProductionWebsiteEndpoint*.
5. Her iki uç noktanın eklenmesi tamamlandığında, **Çevrimiçi** izleme durumuyla birlikte **Traffic Manager profili** bölümünde gösterilir.

    ![Traffic Manager uç noktası ekleme](./media/traffic-manager-subnet-routing-method/customize-endpoint-with-subnet-routing-eastus.png)

## <a name="test-traffic-manager-profile"></a>Traffic Manager profilini test etme
Bu bölümde Traffic Manager'ın belirli bir alt ağdan gelen trafiği belirli bir uç noktaya nasıl yönlendirdiğini test edeceksiniz. Traffic Manager'ı uygulamalı olarak görmek için şu adımları gerçekleştirin:
1. Traffic Manager profilinizin DNS adını belirleyin.
2. Traffic Manager'ın nasıl çalıştığını görmek için şu adımları izleyin:
    - **Doğu ABD** bölgesinde bulunan*VM (myVMEastUS)* testinden, bir web tarayıcısında, Trafik Yöneticisi profilinizin DNS adına göz atın.
    - **Batı Avrupa** bölgesinde bulunan vm *(myVMEastUS)* testinden, bir web tarayıcısında, Trafik Yöneticisi profilinizin DNS adına göz atın.

### <a name="determine-dns-name-of-traffic-manager-profile"></a>Traffic Manager profilinin DNS adını belirleme
Bu öğreticide kolaylık olması açısından web sitelerini ziyaret etmek için Traffic Manager profilinin DNS adı kullanılmaktadır.

Traffic Manager profilinizin DNS adını belirlemek için şu adımları izleyin:

1. Portalın arama çubuğunda, önceki bölümde oluşturduğunuz **Trafik Yöneticisi profil** adını arayın. Görüntülenen sonuçların arasından Traffic Manager profilini seçin.
1. **Genel Bakış**'a tıklayın.
2. **Traffic Manager profili** penceresinde yeni oluşturduğunuz Traffic Manager profilinin DNS adı görüntülenir. Üretim dağıtımlarında bir gösterim etki alanı adını DNS CNAME kaydı kullanarak Traffic Manager etki alanı adına yönlendirirsiniz.

   ![Traffic Manager DNS adı](./media/traffic-manager-subnet-routing-method/traffic-manager-dns-name.png)

### <a name="view-traffic-manager-in-action"></a>Traffic Manager'ın nasıl çalıştığını görün
Bu bölümde Traffic Manager'ın nasıl çalıştığını görebilirsiniz.

1. Sol menüden **Tüm kaynaklar**’ı seçin ve kaynak listesinden, *myResourceGroupTM1* kaynak grubunda bulunan *myVMEastUS* öğesine tıklayın.
2. **Genel Bakış** sayfasında **Bağlan**'a tıklayın ve **Sanal makineye bağlanma** bölümünde **RDP dosyasını indir**'i seçin.
3. İndirilen rdp dosyasını açın. İstendiğinde **Bağlan**’ı seçin. Sanal makine oluştururken belirttiğiniz kullanıcı adını ve parolayı girin. Sanal makineyi oluştururken girdiğiniz kimlik bilgilerini belirtmek için **Diğer seçenekler**’i ve sonra **Farklı bir hesap kullan** seçeneğini belirlemeniz gerekebilir.
4. **Tamam'ı**seçin.
5. Oturum açma işlemi sırasında bir sertifika uyarısı alabilirsiniz. Uyarıyı alırsanız, bağlantıya devam etmek için **Evet** veya **Devam**et'i seçin.
1. Web sitesini görüntülemek için *myVMEastUS* adlı VM'de bir web tarayıcısında Traffic Manager profilinizin DNS adını yazın. VM *myVMEastUS* IP adresi uç noktası *myIISVMEastUS*ile ilişkili olduğundan, web tarayıcısı Test web sitesi sunucusu başlattı - *myIISVMEastUS*.

   ![Traffic Manager profilini test etme](./media/traffic-manager-subnet-routing-method/test-traffic-manager.png)

2. Ardından, 1-5 adımlarını kullanarak **Batı Avrupa'da** bulunan VM *myVMWestEurope'a* bağlanın ve bu VM'den Traffic Manager profil etki alanına göz atın. VM *myVMWestEurope* IP adresi uç noktası *myIISVMEastUS*ile ilişkili olduğundan, web tarayıcısı Test web sitesi sunucusu başlattı - *myIISVMWEurope*.

## <a name="delete-the-traffic-manager-profile"></a>Traffic Manager profilini silme
İhtiyacınız kalmadığında kaynak gruplarını (**ResourceGroupTM1** ve **ResourceGroupTM2**) silebilirsiniz. Bunun için kaynak grubunu (**ResourceGroupTM1** veya **ResourceGroupTM2**) ve ardından **Sil**'i seçin.

## <a name="next-steps"></a>Sonraki adımlar

- [Ağırlıklı trafik yönlendirme yöntemi](traffic-manager-configure-weighted-routing-method.md) hakkında bilgi edinin.
- [Öncelik yönlendirme yöntemi](traffic-manager-configure-priority-routing-method.md) hakkında bilgi edinin.
- [Coğrafi yönlendirme yöntemi](traffic-manager-configure-geographic-routing-method.md) hakkında bilgi edinin.
