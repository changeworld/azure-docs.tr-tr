---
title: 'Sorun giderme: İndirilen etkinlik günlüklerinde eksik veri | Microsoft Dokümanlar'
description: İndirilen Azure Active Directory etkinlik günlüklerindeki eksik verilere yönelik bir çözüm sağlar.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: ffce7eb1-99da-4ea7-9c4d-2322b755c8ce
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 11/13/2018
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: ee07fdb6f8a4e69600297bdb16b6ad74793f10c9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74007716"
---
# <a name="i-cant-find-all-the-data-in-the-azure-active-directory-activity-logs-i-downloaded"></a>İndirdiğim Azure Etkin Dizin etkinlik günlüklerinde tüm verileri bulamıyorum

## <a name="symptoms"></a>Belirtiler

Etkinlik günlüklerini (denetim veya oturum açma) indirdim ve seçtiğim süre için tüm kayıtları göremiyorum. Neden? 

 ![Raporlama](./media/troubleshoot-missing-data-download/01.png)
 
## <a name="cause"></a>Nedeni

Azure portalındaki etkinlik günlüklerini karşıdan yüklediğinizde, ölçeği en son ilk önce sıralanmış 250.000 kayıtla sınırlandırıyoruz. 

## <a name="resolution"></a>Çözüm

Belirli bir noktadaki bir milyon kaydı getirmek için [Azure AD Raporlama API’lerini](concept-reporting-api.md) kullanabilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar

* [Azure Etkin Dizin raporları SSS](reports-faq.md)

