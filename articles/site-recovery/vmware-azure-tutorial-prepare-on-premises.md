---
title: Azure Site Kurtarma ile VMware VM olağanüstü durum kurtarma için hazırlanın
description: Azure Site Recovery hizmetini kullanarak şirket içi VMware sunucularını Azure’a olağanüstü durum kurtarmaya hazırlamayı öğrenin.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: tutorial
ms.date: 11/12/2019
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: 4969a1f14e53aabf79495e179213f9763d4c8803
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/24/2020
ms.locfileid: "79238863"
---
# <a name="prepare-on-premises-vmware-servers-for-disaster-recovery-to-azure"></a>Şirket içi VMware sunucularını Azure’a olağanüstü durum kurtarmaya hazırlama

Bu makalede, [Azure Site Kurtarma](site-recovery-overview.md) hizmetlerini kullanarak şirket içi VMware sunucularını Azure'a olağanüstü durum kurtarma için nasıl hazırlayacağıaçıklanmaktadır. 

Bu, şirket içi VMware sanal makineleri için Azure’da olağanüstü durum kurtarmanın nasıl ayarlanacağını gösteren serideki ikinci öğreticidir. Birinci öğreticide, VMware olağanüstü durum kurtarma için gerekli [Azure bileşenlerini ayarladık](tutorial-prepare-azure.md).


Bu makalede şunları öğreneceksiniz:

> [!div class="checklist"]
> * VM keşfini otomatikleştirmek için vCenter sunucusunda veya vSphere ESXi ana bilgisayarda bir hesap hazırlayın.
> * VMware VM'lere Mobilite hizmetinin otomatik olarak yüklenmesi için bir hesap hazırlayın.
> * VMware sunucusu ve VM gereksinimlerini ve desteğini gözden geçirin.
> * Başarısız olduktan sonra Azure VM'lere bağlanmaya hazırlanın.

> [!NOTE]
> Öğreticiler, bir senaryo için en basit dağıtım yolunu gösterir. Mümkün olduğunca varsayılan seçenekleri kullanır ve tüm olası ayarları ve yolları göstermez. Ayrıntılı talimatlar için, Site Kurtarma İçeriği Tablosunun Nasıl Yapılacağı bölümündeki makaleyi inceleyin.

## <a name="before-you-start"></a>Başlamadan önce

[Azure'u bu serinin ilk öğreticisinde](tutorial-prepare-azure.md)açıklandığı şekilde hazırladığınıza emin olun.

## <a name="prepare-an-account-for-automatic-discovery"></a>Otomatik bulma için bir hesap hazırlama

Site Recovery aşağıdakiler için VMware sunucularına erişmesi gerekir:

- VM'leri otomatik olarak bulma. En az bir salt okunur hesap gereklidir.
- Çoğaltma, yük devretme ve yeniden çalıştırmayı yönetme. Diskleri oluşturma ve kaldırma ve VM’leri çalıştırma gibi işlemleri gerçekleştirebilen bir hesabınızın olması gerekir.

Hesabı aşağıdaki gibi oluşturun:

1. Ayrılmış bir hesap kullanmak için, rolü vCenter düzeyinde oluşturun. Role **Azure_Site_Recovery** gibi bir ad verin.
2. Role aşağıdaki tabloda özetlenen izinleri atayın.
3. vCenter sunucusu veya vSphere ana bilgisayarında bir kullanıcı oluşturun. Rolü kullanıcıya atayın.

### <a name="vmware-account-permissions"></a>VMware hesap izinleri

**Görev** | **Rol/İzinler** | **Şey**
--- | --- | ---
**VM bulma** | En az bir salt okunur kullanıcı<br/><br/> Veri Merkezi nesnesi –> Alt Nesneye Yay, role=Read-only | Kullanıcı veri merkezi düzeyinde atandı ve bu veri merkezindeki tüm nesnelere erişimi var.<br/><br/> Erişimi kısıtlamak **için, Yayı alt nesneye** (vSphere ana bilgisayarlar, veri depoları, VM'ler ve ağlar) **Access'le erişim yok** rolünü atayın.
**Tam çoğaltma, yük devretme, yeniden çalışma** |  Gerekli izinlere sahip bir rol (Azure_Site_Recovery) oluşturup rolü VMware kullanıcısı veya grubuna atayın<br/><br/> Veri Merkezi nesnesi –> Alt Nesneye Yay, role=Azure_Site_Recovery<br/><br/> Veri deposu -> Alan ayırma, veri deposuna göz atma, düşük düzeyli dosya işlemleri, dosyayı kaldırma, sanal makine dosyalarını güncelleştirme<br/><br/> Ağ -> Ağ ataması<br/><br/> Kaynak -> VM’yi kaynak havuzuna atama, kapalı VM’yi geçirme, açık VM’yi geçirme<br/><br/> Görevler -> Görev oluşturma, görevi güncelleştirme<br/><br/> Sanal makine -> Yapılandırma<br/><br/> Sanal makine -> Etkileşim -> soruyu yanıtlama, cihaz bağlantısı, CD ortamını yapılandırma, disket ortamını yapılandırma, kapatma, açma, VMware araçlarını yükleme<br/><br/> Sanal makine -> Envanter -> Oluşturma, kaydetme, kaydı kaldırma<br/><br/> Sanal makine -> Sağlama -> Sanal makine indirmeye izin verme, Sanal makine dosyalarını karşıya yüklemeye izin verme<br/><br/> Sanal makine -> Anlık görüntüler -> Anlık görüntüleri kaldırma | Kullanıcı veri merkezi düzeyinde atandı ve bu veri merkezindeki tüm nesnelere erişimi var.<br/><br/> Erişimi kısıtlamak **için, Yayı alt nesneye** (vSphere ana bilgisayarlar, veri depoları, VM'ler ve ağlar) **Access'le erişim yok** rolünü atayın.

## <a name="prepare-an-account-for-mobility-service-installation"></a>Bir hesabı Mobility hizmeti yüklemesi için hazırlama

Çoğaltmak istediğiniz makinelerde Mobility hizmeti yüklü olmalıdır. Makine için çoğaltmayı etkinleştirdiğinizde Site Recovery bu hizmeti göndererek yükleme işlemi yapabileceği gibi, bunu el ile yükleyebilir veya yükleme araçlarını kullanabilirsiniz.

- Bu öğreticide, Mobility hizmetini göndererek yükleme yoluyla yükleyeceğiz.
- Bu göndererek yükleme için, Site Recovery’nin sanal makineye erişmek için kullanabileceği bir hesap hazırlamanız gerekir. Azure konsolunda olağanüstü durum kurtarmayı ayarlarken bu hesabı belirtirsiniz.

Hesabı aşağıdaki gibi hazırlayın:

VM üzerinde yükleme izinleri ile bir etki alanı veya yerel hesap hazırlayın.

- **Windows VM'leri**: Windows VM'lerine yüklemek için, bir etki alanı hesabı kullanmıyorsanız yerel makinede Uzak Kullanıcı Erişim denetimini devre dışı bırakın. Bunu yapmak için kayıt defterinde > **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System** altında **LocalAccountTokenFilterPolicy** adlı DWORD girişini 1 değeriyle ekleyin.
- **Linux VM'leri**: Linux VM'lerine yüklemek için, kaynak Linux sunucusunda bir kök hesabı hazırlayın.


## <a name="check-vmware-requirements"></a>VMware gereksinimlerini denetleme

VMware sunucularının ve sanal makinelerin gereksinimlerle uyumlu olduğundan emin olun.

1. VMware sunucusu gereksinimlerini [doğrulayın](vmware-physical-azure-support-matrix.md#on-premises-virtualization-servers).
2. Linux VM'leri için dosya sistemini ve depolama gereksinimlerini [denetleyin](vmware-physical-azure-support-matrix.md#linux-file-systemsguest-storage). 
3. Şirket içi [ağ](vmware-physical-azure-support-matrix.md#network) ve [depolama](vmware-physical-azure-support-matrix.md#storage) desteğini denetleyin. 
4. Yük devretmenin ardından [Azure ağ](vmware-physical-azure-support-matrix.md#azure-vm-network-after-failover), [depolama](vmware-physical-azure-support-matrix.md#azure-storage) ve [işlem](vmware-physical-azure-support-matrix.md#azure-compute) için nelerin desteklendiğini denetleyin.
5. Azure’a çoğalttığınız şirket içi sanal makineleriniz, [Azure sanal makinesi gereksinimleri](vmware-physical-azure-support-matrix.md#azure-vm-requirements) ile uyumlu olmalıdır.
6. Linux sanal makinelerde, cihaz adı veya montaj noktası adı benzersiz olmalıdır. İki aygıt/montaj noktasının aynı ada sahip olmadığından emin olun. Adın büyük/küçük harf duyarlı olmadığını unutmayın. Örneğin, _aygıt1_ ve _Device1_ ile aynı VM için iki aygıt adlandırmaya izin verilmez.


## <a name="prepare-to-connect-to-azure-vms-after-failover"></a>Yük devretmeden sonra Azure VM'lerine bağlanmak için hazırlık yapma

Yük devretmeden sonra, şirket içi ağınızdan Azure VM'lerine bağlanmak isteyebilirsiniz.

Yük devretmeden sonra RDP kullanarak Windows VM’lerine bağlanmak için aşağıdakileri yapın:

- **İnternet erişimi**. Yük devretmeden önce, yük devretmeden önce şirket içi VM’de RDP’yi etkinleştirin. TCP ve UDP kurallarının **Ortak** profil için eklendiğinden ve tüm profillerde **Windows Güvenlik Duvarı** > **İzin Verilen Uygulamalar** içinde RDP’ye izin verildiğinden emin olun.
- **Konumdan konuma VPN erişimi**:
    - Yük devretmeden önce, şirket içi makinede RDP’yi etkinleştirin.
    - RDP'ye Etki Alanı ve Özel ağlar için **Windows Güvenlik Duvarı** -> İzin Verilen **uygulamalarda ve** **özelliklerde** izin verilmelidir.
    - İşletim sisteminin SAN ilkesinin **OnlineAll** olarak ayarlandığından emin olun. [Daha fazla bilgi edinin](https://support.microsoft.com/kb/3031135).
- Bir yük devretme tetiklediğinizde VM’de bekleyen Windows güncelleştirmelerinin olmaması gerekir. Varsa, güncelleştirme tamamlanana kadar sanal makinede oturum açamazsınız.
- Yük devretmeden sonra Windows Azure VM’sinde, VM’nin bir ekran görüntüsünü görmek için **Önyükleme tanılaması**’nı kontrol edin. Bağlanamıyorsanız, VM’nin çalıştığından emin olun ve şu [sorun giderme ipuçlarını](https://social.technet.microsoft.com/wiki/contents/articles/31666.troubleshooting-remote-desktop-connection-after-failover-using-asr.aspx) gözden geçirin.

Yük devretmeden sonra SSH kullanarak Linux VM’lerine bağlanmak için aşağıdakileri yapın:

- Yük devretmeden önce şirket içi makinede Secure Shell hizmetinin sistem önyüklemesinde otomatik olarak başlatılmak için ayarlandığından emin olun.
- Güvenlik duvarı kurallarının SSH bağlantısına izin verdiğinden emin olun.
- Yük devretmeden sonra Azure VM’sinde, yük devredilen VM üzrindeki ağ güvenlik grubu kuralları ve VM’nin bağlı olduğu Azure alt ağı için SSH bağlantı noktasına gelen bağlantılara izin verin.
- VM için bir [ortak IP adresi ekleyin](site-recovery-monitoring-and-troubleshooting.md).
- VM’nin bir ekran görüntüsünü görmek için **Önyükleme tanılaması**’nı kontrol edebilirsiniz.


## <a name="failback-requirements"></a>Yeniden çalışma gereksinimleri
Şirket içi sitenize geri dönmeyi planlıyorsanız, [geri dönüş için bir](vmware-azure-reprotect.md#before-you-begin)dizi ön koşul vardır. Bunları şimdi hazırlayabilirsin, ama hazırlamana gerek yok. Azure'da başarısız olduktan sonra hazırlanabilirsiniz.



## <a name="next-steps"></a>Sonraki adımlar

Olağanüstü durum kurtarma yı ayarlayın. Birden çok VM kopyalayıyorsanız, kapasiteyi planlayın.
> [!div class="nextstepaction"]
> [VMware VM'ler](vmware-azure-tutorial.md)
> [kapasite planlamayı gerçekleştirmek](site-recovery-deployment-planner.md)için Azure'da olağanüstü durum kurtarma yı ayarlayın.
