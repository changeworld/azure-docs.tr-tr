---
title: Azure Windows Sanal Makinelerinde SQL Server'a Genel Bakış | Microsoft Docs
description: Azure Sanal Makineler’de tam SQL Server sürümlerini çalıştırma hakkında bilgi edinin.
services: virtual-machines-windows
documentationcenter: ''
author: MashaMSFT
manager: craigg
tags: azure-service-management
ms.assetid: c505089e-6bbf-4d14-af0e-dd39a1872767
ms.service: virtual-machines-sql
ms.topic: conceptual
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 11/27/2019
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 7d8d1505a268976161636abd0ed2d24398978284
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75374327"
---
# <a name="what-is-sql-server-on-azure-virtual-machines-windows"></a>Azure Sanal Makinelerinde SQL Server nedir? (Windows)

> [!div class="op_single_selector"]
> * [Windows](virtual-machines-windows-sql-server-iaas-overview.md)
> * [Linux](../../linux/sql/sql-server-linux-virtual-machines-overview.md)

[Azure sanal makinelerdeki SQL Server,](https://azure.microsoft.com/services/virtual-machines/sql-server/) şirket içi donanımı yönetmek zorunda kalmadan Bulut'ta SQL Server'ın tam sürümlerini kullanmanıza olanak tanır. SQL Server VM’leri, kullandıkça ödeme yaptığınızda lisanslama maliyetlerini de basitleştirir.

Azure sanal makineleri dünyanın birçok farklı [coğrafi bölgesinde](https://azure.microsoft.com/regions/) çalışır. Bu makineler, ayrıca çeşitli [makine boyutları](../sizes.md) sunar. Sanal makine resim galerisi, doğru sürüm ve işletim sistemiyle bir SQL Server VM’i oluşturmanızı sağlar. Bu, sanal makineleri birçok farklı SQL Server iş yükleri için iyi bir seçenek yapar.

## <a name="automated-updates"></a>Otomatik güncelleştirmeler

SQL Server Azure sanal makineleri, önemli Windows ve SQL Server güncelleştirmelerini otomatik olarak yüklemek için bir bakım penceresi zamanlamak üzere [Otomatik Düzeltme Eki Uygulama](virtual-machines-windows-sql-automated-patching.md) özelliğini kullanabilir.

## <a name="automated-backups"></a>Otomatik yedeklemeler

SQL Server Azure sanal makineleri, blob depolama alanında düzenli olarak veritabanınızın yedeklerini oluşturan [Otomatik Yedekleme](virtual-machines-windows-sql-automated-backup-v2.md) özelliğinden yararlanabilir. Bu tekniği el ile de kullanabilirsiniz. Daha fazla bilgi için. bkz. [SQL Server Yedekleme ve Geri Yükleme için Azure Storage’ı Kullanma](virtual-machines-windows-use-storage-sql-server-backup-restore.md).

## <a name="high-availability"></a>Yüksek kullanılabilirlik

Size yüksek kullanılabilirlik gerekiyorsa, SQL Server Kullanılabilirlik gruplarını yapılandırmayı dikkate alın. Bu, bir sanal ağda birden fazla SQL Server Azure VM’leri içerir. Yüksek kullanılabilirlik çözümünüzü kendiniz yapılandırabilir veya otomatik yapılandırma için Azure portalındaki şablonları kullanabilirsiniz. Tüm yüksek kullanılabilirlik seçeneklerine genel bir bakış için bkz. [Azure Sanal Makineler’de SQL Server için Yüksek Kullanılabilirlik ve Olağanüstü Durum Kurtarma](virtual-machines-windows-sql-high-availability-dr.md).

## <a name="performance"></a>Performans

Azure sanal makineleri çeşitli iş yükü taleplerini karşılamak üzere farklı makine boyutları sunar. SQL VM'leri ayrıca performans gereksinimleriniz için en iyi duruma getirilmiş otomatik depolama yapılandırması sağlar. SQL VM'lerine ilişkin depolama yapılandırması hakkında daha fazla bilgi için bkz. [SQL Server VM'leri için depolama yapılandırması](virtual-machines-windows-sql-server-storage-configuration.md). Performansı ayarlamak için bkz. [Azure Sanal Makineler’de SQL Server için performansa yönelik en iyi uygulamalar](virtual-machines-windows-sql-performance.md).

## <a name="get-started-with-sql-vms"></a>SQL VM’lerini kullanmaya başlayın

Kullanmaya başlamak için, gerekli olan sürüm ve işletim sisteminizle birlikte bir SQL Server sanal makine resmi seçin. Aşağıdaki bölümlerde SQL Server sanal makine galeri görüntüleri için Azure portalının doğrudan bağlantıları verilmektedir.

> [!TIP]
> SQL görüntülerinin fiyatlandırmasını anlamak için bkz. [SQL Server Azure VM’leri için fiyatlandırma kılavuzu](virtual-machines-windows-sql-server-pricing-guidance.md). 

### <a name="pay-as-you-go"></a><a id="payasyougo"></a>Gittiğiniz gibi ödeyin
Şu tabloda, kullandıkça öde SQL Server resimlerinin bir matrisi verilmektedir.

| Sürüm | İşletim Sistemi | Sürüm |
| --- | --- | --- |
| **SQL Server 2019** | Windows Server 2019 | [Kurumsal](https://ms.portal.azure.com/#create/microsoftsqlserver.sql2019-ws2019enterprise), [Standart](https://ms.portal.azure.com/#create/microsoftsqlserver.sql2019-ws2019standard), [Web](https://ms.portal.azure.com/#create/microsoftsqlserver.sql2019-ws2019web), [Geliştirici](https://ms.portal.azure.com/#create/microsoftsqlserver.sql2019-ws2019sqldev) | 
| **SQL Server 2017** |Windows Server 2016 |[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2017EnterpriseWindowsServer2016), [Standard](https://portal.azure.com/#create/Microsoft.SQLServer2017StandardonWindowsServer2016), [Web](https://portal.azure.com/#create/Microsoft.SQLServer2017WebonWindowsServer2016), [Express](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017ExpressonWindowsServer2016), [Developer](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017DeveloperonWindowsServer2016) |
| **SQL Server 2016 SP2** |Windows Server 2016 |[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2016SP2EnterpriseWindowsServer2016), [Standard](https://portal.azure.com/#create/Microsoft.SQLServer2016SP2StandardWindowsServer2016), [Web](https://portal.azure.com/#create/Microsoft.SQLServer2016SP2WebWindowsServer2016), [Express](https://portal.azure.com/#create/Microsoft.FreeLicenseSQLServer2016SP2ExpressWindowsServer2016), [Developer](https://portal.azure.com/#create/Microsoft.FreeLicenseSQLServer2016SP2DeveloperWindowsServer2016) |
| **SQL Server 2014 SP2** |Windows Server 2012 R2 |[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2014SP2EnterpriseWindowsServer2012R2), [Standard](https://portal.azure.com/#create/Microsoft.SQLServer2014SP2StandardWindowsServer2012R2), [Web](https://portal.azure.com/#create/Microsoft.SQLServer2014SP2WebWindowsServer2012R2), [Express](https://portal.azure.com/#create/Microsoft.SQLServer2014SP2ExpressWindowsServer2012R2) |
| **SQL Server 2012 SP4** |Windows Server 2012 R2 |[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2012SP4EnterpriseWindowsServer2012R2), [Standard](https://portal.azure.com/#create/Microsoft.SQLServer2012SP4StandardWindowsServer2012R2), [Web](https://portal.azure.com/#create/Microsoft.SQLServer2012SP4WebWindowsServer2012R2), [Express](https://portal.azure.com/#create/Microsoft.SQLServer2012SP4ExpressWindowsServer2012R2) |
| **SQL Server 2008 R2 SP3** |Windows Server 2008 R2|[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2008R2SP3EnterpriseWindowsServer2008R2), [Standard](https://portal.azure.com/#create/Microsoft.SQLServer2008R2SP3StandardWindowsServer2008R2), [Web](https://portal.azure.com/#create/Microsoft.SQLServer2008R2SP3WebWindowsServer2008R2), [Express](https://portal.azure.com/#create/Microsoft.SQLServer2008R2SP3ExpressWindowsServer2008R2) |

Kullanabileceğiniz Linux SQL Server sanal makine görüntülerini görmek için bkz. [Azure Sanal Makinelerinde SQL Server'a Genel Bakış (Linux)](../../linux/sql/sql-server-linux-virtual-machines-overview.md).

> [!NOTE]
> Artık kendi lisansınızı kullanmak için kullanım başına ödemeli SQL Server VM'nin lisans modelini değiştirmek mümkündür. Daha fazla bilgi için, [SQL VM'nin lisans modelini nasıl değiştireceğiniz](virtual-machines-windows-sql-ahb.md)e bakın. 

### <a name="bring-your-own-license"></a><a id="BYOL"></a> Kendi lisansını getir
Ayrıca kendi lisansınızı getirebilirsiniz (KLG). Bu senaryoda, SQL Server Lisans için hiçbir ek bir ücret olmadan yalnızca VM için ödeme yaparsınız.  Kendi lisansınızı getirmek, sürekli üretim iş yüklerinde zaman içinde paradan tasarruf etmenizi sağlayabilir. Bu seçeneği kullanmaya yönelik gereksinimler için bkz. [SQL Server Azure VM’leri için fiyatlandırma kılavuzu](virtual-machines-windows-sql-server-pricing-guidance.md#byol).

Kendi lisansınızı getirmek için, kullanılabilir kullanım başına ödemeLI SQL VM'yi dönüştürebilir veya önceden belirlenmiş **{BYOL}** ile bir görüntü dağıtabilirsiniz. Lisans modelinizi kullanım başına ödeme ve BYOL arasında değiştirme hakkında daha fazla bilgi için, [sql VM'nin lisans modelini nasıl değiştireceğinize](virtual-machines-windows-sql-ahb.md)bakın. 

| Sürüm | İşletim sistemi | Sürüm |
| --- | --- | --- |
| **SQL Server 2019** | Windows Server 2019 | [Kurumsal BYOL](https://ms.portal.azure.com/#create/microsoftsqlserver.sql2019-ws2019-byolenterprise), [Standart BYOL](https://ms.portal.azure.com/#create/microsoftsqlserver.sql2019-ws2019-byolstandard)| 
| **SQL Server 2017** |Windows Server 2016 |[Enterprise KLG](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2017EnterpriseWindowsServer2016), [Standard KLG](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2017StandardonWindowsServer2016) |
| **SQL Server 2016 SP2** |Windows Server 2016 |[Enterprise KLG](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2016SP2EnterpriseWindowsServer2016), [Standard KLG](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2016SP2StandardWindowsServer2016) |
| **SQL Server 2014 SP2** |Windows Server 2012 R2 |[Enterprise KLG](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2014SP2EnterpriseWindowsServer2012R2), [Standard KLG](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2014SP2StandardWindowsServer2012R2) |
| **SQL Server 2012 SP4** |Windows Server 2012 R2 |[Kurumsal BYOL](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2012SP4EnterpriseWindowsServer2012R2), [Standart BYOL](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2012SP4StandardWindowsServer2012R2) |

PowerShell kullanarak Azure portalında bulunmayan eski bir SQL Server görüntüsünü dağıtmak mümkündür. Powershell kullanarak kullanılabilir tüm görüntüleri görüntülemek için aşağıdaki komutu kullanın:

  ```powershell
  Get-AzVMImageOffer -Location $Location -Publisher 'MicrosoftSQLServer'
  ```

PowerShell kullanarak SQL Server VM'leri dağıtma hakkında daha fazla bilgi için [SQL Server sanal makinelerini Azure PowerShell ile nasıl sağlarsanız](virtual-machines-windows-ps-sql-create.md)görüntüleyin.


### <a name="connect-to-the-vm"></a>VM’ye bağlanma
SQL Server sanal makinenizi oluşturduktan sonra, SQL Server Management Studio (SSMS) gibi uygulamalar veya araçlardan bu makineye bağlanın. Yönergeler için bkz. [Azure’da SQL Server Sanal Makinesine Bağlanma](virtual-machines-windows-sql-connect.md).

### <a name="migrate-your-data"></a>Verilerinizi geçirme
Varolan bir veritabanınız varsa, bunu yeni sağlanan SQL VM'ye taşımak istersiniz. Geçiş seçenekleri ve kılavuzların listesi için bkz. [Azure VM’de bir Veritabanını SQL Server’a Geçirme](virtual-machines-windows-migrate-sql.md).

## <a name="create-and-manage-azure-sql-resources-with-the-azure-portal"></a>Azure portalı ile Azure SQL kaynakları oluşturma ve yönetme

Azure portalı, SQL sanal makineleriniz de dahil olmak üzere [tüm Azure SQL kaynaklarınızı](https://portal.azure.com/#blade/HubsExtension/BrowseResource/resourceType/Microsoft.Sql%2Fazuresql) yönetebileceğiniz tek bir sayfa sağlar.

**Azure SQL kaynakları** sayfasına erişmek için Azure portal menüsünde Azure **SQL'i** seçin veya herhangi bir sayfadan **Azure SQL'i** arayın ve seçin.

![Azure SQL ara](./media/quickstart-sql-vm-create-portal/search-for-azure-sql.png)

> [!NOTE]
> **Azure SQL,** tüm SQL veritabanlarınıza, esnek havuzlarınıza, veritabanı sunucularınıza, SQL yönetilen örneklerinize ve SQL sanal makinelerinize erişmek için hızlı ve kolay bir yol sağlar. Azure SQL bir hizmet veya kaynak değildir. 

Varolan kaynakları yönetmek için listede istenen öğeyi seçin. Yeni Azure SQL kaynakları oluşturmak için **+ Ekle'yi**seçin. 

![Azure SQL kaynağı oluşturma](./media/quickstart-sql-vm-create-portal/create-azure-sql-resource.png)

**+ Ekle'** yi seçtikten sonra, herhangi bir döşemedeki **ayrıntıları göster'i** seçerek farklı seçenekler hakkında ek bilgileri görüntüleyin.

![veritabanları döşeme ayrıntıları](./media/quickstart-sql-vm-create-portal/sql-vm-details.png)

Ayrıntılar için bkz.

- [Tek bir veritabanı oluşturma](../../../sql-database/sql-database-single-database-get-started.md)
- [Elastik havuz oluşturma](../../../sql-database/sql-database-elastic-pool.md#creating-a-new-sql-database-elastic-pool-using-the-azure-portal)
- [Yönetilen bir örnek oluşturma](../../../sql-database/sql-database-managed-instance-get-started.md)
- [SQL sanal makine oluşturma](quickstart-sql-vm-create-portal.md)

## <a name="sql-vm-image-refresh-policy"></a><a id="lifecycle"></a> SQL VM görüntü yenileme ilkesi
Azure desteklenen her işletim sistemi, sürüm ve sürüm bileşimi için yalnızca tek bir sanal makine görüntüsü tutar. Diğer bir deyişle, zaman içinde görüntüler yenilenir ve eski görüntüler kaldırılır. Daha fazla bilgi için [SQL Server VM'leri SSS](virtual-machines-windows-sql-server-iaas-faq.md#images) makalesindeki **Görüntüler** bölümüne bakın.

## <a name="customer-experience-improvement-program-ceip"></a>Müşteri deneyimini geliştirme programı (CEIP)
Müşteri Deneyimini Geliştirme Programı (CEIP) varsayılan olarak etkindir. Bu, SQL Server’ın geliştirilmesine yardımcı olmak için Microsoft’a düzenli olarak raporlar gönderir. CEIP’i hazırladıktan sonra devre dışı bırakmak istemiyorsanız CEIP için herhangi bir yönetim görevi gerekmez. VM’ye uzak masaüstüyle bağlanarak CEIP özelleştirebilir ya da devre dışı bırakabilirsiniz. Ardından **SQL Server Hata ve Kullanım Raporlama** yardımcı programını çalıştırın. Raporlamayı devre dışı bırakmak için yönergeleri izleyin. Veri toplama hakkında daha fazla bilgi için bkz. [SQL Server Gizlilik Bildirimi](https://docs.microsoft.com/sql/getting-started/microsoft-sql-server-privacy-statement).

## <a name="related-products-and-services"></a>İlgili ürün ve hizmetler
### <a name="windows-virtual-machines"></a>Windows Sanal Makineleri
* [Sanal Makinelere genel bakış](../overview.md)

### <a name="storage"></a>Depolama
* [Microsoft Azure Depolama'ya Giriş](../../../storage/common/storage-introduction.md)

### <a name="networking"></a>Ağ Oluşturma
* [Sanal Ağa genel bakış](../../../virtual-network/virtual-networks-overview.md)
* [Azure'da IP adresleri](../../../virtual-network/virtual-network-ip-addresses-overview-arm.md)
* [Azure portalda Tam Etki Alanı Adı oluşturma](../portal-create-fqdn.md)

### <a name="sql"></a>SQL
* [SQL Server belgeleri](https://docs.microsoft.com/sql/index)
* [Azure SQL Veritabanı karşılaştırması](../../../sql-database/sql-database-paas-vs-sql-server-iaas.md)

## <a name="next-steps"></a>Sonraki adımlar

Azure sanal makinelerinde SQL Server kullanmaya başlayın:

* [Azure portalında SQL Server VM’i oluşturma](quickstart-sql-vm-create-portal.md)

SQL VM'ler hakkında sık sorulan soruların yanıtlarını alın:

* [Azure Sanal Makinelerde SQL Server SSS](virtual-machines-windows-sql-server-iaas-faq.md)

IaaS'da SQL Server'da N katmanlı uygulamaları çalıştırmak için Başvuru Mimarilerini görüntüleyin

* [SQL Server ile Azure'da Windows N katmanlı uygulama](https://docs.microsoft.com/azure/architecture/reference-architectures/n-tier/n-tier-sql-server)
* [Yüksek kullanılabilirlik için birden çok Azure bölgesinde N katmanlı bir uygulama çalıştırma](https://docs.microsoft.com/azure/architecture/reference-architectures/n-tier/multi-region-sql-server)
