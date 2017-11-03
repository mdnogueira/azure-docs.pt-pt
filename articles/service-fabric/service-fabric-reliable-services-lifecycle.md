---
title: "Descrição geral do ciclo de vida dos Reliable Services do Azure Service Fabric | Microsoft Docs"
description: "Saiba mais sobre os eventos de ciclo de vida de diferentes no serviço de recursos de infraestrutura Reliable Services"
services: Service-Fabric
documentationcenter: .net
author: masnider
manager: timlt
editor: vturecek;
ms.assetid: 
ms.service: Service-Fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: a0a4558da0b308799a153b300b098891e933712b
ms.sourcegitcommit: 5735491874429ba19607f5f81cd4823e4d8c8206
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/16/2017
---
# <a name="reliable-services-lifecycle-overview"></a>Descrição geral de ciclo de vida do Reliable Services
> [!div class="op_single_selector"]
> * [C# no Windows](service-fabric-reliable-services-lifecycle.md)
> * [Java em Linux](service-fabric-reliable-services-lifecycle-java.md)
>
>

Quando estiver a pensar sobre os ciclos de vida dos Reliable Services do Azure Service Fabric, as noções básicas do ciclo de vida são mais importantes. Em geral, o ciclo de vida inclui o seguinte:

- Durante o arranque:
  - Os serviços são construídos.
  - Os serviços têm uma oportunidade para construir e devolver zero ou mais serviços de escuta.
  - Os serviços de escuta devolvidos abertos, permitir a comunicação com o serviço.
  - O serviço **runasync com** método é denominado, permitindo que o serviço efetue tarefas de longa execução ou de trabalho em segundo plano.
- Durante o encerramento:
  - O token de cancelamento transmitido a **runasync com** , serão canceladas, e os serviços de escuta estão fechados.
  - Depois de fechar os serviços de escuta, o objeto de serviço próprio é destructed.

Existem detalhes à volta a ordenação exato destes eventos. A ordem de eventos pode alterar ligeiramente dependendo se o serviço fiável é sem monitorização de estado ou com monitorização de estado. Além disso, para os serviços com monitorização de estado, iremos deve lidar com o cenário de comutação primário. Durante esta sequência, a função de principal é transferida para outra réplica (ou retornado) sem encerramento do serviço. Por fim, vamos tem de pensar sobre as condições de erro ou falha.

## <a name="stateless-service-startup"></a>Arranque do serviço sem monitorização de estado
O ciclo de vida de um serviço sem monitorização de estado é simples. Segue-se a ordem de eventos:

1. O serviço é construído.
2. Em seguida, em paralelo, ocorrem duas coisas:
    - `StatelessService.CreateServiceInstanceListeners()`é invocada e qualquer devolvido os serviços de escuta estão abertos. `ICommunicationListener.OpenAsync()`é chamado para cada serviço de escuta.
    - O serviço `StatelessService.RunAsync()` método é chamado.
3. Se estiver presente, o serviço `StatelessService.OnOpenAsync()` método é chamado. Esta chamada é uma substituição invulgar, mas está disponível.

Tenha em atenção que não existe nenhum ordenação entre as chamadas para criar e abrir os serviços de escuta e **runasync com**. Podem abrir os serviços de escuta antes **runasync com** é iniciado. Da mesma forma, pode invocar **runasync com** antes dos serviços de escuta de comunicação aberta ou mesmo construído. Se for necessária qualquer sincronização, será deixada como um exercício para o implementador. Seguem-se algumas soluções comuns:

  - Por vezes, os serviços de escuta não podem funcionar até que outras informações são criadas ou trabalho está concluído. Para os serviços sem monitorização de estado, normalmente, pode ser feito trabalho noutras localizações, por exemplo, o seguinte: 
    - No construtor do serviço.
    - Durante a `CreateServiceInstanceListeners()` chamada.
    - Como parte de construção do serviço de escuta.
  - Por vezes, o código no **runasync com** não comece até que os serviços de escuta estão abertos. Neste caso, coordenação adicional é necessária. Uma solução comum é que haja um sinalizador dentro de serviços de escuta que indica quando tiver terminado. Este sinalizador estiver marcada, em seguida, na **runasync com** antes de continuar com o trabalho real.

## <a name="stateless-service-shutdown"></a>Encerramento do serviço sem monitorização de estado
Para encerrar um serviço sem monitorização de estado, o mesmo padrão é seguido, apenas na inversa:

1. Em paralelo:
    - Quaisquer serviços de escuta abertos são fechados. `ICommunicationListener.CloseAsync()`é chamado para cada serviço de escuta.
    - O token de cancelamento transmitido a `RunAsync()` foi cancelada. Uma verificação do token de cancelamento `IsCancellationRequested` propriedade devolve true e se a chamada, o token `ThrowIfCancellationRequested` método gera um `OperationCanceledException`.
2. Depois de `CloseAsync()` terminar em cada serviço de escuta e `RunAsync()` também estiver concluído, o serviço `StatelessService.OnCloseAsync()` se chama o método, se estiver presente. É invulgar para substituir `StatelessService.OnCloseAsync()`.
3. Depois de `StatelessService.OnCloseAsync()` é destructed estiver concluída, o objeto de serviço.

## <a name="stateful-service-startup"></a>Arranque do serviço com estado
Os serviços com monitorização de estado têm um padrão semelhante aos serviços sem monitorização de estado, com algumas alterações. Para iniciar um serviço com estado, a ordem de eventos é o seguinte:

1. O serviço é construído.
2. `StatefulServiceBase.OnOpenAsync()`é chamado. Esta chamada não é normalmente substituída no serviço.
3. As seguintes ações ocorrem em paralelo:
    - `StatefulServiceBase.CreateServiceReplicaListeners()`é invocado. 
      - Se o serviço é um serviço principal, todos os serviços de escuta devolvidos estão abertos. `ICommunicationListener.OpenAsync()`é chamado para cada serviço de escuta.
      - Se o serviço é um serviço secundário, apenas esses serviços de escuta marcada como `ListenOnSecondary = true` estão abertas. É menos comum ter os serviços de escuta que são abertos em bases de dados secundárias.
    - Se o serviço está atualmente um site primário, o serviço `StatefulServiceBase.RunAsync()` método é chamado.
4. Depois do todos os o serviço de escuta réplica `OpenAsync()` chama concluir e `RunAsync()` denomina-se, `StatefulServiceBase.OnChangeRoleAsync()` é chamado. Esta chamada não é normalmente substituída no serviço.

Semelhante aos serviços sem monitorização de estado, não há nenhum coordenação entre a ordem em que são criados e abrir os serviços de escuta e quando **runasync com** é chamado. Se precisar de coordenação, as soluções são muito os mesmos. Não há um cenário adicional para o serviço de monitorização de estado. Imaginemos que as chamadas que chegam a escuta de comunicação necessitam de informações mantidas dentro de alguns [coleções fiável](service-fabric-reliable-services-reliable-collections.md). Porque os serviços de escuta de comunicação foi aberta antes das coleções fiáveis são legíveis ou gravável e antes de **runasync com** foi possível iniciar alguns coordenação adicional é necessária. A solução mais simples e mais comuns é para os serviços de escuta de comunicação devolver um código de erro que o cliente utiliza saber para repetir o pedido.

## <a name="stateful-service-shutdown"></a>Encerramento do serviço com monitorização de estado
Como os serviços sem monitorização de estado, os eventos de ciclo de vida durante o encerramento são os mesmos que durante o arranque, mas invertido. Quando um serviço com monitorização de estado está a ser encerrado, ocorrem os seguintes eventos:

1. Em paralelo:
    - Quaisquer serviços de escuta abertos são fechados. `ICommunicationListener.CloseAsync()`é chamado para cada serviço de escuta.
    - O token de cancelamento transmitido a `RunAsync()` foi cancelada. Uma verificação do token de cancelamento `IsCancellationRequested` propriedade devolve true e se a chamada, o token `ThrowIfCancellationRequested` método gera um `OperationCanceledException`.
2. Depois de `CloseAsync()` terminar em cada serviço de escuta e `RunAsync()` também estiver concluído, o serviço `StatefulServiceBase.OnChangeRoleAsync()` é chamado. Esta chamada não é normalmente substituída no serviço.

   > [!NOTE]  
   > A necessidade de aguardar **runasync com** concluir a apenas é necessário se esta réplica é uma réplica primária.

3. Depois do `StatefulServiceBase.OnChangeRoleAsync()` método depois de concluída, o `StatefulServiceBase.OnCloseAsync()` método é chamado. Esta chamada é uma substituição invulgar, mas está disponível.
3. Depois de `StatefulServiceBase.OnCloseAsync()` é destructed estiver concluída, o objeto de serviço.

## <a name="stateful-service-primary-swaps"></a>Trocas de principal de serviço com estado
Enquanto estiver a executar um serviço com estado, as réplicas primárias que serviços com monitorização de estado de tem os respetivos serviços de escuta de comunicação abertos e as respetivas **runasync com** método chamado. Réplicas secundárias são construídas, mas não existem outras chamadas de ver. Enquanto um serviço com estado estiver em execução, a réplica que está atualmente principal pode alterar. O que significa em termos dos eventos de ciclo de vida que pode ver uma réplica? O comportamento que vê a réplica de monitorização de estado depende se está a réplica que está a ser despromovida ou promovida durante a troca.

### <a name="for-the-primary-thats-demoted"></a>Para o site primário que está a ser despromovido
Para a réplica primária, que está a ser despromovida, o Service Fabric tem esta réplica para parar o processamento das mensagens e sair de qualquer trabalho em segundo plano que esteja a executar. Consequentemente, este passo parece que necessitaria se o serviço está a ser encerrado. Uma diferença é que o serviço não se encontra destructed ou fechado porque continua a ser como uma secundária. As APIs seguintes são denominadas:

1. Em paralelo:
    - Quaisquer serviços de escuta abertos são fechados. `ICommunicationListener.CloseAsync()`é chamado para cada serviço de escuta.
    - O token de cancelamento transmitido a `RunAsync()` foi cancelada. Uma verificação do token de cancelamento `IsCancellationRequested` propriedade devolve true e se a chamada, o token `ThrowIfCancellationRequested` método gera um `OperationCanceledException`.
2. Depois de `CloseAsync()` terminar em cada serviço de escuta e `RunAsync()` também estiver concluído, o serviço `StatefulServiceBase.OnChangeRoleAsync()` é chamado. Esta chamada não é normalmente substituída no serviço.

### <a name="for-the-secondary-thats-promoted"></a>Para o secundário que é promovido
Da mesma forma, o Service Fabric tem a réplica secundária, que é promovida para começar a escutar mensagens na rede e iniciar quaisquer tarefas em segundo plano, tem de concluir. Consequentemente, este processo parece que necessitaria se o serviço é criado, exceto que a réplica próprio já existe. As APIs seguintes são denominadas:

1. Em paralelo:
    - `StatefulServiceBase.CreateServiceReplicaListeners()`é invocada e qualquer devolvido os serviços de escuta estão abertos. `ICommunicationListener.OpenAsync()`é chamado para cada serviço de escuta.
    - O serviço `StatefulServiceBase.RunAsync()` método é chamado.
2. Depois do todos os o serviço de escuta réplica `OpenAsync()` chama concluir e `RunAsync()` denomina-se, `StatefulServiceBase.OnChangeRoleAsync()` é chamado. Esta chamada não é normalmente substituída no serviço.

### <a name="common-issues-during-stateful-service-shutdown-and-primary-demotion"></a>Problemas comuns durante o encerramento do serviço com monitorização de estado e despromoção primária
Service Fabric altera o principal de um serviço com monitorização de estado para diversos motivos. As mais comuns são [cluster reequilíbrio](service-fabric-cluster-resource-manager-balancing.md) e [atualização da aplicação](service-fabric-application-upgrade.md). Durante estas operações (bem como durante o encerramento do serviço normal, como o que poderá ver se o serviço foi eliminado), é importante que o serviço Respeitamos a `CancellationToken`. 

Serviços que não processar corretamente cancelamento podem ter problemas de vários. Estas operações são lentas porque Service Fabric aguarda que os serviços para parar corretamente. Em última análise Isto pode levar a falha de atualizações que o limite de tempo e reverter. Falha que respeite o token de cancelamento também pode provocar imbalanced clusters. Clusters ficam desequilibrados porque nós obter frequente, mas os serviços não podem ser reequilibrados porque demora demasiado tempo a movê-los noutro local. 

Porque os serviços estão com monitorização de estado, também é provável que utilizam o [coleções fiável](service-fabric-reliable-services-reliable-collections.md). No Service Fabric, quando um site primário é despromovido, um dos primeiros aspetos que ocorre é que o acesso de escrita para o estado subjacente foi revogado. Isto leva a um segundo conjunto de problemas que podem afetar o ciclo de vida do serviço. Coleções exceções retorno baseadas no período de tempo e se a réplica está a ser movida ou encerramento. Estas exceções devem ser processadas corretamente. Exceções acionadas por Service Fabric enquadram permanente [(`FabricException`)](https://docs.microsoft.com/en-us/dotnet/api/system.fabric.fabricexception?view=azure-dotnet) e transitório [(`FabricTransientException`)](https://docs.microsoft.com/en-us/dotnet/api/system.fabric.fabrictransientexception?view=azure-dotnet) categorias. Permanentes exceções devem ser registadas e emitidas enquanto as exceções transitórias podem ser repetidas com base em algumas lógica de repetição.

Processamento de exceções que vêm da utilização do `ReliableCollections` em conjunto com eventos de ciclo de vida do serviço é uma parte importante de teste e a validar um serviço fiável. Recomendamos que são sempre executadas o serviço sob carga ao efetuar atualizações e [chaos testar](service-fabric-controlled-chaos.md) antes de implementar para produção. Os passos básicos ajudam a garantir que o seu serviço é corretamente implementado e processa eventos de ciclo de vida corretamente.


## <a name="notes-on-the-service-lifecycle"></a>Notas sobre o ciclo de vida do serviço
  - Tanto o `RunAsync()` método e o `CreateServiceReplicaListeners/CreateServiceInstanceListeners` chamadas são opcionais. Um serviço pode ter um dos mesmos, ambos ou nenhum. Por exemplo, se o serviço não todos os respetivo trabalho em resposta a chamadas de utilizador, não é necessário para o mesmo implementar `RunAsync()`. Apenas os serviços de escuta de comunicação e o respetivo código associado são necessárias. Da mesma forma, criação e devolver os serviços de escuta de comunicação são opcional, porque o serviço pode ter apenas trabalho em segundo plano para o fazer e, por isso, só tem de implementar `RunAsync()`.
  - É válido para um serviço concluir `RunAsync()` com êxito e retorno do mesmo. A conclusão não é uma condição de falha. A concluir `RunAsync()` indica que o trabalho em segundo plano do serviço foi concluída. Para os serviços fiáveis com monitorização de estado, `RunAsync()` denomina-se novamente se a réplica ser despromovida de principal para secundário e, em seguida, promovida novamente para o site primário.
  - Se um serviço sai do `RunAsync()` por argumentoutofrangeexception algumas exceção inesperada, isto constitui uma falha. O objeto de serviço é encerrado e é reportado um erro de estado de funcionamento.
  - Apesar de não existe nenhum limite de tempo na devolução destes métodos, imediatamente perder a capacidade de escrever em coleções fiável e, por conseguinte, não é possível concluir qualquer trabalho real. Recomendamos que pode ser devolvido como rapidamente possível após receber o pedido de cancelamento. Se o serviço não responder a estas chamadas de API num período de tempo razoável, o Service Fabric pode forçar a terminar o serviço. Normalmente, isto só ocorre durante as atualizações de aplicações ou quando um serviço está a ser eliminado. Este tempo limite é de 15 minutos por predefinição.
  - Falhas no `OnCloseAsync()` resultado de caminho no `OnAbort()` a ser chamado, que é uma oportunidade de melhor esforço de última oportunidade limpar e quaisquer recursos que possam tem reclamado da versão do serviço.

## <a name="next-steps"></a>Passos seguintes
- [Introdução a Reliable Services](service-fabric-reliable-services-introduction.md)
- [Início rápido de serviços fiável](service-fabric-reliable-services-quick-start.md)
- [Reliable Services avançadas de utilização](service-fabric-reliable-services-advanced-usage.md)
- [Réplicas e instâncias](service-fabric-concepts-replica-lifecycle.md)
