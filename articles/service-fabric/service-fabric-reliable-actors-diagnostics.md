---
title: "Monitorização e diagnóstico de atores | Microsoft Docs"
description: "Este artigo descreve os diagnósticos e funcionalidades no tempo de execução do Service Fabric Reliable Actors, incluindo os eventos e contadores de desempenho emitidos por este de monitorização de desempenho."
services: service-fabric
documentationcenter: .net
author: abhishekram
manager: timlt
editor: vturecek
ms.assetid: 1c229923-670a-4634-ad59-468ff781ad18
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 10/26/2017
ms.author: abhisram
ms.openlocfilehash: 5fbef8a3fb32f4bc47856ef6c6b459ae389dd541
ms.sourcegitcommit: 3ab5ea589751d068d3e52db828742ce8ebed4761
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/27/2017
---
# <a name="diagnostics-and-performance-monitoring-for-reliable-actors"></a>Diagnóstico e monitorização do desempenho dos Reliable Actors
O tempo de execução Reliable Actors emite [EventSource](https://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource.aspx) eventos e [contadores de desempenho](https://msdn.microsoft.com/library/system.diagnostics.performancecounter.aspx). Estes fornecem informações sobre a forma como o tempo de execução está a funcionar e ajudam a resolver problemas e monitorização do desempenho.

## <a name="eventsource-events"></a>Eventos de EventSource
O nome do fornecedor de EventSource para o tempo de execução Reliable Actors é "Microsoft-ServiceFabric-Atores". Eventos a partir desta origem de evento são apresentados no [eventos de diagnóstico](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md#view-service-fabric-system-events-in-visual-studio) janela quando a aplicação de ator está a ser [debugged no Visual Studio](service-fabric-debugging-your-application.md).

São exemplos de ferramentas e tecnologias que ajudam na recolha de e/ou a visualização de eventos de EventSource [PerfView](http://www.microsoft.com/download/details.aspx?id=28567), [diagnósticos do Azure](../cloud-services/cloud-services-dotnet-diagnostics.md), [registo semântica](https://msdn.microsoft.com/library/dn774980.aspx)e o [ Biblioteca do Microsoft TraceEvent](http://www.nuget.org/packages/Microsoft.Diagnostics.Tracing.TraceEvent).

### <a name="keywords"></a>Palavras-chave
Todos os eventos que pertencem a EventSource de Atores fiável estão associados com uma ou mais palavras-chave. Isto permite a filtragem de eventos que são recolhidos. Os seguintes bits de palavra-chave são definidos.

| bits | Descrição |
| --- | --- |
| 0x1 |Conjunto de eventos importantes que resumem a operação do tempo de execução de Atores de recursos de infraestrutura. |
| 0x2 |Conjunto de eventos que descrevem chamadas de método de ator. Para obter mais informações, consulte o [tópico introdutórias sobre atores](service-fabric-reliable-actors-introduction.md). |
| 0x4 |Conjunto de eventos relacionados com o estado do ator. Para obter mais informações, consulte o tópico sobre [gestão do Estado de ator](service-fabric-reliable-actors-state-management.md). |
| 0x8 |Conjunto de eventos relacionados com a simultaneidade de com base em vez de mensagens em fila no ator. Para obter mais informações, consulte o tópico sobre [simultaneidade](service-fabric-reliable-actors-introduction.md#concurrency). |

## <a name="performance-counters"></a>Contadores de desempenho
O tempo de execução Reliable Actors define as seguintes categorias de contador de desempenho.

| Categoria | Descrição |
| --- | --- |
| Ator do Service Fabric |Contadores específicos do Azure Service Fabric atores, por exemplo, tempo decorridos para guardar o estado de ator |
| Método de Ator do Service Fabric |Contadores específicos para métodos implementados pelos atores do Service Fabric, por exemplo, com que frequência um método de atores é invocado |

Cada uma das categorias acima tem um ou mais contadores.

O [Monitor de desempenho do Windows](https://technet.microsoft.com/library/cc749249.aspx) aplicação que está disponível por predefinição no sistema operativo Windows pode ser utilizada para recolher e visualizar dados de contador de desempenho. [Diagnóstico do Azure](../cloud-services/cloud-services-dotnet-diagnostics.md) é outra opção para recolher dados de contador de desempenho e carregá-lo para as tabelas do Azure.

### <a name="performance-counter-instance-names"></a>Nomes de instâncias do contador de desempenho
Um cluster com um grande número de serviços de atores ou partições de serviço de atores terão um grande número de instâncias de contador de desempenho de atores. Os nomes das instâncias do contador de desempenho podem ajudar a identificar o específicos [partição](service-fabric-reliable-actors-platform.md#service-fabric-partition-concepts-for-actors) e método de ator (se aplicável) que se encontra associada a instância do contador de desempenho.

#### <a name="service-fabric-actor-category"></a>Categoria de Atores do Service Fabric
Para a categoria de `Service Fabric Actor`, os nomes de instância do contador estão no formato seguinte:

`ServiceFabricPartitionID_ActorsRuntimeInternalID`

*ServiceFabricPartitionID* é a representação de cadeia do ID de partição de recursos de infraestrutura de serviço que se encontra associada a instância do contador de desempenho. O ID de partição é um GUID e respetiva representação de cadeia que é gerada através de [ `Guid.ToString` ](https://msdn.microsoft.com/library/97af8hh4.aspx) método com o especificador de formato "D".

*ActorRuntimeInternalID* é a representação de cadeia de um número inteiro de 64 bits que é gerado pelo runtime Atores de recursos de infraestrutura para a sua utilização interna. Isto está incluído o nome de instância do contador de desempenho para garantir a exclusividade e evitar conflitos com outros nomes de instância do contador de desempenho. Os utilizadores não devem tentar interpretar esta parte do nome de instância do contador de desempenho.

Segue-se um exemplo de um nome de instância do contador para um contador que pertence o `Service Fabric Actor` categoria:

`2740af29-78aa-44bc-a20b-7e60fb783264_635650083799324046`

No exemplo acima, `2740af29-78aa-44bc-a20b-7e60fb783264` é a representação de cadeia de ID de partição Service Fabric, e `635650083799324046` está a utilizar o ID de 64 bits que é gerado para o tempo de execução 's interno.

#### <a name="service-fabric-actor-method-category"></a>Categoria de método de Ator do Service Fabric
Para a categoria de `Service Fabric Actor Method`, os nomes de instância do contador estão no formato seguinte:

`MethodName_ActorsRuntimeMethodId_ServiceFabricPartitionID_ActorsRuntimeInternalID`

*MethodName* é o nome do método de ator que se encontra associada a instância do contador de desempenho. O formato do nome do método é determinado com base em algumas lógica em tempo de execução dos Atores de recursos de infraestrutura equilibrar a facilitar a leitura do nome com restrições ao comprimento máximo dos nomes de instância do contador de desempenho no Windows.

*ActorsRuntimeMethodId* é a representação de cadeia de um número inteiro de 32 bits que é gerado pelo runtime Atores de recursos de infraestrutura para a sua utilização interna. Isto está incluído o nome de instância do contador de desempenho para garantir a exclusividade e evitar conflitos com outros nomes de instância do contador de desempenho. Os utilizadores não devem tentar interpretar esta parte do nome de instância do contador de desempenho.

*ServiceFabricPartitionID* é a representação de cadeia do ID de partição de recursos de infraestrutura de serviço que se encontra associada a instância do contador de desempenho. O ID de partição é um GUID e respetiva representação de cadeia que é gerada através de [ `Guid.ToString` ](https://msdn.microsoft.com/library/97af8hh4.aspx) método com o especificador de formato "D".

*ActorRuntimeInternalID* é a representação de cadeia de um número inteiro de 64 bits que é gerado pelo runtime Atores de recursos de infraestrutura para a sua utilização interna. Isto está incluído o nome de instância do contador de desempenho para garantir a exclusividade e evitar conflitos com outros nomes de instância do contador de desempenho. Os utilizadores não devem tentar interpretar esta parte do nome de instância do contador de desempenho.

Segue-se um exemplo de um nome de instância do contador para um contador que pertence o `Service Fabric Actor Method` categoria:

`ivoicemailboxactor.leavemessageasync_2_89383d32-e57e-4a9b-a6ad-57c6792aa521_635650083804480486`

No exemplo acima, `ivoicemailboxactor.leavemessageasync` é o nome de método `2` é o ID de 32 bits gerado para utilização interna o tempo de execução, `89383d32-e57e-4a9b-a6ad-57c6792aa521` é a representação de cadeia de ID de partição Service Fabric, e `635650083804480486` é o ID de 64 bits gerado para o utilização interna do tempo de execução.

## <a name="list-of-events-and-performance-counters"></a>Lista de eventos e contadores de desempenho
### <a name="actor-method-events-and-performance-counters"></a>Eventos de método de ator e contadores de desempenho
O tempo de execução Reliable Actors emite os seguintes eventos relacionados com [métodos de ator](service-fabric-reliable-actors-introduction.md).

| Nome do evento | ID de evento | Nível | Palavra-chave | Descrição |
| --- | --- | --- | --- | --- |
| ActorMethodStart |7 |Verboso |0x2 |Tempo de execução de atores está prestes a invocar um método de ator. |
| ActorMethodStop |8 |Verboso |0x2 |Um método de ator terminou a execução. Ou seja, chamada assíncrona do tempo de execução para o método de ator devolveu e a tarefa devolvida pelo método de ator foi concluída. |
| ActorMethodThrewException |9 |Aviso |0x3 |Foi emitida uma exceção durante a execução de um método de ator durante chamada assíncrona do tempo de execução para o método de ator ou durante a execução da tarefa devolvido pelo método de ator. Este evento indica algum tipo de falha com o código de ator que necessita de investigação. |

O tempo de execução Reliable Actors publica os seguintes contadores de desempenho relacionados com a execução dos métodos de ator.

| Nome da categoria | Nome do contador | Descrição |
| --- | --- | --- |
| Método de Ator do Service Fabric |Invocações por segundo |Número de vezes que o método do serviço de atores é invocado por segundo |
| Método de Ator do Service Fabric |Média em milissegundos por invocação |Tempo decorrido para executar o método do serviço de ator em milissegundos |
| Método de Ator do Service Fabric |Exceções emitidas/seg |Número de vezes que o serviço de atores método emitiu uma exceção por segundo |

### <a name="concurrency-events-and-performance-counters"></a>Eventos de simultaneidade e contadores de desempenho
O tempo de execução Reliable Actors emite os seguintes eventos relacionados com [simultaneidade](service-fabric-reliable-actors-introduction.md#concurrency).

| Nome do evento | ID de evento | Nível | Palavra-chave | Descrição |
| --- | --- | --- | --- | --- |
| ActorMethodCallsWaitingForLock |12 |Verboso |0x8 |Este evento é escrito no início de cada vez novo num ator. Contém o número de pendentes chamadas de atores que estão a aguardar para adquirir o bloqueio por ator que impõe a concorrência com base em ativar. |

O tempo de execução Reliable Actors publica os seguintes contadores de desempenho relacionados com a simultaneidade.

| Nome da categoria | Nome do contador | Descrição |
| --- | --- | --- |
| Ator do Service Fabric |chamadas de atores a aguardar que o bloqueio de ator |Número de pendentes a aguardar para adquirir o bloqueio por ator que impõe a concorrência com base em vez de chamadas de atores |
| Ator do Service Fabric |Média em milissegundos por bloqueio de espera |Tempo decorrido (em milissegundos) para adquirir o bloqueio por ator que impõe a concorrência com base em vez |
| Ator do Service Fabric |Média em milissegundos bloqueio de ator ativado |Tempo (em milissegundos) para o qual o bloqueio por ator está retido |

### <a name="actor-state-management-events-and-performance-counters"></a>Eventos de gestão do Estado de ator e contadores de desempenho
O tempo de execução Reliable Actors emite os seguintes eventos relacionados com [gestão do Estado de ator](service-fabric-reliable-actors-state-management.md).

| Nome do evento | ID de evento | Nível | Palavra-chave | Descrição |
| --- | --- | --- | --- | --- |
| ActorSaveStateStart |10 |Verboso |0x4 |Tempo de execução de atores está prestes a guardar o estado de ator. |
| ActorSaveStateStop |11 |Verboso |0x4 |Tempo de execução de atores acabou de guardar o estado de ator. |

O tempo de execução Reliable Actors publica os seguintes contadores de desempenho relacionados com a gestão de estado de ator.

| Nome da categoria | Nome do contador | Descrição |
| --- | --- | --- |
| Ator do Service Fabric |Média em milissegundos por operação de guardar Estado |Tempo decorrido para guardar o estado de ator em milissegundos |
| Ator do Service Fabric |Média em milissegundos por operação de estado de carregamento |Tempo decorrido para carregar o estado de ator em milissegundos |

### <a name="events-related-to-actor-replicas"></a>Eventos relacionados com as réplicas de ator
O tempo de execução Reliable Actors emite os seguintes eventos relacionados com [réplicas de ator](service-fabric-reliable-actors-platform.md#service-fabric-partition-concepts-for-actors).

| Nome do evento | ID de evento | Nível | Palavra-chave | Descrição |
| --- | --- | --- | --- | --- |
| ReplicaChangeRoleToPrimary |1 |Informativo |0x1 |Réplica de ator Alterar função principal. Isto implica que serão criados os atores para esta partição dentro desta réplica. |
| ReplicaChangeRoleFromPrimary |2 |Informativo |0x1 |Réplica de ator Alterar função não principal. Isto implica que o atores para esta partição já não serão criados no interior desta réplica. Não existem novos pedidos serão entregues atores já criados dentro desta réplica. Irão ser destruídos de atores depois de concluídos todos os pedidos em curso. |

### <a name="actor-activation-and-deactivation-events-and-performance-counters"></a>Eventos de ativação e desativação de ator e contadores de desempenho
O tempo de execução Reliable Actors emite os seguintes eventos relacionados com [ativação ator e desativação](service-fabric-reliable-actors-lifecycle.md).

| Nome do evento | ID de evento | Nível | Palavra-chave | Descrição |
| --- | --- | --- | --- | --- |
| ActorActivated |5 |Informativo |0x1 |Um ator tiver sido ativado. |
| ActorDeactivated |6 |Informativo |0x1 |Um ator foi desativada. |

O tempo de execução Reliable Actors publica os seguintes contadores de desempenho relacionados com a ativação de ator e desativação.

| Nome da categoria | Nome do contador | Descrição |
| --- | --- | --- |
| Ator do Service Fabric |Média OnActivateAsync em milissegundos |Tempo decorrido para executar o método OnActivateAsync em milissegundos |

### <a name="actor-request-processing-performance-counters"></a>Contadores de desempenho do processamento do pedido de ator
Quando um cliente invoca um método através de um objeto de proxy de ator, que resulta numa mensagem de pedido que está a ser enviada através da rede para o serviço de atores. O serviço processa a mensagem de pedido e envia uma resposta de volta para o cliente. O tempo de execução Reliable Actors publica os seguintes contadores de desempenho relacionados com o processamento do pedido de ator.

| Nome da categoria | Nome do contador | Descrição |
| --- | --- | --- |
| Ator do Service Fabric |n. º de pedidos pendentes |Número de pedidos em processamento no serviço |
| Ator do Service Fabric |Média em milissegundos por pedido |Tempo decorrido (em milissegundos) pelo serviço para processar um pedido |
| Ator do Service Fabric |Média em milissegundos para a desserialização do pedido |Tempo decorrido (em milissegundos) para anular a serialização da mensagem de pedido de ator quando recebido de serviço |
| Ator do Service Fabric |Média em milissegundos para a serialização da resposta |Tempo decorrido (em milissegundos) antes da resposta é enviada para o cliente a serialização a mensagem de resposta de ator no serviço |

## <a name="next-steps"></a>Passos seguintes
* [Como Reliable Actors utilizar a plataforma de Service Fabric](service-fabric-reliable-actors-platform.md)
* [Documentação de referência da API de ator](https://msdn.microsoft.com/library/azure/dn971626.aspx)
* [Código de exemplo](https://github.com/Azure/servicefabric-samples)
* [Fornecedores de EventSource no PerfView](https://blogs.msdn.microsoft.com/vancem/2012/07/09/introduction-tutorial-logging-etw-events-in-c-system-diagnostics-tracing-eventsource/)
