---
title: "Os Hubs de eventos do Azure mensagens exceções | Microsoft Docs"
description: "Lista de exceções e as ações sugeridas de mensagens de Event Hubs do Azure."
services: event-hubs
documentationcenter: na
author: sethmanheim
manager: timlt
editor: 
ms.assetid: 2c6273de-0106-47e5-b45d-59040e51f2c5
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/15/2017
ms.author: sethm
ms.openlocfilehash: 1a5922506a0db4277b205ba3390c9c30034c177d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="event-hubs-messaging-exceptions"></a>Exceções de mensagens dos Hubs de Eventos
Este artigo apresenta uma lista algumas das exceções geradas pelo mensagens APIs, que incluem os Event Hubs do Azure Service Bus. Esta referência está sujeita a alterações, por isso, verifique novamente para as atualizações.

## <a name="exception-categories"></a>Categorias de exceção
As APIs de Hubs de eventos gerar exceções que podem enquadram-se nas seguintes categorias, juntamente com a ação associada, que pode tomar para tentar corrigir.

1. Erro de codificação de utilizador: [System.ArgumentException](https://msdn.microsoft.com/library/system.argumentexception.aspx), [System.InvalidOperationException](https://msdn.microsoft.com/library/system.invalidoperationexception.aspx), [System.OperationCanceledException](https://msdn.microsoft.com/library/system.operationcanceledexception.aspx), [System.Runtime.Serialization.SerializationException](https://msdn.microsoft.com/library/system.runtime.serialization.serializationexception.aspx). Ação geral: tentar corrigir o código antes de continuar.
2. Erro de configuração/configuração: [Microsoft.ServiceBus.Messaging.MessagingEntityNotFoundException](/dotnet/api/microsoft.servicebus.messaging.messagingentitynotfoundexception), [Microsoft.Azure.EventHubs.MessagingEntityNotFoundException](/dotnet/api/microsoft.azure.eventhubs.messagingentitynotfoundexception), [System. unauthorizedaccessexception](https://msdn.microsoft.com/library/system.unauthorizedaccessexception.aspx). Ação geral: rever a configuração e alterar se necessário.
3. Exceções transitórias: [Microsoft.ServiceBus.Messaging.MessagingException](/dotnet/api/microsoft.servicebus.messaging.messagingexception), [Microsoft.ServiceBus.Messaging.ServerBusyException](#serverbusyexception), [Microsoft.Azure.EventHubs.ServerBusyException](#serverbusyexception), [Microsoft.ServiceBus.Messaging.MessagingCommunicationException](/dotnet/api/microsoft.servicebus.messaging.messagingcommunicationexception). Ação geral: Repita a operação ou notificar os utilizadores.
4. Outras exceções: [System.Transactions.TransactionException](https://msdn.microsoft.com/library/system.transactions.transactionexception.aspx), [System.TimeoutException](#timeoutexception), [Microsoft.ServiceBus.Messaging.MessageLockLostException](/dotnet/api/microsoft.servicebus.messaging.messagelocklostexception), [Microsoft.ServiceBus.Messaging.SessionLockLostException](/dotnet/api/microsoft.servicebus.messaging.sessionlocklostexception). Ação geral: específico para o tipo de exceção; consulte a tabela na secção seguinte. 

## <a name="exception-types"></a>Tipos de exceção
A tabela seguinte lista os tipos de exceção do serviço de mensagens e os respetivos causas e ação sugerida notas que pode tomar.

| **Tipo de exceção** | **Causa/descrição/exemplos** | **Ação sugerida** | **Tenha em atenção da repetição automático imediata** |
| --- | --- | --- | --- |
| [TimeoutException](https://msdn.microsoft.com/library/system.timeoutexception.aspx) |O servidor não respondeu para a operação pedida no tempo especificado, que é controlada pela [OperationTimeout](/dotnet/api/microsoft.servicebus.messaging.messagingfactorysettings#Microsoft_ServiceBus_Messaging_MessagingFactorySettings_OperationTimeout). O servidor pode concluir a operação pedida. Isto pode acontecer devido a rede ou outros atrasos de infraestrutura. |Verifique o estado do sistema de consistência e repita se necessário.<br /> Consulte [TimeoutException](#timeoutexception). |Poderá ajudar repetir em alguns casos; Adicione lógica de repetição para código. |
| [InvalidOperationException](https://msdn.microsoft.com/library/system.invalidoperationexception.aspx) |A operação de utilizador solicitada não é permitida dentro do servidor ou serviço. Consulte a mensagem de exceção para obter mais detalhes. Por exemplo, [concluída](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage#Microsoft_ServiceBus_Messaging_BrokeredMessage_Complete) gera esta exceção se a mensagem foi recebida no [ReceiveAndDelete](/dotnet/api/microsoft.servicebus.messaging.receivemode) modo. |Verifique o código e a documentação. Certifique-se que a operação pedida é válida. |Não irá ajudar a repetir. |
| [OperationCanceledException](https://msdn.microsoft.com/library/system.operationcanceledexception.aspx) |É efetuada uma tentativa de invocar uma operação num objeto que já foi fechado, abortada ou eliminado. Em casos raros, a transação ambient já foi eliminada. |Verifique o código e certifique-se de que não invocar operações num objeto eliminado. |Não irá ajudar a repetir. |
| [UnauthorizedAccessException](https://msdn.microsoft.com/library/system.unauthorizedaccessexception.aspx) |O [TokenProvider](/dotnet/api/microsoft.servicebus.tokenprovider) objeto não foi possível adquirir um token, o token é inválido ou o token não contém as afirmações necessárias para efetuar a operação. |Certifique-se de que o fornecedor de tokens é criado com os valores corretos. Verifique a configuração do serviço de controlo de acesso. |Poderá ajudar repetir em alguns casos; Adicione lógica de repetição para código. |
| [ArgumentException](https://msdn.microsoft.com/library/system.argumentexception.aspx)<br /> [ArgumentNullException](https://msdn.microsoft.com/library/system.argumentnullexception.aspx)<br />[ArgumentOutOfRangeException](https://msdn.microsoft.com/library/system.argumentoutofrangeexception.aspx) |Um ou mais argumentos fornecidos ao método são inválidos. O URI fornecido a [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) ou [criar](/dotnet/api/microsoft.servicebus.messaging.messagingfactory#Microsoft_ServiceBus_Messaging_MessagingFactory_Create_System_Collections_Generic_IEnumerable_System_Uri__) contém o segmento de caminho (s). O esquema de URI fornecido a [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) ou [criar](/dotnet/api/microsoft.servicebus.messaging.messagingfactory#Microsoft_ServiceBus_Messaging_MessagingFactory_Create_System_Collections_Generic_IEnumerable_System_Uri__) é inválido. O valor da propriedade é superior a 32KB. |Verifique o código de chamada e certifique-se que os argumentos estão corretos. |Não irá ajudar a repetir. |
| [Microsoft.ServiceBus.Messaging.MessagingEntityNotFoundException](/dotnet/api/microsoft.servicebus.messaging.messagingentitynotfoundexception) <br /> [Microsoft.Azure.EventHubs.MessagingEntityNotFoundException](/dotnet/api/microsoft.azure.eventhubs.messagingentitynotfoundexception) |Entidade associada com a operação não existe ou foi eliminado. |Certifique-se que a entidade existe. |Não irá ajudar a repetir. |
| [MessagingCommunicationException](/dotnet/api/microsoft.servicebus.messaging.messagingcommunicationexception) |Cliente não é capaz de estabelecer uma ligação ao Hub de eventos. |Certifique-se de que o nome de anfitrião fornecido está correto e o anfitrião está acessível. |Pode ajudar a repetir se existirem problemas de conectividade intermitente. |
| [Microsoft.ServiceBus.Messaging.ServerBusyException](/dotnet/api/microsoft.servicebus.messaging.serverbusyexception) <br /> [Microsoft.Azure.EventHubs.ServerBusyException](/dotnet/api/microsoft.azure.eventhubs.serverbusyexception) |Serviço não é possível processar o pedido neste momento. |Cliente pode aguardar por um período de tempo, em seguida, repita a operação. <br /> Consulte [ServerBusyException](#serverbusyexception). |Cliente pode repetir após determinado intervalo. Se uma repetição resulta numa exceção de diferentes, verifique o comportamento de repetição que exceção. |
| [MessagingException](/dotnet/api/microsoft.servicebus.messaging.messagingexception) |Genérico mensagens exceção que pode ser emitida nos seguintes casos: é efetuada uma tentativa de criar um [QueueClient](/dotnet/api/microsoft.servicebus.messaging.queueclient) utilizando um nome ou o caminho que pertence a um tipo de entidade diferente (por exemplo, um tópico). É efetuada uma tentativa para enviar uma mensagem com mais de 256KB. O servidor ou o serviço encontrou um erro durante o processamento do pedido. Consulte a mensagem de exceção para obter mais detalhes. Isto é, normalmente, uma exceção de transitória. |Verifique o código e certifique-se de que apenas os objetos serializáveis são utilizados para o corpo da mensagem (ou utilize um serializador personalizado). Consulte a documentação para os tipos de valor suportado das propriedades e apenas os tipos de utilização suportado. Verifique o [IsTransient](/dotnet/api/microsoft.servicebus.messaging.messagingexception#Microsoft_ServiceBus_Messaging_MessagingException_IsTransient) propriedade. Se for **verdadeiro**, pode tentar novamente a operação. |Comportamento de repetição não está definido e não pode ajudar. |
| [MessagingEntityAlreadyExistsException](/dotnet/api/microsoft.servicebus.messaging.messagingentityalreadyexistsexception) |Tentativa de criar uma entidade com um nome que já está a ser utilizado por outra entidade esse espaço de nomes de serviço. |Eliminar a entidade existente ou escolha outro nome para a entidade seja criado. |Não irá ajudar a repetir. |
| [QuotaExceededException](/dotnet/api/microsoft.servicebus.messaging.quotaexceededexception) |A entidade de mensagens foi atingido o tamanho máximo permitido. Isto pode acontecer se o número máximo de recetores (que é 5) já foi aberto um nível de grupo por consumidor. |Crie espaço na entidade por receber mensagens de entidade ou as filas secundárias. <br /> Consulte [QuotaExceededException](#quotaexceededexception) |Podem ajudar a tentativas se as mensagens foram removidas entretanto. |
|  | | | |
| [MessagingEntityDisabledException](/dotnet/api/microsoft.servicebus.messaging.messagingentitydisabledexception) |Pedido de uma operação de runtime numa entidade desativada. |Ative a entidade. |Pode ajudar a repetir se a entidade tiver sido ativada no provisório. |
| [Microsoft.ServiceBus.Messaging.MessageSizeExceededException](/dotnet/api/microsoft.servicebus.messaging.messagesizeexceededexception) <br /> [Microsoft.Azure.EventHubs.MessageSizeExceededException](/dotnet/api/microsoft.azure.eventhubs.messagesizeexceededexception) | Payload da mensagem excede o limite de 256K. Tenha em atenção que o limite de k 256 é o tamanho total de mensagens, que pode incluir as propriedades do sistema e quaisquer custos de .NET. |Reduza o tamanho do payload da mensagem, em seguida, repita a operação. |Não irá ajudar a repetir. |

## <a name="quotaexceededexception"></a>QuotaExceededException
[QuotaExceededException](/dotnet/api/microsoft.servicebus.messaging.quotaexceededexception) indica que uma quota para uma entidade específica foi excedida.

Isto pode acontecer se o número máximo de recetores (5) já foi aberto um nível de grupo por consumidor.

### <a name="event-hubs"></a>Event Hubs
Os Event Hubs tem um limite de 20 grupos de consumidores do Hub de eventos. Ao tentar criar mais, recebe um [QuotaExceededException](/dotnet/api/microsoft.servicebus.messaging.quotaexceededexception). 

## <a name="timeoutexception"></a>TimeoutException
A [TimeoutException](https://msdn.microsoft.com/library/system.timeoutexception.aspx) indica que uma operação iniciada pelo utilizador está a demorar mais do que o tempo limite da operação. 

Para os Event Hubs, o tempo limite especificado como parte da cadeia de ligação ou através de [ServiceBusConnectionStringBuilder](/dotnet/api/microsoft.servicebus.servicebusconnectionstringbuilder). A própria mensagem de erro pode variar, mas contém sempre o valor de tempo limite especificado para a operação atual. 

### <a name="common-causes"></a>Causas comuns
Existem duas causas comuns deste erro: configuração incorreta ou um erro no serviço transitório.

1. **Configuração incorreta** o tempo limite da operação poderá ser demasiado pequeno para a condição operacional. O valor predefinido para o tempo limite da operação no cliente do SDK é 60 segundos. Verifique se o seu código tem o valor definido para algo demasiado pequena. Tenha em atenção que a condição de rede e a utilização da CPU pode afetar o tempo que demora para uma operação específica concluir, pelo que o tempo limite da operação não deve ser definido para um valor muito pequeno.
2. **Erro no serviço transitório** , por vezes, o serviço de Event Hubs pode ocorrerem atrasos no processamento de pedidos; por exemplo, durante períodos de tráfego elevado. Nestes casos, pode tentar novamente a operação depois de um atraso até a operação for concluída com êxito. Se a mesma operação continuar a falhar após várias tentativas, visite o [site de estado do serviço do Azure](https://azure.microsoft.com/status/) para ver se existem quaisquer falhas no serviço conhecido.

## <a name="serverbusyexception"></a>ServerBusyException

A [Microsoft.ServiceBus.Messaging.ServerBusyException](/dotnet/api/microsoft.servicebus.messaging.serverbusyexception) ou [Microsoft.Azure.EventHubs.ServerBusyException](/dotnet/api/microsoft.azure.eventhubs.serverbusyexception) indica que um servidor está sobrecarregado. Existem dois códigos de erro relevantes para esta exceção.

### <a name="error-code-50002"></a>Código de erro 50002

Este erro pode ocorrer por uma das duas razões:

1. A carga não é distribuída uniformemente em todas as partições num Hub de eventos e uma partição chega a limitação de unidade de débito local.
    
    Resolução: Dão origem a estratégia de distribuição de partição ou tentar [EventHubClient.Send(eventDataWithOutPartitionKey)](/dotnet/api/microsoft.servicebus.messaging.eventhubclient#Microsoft_ServiceBus_Messaging_EventHubClient_Send_Microsoft_ServiceBus_Messaging_EventData_) poderem ajudá-lo.

2. O espaço de nomes de Event Hubs não tem unidades de débito suficiente (pode verificar o **métricas** painel no painel de espaço de nomes de Event Hubs no [portal do Azure](https://portal.azure.com) para confirmar). Tenha em atenção que o portal mostra informações de agregados (1 minuto), mas estamos a medir o débito em tempo real – para que fique apenas uma estimativa.

    Resolução: Aumentar as unidades de débito no espaço de nomes pode ajudar. Pode fazer isto no portal, além de **escala** painel do painel de espaço de nomes de Event Hubs.

### <a name="error-code-50001"></a>Código de erro 50001

Este erro raramente deve ocorrer. Ocorre quando o contentor com o código para o espaço de nomes é baixa na CPU – começa não mais do que alguns segundos antes dos Event Hubs Balanceador de carga.


## <a name="next-steps"></a>Passos seguintes
Pode saber mais sobre os Hubs de Eventos ao aceder às seguintes ligações:

* [Descrição geral dos Hubs de Eventos](event-hubs-what-is-event-hubs.md)
* [Criar um Hub de Eventos](event-hubs-create.md)
* [FAQ dos Hubs de Eventos](event-hubs-faq.md)
