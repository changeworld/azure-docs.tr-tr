---
title: Azure portalını kullanarak medya işlemeyi ölçeklendirin | Microsoft Dokümanlar
description: Bu öğretici, Azure portalını kullanarak ortam işlemeyi ölçekleme adımlarında size yol açar.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.assetid: e500f733-68aa-450c-b212-cf717c0d15da
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/19/2019
ms.author: juliako
ms.openlocfilehash: c840764dc978a8dacb3450c0aca5e5d93284b8a6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/27/2020
ms.locfileid: "61127558"
---
# <a name="change-the-reserved-unit-type"></a>Ayrılmış birim türünü değiştirme
> [!div class="op_single_selector"]
> * [.NET](media-services-dotnet-encoding-units.md)
> * [Portal](media-services-portal-scale-media-processing.md)
> * [Geri kalanı](https://docs.microsoft.com/rest/api/media/operations/encodingreservedunittype)
> * [Java](https://github.com/southworkscom/azure-sdk-for-media-services-java-samples)
> * [PHP](https://github.com/Azure/azure-sdk-for-php/tree/master/examples/MediaServices)
> 
> 

## <a name="overview"></a>Genel Bakış

Media Services hesabı bir Ayrılmış Birim Türüyle ilişkilendirilir ve bu da medya işleme görevlerinizin ne hızda işleneceğini belirler. Aşağıdaki ayrılmış birim türleri arasından seçim yapabilirsiniz: **S1**, **S2**, veya **S3**. Örneğin, aynı kodlama işi **S2** ayrılmış birim türünü kullandığınızda **S1** türüne göre daha hızlı çalışır.

Ayrılmış birim türünü belirtmenin yanı sıra, hesabınızı Ayrılmış **Birimler** (RUS) ile birlikte sağlamanızı belirtebilirsiniz. Sağlanan RU sayısı, verili bir hesapta eşzamanlı olarak işlenebilecek medya görevlerinin sayısını belirler.

>[!NOTE]
>RU, tüm medya işlemesini paralel hale getirmek için çalışır ve Azure Media Indexer’ın kullanıldığı dizin oluşturma işleri de buna dahildir. Bununla birlikte kodlamadan farklı olarak, dizin oluşturma işleri daha hızlı ayrılmış birimlerde daha hızlı işlenmez.

> [!IMPORTANT]
> Medya işleme konusunu ölçekleme hakkında daha fazla bilgi almak için [genel bakış](media-services-scale-media-processing-overview.md) konusunu gözden geçirin.
> 
> 

## <a name="scale-media-processing"></a>Medya işlemeyi ölçeklendirin
Ayrılmış birim türünü ve ayrılmış birim sayısını değiştirmek için aşağıdakileri yapın:

1. [Azure portalında](https://portal.azure.com/) Azure Media Services hesabınızı seçin.
2. **Ayarlar** penceresinde, **Ortam ayrılmış birimleri**seçin.
   
    Seçili ayrılmış birim türü için ayrılmış birim sayısını değiştirmek için ekranın üst kısmındaki **Ortam Hizmet Birimleri** kaydırıcısını kullanın.
   
    **AYRıLMıŞ Bİrİm TÜRÜNÜ**değiştirmek **için, ayrılmış işleme birimlerinin hızı** çubuğuna tıklayın. Ardından, ihtiyacınız olan fiyatlandırma katmanını seçin: S1, S2 veya S3.
   
3. Yaptığınız değişiklikleri kaydetmek için KAYDET düğmesine basın.
   
    SAVE tuşuna bastığınızda yeni ayrılmış birimler ayrılır.

## <a name="next-steps"></a>Sonraki adımlar
Media Services öğrenme yollarını gözden geçirin.

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Geri bildirimde bulunma
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

