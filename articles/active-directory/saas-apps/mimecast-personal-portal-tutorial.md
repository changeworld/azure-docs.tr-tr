---
title: 'Öğretici: Mimecast Kişisel Portal ile Azure Active Directory entegrasyonu | Microsoft Dokümanlar'
description: Azure Active Directory ve Mimecast Personal Portal arasında tek oturum açma yı nasıl yapılandırabilirsiniz öğrenin.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: 345b22be-d87e-45a4-b4c0-70a67eaf9bfd
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/24/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 259635613855e4d7687cf569c94bbd3dd04027fe
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/24/2020
ms.locfileid: "73160617"
---
# <a name="tutorial-azure-active-directory-integration-with-mimecast-personal-portal"></a>Öğretici: Mimecast Kişisel Portalı ile Azure Active Directory entegrasyonu

Bu eğitimde, Mimecast Kişisel Portal'ı Azure Active Directory (Azure AD) ile nasıl entegre acağınızı öğreneceksiniz.
Mimecast Kişisel Portalıazure AD ile tümleştirmek size aşağıdaki avantajları sağlar:

* Mimecast Kişisel Portalı'na erişimi olan Azure AD'de kontrol edebilirsiniz.
* Kullanıcılarınızın Azure AD hesaplarıyla Mimecast Kişisel Portalında (Tek Oturum Açma) otomatik olarak oturum açmalarını sağlayabilirsiniz.
* Hesaplarınızı tek bir merkezi konumda yönetebilirsiniz - Azure portalı.

Azure AD ile SaaS uygulama tümleştirmesi hakkında daha fazla bilgi almak istiyorsanız, [Azure Active Directory ile uygulama erişimi ve tek oturum açma nedir'e](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)bakın.
Azure aboneliğiniz yoksa, başlamadan önce [ücretsiz bir hesap oluşturun.](https://azure.microsoft.com/free/)

## <a name="prerequisites"></a>Ön koşullar

Azure AD tümleştirmesini Mimecast Kişisel Portalı ile yapılandırmak için aşağıdaki öğelere ihtiyacınız vardır:

* Azure AD aboneliği. Azure REKLAM ortamınız yoksa, [burada](https://azure.microsoft.com/pricing/free-trial/) bir aylık deneme sürümü alabilirsiniz
* Mimecast Personal Portal tek oturum açma özellikli abonelik

## <a name="scenario-description"></a>Senaryo açıklaması

Bu öğreticide, Azure AD tek oturum açma işlemlerini bir test ortamında yapılandırıp sınayabilirsiniz.

* Mimecast Kişisel Portal **SP** başlatılan SSO destekler

## <a name="adding-mimecast-personal-portal-from-the-gallery"></a>Galeriden Mimecast Kişisel Portal Ekleme

Mimecast Kişisel Portalı'nın Azure AD'ye entegrasyonunu yapılandırmak için galeriden yönetilen SaaS uygulamaları listenize Mimecast Kişisel Portalı eklemeniz gerekir.

**Galeriden Mimecast Kişisel Portalı eklemek için aşağıdaki adımları gerçekleştirin:**

1. Sol daki gezinti panelindeki **[Azure portalında](https://portal.azure.com)** **Azure Active Directory simgesini** tıklatın.

    ![Azure Etkin Dizin düğmesi](common/select-azuread.png)

2. Kurumsal **Uygulamalar'a** gidin ve ardından **Tüm Uygulamalar** seçeneğini belirleyin.

    ![Enterprise uygulamaları bıçak](common/enterprise-applications.png)

3. Yeni uygulama eklemek için iletişim kutusunun üst kısmındaki **Yeni uygulama** düğmesini tıklatın.

    ![Yeni uygulama düğmesi](common/add-new-app.png)

4. Arama kutusunda, **Mimecast Kişisel Portal**yazın, sonuç panelinden **Mimecast Kişisel Portal'ı** seçin ve uygulamayı eklemek için **Ekle** düğmesini tıklatın.

     ![Mimecast Kişisel Portal sonuç listesinde](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD tek oturum açma yapılandırma ve test

Bu bölümde, **Britta Simon**adlı bir test kullanıcısına göre Mimecast Personal Portal ile Azure AD tek oturumunu yapılandırıp test edersiniz.
Tek oturum açmanın işe yaraması için, Bir Azure REKLAM kullanıcısı ile Mimecast Kişisel Portalı'ndaki ilgili kullanıcı arasında bir bağlantı ilişkisi nin kurulması gerekir.

Azure AD oturumunu Mimecast Kişisel Portalı ile yapılandırmak ve test etmek için aşağıdaki yapı taşlarını tamamlamanız gerekir:

1. Kullanıcılarınızın bu özelliği kullanmasını sağlamak için Azure AD Tek Oturum Açma'yı **[yapılandırın.](#configure-azure-ad-single-sign-on)**
2. **[Mimecast Personal Portal Tek Oturum Açma](#configure-mimecast-personal-portal-single-sign-on)** -uygulama tarafındaki Tek Oturum Açma ayarlarını yapılandırmak için yapılandırın.
3. Azure AD tek oturum açma işlemini Britta Simon ile test etmek için **[bir Azure AD test kullanıcısı oluşturun.](#create-an-azure-ad-test-user)**
4. Britta Simon'ın Azure AD tek oturum açma işlemini kullanmasını sağlamak için **[Azure AD test kullanıcısını atayın.](#assign-the-azure-ad-test-user)**
5. **[Mimecast Kişisel Portal test kullanıcıoluşturun](#create-mimecast-personal-portal-test-user)** - Kullanıcının Azure AD gösterimi ile bağlantılı Mimecast Kişisel Portal Britta Simon bir meslektaşı olması.
6. **[Yapılandırmanın](#test-single-sign-on)** çalışıp çalışmadığını doğrulamak için tek oturum açma testi yapın.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD'yi tek oturum açma yapılandırma

Bu bölümde, Azure portalında Azure AD oturum açma'yı etkinleştirin.

Mimecast Personal Portal ile Azure AD oturum açma işlemlerini yapılandırmak için aşağıdaki adımları gerçekleştirin:

1. Azure [portalında,](https://portal.azure.com/) **Mimecast Kişisel Portal** uygulama tümleştirme sayfasında **Tek oturum açma'yı**seçin.

    ![Tek oturum açma bağlantısını yapılandırma](common/select-sso.png)

2. Tek **oturum açma yöntemi** iletişim kutusunda, tek oturum açmayı etkinleştirmek için **SAML/WS-Fed** modunu seçin.

    ![Tek oturum açma seçme modu](common/select-saml-option.png)

3. **SAML sayfasıyla Tek Oturum Açma'da** **Temel SAML Yapılandırma** iletişim kutusunu açmak için **Düzenleme** simgesini tıklatın.

    ![Temel SAML Yapılandırması'nı düzenleme](common/edit-urls.png)

4. Temel **SAML Yapılandırması** bölümünde aşağıdaki adımları gerçekleştirin:

    ![Mimecast Kişisel Portal Etki Alanı ve URL'ler tek oturum açma bilgileri](common/sp-identifier-reply.png)

    a. Oturum **Açma URL** metin kutusuna bir URL yazın: 

    | Bölge  |  Değer | 
    | --------------- | --------------- | 
    | Avrupa          | `https://eu-api.mimecast.com/login/saml`|
    | Amerika Birleşik Devletleri   | `https://us-api.mimecast.com/login/saml`|
    | Güney Afrika    | `https://za-api.mimecast.com/login/saml`|
    | Avustralya       | `https://au-api.mimecast.com/login/saml`|
    | Offshore        | `https://jer-api.mimecast.com/login/saml`|

    b. **Tanımlayıcı** metin kutusunda, aşağıdaki deseni kullanarak bir URL yazın:

    | Bölge  |  Değer | 
    | --------------- | --------------- |
    | Avrupa          | `https://eu-api.mimecast.com/sso/<accountcode>`|
    | Amerika Birleşik Devletleri   | `https://us-api.mimecast.com/sso/<accountcode>`|    
    | Güney Afrika    | `https://za-api.mimecast.com/sso/<accountcode>`|
    | Avustralya       | `https://au-api.mimecast.com/sso/<accountcode>`|
    | Offshore        | `https://jer-api.mimecast.com/sso/<accountcode>`|

    c. **Yanıtla URL** metin kutusuna bir URL yazın: 

    | Bölge  |  Değer | 
    | --------------- | --------------- | 
    | Avrupa          | `https://eu-api.mimecast.com/login/saml`|
    | Amerika Birleşik Devletleri   | `https://us-api.mimecast.com/login/saml`|
    | Güney Afrika    | `https://za-api.mimecast.com/login/saml`|
    | Avustralya       | `https://au-api.mimecast.com/login/saml`|
    | Offshore        | `https://jer-api.mimecast.com/login/saml`|

    > [!NOTE]
    > Tanımlayıcı değeri gerçek değildir. Değeri gerçek Tanımlayıcı ile güncelleştirin. Değeri almak için [Mimecast Personal Portal Client destek ekibine](https://www.mimecast.com/customer-success/technical-support/) başvurun. Azure portalındaki **Temel SAML Yapılandırması** bölümünde gösterilen desenlere de bakabilirsiniz.

4. **SAML ile Tek Oturum Açma** sayfasında, **SAML İmza Sertifikası** bölümünde, sertifikayı **(Base64)** gereksiniminize göre verilen seçeneklerden indirmek ve bilgisayarınıza kaydetmek için **İndir'i** tıklatın.

    ![Sertifika indirme bağlantısı](common/certificatebase64.png)

6. **Mimecast Kişisel Portal** ı Ayarla bölümünde, gereksiniminize göre uygun URL'yi kopyalayın.

    ![Yapılandırma URL'lerini kopyalama](common/copy-configuration-urls.png)

    a. Oturum Açma URL’si

    b. Azure Reklam Tanımlayıcısı

    c. Giriş URL'si

### <a name="configure-mimecast-personal-portal-single-sign-on"></a>Yapıla, Mimecast Kişisel Portal Tek Oturum Açma

1. Farklı bir web tarayıcısı penceresinde, mimecast Kişisel Portalınıza yönetici olarak giriş yapın.

2. ** \> Hizmet Uygulamalarına**gidin.
   
    ![Uygulamalar](./media/mimecast-personal-portal-tutorial/ic794998.png "Uygulamalar")

3. **Kimlik Doğrulama Profilleri'ni**tıklatın.
   
    ![Kimlik Doğrulama Profilleri](./media/mimecast-personal-portal-tutorial/ic794999.png "Kimlik Doğrulama Profilleri")

4. **Yeni Kimlik Doğrulama Profili'ni**tıklatın.
   
    ![Yeni Kimlik Doğrulama Profili](./media/mimecast-personal-portal-tutorial/ic795000.png "Yeni Kimlik Doğrulama Profili")

5. Kimlik **Doğrulama Profili** bölümünde aşağıdaki adımları gerçekleştirin:
   
    ![Kimlik Doğrulama Profili](./media/mimecast-personal-portal-tutorial/ic795001.png "Kimlik Doğrulama Profili")
   
    a. **Açıklama** metin kutusunda, yapılandırmanız için bir ad yazın.
   
    b. **Mimecast Kişisel Portalı için SAML Kimlik Doğrulamasını Uygula'yı**seçin.
   
    c. **Sağlayıcı**olarak Azure **Etkin Dizini'ni**seçin.
   
    d. **Veren URL** textbox'ına, Azure portalından kopyaladığınız Azure **Reklam Tanımlayıcısı'nın**değerini yapıştırın.
   
    e. **Giriş URL** textbox'ına, Azure portalından kopyalamış olduğunuz **Giriş URL'sinin**değerini yapıştırın.
   
    f. **Logout URL** textbox'ına, Azure portalından kopyalamış olduğunuz **Logout URL**değerini yapıştırın.

    g. Azure portalından indirilen not defterinde **base-64** kodlu sertifikanızı açın, içeriğini panonuza kopyalayın ve ardından **Kimlik Sağlayıcı Sertifikası (Metadata)** textbox'ına yapıştırın.

    h. **Tek Oturum Aç'ı**seçin.
   
    i. **Kaydet**'e tıklayın.

### <a name="create-an-azure-ad-test-user"></a>Azure AD test kullanıcısı oluşturma 

Bu bölümün amacı, Azure portalında Britta Simon adında bir test kullanıcısı oluşturmaktır.

1. Azure portalında, sol bölmede **Azure Etkin Dizini'ni**seçin, **Kullanıcılar'ı**seçin ve ardından **Tüm Kullanıcıları**seçin.

    !["Kullanıcılar ve gruplar" ve "Tüm kullanıcılar" bağlantıları](common/users.png)

2. Ekranın üst kısmında **Yeni kullanıcı** yı seçin.

    ![Yeni kullanıcı Düğmesi](common/new-user.png)

3. Kullanıcı özelliklerinde aşağıdaki adımları gerçekleştirin.

    ![Kullanıcı iletişim kutusu](common/user-properties.png)

    a. **Ad** alanında **BrittaSimon**girin.
  
    b. Kullanıcı **adı** alanı **türünde\@brittasimon yourcompanydomain.extension**  
    Örneğin, BrittaSimon@contoso.com

    c. Parola onay kutusunu **göster'i** seçin ve ardından Parola kutusunda görüntülenen değeri yazın.

    d. **Oluştur'u**tıklatın.

### <a name="assign-the-azure-ad-test-user"></a>Azure AD test kullanıcısını atama

Bu bölümde, Britta Simon'ın Mimecast Kişisel Portalı'na erişim sağlayarak Azure tek oturum açma işlemini kullanmasını sağlarsınız.

1. Azure portalında **Kurumsal Uygulamalar'ı**seçin, **Tüm uygulamaları**seçin, ardından **Mimecast Kişisel Portalı'nı**seçin.

    ![Kurumsal uygulamalar bıçak](common/enterprise-applications.png)

2. Uygulamalar listesinde **Mimecast Kişisel Portalı**yazın ve seçin.

    ![Uygulamalar listesindeki Mimecast Kişisel Portal bağlantısı](common/all-applications.png)

3. Soldaki **menüde, Kullanıcılar ve gruplar**seçin.

    !["Kullanıcılar ve gruplar" bağlantısı](common/users-groups-blade.png)

4. Kullanıcı **Ekle** düğmesini tıklatın ve ardından **Atama Ekle** iletişim kutusunda **Kullanıcılar ve gruplar'ı** seçin.

    ![Atama Ekle bölmesi](common/add-assign-user.png)

5. Kullanıcılar **ve gruplar** iletişim kutusunda, Kullanıcılar listesinde **Britta Simon'ı** seçin ve ardından ekranın altındaki **Seç** düğmesini tıklatın.

6. SAML iddiasında herhangi bir rol değeri bekliyorsanız, **Rolü Seç** iletişim kutusunda listeden kullanıcı için uygun rolü seçin ve ardından ekranın altındaki **Seç** düğmesini tıklatın.

7. Atama **Ekle** iletişim kutusunda **Atla** düğmesini tıklatın.

### <a name="create-mimecast-personal-portal-test-user"></a>Mimecast Kişisel Portal test kullanıcı oluşturma

Azure AD kullanıcılarının Mimecast Kişisel Portalı'na giriş yapabilmeleri için Mimecast Kişisel Portalı'na dahil edilmeleri gerekir. Mimecast Kişisel Portal durumunda, sağlama manuel bir görevdir.

Kullanıcı oluşturmadan önce bir etki alanı kaydetmeniz gerekir.

**Kullanıcı sağlama yapılandırmak için aşağıdaki adımları gerçekleştirin:**

1. **Mimecast Personal** Portal'ınızda yönetici olarak oturum açın.

2. **Dizinler \> Dahili**gidin.
   
    ![Dizinler](./media/mimecast-personal-portal-tutorial/ic795003.png "Dizinler")

3. **Yeni Etki Alanını Kaydedin'i**tıklatın.
   
    ![Yeni Etki Alanı Kaydol](./media/mimecast-personal-portal-tutorial/ic795004.png "Yeni Etki Alanı Kaydol")

4. Yeni etki alanınız oluşturulduktan sonra **Yeni Adres'i**tıklatın.
   
    ![Yeni Adres](./media/mimecast-personal-portal-tutorial/ic795005.png "Yeni Adres")

5. Yeni adres iletişim kutusunda, sağlamak istediğiniz geçerli bir Azure REKLAM hesabının aşağıdaki adımlarını gerçekleştirin:
   
    ![Kaydet](./media/mimecast-personal-portal-tutorial/ic795006.png "Kaydet")
   
    a. **E-posta Adresi** metin kutusunda, **\@BrittaSimon contoso.com**olarak kullanıcının **E-posta Adresini** yazın.
    
    b. Genel **Ad** metin kutusuna, **kullanıcı adını** **BrittaSimon**olarak yazın.

    c. **Parola**ve **Şifre'yi Onayla** metin kutularına, kullanıcının **Parolasını** yazın.
   
    b. **Kaydet**'e tıklayın.

>[!NOTE]
>Azure AD kullanıcı hesaplarını sağlamak için Mimecast Personal Portal tarafından sağlanan diğer Mimecast Kişisel Portal kullanıcı hesabı oluşturma araçlarını veya API'lerini kullanabilirsiniz.

### <a name="test-single-sign-on"></a>Çoklu oturum açma testi 

Bu bölümde, Access Panelini kullanarak Azure AD tek oturum açma yapılandırmanızı sınarsınız.

Erişim Paneli'ndeki Mimecast Kişisel Portal döşemesini tıklattığınızda, SSO'yu kurduğunuz Mimecast Kişisel Portalı'nda otomatik olarak oturum açmalısınız. Erişim Paneli hakkında daha fazla bilgi için [Erişim Paneline Giriş'e](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)bakın.

## <a name="additional-resources"></a>Ek Kaynaklar

- [SaaS Uygulamalarının Azure Etkin Dizini ile Nasıl Entegre Edilen Öğreticiler Listesi](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma özellikleri nelerdir?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Azure Etkin Dizinde Koşullu Erişim Nedir?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

