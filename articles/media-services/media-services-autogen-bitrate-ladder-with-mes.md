---
title: "Utilizar o Azure codificador de multimédia Standard para gerar automaticamente um ladder de velocidade de transmissão | Microsoft Docs"
description: "Este tópico mostra como utilizar suportes de dados codificador padrão (MES) para gerar automaticamente um ladder de velocidade de transmissão com base na entrada de resolução e velocidade de transmissão. A entrada de resolução e velocidade de transmissão nunca irão ser excedidos. Por exemplo, se a entrada é 720p em 3 Mbps, será de saída permanecem 720p, melhor e iniciará às taxas de inferiores a 3 Mbps."
services: media-services
documentationcenter: 
author: juliako
manager: cfowler
editor: 
ms.assetid: 63ed95da-1b82-44b0-b8ff-eebd535bc5c7
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/20/2017
ms.author: juliako
ms.openlocfilehash: b5616aa9f8b15ab576d914fbae89a56f64c27f4a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
#  <a name="use-azure-media-encoder-standard-to-auto-generate-a-bitrate-ladder"></a>Utilizar o Azure codificador de multimédia Standard para gerar automaticamente um ladder de velocidade de transmissão

## <a name="overview"></a>Descrição geral

Este tópico mostra como utilizar suportes de dados codificador padrão (MES) para gerar automaticamente um ladder de velocidade de transmissão (pares de resolução de velocidade de transmissão) com base na entrada de resolução e velocidade de transmissão. A predefinição de geração automática nunca irá exceder a entrada de resolução e velocidade de transmissão. Por exemplo, se a entrada é 720p em 3 Mbps, será de saída permanecem 720p, melhor e iniciará às taxas de inferiores a 3 Mbps.

### <a name="encoding-for-streaming-only"></a>Codificação de transmissão em fluxo só

Se a sua intenção for codificar o seu vídeo de origem apenas para transmissão em fluxo, shoud utilize o "transmissão em fluxo adaptável" quando criar uma tarefa de codificação da configuração predefinida. Ao utilizar o **transmissão em fluxo adaptável** da configuração predefinida, o codificador MES será inteligentemente cap ladder uma velocidade de transmissão. No entanto, não será possível controlar a codificação de custos, uma vez que o serviço determina quantas camadas para utilizar e em que resolução. Pode ver os exemplos de camadas de saída produzidas pela MES como resultado de codificação com a **transmissão em fluxo adaptável** predefinido no final deste tópico. Não são interleaved a saída Asset irá conter ficheiros MP4 onde áudio e vídeo.

### <a name="encoding-for-streaming-and-progressive-download"></a>Codificação de transmissão em fluxo e transferência progressiva

Se a sua intenção for para codificar o seu vídeo de origem para transmissão em fluxo, bem como para produzir ficheiros MP4 para transferência progressiva, shoud utilize o "conteúdo adaptável vários velocidade de transmissão MP4" quando criar uma tarefa de codificação da configuração predefinida. Ao utilizar o **conteúdo MP4 de velocidade de transmissão adaptável vários** da configuração predefinida, o codificador MES será aplicada a mesma lógica de codificação, conforme apresentado acima, mas agora o elemento de saída conterá os ficheiros MP4 onde áudio e vídeo são interleaved. Pode utilizar um destes ficheiros MP4 (por exemplo, a versão de velocidade de transmissão mais recente) como um ficheiro de transferência progressiva.

## <a id="encoding_with_dotnet"></a>Encoding com Media Services .NET SDK

Exemplo de código seguinte utiliza o SDK .NET dos Media Services para realizar as seguintes tarefas:

- Crie uma tarefa de codificação.
- Obter uma referência ao codificador codificador de multimédia Standard.
- Adicionar uma tarefa de codificação para a tarefa e especifique a utilização de **transmissão em fluxo adaptável** predefinidas. 
- Crie um elemento de saída que irá conter o elemento codificado.
- Adicione um processador de eventos para verificar o progresso da tarefa.
- Submeta a tarefa.

#### <a name="create-and-configure-a-visual-studio-project"></a>Criar e configurar um projeto de Visual Studio

Configure o seu ambiente de desenvolvimento e preencha o ficheiro app.config com informações da ligação, conforme descrito em [Media Services development with .NET](media-services-dotnet-how-to-use.md) (Desenvolvimento de Serviços de Multimédia com .NET). 

#### <a name="example"></a>Exemplo

    using System;
    using System.Configuration;
    using System.Linq;
    using Microsoft.WindowsAzure.MediaServices.Client;
    using System.Threading;

    namespace AdaptiveStreamingMESPresest
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

            // Get an uploaded asset.
            var asset = _context.Assets.FirstOrDefault();

            // Encode and generate the output using the "Adaptive Streaming" preset.
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

            // Create a task
            ITask task = job.Tasks.AddNew("Media Encoder Standard encoding task",
            processor,
            "Adaptive Streaming",
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

## <a id="output"></a>Saída

Esta secção mostra exemplos de três camadas de saída produzidas pela MES como resultado de codificação com a **transmissão em fluxo adaptável** predefinidas. 

### <a name="example-1"></a>Exemplo 1
A origem com altura "1080" e framerate "29.970" produz 6 camadas vídeos:

|Camada|Altura|Largura|Bitrate(Kbps)|
|---|---|---|---|
|1|1080|1920|6780|
|2|720|1280|3520|
|3|540|960|2210|
|4|360|640|1150|
|5|270|480|720|
|6|180|320|380|

### <a name="example-2"></a>Exemplo 2
A origem com altura "720" e framerate "23.970" produz 5 camadas vídeos:

|Camada|Altura|Largura|Bitrate(Kbps)|
|---|---|---|---|
|1|720|1280|2940|
|2|540|960|1850|
|3|360|640|960|
|4|270|480|600|
|5|180|320|320|

### <a name="example-3"></a>Exemplo 3
A origem com altura "360" e framerate "29.970" produz de 3 camadas vídeos:

|Camada|Altura|Largura|Bitrate(Kbps)|
|---|---|---|---|
|1|360|640|700|
|2|270|480|440|
|3|180|320|230|
## <a name="media-services-learning-paths"></a>Percursos de aprendizagem dos Media Services
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Enviar comentários
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

## <a name="see-also"></a>Veja Também
[Descrição geral de codificação de serviços de multimédia](media-services-encode-asset.md)

