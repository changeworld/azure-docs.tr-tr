---
title: Azure Red Hat OpenShift'te güvenlik bağlamı kısıtlamalarını yönetme | Microsoft Dokümanlar
description: Azure Red Hat OpenShift küme yöneticileri için güvenlik bağlamı kısıtlamaları
services: container-service
author: troy0820
ms.author: b-trconn
ms.service: container-service
ms.topic: article
ms.date: 09/25/2019
ms.openlocfilehash: 24163adcec889e9eedc2362ff1f01f00257a98f3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80063168"
---
# <a name="manage-security-context-constraints-in-azure-red-hat-openshift"></a>Azure Red Hat OpenShift'te güvenlik bağlamı kısıtlamalarını yönetme 

Güvenlik bağlamı kısıtlamaları (SCC'ler), küme yöneticilerinin bölme izinlerini denetlemesine olanak sağlar. Bu API türü hakkında daha fazla bilgi edinmek [için SCC'lerin mimari belgelerine](https://docs.openshift.com/container-platform/3.11/architecture/additional_concepts/authorization.html)bakın. CLI'yi kullanarak scc'leri normal API nesneleri olarak yönetebilirsiniz.

## <a name="list-security-context-constraints"></a>Güvenlik bağlamı kısıtlamalarını listele

Geçerli bir SCC listesini almak için şu komutu kullanın: 

```bash
$ oc get scc

NAME               PRIV      CAPS      SELINUX     RUNASUSER          FSGROUP     SUPGROUP    PRIORITY   READONLYROOTFS   VOLUMES
anyuid             false     []        MustRunAs   RunAsAny           RunAsAny    RunAsAny    10         false            [configMap downwardAPI emptyDir persistentVolumeClaim secret]
hostaccess         false     []        MustRunAs   MustRunAsRange     MustRunAs   RunAsAny    <none>     false            [configMap downwardAPI emptyDir hostPath persistentVolumeClaim secret]
hostmount-anyuid   false     []        MustRunAs   RunAsAny           RunAsAny    RunAsAny    <none>     false            [configMap downwardAPI emptyDir hostPath nfs persistentVolumeClaim secret]
hostnetwork        false     []        MustRunAs   MustRunAsRange     MustRunAs   MustRunAs   <none>     false            [configMap downwardAPI emptyDir persistentVolumeClaim secret]
nonroot            false     []        MustRunAs   MustRunAsNonRoot   RunAsAny    RunAsAny    <none>     false            [configMap downwardAPI emptyDir persistentVolumeClaim secret]
privileged         true      [*]       RunAsAny    RunAsAny           RunAsAny    RunAsAny    <none>     false            [*]
restricted         false     []        MustRunAs   MustRunAsRange     MustRunAs   RunAsAny    <none>     false            [configMap downwardAPI emptyDir persistentVolumeClaim secret]
```

## <a name="examine-an-object-for-security-context-constraints"></a>Güvenlik bağlamı kısıtlamaları için nesneyi inceleme

Belirli bir SCC'yi `oc get` `oc describe`incelemek `oc edit`için,  Örneğin, **kısıtlı** SCC'yi incelemek için şu komutu kullanın:
```bash
$ oc describe scc restricted
Name:                    restricted
Priority:                <none>
Access:
  Users:                <none>
  Groups:                system:authenticated
Settings:
  Allow Privileged:            false
  Default Add Capabilities:        <none>
  Required Drop Capabilities:        KILL,MKNOD,SYS_CHROOT,SETUID,SETGID
  Allowed Capabilities:            <none>
  Allowed Seccomp Profiles:        <none>
  Allowed Volume Types:            configMap,downwardAPI,emptyDir,persistentVolumeClaim,projected,secret
  Allow Host Network:            false
  Allow Host Ports:            false
  Allow Host PID:            false
  Allow Host IPC:            false
  Read Only Root Filesystem:        false
  Run As User Strategy: MustRunAsRange
    UID:                <none>
    UID Range Min:            <none>
    UID Range Max:            <none>
  SELinux Context Strategy: MustRunAs
    User:                <none>
    Role:                <none>
    Type:                <none>
    Level:                <none>
  FSGroup Strategy: MustRunAs
    Ranges:                <none>
  Supplemental Groups Strategy: RunAsAny
    Ranges:                <none>
```
## <a name="next-steps"></a>Sonraki adımlar
> [!div class="nextstepaction"]
> [Azure Red Hat OpenShift kümesi oluşturma](tutorial-create-cluster.md) 
