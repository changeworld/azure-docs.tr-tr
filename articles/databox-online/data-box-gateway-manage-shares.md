---
title: Azure Data Box Gateway paylaşımlarını yönetme | Microsoft Docs
description: Azure portalı kullanarak Azure Data Box Gateway paylaşımlarını yönetme adımları.
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: article
ms.date: 03/25/2019
ms.author: alkohli
ms.openlocfilehash: e5463a32e299d9d4d151049ab5afffd4975d5182
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79265447"
---
# <a name="use-the-azure-portal-to-manage-shares-on-your-azure-data-box-gateway"></a>Azure portalı kullanarak Azure Data Box Gateway paylaşımlarını yönetme 

Bu makalede Azure Data Box Gateway paylaşımlarını yönetme adımları açıklanmaktadır. Azure Data Box Gateway’i Azure portal veya yerel web kullanıcı arabirimiyle yönetebilirsiniz. Paylaşım ekleme, silme, yenileme veya paylaşımlarla ilişkili depolama hesabının depolama anahtarını eşitleme işlemleri için Azure portalı kullanın.

## <a name="about-shares"></a>Paylaşımlar hakkında

Azure'a veri aktarmak için Azure Data Box Gateway'inizde paylaşımlar oluşturmanız gerekir. Data Box Gateway cihazına eklediğiniz paylaşımlar, bulut paylaşımlarıdır. Bu paylaşımlardaki veriler otomatik olarak buluta yüklenir. Bu paylaşımlarda Yenile ve Depolama anahtarlarını eşitle gibi tüm bulut işlevleri kullanılabilir. Cihaz verilerinin buluttaki depolama hesabınıza otomatik olarak gönderilmesini istiyorsanız bulut paylaşımlarını kullanın.

Bu makalede şunları öğreneceksiniz:

> [!div class="checklist"]
> * Paylaşım ekleme
> * Paylaşımı silme
> * Paylaşımları yenileme
> * Depolama anahtarını eşitleme


## <a name="add-a-share"></a>Paylaşım ekleme

Paylaşım oluşturmak için Azure portalda aşağıdaki adımları gerçekleştirin.

1. Data Box Gateway kaynağınızın Azure portal sayfasında **Genel bakış** bölümüne gidin. Komut çubuğunda **+ Paylaşım ekle**'ye tıklayın.
2. **Paylaşım Ekle**'de, paylaşım ayarlarını belirtin. Paylaşımınız için benzersiz bir ad sağlayın.

    ![Paylaşım ekle'ye tıklayın](media/data-box-gateway-manage-shares/add-share-1.png)

    Paylaşım adları yalnızca rakam, küçük harf ve kısa çizgiler içerebilir. Paylaşım adı 3 ile 63 karakter arası uzunlukta olmalı ve bir harf veya rakamla başlamalıdır. Her kısa çizginin önünde ve arkasında kısa çizgi dışında bir karakter bulunmalıdır.

3. Paylaşım için **Tür** seçin. Tür **SMB** veya **NFS**olabilir, SMB varsayılan olarak. SMB Windows istemcilerinin standardıdır ve NFS de Linux istemcilerinde kullanılır. SMB paylaşımları mı yoksa NFS paylaşımları mı seçtiğinize bağlı olarak, gösterilen seçenekler biraz farklı olur.

4. Paylaşımın duracağı **Depolama hesabını** sağlamanız gerekir. Henüz kapsayıcı yoksa, depolama hesabında paylaşım adıyla bir kapsayıcı oluşturulur. Kapsayıcı zaten varsa, bu var olan kapsayıcı kullanılır.  

5. Blok blobundan, sayfa blobundan veya dosyadan **Depolama hizmeti**'ni seçin. Seçilen hizmetin türü, verilerin Azure'da hangi biçimde tutulmasını istediğinize bağlıdır. Örneğin, bu durumda, verilerin Azure'da blob blokları olarak yer alabilmesini istiyoruz, bu nedenle **Blob'u engelle'yi**seçiyoruz. **Sayfa Blobunu** seçerseniz, verilerinizi 512 bayt hizalı olduğundan emin olmalısınız. Örneğin VHDX her zaman 512 bayt hizalıdır.

   > [!IMPORTANT]
   > Kullandığınız Azure Depolama hesabının üzerinde bir Azure Yığını Kenarı veya Veri Kutusu Ağ Geçidi aygıtıyla kullanıyorsanız, üzerinde geçici çözümegeçirim ilkeleri olmadığından emin olun. Daha fazla bilgi [için, blob depolama için değişmezlik ilkelerini ayarla ve yönetin.](https://docs.microsoft.com/azure/storage/blobs/storage-blob-immutability-policies-manage)

6. Bu adım SMB paylaşımı mı yoksa NFS paylaşımı mı oluşturduğunuza bağlıdır.
    - **Bir Kobİ payı oluşturuyorsanız** - **Tüm ayrıcalık yerel kullanıcı** alanında, yeni **oluştur** veya **varolan ı kullan'dan**birini seçin. Yeni bir yerel kullanıcı oluşturuyorsanız, **kullanıcı adı,** **parolayı**girin ve ardından parolayı onaylayın. Bu, yerel kullanıcıya izinleri atar. Burada izinleri atadıktan sonra, Dosya Gezgini'ni kullanarak bu izinlerde değişiklik yapabilirsiniz.

        ![SMB paylaşımı ekleme](media/data-box-gateway-manage-shares/add-share-2.png)

        Bu paylaşımın verileri için Yalnızca okuma işlemlerine izin ver'i işaretlerseniz, salt okuma kullanıcıları belirtebilirsiniz.
    - **Bir NFS payı oluşturuyorsanız** - Paylaşıma erişebilen **izin verilen istemcilerin IP adreslerini** sağlamanız gerekir.

        ![NFS paylaşımı ekleme](media/data-box-gateway-manage-shares/add-share-3.png)

7. Paylaşımı oluşturmak için **Oluştur**'a tıklayın. Paylaşım oluşturma işleminin devam ettiği size bildirilir. Paylaşım belirtilen ayarlarla oluşturulduktan sonra, **Paylaşımlar** dikey penceresi yeni paylaşımı yansıtacak şekilde güncelleştirilir.
 
## <a name="delete-a-share"></a>Paylaşımı silme

Paylaşımı silmek için Azure portalda aşağıdaki adımları gerçekleştirin.

1. Paylaşım listesinde silmek istediğiniz paylaşımı seçin ve üzerine tıklayın.

    ![Paylaşımı seçme](media/data-box-gateway-manage-shares/delete-1.png)

2. **Sil'i**tıklatın. 

    ![Sil'e tıklayın](media/data-box-gateway-manage-shares/delete-2.png)

3. Onayınız istendiğinde **Evet**’e tıklayın.

    ![Silmeyi onayla](media/data-box-gateway-manage-shares/delete-3.png)

Paylaşım listesi silinen paylaşıma göre güncelleştirilir.


## <a name="refresh-shares"></a>Paylaşımları yenileme

Yenileme özelliği, şirket içi paylaşımın içeriğini yenilemenizi sağlar. Bir paylaşımı yenilediğinizde bloblar ve dosyalar dahil olmak üzere son yenileme işleminden sonra buluta eklenmiş olan tüm Azure nesnelerini bulmak için bir arama başlatılır. Ardından bu ek dosyalar kullanılarak cihazdaki şirket içi paylaşımın içeriği yenilenir. 

> [!NOTE]
> İzinler ve erişim denetim listeleri (ALA)'lar bir yenileme işlemi boyunca korunmaz. 

Paylaşımı yenilemek için Azure portalda aşağıdaki adımları gerçekleştirin.

1.   Azure portalda **Paylaşımlar** sayfasına gidin. Yenilemek istediğiniz paylaşımı seçin ve üzerine tıklayın.

    ![Paylaşımı seçme](media/data-box-gateway-manage-shares/refresh-1.png)

2.   **Yenile**'ye tıklayın. 

    ![Yenile'ye tıklayın](media/data-box-gateway-manage-shares/refresh-2.png)
 
3.   Onayınız istendiğinde **Evet**’e tıklayın. Şirket içi paylaşımın içeriğinin yenilenmesi için bir iş başlatılır. 

    ![Yenilemeyi onaylama](media/data-box-gateway-manage-shares/refresh-3.png)
 
4.   Yenileme işlemi devam ederken bağlam menüsündeki Yenile seçeneği gri renkte gösterilir. Yenileme işinin durumunu görüntülemek için iş bildirimine tıklayın.

5.   Yenileme süresi Azure kapsayıcısındaki ve cihazdaki dosya sayısına göre değişir. Yenileme işlemi başarıyla tamamlandıktan sonra paylaşım zaman damgası güncelleştirilir. Yenilemede kısmi hatalar olsa da işlem başarılı kabul edilir ve zaman damgası güncelleştirilir. 

    ![Güncelleştirilmiş zaman damgası](media/data-box-gateway-manage-shares/refresh-4.png)
 
Hata varsa bir uyarı görüntülenir. Uyarıda sorunun nedeni ve düzeltme adımları yer alır. Uyarıda ayrıca güncelleştirme veya silme işleminin başarısız olduğu dosyalar da dahil olmak üzere hatanın ayrıntılı bir özetinin yer aldığı bir dosyaya bağlantı da verilir.

>[!IMPORTANT]
> Bu sürümde, aynı anda tek bir hisseden fazlasını yenilemeyin.

## <a name="sync-storage-keys"></a>Depolama anahtarlarını eşitleme

Depolama anahtarlarınız değiştirildiyse eşitlemeniz gerekir. Eşitleme, cihazın depolama hesabınızın en son anahtarlarını almasına yardımcı olur.

Depolama erişim anahtarınızı eşitlemek için Azure portalda aşağıdaki adımları gerçekleştirin.

1. Kaynağınızın **Genel bakış** sayfasına gidin. 
2. Paylaşımlar listesinden, eşitlemeniz gereken depolama hesabıyla ilişkili bir paylaşım seçin ve tıklatın. **Depolama alanını eşitle'yi**tıklatın. 

     ![Depolama anahtarını eşitleme 1](media/data-box-gateway-manage-shares/sync-storage-key-1.png)

3. Onayınız istendiğinde **Evet**’e tıklayın. Eşitleme tamamlandıktan sonra iletişim kutusunu kapatın.

     ![Depolama anahtarını eşitleme 1](media/data-box-gateway-manage-shares/sync-storage-key-2.png)

>[!NOTE]
> Bu işlemi her depolama hesabı için yalnızca bir kez yapmanız gerekir. Tüm paylaşımlar aynı depolama hesabına aitse bu eylemi tüm paylaşımlar için tekrarlamanız gerekmez.


## <a name="next-steps"></a>Sonraki adımlar

- [Azure portal aracılığıyla kullanıcıları yönetme](data-box-gateway-manage-users.md) hakkında bilgi edinin.
