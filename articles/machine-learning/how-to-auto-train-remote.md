---
title: Otomatik ML uzaktan işlem hedefleri
titleSuffix: Azure Machine Learning
description: Azure Machine Learning ile Azure Machine Learning uzaktan bilgi işlem hedefinde otomatik makine öğrenimini kullanarak nasıl model oluşturabilirsiniz öğrenin
services: machine-learning
author: cartacioS
ms.author: sacartac
ms.reviewer: sgilley
ms.service: machine-learning
ms.subservice: core
ms.workload: data-services
ms.topic: conceptual
ms.date: 03/09/2020
ms.openlocfilehash: 9e499d609a3f78dc5f422b9ed90df09be30f2e7c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79080420"
---
# <a name="train-models-with-automated-machine-learning-in-the-cloud"></a>Otomatik makine öğrenmesi ile modelleri bulutta eğitin

[!INCLUDE [aml-applies-to-basic-enterprise-sku](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Azure Machine Learning'de, modelinizi yönettiğiniz farklı türde bilgi işlem kaynakları üzerinde eğitebilirsiniz. Bilgi işlem hedefi yerel bir bilgisayar veya buluttaki bir kaynak olabilir.

Azure Machine Learning Compute (AmlCompute) gibi ek bilgi işlem hedefleri ekleyerek makine öğrenimi denemenizi kolayca büyütebilir veya ölçeklendirebilirsiniz. AmlCompute, kolayca tek veya çok düğümlü bir işlem oluşturmanıza olanak tanıyan, yönetilen bir işlem altyapısıdır.

Bu makalede, Nasıl AmlCompute ile otomatik ML kullanarak bir model oluşturmak için öğrenirler.

## <a name="how-does-remote-differ-from-local"></a>Uzaktan kumandanın yerelden farkı nedir?

Öğretici "[Otomatik makine öğrenme ile bir sınıflandırma modeli tren](tutorial-auto-train-models.md)" otomatik ML ile bir model eğitmek için yerel bir bilgisayar nasıl kullanılacağını öğretir. Yerel olarak eğitim verirken yapılan iş akışı uzak hedefler için de geçerlidir. Ancak, uzaktan hesaplama ile, otomatik ML deneme yinelemeler eşit olarak yürütülür. Bu işlev, belirli bir yinelemeyi iptal etmenizi, yürütmedurumunu izlemenizi veya Jupyter not defterindeki diğer hücreler üzerinde çalışmaya devam etmenizi sağlar. Uzaktan eğitim almak için önce AmlCompute gibi bir uzaktan işlem hedefi oluşturursunuz. Sonra uzak kaynağı yapılandırın ve kodunuzu oraya gönderirsiniz.

Bu makalede, uzak bir AmlCompute hedef üzerinde otomatik bir ML deneme çalıştırmak için gerekli ekstra adımları gösterir. Çalışma alanı nesnesi, `ws`öğretici burada kod boyunca kullanılır.

```python
ws = Workspace.from_config()
```

## <a name="create-resource"></a>Kaynak oluşturma

Zaten [`AmlCompute`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.amlcompute%28class%29?view=azure-ml-py) yoksa, çalışma alanınızda hedef oluşturun .`ws`

**Zaman tahmini**: AmlCompute hedefinin oluşturulması yaklaşık 5 dakika sürer.

```python
from azureml.core.compute import AmlCompute
from azureml.core.compute import ComputeTarget
import os

# choose a name for your cluster
compute_name = os.environ.get("AML_COMPUTE_CLUSTER_NAME", "cpu-cluster")
compute_min_nodes = os.environ.get("AML_COMPUTE_CLUSTER_MIN_NODES", 0)
compute_max_nodes = os.environ.get("AML_COMPUTE_CLUSTER_MAX_NODES", 4)

# This example uses CPU VM. For using GPU VM, set SKU to STANDARD_NC6
vm_size = os.environ.get("AML_COMPUTE_CLUSTER_SKU", "STANDARD_D2_V2")


if compute_name in ws.compute_targets:
    compute_target = ws.compute_targets[compute_name]
    if compute_target and type(compute_target) is AmlCompute:
        print('found compute target. just use it. ' + compute_name)
else:
    print('creating a new compute target...')
    provisioning_config = AmlCompute.provisioning_configuration(vm_size = vm_size,
                                                                min_nodes = compute_min_nodes, 
                                                                max_nodes = compute_max_nodes)

    # create the cluster
    compute_target = ComputeTarget.create(ws, compute_name, provisioning_config)
    
    # can poll for a minimum number of nodes and for a specific timeout. 
    # if no min node count is provided it will use the scale settings for the cluster
    compute_target.wait_for_completion(show_output=True, min_node_count=None, timeout_in_minutes=20)
    
     # For a more detailed view of current AmlCompute status, use get_status()
    print(compute_target.get_status().serialize())
```

Artık nesneyi `compute_target` uzaktan bilgi işlem hedefi olarak kullanabilirsiniz.

Küme adı kısıtlamaları şunlardır:
+ 64 karakterden daha kısa olmalıdır.
+ Aşağıdaki karakterlerden herhangi birini `\` içeremez: ~ ! @ # $ % ^ & * ( \\ \\ ) = + _ [ ] { } | : \' \\" , < > / ?. `

## <a name="access-data-using-tabulardataset-function"></a>TabularDataset işlevini kullanarak verilere erişin

Tanımlanan training_data [`TabularDataset`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.tabulardataset?view=azure-ml-py) olarak ve etiket, otomatik ML [`AutoMLConfig`](https://docs.microsoft.com/python/api/azureml-train-automl-client/azureml.train.automl.automlconfig.automlconfig?view=azure-ml-py)geçirilir. Yöntem `TabularDataset` `from_delimited_files`, varsayılan olarak, `infer_column_types` sütunlar türü otomatik olarak çıkaracaktır true ayarlar. 

Sütun türlerini el ile ayarlamak isterseniz, bağımsız `set_column_types` değişkeni her sütunun türünü el ile ayarlayabilirsiniz. Aşağıdaki kod örneğinde, veriler sklearn paketinden gelir.

```python
from sklearn import datasets
from azureml.core.dataset import Dataset
from scipy import sparse
import numpy as np
import pandas as pd
import os

# Create a project_folder if it doesn't exist
if not os.path.isdir('data'):
    os.mkdir('data')
    
if not os.path.exists('project_folder'):
    os.makedirs('project_folder')

X = pd.DataFrame(data_train.data[100:,:])
y = pd.DataFrame(data_train.target[100:])

# merge X and y
label = "digit"
X[label] = y

training_data = X

training_data.to_csv('data/digits.csv')
ds = ws.get_default_datastore()
ds.upload(src_dir='./data', target_path='digitsdata', overwrite=True, show_progress=True)

training_data = Dataset.Tabular.from_delimited_files(path=ds.path('digitsdata/digits.csv'))
```

## <a name="configure-experiment"></a>Denemeyi yapılandırma
`AutoMLConfig`' nin ayarlarını belirtin.  [(Parametrelerin tam listesine](how-to-configure-auto-train.md#configure-experiment) ve olası değerlerine bakın.)

```python
from azureml.train.automl import AutoMLConfig
import time
import logging

automl_settings = {
    "name": "AutoML_Demo_Experiment_{0}".format(time.time()),
    "experiment_timeout_minutes" : 20,
    "enable_early_stopping" : True,
    "iteration_timeout_minutes": 10,
    "n_cross_validations": 5,
    "primary_metric": 'AUC_weighted',
    "max_concurrent_iterations": 10,
}

automl_config = AutoMLConfig(task='classification',
                             debug_log='automl_errors.log',
                             path=project_folder,
                             compute_target=compute_target,
                             training_data=training_data,
                             label_column_name=label,
                             **automl_settings,
                             )
```

## <a name="submit-training-experiment"></a>Eğitim denemesi gönder

Şimdi algoritmayı, hiper parametreleri otomatik olarak seçmek ve modeli eğitmek için yapılandırmayı gönderin.

```python
from azureml.core.experiment import Experiment
experiment = Experiment(ws, 'automl_remote')
remote_run = experiment.submit(automl_config, show_output=True)
```

Aşağıdaki örneğe benzer çıktı görürsünüz:

    Running on remote compute: mydsvmParent Run ID: AutoML_015ffe76-c331-406d-9bfd-0fd42d8ab7f6
    ***********************************************************************************************
    ITERATION: The iteration being evaluated.
    PIPELINE:  A summary description of the pipeline being evaluated.
    DURATION: Time taken for the current iteration.
    METRIC: The result of computing score on the fitted pipeline.
    BEST: The best observed score thus far.
    ***********************************************************************************************

     ITERATION     PIPELINE                               DURATION                METRIC      BEST
             2      Standardize SGD classifier            0:02:36                  0.954     0.954
             7      Normalizer DT                         0:02:22                  0.161     0.954
             0      Scale MaxAbs 1 extra trees            0:02:45                  0.936     0.954
             4      Robust Scaler SGD classifier          0:02:24                  0.867     0.954
             1      Normalizer kNN                        0:02:44                  0.984     0.984
             9      Normalizer extra trees                0:03:15                  0.834     0.984
             5      Robust Scaler DT                      0:02:18                  0.736     0.984
             8      Standardize kNN                       0:02:05                  0.981     0.984
             6      Standardize SVM                       0:02:18                  0.984     0.984
            10      Scale MaxAbs 1 DT                     0:02:18                  0.077     0.984
            11      Standardize SGD classifier            0:02:24                  0.863     0.984
             3      Standardize gradient boosting         0:03:03                  0.971     0.984
            12      Robust Scaler logistic regression     0:02:32                  0.955     0.984
            14      Scale MaxAbs 1 SVM                    0:02:15                  0.989     0.989
            13      Scale MaxAbs 1 gradient boosting      0:02:15                  0.971     0.989
            15      Robust Scaler kNN                     0:02:28                  0.904     0.989
            17      Standardize kNN                       0:02:22                  0.974     0.989
            16      Scale 0/1 gradient boosting           0:02:18                  0.968     0.989
            18      Scale 0/1 extra trees                 0:02:18                  0.828     0.989
            19      Robust Scaler kNN                     0:02:32                  0.983     0.989


## <a name="explore-results"></a>Sonuçları keşfedin

Bir grafik ve sonuç tablosunu görmek için [eğitim öğreticisinde](tutorial-auto-train-models.md#explore-the-results) gösterildiği gibi aynı [Jupyter widget'ını](https://docs.microsoft.com/python/api/azureml-widgets/azureml.widgets?view=azure-ml-py) kullanabilirsiniz.

```python
from azureml.widgets import RunDetails
RunDetails(remote_run).show()
```

Burada pencere öğesinin statik bir görüntüsü yer alır.  Not defterinde, bu çalıştırmaiçin çalışma özelliklerini ve çıktı günlüklerini görmek için tablodaki herhangi bir satırı tıklatabilirsiniz.   Ayrıca, her yineleme için kullanılabilir her metnin grafiğini görüntülemek için grafiğin üzerindeki açılır bırakmayı da kullanabilirsiniz.

![pencere öğesi tablosu](./media/how-to-auto-train-remote/table.png)
![pencere öğesi çizimi](./media/how-to-auto-train-remote/plot.png)

Widget, tek tek çalışan ayrıntılarını görmek ve keşfetmek için kullanabileceğiniz bir URL görüntüler.  

Jupyter dizüstü bilgisayarda değilseniz, çalıştırın kendisinden URL görüntüleyebilirsiniz:

```
remote_run.get_portal_url()
```

Aynı bilgiler çalışma alanınızda da kullanılabilir.  Bu sonuçlar hakkında daha fazla bilgi edinmek için [bkz.](how-to-understand-automated-ml.md)

## <a name="example"></a>Örnek

Aşağıdaki [not defteri](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/regression/auto-ml-regression.ipynb) bu makalede kavramları gösterir.

[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>Sonraki adımlar

* [Otomatik eğitim için ayarları nasıl yapılandırıştırılayarıştırılayarıştırmayı](how-to-configure-auto-train.md)öğrenin.
* Otomatik ML denemelerinde model yorumlanabilirlik özelliklerini etkinleştirme [nasıl yapılacağını](how-to-machine-learning-interpretability-automl.md) görün.
