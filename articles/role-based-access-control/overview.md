---
title: Azure kaynakları için rol tabanlı erişim denetimi (RBAC) nedir? | Microsoft Belgeleri
description: Azure kaynakları için rol tabanlı erişim denetimine (RBAC) genel bir bakış alın. Azure kaynaklarına erişimi denetlemek için rol atamaları kullanın.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.assetid: 8f8aadeb-45c9-4d0e-af87-f1f79373e039
ms.service: role-based-access-control
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 03/19/2020
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: 6e3313e3ae201d0b730d8582fed9659d89f0d0c1
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/26/2020
ms.locfileid: "80063045"
---
# <a name="what-is-role-based-access-control-rbac-for-azure-resources"></a>Azure kaynakları için rol tabanlı erişim denetimi (RBAC) nedir?

Bulut kaynakları için erişim yönetimi, bulutu kullanan tüm kuruluşlar için kritik öneme sahip bir işlevdir. Rol tabanlı erişim denetimi (RBAC) Azure kaynaklarına erişebilen kişileri, bu kişilerin bu kaynaklarla yapabileceklerini ve erişebildikleri alanları yönetmenize yardımcı olur.

RBAC, [Azure kaynaklarının](../azure-resource-manager/management/overview.md) ince taneli erişim yönetimini sağlayan Azure Kaynak Yöneticisi'nde oluşturulmuş bir yetkilendirme sistemidir.

## <a name="what-can-i-do-with-rbac"></a>RBAC ile ne yapabilirim?

Aşağıda RBAC ile gerçekleştirebileceğiniz işlemlere örnekler verilmiştir:

- Bir kullanıcıya abonelikteki sanal makineleri yönetme, başka bir kullanıcıya ise sanal ağları yönetme izni verme
- Bir DBA grubuna abonelikteki SQL veritabanlarını yönetme izni verme
- Bir kullanıcının sanal makineler, web siteleri ve alt ağlar gibi bir kaynak grubundaki tüm kaynakları yönetmesine izin verme
- Bir uygulamaya bir kaynak grubundaki tüm kaynaklara erişim izni verme

## <a name="best-practice-for-using-rbac"></a>RBAC kullanımı için en iyi yöntem

RBAC kullanarak ekibiniz içinde görevleri ayırabilir, bu işlere gerek duyan kişilere sadece erişim miktarını verebilirsiniz. Herkese Azure aboneliğiniz veya kaynaklarınızda sınırsız izin vermek yerine belirli eylemlere yalnızca belirli kapsamlarda izin verebilirsiniz.

Erişim denetimi stratejinizi planlarken en iyi yöntem, kullanıcılara yalnızca işlerini yapmak için gerekli olan ayrıcalığı tanımaktır. Aşağıdaki diyagramda RBAC kullanımı için önerilen model gösterilmiştir.

![RBAC ve en düşük öncelik](./media/overview/rbac-least-privilege.png)

## <a name="how-rbac-works"></a>RBAC nasıl çalışır?

RBAC özelliğini kullanarak kaynaklara erişimi denetlemek için rol ataması oluşturmanız gerekir. Bu anlamak için önemli bir kavramdır - bu izinler nasıl uygulanır. Rol ataması üç öğeden oluşur: güvenlik sorumlusu, rol tanımı ve kapsam.

### <a name="security-principal"></a>Güvenlik sorumlusu

*Güvenlik ilkesi,* Azure kaynaklarına erişim isteyen bir kullanıcıyı, grubu, hizmet sorumlusunu veya yönetilen kimliği temsil eden bir nesnedir.

![Rol ataması için güvenlik sorumlusu](./media/overview/rbac-security-principal.png)

- Kullanıcı: Azure Active Directory'de bir profile sahip olan kişidir. Diğer kiracılardaki kullanıcılara da rol atayabilirsiniz. Diğer kuruluşlardaki kullanıcılar hakkında bilgi almak için bkz. [Azure Active Directory B2B](../active-directory/b2b/what-is-b2b.md).
- Grup: Azure Active Directory'de oluşturulan kullanıcı kümesidir. Bir gruba rol atadığınızda ilgili gruptaki tüm kullanıcılar o role sahip olur. 
- Hizmet sorumlusu: Uygulamalar veya hizmetler tarafından belirli Azure kaynaklarına erişmek için kullanılan güvenlik kimliğidir. Bunu bir uygulamanın *kullanıcı kimliği* (kullanıcı adı ve parola veya sertifika) olarak düşünebilirsiniz.
- Yönetilen kimlik - Azure Etkin Dizin'de otomatik olarak Azure tarafından yönetilen bir kimlik. Azure hizmetlerine kimlik bilgilerini yönetmek için bulut uygulamaları geliştirirken genellikle [yönetilen kimlikler](../active-directory/managed-identities-azure-resources/overview.md) kullanırsınız.

### <a name="role-definition"></a>Rol tanımı

*Rol tanımı*, izinlerden oluşan bir koleksiyondur. Genellikle sadece bir *rol*denir. Rol tanımı; okuma, yazma ve silme gibi gerçekleştirilebilecek işlemleri listeler. Roller sahip gibi üst düzey veya sanal makine okuyucusu gibi sınırlı olabilir.

![Rol ataması için rol tanımı](./media/overview/rbac-role-definition.png)

Azure'da kullanabileceğiniz birçok [yerleşik rol](built-in-roles.md) bulunur. Dört temel yerleşik rol aşağıda listelenmiştir. İlk üçü tüm kaynak türleri için geçerlidir.

- [Sahip](built-in-roles.md#owner): Erişimi başkalarına verme hakkı dahil olmak üzere tüm kaynaklara tam erişime sahiptir.
- [Katılımcı](built-in-roles.md#contributor) - Tüm Azure kaynakları oluşturabilir ve yönetebilir, ancak başkalarına erişim izni veremez.
- [Okuyucu](built-in-roles.md#reader): Var olan Azure kaynaklarını görüntüleyebilirsiniz.
- [Kullanıcı Erişimi Yöneticisi](built-in-roles.md#user-access-administrator): Azure kaynaklarına kullanıcı erişimini yönetmenizi sağlar.

Yerleşik rollerin diğerleri belirli Azure kaynakları için yönetim özellikleri sunar. Örneğin [Sanal Makine Katılımcısı](built-in-roles.md#virtual-machine-contributor) rolü, kullanıcının sanal makine oluşturmasını ve yönetmesini sağlar. Yerleşik roller kuruluşunuzun özel gereksinimlerini karşılamazsa, Azure kaynakları için kendi [özel rollerinizi](custom-roles.md)oluşturabilirsiniz.

Azure, bir nesne içindeki verilere erişim izni vermenizi sağlayan veri işlemlerine sahiptir. Örneğin kullanıcının bir depolama hesabında verileri okuma erişimi varsa bu kullanıcı ilgili depolama hesabındaki blobları veya iletileri okuyabilir. Daha fazla bilgi için [bkz.](role-definitions.md)

### <a name="scope"></a>Kapsam

*Kapsam,* erişimin uygulandığı kaynak kümesidir. Bir rol atadığınızda kapsam tanımlayarak izin verilen eylemleri sınırlandırabilirsiniz. Bu özellik bir kullanıcıyı yalnızca bir kaynak grubu için [Web Sitesi Katılımcısı](built-in-roles.md#website-contributor) yapmak istediğiniz durumlarda kullanışlıdır.

Azure'da [yönetim grubu](../governance/management-groups/overview.md), abonelik, kaynak grubu veya kaynak olmak üzere birden fazla seviyede kapsam belirtebilirsiniz. Kapsamlar üst-alt öğe ilişkisiyle yapılandırılmıştır.

![Rol ataması kapsamı](./media/overview/rbac-scope.png)

Üst kapsamda verdiğiniz erişim izinleri alt kapsamlar tarafından devralınır. Örnek:

- [Sahip](built-in-roles.md#owner) rolünü yönetim grubu kapsamında bir kullanıcıya atarsanız bu kullanıcı yönetim grubundaki tüm aboneliklerde bulunan nesneleri yönetebilir.
- [Okuyucu](built-in-roles.md#reader) rolünü bir gruba abonelik kapsamında atadığınızda grubun üyeleri aboneliğin içindeki tüm kaynak gruplarını ve kaynakları görüntüleyebilir.
- [Katkıda bulunan](built-in-roles.md#contributor) rolünü bir uygulamaya kaynak grubu kapsamında atadığınızda ilgili grup bu kaynak grubundaki her türlü kaynağı yönetebilir ancak abonelikteki diğer kaynak gruplarını yönetemez.

### <a name="role-assignments"></a>Rol atamaları

*Rol ataması,* erişim sağlamak amacıyla belirli bir kapsamda bir kullanıcıya, gruba, hizmet ilkesine veya yönetilen kimliğe rol tanımı ekleme işlemidir. Erişim, rol ataması oluşturularak sağlanır ve rol ataması kaldırıldığında iptal edilir.

Aşağıdaki diyagramda rol ataması örneği gösterilmektedir. Bu örnekte Marketing grubuna pharma-sales kaynak grubu için [Katkıda bulunan](built-in-roles.md#contributor) rolü atanmıştır. Bu da Marketing grubundaki kullanıcıların pharma-sales kaynak grubunda tüm Azure kaynaklarını oluşturma veya yönetme işlemlerini gerçekleştirebileceği anlamına gelir. Başka bir rol atamasına sahip olmayan Marketing kullanıcılarının pharma-sales kaynak grubu dışındaki kaynaklara erişimi yoktur.

![Erişim denetimi için rol ataması](./media/overview/rbac-overview.png)

Rol atamalarını oluşturmak için Azure portal, Azure CLI, Azure PowerShell, Azure SDK'ları veya REST API'lerini kullanabilirsiniz. Her abonelikte en fazla **2000** rol ataması ve her yönetim grubunda **500** rol ataması yapabilirsiniz. Rol ataması oluşturmak ve kaldırmak için `Microsoft.Authorization/roleAssignments/*` iznine sahip olmanız gerekir. Bu izin, [Sahip](built-in-roles.md#owner) veya [Kullanıcı Erişimi Yöneticisi](built-in-roles.md#user-access-administrator) rolleriyle verilir.

## <a name="multiple-role-assignments"></a>Birden çok rol atamaları

Birden fazla çakışan rol atamanız varsa ne olur? RBAC bir katkı modelidir, bu nedenle etkili izinleriniz rol ödevlerinizin toplamıdır. Bir kullanıcıya abonelik kapsamında Katılımcı rolü ve kaynak grubundaki Okuyucu rolünün verildiği aşağıdaki örneği göz önünde bulundurun. Katılımcı izinlerinin ve Reader izinlerinin toplamı, kaynak grubunun Katılımcı rolüdür. Bu nedenle, bu durumda, Reader rol atamahiçbir etkisi yoktur.

![Birden çok rol atamaları](./media/overview/rbac-multiple-roles.png)

## <a name="deny-assignments"></a>Reddetme atamaları

Daha önce, RBAC reddetme seçeneği olmayan yalnızca izin verme modeliydi ama şimdi RBAC sınırlı bir yoldan reddetme atamalarını da destekliyor. Bir rol atamasına benzer şekilde, *reddet ataması,* erişimi reddetmek amacıyla belirli bir kapsamda ki kullanıcıya, gruba, hizmet ilkesine veya yönetilen kimliğe bir dizi reddetme eylemi bağlar. Rol *ataması, izin verilen*bir eylem kümesini tanımlarken, reddet atamasına izin *verilmeyen*bir eylem kümesi tanımlanır. Başka bir deyişle, reddetme atamaları kullanıcıların belirtilen eylemleri gerçekleştirmesini (rol ataması izin vermiş olsa bile) engeller. Reddetme atamaları rol atamalarından daha önceliklidir. Daha fazla bilgi için [bkz.](deny-assignments.md)

## <a name="how-rbac-determines-if-a-user-has-access-to-a-resource"></a>RBAC kullanıcının bir kaynağa erişimi olduğunu nasıl saptar?

Aşağıdakiler, RBAC'nin yönetim düzleminde bir kaynağa erişiminizin olup olmadığını saptamak için kullandığı üst düzey adımlardır. Bunları anlamak, bir erişim sorununu gidermeye çalışırken işinize yarayabilir.

1. Kullanıcı (veya hizmet sorumlusu) Azure Resource Manager için bir belirteç alır.

    Belirteç kullanıcının grup üyeliğini (geçişli grup üyelikleri de dahil) içerir.

1. Kullanıcı, belirteci de ekleyerek Azure Resource Manager'a bir REST API çağrısı yapar.

1. Azure Resource Manager, üzerinde eylem gerçekleştirilen kaynak için geçerli olan tüm rol atamalarını ve reddetme atamalarını alır.

1. Azure Resource Manager, rol atamalarını bu kullanıcıya veya onun grubuna uygulananlarla daraltır ve kullanıcının bu kaynak için hangi role sahip olduğunu saptar.

1. Azure Resource Manager, API çağrısındaki eylemin bu kaynak için kullanıcının sahip olduğu rollere eklenip eklenmediğini saptar.

1. Kullanıcının istenen kapsamda ki eylemde bir rolü yoksa, erişim izni verilmez. Aksi takdirde, Azure Resource Manager bir reddetme atamasının geçerli olup olmadığını denetler.

1. Reddetme ataması geçerliyse, erişim engellenir. Aksi takdirde erişim izni verilir.

## <a name="license-requirements"></a>Lisans gereksinimleri

[!INCLUDE [Azure AD free license](../../includes/active-directory-free-license.md)]

## <a name="next-steps"></a>Sonraki adımlar

- [Hızlı başlatma: Azure portalını kullanarak bir kullanıcının Azure kaynaklarına sahip olduğu erişimi görüntüleyin](check-access.md)
- [RBAC ve Azure portalını kullanarak Azure kaynaklarına erişimi yönetme](role-assignments-portal.md)
- [Azure'daki farklı rolleri anlama](rbac-and-directory-admin-roles.md)
- [Kurumsal Bulut Benimsemesi: Azure'da kaynak erişimi yönetimi](/azure/cloud-adoption-framework/govern/resource-consistency/resource-access-management)
