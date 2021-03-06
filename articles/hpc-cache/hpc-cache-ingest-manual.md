---
title: Azure HPC Önbellek veri yutmak - manuel kopyalama
description: Azure HPC Önbelleğinde verileri Blob depolama hedefine taşımak için cp komutları nasıl kullanılır?
author: ekpgh
ms.service: hpc-cache
ms.topic: conceptual
ms.date: 10/30/2019
ms.author: rohogue
ms.openlocfilehash: fc397088e46f0d2b623080f3deed24c386e7d8b4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74168491"
---
# <a name="azure-hpc-cache-data-ingest---manual-copy-method"></a>Azure HPC Önbellek veri yutma - manuel kopyalama yöntemi

Bu makalede, Azure HPC Önbelleği ile kullanılmak üzere verileri blob depolama kabına el ile kopyalamak için ayrıntılı yönergeler veremiş. Kopyalama hızını optimize etmek için çok iş parçacığı paralel işlemleri kullanır.

Azure HPC Önbelleğiniz için verileri Blob depolama alanına taşıma hakkında daha fazla bilgi edinmek için [verileri Azure Blob depolama alanına taşı'nı](hpc-cache-ingest.md)okuyun.

## <a name="simple-copy-example"></a>Basit kopya örneği

Önceden tanımlanmış dosya veya yol kümelerine karşı arka planda aynı anda birden fazla kopya komutu çalıştırarak istemciüzerinde çok iş parçacığı kopyasını el ile oluşturabilirsiniz.

Linux/UNIX ``cp`` komutu, ``-p`` sahipliği ve mtime meta verilerini korumak için bağımsız değişkeni içerir. Aşağıdaki komutlara bu bağımsız değişkenin eklenmesi isteğe bağlıdır. (Bağımsız değişken ekleme, istemciden gönderilen dosya sistemi çağrılarının sayısını meta veri değişikliği için hedef dosya sistemine artırır.)

Bu basit örnek iki dosyayı paralel olarak kopyalar:

```bash
cp /mnt/source/file1 /mnt/destination1/ & cp /mnt/source/file2 /mnt/destination1/ &
```

Bu komutu verdikten `jobs` sonra, komut iki iş parçacığının çalıştığını gösterir.

## <a name="copy-data-with-predictable-file-names"></a>Öngörülebilir dosya adlarıyla verileri kopyalama

Dosya adlarınız öngörülebilirse, paralel kopyalama iş parçacıkları oluşturmak için ifadeleri kullanabilirsiniz. 

Örneğin, dizininiz sırayla `0001` numaralandırılan 1000 dosya `1000`içeriyorsa, her biri 100 dosyayı kopyalayan on paralel iş parçacığı oluşturmak için aşağıdaki ifadeleri kullanabilirsiniz:

```bash
cp /mnt/source/file0* /mnt/destination1/ & \
cp /mnt/source/file1* /mnt/destination1/ & \
cp /mnt/source/file2* /mnt/destination1/ & \
cp /mnt/source/file3* /mnt/destination1/ & \
cp /mnt/source/file4* /mnt/destination1/ & \
cp /mnt/source/file5* /mnt/destination1/ & \
cp /mnt/source/file6* /mnt/destination1/ & \
cp /mnt/source/file7* /mnt/destination1/ & \
cp /mnt/source/file8* /mnt/destination1/ & \
cp /mnt/source/file9* /mnt/destination1/
```

## <a name="copy-data-with-unstructured-file-names"></a>Yapılandırılmamış dosya adlarıyla verileri kopyalama

Dosya adlandırma yapınız öngörülebilir değilse, dosyaları dizin adlarıyla gruplandırma yapabilirsiniz. 

Bu örnek, arka plan görevleri ``cp`` olarak çalıştırılan komutlara göndermek için tüm dizinleri toplar:

```bash
/root
|-/dir1
| |-/dir1a
| |-/dir1b
| |-/dir1c
   |-/dir1c1
|-/dir1d
```

Dosyalar toplandıktan sonra, alt dizinleri ve tüm içeriğini özyinelemeli olarak kopyalamak için paralel kopyalama komutları çalıştırabilirsiniz:

```bash
cp /mnt/source/* /mnt/destination/
mkdir -p /mnt/destination/dir1 && cp /mnt/source/dir1/* mnt/destination/dir1/ & 
cp -R /mnt/source/dir1/dir1a /mnt/destination/dir1/ & 
cp -R /mnt/source/dir1/dir1b /mnt/destination/dir1/ & 
cp -R /mnt/source/dir1/dir1c /mnt/destination/dir1/ & # this command copies dir1c1 via recursion
cp -R /mnt/source/dir1/dir1d /mnt/destination/dir1/ &
```

## <a name="when-to-add-mount-points"></a>Montaj noktaları ne zaman eklenir?

Tek bir hedef dosya sistemi montaj noktasına doğru giden yeterli paralel iş parçacığı na sahip olduktan sonra, daha fazla iş parçacığı eklemenin daha fazla iş parçacığı vermediği bir nokta olacaktır. (İşlem, veri türünüze bağlı olarak dosya/saniye veya bayt/saniye olarak ölçülecektir.) Veya daha kötüsü, aşırı iş parçacığı bazen bir iş çıkarma bozulmasına neden olabilir.  

Bu durumda, aynı uzak dosya sistemi montaj yolunu kullanarak diğer Azure HPC Önbellek montaj adreslerine istemci tarafı montaj noktaları ekleyebilirsiniz:

```bash
10.1.0.100:/nfs on /mnt/sourcetype nfs (rw,vers=3,proto=tcp,addr=10.1.0.100)
10.1.1.101:/nfs on /mnt/destination1type nfs (rw,vers=3,proto=tcp,addr=10.1.1.101)
10.1.1.102:/nfs on /mnt/destination2type nfs (rw,vers=3,proto=tcp,addr=10.1.1.102)
10.1.1.103:/nfs on /mnt/destination3type nfs (rw,vers=3,proto=tcp,addr=10.1.1.103)
```

İstemci tarafı montaj noktaları eklemek, ek `/mnt/destination[1-3]` montaj noktalarına ek kopyalama komutları atarak daha fazla paralellik elde etmenizi sağlar.  

Örneğin, dosyalarınız çok büyükse, farklı hedef yolları kullanmak için kopyalama komutlarını tanımlayabilir ve kopyayı gerçekleştiren istemciden paralel olarak daha fazla komut gönderebilirsiniz.

```bash
cp /mnt/source/file0* /mnt/destination1/ & \
cp /mnt/source/file1* /mnt/destination2/ & \
cp /mnt/source/file2* /mnt/destination3/ & \
cp /mnt/source/file3* /mnt/destination1/ & \
cp /mnt/source/file4* /mnt/destination2/ & \
cp /mnt/source/file5* /mnt/destination3/ & \
cp /mnt/source/file6* /mnt/destination1/ & \
cp /mnt/source/file7* /mnt/destination2/ & \
cp /mnt/source/file8* /mnt/destination3/ & \
```

Yukarıdaki örnekte, üç hedef montaj noktası da istemci dosya kopyalama işlemleri tarafından hedeflenmektedir.

## <a name="when-to-add-clients"></a>İstemci ne zaman eklenir?

Son olarak, istemcinin yeteneklerine ulaştığınızda, daha fazla kopya iş parçacığı veya ek montaj noktaları eklemek ek dosya/sn veya bayt/sn artışı oluşturmaz. Bu durumda, kendi dosya kopyalama işlemleri kümelerini çalıştıracak aynı montaj noktaları kümesine sahip başka bir istemci dağıtabilirsiniz. 

Örnek:

```bash
Client1: cp -R /mnt/source/dir1/dir1a /mnt/destination/dir1/ &
Client1: cp -R /mnt/source/dir2/dir2a /mnt/destination/dir2/ &
Client1: cp -R /mnt/source/dir3/dir3a /mnt/destination/dir3/ &

Client2: cp -R /mnt/source/dir1/dir1b /mnt/destination/dir1/ &
Client2: cp -R /mnt/source/dir2/dir2b /mnt/destination/dir2/ &
Client2: cp -R /mnt/source/dir3/dir3b /mnt/destination/dir3/ &

Client3: cp -R /mnt/source/dir1/dir1c /mnt/destination/dir1/ &
Client3: cp -R /mnt/source/dir2/dir2c /mnt/destination/dir2/ &
Client3: cp -R /mnt/source/dir3/dir3c /mnt/destination/dir3/ &

Client4: cp -R /mnt/source/dir1/dir1d /mnt/destination/dir1/ &
Client4: cp -R /mnt/source/dir2/dir2d /mnt/destination/dir2/ &
Client4: cp -R /mnt/source/dir3/dir3d /mnt/destination/dir3/ &
```

## <a name="create-file-manifests"></a>Dosya bildirimleri oluşturma

Yukarıdaki yaklaşımları anladıktan sonra (hedef başına birden çok kopya iş parçacığı, istemci başına birden çok hedef, ağ tarafından erişilebilen kaynak dosya sistemi başına birden çok istemci), şu öneriyi göz önünde bulundurun: Dosya bildirimleri oluşturun ve bunları kopyayla kullanın birden çok istemci arasında komutları.

Bu senaryo, dosyaların ``find`` veya dizinlerin bildirimlerini oluşturmak için UNIX komutunu kullanır:

```bash
user@build:/mnt/source > find . -mindepth 4 -maxdepth 4 -type d
./atj5b55c53be6-01/support/gsi/2018-07-22T21:12:06EDT
./atj5b55c53be6-01/support/pcap/2018-07-23T01:34:57UTC
./atj5b55c53be6-01/support/trace/rolling
./atj5b55c53be6-03/support/gsi/2018-07-22T21:12:06EDT
./atj5b55c53be6-03/support/pcap/2018-07-23T01:34:57UTC
./atj5b55c53be6-03/support/trace/rolling
./atj5b55c53be6-02/support/gsi/2018-07-22T21:12:06EDT
./atj5b55c53be6-02/support/pcap/2018-07-23T01:34:57UTC
./atj5b55c53be6-02/support/trace/rolling
```

Bu sonucu bir dosyaya yönlendirin:`find . -mindepth 4 -maxdepth 4 -type d > /tmp/foo`

Ardından dosyaları saymak ve alt dizinlerin boyutlarını belirlemek için BASH komutlarını kullanarak manifestoyu yineleyebilirsiniz:

```bash
ben@xlcycl1:/sps/internal/atj5b5ab44b7f > for i in $(cat /tmp/foo); do echo " `find ${i} |wc -l`    `du -sh ${i}`"; done
244    3.5M    ./atj5b5ab44b7f-02/support/gsi/2018-07-18T00:07:03EDT
9      172K    ./atj5b5ab44b7f-02/support/gsi/stats_2018-07-18T05:01:00UTC
124    5.8M    ./atj5b5ab44b7f-02/support/gsi/stats_2018-07-19T01:01:01UTC
152    15M     ./atj5b5ab44b7f-02/support/gsi/stats_2018-07-20T01:01:00UTC
131    13M     ./atj5b5ab44b7f-02/support/gsi/stats_2018-07-20T21:59:41UTC_partial
789    6.2M    ./atj5b5ab44b7f-02/support/gsi/2018-07-20T21:59:41UTC
134    12M     ./atj5b5ab44b7f-02/support/gsi/stats_2018-07-20T22:22:55UTC_hpccache_catchup
7      16K     ./atj5b5ab44b7f-02/support/pcap/2018-07-18T17:12:19UTC
8      83K     ./atj5b5ab44b7f-02/support/pcap/2018-07-18T17:17:17UTC
575    7.7M    ./atj5b5ab44b7f-02/support/cores/armada_main.2000.1531980253.gsi
33     4.4G    ./atj5b5ab44b7f-02/support/trace/rolling
281    6.6M    ./atj5b5ab44b7f-01/support/gsi/2018-07-18T00:07:03EDT
15     182K    ./atj5b5ab44b7f-01/support/gsi/stats_2018-07-18T05:01:00UTC
244    17M     ./atj5b5ab44b7f-01/support/gsi/stats_2018-07-19T01:01:01UTC
299    31M     ./atj5b5ab44b7f-01/support/gsi/stats_2018-07-20T01:01:00UTC
256    29M     ./atj5b5ab44b7f-01/support/gsi/stats_2018-07-20T21:59:41UTC_partial
889    7.7M    ./atj5b5ab44b7f-01/support/gsi/2018-07-20T21:59:41UTC
262    29M     ./atj5b5ab44b7f-01/support/gsi/stats_2018-07-20T22:22:55UTC_hpccache_catchup
11     248K    ./atj5b5ab44b7f-01/support/pcap/2018-07-18T17:12:19UTC
11     88K     ./atj5b5ab44b7f-01/support/pcap/2018-07-18T17:17:17UTC
645    11M     ./atj5b5ab44b7f-01/support/cores/armada_main.2019.1531980253.gsi
33     4.0G    ./atj5b5ab44b7f-01/support/trace/rolling
244    2.1M    ./atj5b5ab44b7f-03/support/gsi/2018-07-18T00:07:03EDT
9      158K    ./atj5b5ab44b7f-03/support/gsi/stats_2018-07-18T05:01:00UTC
124    5.3M    ./atj5b5ab44b7f-03/support/gsi/stats_2018-07-19T01:01:01UTC
152    15M     ./atj5b5ab44b7f-03/support/gsi/stats_2018-07-20T01:01:00UTC
131    12M     ./atj5b5ab44b7f-03/support/gsi/stats_2018-07-20T21:59:41UTC_partial
789    8.4M    ./atj5b5ab44b7f-03/support/gsi/2018-07-20T21:59:41UTC
134    14M     ./atj5b5ab44b7f-03/support/gsi/stats_2018-07-20T22:25:58UTC_hpccache_catchup
7      159K    ./atj5b5ab44b7f-03/support/pcap/2018-07-18T17:12:19UTC
7      157K    ./atj5b5ab44b7f-03/support/pcap/2018-07-18T17:17:17UTC
576    12M     ./atj5b5ab44b7f-03/support/cores/armada_main.2013.1531980253.gsi
33     2.8G    ./atj5b5ab44b7f-03/support/trace/rolling
```

Son olarak, istemcilere gerçek dosya kopyalama komutları zanaat gerekir.  

Dört istemciniz varsa, şu komutu kullanın:

```bash
for i in 1 2 3 4 ; do sed -n ${i}~4p /tmp/foo > /tmp/client${i}; done
```

Beş istemciniz varsa, şuna benzer bir şey kullanın:

```bash
for i in 1 2 3 4 5; do sed -n ${i}~5p /tmp/foo > /tmp/client${i}; done
```

Ve altı için .... Gerektiği gibi tahmin edin.

```bash
for i in 1 2 3 4 5 6; do sed -n ${i}~6p /tmp/foo > /tmp/client${i}; done
```

Komuttan çıktının bir parçası olarak elde edilen düzey dört dizinlerine yol adlarına sahip *N* istemcilerinizin her biri için bir tane olan N sonuçlanan dosyaları alırsınız. *N* `find` 

Kopyalama komutunu oluşturmak için her dosyayı kullanın:

```bash
for i in 1 2 3 4 5 6; do for j in $(cat /tmp/client${i}); do echo "cp -p -R /mnt/source/${j} /mnt/destination/${j}" >> /tmp/client${i}_copy_commands ; done; done
```

Yukarıdaki n *dosyaları,* her satır başına bir kopya komutu ile, istemci üzerinde bir BASH komut dosyası olarak çalıştırılabilir verecektir. 

Amaç, birden çok istemcide paralel olarak istemci başına aynı anda bu komut birden çok komut iş parçacığı çalıştırmaktır.
