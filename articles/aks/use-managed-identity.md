---
title: Azure Kubernetes Hizmetinde yönetilen kimlikleri kullanma
description: Azure Kubernetes Hizmeti'nde (AKS) yönetilen kimlikleri nasıl kullanacağınızı öğrenin
services: container-service
author: saudas
manager: saudas
ms.topic: article
ms.date: 03/10/2019
ms.author: saudas
ms.openlocfilehash: 85efc6d9d203ca06c5f7566376993b4c13950788
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/27/2020
ms.locfileid: "80369961"
---
# <a name="use-managed-identities-in-azure-kubernetes-service"></a>Azure Kubernetes Hizmetinde yönetilen kimlikleri kullanma

Şu anda, bir Azure Kubernetes Hizmeti (AKS) kümesi (özellikle Kubernetes bulut sağlayıcısı) Azure'da yük dengeleyicileri ve yönetilen diskler gibi ek kaynaklar oluşturmak için bir *hizmet ilkesi* gerektirir. Ya bir hizmet müdürü sağlamanız gerekir ya da AKS sizin adınıza bir tane oluşturur. Hizmet ilkelerinin genellikle bir son kullanma tarihi vardır. Kümeler sonunda kümenin çalışmasını sağlamak için hizmet sorumlusunun yenilenmesi gereken bir duruma ulaşır. Hizmet ilkelerini yönetmek karmaşıklık ekler.

*Yönetilen kimlikler* aslında hizmet ilkeleri etrafında bir sarmalayıcı ve yönetim kolaylaştırır. Daha fazla bilgi edinmek [için Azure kaynakları için yönetilen kimlikler](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview)hakkında bilgi edinin.

AKS yönetilen iki kimlik oluşturur:

- **Sisteme atanan yönetilen kimlik**: Kubernetes bulut sağlayıcısının kullanıcı adına Azure kaynakları oluşturmak için kullandığı kimlik. Sistem tarafından atanan kimliğin yaşam döngüsü kümeninkine bağlıdır. Küme silindiğinde kimlik silinir.
- **Kullanıcı tarafından atanan yönetilen kimlik**: Kümede yetkilendirme için kullanılan kimlik. Örneğin, kullanıcı tarafından atanan kimlik, AKS'ye Azure Kapsayıcı Kayıt Şirketlerini (ACRs) kullanmasına veya Azure'dan meta veri almak için kubelet'e yetki vermek için kullanılır.

Eklentiler de yönetilen bir kimlik kullanarak kimlik doğrulaması. Her eklenti için, yönetilen bir kimlik AKS tarafından oluşturulur ve eklentinin ömrü boyunca sürer. Kaynakların MC_* kaynak grubunun dışında olduğu kendi VNet, statik IP adresi veya ekli Azure diskinizi oluşturmak ve kullanmak için bir rol ataması gerçekleştirmek için kümenin PrincipalID'ini kullanın. Rol ataması hakkında daha fazla bilgi için diğer [Azure kaynaklarına Temsilci erişimi](kubernetes-service-principal.md#delegate-access-to-other-azure-resources)ne bakın.

## <a name="before-you-begin"></a>Başlamadan önce

Aşağıdaki kaynağın yüklü olması gerekir:

- Azure CLI, sürüm 2.2.0 veya sonrası

## <a name="create-an-aks-cluster-with-managed-identities"></a>Yönetilen kimliklere sahip bir AKS kümesi oluşturma

Artık aşağıdaki CLI komutlarını kullanarak yönetilen kimliklere sahip bir AKS kümesi oluşturabilirsiniz.

İlk olarak, bir Azure kaynak grubu oluşturun:

```azurecli-interactive
# Create an Azure resource group
az group create --name myResourceGroup --location westus2
```

Ardından, bir AKS kümesi oluşturun:

```azurecli-interactive
az aks create -g MyResourceGroup -n MyManagedCluster --enable-managed-identity
```

Yönetilen kimlikleri kullanarak başarılı bir küme oluşturma bu hizmet temel profil bilgilerini içerir:

```json
"servicePrincipalProfile": {
    "clientId": "msi",
    "secret": null
  }
```

Son olarak, kümeye erişmek için kimlik bilgilerini alın:

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name MyManagedCluster
```

Küme birkaç dakika içinde oluşturulacak. Daha sonra uygulama iş yüklerinizi yeni kümeye dağıtabilir ve hizmet temel tabanlı AKS kümelerinde yaptığınız gibi onunla etkileşimkurabilirsiniz.

> [!IMPORTANT]
>
> - Yönetilen kimliklere sahip AKS kümeleri yalnızca kümenin oluşturulması sırasında etkinleştirilebilir.
> - Varolan AKS kümeleri, yönetilen kimlikleri etkinleştirmek için güncelleştirilemez veya yükseltilemez.
