---
title: Azure Veri Fabrikası 'nı kullanarak Concur'dan veri kopyalama (Önizleme)
description: Azure Veri Fabrikası ardışık bir ardışık ardışık ardışık ardışık bir kopya etkinliği kullanarak Concur'dan desteklenen lavabo veri depolarına verileri nasıl kopyalayatılayış edin.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 08/01/2019
ms.author: jingwang
ms.openlocfilehash: e2d9f730852503852b58a22529ba67a848e81d75
ms.sourcegitcommit: a53fe6e9e4a4c153e9ac1a93e9335f8cf762c604
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/09/2020
ms.locfileid: "80991544"
---
# <a name="copy-data-from-concur-using-azure-data-factory-preview"></a>Azure Veri Fabrikası 'nı kullanarak Concur'dan veri kopyalama (Önizleme)

Bu makalede, Concur'daki verileri kopyalamak için Azure Veri Fabrikası'ndaki Kopyalama Etkinliği'nin nasıl kullanılacağı açıklanmaktadır. Kopyalama etkinliğine genel bir genel bakış sunan [kopyalama etkinliğine genel bakış](copy-activity-overview.md) makalesi üzerine inşa edin.

> [!IMPORTANT]
> Bu bağlayıcı şu anda önizlemede. Bunu deneyebilir ve bize geribildirim verebilirsiniz. Çözümünüzde bir önizleme bağlayıcısı bağımlılığı olmasını istiyorsanız lütfen [Azure desteğine](https://azure.microsoft.com/support/) başvurun.

## <a name="supported-capabilities"></a>Desteklenen yetenekler

Bu Concur bağlayıcısı aşağıdaki etkinlikler için desteklenir:

- [Desteklenen kaynak/lavabo matrisi](copy-activity-overview.md) ile [etkinliği](copy-activity-overview.md) kopyalama
- [Arama etkinliği](control-flow-lookup-activity.md)

Concur'daki verileri desteklenen herhangi bir lavabo veri deposuna kopyalayabilirsiniz. Kopyalama etkinliği tarafından kaynak/lavabo olarak desteklenen veri depolarının listesi için [Desteklenen veri depoları](copy-activity-overview.md#supported-data-stores-and-formats) tablosuna bakın.

Azure Veri Fabrikası, bağlantıyı etkinleştirmek için yerleşik bir sürücü sağlar, bu nedenle bu bağlayıcıyı kullanarak herhangi bir sürücüyü el ile yüklemeniz gerekmez.

> [!NOTE]
> İş ortağı hesabı şu anda desteklenmiyor.

## <a name="getting-started"></a>Başlarken

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Aşağıdaki bölümlerde, Concur bağlayıcısına özgü Veri Fabrikası varlıklarını tanımlamak için kullanılan özellikler hakkında ayrıntılar sağlanmaktadır.

## <a name="linked-service-properties"></a>Bağlantılı hizmet özellikleri

Aşağıdaki özellikler Concur bağlantılı hizmet için desteklenir:

| Özellik | Açıklama | Gerekli |
|:--- |:--- |:--- |
| type | Tür özelliği ayarlanmalıdır: **Concur** | Evet |
| clientId | Uygulama client_id Concur App Management tarafından sağlanmaktadır.  | Evet |
| kullanıcı adı | Concur Hizmetine erişmek için kullandığınız kullanıcı adı.  | Evet |
| password | Kullanıcı adı alanında sağladığınız kullanıcı adına karşılık gelen parola. Bu alanı, Veri Fabrikası'nda güvenli bir şekilde depolamak için SecureString olarak işaretleyin veya [Azure Key Vault'ta depolanan bir gizliye başvurun.](store-credentials-in-key-vault.md) | Evet |
| useEncryptedEndpoints | Veri kaynağı uç noktalarının HTTPS kullanılarak şifrelenip şifrelenmediğini belirtir. Varsayılan değer true şeklindedir.  | Hayır |
| useHostVerification | TLS üzerinden bağlanırken sunucunun sertifikasındaki ana bilgisayar adının sunucunun ana bilgisayar adıile eşleşip eşleşmeyeceğini belirtir. Varsayılan değer true şeklindedir.  | Hayır |
| usePeerVerification | TLS üzerinden bağlanırken sunucunun kimliğinin doğrulanıp doğrulamayacağı belirtilir. Varsayılan değer true şeklindedir.  | Hayır |

**Örnek:**

```json
{
    "name": "ConcurLinkedService",
    "properties": {
        "type": "Concur",
        "typeProperties": {
            "clientId" : "<clientId>",
            "username" : "<username>",
            "password": {
                "type": "SecureString",
                "value": "<password>"
            }
        }
    }
}
```

## <a name="dataset-properties"></a>Veri kümesi özellikleri

Veri kümelerini tanımlamak için kullanılabilen bölümlerin ve özelliklerin tam listesi için [veri kümeleri](concepts-datasets-linked-services.md) makalesine bakın. Bu bölümde, Concur dataset tarafından desteklenen özelliklerin bir listesini sağlar.

Concur'dan veri kopyalamak için, veri kümesinin tür özelliğini **ConcurObject**olarak ayarlayın. Bu tür veri kümesinde türüne özgü ek özellik yoktur. Aşağıdaki özellikler desteklenir:

| Özellik | Açıklama | Gerekli |
|:--- |:--- |:--- |
| type | Veri kümesinin tür özelliği şu şekilde ayarlanmalıdır: **ConcurObject** | Evet |
| tableName | Masanın adı. | Hayır (etkinlik kaynağında "sorgu" belirtilirse) |


**Örnek**

```json
{
    "name": "ConcurDataset",
    "properties": {
        "type": "ConcurObject",
        "typeProperties": {},
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<Concur linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Kopyalama etkinliğinin özellikleri

Etkinlikleri tanımlamak için kullanılabilen bölümlerin ve özelliklerin tam listesi [için, Pipelines](concepts-pipelines-activities.md) makalesine bakın. Bu bölümde, Concur kaynağı tarafından desteklenen özelliklerin bir listesini sağlar.

### <a name="concursource-as-source"></a>Kaynak olarak ConcurSource

Concur'daki verileri kopyalamak için, kopyalama etkinliğindeki kaynak türünü **ConcurSource**olarak ayarlayın. Aşağıdaki özellikler kopyalama etkinliği **kaynak** bölümünde desteklenir:

| Özellik | Açıklama | Gerekli |
|:--- |:--- |:--- |
| type | Kopyalama etkinlik kaynağının türü özelliği şu şekilde ayarlanmalıdır: **ConcurSource** | Evet |
| sorgu | Verileri okumak için özel SQL sorgusunu kullanın. Örneğin: `"SELECT * FROM Opportunities where Id = xxx "`. | Hayır (veri kümesinde "tablo Adı" belirtilirse) |

**Örnek:**

```json
"activities":[
    {
        "name": "CopyFromConcur",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Concur input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "ConcurSource",
                "query": "SELECT * FROM Opportunities where Id = xxx"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="lookup-activity-properties"></a>Arama etkinlik özellikleri

Özellikler hakkında daha fazla bilgi edinmek için [Arama etkinliğini](control-flow-lookup-activity.md)kontrol edin.

## <a name="next-steps"></a>Sonraki adımlar
Azure Veri Fabrikası'ndaki kopyalama etkinliği tarafından kaynak ve lavabo olarak desteklenen veri depolarının listesi için [desteklenen veri depolarına](copy-activity-overview.md#supported-data-stores-and-formats)bakın.
