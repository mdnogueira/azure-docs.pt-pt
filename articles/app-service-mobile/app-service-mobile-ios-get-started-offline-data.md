---
title: "Ativar a sincronização offline com aplicações móveis iOS | Microsoft Docs"
description: "Saiba como utilizar aplicações móveis do App Service do Azure para a cache e sincronização de dados offline em aplicações iOS."
documentationcenter: ios
author: ggailey777
manager: syntaxc4
editor: 
services: app-service\mobile
ms.assetid: eb5b9520-0f39-4a09-940a-dadb6d940db8
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-ios
ms.devlang: objective-c
ms.topic: article
ms.date: 10/01/2016
ms.author: glenga
ms.openlocfilehash: 44c0d26b2d7d28322d436d4bda319d728c31a635
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="enable-offline-syncing-with-ios-mobile-apps"></a>Ativar a sincronização offline com aplicações móveis iOS
[!INCLUDE [app-service-mobile-selector-offline](../../includes/app-service-mobile-selector-offline.md)]

## <a name="overview"></a>Descrição geral
Este tutorial abrange offline a sincronizar com a funcionalidade de Mobile Apps do App Service do Azure para iOS. Os utilizadores finais de sincronize offline pode interagir com uma aplicação móvel para ver, adicionar ou modificar dados, mesmo quando estes não têm qualquer ligação de rede. As alterações são armazenadas na base de dados local. Depois do dispositivo estiver novamente online, as alterações são sincronizadas com o back-end remoto.

Se esta for a sua primeira experiência com as aplicações móveis, primeiro deve concluir o tutorial [criar uma aplicação iOS]. Se utilizar o projeto de servidor de início rápido transferido, tem de adicionar os pacotes de extensão de acesso a dados ao seu projeto. Para obter mais informações sobre pacotes de extensão de servidor, consulte [trabalhar com o servidor de back-end .NET SDK de Mobile Apps do Azure](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md).

Para saber mais sobre a funcionalidade de sincronização offline, consulte [sincronização de dados Offline em Mobile Apps].

## <a name="review-sync"></a>Rever o código de sincronização do cliente
O projeto de cliente que transferiu para a [criar uma aplicação iOS] tutorial já contém o código que suporta a sincronização offline utilizando uma base de dados baseada em dados de núcleos local. Esta secção resume o que já está incluído com o código do tutorial. Para uma descrição geral conceptual da funcionalidade, consulte [sincronização de dados Offline em Mobile Apps].

Utilizar a funcionalidade de sincronização de dados offline de aplicações móveis, os utilizadores finais podem interagir uma com uma base de dados local, mesmo quando a rede está inacessível. Para utilizar estas funcionalidades na sua aplicação, a inicializar o contexto de sincronização de `MSClient` e referencia um armazenamento local. Em seguida, referenciar a tabela através de **MSSyncTable** interface.

No **QSTodoService.m** (Objective-C) ou **ToDoTableViewController.swift** (Swift), tenha em atenção que o tipo do membro **syncTable** é **MSSyncTable**. Sincronização offline utiliza esta interface de tabela de sincronização em vez de **MSTable**. Quando uma tabela de sincronização é utilizada, todas as operações de ir para o arquivo local e são sincronizadas apenas com remoto de back-end com as operações de push e pull explícitas.

 Para obter uma referência a uma tabela de sincronização, utilize o **syncTableWithName** método no `MSClient`. Para remover a funcionalidade de sincronização offline, utilize **tableWithName** em vez disso.

Antes de quaisquer operações de tabela podem ser efetuadas, o arquivo local tem de ser inicializado. Eis o código relevante:

* **Objective-C**. No **QSTodoService.init** método:

   ```objc
   MSCoreDataStore *store = [[MSCoreDataStore alloc] initWithManagedObjectContext:context];
   self.client.syncContext = [[MSSyncContext alloc] initWithDelegate:nil dataSource:store callback:nil];
   ```    
* **SWIFT**. No **ToDoTableViewController.viewDidLoad** método:

   ```swift
   let client = MSClient(applicationURLString: "http:// ...") // URI of the Mobile App
   let managedObjectContext = (UIApplication.sharedApplication().delegate as! AppDelegate).managedObjectContext!
   self.store = MSCoreDataStore(managedObjectContext: managedObjectContext)
   client.syncContext = MSSyncContext(delegate: nil, dataSource: self.store, callback: nil)
   ```
   Este método cria um armazenamento local utilizando o `MSCoreDataStore` interface, o que fornece o SDK de aplicações móveis. Em alternativa, pode fornecer um arquivo local diferente, implementando o `MSSyncContextDataSource` protocolo. Além disso, o primeiro parâmetro do **MSSyncContext** é utilizada para especificar um processador de conflito. Porque é passaram `nil`, obtemos o processador de conflito predefinido, que falha em qualquer conflito.

Agora, vamos efetuar a operação de sincronização real e obter dados de back-end remoto:

* **Objective-C**. `syncData`primeiro pushes novas alterações e, em seguida, chama **pullData** para obter dados de back-end remoto. Por sua vez, o **pullData** método obtém dados novos que corresponde a uma consulta:

   ```objc
   -(void)syncData:(QSCompletionBlock)completion
   {
       // Push all changes in the sync context, and then pull new data.
       [self.client.syncContext pushWithCompletion:^(NSError *error) {
           [self logErrorIfNotNil:error];
           [self pullData:completion];
       }];
   }

   -(void)pullData:(QSCompletionBlock)completion
   {
       MSQuery *query = [self.syncTable query];

       // Pulls data from the remote server into the local table.
       // We're pulling all items and filtering in the view.
       // Query ID is used for incremental sync.
       [self.syncTable pullWithQuery:query queryId:@"allTodoItems" completion:^(NSError *error) {
           [self logErrorIfNotNil:error];

           // Lets the caller know that we have finished.
           if (completion != nil) {
               dispatch_async(dispatch_get_main_queue(), completion);
           }
       }];
   }
   ```
* **SWIFT**:
   ```swift
   func onRefresh(sender: UIRefreshControl!) {
      UIApplication.sharedApplication().networkActivityIndicatorVisible = true

      self.table!.pullWithQuery(self.table?.query(), queryId: "AllRecords") {
          (error) -> Void in

          UIApplication.sharedApplication().networkActivityIndicatorVisible = false

          if error != nil {
              // A real application would handle various errors like network conditions,
              // server conflicts, etc via the MSSyncContextDelegate
              print("Error: \(error!.description)")

              // We will discard our changes and keep the server's copy for simplicity
              if let opErrors = error!.userInfo[MSErrorPushResultKey] as? Array<MSTableOperationError> {
                  for opError in opErrors {
                      print("Attempted operation to item \(opError.itemId)")
                      if (opError.operation == .Insert || opError.operation == .Delete) {
                          print("Insert/Delete, failed discarding changes")
                          opError.cancelOperationAndDiscardItemWithCompletion(nil)
                      } else {
                          print("Update failed, reverting to server's copy")
                          opError.cancelOperationAndUpdateItem(opError.serverItem!, completion: nil)
                      }
                  }
              }
          }
          self.refreshControl?.endRefreshing()
      }
   }
   ```

Na versão Objective-C, no `syncData`, chamamos primeiro **pushWithCompletion** no contexto de sincronização. Este método é um membro de `MSSyncContext` (e não a tabela de sincronização propriamente dito) porque este empurra alterações em todas as tabelas. Apenas os registos que foram modificados de alguma forma localmente (através de operações de CUD) são enviados para o servidor. Em seguida, o programa auxiliar de **pullData** denomina-se, que invoca **MSSyncTable.pullWithQuery** para obter os dados remotos e armazene-o numa base de dados local.

Na versão Swift, porque a operação de push não era estritamente necessária, não há nenhuma chamada para **pushWithCompletion**. Se existirem quaisquer alterações pendentes no contexto de sincronização para a tabela que está a fazer uma operação de push, extração sempre emite um push primeiro. No entanto, se tiver mais de uma tabela de sincronização, é melhor chamar explicitamente o push para se certificar de que tudo seja consistente em tabelas relacionadas.

Objective-C tanto nas versões Swift, pode utilizar o **pullWithQuery** método para especificar uma consulta para filtrar os registos que pretende obter. Neste exemplo, a consulta obtém todos os registos na remoto `TodoItem` tabela.

O segundo parâmetro de **pullWithQuery** é um ID de consulta que é utilizado para *sincronização incremental*. Sincronização incremental obtém apenas os registos que foram modificados desde a última sincronização com o registo `UpdatedAt` carimbo de hora (chamado `updatedAt` no arquivo local.) O ID de consulta deve ser uma cadeia descritiva que é exclusiva para cada consulta na sua aplicação lógica. Para optar ativamente por participar fora de sincronização incremental, transmitir `nil` como o ID de consulta. Esta abordagem pode ser ineficaz potencialmente, porque este obtém todos os registos de cada operação de extração.

A aplicação de Objective-C sincroniza-se quando modifica ou adicionar dados, quando um utilizador efetua o gesto de atualização e, no início.

A aplicação Swift sincroniza-se quando o utilizador efetua o gesto de atualização e no início.

Porque as sincronizações de aplicação sempre que os dados são modificados (Objective-C) ou sempre que a aplicação for iniciada (Objective-C e Swift), a aplicação parte do princípio de que o utilizador está online. Numa secção posterior, irá atualizar a aplicação para que os utilizadores podem editar, mesmo quando estão offline.

## <a name="review-core-data"></a>Reveja o modelo de dados de núcleo
Quando utilizar o arquivo de offline de dados principal, tem de definir específicas tabelas e campos no seu modelo de dados. A aplicação de exemplo já inclui um modelo de dados com o formato correto. Nesta secção, iremos guiá-estas tabelas para mostrar a forma como são utilizados.

Abra **QSDataModel.xcdatamodeld**. As quatro tabelas são definidas – três que são utilizados pelo SDK e outro que é utilizado para as tarefas de itens próprios:
  * MS_TableOperations: Controla os itens que devem ser sincronizados com o servidor.
  * MS_TableOperationErrors: Controla os erros que ocorrem durante a sincronização offline.
  * MS_TableConfig: Controla os últimos atualizado tempo para a última operação de sincronização para todas as operações de extração.
  * TodoItem: Armazena itens de tarefas. As colunas de sistema **createdAt**, **updatedAt**, e **versão** são propriedades do sistema opcional.

> [!NOTE]
> O SDK de aplicações móveis reserva-se os nomes de coluna que começam por "**``**". Não utilize este prefixo com diferente de colunas de sistema. Caso contrário, os nomes de coluna são modificados ao utilizar o back-end remoto.
>
>

Quando utilizar a funcionalidade de sincronização offline, defina as tabelas de três sistema e a tabela de dados.

### <a name="system-tables"></a>Tabelas de sistema

**MS_TableOperations**  

![Atributos de tabela MS_TableOperations][defining-core-data-tableoperations-entity]

| Atributo | Tipo |
| --- | --- |
| ID | Número inteiro de 64 |
| itemId | Cadeia |
| propriedades | Dados binários |
| Tabela | Cadeia |
| tableKind | Número inteiro de 16 |


**MS_TableOperationErrors**

 ![Atributos de tabela MS_TableOperationErrors][defining-core-data-tableoperationerrors-entity]

| Atributo | Tipo |
| --- | --- |
| ID |Cadeia |
| operationId |Número inteiro de 64 |
| propriedades |Dados binários |
| tableKind |Número inteiro de 16 |

 **MS_TableConfig**

 ![][defining-core-data-tableconfig-entity]

| Atributo | Tipo |
| --- | --- |
| ID |Cadeia |
| key |Cadeia |
| KeyType |Número inteiro de 64 |
| Tabela |Cadeia |
| valor |Cadeia |

### <a name="data-table"></a>Tabela de dados

**TodoItem**

| Atributo | Tipo | Nota |
| --- | --- | --- |
| ID | Cadeia, marcada como necessária |chave primária no arquivo remoto |
| Concluir | Valor booleano | Campo de item de tarefas |
| Texto |Cadeia |Campo de item de tarefas |
| createdAt | Data | (opcional) Mapeia para **createdAt** propriedade de sistema |
| updatedAt | Data | (opcional) Mapeia para **updatedAt** propriedade de sistema |
| Versão | Cadeia | (opcional) Utilizado para detetar conflitos, é mapeado para a versão |

## <a name="setup-sync"></a>Alterar o comportamento de sincronização da aplicação
Nesta secção, modificar a aplicação para que não sincroniza no início da aplicação ou quando insert e atualizar itens. Sincroniza-se apenas quando o botão de gesto de atualização é executado.

**Objective-C**:

1. No **QSTodoListViewController.m**, altere o **viewDidLoad** método para remover a chamada para `[self refresh]` no final do método. Agora, os dados não estão sincronizados com o servidor no início da aplicação. Em vez disso, está sincronizado com o conteúdo do arquivo local.
2. No **QSTodoService.m**, modifique a definição de `addItem` para que não sincronizar depois do item é inserido. Remova o `self syncData` bloquear e substitua-o com o seguinte:

   ```objc
   if (completion != nil) {
       dispatch_async(dispatch_get_main_queue(), completion);
   }
   ```
3. Modificar a definição de `completeItem` tal como mencionado anteriormente. Remover o bloco de `self syncData` e substitua-o com o seguinte:
   ```objc
   if (completion != nil) {
       dispatch_async(dispatch_get_main_queue(), completion);
   }
   ```

**SWIFT**:

No `viewDidLoad`, na **ToDoTableViewController.swift**, comente duas linhas apresentadas aqui, para parar a sincronização no início da aplicação. No momento desta redação, a aplicação Swift Todo não atualizar o serviço quando alguém adiciona ou concluir um item. Atualiza o serviço apenas no início da aplicação.

   ```swift
  self.refreshControl?.beginRefreshing()
  self.onRefresh(self.refreshControl)
```

## <a name="test-app"></a>Testar a aplicação
Nesta secção, ligar a um URL inválido para simular um cenário offline. Ao adicionar itens de dados, estão contidos no arquivo de dados de núcleo do local, mas não estão sincronizados com o aplicação móvel de back-end.

1. Altere o URL de aplicação móvel no **QSTodoService.m** para um URL inválido e execute novamente a aplicação:

   **Objective-C**. No QSTodoService.m:
   ```objc
   self.client = [MSClient clientWithApplicationURLString:@"https://sitename.azurewebsites.net.fail"];
   ```
   **SWIFT**. No ToDoTableViewController.swift:
   ```swift
   let client = MSClient(applicationURLString: "https://sitename.azurewebsites.net.fail")
   ```
2. Adicione alguns itens de tarefas. Sair do simulador (ou forçado a fechar a aplicação) e, em seguida, reinicie-lo. Certifique-se de que as alterações sejam mantidas.

3. Ver os conteúdos de remoto **TodoItem** tabela:
   * Para um Node.js de back-end, vá para o [portal do Azure](https://portal.azure.com/) e, na sua aplicação móvel de back-end, clique em **tabelas fácil** > **TodoItem**.  
   * Para uma .NET novamente terminar, utilize uma ferramenta SQL, como o SQL Server Management Studio, ou um cliente REST, tais como o Fiddler ou Postman.  

4. Certifique-se de que os novos itens têm *não* foram sincronizados com o servidor.

5. Altere o URL novamente para o correto no **QSTodoService.m**e volte a executar a aplicação.

6. Execute o gesto de atualização ao extrair para baixo a lista de itens.  
Um spinner de progresso é apresentado.

7. Ver o **TodoItem** dados novamente. Os itens de tarefas novas e alteradas deverá ser agora apresentados.

## <a name="summary"></a>Resumo
Para suportar a funcionalidade de sincronização offline, utilizámos o `MSSyncTable` interface e inicializado `MSClient.syncContext` com um arquivo local. Neste caso, o arquivo local foi uma base de dados baseada em dados de núcleos.

Quando utiliza um arquivo local de dados principal, tem de definir várias tabelas com a [corrigir as propriedades do sistema](#review-core-data).

Criar normal, ler, atualizar e eliminar as operações (CRUD) para o trabalho de aplicações móveis, como se a aplicação ainda está ligada, mas todas as operações de ocorrerem contra o arquivo local.

Quando o arquivo local é sincronizado com o servidor, utilizámos o **MSSyncTable.pullWithQuery** método.

## <a name="additional-resources"></a>Recursos adicionais
* [sincronização de dados Offline em Mobile Apps]
* [Nuvem abrangem: Sincronização Offline nos Mobile Services do Azure] \(o vídeo está prestes a Mobile Services, mas as Mobile Apps offline Sincronizar funciona de forma semelhante.\)

<!-- URLs. -->


[criar uma aplicação iOS]: app-service-mobile-ios-get-started.md
[sincronização de dados Offline em Mobile Apps]: app-service-mobile-offline-data-sync.md

[defining-core-data-tableoperationerrors-entity]: ./media/app-service-mobile-ios-get-started-offline-data/defining-core-data-tableoperationerrors-entity.png
[defining-core-data-tableoperations-entity]: ./media/app-service-mobile-ios-get-started-offline-data/defining-core-data-tableoperations-entity.png
[defining-core-data-tableconfig-entity]: ./media/app-service-mobile-ios-get-started-offline-data/defining-core-data-tableconfig-entity.png
[defining-core-data-todoitem-entity]: ./media/app-service-mobile-ios-get-started-offline-data/defining-core-data-todoitem-entity.png

[Nuvem abrangem: Sincronização Offline nos Mobile Services do Azure]: http://channel9.msdn.com/Shows/Cloud+Cover/Episode-155-Offline-Storage-with-Donna-Malayeri
[Azure Friday: Offline-enabled apps in Azure Mobile Services]: http://azure.microsoft.com/en-us/documentation/videos/azure-mobile-services-offline-enabled-apps-with-donna-malayeri/
