---
title: Öğretici - Terraform'u kullanarak Azure'da şirket içi sanal ağ oluşturun
description: Azure'da yerel kaynakları barındıran şirket içi bir VNet'in nasıl uygulanacağını gösteren öğretici
ms.topic: tutorial
ms.date: 10/26/2019
ms.openlocfilehash: 361f9919fdd406a1fef6bbf2b7512dbc20266a54
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/24/2020
ms.locfileid: "74159207"
---
# <a name="tutorial-create-on-premises-virtual-network-in-azure-using-terraform"></a>Öğretici: Terraform'u kullanarak Azure'da şirket içi sanal ağ oluşturun

Bu öğretici, bir Azure sanal ağı (VNet) kullanarak şirket içi bir ağın nasıl uygulanacağını gösterir. Azure VNet'inyerine kendi özel sanal ağınız değiştirilebilir. Bunu yapmak için, alt ağlarda uygun IP adreslerieş.

Aşağıdaki görevler açıklanmıştır:

> [!div class="checklist"]
> * Hub-spoke topolojisinde şirket içi Bir VNet uygulamak için HCL 'yi (HashiCorp Dili) kullanın
> * Hub ağ cihaz kaynakları oluşturmak için Terraform'u kullanın
> * Şirket içi sanal makine oluşturmak için Terraform'u kullanın
> * Şirket içi sanal özel ağ ağ geçidi oluşturmak için Terraform'u kullanın

## <a name="prerequisites"></a>Ön koşullar

1. [Azure'da Terraform ile bir hub ve kollu karma ağ topolojisi oluşturun.](./terraform-hub-spoke-introduction.md)

## <a name="create-the-directory-structure"></a>Dizin yapısını oluşturma

Şirket içi ağı simüle etmek için bir Azure sanal ağı oluşturun. Demo VNet gerçek bir özel şirket içi ağın yerini alır. Mevcut şirket içi ağınızda da aynısını yapmak için alt ağlarda uygun IP adreslerini haritalandırın.

1. [Azure portalına](https://portal.azure.com)göz atın.

1. [Azure Cloud Shell](/azure/cloud-shell/overview)'i açın. Önceden bir ortam seçmediyseniz **Bash** ortamını seçin.

    ![Cloud Shell istemi](./media/terraform-common/azure-portal-cloud-shell-button-min.png)

1. `clouddrive` dizinine geçin.

    ```bash
    cd clouddrive
    ```

1. Dizinleri yeni dizinle değiştirin:

    ```bash
    cd hub-spoke
    ```

## <a name="declare-the-on-premises-vnet"></a>Şirket içi VNet'i bildirin

Şirket içi VNet'i bildiren Terraform yapılandırma dosyasını oluşturun.

1. Bulut Kabuğu'nda, '' `on-prem.tf`adlı yeni bir dosya açın.

    ```bash
    code on-prem.tf
    ```

1. Aşağıdaki kodu düzenleyiciye yapıştırın:

    ```hcl
    locals {
      onprem-location       = "SouthCentralUS"
      onprem-resource-group = "onprem-vnet-rg"
      prefix-onprem         = "onprem"
    }

    resource "azurerm_resource_group" "onprem-vnet-rg" {
      name     = local.onprem-resource-group
      location = local.onprem-location
    }

    resource "azurerm_virtual_network" "onprem-vnet" {
      name                = "onprem-vnet"
      location            = azurerm_resource_group.onprem-vnet-rg.location
      resource_group_name = azurerm_resource_group.onprem-vnet-rg.name
      address_space       = ["192.168.0.0/16"]

      tags {
        environment = local.prefix-onprem
      }
    }

    resource "azurerm_subnet" "onprem-gateway-subnet" {
      name                 = "GatewaySubnet"
      resource_group_name  = azurerm_resource_group.onprem-vnet-rg.name
      virtual_network_name = azurerm_virtual_network.onprem-vnet.name
      address_prefix       = "192.168.255.224/27"
    }

    resource "azurerm_subnet" "onprem-mgmt" {
      name                 = "mgmt"
      resource_group_name  = azurerm_resource_group.onprem-vnet-rg.name
      virtual_network_name = azurerm_virtual_network.onprem-vnet.name
      address_prefix       = "192.168.1.128/25"
    }

    resource "azurerm_public_ip" "onprem-pip" {
        name                         = "${local.prefix-onprem}-pip"
        location            = azurerm_resource_group.onprem-vnet-rg.location
        resource_group_name = azurerm_resource_group.onprem-vnet-rg.name
        allocation_method   = "Dynamic"

        tags {
            environment = local.prefix-onprem
        }
    }

    resource "azurerm_network_interface" "onprem-nic" {
      name                 = "${local.prefix-onprem}-nic"
      location             = azurerm_resource_group.onprem-vnet-rg.location
      resource_group_name  = azurerm_resource_group.onprem-vnet-rg.name
      enable_ip_forwarding = true

      ip_configuration {
        name                          = local.prefix-onprem
        subnet_id                     = azurerm_subnet.onprem-mgmt.id
        private_ip_address_allocation = "Dynamic"
        public_ip_address_id          = azurerm_public_ip.onprem-pip.id
      }
    }

    # Create Network Security Group and rule
    resource "azurerm_network_security_group" "onprem-nsg" {
        name                = "${local.prefix-onprem}-nsg"
        location            = azurerm_resource_group.onprem-vnet-rg.location
        resource_group_name = azurerm_resource_group.onprem-vnet-rg.name

        security_rule {
            name                       = "SSH"
            priority                   = 1001
            direction                  = "Inbound"
            access                     = "Allow"
            protocol                   = "Tcp"
            source_port_range          = "*"
            destination_port_range     = "22"
          source_address_prefix      = "*"
            destination_address_prefix = "*"
        }

        tags {
            environment = "onprem"
        }
    }

    resource "azurerm_subnet_network_security_group_association" "mgmt-nsg-association" {
      subnet_id                 = azurerm_subnet.onprem-mgmt.id
      network_security_group_id = azurerm_network_security_group.onprem-nsg.id
    }

    resource "azurerm_virtual_machine" "onprem-vm" {
      name                  = "${local.prefix-onprem}-vm"
      location              = azurerm_resource_group.onprem-vnet-rg.location
      resource_group_name   = azurerm_resource_group.onprem-vnet-rg.name
      network_interface_ids = [azurerm_network_interface.onprem-nic.id]
      vm_size               = var.vmsize

      storage_image_reference {
        publisher = "Canonical"
        offer     = "UbuntuServer"
        sku       = "16.04-LTS"
        version   = "latest"
      }

      storage_os_disk {
        name              = "myosdisk1"
        caching           = "ReadWrite"
        create_option     = "FromImage"
        managed_disk_type = "Standard_LRS"
      }

      os_profile {
        computer_name  = "${local.prefix-onprem}-vm"
        admin_username = var.username
        admin_password = var.password
      }

      os_profile_linux_config {
        disable_password_authentication = false
      }

      tags {
        environment = local.prefix-onprem
      }
    }

    resource "azurerm_public_ip" "onprem-vpn-gateway1-pip" {
      name                = "${local.prefix-onprem}-vpn-gateway1-pip"
      location            = azurerm_resource_group.onprem-vnet-rg.location
      resource_group_name = azurerm_resource_group.onprem-vnet-rg.name

      allocation_method = "Dynamic"
    }

    resource "azurerm_virtual_network_gateway" "onprem-vpn-gateway" {
      name                = "onprem-vpn-gateway1"
      location            = azurerm_resource_group.onprem-vnet-rg.location
      resource_group_name = azurerm_resource_group.onprem-vnet-rg.name

      type     = "Vpn"
      vpn_type = "RouteBased"

      active_active = false
      enable_bgp    = false
      sku           = "VpnGw1"

      ip_configuration {
        name                          = "vnetGatewayConfig"
        public_ip_address_id          = azurerm_public_ip.onprem-vpn-gateway1-pip.id
        private_ip_address_allocation = "Dynamic"
        subnet_id                     = azurerm_subnet.onprem-gateway-subnet.id
      }
      depends_on = ["azurerm_public_ip.onprem-vpn-gateway1-pip"]

    }
    ```

1. Dosyayı kaydedin ve düzenleyiciden çıkın.

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [Azure'da Terraform ile hub sanal ağı oluşturun](./terraform-hub-spoke-hub-network.md)