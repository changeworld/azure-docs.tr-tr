---
title: Azure Ağ İzleyicisi ile paket yakalamaları yönetme - REST API | Microsoft Dokümanlar
description: Bu sayfa, Azure REST API'sini kullanarak Ağ İzleyicisi'nin paket yakalama özelliğini nasıl yöneteceğimi açıklar
services: network-watcher
documentationcenter: na
author: damendo
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: damendo
ms.openlocfilehash: 5199cf95452f93db2c2dd747fcabc67a6722d31e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76840902"
---
# <a name="manage-packet-captures-with-azure-network-watcher-using-azure-rest-api"></a>Azure REST API'sini kullanarak Azure Ağ İzleyicisi ile paket yakalamaları yönetme

> [!div class="op_single_selector"]
> - [Azure portalında](network-watcher-packet-capture-manage-portal.md)
> - [Powershell](network-watcher-packet-capture-manage-powershell.md)
> - [Azure CLI](network-watcher-packet-capture-manage-cli.md)
> - [Azure REST API](network-watcher-packet-capture-manage-rest.md)

Network Watcher paket yakalama ve sanal bir makineden gelen trafiği izlemek için yakalama oturumları oluşturmanıza olanak sağlar. Yalnızca istediğiniz trafiği yakalamanızı sağlamak için yakalama oturumu için filtreler sağlanır. Paket yakalama, ağ anomalilerinin hem reaktif hem de proaktif olarak tanılenmesine yardımcı olur. Diğer kullanım alanları arasında ağ istatistiklerinin toplanması, ağ ihlalleri hakkında bilgi edinme, istemci-sunucu iletişimini hata ayıklama ve çok daha fazlası yer almaktadır. Paket yakalamalarını uzaktan tetikleyebilen bu özellik, paket yakalamayı el ile ve istenilen makinede çalıştırma nın yükünü hafifleterek değerli zamandan tasarruf sağlar.

Bu makalede, şu anda paket yakalama için kullanılabilir olan farklı yönetim görevleri aracılığıyla alır.

- [**Paket yakalama alma**](#get-a-packet-capture)
- [**Tüm paket yakalamaları listele**](#list-all-packet-captures)
- [**Paket yakalama durumunu sorgula**](#query-packet-capture-status)
- [**Paket yakalama başlatma**](#start-packet-capture)
- [**Paket yakalamayı durdurma**](#stop-packet-capture)
- [**Paket yakalamayı silme**](#delete-packet-capture)


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="before-you-begin"></a>Başlamadan önce

Bu senaryoda, IP Akış Doğrulaması çalıştırmak için Ağ İzleyicisi Dinlenme API'sini çağırırsınız. ARMclient PowerShell kullanarak REST API aramak için kullanılır. ARMClient [Chocolatey armclient](https://chocolatey.org/packages/ARMClient) çikolataüzerinde bulunur

Bu senaryo, Ağ İzleyicisi oluşturmak için [Ağ İzleyicisi Oluştur'daki](network-watcher-create.md) adımları zaten izlediğinizi varsayar.

> Paket yakalama sanal bir `AzureNetworkWatcherExtension`makine uzantısı gerektirir. Uzantıyı Windows VM'de yüklemek [için Windows için Azure Network Watcher Agent sanal makine uzantısını](../virtual-machines/windows/extensions-nwa.md) ziyaret edin ve Linux VM için Linux için Azure Network [Watcher Agent sanal makine uzantısını](../virtual-machines/linux/extensions-nwa.md)ziyaret edin.

## <a name="log-in-with-armclient"></a>ARMClient ile giriş yapın

```powershell
armclient login
```

## <a name="retrieve-a-virtual-machine"></a>Sanal makine alma

Sanal bir makineyi döndürmek için aşağıdaki komut dosyasını çalıştırın. Bu bilgiler, paket yakalama başlatmak için gereklidir.

Aşağıdaki kod değişkenleri gerekir:

- **subscriptionId** - Abonelik kimliği **Get-AzSubscription** cmdlet ile de alınabilir.
- **resourceGroupName** - Sanal makineler içeren bir kaynak grubunun adı.

```powershell
$subscriptionId = "<subscription id>"
$resourceGroupName = "<resource group name>"

armclient get https://management.azure.com/subscriptions/${subscriptionId}/ResourceGroups/${resourceGroupName}/providers/Microsoft.Compute/virtualMachines?api-version=2015-05-01-preview
```

Aşağıdaki çıktıdan, sanal makinenin kimliği sonraki örnekte kullanılır.

```json
...
,
      "type": "Microsoft.Compute/virtualMachines",
      "location": "westcentralus",
      "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/ContosoExampleRG/providers/Microsoft.Compute
/virtualMachines/ContosoVM",
      "name": "ContosoVM"
    }
  ]
}
```


## <a name="get-a-packet-capture"></a>Paket yakalama alma

Aşağıdaki örnek, tek bir paket yakalama durumunu alır

```powershell
$subscriptionId = "<subscription id>"
$resourceGroupName = "NetworkWatcherRG"
$networkWatcherName = "NetworkWatcher_westcentralus"
armclient post "https://management.azure.com/subscriptions/${subscriptionId}/ResourceGroups/${resourceGroupName}/providers/Microsoft.Network/networkWatchers/${networkWatcherName}/packetCaptures/${packetCaptureName}/querystatus?api-version=2016-12-01"
```

Aşağıdaki yanıtlar, paket yakalama durumunu sorgularken döndürülen tipik bir yanıta örnektir.

```json
{
  "name": "TestPacketCapture5",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/NetworkWatcherRG/providers/Microsoft.Network/networkWatchers/NetworkWatcher_westcentralus/packetCaptures/TestPacketCapture6",
  "captureStartTime": "2016-12-06T17:20:01.5671279Z",
  "packetCaptureStatus": "Running",
  "packetCaptureError": []
}
```

```json
{
  "name": "TestPacketCapture5",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/NetworkWatcherRG/providers/Microsoft.Network/networkWatchers/NetworkWatcher_westcentralus/packetCaptures/TestPacketCapture6",
  "captureStartTime": "2016-12-06T17:20:01.5671279Z",
  "packetCaptureStatus": "Stopped",
  "stopReason": "TimeExceeded",
  "packetCaptureError": []
}
```

## <a name="list-all-packet-captures"></a>Tüm paket yakalamaları listele

Aşağıdaki örnek, bir bölgedeki tüm paket yakalama oturumlarını alır.

```powershell
$subscriptionId = "<subscription id>"
$resourceGroupName = "NetworkWatcherRG"
$networkWatcherName = "NetworkWatcher_westcentralus"
armclient get "https://management.azure.com/subscriptions/${subscriptionId}/ResourceGroups/${resourceGroupName}/providers/Microsoft.Network/networkWatchers/${networkWatcherName}/packetCaptures?api-version=2016-12-01"
```

Aşağıdaki yanıt, tüm paket yakalamalarını alırken döndürülen tipik bir yanıtın bir örneğidir

```json
{
  "value": [
    {
      "name": "TestPacketCapture6",
      "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/NetworkWatcherRG/providers/Microsoft.Network/networkWatchers/NetworkWatcher_westcentralus/packetCaptures/TestPacketCapture6",
      "etag": "W/\"091762e1-c23f-448b-89d5-37cf56e4c045\"",
      "properties": {
        "provisioningState": "Succeeded",
        "target": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/ContosoExampleRG/providers/Microsoft.Compute/virtualMachines/ContosoVM",
        "bytesToCapturePerPacket": 0,
        "totalBytesPerSession": 1073741824,
        "timeLimitInSeconds": 60,
        "storageLocation": {
          "storageId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/ContosoExampleRG/providers/Microsoft.Storage/storageAccounts/contosoexamplergdiag374",
          "storagePath": "https://contosoexamplergdiag374.blob.core.windows.net/network-watcher-logs/subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/contosoexamplerg/providers/microsoft.compute/virtualmachines/contosovm/2016/12/06/packetcap
ture_17_19_53_056.cap",
          "filePath": "c:\\temp\\packetcapture.cap"
        },
        "filters": [
          {
            "protocol": "Any",
            "localIPAddress": "",
            "localPort": "",
            "remoteIPAddress": "",
            "remotePort": ""
          }
        ]
      }
    },
    {
      "name": "TestPacketCapture7",
      "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/NetworkWatcherRG/providers/Microsoft.Network/networkWatchers/NetworkWatcher_westcentralus/packetCaptures/TestPacketCapture7",
      "etag": "W/\"091762e1-c23f-448b-89d5-37cf56e4c045\"",
      "properties": {
        "provisioningState": "Failed",
        "target": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/ContosoExampleRG/providers/Microsoft.Compute/virtualMachines/ContosoVM",
        "bytesToCapturePerPacket": 0,
        "totalBytesPerSession": 1073741824,
        "timeLimitInSeconds": 60,
        "storageLocation": {
          "storageId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/ContosoExampleRG/providers/Microsoft.Storage/storageAccounts/contosoexamplergdiag374",
          "storagePath": "https://contosoexamplergdiag374.blob.core.windows.net/network-watcher-logs/subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/contosoexamplerg/providers/microsoft.compute/virtualmachines/contosovm/2016/12/06/packetcap
ture_17_23_15_364.cap",
          "filePath": "c:\\temp\\packetcapture.cap"
        },
        "filters": [
          {
            "protocol": "Any",
            "localIPAddress": "",
            "localPort": "",
            "remoteIPAddress": "",
            "remotePort": ""
          }
        ]
      }
    }
  ]
}
```

## <a name="query-packet-capture-status"></a>Paket yakalama durumunu sorgula

Aşağıdaki örnek, bir bölgedeki tüm paket yakalama oturumlarını alır.

```powershell
$subscriptionId = "<subscription id>"
$resourceGroupName = "NetworkWatcherRG"
$networkWatcherName = "NetworkWatcher_westcentralus"
$packetCaptureName = "TestPacketCapture5"
armclient get "https://management.azure.com/subscriptions/${subscriptionId}/ResourceGroups/${resourceGroupName}/providers/Microsoft.Network/networkWatchers/${networkWatcherName}/packetCaptures/${packetCaptureName}/querystatus?api-version=2016-12-01"
```

Aşağıdaki yanıt, paket yakalama durumunu sorgularken döndürülen tipik bir yanıt örneğidir.

```json
{
    "name": "vm1PacketCapture",
    "id": "/subscriptions/{guid}/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/networkWatchers/{networkWatcherName}/packetCaptures/{packetCaptureName}",
    "captureStartTime" : "9/7/2016 12:35:24PM",
    "packetCaptureStatus" : "Stopped",
    "stopReason" : "TimeExceeded",
    "packetCaptureError" : [ ]
}
```

## <a name="start-packet-capture"></a>Paket yakalamayı başlat

Aşağıdaki örnek, sanal bir makinede paket yakalama oluşturur.  Örnek, örnek oluştururken esneklik sağlamak için parametreli.

```powershell
$subscriptionId = '<subscription id>'
$resourceGroupName = "NetworkWatcherRG"
$networkWatcherName = "NetworkWatcher_westcentralus"
$packetCaptureName = "TestPacketCapture5"
$storageaccountname = "contosoexamplergdiag374"
$vmName = "ContosoVM"
$bytestoCaptureperPacket = "0"
$bytesPerSession = "1073741824"
$captureTimeinSeconds = "60"
$localIP = ""
$localPort = "" # Examples are: 80, or 80-120
$remoteIP = ""
$remotePort = "" # Examples are: 80, or 80-120
$protocol = "" # Valid values are TCP, UDP and Any.
$targetUri = "" # Example: /subscriptions/$subscriptionId/resourceGroups/$resourceGroupName/providers/Microsoft.compute/virtualMachine/$vmName
$storageId = "" #Example "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/ContosoExampleRG/providers/Microsoft.Storage/storageAccounts/contosoexamplergdiag374"
$storagePath = "" # Example: "https://mytestaccountname.blob.core.windows.net/capture/vm1Capture.cap"
$localFilePath = "c:\\temp\\packetcapture.cap" # Example: "d:\capture\vm1Capture.cap"

$requestBody = @"
{
    'properties':  {
                       'target':  '/${targetUri}',
                       'bytesToCapturePerPacket':  '${bytestoCaptureperPacket}',
                       'totalBytesPerSession':  '${bytesPerSession}',
                       'timeLimitinSeconds':  '${captureTimeinSeconds}',
                       'storageLocation':  {
                                               'storageId':  '${storageId}',
                                               'storagePath':  '${storagePath}',
                                               'filePath':  '${localFilePath}'
                                           },
                       'filters':  [
                                       {
                                           'protocol':  '${protocol}',
                                           'localIPAddress':  '${localIP}',
                                           'localPort':  '${localPort}',
                                           'remoteIPAddress':  '${remoteIP}',
                                           'remotePort':  '${remotePort}'
                                       }
                                   ]
                   }
}
"@

armclient PUT "https://management.azure.com/subscriptions/${subscriptionId}/ResourceGroups/${resourceGroupName}/providers/Microsoft.Network/networkWatchers/${networkWatcherName}/packetCaptures/${packetCaptureName}?api-version=2016-07-01" $requestbody
```

## <a name="stop-packet-capture"></a>Paket yakalamayı durdurma

Aşağıdaki örnek, sanal bir makinede paket yakalamayı durdurur.  Örnek, örnek oluştururken esneklik sağlamak için parametreli.

```powershell
$subscriptionId = '<subscription id>'
$resourceGroupName = "NetworkWatcherRG"
$networkWatcherName = "NetworkWatcher_westcentralus"
$packetCaptureName = "TestPacketCapture5"
armclient post "https://management.azure.com/subscriptions/${subscriptionId}/ResourceGroups/${resourceGroupName}/providers/Microsoft.Network/networkWatchers/${networkWatcherName}/packetCaptures/${packetCaptureName}/stop?api-version=2016-12-01"
```

## <a name="delete-packet-capture"></a>Paket yakalamayı silme

Aşağıdaki örnek, sanal bir makinedeki paket yakalamayı siler.  Örnek, örnek oluştururken esneklik sağlamak için parametreli.

```powershell
$subscriptionId = '<subscription id>'
$resourceGroupName = "NetworkWatcherRG"
$networkWatcherName = "NetworkWatcher_westcentralus"
$packetCaptureName = "TestPacketCapture5"

armclient delete "https://management.azure.com/subscriptions/${subscriptionId}/ResourceGroups/${resourceGroupName}/providers/Microsoft.Network/networkWatchers/${networkWatcherName}/packetCaptures/${packetCaptureName}?api-version=2016-12-01"
```

> [!NOTE]
> Paket yakalamayı silmek, depolama hesabındaki dosyayı silmez

## <a name="next-steps"></a>Sonraki adımlar

Azure depolama hesaplarından dosya indirme yle ilgili talimatlar için [,.NET'i kullanarak Azure Blob depolama alanına başlayın'a](../storage/blobs/storage-dotnet-how-to-use-blobs.md)bakın. Kullanılabilecek bir diğer araç da Depolama Gezgini'dir. Depolama Gezgini hakkında daha fazla bilgiyi aşağıdaki bağlantıda bulabilirsiniz: [Depolama Gezgini](https://storageexplorer.com/)

[Uyarı tetiklenen paket yakalamayı](network-watcher-alert-triggered-packet-capture.md) görüntüleyerek Sanal makine uyarılarıyla paket yakalamaları nasıl otomatikleştirin













