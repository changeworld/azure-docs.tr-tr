---
title: PowerShell örnek taşıma Azure SQL veritabanı-elastik havuz
description: PowerShell kullanarak elastik havuzlar arasında bir SQL veritabanını taşımak için Azure PowerShell örnek betiği
services: sql-database
ms.service: sql-database
ms.subservice: elastic-pools
ms.custom: ''
ms.devlang: PowerShell
ms.topic: sample
author: stevestein
ms.reviewer: ''
ms.author: sstein
ms.date: 03/12/2019
ms.openlocfilehash: 1f4465201d93f5bd48bda82b1d3b7e1ebcdc950e
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/24/2020
ms.locfileid: "73691574"
---
# <a name="use-powershell-to-create-elastic-pools-and-move-databases-between-elastic-pools"></a>PowerShell kullanarak elastik havuzlar oluşturma ve elastik havuzlar arasında veritabanlarını taşıma

Bu PowerShell betiği örneği, iki elastik havuz oluşturur ve bir veritabanını bir elastik havuzdan başka bir elastik havuza taşır, sonra da veritabanını elastik havuzun dışındaki tek bir veritabanı işlem boyutuna taşır.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]
[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]
[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

PowerShell'i yerel olarak yüklemeyi ve kullanmayı seçerseniz, bu öğretici AZ PowerShell 1.4.0 veya daha sonra gerektirir. Yükseltmeniz gerekirse, bkz. [Azure PowerShell modülünü yükleme](/powershell/azure/install-az-ps). PowerShell'i yerel olarak çalıştırıyorsanız Azure bağlantısı oluşturmak için `Connect-AzAccount` komutunu da çalıştırmanız gerekir.

## <a name="sample-script"></a>Örnek betik

[!code-powershell-interactive[main](../../../powershell_scripts/sql-database/move-database-between-pools-and-standalone/move-database-between-pools-and-standalone.ps1?highlight=18-19 "Move database between pools")]

## <a name="clean-up-deployment"></a>Dağıtımı temizleme

Kaynak grubunu ve onunla ilişkili tüm kaynakları kaldırmak için aşağıdaki komutu kullanın.

```powershell
Remove-AzResourceGroup -ResourceGroupName $resourcegroupname
```

## <a name="script-explanation"></a>Betik açıklaması

Bu betik aşağıdaki komutları kullanır. Tablodaki her komut, komuta özgü belgelere yönlendirir.

| Komut | Notlar |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Tüm kaynakların depolandığı bir kaynak grubu oluşturur. |
| [Yeni-AzSqlServer](/powershell/module/az.sql/new-azsqlserver) | Tek bir veritabanı veya elastik bir havuz barındıran bir SQL Veritabanı sunucusu oluşturur. |
| [Yeni-Azsqlelasticpool](/powershell/module/az.sql/new-azsqlelasticpool) | Elastik bir havuz oluşturur. |
| [Yeni-AzSqlVeritabanı](/powershell/module/az.sql/new-azsqldatabase) | SQL Veritabanı sunucusunda bağımsız veya havuzlu bir veritabanı olarak bir veritabanı oluşturur. |
| [Set-AzSqlVeritabanı](/powershell/module/az.sql/set-azsqldatabase) | Veritabanı özelliklerini güncelleştirir veya bir veritabanını elastik havuzun içine veya dışına ya da elastik havuzlar arasında taşır. |
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Bir kaynak grubunu tüm iç içe geçmiş kaynaklar dahil siler. |
|||

## <a name="next-steps"></a>Sonraki adımlar

Azure PowerShell hakkında daha fazla bilgi için bkz. [Azure PowerShell belgeleri](/powershell/azure/overview).

Ek SQL Veritabanı PowerShell betiği örnekleri [Azure SQL Veritabanı PowerShell betikleri](../sql-database-powershell-samples.md) içinde bulunabilir.
