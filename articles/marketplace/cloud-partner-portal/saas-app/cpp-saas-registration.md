---
title: Bir SaaS uygulaması - Azure Marketi kaydetme | Microsoft Docs
description: Azure portalını kullanarak bir SaaS uygulamanın nasıl kaydedileceği açıklanmaktadır.
services: Azure, Marketplace, Cloud Partner Portal, Azure portal
documentationcenter: ''
author: v-miclar
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 03/28/2019
ms.author: pbutlerm
ms.openlocfilehash: fbc542ea2ed76d99d551d668b00bad1fb3719a9f
ms.sourcegitcommit: e43ea344c52b3a99235660960c1e747b9d6c990e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/04/2019
ms.locfileid: "59012943"
---
# <a name="register-a-saas-application"></a>Bir SaaS uygulaması kaydetme

Bu makalede, Microsoft kullanan bir SaaS uygulaması kaydedilecek açıklanmaktadır [Azure portalında](https://portal.azure.com/).  Başarılı bir kayıt sonrasında SaaS yerine getirme API'lerine erişmek için kullanabileceğiniz bir Azure Active Directory (Azure AD) güvenlik belirteci alırsınız.  Azure AD hakkında daha fazla bilgi için bkz. [kimlik doğrulaması nedir?](https://docs.microsoft.com/azure/active-directory/develop/authentication-scenarios)


## <a name="service-to-service-authentication-flow"></a>Hizmetten hizmete kimlik doğrulama akışı

Bu API'leri kullanıldığında ve yeni bir müşteri abonelik akışı aşağıdaki diyagramda gösterilmiştir:

![SaaS API'si akışı sunar.](./media/saas-offer-publish-api-flow-v1.png)

Azure, SaaS hizmeti sunan, son kullanıcılar için kimlik doğrulaması tüm kısıtlamalar uygulamaz. Ancak, genellikle SaaS uygulamasını Azure portalı üzerinden kaydolunarak bir Azure AD güvenlik belirteci ile SaaS yerine getirme API'leri ile kimlik doğrulaması gerçekleştirilir. 


## <a name="register-an-azure-ad-secured-app"></a>Bir Azure AD güvenlik altına uygulamayı kaydetme

Azure AD'nin özelliklerini kullanmak isteyen her uygulama önce bir Azure AD kiracısı olarak kaydedilmelidir. Azure AD'ye nerede, bir kullanıcının kimliği doğrulandıktan sonra yanıtların gönderileceği URL URL gibi uygulamanızın ayrıntılarını vererek kayıt işlemini içerir uygulama ve benzeri tanımlayan URI.  Azure portalını kullanarak yeni bir uygulamayı kaydetmek için aşağıdaki adımları gerçekleştirin:

1.  [Azure Portal](https://portal.azure.com/) oturum açın.
2.  Hesabınız birden fazla Azure AD kiracısına erişim sunuyorsa sağ üst köşeden hesabınıza tıklayın ve portal oturumunuzda kullanmak istediğiniz kiracıyı belirleyin.
3.  Sol gezinti bölmesinden **Azure Active Directory** hizmeti ye **uygulama kayıtları**, tıklatıp **yeni uygulama kaydı**.

    ![SaaS AD uygulama kayıtları](./media/saas-offer-app-registration-v1.png)

4.  Uygulama Oluştur sayfasında girin\'s kayıt bilgileri:
    -   **Ad**: Anlamlı uygulama adı girin
    -   **Uygulama türü**: 
        - Bir cihaza yerel olarak yüklenen [istemci uygulamaları](https://docs.microsoft.com/azure/active-directory/develop/active-directory-dev-glossary#client-application) için **Yerel**'i seçin. Bu ayar OAuth ortak [yerel istemcileri](https://docs.microsoft.com/azure/active-directory/develop/active-directory-dev-glossary#native-client) için kullanılır.
        - Seçin **Web uygulaması / API** için [istemci uygulamaları](https://docs.microsoft.com/azure/active-directory/develop/active-directory-dev-glossary#client-application) ve [kaynak/API uygulamaları](https://docs.microsoft.com/azure/active-directory/develop/active-directory-dev-glossary#resource-server) güvenli bir sunucuya yüklenir. Bu ayar, OAuth gizli kullanılır [web istemcileri](https://docs.microsoft.com/azure/active-directory/develop/active-directory-dev-glossary#web-client) ve genel [kullanıcı aracı tabanlı istemciler](https://docs.microsoft.com/azure/active-directory/develop/active-directory-dev-glossary#user-agent-based-client).
        Aynı uygulama gerek bir istemciyi, gerekse kaynağı/API'yi sunabilir.
    -   **Oturum açma URL'si**: Web uygulaması/API uygulamaları için uygulamanızın temel URL'si sağlayın. Örneğin, **http://localhost:31544** yerel makinenizde çalışan bir web uygulaması URL'si olabilir. Kullanıcılar, bir web istemci uygulamasına oturum açmak için bu URL'yi daha sonra kullanmanız gerekir.
    -   **Yeniden yönlendirme URI'si**: Yerel uygulamaları için Azure AD'nin belirteç yanıtlarını döndürmek için kullanılan URI girin. Uygulamanıza özgü bir değer girin, örneğin **http://MyFirstAADApp**.

        ![SaaS AD uygulama kayıtları](./media/saas-offer-app-registration-v1-2.png)

        Web uygulamaları veya yerel uygulamalar için belirli örnekler için bu hızlı başlangıçta kullanıma destekli kullanılabilen ayarlar *Başlarken* bölümünü [Azure AD Geliştirici Kılavuzu](https://docs.microsoft.com/azure/active-directory/develop/active-directory-developers-guide).

5.  Tamamladığınızda **Oluştur**’a tıklayın. Azure AD benzersiz bir atar *uygulama kimliği* uygulamanıza ve\'re uygulamanıza geçen\'s ana kayıt sayfası. Web uygulaması ya da yerel uygulama olmasına bağlı olarak uygulamanıza ek özellikler eklemek için değişik seçenekler sunulur.

>[!Note]
>Varsayılan olarak, yeni kaydettiğiniz uygulamayı, uygulamanız için oturum açmak için aynı kiracıda yalnızca kullanıcılar izin verecek şekilde yapılandırılır.


## <a name="using-the-azure-ad-security-token"></a>Azure AD güvenlik belirteci kullanılarak

Uygulamanızı kaydettikten sonra program aracılığıyla bir Azure AD güvenlik belirtecini talep edebilir.  Yayımcı Bu belirteci kullanmasına ve bu sorunu çözmek için istekte beklenir.  Kullanıcı, Azure SaaS Web sitesine yönlendirilir, çeşitli yerine getirme API'leri kullanırken, belirteç sorgu URL'de parametresidir.  Bu belirteç, yalnızca bir saat boyunca geçerlidir.  URL ayrıca şunları yapmalısınız tarayıcıdan belirteç değeri kullanmadan önce kod çözme.

Bu belirteçleri hakkında daha fazla bilgi için bkz: [Azure Active Directory erişim belirteçleri](https://docs.microsoft.com/azure/active-directory/develop/access-tokens).


### <a name="get-a-token-based-on-the-azure-ad-app"></a>Azure AD uygulama tabanlı bir belirteç Al

HTTP yöntemi

`GET`

*İstek URL'si*

**https://login.microsoftonline.com/*{Tenantıd}*  /oauth2/belirteç**

*URI parametresi*

|  **Parametre adı**  | **Gerekli**  | **Açıklama**                               |
|  ------------------  | ------------- | --------------------------------------------- |
| Kiracı kimliği             | True          | Kayıtlı uygulama AAD Kiracı kimliği   |
|  |  |  |


*İstek üstbilgisi*

|  **Üst bilgi adı**  | **Gerekli** |  **Açıklama**                                   |
|  --------------   | ------------ |  ------------------------------------------------- |
|  Content-Type     | True         | İstekle ilişkili içerik türü. Varsayılan değer `application/x-www-form-urlencoded` şeklindedir.  |
|  |  |  |


*İstek gövdesi*

| **Özellik adı**   | **Gerekli** |  **Açıklama**                                                          |
| -----------------   | -----------  | ------------------------------------------------------------------------- |
|  Grant_type         | True         | İzin verme türü. Varsayılan değer `client_credentials` şeklindedir.                    |
|  Client_id          | True         |  Azure AD uygulama ile ilişkili istemci/uygulama tanımlayıcısı.                  |
|  client_secret      | True         |  Azure AD uygulama ile ilişkili parola.                               |
|  Kaynak           | True         |  Belirtecin istendiği hedef kaynak. Varsayılan değer `62d94f6c-d599-489b-a797-3e10e42fbe22` şeklindedir. |
|  |  |  |


*Yanıt*

|  **Ad**  | **Tür**       |  **Açıklama**    |
| ---------- | -------------  | ------------------- |
| 200 TAMAM    | TokenResponse  | İstek başarılı   |
|  |  |  |

*TokenResponse*

Örnek yanıt belirteci:

``` json
  {
      "token_type": "Bearer",
      "expires_in": "3600",
      "ext_expires_in": "0",
      "expires_on": "15251…",
      "not_before": "15251…",
      "resource": "62d94f6c-d599-489b-a797-3e10e42fbe22",
      "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6ImlCakwxUmNxemhpeTRmcHhJeGRacW9oTTJZayIsImtpZCI6ImlCakwxUmNxemhpeTRmcHhJeGRacW9oTTJZayJ9…"
  }               
```


## <a name="next-steps"></a>Sonraki adımlar

Azure AD güvenlikli uygulamanız artık kullanabilir [SaaS yerine getirme API sürüm 2](./cpp-saas-fulfillment-api-v2.md).