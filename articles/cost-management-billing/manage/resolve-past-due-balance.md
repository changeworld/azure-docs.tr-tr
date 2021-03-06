---
title: Azure’dan süresi geçmiş bakiye e-postası
description: Azure aboneliğinizin süresi geçmiş bakiyesi varsa nasıl ödeme yapılacağını açıklar
author: genlin
ms.reviewer: dcscontentpm
tags: billing
ms.service: cost-management-billing
ms.topic: conceptual
ms.date: 02/13/2020
ms.author: banders
ms.openlocfilehash: cbfd5f7bf47cdaf43df00c710bd6680373d67b09
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/24/2020
ms.locfileid: "77200614"
---
# <a name="resolve-past-due-balance-for-your-azure-subscription"></a>Azure aboneliğiniz için süresi geçmiş bakiye sorununu çözme

Bu makale Azure’a çevrimiçi ortamda kredi kartıyla kaydolan ve Microsoft Online Services Programı ödeme hesabı olan müşterilere yöneliktir. [Ödeme hesabınızın türünü denetlemeyi](#check-the-type-of-your-account) öğrenin. Microsoft Müşteri Sözleşmesi ödeme hesabınız varsa bunun yerine [Microsoft Azure faturanızı ödeme](../understand/pay-bill.md) konusuna bakın.

Ödemeniz alınmadıysa veya ödemenizi işleyemediysek bir e-posta alır ve Azure portalında abonelik ödemenizin süresinin geçtiğini bildiren bir uyarı görürsünüz. Varsayılan ödeme yönteminiz kredi kartıyla [Hesap Yöneticisi](billing-subscription-transfer.md#whoisaa) kalan ücretleri Azure portalında ödeyebilir. Fatura ile ödemeyi (çek/havale) seçerseniz, ödemenizi faturanızın en altında bulunan konuma gönderin.

> [!IMPORTANT]
> * Aynı kredi kartını kullandığınız birden çok aboneliğiniz varsa ve bunların tümünün süresi geçmişse, kalan bakiyenin tamamını bir defada ödemeniz gerekir.
> * Kalan ücretleri ödemek için kullandığınız kredi kartı, başarısız ödeme yöntemini kullanmış olan tüm abonelikler için yeni varsayılan ödeme yöntemi olur.

## <a name="resolve-past-due-balance-in-the-azure-portal"></a>Azure portalında süresi geçmiş bakiye sorunlarını çözme

1. [Azure portalında](https://portal.azure.com) Hesap Yöneticisi olarak oturum açın.
1. **Maliyet Yönetimi + Faturalama** araması yapın.
1. **Genel Bakış** sayfasında ödeme süresi geçmiş aboneliği seçin.
1. Kalan ücreti ödemek için **Aboneliğe genel bakış** sayfasında kırmızı süresi geçmiş başlığına tıklayın.
    > [!NOTE]
    > Hesap Yöneticisi değilseniz kalan ücreti ödeyemezsiniz.
1. **Bakiyeyi öde** sayfasında **Ödeme yöntemi seç**’e tıklayın.
    ![Ödeme yöntemini seçin bağlantısını gösteren ekran görüntüsü](./media/resolve-past-due-balance/settle-balance-screen.png)

1. Sağdaki yeni dikey pencerede açılan menüden kredi kartını seçin veya mavi renkli **Yeni ödeme yöntemi ekleyin** bağlantısına tıklayarak yeni bir kart ekleyin. Bu kredi kartı, şu anda başarısız olan ödeme yöntemini kullanan tüm abonelikler için etkin ödeme yöntemi olur.
     > [!NOTE]
     > * Toplam kalan bakiye, başarısız ödeme yöntemini kullanan tüm Microsoft hizmetlerindeki kalan ücretleri yansıtır.
     > * Seçili ödeme yönteminin Microsoft hizmetleri için kalan ücretleri de varsa bu, toplam kalan bakiyeye yansıtılır. Bu kalan ücretleri de ödemeniz gerekir.
1. **Ödeme**’ye tıklayın.

## <a name="troubleshoot-declined-credit-card"></a>Reddedilen kart sorununu giderme

Kredi kartı ücretiniz finans kurumunuz tarafından reddedildiyse lütfen sorunu çözmek için finans kurumunuza ulaşın. Bankanızla birlikte denetleyip şu konulardan emin olun:
- Kartta uluslararası işlemler etkin.
- Kart bakiyeyi dengeleyecek yeterli kredi sınırına veya mevduata sahip.
- Kartta, yinelenen ödemeler etkin.

## <a name="not-getting-billing-email-notifications"></a>Faturalama e-posta bildirimleri almıyor musunuz?

Hesap Yöneticisiyseniz [bildirimler için hangi e-posta adresinin kullanıldığını kontrol edin](change-azure-account-profile.md). Düzenli olarak kontrol ettiğiniz bir e-posta adresi kullanmanızı öneririz. E-posta doğruysa, istenmeyen posta klasörünüzü kontrol edin.

## <a name="if-i-forget-to-pay-what-happens"></a>Ödemeyi unutursam ne olur?

Hizmet iptal edilir ve kaynaklarınız artık kullanılamaz. Hizmet sonlandırıldıktan 90 gün sonra Azure verileriniz silinir. Daha fazla bilgi edinmek için bkz. [Microsoft Güven Merkezi - Verilerinizi nasıl yönetiriz?](https://go.microsoft.com/fwLink/p/?LinkID=822930&clcid=0x409)

Ödemenizin işlendiğini bildiğiniz halde aboneliğiniz halen devre dışı bırakılmışsa [Azure Desteği](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade)’ne başvurun.

## <a name="check-the-type-of-your-account"></a>Hesabınızın türünü denetleme
[!INCLUDE [billing-check-mca](../../../includes/billing-check-account-type.md)]

## <a name="need-help-contact-us"></a>Yardıma mı ihtiyacınız var? Bize ulaşın.

Sorularınız varsa ya da yardıma gereksinim duyuyorsanız [destek isteği oluşturun](https://go.microsoft.com/fwlink/?linkid=2083458).
