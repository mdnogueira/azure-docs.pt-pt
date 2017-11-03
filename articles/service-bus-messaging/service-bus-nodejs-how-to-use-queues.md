---
title: Como utilizar filas do Service Bus no Node.js | Microsoft Docs
description: "Saiba como utilizar filas do Service Bus no Azure a partir de uma aplicação Node.js."
services: service-bus-messaging
documentationcenter: nodejs
author: sethmanheim
manager: timlt
editor: 
ms.assetid: a87a00f9-9aba-4c49-a0df-f900a8b67b3f
ms.service: service-bus-messaging
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: article
ms.date: 08/10/2017
ms.author: sethm
ms.openlocfilehash: 5b309534f7aef602610cfdb6aa784d180551e1ec
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-use-service-bus-queues-with-nodejs"></a>Como utilizar filas do Service Bus com o Node.js

[!INCLUDE [service-bus-selector-queues](../../includes/service-bus-selector-queues.md)]

Este artigo descreve como utilizar filas do Service Bus com o Node.js. Os exemplos são escritos em JavaScript e utilizam o módulo do Azure do Node.js. Os cenários abrangidos incluem **criar filas**, **enviar e receber mensagens**, e **eliminar filas**. Para obter mais informações sobre as filas, consulte o [passos](#next-steps) secção.

[!INCLUDE [howto-service-bus-queues](../../includes/howto-service-bus-queues.md)]

[!INCLUDE [service-bus-create-namespace-portal](../../includes/service-bus-create-namespace-portal.md)]

## <a name="create-a-nodejs-application"></a>Criar uma aplicação Node.js
Crie uma aplicação Node.js em branco. Para obter instruções sobre como criar uma aplicação Node.js, consulte [criar e implementar uma aplicação Node.js para um Web site Azure][Create and deploy a Node.js application to an Azure Website], ou [serviço em nuvem de Node.js] [ Node.js Cloud Service] através do Windows PowerShell.

## <a name="configure-your-application-to-use-service-bus"></a>Configurar a sua aplicação para utilizar o Service Bus
Para utilizar o Service Bus do Azure, transfira e utilize o pacote do Azure do Node.js. Este pacote inclui um conjunto de bibliotecas que comunicam com os serviços de REST do Service Bus.

### <a name="use-node-package-manager-npm-to-obtain-the-package"></a>Utilize o Gestor de pacote de nó (NPM) para obter o pacote
1. Utilize o **do Windows PowerShell para Node.js** janela de comandos para navegar para o **c:\\nó\\sbqueues\\WebRole1** pasta na qual criou a aplicação de exemplo.
2. Tipo **npm instalar azure** na janela de comando, que deve resultar no resultado semelhante ao seguinte:

    ```
    azure@0.7.5 node_modules\azure
        ├── dateformat@1.0.2-1.2.3
        ├── xmlbuilder@0.4.2
        ├── node-uuid@1.2.0
        ├── mime@1.2.9
        ├── underscore@1.4.4
        ├── validator@1.1.1
        ├── tunnel@0.0.2
        ├── wns@0.5.3
        ├── xml2js@0.2.7 (sax@0.5.2)
        └── request@2.21.0 (json-stringify-safe@4.0.0, forever-agent@0.5.0, aws-sign@0.3.0, tunnel-agent@0.3.0, oauth-sign@0.3.0, qs@0.6.5, cookie-jar@0.3.0, node-uuid@1.4.0, http-signature@0.9.11, form-data@0.0.8, hawk@0.13.1)
    ```
3. Pode executar manualmente o **ls** comando para verificar se um **node_modules** pasta foi criada. Dentro dessa pasta localizar o **azure** pacote, que contém as bibliotecas que precisa de aceder a filas do Service Bus.

### <a name="import-the-module"></a>Importe o módulo
Utilizar o bloco de notas ou noutro editor de texto, adicione o seguinte na parte superior do **server.js** ficheiro da aplicação:

```javascript
var azure = require('azure');
```

### <a name="set-up-an-azure-service-bus-connection"></a>Configurar uma ligação do Service Bus do Azure
O módulo do Azure lê a variável de ambiente `AZURE_SERVICEBUS_CONNECTION_STRING` para obter as informações necessárias para ligar ao Service Bus. Se esta variável de ambiente não for definida, tem de especificar as informações de conta ao chamar `createServiceBusService`.

Para obter um exemplo de definir as variáveis de ambiente num ficheiro de configuração para um serviço em nuvem do Azure, consulte [Node.js o serviço em nuvem com o armazenamento][Node.js Cloud Service with Storage].

Para obter um exemplo de definir as variáveis de ambiente no [portal do Azure] [ Azure portal] para um Web site do Azure, consulte [aplicação Web do Node.js com armazenamento][Node.js Web Application with Storage].

## <a name="create-a-queue"></a>Criar uma fila
O **ServiceBusService** objeto permite-lhe funcionar com as filas do Service Bus. O código seguinte cria um **ServiceBusService** objeto. Adicione-a junto ao topo do **server.js** ficheiro, após a declaração a importar o módulo do Azure:

```javascript
var serviceBusService = azure.createServiceBusService();
```

Ao chamar `createQueueIfNotExists` no **ServiceBusService** objeto, da fila especificada é devolvida (caso exista) ou é criada uma nova fila com o nome especificado. O seguinte código utiliza `createQueueIfNotExists` para criar ou ligue para a fila com o nome `myqueue`:

```javascript
serviceBusService.createQueueIfNotExists('myqueue', function(error){
    if(!error){
        // Queue exists
    }
});
```

O `createServiceBusService` método também suporta opções adicionais, que permitem-lhe substituir as definições predefinidas da fila, tais como a hora da mensagem para o tamanho da fila em direto ou máximo. O exemplo a seguir define o tamanho máximo da fila de 5 GB e um período de tempo (TTL) valor de 1 minuto de TTL:

```javascript
var queueOptions = {
      MaxSizeInMegabytes: '5120',
      DefaultMessageTimeToLive: 'PT1M'
    };

serviceBusService.createQueueIfNotExists('myqueue', queueOptions, function(error){
    if(!error){
        // Queue exists
    }
});
```

### <a name="filters"></a>Filtros
Operações de filtragem opcionais podem ser aplicadas a operações executadas utilizando **ServiceBusService**. Operações de filtragem pode incluir registo automaticamente repetir, etc. Os filtros são objetos que implementam um método com a assinatura:

```javascript
function handle (requestOptions, next)
```

Depois de efetuar o processamento de pré-lançamento nas opções de pedido, tem de chamar o método `next`, transmitir uma chamada de retorno com a assinatura seguinte:

```javascript
function (returnObject, finalCallback, next)
```

Esta chamada de retorno e após o processamento de `returnObject` (a resposta do pedido para o servidor), a chamada de retorno ou deve invocar `next` se existir para continuar a processar outros filtros, ou simplesmente invocar `finalCallback`, que termina a invocação de serviço.

Dois filtros que implementa lógica de repetição são incluídos com o Azure SDK para Node.js, `ExponentialRetryPolicyFilter` e `LinearRetryPolicyFilter`. O código seguinte cria um `ServiceBusService` objeto que utiliza o `ExponentialRetryPolicyFilter`:

```javascript
var retryOperations = new azure.ExponentialRetryPolicyFilter();
var serviceBusService = azure.createServiceBusService().withFilter(retryOperations);
```

## <a name="send-messages-to-a-queue"></a>Enviar mensagens para uma fila
Para enviar uma mensagem para uma fila do Service Bus, as chamadas de aplicação a `sendQueueMessage` método no **ServiceBusService** objeto. As mensagens enviadas para (e recebida do) Service Bus são filas **BrokeredMessage** objetos e têm um conjunto de propriedades padrão (tais como **etiqueta** e **TimeToLive**), um dicionário utilizado para reter propriedades personalizadas de específicas da aplicação e um corpo de dados arbitrários da aplicação. Uma aplicação pode definir o corpo da mensagem através da transmissão de uma cadeia que a mensagem. Quaisquer propriedades de padrão necessárias são preenchidas com valores predefinidos.

O exemplo seguinte demonstra como enviar uma mensagem de teste para a fila com o nome `myqueue` utilizando `sendQueueMessage`:

```javascript
var message = {
    body: 'Test message',
    customProperties: {
        testproperty: 'TestValue'
    }};
serviceBusService.sendQueueMessage('myqueue', message, function(error){
    if(!error){
        // message sent
    }
});
```

As filas do Service Bus suportam um tamanho da mensagem máximo de 256 KB no [escalão Padrão](service-bus-premium-messaging.md) e de 1 MB no [escalão Premium](service-bus-premium-messaging.md). O cabeçalho, que inclui as propriedades da aplicação padrão e personalizadas, pode ter um tamanho máximo de 64 KB. Não existe qualquer limite no número de mensagens contidas numa fila, contudo, existe um limite do tamanho total das mensagens contidas numa fila. O tamanho da fila é definido no momento de criação, com um limite superior de 5 GB. Para mais informações sobre as quotas, consulte [quotas de Service Bus][Service Bus quotas].

## <a name="receive-messages-from-a-queue"></a>Receber mensagens a partir de uma fila
As mensagens são recebidas a partir de uma fila utilizando o `receiveQueueMessage` método no **ServiceBusService** objeto. Por predefinição, as mensagens são eliminadas da fila, que são de leitura; No entanto, pode ler (peek) e bloquear a mensagem sem eliminá-lo a partir da fila, definindo o parâmetro opcional `isPeekLock` para **verdadeiro**.

O comportamento predefinido de ler e eliminar a mensagem como parte da operação de receção é o modelo mais simples e funciona melhor para cenários em que uma aplicação pode tolerar o não processamento de uma mensagem no caso de falha. Para compreender isto, considere um cenário em que o consumidor emite o pedido de receção e, em seguida, o sistema falha antes do respetivo processamento. Porque o Service Bus terá marcado a mensagem como consumida, em seguida, quando a aplicação reinicia e começa a consumir novamente mensagens, terá perdido a mensagem consumida antes da falha.

Se o `isPeekLock` parâmetro está definido como **verdadeiro**, a receção torna-se uma operação de duas fases, o que possibilita o suporte de aplicações que não toleram mensagens em falta. Quando o Service Bus recebe um pedido, localiza a mensagem seguinte a ser consumida, bloqueia-a para impedir a respetiva receção por outros consumidores e, em seguida, devolve a mesma à aplicação. Após a aplicação concluir o processamento da mensagem (ou armazena a mesma forma fiável para processamento futuro), conclui a segunda etapa do processo de receção ao chamar `deleteMessage` método e fornecer a mensagem ser eliminadas como parâmetro. O `deleteMessage` método marca a mensagem como consumida e remove-o da fila.

O exemplo seguinte demonstra como recebe e processa mensagens utilizando `receiveQueueMessage`. O exemplo recebe pela primeira vez e elimina uma mensagem e, em seguida, recebe uma mensagem através de `isPeekLock` definido como **verdadeiro**, em seguida, elimina a mensagem utilizando `deleteMessage`:

```javascript
serviceBusService.receiveQueueMessage('myqueue', function(error, receivedMessage){
    if(!error){
        // Message received and deleted
    }
});
serviceBusService.receiveQueueMessage('myqueue', { isPeekLock: true }, function(error, lockedMessage){
    if(!error){
        // Message received and locked
        serviceBusService.deleteMessage(lockedMessage, function (deleteError){
            if(!deleteError){
                // Message deleted
            }
        });
    }
});
```

## <a name="how-to-handle-application-crashes-and-unreadable-messages"></a>Como processar falhas da aplicação e mensagens ilegíveis
O Service Bus fornece funcionalidades para ajudar a recuperar corretamente de erros na sua aplicação ou problemas no processamento de uma mensagem. Se uma aplicação recetora não é possível processar a mensagem por algum motivo, em seguida, pode chamar o `unlockMessage` método no **ServiceBusService** objeto. Isto irá fazer com que o Service Bus desbloqueie a mensagem na fila e disponibilizá-lo ser novamente recebida, pela mesma aplicação de consumo ou por outra aplicação de consumo.

Existe também um tempo limite associado à mensagem bloqueada na fila, e se a aplicação não conseguir processar a mensagem antes do tempo limite de bloqueio expira (por exemplo, se a falha da aplicação), o barramento de serviço irá desbloquear automaticamente a mensagem e disponibilizá-lo ser recebida novamente.

No caso de falha da aplicação após o processamento da mensagem, mas antes o `deleteMessage` método é denominado, em seguida, a mensagem será reenviada para a aplicação quando esta reiniciar. Isto é frequentemente designado *, pelo menos, uma vez processamento*, ou seja, cada mensagem será processada, pelo menos, uma vez, mas em determinadas situações a mesma mensagem poderá ser reenviada. Se o cenário não conseguir tolerar o processamento duplicado, os programadores da aplicação devem acrescentar uma lógica adicional à aplicação para processar a entrega da mensagem duplicada. Isto é, frequentemente, conseguido utilizando o **MessageId** propriedade da mensagem, que permanecerá constante nas tentativas de entrega.

## <a name="next-steps"></a>Passos seguintes
Para saber mais sobre as filas, consulte os seguintes recursos.

* [Filas, tópicos e subscrições][Queues, topics, and subscriptions]
* [Azure SDK para o nó] [ Azure SDK for Node] repositório no GitHub
* [Centro para Programadores do Node.js](https://azure.microsoft.com/develop/nodejs/)

[Azure SDK for Node]: https://github.com/Azure/azure-sdk-for-node
[Azure portal]: https://portal.azure.com

[Node.js Cloud Service]: ../cloud-services/cloud-services-nodejs-develop-deploy-app.md
[Queues, topics, and subscriptions]: service-bus-queues-topics-subscriptions.md
[Create and deploy a Node.js application to an Azure Website]: ../app-service/app-service-web-get-started-nodejs.md
[Node.js Cloud Service with Storage]:../cosmos-db/table-storage-cloud-service-nodejs.md
[Node.js Web Application with Storage]:../cosmos-db/table-storage-how-to-use-nodejs.md
[Service Bus quotas]: service-bus-quotas.md
