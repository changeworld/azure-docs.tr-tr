---
author: cephalin
ms.service: app-service-web
ms.topic: include
ms.date: 11/03/2016
ms.author: cephalin
ms.openlocfilehash: 87fd6b626efb60c7fc7ec8896f2c3758ae5cc33c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/27/2020
ms.locfileid: "67188178"
---
### <a name="app-service-plan"></a>App Service planı
Web uygulamasını barındırmak için hizmet planını oluşturur. **HostingPlanName** parametresi aracılığıyla planın adını sağlarsınız. Planın konumu, kaynak grubu için kullanılan konumla aynıdır. Fiyatlandırma katmanı ve işçi boyutu **sku** ve **işçiBoyutu** parametrelerinde belirtilir

    {
      "apiVersion": "2015-08-01",
      "name": "[parameters('hostingPlanName')]",
      "type": "Microsoft.Web/serverfarms",
      "location": "[resourceGroup().location]",
      "sku": {
        "name": "[parameters('sku')]",
        "capacity": "[parameters('workerSize')]"
      },
      "properties": {
        "name": "[parameters('hostingPlanName')]"
      }
    },

