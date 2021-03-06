---
title: 'Quickstart: Konuşma SDK ile Özel Komutlar uygulamasına bağlanın - Konuşma hizmeti'
titleSuffix: Azure Cognitive Services
description: Bu makalede, Özel Komutlar ile bir Konuşma SDK istemci uygulaması oluşturacaktır.
services: cognitive-services
author: don-d-kim
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 12/09/2019
ms.author: donkim
ms.openlocfilehash: 9e324af0b90f595b5b7af2a417a562efb193d854
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/28/2020
ms.locfileid: "76156786"
---
# <a name="quickstart-connect-to-a-custom-commands-application-with-the-speech-sdk-preview"></a>Quickstart: Konuşma SDK (Önizleme) ile Özel Komutlar uygulamasına bağlanın

Barındırılan özel komutlar uygulaması oluşturduktan sonra, istemci aygıtından konuşmaya başlayabilirsiniz.

Bu makalede, şunları yapacaksınız:

- Özel Komutlar uygulaması yayımlayın ve bir uygulama tanımlayıcısı (Uygulama Kimliği) alın
- Özel Komutlar uygulamanızla konuşmanızı sağlamak için Konuşma SDK'sını kullanarak bir istemci uygulaması oluşturun

## <a name="prerequisites"></a>Ön koşullar

Bu makaleyi tamamlamak için Özel Komutlar uygulaması gereklidir. Henüz bir Özel Komut uygulaması oluşturmadıysanız, bunu önceki hızlı başlangıçlarda yapabilirsiniz:

- [Quickstart: Özel Komut Oluşturma (Önizleme)](./quickstart-custom-speech-commands-create-new.md)
- [Quickstart: Parametreler (Önizleme) ile Özel Komut oluşturma](./quickstart-custom-speech-commands-create-parameters.md)

Ayrıca şunları yapmanız gerekir:

- [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/)
- Konuşma Hizmetleri için Azure abonelik anahtarı. [Ücretsiz bir tane alın](get-started.md) veya [Azure portalında](https://portal.azure.com) oluşturun

## <a name="optional-get-started-fast"></a>İsteğe bağlı: Hızlı başlayın

Bu hızlı başlangıç, adım adım, Özel Komutlar uygulamanıza bağlanmak için bir istemci uygulaması nasıl yapılacağını açıklar. Sağa dalmayı tercih ederseniz, bu hızlı başlatmada kullanılan tam, derlenmeye hazır kaynak kodu klasörün `quickstart` altındaki Konuşma [SDK Örnekleri'nde](https://aka.ms/csspeech/samples) kullanılabilir.

## <a name="step-1-publish-custom-commands-application"></a>Adım 1: Özel Komutlar uygulamasını yayımla

1. Daha [önce oluşturulmuş Özel Komutlar uygulamanızı](./quickstart-custom-speech-commands-create-new.md) açın ve **Yayımla'yı** seçin

   > [!div class="mx-imgBorder"]
   > ![Uygulama yayımlama](media/custom-speech-commands/fulfill-sdk-publish-application.png)

1. Daha sonra kullanmak üzere yayımlama bildiriminden Uygulama Kimliğini kopyalama

## <a name="step-2-create-a-visual-studio-project"></a>Adım 2: Görsel Stüdyo projesi oluşturma

[!INCLUDE [](../../../includes/cognitive-services-speech-service-quickstart-uwp-create-proj.md)]

## <a name="step-3-add-sample-code"></a>Adım 3: Örnek kod ekleme

Bu adımda, uygulamanın kullanıcı arabirimini tanımlayan XAML kodunu ve C# kodunun arkasındaki uygulamayı ekliyoruz.

### <a name="xaml-code"></a>XAML kodu

XAML kodunu ekleyerek uygulamanın kullanıcı arabirimini oluşturun.

1. **Çözüm Gezgini'nde**, açık`MainPage.xaml`

1. Tasarımcının XAML görünümünde, tüm içeriği aşağıdaki kod parçacıklarıyla değiştirin:

   ```xml
   <Page
       x:Class="helloworld.MainPage"
       xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation"
       xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
       xmlns:local="using:helloworld"
       xmlns:d="http://schemas.microsoft.com/expression/blend/2008"
       xmlns:mc="http://schemas.openxmlformats.org/markup-compatibility/2006"
       mc:Ignorable="d"
       Background="{ThemeResource ApplicationPageBackgroundThemeBrush}">

       <Grid>
           <StackPanel Orientation="Vertical" HorizontalAlignment="Center"
                       Margin="20,50,0,0" VerticalAlignment="Center" Width="800">
               <Button x:Name="EnableMicrophoneButton" Content="Enable Microphone"
                       Margin="0,0,10,0" Click="EnableMicrophone_ButtonClicked"
                       Height="35"/>
               <Button x:Name="ListenButton" Content="Talk"
                       Margin="0,10,10,0" Click="ListenButton_ButtonClicked"
                       Height="35"/>
               <StackPanel x:Name="StatusPanel" Orientation="Vertical"
                           RelativePanel.AlignBottomWithPanel="True"
                           RelativePanel.AlignRightWithPanel="True"
                           RelativePanel.AlignLeftWithPanel="True">
                   <TextBlock x:Name="StatusLabel" Margin="0,10,10,0"
                              TextWrapping="Wrap" Text="Status:" FontSize="20"/>
                   <Border x:Name="StatusBorder" Margin="0,0,0,0">
                       <ScrollViewer VerticalScrollMode="Auto"
                                     VerticalScrollBarVisibility="Auto" MaxHeight="200">
                           <!-- Use LiveSetting to enable screen readers to announce
                                the status update. -->
                           <TextBlock
                               x:Name="StatusBlock" FontWeight="Bold"
                               AutomationProperties.LiveSetting="Assertive"
                               MaxWidth="{Binding ElementName=Splitter, Path=ActualWidth}"
                               Margin="10,10,10,20" TextWrapping="Wrap"  />
                       </ScrollViewer>
                   </Border>
               </StackPanel>
           </StackPanel>
           <MediaElement x:Name="mediaElement"/>
       </Grid>
   </Page>
   ```

Tasarım görünümü, uygulamanın kullanıcı arabirimini göstermek için güncelleştirilir.

### <a name="c-code-behind-source"></a>C# kod arkası kaynağı

Uygulamanın beklendiği gibi çalışması için kod arkasındaki kaynağı ekleyin. Kod arkası kaynağı şunları içerir:

- Ve ad alanları için gerekli `using` ifadeler `Speech.Dialog` `Speech`
- Bir düğme işleyicisine bağlı mikrofon erişimi sağlamak için basit bir uygulama
- İletileri ve uygulamadaki hataları sunmak için temel Kullanıcı Arabirimi yardımcıları
- Daha sonra doldurulacak başlatma kodu yolu için bir iniş noktası
- Metinden konuşmaya oynatma (akış desteği olmadan)
- Daha sonra doldurulacak dinlemeye başlamak için boş bir düğme işleyicisi

Kod arkası kaynağını aşağıdaki gibi ekleyin:

1. **Solution Explorer'da,** kod arkasındaki `MainPage.xaml.cs` kaynak dosyayı `MainPage.xaml`açın (altında gruplandı)

1. Dosyanın içeriğini aşağıdaki kodla değiştirin:

   ```csharp
   using Microsoft.CognitiveServices.Speech;
   using Microsoft.CognitiveServices.Speech.Audio;
   using Microsoft.CognitiveServices.Speech.Dialog;
   using System;
   using System.Diagnostics;
   using System.IO;
   using System.Text;
   using Windows.Foundation;
   using Windows.Storage.Streams;
   using Windows.UI.Xaml;
   using Windows.UI.Xaml.Controls;
   using Windows.UI.Xaml.Media;

   namespace helloworld
   {
       public sealed partial class MainPage : Page
       {
           private DialogServiceConnector connector;

           private enum NotifyType
           {
               StatusMessage,
               ErrorMessage
           };

           public MainPage()
           {
               this.InitializeComponent();
           }

           private async void EnableMicrophone_ButtonClicked(
               object sender, RoutedEventArgs e)
           {
               bool isMicAvailable = true;
               try
               {
                   var mediaCapture = new Windows.Media.Capture.MediaCapture();
                   var settings =
                       new Windows.Media.Capture.MediaCaptureInitializationSettings();
                   settings.StreamingCaptureMode =
                       Windows.Media.Capture.StreamingCaptureMode.Audio;
                   await mediaCapture.InitializeAsync(settings);
               }
               catch (Exception)
               {
                   isMicAvailable = false;
               }
               if (!isMicAvailable)
               {
                   await Windows.System.Launcher.LaunchUriAsync(
                       new Uri("ms-settings:privacy-microphone"));
               }
               else
               {
                   NotifyUser("Microphone was enabled", NotifyType.StatusMessage);
               }
           }

           private void NotifyUser(
               string strMessage, NotifyType type = NotifyType.StatusMessage)
           {
               // If called from the UI thread, then update immediately.
               // Otherwise, schedule a task on the UI thread to perform the update.
               if (Dispatcher.HasThreadAccess)
               {
                   UpdateStatus(strMessage, type);
               }
               else
               {
                   var task = Dispatcher.RunAsync(
                       Windows.UI.Core.CoreDispatcherPriority.Normal,
                       () => UpdateStatus(strMessage, type));
               }
           }

           private void UpdateStatus(string strMessage, NotifyType type)
           {
               switch (type)
               {
                   case NotifyType.StatusMessage:
                       StatusBorder.Background = new SolidColorBrush(
                           Windows.UI.Colors.Green);
                       break;
                   case NotifyType.ErrorMessage:
                       StatusBorder.Background = new SolidColorBrush(
                           Windows.UI.Colors.Red);
                       break;
               }
               StatusBlock.Text += string.IsNullOrEmpty(StatusBlock.Text)
                   ? strMessage : "\n" + strMessage;

               if (!string.IsNullOrEmpty(StatusBlock.Text))
               {
                   StatusBorder.Visibility = Visibility.Visible;
                   StatusPanel.Visibility = Visibility.Visible;
               }
               else
               {
                   StatusBorder.Visibility = Visibility.Collapsed;
                   StatusPanel.Visibility = Visibility.Collapsed;
               }
               // Raise an event if necessary to enable a screen reader
               // to announce the status update.
               var peer = Windows.UI.Xaml.Automation.Peers.FrameworkElementAutomationPeer.FromElement(StatusBlock);
               if (peer != null)
               {
                   peer.RaiseAutomationEvent(
                       Windows.UI.Xaml.Automation.Peers.AutomationEvents.LiveRegionChanged);
               }
           }

           // Waits for and accumulates all audio associated with a given
           // PullAudioOutputStream and then plays it to the MediaElement. Long spoken
           // audio will create extra latency and a streaming playback solution
           // (that plays audio while it continues to be received) should be used --
           // see the samples for examples of this.
           private void SynchronouslyPlayActivityAudio(
               PullAudioOutputStream activityAudio)
           {
               var playbackStreamWithHeader = new MemoryStream();
               playbackStreamWithHeader.Write(Encoding.ASCII.GetBytes("RIFF"), 0, 4); // ChunkID
               playbackStreamWithHeader.Write(BitConverter.GetBytes(UInt32.MaxValue), 0, 4); // ChunkSize: max
               playbackStreamWithHeader.Write(Encoding.ASCII.GetBytes("WAVE"), 0, 4); // Format
               playbackStreamWithHeader.Write(Encoding.ASCII.GetBytes("fmt "), 0, 4); // Subchunk1ID
               playbackStreamWithHeader.Write(BitConverter.GetBytes(16), 0, 4); // Subchunk1Size: PCM
               playbackStreamWithHeader.Write(BitConverter.GetBytes(1), 0, 2); // AudioFormat: PCM
               playbackStreamWithHeader.Write(BitConverter.GetBytes(1), 0, 2); // NumChannels: mono
               playbackStreamWithHeader.Write(BitConverter.GetBytes(16000), 0, 4); // SampleRate: 16kHz
               playbackStreamWithHeader.Write(BitConverter.GetBytes(32000), 0, 4); // ByteRate
               playbackStreamWithHeader.Write(BitConverter.GetBytes(2), 0, 2); // BlockAlign
               playbackStreamWithHeader.Write(BitConverter.GetBytes(16), 0, 2); // BitsPerSample: 16-bit
               playbackStreamWithHeader.Write(Encoding.ASCII.GetBytes("data"), 0, 4); // Subchunk2ID
               playbackStreamWithHeader.Write(BitConverter.GetBytes(UInt32.MaxValue), 0, 4); // Subchunk2Size

               byte[] pullBuffer = new byte[2056];

               uint lastRead = 0;
               do
               {
                   lastRead = activityAudio.Read(pullBuffer);
                   playbackStreamWithHeader.Write(pullBuffer, 0, (int)lastRead);
               }
               while (lastRead == pullBuffer.Length);

               var task = Dispatcher.RunAsync(
                   Windows.UI.Core.CoreDispatcherPriority.Normal, () =>
               {
                   mediaElement.SetSource(
                       playbackStreamWithHeader.AsRandomAccessStream(), "audio/wav");
                   mediaElement.Play();
               });
           }

           private void InitializeDialogServiceConnector()
           {
               // New code will go here
           }

           private async void ListenButton_ButtonClicked(
               object sender, RoutedEventArgs e)
           {
               // New code will go here
           }
       }
   }
   ```

1. Yöntem gövdesine aşağıdaki kodu ekleyin`InitializeDialogServiceConnector`

   ```csharp
   // This code creates the `DialogServiceConnector` with your subscription information.
   // create a DialogServiceConfig by providing a Custom Commands application id and Cognitive Services subscription key
   // the RecoLanguage property is optional (default en-US); note that only en-US is supported in Preview
   const string speechCommandsApplicationId = "YourApplicationId"; // Your application id
   const string speechSubscriptionKey = "YourSpeechSubscriptionKey"; // Your subscription key
   const string region = "YourServiceRegion"; // The subscription service region. Note: only 'westus2' is currently supported

   var speechCommandsConfig = CustomCommandsConfig.FromSubscription(speechCommandsApplicationId, speechSubscriptionKey, region);
   speechCommandsConfig.SetProperty(PropertyId.SpeechServiceConnection_RecoLanguage, "en-us");
   connector = new DialogServiceConnector(speechCommandsConfig);
   ```

1. `YourApplicationId`Uygulamanız, `YourSpeechSubscriptionKey`konuşma aboneliğiniz ve [bölgeniz](regions.md) için dizeleri ve `YourServiceRegion` kendi değerlerinizle değiştirin

1. Yöntem gövdesinin sonuna aşağıdaki kod parçacığı ekine`InitializeDialogServiceConnector`

   ```csharp
   //
   // This code sets up handlers for events relied on by `DialogServiceConnector` to communicate its activities,
   // speech recognition results, and other information.
   //
   // ActivityReceived is the main way your client will receive messages, audio, and events
   connector.ActivityReceived += async (sender, activityReceivedEventArgs) =>
   {
       NotifyUser(
           $"Activity received, hasAudio={activityReceivedEventArgs.HasAudio} activity={activityReceivedEventArgs.Activity}");

       if (activityReceivedEventArgs.HasAudio)
       {
           SynchronouslyPlayActivityAudio(activityReceivedEventArgs.Audio);
       }
   };

   // Canceled will be signaled when a turn is aborted or experiences an error condition
   connector.Canceled += (sender, canceledEventArgs) =>
   {
       NotifyUser($"Canceled, reason={canceledEventArgs.Reason}");
       if (canceledEventArgs.Reason == CancellationReason.Error)
       {
           NotifyUser(
               $"Error: code={canceledEventArgs.ErrorCode}, details={canceledEventArgs.ErrorDetails}");
       }
   };

   // Recognizing (not 'Recognized') will provide the intermediate recognized text
   // while an audio stream is being processed
   connector.Recognizing += (sender, recognitionEventArgs) =>
   {
       NotifyUser($"Recognizing! in-progress text={recognitionEventArgs.Result.Text}");
   };

   // Recognized (not 'Recognizing') will provide the final recognized text
   // once audio capture is completed
   connector.Recognized += (sender, recognitionEventArgs) =>
   {
       NotifyUser($"Final speech-to-text result: '{recognitionEventArgs.Result.Text}'");
   };

   // SessionStarted will notify when audio begins flowing to the service for a turn
   connector.SessionStarted += (sender, sessionEventArgs) =>
   {
       NotifyUser($"Now Listening! Session started, id={sessionEventArgs.SessionId}");
   };

   // SessionStopped will notify when a turn is complete and
   // it's safe to begin listening again
   connector.SessionStopped += (sender, sessionEventArgs) =>
   {
       NotifyUser($"Listening complete. Session ended, id={sessionEventArgs.SessionId}");
   };
   ```

1. `MainPage` Sınıfta yöntemin `ListenButton_ButtonClicked` gövdesine aşağıdaki kod parçacığı ekleme

   ```csharp
   // This code sets up `DialogServiceConnector` to listen, since you already established the configuration and
   // registered the event handlers.
   if (connector == null)
   {
       InitializeDialogServiceConnector();
       // Optional step to speed up first interaction: if not called,
       // connection happens automatically on first use
       var connectTask = connector.ConnectAsync();
   }

   try
   {
       // Start sending audio
       await connector.ListenOnceAsync();
   }
   catch (Exception ex)
   {
       NotifyUser($"Exception: {ex.ToString()}", NotifyType.ErrorMessage);
   }
   ```

1. Menü çubuğundan, değişikliklerinizi kaydetmek için **Tümlerini** > **Kaydet'i** seçin

## <a name="build-and-run-the-application"></a>Uygulamayı derleme ve çalıştırma

1. Menü çubuğundan, uygulamayı oluşturmak için **Yapı** > **Çözüm'üne** bakın' ı seçin. Kodun hatasız derlenmesi gerekir.

1. Uygulamayı başlatmak için **Hata Ayıklama** > **Başlatma Hata Ayıklama'yı** (veya **F5**tuşuna) seçin. **Helloworld** penceresi görüntülenir.

   ![C# örnek UWP sanal asistan uygulaması - quickstart](media/sdk/qs-voice-assistant-uwp-helloworld-window.png)

1. **Mikrofonu Etkinleştir'i**seçin. Erişim izni isteği açılırsa **Evet'i**seçin.

   ![Mikrofon erişim izni isteği](media/sdk/qs-csharp-uwp-10-access-prompt.png)

1. **Konuş'u**seçin ve cihazınızın mikrofonuna İngilizce bir tümcecik veya cümle konuşun. Konuşmanız Doğrudan Satır Konuşması kanalına aktarılır ve pencerede görünen metne aktarılır.

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [Nasıl yapılır: Konuşma SDK (önizleme)](./how-to-custom-speech-commands-fulfill-sdk.md)
> ile istemci komutları yerine getirme[nasıl yapılır: Özel Komut parametrelerine doğrulama ekleme (Önizleme)](./how-to-custom-speech-commands-validations.md)
