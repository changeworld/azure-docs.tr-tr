---
title: Azure IOT OPC UA sertifika yönetimi nedir | Microsoft Docs
description: OPC kasası genel bakış
author: dominicbetts
ms.author: dobett
ms.date: 11/26/2018
ms.topic: overview
ms.service: iot-industrialiot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: ba3313d927ec4317d6c051f6058d75a415b92288
ms.sourcegitcommit: 563f8240f045620b13f9a9a3ebfe0ff10d6787a2
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/01/2019
ms.locfileid: "58759318"
---
# <a name="what-is-azure-iot-open-platform-communications-opc-ua-certificate-management"></a>Azure IOT açık Platform iletişim (OPC) UA sertifika yönetimi nedir?

Azure IOT OPC UA sertifika yönetimi, ayrıca OPC kasası olarak yapılandırabilirsiniz, bir mikro hizmet kaydı, bildiğiniz ve sertifika yaşam döngüsü için OPC UA sunucu ve istemci uygulamalarını bulutta yönetin. Bu makalede, OPC kasanın basit kullanım durumları açıklanmaktadır.

## <a name="certificate-management"></a>Sertifika yönetimi

Örneğin, kendi OPC UA sunucunuz, yeni oluşturulan istemci uygulamaya bağlanmak bir üretim şirketi gerekir. Üretici ilk erişim sunucu makinesinin yaptığında, bir hata iletisi hemen istemci uygulamaya güvenli olmadığını belirtmek için OPC UA sunucu uygulaması gösterilmektedir. Bu mekanizma vicious atölyede deşifre etme engelleyen herhangi bir uygulamada yetkisiz erişimi önlemek için OPC UA server makinesi içinde yerleşik olarak bulunur.

## <a name="application-security-management"></a>Uygulama güvenlik yönetimi
Profesyonel bir güvenlik OPC kasa mikro hizmet OPC kasa sertifika kayıt defteri, depolama ve yaşam döngüsü yönetimi için tüm işlevlere sahip olduğu herhangi bir istemci uygulama ile iletişim kurmak OPC UA sunucusu kolayca etkinleştirmek için kullanır. Artık bir OPC UA sunucusuna güvenli bir şekilde bağlıdır, bu yeni oluşturulan istemci uygulamasına iletişim kurabilir

## <a name="the-complete-opc-vault-architecture"></a>Tüm OPC kasa mimarisi
Aşağıdaki diyagramda, tüm OPC kasa mimarisi gösterilmektedir.

![OPC kasa mimarisi](media/overview-opc-vault-architecture/opc-vault.png)