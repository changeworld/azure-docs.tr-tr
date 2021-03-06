---
title: Python ile bağlanın - PostgreSQL için Azure Veritabanı - Tek Sunucu
description: Bu hızlı başlatma, PostgreSQL - Single Server için Azure Veritabanı'ndan veri bağlamak ve sorgulamak için kullanabileceğiniz Python kod örnekleri sağlar.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.custom: mvc, devcenter
ms.devlang: python
ms.topic: quickstart
ms.date: 11/07/2019
ms.openlocfilehash: 3694c0b74393068538a0c8f496444a1541d88fee
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/26/2020
ms.locfileid: "76769050"
---
# <a name="quickstart-use-python-to-connect-and-query-data-in-azure-database-for-postgresql---single-server"></a>Quickstart: PostgreSQL için Azure Veritabanı'ndaki verileri bağlamak ve sorgulamak için Python'u kullanın - Tek Sunucu

Bu hızlı başlangıçta, macOS, Ubuntu Linux veya Windows'da Python kullanarak PostgreSQL için bir Azure Veritabanı ile çalışırsınız. Hızlı başlangıç, veritabanına nasıl bağlanılsüreceğini ve verileri sorgulamak, eklemek, güncelleştirmek ve silmek için SQL deyimlerini nasıl kullanacağımı gösterir. Makale, Python'u bildiğinizi, ancak PostgreSQL için Azure Veritabanı ile çalışmaya yeni bir şey yaptığınızı varsayar.

## <a name="prerequisites"></a>Ön koşullar

- Etkin bir aboneliği olan bir Azure hesabı. [Ücretsiz bir hesap oluşturun.](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)

- [Quickstart'ın Tamamlanması: Azure portalında veya Quickstart'ta PostgreSQL sunucusu için bir Azure Veritabanı oluşturun:](quickstart-create-server-database-portal.md) [Azure CLI'yi kullanarak PostgreSQL için bir Azure Veritabanı oluşturun.](quickstart-create-server-database-azure-cli.md)
  
- [Python](https://www.python.org/downloads/) 2.7.9+ veya 3.4+
  
- En son [pip](https://pip.pypa.io/en/stable/installing/) paket yükleyici.

## <a name="install-the-python-libraries-for-postgresql"></a>PostgreSQL için Python kitaplıklarını yükleme
[Psycopg2](https://pypi.python.org/pypi/psycopg2/) modülü, Bir PostgreSQL veritabanına bağlanmanızı ve sorgulamanızı sağlar ve Linux, macOS veya Windows [tekerlek](https://pythonwheels.com/) paketi olarak kullanılabilir. Tüm bağımlılıklar da dahil olmak üzere modülün ikili sürümünü yükleyin. Yükleme ve `psycopg2` gereksinimler hakkında daha fazla bilgi için [yükleme](http://initd.org/psycopg/docs/install.html)ye bakın. 

Yüklemek `psycopg2`için, bir terminal veya komut `pip install psycopg2`istemi açın ve komutu çalıştırın.

## <a name="get-database-connection-information"></a>Veritabanı bağlantı bilgilerini alma
PostgreSQL veritabanı için bir Azure Veritabanına bağlanmak için tam nitelikli sunucu adı ve giriş kimlik bilgileri gerekir. Bu bilgileri Azure portalından alabilirsiniz.

1. Azure [portalında,](https://portal.azure.com/)PostgreSQL sunucu adı için Azure Veritabanınızı arayın ve seçin. 
1. Sunucunun Genel **Bakış** sayfasında, tam nitelikli **Sunucu adını** ve **Yönetici kullanıcı adını**kopyalayın. Tam nitelikli **Sunucu adı** her zaman formu * \<my-server-name>.postgres.database.azure.com*ve Yönetici kullanıcı **adı** her zaman formu * \<my-admin-kullanıcı adı> @\<my-server-name>*. 
   
   Ayrıca yönetici şifrenizi de ihtiyacınız var. Unutursanız, bu sayfadan sıfırlayabilirsiniz. 
   
   ![PostgreSQL için Azure Veritabanı sunucu adı](./media/connect-python/1-connection-string.png)

## <a name="how-to-run-the-python-examples"></a>Python örnekleri nasıl çalıştırılır?

Bu makaledeki her kod örneği için:

1. Metin düzenleyicisinde yeni bir dosya oluşturun. 
   
1. Kod örneğini dosyaya ekleyin. Kodda, değiştirin:
   - `<server-name>`ve `<admin-username>` Azure portalından kopyaladığınız değerlerle.
   - `<admin-password>`sunucu şifrenizle.
   - `<database-name>`PostgreSQL veritabanı için Azure Veritabanınızın adı ile. Sunucunuzu oluşturduğunuzda, *postgres* adlı varsayılan veritabanı otomatik olarak oluşturuldu. SQL komutlarını kullanarak bu veritabanını yeniden adlandırabilir veya yeni bir veritabanı oluşturabilirsiniz. 
   
1. Dosyayı proje klasörünüze *,postgres-insert.py*gibi bir *.py* uzantısı ile kaydedin. Windows için, dosyayı kaydederken UTF-8 kodlamasının seçildiğinden emin olun. 
   
1. Dosyayı çalıştırmak için, komut satırı arabiriminde proje klasörünüzde değiştirin ve `python postgres-insert.py`örneğin dosya adını takip eden yazın. `python`

## <a name="create-a-table-and-insert-data"></a>Tablo oluşturma ve veri ekleme
Aşağıdaki kod [örneği, psycopg2.connect](http://initd.org/psycopg/docs/connection.html) işlevini kullanarak PostgreSQL veritabanıiçin Azure Veritabanınıza bağlanır ve verileri SQL **INSERT** deyimiyle yükler. [Imleç.execute](http://initd.org/psycopg/docs/cursor.html#execute) işlevi veritabanına karşı SQL sorgusu yürütür. 

```Python
import psycopg2

# Update connection string information 
host = "<server-name>"
dbname = "<database-name>"
user = "<admin-username>"
password = "<admin-password>"
sslmode = "require"

# Construct connection string
conn_string = "host={0} user={1} dbname={2} password={3} sslmode={4}".format(host, user, dbname, password, sslmode)
conn = psycopg2.connect(conn_string) 
print("Connection established")

cursor = conn.cursor()

# Drop previous table of same name if one exists
cursor.execute("DROP TABLE IF EXISTS inventory;")
print("Finished dropping table (if existed)")

# Create a table
cursor.execute("CREATE TABLE inventory (id serial PRIMARY KEY, name VARCHAR(50), quantity INTEGER);")
print("Finished creating table")

# Insert some data into the table
cursor.execute("INSERT INTO inventory (name, quantity) VALUES (%s, %s);", ("banana", 150))
cursor.execute("INSERT INTO inventory (name, quantity) VALUES (%s, %s);", ("orange", 154))
cursor.execute("INSERT INTO inventory (name, quantity) VALUES (%s, %s);", ("apple", 100))
print("Inserted 3 rows of data")

# Clean up
conn.commit()
cursor.close()
conn.close()
```

Kod başarılı bir şekilde çalıştığında, aşağıdaki çıktıyı üretir:

![Komut satırı çıkışı](media/connect-python/2-example-python-output.png)

## <a name="read-data"></a>Verileri okuma
Aşağıdaki kod örneği PostgreSQL veritabanı için Azure Veritabanınıza bağlanır ve verileri okumak için SQL **SELECT** deyimiyle [imleç.execute](http://initd.org/psycopg/docs/cursor.html#execute) kullanır. Bu işlev bir sorgu kabul eder ve [imleç.fetchall()](http://initd.org/psycopg/docs/cursor.html#cursor.fetchall)kullanarak yinelemek için ayarlanmış bir sonuç döndürür. 

```Python
import psycopg2

# Update connection string information
host = "<server-name>"
dbname = "<database-name>"
user = "<admin-username>"
password = "<admin-password>"
sslmode = "require"

# Construct connection string
conn_string = "host={0} user={1} dbname={2} password={3} sslmode={4}".format(host, user, dbname, password, sslmode)
conn = psycopg2.connect(conn_string) 
print("Connection established")

cursor = conn.cursor()

# Fetch all rows from table
cursor.execute("SELECT * FROM inventory;")
rows = cursor.fetchall()

# Print all rows
for row in rows:
    print("Data row = (%s, %s, %s)" %(str(row[0]), str(row[1]), str(row[2])))

# Cleanup
conn.commit()
cursor.close()
conn.close()
```

## <a name="update-data"></a>Verileri güncelleştirme
Aşağıdaki kod örneği PostgreSQL veritabanı için Azure Veritabanınıza bağlanır ve verileri güncelleştirmek için SQL **UPDATE** deyimiyle [imleç.execute](http://initd.org/psycopg/docs/cursor.html#execute) kullanır. 

```Python
import psycopg2

# Update connection string information
host = "<server-name>"
dbname = "<database-name>"
user = "<admin-username>"
password = "<admin-password>"
sslmode = "require"

# Construct connection string
conn_string = "host={0} user={1} dbname={2} password={3} sslmode={4}".format(host, user, dbname, password, sslmode)
conn = psycopg2.connect(conn_string) 
print("Connection established")

cursor = conn.cursor()

# Update a data row in the table
cursor.execute("UPDATE inventory SET quantity = %s WHERE name = %s;", (200, "banana"))
print("Updated 1 row of data")

# Cleanup
conn.commit()
cursor.close()
conn.close()
```

## <a name="delete-data"></a>Verileri silme
Aşağıdaki kod örneği PostgreSQL veritabanı için Azure Veritabanınıza bağlanır ve daha önce eklediğiniz bir stok öğesini silmek için [IMLEÇ.execute'i](http://initd.org/psycopg/docs/cursor.html#execute) SQL **DELETE** deyimiyle kullanır. 

```Python
import psycopg2

# Update connection string information
host = "<server-name>"
dbname = "<database-name>"
user = "<admin-username>"
password = "<admin-password>"
sslmode = "require"

# Construct connection string
conn_string = "host={0} user={1} dbname={2} password={3} sslmode={4}".format(host, user, dbname, password, sslmode)
conn = psycopg2.connect(conn_string) 
print("Connection established")

cursor = conn.cursor()

# Delete data row from table
cursor.execute("DELETE FROM inventory WHERE name = %s;", ("orange",))
print("Deleted 1 row of data")

# Cleanup
conn.commit()
cursor.close()
conn.close()
```

## <a name="next-steps"></a>Sonraki adımlar
> [!div class="nextstepaction"]
> [Dışarı Aktarma ve İçeri Aktarma seçeneğini kullanarak veritabanınızı geçirme](./howto-migrate-using-export-and-import.md)
