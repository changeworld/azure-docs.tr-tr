---
title: 'Quickstart: Çoklu kapsayıcı uygulaması oluşturma'
description: İlk çok kapsayıcılı uygulamanızı dağıtarak Azure Uygulama Hizmeti'ndeki çok kapsayıcılı uygulamalarla başlayın.
keywords: azure uygulama hizmeti, web uygulaması, linux, docker, beste, multicontainer, multi-container, konteynerler için web uygulaması, birden fazla konteyner, konteyner, wordpress, mysql için azure db, konteyner ile üretim veritabanı
author: msangapu-msft
ms.topic: quickstart
ms.date: 08/23/2019
ms.author: msangapu
ms.custom: mvc, seodec18
ms.openlocfilehash: 62e34859775cb8c574d8d463f636ed81dce8ece3
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/26/2020
ms.locfileid: "80045863"
---
# <a name="create-a-multi-container-preview-app-using-a-docker-compose-configuration"></a>Docker Compose yapılandırması kullanarak çok kapsayıcılı (önizleme) uygulama oluşturma

> [!NOTE]
> Çoklu kapsayıcı önizlemede.

[Kapsayıcılar için Web App](app-service-linux-intro.md), Docker görüntülerini esnek bir şekilde kullanmanızı sağlar. Bu hızlı başlangıç, Docker Compose yapılandırmasını kullanarak [Bulut Kabuğundaki](https://docs.microsoft.com/azure/cloud-shell/overview) Kapsayıcılar için Web Uygulamasına çok kapsayıcılı bir uygulamanın (önizleme) nasıl dağıtılanın caiz olduğunu gösterir.

Bu hızlı başlangıcı Cloud Shell'de tamamlayacaksınız ama bu komutları [Azure CLI](/cli/azure/install-azure-cli) (2.0.32 veya üzeri) ile yerel olarak da çalıştırabilirsiniz. 

![Kapsayıcılar için Web App üzerinde örnek çok kapsayıcılı uygulama][1]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="download-the-sample"></a>Örneği indirme

Bu hızlı başlangıç için [Docker](https://docs.docker.com/compose/wordpress/#define-the-project) Compose dosyasını kullanacaksınız. Yapılandırma dosyasına [Azure Örnekleri](https://github.com/Azure-Samples/multicontainerwordpress) sayfasından ulaşabilirsiniz.

[!code-yml[Main](../../../azure-app-service-multi-container/docker-compose-wordpress.yml)]

Cloud Shell'de bir quickstart dizini oluşturun ve o dizine geçin.

```bash
mkdir quickstart

cd $HOME/quickstart
```

Ardından, örnek uygulama deposunu quickstart dizininize kopyalamak için aşağıdaki komutu çalıştırın. Ardından `multicontainerwordpress` dizinine geçin.

```bash
git clone https://github.com/Azure-Samples/multicontainerwordpress

cd multicontainerwordpress
```

## <a name="create-a-resource-group"></a>Kaynak grubu oluşturma

[!INCLUDE [resource group intro text](../../../includes/resource-group.md)]

Bulut Kabuğu'nda, komutu [`az group create`](/cli/azure/group?view=azure-cli-latest#az-group-create) olan bir kaynak grubu oluşturun. Aşağıdaki örnek *Orta Güney ABD* konumunda *myResourceGroup* adlı bir kaynak grubu oluşturur. **Standart** katmanda Linux üzerinde App Service için desteklenen tüm konumları görüntülemek için [`az appservice list-locations --sku S1 --linux-workers-enabled`](/cli/azure/appservice?view=azure-cli-latest#az-appservice-list-locations) komutunu çalıştırın.

```azurecli-interactive
az group create --name myResourceGroup --location "South Central US"
```

Genellikle kaynak grubunuzu ve kaynakları kendinize yakın bir bölgede oluşturursunuz.

Komut tamamlandığında, bir JSON çıkışı size kaynak grubu özelliklerini gösterir.

## <a name="create-an-azure-app-service-plan"></a>Azure App Service planı oluşturma

Bulut Kabuğu'nda, kaynak grubunda [`az appservice plan create`](/cli/azure/appservice/plan?view=azure-cli-latest#az-appservice-plan-create) komutla birlikte bir Uygulama Hizmeti planı oluşturun.

Aşağıdaki örnek, **Standart** fiyatlandırma katmanı (`--sku S1`) ve bir Linux kapsayıcısı (`--is-linux`) içinde `myAppServicePlan` adlı bir App Service planı oluşturur.

```azurecli-interactive
az appservice plan create --name myAppServicePlan --resource-group myResourceGroup --sku S1 --is-linux
```

App Service planı oluşturulduğunda Azure CLI, aşağıdaki örneğe benzer bilgiler gösterir:

```json
{
  "adminSiteName": null,
  "appServicePlanName": "myAppServicePlan",
  "geoRegion": "South Central US",
  "hostingEnvironmentProfile": null,
  "id": "/subscriptions/0000-0000/resourceGroups/myResourceGroup/providers/Microsoft.Web/serverfarms/myAppServicePlan",
  "kind": "linux",
  "location": "South Central US",
  "maximumNumberOfWorkers": 1,
  "name": "myAppServicePlan",
  < JSON data removed for brevity. >
  "targetWorkerSizeId": 0,
  "type": "Microsoft.Web/serverfarms",
  "workerTierName": null
}
```

## <a name="create-a-docker-compose-app"></a>Docker Compose uygulaması oluşturma

> [!NOTE]
> Azure Uygulama Hizmetleri'ndeki Docker Compose şu anda 4.000 karakter sınırına sahiptir.

Cloud Shell terminalinde [az webapp create](/cli/azure/webapp?view=azure-cli-latest#az-webapp-create) komutunu kullanarak `myAppServicePlan` App Service planında çok kapsayıcılı bir [web uygulaması](app-service-linux-intro.md) oluşturun. _ \<app_name>'ı_ benzersiz bir uygulama adı ile değiştirmeyi `a-z`unutmayın `0-9`(geçerli karakterler , , ve `-`).

```azurecli
az webapp create --resource-group myResourceGroup --plan myAppServicePlan --name <app_name> --multicontainer-config-type compose --multicontainer-config-file compose-wordpress.yml
```

Web uygulaması oluşturulduğunda Azure CLI aşağıda yer alan çıktıdaki gibi bilgiler gösterir:

```json
{
  "additionalProperties": {},
  "availabilityState": "Normal",
  "clientAffinityEnabled": true,
  "clientCertEnabled": false,
  "cloningInfo": null,
  "containerSize": 0,
  "dailyMemoryTimeQuota": 0,
  "defaultHostName": "<app_name>.azurewebsites.net",
  "enabled": true,
  < JSON data removed for brevity. >
}
```

### <a name="browse-to-the-app"></a>Uygulamaya göz atma

Dağıtılan uygulamaya göz atmak için (`http://<app_name>.azurewebsites.net`) adresine gidin. Uygulamanın yüklenmesi birkaç dakika sürebilir. Bir hatayla karşılaşırsanız birkaç dakika daha bekleyip tarayıcıyı yenileyin.

![Kapsayıcılar için Web App üzerinde örnek çok kapsayıcılı uygulama][1]

**Tebrikler,** Kapsayıcılar için Web Uygulaması'nda çok kapsayıcılı bir uygulama oluşturdunuz.

[!INCLUDE [Clean-up section](../../../includes/cli-script-clean-up.md)]

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [Öğretici: Multi-konteyner WordPress uygulaması](tutorial-multi-container-app.md)

> [!div class="nextstepaction"]
> [Özel bir kapsayıcıyı yapılandırma](configure-custom-container.md)

<!--Image references-->
[1]: ./media/tutorial-multi-container-app/azure-multi-container-wordpress-install.png
