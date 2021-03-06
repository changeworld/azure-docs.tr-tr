---
author: IEvangelist
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 02/20/2020
ms.author: dapine
ms.openlocfilehash: d14760147e202491546982254aa8288bc0184eef
ms.sourcegitcommit: 0450ed87a7e01bbe38b3a3aea2a21881f34f34dd
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/03/2020
ms.locfileid: "80658511"
---
Bu hızlı başlangıçta, konuşma özellikli bir ürün oluşturmak veya [Konuşma Transkripsiyon](../conversation-transcription-service.md) aygıtı olarak kullanmak için Linux için Konuşma Aygıtları SDK'yı nasıl kullanacağınızı öğreneceksiniz. Şu anda yalnızca [Azure Kinect DK](https://azure.microsoft.com/services/kinect-dk/) desteklenir.

Uygulama Konuşma SDK paketi ve Eclipse Java IDE (v4) 64-bit Linux (Ubuntu 16.04, Ubuntu 18.04, Debian 9, RHEL 8, CentOS 8) üzerine inşa edilmiştir. 64 bit Java 8 çalışma zamanı ortamında (JRE) çalışır.

Bu kılavuz, Konuşma hizmeti kaynağına sahip bir [Azure Bilişsel Hizmetler](../get-started.md) hesabı gerektirir. Bir hesabınız yoksa, abonelik anahtarı almak için [ücretsiz deneme sürümünü](https://azure.microsoft.com/try/cognitive-services/) kullanabilirsiniz.

[Örnek uygulamanın](https://aka.ms/sdsdk-download-JRE) kaynak kodu Konuşma Aygıtları SDK'ya dahildir. [GitHub'da](https://github.com/Azure-Samples/Cognitive-Services-Speech-Devices-SDK)da mevcuttur.

## <a name="prerequisites"></a>Ön koşullar

Bu hızlı başlangıç şunları gerektirir:

* İşletim Sistemi: 64-bit Linux (Ubuntu 16.04, Ubuntu 18.04, Debian 9, RHEL 8, CentOS 8)
* [Azure Kinect DK](https://azure.microsoft.com/services/kinect-dk/)
* [Eclipse Java IDE](https://www.eclipse.org/downloads/)
* [Yalnızca Java 8](https://www.oracle.com/technetwork/java/javase/downloads/jre8-downloads-2133155.html) veya [JDK 8.](https://www.oracle.com/technetwork/java/javase/downloads/index.html)
* Konuşma hizmeti için azure abonelik anahtarı. [Ücretsiz bir tane alın.](../get-started.md)
* Java için Konuşma [Aygıtları SDK'nın](https://aka.ms/sdsdk-download-JRE) en son sürümünü indirin ve .zip'i çalışma dizininize ayıklayın.
   > [!NOTE]
   > Bu hızlı başlangıç, uygulamanın /home/wcaltest/JRE-Sample-Release'e çıkarıldığını varsayar

Eclipse'i başlatmadan önce bu bağımlılıkların yüklü olduğundan emin olun.

* Ubuntu üzerinde:

  ```sh
  sudo apt-get update
  sudo apt-get install libssl1.0.0 libasound2
  ```

* Debian 9'da:

  ```sh
  sudo apt-get update
  sudo apt-get install libssl1.0.2 libasound2
  ```

RHEL/CentOS 8'de:

  ```sh
  sudo yum update
  sudo yum install alsa-lib openssl
  ```

> [!NOTE]
> RHEL/CentOS 8'de [OpenSSL'in Linux için nasıl yapılandırılacağı](~/articles/cognitive-services/speech-service/how-to-configure-openssl-linux.md)yla ilgili talimatları uygulayın.

Konuşma Transkripsiyon şu anda sadece "en-US" ve "zh-CN" için, "centralus" ve "eastasia" bölgelerde kullanılabilir. Konuşma Transkripsiyon'u kullanmak için bu bölgelerden birinde bir konuşma anahtarına sahip olmalısınız.

Bu amaçları kullanmayı planlıyorsanız, bir [Dil Anlama Hizmeti (LUIS)](https://docs.microsoft.com/azure/cognitive-services/luis/azureibizasubscription) aboneliğine ihtiyacınız vardır. LUIS ve niyet tanıma hakkında daha fazla bilgi edinmek için [bkz.](https://docs.microsoft.com/azure/cognitive-services/speech-service/how-to-recognize-intents-from-speech-csharp) Bu uygulama için örnek bir [LUIS modeli](https://aka.ms/sdsdk-luis) mevcuttur.

## <a name="create-and-configure-the-project"></a>Projeyi oluşturma ve yapılandırma

1. Eclipse’i başlatın.

1. Eclipse **IDE Başlatıcısı'** nda, **Çalışma Alanı** alanında, yeni bir çalışma alanı dizininin adını girin. Ardından **Başlat**’ı seçin.

   ![Eclipse Başlatıcısı ekran görüntüsü](../media/speech-devices-sdk/eclipse-launcher-linux.png)

1. Çok geçmeden Eclipse IDE ana penceresi görüntülenir. Varsa, Hoş Geldiniz ekranını kapatın.

1. Eclipse menü çubuğundan, **Dosya** > **Yeni** > **Java Projesi**seçerek yeni bir proje oluşturun. Varsa **Project'i** ve ardından **Java Project'i**seçin.

1. **Yeni Java Projesi** sihirbazı başlar. Örnek projenin konumuna **göz atın.** **Bitiş'i**seçin.

   ![Yeni Java Projesi sihirbazının ekran görüntüsü](../media/speech-devices-sdk/eclipse-new-java-project-linux.png)

1. Paket **gezgininde,** projenizi sağ tıklatın. Bağlam menüsünden > **Maven Project'e** **Dönüştür'ü Yapılandır'ı**seçin. **Bitiş'i**seçin.

   ![Paket gezgininin ekran görüntüsü](../media/speech-devices-sdk/eclipse-convert-to-maven.png)

1. pom.xml dosyasını açıp düzenleyin.

    Dosyanın sonunda, kapanış `</project>`etiketinden önce, `repositories` `dependencies` burada gösterildiği gibi oluşturma ve `version` öğeleri oluşturun ve geçerli sürümünüzle eşleştiğinden emin olun:
    ```xml    
    <repositories>
         <repository>
             <id>maven-cognitiveservices-speech</id>
             <name>Microsoft Cognitive Services Speech Maven Repository</name>
             <url>https://csspeechstorage.blob.core.windows.net/maven/</url>
         </repository>
    </repositories>
 
    <dependencies>
        <dependency>
             <groupId>com.microsoft.cognitiveservices.speech</groupId>
             <artifactId>client-sdk</artifactId>
             <version>1.11.0</version>
        </dependency>
    </dependencies>
   ```

1. Paket **gezgininde,** projenizi sağ tıklatın. **Özellikleri**seçin, ardından **Çalıştır/Hata Ayıklama Ayarları** > **Yeni...** > **Java Uygulaması**. 

1. **Yapılandırmayı Edit** penceresi görüntülenir. **Ad** alanında **Ana**girin ve **com.microsoft.cognitiveservices.speech.samples.FunctionsList**adresini bulmak ve seçmek için **Ana Sınıfı** **Ara'yı** kullanın.

   ![Başlat Yapılandırmayı Edit Ekran Görüntüsü](../media/speech-devices-sdk/eclipse-edit-launch-configuration-linux.png)

1. **Linux-arm** veya **Linux-x64'ten**Java Project konumuna, örneğin **/home/wcaltest/JRE-Sample-Release'e** kadar hedef mimariniz için ses ikililerini kopyalayın

1. Ayrıca **Yapılandırmayı Edit** penceresinden **Ortam** sayfasını ve **Yeni'yi**seçin. **Yeni Çevre Değişkeni** penceresi görüntülenir. **Ad** alanında **LD_LIBRARY_PATH** girin ve **değer** alanında *.so dosyaları içeren klasörü girin, örneğin **/home/wcaltest/JRE-Sample-Release**

1. Proje `kws.table` `participants.properties` klasörü **hedef/sınıflara** kopyala ve


## <a name="configure-the-sample-application"></a>Örnek uygulamayı yapılandırma

1. Konuşma aboneliği anahtarınızı kaynak koduna ekleyin. Niyet tanımayı denemek istiyorsanız, [Dil Bilgisi hizmet](https://azure.microsoft.com/services/cognitive-services/language-understanding-intelligent-service/) abonelik anahtarınızı ve uygulama kimliğinizi de ekleyin.

   Konuşma ve LUIS için, `FunctionsList.java`bilgileriniz in:

   ```java
    // Subscription
    private static String SpeechSubscriptionKey = "<enter your subscription info here>";
    private static String SpeechRegion = "westus"; // You can change this if your speech region is different.
    private static String LuisSubscriptionKey = "<enter your subscription info here>";
    private static String LuisRegion = "westus2"; // you can change this, if you want to test the intent, and your LUIS region is different.
    private static String LuisAppId = "<enter your LUIS AppId>";
   ```

    Konuşma transkripsiyonunu kullanıyorsanız, konuşma anahtarınız ve `Cts.java`bölge bilgileriniz de şu şekilde gereklidir:

   ```java
    private static final String CTSKey = "<Conversation Transcription Service Key>";
    private static final String CTSRegion="<Conversation Transcription Service Region>";// Region may be "centralus" or "eastasia"
    ```

1. Varsayılan anahtar kelime (anahtar kelime) "Bilgisayar"dır. Ayrıca, "Makine" veya "Yardımcı" gibi sağlanan diğer anahtar kelimelerden birini de deneyebilirsiniz. Bu alternatif anahtar kelimelerin kaynak dosyaları Konuşma Aygıtları SDK'da, anahtar kelime klasöründe bulunmaktadır. Örneğin, `/home/wcaltest/JRE-Sample-Release/keyword/Computer` "Bilgisayar" anahtar kelimesi için kullanılan dosyaları içerir.

   > [!TIP]
   > [Ayrıca özel bir anahtar kelime oluşturabilirsiniz.](../speech-devices-sdk-create-kws.md)

    Yeni bir anahtar kelime kullanmak için `FunctionsList.java`aşağıdaki satırı güncelleştirin ve anahtar kelimeyi uygulamanız için kopyalayın. Örneğin, anahtar kelime paketinden `machine.zip`'Makine' anahtar kelimesini kullanmak için:

   * Dosyayı `kws.table` zip paketinden proje klasörü **hedefine/sınıflarına**kopyalayın.

   * Anahtar `FunctionsList.java` kelime adı ile güncelleştirin:

     ```java
     private static final String Keyword = "Machine";
     ```

## <a name="run-the-sample-application-from-eclipse"></a>Eclipse'den örnek uygulamayı çalıştırın

1. Eclipse menü çubuğundan, **Run** > **Run** 

1. Konuşma Aygıtları SDK örnek uygulaması başlar ve aşağıdaki seçenekleri görüntüler:

   ![Örnek Konuşma Cihazları SDK örnek uygulama ve seçenekleri](../media/speech-devices-sdk/java-sample-app-linux.png)

1. Yeni **Konuşma Transkripsiyon** demosu deneyin. **Oturum** > **Başlangıç**ile transkripsiyonu başlatın. Varsayılan olarak herkes bir konuk. Ancak, katılımcının ses imzaları varsa, bunlar `participants.properties` proje klasörü **hedef/sınıflara**konulabilir. Ses imzasını oluşturmak [için, Transcribe konuşmalarına (SDK)](../how-to-use-conversation-transcription-service.md)bakın.

   ![Demo Konuşma Transkripsiyon uygulaması](../media/speech-devices-sdk/cts-sample-app-linux.png)

## <a name="create-and-run-standalone-the-application"></a>Uygulamayı tek başına oluşturma ve çalıştırma

1. Paket **gezgininde,** projenizi sağ tıklatın. **Dışa Aktarma'yı**seçin. 
1. **Dışa Aktarma** penceresi görüntülenir. **Java'yı** genişletin ve **Runnable JAR dosyasını** seçin ve sonra **Sonraki'** yi seçin.

   ![Dışa Aktarma penceresinin ekran görüntüsü](../media/speech-devices-sdk/eclipse-export-linux.png) 

1. **Runnable JAR Dosya Dışa aktarma** penceresi görüntülenir. Uygulama için bir **Dışa Aktarma hedefi** seçin ve ardından **Finish'i**seçin.
 
   ![Runnable JAR Dosya Verme Ekran Görüntüsü](../media/speech-devices-sdk/eclipse-export-jar-linux.png)

1. Bu `kws.table` dosyalar `participants.properties` uygulama tarafından gerekli olduğu için lütfen yukarıda seçilen hedef klasörüne koyun.

1. LD_LIBRARY_LIB *.so dosyalarını içeren klasöre ayarlama

     ```bash
     export LD_LIBRARY_PATH=/home/wcaltest/JRE-Sample-Release
     ```

1. Bağımsız uygulamayı çalıştırmak için

     ```bash
     java -jar SpeechDemo.jar
     ```
