---
title: 'Öğretici: 8x8 ile Azure Active Directory tek oturum açma (SSO) entegrasyonu | Microsoft Dokümanlar'
description: Azure Active Directory ile 8x8 arasında tek oturum açma yı nasıl yapılandırabilirsiniz öğrenin.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: b34a6edf-e745-4aec-b0b2-7337473d64c5
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 02/20/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9c598222978a1c831be6f5e9db9eb87b2d6b6b96
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/24/2020
ms.locfileid: "78968698"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-8x8"></a>Öğretici: 8x8 ile Azure Active Directory tek oturum açma (SSO) entegrasyonu

Bu eğitimde, 8x8'i Azure Etkin Dizini (Azure AD) ile nasıl entegre acağınızı öğreneceksiniz. 8x8'i Azure AD ile tümleştirdiğinizde şunları yapabilirsiniz:

* 8x8 erişimi olan Azure AD'de denetim.
* Kullanıcılarınızın Azure AD hesaplarıyla otomatik olarak 8x8'e kadar oturum açabilmelerini etkinleştirin.
* Hesaplarınızı tek bir merkezi konumda yönetin - Azure portalı.

Azure AD ile SaaS uygulama tümleştirmesi hakkında daha fazla bilgi edinmek için Azure [Active Directory ile uygulama erişimi ve tek oturum açma nedir'e](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)bakın.

## <a name="prerequisites"></a>Ön koşullar

Başlamak için aşağıdaki öğelere ihtiyacınız vardır:

* Azure AD aboneliği. Aboneliğiniz [yoksa, ücretsiz bir hesap](https://azure.microsoft.com/free/)alabilirsiniz.
* 8x8 abonelik.

## <a name="scenario-description"></a>Senaryo açıklaması

Bu eğitimde, Azure AD SSO'su bir test ortamında yapılandırın ve test esiniz.

* 8x8 **SP ve IDP** SSO başlatılan destekler

* 8x8'i yapılandırdıktan sonra, kuruluşunuzun hassas verilerinin gerçek zamanlı olarak sızma ve sızmalarını koruyan oturum denetimini uygulayabilirsiniz. Oturum denetimi Koşullu Erişim'den genişletir. [Microsoft Cloud App Security ile oturum denetimini nasıl uygulayacağınızı öğrenin.](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app)

> [!NOTE]
> Bu uygulamanın tanımlayıcısı sabit bir dize değeridir, bu nedenle yalnızca bir örnek bir kiracıda yapılandırılabilir.

## <a name="adding-8x8-from-the-gallery"></a>Galeriden 8x8 ekleme

8x8'in Azure AD'ye entegrasyonunu yapılandırmak için galeriden yönetilen SaaS uygulamaları listenize 8x8 eklemeniz gerekir.

1. Azure [portalında](https://portal.azure.com) bir iş veya okul hesabını veya kişisel bir Microsoft hesabını kullanarak oturum açın.
1. Sol gezinti bölmesinde **Azure Etkin Dizin** hizmetini seçin.
1. Kurumsal **Uygulamalar'a** gidin ve ardından **Tüm Uygulamaları**seçin.
1. Yeni uygulama eklemek için **Yeni uygulama'yı**seçin.
1. Galeri **bölümünden Ekle** bölümünde arama kutusuna **8x8** yazın.
1. Sonuç panelinden **8x8'i** seçin ve ardından uygulamayı ekleyin. Uygulama kiracınıza eklenirken birkaç saniye bekleyin.

## <a name="configure-and-test-azure-ad-single-sign-on-for-8x8"></a>Azure AD oturum açma yı 8x8 için yapılandırma ve test

Azure AD SSO'nu **B.Simon**adlı bir test kullanıcısı kullanarak 8x8 ile yapılandırın ve test edin. SSO'nun çalışması için, bir Azure AD kullanıcısı ile 8x8'deki ilgili kullanıcı arasında bir bağlantı ilişkisi kurmanız gerekir.

Azure AD SSO'yu 8x8 ile yapılandırmak ve test etmek için aşağıdaki yapı taşlarını tamamlayın:

1. Kullanıcılarınızın bu özelliği kullanmasını sağlamak için **[Azure AD SSO'su yapılandırın.](#configure-azure-ad-sso)**
    1. Azure AD'yi B.Simon ile tek oturum açma test etmek için **[bir Azure AD test kullanıcısı oluşturun.](#create-an-azure-ad-test-user)**
    1. B.Simon'ın Azure AD tek oturum açma kullanmasını sağlamak için **[Azure AD test kullanıcısını atayın.](#assign-the-azure-ad-test-user)**
1. Uygulama tarafındaki tek oturum açma ayarlarını yapılandırmak için **[8x8 SSO'yu yapılandırın.](#configure-8x8-sso)**
    1. **[8x8 test kullanıcısı oluşturun](#create-8x8-test-user)** - 8x8'de B.Simon'ın, kullanıcının Azure AD gösterimine bağlı bir muadili olması için.
1. **[SSO'yu test](#test-sso)** edin - yapılandırmanın çalışıp çalışmadığını doğrulamak için.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO’yu yapılandırma

Azure portalında Azure AD SSO'yu etkinleştirmek için aşağıdaki adımları izleyin.

1. Azure [portalında](https://portal.azure.com/), **8x8** uygulama tümleştirme sayfasında, **Yönet** bölümünü bulun ve **tek oturum açma'yı**seçin.
1. Tek **bir oturum açma yöntemi** seç sayfasında **SAML'yi**seçin.
1. **SAML sayfasıyla tek oturum** açma'da, ayarları ayarlamak için **Temel SAML Yapılandırması** için düzenleme/kalem simgesini tıklatın.

   ![Temel SAML Yapılandırması'nı düzenleme](common/edit-urls.png)

1. Temel **SAML Yapılandırması** bölümünde aşağıdaki adımları gerçekleştirin:

    a. **Tanımlayıcı** metin kutusuna bir URL yazın:`https://sso.8x8.com/saml2`

    b. **Yanıtla URL** metin kutusuna bir URL yazın:`https://sso.8x8.com/saml2`

1. **SAML** Ile Tek Oturum Açma sayfasında, **SAML İmza Sertifikası** bölümünde **Sertifika 'yı (Base64)** bulun ve sertifikayı indirmek ve bilgisayarınıza kaydetmek için **İndir'i** seçin. Sertifikayı daha sonra **8x8 SSO'yu Yapılandırışla** bölümündeki öğreticide kullanacaksınız.

    ![Sertifika indirme bağlantısı](common/certificatebase64.png)

1. **8x8'i Ayarla** bölümünde, URL(ler)'i kopyalayın ve bu URL değerlerini daha sonra öğreticide kullanın.

    ![Yapılandırma URL'lerini kopyalama](./media/8x8virtualoffice-tutorial/copy-configuration-urls.png)

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

Bu bölümde, B.Simon'ın 8x8'e erişim sağlayarak Azure tek oturum açma'yı kullanmasını sağlayacaksınız.

1. Azure portalında **Kurumsal Uygulamalar'ı**seçin ve ardından **Tüm Uygulamaları**seçin.
1. Uygulamalar listesinde **8x8'i**seçin.
1. Uygulamanın genel bakış sayfasında, **Yönet** bölümünü bulun ve **Kullanıcıları ve grupları**seçin.

   !["Kullanıcılar ve gruplar" bağlantısı](common/users-groups-blade.png)

1. **Kullanıcı Ekle'yi**seçin, ardından **Atama Ekle** iletişim kutusunda Kullanıcılar ve **gruplar** seçin.

    ![Kullanıcı Ekle bağlantısı](common/add-assign-user.png)

1. Kullanıcılar **ve gruplar** iletişim kutusunda, Kullanıcılar listesinden **B.Simon'ı** seçin ve ardından ekranın altındaki **Seç** düğmesini tıklatın.
1. Atama **Ekle** iletişim kutusunda, **Ata ekle** düğmesini tıklatın.

## <a name="configure-8x8-sso"></a>8x8 SSO yapılandırın

Öğreticinin bir sonraki bölümü 8x8 ile ne tür bir abonelik var bağlıdır.

* Yönetim için Configuration Manager kullanan 8x8 Sürümleri ve X Serisi müşterileri için, [8x8 Configuration Manager'ı Yapılandırma](#configure-8x8-configuration-manager)bölümüne bakın.
* Yönetim için Hesap Yöneticisi'ni kullanan Sanal Ofis müşterileri [için, 8x8 Hesap Yöneticisini Yapılandırma](#configure-8x8-account-manager)bölümüne bakın.

### <a name="configure-8x8-configuration-manager"></a>8x8 Configuration Manager'ı yapılandır

1. 8x8 Configuration [Manager'a](https://vo-cm.8x8.com/)giriş yapın.

1. Ana sayfadan **Kimlik Yönetimi'ni**tıklatın.

    ![8x8 Konfigürasyon Yöneticisi](./media/8x8virtualoffice-tutorial/configure1.png)

1. **Tek Oturum Açma 'yı (SSO)** denetleyin ve ardından Microsoft Azure **AD'yi**seçin.

    ![8x8 Konfigürasyon Yöneticisi](./media/8x8virtualoffice-tutorial/configure2.png)

1. Azure AD'deki **SAML** ile Tek Oturum Açma'yı ayarla'daki üç URL'yi ve imza sertifikasını 8x8 Configuration Manager'daki **Microsoft Azure AD SAML Ayarları** bölümüne kopyalayın.

    ![8x8 Konfigürasyon Yöneticisi](./media/8x8virtualoffice-tutorial/configure3.png)

    a. **Giriş URL'sini** **IDP Giriş URL'sine**kopyalayın.

    b. **Azure AD Tanımlayıcısını** **IDP Veren URL/URN'a**kopyalayın.

    c. **Giriş URL'sini** **IDP Giriş URL'sine**kopyalayın.

    d. Sertifikayı İndir **(Base64)** ve **Sertifikaya**yükleyin.

    e. **Kaydet**'e tıklayın.

### <a name="configure-8x8-account-manager"></a>8x8 Hesap Yöneticisini Yapılandırma

1. Yönetici olarak 8x8 Sanal Office kiracınıza oturum açın.

1. Uygulama Panelinde **Sanal Ofis Hesabı Mgr'i** seçin.

    ![Uygulama Tarafında Yapılandır](./media/8x8virtualoffice-tutorial/tutorial_8x8virtualoffice_001.png)

1. Yönetmek için **İşletme** hesabını seçin ve **Oturum Aç** düğmesini tıklatın.

    ![Uygulama Tarafında Yapılandır](./media/8x8virtualoffice-tutorial/tutorial_8x8virtualoffice_002.png)

1. Menü **listesindeKI HESAPLAR** sekmesini tıklatın.

    ![Uygulama Tarafında Yapılandır](./media/8x8virtualoffice-tutorial/tutorial_8x8virtualoffice_003.png)

1. Hesaplar listesinde **Tek Oturum Aç'ı** tıklatın.

    ![Uygulama Tarafında Yapılandır](./media/8x8virtualoffice-tutorial/tutorial_8x8virtualoffice_004.png)

1. Kimlik Doğrulama yöntemleri altında **Tek Oturum Açma'yı** seçin ve **SAML'yi**tıklatın.

    ![Uygulama Tarafında Yapılandır](./media/8x8virtualoffice-tutorial/tutorial_8x8virtualoffice_005.png)

1. **Saml Tek İşaret bölümünde** aşağıdaki adımları gerçekleştirin:

    ![Uygulama Tarafında Yapılandır](./media/8x8virtualoffice-tutorial/tutorial_8x8virtualoffice_006.png)

    a. Oturum **Aç URL** metin kutusuna, Azure portalından kopyaladığınız **Giriş URL** değerini yapıştırın.

    b. Oturumu **Çıkış URL** metin kutusuna, Azure portalından kopyaladığınız **Logout URL** değerini yapıştırın.

    c. Veren **URL** metin kutusuna, Azure portalından kopyaladığınız **Azure AD Tanımlayıcı** değerini yapıştırın.

    d. Azure portalından indirdiğiniz sertifikayı yüklemek için **Gözat** düğmesini tıklatın.

    e. **Kaydet** düğmesine tıklayın.

### <a name="create-8x8-test-user"></a>8x8 test kullanıcısı oluşturma

Bu bölümde, 8x8 britta Simon adlı bir kullanıcı oluşturun. Kullanıcıları 8x8 platformuna eklemek için [8x8 destek ekibiyle](https://www.8x8.com/about-us/contact-us) çalışın. Tek oturum açmadan önce kullanıcılar oluşturulmalı ve etkinleştirilmelidir.

## <a name="test-sso"></a>Test SSO

Bu bölümde, Access Panelini kullanarak Azure AD tek oturum açma yapılandırmanızı sınarsınız.

Access Paneli'ndeki 8x8 döşemesini tıklattığınızda, SSO'yu kurduğunuz 8x8'de otomatik olarak oturum açmalısınız. Erişim Paneli hakkında daha fazla bilgi için [Erişim Paneline Giriş'e](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)bakın.

## <a name="additional-resources"></a>Ek kaynaklar

- [SaaS Uygulamalarının Azure Etkin Dizini ile Nasıl Entegre Edilen Öğreticiler Listesi](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory ile uygulama erişimi ve tek oturum açma nedir?](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)

- [Azure Active Directory'de koşullu erişim nedir?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Azure AD ile 8x8'i deneyin](https://aad.portal.azure.com/)

- [Microsoft Cloud App Security'de oturum denetimi nedir?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [Gelişmiş görünürlük ve kontrollerle 8x8 nasıl korunur?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)