---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 04/03/2020
ms.author: dacoulte
ms.openlocfilehash: 28a3618941c9ee36798dec9e37e50e245ed96669
ms.sourcegitcommit: b129186667a696134d3b93363f8f92d175d51475
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2020
ms.locfileid: "80758499"
---
|Adı |Açıklama |Etki(ler) |Sürüm |GitHub |
|---|---|---|---|---|
|[RootManageSharedAccessKey dışındaki tüm yetkilendirme kuralları Hizmet Veri Günü ad alanından kaldırılmalıdır](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa1817ec0-a368-432a-8057-8371e17ac6ee) |Hizmet Veri Mes istemcileri, ad alanındaki tüm kuyruklara ve konulara erişim sağlayan bir ad alanı düzeyi erişim ilkesi kullanmamalıdır. En az ayrıcalık güvenlik modeliyle hizalamak için, yalnızca belirli bir varlığa erişim sağlamak için kuyruklar ve konular için varlık düzeyinde erişim ilkeleri oluşturmanız gerekir |Denetim, Reddet, Devre Dışı Bırakma |1.0.1 |[Bağlantı](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Service%20Bus/ServiceBus_AuditNamespaceAccessRules_Audit.json)
|[Hizmet Veri Servisi'ndeki tanılama günlükleri etkinleştirilmeli](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff8d36e2f-389b-4ee4-898d-21aeb69a0f45) |Tanılama günlüklerinin denetim için. Bu, araştırma amacıyla kullanmak üzere etkinlik izlerini yeniden oluşturmanıza olanak tanır; bir güvenlik olayı meydana geldiğinde veya ağınızın gizliliği ihlal edildiğinde |Auditifnotexists, Devre Dışı |2.0.0 |[Bağlantı](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Service%20Bus/ServiceBus_AuditDiagnosticLog_Audit.json)
