---
title: CLI komut dosyası - Sunucuyu geri yükleme - MySQL için Azure Veritabanı
description: Bu örnek Azure CLI betiğinde bir MySQL için Azure Veritabanı sunucunu ve veritabanlarını zamanda önceki bir noktaya geri yükleme gösterilmektedir.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.devlang: azurecli
ms.topic: sample
ms.custom: mvc
ms.date: 12/02/2019
ms.openlocfilehash: 5a84a44a4ea941bf3b77f0762fc764ec069919c7
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/24/2020
ms.locfileid: "74771337"
---
# <a name="restore-an-azure-database-for-mysql-server-using-azure-cli"></a>Azure CLI kullanarak MySQL için Azure Veritabanı sunucusunu geri yükleme
Bu örnek CLI betiği, tek bir MySQL için Azure Veritabanı sunucusunu zaman içinde önceki bir noktaya geri yükler.

[!INCLUDE [cloud-shell-try-it](../../../includes/cloud-shell-try-it.md)]

CLI aracını yerel olarak çalıştırmayı tercih ederseniz bu makale için Azure CLI aracının 2.0 veya sonraki bir sürümü gerekir. `az --version` komutunu çalıştırarak sürümü denetleyin. Azure CLI aracını yüklemek veya sürümünüzü yükseltmek için bkz. [Azure CLI’yi Yükleme]( /cli/azure/install-azure-cli). 

## <a name="sample-script"></a>Örnek betik
Bu örnek betikte, vurgulanan satırları düzenleyerek yönetici kullanıcı adını ve parolasını kendi değerlerinizle güncelleştirin. `az monitor` komutlarında kullanılan abonelik kimliğini kendi abonelik kimliğinizle değiştirin.
[!code-azurecli-interactive[main](../../../cli_scripts/mysql/backup-restore-pitr/backup-restore.sh?highlight=15-16 "Restore Azure Database for MySQL.")]

## <a name="clean-up-deployment"></a>Dağıtımı temizleme
Betik çalıştırıldıktan sonra aşağıdaki komutu kullanarak kaynak grubunu ve bununla ilişkili tüm kaynakları kaldırın. 
[!code-azurecli-interactive[main](../../../cli_scripts/mysql/backup-restore-pitr/delete-mysql.sh  "Delete the resource group.")]

## <a name="script-explanation"></a>Betik açıklaması
Bu betik, aşağıdaki tabloda ana hatları verilen komutları kullanır:

| **Komut** | **Notlar** |
|---|---|
| [az group create](/cli/azure/group#az-group-create) | Tüm kaynakların depolandığı bir kaynak grubu oluşturur. |
| [az mysql server create](/cli/azure/mysql/server#az-mysql-server-create) | Veritabanlarını barındıran bir MySQL sunucusu oluşturur. |
| [az mysql server restore](/cli/azure/mysql/server#az-mysql-server-restore) | Bir sunucuyu yedekten geri yükler. |
| [az group delete](/cli/azure/group#az-group-delete) | Bir kaynak grubunu tüm iç içe geçmiş kaynaklar dahil siler. |

## <a name="next-steps"></a>Sonraki adımlar
- Azure CLI hakkında daha fazla bilgi okuyun: [Azure CLI belgeleri](/cli/azure).
- Ek betikleri deneyin: [MySQL için Azure Veritabanı Azure CLI örnekleri](../sample-scripts-azure-cli.md)
