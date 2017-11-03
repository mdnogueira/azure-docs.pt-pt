---
title: "Detetar enfrentam e emoções com a análise de multimédia do Azure | Microsoft Docs"
description: "Este tópico demonstra como detetar faces e emotions análise de multimédia do Azure."
services: media-services
documentationcenter: 
author: juliako
manager: cfowler
editor: 
ms.assetid: 5ca4692c-23f1-451d-9d82-cbc8bf0fd707
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 09/18/2017
ms.author: milanga;juliako;
ms.openlocfilehash: a55a0c2ef8c1c065b39fce9dc6ef2f806b60dfdc
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="detect-face-and-emotion-with-azure-media-analytics"></a>Detetar enfrentam e emoções com a análise de multimédia do Azure
## <a name="overview"></a>Descrição geral
O **Azure suporte de dados enfrentam Detector** processador de multimédia (MP) permite-lhe contagem, controlar movimentos e mesmo meça a participação de público-alvo e reação através de expressões facial. Este serviço contém duas funcionalidades: 

* **Deteção de rostos em**
  
    Deteção de rostos em localiza e controla faces humanos dentro de um vídeo. Vários faces podem ser detetados e, subsequentemente, ser monitorizados como estes mover-se, com os metadados de hora e a localização devolvido num ficheiro JSON. Durante o registo, a tentativa de atribuir um ID de consistente para a mesma letra enquanto a pessoa que está a mover à volta no ecrã, mesmo que estes são obstructed ou deixam brevemente da moldura.
  
  > [!NOTE]
  > Este serviço não efetua o reconhecimento facial. Uma pessoa que mantém a frame ou fica obstructed para demasiado tempo terá um novo ID quando devolvem.
  > 
  > 
* **Deteção de emoções**
  
    Deteção de emoções é um componente opcional do processador do suporte de dados de deteção de rostos em que devolve o Analysis Services em vários atributos emotional de faces detetados, incluindo happiness, sadness, fear, anger e muito mais. 

O **Azure suporte de dados enfrentam Detector** MP está atualmente em pré-visualização.

Este tópico fornece detalhes sobre **Azure suporte de dados enfrentam Detector** e mostra como utilizá-la com o SDK de Media Services para .NET.

## <a name="face-detector-input-files"></a>Enfrentam os ficheiros de entrada Detector
Ficheiros de vídeo. Atualmente, são suportados os seguintes formatos: MP4, MOV e WMV.

## <a name="face-detector-output-files"></a>Enfrentam Detector ficheiros de saída
A API de deteção e controlo de rostos em fornece deteção de localização de rostos em elevada precisão e controlo pode detetar até 64 faces humanos um vídeo. Frontal faces fornecem os melhores resultados, ao lado faces e em pequenos poderão não ser exatas como faces (menor ou igual a 24 x 24 pixéis).

Os faces detetados e controladas são devolvidos com coordenadas (à esquerda, superior, largura e altura) que indica a localização do faces na imagem em pixels, bem como um número de ID de rostos em que indica que o controlo de que individuais. Números de ID de rostos em são suscetíveis a repor circunstâncias quando o enfrentam frontal é perdido ou que se sobreponham a num intervalo, resultando em alguns indivíduos obter atribuídos a vários IDs.

## <a id="output_elements"></a>Elementos do ficheiro de saída JSON

[!INCLUDE [media-services-analytics-output-json](../../includes/media-services-analytics-output-json.md)]

Enfrentam Detector utiliza técnicas de fragmentação (onde podem dividir os metadados de cópia de segurança em segmentos baseados no tempo e pode transferir apenas o que precisa de) e a segmentação de (em que os eventos são quebrados cópias de segurança no caso de obtêm demasiado grandes). Alguns cálculos simples podem ajudar a transformar os dados. Por exemplo, se um evento foi iniciado no 6300 (ticks), com uma escala temporal de 2997 (ticks/seg) e framerate de 29.97 (fotogramas por segundo), em seguida:

* Iniciar/escala temporal = segundos 2.1
* Segundos x Framerate = 63 frames

## <a name="face-detection-input-and-output-example"></a>Enfrentam deteção entrada e saída de exemplo
### <a name="input-video"></a>Vídeo de entrada
[Vídeo de entrada](http://ampdemo.azureedge.net/azuremediaplayer.html?url=https%3A%2F%2Freferencestream-samplestream.streaming.mediaservices.windows.net%2Fc8834d9f-0b49-4b38-bcaf-ece2746f1972%2FMicrosoft%20Convergence%202015%20%20Keynote%20Highlights.ism%2Fmanifest&amp;autoplay=false)

### <a name="task-configuration-preset"></a>Configuração da tarefa (predefinição)
Quando criar uma tarefa com **Azure suporte de dados enfrentam Detector**, tem de especificar uma predefinição de configuração. A predefinição de configuração seguinte é apenas para a deteção de rostos em.

    {
      "version":"1.0",
      "options":{
          "TrackingMode": "Fast"
      }
    }

#### <a name="attribute-descriptions"></a>Descrições de atributo
| Nome do atributo | Descrição |
| --- | --- |
| Modo |Rápida - rápido processar velocidade, mas inferior (predefinição).|

### <a name="json-output"></a>Saída JSON
O seguinte exemplo de saída JSON foi truncado.

    {
    "version": 1,
    "timescale": 30000,
    "offset": 0,
    "framerate": 29.97,
    "width": 1280,
    "height": 720,
    "fragments": [
        {
        "start": 0,
        "duration": 60060
        },
        {
        "start": 60060,
        "duration": 60060,
        "interval": 1001,
        "events": [
            [
            {
                "id": 0,
                "x": 0.519531,
                "y": 0.180556,
                "width": 0.0867188,
                "height": 0.154167
            }
            ],
            [
            {
                "id": 0,
                "x": 0.517969,
                "y": 0.181944,
                "width": 0.0867188,
                "height": 0.154167
            }
            ],
            [
            {
                "id": 0,
                "x": 0.517187,
                "y": 0.183333,
                "width": 0.0851562,
                "height": 0.151389
            }
            ],

        . . . 

## <a name="emotion-detection-input-and-output-example"></a>Deteção de emoções de entrada e saída de exemplo
### <a name="input-video"></a>Vídeo de entrada
[Vídeo de entrada](http://ampdemo.azureedge.net/azuremediaplayer.html?url=https%3A%2F%2Freferencestream-samplestream.streaming.mediaservices.windows.net%2Fc8834d9f-0b49-4b38-bcaf-ece2746f1972%2FMicrosoft%20Convergence%202015%20%20Keynote%20Highlights.ism%2Fmanifest&amp;autoplay=false)

### <a name="task-configuration-preset"></a>Configuração da tarefa (predefinição)
Quando criar uma tarefa com **Azure suporte de dados enfrentam Detector**, tem de especificar uma predefinição de configuração. Especifica a predefinição de configuração seguintes para criar com base na deteção de emoções de JSON.

    {
      "version": "1.0",
      "options": {
        "aggregateEmotionWindowMs": "987",
        "mode": "aggregateEmotion",
        "aggregateEmotionIntervalMs": "342"
      }
    }


#### <a name="attribute-descriptions"></a>Descrições de atributo
| Nome do atributo | Descrição |
| --- | --- |
| Modo |Faces: Enfrentam apenas deteção.<br/>PerFaceEmotion: Devolver emoções independentemente para cada deteção de rostos em.<br/>AggregateEmotion: Valores de retorno emoções médio para todos os faces num intervalo. |
| AggregateEmotionWindowMs |Utilize se AggregateEmotion modo selecionado. Especifica o comprimento de vídeo utilizado para produzir cada resultado do agregado, em milissegundos. |
| AggregateEmotionIntervalMs |Utilize se AggregateEmotion modo selecionado. Especifica com que frequência para produzir resultados de agregação. |

#### <a name="aggregate-defaults"></a>Predefinições de agregação
Abaixo são recomendadas valores para as definições de janela e o intervalo de agregação. AggregateEmotionWindowMs deve ser maior que AggregateEmotionIntervalMs.

|| Predefinições (s) | Max(s) | Min(s) |
|--- | --- | --- | --- |
| AggregateEmotionWindowMs |0.5 |2 |0.25|
| AggregateEmotionIntervalMs |0.5 |1 |0.25|

### <a name="json-output"></a>Saída JSON
JSON de saída para emoções agregada (truncada):

    {
     "version": 1,
     "timescale": 30000,
     "offset": 0,
     "framerate": 29.97,
     "width": 1280,
     "height": 720,
     "fragments": [
       {
         "start": 0,
         "duration": 60060,
         "interval": 15015,
         "events": [
           [
             {
               "windowFaceDistribution": {
                 "neutral": 0,
                 "happiness": 0,
                 "surprise": 0,
                 "sadness": 0,
                 "anger": 0,
                 "disgust": 0,
                 "fear": 0,
                 "contempt": 0
               },
               "windowMeanScores": {
                 "neutral": 0,
                 "happiness": 0,
                 "surprise": 0,
                 "sadness": 0,
                 "anger": 0,
                 "disgust": 0,
                 "fear": 0,
                 "contempt": 0
               }
             }
           ],
           [
             {
               "windowFaceDistribution": {
                 "neutral": 0,
                 "happiness": 0,
                 "surprise": 0,
                 "sadness": 0,
                 "anger": 0,
                 "disgust": 0,
                 "fear": 0,
                 "contempt": 0
               },
               "windowMeanScores": {
                 "neutral": 0,
                 "happiness": 0,
                 "surprise": 0,
                 "sadness": 0,
                 "anger": 0,
                 "disgust": 0,
                 "fear": 0,
                 "contempt": 0
               }
             }
           ],
           [
             {
               "windowFaceDistribution": {
                 "neutral": 0,
                 "happiness": 0,
                 "surprise": 0,
                 "sadness": 0,
                 "anger": 0,
                 "disgust": 0,
                 "fear": 0,
                 "contempt": 0
               },
               "windowMeanScores": {
                 "neutral": 0,
                 "happiness": 0,
                 "surprise": 0,
                 "sadness": 0,
                 "anger": 0,
                 "disgust": 0,
                 "fear": 0,
                 "contempt": 0
               }
             }
           ],
           [
             {
               "windowFaceDistribution": {
                 "neutral": 0,
                 "happiness": 0,
                 "surprise": 0,
                 "sadness": 0,
                 "anger": 0,
                 "disgust": 0,
                 "fear": 0,
                 "contempt": 0
               },
               "windowMeanScores": {
                 "neutral": 0,
                 "happiness": 0,
                 "surprise": 0,
                 "sadness": 0,
                 "anger": 0,
                 "disgust": 0,
                 "fear": 0,
                 "contempt": 0
               }
             }
           ]
         ]
       },
       {
         "start": 60060,
         "duration": 60060,
         "interval": 15015,
         "events": [
           [
             {
               "windowFaceDistribution": {
                 "neutral": 1,
                 "happiness": 0,
                 "surprise": 0,
                 "sadness": 0,
                 "anger": 0,
                 "disgust": 0,
                 "fear": 0,
                 "contempt": 0
               },
               "windowMeanScores": {
                 "neutral": 0.688541,
                 "happiness": 0.0586323,
                 "surprise": 0.227184,
                 "sadness": 0.00945675,
                 "anger": 0.00592107,
                 "disgust": 0.00154993,
                 "fear": 0.00450447,
                 "contempt": 0.0042109
               }
             }
           ],
           [
             {
               "windowFaceDistribution": {
                 "neutral": 1,
                 "happiness": 0,
                 "surprise": 0,
                 "sadness": 0,
                 "anger": 0,
                 "disgust": 0,
                 "fear": 0,

## <a name="limitations"></a>Limitações
* Os formatos de vídeos de entrada suportados incluem MP4, MOV e WMV.
* O intervalo de tamanho de rostos detetável é 24 x 24 para 2048 x 2048 pixéis. Não serão detetados faces fora deste intervalo.
* Para cada vídeo, o número máximo de faces devolvido é 64.
* Alguns faces não seja detetados devido a dificuldades técnicas; Por exemplo, muito grandes enfrentam os ângulos (implicar head) e occlusion grande. Faces frontal e perto frontal tem obter os melhores resultados.

## <a name="net-sample-code"></a>Código de exemplo do .NET

O seguinte programa mostra como:

1. Criar um elemento e carregue um ficheiro de suporte de dados para o elemento.
2. Crie uma tarefa com uma tarefa de deteção de rostos em baseado num ficheiro de configuração que contém a seguinte predefinição de json. 
   
        {
            "version": "1.0"
        }
3. Transferir os ficheiros JSON de saída. 

#### <a name="create-and-configure-a-visual-studio-project"></a>Criar e configurar um projeto de Visual Studio

Configure o seu ambiente de desenvolvimento e preencha o ficheiro app.config com informações da ligação, conforme descrito em [Media Services development with .NET](media-services-dotnet-how-to-use.md) (Desenvolvimento de Serviços de Multimédia com .NET). 

#### <a name="example"></a>Exemplo

    using System;
    using System.Configuration;
    using System.IO;
    using System.Linq;
    using Microsoft.WindowsAzure.MediaServices.Client;
    using System.Threading;
    using System.Threading.Tasks;

    namespace FaceDetection
    {
        class Program
        {
            private static readonly string _AADTenantDomain =
                      ConfigurationManager.AppSettings["AADTenantDomain"];
            private static readonly string _RESTAPIEndpoint =
                      ConfigurationManager.AppSettings["MediaServiceRESTAPIEndpoint"];

            // Field for service context.
            private static CloudMediaContext _context = null;

            static void Main(string[] args)
            {
                var tokenCredentials = new AzureAdTokenCredentials(_AADTenantDomain, AzureEnvironments.AzureCloudEnvironment);
                var tokenProvider = new AzureAdTokenProvider(tokenCredentials);

                _context = new CloudMediaContext(new Uri(_RESTAPIEndpoint), tokenProvider);

                // Run the FaceDetection job.
                var asset = RunFaceDetectionJob(@"C:\supportFiles\FaceDetection\BigBuckBunny.mp4",
                                            @"C:\supportFiles\FaceDetection\config.json");

                // Download the job output asset.
                DownloadAsset(asset, @"C:\supportFiles\FaceDetection\Output");
            }

            static IAsset RunFaceDetectionJob(string inputMediaFilePath, string configurationFile)
            {
                // Create an asset and upload the input media file to storage.
                IAsset asset = CreateAssetAndUploadSingleFile(inputMediaFilePath,
                    "My Face Detection Input Asset",
                    AssetCreationOptions.None);

                // Declare a new job.
                IJob job = _context.Jobs.Create("My Face Detection Job");

                // Get a reference to Azure Media Face Detector.
                string MediaProcessorName = "Azure Media Face Detector";

                var processor = GetLatestMediaProcessorByName(MediaProcessorName);

                // Read configuration from the specified file.
                string configuration = File.ReadAllText(configurationFile);

                // Create a task with the encoding details, using a string preset.
                ITask task = job.Tasks.AddNew("My Face Detection Task",
                    processor,
                    configuration,
                    TaskOptions.None);

                // Specify the input asset.
                task.InputAssets.Add(asset);

                // Add an output asset to contain the results of the job.
                task.OutputAssets.AddNew("My Face Detectoion Output Asset", AssetCreationOptions.None);

                // Use the following event handler to check job progress.  
                job.StateChanged += new EventHandler<JobStateChangedEventArgs>(StateChanged);

                // Launch the job.
                job.Submit();

                // Check job execution and wait for job to finish.
                Task progressJobTask = job.GetExecutionProgressTask(CancellationToken.None);

                progressJobTask.Wait();

                // If job state is Error, the event handling
                // method for job progress should log errors.  Here we check
                // for error state and exit if needed.
                if (job.State == JobState.Error)
                {
                    ErrorDetail error = job.Tasks.First().ErrorDetails.First();
                    Console.WriteLine(string.Format("Error: {0}. {1}",
                                                    error.Code,
                                                    error.Message));
                    return null;
                }

                return job.OutputMediaAssets[0];
            }

            static IAsset CreateAssetAndUploadSingleFile(string filePath, string assetName, AssetCreationOptions options)
            {
                IAsset asset = _context.Assets.Create(assetName, options);

                var assetFile = asset.AssetFiles.Create(Path.GetFileName(filePath));
                assetFile.Upload(filePath);

                return asset;
            }

            static void DownloadAsset(IAsset asset, string outputDirectory)
            {
                foreach (IAssetFile file in asset.AssetFiles)
                {
                    file.Download(Path.Combine(outputDirectory, file.Name));
                }
            }

            static IMediaProcessor GetLatestMediaProcessorByName(string mediaProcessorName)
            {
                var processor = _context.MediaProcessors
                    .Where(p => p.Name == mediaProcessorName)
                    .ToList()
                    .OrderBy(p => new Version(p.Version))
                    .LastOrDefault();

                if (processor == null)
                    throw new ArgumentException(string.Format("Unknown media processor",
                                                               mediaProcessorName));

                return processor;
            }

            static private void StateChanged(object sender, JobStateChangedEventArgs e)
            {
                Console.WriteLine("Job state changed event:");
                Console.WriteLine("  Previous state: " + e.PreviousState);
                Console.WriteLine("  Current state: " + e.CurrentState);

                switch (e.CurrentState)
                {
                    case JobState.Finished:
                        Console.WriteLine();
                        Console.WriteLine("Job is finished.");
                        Console.WriteLine();
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
                        // LogJobStop(job.Id);
                        break;
                    default:
                        break;
                }
            }
        }
    }

## <a name="media-services-learning-paths"></a>Percursos de aprendizagem dos Media Services
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Enviar comentários
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

## <a name="related-links"></a>Ligações relacionadas
[Descrição geral da análise de serviços de multimédia do Azure](media-services-analytics-overview.md)

[Demonstrações de análise de multimédia do Azure](http://amslabs.azurewebsites.net/demos/Analytics.html)

