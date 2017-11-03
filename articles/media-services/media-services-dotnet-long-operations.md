---
title: "Operações de longa execução de consulta | Microsoft Docs"
description: "Este tópico mostra como consultar operações de execução longa."
services: media-services
documentationcenter: 
author: juliako
manager: cfowler
editor: 
ms.assetid: 9a68c4b1-6159-42fe-9439-a3661a90ae03
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/18/2017
ms.author: juliako
ms.openlocfilehash: 7123a2d44d3b7c332afe30fb0fcea88ca29e313a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="delivering-live-streaming-with-azure-media-services"></a>Entrega de transmissão em fluxo em direto com Media Services do Azure

## <a name="overview"></a>Descrição geral

Serviços de suporte de dados do Microsoft Azure oferece APIs que enviar pedidos para serviços de suporte de dados para iniciar as operações (por exemplo: criar, iniciar, parar ou eliminar um canal). Estas operações são de execução longa.

O SDK de Media Services .NET fornece APIs que enviar o pedido e aguarde pela conclusão da operação (internamente, as APIs são a consultar progresso da operação em alguns intervalos). Por exemplo, quando chamar o canal. Start (), o método devolve após o canal foi iniciado. Também pode utilizar a versão assíncrona: await canal. StartAsync() (para obter informações sobre o padrão assíncrono baseado em tarefas, consulte [TOQUE](https://msdn.microsoft.com/library/hh873175\(v=vs.110\).aspx)). APIs que enviam um pedido de operação e, em seguida, consultam o estado até que a operação foi concluída são denominadas "métodos de consulta". Estes métodos (especialmente a versão assíncrona) são recomendados para aplicações de cliente avançado e/ou serviços com monitorização de estado.

Existem cenários onde uma aplicação não pode aguardar por um pedido de http de execução longa e pretende consultar o progresso da operação manualmente. Um exemplo típico seria um browser a interagir com um serviço web sem monitorização de estado: quando o browser pede para criar um canal, o serviço web inicia uma operação demorada que e devolve o ID de operação para o browser. O browser, em seguida, pedir ao serviço web para obter o estado da operação baseiam o ID. O SDK de Media Services .NET fornece APIs que são úteis para este cenário. Estas APIs são denominados "métodos de consulta não".
Os métodos"não consulta" tem o padrão de nomenclatura seguinte: enviar*OperationName*operação (por exemplo, SendCreateOperation). Enviar*OperationName*métodos operação devolvem o **IOperation** objeto; o devolvido objeto contém informações que podem ser utilizadas para controlar a operação. Enviar*OperationName*OperationAsync métodos devolvem **tarefas<IOperation>**.

Atualmente, as seguintes classes de suportam métodos de consulta não: **canal**, **StreamingEndpoint**, e **programa**.

Para consultar o estado da operação, utilize o **GetOperation** método no **OperationBaseCollection** classe. Utilize os seguintes intervalos para verificar o estado da operação: para **canal** e **StreamingEndpoint** operações, utilize 30 segundos; para **programa** operações, utilize a 10 segundos.

## <a name="create-and-configure-a-visual-studio-project"></a>Criar e configurar um projeto de Visual Studio

Configure o seu ambiente de desenvolvimento e preencha o ficheiro app.config com informações da ligação, conforme descrito em [Media Services development with .NET](media-services-dotnet-how-to-use.md) (Desenvolvimento de Serviços de Multimédia com .NET).

## <a name="example"></a>Exemplo

O exemplo seguinte define uma classe denominada **ChannelOperations**. Esta definição de classe pode ser um ponto de partida para a sua definição de classe de serviço web. Para simplicidade, os exemplos seguintes utilizam as versões não async dos métodos.

O exemplo mostra também como o cliente poderá utilizar esta classe.

### <a name="channeloperations-class-definition"></a>Definição de classe ChannelOperations

    using Microsoft.WindowsAzure.MediaServices.Client;
    using System;
    using System.Collections.Generic;
    using System.Configuration;
    using System.Net;

    /// <summary> 
    /// The ChannelOperations class only implements 
    /// the Channel’s creation operation. 
    /// </summary> 
    public class ChannelOperations
    {
        // Read values from the App.config file.
        private static readonly string _AADTenantDomain =
            ConfigurationManager.AppSettings["AADTenantDomain"];
        private static readonly string _RESTAPIEndpoint =
            ConfigurationManager.AppSettings["MediaServiceRESTAPIEndpoint"];

        // Field for service context.
        private static CloudMediaContext _context = null;

        public ChannelOperations()
        {
            var tokenCredentials = new AzureAdTokenCredentials(_AADTenantDomain, AzureEnvironments.AzureCloudEnvironment);
            var tokenProvider = new AzureAdTokenProvider(tokenCredentials);

            _context = new CloudMediaContext(new Uri(_RESTAPIEndpoint), tokenProvider);
        }

        /// <summary>  
        /// Initiates the creation of a new channel.  
        /// </summary>  
        /// <param name="channelName">Name to be given to the new channel</param>  
        /// <returns>  
        /// Operation Id for the long running operation being executed by Media Services. 
        /// Use this operation Id to poll for the channel creation status. 
        /// </returns> 
        public string StartChannelCreation(string channelName)
        {
            var operation = _context.Channels.SendCreateOperation(
                new ChannelCreationOptions
                {
                    Name = channelName,
                    Input = CreateChannelInput(),
                    Preview = CreateChannelPreview(),
                    Output = CreateChannelOutput()
                });

            return operation.Id;
        }

        /// <summary> 
        /// Checks if the operation has been completed. 
        /// If the operation succeeded, the created channel Id is returned in the out parameter.
        /// </summary> 
        /// <param name="operationId">The operation Id.</param> 
        /// <param name="channel">
        /// If the operation succeeded, 
        /// the created channel Id is returned in the out parameter.</param>
        /// <returns>Returns false if the operation is still in progress; otherwise, true.</returns> 
        public bool IsCompleted(string operationId, out string channelId)
        {
            IOperation operation = _context.Operations.GetOperation(operationId);
            bool completed = false;

            channelId = null;

            switch (operation.State)
            {
                case OperationState.Failed:
                    // Handle the failure. 
                    // For example, throw an exception. 
                    // Use the following information in the exception: operationId, operation.ErrorMessage.
                    break;
                case OperationState.Succeeded:
                    completed = true;
                    channelId = operation.TargetEntityId;
                    break;
                case OperationState.InProgress:
                    completed = false;
                    break;
            }
            return completed;
        }

        private static ChannelInput CreateChannelInput()
        {
            return new ChannelInput
            {
                StreamingProtocol = StreamingProtocol.RTMP,
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

        private static ChannelOutput CreateChannelOutput()
        {
            return new ChannelOutput
            {
                Hls = new ChannelOutputHls { FragmentsPerSegment = 1 }
            };
        }
    }

### <a name="the-client-code"></a>O código de cliente
    ChannelOperations channelOperations = new ChannelOperations();
    string opId = channelOperations.StartChannelCreation("MyChannel001");

    string channelId = null;
    bool isCompleted = false;

    while (isCompleted == false)
    {
        System.Threading.Thread.Sleep(TimeSpan.FromSeconds(30));
        isCompleted = channelOperations.IsCompleted(opId, out channelId);
    }

    // If we got here, we should have the newly created channel id.
    Console.WriteLine(channelId);



## <a name="media-services-learning-paths"></a>Percursos de aprendizagem dos Media Services
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Enviar comentários
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

