---
title: 'Öğretici: SAP Qualtrics ile Azure Active Directory entegrasyonu | Microsoft Dokümanlar'
description: Azure Active Directory ve SAP Qualtrics arasında tek oturum açma yı nasıl yapılandırılayarıştırmayı öğrenin.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 4df889ab-2685-4d15-a163-1ba26567eeda
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 04/03/2020
ms.author: jeedes
ms.openlocfilehash: d7a35f9f366f45a111aa7111501c9261ecbfa87e
ms.sourcegitcommit: 67addb783644bafce5713e3ed10b7599a1d5c151
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/05/2020
ms.locfileid: "80667515"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-sap-qualtrics"></a>Öğretici: SAP Qualtrics ile Azure Active Directory tek oturum açma (SSO) entegrasyonu

Bu eğitimde, SAP Qualtrics'in Azure Etkin Dizin (Azure AD) ile nasıl entegre edileceğinizi öğreneceksiniz. SAP Qualtrics'i Azure AD ile tümleştirdiğinizde şunları yapabilirsiniz:

* SAP Qualtrics'e erişimi olan Azure AD'de denetim.
* Kullanıcılarınızın Azure REKLAM hesaplarıyla SAP Qualtrics'te otomatik olarak oturum açabilmelerini etkinleştirin.
* Hesaplarınızı tek bir merkezi konumda yönetin - Azure portalı.

Azure AD ile SaaS uygulama tümleştirmesi hakkında daha fazla bilgi edinmek için Azure [Active Directory ile uygulama erişimi ve tek oturum açma nedir'e](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)bakın.

## <a name="prerequisites"></a>Ön koşullar

Başlamak için aşağıdaki öğelere ihtiyacınız vardır:

* Azure AD aboneliği. Aboneliğiniz [yoksa, ücretsiz bir hesap](https://azure.microsoft.com/free/)alabilirsiniz.
* SAP Qualtrics tek oturum açma (SSO) aboneliği ni sağladı.

## <a name="scenario-description"></a>Senaryo açıklaması

Bu eğitimde, Azure AD SSO'su bir test ortamında yapılandırın ve test esiniz.

* SAP Qualtrics, **SP** ve **IDP'nin** başlattığı SSO'ya destek veriyor
* SAP Qualtrics **Just In Time** kullanıcı sağlama desteği
* SAP Qualtrics'i yapılandırdıktan sonra, kuruluşunuzun hassas verilerinin gerçek zamanlı olarak sızma ve sızmalarını koruyan oturum denetimini uygulayabilirsiniz. Oturum denetimi Koşullu Erişim'den genişletir. [Microsoft Cloud App Security ile oturum denetimini nasıl uygulayacağınızı öğrenin.](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app)

## <a name="adding-sap-qualtrics-from-the-gallery"></a>Galeriden SAP Qualtrics ekleme

SAP Qualtrics'in Azure AD'ye entegrasyonunu yapılandırmak için, galeriden SAP Qualtrics'i yönetilen SaaS uygulamaları listenize eklemeniz gerekir.

1. Azure [portalında](https://portal.azure.com) bir iş veya okul hesabını veya kişisel bir Microsoft hesabını kullanarak oturum açın.
1. Sol gezinti bölmesinde **Azure Etkin Dizin** hizmetini seçin.
1. Kurumsal **Uygulamalar'a** gidin ve ardından **Tüm Uygulamaları**seçin.
1. Yeni uygulama eklemek için **Yeni uygulama'yı**seçin.
1. **Galeriden Ekle** bölümünde, arama kutusuna **SAP Qualtrics** yazın.
1. Sonuç panelinden **SAP Qualtrics'i** seçin ve ardından uygulamayı ekleyin. Uygulama kiracınıza eklenirken birkaç saniye bekleyin.

## <a name="configure-and-test-azure-ad-single-sign-on-for-sap-qualtrics"></a>SAP Qualtrics için Azure AD oturum açma işlemlerini yapılandırma ve test edin

Azure AD SSO'nu **B.Simon**adlı bir test kullanıcısı kullanarak SAP Qualtrics ile yapılandırın ve test edin. SSO'nun çalışması için, bir Azure AD kullanıcısı ile SAP Qualtrics'teki ilgili kullanıcı arasında bir bağlantı ilişkisi kurmanız gerekir.

Azure AD SSO'yu SAP Qualtrics ile yapılandırmak ve test etmek için aşağıdaki yapı taşlarını tamamlayın:

1. Kullanıcılarınızın bu özelliği kullanmasını sağlamak için **[Azure AD SSO'su yapılandırın.](#configure-azure-ad-sso)**
    1. Azure AD'yi B.Simon ile tek oturum açma test etmek için **[bir Azure AD test kullanıcısı oluşturun.](#create-an-azure-ad-test-user)**
    1. B.Simon'ın Azure AD tek oturum açma kullanmasını sağlamak için **[Azure AD test kullanıcısını atayın.](#assign-the-azure-ad-test-user)**
1. SAP **[Qualtrics SSO'yu yapılandırır](#configure-sap-qualtrics-sso)** - uygulama tarafındaki tek oturum açma ayarlarını yapılandırmak için.
    1. **[SAP Qualtrics test kullanıcısını oluşturun](#create-sap-qualtrics-test-user)** - kullanıcının Azure AD gösterimine bağlı SAP Qualtrics'teki B.Simon'ın bir örneğine sahip olmak için.
1. **[SSO'yu test](#test-sso)** edin - yapılandırmanın çalışıp çalışmadığını doğrulamak için.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO’yu yapılandırma

Azure portalında Azure AD SSO'yu etkinleştirmek için aşağıdaki adımları izleyin.

1. Azure [portalında,](https://portal.azure.com/) **SAP Qualtrics** uygulama tümleştirme sayfasında, **Yönet** bölümünü bulun ve tek **oturum açma'yı**seçin.
1. Tek **bir oturum açma yöntemi** seç sayfasında **SAML'yi**seçin.
1. **SAML sayfasıyla tek oturum** açma'da, ayarları ayarlamak için **Temel SAML Yapılandırması** için düzenleme/kalem simgesini tıklatın.

   ![Temel SAML Yapılandırması'nı düzenleme](common/edit-urls.png)

1. **SAML** ile tek oturum açma'da, **Uygulamayı IDP** tarafından başlatılan modda yapılandırmak istiyorsanız, aşağıdaki alanların değerlerini girin:
    
    a. **Tanımlayıcı** metin kutusuna, aşağıdaki deseni kullanarak bir URL yazın:

    `https://< DATACENTER >.qualtrics.com`
   
    b. **Yanıtla URL** metin kutusuna, aşağıdaki deseni kullanarak bir URL yazın:

    `https://< DATACENTER >.qualtrics.com/login/v1/sso/saml2/default-sp`

    c. **Röle Durumu** metin kutusuna, aşağıdaki deseni kullanarak bir URL yazın:

    `https://< brandID >.< DATACENTER >.qualtrics.com`

1. Uygulamayı **SP** başlatılan modda yapılandırmak istiyorsanız **ek URL'ler ayarla'yı** tıklatın ve aşağıdaki adımı gerçekleştirin:

    URL metin kutusunda **Oturum Aç'ta** aşağıdaki deseni kullanarak bir URL yazın:

    `https://< brandID >.< DATACENTER >.qualtrics.com`

    > [!NOTE]
    > Bu değerler gerçek değildir. Bu değerleri URL'de gerçek Oturum Aç, Tanımlayıcı, Yanıt URL'si ve Röle Durumu ile güncelleştirin. Bu değerleri almak için [Qualtrics Müşteri destek ekibine](https://www.qualtrics.com/support/) başvurun. Azure portalındaki **Temel SAML Yapılandırması** bölümünde gösterilen desenlere de bakabilirsiniz.

1. **SAML ile Tek Oturum** Açma sayfasında, **SAML İmza Sertifikası** bölümünde, App **Federation Metadata Url'sini** kopyalamak ve bilgisayarınıza kaydetmek için kopyala düğmesini tıklatın.

    ![Sertifika indirme bağlantısı](common/copy-metadataurl.png)

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

Bu bölümde, B.Simon'ın SAP Qualtrics'e erişim sağlayarak Azure tek oturum açma'yı kullanmasını sağlayacaksınız.

1. Azure portalında **Kurumsal Uygulamalar'ı**seçin ve ardından **Tüm Uygulamaları**seçin.
1. Uygulamalar listesinde **SAP Qualtrics'i**seçin.
1. Uygulamanın genel bakış sayfasında, **Yönet** bölümünü bulun ve **Kullanıcıları ve grupları**seçin.

   !["Kullanıcılar ve gruplar" bağlantısı](common/users-groups-blade.png)

1. **Kullanıcı Ekle'yi**seçin, ardından **Atama Ekle** iletişim kutusunda Kullanıcılar ve **gruplar** seçin.

    ![Kullanıcı Ekle bağlantısı](common/add-assign-user.png)

1. Kullanıcılar **ve gruplar** iletişim kutusunda, Kullanıcılar listesinden **B.Simon'ı** seçin ve ardından ekranın altındaki **Seç** düğmesini tıklatın.
1. SAML iddiasında herhangi bir rol değeri bekliyorsanız, **Rolü Seç** iletişim kutusunda, listeden kullanıcı için uygun rolü seçin ve ardından ekranın altındaki **Seç** düğmesini tıklatın.
1. Atama **Ekle** iletişim kutusunda, **Ata ekle** düğmesini tıklatın.

## <a name="configure-sap-qualtrics-sso"></a>SAP Qualtrics SSO'nun yapılandırılması

**SAP Qualtrics** tarafında tek oturum açma yapılandırmak için, kopyalanan **App Federation Metadata Url'sini** Azure portalından SAP [Qualtrics destek ekibine](https://www.qualtrics.com/support/)göndermeniz gerekir. Bu ayarı, SAML SSO bağlantısının her iki tarafta da düzgün bir şekilde ayarlanması için ayarlarlar.

### <a name="create-sap-qualtrics-test-user"></a>SAP Qualtrics test kullanıcıoluşturma

Bu bölümde, Britta Simon adlı bir kullanıcı SAP Qualtrics oluşturulur. SAP Qualtrics, varsayılan olarak etkinleştirilen tam zamanında kullanıcı sağlamayı destekler. Bu bölümde sizin için bir eylem öğesi yoktur. SAP Qualtrics'te bir kullanıcı zaten yoksa, kimlik doğrulamadan sonra yeni bir kullanıcı oluşturulur.

## <a name="test-sso"></a>Test SSO 

Bu bölümde, Access Panelini kullanarak Azure AD tek oturum açma yapılandırmanızı sınarsınız.

Erişim Paneli'ndeki SAP Qualtrics karosu tıklattığınızda, SSO'yu kurduğunuz SAP Qualtrics'te otomatik olarak oturum açmalısınız. Erişim Paneli hakkında daha fazla bilgi için [Erişim Paneline Giriş'e](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)bakın.

## <a name="additional-resources"></a>Ek kaynaklar

- [SaaS Uygulamalarının Azure Etkin Dizini ile Nasıl Entegre Edilen Öğreticiler Listesi](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory ile uygulama erişimi ve tek oturum açma nedir?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Azure Active Directory'de koşullu erişim nedir?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Azure AD ile SAP Qualtrics'i deneyin](https://aad.portal.azure.com/)

- [Microsoft Cloud App Security'de oturum denetimi nedir?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [SAP Qualtrics'i gelişmiş görünürlük ve kontrollerle nasıl koruyulur?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

