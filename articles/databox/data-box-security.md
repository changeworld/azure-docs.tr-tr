---
title: Microsoft Azure Data Box güvenliğine genel bakış | Verilerde Microsoft Docs
description: Data Box’ta yer alan cihaz, hizmet ve verilerin Azure Data Box güvenlik özelliklerini tanımlar
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: conceptual
ms.date: 09/23/2019
ms.author: alkohli
ms.openlocfilehash: 21b05631f1c225c9c4b1f7c65d18588900850b8e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77911732"
---
# <a name="azure-data-box-security-and-data-protection"></a>Azure Data Box güvenliği ve veri koruması

Data Box, verilerinizi yalnızca yetkili varlıkların görüntüleyebilmesini, değiştirebilmesini veya silebilmesini sağlayarak veri koruma için güvenli çözüm getirir. Bu makalede, Data Box çözümü bileşenlerinden her birini ve buralarda depolanan verileri korumaya yardımcı olan Azure Data Box güvenliği açıklanır. 

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-intro-sentence.md)]

## <a name="data-flow-through-components"></a>Bileşenler arasında veri akışı

Microsoft Azure Data Box çözümü birbiriyle etkileşimde olan dört ana bileşenden oluşur:

- **Azure’da barındırılan Azure Data Box hizmeti** – Cihaz sırası oluşturmak, cihazı yapılandırmak ve tamamlanması için sırayı izlemek amacıyla kullandığınız yönetim hizmeti.
- **Data Box cihazı** – Şirket içi verilerinizi Azure’a aktarmanız için size gönderilen aktarım cihazı. 
- **Cihaza bağlı istemciler/konaklar** – Altyapınızda Data Box cihazına bağlanan ve korunması gereken verileri içeren istemciler.
- **Bulut depolama** – Azure bulutunda verilerin depolandığı konum. Bu genellikle, oluşturduğunuz Azure Data Box kaynağına bağlanan depolama hesabıdır.

Aşağıdaki diyagram, Azure Data Box çözümü aracılığıyla şirket içinden Azure’a veri akışını gösterir.

![Data Box güvenliği](media/data-box-security/data-box-security-2.png)

Veriler bu çözüm den akış yaptıkça, olaylar günlüğe kaydedilir ve günlükler oluşturulur. Daha fazla bilgi için [Azure Veri Kutunuz için İzleme ve etkinlik günlüğe kaydetme'ye](data-box-logs.md)gidin.

## <a name="security-features"></a>Güvenlik özellikleri

Data Box, verilerinizi yalnızca yetkili varlıkların görüntüleyebilmesini, değiştirebilmesini veya silebilmesini sağlayarak veri koruma için güvenli çözüm getirir. Bu çözümle ilgili güvenlik özellikleri hem diske hem de hizmete yöneliktir ve burada depolanan verilerin güvenliğini sağlar. 

### <a name="data-box-device-protection"></a>Data Box cihazı koruması

Data Box cihazı aşağıdaki özelliklerle korunur:

- Şok, elverişsiz aktarım ve çevresel şartlara dayanıklı sağlam bir cihaz kasası. 
- Taşıma sırasında gerçekleşen kurcalamaları gösteren kurcalama karşıtı mühürler.
- Cihazın işlemesini engelleyen donanım ve yazılım kurcalama algılaması.
- Yalnızca Data Box’a özgün yazılım çalıştırır.
- Kilitli bir durumda önyüklenir.
- Cihaz erişimi, cihaz kilit açma şifresi ile kontrol edilir.
- Cihaz içine ve cihazdan dışarı veri kopyalamak için erişim kimlik bilgileri. Azure portalındaki **Aygıt kimlik bilgileri** sayfasına tüm erişim etkinlik [günlüklerinde](data-box-logs.md#query-activity-logs-during-setup)günlüğe kaydedilir.

### <a name="data-box-data-protection"></a>Data Box veri koruması

Data Box içine ve dışına akıtılan veriler şu özelliklerle korunur:

- Bekleyen veriler için 256 bit AES şifreleme.
- Kullanım halindeki veriler için şifrelenmiş protokoller kullanılabilir.
- Azure’a yapılan yüklenme tamamlandıktan sonra cihazdaki verilerin güvenli bir şekilde silinmesi. Veri silme, [NIST 800-88r1 standartlarında ATA Sabit Disk Sürücüleri için Ek A'daki](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-88r1.pdf)kurallara uygundur. Veri silme olayı [sipariş geçmişine](data-box-logs.md#download-order-history)kaydedilir.

### <a name="data-box-service-protection"></a>Data Box hizmeti koruması

Data Box hizmeti aşağıdaki özelliklerle korunur.

- Data Box hizmetine erişim için kuruluşunuzun Data Box içeren bir Azure aboneliği olmalıdır. Aboneliğiniz, Azure portalında erişebildiğiniz özellikleri yönetir.
- Data Box hizmeti Azure’da barındırıldığından, Azure güvenlik özellikleriyle korunur. Microsoft Azure tarafından sağlanan güvenlik özellikleri hakkında daha fazla bilgi için [Microsoft Azure Güven Merkezi](https://www.microsoft.com/TrustCenter/Security/default.aspx)’ne gidin.
- Veri Kutusu siparişine erişim, Rol tabanlı Erişim Denetimi (RBAC) rollerinin kullanımı yla denetlenebilir. Daha fazla bilgi için [bkz.](data-box-logs.md#set-up-access-control-on-the-order)
- Data Box hizmeti, hizmette cihazın kilidini açmak için kullanılan kilit açma şifrelerini saklar.
- Data Box hizmeti, hizmetteki sipariş ayrıntılarını ve durumunu depolar. Sipariş silindiğinde bu bilgiler de silinir.

## <a name="managing-personal-data"></a>Kişisel verileri yönetme

Azure Data Box, hizmette şu anahtar örneklerinde kişisel bilgileri toplar ve görüntüler:

- **Bildirim ayarları** - Sipariş oluşturduğunuzda, bildirim ayarlarının altında kullanıcıların e-posta adreslerini yapılandırırsınız. Bu bilgiler yönetici tarafından görüntülenebilir. Bu bilgiler, iş terminal duruman ulaştığında veya siparişi sildiğinizde hizmet tarafından silinir.

- **Sipariş ayrıntıları** – Sipariş oluşturulduktan sonra, kullanıcının teslimat adresi, e-postası, kişi bilgileri Azure portalında depolanır. Kaydedilen bilgiler:

  - Kişi adı
  - Telefon numarası
  - Email
  - Açık adres
  - Şehir
  - Posta kodu
  - Durum
  - Ülke/İl/Bölge
  - Taşıyıcı hesap numarası
  - Kargo takip numarası

    Sipariş ayrıntıları, iş tamamlandığında veya siz siparişi sildiğinizde Data Box hizmeti tarafından silinir.

- **Teslimat adresi** – Sipariş verildikten sonra, Data Box hizmeti UPS veya DHL gibi üçüncü taraf taşıyıcılara teslimat adresini verir. 

Daha fazla bilgi için, [Güven Merkezi](https://www.microsoft.com/trustcenter)’nde Microsoft Gizlilik ilkesini gözden geçirin.


## <a name="security-guidelines-reference"></a>Güvenlik yönergeleri başvurusu

Data Box’da aşağıdaki güvenlik yönergeleri uygulanmıştır: 

|Yönerge   |Açıklama   |
|---------|---------|
|[IEC 60529 IP52](https://www.iec.ch/)    | Su ve toz koruması için         |
|[ISTA 2A](https://ista.org/docs/2Aoverview.pdf)     | Olumsuz taşıma koşullarına dayanıklılık için          |
|[NIST SP 800-147](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-147.pdf)      | Güvenli cihaz yazılımı güncelleştirmesi için         |
|[FIPS 140-2 Level 2](https://csrc.nist.gov/csrc/media/publications/fips/140/2/final/documents/fips1402.pdf)      | Veri koruma için         |
|Ek A, [NIST SP 800-88r1](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-88r1.pdf) ATA Sabit Disk Sürücüler için      | Veri temizleme için         |

## <a name="next-steps"></a>Sonraki adımlar

- [Data Box gereksinimlerini](data-box-system-requirements.md) gözden geçirin.
- [Data Box sınırlarını](data-box-limits.md) anlayın.
- Azure portalda [Azure Data Box](data-box-quickstart-portal.md)’u hızla dağıtın.
