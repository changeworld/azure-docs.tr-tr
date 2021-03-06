---
title: Azure Uygulama Öngörüleri AracıAP başvurusu
description: Uygulama Öngörüleri Aracısı API başvurusu. Get-ApplicationInsightsMonitoringConfig. Web sitesini yeniden dağıtmadan web sitesinin performansını izleyin. Şirket içinde, VM'lerde veya Azure'da barındırılan ASP.NET web uygulamalarıyla çalışır.
ms.topic: conceptual
author: TimothyMothra
ms.author: tilee
ms.date: 04/23/2019
ms.openlocfilehash: 93ab8f613e5634b2eabe7c02189e223d3dfbb0a8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77671282"
---
# <a name="application-insights-agent-api-get-applicationinsightsmonitoringconfig"></a>Uygulama Insights Ajan API: Get-ApplicationInsightsMonitoringConfig

Bu [makalede, Az.ApplicationMonitor PowerShell modülüüyesi](https://www.powershellgallery.com/packages/Az.ApplicationMonitor/)bir cmdlet açıklanır.

## <a name="description"></a>Açıklama

Config dosyasını alır ve değerleri konsola yazdırır.

> [!IMPORTANT] 
> Bu cmdlet, Yönetici izinleri içeren bir PowerShell oturumu gerektirir.

## <a name="examples"></a>Örnekler

```powershell
PS C:\> Get-ApplicationInsightsMonitoringConfig
```

## <a name="parameters"></a>Parametreler

Parametre gerekmez.

## <a name="output"></a>Çıktı


#### <a name="example-output-from-reading-the-config-file"></a>Config dosyasını okuyarak örnek çıktı

```
RedfieldConfiguration:
Filters:
0)InstrumentationKey:  AppFilter: WebAppExclude MachineFilter: .*
1)InstrumentationKey: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx2 AppFilter: WebAppTwo MachineFilter: .*
2)InstrumentationKey: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxdefault AppFilter: .* MachineFilter: .*
```

## <a name="next-steps"></a>Sonraki adımlar

  Telemetrinizi görüntüleyin:
 - Performansı ve kullanımı izlemek için [ölçümleri keşfedin.](../../azure-monitor/app/metrics-explorer.md)
- Sorunları tanılamak için [olayları ve günlükleri arayın.](../../azure-monitor/app/diagnostic-search.md)
- Daha gelişmiş sorgular için [analitiği](../../azure-monitor/app/analytics.md) kullanın.
- [Panolar oluşturun.](../../azure-monitor/app/overview-dashboard.md)
 
 Daha fazla telemetri ekleyin:
 - Sitenizin canlı kalması için [web testleri oluşturun](monitor-web-app-availability.md).
- Web sayfası kodundan özel durumları görmek ve izleme çağrılarını etkinleştirmek için [web istemcisi telemetrisi ekleyin.](../../azure-monitor/app/javascript.md)
- İzleme ve günlük aramaları ekleyebilmeniz [için Uygulama Öngörüleri SDK'yı kodunuza ekleyin.](../../azure-monitor/app/asp-net.md)
 
 Application Insights Agent ile daha fazlasını yapın:
 - Uygulama Öngörüleri [Aracısı'nın sorun giderme](status-monitor-v2-troubleshoot.md) kılavuzunu kullanın.
 - [Set config](status-monitor-v2-api-set-config.md) cmdlet kullanarak config değişiklikler yapın.
