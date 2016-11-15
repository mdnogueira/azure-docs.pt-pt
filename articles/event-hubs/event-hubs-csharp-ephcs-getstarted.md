---
title: "Introdução ao Hubs de Eventos no C# | Microsoft Docs"
description: "Siga este tutorial para começar a utilizar Event Hubs do Azure com C# e utilizar EventProcessorHost."
services: event-hubs
documentationcenter: 
author: jtaubensee
manager: timlt
editor: 
ms.assetid: 2ec2378a-34f7-44c3-b976-cc444c98c338
ms.service: event-hubs
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 09/02/2016
ms.author: jotaub;sethm
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 35a7e4693ef979dfb947714f2fe5ce5599991189


---
# <a name="get-started-with-event-hubs"></a>Introdução ao Event Hubs
[!INCLUDE [service-bus-selector-get-started](../../includes/service-bus-selector-get-started.md)]

## <a name="introduction"></a>Introdução
Os Event Hubs são um serviço que processa grandes quantidades de dados de eventos (telemetria) em aplicações e dispositivos ligados. Depois de recolher os dados para os Event Hubs, pode armazená-los utilizando um cluster de armazenamento ou transformá-los utilizando um fornecedor de análise em tempo real. Esta capacidade de processamento e recolha de eventos de grande escala é um componente importante das arquiteturas das aplicações modernas, incluindo a Internet das Coisas (IoT).

Este tutorial mostra como utilizar o Portal Clássico do Azure para criar um Hub de Eventos. Mostra também como recolher mensagens para um Hub de Eventos utilizando uma aplicação de consola escrita em C# e como as pode recuperar em paralelo utilizando a biblioteca do [Anfitrião do Processador de Eventos][Anfitrião do Processador de Eventos] do C#.

Para concluir este tutorial, irá precisar do seguinte:

* [Microsoft Visual Studio](http://visualstudio.com)
* Uma conta ativa do Azure. Se não tiver uma, pode criar uma conta gratuita em apenas alguns minutos. Para obter mais detalhes, consulte [Avaliação Gratuita do Azure](https://azure.microsoft.com/free/).

[!INCLUDE [event-hubs-create-event-hub](../../includes/event-hubs-create-event-hub.md)]

[!INCLUDE [service-bus-event-hubs-get-started-send-csharp](../../includes/service-bus-event-hubs-get-started-send-csharp.md)]

[!INCLUDE [service-bus-event-hubs-get-started-receive-ephcs](../../includes/service-bus-event-hubs-get-started-receive-ephcs.md)]

## <a name="run-the-applications"></a>Executar as aplicações
Agora pode executar as aplicações.

1. No Visual Studio, abra o projeto **Recetor** que criou anteriormente.
2. Clique com o botão direito do rato na solução **Recetor**, clique em **Adicionar** e em **Projeto Existente**.
3. Localize o ficheiro Sender.csproj existente e faça duplo clique para adicioná-lo à solução.
4. Novamente, clique com o botão direito do rato na solução **Recetor** e, em seguida, clique em **Propriedades**. É apresentada a página de propriedades do **Recetor**.
5. Clique em **Projeto de Arranque** e no botão **Vários projetos de arranque**. Defina a caixa **Ação** para os projetos **Recetor** e **Remetente** para **Iniciar**.
   
    ![][19]
6. Clique em **Dependências do Projeto**. Na caixa **Projetos**, clique em **Remetente**. Na caixa **Depende de**, certifique-se que a opção **Recetor** está marcada.
   
    ![][20]
7. Clique em **OK** para dispensar a caixa de diálogo **Propriedades**.
8. Prima F5 para executar o projeto **Recetor** no Visual Studio e espere até que este inicie os recetores para todas as partições.
   
   ![][21]
9. O projeto **Remetente** será executado automaticamente. Prima **Enter** na janela de consola e veja os eventos a serem apresentados na janela do recetor.
   
   ![][22]

Prima **Ctrl+C** na janela **Remetente** para terminar a aplicação Remetente e, em seguida, prima **Enter** na janela Recetor para encerrar essa aplicação.

## <a name="next-steps"></a>Passos seguintes
Agora que compilou uma aplicação de trabalho que cria um Hub de Eventos e envia e recebe dados, pode passar para os cenários seguintes:

* Uma [aplicação de exemplo que utiliza Hubs de eventos][aplicação de exemplo que utiliza Hubs de eventos].
* A amostra de [Processamento de Eventos de Aumento horizontal com Hubs de Eventos][Processamento de Eventos de Aumento horizontal com Hubs de Eventos].
* [Descrição geral dos Hubs de Eventos][Descrição geral dos Hubs de Eventos]

<!-- Images. -->
[19]: ./media/event-hubs-csharp-ephcs-getstarted/create-eh-proj1.png
[20]: ./media/event-hubs-csharp-ephcs-getstarted/create-eh-proj2.png
[21]: ./media/event-hubs-csharp-ephcs-getstarted/run-csharp-ephcs1.png
[22]: ./media/event-hubs-csharp-ephcs-getstarted/run-csharp-ephcs2.png

<!-- Links -->
[Portal clássico do Azure]: https://manage.windowsazure.com/
[Anfitrião do Processador de Eventos]: https://www.nuget.org/packages/Microsoft.Azure.ServiceBus.EventProcessorHost
[Descrição geral dos Hubs de Eventos]: event-hubs-overview.md
[aplicação de exemplo que utiliza os Hubs de Eventos]: https://code.msdn.microsoft.com/Service-Bus-Event-Hub-286fd097
[Dimensionar o Processamento de Eventos com os Hubs de Eventos]: https://code.msdn.microsoft.com/Service-Bus-Event-Hub-45f43fc3
[solução de mensagens em fila]: ../service-bus-messaging/service-bus-dotnet-multi-tier-app-using-service-bus-queues.md




<!--HONumber=Nov16_HO2-->


