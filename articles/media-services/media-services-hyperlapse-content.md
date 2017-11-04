---
title: "Ficheiros Hyperlapse de multimédia com Hyperlapse de multimédia do Azure | Microsoft Docs"
description: "Hyperlapse de multimédia do Azure cria uniforme vídeos passado o tempo do conteúdo primeira pessoa ou ação câmara. Este tópico mostra como utilizar o indexador de suporte de dados."
services: media-services
documentationcenter: 
author: asolanki
manager: johndeu
editor: 
ms.assetid: 37d54db6-9cf3-4ae9-b3c6-0d29c744e965
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 03/02/2017
ms.author: adsolank
ms.openlocfilehash: 02f634c2af04b6b372642ab0e6a17a5d29f16450
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="hyperlapse-media-files-with-azure-media-hyperlapse"></a>Ficheiros Hyperlapse de multimédia com Hyperlapse de multimédia do Azure
Hyperlapse de multimédia do Azure é um suporte de dados de processador (MP) que cria uniforme vídeos passado o tempo do conteúdo primeira pessoa ou ação câmara.  O colateral baseado na nuvem para [Microsoft Research Pro de Hyperlapse ambiente de trabalho e móvel de Hyperlapse baseada no telefone](http://aka.ms/hyperlapse), Microsoft Hyperlapse de Media Services do Azure utiliza a grande escala do processamento do suporte de dados do Azure suporte de dados serviços plataforma horizontalmente dimensionar e parallelize em massa Hyperlapse de processamento.

> [!IMPORTANT]
> Microsoft Hyperlapse foi concebida para funcionar melhor no conteúdo da primeira pessoa com uma câmara mover.  Embora filmagens de câmaras ainda podem continuarão a funcionar, o desempenho e a qualidade do processador de multimédia de Hyperlapse de suporte de dados do Azure, não poderá ser garantidos para outros tipos de conteúdo.  Para saber mais sobre o Microsoft Hyperlapse de Media Services do Azure e ver alguns vídeos de exemplo, consulte o [blogue introdutórias](http://aka.ms/azurehyperlapseblog) partir da pré-visualização pública.
> 
> 

Um Hyperlapse de multimédia do Azure tarefa demora como um ficheiro de recurso MP4, MOV ou WMV juntamente com um ficheiro de configuração que especifica quais frames de vídeo devem ter passado o tempo e a que velocidade de entrada (por exemplo, 10 000 primeiro enquadra-se em 2 x).  O resultado é um rendition stabilized e passado o tempo do vídeo de entrada.

As atualizações mais recentes do Hyperlapse de multimédia do Azure, consulte [blogues de Media Services](https://azure.microsoft.com/blog/topics/media-services/).

## <a name="hyperlapse-an-asset"></a>Um recurso de Hyperlapse
Primeiro terá de carregar o ficheiro de entrada pretendido para Media Services do Azure.  Para saber mais sobre os conceitos envolvidos carregar e gerir conteúdo, leia o [artigo gestão de conteúdos](media-services-portal-vod-get-started.md).

### <a id="configuration"></a>Predefinição de configuração de Hyperlapse
Assim que o conteúdo estiver na sua conta de Media Services, terá de construir a predefinição de configuração.  A tabela seguinte explica os campos especificados de utilizador:

| Campo | Descrição |
| --- | --- |
| StartFrame |A frame no qual o processamento de Microsoft Hyperlapse deve começar. |
| NumFrames |O número de fotogramas processar |
| Velocidade |O fator com a qual acelerar o vídeo de entrada. |

Segue-se um exemplo de um ficheiro de configuração compatível nos XML e JSON:

**Predefinição XML:**

    <?xml version="1.0" encoding="utf-16"?>
    <Preset xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:xsd="http://www.w3.org/2001/XMLSchema" Version="1.0" xmlns="http://www.windowsazure.com/media/encoding/Preset/2014/03">
        <Sources>
            <Source StartFrame="0" NumFrames="10000" />
        </Sources>
        <Options>
            <Speed>12</Speed>
        </Options>
    </Preset>

**Predefinição JSON:**

    {
        "Version":1.0,
        "Sources": [
            {
                "StartFrame":0,
                "NumFrames":2147483647
            }
        ],
        "Options": {
            "Speed":1,
            "Stabilize":false
        }
    }

### <a id="sample_code"></a>Hyperlapse Microsoft com o SDK .NET do AMS
O método seguinte carrega um ficheiro de suporte de dados como um recurso e cria uma tarefa com o processador de multimédia de Hyperlapse de suporte de dados do Azure.

> [!NOTE]
> Já deverá ter um CloudMediaContext no âmbito com o nome "contexto" para este código para trabalhar.  Para obter mais informações, leia o [artigo gestão de conteúdos](media-services-dotnet-get-started.md).
> 
> [!NOTE]
> O argumento de cadeia "hyperConfig" deve ser uma configuração de compatível da configuração predefinida no JSON ou XML, tal como descrito acima.
> 
> 

        static bool RunHyperlapseJob(string input, string output, string hyperConfig)
        {
            // create asset with input file
            IAsset asset = context
            .Assets
            .CreateAssetAndUploadSingleFile(input, "My Hyperlapse Input", AssetCreationOptions.None);

            // grab instances of Azure Media Hyperlapse MP
            IMediaProcessor mp = context
            .MediaProcessors
            .GetLatestMediaProcessorByName("Azure Media Hyperlapse");

            // create Job with Hyperlapse task
            IJob job = context
            .Jobs
            .Create(String.Format("Hyperlapse {0}", input));

            if (String.IsNullOrEmpty(hyperConfig))
            {
            // config cannot be empty
            return false;
            }

            hyperConfig = File.ReadAllText(hyperConfig);

            ITask hyperlapseTask = job.Tasks.AddNew("Hyperlapse task",
            mp,
            hyperConfig,
            TaskOptions.None);
            hyperlapseTask.InputAssets.Add(asset);
            hyperlapseTask.OutputAssets.AddNew("Hyperlapse output",
            AssetCreationOptions.None);

            job.Submit();

            // Create progress printing and querying tasks
            Task progressPrintTask = new Task(() =>
            {

            IJob jobQuery = null;
            do
            {
                var progressContext = context;
                jobQuery = progressContext.Jobs
                .Where(j => j.Id == job.Id)
                .First();
                Console.WriteLine(string.Format("{0}\t{1}\t{2}",
                DateTime.Now,
                jobQuery.State,
                jobQuery.Tasks[0].Progress));
                Thread.Sleep(10000);
            }
            while (jobQuery.State != JobState.Finished &&
                                   jobQuery.State != JobState.Error &&
                                   jobQuery.State != JobState.Canceled);
                });
                
            progressPrintTask.Start();

            Task progressJobTask = job.GetExecutionProgressTask(
                                                 CancellationToken.None);
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
                return false;                  
            }

        DownloadAsset(job.OutputMediaAssets.First(), output);
        return true;
    }

    static void DownloadAsset(IAsset asset, string outputDirectory)
    {
        foreach (IAssetFile file in asset.AssetFiles)
        {
            file.Download(Path.Combine(outputDirectory, file.Name));
        }
    }


    static IAsset CreateAssetAndUploadSingleFile(string filePath, string assetName, AssetCreationOptions options)
    {
        IAsset asset = context.Assets.Create(assetName, options);

        var assetFile = asset.AssetFiles.Create(Path.GetFileName(filePath));
        assetFile.Upload(filePath);

        return asset;
    }

    static IMediaProcessor GetLatestMediaProcessorByName(string mediaProcessorName)
    {
        var processor = context.MediaProcessors
        .Where(p => p.Name == mediaProcessorName)
        .ToList()
        .OrderBy(p => new Version(p.Version))
        .LastOrDefault();

        if (processor == null)
            throw new ArgumentException(string.Format("Unknown media processor",
                                                       mediaProcessorName));

        return processor;
    }

### <a id="file_types"></a>Tipos de ficheiro suportados
* MP4
* MOV
* WMV

## <a name="media-services-learning-paths"></a>Percursos de aprendizagem dos Media Services
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Enviar comentários
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

## <a name="related-links"></a>Ligações relacionadas
[Descrição geral da análise de serviços de multimédia do Azure](media-services-analytics-overview.md)

[Demonstrações de análise de multimédia do Azure](http://azuremedialabs.azurewebsites.net/demos/Analytics.html)

