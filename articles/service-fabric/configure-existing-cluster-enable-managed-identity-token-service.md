---
title: Varolan bir Hizmet Kumaşı kümesinde yönetilen kimlik desteğini yapılandırma
description: 'Varolan bir Azure Hizmet Kumaşı kümesinde yönetilen kimlik desteğini şu şekilde etkinleştirebilirsiniz:'
ms.topic: article
ms.date: 12/09/2019
ms.custom: sfrev
ms.openlocfilehash: 396978546b301884087c4ea51e242258d64a6b0b
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/09/2020
ms.locfileid: "80983815"
---
# <a name="configure-managed-identity-support-in-an-existing-service-fabric-cluster-preview"></a>Yönetilen kimlik desteğini varolan bir Hizmet Kumaşı kümesinde yapılandırma (önizleme)

Hizmet Kumaşı uygulamalarınızda [Azure kaynakları için Yönetilen kimlikleri](../active-directory/managed-identities-azure-resources/overview.md) kullanmak için, önce kümedeki Yönetilen Kimlik *Belirteç Hizmeti'ni* etkinleştirin. Bu hizmet, Yönetilen kimliklerini kullanarak Service Fabric uygulamalarının kimlik doğrulamalarından ve onlar adına erişim belirteçleri almaktan sorumludur. Hizmet etkinleştirildikten sonra, sol bölmedeki **Sistem** bölümünün altında, kumaş adı altında çalışan Service Fabric Explorer'da **görebilirsiniz:/System/ManagedIdentityTokenService**.

> [!NOTE]
> **Yönetilen Kimlik Belirteç Hizmeti**etkinleştirmek için Service Fabric runtime sürüm 6.5.658.9590 veya daha yüksek gereklidir.  
>
> Küme kaynağını açıp **Essentials** bölümünde **Service Fabric sürüm** özelliğini kontrol ederek Bir kümenin Hizmet Kumaşı sürümünü Azure portalından bulabilirsiniz.
>
> Küme **Manuel** yükseltme modundaysa, önce 6.5.658.9590 veya sonrası yükseltmeniz gerekir.

## <a name="enable-managed-identity-token-service-in-an-existing-cluster"></a>Varolan bir kümede *Yönetilen Kimlik Belirteç Hizmetini* etkinleştirme

Yönetilen Kimlik Belirteci Hizmetini varolan bir kümede etkinleştirmek için, iki değişiklik belirten bir küme yükseltmesi başlatmanız gerekir: (1) Yönetilen Kimlik Belirteci Hizmetini etkinleştirme ve (2) her düğümün yeniden başlatılmasını istemek. İlk olarak, kümeAzure Kaynak Yöneticisi şablonunuzu aşağıdaki snippet ekleyin:

```json
"fabricSettings": [
    {
        "name": "ManagedIdentityTokenService",
        "parameters": [
            {
                "name": "IsEnabled",
                "value": "true"
            }
        ]
    }
]
```

Değişikliklerin etkili olması için, yükseltme küme de ilerledikçe her düğümde Hizmet Kumaşı çalışma zamanının güçlü bir şekilde yeniden başlatılmasını belirtmek için yükseltme ilkesini değiştirmeniz gerekir. Bu yeniden başlatma, yeni etkinleştirilen sistem hizmetinin başlatılmasını ve her düğümüzerinde çalışmasını sağlar. Aşağıdaki snippet' `forceRestart` te yeniden başlatmayı etkinleştirmek için gerekli ayar dır. Kalan parametreler için aşağıda açıklanan değerleri kullanın veya küme kaynağı için önceden belirtilen varolan özel değerleri kullanın. Hizmet Kumaşı kaynağında 'Kumaş Yükseltmeleri' seçeneğini seçerek Azure Portalı'ndan Kumaş Yükseltme İlkesi ('yükseltme Açıklaması') için özel ayarlar görüntülenebilir veya resources.azure.com. Yükseltme ilkesi ('upgradeDescription') için varsayılan seçenekler powershell veya resources.azure.com görüntülenemez. Ek bilgi için [ClusterUpgradePolicy'ye](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.servicefabric.models.clusterupgradepolicy?view=azure-dotnet) bakın.  

```json
"upgradeDescription": {
    "forceRestart": true,
    "healthCheckRetryTimeout": "00:45:00",
    "healthCheckStableDuration": "00:05:00",
    "healthCheckWaitDuration": "00:05:00",
    "upgradeDomainTimeout": "02:00:00",
    "upgradeReplicaSetCheckTimeout": "1.00:00:00",
    "upgradeTimeout": "12:00:00"
}
```

> [!NOTE]
> Yükseltmenin başarıyla tamamlanmasından sonra, sonraki yükseltmelerin etkisini en aza indirmek için `forceRestart` ayarı geri döndürmeyi unutmayın. 

## <a name="errors-and-troubleshooting"></a>Hatalar ve sorun giderme

Dağıtım aşağıdaki iletiyle başarısız olursa, kümenin yeterince yüksek bir Hizmet Kumaşı sürümünde çalışmadığı anlamına gelir:

```json
{
    "code": "ParameterNotAllowed",
    "message": "Section 'ManagedIdentityTokenService' and Parameter 'IsEnabled' is not allowed."
}
```

## <a name="next-steps"></a>Sonraki adımlar
* [Sistem tarafından atanmış yönetilen bir kimliğe sahip bir Azure Hizmet Kumaşı uygulamasını dağıtma](./how-to-deploy-service-fabric-application-system-assigned-managed-identity.md)
* [Kullanıcı tarafından atanan yönetilen bir kimliğe sahip bir Azure Hizmet Kumaşı uygulaması dağıtma](./how-to-deploy-service-fabric-application-user-assigned-managed-identity.md)
* [Hizmet kodundan Hizmet Kumaşı uygulamasının yönetilen kimliğinden yararlanma](./how-to-managed-identity-service-fabric-app-code.md)
* [Azure Hizmet Kumaşı uygulamasına diğer Azure kaynaklarına erişim hakkı verme](./how-to-grant-access-other-resources.md)
