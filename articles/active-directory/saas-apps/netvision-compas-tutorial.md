---
title: 'Öğretici: Netvision Compas ile Azure Active Directory tek oturum açma (SSO) entegrasyonu | Microsoft Dokümanlar'
description: Azure Active Directory ve Netvision Compas arasında tek oturum açma yı nasıl yapılandırabilirsiniz öğrenin.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: a56f44c5-dc08-4c7c-ad20-b6e7127deb2c
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 03/16/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: c3015ea26d81505c4f058846dbcb3b7858f79267
ms.sourcegitcommit: b0ff9c9d760a0426fd1226b909ab943e13ade330
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/01/2020
ms.locfileid: "80520186"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-netvision-compas"></a>Öğretici: Netvision Compas ile Azure Active Directory tek oturum açma (SSO) entegrasyonu

Bu eğitimde, Netvision Compas'ı Azure Etkin Dizini (Azure AD) ile nasıl entegre acağınızı öğreneceksiniz. Netvision Compas'ı Azure AD ile tümleştirdiğinizde şunları yapabilirsiniz:

* Netvision Compas erişimi olan Azure AD'de denetim.
* Kullanıcılarınızın Azure REKLAM hesaplarıyla Netvision Compas'ta otomatik olarak oturum açabilmelerini etkinleştirin.
* Hesaplarınızı tek bir merkezi konumda yönetin - Azure portalı.

Azure AD ile SaaS uygulama tümleştirmesi hakkında daha fazla bilgi edinmek için Azure [Active Directory ile uygulama erişimi ve tek oturum açma nedir'e](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)bakın.

## <a name="prerequisites"></a>Ön koşullar

Başlamak için aşağıdaki öğelere ihtiyacınız vardır:

* Azure AD aboneliği. Aboneliğiniz [yoksa, ücretsiz bir hesap](https://azure.microsoft.com/free/)alabilirsiniz.
* Netvision Compas tek oturum açma (SSO) aboneliğini etkinleştirildi.

## <a name="scenario-description"></a>Senaryo açıklaması

Bu eğitimde, Azure AD SSO'su bir test ortamında yapılandırın ve test esiniz.

* Netvision Compas **SP ve IDP** sso başlatılan destekler
* Netvision Compas'ı yapılandırdıktan sonra, kuruluşunuzun hassas verilerinin gerçek zamanlı olarak sızma ve sızmalarını koruyan Oturum Denetimini uygulayabilirsiniz. Oturum Denetimi Koşullu Erişim'den genişletir. [Microsoft Cloud App Security ile oturum denetimini nasıl uygulayacağınızı öğrenin](https://docs.microsoft.com/cloud-app-security/proxy-deployment-aad)


## <a name="adding-netvision-compas-from-the-gallery"></a>Galeriden Netvision Compas ekleme

Netvision Compas'ın Azure AD'ye entegrasyonunu yapılandırmak için, galeriden Yönetilen SaaS uygulamaları listenize Netvision Compas eklemeniz gerekir.

1. Azure [portalında](https://portal.azure.com) bir iş veya okul hesabını veya kişisel bir Microsoft hesabını kullanarak oturum açın.
1. Sol gezinti bölmesinde **Azure Etkin Dizin** hizmetini seçin.
1. Kurumsal **Uygulamalar'a** gidin ve ardından **Tüm Uygulamaları**seçin.
1. Yeni uygulama eklemek için **Yeni uygulama'yı**seçin.
1. Galeri **bölümünden Ekle** bölümünde, arama kutusuna **Netvision Compas** yazın.
1. Sonuç panelinden **Netvision Compas'ı** seçin ve ardından uygulamayı ekleyin. Uygulama kiracınıza eklenirken birkaç saniye bekleyin.


## <a name="configure-and-test-azure-ad-single-sign-on-for-netvision-compas"></a>Netvision Compas için Azure AD oturum açma işlemlerini yapılandırın ve test edin

Azure AD SSO'nu Netvision Compas ile **B.Simon**adlı bir test kullanıcısı kullanarak yapılandırın ve test edin. SSO'nun çalışması için, Bir Azure REKLAM kullanıcısı ile Netvision Compas'taki ilgili kullanıcı arasında bir bağlantı ilişkisi kurmanız gerekir.

Azure AD SSO'yu Netvision Compas ile yapılandırmak ve test etmek için aşağıdaki yapı taşlarını tamamlayın:

1. Kullanıcılarınızın bu özelliği kullanmasını sağlamak için **[Azure AD SSO'su yapılandırın.](#configure-azure-ad-sso)**
    1. Azure AD'yi B.Simon ile tek oturum açma test etmek için **[bir Azure AD test kullanıcısı oluşturun.](#create-an-azure-ad-test-user)**
    1. B.Simon'ın Azure AD tek oturum açma kullanmasını sağlamak için **[Azure AD test kullanıcısını atayın.](#assign-the-azure-ad-test-user)**
1. **[Netvision Compas SSO'yu yapılandırır](#configure-netvision-compas-sso)** - uygulama tarafındaki tek oturum açma ayarlarını yapılandırmak için.
    1. **[Netvision Compas test kullanıcısını yapılandırın](#configure-netvision-compas-test-user)** - Netvision Compas'ta Kullanıcının Azure AD gösterimine bağlı B.Simon'ın bir muadili olması için.
1. **[SSO'yu test](#test-sso)** edin - yapılandırmanın çalışıp çalışmadığını doğrulamak için.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO’yu yapılandırma

Azure portalında Azure AD SSO'yu etkinleştirmek için aşağıdaki adımları izleyin.

1. Azure [portalında,](https://portal.azure.com/) **Netvision Compas** uygulama tümleştirme sayfasında, **Yönet** bölümünü bulun ve **tek oturum açma'yı**seçin.
1. Tek **bir oturum açma yöntemi** seç sayfasında **SAML'yi**seçin.
1. **SAML sayfasıyla tek oturum** açma'da, ayarları ayarlamak için **Temel SAML Yapılandırması** için düzenleme/kalem simgesini tıklatın.

   ![Temel SAML Yapılandırması'nı düzenleme](common/edit-urls.png)

1. Temel **SAML Yapılandırma** sı bölümünde, uygulamayı **IDP** tarafından başlatılan modda yapılandırmak istiyorsanız, aşağıdaki alanların değerlerini girin:

    a. **Tanımlayıcı** metin kutusuna, aşağıdaki deseni kullanarak bir URL yazın:`https://<TENANT>.compas.cloud/Identity/Saml20`

    b. **Yanıtla URL** metin kutusuna, aşağıdaki deseni kullanarak bir URL yazın:`https://<TENANT>.compas.cloud/Identity/Auth/AssertionConsumerService`

1. Uygulamayı **SP** başlatılan modda yapılandırmak istiyorsanız **ek URL'ler ayarla'yı** tıklatın ve aşağıdaki adımı gerçekleştirin:

    Oturum **Açma URL** metin kutusuna aşağıdaki deseni kullanarak bir URL yazın:`https://<TENANT>.compas.cloud/Identity/Auth/AssertionConsumerService`

    > [!NOTE]
    > Bu değerler gerçek değildir. Bu değerleri gerçek Tanımlayıcı, YanıtLA URL'si ve Oturum Açma URL'si ile güncelleştirin. Bu değerleri almak için [Netvision Compas Müşteri destek ekibine](mailto:contact@net.vision) başvurun. Azure portalındaki **Temel SAML Yapılandırması** bölümünde gösterilen desenlere de bakabilirsiniz.

1. **SAML** Ile Tek Oturum Açma sayfasında, **SAML İmza Sertifikası** bölümünde, **Federasyon Metadata XML'i** bulun ve meta veri dosyasını indirmek ve bilgisayarınıza kaydetmek için **İndir'i** seçin.

    ![Sertifika indirme bağlantısı](common/metadataxml.png)



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

Bu bölümde, Netvision Compas'a erişim sağlayarak B.Simon'Un Azure tek oturum açma'yı kullanmasını sağlayacaksınız.

1. Azure portalında **Kurumsal Uygulamalar'ı**seçin ve ardından **Tüm Uygulamaları**seçin.
1. Uygulamalar listesinde **Netvision Compas'ı**seçin.
1. Uygulamanın genel bakış sayfasında, **Yönet** bölümünü bulun ve **Kullanıcıları ve grupları**seçin.

   !["Kullanıcılar ve gruplar" bağlantısı](common/users-groups-blade.png)

1. **Kullanıcı Ekle'yi**seçin, ardından **Atama Ekle** iletişim kutusunda Kullanıcılar ve **gruplar** seçin.

    ![Kullanıcı Ekle bağlantısı](common/add-assign-user.png)

1. Kullanıcılar **ve gruplar** iletişim kutusunda, Kullanıcılar listesinden **B.Simon'ı** seçin ve ardından ekranın altındaki **Seç** düğmesini tıklatın.
1. SAML iddiasında herhangi bir rol değeri bekliyorsanız, **Rolü Seç** iletişim kutusunda, listeden kullanıcı için uygun rolü seçin ve ardından ekranın altındaki **Seç** düğmesini tıklatın.
1. Atama **Ekle** iletişim kutusunda, **Ata ekle** düğmesini tıklatın.

## <a name="configure-netvision-compas-sso"></a>Netvision Compas SSO'nun yapılandırılSın

Bu bölümde **Netvision Compas'ta**SAML SSO'ya olanak sağlıyorsunuz.
1. Yönetim hesabı kullanarak **Netvision Compas'a** giriş yapın ve yönetim alanına erişin.

    ![Yönetici alanı](media/netvision-compas-tutorial/admin.png)

1. **Sistem** alanını bulun ve **Kimlik Sağlayıcılar'ı**seçin.

    ![Yönetici IDP'leri](media/netvision-compas-tutorial/admin-idps.png)

1. Azure AD'yi yeni bir IDP olarak kaydetmek için eylem **ekle'yi** seçin.

    ![IDP Ekle](media/netvision-compas-tutorial/idps-add.png)

1. Sağlayıcı türü için **SAML'yi** seçin. **Provider type**
1. **Görüntü adı** ve **Açıklama** alanları için anlamlı değerler girin.
1. **Kullanılabilir kullanıcılar** listesinden seçip **seçili** ekle düğmesini seçerek **Netvision Compas** kullanıcılarını IDP'ye atayın. Kullanıcılar, sağlama prosedürünü takip ederken IDP'ye de atanabilirler.
1. **Metadata** SAML seçeneği için **Dosyayı Seç** düğmesini tıklatın ve daha önce bilgisayarınıza kaydedilmiş meta veri dosyasını seçin.
1. **Kaydet**'e tıklayın.

    ![IDP'yi ede](media/netvision-compas-tutorial/idp-edit.png)


### <a name="configure-netvision-compas-test-user"></a>Netvision Compas test kullanıcılarını yapılandırın

Bu bölümde, **Netvision Compas'taki** varolan bir kullanıcıyı SSO için Azure AD'ı kullanacak şekilde yapılandırırsınız.
1. Şirketiniz tarafından tanımlandığı şekilde **Netvision Compas** kullanıcı sağlama yordamını izleyin veya mevcut bir kullanıcı hesabını düzenlemeyin.
1. Kullanıcının profilini tanımlarken, kullanıcının **E-posta (Kişisel)** adresinin Azure AD kullanıcı username@companydomain.extensionadı ile eşleştiğinden emin olun: . Örneğin, `B.Simon@contoso.com`.

    ![Kullanıcıyı edin](media/netvision-compas-tutorial/user-config.png)

Tek oturum açmadan önce kullanıcılar oluşturulmalı ve etkinleştirilmelidir.

## <a name="test-sso"></a>Test SSO 

Bu bölümde, Azure AD tek oturum açma yapılandırmanızı test edeyim.

### <a name="using-the-access-panel-idp-initiated"></a>Erişim Paneli 'ni (IDP başlatıldı) kullanma.

Erişim Paneli'ndeki Netvision Compas karosu tıklattığınızda, SSO'yu kurduğunuz Netvision Compas'ta otomatik olarak oturum açmalısınız. Erişim Paneli hakkında daha fazla bilgi için [Erişim Paneline Giriş'e](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)bakın.

### <a name="directly-accessing-netvision-compas-sp-initiated"></a>Doğrudan Netvision Compas (SP başlatılan) erişme.

1. **Netvision Compas** URL'ye erişin. Örneğin, `https://tenant.compas.cloud`.
1. **Netvision Compas** kullanıcı adını girin ve **İleri'yi**seçin.

    ![Oturum açan kullanıcı](media/netvision-compas-tutorial/login-user.png)

1. **(isteğe bağlı)** Kullanıcıya **Netvision Compas**içinde birden fazla IDP atanmışsa, kullanılabilir IDP'lerin listesi sunulur. **Netvision Compas'ta**daha önce yapılandırılan Azure AD IDP'yi seçin.

    ![Giriş seçin](media/netvision-compas-tutorial/login-choose.png)

1. Kimlik doğrulamasını gerçekleştirmek için Azure AD'ye yönlendirilirsiniz. Başarılı bir şekilde kimlik doğrulaması yaptıktan sonra, SSO'ya ayarladığınız **Netvision Compas'ta** otomatik olarak oturum açmalısınız.

## <a name="additional-resources"></a>Ek kaynaklar

- [SaaS Uygulamalarının Azure Etkin Dizini ile Nasıl Entegre Edilen Öğreticiler Listesi](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory ile uygulama erişimi ve tek oturum açma nedir?](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)

- [Azure Active Directory'de koşullu erişim nedir?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Azure AD ile Netvision Compas'ı deneyin](https://aad.portal.azure.com/)

- [Microsoft Cloud App Security'de oturum denetimi nedir?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)
