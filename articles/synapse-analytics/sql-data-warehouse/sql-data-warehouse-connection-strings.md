---
title: Bağlantı dizeleri
description: Synapse SQL havuzu için bağlantı dizeleri
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/17/2018
ms.author: xiaoyul
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: aad65c112bfc5a6a80f481fe3748e0f4d5c52f74
ms.sourcegitcommit: bd5fee5c56f2cbe74aa8569a1a5bce12a3b3efa6
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2020
ms.locfileid: "80745277"
---
# <a name="connection-strings-for-synapse-sql-pool"></a>Synapse SQL havuzu için bağlantı dizeleri

[ADO.NET,](/dotnet/framework/data/adonet?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) [ODBC,](/sql/connect/odbc/windows/microsoft-odbc-driver-for-sql-server-on-windows?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) [PHP](/sql/connect/php/overview-of-the-php-sql-driver?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)ve [JDBC](/sql/connect/jdbc/microsoft-jdbc-driver-for-sql-server?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)gibi birkaç farklı uygulama protokolü ile SQL havuzuna bağlanabilirsiniz. Aşağıda her protokol için bağlantı dizeleri bazı örnekler verilmiştir.  Bağlantı dizenizi oluşturmak için Azure portalını da kullanabilirsiniz.  

Azure portalını kullanarak bağlantı dizenizi oluşturmak *için, Temel Bilgiler* altında *veritabanı bağlantı dizelerini göster'i*tıklatın.

## <a name="sample-adonet-connection-string"></a>Örnek ADO.NET bağlantı dizesi

```csharp
Server=tcp:{your_server}.database.windows.net,1433;Database={your_database};User ID={your_user_name};Password={your_password_here};Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;
```

## <a name="sample-odbc-connection-string"></a>Örnek ODBC bağlantı dizesi

```csharp
Driver={SQL Server Native Client 11.0};Server=tcp:{your_server}.database.windows.net,1433;Database={your_database};Uid={your_user_name};Pwd={your_password_here};Encrypt=yes;TrustServerCertificate=no;Connection Timeout=30;
```

## <a name="sample-php-connection-string"></a>Örnek PHP bağlantı dizesi

```PHP
Server: {your_server}.database.windows.net,1433 \r\nSQL Database: {your_database}\r\nUser Name: {your_user_name}\r\n\r\nPHP Data Objects(PDO) Sample Code:\r\n\r\ntry {\r\n   $conn = new PDO ( \"sqlsrv:server = tcp:{your_server}.database.windows.net,1433; Database = {your_database}\", \"{your_user_name}\", \"{your_password_here}\");\r\n    $conn->setAttribute( PDO::ATTR_ERRMODE, PDO::ERRMODE_EXCEPTION );\r\n}\r\ncatch ( PDOException $e ) {\r\n   print( \"Error connecting to SQL Server.\" );\r\n   die(print_r($e));\r\n}\r\n\rSQL Server Extension Sample Code:\r\n\r\n$connectionInfo = array(\"UID\" => \"{your_user_name}\", \"pwd\" => \"{your_password_here}\", \"Database\" => \"{your_database}\", \"LoginTimeout\" => 30, \"Encrypt\" => 1, \"TrustServerCertificate\" => 0);\r\n$serverName = \"tcp:{your_server}.database.windows.net,1433\";\r\n$conn = sqlsrv_connect($serverName, $connectionInfo);
```

## <a name="sample-jdbc-connection-string"></a>Örnek JDBC bağlantı dizesi

```Java
jdbc:sqlserver://yourserver.database.windows.net:1433;database=yourdatabase;user={your_user_name};password={your_password_here};encrypt=true;trustServerCertificate=false;hostNameInCertificate=*.database.windows.net;loginTimeout=30;
```

> [!NOTE]
> Bağlantının kısa süreler kullanılamaz durumda kalması için bağlantı zaman süresini 300 saniyeye ayarlamayı düşünün.

## <a name="next-steps"></a>Sonraki adımlar

SQL havuzunuzu Visual Studio ve diğer uygulamalarla sorgulamaya başlamak için [Visual Studio ile Sorgula'ya](sql-data-warehouse-query-visual-studio.md)bakın.
