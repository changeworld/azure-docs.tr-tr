---
title: Salesforce için müşteri adayı yönetimini yapılandırma | Azure Marketi
description: Azure Marketi müşterileri için Salesforce'ta müşteri adayı yönetimini yapılandırın.
author: qianw211
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 03/30/2020
ms.author: dsindona
ms.openlocfilehash: 087cdafe8b819e4929e1608ed7e00be2c1169414
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/13/2020
ms.locfileid: "81263103"
---
# <a name="configure-lead-management-for-salesforce"></a>Salesforce için müşteri adayı yönetimini yapılandırma

Bu makalede, satış müşteri adaylarını ticari pazar teklifinizden işlemek için Salesforce sisteminizin nasıl ayarlanır.

> [!Note]
> Market, **Ülke** alanı için değerler listesi gibi önceden doldurulmuş listeleri desteklemez. Devam etmeden önce ayarlanmış liste olmadığından emin olun. Alternatif olarak, müşteri adaylarını alacak şekilde bir [HTTPS bitiş noktası](./commercial-marketplace-lead-management-instructions-https.md) veya Azure [tablosu](./commercial-marketplace-lead-management-instructions-azure-table.md) yapılandırabilirsiniz.

## <a name="set-up-your-salesforce-system"></a>Salesforce sisteminizi ayarlama

1. Salesforce'ta oturum açın.
2. Salesforce aydınlatma deneyimini kullanıyorsanız.
    1. Salesforce Ana sayfasından **Kurulum'u** seçin.
    ![Salesforce kurulumu](./media/commercial-marketplace-lead-management-instructions-salesforce/salesforce-1.png)

    1. Kurulum sayfasından, sol navigasyon üzerinden **Platform Araçları->Özellik Ayarları->Pazarlama->Web-to-Lead**gidin.

        ![Salesforce Web'den Müşteri Adayına](./media/commercial-marketplace-lead-management-instructions-salesforce/salesforce-2.png)

3. Salesforce Classic deneyimini kullanıyorsanız:
    1. Salesforce Ana sayfasından **Kurulum'u** seçin.
    ![Salesforce klasik kurulum](./media/commercial-marketplace-lead-management-instructions-salesforce/salesforce-classic-setup.png)

    1. Kurulum sayfasından, sol gezinme yoluyla Yapı **->Özelleştir->Müşteri Adayları->Web'den Müşteri Adayına.**

        ![Salesforce klasik web-to-lead](./media/commercial-marketplace-lead-management-instructions-salesforce/salesforce-classic-web-to-lead.png)

Talimatların geri kalanı, hangi Salesforce deneyimini kullandığınızdan bağımsız olarak aynıdır.

4. **Web'den Müşteri Adayına Kurulum sayfasında,** **Web'den Müşteri Adayı Formu'na** Oluştur düğmesini seçin.
5. **Web'den Müşteri Adayına**Kurulum'da, **Web'den Müşteri Adayı Formu'na oluştur'u**seçin.
    ![Salesforce - Web'den Müşteri Adayına Kurulum](./media/commercial-marketplace-lead-management-instructions-salesforce/salesforce-3.png)

6. **Web'den Müşteri Adayı Formu'na**, `the Include reCAPTCHA in HTML` ayarın işaretsiz olduğundan emin olun ve **Oluştur'u**seçin. 
    ![Salesforce - Web'den Müşteri Adayı Formu Oluşturma](./media/commercial-marketplace-lead-management-instructions-salesforce/salesforce-4.png)

7. Bazı HTML metin ile sunulacaktır. "oid" metnini arayın ve HTML metninden **oid değerini** kopyalayın (yalnızca tırnak işaretleri arasındaki metin) ve kaydedin. Bu değeri yayımlama portalındaki **Kuruluş Tanımlayıcı** alanına yapıştıracaksınız.
    ![Salesforce - Web'den Müşteri Adayı Formu Oluşturma](./media/commercial-marketplace-lead-management-instructions-salesforce/salesforce-5.png)

8. Seçili **Bitmiş**.

## <a name="configure-your-offer-to-send-leads-to-salesforce"></a>Müşteri adaylarını Salesforce'a göndermek için teklifinizi yapılandırın

Yayımlama portalında teklifiniz için müşteri adayı yönetimi bilgilerini yapılandırmaya hazır olduğunuzda aşağıdaki adımları izleyin:

1. **Teklifiniz** için Teklif kurulum sayfasına gidin.
1. Müşteri Adayı Yönetimi bölümü altında **Bağlan'ı** seçin.
    ![Müşteri adayı yönetimi - Bağlan](./media/commercial-marketplace-lead-management-instructions-salesforce/lead-management-connect.png)

1. Bağlantı ayrıntıları açılır penceresinde, **Müşteri Adayı Hedef** için `oid` **Salesforce'u** seçin ve Kuruluş **tanımlayıcıalanına** daha önceki adımları izleyerek oluşturduğunuz web'den müşteri adayı formuna yapıştırın.

1. **Kişi e-postası** - Şirketinizde yeni bir müşteri adayı geldiğinde e-posta bildirimleri alması gereken kişiler için e-posta sağlayın. Birden çok e-postayı yarı kolon ile ayırarak sağlayabilirsiniz.

1. **Tamam**’ı seçin.

Bir müşteri adayı hedefine başarıyla bağlandığınızdan emin olmak için doğrulama düğmesini tıklatın. Başarılı olursa, müşteri adayı hedefinde bir test müşteri adayınız olur.

>[!Note]
>Teklifin geri kalanını yapılandırmayı bitirmeniz ve teklifiçin müşteri adayı alabilmek için yayımlamanız gerekir.

![Bağlantı ayrıntıları - Bir müşteri adayı hedef seçin](./media/commercial-marketplace-lead-management-instructions-salesforce/choose-lead-destination.png)

![Bağlantı ayrıntıları - Bir müşteri adayı hedef seçin](./media/commercial-marketplace-lead-management-instructions-salesforce/salesforce-connection-details.png)
