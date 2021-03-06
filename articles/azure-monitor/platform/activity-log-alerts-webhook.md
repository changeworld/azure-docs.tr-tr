---
title: Etkinlik günlüğü uyarılarında kullanılan webhook şeasını anlama
description: Etkinlik günlüğü uyarısı etkinleştirildiğinde webhook URL'sine gönderilen JSON şeması hakkında bilgi edinin.
ms.topic: conceptual
ms.date: 03/31/2017
ms.subservice: alerts
ms.openlocfilehash: c076b8dcea350f9ddd66977e89ce99b81f377b17
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77669055"
---
# <a name="webhooks-for-azure-activity-log-alerts"></a>Azure etkinlik günlüğü uyarıları için Webhooks
Bir eylem grubunun tanımının bir parçası olarak, etkinlik günlüğü uyarı bildirimleri almak için webhook uç noktalarını yapılandırabilirsiniz. Web hooks ile, bu bildirimleri işleme sonrası veya özel eylemler için diğer sistemlere yönlendirebilirsiniz. Bu makalede, bir webhook için HTTP POST için yük nasıl göründüğünü gösterir.

Etkinlik günlüğü uyarıları hakkında daha fazla bilgi için [Azure etkinlik günlüğü uyarılarının](activity-log-alerts.md)nasıl oluşturulabildiğini öğrenin.

Eylem grupları hakkında bilgi [için, eylem gruplarının](../../azure-monitor/platform/action-groups.md)nasıl oluşturulabildiğini görün.

> [!NOTE]
> Webhook tümleştirmeleriniz için Azure Monitor'daki tüm uyarı hizmetlerinde tek bir genişletilebilir ve birleşik uyarı yüküne sahip olmanın avantajını sağlayan [ortak uyarı şemasını](https://aka.ms/commonAlertSchemaDocs)da kullanabilirsiniz. [Ortak uyarı şeması tanımları hakkında bilgi edinin.](https://aka.ms/commonAlertSchemaDefinitions)


## <a name="authenticate-the-webhook"></a>Webhook'u doğrula
Webhook isteğe bağlı olarak kimlik doğrulama için belirteç tabanlı yetkilendirme kullanabilirsiniz. Webhook URI bir belirteç kimliği ile `https://mysamplealert/webcallback?tokenid=sometokenid&someparameter=somevalue`kaydedilir, örneğin.

## <a name="payload-schema"></a>Yük şeması
POST işleminde yer alan JSON yükü, yükün data.context.activityLog.eventSource alanına göre farklılık gösterir.

### <a name="common"></a>Common

```json
{
    "schemaId": "Microsoft.Insights/activityLogs",
    "data": {
        "status": "Activated",
        "context": {
            "activityLog": {
                "channels": "Operation",
                "correlationId": "6ac88262-43be-4adf-a11c-bd2179852898",
                "eventSource": "Administrative",
                "eventTimestamp": "2017-03-29T15:43:08.0019532+00:00",
                "eventDataId": "8195a56a-85de-4663-943e-1a2bf401ad94",
                "level": "Informational",
                "operationName": "Microsoft.Insights/actionGroups/write",
                "operationId": "6ac88262-43be-4adf-a11c-bd2179852898",
                "status": "Started",
                "subStatus": "",
                "subscriptionId": "52c65f65-0518-4d37-9719-7dbbfc68c57a",
                "submissionTimestamp": "2017-03-29T15:43:20.3863637+00:00",
                ...
            }
        },
        "properties": {}
    }
}
```

### <a name="administrative"></a>Yönetim

```json
{
    "schemaId": "Microsoft.Insights/activityLogs",
    "data": {
        "status": "Activated",
        "context": {
            "activityLog": {
                "authorization": {
                    "action": "Microsoft.Insights/actionGroups/write",
                    "scope": "/subscriptions/52c65f65-0518-4d37-9719-7dbbfc68c57b/resourceGroups/CONTOSO-TEST/providers/Microsoft.Insights/actionGroups/IncidentActions"
                },
                "claims": "{...}",
                "caller": "me@contoso.com",
                "description": "",
                "httpRequest": "{...}",
                "resourceId": "/subscriptions/52c65f65-0518-4d37-9719-7dbbfc68c57b/resourceGroups/CONTOSO-TEST/providers/Microsoft.Insights/actionGroups/IncidentActions",
                "resourceGroupName": "CONTOSO-TEST",
                "resourceProviderName": "Microsoft.Insights",
                "resourceType": "Microsoft.Insights/actionGroups"
            }
        },
        "properties": {}
    }
}
```

### <a name="security"></a>Güvenlik

```json
{
    "schemaId":"Microsoft.Insights/activityLogs",
    "data":{"status":"Activated",
        "context":{
            "activityLog":{
                "channels":"Operation",
                "correlationId":"2518408115673929999",
                "description":"Failed SSH brute force attack. Failed brute force attacks were detected from the following attackers: [\"IP Address: 01.02.03.04\"].  Attackers were trying to access the host with the following user names: [\"root\"].",
                "eventSource":"Security",
                "eventTimestamp":"2017-06-25T19:00:32.607+00:00",
                "eventDataId":"Sec-07f2-4d74-aaf0-03d2f53d5a33",
                "level":"Informational",
                "operationName":"Microsoft.Security/locations/alerts/activate/action",
                "operationId":"Sec-07f2-4d74-aaf0-03d2f53d5a33",
                "properties":{
                    "attackers":"[\"IP Address: 01.02.03.04\"]",
                    "numberOfFailedAuthenticationAttemptsToHost":"456",
                    "accountsUsedOnFailedSignInToHostAttempts":"[\"root\"]",
                    "wasSSHSessionInitiated":"No","endTimeUTC":"06/25/2017 19:59:39",
                    "actionTaken":"Detected",
                    "resourceType":"Virtual Machine",
                    "severity":"Medium",
                    "compromisedEntity":"LinuxVM1",
                    "remediationSteps":"[In case this is an Azure virtual machine, add the source IP to NSG block list for 24 hours (see https://azure.microsoft.com/documentation/articles/virtual-networks-nsg/)]",
                    "attackedResourceType":"Virtual Machine"
                },
                "resourceId":"/subscriptions/12345-5645-123a-9867-123b45a6789/resourceGroups/contoso/providers/Microsoft.Security/locations/centralus/alerts/Sec-07f2-4d74-aaf0-03d2f53d5a33",
                "resourceGroupName":"contoso",
                "resourceProviderName":"Microsoft.Security",
                "status":"Active",
                "subscriptionId":"12345-5645-123a-9867-123b45a6789",
                "submissionTimestamp":"2017-06-25T20:23:04.9743772+00:00",
                "resourceType":"MICROSOFT.SECURITY/LOCATIONS/ALERTS"
            }
        },
        "properties":{}
    }
}
```

### <a name="recommendation"></a>Öneri

```json
{
    "schemaId":"Microsoft.Insights/activityLogs",
    "data":{
        "status":"Activated",
        "context":{
            "activityLog":{
                "channels":"Operation",
                "claims":"{\"http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress\":\"Microsoft.Advisor\"}",
                "caller":"Microsoft.Advisor",
                "correlationId":"123b4c54-11bb-3d65-89f1-0678da7891bd",
                "description":"A new recommendation is available.",
                "eventSource":"Recommendation",
                "eventTimestamp":"2017-06-29T13:52:33.2742943+00:00",
                "httpRequest":"{\"clientIpAddress\":\"0.0.0.0\"}",
                "eventDataId":"1bf234ef-e45f-4567-8bba-fb9b0ee1dbcb",
                "level":"Informational",
                "operationName":"Microsoft.Advisor/recommendations/available/action",
                "properties":{
                    "recommendationSchemaVersion":"1.0",
                    "recommendationCategory":"HighAvailability",
                    "recommendationImpact":"Medium",
                    "recommendationName":"Enable Soft Delete to protect your blob data",
                    "recommendationResourceLink":"https://portal.azure.com/#blade/Microsoft_Azure_Expert/RecommendationListBlade/recommendationTypeId/12dbf883-5e4b-4f56-7da8-123b45c4b6e6",
                    "recommendationType":"12dbf883-5e4b-4f56-7da8-123b45c4b6e6"
                },
                "resourceId":"/subscriptions/12345-5645-123a-9867-123b45a6789/resourceGroups/contoso/providers/microsoft.storage/storageaccounts/contosoStore",
                "resourceGroupName":"CONTOSO",
                "resourceProviderName":"MICROSOFT.STORAGE",
                "status":"Active",
                "subStatus":"",
                "subscriptionId":"12345-5645-123a-9867-123b45a6789",
                "submissionTimestamp":"2017-06-29T13:52:33.2742943+00:00",
                "resourceType":"MICROSOFT.STORAGE/STORAGEACCOUNTS"
            }
        },
        "properties":{}
    }
}
```

### <a name="servicehealth"></a>ServiceHealth

```json
{
    "schemaId": "Microsoft.Insights/activityLogs",
    "data": {
        "status": "Activated",
        "context": {
            "activityLog": {
            "channels": "Admin",
            "correlationId": "bbac944f-ddc0-4b4c-aa85-cc7dc5d5c1a6",
            "description": "Active: Virtual Machines - Australia East",
            "eventSource": "ServiceHealth",
            "eventTimestamp": "2017-10-18T23:49:25.3736084+00:00",
            "eventDataId": "6fa98c0f-334a-b066-1934-1a4b3d929856",
            "level": "Informational",
            "operationName": "Microsoft.ServiceHealth/incident/action",
            "operationId": "bbac944f-ddc0-4b4c-aa85-cc7dc5d5c1a6",
            "properties": {
                "title": "Virtual Machines - Australia East",
                "service": "Virtual Machines",
                "region": "Australia East",
                "communication": "Starting at 02:48 UTC on 18 Oct 2017 you have been identified as a customer using Virtual Machines in Australia East who may receive errors starting Dv2 Promo and DSv2 Promo Virtual Machines which are in a stopped &quot;deallocated&quot; or suspended state. Customers can still provision Dv1 and Dv2 series Virtual Machines or try deploying Virtual Machines in other regions, as a possible workaround. Engineers have identified a possible fix for the underlying cause, and are exploring implementation options. The next update will be provided as events warrant.",
                "incidentType": "Incident",
                "trackingId": "0NIH-U2O",
                "impactStartTime": "2017-10-18T02:48:00.0000000Z",
                "impactedServices": "[{\"ImpactedRegions\":[{\"RegionName\":\"Australia East\"}],\"ServiceName\":\"Virtual Machines\"}]",
                "defaultLanguageTitle": "Virtual Machines - Australia East",
                "defaultLanguageContent": "Starting at 02:48 UTC on 18 Oct 2017 you have been identified as a customer using Virtual Machines in Australia East who may receive errors starting Dv2 Promo and DSv2 Promo Virtual Machines which are in a stopped &quot;deallocated&quot; or suspended state. Customers can still provision Dv1 and Dv2 series Virtual Machines or try deploying Virtual Machines in other regions, as a possible workaround. Engineers have identified a possible fix for the underlying cause, and are exploring implementation options. The next update will be provided as events warrant.",
                "stage": "Active",
                "communicationId": "636439673646212912",
                "version": "0.1.1"
            },
            "status": "Active",
            "subscriptionId": "45529734-0ed9-4895-a0df-44b59a5a07f9",
            "submissionTimestamp": "2017-10-18T23:49:28.7864349+00:00"
        }
    },
    "properties": {}
    }
}
```

Hizmet durumu bildirimi etkinlik günlüğü uyarılarıyla ilgili belirli şema ayrıntıları için [Hizmet durumu bildirimleri'ne](../../azure-monitor/platform/service-notifications.md)bakın. Ayrıca, [mevcut sorun yönetimi çözümleri ile hizmet durumu webhook bildirimleri yapılandırmak](../../service-health/service-health-alert-webhook-guide.md)için nasıl öğrenin.

### <a name="resourcehealth"></a>KaynakSağlık

```json
{
    "schemaId": "Microsoft.Insights/activityLogs",
    "data": {
        "status": "Activated",
        "context": {
            "activityLog": {
                "channels": "Admin, Operation",
                "correlationId": "a1be61fd-37ur-ba05-b827-cb874708babf",
                "eventSource": "ResourceHealth",
                "eventTimestamp": "2018-09-04T23:09:03.343+00:00",
                "eventDataId": "2b37e2d0-7bda-4de7-ur8c6-1447d02265b2",
                "level": "Informational",
                "operationName": "Microsoft.Resourcehealth/healthevent/Activated/action",
                "operationId": "2b37e2d0-7bda-489f-81c6-1447d02265b2",
                "properties": {
                    "title": "Virtual Machine health status changed to unavailable",
                    "details": "Virtual machine has experienced an unexpected event",
                    "currentHealthStatus": "Unavailable",
                    "previousHealthStatus": "Available",
                    "type": "Downtime",
                    "cause": "PlatformInitiated"
                },
                "resourceId": "/subscriptions/<subscription Id>/resourceGroups/<resource group>/providers/Microsoft.Compute/virtualMachines/<resource name>",
                "resourceGroupName": "<resource group>",
                "resourceProviderName": "Microsoft.Resourcehealth/healthevent/action",
                "status": "Active",
                "subscriptionId": "<subscription Id>",
                "submissionTimestamp": "2018-09-04T23:11:06.1607287+00:00",
                "resourceType": "Microsoft.Compute/virtualMachines"
            }
        }
    }
}
```

| Öğe adı | Açıklama |
| --- | --- |
| durum |Metrik uyarılar için kullanılır. Etkinlik günlüğü uyarıları için her zaman "etkinleştirildi". |
| bağlam |Olayın bağlamı. |
| kaynakProviderName |Etkilenen kaynağın kaynak sağlayıcısı. |
| Conditiontype |Her zaman "Olay" |
| ad |Uyarı kuralının adı. |
| id |Uyarının kaynak kimliği. |
| açıklama |Uyarı oluşturulduğunda uyarı açıklaması ayarlanır. |
| subscriptionId |Azure abonelik kimliği. |
| timestamp |Etkinliğin isteği işleyen Azure hizmeti tarafından oluşturulduğu saat. |
| resourceId |Etkilenen kaynağın kaynak kimliği. |
| resourceGroupName |Etkilenen kaynak için kaynak grubunun adı. |
| properties |Olayla `<Key, Value>` ilgili ayrıntıları içeren `Dictionary<String, String>`çiftler kümesi (diğer bir zamanda). |
| event |Olay la ilgili meta verileri içeren öğe. |
| yetkilendirme |Olayın Rol Tabanlı Erişim Denetimi özellikleri. Bu özellikler genellikle eylemi, rolü ve kapsamı içerir. |
| category |Etkinliğin kategorisi. Desteklenen değerler Yönetim, Uyarı, Güvenlik, ServiceHealth ve Öneri içerir. |
| Ara -yan |İşi gerçekleştiren kullanıcının e-posta adresi, UPN talebi veya kullanılabilirlik durumuna göre SPN talebi. Belirli sistem aramaları için null olabilir. |
| correlationId |Genellikle dize biçiminde bir GUID. Bağıntılı Olaylar aynı büyük eyleme aittir ve genellikle bir korelasyonid paylaşır. |
| eventDescription |Olayın statik metin açıklaması. |
| olayDataId |Etkinlik için benzersiz tanımlayıcı. |
| Olaykaynağı |Etkinliği oluşturan Azure hizmetinin veya altyapısının adı. |
| httpİstek |İstek genellikle clientRequestId, clientIpAddress ve HTTP yöntemini (örneğin, PUT) içerir. |
| düzey |Aşağıdaki değerlerden biri: Kritik, Hata, Uyarı ve Bilgilendirme. |
| operationId |Genellikle tek bir işlemle karşılık gelen olaylar arasında paylaşılan bir GUID. |
| operationName |Operasyonun adı. |
| properties |Olayın özellikleri. |
| durum |Dize. Operasyonun durumu. Ortak değerler, Başlat, Devam Ediyor, Başarılı, Başarısız, Etkin ve Çözümlenmiş'i içerir. |
| altDurum |Genellikle ilgili REST çağrısının HTTP durum kodunu içerir. Bir alt durumu açıklayan diğer dizeleri de içerebilir. Ortak alt durum değerleri arasında OK (HTTP Durum Kodu: 200), Oluşturulan (HTTP Durum Kodu: 201), Kabul Edilen (HTTP Durum Kodu: 202), İçerik Yok (HTTP Durum Kodu: 204), Kötü İstek (HTTP Durum Kodu: 400), Bulunamadı (HTTP Durum Kodu: 404), Çakışma (HTTP Durum Kodu: 409 ), Dahili Sunucu Hatası (HTTP Durum Kodu: 500), Hizmet Kullanılamıyor (HTTP Durum Kodu: 503) ve Ağ Geçidi Zaman Ası (HTTP Durum Kodu: 504). |

Diğer tüm etkinlik günlüğü uyarılarındaki belirli şema ayrıntıları için Azure [etkinlik günlüğüne genel bakış bölümüne](../../azure-monitor/platform/platform-logs-overview.md)bakın.

## <a name="next-steps"></a>Sonraki adımlar
* [Etkinlik günlüğü hakkında daha fazla bilgi edinin.](../../azure-monitor/platform/platform-logs-overview.md)
* [Azure uyarılarında Azure otomasyon komut dosyalarını (Runbook) çalıştırın.](https://go.microsoft.com/fwlink/?LinkId=627081)
* [Azure uyarısı üzerinden Twilio üzerinden SMS göndermek için bir mantık uygulaması kullanın.](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-text-message-with-logic-app) Bu örnek metrik uyarılar içindir, ancak bir etkinlik günlüğü uyarısı ile çalışacak şekilde değiştirilebilir.
* [Azure uyarısından Bir Bolluk iletisi göndermek için bir mantık uygulaması kullanın.](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-slack-with-logic-app) Bu örnek metrik uyarılar içindir, ancak bir etkinlik günlüğü uyarısı ile çalışacak şekilde değiştirilebilir.
* [Azure uyarısından Azure kuyruğuna ileti göndermek için bir mantık uygulaması kullanın.](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-queue-with-logic-app) Bu örnek metrik uyarılar içindir, ancak bir etkinlik günlüğü uyarısı ile çalışacak şekilde değiştirilebilir.

