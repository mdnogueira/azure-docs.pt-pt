---
title: "Digitize texto com OCR de análise de multimédia do Azure | Microsoft Docs"
description: "OCR de análise de multimédia do Azure (reconhecimento de caráter optical) permite-lhe converter o conteúdo de texto nos ficheiros de vídeos num editável, pesquisável texto digital.  Isto permite-lhe automatizar a extração dos metadados significativo do sinal de vídeo do suporte de dados."
services: media-services
documentationcenter: 
author: juliako
manager: cfowler
editor: 
ms.assetid: 307c196e-3a50-4f4b-b982-51585448ffc6
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 07/31/2017
ms.author: juliako
ms.openlocfilehash: 43f5b3a9bbec243e668c79702045094fcfedbdda
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="use-azure-media-analytics-to-convert-text-content-in-video-files-into-digital-text"></a>Utilize a análise de multimédia do Azure para converter o conteúdo de texto nos ficheiros de vídeos num texto digital
## <a name="overview"></a>Descrição geral
Se precisar de extrair conteúdo de texto de ficheiros de vídeo e gerar um texto editável, pesquisável digital, deve utilizar OCR de análise de suporte de dados do Azure (reconhecimento de caráter optical). Este processador de multimédia do Azure Deteta o conteúdo de texto nos ficheiros de vídeos e gera ficheiros de texto para utilização. OCR permite-lhe automatizar a extração dos metadados significativo do sinal de vídeo do suporte de dados.

Quando utilizado em conjunto com um motor de busca, pode facilmente o suporte de dados de índice por texto e melhorar a capacidade de deteção do seu conteúdo. Isto é extremamente útil altamente textual vídeo, como um vídeo gravação ou a captura de ecrã de uma apresentação em slideshow. O processador de multimédia do Azure OCR está otimizado para texto digital.

O **OCR de suporte de dados do Azure** processador de multimédia está atualmente em pré-visualização.

Este tópico fornece detalhes sobre **OCR de suporte de dados do Azure** e mostra como utilizá-la com o SDK de Media Services para .NET. Para obter informações adicionais e exemplos, consulte [este blogue](https://azure.microsoft.com/blog/announcing-video-ocr-public-preview-new-config/).

## <a name="ocr-input-files"></a>Ficheiros de entrada OCR
Ficheiros de vídeo. Atualmente, são suportados os seguintes formatos: MP4, MOV e WMV.

## <a name="task-configuration"></a>Configuração da tarefa
Configuração da tarefa (predefinição). Quando criar uma tarefa com **OCR de suporte de dados do Azure**, tem de especificar uma configuração utilizando JSON ou XML da configuração predefinida. 

>[!NOTE]
>O motor de OCR demora apenas uma região de imagem com o mínimo 40 pixels a 32000 pixels máximos como um valor válido em ambos os largura/altura.
>

### <a name="attribute-descriptions"></a>Descrições de atributo
| Nome do atributo | Descrição |
| --- | --- |
|AdvancedOutput| Se definir AdvancedOutput como VERDADEIRO, a saída JSON irá conter dados posicionais para cada uma única palavra (além expressões e regiões). Se não pretender ver estes detalhes, defina o sinalizador como false. O valor predefinido é falso. Para obter mais informações, consulte [este blogue](https://azure.microsoft.com/blog/azure-media-ocr-simplified-output/).|
| Idioma |(opcional), descreve o idioma de texto para o qual pretende procurar. Um dos seguintes: AutoDetect (predefinição), árabe, ChineseSimplified, ChineseTraditional, checo dinamarquês, neerlandês, inglês, finlandês, francês, alemão, grego, húngaro, italiano, japonês, coreano, norueguês, polaco, português, romeno, russo, SerbianCyrillic SerbianLatin, eslovaco, espanhol, sueco, turco. |
| TextOrientation |(opcional) descreve a orientação do texto para o qual pretende procurar.  "Left" significa que a parte superior de todas as letras são indicada para a esquerda.  Texto predefinido (por exemplo, que pode ser encontrado num livro) pode ser chamado "Cópia de segurança" orientado para.  Um dos seguintes: AutoDetect (predefinição), até, à direita, baixo, à esquerda. |
| TimeInterval |(opcional) descreve a frequência de amostragem.  Predefinição é a cada segundo 1/2.<br/>Formato JSON – hh: mm:. SSS (00:00:00.500 predefinido)<br/>Formato XML – W3C XSD duração primitivo (predefinição PT0.5) |
| DetectRegions |(opcional) Uma matriz de objetos de DetectRegion especificando regiões dentro da moldura de vídeo no qual pretende detetar texto.<br/>Um objeto de DetectRegion é constituído pelos seguintes valores de número inteiro de quatro:<br/>À esquerda – pixels da margem da esquerda<br/>Parte superior – pixels da margem da parte superior<br/>Largura – largura da região em pixéis<br/>Altura – altura da região em pixéis |

#### <a name="json-preset-example"></a>Exemplo de configuração predefinida JSON

    {
        "Version":1.0, 
        "Options": 
        {
            "AdvancedOutput":"true",
            "Language":"English", 
            "TimeInterval":"00:00:01.5",
            "TextOrientation":"Up",
            "DetectRegions": [
                    {
                       "Left": 10,
                       "Top": 10,
                       "Width": 100,
                       "Height": 50
                    }
             ]
        }
    }


#### <a name="xml-preset-example"></a>Exemplo de configuração predefinida XML
    <?xml version=""1.0"" encoding=""utf-16""?>
    <VideoOcrPreset xmlns:xsi=""http://www.w3.org/2001/XMLSchema-instance"" xmlns:xsd=""http://www.w3.org/2001/XMLSchema"" Version=""1.0"" xmlns=""http://www.windowsazure.com/media/encoding/Preset/2014/03"">
      <Options>
         <AdvancedOutput>true</AdvancedOutput>
         <Language>English</Language>
         <TimeInterval>PT1.5S</TimeInterval>
         <DetectRegions>
             <DetectRegion>
                   <Left>10</Left>
                   <Top>10</Top>
                   <Width>100</Width>
                   <Height>50</Height>
            </DetectRegion>
       </DetectRegions>
       <TextOrientation>Up</TextOrientation>
      </Options>
    </VideoOcrPreset>

## <a name="ocr-output-files"></a>Ficheiros de saída OCR
O resultado do processador de multimédia OCR é um ficheiro JSON.

### <a name="elements-of-the-output-json-file"></a>Elementos do ficheiro de saída JSON
A saída de vídeo OCR fornece dados de tempo-segmentado carateres encontrado as vídeo.  Pode utilizar atributos, tais como o idioma ou de orientação para hone-se nas exatamente as palavras que está interessado na análise. 

A saída contém os seguintes atributos:

| Elemento | Descrição |
| --- | --- |
| escala temporal |"ticks" por segundo do vídeo |
| Desvio |tempo de desfasamento para os carimbos. Na versão 1.0 do vídeo APIs, este será sempre 0. |
| framerate |Fotogramas por segundo do vídeo |
| Largura |largura do vídeo em pixéis |
| Altura |altura do vídeo em pixéis |
| fragmentos |matriz de segmentos baseados no tempo de vídeo no qual os metadados é partes |
| start |hora de início de um fragmento de "ticks" |
| Duração |comprimento de um fragmento de "ticks" |
| intervalo |intervalo de cada evento dentro de fragmento indicado |
| eventos |matriz contendo regiões |
| Região |objeto que representa detetado palavras ou frases reconhecíveis |
| idioma |idioma do texto detetado numa região |
| orientação |orientação do texto detetado numa região |
| linhas |matriz de linhas de texto detetado numa região |
| Texto |o texto real |

### <a name="json-output-example"></a>Exemplo de saída JSON
O seguinte exemplo de saída contém as informações gerais de vídeos e alguns fragmentos de vídeos. Todas as vídeo fragmento, contém cada região que é detetada pelo OCR pacote de gestão com o idioma e a orientação de texto. A região também contém todas as linhas de word nesta região com o texto da linha, posição da linha e cada palavra informações (word, posição e conteúdo confiança) esta linha. Segue-se um exemplo e posso colocar alguns inline de comentários.

    {
        "version": 1, 
        "timescale": 90000, 
        "offset": 0, 
        "framerate": 30, 
        "width": 640, 
        "height": 480,  // general video information
        "fragments": [
            {
                "start": 0, 
                "duration": 180000, 
                "interval": 90000,  // the time information about this fragment
                "events": [
                    [
                       { 
                            "region": { // the detected region array in this fragment 
                                "language": "English",  // region language
                                "orientation": "Up",  // text orientation
                                "lines": [  // line information array in this region, including the text and the position
                                    {
                                        "text": "One Two", 
                                        "left": 10, 
                                        "top": 10, 
                                        "right": 210, 
                                        "bottom": 110, 
                                        "word": [  // word information array in this line
                                            {
                                                "text": "One", 
                                                "left": 10, 
                                                "top": 10, 
                                                "right": 110, 
                                                "bottom": 110, 
                                                "confidence": 900
                                            }, 
                                            {
                                                "text": "Two", 
                                                "left": 110, 
                                                "top": 10, 
                                                "right": 210, 
                                                "bottom": 110, 
                                                "confidence": 910
                                            }
                                        ]
                                    }
                                ]
                            }
                        }
                    ]
                ]
            }
        ]
    }

## <a name="net-sample-code"></a>Código de exemplo do .NET

O seguinte programa mostra como:

1. Criar um elemento e carregue um ficheiro de suporte de dados para o elemento.
2. Crie uma tarefa com um ficheiro de configuração/predefinição OCR.
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

    namespace OCR
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

                // Run the OCR job.
                var asset = RunOCRJob(@"C:\supportFiles\OCR\presentation.mp4",
                                            @"C:\supportFiles\OCR\config.json");

                // Download the job output asset.
                DownloadAsset(asset, @"C:\supportFiles\OCR\Output");
            }

            static IAsset RunOCRJob(string inputMediaFilePath, string configurationFile)
            {
                // Create an asset and upload the input media file to storage.
                IAsset asset = CreateAssetAndUploadSingleFile(inputMediaFilePath,
                    "My OCR Input Asset",
                    AssetCreationOptions.None);

                // Declare a new job.
                IJob job = _context.Jobs.Create("My OCR Job");

                // Get a reference to Azure Media OCR.
                string MediaProcessorName = "Azure Media OCR";

                var processor = GetLatestMediaProcessorByName(MediaProcessorName);

                // Read configuration from the specified file.
                string configuration = File.ReadAllText(configurationFile);

                // Create a task with the encoding details, using a string preset.
                ITask task = job.Tasks.AddNew("My OCR Task",
                    processor,
                    configuration,
                    TaskOptions.None);

                // Specify the input asset.
                task.InputAssets.Add(asset);

                // Add an output asset to contain the results of the job.
                task.OutputAssets.AddNew("My OCR Output Asset", AssetCreationOptions.None);

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

