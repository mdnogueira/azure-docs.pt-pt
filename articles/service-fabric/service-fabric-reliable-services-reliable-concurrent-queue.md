---
title: "ReliableConcurrentQueue nos recursos de infraestrutura de serviço do Azure"
description: "ReliableConcurrentQueue é uma fila de débito elevado que permite enqueues paralelas e dequeues."
services: service-fabric
documentationcenter: .net
author: sangarg
manager: timlt
editor: raja,tyadam,masnider,vturecek
ms.assetid: 62857523-604b-434e-bd1c-2141ea4b00d1
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: required
ms.date: 5/1/2017
ms.author: sangarg
ms.openlocfilehash: 122cb48149477f295a65b8ee623c647b6db10a86
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="introduction-to-reliableconcurrentqueue-in-azure-service-fabric"></a>Introdução ao ReliableConcurrentQueue nos recursos de infraestrutura de serviço do Azure
Fiável fila em simultâneo é uma fila assíncrona, transacional e replicada que simultaneidade elevada de funcionalidades para colocar em fila e anular operações. Foi concebido para fornecer o débito alto e baixa latência ao simplificar a ordenação de FIFO strict fornecida pelo [fila fiável](https://msdn.microsoft.com/library/azure/dn971527.aspx) e em vez disso, fornece uma ordenação de melhor esforço.

## <a name="apis"></a>APIs

|Fila em simultâneo                |Fila simultânea fiável                                         |
|--------------------------------|------------------------------------------------------------------|
| void Enqueue(T item)           | Tarefa EnqueueAsync (tx ITransaction, item de T)                       |
| bool TryDequeue (saída de resultado de T)  | Tarefa < ConditionalValue < T >> TryDequeueAsync (ITransaction tx)  |
| Int existente                    | muito existente                                                     |

## <a name="comparison-with-reliable-queuehttpsmsdnmicrosoftcomlibraryazuredn971527aspx"></a>Comparação com [fila fiável](https://msdn.microsoft.com/library/azure/dn971527.aspx)

Fiável fila em simultâneo é oferecida como uma alternativa à [fila fiável](https://msdn.microsoft.com/library/azure/dn971527.aspx). Deve ser utilizado em casos onde o ordenação de FIFO strict não é necessária, como guaranteeing FIFO requer um compromisso com a simultaneidade.  [Fila fiável](https://msdn.microsoft.com/library/azure/dn971527.aspx) utiliza bloqueios para impor a ordenação FIFO, com um máximo de uma transação permissão para colocar em fila e no máximo uma transação permissão para anular a uma hora. Em comparação, fila simultâneas fiável relaxes a restrição de ordenação e permite que qualquer número transações concorrentes intercalação os colocar em fila e anular operações. Ordenação de melhor esforço é fornecido, no entanto a ordenação relativo de dois valores numa fila simultâneas fiável nunca pode ser garantida.

Fila de simultâneas fiável proporciona um maior débito e latência inferior do que [fila fiável](https://msdn.microsoft.com/library/azure/dn971527.aspx) sempre que existem várias transações concorrentes efetuar enqueues e/ou dequeues.

Exemplo de utilizar as maiúsculas e minúsculas para a ReliableConcurrentQueue é o [fila de mensagens](https://en.wikipedia.org/wiki/Message_queue) cenário. Neste cenário, os produtores de mensagem de um ou mais criarem e adicionar os itens para a fila e um ou mais consumidores de mensagem mensagens da fila de extração e processá-los. Vários produtores e consumidores podem trabalhar de forma independente, utilizando transações concorrentes para processar a fila.

## <a name="usage-guidelines"></a>Diretrizes de utilização
* A fila de espera que os itens na fila com um período de retenção baixa. Ou seja, os itens não seriam permanecem na fila de espera durante muito tempo.
* A fila garante a ordenação de FIFO rigorosa.
* A fila não ler as suas próprias escritas. Se um item é colocados em fila dentro de uma transação, não serão visível para um dequeuer na mesma transação.
* Dequeues não estão isoladas umas das outras. Se o item *A* é removida na transação *txnA*, mesmo que *txnA* não está consolidada, item *A* não será visível para um em simultâneo transação *txnB*.  Se *txnA* interrompe, *A* ficarão visíveis para *txnB* imediatamente.
* *TryPeekAsync* comportamento pode ser implementado utilizando um *TryDequeueAsync* e, em seguida, a abortar a transação. Um exemplo desta situação pode ser encontrado na secção de padrões de programação.
* A contagem é não transacional. Pode ser utilizado para obter uma ideia do número de elementos da fila, mas representa um ponto no tempo e não pode ser confiada.
* Não deve ser efetuado processamento dispendiosas sobre os itens dequeued enquanto a transação está ativa, para evitar transações de longa execução que podem ter um impacto no desempenho no sistema.

## <a name="code-snippets"></a>Fragmentos de código
Informe-nos observe alguns fragmentos de código e as respetivas saídas esperadas. Processamento de exceções é ignorado nesta secção.

### <a name="enqueueasync"></a>EnqueueAsync
Seguem-se alguns fragmentos de código para utilizar EnqueueAsync seguido as respetivas saídas esperadas.

- *Cenário 1: Único colocar em fila de tarefas*

```
using (var txn = this.StateManager.CreateTransaction())
{
    await this.Queue.EnqueueAsync(txn, 10, cancellationToken);
    await this.Queue.EnqueueAsync(txn, 20, cancellationToken);

    await txn.CommitAsync();
}
```

Partem do princípio de que a tarefa foi concluída com êxito e que existe foram não modificar a fila de transações em simultâneo. O utilizador pode esperar da fila para conter os itens em qualquer uma das ordens seguintes:

> 10, 20

> 20, 10


- *Caso 2: Paralela colocar em fila de tarefas*

```
// Parallel Task 1
using (var txn = this.StateManager.CreateTransaction())
{
    await this.Queue.EnqueueAsync(txn, 10, cancellationToken);
    await this.Queue.EnqueueAsync(txn, 20, cancellationToken);

    await txn.CommitAsync();
}

// Parallel Task 2
using (var txn = this.StateManager.CreateTransaction())
{
    await this.Queue.EnqueueAsync(txn, 30, cancellationToken);
    await this.Queue.EnqueueAsync(txn, 40, cancellationToken);

    await txn.CommitAsync();
}
```

Partem do princípio de que as tarefas concluídas com êxito, se as tarefas executaram em paralelo e que ocorreram não existem transações concorrentes modificar a fila. A inferência não pode ser efetuada sobre a ordem dos itens na fila. Para este fragmento de código, os itens podem aparecer em qualquer uma das 4! orderings possíveis.  A fila tentará manter os itens por ordem de original (colocados em fila), mas pode ser forçada a reordenar devido a operações simultâneas ou falhas.


### <a name="dequeueasync"></a>DequeueAsync
Seguem-se alguns fragmentos de código para utilizar TryDequeueAsync seguido as saídas esperadas. Partem do princípio de que a fila já está preenchida com os seguintes itens na fila:
> 10, 20, 30, 40, 50, 60

- *Cenário 1: Único anular tarefas*

```
using (var txn = this.StateManager.CreateTransaction())
{
    await this.Queue.TryDequeueAsync(txn, cancellationToken);
    await this.Queue.TryDequeueAsync(txn, cancellationToken);
    await this.Queue.TryDequeueAsync(txn, cancellationToken);

    await txn.CommitAsync();
}
```

Partem do princípio de que a tarefa foi concluída com êxito e que existe foram não modificar a fila de transações em simultâneo. Uma vez que a inferência não pode ser efetuada sobre a ordem dos itens na fila de espera, podem ser removida qualquer três dos itens, por qualquer ordem. A fila tentará manter os itens por ordem de original (colocados em fila), mas pode ser forçada a reordenar devido a operações simultâneas ou falhas.  

- *Caso 2: Efetuada em paralelo anular a tarefa*

```
// Parallel Task 1
List<int> dequeue1;
using (var txn = this.StateManager.CreateTransaction())
{
    dequeue1.Add(await this.Queue.TryDequeueAsync(txn, cancellationToken)).val;
    dequeue1.Add(await this.Queue.TryDequeueAsync(txn, cancellationToken)).val;

    await txn.CommitAsync();
}

// Parallel Task 2
List<int> dequeue2;
using (var txn = this.StateManager.CreateTransaction())
{
    dequeue2.Add(await this.Queue.TryDequeueAsync(txn, cancellationToken)).val;
    dequeue2.Add(await this.Queue.TryDequeueAsync(txn, cancellationToken)).val;

    await txn.CommitAsync();
}
```

Partem do princípio de que as tarefas concluídas com êxito, se as tarefas executaram em paralelo e que ocorreram não existem transações concorrentes modificar a fila. Uma vez que a inferência não pode ser efetuada sobre a ordem dos itens da fila, as listas *dequeue1* e *dequeue2* cada conterá os dois itens, por qualquer ordem.

O mesmo item será *não* aparecer em ambos. Por conseguinte, se tiver dequeue1 *10*, *30*, em seguida, teria dequeue2 *20*, *40*.

- *Cenário 3: Anular a ordenação com Abort de transação*

Abortar uma transação com em trânsito dequeues PUT que os itens de cópia no cabeçalho da fila. Não é garantida a ordem na qual os itens são colocados de novo no cabeçalho da fila. Informe-nos analisar o código seguinte:

```
using (var txn = this.StateManager.CreateTransaction())
{
    await this.Queue.TryDequeueAsync(txn, cancellationToken);
    await this.Queue.TryDequeueAsync(txn, cancellationToken);

    // Abort the transaction
    await txn.AbortAsync();
}
```
Partem do princípio de que os itens foram removidos pela seguinte ordem:
> 10, 20

Quando é abortar a transação, os itens seriam adicionados novamente para o cabeçalho da fila em qualquer uma das ordens seguintes:
> 10, 20

> 20, 10

O mesmo se aplica-se todos os casos em que a transação não foi com êxito *consolidado*.

## <a name="programming-patterns"></a>Padrões de programação
Nesta secção, informe-nos observe alguns programação padrões que poderão ser útil utilizar ReliableConcurrentQueue.

### <a name="batch-dequeues"></a>Dequeues do batch
A recomendado é de programação padrão para a tarefa de consumidor, lote respetivo dequeues em vez de efetuar um anular cada vez. O utilizador pode optar por limitar atrasos entre cada lote ou o tamanho de lote. O fragmento de código seguinte mostra este modelo de programação.  Tenha em atenção que neste exemplo, o processamento é efetuado depois da transação está consolidada, pelo que o se um índice de falhas que ocorrem durante o processamento, os itens não processados serão perdidos sem ter sido processada.  Em alternativa, o processamento pode ser feito no âmbito da transação, no entanto, isto pode ter um impacto negativo no desempenho e necessita de processamento dos itens que já foi processado.

```
int batchSize = 5;
long delayMs = 100;

while(!cancellationToken.IsCancellationRequested)
{
    // Buffer for dequeued items
    List<int> processItems = new List<int>();

    using (var txn = this.StateManager.CreateTransaction())
    {
        ConditionalValue<int> ret;

        for(int i = 0; i < batchSize; ++i)
        {
            ret = await this.Queue.TryDequeueAsync(txn, cancellationToken);

            if (ret.HasValue)
            {
                // If an item was dequeued, add to the buffer for processing
                processItems.Add(ret.Value);
            }
            else
            {
                // else break the for loop
                break;
            }
        }

        await txn.CommitAsync();
    }

    // Process the dequeues
    for (int i = 0; i < processItems.Count; ++i)
    {
        Console.WriteLine("Value : " + processItems[i]);
    }

    int delayFactor = batchSize - processItems.Count;
    await Task.Delay(TimeSpan.FromMilliseconds(delayMs * delayFactor), cancellationToken);
}
```

### <a name="best-effort-notification-based-processing"></a>Processamento de com base na notificação de melhor esforço
Padrão de programação interessante outro utiliza a API de contagem. Aqui, pode implementar melhor esforço, baseado na notificação para a fila de processamento. A contagem de fila pode ser utilizada para limitar uma colocar em fila ou uma tarefa de dequeue.  Note que do exemplo anterior, uma vez que o processamento ocorre fora da transação não processados itens podem ser perdidas se ocorrer uma falha durante o processamento.

```
int threshold = 5;
long delayMs = 1000;

while(!cancellationToken.IsCancellationRequested)
{
    while (this.Queue.Count < threshold)
    {
        cancellationToken.ThrowIfCancellationRequested();

        // If the queue does not have the threshold number of items, delay the task and check again
        await Task.Delay(TimeSpan.FromMilliseconds(delayMs), cancellationToken);
    }

    // If there are approximately threshold number of items, try and process the queue

    // Buffer for dequeued items
    List<int> processItems = new List<int>();

    using (var txn = this.StateManager.CreateTransaction())
    {
        ConditionalValue<int> ret;

        do
        {
            ret = await this.Queue.TryDequeueAsync(txn, cancellationToken);

            if (ret.HasValue)
            {
                // If an item was dequeued, add to the buffer for processing
                processItems.Add(ret.Value);
            }
        } while (processItems.Count < threshold && ret.HasValue);

        await txn.CommitAsync();
    }

    // Process the dequeues
    for (int i = 0; i < processItems.Count; ++i)
    {
        Console.WriteLine("Value : " + processItems[i]);
    }
}
```

### <a name="best-effort-drain"></a>Drenagem de melhor esforço
A drenagem da fila não poderá ser garantida devido à natureza da estrutura de dados em simultâneo.  É possível que, mesmo se não existem operações do utilizador na fila em trânsito, uma chamada específica para TryDequeueAsync pode não devolver um item que foi anteriormente colocados em fila e consolidada.  O item de colocados em fila fique *eventualmente* ficar visível para anular, no entanto sem um mecanismo de comunicação fora de banda, um consumidor de independente não é possível saber que a fila atingiu um Estado de repouso mesmo quando todos os produtores ter sido parado e novo não colocar em fila são permitidas operações. Assim, a operação de drenagem é melhor esforço conforme implementado abaixo.

O utilizador deve parar todas as outras produtor e tarefas de consumidor e para quaisquer transações em trânsito consolidar ou abort, antes de tentar drenar a fila de espera.  Se o utilizador sabe o número esperado de itens na fila, estes podem configurar uma notificação que indica que todos os itens de tem sido removidos.

```
int numItemsDequeued;
int batchSize = 5;

ConditionalValue ret;

do
{
    List<int> processItems = new List<int>();

    using (var txn = this.StateManager.CreateTransaction())
    {
        do
        {
            ret = await this.Queue.TryDequeueAsync(txn, cancellationToken);

            if(ret.HasValue)
            {
                // Buffer the dequeues
                processItems.Add(ret.Value);
            }
        } while (ret.HasValue && processItems.Count < batchSize);

        await txn.CommitAsync();
    }

    // Process the dequeues
    for (int i = 0; i < processItems.Count; ++i)
    {
        Console.WriteLine("Value : " + processItems[i]);
    }
} while (ret.HasValue);
```

### <a name="peek"></a>Pré-visualizar
ReliableConcurrentQueue não fornece o *TryPeekAsync* api. Utilizadores acedem a peek semântica utilizando um *TryDequeueAsync* e, em seguida, a abortar a transação. Neste exemplo, dequeues são processados apenas se o valor do item é superior ao *10*.

```
using (var txn = this.StateManager.CreateTransaction())
{
    ConditionalValue ret = await this.Queue.TryDequeueAsync(txn, cancellationToken);
    bool valueProcessed = false;

    if (ret.HasValue)
    {
        if (ret.Value > 10)
        {
            // Process the item
            Console.WriteLine("Value : " + ret.Value);
            valueProcessed = true;
        }
    }

    if (valueProcessed)
    {
        await txn.CommitAsync();    
    }
    else
    {
        await txn.AbortAsync();
    }
}
```

## <a name="must-read"></a>Tem de leitura
* [Início rápido Reliable Services](service-fabric-reliable-services-quick-start.md)
* [Trabalhar com as Reliable Collections](service-fabric-work-with-reliable-collections.md)
* [Notificações de serviços fiáveis](service-fabric-reliable-services-notifications.md)
* [Reliable Services de cópia de segurança e restaurar (recuperação após desastre)](service-fabric-reliable-services-backup-restore.md)
* [Configuração do Gestor de estado fiável](service-fabric-reliable-services-configuration.md)
* [Introdução aos serviços de API Web do Service Fabric](service-fabric-reliable-services-communication-webapi.md)
* [Utilização avançada de Reliable Services modelo de programação](service-fabric-reliable-services-advanced-usage.md)
* [Referência para programadores para coleções fiáveis](https://msdn.microsoft.com/library/azure/microsoft.servicefabric.data.collections.aspx)
