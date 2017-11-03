---
title: "Coleções fiável de recursos de infraestrutura de serviços de transações e modos de bloqueio no Azure | Microsoft Docs"
description: "Gestor de estado do serviço do Azure recursos de infraestrutura fiável e transações coleções fiável e bloqueio."
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
ms.date: 5/1/2017
ms.author: mcoskun
ms.openlocfilehash: 3452473f5b2f86d29e46339c997193bc6403736a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="transactions-and-lock-modes-in-azure-service-fabric-reliable-collections"></a>Transações e modos de bloqueio em coleções fiável do Azure Service Fabric

## <a name="transaction"></a>Transação
Uma transação é uma sequência de operações efetuadas como uma unidade lógica única de trabalho.
Uma transação tem apresentem um as seguintes propriedades ACID. (consulte o artigo: https://technet.microsoft.com/en-us/library/ms190612)
* **Atomicity**: uma transação tem de ser uma unidade atómica do trabalho. Por outras palavras, a todos os respetivos modificações de dados são executadas ou nenhuma delas é executada.
* **Consistência**: Quando tiver terminado, uma transação tem de deixar todos os dados num estado consistente. Todas as estruturas de dados internos tem de ser corretas no final da transação.
* **Isolamento**: modificações efetuadas por transações concorrentes tem de ser isoladas das modificações efetuadas por quaisquer outras transações em simultâneo. O nível de isolamento utilizado para uma operação dentro de um ITransaction é determinado pelo IReliableState efetuar a operação.
* **Características de durabilidade**: depois de uma transação foi concluída, os respetivos efeitos estão permanentemente implementados no sistema. As modificações são mantidas, mesmo em caso de falha de sistema.

### <a name="isolation-levels"></a>Níveis de isolamento
Nível de isolamento define o grau ao qual a transação tem de ser isolada dos modificações efetuadas por outras transações.
Existem dois níveis de isolamento que são suportados em coleções fiável:

* **Leitura repetíveis**: Especifica que as instruções não é possível ler os dados que tenham sido modificados, mas ainda não consolidados por outras transações e que não existem outras transações podem modificar dados que foram lidos pela transação atual até que a transação atual Depois de concluída. Para obter mais detalhes, consulte [https://msdn.microsoft.com/library/ms173763.aspx](https://msdn.microsoft.com/library/ms173763.aspx).
* **Instantâneo**: Especifica que os dados lidos pelo qualquer declaração numa transação são a versão de uma forma consistente dos dados que existiam no início da transação.
  A transação pode reconhecer apenas as modificações de dados que foram committed antes do início da transação.
  Modificações de dados efetuadas por outras transações após o início da transação atual não estão visíveis para instruções de execução na transação atual.
  O efeito é como se as declarações numa transação obtêm um instantâneo dos dados consolidados, que existia no início da transação.
  Os instantâneos são consistentes em coleções fiável.
  Para obter mais detalhes, consulte [https://msdn.microsoft.com/library/ms173763.aspx](https://msdn.microsoft.com/library/ms173763.aspx).

Coleções fiáveis automaticamente escolha o nível de isolamento a utilizar para uma operação de leitura indicada, dependendo da operação e a função da réplica no momento da criação da transação.
Segue-se a tabela que mostra as predefinições de nível de isolamento para operações de dicionário fiável e fila.

| Operação \ função | Primária | Secundária |
| --- |:--- |:--- |
| Leitura de entidade única |Leitura repetida |Instantâneo |
| Enumeração, contagem |Instantâneo |Instantâneo |

> [!NOTE]
> Os exemplos comuns para operações de entidade única `IReliableDictionary.TryGetValueAsync`, `IReliableQueue.TryPeekAsync`.
> 

O dicionário fiável e a fila fiável suportam leitura Your escreve.
Por outras palavras, qualquer escrita dentro de uma transação serão visível para uma leitura seguinte que pertence à mesma transação.

## <a name="locks"></a>Bloqueios
Coleções fiável, implemente de todas as transações rigorosas dois fase bloqueio: uma transação não libertar bloqueios obteve até que a transação termina com um aborto ou de confirmação.

Dicionário fiável utiliza o nível de linha bloqueio para todas as operações de entidade única.
Fila fiável trades desativar simultaneidade strict propriedade FIFO transacional.
Fila fiável utiliza bloqueios de nível de operação, permitindo uma transação com `TryPeekAsync` e/ou `TryDequeueAsync` e uma transação com `EnqueueAsync` cada vez.
Tenha em atenção que para preservar FIFO, se um `TryPeekAsync` ou `TryDequeueAsync` situa alguma vez que a fila fiável está vazia, também irão bloquear `EnqueueAsync`.

Escreva operações tomar bloqueios exclusivos.
Para operações de leitura, o bloqueio depende de alguns fatores.
Qualquer operação de leitura feita utilizando o isolamento do instantâneo é livre de bloqueio.
Todas as operações de leitura repetida por predefinição demora bloqueios partilhados.
No entanto, para qualquer operação de leitura que suporta a leitura repetíveis, o utilizador pode pedir um bloqueio de atualização em vez do bloqueio partilhado.
Um bloqueio de atualização é um bloqueio assimétrico utilizado para impedir que uma forma comum de impasse que ocorre quando várias transações bloquear recursos para as atualizações de potenciais numa altura posterior.

A matriz de compatibilidade de bloqueio pode ser encontrada na tabela seguinte:

| Pedir \ concedido | Nenhuma | Partilhado | Atualizar | Exclusivo |
| --- |:--- |:--- |:--- |:--- |
| Partilhado |Nenhum conflito |Nenhum conflito |Conflito |Conflito |
| Atualizar |Nenhum conflito |Nenhum conflito |Conflito |Conflito |
| Exclusivo |Nenhum conflito |Conflito |Conflito |Conflito |

Argumento de limite de tempo nas APIs coleções fiável é utilizado para a deteção de impasse.
Por exemplo, dois transações (T1 e T2) estão a tentar ler e atualizar K1.
É possível que sejam criar um impasse, porque podem ambos acaba por ficar com o bloqueio partilhado.
Neste caso, uma ou ambas as operações irão tempo limite.

Este cenário de impasse é um excelente exemplo de como a impasses pode impedir que um bloqueio de atualização.

## <a name="next-steps"></a>Passos seguintes
* [Trabalhar com as Reliable Collections](service-fabric-work-with-reliable-collections.md)
* [Notificações de serviços fiáveis](service-fabric-reliable-services-notifications.md)
* [Reliable Services de cópia de segurança e restaurar (recuperação após desastre)](service-fabric-reliable-services-backup-restore.md)
* [Configuração do Gestor de estado de fiável](service-fabric-reliable-services-configuration.md)
* [Referência para programadores para coleções fiável](https://msdn.microsoft.com/library/azure/microsoft.servicefabric.data.collections.aspx)

