---
title: Sık sorulan sorular (SSS) - Azure Etkin Dizini | Microsoft Dokümanlar
description: Azure ve Azure Etkin Dizini, parola yönetimi ve uygulama erişimi hakkında sık sorulan sorular ve yanıtlar.
services: active-directory
author: msaburnley
manager: daveba
ms.assetid: b8207760-9714-4871-93d5-f9893de31c8f
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: conceptual
ms.date: 11/12/2018
ms.author: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9f4a961e601949689db89f8819f0a1fe1c5a7b3a
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/08/2020
ms.locfileid: "80875801"
---
# <a name="frequently-asked-questions-about-azure-active-directory"></a>Azure Etkin Dizini hakkında sık sorulan sorular
Azure Active Directory (Azure AD), kimlik, erişim yönetimi ve güvenliği tüm yönleriyle kapsayan bir hizmet olarak kimlik (IDaaS) çözümüdür.

Daha fazla bilgi için bkz. [Azure Active Directory nedir?](active-directory-whatis.md).


## <a name="access-azure-and-azure-active-directory"></a>Azure ve Azure Active Directory erişimi
**S: Azure portalında Azure AD'ye erişmeye çalıştığımda neden "Abonelik bulunamadı" alıyorum?**

**Y:** Azure portalına erişmek için her kullanıcının bir Azure aboneliğiyle birlikte izinleri olmalıdır. Ücretli bir Office 365 veya Azure AD aboneliğiniz yoksa, ücretsiz bir [Azure hesabı](https://azure.microsoft.com/free/
) veya ücretli abonelik etkinleştirmeniz gerekir.

Daha fazla bilgi için bkz.

* [Azure aboneliklerinin Azure Active Directory ile ilişkisi](active-directory-how-subscriptions-associated-directory.md)

---
**S: Azure AD, Office 365 ve Azure arasındaki ilişki nedir?**

**Y:** Azure AD, tüm web hizmetlerine yönelik ortak kimlik ve erişim işlevleri sunar. Office 365, Microsoft Azure, Intune veya diğer uygulamalardan hangisini kullanırsanız kullanın bu hizmetlerin tümü için oturum açma ve erişim yönetimini etkinleştirmek üzere zaten Azure AD'yi kullanırsınız.

Web hizmetlerini kullanacak şekilde ayarlanmış tüm kullanıcılar, bir veya daha fazla Azure AD örneğinde kullanıcı hesabı olarak tanımlanır. Bulut uygulama erişimi gibi ücretsiz Azure AD özellikleri için bu hesapları ayarlayabilirsiniz.

Enterprise Mobility + Security gibi ücretli Azure AD hizmetleri, kurumsal ölçekte kapsamlı yönetim ve güvenlik çözümleriyle Office 365 ve Microsoft Azure gibi diğer web hizmetlerini tamamlar.

---

**S: Sahibi ve Genel Yönetici arasındaki farklar nelerdir?**

**A:** Varsayılan olarak, Bir Azure aboneliğine kaydolan kişiye Azure kaynakları için Sahip rolü atanır. İçerik Sahibi, Azure aboneliğinin ilişkili olduğu dizinden bir Microsoft hesabı veya bir çalışma veya okul hesabı kullanabilir.  Bu rol Azure portaldaki hizmetleri yönetme yetkisine sahiptir.

Başkalarının aynı aboneliği kullanarak oturum açmaları ve hizmetlere erişmeleri gerekiyorsa, onlara uygun [yerleşik rolü](../../role-based-access-control/built-in-roles.md)atayabilirsiniz. Daha fazla bilgi için [RBAC ve Azure portalını kullanarak erişimi yönet'e](../../role-based-access-control/role-assignments-portal.md)bakın.

Varsayılan olarak, bir Azure aboneliğiiçin kaydolan kişiye dizin için Global Administrator rolü atanır. Global Administrator tüm Azure AD dizin özelliklerine erişebilir. Azure AD, dizin ve kimlikle ilgili özellikleri yönetmek için farklı yönetici rolleri kümesine sahiptir. Bu yöneticiler Azure portalındaki çeşitli özelliklere erişebilirler. Yöneticinin rolü, kullanıcıları oluşturma veya yönetme, başkalarına yönetim rolleri atama, kullanıcı parolalarını sıfırlama, kullanıcı lisanslarını yönetme veya etki alanlarını yönetme gibi neler yapabileceklerini belirler.  Azure REKLAM dizin yöneticileri ve rolleri hakkında daha fazla bilgi için [bkz.](active-directory-users-assign-role-azure-portal.md) [Assigning administrator roles in Azure Active Directory](../users-groups-roles/directory-assign-admin-roles.md)

Ayrıca, Enterprise Mobility + Security gibi ücretli Azure AD hizmetleri, kurumsal ölçekte kapsamlı yönetim ve güvenlik çözümleriyle Office 365 ve Microsoft Azure gibi diğer web hizmetlerini tamamlar.

---
**S: Azure AD kullanıcı lisanslarımın ne zaman sona ereceğini gösteren bir rapor var mı?**

**A:** №.  Bu özellik şu an kullanılamıyor.

---

## <a name="get-started-with-hybrid-azure-ad"></a>Karma Azure AD ile çalışmaya başlama


**S: Ortak çalışan olarak eklendiğimde kiracıdan nasıl ayrılabilirim?**

**Y:** Başka bir kuruluşun kiracısına ortak çalışan olarak eklendiğinizde, kiracılar arasında geçiş yapmak için sağ üst köşedeki "kiracı değiştiricisi" seçeneğini kullanabilirsiniz.  Şu anda, davet eden kuruluştan ayrılma seçeneği mevcut değildir ve Microsoft bu işlevselliği sunmak için çalışmaktadır.  Bu özellik kullanılabilir oluncaya kadar, davet eden kuruluştan sizi kiracısından kaldırmasını isteyebilirsiniz.

---
**S: Şirket içi dizinimi Azure AD'ye nasıl bağlayabilirim?**

**Y:** Şirket içi dizininizi Azure AD'ye bağlamak için Azure AD Connect'i kullanabilirsiniz.

Daha fazla bilgi için bkz. [Şirket içi kimliklerinizi Azure Active Directory ile tümleştirme](../hybrid/whatis-hybrid-identity.md).

---
**S: Şirket içi dizinim ve bulut uygulamalarım arasında SSO'yu nasıl ayarlarım?**

**Y:** Çoklu oturum açmayı (SSO) yalnızca şirket içi dizininiz ve Azure AD arasında ayarlamanız yeterlidir. Bulut uygulamalarınıza Azure AD üzerinden eriştiğiniz sürece, hizmet otomatik olarak kullanıcılarınızı şirket içi kimlik bilgileriyle doğru şekilde kimlik doğrulaması gerçekleştirmeye yönlendirir.

SSO'nun şirket içinde uygulanması, Active Directory Federation Services (AD FS) gibi federasyon çözümleri yle veya şifre karma senkronizasyonu yapılandırılarak kolayca elde edilebilir. Azure AD Connect yapılandırma sihirbazını kullanarak her iki seçeneği de kolayca dağıtabilirsiniz.

Daha fazla bilgi için bkz. [Şirket içi kimliklerinizi Azure Active Directory ile tümleştirme](../hybrid/whatis-hybrid-identity.md).

---
**S: Azure AD, kuruluşumdaki kullanıcılar için bir self servis portal sağlar mı?**

**Y:** Evet, AD size kullanıcı self servis ve uygulama erişimi için [Azure AD Erişim Paneli](https://myapps.microsoft.com)'ni sağlar. Office 365 müşterisiyseniz, [Office 365 portalında](https://portal.office.com)aynı özelliklerin çoğunu bulabilirsiniz.

Daha fazla bilgi için bkz. [Erişim Paneli'ne Giriş](../user-help/active-directory-saas-access-panel-introduction.md).

---
**S: Azure AD, şirket içi altyapımı yönetmeme yardımcı olur mu?**

**Y:** Evet. Azure AD Premium sürümü size Azure AD Connect Health olanağını sağlar. Azure AD Connect Health, şirket içi kimlik altyapınızı ve eşitleme hizmetlerini izlemenize ve daha iyi kavramanıza yardımcı olur.  

Daha fazla bilgi için bkz. [Bulutta şirket içi kimlik altyapınızı ve eşitleme hizmetlerinizi izleme](../hybrid/whatis-hybrid-identity-health.md).  

---
## <a name="password-management"></a>Parola yönetimi
**S: Azure AD parola yazma'yı parola eşitlemeden kullanabilir miyim? (Bu senaryoda, parola geri yazma yla Azure AD self servis parola sıfırlama (SSPR) kullanmak ve parolaları bulutta depolamamak mümkün mü?)**

**Y:** Geri yazmayı etkinleştirmek için Active Directory parolalarınızı Azure AD ile eşitlemeniz gerekmez. Birleştirilmiş bir ortamda Azure AD çoklu oturum açma (SSO), kullanıcının kimliğini doğrulamak için şirket içi dizini kullanır. Bu senaryoda, şirket içi parolanın Azure AD'de izlenmesi gerekmez.

---
**S: Bir parolanın Active Directory şirket içi konumuna geri yazılması ne kadar zaman alır?**

**Y:** Parola geri yazma işlemi gerçek zamanlı olarak gerçekleşir.

Daha fazla bilgi için bkz. [Parola yönetimine başlarken](../authentication/quickstart-sspr.md).

---
**S: Parola geri yazma özelliğini bir yönetici tarafından yönetilen parolalarla kullanabilir miyim?**

**Y:** Evet, parola geri yazma özelliğini etkinleştirdiyseniz bir yönetici tarafından gerçekleştirilen parola işlemleri, şirket içi ortamınıza geri yazılır.  

<a name="for-more-answers-to-password-related-questions-see-password-management-frequently-asked-questions"></a>Parola ile ilgili daha fazla soru yanıtı için bkz. [Parola yönetimi ile ilgili sık sorulan sorular](../authentication/active-directory-passwords-faq.md).
---
**S:  Parolamı değiştirirken mevcut Office 365/Azure AD parolamı hatırlayamazsam ne olur?**

**Y:** Bu tür bir durum için birkaç seçenek vardır.  Varsa, self servis parola sıfırlama (SSPR) özelliğini kullanın.  SSPR’nin çalışıp çalışmaması nasıl yapılandırıldığına bağlıdır.  Daha fazla bilgi için bkz. [Parola sıfırlama portalının çalışması](../authentication/howto-sspr-deployment.md).

Office 365 kullanıcıları için yöneticiniz [Kullanıcı parolalarını sıfırlama](https://support.office.com/article/Admins-Reset-user-passwords-7A5D073B-7FAE-4AA5-8F96-9ECD041ABA9C?ui=en-US&rs=en-US&ad=US) bölümünde özetlenen adımları kullanarak parolayı sıfırlayabilir.

Azure AD hesapları için yöneticiler aşağıdakilerden birini kullanarak parolaları sıfırlayabilir:

- [Azure portalda hesapları sıfırlama](active-directory-users-reset-password-azure-portal.md)
- [PowerShell’i kullanma](/powershell/module/msonline/set-msoluserpassword?view=azureadps-1.0)


---
## <a name="security"></a>Güvenlik
**S: Hesaplar belirli sayıda girişim başarısız olduktan sonra kilitleniyor mu, yoksa kullanılan daha karmaşık bir strateji mi var?**

Hesapları kilitlemek için daha karmaşık bir strateji kullanırız.  Bu strateji, isteğin IP adresini ve girilen parolaları temel alır. Kilitleme süresi ayrıca girişimin bir saldırı olma olasılığına göre artar.  

**S: Belirli (yaygın) parolalar 'bu parola birçok kez kullanıldı' iletileriyle reddedilir, bu geçerli etkin dizinde kullanılan parolalara atıfta bulunur mu?**

Bu, "Parola" ve "123456" türevleri gibi genel olarak yaygın olan parolaları ifade eder.

**S: Güvenilmez kaynaklardan (botnet, tor uç noktası) gelen oturum açma istekleri bir B2C kiracısında engellenir mi veya bir Temel ya da Premium sürüm kiracı gerekir mi?**

İstekleri filtreleyen ve botnetlere karşı koruma sağlayıp tüm B2C kiracılarına uygulanan bir ağ geçidine sahibiz.

## <a name="application-access"></a>Uygulama erişimi

**S: Azure AD ile önceden tümleştirilmiş olan uygulamaların ve özelliklerinin listesini nereden bulabilirim?**

**Y:** Azure AD, Microsoft'a, uygulama hizmeti sağlayıcılarına ve iş ortaklarına ait 2.600'ü aşkın önceden tümleştirilmiş uygulama içerir. Önceden tümleştirilmiş tüm uygulamalar çoklu oturum açmayı (SSO) destekler. SSO, uygulamalarınıza erişmek için kurumsal kimlik bilgilerinizi kullanmanıza olanak tanır. Uygulamalardan bazıları aynı zamanda otomatik hazırlama ve sağlamayı kaldırma özelliklerini destekler.

Önceden tümleştirilmiş uygulamaların tam listesi için bkz. [Active Directory Marketi](https://azure.microsoft.com/marketplace/active-directory/).

---
**S: İhtiyacım olan uygulama Azure AD marketinde yoksa ne olur?**

**Y:** Azure AD Premium ile istediğiniz uygulamayı ekleyip yapılandırabilirsiniz. Uygulamanızın özelliklerine ve tercihlerinize bağlı olarak, SSO'yu ve otomatik sağlamayı yapılandırabilirsiniz.  

Daha fazla bilgi için bkz.

* [Azure Active Directory uygulama galerisinde bulunmayan uygulamalar için çoklu oturum açmayı yapılandırma](../manage-apps/configure-federated-single-sign-on-non-gallery-applications.md)
* [Kullanıcıların ve grupların Azure Active Directory'den uygulamalara otomatik olarak hazırlanmasını etkinleştirmek için SCIM'yi kullanma](../app-provisioning/use-scim-to-provision-users-and-groups.md)

---
**S: Kullanıcılar Azure AD'yi kullanarak uygulamalarda nasıl oturum açar?**

**Y:** Azure AD, kullanıcılara uygulamalarını görüntülemeleri ve bunlara erişmeleri için birçok yol sağlar. Bunlardan bazıları aşağıda verilmiştir:

* Azure AD erişim paneli
* Office 365 uygulama başlatıcı
* Birleştirilmiş uygulamalarda doğrudan oturum açma
* Birleştirilmiş, parola tabanlı veya var olan uygulamalara yönelik ayrıntılı bağlantılar

Daha fazla bilgi [için, uygulamalar için Son kullanıcı deneyimlerine](../manage-apps/end-user-experiences.md)bakın.

---
**S: Azure AD, uygulamalar için kimlik doğrulaması ve çoklu oturum açmaya ne gibi farklı yollarla olanak tanır?**

**Y:** Azure AD; SAML 2.0, OpenID Connect, OAuth 2.0 ve WS-Federasyon gibi birçok standartlaştırılmış kimlik doğrulaması ve yetkilendirme protokolünü destekler. Azure AD aynı zamanda, yalnızca form tabanlı kimlik doğrulamasını destekleyen uygulamalar için parola kasası oluşturma ve otomatik oturum açma işlevlerini de destekler.  

Daha fazla bilgi için bkz.

* [Azure AD için Kimlik Doğrulama Senaryoları](../develop/authentication-scenarios.md)
* [Active Directory kimlik doğrulama protokolleri](https://msdn.microsoft.com/library/azure/dn151124.aspx)
* [Azure AD'deki uygulamalar için tek oturum açma](../manage-apps/what-is-single-sign-on.md)

---
**S: Şirket içinde yürüttüğüm uygulamaları ekleyebilir miyim?**

**Y:** Azure AD Uygulama Ara Sunucusu, size seçtiğiniz şirket içi web uygulamaları için kolay ve güvenli erişim sağlar. Bu uygulamalara, Azure AD'de hizmet olarak yazılım (SaaS) uygulamalarınıza eriştiğiniz gibi erişebilirsiniz. VPN kullanmanız veya ağ altyapınızı değiştirmeniz gerekmez.  

Daha fazla bilgi için bkz. [Şirket içi uygulamalara güvenli uzaktan erişim sağlama](../manage-apps/application-proxy.md).

---
**S: Belirli bir uygulamaya erişen kullanıcılar için çok faktörlü kimlik doğrulamasını nasıl isteyebilirim?**

**A:** Azure AD Koşullu Erişim ile her uygulama için benzersiz bir erişim ilkesi atayabilirsiniz. İlkenizde, çok faktörlü kimlik doğrulamasını her zaman veya kullanıcılar yerel ağa bağlı olmadığında gerekli kılabilirsiniz.  

Daha fazla bilgi için bkz. [Office 365'e ve Azure Active Directory'ye bağlı diğer uygulamalara erişimi güvence altına alma](../conditional-access/overview.md).

---
**S: SaaS uygulamaları için otomatik kullanıcı hazırlama nedir?**

**Y:** Birçok popüler bulut SaaS uygulamasında kullanıcı kimliklerinin oluşturulmasını, bakımını ve kaldırılmasını otomatik hale getirmek için Azure AD kullanın.

Daha fazla bilgi için bkz. [Azure Active Directory ile SaaS uygulamalarına kullanıcı hazırlama ve sağlamayı kaldırma işlemlerini otomatik hale getirme](../app-provisioning/user-provisioning.md).

---
**S:  Azure AD ile güvenli bir LDAP bağlantısı oluşturabilir miyim?**

**Y:** Hayır. Azure AD, Hafif Dizin Erişim Protokolü (LDAP) protokolünü veya Secure LDAP'yi doğrudan desteklemez. Ancak, LDAP bağlantısı elde etmek için Azure Ağını üzerinden düzgün yapılandırılmış ağ güvenlik gruplarıyla Azure AD Etki Alanı Hizmetleri (Azure AD DS) örneğini Azure AD kiracınızda etkinleştirmek mümkündür. Daha fazla bilgi için bkz: [Azure Active Directory Etki Alanı Hizmetleri yönetilen etki alanı için güvenli LDAP Yapılandırma](../../active-directory-domain-services/tutorial-configure-ldaps.md)
