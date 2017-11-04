---
title: "Diagnóstico de Service Fabric com monitorização de estado Reliable Services do Azure | Microsoft Docs"
description: "Funcionalidade de diagnóstico para monitorização de estado Reliable Services no Azure Service Fabric"
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: timlt
editor: 
ms.assetid: ae0e8f99-69ab-4d45-896d-1fa80ed45659
ms.service: Service-Fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 10/15/2017
ms.author: dekapur
ms.openlocfilehash: edcaaaf8f1619082b33195aedf1fb1abf32e85b1
ms.sourcegitcommit: 804db51744e24dca10f06a89fe950ddad8b6a22d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/30/2017
---
# <a name="diagnostic-functionality-for-stateful-reliable-services"></a>Funcionalidade de diagnóstico dos Reliable Services com Monitorização de Estado
A classe de Azure Service Fabric com monitorização de estado fiável serviços StatefulServiceBase emite [EventSource](https://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource.aspx) eventos que podem ser utilizados para depurar o serviço, fornecer informações sobre como o tempo de execução está a funcionar e ajudar a resolver problemas.

## <a name="eventsource-events"></a>Eventos de EventSource
O nome de EventSource para a classe de monitorização de estado Reliable Services StatefulServiceBase é "Microsoft-ServiceFabric-Services." Eventos a partir desta origem de evento são apresentados no [eventos de diagnóstico](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md#view-service-fabric-system-events-in-visual-studio) janela quando o serviço está a ser [debugged no Visual Studio](service-fabric-debugging-your-application.md).

São exemplos de ferramentas e tecnologias que ajudam na recolha de e/ou a visualização de eventos de EventSource [PerfView](http://www.microsoft.com/download/details.aspx?id=28567), [diagnósticos do Azure](../cloud-services/cloud-services-dotnet-diagnostics.md)e o [Microsoft TraceEvent Library](http://www.nuget.org/packages/Microsoft.Diagnostics.Tracing.TraceEvent).

## <a name="events"></a>Eventos
| Nome do evento | ID de evento | Nível | Descrição do evento |
| --- | --- | --- | --- |
| StatefulRunAsyncInvocation |1 |Informativo |Emitidos quando a tarefa de runasync com de serviço é iniciada |
| StatefulRunAsyncCancellation |2 |Informativo |Emitidos quando a tarefa de runasync com do serviço foi cancelada |
| StatefulRunAsyncCompletion |3 |Informativo |Emitidos quando a tarefa de runasync com de serviço está concluída |
| StatefulRunAsyncSlowCancellation |4 |Aviso |Quando a tarefa de runasync com serviço demora demasiado tempo a completar o cancelamento de emitidos |
| StatefulRunAsyncFailure |5 |Erro |Emitidos quando a tarefa de runasync com serviço emite uma exceção |

## <a name="interpret-events"></a>Interpretar os eventos
Eventos de StatefulRunAsyncInvocation, StatefulRunAsyncCompletion e StatefulRunAsyncCancellation são úteis para o escritor do serviço para compreender o ciclo de vida de um serviço, bem como a temporização quando um serviço é iniciado, cancela ou termina. Estas informações podem ser úteis quando depurar problemas do serviço ou compreender o ciclo de vida do serviço.

Os escritores de serviço devem preste especial atenção a eventos StatefulRunAsyncSlowCancellation e StatefulRunAsyncFailure porque indicam problemas com o serviço.

StatefulRunAsyncFailure é emitida sempre que a tarefa de RunAsync() serviço emite uma exceção. Normalmente, uma exceção emitida indica um erro ou erros no serviço. Além disso, a exceção provoca o serviço falhar, pelo que este for movido para um nó diferente. Esta operação pode ser dispendiosa e pode atrasar pedidos recebidos, enquanto o serviço é movido. Os escritores de serviço devem determinar a causa da exceção e, se possível, mitigá-lo.

StatefulRunAsyncSlowCancellation é emitida sempre que um pedido de cancelamento da tarefa runasync com demora mais de quatro segundos. Quando um serviço demora demasiado tempo a completar o cancelamento, afeta a capacidade do serviço de rapidamente ser reiniciada noutro nó. Este cenário poderá afetar a disponibilidade geral do serviço.

## <a name="next-steps"></a>Passos seguintes
[Fornecedores de EventSource no PerfView](https://blogs.msdn.microsoft.com/vancem/2012/07/09/introduction-tutorial-logging-etw-events-in-c-system-diagnostics-tracing-eventsource/)
