---
title: Azure Monitör'de Tel Veri çözümü | Microsoft Dokümanlar
description: Havale verileri, Log Analytics aracıları ile bilgisayarlardan gelen birleştirilmiş ağ ve performans verileridir. Verilerin bağıntısını sağlamanıza yardımcı olmak için ağ verileri günlük verilerinizle birleştirilir.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 10/03/2018
ms.openlocfilehash: ee7a2f49641eb0cfe1f8a4bffb44c7f8642408fa
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77670653"
---
# <a name="wire-data-20-preview-solution-in-azure-monitor"></a>Azure Monitor'da Tel Veri 2.0 (Önizleme) çözümü

![Wire Data sembolü](media/wire-data/wire-data2-symbol.png)

Wire verileri, windows'a bağlı ve Linux'a bağlı bilgisayarlardan Log Analytics aracısı ile toplanan ve ortamınızda Operations Manager tarafından izlenenler de dahil olmak üzere birleştirilmiş ağ ve performans verileridir. Verilerin bağıntısını sağlamanıza yardımcı olmak için ağ verileri diğer günlük verilerinizle birleştirilir.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

Günlük Analizi aracısına ek olarak, Wire Data çözümü BT altyapınızdaki bilgisayarlara yüklediğiniz Microsoft Bağımlılık Aracılarını kullanır. Bağımlılık Aracıları [OSI modelinde](https://en.wikipedia.org/wiki/OSI_model) 2-3 ağ düzeyleri için bilgisayarlarınıza ve bilgisayarlarınızdan gönderilen ağ verilerini, ayrıca kullanılan çeşitli protokollerle bağlantı noktalarını izler. Veriler daha sonra aracılar kullanılarak Azure Monitor'a gönderilir.  

>[!NOTE]
>Hizmet Haritası'nı zaten dağıttıysanız veya Sanal Taşıtlar için Hizmet Haritası veya [Azure Monitörü](../../azure-monitor/insights/vminsights-overview.md)düşünüyorsanız, Topladıkları ve Depoladıkları Yeni bağlantı ölçümleri veri seti, Havale Verileri ile karşılaştırılabilir bilgiler sağlayan Azure Monitor'da dır.

Varsayılan olarak, Azure Monitor, Windows ve Linux'ta yerleşik sayaçlardan gelen CPU, bellek, disk ve ağ performans verilerinin yanı sıra belirtebileceğiniz diğer performans sayaçları için verileri günlüğe kaydeder. Her aracı için, alt ağlar ve bilgisayar tarafından kullanılmakta olan uygulama düzeyi protokoller de dahil olmak üzere, ağ ve diğer verileri toplama işlemi gerçek zamanlı olarak yapılır.  Wire Data, alttaki TCP aktarım katmanında değil uygulama düzeyindeki ağ verilerine bakar. Çözüm tek tek ACK'lere ve SYN'lere bakmaz. Karşılıklı anlaşma tamamlandıktan sonra, bu canlı bir bağlantı olarak kabul edilir ve Bağlandı olarak işaretlenir. Söz konusu bağlantı, her iki taraf da yuvanın açık olduğunu ve verilerin ileri ve geri geçiş yapabildiğini kabul ettiği sürece canlı kalır. Her iki taraf da bağlantıyı kapattıktan sonra Bağlantısı kesildi olarak işaretlenir.  Bu nedenle, yalnızca başarıyla tamamlanan paketlerin bant genişliğini sayar; yeniden göndermeleri veya başarısız paketleri raporlamaz.

[Cisco'nun NetFlow protokolüyle](https://www.cisco.com/c/en/us/products/collateral/ios-nx-os-software/ios-netflow/prod_white_paper0900aecd80406232.html)[sFlow](http://www.sflow.org/)'u veya başka bir yazılımı kullandıysanız, sinyal verilerinden gördüğünüz istatistikler ve veriler tanıdık gelecektir.

Yerleşik Günlük arama sorgusu türlerinden bazıları şunlardır:

- Sinyal verileri sağlayan aracılar
- Sinyal verilerini sağlayan aracıların IP adresleri
- IP adresinden giden iletişimler
- Uygulama protokolleri tarafından gönderilen bayt sayısı
- Uygulama hizmeti tarafından gönderilen bayt sayısı
- Farklı protokoller tarafından alınan bayt sayısı
- IP sürümü tarafından gönderilen ve alınan toplam bayt sayısı
- Güvenilir şekilde ölçülmüş bağlantılarda ortalama gecikme süresi
- Ağ trafiği başlatan veya alan bilgisayar işlemleri
- İşlem için ağ trafiği miktarı

Sinyal verilerini kullanarak arama yaptığınızda, en önemli aracılar ve en önemli protokoller hakkındaki bilgileri görüntülemek için verileri filtreleyebilir ve gruplandırabilirsiniz. Öte yandan bazı bilgisayarların (IP adresleri/MAC adresleri) birbiriyle ne zaman, ne kadar süreyle iletişim kurduğunu ve ne kadar veri gönderildiği de görüntüleyebilirsiniz. Temelde, ağ trafiği hakkında arama tabanlı olan meta verileri görüntülersiniz.

Bununla birlikte, meta verileri görüntülediğiniz için bunların kapsamlı bir sorun giderme işleminde kullanışlı olması şart değildir. Azure Monitor'daki kablo verileri ağ verilerinin tam olarak ele geçirilmesi değildir.  Derin paket düzeyi sorun giderme işlemlerine yönelik değildir. Aracıyı kullanmanın diğer toplama yöntemleriyle karşılaştırıldığında avantajı, cihazları yüklemek, ağ anahtarlarınızı yeniden yapılandırmak veya karmaşık yapılandırmalar gerçekleştirmek zorunda kalmamanızdır. Sinyal verileri yalnızca aracı tabanlıdır; bilgisayara aracıyı yüklersiniz ve o da kendi ağ trafiğini izler. Bir diğer avantajı da, kullanıcının yapı katmanına sahip olmadığı bulut sağlayıcılarında ya da barındıran hizmet sağlayıcısında veya Microsoft Azure'da çalıştırılan iş yüklerini izlemek istediğinizde ortaya çıkar.

## <a name="connected-sources"></a>Bağlı kaynaklar

Wire Data verilerini Microsoft Bağımlılık Aracısı'ndan alır. Bağımlılık Aracısı, Azure Monitor'a olan bağlantıları için Log Analytics aracısına bağlıdır. Bu, bir sunucunun Log Analytics aracısını Bağımlılık aracısıyla yüklü ve yapılandırmış olması gerektiği anlamına gelir. Aşağıdaki tabloda Wire Data çözümü tarafından desteklenen bağlı kaynaklar açıklanır:

| **Bağlı kaynak** | **Desteklenen** | **Açıklama** |
| --- | --- | --- |
| Windows aracıları | Evet | Wire Data, Windows aracı bilgisayarlarından gelen verileri analiz eder ve toplar. <br><br> Windows için [Log Analytics aracısına](../platform/agent-windows.md)ek olarak, Windows aracıları Microsoft Bağımlılık aracısı gerektirir. İşletim sistemi sürümlerinin tam listesi için bkz. [Desteklenen işletim sistemleri](vminsights-enable-overview.md#supported-operating-systems). |
| Linux aracıları | Evet | Wire Data, Linux aracı bilgisayarlarından gelen verileri analiz eder ve toplar.<br><br> Linux için [Log Analytics aracısına](../learn/quick-collect-linux-computer.md)ek olarak, Linux aracıları Microsoft Bağımlılık aracısına ihtiyaç duyar. İşletim sistemi sürümlerinin tam listesi için bkz. [Desteklenen işletim sistemleri](vminsights-enable-overview.md#supported-operating-systems). |
| System Center Operations Manager yönetim grubu | Evet | Wire Data, bağlantılı bir [System Center Operations Manager yönetim grubunda](../platform/om-agents.md) Windows ve Linux aracılarından gelen verileri analiz eder ve toplar. <br><br> System Center Operations Manager aracı bilgisayarından Azure Monitor'a doğrudan bağlantı gereklidir. |
| Azure depolama hesabı | Hayır | Wire Data verileri aracı bilgisayarlardan topladığından, Azure Depolama'dan toplayacağı veri yoktur. |

Windows'da, Microsoft İzleme Aracısı (MMA), hem Sistem Merkezi İşlemleri Yöneticisi hem de Azure Monitor tarafından veri toplamak ve göndermek için kullanılır. İçeriğe bağlı olarak, aracı sistem merkezi operasyon yöneticisi aracısı, Log Analytics aracısı, MMA veya Doğrudan Aracı olarak adlandırılır. System Center Operations Manager ve Azure Monitor, MMA'nın biraz farklı sürümlerini sağlar. Bu sürümlerin her biri Sistem Merkezi İşlemleri Yöneticisi'ne, Azure Monitor'a veya her ikisine de rapor verebilir.

Linux'ta, Linux için Log Analytics aracısı veri toplar ve Azure Monitor'a gönderir. Wire Data'yı doğrudan Azure Monitor'a bağlı aracılara sahip sunucularda veya Sistem Merkezi Operasyon Yöneticisi yönetim grupları aracılığıyla Azure Monitor'a bağlanan sunucularda kullanabilirsiniz.

Bağımlılık aracısı herhangi bir veriyi kendisi iletmez ve güvenlik duvarlarında veya bağlantı noktalarında değişiklik gerektirmez. Wire Data'daki veriler, Log Analytics aracısı tarafından her zaman doğrudan veya Log Analytics ağ geçidi aracılığıyla Azure Monitor'a iletilir.

![aracı diyagramı](./media/wire-data/agents.png)

Azure Monitor'a bağlı bir yönetim grubu olan bir Sistem Merkezi Operasyon Yöneticisi kullanıcısıysanız:

- Sistem Merkezi Operasyon Yöneticisi aracılarınız Azure Monitor'a bağlanmak için internete erişebiliyorsa ek yapılandırma gerekmez.
- Sistem Merkezi Operasyon Yöneticisi aracılarınız Azure Monitor'a internet üzerinden erişemediğinde, Log Analytics ağ geçidini Sistem Merkezi Operasyon Yöneticisi ile çalışacak şekilde yapılandırmanız gerekir.

Windows veya Linux bilgisayarlarınız hizmete doğrudan bağlanamıyorsa, Log Analytics aracısını Log Analytics ağ geçidini kullanarak Azure Monitor'a bağlanacak şekilde yapılandırmanız gerekir. Log Analytics ağ geçidini [Microsoft Download Center'dan](https://www.microsoft.com/download/details.aspx?id=52666)indirebilirsiniz.

## <a name="prerequisites"></a>Ön koşullar

- [İçgörü ve Analiz](https://www.microsoft.com/cloud-platform/operations-management-suite-pricing) çözüm teklifi gereklidir.
- Wire Data çözümünü önceki sürümünü kullanıyorsanız, önce o sürümü kaldırmalısınız. Ancak özgün Wire Data çözümüyle yakalanmış olan tüm veriler Wire Data 2.0'da ve günlük aramasında yine kullanılabilir.
- Bağımlılık aracısını yüklemek veya kaldırmak için yönetici ayrıcalıkları gereklidir.
- Bağımlılık aracısı, 64 bit işletim sistemine sahip bir bilgisayara yüklenmelidir.

### <a name="operating-systems"></a>İşletim sistemleri

Aşağıdaki bölümlerde Bağımlılık aracısı için desteklenen işletim sistemleri listelenir. Wire Data hiçbir işletim sisteminin 32 bit mimarilerini desteklemez.

#### <a name="windows-server"></a>Windows Server

- Windows Server 2019
- Windows Server 2016 1803
- Windows Server 2016
- Windows Server 2012 R2
- Windows Server 2012
- Windows Server 2008 R2 SP1

#### <a name="windows-desktop"></a>Windows masaüstü

- Windows 10 1803
- Windows 10
- Windows 8.1
- Windows 8
- Windows 7

#### <a name="supported-linux-operating-systems"></a>Desteklenen Linux işletim sistemleri
Aşağıdaki bölümlerde Linux'taki Bağımlılık aracısı için desteklenen işletim sistemleri listelenir.  

- Yalnızca varsayılan ve SMP Linux çekirdek sürümleri desteklenir.
- PAE ve Xen gibi standart dışı çekirdek sürümleri hiçbir Linux dağıtımında desteklenmez. Örneğin, "2.6.16.21-0.8-xen" sürüm dizeli bir sistem desteklenmez.
- Standart çekirdeklerin yeniden derlemeleri de dahil olmak üzere özel çekirdekler desteklenmez.

##### <a name="red-hat-linux-7"></a>Red Hat Linux 7

| İşletim sistemi sürümü | Çekirdek sürümü |
|:--|:--|
| 7.4 | 3.10.0-693 |
| 7,5 | 3.10.0-862 |
| 7.6 | 3.10.0-957 |

##### <a name="red-hat-linux-6"></a>Red Hat Linux 6

| İşletim sistemi sürümü | Çekirdek sürümü |
|:--|:--|
| 6.9 | 2.6.32-696 |
| 6.10 | 2.6.32-754 |

##### <a name="centosplus"></a>CentOSPlus
| İşletim sistemi sürümü | Çekirdek sürümü |
|:--|:--|
| 6.9 | 2.6.32-696.18.7<br>2.6.32-696.30.1 |
| 6.10 | 2.6.32-696.30.1<br>2.6.32-754.3.5 |

##### <a name="ubuntu-server"></a>Ubuntu Server

| İşletim sistemi sürümü | Çekirdek sürümü |
|:--|:--|
| Ubuntu 18.04 | çekirdek 4.15.\*<br>4.18* |
| Ubuntu 16.04.3 | çekirdek 4.15.* |
| 16.04 | 4.4.\*<br>4.8.\*<br>4.10.\*<br>4.11.\*<br>4.13.\* |
| 14.04 | 3.13.\*<br>4.4.\* |

##### <a name="suse-linux-11-enterprise-server"></a>SUSE Linux 11 Kurumsal Sunucu

| İşletim sistemi sürümü | Çekirdek sürümü
|:--|:--|
| 11 SP4 | 3.0.* |

##### <a name="suse-linux-12-enterprise-server"></a>SUSE Linux 12 Kurumsal Sunucu

| İşletim sistemi sürümü | Çekirdek sürümü
|:--|:--|
| 12 SP2 | 4.4.* |
| 12 SP3 | 4.4.* |

### <a name="dependency-agent-downloads"></a>Bağımlılık aracısı indirmeleri

| Dosya | İşletim Sistemi | Sürüm | SHA-256 |
|:--|:--|:--|:--|
| [InstallDependencyAgent-Windows.exe](https://aka.ms/dependencyagentwindows) | Windows | 9.7.4 | A111B92AB6CF28EB68B696C60FE51F980BFDFF78C36A900575E17083972989E0 |
| [InstallDependencyAgent-Linux64.bin](https://aka.ms/dependencyagentlinux) | Linux | 9.7.4 | AB58F3DB8B1C3DEE7512690E5A65F1DFC41B43831543B5C040FCCE8390F2282C |



## <a name="configuration"></a>Yapılandırma

Çalışma alanlarınızda Wire Data çözümünü yapılandırmak için aşağıdaki adımları uygulayın.

1. [Azure pazar](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.WireData2OMS?tab=Overview) alanından veya Çözüm Galerisi'nden İzleme Ekle çözümleri ekle'de açıklanan işlemi kullanarak Etkinlik Günlüğü Analizi [çözümünü etkinleştirin.](../../azure-monitor/insights/solutions.md)
2. Veri almak istediğiniz her bilgisayara Bağımlılık aracısını yükleyin. Bağımlılık aracısı yakın komşulara bağlantıları izleyebilir, bu nedenle her bilgisayarda bir aracıya ihtiyacınız olmayabilir.

> [!NOTE]
> Wire Data çözümünün önceki sürümünü yeni çalışma alanlarına ekleyemezsiniz. Özgün Wire Data çözümünü daha önce etkinleştirdiyseniz, bunu kullanmaya devam edebilirsiniz. Öte yandan, Wire Data 2.0'ı kullanmak için önce özgün sürümü kaldırmanız gerekir.
> 
 
### <a name="install-the-dependency-agent-on-windows"></a>Bağımlılık aracısını Windows'a yükleme

Aracıyı yüklemek veya kaldırmak için yönetici ayrıcalıkları gereklidir.

Bağımlılık aracısı InstallDependencyAgent-Windows.exe üzerinden Windows çalıştıran bilgisayarlarda yüklenir. Bu yürütülebilir dosyayı hiçbir seçenek olmadan çalıştırırsanız, etkileşimli yükleme yapmak için izleyebileceğiniz bir sihirbaz başlatır.

Windows çalıştıran her bilgisayara Bağımlılık aracısını yüklemek için aşağıdaki adımları kullanın:

1. [Ortamınızda barındırılan Windows bilgisayarlarından veri topla'daki](../../azure-monitor/platform/agent-windows.md)adımları izleyerek Günlük Analizi aracısını yükleyin.
2. Önceki bölümdeki bağlantıyı kullanarak Windows Bağımlılık aracısını indirin ve ardından aşağıdaki komutu kullanarak çalıştırın:`InstallDependencyAgent-Windows.exe`
3. Sihirbazı izleyerek aracıyı yükleyin.
4. Bağımlılık aracısı başlatılamazsa, ayrıntılı hata bilgileri için günlükleri denetleyin. Windows aracıları için günlük dizini: %Programfiles%\Microsoft Dependency Agent\logs.

#### <a name="windows-command-line"></a>Windows komut satırı

Komut satırından yüklemek için aşağıdaki tabloda yer alan seçenekleri kullanın. Yükleme bayraklarının listesini görmek için yükleyiciyi çalıştırırken aşağıda gösterildiği gibi /? bayrağını kullanın.

InstallDependencyAgent-Windows.exe /?

| **Bayrak** | **Açıklama** |
| --- | --- |
| <code>/?</code> | Komut satırı seçeneklerinin listesini alır. |
| <code>/S</code> | Kullanıcıdan bilgi istenmeden sessiz yükleme gerçekleştirir. |

Windows Bağımlılık aracısı için dosyalar varsayılan olarak C:\Program Files\Microsoft Dependency aracısı yerleştirilir.

### <a name="install-the-dependency-agent-on-linux"></a>Bağımlılık aracısını Linux'a yükleyin

Aracıyı yüklemek veya yapılandırmak için kök erişimi gerekir.

Bağımlılık aracısı Linux bilgisayarlara InstallDependencyAgent-Linux64.bin aracılığıyla yüklenir. Dosyayı çalıştırmak için _sh_ kullanın veya dosyanın kendisine yürütme izinleri ekleyin.

Bağımlılık aracısını her Linux bilgisayarına yüklemek için aşağıdaki adımları kullanın:

1. [Ortamınızda barındırılan Linux bilgisayarlarından veri topla'daki](../../azure-monitor/learn/quick-collect-linux-computer.md#obtain-workspace-id-and-key)adımları izleyerek Log Analytics aracısını yükleyin.
2. Önceki bölümdeki bağlantıyı kullanarak Linux Bağımlılık aracısını indirin ve ardından aşağıdaki komutu kullanarak kök olarak yükleyin: sh InstallDependencyAgent-Linux64.bin
3. Bağımlılık aracısı başlatılamazsa, ayrıntılı hata bilgileri için günlükleri denetleyin. Linux aracıları için günlük dizini: /var/opt/microsoft/dependency-agent/log.

Yükleme bayraklarının listesini görmek için, yükleme programını aşağıda gösterildiği gibi `-help` bayrağıyla çalıştırın.

```
InstallDependencyAgent-Linux64.bin -help
```

| **Bayrak** | **Açıklama** |
| --- | --- |
| <code>-help</code> | Komut satırı seçeneklerinin listesini alır. |
| <code>-s</code> | Kullanıcıdan bilgi istenmeden sessiz yükleme gerçekleştirir. |
| <code>--check</code> | İzinleri ve işletim sistemini denetleyin ama aracıyı yüklemeyin. |

Bağımlılık aracısı için dosyalar aşağıdaki dizinlere yerleştirilir:

| **Dosyaları** | **Konum** |
| --- | --- |
| Çekirdek dosyaları | /opt/microsoft/dependency-agent |
| Günlük dosyaları | /var/opt/microsoft/dependency-agent/log |
| Yapılandırma dosyaları | /etc/opt/microsoft/dependency-agent/config |
| Hizmet yürütülebilir dosyaları | /opt/microsoft/dependency-agent/bin/microsoft-dependency-agent<br><br>/opt/microsoft/dependency-agent/bin/microsoft-dependency-agent-manager |
| İkili depolama dosyaları | /var/opt/microsoft/dependency-agent/storage |

### <a name="installation-script-examples"></a>Yükleme betiği örnekleri

Bağımlılık aracısını aynı anda birçok sunucuda kolayca dağıtmak için komut dosyası nın kullanılmasına yardımcı olur. Bağımlılık aracısını Windows veya Linux'a indirmek ve yüklemek için aşağıdaki komut dosyası örneklerini kullanabilirsiniz.

#### <a name="powershell-script-for-windows"></a>Windows için PowerShell betiği

```powershell

Invoke-WebRequest "https://aka.ms/dependencyagentwindows" -OutFile InstallDependencyAgent-Windows.exe

.\InstallDependencyAgent-Windows.exe /S

```

#### <a name="shell-script-for-linux"></a>Linux için kabuk betiği

```
wget --content-disposition https://aka.ms/dependencyagentlinux -O InstallDependencyAgent-Linux64.bin
```

```
sh InstallDependencyAgent-Linux64.bin -s
```

### <a name="desired-state-configuration"></a>İstenen Durum Yapılandırması

Bağımlılık aracısını İstenilen Durum Yapılandırması ile dağıtmak için xPSDesiredStateConfiguration modüllerini ve aşağıdaki gibi bir kod parçasını kullanabilirsiniz:

```powershell
Import-DscResource -ModuleName xPSDesiredStateConfiguration

$DAPackageLocalPath = "C:\InstallDependencyAgent-Windows.exe"



Node $NodeName

{

    # Download and install the Dependency agent

    xRemoteFile DAPackage

    {

        Uri = "https://aka.ms/dependencyagentwindows"

        DestinationPath = $DAPackageLocalPath

        DependsOn = "[Package]OI"

    }

    xPackage DA

    {

        Ensure = "Present"

        Name = "Dependency Agent"

        Path = $DAPackageLocalPath

        Arguments = '/S'

        ProductId = ""

        InstalledCheckRegKey = "HKEY\_LOCAL\_MACHINE\SOFTWARE\Wow6432Node\Microsoft\Windows\CurrentVersion\Uninstall\DependencyAgent"

        InstalledCheckRegValueName = "DisplayName"

        InstalledCheckRegValueData = "Dependency Agent"

    }

}

```

### <a name="uninstall-the-dependency-agent"></a>Bağımlılık aracısını kaldırma

Bağımlılık aracısını kaldırmanıza yardımcı olmak için aşağıdaki bölümleri kullanın.

#### <a name="uninstall-the-dependency-agent-on-windows"></a>Windows'daki Bağımlılık aracısını kaldırma

Yönetici, Denetim Masası aracılığıyla Windows için Bağımlılık aracısını kaldırabilir.

Bir yönetici, Bağımlılık aracısını kaldırmak için %Programdosyaları%\Microsoft Bağımlılık Aracısı\Kaldır.exe'yi de çalıştırabilir.

#### <a name="uninstall-the-dependency-agent-on-linux"></a>Linux'taki Bağımlılık aracısını kaldırın

Bağımlılık aracısını Linux'tan tamamen kaldırmak için, aracının kendisini ve aracıyla otomatik olarak yüklenen konektörü kaldırmanız gerekir. Aşağıdaki tek komutu kullanarak ikisini de kaldırabilirsiniz:

```
rpm -e dependency-agent dependency-agent-connector
```

## <a name="management-packs"></a>Yönetim paketleri

Log Analytics çalışma alanında Wire Data etkinleştirildiğinde, söz konusu çalışma alanındaki tüm Windows sunucularına 300 KB'lık bir yönetim paketi gönderilir. System Center Operations Manager aracılarını bir [bağlı yönetim grubunda](../platform/om-agents.md) kullanıyorsanız, System Center Operations Manager'dan Bağımlılık İzleyicisi yönetim paketi dağıtılır. Aracılar doğrudan bağlıysa, Azure Monitor yönetim paketini sunar.

Yönetim paketinin adı Microsoft.IntelligencePacks.ApplicationDependencyMonitor'dır. Şu konuma yazılır: %Programfiles%\Microsoft Monitoring Agent\Agent\Health Service State\Management Packs. Yönetim paketi şu veri kaynağını kullanır: %Program files%\Microsoft Monitoring Agent\Agent\Health Service State\Resources&lt;AutoGeneratedID&gt;\Microsoft.EnterpriseManagement.Advisor.ApplicationDependencyMonitorDataSource.dll.

## <a name="using-the-solution"></a>Çözümü kullanma

Çözümü yüklemek ve yapılandırmak için aşağıdaki bilgileri kullanın.

- Wire Data çözümü Windows Server 2012 R2, Windows 8.1 ve daha sonraki işletim sistemlerini çalıştıran bilgisayarlardan veri alır.
- Sinyal verilerini almak istediğiniz bilgisayarlarda Microsoft .NET Framework 4.0 veya üstü bulunmalıdır.
- [Çözüm Galerisi'nden İzleme Çözümleri Ekle'de](solutions.md)açıklanan işlemi kullanarak Günlük Analizi çalışma alanınıza Tel Veri çözümünü ekleyin. Başka bir yapılandırma işlemi gerekmez.
- Belirli bir çözümle ilişkili sinyal verilerini görüntülemek istiyorsanız, çözümün çalışma alanınıza önceden eklenmiş olması gerekir.

Aracılarınız yüklendikten ve siz çözümü yükledikten sonra, çalışma alanınızda Wire Data 2.0 kutucuğu gösterilir.

![Wire Data kutucuğu](./media/wire-data/wire-data-tile.png)

## <a name="using-the-wire-data-20-solution"></a>Wire Data 2.0 çözümünü kullanma

Azure portalında Log Analytics çalışma alanınızın **Genel bakış** sayfasında **Wire Data 2.0** kutucuğuna tıklayarak Wire Data panosunu açın. Pano aşağıdaki tabloda gösterilen dikey pencereleri içerir. Her dikey pencerede, dikey pencerenin belirtilen kapsam ve zaman aralığına yönelik ölçütleriyle eşleşen en fazla 10 öğe listelenir. Dikey pencerenin altındaki **Tümünü göster**’e tıklayarak veya dikey pencere başlığına tıklayarak tüm kayıtları döndüren bir günlük araması yapabilirsiniz.

| **Dikey pencere** | **Açıklama** |
| --- | --- |
| Ağ trafiğini yakalayan aracılar | Ağ trafiğini yakalayan aracıların sayısını gösterir ve trafiği yakalayan ilk 10 bilgisayarı listeler. <code>WireData \| summarize sum(TotalBytes) by Computer \| take 500000</code> günlük araması çalıştırmak için sayıya tıklayın. Yakalanan toplam bayt sayısını döndüren bir günlük araması çalıştırmak için listedeki bir bilgisayara tıklayın. |
| Yerel Alt Ağlar | Aracıların keşfettiği yerel alt ağların sayısını gösterir.  Tüm alt ağları ve her birinden gönderilen bayt sayısını listeleyen bir <code>WireData \| summarize sum(TotalBytes) by LocalSubnet</code> günlük araması çalıştırmak için sayıya tıklayın. Alt ağ üzerinden gönderilen toplam bayt sayısını döndüren bir günlük araması çalıştırmak için listedeki bir alt ağa tıklayın. |
| Uygulama Düzeyi Protokolleri | Aracılar tarafından keşfedilen, kullanımdaki uygulama düzeyi protokollerinin sayısını gösterir. <code>WireData \| summarize sum(TotalBytes) by ApplicationProtocol</code> günlük araması çalıştırmak için sayıya tıklayın. Protokol kullanılarak gönderilen toplam bayt sayısını döndüren bir günlük araması çalıştırmak için listedeki bir protokole tıklayın. |

![Wire Data panosu](./media/wire-data/wire-data-dash.png)

**Ağ trafiğini yakalayan aracılar** dikey penceresini kullanarak bilgisayarlar tarafından kullanılmakta olan ağ genişliğini miktarını belirleyebilirsiniz. Bu dikey pencere ortamınızdaki en _geveze_ bilgisayarı kolayca bulmanıza yardımcı olabilir. Bu tür bilgisayarlar aşırı yüklenmiş, anormal çalışıyor veya normalin üzerinde ağ kaynağı kullanıyor olabilir.

![günlük araması örneği](./media/wire-data/log-search-example01.png)

Benzer biçimde, **Yerel Alt Ağlar** dikey penceresini kullanarak alt ağlarınız üzerinden ne kadar ağ trafiği taşındığını belirleyebilirsiniz. Kullanıcılar alt ağları çoğunlukla uygulamalarının kritik alanları çevresinde tanımlar. Bu dikey pencere söz konusu alanların görülmesini sağlar.

![günlük araması örneği](./media/wire-data/log-search-example02.png)

**Uygulama Düzeyi Protokolleri** hangi protokollerin kullanımda olduğunu öğrenmenize yardımcı olduğundan, yararlı bir dikey penceredir. Örneğin, ağ ortamınızda SSH'nin kullanımda olmamasını bekliyor olabilirsiniz. Dikey pencerede sağlanan bilgileri görüntüleyerek bu beklentinizin doğru olup olmadığını hızla anlayabilirsiniz.

![günlük araması örneği](./media/wire-data/log-search-example03.png)

Ayrıca protokol trafiğinin zaman içinde arttığını mı yoksa azaldığını mı bilmek de yararlı olur. Örneğin, bir uygulama tarafından iletilen verilerin miktarı artıyorsa, bu farkında olmanız gereken bir durum veya dikkate değer bulduğunuz bir bilgi olabilir.

## <a name="input-data"></a>Giriş verileri

Sinyal verileri, etkinleştirilmiş olan aracıları kullanarak ağ trafiği hakkındaki meta verileri toplar. Her aracı yaklaşık her 15 saniyede bir veri gönderir.

## <a name="output-data"></a>Çıktı verileri

Her giriş verileri türü için _WireData_ türünde bir kayıt oluşturulur. Aşağıdaki tabloda WireData kayıtlarının özellikleri gösterilmiştir:

| Özellik | Açıklama |
|---|---|
| Bilgisayar | Verilerin toplandığı bilgisayarın adı |
| TimeGenerated | Kaydın zamanı |
| LocalIP | Yerel bilgisayarın IP adresi |
| SessionState | Bağlantılı veya bağlantısı kesilmiş |
| ReceivedBytes | Alınan bayt miktarı |
| ProtocolName | Kullanılan ağ protokolünün adı |
| IPVersion | IP sürümü |
| Yön | Gelen veya giden |
| MaliciousIP | Bilinen kötü amaçlı kaynağın IP adresi |
| Severity | Kötü amaçlı olduğundan şüphe edilen yazılımın önem derecesi |
| RemoteIPCountry | Uzak IP adresinin ülke/bölgesi |
| ManagementGroupName | Operations Manager yönetim grubunun adı |
| SourceSystem | Verilerin toplandığı kaynak |
| SessionStartTime | Oturumun başlangıç saati |
| SessionEndTime | Oturumun bitiş saati |
| LocalSubnet | Verilerin toplandığı alt ağ |
| LocalPortNumber | Yerel bağlantı noktası numarası |
| RemoteIP | Uzak bilgisayar tarafından kullanılan uzak IP adresi |
| RemotePortNumber | Uzak IP adresi tarafından kullanılan bağlantı noktası numarası |
| SessionID | İki IP adresi arasındaki iletişim oturumunu tanımlayan benzersiz bir değer |
| SentBytes | Gönderilen bayt sayısı |
| TotalBytes | Oturum sırasında gönderilen toplam bayt sayısı |
| ApplicationProtocol | Kullanılan ağ protokolünün türü   |
| ProcessID | Windows işlem kimliği |
| ProcessName | İşlemin yıl ve dosya adı |
| RemoteIPLongitude | IP boylam değeri |
| RemoteIPLatitude | IP enlem değeri |

## <a name="next-steps"></a>Sonraki adımlar

- Ayrıntılı sinyal verileri arama kayıtlarını görüntülemek için [günlüklerde arama yapın](../../azure-monitor/log-query/log-query-overview.md).
