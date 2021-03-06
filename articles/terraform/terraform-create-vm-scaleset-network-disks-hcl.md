---
title: Öğretici - Terraform kullanarak bir Azure sanal makine ölçeği kümesi oluşturun
description: Azure sanal makine ölçeği kümesini yapılandırmak ve sürümlendirmek için Terraform'u kullanmayı öğrenin.
ms.topic: tutorial
ms.date: 11/07/2019
ms.openlocfilehash: 4e445d5e6ae4b7fc4528c6d61ee2bc86870827b1
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/24/2020
ms.locfileid: "77472239"
---
# <a name="tutorial-create-an-azure-virtual-machine-scale-set-using-terraform"></a>Öğretici: Terraform kullanarak bir Azure sanal makine ölçeği kümesi oluşturun

[Azure sanal makine ölçek kümeleri,](/azure/virtual-machine-scale-sets) aynı VM'leri yapılandırmanıza olanak tanır. VM örneklerinin sayısı isteğe veya zamanlamaya göre ayarlanabilir. Daha fazla bilgi için bkz. [Azure portalında ayarlanan sanal makine ölçeğini otomatik olarak](/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-autoscale-portal)ölçeklendirin.

Bu eğitimde, aşağıdaki görevleri yapmak için [Azure Bulut Kabuğu'nu](/azure/cloud-shell/overview) nasıl kullanacağınızı öğrenirsiniz:

> [!div class="checklist"]
> * Terraform dağıtımı ayarlama
> * Terraform dağıtımı için değişkenleri ve çıkışları kullanma
> * Ağ altyapısı oluşturma ve dağıtma
> * Sanal makine ölçek kümesi oluşturma, dağıtma ve ağa ekleme
> * VM'lere SSH aracılığıyla bağlanmak için bir sıçrama kutusu oluşturma ve dağıtma

> [!NOTE]
> Bu makalede kullanılan Terraform yapılandırma dosyalarının en son sürümü [GitHub Awesome Terraform deposubulunmaktadır.](https://github.com/Azure/awesome-terraform/tree/master/codelab-vmss)

## <a name="prerequisites"></a>Ön koşullar

- **Azure aboneliği**: Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) oluşturun.

- **Terraform'u yükleme**: [Terraform'u yükleme ve Azure erişimini yapılandırma](terraform-install-configure.md) makalesindeki yönergeleri izleyin

- **Bir SSH anahtar çifti oluşturun**: Daha fazla bilgi için [Azure'daki Linux VM'leri için bir SSH ortak ve özel anahtar çiftinin nasıl oluşturulup kullanılacağına](/azure/virtual-machines/linux/mac-create-ssh-keys)bakın.

## <a name="create-the-directory-structure"></a>Dizin yapısını oluşturma

1. [Azure portalına](https://portal.azure.com)göz atın.

1. [Azure Cloud Shell](/azure/cloud-shell/overview)'i açın. Önceden bir ortam seçmediyseniz **Bash** ortamını seçin.

    ![Cloud Shell istemi](./media/terraform-create-vm-scaleset-network-disks-hcl/azure-portal-cloud-shell-button-min.png)

1. `clouddrive` dizinine geçin.

    ```bash
    cd clouddrive
    ```

1. `vmss` adlı bir dizin oluşturun.

    ```bash
    mkdir vmss
    ```

1. Dizinleri yeni dizinle değiştirin:

    ```bash
    cd vmss
    ```

## <a name="create-the-variables-definitions-file"></a>Değişken tanımı dosyasını oluşturma
Bu bölümde Terraform tarafından oluşturulan kaynakları özelleştiren değişkenleri tanımlayacaksınız.

Azure Bulut Kabuğu içinde aşağıdaki adımları yapın:

1. `variables.tf` adlı bir dosya oluşturun.

    ```bash
    code variables.tf
    ```

1. Aşağıdaki kodu düzenleyiciye yapıştırın:

   ```hcl
   variable "location" {
    description = "The location where resources will be created"
   }

   variable "tags" {
    description = "A map of the tags to use for the resources that are deployed"
    type        = "map"

    default = {
      environment = "codelab"
    }
   }

   variable "resource_group_name" {
    description = "The name of the resource group in which the resources will be created"
    default     = "myResourceGroup"
   }
   ```

1. Dosyayı kaydedin (**&lt;Ctrl>S)** ve düzenleyiciden çıkın (**&lt;Ctrl>Q**).

## <a name="create-the-output-definitions-file"></a>Çıkış tanımları dosyasını oluşturma
Bu bölümde dağıtım sonrasındaki çıkışı açıklayan dosyayı oluşturacaksınız.

Azure Bulut Kabuğu içinde aşağıdaki adımları yapın:

1. `output.tf` adlı bir dosya oluşturun.

    ```bash
    code output.tf
    ```

1. Aşağıdaki kodu düzenleyiciye yapıştırarak tam etki alanı adını (FQDN) sanal makinelerin kullanımına açın.
   :

   ```hcl
    output "vmss_public_ip" {
        value = azurerm_public_ip.vmss.fqdn
    }
   ```

1. Dosyayı kaydedin (**&lt;Ctrl>S)** ve düzenleyiciden çıkın (**&lt;Ctrl>Q**).

## <a name="define-the-network-infrastructure-in-a-template"></a>Şablonda ağ altyapısını tanımlama
Bu bölümde yeni bir Azure kaynak grubunda aşağıdaki ağ altyapısını oluşturacaksınız:

  - 10.0.0.0/16 adres alanına sahip bir sanal ağ
  - 10.0.2.0/24 adres alanına sahip bir alt ağ
  - İki genel IP adresi. Biri sanal makine ölçek kümesi yük dengeleyici tarafından, diğeri ise SSH sıçrama kutusuna bağlanmak için kullanılır.

Azure Bulut Kabuğu içinde aşağıdaki adımları yapın:

1. Sanal makine ölçek kümesi altyapısını açıklayacak `vmss.tf` adlı bir dosya oluşturun.

    ```bash
    code vmss.tf
    ```

1. Aşağıdaki kodu dosyanın sonuna yapıştırarak tam etki alanı adını (FQDN) sanal makinelerin kullanımına açın.

   ```hcl
   resource "azurerm_resource_group" "vmss" {
    name     = var.resource_group_name
    location = var.location
    tags     = var.tags
   }

   resource "random_string" "fqdn" {
    length  = 6
    special = false
    upper   = false
    number  = false
   }

   resource "azurerm_virtual_network" "vmss" {
    name                = "vmss-vnet"
    address_space       = ["10.0.0.0/16"]
    location            = var.location
    resource_group_name = azurerm_resource_group.vmss.name
    tags                = var.tags
   }

   resource "azurerm_subnet" "vmss" {
    name                 = "vmss-subnet"
    resource_group_name  = azurerm_resource_group.vmss.name
    virtual_network_name = azurerm_virtual_network.vmss.name
    address_prefix       = "10.0.2.0/24"
   }

   resource "azurerm_public_ip" "vmss" {
    name                         = "vmss-public-ip"
    location                     = var.location
    resource_group_name          = azurerm_resource_group.vmss.name
    allocation_method = "Static"
    domain_name_label            = random_string.fqdn.result
    tags                         = var.tags
   }
   ```

1. Dosyayı kaydedin (**&lt;Ctrl>S)** ve düzenleyiciden çıkın (**&lt;Ctrl>Q**).

## <a name="provision-the-network-infrastructure"></a>Ağ altyapısını sağlama
Yapılandırma dosyalarını (.tf) oluşturduğunuz dizinden Azure Bulut Kabuğu'nu kullanarak aşağıdaki adımları yapın:

1. Terraform'u başlatın.

   ```bash
   terraform init
   ```

1. Tanımlanan altyapıyı Azure'a dağıtmak için aşağıdaki komutu çalıştırın.

   ```bash
   terraform apply
   ```

   Terraform, değişken tanımlandığı gibi bir `location` değer `variables.tf`ister, ancak asla ayarlanmaz. `location` "West US" gibi geçerli bir konum girip Enter tuşuyla onaylayabilirsiniz. (Boşluk içeren değerleri girerken parantez kullanın.)

1. Terraform, `output.tf` dosyasında tanımlanan şekilde çıkışı yazdırır. Aşağıdaki ekran görüntüsünde gösterildiği gibi, FQDN `<ID>.<location>.cloudapp.azure.com`aşağıdaki formu alır: . Kimlik, hesaplanmış bir değerdir ve konum Terraform çalıştırırken sağladığı değerdir.

   ![Genel IP adresi için tam nitelikli alan adı ayarlı sanal makine ölçeği](./media/terraform-create-vm-scaleset-network-disks-hcl/fqdn.png)

1. Azure portal ana menüsünden **Kaynak grupları**'nı seçin.

1. Terraform tarafından oluşturulan kaynakları görüntülemek için **Kaynak grupları** sekmesinde **myResourceGroup** kaynak grubunu seçin.
   ![Sanal makine ölçek kümesi ağ kaynakları](./media/terraform-create-vm-scaleset-network-disks-hcl/resource-group-resources.png)

## <a name="add-a-virtual-machine-scale-set"></a>Sanal makine ölçek kümesi ekleme

Bu bölümde şablona aşağıdaki kaynakları eklemeyi öğreneceksiniz:

- Uygulamayı sunmak ve bu makalenin önceki bölümlerinde yapılandırılan genel IP adresine eklemek için bir Azure yük dengeleyici ve kurallar
- Yük dengeleyiciye atanacak Azure arka uç adres havuzu
- Uygulama tarafından kullanılan ve yük dengeleyici üzerinde yapılandırılan sistem durumu yoklama bağlantı noktası
- Bu makalenin önceki bölümlerinde dağıtılan sanal ağ üzerinde çalışan ve yük dengeleyicinin arkasında bulunan bir sanal makine ölçek kümesi
- [cloud-init](https://cloudinit.readthedocs.io/en/latest/) kullanılarak yüklenen ve sanal makine ölçek kümesi düğümlerinde bulunan [Nginx](https://nginx.org/).

Cloud Shell'de aşağıdaki adımları yapın:

1. `vmss.tf` yapılandırma dosyasını açın.

   ```bash
   code vmss.tf
   ```

1. Dosyanın sonuna gidin ve A tuşuna basarak ekleme moduna geçin.

1. Dosyanın sonuna aşağıdaki kodu yapıştırın:

   ```hcl
   resource "azurerm_lb" "vmss" {
    name                = "vmss-lb"
    location            = var.location
    resource_group_name = azurerm_resource_group.vmss.name

    frontend_ip_configuration {
      name                 = "PublicIPAddress"
      public_ip_address_id = azurerm_public_ip.vmss.id
    }

    tags = var.tags
   }

   resource "azurerm_lb_backend_address_pool" "bpepool" {
    resource_group_name = azurerm_resource_group.vmss.name
    loadbalancer_id     = azurerm_lb.vmss.id
    name                = "BackEndAddressPool"
   }

   resource "azurerm_lb_probe" "vmss" {
    resource_group_name = azurerm_resource_group.vmss.name
    loadbalancer_id     = azurerm_lb.vmss.id
    name                = "ssh-running-probe"
    port                = var.application_port
   }

   resource "azurerm_lb_rule" "lbnatrule" {
      resource_group_name            = azurerm_resource_group.vmss.name
      loadbalancer_id                = azurerm_lb.vmss.id
      name                           = "http"
      protocol                       = "Tcp"
      frontend_port                  = var.application_port
      backend_port                   = var.application_port
      backend_address_pool_id        = azurerm_lb_backend_address_pool.bpepool.id
      frontend_ip_configuration_name = "PublicIPAddress"
      probe_id                       = azurerm_lb_probe.vmss.id
   }

   resource "azurerm_virtual_machine_scale_set" "vmss" {
    name                = "vmscaleset"
    location            = var.location
    resource_group_name = azurerm_resource_group.vmss.name
    upgrade_policy_mode = "Manual"

    sku {
      name     = "Standard_DS1_v2"
      tier     = "Standard"
      capacity = 2
    }

    storage_profile_image_reference {
      publisher = "Canonical"
      offer     = "UbuntuServer"
      sku       = "16.04-LTS"
      version   = "latest"
    }

    storage_profile_os_disk {
      name              = ""
      caching           = "ReadWrite"
      create_option     = "FromImage"
      managed_disk_type = "Standard_LRS"
    }

    storage_profile_data_disk {
      lun          = 0
      caching        = "ReadWrite"
      create_option  = "Empty"
      disk_size_gb   = 10
    }

    os_profile {
      computer_name_prefix = "vmlab"
      admin_username       = var.admin_user
      admin_password       = var.admin_password
      custom_data          = file("web.conf")
    }

    os_profile_linux_config {
      disable_password_authentication = false
    }

    network_profile {
      name    = "terraformnetworkprofile"
      primary = true

      ip_configuration {
        name                                   = "IPConfiguration"
        subnet_id                              = azurerm_subnet.vmss.id
        load_balancer_backend_address_pool_ids = [azurerm_lb_backend_address_pool.bpepool.id]
        primary = true
      }
    }

    tags = var.tags
   }
   ```

1. Dosyayı kaydedin ve aşağıdaki komutu girerek VI düzenleyicisini kapatın:

    ```bash
    :wq
    ```

1. Ölçek kümesine dahil olan sanal makineler için cloud-init yapılandırması görevi görecek `web.conf` adlı bir dosya oluşturun.

    ```bash
    code web.conf
    ```

1. Aşağıdaki kodu düzenleyiciye yapıştırın:

   ```hcl
   #cloud-config
   packages:
    - nginx
   ```

1. Dosyayı kaydedin ve aşağıdaki komutu girerek VI düzenleyicisini kapatın:

     ```bash
     :wq
     ```

1. `variables.tf` yapılandırma dosyasını açın.

    ```bash
    code variables.tf
    ```

1. Dosyanın sonuna gidin ve A tuşuna basarak ekleme moduna geçin.

1. Dağıtımı özelleştirmek için dosyanın sonuna aşağıdaki kodu yapıştırın:

    ```hcl
    variable "application_port" {
       description = "The port that you want to expose to the external load balancer"
       default     = 80
    }

    variable "admin_user" {
       description = "User name to use as the admin account on the VMs that will be part of the VM Scale Set"
       default     = "azureuser"
    }

    variable "admin_password" {
       description = "Default password for admin account"
    }
    ```

1. Dosyayı kaydedin (**&lt;Ctrl>S)** ve düzenleyiciden çıkın (**&lt;Ctrl>Q**).

1. Sanal makine ölçek kümesi dağıtımını görselleştirmek için bir Terraform planı oluşturun. (Kaynaklarınızın konumuna ek olarak bir parola da belirlemeniz gerekir.)

    ```bash
    terraform plan
    ```

    Komut çıktısı aşağıdaki ekran görüntüsüne benzer olmalıdır:

    ![Sanal makine ölçek kümesi oluşturma işleminin çıkışı](./media/terraform-create-vm-scaleset-network-disks-hcl/add-mvss-plan.png)

1. Yeni kaynakları Azure'a dağıtın.

    ```bash
    terraform apply
    ```

    Komut çıktısı aşağıdaki ekran görüntüsüne benzer olmalıdır:

    ![Terraform sanal makine ölçek kümesi kaynak grubu](./media/terraform-create-vm-scaleset-network-disks-hcl/resource-group-contents.png)

1. Bir tarayıcı penceresi açın ve komutun döndürdüğü FQDN değerine bağlanın.

    ![FQDN değerine göz atma sonuçları](./media/terraform-create-vm-scaleset-network-disks-hcl/browser-fqdn.png)

## <a name="add-an-ssh-jumpbox"></a>SSH sıçrama kutusu ekleme
SSH *atlama kutusu,* ağdaki diğer sunuculara erişmek için "atladığınız" tek bir sunucudur. Bu adımda aşağıdaki kaynakları oluşturacaksınız:

- Sanal makine ölçek kümesiyle aynı alt ağa bağlı olan bir ağ arabirimi (veya sıçrama kutusu).

- Bu ağ arabirimine bağlı bir sanal makine. Bu "sıçrama kutusuna" uzaktan erişim sağlanabilir. Bağlantı kurduktan sonra SSH ile ölçek kümesindeki sanal makinelere bağlanabilirsiniz.

1. `vmss.tf` yapılandırma dosyasını açın.

   ```bash
   code vmss.tf
   ```

1. Dosyanın sonuna gidin ve A tuşuna basarak ekleme moduna geçin.

1. Dosyanın sonuna aşağıdaki kodu yapıştırın:

   ```hcl
   resource "azurerm_public_ip" "jumpbox" {
    name                         = "jumpbox-public-ip"
    location                     = var.location
    resource_group_name          = azurerm_resource_group.vmss.name
    allocation_method = "Static"
    domain_name_label            = "${random_string.fqdn.result}-ssh"
    tags                         = var.tags
   }

   resource "azurerm_network_interface" "jumpbox" {
    name                = "jumpbox-nic"
    location            = var.location
    resource_group_name = azurerm_resource_group.vmss.name

    ip_configuration {
      name                          = "IPConfiguration"
      subnet_id                     = azurerm_subnet.vmss.id
      private_ip_address_allocation = "dynamic"
      public_ip_address_id          = azurerm_public_ip.jumpbox.id
    }

    tags = var.tags
   }

   resource "azurerm_virtual_machine" "jumpbox" {
    name                  = "jumpbox"
    location              = var.location
    resource_group_name   = azurerm_resource_group.vmss.name
    network_interface_ids = [azurerm_network_interface.jumpbox.id]
    vm_size               = "Standard_DS1_v2"

    storage_image_reference {
      publisher = "Canonical"
      offer     = "UbuntuServer"
      sku       = "16.04-LTS"
      version   = "latest"
    }

    storage_os_disk {
      name              = "jumpbox-osdisk"
      caching           = "ReadWrite"
      create_option     = "FromImage"
      managed_disk_type = "Standard_LRS"
    }

    os_profile {
      computer_name  = "jumpbox"
      admin_username = var.admin_user
      admin_password = var.admin_password
    }

    os_profile_linux_config {
      disable_password_authentication = false
    }

    tags = var.tags
   }
   ```

1. `output.tf` yapılandırma dosyasını açın.

   ```bash
   code output.tf
   ```

1. Dosyanın sonuna gidin ve A tuşuna basarak ekleme moduna geçin.

1. Dağıtım tamamlandığında sıçrama kutusunun ana bilgisayar adının gösterilmesi için aşağıdaki kodu dosyanın sonuna yapıştırın:

   ```hcl
   output "jumpbox_public_ip" {
      value = azurerm_public_ip.jumpbox.fqdn
   }
   ```

1. Dosyayı kaydedin (**&lt;Ctrl>S)** ve düzenleyiciden çıkın (**&lt;Ctrl>Q**).

1. Sıçrama kutusu dağıtın.

   ```bash
   terraform apply
   ```

Dağıtım tamamlandıktan sonra kaynak grubunun içeriği şu ekran görüntüsünde gösterilene benzer olacaktır:

![Terraform sanal makine ölçek kümesi kaynak grubu](./media/terraform-create-vm-scaleset-network-disks-hcl/resource-group-contents-final.png)

> [!NOTE]
> Dağıttığınız sıçrama kutusunda ve sanal makine ölçek kümesinde devre dışı bırakılmış olan bir parolayla oturum açma imkanı. Sanal makinelere erişmek için SSH ile oturum açın.

## <a name="environment-cleanup"></a>Ortamı temizleme

Bu öğreticide oluşturulan Terraform kaynaklarını silmek için Cloud Shell'e şu komutu girin:

```bash
terraform destroy
```

Yok etme işleminin tamamlanması birkaç dakika sürebilir.

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"] 
> [Azure'da Terraform'u kullanma hakkında daha fazla bilgi edinin](/azure/terraform)
