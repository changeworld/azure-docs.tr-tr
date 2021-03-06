---
title: Azure’da Kubernetes öğreticisi - Uygulamayı güncelleştirme
description: Bu Azure Kubernetes Service (AKS) öğreticisinde var olan bir AKS uygulama dağıtımını uygulama kodunun yeni sürümüyle güncelleştirmeyi öğreneceksiniz.
services: container-service
ms.topic: tutorial
ms.date: 12/19/2018
ms.custom: mvc
ms.openlocfilehash: d5457d790cd3c95bb23ec0c517097b443a2389ed
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/24/2020
ms.locfileid: "77593385"
---
# <a name="tutorial-update-an-application-in-azure-kubernetes-service-aks"></a>Öğretici: Azure Kubernetes Hizmeti’nde (AKS) bir uygulamayı güncelleştirme

Bir uygulama Kubernetes’te dağıtıldıktan sonra, yeni bir kapsayıcı görüntüsü veya görüntü sürümü belirtilerek güncelleştirilebilir. Dağıtımın yalnızca bir bölümünün aynı anda güncelleştirilen bir güncelleştirme sahnelenir. Hazırlanan bu güncelleştirme, uygulamanın güncelleştirme sırasında çalışmaya devam etmesini sağlar. Ayrıca bir dağıtım hatası oluşursa, bir geri alma mekanizması sağlar.

Bu yedi parçalı öğreticinin altıncı bölümünde, örnek Azure Vote uygulaması güncelleştirilir. Aşağıdakileri nasıl yapacağınızı öğrenirsiniz:

> [!div class="checklist"]
> * Ön uç uygulama kodunu güncelleştirme
> * Güncelleştirilmiş kapsayıcı görüntüsü oluşturma
> * Kapsayıcı görüntüsünü Azure Container Registry’ye gönderme
> * Güncelleştirilmiş kapsayıcı görüntüsünü dağıtma

## <a name="before-you-begin"></a>Başlamadan önce

Önceki öğreticilerde, bir uygulama bir kapsayıcı görüntüsüne paketlenmişti. Bu resim Azure Kapsayıcı Kayıt Defteri'ne yüklendi ve bir AKS kümesi oluşturdunuz. Uygulama daha sonra AKS kümesine dağıtıldı.

Bu öğreticide ayrıca uygulama kaynak kodunu içeren bir uygulama deposu da kopyalandı ve önceden oluşturulmuş bir Docker Compose dosyası kullanıldı. Repo'nun bir klonunu oluşturduğunuzu ve dizinleri klonlanmış dizine dönüştürttüldünden doğrulayın. Bu adımları tamamlamadıysanız ve takip etmek istiyorsanız, [Tutorial 1 – Kapsayıcı görüntüleri oluşturun.][aks-tutorial-prepare-app]

Bu öğretici, Azure CLI sürümünü 2.0.53 veya daha sonra çalıştırdığınızı gerektirir. Sürümü bulmak için `az --version` komutunu çalıştırın. Yüklemeniz veya yükseltmeniz gerekirse, bkz. [Azure CLI yükleme][azure-cli-install].

## <a name="update-an-application"></a>Uygulamayı güncelleştirme

Şimdi örnek uygulamada bir değişiklik yapalım ve AKS kümenize dağıtılmış olan sürümü güncelleştirelim. Klonlanmış *azure oylama-uygulama-redis* dizininde olduğunuzdan emin olun. Örnek uygulama kaynak kodu daha sonra *azure oy* dizininde bulunabilir. *config_file.cfg* dosyasını `vi` gibi bir düzenleyiciyle açın:

```console
vi azure-vote/azure-vote/config_file.cfg
```

*VOTE1VALUE* ve *VOTE2VALUE* değerlerini renkler gibi farklı değerlerle değiştirin. Aşağıdaki örnek, güncelleştirilmiş değerleri gösterir:

```
# UI Configurations
TITLE = 'Azure Voting App'
VOTE1VALUE = 'Blue'
VOTE2VALUE = 'Purple'
SHOWHOST = 'false'
```

Dosyayı kaydedin ve kapatın. In `vi`, `:wq`kullanın.

## <a name="update-the-container-image"></a>Kapsayıcı görüntüsünü güncelleştirme

Ön uç görüntüsünü yeniden oluşturmak ve güncelleştirilen uygulamayı test etmek için [docker-compose][docker-compose] komutunu kullanın. `--build` bağımsız değişkeni, uygulama görüntüsünü yeniden oluşturmak üzere Docker Compose'a komut vermek için kullanılır:

```console
docker-compose up --build -d
```

## <a name="test-the-application-locally"></a>Uygulamayı yerel olarak test etme

Güncelleştirilmiş kapsayıcı görüntüsünde yaptığınız değişikliklerin göründüğünü doğrulamak için yerel web tarayıcısında `http://localhost:8080` adresine gidin.

![Azure’da Kubernetes kümesinin görüntüsü](media/container-service-kubernetes-tutorials/vote-app-updated.png)

*config_file.cfg* dosyasında sağlanan güncelleştirilmiş değerler çalışan uygulamanızda görüntülenir.

## <a name="tag-and-push-the-image"></a>Görüntüyü etiketleme ve gönderme

Güncelleştirilen görüntüyü doğru şekilde kullanmak için *azure-vote-front* görüntüsünü ACR kayıt defterinizin oturum açma sunucusunun adıyla etiketleyin. [Az acr listesi](/cli/azure/acr) komutu ile giriş sunucusu adını alın:

```azurecli
az acr list --resource-group myResourceGroup --query "[].{acrLoginServer:loginServer}" --output table
```

Görüntüyü etiketlemek için [docker tag][docker-tag]’i kullanın. Aşağıda gösterilen şekilde `<acrLoginServer>` yerine ACR oturum açma sunucunuzun adını veya genel kayıt defteri ana bilgisayar adını yazın ve görüntü sürümünü *:v2* olarak güncelleştirin:

```console
docker tag azure-vote-front <acrLoginServer>/azure-vote-front:v2
```

Şimdi görüntüyü kayıt defterinize yüklemek için [docker push][docker-push] komutunu kullanın. `<acrLoginServer>` yerine ACR oturum açma sunucunuzun adını yazın.

> [!NOTE]
> ACR kayıt defterinize doğru itilen sorunlarla karşılaşırsanız, oturum açmadığınızdan emin olun. [Azure Kapsayıcı Kayıt Defteri Oluştur](tutorial-kubernetes-prepare-acr.md#create-an-azure-container-registry) adımında oluşturduğunuz Azure Kapsayıcı Kayıt Defteri'nizin adını kullanarak az [acr giriş][az-acr-login] komutunu çalıştırın. Örneğin, `az acr login --name <azure container registry name>`.

```console
docker push <acrLoginServer>/azure-vote-front:v2
```

## <a name="deploy-the-updated-application"></a>Güncelleştirilmiş uygulamayı dağıtma

Maksimum çalışma süresi sağlamak için, uygulama bölmesinin birden çok örneğinin çalışıyor olması gerekir. [kubectl get pods][kubectl-get] komutunu kullanarak çalışan ön uç görüntülerinin sayısını doğrulayın:

```
$ kubectl get pods

NAME                               READY     STATUS    RESTARTS   AGE
azure-vote-back-217588096-5w632    1/1       Running   0          10m
azure-vote-front-233282510-b5pkz   1/1       Running   0          10m
azure-vote-front-233282510-dhrtr   1/1       Running   0          10m
azure-vote-front-233282510-pqbfk   1/1       Running   0          10m
```

Birden çok ön uç bölmeniz yoksa, *azure oy ön* dağıtımını aşağıdaki gibi ölçeklendirin:

```console
kubectl scale --replicas=3 deployment/azure-vote-front
```

Uygulamayı güncelleştirmek için [kubectl set][kubectl-set] komutunu kullanın. `<acrLoginServer>` öğesini, kapsayıcı kayıt defterinizin oturum açma sunucusu veya ana bilgisayar adıyla güncelleştirin ve uygulama sürümünü *v2* olarak belirtin:

```console
kubectl set image deployment azure-vote-front azure-vote-front=<acrLoginServer>/azure-vote-front:v2
```

Dağıtımı izlemek için [kubectl get pod][kubectl-get] komutunu kullanın. Güncelleştirilmiş uygulama dağıtıldığında, podlarınız sonlandırılır ve yeni kapsayıcı görüntüsüyle yeniden oluşturulur.

```console
kubectl get pods
```

Aşağıdaki örnek çıktı, dağıtımın ilerlemesi sırasında sonlandırılan podları ve çalışan yeni örnekleri göstermektedir:

```
$ kubectl get pods

NAME                               READY     STATUS        RESTARTS   AGE
azure-vote-back-2978095810-gq9g0   1/1       Running       0          5m
azure-vote-front-1297194256-tpjlg  1/1       Running       0          1m
azure-vote-front-1297194256-tptnx  1/1       Running       0          5m
azure-vote-front-1297194256-zktw9  1/1       Terminating   0          1m
```

## <a name="test-the-updated-application"></a>Güncelleştirilmiş uygulamayı test etme

Güncelleştirme uygulamasını görüntülemek için öncelikle `azure-vote-front` hizmetinin dış IP'sini alın:

```console
kubectl get service azure-vote-front
```

Artık yerel bir web tarayıcısını hizmetinizin IP adresine açın:

![Azure’da Kubernetes kümesinin görüntüsü](media/container-service-kubernetes-tutorials/vote-app-updated-external.png)

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide, bir uygulamayı güncellediniz ve bu güncelleştirmeyi AKS kümenize kaydettiniz. Şunları öğrendiniz:

> [!div class="checklist"]
> * Ön uç uygulama kodunu güncelleştirme
> * Güncelleştirilmiş kapsayıcı görüntüsü oluşturma
> * Kapsayıcı görüntüsünü Azure Container Registry’ye gönderme
> * Güncelleştirilmiş kapsayıcı görüntüsünü dağıtma

Bir AKS kümesini yeni bir Kubernetes sürümüne yükseltmeyi öğrenmek için bir sonraki öğreticiye geçin.

> [!div class="nextstepaction"]
> [Kubernetes’i yükseltme][aks-tutorial-upgrade]

<!-- LINKS - external -->
[docker-compose]: https://docs.docker.com/compose/
[docker-push]: https://docs.docker.com/engine/reference/commandline/push/
[docker-tag]: https://docs.docker.com/engine/reference/commandline/tag/
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-set]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#set

<!-- LINKS - internal -->
[aks-tutorial-prepare-app]: ./tutorial-kubernetes-prepare-app.md
[aks-tutorial-upgrade]: ./tutorial-kubernetes-upgrade-cluster.md
[az-acr-login]: /cli/azure/acr
[azure-cli-install]: /cli/azure/install-azure-cli
