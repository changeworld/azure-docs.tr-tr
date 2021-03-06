---
title: 'Hızlı başlangıç: Veri bilimi deneyi oluşturma'
titleSuffix: ML Studio (classic) - Azure
description: Bu makine öğrenme quickstart kolay bir veri bilimi deneyi ile size yol. Regresyon algoritması kullanarak bir arabanın fiyatını tahmin edeceğiz.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: quickstart
author: likebupt
ms.author: keli19
ms.custom: seodec18
ms.date: 02/06/2019
ms.openlocfilehash: c4c50ba53bc40ad6ae6fc60b3992f9ab992eb268
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/26/2020
ms.locfileid: "79204571"
---
# <a name="quickstart-create-your-first-data-science-experiment-in-azure-machine-learning-studio-classic"></a>Quickstart: Azure Machine Learning Studio'da ilk veri bilimi denemenizi oluşturun (klasik)

[!INCLUDE [Notebook deprecation notice](../../../includes/aml-studio-notebook-notice.md)]

[!INCLUDE [Designer notice](../../../includes/designer-notice.md)]

Bu hızlı başlangıçta, Azure Machine [Learning Studio'da (klasik)](what-is-ml-studio.md) bir otomobilin fiyatını, yapma ve teknik özellikler gibi farklı değişkenlere göre tahmin eden bir makine öğrenme deneyi oluşturursunuz.

Makine öğreniminde yeniyseniz, [Yeni Başlayanlar için Data Science](data-science-for-beginners-the-5-questions-data-science-answers.md) adlı video serisi, günlük dil ve kavramları kullanarak makine öğrenimine harika bir giriştir.

Bu hızlı başlatma, bir deneme için varsayılan iş akışını izler:

1. **Bir model oluşturma**
    - [Verileri alma]
    - [Verileri hazırlama]
    - [Özellikleri tanımlama]
1. **Modeli eğitme**
    - [Bir algoritma seçin ve uygulayın]
1. **Modeli puanlama ve test etme**
    - [Yeni otomobil fiyatlarını tahmin edin]

[Verileri alma]: #get-the-data
[Verileri hazırlama]: #prepare-the-data
[Özellikleri tanımlama]: #define-features
[Bir algoritma seçin ve uygulayın]: #choose-and-apply-an-algorithm
[Yeni otomobil fiyatlarını tahmin edin]: #predict-new-automobile-prices

## <a name="get-the-data"></a>Verileri alma

Makine öğreniminde ihtiyacınız olan ilk şey veridir.
Studio (klasik) ile birlikte kullanabileceğiniz veya birçok kaynaktan veri içe aktarabileceğiniz birkaç örnek veri kümesi vardır. Bu örnekte, **Otomobil fiyat verileri (Ham)** adlı çalışma alanınıza dahil edilmiş örnek veri kümesini kullanacağız.
Bu veri kümesi; marka, model, teknik belirtimler ve fiyat gibi bilgiler dahil olmak üzere birçok ayrı otomobil için giriş içerir.

> [!TIP]
> Aşağıdaki denemenin çalışan bir kopyasını [Azure AI Gallery](https://gallery.azure.ai)’de bulabilirsiniz. İlk **[veri bilimi deneyinize](https://gallery.azure.ai/Experiment/Your-first-data-science-experiment-Automobile-price-prediction-1)** gidin - Otomobil fiyat tahmini ve Machine Learning Studio (klasik) çalışma alanınıza denemenin bir kopyasını indirmek için **Studio'da Aç'ı** tıklatın.

Veri kümesini denemenize aşağıdaki gibi aktarabilirsiniz.

1. Machine Learning Studio (klasik) penceresinin altındaki **+Yenİ'yi** tıklatarak yeni bir deneme oluşturun. DENEY**Boş Deneme'yi** **EXPERIMENT** >  seçin.

1. Denemenize tuvalin üzerinde görebileceğiniz bir varsayılan ad verilir. Bu adı seçerek anlamlı bir adla değiştirin, örneğin, **Otomobil fiyat tahmini**. Adın benzersiz olması gerekmez.

    ![Denemeyi yeniden adlandırma](./media/create-experiment/rename-experiment.png)

1. Deneme tuvalinin sol tarafında bir veri kümesi ve modül paleti bulunur. **Otomobil fiyat verileri (Ham)** etiketli veri kümesini bulmak için bu paletin en üst kısmındaki Arama kutusuna **otomobil** yazın. Bu veri kümesini deneme tuvaline sürükleyin.

    ![Otomobil veri kümesini bulun ve deneme tuvaline sürükleyin](./media/create-experiment/type-automobile.png)

Bu verilerin neye benzediğini görmek için otomobil veri kümesinin altındaki çıkış bağlantı noktasını tıklatın ve ardından **Visualize'ı**seçin.

![Çıkış bağlantı noktasına tıklayın ve “Görselleştir”i seçin](./media/create-experiment/select-visualize.png)

> [!TIP]
> Veri kümeleri ve modülleri küçük dairelerle gösterilen giriş ve çıkış bağlantı noktalarına sahiptir. Giriş bağlantı noktaları yukarıda, çıkış bağlantı noktaları aşağıdadır.
Denemeniz üzerinden veri akışı oluşturmak için bir modülün çıkış bağlantı noktasını bir diğerinin giriş bağlantı noktasına bağlayacaksınız.
Herhangi bir zamanda bir veri kümesi veya modülün çıkış bağlantı noktasına tıklayarak, veri akışında bu noktadaki verileri görebilirsiniz.

Bu veri kümesinde, her satır bir otomobili temsil eder ve her otomobille ilişkili değişkenler sütun olarak görünür. Belirli bir otomobil için değişkenleri kullanarak fiyatı aşırı sağ sütunda ("fiyat" başlıklı sütun 26) tahmin edeceğiz.

![Veri görselleştirme penceresinde otomobil verilerini görüntüleme](./media/create-experiment/visualize-auto-data.png)

Sağ üst köşedeki "**x**" işaretine tıklayarak görselleştirme penceresini kapatın.

## <a name="prepare-the-data"></a>Verileri hazırlama

Genellikle bir veri kümesi analiz edilmeden önce biraz ön işleme gerekir. Çeşitli satırların sütunlarında bulunan eksik değerleri fark etmiş olabilirsiniz. Modelin verileri doğru şekilde analiz edebilmesi için bu eksik değerlerin temizlenmesi gerekir. Eksik değerlere sahip satırları kaldırırız. Ayrıca, **normalleştirilmiş kayıplar** sütununun büyük kısmı eksik değerlere sahiptir; bu nedenle bu sütunu modelin tamamen dışında bırakacağız.

> [!TIP]
> Giriş verilerinden eksik değerleri temizleme, çoğu modülü kullanmanın bir önkoşuludur.

İlk olarak, **normalize-kayıplar** sütunundan tamamen kaldıran bir modül ekliyoruz. Daha sonra eksik veri olan satırları kaldıran başka bir modül ekliyoruz.

1. Dataset modülünde [Sütunları Seç'i][select-columns] bulmak için modül paletinin üst kısmındaki arama kutusuna **sütun** seçin. Sonra deney tuvaline sürükleyin. Bu modül, modele hangi veri sütunlarını dahil etmek veya dışarıda bırakmak istediğimizi seçmemizi sağlar.

1. **Otomobil fiyat verilerinin (Ham)** veri kümesinin çıkış bağlantı noktasını Dataset'teki Select Columns'un giriş noktasına bağlayın.

    !["Veri Kümesindeki Sütunları Seçme" modülünü deneme tuvaline ekleyin ve bağlayın](./media/create-experiment/type-select-columns.png)

1. [Select Columns in Dataset (Veri Kümesinde Sütun Seçme)][select-columns] modülüne tıklayın ve **Özellikler** bölmesinde **Sütun seçiciyi başlat** seçeneğine tıklayın.

   - Sol tarafta **Kurallar ile**’ye tıklayın
   - **Şununla Başla** altında **Tüm sütunlar**’a tıklayın. Bu [kurallar, Veri Kümesi'ndeki Sütunları Seç][select-columns] sütunlarını tüm sütunlardan geçmesi için yönlendirir (hariç tutmak üzere olduğumuz sütunlar hariç).
   - Açılan menülerden **Hariç Tut** ve **sütun adlarını** seçerek metin kutusuna tıklayın. Sütun listesi görüntülenir. **Normalleştirilmiş kayıplar**’ı seçin; böylece metin kutusuna eklenir.
   - Sütun seçiciyi kapatmak için onay işareti (Tamam) düğmesini tıklatın (sağ altta).

     ![Sütun seçiciyi başlatın ve "normalized-losses" sütununu hariç tutun](./media/create-experiment/launch-column-selector.png)

     Artık **Select Columns in Dataset (Veri Kümesinde Sütun Seçme)** için özellikler bölmesi, **normalleştirilmiş kayıplar** dışındaki tüm veri kümelerindeki tüm sütunlardan geçeceğini belirtir.

     ![Özellikler bölmesinde "normalized-losses" sütununun hariç tutulduğu gösterilir](./media/create-experiment/showing-excluded-column.png)

     > [!TIP] 
     > Modüle çift tıklayıp metin girerek bir modüle yorum ekleyebilirsiniz. Bu, modülün denemenizde ne işe yaradığını bir bakışta görmenize yardımcı olabilir. Bu durumda [Dataset modülünde Sütunları Seç'i][select-columns] çift tıklatın ve "Normalleştirilmiş kayıpları hariç tut" yorumunu yazın.

     ![Açıklama eklemek için bir modüle çift tıklayın](./media/create-experiment/add-comment.png)

1. [Eksik Verileri Temizleme][clean-missing-data] modülünü deneme tuvaline sürükleyin ve bunu [Veri Kümesindeki Sütunları Seçme][select-columns] modülüne bağlayın. **Özellikler** bölmesinde, **Temizleme modu** altında **Tüm satırı kaldır**’ı seçin. Bu seçenekler, eksik değerlere sahip satırları kaldırarak verileri temizlemek için [Eksik Verileri Temizle'yi][clean-missing-data] yönlendirir. Modüle çift tıklayın ve "Eksik değerli satırları kaldır" yorumunu yazın.

    !["Eksik Verileri Temizleme" için temizleme modunu "Tüm satırı kaldır" olarak ayarlayın](./media/create-experiment/set-remove-entire-row.png)

1. Sayfanın en altında yer alan **ÇALIŞTIR**'a tıklayarak denemeyi çalıştırın.

    Deneme çalıştırma bittiğinde, tüm modüllerin başarıyla tamamlandığını göstermek için yeşil bir onay işareti bulunur. Sağ üst köşede **Çalıştırma tamamlandı** durumunun olduğuna da dikkat edin.

    ![Çalıştırdıktan sonra, deneme aşağıdakine benzer görünmelidir](./media/create-experiment/early-experiment-run.png)

> [!TIP]
> Denemeyi neden şimdi çalıştırdık? Deneme çalıştırılarak, verilerimizin sütun tanımları [Veri Kümesindeki Sütunları Seçme][select-columns] modülü ve [Eksik Verileri Temizleme][clean-missing-data] modülü aracılığıyla veri kümesinden geçer. Bu, [Eksik Verileri Temizleme][clean-missing-data] öğesine bağladığımız modüllerin de aynı bilgilere sahip olacağı anlamına gelir.

Şimdi elimizde temiz veriler var. Temizlenen veri kümesini görüntülemek istiyorsanız [Eksik Verileri Temizleme][clean-missing-data] modülünün sol çıkış bağlantı noktasına tıklayın ve **Görselleştir**'i seçin. **Normalleştirilmiş kayıplar** sütununun artık dahil olmadığına ve eksik değerlerin yok olduğuna dikkat edin.

Artık veriler temizlendiğine göre, tahmine dayalı modelde hangi özellikleri kullanacağımızı belirtmeye hazırız.

## <a name="define-features"></a>Özellikleri tanımlama

Makine öğreniminde *özellikler,* ilgilendiğiniz bir şeyin ölçülebilir özellikleridir. Veri kümemizde her bir satır bir otomobili temsil eder ve her bir sütun da bu otomobilin bir özelliğidir.

Tahmine dayalı bir model oluşturmaya yönelik iyi bir özellikler kümesi bulmak için, deneme ve çözmek istediğiniz sorun hakkında bilgi gerekir. Bazı özellikler, hedefi tahmin etmede diğerlerinden daha uygundur. Bazı özellikler diğer özelliklerle güçlü bir korelasyona sahiptir ve kaldırılabilir. Örneğin, city-mpg ve highway-mpg yakından ilişkilidir, bu nedenle tahmini önemli ölçüde etkilemeden birini tutabilir ve diğerini kaldırabiliriz.

Veri kümemizdeki bir alt özellikler kümesini kullanan bir model oluşturalım. Daha sonra geri dönüp farklı özellikler seçebilir, denemeyi tekrar çalıştırabilir ve daha iyi sonuçlar elde edip etmeyeceğinizi görebilirsiniz. Ancak başlarken aşağıdaki özellikleri deneyelim:

    make, body-style, wheel-base, engine-size, horsepower, peak-rpm, highway-mpg, price

1. Deneme tuvaline başka bir [Veri Kümesindeki Sütunları Seçme][select-columns] modülü sürükleyin. [Eksik Verileri Temizleme][clean-missing-data] modülünün sol çıkış bağlantı noktasını [Veri Kümesindeki Sütunları Seçme][select-columns] modülünün girişine bağlayın.

    !["Veri Kümesindeki Sütunları Seçme" modülünü "Eksik Verileri Temizleme" modülüne bağlayın](./media/create-experiment/connect-clean-to-select.png)

1. Modüle çift tıklayın ve "Tahmin için özellik seç" yazın.

1. **Properties (Özellikler)** bölmesindeki **Launch column selector (Sütun seçiciyi başlat)** seçeneğine tıklayın.

1. **Kurallar ile**’ye tıklayın.

1. **Şununla Başla** altında **Sütun yok**’a tıklayın. Filtre satırında, **Dahil et** ve **sütun adları** öğelerini seçin ve metin kutusunda sütun adları listemizi seçin. Bu filtre, modülü belirttiğimiz sütunlar dışında herhangi bir sütundan (özellik) geçmemeye yönlendirir.

1. Onay işareti (Tamam) düğmesine tıklayın.

    ![Tahmine dahil edilecek sütunları (özellikleri) seçin](./media/create-experiment/select-columns-to-include.png)

Bu modül, yalnızca bir sonraki adımda kullanacağımız öğrenme algoritmasına aktarmak istediğimiz özellikleri içeren filtrelenmiş bir veri kümesi üretir. Daha sonra geri dönüp farklı özellikler seçerek yeniden deneyebilirsiniz.

## <a name="choose-and-apply-an-algorithm"></a>Bir algoritma seçin ve uygulayın

Artık veriler hazır olduğuna göre, tahmine dayalı bir model oluşturmak için eğitim ve test etme gerekir. Modeli eğitmek ve sonra fiyatları tahmin etmeye ne kadar yaklaştığını görmek üzere modeli test etmek için verilerimizi kullanacağız.
<!-- For now, don't worry about *why* we need to train and then test a model.-->

*Sınıflandırma* ve *regresyon*, denetimli makine öğrenimi algoritmasının iki türüdür. Sınıflandırma; renk gibi (kırmızı, mavi veya yeşil) tanımlanmış bir kategori kümesinden yanıt tahmin eder. Bir sayıyı tahmin etmek için regresyon kullanılır.

Bir sayı olan fiyatı tahmin etmek istediğimizden bir regresyon algoritması kullanırız. Bu örnekiçin, doğrusal bir *regresyon* modeli kullanacağız.


Modele fiyatı da içeren bir veri kümesi vererek modeli eğitiriz. Model verileri tarar ve otomobilin özellikleri ile fiyatı arasındaki bağlantıları arar. Daha sonra modeli test edeceğiz. Bildiğimiz otomobiller için bir özellik kümesi vererek modelin bilinen fiyatı tahmin etmeye ne kadar yaklaştığını göreceğiz.

Verilerimizi modeli eğitmek ve verileri ayrı eğitim ve test kümelerine ayırarak modeli test etmek için kullanırız.

1. [Verileri Bölme][split] modülünü seçip deneme tuvaline sürükleyin ve bunu son [Veri Kümesindeki Sütunları Seçme][select-columns] modülüne bağlayın.

1. Seçmek için [Verileri Bölme][split] modülüne tıklayın. **İlk çıkış veri kümesinde satır kesiri**’ni bulun (tuvalin sağ tarafında **Özellikler** bölmesinde) ve 0,75 olarak ayarlayın. Bu şekilde, modeli eğitmek için verilerin yüzde 75'ini kullanıp test etmek için yüzde 25'ini ayıracağız.

    !["Verileri Bölme" modülünün bölüm kesirini 0,75 olarak ayarlayın](./media/create-experiment/set-split-data-percentage.png)

    > [!TIP]
    > **Rastgele doldurma** parametresini değiştirerek eğitim ve test etme için farklı rastgele örnekler oluşturabilirsiniz. Bu parametre, sözde rastgele sayı üreticisinin doldurulmasını denetler.

1. Denemeyi çalıştırın. Deneme çalıştırıldığında [Veri Kümesindeki Sütunları Seçme][select-columns] ve [Verileri Bölme][split] modüllerinin sütun tanımlarını sonra ekleyeceğimiz modüllere geçirmesine olanak sağlanır.  

1. Öğrenme algoritmasını seçmek için, tuvalin solundaki modül paletindeki **Machine Learning** kategorisini genişletin ve ardından **Modeli Başlat**'ı genişletin. Böylece makine öğrenimi algoritmalarını başlatmak için kullanılabilecek çeşitli modül kategorileri görüntülenir. Bu deneme için, **Regresyon** kategorisinin altında [Doğrusal Regresyon][linear-regression] modülünü seçin ve bunu deneme tuvaline sürükleyin. (Modülü bulmak için palet Arama kutusuna “doğrusal regresyon” da yazabilirsiniz.)

1. [Modeli Eğitme][train-model] modülünü bulup deneme tuvaline sürükleyin. [Doğrusal Regresyon][linear-regression] modülünün çıkışını [Modeli Eğitme][train-model] modülünün sol girişine bağlayın ve [Verileri Bölme][split] modülünün eğitim verileri çıkışını (sol bağlantı noktası) [Modeli Eğitme][train-model] modülünün sağ girişine bağlayın.

    !["Modeli Eğitme" modülünü "Çizgisel Regresyon" ve "Verileri Bölme" modüllerine bağlayın](./media/create-experiment/connect-train-model.png)

1. [Modeli Eğitme][train-model] modülüne tıklayın, **Özellikler** bölmesinde **Sütun seçiciyi başlat** seçeneğine tıklayın ve ardından **fiyat** sütununu seçin. **Fiyat,** modelimizin tahmin edileteceğimiz değerdir.

    Sütun seçicide **fiyat** sütununu **Kullanılabilir sütunlar** listesinden **Seçili sütunlar** listesine taşıyarak seçersiniz.

    !["Modeli Eğitme" modülü için fiyat sütununu seçin](./media/create-experiment/select-price-column.png)

1. Denemeyi çalıştırın.

Şimdi, fiyat tahmininde bulunmak amacıyla yeni otomobil verilerini puanlamak için kullanılabilecek eğitilmiş bir regresyon modeli oluşturduk.

![Çalıştırdıktan sonra, deneme şimdi aşağıdakine benzer görünmelidir](./media/create-experiment/second-experiment-run.png)

## <a name="predict-new-automobile-prices"></a>Yeni otomobil fiyatlarını tahmin edin

Verilerimizin yüzde 75'ini kullanarak modeli eğittiğimize göre, modelimizin ne kadar iyi işlediğini görmek için verilerimizin diğer yüzde 25'ini puanlama amacıyla kullanabiliriz.

1. [Model Puanlama][score-model] modülünü bulup deneme tuvaline sürükleyin. [Modeli Eğitme][train-model] modülünün çıkışını [Model Puanlama][score-model] modülünün sol giriş bağlantı noktasına bağlayın. [Verileri Bölme][split] modülünün test verileri çıkışını (sağ bağlantı noktası) [Model Puanlama][score-model] modülünün sağ giriş bağlantı noktasına bağlayın.

    !["Model Puanlama" modülünü "Modeli Eğitme" ve "Verileri Bölme" modüllerine bağlayın](./media/create-experiment/connect-score-model.png)

1. Denemeyi çalıştırın ve [Puan Modeli][score-model] çıkış bağlantı noktasını tıklatarak [Puan Modeli][score-model] modülünden çıktıyı görüntüleyin ve **Visualize'ı**seçin. Çıkış, fiyat için tahmin edilen değerleri ve test verileri için bilinen değerleri gösterir.  

    !["Model Puanlama" modülü çıkışı](./media/create-experiment/score-model-output.png)

1. Son olarak, sonuç kalitesini test edeceğiz. [Model Değerlendirme][evaluate-model] modülünü seçip deneme tuvaline sürükleyin ve [Model Puanlama][score-model] modülünün çıkışını [Model Değerlendirme][evaluate-model] modülünün sol giriş bağlantı noktasına bağlayın. Son deneme şuna benzer şekilde görünecektir:

    ![Son deneme](./media/create-experiment/complete-linear-regression-experiment.png)

1. Denemeyi çalıştırın.

[Model Değerlendirme][evaluate-model] modülünden çıkışı görüntülemek için, çıkış bağlantı noktasına tıklayın ve ardından **Görselleştir**'i seçin.

![Deneme için değerlendirme sonuçları](./media/create-experiment/evaluation-results.png)

Modelimiz için aşağıdaki istatistikler gösterilir:

- **Mean Absolute Error (Ortalama Mutlak Hata)** (MAE): Mutlak hataların ortalaması (*hata*, tahmin edilen değer ile gerçek değer arasındaki farktır).
- **Root Mean Squared Error (Kök Ortalama Karesi Alınmış Hata)** (RMSE): Test veri kümesinde yapılan tahminlerin karesi alınmış hata ortalamasının kare kökü.
- **Relative Absolute Error (Göreli Mutlak Hata)**: Gerçek değerler ve tüm gerçek değerlerin ortalaması arasındaki mutlak hataların mutlak farka göreli ortalaması.
- **Relative Squared Error (Göreli Karesi Alınmış Hata)**: Gerçek değerler ve tüm gerçek değerlerin ortalaması arasındaki karesi alınmış hataların karesi alınmış farka göreli ortalaması.
- **Coefficient of Determination (Determinasyon Katsayısı)**: **R karesi alınmış değer** olarak da bilinen ve modelin verilere ne kadar iyi uyumlu olduğunu gösteren istatistik ölçümleridir.

Her bir hata istatistiği ne kadar küçük olursa o kadar iyidir. Daha küçük olan bir değer, tahminlerin gerçek değerlerle daha yakından eşleştiğini gösterir. **Coefficient of Determination (Determinasyon Katsayısı)** değeri bire (1.0) ne kadar yakınsa tahminler o kadar iyi olur.

## <a name="clean-up-resources"></a>Kaynakları temizleme

[!INCLUDE [machine-learning-studio-clean-up](../../../includes/machine-learning-studio-clean-up.md)]

## <a name="next-steps"></a>Sonraki adımlar

Bu hızlı başlatmada, örnek bir veri kümesi kullanarak basit bir deneme oluşturdunuz. Bir modeli daha derinlemesine oluşturma ve dağıtma işlemini araştırmak için, tahmine dayalı çözüm eğitimine devam edin.

> [!div class="nextstepaction"]
> [Öğretici: Studio'da tahmine dayalı bir çözüm geliştirin (klasik)](tutorial-part1-credit-risk.md)

<!-- Module References -->
[evaluate-model]: https://msdn.microsoft.com/library/azure/927d65ac-3b50-4694-9903-20f6c1672089/
[linear-regression]: https://msdn.microsoft.com/library/azure/31960a6f-789b-4cf7-88d6-2e1152c0bd1a/
[clean-missing-data]: https://msdn.microsoft.com/library/azure/d2c5ca2f-7323-41a3-9b7e-da917c99f0c4/
[select-columns]: https://msdn.microsoft.com/library/azure/1ec722fa-b623-4e26-a44e-a50c6d726223/
[score-model]: https://msdn.microsoft.com/library/azure/401b4f92-e724-4d5a-be81-d5b0ff9bdb33/
[split]: https://msdn.microsoft.com/library/azure/70530644-c97a-4ab6-85f7-88bf30a8be5f/
[train-model]: https://msdn.microsoft.com/library/azure/5cc7053e-aa30-450d-96c0-dae4be720977/
