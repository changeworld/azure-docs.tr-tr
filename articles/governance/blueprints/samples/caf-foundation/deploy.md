---
title: CAF Vakfı plan örneğini dağıtın
description: Plan yapı parametresi ayrıntılarını içeren CAF Foundation plan örneği için adımları dağıtın.
ms.date: 08/20/2019
ms.topic: sample
ms.openlocfilehash: df17f8c3f539e25635ea4718be9d51d5e5e3f708
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/24/2020
ms.locfileid: "74545512"
---
# <a name="deploy-the-microsoft-cloud-adoption-framework-for-azure-foundation-blueprint-sample"></a>Azure Vakfı plan örneği için Microsoft Bulut Benimseme Çerçevesi'ni dağıtma

Azure (CAF) Vakfı için Microsoft Bulut Benimseme Çerçevesi'ni dağıtmak için aşağıdaki adımların atılması gerekir:

> [!div class="checklist"]
> - Örnekten yeni bir plan oluşturma
> - Örnek kopyanızı **Yayımlanmış** olarak işaretleme
> - Planın kopyasını varolan bir aboneye atama

Azure aboneliğiniz yoksa, başlamadan önce [ücretsiz](https://azure.microsoft.com/free) bir hesap oluşturun.

## <a name="create-blueprint-from-sample"></a>Örnekten plan oluşturma

İlk olarak, bir başlangıç olarak örnek kullanarak ortamınızda yeni bir plan oluşturarak plan örneği uygulayın.

1. Sol bölmede **Tüm hizmetler**'i seçin. **Planları**arayın ve seçin.

1. Soldaki **Başlat'tan plan** _Oluştur'un_altındaki **Oluştur** düğmesini seçin.

1. _Diğer Örnekler_ altında **CAF Vakfı** plan örneğini bulun ve bu **örneği kullanın'ı**seçin.

1. Plan örneğinin _Temellerini_ girin:

   - **Plan adı**: CAF Foundation plan örneğinin kopyası için bir ad verin.
   - **Tanım yeri**: Elipsleri kullanın ve numunenin kopyasını kaydetmek için yönetim grubunu seçin.

1. Sayfanın üst kısmındaki _Eserler_ sekmesini veya **Sonraki: Sayfanın** altındaki Eserler sekmesini seçin.

1. Plan örneğini oluşturan yapıtların listesini gözden geçirin. Yapıtların çoğunun daha sonra tanımlayabildiğimiz parametreleri var. Plan örneğini gözden geçirmeyi bitirdiğinizde **Taslağı Kaydet'i** seçin.

## <a name="publish-the-sample-copy"></a>Örnek kopyayı yayımla

Plan örneğinin kopyası artık ortamınızda oluşturuldu. **Taslak** modunda oluşturulur ve atanıp dağıtılmadan önce **yayımlanmalıdır.** Plan örneğinin kopyası ortamınıza ve gereksinimlerinize göre özelleştirilebilir, ancak bu değişiklik onu CAF Foundation planından uzaklaştırabilir.

1. Sol bölmede **Tüm hizmetler**'i seçin. **Planları**arayın ve seçin.

1. Soldaki **Blueprint tanımları** sayfasını seçin. Plan örneğinin kopyasını bulmak ve sonra seçmek için filtreleri kullanın.

1. Sayfanın üst kısmında **ki planı Yayımla'yı** seçin. Sağdaki yeni sayfada, plan örneğinin kopyası için bir **Sürüm** sağlayın. Bu özellik, daha sonra bir değişiklik yaparsanız için yararlıdır. "CAF Foundation plan örneğinden yayınlanan ilk sürüm" gibi **Değişiklik notları** sağlayın. Ardından sayfanın altındaki **Yayımla'yı** seçin.

## <a name="assign-the-sample-copy"></a>Örnek kopyayı atama

Plan örneğinin kopyası başarıyla **Yayımlandıktan**sonra, kaydedildiği yönetim grubu içindeki bir aboneye atanabilir. Bu adım, plan örneğinin her kopyasını benzersiz hale getirmek için parametrelerin sağlandığı adımdır.

1. Sol bölmede **Tüm hizmetler**'i seçin. **Planları**arayın ve seçin.

1. Soldaki **Blueprint tanımları** sayfasını seçin. Plan örneğinin kopyasını bulmak ve sonra seçmek için filtreleri kullanın.

1. Plan tanımı sayfasının üst **kısmındaki plan atay'ı** seçin.

1. Plan ataması için parametre değerlerini sağlayın:

   - Temel Bilgiler
       - **Abonelikler**: Plan örneğinin kopyasını kaydettiğiniz yönetim grubundaki aboneliklerden birini veya birkaçını seçin. Birden fazla abonelik seçerseniz, girilen parametreleri kullanarak her biri için bir atama oluşturulur.
     - **Atama adı**: Adı, planın adına göre sizin için önceden doldurulur.
       Gerektiği gibi değiştirin veya olduğu gibi ayrılın.
     - **Konum**: Oluşturulan yönetilen kimlik için bir bölge seçin.
     - Azure Blueprint bu yönetilen kimliği kullanarak tüm yapıtları atanmış şemaya dağıtır.
       Daha fazla bilgi için bkz. [Azure kaynakları için yönetilen kimlikler](../../../../active-directory/managed-identities-azure-resources/overview.md).
     - **Blueprint tanımı sürümü**: Plan örneğinin kopyanızın **Yayınlanmış** sürümünü seçin.

   - Kilit Ataması

     Ortamınız için plan kilidi ayarını seçin. Daha fazla bilgi için bkz. [şema kaynağı kilitleme](../../concepts/resource-locking.md).

   - Yönetilen Kimlik

     Yönetilen kimlik seçeneğiatanan varsayılan _sistemi_ veya _atanan kimlik_ seçeneğini seçin.

   - Şema parametreleri

     Bu bölümde tanımlanan parametreler tutarlılık sağlamak için plan tanımındaki birçok yapıt tarafından kullanılır.

     - **Organizasyon**: Contoso gibi kuruluş adınızı girin, benzersiz olmalıdır.
     - **Azure Bölgesi**: Dağıtım için Azure Bölgesini seçin.
     - **İzin verilen konumlar**: Hangi Azure Bölgelerinde kaynakların oluşturulmasına izin verirsiniz?
     
   - Yapı parametreleri

     Bu bölümde tanımlanan parametreler, tanımlandığı yapı için geçerlidir. Bu parametreler, planın atanması sırasında tanımlandıkları için [dinamik parametrelerdir.](../../concepts/parameters.md#dynamic-parameters) Tam liste veya yapı parametreleri ve açıklamaları için [Yapı parametreleri tablosuna](#artifact-parameters-table)bakın.

1. Tüm parametreler girildikten sonra, sayfanın alt kısmında **Ata'yı** seçin. Plan ataması oluşturulur ve artefakt dağıtımı başlar. Dağıtım yaklaşık bir saat sürer. Dağıtım durumunu denetlemek için plan atamasını açın.

> [!WARNING]
> Azure Planları hizmeti ve yerleşik plan örnekleri **ücretsizdir.** Azure kaynakları [ürüne göre fiyatlandırılır.](https://azure.microsoft.com/pricing/) Bu plan örneği tarafından dağıtılan kaynakların çalışma maliyetini tahmin etmek için [fiyatlandırma hesaplayıcısını](https://azure.microsoft.com/pricing/calculator/) kullanın.

## <a name="artifact-parameters-table"></a>Yapı parametreleri tablosu

Aşağıdaki tablo, plan yapı parametrelerinin bir listesini sağlar:

|Artefakt adı|Artefakt türü|Parametre adı|Açıklama|
|-|-|-|-|
|İzin verilen depolama hesabı SKU'ları|İlke ataması|Policy_Allowed-SaklamaHesabı-SKUs|Tanılama Günlüğü depolama hesaplarında kullanılan SKU|
|İzin verilen sanal makine SUS'lar|İlke ataması|Policy_Allowed-VM-SKUs|İzin verilen sanal makine SUS'lar|
|Kaynak Gruplarına CostCenter TAG ekle|İlke ataması|Policy_CostCenter_Tag|CostCenter TAG'i ve değerini Kaynak Grubundan ekle|
|Ortamınızda izin vermek istemediğiniz Kaynak Türleri|İlke ataması|İlke _Allowed-Kaynak Türleri|Ortamınızda hangi Azure Kaynaklarına izin vermek istiyorsunuz?|
|Anahtar Kasası Dağıt|Resource Manager şablonu|KV-AccessPolitikası|**Kilitli** - Azure AD <Object ID> Grubu veya Kullanıcı Key Vault izinleri vermek için|
|Günlük Analizini Dağıt|Resource Manager şablonu|LogAnalytics_DataRetention|**Kilitli** - Günlük Analizi'nde gün sayısı verileri korunacaktır|
|Günlük Analizini Dağıt|Resource Manager şablonu|LogAnalytics_Location|**Kilitli** - Çalışma alanı kurulurken kullanılan bölge|

## <a name="next-steps"></a>Sonraki adımlar

Şimdi CAF Vakfı plan örneği dağıtmak için adımları gözden geçirdim, mimari hakkında bilgi edinmek için aşağıdaki makaleyi ziyaret edin:

> [!div class="nextstepaction"]
> [CAF Vakfı planı - Genel Bakış](./index.md)

Şemalar ve bunların kullanımı hakkındaki diğer makaleler:

- [Plan yaşam döngüsü](../../concepts/lifecycle.md)hakkında bilgi edinin.
- [Statik ve dinamik parametrelerin](../../concepts/parameters.md) kullanımını anlayın.
- [Şema sıralama düzenini](../../concepts/sequencing-order.md) özelleştirmeyi öğrenin.
- [Şema kaynak kilitleme](../../concepts/resource-locking.md) özelliğini kullanmayı öğrenin.
- Varolan atamaları nasıl [güncelleştirini](../../how-to/update-existing-assignments.md)öğrenin.