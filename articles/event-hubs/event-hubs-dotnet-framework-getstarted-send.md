---
title: Enviar eventos para Hubs de Eventos do Azure com o Framework .NET | Microsoft Docs
description: "Introdução ao envio de eventos para Hubs de Eventos com o Framework .NET"
services: event-hubs
documentationcenter: 
author: jtaubensee
manager: timlt
editor: 
ms.assetid: c4974bd3-2a79-48a1-aa3b-8ee2d6655b28
ms.service: event-hubs
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 03/08/2017
ms.author: jotaub;sethm
translationtype: Human Translation
ms.sourcegitcommit: cfe4957191ad5716f1086a1a332faf6a52406770
ms.openlocfilehash: 5c326a025a4276ae9b1a777439ed6c728d3e7103
ms.lasthandoff: 03/09/2017


---
# <a name="send-events-to-azure-event-hubs-using-the-net-framework"></a>Enviar eventos para Hubs de Eventos do Azure com o Framework .NET

## <a name="introduction"></a>Introdução
Os Event Hubs são um serviço que processa grandes quantidades de dados de eventos (telemetria) em aplicações e dispositivos ligados. Depois de recolher os dados para os Event Hubs, pode armazená-los utilizando um cluster de armazenamento ou transformá-los utilizando um fornecedor de análise em tempo real. Esta capacidade de processamento e recolha de eventos de grande escala é um componente importante das arquiteturas das aplicações modernas, incluindo a Internet das Coisas (IoT).

Este tutorial mostra como utilizar o [Portal do Azure](https://portal.azure.com) para criar um Hub de Eventos. Também mostra como enviar eventos para um Hub de Eventos através de uma aplicação de consola escrita no C# com o .NET Framework. Para receber eventos com o .NET Framework, consulte o artigo [Receive events using the .NET Framework (Receber eventos com o .NET Framework)](event-hubs-dotnet-framework-getstarted-receive-eph.md) ou clique no idioma de receção adequado na tabela esquerda do conteúdo.

Para concluir este tutorial, irá precisar do seguinte:

* [Microsoft Visual Studio 2015 ou superior](http://visualstudio.com). As capturas de ecrã neste tutorial utilizam o Visual Studio 2017.
* Uma conta ativa do Azure. Se não tiver uma, pode criar uma conta gratuita em apenas alguns minutos. Para obter mais detalhes, consulte [Avaliação Gratuita do Azure](https://azure.microsoft.com/free/).

## <a name="create-an-event-hubs-namespace-and-an-event-hub"></a>Criar um espaço de nomes de Hubs de Eventos e um Hub de Eventos

O primeiro passo consiste em utilizar o [portal do Azure](https://portal.azure.com) para criar um espaço de nomes do tipo Hubs de Eventos e obter as credenciais de gestão de que a sua aplicação precisa para comunicar com o Hub de Eventos. Para criar um espaço de nomes e um Hub de Eventos, siga o procedimento descrito [neste artigo](event-hubs-create.md) e, em seguida, continue com os passos seguintes.

## <a name="create-a-console-application"></a>Criar uma aplicação de consola
Nesta secção, irá escrever uma aplicação de consola do Windows para enviar eventos para o seu Hub de Eventos.

1. No Visual Studio, crie um novo projeto de Aplicação de Ambiente de Trabalho do Visual C# com o modelo de projeto **Aplicação de Consola**. Dê ao projeto o nome **Remetente**.
   
    ![](./media/event-hubs-dotnet-framework-getstarted-send/create-sender-csharp1.png)
2. No Explorador de Soluções, clique com o botão direito do rato no projeto **Emissor** e, em seguida, clique em **Gerir Pacotes NuGet para Solução**. 
3. Clique no separador **Procurar** e, em seguida, procure `Microsoft Azure Service Bus`. Clique em **Instalar** e aceite os termos de utilização. 
   
    ![](./media/event-hubs-dotnet-framework-getstarted-send/create-sender-csharp2.png)
   
    O Visual Studio transfere, instala e adiciona uma referência ao [Pacote NuGet da biblioteca do Service Bus do Azure](https://www.nuget.org/packages/WindowsAzure.ServiceBus).
4. Adicione as seguinte declarações `using` na parte superior do ficheiro **Program.cs**:
   
    ```csharp
    using System.Threading;
    using Microsoft.ServiceBus.Messaging;
    ```
5. Adicione os campos seguintes à classe **Programa**, substituindo os valores dos marcadores de posição pelo nome do Hub de Eventos que criou na secção anterior e pela cadeia de ligação de ao nível do espaço de nomes que guardou anteriormente.
   
    ```csharp
    static string eventHubName = "{Event Hub name}";
    static string connectionString = "{send connection string}";
    ```
6. Adicione o seguinte método à classe **Programa**:
   
    ```csharp
    static void SendingRandomMessages()
    {
        var eventHubClient = EventHubClient.CreateFromConnectionString(connectionString, eventHubName);
        while (true)
        {
            try
            {
                var message = Guid.NewGuid().ToString();
                Console.WriteLine("{0} > Sending message: {1}", DateTime.Now, message);
                eventHubClient.Send(new EventData(Encoding.UTF8.GetBytes(message)));
            }
            catch (Exception exception)
            {
                Console.ForegroundColor = ConsoleColor.Red;
                Console.WriteLine("{0} > Exception: {1}", DateTime.Now, exception.Message);
                Console.ResetColor();
            }
   
            Thread.Sleep(200);
        }
    }
    ```
   
    Este método envia continuamente eventos para o Event Hub com um atraso de 200 ms.
7. Por fim, adicione as seguintes linhas ao método **Main**:
   
    ```csharp
    Console.WriteLine("Press Ctrl-C to stop the sender process");
    Console.WriteLine("Press Enter to start now");
    Console.ReadLine();
    SendingRandomMessages();
    ```
8. Execute o programa e certifique-se de que não existem erros.
  
Parabéns! Enviou agora mensagens para um Hub de Eventos.

## <a name="next-steps"></a>Passos seguintes
Agora que compilou uma aplicação de trabalho que cria um Hub de Eventos e envia dados, pode passar para os cenários seguintes:

* [Receber eventos com o Anfitrião de Processador de Eventos](event-hubs-dotnet-framework-getstarted-receive-eph.md)
* [Referência do Anfitrião do Processador de Eventos](/dotnet/api/microsoft.servicebus.messaging.eventprocessorhost)
* [Descrição geral dos Hubs de Eventos](event-hubs-what-is-event-hubs.md)

<!-- Images. -->
[19]: ./media/event-hubs-csharp-ephcs-getstarted/create-eh-proj1.png
[20]: ./media/event-hubs-csharp-ephcs-getstarted/create-eh-proj2.png
[21]: ./media/event-hubs-csharp-ephcs-getstarted/run-csharp-ephcs1.png
[22]: ./media/event-hubs-csharp-ephcs-getstarted/run-csharp-ephcs2.png


