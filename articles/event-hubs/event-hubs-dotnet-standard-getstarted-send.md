---
title: "Enviar eventos para utilizar o padrão de .NET de Event Hubs do Azure | Microsoft Docs"
description: "Introdução ao envio de eventos para os Event Hubs no .NET padrão"
services: event-hubs
documentationcenter: na
author: sethmanheim
manager: timlt
editor: 
ms.assetid: 
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/10/2017
ms.author: sethm
ms.openlocfilehash: 5cf01580b53b551064a46282b9005ade6afe9604
ms.sourcegitcommit: 51ea178c8205726e8772f8c6f53637b0d43259c6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="get-started-sending-messages-to-azure-event-hubs-in-net-standard"></a>Começar a enviar mensagens aos Hubs de eventos do Azure no .NET padrão

> [!NOTE]
> Este exemplo está disponível no [GitHub](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/SampleSender).

Este tutorial mostra como escrever uma aplicação de consola .NET Core envia um conjunto de mensagens para um hub de eventos. Pode executar o [GitHub](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/SampleSender) solução como-é, substituindo o `EhConnectionString` e `EhEntityPath` cadeias com os valores de hub de eventos. Ou, pode seguir os passos neste tutorial para criar os seus próprios.

## <a name="prerequisites"></a>Pré-requisitos

* [Microsoft Visual Studio 2015 ou 2017](http://www.visualstudio.com). Os exemplos neste tutorial utilize 2017 do Visual Studio, mas o Visual Studio 2015 também é suportada.
* [.NET core Visual Studio 2015 ou ferramentas de 2017](http://www.microsoft.com/net/core).
* Uma subscrição do Azure.
* Um espaço de nomes do hub de eventos.

Para enviar mensagens para um hub de eventos, iremos irá utilizar o Visual Studio para escrever uma aplicação de consola c#.

## <a name="create-an-event-hubs-namespace-and-an-event-hub"></a>Criar um espaço de nomes de Hubs de Eventos e um hub de eventos

O primeiro passo consiste em utilizar o [portal do Azure](https://portal.azure.com) para criar um espaço de nomes para o tipo de hub de eventos e obter as credenciais de gestão que a aplicação necessita para comunicar com o hub de eventos. Para criar um espaço de nomes e um hub de eventos, siga o procedimento [neste artigo](event-hubs-create.md)e, em seguida, continue com os seguintes passos.

## <a name="create-a-console-application"></a>Criar uma aplicação de consola

Inicie o Visual Studio. No menu **Ficheiro**, clique em **Novo** e, em seguida, clique em **Projeto**. Crie uma aplicação de consola .NET Core.

![Novo projeto][1]

## <a name="add-the-event-hubs-nuget-package"></a>Adicione o pacote NuGet de Hubs de eventos

Adicionar o [ `Microsoft.Azure.EventHubs` ](https://www.nuget.org/packages/Microsoft.Azure.EventHubs/) pacote NuGet da biblioteca padrão do .NET ao seu projeto, seguindo estes passos: 

1. Clique com o botão direito do rato no projeto recém-criado e selecione **Gerir Pacotes NuGet**.
2. Clique em de **procurar** separador, em seguida, procure "Microsoft.Azure.EventHubs" e selecione o **Microsoft.Azure.EventHubs** pacote. Clique em **Instalar** para concluir a instalação e, em seguida, feche esta caixa de diálogo.

## <a name="write-some-code-to-send-messages-to-the-event-hub"></a>Escrever alguns códigos para enviar mensagens para o hub de eventos

1. Adicione as seguinte declarações `using` à parte superior do ficheiro Program.cs.

    ```csharp
    using Microsoft.Azure.EventHubs;
    using System.Text;
    using System.Threading.Tasks;
    ```

2. As constantes de adicionar o `Program` classe para os Event Hubs caminho de ligação a cadeia e a entidade (nome de hub de eventos individuais). Substitua os marcadores de posição entre parênteses Retos os valores adequados que foram obtidos ao criar o hub de eventos. Certifique-se de que o `{Event Hubs connection string}` é a cadeia de ligação ao nível do espaço de nomes e não a cadeia de hub de eventos. 

    ```csharp
    private static EventHubClient eventHubClient;
    private const string EhConnectionString = "{Event Hubs connection string}";
    private const string EhEntityPath = "{Event Hub path/name}";
    ```

3. Adicionar um novo método denominado `MainAsync` para o `Program` classe, da seguinte forma:

    ```csharp
    private static async Task MainAsync(string[] args)
    {
        // Creates an EventHubsConnectionStringBuilder object from the connection string, and sets the EntityPath.
        // Typically, the connection string should have the entity path in it, but for the sake of this simple scenario
        // we are using the connection string from the namespace.
        var connectionStringBuilder = new EventHubsConnectionStringBuilder(EhConnectionString)
        {
            EntityPath = EhEntityPath
        };

        eventHubClient = EventHubClient.CreateFromConnectionString(connectionStringBuilder.ToString());

        await SendMessagesToEventHub(100);

        await eventHubClient.CloseAsync();

        Console.WriteLine("Press ENTER to exit.");
        Console.ReadLine();
    }
    ```

4. Adicionar um novo método denominado `SendMessagesToEventHub` para o `Program` classe, da seguinte forma:

    ```csharp
    // Creates an event hub client and sends 100 messages to the event hub.
    private static async Task SendMessagesToEventHub(int numMessagesToSend)
    {
        for (var i = 0; i < numMessagesToSend; i++)
        {
            try
            {
                var message = $"Message {i}";
                Console.WriteLine($"Sending message: {message}");
                await eventHubClient.SendAsync(new EventData(Encoding.UTF8.GetBytes(message)));
            }
            catch (Exception exception)
            {
                Console.WriteLine($"{DateTime.Now} > Exception: {exception.Message}");
            }

            await Task.Delay(10);
        }

        Console.WriteLine($"{numMessagesToSend} messages sent.");
    }
    ```

5. Adicione o seguinte código para o `Main` método o `Program` classe.

    ```csharp
    MainAsync(args).GetAwaiter().GetResult();
    ```

   O ficheiro Program.cs deve ter o seguinte aspeto.

    ```csharp
    namespace SampleSender
    {
        using System;
        using System.Text;
        using System.Threading.Tasks;
        using Microsoft.Azure.EventHubs;

        public class Program
        {
            private static EventHubClient eventHubClient;
            private const string EhConnectionString = "{Event Hubs connection string}";
            private const string EhEntityPath = "{Event Hub path/name}";

            public static void Main(string[] args)
            {
                MainAsync(args).GetAwaiter().GetResult();
            }

            private static async Task MainAsync(string[] args)
            {
                // Creates an EventHubsConnectionStringBuilder object from the connection string, and sets the EntityPath.
                // Typically, the connection string should have the entity path in it, but for the sake of this simple scenario
                // we are using the connection string from the namespace.
                var connectionStringBuilder = new EventHubsConnectionStringBuilder(EhConnectionString)
                {
                    EntityPath = EhEntityPath
                };

                eventHubClient = EventHubClient.CreateFromConnectionString(connectionStringBuilder.ToString());

                await SendMessagesToEventHub(100);

                await eventHubClient.CloseAsync();

                Console.WriteLine("Press ENTER to exit.");
                Console.ReadLine();
            }

            // Creates an event hub client and sends 100 messages to the event hub.
            private static async Task SendMessagesToEventHub(int numMessagesToSend)
            {
                for (var i = 0; i < numMessagesToSend; i++)
                {
                    try
                    {
                        var message = $"Message {i}";
                        Console.WriteLine($"Sending message: {message}");
                        await eventHubClient.SendAsync(new EventData(Encoding.UTF8.GetBytes(message)));
                    }
                    catch (Exception exception)
                    {
                        Console.WriteLine($"{DateTime.Now} > Exception: {exception.Message}");
                    }

                    await Task.Delay(10);
                }

                Console.WriteLine($"{numMessagesToSend} messages sent.");
            }
        }
    }
    ```

6. Execute o programa e certifique-se de que não existem erros.

Parabéns! Enviou agora mensagens para um hub de eventos.

## <a name="next-steps"></a>Passos seguintes
Pode saber mais sobre os Hubs de Eventos ao aceder às seguintes ligações:

* [Receber eventos provenientes dos Hubs de eventos](event-hubs-dotnet-standard-getstarted-receive-eph.md)
* [Descrição geral dos Hubs de Eventos](event-hubs-what-is-event-hubs.md)
* [Criar um hub de eventos](event-hubs-create.md)
* [FAQ dos Hubs de Eventos](event-hubs-faq.md)

[1]: ./media/event-hubs-dotnet-standard-getstarted-send/netcore.png
