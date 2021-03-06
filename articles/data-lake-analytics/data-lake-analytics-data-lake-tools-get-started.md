---
title: Azure Veri Gölü Analizini Sorgula - Visual Studio
description: Visual Studio için Data Lake Araçları'nı nasıl yükleyeceğinizi ve U-SQL betiklerini nasıl geliştirip test edeceğinizi öğrenin.
services: data-lake-analytics
ms.service: data-lake-analytics
author: saveenr
ms.author: saveenr
ms.reviewer: jasonwhowell
ms.assetid: ad8a6992-02c7-47d4-a108-62fc5a0777a3
ms.topic: conceptual
ms.date: 08/30/2019
ms.openlocfilehash: dacce0d4f40f077b5da6221000192a4398da99e0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79260351"
---
# <a name="develop-u-sql-scripts-by-using-data-lake-tools-for-visual-studio"></a>Visual Studio için Data Lake Araçları'nı kullanarak U-SQL betikleri geliştirme

[!INCLUDE [get-started-selector](../../includes/data-lake-analytics-selector-get-started.md)]

Azure Veri Gölü ve Akış Analizi Araçları, iki Azure hizmeti, Azure Veri Gölü Analizi ve Azure Akış Analizi ile ilgili işlevleri içerir. Azure Akış Analizi senaryoları hakkında daha fazla bilgi için [Visual Studio için Azure Akış Analizi araçlarına](../stream-analytics/stream-analytics-tools-for-visual-studio-install.md)bakın.

Bu makalede, Azure Veri Gölü Analizi hesapları oluşturmak için Visual Studio'nun nasıl kullanılacağı açıklanmaktadır. [U-SQL'deki](data-lake-analytics-u-sql-get-started.md)işleri tanımlayabilir ve işleri Data Lake Analytics hizmetine gönderebilirsiniz. Veri Gölü Analizi hakkında daha fazla bilgi için [Azure Veri Gölü Analizi'ne genel bakış](data-lake-analytics-overview.md)bölümüne bakın.

> [!IMPORTANT]
> Visual Studio sürümü 2.3.3000.4 veya sonraki sürüm için Azure Veri Göl Araçları'na yükseltmenizi öneririz. Önceki sürümler artık indirilemiyor ve kullanım dışı.
>
> 1. Visual Studio için Azure Data Lake Araçları’nın 2.3.3000.4’ten önceki bir sürümünü kullanıp kullanmadığınızı denetleyin.
>
>    ![Araç sürümünü denetleyin](./media/data-lake-analytics-data-lake-tools-get-started/data-lake-analytics-data-lake-tools-about-data-lake.png)
>
> 1. Sürümünüz 2.3.3000.4 sürümünden eskiyse, indirme merkezini ziyaret ederek Visual Studio için Azure Data Lake Araçları sürümünüzü güncelleştirin:
>    - [Visual Studio 2017 ve 2019 için](https://marketplace.visualstudio.com/items?itemName=ADLTools.AzureDataLakeandStreamAnalyticsTools)
>    - [Visual Studio 2013 ve 2015 için](https://www.microsoft.com/en-us/download/details.aspx?id=49504)

## <a name="prerequisites"></a>Ön koşullar

* **Visual Studio**: Express dışında tüm sürümler desteklenir.

  * Visual Studio 2019
  * Visual Studio 2017
  * Visual Studio 2015
  * Visual Studio 2013

* **.NET için Microsoft Azure SDK** 2.7.1 sürümü veya sonraki sürümleri. [Web platformu yükleyicisini](https://www.microsoft.com/web/downloads/platform.aspx) kullanarak yükleyin.
* **Bir Data Lake Analytics** hesabı. Hesap oluşturmak için bkz. [Azure portalı kullanarak Azure Data Lake Analytics ile çalışmaya başlama](data-lake-analytics-get-started-portal.md).

## <a name="install-azure-data-lake-tools-for-visual-studio"></a>Visual Studio için Azure Data Lake Araçları’nı yükleme

Bu öğretici, Visual Studio için Data Lake Araçları’nın yüklü olmasını gerektirir. Daha fazla bilgi için Visual [Studio için Veri Gölü Araçlarını Yükle'ye](data-lake-analytics-data-lake-tools-install.md)bakın.

## <a name="connect-to-an-azure-data-lake-analytics-account"></a>Azure Data Lake Analytics hesabına bağlanma

1. Visual Studio'yu açın.

1. Sunucu **Gezgini'ni** **Görüntüle'yi** > seçerek Server**Explorer'ı**açın.

1. Azure'a **Azure**sağ tıklayın, ardından **Microsoft Azure Aboneliği'ne Bağlan'ı**seçin. **Hesabınızda Oturum Aç,** talimatları izleyin.

1. **Sunucu Gezgini'nde** **Azure** > **Veri Gölü Analitiği'ni**seçin. Data Lake Analytics hesaplarınızın listesini görürsünüz.

## <a name="write-your-first-u-sql-script"></a>İlk U-SQL betiğinizi yazma

Aşağıda basit bir U-SQL betiği gösterilmiştir. Küçük bir veri kümesini tanımlar ve bu veri kümesini `/data.csv` adlı bir dosya olarak varsayılan Data Lake Store’a yazar.

```sql
USE DATABASE master;
USE SCHEMA dbo;
@a  = 
    SELECT * FROM 
        (VALUES
            ("Contoso", 1500.0),
            ("Woodgrove", 2700.0)
        ) AS 
              D( customer, amount );
OUTPUT @a
    TO "/data.csv"
    USING Outputters.Csv();
```

## <a name="submit-a-data-lake-analytics-job"></a>Data Lake Analytics işi gönderme

1. Visual Studio'da **Dosya** > **Yeni** > **Projesi'ni**seçin.

1. **U-SQL Project** türünü seçin ve sonra **İleri'yi**seçin. **Yeni projenizi yapılandırın**, **Oluştur'u**seçin.

   Visual Studio, **Script.usql** dosyası içeren bir çözüm oluşturur.

1. Komut [dosyasını ilk U-SQL komut dosyanızı](#write-your-first-u-sql-script) **Script.usql** penceresine yazın' dan yapıştırın.

1. **Solution Explorer'da**, **Script.usql'e**sağ tıklayın ve **Komut Dosyası Gönder'i**seçin.

1. **İş**Gönder'de, Veri Gölü Analizi hesabınızı seçin ve **Gönder'i**seçin.

   ![U-SQL Visual Studio projesini gönderme](./media/data-lake-analytics-data-lake-tools-get-started/data-lake-analytics-submit-job-vs2019.png)

İş gönderildikten sonra açılan **İş görünümü** sekmesinde işin ilerleme durumu gösterilir.

* **İş Özeti**, işin özetini gösterir.
* **İş Grafı**, işin ilerleme durumunu görselleştirir.
* **Meta Veri İşlemleri**, U-SQL kataloğunda yapılan tüm işlemleri gösterir.
* **Veri** tüm girdileri ve çıktıları gösterir.
* **Durum Geçmişi** bölümünde zaman çizelgesi ve durum ayrıntıları gösterilir.
* **AU Analizi,** işte kaç AUs kullanıldığını gösterir ve farklı AU ayırma stratejilerinin simülasyonlarını araştırın.
* **Tanılama**, iş yürütme ve performans iyileştirme için gelişmiş bir analiz sağlar.

![U-SQL Visual Studio Data Lake Analytics iş performans grafiği](./media/data-lake-analytics-data-lake-tools-get-started/data-lake-analytics-data-lake-tools-performance-graph.png)

En son iş durumunu görmek ve ekranı yenilemek için **Yenile'yi**seçin.

## <a name="check-job-status"></a>İş durumunu kontrol etme

1. **Sunucu Gezgini'nde** **Azure** > **Veri Gölü Analitiği'ni**seçin.

1. Data Lake Analytics hesap adını genişletin.

1. **İşler**’e çift tıklayın.

1. Daha önce gönderdiğiniz işi seçin.

## <a name="see-the-job-output"></a>İş çıktısını görme

1. **Sunucu Gezgini'nde,** gönderdiğiniz işe göz atın.

1. **Veri** sekmesine tıklayın.

1. **İş Çıktıları** sekmesinde `"/data.csv"` dosyasını seçin.

## <a name="next-steps"></a>Sonraki adımlar

* [Test etmek ve hata ayıklamak için kendi iş istasyonunuzda U-SQL betiklerini çalıştırma](data-lake-analytics-data-lake-tools-local-run.md)
* [Visual Studio Code için Azure Data Lake Araçları'nı kullanarak U-SQL işlerindeki C# kodunda hata ayıklama](data-lake-tools-for-vscode-local-run-and-debug.md)
* [Visual Studio Code için Azure Data Lake Araçları’nı kullanma](data-lake-analytics-data-lake-tools-for-vscode.md)
