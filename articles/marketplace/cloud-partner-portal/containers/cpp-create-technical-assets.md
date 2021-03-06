---
title: Azure Kapsayıcıları görüntü teknik varlıkları oluşturma | Azure Marketi
description: Azure kapsayıcısı için teknik varlıkları oluşturun.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 11/01/2018
ms.author: dsindona
ms.openlocfilehash: 1116bd03cf0ef734b74b387ff8f3cff74b29b215
ms.sourcegitcommit: 530e2d56fc3b91c520d3714a7fe4e8e0b75480c8
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/14/2020
ms.locfileid: "81270324"
---
# <a name="prepare-your-container-technical-assets"></a>Kapsayıcınızın teknik varlıklarını hazırlama

> [!IMPORTANT]
> 13 Nisan 2020'den itibaren Azure Kapsayıcı tekliflerinizin yönetimini İş Ortağı Merkezi'ne taşımaya başlayacağız. Geçişten sonra, Tekliflerinizi İş Ortağı Merkezi'nde oluşturur ve yönetirsiniz. Aktarılan tekliflerinizi yönetmek için [Azure Kapsayıcıteknik varlıklarınızı hazırlayın'daki](https://aka.ms/CreateContainerTechAssets) yönergeleri izleyin.

Bu makalede, azure marketplace'e bir kapsayıcı teklifini yapılandırma adımları ve gereksinimleri açıklanmaktadır.

## <a name="before-you-begin"></a>Başlamadan önce

Hızlı [Başlangıçlar,](https://docs.microsoft.com/azure/container-instances) Öğreticiler ve Örnekler sağlayan Azure Kapsayıcı Örnekleri belgelerini gözden geçirin.

## <a name="fundamental-technical-knowledge"></a>Temel teknik bilgi

Bu varlıkların tasarlanması, oluşturulması ve test edilmesi zaman alır ve hem Azure platformu hem de teklifi oluşturmak için kullanılan teknolojiler hakkında teknik bilgi gerektirir.
 
Çözüm etki alanınıza ek olarak, mühendislik ekibiniz aşağıdaki Microsoft teknolojileri hakkında bilgi sahibi olmalıdır:

-    [Azure Hizmetlerinin](https://azure.microsoft.com/services/) temel anlayışı 
-    Azure uygulamaları nasıl [tasarlar ve tasarlar](https://azure.microsoft.com/solutions/architecture/)
-    [Azure Sanal Makineler,](https://azure.microsoft.com/services/virtual-machines/) [Azure Depolama](https://azure.microsoft.com/services/?filter=storage) ve [Azure Ağı](https://azure.microsoft.com/services/?filter=networking) çalışma bilgisi
-    [Azure Kaynak Yöneticisi'nin](https://azure.microsoft.com/features/resource-manager/) çalışma bilgisi
-    [JSON](https://www.json.org/) Çalışma Bilgisi

## <a name="suggested-tools"></a>Önerilen araçlar

Kapsayıcı görüntünüzün yönetilmesine yardımcı olmak için aşağıdaki komut dosyası ortamlarından birini veya her ikisini seçin:

-    [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview)
-    [Azure CLI](https://docs.microsoft.com/cli/azure)

Ayrıca, geliştirme ortamınıza aşağıdaki araçları eklemenizi öneririz:

-    [Azure Depolama Gezgini](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer)
-    [Visual Studio Code](https://code.visualstudio.com/)
    *    Uzantı: [Azure Kaynak Yöneticisi Araçları](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools)
    *    Uzantısı: [Güzelleştirmek](https://marketplace.visualstudio.com/items?itemName=HookyQR.beautify)
    *    Uzatma: [Prettify JSON](https://marketplace.visualstudio.com/items?itemName=mohsen1.prettify-json)

[Ayrıca Azure Geliştirici Araçları](https://azure.microsoft.com/tools/) sayfasında ve Visual Studio kullanıyorsanız, Visual Studio [Marketplace'te](https://marketplace.visualstudio.com/)mevcut araçları incelemenizi öneririz.

## <a name="create-the-container-image"></a>Kapsayıcı görüntüsünü oluşturma

Daha fazla bilgi için aşağıdakibilgilere bakın:

* [Öğretici: Azure Kapsayıcı Örneklerine dağıtım için kapsayıcı görüntüsü oluşturma](https://docs.microsoft.com/azure/container-instances/container-instances-tutorial-prepare-app)
* [Öğretici: Azure Kapsayıcı Kayıt Defteri Görevleri ile bulutta kapsayıcı görüntüleri oluşturma ve dağıtma](https://docs.microsoft.com/azure/container-registry/container-registry-tutorial-quick-task)

## <a name="next-steps"></a>Sonraki adımlar

[Konteyner teklifinizi oluşturun](./cpp-create-offer.md)
