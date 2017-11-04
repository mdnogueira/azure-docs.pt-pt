---
title: "Avançadas utilização dos Reliable Services | Microsoft Docs"
description: "Saiba mais sobre a utilização avançada de Service Fabric Reliable Services flexibilidade adicional nos seus serviços."
services: Service-Fabric
documentationcenter: .net
author: vturecek
manager: timlt
editor: masnider
ms.assetid: f2942871-863d-47c3-b14a-7cdad9a742c7
ms.service: Service-Fabric
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/02/2017
ms.author: vturecek
ms.openlocfilehash: 694d75807d978ece6296b945bf348f08688d3b5d
ms.sourcegitcommit: 3df3fcec9ac9e56a3f5282f6c65e5a9bc1b5ba22
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2017
---
# <a name="advanced-usage-of-the-reliable-services-programming-model"></a>Avançadas utilização dos serviços fiável de modelo de programação
Azure Service Fabric simplifica escrever e gerir serviços sem monitorização de estado e com monitorização de estado fiáveis. Este guia aborda as utilizações avançadas de Reliable Services para obter mais controlo e flexibilidade de TI sobre os serviços. Antes de ler este guia, familiarize-se com [o modelo de programação Reliable Services](service-fabric-reliable-services-introduction.md).

Serviços com monitorização de estado e sem monitorização de estado tem dois pontos de entrada principal para o código de utilizador:

* `RunAsync(C#) / runAsync(Java)`é um ponto de entrada para fins gerais para o seu código de serviço.
* `CreateServiceReplicaListeners(C#)`e `CreateServiceInstanceListeners(C#) / createServiceInstanceListeners(Java)` é para abrir os serviços de escuta de comunicação para pedidos de cliente.

Para a maioria dos serviços, estes pontos de duas entrada são suficientes. Em casos raros quando mais controlo sobre o ciclo de vida de um serviço é necessário, os eventos de ciclo de vida adicionais estão disponíveis.

## <a name="stateless-service-instance-lifecycle"></a>Ciclo de vida de instância de serviço sem monitorização de estado
Ciclo de vida de um serviço sem monitorização de estado é muito simple. Um serviço sem estado só pode ser aberto, fechado ou abortado. `RunAsync`num serviço sem monitorização de estado é executado quando uma instância de serviço é aberta e cancelada quando uma instância de serviço está fechada ou abortada.

Embora `RunAsync` deve ser suficiente na quase todos os casos, aberto, feche e eventos de abortar num serviço sem monitorização de estado também estão disponíveis:

* `Task OnOpenAsync(IStatelessServicePartition, CancellationToken) - C# / CompletableFuture<String> onOpenAsync(CancellationToken) - Java`OnOpenAsync é chamado quando a instância de serviço sem monitorização de estado está prestes a ser utilizado. Tarefas de inicialização do serviço expandida podem ser iniciadas neste momento.
* `Task OnCloseAsync(CancellationToken) - C# / CompletableFuture onCloseAsync(CancellationToken) - Java`OnCloseAsync é chamado quando a instância de serviço sem estado vai ser corretamente encerrado. Isto pode ocorrer quando o código do serviço está a ser atualizado, a instância de serviço está a ser movida devido ao balanceamento de carga ou foi detetado um erro transitório. OnCloseAsync pode ser utilizado em segurança feche quaisquer recursos, pare qualquer processamento em segundo plano, concluir guardar Estado externo ou fechar-se para baixo de ligações existentes.
* `void OnAbort() - C# / void onAbort() - Java`OnAbort é chamado quando a instância de serviço sem monitorização de estado está a ser forçadamente encerrada. Geralmente, chama-se quando é detetada uma falha permanente no nó, ou quando o Service Fabric fiável não é possível gerir o ciclo de vida da instância de serviço devido a falhas internos.

## <a name="stateful-service-replica-lifecycle"></a>Ciclo de vida de réplica de serviço com estado

> [!NOTE]
> Serviços fiáveis com monitorização de estado não são suportados ainda em Java.
>
>

Ciclo de vida de uma réplica de serviço com estado é muito mais complexo do que uma instância de serviço sem estado. Além disso abrir, fechar e abortar eventos, uma réplica de serviço com estado sofre alterações de função durante a respetiva duração. Quando uma réplica de serviço com estado muda de função, o `OnChangeRoleAsync` evento é acionado:

* `Task OnChangeRoleAsync(ReplicaRole, CancellationToken)`OnChangeRoleAsync é chamado quando a réplica de serviço com estado está a alterar função, por exemplo a primária ou secundária. Réplicas primárias recebem o estado de escrita (estão autorizados a criar e escrever coleções fiável). As réplicas secundárias são indicadas o estado de leitura (pode apenas ler a partir do coleções fiável existente). A maioria das trabalho num serviço de monitorização de estado é efetuado na réplica primária. As réplicas secundárias podem efetuar a validação de só de leitura, a geração de relatórios, extração de dados ou outras tarefas só de leitura.

Num serviço com monitorização de estado, apenas a réplica primária possui acesso de escrita para o estado e, por conseguinte, é, geralmente, quando o serviço está a efetuar trabalho real. O `RunAsync` método num serviço de monitorização de estado é executado apenas quando a réplica de serviço com estado é primária. O `RunAsync` método é cancelado quando a função da réplica primária alterações na direção oposta ao site primário, bem como durante os eventos de fecho: Commit e abort.

Utilizar o `OnChangeRoleAsync` eventos permite-lhe efetuar o trabalho, dependendo da função de réplica, bem como em resposta a alteração da função.

Um serviço com estado também fornece os mesmos eventos de ciclo de vida quatro como um serviço sem monitorização de estado, com a mesma semântica e casos de utilização:

```csharp
* Task OnOpenAsync(IStatefulServicePartition, CancellationToken)
* Task OnCloseAsync(CancellationToken)
* void OnAbort()
```

## <a name="next-steps"></a>Passos seguintes
Para mais avançados Tópicos relacionados com o Service Fabric, consulte os artigos seguintes:

* [Configurar Reliable Services com monitorização de estado](service-fabric-reliable-services-configuration.md)
* [Introdução de estado de funcionamento do Service Fabric](service-fabric-health-introduction.md)
* [Utilizar relatórios de estado de funcionamento do sistema para resolução de problemas](service-fabric-understand-and-troubleshoot-with-system-health-reports.md)
* [Configurar os serviços com o serviço de recursos de infraestrutura Cluster Gestor de recursos](service-fabric-cluster-resource-manager-configure-services.md)
