---
title: "Introdução ao Hubs de Eventos no C# | Microsoft Docs"
description: "Siga este tutorial para começar a utilizar os Hubs de Eventos do Azure; enviando eventos em C# e recebendo os mesmo em Java utilizando o EventProcessorHost."
services: event-hubs
documentationcenter: 
author: jtaubensee
manager: timlt
editor: 
ms.assetid: 059fb733-a397-400e-8e43-0c7ea5930b8b
ms.service: event-hubs
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 09/27/2016
ms.author: jotaub;sethm
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 51d880650ab8059f3346b5c1272c232b49be33e9


---
# <a name="get-started-with-event-hubs"></a>Introdução ao Event Hubs
[!INCLUDE [service-bus-selector-get-started](../../includes/service-bus-selector-get-started.md)]

## <a name="introduction"></a>Introdução
Os Event Hubs são um serviço que processa grandes quantidades de dados de eventos (telemetria) em aplicações e dispositivos ligados. Depois de recolher os dados para os Event Hubs, pode armazená-los utilizando um cluster de armazenamento ou transformá-los utilizando um fornecedor de análise em tempo real. Esta capacidade de processamento e recolha de eventos de grande escala é um componente importante das arquiteturas das aplicações modernas, incluindo a Internet das Coisas (IoT).

Este tutorial mostra como utilizar o Portal Clássico do Azure para criar um Hub de Eventos. Mostra também como recolher mensagens para um Hub de Eventos utilizando uma aplicação de consola escrita em C# e como as pode recuperar em paralelo utilizando a biblioteca Java de Anfitrião do Processador de Eventos.

Para concluir este tutorial, irá precisar do seguinte:

* [Microsoft Visual Studio](http://visualstudio.com)
* Uma conta ativa do Azure. <br/>Se não tiver uma, pode criar uma conta gratuita em apenas alguns minutos. Para obter mais detalhes, consulte [Avaliação Gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started%2F target="_blank").

[!INCLUDE [event-hubs-create-event-hub](../../includes/event-hubs-create-event-hub.md)]

[!INCLUDE [service-bus-event-hubs-get-started-send-csharp](../../includes/service-bus-event-hubs-get-started-send-csharp.md)]

[!INCLUDE [service-bus-event-hubs-get-started-receive-ephjava](../../includes/service-bus-event-hubs-get-started-receive-ephjava.md)]

## <a name="run-the-applications"></a>Executar as aplicações
Agora pode executar as aplicações.

1. Executar o projeto **Recetor**.
   
   ![][21]
2. Executar o projeto **Emissor**.
   
   ![][22]

## <a name="next-steps"></a>Passos seguintes
Agora que compilou uma aplicação de trabalho que cria um Hub de Eventos e envia e recebe dados, pode passar para os cenários seguintes:

* Uma [aplicação de exemplo que utiliza Hubs de eventos][aplicação de exemplo que utiliza Hubs de eventos].
* A amostra de [Processamento de Eventos de Aumento horizontal com Hubs de Eventos][Processamento de Eventos de Aumento horizontal com Hubs de Eventos].
* [Descrição geral dos Hubs de Eventos][Descrição geral dos Hubs de Eventos]

<!-- Images. -->
[21]: ./media/event-hubs-csharp-ephjava-getstarted/ephjava.png
[22]: ./media/event-hubs-csharp-ephjava-getstarted/cs-send.png

<!-- Links -->
[Portal Clássico do Azure]: https://manage.windowsazure.com/
[Descrição geral dos Hubs de Eventos]: event-hubs-overview.md
[aplicação de exemplo que utiliza os Hubs de Eventos]: https://code.msdn.microsoft.com/Service-Bus-Event-Hub-286fd097
[Aumentar Horizontalmente o Processamento de Eventos com os Hubs de Eventos]: https://code.msdn.microsoft.com/Service-Bus-Event-Hub-45f43fc3



<!--HONumber=Nov16_HO2-->


