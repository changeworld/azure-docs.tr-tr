---
title: 'Quickstart: Özel bir ses asistanı, Java (Android) oluşturma - Konuşma hizmeti'
titleSuffix: Azure Cognitive Services
description: Konuşma SDK'yı kullanarak Android'de Java'da nasıl özel bir sesli asistan oluşturabilirsiniz öğrenin.
services: cognitive-services
author: trrwilson
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 04/04/2020
ms.author: travisw
ms.openlocfilehash: 8a1dd07fd567f41c2b406aabccd0421b5a6983af
ms.sourcegitcommit: 67addb783644bafce5713e3ed10b7599a1d5c151
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/05/2020
ms.locfileid: "80671043"
---
## <a name="prerequisites"></a>Ön koşullar

Başlamadan önce şunları unutmayın:

> [!div class="checklist"]
> * [Azure Konuşma kaynağı oluşturma](~/articles/cognitive-services/speech-service/get-started.md)
> * [Geliştirme ortamınızı ayarlayın ve boş bir proje oluşturun](~/articles/cognitive-services/speech-service/quickstarts/setup-platform.md?tabs=android&pivots=programming-language-java)
> * Doğrudan Satır Konuşma [kanalına](https://docs.microsoft.com/azure/bot-service/bot-service-channel-connect-directlinespeech) bağlı bir bot oluşturma
> * Ses çekimi için mikrofona erişebildiğinizden emin olun

  > [!NOTE]
  > Lütfen [sesli asistanlar için desteklenen bölgeler listesine](~/articles/cognitive-services/speech-service/regions.md#voice-assistants) bakın ve kaynaklarınızın bu bölgelerden birinde dağıtıldığından emin olun.

## <a name="create-and-configure-a-project"></a>Projeyi oluşturma ve yapılandırma

[!INCLUDE [](~/includes/cognitive-services-speech-service-quickstart-java-android-create-proj.md)]

## <a name="create-user-interface"></a>Kullanıcı arabirimi oluşturma

Bu bölümde, uygulama için temel bir kullanıcı arabirimi (UI) oluşturacağız. Ana etkinliği açarak başlayalım: `activity_main.xml`. Temel şablon, uygulamanın adını içeren bir başlık `TextView` çubuğu ve "Merhaba dünya!" iletisini içerir.

Ardından, aşağıdaki kodun `activity_main.xml` içeriğini değiştirin:

   ```xml
   <?xml version="1.0" encoding="utf-8"?>
   <LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:orientation="vertical"
    tools:context=".MainActivity">

    <Button
        android:id="@+id/button"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_gravity="center"
        android:onClick="onBotButtonClicked"
        android:text="Talk to your bot" />

    <TextView
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:text="Recognition Data"
        android:textSize="18dp"
        android:textStyle="bold" />

    <TextView
        android:id="@+id/recoText"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:text="  \n(Recognition goes here)\n" />

    <TextView
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:text="Activity Data"
        android:textSize="18dp"
        android:textStyle="bold" />

    <TextView
        android:id="@+id/activityText"
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        android:scrollbars="vertical"
        android:text="  \n(Activities go here)\n" />

   </LinearLayout>
   ```

Bu XML, botunuzun etkileşimini sağlamak için basit bir kullanıcı arama birimi tanımlar.

- Öğe `button` bir etkileşim başlatır ve `onBotButtonClicked` tıklatıldığında yöntemi çağırır.
- Öğe, `recoText` siz botunuzla konuşurken konuşma metni sonuçlarını görüntüler.
- Öğe, `activityText` botunuzdan en son Bot Framework etkinliği için JSON yükünü görüntüler.

Kullanıcı arabiriminizin metin ve grafik gösterimi şimdi şu şekilde görünmelidir:

![](~/articles/cognitive-services/speech-service/media/sdk/qs-java-android-assistant-designer-ui.png)

## <a name="add-sample-code"></a>Örnek kod ekleme

1. İçeriği `MainActivity.java`aşağıdaki kodla açın ve değiştirin:

   ```java
    package samples.speech.cognitiveservices.microsoft.com;

    import android.media.AudioFormat;
    import android.media.AudioManager;
    import android.media.AudioTrack;
    import android.support.v4.app.ActivityCompat;
    import android.support.v7.app.AppCompatActivity;
    import android.os.Bundle;
    import android.text.method.ScrollingMovementMethod;
    import android.view.View;
    import android.widget.TextView;

    import com.microsoft.cognitiveservices.speech.audio.AudioConfig;
    import com.microsoft.cognitiveservices.speech.audio.PullAudioOutputStream;
    import com.microsoft.cognitiveservices.speech.dialog.DialogServiceConfig;
    import com.microsoft.cognitiveservices.speech.dialog.DialogServiceConnector;

    import org.json.JSONException;
    import org.json.JSONObject;

    import static android.Manifest.permission.*;

    public class MainActivity extends AppCompatActivity {
        // Replace below with your own speech subscription key
        private static String speechSubscriptionKey = "YourSpeechSubscriptionKey";
        // Replace below with your own speech service region
        private static String serviceRegion = "YourSpeechServiceRegion";

        private DialogServiceConnector connector;

        @Override
        protected void onCreate(Bundle savedInstanceState) {
            super.onCreate(savedInstanceState);
            setContentView(R.layout.activity_main);

            TextView recoText = (TextView) this.findViewById(R.id.recoText);
            TextView activityText = (TextView) this.findViewById(R.id.activityText);
            recoText.setMovementMethod(new ScrollingMovementMethod());
            activityText.setMovementMethod(new ScrollingMovementMethod());

            // Note: we need to request permissions for audio input and network access
            int requestCode = 5; // unique code for the permission request
            ActivityCompat.requestPermissions(MainActivity.this, new String[]{RECORD_AUDIO, INTERNET}, requestCode);
        }

        public void onBotButtonClicked(View v) {
            // Recreate the DialogServiceConnector on each button press, ensuring that the existing one is closed
            if (connector != null) {
                connector.close();
                connector = null;
            }

            // Create the DialogServiceConnector from speech subscription information
            BotFrameworkConfig config = BotFrameworkConfig.fromSubscription(speechSubscriptionKey, serviceRegion);
            connector = new DialogServiceConnector(config, AudioConfig.fromDefaultMicrophoneInput());

            // Optional step: preemptively connect to reduce first interaction latency
            connector.connectAsync();

            // Register the DialogServiceConnector's event listeners
            registerEventListeners();

            // Begin sending audio to your bot
            connector.listenOnceAsync();
        }

        private void registerEventListeners() {
            TextView recoText = (TextView) this.findViewById(R.id.recoText); // 'recoText' is the ID of your text view
            TextView activityText = (TextView) this.findViewById(R.id.activityText); // 'activityText' is the ID of your text view

            // Recognizing will provide the intermediate recognized text while an audio stream is being processed
            connector.recognizing.addEventListener((o, recoArgs) -> {
                recoText.setText("  Recognizing: " + recoArgs.getResult().getText());
            });

            // Recognized will provide the final recognized text once audio capture is completed
            connector.recognized.addEventListener((o, recoArgs) -> {
                recoText.setText("  Recognized: " + recoArgs.getResult().getText());
            });

            // SessionStarted will notify when audio begins flowing to the service for a turn
            connector.sessionStarted.addEventListener((o, sessionArgs) -> {
                recoText.setText("Listening...");
            });

            // SessionStopped will notify when a turn is complete and it's safe to begin listening again
            connector.sessionStopped.addEventListener((o, sessionArgs) -> {
            });

            // Canceled will be signaled when a turn is aborted or experiences an error condition
            connector.canceled.addEventListener((o, canceledArgs) -> {
                recoText.setText("Canceled (" + canceledArgs.getReason().toString() + ") error details: {}" + canceledArgs.getErrorDetails());
                connector.disconnectAsync();
            });

            // ActivityReceived is the main way your bot will communicate with the client and uses bot framework activities.
            connector.activityReceived.addEventListener((o, activityArgs) -> {
                try {
                    // Here we use JSONObject only to "pretty print" the condensed Activity JSON
                    String rawActivity = activityArgs.getActivity().serialize();
                    String formattedActivity = new JSONObject(rawActivity).toString(2);
                    activityText.setText(formattedActivity);
                } catch (JSONException e) {
                    activityText.setText("Couldn't format activity text: " + e.getMessage());
                }

                if (activityArgs.hasAudio()) {
                    // Text-to-speech audio associated with the activity is 16 kHz 16-bit mono PCM data
                    final int sampleRate = 16000;
                    int bufferSize = AudioTrack.getMinBufferSize(sampleRate, AudioFormat.CHANNEL_OUT_MONO, AudioFormat.ENCODING_PCM_16BIT);

                    AudioTrack track = new AudioTrack(
                            AudioManager.STREAM_MUSIC,
                            sampleRate,
                            AudioFormat.CHANNEL_OUT_MONO,
                            AudioFormat.ENCODING_PCM_16BIT,
                            bufferSize,
                            AudioTrack.MODE_STREAM);

                    track.play();

                    PullAudioOutputStream stream = activityArgs.getAudio();

                    // Audio is streamed as it becomes available. Play it as it arrives.
                    byte[] buffer = new byte[bufferSize];
                    long bytesRead = 0;

                    do {
                        bytesRead = stream.read(buffer);
                        track.write(buffer, 0, (int) bytesRead);
                    } while (bytesRead == bufferSize);

                    track.release();
                }
            });
        }
    }
   ```

   * Yöntem, `onCreate` mikrofon ve internet izinleri isteyen kod içerir.

   * `onBotButtonClicked` yöntemi daha önce de belirtildiği gibi düğme tıklama işleyicisidir. Bir düğmeye basmak, botunuzun izle tek bir etkileşimini ("dönüş") tetikler.

   * Yöntem, `registerEventListeners` gelen etkinliklerin `DialogServiceConnector` temel olarak işlenmesinde kullanılan olayları gösterir.

1. Aynı dosyada, kaynaklarınızla eşleşecek şekilde yapılandırma dizelerini değiştirin:

    * `YourSpeechSubscriptionKey` değerini abonelik anahtarınızla değiştirin.

    * Aboneliğinizle ilişkili `YourServiceRegion` [bölgeyle](~/articles/cognitive-services/speech-service/regions.md) değiştirin Yalnızca Konuşma hizmet bölgelerinin bir alt kümesi şu anda Doğrudan Satır Konuşması ile desteklenir. Daha fazla bilgi için [bölgelere](~/articles/cognitive-services/speech-service/regions.md#voice-assistants)bakın.

## <a name="build-and-run-the-app"></a>Uygulamayı derleme ve çalıştırma

1. Android cihazınızı geliştirme bilgisayarınıza bağlayın. Cihazda [geliştirme modunu ve USB hata ayıklamasını](https://developer.android.com/studio/debug/dev-options) etkinleştirdiğinizden emin olun.

1. Uygulamayı oluşturmak için Ctrl+F9 tuşuna basın veya menü çubuğundan **Yapı** > **Projesi'ni** seçin.

1. Uygulamayı başlatmak için Shift+F10'a basın veya **Çalıştır** > **'uygulamasını'** seçin.

1. Görüntülenen dağıtım hedefi penceresinde Android cihazınızı seçin.

   ![Dağıtım Hedefi Seç penceresinin ekran görüntüsü](~/articles/cognitive-services/speech-service/media/sdk/qs-java-android-12-deploy.png)

Uygulama ve etkinliği başladıktan sonra, botunuzla konuşmaya başlamak için düğmeye tıklayın. Siz konuşurken transkripsiyonlu metin görünür ve botunuzdan aldığınız en son etkinlik, yapıldığında görünür. Botunuzun sözlü yanıtları sağlayacak şekilde yapılandırılırsa, konuşma metni otomatik olarak oynanır.

![Android uygulamasının ekran görüntüsü](~/articles/cognitive-services/speech-service/media/sdk/qs-java-android-assistant-completed-turn.png)

## <a name="next-steps"></a>Sonraki adımlar

[!INCLUDE [footer](./footer.md)]

