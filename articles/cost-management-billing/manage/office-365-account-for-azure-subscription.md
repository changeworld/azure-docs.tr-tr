---
title: Office 365 hesabı ile Azure’a kaydolma
description: Office 365 hesabı kullanarak nasıl Azure aboneliği oluşturulacağını öğrenin
author: JiangChen79
ms.reviewer: adwise
tags: billing,top-support-issue
ms.service: cost-management-billing
ms.topic: conceptual
ms.date: 02/12/2020
ms.author: banders
ms.openlocfilehash: b42a5b83f5442755614a3cb2ae81cffda09b4b3b
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/24/2020
ms.locfileid: "79238117"
---
# <a name="sign-up-for-an-azure-subscription-with-your-office-365-account"></a>Office 365 hesabınızla Azure aboneliğine kaydolma
Office 365 aboneliğiniz varsa, Azure aboneliği oluşturmak için Office 365 hesabınızı kullanabilirsiniz. Office 365 kullanıcı adı ve parolanızı kullanarak [Azure portalında](https://portal.azure.com/) oturum açın. Sanal makineleri ayarlamak veya diğer Azure hizmetlerini kullanmak istiyorsanız, bir Azure aboneliğine kaydolmanız gerekir. Azure aboneliğinizi başkalarıyla paylaşabilir ve [Azure aboneliğinize ve kaynaklarınıza erişimi yönetmek için Rol Tabanlı Erişim Denetimini kullanabilirsiniz](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal)

Zaten bir Office 365 hesabınız ve Azure aboneliğiniz varsa bkz. [bir Office 365 kiracısını Azure aboneliği ile ilişkilendirme](../../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md).

## <a name="get-an-azure-subscription-using-your-office-365-account"></a>Office 365 hesabınızı kullanarak Azure aboneliği alma

Office 365 kullanıcı adı ve parolanızı kullanarak Azure’a kaydolup zaman kazanın ve hesap artışını engelleyin.

1. [Azure.com](https://account.azure.com/signup?offer=MS-AZR-0044p&appId=docs) adresinden kaydolun.
2. Office 365 kullanıcı adı ve parolanızı kullanarak oturum açın. Kullandığınız hesabın yönetici izinlerine sahip olması gerekmez. Birden fazla Office 365 hesabınız varsa, Azure aboneliğinizle ilişkilendirmek istediğiniz Office 365 hesabının kimlik bilgilerini kullandığınızdan emin olun.

   ![Oturum açma sayfasını gösteren ekran görüntüsü.](./media/office-365-account-for-azure-subscription/billing-sign-in-with-office-365-account.png)

3. Gerekli bilgileri girin ve kaydolma işlemini tamamlayın. Mevcut bir Office 365 hesabınız varsa bazı bilgiler gerekli olmayabilir.

    ![Kayıt formunu gösteren ekran görüntüsü.](./media/office-365-account-for-azure-subscription/billing-azure-sign-up-fill-information.png)

- Kuruluşunuzdaki diğer kişileri Azure aboneliğine eklemeniz gerekiyorsa bkz. [Azure portalında erişim yönetimini kullanmaya başlama](../../role-based-access-control/overview.md).

## <a name=""></a><a id="more-about-subs">Azure ve Office 365 abonelikleri hakkında daha fazla bilgi</a>
Office 365 ve Azure, kullanıcıları ve abonelikleri yönetmek için Azure AD hizmetini kullanır. Azure dizini, kullanıcıları ve abonelikleri gruplandırabileceğiniz bir kapsayıcı gibidir. Azure ve Office 365 abonelikleriniz için aynı kullanıcı hesaplarını kullanmak istiyorsanız, Azure aboneliklerinin Office 365 abonelikleriyle aynı dizinde oluşturulduğundan emin olmanız gerekir. Aşağıdaki noktaları göz önünde bulundurun:

* Abonelik bir dizin altında oluşturulur
* Kullanıcılar dizinlere aittir
* Abonelik, aboneliği oluşturan kullanıcının dizininde yer alır. Bu nedenle Office 365 aboneliğiniz, Azure aboneliğinizle aynı hesaba bağlıdır.
* Azure abonelikleri, dizindeki bireysel kullanıcılara aittir
* Office 365 abonelikleri, dizine aittir. Dizinde doğru izinlere sahip kullanıcılar bu abonelikleri yönetebilir.

![Dizin, kullanıcı ve abonelik ilişkisini gösteren ekran görüntüsü.](./media/office-365-account-for-azure-subscription/19-background-information.png)

Daha fazla bilgi için bkz. [Azure aboneliklerinin Azure Active Directory ile ilişkisi](../../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md).

## <a name="need-help-contact-us"></a>Yardıma mı ihtiyacınız var? Bize ulaşın.

Sorularınız varsa ya da yardıma gereksinim duyuyorsanız [destek isteği oluşturun](https://go.microsoft.com/fwlink/?linkid=2083458).
