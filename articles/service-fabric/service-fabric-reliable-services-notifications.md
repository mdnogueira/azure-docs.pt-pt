---
title: "Notificações de serviços fiáveis | Microsoft Docs"
description: "Documentação conceptual para notificações de Service Fabric Reliable Services"
services: service-fabric
documentationcenter: .net
author: mcoskun
manager: timlt
editor: masnider,vturecek
ms.assetid: cdc918dd-5e81-49c8-a03d-7ddcd12a9a76
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 6/29/2017
ms.author: mcoskun
ms.openlocfilehash: c6a53d851510ed5e6eec1f3ac0f636ad034a6d4c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="reliable-services-notifications"></a>Notificações de serviços fiáveis
Notificações de permitir que os clientes controlar as alterações que estão a ser efetuadas a um objeto que o se estiverem interessados em. Notificações de suportam dois tipos de objetos: *fiável Gestor de estado* e *dicionário fiável*.

Os motivos mais comuns para utilizar as notificações são:

* Edifício materializada vistas, tais como índices secundários ou agregado vistas filtradas de estado a réplica. Um exemplo é um índice ordenado de todas as chaves no dicionário fiável.
* Enviar dados de monitorização, como o número de utilizadores adicionados na última hora.

As notificações são desencadeadas como parte da aplicação de operações. Devido a que, notificações devem ser processadas como rápido eventos possíveis e síncronos não devem incluir quaisquer operações dispendiosas.

## <a name="reliable-state-manager-notifications"></a>Notificações do Gestor de estado de fiável
O Gestor de estado de fiável fornece notificações para os seguintes eventos:

* Transação
  * Consolidação
* Gestor de estado
  * Reconstrução
  * Adição de um Estado fiável
  * Remoção de um Estado de fiável

Gestor de estado de fiável controla as transações em utilização atual. A única alteração no estado de transação que faz com que uma notificação sejam acionados é uma transação ser consolidada.

Gestor de estado de fiável mantém uma coleção de Estados fiáveis como dicionário fiável e fiável de fila. Gestor de estado de fiável desencadeado notificações quando as alterações esta coleção: um Estado fiável é adicionado ou removido ou o conjunto completo é reconstruído.
A coleção do Gestor de estado fiável for reconstruída em três casos:

* Recuperação: Quando uma réplica é iniciado,-recupera o estado anterior do disco. No final da recuperação, utiliza **NotifyStateManagerChangedEventArgs** para accionar um evento que contém o conjunto de Estados fiáveis recuperados.
* Total de cópia: antes de uma réplica pode associar o conjunto de configuração, tem de ser criada. Por vezes, isto requer uma cópia completa de estado de fiável do Gestor de estado da réplica primária para ser aplicadas à réplica secundária inativa. Utiliza o Gestor de estado de fiável na réplica secundária **NotifyStateManagerChangedEventArgs** para accionar um evento que contém o conjunto de Estados fiáveis que que adquiriu da réplica primária.
* Restauro: Em cenários de recuperação após desastre, estado da réplica pode ser restaurado a partir de uma cópia de segurança através de **RestoreAsync**. Nestes casos, o Gestor de estado fiável na réplica primária utiliza **NotifyStateManagerChangedEventArgs** para accionar um evento que contém o conjunto de Estados fiáveis que restaurada a partir de cópia de segurança.

Para se registar para notificações de transação e/ou notificações do Gestor de estado, terá de registar o **TransactionChanged** ou **StateManagerChanged** eventos no Gestor de estado fiável. Um local comum para registar com estes processadores de eventos é o construtor do seu serviço de monitorização de estado. Ao registar no construtor, não irá perder as notificações que é causada por uma alteração ao longo da duração de **IReliableStateManager**.

```C#
public MyService(StatefulServiceContext context)
    : base(MyService.EndpointName, context, CreateReliableStateManager(context))
{
    this.StateManager.TransactionChanged += this.OnTransactionChangedHandler;
    this.StateManager.StateManagerChanged += this.OnStateManagerChangedHandler;
}
```

O **TransactionChanged** utiliza o processador de eventos **NotifyTransactionChangedEventArgs** para fornecer os detalhes sobre o evento. Contém a propriedade de ação (por exemplo, **NotifyTransactionChangedAction.Commit**) que especifica o tipo de alteração. Também contém a propriedade de transação que fornece uma referência para a transação que foi alterado.

> [!NOTE]
> Hoje em dia, **TransactionChanged** eventos são gerados apenas se a transação foi consolidada. A ação, em seguida, é igual a **NotifyTransactionChangedAction.Commit**. Mas, no futuro, os eventos poderão ser gerados para outros tipos de transação de alterações de estado. Recomendamos que a ação de verificação e processar o evento apenas se for um que espera.
> 
> 

Segue-se um exemplo **TransactionChanged** processador de eventos.

```C#
private void OnTransactionChangedHandler(object sender, NotifyTransactionChangedEventArgs e)
{
    if (e.Action == NotifyTransactionChangedAction.Commit)
    {
        this.lastCommitLsn = e.Transaction.CommitSequenceNumber;
        this.lastTransactionId = e.Transaction.TransactionId;

        this.lastCommittedTransactionList.Add(e.Transaction.TransactionId);
    }
}
```

O **StateManagerChanged** utiliza o processador de eventos **NotifyStateManagerChangedEventArgs** para fornecer os detalhes sobre o evento.
**NotifyStateManagerChangedEventArgs** tem dois subclasses: **NotifyStateManagerRebuildEventArgs** e **NotifyStateManagerSingleEntityChangedEventArgs**.
Utilize a propriedade de ação na **NotifyStateManagerChangedEventArgs** converter **NotifyStateManagerChangedEventArgs** para a subclasse correta:

* **NotifyStateManagerChangedAction.Rebuild**: **NotifyStateManagerRebuildEventArgs**
* **NotifyStateManagerChangedAction.Add** e **NotifyStateManagerChangedAction.Remove**: **NotifyStateManagerSingleEntityChangedEventArgs**

Segue-se um exemplo **StateManagerChanged** processador de notificação.

```C#
public void OnStateManagerChangedHandler(object sender, NotifyStateManagerChangedEventArgs e)
{
    if (e.Action == NotifyStateManagerChangedAction.Rebuild)
    {
        this.ProcessStataManagerRebuildNotification(e);

        return;
    }

    this.ProcessStateManagerSingleEntityNotification(e);
}
```

## <a name="reliable-dictionary-notifications"></a>Notificações de dicionário fiáveis
Dicionário fiável fornece notificações para os seguintes eventos:

* A reconstrução: Chamado quando **ReliableDictionary** recuperou o estado de uma cópia de segurança ou recuperado ou copiado de estado local.
* Limpar: Chamado quando o estado de **ReliableDictionary** foi limpo através de **ClearAsync** método.
* Adicione: Chamado quando um item foi adicionado ao **ReliableDictionary**.
* Atualização: Chamado quando um item na **IReliableDictionary** foi atualizada.
* Remover: Chamado quando um item na **IReliableDictionary** foi eliminado.

Para obter notificações do dicionário fiável, terá de registar o **DictionaryChanged** processador de eventos no **IReliableDictionary**. Um local comum para registar com estes processadores de eventos está no **ReliableStateManager.StateManagerChanged** adicionar notificação.
Registar quando **IReliableDictionary** é adicionado à **IReliableStateManager** garante que não irá perder quaisquer notificações.

```C#
private void ProcessStateManagerSingleEntityNotification(NotifyStateManagerChangedEventArgs e)
{
    var operation = e as NotifyStateManagerSingleEntityChangedEventArgs;

    if (operation.Action == NotifyStateManagerChangedAction.Add)
    {
        if (operation.ReliableState is IReliableDictionary<TKey, TValue>)
        {
            var dictionary = (IReliableDictionary<TKey, TValue>)operation.ReliableState;
            dictionary.RebuildNotificationAsyncCallback = this.OnDictionaryRebuildNotificationHandlerAsync;
            dictionary.DictionaryChanged += this.OnDictionaryChangedHandler;
            }
        }
    }
}
```

> [!NOTE]
> **ProcessStateManagerSingleEntityNotification** é o método de exemplo que precedente **OnStateManagerChangedHandler** chamadas de exemplo.
> 
> 

Os conjuntos de código anterior a **IReliableNotificationAsyncCallback** interface, along com **DictionaryChanged**. Porque **NotifyDictionaryRebuildEventArgs** contém um **IAsyncEnumerable** interface – que têm de ser enumerados de forma assíncrona - notificações de reconstrução são desencadeadas através de **RebuildNotificationAsyncCallback** em vez de **OnDictionaryChangedHandler**.

```C#
public async Task OnDictionaryRebuildNotificationHandlerAsync(
    IReliableDictionary<TKey, TValue> origin,
    NotifyDictionaryRebuildEventArgs<TKey, TValue> rebuildNotification)
{
    this.secondaryIndex.Clear();

    var enumerator = e.State.GetAsyncEnumerator();
    while (await enumerator.MoveNextAsync(CancellationToken.None))
    {
        this.secondaryIndex.Add(enumerator.Current.Key, enumerator.Current.Value);
    }
}
```

> [!NOTE]
> Pela primeira vez no código anterior, como parte de processar a notificação de reconstrução é limpa o estado agregado manter. Porque a coleção fiável está a ser reconstruída com um Estado de novo, todas as notificações anteriores são irrelevantes.
> 
> 

O **DictionaryChanged** utiliza o processador de eventos **NotifyDictionaryChangedEventArgs** para fornecer os detalhes sobre o evento.
**NotifyDictionaryChangedEventArgs** tem cinco subclasses. Utilize a propriedade de ação na **NotifyDictionaryChangedEventArgs** converter **NotifyDictionaryChangedEventArgs** para a subclasse correta:

* **NotifyDictionaryChangedAction.Rebuild**: **NotifyDictionaryRebuildEventArgs**
* **NotifyDictionaryChangedAction.Clear**: **NotifyDictionaryClearEventArgs**
* **NotifyDictionaryChangedAction.Add** e **NotifyDictionaryChangedAction.Remove**: **NotifyDictionaryItemAddedEventArgs**
* **NotifyDictionaryChangedAction.Update**: **NotifyDictionaryItemUpdatedEventArgs**
* **NotifyDictionaryChangedAction.Remove**: **NotifyDictionaryItemRemovedEventArgs**

```C#
public void OnDictionaryChangedHandler(object sender, NotifyDictionaryChangedEventArgs<TKey, TValue> e)
{
    switch (e.Action)
    {
        case NotifyDictionaryChangedAction.Clear:
            var clearEvent = e as NotifyDictionaryClearEventArgs<TKey, TValue>;
            this.ProcessClearNotification(clearEvent);
            return;

        case NotifyDictionaryChangedAction.Add:
            var addEvent = e as NotifyDictionaryItemAddedEventArgs<TKey, TValue>;
            this.ProcessAddNotification(addEvent);
            return;

        case NotifyDictionaryChangedAction.Update:
            var updateEvent = e as NotifyDictionaryItemUpdatedEventArgs<TKey, TValue>;
            this.ProcessUpdateNotification(updateEvent);
            return;

        case NotifyDictionaryChangedAction.Remove:
            var deleteEvent = e as NotifyDictionaryItemRemovedEventArgs<TKey, TValue>;
            this.ProcessRemoveNotification(deleteEvent);
            return;

        default:
            break;
    }
}
```

## <a name="recommendations"></a>Recomendações
* *Efetue* concluir mais rápido possíveis eventos de notificação.
* *Não* executar quaisquer operações dispendiosas (por exemplo, operações de e/s) como parte dos eventos síncronos.
* *Efetue* Verifique o tipo de ação antes de processar o evento. Novos tipos de ação podem ser adicionados no futuro.

Seguem-se alguns aspetos a ter em mente:

* As notificações são desencadeadas como parte da execução de uma operação. Por exemplo, uma notificação de restauro é desencadeada como o último passo de uma operação de restauro. Um restauro não será concluída até que o evento de notificação está processado.
* Porque as notificações são desencadeadas como parte das operações aplicar, os clientes ver apenas as notificações para operações localmente consolidadas. E porque operações garantidas apenas a ser consolidada localmente (por outras palavras, tem sessão iniciada), podem ou não ser anuladas no futuro.
* No caminho de Refazer, uma única notificação é desencadeada para cada operação aplicada. Isto significa que se transação T1 inclui Create(X), Delete(X) e Create(X), receberá uma notificação para a criação da X, um para a eliminação e um para a criação de novo, por essa ordem.
* Para transações que contêm várias operações, as operações são aplicadas pela ordem em que foram recebidos na réplica primária do utilizador.
* Como parte do processamento de progresso FALSO, algumas operações podem ser anuladas. As notificações são geradas para anulação operações, a reverter o estado da réplica para um ponto de estável. Uma diferença importante de notificações de anulação é que são agregados eventos que tenham chaves duplicadas. Por exemplo, se está a ser anulada transação T1, verá uma notificação para Delete(X) único.

## <a name="next-steps"></a>Passos seguintes
* [Reliable Collections](service-fabric-work-with-reliable-collections.md)
* [Início rápido de serviços fiável](service-fabric-reliable-services-quick-start.md)
* [Reliable Services de cópia de segurança e restaurar (recuperação após desastre)](service-fabric-reliable-services-backup-restore.md)
* [Referência para programadores para coleções fiável](https://msdn.microsoft.com/library/azure/microsoft.servicefabric.data.collections.aspx)

