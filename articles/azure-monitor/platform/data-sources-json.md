---
title: Azure Monitör'de özel JSON verileri toplama | Microsoft Dokümanlar
description: Özel JSON veri kaynakları, Linux için Log Analytics Agent kullanılarak Azure Monitor'da toplanabilir.  Bu özel veri kaynakları, curl veya FluentD'nin 300+ eklentisi gibi JSON'ı döndüren basit komut dosyaları olabilir. Bu makalede, bu veri toplama için gerekli yapılandırma açıklanır.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 11/28/2018
ms.openlocfilehash: 49eb3fa22bc9afffb9e93f3152cdc00323b76d41
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77662170"
---
# <a name="collecting-custom-json-data-sources-with-the-log-analytics-agent-for-linux-in-azure-monitor"></a>Azure Monitor'da Linux için Log Analytics aracısıyla özel JSON veri kaynakları toplama
[!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)]

Özel JSON veri kaynakları, Linux için Log Analytics aracısı kullanılarak [Azure Monitor'da](data-platform.md) toplanabilir.  Bu özel veri [kaynakları, curl](https://curl.haxx.se/) veya [FluentD'nin 300+ eklentilerinden](https://www.fluentd.org/plugins/all)biri gibi JSON'u döndüren basit komut dosyaları olabilir. Bu makalede, bu veri toplama için gerekli yapılandırma açıklanır.


> [!NOTE]
> Linux v1.1.0-217+ için Log Analytics aracısı Özel JSON Verileri için gereklidir

## <a name="configuration"></a>Yapılandırma

### <a name="configure-input-plugin"></a>Giriş eklentisini yapılandırma

Azure Monitor'da JSON verilerini `oms.api.` toplamak için, giriş eklentisine Bir FluentD etiketinin başlangıcına ekleyin.

Örneğin, aşağıdaki `exec-json.conf` `/etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.d/`ayrı bir yapılandırma dosyası.  Bu, her 30 saniyede `exec` bir kıvrılma komutunu çalıştırmak için FluentD eklentisini kullanır.  Bu komutun çıktısı JSON çıkış eklentisi tarafından toplanır.

```
<source>
  type exec
  command 'curl localhost/json.output'
  format json
  tag oms.api.httpresponse
  run_interval 30s
</source>

<match oms.api.httpresponse>
  type out_oms_api
  log_level info

  buffer_chunk_limit 5m
  buffer_type file
  buffer_path /var/opt/microsoft/omsagent/<workspace id>/state/out_oms_api_httpresponse*.buffer
  buffer_queue_limit 10
  flush_interval 20s
  retry_limit 10
  retry_wait 30s
</match>
```
Altında `/etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.d/` eklenen yapılandırma dosyasının sahipliğinin aşağıdaki komutla değiştirilmesi gerekir.

`sudo chown omsagent:omiusers /etc/opt/microsoft/omsagent/conf/omsagent.d/exec-json.conf`

### <a name="configure-output-plugin"></a>Çıktı eklentisini yapılandırma 
Aşağıdaki çıktı eklentisi yapılandırmasını ana `/etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.conf` yapılandırmaya veya ayrı bir yapılandırma dosyasına yerleştirilen`/etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.d/`

```
<match oms.api.**>
  type out_oms_api
  log_level info

  buffer_chunk_limit 5m
  buffer_type file
  buffer_path /var/opt/microsoft/omsagent/<workspace id>/state/out_oms_api*.buffer
  buffer_queue_limit 10
  flush_interval 20s
  retry_limit 10
  retry_wait 30s
</match>
```

### <a name="restart-log-analytics-agent-for-linux"></a>Linux için Log Analytics aracıyı yeniden başlatın
Linux hizmeti için Log Analytics aracısını aşağıdaki komutla yeniden başlatın.

    sudo /opt/microsoft/omsagent/bin/service_control restart 

## <a name="output"></a>Çıktı
Veriler Azure Monitor'da kayda uygun bir `<FLUENTD_TAG>_CL`..

Örneğin, Azure Monitor'da kayıt türüne `tag oms.api.tomcat` `tomcat_CL`sahip özel etiket .  Aşağıdaki günlük sorgusu ile bu tür tüm kayıtları alabilirsiniz.

    Type=tomcat_CL

İç içe json veri kaynakları desteklenir, ancak ana alanın dışında dizine alınır. Örneğin, aşağıdaki JSON verileri günlük sorgusundan `tag_s : "[{ "a":"1", "b":"2" }]`.

```
{
    "tag": [{
        "a":"1",
        "b":"2"
    }]
}
```


## <a name="next-steps"></a>Sonraki adımlar
* Veri kaynaklarından ve çözümlerinden toplanan verileri analiz etmek için [günlük sorguları](../log-query/log-query-overview.md) hakkında bilgi edinin. 
