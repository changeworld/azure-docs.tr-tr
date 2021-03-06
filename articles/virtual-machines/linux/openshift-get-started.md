---
title: Azure'da OpenShift'e genel bakış
description: Azure'da OpenShift'e genel bakış.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: haroldwongms
manager: mdotson
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 05/7/2019
ms.author: haroldw
ms.openlocfilehash: 021ebe010a27fa155de861121e1972466c800f4a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74035419"
---
# <a name="openshift-in-azure"></a>Azure'da OpenShift

OpenShift, Docker ve Kubernetes'i işletmeye getiren açık ve genişletilebilir bir konteyner uygulama platformudur.  

OpenShift konteyner orkestrasyon ve yönetimi için Kubernetes içerir. Aşağıdakileri etkinleştiren geliştirici merkezli ve operasyon merkezli araçlar ekler:

- Hızlı uygulama geliştirme.
- Kolay dağıtım ve ölçekleme.
- Ekipler ve uygulamalar için uzun süreli yaşam döngüsü bakımı.

OpenShift'in birden çok sürümü vardır.  Bu sürümlerden yalnızca ikisi bugün müşterilerin Azure'da dağıtılaması için kullanılabilir: OpenShift Kapsayıcı Platformu ve OKD (eski adıyla OpenShift Origin).

## <a name="azure-red-hat-openshift"></a>Azure Red Hat OpenShift

Microsoft Azure Red Hat OpenShift, Azure'da çalışan OpenShift'in tam olarak yönetilen bir teklifidir. Bu hizmet, Microsoft ve Red Hat tarafından ortaklaşa yönetilir ve desteklenir. Daha fazla bilgi için [Azure Red Hat OpenShift Service](https://docs.microsoft.com/azure/openshift/) belgelerine bakın.

## <a name="openshift-container-platform"></a>OpenShift Kapsayıcı Platformu

Konteyner Platformu, Red Hat tarafından desteklenen, kurumsal kullanıma hazır bir [ticari sürümdür.](https://www.openshift.com) Bu sürümle, müşteriler OpenShift Konteyner Platformu için gerekli yetkileri satın alır ve tüm altyapının kurulumu ve yönetiminden sorumludur.

Müşteriler platformun tamamını "sahip" olduklarından, platformu şirket içi veri merkezlerine veya genel bir buluta (Azure gibi) yükleyebilirler.

## <a name="okd"></a>OKD

OKD, OpenShift'in topluluk tarafından desteklenen [açık kaynaklı](https://www.okd.io/) bir yukarı akım projesidir. OKD CentOS veya Red Hat Enterprise Linux (RHEL) yüklenebilir.

## <a name="next-steps"></a>Sonraki adımlar

- [Azure'da OpenShift için ortak ön koşulları yapılandırma](./openshift-container-platform-3x-prerequisites.md)
- [Azure'da OpenShift Kapsayıcı Platformu'na dağıtma](./openshift-container-platform-3x.md)
- [OpenShift Konteyner Platformu Kendi Kendini Yöneten Pazar Teklifi dağıtın](./openshift-container-platform-3x-marketplace-self-managed.md)
- [Azure Yığını'nda OpenShift'i dağıtma](./openshift-azure-stack.md)
- [Dağıtım sonrası görevler](./openshift-container-platform-3x-post-deployment.md)
- [OpenShift dağıtımını giderme](./openshift-container-platform-3x-troubleshooting.md)
