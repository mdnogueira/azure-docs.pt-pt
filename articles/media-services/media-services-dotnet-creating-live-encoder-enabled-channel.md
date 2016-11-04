---
title: Como realizar a transmissão em fluxo em direto através dos Serviços de Multimédia do Azure para criar transmissões com velocidade de transmissão múltipla com o .NET | Microsoft Docs
description: Este tutorial explica-lhe os passos da criação de um canal que recebe uma transmissão em fluxo em direto com uma velocidade de transmissão única e a codifica para uma transmissão com velocidade de transmissão múltipla utilizando o SDK .NET.
services: media-services
documentationcenter: ''
author: anilmur
manager: erikre
editor: ''

ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 09/15/2016
ms.author: juliako;anilmur

---
# Como realizar uma transmissão em fluxo em direto utilizando os Media Services do Azure para criar transmissões com velocidade de transmissão múltipla com .NET
> [!div class="op_single_selector"]
> * [Portal](media-services-portal-creating-live-encoder-enabled-channel.md)
> * [.NET](media-services-dotnet-creating-live-encoder-enabled-channel.md)
> * [API REST](https://msdn.microsoft.com/library/azure/dn783458.aspx)
> 
> [!NOTE]
> Para concluir este tutorial, precisa de uma conta do Azure. Para obter mais detalhes, consulte [Avaliação Gratuita do Azure](/pricing/free-trial/?WT.mc_id=A261C142F). 
> 
> 

## Descrição geral
Este tutorial explica-lhe os passos da criação de um **Canal** que recebe uma transmissão em fluxo em direto com uma velocidade de transmissão única e a codifica para uma transmissão com velocidade de transmissão múltipla.

Para obter mais informações conceptuais relacionadas com Canais ativados para Live Encoding, consulte [Transmissão em fluxo em direto através dos Media Services do Azure para criar transmissões com velocidade de transmissão múltipla](media-services-manage-live-encoder-enabled-channels.md).

## Cenário Comum de Transmissão em Fluxo em Direto
Os passos seguintes descrevem as tarefas envolvidas na criação de aplicações comuns de transmissão em fluxo em direto.

> [!NOTE]
> Atualmente, a duração máxima recomendada de um evento em direto é de 8 horas. Contacte amslived através de Microsoft.com se tiver de executar um Canal durante períodos de tempo mais longos.
> 
> 

1. Ligue uma câmara de vídeo a um computador. Inicie e configure um codificador em direto no local que possa enviar uma transmissão de velocidade de transmissão única através de um dos seguintes protocolos: RTMP, Transmissão em Fluxo Uniforme ou RTP (MPEG-TS). Para obter mais informações, consulte [Suporte RTMP dos Media Services do Azure e Codificadores em Direto](http://go.microsoft.com/fwlink/?LinkId=532824).
   
    Este passo também pode ser realizado depois de criar o Canal.
2. Crie e inicie um Canal.
3. Obtenha o URL de inserção do Canal.
   
    O URL de inserção é utilizado pelo codificador em direto para enviar a transmissão para o Canal.
4. Obtenha o URL de pré-visualização do Canal.
   
    Utilize este URL para verificar se o canal está a receber corretamente a transmissão em fluxo em direto.
5. Crie um elemento.
6. Se pretender que o elemento seja encriptado dinamicamente durante a reprodução, faça o seguinte:
7. Crie uma chave de conteúdo.
8. Configure a política de autorização da chave de conteúdo.
9. Configure a política de entrega de elementos (utilizada pelo empacotamento dinâmico e a encriptação dinâmica).
10. Crie um programa e especifique a utilização do elemento que criou.
11. Publique o elemento associado ao programa através da criação de um localizador OnDemand.
    
     Certifique-se de que tem, pelo menos, uma unidade reservada para transmissão em fluxo no ponto final da transmissão a partir do qual pretende transmitir o conteúdo.
12. Inicie o programa quando estiver pronto para começar a transmissão em fluxo e o arquivamento.
13. Opcionalmente, o codificador em direto pode ser indicado para iniciar um anúncio. O anúncio é inserido na transmissão de saída.
14. Pare o programa sempre que pretender interromper a transmissão e arquivar o evento.
15. Elimine o Programa (e, opcionalmente, elimine o elemento).

## O que irá aprender
Este tópico mostra como realizar operações diferentes em canais e programas que utilizam o SDK .NET dos Media Services. Como muitas operações são de execução longa, são utilizadas APIs .NET que gerem operações de execução longa .

O tópico mostra como fazer o seguinte:

1. Crie e inicie um canal. São utilizadas APIs de execução longa.
2. Obtenha o ponto final (entrada) de inserção dos canais. Este ponto final deve ser fornecido ao codificador que pode enviar uma transmissão em fluxo em direto única.
3. Obtenha o ponto final de pré-visualização. Este ponto final é utilizado para pré-visualizar a transmissão.
4. Crie um elemento que será utilizado para armazenar o conteúdo. As políticas de entrega de elementos também devem ser configuradas, conforme exibido neste exemplo.
5. Crie um programa e especifique-o para que utilize o elemento criado anteriormente. Inicie o programa. São utilizadas APIs de execução longa.
6. Crie um localizador para o elemento, permitindo que o conteúdo seja publicado e possa ser transmitido para os seus clientes.
7. Mostre e oculte imagens fixas. Inicie e pare os anúncios. São utilizadas APIs de execução longa.
8. Limpe o canal e todos os recursos associados.

## Pré-requisitos
O seguinte é necessário para concluir o tutorial.

* Para concluir este tutorial, precisa de uma conta do Azure. 
  
    Se não tiver uma conta, pode criar uma de avaliação gratuita em apenas alguns minutos. Para obter mais detalhes, consulte [Avaliação Gratuita do Azure](/pricing/free-trial/?WT.mc_id=A261C142F). Recebe créditos que podem ser utilizados para experimentar os serviços pagos do Azure. Mesmo depois de gastar todos os créditos, pode manter a conta e utilizar os serviços e recursos gratuitos do Azure, tal como a funcionalidade Web Apps no App Service do Azure.
* Uma conta dos Media Services. Para criar uma conta dos Media Services, consulte [Criar Conta](media-services-create-account.md).
* Visual Studio 2010 SP1 (Professional, Premium, Ultimate ou Express) ou versões posteriores.
* Tem de utilizar a versão 3.2.0.0 ou mais recente do SDK .NET dos Media Services.
* Uma câmara Web e um codificador que possa enviar uma transmissão de velocidade de transmissão única.

## Considerações
* Atualmente, a duração máxima recomendada de um evento em direto é de 8 horas. Contacte amslived através de Microsoft.com se tiver de executar um Canal durante períodos de tempo mais longos.
* Certifique-se de que tem, pelo menos, uma unidade reservada para transmissão em fluxo no ponto final da transmissão a partir do qual pretende transmitir o conteúdo.

## Transferir exemplo
Obtenha e execute um exemplo [aqui](https://azure.microsoft.com/documentation/samples/media-services-dotnet-encode-live-stream-with-ams-clear/).

## Configurar o desenvolvimento com o SDK .NET dos Media Services
1. Criar uma aplicação da consola utilizando o Visual Studio.
2. Adicione o SDK .NET dos Media Services à aplicação da consola utilizando o pacote NuGet dos Media Services.

## Ligar aos Media Services
Como melhor prática, deve utilizar um ficheiro app.config para armazenar a chave da conta e o nome dos Media Services.

> [!NOTE]
> Para localizar os valores do Nome e da Chave, aceda ao Portal Clássico do Azure, selecione a conta Serviço de Multimédia e clique no ícone “GERIR CHAVES” na parte inferior da janela do portal. Clicar no ícone junto a cada caixa de texto copia o valor para a área de transferência do sistema.
> 
> 

Adicione a secção appSettings ao ficheiro app.config e configure os valores da chave da conta e nome da conta dos Media Services.

    <?xml version="1.0"?>
    <configuration>
      <appSettings>
          <add key="MediaServicesAccountName" value="YouMediaServicesAccountName" />
          <add key="MediaServicesAccountKey" value="YouMediaServicesAccountKey" />
      </appSettings>
    </configuration>


## Exemplo de código
    using System;
    using System.Collections.Generic;
    using System.Configuration;
    using System.IO;
    using System.Linq;
    using System.Net;
    using Microsoft.WindowsAzure.MediaServices.Client;
    using Microsoft.WindowsAzure.MediaServices.Client.DynamicEncryption;

    namespace EncodeLiveStreamWithAmsClear
    {
        class Program
        {
            private const string ChannelName = "channel001";
            private const string AssetlName = "asset001";
            private const string ProgramlName = "program001";

            // Read values from the App.config file.
            private static readonly string _mediaServicesAccountName =
                ConfigurationManager.AppSettings["MediaServicesAccountName"];
            private static readonly string _mediaServicesAccountKey =
                ConfigurationManager.AppSettings["MediaServicesAccountKey"];

            // Field for service context.
            private static CloudMediaContext _context = null;
            private static MediaServicesCredentials _cachedCredentials = null;


            static void Main(string[] args)
            {
                // Create and cache the Media Services credentials in a static class variable.
                _cachedCredentials = new MediaServicesCredentials(
                                _mediaServicesAccountName,
                                _mediaServicesAccountKey);
                // Used the cached credentials to create CloudMediaContext.
                _context = new CloudMediaContext(_cachedCredentials);

                IChannel channel = CreateAndStartChannel();

                // The channel's input endpoint:
                string ingestUrl = channel.Input.Endpoints.FirstOrDefault().Url.ToString();

                Console.WriteLine("Intest URL: {0}", ingestUrl);


                // Use the previewEndpoint to preview and verify 
                // that the input from the encoder is actually reaching the Channel. 
                string previewEndpoint = channel.Preview.Endpoints.FirstOrDefault().Url.ToString();

                Console.WriteLine("Preview URL: {0}", previewEndpoint);

                // When Live Encoding is enabled, you can now get a preview of the live feed as it reaches the Channel. 
                // This can be a valuable tool to check whether your live feed is actually reaching the Channel. 
                // The thumbnail is exposed via the same end-point as the Channel Preview URL.
                string thumbnailUri = new UriBuilder
                {
                    Scheme = Uri.UriSchemeHttps,
                    Host = channel.Preview.Endpoints.FirstOrDefault().Url.Host,
                    Path = "thumbnails/input.jpg"
                }.Uri.ToString();

                Console.WriteLine("Thumbain URL: {0}", thumbnailUri);

                // Once you previewed your stream and verified that it is flowing into your Channel, 
                // you can create an event by creating an Asset, Program, and Streaming Locator. 
                IAsset asset = CreateAndConfigureAsset();

                IProgram program = CreateAndStartProgram(channel, asset);

                ILocator locator = CreateLocatorForAsset(program.Asset, program.ArchiveWindowLength);

                // You can use slates and ads only if the channel type is Standard.  
                StartStopAdsSlates(channel);

                // Once you are done streaming, clean up your resources.
                Cleanup(channel);

            }

            public static IChannel CreateAndStartChannel()
            {
                var channelInput = CreateChannelInput();
                var channePreview = CreateChannelPreview();
                var channelEncoding = CreateChannelEncoding();


                ChannelCreationOptions options = new ChannelCreationOptions
                {
                    EncodingType = ChannelEncodingType.Standard,
                    Name = ChannelName,
                    Input = channelInput,
                    Preview = channePreview,
                    Encoding = channelEncoding
                };

                Log("Creating channel");
                IOperation channelCreateOperation = _context.Channels.SendCreateOperation(options);
                string channelId = TrackOperation(channelCreateOperation, "Channel create");

                IChannel channel = _context.Channels.Where(c => c.Id == channelId).FirstOrDefault();

                Log("Starting channel");
                var channelStartOperation = channel.SendStartOperation();
                TrackOperation(channelStartOperation, "Channel start");

                return channel;
            }

            /// <summary>
            /// Create channel input, used in channel creation options. 
            /// </summary>
            /// <returns></returns>
            private static ChannelInput CreateChannelInput()
            {
                return new ChannelInput
                {
                    StreamingProtocol = StreamingProtocol.RTPMPEG2TS,
                    AccessControl = new ChannelAccessControl
                    {
                        IPAllowList = new List<IPRange>
                        {
                            new IPRange
                            {
                                Name = "TestChannelInput001",
                                Address = IPAddress.Parse("0.0.0.0"),
                                SubnetPrefixLength = 0
                            }
                        }
                    }
                };
            }

            /// <summary>
            /// Create channel preview, used in channel creation options. 
            /// </summary>
            /// <returns></returns>
            private static ChannelPreview CreateChannelPreview()
            {
                return new ChannelPreview
                {
                    AccessControl = new ChannelAccessControl
                    {
                        IPAllowList = new List<IPRange>
                        {
                            new IPRange
                            {
                                Name = "TestChannelPreview001",
                                Address = IPAddress.Parse("0.0.0.0"),
                                SubnetPrefixLength = 0
                            }
                        }
                    }
                };
            }

            /// <summary>
            /// Create channel encoding, used in channel creation options. 
            /// </summary>
            /// <returns></returns>
            private static ChannelEncoding CreateChannelEncoding()
            {
                return new ChannelEncoding
                {
                    SystemPreset = "Default720p",
                    IgnoreCea708ClosedCaptions = false,
                    AdMarkerSource = AdMarkerSource.Api,
                    // You can only set audio if streaming protocol is set to StreamingProtocol.RTPMPEG2TS.
                    AudioStreams = new List<AudioStream> { new AudioStream { Index = 103, Language = "eng" } }.AsReadOnly()
                };
            }

            /// <summary>
            /// Create an asset and configure asset delivery policies.
            /// </summary>
            /// <returns></returns>
            public static IAsset CreateAndConfigureAsset()
            {
                IAsset asset = _context.Assets.Create(AssetlName, AssetCreationOptions.None);

                IAssetDeliveryPolicy policy =
                    _context.AssetDeliveryPolicies.Create("Clear Policy",
                    AssetDeliveryPolicyType.NoDynamicEncryption,
                    AssetDeliveryProtocol.HLS | AssetDeliveryProtocol.SmoothStreaming | AssetDeliveryProtocol.Dash, null);

                asset.DeliveryPolicies.Add(policy);

                return asset;
            }

            /// <summary>
            /// Create a Program on the Channel. You can have multiple Programs that overlap or are sequential;
            /// however each Program must have a unique name within your Media Services account.
            /// </summary>
            /// <param name="channel"></param>
            /// <param name="asset"></param>
            /// <returns></returns>
            public static IProgram CreateAndStartProgram(IChannel channel, IAsset asset)
            {
                IProgram program = channel.Programs.Create(ProgramlName, TimeSpan.FromHours(3), asset.Id);
                Log("Program created", program.Id);

                Log("Starting program");
                var programStartOperation = program.SendStartOperation();
                TrackOperation(programStartOperation, "Program start");

                return program;
            }

            /// <summary>
            /// Create locators in order to be able to publish and stream the video.
            /// </summary>
            /// <param name="asset"></param>
            /// <param name="ArchiveWindowLength"></param>
            /// <returns></returns>
            public static ILocator CreateLocatorForAsset(IAsset asset, TimeSpan ArchiveWindowLength)
            {
                // You cannot create a streaming locator using an AccessPolicy that includes write or delete permissions.            
                var locator = _context.Locators.CreateLocator
                    (
                        LocatorType.OnDemandOrigin,
                        asset,
                        _context.AccessPolicies.Create
                            (
                                "Live Stream Policy",
                                ArchiveWindowLength,
                                AccessPermissions.Read
                            )
                    );

                return locator;
            }

            /// <summary>
            /// Perform operations on slates.
            /// </summary>
            /// <param name="channel"></param>
            public static void StartStopAdsSlates(IChannel channel)
            {
                int cueId = new Random().Next(int.MaxValue);
                var path = Path.GetFullPath(Path.Combine(AppDomain.CurrentDomain.BaseDirectory, @"..\\..\\SlateJPG\\DefaultAzurePortalSlate.jpg"));

                Log("Creating asset");
                var slateAsset = _context.Assets.Create("Slate test asset " + DateTime.Now.ToString("yyyy-MM-dd HH-mm"), AssetCreationOptions.None);
                Log("Slate asset created", slateAsset.Id);

                Log("Uploading file");
                var assetFile = slateAsset.AssetFiles.Create("DefaultAzurePortalSlate.jpg");
                assetFile.Upload(path);
                assetFile.IsPrimary = true;
                assetFile.Update();

                Log("Showing slate");
                var showSlateOpeartion = channel.SendShowSlateOperation(TimeSpan.FromMinutes(1), slateAsset.Id);
                TrackOperation(showSlateOpeartion, "Show slate");

                Log("Hiding slate");
                var hideSlateOperation = channel.SendHideSlateOperation();
                TrackOperation(hideSlateOperation, "Hide slate");

                Log("Starting ad");
                var startAdOperation = channel.SendStartAdvertisementOperation(TimeSpan.FromMinutes(1), cueId, false);
                TrackOperation(startAdOperation, "Start ad");

                Log("Ending ad");
                var endAdOperation = channel.SendEndAdvertisementOperation(cueId);
                TrackOperation(endAdOperation, "End ad");

                Log("Deleting slate asset");
                slateAsset.Delete();
            }

            /// <summary>
            /// Clean up resources associated with the channel.
            /// </summary>
            /// <param name="channel"></param>
            public static void Cleanup(IChannel channel)
            {
                IAsset asset;
                if (channel != null)
                {
                    foreach (var program in channel.Programs)
                    {
                        asset = _context.Assets.Where(se => se.Id == program.AssetId)
                                                .FirstOrDefault();

                        Log("Stopping program");
                        var programStopOperation = program.SendStopOperation();
                        TrackOperation(programStopOperation, "Program stop");

                        program.Delete();

                        if (asset != null)
                        {
                            Log("Deleting locators");
                            foreach (var l in asset.Locators)
                                l.Delete();

                            Log("Deleting asset");
                            asset.Delete();
                        }
                    }

                    Log("Stopping channel");
                    var channelStopOperation = channel.SendStopOperation();
                    TrackOperation(channelStopOperation, "Channel stop");

                    Log("Deleting channel");
                    var channelDeleteOperation = channel.SendDeleteOperation();
                    TrackOperation(channelDeleteOperation, "Channel delete");
                }
            }


            /// <summary>
            /// Track long running operations.
            /// </summary>
            /// <param name="operation"></param>
            /// <param name="description"></param>
            /// <returns></returns>
            public static string TrackOperation(IOperation operation, string description)
            {
                string entityId = null;
                bool isCompleted = false;

                Log("starting to track ", null, operation.Id);
                while (isCompleted == false)
                {
                    operation = _context.Operations.GetOperation(operation.Id);
                    isCompleted = IsCompleted(operation, out entityId);
                    System.Threading.Thread.Sleep(TimeSpan.FromSeconds(30));
                }
                // If we got here, the operation succeeded.
                Log(description + " in completed", operation.TargetEntityId, operation.Id);

                return entityId;
            }

            /// <summary> 
            /// Checks if the operation has been completed. 
            /// If the operation succeeded, the created entity Id is returned in the out parameter.
            /// </summary> 
            /// <param name="operationId">The operation Id.</param> 
            /// <param name="channel">
            /// If the operation succeeded, 
            /// the entity Id associated with the sucessful operation is returned in the out parameter.</param>
            /// <returns>Returns false if the operation is still in progress; otherwise, true.</returns> 
            private static bool IsCompleted(IOperation operation, out string entityId)
            {

                bool completed = false;

                entityId = null;

                switch (operation.State)
                {
                    case OperationState.Failed:
                        // Handle the failure. 
                        // For example, throw an exception. 
                        // Use the following information in the exception: operationId, operation.ErrorMessage.
                        Log("operation failed", operation.TargetEntityId, operation.Id);
                        break;
                    case OperationState.Succeeded:
                        completed = true;
                        entityId = operation.TargetEntityId;
                        break;
                    case OperationState.InProgress:
                        completed = false;
                        Log("operation in progress", operation.TargetEntityId, operation.Id);
                        break;
                }
                return completed;
            }


            private static void Log(string action, string entityId = null, string operationId = null)
            {
                Console.WriteLine(
                    "{0,-21}{1,-51}{2,-51}{3,-51}",
                    DateTime.Now.ToString("yyyy'-'MM'-'dd HH':'mm':'ss"),
                    action,
                    entityId ?? string.Empty,
                    operationId ?? string.Empty);
            }
        }
    }   


## Passo seguinte
Rever os percursos de aprendizagem dos Serviços de Multimédia

[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## Enviar comentários
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

### Está à procura de outra coisa?
Se este tópico não continha o que pretendia, algo está em falta ou não correspondeu de alguma forma às suas necessidades, envie-nos os seus comentários utilizando o thread Disqus abaixo.

<!--HONumber=Sep16_HO3-->


