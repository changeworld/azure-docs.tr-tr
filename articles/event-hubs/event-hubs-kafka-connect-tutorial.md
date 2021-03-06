---
title: Apache Kafka Connect- Azure Etkinlik Hub'ları ile tümleştir | Microsoft Dokümanlar
description: Bu makalede, Kafka için Azure Etkinlik Hub'ları ile Apache Spark'ın nasıl kullanılacağı hakkında bilgi verilmektedir.
services: event-hubs
documentationcenter: .net
author: ShubhaVijayasarathy
ms.service: event-hubs
ms.topic: how-to
ms.date: 04/02/2020
ms.author: shvija
ms.openlocfilehash: 60c6207b1c90ca40f02097a1c82d2811a50f664d
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/03/2020
ms.locfileid: "80632824"
---
# <a name="integrate-apache-kafka-connect-support-on-azure-event-hubs-preview"></a>Azure Event Hubs'a Apache Kafka Connect desteğiyle tümleştirme (Önizleme)
İş gereksinimleri için alma işlemleri arttıkça, çeşitli dış kaynaklar ve havuzlar için alma gereksinimi de artıyor. [Apache Kafka Connect](https://kafka.apache.org/documentation/#connect), Kafka kümesi aracılığıyla MySQL, HDFS ve dosya sistemi gibi herhangi bir dış sistemden/sisteme bağlanmak ve verileri içeri/dışarı aktarmak için böyle bir çerçeve sağlar. Bu öğretici, Kafka Connect çerçevelerini Etkinlik Hub'ları ile kullanarak size yol görebilirsiniz.

Bu öğretici, Kafka Connect'i bir etkinlik merkeziyle bütünleştirme ve temel FileStreamSource ve FileStreamSink konektörlerini dağıtma konusunda size yol açar. Bu özellik şu anda önizleme sürümündedir. Bu bağlayıcılar üretimde kullanıma yönelik olmasa da, Azure Event Hubs'ın bir Kafa aracısı işlevi üstlendiği uçtan uca bir Kafka Connect senaryosunu gösterirler.

> [!NOTE]
> Bu örnek [GitHub](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/connect)'da sağlanır.

Bu öğreticide, aşağıdaki adımları gerçekleştireceksiniz:

> [!div class="checklist"]
> * Event Hubs ad alanı oluşturma
> * Örnek projeyi kopyalama
> * Event Hubs için Kafka Connect'i yapılandırma
> * Kafka Connect'i çalıştırma
> * Bağlayıcıları oluşturma

## <a name="prerequisites"></a>Ön koşullar
Bu yol gösterici adımları tamamlamak için aşağıdaki önkoşulların karşılandığından emin olun:

- Azure aboneliği. Aboneliğiniz yoksa [ücretsiz bir hesap](https://azure.microsoft.com/free/) oluşturun.
- [Git](https://www.git-scm.com/downloads)
- Linux/MacOS
- Kafka sürümü (sürüm 1.1.1, Scala sürüm 2.11), [kafka.apache.org](https://kafka.apache.org/downloads#1.1.1)'da sağlanır
- [Apache Kafka için Event Hubs](https://docs.microsoft.com/azure/event-hubs/event-hubs-for-kafka-ecosystem-overview) giriş makalesini okuyun

## <a name="create-an-event-hubs-namespace"></a>Event Hubs ad alanı oluşturma
Herhangi bir Event Hubs hizmetinden göndermek ve almak için Event Hubs ad alanı gereklidir. Bkz. Ad alanı ve olay hub'ı oluşturmak için yönergeler için bir [olay hub'ı oluşturma.](event-hubs-create.md) Daha sonra kullanmak üzere Event Hubs bağlantı dizesini ve tam etki alanı adını (FQDN) alın. Yönergeler için bkz. [Event Hubs bağlantı dizesi alma](event-hubs-get-connection-string.md). 

## <a name="clone-the-example-project"></a>Örnek projeyi kopyalama
Azure Event Hubs deposunu kopyalayın ve tutorials/connect alt klasörüne gidin: 

```
git clone https://github.com/Azure/azure-event-hubs-for-kafka.git
cd azure-event-hubs-for-kafka/tutorials/connect
```

## <a name="configure-kafka-connect-for-event-hubs"></a>Event Hubs için Kafka Connect'i yapılandırma
Kafka Connect aktarım hızını Kafka'dan Event Hubs'a yeniden yönlendirmek için çok az yeniden yapılandırma gerekir.  Aşağıdaki `connect-distributed.properties` örneğinde, Event Hubs'da Kafka uç noktasıyla kimlik doğrulaması yapmak ve iletişim kurmak için Connect'in nasıl yapılandırılacağı gösterilir:

```properties
bootstrap.servers={YOUR.EVENTHUBS.FQDN}:9093 # e.g. namespace.servicebus.windows.net:9093
group.id=connect-cluster-group

# connect internal topic names, auto-created if not exists
config.storage.topic=connect-cluster-configs
offset.storage.topic=connect-cluster-offsets
status.storage.topic=connect-cluster-status

# internal topic replication factors - auto 3x replication in Azure Storage
config.storage.replication.factor=1
offset.storage.replication.factor=1
status.storage.replication.factor=1

rest.advertised.host.name=connect
offset.flush.interval.ms=10000

key.converter=org.apache.kafka.connect.json.JsonConverter
value.converter=org.apache.kafka.connect.json.JsonConverter
internal.key.converter=org.apache.kafka.connect.json.JsonConverter
internal.value.converter=org.apache.kafka.connect.json.JsonConverter

internal.key.converter.schemas.enable=false
internal.value.converter.schemas.enable=false

# required EH Kafka security settings
security.protocol=SASL_SSL
sasl.mechanism=PLAIN
sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required username="$ConnectionString" password="{YOUR.EVENTHUBS.CONNECTION.STRING}";

producer.security.protocol=SASL_SSL
producer.sasl.mechanism=PLAIN
producer.sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required username="$ConnectionString" password="{YOUR.EVENTHUBS.CONNECTION.STRING}";

consumer.security.protocol=SASL_SSL
consumer.sasl.mechanism=PLAIN
consumer.sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required username="$ConnectionString" password="{YOUR.EVENTHUBS.CONNECTION.STRING}";

plugin.path={KAFKA.DIRECTORY}/libs # path to the libs directory within the Kafka release
```

## <a name="run-kafka-connect"></a>Kafka Connect'i çalıştırma

Bu adımda, bir Kafka Connect çalışanı dağıtılmış modda yerel olarak başlatılır ve küme durumunu korumak için Event Hubs kullanılır.

1. Yukarıdaki `connect-distributed.properties` dosyasını yerel olarak kaydedin.  Küme ayracı içindeki tüm değerleri değiştirdiğinizden emin olun.
2. Makinenizde Kafka sürümünün konumuna gidin.
4. `./bin/connect-distributed.sh /PATH/TO/connect-distributed.properties` öğesini çalıştırın.  `'INFO Finished starting connectors and tasks'` iletisini gördüğünüzde Connect çalışanı REST API etkileşime hazır demektir. 

> [!NOTE]
> Kafka Connect, sıkıştırma da dahil olmak üzere önerilen yapılandırmalara sahip konuları otomatik olarak oluşturmak için Kafka AdminClient API'sini kullanır. Azure portalında ad alanına hızla göz attığınızda, Connect çalışanı iç konusunun otomatik olarak oluşturulduğu ortaya çıkar.
>
>Kafka Connect dahili konular **sıkıştırma kullanmalıdır.**  Dahili Connect konuları yanlış yapılandırılırsa, Olay Hub'ları ekibi hatalı yapılandırmaları düzeltmekten sorumlu değildir.

### <a name="create-connectors"></a>Bağlayıcıları oluşturma
Bu bölümde FileStreamSource ve FileStreamSink bağlayıcılarını çalıştırma işleminde yol gösterilir. 

1. Giriş ve çıkış veri dosyaları için bir dizin oluşturun.
    ```bash
    mkdir ~/connect-quickstart
    ```

2. İki dosya oluşturun: bir dosya FileStreamSource bağlayıcısının okuduğu çekirdek verileri için ve diğer dosya da FileStreamSink bağlayıcımızın yazması için.
    ```bash
    seq 1000 > ~/connect-quickstart/input.txt
    touch ~/connect-quickstart/output.txt
    ```

3. FileStreamSource bağlayıcısını oluşturun.  Küme ayraçlarının içindeki değeri giriş dizin yolunuzla değiştirmeyi unutmayın.
    ```bash
    curl -s -X POST -H "Content-Type: application/json" --data '{"name": "file-source","config": {"connector.class":"org.apache.kafka.connect.file.FileStreamSourceConnector","tasks.max":"1","topic":"connect-quickstart","file": "{YOUR/HOME/PATH}/connect-quickstart/input.txt"}}' http://localhost:8083/connectors
    ```
    Yukarıdaki komutu çalıştırdıktan sonra Event Hubs örneğinizde Event Hub `connect-quickstart` öğesini görmeniz gerekir.
4. Kaynak bağlayıcının durumunu denetleyin.
    ```bash
    curl -s http://localhost:8083/connectors/file-source/status
    ```
    İsteğe bağlı olarak, olayların `connect-quickstart` konusuna ulaştığını doğrulamak için [Service Bus Gezgini](https://github.com/paolosalvatori/ServiceBusExplorer/releases)'ni kullanabilirsiniz.

5. FileStreamSink Bağlayıcısını oluşturun.  Küme ayraçlarının içindeki değeri giriş dizin yolunuzla değiştirdiğinizden emin olun.
    ```bash
    curl -X POST -H "Content-Type: application/json" --data '{"name": "file-sink", "config": {"connector.class":"org.apache.kafka.connect.file.FileStreamSinkConnector", "tasks.max":"1", "topics":"connect-quickstart", "file": "{YOUR/HOME/PATH}/connect-quickstart/output.txt"}}' http://localhost:8083/connectors
    ```
 
6. Havuz bağlayıcısının durumunu denetleyin.
    ```bash
    curl -s http://localhost:8083/connectors/file-sink/status
    ```

7. Verilerin dosyalar arasında çoğaltıldığını ve her iki dosyada da aynı veriler bulunduğunu doğrulayın.
    ```bash
    # read the file
    cat ~/connect-quickstart/output.txt
    # diff the input and output files
    diff ~/connect-quickstart/input.txt ~/connect-quickstart/output.txt
    ```

### <a name="cleanup"></a>Temizleme
Kafka Connect, Connect kümesi devre dışı bırakıldıktan sonra bile kalıcı olan yapılandırmaları, uzaklıkları ve durumları depolamak için Event Hub konuları oluşturur. Bu kalıcılık istenen bir şey değilse, bu konuların silinmesi önerilir. Bu adımlar sırasında oluşturulan `connect-quickstart` Olay Hub'ını da silmek isteyebilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar

Kafka için Etkinlik Hub'ları hakkında daha fazla bilgi edinmek için aşağıdaki makalelere bakın:  

- [Bir olay hub'ında Kafka aracısı yansıtma](event-hubs-kafka-mirror-maker-tutorial.md)
- [Apache Spark'ı bir olay hub'ına bağlama](event-hubs-kafka-spark-tutorial.md)
- [Apache Flink'i bir olay hub'ına bağlama](event-hubs-kafka-flink-tutorial.md)
- [GitHub'ımızdaki örnekleri inceleme](https://github.com/Azure/azure-event-hubs-for-kafka)
- [Akka Akışlarını bir etkinlik merkezine bağlayın](event-hubs-kafka-akka-streams-tutorial.md)
- [Azure Etkinlik Hub'ları için Apache Kafka geliştirici kılavuzu](apache-kafka-developer-guide.md)
