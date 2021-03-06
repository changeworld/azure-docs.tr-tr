---
title: 'Öğretici: Ethidex Uyumluluk Ofisi için MS Azure SSO Access ile Azure Active Directory tek oturum açma (SSO) entegrasyonu™ | Microsoft Dokümanlar'
description: Ethidex Uyumluluk Ofisi için Azure Active Directory ve MS Azure SSO Access arasında tek oturum açma ™ nasıl yapılandırılamayın.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 58b2e70f-d1dd-47b6-b91f-f77581df01c6
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 09/06/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 00cbb5ff4b9354d0d1702161460b2646669f34c2
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/24/2020
ms.locfileid: "70844376"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-ms-azure-sso-access-for-ethidex-compliance-office"></a>Öğretici: Ethidex Uyumluluk Ofisi için MS Azure SSO Access ile Azure Active Directory tek oturum açma (SSO) entegrasyonu™

Bu eğitimde, Ethidex Uyumluluk Ofisi için MS Azure SSO Access™ Azure Etkin Dizin (Azure AD) ile nasıl entegre edeceğinizi öğreneceksiniz. Ethidex Uyumluluk Ofisi için MS Azure SSO Access™ Azure AD ile tümleştirdiğinizde şunları yapabilirsiniz:

* Ethidex Uyumluluk Ofisi için MS Azure SSO Erişimi'ne erişimi olan Azure AD'de denetim™.
* Kullanıcılarınızın, Azure AD hesaplarıyla ™ Ethidex Uyumluluk Ofisi için MS Azure SSO Access'te otomatik olarak oturum açmasını etkinleştirin.
* Hesaplarınızı tek bir merkezi konumda yönetin - Azure portalı.

Azure AD ile SaaS uygulama tümleştirmesi hakkında daha fazla bilgi edinmek için Azure [Active Directory ile uygulama erişimi ve tek oturum açma nedir'e](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)bakın.

## <a name="prerequisites"></a>Ön koşullar

Başlamak için aşağıdaki öğelere ihtiyacınız vardır:

* Azure AD aboneliği. Aboneliğiniz [yoksa, ücretsiz bir hesap](https://azure.microsoft.com/free/)alabilirsiniz.
* Ethidex Uyumluluk Ofisi için MS Azure SSO Access™ tek oturum açma (SSO) aboneliği ni etkinleştirdi.

## <a name="scenario-description"></a>Senaryo açıklaması

Bu eğitimde, Azure AD SSO'su bir test ortamında yapılandırın ve test esiniz.

* Ethidex Uyumluluk Ofisi için MS Azure SSO Access™ **IDP** tarafından başlatılan SSO'ya destek veriyor

## <a name="adding-ms-azure-sso-access-for-ethidex-compliance-office-from-the-gallery"></a>Galeriden Ethidex Uyumluluk Ofisi için MS Azure SSO Erişimi™ ekleme

Ethidex Uyumluluk Ofisi için MS Azure SSO Access'in entegrasyonunu ™ Azure AD'ye yapılandırmak için, galeriden ethidex Uyumluluk Ofisi için MS Azure SSO Erişimi™ yönetilen SaaS uygulamaları listenize eklemeniz gerekir.

1. Azure [portalında](https://portal.azure.com) bir iş veya okul hesabını veya kişisel bir Microsoft hesabını kullanarak oturum açın.
1. Sol gezinti bölmesinde **Azure Etkin Dizin** hizmetini seçin.
1. Kurumsal **Uygulamalar'a** gidin ve ardından **Tüm Uygulamaları**seçin.
1. Yeni uygulama eklemek için **Yeni uygulama'yı**seçin.
1. **Galeriden Ekle** bölümünde, arama kutusuna **ethidex Uyumluluk Ofisi için MS Azure SSO Erişimi™** yazın.
1. **Ethidex Uyumluluk Ofisi için MS Azure SSO Access™** sonuç panelinden seçin ve ardından uygulamayı ekleyin. Uygulama kiracınıza eklenirken birkaç saniye bekleyin.

## <a name="configure-and-test-azure-ad-single-sign-on-for-ms-azure-sso-access-for-ethidex-compliance-office"></a>Ethidex Uyumluluk Ofisi için MS Azure SSO Access için Azure AD oturumunu yapılandırın ve test edin™

Azure AD SSO'nu, **B.Simon**adlı bir test kullanıcısı kullanarak™ Ethidex Uyumluluk Ofisi için MS Azure SSO Access ile yapılandırın ve test edin. SSO'nun çalışması için, Bir Azure REKLAM kullanıcısı ile ilgili kullanıcı arasında Ethidex Uyumluluk Ofisi™ için MS Azure SSO Access'te bir bağlantı ilişkisi kurmanız gerekir.

Azure AD SSO'yu, Ethidex Uyumluluk Ofisi için MS Azure SSO Access ile yapılandırmak ve test etmek için™ aşağıdaki yapı taşlarını tamamlayın:

1. Kullanıcılarınızın bu özelliği kullanmasını sağlamak için **[Azure AD SSO'su yapılandırın.](#configure-azure-ad-sso)**
    1. Azure AD'yi B.Simon ile tek oturum açma test etmek için **[bir Azure AD test kullanıcısı oluşturun.](#create-an-azure-ad-test-user)**
    1. B.Simon'ın Azure AD tek oturum açma kullanmasını sağlamak için **[Azure AD test kullanıcısını atayın.](#assign-the-azure-ad-test-user)**
1. Uygulama tarafındaki tek oturum açma ayarlarını yapılandırmak **[için Ethidex Uyumluluk Office SSO için MS Azure SSO Access'i yapılandırın.](#configure-ms-azure-sso-access-for-ethidex-compliance-office-sso)**
    1. **[Ethidex Uyumluluk Ofisi test kullanıcısı için MS Azure SSO Access'i oluşturun](#create-ms-azure-sso-access-for-ethidex-compliance-office-test-user)** - Kullanıcının Azure REKLAM gösterimine bağlı olan™ Ethidex Uyumluluk Ofisi için MS Azure SSO Access'te B.Simon'ın bir örneğine sahip olmak için.
1. **[SSO'yu test](#test-sso)** edin - yapılandırmanın çalışıp çalışmadığını doğrulamak için.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO’yu yapılandırma

Azure portalında Azure AD SSO'yu etkinleştirmek için aşağıdaki adımları izleyin.

1. **Ethidex Uyumluluk Ofisi ™** için MS Azure SSO Access'teki [Azure portalında,](https://portal.azure.com/) **Yönet** bölümünü bulun ve tek **oturum açma'yı**seçin.
1. Tek **bir oturum açma yöntemi** seç sayfasında **SAML'yi**seçin.
1. **SAML sayfasıyla tek oturum** açma'da, ayarları ayarlamak için **Temel SAML Yapılandırması** için düzenleme/kalem simgesini tıklatın.

   ![Temel SAML Yapılandırması'nı düzenleme](common/edit-urls.png)

1. Temel **SAML Yapılandırması** bölümünde, aşağıdaki alanların değerlerini girin:

    a. **Tanımlayıcı** metin kutusuna, aşağıdaki deseni kullanarak bir URL yazın:`com.ethidex.prod.<CLIENTID>`

    b. **Yanıtla URL** metin kutusuna, aşağıdaki deseni kullanarak bir URL yazın:`https://www.ethidex.com/saml2/sp/acs/<CLIENTID>`

    > [!NOTE]
    > Bu değerler gerçek değildir. Bu değerleri gerçek Tanımlayıcı ve YanıtURL'i ile güncelleştirin. Bu değerleri almak [için Ethidex Uyumluluk Ofisi™ destek ekibine](mailto:support@ethidex.com) başvurun. Azure portalındaki **Temel SAML Yapılandırması** bölümünde gösterilen desenlere de bakabilirsiniz.

1. Ethidex Uyumluluk Ofisi için MS Azure SSO Access™ uygulama uygulaması, SAML belirteç öznitelikleri yapılandırmanıza özel öznitelik eşlemeleri eklemenizi gerektiren belirli bir biçimde SAML iddiaları bekler. Aşağıdaki ekran görüntüsü varsayılan özniteliklerin listesini gösterir, **nameidentifier** **user.userprincipalname**ile eşlenir gibi. Ethidex Uyumluluk Ofisi için MS Azure SSO Access™ uygulaması **nameidentifier** **user.mail**ile eşlenir bekliyor, bu yüzden **düzenleme** simgesine tıklayarak öznitelik eşleme düzenlemeniz ve öznitelik eşlemi değiştirmeniz gerekir.

    ![image](common/edit-attribute.png)

1. **SAML** Ile Tek Oturum Açma sayfasında, **SAML İmza Sertifikası** bölümünde **Sertifika (Ham)** bulun ve sertifikayı indirmek ve bilgisayarınıza kaydetmek için **İndir'i** seçin.

    ![Sertifika indirme bağlantısı](common/certificateraw.png)

1. **Ethidex Uyumluluk Ofisi için MS Azure SSO Erişimi™** bölümünde, gereksiniminize göre uygun URL(ler)'i kopyalayın.

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

Bu bölümde, B.Simon'ın Ethidex Uyumluluk Ofisi için MS Azure SSO Access'e erişim sağlayarak Azure tek oturum açma'yı kullanmasını sağlayacaksınız™.

1. Azure portalında **Kurumsal Uygulamalar'ı**seçin ve ardından **Tüm Uygulamaları**seçin.
1. Uygulamalar listesinde, **Ethidex Uyumluluk Ofisi için MS Azure SSO Access'i seçin™.**
1. Uygulamanın genel bakış sayfasında, **Yönet** bölümünü bulun ve **Kullanıcıları ve grupları**seçin.

   !["Kullanıcılar ve gruplar" bağlantısı](common/users-groups-blade.png)

1. **Kullanıcı Ekle'yi**seçin, ardından **Atama Ekle** iletişim kutusunda Kullanıcılar ve **gruplar** seçin.

    ![Kullanıcı Ekle bağlantısı](common/add-assign-user.png)

1. Kullanıcılar **ve gruplar** iletişim kutusunda, Kullanıcılar listesinden **B.Simon'ı** seçin ve ardından ekranın altındaki **Seç** düğmesini tıklatın.
1. SAML iddiasında herhangi bir rol değeri bekliyorsanız, **Rolü Seç** iletişim kutusunda, listeden kullanıcı için uygun rolü seçin ve ardından ekranın altındaki **Seç** düğmesini tıklatın.
1. Atama **Ekle** iletişim kutusunda, **Ata ekle** düğmesini tıklatın.

## <a name="configure-ms-azure-sso-access-for-ethidex-compliance-office-sso"></a>Ethidex Uyumluluk Ofisi SSO için MS Azure SSO Erişimini Yapılandırın

**Ethidex Uyumluluk Ofisi için MS Azure SSO Access™** tarafında tek oturum açma yapılandırmak için, indirilen **Sertifikayı (Raw)** ve uygun kopyalanmış URL'leri Azure portalından [Ms Azure SSO Access for Ethidex Compliance Office™ destek ekibine](mailto:support@ethidex.com)göndermeniz gerekir. Bu ayarı, SAML SSO bağlantısının her iki tarafta da düzgün bir şekilde ayarlanması için ayarlarlar.

### <a name="create-ms-azure-sso-access-for-ethidex-compliance-office-test-user"></a>Ethidex Uyumluluk Ofisi test kullanıcısı için MS Azure SSO Erişimi oluşturma

Bu bölümde, MS Azure SSO Access for Ethidex Uyumluluk Ofisi™'nde B.Simon adında bir kullanıcı oluşturursunuz. Kullanıcıları [Ethidex Uyumluluk Ofisi için MS Azure SSO Access ™ destek ekibiyle](mailto:support@ethidex.com) çalışarak kullanıcıları Ethidex Uyumluluk Ofisi için MS Azure SSO Access™ platformuna ekleyin. Tek oturum açmadan önce kullanıcılar oluşturulmalı ve etkinleştirilmelidir.

## <a name="test-sso"></a>Test SSO 

Bu bölümde, Access Panelini kullanarak Azure AD tek oturum açma yapılandırmanızı sınarsınız.

Ethidex Uyumluluk Ofisi için MS Azure SSO Erişimi™ Access Paneli'ndeki döşemeyi tıklattığınızda, SSO'yu kurduğunuz ™ Ethidex Uyumluluk Ofisi için MS Azure SSO Access'te otomatik olarak oturum açmış olmalısınız. Erişim Paneli hakkında daha fazla bilgi için [Erişim Paneline Giriş'e](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)bakın.

## <a name="additional-resources"></a>Ek kaynaklar

- [SaaS Uygulamalarının Azure Etkin Dizini ile Nasıl Entegre Edilen Öğreticiler Listesi](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory ile uygulama erişimi ve tek oturum açma nedir?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Azure Active Directory'de koşullu erişim nedir?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Azure AD ile ethidex Uyumluluk Ofisi için MS Azure SSO Access™ deneyin](https://aad.portal.azure.com/)

