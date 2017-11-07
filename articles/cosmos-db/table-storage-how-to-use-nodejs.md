---
title: Como utilizar o Table storage do Azure do Node.js | Microsoft Docs
description: "Armazene dados estruturados na nuvem através do Table Storage do Azure, um arquivo de dados NoSQL."
services: cosmos-db
documentationcenter: nodejs
author: mimig1
manager: jhubbard
editor: tysonn
ms.assetid: fc2e33d2-c5da-4861-8503-53fdc25750de
ms.service: cosmos-db
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: article
ms.date: 11/03/2017
ms.author: mimig
ms.openlocfilehash: 7a92abbb08270c33f1848dc6789a89c2a81701ff
ms.sourcegitcommit: 295ec94e3332d3e0a8704c1b848913672f7467c8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/06/2017
---
# <a name="how-to-use-azure-table-storage-from-nodejs"></a>Como utilizar o Table storage do Azure do Node.js
[!INCLUDE [storage-selector-table-include](../../includes/storage-selector-table-include.md)]
[!INCLUDE [storage-table-cosmos-db-langsoon-tip-include](../../includes/storage-table-cosmos-db-langsoon-tip-include.md)]

## <a name="overview"></a>Descrição geral
Este tópico mostra como efetuar cenários comuns utilizando o serviço de tabelas do Azure numa aplicação Node.js.

Os exemplos de código neste tópico partem do princípio de que já tem uma aplicação Node.js. Para obter informações sobre como criar uma aplicação Node.js no Azure, consulte a qualquer um dos seguintes tópicos:

* [Criar uma aplicação web Node.js no App Service do Azure](../app-service/app-service-web-get-started-nodejs.md)
* [Criar e implementar uma aplicação Node.js num serviço em nuvem do Azure](../cloud-services/cloud-services-nodejs-develop-deploy-app.md) (através do Windows PowerShell)

[!INCLUDE [storage-table-concepts-include](../../includes/storage-table-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../includes/storage-create-account-include.md)]

## <a name="configure-your-application-to-access-azure-storage"></a>Configurar a sua aplicação para aceder ao armazenamento do Azure
Para utilizar o Storage do Azure, terá do SDK de armazenamento do Azure para Node.js, que inclui um conjunto de bibliotecas de conveniência que comunicam com os serviços REST do storage.

### <a name="use-node-package-manager-npm-to-install-the-package"></a>Utilize o Gestor de pacote de nó (NPM) para instalar o pacote
1. Utilize uma interface de linha de comandos, como **PowerShell** (Windows), **Terminal** (Mac), ou **Bash** (Unix) e navegue para a pasta onde criou a aplicação.
2. Tipo **npm instalar storage do azure** na janela de comandos. Saída do comando é semelhante ao seguinte exemplo.

       azure-storage@0.5.0 node_modules\azure-storage
       +-- extend@1.2.1
       +-- xmlbuilder@0.4.3
       +-- mime@1.2.11
       +-- node-uuid@1.4.3
       +-- validator@3.22.2
       +-- underscore@1.4.4
       +-- readable-stream@1.0.33 (string_decoder@0.10.31, isarray@0.0.1, inherits@2.0.1, core-util-is@1.0.1)
       +-- xml2js@0.2.7 (sax@0.5.2)
       +-- request@2.57.0 (caseless@0.10.0, aws-sign2@0.5.0, forever-agent@0.6.1, stringstream@0.0.4, oauth-sign@0.8.0, tunnel-agent@0.4.1, isstream@0.1.2, json-stringify-safe@5.0.1, bl@0.9.4, combined-stream@1.0.5, qs@3.1.0, mime-types@2.0.14, form-data@0.2.0, http-signature@0.11.0, tough-cookie@2.0.0, hawk@2.3.1, har-validator@1.8.0)
3. Pode executar manualmente o **ls** comando para verificar se um **nó\_módulos** pasta foi criada. Dentro dessa pasta, encontrará a **storage do azure** pacote, que contém as bibliotecas que precisa de aceder ao armazenamento.

### <a name="import-the-package"></a>Importar o pacote
Adicione o seguinte código na parte superior do **server.js** ficheiros na sua aplicação:

```nodejs
var azure = require('azure-storage');
```

## <a name="set-up-an-azure-storage-connection"></a>Configurar uma ligação de armazenamento do Azure
O módulo do azure irá ler as variáveis de ambiente do AZURE\_armazenamento\_conta e o AZURE\_armazenamento\_acesso\_chave ou o AZURE\_armazenamento\_ligação\_cadeia de informações necessárias para ligar à sua conta de armazenamento do Azure. Se estas variáveis de ambiente não estiver definidas, tem de especificar as informações de conta ao chamar **TableService**.

## <a name="create-a-table"></a>Criar uma tabela
O código seguinte cria um **TableService** de objeto e utiliza-o para criar uma nova tabela. Adicione o seguinte perto do topo da **server.js**.

```nodejs
var tableSvc = azure.createTableService();
```

A chamada para **createTableIfNotExists** criará uma nova tabela com o nome especificado se já existir. O exemplo seguinte cria uma nova tabela com o nome 'mytable' se já existir:

```nodejs
tableSvc.createTableIfNotExists('mytable', function(error, result, response){
  if(!error){
    // Table exists or created
  }
});
```

O `result.created` será `true` se for criada uma nova tabela, e `false` se a tabela já existe. O `response` contêm informações sobre o pedido.

### <a name="filters"></a>Filtros
Operações de filtragem opcionais podem ser aplicadas a operações executadas utilizando **TableService**. Operações de filtragem pode incluir registo automaticamente repetir, etc. Os filtros são objetos que implementam um método com a assinatura:

```nodejs
function handle (requestOptions, next)
```

Depois de efetuar a respetiva pré-processamentos nas opções de pedido, o método tem de chamar "seguinte", transmitir uma chamada de retorno com a assinatura seguinte:

```nodejs
function (returnObject, finalCallback, next)
```

Esta chamada de retorno e após o processamento de returnObject (a resposta do pedido para o servidor), a chamada de retorno tem de invocar, em seguida, se existir para continuar a processar outros filtros ou simplesmente invocar finalCallback caso contrário, para terminar a invocação de serviço.

Dois filtros que implementa lógica de repetição são incluídos com o Azure SDK para Node.js, **ExponentialRetryPolicyFilter** e **LinearRetryPolicyFilter**. O seguinte cria um **TableService** objeto que utiliza o **ExponentialRetryPolicyFilter**:

```nodejs
var retryOperations = new azure.ExponentialRetryPolicyFilter();
var tableSvc = azure.createTableService().withFilter(retryOperations);
```

## <a name="add-an-entity-to-a-table"></a>Adicionar uma entidade a uma tabela
Para adicionar uma entidade, primeiro crie um objeto que define as propriedades de entidade. Todas as entidades tem de conter um **PartitionKey** e **RowKey**, que são identificadores exclusivos para a entidade.

* **PartitionKey** -determina a partição que a entidade é armazenada no
* **RowKey** - exclusivamente identifica a entidade na partição

Ambos **PartitionKey** e **RowKey** tem de ser valores de cadeia. Para obter mais informações, consulte [compreender o modelo de dados do serviço tabela](http://msdn.microsoft.com/library/azure/dd179338.aspx).

Segue-se um exemplo de definir uma entidade. Tenha em atenção que **dueDate** está definido como um tipo de **Edm.DateTime**. Especificar o tipo é opcional e tipos irão ser inferidos se não for especificados.

```nodejs
var task = {
  PartitionKey: {'_':'hometasks'},
  RowKey: {'_': '1'},
  description: {'_':'take out the trash'},
  dueDate: {'_':new Date(2015, 6, 20), '$':'Edm.DateTime'}
};
```

> [!NOTE]
> Há também um **Timestamp** campo para cada registo, o que é definido pelo Azure quando uma entidade é inserida ou atualizada.
>
>

Também pode utilizar o **entityGenerator** criar entidades. O exemplo seguinte cria o mesmo tarefa entidade utilizando o **entityGenerator**.

```nodejs
var entGen = azure.TableUtilities.entityGenerator;
var task = {
  PartitionKey: entGen.String('hometasks'),
  RowKey: entGen.String('1'),
  description: entGen.String('take out the trash'),
  dueDate: entGen.DateTime(new Date(Date.UTC(2015, 6, 20))),
};
```

Para adicionar uma entidade a sua tabela, passar o objeto de entidade para o **insertEntity** método.

```nodejs
tableSvc.insertEntity('mytable',task, function (error, result, response) {
  if(!error){
    // Entity inserted
  }
});
```

Se a operação for concluída com êxito, `result` irá conter o [ETag](http://en.wikipedia.org/wiki/HTTP_ETag) do registo inserido e `response` contêm informações sobre a operação.

Exemplo de resposta:

```nodejs
{ '.metadata': { etag: 'W/"datetime\'2015-02-25T01%3A22%3A22.5Z\'"' } }
```

> [!NOTE]
> Por predefinição, **insertEntity** não devolve a entidade inserida como parte do `response` informações. Se planeia efetuar outras operações nesta entidade, ou se pretende colocar em cache as informações, podem ser úteis para que o devolvidas como parte do `result`. Pode fazê-lo ao ativar **echoContent** da seguinte forma:
>
> `tableSvc.insertEntity('mytable', task, {echoContent: true}, function (error, result, response) {...}`
>
>

## <a name="update-an-entity"></a>Atualizar uma entidade
Existem vários métodos disponíveis para atualizar uma entidade existente:

* **replaceEntity** -atualizações de uma entidade existente, substituindo-la
* **mergeEntity** -atualiza uma entidade existente, a intercalação novos valores de propriedade para a entidade existente
* **insertOrReplaceEntity** -atualizações de uma entidade existente, substituindo-lo. Se nenhuma entidade de existir, será inserido um novo
* **insertOrMergeEntity** -atualiza uma entidade existente, a intercalação novos valores de propriedade para o existente. Se nenhuma entidade de existir, será inserido um novo

O exemplo seguinte demonstra a atualizar uma entidade utilizando **replaceEntity**:

```nodejs
tableSvc.replaceEntity('mytable', updatedTask, function(error, result, response){
  if(!error) {
    // Entity updated
  }
});
```

> [!NOTE]
> Por predefinição, ao atualizar uma entidade não verifique se os dados que está a ser atualizados anteriormente foi modificados por outro processo. Para suportar atualizações em simultâneo:
>
> 1. Obter o ETag do objeto que está a ser atualizado. Este é devolvido como parte do `response` em nenhuma operação relacionada com entidade e podem ser recuperadas por meio `response['.metadata'].etag`.
> 2. Quando efetuar uma operação de atualização numa entidade, adicione as informações de ETag obtidas anteriormente para a entidade de novo. Por exemplo:
>
>       entity2 .etag ['.metadata'] = currentEtag;
> 3. Efetue a operação de atualização. Se a entidade foi modificada desde a obter o valor ETag, tais como outra instância da sua aplicação, um `error` vai ser devolvido a indicar que a condição de atualização especificada no pedido não foi cumprida.
>
>

Com **replaceEntity** e **mergeEntity**, se a entidade que está a ser atualizada não existir, em seguida, a operação de atualização falhará. Por conseguinte, se pretender armazenar uma entidade, independentemente de já existir, utilize **insertOrReplaceEntity** ou **insertOrMergeEntity**.

O `result` para atualização com êxito as operações irão conter o **Etag** da entidade atualizada.

## <a name="work-with-groups-of-entities"></a>Trabalhar com grupos de entidades
Por vezes, faz sentido para submeter várias operações em conjunto num batch para garantir atómico processamento pelo servidor. Para o fazer, utilize o **TableBatch** para criar um lote de classe e, em seguida, utilize o **executeBatch** método **TableService** para efetuarem as operações em lote.

 O exemplo seguinte mostra como submeter duas entidades no batch:

```nodejs
var task1 = {
  PartitionKey: {'_':'hometasks'},
  RowKey: {'_': '1'},
  description: {'_':'Take out the trash'},
  dueDate: {'_':new Date(2015, 6, 20)}
};
var task2 = {
  PartitionKey: {'_':'hometasks'},
  RowKey: {'_': '2'},
  description: {'_':'Wash the dishes'},
  dueDate: {'_':new Date(2015, 6, 20)}
};

var batch = new azure.TableBatch();

batch.insertEntity(task1, {echoContent: true});
batch.insertEntity(task2, {echoContent: true});

tableSvc.executeBatch('mytable', batch, function (error, result, response) {
  if(!error) {
    // Batch completed
  }
});
```

Para operações de lote com êxito, `result` irá conter informações para cada operação no lote.

### <a name="work-with-batched-operations"></a>Trabalhar com operações em lote
Operações adicionadas a um lote podem ser inspecionadas visualizando o `operations` propriedade. Também pode utilizar os métodos seguintes para trabalhar com operações:

* **Limpar** -limpa todas as operações do batch
* **getOperations** -obtém uma operação do batch
* **hasOperations** -devolve true se o batch contém operações
* **removeOperations** -remove uma operação
* **tamanho** -devolve o número de operações no lote

## <a name="retrieve-an-entity-by-key"></a>Obter uma entidade pela chave
Para devolver uma entidade específica com base no **PartitionKey** e **RowKey**, utilize o **retrieveEntity** método.

```nodejs
tableSvc.retrieveEntity('mytable', 'hometasks', '1', function(error, result, response){
  if(!error){
    // result contains the entity
  }
});
```

Assim que esta operação for concluída, `result` irá conter a entidade.

## <a name="query-a-set-of-entities"></a>Consulta de um conjunto de entidades
Para consultar uma tabela, utilize o **TableQuery** objeto para criar cópias de segurança utilizando as cláusulas seguintes expressão de consulta:

* **Selecione** -os campos devem ser devolvidos da consulta
* **onde** -where cláusula

  * **e** - um `and` onde condição
  * **ou** - um `or` onde condição
* **parte superior** -o número de itens a obter

O exemplo seguinte cria uma consulta que irá devolver os itens de cinco superiores com um PartitionKey do 'hometasks'.

```nodejs
var query = new azure.TableQuery()
  .top(5)
  .where('PartitionKey eq ?', 'hometasks');
```

Uma vez que **selecione** não for utilizado, serão devolvidos todos os campos. Para executar a consulta em relação a uma tabela, utilize **queryEntities**. O exemplo seguinte utiliza esta consulta para devolver as entidades de 'mytable'.

```nodejs
tableSvc.queryEntities('mytable',query, null, function(error, result, response) {
  if(!error) {
    // query was successful
  }
});
```

Se tiver êxito, `result.entries` irá conter uma matriz de entidades que correspondem a consulta. Se a consulta não foi possível devolver todas as entidades, `result.continuationToken` será não -*nulo* e pode ser utilizado como o terceiro parâmetro de **queryEntities** para obter mais resultados. Para a consulta inicial, utilize *nulo* para o terceiro parâmetro.

### <a name="query-a-subset-of-entity-properties"></a>Consultar um subconjunto de propriedades de entidade
Uma consulta a uma tabela pode obter alguns campos de uma entidade.
Isto reduz a largura de banda e pode melhorar o desempenho de consulta, especialmente para entidades grandes. Utilize o **selecione** cláusula e passar os nomes dos campos a ser devolvido. Por exemplo, a seguinte consulta devolverá apenas o **Descrição** e **dueDate** campos.

```nodejs
var query = new azure.TableQuery()
  .select(['description', 'dueDate'])
  .top(5)
  .where('PartitionKey eq ?', 'hometasks');
```

## <a name="delete-an-entity"></a>Eliminar uma entidade
Pode eliminar uma entidade com as chaves de partição e linha. Neste exemplo, o **task1** objeto contém os **RowKey** e **PartitionKey** valores da entidade será eliminada. Em seguida, o objeto é passado para o **deleteEntity** método.

```nodejs
var task = {
  PartitionKey: {'_':'hometasks'},
  RowKey: {'_': '1'}
};

tableSvc.deleteEntity('mytable', task, function(error, response){
  if(!error) {
    // Entity deleted
  }
});
```

> [!NOTE]
> Considere utilizar ETags aquando da eliminação de itens, para se certificar de que o item não foi modificado por outro processo. Consulte [atualizar uma entidade](#update-an-entity) para obter informações sobre como utilizar ETags.
>
>

## <a name="delete-a-table"></a>Eliminar uma tabela
O seguinte código elimina uma tabela a partir de uma conta de armazenamento.

```nodejs
tableSvc.deleteTable('mytable', function(error, response){
    if(!error){
        // Table deleted
    }
});
```

Se não tiver a certeza se a tabela existe, utilize **deleteTableIfExists**.

## <a name="use-continuation-tokens"></a>Utilize tokens de continuação
Quando estiver a consultar tabelas para grandes quantidades de resultados, procure os tokens de continuação. Poderão estar disponíveis para a consulta que não pode ter apercebido se não criar de reconhecer quando encontra-se um token de continuação grandes quantidades de dados.

Os resultados de objetos devolvido durante a consulta de conjuntos de entidades um `continuationToken` propriedade quando tal um token está presente. Pode utilizar este quando executar uma consulta para continuar para mover as entidades de partição e a tabela.

Ao consultar, poderá ser fornecido um parâmetro de continuationToken entre a instância de objeto de consulta e a função de chamada de retorno:

```nodejs
var nextContinuationToken = null;
dc.table.queryEntities(tableName,
    query,
    nextContinuationToken,
    function (error, results) {
        if (error) throw error;

        // iterate through results.entries with results

        if (results.continuationToken) {
            nextContinuationToken = results.continuationToken;
        }

    });
```

Se a inspecionar o `continuationToken` objeto, encontrará propriedades, tais como `nextPartitionKey`, `nextRowKey` e `targetLocation`, que podem ser utilizadas para iterar através de todos os resultados.

Também é um exemplo de continuação no repositório Node.js de armazenamento do Azure no GitHub. Procure `examples/samples/continuationsample.js`.

## <a name="work-with-shared-access-signatures"></a>Trabalhar com assinaturas de acesso partilhado
Assinaturas de acesso partilhado (SAS) são uma forma segura para fornecer acesso granular a tabelas sem fornecer o nome da conta de armazenamento ou chaves. SAS são frequentemente utilizado para fornecer acesso limitado aos seus dados, tais como permitir que uma aplicação móvel para registos de consulta.

Uma aplicação como um serviço baseado na nuvem fidedigna gera uma SAS utilizando o **generateSharedAccessSignature** do **TableService**e fornece-o a uma aplicação não fidedigna ou por fidedigna, como uma aplicação móvel. O SAS é gerado utilizando uma política, que descreve as datas de início e de fim durante o qual a SAS é válida, bem como o nível de acesso concedido o marcador de posição SAS.

O exemplo seguinte gera uma nova política de acesso partilhado, o que permitirá o marcador de posição SAS para ('r') da tabela de consulta e expira 100 minutos após a hora que é criado.

```nodejs
var startDate = new Date();
var expiryDate = new Date(startDate);
expiryDate.setMinutes(startDate.getMinutes() + 100);
startDate.setMinutes(startDate.getMinutes() - 100);

var sharedAccessPolicy = {
  AccessPolicy: {
    Permissions: azure.TableUtilities.SharedAccessPermissions.QUERY,
    Start: startDate,
    Expiry: expiryDate
  },
};

var tableSAS = tableSvc.generateSharedAccessSignature('mytable', sharedAccessPolicy);
var host = tableSvc.host;
```

Tenha em atenção que as informações do anfitrião é necessário especificar também, conforme necessário, quando o marcador de posição SAS tenta aceder à tabela.

A aplicação de cliente, em seguida, utiliza a SAS com **TableServiceWithSAS** para executar operações na tabela. O exemplo seguinte liga-se à tabela e executa uma consulta.

```nodejs
var sharedTableService = azure.createTableServiceWithSas(host, tableSAS);
var query = azure.TableQuery()
  .where('PartitionKey eq ?', 'hometasks');

sharedTableService.queryEntities(query, null, function(error, result, response) {
  if(!error) {
    // result contains the entities
  }
});
```

Uma vez que a SAS foi gerada com acesso de consulta apenas, se foi efetuada uma tentativa de inserir, atualizar ou eliminar entidades, será devolvido um erro.

### <a name="access-control-lists"></a>Listas de controlo de acesso
Também pode utilizar uma lista de controlo de acesso (ACL) para definir a política de acesso para um SAS. Isto é útil se pretender permitir que os vários clientes aceder à tabela, mas fornece as políticas de acesso diferentes para cada cliente.

Uma ACL é implementada com uma matriz de políticas de acesso, com um ID associado a cada política. O exemplo seguinte define duas políticas, uma para 'user1' e outra para 'Utilizador2':

```nodejs
var sharedAccessPolicy = {
  user1: {
    Permissions: azure.TableUtilities.SharedAccessPermissions.QUERY,
    Start: startDate,
    Expiry: expiryDate
  },
  user2: {
    Permissions: azure.TableUtilities.SharedAccessPermissions.ADD,
    Start: startDate,
    Expiry: expiryDate
  }
};
```

O exemplo seguinte obtém a ACL atual para o **hometasks** tabela e, em seguida, adiciona as novas políticas utilizando **setTableAcl**. Esta abordagem permite:

```nodejs
var extend = require('extend');
tableSvc.getTableAcl('hometasks', function(error, result, response) {
if(!error){
    var newSignedIdentifiers = extend(true, result.signedIdentifiers, sharedAccessPolicy);
    tableSvc.setTableAcl('hometasks', newSignedIdentifiers, function(error, result, response){
      if(!error){
        // ACL set
      }
    });
  }
});
```

Assim que tiver sido definida a ACL, em seguida, pode criar um SAS com base no ID de uma política. O exemplo seguinte cria uma nova SAS para 'Utilizador2':

```nodejs
tableSAS = tableSvc.generateSharedAccessSignature('hometasks', { Id: 'user2' });
```

## <a name="next-steps"></a>Passos seguintes
Para obter mais informações, consulte os seguintes recursos.

* O [Explorador de Armazenamento do Microsoft Azure](../vs-azure-tools-storage-manage-with-storage-explorer.md) é uma aplicação autónoma e gratuita da Microsoft, que lhe permite trabalhar visualmente com dados do Armazenamento do Azure no Windows, macOS e Linux.
* [SDK de armazenamento do Azure para o nó](https://github.com/Azure/azure-storage-node) repositório no GitHub.
* [Centro para Programadores do Node.js](/develop/nodejs/)
* [Criar e implementar uma aplicação Node.js para um Web site do Azure](../app-service/app-service-web-get-started-nodejs.md)