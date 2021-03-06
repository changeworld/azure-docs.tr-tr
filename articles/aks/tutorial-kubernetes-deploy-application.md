---
title: Azure’da Kubernetes öğreticisi - Uygulamayı dağıtma
description: Bu Azure Kubernetes Service (AKS) öğreticisinde, Azure Container Registry'de depolanan özel bir görüntüyü kullanarak kümenizde çok kapsayıcılı bir uygulama dağıtacaksınız.
services: container-service
ms.topic: tutorial
ms.date: 12/19/2018
ms.custom: mvc
ms.openlocfilehash: 3b614fcb6692f35884af2fc4e19210267ab8ab04
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/24/2020
ms.locfileid: "77593283"
---
# <a name="tutorial-run-applications-in-azure-kubernetes-service-aks"></a>Öğretici: Azure Kubernetes Hizmeti’nde (AKS) uygulamaları çalıştırma

Kubernetes, kapsayıcılı uygulamalar için dağıtılmış bir platform sunar. Kendi uygulamalarınızı ve hizmetlerinizi oluşturup bir Kubernetes kümesine dağıtırsınız, kullanılabilirlik ve bağlantı özellikleri de küme tarafından yönetilir. Yedi bölümün dördüncüsü olan bu öğreticide Kubernetes kümesine örnek bir uygulama dağıtılır. Aşağıdakileri nasıl yapacağınızı öğrenirsiniz:

> [!div class="checklist"]
> * Bir Kubernetes bildirim dosyasini güncelleştir
> * Kubernetes'te uygulama çalıştırma
> * Uygulamayı test etme

Ek öğreticilerde, bu uygulama ölçeklendirilir ve güncelleştirilir.

Bu hızlı başlangıç, Kubernetes kavramlarının temel olarak bilindiğini varsayar. Daha fazla bilgi için Azure [Kubernetes Hizmeti (AKS) için Kubernetes temel kavramlarına][kubernetes-concepts]bakın.

## <a name="before-you-begin"></a>Başlamadan önce

Önceki öğreticilerde, bir uygulama bir kapsayıcı görüntüsüne paketlendi, görüntü Azure Container Registry’ye yüklendi ve bir Kubernetes kümesi oluşturuldu.

Bu öğreticiyi tamamlamak için önceden oluşturulmuş `azure-vote-all-in-one-redis.yaml` Kubernetes bildirim dosyasına ihtiyaç duyarsınız. Bu dosya, önceki öğreticide uygulama kaynak koduyla indirildi. Repo'yu klonladığınızdan ve dizinleri klonlanmış repo'ya dönüştürttettiğinizi doğrulayın. Bu adımları yapmadıysanız ve takip etmek istiyorsanız, Tutorial 1 ile başlayın [– Kapsayıcı görüntüleri oluşturun.][aks-tutorial-prepare-app]

Bu öğretici, Azure CLI sürümünü 2.0.53 veya daha sonra çalıştırdığınızı gerektirir. Sürümü bulmak için `az --version` komutunu çalıştırın. Yüklemeniz veya yükseltmeniz gerekirse, bkz. [Azure CLI yükleme][azure-cli-install].

## <a name="update-the-manifest-file"></a>Bildirim dosyasını güncelleştirme

Bu öğreticilerde Azure Container Registry (ACR) örneği, örnek uygulamanın kapsayıcı görüntüsünü depolar. Uygulamayı dağıtmak için Kubernetes bildirim dosyası içindeki görüntü adını ACR oturum açma sunucusu adını içerecek şekilde güncelleştirmeniz gerekir.

ACR oturum açma sunucusunun adını almak için [az acr list][az-acr-list] komutunu aşağıda gösterilen şekilde kullanın:

```azurecli
az acr list --resource-group myResourceGroup --query "[].{acrLoginServer:loginServer}" --output table
```

İlk öğreticide kopyalanan git deposundaki örnek bildirim dosyasında oturum açma sunucusu adı olarak *microsoft* kullanılmıştır. Klonlanmış *azure-voting-app-redis* dizininde olduğunuzdan emin olun, ardından manifesto dosyasını aşağıdakiler gibi `vi`bir metin düzenleyicisi ile açın:

```console
vi azure-vote-all-in-one-redis.yaml
```

*microsoft* yerine ACR oturum açma sunucunuzun adını yazın. Resim adı, bildirim dosyasının 51 satırında bulunur. Aşağıdaki örnekte varsayılan görüntü adı gösterilir:

```yaml
containers:
- name: azure-vote-front
  image: microsoft/azure-vote-front:v1
```

Kendi ACR oturum açma sunucunuzun adını girerek bildirim dosyanızın aşağıdaki örnekte olduğu gibi görünmesini sağlayın:

```yaml
containers:
- name: azure-vote-front
  image: <acrName>.azurecr.io/azure-vote-front:v1
```

Dosyayı kaydedin ve kapatın. In `vi`, `:wq`kullanın.

## <a name="deploy-the-application"></a>Uygulamayı dağıtma

Uygulamanızı dağıtmak için [kubectl apply][kubectl-apply] komutunu kullanın. Bu komut, bildirim dosyasını ayrıştırır ve tanımlanmış Kubernetes nesnelerini oluşturur. Aşağıdaki örnekte gösterildiği gibi örnek bildirim dosyasının adını belirtin:

```console
kubectl apply -f azure-vote-all-in-one-redis.yaml
```

Aşağıdaki örnek çıktı, AKS kümesinde başarıyla oluşturulan kaynakları gösterir:

```
$ kubectl apply -f azure-vote-all-in-one-redis.yaml

deployment "azure-vote-back" created
service "azure-vote-back" created
deployment "azure-vote-front" created
service "azure-vote-front" created
```

## <a name="test-the-application"></a>Uygulamayı test etme

Uygulama çalıştığında, bir Kubernetes hizmeti uygulamanın ön ucunu internete maruz bırakır. Bu işlemin tamamlanması birkaç dakika sürebilir.

İlerlemeyi izlemek için, bağımsız değişkenle birlikte `--watch` [kubectl get service][kubectl-get] komutunu kullanın.

```console
kubectl get service azure-vote-front --watch
```

Başlangıçta *azure-vote-front* hizmeti için *EXTERNAL-IP* *beklemede*olarak gösterilir:

```
azure-vote-front   LoadBalancer   10.0.34.242   <pending>     80:30676/TCP   5s
```

*EXTERNAL-IP* adresi *beklemeden* gerçek bir genel IP `CTRL-C` adresine `kubectl` değiştiğinde, izleme işlemini durdurmak için kullanın. Aşağıdaki örnek çıktı, hizmete atanan geçerli bir genel IP adresini gösterir:

```
azure-vote-front   LoadBalancer   10.0.34.242   52.179.23.131   80:30676/TCP   67s
```

Uygulamayı iş başında görmek için, hizmetinizin harici IP adresine bir web tarayıcısı açın:

![Azure’da Kubernetes kümesinin görüntüsü](media/container-service-kubernetes-tutorials/azure-vote.png)

Uygulama yüklenmediyse, bunun nedeni görüntü kayıt defterinizdeki bir yetkilendirme sorunundan kaynaklanıyor olabilir. Kapsayıcılarınızın durumunu görüntülemek için `kubectl get pods` komutunu kullanın. Kapsayıcı görüntüleri çekilmiyorsa, [Azure Kubernetes Hizmeti'nden Azure Kapsayıcı Kayıt Defteri ile Kimlik Doğrulaması'ya](cluster-container-registry-integration.md)bakın.

## <a name="next-steps"></a>Sonraki adımlar

Bu eğitimde, aks'taki bir Kubernetes kümesine örnek bir Azure oy uygulaması dağıtıldı. Şunları öğrendiniz:

> [!div class="checklist"]
> * Kubernetes bildirim dosyalarını güncelleştirme
> * Kubernetes'te uygulama çalıştırma
> * Uygulamayı test etme

Kubernetes uygulamasını ve temel alınan Kubernetes altyapısını ölçeklendirme hakkında daha fazla bilgi için sonraki öğreticiye ilerleyin.

> [!div class="nextstepaction"]
> [Kubernetes uygulamasını ve altyapısını ölçeklendirme][aks-tutorial-scale]

<!-- LINKS - external -->
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-create]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#create
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get

<!-- LINKS - internal -->
[aks-tutorial-prepare-app]: ./tutorial-kubernetes-prepare-app.md
[aks-tutorial-scale]: ./tutorial-kubernetes-scale.md
[az-acr-list]: /cli/azure/acr
[azure-cli-install]: /cli/azure/install-azure-cli
[kubernetes-concepts]: concepts-clusters-workloads.md
[kubernetes-service]: concepts-network.md#services
