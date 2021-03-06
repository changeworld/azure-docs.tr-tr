---
title: Coğrafi yedeklemeden veri ambarı geri yükleme
description: Bir SQL havuzunu coğrafi olarak geri getirmek için nasıl yapılır kılavuzu.
services: synapse-analytics
author: anumjs
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 07/12/2019
ms.author: anjangsh
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 7e0980a9142dc966916d5a4df898ea53b0ddeae5
ms.sourcegitcommit: bd5fee5c56f2cbe74aa8569a1a5bce12a3b3efa6
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2020
ms.locfileid: "80745084"
---
# <a name="geo-restore-for-sql-pool"></a>SQL havuzu için coğrafi geri yükleme

Bu makalede, Azure portalı ve PowerShell aracılığıyla bir coğrafi yedeklemeden SQL havuzunuzu geri yüklemeyi öğrenirsiniz.

## <a name="before-you-begin"></a>Başlamadan önce

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

**DTU kapasitenizi doğrulayın.** Her SQL havuzu, varsayılan DTU kotası olan bir SQL sunucusu (örneğin, myserver.database.windows.net) tarafından barındırılır. SQL sunucusunun veritabanının geri yüklenmesi için yeterli DTU kotası kaldığını doğrulayın. DTU'nun gerekli hesaplamasını öğrenmek veya daha fazla DTU istemek için [bkz.](sql-data-warehouse-get-started-create-support-ticket.md)

## <a name="restore-from-an-azure-geographical-region-through-powershell"></a>PowerShell aracılığıyla Azure coğrafi bölgesinden geri yükleme

Bir coğrafi yedeklemeden geri yüklemek için [Get-AzSqlDatabaseGeoBackup](/powershell/module/az.sql/get-azsqldatabasegeobackup?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) ve [Restore-AzSqlDatabase](/powershell/module/az.sql/restore-azsqldatabase?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) cmdlet'i kullanın.

> [!NOTE]
> Gen2'ye coğrafi geri yükleme yapabilirsiniz! Bunu yapmak için isteğe bağlı bir parametre olarak Gen2 ServiceObjectiveName (örneğin DW1000**c)** belirtin.
>

1. Başlamadan önce [Azure PowerShell'i yüklediğinizden](/powershell/azure/overview?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)emin olun.
2. PowerShell’i açın.
3. Azure hesabınıza bağlanın ve hesabınızla ilişkili tüm abonelikleri listele.
4. Geri yüklenecek veri ambarını içeren aboneliği seçin.
5. Kurtarmak istediğiniz veri ambarını alın.
6. Veri ambarı için kurtarma isteğini oluşturun.
7. Coğrafi olarak geri yüklenen veri ambarının durumunu doğrulayın.
8. Geri yükleme tamamlandıktan sonra veri ambarınızı yapılandırmak için [bkz.]( ../../sql-database/sql-database-disaster-recovery.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json#configure-your-database-after-recovery)

```Powershell
$SubscriptionName="<YourSubscriptionName>"
$ResourceGroupName="<YourResourceGroupName>"
$ServerName="<YourServerNameWithoutURLSuffixSeeNote>"  # Without database.windows.net
$TargetResourceGroupName="<YourTargetResourceGroupName>" # Restore to a different logical server.
$TargetServerName="<YourtargetServerNameWithoutURLSuffixSeeNote>"  
$DatabaseName="<YourDatabaseName>"
$NewDatabaseName="<YourDatabaseName>"
$TargetServiceObjective="<YourTargetServiceObjective-DWXXXc>"

Connect-AzAccount
Get-AzSubscription
Select-AzSubscription -SubscriptionName $SubscriptionName
Get-AzureSqlDatabase -ServerName $ServerName

# Get the data warehouse you want to recover
$GeoBackup = Get-AzSqlDatabaseGeoBackup -ResourceGroupName $ResourceGroupName -ServerName $ServerName -DatabaseName $DatabaseName

# Recover data warehouse
$GeoRestoredDatabase = Restore-AzSqlDatabase –FromGeoBackup -ResourceGroupName $TargetResourceGroupName -ServerName $TargetServerName -TargetDatabaseName $NewDatabaseName –ResourceId $GeoBackup.ResourceID -ServiceObjectiveName $TargetServiceObjective

# Verify that the geo-restored data warehouse is online
$GeoRestoredDatabase.status
```

Kurtarılan veritabanı, kaynak veritabanı TDE etkinse TDE etkin olur.

## <a name="restore-from-an-azure-geographical-region-through-azure-portal"></a>Azure coğrafi bölgesinden Azure portalı üzerinden geri yükleme

Bir SQL havuzunu coğrafi yedeklemeden geri yüklemek için aşağıda özetlenen adımları izleyin:

1. [Azure portal](https://portal.azure.com/) hesabınızda oturum açın.
2. + **Kaynak oluştur'u**tıklatın.

   ![Yeni DW](./media/sql-data-warehouse-restore-from-geo-backup/georestore-new.png)

3. **Veritabanları'nı** ve ardından **Azure Synapse Analytics 'i (eski adıyla SQL DW) tıklatın**

   ![Yeni DW 2](./media/sql-data-warehouse-restore-from-geo-backup/georestore-new-02.png)

4. **Temel bilgiler** sekmesinde istenen bilgileri doldurun ve **İleri: Ek ayarları**tıklatın.

   ![Temel Bilgiler](./media/sql-data-warehouse-restore-from-geo-backup/georestore-dw-1.png)

5. **Varolan veri** parametresini kullanmak için **Yedekleme'yi** seçin ve kaydırma seçeneklerinden uygun yedeklemeyi seçin. **Gözden Geçir + Oluştur'a**tıklayın.

   ![yedekleme](./media/sql-data-warehouse-restore-from-geo-backup/georestore-select.png)

6. Veri ambarı geri yüklendikten **sonra, Durum'un** Çevrimiçi olup olmadığını denetleyin.

## <a name="next-steps"></a>Sonraki Adımlar

- [Varolan bir SQL havuzunun geri yüklemesi](sql-data-warehouse-restore-active-paused-dw.md)
- [Silinen bir SQL havuzunun geri yüklemesi](sql-data-warehouse-restore-deleted-dw.md)
