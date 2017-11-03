---
title: "Exceções de reencaminhamento do Azure e como resolvê-los | Microsoft Docs"
description: "Obter uma lista de exceções de reencaminhamento do Azure e as ações sugeridas que pode tomar para ajudar a resolvê-los."
services: service-bus-relay
documentationcenter: na
author: sethmanheim
manager: timlt
editor: 
ms.assetid: 5f9dd02c-cce0-43b3-8eb8-744f0c27f38c
ms.service: service-bus-relay
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/23/2017
ms.author: sethm
ms.openlocfilehash: 83ff97b59e428e7b617a7f5d1011ca5ddf3060b6
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="azure-relay-exceptions"></a>Exceções de reencaminhamento do Azure

Este artigo apresenta uma lista de algumas exceções que podem ser geradas através das APIs de reencaminhamento do Azure. Esta referência está sujeita a alterações, por isso, verifique novamente para as atualizações.

## <a name="exception-categories"></a>Categorias de exceção

As APIs de reencaminhamento gere exceções que poderão enquadram-se nas categorias seguintes. Também incluídos estão as ações sugeridas que pode tomar para ajudar a resolver as exceções.

*   **Erro de codificação de utilizador**: [System.ArgumentException](https://msdn.microsoft.com/library/system.argumentexception.aspx), [System.InvalidOperationException](https://msdn.microsoft.com/library/system.invalidoperationexception.aspx), [System.OperationCanceledException](https://msdn.microsoft.com/library/system.operationcanceledexception.aspx), [ System.Runtime.Serialization.SerializationException](https://msdn.microsoft.com/library/system.runtime.serialization.serializationexception.aspx). 

    **Ação geral**: tentar corrigir o código antes de continuar.
*   **Erro de configuração/configuração**: [System. unauthorizedaccessexception](https://msdn.microsoft.com/library/system.unauthorizedaccessexception.aspx). 

    **Ação geral**: Reveja a configuração. Se necessário, altere a configuração.
*   **Exceções transitórias**: [Microsoft.ServiceBus.Messaging.MessagingException](/dotnet/api/microsoft.servicebus.messaging.messagingexception), [Microsoft.ServiceBus.Messaging.ServerBusyException](/dotnet/api/microsoft.servicebus.messaging.serverbusyexception), [ Microsoft.ServiceBus.Messaging.MessagingCommunicationException](/dotnet/api/microsoft.servicebus.messaging.messagingcommunicationexception). 

    **Ação geral**: Repita a operação ou notificar os utilizadores.
*   **Outras exceções**: [System.Transactions.TransactionException](https://msdn.microsoft.com/library/system.transactions.transactionexception.aspx), [System.TimeoutException](https://msdn.microsoft.com/library/system.timeoutexception.aspx). 

    **Ação geral**: específico para o tipo de exceção. Consulte a tabela na secção seguinte. 

## <a name="exception-types"></a>Tipos de exceção

A tabela seguinte lista os tipos de exceção mensagens e das suas causas. Indica também a sugeridos ações que pode tomar para ajudar a resolver as exceções.

| **Tipo de exceção** | **Descrição** | **Ação sugerida** | **Tenha em atenção da repetição automática ou imediata** |
| --- | --- | --- | --- |
| [Tempo limite](https://msdn.microsoft.com/library/system.timeoutexception.aspx) |O servidor não respondeu para a operação pedida no tempo especificado, que é controlada pela [OperationTimeout](/dotnet/api/microsoft.servicebus.messaging.messagingfactorysettings.operationtimeout). O servidor poderá concluir a operação pedida. Isto pode acontecer devido a rede ou outros atrasos de infraestrutura. |Verifique o estado do sistema de consistência e, em seguida, tentar novamente, se necessário. Consulte [TimeoutException](#timeoutexception). |Poderá ajudar repetir em alguns casos; Adicione lógica de repetição para código. |
| [Operação inválida](https://msdn.microsoft.com/library/system.invalidoperationexception.aspx) |A operação de utilizador solicitada não é permitida dentro do servidor ou serviço. Consulte a mensagem de exceção para obter mais detalhes. |Verifique o código e a documentação. Certifique-se de que a operação pedida é válida. |Não irá ajudar a repetir. |
| [Operação cancelada](https://msdn.microsoft.com/library/system.operationcanceledexception.aspx) |É efetuada uma tentativa de invocar uma operação num objeto que já foi fechado, abortado ou eliminado. Em casos raros, a transação ambient já foi eliminada. |Verifique o código e certifique-se de que não invocar operações num objeto eliminado. |Não irá ajudar a repetir. |
| [Acesso não autorizado](https://msdn.microsoft.com/library/system.unauthorizedaccessexception.aspx) |O [TokenProvider](/dotnet/api/microsoft.servicebus.tokenprovider) objeto não foi possível adquirir um token, o token é inválido ou o token não contém as afirmações necessárias para efetuar a operação. |Certifique-se de que o fornecedor de tokens é criado com os valores corretos. Verifique a configuração do serviço de controlo de acesso. |Poderá ajudar repetir em alguns casos; Adicione lógica de repetição para código. |
| [Exceção de argumento](https://msdn.microsoft.com/library/system.argumentexception.aspx),<br /> [O argumento nulo](https://msdn.microsoft.com/library/system.argumentnullexception.aspx),<br />[Argumento fora do intervalo](https://msdn.microsoft.com/library/system.argumentoutofrangeexception.aspx) |Ocorreu um ou mais dos seguintes procedimentos:<br />Um ou mais argumentos fornecidos ao método são inválidos.<br /> O URI fornecido a [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) ou [criar](/dotnet/api/microsoft.servicebus.messaging.messagingfactory.create) contém um ou mais segmentos de caminho.<br />O esquema de URI fornecido a [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) ou [criar](/dotnet/api/microsoft.servicebus.messaging.messagingfactory.create) é inválido. <br />O valor da propriedade é superior a 32 KB. |Verifique o código de chamada e certifique-se que os argumentos estão corretos. |Não irá ajudar a repetir. |
| [Servidor ocupado](/dotnet/api/microsoft.servicebus.messaging.serverbusyexception) |Serviço não é possível processar o pedido neste momento. |O cliente pode aguardar por um período de tempo, em seguida, repita a operação. |O cliente pode repetir após um intervalo específico. Se os resultados de repetição numa exceção de diferentes, verifique o comportamento de repetição que exceção. |
| [Quota foi excedida](/dotnet/api/microsoft.servicebus.messaging.quotaexceededexception) |A entidade de mensagens foi atingido o tamanho máximo permitido. |Crie espaço na entidade por receber mensagens de entidade ou o respetivas subfilas. Consulte [QuotaExceededException](#quotaexceededexception). |Podem ajudar a tentativas se as mensagens foram removidas entretanto. |
| [Excedida o tamanho da mensagem](/dotnet/api/microsoft.servicebus.messaging.messagesizeexceededexception) |Payload da mensagem excede o limite de 256 KB. Tenha em atenção que o limite de 256 KB é o tamanho total de mensagens. O tamanho total de mensagens pode incluir propriedades do sistema e quaisquer custos de Microsoft .NET. |Reduza o tamanho do payload da mensagem, em seguida, repita a operação. |Não irá ajudar a repetir. |

## <a name="quotaexceededexception"></a>QuotaExceededException

[QuotaExceededException](/dotnet/api/microsoft.servicebus.messaging.quotaexceededexception) indica que uma quota para uma entidade específica foi excedida.

Para reencaminhamento, esta exceção encapsula num wrapper a [System.ServiceModel.QuotaExceededException](https://msdn.microsoft.com/library/system.servicemodel.quotaexceededexception.aspx), que indica que foi excedido o número máximo de serviços de escuta para este ponto final. Esta situação é indicada no **MaximumListenersPerEndpoint** valor da mensagem de exceção.

## <a name="timeoutexception"></a>TimeoutException
A [TimeoutException](https://msdn.microsoft.com/library/system.timeoutexception.aspx) indica que uma operação iniciada pelo utilizador está a demorar mais do que o tempo limite da operação. 

Verifique o valor da [ServicePointManager.DefaultConnectionLimit](https://msdn.microsoft.com/library/system.net.servicepointmanager.defaultconnectionlimit) propriedade. Atingir este limite também pode provocar uma [TimeoutException](https://msdn.microsoft.com/library/system.timeoutexception.aspx).

Para reencaminhamento, poderá receber exceções de tempo limite primeira vez que abrir uma ligação de remetente de reencaminhamento. Existem duas causas comuns para esta exceção:

*   O [OpenTimeout](https://msdn.microsoft.com/library/wcf.opentimeout.aspx) valor pode ser demasiado pequeno (se, mesmo ao fração de um segundo).
* Um serviço de escuta de reencaminhamento no local poderá responder (ou podem ocorrer problemas de regras de firewall que proíbem os serviços de escuta de aceitar novas ligações de cliente) e o [OpenTimeout](https://msdn.microsoft.com/library/wcf.opentimeout.aspx) valor é menor que cerca de 20 segundos.

Exemplo:

```
'System.TimeoutException’: The operation did not complete within the allotted timeout of 00:00:10.
The time allotted to this operation may have been a portion of a longer timeout.
```

### <a name="common-causes"></a>Causas comuns
Existem duas causas comuns deste erro:

*   **Configuração incorreta**
    
    O tempo limite da operação poderá ser demasiado pequeno para a condição operacional. O valor predefinido para o tempo limite da operação no cliente do SDK é 60 segundos. Verifique se o valor no seu código é definido para algo demasiado pequena. Tenha em atenção que a utilização da CPU e a condição da rede podem afetar o tempo que demora para uma operação seja concluída. É uma boa ideia não definir o tempo limite da operação para um valor muito pequeno.
*   **Erro transitório de serviço**

    Ocasionalmente, o serviço de reencaminhamento pode ocorrer atrasos no processamento de pedidos. Isto pode acontecer, por exemplo, durante períodos de tráfego elevado. Se isto ocorrer, repita a operação depois de um atraso até a operação for concluída com êxito. Se a mesma operação continuar a falhar após várias tentativas, verifique o [site de estado do serviço do Azure](https://azure.microsoft.com/status/) para ver se reconhecidamente falhas no serviço.

## <a name="next-steps"></a>Passos seguintes
* [Perguntas mais frequentes do reencaminhamento do Azure](relay-faq.md)
* [Criar um espaço de nomes de reencaminhamento](relay-create-namespace-portal.md)
* [Introdução ao Azure reencaminhamento e .NET](relay-hybrid-connections-dotnet-get-started.md)
* [Começar com o reencaminhamento do Azure e o nó](relay-hybrid-connections-node-get-started.md)

