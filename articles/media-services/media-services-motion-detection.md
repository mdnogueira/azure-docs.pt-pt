---
title: "Detetar Motions com a análise de multimédia do Azure | Microsoft Docs"
description: "O processador de multimédia Detector de movimento de suporte de dados do Azure (MP) permite-lhe identificar eficientemente secções de interesse num vídeo longo e uneventful caso contrário."
services: media-services
documentationcenter: 
author: juliako
manager: cfowler
editor: 
ms.assetid: d144f813-1a55-442f-a895-5c4cb6d0aeae
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 07/31/2017
ms.author: milanga;juliako;
ms.openlocfilehash: 115ad9dfd88062f23d5d17eed8897ce5d2ca8484
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="detect-motions-with-azure-media-analytics"></a>Detetar Motions com a análise de multimédia do Azure
## <a name="overview"></a>Descrição geral
O **Detector de movimento de suporte de dados do Azure** processador de multimédia (MP) permite-lhe identificar eficientemente secções de interesse num vídeo longo e uneventful caso contrário. Deteção de movimento pode ser utilizada no filmagens de câmaras estático para identificar secções do vídeo onde ocorre o movimento. Gera um ficheiro JSON que contém um metadados com carimbos região e o delimitador onde o evento ocorreu.

Esta tecnologia direcionado para os feeds de vídeo de segurança, é capaz de categorizar movimento eventos relevantes e falsos positivos, tais como sombras e alterações de lighting. Isto permite-lhe gerar alertas de segurança a partir de feeds da câmara sem ser spammed com eventos irrelevantes endless, ao conseguir extrair instantes de interesse de vídeos de vigilância extremamente longos.

O **Detector de movimento de suporte de dados do Azure** MP está atualmente em pré-visualização.

Este tópico fornece detalhes sobre **Detector de movimento de suporte de dados do Azure** e mostra como utilizá-la com o SDK de Media Services para .NET

## <a name="motion-detector-input-files"></a>Ficheiros de entrada Detector de movimento
Ficheiros de vídeo. Atualmente, são suportados os seguintes formatos: MP4, MOV e WMV.

## <a name="task-configuration-preset"></a>Configuração da tarefa (predefinição)
Quando criar uma tarefa com **Detector de movimento de suporte de dados do Azure**, tem de especificar uma predefinição de configuração. 

### <a name="parameters"></a>Parâmetros
Pode utilizar os seguintes parâmetros:

| Nome | Opções | Descrição | Predefinição |
| --- | --- | --- | --- |
| sensitivityLevel |Cadeia: 'baixa', 'Média', 'Alta' |Define o nível de sensibilidade em quais motions é comunicado. Ajuste esta opção para ajustar a quantidade de falsos positivos. |'Média' |
| frameSamplingValue |Número inteiro positivo |Define a frequência em que executa de algoritmo. cada intervalo de igual a 1, 2 significa cada 2nd moldura e assim sucessivamente. |1 |
| detectLightChange |Booleano: 'true', 'false' |Define se leve alterações são reportadas nos resultados |'False' |
| mergeTimeThreshold |Tempo de xs: Hh: mm:<br/>Exemplo: 00:00:03 |Especifica o intervalo de tempo entre os eventos de movimento onde 2 eventos serão combinados e reportados como 1. |00:00:00 |
| detectionZones |Uma matriz de zonas de Deteção:<br/>-Deteção zona é uma matriz de 3 ou mais pontos<br/>-Ponto é um x e y coordenada de 0 a 1. |Descreve a lista das zonas de deteção poligonais na ser utilizado.<br/>Resultados serão comunicados com as zonas como um ID, com o primeiro um que está a ser 'id': 0 |Zona única que abrange o intervalo completo. |

### <a name="json-example"></a>Exemplo JSON
    {
      "version": "1.0",
      "options": {
        "sensitivityLevel": "medium",
        "frameSamplingValue": 1,
        "detectLightChange": "False",
        "mergeTimeThreshold":
        "00:00:02",
        "detectionZones": [
          [
            {"x": 0, "y": 0},
            {"x": 0.5, "y": 0},
            {"x": 0, "y": 1}
           ],
          [
            {"x": 0.3, "y": 0.3},
            {"x": 0.55, "y": 0.3},
            {"x": 0.8, "y": 0.3},
            {"x": 0.8, "y": 0.55},
            {"x": 0.8, "y": 0.8},
            {"x": 0.55, "y": 0.8},
            {"x": 0.3, "y": 0.8},
            {"x": 0.3, "y": 0.55}
          ]
        ]
      }
    }


## <a name="motion-detector-output-files"></a>Ficheiros de saída do movimento Detector
Uma tarefa de deteção de movimento irá devolver um ficheiro JSON no elemento de saída que descreve os alertas de movimento e a respetiva categorias, dentro de vídeo. O ficheiro irá conter informações sobre a hora e a duração de movimento detectada as vídeo.

A API de Detector movimento fornece indicadores assim que existem objetos em movimento um fundo fixo vídeo (por exemplo, um vigilância vídeo). O movimento Detector está preparado para reduzir alarmes false, tais como iluminação da e alterações de sombra de volumes. Limitações atuais dos algoritmos de incluem vídeos de visão as noites, por transparentes objetos e pequenos objetos.

### <a id="output_elements"></a>Elementos do ficheiro de saída JSON
> [!NOTE]
> A versão mais recente, o formato JSON de saída foi alterado e pode representar uma alteração inovadora para alguns clientes.
> 
> 

A tabela seguinte descreve elementos do ficheiro de saída JSON.

| Elemento | Descrição |
| --- | --- |
| Versão |Isto refere-se para a versão da API de vídeo. A versão atual é 2. |
| escala temporal |"Ticks" por segundo do vídeo. |
| Desvio |O desvio da hora para carimbos de hora em "ticks". Na versão 1.0 do vídeo APIs, este será sempre 0. No futuro cenários que suportamos, pode alterar este valor. |
| framerate |Fotogramas por segundo do vídeo. |
| Largura, Altura |Refere-se a largura e altura do vídeo em pixéis. |
| Iniciar |O carimbo de início no "ticks". |
| Duração |O comprimento do evento, em "ticks". |
| intervalo |Intervalo de cada entrada no evento, em "ticks". |
| Eventos |Cada fragmento de evento contém o movimento detetado dentro desse duração de tempo. |
| Tipo |A versão atual, este é sempre '2' para movimento genérico. Esta etiqueta de fornecem APIs de vídeo a flexibilidade para categorizar de movimento em futuras versões. |
| RegionID |Tal como explicado anteriormente, este será sempre 0 nesta versão. Esta etiqueta dá-API de vídeo a flexibilidade para localizar o movimento em várias regiões em versões futuras. |
| Regiões |Refere-se para a área do seu vídeo de onde que mais lhe interessam movimento. <br/><br/>-"id" representa a área de região – nesta versão houver apenas um ID de 0. <br/>-"type" representa a forma da região que mais lhe interessam para movimento. Atualmente, são suportados "retângulo" e "polígono".<br/> Se tiver especificado "retângulo", a região tem dimensões no X, Y, largura e altura. As coordenadas X e Y representam as coordenadas XY superior esquerda da região na escala normalizada de entre 0,0 e 1,0. A largura e altura representam o tamanho da região na escala normalizada de entre 0,0 e 1,0. Na versão atual, os X, Y, largura e altura sempre estiverem corrigidos, 0, 0 e 1, 1. <br/>Se tiver especificado "polígono", a região tem dimensões em pontos. <br/> |
| fragmentos |Os metadados é partes cópias de segurança em diferentes segmentos chamados fragmentos. Cada fragmento contém um início, a duração, o número do intervalo e o evento (s). Um fragmento com não há eventos significa que nenhum movimento foi detetado durante essa hora de início e a duração. |
| Parênteses Retos] |Cada Reto representa um intervalo no evento. Foi detetado o Retos vazios para esse intervalo não significa que nenhum movimento. |
| localizações |Esta nova entrada em eventos apresenta a localização onde ocorreu o movimento. Este é mais específico que as zonas de deteção. |

Segue-se um exemplo de saída JSON

    {
      "version": 2,
      "timescale": 23976,
      "offset": 0,
      "framerate": 24,
      "width": 1280,
      "height": 720,
      "regions": [
        {
          "id": 0,
          "type": "polygon",
          "points": [{'x': 0, 'y': 0},
            {'x': 0.5, 'y': 0},
            {'x': 0, 'y': 1}]
        }
      ],
      "fragments": [
        {
          "start": 0,
          "duration": 226765
        },
        {
          "start": 226765,
          "duration": 47952,
          "interval": 999,
          "events": [
            [
              {
                "type": 2,
                "typeName": "motion",
                "locations": [
                  {
                    "x": 0.004184,
                    "y": 0.007463,
                    "width": 0.991667,
                    "height": 0.985185
                  }
                ],
                "regionId": 0
              }
            ],

    …
## <a name="limitations"></a>Limitações
* Os formatos de vídeos de entrada suportados incluem MP4, MOV e WMV.
* Deteção de movimento está otimizada para vídeos estacionário em segundo plano. O algoritmo centra-se em reduzir alarmes false, tais como alterações lighting e sombras.
* Alguns movimento não pode ser detetado devido a dificuldades técnicas; Por exemplo, as noites visão vídeos, objetos por transparentes e pequenos objetos.

## <a name="net-sample-code"></a>Código de exemplo do .NET

O seguinte programa mostra como:

1. Criar um elemento e carregue um ficheiro de suporte de dados para o elemento.
2. Crie uma tarefa com uma tarefa de deteção de movimento em vídeo baseada num ficheiro de configuração que contém a seguinte predefinição de json. 
   
        {
          "Version": "1.0",
          "Options": {
            "SensitivityLevel": "medium",
            "FrameSamplingValue": 1,
            "DetectLightChange": "False",
            "MergeTimeThreshold":
            "00:00:02",
            "DetectionZones": [
              [
                {"x": 0, "y": 0},
                {"x": 0.5, "y": 0},
                {"x": 0, "y": 1}
               ],
              [
                {"x": 0.3, "y": 0.3},
                {"x": 0.55, "y": 0.3},
                {"x": 0.8, "y": 0.3},
                {"x": 0.8, "y": 0.55},
                {"x": 0.8, "y": 0.8},
                {"x": 0.55, "y": 0.8},
                {"x": 0.3, "y": 0.8},
                {"x": 0.3, "y": 0.55}
              ]
            ]
          }
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

    namespace VideoMotionDetection
    {
        class Program
        {
            // Read values from the App.config file.
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

                // Run the VideoMotionDetection job.
                var asset = RunVideoMotionDetectionJob(@"C:\supportFiles\VideoMotionDetection\BigBuckBunny.mp4",
                                            @"C:\supportFiles\VideoMotionDetection\config.json");

                // Download the job output asset.
                DownloadAsset(asset, @"C:\supportFiles\VideoMotionDetection\Output");
            }

            static IAsset RunVideoMotionDetectionJob(string inputMediaFilePath, string configurationFile)
            {
                // Create an asset and upload the input media file to storage.
                IAsset asset = CreateAssetAndUploadSingleFile(inputMediaFilePath,
                    "My Video Motion Detection Input Asset",
                    AssetCreationOptions.None);

                // Declare a new job.
                IJob job = _context.Jobs.Create("My Video Motion Detection Job");

                // Get a reference to Azure Media Motion Detector.
                string MediaProcessorName = "Azure Media Motion Detector";

                var processor = GetLatestMediaProcessorByName(MediaProcessorName);

                // Read configuration from the specified file.
                string configuration = File.ReadAllText(configurationFile);

                // Create a task with the encoding details, using a string preset.
                ITask task = job.Tasks.AddNew("My Video Motion Detection Task",
                    processor,
                    configuration,
                    TaskOptions.None);

                // Specify the input asset.
                task.InputAssets.Add(asset);

                // Add an output asset to contain the results of the job.
                task.OutputAssets.AddNew("My Video Motion Detectoion Output Asset", AssetCreationOptions.None);

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
[Blogue de Detector de movimento de serviços de suporte de dados do Azure](https://azure.microsoft.com/blog/motion-detector-update/)

[Descrição geral da análise de serviços de multimédia do Azure](media-services-analytics-overview.md)

[Demonstrações de análise de multimédia do Azure](http://azuremedialabs.azurewebsites.net/demos/Analytics.html)

