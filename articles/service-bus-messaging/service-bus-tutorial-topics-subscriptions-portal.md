---
title: Azure portalLarını ve konuları/abonelikleri kullanarak envanteri güncelleştirme
description: Bu öğreticide bir konu başlığı ve abonelikten ileti gönderip almayı ve .NET ile filtre kuralları ekleyip kullanmayı öğreneceksiniz
services: service-bus-messaging
author: spelluru
manager: timlt
ms.author: spelluru
ms.date: 01/21/2020
ms.topic: tutorial
ms.service: service-bus-messaging
ms.custom: mvc
ms.openlocfilehash: b6a3f38e0b83dc927a225c630b8178be1dc539ce
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/24/2020
ms.locfileid: "76310127"
---
# <a name="tutorial-update-inventory-using-azure-portal-and-topicssubscriptions"></a>Öğretici: Azure portalı ve konuları/abonelikleri kullanarak envanter güncelleştirme

Microsoft Azure Service Bus, uygulamalar ve hizmetler arasında bilgi gönderen çok kiracılı bir bulut mesajlaşma hizmetidir. Zaman uyumsuz işlemler esnek ve aracılı mesajlaşmanın yanı sıra ilk giren ilk çıkar (FIFO) yöntemiyle yapılandırılmış mesajlaşma ve yayımlama/abonelik olanakları da sunar. Bu öğretici, bir perakende stok senaryosunda, Azure portal ve .NET kullanan yayımlama/abonelik kanallarıyla Service Bus konu başlıklarını ve abonelikleri kullanmayı göstermektedir.

Bu öğreticide şunların nasıl yapıldığını öğrenirsiniz:
> [!div class="checklist"]
> * Azure portalı kullanarak bir Service Bus konu başlığı ve bunun için bir veya daha fazla abonelik oluşturma
> * .NET kodu kullanarak konu filtreleri ekleme
> * Farklı içerikle iki ileti oluşturma
> * İletileri gönderme ve bunların beklenen aboneliklere vardığını doğrulama
> * Aboneliklerden ileti alma

Bu senaryonun bir örneği, birden çok perakende mağazası için stok sınıflama güncelleştirmesidir. Bu senaryoda, her mağaza veya mağaza grubu, sınıflamalarını güncelleştirmeye yönelik iletiler alır. Bu öğretici, bu senaryonun abonelikler ve filtreler kullanılarak uygulanmasını göstermektedir. İlk olarak önce 3 aboneliği olan bir konu başlığı oluşturun, bazı kurallar ve filtreler ekleyin ve ardından konu başlıkları ve aboneliklerden iletiler gönderip alın.

![konu başlığı](./media/service-bus-tutorial-topics-subscriptions-portal/about-service-bus-topic.png)

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap][] oluşturabilirsiniz.

## <a name="prerequisites"></a>Ön koşullar

Bu öğreticiyi tamamlamak için şunları yüklediğinizden emin olun:

- [Visual Studio 2017 Güncelleştirme 3 (sürüm 15.3, 26730.01)](https://www.visualstudio.com/vs) veya sonraki sürümler.
- [NET Core SDK](https://www.microsoft.com/net/download/windows), sürüm 2.0 veya sonraki sürümler.

## <a name="service-bus-topics-and-subscriptions"></a>Service Bus konu başlıkları ve abonelikler

Her [konu başlığı aboneliği](service-bus-messaging-overview.md#topics) her iletinin bir kopyasını alabilir. Konular, protokol ve anlam açılarından Service Bus kuyrukları ile tam olarak uyumludur. Service Bus konu başlıkları, filtreleme koşullarını ve ileti özelliklerini belirleyen veya değiştiren isteğe bağlı eylemleri olan geniş bir seçim kuralı yelpazesini destekler. Bir kural eşleştiğinde bir ileti oluşturulur. Kurallar, filtreler ve eylemler hakkında daha fazla bilgi edinmek için bu [bağlantıyı](topic-filters.md) izleyin.

[!INCLUDE [service-bus-create-namespace-portal](../../includes/service-bus-create-namespace-portal.md)]

[!INCLUDE [service-bus-create-topics-three-subscriptions-portal](../../includes/service-bus-create-topics-three-subscriptions-portal.md)]



## <a name="create-filter-rules-on-subscriptions"></a>Aboneliklerde filtre kuralları oluşturma

Ad alanı ve konu başlıkları/abonelikler sağlandıktan ve gerekli kimlik bilgilerini edindikten sonra, aboneliklerde filtre kuralları oluşturmaya ve ileti gönderip almaya hazır olursunuz. [Bu GitHub örnek klasöründeki](https://github.com/Azure/azure-service-bus/tree/master/samples/Java/azure-servicebus/TopicFilters) kodu inceleyebilirsiniz.

### <a name="send-and-receive-messages"></a>İleti alma ve gönderme

Kodu çalıştırmak için aşağıdakileri yapın:

1. Bir komut veya PowerShell komut isteminde aşağıdaki komutu göndererek [Service Bus GitHub deposunu](https://github.com/Azure/azure-service-bus/) kopyalayın:

   ```shell
   git clone https://github.com/Azure/azure-service-bus.git
   ```

2. Örnek `azure-service-bus\samples\DotNet\GettingStarted\BasicSendReceiveTutorialwithFilters` klasörüne gidin.

3. Bu öğreticinin Yönetim kimlik bilgilerini edinme bölümünde Not Defteri'ne kopyaladığınız bağlantı dizesini edinin. Ayrıca önceki bölümde oluşturduğunuz konu adı da gerekir.

4. Komut isteminde aşağıdaki komutu yazın:

   ```shell
   dotnet build
   ```

5. `BasicSendReceiveTutorialwithFilters\bin\Debug\netcoreapp2.0` klasörüne gidin.

6. Programı çalıştırmak için aşağıdaki komutu yazın. `myConnectionString` yerine daha önce edindiğiniz değeri ve `myTopicName` yerine oluşturduğunuz konu başlığının adını koymayı unutmayın:

   ```shell
   dotnet BasicSendReceiveTutorialwithFilters.dll -ConnectionString "myConnectionString" -TopicName "myTopicName"
   ``` 
7. İlk önce filtre oluşturmayı seçmek için konsoldaki yönergeleri izleyin. Varsayılan filtreleri kaldırmak filtre oluşturmanın bir parçasıdır. PowerShell veya CLI kullandığınızda varsayılan filtreyi kaldırmanız gerekmez, ancak kodda bunu yaparsanız bunları kaldırmanız gerekir. 1 ve 3 konsol komutları, daha önce oluşturduğunuz aboneliklerdeki filtreleri yönetmenize yardımcı olur:

   - Varsayılan filtreleri kaldırmak için 1'i yürütün.
   - Kendi filtrelerini eklemek için 2'yi yürütün.
   - İsteğe bağlı olarak kendi filtrelerinizi kaldırmak için 3'ü yürütün. Bunun varsayılan filtreleri yeniden oluşturmayacağını unutmayın.

     ![Gösterilen 2'nin çıktısıdır](./media/service-bus-tutorial-topics-subscriptions-portal/create-rules.png)

8. Filtre oluşturulduktan sonra ileti gönderebilirsiniz. 4'e basın ve konu başlığına gönderilen 10 iletiyi gözlemleyin:

    ![Çıktı gönderme](./media/service-bus-tutorial-topics-subscriptions-portal/send-output.png)

9. 5'e basın ve alınan iletileri gözlemleyin. 10 ileti almadıysanız, menüyü görüntülemek için "m" tuşuna basın ve ardından 5'e yeniden basın.

    ![Çıktı alma](./media/service-bus-tutorial-topics-subscriptions-portal/receive-output.png)

## <a name="clean-up-resources"></a>Kaynakları temizleme

Artık gerekli olmadığında, ad alanını ve kuyruğu silin. Bunu yapmak için portalda bu kaynakları seçin ve **Sil**'e tıklayın.

## <a name="understand-the-sample-code"></a>Örnek kodu anlama

Bu bölümde, örnek kodun yaptıkları hakkında daha fazla ayrıntı bulunmaktadır.

### <a name="get-connection-string-and-topic"></a>Bağlantı dizesini ve konu başlığını alma

Kod öncelikle programın geri kalanını yürüten bir dizi değişken bildirir.

```csharp
string ServiceBusConnectionString;
string TopicName;

static string[] Subscriptions = { "S1", "S2", "S3" };
static IDictionary<string, string[]> SubscriptionFilters = new Dictionary<string, string[]> {
    { "S1", new[] { "StoreId IN('Store1', 'Store2', 'Store3')", "StoreId = 'Store4'"} },
    { "S2", new[] { "sys.To IN ('Store5','Store6','Store7') OR StoreId = 'Store8'" } },
    { "S3", new[] { "sys.To NOT IN ('Store1','Store2','Store3','Store4','Store5','Store6','Store7','Store8') OR StoreId NOT IN ('Store1','Store2','Store3','Store4','Store5','Store6','Store7','Store8')" } }
};
// You can have only have one action per rule and this sample code supports only one action for the first filter, which is used to create the first rule. 
static IDictionary<string, string> SubscriptionAction = new Dictionary<string, string> {
    { "S1", "" },
    { "S2", "" },
    { "S3", "SET sys.Label = 'SalesEvent'"  }
};
static string[] Store = { "Store1", "Store2", "Store3", "Store4", "Store5", "Store6", "Store7", "Store8", "Store9", "Store10" };
static string SysField = "sys.To";
static string CustomField = "StoreId";
static int NrOfMessagesPerStore = 1; // Send at least 1.
```

Bağlantı dizesi ve konu başlığı adı gösterildiği gibi komut satırı parametreleri aracılığıyla geçirilir ve ardından `Main()` yönteminde okunur:

```csharp
static void Main(string[] args)
{
    string ServiceBusConnectionString = "";
    string TopicName = "";

    for (int i = 0; i < args.Length; i++)
    {
        if (args[i] == "-ConnectionString")
        {
            Console.WriteLine($"ConnectionString: {args[i + 1]}");
            ServiceBusConnectionString = args[i + 1]; // Alternatively enter your connection string here.
        }
        else if (args[i] == "-TopicName")
        {
            Console.WriteLine($"TopicName: {args[i + 1]}");
            TopicName = args[i + 1]; // Alternatively enter your queue name here.
        }
    }

    if (ServiceBusConnectionString != "" && TopicName != "")
    {
        Program P = StartProgram(ServiceBusConnectionString, TopicName);
        P.PresentMenu().GetAwaiter().GetResult();
    }
    else
    {
        Console.WriteLine("Specify -Connectionstring and -TopicName to execute the example.");
        Console.ReadKey();
    }
}
```

### <a name="remove-default-filters"></a>Varsayılan filtreleri kaldırma

Bir abonelik oluşturduğunuzda, Service Bus abonelik başına varsayılan birer filtre oluşturur. Bu filtre, konu başlığına gönderilen her iletiyi almayı etkinleştirir. Özel filtreler kullanmak istiyorsanız, aşağıdaki kodda gösterildiği gibi varsayılan filtreyi kaldırabilirsiniz:

```csharp
private async Task RemoveDefaultFilters()
{
    Console.WriteLine($"Starting to remove default filters.");

    try
    {
        foreach (var subscription in Subscriptions)
        {
            SubscriptionClient s = new SubscriptionClient(ServiceBusConnectionString, TopicName, subscription);
            await s.RemoveRuleAsync(RuleDescription.DefaultRuleName);
            Console.WriteLine($"Default filter for {subscription} has been removed.");
            await s.CloseAsync();
        }

        Console.WriteLine("All default Rules have been removed.\n");
    }
    catch (Exception ex)
    {
        Console.WriteLine(ex.ToString());
    }

    await PresentMenu();
}
```

### <a name="create-filters"></a>Filtre oluşturma

Aşağıdaki kod, bu öğreticide tanımlanan özel filtreleri eklemektedir:

```csharp
private async Task CreateCustomFilters()
{
    try
    {
        for (int i = 0; i < Subscriptions.Length; i++)
        {
            SubscriptionClient s = new SubscriptionClient(ServiceBusConnectionString, TopicName, Subscriptions[i]);
            string[] filters = SubscriptionFilters[Subscriptions[i]];
            if (filters[0] != "")
            {
                int count = 0;
                foreach (var myFilter in filters)
                {
                    count++;

                    string action = SubscriptionAction[Subscriptions[i]];
                    if (action != "")
                    {
                        await s.AddRuleAsync(new RuleDescription
                        {
                            Filter = new SqlFilter(myFilter),
                            Action = new SqlRuleAction(action),
                            Name = $"MyRule{count}"
                        });
                    }
                    else
                    {
                        await s.AddRuleAsync(new RuleDescription
                        {
                            Filter = new SqlFilter(myFilter),
                            Name = $"MyRule{count}"
                        });
                    }
                }
            }

            Console.WriteLine($"Filters and actions for {Subscriptions[i]} have been created.");
        }

        Console.WriteLine("All filters and actions have been created.\n");
    }
    catch (Exception ex)
    {
        Console.WriteLine(ex.ToString());
    }

    await PresentMenu();
}
```

### <a name="remove-your-custom-created-filters"></a>Özel oluşturulan filtrelerinizi kaldırma

Aboneliğinizdeki tüm filtreleri kaldırmak istiyorsanız, aşağıdaki kod bunun nasıl yapılacağını göstermektedir:

```csharp
private async Task CleanUpCustomFilters()
{
    foreach (var subscription in Subscriptions)
    {
        try
        {
            SubscriptionClient s = new SubscriptionClient(ServiceBusConnectionString, TopicName, subscription);
            IEnumerable<RuleDescription> rules = await s.GetRulesAsync();
            foreach (RuleDescription r in rules)
            {
                await s.RemoveRuleAsync(r.Name);
                Console.WriteLine($"Rule {r.Name} has been removed.");
            }
            await s.CloseAsync();
        }
        catch (Exception ex)
        {
            Console.WriteLine(ex.ToString());
        }
    }
    Console.WriteLine("All default filters have been removed.\n");

    await PresentMenu();
}
```

### <a name="send-messages"></a>İleti gönderme

Bir konu başlığına ileti göndermek bir kuyruğa ileti göndermeye benzer. Bu örnek, bir görev listesi ve zaman uyumsuz işleme kullanarak ileti göndermeyi göstermektedir:

```csharp
public async Task SendMessages()
{
    try
    {
        TopicClient tc = new TopicClient(ServiceBusConnectionString, TopicName);

        var taskList = new List<Task>();
        for (int i = 0; i < Store.Length; i++)
        {
            taskList.Add(SendItems(tc, Store[i]));
        }

        await Task.WhenAll(taskList);
        await tc.CloseAsync();
    }
    catch (Exception ex)
    {
        Console.WriteLine(ex.ToString());
    }
    Console.WriteLine("\nAll messages sent.\n");
}

private async Task SendItems(TopicClient tc, string store)
{
    for (int i = 0; i < NrOfMessagesPerStore; i++)
    {
        Random r = new Random();
        Item item = new Item(r.Next(5), r.Next(5), r.Next(5));

        // Note the extension class which is serializing an deserializing messages
        Message message = item.AsMessage();
        message.To = store;
        message.UserProperties.Add("StoreId", store);
        message.UserProperties.Add("Price", item.getPrice().ToString());
        message.UserProperties.Add("Color", item.getColor());
        message.UserProperties.Add("Category", item.getItemCategory());

        await tc.SendAsync(message);
        Console.WriteLine($"Sent item to Store {store}. Price={item.getPrice()}, Color={item.getColor()}, Category={item.getItemCategory()}"); ;
    }
}
```

### <a name="receive-messages"></a>İleti alma

İletiler de bir görev listesi aracılığıyla alınmakta ve kod toplu işlem kullanmaktadır. İletileri toplu işlem kullanarak gönderip alabilirsiniz, ancak bu örnek yalnızca toplu almayı göstermektedir. Gerçekte, döngüyü yarıda kesmez, döngüye devam edip bir dakika gibi daha yüksek bir zaman dilimi belirlersiniz. Aracıya gönderilen alma çağrısı bu süre boyunca açık tutulur ve ileti gelirse bunlar hemen döndürülüp yeni bir çağrı çıkarılır. Bu kavrama *uzun süreli yoklama* denir. [Hızlı başlangıçta](service-bus-quickstart-portal.md) görebileceğiniz alma pompasını ve depodaki diğer birkaç örneği kullanmak daha tipik bir seçenektir.

```csharp
public async Task Receive()
{
    var taskList = new List<Task>();
    for (var i = 0; i < Subscriptions.Length; i++)
    {
        taskList.Add(this.ReceiveMessages(Subscriptions[i]));
    }

    await Task.WhenAll(taskList);
}

private async Task ReceiveMessages(string subscription)
{
    var entityPath = EntityNameHelper.FormatSubscriptionPath(TopicName, subscription);
    var receiver = new MessageReceiver(ServiceBusConnectionString, entityPath, ReceiveMode.PeekLock, RetryPolicy.Default, 100);

    while (true)
    {
        try
        {
            IList<Message> messages = await receiver.ReceiveAsync(10, TimeSpan.FromSeconds(2));
            if (messages.Any())
            {
                foreach (var message in messages)
                {
                    lock (Console.Out)
                    {
                        Item item = message.As<Item>();
                        IDictionary<string, object> myUserProperties = message.UserProperties;
                        Console.WriteLine($"StoreId={myUserProperties["StoreId"]}");

                        if (message.Label != null)
                        {
                            Console.WriteLine($"Label={message.Label}");
                        }

                        Console.WriteLine(
                            $"Item data: Price={item.getPrice()}, Color={item.getColor()}, Category={item.getItemCategory()}");
                    }

                    await receiver.CompleteAsync(message.SystemProperties.LockToken);
                }
            }
            else
            {
                break;
            }
        }
        catch (Exception ex)
        {
            Console.WriteLine(ex.ToString());
        }
    }

    await receiver.CloseAsync();
}
```

> [!NOTE]
> [Servis](https://github.com/paolosalvatori/ServiceBusExplorer/)Veri Servisi Explorer ile Servis Veri Servisi kaynaklarını yönetebilirsiniz. Service Bus Explorer, kullanıcıların bir Service Bus ad alanına bağlanmasına ve ileti varlıklarını kolay bir şekilde yönetmesine olanak tanır. Araç, alma/dışa aktarma işlevselliği veya konuyu, kuyrukları, abonelikleri, geçiş hizmetlerini, bildirim hub'larını ve olay hub'larını test etme olanağı gibi gelişmiş özellikler sağlar. 

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide Azure portalı kullanarak kaynaklar sağladınız, sonra bir Service Bus konu başlığından ve bunun aboneliklerinden iletiler gönderip aldınız. Şunları öğrendiniz:

> [!div class="checklist"]
> * Azure portalı kullanarak bir Service Bus konu başlığı ve bunun için bir veya daha fazla abonelik oluşturma
> * .NET kodu kullanarak konu filtreleri ekleme
> * Farklı içerikle iki ileti oluşturma
> * İletileri gönderme ve bunların beklenen aboneliklere vardığını doğrulama
> * Aboneliklerden ileti alma

Daha fazla ileti gönderme ve alma örneği için [GitHub’daki Service Bus örnekleri](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/GettingStarted) ile çalışmaya başlayın.

Service Bus’ın yayımlama/abone olma özelliklerini kullanma hakkında daha fazla bilgi edinmek için bir sonraki öğreticiye ilerleyin.

> [!div class="nextstepaction"]
> [PowerShell ve konular/abonelikler kullanarak stok güncelleştirme](service-bus-tutorial-topics-subscriptions-powershell.md)

[ücretsiz bir hesap]: https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio
[fully qualified domain name]: https://wikipedia.org/wiki/Fully_qualified_domain_name
[Azure portal]: https://portal.azure.com/

[connection-string]: ./media/service-bus-tutorial-topics-subscriptions-portal/connection-string.png
[service-bus-flow]: ./media/service-bus-tutorial-topics-subscriptions-portal/about-service-bus-topic.png
