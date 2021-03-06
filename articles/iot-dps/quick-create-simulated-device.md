---
title: "Hızlı başlatma: C kullanarak Azure IoT Hub'ına simüle edilmiş bir TPM aygıtı sağlama"
description: Bu hızlı başlangıçta bireysel kayıtlar kullanılmaktadır. Bu hızlı başlangıçta, Azure IoT Hub Aygıt Sağlama Hizmeti (DPS) için C aygıtı SDK'yı kullanarak simüle edilmiş bir TPM aygıtı oluşturur ve sağlarsınız.
author: wesmc7777
ms.author: wesmc
ms.date: 11/08/2019
ms.topic: quickstart
ms.service: iot-dps
services: iot-dps
manager: philmea
ms.custom: mvc
ms.openlocfilehash: d41c4757f0b81312cefa580c3a3263f87bccffa9
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/26/2020
ms.locfileid: "79241706"
---
# <a name="quickstart-provision-a-simulated-tpm-device-using-the-azure-iot-c-sdk"></a>Hızlı başlangıç: Azure IoT C SDK'sını kullanarak simülasyon TPM cihazı sağlama

[!INCLUDE [iot-dps-selector-quick-create-simulated-device-tpm](../../includes/iot-dps-selector-quick-create-simulated-device-tpm.md)]

Bu hızlı başlangıçta, Windows geliştirme makinesi üzerinde Güvenilir Platform Modülü (TPM) cihazı simülatörünü oluşturmayı ve çalıştırmayı öğreneceksiniz. Cihaz Sağlama Hizmeti örneğini kullanarak bu simülasyon cihazını bir IoT hub'ına bağlayacaksınız. Cihazı Cihaz Sağlama Hizmeti örneğine kaydetmek ve cihaz için bir önyükleme sırası simülasyonu yapmak için [Azure IoT C SDK'sındaki](https://github.com/Azure/azure-iot-sdk-c) örnek kodu kullanacaksınız.

Otomatik sağlama işlemini bilmiyorsanız, [Otomatik sağlama kavramlarını](concepts-auto-provisioning.md) gözden geçirin. Ayrıca, bu hızlı başlangıçla devam etmeden önce [IoT Hub Cihazı Sağlama Hizmetini Azure portalla ayarlama](./quick-setup-auto-provision.md) bölümünde bulunan adımları tamamladığınızdan emin olun. 

Azure IoT Cihaz Sağlama Hizmeti iki tür kaydı destekler:
- [Kayıt grupları](concepts-service.md#enrollment-group): Birden fazla ilgili cihazı kaydetmek için kullanılır.
- [Bireysel kayıtlar](concepts-service.md#individual-enrollment): Tek bir cihazı kaydetmek için kullanılır.

Bu makalede bireysel kayıtlar gösterilmektedir.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Ön koşullar

Aşağıdaki ön koşullar Windows geliştirme ortamı içindir. Linux veya macOS için, SDK belgelerinde [geliştirme ortamınızı hazırlayın'daki](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md) uygun bölüme bakın.

* [Visual Studio](https://visualstudio.microsoft.com/vs/) 2019 ile ['C++'](https://docs.microsoft.com/cpp/?view=vs-2019#pivot=workloads) iş yükü ile masaüstü geliştirme özelliğine sahip. Visual Studio 2015 ve Visual Studio 2017 de desteklendi.

* [Git](https://git-scm.com/download/)'in en son sürümünün yüklemesi.

<a id="setupdevbox"></a>

## <a name="prepare-a-development-environment-for-the-azure-iot-c-sdk"></a>Azure IoT C SDK'sı için geliştirme ortamını hazırlama

Bu bölümde, [Azure IoT C SDK'sı](https://github.com/Azure/azure-iot-sdk-c) ile [TPM](https://docs.microsoft.com/windows/device-security/tpm/trusted-platform-module-overview) cihaz simülatör örneğini oluşturmak için kullanılan geliştirme ortamını hazırlayacaksınız.

1. [CMake yapı sistemini](https://cmake.org/download/)indirin.

    `CMake` yüklemesine başlamadan **önce** makinenizde Visual Studio önkoşullarının (Visual Studio ve "C++ ile masaüstü geliştirme" iş yükü) yüklenmiş olması önemlidir. Önkoşullar sağlandıktan ve indirme doğrulandıktan sonra, CMake derleme sistemini yükleyin.

2. SDK'nın [en son sürümü](https://github.com/Azure/azure-iot-sdk-c/releases/latest) için etiket adını bulun.

3. Komut istemini veya Git Bash kabuğunu açın. [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c) GitHub deposunun en son sürümünde klonlamak için aşağıdaki komutları çalıştırın. Önceki adımda bulduğunuz etiketi `-b` parametre nin değeri olarak kullanın:

    ```cmd/sh
    git clone -b <release-tag> https://github.com/Azure/azure-iot-sdk-c.git
    cd azure-iot-sdk-c
    git submodule update --init
    ```

    Bu işlemin tamamlanması için birkaç dakika beklemeniz gerekebilir.

4. Git deposunun kök dizininde bir `cmake` alt dizini oluşturun ve o klasöre gidin. Dizinden aşağıdaki komutları `azure-iot-sdk-c` çalıştırın:

    ```cmd/sh
    mkdir cmake
    cd cmake
    ```

## <a name="build-the-sdk-and-run-the-tpm-device-simulator"></a>SDK'yı derleme ve TPM cihaz simülatörünü çalıştırma

Bu bölümde, TPM cihaz simülatörü örnek kodunu içeren Azure IoT C SDK'sını oluşturacaksınız. Bu örnek, Paylaşılan Erişim İmzası (SAS) Belirteci kimlik doğrulaması yoluyla bir TPM [kanıtlama mekanizması](concepts-security.md#attestation-mechanism) sağlar.

1. azure-iot-sdk-c git deposunda oluşturduğunuz `cmake` alt dizininden aşağıdaki komutu çalıştırarak örneği derleyin. Bu derleme komutuyla simülasyon cihazı için bir de Visual Studio çözümü oluşturulur.

    ```cmd/sh
    cmake -Duse_prov_client:BOOL=ON -Duse_tpm_simulator:BOOL=ON ..
    ```

    `cmake`, C++ derleyicinizi bulamazsa yukarıdaki komutu çalıştırırken derleme hatalarıyla karşılaşabilirsiniz. Bu durumda bu komutu [Visual Studio komut isteminde](https://docs.microsoft.com/dotnet/framework/tools/developer-command-prompt-for-vs) çalıştırmayı deneyin. 

    Derleme başarılı olduktan sonra, son birkaç çıkış satırı aşağıdaki çıkışa benzer olacaktır:

    ```cmd/sh
    $ cmake -Duse_prov_client:BOOL=ON -Duse_tpm_simulator:BOOL=ON ..
    -- Building for: Visual Studio 15 2017
    -- Selecting Windows SDK version 10.0.16299.0 to target Windows 10.0.17134.
    -- The C compiler identification is MSVC 19.12.25835.0
    -- The CXX compiler identification is MSVC 19.12.25835.0

    ...

    -- Configuring done
    -- Generating done
    -- Build files have been written to: E:/IoT Testing/azure-iot-sdk-c/cmake
    ```

2. Kopyaladığınız git deposunun kök klasörüne gidin ve aşağıda gösterilen yolu kullanarak [TPM](https://docs.microsoft.com/windows/device-security/tpm/trusted-platform-module-overview) simülatörünü çalıştırın. Bu simülatör 2321 ve 2322 bağlantı noktalarında bulunan bir yuva üzerinden dinler. Bu komut penceresini kapatmayın; bu hızlı başlangıcın sonuna kadar simülatörü çalışır durumda tutmanız gerekir. 

   *cmake* klasöründeyseniz aşağıdaki komutları çalıştırın:

    ```cmd/sh
    cd ..
    .\provisioning_client\deps\utpm\tools\tpm_simulator\Simulator.exe
    ```

    Simülatörden hiçbir çıkış görmeyeceksiniz. TPM cihazının simülasyonunu yaparak çalışmaya devam etmesine izin verin.

<a id="simulatetpm"></a>

## <a name="read-cryptographic-keys-from-the-tpm-device"></a>TPM cihazından şifreleme anahtarlarını okuma

Bu bölümde, çalışır ve 2321 ile 2322 numaralı bağlantı noktaları üzerinden dinler durumda bıraktığınız TPM simülatöründen onay anahtarını ve kayıt kimliğini okuyan bir örnek derleyecek ve bu örneği yürüteceksiniz. Bu değerler, Cihaz Sağlama Hizmeti örneğinize cihaz kaydı için kullanılacak.

1. Visual Studio’yu başlatın ve `azure_iot_sdks.sln` adlı yeni çözüm dosyasını açın. Bu çözüm dosyası daha önce azure-iot-sdk-c git deposunun kökünde oluşturduğunuz `cmake` klasöründe yer alır.

2. Visual Studio menüsünde, çözümdeki tüm projeleri oluşturmak için **Build** > **Solution'ı** seçin.

3. Visual Studio'nun *Çözüm Gezgini* penceresinde **Sağlama\_Araçları** klasörüne gidin. **tpm_device_provision** projesine sağ tıklayın ve **Başlangıç Projesi Olarak Ayarla**’yı seçin. 

4. Visual Studio menüsünde, çözümü çalıştırmak için hata ayıklama yapmadan **Hata Ayıklama** > **Başlat'ı** seçin. Uygulama bir Kayıt **_Kimliği_** ve **_Onay anahtarını_** okur ve görüntüler. Bu değerleri not edin veya kopyalayın. Bunlar, bir sonraki bölümde cihaz kaydı için kullanılacaktır. 


<a id="portalenrollment"></a>

## <a name="create-a-device-enrollment-entry-in-the-portal"></a>Portalda bir cihaz kaydı girişi oluşturma

1. Azure portalında oturum açın, sol menüdeki **Tüm kaynaklar** düğmesini seçin ve Cihaz Sağlama hizmetinizi açın.

1. Kayıtları **Yönet** sekmesini seçin ve ardından üstteki **tek tek kayıt ekle** düğmesini seçin. 

1. Kayıt **Ekle** paneline aşağıdaki bilgileri girin:
   - Kimlik onay *Mekanizması* olarak **TPM** seçeneğini belirleyin.
   - TPM aygıtınızın *Kayıt Kimliği* ve *Onay anahtarını* daha önce belirttiğiniz değerlerden girin.
   - Sağlama hizmetinizle bağlanacak IoT hub'ını seçin.
   - İsteğe bağlı olarak, aşağıdaki bilgileri sağlayabilirsiniz:
       - Benzersiz bir *Aygıt Kimliği* girin (önerilen test **dokümanları aygıtını** kullanabilir veya kendi kimliğinizi sağlayabilirsiniz). Cihazınızı adlandırırken gizli veriler kullanmaktan kaçının. Bir tane sağlamamayı seçerseniz, bunun yerine aygıtı tanımlamak için kayıt kimliği kullanılır.
       - **Başlangıç cihaz ikizi durumu** alanını cihaz için istenen başlangıç yapılandırmasına göre güncelleştirin.
   - Tamamlandıktan sonra **Kaydet** düğmesine basın. 

      ![Portala cihaz kayıt bilgilerini girme](./media/quick-create-simulated-device/enter-device-enrollment.png)  

      Kayıt başarıyla tamamlanınca cihazınızın *Kayıt Kimliği* listenin altında *Tek Tek Kayıtlar* sekmesinde görünür. 


<a id="firstbootsequence"></a>

## <a name="simulate-first-boot-sequence-for-the-device"></a>Cihazın ilk önyükleme sırasını benzetim olarak çalıştırma

Bu bölümde, örnek kodu yapılandırarak cihazın önyükleme sırasını Cihaz Sağlama Hizmeti örneğinize göndermek üzere [Gelişmiş İleti Sıraya Alma Protokolü (AMQP)](https://wikipedia.org/wiki/Advanced_Message_Queuing_Protocol) kullanmasını sağlayacaksınız. Bu önyükleme sırası cihazın tanınmasına ve Cihaz Sağlama Hizmeti örneğine bağlı bir IoT hub'ına atanmasına neden olur.

1. Azure portalda, Cihaz Sağlama hizmetiniz için **Genel Bakış** sekmesini seçin ve **_Kimlik Kapsamı_** değerini kopyalayın.

    ![Portaldan Cihaz Sağlama Hizmeti uç noktası bilgilerini ayıklama](./media/quick-create-simulated-device/extract-dps-endpoints.png) 

2. Visual Studio'nun *Çözüm Gezgini* penceresinde **Sağlama\_Örnekleri** klasörüne gidin. **prov\_dev\_client\_sample** adlı örnek projeyi genişletin. **Kaynak Dosyalar**'ı genişletin ve **prov\_dev\_client\_sample.c** dosyasını açın.

3. Dosyanın üst kısmında, aşağıda gösterildiği gibi her cihaz protokolü için `#define` deyimlerini bulun. Yalnızca `SAMPLE_AMQP` öğesinin açıklama haline getirilmediğinden emin olun.

    Şu anda, [TPM Bireysel Kaydı için MQTT protokolü desteklenmemektedir](https://github.com/Azure/azure-iot-sdk-c#provisioning-client-sdk).

    ```c
    //
    // The protocol you wish to use should be uncommented
    //
    //#define SAMPLE_MQTT
    //#define SAMPLE_MQTT_OVER_WEBSOCKETS
    #define SAMPLE_AMQP
    //#define SAMPLE_AMQP_OVER_WEBSOCKETS
    //#define SAMPLE_HTTP
    ```

4. `id_scope` sabitini bulun ve değeri daha önce kopyalamış olduğunuz **Kimlik Kapsamı** değerinizle değiştirin. 

    ```c
    static const char* id_scope = "0ne00002193";
    ```

5. Aynı dosyada `main()` işlevinin tanımını bulun. `hsm_type` değişkeninin aşağıda gösterildiği gibi `SECURE_DEVICE_TYPE_X509` yerine `SECURE_DEVICE_TYPE_TPM` değerine ayarlandığından emin olun.

    ```c
    SECURE_DEVICE_TYPE hsm_type;
    hsm_type = SECURE_DEVICE_TYPE_TPM;
    //hsm_type = SECURE_DEVICE_TYPE_X509;
    ```

6. **prov\_dev\_client\_sample** projesine sağ tıklayın ve **Başlangıç Projesi Olarak Ayarla**’yı seçin. 

7. Visual Studio menüsünde, çözümü çalıştırmak için hata ayıklama yapmadan **Hata Ayıklama** > **Başlat'ı** seçin. Projeyi yeniden oluşturma isteminde, çalıştırmadan önce projeyi yeniden oluşturmak için **Evet'i**seçin.

    Aşağıdaki çıkış, başarıyla önyüklemesi yapılan cihaz istemci örneğini sağlama ve IoT hub bilgilerini almak üzere Cihaz Sağlama Hizmeti'ne bağlanıp kaydolma işlemlerinin bir örneğidir:

    ```cmd
    Provisioning API Version: 1.2.7
    Provisioning Status: PROV_DEVICE_REG_STATUS_CONNECTED

    Registering... Press enter key to interrupt.

    Provisioning Status: PROV_DEVICE_REG_STATUS_CONNECTED
    Provisioning Status: PROV_DEVICE_REG_STATUS_ASSIGNING
    Provisioning Status: PROV_DEVICE_REG_STATUS_ASSIGNING

    Registration Information received from service:
    test-docs-hub.azure-devices.net, deviceId: test-docs-device
    ```

8. Simüle edilen aygıt, sağlama hizmetiniz tarafından IoT hub'ına sağlandıktan sonra, aygıt kimliği hub'ın **IoT aygıtlarıyla**birlikte görünür. 

    ![Cihaz IOT hub'da kayıtlı](./media/quick-create-simulated-device/hub-registration.png) 


## <a name="clean-up-resources"></a>Kaynakları temizleme

Aygıt istemcisi örneği üzerinde çalışmaya ve keşfetmeye devam etmeyi planlıyorsanız, bu hızlı başlatmada oluşturulan kaynakları temizlemeyin. Devam etmeyi planlamıyorsanız, bu hızlı başlatma tarafından oluşturulan tüm kaynakları silmek için aşağıdaki adımları kullanın.

1. Makinenizde cihaz istemci örnek çıktı penceresini kapatın.
2. Makinenizde TPM simülatörü penceresini kapatın.
3. Azure portalındaki sol menüden Tüm **kaynakları** seçin ve ardından Aygıt Sağlama hizmetinizi seçin. Hizmetiniz için **Kayıtları Yönet'i** açın ve ardından **Bireysel Kayıtlar** sekmesini seçin. Bu hızlı başlangıç ta kaydolduğunuz aygıtın *KAYIT KİmLİğİ'nin* yanındaki onay kutusunu seçin ve bölmenin üst kısmındaki **Sil** düğmesine basın. 
4. Azure portalındaki sol menüden Tüm **kaynakları** seçin ve ardından IoT hub'ınızı seçin. Hub'ınız için **IoT aygıtlarını** açın, bu hızlı başlatmada kaydettiğiniz aygıtın *AYGıT Kimliğinin* yanındaki onay kutusunu seçin ve ardından bölmenin üst kısmındaki **Sil** düğmesine basın.

## <a name="next-steps"></a>Sonraki adımlar

Bu hızlı başlangıçta, makinenizde bir TPM simüle edilmiş aygıt oluşturdunuz ve IoT Hub Aygıt Sağlama Hizmeti'ni kullanarak IoT hub'ınıza sokulmuşsunuz. TPM aygıtınızı programlı olarak nasıl kaydedebilirsiniz öğrenmek için, bir TPM aygıtının programlı kaydı için hızlı bir şekilde başlamaya devam edin. 

> [!div class="nextstepaction"]
> [Azure quickstart - TPM aygıtını Azure IoT Hub Aygıt Sağlama Hizmetine kaydedin](quick-enroll-device-tpm-java.md)
