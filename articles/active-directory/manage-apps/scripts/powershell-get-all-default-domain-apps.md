---
title: PowerShell örneği - Varsayılan etki alanını kullanarak Uygulama Proxy uygulamaları
description: Varsayılan etki alanlarını (.msappproxy.net) kullanan tüm Azure Etkin Dizin (Azure AD) Uygulama Proxy uygulamalarını listeleyen PowerShell örneği.
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 12/05/2019
ms.author: mimart
ms.reviewer: japere
ms.collection: M365-identity-device-management
ms.openlocfilehash: 40a9f7ce86981d6f03b25237a7a73869fd5fd4ba
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75483381"
---
# <a name="get-all-application-proxy-apps-using-default-domains-msappproxynet"></a>Varsayılan etki alanlarını kullanarak tüm Uygulama Proxy uygulamalarını edinin (.msappproxy.net)

Bu PowerShell komut dosyası örneği, varsayılan etki alanlarını (.msappproxy.net) kullanan tüm Azure Etkin Dizin (Azure AD) Uygulama Proxy uygulamalarını listeler.

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../../includes/cloud-shell-try-it.md)]

Bu örnek [için AzureAD V2 PowerShell grafik modülü modülü](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2?view=azureadps-2.0) (AzureAD) veya [Graph modülü önizleme sürümü için AzureAD V2 PowerShell](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2?view=azureadps-2.0-preview) (AzureADPreview) gerekir.

## <a name="sample-script"></a>Örnek betik

[!code-azurepowershell[main](~/powershell_scripts/application-proxy/get-all-default-domain-apps.ps1 "Get all Application Proxy apps using default domains (.msappproxy.net)")]

## <a name="script-explanation"></a>Betik açıklaması

| Komut | Notlar |
|---|---|
|[Get-AzureADServicePrincipal](https://docs.microsoft.com/powershell/module/azuread/get-azureadserviceprincipal?view=azureadps-2.0) | Bir servis müdürü alır. |
|[Get-AzureADApplication](https://docs.microsoft.com/powershell/module/azuread/get-azureadapplication?view=azureadps-2.0) | Azure AD uygulaması alır. |
|[Al-AzureADApplicationProxyApplication](https://docs.microsoft.com/powershell/module/azuread/get-azureadapplicationproxyapplication?view=azureadps-2.0) | Azure AD'de Uygulama Proxy için yapılandırılan bir uygulamayı alır. |

## <a name="next-steps"></a>Sonraki adımlar

Azure AD PowerShell modülü hakkında daha fazla bilgi için [Azure AD PowerShell modülüne genel bakış](https://docs.microsoft.com/powershell/azure/active-directory/overview?view=azureadps-2.0)bilgisine bakın.

Uygulama Proxy'si için diğer PowerShell örnekleri için Azure [AD Application Proxy için Azure AD PowerShell örneklerine](../application-proxy-powershell-samples.md)bakın.