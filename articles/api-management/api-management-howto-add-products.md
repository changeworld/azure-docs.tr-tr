---
title: Azure API Management'ta ürün oluşturma ve yayımlama
description: Azure API Management'ta ürün oluşturmayı ve yayımlamayı öğrenin.
services: api-management
documentationcenter: ''
author: mikebudzynski
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.custom: mvc
ms.topic: tutorial
ms.date: 08/10/2018
ms.author: apimpm
ms.openlocfilehash: 686d6e2ff01fd4d8283350ab8097e1f73c914af0
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/13/2020
ms.locfileid: "81261005"
---
# <a name="create-and-publish-a-product"></a>Ürün oluşturma ve yayımlama  

Azure API Management'ta, üründe bir veya birden çok API, ayrıca kullanım kotası ve kullanım koşulları bulunur. Ürün yayımlandığında, geliştiriciler ürüne abone olabilir ve ürünün API'lerini kullanmaya başlayabilir.  

Bu öğreticide şunların nasıl yapıldığını öğrenirsiniz:

> [!div class="checklist"]
> * Ürün oluşturma ve yayımlama
> * Ürüne bir API ekleme

![Ürün öğreticisi ekleme](media/api-management-howto-add-products/added-product.png)

## <a name="prerequisites"></a>Ön koşullar

+ [Azure API Management terminolojisini](api-management-terminology.md) öğrenin.
+ Şu hızlı başlangıcı tamamlayın: [Azure API Management örneği oluşturma](get-started-create-service-instance.md).
+ Ayrıca, şu öğreticiyi tamamlayın: [İlk API'nizi içeri aktarma ve yayımlama](import-and-publish.md).

## <a name="create-and-publish-a-product"></a>Ürün oluşturma ve yayımlama

![Ürün ekleme](media/api-management-howto-add-products/02-create-publish-product-01.png)

1. **Ürünler** sayfasını görüntülemek için soldaki menüden **Ürünler**’e tıklayın.
2. **+ Ekle**'ye tıklayın.

    Ürün eklerken aşağıdaki bilgileri sağlamanız gerekir: 

    | Adı                     | Açıklama                                                                                                                                                                                                                                                                                                             |
    |--------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
    | Görünen ad             | **Geliştirici portalında** gösterilmesini istediğiniz ad.                                                                                                                                                                                                                                                        |
    | Adı                     | Ürün için açıklayıcı bir ad.                                                                                                                                                                                                                                                                                      |
    | Açıklama              | **Açıklama** alanı, ürünün amacı ve hangi API’ler için erişim sağladığı gibi konularda ayrıntılı, kullanışlı bilgiler sağlamanıza imkan tanır.                                                                                                                                               |
    | Durum                    | Ürünü yayımlamak istiyorsanız **Yayımlandı** seçeneğine basın. Bir üründeki API'lerin çağrılabilmesi için önce ürünün yayımlanması gerekir. Varsayılan olarak, yeni ürünler yayımlanmış durumda değildir ve yalnızca **Yöneticiler** grubundaki kullanıcılar tarafından görülebilir.                                                                                      |
    | Abonelik gerektirir    | Kullanıcının ürünü kullanmak için aboneliğe sahip olması gerekiyorsa **Abonelik iste**'yi işaretleyin.                                                                                                                                                                                                                                   |
    | Onay gerekiyor        | Yöneticinin bu ürüne yönelik abonelik girişimlerini gözden geçirip kabul etmesini ya da reddetmesini istiyorsanız **Onay iste** seçeneğini işaretleyin. Kutu işaretsiz bırakılırsa abonelik girişimleri otomatik olarak onaylanır.                                                                                                                         |
    | Abonelik sayısı limiti | Aynı anda sahip olunabilecek abonelik sayısını kısıtlamak için abonelik limitini girin.                                                                                                                                                                                                                                |
    | Yasal koşullar              | Abonelerin ürünü kullanmak için kabul etmek zorunda olduğu ürün kullanım koşullarını ekleyebilirsiniz.                                                                                                                                                                                                             |
    | API'ler                     | Ürünler bir veya daha fazla API arasındaki ilişkilendirmelerdir. Bir dizi API ekleyebilir ve geliştirici portalı aracılığıyla geliştiricilere sunabilirsiniz. <br/> Ürün oluşturma sırasında mevcut bir API’yi ekleyebilirsiniz. Ürüne daha sonra, Ürünün **Ayarlar** sayfasından veya bir API oluştururken bir API ekleyebilirsiniz. |

3. Yeni ürünü oluşturmak için **Oluştur**’a tıklayın.

### <a name="add-more-configurations"></a>Daha fazla yapılandırma ekleme

Ürünü kaydettikten sonra **Ayarlar** sekmesini seçerek yapılandırmaya devam edebilirsiniz. 

**Abonelikler** sekmesinden aboneleri görüntüleyin/ürüne abone ekleyin.

**Access denetim** sekmesinden geliştiriciler veya misafirler için bir ürünün görünürlüğünü ayarlayın.

## <a name="add-apis-to-a-product"></a><a name="add-apis"> </a>Bir ürüne API ekleme

Ürünler bir veya daha fazla API arasındaki ilişkilendirmelerdir. Bir dizi API ekleyebilir ve geliştirici portalı aracılığıyla geliştiricilere sunabilirsiniz. Ürün oluşturma sırasında mevcut bir API’yi ekleyebilirsiniz. Ürüne daha sonra Ürün **Ayarları** sayfasından veya bir API oluşturduğunuz sırada API ekleyebilirsiniz.

Geliştiricilerin bir API’ye erişebilmesi için önce ürüne abone olması gerekir. Abone olduklarında, ilgili üründeki tüm API’ler için geçerli olan bir abonelik anahtarı edinirler. APIM örneğini siz oluşturduysanız zaten bir yöneticisinizdir ve varsayılan olarak tüm ürünlere abone olmuşsunuz demektir.

### <a name="add-an-api-to-an-existing-product"></a>Mevcut bir ürüne API ekleme

![ürün ekleme API'si](media/api-management-howto-add-products/02-create-publish-product-02.png)

1. **Ürünler** sekmesinden bir ürün seçin.
2. **API'ler** sekmesine gidin.
3. **+ Ekle**'ye tıklayın.
4. Bir API seçip **Seç**’e tıklayın.

> [!TIP]
> [REST API](https://docs.microsoft.com/rest/api/apimanagement/2019-12-01/subscription/createorupdate) veya PowerShell komutu aracılığıyla özel abonelik anahtarları kullanarak kullanıcının *Ürün* aboneliğini oluşturabilir veya güncelleştirebilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide, şunların nasıl yapıldığını öğrendiniz:

> [!div class="checklist"]
> * Ürün oluşturma ve yayımlama
> * Ürüne bir API ekleme

Sonraki öğreticiye ilerleyin:

> [!div class="nextstepaction"]
> [Boş bir API ve sahte API yanıtları oluşturma](mock-api-responses.md)
