---
title: sfctl'deki bir kümedeki uygulamayı güncelleştirme
description: Service Fabric CLI Betik Örneği - Uygulamayı yeni bir sürümle güncelleştirme. Bu örnek ayrıca dağıtılmış bir uygulamayı yeni bitlerle yükseltir.
services: service-fabric
documentationcenter: ''
author: athinanthny
manager: chackdan
editor: ''
tags: ''
ms.assetid: ''
ms.service: service-fabric
ms.workload: multiple
ms.topic: sample
ms.date: 12/06/2017
ms.author: atsenthi
ms.custom: ''
ms.openlocfilehash: 34f2ae6f3a2ff3adc35794d6e7dfd682640c646b
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/24/2020
ms.locfileid: "75614732"
---
# <a name="update-an-application-using-the-service-fabric-cli"></a>Service Fabric CLI kullanarak uygulamayı güncelleştirme

Bu örnek betik, mevcut bir uygulamanın yeni bir sürümünü karşıya yükler ve sonra dağıtılmış bir uygulamayı yeni bitlerle yükseltir.

[!INCLUDE [links to azure cli and service fabric cli](../../../includes/service-fabric-sfctl.md)]

## <a name="sample-script"></a>Örnek betik

[!code-sh[main](../../../cli_scripts/service-fabric/upgrade-application/upgrade-application.sh "Upload and update an application on a Service Fabric cluster")]

## <a name="next-steps"></a>Sonraki adımlar

Daha fazla bilgi için bkz. [Service Fabric CLI belgeleri](../service-fabric-cli.md).

Azure Service Fabric’e yönelik ek Service Fabric CLI örnekleri [Service Fabric CLI örnekleri](../samples-cli.md)’nde bulunabilir.
