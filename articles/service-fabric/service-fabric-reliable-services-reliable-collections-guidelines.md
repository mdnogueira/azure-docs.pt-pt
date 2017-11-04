---
title: "Diretrizes e recomendações para coleções fiáveis no Azure Service Fabric | Microsoft Docs"
description: "Diretrizes e recomendações para utilizar o serviço de recursos de infraestrutura fiável coleções"
services: service-fabric
documentationcenter: .net
author: mcoskun
manager: timlt
editor: masnider,rajak
ms.assetid: 62857523-604b-434e-bd1c-2141ea4b00d1
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: required
ms.date: 5/3/2017
ms.author: mcoskun
ms.openlocfilehash: 053a7bca76362035e428fc11806b3e4f83d00946
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="guidelines-and-recommendations-for-reliable-collections-in-azure-service-fabric"></a>Diretrizes e recomendações para coleções fiável no Azure Service Fabric
Esta secção fornece orientações para utilizar o Gestor de estado fiável e fiável de coleções. O objetivo é ajudar os utilizadores a evitar pitfalls comuns.

As diretrizes são organizadas como recomendações simples, o prefixo com os termos *fazer*, *considere*, *Evite* e *não*.

* Não modifique um objeto do tipo personalizado devolvido por operações de leitura (por exemplo, `TryPeekAsync` ou `TryGetValueAsync`). Coleções fiáveis, tal como coleções em simultâneo, é devolvida uma referência para os objetos e não uma cópia.
* Fazer cópia profunda o objeto devolvido do tipo personalizado antes de modificá-lo. Uma vez que estruturas e tipos incorporados passagem por valor, não precisa de fazer uma cópia profunda nos mesmos.
* Não utilize `TimeSpan.MaxValue` para tempos limite. Tempos limite deve ser utilizada para detetar a impasses.
* Não utilize uma transação depois foi consolidada, abortado ou eliminado.
* Não utilize uma enumeração fora do âmbito de transação que foi criado numa.
* Não criar uma transação dentro de outra transação `using` instrução porque pode causar impasses.
* Certifique-se de que o `IComparable<TKey>` implementação está correta. O sistema demora dependência `IComparable<TKey>` para pontos de verificação e linhas de intercalação.
* Utilize o bloqueio de atualização durante a leitura de um item com uma intenção atualizá-la para impedir que uma determinada classe de impasses.
* Considere a manter os itens (por exemplo, TKey + TValue para fiável dicionário) de mensagens em fila abaixo 80 KBytes: mais pequeno melhores. Isto reduz a quantidade de requisitos de e/s de utilização, bem como o disco e rede de pilha de objetos grandes. Muitas vezes, reduz a replicar dados duplicados, quando está a ser atualizada apenas uma pequena parte do valor. É comum forma de alcançar isto no dicionário fiável, entrar as linhas várias linhas.
* Considere a utilização da cópia de segurança e restaurar a funcionalidade para que a recuperação após desastre.
* Evitar a mistura de operações de entidade única e operações de multientidade (por exemplo `GetCountAsync`, `CreateEnumerableAsync`) na mesma transação devido aos níveis de isolamento diferentes.
* Processe uma InvalidOperationException. Transações de utilizador podem ser abortadas pelo sistema por diversas razões. Por exemplo, quando o Gestor de estado fiável está a alterar o respetiva função de site primário ou quando uma transação de longa execução está a bloquear truncagem do registo transacional. Nestes casos, o utilizador pode receber InvalidOperationException que indica que os respetivos transação já foi terminada. Pressupondo que, a terminação da transação não foi pedida pelo utilizador, é melhor forma de lidar com esta exceção para eliminar a transação, verifique se o token de cancelamento foi assinalado (ou a função da réplica ter sido alterada) e, se não criar um novo a transação e tente novamente.  

Seguem-se alguns aspetos a ter em mente:

* O tempo limite predefinido é de quatro segundos para todos os APIs de coleção fiável. A maioria dos utilizadores devem utilizar o tempo limite predefinido.
* O token de cancelamento predefinida é `CancellationToken.None` em todos os APIs de coleções fiável.
* O parâmetro de tipo de chave (*TKey*) para um dicionário fiável tem de implementar corretamente `GetHashCode()` e `Equals()`. As chaves devem ser imutáveis.
* Para alcançar a elevada disponibilidade para as coleções fiável, cada serviço deve ter, pelo menos, um destino e de réplica mínimo, defina o tamanho de 3.
* As operações de leitura no secundário podem ler versões não consolidada de quórum.
  Isto significa que uma versão de dados que se leia uma secundária única pode ser progredida false.
  Leituras de primário encontram-se sempre estáveis: pode nunca ser false progredido.

### <a name="next-steps"></a>Passos seguintes
* [Trabalhar com as Reliable Collections](service-fabric-work-with-reliable-collections.md)
* [Transações e bloqueios](service-fabric-reliable-services-reliable-collections-transactions-locks.md)
* [Gestor de estado fiável e características de coleção](service-fabric-reliable-services-reliable-collections-internals.md)
* Gestão de dados
  * [Cópia de Segurança e Restauro](service-fabric-reliable-services-backup-restore.md)
  * [Notificações](service-fabric-reliable-services-notifications.md)
  * [Serialização e a atualização](service-fabric-application-upgrade-data-serialization.md)
  * [Configuração do Gestor de estado de fiável](service-fabric-reliable-services-configuration.md)
* Outros
  * [Início rápido de serviços fiável](service-fabric-reliable-services-quick-start.md)
  * [Referência para programadores para coleções fiável](https://msdn.microsoft.com/library/azure/microsoft.servicefabric.data.collections.aspx)
