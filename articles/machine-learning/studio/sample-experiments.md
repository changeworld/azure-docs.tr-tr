---
title: Örneklerden Kickstart deneyleri
titleSuffix: ML Studio (classic) - Azure
description: Azure AI Galerisi ve Azure Machine Learning Studio (klasik) ile yeni denemeler oluşturmak için örnek makine öğrenimi denemelerini nasıl kullanacağınızı öğrenin.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: likebupt
ms.author: keli19
ms.custom: seodec18, previous-author=heatherbshapiro, previous-ms.author=hshapiro
ms.date: 01/05/2018
ms.openlocfilehash: 1a913e714565ffb99237357e331ef5cf0189c40f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79204230"
---
# <a name="create-azure-machine-learning-studio-classic-experiments-from-working-examples-in-azure-ai-gallery"></a>Azure Yapay AI Galerisi'nde çalışan örneklerden Azure Machine Learning Studio (klasik) denemeleri oluşturun

[!INCLUDE [Notebook deprecation notice](../../../includes/aml-studio-notebook-notice.md)]

Sıfırdan makine öğrenimi denemeleri oluşturmayı öğrenmek yerine [Azure AI Gallery](https://gallery.azure.ai/)’deki örnek denemelerle nasıl çalışmaya başlayacağınızı öğrenin. Örnekleri kullanarak kendi makine öğrenimi çözümünüzü oluşturabilirsiniz.

Galeride Microsoft Azure Machine Learning Studio (klasik) ekibinin örnek deneylerini ve Machine Learning topluluğu tarafından paylaşılan örnekler bulunmaktadır. Ayrıca, denemeler hakkında soru sorabilir veya yorum yazabilirsiniz.

Galeriyi nasıl kullanacağınızı görmek için [Yeni Başlayanlar için Veri Bilimi](data-science-for-beginners-the-5-questions-data-science-answers.md) serisinden [Veri bilimi yapmak için başka insanların çalışmalarını kopyalama](data-science-for-beginners-copy-other-peoples-work-to-do-data-science.md) başlıklı 3 dakikalık videoyu izleyin.



## <a name="find-an-experiment-to-copy-in-azure-ai-gallery"></a>Azure AI Gallery'de kopyalanacak bir deneme bulma
Hangi denemelerin kullanılabildiğini görmek için [Galeri](https://gallery.azure.ai/)'ye gidin ve sayfanın en üst kısmında **Denemeler**'e tıklayın.

### <a name="find-the-newest-or-most-popular-experiments"></a>En yeni ve en popüler denemeleri bulma
Bu sayfada **Son eklenen** denemeleri görüntüleyebilir, aşağı kaydırarak **Popüler olanlar**'a bakabilir veya en son **Popüler Microsoft denemeleri**’ni görebilirsiniz.

### <a name="look-for-an-experiment-that-meets-specific-requirements"></a>Belirli gereksinimleri karşılayan bir deneme arama
Tüm denemelere gözatmak için:

1. Sayfanın üstündeki **Tümüne gözat**'a tıklayın.
2. Galeri'deki tüm denemeleri görmek için sol tarafta bulunan **Kategoriler** bölümündeki **Daraltma ölçütü** seçeneğinin altından **Denemeler**'i seçin.
3. Gereksinimlerinize uyan denemeleri birkaç farklı yolla bulabilirsiniz:
   * **Soldaki filtreleri seçin.** Örneğin, PCA tabanlı bir anomali algılama algoritması kullanan denemelere göz atmak için: **Kategoriler** altında **Deneme**'ye tıklayın. Ardından, **Kullanılan Algoritmalar** altında **Tümünü Göster**’e tıklayın ve iletişim kutusunda **PCA Tabanlı Anomali Algılama**’yı seçin. Bu seçeneği görmek için kaydırmanız gerekebilir.<br></br>
     ![Filtreleri seçme](./media/sample-experiments/choose-an-algorithm.png)
   * **Arama kutusunu kullanın.** Örneğin, Microsoft tarafından paylaşılan iki sınıflı destekli vektör makinesi algoritması kullanan rakam tanıma ile ilgili denemeleri bulmak için arama kutusuna "rakam tanıma" yazın. Daha sonra, **Deneme**, **Yalnızca Microsoft içeriği** ve **İki Sınıflı Destekli Vektör Makinesi**'ni seçin:<br></br>
     ![Arama kutusunu kullanma](./media/sample-experiments/search-for-experiments.png)
4. Hakkında daha fazla bilgi almak için bir denemeye tıklayın.
5. Denemeyi çalıştırmak ve/veya değiştirmek için, deneme sayfasında **Studio'da aç** seçeneğine tıklayın. <br></br>

    ![Örnek deneme](./media/sample-experiments/example-experiment.png)

## <a name="create-a-new-experiment-using-an-example-as-a-template"></a>Şablon olarak bir örnek kullanarak yeni deneme oluşturma
Ayrıca, Şablon olarak Galeri örneğini kullanarak Machine Learning Studio'da (klasik) yeni bir deneme oluşturabilirsiniz.

1. Microsoft hesabı kimlik bilgilerinizle [Studio](https://studio.azureml.net)'da oturum açın ve ardından bir deneme oluşturmak için **Yeni** seçeneğine tıklayın.
2. Örnek içeriğine gözatın ve birine tıklayın.

Örnek deneme şablon olarak kullanılarak Machine Learning Studio (klasik) çalışma alanınızda yeni bir deneme oluşturulur.

## <a name="next-steps"></a>Sonraki adımlar
* [Çeşitli kaynaklardan veri alma](import-data.md)
* [Machine Learning için R programlama diline yönelik hızlı başlangıç öğreticisi](r-quickstart.md)
* [Machine Learning web hizmeti dağıtma](deploy-a-machine-learning-web-service.md)
