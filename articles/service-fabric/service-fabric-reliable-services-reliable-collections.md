---
title: "Introdução às coleções fiável nos serviços de monitorização de estado de Service Fabric do Azure | Microsoft Docs"
description: "Serviços de monitorização de estado de Service Fabric fornecem fiáveis coleções que permitem-lhe escrever aplicações em nuvem de elevada disponibilidade, dimensionáveis e baixa latência."
services: service-fabric
documentationcenter: .net
author: mcoskun
manager: timlt
editor: masnider,rajak,zhol
ms.assetid: 62857523-604b-434e-bd1c-2141ea4b00d1
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: required
ms.date: 11/6/2017
ms.author: mcoskun
ms.openlocfilehash: 0e89df79d2ff619343f914ce3a5ffe87b7bf25de
ms.sourcegitcommit: 6a6e14fdd9388333d3ededc02b1fb2fb3f8d56e5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/07/2017
---
# <a name="introduction-to-reliable-collections-in-azure-service-fabric-stateful-services"></a>Introdução às coleções fiável nos serviços de monitorização de estado do Azure Service Fabric
Coleções fiáveis permitem-lhe escrever aplicações em nuvem de elevada disponibilidade, dimensionáveis e baixa latência, como se se tratasse foram escrever as aplicações de computador individual. As classes no **Microsoft.ServiceFabric.Data.Collections** espaço de nomes fornecem um conjunto de coleções que tornam o estado de disponibilidade elevada automaticamente. Os programadores têm de programa apenas para as APIs de coleção fiável e permitir que as coleções fiável gerir o estado replicado e local.

A principal diferença entre coleções fiável e outras tecnologias de elevada disponibilidade (tais como Redis, o serviço de tabela do Azure e o serviço de fila do Azure) é que o estado é mantido localmente a instância de serviço ao também a ser tornado de elevada. Isto significa que:

* Todas as leituras são locais, que resulta numa latência baixa e lê débito elevado.
* Todas as escritas implicar o número mínimo de rede em IOs, o que resulta em baixa latência e escreve débito elevado.

![Imagem da evolução das coleções.](media/service-fabric-reliable-services-reliable-collections/ReliableCollectionsEvolution.png)

Coleções fiáveis podem considerar como a evolução natural do **System.Collections** classes: um novo conjunto de coleções que foram concebidos para as aplicações em nuvem e multi-computador sem aumentar a complexidade para o para programadores. Como tal, são coleções fiável:

* Replicadas: Alterações de estado são replicadas para elevada disponibilidade.
* Persistentes: Os dados persistem para disco para uma durabilidade contra falhas em grande escala (por exemplo, uma centro de dados de falha de energia).
* Assíncrona: APIs são assíncronas para se certificar de que os threads não são bloqueadas quando incorrer em e/s.
* Transacional: APIs utilizam a abstração de transações para que consiga gerir facilmente várias coleções fiável dentro de um serviço.

Coleções fiáveis de fornecer a consistência forte garante que a Box para facilitar a raciocínio sobre o estado da aplicação.
A consistência forte é conseguida ao garantir que a transação consolidações concluir apenas depois de toda a transação foi registada no quórum maioria de réplicas, incluindo o principal.
Para alcançar consistência mais fraco, aplicações podem reconhecer novamente para o cliente/autor do pedido antes de consolidação assíncrona devolve.

As APIs de coleções fiável são uma evolução de colecções simultâneas APIs (localizado no **System.Collections.Concurrent** espaço de nomes):

* Assíncrona: Devolve uma tarefa, uma vez que, ao contrário das coleções em simultâneo, as operações são replicadas e persistente.
* Não parâmetros out: utiliza `ConditionalValue<T>` para devolver um booleano e um valor em vez de parâmetros de saída. `ConditionalValue<T>`é como `Nullable<T>` mas não necessita de T ser uma estrutura.
* Transações: Utiliza um objeto de transação para permitir que o utilizador para ações de grupo em várias coleções fiável numa transação.

Hoje em dia, **Microsoft.ServiceFabric.Data.Collections** contém três coleções:

* [Dicionário fiável](https://msdn.microsoft.com/library/azure/dn971511.aspx): representa uma coleção replicada, transacional e assíncrona de pares chave/valor. Semelhante ao **ConcurrentDictionary**, a chave e o valor podem ser de qualquer tipo.
* [Fila fiável](https://msdn.microsoft.com/library/azure/dn971527.aspx): representa uma replicados, transacional e assíncrona strict first in, First Out (FIFO) da fila. Semelhante ao **ConcurrentQueue**, o valor pode ser de qualquer tipo.
* [Fila simultâneas fiável](service-fabric-reliable-services-reliable-concurrent-queue.md): representa um replicados, transacional e assíncrono melhor esforço ordenação fila para o débito elevado. Semelhante para o **ConcurrentQueue**, o valor pode ser de qualquer tipo.

## <a name="next-steps"></a>Passos seguintes
* [Coleção fiável diretrizes e recomendações](service-fabric-reliable-services-reliable-collections-guidelines.md)
* [Trabalhar com as Reliable Collections](service-fabric-work-with-reliable-collections.md)
* [Transações e bloqueios](service-fabric-reliable-services-reliable-collections-transactions-locks.md)
* [Gestor de estado fiável e características de coleção](service-fabric-reliable-services-reliable-collections-internals.md)
* Gestão de dados
  * [Cópia de Segurança e Restauro](service-fabric-reliable-services-backup-restore.md)
  * [Notificações](service-fabric-reliable-services-notifications.md)
  * [Serialização das Reliable Collections](service-fabric-reliable-services-reliable-collections-serialization.md)
  * [Serialização e a atualização](service-fabric-application-upgrade-data-serialization.md)
  * [Configuração do Gestor de estado de fiável](service-fabric-reliable-services-configuration.md)
* Outros
  * [Início rápido de serviços fiável](service-fabric-reliable-services-quick-start.md)
  * [Referência para programadores para coleções fiável](https://msdn.microsoft.com/library/azure/microsoft.servicefabric.data.collections.aspx)
