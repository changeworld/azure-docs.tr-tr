---
title: 'Öğretici: Versal ile Azure Active Directory tek oturum açma (SSO) entegrasyonu | Microsoft Dokümanlar'
description: Azure Active Directory ve Versal arasında tek oturum açma yı nasıl yapılandırıştırmayı öğrenin.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 5b2e53c0-61a3-4954-ae46-8c28c6368bfd
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/10/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: e5e5a3f8070829916b228e9bc68e37156ddf5f6a
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/24/2020
ms.locfileid: "75561794"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-versal"></a>Öğretici: Versal ile Azure Active Directory tek oturum açma (SSO) entegrasyonu

Bu eğitimde, Versal'ı Azure Etkin Dizini (Azure AD) ile nasıl entegre acağınızı öğreneceksiniz. Versal'ı Azure AD ile tümleştirdiğinizde şunları yapabilirsiniz:

* Versal erişimi olan Azure AD'de denetim.
* Kullanıcılarınızın Azure REKLAM hesaplarıyla Versal'da otomatik olarak oturum açmasını etkinleştirin.
* Hesaplarınızı tek bir merkezi konumda yönetin - Azure portalı.

Azure AD ile SaaS uygulama tümleştirmesi hakkında daha fazla bilgi edinmek için Azure [Active Directory ile uygulama erişimi ve tek oturum açma nedir'e](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)bakın.

## <a name="prerequisites"></a>Ön koşullar

Başlamak için aşağıdaki öğelere ihtiyacınız vardır:

* Azure AD aboneliği. Aboneliğiniz [yoksa, ücretsiz bir hesap](https://azure.microsoft.com/free/)alabilirsiniz.
* Versal tek oturum açma (SSO) aboneliği sağladı.

## <a name="scenario-description"></a>Senaryo açıklaması

Bu eğitimde, Azure AD SSO'su bir test ortamında yapılandırın ve test esiniz.


* Versal **IDP'nin** başlattığı SSO'ya destek verdi

> [!NOTE]
> Bu uygulamanın tanımlayıcısı sabit bir dize değeridir, bu nedenle yalnızca bir örnek bir kiracıda yapılandırılabilir.

## <a name="adding-versal-from-the-gallery"></a>Galeriden Versal ekleme

Versal'ın Azure AD'ye entegrasyonunu yapılandırmak için Galeriden Versal'ı yönetilen SaaS uygulamaları listenize eklemeniz gerekir.

1. Azure [portalında](https://portal.azure.com) bir iş veya okul hesabını veya kişisel bir Microsoft hesabını kullanarak oturum açın.
1. Sol gezinti bölmesinde **Azure Etkin Dizin** hizmetini seçin.
1. Kurumsal **Uygulamalar'a** gidin ve ardından **Tüm Uygulamaları**seçin.
1. Yeni uygulama eklemek için **Yeni uygulama'yı**seçin.
1. **Galeribölümünden Ekle** bölümünde, arama kutusuna **Versal** yazın.
1. Sonuçlar panelinden **Versal'ı** seçin ve ardından uygulamayı ekleyin. Uygulama kiracınıza eklenirken birkaç saniye bekleyin.


## <a name="configure-and-test-azure-ad-single-sign-on-for-versal"></a>Versal için Azure AD oturum açma işlemlerini yapılandırma ve test edin

Azure AD SSO'nu **B.Simon**adlı bir test kullanıcısı kullanarak Versal ile yapılandırma ve test edin. SSO'nun çalışması için, Bir Azure REKLAM kullanıcısı ile Versal'daki ilgili kullanıcı arasında bir bağlantı ilişkisi kurmanız gerekir.

Azure AD SSO'yu Versal ile yapılandırmak ve test etmek için aşağıdaki yapı taşlarını tamamlayın:

1. Kullanıcılarınızın bu özelliği kullanmasını sağlamak için **[Azure AD SSO'su yapılandırın.](#configure-azure-ad-sso)**
    1. Azure AD'yi B.Simon ile tek oturum açma test etmek için **[bir Azure AD test kullanıcısı oluşturun.](#create-an-azure-ad-test-user)**
    1. B.Simon'ın Azure AD tek oturum açma kullanmasını sağlamak için **[Azure AD test kullanıcısını atayın.](#assign-the-azure-ad-test-user)**
1. **[Versal SSO'yu yapılandırır](#configure-versal-sso)** - uygulama tarafındaki tek oturum açma ayarlarını yapılandırmak için.
    1. **[Versal test kullanıcıoluşturun](#create-versal-test-user)** - Kullanıcının Azure AD gösterimi ile bağlantılı Versal B.Simon bir meslektaşı olması.
1. **[SSO'yu test](#test-sso)** edin - yapılandırmanın çalışıp çalışmadığını doğrulamak için.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO’yu yapılandırma

Azure portalında Azure AD SSO'yu etkinleştirmek için aşağıdaki adımları izleyin.

1. **Versal** uygulama tümleştirme sayfasındaki [Azure portalında](https://portal.azure.com/) **Yönet** bölümünü bulun ve **tek oturum açma'yı**seçin.
1. Tek **bir oturum açma yöntemi** seç sayfasında **SAML'yi**seçin.
1. **SAML sayfasıyla tek oturum** açma'da, ayarları ayarlamak için **Temel SAML Yapılandırması** için düzenleme/kalem simgesini tıklatın.

   ![Temel SAML Yapılandırması'nı düzenleme](common/edit-urls.png)

1. **SAML sayfasıyla tek oturum açma'da** aşağıdaki alanların değerlerini girin:

    a. **Tanımlayıcı** metin kutusuna bir URL yazın:`VERSAL`

    b. **Yanıtla URL** metin kutusuna, aşağıdaki deseni kullanarak bir URL yazın:`https://versal.com/sso/saml/orgs/<organization_id>`

    > [!NOTE]
    > YanıtURL değeri gerçek değil. Bu değeri gerçek Yanıt URL'si ile güncelleştirin. Bu değerleri almak için [Versal İstemci destek ekibine](https://support.versal.com/hc/) başvurun. Azure portalındaki **Temel SAML Yapılandırması** bölümünde gösterilen desenlere de bakabilirsiniz.

1. Versal uygulama, SAML belirteç öznitelikleri yapılandırmanıza özel öznitelik eşlemeleri eklemenizi gerektiren belirli bir biçimde SAML iddialarını bekler. Aşağıdaki ekran görüntüsü varsayılan özniteliklerin listesini gösterir, **nameidentifier** **user.userprincipalname**ile eşlenir gibi. Versal uygulama **nameidentifier** **user.mail**ile eşlenen bekliyor, bu yüzden **düzenleme** simgesine tıklayarak öznitelik eşleme düzenlemeniz ve öznitelik eşleme değiştirmek gerekir.

    ![image](common/edit-attribute.png)

1. **SAML İmza Sertifikası** bölümünde **SAML ile tek oturum açma'da** **Federation Metadata XML'i** bulun ve sertifikayı indirmek ve bilgisayarınıza kaydetmek için **İndir'i** seçin.

    ![Sertifika indirme bağlantısı](common/metadataxml.png)

1. **Versal'ı Ayarla** bölümünde, gereksiniminize göre uygun URL'yi kopyalayın.

    ![Yapılandırma URL'lerini kopyalama](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Azure AD test kullanıcısı oluşturma

Bu bölümde, Azure portalında B.Simon adında bir test kullanıcısı oluşturursunuz.

1. Azure portalındaki sol bölmeden **Azure Etkin Dizini'ni**seçin, **Kullanıcılar'ı**seçin ve ardından **Tüm Kullanıcıları**seçin.
1. Ekranın üst kısmında **Yeni kullanıcı** yı seçin.
1. **Kullanıcı** özelliklerinde aşağıdaki adımları izleyin:
   1. **Ad** alanına `B.Simon` girin.  
   1. Kullanıcı **adı** alanına. username@companydomain.extension Örneğin, `B.Simon@contoso.com`.
   1. **Parolayı Göster** onay kutusunu seçin ve ardından **Parola** kutusunda görüntülenen değeri yazın.
   1. **Oluştur'u**tıklatın.

### <a name="assign-the-azure-ad-test-user"></a>Azure AD test kullanıcısını atama

Bu bölümde, B.Simon'ın Versal'a erişim izni vererek Azure tek oturum açma'yı kullanmasını sağlayacaksınız.

1. Azure portalında **Kurumsal Uygulamalar'ı**seçin ve ardından **Tüm Uygulamaları**seçin.
1. Uygulamalar listesinde **Versal'ı**seçin.
1. Uygulamanın genel bakış sayfasında, **Yönet** bölümünü bulun ve **Kullanıcıları ve grupları**seçin.

   !["Kullanıcılar ve gruplar" bağlantısı](common/users-groups-blade.png)

1. **Kullanıcı Ekle'yi**seçin, ardından **Atama Ekle** iletişim kutusunda Kullanıcılar ve **gruplar** seçin.

    ![Kullanıcı Ekle bağlantısı](common/add-assign-user.png)

1. Kullanıcılar **ve gruplar** iletişim kutusunda, Kullanıcılar listesinden **B.Simon'ı** seçin ve ardından ekranın altındaki **Seç** düğmesini tıklatın.
1. SAML iddiasında herhangi bir rol değeri bekliyorsanız, **Rolü Seç** iletişim kutusunda, listeden kullanıcı için uygun rolü seçin ve ardından ekranın altındaki **Seç** düğmesini tıklatın.
1. Atama **Ekle** iletişim kutusunda, **Ata ekle** düğmesini tıklatın.

## <a name="configure-versal-sso"></a>Versal SSO'nun yapılandırılması

**Versal** tarafında tek oturum açma yapılandırmak için, indirilen **Federasyon Metadata XML'i** ve azure portalından uygun kopyalanmış URL'leri [Versal destek ekibine](https://support.versal.com/hc/)göndermeniz gerekir. Bu ayarı, SAML SSO bağlantısının her iki tarafta da düzgün bir şekilde ayarlanması için ayarlarlar.

### <a name="create-versal-test-user"></a>Versal test kullanıcısı oluşturma

Bu bölümde, Versal'da B.Simon adında bir kullanıcı oluşturursunuz. Kuruluşunuzdaki kullanıcı B.Simon'ı oluşturmak için [SAML test kullanıcı](https://support.versal.com/hc/articles/115011672887-Creating-a-SAML-test-user) destek kılavuzunu izleyin. Tek oturum açmadan önce kullanıcıların Versal'da oluşturulması ve etkinleştirilmesi gerekir. 

## <a name="test-sso"></a>Test SSO 

Bu bölümde, web sitenize gömülü bir Versal kursu kullanarak Azure AD tek oturum açma yapılandırmanızı test edersiniz.
Azure AD tek oturum açma desteğiyle bir Versal kursun nasıl yerleştirilebildiğini anlatan talimatlar için [lütfen Katıştırma Organizasyonel Kursları](https://support.versal.com/hc/articles/203271866-Embedding-organizational-courses) **SAML Tek Oturum** Açma destek kılavuzuna bakın. 

Kurs katıştırma test etmek için bir kurs oluşturmanız, kuruluşunuzla paylaşmanız ve yayınlamanız gerekir. Daha fazla bilgi için lütfen [kurs oluşturma,](https://support.versal.com/hc/articles/203722528-Create-a-course) [kurs yayınlama](https://support.versal.com/hc/articles/203753398-Publishing-a-course)ve kurs ve [öğrenci yönetimi](https://support.versal.com/hc/articles/206029467-Course-and-learner-management) ne kadar bilgi edinin.

## <a name="additional-resources"></a>Ek kaynaklar

- [SaaS Uygulamalarının Azure Etkin Dizini ile Nasıl Entegre Edilen Öğreticiler Listesi](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory ile uygulama erişimi ve tek oturum açma nedir?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Azure Active Directory'de koşullu erişim nedir?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Azure AD ile Versal'ı deneyin](https://aad.portal.azure.com/)

