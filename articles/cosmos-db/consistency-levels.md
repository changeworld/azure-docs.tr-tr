---
title: Azure Cosmos DB'de tutarlılık düzeyleri
description: Azure Cosmos DB, nihai tutarlılık, kullanılabilirlik ve gecikme gecikmesi dengelerini dengelemeye yardımcı olmak için beş tutarlılık düzeyine sahiptir.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/06/2020
ms.openlocfilehash: e5966f142ece32f148c56edb5b0ef5dfd88603aa
ms.sourcegitcommit: ea006cd8e62888271b2601d5ed4ec78fb40e8427
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/14/2020
ms.locfileid: "81380072"
---
# <a name="consistency-levels-in-azure-cosmos-db"></a>Azure Cosmos DB'de tutarlılık düzeyleri

Yüksek kullanılabilirlik, düşük gecikme süresi veya her ikisi için çoğaltmaya dayanan dağıtılmış veritabanları, okuma tutarlılığı ile kullanılabilirlik, gecikme süresi ve iş sonu arasındaki temel dengeyi sağlar. Ticari olarak kullanılabilen dağıtılmış veritabanlarının çoğu geliştiricilerden iki aşırı tutarlılık modeli arasında seçim yapmalarını ister: *güçlü* tutarlılık ve *nihai* tutarlılık. Güçlü tutarlılık modelinin doğrusallanabilirliği veri programlanabilirliğinin altın standardıdır. Ama daha yüksek yazma gecikme (sabit durumda) ve azaltılmış kullanılabilirlik (başarısızlıkları sırasında) bir fiyat ekler. Öte yandan, nihai tutarlılık daha yüksek kullanılabilirlik ve daha iyi performans sunar, ancak uygulamaları programlamayı zorlaştırır.

Azure Cosmos DB, veri tutarlılığını iki uç yerine seçenek yelpazesi olarak yaklaşır. Geliştiriciler, yüksek kullanılabilirlik ve performans açısından kesin seçimler ve ayrıntılı dengelemeler yapmak için bu seçenekleri kullanabilir.

Azure Cosmos DB ile geliştiriciler, tutarlılık spektrumundaki iyi tanımlanmış beş tutarlılık düzeyi arasından seçim yapabilir. Bu düzeyleri *güçlü*içerir , *bayatlık sınırlı*, *oturum*, *tutarlı önek*, ve *nihai* tutarlılık. Düzeyleri iyi tanımlanmış ve sezgisel ve belirli gerçek dünya senaryoları için kullanılabilir. Her seviye [kullanılabilirlik ve performans dengeleri](consistency-levels-tradeoffs.md) sağlar ve SLA'lar tarafından desteklenen. Aşağıdaki resim, spektrum olarak farklı tutarlılık düzeylerini gösterir.

![Spektrum olarak tutarlılık](./media/consistency-levels/five-consistency-levels.png)

Tutarlılık düzeyleri bölge agnostiktir ve okuma ve yazmaların sunulduğu bölge, Azure Cosmos hesabınızla ilişkili bölge sayısı veya hesabınızın tek veya birden çok yazma bölgesiyle yapılandırılıp yapılandırılmadığına bakılmaksızın tüm işlemler için garanti edilir.

## <a name="scope-of-the-read-consistency"></a>Okuma tutarlılığının kapsamı

Okuma tutarlılığı, mantıksal bir bölüm içinde kapsamlı tek bir okuma işlemi için geçerlidir. Okuma işlemi uzak bir istemci veya depolanan yordam tarafından verilebilir.

## <a name="configure-the-default-consistency-level"></a>Varsayılan tutarlılık düzeyini yapılandırma

Azure Cosmos hesabınızdaki varsayılan tutarlılık düzeyini istediğiniz zaman yapılandırabilirsiniz. Hesabınızda yapılandırılan varsayılan tutarlılık düzeyi, bu hesap altındaki tüm Azure Cosmos veritabanları ve kapsayıcılar için geçerlidir. Bir kapsayıcı veya veritabanına karşı verilen tüm okumalar ve sorgular varsayılan olarak belirtilen tutarlılık düzeyini kullanır. Daha fazla bilgi edinmek için varsayılan tutarlılık düzeyini nasıl [yapılandırılabildiğini](how-to-manage-consistency.md#configure-the-default-consistency-level)öğrenin.

## <a name="guarantees-associated-with-consistency-levels"></a>Tutarlılık düzeyleriyle ilişkili garantiler

Azure Cosmos DB tarafından sağlanan kapsamlı SLA'lar, okuma isteklerinin yüzde 100'ünün seçtiğiniz herhangi bir tutarlılık düzeyi için tutarlılık garantisini karşıladığını garanti eder. Tutarlılık düzeyiyle ilişkili tüm tutarlılık garantileri karşılanırsa, okuma isteği tutarlılık SLA'sını karşılar. Azure Cosmos DB'deki Beş tutarlılık düzeyinin tLA+ belirtim dilini kullanarak kesin tanımları [azure-cosmos-tla](https://github.com/Azure/azure-cosmos-tla) GitHub repo'sunda sağlanır.

Beş tutarlılık düzeyinin anlambilimi burada açıklanmıştır:

- **Güçlü**: Güçlü tutarlılık doğrusallanabilirlik garantisi sunar. Doğrusallanabilirlik aynı anda hizmet istekleri anlamına gelir. Okumaların, bir öğenin en son taahhüt edilen sürümünü döndürmesi garanti edilir. İstemci hiçbir zaman taahhütedilmemiş veya kısmi bir yazma görmez. Kullanıcılar her zaman en son taahhüt yazma okumak için garanti edilir.

  Aşağıdaki grafik, müzik notaları ile güçlü tutarlılık göstermektedir. Veriler "Batı ABD 2" bölgesine yazıldıktan sonra, diğer bölgelerden gelen verileri okuduğunuzda, en son değeri alırsınız:

  ![video](media/consistency-levels/strong-consistency.gif)

- **Sınırlı bayatlık**: Okumatutarlı önek garantisi onurlandırmak için garanti edilir. Okumalar, bir öğenin en fazla *"K"* sürümünde (diğer bir şekilde "güncelleştirmeler") veya *"T"* zaman aralığıyla yazmanın gerisinde kalabilir. Başka bir deyişle, sınırlı bayatlığı seçtiğinizde, "bayatlık" iki şekilde yapılandırılabilir:

- Maddenin sürüm sayısı *(K*)
- Okumaların yazmaların gerisinde kılabileceği zaman aralığı (*T*)

Sınırlı bayatlık "bayatlık penceresi dışında toplam küresel düzen sunuyor." Bir istemci yazma kabul eden bir bölge içinde okuma işlemleri gerçekleştirdiğinde, sınırlı bayatlık tutarlılığı tarafından sağlanan garantiler güçlü tutarlılık ile bu garantiler aynıdır.

Bayatlık penceresinin içinde, Sınırlı Bayatlık aşağıdaki tutarlılık garantilerini sağlar:

- Tek ana hesap için aynı bölgedeki istemciler için tutarlılık = Güçlü
- Tek ana hesap için farklı bölgelerdeki istemciler için tutarlılık = Tutarlı Önek
- Çok büyük bir hesap için tek bir bölgeye yazan istemciler için tutarlılık = Tutarlı Önek
- Çok yönlü bir hesap için farklı bölgelere yazan istemciler için tutarlılık = Nihai

  Sınırlı bayatlık genellikle düşük yazma gecikmeleri bekleyen ancak toplam küresel sipariş garantisi gerektiren küresel olarak dağıtılan uygulamalar tarafından seçilir. Sınırlı bayatlık grup işbirliği ve paylaşım, hisse senedi ticker, publish-subscribe/queueing vb içeren uygulamalar için harika. Aşağıdaki grafik, müzik notaları ile sınırlı bayatlık tutarlılığı göstermektedir. Veriler "Batı ABD 2" bölgesine yazıldıktan sonra, "Doğu ABD 2" ve "Avustralya Doğu" bölgeleri, yapılandırılan maksimum gecikme süresine veya maksimum işlemlere göre yazılı değeri okur:

  ![video](media/consistency-levels/bounded-staleness-consistency.gif)

- **Oturum**: Tek bir istemci oturumunda okumalar tutarlı önek, monoton okuma, monoton yazma, okuma-yazma ve yazma-takip-okuma garantilerini onurlandırmayı garanti eder. Bu, tek bir "yazar" oturumu veya birden çok yazar için oturum belirteci paylaşımı varsayar.

Oturum dışındaki istemciler aşağıdaki garantileri görür:

- Tek ana hesap için aynı bölgedeki istemciler için tutarlılık = Tutarlı Önek
- Tek ana hesap için farklı bölgelerdeki istemciler için tutarlılık = Tutarlı Önek
- Çok büyük bir hesap için tek bir bölgeye yazan istemciler için tutarlılık = Tutarlı Önek
- Çok büyük bir hesap için birden çok bölgeye yazan istemciler için tutarlılık = Nihai

  Oturum tutarlılığı, hem tek bölge hem de genel olarak dağıtılan uygulamalar için yaygın olarak kullanılan tutarlılık düzeyidir. Yazma gecikmeleri, kullanılabilirlik ve nihai tutarlılık ile karşılaştırılabilir okuma iş letme sağlar, ancak aynı zamanda bir kullanıcı bağlamında çalışması için yazılmış uygulamaların gereksinimlerine uygun tutarlılık garantileri sağlar. Aşağıdaki grafik, notalarla oturum tutarlılığını göstermektedir. "West US 2 yazar" ve "Batı ABD 2 okuyucu" aynı oturumu (Oturum A) bu yüzden her ikisi de aynı anda aynı verileri okumak kullanıyorsunuz. "Avustralya Doğu" bölgesi "Oturum B"yi bu nedenle kullanırken, daha sonra ancak yazdığı gibi aynı sırada veri alır.

  ![video](media/consistency-levels/session-consistency.gif)

- **Tutarlı önek**: Döndürülen güncelleştirmeler, boşluk olmadan tüm güncelleştirmelerin bazı önekisini içerir. Tutarlı önek tutarlılık düzeyi, okunan hiçbir zaman sıra dışı yazmaz garanti eder.

Yazma işlemlerinin `A, B, C` sırasında gerçekleştirilmesi durumunda istemci `A`, `A,B` veya `A,B,C` görebilir ancak `A,C` veya `B,A,C` gibi bozuk sıraları görmez. Tutarlı Önek, yazma gecikmeleri, kullanılabilirlik ve okuma iş ortamının nihai tutarlılıkla karşılaştırılabilir olmasını sağlar, ancak aynı zamanda siparişin önemli olduğu senaryoların gereksinimlerine uygun sipariş garantileri sağlar. 

Tutarlı Önek için tutarlılık garantileri aşağıda verilmiştir:

- Tek ana hesap için aynı bölgedeki istemciler için tutarlılık = Tutarlı Önek
- Tek ana hesap için farklı bölgelerdeki istemciler için tutarlılık = Tutarlı Önek
- Çok büyük bir hesap için tek bir bölgeye yazan istemciler için tutarlılık = Tutarlı Önek
- Çok büyük bir hesap için birden çok bölgeye yazan istemciler için tutarlılık = Nihai

Aşağıdaki grafik, müzik notalarıyla tutarlılık öneki tutarlılığını göstermektedir. Tüm bölgelerde, okumalar sıra dışı görmek asla yazıyor:

  ![video](media/consistency-levels/consistent-prefix.gif)

- **Eventual**: Okuma garantisi yoktur. Başka bir yazı yokluğunda, yinelemeler sonunda yakınsama.  
Bir istemci daha önce okuduğu değerlerden daha eski değerleri okuyabilir, çünkü nihai tutarlılık en zayıf şeklidir. Nihai tutarlılık, uygulamanın herhangi bir sipariş garantisi gerektirmediği durumlarda idealdir. Örnekler arasında Retweet, Beğenme veya iş parçacığı olmayan yorumların sayısı verilebilir. Aşağıdaki grafik, müzik notaları ile nihai tutarlılık göstermektedir.

  ![video](media/consistency-levels/eventual-consistency.gif)

## <a name="additional-reading"></a>Ek okuma

Tutarlılık kavramları hakkında daha fazla bilgi edinmek için aşağıdaki makaleleri okuyun:

- [Azure Cosmos DB tarafından sunulan beş tutarlılık düzeyi için üst düzey TLA+ belirtimleri](https://github.com/Azure/azure-cosmos-tla)
- [Çoğaltılmış Veri Tutarlılığı Beyzbol (video) Doug Terry tarafından Açıklanmıştır](https://www.youtube.com/watch?v=gluIh8zd26I)
- [Çoğaltılmış Veri Tutarlılığı Beyzbol (teknik inceleme) ile Doug Terry tarafından açıklanmıştır](https://www.microsoft.com/research/publication/replicated-data-consistency-explained-through-baseball/)
- [Zayıf tutarlı çoğaltılan veriler için oturum garantileri](https://dl.acm.org/citation.cfm?id=383631)
- [Modern Dağıtılmış Veritabanı Sistemleri Tasarımında Tutarlılık Tradeoffs: CAP Hikayenin Sadece Bir Parçasıdır](https://www.computer.org/csdl/magazine/co/2012/02/mco2012020037/13rRUxjyX7k)
- [Pratik Kısmi Çoğunluk için Olasılıklı Sınırlı Bayatlık (PBS)](https://vldb.org/pvldb/vol5/p776_peterbailis_vldb2012.pdf)
- [Sonunda Tutarlı - Revisited](https://www.allthingsdistributed.com/2008/12/eventually_consistent.html)

## <a name="next-steps"></a>Sonraki adımlar

Azure Cosmos DB'deki tutarlılık düzeyleri hakkında daha fazla bilgi edinmek için aşağıdaki makaleleri okuyun:

- [Uygulamanız için doğru tutarlılık düzeyini seçin](consistency-levels-choosing.md)
- [Azure Cosmos DB API'lerinde tutarlılık düzeyleri](consistency-levels-across-apis.md)
- [Çeşitli tutarlılık düzeyleri için kullanılabilirlik ve performans dengeleri](consistency-levels-tradeoffs.md)
- [Varsayılan tutarlılık düzeyini yapılandırma](how-to-manage-consistency.md#configure-the-default-consistency-level)
- [Varsayılan tutarlılık düzeyini geçersiz kılma](how-to-manage-consistency.md#override-the-default-consistency-level)
