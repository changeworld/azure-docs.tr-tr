---
title: TPM aygıtını Java kullanarak Azure Aygıt Sağlama Hizmetine kaydedin
description: Quickstart - TPM aygıtını Java hizmeti SDK'yı kullanarak Azure IoT Hub Aygıt Sağlama Hizmetine (DPS) kaydedin. Bu hızlı başlangıçta bireysel kayıtlar kullanılmaktadır.
author: wesmc7777
ms.author: wesmc
ms.date: 11/08/2019
ms.topic: quickstart
ms.service: iot-dps
services: iot-dps
ms.devlang: java
ms.custom: mvc
ms.openlocfilehash: c199d5be4c103c80a6fcc126af70f48367909f64
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/26/2020
ms.locfileid: "79241699"
---
# <a name="quickstart-enroll-tpm-device-to-iot-hub-device-provisioning-service-using-java-service-sdk"></a>Quickstart: Java Service SDK kullanarak TPM cihazını IoT Hub Aygıt Sağlama Hizmetine kaydedin

[!INCLUDE [iot-dps-selector-quick-enroll-device-tpm](../../includes/iot-dps-selector-quick-enroll-device-tpm.md)]

Bu hızlı başlatmada, örnek bir Java uygulaması yardımıyla Java Service SDK'yı kullanarak Azure IoT Hub Aygıt Sağlama Hizmeti'nde simüle edilmiş bir TPM aygıtı için programlı bir şekilde tek bir kayıt oluşturursunuz.

## <a name="prerequisites"></a>Ön koşullar

- Azure [portalı ile IoT Hub Aygıt Sağlama Hizmetini Ayarlama'nın](./quick-setup-auto-provision.md)tamamlanması.
- [TPM aygıtından Okuma şifreleme anahtarlarının](quick-create-simulated-device.md#simulatetpm)tamamlanması.
- Etkin bir aboneliği olan bir Azure hesabı. [Ücretsiz bir tane oluşturun.](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)
- [Java SE Geliştirme Kiti 8](https://aka.ms/azure-jdks). Bu quickstart aşağıdaki [Java Hizmeti SDK](https://azure.github.io/azure-iot-sdk-java/service/) yükler. Hem Windows hem de Linux'ta çalışır. Bu hızlı başlatma Windows kullanır.
- [Maven 3](https://maven.apache.org/download.cgi).
- [Git.](https://git-scm.com/download/)

<a id="setupdevbox"></a>

## <a name="prepare-the-development-environment"></a>Geliştirme ortamını hazırlama 

1. Makinenizde [Java SE Development Kit 8](https://aka.ms/azure-jdks) uygulamasının yüklü olduğundan emin olun. 

2. Java yüklemeniz için ortam değişkenlerini ayarlayın. `PATH` değişkeni *jdk1.8.x\bin* dizininin tam yolunu içermelidir. Makinenize ilk yüklediğiniz Java uygulaması buysa, `JAVA_HOME` adlı bir ortam değişkeni oluşturup *jdk1.8.x* dizininin tam yolu olacak şekilde ayarlayın. Windows makinesinde bu dizin *C:\\Program Files\\Java\\* klasöründe bulunur ve ortam değişkenlerini oluşturmak veya düzenlemek için Windows makinenizin **Denetim masası** sayfasında **Sistem ortam değişkenlerini düzenleyin** araması yapabilirsiniz. 

   Java'nın makinenize başarıyla yüklenip yüklenmediğini kontrol etmek için komut pencerenizde aşağıdaki komutu çalıştırabilirsiniz:

    ```cmd\sh
    java -version
    ```

3. [Maven 3](https://maven.apache.org/download.cgi) uygulamasını indirin ve makinenizde ayıklayın. 

4. `PATH` adlı ortam değişkenini Maven uygulamasının ayıklandığı klasörün içindeki *apache-maven-3.x.x\\bin* klasörünü gösterecek şekilde düzenleyin. Maven uygulamasının makinenize başarıyla yüklenip yüklenmediğini kontrol etmek için komut pencerenizde aşağıdaki komutu çalıştırabilirsiniz:

    ```cmd\sh
    mvn --version
    ```

5. [git](https://git-scm.com/download/) uygulamasının makinenizde yüklü olduğundan ve `PATH` adlı ortam değişkenlerine eklendiğinden emin olun. 


<a id="javasample"></a>

## <a name="download-and-modify-the-java-sample-code"></a>Örnek Java kodunu indirme ve değiştirme

Bu bölümde örnek koda TPM cihazınızın sağlama ayrıntılarını nasıl ekleyeceğiniz gösterilir. 

1. Bir komut istemi açın. [Java Service SDK'yı](https://azure.github.io/azure-iot-sdk-java/service/)kullanarak cihaz kayıt kodu örneği için GitHub repo'yu klonlayın:
    
    ```cmd\sh
    git clone https://github.com/Azure/azure-iot-sdk-java.git --recursive
    ```

2. İndirilen kaynak kodunda örnek klasörüne gidin: **_azure-iot-sdk-java/provisioning/provisioning-samples/service-enrollment-sample_**. **_/src/main/java/samples/com/microsoft/azure/sdk/iot/ServiceEnrollmentSample.java_** adlı dosyayı istediğiniz düzenleyicide açıp aşağıdaki ayrıntıları ekleyin:

   1. Portaldan sağlama hizmetinize ait `[Provisioning Connection String]` bilgisini aşağıdaki şekilde ekleyin:
       1. [Azure portalında](https://portal.azure.com) sağlama hizmetinize gidin. 
       2. **Paylaşılan erişim ilkeleri**'ni açıp *EnrollmentWrite* iznine sahip bir ilke seçin.
       3. **Birincil anahtar bağlantı dizesini** kopyalayın. 

           ![Sağlama bağlantısı dizesini portaldan alma](./media/quick-enroll-device-tpm-java/provisioning-string.png)  

       4. **_ServiceEnrollmentSample.java_** adlı örnek kod dosyasında `[Provisioning Connection String]` yerine **Birincil anahtar bağlantı dizesini** yazın.
    
           ```Java
           private static final String PROVISIONING_CONNECTION_STRING = "[Provisioning Connection String]";
           ```

   2. TPM cihazı ayrıntıları ekleyin:
       1. [TPM cihazını benzetme](quick-create-simulated-device.md#simulatetpm) bölümündeki adımları izleyerek TPM cihazı benzetmesinin *Kayıt Kimliği* ve *TPM onay anahtarı* değerlerini alın.
       2. Önceki adımda aldığınız **_Kayıt Kimliği_** ve **_Onay Anahtarı_** değerlerini **_ServiceEnrollmentSample.java_** örnek kod dosyasındaki `[RegistrationId]` ve `[TPM Endorsement Key]` değerlerinin yerine yazın:
        
           ```Java
           private static final String REGISTRATION_ID = "[RegistrationId]";
           private static final String TPM_ENDORSEMENT_KEY = "[TPM Endorsement Key]";
           ```

   3. İsteğe bağlı olarak örnek kod aracılığıyla sağlama hizmetinizi de yapılandırabilirsiniz:
      - Örneğe bu yapılandırmayı eklemek için aşağıdaki adımları izleyin:
        1. [Azure portalında](https://portal.azure.com) sağlama hizmetinizle bağlantılı IoT hub sayfasına gidin. Hub'ın **Özet** sayfasını açıp **Ana bilgisayar adı**'nı kopyalayın. Bu **Ana bilgisayar adı** değerini *IOTHUB_HOST_NAME* parametresine atayın.
            ```Java
            private static final String IOTHUB_HOST_NAME = "[Host name].azure-devices.net";
            ```
        2. *DEVICE_ID* parametresine kolay ad atayıp *PROVISIONING_STATUS* parametresinin varsayılan *ENABLED* değerini tutun. 
    
      - VEYA, sağlama hizmetinizi yapılandırmak istemezseniz _ServiceEnrollmentSample.java_ dosyasında bulunan aşağıdaki bölümleri açıklama satırı yapmayı veya silmeyi unutmayın:
          ```Java
          // The following parameters are optional. Remove it if you don't need.
          individualEnrollment.setDeviceId(DEVICE_ID);
          individualEnrollment.setIotHubHostName(IOTHUB_HOST_NAME);
          individualEnrollment.setProvisioningStatus(PROVISIONING_STATUS);
          ```

   4. Örnek kodu inceleyin. Bu kod bireysel TPM cihazı kaydı oluşturup bu kaydı güncelleştirir, sorgular ve siler. Portal kaydının başarılı olduğundan emin olmak için _ServiceEnrollmentSample.java_ dosyasının en sonunda bulunan aşağıdaki kod satırlarını geçici olarak açıklama satırı yapın:
    
       ```Java
       // *********************************** Delete info of individualEnrollment ************************************
       System.out.println("\nDelete the individualEnrollment...");
       provisioningServiceClient.deleteIndividualEnrollment(REGISTRATION_ID);
       ```

   5. _ServiceEnrollmentSample.java_ adlı dosyayı kaydedin.

<a id="runjavasample"></a>

## <a name="build-and-run-the-java-sample-code"></a>Java örnek kodunu derleme ve çalıştırma

1. Bir komut penceresi açıp **_azure-iot-sdk-java/provisioning/provisioning-samples/service-enrollment-sample_** klasörüne gidin.

2. Şu komutu kullanarak örnek kodu derleyin:

    ```cmd\sh
    mvn install -DskipTests
    ```

   Bu komut, Maven [`com.microsoft.azure.sdk.iot.provisioning.service`](https://www.mvnrepository.com/artifact/com.microsoft.azure.sdk.iot.provisioning/provisioning-service-client) paketini makinenize indirir. Bu paket, örnek kodun oluşturması gereken [Java Hizmeti SDK'sı](https://azure.github.io/azure-iot-sdk-java/service/)için ikili yayınları içerir. 

3. Komut penceresinde aşağıdaki komutları çalıştırarak örneği çalıştırın:

    ```cmd\sh
    cd target
    java -jar ./service-enrollment-sample-{version}-with-deps.jar
    ```

4. Kaydın başarılı olup olmadığını görmek için çıktı penceresini izleyin. 

5. Azure portalında sağlama hizmetinize gidin. **Kayıtları Yönet'i**seçin ve **Bireysel Kayıtlar** sekmesini seçin. Benzetimli TPM aygıtınızın *Kayıt Kimliğinin* artık listelenmiş olduğuna dikkat edin. 

    ![Portalda TPM kaydının başarılı olup olmadığını doğrulama](./media/quick-enroll-device-tpm-java/verify-tpm-enrollment.png)  

## <a name="clean-up-resources"></a>Kaynakları temizleme
Java hizmeti örneğini keşfetmeyi planlıyorsanız, bu hızlı başlangıçta oluşturulan kaynakları temizlemeyin. Devam etmeyi planlamıyorsanız, bu hızlı başlatma tarafından oluşturulan tüm kaynakları silmek için aşağıdaki adımları kullanın.

1. Makinenizdeki Java örnek çıktı penceresini kapatın.
1. TPM cihazınızı benzetmek için oluşturmuş olabileceğiniz TPM simülatörü penceresini kapatın.
1. Azure portalında Cihaz Sağlama hizmetinize gidin, **Kayıtları Yönet'i**seçin ve ardından **Bireysel Kayıtlar** sekmesini seçin. Bu hızlı başlangıcı kullanarak oluşturduğunuz kayıt girişi için *Kayıt Kimliği'nin* yanındaki onay kutusunu seçin ve bölmenin üst kısmındaki **Sil** düğmesine basın.

## <a name="next-steps"></a>Sonraki adımlar
Bu hızlı başlangıçta, simüle edilmiş bir TPM aygıtını Aygıt Sağlama hizmetinize kaydettirdiniz. Cihaz sağlama hakkında ayrıntılı bilgi edinmek için Azure portalında Cihaz Sağlama Hizmeti ayarları öğreticisine geçin. 

> [!div class="nextstepaction"]
> [Azure IoT Hub Cihazı Sağlama Hizmeti öğreticileri](./tutorial-set-up-cloud.md)
