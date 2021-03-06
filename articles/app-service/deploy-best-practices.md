---
title: Dağıtım en iyi uygulamaları
description: Azure Uygulama Hizmeti'ne dağıtımın temel mekanizmaları hakkında bilgi edinin. Dile özel önerileri ve diğer uyarılar bulun.
keywords: azure uygulama hizmeti, web uygulaması, dağıtım, dağıtım, boru hatları, inşa
author: jasonfreeberg
ms.assetid: bb51e565-e462-4c60-929a-2ff90121f41d
ms.topic: article
ms.date: 07/31/2019
ms.author: jafreebe
ms.openlocfilehash: 14946a05f021a9b155fd9a9621f73bde980970fa
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75750474"
---
# <a name="deployment-best-practices"></a>Dağıtım En İyi Uygulamalar

Her geliştirme ekibinin, etkin bir dağıtım ardışık hattını tüm bulut hizmetinde zorlaştırmak için benzersiz gereksinimleri vardır. Bu makalede, Uygulama Hizmeti'ne dağıtımın üç ana bileşeni tanıtılır: dağıtım kaynakları, yapı denetim hatları ve dağıtım mekanizmaları. Bu makalede, belirli dil yığınları için bazı en iyi uygulamaları ve ipuçlarını da kapsar.

## <a name="deployment-components"></a>Dağıtım Bileşenleri

### <a name="deployment-source"></a>Dağıtım Kaynağı

Dağıtım kaynağı, uygulama kodunuzu konumudur. Üretim uygulamaları için dağıtım kaynağı genellikle [GitHub, BitBucket veya Azure Repos](deploy-continuous-deployment.md)gibi sürüm kontrol yazılımları tarafından barındırılan bir depodur. Geliştirme ve test senaryoları için dağıtım kaynağı [yerel makinenizde bir proje](deploy-local-git.md)olabilir. Uygulama Hizmeti, [OneDrive ve Dropbox klasörlerini](deploy-content-sync.md) dağıtım kaynağı olarak da destekler. Bulut klasörleri App Service ile başlamak kolay hale getirebilir, ancak genellikle kurumsal düzeyde üretim uygulamaları için bu kaynağı kullanmak için tavsiye edilmez. 

### <a name="build-pipeline"></a>Derleme İşlem Hattı

Bir dağıtım kaynağına karar veredikten sonra, bir sonraki adımınız bir yapı ardışık hattı seçmektir. Yapı ardışık bir yapı hattı, uygulamanın çalıştırılabilir bir durumda olması için kaynak kodunuzu dağıtım kaynağından okur ve bir dizi adımı (kod derleme, HTML ve JavaScript'i çözme, testleri çalıştırma ve paketleme bileşenleri gibi) yürütür. Yapı ardışık hattı tarafından yürütülen belirli komutlar dil yığınınıza bağlıdır. Bu işlemler Azure Ardışık Hatları gibi bir yapı sunucusunda yürütülebilir veya yerel olarak yürütülebilir.

### <a name="deployment-mechanism"></a>Dağıtım Mekanizması

Dağıtım mekanizması, yerleşik uygulamanızı web uygulamanızın */home/site/wwwroot* dizinine koymak için kullanılan eylemdir. */wwwroot* dizini, web uygulamanızın tüm örnekleri tarafından paylaşılan bir depolama konumudur. Dağıtım mekanizması uygulamanızı bu dizine koyduğunda, örnekleriniz yeni dosyaları eşitlemek için bir bildirim alır. Uygulama Hizmeti aşağıdaki dağıtım mekanizmalarını destekler:

- Kudu uç noktaları: [Kudu,](https://github.com/projectkudu/kudu/wiki) Windows App Service'de ayrı bir işlem olarak ve Linux App Service'de ikinci bir kapsayıcı olarak çalışan açık kaynak geliştirici üretkenlik aracıdır. Kudu sürekli dağıtımları işler ve zipdeploy gibi dağıtım için HTTP uç noktaları sağlar.
- FTP ve WebDeploy: [Sitenizi veya kullanıcı kimlik bilgilerinizi](deploy-configure-credentials.md)kullanarak [FTP](deploy-ftp.md) veya WebDeploy üzerinden dosya yükleyebilirsiniz. Bu mekanizmalar Kudu'dan geçmez.  

Azure Pipelines, Jenkins ve düzenleyici eklentileri gibi dağıtım araçları bu dağıtım mekanizmalarından birini kullanır.

## <a name="language-specific-considerations"></a>Dile Özel Hususlar

### <a name="java"></a>Java

JAR uygulamalarını dağıtmak için Kudu [zipdeploy/](deploy-zip.md) API'yi ve WAR uygulamaları için [wardeploy/'ı](deploy-zip.md#deploy-war-file) kullanın. Jenkins kullanıyorsanız, bu API'leri doğrudan dağıtım aşamasında kullanabilirsiniz. Daha fazla bilgi için [bu makaleye](../jenkins/execute-cli-jenkins-pipeline.md)bakın.

### <a name="node"></a>Node

Varsayılan olarak, Kudu Düğüm uygulamanız için yapı`npm install`adımlarını yürütür ( ). Azure DevOps gibi bir yapı hizmeti kullanıyorsanız, Kudu yapısı gereksizdir. Kudu yapıdevre dışı kalmak için, `SCM_DO_BUILD_DURING_DEPLOYMENT`bir uygulama ayarı oluşturmak, bir değer `false`.

### <a name="net"></a>.NET 

Varsayılan olarak, Kudu .Net uygulamanız (`dotnet build`) için yapı adımlarını yürütür. Azure DevOps gibi bir yapı hizmeti kullanıyorsanız, Kudu yapısı gereksizdir. Kudu yapıdevre dışı kalmak için, `SCM_DO_BUILD_DURING_DEPLOYMENT`bir uygulama ayarı oluşturmak, bir değer `false`.

## <a name="other-deployment-considerations"></a>Diğer Dağıtım Hususları

### <a name="use-deployment-slots"></a>Dağıtım yuvalarını kullanma

Mümkün olduğunda, yeni bir üretim yapısı dağıtırken [dağıtım yuvalarını](deploy-staging-slots.md) kullanın. Standart Uygulama Hizmeti Planı katmanını veya daha iyisini kullanırken, uygulamanızı bir hazırlama ortamına dağıtabilir, değişikliklerinizi doğrulayabilir ve duman testleri yapabilirsiniz. Hazır olduğunuzda, evreleme ve üretim yuvaları nızı değiştirebilirsiniz. Takas işlemi, üretim ölçeğinize uyacak şekilde gerekli işçi örneklerini ısıtır ve böylece kesinti süresini ortadan kaldırır. 

### <a name="local-cache"></a>Yerel Önbellek

Azure Uygulama Hizmeti içeriği Azure Depolama'da depolanır ve içerik paylaşımı olarak dayanıklı bir şekilde ortaya çıkar. Ancak, bazı uygulamaların yüksek kullanılabilirlik ile çalıştırabilecekleri yüksek performanslı, salt okunur bir içerik deposuna ihtiyacı vardır. Bu uygulamalar yerel [önbelleği](overview-local-cache.md)kullanarak yararlanabilir. WordPress gibi içerik yönetimi siteleri için yerel önbellek önerilmez.

Kapalı kalma süresini önlemek için her zaman [dağıtım yuvalarıyla](deploy-staging-slots.md) birlikte yerel önbelleği kullanın. Bu özellikleri birlikte kullanma hakkında bilgi için [bu bölüme](overview-local-cache.md#best-practices-for-using-app-service-local-cache) bakın.

### <a name="high-cpu-or-memory"></a>Yüksek CPU veya Bellek

Uygulama Hizmet Planınız kullanılabilir CPU veya belleğin %90'ından fazlasını kullanıyorsa, altta yatan sanal makine dağıtımınızı işleme de sorun yaşayabilir. Bu durumda, dağıtımı gerçekleştirmek için örnek sayınızı geçici olarak ölçeklendirin. Dağıtım tamamlandıktan sonra, örnek sayısını önceki değerine döndürebilirsiniz.

En iyi uygulamalar hakkında daha fazla bilgi için, kaynağınıza özel eyleme geçirilebilir en iyi uygulamaları bulmak için [App Service Diagnostics'i](https://docs.microsoft.com/azure/app-service/overview-diagnostics) ziyaret edin.

- [Azure portalında](https://portal.azure.com)Web Uygulamanıza gidin.
- Uygulama Hizmeti Tanılama'yı açan sol navigasyonda **Tanılama ve sorunları çözme'yi** tıklatın.
- **En İyi Uygulamalar** ana sayfa döşemesini seçin.
- Uygulamanızın bu en iyi uygulamalarla ilgili geçerli durumunu görüntülemek için **Kullanılabilirlik & Performans** için En İyi Uygulamalar'ı veya **Optimal Yapılandırma için En İyi Uygulamalar'ı** tıklatın.

Bu bağlantıyı, kaynağınız için Doğrudan Uygulama Hizmeti Tanılama'yı açmak için de kullanabilirsiniz: `https://ms.portal.azure.com/?websitesextension_ext=asd.featurePath%3Ddetectors%2FParentAvailabilityAndPerformance#@microsoft.onmicrosoft.com/resource/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Web/sites/{siteName}/troubleshoot`.
