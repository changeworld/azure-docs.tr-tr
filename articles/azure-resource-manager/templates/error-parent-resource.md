---
title: Üst kaynak hataları
description: Azure Kaynak Yöneticisi şablonundaki bir üst kaynakla çalışırken hataların nasıl çözüleceğini açıklar.
ms.topic: troubleshooting
ms.date: 08/01/2018
ms.openlocfilehash: f1847389d60ddf3c6abc70bc3309940c2246084e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76154049"
---
# <a name="resolve-errors-for-parent-resources"></a>Üst kaynaklardaki hataları çözümle

Bu makalede, bir üst kaynağa bağlı bir kaynak dağıtılırken karşılaşabileceğiniz hatalar açıklanmaktadır.

## <a name="symptom"></a>Belirti

Çocuk olan bir kaynağı başka bir kaynağa dağıtırken aşağıdaki hatayı alabilirsiniz:

```
Code=ParentResourceNotFound;
Message=Can not perform requested operation on nested resource. Parent resource 'exampleserver' not found."
```

## <a name="cause"></a>Nedeni

Bir kaynak başka bir kaynağa bir alt kaynak olduğunda, alt kaynak oluşturmadan önce üst kaynak bulunması gerekir. Alt kaynağın adı, üst kaynakla bağlantıyı tanımlar. Alt kaynağın adı biçimindedir. `<parent-resource-name>/<child-resource-name>` Örneğin, bir SQL Veritabanı olarak tanımlanabilir:

```json
{
  "type": "Microsoft.Sql/servers/databases",
  "name": "[concat(variables('databaseServerName'), '/', parameters('databaseName'))]",
  ...
```

Hem sunucuyu hem de veritabanını aynı şablonda dağıtıyorsanız, ancak sunucuya bağımlılık belirtmiyorsanız, veritabanı dağıtımı sunucu dağıtılmadan önce başlayabilir.

Ana kaynak zaten varsa ve aynı şablonda dağıtılmıyorsa, Kaynak Yöneticisi alt kaynağı üst kaynakla ilişkilendiremediğinde bu hatayı alırsınız. Alt kaynak doğru biçimde olmadığında veya alt kaynak üst kaynak için kaynak grubundan farklı bir kaynak grubuna dağıtıldığında bu hata olabilir.

## <a name="solution"></a>Çözüm

Üst ve alt kaynaklar aynı şablonda dağıtıldığında bu hatayı gidermek için bir bağımlılık ekleyin.

```json
"dependsOn": [
  "[variables('databaseServerName')]"
]
```

Ana kaynak daha önce farklı bir şablonda dağıtıldığında bu hatayı gidermek için bir bağımlılık ayarlamazsınız. Bunun yerine, alt öğeyi aynı kaynak grubuna dağıtın ve üst kaynağın adını sağlayın.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "sqlServerName": {
      "type": "string"
    },
    "databaseName": {
      "type": "string"
    }
  },
  "resources": [
    {
      "type": "Microsoft.Sql/servers/databases",
      "apiVersion": "2014-04-01",
      "name": "[concat(parameters('sqlServerName'), '/', parameters('databaseName'))]",
      "location": "[resourceGroup().location]",
      "properties": {
        "collation": "SQL_Latin1_General_CP1_CI_AS",
        "edition": "Basic"
      }
    }
  ],
  "outputs": {}
}
```

Daha fazla bilgi için bkz. Azure [Kaynak Yöneticisi şablonlarında kaynakları dağıtma sırasını tanımlayın.](define-resource-dependency.md)