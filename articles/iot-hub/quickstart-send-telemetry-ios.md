---
title: Azure IoT Hub’a telemetri gönderme hızlı başlangıç kılavuzu | Microsoft Docs
description: Bu hızlı başlangıçta bir IoT hub’a sanal telemetri göndermek ve bulutta işlemek üzere IoT hub’dan telemetri okumak amacıyla örnek bir iOS uygulaması çalıştıracaksınız.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.topic: quickstart
ms.custom: mvc
ms.date: 04/03/2019
ms.openlocfilehash: 3bb51db139dbdafef63c0c2da71a1ca4ce582338
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/26/2020
ms.locfileid: "78675395"
---
# <a name="quickstart-send-telemetry-from-a-device-to-an-iot-hub-ios"></a>Hızlı Başlangıç: Bir cihazdan IoT hub’a telemetri gönderme (iOS)

[!INCLUDE [iot-hub-quickstarts-1-selector](../../includes/iot-hub-quickstarts-1-selector.md)]

IoT Hub, IoT cihazlarınızdan buluta depolama veya işleme amacıyla yüksek hacimlerde telemetri almanızı sağlayan bir Azure hizmetidir. Bu makalede, bir simülasyon cihazı uygulamasından IoT Hub’a telemetri göndereceksiniz. Daha sonra, bir arka uç uygulamasından verileri görüntüleyebilirsiniz.

Bu makalede, telemetri göndermek için önceden yazılmış bir Swift uygulaması ve IoT Hub’dan telemetri okumak için bir CLI yardımcı programı kullanılır.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Azure aboneliğiniz yoksa, başlamadan önce [ücretsiz](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) bir hesap oluşturun.

## <a name="prerequisites"></a>Ön koşullar

- [Azure örneklerinden](https://github.com/Azure-Samples/azure-iot-samples-ios/archive/master.zip) kod örneğini indirin

- En son iOS SDK sürümünü çalıştıran en yeni [XCode](https://developer.apple.com/xcode/) sürümü. Bu hızlı başlangıç XCode 10.2 ve iOS 12.2 ile test edildi.

- [CocoaPods](https://guides.cocoapods.org/using/getting-started.html)'un en son sürümü.

- 8883 bağlantı noktasının güvenlik duvarınızda açık olduğundan emin olun. Bu hızlı başlatmadaki aygıt örneği, bağlantı noktası 8883 üzerinden iletişim sağlayan MQTT protokolünü kullanır. Bu bağlantı noktası, bazı kurumsal ve eğitim ağı ortamlarında engellenebilir. Daha fazla bilgi ve bu sorunu çözmenin yolları için [IoT Hub'ına Bağlanma (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub)konusuna bakın.


- Bulut Kabuğu örneğinize Azure CLI için Microsoft Azure IoT Uzantısı'nı eklemek için aşağıdaki komutu çalıştırın. IoT Uzantı, Azure CLI'ye IoT Hub, IoT Edge ve IoT Aygıt Sağlama Hizmeti (DPS) özel komutlarını ekler.

   ```azurecli-interactive
   az extension add --name azure-iot
   ```

   [!INCLUDE [iot-hub-cli-version-info](../../includes/iot-hub-cli-version-info.md)]

## <a name="create-an-iot-hub"></a>IoT hub oluşturma

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-device"></a>Cihaz kaydetme

Bir cihazın bağlanabilmesi için IoT hub’ınıza kaydedilmesi gerekir. Bu hızlı başlangıçta Azure Cloud Shell kullanarak bir simülasyon cihazı kaydedeceksiniz.

1. Aygıt kimliğini oluşturmak için Azure Cloud Shell'de aşağıdaki komutu çalıştırın.

   **YourIoTHubName**: Aşağıdaki yer tutucuyu IoT hub'ınız için seçtiğiniz adla değiştirin.

   **myiOSdevice**: Kayıt yaptığınız cihazın adıdır. Gösterildiği gibi **myiOSdevice** kullanılması önerilir. Aygıtınız için farklı bir ad seçerseniz, bu makale boyunca bu adı kullanmanız ve bunları çalıştırmadan önce örnek uygulamalardaki aygıt adını güncelleştirmeniz gerekir.

   ```azurecli-interactive
   az iot hub device-identity create --hub-name {YourIoTHubName} --device-id myiOSdevice
   ```

1. Kaydolduğunuz aygıtın _aygıt bağlantı dizesini_ almak için Azure Cloud Shell'de aşağıdaki komutu çalıştırın:

   **YourIoTHubName**: Aşağıdaki yer tutucuyu IoT hub'ınız için seçtiğiniz adla değiştirin.

   ```azurecli-interactive
   az iot hub device-identity show-connection-string --hub-name {YourIoTHubName} --device-id myiOSdevice --output table
   ```

   Şu ifadeye benzer şekilde görünen cihaz bağlantı dizesini not edin:

   `HostName={YourIoTHubName}.azure-devices.net;DeviceId=myiOSdevice;SharedAccessKey={YourSharedAccessKey}`

    Bu değeri daha sonra hızlı bir şekilde kullanırsınız.

## <a name="send-simulated-telemetry"></a>Sanal telemetri gönderme

Örnek uygulama, IoT hub’ınız üzerindeki cihaza özgü bir uç noktaya bağlanan ve sanal sıcaklık ile nem telemetrisini gönderen bir iOS cihazı üzerinde çalışır. 

### <a name="install-cocoapods"></a>CocoaPods yükleme

CocoaPods, üçüncü taraf kitaplıklar kullanan iOS projeleri için bağımlılıkları yönetir.

Yerel terminal penceresinde, önkoşulların bir parçası olarak indirdiğiniz Azure-IoT-Samples-iOS klasörüne gidin. Ardından örnek projeye gidin:

```sh
cd quickstart/sample-device
```

XCode’un kapalı olduğundan emin olun ve **podfile** dosyasında bildirilen CocoaPods’u yüklemek üzere aşağıdaki komutu çalıştırın:

```sh
pod install
```

Yükleme komutu, projeniz için gereken podları yüklemeye ek olarak bağımlılıklar için podları kullanacak şekilde önceden yapılandırılmış bir XCode çalışma alanı dosyası da oluşturmuştur. 

### <a name="run-the-sample-application"></a>Örnek uygulamayı çalıştırın 

1. Örnek çalışma alanını XCode'da açın.

   ```sh
   open "MQTT Client Sample.xcworkspace"
   ```

2. **MQTT İstemci Örneği** projesini genişletin ve sonra aynı ada sahip klasörü genişletin.  
3. XCode’da düzenlemek üzere **ViewController.swift** dosyasını açın. 
4. **ConnectionString** değişkenini arayın ve daha önce not aldığınız aygıt bağlantı dizesi ile değeri güncelleştirin.
5. Yaptığınız değişiklikleri kaydedin. 
6. **Derle ve çalıştır** düğmesi ya da **command + r** tuş birleşimi ile projeyi cihaz öykünücüsünde çalıştırın. 

   ![Projeyi çalıştırma](media/quickstart-send-telemetry-ios/run-sample.png)

7. Öykünücü açıldığında örnek uygulamada **Başlat**’ı seçin.

Aşağıdaki ekran görüntüsünde, uygulama IoT hub’ınıza sanal telemetri gönderdiğinde oluşan bazı örnek çıktılar gösterilmiştir:

   ![Simülasyon cihazını çalıştırma](media/quickstart-send-telemetry-ios/view-d2c.png)

## <a name="read-the-telemetry-from-your-hub"></a>Hub’ınızdan telemetri okuma

XCode öykünücüsü üzerinde çalıştığınız örnek uygulama, cihazdan gönderilen iletilere ilişkin verileri gösterir. Alınan verileri IoT hub’ınız aracılığıyla da görüntüleyebilirsiniz. IoT Hub uzantısı IoT Hub’ınızdaki bir hizmet tarafı **Olaylar** uç noktasına bağlanabilir. Uzantı, simülasyon cihazınızdan gönderilen cihazdan buluta iletileri alır. IoT Hub arka uç uygulaması genellikle cihazdan buluta iletileri alıp işlemek için bulutta çalışır.

Aşağıdaki komutları Azure Cloud Shell'de çalıştırın, `YourIoTHubName` yerine IoT hub'ınızın adını yazın:

```azurecli-interactive
az iot hub monitor-events --device-id myiOSdevice --hub-name {YourIoTHubName}
```

Aşağıdaki ekran görüntüsünde uzantı, simülasyon cihazı tarafından hub’a gönderilen telemetriyi aldığında oluşan çıktı gösterilmektedir:

Aşağıdaki ekran görüntüsünde, yerel terminal pencerenizde gördüğünüz telemetri türü gösterilmiştir:

![Telemetri görüntüleme](media/quickstart-send-telemetry-ios/view-telemetry.png)

## <a name="clean-up-resources"></a>Kaynakları temizleme

[!INCLUDE [iot-hub-quickstarts-clean-up-resources](../../includes/iot-hub-quickstarts-clean-up-resources.md)]

## <a name="next-steps"></a>Sonraki adımlar

Bu hızlı başlangıçta, bir IoT hub'ı ayarlarsınız, bir aygıtı kaydettirmişsiniz, bir iOS aygıtından hub'a simüle edilmiş telemetri gönderdiniz ve telemetriyi hub'dan okudunuz. 

Bir arka uç uygulamasından simülasyon cihazınızı denetlemeyi öğrenmek için sonraki hızlı başlangıçla devam edin.

> [!div class="nextstepaction"]
> [Hızlı Başlangıç: IoT hub’a bağlı bir cihazı denetleme](quickstart-control-device-node.md)
