---
title: 'Hızlı başlangıç: Sunucu oluşturma - Azure CLI - PostgreSQL için Azure Veritabanı - Tek Sunucu'
description: Azure CLI (komut satırı arabirimi) kullanarak PostgreSQL - Single Server için bir Azure Veritabanı oluşturmak için hızlı başlangıç kılavuzu.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.devlang: azurecli
ms.topic: quickstart
ms.date: 06/25/2019
ms.custom: mvc
ms.openlocfilehash: ed78d3dd4e6fbde10c69403cc3dcff24072dc676
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/26/2020
ms.locfileid: "75358062"
---
# <a name="quickstart-create-an-azure-database-for-postgresql---single-server-using-the-azure-cli"></a>Hızlı başlangıç: Azure CLI'yi kullanarak PostgreSQL - Single Server için bir Azure Veritabanı Oluşturma

> [!TIP]
> Azure CLI komutuna [(şu](/cli/azure/ext/db-up/postgres#ext-db-up-az-postgres-up) anda önizlemede) daha basit bir gönderi kullanmayı düşünün. [Hızlı](./quickstart-create-server-up-azure-cli.md)başlatılanı deneyin.

PostgreSQL için Azure Veritabanı, bulutta son derece kullanılabilir olan PostgreSQL veritabanları çalıştırmanızı, yönetmenizi ve ölçeklendirmenizi sağlayan ve yönetilen bir hizmettir. Azure CLI, komut satırından veya betik içindeki Azure kaynaklarını oluşturmak ve yönetmek için kullanılır. Bu hızlı başlangıçta, Azure CLI aracını kullanarak bir [Azure kaynak grubunda](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) nasıl PostgreSQL için Azure Veritabanı sunucusu oluşturabileceğiniz gösterilir.

Azure aboneliğiniz yoksa, başlamadan önce [ücretsiz](https://azure.microsoft.com/free/) bir hesap oluşturun.

[!INCLUDE [cloud-shell-try-it](../../includes/cloud-shell-try-it.md)]

CLI'yi yerel olarak yükleyip kullanmayı tercih ederseniz bu makale için Azure CLI 2.0 veya sonraki bir sürümünü kullanmanız gerekir. Yüklü sürümü görmek için `az --version` komutunu çalıştırın. Yüklemeniz veya yükseltmeniz gerekirse, bkz. [Azure CLI yükleme]( /cli/azure/install-azure-cli). 

CLI'yi yerel olarak çalıştırıyorsanız, [az giriş](/cli/azure/authenticate-azure-cli?view=interactive-log-in) komutunu kullanarak hesabınızda oturum açmanız gerekir. İlgili abonelik adı için komut çıktısından **kimlik** özelliğine dikkat edin.
```azurecli-interactive
az login
```

Birden fazla aboneliğiniz varsa kaynağın faturalanacağı uygun aboneliği seçin. [az account set](/cli/azure/account) komutunu kullanarak hesabınız altındaki belirli bir abonelik kimliğini seçin. Az **giriş** çıkışındaki **kimlik** özelliğini abonelik kimliği yer tutucusuna abone olmak için değiştirin.
```azurecli-interactive
az account set --subscription <subscription id>
```

## <a name="create-a-resource-group"></a>Kaynak grubu oluşturma

[az group create](/cli/azure/group) komutunu kullanarak bir [Azure kaynak grubu](../azure-resource-manager/management/overview.md) oluşturun. Kaynak grubu, Azure kaynaklarının grup olarak dağıtıldığı ve yönetildiği bir mantıksal kapsayıcıdır. Benzersiz bir ad sağlamanız gerekir. Aşağıdaki örnek `westus` konumunda `myresourcegroup` adlı bir kaynak grubu oluşturur.
```azurecli-interactive
az group create --name myresourcegroup --location westus
```

## <a name="create-an-azure-database-for-postgresql-server"></a>PostgreSQL için Azure Veritabanı sunucusu oluşturma

[az postgres server create](/cli/azure/postgres/server) komutunu kullanarak [PostgreSQL sunucusu için Azure SQL Veritabanı ](overview.md) oluşturun. Bir sunucu birden çok veritabanı içerebilir.


**Ayar** | **Örnek değer** | **Açıklama**
---|---|---
ad | mydemoserver | Azure veritabanınızı PostgreSQL sunucusuna tanıtan benzersiz bir ad seçin. Sunucu adı yalnızca küçük harf, sayı ve kısa çizgi (-) karakterini içerebilir. 3 ila 63 karakter arası içermelidir.
resource-group | myresourcegroup | Azure kaynak grubunun adını sağlayın.
sku-name | GP_Gen5_2 | Sku'nun adı. Kısaca {fiyatlandırma katmanı}\_{işlem nesli}\_{sanal çekirdek sayısı} kuralına uyar. sku-name parametresi hakkında daha fazla bilgi için aşağıdaki tabloya bakın.
backup-retention | 7 | Yedeklemenin ne kadar süreyle tutulacağı. Birim olarak gün kullanılır. 7-35 aralığındadır. 
geo-redundant-backup | Devre dışı | Coğrafi olarak yedekli yedeklemelerin bu sunucu için etkinleştirilip etkinleştirilmeyeceği. İzin verilen değerler: Etkin, Devre Dışı.
location | westus | Sunucu için Azure konumu.
ssl-enforcement | Etkin | Bu sunucu için ssl'in etkinleştirilip etkinleştirilmeyeceği. İzin verilen değerler: Etkin, Devre Dışı.
storage-size | 51200 | Sunucunun depolama kapasitesi (birim olan megabayt kullanılır). Geçerli storage-size en az 5120 MB'tır ve 1024 MB'lık artışlarla büyür. Depolama boyutu sınırları hakkında daha fazla bilgi için [fiyatlandırma katmanları](./concepts-pricing-tiers.md) belgesine bakın. 
version | 9.6 | PostgreSQL ana sürümü.
admin-user | myadmin | Yöneticinin oturum açma kullanıcı adı. Şu değerler kullanılamaz: **azure_superuser**, **admin**, **administrator**, **root**, **guest** veya **public**.
admin-password | *güvenli parola* | Yönetici kullanıcının parolası. 8 ile 128 arasında karakter içermelidir. Parolanız şu üç kategoride yer alan karakterlerden oluşmalıdır: İngilizce büyük ve küçük harfler, sayılar ve alfasayısal olmayan karakterler.


sku-name parametresi değeri aşağıdaki örneklerde gösterildiği gibi {fiyatlandırma katmanı}\_{işlem oluşturma}\_{sanal çekirdek} kuralını kullanır:
+ `--sku-name B_Gen5_1`Temel, Gen 5 ve 1 vCore haritaları. Bu seçenek, mevcut en küçük SKU'dur.
+ `--sku-name GP_Gen5_32` Genel Amaçlı, Gen 5 ve 32 sanal çekirdekle eşleşir.
+ `--sku-name MO_Gen5_2` Bellek için iyileştirilmiş, Gen 5 ve 2 sanal çekirdekle eşleşir.

Bölgeler ve katmanlar için geçerli olan değerleri anlamak için lütfen [fiyatlandırma katmanları](./concepts-pricing-tiers.md) belgesini inceleyin.

Aşağıdaki örnekte, Batı ABD bölgesinde `myadmin` sunucu yöneticisi oturum açma adıyla `myresourcegroup` kaynak grubunuzda `mydemoserver` adlı bir PostgreSQL 9.6 sunucusu oluşturulur. Bu **2 vCores**ile Gen **4** **Genel Amaçlı** sunucu. `<server_admin_password>` değerini kendi değerinizle değiştirin.
```azurecli-interactive
az postgres server create --resource-group myresourcegroup --name mydemoserver  --location westus --admin-user myadmin --admin-password <server_admin_password> --sku-name GP_Gen4_2 --version 9.6
```

> [!NOTE]
> Hafif işlem ve G/Ç iş yükünüz için yeterliyse Temel fiyatlandırma katmanını kullanmayı düşünün. Temel fiyatlandırma katmanında oluşturulan sunucuların daha sonra Genel Amaç veya Bellek Optimize Edilemeyeceğine dikkat edin. Daha fazla bilgi için [fiyatlandırma sayfasına](https://azure.microsoft.com/pricing/details/postgresql/) bakın.
> 

## <a name="configure-a-server-level-firewall-rule"></a>Sunucu düzeyinde güvenlik duvarı kuralı oluşturma

[az postgres server firewall-rule create](/cli/azure/postgres/server/firewall-rule) komutunu kullanarak Azure PostgreSQL sunucusu düzeyinde bir güvenlik duvarı kuralı oluşturun. Sunucu düzeyindeki bir güvenlik duvarı kuralı, [psql](https://www.postgresql.org/docs/9.2/static/app-psql.html) veya [PgAdmin](https://www.pgadmin.org/) gibi bir dış uygulamanın Azure PostgreSQL hizmetinin güvenlik duvarı üzerinden sunucunuza bağlanmasına imkan tanır. 

Ağınızdan bağlanabilmek için bir IP aralığını kapsayan bir güvenlik duvarı kuralı ayarlayabilirsiniz. Aşağıdaki örnekte, [az postgres server firewall-rule create](/cli/azure/postgres/server/firewall-rule) komutu kullanılarak tek bir IP adresi için `AllowMyIP` güvenlik duvarı kuralı oluşturulur.
```azurecli-interactive
az postgres server firewall-rule create --resource-group myresourcegroup --server mydemoserver --name AllowMyIP --start-ip-address 192.168.0.1 --end-ip-address 192.168.0.1
```

> [!NOTE]
> Azure PostgreSQL sunucusu, 5432 bağlantı noktası üzerinden iletişim kurar. Kurumsal ağ içinden bağlanıyorsanız ağınızın güvenlik duvarı tarafından 5432 numaralı bağlantı noktası üzerinden giden trafiğe izin verilmiyor olabilir. BT departmanınızdan Azure PostgreSQL sunucunuza bağlanması için 5432 numaralı bağlantı noktasını açmasını isteyin.

## <a name="get-the-connection-information"></a>Bağlantı bilgilerini alma

Sunucunuza bağlanmak için ana bilgisayar bilgilerini ve erişim kimlik bilgilerini sağlamanız gerekir.
```azurecli-interactive
az postgres server show --resource-group myresourcegroup --name mydemoserver
```

Sonuç JSON biçimindedir. **administratorLogin** ve **fullyQualifiedDomainName** bilgilerini not alın.
```json
{
  "administratorLogin": "myadmin",
  "earliestRestoreDate": null,
  "fullyQualifiedDomainName": "mydemoserver.postgres.database.azure.com",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myresourcegroup/providers/Microsoft.DBforPostgreSQL/servers/mydemoserver",
  "location": "westus",
  "name": "mydemoserver",
  "resourceGroup": "myresourcegroup",
  "sku": {
    "capacity": 2,
    "family": "Gen5",
    "name": "GP_Gen5_2",
    "size": null,
    "tier": "GeneralPurpose"
  },
  "sslEnforcement": "Enabled",
  "storageProfile": {
    "backupRetentionDays": 7,
    "geoRedundantBackup": "Disabled",
    "storageMb": 5120
  },
  "tags": null,
  "type": "Microsoft.DBforPostgreSQL/servers",
  "userVisibleState": "Ready",
  "version": "9.6"
}
```

## <a name="connect-to-postgresql-database-using-psql"></a>psql’yi kullanarak PostgreSQL veritabanına bağlanma

İstemci bilgisayarınızda PostgreSQL yüklüyse, [psql](https://www.postgresql.org/docs/current/static/app-psql.html)’nin yerel bir örneğini kullanarak Azure PostgreSQL sunucusuna bağlanabilirsiniz. Şimdi psql komut satırı yardımcı programını kullanarak Azure PostgreSQL sunucusuna bağlanalım.

1. PostgreSQL için Azure Veritabanı sunucusuna bağlanmak üzere aşağıdaki psql komutunu çalıştırın
   ```bash
   psql --host=<servername> --port=<port> --username=<user@servername> --dbname=<dbname>
   ```

   Örneğin aşağıdaki komut, erişim kimlik bilgilerini kullanarak **mydemoserver.postgres.database.azure.com** PostgreSQL sunucunuzda **postgres** adlı varsayılan veritabanına bağlanır. Parola istendiğinde seçtiğiniz `<server_admin_password>` değerini girin.
  
   ```bash
   psql --host=mydemoserver.postgres.database.azure.com --port=5432 --username=myadmin@mydemoserver --dbname=postgres
   ```

   > [!TIP]
   > Postgres'e bağlanmak için bir URL yolu kullanmayı tercih ediyorsanız, URL `%40`kullanıcı adı ile @ oturum açmasını kodlar. Örneğin psql bağlantı dizesi,
   > ```
   > psql postgresql://myadmin%40mydemoserver@mydemoserver.postgres.database.azure.com:5432/postgres
   > ```


2. Sunucuya bağlandıktan sonra, istemde boş bir veritabanı oluşturun.
   ```sql
   CREATE DATABASE mypgsqldb;
   ```

3. İstemde, yeni oluşturulan veritabanı **mypgsqldb**bağlantı geçmek için aşağıdaki komutu yürütmek:
   ```sql
   \c mypgsqldb
   ```

## <a name="connect-to-the-postgresql-server-using-pgadmin"></a>pgAdmin’i kullanarak PostgreSQL Sunucusu’na bağlanma

pgAdmin, PostgreSQL ile kullanılan açık kaynak bir araçtır. pgAdmin'i [pgAdmin web sitesinden](https://www.pgadmin.org/) yükleyebilirsiniz. Kullandığınız pgAdmin sürümü bu Hızlı Başlangıçta kullanılan sürümden farklı olabilir. Ek yönergeler gerekiyorsa pgAdmin belgelerini okuyun.

1. İstemci bilgisayarınızda pgAdmin uygulamasını açın.

2. Araç çubuğundan **Nesne**’ye gidin, **Oluştur** seçeneğinin üzerine gelip **Sunucu**’yu seçin.

3. **Oluştur - Sunucu** iletişim kutusunun **Genel** sekmesinde, sunucu için **mydemoserver** gibi benzersiz ve kolay bir ad girin.

   !["Genel" sekmesi](./media/quickstart-create-server-database-azure-cli/9-pgadmin-create-server.png)

4. **Oluştur - Sunucu** iletişim kutusunun **Bağlantı** sekmesinde ayarlar tablosunu doldurun.

   !["Bağlantı" sekmesi](./media/quickstart-create-server-database-azure-cli/10-pgadmin-create-server.png)

    pgAdmin parametresi |Değer|Açıklama
    ---|---|---
    Konak adı/adresi | Sunucu adı | PostgreSQL için Azure Veritabanı sunucusunu oluştururken kullandığınız sunucu adı değeri. Örnek sunucumuz: **mydemoserver.postgres.database.azure.com.** Örnekte gösterildiği gibi tam nitelikli alan adı (**\*.postgres.database.azure.com**) kullanın. Sunucu adınızı anımsamıyorsanız bağlantı bilgilerini almak için bir önceki bölümdeki adımları izleyin. 
    Bağlantı noktası | 5432 | PostgreSQL için Azure Veritabanı sunucusuna bağlanırken kullanılacak bağlantı noktası. 
    Bakım veritabanı | *postgres* | Sistem tarafından oluşturulan varsayılan veritabanı adı.
    Kullanıcı adı | Sunucu yöneticisi oturum açma adı | PostgreSQL için Azure Veritabanı sunucusunu oluştururken girdiğiniz sunucu yöneticisi oturum açma kullanıcı adı. Kullanıcı adını anımsamıyorsanız bağlantı bilgilerini almak için bir önceki bölümdeki adımları izleyin. Biçimi *kullanıcı adı\@servername.*
    Parola | Yönetici parolanız | Bu Hızlı Başlangıçta daha önce sunucuyu oluştururken seçtiğiniz parola.
    Rol | Boş bırakın | Bu noktada bir rol adı sağlamanız gerekmez. Alanı boş bırakın.
    SSL modu | *Gerektirme* | PgAdmin'in SSL sekmesinde SSL modunu ayarlayabilirsiniz. Varsayılan olarak, PostgreSQL sunucuları için tüm Azure Veritabanı, SSL zorlama açık olarak oluşturulur. SSL'yi zorunlu tutma seçeneğini kapatmak için bkz. [SSL'yi zorunlu tutma](./concepts-ssl-connection-security.md).
    
5. **Kaydet'i**seçin.

6. Sol taraftaki **Tarayıcı** bölmesinde **Sunucular** düğümünü genişletin. Sunucunuzu (örneğin, **mydemoserver**) seçin. Bu sunucuya bağlanmak için tıklayın.

7. Sunucu düğümünü ve ardından onun altındaki **Veritabanları**’nı genişletin. Liste mevcut *postgres* veritabanınızı ve oluşturduğunuz diğer veritabanlarını içermelidir. PostgreSQL için Azure Veritabanı ile sunucu başına birden çok veritabanı oluşturabilirsiniz.

8. **Veritabanları**’na sağ tıklayın, **Oluştur** menüsünü seçin ve sonra da **Veritabanı**’nı seçin.

9. **Veritabanı** alanına, **mypgsqldb2** gibi tercih ettiğiniz veritabanı adını yazın.

10. Liste kutusundan veritabanı için **Sahip**’i seçin. Sunucu yöneticinizin oturum açma adını (örnekteki **my admin** gibi) seçin.

    ![pgadmin’de veritabanı oluşturma](./media/quickstart-create-server-database-azure-cli/11-pgadmin-database.png)

11. Yeni ve boş bir veritabanı oluşturmak için **Kaydet**’i seçin.

12. **Tarayıcı** bölmesinde, oluşturduğunuz veritabanını sunucu adınızın altındaki veritabanları listesinde görebilirsiniz.




## <a name="clean-up-resources"></a>Kaynakları temizleme

[Azure kaynak grubunu](../azure-resource-manager/management/overview.md) silerek hızlı başlangıçta oluşturduğunuz tüm kaynakları temizleyin.

> [!TIP]
> Bu koleksiyondaki diğer hızlı başlangıçlar, bu hızlı başlangıcı temel alır. Sonraki hızlı başlangıçlarla çalışmaya devam etmeyi planlıyorsanız bu hızlı başlangıçta oluşturulan kaynakları temizlemeyin. Devam etmeyi planlamıyorsanız, Azure CLI aracında bu hızlı başlangıç ile oluşturulan tüm kaynakları silmek için aşağıdaki adımları kullanın.

```azurecli-interactive
az group delete --name myresourcegroup
```

Yeni oluşturulan tek bir sunucuyu silmek istiyorsanız [az postgres server delete](/cli/azure/postgres/server) komutunu kullanabilirsiniz.
```azurecli-interactive
az postgres server delete --resource-group myresourcegroup --name mydemoserver
```

## <a name="next-steps"></a>Sonraki adımlar
> [!div class="nextstepaction"]
> [Dışarı Aktarma ve İçeri Aktarma seçeneğini kullanarak veritabanınızı geçirme](./howto-migrate-using-export-and-import.md)

