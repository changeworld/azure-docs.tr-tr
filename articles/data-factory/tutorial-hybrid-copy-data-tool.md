---
title: Azure Kopya Veri aracını kullanarak şirket içi verileri kopyalama
description: Şirket içi bir SQL Server veritabanındaki verileri Azure Blob depolamaya kopyalamak için bir Azure veri fabrikası oluşturun ve Veri Kopyalama aracını kullanın.
services: data-factory
ms.author: abnarain
author: nabhishek
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: tutorial
ms.custom: seo-lt-2019
ms.date: 04/09/2018
ms.openlocfilehash: 1d8c68550d294534178fd6094b71fd6a7f1d1c46
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/24/2020
ms.locfileid: "75977371"
---
# <a name="copy-data-from-an-on-premises-sql-server-database-to-azure-blob-storage-by-using-the-copy-data-tool"></a>Veri Kopyalama aracını kullanarak şirket içi bir SQL Server veritabanındaki verileri Azure Blob depolamaya kopyalama
> [!div class="op_single_selector" title1="Kullandığınız Veri Fabrikası hizmetisürümünü seçin:"]
> * [Sürüm 1](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
> * [Geçerli sürüm](tutorial-hybrid-copy-data-tool.md)

Bu öğreticide, Azure portalını kullanarak bir veri fabrikası oluşturursunuz. Sonra, Veri Kopyalama aracını kullanarak şirket içi bir SQL Server veritabanındaki verileri Azure Blob depolamaya kopyalarsınız.

> [!NOTE]
> - Azure Data Factory’yi ilk kez kullanıyorsanız bkz. [Data Factory'ye giriş](introduction.md).

Bu öğreticide, aşağıdaki adımları gerçekleştireceksiniz:

> [!div class="checklist"]
> * Veri fabrikası oluşturma.
> * Veri Kopyalama aracını kullanarak bir işlem hattı oluşturun.
> * İşlem hattı ve etkinlik çalıştırmalarını izleme.

## <a name="prerequisites"></a>Ön koşullar
### <a name="azure-subscription"></a>Azure aboneliği
Başlamadan önce, mevcut bir Azure aboneliğiniz yoksa [ücretsiz hesap oluşturun](https://azure.microsoft.com/free/).

### <a name="azure-roles"></a>Azure rolleri
Veri fabrikası örnekleri oluşturmak için, Azure'da oturum açmak için kullandığınız kullanıcı hesabına bir *Katılımcı* veya *Sahip* rolü atanmalı veya Azure aboneliğinin *yöneticisi* olmalıdır.

Abonelikte sahip olduğunuz izinleri görüntülemek için Azure portalına gidin. Sağ üst köşeden kullanıcı adınızı ve sonra **İzinler**’i seçin. Birden çok aboneliğe erişiminiz varsa uygun aboneliği seçin. Bir role kullanıcı eklemeye ilişkin örnek yönergeler için, bkz. [RBAC ve Azure portalı kullanarak erişimi yönetme](../role-based-access-control/role-assignments-portal.md).

### <a name="sql-server-2014-2016-and-2017"></a>SQL Server 2014, 2016 ve 2017
Bu öğreticide, şirket içi SQL Server veritabanını bir *kaynak* veri deposu olarak kullanırsınız. Bu öğreticide oluşturduğunuz veri fabrikasındaki işlem hattı, verileri bu şirket içi SQL Server veritabanından (kaynak) Blob depolama alanına (havuz) kopyalar. Daha sonra SQL Server veritabanınızda **emp** adında bir tablo oluşturur sunuz ve tabloya birkaç örnek giriş eklersiniz.

1. SQL Server Management Studio’yu başlatın. Makinenizde zaten yüklü değilse [SQL Server Management Studio'yu indirme](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) sayfasına gidin.

1. Kimlik bilgilerinizi kullanarak SQL Server örneğinize bağlanın.

1. Örnek bir veritabanı oluşturun. Ağaç görünümünde **Veritabanları**'na sağ tıklayın ve **Yeni Veritabanı**'nı seçin.

1. **Yeni Veritabanı** penceresinde, veritabanı için bir ad girin ve **Tamam**'ı seçin.

1. **Emp** tablosunu oluşturmak ve içine bazı örnek veriler eklemek için aşağıdaki sorgu komut dosyasını veritabanına karşı çalıştırın. Ağaç görünümünde, oluşturduğunuz veritabanına sağ tıklayın ve **Yeni Sorgu**'yu seçin.

    ```sql
    CREATE TABLE dbo.emp
    (
        ID int IDENTITY(1,1) NOT NULL,
        FirstName varchar(50),
        LastName varchar(50)
    )
    GO

    INSERT INTO emp (FirstName, LastName) VALUES ('John', 'Doe')
    INSERT INTO emp (FirstName, LastName) VALUES ('Jane', 'Doe')
    GO
    ```

### <a name="azure-storage-account"></a>Azure depolama hesabı
Bu öğreticide, genel amaçlı Azure depolama hesabını (özel olarak Blob depolama) hedef/havuz veri deposu olarak kullanırsınız. Genel amaçlı bir depolama hesabınız yoksa yeni hesap oluşturma yönergeleri için bkz. [Depolama hesabı oluşturma](../storage/common/storage-account-create.md). Bu öğreticide oluşturduğunuz veri fabrikasındaki işlem hattı, verileri şirket içi SQL Server veritabanından (kaynak) bu Blob depolama alanına (havuz) kopyalar. 

#### <a name="get-the-storage-account-name-and-account-key"></a>Depolama hesabı adını ve hesap anahtarını alma
Bu öğreticide, depolama hesabınızın adını ve anahtarını kullanırsınız. Depolama hesabınızın adını ve anahtarını almak için aşağıdaki adımları gerçekleştirin:

1. Azure kullanıcı adı ve parolanızla [Azure portalında](https://portal.azure.com) oturum açın.

1. Sol bölmede **Tüm hizmetler**'i seçin. **Depolama** anahtar sözcüğünü kullanarak filtreleyin ve **Depolama hesapları**’nı seçin.

    ![Depolama hesabı araması](media/doc-common-process/search-storage-account.png)

1. Depolama hesapları listesinde, depolama hesabınız için filtre uygulayın (gerekirse). Sonra depolama hesabınızı seçin.

1. **Depolama hesabı** penceresinde **Erişim anahtarları**'nı seçin.


1. **Depolama hesabı adı** ve **key1** kutularında değerleri kopyalayın ve ardından onları öğreticide daha sonra kullanmak için Not Defteri'ne veya başka bir düzenleyiciye yapıştırın.

#### <a name="create-the-adftutorial-container"></a>Adftutorial kapsayıcını oluşturma
Bu bölümde, Blob depolama alanınızda **adftutorial** adlı bir blob kapsayıcısı oluşturursunuz.

1. **Depolama hesabı** penceresinde **Genel Bakış**’a geçin ve sonra **Bloblar**’ı seçin.

1. **Blobs** penceresinde + **Kapsayıcı'yı**seçin.

1. Yeni **kapsayıcı** penceresinde, **Ad**altında , **adftutorial**girin ve sonra **Tamam'ı**seçin.

1. Kapsayıcılar listesinde **adftutorial**’ı seçin.


1. **adftutorial** öğesine ait **Kapsayıcı** penceresini açık tutun. Öğreticinin sonundaki çıktıyı doğrulamak için kullanırsınız. Data Factory bu kapsayıcıda çıktı klasörünü otomatik olarak oluşturduğundan sizin oluşturmanız gerekmez.


## <a name="create-a-data-factory"></a>Veri fabrikası oluşturma

1. Soldaki menüde + **Kaynak** > **Analitik** > **Veri Fabrikası**Oluştur'u seçin.

   ![Yeni veri fabrikası oluşturma](./media/doc-common-process/new-azure-data-factory-menu.png)

1. **Yeni veri fabrikası** sayfasında **Ad** bölümüne **ADFTutorialDataFactory** girin.

   Veri fabrikasının adı *genel olarak benzersiz* olmalıdır. Ad alanı için aşağıdaki hata iletisini görürseniz veri fabrikasının adını değiştirin (örneğin, adınızADFTutorialDataFactory). Data Factory yapıtlarının adlandırma kuralları için bkz.[Data Factory adlandırma kuralları](naming-rules.md).

   ![Yeni veri fabrikasının adı](./media/doc-common-process/name-not-available-error.png)
1. Veri fabrikasını oluşturmak istediğiniz Azure **aboneliğinizi** seçin.
1. **Kaynak Grubu** için aşağıdaki adımlardan birini uygulayın:

   - **Var olanı kullan**’ı seçin ve ardından açılır listeden var olan bir kaynak grubu belirleyin.

   - **Yeni oluştur**’u seçin ve bir kaynak grubunun adını girin. 
        
     Kaynak grupları hakkında daha fazla bilgi için bkz. [Azure kaynaklarınızı yönetmek için kaynak gruplarını kullanma](../azure-resource-manager/management/overview.md).
1. **Sürüm** bölümünde **V2**'yi seçin.
1. **Konum**altında, veri fabrikasının konumunu seçin. Açılan listede yalnızca desteklenen konumlar görüntülenir. Veri Fabrikası tarafından kullanılan veri depoları (örneğin, Azure Depolama ve SQL Veritabanı) ve işlemler (örneğin, Azure HDInsight) başka konumlarda/bölgelerde olabilir.
1. **Oluştur'u**seçin.

1. Oluşturma işlemi bittikten sonra, resimde gösterildiği gibi **Veri Fabrikası** sayfası görüntülenir.

     ![Data factory giriş sayfası](./media/doc-common-process/data-factory-home-page.png)
1. Azure Data Factory kullanıcı arabirimini ayrı bir sekmede açmak için **Geliştir ve İzle**’yi seçin.

## <a name="use-the-copy-data-tool-to-create-a-pipeline"></a>Veri Kopyalama aracını kullanarak işlem hattı oluşturma

1. **Başlayalım** sayfasında, Veri Kopyalama aracını açmak için **Veri Kopyala**’yı seçin.

   ![Başlarken sayfası](./media/doc-common-process/get-started-page.png)

1. Veri Kopyalama aracının **Özellikler** sayfasında, **Görev adı** bölümüne **CopyFromOnPremSqlToAzureBlobPipeline** adını girin. Ardından **İleri'yi**seçin. Veri Kopyalama aracı, bu alan için belirttiğiniz ada sahip bir işlem hattı oluşturur.
  ![Görev adı](./media/tutorial-hybrid-copy-data-tool/properties-page.png)

1. **Kaynak veri deposu** sayfasında **Yeni bağlantı oluştur**'a tıklayın.


1. **Yeni Bağlantılı Hizmet**altında, SQL **Server'ı**arayın ve ardından **Devam et'i**seçin.

1. Yeni **Bağlantılı Hizmet (SQL Server)** iletişim kutusunda, **Adı**altında, **SqlServerLinkedService**girin. Entegrasyon çalışma zamanı ile **+Yeni'yi** ve **tümleştirme ile Bağlan'ı**seçin. Şirket içinde barındırılan bir tümleştirme çalışma zamanı oluşturup bunu makinenize indirmeniz ve Data Factory’ye kaydetmeniz gerekir. Şirket içinde barındırılan tümleştirme çalışma zamanı, şirket içi ortamınızla bulut arasında veri kopyalar.


1. **Tümleştirme Runtime Kurulumu** iletişim kutusunda, **Kendi Kendine Barındırılan'ı**seçin. Ardından **İleri'yi**seçin.

   ![Tümleştirme çalışma zamanı oluşturma](./media/tutorial-hybrid-copy-data-tool/create-integration-runtime-dialog0.png)

1. **Tümleştirme Runtime Kurulum** iletişim kutusunda, **Adı**altında, **TutorialIntegrationRuntime**girin. Ardından **İleri'yi**seçin.


1. **Tümleştirme Çalışma Zamanı Kurulumu** iletişim kutusunda, **bu bilgisayarın ekspres kurulumunu başlatmak için buraya tıklayın'ı**seçin. Bu işlem, tümleştirme çalışma zamanını makinenize yükler ve Data Factory’ye kaydeder. Alternatif olarak, el ile kurulum seçeneğini kullanarak yükleme dosyasını indirip çalıştırabilir ve anahtarı kullanarak tümleştirme çalışma zamanını kaydedebilirsiniz.

1. İndirilen uygulamayı çalıştırın. Pencerede hızlı kurulum durumunu görürsünüz.

    ![Hızlı kurulum durumu](./media/tutorial-hybrid-copy-data-tool/express-setup-status.png)

1. Yeni **Bağlantılı Hizmet (SQL Server)** iletişim kutusunda, **TutorialIntegrationRuntime'ın** Tümleştirme Çalışma Süresi alanı için seçildiğini doğrulayın. Ardından, aşağıdaki adımları izleyin:

    a. **Ad** bölümüne **SqlServerLinkedService** adını girin.

    b. **Sunucu adı** bölümüne şirket içi SQL Server örneğinizin adını girin.

    c. **Veritabanı adı** bölümüne şirket içi veritabanınızın adını girin.

    d. **Kimlik doğrulaması türü** bölümünde uygun kimlik doğrulamasını seçin.

    e. **Kullanıcı adı** bölümüne şirket içi SQL Server’a erişimi olan kullanıcının adını girin.

    f. Kullanıcının **parolasını** girin.

    g. Bağlantıyı test edin ve **Finish'i**seçin.

      ![Tümleştirme çalışma zamanı seçildi](./media/tutorial-hybrid-copy-data-tool/integration-runtime-selected.png)

1. Kaynak **veri deposu** sayfasında **İleri'yi**seçin.

1. **Verilerin kopyalanacağı tabloları seçin veya özel sorgu kullanın** sayfasında, listeden **[dbo].[emp]** tablosunu seçip **İleri**’yi seçin. Veritabanınıza göre diğer tablolardan da seçim yapabilirsiniz.

1. **Hedef veri deposu** sayfasında **Yeni bağlantı oluştur**'u seçin


1. **Yeni Bağlantılı Hizmette**, Arama ve **Azure Blob'u**seçin ve ardından **Devam et'i**seçin.

   ![Blob depolama seçimi](./media/tutorial-hybrid-copy-data-tool/select-destination-data-store.png)

1. **Yeni Bağlı Hizmet (Azure Blob Depolama)** iletişim kutusunda aşağıdaki adımları uygulayın:

   a. **Ad** bölümüne **AzureStorageLinkedService** adını girin.

   b. **Tümleştirme çalışma zamanıyla bağlan** bölümünde **TutorialIntegrationRuntime** öğesini seçin

   c. **Depolama hesabı adı** bölümünde, açılan listeden depolama hesabınızı seçin.

   d. **Bitiş'i**seçin.

1. **Hedef veri deposu** iletişim kutusunda, Azure **Blob Depolama'nın** seçildiğinden emin olun. Ardından **İleri'yi**seçin.

1. **Çıkış dosyasını veya klasörünü seçin** iletişim kutusunda, **Klasör yolu** bölümüne **adftutorial/fromonprem** yolunu girin. Ön koşulların bir parçası olarak **adftutorial** kapsayıcısını oluşturdunuz. Çıkış klasörü yoksa (bu örnekte **fromonprem**), Data Factory tarafından otomatik olarak oluşturulur. Blob depolama alanına ve kapsayıcılarına/klasörlerine göz atmak için **Gözat** düğmesini de kullanabilirsiniz. **Dosya adı** bölümünde değer belirtmezseniz varsayılan olarak kaynaktaki ad (bu örnekte **dbo.emp**) kullanılır.

   ![Çıktı dosyasını veya klasörünü seçin](./media/tutorial-hybrid-copy-data-tool/choose-output-file-folder.png)

1. **Dosya biçimi ayarları** iletişim kutusunda **İleri**’yi seçin.

1. **Ayarlar** iletişim kutusunda **İleri**’yi seçin.

1. **Özet** iletişim kutusunda tüm ayarların değerlerini gözden geçirin ve **İleri**’yi seçin.

1. **Dağıtım** sayfasında, oluşturduğunuz işlem hattını veya görevi izlemek için **İzleyici**’yi seçin.

   ![Dağıtım sayfası](./media/tutorial-hybrid-copy-data-tool/deployment-page.png)

1. **İzleyici** sekmesinde, oluşturduğunuz işlem hattının durumunu görüntüleyebilirsiniz. İşlem hattı çalıştırmasıyla ilişkili etkinlik çalıştırmalarını görüntülemek ve işlem hattını yeniden çalıştırmak için **Eylem** sütunundaki bağlantıları kullanabilirsiniz.

1. İşlem hattı çalıştırmalarıyla ilişkili etkinlik çalıştırmalarını görmek için **Eylemler** sütunundaki **Etkinlik Çalıştırmalarını Görüntüle** bağlantısını seçin. Kopyalama işlemiyle ilgili ayrıntıları görmek için **Eylemler** sütunundaki **Ayrıntılar** bağlantısını (gözlük simgesi) seçin. **Pipeline Runs** görünümüne geri dönmek için, en üstte **Pipeline Runs'ı** seçin.

1. Çıktı dosyasını **adftutorial** kapsayıcısının **fromonprem** klasöründe gördüğünüzü onaylayın.


1. Düzenleyici moduna geçmek için soldaki **Düzenle** sekmesini seçin. Düzenleyiciyi kullanarak araç tarafından oluşturulan bağlı hizmetleri, veri kümelerini ve işlem hatlarını güncelleştirebilirsiniz. Düzenleyicide açılan varlıkla ilişkili JSON kodunu görüntülemek için **Kod**’u seçin. Bu varlıklarım Data Factory kullanıcı arabiriminde nasıl düzenleneceği ile ilgili ayrıntılar için [bu öğreticinin Azure portalı sürümüne](tutorial-copy-data-portal.md) bakın.

   ![Düzenle sekmesi](./media/tutorial-hybrid-copy-data-tool/edit-tab.png)


## <a name="next-steps"></a>Sonraki adımlar
Bu örnekteki işlem hattı, şirket içi bir SQL Server veritabanındaki verileri Blob depolama alanına kopyalar. Şunları öğrendiniz:

> [!div class="checklist"]
> * Veri fabrikası oluşturma.
> * Veri Kopyalama aracını kullanarak bir işlem hattı oluşturun.
> * İşlem hattı ve etkinlik çalıştırmalarını izleme.

Data Factory tarafından desteklenen veri depolarının listesi için [Desteklenen veri depoları](copy-activity-overview.md#supported-data-stores-and-formats) konusuna bakın.

Verilerin toplu olarak kaynaktan hedefe nasıl kopyalanacağı hakkında bilgi edinmek için aşağıdaki öğreticiye geçin:

> [!div class="nextstepaction"]
>[Verileri toplu halde kopyalama](tutorial-bulk-copy-portal.md)
