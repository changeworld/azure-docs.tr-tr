---
title: Azure portalını kullanarak tabloyu aşamalı olarak kopyalama
description: Bu öğreticide, verileri Azure SQL veritabanından Azure Blob depolama alanına artımlı olarak kopyalayan bir Azure veri fabrikası işlem hattı oluşturacaksınız.
services: data-factory
author: dearandyxu
ms.author: yexu
manager: anandsub
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: tutorial
ms.custom: seo-dt-2019
ms.date: 01/11/2018
ms.openlocfilehash: a908b44f596e6ca1ecd2960110a35661448c2e1a
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/24/2020
ms.locfileid: "75982661"
---
# <a name="incrementally-load-data-from-an-azure-sql-database-to-azure-blob-storage-using-the-azure-portal"></a>Azure portalını kullanarak azure SQL veritabanından Azure Blob depolamasına verileri aşamalı olarak yükleyin

Bu öğreticide, Azure SQL veritabanındaki bir tablodan Azure Blob depolama alanına delta veri yükleyen işlem hattına sahip bir Azure veri fabrikası oluşturacaksınız.

Bu öğreticide aşağıdaki adımları gerçekleştireceksiniz:

> [!div class="checklist"]
> * Eşik değerini depolamak için veri deposunu hazırlama.
> * Veri fabrikası oluşturma.
> * Bağlı hizmet oluşturma.
> * Kaynak, havuz ve eşik veri kümeleri oluşturun.
> * İşlem hattı oluşturma.
> * İşlem hattını çalıştırma.
> * İşlem hattı çalıştırmasını izleme.
> * Sonuçları gözden geçirme
> * Kaynağa daha fazla veri ekleyin.
> * İşlem hattını yeniden çalıştırın.
> * İkinci işlem hattı çalıştırmasını izleme
> * İkinci çalıştırmanın sonuçlarını gözden geçirme


## <a name="overview"></a>Genel Bakış
Yüksek düzeyli çözüm diyagramı aşağıdaki gibidir:

![Artımlı olarak veri yükleme](media/tutorial-Incremental-copy-portal/incrementally-load.png)

Bu çözümü oluşturmak için önemli adımlar şunlardır:

1. **Eşit sütununu seçin**.
    Kaynak veri deposunda her çalıştırma için yeni veya güncelleştirilmiş kayıtları dilimlemek için kullanılabilen bir sütun seçin. Normalde, satırlar oluşturulduğunda veya güncelleştirildiğinde seçilen bu sütundaki veriler (örneğin, last_modify_time veya kimlik) artmaya devam eder. Bu sütundaki en büyük değer eşik olarak kullanılır.

2. **Eşik değerini depolamak için veri deposunu hazırlayın**. Bu öğreticide, eşik değerini bir SQL veritabanında depolayacaksınız.

3. **Aşağıdaki iş akışıile bir ardışık kaynak oluşturma:**

    Bu çözümdeki işlem hattı aşağıdaki etkinlikleri içerir:

    * İki Arama etkinliği oluşturun. Son eşik değerini almak için ilk Arama etkinliğini kullanın. Yeni eşik değerini almak için ikinci Arama etkinliğini kullanın. Bu eşik değerleri, Kopyalama etkinliğine geçirilir.
    * Eşik sütununun değeri eski eşik değerinden büyük ve yeni eşik değerinden küçük olacak şekilde, satırları kaynak veri deposundan kopyalayan bir Kopyalama etkinliği oluşturun. Ardından, delta veriler kaynak veri deposundan Blob depolama alanına yeni bir dosya olarak kopyalanır.
    * Sonraki seferde çalışan işlem hattı için eşik değerini güncelleştiren bir StoredProcedure etkinliği oluşturun.


Azure aboneliğiniz yoksa, başlamadan önce [ücretsiz](https://azure.microsoft.com/free/) bir hesap oluşturun.

## <a name="prerequisites"></a>Ön koşullar
* **Azure SQL Veritabanı**. Veritabanını kaynak veri deposu olarak kullanabilirsiniz. SQL veritabanınız yoksa, oluşturma adımları için bkz. [Azure SQL veritabanı oluşturma](../sql-database/sql-database-get-started-portal.md).
* **Azure Depolama**. Blob depolamayı havuz veri deposu olarak kullanabilirsiniz. Depolama hesabınız yoksa, oluşturma adımları için bkz. [Depolama hesabı oluşturma](../storage/common/storage-account-create.md). adftutorial adlı bir kapsayıcı oluşturun. 

### <a name="create-a-data-source-table-in-your-sql-database"></a>SQL veritabanınızda bir veri kaynağı tablosu oluşturma
1. SQL Server Management Studio’yu açın. **Sunucu Gezgini'nde**veritabanına sağ tıklayın ve **Yeni Sorgu'yu**seçin.

2. SQL veritabanınızda aşağıdaki SQL komutunu çalıştırarak veri kaynağı deponuz olarak `data_source_table` adlı bir tablo oluşturun:

    ```sql
    create table data_source_table
    (
        PersonID int,
        Name varchar(255),
        LastModifytime datetime
    );

    INSERT INTO data_source_table
    (PersonID, Name, LastModifytime)
    VALUES
    (1, 'aaaa','9/1/2017 12:56:00 AM'),
    (2, 'bbbb','9/2/2017 5:23:00 AM'),
    (3, 'cccc','9/3/2017 2:36:00 AM'),
    (4, 'dddd','9/4/2017 3:21:00 AM'),
    (5, 'eeee','9/5/2017 8:06:00 AM');
    ```
    Bu öğreticide eşik sütunu olarak LastModifytime kullanılır. Veri kaynağı deposundaki veriler aşağıdaki tabloda gösterilmiştir:

    ```
    PersonID | Name | LastModifytime
    -------- | ---- | --------------
    1 | aaaa | 2017-09-01 00:56:00.000
    2 | bbbb | 2017-09-02 05:23:00.000
    3 | cccc | 2017-09-03 02:36:00.000
    4 | dddd | 2017-09-04 03:21:00.000
    5 | eeee | 2017-09-05 08:06:00.000
    ```

### <a name="create-another-table-in-your-sql-database-to-store-the-high-watermark-value"></a>Üst eşik değerini depolamak için SQL veritabanınızda başka bir tablo oluşturma
1. SQL veritabanınızda aşağıdaki SQL komutunu çalıştırarak eşik değerini depolamak için `watermarktable` adlı bir tablo oluşturun:  

    ```sql
    create table watermarktable
    (

    TableName varchar(255),
    WatermarkValue datetime,
    );
    ```
2. Üst eşiğin varsayılan değerini kaynak veri deposunun tablo adıyla ayarlayın. Bu öğreticide tablo adı data_source_table şeklindedir.

    ```sql
    INSERT INTO watermarktable
    VALUES ('data_source_table','1/1/2010 12:00:00 AM')    
    ```
3. `watermarktable` tablosundaki verileri gözden geçirin.

    ```sql
    Select * from watermarktable
    ```
    Çıktı:

    ```
    TableName  | WatermarkValue
    ----------  | --------------
    data_source_table | 2010-01-01 00:00:00.000
    ```

### <a name="create-a-stored-procedure-in-your-sql-database"></a>SQL veritabanınızda bir saklı yordam oluşturma

SQL veritabanınızda bir saklı yordam oluşturmak için aşağıdaki komutu çalıştırın:

```sql
CREATE PROCEDURE usp_write_watermark @LastModifiedtime datetime, @TableName varchar(50)
AS

BEGIN

    UPDATE watermarktable
    SET [WatermarkValue] = @LastModifiedtime
WHERE [TableName] = @TableName

END
```

## <a name="create-a-data-factory"></a>Veri fabrikası oluşturma

1. **Microsoft Edge** veya **Google Chrome** web tarayıcısını açın. Şu anda Data Factory kullanıcı arabirimi yalnızca Microsoft Edge ve Google Chrome web tarayıcılarında desteklenmektedir.
2. Sol menüde, **bir kaynak** > **Analiz** > **Veri Fabrikası**Oluştur'u seçin:

   ![“Yeni” bölmesinde Data Factory seçimi](./media/doc-common-process/new-azure-data-factory-menu.png)

3. **Yeni veri fabrikası** sayfasında **ad** için **ADFIncCopyTutorialDF** adını girin.

   Azure veri fabrikasının adı **genel olarak benzersiz**olmalıdır. Aşağıdaki hatayla birlikte kırmızı bir ünlem işareti görürseniz veri fabrikasının adını değiştirin (örneğin adınızADFIncCopyTutorialDF) ve yeniden oluşturmayı deneyin. Data Factory yapıtlarını adlandırma kuralları için [Data Factory - Adlandırma Kuralları](naming-rules.md) makalesine bakın.

       `Data factory name "ADFIncCopyTutorialDF" is not available`
4. Veri fabrikasını oluşturmak istediğiniz Azure **aboneliğini** seçin.
5. Kaynak **Grubu**için aşağıdaki adımlardan birini yapın:

      - **Var olanı kullan**’ı seçin ve ardından açılır listeden var olan bir kaynak grubu belirleyin.
      - **Yeni oluştur**’u seçin ve bir kaynak grubunun adını girin.   
         
        Kaynak grupları hakkında daha fazla bilgi için bkz. [Azure kaynaklarınızı yönetmek için kaynak gruplarını kullanma](../azure-resource-manager/management/overview.md).  
6. **Sürüm** için **V2**'yi seçin.
7. Data factory için **konum** seçin. Açılan listede yalnızca desteklenen konumlar görüntülenir. Veri fabrikası tarafından kullanılan verileri depoları (Azure Depolama, Azure SQL Veritabanı vb.) ve işlemler (HDInsight vb.) başka bölgelerde olabilir.
8. **Oluştur'u**tıklatın.      
9. Oluşturma işlemi tamamlandıktan sonra, resimde gösterildiği gibi **Data Factory** sayfasını görürsünüz.

   ![Data factory giriş sayfası](./media/doc-common-process/data-factory-home-page.png)
10. Azure Data Factory kullanıcı arabirimini (UI) ayrı bir sekmede açmak için **Geliştir ve İzle** kutucuğuna tıklayın.

## <a name="create-a-pipeline"></a>İşlem hattı oluşturma
Bu öğreticide tek işlem hattında zincirlenmiş iki Arama etkinliği, bir Kopyalama etkinliği ve bir StoredProcedure etkinliği ile işlem hattı oluşturacaksınız.

1. Data Factory kullanıcı arabiriminin **başlarken** sayfasında **İşlem hattı oluştur** kutucuğuna tıklayın.

   ![Data Factory kullanıcı arabiriminin başlarken sayfası](./media/doc-common-process/get-started-page.png)    
3. İşlem hattının **Özellikler** penceresinin **Genel** sayfasında **IncrementalCopyPipeline** adını girin.

4. Eski filigran değerini almak için ilk arama etkinliğini ekleyelim. **Etkinlikler** araç kutusunda **Genel**’i genişletin ve bir **Arama** etkinliğini sürükleyerek işlem hattı tasarımcısının yüzeyine bırakın. Etkinliğin adını **LookupOldWaterMarkActivity** olarak değiştirin.

   ![İlk arama etkinliği - ad](./media/tutorial-incremental-copy-portal/first-lookup-name.png)
5. **Ayarlar** sekmesine geçin ve **Kaynak Veri Kümesi** için **+ Yeni**’ye tıklayın. Bu adımda, **filigran tablosundaki** verileri temsil eden bir veri kümesi oluşturursunuz. Bu tablo, önceki kopyalama işleminde kullanılan eski filigranı içerir.

6. Yeni **Veri Kümesi** penceresinde **Azure SQL Veritabanı'nı**seçin ve **Devam et'i**tıklatın. Veri kümesi için yeni bir pencere açıldığını görürsünüz.

7. Veri kümesiiçin **Ayar özellikleri** penceresinde, **Ad**için **FiligranDataset'i** girin.

8. **Bağlantılı Hizmet**için **Yeni'yi**seçin ve ardından aşağıdaki adımları yapın:

    1. **Ad** için **AzureSqlDatabaseLinkedService** adını girin.
    2. **Sunucu adı** için Azure SQL Server’ınızı seçin.
    3. Açılan listeden **Veritabanı adınızı** seçin.
    4. Kullanıcı **adı** & **Şifrenizi**girin.
    5. Azure SQL veritabanı bağlantısını test etmek için **Bağlantıyı sına**’ya tıklayın.
    6. **Son**'a tıklayın.
    7. **AzureSqlDatabaseLinkedService'in** Bağlantılı **hizmet**için seçildiğini doğrulayın.

        ![Yeni bağlı hizmet penceresi](./media/tutorial-incremental-copy-portal/azure-sql-linked-service-settings.png)
    8. **Bitiş'i**seçin.
9. **Bağlantı** sekmesinde **[dbo]'yu seçin.[ ** **tablo**için filigran] . Tablodaki verilerin önizlemesini yapmak istiyorsanız **Veri önizlemesini görüntüle**’ye tıklayın.

    ![Filigran veri kümesi - bağlantı ayarları](./media/tutorial-incremental-copy-portal/watermark-dataset-connection-settings.png)
10. Üstteki işlem hattı sekmesine veya soldaki ağaç görünümünden işlem hattının adına tıklayarak işlem hattı düzenleyicisine geçin. **Arama** etkinliğinin özellikler penceresinde **Kaynak Veri Kümesi** alanı için **WatermarkDataset** seçeneğinin belirlendiğinden emin olun.

11. **Etkinlikler** araç kutusunda **Genel**’i genişletip başka bir **Arama** etkinliğini işlem hattı tasarımcısının yüzeyine sürükleyin ve özellikler penceresinin **Genel** sekmesinde adı **LookupNewWaterMarkActivity** olarak ayarlayın. Bu Arama etkinliği, kaynak verileri içeren tablodan hedefe kopyalanacak yeni filigran değerini alır.

12. İkinci **Arama** etkinliğinin özellikler penceresinde **Ayarlar** sekmesine geçip **Yeni**’ye tıklayın. Yeni filigran değerini (en yüksek LastModifyTime değeri) içeren kaynak tabloyu gösteren bir veri kümesi oluşturursunuz.

13. Yeni **Veri Kümesi** penceresinde **Azure SQL Veritabanı'nı**seçin ve **Devam et'i**tıklatın.
14. Set **özellikleri** penceresinde, **Ad**için **SourceDataset'i** girin. **Bağlı hizmet** için **AzureSqlDatabaseLinkedService** hizmetini seçin.
15. **[dbo]'yu seçin.[ tablo** için data_source_table] Bu öğreticinin sonraki bölümlerinde, bu veri kümesinde bir sorgu belirtirsiniz. Bu sorgu, bu adımda belirttiğiniz tablodan önceliklidir.
16. **Bitiş'i**seçin.
17. Üstteki işlem hattı sekmesine veya soldaki ağaç görünümünden işlem hattının adına tıklayarak işlem hattı düzenleyicisine geçin. **Arama** etkinliğinin özellikler penceresinde **Kaynak Veri Kümesi** alanı için **SourceDataset** seçeneğinin belirlendiğinden emin olun.
18. **Sorgu Kullan** alanı için **Sorgu**’yu seçin ve aşağıdaki sorguyu girin: **data_source_table** tablosundan yalnızca en yüksek **LastModifytime** değerini seçersiniz. Lütfen **yalnızca İlk satırı**da kontrol ettiğinizden emin olun.

    ```sql
    select MAX(LastModifytime) as NewWatermarkvalue from data_source_table
    ```

    ![İkinci arama etkinliği - sorgu](./media/tutorial-incremental-copy-portal/query-for-new-watermark.png)
19. **Etkinlikler** araç kutusunda, **Dönüştür& taşı'nı**genişletin ve Etkinlikler araç kutusundan **Kopyalama** etkinliğini sürükleyin ve adını **Artımlı CopyActivity**olarak ayarlayın.

20. Arama etkinliklerine bağlı **yeşil düğmeyi** Kopyalama etkinliğine sürükleyerek **her iki Arama etkinliğini Kopyalama etkinliğine bağlayın**. Kopyalama etkinliğinin kenarlık renginin mavi olduğunu gördüğünüzde fare düğmesini bırakın.

    ![Arama etkinliklerini Kopyalama etkinliklerine bağlama](./media/tutorial-incremental-copy-portal/connection-lookups-to-copy.png)
21. **Kopyalama etkinliğini** seçin ve **Özellikler** penceresinde etkinliğin özelliklerini gördüğünüzü onaylayın.

22. **Özellikler** penceresinde **Kaynak** sekmesine geçin ve aşağıdaki adımları uygulayın:

    1. **Kaynak Veri Kümesi** alanı için **SourceDataset**’i seçin.
    2. **Sorgu Kullan** alanı için **Sorgu**’yu seçin.
    3. **Sorgu** alanı için aşağıdaki SQL sorgusunu girin.

        ```sql
        select * from data_source_table where LastModifytime > '@{activity('LookupOldWaterMarkActivity').output.firstRow.WatermarkValue}' and LastModifytime <= '@{activity('LookupNewWaterMarkActivity').output.firstRow.NewWatermarkvalue}'
        ```

        ![Kopyalama etkinliği - kaynak](./media/tutorial-incremental-copy-portal/copy-activity-source.png)
23. **Havuz** sekmesine geçin ve **Havuz Veri Kümesi** alanı için **+ Yeni**’ye tıklayın.

24. Bu öğreticide, havuz veri deposu Azure Blob Depolama türündedir. Bu nedenle, **Azure Blob Depolama'yı**seçin ve **Yeni Veri Kümesi** penceresinde **Devam** et'i tıklatın.
25. **Biçim'i seç** penceresinde, verilerinizin biçim türünü seçin ve **Devam et'i**tıklatın.
25. Özellikleri **Ayarla** penceresinde, **Ad**için **SinkDataset'i** girin. **Bağlantılı Hizmet**için , seçin **+ Yeni**. Bu adımda, **Azure Blob Depolama**’nıza yönelik bir bağlantı (bağlı hizmet) oluşturursunuz.
26. Yeni **Bağlantılı Hizmet (Azure Blob Depolama)** penceresinde aşağıdaki adımları yapın:

    1. **Ad** için **AzureStorageLinkedService** adını girin.
    2. **Depolama hesabı adı**için Azure Depolama hesabınızı seçin.
    3. Bağlantıyı Test Edin ve **ardından Bitir'i**tıklatın.

27. Özellikleri **Ayarla** penceresinde, **AzureStorageLinkedService'in** **Bağlantılı hizmet**için seçildiğini onaylayın. Ardından **Son**’u seçin.
28. SinkDataset'in **Bağlantı** sekmesine gidin ve aşağıdaki adımları yapın:
    1. Dosya **yolu** alanı için **adftutorial/artımlı kopya**girin. **adftutorial** blob kapsayıcısı adı, **incrementalcopy** klasör adıdır. Bu kod parçacığı blob depolama hesabınızda adftutorial adlı bir blob kapsayıcıya sahip olduğunuzu varsayar. Henüz yoksa kapsayıcıyı oluşturun veya var olan bir kapsayıcının adına ayarlayın. **incrementalcopy** adlı çıktı dosyası mevcut değilse Azure Data Factory tarafından otomatik olarak oluşturulur. Bir blob kapsayıcısındaki klasörlerden birine gitmek istiyorsanız **Dosya yolu** için **Gözat** düğmesini de kullanabilirsiniz.
    2. **Dosya yolu** alanının **Dosya** bölümü için dinamik içerik **ekle [Alt+P]** ve ardından açılan pencereye girin' `@CONCAT('Incremental-', pipeline().RunId, '.txt')`i seçin. Ardından **Son**’u seçin. Dosya adı, ifade kullanılarak dinamik olarak oluşturulur. Her işlem hattı çalıştırması benzersiz bir kimliğe sahiptir. Kopyalama etkinliği, dosya adını oluşturmak için çalışma kimliğini kullanır.

28. Üstteki işlem hattı sekmesine veya soldaki ağaç görünümünden işlem hattının adına tıklayarak **işlem hattı** düzenleyicisine geçin.
29. **Etkinlikler** araç kutusunda **Genel**’i genişletin ve **Etkinlikler** araç kutusundan **Saklı Yordam** etkinliğini sürükleyip işlem hattı tasarımcısının yüzeyine bırakın. **Kopyalama** etkinliğinin yeşil (Başarılı) çıktısını **Saklı Yordam** etkinliğine **bağlayın**.

24. İşlem hattı tasarımcısında **Saklı Yordam Etkinliğini** seçip etkinliğin adını **StoredProceduretoWriteWatermarkActivity** olarak değiştirin.

25. **SQL Hesabı** sekmesine geçin ve **Bağlantılı hizmet**için **AzureSqlDatabaseLinkedService'i** seçin.

26. **Saklı Yordam** sekmesine geçin ve aşağıdaki adımları uygulayın:

    1. **Depolanan yordam adı**için **usp_write_watermark'** ı seçin.
    2. Saklı yordam parametrelerinin değerlerini belirtmek için, **Parametreyi içeri aktar**’a tıklayın ve parametreler için aşağıdaki değerleri girin:

        | Adı | Tür | Değer |
        | ---- | ---- | ----- |
        | LastModifiedtime | DateTime | @{activity('LookupNewWaterMarkActivity').output.firstRow.NewWatermarkvalue} |
        | TableName | Dize | @{activity('LookupOldWaterMarkActivity').output.firstRow.TableName} |

    ![Saklı Yordam Etkinliği - saklı yordam ayarları](./media/tutorial-incremental-copy-portal/sproc-activity-stored-procedure-settings.png)
27. İşlem hattı ayarlarını doğrulamak için araç çubuğunda **Doğrula**’ya tıklayın. Doğrulama hatası olmadığından emin olun. **İşlem Hattı Doğrulama Raporu** penceresini kapatmak için >> seçeneğine tıklayın.   

28. **Tümünü Yayımla** düğmesine tıklayarak varlıkları (bağlı hizmetler, veri kümeleri ve işlem hatları) Azure Data Factory hizmetinde yayımlayın. Yayımlamanın başarılı olduğunu belirten bir ileti görene kadar bekleyin.


## <a name="trigger-a-pipeline-run"></a>İşlem hattı çalıştırmasını tetikleme
1. Araç çubuğunda **Tetikleyici Ekle'yi** tıklatın ve **Şimdi Tetikle'yi**tıklatın.

2. **İşlem Hattı Çalıştırma** penceresinde **Son**’u seçin.

## <a name="monitor-the-pipeline-run"></a>İşlem hattı çalıştırmasını izleme

1. Soldaki **İzleyici** sekmesine geçin. El ile tetikleme işlemi tarafından tetiklenen işlem hattı çalıştırmasının durumunu görebilirsiniz. Listeyi yenilemek için **Yenile** düğmesine tıklayın.

2. Bu işlem hattı çalıştırmasıyla ilişkili etkinlik çalıştırmalarını görüntülemek için **Eylemler** sütunundaki ilk bağlantıya (**Etkinlik Çalıştırmalarını Görüntüle**) tıklayın. Üst taraftan **İşlem Hatları**’na tıklayarak önceki görünüme dönebilirsiniz. Listeyi yenilemek için **Yenile** düğmesine tıklayın.


## <a name="review-the-results"></a>Sonuçları gözden geçirin
1. [Azure Depolama Gezgini](https://azure.microsoft.com/features/storage-explorer/) gibi araçları kullanarak Azure Depolama Hesabınıza bağlanın. **adftutorial** kapsayıcısının **incrementalcopy** klasöründe bir çıktı dosyası oluşturulduğunu doğrulayın.

    ![İlk çıktı dosyası](./media/tutorial-incremental-copy-portal/first-output-file.png)
2. Çıktı dosyasını açın ve **data_source_table** tablosundaki tüm verilerin blob dosyasına kopyalandığına dikkat edin.

    ```
    1,aaaa,2017-09-01 00:56:00.0000000
    2,bbbb,2017-09-02 05:23:00.0000000
    3,cccc,2017-09-03 02:36:00.0000000
    4,dddd,2017-09-04 03:21:00.0000000
    5,eeee,2017-09-05 08:06:00.0000000
    ```
3. `watermarktable` içindeki en son değeri denetleyin. Eşik değerinin güncelleştirildiğini görürsünüz.

    ```sql
    Select * from watermarktable
    ```

    Çıktı şöyledir:

    | TableName | WatermarkValue |
    | --------- | -------------- |
    | data_source_table | 2017-09-05    8:06:00.000 |

## <a name="add-more-data-to-source"></a>Kaynağa daha fazla veri ekleme

Yeni verileri SQL veritabanına (veri kaynağı deposu) ekleyin.

```sql
INSERT INTO data_source_table
VALUES (6, 'newdata','9/6/2017 2:23:00 AM')

INSERT INTO data_source_table
VALUES (7, 'newdata','9/7/2017 9:01:00 AM')
```

SQL veritabanında güncelleştirilmiş veriler:

```
PersonID | Name | LastModifytime
-------- | ---- | --------------
1 | aaaa | 2017-09-01 00:56:00.000
2 | bbbb | 2017-09-02 05:23:00.000
3 | cccc | 2017-09-03 02:36:00.000
4 | dddd | 2017-09-04 03:21:00.000
5 | eeee | 2017-09-05 08:06:00.000
6 | newdata | 2017-09-06 02:23:00.000
7 | newdata | 2017-09-07 09:01:00.000
```


## <a name="trigger-another-pipeline-run"></a>Başka bir işlem hattı çalıştırması tetikleme
1. **Düzenleme** sekmesine geç. Tasarımcıda açılmamışsa ağaç görünümündeki ardışık hattı tıklatın.

2. Araç çubuğunda **Tetikleyici Ekle'yi** tıklatın ve **Şimdi Tetikle'yi**tıklatın.


## <a name="monitor-the-second-pipeline-run"></a>İkinci işlem hattı çalıştırmasını izleme

1. Soldaki **İzleyici** sekmesine geçin. El ile tetikleme işlemi tarafından tetiklenen işlem hattı çalıştırmasının durumunu görebilirsiniz. Listeyi yenilemek için **Yenile** düğmesine tıklayın.

2. Bu işlem hattı çalıştırmasıyla ilişkili etkinlik çalıştırmalarını görüntülemek için **Eylemler** sütunundaki ilk bağlantıya (**Etkinlik Çalıştırmalarını Görüntüle**) tıklayın. Üst taraftan **İşlem Hatları**’na tıklayarak önceki görünüme dönebilirsiniz. Listeyi yenilemek için **Yenile** düğmesine tıklayın.


## <a name="verify-the-second-output"></a>İkinci çıkışı doğrulama

1. Blob depolama alanında başka bir dosyanın oluşturulduğunu görürsünüz. Bu öğreticide yeni dosya adı `Incremental-<GUID>.txt` şeklindedir. Bu dosyayı açın, içinde 2 satır kaydı göreceksiniz.

    ```
    6,newdata,2017-09-06 02:23:00.0000000
    7,newdata,2017-09-07 09:01:00.0000000    
    ```
2. `watermarktable` içindeki en son değeri denetleyin. Eşik değerinin tekrar güncelleştirildiğini görürsünüz.

    ```sql
    Select * from watermarktable
    ```
    örnek çıktı:

    | TableName | WatermarkValue |
    | --------- | --------------- |
    | data_source_table | 2017-09-07 09:01:00.000 |



## <a name="next-steps"></a>Sonraki adımlar
Bu öğreticide aşağıdaki adımları gerçekleştirdiniz:

> [!div class="checklist"]
> * Eşik değerini depolamak için veri deposunu hazırlama.
> * Veri fabrikası oluşturma.
> * Bağlı hizmet oluşturma.
> * Kaynak, havuz ve eşik veri kümeleri oluşturun.
> * İşlem hattı oluşturma.
> * İşlem hattını çalıştırma.
> * İşlem hattı çalıştırmasını izleme.
> * Sonuçları gözden geçirme
> * Kaynağa daha fazla veri ekleyin.
> * İşlem hattını yeniden çalıştırın.
> * İkinci işlem hattı çalıştırmasını izleme
> * İkinci çalıştırmanın sonuçlarını gözden geçirme

Bu öğreticide, işlem hattı SQL veritabanındaki tek bir tablodan Blob depolama alanına veri kopyalamıştır. Şirket içi SQL Server veritabanındaki birden çok tablodan SQL veritabanına veri kopyalama hakkında bilgi edinmek için şu öğreticiye ilerleyin.

> [!div class="nextstepaction"]
>[SQL Server’daki birden fazla tablodan Azure SQL Veritabanı’na artımlı olarak veri yükleme](tutorial-incremental-copy-multiple-tables-portal.md)
