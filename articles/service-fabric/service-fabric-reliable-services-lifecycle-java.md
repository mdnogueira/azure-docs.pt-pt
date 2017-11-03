---
title: "Descrição geral do ciclo de vida dos Reliable Services do Azure Service Fabric | Microsoft Docs"
description: "Saiba mais sobre os eventos de ciclo de vida de diferentes nos serviços de fiáveis de Service Fabric"
services: Service-Fabric
documentationcenter: java
author: PavanKunapareddyMSFT
manager: timlt
ms.assetid: 
ms.service: Service-Fabric
ms.devlang: java
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/30/2017
ms.author: pakunapa;
ms.openlocfilehash: 80eb68346dd05c256c60725eb082aa0651fe7cbd
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="reliable-services-lifecycle-overview"></a>Descrição geral do ciclo de vida de serviços fiável
> [!div class="op_single_selector"]
> * [C# no Windows](service-fabric-reliable-services-lifecycle.md)
> * [Java em Linux](service-fabric-reliable-services-lifecycle-java.md)
>
>

Quando pensar sobre os ciclos de vida dos Reliable Services, as noções básicas do ciclo de vida são mais importantes. Em geral:

* Durante o arranque
  * Os serviços são construídos
  * Têm uma oportunidade para construir e devolver zero ou mais serviços de escuta
  * Os serviços de escuta devolvidos estão abertos, permitir a comunicação com o serviço
  * Método de runasync com o serviço é chamado, permitindo que o serviço à não execução longa ou em segundo plano de trabalho
* Durante o encerramento
  * O token de cancelamento transmitido a runasync com foi cancelado e os serviços de escuta estão fechados
  * Assim que estiver concluído, o objeto de serviço próprio é destructed

Existem detalhes à volta a ordenação exato destes eventos. Em particular, a ordem dos eventos podem ser alteradas ligeiramente dependendo se o serviço fiável é Stateless ou de monitorização de estado. Além disso, para os serviços com monitorização de estado, temos de lidar com o cenário de comutação primário. Durante esta sequência, a função de principal é transferida para outra réplica (ou retornado) sem encerramento do serviço. Por fim, temos de pensar em condições de erro ou falha.

## <a name="stateless-service-startup"></a>Arranque do serviço sem monitorização de estado
O ciclo de vida de um serviço sem monitorização de estado é bastante simples. Segue-se a ordem de eventos:

1. O serviço foi construído
2. Em seguida, em paralelas duas coisas ocorrer:
    - `StatelessService.createServiceInstanceListeners()`é invocada e quaisquer serviços de escuta devolvidos são abertos (`CommunicationListener.openAsync()` denomina-se cada serviço de escuta de)
    - Método de runasync com o serviço (`StatelessService.runAsync()`) é chamado
3. Se estiver presente, é chamado o método de onOpenAsync do serviço (especificamente, `StatelessService.onOpenAsync()` é chamado. Esta é uma substituição invulgar mas fica disponível).

É importante salientar que não existe nenhum ordenação entre as chamadas para criar e abrir os serviços de escuta e runasync com. Os serviços de escuta poderão abrir antes runasync com é iniciado. Da mesma forma, runasync com poderá acabar invocado antes dos serviços de escuta de comunicação estão abertos ou mesmo foi construídos. Se for necessária qualquer sincronização, será deixada como um exercício para o implementador. Soluções comuns:

* Por vezes, os serviços de escuta não podem funcionar até que outras informações é criado ou trabalho efectuadas. Para serviços sem monitorização de estado de trabalho, normalmente, pode ser feito no construtor do serviço, durante o `createServiceInstanceListeners()` chamar, ou como parte de construção do serviço de escuta.
* Por vezes, o código no runasync com não pretende iniciar até que os serviços de escuta estão abertos. Neste caso coordenação adicional é necessária. Uma solução comum é algumas sinalizador dentro de serviços de escuta que indicam quando se tem concluído, que é verificado runasync com antes de continuar com o trabalho real.

## <a name="stateless-service-shutdown"></a>Encerramento do serviço sem monitorização de estado
Quando encerrar um serviço sem monitorização de estado, o mesmo padrão é seguido apenas na inversa:

1. Em paralelo
    - Quaisquer serviços de escuta abertos são fechados (`CommunicationListener.closeAsync()` denomina-se cada serviço de escuta de)
    - O token de cancelamento transmitido a `runAsync()` foi cancelada (verificar o token de cancelamento `isCancelled` propriedade devolve true e se a chamada do token `throwIfCancellationRequested` método gera um `CancellationException`)
2. Uma vez `closeAsync()` conclusão em cada serviço de escuta e `runAsync()` também estiver concluída, o serviço `StatelessService.onCloseAsync()` se chama o método, se estiver presente (novamente esta é uma substituição invulgar).
3. Depois de `StatelessService.onCloseAsync()` estiver concluída, o objeto de serviço é destructed

## <a name="notes-on-service-lifecycle"></a>Notas sobre o ciclo de vida do serviço
* Tanto o `runAsync()` método e o `createServiceInstanceListeners` chamadas são opcionais. Um serviço pode ter um dos mesmos, ambos ou nenhum. Por exemplo, se o serviço não todos os respetivo trabalho em resposta a chamadas de utilizador, não é necessário para o mesmo implementar `runAsync()`. Apenas os serviços de escuta de comunicação e o respetivo código associado são necessárias. Da mesma forma, criar e devolver os serviços de escuta de comunicação é opcional, como o serviço pode ter apenas trabalho em segundo plano para o fazer e, por isso, só tem de implementar`runAsync()`
* É válido para um serviço concluir `runAsync()` com êxito e retorno do mesmo. Isto não é considerado uma condição de falha e representaria o trabalho em segundo plano da conclusão de serviço. Para serviços fiáveis com monitorização de estado `runAsync()` teria de ser chamado novamente se o serviço foram despromovido do site primário e, em seguida, promovido novamente para o site primário.
* Se um serviço sai do `runAsync()` por argumentoutofrangeexception algumas exceção inesperada, esta é uma falha e o objeto de serviço é encerrado e reportado um erro de estado de funcionamento.
* Enquanto não estiver não existe nenhum limite de tempo na devolução destes métodos, imediatamente perder a capacidade de escrever e, por conseguinte, não é possível concluir qualquer trabalho real. Recomenda-se que é devolvido como rapidamente possível após receber o pedido de cancelamento. Se o serviço não responder a estas chamadas de API num período de tempo razoável Service Fabric pode forçar a terminar o serviço. Normalmente, isto só ocorre durante as atualizações de aplicações ou quando um serviço está a ser eliminado. Este tempo limite é de 15 minutos por predefinição.
* Falhas no `onCloseAsync()` resultado de caminho no `onAbort()` chamados que é uma oportunidade de melhor esforço de última oportunidade para o serviço limpar configurar e quaisquer recursos que possam tem reclamado de versão.

> [!NOTE]
> Serviços fiáveis com monitorização de estado não são suportados ainda em java.
>
>

## <a name="next-steps"></a>Passos seguintes
* [Introdução a Reliable Services](service-fabric-reliable-services-introduction.md)
* [Início rápido de serviços fiável](service-fabric-reliable-services-quick-start.md)
* [Reliable Services avançadas de utilização](service-fabric-reliable-services-advanced-usage.md)
