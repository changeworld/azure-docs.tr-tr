---
title: SMS, e-postalar, anında iletme bildirimleri için hız sınırlaması
description: Azure'un bir eylem grubundan olası SMS, e-posta, Azure Uygulaması push veya webhook bildirimlerinin sayısını nasıl sınırladıklarını anlayın.
author: dkamstra
ms.author: dukek
ms.topic: conceptual
ms.date: 3/12/2018
ms.subservice: alerts
ms.openlocfilehash: 61e6cc22171815b15b865dd6ed5670bd9c446ead
ms.sourcegitcommit: fb23286d4769442631079c7ed5da1ed14afdd5fc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/10/2020
ms.locfileid: "81114323"
---
# <a name="rate-limiting-for-voice-sms-emails-azure-app-push-notifications-and-webhook-posts"></a>Ses, SMS, e-postalar, Azure Uygulaması anında iletme bildirimleri ve webhook gönderileri için hız sınırlaması
Hız sınırlaması, belirli bir telefon numarasına, e-posta adresine veya cihaza çok fazla gönderildiğinde oluşan bildirimlerin askıya alınmasıdır. Hız sınırlaması, uyarıların yönetilebilir ve işlenebilir olmasını sağlar.

Oran sınırı eşikleri şunlardır:

- **SMS**: Her 5 dakikada bir en fazla 1 SMS.
- **Ses**: Her 5 dakikada bir en fazla 1 sesli arama.
- **E-posta**: Bir saat içinde en fazla 100 e-posta.
 
  Diğer eylemler oranı sınırlı değildir.

## <a name="rate-limit-rules"></a>Oran sınırı kuralları
- Belirli bir telefon numarası veya e-posta, eşiğin izin verdiğinden daha fazla ileti aldığında oranı sınırlıdır.
- Telefon numarası veya e-posta, birçok abonelikte eylem gruplarının bir parçası olabilir. Fiyat sınırlaması tüm abonelikler için geçerlidir. İletiler birden çok abonelikten gönderilse bile, eşiğe ulaşılır ulaşmaz geçerlidir.
- Bir e-posta adresi nin fiyatı sınırlı olduğunda, hızı sınırlandıran iletişim kurmak için ek bir bildirim gönderilir. Oran sınırlamasüresi nin dolduğu e-postayı belirtir.

## <a name="next-steps"></a>Sonraki adımlar ##
* [SMS uyarısı davranışı](alerts-sms-behavior.md)hakkında daha fazla bilgi edinin.
* Etkinlik [günlüğü uyarılarına genel](alerts-overview.md)bir bakış alın ve uyarıları nasıl alacağınızı öğrenin.  
* Bir hizmet durumu bildirimi yayınlandığında uyarıları nasıl [yapılandırıştırılaca](../../azure-monitor/platform/alerts-activity-log-service-notifications.md)öğrenin.

