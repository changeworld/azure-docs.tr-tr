---
title: Self servis uygulama erişimi nasıl kullanılır | Microsoft Dokümanlar
description: Kullanıcıların kendi uygulamalarını bulmalarını sağlamak için self servis uygulama erişimini etkinleştirin
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: mimart
ms.reviewer: japere,asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: 55da8731855c8afda496edff33f3fbb7982cd44b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/27/2020
ms.locfileid: "65784548"
---
# <a name="how-to-use-self-service-application-access"></a>Self servis uygulama erişimi nasıl kullanılır?

Kullanıcılarınız uygulamaları erişim panelinden kendi kendine keşfedebilmeniz için, kullanıcıların kendi kendine keşfetmesine ve erişimini istemesine izin vermek istediğiniz uygulamalara **Self servis uygulama erişimini** etkinleştirmeniz gerekir.

Bu özellik, BT grubu olarak zamandan ve paradan tasarruf etmenizi sağlamak için harika bir yoldur ve Azure Active Directory ile modern uygulama dağıtımının bir parçası olarak önerilir.

Bu özelliği kullanarak şunları yapabilirsiniz:

-   Kullanıcıların BT grubunu rahatsız etmeden [Uygulama Erişim Paneli'ndeki](https://myapps.microsoft.com/) uygulamaları kendi kendine keşfetmelerine izin verin.

-   Bu kullanıcıları önceden yapılandırılmış bir gruba ekleyin, böylece kimlerin erişim istediğini, erişimi kaldırdığını ve kendilerine atanan rolleri yönetebileceğini görebilirsiniz.

-   İsteğe bağlı olarak, BT grubunun onaylamasıgerekmemesi için bir iş onaylayıcısının uygulama erişim isteklerini onaylamasına izin verin.

-   İsteğe bağlı olarak, bu uygulamaya erişimi onaylayacak en fazla 10 kişiyi yapılandırın.

-   İsteğe bağlı olarak, bir işletme onaylayıcısının, iş onaylayıcısının Uygulama Erişim Paneli'nden, [Application Access Panel](https://myapps.microsoft.com/)bu kullanıcıların uygulamada oturum açmada kullanabileceği parolaları ayarlamasına izin verin.

-   İsteğe bağlı olarak otomatik olarak atanan kullanıcıları doğrudan bir uygulama rolüne atayın.

## <a name="enable-self-service-application-access-to-allow-users-to-find-their-own-applications"></a>Kullanıcıların kendi uygulamalarını bulmalarını sağlamak için self servis uygulama erişimini etkinleştirin

Self servis uygulama erişimi, kullanıcıların uygulamaları kendi kendine keşfetmelerine izin vermek ve isteğe bağlı olarak iş grubunun bu uygulamalara erişimi onaylamasına izin vermek için harika bir yoldur. İş grubunun, bu kullanıcılara Parola Tek Oturum Açma Uygulamaları için atanan kimlik bilgilerini erişim panellerinden yönetmesine izin verebilirsiniz.

Bir uygulamaya self servis uygulama erişimini etkinleştirmek için aşağıdaki adımları izleyin:

1. Azure [**Portalını**](https://portal.azure.com/) açın ve Global Administrator olarak oturum **açın.**

2. Tüm **hizmetleri** sol ana gezinme menüsünün üst kısmındatıklayarak **Azure Etkin Dizin Uzantısı'nı** açın.

3. Filtre arama kutusuna **"Azure Etkin Dizini"** yazın ve **Azure Etkin Dizin** öğesini seçin.

4. Azure Active Directory sol el gezinti menüsünden **Kurumsal Uygulamalar'ı** tıklatın.

5. **tüm uygulamalarınızın** listesini görüntülemek için Tüm Uygulamalar'ı tıklatın.

   * Burada gösterilmesini istediğiniz uygulamayı göremiyorsanız, **Tüm Uygulamalar Listesi'nin** en üstündeki **Filtre** denetimini kullanın ve Tüm Uygulamalar için **Göster** seçeneğini **ayarlayın.**

6. Listeden Self servis erişimini etkinleştirmek istediğiniz uygulamayı seçin.

7. Uygulama yüklendikten sonra, uygulamanın sol daki gezinme menüsünden **Self servis'i** tıklatın.

8. Bu uygulama için Self servis uygulama erişimini etkinleştirmek için, kullanıcıların bu uygulamaya **Yes.** **erişim isteğinde bulunmalarına izin ver'i açın?**

9. Ardından, bu uygulamaya erişim isteyen kullanıcıların eklenmesi gereken grubu seçmek için, **hangi gruba atanmalı etiketin** yanındaki seçiciyi tıklatın?

10. **İsteğe bağlı:** Kullanıcılara erişim izni verilmeden önce bir işletme onayı almak istiyorsanız, bu uygulamaya erişim izni vermeden önce **Onay** **İste'yi ayarlayın?**

11. **İsteğe bağlı: Yalnızca parola tek oturum açma kullanan uygulamalarda,** bu iş onaylayıcılarının onaylı kullanıcılar için bu uygulamaya gönderilen parolaları belirtmesine izin vermek istiyorsanız, **onaylayıcıları bu uygulama için kullanıcı parolalarını ayarlamak için** izin verin'i ayarlayın? **Yes**

12. **İsteğe bağlı:** Bu uygulamaya erişimi onaylamasına izin verilen iş onaylayıcılarını belirtmek için, **10** tek tek iş onaylayıcısına kadar seçim yapmak için bu uygulamaya erişimi kim onaylamasına izin verilen etiketin yanındaki seçiciyi tıklatın.

    * Gruplar desteklenmez.

13. **İsteğe bağlı:** **Rolleri ortaya çıkaran uygulamalarda**, bir role self servis onaylı kullanıcıları atamak istiyorsanız, bu uygulamada kullanıcıların hangi role atanması gerektiğinin yanındaki seçiciyi **tıklatın?**

14. Bitirmek için bıçağın üst kısmındaki **Kaydet** düğmesini tıklatın.

Self servis uygulama yapılandırmasını tamamladıktan sonra, kullanıcılar [Uygulama Erişim Paneli'ne](https://myapps.microsoft.com/) gidebilir ve Self servis erişimi etkinleştirdiğiniz uygulamaları bulmak için **+Ekle** düğmesini tıklatabilir. İş onaylayanlar da [Uygulama Erişim Paneli'nde](https://myapps.microsoft.com/)bir bildirim görürler. Bir kullanıcı onayını gerektiren bir uygulamaya erişim istediğinde, bir e-postayı bunları bildiren bir e-postayı etkinleştirebilirsiniz. 

Bu onaylar yalnızca tek onay iş akışlarını destekler, yani birden çok onaylayıcı belirtirseniz, herhangi bir tek onaylayıcı uygulamaya erişimi onaylayabilir.

## <a name="next-steps"></a>Sonraki adımlar
[Self servis grup yönetimi için Azure Active Directory'yi ayarlama](../users-groups-roles/groups-self-service-management.md)
