---
title: Azure NetApp Dosyaları için kaydolun | Microsoft Dokümanlar
description: Azure NetApp Dosyalarını kullanmak için nasıl kaydolunur açıklanır.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/06/2019
ms.author: b-juche
ms.openlocfilehash: 6f5d84dea2e835fd12a062b628181354295ed9f6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79274066"
---
# <a name="register-for-azure-netapp-files"></a>Azure NetApp Files için kaydolma

> [!IMPORTANT] 
> Azure NetApp Files kaynak sağlayıcısını kaydetmeden önce, Azure NetApp Files ekibinden hizmete erişim izni verildiğini onaylayan bir e-posta almış olmanız gerekir. 

Bu makalede, hizmeti kullanmaya başlayabilmek için Azure NetApp Dosyalarına nasıl kaydolabileceğinizi öğrenin.

## <a name="submit-a-waitlist-request-for-accessing-the-service"></a><a name="waitlist"></a>Hizmete erişmek için bekleme listesi isteği gönderme

1. Azure NetApp Files hizmetine Erişmek için bekleme listesi isteğini [Azure NetApp Dosyaları bekleme listesi gönderme sayfası](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR8cq17Xv9yVBtRCSlcD_gdVUNUpUWEpLNERIM1NOVzA5MzczQ0dQR1ZTSS4u)üzerinden gönderin. 

    Bekleme listesi kaydolma, anında hizmet erişimini garanti etmez. 

2. Diğer görevlere devam etmeden önce Azure NetApp Files ekibinden resmi onay e-postası bekleyin. 

## <a name="register-the-netapp-resource-provider"></a><a name="resource-provider"></a>NetApp Kaynak Sağlayıcısı'nı Kaydedin

Hizmeti kullanmak için Azure NetApp Dosyaları için Azure Kaynak Sağlayıcısı'nı kaydetmeniz gerekir.

> [!NOTE] 
> NetApp Kaynak Sağlayıcısı'nı hizmet erişimi ne olursa olsun başarılı bir şekilde kaydedebilirsiniz. Ancak, erişim izni olmadan, herhangi bir Azure portalı veya API'nin NetApp hesabı veya başka bir Azure NetApp Files kaynağı oluşturma isteği aşağıdaki hatayla reddedilecektir:  
>
> `{"code":"DeploymentFailed","message":"At least one resource deployment operation failed. Please list deployment operations for details. Please see https://aka.ms/arm-debug for usage details.","details":[{"code":"NotFound","message":"{\r\n \"error\": {\r\n \"code\": \"InvalidResourceType\",\r\n \"message\": \"The resource type could not be found in the namespace 'Microsoft.NetApp' for api version '2017-08-15'.\"\r\n }\r\n}"}]}`


1. Azure portalından sağ üst köşedeki Azure Bulut BulutU simgesini tıklatın:

      ![Azure Bulut Kabuğu simgesi](../media/azure-netapp-files/azure-netapp-files-azure-cloud-shell.png)

2. Azure hesabınızda birden çok aboneliğiniz varsa, Azure NetApp Dosyaları için beyaz listeye alınmış olan aboneliği seçin:
    
        az account set --subscription <subscriptionId>

3. Azure Bulut BulutU konsolunda, aboneliğinizin beyaz listeye alındığını doğrulamak için aşağıdaki komutu girin:
    
        az feature list | grep NetApp

   Komut çıktısı aşağıdaki gibi görünür:
   
       "id": "/subscriptions/<SubID>/providers/Microsoft.Features/providers/Microsoft.NetApp/features/ANFGA",  
       "name": "Microsoft.NetApp/ANFGA" 
       
   `<SubID>`abonelik kimliğinizdir.

    Özellik adını `Microsoft.NetApp/ANFGA`görmüyorsanız, hizmete erişiminiz yoktur. Bu adımda dur. Devam etmeden önce hizmete erişim istemek [için hizmete erişmek için bekleme listesi isteği gönder'deki](#waitlist) yönergeleri izleyin. 

4. Azure Bulut BulutU konsolunda, Azure Kaynak Sağlayıcısı'nı kaydetmek için aşağıdaki komutu girin: 
    
        az provider register --namespace Microsoft.NetApp --wait

   `--wait` Parametre, konsola kaydın tamamlanmasını beklemesini bildirir. Kayıt işleminin tamamlanması biraz zaman alabilir.

5. Azure Bulut BulutU konsolunda, Azure Kaynak Sağlayıcısı'nın kayıtlı olduğunu doğrulamak için aşağıdaki komutu girin: 
    
        az provider show --namespace Microsoft.NetApp

   Komut çıktısı aşağıdaki gibi görünür:
   
        {
        "id": "/subscriptions/<SubID>/providers/Microsoft.NetApp",
        "namespace": "Microsoft.NetApp", 
        "registrationState": "Registered", 
        "resourceTypes": […. 

   `<SubID>`abonelik kimliğinizdir.  `state` Parametre değeri `Registered`gösterir.

6. Azure portalından **Abonelikler** bıçağını tıklatın.
7. Abonelikler bıçağında abonelik kimliğinizi tıklatın. 
8. Abonelik ayarlarında, Microsoft.NetApp Sağlayıcısı'nın Kayıtlı durumu belirttiğini doğrulamak için **Kaynak sağlayıcılarını** tıklatın: 

      ![Kayıtlı Microsoft.NetApp](../media/azure-netapp-files/azure-netapp-files-registered-resource-providers.png)


## <a name="next-steps"></a>Sonraki adımlar

[NetApp hesabı oluşturma](azure-netapp-files-create-netapp-account.md)