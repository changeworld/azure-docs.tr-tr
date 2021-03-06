---
title: Özel belirteç önbellek serileştirme (MSAL Python) | Azure
titleSuffix: Microsoft identity platform
description: Python için MSAL için belirteç önbelleğini nasıl serileştireceğinizi öğrenin
services: active-directory
author: rayluo
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 11/13/2019
ms.author: rayluo
ms.reviewer: nacanuma
ms.custom: aaddev
ms.openlocfilehash: 2593cc856afb98cf5186c4e33032c5e9151614f0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76704400"
---
# <a name="custom-token-cache-serialization-in-msal-for-python"></a>Python için MSAL'da özel belirteç önbelleği serileştirme

MSAL Python'da, uygulama oturumu süresince devam eden bir bellek belirteç önbelleği, Istemci [Uygulaması'nın](https://msal-python.readthedocs.io/en/latest/#confidentialclientapplication)bir örneğini oluşturduğunuzda varsayılan olarak sağlanır.

Uygulamanızın farklı oturumlarının erişebilmeleri için belirteç önbelleğinin serileştirilmesi "kutunun dışında" sağlanmaz. Bunun nedeni, MSAL Python'un Web uygulamaları gibi dosya sistemine erişimi olmayan uygulama türlerinde kullanılabilmesidir. Bir MSAL Python uygulamasında kalıcı bir belirteç önbelleğine sahip olmak için özel belirteç önbelleği serileştirme sağlamanız gerekir.

Belirteç önbelleğini seri hale getirme stratejileri, ortak istemci uygulaması (Masaüstü) veya gizli bir istemci uygulaması (Web Uygulaması, Web API veya Daemon uygulaması) yazıp yazmadığınıza bağlı olarak değişir.

## <a name="token-cache-for-a-public-client-application"></a>Genel istemci uygulaması için belirteç önbelleği

Ortak istemci uygulamaları bir kullanıcının aygıtında çalışır ve tek bir kullanıcı için belirteçleri yönetir. Bu durumda, tüm önbelleği bir dosyaya seri leştirebilirsiniz. Uygulamanız veya başka bir uygulama önbelleğe aynı anda erişebiliyorsa dosya kilitleme sağlamayı unutmayın. Kilitleme olmadan bir dosyaya belirteç önbelleğini seri hale getirmek için basit bir örnek için, [SerializableTokenCache](https://msal-python.readthedocs.io/en/latest/#msal.SerializableTokenCache) sınıfı başvuru belgelerindeki örneğe bakın.

## <a name="token-cache-for-a-web-app-confidential-client-application"></a>Bir Web uygulaması için belirteç önbelleği (gizli istemci uygulaması)

Web Uygulamaları veya Web API'leri için, belirteç önbelleğini depolamak için oturumu, Redis önbelleğini veya veritabanını kullanabilirsiniz. Kullanıcı başına (hesap başına) bir belirteç önbelleği olmalıdır, bu nedenle hesap başına belirteç önbelleğini seri hale aldığınızdan emin olun.

## <a name="next-steps"></a>Sonraki adımlar

Windows veya Linux Web uygulaması veya Web API'si için belirteç önbelleğinin nasıl kullanılacağına bir örnek için [ms-identity-python-webapp'a](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/master/app.py#L64-L72) bakın. Örnek, Microsoft Graph API çağıran bir web uygulaması içindir.
