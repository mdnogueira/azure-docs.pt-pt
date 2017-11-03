---
title: "Introdução ao armazenamento de filas do Azure e o Visual Studio ligado serviços (ASP.NET) | Microsoft Docs"
description: "Como começar a utilizar o armazenamento de filas do Azure num projeto ASP.NET no Visual Studio depois de se ligar a uma conta de armazenamento utilizando o Visual Studio ligado Services"
services: storage
documentationcenter: 
author: kraigb
manager: ghogen
editor: 
ms.assetid: 94ca3413-5497-433f-abbe-836f83a9de72
ms.service: storage
ms.workload: web
ms.tgt_pltfrm: vs-getting-started
ms.devlang: na
ms.topic: article
ms.date: 12/23/2016
ms.author: kraigb
ms.openlocfilehash: 4687e5dfce72583728068c176d86d100313badf6
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="get-started-with-azure-queue-storage-and-visual-studio-connected-services-aspnet"></a>Introdução ao armazenamento de filas do Azure e o Visual Studio ligado serviços (ASP.NET)
[!INCLUDE [storage-try-azure-tools-queues](../../includes/storage-try-azure-tools-queues.md)]

## <a name="overview"></a>Descrição geral

Armazenamento de filas do Azure fornece processamento de mensagens entre componentes da aplicação de nuvem. Ao conceber aplicações para o dimensionamento, os componentes da aplicação, muitas vezes, são desacoplados para um dimensionamento independente. O Armazenamento de filas fornece um serviço de mensagens assíncrono para uma comunicação entre os componentes da aplicação, quer estejam a ser executados na nuvem, no ambiente de trabalho, num servidor no local ou num dispositivo móvel. O Armazenamento de filas também suporta a gestão das tarefas assíncronas e a criação de fluxos de trabalho do processo.

Este tutorial mostra como escrever código ASP.NET para alguns cenários comuns utilizando entidades de armazenamento de filas do Azure. Estes cenários incluem tarefas comuns, tais como criar uma fila do Azure, adicionar, modificar, ler e remoção de fila de mensagens.

##<a name="prerequisites"></a>Pré-requisitos

* [Microsoft Visual Studio](https://www.visualstudio.com/downloads/)
* [Conta de armazenamento do Azure](../storage/common/storage-create-storage-account.md#create-a-storage-account)

[!INCLUDE [storage-queue-concepts-include](../../includes/storage-queue-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../includes/vs-storage-aspnet-getting-started-create-azure-account.md)]

[!INCLUDE [storage-development-environment-include](../../includes/vs-storage-aspnet-getting-started-setup-dev-env.md)]

### <a name="create-an-mvc-controller"></a>Criar um controlador MVC 

1. No **Explorador de soluções**, faça duplo clique **controladores**e, no menu de contexto, selecione **adicionar -> controlador**.

    ![Adicionar um controlador a uma aplicação ASP.NET MVC](./media/vs-storage-aspnet-getting-started-queues/add-controller-menu.png)

1. No **adicionar andaime** caixa de diálogo, selecione **controlador MVC 5 – vazio**e selecione **adicionar**.

    ![Especifique o tipo de controlador MVC](./media/vs-storage-aspnet-getting-started-queues/add-controller.png)

1. No **Adicionar controlador** caixa de diálogo, o nome do controlador *QueuesController*e selecione **adicionar**.

    ![Nome do controlador MVC](./media/vs-storage-aspnet-getting-started-queues/add-controller-name.png)

1. Adicione o seguinte *utilizando* diretivas para a `QueuesController.cs` ficheiro:

    ```csharp
    using Microsoft.Azure;
    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Auth;
    using Microsoft.WindowsAzure.Storage.Queue;
    ```
## <a name="create-a-queue"></a>Criar uma fila

Os passos seguintes mostram como criar uma fila:

> [!NOTE]
> 
> Esta secção assume que concluiu as fases [configurar o ambiente de desenvolvimento](#set-up-the-development-environment). 

1. Abra o ficheiro `QueuesController.cs`. 

1. Adicione um método denominado **CreateQueue** que devolve um **ActionResult**.

    ```csharp
    public ActionResult CreateQueue()
    {
        // The code in this section goes here.

        return View();
    }
    ```

1. Dentro do **CreateQueue** método, obter um **CloudStorageAccount** objeto que representa as informações da conta de armazenamento. Utilize o seguinte código para obter a cadeia de ligação de armazenamento e informações de conta de armazenamento da configuração do serviço do Azure: (alteração  *&lt;nome da conta de armazenamento >* para o nome da conta do storage do Azure que está a aceder.)
   
    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
       CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```

1. Obter um **CloudQueueClient** objeto representa um cliente do serviço fila.
   
    ```csharp
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();
    ```
1. Obter um **CloudQueue** objeto que representa uma referência para o nome da fila pretendida. O **CloudQueueClient.GetQueueReference** método não faz um pedido de armazenamento de filas. A referência é devolvida se ou não a fila existe. 
   
    ```csharp
    CloudQueue queue = queueClient.GetQueueReference("test-queue");
    ```

1. Chamar o **CloudQueue.CreateIfNotExists** método para criar a fila se ainda não existir. O **CloudQueue.CreateIfNotExists** método devolve **verdadeiro** se a fila existe e é criada com êxito. Caso contrário, **falso** é devolvido.    

    ```csharp
    ViewBag.Success = queue.CreateIfNotExists();
    ```

1. Atualização do **ViewBag** com o nome da fila.

    ```csharp
    ViewBag.QueueName = queue.Name;
    ```

1. No **Explorador de soluções**, expanda o **vistas** pasta, faça duplo clique **filas**e no menu de contexto, selecione **adicionar -> vista**.

1. No **Adicionar vista** caixa de diálogo, introduza **CreateQueue** para o nome da vista e selecione **adicionar**.

1. Abra `CreateQueue.cshtml`e modificá-lo de modo a que se parece com o seguinte fragmento de código:

    ```csharp
    @{
        ViewBag.Title = "Create Queue";
    }
    
    <h2>Create Queue results</h2>

    Creation of @ViewBag.QueueName @(ViewBag.Success == true ? "succeeded" : "failed")
    ```

1. No **Explorador de soluções**, expanda o **vistas -> partilhados** pasta e abra `_Layout.cshtml`.

1. Após a última **Html.ActionLink**, adicione o seguinte **Html.ActionLink**:

    ```html
    <li>@Html.ActionLink("Create queue", "CreateQueue", "Queues")</li>
    ```

1. Executar a aplicação e selecione **criar fila** para ver os resultados semelhantes à captura de ecrã seguinte:
  
    ![Criar fila](./media/vs-storage-aspnet-getting-started-queues/create-queue-results.png)

    Tal como mencionado anteriormente, o **CloudQueue.CreateIfNotExists** método devolve **verdadeiro** apenas quando a fila não existe e é criada. Por conseguinte, se executar a aplicação quando a fila existe, o método devolve **falso**. Para executar a aplicação várias vezes, tem de eliminar a fila antes de executar novamente a aplicação. Eliminar a fila pode ser efetuado através de **CloudQueue.Delete** método. Também pode eliminar a fila utilizando o [portal do Azure](http://go.microsoft.com/fwlink/p/?LinkID=525040) ou [Explorador de armazenamento do Microsoft Azure](../vs-azure-tools-storage-manage-with-storage-explorer.md).  

## <a name="add-a-message-to-a-queue"></a>Adicionar uma mensagem para uma fila

Assim que tiver [criou uma fila](#create-a-queue), pode adicionar mensagens para essa fila. Esta secção explica como adicionar uma mensagem para uma fila *teste fila*. 

> [!NOTE]
> 
> Esta secção assume que concluiu as fases [configurar o ambiente de desenvolvimento](#set-up-the-development-environment). 

1. Abra o ficheiro `QueuesController.cs`.

1. Adicione um método denominado **AddMessage** que devolve um **ActionResult**.

    ```csharp
    public ActionResult AddMessage()
    {
        // The code in this section goes here.

        return View();
    }
    ```
 
1. Dentro do **AddMessage** método, obter um **CloudStorageAccount** objeto que representa as informações da conta de armazenamento. Utilize o seguinte código para obter a cadeia de ligação de armazenamento e informações de conta de armazenamento da configuração do serviço do Azure: (alteração  *&lt;nome da conta de armazenamento >* para o nome da conta do storage do Azure que está a aceder.)
   
    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
       CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```
   
1. Obter um **CloudQueueClient** objeto representa um cliente do serviço fila.
   
    ```csharp
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();
    ```

1. Obter um **CloudQueueContainer** objeto que representa uma referência para a fila. 
   
    ```csharp
    CloudQueue queue = queueClient.GetQueueReference("test-queue");
    ```

1. Criar o **CloudQueueMessage** objeto que representa a mensagem que pretende adicionar à fila. A **CloudQueueMessage** objeto pode ser criado a partir de uma cadeia (no formato UTF-8) ou uma matriz de bytes.

    ```csharp
    CloudQueueMessage message = new CloudQueueMessage("Hello, Azure Queue Storage");
    ```

1. Chamar o **CloudQueue.AddMessage** método para adicionar o messaged para a fila.

    ```csharp
    queue.AddMessage(message);
    ```

1. Crie e defina alguns **ViewBag** propriedades para apresentação na vista.

    ```csharp
    ViewBag.QueueName = queue.Name;
    ViewBag.Message = message.AsString;
    ```

1. No **Explorador de soluções**, expanda o **vistas** pasta, faça duplo clique **filas**e no menu de contexto, selecione **adicionar -> vista**.

1. No **Adicionar vista** caixa de diálogo, introduza **AddMessage** para o nome da vista e selecione **adicionar**.

1. Abra `AddMessage.cshtml`e modificá-lo de modo a que se parece com o seguinte fragmento de código:

    ```csharp
    @{
        ViewBag.Title = "Add Message";
    }
    
    <h2>Add Message results</h2>
    
    The message '@ViewBag.Message' was added to the queue '@ViewBag.QueueName'.
    ```

1. No **Explorador de soluções**, expanda o **vistas -> partilhados** pasta e abra `_Layout.cshtml`.

1. Após a última **Html.ActionLink**, adicione o seguinte **Html.ActionLink**:

    ```html
    <li>@Html.ActionLink("Add message", "AddMessage", "Queues")</li>
    ```

1. Executar a aplicação e selecione **Adicionar mensagem** para ver os resultados semelhantes à captura de ecrã seguinte:
  
    ![Adicionar mensagem](./media/vs-storage-aspnet-getting-started-queues/add-message-results.png)

As duas secções - [ler uma mensagem numa fila sem a remover](#read-a-message-from-a-queue-without-removing-it) e [leitura e remover uma mensagem numa fila](#read-and-remove-a-message-from-a-queue) -ilustrar como ler mensagens numa fila.    

## <a name="read-a-message-from-a-queue-without-removing-it"></a>Ler uma mensagem numa fila sem a remover

Esta secção ilustra como observar uma mensagem em fila (lida a primeira mensagem sem a remover).  

> [!NOTE]
> 
> Esta secção assume que concluiu as fases [configurar o ambiente de desenvolvimento](#set-up-the-development-environment). 

1. Abra o ficheiro `QueuesController.cs`.

1. Adicione um método denominado **PeekMessage** que devolve um **ActionResult**.

    ```csharp
    public ActionResult PeekMessage()
    {
        // The code in this section goes here.

        return View();
    }
    ```
 
1. Dentro do **PeekMessage** método, obter um **CloudStorageAccount** objeto que representa as informações da conta de armazenamento. Utilize o seguinte código para obter a cadeia de ligação de armazenamento e informações de conta de armazenamento da configuração do serviço do Azure: (alteração  *&lt;nome da conta de armazenamento >* para o nome da conta do storage do Azure que está a aceder.)
   
    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
       CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```
   
1. Obter um **CloudQueueClient** objeto representa um cliente do serviço fila.
   
    ```csharp
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();
    ```

1. Obter um **CloudQueueContainer** objeto que representa uma referência para a fila. 
   
    ```csharp
    CloudQueue queue = queueClient.GetQueueReference("test-queue");
    ```

1. Chamar o **CloudQueue.PeekMessage** método ler a primeira mensagem da fila sem a remover da fila. 

    ```csharp
    CloudQueueMessage message = queue.PeekMessage();
    ```

1. Atualização do **ViewBag** com dois valores: o nome da fila e a mensagem foi lida. O **CloudQueueMessage** objeto expõe duas propriedades para obter o valor do objeto: **CloudQueueMessage.AsBytes** e **CloudQueueMessage.AsString**. **AsString** (utilizada neste exemplo) devolve uma cadeia, enquanto **AsBytes** devolve uma matriz de bytes.

    ```csharp
    ViewBag.QueueName = queue.Name; 
    ViewBag.Message = (message != null ? message.AsString : "");
    ```

1. No **Explorador de soluções**, expanda o **vistas** pasta, faça duplo clique **filas**e no menu de contexto, selecione **adicionar -> vista**.

1. No **Adicionar vista** caixa de diálogo, introduza **PeekMessage** para o nome da vista e selecione **adicionar**.

1. Abra `PeekMessage.cshtml`e modificá-lo de modo a que se parece com o seguinte fragmento de código:

    ```csharp
    @{
        ViewBag.Title = "PeekMessage";
    }
    
    <h2>Peek Message results</h2>
    
    <table border="1">
        <tr><th>Queue</th><th>Peeked Message</th></tr>
        <tr><td>@ViewBag.QueueName</td><td>@ViewBag.Message</td></tr>
    </table>    
    ```

1. No **Explorador de soluções**, expanda o **vistas -> partilhados** pasta e abra `_Layout.cshtml`.

1. Após a última **Html.ActionLink**, adicione o seguinte **Html.ActionLink**:

    ```html
    <li>@Html.ActionLink("Peek message", "PeekMessage", "Queues")</li>
    ```

1. Executar a aplicação e selecione **observar mensagem** para ver os resultados semelhantes à captura de ecrã seguinte:
  
    ![Observar mensagem](./media/vs-storage-aspnet-getting-started-queues/peek-message-results.png)

## <a name="read-and-remove-a-message-from-a-queue"></a>Ler e remover uma mensagem a partir de uma fila

Nesta secção, irá aprender a ler e remover uma mensagem numa fila.   

> [!NOTE]
> 
> Esta secção assume que concluiu as fases [configurar o ambiente de desenvolvimento](#set-up-the-development-environment). 

1. Abra o ficheiro `QueuesController.cs`.

1. Adicione um método denominado **ReadMessage** que devolve um **ActionResult**.

    ```csharp
    public ActionResult ReadMessage()
    {
        // The code in this section goes here.

        return View();
    }
    ```
 
1. Dentro do **ReadMessage** método, obter um **CloudStorageAccount** objeto que representa as informações da conta de armazenamento. Utilize o seguinte código para obter a cadeia de ligação de armazenamento e informações de conta de armazenamento da configuração do serviço do Azure: (alteração  *&lt;nome da conta de armazenamento >* para o nome da conta do storage do Azure que está a aceder.)
   
    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
       CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```
   
1. Obter um **CloudQueueClient** objeto representa um cliente do serviço fila.
   
    ```csharp
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();
    ```

1. Obter um **CloudQueueContainer** objeto que representa uma referência para a fila. 
   
    ```csharp
    CloudQueue queue = queueClient.GetQueueReference("test-queue");
    ```

1. Chamar o **CloudQueue.GetMessage** método ler a primeira mensagem da fila. O **CloudQueue.GetMessage** método faz com que a mensagem invisível durante 30 segundos (por predefinição) para quaisquer outras código ler mensagens de modo a que nenhum outro código pode modificar ou eliminar a mensagem durante o processamento. Para alterar o período de tempo a mensagem é invisível, modifique o **visibilityTimeout** parâmetro a ser transmitido para o **CloudQueue.GetMessage** método.

    ```csharp
    // This message will be invisible to other code for 30 seconds.
    CloudQueueMessage message = queue.GetMessage();     
    ```

1. Chamar o **CloudQueueMessage.Delete** método ao eliminar a mensagem da fila.

    ```csharp
    queue.DeleteMessage(message);
    ```

1. Atualização do **ViewBag** com a mensagem eliminado e o nome da fila.

    ```csharp
    ViewBag.QueueName = queue.Name;
    ViewBag.Message = message.AsString;
    ```
 
1. No **Explorador de soluções**, expanda o **vistas** pasta, faça duplo clique **filas**e no menu de contexto, selecione **adicionar -> vista**.

1. No **Adicionar vista** caixa de diálogo, introduza **ReadMessage** para o nome da vista e selecione **adicionar**.

1. Abra `ReadMessage.cshtml`e modificá-lo de modo a que se parece com o seguinte fragmento de código:

    ```csharp
    @{
        ViewBag.Title = "ReadMessage";
    }
    
    <h2>Read Message results</h2>
    
    <table border="1">
        <tr><th>Queue</th><th>Read (and Deleted) Message</th></tr>
        <tr><td>@ViewBag.QueueName</td><td>@ViewBag.Message</td></tr>
    </table>
    ```

1. No **Explorador de soluções**, expanda o **vistas -> partilhados** pasta e abra `_Layout.cshtml`.

1. Após a última **Html.ActionLink**, adicione o seguinte **Html.ActionLink**:

    ```html
    <li>@Html.ActionLink("Read/Delete message", "ReadMessage", "Queues")</li>
    ```

1. Executar a aplicação e selecione **mensagens de leitura/eliminar** para ver os resultados semelhantes à captura de ecrã seguinte:
  
    ![Ler e eliminar a mensagem](./media/vs-storage-aspnet-getting-started-queues/read-message-results.png)

## <a name="get-the-queue-length"></a>Obter o comprimento da fila

Esta secção ilustra como obter o comprimento da fila (número de mensagens em fila). 

> [!NOTE]
> 
> Esta secção assume que concluiu as fases [configurar o ambiente de desenvolvimento](#set-up-the-development-environment). 

1. Abra o ficheiro `QueuesController.cs`.

1. Adicione um método denominado **GetQueueLength** que devolve um **ActionResult**.

    ```csharp
    public ActionResult GetQueueLength()
    {
        // The code in this section goes here.

        return View();
    }
    ```
 
1. Dentro do **ReadMessage** método, obter um **CloudStorageAccount** objeto que representa as informações da conta de armazenamento. Utilize o seguinte código para obter a cadeia de ligação de armazenamento e informações de conta de armazenamento da configuração do serviço do Azure: (alteração  *&lt;nome da conta de armazenamento >* para o nome da conta do storage do Azure que está a aceder.)
   
    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
       CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```
   
1. Obter um **CloudQueueClient** objeto representa um cliente do serviço fila.
   
    ```csharp
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();
    ```

1. Obter um **CloudQueueContainer** objeto que representa uma referência para a fila. 
   
    ```csharp
    CloudQueue queue = queueClient.GetQueueReference("test-queue");
    ```

1. Chamar o **CloudQueue.FetchAttributes** método para obter os atributos da fila (incluindo o respetivo comprimento). 

    ```csharp
    queue.FetchAttributes();
    ```

6. Acesso a **CloudQueue.ApproximateMessageCount** propriedade para obter o comprimento da fila.
 
    ```csharp
    int? nMessages = queue.ApproximateMessageCount;
    ```

1. Atualização do **ViewBag** com o nome da fila e o comprimento.

    ```csharp
    ViewBag.QueueName = queue.Name;
    ViewBag.Length = nMessages;
    ```
 
1. No **Explorador de soluções**, expanda o **vistas** pasta, faça duplo clique **filas**e no menu de contexto, selecione **adicionar -> vista**.

1. No **Adicionar vista** caixa de diálogo, introduza **GetQueueLength** para o nome da vista e selecione **adicionar**.

1. Abra `GetQueueLengthMessage.cshtml`e modificá-lo de modo a que se parece com o seguinte fragmento de código:

    ```csharp
    @{
        ViewBag.Title = "GetQueueLength";
    }
    
    <h2>Get Queue Length results</h2>
    
    The queue '@ViewBag.QueueName' has a length of (number of messages): @ViewBag.Length
    ```

1. No **Explorador de soluções**, expanda o **vistas -> partilhados** pasta e abra `_Layout.cshtml`.

1. Após a última **Html.ActionLink**, adicione o seguinte **Html.ActionLink**:

    ```html
    <li>@Html.ActionLink("Get queue length", "GetQueueLength", "Queues")</li>
    ```

1. Executar a aplicação e selecione **obter o comprimento da fila** para ver os resultados semelhantes à captura de ecrã seguinte:
  
    ![Obter o comprimento da fila](./media/vs-storage-aspnet-getting-started-queues/get-queue-length-results.png)


## <a name="delete-a-queue"></a>Eliminar uma fila
Esta secção ilustra como eliminar uma fila. 

> [!NOTE]
> 
> Esta secção assume que concluiu as fases [configurar o ambiente de desenvolvimento](#set-up-the-development-environment). 

1. Abra o ficheiro `QueuesController.cs`.

1. Adicione um método denominado **DeleteQueue** que devolve um **ActionResult**.

    ```csharp
    public ActionResult DeleteQueue()
    {
        // The code in this section goes here.

        return View();
    }
    ```
 
1. Dentro do **DeleteQueue** método, obter um **CloudStorageAccount** objeto que representa as informações da conta de armazenamento. Utilize o seguinte código para obter a cadeia de ligação de armazenamento e informações de conta de armazenamento da configuração do serviço do Azure: (alteração  *&lt;nome da conta de armazenamento >* para o nome da conta do storage do Azure que está a aceder.)
   
    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
       CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```
   
1. Obter um **CloudQueueClient** objeto representa um cliente do serviço fila.
   
    ```csharp
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();
    ```

1. Obter um **CloudQueueContainer** objeto que representa uma referência para a fila. 
   
    ```csharp
    CloudQueue queue = queueClient.GetQueueReference("test-queue");
    ```

1. Chamar o **CloudQueue.Delete** método para eliminar a fila representada pelo **CloudQueue** objeto.

    ```csharp
    queue.Delete();
    ```

1. Atualização do **ViewBag** com o nome da fila e o comprimento.

    ```csharp
    ViewBag.QueueName = queue.Name;
    ```
 
1. No **Explorador de soluções**, expanda o **vistas** pasta, faça duplo clique **filas**e no menu de contexto, selecione **adicionar -> vista**.

1. No **Adicionar vista** caixa de diálogo, introduza **DeleteQueue** para o nome da vista e selecione **adicionar**.

1. Abra `DeleteQueue.cshtml`e modificá-lo de modo a que se parece com o seguinte fragmento de código:

    ```csharp
    @{
        ViewBag.Title = "DeleteQueue";
    }
    
    <h2>Delete Queue results</h2>
    
    @ViewBag.QueueName deleted.
    ```

1. No **Explorador de soluções**, expanda o **vistas -> partilhados** pasta e abra `_Layout.cshtml`.

1. Após a última **Html.ActionLink**, adicione o seguinte **Html.ActionLink**:

    ```html
    <li>@Html.ActionLink("Delete queue", "DeleteQueue", "Queues")</li>
    ```

1. Executar a aplicação e selecione **obter o comprimento da fila** para ver os resultados semelhantes à captura de ecrã seguinte:
  
    ![Eliminar a fila](./media/vs-storage-aspnet-getting-started-queues/delete-queue-results.png)

## <a name="next-steps"></a>Passos seguintes
Ver mais guias de funcionalidades para saber mais sobre as opções adicionais para armazenar dados no Azure.

  * [Introdução ao blob storage do Azure e o Visual Studio ligado serviços (ASP.NET)](../storage/vs-storage-aspnet-getting-started-blobs.md)
  * [Introdução ao table storage do Azure e o Visual Studio ligado serviços (ASP.NET)](vs-storage-aspnet-getting-started-tables.md)
