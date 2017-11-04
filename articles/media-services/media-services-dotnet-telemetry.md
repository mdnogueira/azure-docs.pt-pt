---
title: Configurar a telemetria de Media Services do Azure com o .NET | Microsoft Docs
description: Este artigo mostra como utilizar a telemetria de Media Services do Azure com o .NET SDK.
services: media-services
documentationcenter: 
author: Juliako
manager: cfowler
editor: 
ms.assetid: f8f55e37-0714-49ea-bf4a-e6c1319bec44
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/18/2017
ms.author: juliako
ms.openlocfilehash: 1d857f3d062d8d1b15c64fa4b8c3e27ad6c2247e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="configuring-azure-media-services-telemetry-with-net"></a>Configurar a telemetria de Media Services do Azure com o .NET

Este tópico descreve os passos gerais que podem consumir quando configurar a telemetria de serviços de suporte de dados do Azure (AMS) utilizando o .NET SDK. 

>[!NOTE]
>A explicação detalhada das quais é telemetria do AMS e como consumir os-lo, consulte o [descrição geral](media-services-telemetry-overview.md) tópico.

Pode consumir dados de telemetria de uma das seguintes formas:

- Ler dados diretamente a partir do armazenamento de tabelas do Azure (por exemplo, utilizando o SDK de armazenamento). Para obter a descrição das tabelas de armazenamento de telemetria, consulte o **consumir informações de telemetria** no [isto](https://msdn.microsoft.com/library/mt742089.aspx) tópico.

Ou

- Utilize o suporte do SDK do .NET de Media Services para a leitura de dados de armazenamento. Este tópico mostra como ativar a telemetria para a conta de AMS especificada e como consultar as métricas utilizando o SDK .NET do Azure Media Services.  

## <a name="configuring-telemetry-for-a-media-services-account"></a>Configurar a telemetria para uma conta de Media Services

Os seguintes passos são necessários para ativar a telemetria:

- Obter as credenciais da conta de armazenamento ligado à conta de Media Services. 
- Criar um ponto final da notificação com **EndPointType** definido como **AzureTable** e endPointAddress apontar para a tabela de armazenamento.

        INotificationEndPoint notificationEndPoint = 
                      _context.NotificationEndPoints.Create("monitoring", 
                      NotificationEndPointType.AzureTable,
                      "https://" + _mediaServicesStorageAccountName + ".table.core.windows.net/");

- Definições de criação de uma configuração de monitorização para os serviços que pretende monitorizar. Mais do que um definições de configuração de monitorização é permitida. 
  
        IMonitoringConfiguration monitoringConfiguration = _context.MonitoringConfigurations.Create(notificationEndPoint.Id,
            new List<ComponentMonitoringSetting>()
            {
                new ComponentMonitoringSetting(MonitoringComponent.Channel, MonitoringLevel.Normal),
                new ComponentMonitoringSetting(MonitoringComponent.StreamingEndpoint, MonitoringLevel.Normal)
            });

## <a name="consuming-telemetry-information"></a>Consumir informações de telemetria

Para informações sobre consumo de telemetria de informações, consulte [isto](media-services-telemetry-overview.md) tópico.

## <a name="create-and-configure-a-visual-studio-project"></a>Criar e configurar um projeto de Visual Studio

1. Configure o seu ambiente de desenvolvimento e preencha o ficheiro app.config com informações da ligação, conforme descrito em [Media Services development with .NET](media-services-dotnet-how-to-use.md) (Desenvolvimento de Serviços de Multimédia com .NET). 

2. Adicione o seguinte elemento para **appSettings** definida no ficheiro App. config:

    <add key="StorageAccountName" value="storage_name" />
 
## <a name="example"></a>Exemplo  
    
O exemplo seguinte mostra como ativar a telemetria para a conta de AMS especificada e como consultar as métricas utilizando o SDK .NET do Azure Media Services.  

    using System;
    using System.Collections.Generic;
    using System.Configuration;
    using System.Linq;
    using Microsoft.WindowsAzure.MediaServices.Client;

    namespace AMSMetrics
    {
        class Program
        {
        private static readonly string _AADTenantDomain =
            ConfigurationManager.AppSettings["AADTenantDomain"];
        private static readonly string _RESTAPIEndpoint =
            ConfigurationManager.AppSettings["MediaServiceRESTAPIEndpoint"];

        private static readonly string _mediaServicesStorageAccountName =
            ConfigurationManager.AppSettings["StorageAccountName"];

        // Field for service context.
        private static CloudMediaContext _context = null;

        private static IStreamingEndpoint _streamingEndpoint = null;
        private static IChannel _channel = null;

        static void Main(string[] args)
        {
            var tokenCredentials = new AzureAdTokenCredentials(_AADTenantDomain, AzureEnvironments.AzureCloudEnvironment);
            var tokenProvider = new AzureAdTokenProvider(tokenCredentials);

            _context = new CloudMediaContext(new Uri(_RESTAPIEndpoint), tokenProvider);

            _streamingEndpoint = _context.StreamingEndpoints.FirstOrDefault();
            _channel = _context.Channels.FirstOrDefault();

            var monitoringConfigurations = _context.MonitoringConfigurations;
            IMonitoringConfiguration monitoringConfiguration = null;

            // No more than one monitoring configuration settings is allowed.
            if (monitoringConfigurations.ToArray().Length != 0)
            {
            monitoringConfiguration = _context.MonitoringConfigurations.FirstOrDefault();
            }
            else
            {
            INotificationEndPoint notificationEndPoint =
                      _context.NotificationEndPoints.Create("monitoring",
                      NotificationEndPointType.AzureTable, GetTableEndPoint());

            monitoringConfiguration = _context.MonitoringConfigurations.Create(notificationEndPoint.Id,
                new List<ComponentMonitoringSetting>()
                {
                    new ComponentMonitoringSetting(MonitoringComponent.Channel, MonitoringLevel.Normal),
                    new ComponentMonitoringSetting(MonitoringComponent.StreamingEndpoint, MonitoringLevel.Normal)

                });
            }

            //Print metrics for a Streaming Endpoint.
            PrintStreamingEndpointMetrics();

            Console.ReadLine();
        }

        private static string GetTableEndPoint()
        {
            return "https://" + _mediaServicesStorageAccountName + ".table.core.windows.net/";
        }

        private static void PrintStreamingEndpointMetrics()
        {
            Console.WriteLine(string.Format("Telemetry for streaming endpoint '{0}'", _streamingEndpoint.Name));

            DateTime timerangeEnd = DateTime.UtcNow;
            DateTime timerangeStart = DateTime.UtcNow.AddHours(-5);

            // Get some streaming endpoint metrics.
            var telemetry = _streamingEndpoint.GetTelemetry();

            var res = telemetry.GetStreamingEndpointRequestLogs(timerangeStart, timerangeEnd);

            Console.Title = "Streaming endpoint metrics:";

            foreach (var log in res)
            {
            Console.WriteLine("AccountId: {0}", log.AccountId);
            Console.WriteLine("BytesSent: {0}", log.BytesSent);
            Console.WriteLine("EndToEndLatency: {0}", log.EndToEndLatency);
            Console.WriteLine("HostName: {0}", log.HostName);
            Console.WriteLine("ObservedTime: {0}", log.ObservedTime);
            Console.WriteLine("PartitionKey: {0}", log.PartitionKey);
            Console.WriteLine("RequestCount: {0}", log.RequestCount);
            Console.WriteLine("ResultCode: {0}", log.ResultCode);
            Console.WriteLine("RowKey: {0}", log.RowKey);
            Console.WriteLine("ServerLatency: {0}", log.ServerLatency);
            Console.WriteLine("StatusCode: {0}", log.StatusCode);
            Console.WriteLine("StreamingEndpointId: {0}", log.StreamingEndpointId);
            Console.WriteLine();
            }

            Console.WriteLine();
        }

        private static void PrintChannelMetrics()
        {
            if (_channel == null)
            {
            Console.WriteLine("There are no channels in this AMS account");
            return;
            }

            Console.WriteLine(string.Format("Telemetry for channel '{0}'", _channel.Name));

            DateTime timerangeEnd = DateTime.UtcNow; 
            DateTime timerangeStart = DateTime.UtcNow.AddHours(-5);

            // Get some channel metrics.
            var telemetry = _channel.GetTelemetry();

            var channelMetrics = telemetry.GetChannelHeartbeats(timerangeStart, timerangeEnd);

            // Print the channel metrics.
            Console.WriteLine("Channel metrics:");

            foreach (var channelHeartbeat in channelMetrics.OrderBy(x => x.ObservedTime))
            {
            Console.WriteLine(
                "    Observed time: {0}, Last timestamp: {1}, Incoming bitrate: {2}",
                channelHeartbeat.ObservedTime,
                channelHeartbeat.LastTimestamp,
                channelHeartbeat.IncomingBitrate);
            }

            Console.WriteLine();
        }
        }
    }


## <a name="next-steps"></a>Passos seguintes

[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Enviar comentários

[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]
