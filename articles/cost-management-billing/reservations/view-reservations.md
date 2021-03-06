---
title: Azure kaynakları için rezervasyonları görüntüleme | Microsoft Docs
description: Azure portalında Azure rezervasyonlarının nasıl görüntüleneceğini öğrenin.
author: yashesvi
ms.reviewer: yashar
ms.service: cost-management-billing
ms.topic: conceptual
ms.date: 02/13/2020
ms.author: banders
ms.openlocfilehash: 5c9d9074e4b8d0d9e36417daee4d58c1d9b28b64
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/24/2020
ms.locfileid: "77199254"
---
# <a name="view-azure-reservations-in-the-azure-portal"></a>Azure portalında Azure rezervasyonlarını görüntüleme

Abonelik türünüze ve izinlerinize bağlı olarak, Azure için rezervasyonları görüntülemenin birkaç yolu vardır.

## <a name="view-purchased-reservations"></a>Satın alınan rezervasyonları görüntüleme

Varsayılan olarak, bir rezervasyon satın aldığınızda siz ve hesap yöneticisi rezervasyonu görüntüleyebilirsiniz. Siz ve hesap yöneticisi, rezervasyon siparişinde ve rezervasyonda otomatik olarak Sahip rolünü alır. Diğer kişilerin rezervasyonu görüntülemesine izin vermek için bu kişileri, Rezervasyon siparişinde veya rezervasyonda **Sahip** veya **Okuyucu** olarak eklemeniz gerekir.

Daha fazla bilgi için bkz. [Rezervasyonu yönetebilecek kullanıcıları ekleme veya değiştirme](manage-reserved-vm-instance.md#add-or-change-users-who-can-manage-a-reservation).

Bir rezervasyonu Sahip veya Okuyucu olarak görüntülemek için,

1. [Azure Portal](https://portal.azure.com)’ında oturum açın.
2. **Rezervasyonlar** araması yapın.
    ![Azure portalı aramasını gösteren ekran görüntüsü](./media/view-reservations/portal-reservation-search.png)  
3. Listede, Sahip veya Okuyucu rolüne sahip olduğunuz tüm rezervasyonlar gösterilir. Her rezervasyon, bilinen son kullanım yüzdesini gösterir.  
    ![Rezervasyon listesini gösteren örnek](./media/view-reservations/view-reservations.png)
4. Bir rezervasyon seçin ve son beş güne ait kullanım eğilimini görüntüleyin.  
    ![Rezervasyon kullanım eğilimini gösteren örnek](./media/view-reservations/reservation-utilization.png)
5. Ayrılmış Örnek kullanımı API’sini kullanarak ve [Microsoft Azure Consumption Insights Power BI içerik paketi](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-usage) ile de [rezervasyon kullanımını](/power-bi/service-connect-to-azure-consumption-insights) alabilirsiniz.

Bir rezervasyonun kapsamını değiştirmeniz, rezervasyonu bölmeniz veya bir rezervasyonu kimin yönetebileceğini değiştirmeniz gerekirse bkz. [Azure Rezervasyonlarını Yönetme](manage-reserved-vm-instance.md).

## <a name="view-reservation-transactions-for-enterprise-enrollments"></a>Kurumsal kayıtlar için rezervasyon işlemlerini görüntüleme

 İş ortağı tarafından yönetilen bir Kurumsal kaydınız varsa, Kurumsal Anlaşma portalında **Raporlar**’a giderek rezervasyonları görüntüleyin. Diğer Kurumsal kayıtlar için Kurumsal Anlaşma portalında ve Azure portalında rezervasyonları görüntüleyebilirsiniz. Rezervasyon işlemlerini görüntülemek için bir Kurumsal Anlaşma yöneticisi olmanız gerekir.

Azure portalında rezervasyon işlemlerini görüntülemek için,

1. [Azure Portal](https://portal.azure.com)’ında oturum açın.
1. **Maliyet Yönetimi + Faturalama** araması yapın.

    ![Azure portalı aramasını gösteren ekran görüntüsü](./media/view-reservations/portal-cm-billing-search.png)

1. **Rezervasyon işlemleri**’ni seçin.
1. Sonuçları filtrelemek için **Zaman aralığı**, **Tür** veya **Açıklama** seçeneğini belirleyin.
1. **Uygula**’yı seçin.

    ![Rezervasyon işlemlerinin sonuçlarını gösteren ekran görüntüsü](./media/view-reservations/portal-billing-reservation-transaction-results.png)

Bir API kullanarak verileri almak için bkz. [Kurumsal müşteriler için Ayrılmış Örnek işlem ücretlerini alma](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-charges).

## <a name="next-steps"></a>Sonraki adımlar

Azure rezervasyonları hakkında daha fazla bilgi edinmek için aşağıdaki makalelere bakın:

- [Azure rezervasyonları nedir?](save-compute-costs-reservations.md)
- [Azure rezervasyonlarını yönetme](manage-reserved-vm-instance.md)

Hizmet planı satın alma:

- [Cosmos DB ayrılmış kapasitesi için ö ödeme yapma](../../cosmos-db/cosmos-db-reserved-capacity.md)
- [Azure SQL Veritabanı ayrılmış kapasitesi ile SQL Veritabanı işlem kaynakları için ön ödeme yapma](../../sql-database/sql-database-reserved-capacity.md)
- [Azure Ayrılmış VM Örnekleri ile Sanal Makinelere ön ödeme yapma](../../virtual-machines/windows/prepay-reserved-vm-instances.md)

Yazılım planı satın alma:

- [Azure Ayırmaları ile Red Hat yazılım planları için ön ödeme yapma](../../virtual-machines/linux/prepay-rhel-software-charges.md)
- [Azure Ayırmaları ile SUSE yazılım planları için ön ödeme yapma](../../virtual-machines/linux/prepay-suse-software-charges.md)

Kullanımı anlama:

- [Kullandıkça Öde aboneliğiniz için rezervasyon kullanımını anlama](understand-reserved-instance-usage.md)
- [Kurumsal kaydınız için rezervasyon kullanımını anlama](understand-reserved-instance-usage-ea.md)
- [CSP abonelikleri için rezervasyon kullanımını anlama](https://docs.microsoft.com/partner-center/azure-reservations)

## <a name="need-help-contact-us"></a>Yardıma mı ihtiyacınız var? Bizimle iletişim kurun

Sorularınız varsa ya da yardıma gereksinim duyuyorsanız [destek isteği oluşturun](https://go.microsoft.com/fwlink/?linkid=2083458).
