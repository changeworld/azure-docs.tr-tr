---
title: Microsoft Azure Data Box Hakkında SSS | Microsoft Docs
description: Büyük miktarda veriyi Azure'a aktarmanızı sağlayan bir bulut çözümü olan Azure Veri Kutusu için sık sorulan sorular ve yanıtlar içerir.
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: article
ms.date: 09/03/2019
ms.author: alkohli
ms.openlocfilehash: 73256aef19a03c4c971be5fc9e69f988ef5a831a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75438623"
---
# <a name="azure-data-box-frequently-asked-questions"></a>Azure Data Box: Sık Sorulan Sorular

Microsoft Azure Data Box hibrit çözümü, bir aktarım cihazı kullanarak terabaytlarca veriyi Azure'a hızlı, uygun maliyetli ve güvenilir bir şekilde göndermenizi sağlar. Bu SSS belgesinde Azure portaldaki Data Box kullanımınızla ilgili sorulara ve yanıtlarına yer verilmiştir. 

Sorular ve yanıtlar aşağıdaki kategorilere ayrılmıştır:

- Hizmet hakkında
- Cihaz sipariş etme
- Yapılandırma ve bağlanma 
- Durumu izleme
- Veri kopyalama 
- Cihaz gönderme
- Verileri doğrulama ve yükleme 
- Koruma zinciri desteği

## <a name="about-the-service"></a>Hizmet hakkında

### <a name="q-what-is-azure-data-box-service"></a>S. Azure Data Box hizmeti nedir? 
A.  Azure Data Box hizmeti, çevrimdışı veri alımı için tasarlanmıştır. Bu hizmet tamamı veri aktarımı için tasarlanmış olan farklı depolama kapasitelerine sahip ürün dizisini yönetir. 

### <a name="q-what-is-azure-data-box"></a>S. Azure Data Box nedir?
A. Azure Veri Kutusu, terabaytlarca verinin Azure'a hızlı, ucuz ve güvenli bir şekilde aktarılmasına olanak tanır. Data Box cihazını Azure portalı üzerinden sipariş edersiniz. Microsoft, bölgesel bir taşıyıcı aracılığıyla size 80 TB kullanılabilir kapasiteye sahip bir depolama aygıtı göndererek gönderebilirsiniz. 

Cihaz size ulaştıktan sonra, yerel web kullanıcı arabirimini kullanarak cihazı hızla ayarlayabilirsiniz. Sunucularınızdaki verileri cihaza kopyalayın ve cihazı Azure'a geri gönderin. Azure veri merkezinde, verileriniz cihazdan Azure'a otomatik olarak yüklenir. Sürecin tamamı Azure portalındaki Data Box hizmeti tarafından uçtan uca izlenir.

### <a name="q-when-should-i-use-data-box"></a>S. Data Box'ı ne zaman kullanmalıyım?
A. Azure'a aktarmak istediğiniz 40-500 TB arası veriniz varsa Data Box'tan faydalanabilirsiniz. 40 TB'< veri boyutları için, Veri Kutusu Diski kullanın ve 500 TB > veri boyutları [için, Data Box Heavy'ye](data-box-heavy-overview.md)kaydolun.

### <a name="q-what-is-the-price-of-data-box"></a>S. Data Box'ın maliyeti nedir?
A. Data Box için 10 gün boyunca nominal bir ücret tahsil edilir. Azure portalda sipariş oluştururken ürün modelini seçme aşamasında cihaz ücretleri görüntülenir. Sevkiyat da ücretsizdir ancak Azure depolama ücretleri tahsil edilecektir. Daha fazla bilgi için bkz. [Azure Data Box fiyatlandırması](https://azure.microsoft.com/pricing/details/storage/databox/). 

### <a name="q-what-is-the-maximum-amount-of-data-i-can-transfer-with-data-box-in-one-instance"></a>S. Data Box ile tek seferde en fazla ne kadar veri aktarabilirim?
A. Data Box toplamda 100 TB, kullanılabilir alan olarak ise 80 TB kapasiteye sahiptir. Data Box ile 80 TB'a kadar veri aktarabilirsiniz. Daha fazla veri aktarmak için daha fazla cihaz sipariş etmeniz gerekir.

### <a name="q-how-can-i-check-if-data-box-is-available-in-my-region"></a>S. Data Box'ın bulunduğum bölgede kullanılabilir durumda olup olmadığını nasıl kontrol edebilirim? 
A.  Veri Kutusu'nun hangi ülkeler/bölgeler için kullanılabilir olduğu hakkında bilgi için [bölge kullanılabilirliğine](data-box-overview.md#region-availability)gidin.  

### <a name="q-which-regions-can-i-store-data-in-with-data-box"></a>S. Data Box ile hangi bölgelerde veri depolayabilirim?
A. Veri Kutusu ABD, Batı Avrupa, Kuzey Avrupa, Fransa, İngiltere, Japonya, Avustralya ve Kanada'daki tüm bölgeler için desteklenir. Daha fazla bilgi için [Bölge kullanılabilirliğine](data-box-overview.md#region-availability)gidin.

### <a name="q-whom-should-i-contact-if-i-encounter-any-issues-with-data-box"></a>S. Data Box ile ilgili sorun yaşamam halinde kiminle iletişim kurmam gerekir?
A. Data Box ile ilgili sorun yaşamanız halinde lütfen [Microsoft Desteği ile iletişime geçin](data-box-disk-contact-microsoft-support.md).

### <a name="q-i-have-lost-my-data-box-is-there-a-lost-device-charge"></a>S. Veri Kutumu kaybettim. Kayıp bir cihaz şarjı var mı?
A. Evet. Kayıp veya hasarlı bir cihaz şarjı vardır. Bu [ücret, Fiyatlandırma sayfasında](https://azure.microsoft.com/pricing/details/storage/databox/) ve Ürün [Hizmet Şartlarında](https://www.microsoft.com/licensing/product-licensing/products)karşılanmaktadır.


## <a name="order-device"></a>Cihaz sipariş etme

### <a name="q-how-do-i-get-data-box"></a>S. Data Box'ı nasıl edinebilirim? 
A.  Azure Data Box'ı almak için Azure portalda oturum açın ve bir Data Box siparişi oluşturun. İletişim bilgilerinizi ve bildirim ayrıntılarını girin. Sipariş verdikten sonra kullanılabilirlik durumuna göre Data Box 10 gün içinde gönderilir. Daha fazla bilgi için bkz. [Data Box sipariş etme](data-box-deploy-ordered.md).

### <a name="q-i-was-not-able-to-create-a-data-box-order-in-the-azure-portal-why-would-this-be"></a>S. Azure portalda Data Box siparişi oluşturamadım. Neden olabilir?
A. Data Box siparişi oluşturamadıysanız abonelik türünüz veya erişimle ilgili bir sorun olabilir. 

Aboneliğinizi kontrol edin. Data Box yalnızca Kurumsal Anlaşma (EA) ve Bulut çözüm sağlayıcısı (CSP) aboneliği teklifleriyle kullanılabilir. Aboneliğiniz bunlardan biri değilse Microsoft Desteği ile iletişime geçerek aboneliğinizi yükseltin.

Aboneliğiniz, desteklenen teklif türlerinden birine aitse abonelik erişim düzeyinizi kontrol edin. Sipariş oluşturma için aboneliğinizde katkıda bulunan veya sahip olmanız gerekir.

### <a name="q-i-ordered-a-couple-of-data-box-devices-i-am-not-able-to-create-any-additional-orders-why-would-this-be"></a>S. Birkaç Data Box cihazı sipariş ettim. Başka sipariş oluşturamıyorum. Neden olabilir?
A. Ticari sınır başına (seçilen ülke ve bölge birleşimi) her abonelik için en fazla beş etkin siparişe izin veriyoruz. Daha fazla cihaz sipariş etmeniz gerekiyorsa Microsoft Desteği ile iletişime geçerek aboneliğinizin sınırını artırabilirsiniz.

### <a name="q-when-i-try-to-create-an-order-i-receive-a-notification-that-the-data-box-service-is-not-available-what-does-this-mean"></a>S. Sipariş oluşturmaya çalıştığımda Data Box hizmetinin kullanılabilir durumda olmadığını belirten bir bildirim alıyorum. Bu ne anlama geliyor?
A. Bu ifade Data Box hizmetinin seçtiğiniz ülke ve bölge birleşimi için kullanılabilir durumda olmadığını gösterir. Bu birleşimi değiştirmeniz durumunda muhtemelen Data Box hizmetinden faydalanabileceksiniz. Hizmetin kullanılabildiği bölgelerin listesi için bkz. [Data Box için bölge kullanılabilirliği](data-box-overview.md#region-availability).

### <a name="q-i-placed-my-data-box-order-few-days-back-when-will-i-receive-my-data-box"></a>S. Data Box siparişimi vereli birkaç gün oldu. Data Box cihazım ne zaman gelecek?
A. Sipariş verdiğinizde uygun bir cihazın olup olmadığı kontrol edilir. Cihaz varsa 10 gün içinde gönderilir. Talebin yüksek olduğu dönemlerle de karşılaşabilirsiniz. Bu durumda siparişiniz sıraya alınır ve durumunu Azure portaldan takip edebilirsiniz. Siparişiniz 90 gün içinde işleme alınmazsa otomatik olarak iptal edilir.

### <a name="q-i-have-filled-up-my-data-box-with-data-and-need-to-order-another-one-is-there-a-way-to-quickly-place-the-order"></a>S. Data Box cihazıma veri yükledim ve yeni bir tane daha sipariş etmem gerekiyor. Siparişi hızlı bir şekilde vermek için kullanabileceğim bir yöntem var mı?
A. Önceki siparişinizi kopyalayabilirsiniz. Kopyalama işlemi, bir öncekiyle aynı bilgilere sahip bir sipariş oluşturur ve adres, iletişim ve bildirim ayrıntılarını yeniden girmenize gerek kalmadan yalnızca sipariş bilgilerini düzenleyebilirsiniz.

## <a name="configure-and-connect"></a>Yapılandırma ve bağlanma

### <a name="q-how-do-i-unlock-the-data-box"></a>S. Data Box cihazının kilidini nasıl açabilirim? 
A.  Azure portalda Data Box siparişinize ve **Cihaz ayrıntıları**'na gidin. Kilit açma parolasını kopyalayın. Bu parolayı kullanarak Data Box cihazınızın yerel web arabiriminde oturum açabilirsiniz. Daha fazla bilgi için bkz. [Öğretici: Azure Data Box'ın paketini açma, kablolarını takma ve bağlantılarını yapma](data-box-deploy-set-up.md).

### <a name="q-can-i-use-a-linux-host-computer-to-connect-and-copy-the-data-on-to-the-data-box"></a>S. Data Box'a bağlanmak ve veri kopyalamak için Linux ana bilgisayarı kullanabilir miyim?
A.  Evet. Data Box ile SMB ve NFS istemcilerine bağlanabilirsiniz. Daha fazla bilgi için ana bilgisayarınıza ilişkin [Desteklenen işletim sistemleri](data-box-system-requirements.md) listesine gidin.

### <a name="q-my-data-box-is-dispatched-but-now-i-want-to-cancel-this-order-why-is-the-cancel-button-not-available"></a>S. Data Box'ım yola çıktı ancak şimdi siparişimi iptal etmek istiyorum. Neden iptal düğmesi kullanılamıyor?
A.  Siparişi yalnızca Data Box siparişi verdikten sonra ancak sipariş işleme alınmadan önce iptal edebilirsiniz. Data Box siparişi işleme alındıktan sonra iptal edemezsiniz. 

### <a name="q-can-i-connect-a-data-box-at-the-same-to-multiple-host-computers-to-transfer-data"></a>S. Veri aktarımı için bir Data Box'ı aynı anda birden fazla ana bilgisayara bağlayabilir miyim?
A. Evet. Bir Data Box'a birden fazla ana bilgisayar bağlanarak aynı anda birden fazla veri aktarım ve kopyalama işi çalıştırılabilir. Daha fazla bilgi için bkz. [Öğretici: Azure Data Box'a veri kopyalama](data-box-deploy-copy-data.md).

### <a name="q-can-i-connect-to-both-the-10-gbe-interfaces-on-the-data-box-to-transfer-data"></a>S. Veri aktarmak için Veri Kutusu'ndaki 10 GbE arabirimlerine de bağlanabilir miyim?
A. Evet. Her iki 10 GbE arabirimleri aynı anda veri kopyalamak için Veri Kutusu'na bağlanabilir. Verilerin nasıl kopyalanılatırılabilenhakkında daha fazla bilgi için [Tutorial: Veri'yi Azure Veri Kutusu'na kopyalayın'](data-box-deploy-copy-data.md)a gidin.

<!--### Q. The network interface on my Data Box is not working. What should I do? 
A. 

### Q. I could not set up Data Box using a Dynamic (DHCP) IP address. Why would this be?
A.

### Q. I could not set up Data Box using a Static IP address. Why would this be?
A.

### Q. I could not set up Data Box on a private network. Why would this be?
A.-->

### <a name="q-the-system-fault-indicator-led-on-the-front-operating-panel-is-on-what-should-i-do"></a>S. Ön paneldeki sistem hatası göstergesi LED ışığı yanıyor. Ne yapmalıyım?
A. Sistem hatası göstergesi LED ışığının yanması sisteminizin iyi durumda olmadığını gösterir. Sonraki adımlar için [Microsoft Destek'e başvurun.](data-box-disk-contact-microsoft-support.md)

### <a name="q-i-cant-access-the-data-box-unlock-password-in-the-azure-portal-why-would-this-be"></a>S. Azure portalda Data Box kilit açma parolasına erişemiyorum. Neden olabilir?
A. Azure portalda kilit açma parolasına erişemiyorsanız aboneliğinizin ve depolama hesabınızın izinlerini kontrol edin. Kaynak grubu düzeyinde katkıda bulunan veya sahip izinlerine sahip olduğunuzdan emin olun. Değilse, erişim kimlik bilgilerini görmek için en azından Veri Kutusu Operatörü rol iznine sahip olmanız gerekir.

### <a name="q-is-port-channel-configuration-supported-on-data-box-how-about-mpio"></a>S. Veri Kutusu'nda bağlantı noktası kanalı yapılandırması desteklendi mi? MPIO'ya ne dersin?
A. Veri Kutusu'ndaki bağlantı noktası kanalı yapılandırması, Multipath IO (MPIO) yapılandırması veya vLAN yapılandırması desteklemeyiz.

## <a name="track-status"></a>Durumu izleme

### <a name="q-how-do-i-track-the-data-box-from-when-i-placed-the-order-to-shipping-the-device-back"></a>S. Data Box'ı geri göndermek için sipariş oluşturduktan sonra cihazı nasıl takip edebilirim? 
A.  Data Box siparişinin durumunu Azure portaldan takip edebilirsiniz. Siparişi oluşturduğunuzda bildirimlerin gönderileceği bir e-posta adresi girmeniz de istenir. E-posta adresi girdiyseniz sipariş durumundaki tüm değişiklikler bu adrese gönderilir. [Bilgilendirme e-postalarını yapılandırma](data-box-portal-ui-admin.md#edit-notification-details) hakkında daha fazla bilgi edinin.

### <a name="q-how-do-i-return-the-device"></a>S. Cihazı nasıl iade ederim? 
A.  Microsoft, E-mürekkep ekranında bir sevkiyat etiketi görüntüler. Sevkiyat etiketi E-mürekkep ekranında görünmüyorsa **Genel bakış > Sevkiyat etiketi indirme**'ye gidin. Etiketi indirin ve yazdırın, etiketi cihazın üzerindeki şeffaf plastik etikete takın ve cihazı sevkiyatı yapacak taşıyıcı şirkete bırakın. 

### <a name="q-i-received-an-email-notification-that-my-device-has-reached-the-azure-datacenter-how-do-i-find-out-if-the-data-upload-is-in-progress"></a>S. Cihazımın Azure veri merkezine ulaştığını bildiren bir e-posta bildirimi aldım. Veri yükleme işleminin ilerleme durumunu nasıl takip edebilirim?
A. Azure portalda Data Box siparişinize gidip **Genel bakış** sayfasını inceleyebilirsiniz. Azure'a veri yükleme işlemi başladıysa kopyalama ilerleme durumu sağ taraftaki bölmede gösterilir. 

## <a name="migrate-data"></a>Geçiş verileri

### <a name="q-what-is-the-maximum-data-size-that-can-be-used-with-data-box"></a>S. Data Box ile kullanılabilecek maksimum veri boyutu nedir?  
A.  Data Box'ın 80 TB kullanılabilir depolama kapasitesi vardır. 40 TB-80 TB arasındaki veriler için tek bir Data Box cihazı kullanabilirsiniz. 500 TB'a kadar daha büyük veri boyutları için birden çok Veri Kutusu aygıtı sipariş edebilirsiniz. 500 TB üzerindeki veriler için Data Box Heavy'ye kaydolun.  

### <a name="q-what-are-the-maximum-block-blob-and-page-blob-sizes-supported-by-data-box"></a>S. Data Box tarafından desteklenen maksimum blok blobu ve sayfa blobu boyutu nedir? 
A.  Maksimum boyutlar Azure Depolama sınırları ile belirlenir. Maksimum blok blobu yaklaşık 4,768 TiB, maksimum sayfa blobu boyutu ise 8 TiB olarak belirlenmiştir. Daha fazla bilgi için [Blob depolama için Ölçeklenebilirlik ve performans hedeflerine](../storage/blobs/scalability-targets.md)bakın.

### <a name="q-how-do-i-know-that-my-data-is-secure-during-transit"></a>S. Verilerimin aktarım sırasında güvende olduğundan nasıl emin olabilirim? 
A. Data Box'ınızın taşıma sırasındaki güvenliğini sağlamak için kullanılan birçok güvenlik özelliği vardır. Bunların bazıları kurcalamaları gösteren mühürler, donanım ve yazılım kurcalama algılaması ve cihaz kilidini açma parolasıdır. Daha fazla bilgi için bkz. [Azure Data Box güvenliği ve veri koruması](data-box-security.md).

### <a name="q-how-do-i-copy-the-data-to-the-data-box"></a>S. Data Box'a nasıl veri kopyalayabilirim? 
A.  SMB istemcisi kullanıyorsanız Robocopy, Diskboss veya Windows Dosya Gezgini gibi bir SMB kopyalama aracı kullanarak verileri sürükleyip cihaza bırakın. 

NFS istemcisi kullanıyorsanız [rsync](https://rsync.samba.org/), [FreeFileSync](https://www.freefilesync.org/), [Unison](https://www.cis.upenn.edu/~bcpierce/unison/) veya [Ultracopier](https://ultracopier.first-world.info/) araçlarından faydalanabilirsiniz. 

Daha fazla bilgi için bkz. [Öğretici: Azure Data Box'a veri kopyalama](data-box-deploy-copy-data.md).

### <a name="q-are-there-any-tips-to-speed-up-the-data-copy"></a>S. Veri kopyalama işlemini hızlandırmaya yönelik ipuçları var mı?
A.  Kopyalama işlemini hızlandırmak için:

- Birden fazla veri kopyalama akışı kullanın. Örneğin Robocopy'de çok iş parçacıklı seçeneği kullanın. Kullanılan komut hakkında daha fazla bilgi için [Öğretici: Azure Data Box'a veri kopyalama ve doğrulama](data-box-deploy-copy-data.md) sayfasına gidin.
- Birden fazla oturum kullanın.
- Ağ paylaşımı üzerinden kopyalama yapmak yerine (ağ hızları kısıtlayıcı olabilir) verilerin, Data Box'ın bağlı olduğu bilgisayarın yerel depolama alanında bulunduğundan emin olun.
- Veri kopyalamak için kullanılan bilgisayarın performansını karşılaştırın. Sunucu donanımının performansını karşılaştırmak için [Bluestop FIO aracını](https://ci.appveyor.com/project/axboe/fio) indirin ve kullanın. En son x86 veya x64 oluşturmayı seçin, **Eserler** sekmesini seçin ve MSI'ı indirin.

<!--### Q. How to speed up the data copy if the source data has small files (KBs or few MBs)?
A.  To speed up the copy process:

- Create a local VHDx on fast storage or create an empty VHD on the HDD/SSD (slower).
- Mount it to a VM.
- Copy files to the VM’s disk.-->


### <a name="q-can-i-use-multiple-storage-accounts-with-data-box"></a>S. Data Box ile birden fazla depolama hesabı kullanabilir miyim?
A.  Evet. Data Box ile genel amaçlı, klasik veya blob depolama olmak üzere en fazla 10 depolama hesabı kullanılabilir. Hem sık hem de seyrek erişimli bloblar desteklenir. 


## <a name="ship-device"></a>Cihaz gönderme

<!--### Q. How do I schedule a pickup for my Data Box?--> 

### <a name="q-my-device-was-delivered-but-the-device-seems-to-be-damaged-what-should-i-do"></a>S. Cihazım teslim edildi ancak hasarlı görünüyor. Ne yapmalıyım?
A. Hasarlı bir şekilde ulaşan veya bariz kurcalama izleri bulunan cihazları kullanmayın. [Microsoft Desteğine başvurun](data-box-disk-contact-microsoft-support.md) ve cihazı mümkün olan en kısa sürede geri gönderin. Yenisinin gönderilmesi için yeni bir Data Box siparişi de oluşturabilirsiniz. Bu durumda yeni cihaz için ayrıca ücret alınmaz.

### <a name="q-can-i-pick-up-my-data-box-order-myself-can-i-return-the-data-box-via-a-carrier-that-i-choose"></a>S. Veri Kutusu siparişimi kendim alabilir miyim? Veri Kutusunu seçtiğim bir operatör aracılığıyla döndürebilir miyim?
A. Evet. Microsoft ayrıca yalnızca ABD Gov bölgesinde kendi kendini yöneten gönderim sunar. Veri Kutusu siparişini verirken, kendi kendini yöneten sevkiyat seçeneğini seçebilirsiniz. Veri Kutusu aygıtınızı almak için aşağıdaki adımları izleyin:
    
1. Siparişi verdikten sonra sipariş işlenir ve Veri Kutusu hazırlanır. Siparişinizin teslim alınmaya hazır olduğu bir e-posta yoluyla size bildirilir. 
2. Sipariş teslim alınmaya hazır olduğunda, Azure portalındaki siparişinize gidin ve **Genel Bakış** bıçağına gidin. 
3. Azure portalında kod içeren bir bildirim görürsünüz. Azure [Veri Kutusu İşlemleri ekibine](mailto:adbops@microsoft.com) e-posta ile gönder ve kodu sağlayın. Takım konumu sağlayacak ve bir teslim alma tarihi ve saati zamanlayacaktır. E-posta bildirimini aldıktan sonraki 5 iş günü içinde ekibi aramanız gerekir.

Veri kopyası tamamlandıktan sonra cihazınızı iade etmek için aşağıdaki adımları izleyin:

1. Veri kopyası hatasız olarak tamamlandıktan **sonra, gemiye hazırla'yı**çalıştırın. Hazırlık tamamlandıktan sonra, aygıtın yerel web ui bir kod alırsınız. Kodu kopyalayın ve kaydedin.
2. Cihazı kapatın ve bağlantı kablolarını çıkarın.
3. Cihaz ile beraber sağlanan güç kablosunu sararak emniyetli şekilde cihazın arkasına yerleştirin.
4. Azure [Veri Kutusu İşlemleri ekibine](mailto:adbops@microsoft.com) e-posta ile gönder ve onlara daha önce kaydettiğiniz kodu sağlayın. Aygıtı nereye ve ne zaman bırakacağınız hakkında bilgi verirler.

### <a name="q-will-my-data-box-devices-cross-country-borders-during-shipping"></a>S. Veri Kutusu aygıtlarım gönderim sırasında ülke sınırlarını geçer mi?
A. Tüm Veri Kutusu cihazları hedefleri yle aynı ülkeden sevk edilir ve herhangi bir uluslararası sınırdan geçmez. Bunun tek istisnası, cihazların herhangi bir AB ülkesine gönderip gönderilebildiği Avrupa Birliği'ndeki (AB) siparişlerdir. Bu, hem Veri Kutusu hem de Veri Kutusu Ağır aygıtları için geçerlidir.

### <a name="q-i-ordered-a-data-box-in-us-east-but-i-received-a-device-that-was-shipped-from-a-location-in-us-west-where-should-i-return-the-device-to"></a>S. Ben ABD Doğu'da bir Veri Kutusu emretti ama ABD Batı'da bir yerden sevk edildi bir cihaz aldı. Cihazı nereye iade etmeliyim?
A. Size mümkün olduğunca hızlı bir Veri Kutusu cihazı almaya çalışıyoruz. Gönderiye depolama hesabı konumunuza en yakın bir veri merkezinden öncelik veririz, ancak kullanılabilir envanteri olan herhangi bir Azure veri merkezinden bir aygıt nakleder. Veri Kutunuz, gönderi etiketinde gösterildiği şekilde gönderildiği konuma iade edilmelidir.

### <a name="q-e-ink-display-is-not-showing-the-return-shipment-label-what-should-i-do"></a>S. E-mürekkep üzerinde iade sevkiyat etiketi gösterilmiyor. Ne yapmalıyım?
A. E-mürekkepte iade sevkiyat etiketi gösterilmiyorsa aşağıdaki adımları uygulayın:
- Eski sevkiyat etiketini ve önceki sevkiyat işleminden kalan diğer tüm etiketleri sökün.
- Azure portalda sipariş sayfasına gidin. Genel **Bakış'a** gidin ve **gönderi etiketini indirin.** Daha fazla bilgi için bkz. [Sevkiyat etiketini indirme](data-box-portal-admin.md#download-shipping-label).
- Sevkiyat etiketini yazdırın ve cihaz üzerindeki şeffaf plastik cebe yerleştirin. 
- Sevkiyat etiketinin net bir şekilde göründüğünden emin olun. 

### <a name="q-how-is-my-data-protected-during-transit"></a>S. Verilerim aktarım sırasında nasıl korunur? 
A.  Aşağıdaki Data Box özellikleri taşıma sırasında verilerinizin korunmasına yardımcı olur.
 - Data Box diskleri AES 256 bit şifreleme ile şifrelenir. 
 - Cihaz kilitlenir ve verilere erişmek için bir parola kullanılması gerekir.
Daha fazla bilgi için bkz. [Data Box güvenlik özellikleri](data-box-security.md).  

### <a name="q-i-have-finished-prepare-to-ship-and-shut-down-the-device-can-i-still-add-more-data-to-data-box"></a>S. Göndermeye Hazırlama adımlarını tamamladım ve cihazı kapattım. Data Box'a yeni veri eklemem mümkün mü?
A. Evet. Cihazı açarak daha fazla veri ekleyebilirsiniz. Veri kopyalama işlemini tamamladıktan sonra **Göndermeye Hazırlama** adımlarını yeniden tamamlamanız gerekir.

### <a name="q-i-received-my-device-and-it-is-not-booting-up-how-do-i-ship-the-device-back"></a>S. Aygıtımı aldım ve başlatmıyor mu? Cihazı nasıl geri gönderim?
A. Aygıtınız önyükleme de değilse, Azure portalında siparişinize gidin. Bir gönderi etiketi indirin ve bunu aygıta yapıştırın. Daha fazla bilgi için bkz. [Sevkiyat etiketini indirme](data-box-portal-admin.md#download-shipping-label).

## <a name="verify-and-upload"></a>Doğrulama ve yükleme

### <a name="q-how-soon-can-i-access-my-data-in-azure-once-ive-shipped-the-data-box-back"></a>S. Data Box'ı geri gönderdikten sonra verilerime Azure'da nasıl erişebilirim? 
A.  **Veri Kopyalama** işlemi için sipariş durumu **Tamamlandı** olarak değiştiğinde verilerinize doğrudan erişim sağlayabilirsiniz.

### <a name="q-where-is-my-data-located-in-azure-after-the-upload"></a>S. Yükleme sonrasında verilerim Azure'da hangi konumda bulunur?
A.  Verileri Veri Kutusu'na kopyaladiğinizde, verilerin blok blob veya sayfa blob veya Azure dosyaları olup olmadığına bağlı olarak, veriler Azure Depolama hesabınızdaki aşağıdaki yollardan birine yüklenir.
- `https://<storage_account_name>.blob.core.windows.net/<containername>` 
- `https://<storage_account_name>.file.core.windows.net/<sharename>`
 
  Alternatif olarak Azure portalda Azure depolama hesabınıza gidip oradan ilerleyebilirsiniz.

### <a name="q-i-just-noticed-that-i-did-not-follow-the-azure-naming-requirements-for-my-containers-will-my-data-fail-to-upload-to-azure"></a>S. Kapsayıcılarım için Azure adlandırma gereksinimlerine uygun hareket etmediğimi fark ettim. Verilerim yine de Azure'a yüklenir mi?
A.  Kapsayıcı adlarının büyük harfolması durumunda, bu adlar otomatik olarak küçük harfe dönüştürülür. Adlar diğer kurallara (özel karakterler, diğer diller gibi) uygun değilse yükleme işlemi başarısız olur. Paylaşımları adlandırma konusundaki en iyi yöntemler hakkında daha fazla bilgi için bkz.
- [Paylaşımları adlandırma ve onlara başvurma](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata)
- [Blok blobları ve sayfa blobları kuralları](https://docs.microsoft.com/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs).

### <a name="q-how-do-i-verify-the-data-i-copied-onto-data-box"></a>S. Data Box'a kopyaladığım verileri nasıl doğrulayabilirim?
A.  Veri kopyalama işlemi tamamlandıktan sonra **Göndermeye Hazırlama** adımlarında verileriniz doğrulanır. Data Box, doğrulama sırasında dosyaların ve veri sağlama toplamlarının bir listesini oluşturur. Dosya listesini indirebilir ve listeyi kaynak verilerdeki dosyalarla karşılaştırabilirsiniz. Daha fazla bilgi için bkz. [Göndermeye hazırlama](data-box-deploy-picked-up.md#prepare-to-ship).

### <a name="q-what-happens-to-my-data-after-i-have-returned-the-data-box"></a>S. Data Box'ı iade ettiğimde verilerime ne olur?
A.  Veriler Azure'a kopyalandıktan sonra Data Box disklerindeki veriler NIST SP 800-88 Revision 1 yönergelerine uygun ve güvenli bir şekilde silinir. Daha fazla bilgi için bkz. [Data Box'tan verileri silme](data-box-deploy-picked-up.md#erasure-of-data-from-data-box).

## <a name="audit-report"></a>Denetim raporu

### <a name="how-does-azure-data-box-service-help-support-customers-chain-of-custody-procedure"></a>Azure Data Box hizmeti müşterilere delil zinciri yordamı konusunda nasıl destek oluyor?
A.  Azure Data Box hizmeti delil zinciri belgeleri için kullanabileceğiniz raporlar sunar. Denetleme ve kopyalama günlükleri Azure'daki depolama hesabınızda mevcuttur ve siparişler tamamlandıktan sonra Azure portalda [sipariş geçmişini indirebilirsiniz](data-box-portal-admin.md#download-order-history).


### <a name="what-type-of-reporting-is-available-to-support-chain-of-custody"></a>Koruma zinciri desteği için sunulan rapor türleri nelerdir?
A.  Koruma zinciri desteği için aşağıdaki rapor türleri sunulur:

- UPS'ten nakliye lojistiği.
- Cihazı açma ve kullanıcı paylaşımı erişimi günlükleri.
- Data Box'a başarıyla eklenen her dosya için 64 bit dönüşsel artıklık denetimi (CRC-64) veya sağlama toplamı.
- Azure depolama hesabına yükleme işlemi başarısız olan dosyaların bildirilmesi.
- Veriler Azure depolama hesabınıza kopyalandıktan sonra Data Box cihazının temizlenmesi (NIST 800 88R1 standartlarına göre).

### <a name="are-the-carrier-tracking-logs-from-ups-available"></a>Operatör izleme günlükleri (UPS'ten) kullanılabilir mi? 
A.  Taşıyıcı takip günlükleri, Data Box sipariş geçmişine kaydedilir. Bu rapor cihaz Azure veri merkezine iade edildikten ve cihaz üzerindeki veriler silindikten sonra sunulur. Daha erken ihtiyaç duymanız durumunda taşıyıcının web sitesine gidip sipariş takip numarasıyla takip bilgilerine ulaşabilirsiniz.

### <a name="can-i-transport-the-data-box-to-azure-datacenter"></a>Data Box'ı Azure veri merkezine kendim götürebilir miyim? 
A.  Hayır. Şu anda Azure veri merkezi, Veri Kutusu'nun müşterilerden veya UPS dışındaki operatörlerden teslimini kabul etmemektedir.


## <a name="next-steps"></a>Sonraki adımlar

- [Data Box sistem gereksinimlerini](data-box-system-requirements.md) gözden geçirin.
- [Data Box sınırlarını](data-box-limits.md) anlayın.
- Azure portalda [Azure Data Box](data-box-quickstart-portal.md)’u hızla dağıtın.
