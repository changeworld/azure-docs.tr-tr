---
title: Quickstart - Yeni kiracı oluşturmak & erişin - Azure AD
description: Azure Etkin Dizin'ini nasıl bulacağınız ve kuruluşunuz için yeni bir kiracı oluşturma hakkında yönergeler.
services: active-directory
author: msaburnley
manager: daveba
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: quickstart
ms.date: 09/10/2018
ms.author: ajburnle
ms.custom: it-pro, seodec18, fasttrack-edit
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5f29d103ce1be426fb0b5c462cc1d831fefe87b6
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/26/2020
ms.locfileid: "80050002"
---
# <a name="quickstart-create-a-new-tenant-in-azure-active-directory"></a>Hızlı başlangıç: Azure Etkin Dizini'nde yeni bir kiracı oluşturma
Kuruluşunuz için yeni bir kiracı oluşturulması da dahil olmak üzere, Azure Active Directory (Azure AD) portalı kullanarak tüm yönetim görevlerinizi gerçekleştirebilirsiniz. 

Bu hızlı başlangıçta, Azure portala ve Azure Active Directory’ye nasıl erişeceğinizi ve kuruluşunuz için temel bir kiracının nasıl oluşturulacağını öğreneceksiniz.

Azure aboneliğiniz yoksa, başlamadan önce [ücretsiz](https://azure.microsoft.com/free/) bir hesap oluşturun.

## <a name="create-a-new-tenant-for-your-organization"></a>Kuruluşunuz için yeni bir kiracı oluşturma
Azure portalda oturum açtıktan sonra kuruluşunuz için yeni bir kiracı oluşturabilirsiniz. Yeni kiracınız kuruluşunuzu temsil eder ve şirket içindeki ve dışındaki kullanıcılarınız için belirli bir Microsoft bulut hizmetleri örneğini yönetmenize yardımcı olur.

### <a name="to-create-a-new-tenant"></a>Yeni kiracı oluşturmak için

1. Kuruluşunuzun [Azure portalında](https://portal.azure.com/)oturum açın.

1. Azure portalı menüsünden **kaynak oluştur'u**seçin.  

    ![Azure Etkin Dizin resoure sayfası oluşturma](media/active-directory-access-create-new-tenant/azure-ad-portal.png)

1. **Kimlik'i**seçin ve ardından **Azure Etkin Dizini'ni**seçin.

    **Dizin oluştur** sayfası görüntülenir.

    ![Azure Active Directory Oluştur sayfası](media/active-directory-access-create-new-tenant/azure-ad-create-new-tenant.png)

1.  **Dizin oluştur** sayfasına aşağıdaki bilgileri girin:
    
    - **Kuruluş adı** kutusuna _Contoso_ yazın.

    - **İlk etki alanı adı** kutusuna _Contoso_ yazın.

    - **Ülke veya bölge** kutusunda _Amerika Birleşik Devletleri_ seçeneğini değiştirmeden bırakın.

1. **Oluştur'u**seçin.

contoso.onmicrosoft.com etki alanıyla yeni kiracınız oluşturulur.

## <a name="clean-up-resources"></a>Kaynakları temizleme
Bu uygulamayı kullanmaya devam etmeyecekseniz, aşağıdaki adımları kullanarak kiracıyı silebilirsiniz:

- Azure Portalı'ndaki **Dizin + abonelik** filtresi aracılığıyla silmek istediğiniz dizinde oturum açarak ve gerekirse hedef dizine geçiş yaptığınızdan emin olun.
- **Azure Active Directory** seçeneğini belirleyin ve sonra **Contoso - Genel Bakış** sayfasında **Dizini sil**’i seçin.

    Kiracı ve ilişkili bilgileri silinir.

    ![Vurgulanan Sil dizin düğmesiyle genel bakış sayfası](media/active-directory-access-create-new-tenant/azure-ad-delete-new-tenant.png)

## <a name="next-steps"></a>Sonraki adımlar
- Etki alanı adlarını değiştirme veya ekleme; bkz. [Azure Active Directory’ye özel etki alanı adı ekleme](add-custom-domain.md)

- Kullanıcı ekleme; bkz. [Yeni kullanıcı ekleme veya silme](add-users-azure-active-directory.md)

- Gruplar ve üyeler ekleme; bkz. [Temel bir grup oluşturma ve üyeler ekleme](active-directory-groups-create-azure-portal.md)

- Kuruluşunuzun uygulama ve kaynak erişimini yönetmenize yardımcı olmak için Ayrıcalıklı Kimlik Yönetimi ve [Koşullu Erişimi](../../role-based-access-control/conditional-access-azure-management.md) [kullanarak rol tabanlı erişim](../../role-based-access-control/pim-azure-resource.md) hakkında bilgi edinin.

- [Temel lisans bilgileri, terminoloji ve ilişkili özellikleri](active-directory-whatis.md) dahil olmak üzere Azure AD hakkında bilgi edinin.
