---
title: Azure portal ile Azure dosya paylaşımlarını yönetme hızlı başlangıcı
description: Azure portaldan Azure Dosyaları'nı yönetmeyi öğrenmek için bu hızlı başlangıcı kullanın.
author: roygara
ms.service: storage
ms.topic: quickstart
ms.date: 10/18/2018
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: fef3daf6e9e535736002e309e3d27491364dc553
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/26/2020
ms.locfileid: "71260298"
---
# <a name="quickstart-create-and-manage-azure-file-shares-with-the-azure-portal"></a>Hızlı Başlangıç: Azure portal ile Azure dosya paylaşımları oluşturma ve yönetme 
[Azure Dosyaları](storage-files-introduction.md), Microsoft’un kullanımı kolay bulut dosya sistemidir. Azure dosya paylaşımları, Windows, Linux ve macOS platformlarına bağlanabilir. Bu kılavuzda, [Azure portalını](https://portal.azure.com/) kullanarak Azure dosya paylaşımlarıyla çalışmanın temel bilgileri gösterilmektedir.

Azure aboneliğiniz yoksa, başlamadan önce [ücretsiz](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) bir hesap oluşturun.

## <a name="create-a-storage-account"></a>Depolama hesabı oluşturma
[!INCLUDE [storage-files-create-storage-account-portal](../../../includes/storage-files-create-storage-account-portal.md)]

## <a name="create-an-azure-file-share"></a>Azure dosya paylaşımı oluşturma
Azure dosya paylaşımı oluşturmak için:

1. Panonuzdan depolama hesabını seçin.
2. Depolama hesabı sayfasında, **Hizmetler** bölümünden **Dosyalar**’ı seçin.
    ![Depolama hesabının hizmetler bölümünün anlık görüntüsü; Dosyalar hizmetini seçin](media/storage-how-to-use-files-portal/create-file-share-1.png)

3. **Dosya hizmeti** sayfasının üst kısmındaki menüde **Dosya paylaşımı'nı**tıklatın. **Yeni dosya paylaşımı** sayfası aşağı doğru açılır.
4. **Ad** alanına *myshare* yazın.
5. **Tamam**’a tıklayarak Azure dosya paylaşımını oluşturun.

Paylaşım adları tümüyle küçük harf, sayı ve tek kısa çizgilerden oluşmalı ve kısa çizgiyle başlamamalıdır. Dosya paylaşımlarını ve dosyalarını adlandırma hakkında ayrıntılı bilgi için, [Paylaşımları, Dizinleri, Dosyaları ve Meta Verileri Adlandırma ve Başvurma bölümüne](https://docs.microsoft.com/rest/api/storageservices/Naming-and-Referencing-Shares--Directories--Files--and-Metadata)bakın.

## <a name="use-your-azure-file-share"></a>Azure dosya paylaşımınızı kullanma
Azure Dosyaları, Azure dosya paylaşımınızdaki dosya ve klasörler ile çalışmak için iki yöntem sunar: sektör standardı [Sunucu İleti Bloğu (SMB) protokolü](https://msdn.microsoft.com/library/windows/desktop/aa365233.aspx) ve [Dosya REST protokolü](https://docs.microsoft.com/rest/api/storageservices/file-service-rest-api). 

Bir dosya paylaşımını SMB ile bağlayabilmeniz için işletim sisteminize göre aşağıdaki belgeye bakın:
- [Windows](storage-how-to-use-files-windows.md)
- [Linux](storage-how-to-use-files-linux.md)
- [Macos](storage-how-to-use-files-mac.md)

### <a name="using-an-azure-file-share-from-the-azure-portal"></a>Azure portalda Azure dosya paylaşımını kullanma
Azure portalı üzerinde gelen tüm istekler Dosya REST API ile yapılır; böylelikle SMB erişimi olmadan istemcilerdeki dosyaları ve dizinleri oluşturabilir, değiştirebilir ve silebilirsiniz. Doğrudan Dosya REST protokolü yle çalışmak mümkündür (diğer bir deyişle, HANDcrafting REST HTTP kendiniz çağırır), ancak Dosya REST protokolünü kullanmanın en yaygın yolu (Azure portalını kullanmanın ötesinde) [Azure PowerShell modülünü](storage-how-to-use-files-powershell.md), [Azure CLI](storage-how-to-use-files-cli.md)veya azure depolama SDK'sını kullanmaktır ve bunların hepsi seçtiğiniz komut dosyası/programlama dilinde Dosya REST protokolü nün etrafında güzel bir sarmalayıcı sağlar. 

Azure Dosyaları kullanıcılarının çoğunun SMB protokolü üzerinden Azure dosya paylaşımlarıyla çalışmak isteyeceğini bekliyoruz, çünkü bu, kullanabilmeyi bekledikleri varolan uygulamaları ve araçları kullanmalarına olanak sağlıyor, ancak Dosya REST API'sini kullanmanın avantajlı olmasının birkaç nedeni vardır gibi SMB yerine,

- SMB erişiminizin olmadığı bir dizüstü bilgisayar, tablet veya mobil cihaz vb. ile hareket halindeyken Azure dosya paylaşımınızda hızlı bir değişiklik yapmanız gerekir.
- SMB paylaşımını bağlayamayan istemcilerden; örneğin 445 numaralı bağlantı noktası engeli kaldırılmamış şirket içi bir istemciden bir betik veya uygulama yürütmeniz gerekiyorsa.
- [Azure İşlevleri](../../azure-functions/functions-overview.md) gibi sunucusuz kaynaklardan yararlanıyorsanız. 

Aşağıdaki örnekler, Azure portalın Azure dosya paylaşımınızı Dosya REST protokolü ile yönetmek için nasıl kullanılacağını göstermektedir. 

Artık Azure dosya paylaşımını oluşturduğunuza göre, [Windows](storage-how-to-use-files-windows.md), [Linux](storage-how-to-use-files-linux.md) veya [macOS](storage-how-to-use-files-mac.md)'ta SMB ile dosya paylaşımını bağlayabilirsiniz. Alternatif olarak, Azure dosya paylaşımınızla Azure portalını kullanarak çalışabilirsiniz. 

#### <a name="create-a-directory"></a>Dizin oluşturma
Azure dosya paylaşımınızın kökünde *myDirectory* adlı yeni bir dizin oluşturmak için:

1. **Dosya Hizmeti** sayfasında **myshare** dosya paylaşımını seçin. Dosya paylaşımınızın sayfası açılır.
2. Sayfanın en üstündeki menüden **+ Dizin ekle**’yi seçin. **Yeni dizin** sayfası aşağı doğru açılır.
3. *myDirectory* yazın ve **Tamam**’a tıklayın.

#### <a name="upload-a-file"></a>Dosyayı karşıya yükleme 
Bir dosyayı karşıya yüklemeyi göstermek için önce karşıya yüklenecek bir dosya oluşturmanız veya seçmeniz gerekir. Uygun gördüğünüz herhangi bir yolla bunu yapabilirsiniz. Karşıya yüklemek istediğiniz dosyayı seçtikten sonra:

1. **myDirectory** dizinine tıklayın. **myDirectory** paneli açılır.
2. En üstteki menüde **Karşıya Yükle**’ye tıklayın. **Dosyaları karşıya yükleme** paneli açılır.  
    ![Dosyaları karşıya yükleme panelinin ekran görüntüsü](media/storage-how-to-use-files-portal/upload-file-1.png)

3. Yerel dosyalarınıza göz atmak amacıyla bir pencereyi açmak için klasör simgesine tıklayın. 
4. Bir dosya seçin ve **Aç**’a tıklayın. 
5. **Dosyaları karşıya yükleme** sayfasında, dosya adını doğrulayın ve **Karşıya Yükle**’ye tıklayın.
6. Tamamlandığında, dosyanın **myDirectory** sayfasındaki listede gösterilmesi gerekir.

#### <a name="download-a-file"></a>Dosya indirme
Dosyaya sağ tıklayarak, karşıya yüklediğiniz dosyanın bir kopyasını indirebilirsiniz. İndir düğmesine tıklandıktan sonra olacak işlemler, kullandığınız işletim sistemine ve tarayıcıya bağlıdır.

## <a name="clean-up-resources"></a>Kaynakları temizleme
[!INCLUDE [storage-files-clean-up-portal](../../../includes/storage-files-clean-up-portal.md)]

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [Azure Dosyaları nedir?](storage-files-introduction.md)
