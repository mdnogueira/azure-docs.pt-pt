---
title: "Codificar um elemento com o codificador de multimédia Standard utilizando o .NET | Microsoft Docs"
description: "Este tópico mostra como utilizar o .NET para codificar um elemento utilizando Strandard de codificador de multimédia."
services: media-services
documentationcenter: 
author: juliako
manager: cfowler
editor: 
ms.assetid: 03431b64-5518-478a-a1c2-1de345999274
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/31/2017
ms.author: juliako;anilmur
ms.openlocfilehash: 929592368501c54277748bf46b2160c9058db3fb
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="encode-an-asset-with-media-encoder-standard-using-net"></a>Codificar um elemento com o codificador de multimédia Standard utilizando o .NET
As tarefas de codificação são uma das operações de processamento mais comuns nos Serviços de Multimédia. Estes Serviços permitem-lhe criar tarefas de codificação para converter ficheiros de multimédia de uma codificação para outra. Quando codificar, pode utilizar o codificador de multimédia incorporada dos Media Services. Também pode utilizar um codificador fornecido por um parceiro de Media Services; codificadores de terceiros estão disponíveis através do Azure Marketplace. 

Este tópico mostra como utilizar o .NET para codificar seus ativos com suporte de dados codificador padrão (MES). Codificador de multimédia Standard for configurado através de uma das predefinições codificador descritas [aqui](http://go.microsoft.com/fwlink/?linkid=618336&clcid=0x409).

Recomenda-se sempre codificar os ficheiros de origem para uma conjunto de MP4 de velocidade de transmissão adaptável e, em seguida, converter o conjunto para o formato pretendido utilizando a [empacotamento dinâmico](media-services-dynamic-packaging-overview.md). 

Se o elemento de saída é armazenamento encriptado, tem de configurar política de entrega de elementos. Para obter mais informações consulte [configurar política de entrega de elemento](media-services-dotnet-configure-asset-delivery-policy.md).

> [!NOTE]
> MES produz um ficheiro de saída com um nome que contém os primeiro 32 carateres do nome do ficheiro de entrada. O nome baseia-se no que é especificado no ficheiro predefinido. Por exemplo, "FileName": "{Basename} _ {índice} {extensão}". {Basename} é substituído pelos primeiro 32 carateres do nome do ficheiro de entrada.
> 
> 

### <a name="mes-formats"></a>Formatos MES
[Formatos e codecs](media-services-media-encoder-standard-formats.md)

### <a name="mes-presets"></a>Predefinições MES
Codificador de multimédia Standard for configurado através de uma das predefinições codificador descritas [aqui](http://go.microsoft.com/fwlink/?linkid=618336&clcid=0x409).

### <a name="input-and-output-metadata"></a>Metadados de entrada e de saída
Quando codificar um elemento de entrada (ou ativos) utilizando MES, obter um elemento de saída a com êxito na conclusão desse codificar tarefas. O elemento de saída contém as vídeo, áudio, as miniaturas, manifesto, etc. com base na predefinição de codificação a utiliza.

O elemento de saída também contém um ficheiro com os metadados sobre o recurso de entrada. O nome do ficheiro XML de metadados tem o seguinte formato: < asset_id > _metadata.xml (por exemplo, 41114ad3-eb5e - 4C 57 8d 92-5354e2b7d4a4_metadata.xml), onde < asset_id > é o valor de AssetId do recurso de entrada. O esquema dos metadados entrado XML está descrito [aqui](media-services-input-metadata-schema.md).

O elemento de saída também contém um ficheiro com os metadados sobre o elemento de saída. O nome do ficheiro XML de metadados tem o seguinte formato: < source_file_name > _manifest.xml (por exemplo, BigBuckBunny_manifest.xml). O esquema dos metadados resultado XML está descrito [aqui](media-services-output-metadata-schema.md).

Se pretende examinar um dos dois ficheiros de metadados, pode criar um localizador SAS e transfira o ficheiro para o seu computador local. Pode encontrar um exemplo sobre como criar um localizador SAS e transferir um ficheiro com as extensões de SDK do .NET de serviços de suporte de dados.

## <a name="download-sample"></a>Transferir exemplo
Pode obter e executar um exemplo que mostra como codificar com MES de [aqui](https://azure.microsoft.com/documentation/samples/media-services-dotnet-on-demand-encoding-with-media-encoder-standard/).

## <a name="net-sample-code"></a>Código de exemplo do .NET

Exemplo de código seguinte utiliza o SDK .NET dos Media Services para realizar as seguintes tarefas:

* Crie uma tarefa de codificação.
* Obter uma referência ao codificador codificador de multimédia Standard.
* Especifique a utilização de [transmissão em fluxo adaptável](media-services-autogen-bitrate-ladder-with-mes.md) predefinidas. 
* Adicione uma tarefa de codificação único para a tarefa. 
* Especifique o elemento de entrada para ser codificado.
* Crie um elemento de saída que irá conter o elemento codificado.
* Adicione um processador de eventos para verificar o progresso da tarefa.
* Submeta a tarefa.

#### <a name="create-and-configure-a-visual-studio-project"></a>Criar e configurar um projeto de Visual Studio

Configure o seu ambiente de desenvolvimento e preencha o ficheiro app.config com informações da ligação, conforme descrito em [Media Services development with .NET](media-services-dotnet-how-to-use.md) (Desenvolvimento de Serviços de Multimédia com .NET). 

#### <a name="example"></a>Exemplo 

        using System;
        using System.Linq;
        using System.Configuration;
        using System.IO;
        using System.Threading;
        using Microsoft.WindowsAzure.MediaServices.Client;

        namespace MediaEncoderStandardSample
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

                    // Create a task with the encoding details, using a string preset.
                    // In this case "Adaptive Streaming" preset is used.
                    ITask task = job.Tasks.AddNew("My encoding task",
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

## <a name="media-services-learning-paths"></a>Percursos de aprendizagem dos Media Services
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Enviar comentários
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

## <a name="next-steps"></a>Passos seguintes
[Como gerar miniatura utilizando o codificador de multimédia Standard com .NET](media-services-dotnet-generate-thumbnail-with-mes.md)
[codificação descrição geral de serviços de suporte de dados](media-services-encode-asset.md)

