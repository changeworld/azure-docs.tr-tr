---
title: Veri şifreleme - Azure CLI - MySQL için Azure Veritabanı
description: Azure CLI'yi kullanarak MySQL için Azure Veritabanınız için veri şifrelemeyi nasıl ayarlayıp yöneteceklerinizi öğrenin.
author: kummanish
ms.author: manishku
ms.service: mysql
ms.topic: conceptual
ms.date: 03/30/2020
ms.openlocfilehash: fca9b9eea3697a10650e5dbf0522f795fe0a8e0b
ms.sourcegitcommit: b0ff9c9d760a0426fd1226b909ab943e13ade330
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/01/2020
ms.locfileid: "80522218"
---
# <a name="data-encryption-for-azure-database-for-mysql-by-using-the-azure-cli"></a>Azure CLI'yi kullanarak MySQL için Azure Veritabanı için veri şifreleme

MySQL için Azure Veritabanınız için veri şifrelemesini ayarlamak ve yönetmek için Azure CLI'yi nasıl kullanacağınızı öğrenin.

## <a name="prerequisites-for-azure-cli"></a>Azure CLI için ön koşullar

* Azure aboneliğiniz olmalı ve bu abonelikte yönetici olmalısınız.
* Müşteri tarafından yönetilen bir anahtar için kullanılacak bir anahtar ve anahtar oluşturun. Ayrıca anahtar kasasında temizleme koruması ve yumuşak silme sağlar.

    ```azurecli-interactive
    az keyvault create -g <resource_group> -n <vault_name> --enable-soft-delete true --enable-purge-protection true
    ```

* Oluşturulan Azure Anahtar Kasası'nda, MySQL için Azure Veritabanı'nın veri şifrelemesi için kullanılacak anahtarı oluşturun.

    ```azurecli-interactive
    az keyvault key create --name <key_name> -p software --vault-name <vault_name>
    ```

* Varolan bir anahtar kasasını kullanmak için, müşteri tarafından yönetilen bir anahtar olarak kullanmak için aşağıdaki özelliklere sahip olmalıdır:
  * [Yumuşak silme](../key-vault/key-vault-ovw-soft-delete.md)

    ```azurecli-interactive
    az resource update --id $(az keyvault show --name \ <key_vault_name> -o tsv | awk '{print $1}') --set \ properties.enableSoftDelete=true
    ```

  * [Temizleme korumalı](../key-vault/key-vault-ovw-soft-delete.md#purge-protection)

    ```azurecli-interactive
    az keyvault update --name <key_vault_name> --resource-group <resource_group_name>  --enable-purge-protection true
    ```

* Anahtar, müşteri tarafından yönetilen bir anahtar olarak kullanılacak aşağıdaki özniteliklere sahip olmalıdır:
  * Son kullanma tarihi yok
  * Devre dışı bırakılmadı
  * **Get**, **wrap**, **açma** işlemleri gerçekleştirin

## <a name="set-the-right-permissions-for-key-operations"></a>Önemli işlemler için doğru izinleri ayarlama

1. MySQL için Azure Veritabanınız için yönetilen kimliği almanın iki yolu vardır.

    ### <a name="create-an-new-azure-database-for-mysql-server-with-a-managed-identity"></a>MySQL sunucusu için yönetilen bir kimliğe sahip yeni bir Azure Veritabanı oluşturun.

    ```azurecli-interactive
    az mysql server create --name -g <resource_group> --location <locations> --storage-size <size>  -u <user>-p <pwd> --backup-retention <7> --sku-name <sku name> --geo-redundant-backup <Enabled/Disabled>  --assign-identity
    ```

    ### <a name="update-an-existing-the-azure-database-for-mysql-server-to-get-a-managed-identity"></a>Yönetilen bir kimliğe bulaşmak için MySQL sunucusu için varolan bir Azure Veritabanını güncelleştirin.

    ```azurecli-interactive
    az mysql server update --name  <server name>  -g <resource_group> --assign-identity
    ```

2. MySQL sunucusunun adı olan **Asıl**için **Anahtar izinlerini** **(Get**, **Wrap**, **Unwrap)** ayarlayın.

    ```azurecli-interactive
    az keyvault set-policy --name -g <resource_group> --key-permissions get unwrapKey wrapKey --object-id <principal id of the server>
    ```

## <a name="set-data-encryption-for-azure-database-for-mysql"></a>MySQL için Azure Veritabanı için veri şifrelemesi ayarlama

1. Azure Anahtar Kasası'nda oluşturulan anahtarı kullanarak MySQL için Azure Veritabanı için Veri şifrelemesini etkinleştirin.

    ```azurecli-interactive
    az mysql server key create –name  <server name>  -g <resource_group> --kid <key url>
    ```

    Anahtar url:https://YourVaultName.vault.azure.net/keys/YourKeyName/01234567890123456789012345678901>

## <a name="using-data-encryption-for-restore-or-replica-servers"></a>Geri yükleme veya çoğaltma sunucuları için Veri şifrelemesi kullanma

MySQL için Azure Veritabanı, Key Vault'ta saklanan bir müşterinin yönetilen anahtarıyla şifrelendikten sonra, sunucunun yeni oluşturulan herhangi bir kopyası da şifrelenir. Bu yeni kopyayı yerel veya coğrafi geri yükleme işlemi veya yineleme (yerel/çapraz bölge) işlemi aracılığıyla yapabilirsiniz. Yani, şifreli bir MySQL sunucusu için, şifreli bir geri yüklenen sunucu oluşturmak için aşağıdaki adımları kullanabilirsiniz.

### <a name="creating-a-restoredreplica-server"></a>Geri yüklenen/çoğaltma sunucusu oluşturma

  *  [Geri yükleme sunucusu oluşturma](howto-restore-server-cli.md) 
  *  [Okuma çoğaltma sunucusu oluşturma](howto-read-replicas-cli.md) 

### <a name="once-the-server-is-restored-revalidate-data-encryption-the-restored-server"></a>Sunucu geri yüklendikten sonra, geri yüklenen sunucunun veri şifrelemesini yeniden geçersiz kılın

    ```azurecli-interactive
    az mysql server key create –name  <server name> -g <resource_group> --kid <key url>
    ```

## <a name="additional-capability-for-the-key-being-used-for-the-azure-database-for-mysql"></a>MySQL için Azure Veritabanı için kullanılan anahtar için ek özellik

### <a name="get-the-key-used"></a>Anahtarı kullanma

    ```azurecli-interactive
    az mysql server key show --name  <server name>  -g <resource_group> --kid <key url>
    ```

    Key url:  https://YourVaultName.vault.azure.net/keys/YourKeyName/01234567890123456789012345678901>

### <a name="list-the-key-used"></a>Kullanılan Anahtarı listele

    ```azurecli-interactive
    az mysql server key list --name  <server name>  -g <resource_group>
    ```

### <a name="drop-the-key-being-used"></a>Kullanılan anahtarı bırak

    ```azurecli-interactive
    az mysql server key delete -g <resource_group> --kid <key url> 
    ```

## <a name="using-an-azure-resource-manager-template-to-enable-data-encryption"></a>Veri şifrelemesini etkinleştirmek için Azure Kaynak Yöneticisi şablonu kullanma

Azure portalıdışında, yeni ve mevcut sunucular için Azure Kaynak Yöneticisi şablonlarını kullanarak MySQL sunucusu için Azure Veritabanınızda veri şifrelemesini de etkinleştirebilirsiniz.

### <a name="for-a-new-server"></a>Yeni bir sunucu için

Sunucuya veri şifreleme etkinliği sağlamak için önceden oluşturulmuş Azure Kaynak Yöneticisi şablonlarından birini kullanın: [Veri şifrelemesi ile örnek](https://github.com/Azure/azure-mysql/tree/master/arm-templates/ExampleWithDataEncryption)

Bu Azure Kaynak Yöneticisi şablonu MySQL sunucusu için bir Azure Veritabanı oluşturur ve sunucuda veri şifrelemesini etkinleştirmek için anahtar **atlama** ve **anahtar** parametreleri olarak geçirilir.

### <a name="for-an-existing-server"></a>Varolan bir sunucu için
Ayrıca, MySQL sunucuları için mevcut Azure Veritabanınızda veri şifrelemesini etkinleştirmek için Azure Kaynak Yöneticisi şablonlarını kullanabilirsiniz.

* Özellikler nesnesindeki özelliğin altında daha önce kopyaladığınız Azure Anahtar Kasası anahtarının `Uri` Kaynak Kimliğini geçirin.

* API sürümü olarak *2020-01-01 önizleme* kullanın.

```json
{
  "$schema": "http://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "location": {
      "type": "string"
    },
    "serverName": {
      "type": "string"
    },
    "keyVaultName": {
      "type": "string",
      "metadata": {
        "description": "Key vault name where the key to use is stored"
      }
    },
    "keyVaultResourceGroupName": {
      "type": "string",
      "metadata": {
        "description": "Key vault resource group name where it is stored"
      }
    },
    "keyName": {
      "type": "string",
      "metadata": {
        "description": "Key name in the key vault to use as encryption protector"
      }
    },
    "keyVersion": {
      "type": "string",
      "metadata": {
        "description": "Version of the key in the key vault to use as encryption protector"
      }
    }
  },
  "variables": {
    "serverKeyName": "[concat(parameters('keyVaultName'), '_', parameters('keyName'), '_', parameters('keyVersion'))]"
  },
  "resources": [
    {
      "type": "Microsoft.DBforMySQL/servers",
      "apiVersion": "2017-12-01",
      "kind": "",
      "location": "[parameters('location')]",
      "identity": {
        "type": "SystemAssigned"
      },
      "name": "[parameters('serverName')]",
      "properties": {
      }
    },
    {
      "type": "Microsoft.Resources/deployments",
      "apiVersion": "2019-05-01",
      "name": "addAccessPolicy",
      "resourceGroup": "[parameters('keyVaultResourceGroupName')]",
      "dependsOn": [
        "[resourceId('Microsoft.DBforMySQL/servers', parameters('serverName'))]"
      ],
      "properties": {
        "mode": "Incremental",
        "template": {
          "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
          "contentVersion": "1.0.0.0",
          "resources": [
            {
              "type": "Microsoft.KeyVault/vaults/accessPolicies",
              "name": "[concat(parameters('keyVaultName'), '/add')]",
              "apiVersion": "2018-02-14-preview",
              "properties": {
                "accessPolicies": [
                  {
                    "tenantId": "[subscription().tenantId]",
                    "objectId": "[reference(resourceId('Microsoft.DBforMySQL/servers/', parameters('serverName')), '2017-12-01', 'Full').identity.principalId]",
                    "permissions": {
                      "keys": [
                        "get",
                        "wrapKey",
                        "unwrapKey"
                      ]
                    }
                  }
                ]
              }
            }
          ]
        }
      }
    },
    {
      "name": "[concat(parameters('serverName'), '/', variables('serverKeyName'))]",
      "type": "Microsoft.DBforMySQL/servers/keys",
      "apiVersion": "2020-01-01-preview",
      "dependsOn": [
        "addAccessPolicy",
        "[resourceId('Microsoft.DBforMySQL/servers', parameters('serverName'))]"
      ],
      "properties": {
        "serverKeyType": "AzureKeyVault",
        "uri": "[concat(reference(resourceId(parameters('keyVaultResourceGroupName'), 'Microsoft.KeyVault/vaults/', parameters('keyVaultName')), '2018-02-14-preview', 'Full').properties.vaultUri, 'keys/', parameters('keyName'), '/', parameters('keyVersion'))]"
      }
    }
  ]
}

```

## <a name="next-steps"></a>Sonraki adımlar

 Veri şifreleme hakkında daha fazla bilgi edinmek [için müşteri tarafından yönetilen anahtarla MySQL veri şifrelemeiçin Azure Veritabanı'na](concepts-data-encryption-mysql.md)bakın.
