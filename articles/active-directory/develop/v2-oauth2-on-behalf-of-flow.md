---
title: Microsoft kimlik platformu ve OAuth2.0 On-Behalf-Akış | Azure
description: Bu makalede, OAuth2.0 On-Behalf-Of akışını kullanarak hizmet kimlik doğrulaması için hizmet uygulamak için HTTP iletileri nasıl kullanılacağı açıklanmaktadır.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 1/3/2020
ms.author: hirsin
ms.reviewer: hirsin
ms.custom: aaddev
ms.openlocfilehash: 7a91f61302b5944e69f71c3cfee2f41cd87b809f
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/14/2020
ms.locfileid: "81309369"
---
# <a name="microsoft-identity-platform-and-oauth-20-on-behalf-of-flow"></a>Microsoft kimlik platformu ve OAuth 2.0 On-Behalf-Of akış


OAuth 2.0 On-Behalf-Of akışı (OBO), bir uygulamanın başka bir hizmet/web API'sini çağırması gereken kullanım örneğine hizmet eder. Amaç, devredilen kullanıcı kimliğini ve izinleri istek zinciri aracılığıyla yaymaktır. Orta katman hizmetinin alt akış hizmetine kimlik doğrulama isteği nde bulunabilmesi için, kullanıcı adına Microsoft kimlik platformundan bir erişim belirteci sağlaması gerekir.

Bu makalede, uygulamanızdaki protokole karşı doğrudan programlama nın nasıl yapılacağını açıklanmaktadır.  Mümkün olduğunda, [belirteçleri elde etmek ve güvenli web API'lerini aramak](authentication-flows-app-scenarios.md#scenarios-and-supported-authentication-flows)yerine desteklenen Microsoft Kimlik Doğrulama Kitaplıklarını (MSAL) kullanmanızı öneririz.  Ayrıca [MSAL kullanan örnek uygulamalara](sample-v2-code.md)da göz atın.

> [!NOTE]
>
> - Microsoft kimlik platformu bitiş noktası tüm senaryoları ve özellikleri desteklemez. Microsoft kimlik platformu bitiş noktasını kullanıp kullanmadığınızı belirlemek için [Microsoft kimlik platformu sınırlamaları](active-directory-v2-limitations.md)hakkında bilgi edinin. 
> - Mayıs 2018 itibariyle, bazı örtük akış obo `id_token` akışı için kullanılamaz. Tek sayfalı uygulamalar (SPA'lar), OBO akışlarını gerçekleştirmek için bir **erişim** jetonundan orta katmangizli istemciye geçmelidir. Hangi istemcilerin OBO aramalarını gerçekleştirebileceği hakkında daha fazla bilgi için [sınırlamalar](#client-limitations)bakın.

## <a name="protocol-diagram"></a>Protokol diyagramı

Kullanıcının [OAuth 2.0 yetkilendirme kodu hibe akışı](v2-oauth2-auth-code-flow.md) veya başka bir giriş akışı kullanarak bir uygulamada kimlik doğrulaması olduğunu varsayalım. Bu noktada, uygulama, kullanıcının talepleri ve orta katman web API 'sine (API A) erişim izni içeren *API A* (belirteç A) için bir erişim belirteci vardır. Şimdi, API A downstream web API (API B) için kimlik doğrulaması isteği yapmak gerekir.

İzleyen adımlar OBO akışını oluşturur ve aşağıdaki diyagram yardımıyla açıklanır.

![OAuth2.0 On-Behalf-Of akış gösterir](./media/v2-oauth2-on-behalf-of-flow/protocols-oauth-on-behalf-of-flow.png)

1. İstemci uygulaması, A belirteciyle (API `aud` A talebiyle) API A'ya bir istekte bulunur.
1. API A, Microsoft kimlik platformu belirteç verme bitiş noktasına doğrulanınr ve API B'ye erişmek için bir belirteç ister.
1. Microsoft kimlik platformu belirteç verme bitiş noktası, Belirteç A ile birlikte API A'nın kimlik bilgilerini doğrular ve API B (belirteç B) erişim belirtecinin işeyamasını API A'ya aktarır.
1. Token B, API B'ye isteğin yetkilendirme üstbilgisinde API A tarafından ayarlanır.
1. Güvenli kaynaktan gelen veriler API B tarafından API A'ya ve oradan da istemciye döndürülür.

> [!NOTE]
> Bu senaryoda, orta katman hizmeti, alt akış API'sine erişmek için kullanıcının onayını almak için kullanıcı etkileşimi yoktur. Bu nedenle, alt akış API'sine erişim izni verme seçeneği, kimlik doğrulama sırasında onay adımının bir parçası olarak peşin olarak sunulur. Bunu uygulamanız için nasıl ayarlayabilirsiniz öğrenmek için orta [katman uygulaması için onay alma 'ya](#gaining-consent-for-the-middle-tier-application)bakın.

## <a name="service-to-service-access-token-request"></a>Servisten hizmete erişim jeton isteği

Bir erişim belirteci istemek için, aşağıdaki parametreleri içeren kiracıya özgü Microsoft kimlik platformu belirteç uç noktasına bir HTTP POST gönderin.

```
https://login.microsoftonline.com/<tenant>/oauth2/v2.0/token
```

İstemci uygulamasının paylaşılan bir sır veya sertifika tarafından güvence altına alınıp alınmayacağına bağlı olarak iki durum vardır.

### <a name="first-case-access-token-request-with-a-shared-secret"></a>İlk durum: Paylaşılan bir sırla giriş belirteç isteği

Paylaşılan bir gizli kullanırken, servisten hizmete erişim belirteç isteği aşağıdaki parametreleri içerir:

| Parametre |  | Açıklama |
| --- | --- | --- |
| `grant_type` | Gerekli | Belirteç isteğinin türü. JWT kullanan bir istek için, `urn:ietf:params:oauth:grant-type:jwt-bearer`değer . |
| `client_id` | Gerekli | [Azure portalı - Uygulama kayıtları sayfasının](https://go.microsoft.com/fwlink/?linkid=2083908) uygulamanıza atadığı uygulama (istemci) kimliği. |
| `client_secret` | Gerekli | Azure portalında uygulamanız için oluşturduğunuz istemci sırrı - Uygulama kayıtları sayfası. |
| `assertion` | Gerekli | İstekte kullanılan belirteç değeri.  Bu belirteç, uygulamanın bu OBO isteğini `client-id` (alan tarafından gösterilen uygulama) yapan bir hedef kitleye sahip olmalıdır. |
| `scope` | Gerekli | Belirteç isteği için kapsamların ayrılmış bir boşluk listesi. Daha fazla bilgi için [kapsamlara](v2-permissions-and-consent.md)bakın. |
| `requested_token_use` | Gerekli | İsteğin nasıl işlenmesi gerektiğini belirtir. OBO akışında, değer `on_behalf_of`. |

#### <a name="example"></a>Örnek

Aşağıdaki HTTP POST, web API'sinin `user.read` kapsamıyla https://graph.microsoft.com bir erişim belirteci ve yenileme belirteci talep ediyor.

```
//line breaks for legibility only

POST /oauth2/v2.0/token HTTP/1.1
Host: login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

grant_type=urn:ietf:params:oauth:grant-type:jwt-bearer
&client_id=2846f71b-a7a4-4987-bab3-760035b2f389
&client_secret=BYyVnAt56JpLwUcyo47XODd
&assertion=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsImtpZCI6InowMzl6ZHNGdWl6cEJmQlZLMVRuMjVRSFlPMCJ9.eyJhdWQiOiIyODQ2ZjcxYi1hN2E0LTQ5ODctYmFiMy03NjAwMzViMmYzODkiLCJpc3MiOiJodHRwczovL2xvZ2luLm1pY3Jvc29mdG9ubGluZS5jb20vNzJmOTg4YmYtODZmMS00MWFmLTkxYWItMmQ3Y2QwMTFkYjQ3L3YyLjAiLCJpYXQiOjE0OTM5MjA5MTYsIm5iZiI6MTQ5MzkyMDkxNiwiZXhwIjoxNDkzOTI0ODE2LCJhaW8iOiJBU1FBMi84REFBQUFnZm8vNk9CR0NaaFV2NjJ6MFFYSEZKR0VVYUIwRUlIV3NhcGducndMMnVrPSIsIm5hbWUiOiJOYXZ5YSBDYW51bWFsbGEiLCJvaWQiOiJkNWU5NzljNy0zZDJkLTQyYWYtOGYzMC03MjdkZDRjMmQzODMiLCJwcmVmZXJyZWRfdXNlcm5hbWUiOiJuYWNhbnVtYUBtaWNyb3NvZnQuY29tIiwic3ViIjoiZ1Q5a1FMN2hXRUpUUGg1OWJlX1l5dVZNRDFOTEdiREJFWFRhbEQzU3FZYyIsInRpZCI6IjcyZjk4OGJmLTg2ZjEtNDFhZi05MWFiLTJkN2NkMDExZGI0NyIsInV0aSI6IjN5U3F4UHJweUVPd0ZsTWFFMU1PQUEiLCJ2ZXIiOiIyLjAifQ.TPPJSvpNCSCyUeIiKQoLMixN1-M-Y5U0QxtxVkpepjyoWNG0i49YFAJC6ADdCs5nJXr6f-ozIRuaiPzy29yRUOdSz_8KqG42luCyC1c951HyeDgqUJSz91Ku150D9kP5B9-2R-jgCerD_VVuxXUdkuPFEl3VEADC_1qkGBiIg0AyLLbz7DTMp5DvmbC09DhrQQiouHQGFSk2TPmksqHm3-b3RgeNM1rJmpLThis2ZWBEIPx662pjxL6NJDmV08cPVIcGX4KkFo54Z3rfwiYg4YssiUc4w-w3NJUBQhnzfTl4_Mtq2d7cVlul9uDzras091vFy32tWkrpa970UvdVfQ
&scope=https://graph.microsoft.com/user.read+offline_access
&requested_token_use=on_behalf_of
```

### <a name="second-case-access-token-request-with-a-certificate"></a>İkinci durum: Sertifikayla giriş isteği

Sertifikalı servisten hizmete erişim belirteç isteği aşağıdaki parametreleri içerir:

| Parametre |  | Açıklama |
| --- | --- | --- |
| `grant_type` | Gerekli | Belirteç isteğinin türü. JWT kullanan bir istek için, `urn:ietf:params:oauth:grant-type:jwt-bearer`değer . |
| `client_id` | Gerekli |  [Azure portalı - Uygulama kayıtları sayfasının](https://go.microsoft.com/fwlink/?linkid=2083908) uygulamanıza atadığı uygulama (istemci) kimliği. |
| `client_assertion_type` | Gerekli | Değer. `urn:ietf:params:oauth:client-assertion-type:jwt-bearer` |
| `client_assertion` | Gerekli | Uygulamanız için kimlik bilgileri olarak kaydettiğiniz sertifikayı oluşturmanız ve imzalamanız gereken bir iddia (JSON web belirteci). Sertifikanızı ve belgenin biçimini nasıl kaydedebilirsiniz öğrenmek için [sertifika kimlik bilgilerine](active-directory-certificate-credentials.md)bakın. |
| `assertion` | Gerekli | İstekte kullanılan belirteç değeri. |
| `requested_token_use` | Gerekli | İsteğin nasıl işlenmesi gerektiğini belirtir. OBO akışında, değer `on_behalf_of`. |
| `scope` | Gerekli | Belirteç isteği için alan ayrılmış kapsamların listesi. Daha fazla bilgi için [kapsamlara](v2-permissions-and-consent.md)bakın.|

Parametrenin iki parametre ile değiştirilmesi dışında, parametrenin paylaşılan gizli istek durumunda olduğu gibi `client_assertion_type` `client_assertion`parametrelerin hemen hemen aynı olduğuna dikkat edin: a ve . `client_secret`

#### <a name="example"></a>Örnek

Aşağıdaki HTTP POST, sertifikalı `user.read` web API'sinin kapsamına https://graph.microsoft.com sahip bir erişim belirteci ister.

```
// line breaks for legibility only

POST /oauth2/v2.0/token HTTP/1.1
Host: login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

grant_type=urn%3Aietf%3Aparams%3Aoauth%3Agrant-type%3Ajwt-bearer
&client_id=625391af-c675-43e5-8e44-edd3e30ceb15
&client_assertion_type=urn%3Aietf%3Aparams%3Aoauth%3Aclient-assertion-type%3Ajwt-bearer
&client_assertion=eyJhbGciOiJSUzI1NiIsIng1dCI6Imd4OHRHeXN5amNScUtqRlBuZDdSRnd2d1pJMCJ9.eyJ{a lot of characters here}M8U3bSUKKJDEg
&assertion=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6InowMzl6ZHNGdWl6cEJmQlZLMVRuMjVRSFlPMCIsImtpZCI6InowMzl6ZHNGdWl6cEJmQlZLMVRuMjVRSFlPMCJ9.eyJhdWQiOiJodHRwczovL2Rkb2JhbGlhbm91dGxvb2sub25taWNyb3NvZnQuY29tLzE5MjNmODYyLWU2ZGMtNDFhMy04MWRhLTgwMmJhZTAwYWY2ZCIsImlzcyI6Imh0dHBzOi8vc3RzLndpbmRvd3MubmV0LzI2MDM5Y2NlLTQ4OWQtNDAwMi04MjkzLTViMGM1MTM0ZWFjYi8iLCJpYXQiOjE0OTM0MjMxNTIsIm5iZiI6MTQ5MzQyMzE1MiwiZXhwIjoxNDkzNDY2NjUyLCJhY3IiOiIxIiwiYWlvIjoiWTJaZ1lCRFF2aTlVZEc0LzM0L3dpQndqbjhYeVp4YmR1TFhmVE1QeG8yYlN2elgreHBVQSIsImFtciI6WyJwd2QiXSwiYXBwaWQiOiJiMzE1MDA3OS03YmViLTQxN2YtYTA2YS0zZmRjNzhjMzI1NDUiLCJhcHBpZGFjciI6IjAiLCJlX2V4cCI6MzAyNDAwLCJmYW1pbHlfbmFtZSI6IlRlc3QiLCJnaXZlbl9uYW1lIjoiTmF2eWEiLCJpcGFkZHIiOiIxNjcuMjIwLjEuMTc3IiwibmFtZSI6Ik5hdnlhIFRlc3QiLCJvaWQiOiIxY2Q0YmNhYy1iODA4LTQyM2EtOWUyZi04MjdmYmIxYmI3MzkiLCJwbGF0ZiI6IjMiLCJzY3AiOiJ1c2VyX2ltcGVyc29uYXRpb24iLCJzdWIiOiJEVXpYbkdKMDJIUk0zRW5pbDFxdjZCakxTNUllQy0tQ2ZpbzRxS1MzNEc4IiwidGlkIjoiMjYwMzljY2UtNDg5ZC00MDAyLTgyOTMtNWIwYzUxMzRlYWNiIiwidW5pcXVlX25hbWUiOiJuYXZ5YUBkZG9iYWxpYW5vdXRsb29rLm9ubWljcm9zb2Z0LmNvbSIsInVwbiI6Im5hdnlhQGRkb2JhbGlhbm91dGxvb2sub25taWNyb3NvZnQuY29tIiwidmVyIjoiMS4wIn0.R-Ke-XO7lK0r5uLwxB8g5CrcPAwRln5SccJCfEjU6IUqpqcjWcDzeDdNOySiVPDU_ZU5knJmzRCF8fcjFtPsaA4R7vdIEbDuOur15FXSvE8FvVSjP_49OH6hBYqoSUAslN3FMfbO6Z8YfCIY4tSOB2I6ahQ_x4ZWFWglC3w5mK-_4iX81bqi95eV4RUKefUuHhQDXtWhrSgIEC0YiluMvA4TnaJdLq_tWXIc4_Tq_KfpkvI004ONKgU7EAMEr1wZ4aDcJV2yf22gQ1sCSig6EGSTmmzDuEPsYiyd4NhidRZJP4HiiQh-hePBQsgcSgYGvz9wC6n57ufYKh2wm_Ti3Q
&requested_token_use=on_behalf_of
&scope=https://graph.microsoft.com/user.read+offline_access
```

## <a name="service-to-service-access-token-response"></a>Hizmet erişim jeton yanıtı için hizmet

Bir başarı yanıtı aşağıdaki parametreleri ile bir JSON OAuth 2.0 yanıtıdır.

| Parametre | Açıklama |
| --- | --- |
| `token_type` | Belirteç türü değerini gösterir. Microsoft kimlik platformunun desteklediği `Bearer`tek tür. Taşıyıcı belirteçleri hakkında daha fazla bilgi için [OAuth 2.0 Yetkilendirme Çerçevesi: Taşıyıcı Belirteç Kullanımı (RFC 6750)](https://www.rfc-editor.org/rfc/rfc6750.txt)bakın. |
| `scope` | Belirteçte verilen erişim kapsamı. |
| `expires_in` | Erişim belirtecinin geçerli olduğu saniye cinsinden sürenin uzunluğu. |
| `access_token` | İstenen erişim jetonu. Arama hizmeti, alıcı hizmetin kimliğini doğrulamak için bu belirteci kullanabilir. |
| `refresh_token` | İstenen erişim belirteci için yenileme belirteci. Arama hizmeti, geçerli erişim belirteci sona erdikten sonra başka bir erişim belirteci istemek için bu belirteci kullanabilirsiniz. Yenileme belirteci yalnızca `offline_access` kapsam istendiğinde sağlanır. |

### <a name="success-response-example"></a>Başarı yanıtı örneği

Aşağıdaki örnek, web API'si için erişim https://graph.microsoft.com belirteci isteğine verilen başarı yanıtını gösterir.

```
{
  "token_type": "Bearer",
  "scope": "https://graph.microsoft.com/user.read",
  "expires_in": 3269,
  "ext_expires_in": 0,
  "access_token": "eyJ0eXAiOiJKV1QiLCJub25jZSI6IkFRQUJBQUFBQUFCbmZpRy1tQTZOVGFlN0NkV1c3UWZkQ0NDYy0tY0hGa18wZE50MVEtc2loVzRMd2RwQVZISGpnTVdQZ0tQeVJIaGlDbUN2NkdyMEpmYmRfY1RmMUFxU21TcFJkVXVydVJqX3Nqd0JoN211eHlBQSIsImFsZyI6IlJTMjU2IiwieDV0IjoiejAzOXpkc0Z1aXpwQmZCVksxVG4yNVFIWU8wIiwia2lkIjoiejAzOXpkc0Z1aXpwQmZCVksxVG4yNVFIWU8wIn0.eyJhdWQiOiJodHRwczovL2dyYXBoLm1pY3Jvc29mdC5jb20iLCJpc3MiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC83MmY5ODhiZi04NmYxLTQxYWYtOTFhYi0yZDdjZDAxMWRiNDcvIiwiaWF0IjoxNDkzOTMwMzA1LCJuYmYiOjE0OTM5MzAzMDUsImV4cCI6MTQ5MzkzMzg3NSwiYWNyIjoiMCIsImFpbyI6IkFTUUEyLzhEQUFBQU9KYnFFWlRNTnEyZFcxYXpKN1RZMDlYeDdOT29EMkJEUlRWMXJ3b2ZRc1k9IiwiYW1yIjpbInB3ZCJdLCJhcHBfZGlzcGxheW5hbWUiOiJUb2RvRG90bmV0T2JvIiwiYXBwaWQiOiIyODQ2ZjcxYi1hN2E0LTQ5ODctYmFiMy03NjAwMzViMmYzODkiLCJhcHBpZGFjciI6IjEiLCJmYW1pbHlfbmFtZSI6IkNhbnVtYWxsYSIsImdpdmVuX25hbWUiOiJOYXZ5YSIsImlwYWRkciI6IjE2Ny4yMjAuMC4xOTkiLCJuYW1lIjoiTmF2eWEgQ2FudW1hbGxhIiwib2lkIjoiZDVlOTc5YzctM2QyZC00MmFmLThmMzAtNzI3ZGQ0YzJkMzgzIiwib25wcmVtX3NpZCI6IlMtMS01LTIxLTIxMjc1MjExODQtMTYwNDAxMjkyMC0xODg3OTI3NTI3LTI2MTE4NDg0IiwicGxhdGYiOiIxNCIsInB1aWQiOiIxMDAzM0ZGRkEwNkQxN0M5Iiwic2NwIjoiVXNlci5SZWFkIiwic3ViIjoibWtMMHBiLXlpMXQ1ckRGd2JTZ1JvTWxrZE52b3UzSjNWNm84UFE3alVCRSIsInRpZCI6IjcyZjk4OGJmLTg2ZjEtNDFhZi05MWFiLTJkN2NkMDExZGI0NyIsInVuaXF1ZV9uYW1lIjoibmFjYW51bWFAbWljcm9zb2Z0LmNvbSIsInVwbiI6Im5hY2FudW1hQG1pY3Jvc29mdC5jb20iLCJ1dGkiOiJWR1ItdmtEZlBFQ2M1dWFDaENRSkFBIiwidmVyIjoiMS4wIn0.cubh1L2VtruiiwF8ut1m9uNBmnUJeYx4x0G30F7CqSpzHj1Sv5DCgNZXyUz3pEiz77G8IfOF0_U5A_02k-xzwdYvtJUYGH3bFISzdqymiEGmdfCIRKl9KMeoo2llGv0ScCniIhr2U1yxTIkIpp092xcdaDt-2_2q_ql1Ha_HtjvTV1f9XR3t7_Id9bR5BqwVX5zPO7JMYDVhUZRx08eqZcC-F3wi0xd_5ND_mavMuxe2wrpF-EZviO3yg0QVRr59tE3AoWl8lSGpVc97vvRCnp4WVRk26jJhYXFPsdk4yWqOKZqzr3IFGyD08WizD_vPSrXcCPbZP3XWaoTUKZSNJg",
  "refresh_token": "OAQABAAAAAABnfiG-mA6NTae7CdWW7QfdAALzDWjw6qSn4GUDfxWzJDZ6lk9qRw4AnqPnvFqnzS3GiikHr5wBM1bV1YyjH3nUeIhKhqJWGwqJFRqs2sE_rqUfz7__3J92JDpi6gDdCZNNaXgreQsH89kLCVNYZeN6kGuFGZrjwxp1wS2JYc97E_3reXBxkHrA09K5aR-WsSKCEjf6WI23FhZMTLhk_ZKOe_nWvcvLj13FyvSrTMZV2cmzyCZDqEHtPVLJgSoASuQlD2NXrfmtcmgWfc3uJSrWLIDSn4FEmVDA63X6EikNp9cllH3Gp7Vzapjlnws1NQ1_Ff5QrmBHp_LKEIwfzVKnLLrQXN0EzP8f6AX6fdVTaeKzm7iw6nH0vkPRpUeLc3q_aNsPzqcTOnFfgng7t2CXUsMAGH5wclAyFCAwL_Cds7KnyDLL7kzOS5AVZ3Mqk2tsPlqopAiHijZaJumdTILDudwKYCFAMpUeUwEf9JmyFjl2eIWPmlbwU7cHKWNvuRCOYVqbsTTpJthwh4PvsL5ov5CawH_TaV8omG_tV6RkziHG9urk9yp2PH9gl7Cv9ATa3Vt3PJWUS8LszjRIAJmyw_EhgHBfYCvEZ8U9PYarvgqrtweLcnlO7BfnnXYEC18z_u5wemAzNBFUje2ttpGtRmRic4AzZ708tBHva2ePJWGX6pgQbiWF8esOrvWjfrrlfOvEn1h6YiBW291M022undMdXzum6t1Y1huwxHPHjCAA"
}
```

> [!NOTE]
> Yukarıdaki erişim belirteci v1.0 biçimli bir belirteçtir. Bunun nedeni, belirteç erişilen **kaynağa** dayalı olarak sağlanmaktadır olmasıdır. Microsoft Graph v1.0 belirteçlerini kabul etmek üzere kurulumdur, bu nedenle Microsoft kimlik platformu, istemci Microsoft Graph için belirteç istediğinde v1.0 erişim belirteçleri üretir. Yalnızca uygulamalar erişim belirteçlerine bakmalıdır. İstemciler bunları **incelememelidir.**

### <a name="error-response-example"></a>Hata yanıtı örneği

Alt akış API'de Koşullu Erişim ilkesi (çok faktörlü kimlik doğrulama gibi) ayarlanmışsa, alt akış API'si için bir erişim belirteci elde etmeye çalışırken belirteç bitiş noktası tarafından bir hata yanıtı döndürülür. Orta katman hizmeti, istemci uygulamasının Koşullu Erişim ilkesini karşılamak için kullanıcı etkileşimini sağlayabilmek için bu hatayı istemci uygulamasına sunmalıdır.

```
{
    "error":"interaction_required",
    "error_description":"AADSTS50079: Due to a configuration change made by your administrator, or because you moved to a new location, you must enroll in multi-factor authentication to access 'bf8d80f9-9098-4972-b203-500f535113b1'.\r\nTrace ID: b72a68c3-0926-4b8e-bc35-3150069c2800\r\nCorrelation ID: 73d656cf-54b1-4eb2-b429-26d8165a52d7\r\nTimestamp: 2017-05-01 22:43:20Z",
    "error_codes":[50079],
    "timestamp":"2017-05-01 22:43:20Z",
    "trace_id":"b72a68c3-0926-4b8e-bc35-3150069c2800",
    "correlation_id":"73d656cf-54b1-4eb2-b429-26d8165a52d7",
    "claims":"{\"access_token\":{\"polids\":{\"essential\":true,\"values\":[\"9ab03e19-ed42-4168-b6b7-7001fb3e933a\"]}}}"
}
```

## <a name="use-the-access-token-to-access-the-secured-resource"></a>Güvenli kaynağa erişmek için erişim belirteci'ni kullanma

Artık orta katman hizmeti, üstbilgideki `Authorization` belirteci ayarlayarak, alt akış web API'sine kimlik doğrulama istekleriyapmak için yukarıda edinilen belirteci kullanabilir.

### <a name="example"></a>Örnek

```
GET /v1.0/me HTTP/1.1
Host: graph.microsoft.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJub25jZSI6IkFRQUJBQUFBQUFCbmZpRy1tQTZOVGFlN0NkV1c3UWZkSzdNN0RyNXlvUUdLNmFEc19vdDF3cEQyZjNqRkxiNlVrcm9PcXA2cXBJclAxZVV0QktzMHEza29HN3RzXzJpSkYtQjY1UV8zVGgzSnktUHZsMjkxaFNBQSIsImFsZyI6IlJTMjU2IiwieDV0IjoiejAzOXpkc0Z1aXpwQmZCVksxVG4yNVFIWU8wIiwia2lkIjoiejAzOXpkc0Z1aXpwQmZCVksxVG4yNVFIWU8wIn0.eyJhdWQiOiJodHRwczovL2dyYXBoLm1pY3Jvc29mdC5jb20iLCJpc3MiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC83MmY5ODhiZi04NmYxLTQxYWYtOTFhYi0yZDdjZDAxMWRiNDcvIiwiaWF0IjoxNDkzOTMwMDE2LCJuYmYiOjE0OTM5MzAwMTYsImV4cCI6MTQ5MzkzMzg3NSwiYWNyIjoiMCIsImFpbyI6IkFTUUEyLzhEQUFBQUlzQjN5ZUljNkZ1aEhkd1YxckoxS1dlbzJPckZOUUQwN2FENTVjUVRtems9IiwiYW1yIjpbInB3ZCJdLCJhcHBfZGlzcGxheW5hbWUiOiJUb2RvRG90bmV0T2JvIiwiYXBwaWQiOiIyODQ2ZjcxYi1hN2E0LTQ5ODctYmFiMy03NjAwMzViMmYzODkiLCJhcHBpZGFjciI6IjEiLCJmYW1pbHlfbmFtZSI6IkNhbnVtYWxsYSIsImdpdmVuX25hbWUiOiJOYXZ5YSIsImlwYWRkciI6IjE2Ny4yMjAuMC4xOTkiLCJuYW1lIjoiTmF2eWEgQ2FudW1hbGxhIiwib2lkIjoiZDVlOTc5YzctM2QyZC00MmFmLThmMzAtNzI3ZGQ0YzJkMzgzIiwib25wcmVtX3NpZCI6IlMtMS01LTIxLTIxMjc1MjExODQtMTYwNDAxMjkyMC0xODg3OTI3NTI3LTI2MTE4NDg0IiwicGxhdGYiOiIxNCIsInB1aWQiOiIxMDAzM0ZGRkEwNkQxN0M5Iiwic2NwIjoiVXNlci5SZWFkIiwic3ViIjoibWtMMHBiLXlpMXQ1ckRGd2JTZ1JvTWxrZE52b3UzSjNWNm84UFE3alVCRSIsInRpZCI6IjcyZjk4OGJmLTg2ZjEtNDFhZi05MWFiLTJkN2NkMDExZGI0NyIsInVuaXF1ZV9uYW1lIjoibmFjYW51bWFAbWljcm9zb2Z0LmNvbSIsInVwbiI6Im5hY2FudW1hQG1pY3Jvc29mdC5jb20iLCJ1dGkiOiJzUVlVekYxdUVVS0NQS0dRTVFVRkFBIiwidmVyIjoiMS4wIn0.Hrn__RGi-HMAzYRyCqX3kBGb6OS7z7y49XPVPpwK_7rJ6nik9E4s6PNY4XkIamJYn7tphpmsHdfM9lQ1gqeeFvFGhweIACsNBWhJ9Nx4dvQnGRkqZ17KnF_wf_QLcyOrOWpUxdSD_oPKcPS-Qr5AFkjw0t7GOKLY-Xw3QLJhzeKmYuuOkmMDJDAl0eNDbH0HiCh3g189a176BfyaR0MgK8wrXI_6MTnFSVfBePqklQeLhcr50YTBfWg3Svgl6MuK_g1hOuaO-XpjUxpdv5dZ0SvI47fAuVDdpCE48igCX5VMj4KUVytDIf6T78aIXMkYHGgW3-xAmuSyYH_Fr0yVAQ
```

## <a name="gaining-consent-for-the-middle-tier-application"></a>Orta katman başvurusu için onay alma

Uygulamanızın mimarisine veya kullanımına bağlı olarak, OBO akışının başarılı olmasını sağlamak için farklı stratejiler düşünebilirsiniz. Her durumda nihai amaç, istemci uygulamasının orta katman uygulamasını arayabilmesi için uygun izin verilmesini sağlamak ve orta katman uygulamasının arka uç kaynağını arama iznine sahip olmasını sağlamaktır. 

> [!NOTE]
> Daha önce Microsoft hesap sistemi (kişisel hesaplar) "Bilinen istemci uygulaması" alanını desteklemediği gibi, birleşik onay da gösteremezdi.  Bu eklenmiştir ve Microsoft kimlik platformundaki tüm uygulamalar OBO aramaları için gettign onayı için bilinen istemci uygulama yaklaşımını kullanabilir. 

### <a name="default-and-combined-consent"></a>/.varsayılan ve kombine rıza

Orta katman uygulaması, istemciyi bilinen istemci uygulamaları listesine bildirimine ekler ve ardından istemci hem kendisi hem de orta katman uygulaması için birleşik bir onay akışını tetikleyebilir. Microsoft kimlik platformu bitiş noktasında, bu [ `/.default` kapsam](v2-permissions-and-consent.md#the-default-scope)kullanılarak yapılır. Bilinen istemci uygulamalarını kullanarak bir onay `/.default`ekranı tetiklerken ve onay ekranı **hem** istemcinin izinlerini orta katman API'ye gösterir hem de orta katman API tarafından gerekli olan izinleri ister. Kullanıcı her iki uygulama için de onay sağlar ve sonra OBO akışı çalışır.

### <a name="pre-authorized-applications"></a>Ön yetkili uygulamalar

Kaynaklar, belirli bir uygulamanın her zaman belirli kapsamları alma iznine sahip olduğunu gösterebilir. Bu, öncelikle bir ön uç istemcisi ve bir arka uç kaynağı arasında bağlantıları daha sorunsuz yapmak için yararlıdır. Bir kaynak birden fazla ön yetkili uygulamayı bildirebilir - bu tür bir uygulama bu izinleri obo akışında isteyebilir ve kullanıcının izni olmadan alabilir.

### <a name="admin-consent"></a>Yönetici onayı

Kiracı yönetici, orta katman uygulaması için yönetici onayı sağlayarak uygulamaların gerekli AP'leri arama iznine sahip olduğunu garanti edebilir. Bunu yapmak için yönetici, orta katman uygulamasını kiracısında bulabilir, gerekli izinler sayfasını açabilir ve uygulama için izin vermeyi seçebilir. Yönetici onayı hakkında daha fazla bilgi edinmek [için, onay ve izin belgelerine](v2-permissions-and-consent.md)bakın.

### <a name="use-of-a-single-application"></a>Tek bir uygulamanın kullanımı

Bazı senaryolarda, yalnızca orta katman ve ön uç istemcisinin tek bir eşleşmesi olabilir. Bu senaryoda, orta katman uygulama gereksinimini tamamen ortadan kleyerek bunu tek bir uygulama haline getirmeniz daha kolay olabilir. Ön uç ve web API arasında kimlik doğrulamak için çerezleri, id_token veya uygulamanın kendisi için istenen bir erişim belirteci kullanabilirsiniz. Ardından, bu tek uygulamadan arka uç kaynağına onay isteyin.

## <a name="client-limitations"></a>İstemci sınırlamaları

İstemci id_token almak için örtük akışı kullanıyorsa ve bu istemcide yanıt URL'sinde joker karakterler de varsa, id_token OBO akışı için kullanılamaz.  Ancak, örtülü hibe akışı yoluyla edinilen erişim belirteçleri, başlatan istemcide joker karakter yanıtı URL'si kayıtlı olsa bile gizli bir istemci tarafından yine de kullanılabilir.

## <a name="next-steps"></a>Sonraki adımlar

OAuth 2.0 protokolü ve istemci kimlik bilgilerini kullanarak auth hizmeti için hizmet gerçekleştirmek için başka bir yol hakkında daha fazla bilgi edinin.

* [Microsoft kimlik platformunda OAuth 2.0 istemci kimlik bilgileri hibe](v2-oauth2-client-creds-grant-flow.md)
* [Microsoft kimlik platformunda OAuth 2.0 kod akışı](v2-oauth2-auth-code-flow.md)
* [Kapsamı `/.default` kullanma](v2-permissions-and-consent.md#the-default-scope)
