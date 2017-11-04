---
title: "Exceções de mensagens do Service Bus do Azure | Microsoft Docs"
description: "Lista de exceções de mensagens do Service Bus e as ações sugeridas."
services: service-bus-messaging
documentationcenter: na
author: sethmanheim
manager: timlt
editor: 
ms.assetid: 3d8526fe-6e47-4119-9f3e-c56d916a98f9
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/12/2017
ms.author: sethm
ms.openlocfilehash: f927aa7a33a650354abd090b6280795875ab693f
ms.sourcegitcommit: 4ed3fe11c138eeed19aef0315a4f470f447eac0c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/23/2017
---
# <a name="service-bus-messaging-exceptions"></a>Exceções de mensagens do Service Bus
Este artigo apresenta uma lista de algumas exceções geradas pelo Microsoft Azure Service Bus APIs de mensagens. Esta referência está sujeita a alterações, por isso, verifique novamente para as atualizações.

## <a name="exception-categories"></a>Categorias de exceção
As APIs de mensagens geram exceções que podem enquadram-se nas seguintes categorias, juntamente com a ação associada, que pode tomar para tentar corrigir. Tenha em atenção que o significado e causas de uma exceção podem variar consoante o tipo de entidade de mensagens (filas/tópicos ou Event Hubs):

1. Erro de codificação de utilizador ([System.ArgumentException](https://msdn.microsoft.com/library/system.argumentexception.aspx), [System.InvalidOperationException](https://msdn.microsoft.com/library/system.invalidoperationexception.aspx), [System.OperationCanceledException](https://msdn.microsoft.com/library/system.operationcanceledexception.aspx), [ System.Runtime.Serialization.SerializationException](https://msdn.microsoft.com/library/system.runtime.serialization.serializationexception.aspx)). Ação geral: tentar corrigir o código antes de continuar.
2. Erro de configuração/configuração ([Microsoft.ServiceBus.Messaging.MessagingEntityNotFoundException](/dotnet/api/microsoft.azure.servicebus.messagingentitynotfoundexception), [System. unauthorizedaccessexception](https://msdn.microsoft.com/library/system.unauthorizedaccessexception.aspx). Ação geral: rever a configuração e alterar se necessário.
3. Exceções transitórias ([Microsoft.ServiceBus.Messaging.MessagingException](/dotnet/api/microsoft.servicebus.messaging.messagingexception), [Microsoft.ServiceBus.Messaging.ServerBusyException](/dotnet/api/microsoft.azure.servicebus.serverbusyexception), [ Microsoft.ServiceBus.Messaging.MessagingCommunicationException](/dotnet/api/microsoft.servicebus.messaging.messagingcommunicationexception)). Ação geral: Repita a operação ou notificar os utilizadores.
4. Outras exceções ([System.Transactions.TransactionException](https://msdn.microsoft.com/library/system.transactions.transactionexception.aspx), [System.TimeoutException](https://msdn.microsoft.com/library/system.timeoutexception.aspx), [Microsoft.ServiceBus.Messaging.MessageLockLostException](/dotnet/api/microsoft.azure.servicebus.messagelocklostexception), [Microsoft.ServiceBus.Messaging.SessionLockLostException](/dotnet/api/microsoft.azure.servicebus.sessionlocklostexception)). Ação geral: específico para o tipo de exceção; consulte a tabela na secção seguinte. 

## <a name="exception-types"></a>Tipos de exceção
A tabela seguinte lista os tipos de exceção do serviço de mensagens e os respetivos causas e ação sugerida notas que pode tomar.

| **Tipo de exceção** | **Causa/descrição/exemplos** | **Ação sugerida** | **Tenha em atenção da repetição automático imediata** |
| --- | --- | --- | --- |
| [TimeoutException](https://msdn.microsoft.com/library/system.timeoutexception.aspx) |O servidor não respondeu para a operação pedida no tempo especificado que é controlada pela [OperationTimeout](/dotnet/api/microsoft.servicebus.messaging.messagingfactorysettings#Microsoft_ServiceBus_Messaging_MessagingFactorySettings_OperationTimeout). O servidor pode concluir a operação pedida. Isto pode acontecer devido a rede ou outros atrasos de infraestrutura. |Verifique o estado do sistema de consistência e repita se necessário. Consulte [exceções de tempo limite](#timeoutexception). |Poderá ajudar repetir em alguns casos; Adicione lógica de repetição para código. |
| [InvalidOperationException](https://msdn.microsoft.com/library/system.invalidoperationexception.aspx) |A operação de utilizador solicitada não é permitida dentro do servidor ou serviço. Consulte a mensagem de exceção para obter mais detalhes. Por exemplo, [Complete()](/dotnet/api/microsoft.azure.servicebus.queueclient.completeasync) gera esta exceção se a mensagem foi recebida no [ReceiveAndDelete](/dotnet/api/microsoft.azure.servicebus.receivemode) modo. |Verifique o código e a documentação. Certifique-se que a operação pedida é válida. |Não irá ajudar a repetir. |
| [OperationCanceledException](https://msdn.microsoft.com/library/system.operationcanceledexception.aspx) |É efetuada uma tentativa de invocar uma operação num objeto que já foi fechado, abortada ou eliminado. Em casos raros, a transação ambient já foi eliminada. |Verifique o código e certifique-se de que não invocar operações num objeto eliminado. |Não irá ajudar a repetir. |
| [UnauthorizedAccessException](https://msdn.microsoft.com/library/system.unauthorizedaccessexception.aspx) |O [TokenProvider](/dotnet/api/microsoft.azure.servicebus.tokenprovider) objeto não foi possível adquirir um token, o token é inválido ou o token não contém as afirmações necessárias para efetuar a operação. |Certifique-se de que o fornecedor de tokens é criado com os valores corretos. Verifique a configuração do serviço de controlo de acesso. |Poderá ajudar repetir em alguns casos; Adicione lógica de repetição para código. |
| [ArgumentException](https://msdn.microsoft.com/library/system.argumentexception.aspx)<br /> [ArgumentNullException](https://msdn.microsoft.com/library/system.argumentnullexception.aspx)<br />[ArgumentOutOfRangeException](https://msdn.microsoft.com/library/system.argumentoutofrangeexception.aspx) |Um ou mais argumentos fornecidos ao método são inválidos.<br /> O URI fornecido a [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) ou [criar](/dotnet/api/microsoft.servicebus.messaging.messagingfactory#Microsoft_ServiceBus_Messaging_MessagingFactory_Create_System_Collections_Generic_IEnumerable_System_Uri__) contém o segmento de caminho (s).<br /> O esquema de URI fornecido a [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) ou [criar](/dotnet/api/microsoft.servicebus.messaging.messagingfactory#Microsoft_ServiceBus_Messaging_MessagingFactory_Create_System_Collections_Generic_IEnumerable_System_Uri__) é inválido. <br />O valor da propriedade é superior a 32KB. |Verifique o código de chamada e certifique-se que os argumentos estão corretos. |Não irá ajudar a repetir. |
| [MessagingEntityNotFoundException](/dotnet/api/microsoft.azure.servicebus.messagingentitynotfoundexception) |Entidade associada com a operação não existe ou foi eliminado. |Certifique-se que a entidade existe. |Não irá ajudar a repetir. |
| [MessageNotFoundException](/dotnet/api/microsoft.servicebus.messaging.messagenotfoundexception) |Tentativa de receber uma mensagem com um número de sequência específico. Esta mensagem não foi encontrada. |Certifique-se que a mensagem já não foi recebida. Certifique-se a fila de mensagens não entregues para ver se a mensagem foi deadlettered. |Não irá ajudar a repetir. |
| [MessagingCommunicationException](/dotnet/api/microsoft.servicebus.messaging.messagingcommunicationexception) |Cliente não é capaz de estabelecer uma ligação ao Service Bus. |Certifique-se de que o nome de anfitrião fornecido está correto e o anfitrião está acessível. |Pode ajudar a repetir se existirem problemas de conectividade intermitente. |
| [ServerBusyException](/dotnet/api/microsoft.azure.servicebus.serverbusyexception) |Serviço não é possível processar o pedido neste momento. |Cliente pode aguardar por um período de tempo, em seguida, repita a operação. |Cliente pode repetir após determinado intervalo. Se uma repetição resulta numa exceção de diferentes, verifique o comportamento de repetição que exceção. |
| [MessageLockLostException](/dotnet/api/microsoft.azure.servicebus.messagelocklostexception) |Token de bloqueio associada com a mensagem expirou ou o token de bloqueio não foi encontrado. |Eliminar a mensagem. |Não irá ajudar a repetir. |
| [SessionLockLostException](/dotnet/api/microsoft.azure.servicebus.sessionlocklostexception) |O bloqueio de associados a esta sessão é perdido. |Abortar o [MessageSession](/dotnet/api/microsoft.servicebus.messaging.messagesession) objeto. |Não irá ajudar a repetir. |
| [MessagingException](/dotnet/api/microsoft.servicebus.messaging.messagingexception) |Genérico mensagens exceção que pode ser emitida nos seguintes casos:<br /> É efetuada uma tentativa de criar um [QueueClient](/dotnet/api/microsoft.azure.servicebus.queueclient) utilizando um nome ou o caminho que pertence a um tipo de entidade diferente (por exemplo, um tópico).<br />  É efetuada uma tentativa para enviar uma mensagem com mais de 256KB. O servidor ou o serviço encontrou um erro durante o processamento do pedido. Consulte a mensagem de exceção para obter mais detalhes. Isto é, normalmente, uma exceção de transitória. |Verifique o código e certifique-se de que apenas os objetos serializáveis são utilizados para o corpo da mensagem (ou utilize um serializador personalizado). Consulte a documentação para os tipos de valor suportado das propriedades e apenas os tipos de utilização suportado. Verifique o [IsTransient](/dotnet/api/microsoft.servicebus.messaging.messagingexception#Microsoft_ServiceBus_Messaging_MessagingException_IsTransient) propriedade. Se for **verdadeiro**, pode tentar novamente a operação. |Comportamento de repetição não está definido e não pode ajudar. |
| [MessagingEntityAlreadyExistsException](/dotnet/api/microsoft.servicebus.messaging.messagingentityalreadyexistsexception) |Tentativa de criar uma entidade com um nome que já está a ser utilizado por outra entidade esse espaço de nomes de serviço. |Eliminar a entidade existente ou escolha outro nome para a entidade seja criado. |Não irá ajudar a repetir. |
| [QuotaExceededException](/dotnet/api/microsoft.azure.servicebus.quotaexceededexception) |A entidade de mensagens foi atingido o tamanho máximo admissível, ou foi excedido o número máximo de ligações com um espaço de nomes. |Crie espaço na entidade por receber mensagens de entidade ou as filas secundárias. Consulte [QuotaExceededException](#quotaexceededexception). |Podem ajudar a tentativas se as mensagens foram removidas entretanto. |
| [RuleActionException](/dotnet/api/microsoft.servicebus.messaging.ruleactionexception) |Barramento de serviço devolve esta exceção, se tentar criar uma ação de regra inválida. Barramento de serviço liga esta exceção a uma mensagem de deadlettered se ocorrer um erro ao processar a ação de regra para essa mensagem. |Certifique-se a ação de regra estão correctos. |Não irá ajudar a repetir. |
| [FilterException](/dotnet/api/microsoft.servicebus.messaging.filterexception) |Barramento de serviço devolve esta exceção, se tentar criar um filtro inválido. Barramento de serviço liga esta exceção a uma mensagem de deadlettered se Ocorreu um erro ao processar o filtro para essa mensagem. |Verifique o filtro estão correctos. |Não irá ajudar a repetir. |
| [SessionCannotBeLockedException](/dotnet/api/microsoft.servicebus.messaging.sessioncannotbelockedexception) |Tentar aceitar uma sessão com um ID de sessão específico, mas a sessão está atualmente bloqueada por outro cliente. |Certifique-se que a sessão está desbloqueada por outros clientes. |Pode ajudar a repetir se a sessão foi lançada no provisório. |
| [TransactionSizeExceededException](/dotnet/api/microsoft.servicebus.messaging.transactionsizeexceededexception) |Existem demasiadas operações fazem parte da transação. |Reduza o número de operações que fazem parte desta transação. |Não irá ajudar a repetir. |
| [MessagingEntityDisabledException](/dotnet/api/microsoft.azure.servicebus.messagingentitydisabledexception) |Pedido de uma operação de runtime numa entidade desativada. |Ative a entidade. |Pode ajudar a repetir se a entidade tiver sido ativada no provisório. |
| [NoMatchingSubscriptionException](/dotnet/api/microsoft.servicebus.messaging.nomatchingsubscriptionexception) |Barramento de serviço devolve esta exceção se enviar uma mensagem para um tópico que ativou previamente filtragem e corresponder a nenhum dos filtros. |Certifique-se de que corresponde a pelo menos um filtro. |Não irá ajudar a repetir. |
| [MessageSizeExceededException](/dotnet/api/microsoft.servicebus.messaging.messagesizeexceededexception) |Payload da mensagem excede o limite de 256 KB. Tenha em atenção que o limite de 256 KB é o tamanho total de mensagens, que pode incluir as propriedades do sistema e quaisquer custos de .NET. |Reduza o tamanho do payload da mensagem, em seguida, repita a operação. |Não irá ajudar a repetir. |
| [TransactionException](https://msdn.microsoft.com/library/system.transactions.transactionexception.aspx) |A transação ambient (*Current*) é inválido. Poderá ter foi concluída ou abortada. A exceção interna pode fornecer informações adicionais. | |Não irá ajudar a repetir. |
| [TransactionInDoubtException](https://msdn.microsoft.com/library/system.transactions.transactionindoubtexception.aspx) |Uma operação é tentada numa transação que está em dúvida, ou é efetuada uma tentativa para consolidar a transação e a transação torna-se em dúvida. |A aplicação deve processar esta exceção (como num caso especial), como a transação pode já ter sido consolidada. |- |

## <a name="quotaexceededexception"></a>QuotaExceededException
[QuotaExceededException](/dotnet/api/microsoft.azure.servicebus.quotaexceededexception) indica que uma quota para uma entidade específica foi excedida.

### <a name="queues-and-topics"></a>As filas e tópicos
Para filas e tópicos, isto é, muitas vezes, o tamanho da fila. A propriedade de mensagem de erro contém mais detalhes, como no exemplo seguinte:

```
Microsoft.ServiceBus.Messaging.QuotaExceededException
Message: The maximum entity size has been reached or exceeded for Topic: ‘xxx-xxx-xxx’. 
    Size of entity in bytes:1073742326, Max entity size in bytes:
1073741824..TrackingId:xxxxxxxxxxxxxxxxxxxxxxxxxx, TimeStamp:3/15/2013 7:50:18 AM
```

A mensagem indica que o tópico excedeu o limite de tamanho, neste cenário 1 GB (o limite do tamanho predefinido). 

### <a name="namespaces"></a>Espaços de nomes

Para espaços de nomes, [QuotaExceededException](/dotnet/api/microsoft.azure.servicebus.quotaexceededexception) pode indicar que uma aplicação excedeu o número máximo de ligações com um espaço de nomes. Por exemplo:

```
Microsoft.ServiceBus.Messaging.QuotaExceededException: ConnectionsQuotaExceeded for namespace xxx.
<tracking-id-guid>_G12 ---> 
System.ServiceModel.FaultException`1[System.ServiceModel.ExceptionDetail]: 
ConnectionsQuotaExceeded for namespace xxx.
```

#### <a name="common-causes"></a>Causas comuns
Existem duas causas comuns deste erro: a fila de entregues e recetores de mensagem não está em funcionamento.

1. **[Fila de entregues](service-bus-dead-letter-queues.md)**  um leitor está a conseguir concluir mensagens e as mensagens são devolvidas para o tópico/fila quando o bloqueio expira. Isto pode acontecer se o leitor de detetar uma exceção que o impede de chamar [BrokeredMessage.Complete](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.complete). Depois de uma mensagem lidas 10 vezes, são transmitidos para a fila de entregues por predefinição. Este comportamento é controlado pelo [QueueDescription.MaxDeliveryCount](/dotnet/api/microsoft.servicebus.messaging.queuedescription.maxdeliverycount) propriedade e tem um valor predefinido de 10. Como mensagens pile cópias de segurança da fila de entregues, podem demorar até espaço.
   
    Para resolver o problema, ler e conclua as mensagens da fila entregues, tal como faria com qualquer outra fila. Pode utilizar o [FormatDeadLetterPath](/dotnet/api/microsoft.azure.servicebus.entitynamehelper.formatdeadletterpath) método para ajudar a formatar o caminho de fila de entregues.
2. **Recetor parado** um recetor parou a receção de mensagens de uma fila ou a subscrição. A forma de identificar esta está a observar a [QueueDescription.MessageCountDetails](/dotnet/api/microsoft.servicebus.messaging.messagecountdetails) propriedade, que mostra a repartição total das mensagens. Se o [ActiveMessageCount](/dotnet/api/microsoft.servicebus.messaging.messagecountdetails.activemessagecount) propriedade é alta ou crescente, em seguida, as mensagens não estão a ser lidos tão rápido estão a ser escritos.

### <a name="event-hubs"></a>Event Hubs
Os Event Hubs tem um limite de 20 grupos de consumidores do Hub de eventos. Ao tentar criar mais, recebe um [QuotaExceededException](/dotnet/api/microsoft.servicebus.messaging.quotaexceededexception). 

## <a name="timeoutexception"></a>TimeoutException
A [TimeoutException](https://msdn.microsoft.com/library/system.timeoutexception.aspx) indica que uma operação iniciada pelo utilizador está a demorar mais do que o tempo limite da operação. 

Deve verificar o valor da [ServicePointManager.DefaultConnectionLimit](https://msdn.microsoft.com/library/system.net.servicepointmanager.defaultconnectionlimit) propriedade, como atingir este limite também pode fazer com que um [TimeoutException](https://msdn.microsoft.com/library/system.timeoutexception.aspx).

### <a name="queues-and-topics"></a>As filas e tópicos
O tempo limite, para filas e tópicos, é especificado no [MessagingFactorySettings.OperationTimeout](/dotnet/api/microsoft.servicebus.messaging.messagingfactorysettings#Microsoft_ServiceBus_Messaging_MessagingFactorySettings_OperationTimeout) propriedade, como parte da cadeia de ligação ou através de [ServiceBusConnectionStringBuilder](/dotnet/api/microsoft.azure.servicebus.servicebusconnectionstringbuilder). A própria mensagem de erro pode variar, mas contém sempre o valor de tempo limite especificado para a operação atual. 



## <a name="next-steps"></a>Passos seguintes

Para referência de API .NET do Service Bus completa, consulte o [referência da API de .NET do Azure](/dotnet/api/overview/azure/service-bus).

Para saber mais sobre [Service Bus](https://azure.microsoft.com/services/service-bus/), consulte os artigos seguintes:

* [Descrição geral das mensagens do Service Bus](service-bus-messaging-overview.md)
* [Noções básicas sobre o Service Bus](service-bus-fundamentals-hybrid-solutions.md)
* [Arquitetura do Service Bus](service-bus-architecture.md)

