---
title: IoT çözüm hızlandırıcılarına giriş - Azure | Microsoft Dokümanlar
description: Azure IoT çözüm hızlandırıcıları hakkında bilgi edinin. IoT çözüm hızlandırıcıları, IoT çözümlerini dağıtmak için kullanılan tam kapsamlı, uçtan uca ve dağıtıma hazır sistemlerdir.
author: dominicbetts
ms.author: dobett
ms.date: 03/09/2019
ms.topic: overview
ms.custom: mvc
ms.service: iot-accelerators
services: iot-accelerators
manager: timlt
ms.openlocfilehash: 1a27d748e16f892a748cf18569c13ca3f9ead1dd
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/26/2020
ms.locfileid: "71309508"
---
# <a name="what-are-azure-iot-solution-accelerators"></a>Azure IoT çözüm hızlandırıcıları nedir?

Bulut tabanlı bir IoT çözümü, aygıt bağlantısını, veri işlemeyi ve analizini ve sunusunu yönetmek için genellikle özel kod ve bulut hizmetlerini kullanır.

IoT çözüm hızlandırıcıları sık kullanılan IoT senaryolarını uygulayan tam kapsamlı ve dağıtıma hazır IoT çözümleridir. Senaryolar uzaktan izleme, bağlı fabrika, tahmine dayalı bakım ve cihaz benzetimini içerir. Bir çözüm hızlandırıcısını dağıttığınızda gerekli tüm bulut tabanlı hizmetler ve gerekli uygulama kodları dağıtılır.

Çözüm hızlandırıcıları, kendi IoT çözümlerinizi oluşturmak için başlangıç noktalarıdır. Tüm çözüm hızlandırıcılarının kaynak kodu açık kaynaktır ve GitHub üzerinde mevcuttur. Çözüm hızlandırıcılarını indirmeniz ve gereksinimlerinize göre özelleştirmeniz önerilir.

Sıfırdan özel bir IoT çözümü derlemeden önce çözüm hızlandırıcılarını eğitim aracı olarak kullanabilirsiniz. Çözüm hızlandırıcıları, bulut tabanlı IoT çözümleri için uygulayabileceğiniz kanıtlanmış yöntemler sunar.

Her çözüm hızlandırıcısındaki uygulama kodunda çözüm hızlandırıcısını yönetmenizi sağlayan bir web uygulaması bulunur.

## <a name="supported-iot-scenarios"></a>Desteklenen IoT senaryoları

Şu anda dağıtıma hazır dört çözüm hızlandırıcısı vardır:

### <a name="remote-monitoring"></a>Uzaktan İzleme

Uzaktan [İzleme çözüm hızlandırıcısını](iot-accelerators-remote-monitoring-sample-walkthrough.md) kullanarak uzak aygıtlardan telemetri toplayın ve bunları kontrol edin. Cihazlara örnek olarak müşterilerinizin tesislerindeki soğutma sistemleri veya uzak pompa istasyonlarındaki valfler verilebilir.

Uzaktan izleme panosunu kullanarak bağlı cihazlarınızdan gelen telemetri verilerini görüntüleyebilir, yeni cihazlar sağlayabilir veya bağlı cihazlarınızdaki üretici yazılımını yükseltebilirsiniz:

[![Uzaktan izleme çözüm panosu](./media/about-iot-accelerators/rm-dashboard-inline.png)](./media/about-iot-accelerators/rm-dashboard-expanded.png#lightbox)

### <a name="connected-factory"></a>Bağlı Fabrika

Bağlı [Fabrika çözüm hızlandırıcısını](iot-accelerators-connected-factory-features.md) kullanarak [OPC Birleşik Mimari](https://opcfoundation.org/about/opc-technologies/opc-ua/) arabirimi olan endüstriyel varlıklardan telemetri toplayın ve bunları kontrol edin. Endüstriyel varlıklar arasında bir fabrikanın üretim hattındaki montaj ve test istasyonları olabilir.

Bağlı fabrika panonuzu kullanarak endüstriyel cihazlarınızı izleyebilir ve yönetebilirsiniz:

[![Bağlı fabrika çözüm panosu](./media/about-iot-accelerators/cf-dashboard-inline.png)](./media/about-iot-accelerators/cf-dashboard-expanded.png#lightbox)

### <a name="predictive-maintenance"></a>Tahmine Dayalı Bakım

Uzak bir aygıtın ne zaman arızalandığını tahmin etmek için [Predictive Maintenance çözüm hızlandırıcısını](iot-accelerators-predictive-walkthrough.md) kullanın, böylece aygıt başarısız olmadan önce bakım gerçekleştirebilirsiniz. Bu çözüm hızlandırıcısı, cihazların telemetri verilerini kullanarak arıza tahmini gerçekleştirmek için makine öğrenimi algoritmalarını kullanır. Örnek cihazlar uçak motorları veya asansörler olabilir.

Tahmine dayalı bakım panosunu kullanarak tahmine dayalı bakım analizlerini görüntüleyebilirsiniz:

[![Bağlı fabrika çözüm panosu](./media/about-iot-accelerators/pm-dashboard-inline.png)](./media/about-iot-accelerators/pm-dashboard-expanded.png#lightbox)

### <a name="device-simulation"></a>Cihaz Benzetimi

Gerçekçi telemetri üreten simüle edilmiş cihazları çalıştırmak için [Aygıt Simülasyonu çözüm hızlandırıcısını](iot-accelerators-device-simulation-overview.md) kullanın. Bu çözüm hızlandırıcısını kullanarak diğer çözüm hızlandırıcılarının davranışını veya kendi IoT çözümlerinizi test edebilirsiniz.

Cihaz benzetimi web uygulamasını kullanarak benzetimlerinizi yapılandırabilir ve çalıştırabilirsiniz:

[![Bağlı fabrika çözüm panosu](./media/about-iot-accelerators/ds-dashboard-inline.png)](./media/about-iot-accelerators/ds-dashboard-expanded.png#lightbox)

## <a name="design-principles"></a>Tasarım ilkeleri

Tüm çözüm hızlandırıcıları aynı tasarım ilkelerini ve hedeflerini takip etmektedir. Bu bileşenler şu şekilde tasarlanmıştır:

* **Ölçeklenebilir**: Milyonlarca cihaz bağlamanıza ve yönetmenize izin verir.
* **Genişletilebilir**: Çözümleri ihtiyaçlarınıza göre özelleştirmenizi sağlar.
* **Anlaşılır**: Çözümlerin nasıl çalıştığını ve nasıl uygulandığını kavramanızı sağlar.
* **Modüler**: Hizmetleri alternatifleriyle değiştirmeniz izin verir.
* **Güvenli**: Azure güvenliğini yerleşik bağlantı ve cihaz güvenliği özellikleriyle birleştirir.

## <a name="architectures-and-languages"></a>Mimariler ve diller

Özgün çözüm hızlandırıcıları .NET ile model-görünüm-denetleyici (MVC) mimarisi kullanılarak yazılmıştır. Microsoft, çözüm hızlandırıcıları yeni bir mikro hizmet mimarisiyle güncelleştirmektedir. Aşağıdaki tabloda, çözüm hızlandırıcılarının geçerli durumu ve GitHub deposu bağlantıları gösterilmektedir:

| Çözüm hızlandırıcısı   | Mimari  | Diller     |
| ---------------------- | ------------- | ------------- |
| Uzaktan İzleme      | Mikro hizmetler | [Java](https://github.com/Azure/azure-iot-pcs-remote-monitoring-java) ve [.NET](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet) |
| Tahmine Dayalı Bakım | MVC           | [.NET](https://github.com/Azure/azure-iot-predictive-maintenance)          |
| Bağlı Fabrika      | MVC           | [.NET](https://github.com/Azure/azure-iot-connected-factory)          |
| Cihaz Benzetimi      | Mikro hizmetler | [.NET](https://github.com/Azure/device-simulation-dotnet)          |

Mikro hizmetler mimarisi hakkında daha fazla bilgi edinmek için Azure [IoT başvuru mimarisine giriş bölümüne](https://docs.microsoft.com/azure/architecture/reference-architectures/iot/)bakın.

## <a name="deployment-options"></a>Dağıtım seçenekleri

Çözüm hızlandırıcılarını [Microsoft Azure IoT Çözüm Hızlandırıcıları](https://www.azureiotsolutions.com/Accelerators#) sitesinden veya komut satırını kullanarak dağıtabilirsiniz.

Uzaktan İzleme çözümü hızlandırıcısını aşağıdaki yapılandırmalarla dağıtabilirsiniz:

* **Standart:** Bir üretim dağıtımı geliştirmek için genişletilmiş altyapı dağıtımı. Azure Kapsayıcı Hizmeti, mikro hizmetleri çeşitli Azure sanal makinelerine dağır. Kubernetes mikro hizmetleri tek tek barındıran Docker kapsayıcılarını düzenler.
* **Temel:** Tanıtım için veya bir dağıtımı test etmek için daha düşük maliyetli sürüm. Tüm mikro hizmetler tek bir Azure sanal makinesine dağıtılır.
* **Yerel:** Test ve geliştirme için yerel makineye dağıtma. Bu yaklaşımda mikro hizmetler yerel bir Docker kapsayıcısına dağıtılır ve buluttaki IoT Hub, Azure Cosmos DB ve Azure depolama hizmetlerine bağlanır.

Çözüm hızlandırıcıçalıştırmanın maliyeti, [temel Azure hizmetlerini çalıştırmanın](https://azure.microsoft.com/pricing)birleştirilmiş maliyetidir. Kullanılan Azure hizmetlerinin ayrıntılarını dağıtım seçeneklerinizi belirlerken görebilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar

IoT çözüm hızlandırıcılarından birini denemek için hızlı başlangıçları inceleyin:

* [Uzaktan izleme çözümü deneyin](quickstart-remote-monitoring-deploy.md)
* [Bağlı fabrika çözümünü deneyin](quickstart-connected-factory-deploy.md)
* [Tahmine dayalı bakım çözümünü deneyin](quickstart-predictive-maintenance-deploy.md)
* [Cihaz benzetimi çözümünü deneyin](quickstart-device-simulation-deploy.md)
