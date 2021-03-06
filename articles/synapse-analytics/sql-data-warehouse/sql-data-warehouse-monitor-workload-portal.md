---
title: İş yükünü izleyin - Azure portalı
description: Azure portalını kullanarak Synapse SQL'i izleyin
services: synapse-analytics
author: kevinvngo
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 02/04/2020
ms.author: kevin
ms.reviewer: jrasnick
ms.openlocfilehash: 0658a775e40c1fc433c7c2e1d853493544e74ee4
ms.sourcegitcommit: bd5fee5c56f2cbe74aa8569a1a5bce12a3b3efa6
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2020
ms.locfileid: "80743208"
---
# <a name="monitor-workload---azure-portal"></a>İş yükünü izleyin - Azure portalı

Bu makalede, iş yükünüzü izlemek için Azure portalının nasıl kullanılacağı açıklanmaktadır. Buna, [Synapse SQL](https://azure.microsoft.com/blog/workload-insights-with-sql-data-warehouse-delivered-through-azure-monitor-diagnostic-logs-pass/)için günlük analitiğini kullanarak sorgu yürütmesini ve iş yükü eğilimlerini araştırmak için Azure Monitor Günlükleri'ni ayarlamayı içerir.

## <a name="prerequisites"></a>Ön koşullar

- Azure aboneliği: Azure aboneliğiniz yoksa, başlamadan önce ücretsiz bir [hesap](https://azure.microsoft.com/free/) oluşturun.
- SQL havuzu: Bir SQL havuzu için günlükleri topluyor olacağız. Sql havuzu sağlanmış değilseniz, [SQL havuzu oluştur'daki](load-data-from-azure-blob-storage-using-polybase.md)yönergeleri görün.

## <a name="create-a-log-analytics-workspace"></a>Log Analytics çalışma alanı oluşturma

Log Analytics çalışma alanları için gözatma bıçağına gidin ve bir çalışma alanı oluşturun

![Log Analytics çalışma alanları](./media/sql-data-warehouse-monitor-workload-portal/log_analytics_workspaces.png)

![Analitik çalışma alanı ekle](./media/sql-data-warehouse-monitor-workload-portal/add_analytics_workspace.png)

![Analitik çalışma alanı ekle](./media/sql-data-warehouse-monitor-workload-portal/add_analytics_workspace_2.png)

Çalışma alanları hakkında daha fazla bilgi için aşağıdaki belgeleri ziyaret [edin.](../../azure-monitor/learn/quick-create-workspace.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.jsond#create-a-workspace)

## <a name="turn-on-diagnostic-logs"></a>Tanılama günlüklerini açma

SQL havuzunuzdan günlükler yayarlar için tanılama ayarlarını yapılandırın. Günlükler, en sık kullanılan performans sorun giderme DMV'lerine eşdeğer telemetri görünümlerinden oluşur. Şu anda aşağıdaki görünümler desteklenir:

- [sys.dm_pdw_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)
- [sys.dm_pdw_request_steps](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-request-steps-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)
- [sys.dm_pdw_dms_workers](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-dms-workers-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)
- [sys.dm_pdw_waits](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-waits-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)
- [sys.dm_pdw_sql_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-sql-requests-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)

![Tanılama günlüklerini etkinleştirme](./media/sql-data-warehouse-monitor-workload-portal/enable_diagnostic_logs.png)

Günlükler Azure Depolama, Akış Analizi veya Log Analytics'e yayılabilir. Bu eğitim için Log Analytics'i seçin.

![Günlükleri belirtin](./media/sql-data-warehouse-monitor-workload-portal/specify_logs.png)

## <a name="run-queries-against-log-analytics"></a>Günlük Analitiği'ne karşı sorguları çalıştırın

Aşağıdakileri yapabileceğiniz Log Analytics çalışma alanınıza gidin:

- Günlük sorgularını kullanarak günlükleri analiz edin ve sorguları yeniden kullanmak için kaydedin
- Sorguları yeniden kullanmak için kaydetme
- Günlük uyarıları oluşturma
- Sorgu sonuçlarını panoya sabitleme

Günlük sorgularının özellikleri hakkında ayrıntılı bilgi için aşağıdaki belgeleri ziyaret [edin.](../../azure-monitor/log-query/query-language.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)

![Log Analytics çalışma alanı düzenleyicisi](./media/sql-data-warehouse-monitor-workload-portal/log_analytics_workspace_editor.png)

![Günlük Analytics çalışma alanı sorguları](./media/sql-data-warehouse-monitor-workload-portal/log_analytics_workspace_queries.png)

## <a name="sample-log-queries"></a>Örnek günlük sorguları

```Kusto
//List all queries
AzureDiagnostics
| where Category contains "ExecRequests"
| project TimeGenerated, StartTime_t, EndTime_t, Status_s, Command_s, ResourceClass_s, duration=datetime_diff('millisecond',EndTime_t, StartTime_t)
```

```Kusto
//Chart the most active resource classes
AzureDiagnostics
| where Category contains "ExecRequests"
| where Status_s == "Completed"
| summarize totalQueries = dcount(RequestId_s) by ResourceClass_s
| render barchart
```

```Kusto
//Count of all queued queries
AzureDiagnostics
| where Category contains "waits"
| where Type_s == "UserConcurrencyResourceType"
| summarize totalQueuedQueries = dcount(RequestId_s)
```

## <a name="next-steps"></a>Sonraki adımlar

Azure monitör günlüklerini ayarladığınızda ve yapılandırdığınıza göre, Azure panolarını ekibinizde paylaşacak şekilde [özelleştirin.](../../azure-portal/azure-portal-dashboards.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)
