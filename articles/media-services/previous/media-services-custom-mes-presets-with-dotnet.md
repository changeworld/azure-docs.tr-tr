---
title: Ortam Kodlayıcı Standart ön ayarlarını özelleştirme | Microsoft Dokümanlar
description: Bu konu, Media Encoder Standart görev hazır ayarlarını özelleştirerek gelişmiş kodlamanın nasıl gerçekleştirilini gösterir. Konu, kodlama görevi ve işi oluşturmak için Media Services .NET SDK'nın nasıl kullanılacağını gösterir. Ayrıca, kodlama işine özel hazır ayarların nasıl verilebildiğini de gösterir.
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.assetid: ec95392f-d34a-4c22-a6df-5274eaac445b
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/26/2019
ms.author: juliako
ms.openlocfilehash: 39a1dd5c3d26eeb6545a96aa35f9457bd9859c21
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79251251"
---
# <a name="customizing-media-encoder-standard-presets"></a>Ortam Kodlayıcı Standart ön ayarlarını özelleştirme  

## <a name="overview"></a>Genel Bakış

Bu makalede, özel bir ön ayar kullanarak Media Encoder Standard (MES) ile gelişmiş kodlama nasıl gerçekleştiriliş gösterilmektedir. Makale, bir kodlama görevi ve bu görevi yürüten bir iş oluşturmak için .NET kullanır.  

Bu makalede, [H264 Çoklu Bitrate 720p](media-services-mes-preset-H264-Multiple-Bitrate-720p.md) önceden ayarlayarak ve katman sayısını azaltarak bir önceden ayarlanmış özelleştirmek için nasıl gösterir. [Özelleştirici Ortam Kodlayıcı Standart hazır ayarları](media-services-advanced-encoding-with-mes.md) makalesi, gelişmiş kodlama görevlerini gerçekleştirmek için kullanılabilecek özel hazır ayarları gösterir.

> [!NOTE]
> Bu makalede açıklanan özel hazır ayarlar [Medya Hizmetleri V3](https://docs.microsoft.com/azure/media-services/latest/) dönüşümlerinde veya CLI komutlarında kullanılamaz. Daha fazla bilgi [için v2'den v3'e geçiş kılavuzuna](../latest/migrate-from-v2-to-v3.md) bakın.

## <a name="customizing-a-mes-preset"></a><a id="customizing_presets"></a>MES ön ayarlarını özelleştirme

### <a name="original-preset"></a>Orijinal ön ayar

[H264 Çoklu Bitrate 720p](media-services-mes-preset-H264-Multiple-Bitrate-720p.md) makalesinde tanımlanan JSON'u .json uzantılı bir dosyaya kaydedin. Örneğin, **CustomPreset_JSON.json**.

### <a name="customized-preset"></a>Özelleştirilmiş ön ayar

**CustomPreset_JSON.json** dosyasını açın ve dosyanızın bu gibi görünmesi için **H264Layers'dan** ilk üç katmanı kaldırın.

```json 
    {  
      "Version": 1.0,  
      "Codecs": [  
        {  
          "KeyFrameInterval": "00:00:02",  
          "H264Layers": [  
            {  
              "Profile": "Auto",  
              "Level": "auto",  
              "Bitrate": 1000,  
              "MaxBitrate": 1000,  
              "BufferWindow": "00:00:05",  
              "Width": 640,  
              "Height": 360,  
              "BFrames": 3,  
              "ReferenceFrames": 3,  
              "AdaptiveBFrame": true,  
              "Type": "H264Layer",  
              "FrameRate": "0/1"  
            },  
            {  
              "Profile": "Auto",  
              "Level": "auto",  
              "Bitrate": 650,  
              "MaxBitrate": 650,  
              "BufferWindow": "00:00:05",  
              "Width": 640,  
              "Height": 360,  
              "BFrames": 3,  
              "ReferenceFrames": 3,  
              "AdaptiveBFrame": true,  
              "Type": "H264Layer",  
              "FrameRate": "0/1"  
            },  
            {  
              "Profile": "Auto",  
              "Level": "auto",  
              "Bitrate": 400,  
              "MaxBitrate": 400,  
              "BufferWindow": "00:00:05",  
              "Width": 320,  
              "Height": 180,  
              "BFrames": 3,  
              "ReferenceFrames": 3,  
              "AdaptiveBFrame": true,  
              "Type": "H264Layer",  
              "FrameRate": "0/1"  
            }  
          ],  
          "Type": "H264Video"  
        },  
        {  
          "Profile": "AACLC",  
          "Channels": 2,  
          "SamplingRate": 48000,  
          "Bitrate": 128,  
          "Type": "AACAudio"  
        }  
      ],  
      "Outputs": [  
        {  
          "FileName": "{Basename}_{Width}x{Height}_{VideoBitrate}.mp4",  
          "Format": {  
            "Type": "MP4Format"  
          }  
        }  
      ]  
    }  
```

## <a name="encoding-with-media-services-net-sdk"></a><a id="encoding_with_dotnet"></a>Medya Hizmetleri ile Kodlama .NET SDK

Aşağıdaki kod örneği, aşağıdaki görevleri gerçekleştirmek için Medya Hizmetleri .NET SDK'yı kullanır:

- Kodlama işi oluşturun.
- Media Encoder Standart kodlayıcısına başvurun.
- Önceki bölümde oluşturduğunuz özel JSON ön ayarını yükleyin. 
  
        // Load the JSON from the local file.
        string configuration = File.ReadAllText(fileName);  

- İşe bir kodlama görevi ekleyin. 
- Kodlanacak giriş kıymetini belirtin.
- Kodlanmış kıymeti içeren bir çıktı kıymeti oluşturun.
- İş ilerlemesini denetlemek için bir olay işleyicisi ekleyin.
- İşi gönderin.
   
#### <a name="create-and-configure-a-visual-studio-project"></a>Visual Studio projesi oluşturup yapılandırma

Geliştirme ortamınızı kurun ve app.config dosyanızı [.NET ile Media Services geliştirme](media-services-dotnet-how-to-use.md) bölümünde açıklandığı gibi bağlantı bilgileriyle doldurun. 

#### <a name="example"></a>Örnek   

```csharp
using System;
using System.Configuration;
using System.IO;
using System.Linq;
using Microsoft.WindowsAzure.MediaServices.Client;
using System.Threading;

namespace CustomizeMESPresests
{
    class Program
    {
        // Read values from the App.config file.
        private static readonly string _AADTenantDomain =
            ConfigurationManager.AppSettings["AMSAADTenantDomain"];
        private static readonly string _RESTAPIEndpoint =
            ConfigurationManager.AppSettings["AMSRESTAPIEndpoint"];
        private static readonly string _AMSClientId =
            ConfigurationManager.AppSettings["AMSClientId"];
        private static readonly string _AMSClientSecret =
            ConfigurationManager.AppSettings["AMSClientSecret"];

        // Field for service context.
        private static CloudMediaContext _context = null;

        private static readonly string _mediaFiles =
            Path.GetFullPath(@"../..\Media");

        private static readonly string _singleMP4File =
            Path.Combine(_mediaFiles, @"BigBuckBunny.mp4");

        static void Main(string[] args)
        {
            AzureAdTokenCredentials tokenCredentials =
                new AzureAdTokenCredentials(_AADTenantDomain,
                    new AzureAdClientSymmetricKey(_AMSClientId, _AMSClientSecret),
                    AzureEnvironments.AzureCloudEnvironment);

            var tokenProvider = new AzureAdTokenProvider(tokenCredentials);

            _context = new CloudMediaContext(new Uri(_RESTAPIEndpoint), tokenProvider);

            // Get an uploaded asset.
            var asset = _context.Assets.FirstOrDefault();

            // Encode and generate the output using custom presets.
            EncodeToAdaptiveBitrateMP4Set(asset);

            Console.ReadLine();
        }

        static public IAsset EncodeToAdaptiveBitrateMP4Set(IAsset asset)
        {
            // Declare a new job.
            IJob job = _context.Jobs.Create("Media Encoder Standard Job");
            // Get a media processor reference, and pass to it the name of the 
            // processor to use for the specific task.
            IMediaProcessor processor = GetLatestMediaProcessorByName("Media Encoder Standard");

            // Load the XML (or JSON) from the local file.
            string configuration = File.ReadAllText("CustomPreset_JSON.json");

            // Create a task
            ITask task = job.Tasks.AddNew("Media Encoder Standard encoding task",
            processor,
            configuration,
            TaskOptions.None);

            // Specify the input asset to be encoded.
            task.InputAssets.Add(asset);
            // Add an output asset to contain the results of the job. 
            // This output is specified as AssetCreationOptions.None, which 
            // means the output asset is not encrypted. 
            task.OutputAssets.AddNew("Output asset",
            AssetCreationOptions.None);

            job.StateChanged += new EventHandler<JobStateChangedEventArgs>(JobStateChanged);
            job.Submit();
            job.GetExecutionProgressTask(CancellationToken.None).Wait();

            return job.OutputMediaAssets[0];
        }

        private static void JobStateChanged(object sender, JobStateChangedEventArgs e)
        {
            Console.WriteLine("Job state changed event:");
            Console.WriteLine("  Previous state: " + e.PreviousState);
            Console.WriteLine("  Current state: " + e.CurrentState);
            switch (e.CurrentState)
            {
                case JobState.Finished:
                    Console.WriteLine();
                    Console.WriteLine("Job is finished. Please wait while local tasks or downloads complete...");
                    break;
                case JobState.Canceling:
                case JobState.Queued:
                case JobState.Scheduled:
                case JobState.Processing:
                    Console.WriteLine("Please wait...\n");
                    break;
                case JobState.Canceled:
                case JobState.Error:

                    // Cast sender as a job.
                    IJob job = (IJob)sender;

                    // Display or log error details as needed.
                    break;
                default:
                    break;
            }
        }

        private static IMediaProcessor GetLatestMediaProcessorByName(string mediaProcessorName)
        {
            var processor = _context.MediaProcessors.Where(p => p.Name == mediaProcessorName).
            ToList().OrderBy(p => new Version(p.Version)).LastOrDefault();

            if (processor == null)
                throw new ArgumentException(string.Format("Unknown media processor", mediaProcessorName));

            return processor;
        }

    }
}
```

## <a name="see-also"></a>Ayrıca bkz.

- [CLI kullanarak özel bir dönüştürme ile kodlama](../latest/custom-preset-cli-howto.md)
- [Media Services v3 ile kodlama](../latest/encoding-concept.md)

## <a name="media-services-learning-paths"></a>Media Services’i öğrenme yolları
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Geri bildirimde bulunma
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
