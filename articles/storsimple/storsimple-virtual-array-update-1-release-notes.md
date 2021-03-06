---
title: StorSimple Virtual Array Update 1.0 sürüm notları
description: Güncelleştirme 1.0 çalıştıran StorSimple Virtual Array için kritik açık sorunları ve çözümleri açıklar.
author: alkohli
ms.service: storsimple
ms.topic: conceptual
ms.date: 11/02/2017
ms.author: alkohli
ms.openlocfilehash: 48dec3a87ab540af224ae4ac59dd37cee7c9d0ee
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76271334"
---
# <a name="storsimple-virtual-array-update-10-release-notes"></a>StorSimple Virtual Array Update 1.0 sürüm notları

## <a name="overview"></a>Genel Bakış

Aşağıdaki sürüm notları, Microsoft Azure StorBasit Sanal Dizi güncelleştirmeleri için kritik açık sorunları ve çözülmüş sorunları tanımlar.

Sürüm notları sürekli olarak güncelleştirilir ve geçici çözüm gerektiren kritik sorunlar keşfedildikçe eklenir. StorSimple Virtual Array'inizi dağıtmadan önce, sürüm notlarında yer alan bilgileri dikkatle inceleyin.

Güncelleme 1.0 yazılım sürümü **10.0.10296.0**karşılık gelir.

> [!IMPORTANT]
> - Güncelleştirmeler kesintiye uğrar ve cihazınızı yeniden başlatın. G/Ç devam ediyorsa, aygıt kapalı kalma süresine neden olur. Güncelleştirmenin nasıl uygulanacağı yla ilgili ayrıntılı talimatlar için [Güncelleştirme 1.0'ı yükleyin.](storsimple-virtual-array-install-update-1.md)
>
> - Güncelleme 1 yalnızca aygıtınız 0.6 Güncelle'yi çalıştırıyorsa Azure portalı üzerinden kullanılabilir.

## <a name="whats-new-in-update-10"></a>Update 1.0'daki yenilikler

**Güncelleştirme 1.0, StorSimple Device Manager hizmetinin kimlik doğrulamasına ilişkin değişiklikleri içerir ve en erken dağıtımınız gerekir.** Bu güncelleştirme, aşağıdaki geliştirmeleri ve hata düzeltmelerini içerir:

 - **StorSimple Device Manager hizmetiyle kimlik doğrulaması yapmak için Azure Active Directory 'nin (AAD) kullanımı** – Güncelleme 1.0'dan itibaren, Azure Etkin Dizini StorSimple Device Manager hizmetiyle kimlik doğrulaması yapmak için kullanılır. Eski kimlik doğrulama mekanizması Aralık 2017'ye kadar amortismana hazır olacak. Tüm kullanıcıların güvenlik duvarı kurallarına yeni kimlik doğrulama URL'lerini eklemesi gerekir. Daha fazla bilgi için, [StorSimple Virtual Array'iniz için Ağ gereksinimlerinde](storsimple-ova-system-requirements.md)listelenen kimlik doğrulama URL'lerine gidin.
 
    Kimlik doğrulama URL'si güvenlik duvarı kurallarına dahil edilmezse, kullanıcılar StorSimple aygıtlarının hizmetle birlikte kimlik doğrulaması yapamadığını belirten kritik bir uyarı görür. Kullanıcılar bu uyarıyı görürse, yeni kimlik doğrulama URL'sini eklemeleri gerekir. Daha fazla bilgi için [StorSimple ağ uyarılarına](storsimple-virtual-array-manage-alerts.md)gidin.

 - **Performans iyileştirmesi** - Bulut okuma, katman-ins ve kademe çıkışlarının hızını artırmak için çeşitli hata düzeltmeleri yapıldı. Sonuç olarak, iSCSI ve dosya sunucusu aygıtları için hem yedekleme hem de geri yükleme performansı artırıldı.

 - **Çöp toplama geliştirme** - Bu sürüm, aygıt ve depolama hesabı iki uzak bölgede olduğunda çöp toplama döngüsünün performansını artıran hata düzeltmeleri vardır.

 - **Günlüğe kaydetme geliştirme** - Bu sürüm, çöp toplama ve G/Ç yolu ile ilgili günlüğe kaydetme de iyileştirmeler içerir.


## <a name="issues-fixed-in-update-10"></a>Güncelleştirme 1.0'da düzeltilen sorunlar

Aşağıdaki tablo, bu sürümde düzeltilen sorunların bir özetini sağlar.

| Hayır. | Özellik | Sorun |
| --- | --- | --- |
| 1 |AAD tabanlı kimlik doğrulama| Bu sürüm, AAD'nin StorSimple Device Manager ile kimlik doğrulaması yapmasına olanak tanıyan değişiklikler içerir.|
| 2 |Atık toplama| Bu sorun, aygıt ve depolama hesaplarının farklı bölgelerde olduğu bir müşteri sitesinde raporedildi ve müşteri aralıklı ağ hataları bildirdi ve böylece faturalandırmayı etkiledi. Bu sürümde, bu sorun giderildi. |
| 3 |Performans| Bu sürüm, geri yükleme/bulut okuma/katman performansı geliştirme ile sonuçlanan değişiklikler içerir.|
| 4 |Güncelleştirme| Önceki sürümde bir müşteri sitesinde yedekleme hatalarıile sonuçlanan güncelleştirme yle ilgili bir sorun vardı. Bu sorun bu sürümde giderilmiştir.|

## <a name="known-issues-in-update-10"></a>Güncelleştirme 1.0'da bilinen sorunlar

Aşağıdaki tablo, StorSimple Virtual Array için bilinen sorunların bir özetini sağlar ve önceki sürümlerden belirtilen sorunları içerir.

| Hayır. | Özellik | Sorun | Geçici çözüm/yorumlar |
| --- | --- | --- | --- |
| **1.** |Güncelleştirmeler |Önizleme sürümünde oluşturulan sanal diziler desteklenen genel kullanılabilirlik sürümüne güncelleştirilemez. |Bu sanal diziler, olağanüstü durum kurtarma (DR) iş akışı kullanılarak Genel Kullanılabilirlik sürümü için başarısız olunmalıdır. |
| **2.** |Sağlanan veri diski |Belirli bir boyutta bir veri diski oluşturduktan ve ilgili StorSimple Sanal Dizisini oluşturduktan sonra, veri diskini genişletmemeniz veya küçültmemeniz gerekir. Yapmaya çalışmak, aygıtın yerel katmanlarındaki tüm verilerin kaybolmasına neden olabilir. | |
| **3.** |Grup ilkesi |Bir aygıt etki alanına katıldığında, grup ilkesi uygulamak aygıt çalışmasını olumsuz etkileyebilir. |Sanal dizinizin Active Directory için kendi kuruluş biriminde (OU) olduğundan ve buna grup ilkesi nesnesi (GPO) uygulanmadığından emin olun. |
| **4.** |Yerel web Web UI |Internet Explorer'da (IE ESC) gelişmiş güvenlik özellikleri etkinse, Sorun Giderme veya Bakım gibi bazı yerel web ui sayfaları düzgün çalışmayabilir. Bu sayfalardaki düğmeler de çalışmayabilir. |Internet Explorer'daki gelişmiş güvenlik özelliklerini kapatın. |
| **5.** |Yerel web Web UI |Hyper-V sanal makinede, web arabirimi ndeki ağ arabirimleri 10 Gbps arabirimi olarak görüntülenir. |Bu davranış Hyper-V'nin bir yansımasıdır. Hyper-V her zaman sanal ağ bağdaştırıcıları için 10 Gbps gösterir. |
| **6.** |Katmanlı hacimler veya hisseler |StorSimple katmanlı birimleriyle çalışan uygulamalar için bayt aralığı kilitleme desteklenmez. Bayt aralığı kilitleme etkinse, StorSimple katmanlama çalışmaz. |Önerilen önlemler şunlardır: <br></br>Uygulama mantığınızda bayt aralığı kilitlemeyi kapatın.<br></br>Katmanlı birimlerin aksine, bu uygulama için verileri yerel olarak sabitlenmiş birimlere koymayı seçin.<br></br>*Uyarı*: Yerel olarak sabitlenmiş hacimler ve bayt aralığı kilitleme etkinleştirildiğinde, yerel olarak sabitlenmiş birim, geri yükleme tamamlanmadan önce bile çevrimiçi olabilir. Bu gibi durumlarda, geri yükleme devam ediyorsa, geri yüklemenin tamamlanmasını beklemeniz gerekir. |
| **7.** |Katmanlı hisseler |Büyük dosyalarla çalışmak, yavaş katman dışına çıkmasına neden olabilir. |Büyük dosyalarla çalışırken, en büyük dosyanın paylaşım boyutunun %3'ünden daha küçük olduğunu öneririz. |
| **8.** |Hisseler için kullanılan kapasite |Payla ilgili veri olmadığında hisse tüketimini görebilirsiniz. Bu tüketim, hisseler için kullanılan kapasitenin meta verileri içermesinden dir. | |
| **9.** |Olağanüstü durum kurtarma |Bir dosya sunucusunun olağanüstü durum kurtarma durumunu yalnızca kaynak aygıtla aynı etki alanına gerçekleştirebilirsiniz. Bu sürümde, başka bir etki alanında hedef aygıta olağanüstü durum kurtarma desteklenmez. |Bu daha sonraki bir sürümde uygulanır. Daha fazla bilgi [için, StorSimple Virtual Array için Failover ve olağanüstü durum kurtarma](storsimple-virtual-array-failover-dr.md) gidin |
| **10.** |Azure PowerShell |StorSimple Sanal Diziler bu sürümde Azure PowerShell üzerinden yönetilemez. |Sanal cihazların tüm yönetimi Azure portalı ve yerel web web ui üzerinden yapılmalıdır. |
| **11.** |Parola değiştirme |Sanal dizi aygıt konsolu yalnızca en-us klavye formatında giriş kabul eder. | |
| **12.** |CHAP |Oluşturulduktan sonra CHAP kimlik bilgileri kaldırılamaz. Ayrıca, CHAP kimlik bilgilerini değiştirirseniz, birimleri çevrimdışı duruma getirmeniz ve değişikliğin etkili olması için bunları çevrimiçi duruma getirmeniz gerekir. |Bu sorun daha sonraki bir sürümde ele alınmıştır. |
| **13.** |iSCSI sunucusu |iSCSI birimi için görüntülenen 'Kullanılmış depolama' StorSimple Device Manager hizmetinde ve iSCSI ana bilgisayarda farklı olabilir. |iSCSI ana bilgisayarı dosya sistemi görünümüne sahiptir.<br></br>Aygıt, birim maksimum boyuttayken ayrılan blokları görür. |
| **14.** |Dosya sunucusu |Klasördeki bir dosyada onunla ilişkili alternatif veri akışı (ADS) varsa, ADS olağanüstü durum kurtarma, klonlama ve Öğe Düzeyi Kurtarma yoluyla yedeklenmez veya geri yüklenmez. | |
| **15.** |Dosya sunucusu |Sembolik bağlantılar desteklenmez. | |
| **16.** |Dosya sunucusu |StorSimple Virtual Array dosya sunucusunda kopyalandığında veya depolandığında Windows Şifreleme Dosya Sistemi (EFS) tarafından korunan dosyalar, desteklenmeyen bir yapılandırmayla sonuçlanır.  | |
| **17.** |Güncelleştirmeler |Hata kodu görürseniz: 2359302 (hex 0x240006) yerel ui üzerinden bir düzeltme yüklemeye çalışırken, bu düzeltmenin cihazınıza zaten yüklenmiş olduğu anlamına gelir.   | |
| **18.** |Güncelleştirmeler |Sanal dizinizde Güncelleştirme 1'i yüklemek için yerel web kullanıcı arama aylığını kullanıyorsanız, Güncelleştirme 0.6'yı çalıştırdığınızdan emin olmalısınız. Güncelleştirme 0.6'dan daha düşük bir sürüm çalıştırıyorsanız, önce 0.6'yı yüklemeniz ve ardından Güncelleştirme 1'i uygulamanız gerekir. Güncelleştirme 1.0'ı doğrudan güncelleştirme öncesi 0.6 sürümünden yüklerseniz, bazı güncelleştirmeleri kaçırırsınız ve izleme grafikleri çalışmaz.   | |


## <a name="next-steps"></a>Sonraki adımlar
StorSimple Virtual Array'inize [Güncelleme 1.0'ı yükleyin.](storsimple-virtual-array-install-update-1.md)

## <a name="references"></a>Başvurular
Eski bir sürüm notu mu arıyorsunuz? Şuraya gidin:
*  [StorSimple Sanal Dizi Güncelleme 0.6 Sürüm Notları](storsimple-virtual-array-update-06-release-notes.md)
* [StorSimple Sanal Dizi Güncelleme 0.5 Sürüm Notları](storsimple-virtual-array-update-05-release-notes.md)
* [StorSimple Sanal Dizi Güncelleme 0.4 Sürüm Notları](storsimple-virtual-array-update-04-release-notes.md)
* [StorSimple Sanal Dizi Güncelleme 0.3 Sürüm Notları](storsimple-ova-update-03-release-notes.md)
* [StorSimple Virtual Array Update 0.1 ve 0.2 Sürüm Notları](storsimple-ova-update-01-release-notes.md)
* [StorSimple Virtual Array Genel Kullanılabilirlik Sürüm Notları](storsimple-ova-pp-release-notes.md)
