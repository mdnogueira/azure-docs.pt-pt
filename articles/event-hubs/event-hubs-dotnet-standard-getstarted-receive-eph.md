---
title: "Receber eventos de utilizar a biblioteca .NET padrão de Event Hubs do Azure | Microsoft Docs"
description: "Começar a receber mensagens com o EventProcessorHost em .NET padrão"
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
ms.date: 11/28/2017
ms.author: sethm
ms.openlocfilehash: a88b5da8fa504e0528caa7fa212d4cec26d1cf66
ms.sourcegitcommit: 651a6fa44431814a42407ef0df49ca0159db5b02
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/28/2017
---
# <a name="get-started-receiving-messages-with-the-event-processor-host-in-net-standard"></a>Começar a receber mensagens com o anfitrião do processador de eventos no .NET padrão

> [!NOTE]
> Este exemplo está disponível no [GitHub](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/SampleEphReceiver).

Este tutorial mostra como escrever uma aplicação de consola .NET Core que recebe mensagens do hub de eventos utilizando o **anfitrião do processador de eventos** biblioteca. Pode executar o [GitHub](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/SampleEphReceiver) solução como-é, substituindo as cadeias com os valores de conta de armazenamento e de hub de eventos. Ou, pode seguir os passos neste tutorial para criar os seus próprios.

## <a name="prerequisites"></a>Pré-requisitos

* [Microsoft Visual Studio 2015 ou 2017](http://www.visualstudio.com). Os exemplos neste tutorial utilize 2017 do Visual Studio, mas o Visual Studio 2015 também é suportada.
* [.NET core Visual Studio 2015 ou ferramentas de 2017](http://www.microsoft.com/net/core).
* Uma subscrição do Azure.
* Um espaço de nomes de Event Hubs do Azure.
* Uma conta de armazenamento do Azure.

## <a name="create-an-event-hubs-namespace-and-an-event-hub"></a>Criar um espaço de nomes de Hubs de Eventos e um hub de eventos  

O primeiro passo consiste em utilizar o [portal do Azure](https://portal.azure.com) para criar um espaço de nomes para o tipo de Event Hubs e obter as credenciais de gestão que a aplicação necessita para comunicar com o hub de eventos. Para criar um hub de espaço de nomes e eventos, siga o procedimento [neste artigo](event-hubs-create.md)e, em seguida, continue com este tutorial.  

## <a name="create-an-azure-storage-account"></a>Criar uma conta de Armazenamento do Azure  

1. Inicie sessão no [Portal do Azure](https://portal.azure.com).  
2. No painel de navegação esquerdo do portal, clique em **novo**, clique em **armazenamento**e, em seguida, clique em **conta de armazenamento**.  
3. Preencha os campos na janela de conta de armazenamento e, em seguida, clique em **criar**.

    ![Criar conta de armazenamento][1]

4. Depois de confirmar a **implementações com êxito** da mensagem, clique no nome da nova conta de armazenamento. No **Essentials** janela, clique em **Blobs**. Quando o **serviço Blob** é aberta a caixa de diálogo, clique em **+ contentor** na parte superior. Dê um nome de contentor e, em seguida, feche **serviço Blob**.  
5. Clique em **chaves de acesso** na janela da esquerda e copie o nome do contentor de armazenamento, a conta de armazenamento e o valor de **chave1**. Guarde estes valores para o bloco de notas ou alguns outra localização temporária.  

## <a name="create-a-console-application"></a>Criar uma aplicação de consola

Inicie o Visual Studio. No menu **Ficheiro**, clique em **Novo** e, em seguida, clique em **Projeto**. Crie uma aplicação de consola .NET Core.

![Novo projeto][2]

## <a name="add-the-event-hubs-nuget-package"></a>Adicione o pacote NuGet de Hubs de eventos

Adicionar o [ **Microsoft.Azure.EventHubs** ](https://www.nuget.org/packages/Microsoft.Azure.EventHubs/) e [ **Microsoft.Azure.EventHubs.Processor** ](https://www.nuget.org/packages/Microsoft.Azure.EventHubs.Processor/) biblioteca .NET padrão NuGet pacotes para o projeto, seguindo estes passos: 

1. Clique com o botão direito do rato no projeto recém-criado e selecione **Gerir Pacotes NuGet**.
2. Clique em de **procurar** separador, procure **Microsoft.Azure.EventHubs**e, em seguida, selecione o **Microsoft.Azure.EventHubs** pacote. Clique em **Instalar** para concluir a instalação e, em seguida, feche esta caixa de diálogo.
3. Repita os passos 1 e 2 e instalar o **Microsoft.Azure.EventHubs.Processor** pacote.

## <a name="implement-the-ieventprocessor-interface"></a>Implementa a interface IEventProcessor

1. No Explorador de soluções, clique com botão direito no projeto, clique em **adicionar**e, em seguida, clique em **classe**. Nome da nova classe **SimpleEventProcessor**.

2. Abra o ficheiro SimpleEventProcessor.cs e adicione o seguinte `using` declarações na parte superior do ficheiro.

    ```csharp
    using Microsoft.Azure.EventHubs;
    using Microsoft.Azure.EventHubs.Processor;
    using System.Threading.Tasks;
    ```

3. Implementar o `IEventProcessor` interface. Substitua os conteúdos integrais do `SimpleEventProcessor` classe com o seguinte código:

    ```csharp
    public class SimpleEventProcessor : IEventProcessor
    {
        public Task CloseAsync(PartitionContext context, CloseReason reason)
        {
            Console.WriteLine($"Processor Shutting Down. Partition '{context.PartitionId}', Reason: '{reason}'.");
            return Task.CompletedTask;
        }

        public Task OpenAsync(PartitionContext context)
        {
            Console.WriteLine($"SimpleEventProcessor initialized. Partition: '{context.PartitionId}'");
            return Task.CompletedTask;
        }

        public Task ProcessErrorAsync(PartitionContext context, Exception error)
        {
            Console.WriteLine($"Error on Partition: {context.PartitionId}, Error: {error.Message}");
            return Task.CompletedTask;
        }

        public Task ProcessEventsAsync(PartitionContext context, IEnumerable<EventData> messages)
        {
            foreach (var eventData in messages)
            {
                var data = Encoding.UTF8.GetString(eventData.Body.Array, eventData.Body.Offset, eventData.Body.Count);
                Console.WriteLine($"Message received. Partition: '{context.PartitionId}', Data: '{data}'");
            }

            return context.CheckpointAsync();
        }
    }
    ```

## <a name="write-a-main-console-method-that-uses-the-simpleeventprocessor-class-to-receive-messages"></a>Escrever um método principal de consola que utiliza a classe de SimpleEventProcessor para receber mensagens

1. Adicione as seguinte declarações `using` à parte superior do ficheiro Program.cs.

    ```csharp
    using Microsoft.Azure.EventHubs;
    using Microsoft.Azure.EventHubs.Processor;
    using System.Threading.Tasks;
    ```

2. As constantes de adicionar o `Program` classe para a cadeia de ligação do hub de eventos, nome do hub de eventos, nome de contentor da conta de armazenamento, nome de conta do storage e chave de conta de armazenamento. Adicione o seguinte código, substituindo os marcadores de posição com os valores correspondentes.

    ```csharp
    private const string EhConnectionString = "{Event Hubs connection string}";
    private const string EhEntityPath = "{Event Hub path/name}";
    private const string StorageContainerName = "{Storage account container name}";
    private const string StorageAccountName = "{Storage account name}";
    private const string StorageAccountKey = "{Storage account key}";

    private static readonly string StorageConnectionString = string.Format("DefaultEndpointsProtocol=https;AccountName={0};AccountKey={1}", StorageAccountName, StorageAccountKey);
    ```   

3. Adicionar um novo método denominado `MainAsync` para o `Program` classe, da seguinte forma:

    ```csharp
    private static async Task MainAsync(string[] args)
    {
        Console.WriteLine("Registering EventProcessor...");

        var eventProcessorHost = new EventProcessorHost(
            EhEntityPath,
            PartitionReceiver.DefaultConsumerGroupName,
            EhConnectionString,
            StorageConnectionString,
            StorageContainerName);

        // Registers the Event Processor Host and starts receiving messages
        await eventProcessorHost.RegisterEventProcessorAsync<SimpleEventProcessor>();

        Console.WriteLine("Receiving. Press ENTER to stop worker.");
        Console.ReadLine();

        // Disposes of the Event Processor Host
        await eventProcessorHost.UnregisterEventProcessorAsync();
    }
    ```

3. Adicione a seguinte linha de código para o `Main` método:

    ```csharp
    MainAsync(args).GetAwaiter().GetResult();
    ```

    O ficheiro Program.cs deve ter o seguinte aspeto:

    ```csharp
    namespace SampleEphReceiver
    {

        public class Program
        {
            private const string EhConnectionString = "{Event Hubs connection string}";
            private const string EhEntityPath = "{Event Hub path/name}";
            private const string StorageContainerName = "{Storage account container name}";
            private const string StorageAccountName = "{Storage account name}";
            private const string StorageAccountKey = "{Storage account key}";

            private static readonly string StorageConnectionString = string.Format("DefaultEndpointsProtocol=https;AccountName={0};AccountKey={1}", StorageAccountName, StorageAccountKey);

            public static void Main(string[] args)
            {
                MainAsync(args).GetAwaiter().GetResult();
            }

            private static async Task MainAsync(string[] args)
            {
                Console.WriteLine("Registering EventProcessor...");

                var eventProcessorHost = new EventProcessorHost(
                    EhEntityPath,
                    PartitionReceiver.DefaultConsumerGroupName,
                    EhConnectionString,
                    StorageConnectionString,
                    StorageContainerName);

                // Registers the Event Processor Host and starts receiving messages
                await eventProcessorHost.RegisterEventProcessorAsync<SimpleEventProcessor>();

                Console.WriteLine("Receiving. Press ENTER to stop worker.");
                Console.ReadLine();

                // Disposes of the Event Processor Host
                await eventProcessorHost.UnregisterEventProcessorAsync();
            }
        }
    }
    ```

4. Execute o programa e certifique-se de que não existem erros.

Parabéns! Agora recebeu mensagens de um hub de eventos ao utilizar o anfitrião do processador de eventos.

## <a name="next-steps"></a>Passos seguintes
Pode saber mais sobre os Hubs de Eventos ao aceder às seguintes ligações:

* [Descrição geral dos Hubs de Eventos](event-hubs-what-is-event-hubs.md)
* [Criar um hub de eventos](event-hubs-create.md)
* [FAQ dos Hubs de Eventos](event-hubs-faq.md)

[1]: ./media/event-hubs-dotnet-standard-getstarted-receive-eph/event-hubs-python1.png
[2]: ./media/event-hubs-dotnet-standard-getstarted-receive-eph/netcore.png
