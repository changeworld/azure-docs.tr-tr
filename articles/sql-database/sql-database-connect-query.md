---
title: Bağlan ve Sorgula hızlı başla
description: Azure SQL veritabanına nasıl bağlanacağınızı ve bu veritabanını nasıl sorgulayacağınızı gösteren Azure SQL Veritabanı hızlı başlangıçları.
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: ''
ms.devlang: ''
ms.topic: quickstart
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 12/18/2018
ms.openlocfilehash: 05ba54c10cc693f81115f364d7a400e2275a3875
ms.sourcegitcommit: c5661c5cab5f6f13b19ce5203ac2159883b30c0e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/01/2020
ms.locfileid: "80528225"
---
# <a name="quickstarts-azure-sql-database-connect-and-query"></a>Hızlı başlatmalar: Azure SQL Veritabanı bağlantı ve sorgu

Aşağıdaki tabloda, Azure SQL veritabanına nasıl bağlanılacağını ve Azure SQL veritabanının nasıl sorgulanacağını gösteren Azure örneklerinin bağlantıları yer almaktadır. Ayrıca Taşıma Düzeyi Güvenliği için bazı öneriler sunar.

## <a name="quickstarts"></a>Hızlı Başlangıçlar

| |  |
|---|---|
|[SQL Server Management Studio](sql-database-connect-query-ssms.md)|Bu hızlı başlangıçta SSMS kullanarak bir Azure SQL veritabanına bağlanma ve daha sonra Transact-SQL deyimlerini kullanarak veritabanındaki verileri sorgulama, ekleme, güncelleştirme ve silme işlemlerinin nasıl yapılacağı açıklanır.|
|[Azure Data Studio](https://docs.microsoft.com/sql/azure-data-studio/quickstart-sql-database?toc=/azure/sql-database/toc.json)|Bu hızlı başlangıçta Azure Data Studio'yu kullanarak bir Azure SQL veritabanına bağlanma, sonra Transact-SQL (T-SQL) deyimlerini kullanarak Azure Data Studio öğreticilerinde kullanılan TutorialDB'yi oluşturma gösterilmektedir.|
|[Azure portalı](sql-database-connect-query-portal.md)|Bu hızlı başlangıçta Sorgu düzenleyicisini kullanarak bir SQL veritabanına bağlanma ve daha sonra Transact-SQL deyimlerini kullanarak veritabanındaki verileri sorgulama, ekleme, güncelleştirme ve silme işlemlerinin nasıl yapılacağı açıklanır.|
|[Visual Studio Code](sql-database-connect-query-vscode.md)|Bu hızlı başlangıçta Visual Studio Code’u kullanarak bir Azure SQL veritabanına bağlanma ve daha sonra Transact-SQL deyimlerini kullanarak veritabanındaki verileri sorgulama, ekleme, güncelleştirme ve silme işlemlerinin nasıl yapılacağı açıklanır.|
|[Visual Studio ile .NET](sql-database-connect-query-dotnet-visual-studio.md)|Bu hızlı başlangıçta, .NET framework kullanarak Visual Studio ile Azure SQL veritabanına bağlanan ve Transact-SQL deyimleriyle veri sorgulayan bir C# programı oluşturma işleminin nasıl yapılacağı açıklanır.|
|[.NET çekirdeği](sql-database-connect-query-dotnet-core.md)|Bu hızlı başlangıçta, Windows/Linux/macOS’ta .NET Core kullanarak Azure SQL veritabanına bağlanan ve Transact-SQL deyimleriyle veri sorgulayan bir C# programı oluşturma işleminin nasıl yapılacağı açıklanır.|
|[Git](sql-database-connect-query-go.md)|Bu hızlı başlangıçta Go kullanarak Azure SQL veritabanına nasıl bağlanacağınız gösterilmiştir. Verileri sorgulamak ve değiştirmek için Transact-SQL bildirimleri de gösterilir.|
|[Java](sql-database-connect-query-java.md)|Bu hızlı başlangıçta Java kullanarak bir Azure SQL veritabanına bağlanma ve Transact-SQL deyimleriyle veri sorgulama işlemleri gösterilir.|
|[Node.js](sql-database-connect-query-nodejs.md)|Bu hızlı başlangıçta, Node.js kullanarak Azure SQL veritabanına bağlanan ve Transact-SQL deyimleriyle veri sorgulayan bir program oluşturma işleminin nasıl yapılacağı açıklanır.|
|[PHP](sql-database-connect-query-php.md)|Bu hızlı başlangıçta, PHP kullanarak Azure SQL veritabanına bağlanan ve Transact-SQL deyimleriyle veri sorgulayan bir program oluşturma işleminin nasıl yapılacağı açıklanır.|
|[Python](sql-database-connect-query-python.md)|Bu hızlı başlangıçta Python kullanarak bir Azure SQL veritabanına bağlanma ve Transact-SQL deyimleriyle veri sorgulama işlemleri gösterilir. |
|[Ruby](sql-database-connect-query-ruby.md)|Bu hızlı başlangıçta, Ruby kullanarak Azure SQL veritabanına bağlanan ve Transact-SQL deyimleriyle veri sorgulayan bir program oluşturma işleminin nasıl yapılacağı açıklanır.|
|[R](sql-database-connect-query-r.md)|Bu hızlı başlangıç, Azure SQL veritabanına bağlanmak ve verileri sorgulamak için Transact-SQL deyimlerini kullanmak için bir program oluşturmak için Azure SQL Veritabanı Makine Öğrenme Hizmetleri ile R'nin nasıl kullanılacağını gösterir.|
|||

## <a name="tls-considerations-for-sql-database-connectivity"></a>SQL Veritabanı bağlanabilirliği için TLS konuları
Taşıma Katmanı Güvenliği (TLS), Microsoft’un Azure SQL Veritabanına bağlanmak için tedarik ettiği veya desteklediği tüm sürücüler tarafından kullanılır. Özel yapılandırma gerekli değildir. SQL Server veya Azure SQL Veritabanına yönelik tüm bağlantılar için tüm uygulamaların aşağıdaki yapılandırmalar veya eşdeğerleriyle ayarlanmasını öneririz:

 - **Encrypt = On**
 - **TrustServerCertificate = Off**

Bazı sistemler bu yapılandırma anahtar sözcükleri için farklı ancak eşdeğer anahtar sözcükler kullanmaktadır. Bu yapılandırmalar istemci sürücüsünün sunucudan alınan TLS sertifikası kimliğini doğrulamasını sağlamaktadır.

Ayrıca Ödeme Kartı Endüstrisi - Veri Güvenliği Standardı’na (PCI-DSS) uymanız gerekiyorsa istemcide TLS 1.1 ve 1.0’ı devre dışı bırakmanızı öneririz.

Microsoft olmayan sürücüler varsayılan olarak TLS’yi kullanmayabilir. Bu, Azure SQL Veritabanına bağlanırken bir faktör olabilir. Ekli sürücüleri olan uygulamalar bu bağlantı ayarlarını denetlemenize izin vermeyebilir. Hassas verilerle etkileşimde bulunan sistemlerde kullanmadan önce bu tarz sürücülerin ve uygulamaların güvenliğini incelemenizi öneririz.

## <a name="libraries"></a>Kitaplıklar

Azure SQL Veritabanı'na bağlanmak için çeşitli kitaplıkları ve çerçeveleri kullanabilirsiniz. C#, Java, Node.js, PHP ve Python gibi programlama dillerine hızla başlamak için [eğitimlerimize başlayın.](https://aka.ms/sqldev) Ardından, Linux'ta SQL Server veya macOS'ta Windows veya Docker'ı kullanarak bir uygulama oluşturun.

Aşağıdaki tabloda, istemci uygulamalarının şirket içinde veya bulutta çalışan SQL Server'a bağlanmak ve kullanmak için çeşitli dillerden kullanabileceği bağlantı kitaplıkları veya *sürücüleri* listelemektedir. Bunları Linux, Windows veya Docker'da kullanabilir ve Azure SQL Veritabanı ve Azure SQL Veri Ambarı'na bağlanmak için kullanabilirsiniz. 

| Dil | Platform | Ek kaynaklar | İndirme | başlarken |
| :-- | :-- | :-- | :-- | :-- |
| C# | Windows, Linux, macOS | [SQL Server için Microsoft ADO.NET](https://docs.microsoft.com/sql/connect/ado-net/microsoft-ado-net-sql-server) | [İndir](https://www.microsoft.com/net/download/) | [başlarken](https://www.microsoft.com/sql-server/developer-get-started/csharp/ubuntu)
| Java | Windows, Linux, macOS | [SQL Server için Microsoft JDBC sürücüsü](https://msdn.microsoft.com/library/mt484311.aspx) | [İndir](https://go.microsoft.com/fwlink/?linkid=852460) |  [başlarken](https://www.microsoft.com/sql-server/developer-get-started/java/ubuntu)
| PHP | Windows, Linux, macOS| [SQL Server için PHP SQL sürücüsü](https://docs.microsoft.com/sql/connect/php/microsoft-php-driver-for-sql-server) | [İndir](https://docs.microsoft.com/sql/connect/php/download-drivers-php-sql-server) | [başlarken](https://www.microsoft.com/sql-server/developer-get-started/php/ubuntu/)
| Node.js | Windows, Linux, macOS | [SQL Server için Node.js sürücüsü](https://msdn.microsoft.com/library/mt652093.aspx) | [Yükle](https://msdn.microsoft.com/library/mt652094.aspx) |  [başlarken](https://www.microsoft.com/sql-server/developer-get-started/node/ubuntu)
| Python | Windows, Linux, macOS | [Python SQL sürücüsü](https://msdn.microsoft.com/library/mt652092.aspx) | Yükleme seçenekleri: <br/> \*[pymssql](https://msdn.microsoft.com/library/mt694094.aspx) <br/> \*[pyodbc](https://msdn.microsoft.com/library/mt763257.aspx) |  [başlarken](https://www.microsoft.com/sql-server/developer-get-started/python/ubuntu)
| Ruby | Windows, Linux, macOS | [SQL Server için Ruby sürücüsü](https://msdn.microsoft.com/library/mt691981.aspx) | [Yükle](https://msdn.microsoft.com/library/mt711041.aspx) | [başlarken](https://www.microsoft.com/sql-server/developer-get-started/ruby/ubuntu)
| C++ | Windows, Linux, macOS | [SQL Server için Microsoft ODBC sürücüsü](https://msdn.microsoft.com/library/mt654048(v=sql.1).aspx) | [İndir](https://msdn.microsoft.com/library/mt654048(v=sql.1).aspx) |  

Aşağıdaki tabloda, istemci uygulamalarının şirket içinde veya bulutta çalışan SQL Server ile kullanabileceği nesne ilişkisisel eşleme (ORM) çerçeveleri ve web çerçeveleri örnekleri listelemektedir. Linux, Windows veya Docker'daki çerçeveleri kullanabilir ve bunları SQL Veritabanı ve SQL Veri Ambarı'na bağlanmak için kullanabilirsiniz. 

| Dil | Platform | ORM(lar) |
| :-- | :-- | :-- |
| C# | Windows, Linux, macOS | [Entity Framework](https://docs.microsoft.com/ef)<br>[Entity Framework Core](https://docs.microsoft.com/ef/core/index) |
| Java | Windows, Linux, macOS |[Hazırda bekletme ORM](https://hibernate.org/orm)|
| PHP | Windows, Linux, macOS | [Laravel (Belagatli)](https://laravel.com/docs/eloquent)<br>[Doktrin](https://www.doctrine-project.org/projects/orm.html) |
| Node.js | Windows, Linux, macOS | [Devamı ORM](https://sequelize.org/) |
| Python | Windows, Linux, macOS |[Django](https://www.djangoproject.com/) |
| Ruby | Windows, Linux, macOS | [Raylı Yakut](https://rubyonrails.org/) |
||||

## <a name="next-steps"></a>Sonraki adımlar

- Bağlanabilirlik mimarisi bilgileri için bkz. [Azure SQL Veritabanı Bağlanabilirlik Mimarisi](sql-database-connectivity-architecture.md)
- İstemci uygulamalarından bağlanmak için kullanılan [SQL Server sürücülerini](https://msdn.microsoft.com/library/mt654049.aspx) bulma
- SQL Veritabanına Bağlan:
  - [.NET (C#) kullanarak SQL Veritabanı’na bağlanma](sql-database-connect-query-dotnet.md) 
  - [PHP kullanarak SQL Veritabanı’na bağlanma](sql-database-connect-query-php.md) 
  - [Node.js kullanarak SQL Veritabanı’na bağlanma](sql-database-connect-query-nodejs.md) 
  - [Java kullanarak SQL Veritabanı’na bağlanma](sql-database-connect-query-java.md) 
  - [Python kullanarak SQL Veritabanı’na bağlanma](sql-database-connect-query-python.md)
  - [Ruby kullanarak SQL Veritabanı’na bağlanma](sql-database-connect-query-ruby.md)
- Mantık kodu örneklerini yeniden deneyin:
  - [ADO.NET ile SQL'e esnek bir şekilde bağlanın][step-4-connect-resiliently-to-sql-with-ado-net-a78n]
  - [PHP ile SQL'e esnek bir şekilde bağlanın][step-4-connect-resiliently-to-sql-with-php-p42h]

<!-- Link references. -->

[step-4-connect-resiliently-to-sql-with-ado-net-a78n]: https://docs.microsoft.com/sql/connect/ado-net/step-4-connect-resiliently-sql-ado-net

[step-4-connect-resiliently-to-sql-with-php-p42h]: https://docs.microsoft.com/sql/connect/php/step-4-connect-resiliently-to-sql-with-php
