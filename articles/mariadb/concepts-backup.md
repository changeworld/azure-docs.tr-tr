---
title: Yedekleme ve geri yükleme - MariaDB için Azure Veritabanı
description: Otomatik yedeklemeler ve MariaDB sunucusu için Azure Veritabanınızı geri alma hakkında bilgi edinin.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 3/27/2020
ms.openlocfilehash: c4d5a9ca85237bde1277904a478a0b8828fc2b08
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/27/2020
ms.locfileid: "80369232"
---
# <a name="backup-and-restore-in-azure-database-for-mariadb"></a>MariaDB için Azure Veritabanı'nda yedekleme ve geri yükleme

MariaDB için Azure Veritabanı otomatik olarak sunucu yedeklemeleri oluşturur ve bunları kullanıcı tarafından yapılandırılan yerel olarak yapılandırılan yedekli veya coğrafi yedekli depolama alanında saklar. Sunucunuzu belirli bir noktaya geri yüklemek için yedeklemeler kullanılabilir. Yedekleme ve geri yükleme, verilerinizi yanlışlıkla oluşan bozulmaveya silmeye karşı koruduklarından, herhangi bir iş sürekliliği stratejisinin önemli bir parçasıdır.

## <a name="backups"></a>Yedeklemeler

MariaDB için Azure Veritabanı tam, diferansiyel ve işlem günlüğü yedeklemeleri alır. Bu yedeklemeler, yapılandırılmış yedekleme bekletme süreniz içinde sunucunuzu zamanında geri yüklemenize olanak sağlar. Varsayılan yedekleme bekletme süresi yedi gündür. İsteğe bağlı olarak 35 güne kadar yapılandırabilirsiniz. Tüm yedeklemeler AES 256 bit şifreleme kullanılarak şifrelenir.

Bu yedekleme dosyaları kullanıcıtarafından açıkta değildir ve dışa aktarılamaz. Bu yedeklemeler yalnızca MariaDB için Azure Veritabanı'ndaki işlemleri geri yüklemek için kullanılabilir. Bir veritabanıkopyalamak için [mysqldump](howto-migrate-dump-restore.md) kullanabilirsiniz.

### <a name="backup-frequency"></a>Yedekleme sıklığı

Genellikle tam yedeklemeler haftada bir, değişiklik yedekleri günde iki kez, işlem günlüğü yedekleri ise beş dakikada bir alınır. İlk tam yedekleme, bir sunucu oluşturulduktan hemen sonra zamanlanır. İlk yedekleme, büyük bir geri yüklenen sunucuda daha uzun sürebilir. Yeni bir sunucunun geri yüklenebileceği en erken nokta, ilk tam yedeklemenin tamamlandığı zamandır.

### <a name="backup-redundancy-options"></a>Yedekleme artıklığı seçenekleri

MariaDB için Azure Veritabanı, Genel Amaç ve Bellek Optimize Edilmiş katmanlarda yerel olarak gereksiz veya coğrafi yedekli yedekleme depolama arasında seçim yapma esnekliği sağlar. Yedeklemeler coğrafi yedekli yedekleme depolama alanında depolandığında, yalnızca sunucunuzun barındırıldığı bölgede depolanmaz, aynı zamanda eşleştirilmiş bir [veri merkezine](https://docs.microsoft.com/azure/best-practices-availability-paired-regions)çoğaltılır. Bu, bir felaket durumunda sunucunuzu farklı bir bölgede geri yüklemeyi ve daha iyi koruma sağlar. Temel katman yalnızca yerel olarak yedekli yedekleme depolama alanı sunar.

> [!IMPORTANT]
> Yedekleme için yerel olarak yedekli veya coğrafi yedekli depolama nın yapılandırılmasına yalnızca sunucu oluşturma sırasında izin verilir. Sunucu sağlandıktan sonra, yedekleme depolama artıklığı seçeneğini değiştiremezsiniz.

### <a name="backup-storage-cost"></a>Yedekleme depolama maliyeti

MariaDB için Azure Veritabanı, sağlanan sunucu depolamanızın %100'e kadarını ek ücret ödemeden yedek depolama alanı olarak sağlar. Genellikle, bu yedi günlük bir yedekleme bekletme için uygundur. Kullanılan ek yedekleme depolama alanı GB-ay olarak ücretlendirilir.

Örneğin, 250 GB'lık bir sunucu verdiyseniz, ek ücret ödemeden 250 GB yedekleme depolama alanınız vardır. 250 GB'ı aşan depolama ücreti alınır.

Yedekleme depolama maliyeti hakkında daha fazla bilgi için [MariaDB fiyatlandırma sayfasını](https://azure.microsoft.com/pricing/details/mariadb/)ziyaret edin.

## <a name="restore"></a>Geri Yükleme

MariaDB için Azure Veritabanı'nda, geri yükleme yapmak özgün sunucunun yedeklerinden yeni bir sunucu oluşturur ve sunucuda bulunan tüm veritabanlarını geri yükler.

Kullanılabilir geri yükleme nin iki türü vardır:

- **Tam** ve işlem günlüğü yedeklemelerinin birleşimini kullanan özgün sunucunuzla aynı bölgede yeni bir sunucu oluşturur.
- **Coğrafi geri yükleme,** yalnızca sunucunuzu coğrafi yedekli depolama için yapılandırdığınızda ve sunucunuzu en son alınan yedeklemeyi kullanarak farklı bir bölgeye geri yüklemenize olanak tanırsa kullanılabilir.

Tahmini kurtarma süresi veritabanı boyutları, işlem günlüğü boyutu, ağ bant genişliği ve aynı anda aynı bölgede kurtarılan toplam veritabanı sayısı dahil olmak üzere çeşitli etkenlere bağlıdır. Kurtarma süresi genellikle 12 saatten azdır.

> [!IMPORTANT]
> Silinen sunucular geri **yüklenemez.** Sunucuyu silerseniz, sunucuya ait tüm veritabanları da silinir ve kurtarılamaz. Sunucu kaynaklarını, dağıtım sonrası, yanlışlıkla silinme veya beklenmeyen değişikliklere karşı korumak için yöneticiler [yönetim kilitlerinden](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-lock-resources)yararlanabilir.

### <a name="point-in-time-restore"></a>Belirli bir noktaya geri yükleme

Yedek artıklık seçeneğinizden bağımsız olarak, yedekleme bekletme süreniz içinde herhangi bir noktaya geri yükleme gerçekleştirebilirsiniz. Özgün sunucuyla aynı Azure bölgesinde yeni bir sunucu oluşturulur. Orijinal sunucunun fiyatlandırma katmanı, bilgi işlem oluşturma, vCore sayısı, depolama boyutu, yedekleme bekletme süresi ve yedekleme artıklığı seçeneği için yapılandırmasıyla oluşturulur.

Zaman içinde geri yükleme birden çok senaryoda yararlıdır. Örneğin, bir kullanıcı yanlışlıkla verileri siler, önemli bir tablo veya veritabanı düşerse veya bir uygulama yanlışlıkla bir uygulama hatası nedeniyle hatalı verilerle iyi verilerin üzerine yazarsa.

Son beş dakika içinde bir noktaya geri yükleyebilmeniz için bir sonraki işlem günlüğü yedeklemesinin alınmasını beklemeniz gerekebilir.

### <a name="geo-restore"></a>Coğrafi Geri Yükleme

Sunucunuzu coğrafi yedekli yedeklemeler için yapılandırıldıysanız, sunucunuzu hizmetin kullanılabildiği başka bir Azure bölgesine geri yükleyebilirsiniz. Coğrafi geri yükleme, sunucunun barındırıldığı bölgede meydana gelen bir olay nedeniyle sunucunuz kullanılamadığında varsayılan kurtarma seçeneğidir. Bir bölgedeki büyük ölçekli bir olay veritabanı uygulamanızın kullanılamamasına neden oluyorsa, bir sunucuyu coğrafi yedekli yedeklemelerden başka bir bölgedeki bir sunucuya geri yükleyebilirsiniz. Coğrafi geri yükleme, sunucunun en son yedeklemesini kullanır. Bir yedeklemenin alınmasıyla farklı bölgeye çoğaltılması arasında bir gecikme vardır. Bu gecikme bir saate kadar olabilir, bu nedenle bir felaket oluşursa, bir saate kadar veri kaybı olabilir.

Coğrafi geri yükleme sırasında değiştirilebilen sunucu yapılandırmaları arasında bilgi işlem oluşturma, vCore, yedekleme bekletme süresi ve yedekleme artıklığı seçenekleri yer almaktadır. Coğrafi geri yükleme sırasında fiyatlandırma katmanını (Temel, Genel Amaç veya Bellek Optimize Edilmiş) veya depolama boyutunu değiştirme desteklenmez.

### <a name="perform-post-restore-tasks"></a>Geri yükleme sonrası görevleri gerçekleştirin

Kurtarma mekanizmasından geri yükleme den sonra, kullanıcılarınızı ve uygulamalarınızı yeniden çalışır hale getirmek için aşağıdaki görevleri gerçekleştirmeniz gerekir:

- Yeni sunucu orijinal sunucunun yerini alacaksa, istemcileri ve istemci uygulamalarını yeni sunucuya yeniden yönlendirin
- Kullanıcıların bağlanması için uygun VNet kurallarının geçerli olduğundan emin olun. Bu kurallar orijinal sunucudan kopyalanmaz.
- Uygun oturum açma ve veritabanı düzeyinde izinlerin yerinde olduğundan emin olun
- Uyarıları uygun şekilde yapılandırma

## <a name="next-steps"></a>Sonraki adımlar

- İş sürekliliği hakkında daha fazla bilgi edinmek için [iş sürekliliğine genel bakışa](concepts-business-continuity.md)bakın.
- Azure portalını kullanarak bir noktada niçin geri yükleme yapmak [için, Azure portalını kullanarak sunucuyu zamanında geri yükleme'ye](howto-restore-server-portal.md)bakın.
- Azure CLI'yi kullanarak tam zamanında geri yüklemek [için, CLI kullanarak sunucunun zamanında geri yükle'ye geri yükle'ye](howto-restore-server-cli.md)bakın.
