---
title: Linux Python uygulamalarını yapılandırma
description: Uygulamanız için önceden oluşturulmuş bir Python kapsayıcısını nasıl yapılandıracaklarınızı öğrenin. Bu makalede, en yaygın yapılandırma görevleri gösterir.
ms.topic: quickstart
ms.date: 03/28/2019
ms.reviewer: astay; kraigb
ms.custom: mvc, seodec18
ms.openlocfilehash: 8a9276f73c1d9bdf0289f41bb59340b29f5a2575
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/26/2020
ms.locfileid: "80046015"
---
# <a name="configure-a-linux-python-app-for-azure-app-service"></a>Azure Uygulama Hizmeti için bir Linux Python uygulamasını yapılandırma

Bu makalede, [Azure Uygulama Hizmeti'nin](app-service-linux-intro.md) Python uygulamalarını nasıl çalıştırdığınız ve gerektiğinde Uygulama Hizmeti'nin davranışını nasıl özelleştirebileceğiniz açıklanmaktadır. Python uygulamaları gerekli tüm [pip](https://pypi.org/project/pip/) modülleri ile dağıtılmalıdır.

Uygulama Hizmeti dağıtım motoru sanal bir ortamı `pip install -r requirements.txt` otomatik olarak etkinleştirir ve [bir Git deposu](../deploy-local-git.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)veya yapı işlemlerinin açık olduğu bir [Zip paketini](../deploy-zip.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json) dağıttığınızda sizin için çalışır.

Bu kılavuz, App Service'de yerleşik bir Linux kapsayıcısı kullanan Python geliştiricileri için temel kavramlar ve yönergeler sağlar. Azure Uygulama Hizmeti'ni hiç kullanmadıysanız, önce [PostgreSQL öğreticisiyle](tutorial-python-postgresql-app.md) [Python quickstart](quickstart-python.md) ve Python'u takip etmelisiniz.

> [!NOTE]
> Linux şu anda App Service Python uygulamaları çalıştırmak için önerilen seçenektir. Windows seçeneği hakkında daha fazla bilgi için, [Uygulama Hizmeti'nin Windows lezzetinde Python'a](https://docs.microsoft.com/visualstudio/python/managing-python-on-azure-app-service)bakın.
>

## <a name="show-python-version"></a>Python sürümünü göster

Geçerli Python sürümünü göstermek için [Bulut Kabuğu'nda](https://shell.azure.com)aşağıdaki komutu çalıştırın:

```azurecli-interactive
az webapp config show --resource-group <resource-group-name> --name <app-name> --query linuxFxVersion
```

Desteklenen tüm Python sürümlerini göstermek için [Bulut Kabuğu'nda](https://shell.azure.com)aşağıdaki komutu çalıştırın:

```azurecli-interactive
az webapp list-runtimes --linux | grep PYTHON
```

Bunun yerine kendi kapsayıcı resminizi oluşturarak Python'un desteklenmeyen bir sürümünü çalıştırabilirsiniz. Daha fazla bilgi için [bkz.](tutorial-custom-docker-image.md)

## <a name="set-python-version"></a>Python sürümünü ayarlama

Python sürümünü 3,7 olarak ayarlamak için [Bulut Kabuğu'nda](https://shell.azure.com) aşağıdaki komutu çalıştırın:

```azurecli-interactive
az webapp config set --resource-group <resource-group-name> --name <app-name> --linux-fx-version "PYTHON|3.7"
```

## <a name="customize-build-automation"></a>Yapı otomasyonu özelleştirme

Uygulamanızı Git veya zip paketlerini yapı otomasyonu açık ken kullanırsanız, Uygulama Hizmeti aşağıdaki sırayla otomasyon adımları oluşturur:

1. 'ye göre belirtilirse özel komut dosyası çalıştırın `PRE_BUILD_SCRIPT_PATH`
1. `pip install -r requirements.txt` öğesini çalıştırın.
1. *Deponun* kökünde manage.py bulunursa, manage.py *toplayın.* Ancak, `DISABLE_COLLECTSTATIC` ayarlanırsa `true`, bu adım atlanır.
1. 'ye göre belirtilirse özel komut dosyası çalıştırın `POST_BUILD_SCRIPT_PATH`

`PRE_BUILD_COMMAND`, `POST_BUILD_COMMAND`, `DISABLE_COLLECTSTATIC` ve varsayılan olarak boş olan ortam değişkenleridir. Önceden yapı komutları çalıştırmak `PRE_BUILD_COMMAND`için tanımlayın. Yapı sonrası komutları çalıştırmak `POST_BUILD_COMMAND`için. Django uygulamaları inşa ederken çalışan collectstatic `DISABLE_COLLECTSTATIC=true`devre dışı kalmak için, ayarlayın.

Aşağıdaki örnekte, iki değişken virgülle ayrılmış bir dizi komuta göre belirtilir.

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings PRE_BUILD_COMMAND="echo foo, scripts/prebuild.sh"
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings POST_BUILD_COMMAND="echo foo, scripts/postbuild.sh"
```

Yapı otomasyonu özelleştirmek için ek ortam değişkenleri için [Bkz. Oryx yapılandırması.](https://github.com/microsoft/Oryx/blob/master/doc/configuration.md)

Uygulama Hizmeti'nin Linux'ta Python uygulamalarını nasıl çalıştırıp oluşturduğu hakkında daha fazla bilgi için [Oryx belgelerine bakın: Python uygulamaları nasıl algılanır ve oluşturulur.](https://github.com/microsoft/Oryx/blob/master/doc/runtimes/python.md)

## <a name="container-characteristics"></a>Kapsayıcı özellikleri

Linux'ta App Service'e dağıtılan Python uygulamaları, [App Service Python GitHub deposunda](https://github.com/Azure-App-Service/python)tanımlanan bir Docker kapsayıcısı içinde çalışır. Sürüme özgü dizinlerin içinde görüntü yapılandırmalarını bulabilirsiniz.

Bu kapsayıcı aşağıdaki özelliklere sahiptir:

- Uygulamalar ek `--bind=0.0.0.0 --timeout 600` bağımsız değişkenleri kullanılarak [Gunicorn WSGI HTTP Server](https://gunicorn.org/) ile çalıştırılır.

- Temel görüntü varsayılan olarak Flask web çerçevesini içerir ancak kapsayıcı Django gibi WSGI ve Python 3.7 ile uyumlu diğer çerçeveleri de destekler.

- Django gibi ek paketleri yüklemek için `pip freeze > requirements.txt` kullanarak projenizin kök dizininde bir [*requirements.txt*](https://pip.pypa.io/en/stable/user_guide/#requirements-files) dosyası oluşturun. Ardından projenizi Git dağıtımı kullanarak App Service'te yayımlayın. Bunu yaptığınızda uygulamanızın bağımlılıklarının yüklenmesi için kapsayıcıda otomatik olarak `pip install -r requirements.txt` çalıştırılır.

## <a name="container-startup-process"></a>Konteyner başlatma işlemi

Başlatma sırasında Linux'ta App Service kapsayıcısı şu adımları çalıştırır:

1. Sağlandığı takdirde [özel bir başlangıç komutu](#customize-startup-command)kullanın.
2. Bir [Django uygulamasının](#django-app)varlığını kontrol edin ve tespit edilirse bunun için Gunicorn başlatın.
3. Bir [Flask uygulamasının](#flask-app)varlığını kontrol edin ve tespit edilirse bunun için Gunicorn başlatın.
4. Başka bir uygulama bulunamazsa yoksa kapsayıcıda bulunan varsayılan uygulamayı başlatır.

Aşağıdaki bölümlerde bu seçeneklerle ilgili ek ayrıntılar verilmiştir.

### <a name="django-app"></a>Django uygulaması

Django uygulamaları için App Service uygulama kodunuzda `wsgi.py` adlı bir dosya olup olmadığını denetler ve ardından şu komutu kullanarak Gunicorn'u çalıştırır:

```bash
# <module> is the path to the folder that contains wsgi.py
gunicorn --bind=0.0.0.0 --timeout 600 <module>.wsgi
```

Başlangıç komutu üzerinde daha fazla denetime sahip olmak istiyorsanız [özel başlangıç komutu](#customize-startup-command) kullanın ve `<module>` yerine *wsgi.py* dosyasını içeren modülün adını yazın.

### <a name="flask-app"></a>Flask uygulaması

Flask için, App Service *application.py* veya *app.py* adlı bir dosya arar ve Gunicorn'u aşağıdaki gibi başlatır:

```bash
# If application.py
gunicorn --bind=0.0.0.0 --timeout 600 application:app
# If app.py
gunicorn --bind=0.0.0.0 --timeout 600 app:app
```

Ana uygulama modülünüz farklı bir dosyada bulunuyorsa uygulama nesnesi için farklı bir ad kullanın veya Gunicorn'a ek bağımsız değişkenler sağlamak istiyorsanız [özel başlangıç komutu](#customize-startup-command) kullanın.

### <a name="default-behavior"></a>Varsayılan davranış

App Service özel komut dosyası, Django uygulaması veya Flask uygulaması bulamazsa _opt/defaultsite_ klasöründe bulunan varsayılan salt okunur uygulamayı çalıştırır. Varsayılan uygulama aşağıdaki gibi görünür:

![Varsayılan Linux'ta App Service web sayfası](media/how-to-configure-python/default-python-app.png)

## <a name="customize-startup-command"></a>Başlangıç komutunu özelleştirme

Özel bir Gunicorn başlangıç komutu sağlayarak kapsayıcının başlangıç davranışını denetleyebilirsiniz. Bunu yapmak için [Bulut Kabuğu'nda](https://shell.azure.com)aşağıdaki komutu çalıştırın:

```azurecli-interactive
az webapp config set --resource-group <resource-group-name> --name <app-name> --startup-file "<custom-command>"
```

Örneğin, ana modülü *hello.py* olan bir Flask uygulamanız varsa ve bu dosyadaki Flask uygulaması nesnesi adlandırılmışsa, `myapp` * \<özel komut>* aşağıdaki gibidir:

```bash
gunicorn --bind=0.0.0.0 --timeout 600 hello:myapp
```

Ana modülünüz `website` gibi bir alt klasör ise bu klasörü `--chdir` bağımsız değişkeniyle belirtin:

```bash
gunicorn --bind=0.0.0.0 --timeout 600 --chdir website hello:myapp
```

Ayrıca Gunicorn * \< *için özel komut>ek argümanlar ekleyebilirsiniz `--workers=4`, gibi . Daha fazla bilgi için bkz. [Gunicorn'u Çalıştırma](https://docs.gunicorn.org/en/stable/run.html) (docs.gunicorn.org).

[Aiohttp](https://aiohttp.readthedocs.io/en/stable/web_quickstart.html)gibi Gunicorn olmayan bir sunucu kullanmak için, * \<özel komut>'ı* şu gibi bir şeyle değiştirebilirsiniz:

```bash
python3.7 -m aiohttp.web -H localhost -P 8080 package.module:init_func
```

> [!Note]
> App Service, özel komut dosyasının işlenmesi sırasında oluşan hataları yoksayar ve başlatma işlemine Django ve Flask uygulamalarını arayarak devam eder. Beklediğiniz davranışı görmüyorsanız başlangıç dosyanızın App Service'e dağıtıldığından ve dosyada hata bulunmadığından emin olun.

## <a name="access-environment-variables"></a> Ortam değişkenlerine erişme

Uygulama Hizmeti'nde, [uygulama ayarlarınızı](../configure-common.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#configure-app-settings) uygulama kodunuzu dışında ayarlayabilirsiniz. Daha sonra standart [os.environ](https://docs.python.org/3/library/os.html#os.environ) deseni kullanarak onlara erişebilirsiniz. Örneğin, adlı `WEBSITE_SITE_NAME`bir uygulama ayarına erişmek için aşağıdaki kodu kullanın:

```python
os.environ['WEBSITE_SITE_NAME']
```

## <a name="detect-https-session"></a>HTTPS oturumunu algıla

App Service'te, [SSL sonlandırma](https://wikipedia.org/wiki/TLS_termination_proxy) ağı yük dengeleyicilerinde gerçekleşir, böylece tüm HTTPS istekleri uygulamanıza şifrelenmemiş HTTP istekleri olarak ulaşır. Uygulama mantığınızın kullanıcı isteklerinin şifreli olup olmadığını denetlemesi gerekiyorsa, üstbilgide inceleme. `X-Forwarded-Proto`

```python
if 'X-Forwarded-Proto' in request.headers and request.headers['X-Forwarded-Proto'] == 'https':
# Do something when HTTPS is used
```

Popüler web çerçeveleri, `X-Forwarded-*` standart uygulama deseninizdeki bilgilere erişmenizi sağlar. [CodeIgniter'da](https://codeigniter.com/) [is_https()](https://github.com/bcit-ci/CodeIgniter/blob/master/system/core/Common.php#L338-L365) varsayılan olarak `X_FORWARDED_PROTO` değerini denetler.

## <a name="access-diagnostic-logs"></a>Tanılama günlüklerine erişim

[!INCLUDE [Access diagnostic logs](../../../includes/app-service-web-logs-access-no-h.md)]

## <a name="open-ssh-session-in-browser"></a>Tarayıcıda SSH oturumunu açma

[!INCLUDE [Open SSH session in browser](../../../includes/app-service-web-ssh-connect-builtin-no-h.md)]

## <a name="troubleshooting"></a>Sorun giderme

- **Kendi uygulama kodunuzu dağıttıktan sonra varsayılan uygulamayı görüyorsunuz.** Varsayılan uygulama, uygulama kodunuzu Uygulama Hizmeti'ne dağıtmadığınız veya Uygulama Hizmeti'nin uygulama kodunuzu bulamadığı ve bunun yerine varsayılan uygulamayı çalıştırdığı için görünür.
- App Service'i yeniden başlatın, 15-20 saniye bekleyin ve uygulamayı yeniden denetleyin.
- Windows tabanlı örnek yerine Linux için App Service’i kullandığınızdan emin olun. Azure CLI’de `<resource_group_name>` ve `<app_service_name>` hizmetini uygun bir şekilde değiştiren `az webapp show --resource-group <resource_group_name> --name <app_service_name> --query kind` komutunu çalıştırın. Çıktı olarak `app,linux` görünmelidir, aksi takdirde App Service’i yeniden oluşturun ve Linux’u seçin.
- SSH veya Kudu kullanarak doğrudan App Service'e bağlanın ve dosyalarınızın *site/wwwroot* dizininde bulunduğunu doğrulayın. Dosyalarınız orada değilse dağıtım işlemlerinizi gözden geçirin ve uygulamayı yeniden dağıtın.
- Dosyalarınız oradaysa App Service başlangıç dosyanızı tanımlayamamış olabilir. Uygulamanızın App Service'in [Django](#django-app) veya [Flask](#flask-app) için beklediği şekilde yapılandırılmış olduğundan emin olun veya [özel başlangıç komutu](#customize-startup-command) kullanın.
- **Tarayıcıda "Hizmet Kullanılamıyor" iletisini görüyorsunuz.** Bu durum, tarayıcının App Service'ten yanıt beklerken zaman aşımına uğradığını gösterir. Bunun nedeni App Service'in Gunicorn sunucusunu başlatmış olması ancak uygulama kodunu belirten bağımsız değişkenlerin hatalı olmasıdır.
- Özellikle App Service Planınızda en düşük fiyatlandırma katmanlarını kullanıyorsanız tarayıcıyı yenileyin. Ücretsiz katmanları kullandığınızda uygulamanın başlaması daha uzun sürebilir ve tarayıcıyı yenilediğinizde yanıt verebilir.
- Uygulamanızın App Service'in [Django](#django-app) veya [Flask](#flask-app) için beklediği şekilde yapılandırılmış olduğundan emin olun veya [özel başlangıç komutu](#customize-startup-command) kullanın.
- [Günlük akışına erişin.](#access-diagnostic-logs)

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [Öğretici: PostgreSQL ile Python uygulaması](tutorial-python-postgresql-app.md)

> [!div class="nextstepaction"]
> [Öğretici: Özel konteyner deposundan dağıtma](tutorial-custom-docker-image.md)

> [!div class="nextstepaction"]
> [Uygulama Hizmeti Linux SSS](app-service-linux-faq.md)
