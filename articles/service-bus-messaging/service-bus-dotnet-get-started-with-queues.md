---
title: Escrever um programa que utiliza filas do Azure Service Bus | Microsoft Docs
description: "Como escrever uma aplicação de consola C# para as mensagens do Service Bus"
services: service-bus-messaging
documentationcenter: .net
author: jtaubensee
manager: timlt
editor: 
ms.assetid: 68a34c00-5600-43f6-bbcc-fea599d500da
ms.service: service-bus-messaging
ms.devlang: tbd
ms.topic: hero-article
ms.tgt_pltfrm: dotnet
ms.workload: na
ms.date: 03/23/2017
ms.author: jotaub;sethm
translationtype: Human Translation
ms.sourcegitcommit: f92909e0098a543f99baf3df3197a799bc9f1edc
ms.openlocfilehash: 83649bdad1d369cdfe4edf3c2bdaa67180db8668
ms.lasthandoff: 02/28/2017


---
# <a name="get-started-with-service-bus-queues"></a>Introdução às filas do Service Bus
[!INCLUDE [service-bus-selector-queues](../../includes/service-bus-selector-queues.md)]

## <a name="what-will-be-accomplished"></a>O que será efetuado
Neste tutorial, iremos realizar o seguinte:

1. Criar um espaço de nomes do Service Bus com o Portal do Azure.
2. Criar uma fila de Mensagens do Service Bus com o Portal do Azure.
3. Escrever uma aplicação de consola para enviar uma mensagem.
4. Escrever uma aplicação de consola para receber mensagens.

## <a name="prerequisites"></a>Pré-requisitos
1. [Visual Studio 2015 ou superior](http://www.visualstudio.com). Os exemplos neste tutorial utilizam o Visual Studio 2015.
2. Uma subscrição do Azure.

[!INCLUDE [create-account-note](../../includes/create-account-note.md)]

## <a name="1-create-a-namespace-using-the-azure-portal"></a>1. Criar um espaço de nomes com o Portal do Azure
Se já tiver criado um espaço de nomes do Service Bus, avance para a secção [Criar uma fila com o Portal do Azure](#2-create-a-queue-using-the-azure-portal).

[!INCLUDE [service-bus-create-namespace-portal](../../includes/service-bus-create-namespace-portal.md)]

## <a name="2-create-a-queue-using-the-azure-portal"></a>2. Criar uma fila com o Portal do Azure
Se já tiver criado uma fila do Service Bus, avance para a secção [Enviar mensagens para a fila](#3-send-messages-to-the-queue).

[!INCLUDE [service-bus-create-queue-portal](../../includes/service-bus-create-queue-portal.md)]

## <a name="3-send-messages-to-the-queue"></a>3. Enviar mensagens para a fila
Para enviar mensagens para a fila, vamos criar uma aplicação de consola C# com o Visual Studio.

### <a name="create-a-console-application"></a>Criar uma aplicação de consola

- Abra o Visual Studio e crie uma nova aplicação de Consola.

### <a name="add-the-service-bus-nuget-package"></a>Adicionar o pacote NuGet do Service Bus
1. Clique com o botão direito do rato no projeto recém-criado e selecione **Gerir Pacotes NuGet**.
2. Clique no separador **Procurar**, procure “Microsoft Azure Service Bus” e selecione o item **Microsoft Azure Service Bus**. Clique em **Instalar** para concluir a instalação e, em seguida, feche esta caixa de diálogo.
   
    ![Selecionar um pacote NuGet][nuget-pkg]

### <a name="write-some-code-to-send-a-message-to-the-queue"></a>Escrever código para enviar uma mensagem para a fila
1. Adicione a seguinte declaração using na parte superior do ficheiro Program.cs.
   
    ```csharp
    using Microsoft.ServiceBus.Messaging;
    ```
2. Adicione o seguinte código no método `Main`, defina a variável **connectionString** como a cadeia de ligação obtida ao criar o espaço de nomes e defina o **queueName** como o nome da fila utilizado aquando da criação da fila.
   
    ```csharp
    var connectionString = "<Your connection string>";
    var queueName = "<Your queue name>";
   
    var client = QueueClient.CreateFromConnectionString(connectionString, queueName);
    var message = new BrokeredMessage("This is a test message!");
    client.Send(message);
    ```
   
    O ficheiro Program.cs deve ter o seguinte aspeto.
   
    ```csharp
    using System;
    using Microsoft.ServiceBus.Messaging;
   
    namespace GettingStartedWithQueues
    {
        class Program
        {
            static void Main(string[] args)
            {
                var connectionString = "<Your connection string>";
                var queueName = "<Your queue name>";
   
                var client = QueueClient.CreateFromConnectionString(connectionString, queueName);
                var message = new BrokeredMessage("This is a test message!");
   
                client.Send(message);
            }
        }
    }
    ```
3. Execute o programa e consulte o Portal do Azure. Clique no nome da sua fila no painel **Descrição geral** do espaço de nomes. Repare que o valor de **Contagem de mensagens ativas** deve ser agora 1.
   
      ![Contagem de mensagens][queue-message]

## <a name="4-receive-messages-from-the-queue"></a>4. Receber mensagens da fila
1. Crie uma nova aplicação de consola e adicione uma referência ao pacote NuGet do Service Bus, semelhante à aplicação de envio anterior.
2. Adicione a seguinte declaração `using` na parte superior do ficheiro Program.cs.
   
    ```csharp
    using Microsoft.ServiceBus.Messaging;
    ```
3. Adicione o seguinte código no método `Main`, defina a variável **connectionString** como a cadeia de ligação obtida ao criar o espaço de nomes e defina o **queueName** como o nome da fila utilizado aquando da criação da fila.
   
    ```csharp
    var connectionString = "";
    var queueName = "samplequeue";
   
    var client = QueueClient.CreateFromConnectionString(connectionString, queueName);
   
    client.OnMessage(message =>
    {
      Console.WriteLine(String.Format("Message body: {0}", message.GetBody<String>()));
      Console.WriteLine(String.Format("Message id: {0}", message.MessageId));
    });
   
    Console.ReadLine();
    ```
   
    O ficheiro Program.cs deve ter o seguinte aspeto:
   
    ```csharp
    using System;
    using Microsoft.ServiceBus.Messaging;
   
    namespace GettingStartedWithQueues
    {
      class Program
      {
        static void Main(string[] args)
        {
          var connectionString = "";
          var queueName = "samplequeue";
   
          var client = QueueClient.CreateFromConnectionString(connectionString, queueName);
   
          client.OnMessage(message =>
          {
            Console.WriteLine(String.Format("Message body: {0}", message.GetBody<String>()));
            Console.WriteLine(String.Format("Message id: {0}", message.MessageId));
          });
   
          Console.ReadLine();
        }
      }
    }
    ```
4. Execute o programa e consulte o portal. Repare que o valor do **Comprimento da Fila** deve ser 0.
   
    ![Comprimento da fila][queue-message-receive]

Parabéns! Acabou de criar uma fila, enviar uma mensagem e receber uma mensagem.

## <a name="next-steps"></a>Passos seguintes
Consulte o nosso [repositório do GitHub com exemplos](https://github.com/Azure-Samples/azure-servicebus-messaging-samples) que demonstra algumas das funcionalidades mais avançadas das Mensagens do Azure Service Bus.

<!--Image references-->

[nuget-pkg]: ./media/service-bus-dotnet-get-started-with-queues/nuget-package.png
[queue-message]: ./media/service-bus-dotnet-get-started-with-queues/queue-message.png
[queue-message-receive]: ./media/service-bus-dotnet-get-started-with-queues/queue-message-receive.png
[github-samples]: https://github.com/Azure-Samples/azure-servicebus-messaging-samples

