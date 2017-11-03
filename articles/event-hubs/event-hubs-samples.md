---
title: Exemplos de Event Hubs do Azure | Microsoft Docs
description: Exemplos de Event Hubs do Azure
services: event-hubs
documentationcenter: na
author: sethmanheim
manager: timlt
editor: 
ms.assetid: 
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/15/2017
ms.author: sethm
ms.openlocfilehash: ae9fbd97a1747d8f14c561f247a0973bb11fd039
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="event-hubs-samples"></a>Exemplos de Hubs de eventos 

O conjunto de exemplos de Event Hubs do Azure demonstra as principais funcionalidades no [Event Hubs do Azure](/azure/event-hubs/). Este artigo categoriza e descreve os exemplos disponíveis, com ligações para cada um.

No momento desta redação, exemplos de Event Hubs estão localizados em vários locais diferentes:

- [Exemplos de código do MSDN para programadores](https://code.msdn.microsoft.com/site/search?query=event%20hubs&f%5B0%5D.Value=event%20hubs&f%5B0%5D.Type=SearchText&ac=5)
- [GitHub](https://github.com/Azure/azure-event-hubs/tree/master/samples)

Para obter mais informações sobre as diferentes versões do .NET Framework, consulte [estruturas e destinos](/dotnet/articles/standard/frameworks).

Mais exemplos será adicionado ao longo do tempo, por isso, verifique novamente aqui frequentemente para atualizações.

## <a name="net-standard"></a>Padrão de .NET

Os exemplos seguintes demonstram como enviar e receber eventos utilizando o [cliente dos Event Hubs](https://github.com/Azure/azure-event-hubs-dotnet/blob/master/readme.md) para o [biblioteca .NET padrão](/dotnet/articles/standard/library).

### <a name="send-events"></a>Enviar eventos 

O [começar a enviar](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/SampleSender) exemplo demonstra como escrever uma aplicação de consola .NET Core envia eventos para um hub de eventos.

### <a name="receive-events"></a>Receber eventos 

O [começar a receber com o anfitrião do processador de eventos](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/SampleEphReceiver) exemplo é uma aplicação de consola .NET Core que recebe mensagens do hub de eventos utilizando o anfitrião do processador de eventos.

## <a name="net-framework"></a>.NET framework   

Estes exemplos demonstram várias outras funcionalidades do Event Hubs do Azure, como objetivo o [biblioteca .NET Framework](/dotnet/framework/index).
 
### <a name="notify-users-of-events-received"></a>Notificar os utilizadores dos eventos recebidos

O [AppToNotifyUsers](https://github.com/Azure-Samples/event-hubs-dotnet-user-notifications) exemplo notifica os utilizadores de dados recebidos de sensores ou outros sistemas.

### <a name="get-started-with-event-hubs"></a>Introdução ao Event Hubs 

O [Event Hubs introdução](https://code.msdn.microsoft.com/Service-Bus-Event-Hub-286fd097) exemplo demonstra as funcionalidades básicas dos Event Hubs, tais como criar um hub de eventos, enviar eventos para um hub de eventos e consumir eventos utilizando o [anfitrião do processador de eventos](https://www.nuget.org/packages/Microsoft.Azure.ServiceBus.EventProcessorHost/) .

### <a name="scale-out-event-processing"></a>Aumentar horizontalmente o processamento de eventos 

O [aumentar horizontalmente o processamento de eventos](https://code.msdn.microsoft.com/Service-Bus-Event-Hub-45f43fc3) exemplo demonstra como utilizar o [anfitrião do processador de eventos](https://www.nuget.org/packages/Microsoft.Azure.ServiceBus.EventProcessorHost/) para distribuir a carga de trabalho de consumo de fluxo de Event Hubs. Mostra como implementar o **EventProcessor** e **EventProcessorFactory** objetos a gerir o fluxo de eventos. 

###  <a name="pull-data-from-sql-into-an-event-hub"></a>Extrair dados do SQL Server para um hub de eventos

O [dados do SQL extrair](https://github.com/Azure-Samples/event-hubs-dotnet-import-from-sql) exemplo mostra como solicitar dados de uma tabela SQL e enviá-lo para um hub de eventos, para utilizar como uma entrada em aplicações analíticas a jusante.

### <a name="pull-web-data-into-an-event-hub"></a>Extrair dados da web para um hub de eventos 

O [importar dados a partir da web](https://github.com/Azure-Samples/event-hubs-dotnet-importfromweb) exemplo mostra como obter dados a partir de feeds públicos (por exemplo, o feed de informações de tráfego do departamento de transportes) e enviá-lo para um hub de eventos.

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre as versões do .NET Framework, visitando as seguintes ligações:

- [Estruturas e destinos](/dotnet/articles/standard/frameworks)
- [.NET framework 4.6 e 4.5](/dotnet/framework/index)

Pode saber mais sobre os Event Hubs nos seguintes artigos:

- [Descrição geral dos Hubs de Eventos](event-hubs-what-is-event-hubs.md)
- [Criar um hub de eventos](event-hubs-create.md)
- [FAQ dos Hubs de Eventos](event-hubs-faq.md)