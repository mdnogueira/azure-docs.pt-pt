---
title: Como gerar miniaturas com o Media Encoder Standard com .NET
description: "Este tópico mostra como utilizar o .NET para codificar um elemento e gerar miniaturas ao mesmo tempo utilizando um codificador de multimédia Standard."
services: media-services
documentationcenter: 
author: juliako
manager: cfowler
editor: 
ms.assetid: b8dab73a-1d91-4b6d-9741-a92ad39fc3f7
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/08/2017
ms.author: juliako
ms.openlocfilehash: 7b8732a06e54f7828418cba0c0d172e34f1f4ef7
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-generate-thumbnails-using-media-encoder-standard-with-net"></a>Como gerar miniaturas com o Media Encoder Standard com .NET

Pode utilizar o codificador de multimédia Standard para gerar um ou mais miniaturas do vídeo de entrada [JPEG](https://en.wikipedia.org/wiki/JPEG), [PNG](https://en.wikipedia.org/wiki/Portable_Network_Graphics), ou [BMP](https://en.wikipedia.org/wiki/BMP_file_format) formatos de ficheiro de imagem. Pode submeter as tarefas que produzem apenas imagens ou, pode combinar em miniatura geração com codificação. Este tópico fornece algumas exemplo XML e JSON em miniatura predefinições de tais cenários. No final do tópico, está um [código de exemplo](#code_sample) que mostra como utilizar o SDK do .NET dos serviços de suporte de dados para realizar a tarefa de codificação.

Para obter mais detalhes sobre os elementos que são utilizados nas predefinições de exemplo, deve rever [esquema codificador de multimédia Standard](media-services-mes-schema.md).

Certifique-se de que revê o [considerações](media-services-dotnet-generate-thumbnail-with-mes.md#considerations) secção.
    
## <a name="example-of-a-single-png-file-preset"></a>Exemplo de uma predefinição de "único ficheiro PNG"

A predefinição JSON e XML seguinte pode ser utilizada para produzir um resultado único PNG ficheiro a partir dos primeiro alguns segundos da entrada vídeo, onde o codificador faz uma tentativa de melhor esforço de localizar um frame "interessante". Tenha em atenção que as dimensões da imagem de saída foram definidas para 100%, o que significa que estes corresponderá as dimensões da vídeo de entrada. Tenha em atenção também como a definição de "Format" no "Saídas" é necessário para corresponder a utilização de "PngLayers" na secção "Codecs". 

### <a name="json-preset"></a>Predefinição JSON

    {
      "Version": 1.0,
      "Codecs": [
        {
          "PngLayers": [
            {
              "Type": "PngLayer",
              "Width": "100%",
              "Height": "100%"
            }
          ],
          "Start": "{Best}",
          "Type": "PngImage"
        }
      ],
      "Outputs": [
        {
          "FileName": "{Basename}_{Index}{Extension}",
          "Format": {
            "Type": "PngFormat"
          }
        }
      ]
    }
    
### <a name="xml-preset"></a>Predefinição XML

    <?xml version="1.0" encoding="utf-16"?>
    <Preset xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" Version="1.0" xmlns="http://www.windowsazure.com/media/encoding/Preset/2014/03">
      <Encoding>
        <PngImage Start="{Best}">
          <PngLayers>
            <PngLayer>
              <Width>100%</Width>
              <Height>100%</Height>
            </PngLayer>
          </PngLayers>
        </PngImage>
      </Encoding>
      <Outputs>
        <Output FileName="{Basename}_{Index}{Extension}">
          <PngFormat />
        </Output>
      </Outputs>
    </Preset>

## <a name="example-of-a-series-of-jpeg-images-preset"></a>Exemplo de uma predefinição de "série de imagens JPEG"

A predefinição JSON e XML seguinte pode ser utilizada para produzir um conjunto de 10 imagens em carimbos de 5% 15%,..., 95% da linha cronológica entrada, onde o tamanho da imagem é especificado para ser um trimestre que as vídeo de entrada.

### <a name="json-preset"></a>Predefinição JSON

    {
      "Version": 1.0,
      "Codecs": [
        {
          "JpgLayers": [
            {
              "Quality": 90,
              "Type": "JpgLayer",
              "Width": "25%",
              "Height": "25%"
            }
          ],
          "Start": "5%",
          "Step": "10%",
          "Range": "96%",
          "Type": "JpgImage"
        }
      ],
      "Outputs": [
        {
          "FileName": "{Basename}_{Index}{Extension}",
          "Format": {
            "Type": "JpgFormat"
          }
        }
      ]
    }

### <a name="xml-preset"></a>Predefinição XML
    
    <?xml version="1.0" encoding="utf-16"?>
    <Preset xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" Version="1.0" xmlns="http://www.windowsazure.com/media/encoding/Preset/2014/03">
      <Encoding>
        <JpgImage Start="5%" Step="10%" Range="96%">
          <JpgLayers>
            <JpgLayer>
              <Width>25%</Width>
              <Height>25%</Height>
              <Quality>90</Quality>
            </JpgLayer>
          </JpgLayers>
        </JpgImage>
      </Encoding>
      <Outputs>
        <Output FileName="{Basename}_{Index}{Extension}">
          <JpgFormat />
        </Output>
      </Outputs>
    </Preset>

## <a name="example-of-a-one-image-at-a-specific-timestamp-preset"></a>Exemplo de uma predefinição de "uma imagem num timestamp específico"

A predefinição JSON e XML seguinte pode ser utilizada para produzir uma única imagem JPEG na segunda 30 marca da vídeo de entrada. Esta predefinição espera o vídeo de entrada para ser mais do que 30 segundos duração (caso contrário a tarefa irá falhar).

### <a name="json-preset"></a>Predefinição JSON

    {
      "Version": 1.0,
      "Codecs": [
        {
          "JpgLayers": [
            {
              "Quality": 90,
              "Type": "JpgLayer",
              "Width": "25%",
              "Height": "25%"
            }
          ],
          "Start": "00:00:30",
          "Step": "1",
          "Range": "1",
          "Type": "JpgImage"
        }
      ],
      "Outputs": [
        {
          "FileName": "{Basename}_{Index}{Extension}",
          "Format": {
            "Type": "JpgFormat"
          }
        }
      ]
    }
    
### <a name="xml-preset"></a>Predefinição XML
    
    <?xml version="1.0" encoding="utf-16"?>
    <Preset xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" Version="1.0" xmlns="http://www.windowsazure.com/media/encoding/Preset/2014/03">
      <Encoding>
        <JpgImage Start="00:00:30" Step="00:00:01" Range="00:00:01">
          <JpgLayers>
            <JpgLayer>
              <Width>25%</Width>
              <Height>25%</Height>
              <Quality>90</Quality>
            </JpgLayer>
          </JpgLayers>
        </JpgImage>
      </Encoding>
      <Outputs>
        <Output FileName="{Basename}_{Index}{Extension}">
          <JpgFormat />
        </Output>
      </Outputs>
    </Preset>
    
## <a name="example-of-a-thumbnails-at-different-resolutions-preset"></a>Exemplo de uma predefinição de "miniaturas na resoluções diferentes"

A predefinição seguinte pode ser utilizada para gerar miniaturas na resoluções diferentes de uma tarefa. No exemplo, a % de 5 posições, 15%,..., 95% da linha cronológica entrada, o codificador irá gerar duas imagens – um em 100% a resolução de vídeo de entrada e outro em 50%.

Tenha em atenção a utilização de macro {resolução} no nome de ficheiro; indica ao codificador para utilizar a largura e altura que especificou na secção de codificação de predefinição ao gerar o nome de ficheiro das imagens de saída. Isto também ajuda a distinguir entre as diferentes imagens facilmente

### <a name="json-preset"></a>Predefinição JSON

    {
      "Version": 1.0,
      "Codecs": [
        {
          "JpgLayers": [
        {
          "Quality": 90,
          "Type": "JpgLayer",
          "Width": "100%",
          "Height": "100%"
        },
        {
          "Quality": 90,
          "Type": "JpgLayer",
          "Width": "50%",
          "Height": "50%"
        }

          ],
          "Start": "5%",
          "Step": "10%",
          "Range": "96%",
          "Type": "JpgImage"
        }
      ],
      "Outputs": [
        {
          "FileName": "{Basename}_{Resolution}_{Index}{Extension}",
          "Format": {
        "Type": "JpgFormat"
          }
        }
      ]
    }

### <a name="xml-preset"></a>Predefinição XML

    <?xml version="1.0" encoding="utf-8"?>
    <Preset xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" Version="1.0" xmlns="http://www.windowsazure.com/media/encoding/Preset/2014/03">
    <Encoding>
    <JpgImage Start="5%" Step="10%" Range="96%"><JpgImage Start="00:00:01" Step="00:00:15">
      <JpgLayers>
       <JpgLayer>
        <Width>100%</Width>
        <Height>100%</Height>
        <Quality>90</Quality>
       </JpgLayer>
       <JpgLayer>
        <Width>50%</Width>
        <Height>50%</Height>
        <Quality>90</Quality>
       </JpgLayer>
      </JpgLayers>
    </JpgImage>
    </Encoding>
    <Outputs>
      <Output FileName="{Basename}_{Resolution}_{Index}{Extension}">
        <JpgFormat/>
      </Output>
    </Outputs>
    </Preset>
    
## <a name="example-of-generating-a-thumbnail-while-encoding"></a>Exemplo de gerar uma miniatura ao codificação

Enquanto todos os exemplos acima tem abordado como pode submeter uma tarefa de codificação que produz apenas imagens, também é possível combinar codificação de áudio/vídeo em miniatura geração. A predefinição JSON e XML seguinte dizer **codificador de multimédia Standard** para gerar uma miniatura durante codificação.

### <a id="json"></a>Predefinição JSON
Para obter informações sobre o esquema, consulte [isto](https://msdn.microsoft.com/library/mt269962.aspx) tópico.

    {
      "Version": 1.0,
      "Codecs": [
        {
          "KeyFrameInterval": "00:00:02",
          "SceneChangeDetection": "true",
          "H264Layers": [
            {
              "Profile": "Auto",
              "Level": "auto",
              "Bitrate": 4500,
              "MaxBitrate": 4500,
              "BufferWindow": "00:00:05",
              "Width": 1280,
              "Height": 720,
              "ReferenceFrames": 3,
              "EntropyMode": "Cabac",
              "AdaptiveBFrame": true,
              "Type": "H264Layer",
              "FrameRate": "0/1"
    
            }
          ],
          "Type": "H264Video"
        },
        {
          "JpgLayers": [
            {
              "Quality": 90,
              "Type": "JpgLayer",
              "Width": "100%",
              "Height": "100%"
            }
          ],
          "Start": "{Best}",
          "Type": "JpgImage"
        },
        {
          "Channels": 2,
          "SamplingRate": 48000,
          "Bitrate": 128,
          "Type": "AACAudio"
        }
      ],
      "Outputs": [
        {
          "FileName": "{Basename}_{Index}{Extension}",
          "Format": {
            "Type": "JpgFormat"
          }
        },
        {
          "FileName": "{Basename}_{Resolution}_{VideoBitrate}.mp4",
          "Format": {
            "Type": "MP4Format"
          }
        }
      ]
    }

### <a id="xml"></a>Predefinição XML
Para obter informações sobre o esquema, consulte [isto](https://msdn.microsoft.com/library/mt269962.aspx) tópico.
    
    <?xml version="1.0" encoding="utf-16"?>
    <Preset xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" Version="1.0" xmlns="http://www.windowsazure.com/media/encoding/Preset/2014/03">
      <Encoding>
        <H264Video>
          <KeyFrameInterval>00:00:02</KeyFrameInterval>
          <SceneChangeDetection>true</SceneChangeDetection>
          <H264Layers>
            <H264Layer>
              <Bitrate>4500</Bitrate>
              <Width>1280</Width>
              <Height>720</Height>
              <FrameRate>0/1</FrameRate>
              <Profile>Auto</Profile>
              <Level>auto</Level>
              <BFrames>3</BFrames>
              <ReferenceFrames>3</ReferenceFrames>
              <Slices>0</Slices>
              <AdaptiveBFrame>true</AdaptiveBFrame>
              <EntropyMode>Cabac</EntropyMode>
              <BufferWindow>00:00:05</BufferWindow>
              <MaxBitrate>4500</MaxBitrate>
            </H264Layer>
          </H264Layers>
        </H264Video>
        <AACAudio>
          <Profile>AACLC</Profile>
          <Channels>2</Channels>
          <SamplingRate>48000</SamplingRate>
          <Bitrate>128</Bitrate>
        </AACAudio>
        <JpgImage Start="{Best}">
          <JpgLayers>
            <JpgLayer>
              <Width>100%</Width>
              <Height>100%/Height>
              <Quality>90</Quality>
            </JpgLayer>
          </JpgLayers>
        </JpgImage>
      </Encoding>
      <Outputs>
        <Output FileName="{Basename}_{Resolution}_{VideoBitrate}.mp4">
          <MP4Format />
        </Output>
        <Output FileName="{Basename}_{Index}{Extension}">
          <JpgFormat />
        </Output>
      </Outputs>
    </Preset>   

## <a id="code_sample"></a>Codificar as vídeo e gerar miniatura com o .NET

Exemplo de código seguinte utiliza o SDK .NET dos Media Services para realizar as seguintes tarefas:

* Crie uma tarefa de codificação.
* Obter uma referência ao codificador codificador de multimédia Standard.
* A predefinição de carga [XML](media-services-dotnet-generate-thumbnail-with-mes.md#xml) ou [JSON](media-services-dotnet-generate-thumbnail-with-mes.md#json) que contém a configuração predefinida, bem como informações necessárias para gerar miniaturas codificação. Pode guardar esta [XML](media-services-dotnet-generate-thumbnail-with-mes.md#xml) ou [JSON](media-services-dotnet-generate-thumbnail-with-mes.md#json) num ficheiro e utilize o seguinte código ao carregar o ficheiro.
  
        // Load the XML (or JSON) from the local file.
        string configuration = File.ReadAllText(fileName);  
* Adicione uma tarefa de codificação único para a tarefa. 
* Especifique o elemento de entrada para ser codificado.
* Crie um elemento de saída que irá conter o elemento codificado.
* Adicione um processador de eventos para verificar o progresso da tarefa.
* Submeta a tarefa.

Consulte o [desenvolvimento de Media Services com .NET](media-services-dotnet-how-to-use.md) tópico para instruções sobre como configurar o ambiente de desenvolvimento.

        using System;
        using System.Configuration;
        using System.IO;
        using System.Linq;
        using Microsoft.WindowsAzure.MediaServices.Client;
        using System.Threading;

        namespace EncodeAndGenerateThumbnails
        {
        class Program
        {
            // Read values from the App.config file.
            private static readonly string _AADTenantDomain =
            ConfigurationManager.AppSettings["AADTenantDomain"];
            private static readonly string _RESTAPIEndpoint =
            ConfigurationManager.AppSettings["MediaServiceRESTAPIEndpoint"];

            private static CloudMediaContext _context = null;

            private static readonly string _mediaFiles =
            Path.GetFullPath(@"../..\Media");

            private static readonly string _singleMP4File =
            Path.Combine(_mediaFiles, @"BigBuckBunny.mp4");

            static void Main(string[] args)
            {
            var tokenCredentials = new AzureAdTokenCredentials(_AADTenantDomain, AzureEnvironments.AzureCloudEnvironment);
            var tokenProvider = new AzureAdTokenProvider(tokenCredentials);

            _context = new CloudMediaContext(new Uri(_RESTAPIEndpoint), tokenProvider);

            // Get an uploaded asset.
            var asset = _context.Assets.FirstOrDefault();

            // Encode and generate the thumbnails.
            EncodeToAdaptiveBitrateMP4Set(asset);

            Console.ReadLine();
            }

            static public IAsset EncodeToAdaptiveBitrateMP4Set(IAsset asset)
            {
            // Declare a new job.
            IJob job = _context.Jobs.Create("Media Encoder Standard Thumbnail Job");
            // Get a media processor reference, and pass to it the name of the 
            // processor to use for the specific task.
            IMediaProcessor processor = GetLatestMediaProcessorByName("Media Encoder Standard");

            // Load the XML (or JSON) from the local file.
            string configuration = File.ReadAllText("ThumbnailPreset_JSON.json");

            // Create a task
            ITask task = job.Tasks.AddNew("Media Encoder Standard Thumbnail task",
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


## <a name="considerations"></a>Considerações
Aplicam as seguintes considerações:

* A utilização de carimbos explícitas para iniciar/passo/intervalo parte do princípio de que a origem de entrada é muito pelo menos 1 minuto.
* Elementos de jpg/Png/BmpImage tem início, o passo e o intervalo de atributos de cadeia – estes podem ser interpretados como:
  
  * Número de moldura que se encontrem números inteiros de não negativo, por exemplo. "Start": "120"
  * Caminho relativo para a duração de origem se expressa %-o sufixo, por exemplo. "Start": "15%", ou
  * Timestamp se expresso como hh: mm:... formato. Ex. "Start": "00: 01:00"
    
    Pode combinar e misturar notações como.
    
    Além disso, o início suporta também uma Macro especial: {melhores}, que tenta determinar o intervalo de "interessante" primeiro do conteúdo tenha em atenção: (passo e o intervalo são ignoradas quando início está definido como {melhor})
  * As predefinições: Iniciar: {melhor}
* Formato de saída tem de ser explicitamente fornecido para cada formato de imagem: Png/Jpg/BmpFormat. Quando presente, MES corresponderá JpgVideo para JpgFormat e assim sucessivamente. OutputFormat introduz uma nova Macro específico de imagem codec: {índice}, que tem de estar presente (uma vez e apenas uma vez) para formatos de saída de imagem.

## <a name="next-steps"></a>Passos seguintes

Pode verificar o [progresso da tarefa](media-services-check-job-progress.md) enquanto a tarefa de codificação está pendente.

## <a name="media-services-learning-paths"></a>Percursos de aprendizagem dos Media Services
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Enviar comentários
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

## <a name="see-also"></a>Veja Também
[Descrição geral de codificação de serviços de multimédia](media-services-encode-asset.md)

