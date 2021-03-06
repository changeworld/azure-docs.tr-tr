---
title: Abonelik bulunamadı hatası - Azure portalında oturum açma
description: Azure portalında veya Azure hesap merkezinde oturum açıldığında Abonelik bulunamadı hatasının oluştuğu bir sorunun çözümünü sağlar.
author: genlin
ms.reviewer: dcscontentpm
tags: billing
ms.service: cost-management-billing
ms.topic: conceptual
ms.date: 02/12/2020
ms.author: banders
ms.custom: seodec18
ms.openlocfilehash: 76f109645e4a27e712066cec7010f2ac7eb6b507
ms.sourcegitcommit: 27bbda320225c2c2a43ac370b604432679a6a7c0
ms.translationtype: HT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/31/2020
ms.locfileid: "80411654"
---
# <a name="no-subscriptions-found-sign-in-error-for-azure-portal-or-azure-account-center"></a>Azure portalı veya Azure hesap merkezi için Abonelik bulunamadı oturum açma hatası

[Azure portalında](https://portal.azure.com/) veya [Azure Hesap Merkezi](https://account.windowsazure.com/Subscriptions)’nde oturum açmayı denediğinizde “Abonelik bulunamadı” hata iletisini alabilirsiniz. Bu makalede, bu sorun için bir çözüm sağlanmaktadır.

## <a name="symptom"></a>Belirti

[Azure portalında](https://portal.azure.com/) veya [Azure hesap merkezinde](https://account.windowsazure.com/Subscriptions) oturum açmayı denediğinizde şu hata iletisini alırsınız: “Abonelik bulunamadı”.

## <a name="cause"></a>Nedeni

Yanlış dizinde seçim yaptıysanız veya hesabınız yeterli izinlere sahip değilse bu sorun oluşur.

## <a name="solution"></a>Çözüm

### <a name="scenario-1-error-message-is-received-in-the-azure-portal"></a>Senaryo 1: [Azure portalında](https://portal.azure.com) hata iletisi alınır

Bu sorunu düzeltmek için:

* Sağ üst kısımda hesabınıza tıklayarak doğru Azure dizininin seçildiğinden emin olun.

  ![Azure portalının sağ üst köşesinde dizini seçin](./media/no-subscriptions-found/directory-switch.png)
* Doğru Azure dizini seçili olduğu halde hata iletisini almaya devam ediyorsanız [hesabınıza Sahip rolünü atayın](../../role-based-access-control/role-assignments-portal.md).

### <a name="scenario-2-error-message-is-received-in-the-azure-account-center"></a>Senaryo 2: [Azure Hesap Merkezi](https://account.windowsazure.com/Subscriptions)’nde hata iletisi alınır

Kullandığınız hesabın Hesap Yöneticisi olup olmadığını denetleyin. Hesap Yöneticisinin kim olduğunu doğrulamak için şu adımları izleyin:

1. [Azure portalındaki Abonelikler görünümünde](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) oturum açın.
1. Denetlemek istediğiniz aboneliği seçin ve **Ayarlar** bölümüne bakın.
1. **Özellikler**’i seçin. Aboneliğin hesap yöneticisi, **Hesap Yöneticisi** kutusunda görüntülenir.  

## <a name="need-help-contact-us"></a>Yardıma mı ihtiyacınız var? Bize ulaşın.

Sorularınız varsa ya da yardıma gereksinim duyuyorsanız [destek isteği oluşturun](https://go.microsoft.com/fwlink/?linkid=2083458).
