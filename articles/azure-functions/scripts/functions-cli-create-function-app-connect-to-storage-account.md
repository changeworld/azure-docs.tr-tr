---
title: Bağlı depolama alanıyla bir işlev uygulaması oluşturma - Azure CLI
description: Azure CLI Betiği Örneği - Azure Depolama’ya bağlanan bir Azure İşlevi oluşturma
ms.topic: sample
ms.date: 04/20/2017
ms.custom: mvc
ms.openlocfilehash: 833b9223d473c8bfc62485e9e47ba662a4f0e154
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/24/2020
ms.locfileid: "75922681"
---
# <a name="create-a-function-app-with-a-named-storage-account-connection"></a>Adlandırılmış Depolama hesabı bağlantısı olan bir işlev uygulaması oluşturma 

Bu Azure İşlevleri örnek betiği, bir işlev uygulaması oluşturur ve işlevi bir Azure Depolama hesabına bağlar. Bağlantıyı içeren oluşturulan uygulama ayarı bir [depolama tetikleyicisi veya bağlama](../functions-bindings-storage-blob.md)ile kullanılabilir. 

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

CLI’yi yerel olarak kullanırsanız, Azure CLI sürüm 2.0 veya sonraki bir sürümü çalıştırdığınızdan emin olun. Sürümü bulmak için `az --version` komutunu çalıştırın. Yükleme veya yükseltme yapmanız gerekirse bkz. [Azure CLI’yı yükleme](/cli/azure/install-azure-cli). 

## <a name="sample-script"></a>Örnek betik

Bu örnek bir Azure İşlev uygulaması oluşturur ve depolama bağlantı dizesini bir uygulama ayarına ekler.

[!code-azurecli-interactive[main](../../../cli_scripts/azure-functions/create-function-app-connect-to-storage/create-function-app-connect-to-storage-account.sh "Integrate Function App into Azure Storage Account")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Betik açıklaması

Bu betik aşağıdaki komutları kullanır. Tablodaki her komut, komuta özgü belgelere yönlendirir.

| Komut | Notlar |
|---|---|
| [az group create](/cli/azure/group#az-group-create) | Konum ile bir kaynak grubu oluşturun. |
| [az storage account create](/cli/azure/storage/account#az-storage-account-create) | Depolama hesabı oluşturma. |
| [az functionapp create](/cli/azure/functionapp#az-functionapp-create) | Sunucusuz [Tüketim planında](../functions-scale.md#consumption-plan)bir işlev uygulaması oluşturur. |
| [az depolama hesabı show-connection-string](/cli/azure/storage/account#az-storage-account-show-connection-string) | Hesap için bağlantı dizesini alır. |
| [az functionapp config appsettings seti](/cli/azure/functionapp/config/appsettings#az-functionapp-config-appsettings-set) | Bağlantı dizesini işlev uygulamasında bir uygulama ayarı olarak ayarlar. |

## <a name="next-steps"></a>Sonraki adımlar

Azure CLI hakkında daha fazla bilgi için bkz. [Azure CLI belgeleri](/cli/azure).

Ek Azure İşlevleri CLI betiği örnekleri, [Azure İşlevleri belgelerinde](../functions-cli-samples.md) bulunabilir.
