---
title: Enviar eventos para os Hubs de eventos do Azure utilizando Java | Microsoft Docs
description: "Começar a enviar para os Event Hubs utilizando Java"
services: event-hubs
documentationcenter: 
author: sethmanheim
manager: timlt
editor: 
ms.assetid: 
ms.service: event-hubs
ms.workload: core
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/15/2017
ms.author: sethm
ms.openlocfilehash: b31771001989e20b88bc8d7bca1afceb58ec197c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="send-events-to-azure-event-hubs-using-java"></a>Enviar eventos para utilizando Java de Event Hubs do Azure

## <a name="introduction"></a>Introdução
Os Event Hubs são um sistema de ingestão altamente dimensionável, que pode ingerir milhões de eventos por segundo, que uma aplicação possa processar e analisar as quantidades enormes de dados produzidos pelos seus dispositivos e aplicações ligados. Depois de recolhidos para um hub de eventos, pode transformar e armazenar dados através de qualquer fornecedor de análise em tempo real ou cluster de armazenamento.

Para obter mais informações, consulte o [descrição geral dos Event Hubs][Event Hubs overview].

Este tutorial mostra como enviar eventos para um hub de eventos utilizando uma aplicação de consola em Java. Para receber eventos utilizando a biblioteca de anfitrião do processador de eventos de Java, consulte [neste artigo](event-hubs-java-get-started-receive-eph.md), ou ao clicar no idioma adequado de receção na tabela da esquerda do conteúdo.

Para concluir este tutorial, irá precisar do seguinte:

* Ambiente de desenvolvimento Java. Para este tutorial, partimos do pressuposto [Eclipse](https://www.eclipse.org/).
* Uma conta ativa do Azure. <br/>Se não tiver uma conta, pode criar uma conta gratuita em apenas alguns minutos. Para obter mais detalhes, consulte <a href="http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started%2F" target="_blank">Avaliação Gratuita do Azure</a>.

## <a name="send-messages-to-event-hubs"></a>Enviar mensagens para os Event Hubs
A biblioteca de cliente de Java para os Event Hubs está disponível para utilização em projetos Maven a partir de [repositório Central Maven](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-eventhubs%22). Pode referenciar esta biblioteca utilizando a seguinte declaração de dependência dentro do ficheiro de projeto Maven:    

```xml
<dependency>
    <groupId>com.microsoft.azure</groupId>
    <artifactId>azure-eventhubs</artifactId>
    <version>{VERSION}</version>
</dependency>
```

Para diferentes tipos de ambientes de compilação, pode obter explicitamente os mais recente ficheiros JAR do [repositório Central Maven](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-eventhubs%22).  

Para um publicador de evento simples, importar o *com.microsoft.azure.eventhubs* pacote para as classes de cliente dos Event Hubs e o *com.microsoft.azure.servicebus* , tais como o pacote para classes de utilitários exceções comuns que são partilhadas com o cliente de mensagens do Service Bus do Azure. 

Para o exemplo que se segue, comece por criar um novo projeto Maven para uma consola/aplicação shell no seu ambiente de desenvolvimento Java favorito. Designar a classe `Send`.     

```java
import java.io.IOException;
import java.nio.charset.*;
import java.util.*;
import java.util.concurrent.ExecutionException;

import com.microsoft.azure.eventhubs.*;
import com.microsoft.azure.servicebus.*;

public class Send
{
    public static void main(String[] args) 
            throws ServiceBusException, ExecutionException, InterruptedException, IOException
    {
```

Substitua os nomes de hub de espaço de nomes e os eventos com os valores que utilizou quando criou o hub de eventos.

```java
    final String namespaceName = "----ServiceBusNamespaceName-----";
    final String eventHubName = "----EventHubName-----";
    final String sasKeyName = "-----SharedAccessSignatureKeyName-----";
    final String sasKey = "---SharedAccessSignatureKey----";
    ConnectionStringBuilder connStr = new ConnectionStringBuilder(namespaceName, eventHubName, sasKeyName, sasKey);
```

Em seguida, crie um evento único, transformar uma cadeia na respectiva codificação de bytes UTF-8. Em seguida, crie uma nova instância de cliente de Event Hubs da cadeia de ligação e envie a mensagem.   

```java 

    byte[] payloadBytes = "Test AMQP message from JMS".getBytes("UTF-8");
    EventData sendEvent = new EventData(payloadBytes);

    EventHubClient ehClient = EventHubClient.createFromConnectionStringSync(connStr.toString());
    ehClient.sendSync(sendEvent);
    }
}

``` 

## <a name="next-steps"></a>Passos seguintes
Pode saber mais sobre os Hubs de Eventos ao aceder às seguintes ligações:

* [Receber eventos utilizando o EventProcessorHost](event-hubs-java-get-started-receive-eph.md)
* [Descrição geral dos Hubs de Eventos][Event Hubs overview]
* [Criar um hub de eventos](event-hubs-create.md)
* [FAQ dos Hubs de Eventos](event-hubs-faq.md)

<!-- Links -->
[Event Hubs overview]: event-hubs-overview.md