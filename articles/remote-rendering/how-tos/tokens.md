---
title: Hizmet erişim jetonları alın
description: ARR REST API'lerine erişmek için belirteçlerin nasıl oluşturulacak larını açıklar
author: florianborn71
ms.author: flborn
ms.date: 02/11/2020
ms.topic: how-to
ms.openlocfilehash: 001a77734a8cedf08c5523380c1cbd00dce89f40
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2020
ms.locfileid: "80681200"
---
# <a name="get-service-access-tokens"></a>Hizmet erişim jetonları alın

ARR REST API'lerine erişim yalnızca yetkili kullanıcılar için verilir. Yetkilendirmenizi kanıtlamak için, REST istekleriyle birlikte bir *erişim jetonu* göndermeniz gerekir. Bu belirteçler, bir hesap anahtarı karşılığında *Güvenli Belirteç Hizmeti* (STS) tarafından verilir. Jetonlar 24 saatlik bir **kullanım ömrüne** sahiptir ve bu nedenle kullanıcılara hizmete tam erişim hakkı verilmeden verilebilir.

Bu makalede, bu tür erişim belirteci oluşturmak için nasıl açıklanmaktadır.

## <a name="prerequisites"></a>Ön koşullar

Henüz hesabınız yoksa [bir ARR hesabı oluşturun.](create-an-account.md)

## <a name="token-service-rest-api"></a>Belirteç hizmeti REST API

Erişim belirteçleri oluşturmak *için, Güvenli Belirteç Hizmeti* tek bir REST API sağlar. ARR STS hizmetinin URL'si [https://sts.mixedreality.azure.com](https://sts.mixedreality.azure.com).

### <a name="get-token-request"></a>'Jeton alın' isteği

| URI | Yöntem |
|-----------|:-----------|
| /accounts/**accountId**/token | GET |

| Üst bilgi | Değer |
|--------|:------|
| Yetkilendirme | "Taşıyıcı **hesap Numarası**:**accountKey**" |

AccountId ve *accountKey'i* ilgili verilerinizle değiştirin. *accountKey*

### <a name="get-token-response"></a>'Belirteç alın' yanıtı

| Durum kodu | JSON yükü | Yorumlar |
|-----------|:-----------|:-----------|
| 200 | AccessToken: string | Başarılı |

| Üst bilgi | Amaç |
|--------|:------|
| MS-CV | Bu değer, hizmet içindeki çağrıyı izlemek için kullanılabilir |

## <a name="getting-a-token-using-powershell"></a>PowerShell kullanarak belirteç alma

Aşağıdaki PowerShell kodu, STS'ye gerekli REST isteğinin nasıl gönderilebildiğini göstermektedir. Ardından powershell isteminin belirteci yazdırır.

```PowerShell
$accountId = "<account_id_from_portal>"
$accountKey = "<account_key_from_portal>"

[System.Net.ServicePointManager]::SecurityProtocol = [System.Net.SecurityProtocolType]::Tls12;
$webResponse = Invoke-WebRequest -Uri "https://sts.mixedreality.azure.com/accounts/$accountId/token" -Method Get -Headers @{ Authorization = "Bearer ${accountId}:$accountKey" }
$response = ConvertFrom-Json -InputObject $webResponse.Content

Write-Output "Token: $($response.AccessToken)"
```

Komut dosyası yalnızca, kopyalayabildiğiniz & yapıştırabileceğiniz çıkış belirteci yazdırır. Gerçek bir proje için bu işlemi otomatikleştirmeniz gerekir.

## <a name="next-steps"></a>Sonraki adımlar

* [Örnek PowerShell komut dosyaları](../samples/powershell-example-scripts.md)
* [Azure Frontend API'leri](../how-tos/frontend-apis.md)
* [Oturum yönetimi REST API](../how-tos/session-rest-api.md)
