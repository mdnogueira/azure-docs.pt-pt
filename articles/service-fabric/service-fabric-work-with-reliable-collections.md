---
title: "Trabalhar com coleções fiáveis | Microsoft Docs"
description: "Saiba as melhores práticas para trabalhar com coleções fiável."
services: service-fabric
documentationcenter: .net
author: rajak
manager: timlt
editor: 
ms.assetid: 39e0cd6b-32c4-4b97-bbcf-33dad93dcad1
ms.service: Service-Fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 04/19/2017
ms.author: rajak
ms.openlocfilehash: f53f13e4fb83b1cd370ec673e86e5311cd93055f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="working-with-reliable-collections"></a>Trabalhar com coleções fiáveis
Recursos de infraestrutura de serviço oferece um modelo de programação com monitorização de estado disponível para programadores do .NET através de coleções fiável. Especificamente, o Service Fabric fornece dicionário fiável e classes de fila fiável. Ao utilizar estas classes, o estado está particionado (para escalabilidade), replicado (para disponibilidade) e transacionado dentro de uma partição (para semântica ACID). Vamos observar uma utilização típica de um objeto de dicionário fiável e ver o respetivo, na verdade, a fazer.

```csharp

///retry:

try {
   // Create a new Transaction object for this partition
   using (ITransaction tx = base.StateManager.CreateTransaction()) {
      // AddAsync takes key's write lock; if >4 secs, TimeoutException
      // Key & value put in temp dictionary (read your own writes),
      // serialized, redo/undo record is logged & sent to
      // secondary replicas
      await m_dic.AddAsync(tx, key, value, cancellationToken);

      // CommitAsync sends Commit record to log & secondary replicas
      // After quorum responds, all locks released
      await tx.CommitAsync();
   }
   // If CommitAsync not called, Dispose sends Abort
   // record to log & all locks released
}
catch (TimeoutException) {
   await Task.Delay(100, cancellationToken); goto retry;
}
```

Todas as operações em objetos de dicionário fiável (exceto ClearAsync que não é undoable), necessitam de um objeto de ITransaction. Este objeto associado ao mesmo qualquer e todas as alterações que está a tentar efetuar para qualquer dicionário fiável e/ou fiável fila objetos dentro de uma única partição. Adquirir um ITransaction objeto chamando a partição do método de CreateTransaction do StateManager.

No código acima, o objeto de ITransaction é transmitido ao método de AddAsync de um dicionário fiável. Internamente, métodos de dicionário que aceita uma chave demorar um bloqueio de leitor/escritor associado à chave. Se o método modifica o valor da chave, o método assume um bloqueio de escrita na chave e se o método só lê a partir do valor da chave, um bloqueio de leitura, em seguida, é executado na chave. Uma vez que AddAsync modifica o valor da chave para o novo e passado no valor, o bloqueio de escrita a chave foi atribuído. Por isso, se a tentativa de threads de 2 (ou mais) adicionar valores com a mesma chave ao mesmo tempo, um thread vai adquirir o bloqueio de escrita e irão impedir que os outros threads. Por predefinição, o bloco de métodos de até 4 segundos adquirir o bloqueio; Depois de 4 segundos, os métodos de gerem um TimeoutException. Sobrecargas do método existem, permitindo-lhe transmita um valor de timeout explícito, se preferir.

Normalmente, escrever código para reagir a um TimeoutException por obtendo-lo e repetir a operação de completa (conforme mostrado no código acima). No código my simple, apenas posso estou chamar Task.Delay transmitir 100 milissegundos cada vez. No entanto, na realidade, poderá ser melhor através de algum tipo de atraso de início de término exponencial em vez disso.

Depois do bloqueio é obtido, AddAsync adiciona as referências de objeto de chave e valor para um dicionário temporário interno associado ao objeto de ITransaction. Isto é feito para lhe fornecer a semântica de leitura-your-proprietário-operações de escrita. Ou seja, depois de chamar AddAsync, uma chamada posterior para TryGetValueAsync (utilizando o mesmo objeto de ITransaction) irá devolver o valor, mesmo se ainda não consolidada a transação. Em seguida, AddAsync serializa a chave e valor objetos às matrizes de bytes e acrescenta estes matrizes de bytes para um ficheiro de registo no nó local. Por fim, AddAsync envia as matrizes de bytes para todas as réplicas secundárias pelo que têm as mesmas informações de chave/valor. Apesar das informações de chave/valor foi escritas para um ficheiro de registo, as informações não são consideradas parte do dicionário de até é consolidar a transação que estão associados.

No código acima, a chamada para CommitAsync consolida todas as operações da transação. Especificamente, acrescenta consolidação informações para o ficheiro de registo no nó local e também envia o registo de consolidação para todas as réplicas secundárias. Depois de um quórum (maioria) das réplicas tem respondidas, todas as alterações de dados são consideradas permanentes e qualquer bloqueios associados às chaves que foram manipuladas através do objeto de ITransaction são libertados para outras transações/threads pode manipular as mesmas chaves e os respetivos valores.

Se CommitAsync não for chamado (normalmente, devido a uma exceção que está a ser emitida), o objeto de ITransaction obtém eliminado. Quando disposing um objeto de ITransaction não consolidado, o Service Fabric acrescenta abortar informações para o ficheiro de registo do nó local e nada tem de ser enviadas para qualquer uma das réplicas secundárias. E, em seguida, são lançados as bloqueios associados às chaves que foram manipuladas através da transação.

## <a name="common-pitfalls-and-how-to-avoid-them"></a>Pitfalls comuns e como os evitar
Agora que compreende como as coleções fiáveis funcionam internamente, vamos ver alguns misuses comuns delas. Consulte o código abaixo:

```csharp
using (ITransaction tx = StateManager.CreateTransaction()) {
   // AddAsync serializes the name/user, logs the bytes,
   // & sends the bytes to the secondary replicas.
   await m_dic.AddAsync(tx, name, user);

   // The line below updates the property’s value in memory only; the
   // new value is NOT serialized, logged, & sent to secondary replicas.
   user.LastLogin = DateTime.UtcNow;  // Corruption!

   await tx.CommitAsync();
}
```

Ao trabalhar com um dicionário de .NET regular, pode adicionar um chave-valor para o dicionário e, em seguida, altere o valor de uma propriedade (tais como LastLogin). No entanto, este código não funcionará corretamente com um dicionário fiável. Lembre-se do debate anterior, a chamada para AddAsync serializa os objetos de chave/valor a matrizes de bytes e, em seguida, guarda as matrizes de um ficheiro local e também envia-as para réplicas secundárias. Se posteriormente alterar uma propriedade, isto altera o valor da propriedade na memória apenas; não afeta o ficheiro local ou os dados enviados para as réplicas. Se o processo de falha, o que é na memória é emitida ausente. Quando inicia um processo novo ou se outra réplica se tornar principal, em seguida, o valor da propriedade antigo é que está disponível.

Realçar posso não é suficientemente como é fácil para tornar o tipo de erro mostrado acima. Além disso, apenas aprenderá sobre o erro se/quando que o processo de fica inativo. A forma correta para escrever o código é simplesmente inverter as duas linhas:


```csharp

using (ITransaction tx = StateManager.CreateTransaction()) {
   user.LastLogin = DateTime.UtcNow;  // Do this BEFORE calling AddAsync
   await m_dic.AddAsync(tx, name, user);
   await tx.CommitAsync();
}
```

Segue-se outro exemplo que mostra um erro comum:

```csharp

using (ITransaction tx = StateManager.CreateTransaction()) {
   // Use the user’s name to look up their data
   ConditionalValue<User> user =
      await m_dic.TryGetValueAsync(tx, name);

   // The user exists in the dictionary, update one of their properties.
   if (user.HasValue) {
      // The line below updates the property’s value in memory only; the
      // new value is NOT serialized, logged, & sent to secondary replicas.
      user.Value.LastLogin = DateTime.UtcNow; // Corruption!
      await tx.CommitAsync();
   }
}
```

Novamente, com regulares dicionários de .NET, o código acima funciona bem e é um padrão comum: o programador utiliza uma chave para procurar um valor. Se o valor, o programador altera o valor da propriedade. No entanto, com coleções fiáveis, este código exhibits o mesmo problema abordado como já: **não deve modificar um objecto uma vez que atribuiu-la a uma coleção fiável.**

É a forma correta para atualizar um valor de uma coleção fiável, para obter uma referência para o valor existente e considere o objeto referenciado por esta referência imutável. Em seguida, crie um novo objeto que é uma cópia exata do objeto original. Agora, pode modificar o estado deste objeto novo e escrever o novo objeto à coleção para que este obtém serializado para matrizes de bytes, anexado ao ficheiro local e enviadas para as réplicas. Depois de confirmar a change(s), os objetos em memória, o ficheiro local e todas as réplicas têm o mesmo Estado exato. Todos os é boa!

O código abaixo mostra a forma correta para atualizar um valor de uma coleção fiável:

```csharp

using (ITransaction tx = StateManager.CreateTransaction()) {
   // Use the user’s name to look up their data
   ConditionalValue<User> currentUser =
      await m_dic.TryGetValueAsync(tx, name);

   // The user exists in the dictionary, update one of their properties.
   if (currentUser.HasValue) {
      // Create new user object with the same state as the current user object.
      // NOTE: This must be a deep copy; not a shallow copy. Specifically, only
      // immutable state can be shared by currentUser & updatedUser object graphs.
      User updatedUser = new User(currentUser);

      // In the new object, modify any properties you desire
      updatedUser.LastLogin = DateTime.UtcNow;

      // Update the key’s value to the updateUser info
      await m_dic.SetValue(tx, name, updatedUser);

      await tx.CommitAsync();
   }
}
```

## <a name="define-immutable-data-types-to-prevent-programmer-error"></a>Definir os tipos de dados imutável para evitar erros de programador
Idealmente, pretende que o compilador de erros de relatório quando acidentalmente a produzir código mutates estado de um objeto que é suposto considerar imutável. No entanto, o compilador do c# não tem a capacidade para efetuar este procedimento. Por isso, para evitar potenciais erros de programador, recomendamos vivamente que definir os tipos de utilizar com coleções fiáveis para ser tipos imutáveis. Mais concretamente, isto significa que pen para tipos de valor de núcleos (por exemplo, números [Int32, UInt64, etc.], DateTime, Guid, TimeSpan e o semelhante). E, obviamente, também pode utilizar cadeia. É melhor evitar a propriedades de coleção como serializar e anular a serialização dos mesmos pode frequentemente pacote pode prejudicar o desempenho. No entanto, se pretender utilizar propriedades de coleção, recomendamos vivamente a utilização de. Biblioteca de coleções imutável do NET ([System.Collections.Immutable](https://www.nuget.org/packages/System.Collections.Immutable/)). Está disponível para transferência a partir do http://nuget.org esta biblioteca. Também recomendamos que selar as classes e tornando os campos só de leitura sempre que possível.

O tipo de UserInfo abaixo demonstra como definir um tipo imutável tirando partido de recomendações acima mencionados.

```csharp

[DataContract]
// If you don’t seal, you must ensure that any derived classes are also immutable
public sealed class UserInfo {
   private static readonly IEnumerable<ItemId> NoBids = ImmutableList<ItemId>.Empty;

   public UserInfo(String email, IEnumerable<ItemId> itemsBidding = null) {
      Email = email;
      ItemsBidding = (itemsBidding == null) ? NoBids : itemsBidding.ToImmutableList();
   }

   [OnDeserialized]
   private void OnDeserialized(StreamingContext context) {
      // Convert the deserialized collection to an immutable collection
      ItemsBidding = ItemsBidding.ToImmutableList();
   }

   [DataMember]
   public readonly String Email;

   // Ideally, this would be a readonly field but it can't be because OnDeserialized
   // has to set it. So instead, the getter is public and the setter is private.
   [DataMember]
   public IEnumerable<ItemId> ItemsBidding { get; private set; }

   // Since each UserInfo object is immutable, we add a new ItemId to the ItemsBidding
   // collection by creating a new immutable UserInfo object with the added ItemId.
   public UserInfo AddItemBidding(ItemId itemId) {
      return new UserInfo(Email, ((ImmutableList<ItemId>)ItemsBidding).Add(itemId));
   }
}
```

O tipo de ItemId também é um tipo imutável, conforme mostrado aqui:

```csharp

[DataContract]
public struct ItemId {

   [DataMember] public readonly String Seller;
   [DataMember] public readonly String ItemName;
   public ItemId(String seller, String itemName) {
      Seller = seller;
      ItemName = itemName;
   }
}
```

## <a name="schema-versioning-upgrades"></a>Controlo de versões do esquema (atualizações)
Internamente, coleções fiável serializar os objetos a utilizar. DataContractSerializer da rede. Os objetos serializados são mantidos no disco local da réplica primária e também são transmitidos para réplicas secundárias. Como o seu serviço evoluiu, é provável que pretenda alterar o tipo de dados (esquema) necessita do seu serviço. Tem de abordar o controlo de versões dos seus dados com cuidado excelente. Primeiro e mais importante, deve sempre poderá anular a serialização dados antigos. Mais concretamente, isto significa que o código de anulação da serialização tem de ser infinitamente retrocompatível: 333 de versão de código do serviço tem de ser possível operar nos dados colocados numa coleção fiável por versão 1 do código do serviço 5 anos há.

Além disso, o código do serviço é atualizado um domínio de atualização a uma hora. Por isso, durante uma atualização, tem duas versões diferentes do código do serviço em execução em simultâneo. Deve evitar ter a nova versão do código do serviço utilizar o novo esquema como versões antigas do código do serviço poderão não ser capazes de lidar com o esquema novo. Sempre que possível, deve criar cada versão do seu serviço para ser diretamente compatíveis por 1 versão. Mais concretamente, isto significa que V1 do código do serviço deve conseguir simplesmente ignorar quaisquer elementos de esquema não processar explicitamente. No entanto, tem de ser capaz de guardar quaisquer dados que explicitamente desconheça e simplesmente-lo de contingência ao atualizar uma chave de dicionário ou valor de escrita.

> [!WARNING]
> Enquanto pode modificar o esquema de uma chave, certifique-se de que o código de hash da chave e algoritmos de é igual a estão estáveis. Se alterar qualquer uma destes algoritmos como operar, não será possível procurar alguma vez novamente a chave no dicionário fiável.
>
>

Em alternativa, pode efetuar o que, normalmente, é referido como uma atualização de fase 2. Com uma atualização de fase 2, atualizar o serviço v1 para V2: V2 contém o código que sabe como lidar com a nova alteração de esquema, mas este código não ser executado. Quando o código de V2 lê dados V1, funciona no mesmo e escreve dados V1. Em seguida, após a atualização concluída em atualização de todos os domínios, pode examinar assinalar para as instâncias de V2 em execução que a atualização foi concluída. (Uma forma Sinal Isto serve para implementar uma atualização da configuração; este é o que faz com que esta uma atualização de fase 2). Agora, as instâncias de V2 podem ler os dados de V1, convertê-lo para dados V2, operar na mesma e escrever como dados V2. Quando outras instâncias de ler os dados de V2, não é necessário convertê-lo, podendo apenas operam na mesma e escrever dados V2.

## <a name="next-steps"></a>Passos Seguintes
Para saber mais sobre a criação de contratos de reencaminhar dados compatível, consulte [compatível com a reencaminhar dados contratos](https://msdn.microsoft.com/library/ms731083.aspx).

Para obter as melhores práticas em contratos de dados de controlo de versões, consulte [controlo de versões de contrato de dados](https://msdn.microsoft.com/library/ms731138.aspx).

Para saber como implementar contratos de dados de tolerância a falhas de versão, consulte [chamadas de retorno de serialização tolerância a falhas versão](https://msdn.microsoft.com/library/ms733734.aspx).

Para saber como fornecer uma estrutura de dados que pode interagir entre várias versões, consulte [IExtensibleDataObject](https://msdn.microsoft.com/library/system.runtime.serialization.iextensibledataobject.aspx).
