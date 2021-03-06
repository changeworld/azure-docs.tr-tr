---
title: Microsoft Azure Data Box Disk Hakkında SSS | Microsoft Docs
description: Büyük miktarda veriyi Azure'a aktarmanızı sağlayan bir bulut çözümü olan Azure Veri Kutusu Diski için sık sorulan sorular ve yanıtlar içerir
services: databox
author: alkohli
ms.service: databox
ms.subservice: disk
ms.topic: conceptual
ms.date: 08/29/2019
ms.author: alkohli
ms.openlocfilehash: 7ba6ea8606fc354527ff4114bc45a0904941ba93
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77918946"
---
# <a name="azure-data-box-disk-frequently-asked-questions"></a>Azure Veri Kutusu Diski: Sık Sorulan Sorular

Microsoft Azure Data Box Disk bulut istasyonu çözümü, terabaytlarca veriyi Azure'a hızlı, uygun maliyetli ve güvenilir bir şekilde göndermenizi sağlar. Bu SSS belgesinde Azure portaldaki Data Box Disklerini kullanımınızla ilgili sorulara ve yanıtlarına yer verilmiştir. 

Sorular ve yanıtlar aşağıdaki kategorilere ayrılmıştır:

- Hizmet hakkında
- Yapılandırma ve bağlanma 
- Durumu izleme
- Geçiş verileri 
- Verileri doğrulama ve yükleme 


## <a name="about-the-service"></a>Hizmet hakkında

### <a name="q-what-is-azure-data-box-service"></a>S. Azure Data Box hizmeti nedir? 
A.  Azure Data Box hizmeti, çevrimdışı veri alımı için tasarlanmıştır. Bu hizmet farklı depolama kapasitelerinde veri aktarımı gerçekleştirmek için tasarlanmış olan bir dizi ürünü yönetir. 

### <a name="q-what-are-azure-data-box-disks"></a>S. Azure Data Box Diskleri nedir?
A. Azure Data Box Diskleri terabaytlarca veriyi Azure'da çift yönlü olarak hızlı, uygun maliyetli ve güvenli bir şekilde aktarmanızı sağlar. Microsoft size toplamda maksimum 35 TB depolama kapasitesi sunan 1 ile 5 arasında disk gönderir. Bu diskleri Azure portalda Data Box hizmetiyle kolayca yapılandırabilir, bağlayabilir ve disklerin kilidini açabilirsiniz.  

Diskler Microsoft BitLocker sürücü şifrelemesi ile şifrelenir ve şifreleme anahtarlarınız Azure portalda yönetilir. Ardından müşterinin sunucularındaki verileri kopyalarsınız. Microsoft verilerinizi veri merkezinde hızlı ve özel bir ağ yükleme bağlantısı aracılığıyla sürücüden buluta geçirir ve Azure'a yükler.

### <a name="q-when-should-i-use-data-box-disks"></a>S. Data Box Disklerini ne zaman kullanmalıyım?
A. Azure'a aktarmak istediğiniz 40 TB (veya daha az) boyutunda veriniz varsa Data Box Disklerinden faydalanabilirsiniz.

### <a name="q-what-is-the-price-of-data-box-disks"></a>S. Data Box Disklerinin maliyeti nedir?
A. Veri Kutusu Disklerinin fiyatı hakkında daha fazla bilgi için [Fiyatlandırma sayfasına](https://azure.microsoft.com/pricing/details/databox/disk/)gidin.

### <a name="q-how-do-i-get-data-box-disks"></a>S. Data Box Disklerini nasıl edinebilirim? 
A.  Azure Veri Kutusu Diskleri'ni almak için Azure portalına giriş yapın ve diskler için bir Veri Kutusu sırası oluşturun. İletişim bilgilerinizi ve bildirim ayrıntılarını girin. Sipariş verdikten sonra kullanılabilirlik durumuna göre diskler 10 gün içinde gönderilir.

### <a name="q-what-is-the-maximum-amount-of-data-i-can-transfer-with-data-box-disks-in-one-instance"></a>S. Data Box Disklerine tek seferde en fazla ne kadar veri aktarabilirim?
A. Her biri 8 TB boyutunda (7 TB kullanılabilir kapasite) 5 disk için kullanabileceğiniz maksimum kapasite 35 TB olacaktır. Dolayısıyla tek seferde en fazla 35 TB veri aktarabilirsiniz. Daha fazla veri aktarmak için daha fazla disk sipariş etmeniz gerekir.

### <a name="q-how-can-i-check-if-data-box-disks-are-available-in-my-region"></a>S. Data Box Disklerinin bulunduğum bölgede kullanılabilir durumda olup olmadığını nasıl kontrol edebilirim? 
A.  Veri Kutusu Disklerinin şu anda nerede kullanılabildiği görmek için [Bölge kullanılabilirliğine](data-box-disk-overview.md#region-availability)gidin.  

### <a name="q-which-regions-can-i-store-data-in-with-data-box-disks"></a>S. Data Box Diskleri ile hangi bölgelerde veri depolayabilirim?
A. Data Box Disk, ABD, Kanada, Avustralya, Batı Avrupa ve Kuzey Avrupa, Kore ve Japonya'daki tüm bölgeler için desteklenir. Yalnızca Azure genel bulut bölgeleri desteklenir. Azure Kamu veya diğer bağımsız bulutlar desteklenmez.

### <a name="q-will-my-data-box-disk-cross-country-borders-during-shipping"></a>S. Veri Kutusu Diskim gönderim sırasında ülke sınırlarını geçer mi?
A. Veri Kutusu Diski, hedefleriyle aynı ülkeden gönderilir ve herhangi bir uluslararası sınırdan geçmez. Bunun tek istisnası, disklerin herhangi bir AB ülkesine gönderip gönderiedebileceği Avrupa Birliği'ndeki (AB) siparişlerdir.

### <a name="q-whom-should-i-contact-if-i-encounter-any-issues--with-data-box-disks"></a>S. Data Box Diskleriyle ilgili sorun yaşamam halinde kiminle iletişim kurmam gerekir?
A. Veri Kutusu Diskleri ile ilgili herhangi bir sorunla karşılaşırsanız, lütfen [Microsoft Destek'e başvurun.](https://docs.microsoft.com/azure/databox/data-box-disk-contact-microsoft-support)

## <a name="configure-and-connect"></a>Yapılandırma ve bağlanma
 
### <a name="q-can-i-specify-the-number-of-data-box-disks-in-the-order"></a>S. Siparişteki Data Box Diski sayısını seçebilir miyim?
A.  Hayır. Verilerinizin boyutuna ve disklerin kullanılabilirliğine göre 8 TB boyutunda diskler (en fazla 5 disk) gönderilir.  

### <a name="q-how-do-i-unlock-the-data-box-disks"></a>S. Data Box Disklerinin kilidini nasıl açabilirim? 
A.  Azure portalda Data Box Disk siparişinize gidin ve **Cihaz ayrıntıları**'na gidin. Geçiş anahtarını kopyalayın. İşletim sisteminiz için Azure portaldan Data Box kilidi açma aracını indirip açın. Aracı, disklere kopyalamak istediğiniz verileri içeren bilgisayarda çalıştırın. Disklerin kilidini açmak için geçiş anahtarını girin. Aynı geçiş anahtarı tüm disklerin kilidini açar. 

Yönerge adımları için [Windows istemcisinde disk kilidi açma](data-box-disk-deploy-set-up.md#unlock-disks-on-windows-client)'ya veya [Linux istemcisinde disk kilidi açma](data-box-disk-deploy-set-up.md#unlock-disks-on-linux-client)'ya gidin.

### <a name="q-can-i-use-a-linux-host-computer-to-connect-and-copy-the-data-on-to-the-data-box-disks"></a>S. Data Box Disklerine bağlanmak ve veri kopyalamak için Linux ana bilgisayarı kullanabilir miyim?
A.  Evet. Bağlanmak ve Data Box Disklerine veri kopyalamak için gerek Linux, gerekse Windows istemcisi kullanılabilir. Daha fazla bilgi için ana bilgisayarınıza ilişkin [Desteklenen işletim sistemleri](data-box-disk-system-requirements.md) listesine gidin.

### <a name="q-my-disks-are-dispatched-but-now-i-want-to-cancel-this-order-why-is-the-cancel-button-not-available"></a>S. Disklerim yola çıktı ancak şimdi siparişimi iptal etmek istiyorum. Neden iptal düğmesi kullanılamıyor?
A.  Siparişi yalnızca disk siparişi verdikten sonra ancak diskler gönderilmeden önce iptal edebilirsiniz. Diskler yola çıktıktan sonra siparişi iptal edemezsiniz. Ancak, disklerinizi bir ücret karşılığında döndürebilirsiniz. 

### <a name="q-can-i-connect-multiple-data-box-disks-at-the-same-to-the-host-computer-to-transfer-data"></a>S. Veri aktarımı için aynı anda birden fazla Data Box Diskini ana bilgisayara bağlayabilir miyim?
A. Evet. Evet, birden fazla Data Box Diski aynı ana bilgisayara bağlanarak aynı anda birden fazla veri aktarım ve kopyalama işi çalıştırılabilir.

## <a name="track-status"></a>Durumu izleme

### <a name="q-how-do-i-track-the-disks-from-when-i-placed-the-order-to-shipping-the-disks-back"></a>S. Diskleri geri göndermek için sipariş oluşturduktan sonra diskleri nasıl takip edebilirim? 
A.  Data Box Disk siparişinin durumunu Azure portaldan takip edebilirsiniz. Siparişi oluşturduğunuzda bildirimlerin gönderileceği bir e-posta adresi girmeniz de istenir. E-posta adresi girdiyseniz sipariş durumundaki tüm değişiklikler bu adrese gönderilir. [Bilgilendirme e-postalarını yapılandırma](data-box-portal-ui-admin.md#edit-notification-details) hakkında daha fazla bilgi edinin.

### <a name="q-how-do-i-return-the-disks"></a>S. Diskleri nasıl iade edebilirim? 
A.  Microsoft tarafından gönderilen Data Box Diski paketinde iade için kullanabileceğiniz bir sevkiyat etiketi bulunur. Etiketi kutunun üzerine yapıştırın ve kapattığınız paketi sevkiyat şirketine teslim edin. Etiketin hasar görmesi veya kaybolması durumunda **Genel bakış > Sevkiyat etiketini indir** sayfasına gidin ve yeni bir sevkiyat etiketi indirin.

### <a name="can-i-pick-up-my-data-box-disk-order-myself-can-i-return-the-disks-via-a-carrier-that-i-choose"></a>Veri Kutusu Disk siparişimi kendim alabilir miyim? Diskleri seçtiğim bir taşıyıcı aracılığıyla döndürebilir miyim?
A. Evet. Microsoft ayrıca yalnızca ABD Gov bölgesinde kendi kendini yöneten gönderim sunar. Veri Kutusu Disk siparişini yerleştirirken, kendi kendini yöneten gönderi seçeneğini seçebilirsiniz. Veri Kutusu Disk siparişinizi almak için aşağıdaki adımları izleyin:
    
1. Siparişi verdikten sonra sipariş işlenir ve diskler hazırlanır. Siparişinizin teslim alınmaya hazır olduğu bir e-posta yoluyla size bildirilir. 
2. Sipariş teslim alınmaya hazır olduğunda, Azure portalındaki siparişinize gidin ve **Genel Bakış** bıçağına gidin. 
3. Azure portalında kod içeren bir bildirim görürsünüz. Azure [Veri Kutusu İşlemleri ekibine](mailto:adbops@microsoft.com) e-posta ile gönder ve kodu sağlayın. Takım konumu sağlayacak ve bir teslim alma tarihi ve saati zamanlayacaktır. E-posta bildirimini aldıktan sonraki 5 iş günü içinde ekibi aramanız gerekir.

Veri kopyalama ve doğrulama tamamlandıktan sonra, diskinizi iade etmek için aşağıdaki adımları izleyin:

1. Veri doğrulama tamamlandıktan sonra diskleri çıkarın. Bağlantı kablolarını çıkarın.
2. Tüm diskleri ve bağlantı kablolarını kabarcıklı naylona sarın ve bunları sevkiyat kutusuna yerleştirin. Aksesuarlar eksikse ücret uygulanabilir.

    - Ürün size ilk ulaştığında gönderilen paketi kullanın. Disklerinizi, hava kabarcıklı ambalaj malzemesiyle sıkıca sararak paketlemenizi öneririz.
    - Diskin kutunun içinde hareket etmesini engellemek için kutuda boşluk kalmadığından emin olun.
3. Azure portalındaki siparişiniz için **Genel Bakış bıçağına** gidin. Kodlu bir bildirim görmeniz gerekir.
4. Bu kodu kullanın ve [Azure Veri Kutusu İşlemleri ekibine](mailto:adbops@microsoft.com) e-posta ile gönder ve kodu sağlayın. Disklerin nereye ve ne zaman bırakacağınız hakkında bilgi sağlarlar.


## <a name="migrate-data"></a>Geçiş verileri

### <a name="q-what-is-the-maximum-data-size-that-can-be-used-with-data-box-disks"></a>S. Data Box Diskleri ile kullanılabilecek maksimum veri boyutu nedir?  
A.  Data Box Diskleri çözümünde toplam 35 GB kapasiteye sahip en fazla 5 disk kullanılabilir. Her bir disk 8 TB (7 TB kullanılabilir) boyutundadır.

### <a name="q-what-are-the-maximum-block-blob-and-page-blob-sizes-supported-by-data-box-disks"></a>S. Data Box Diskleri tarafından desteklenen maksimum blok blobu ve sayfa blobu boyutu nedir? 
A.  Maksimum boyutlar Azure Depolama sınırları ile belirlenir. Maksimum blok blobu yaklaşık 4,768 TiB, maksimum sayfa blobu boyutu ise 8 TiB olarak belirlenmiştir. Daha fazla bilgi için [Blob depolama için Ölçeklenebilirlik ve performans hedeflerine](../storage/blobs/scalability-targets.md)bakın.

### <a name="q-what-is-the-data-transfer-speed-for-data-box-disks"></a>S. Data Box Disklerinin veri aktarım hızı nedir?
A. USB 3.0 bağlantısıyla yapılan testlerde disk performansının 430 MB/sn seviyesine çıkabildiği görülmüştür. Gerçek performans kullanılan dosya boyutuna göre değişiklik gösterecektir. Daha küçük dosyalarda performans daha düşük olabilir.

### <a name="q-how-do-i-know-that-my-data-is-secure-during-transit"></a>S. Verilerimin aktarım sırasında güvende olduğundan nasıl emin olabilirim? 
A.  Data Box Diskleri BitLocker AES-128 bit şifreleme kullanılarak şifrelenir ve destek anahtarına yalnızca Azure portaldan erişilebilir. Geçiş anahtarını almak için hesap kimlik bilgilerinizi kullanarak Azure portalda oturum açın. Data Box Disk kilit açma aracını çalıştırdığınızda bu geçiş anahtarını kullanın.

### <a name="q-how-do-i-copy-the-data-to-the-data-box-disks"></a>S. Data Box Disklerine nasıl veri kopyalayabilirim? 
A.  Robocopy, Diskboss veya Windows Dosya Gezgini gibi bir SMB kopyalama aracı kullanarak verileri sürükleyip diske bırakın.

### <a name="q-are-there-any-tips-to-speed-up-the-data-copy"></a>S. Veri kopyalama işlemini hızlandırmaya yönelik ipuçları var mı?
A.  Kopyalama işlemini hızlandırmak için:

- Birden fazla veri kopyalama akışı kullanın. Örneğin Robocopy'de çok iş parçacıklı seçeneği kullanın. Kullanılan komut hakkında daha fazla bilgi için [Öğretici: Azure Data Box Diskine veri kopyalama ve doğrulama](data-box-disk-deploy-copy-data.md#copy-data-to-disks) sayfasına gidin.
- Birden fazla oturum kullanın.
- Ağ paylaşımı üzerinden kopyalama yapmak yerine (ağ hızları kısıtlayıcı olabilir) verilerin, disklerin bağlı olduğu bilgisayarın yerel depolama alanında bulunduğundan emin olun.
- Kopyalama işlemi boyunca USB 3.0 veya üzeri bağlantı kullandığınızdan emin olun. Bilgisayara bağlı USB denetleyicilerini ve USB cihazlarını tanımlamak için [USBView aracını](https://docs.microsoft.com/windows-hardware/drivers/debugger/usbview) indirin ve kullanın.
- Veri kopyalamak için kullanılan bilgisayarın performansını karşılaştırın. Sunucu donanımının performansını karşılaştırmak için [Bluestop FIO aracını](https://ci.appveyor.com/project/axboe/fio) indirin ve kullanın. En son x86 veya x64 oluşturmayı seçin, **Eserler** sekmesini seçin ve MSI'ı indirin.

### <a name="q-how-to-speed-up-the-data-if-the-source-data-has-small-files-kbs-or-few-mbs"></a>S. Kaynak veriler küçük dosyalardan (KB veya birkaç MB) oluşuyorsa veri aktarımını nasıl hızlandırabilirim?
A.  Kopyalama işlemini hızlandırmak için:

- Hızlı depolama alanında yerel bir VHDx oluşturun veya HDD/SSD üzerinde boş bir VHD oluşturun (daha yavaştır).
- Bunu bir sanal makineye takın.
- Dosyaları sanal makinenin diskine kopyalayın.

### <a name="q-can-i-use-multiple-storage-accounts-with-data-box-disks"></a>S. Data Box Diskleriyle birden fazla depolama hesabı kullanabilir miyim?
A.  Hayır. Şu an için Data Box Diskleri ile yalnızca tek bir depolama hesabı (genel veya klasik) kullanılabilir. Hem sık hem de seyrek erişimli bloblar desteklenir. Şu anda, Azure genel bulutundaki yalnızca ABD, Batı Avrupa ve Kuzey Avrupa'daki depolama hesapları desteklenir.

### <a name="q-what-is-the-toolset-available-for-my-data-with-data-box-disks"></a>S. Veri Kutusu Diskleri ile verilerim için kullanılabilir araç kümesi nedir?
A. Veri Kutusu Diski ile kullanılabilen araç kümesi üç araç içerir:
 - **Veri Kutusu Disk Kilidini açma aracı**: Microsoft'tan gönderilen şifreli disklerin kilidini açmak için bu aracı kullanın. Aracı kullanarak disklerin kilidini açarken, Azure portalındaki Veri Kutusu Diski siparişinde kullanılabilen bir geçiş anahtarı sağlamanız gerekir. 
 - **Veri Kutusu Disk Doğrulama aracı**: Azure adlandırma kurallarına göre boyut, biçim ve blob adlarını doğrulamak için bu aracı kullanın. Ayrıca, azure'a yüklenen verileri doğrulamak için kullanılan kopyalanan veriler için denetim umları da oluşturur.
 - **Veri Kutusu Disk Bölme Kopyalama aracı**: Birden çok disk kullanırken ve tüm diskler arasında bölünmesi ve kopyalanması gereken büyük bir veri kümesine sahipken bu aracı kullanın. Bu araç şu anda Windows için kullanılabilir. Bu araç yönetilen disklerle desteklenmez. Bu araç, verileri kopyalarken de doğrular, bu nedenle bu aracı kullanırken doğrulama adımını atlayabilirsiniz.

Araç seti hem Windows hem de Linux için kullanılabilir. Araç kümesini buradan indirebilirsiniz:
- [Windows için Data Box Disk araç takımını indirin](https://aka.ms/databoxdisktoolswin) 
- [Linux için Data Box Disk araç takımını indirin](https://aka.ms/databoxdisktoolslinux)
 
### <a name="q-can-i-use-data-box-disk-to-transfer-data-to-azure-files-and-then-use-the-data-with-azure-file-sync"></a>S. Verileri Azure Dosyaları'na aktarmak ve verileri Azure Dosya Eşitle'si ile kullanmak için Veri Kutusu Disk'i kullanabilir miyim? 
A. Azure Dosyaları Veri Kutusu Diski ile desteklenir, ancak Azure Dosya Eşitlemesi ile iyi çalışmaz. Dosya verileri Azure Dosya Eşitlemesi ile kullanılıyorsa meta veriler tutulmaz.


## <a name="verify-and-upload"></a>Doğrulama ve yükleme

### <a name="q-how-soon-can-i-access-my-data-in-azure-once-ive-shipped-the-disks-back"></a>S. Diskleri geri gönderdikten sonra verilerime Azure'da nasıl erişebilirim? 
A.  Veri Kopyalama işlemi için sipariş durumu tamamlandı olarak değiştiğinde verilerinize doğrudan erişim sağlayabilirsiniz.

### <a name="q-where-is-my-data-located-in-azure-after-the-upload"></a>S. Yükleme sonrasında verilerim Azure'da hangi konumda bulunur?
A.  Verileri diskinizdeki *BlockBlob* ve *PageBlob* klasörlerinin altına kopyaladığınızda Azure depolama hesabında *BlockBlob* ve *PageBlob* klasörünün her alt klasörü için bir kapsayıcı oluşturulur. Dosyaları doğrudan *BlockBlob* ve *PageBlob* klasörlerine kopyaladıysanız Azure Depolama hesabında *$root* adlı varsayılan kapsayıcıya yerleştirilir. Verileri *AzureFile* klasörü altındaki bir klasöre kopyaladiğinizde bir dosya paylaşımı oluşturulur.

### <a name="q-i-just-noticed-that-i-did-not-follow-the-azure-naming-requirements-for-my-containers-will-my-data-fail-to-upload-to-azure"></a>S. Kapsayıcılarım için Azure adlandırma gereksinimlerine uygun hareket etmediğimi fark ettim. Verilerim yine de Azure'a yüklenir mi?
A. Kapsayıcı adlarında büyük harf kullandıysanız bunlar otomatik olarak küçük harfe dönüştürülür. Adlar diğer kurallara (özel karakterler, diğer diller gibi) uygun değilse yükleme işlemi başarısız olur. Daha fazla bilgi için [Azure adlandırma kurallarına](data-box-disk-limits.md#azure-block-blob-page-blob-and-file-naming-conventions) gidin.

### <a name="q-how-do-i-verify-the-data-i-copied-onto-multiple-data-box-disks"></a>S. Birden fazla Data Box Diskine kopyaladığım verileri nasıl doğrulayabilirim?
A.  Veri kopyalama işlemi tamamlandıktan sonra *DataBoxDiskImport* klasöründe bulunan `DataBoxDiskValidation.cmd` uygulamasını çalıştırarak doğrulama için sağlama toplamları alabilirsiniz. Birden fazla diskiniz varsa her disk için bir komut penceresi açarak bu komutu çalıştırmanız gerekir. Bu işlemin verilerinizin boyutuna göre uzun sürebileceğini (birkaç saat) unutmayın.

### <a name="q-what-happens-to-my-data-after-i-have-returned-the-disks"></a>S. Diskleri iade ettiğimde verilerime ne olur?
A.  Veriler Azure'a kopyalandıktan sonra disklerdeki veriler NIST SP 800-88 Revision 1 yönergelerine uygun ve güvenli bir şekilde silinir.  

### <a name="q-how-is-my-data-protected-during-transit"></a>S. Verilerim aktarım sırasında nasıl korunur? 
A.  Data Box Diskleri AES-128 Microsoft BitLocker şifrelemesi ile korunur. Tüm disklerin kilidini açmak ve verilere erişmek için tek bir geçiş anahtarı kullanılması gerekir.

### <a name="q-do-i-need-to-rerun-checksum-validation-if-i-add-more-data-to-the-data-box-disks"></a>S. Data Box Disklerine daha fazla veri eklersem sağlama toplamı doğrulama işlemini tekrar çalıştırmam gerekir mi?
A. Evet. Verilerinizi doğrulamaya karar verirseniz (bunu yapmanızı öneririz!) disklere daha fazla veri eklemeniz durumunda doğrulama işlemini tekrarlamanız gerekir.

### <a name="q-i-used-all-my-disks-to-transfer-data-and-need-to-order-more-disks-is-there-a-way-to-quickly-place-the-order"></a>S. Veri aktarımı için tüm diskleri kullandım ve daha fazla disk sipariş etmem gerekiyor. Siparişi hızlı bir şekilde vermek için kullanabileceğim bir yöntem var mı?
A. Önceki siparişinizi kopyalayabilirsiniz. Kopyalama işlemi, bir öncekiyle aynı bilgilere sahip bir sipariş oluşturur ve adres, iletişim ve bildirim ayrıntılarını yeniden girmenize gerek kalmadan yalnızca sipariş bilgilerini düzenleyebilirsiniz.

### <a name="q-i-copied-data-to-manageddisk-folder-i-dont-see-any-managed-disks-with-the-resource-group-specified-for-managed-disks-was-my-data-uploaded-to-azure-and-how-can-i-locate-it"></a>S. Verileri ManagedDisk klasörüne kopyaladım. Yönetilen diskler için belirtilen kaynak grubu ile yönetilen diskler görmüyorum. Verilerim Azure'a yüklendi ve bu verileri nasıl bulabiliyorum?
A. Evet. Verileriniz Azure'a yüklendi, ancak belirtilen kaynak gruplarına sahip yönetilen diskler görmüyorsanız, bu büyük olasılıkla verilerin geçerli olmamasıdır. Sayfa blobs, blok blobs, Azure Dosyaları ve yönetilen diskler geçerli değilse, aşağıdaki klasörlere gider:
 - Sayfa lekeleri *databoxdisk-geçersiz-pb*ile başlayan bir blok blob konteyner giderdi.
 - Azure *Dosyaları, databoxdisk-geçersiz-af ile*başlayan bir blok blob kapsayıcıya gider.
 - Yönetilen diskler *databoxdisk-geçersiz-md*ile başlayan bir blok blob kapsayıcı giderdi.

## <a name="next-steps"></a>Sonraki adımlar

- Veri [Kutusu Disk sistem gereksinimlerini](data-box-disk-system-requirements.md)gözden geçirin.
- [Data Box Disk sınırlarını](data-box-disk-limits.md) anlayın.
- Azure portalında [Azure Data Box Disk](data-box-disk-quickstart-portal.md)’i hızlı dağıtın.
