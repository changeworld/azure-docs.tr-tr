---
title: Öğretici - Modül Kayıt Defterini Kullanarak Terraform ile Azure VM kümesi oluşturma
description: Bu eğitimde, Azure'da bir Windows sanal makine kümesi oluşturmak için Terraform modüllerini kullanıyorsunuz
keywords: masmavi devops terraform vm sanal makine küme modülü kayıt defteri
ms.topic: tutorial
ms.date: 03/09/2020
ms.openlocfilehash: 9faeee9bb2f0fb6dc148a3868f6fc0dae3833a2a
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/24/2020
ms.locfileid: "78945282"
---
# <a name="tutorial-create-an-azure-vm-cluster-with-terraform-using-the-module-registry"></a>Öğretici: Modül Kayıt Defterini kullanarak Terraform ile Azure VM kümesi oluşturun

Bu makalede Terraform [Azure işlem modülü](https://registry.terraform.io/modules/Azure/compute/azurerm/1.0.2) ile küçük bir VM kümesi oluşturma adımları gösterilmektedir. Bu öğreticide şunların nasıl yapıldığını öğrenirsiniz: 

> [!div class="checklist"]
> * Azure ile kimlik doğrulamasını ayarlama
> * Terraform şablonunu oluşturma
> * Plan ile değişiklikleri görselleştirme
> * VM kümesini oluşturmak için yapılandırmayı uygulama

Terraform hakkında daha fazla bilgi için [Terraform belgelerini](https://www.terraform.io/docs/index.html) inceleyin.

## <a name="set-up-authentication-with-azure"></a>Azure ile kimlik doğrulamasını ayarlama

> [!TIP]
> [Terraform ortam değişkenlerini kullanıyorsanız](terraform-install-configure.md) veya bu [Azure Cloud Shell](/azure/cloud-shell/overview) öğreticisini çalıştırdıysanız bu adımı atlayın.

 Azure hizmet sorumlusu oluşturmak için [Terraform'u yükleyin ve Azure erişimini yapılandırın](terraform-install-configure.md) sayfasını inceleyin. Aşağıdaki kodla boş bir dizinde yeni bir `azureProviderAndCreds.tf` dosyası oluşturmak için bu hizmet sorumlusunu kullanın:

```hcl
variable subscription_id {}
variable tenant_id {}
variable client_id {}
variable client_secret {}

provider "azurerm" {
    version = "~>1.40"

    subscription_id = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    tenant_id = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    client_id = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    client_secret = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
}
```

## <a name="create-the-template"></a>Şablonu oluşturma

Aşağıdaki kodu kullanarak `main.tf` adlı yeni bir Terraform şablonu oluşturun:

```hcl
module mycompute {
    source = "Azure/compute/azurerm"
    resource_group_name = "myResourceGroup"
    location = "East US 2"
    admin_password = "ComplxP@assw0rd!"
    vm_os_simple = "WindowsServer"
    is_windows_image = "true"
    remote_port = "3389"
    nb_instances = 2
    public_ip_dns = ["unique_dns_name"]
    vnet_subnet_id = module.network.vnet_subnets[0]
}

module "network" {
    source = "Azure/network/azurerm"
    location = "East US 2"
    resource_group_name = "myResourceGroup"
}

output "vm_public_name" {
    value = module.mycompute.public_ip_dns_name
}

output "vm_public_ip" {
    value = module.mycompute.public_ip_address
}

output "vm_private_ips" {
    value = module.mycompute.network_interface_private_ip
}
```

Yapılandırma dizininizde `terraform init` komutunu çalıştırın. Terraform 0.10.6 sürümü ve sonrası aşağıdaki çıktıyı gösterir:

![Terraform Init](media/terraformInitWithModules.png)

## <a name="visualize-the-changes-with-plan"></a>Plan ile değişiklikleri görselleştirme

Şablon tarafından oluşturulan sanal makine altyapısını önizlemek için `terraform plan` komutunu çalıştırın.

![Terraform Plan](media/terraform-create-vm-cluster-with-infrastructure/terraform-plan.png)


## <a name="create-the-virtual-machines-with-apply"></a>apply ile sanal makineleri oluşturma

VM'leri Azure'da sağlamak için `terraform apply` komutunu çalıştırın.

![Terraform Apply](media/terraform-create-vm-cluster-with-infrastructure/terraform-apply.png)

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"] 
> [Azure Terraform modüllerinin listesine göz atın](https://registry.terraform.io/modules/Azure)