---
title: Öğretici`:` Azure Kaynak Yöneticisi'ne erişmek için yönetilen bir kimliği kullanma - Windows - Azure AD
description: Windows VM üzerinde bir kullanıcı tarafından atanan yönetilen kimliği kullanarak Azure Resource Manager'a erişme işleminde size yol gösteren bir öğretici.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 01/14/2020
ms.author: markvi
ms.collection: M365-identity-device-management
ms.openlocfilehash: ec9956f0c5d834633646938da19f03e5467a9f6d
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/24/2020
ms.locfileid: "75977848"
---
# <a name="tutorial-use-a-user-assigned-managed-identity-on-a-windows-vm-to-access-azure-resource-manager"></a>Öğretici: Azure Kaynak Yöneticisi'ne erişmek için Windows VM'de kullanıcı tarafından atanmış yönetilen bir kimlik kullanma

[!INCLUDE [preview-notice](~/includes/active-directory-msi-preview-notice-ua.md)]

Bu öğreticide, kullanıcı tarafından atanan kimliği oluşturma, bunu Windows Sanal Makinesine (VM) atama ve bu kimliği Azure Resource Manager API’sine erişmek için kullanma işlemleri açıklanır. Yönetilen Hizmet Kimlikleri Azure tarafından otomatik olarak yönetilir. Bunlar, kodunuza kimlik bilgileri girmenize gerek kalmadan Azure AD kimlik doğrulamasını destekleyen hizmetlerde kimlik doğrulaması yapmaya olanak tanır. 

Aşağıdakileri nasıl yapacağınızı öğrenirsiniz:

> [!div class="checklist"]
> * Kullanıcı tarafından atanan yönetilen kimlik oluşturma
> * Kullanıcı tarafından atanan kimliğinizi Windows VM’nize atama
> * Azure Resource Manager’da Kaynak Grubuna kullanıcı tarafından atanan kimlik için erişim verme 
> * Kullanıcı tarafından atanan kimliği kullanarak erişim belirteci alma ve Azure Resource Manager çağrısı yapmak için bunu kullanma 
> * Kaynak Grubunun özelliklerini okuma

[!INCLUDE [az-powershell-update](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Ön koşullar

[!INCLUDE [msi-qs-configure-prereqs](../../../includes/active-directory-msi-qs-configure-prereqs.md)]

- [Azure portal'da oturum açma](https://portal.azure.com)

- [Windows sanal makinesi oluşturma](/azure/virtual-machines/windows/quick-create-portal)

- Bu öğreticideki gerekli kaynak oluşturma ve rol yönetimini adımlarını gerçekleştirmek için hesabınız uygun kapsamda (aboneliğiniz veya kaynak grubunuz) "Sahip" izinlerini gerektiriyor. Rol atamayla ilgili yardıma ihtiyacınız varsa bkz. [Azure abonelik kaynaklarınıza erişimi yönetmek için Rol Tabanlı Erişim Denetimi kullanma](/azure/role-based-access-control/role-assignments-portal).
- [Azure PowerShell modülünün en son sürümünü yükleyin.](/powershell/azure/install-az-ps) 
- Azure ile bağlantı oluşturmak için `Connect-AzAccount` komutunu çalıştırın.
- [PowerShellGet'in en son sürümünü](/powershell/scripting/gallery/installing-psget#for-systems-with-powershell-50-or-newer-you-can-install-the-latest-powershellget) yükleyin.
- `Install-Module -Name PowerShellGet -AllowPrerelease` komutunu çalıştırarak `PowerShellGet` modülünün yayın öncesi sürümünü alın (`Az.ManagedServiceIdentity` modülünü yüklemek için bu komutu çalıştırdıktan sonra geçerli PowerShell oturumundan `Exit` ile çıkmanız gerekebilir).
- Bu makaledeki kullanıcı tarafından atanan kimlik işlemlerini gerçekleştirmek için `Install-Module -Name Az.ManagedServiceIdentity -AllowPrerelease` komutunu çalıştırarak `Az.ManagedServiceIdentity` modülünün yayın öncesi sürümünü yükleyin.


## <a name="enable"></a>Etkinleştirme

Kullanıcı tarafından atanan bir kimliğe dayanan bir senaryo için aşağıdaki adımları gerçekleştirmeniz gerekir:

- Kimlik oluşturma
 
- Yeni oluşturulan kimliği atama

### <a name="create-identity"></a>Kimlik oluşturma

Bu bölümde, kullanıcı tarafından atanan bir kimliğin nasıl oluşturulacak olduğu gösterilmektedir. Kullanıcı tarafından atanan kimlik, tek başına bir Azure kaynağı olarak oluşturulur. [Azure, Yeni Kullanıcı Atanmış Kimliği'ni](/powershell/module/az.managedserviceidentity/get-azuserassignedidentity)kullanarak Azure AD kiracınızda bir veya daha fazla Azure hizmet örneğine atanabilecek bir kimlik oluşturur.

[!INCLUDE [ua-character-limit](~/includes/managed-identity-ua-character-limits.md)]

```azurepowershell-interactive
New-AzUserAssignedIdentity -ResourceGroupName myResourceGroupVM -Name ID1
```

Yanıt, aşağıdaki örneğe benzer biçimde, oluşturulmuş kullanıcı tarafından atanan kimliğin ayrıntılarını içerir. Sonraki adımlarda kullanılacağından kullanıcı tarafından atanan kimliğin `Id` ve `ClientId` değerlerini not edin:

```azurepowershell
{
Id: /subscriptions/<SUBSCRIPTIONID>/resourcegroups/myResourceGroupVM/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1
ResourceGroupName : myResourceGroupVM
Name: ID1
Location: westus
TenantId: 733a8f0e-ec41-4e69-8ad8-971fc4b533f8
PrincipalId: e591178e-b785-43c8-95d2-1397559b2fb9
ClientId: af825a31-b0e0-471f-baea-96de555632f9
ClientSecretUrl: https://control-westus.identity.azure.net/subscriptions/<SUBSCRIPTIONID>/resourcegroups/myResourceGroupVM/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1/credentials?tid=733a8f0e-ec41-4e69-8ad8-971fc4b533f8&oid=e591178e-b785-43c8-95d2-1397559b2fb9&aid=af825a31-b0e0-471f-baea-96de555632f9
Type: Microsoft.ManagedIdentity/userAssignedIdentities
}
```

### <a name="assign-identity"></a>Kimlik atama

Bu bölümde, kullanıcı tarafından atanan kimliğin bir Windows VM'ye nasıl atandığı gösterilmektedir. Kullanıcı tarafından atanan kimlik, istemciler tarafından birden çok Azure kaynağında kullanılabilir. Aşağıdaki komutları kullanarak kullanıcı tarafından atanan kimliği tek bir VM'ye atayın. `-IdentityID` parametresi için önceki adımda döndürülen `Id` özelliğini kullanın.

```azurepowershell-interactive
$vm = Get-AzVM -ResourceGroupName myResourceGroup -Name myVM
Update-AzVM -ResourceGroupName TestRG -VM $vm -IdentityType "UserAssigned" -IdentityID "/subscriptions/<SUBSCRIPTIONID>/resourcegroups/myResourceGroupVM/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1"
```

## <a name="grant-access"></a>Erişim verme 

Bu bölümde, Azure Kaynak Yöneticisi'nde bir Kaynak Grubuna kullanıcı tarafından atanan kimlik erişimi nin nasıl verilebildiğini gösterilmektedir. Azure kaynakları için yönetilen kimlikler, kodunuzun Azure AD kimlik doğrulamasını destekleyen kaynak API'lerinde kimlik doğrulaması yapmak amacıyla erişim belirteçleri istemek için kullanabileceği kimlikleri sağlar. Bu öğreticide, kodunuz Azure Resource Manager API’sine erişir. 

Kodunuzun API'ye erişebilmesi için önce Azure Resource Manager'da kaynağa kimlik erişimi vermeniz gerekir. Bu durumda, içinde VM'nin yer aldığı Kaynak Grubudur. `<SUBSCRIPTION ID>` değerini ortamınıza uyacak şekilde güncelleştirin.

```azurepowershell-interactive
$spID = (Get-AzUserAssignedIdentity -ResourceGroupName myResourceGroupVM -Name ID1).principalid
New-AzRoleAssignment -ObjectId $spID -RoleDefinitionName "Reader" -Scope "/subscriptions/<SUBSCRIPTIONID>/resourcegroups/myResourceGroupVM/"
```

Yanıt, aşağıdaki örneğe benzer biçimde, oluşturulmuş atamasının ayrıntılarını içerir:

```azurepowershell
RoleAssignmentId: /subscriptions/80c696ff-5efa-4909-a64d-f1b616f423ca/resourcegroups/myResourceGroupVM/providers/Microsoft.Authorization/roleAssignments/f9cc753d-265e-4434-ae19-0c3e2ead62ac
Scope: /subscriptions/80c696ff-5efa-4909-a64d-f1b616f423ca/resourcegroups/myResourceGroupVM
DisplayName: ID1
SignInName:
RoleDefinitionName: Reader
RoleDefinitionId: acdd72a7-3385-48ef-bd42-f606fba81ae7
ObjectId: e591178e-b785-43c8-95d2-1397559b2fb9
ObjectType: ServicePrincipal
CanDelegate: False
```

## <a name="access-data"></a>Verilere erişme

### <a name="get-an-access-token"></a>Bir erişim belirteci alma 

Bu öğreticinin kalan bölümünde, daha önce oluşturmuş olduğumuz VM'den çalışacaksınız.

1. Azure portalında oturum açın[https://portal.azure.com](https://portal.azure.com)

2. Portalda, **Sanal Makineler**'e ve Windows sanal makinesine gidin, ardından **Genel Bakış**'ta **Bağlan**'a tıklayın.

3. Windows VM'sini oluştururken kullandığınız **Kullanıcı adı** ve **Parola**’yı girin.

4. Artık sanal makineile uzak bir **masaüstü bağlantısı** oluşturduğunuza göre, uzak oturumda **PowerShell'i** açın.

5. PowerShell’in `Invoke-WebRequest` komutunu kullanarak, Azure kaynakları için yönetilen kimliği uç noktasına Azure Resource Manager için erişim belirteci alma isteğinde bulunun.  Değer, `client_id` kullanıcı tarafından atanan yönetilen kimliği oluşturduğunuzda döndürülen değerdir.

    ```azurepowershell
    $response = Invoke-WebRequest -Uri 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&client_id=af825a31-b0e0-471f-baea-96de555632f9&resource=https://management.azure.com/' -Method GET -Headers @{Metadata="true"}
    $content = $response.Content | ConvertFrom-Json
    $ArmToken = $content.access_token
    ```

### <a name="read-properties"></a>Özellikleri okuma

Azure Resource Manager’a erişmek için önceki adımda alınan erişim belirtecini kullanın ve kullanıcı tarafından atanan kimliğiniz için erişim verdiğiniz Kaynak Grubunun özelliklerini okuyun. `<SUBSCRIPTION ID>` öğesini ortamınızın abonelik kimliğiyle değiştirin.

```azurepowershell
(Invoke-WebRequest -Uri https://management.azure.com/subscriptions/80c696ff-5efa-4909-a64d-f1b616f423ca/resourceGroups/myResourceGroupVM?api-version=2016-06-01 -Method GET -ContentType "application/json" -Headers @{Authorization ="Bearer $ArmToken"}).content
```
Yanıtta, aşağıdaki örneğe benzer belirli Kaynak Grubu bilgileri yer alır:

```json
{"id":"/subscriptions/<SUBSCRIPTIONID>/resourceGroups/myResourceGroupVM","name":"myResourceGroupVM","location":"eastus","properties":{"provisioningState":"Succeeded"}}
```

## <a name="next-steps"></a>Sonraki adımlar

Bu eğitimde, kullanıcı tarafından atanan bir kimliği niçin oluşturup Azure Kaynak Yöneticisi API'sine erişmek için azure sanal makineye nasıl ekleyip ekleyip, bu kimliği nasıl ekleyip, azure kaynak yöneticisi API'sine nasıl ekleyip ona ekleyip, bu kimliği öğrendiniz.  Azure Resource Manager hakkında daha fazla bilgi edinmek için bkz:

> [!div class="nextstepaction"]
>[Azure Resource Manager](/azure/azure-resource-manager/resource-group-overview)
