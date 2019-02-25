---
title: Azure AD SSPR ve mfa'yı (Önizleme) için birleşik kaydına Başlarken
description: Azure AD multi-Factor Authentication etkin birleştirilir ve Self Servis parola sıfırlama kaydı (Önizleme)
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 02/20/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahenry
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2ead303257b0b5a4b56803abe57a0101b8f031c0
ms.sourcegitcommit: 7723b13601429fe8ce101395b7e47831043b970b
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 02/21/2019
ms.locfileid: "56589115"
---
# <a name="enable-combined-security-information-registration-preview"></a>Birleştirilmiş etkinleştir güvenlik bilgileri kayıt (Önizleme)

Yeni deneyimi etkinleştirmeden önce makalesini gözden geçirin [güvenlik bilgileri kayıt (Önizleme) birleştirilmiş](concept-registration-mfa-sspr-combined.md) işlevleri ve bu özellik etkisini anladığınızdan emin olmak için.

![Birleşik güvenlik bilgileri geliştirilmiş kayıt deneyimi oturum açma sırasında daha fazla bilgi isteniyor. İlk yöntem Microsoft Authenticator uygulamasını kaydetme örnek gösterir.](media/howto-registration-mfa-sspr-combined/combined-security-info-more-required.png)

|     |
| --- |
| Azure multi-Factor Authentication ve Azure AD Self Servis parola sıfırlama için birleşik güvenlik bilgileri kayıt Azure Active Directory genel Önizleme özelliğidir. Önizlemeler hakkında daha fazla bilgi için bkz: [ek kullanım koşulları Microsoft Azure önizlemeleri için](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)|
|     |

## <a name="enable-combined-registration"></a>Birleşik kaydı etkinleştirme

Birleşik kaydını etkinleştirmek için aşağıdaki adımları tamamlayın:

1. Azure portalına genel yönetici veya Kullanıcı Yöneticisi olarak oturum açın.
2. Gözat **Azure Active Directory** > **kullanıcı ayarları** > **erişim paneli Önizleme özellikleri için ayarları yönetme**.
3. Altında **kullanıcılar Önizleme özellikleri kaydediliyor ve güvenlik bilgilerinizi yönetmek için - yenileme**, için etkinleştirmeyi seçerseniz bir **seçili** için kullanıcı ve grup **tüm** kullanıcılar.

![Azure AD portalında tüm kullanıcıları için birleşik güvenlik bilgisi Önizleme deneyimini etkinleştirmek](media/howto-registration-mfa-sspr-combined/combined-security-info-enable.png)

> [!NOTE]
> Bir kez birleşik kayıt kaydetmek veya telefon numarası ya da yeni deneyim aracılığıyla mobil uygulama bunları MFA ve SSPR, kullanabileceğiniz bu yöntemler MFA ve SSPR ilkelerinde etkinleştirilip etkinleştirilmediğini onaylayın kullanıcıları etkinleştirin. Bu deneyim devre dışı bırakırsanız, önceki SSPR kaydı için Git Kullanıcılar sayfasında [https:/aka.ms/ssprsetup](https:/aka.ms/ssprsetup) sayfa erişebilmeniz için önce çok faktörlü kimlik doğrulaması gerçekleştirmek için gerekli.

## <a name="next-steps"></a>Sonraki adımlar

[MFA ve SSPR için kullanılabilen yöntemler](concept-authentication-methods.md)

[Self Servis parola sıfırlamayı yapılandırın](howto-sspr-deployment.md)

[Azure çok faktörlü kimlik doğrulamasını yapılandırma](howto-mfa-getstarted.md)

[Güvenlik bilgileri kayıt birleştirilmiş sorunlarını giderme](howto-registration-mfa-sspr-combined-troubleshoot.md)