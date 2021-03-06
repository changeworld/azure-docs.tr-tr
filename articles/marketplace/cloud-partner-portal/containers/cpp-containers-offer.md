---
title: Azure Kapsayıcılar resim teklifi | Azure Marketi
description: Azure Marketi'nde bir kapsayıcı teklifi yayımlama işlemine genel bakış.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 11/02/2018
ms.author: dsindona
ms.openlocfilehash: cd9f98d42efcb35dbab4f3c0a06c5a11360e36b6
ms.sourcegitcommit: 530e2d56fc3b91c520d3714a7fe4e8e0b75480c8
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/14/2020
ms.locfileid: "81270358"
---
# <a name="containers"></a>Kapsayıcılar

> [!IMPORTANT]
> 13 Nisan 2020'den itibaren Azure Kapsayıcı tekliflerinizin yönetimini İş Ortağı Merkezi'ne taşımaya başlayacağız. Geçişten sonra, Tekliflerinizi İş Ortağı Merkezi'nde oluşturur ve yönetirsiniz. Geçirilen tekliflerinizi yönetmek için [Azure Kapsayıcısı Oluştur teklifinde](https://aka.ms/CreateContainerOffer) yönergeleri izleyin.

<table> <tr> <td>Bu bölümde, bir kapsayıcı görüntüsünün <a href="https://azuremarketplace.microsoft.com">Azure Marketi'nde</a>nasıl yayımlanılabildiğini açıklanmaktadır.  
Kapsayıcı teklif türü, <a href="https://docs.microsoft.com/azure/aks/index">Azure Kubernetes Hizmeti</a> örnekleri veya <a href="https://docs.microsoft.com/azure/container-instances/container-instances-overview">Azure Kapsayıcı Örnekleri</a> olarak sağlanan ve bir Azure Kapsayıcı Kayıt <a href="https://docs.microsoft.com/azure/container-registry">Defteri</a> deposunda barındırılan Docker kapsayıcı görüntülerini destekler. </td> <td><img src="./media/container-icon.png"  alt="Azure container icon" /></td> </tr> </table>

## <a name="offer-components"></a>Teklif bileşenleri

Bu bölümde kapsayıcı yayımlama nın öğeleri özetlenir ve yayımcı için Azure Marketi için bir kılavuz olarak tasarlanmıştır. Yayıncılık aşağıdaki ana bölümlere ayrılmıştır:

- [Ön koşullar](./cpp-prerequisites.md) - bir konteyner teklifi oluşturmadan veya yayımlamadan önce teknik ve iş gereksinimlerini listeler.
- [Teklif oluşturun](./cpp-create-offer.md) -Bulut İş Ortağı Portalı'nı kullanarak yeni bir kapsayıcı teklifi girişi oluşturmak için gereken adımları listeler.
- [Azure Marketi'nde](./cpp-create-technical-assets.md) teklif olarak bir kapsayıcı çözümü için teknik varlıkların nasıl oluşturulabileceği gibi teknik varlıkları hazırlayın.
- [Teklifi yayımlayın](./cpp-publish-offer.md) - azure marketine yayımlama teklifini nasıl gönderebilirsiniz.

## <a name="container-publishing-process"></a>Kapsayıcı yayımlama süreci

Aşağıdaki diyagram, VM teklifini yayımlamadaki üst düzey adımları göstermektedir.
![Teklif yayımlama adımları](./media/containers-offer-process.png)

Kapsayıcı teklifini yayımlamanın üst düzey adımları şunlardır:

1. Teklifi oluşturun - Teklif hakkında ayrıntılı bilgi sağlayın. Bu bilgiler şunları içerir: teklif açıklaması, pazarlama malzemeleri, destek bilgileri ve varlık özellikleri.
2. İş ve teknik varlıkları oluşturun - İlişkili çözüm için iş varlıkları (yasal belgeler ve pazarlama malzemeleri) ve teknik varlıkları (Azure Konteyner Kayıt Defteri'nde barındırılan kapsayıcı görüntüleri) oluşturun.
3. SKU oluşturun - Teklifle ilişkili SKU(lar)ı oluşturun. Yayınlamayı planladığınız her resim için benzersiz bir SKU gereklidir.
4. Teklifi onaylayın ve yayınlayın - Teklif ve teknik varlıklar tamamlandıktan sonra teklifi iletebilirsiniz. Bu gönderme yayımlama işlemini başlatır. Bu işlem sırasında çözüm test edilir, doğrulanır, onaylanır ve Azure Marketi'nde "yayınlanır".

## <a name="next-steps"></a>Sonraki adımlar

Bu adımları düşünmeden önce, bir kapsayıcıyı Microsoft Azure Marketi'nde yayımlamak için [teknik ve iş gereksinimlerini](./cpp-prerequisites.md) karşılamanız gerekir.
