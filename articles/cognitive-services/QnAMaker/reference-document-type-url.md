---
title: İthalat için desteklenen URL türleri - QnA Maker
description: Url türlerinin QnA çiftlerini almak ve oluşturmak için nasıl kullanıldığını anlayın.
ms.topic: reference
ms.date: 01/02/2020
ms.openlocfilehash: c6c6c7ba19fe7be54733f45997134fddb56a6ba0
ms.sourcegitcommit: 6397c1774a1358c79138976071989287f4a81a83
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/07/2020
ms.locfileid: "80804325"
---
# <a name="urls-supported-for-importing-documents"></a>Belge alma için desteklenen URL'ler

Url türlerinin QnA çiftlerini almak ve oluşturmak için nasıl kullanıldığını anlayın.

## <a name="faq-urls"></a>SSS URL'leri

QnA Maker, SSS web sayfalarını 3 farklı şekilde destekleyebilir:

* Düz SSS sayfaları
* Bağlantıları olan SSS sayfaları
* Konular Ana Sayfası olan SSS sayfaları

### <a name="plain-faq-pages"></a>Düz SSS sayfaları

Bu, yanıtların soruları hemen aynı sayfadan takip ettiği en yaygın SSS sayfası türüdür.

Aşağıda düz bir SSS sayfası örneği verilmiştir:

![Bilgi tabanı için düz SSS sayfası örneği](./media/qnamaker-concepts-datasources/plain-faq.png)


### <a name="faq-pages-with-links"></a>Bağlantıları olan SSS sayfaları

Bu tür SSS sayfasında, sorular bir araya toplanır ve aynı sayfanın farklı bölümlerinde veya farklı sayfalarda bulunan yanıtlara bağlanır.

Aşağıda, aynı sayfada bulunan bölümlerdeki bağlantıların yer aldığı bir SSS sayfası örneği verilmiştir:

 ![Bilgi tabanı için Bölüm Bağlantısı SSS sayfası örneği](./media/qnamaker-concepts-datasources/sectionlink-faq.png)


### <a name="parent-topics-page-links-to-child-answers-pages"></a>Alt Konular sayfa larını alt yanıt sayfalarına bağlar

Bu sSS türünde, her konunun farklı bir sayfadaki karşılık gelen soru ve yanıt kümesiyle bağlantılı olduğu bir Konular sayfası vardır. QnA Maker, ilgili soruları & yanıtları ayıklamak için tüm bağlantılı sayfaları taramak için türünür.

Aşağıda, farklı sayfalardaki SSS bölümlerine bağlantılar içeren konular sayfasının bir örneği verilmiştir.

 ![Bilgi tabanı için derin bağlantı SSS sayfa örneği](./media/qnamaker-concepts-datasources/topics-faq.png)

## <a name="support-urls"></a>Destek URL'leri

QnA Maker, belirli bir görevin nasıl gerçekleştirileceğini, belirli bir sorunun nasıl tanılanacağını ve çözüleceğini ve belirli bir işlem için en iyi uygulamaların neler olduğunu açıklayan web makaleleri gibi yarı yapılandırılmış destek web sayfalarını işleyebilir. Çıkarma, hiyerarşik başlıklara sahip açık bir yapıya sahip içerik üzerinde en iyi şekilde çalışır.

> [!NOTE]
> Destek makaleleri için çıkarma yeni bir özelliktir ve erken aşamalarındadır. Bu iyi yapılandırılmış basit sayfalar için en iyi çalışır ve karmaşık üstbilgi / altbilgi içermez.

![QnA Maker, hiyerarşik başlıklarla açık bir yapının sunulduğu yarı yapılandırılmış web sayfalarından çıkarmayı destekler](./media/qnamaker-concepts-datasources/support-web-pages-with-heirarchical-structure.png)