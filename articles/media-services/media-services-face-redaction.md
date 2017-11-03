---
title: "Redact faces análise de multimédia do Azure | Microsoft Docs"
description: "Este tópico demonstra como redact faces com a análise de multimédia do Azure."
services: media-services
documentationcenter: 
author: juliako
manager: cfowler
editor: 
ms.assetid: 5b6d8b8c-5f4d-4fef-b3d6-dc22c6b5a0f5
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 09/27/2017
ms.author: juliako;
ms.openlocfilehash: b3584c5aa5405e7f5acdd9bc0a6573b4acbab855
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="redact-faces-with-azure-media-analytics"></a>Redact faces análise de multimédia do Azure
## <a name="overview"></a>Descrição geral
**Azure Media Redactor** é um [análise de multimédia do Azure](media-services-analytics-overview.md) processador de multimédia (MP) que oferece redaction enfrentam dimensionável na nuvem. Enfrentam redaction permite-lhe modificar o vídeo para blur faces de indivíduos selecionados. Poderá pretender utilizar o serviço de redaction de rostos em cenários de segurança e de suporte de dados de notícias públicos. Alguns minutos de imagens que contém vários faces podem demorar horas a redact manualmente, mas com este serviço, o processo de redaction enfrentam necessitarão de apenas alguns passos simples. Para obter mais informações, consulte [isto](https://azure.microsoft.com/blog/azure-media-redactor/) blogue.

Este tópico fornece detalhes sobre **Redactor de suporte de dados do Azure** e mostra como utilizá-la com o SDK de Media Services para .NET.

## <a name="face-redaction-modes"></a>Modos de redaction de rostos em
Facial redaction funciona ao detetar faces em cada período de vídeo e ao controlar o objeto de rostos em ambos os forwards e para trás no tempo, para que o mesmo individuais podem ser blurred de outros ângulos bem. O processo automatizado redaction é muito complexo e não sempre produzir 100% de saída pretendida, por este motivo, análise de multimédia fornece duas formas de modificar o resultado final.

Para além de um modo totalmente automático, há um fluxo de trabalho de passagem de dois que lhe permite a seleção/de-selection de faces encontrados através de uma lista de IDs. Além disso fazer arbitrários por ajustes de moldura o pacote de gestão utiliza um ficheiro de metadados no formato JSON. Este fluxo de trabalho é dividido em **analisar** e **Redact** modos. Pode combinar os dois modos numa única passagem, que é executado ambas as tarefas numa tarefa; Este modo é chamado **Combined**.

### <a name="combined-mode"></a>Modo combinado
Isto produzirá um mp4 redacted automaticamente sem qualquer manual de entrada.

| Fase | Nome de ficheiro | Notas |
| --- | --- | --- |
| Recurso de entrada |foo.bar |Vídeo no formato WMV, MOV ou MP4 |
| Configuração de entrada |Configuração de tarefa da configuração predefinida |{'version':'1.0 ', 'Opções': {'mode': 'combinado'}} |
| Elemento de saída |foo_redacted.mp4 |Vídeo com diária esbater pessoal aplicada |

#### <a name="input-example"></a>Exemplo de entrada:
[Veja este vídeo](http://ampdemo.azureedge.net/?url=http%3A%2F%2Freferencestream-samplestream.streaming.mediaservices.windows.net%2Fed99001d-72ee-4f91-9fc0-cd530d0adbbc%2FDancing.mp4)

#### <a name="output-example"></a>Exemplo de saída:
[Veja este vídeo](http://ampdemo.azureedge.net/?url=http%3A%2F%2Freferencestream-samplestream.streaming.mediaservices.windows.net%2Fc6608001-e5da-429b-9ec8-d69d8f3bfc79%2Fdance_redacted.mp4)

### <a name="analyze-mode"></a>Analisar modo
O **analisar** passagem do fluxo de trabalho de passagem de dois assume um vídeo de entrada e produz um ficheiro JSON das localizações de rostos em e jpg imagens de cada detetado enfrentam reside.

| Fase | Nome de ficheiro | Notas |
| --- | --- | --- |
| Recurso de entrada |foo.bar |Vídeo no formato WMV, MPV ou MP4 |
| Configuração de entrada |Configuração de tarefa da configuração predefinida |{'version':'1.0 ', 'Opções': {'mode': 'Analisar'}} |
| Elemento de saída |foo_annotations.JSON |Dados de anotação de localizações de rostos no formato JSON. Isto pode ser editado pelo utilizador para modificar o diária esbater pessoal delimitadora caixas. Consulte o exemplo abaixo. |
| Elemento de saída |foo_thumb%06d.jpg [foo_thumb000001.jpg, foo_thumb000002.jpg] |Um jpg cropped de cada detetado enfrentam, onde o número indica labelId do tipo de letra |

#### <a name="output-example"></a>Exemplo de saída:

    {
      "version": 1,
      "timescale": 24000,
      "offset": 0,
      "framerate": 23.976,
      "width": 1280,
      "height": 720,
      "fragments": [
        {
          "start": 0,
          "duration": 48048,
          "interval": 1001,
          "events": [
            [],
            [],
            [],
            [],
            [],
            [],
            [],
            [],
            [],
            [],
            [],
            [],
            [],
            [
              {
                "index": 13,
                "id": 1138,
                "x": 0.29537,
                "y": -0.18987,
                "width": 0.36239,
                "height": 0.80335
              },
              {
                "index": 13,
                "id": 2028,
                "x": 0.60427,
                "y": 0.16098,
                "width": 0.26958,
                "height": 0.57943
              }
            ],

    … truncated

### <a name="redact-mode"></a>Redact modo
A segundo passagem do fluxo de trabalho demora um grande número de entradas que tem de ser combinados um único recurso.

Isto inclui uma lista de IDs de blur, o vídeo original e as anotações JSON. Este modo utiliza as anotações para aplicar diária esbater pessoal no vídeo de entrada.

O resultado da passagem de analisar não inclui o vídeo original. O vídeo tem de ser carregada para o elemento de entrada para a tarefa de modo Redact e selecionada como ficheiro principal.

| Fase | Nome de ficheiro | Notas |
| --- | --- | --- |
| Recurso de entrada |foo.bar |Vídeo no formato WMV, MPV ou MP4. Mesmo vídeo como no passo 1. |
| Recurso de entrada |foo_annotations.JSON |ficheiro de metadados de anotações da primeira fase, com modificações opcionais. |
| Recurso de entrada |foo_IDList.txt (opcional) |Opcional nova linha separados por lista de IDs de redact de letra. Se deixado em branco, isto blurs faces todos os. |
| Configuração de entrada |Configuração de tarefa da configuração predefinida |{'version':'1.0 ', 'Opções': {'mode': 'redact'}} |
| Elemento de saída |foo_redacted.mp4 |Vídeo com diária esbater pessoal aplicadas com base em anotações |

#### <a name="example-output"></a>Exemplo de saída
Este é o resultado de uma IDList com um ID selecionado.

[Veja este vídeo](http://ampdemo.azureedge.net/?url=http%3A%2F%2Freferencestream-samplestream.streaming.mediaservices.windows.net%2Fad6e24a2-4f9c-46ee-9fa7-bf05e20d19ac%2Fdance_redacted1.mp4)

Foo_IDList.txt de exemplo
 
     1
     2
     3

## <a name="blur-types"></a>Blur tipos

No **Combined** ou **Redact** modo, existem 5 blur diferentes modos, pode escolher entre através da configuração de entrada de JSON: **baixa**, **Med**, **Elevada**, **caixa**, e **negra**. Por predefinição **Med** é utilizado.

Pode encontrar exemplos dos tipos blur abaixo.

### <a name="example-json"></a>JSON de exemplo:

    {'version':'1.0', 'options': {'Mode': 'Combined', 'BlurType': 'High'}}

#### <a name="low"></a>Baixo

![Baixo](./media/media-services-face-redaction/blur1.png)
 
#### <a name="med"></a>Med

![Med](./media/media-services-face-redaction/blur2.png)

#### <a name="high"></a>Elevado

![Elevado](./media/media-services-face-redaction/blur3.png)

#### <a name="box"></a>Box

![Box](./media/media-services-face-redaction/blur4.png)

#### <a name="black"></a>Preto

![Preto](./media/media-services-face-redaction/blur5.png)

## <a name="elements-of-the-output-json-file"></a>Elementos do ficheiro de saída JSON

O pacote de gestão Redaction fornece deteção de localização de rostos em precisão elevado e de controlo que pode detetar até 64 faces humanos num intervalo de vídeo. Frontal faces fornecem os melhores resultados, ao lado faces e em pequenos faces (menor ou igual a 24 x 24 pixels) são um desafio.

[!INCLUDE [media-services-analytics-output-json](../../includes/media-services-analytics-output-json.md)]

## <a name="net-sample-code"></a>Código de exemplo do .NET

O seguinte programa mostra como:

1. Criar um elemento e carregue um ficheiro de suporte de dados para o elemento.
2. Crie uma tarefa com uma tarefa de redaction enfrentam baseada num ficheiro de configuração que contém a seguinte predefinição de json. 
   
        {'version':'1.0', 'options': {'mode':'combined'}}
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

    namespace FaceRedaction
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

            // Run the FaceRedaction job.
            var asset = RunFaceRedactionJob(@"C:\supportFiles\FaceRedaction\SomeFootage.mp4",
                        @"C:\supportFiles\FaceRedaction\config.json");

            // Download the job output asset.
            DownloadAsset(asset, @"C:\supportFiles\FaceRedaction\Output");
        }

        static IAsset RunFaceRedactionJob(string inputMediaFilePath, string configurationFile)
        {
            // Create an asset and upload the input media file to storage.
            IAsset asset = CreateAssetAndUploadSingleFile(inputMediaFilePath,
            "My Face Redaction Input Asset",
            AssetCreationOptions.None);

            // Declare a new job.
            IJob job = _context.Jobs.Create("My Face Redaction Job");

            // Get a reference to Azure Media Redactor.
            string MediaProcessorName = "Azure Media Redactor";

            var processor = GetLatestMediaProcessorByName(MediaProcessorName);

            // Read configuration from the specified file.
            string configuration = File.ReadAllText(configurationFile);

            // Create a task with the encoding details, using a string preset.
            ITask task = job.Tasks.AddNew("My Face Redaction Task",
            processor,
            configuration,
            TaskOptions.None);

            // Specify the input asset.
            task.InputAssets.Add(asset);

            // Add an output asset to contain the results of the job.
            task.OutputAssets.AddNew("My Face Redaction Output Asset", AssetCreationOptions.None);

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

## <a name="next-steps"></a>Passos seguintes

[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Enviar comentários
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

## <a name="related-links"></a>Ligações relacionadas
[Descrição geral da análise de serviços de multimédia do Azure](media-services-analytics-overview.md)

[Demonstrações de análise de multimédia do Azure](http://azuremedialabs.azurewebsites.net/demos/Analytics.html)

