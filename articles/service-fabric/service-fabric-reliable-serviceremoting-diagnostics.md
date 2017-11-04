---
title: "ServiceFabric diagnóstico do Azure e monitorização | Microsoft Docs"
description: "Este artigo descreve as funcionalidades de monitorização do desempenho em tempo de execução ServiceRemoting fiável de recursos de infraestrutura de serviço, como os contadores de desempenho emitidos pelo-lo."
services: service-fabric
documentationcenter: .net
author: suchiagicha
manager: timlt
editor: suchiagicha
ms.assetid: 1c229923-670a-4634-ad59-468ff781ad18
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/29/2017
ms.author: suchiagicha
ms.openlocfilehash: f54e157654fb15d2f7ff48ddc666c6c8803c75a2
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="diagnostics-and-performance-monitoring-for-reliable-service-remoting"></a>Diagnóstico e monitorização de desempenho para fiável de serviço de gestão remota
O tempo de execução fiável ServiceRemoting emite [contadores de desempenho](https://msdn.microsoft.com/library/system.diagnostics.performancecounter.aspx). Estes fornecem informações sobre a forma como o ServiceRemoting está a funcionar e ajudam a resolver problemas e monitorização do desempenho.


## <a name="performance-counters"></a>Contadores de desempenho
O tempo de execução fiável ServiceRemoting define as categorias de contador de desempenho seguintes:

| Categoria | Descrição |
| --- | --- |
| Serviço de recursos de infraestrutura |Contadores específicos do serviço de recursos de infraestrutura de serviço de Azure remota, por exemplo, tempo médio de tempo que demora a processar o pedido |
| Método do serviço de recursos de infraestrutura de serviço |Contadores específicos para métodos implementados pelo serviço de comunicação remota do serviço de recursos de infraestrutura, por exemplo, com que frequência um método de serviço é invocado |

Cada uma das categorias anteriores tem um ou mais contadores.

O [Monitor de desempenho do Windows](https://technet.microsoft.com/library/cc749249.aspx) aplicação que está disponível por predefinição no sistema operativo Windows pode ser utilizada para recolher e visualizar dados de contador de desempenho. [Diagnóstico do Azure](../cloud-services/cloud-services-dotnet-diagnostics.md) é outra opção para recolher dados de contador de desempenho e carregá-lo para as tabelas do Azure.

### <a name="performance-counter-instance-names"></a>Nomes de instâncias do contador de desempenho
Um cluster com um grande número de partições ou de serviços de ServiceRemoting ter um grande número de instâncias do contador de desempenho. Os nomes das instâncias do contador de desempenho podem ajudar a identificar a partição específica e método do serviço (se aplicável) que está associada a instância do contador de desempenho.

#### <a name="service-fabric-service-category"></a>Categoria de serviço do Service Fabric
Para a categoria de `Service Fabric Service`, os nomes de instância do contador estão no formato seguinte:

`ServiceFabricPartitionID_ServiceReplicaOrInstanceId_ServiceRuntimeInternalID`

*ServiceFabricPartitionID* é a representação de cadeia do ID de partição de recursos de infraestrutura de serviço que se encontra associada a instância do contador de desempenho. O ID de partição é um GUID e respetiva representação de cadeia que é gerada através de [ `Guid.ToString` ](https://msdn.microsoft.com/library/97af8hh4.aspx) método com o especificador de formato "D".

*ServiceReplicaOrInstanceId* é a representação de cadeia do ID de réplica/instância do serviço de recursos de infraestrutura, que se encontra associada a instância do contador de desempenho.

*ServiceRuntimeInternalID* é a representação de cadeia de um número inteiro de 64 bits que é gerado pelo tempo de execução do serviço de recursos de infraestrutura para a sua utilização interna. Isto está incluído o nome de instância do contador de desempenho para garantir a exclusividade e evitar conflitos com outros nomes de instância do contador de desempenho. Os utilizadores não devem tentar interpretar esta parte do nome de instância do contador de desempenho.

Segue-se um exemplo de um nome de instância do contador para um contador que pertence o `Service Fabric Service` categoria:

`2740af29-78aa-44bc-a20b-7e60fb783264_635650083799324046_5008379932`

No exemplo anterior, `2740af29-78aa-44bc-a20b-7e60fb783264` é a representação de cadeia de ID de partição Service Fabric, `635650083799324046` é representação de cadeia de réplica/InstanceId e `5008379932` está a utilizar o ID de 64 bits que é gerado para o tempo de execução 's interno.

#### <a name="service-fabric-service-method-category"></a>Categoria de método do serviço de recursos de infraestrutura de serviço
Para a categoria de `Service Fabric Service Method`, os nomes de instância do contador estão no formato seguinte:

`MethodName_ServiceRuntimeMethodId_ServiceFabricPartitionID_ServiceReplicaOrInstanceId_ServiceRuntimeInternalID`

*MethodName* é o nome do método de serviço que se encontra associada a instância do contador de desempenho. O formato do nome do método é determinado com base em algumas lógica no tempo de execução do serviço de recursos de infraestrutura equilibrar a facilitar a leitura do nome com restrições ao comprimento máximo dos nomes de instância do contador de desempenho no Windows.

*ServiceRuntimeMethodId* é a representação de cadeia de um número inteiro de 32 bits que é gerado pelo tempo de execução do serviço de recursos de infraestrutura para a sua utilização interna. Isto está incluído o nome de instância do contador de desempenho para garantir a exclusividade e evitar conflitos com outros nomes de instância do contador de desempenho. Os utilizadores não devem tentar interpretar esta parte do nome de instância do contador de desempenho.

*ServiceFabricPartitionID* é a representação de cadeia do ID de partição de recursos de infraestrutura de serviço que se encontra associada a instância do contador de desempenho. O ID de partição é um GUID e respetiva representação de cadeia que é gerada através de [ `Guid.ToString` ](https://msdn.microsoft.com/library/97af8hh4.aspx) método com o especificador de formato "D".

*ServiceReplicaOrInstanceId* é a representação de cadeia do ID de réplica/instância do serviço de recursos de infraestrutura, que se encontra associada a instância do contador de desempenho.

*ServiceRuntimeInternalID* é a representação de cadeia de um número inteiro de 64 bits que é gerado pelo tempo de execução do serviço de recursos de infraestrutura para a sua utilização interna. Isto está incluído o nome de instância do contador de desempenho para garantir a exclusividade e evitar conflitos com outros nomes de instância do contador de desempenho. Os utilizadores não devem tentar interpretar esta parte do nome de instância do contador de desempenho.

Segue-se um exemplo de um nome de instância do contador para um contador que pertence o `Service Fabric Service Method` categoria:

`ivoicemailboxservice.leavemessageasync_2_89383d32-e57e-4a9b-a6ad-57c6792aa521_635650083804480486_5008380`

No exemplo anterior, `ivoicemailboxservice.leavemessageasync` é o nome de método `2` é o ID de 32 bits gerado para utilização interna o tempo de execução, `89383d32-e57e-4a9b-a6ad-57c6792aa521` é a representação de cadeia de ID de partição Service Fabric,`635650083804480486` é a representação de cadeia de o ID de réplica/instância do serviço de recursos de infraestrutura e `5008380` está a utilizar o ID de 64 bits gerado para o tempo de execução 's interno.

## <a name="list-of-performance-counters"></a>Lista de contadores de desempenho
### <a name="service-method-performance-counters"></a>Contadores de desempenho de método do serviço

O tempo de execução do serviço fiável publica os seguintes contadores de desempenho relacionados com a execução dos métodos do serviço.

| Nome da categoria | Nome do contador | Descrição |
| --- | --- | --- |
| Método do serviço de recursos de infraestrutura de serviço |Invocações por segundo |Número de vezes que o método do serviço é invocado por segundo |
| Método do serviço de recursos de infraestrutura de serviço |Média em milissegundos por invocação |Tempo decorrido para executar o método do serviço em milissegundos |
| Método do serviço de recursos de infraestrutura de serviço |Exceções emitidas/seg |Número de vezes que o método do serviço emitiu uma exceção por segundo |

### <a name="service-request-processing-performance-counters"></a>Contadores de desempenho de processamento de pedido de serviço
Quando um cliente invoca um método através de um objeto de proxy de serviço, o que resulta numa mensagem de pedido que está a ser enviada através da rede para o serviço de sistema de interação remota. O serviço processa a mensagem de pedido e envia uma resposta de volta para o cliente. O tempo de execução fiável ServiceRemoting publica os seguintes contadores de desempenho relacionados com o processamento de pedidos de serviço.

| Nome da categoria | Nome do contador | Descrição |
| --- | --- | --- |
| Serviço de recursos de infraestrutura |n. º de pedidos pendentes |Número de pedidos em processamento no serviço |
| Serviço de recursos de infraestrutura |Média em milissegundos por pedido |Tempo decorrido (em milissegundos) pelo serviço para processar um pedido |
| Serviço de recursos de infraestrutura |Média em milissegundos para a desserialização do pedido |Tempo decorrido (em milissegundos) para anular a serialização da mensagem de pedido de serviço quando recebido de serviço |
| Serviço de recursos de infraestrutura |Média em milissegundos para a serialização da resposta |Tempo decorrido (em milissegundos) antes da resposta é enviada para o cliente a serialização a mensagem de resposta do serviço do serviço |

## <a name="next-steps"></a>Passos seguintes
* [Código de exemplo](https://github.com/Azure/servicefabric-samples)
* [Fornecedores de EventSource no PerfView](https://blogs.msdn.microsoft.com/vancem/2012/07/09/introduction-tutorial-logging-etw-events-in-c-system-diagnostics-tracing-eventsource/)
