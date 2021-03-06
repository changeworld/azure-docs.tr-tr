---
title: "Öğretici: Apache Kafka Üretici & Tüketici API'leri - Azure HDInsight"
description: HDInsight’ta Apache Kafka Üretici ve Tüketici API’lerini kullanmayı öğrenin. Bu öğreticide, bu API’leri HDInsight üzerinde Kafka ile kullanmayı öğreneceksiniz.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: tutorial
ms.date: 10/08/2019
ms.openlocfilehash: 5a7d4d1917f65cd3d836db83600937a3e3d89de6
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/24/2020
ms.locfileid: "79239542"
---
# <a name="tutorial-use-the-apache-kafka-producer-and-consumer-apis"></a>Öğretici: Apache Kafka Üretici ve Tüketici API’lerini kullanma

HDInsight’ta Apache Kafka Üretici ve Tüketici API’lerini kullanmayı öğrenin.

Kafka Üretici API’si, uygulamaların Kafka kümesine veri akışları göndermesine olanak tanır. Kafka Tüketici API’si, uygulamaların kümeden veri akışları okumasına olanak tanır.

Bu öğreticide şunların nasıl yapıldığını öğrenirsiniz:

> [!div class="checklist"]
> * Ön koşullar
> * Kodu anlama
> * Uygulama derleme ve dağıtma
> * Uygulamayı küme üzerinde çalıştırma

API’ler hakkında daha fazla bilgi için [Üretici API’si](https://kafka.apache.org/documentation/#producerapi) ve [Tüketici API’si](https://kafka.apache.org/documentation/#consumerapi) hakkında Apache belgelerine bakın.

## <a name="prerequisites"></a>Ön koşullar

* Apache Kafka HDInsight kümesi nde. Kümeyi nasıl oluşturacağınıöğrenmek için [HDInsight'ta Apache Kafka ile başlat'a](apache-kafka-get-started.md)bakın.
* [Java Geliştirici Kiti (JDK) sürüm 8](https://aka.ms/azure-jdks) veya OpenJDK gibi eşdeğeri.
* [Apache Maven](https://maven.apache.org/download.cgi) düzgün Apache göre [yüklü.](https://maven.apache.org/install.html)  Maven Java projeleri için bir proje inşa sistemidir.
* Putty gibi bir SSH müşterisi. Daha fazla bilgi için [SSH kullanarak HDInsight'a (Apache Hadoop) bağlan'a](../hdinsight-hadoop-linux-use-ssh-unix.md)bakın.

## <a name="understand-the-code"></a>Kodu anlama

Örnek [https://github.com/Azure-Samples/hdinsight-kafka-java-get-started](https://github.com/Azure-Samples/hdinsight-kafka-java-get-started)uygulama, `Producer-Consumer` alt dizinde yer alır. **Kurumsal Güvenlik Paketi (ESP)** etkin Kafka kümesi kullanıyorsanız, `DomainJoined-Producer-Consumer` alt dizinde bulunan uygulama sürümünü kullanmanız gerekir.

Uygulama öncelikli olarak dört dosyadan oluşur:
* `pom.xml`: Bu dosya, proje bağımlılıklarını, Java sürümünü ve paketleme yöntemlerini tanımlar.
* `Producer.java`: Bu dosya, üretici API’sini kullanarak Kafka’ya rastgele tümceler gönderir.
* `Consumer.java`: Bu dosya, tüketici API’sini kullanarak Kafka’dan verileri okur ve STDOUT’a yayar.
* `AdminClientWrapper.java`: Bu dosya Kafka konularını oluşturmak, açıklamak ve silmek için yönetici API'sını kullanır.
* `Run.java`: Üretici ve tüketici kodunu çalıştırmak için kullanılan komut satırı arabirimi.

### <a name="pomxml"></a>Pom.xml

`pom.xml` dosyasında aşağıdaki önemli şeyler anlaşılır:

* Bağımlılıklar: Bu proje, `kafka-clients` paketi tarafından sağlanan Kafka üretici ve tüketici API’lerini kullanır. Aşağıdaki XML kodu, bu bağımlılığı tanımlar:

    ```xml
    <!-- Kafka client for producer/consumer operations -->
    <dependency>
            <groupId>org.apache.kafka</groupId>
            <artifactId>kafka-clients</artifactId>
            <version>${kafka.version}</version>
    </dependency>
    ```

    `${kafka.version}` girişi, `pom.xml` dosyasının `<properties>..</properties>` bölümünde bildirilir ve HDInsight kümesinin Kafka sürümüne yapılandırılır.

* Eklentiler: Maven eklentileri çeşitli özellikler sağlar. Bu projede aşağıdaki eklentiler kullanılır:

    * `maven-compiler-plugin`: Proje tarafından kullanılan Java sürümünü 8 olarak ayarlamak için kullanılır. HDInsight 3.6 tarafından kullanılan Java sürümüdür.
    * `maven-shade-plugin`: Bu uygulamayı ve tüm bağımlılıkları içeren bir uber jar oluşturmak için kullanılır. Ana sınıfı belirtmek zorunda olmadan doğrudan Jar dosyasını çalıştırabilmeniz için uygulamanın giriş noktasını ayarlamak için de kullanılır.

### <a name="producerjava"></a>Producer.java

Üretici, Kafka aracı konakları (çalışan düğümleri) ile iletişim kurar ve verileri bir Kafka konusuna gönderir. Aşağıdaki kod parçacığı [GitHub deposundan](https://github.com/Azure-Samples/hdinsight-kafka-java-get-started) [Producer.java](https://github.com/Azure-Samples/hdinsight-kafka-java-get-started/blob/master/Producer-Consumer/src/main/java/com/microsoft/example/Producer.java) dosyasından gelir ve üretici özelliklerini nasıl ayarlayacaýný gösterir:

```java
Properties properties = new Properties();
// Set the brokers (bootstrap servers)
properties.setProperty("bootstrap.servers", brokers);
// Set how to serialize key/value pairs
properties.setProperty("key.serializer","org.apache.kafka.common.serialization.StringSerializer");
properties.setProperty("value.serializer","org.apache.kafka.common.serialization.StringSerializer");
KafkaProducer<String, String> producer = new KafkaProducer<>(properties);
```

### <a name="consumerjava"></a>Consumer.java

Tüketici, Kafka aracı konakları (çalışan düğümleri) ile iletişim kurar ve bir döngüdeki kayıtları okur. [Consumer.java](https://github.com/Azure-Samples/hdinsight-kafka-java-get-started/blob/master/Producer-Consumer/src/main/java/com/microsoft/example/Consumer.java) dosyasından alınan aşağıdaki kod parçacığı tüketici özelliklerini ayarlar:

```java
KafkaConsumer<String, String> consumer;
// Configure the consumer
Properties properties = new Properties();
// Point it to the brokers
properties.setProperty("bootstrap.servers", brokers);
// Set the consumer group (all consumers must belong to a group).
properties.setProperty("group.id", groupId);
// Set how to serialize key/value pairs
properties.setProperty("key.deserializer","org.apache.kafka.common.serialization.StringDeserializer");
properties.setProperty("value.deserializer","org.apache.kafka.common.serialization.StringDeserializer");
// When a group is first created, it has no offset stored to start reading from. This tells it to start
// with the earliest record in the stream.
properties.setProperty("auto.offset.reset","earliest");

consumer = new KafkaConsumer<>(properties);
```

Bu kodda tüketici, konu başlangıcından okumak üzere yapılandırılmıştır (`auto.offset.reset` değeri `earliest` olarak ayarlanır.)

### <a name="runjava"></a>Run.java

[Run.java](https://github.com/Azure-Samples/hdinsight-kafka-java-get-started/blob/master/Producer-Consumer/src/main/java/com/microsoft/example/Run.java) dosyası, üretici veya tüketici kodunu çalıştıran bir komut satırı arabirimi sağlar. Kafka aracı konağı bilgilerini bir parametre olarak sağlamanız gerekir. İsteğe bağlı olarak, tüketici işlemi tarafından kullanılan bir grup kimliği değeri ekleyebilirsiniz. Aynı grup kimliğini kullanarak birden çok tüketici örneği oluşturursanız, bunlar konudaki bakiye okumasını yükler.

## <a name="build-and-deploy-the-example"></a>Örnek derleme ve dağıtma

Bu adımı atlamak isterseniz, önceden oluşturulmuş kavanozlar `Prebuilt-Jars` alt dizinden indirilebilir. Kafka-üretici-tüketici.jar indirin. Kümeniz **Kurumsal Güvenlik Paketi (ESP)** etkinse, kafka-üretici-tüketici-esp.jar'ı kullanın. Kavanozu HDInsight kümenize kopyalamak için adım 3'e uygulayın.

1. Örnekleri indirin ve [https://github.com/Azure-Samples/hdinsight-kafka-java-get-started](https://github.com/Azure-Samples/hdinsight-kafka-java-get-started)ayıklayın.

2. Geçerli dizininizi `hdinsight-kafka-java-get-started\Producer-Consumer` dizinin konumuna ayarlayın. Kurumsal Güvenlik **Paketi (ESP)** etkin Kafka kümesi kullanıyorsanız, `DomainJoined-Producer-Consumer`konumu alt dizine ayarlamanız gerekir. Uygulamayı oluşturmak için aşağıdaki komutu kullanın:

    ```cmd
    mvn clean package
    ```

    Bu komut, `kafka-producer-consumer-1.0-SNAPSHOT.jar` adlı dosyayı içeren `target` adlı bir dizin oluşturur.

3. `sshuser` değerini, kümenizin SSH kullanıcısı ile, `CLUSTERNAME` değerini kümenizin adıyla değiştirin. Dosyayı `kafka-producer-consumer-1.0-SNAPSHOT.jar` HDInsight kümenize kopyalamak için aşağıdaki komutu girin. İstendiğinde, SSH kullanıcısının parolasını girin.

    ```cmd
    scp ./target/kafka-producer-consumer-1.0-SNAPSHOT.jar sshuser@CLUSTERNAME-ssh.azurehdinsight.net:kafka-producer-consumer.jar
    ```

## <a name="run-the-example"></a><a id="run"></a> Örneği çalıştırma

1. `sshuser` değerini, kümenizin SSH kullanıcısı ile, `CLUSTERNAME` değerini kümenizin adıyla değiştirin. Aşağıdaki komutu girerek kümeye bir SSH bağlantısı açın. İstendiğinde, SSH kullanıcı hesabının parolasını girin.

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. Kafka broker ev sahiplerini almak `<clustername>` için, `<password>` değerleri aşağıdaki komutun yerine getirin ve uygulayın. Azure portalında gösterildiği `<clustername>` gibi aynı kılıfı kullanın. Küme `<password>` giriş parolası ile değiştirin ve çalıştırın:

    ```bash
    sudo apt -y install jq
    export clusterName='<clustername>'
    export password='<password>'
    export KAFKABROKERS=$(curl -sS -u admin:$password -G https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/KAFKA/components/KAFKA_BROKER | jq -r '["\(.host_components[].HostRoles.host_name):9092"] | join(",")' | cut -d',' -f1,2);
    ```

    > [!Note]  
    > Bu komut Ambari erişimi gerektirir. Kümeniz bir NSG'nin arkasındaysa, bu komutu Ambari'ye erişebilen bir makineden çalıştırın.

1. Kafka konusunu `myTest`aşağıdaki komutu girerek oluşturun:

    ```bash
    java -jar kafka-producer-consumer.jar create myTest $KAFKABROKERS
    ```

1. Üreticiyi çalıştırmak ve konuya veri yazmak için aşağıdaki komutu kullanın:

    ```bash
    java -jar kafka-producer-consumer.jar producer myTest $KAFKABROKERS
    ```

1. Üretici tamamlandıktan sonra, konu başlığından okumak için aşağıdaki komutu kullanın:

    ```bash
    java -jar kafka-producer-consumer.jar consumer myTest $KAFKABROKERS
    ```

    Okunan kayıtlar, kayıt sayısıyla birlikte gösterilir.

1. Tüketiciden çıkış yapmak için __Ctrl + C__ tuşlarını kullanın.

### <a name="multiple-consumers"></a>Birden çok tüketici

Kafka tüketicileri kayıtları okurken bir tüketici grubu kullanır. Birden çok tüketiciyle aynı grubun kullanılması, konu başlığından yük dengeli okuma yapılmasına neden olur. Gruptaki her bir tüketici, kayıtların bir kısmını alır.

Tüketici uygulaması, grup kimliği olarak kullanılan bir parametre kabul eder Örneğin, aşağıdaki komut bir `myGroup` grup kimliği kullanarak tüketiciyi başlatır:

```bash
java -jar kafka-producer-consumer.jar consumer myTest $KAFKABROKERS myGroup
```

Tüketiciden çıkış yapmak için __Ctrl + C__ tuşlarını kullanın.

Bu işlemi uygulamada görmek için aşağıdaki komutu kullanın:

```bash
tmux new-session 'java -jar kafka-producer-consumer.jar consumer myTest $KAFKABROKERS myGroup' \
\; split-window -h 'java -jar kafka-producer-consumer.jar consumer myTest $KAFKABROKERS myGroup' \
\; attach
```

Bu komut, terminali iki sütuna bölmek için `tmux` kullanır. Her sütunda aynı grup kimliği değerine sahip bir tüketici başlatılır. Tüketici okumayı tamamladıktan sonra her birinin yalnızca kayıtların bir bölümünü okuduğuna dikkat edin. Çıkmak `tmux`için __Ctrl + C'yi__ iki kez kullanın.

Aynı gruptaki istemcilerin tüketimi, konu başlığının bölümleri aracılığıyla işlenir. Bu kod örneğinde, daha önce oluşturulan `test` konusunda sekiz bölüm vardır. Sekiz tüketici başlatırsanız, her tüketici konunun tek bir bölümünden kayıtları okur.

> [!IMPORTANT]  
> Bir tüketici grubunda bölümden daha fazla tüketici örneği olamaz. Bu örnekte, konu başlığındaki bölüm sayısı sekiz olduğu için bir tüketici grubu en fazla bu sayıda tüketici içerebilir. Ya da her biri en fazla sekiz tüketici içeren birden fazla tüketici grubunuz olabilir.

Kafka'da saklanan kayıtlar, bir bölüm içinde alındıkları sırayla saklanır. *Bir bölüm* içindeki kayıtlar için sıralı teslim sağlamak üzere, tüketici örneklerinin bölüm sayısıyla eşleştiği bir tüketici grubu oluşturun. *Konu başlığı içindeki* kayıtların sıralı teslim edilmesini sağlayabilmek için, yalnızca bir tüketici örneği içeren bir tüketici grubu oluşturun.

## <a name="clean-up-resources"></a>Kaynakları temizleme

Bu öğretici ile oluşturulan kaynakları temizlemek için kaynak grubunu silebilirsiniz. Kaynak grubunun silinmesi, ilişkili HDInsight kümesini ve kaynak grubuyla ilişkili diğer tüm kaynakları da siler.

Azure portalını kullanarak kaynak grubunu kaldırmak için:

1. Azure portalında sol taraftaki menüyü genişleterek hizmet menüsünü açın ve sonra __Kaynak Grupları__'nı seçerek kaynak gruplarınızın listesini görüntüleyin.
2. Silinecek kaynak grubunu bulun ve sonra listenin sağ tarafındaki __Daha fazla__ düğmesine (...) sağ tıklayın.
3. __Kaynak grubunu sil__'i seçip onaylayın.

## <a name="next-steps"></a>Sonraki adımlar

Bu belgede, HDInsight'ta Kafka ile Apache Kafka Üretici ve Tüketici API'sini kullanmayı öğrendiniz. Kafka ile çalışma hakkında daha fazla bilgi için aşağıdakileri kullanın:

* [Kafka REST Proxy kullanın](rest-proxy.md)
* [Apache Kafka günlüklerini çözümleme](apache-kafka-log-analytics-operations-management.md)
