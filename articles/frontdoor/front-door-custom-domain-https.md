---
title: Öğretici - Azure Ön Kapı için özel bir etki alanında HTTPS'yi yapılandırın | Microsoft Dokümanlar
description: Bu eğitimde, özel bir etki alanı için Azure Ön Kapı yapılandırmanızda HTTPS'yi etkinleştirmeyi ve devre dışı kmayı öğrenirsiniz.
services: frontdoor
documentationcenter: ''
author: sharad4u
editor: ''
ms.service: frontdoor
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 10/05/2018
ms.author: sharadag
ms.openlocfilehash: efe2c96c619aaf92efc5b4abf76b6b89c96ebd37
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/08/2020
ms.locfileid: "80878043"
---
# <a name="tutorial-configure-https-on-a-front-door-custom-domain"></a>Öğretici: Front Door özel etki alanı üzerinde HTTPS'yi yapılandırma

Bu öğreticide ön uç konakları bölümü altında Front Door’unuzla ilişkili özel bir etki alanı için HTTPS protokolünün nasıl etkinleştirileceği gösterilir. Özel etki alanınızda HTTPS protokolünü kullanarak (örneğin, https:\//www.contoso.com), hassas veriler internet üzerinden gönderildiğinde bunların TLS/SSL şifrelemesi ile güvenli bir şekilde teslim edilmesini sağlarsınız. Web tarayıcınız HTTPS üzerinden bir web sitesine bağlanırken, web sitesinin güvenlik sertifikasını onaylar ve bu sertifikanın yasal bir sertifika yetkilisi tarafından verildiğini doğrular. Bu işlem güvenlik sağlar ve web uygulamalarınızı saldırılara karşı korur.

Azure Ön Kapı, varsayılan olarak ön kapı varsayılan ana bilgisayar adında HTTPS'yi destekler. Örneğin, bir Front Door (https:\//contoso.azurefd.net gibi) oluşturursanız, https://contoso.azurefd.net'e yönelik istekler için HTTPS otomatik olarak etkinleştirilir. Öte yandan, 'www.contoso.com' özel etki alanını ekledikten sonra bu ön uç konağı için HTTPS'yi etkinleştirmeniz gerekecektir.   

Özel HTTPS özelliğinin en önemli niteliklerinden bazıları şunlardır:

- Ek ücret yoktur: Sertifika edinme veya yenileme işlemleri için herhangi bir maliyet söz konusu değildir ve HTTPS trafiği için ek ücret alınmaz. 

- Basit etkinleştirme: Tek tıklamayla sağlama özelliği [Azure portalından](https://portal.azure.com) kullanılabilir. Özelliği etkinleştirmek için REST API’yi veya diğer geliştirici araçlarını kullanabilirsiniz.

- Eksiksiz sertifika yönetimi kullanılabilir: Sizin için tüm sertifika tedariki ve yönetimi gerçekleştirilir. Sertifikalar sona ermeden önce otomatik olarak sağlanır ve yenilenir. Bu da sertifika süre sonu nedeniyle hizmette yaşanabilecek kesinti risklerini ortadan kaldırır.

Bu öğreticide şunların nasıl yapıldığını öğrenirsiniz:
> [!div class="checklist"]
> - Özel etki alanınızda HTTPS protokolünü etkinleştirme.
> - AFD tarafından yönetilen sertifikayı kullanma 
> - Kendi sertifikanızı, yani özel bir TLS/SSL sertifikasını kullanın
> - Etki alanını doğrulama
> - Özel etki alanınızda HTTPS protokolünü devre dışı bırakma


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Ön koşullar

Bu öğreticideki adımları tamamlayabilmeniz için öncelikle bir Front Door oluşturmalı ve en az bir özel etki alanı eklemelisiniz. Daha fazla bilgi için bkz. [Öğretici: Front Door’unuza özel etki alanı ekleme](front-door-custom-domain.md).

## <a name="tlsssl-certificates"></a>TLS/SSL sertifikaları

Ön Kapı özel etki alanında güvenli bir şekilde içerik sunmak için HTTPS protokolünü etkinleştirmek için TLS/SSL sertifikası kullanmanız gerekir. Azure Ön Kapı tarafından yönetilen bir sertifikayı kullanmayı veya kendi sertifikanızı kullanmayı seçebilirsiniz.


### <a name="option-1-default-use-a-certificate-managed-by-front-door"></a>Seçenek 1 (varsayılan): Front Door tarafından yönetilen bir sertifika kullanın

Azure Ön Kapı tarafından yönetilen bir sertifika kullandığınızda, HTTPS özelliği yalnızca birkaç tıklamayla açılabilir. Azure Ön Kapı, satın alma ve yenileme gibi sertifika yönetimi görevlerini tamamen işler. Özelliği etkinleştirmenizin ardından işlem hemen başlar. Özel etki alanı önceden Front Door’un varsayılan ön uç konağına (`{hostname}.azurefd.net`) eşlendiyse başka bir eylem gerekmez. Front Door, adımları işler ve isteğinizi otomatik olarak tamamlar. Ancak özel etki alanınız başka bir yerde eşlendiyse, etki alanı sahipliğinizi doğrulamak için e-posta kullanmanız gerekir.

Özel bir etki alanı üzerinde HTTPS'yi etkinleştirmek için aşağıdaki adımları uygulayın:

1. [Azure portalda](https://portal.azure.com)**Front Door** profilinize göz atın.

2. Ön uç konakları listesinde, özel etki alanınızın eklenmesi için HTTPS’yi etkinleştirmek istediğiniz özel etki alanını seçin.

3. **Özel etki alanı HTTPS’si** bölümünde **Etkin**’e tıklayın ve sertifika kaynağı olarak **Front Door ile yönetilen**’i seçin.

4. Kaydet’e tıklayın.

5. [Etki alanını doğrulama](#validate-the-domain) adımına ilerleyin.


### <a name="option-2-use-your-own-certificate"></a>Seçenek 2: Kendi sertifikanızı kullanın

HTTPS özelliğini etkinleştirmek için kendi sertifikanızı kullanabilirsiniz. Bu işlem, sertifikalarınızı güvenli bir şekilde depolamanıza olanak tanıyan Azure Key Vault ile tümleştirme yoluyla gerçekleştirilir. Azure Ön Kapı, sertifikanızı almak için bu güvenli mekanizmayı kullanır ve birkaç ek adım gerektirir. TLS/SSL sertifikanızı oluştururken, sertifikayı izin verilen bir sertifika yetkilisi (CA) ile oluşturmanız gerekir. Buna karşılık, izin verilmeyen bir CA kullanırsanız isteğiniz reddedilir. İzin verilen CA'ların listesi için, [Azure Ön Kapı'da özel HTTPS etkinleştirmek için İzin Verilen sertifika yetkililerine](front-door-troubleshoot-allowed-ca.md)bakın.

#### <a name="prepare-your-azure-key-vault-account-and-certificate"></a>Azure Key Vault hesabınızı ve sertifikanızı hazırlama
 
1. Azure Key Vault: Özel HTTPS’yi etkinleştirmek istediğiniz Front Door’unuzla aynı abonelik altında çalışan bir Azure Key Vault hesabınız olması gerekir. Azure Key Vault hesabınız yoksa oluşturun.

> [!WARNING]
> Azure Ön Kapı şu anda Yalnızca Ön Kapı yapılandırması ile aynı abonelikteki Key Vault hesaplarını destekler. Front Door’dan farklı bir abonelik altındaki Key Vault’un seçilmesi hatayla sonuçlanır.

2. Azure Key Vault sertifikaları: Zaten bir sertifikanız varsa, bu sertifikayı doğrudan Azure Key Vault hesabınıza yükleyebilir veya doğrudan Azure Key Vault’un tümleştirildiği iş ortağı CA'lardan birinin Azure Key Vault’u üzerinden yeni bir sertifika oluşturabilirsiniz. Sertifikanızı **gizli**değil, **sertifika** nesnesi olarak yükleyin.

> [!NOTE]
> Kendi TLS/SSL sertifikanız için Front Door, EC şifreleme algoritmalarına sahip sertifikaları desteklemez.

#### <a name="register-azure-front-door"></a>Azure Ön Kapıyı Kaydedin

PowerShell aracılığıyla Azure Active Directory'nizde Azure Ön Kapı'nın hizmet sorumlusunu uygulama olarak kaydedin.

> [!NOTE]
> Bu eylem, Genel Yönetici izinleri gerektirir ve kiracı başına yalnızca **bir kez** gerçekleştirilmesi gerekir.

1. Gerekirse yerel makinenizdeki PowerShell’de [Azure PowerShell](/powershell/azure/install-az-ps)’i yükleyin.

2. PowerShell’de aşağıdaki komutu çalıştırın:

     `New-AzADServicePrincipal -ApplicationId "ad0e1c7e-6d38-4ba4-9efd-0bc77ba9f037"`              

#### <a name="grant-azure-front-door-access-to-your-key-vault"></a>Azure Ön Kapı erişimini anahtar kasanıza verme
 
Azure Key Vault hesabınızdaki sertifikalara erişmek için Azure Ön Kapı izni ver.

1. Anahtar kasası hesabınızda AYARLAR bölümünden **Erişim ilkeleri**’ni ve sonra **Yeni ekle**’yi seçip yeni bir ilke oluşturun.

2. **Sorumlu seç** bölümünde **ad0e1c7e-6d38-4ba4-9efd-0bc77ba9f037**'yi arayın ve **Microsoft.Azure.Frontdoor**’u seçin. **Seç'i**tıklatın.

3. **Gizli izinlerde,** Ön Kapı'nın **sertifikayı** almasına izin ver'i seçin.

4. **Sertifika izinlerinde,** Ön Kapı'nın sertifikayı almasına izin ver'i **seçin.**

5. **Tamam'ı**seçin. 

    Azure Ön Kapı artık bu Anahtar Kasasına ve bu Anahtar Kasası'nda depolanan sertifikalara erişebilir.
 
#### <a name="select-the-certificate-for-azure-front-door-to-deploy"></a>Dağıtmak için Azure Ön Kapı sertifikasını seçin
 
1. Portalda Front Door’unuza dönün. 

2. Özel etki alanları listesinde, HTTPS'yi etkinleştirmek istediğiniz özel etki alanını seçin.

    **Özel etki alanı** sayfası görünür.

3. Sertifika yönetimi türü bölümünde **Kendi sertifikamı kullan**’ı seçin. 

4. Azure Ön Kapı, Key Vault hesabının aboneliğinin Ön Kapınızla aynı olması gerekir. Bir anahtar kasası, sertifika (gizli gizi) ve sertifika sürümü seçin.

    Azure Ön Kapı aşağıdaki bilgileri listeler: 
    - Abonelik kimliğiniz için anahtar kasası hesapları. 
    - Seçilen anahtar kasası altındaki sertifikalar (gizli diziler). 
    - Kullanılabilir sertifika sürümleri. 
 
5. Kendi sertifikanızı kullanıyorsanız etki alanı doğrulaması gerekmez. [Yayılma için bekleme](#wait-for-propagation) adımına geçin.

## <a name="validate-the-domain"></a>Etki alanını doğrulama

CNAME kaydı kullanılarak özel uç noktanızla eşlenen ve kullanılmakta olan bir özel etki alanınız zaten varsa ya da kendi sertifikanızı kullanıyorsanız şu adıma geçin:  
[Özel etki alanı, Front Door’unuzla eşlendi](#custom-domain-is-mapped-to-your-front-door-by-a-cname-record). Böyle bir etki alanınız yoksa, etki alanınıza yönelik CNAME kaydı girişinin artık mevcut olmaması veya afdverify alt etki alanını içeriyor olması durumunda [Özel etki alanı Front Door’unuzla eşlenmedi](#custom-domain-is-not-mapped-to-your-front-door) adımına geçin.

### <a name="custom-domain-is-mapped-to-your-front-door-by-a-cname-record"></a>Özel etki alanı bir CNAME kaydı kullanılarak Front Door’unuzla eşlendi

Front Door’unuzun ön uç konaklarına özel etki alanı eklediğinizde etki alanı kayıt yetkilinizin DNS tablosunda, Front Door’unuzun varsayılan .azurefd.net konak adıyla eşlenecek bir CNAME kaydı oluşturmuş oldunuz. Bu CNAME kaydı hala mevcutsa ve afdverify alt etki alanını içermiyorsa DigiCert Sertifika Yetkilisi (CA), özel etki alanınızın sahipliğini otomatik olarak doğrulamak için bunu kullanır. 

Kendi sertifikanızı kullanıyorsanız etki alanı doğrulaması gerekmez.

CNAME kaydınız, *Ad*’ın özel etki alanınız, *Değer*’in ise Front Door’unuzun varsayılan .azurefd.net konak adı olduğu aşağıdaki biçimde olmalıdır:

| Adı            | Tür  | Değer                 |
|-----------------|-------|-----------------------|
| <www.contoso.com> | CNAME | contoso.azurefd.net |

CNAME kayıtları hakkında daha fazla bilgi için bkz. [CNAME DNS kaydı oluşturma](https://docs.microsoft.com/azure/cdn/cdn-map-content-to-custom-domain).

CNAME kaydınız doğru biçimdeyse DigiCert, özel etki alanı adınızı otomatik olarak doğrular ve etki alanı adınız için ayrılmış bir sertifika oluşturur. DigitCert size doğrulama e-postası göndermez ve isteğinizi onaylamanız gerekmez. Sertifika bir yıl için geçerlidir ve süresi dolmadan önce otomatik olarak yenilenir. [Yayılma için bekleme](#wait-for-propagation) adımına geçin. 

Otomatik doğrulama genellikle birkaç dakika sürer. Bir saat içinde etki alanınızı doğrulanmış olarak görmüyorsanız destek bileti açın.

>[!NOTE]
>DNS sağlayıcınız ile bir Sertifika Yetkilisi Yetkilendirme (CAA) kaydınız varsa bunun geçerli CA olarak DigiCert‘i içermesi gerekir. CAA kaydı; etki alanı sahiplerinin DNS sağlayıcıları ile hangi CA'ların, etki alanları için sertifika vermeye yetkili olduğunu belirtmesini sağlar. Bir CA, CAA kaydına sahip bir etki alanı için sertifika siparişi alırsa ve bu CA, sertifika vermeye yetkili olarak listelenmemişse bu CA’nın, söz konusu etki alanına veya alt etki alanına sertifika vermesi yasaklanır. CAA kayıtlarını yönetme ile ilgili bilgi için bkz. [CAA kayıtlarını yönetme](https://support.dnsimple.com/articles/manage-caa-record/). CAA kayıt aracı için bkz. [CAA Kayıt Yardımcısı](https://sslmate.com/caa/).

### <a name="custom-domain-is-not-mapped-to-your-front-door"></a>Özel etki alanı Front Door’unuzla eşlenmedi

Uç noktanız için CNAME kaydı girişi artık mevcut değilse veya afdverify alt etki alanını içeriyorsa bu adımın diğer yönergelerini uygulayın.

Özel etki alanınızda HTTPS'yi etkinleştirdikten sonra, DigiCert CA etki alanının [WHOIS](http://whois.domaintools.com/) kayıt yetkilisi bilgisine göre kayıt yetkilisiyle iletişim kurarak etki alanınızın sahipliğini doğrular. İletişim, WHOIS kaydında belirtilen e-posta adresi (varsayılan) veya telefon numarası aracılığıyla kurulur. HTTPS, özel etki alanınızda etkin hale gelmeden önce etki alanı doğrulamasını tamamlamanız gerekir. Etki alanını onaylamak için altı iş gününüz vardır. Altı iş günü içinde onaylanmamış istekler otomatik olarak iptal edilir. 

![WHOIS kaydı](./media/front-door-custom-domain-https/whois-record.png)

DigiCert ayrıca ek e-posta adreslerine de bir doğrulama e-postası gönderir. WHOIS kayıt yetkilisi bilgileri özelse doğrudan şu adreslerden birini kullanarak onaylayabildiğinizi doğrulayın:

admin@&lt;etki-alanı-adınız.com&gt;  
administrator@&lt;etki-alanı-adınız.com&gt;  
webmaster@&lt;etki-alanı-adınız.com&gt;  
hostmaster@&lt;etki-alanı-adınız.com&gt;  
postmaster@&lt;.com&gt;  

Birkaç dakika içinde sizden isteği onaylamanızı isteyen, aşağıdaki örneğe benzer bir e-posta alırsınız. Spam filtresi kullanıyorsanız, izin admin@digicert.com verenler listesine ekleyin. E-postayı 24 saat içinde almazsanız Microsoft destek ekibine başvurun.

Onay bağlantısına tıkladığınızda, çevrimiçi bir onay formuna yönlendirilirsiniz. Formdaki yönergeleri uygulayın. İki doğrulama seçeneğiniz vardır:

- contoso.com gibi aynı kök etki alanı için aynı hesap üzerinden verilen gelecekteki tüm siparişleri onaylayabilirsiniz. Aynı kök etki alanı için ek özel etki alanları eklemeyi planlıyorsanız bu yaklaşım önerilir.

- Yalnızca bu istekte kullanılan söz konusu ana bilgisayar adını onaylayabilirsiniz. Sonraki istekler için ek onay gereklidir.

DigiCert onaydan sonra özel etki alanı adınız için sertifika oluşturma işlemlerini tamamlar. Sertifika bir yıl için geçerlidir ve süresi dolmadan önce otomatik olarak yenilenir.

## <a name="wait-for-propagation"></a>Yayılma için bekleme

Etki alanı doğrulandıktan sonra özel etki alanı HTTPS özelliğinin etkinleştirilmesi 6-8 saate kadar sürebilir. İşlem tamamlandığında, Azure portalındaki özel HTTPS durumu **Etkin** olarak ayarlanır ve özel etki alanı iletişim kutusundaki dört işlem adımı tamamlandı olarak işaretlenir. Özel etki alanınız artık HTTPS’yi kullanmak için hazırdır.

### <a name="operation-progress"></a>İşlem ilerleme durumu

Aşağıdaki tabloda, HTTPS’yi etkinleştirdiğinizde oluşan işlem ilerleme durumunu gösterir. HTTPS’yi etkinleştirmenizin ardından özel etki alanı iletişim kutusunda dört işlem adımı görünür. Her adım etkin hale gelir ve ilerledikçe adımın altında ek alt adım ayrıntıları görüntülenir. Bu alt adımların hiçbiri gerçekleşmez. Bir adım başarıyla tamamlandıktan sonra adımın yanında yeşil bir onay işareti görünür. 

| İşlem adımı | İşlem alt adımı ayrıntıları | 
| --- | --- |
| 1 İstek gönderiliyor | İstek gönderiliyor |
| | HTTPS isteğiniz gönderiliyor. |
| | HTTPS isteğiniz başarıyla gönderildi. |
| 2 Etki alanı doğrulaması | Front Door’unuzun varsayılan .azurefd.net ön uç konağıyla eşlenen CNAME ise etki alanı otomatik olarak doğrulanır. Eşleme yapılmadıysa etki alanınızın kayıt kaydında listelenen e-posta adresine (WHOIS kayıt şirketi) bir doğrulama isteği gönderilir. Etki alanını mümkün olan en kısa süre içinde doğrulayın. |
| | Etki alanı sahipliğiniz başarıyla doğrulandı. |
| | Etki alanı doğrulama isteğinin süresi doldu. (Müşteri büyük olasılıkla 6 gün içinde yanıt vermedi.) HTTPS, etki alanınızda etkinleştirilmeyecek. * |
| | Etki alanı sahipliğini doğrulama isteği, müşteri tarafından reddedildi. HTTPS, etki alanınızda etkinleştirilmeyecek. * |
| 3 Sertifika sağlanıyor | Sertifika yetkilisi şu anda etki alanınızdaki HTTPS'nin etkinleştirilmesi için gereken sertifikayı veriyor. |
| | Sertifika verildi ve şu anda Front Door’unuz için dağıtılıyor. Bu işlem 1 saat kadar sürebilir. |
| | Sertifika, Front Door’unuz için başarıyla dağıtıldı. |
| 4 Tamamlandı | HTTPS, etki alanınızda başarıyla etkinleştirildi. |

\* Hata oluşmadığı sürece bu ileti görüntülenmez. 

İstek gönderilmeden önce hata oluşursa, aşağıdaki hata iletisi görüntülenir:

<code>
We encountered an unexpected error while processing your HTTPS request. Please try again and contact support if the issue persists.
</code>



## <a name="clean-up-resources---disable-https"></a>Kaynakları temizleme - HTTPS’yi devre dışı bırakma

Önceki adımlarda özel etki alanınızda HTTPS protokolünü etkinleştirdiniz. Artık HTTPS ile özel etki alanınızı kullanmak istiyorsanız, aşağıdaki adımları uygulayarak HTTPS’yi devre dışı bırakabilirsiniz:

### <a name="disable-the-https-feature"></a>HTTPS özelliğini devre dışı bırakma 

1. Azure [portalında](https://portal.azure.com)Azure Ön **Kapı** yapılandırmanıza göz atın.

2. Ön uç konakları listesinde HTTPS’yi devre dışı bırakmak istediğiniz özel etki alanına tıklayın.

3. HTTPS’yi devre dışı bırakmak için **Devre Dışı**’na ve sonra da **Kaydet**’e tıklayın.

### <a name="wait-for-propagation"></a>Yayılma için bekleme

Özel etki alanı HTTPS özelliği devre dışı bırakıldıktan sonra bu işlemin geçerli olması 6-8 saate kadar sürebilir. İşlem tamamlandığında, Azure portalındaki özel HTTPS durumu **Devre Dışı** olarak ayarlanır ve özel etki alanı iletişim kutusundaki üç işlem adımı tamamlandı olarak işaretlenir. Özel etki alanınız artık HTTPS’yi kullanamaz.

#### <a name="operation-progress"></a>İşlem ilerleme durumu

Aşağıdaki tabloda, HTTPS’yi devre dışı bıraktığınızda oluşan işlem ilerleme durumunu gösterir. HTTPS’yi devre dışı bırakmanızın ardından özel etki alanı iletişim kutusunda üç işlem adımı görünür. Her adım etkin hale gelir ve adımın altında ek ayrıntılar görüntülenir. Bir adım başarıyla tamamlandıktan sonra adımın yanında yeşil bir onay işareti görünür. 

| İşlem ilerleme durumu | İşlem ayrıntıları | 
| --- | --- |
| 1 İstek gönderiliyor | İsteğiniz gönderiliyor |
| 2 Sertifika sağlaması kaldırılıyor | Sertifika siliniyor |
| 3 Tamamlandı | Sertifika silindi |

## <a name="frequently-asked-questions"></a>Sık sorulan sorular

1. *Sertifika sağlayıcısı kimdir ve ne tür bir sertifika kullanılır?*

    Özel etki alanınız için Digicert tarafından sağlanan ayrılmış/tek bir sertifika kullanılır. 

2. *IP tabanlı veya SNI TLS/SSL kullanıyor musunuz?*

    Azure Ön Kapı SNI TLS/SSL kullanır.

3. *DigiCert’ten etki alanı doğrulama e-postası almazsam ne olur?*

    Özel etki alanınız için doğrudan uç nokta ana bilgisayar adına işaret eden bir CNAME girişiniz varsa (ve afdverify alt etki alanı adını kullanmıyorsanız) etki alanı doğrulama e-postası almazsınız. Doğrulama otomatik olarak gerçekleşir. Ancak CNAME girişiniz yoksa ve e-postayı 24 saat içinde almazsanız Microsoft destek ekibine başvurun.

4. *Ayrılmış sertifika kullanmak, SAN sertifikasından daha mı güvenlidir?*
    
    SAN sertifikası, ayrılmış sertifika ile aynı şifreleme ve güvenlik standartlarını uygular. Verilen tüm TLS/SSL sertifikaları gelişmiş sunucu güvenliği için SHA-256 kullanır.

5. *DNS sağlayıcım ile Sertifika Yetkilisi Yetkilendirme kaydı kullanmam gerekir mi?*

    Hayır, Sertifika Yetkilisi Yetkilendirme kaydı şu anda gerekli değildir. Ancak, varsa, geçerli CA olarak DigiCert’i içermelidir.


## <a name="next-steps"></a>Sonraki adımlar

- [Front Door oluşturmayı](quickstart-create-front-door.md) öğrenin.
- [Front Door’un nasıl çalıştığını](front-door-routing-architecture.md) öğrenin.
