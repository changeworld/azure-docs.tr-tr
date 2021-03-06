---
title: Azure Güvenlik Denetimi - Olay Yanıtı
description: Güvenlik Denetimi Olay Yanıtı
author: msmbaldwin
manager: rkarlin
ms.service: security
ms.topic: conceptual
ms.date: 12/30/2019
ms.author: mbaldwin
ms.custom: security-recommendations
ms.openlocfilehash: fb3560aa2d3fbf48ab63c4da4d3a8d69cb677209
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75934411"
---
# <a name="security-control-incident-response"></a>Güvenlik Kontrolü: Olay Yanıtı

Bir saldırıyı hızlı bir şekilde keşfetmek için bir olay müdahale altyapısı (örneğin, planlar, tanımlanmış roller, eğitim, iletişim, yönetim gözetimi) geliştirerek ve uygulayarak kuruluşun bilgilerini ve itibarını korumak ve daha sonra etkin bir şekilde hasar ın kontrol altına edilmesi, saldırganın varlığının ortadan kaldırılması ve ağ ve sistemlerin bütünlüğünün geri dalması.

## <a name="101-create-an-incident-response-guide"></a>10.1: Olay yanıt kılavuzu oluşturma

| Azure Kimliği | BDT'ler | Sorumluluk |
|--|--|--|
| 10.1 | 19.1, 19.2, 19.3 | Müşteri |

Kuruluşunuz için bir olay yanıt kılavuzu oluşturun. Personelin tüm rollerini ve olay işleme/yönetiminin algılamadan olay sonrası incelemeye kadar olan aşamalarını tanımlayan yazılı olay yanıt planları olduğundan emin olun.

Azure Güvenlik Merkezi'nde İş Akışı Otomasyonları nasıl yapılandırılabilen:

https://docs.microsoft.com/azure/security-center/security-center-planning-and-operations-guide

Kendi güvenlik olayı yanıt sürecinizi oluşturma kılavuzu:

https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/

Microsoft Güvenlik Yanıt Merkezi'nin Bir Olayın Anatomisi:

https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/

Müşteri ayrıca, kendi olay yanıt planının oluşturulmasına yardımcı olmak için NIST'nin Bilgisayar Güvenliği Olay İşlemleri Kılavuzu'ndan da yararlanabilir:

https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf

## <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2: Olay puanlama ve öncelik lendirme prosedürü oluşturma

| Azure Kimliği | BDT'ler | Sorumluluk |
|--|--|--|
| 10.2 | 19.8 | Müşteri |

Güvenlik Merkezi, önce hangi uyarıların araştırılması gerektiğine öncelik vermenize yardımcı olmak için her uyarıya bir önem atar. Önem derecesi, Güvenlik Merkezi'nin bulguda ne kadar emin olduğuna veya uyarıyı vermek için kullanılan analize ve uyarıya yol açan etkinliğin arkasında kötü amaçlı niyet olduğuna dair güven düzeyine dayanır.

Ayrıca, abonelikleri açıkça işaretleyin (eski için. azure kaynaklarını net bir şekilde tanımlamak ve kategorilere ayırmak için bir adlandırma sistemi oluşturun.

## <a name="103-test-security-response-procedures"></a>10.3: Test güvenlik yanıt yordamları

| Azure Kimliği | BDT'ler | Sorumluluk |
|--|--|--|
| 10.3 | 19 | Müşteri |

Sistemlerinizin olay yanıt yeteneklerini düzenli olarak test etmek için alıştırmalar yapın. Zayıf noktaları ve boşlukları belirleyin ve planı gerektiği gibi gözden geçirin.

NIST'in yayınına bakın: BT Planları ve Yetenekleri için Test, Eğitim ve Egzersiz Programları Kılavuzu:

https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf

## <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4: Güvenlik olayı iletişim bilgilerini sağlayın ve güvenlik olayları için uyarı bildirimlerini yapılandırma

| Azure Kimliği | BDT'ler | Sorumluluk |
|--|--|--|
| 10.4 | 19.5 | Müşteri |

Microsoft Güvenlik Yanıt Merkezi (MSRC), müşterinin verilerine yasadışı veya yetkisiz bir taraf tarafından erişildiğini tespit ederse, güvenlik olayı iletişim bilgileri Microsoft tarafından sizinle iletişim kurmak için kullanılır.  Sorunların çözülmesini sağlamak için olaydan sonraki olayları gözden geçirin.

Azure Güvenlik Merkezi Güvenlik Kişisi nasıl ayarlanır:

https://docs.microsoft.com/azure/security-center/security-center-provide-security-contact-details

## <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5: Olay yanıt sisteminize güvenlik uyarıları dahil edin

| Azure Kimliği | BDT'ler | Sorumluluk |
|--|--|--|
| 10,5 | 19.6 | Müşteri |

Sürekli Dışa Aktarma özelliğini kullanarak Azure Güvenlik Merkezi uyarılarınızı ve önerilerinizi dışa aktarın. Sürekli Dışa Aktarma, uyarı ve önerileri el ile veya sürekli olarak dışa aktarmanıza olanak tanır. Sentinel uyarılarını yayınlamak için Azure Güvenlik Merkezi veri konektörünü kullanabilirsiniz.

Sürekli dışa aktarma nasıl yapılandırılabilen:

https://docs.microsoft.com/azure/security-center/continuous-export

Uyarıları Azure Sentinel'e nasıl aktarın:

https://docs.microsoft.com/azure/sentinel/connect-azure-security-center

## <a name="106-automate-the-response-to-security-alerts"></a>10.6: Güvenlik uyarılarına yanıtı otomatikleştirin

| Azure Kimliği | BDT'ler | Sorumluluk |
|--|--|--|
| 10.6 | 19 | Müşteri |

Güvenlik uyarıları ve önerileri nde Logic Apps &quot;&quot; aracılığıyla yanıtları otomatik olarak tetiklemek için Azure Güvenlik Merkezi'ndeki İş Akışı Otomasyonu özelliğini kullanın.

İş Akışı Otomasyonu ve Mantık Uygulamaları nasıl yapılandırılabilen:

https://docs.microsoft.com/azure/security-center/workflow-automation

## <a name="next-steps"></a>Sonraki adımlar

Bir sonraki güvenlik denetimine bakın: [Penetrasyon Testleri ve Kırmızı Takım Egzersizleri](security-control-penetration-tests-red-team-exercises.md)