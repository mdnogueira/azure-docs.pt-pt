---
title: "Introdução ao armazenamento de filas e o Visual Studio ligado serviços (ASP.NET Core) | Microsoft Docs"
description: "Como começar a utilizar o armazenamento de filas do Azure num projeto ASP.NET Core no Visual Studio"
services: storage
documentationcenter: 
author: kraigb
manager: ghogen
editor: 
ms.assetid: 04977069-5b2d-4cba-84ae-9fb2f5eb1006
ms.service: storage
ms.workload: web
ms.tgt_pltfrm: vs-getting-started
ms.devlang: na
ms.topic: article
ms.date: 11/14/2017
ms.author: kraigb
ms.openlocfilehash: d83ad26e4f96e1a7670c5212b7e9ca8182b7cec4
ms.sourcegitcommit: f847fcbf7f89405c1e2d327702cbd3f2399c4bc2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/28/2017
---
# <a name="get-started-with-queue-storage-and-visual-studio-connected-services-aspnet-core"></a>Introdução ao armazenamento de filas e o Visual Studio ligado serviços (ASP.NET Core)

[!INCLUDE [storage-try-azure-tools-queues](../../includes/storage-try-azure-tools-queues.md)]

Este artigo descreve como começar a utilizar o armazenamento de filas do Azure no Visual Studio depois de ter criado ou referenciada uma conta de armazenamento do Azure num projeto ASP.NET Core utilizando o Visual Studio **serviços ligados** funcionalidade. O **serviços ligados** operação instala os pacotes de NuGet adequados para aceder ao armazenamento do Azure no seu projeto e adiciona a cadeia de ligação para a conta de armazenamento para ficheiros de configuração do projeto. (Consulte [documentação do Storage](https://azure.microsoft.com/documentation/services/storage/) para obter informações gerais sobre o Storage do Azure.)

Armazenamento de filas do Azure é um serviço para armazenar grandes quantidades de mensagens que podem ser acedidas de qualquer local no mundo através de chamadas autenticadas com HTTP ou HTTPS. Uma mensagem de fila única pode ser até 64 quilobytes (KB) e uma fila pode conter milhões de mensagens, até ao limite de capacidade total de uma conta de armazenamento. Consulte também [introdução ao armazenamento de filas do Azure através do .NET](../storage/queues/storage-dotnet-how-to-use-queues.md) para obter detalhes sobre manipular programaticamente filas.

Para começar a utilizar, crie uma fila do Azure na sua conta de armazenamento. Este artigo, em seguida, mostra como criar uma fila em c# e como realizar operações de fila básica, tais como adicionar, modificar, ler e remover mensagens da fila.  O código utiliza a biblioteca de clientes de armazenamento do Azure para .NET. Para mais informações sobre o ASP.NET, consulte [ASP.NET](http://www.asp.net).

Algumas das APIs de armazenamento do Azure são assíncronas e o código neste artigo parte do princípio de métodos assíncronos estão a ser utilizados. Consulte [programação assíncrona](https://docs.microsoft.com/dotnet/csharp/async) para obter mais informações.

## <a name="access-queues-in-code"></a>Aceder a filas no código

Para aceder a filas em projetos de ASP.NET Core, incluem os seguintes itens no qualquer origem ficheiro c# que acede ao armazenamento de filas do Azure. Utilize todo este código à frente o código de secções que se seguem.

1. Adicionar o necessário `using` instruções:
    ```cs
    using Microsoft.Framework.Configuration;
    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Queue;
    using System.Threading.Tasks;
    using LogLevel = Microsoft.Framework.Logging.LogLevel;
    ```

1. Obter um `CloudStorageAccount` objeto que representa as informações da conta de armazenamento. Utilize o seguinte código para obter a cadeia de ligação de armazenamento e informações de conta de armazenamento da configuração do serviço do Azure:

    ```cs
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```

1. Obter um `CloudQueueClient` objeto referenciar os objetos de fila na sua conta de armazenamento:

    ```cs
    // Create the CloudQueueClient object for the storage account.
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();
    ```
1. Obter um `CloudQueue` objeto para fazer referência a uma fila específica:

    ```cs
    // Get a reference to the CloudQueue named "messagequeue"
    CloudQueue messageQueue = queueClient.GetQueueReference("messagequeue");
    ```

### <a name="create-a-queue-in-code"></a>Criar uma fila no código

Para criar a fila do Azure no código, chamar ' CreateIfNotExistsAsync':

```cs
// Create the CloudQueue if it does not exist.
await messageQueue.CreateIfNotExistsAsync();
```

## <a name="add-a-message-to-a-queue"></a>Adicionar uma mensagem para uma fila

Para introduzir uma mensagem numa fila existente, crie um novo `CloudQueueMessage` objeto, em seguida, invoque o `AddMessageAsync` método. A `CloudQueueMessage` objeto pode ser criado a partir de uma cadeia (no formato UTF-8) ou uma matriz de bytes.

```cs
// Create a message and add it to the queue.
CloudQueueMessage message = new CloudQueueMessage("Hello, World");
await messageQueue.AddMessageAsync(message);
```

## <a name="read-a-message-in-a-queue"></a>Ler uma mensagem numa fila

Pode pré-visualizar a mensagem no início de uma fila sem a remover da fila ao chamar o `PeekMessageAsync` método:

```cs
// Peek the next message in the queue.
CloudQueueMessage peekedMessage = await messageQueue.PeekMessageAsync();
```

## <a name="read-and-remove-a-message-in-a-queue"></a>Ler e remover uma mensagem numa fila

Pode remover o seu código (anular) uma mensagem da fila em dois passos.

1. Chamar `GetMessageAsync` para obterá a seguinte mensagem numa fila. Uma mensagem devolvida por `GetMessageAsync` torna-se invisível para quaisquer outras código ao ler as mensagens desta fila. Por predefinição, esta mensagem permanece invisível durante 30 segundos.
1. Para acabar de remover a mensagem da fila, chame `DeleteMessageAsync`.

Este processo de dois passos da remoção de uma mensagem garante que se o código não conseguir processar uma mensagem devido a uma falha de hardware ou software, outra instância do seu código poderá obter a mesma mensagem e tentar novamente. O seguinte código chamadas `DeleteMessageAsync` imediatamente após a mensagem ser processada:

```cs
// Get the next message in the queue.
CloudQueueMessage retrievedMessage = await messageQueue.GetMessageAsync();

// Process the message in less than 30 seconds.

// Then delete the message.
await messageQueue.DeleteMessageAsync(retrievedMessage);
```

## <a name="additional-options-for-dequeuing-messages"></a>Opções adicionais para mensagens dequeuing

Existem duas formas de personalizar a obtenção de mensagens numa fila. Em primeiro lugar, pode obter um lote de mensagens (até 32). Em segundo lugar, pode definir um tempo limite de invisibilidade superior ou inferior, dando mais ou menos tempo ao código para processar totalmente cada mensagem. O seguinte código de exemplo utiliza o `GetMessages` método para obter 20 mensagens numa chamada. Em seguida, processa cada mensagem utilizando um `foreach` ciclo. Define também o tempo limite de invisibilidade para cinco minutos para cada mensagem. Tenha em atenção que a cinco minutos temporizador é iniciado para todas as mensagens ao mesmo tempo, após ter passado cinco minutos, as mensagens que não tenham sido eliminadas ficam visíveis novamente.

```cs
// Retrieve 20 messages at a time, keeping those messages invisible for 5 minutes, 
//   delete each message after processing.

foreach (CloudQueueMessage message in messageQueue.GetMessages(20, TimeSpan.FromMinutes(5)))
{
    // Process all messages in less than 5 minutes, deleting each message after processing.
    queue.DeleteMessage(message);
}
```

## <a name="get-the-queue-length"></a>Obter o comprimento da fila

Pode obter uma estimativa do número de mensagens numa fila. O `FetchAttributes` método pede ao serviço de fila para obter os atributos de fila, incluindo a contagem de mensagens. O `ApproximateMethodCount` propriedade devolve o último valor obtido pelo `FetchAttributes` método, sem chamar o serviço fila.

```cs
// Fetch the queue attributes.
messageQueue.FetchAttributes();

// Retrieve the cached approximate message count.
int? cachedMessageCount = messageQueue.ApproximateMessageCount;

// Display the number of messages.
Console.WriteLine("Number of messages in queue: " + cachedMessageCount);
```

## <a name="use-the-async-await-pattern-with-common-queue-apis"></a>Utilizar o padrão Async-Await com APIs de fila comum

Este exemplo mostra como utilizar o async-await padrão com comuns fila APIs terminando `Async`. Quando é utilizado um método assíncrono, o async-await padrão suspende a execução local até que a chamada ser concluída. Este comportamento permite que o thread atual efetue outro trabalho que ajuda a evitar congestionamentos de desempenho e melhora a capacidade de resposta global da sua aplicação.

```cs
// Create a message to add to the queue.
CloudQueueMessage cloudQueueMessage = new CloudQueueMessage("My message");

// Async enqueue the message.
await messageQueue.AddMessageAsync(cloudQueueMessage);
Console.WriteLine("Message added");

// Async dequeue the message.
CloudQueueMessage retrievedMessage = await messageQueue.GetMessageAsync();
Console.WriteLine("Retrieved message with content '{0}'", retrievedMessage.AsString);

// Async delete the message.
await messageQueue.DeleteMessageAsync(retrievedMessage);
Console.WriteLine("Deleted message");
```

## <a name="delete-a-queue"></a>Eliminar uma fila

Para eliminar uma fila e todas as mensagens nela contidas, chame o `Delete` método no objeto de fila:

```cs
// Delete the queue.
messageQueue.Delete();
```

## <a name="next-steps"></a>Passos seguintes

[!INCLUDE [vs-storage-dotnet-queues-next-steps](../../includes/vs-storage-dotnet-queues-next-steps.md)]
