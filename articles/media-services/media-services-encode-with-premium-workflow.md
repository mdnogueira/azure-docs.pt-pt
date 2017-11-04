---
title: "Avançadas codificação com o fluxo de trabalho do suporte de dados codificador Premium | Microsoft Docs"
description: "Saiba como codificar com o fluxo de trabalho do suporte de dados codificador Premium. Exemplos de código são escritos em c# e utilizam o SDK de Media Services para .NET."
services: media-services
documentationcenter: 
author: juliako
manager: cfowler
editor: 
ms.assetid: 0f4c87ac-810a-4d42-8df8-923dff2016c6
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/18/2017
ms.author: juliako
ms.openlocfilehash: 2b03853bf07e05c07fd730d5e8a8563963887921
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="advanced-encoding-with-media-encoder-premium-workflow"></a>Avançadas codificação com o fluxo de trabalho do suporte de dados codificador Premium
> [!NOTE]
> Processador de multimédia de fluxo de trabalho do suporte de dados codificador Premium debatido neste tópico não está disponível na China.
>
>

Para perguntas de codificador premium, e-mail mepd em Microsoft.com.

## <a name="overview"></a>Descrição geral
Serviços de suporte de dados do Microsoft Azure está a introduzir a **o fluxo de trabalho do suporte de dados codificador Premium** processador de multimédia. Este avançadas de ofertas de processador codificação capacidades para os fluxos de trabalho do premium a pedido.

Os tópicos seguintes descrevem os detalhes relacionados com **o fluxo de trabalho do suporte de dados codificador Premium**:

* [Formatos suportados pelo fluxo de trabalho do Gestor de codificador de multimédia Premium](media-services-premium-workflow-encoder-formats.md) – Discusses o ficheiro formata e codecs suportado pelo **o fluxo de trabalho do suporte de dados codificador Premium**.
* [Descrição geral e a comparação do Azure em codificadores de suporte de dados a pedido](media-services-encode-asset.md) compara as capacidades de codificação do **o fluxo de trabalho do suporte de dados codificador Premium** e **codificador de multimédia Standard**.

Este tópico demonstra como codificar com **o fluxo de trabalho do suporte de dados codificador Premium** através do .NET.

A codificação de tarefas para o **o fluxo de trabalho do suporte de dados codificador Premium** necessitam de um ficheiro de configuração separado, denominado um ficheiro de fluxo de trabalho. Estes ficheiros têm uma extensão de .workflow e são criados utilizando o [estruturador de fluxo de trabalho](media-services-workflow-designer.md) ferramenta.

Também pode obter a predefinição ficheiros de fluxo de trabalho [aqui](https://github.com/Azure/azure-media-services-samples/tree/master/Encoding%20Presets/VoD/MediaEncoderPremiumWorkfows). A pasta também contém a descrição destes ficheiros.

Os ficheiros de fluxo de trabalho tem de ser também carregadas para a sua conta de Media Services como um recurso e este recurso deve ser transmitido para a tarefa de codificação.

## <a name="create-and-configure-a-visual-studio-project"></a>Criar e configurar um projeto de Visual Studio

Configure o seu ambiente de desenvolvimento e preencha o ficheiro app.config com informações da ligação, conforme descrito em [Media Services development with .NET](media-services-dotnet-how-to-use.md) (Desenvolvimento de Serviços de Multimédia com .NET). 

## <a name="encoding-example"></a>Exemplo de codificação

O exemplo seguinte demonstra como codificar com **o fluxo de trabalho do suporte de dados codificador Premium**.

São efetuados os seguintes passos:

1. Criar um elemento e carregar um ficheiro de fluxo de trabalho.
2. Criar um elemento e carregar um ficheiro de suporte de dados de origem.
3. Obter o processador de multimédia "Suporte de dados codificador Premium fluxo de trabalho".
4. Crie uma tarefa e uma tarefa.

    Na maioria dos casos, a cadeia de configuração para a tarefa está vazia (como no exemplo seguinte). Existem alguns cenários avançados (que exijam uma definir propriedades de tempo de execução dinamicamente) caso em que pretende fornecer uma cadeia XML para a tarefa de codificação. Exemplos de tais cenários são: criar uma sobreposição, paralelas sequenciais suporte de dados ou stitching, subtitling.
5. Adicione dois elementos de entrada à tarefa.

    1. partir do dia 1 – o elemento de fluxo de trabalho.
    2. 2 – o elemento de vídeo.

    >[!NOTE]
    >O elemento de fluxo de trabalho tem de ser adicionado à tarefa antes do elemento de suporte de dados.
   A cadeia de configuração para esta tarefa deve estar vazia.
   
6. Submeta a tarefa de codificação.

        using System;
        using System.Linq;
        using System.Configuration;
        using System.IO;
        using System.Threading;
        using System.Threading.Tasks;
        using Microsoft.WindowsAzure.MediaServices.Client;

        namespace MediaEncoderPremiumWorkflowSample
        {
            class Program
            {
                private static readonly string _AADTenantDomain =
                    ConfigurationManager.AppSettings["AADTenantDomain"];
                private static readonly string _RESTAPIEndpoint =
                    ConfigurationManager.AppSettings["MediaServiceRESTAPIEndpoint"];

                // Field for service context.
                private static CloudMediaContext _context = null;

                private static readonly string _supportFiles =
                    Path.GetFullPath(@"../..\Media");

                private static readonly string _workflowFilePath =
                    Path.GetFullPath(_supportFiles + @"\H264 Progressive Download MP4.workflow");

                private static readonly string _singleMP4InputFilePath =
                    Path.GetFullPath(_supportFiles + @"\BigBuckBunny.mp4");

                static void Main(string[] args)
                {
                    var tokenCredentials = new AzureAdTokenCredentials(_AADTenantDomain, AzureEnvironments.AzureCloudEnvironment);
                    var tokenProvider = new AzureAdTokenProvider(tokenCredentials);

                    _context = new CloudMediaContext(new Uri(_RESTAPIEndpoint), tokenProvider);

                    var workflowAsset = CreateAssetAndUploadSingleFile(_workflowFilePath);
                    var videoAsset = CreateAssetAndUploadSingleFile(_singleMP4InputFilePath);
                    IAsset outputAsset = CreateEncodingJob(workflowAsset, videoAsset);

                }

                static public IAsset CreateAssetAndUploadSingleFile(string singleFilePath)
                {
                    var assetName = "UploadSingleFile_" + DateTime.UtcNow.ToString();
                    var asset = _context.Assets.Create(assetName, AssetCreationOptions.None);

                    var fileName = Path.GetFileName(singleFilePath);

                    var assetFile = asset.AssetFiles.Create(fileName);

                    Console.WriteLine("Created assetFile {0}", assetFile.Name);

                    Console.WriteLine("Upload {0}", assetFile.Name);

                    assetFile.Upload(singleFilePath);
                    Console.WriteLine("Done uploading {0}", assetFile.Name);

                    return asset;
                }

                static public IAsset CreateEncodingJob(IAsset workflow, IAsset video)
                {
                    // Declare a new job.
                    IJob job = _context.Jobs.Create("Premium Workflow encoding job");
                    // Get a media processor reference, and pass to it the name of the
                    // processor to use for the specific task.
                    IMediaProcessor processor = GetLatestMediaProcessorByName("Media Encoder Premium Workflow");

                    // Create a task with the encoding details, using a string preset.
                    ITask task = job.Tasks.AddNew("Premium Workflow encoding task",
                        processor,
                        "",
                        TaskOptions.None);

                    // Specify the input asset to be encoded.
                    task.InputAssets.Add(workflow);
                    task.InputAssets.Add(video); // we add one asset
                                                 // Add an output asset to contain the results of the job.
                                                 // This output is specified as AssetCreationOptions.None, which
                                                 // means the output asset is not encrypted.
                    task.OutputAssets.AddNew("Output asset",
                        AssetCreationOptions.None);

                    // Use the following event handler to check job progress.  
                    job.StateChanged += new
                            EventHandler<JobStateChangedEventArgs>(StateChanged);

                    // Launch the job.
                    job.Submit();

                    // Check job execution and wait for job to finish.
                    Task progressJobTask = job.GetExecutionProgressTask(CancellationToken.None);
                    progressJobTask.Wait();

                    // If job state is Error the event handling
                    // method for job progress should log errors.  Here we check
                    // for error state and exit if needed.
                    if (job.State == JobState.Error)
                    {
                        throw new Exception("\nExiting method due to job error.");
                    }

                    return job.OutputMediaAssets[0];
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

Para perguntas de codificador premium, e-mail mepd em Microsoft.com.

## <a name="media-services-learning-paths"></a>Percursos de aprendizagem dos Media Services
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Enviar comentários
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]
