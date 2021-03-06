---
title: Öğretici - Azure CDN uç noktanıza özel etki alanı ekleme | Microsoft Docs
description: Bu öğreticide, Azure CDN uç noktası içeriğini özel bir etki alanına eşleyeceksiniz.
services: cdn
documentationcenter: ''
author: asudbring
manager: danielgi
editor: ''
ms.service: azure-cdn
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 06/11/2018
ms.author: allensu
ms.custom: mvc
ms.openlocfilehash: ab4845a93771bace5bca15ea3afa7027884e36f4
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/13/2020
ms.locfileid: "81260590"
---
# <a name="tutorial-add-a-custom-domain-to-your-azure-cdn-endpoint"></a>Öğretici: Azure CDN uç noktanıza özel etki alanı ekleme
Bu öğreticide bir Azure Content Delivery Network (CDN) uç noktasına özel etki alanı ekleme işlemi gösterilmektedir. İçerik ulaştırmak için bir CDN uç noktası kullandığınızda, CDN URL’nizde kendi etki alanı adınızın görünmesini istiyorsanız özel bir etki alanı gereklidir. Görünür bir etki alanınızın olması, müşterileriniz için kolaylık sağlar ve markalama için faydalıdır. 

Profilinizde bir CDN uç noktası oluşturduğunuzda, varsayılan olarak CDN içeriği sunmak için URL’nize azureedge.net adresinin alt etki alanı olan uç nokta adı eklenir (örneğin, https:\//contoso.azureedge.net/photo.png). Size kolaylık olması için Azure CDN, bir CDN uç noktası ile özel etki alanını ilişkilendirme seçeneği sağlar. Bu seçeneği kullanarak URL’nizde bir uç nokta adı yerine özel etki alanı ile içerik sunabilirsiniz (örneğin, https:\//www.contoso.com/photo.png). 

Bu öğreticide şunların nasıl yapıldığını öğrenirsiniz:
> [!div class="checklist"]
> - CNAME DNS kaydı oluşturun.
> - Özel etki alanını CDN uç noktanızla ilişkilendirin.
> - Özel etki alanını doğrulayın.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Ön koşullar

Bu öğreticideki adımları tamamlayabilmeniz için öncelikle bir CDN profili ve en az bir CDN uç noktası oluşturmanız gerekir. Daha fazla bilgi için bkz. [Hızlı Başlangıç: Azure CDN profili ve uç noktası oluşturma](cdn-create-new-endpoint.md).

Henüz özel bir etki alanınız yoksa ilk olarak bir etki alanı sağlayıcısından satın almanız gerekir. Örneğin bkz. [Özel etki alanı adı satın alma](https://docs.microsoft.com/azure/app-service/manage-custom-dns-buy-domain).

[DNS etki alanlarınızı](https://docs.microsoft.com/azure/dns/dns-overview) barındırmak için Azure kullanıyorsanız, etki alanı sağlayıcısının etki alanı adı sistemini (DNS) bir Azure DNS’e devretmeniz gerekir. Daha fazla bilgi için bkz. [Bir etki alanını Azure DNS'ye devretme](https://docs.microsoft.com/azure/dns/dns-delegate-domain-azure-dns). Aksi takdirde, DNS etki alanınızı işlemek için bir etki alanı sağlayıcısı kullanıyorsanız [CNAME DNS kaydı oluşturma](#create-a-cname-dns-record) bölümüne geçin.


## <a name="create-a-cname-dns-record"></a>CNAME DNS kaydı oluşturma

Bir özel etki alanını Azure CDN uç noktasıyla birlikte kullanabilmeniz için öncelikle etki alanı sağlayıcınızla CDN uç noktanızı işaret eden bir kurallı ad (CNAME) kaydı oluşturmanız gerekir. CNAME kaydı, bir kaynak etki alanı adını hedef etki alanı adına eşleyen bir DNS kaydı türüdür. Azure CDN için kaynak etki alanı adı, özel etki alanı adınızdır; hedef etki alanı adı ise CDN uç noktası ana bilgisayar adınızdır. Azure CDN oluşturduğunuz CNAME kaydını doğruladıktan sonra, kaynak özel etki\.alanına (www contoso.com gibi) gönderilen trafik, belirtilen hedef CDN bitiş noktası ana bilgisayar adına (contoso.azureedge.net gibi) yönlendirilir. 

Özel etki alanı ve alt etki alanı aynı anda yalnızca tek bir uç nokta ile ilişkilendirilebilir. Ancak, birden fazla CNAME kaydı kullanarak farklı Azure hizmet uç noktaları için aynı özel etki alanından farklı alt etki alanları kullanabilirsiniz. Farklı alt etki alanlarına sahip özel bir etki alanını aynı CDN uç noktasına da eşleyebilirsiniz.

> [!NOTE]
> Azure DNS'yi etki alanı sağlayıcınız olarak kullanıyorsanız, herhangi bir takma ad kayıt türü Özel etki alanları için kullanılabilir. Bu gözden geçirme, CNAME kayıt türünü kullanır. A veya AAAA kayıt türlerini kullanıyorsanız, aşağıdaki adımları izleyin ve CNAME'yi seçtiğiniz kayıt türüyle değiştirin. Kök etki alanını özel etki alanı olarak eklemek için bir takma ad kaydı kullanıyorsanız ve TLS'yi etkinleştirmek istiyorsanız, [bu makalede](https://docs.microsoft.com/azure/cdn/cdn-custom-ssl?tabs=option-1-default-enable-https-with-a-cdn-managed-certificate#custom-domain-is-not-mapped-to-your-cdn-endpoint)açıklandığı gibi el ile doğrulama kullanmanız gerekir. Daha fazla bilgi için Azure [CDN uç noktalarına nokta bölgesi tepe noktasını](https://docs.microsoft.com/azure/dns/dns-alias#point-zone-apex-to-azure-cdn-endpoints)görün.

## <a name="map-the-temporary-cdnverify-subdomain"></a>Geçici cdnverify alt etki alanını eşleme

Üretim aşamasındaki var olan bir etki alanını eşlerken göz önünde bulundurmanız gereken özel durumlar vardır. Özel etki alanınızı Azure portalına kaydederken etki alanı için kısa bir kapalı kalma süresi yaşanabilir. Web trafiğinin kesintiye uğramaması için, ilk olarak özel etki alanınızı Azure cdnverify alt etki alanı ile CDN uç noktası ana bilgisayar adınıza eşleyerek geçici bir CNAME eşlemesi oluşturun. Bu yöntemle kullanıcılar, DNS eşlemesi gerçekleşirken kesinti olmadan etki alanınıza erişebilir. 

Aksi takdirde, özel etki alanınızı ilk kez kullanıyorsanız ve üzerinde devam eden bir üretim trafiği yoksa, özel etki alanınızı doğrudan CDN uç noktanıza eşleyebilirsiniz. [Kalıcı özel etki alanını eşleme](#map-the-permanent-custom-domain) bölümüne geçin.

cdnverify alt etki alanı ile bir CNAME kaydı oluşturmak için:

1. Özel etki alanınızın etki alanı sağlayıcısına ait web sitesinde oturum açın.

2. Sağlayıcının belgelerine başvurarak veya web sitesinin **Etki Alanı Adı**, **DNS** ya da **Ad sunucusu yönetimi** etiketli alanlarını arayarak DNS kayıtlarını yönetmeye ilişkin sayfayı bulun. 

3. Özel etki alanınız için bir CNAME kaydı girişi oluşturun ve alanları (alan adları değişebilir) aşağıdaki tabloda gösterildiği gibi tamamlayın:

    | Kaynak                    | Tür  | Hedef                     |
    |---------------------------|-------|---------------------------------|
    | cdnverify.www.contoso.com | CNAME | cdnverify.contoso.azureedge.net |

    - Kaynak: Cdnverify alt etki alanı da dahil olmak üzere özel alan adınızı aşağıdaki biçimde girin: cdnverify. &lt;özel alan&gt;adı . Örneğin, cdnverify.www.contoso.com.

    - Tür: *CNAME* yazın.

    - Hedef: CDN verify alt etki alanı da dahil olmak üzere CDN bitiş noktası ana adınızı aşağıdaki biçimde girin: cdnverify. _bitiş noktası&gt;adı .azureedge.net. &lt;_ Örneğin, cdnverify.contoso.azureedge.net.

4. Yaptığınız değişiklikleri kaydedin.

Örneğin, GoDaddy etki alanı kayıt şirketi için yordam şu şekildedir:

1. Oturum açın ve kullanmak istediğiniz özel etki alanını seçin.

2. Etki Alanları bölümünde **Tümünü Yönet** seçeneğini belirleyip **DNS** | **Bölgeleri Yönet**’i seçin.

3. **Etki Alanı Adı** alanına özel etki alanınızı girin, ardından **Ara**’yı seçin.

4. **DNS Yönetimi** sayfasından **Ekle**’yi ve sonra **Tür** listesinden **CNAME** öğesini seçin.

5. CNAME girişi için aşağıdaki alanları doldurun:

    ![CNAME girişi](./media/cdn-map-content-to-custom-domain/cdn-cdnverify-cname-entry.png)

    - Tür: *CNAME* seçeneğini işaretli bırakın.

    - Ana bilgisayar: cdnverify alt etki alanı adı ile birlikte kullanmak istediğiniz özel etki alanınızın alt etki alanını girin. Örneğin, cdnverify.www.

    - Şuraya işaret eder: cdnverify alt etki alanı adı ile birlikte CDN uç noktanızın ana bilgisayar adını girin. Örneğin, cdnverify.contoso.azureedge.net. 

    - TTL: *1 Saat* seçeneğini işaretli bırakın.

6. **Kaydet'i**seçin.
 
    CNAME girişi DNS kayıtları tablosuna eklenir.

    ![DNS kayıtları tablosu](./media/cdn-map-content-to-custom-domain/cdn-cdnverify-dns-table.png)


## <a name="associate-the-custom-domain-with-your-cdn-endpoint"></a>Özel etki alanını CDN uç noktanızla ilişkilendirme

Özel etki alanınızı kaydettikten sonra CDN uç noktanıza ekleyebilirsiniz. 

1. [Azure portalında](https://portal.azure.com/) oturum açın ve bir özel etki alanına eşlemek istediğiniz uç noktayı içeren CDN profiline göz atın.
    
2. **CDN profili** sayfasında, özel etki alanı ile ilişkilendirilecek CDN uç noktasını seçin.

   **Uç Nokta** sayfası açılır.
    
3. **Özel etki alanı**’nı seçin. 

   ![CDN özel etki alanı düğmesi](./media/cdn-map-content-to-custom-domain/cdn-custom-domain-button.png)

   **Özel etki alanı ekleme** sayfası açılır.

4. **Uç nokta ana bilgisayar adı** için, CNAME kaydınızın hedef etki alanı olarak kullanılacak uç nokta ana bilgisayar adı önceden doldurulmuş ve CDN uç noktanızın URL’sinden türetilmiştir: *&lt;uç nokta ana bilgisayar adı&gt;*.azureedge.net. Bu değer değiştirilemez.

5. **Özel ana bilgisayar adı** için, CNAME kaydınızın kaynak etki alanı olarak kullanılacak alt etki alanı dahil özel etki alanınızı girin. Örneğin, www\.contoso.com veya cdn.contoso.com. cdnverify alt etki alanı adını kullanmayın.

   ![CDN özel etki alanı iletişim kutusu](./media/cdn-map-content-to-custom-domain/cdn-add-custom-domain.png)

6. **Ekle'yi**seçin.

   Azure, girdiğiniz özel etki alanı adı için CNAME kaydının bulunduğunu doğrular. CNAME doğruysa, özel etki alanınız doğrulanır. 

   Yeni özel etki alanı ayarlarının tüm CDN kenar düğümlerine yayılması biraz sürebilir: 
    - **Microsoft’tan Azure CDN Standart** profilleri için yayma işlemi genellikle 10 dakikada tamamlanır. 
    - **Akamai’den Azure CDN Standart** profilleri için yayma işlemi genellikle bir dakika içinde tamamlanır. 
    - **Verizon’dan Azure CDN Standart** ve **Verizon’dan Azure CDN Premium** profilleri için yayma işlemi genellikle 10 dakika içinde tamamlanır.   


## <a name="verify-the-custom-domain"></a>Özel etki alanını doğrulama

Özel etki alanınızın kaydını tamamladıktan sonra özel etki alanının CDN uç noktanıza başvurduğunu doğrulayın.
 
1. Uç noktada önbelleğe alınan genel içeriğinizin olduğundan emin olun. Örneğin, CDN uç noktanız bir depolama hesabıyla ilişkiliyse, Azure CDN içeriği genel bir kapsayıcıda önbelleğe alır. Özel etki alanını test etmek için kapsayıcınızın genel erişime izin verecek şekilde ayarlandığını ve en az bir dosya içerdiğini doğrulayın.

2. Tarayıcınızda, özel etki alanını kullanarak dosyanın adresine gidin. Örneğin, özel etki alanınız `www.contoso.com`önbelleğe alınmış dosyanın URL'si aşağıdaki `http://www.contoso.com/my-public-container/my-file.jpg`URL'ye benzer olmalıdır: . Sonucun, CDN bitiş noktasına doğrudan bitiş noktası ana * &lt;bilgisayar adı&gt;*.azureedge.net'da erişmenizle aynı olduğunu doğrulayın.


## <a name="map-the-permanent-custom-domain"></a>Kalıcı özel etki alanını eşleme

cdnverify alt etki alanının uç noktanıza başarıyla eşlendiğini doğruladıysanız (veya üretim aşamasında olmayan yeni bir özel etki alanı kullanıyorsanız), özel etki alanını doğrudan CDN uç noktanızın ana bilgisayar adına eşleyebilirsiniz.

Özel etki alanınıza yönelik bir CNAME kaydı oluşturmak için:

1. Özel etki alanınızın etki alanı sağlayıcısına ait web sitesinde oturum açın.

2. Sağlayıcının belgelerine danışarak veya **Alan Adı,** **DNS**veya **Ad Sunucusu Yönetimi**etiketli web sitesinin alanlarını arayarak DNS kayıtlarını yönetmek için sayfayı bulun. 

3. Özel etki alanınız için bir CNAME kaydı girişi oluşturun ve alanları (alan adları değişebilir) aşağıdaki tabloda gösterildiği gibi tamamlayın:

    | Kaynak          | Tür  | Hedef           |
    |-----------------|-------|-----------------------|
    | <www.contoso.com> | CNAME | contoso.azureedge.net |

   - Kaynak: Özel alan adınızı girin (örneğin, www\.contoso.com).

   - Tür: *CNAME* yazın.

   - Hedef: CDN uç noktanızın ana bilgisayar adını girin. Aşağıdaki biçimde olmalıdır:_&lt;bitiş noktası&gt;adı_.azureedge.net. Örneğin, contoso.azureedge.net.

4. Yaptığınız değişiklikleri kaydedin.

5. Daha önce geçici bir cdnverify alt etki alanı CNAME kaydı oluşturduysanız silin. 

6. Bu özel etki alanını üretimde ilk kez kullanıyorsanız, [Özel etki alanını CDN uç noktanızla ilişkilendirme](#associate-the-custom-domain-with-your-cdn-endpoint) ve [Özel etki alanını doğrulama](#verify-the-custom-domain) adımlarını izleyin.

Örneğin, GoDaddy etki alanı kayıt şirketi için yordam şu şekildedir:

1. Oturum açın ve kullanmak istediğiniz özel etki alanını seçin.

2. Etki Alanları bölümünde **Tümünü Yönet** seçeneğini belirleyip **DNS** | **Bölgeleri Yönet**’i seçin.

3. **Etki Alanı Adı** alanına özel etki alanınızı girin, ardından **Ara**’yı seçin.

4. **DNS Yönetimi** sayfasından **Ekle**’yi ve sonra **Tür** listesinden **CNAME** öğesini seçin.

5. CNAME girişinin alanlarını doldurun:

    ![CNAME girişi](./media/cdn-map-content-to-custom-domain/cdn-cname-entry.png)

    - Tür: *CNAME* seçeneğini işaretli bırakın.

    - Ana bilgisayar: Kullanılacak özel etki alanınızın alt etki alanını girin. Örneğin, www veya cdn.

    - Şuraya işaret eder: CDN uç noktanızın ana bilgisayar adını girin. Örneğin, contoso.azureedge.net. 

    - TTL: *1 Saat* seçeneğini işaretli bırakın.

6. **Kaydet'i**seçin.
 
    CNAME girişi DNS kayıtları tablosuna eklenir.

    ![DNS kayıtları tablosu](./media/cdn-map-content-to-custom-domain/cdn-dns-table.png)

7. Bir cdnverify CNAME kaydınız varsa yanındaki kalem simgesini ve sonra çöp kutusu simgesini seçin.

8. CNAME kaydını silmek için **Sil**’i seçin.


## <a name="clean-up-resources"></a>Kaynakları temizleme

Yukarıdaki adımlarda bir özel etki alanını CDN uç noktasına eklediniz. Uç noktanızı artık özel bir etki alanı ile ilişkilendirmek istemiyorsanız, aşağıdaki adımları uygulayarak özel etki alanını kaldırabilirsiniz:
 
1. CDN profilinizde kaldırmak istediğiniz özel etki alanını içeren uç noktayı seçin.

2. **Uç nokta** sayfasındaki Özel etki alanları altında, kaldırmak istediğiniz özel etki alanına sağ tıklayın ve sonra açılır menüden **Sil**’i seçin.  

   Özel etki alanının uç noktanızla ilişkisi silinir.


## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide, şunların nasıl yapıldığını öğrendiniz:

> [!div class="checklist"]
> - CNAME DNS kaydı oluşturun.
> - Özel etki alanını CDN uç noktanızla ilişkilendirin.
> - Özel etki alanını doğrulayın.

Bir Azure CDN özel etki alanı üzerinde HTTPS yapılandırma hakkında bilgi edinmek için sonraki öğreticiye ilerleyin.

> [!div class="nextstepaction"]
> [Öğretici: Azure CDN özel etki alanı üzerinde HTTPS yapılandırma](cdn-custom-ssl.md)


