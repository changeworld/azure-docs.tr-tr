---
title: Provizyon StorBasit Sanal Dizi Hyper-V | Microsoft Dokümanlar
description: StorSimple Virtual Array dağıtımındaki bu ikinci öğretici, Hyper-V'de sanal bir dizi sağlanmasını içerir.
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: 4354963c-e09d-41ac-9c8b-f21abeae9913
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 07/25/2019
ms.author: alkohli
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 9d3f4f4ab6cc1c928761fce740d39f3f73426e62
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79267540"
---
# <a name="deploy-storsimple-virtual-array---provision-in-hyper-v"></a>StorSimple Sanal Dizi dağıtın - Hyper-V'de Hüküm
![](./media/storsimple-virtual-array-deploy2-provision-hyperv/hyperv4.png)

## <a name="overview"></a>Genel Bakış

[!INCLUDE [storsimple-virtual-array-eol-banner](../../includes/storsimple-virtual-array-eol-banner.md)]

Bu öğretici, Windows Server 2012 R2, Windows Server 2012 veya Windows Server 2008 R2'de Hyper-V çalıştıran bir ana bilgisayar sisteminde StorSimple Virtual Array'in nasıl sağlanmaktadır. Bu makale, Azure portalında ve Microsoft Azure Devlet Bulutu'nda StorSimple Sanal Diziler dağıtımı için geçerlidir.

Sanal bir dizisağlamak ve yapılandırmak için yönetici ayrıcalıklarına ihtiyacınız vardır. Sağlama ve ilk kurulum adımlarını tamamlamak yaklaşık 10 dakika sürecektir.

## <a name="provisioning-prerequisites"></a>Ön koşulların sağlanması
Burada, Windows Server 2012 R2, Windows Server 2012 veya Windows Server 2008 R2'de Hyper-V çalıştıran bir ana bilgisayar sisteminde sanal bir dizi sağlamanın ön koşulları bulacaksınız.

### <a name="for-the-storsimple-device-manager-service"></a>StorSimple Cihaz Yöneticisi hizmeti için
Başlamadan önce aşağıdakilerden emin olun:

* [StorSimple Virtual Array için portalı hazırlayın](storsimple-virtual-array-deploy1-portal-prep.md)tüm adımları tamamladınız.
* Azure portalından Hyper-V için sanal dizi görüntüsünü indirdiniz. Daha fazla bilgi için, **Bkz. Adım 3:** [StorSimple Virtual Array kılavuzu için portalı hazırlayın](storsimple-virtual-array-deploy1-portal-prep.md)sanal dizi görüntüsünü indirin.

  > [!IMPORTANT]
  > StorSimple Virtual Array üzerinde çalışan yazılım yalnızca StorSimple Device Manager hizmeti ile kullanılabilir.
  >
  >

### <a name="for-the-storsimple-virtual-array"></a>StorSimple Sanal Dizi için
Sanal bir dizi dağıtmadan önce şunları yapın:

* Windows Server 2008 R2'de Hyper-V çalıştıran bir ana bilgisayar sistemine veya daha sonra bir aygıtta kullanılabilir.
* Ana bilgisayar sistemi, sanal dizinizi sağlamak için aşağıdaki kaynakları ayırabilir:

  * En az 4 çekirdek.
  * En az 8 GB RAM. Sanal diziyi dosya sunucusu olarak yapılandırmayı planlıyorsanız, 8 GB 2 milyondan az dosyayı destekler. 2 - 4 milyon dosyayı desteklemek için 16 GB RAM gerekir.
  * Bir ağ arabirimi.
  * Veri için 500 GB sanal disk.

### <a name="for-the-network-in-the-datacenter"></a>Veri merkezindeki ağ için
Başlamadan önce, StorSimple Virtual Array dağıtmak ve veri merkezi ağını uygun şekilde yapılandırmak için ağ gereksinimlerini gözden geçirin. Daha fazla bilgi için [Bkz. StorSimple Virtual Array ağ gereksinimleri.](storsimple-ova-system-requirements.md#networking-requirements)

## <a name="step-by-step-provisioning"></a>Adım adım sağlama
Sanal bir diziyi sağlamak ve bu diziye bağlanmak için aşağıdaki adımları gerçekleştirmeniz gerekir:

1. Ana bilgisayar sisteminin minimum sanal dizi gereksinimlerini karşılamak için yeterli kaynağa sahip olduğundan emin olun.
2. Hipervizörünüzde sanal bir dizi sağlama.
3. Sanal diziyi başlatın ve IP adresini alın.

Bu adımların her biri aşağıdaki bölümlerde açıklanmıştır.

## <a name="step-1-ensure-that-the-host-system-meets-minimum-virtual-array-requirements"></a>Adım 1: Ana bilgisayar sisteminin minimum sanal dizi gereksinimlerini karşıladığından emin olun
Sanal bir dizi oluşturmak için şunları yapmanız gerekir:

* Hyper-V rolü Windows Server 2012 R2, Windows Server 2012 veya Windows Server 2008 R2 SP1'de yüklenir.
* Ana bilgisayara bağlı Microsoft Windows istemcisine yüklenmiş Microsoft Hyper-V Yöneticisi.

Sanal diziyi oluşturduğunuz temel donanımın (ana bilgisayar sistemi) aşağıdaki kaynakları sanal dizinize ayırabilmesini sağla:

* En az 4 çekirdek.
* En az 8 GB RAM. Sanal diziyi dosya sunucusu olarak yapılandırmayı planlıyorsanız, 8 GB 2 milyondan az dosyayı destekler. 2 - 4 milyon dosyayı desteklemek için 16 GB RAM gerekir.
* Bir ağ arabirimi.
* Sistem verileri için 500 GB sanal disk.

## <a name="step-2-provision-a-virtual-array-in-hypervisor"></a>Adım 2: Hipervizörde sanal bir dizi sağlama
Hiper yöneticinizde cihaz sağlamak için aşağıdaki adımları gerçekleştirin.

#### <a name="to-provision-a-virtual-array"></a>Sanal bir dizi sağlamak için
1. Windows Server ana bilgisayarınızda, sanal dizi görüntüsünü yerel bir sürücüye kopyalayın. Bu resmi (VHD veya VHDX) Azure portalı üzerinden indirdiniz. Bu görüntüyü yordamın ilerleyen bölümlerinde kullanacağınız için kopyaladığınız konumu not edin.
2. **Sunucu Yöneticisi**'ni açın. Sağ üst köşede **Araçlar**'a tıklayın ve **Hyper-V Yöneticisi**'ni seçin.

   ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image1.png)  

   Windows Server 2008 R2 çalıştırıyorsanız Hyper-V Manager'ı açın. Server Manager'da, **Hyper-V > Hyper-V Manager > Roller'i**tıklatın.
3. **Hyper-V Yöneticisi**'nin kapsam bölmesinde sistem düğümünüze sağ tıklayarak bağlam menüsünü açın ve **Yeni** > **Sanal Makine**'ye tıklayın.

   ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image2.png)
4. Yeni Sanal Makine Sihirbazı'nın **Başlamadan önce** sayfasında **İleri**'ye tıklayın.
5. Ad **ve konum belirt** sayfasında, sanal diziniz için bir **Ad** sağlayın. **İleri**'ye tıklayın.

   ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image4.png)
6. Oluşturma **yı belirt** sayfasında, aygıt görüntü türünü seçin ve sonra **İleri'yi**tıklatın. Windows Server 2008 R2 kullanıyorsanız bu sayfa görünmez.

   * Windows Server 2012 veya sonraki bir süre için bir .vhdx görüntü indirdiyseniz **Nesil 2'yi** seçin.
   * Windows Server 2008 R2 veya sonrası için bir .vhd görüntü indirdiyseniz **Nesil 1'i** seçin.

   ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image5.png)
7. **Bellek ata** sayfasında **Başlangıç belleği** değerini en az **8192 MB** yapın, dinamik bellek özelliğini etkinleştirmeyin ve **İleri**'ye tıklayın.

   ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image6.png)  
8. **Ağı yapılandır** sayfasında İnternete bağlı olan sanal anahtarı belirtin ve **İleri**'ye tıklayın.

   ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image7.png)
9. Sanal **sabit diski Bağla** sayfasında, **varolan sanal sabit diski kullan'ı**seçin, sanal dizi görüntüsünün (.vhdx veya .vhd) konumunu belirtin ve **sonra İleri'yi**tıklatın.

   ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image8m.png)
10. **Özet** sayfasını gözden geçirin ve **Son**'a tıklayarak sanal makineyi oluşturun.

    ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image9.png)
11. Minimum gereksinimleri karşılamak için 4 çekirdeğe ihtiyacınız vardır. 4 sanal işlemci eklemek için **Hyper-V Yöneticisi** penceresinde ana bilgisayar sisteminizi seçin. Sağ tarafta, **Sanal Makineler** listesinin altında bulunan bölmede az önce oluşturduğunuz sanal makineyi bulun. Makine adına sağ tıklayın ve **Ayarlar**'ı seçin.

    ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image10.png)
12. **Ayarlar** sayfasında sol taraftaki bölmeden **İşlemci**'yi seçin. Sağ taraftaki bölmede **sanal işlemci sayısını** 4 (veya üzeri) olarak ayarlayın. **Uygula**’ya tıklayın.

    ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image11.png)
13. Minimum gereksinimleri karşılamak için 500 GB sanal veri diski eklemeniz gerekir. **Ayarlar** sayfasında:

    1. Sol taraftaki bölmede **SCSI Denetleyicisi**'ni seçin.
    2. Sağ taraftaki bölmede **Sabit Sürücü**'yü seçin ve **Ekle**'ye tıklayın.

    ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image12.png)
14. **Sabit sürücü** sayfasında **Sanal sabit disk** seçeneğini belirleyin ve **Yeni**'ye tıklayın. **Yeni Sanal Sabit Disk Sihirbazı** açılır.

    ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image13.png)
15. Yeni Sanal Sabit Disk Sihirbazı'nın **Başlamadan önce** sayfasında **İleri**'ye tıklayın.
16. **Disk Biçimini Seç** sayfasında varsayılan seçenek olan **VHDX** biçimini kabul edin. **İleri**'ye tıklayın. Windows Server 2008 R2 çalıştırıyorsa bu ekran sunulmaz.

    ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image15.png)
17. **Disk Türünü Seç** sayfasında sanal sabit disk türünü **Dinamik olarak genişletilen** (önerilen) olarak ayarlayın. **Sabit boyutlu** diski de seçebilirsiniz ancak daha uzun süre beklemeniz gerekebilir. **Fark kayıt** seçeneğini kullanmamanızı öneririz. **İleri**'ye tıklayın. Windows Server 2012 R2 ve Windows Server 2012'de dinamik **olarak genişletme** varsayılan seçenektir, Windows Server 2008 R2'de ise varsayılan değer **Sabit boyutdur.**

    ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image16.png)
18. **Ad ve Konum Belirtin** sayfasında veri diski için bir **ad** ve **konum** (göz atabilirsiniz) belirtin. **İleri**'ye tıklayın.

    ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image17.png)
19. **Diski Yapılandır** sayfasında, **yeni boş sanal sabit disk oluştur** seçeneğini seçin ve boyutu **500 GB** (veya daha fazla) olarak belirtin. 500 GB minimum gereksinim olsa da, her zaman daha büyük bir disk sağlayabilir. Sağlanan diski genişletemeyeceğinizveya küçültemeyeceğiniz dikkat edin. Diskin boyutu hakkında daha fazla bilgi için, en [iyi uygulamalar belgesinde](storsimple-ova-best-practices.md)boyutlandırma bölümünü gözden geçirin. **İleri**'ye tıklayın.

    ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image18.png)
20. **Özet** sayfasında sanal veri diskinizin ayrıntılarını gözden geçirin ve her şey yolunda görünüyorsa **Son**'a tıklayarak diski oluşturun. Sihirbaz kapanır ve makinenize bir sanal sabit disk eklenir.

    ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image19.png)
21. **Ayarlar** sayfasına geri dönün. **Tamam**'a tıklayarak **Ayarlar** sayfasını kapatın ve Hyper-V Yöneticisi penceresine dönün.

    ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image20.png)

## <a name="step-3-start-the-virtual-array-and-get-the-ip"></a>Adım 3: Sanal diziyi başlatın ve IP'yi alın
Sanal dizinizi başlatmak ve ona bağlanmak için aşağıdaki adımları gerçekleştirin.

#### <a name="to-start-the-virtual-array"></a>Sanal diziyi başlatmak için
1. Sanal diziyi başlatın.

   ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image21.png)
2. Cihaz çalışmaya başladıktan sonra cihazı ve **Bağlan**'ı seçin.

   ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image22.png)
3. Cihazın hazır olması için 5-10 dakika beklemeniz gerekebilir. Konsolda ilerleme durumunu gösteren bir durum iletisi görüntülenir. Cihaz hazır olduktan sonra **Eylem** bölümüne gidin. Sanal `Ctrl + Alt + Delete` dizide oturum açmak için basın. Varsayılan kullanıcı *StorSimpleAdmin* ve varsayılan şifre *Password1*olduğunu.

   ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image23.png)
4. Güvenlik nedeniyle cihazın yönetici parolasının ilk oturum açma işleminin ardından değiştirilmesi gerekir. Parolayı değiştirmeniz istenir.

   ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image24.png)

   En az 8 karakterden oluşan bir parola girin. Parolanın şu 4 gereksinimden en az 3 tanesini karşılaması gerekir: büyük harf, küçük harf, rakam ve özel karakter. Onaylamak için parolayı yeniden girin. Parolanın değiştirildiği bildirilir.

   ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image25.png)
5. Parola başarıyla değiştirildikten sonra, sanal dizi yeniden başlatılabilir. Cihazın başlatılmasını bekleyin.

   ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image26.png)

    Cihazın Windows PowerShell konsolu ve ilerleme çubuğu gösterilir.

   ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image27.png)
6. Adım 6-8 yalnızca DHCP bulunmayan bir ortamdaki önyükleme süreci için geçerlidir. DHCP ortamındaysanız bu adımları atlayıp 9. adımla devam edebilirsiniz. Cihazınızı DHCP olmayan bir ortamda başlattıysanız, aşağıdaki ekranı görürsünüz.

   ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image28m.png)

    Ardından, ağı yapılandırın.
7. Sanal `Get-HcsIpAddress` dizinizde etkinleştirilen ağ arabirimlerini listelemek için komutu kullanın. Cihazınızda tek bir ağ arabirimi varsa `Ethernet` varsayılan adı atanır.

   ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image29m.png)
8. Ağı yapılandırmak için `Set-HcsIpAddress` cmdlet'ini kullanın. Aşağıdaki örneğe bakın:

    `Set-HcsIpAddress –Name Ethernet –IpAddress 10.161.22.90 –Netmask 255.255.255.0 –Gateway 10.161.22.1`

    ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image30.png)
9. İlk kurulum işlemleri tamamlandıktan ve cihaz önyüklendikten sonra cihaz başlık metnini görürsünüz. Cihazı yönetmek için başlık metninde görüntülenen IP adresini ve URL'yi not edin. Sanal dizinizin web Arama Ara birimi'ne bağlanmak ve yerel kurulum ve kaydı tamamlamak için bu IP adresini kullanın.

   ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image31m.png)
10. (İsteğe bağlı) Bu adımı yalnızca Cihazınızı Devlet Bulutu'nda dağıtıyorsanız gerçekleştirin. Artık cihazınızda Abd Federal Bilgi İşlem Standardı (FIPS) modunu etkinleştireceksiniz. FIPS 140 standardı, hassas verilerin korunması için ABD Federal hükümet bilgisayar sistemleri tarafından kullanılmak üzere onaylanan şifreleme algoritmalarını tanımlar.

    1. FIPS modunu etkinleştirmek için aşağıdaki cmdlet'i çalıştırın:

        `Enable-HcsFIPSMode`
    2. Fips modunu etkinleştirdikten sonra cihazınızı yeniden başlatın, böylece şifreleme doğrulamaları etkili olur.

       > [!NOTE]
       > Aygıtınızda FIPS modunu etkinleştirebilir veya devre dışı kullanabilirsiniz. Aygıtı FIPS ve FIPS olmayan mod arasında alternatif olarak değiştirin desteklenmez.
       >
       >

Cihazınız minimum yapılandırma gereksinimlerini karşılamazsa, aşağıdaki hatayı başlık metninde (aşağıda gösterilmiştir) görürsünüz. Cihaz yapılandırmasını minimum gereksinimleri karşılayacak şekilde değiştirin. Ardından cihazı yeniden başlatıp bağlantı kurabilirsiniz. Adım 1'deki minimum yapılandırma gereksinimlerine bakın: Ana bilgisayar sisteminin minimum sanal dizi gereksinimlerini karşıladığından emin olun.

![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image32.png)

Yerel web Web Web Web Web'i kullanarak ilk yapılandırma sırasında başka bir hatayla karşılaşıyorsanız, aşağıdaki iş akışlarına bakın:

* [Web UI kurulumunun giderilen sorun gidermek](storsimple-ova-web-ui-admin.md#troubleshoot-web-ui-setup-errors)için tanılama testlerini çalıştırın.
* [Günlük paketi oluşturun ve günlük dosyalarını görüntüleyin.](storsimple-ova-web-ui-admin.md#generate-a-log-package)

## <a name="next-steps"></a>Sonraki adımlar
* [StorSimple Virtual Array'inizi dosya sunucusu olarak ayarlama](storsimple-virtual-array-deploy3-fs-setup.md)
* [StorSimple Virtual Array'inizi iSCSI sunucusu olarak ayarlama](storsimple-virtual-array-deploy3-iscsi-setup.md)
