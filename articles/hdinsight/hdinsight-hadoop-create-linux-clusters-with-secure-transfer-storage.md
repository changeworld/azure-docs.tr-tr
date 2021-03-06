---
title: Apache Hadoop güvenli aktarım depolama & - Azure HDInsight
description: Güvenli aktarım özellikli Azure depolama hesapları ile HDInsight kümeleri oluşturma hakkında bilgi edinin.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 02/18/2020
ms.openlocfilehash: c1e5ca8b0bb828e5e8ce896bba6a5278266b118e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77560091"
---
# <a name="apache-hadoop-clusters-with-secure-transfer-storage-accounts-in-azure-hdinsight"></a>Azure HDInsight'ta güvenli aktarım depolama hesaplarıyla Apache Hadoop kümeleri

[Güvenli aktarım gereklidir](../storage/common/storage-require-secure-transfer.md) özelliği, güvenli bir bağlantı üzerinden tüm istekleri hesabınıza uygulayarak Azure Depolama hesabınızın güvenliğini artırır. Bu özellik ve wasbs şeması yalnızca HDInsight kümesi 3.6 veya sonraki sürümlerde desteklenir.

> [!IMPORTANT]
> Bir küme oluşturduktan sonra güvenli depolama aktarımını etkinleştirmek depolama hesabınızı kullanarak hatalara neden olabilir ve önerilmez. Zaten etkin leştirilmiş güvenli aktarım içeren bir depolama hesabı kullanarak yeni bir küme oluşturmak daha iyidir.

## <a name="storage-accounts"></a>Depolama hesapları

### <a name="azure-portal"></a>Azure portalında

Varsayılan olarak, Azure portalında bir depolama hesabı oluşturduğunuzda güvenli aktarım gerekli özelliği etkinleştirilir.

Varolan bir depolama hesabını Azure portalıyla güncelleştirmek için [bkz.](../storage/common/storage-require-secure-transfer.md#require-secure-transfer-for-an-existing-storage-account)

### <a name="powershell"></a>PowerShell

PowerShell cmdlet [New-AzStorageAccount](https://docs.microsoft.com/powershell/module/az.storage/new-azstorageaccount)için, `-EnableHttpsTrafficOnly` parametrenin `1`.

PowerShell ile mevcut bir depolama hesabını güncelleştirmek için [powershell ile güvenli aktarım gerektir'](../storage/common/storage-require-secure-transfer.md#require-secure-transfer-with-powershell)e bakın.

### <a name="azure-cli"></a>Azure CLI

Azure CLI komut [u depolama hesabı](https://docs.microsoft.com/cli/azure/storage/account?view=azure-cli-latest#az-storage-account-create)oluşturmak `--https-only` için `true`parametrenin .

Varolan bir depolama hesabını Azure CLI ile güncelleştirmek için [bkz.](../storage/common/storage-require-secure-transfer.md#require-secure-transfer-with-azure-cli)

## <a name="add-additional-storage-accounts"></a>Başka depolama hesapları ekleme

Güvenli aktarım özellikli başka depolama hesapları eklemek için birkaç seçenek vardır:

* Son bölümdeki Azure Resource Manager şablonunu değiştirin.
* [Azure portalını](https://portal.azure.com) kullanarak bir küme oluşturun ve bağlı depolama hesabını belirtin.
* Var olan bir HDInsight kümesine güvenli aktarım özellikli başka depolama hesapları eklemek için betik eylemini kullanın. Daha fazla bilgi için bkz. [HDInsight’a başka depolama hesapları ekleme](hdinsight-hadoop-add-storage.md).

## <a name="next-steps"></a>Sonraki adımlar

* Varsayılan veri deposu olarak [Apache Hadoop HDFS](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsUserGuide.html) yerine Azure Depolama (WASB) kullanımı
* HDInsight’ın Azure Depolama’yı nasıl kullandığı hakkında daha fazla bilgi için bkz. [HDInsight ile Azure Depolama kullanma](hdinsight-hadoop-use-blob-storage.md).
* HDInsight’a veril yükleme hakkında daha fazla bilgi için bkz. [Verileri HDInsight’a yükleme](hdinsight-upload-data.md).
