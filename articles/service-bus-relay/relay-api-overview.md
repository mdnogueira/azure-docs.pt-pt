---
title: "Descrição geral de API de reencaminhamento do Azure | Microsoft Docs"
description: "Descrição geral das APIs de reencaminhamento do Azure disponíveis"
services: event-hubs
documentationcenter: na
author: sethmanheim
manager: timlt
editor: 
ms.assetid: fdaa1d2b-bd80-4e75-abb9-0c3d0773af2d
ms.service: service-bus-relay
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/05/2017
ms.author: sethm
ms.openlocfilehash: 60413bd110fd4021b070f94d6338a50e1b321dea
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="available-relay-apis"></a>APIs de reencaminhamento disponíveis

## <a name="runtime-apis"></a>APIs de tempo de execução

A tabela seguinte apresenta uma lista de todos os clientes de tempo de execução de reencaminhamento atualmente disponíveis.

O [informações adicionais](#additional-information) secção contém mais informações sobre o estado de cada biblioteca de tempo de execução.

| Idioma/plataforma | Funcionalidade disponível | Pacote de cliente | Repositório |
| --- | --- | --- | --- |
| Padrão de .NET | Ligações Híbridas | [Microsoft.Azure.Relay](https://www.nuget.org/packages/Microsoft.Azure.Relay/) | [GitHub](https://github.com/azure/azure-relay-dotnet) |
| .NET framework | Reencaminhamento do WCF | [Windowsazure](https://www.nuget.org/packages/WindowsAzure.ServiceBus/) | N/D |
| Nó | Ligações Híbridas | [`hyco-ws`](https://www.npmjs.com/package/hyco-ws)<br/>[`hyco-websocket`](https://www.npmjs.com/package/hyco-websocket) | [GitHub](https://github.com/Azure/azure-relay-node) |

### <a name="additional-information"></a>Informações adicionais

#### <a name="net"></a>.NET
A ecossistema de .NET tem vários tempos de execução, por conseguinte, existem vários bibliotecas .NET para os Event Hubs. A biblioteca .NET padrão pode ser executada através do .NET Core ou o .NET Framework, enquanto a biblioteca de .NET Framework só pode ser executada num ambiente de .NET Framework. Para obter mais informações sobre estruturas de .NET, consulte [versões framework](/dotnet/articles/standard/frameworks#framework-versions).

## <a name="next-steps"></a>Passos seguintes
Para mais informações sobre o reencaminhamento do Azure, visite estas ligações:
* [O que é o Reencaminhamento do Azure?](relay-what-is-it.md)
* [FAQ de Reencaminhamento](relay-faq.md)