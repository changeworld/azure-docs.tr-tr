---
title: 'Hızlı Başlangıç: Python için Özel Görüntü İşleme SDK’sı ile görüntü sınıflandırma projesi oluşturma'
titleSuffix: Azure Cognitive Services
description: Python SDK'sını kullanarak bir proje oluşturun, etiketler ekleyin, görüntüleri karşıya yükleyin, projenizi eğitin ve bir tahminde bulunun.
services: cognitive-services
author: areddish
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: quickstart
ms.date: 12/05/2019
ms.author: areddish
ms.openlocfilehash: ffb12af93e249b0b9bb510d3507a30e67d6cd19f
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76169139"
---
# <a name="quickstart-create-an-image-classification-project-with-the-custom-vision-python-sdk"></a>Hızlı Başlangıç: Özel Görüntü İşleme Python SDK’sı ile görüntü sınıflandırma projesi oluşturma

Bu makalede, bir görüntü sınıflandırma modeli oluşturmak için Python ile Özel Vizyon SDK kullanmaya başlamak için nasıl gösterir. Oluşturulduktan sonra etiketler ekleyebilir, resim yükleyebilir, projeyi eğitebilir, projenin yayınlanmış tahmin bitiş noktası URL'sini alabilir ve bir resmi programlı olarak sınamak için bitiş noktasını kullanabilirsiniz. Kendi Python uygulamanızı oluştururken bu örneği şablon olarak kullanın. Kod _içermeyen_ bir sınıflandırma modeli oluşturma ve kullama işlemi yapmak istiyorsanız, [tarayıcı tabanlı kılavuz](getting-started-build-a-classifier.md) konusuna bakın.

## <a name="prerequisites"></a>Ön koşullar

- [Python 2.7+ veya 3.5+](https://www.python.org/downloads/)
- [pip](https://pip.pypa.io/en/stable/installing/) aracı
- [!INCLUDE [create-resources](includes/create-resources.md)]

## <a name="install-the-custom-vision-sdk"></a>Özel Görüntü İşleme SDK’sını yükleme

PowerShell'de aşağıdaki komutu çalıştırarak Python için Özel Görüntü İşleme Hizmeti SDK’sını yükleyin:

```powershell
pip install azure-cognitiveservices-vision-customvision
```

[!INCLUDE [get-keys](includes/get-keys.md)]

[!INCLUDE [python-get-images](includes/python-get-images.md)]

## <a name="add-the-code"></a>Kod ekleme

Tercih ettiğiniz proje dizininde *sample.py* adlı yeni bir dosya oluşturun.

### <a name="create-the-custom-vision-service-project"></a>Özel Görüntü İşleme hizmeti projesi oluşturma

Yeni bir Özel Görüntü İşleme hizmeti projesi oluşturmak için betiğinize aşağıdaki kodu ekleyin. Abonelik anahtarlarınızı uygun tanımlara ekleyin. Ayrıca, Özel Görüntü İşleme Web sitesinin ayarlar sayfasından uç nokta URL 'nizi alın.

Projenizi oluştururken diğer seçenekleri belirtmek için [create_project](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-customvision/azure.cognitiveservices.vision.customvision.training.custom_vision_training_client.customvisiontrainingclient?view=azure-python#create-project-name--description-none--domain-id-none--classification-type-none--target-export-platforms-none--custom-headers-none--raw-false----operation-config- ) yöntemine bakın [(sınıflandırıcı](getting-started-build-a-classifier.md) web portalı oluştur kılavuzunda açıklanmıştır).  

```Python
from azure.cognitiveservices.vision.customvision.training import CustomVisionTrainingClient
from azure.cognitiveservices.vision.customvision.training.models import ImageFileCreateEntry

ENDPOINT = "<your API endpoint>"

# Replace with a valid key
training_key = "<your training key>"
prediction_key = "<your prediction key>"
prediction_resource_id = "<your prediction resource id>"

publish_iteration_name = "classifyModel"

trainer = CustomVisionTrainingClient(training_key, endpoint=ENDPOINT)

# Create a new project
print ("Creating project...")
project = trainer.create_project("My New Project")
```

### <a name="create-tags-in-the-project"></a>Projede etiketler oluşturma

Projenizde sınıflandırma etiketleri oluşturmak için, *sample.py* dosyasının sonuna aşağıdaki kodu ekleyin:

```Python
# Make two tags in the new project
hemlock_tag = trainer.create_tag(project.id, "Hemlock")
cherry_tag = trainer.create_tag(project.id, "Japanese Cherry")
```

### <a name="upload-and-tag-images"></a>Görüntüleri karşıya yükleme ve etiketleme

Projeye örnek görüntüleri eklemek için etiket oluşturduktan sonra aşağıdaki kodu ekleyin. Bu kod, her görüntüyü ilgili etiketiyle birlikte karşıya yükler. Tek bir toplu iş halinde en fazla 64 resim yükleyebilirsiniz.

> [!NOTE]
> Bilişsel Hizmetler Python SDK Örnekleri repo'yu daha önce indirdiğiniz yere bağlı olarak resimlerin yolunu değiştirmeniz gerekir.

```Python
base_image_url = "<path to repo directory>/cognitive-services-python-sdk-samples/samples/vision/"

print("Adding images...")

image_list = []

for image_num in range(1, 11):
    file_name = "hemlock_{}.jpg".format(image_num)
    with open(base_image_url + "images/Hemlock/" + file_name, "rb") as image_contents:
        image_list.append(ImageFileCreateEntry(name=file_name, contents=image_contents.read(), tag_ids=[hemlock_tag.id]))

for image_num in range(1, 11):
    file_name = "japanese_cherry_{}.jpg".format(image_num)
    with open(base_image_url + "images/Japanese Cherry/" + file_name, "rb") as image_contents:
        image_list.append(ImageFileCreateEntry(name=file_name, contents=image_contents.read(), tag_ids=[cherry_tag.id]))

upload_result = trainer.create_images_from_files(project.id, images=image_list)
if not upload_result.is_batch_successful:
    print("Image batch upload failed.")
    for image in upload_result.images:
        print("Image status: ", image.status)
    exit(-1)
```

### <a name="train-the-classifier-and-publish"></a>Sınıflandırıcıyı eğitin ve yayımlayın

Bu kod, tahmin modelinin ilk yinelemesini oluşturur ve sonra bu yinelemeyi tahmin bitiş noktasına yayımlar. Yayımlanan yinelemeye verilen ad, tahmin istekleri göndermek için kullanılabilir. Bir yineleme, yayımlanana kadar tahmin bitiş noktasında kullanılamaz.

```Python
import time

print ("Training...")
iteration = trainer.train_project(project.id)
while (iteration.status != "Completed"):
    iteration = trainer.get_iteration(project.id, iteration.id)
    print ("Training status: " + iteration.status)
    time.sleep(1)

# The iteration is now trained. Publish it to the project endpoint
trainer.publish_iteration(project.id, iteration.id, publish_iteration_name, prediction_resource_id)
print ("Done!")
```

### <a name="get-and-use-the-published-iteration-on-the-prediction-endpoint"></a>Yayınlanan yinelemeyi tahmin bitiş noktasında alın ve kullanın

Tahmin uç noktasına bir görüntü göndermek ve tahmini almak için dosyanın sonuna aşağıdaki kodu ekleyin:

```python
from azure.cognitiveservices.vision.customvision.prediction import CustomVisionPredictionClient

# Now there is a trained endpoint that can be used to make a prediction
predictor = CustomVisionPredictionClient(prediction_key, endpoint=ENDPOINT)

with open(base_image_url + "images/Test/test_image.jpg", "rb") as image_contents:
    results = predictor.classify_image(
        project.id, publish_iteration_name, image_contents.read())

    # Display the results.
    for prediction in results.predictions:
        print("\t" + prediction.tag_name +
              ": {0:.2f}%".format(prediction.probability * 100))
```

## <a name="run-the-application"></a>Uygulamayı çalıştırma

*Sample.py*'yi çalıştırın.

```powershell
python sample.py
```

Uygulamanın çıkışı aşağıdaki metne benzer olmalıdır:

```console
Creating project...
Adding images...
Training...
Training status: Training
Training status: Completed
Done!
        Hemlock: 93.53%
        Japanese Cherry: 0.01%
```

Daha sonra test görüntüsünün **(<base_image_url>görüntülerin/Test/ )** uygun şekilde etiketlendirildiğini doğrulayabilirsiniz. Ayrıca [Özel Görüntü İşleme web sitesine](https://customvision.ai) geri dönebilir ve yeni oluşturulan projenizin geçerli durumunu görebilirsiniz.

[!INCLUDE [clean-ic-project](includes/clean-ic-project.md)]

## <a name="next-steps"></a>Sonraki adımlar

Artık kodda görüntü sınıflandırma işleminin her adımının nasıl uygulanabileceğini gördünüz. Bu örnek tek bir eğitim yinelemesi yürütür ama modelinizin daha doğru olmasını sağlamak için çoğunlukla birden çok kez eğitmeniz ve test etmeniz gerekecektir.

> [!div class="nextstepaction"]
> [Modeli test etme ve yeniden eğitme](test-your-model.md)
