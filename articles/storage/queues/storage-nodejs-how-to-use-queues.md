---
title: Como utilizar o armazenamento de filas do Node.js | Microsoft Docs
description: "Saiba como utilizar o serviço de fila do Azure para criar e eliminar filas, inserir, obter e eliminar as mensagens. Amostras de escrita no Node.js."
services: storage
documentationcenter: nodejs
author: tamram
manager: timlt
editor: tysonn
ms.assetid: a8a92db0-4333-43dd-a116-28b3147ea401
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: article
ms.date: 12/08/2016
ms.author: tamram
ms.openlocfilehash: ce61687e0f64b5971daca85265ef0e7d2b6f43b8
ms.sourcegitcommit: 2d1153d625a7318d7b12a6493f5a2122a16052e0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/20/2017
---
# <a name="how-to-use-queue-storage-from-nodejs"></a>Como utilizar o Armazenamento de filas do Node.js
[!INCLUDE [storage-selector-queue-include](../../../includes/storage-selector-queue-include.md)]

[!INCLUDE [storage-check-out-samples-all](../../../includes/storage-check-out-samples-all.md)]

## <a name="overview"></a>Descrição geral
Este guia mostra como efetuar cenários comuns utilizando o serviço de fila do Microsoft Azure. Os exemplos são escritos utilizando a API do Node.js. Os cenários abrangidos incluem **inserir**, **leitura**, **obter**, e **eliminar** fila de mensagens, bem como **criar e eliminar filas**.

[!INCLUDE [storage-queue-concepts-include](../../../includes/storage-queue-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../../includes/storage-create-account-include.md)]

## <a name="create-a-nodejs-application"></a>Criar uma aplicação Node.js
Crie uma aplicação Node.js em branco. Para obter instruções sobre como criar uma aplicação Node.js, consulte [criar uma aplicação web Node.js no App Service do Azure](../../app-service/app-service-web-get-started-nodejs.md), [criar e implementar uma aplicação Node.js num serviço em nuvem do Azure](../../cloud-services/cloud-services-nodejs-develop-deploy-app.md) através do Windows PowerShell, ou [criar e implementar uma aplicação web Node.js no Azure utilizando a Web Matrix](https://www.microsoft.com/web/webmatrix/).

## <a name="configure-your-application-to-access-storage"></a>Configurar a sua aplicação para aceder ao armazenamento
Para utilizar o armazenamento do Azure, terá do SDK de armazenamento do Azure para Node.js, que inclui um conjunto de bibliotecas de conveniência que comunicam com os serviços REST do storage.

### <a name="use-node-package-manager-npm-to-obtain-the-package"></a>Utilize o Gestor de pacote de nó (NPM) para obter o pacote
1. Utilize uma interface de linha de comandos, como **PowerShell** (Windows), **Terminal** (Mac), ou **Bash** (Unix), navegue para a pasta onde criou o seu exemplo de aplicação.
2. Tipo **npm instalar storage do azure** na janela de comandos. Saída do comando é semelhante ao seguinte exemplo.
 
    ```
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
    ```

3. Pode executar manualmente o **ls** comando para verificar se um **nó\_módulos** pasta foi criada. Dentro dessa pasta, encontrará a **storage do azure** pacote, que contém as bibliotecas que precisa de aceder ao armazenamento.

### <a name="import-the-package"></a>Importar o pacote
Utilizar o bloco de notas ou noutro editor de texto, adicione o seguinte na parte superior do **server.js** ficheiro da aplicação em que pretende utilizar o armazenamento:

```
var azure = require('azure-storage');
```

## <a name="setup-an-azure-storage-connection"></a>Configurar uma ligação de armazenamento do Azure
O módulo do azure irá ler as variáveis de ambiente do AZURE\_armazenamento\_conta e o AZURE\_armazenamento\_acesso\_chave ou o AZURE\_armazenamento\_ligação\_cadeia de informações necessárias para ligar à sua conta de armazenamento do Azure. Se estas variáveis de ambiente não estiver definidas, tem de especificar as informações de conta ao chamar **createQueueService**.

Para obter um exemplo de definir as variáveis de ambiente no [Portal do Azure](https://portal.azure.com) para um Web site do Azure, consulte [aplicação de web de Node.js utilizando o serviço de tabela do Azure].

## <a name="how-to-create-a-queue"></a>Como: Criar uma fila
O código seguinte cria um **QueueService** objeto, que lhe permite trabalhar com as filas.

```
var queueSvc = azure.createQueueService();
```

Utilize o **createQueueIfNotExists** método, que devolve a fila especificada, se já existe ou cria uma nova fila com o nome especificado se já existir.

```
queueSvc.createQueueIfNotExists('myqueue', function(error, result, response){
  if(!error){
    // Queue created or exists
  }
});
```

Se a fila é criada, `result.created` é verdadeiro. Se a fila existe, `result.created` é falso.

### <a name="filters"></a>Filtros
Operações de filtragem opcionais podem ser aplicadas a operações executadas utilizando **QueueService**. Operações de filtragem pode incluir registo automaticamente repetir, etc. Os filtros são objetos que implementam um método com a assinatura:

```
function handle (requestOptions, next)
```

Depois de efetuar a respetiva pré-processamentos nas opções de pedido, o método tem de chamar "seguinte" transmitir uma chamada de retorno com a assinatura seguinte:

```
function (returnObject, finalCallback, next)
```

Esta chamada de retorno e após o processamento de returnObject (a resposta do pedido para o servidor), a chamada de retorno tem de invocar, em seguida, se existir para continuar a processar outros filtros ou simplesmente invocar finalCallback caso contrário, para ficar a invocação de serviço.

Dois filtros que implementa lógica de repetição são incluídos com o Azure SDK para Node.js, **ExponentialRetryPolicyFilter** e **LinearRetryPolicyFilter**. O seguinte cria um **QueueService** objeto que utiliza o **ExponentialRetryPolicyFilter**:

```
var retryOperations = new azure.ExponentialRetryPolicyFilter();
var queueSvc = azure.createQueueService().withFilter(retryOperations);
```

## <a name="how-to-insert-a-message-into-a-queue"></a>Como: Introduzir uma mensagem para uma fila
Para introduzir uma mensagem para uma fila, utilize o **createMessage** método para criar uma nova mensagem e adicioná-lo para a fila.

```
queueSvc.createMessage('myqueue', "Hello world!", function(error, result, response){
  if(!error){
    // Message inserted
  }
});
```

## <a name="how-to-peek-at-the-next-message"></a>Como: Pré-visualizar a mensagem seguinte
Pode pré-visualizar a mensagem no início de uma fila sem a remover da fila ao chamar o **peekMessages** método. Por predefinição, **peekMessages** observa uma única mensagem.

```
queueSvc.peekMessages('myqueue', function(error, result, response){
  if(!error){
    // Message text is in messages[0].messageText
  }
});
```

O `result` contém a mensagem.

> [!NOTE]
> Utilizar **peekMessages** quando existem não existem mensagens na fila não devolverá um erro, no entanto, não existem mensagens vai ser devolvidas.
> 
> 

## <a name="how-to-dequeue-the-next-message"></a>Como: Anular a mensagem seguinte
A processar uma mensagem é um processo de duas fases:

1. Anular a mensagem.
2. Elimine a mensagem.

Para anular uma mensagem, utilize **getMessages**. Isto faz com que as mensagens invisível da fila, pelo que não existem outros clientes podem processá-los. Assim que a aplicação tem de processar uma mensagem, chamar **deleteMessage** eliminá-la a partir da fila. O exemplo seguinte obtém uma mensagem, em seguida, elimina-a:

```
queueSvc.getMessages('myqueue', function(error, result, response){
  if(!error){
    // Message text is in messages[0].messageText
    var message = result[0];
    queueSvc.deleteMessage('myqueue', message.messageId, message.popReceipt, function(error, response){
      if(!error){
        //message deleted
      }
    });
  }
});
```

> [!NOTE]
> Por predefinição, uma mensagem apenas está oculto para 30 segundos, após o qual é visível para outros clientes. Pode especificar um valor diferente utilizando `options.visibilityTimeout` com **getMessages**.
> 
> [!NOTE]
> Utilizar **getMessages** quando existem não existem mensagens na fila não devolverá um erro, no entanto, não existem mensagens vai ser devolvidas.
> 
> 

## <a name="how-to-change-the-contents-of-a-queued-message"></a>Como: Alterar os conteúdos de uma mensagem em fila
Pode alterar os conteúdos de uma mensagem no local na fila com **updateMessage**. O exemplo seguinte atualiza o texto da mensagem:

```
queueSvc.getMessages('myqueue', function(error, result, response){
  if(!error){
    // Got the message
    var message = result[0];
    queueSvc.updateMessage('myqueue', message.messageId, message.popReceipt, 10, {messageText: 'new text'}, function(error, result, response){
      if(!error){
        // Message updated successfully
      }
    });
  }
});
```

## <a name="how-to-additional-options-for-dequeuing-messages"></a>Como: Mensagens de opções adicionais para Dequeuing
Existem duas formas que pode personalizar a obtenção de mensagens numa fila:

* `options.numOfMessages`-Obter um lote de mensagens (até 32).
* `options.visibilityTimeout`-Defina um tempo de limite de invisibilidade superiores ou inferiores.

O exemplo seguinte utiliza o **getMessages** método para obter 15 mensagens numa chamada. Em seguida, processa cada mensagem utilizando um ciclo for. Também define o tempo limite de invisibilidade para cinco minutos para todas as mensagens devolvidos por este método.

```
queueSvc.getMessages('myqueue', {numOfMessages: 15, visibilityTimeout: 5 * 60}, function(error, result, response){
  if(!error){
    // Messages retreived
    for(var index in result){
      // text is available in result[index].messageText
      var message = result[index];
      queueSvc.deleteMessage(queueName, message.messageId, message.popReceipt, function(error, response){
        if(!error){
          // Message deleted
        }
      });
    }
  }
});
```

## <a name="how-to-get-the-queue-length"></a>Como: Obter o comprimento da fila
O **getQueueMetadata** devolve metadados sobre a fila, incluindo o número aproximado de mensagens a aguardar na fila.

```
queueSvc.getQueueMetadata('myqueue', function(error, result, response){
  if(!error){
    // Queue length is available in result.approximateMessageCount
  }
});
```

## <a name="how-to-list-queues"></a>Como: Filas de lista
Para obter uma lista de filas, utilize **listQueuesSegmented**. Para obter uma lista filtrada por um prefixo específico, utilize **listQueuesSegmentedWithPrefix**.

```
queueSvc.listQueuesSegmented(null, function(error, result, response){
  if(!error){
    // result.entries contains the list of queues
  }
});
```

Se não podem ser devolvidas todas as filas, `result.continuationToken` pode ser utilizado como o primeiro parâmetro do **listQueuesSegmented** ou o segundo parâmetro de **listQueuesSegmentedWithPrefix** para obter mais resultados.

## <a name="how-to-delete-a-queue"></a>Como: Eliminar uma fila
Para eliminar uma fila e todas as mensagens nela contidas, chame o **deleteQueue** método no objeto de fila.

```
queueSvc.deleteQueue(queueName, function(error, response){
  if(!error){
    // Queue has been deleted
  }
});
```

Para limpar todas as mensagens de uma fila sem eliminá-lo, utilize **clearMessages**.

## <a name="how-to-work-with-shared-access-signatures"></a>Como: trabalhar com assinaturas de acesso partilhado
Assinaturas de acesso partilhado (SAS) são uma forma segura para fornecer acesso granular filas sem fornecer o nome da conta de armazenamento ou chaves. SAS são frequentemente utilizado para fornecer acesso limitado a filas, tais como permitir que uma aplicação móvel submeter mensagens.

Uma aplicação como um serviço baseado na nuvem fidedigna gera uma SAS utilizando o **generateSharedAccessSignature** do **QueueService**e fornece-o a uma aplicação não fidedigna ou por fidedigna. Por exemplo, uma aplicação móvel. O SAS é gerado utilizando uma política, que descreve as datas de início e de fim durante o qual a SAS é válida, bem como o nível de acesso concedido o marcador de posição SAS.

O exemplo seguinte gera uma nova política de acesso partilhado, o que permitirá o marcador de posição SAS adicionar mensagens para a fila e expira 100 minutos após a hora que é criado.

```
var startDate = new Date();
var expiryDate = new Date(startDate);
expiryDate.setMinutes(startDate.getMinutes() + 100);
startDate.setMinutes(startDate.getMinutes() - 100);

var sharedAccessPolicy = {
  AccessPolicy: {
    Permissions: azure.QueueUtilities.SharedAccessPermissions.ADD,
    Start: startDate,
    Expiry: expiryDate
  }
};

var queueSAS = queueSvc.generateSharedAccessSignature('myqueue', sharedAccessPolicy);
var host = queueSvc.host;
```

Tenha em atenção que as informações do anfitrião é necessário especificar também, conforme necessário, quando o marcador de posição SAS tenta aceder a fila.

A aplicação de cliente, em seguida, utiliza a SAS com **QueueServiceWithSAS** para efetuar operações em relação a fila. O exemplo seguinte estabelece ligação à fila e cria uma mensagem.

```
var sharedQueueService = azure.createQueueServiceWithSas(host, queueSAS);
sharedQueueService.createMessage('myqueue', 'Hello world from SAS!', function(error, result, response){
  if(!error){
    //message added
  }
});
```

Uma vez que a SAS foi gerada com adicionar acesso, se foi efetuada uma tentativa de ler, atualizar ou eliminar as mensagens, será devolvido um erro.

### <a name="access-control-lists"></a>Lista de controlo de acesso
Também pode utilizar uma lista de controlo de acesso (ACL) para definir a política de acesso para um SAS. Isto é útil se pretender permitir que os vários clientes aceder a fila, mas fornece as políticas de acesso diferentes para cada cliente.

Uma ACL é implementada com uma matriz de políticas de acesso, com um ID associado a cada política. O exemplo seguinte define duas políticas; um para 'user1' e outra para 'Utilizador2':

```
var sharedAccessPolicy = {
  user1: {
    Permissions: azure.QueueUtilities.SharedAccessPermissions.PROCESS,
    Start: startDate,
    Expiry: expiryDate
  },
  user2: {
    Permissions: azure.QueueUtilities.SharedAccessPermissions.ADD,
    Start: startDate,
    Expiry: expiryDate
  }
};
```

O exemplo seguinte obtém a ACL atual para **myqueue**, em seguida, adiciona as novas políticas utilizando **setQueueAcl**. Esta abordagem permite:

```
var extend = require('extend');
queueSvc.getQueueAcl('myqueue', function(error, result, response) {
  if(!error){
    var newSignedIdentifiers = extend(true, result.signedIdentifiers, sharedAccessPolicy);
    queueSvc.setQueueAcl('myqueue', newSignedIdentifiers, function(error, result, response){
      if(!error){
        // ACL set
      }
    });
  }
});
```

Assim que tiver sido definida a ACL, em seguida, pode criar um SAS com base no ID de uma política. O exemplo seguinte cria uma nova SAS para 'Utilizador2':

```
queueSAS = queueSvc.generateSharedAccessSignature('myqueue', { Id: 'user2' });
```

## <a name="next-steps"></a>Passos Seguintes
Agora que aprendeu as noções básicas do armazenamento de filas, siga estas ligações para saber mais sobre as tarefas de armazenamento mais complexas.

* Visite o [blogue da equipa do Storage do Azure][Azure Storage Team Blog].
* Visite o [SDK de armazenamento do Azure para o nó] [ Azure Storage SDK for Node] repositório no GitHub.



[Azure Storage SDK for Node]: https://github.com/Azure/azure-storage-node

[using the REST API]: http://msdn.microsoft.com/library/azure/hh264518.aspx

[Azure Portal]: https://portal.azure.com

[Criar uma aplicação web Node.js no App Service do Azure](../../app-service/app-service-web-get-started-nodejs.md)

[Compilar e implementar uma aplicação Node.js num Serviço Cloud do Azure](../../cloud-services/cloud-services-nodejs-develop-deploy-app.md)

[Azure Storage Team Blog]: http://blogs.msdn.com/b/windowsazurestorage/

[Build and deploy a Node.js web app to Azure using Web Matrix]: https://www.microsoft.com/web/webmatrix/
