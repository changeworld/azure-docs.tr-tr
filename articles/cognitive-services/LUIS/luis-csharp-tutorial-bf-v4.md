---
title: 'Öğretici: Dil Anlama Bot C# v4'
description: C# kullanarak, dil anlama (LUIS) ile tümleşik bir sohbet robotu oluşturun. Bot, Bot Framework sürüm 4 ve Azure Web uygulaması bot hizmeti yle oluşturulmuştur.
ms.topic: tutorial
ms.date: 02/03/2020
ms.openlocfilehash: 2381f4cba39f81ab593f3149a2708f442156f30d
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76988014"
---
# <a name="tutorial-use-a-web-app-bot-enabled-with-language-understanding-in-c"></a>Öğretici: C'de Dil Anlama özelliğine sahip bir Web App Bot'u kullanın #

Dil anlama (LUIS) ile entegre bir sohbet botu oluşturmak için C# kullanın. Bot, Azure Web [uygulaması bot](https://docs.microsoft.com/azure/bot-service/) kaynağı ve [Bot Framework sürümü](https://github.com/Microsoft/botbuilder-dotnet) V4 ile oluşturulmuştur.

**Bu öğreticide şunların nasıl yapıldığını öğrenirsiniz:**

> [!div class="checklist"]
> * Web uygulaması robotu oluşturma. Bu işlem sizin için yeni bir LUIS uygulaması oluşturur.
> * Web bot hizmeti tarafından oluşturulan bot projesini indirin
> * Robotu ve öykünücüyü bilgisayarınızda yerel olarak başlatma
> * Robotta konuşma sonuçlarını görüntüleme

## <a name="prerequisites"></a>Ön koşullar

* [Robot öykünücüsü](https://aka.ms/abs/build/emulatordownload)
* [Visual Studio](https://visualstudio.microsoft.com/downloads/)


## <a name="create-a-web-app-bot-resource"></a>Bir web uygulaması bot kaynağı oluşturma

1. [Azure portalda](https://portal.azure.com)**Yeni kaynak oluştur**'u seçin.

1. Arama kutusunda **Web Uygulaması Robotu**'nu arayın ve bunu seçin. **Oluştur'u**seçin.

1. **Robot Hizmeti**'nde gerekli bilgileri sağlayın:

    |Ayar|Amaç|Önerilen ayar|
    |--|--|--|
    |Robot adı|Kaynak adı|`luis-csharp-bot-` + `<your-name>`, örneğin `luis-csharp-bot-johnsmith`|
    |Abonelik|Robotun oluşturulacağı abonelik.|Birincil aboneliğiniz.
    |Kaynak grubu|Azure kaynaklarının mantıksal grubu|Bu robotla kullanılan tüm kaynakları depolamak için yeni bir grup oluşturun ve grubu `luis-csharp-bot-resource-group` olarak adlandırın.|
    |Konum|Azure bölgesi - LUIS yazma veya yayımlama bölgesi ile aynı olması gerekmez.|`westus`|
    |Fiyatlandırma katmanı|Hizmet isteği sınırları ve faturalandırma için kullanılır.|`F0` ücretsiz katmanı gösterir.
    |Uygulama adı|Ad, robotunuz buluta dağıtıldığında alt etki alanı olarak kullanılır (örneğin humanresourcesbot.azurewebsites.net).|`luis-csharp-bot-` + `<your-name>`, örneğin `luis-csharp-bot-johnsmith`|
    |Robot şablonu|Bot Framework ayarları - sonraki tabloya bakın|
    |LUIS Uygulaması konumu|LUIS kaynak bölgesi ile aynı olmalıdır|`westus`|
    |Uygulama hizmet planı/Konum|Sağlanan varsayılan değerden değiştirmeyin.|
    |Application Insights|Sağlanan varsayılan değerden değiştirmeyin.|
    |Microsoft Uygulama Kimliği ve parola|Sağlanan varsayılan değerden değiştirmeyin.|

1. Bot **şablonunda**aşağıdakileri seçin ve ardından aşağıdaki ayarların altındaki **Seç** düğmesini seçin:

    |Ayar|Amaç|Seçim|
    |--|--|--|
    |SDK sürümü|Robot Framework sürümü|**SDK v4**|
    |SDK dili|Robotun programlama dili|**C #**|
    |Bot|Robot türü|**Temel robot**|

1. **Oluştur'u**seçin. Robot hizmetini oluşturur ve Azure'a dağıtır. Bu işlemin bir parçası olarak `luis-csharp-bot-XXXX` adlı bir LUIS uygulaması oluşturulur. Bu ad , /Azure Bot Hizmeti uygulama adını temel alıntır.

    > [!div class="mx-imgBorder"]
    > [![Web uygulaması robotu oluşturma](./media/bfv4-csharp/create-web-app-service.png)](./media/bfv4-csharp/create-web-app-service.png#lightbox)

    Devam etmeden önce bot hizmeti oluşturulana kadar bekleyin.

## <a name="the-bot-has-a-language-understanding-model"></a>Botun Dil Anlama modeli vardır

Bot hizmeti oluşturma işlemi de niyet ve örnek söyleyiş ile yeni bir LUIS uygulaması oluşturur. Robot, yeni LUIS uygulamasına şu amaçlar için amaç eşlemesi sağlar:

|Temel robot LUIS amaçları|örnek konuşma|
|--|--|
|Kitap uçuşu|`Travel to Paris`|
|İptal|`bye`|
|GetWeather|`what's the weather like?`|
|None|Uygulamanın etki alanı dışındaki her şey.|

## <a name="test-the-bot-in-web-chat"></a>Web Sohbet'te botu test edin

1. Yeni bot için Azure portalında yken, **Web Sohbetinde Test'i**seçin.
1. **İletimetin kutusunu yazın,** metni `Book a flight from Seattle to Berlin tomorrow`girin. Bot, bir uçuş rezervasyonu yapmak istediğinizi doğrulayan bir yanıt verir.

    ![Azure portalının ekran görüntüsüne 'merhaba' metnini girin.](./media/bfv4-nodejs/ask-bot-question-in-portal-test-in-web-chat.png)

    Botunuzu hızlı bir şekilde test etmek için test işlevini kullanabilirsiniz. Hata ayıklama da dahil olmak üzere daha eksiksiz test için, bot kodunu indirin ve Visual Studio'yu kullanın.

## <a name="download-the-web-app-bot-source-code"></a>Web uygulaması bot kaynak kodunu indirin
Web uygulaması robot kodunu geliştirmek için, yerel bilgisayarınızda kodu indirin ve kullanın.

1. Azure portalda, **Robot yönetimi** bölümünden **Derle**'yi seçin.

1. **Robot kaynak kodunu indir**'i seçin.

    [![Temel bot için web uygulaması bot kaynak kodu indirin](../../../includes/media/cognitive-services-luis/bfv4/download-code.png)](../../../includes/media/cognitive-services-luis/bfv4/download-code.png#lightbox)

1. Açılan iletişim kutusu sorduğunda **Yes** **indirilen zip dosyasına uygulama ayarlarını ekle?**

1. Kaynak kodu .zip dosyasına sıkıştırılmışsa, bir iletide kodu indirme bağlantısı sağlanır. Bağlantıyı seçin.

1. Bu .zip dosyasını yerel bilgisayarınıza kaydedin ve dosyaları ayıklayın. Visual Studio ile projeyi açın.

## <a name="review-code-to-send-utterance-to-luis-and-get-response"></a>LUIS'e söyleyiş göndermek ve yanıt almak için kodu gözden geçirme

1. Kullanıcı sözcüklerini LUIS tahmin bitiş noktasına göndermek için **FlightBookingRecognizer.cs** dosyasını açın. Robota girilen kullanıcı konuşmasının LUIS'e gönderildiği yer budur. LUIS'in yanıtı **RecognizeAsync** yönteminden döndürülür.

    ```csharp
    using System.Threading;
    using System.Threading.Tasks;
    using Microsoft.Bot.Builder;
    using Microsoft.Bot.Builder.AI.Luis;
    using Microsoft.Extensions.Configuration;

    namespace Microsoft.BotBuilderSamples
    {
        public class FlightBookingRecognizer : IRecognizer
        {
            private readonly LuisRecognizer _recognizer;

            public FlightBookingRecognizer(IConfiguration configuration)
            {
                var luisIsConfigured = !string.IsNullOrEmpty(configuration["LuisAppId"]) && !string.IsNullOrEmpty(configuration["LuisAPIKey"]) && !string.IsNullOrEmpty(configuration["LuisAPIHostName"]);
                if (luisIsConfigured)
                {
                    var luisApplication = new LuisApplication(
                        configuration["LuisAppId"],
                        configuration["LuisAPIKey"],
                        "https://" + configuration["LuisAPIHostName"]);

                    _recognizer = new LuisRecognizer(luisApplication);
                }
            }

            // Returns true if luis is configured in the appsettings.json and initialized.
            public virtual bool IsConfigured => _recognizer != null;

            public virtual async Task<RecognizerResult> RecognizeAsync(ITurnContext turnContext, CancellationToken cancellationToken)
                => await _recognizer.RecognizeAsync(turnContext, cancellationToken);

            public virtual async Task<T> RecognizeAsync<T>(ITurnContext turnContext, CancellationToken cancellationToken)
                where T : IRecognizerConvert, new()
                => await _recognizer.RecognizeAsync<T>(turnContext, cancellationToken);
        }
    }
    ````

1. Açık **Diyaloglar -> MainDialog.cs** söyleyişi yakalar ve actStep yöntemiyle executeLuisQuery'ye gönderir.

    ```csharp
    // Copyright (c) Microsoft Corporation. All rights reserved.
    // Licensed under the MIT License.

    using System;
    using System.Collections.Generic;
    using System.Linq;
    using System.Threading;
    using System.Threading.Tasks;
    using Microsoft.Bot.Builder;
    using Microsoft.Bot.Builder.Dialogs;
    using Microsoft.Bot.Schema;
    using Microsoft.Extensions.Logging;
    using Microsoft.Recognizers.Text.DataTypes.TimexExpression;

    namespace Microsoft.BotBuilderSamples.Dialogs
    {
        public class MainDialog : ComponentDialog
        {
            private readonly FlightBookingRecognizer _luisRecognizer;
            protected readonly ILogger Logger;

            // Dependency injection uses this constructor to instantiate MainDialog
            public MainDialog(FlightBookingRecognizer luisRecognizer, BookingDialog bookingDialog, ILogger<MainDialog> logger)
                : base(nameof(MainDialog))
            {
                _luisRecognizer = luisRecognizer;
                Logger = logger;

                AddDialog(new TextPrompt(nameof(TextPrompt)));
                AddDialog(bookingDialog);
                AddDialog(new WaterfallDialog(nameof(WaterfallDialog), new WaterfallStep[]
                {
                    IntroStepAsync,
                    ActStepAsync,
                    FinalStepAsync,
                }));

                // The initial child Dialog to run.
                InitialDialogId = nameof(WaterfallDialog);
            }

            private async Task<DialogTurnResult> IntroStepAsync(WaterfallStepContext stepContext, CancellationToken cancellationToken)
            {
                if (!_luisRecognizer.IsConfigured)
                {
                    await stepContext.Context.SendActivityAsync(
                        MessageFactory.Text("NOTE: LUIS is not configured. To enable all capabilities, add 'LuisAppId', 'LuisAPIKey' and 'LuisAPIHostName' to the appsettings.json file.", inputHint: InputHints.IgnoringInput), cancellationToken);

                    return await stepContext.NextAsync(null, cancellationToken);
                }

                // Use the text provided in FinalStepAsync or the default if it is the first time.
                var messageText = stepContext.Options?.ToString() ?? "What can I help you with today?\nSay something like \"Book a flight from Paris to Berlin on March 22, 2020\"";
                var promptMessage = MessageFactory.Text(messageText, messageText, InputHints.ExpectingInput);
                return await stepContext.PromptAsync(nameof(TextPrompt), new PromptOptions { Prompt = promptMessage }, cancellationToken);
            }

            private async Task<DialogTurnResult> ActStepAsync(WaterfallStepContext stepContext, CancellationToken cancellationToken)
            {
                if (!_luisRecognizer.IsConfigured)
                {
                    // LUIS is not configured, we just run the BookingDialog path with an empty BookingDetailsInstance.
                    return await stepContext.BeginDialogAsync(nameof(BookingDialog), new BookingDetails(), cancellationToken);
                }

                // Call LUIS and gather any potential booking details. (Note the TurnContext has the response to the prompt.)
                var luisResult = await _luisRecognizer.RecognizeAsync<FlightBooking>(stepContext.Context, cancellationToken);
                switch (luisResult.TopIntent().intent)
                {
                    case FlightBooking.Intent.BookFlight:
                        await ShowWarningForUnsupportedCities(stepContext.Context, luisResult, cancellationToken);

                        // Initialize BookingDetails with any entities we may have found in the response.
                        var bookingDetails = new BookingDetails()
                        {
                            // Get destination and origin from the composite entities arrays.
                            Destination = luisResult.ToEntities.Airport,
                            Origin = luisResult.FromEntities.Airport,
                            TravelDate = luisResult.TravelDate,
                        };

                        // Run the BookingDialog giving it whatever details we have from the LUIS call, it will fill out the remainder.
                        return await stepContext.BeginDialogAsync(nameof(BookingDialog), bookingDetails, cancellationToken);

                    case FlightBooking.Intent.GetWeather:
                        // We haven't implemented the GetWeatherDialog so we just display a TODO message.
                        var getWeatherMessageText = "TODO: get weather flow here";
                        var getWeatherMessage = MessageFactory.Text(getWeatherMessageText, getWeatherMessageText, InputHints.IgnoringInput);
                        await stepContext.Context.SendActivityAsync(getWeatherMessage, cancellationToken);
                        break;

                    default:
                        // Catch all for unhandled intents
                        var didntUnderstandMessageText = $"Sorry, I didn't get that. Please try asking in a different way (intent was {luisResult.TopIntent().intent})";
                        var didntUnderstandMessage = MessageFactory.Text(didntUnderstandMessageText, didntUnderstandMessageText, InputHints.IgnoringInput);
                        await stepContext.Context.SendActivityAsync(didntUnderstandMessage, cancellationToken);
                        break;
                }

                return await stepContext.NextAsync(null, cancellationToken);
            }

            // Shows a warning if the requested From or To cities are recognized as entities but they are not in the Airport entity list.
            // In some cases LUIS will recognize the From and To composite entities as a valid cities but the From and To Airport values
            // will be empty if those entity values can't be mapped to a canonical item in the Airport.
            private static async Task ShowWarningForUnsupportedCities(ITurnContext context, FlightBooking luisResult, CancellationToken cancellationToken)
            {
                var unsupportedCities = new List<string>();

                var fromEntities = luisResult.FromEntities;
                if (!string.IsNullOrEmpty(fromEntities.From) && string.IsNullOrEmpty(fromEntities.Airport))
                {
                    unsupportedCities.Add(fromEntities.From);
                }

                var toEntities = luisResult.ToEntities;
                if (!string.IsNullOrEmpty(toEntities.To) && string.IsNullOrEmpty(toEntities.Airport))
                {
                    unsupportedCities.Add(toEntities.To);
                }

                if (unsupportedCities.Any())
                {
                    var messageText = $"Sorry but the following airports are not supported: {string.Join(',', unsupportedCities)}";
                    var message = MessageFactory.Text(messageText, messageText, InputHints.IgnoringInput);
                    await context.SendActivityAsync(message, cancellationToken);
                }
            }

            private async Task<DialogTurnResult> FinalStepAsync(WaterfallStepContext stepContext, CancellationToken cancellationToken)
            {
                // If the child dialog ("BookingDialog") was cancelled, the user failed to confirm or if the intent wasn't BookFlight
                // the Result here will be null.
                if (stepContext.Result is BookingDetails result)
                {
                    // Now we have all the booking details call the booking service.

                    // If the call to the booking service was successful tell the user.

                    var timeProperty = new TimexProperty(result.TravelDate);
                    var travelDateMsg = timeProperty.ToNaturalLanguage(DateTime.Now);
                    var messageText = $"I have you booked to {result.Destination} from {result.Origin} on {travelDateMsg}";
                    var message = MessageFactory.Text(messageText, messageText, InputHints.IgnoringInput);
                    await stepContext.Context.SendActivityAsync(message, cancellationToken);
                }

                // Restart the main dialog with a different message the second time around
                var promptMessage = "What else can I do for you?";
                return await stepContext.ReplaceDialogAsync(InitialDialogId, promptMessage, cancellationToken);
            }
        }
    }
    ```

## <a name="start-the-bot-code-in-visual-studio"></a>Visual Studio'da bot kodunu başlatın

Visual Studio 2019'da botu başlatın. Web uygulaması robotunun `http://localhost:3978/` adresindeki sitesi ile bir tarayıcı penceresi açılır. Bir ana sayfa, botunuzun izleyişleriyle birlikte görüntülenir.

![Bir ana sayfa, botunuzun izleyişleriyle birlikte görüntülenir.](./media/bfv4-csharp/running-bot-web-home-page-success.png)

## <a name="use-the-bot-emulator-to-test-the-bot"></a>Bot test etmek için bot emülatörü kullanın

1. Bot Emülatörü başlatın ve **Açık Bot**seçin.
1. Bir **bot** açılır pencere aç iletişim kutusunda, bot URL'nizi girin, örneğin. `http://localhost:3978/api/messages` `/api/messages` Rota bot için web adresidir.
1. İndirdiğiniz bot kodunun kökündeki **appsettings.json** dosyasında bulunan **Microsoft App ID** ve Microsoft App **parolasını**girin.


1. Bot emülatörolarak, `Book a flight from Seattle to Berlin tomorrow` bir önceki bölümde **Web Sohbet Testi'nde** aldığınız temel bot için aynı yanıtı girin ve alın.

    [![Emülatörde temel bot yanıtı](./media/bfv4-nodejs/ask-bot-emulator-a-question-and-get-response.png)](./media/bfv4-nodejs/ask-bot-emulator-a-question-and-get-response.png#lightbox)

1. **Evet'i**seçin. Bot eylemlerinin bir özeti ile yanıt verir.
1. Bot emülatörü günlüğünden, içeren `Luis Trace`satırı seçin. Bu, söyleyiş niyeti ve varlıkları için LUIS gelen JSON yanıtı görüntüler.

    [![Emülatörde temel bot yanıtı](./media/bfv4-nodejs/ask-luis-book-flight-question-get-json-response-in-bot-emulator.png)](./media/bfv4-nodejs/ask-luis-book-flight-question-get-json-response-in-bot-emulator.png#lightbox)


[!INCLUDE [Bot Information](../../../includes/cognitive-services-qnamaker-luis-bot-info.md)]


## <a name="next-steps"></a>Sonraki adımlar

Konuşma botları ile daha fazla [örnek](https://github.com/microsoft/botframework-solutions) görün.

> [!div class="nextstepaction"]
> [Özel bir konu etki alanıyla Dil Anlama uygulaması oluşturma](luis-quickstart-intents-only.md)
