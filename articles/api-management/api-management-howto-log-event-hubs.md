---
title: Como registar eventos aos Hubs de eventos do Azure na API Management do Azure | Microsoft Docs
description: Saiba como registar eventos aos Hubs de eventos do Azure na API Management do Azure.
services: api-management
documentationcenter: 
author: vladvino
manager: erikre
editor: 
ms.assetid: 88f6507d-7460-4eb2-bffd-76025b73f8c4
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/15/2016
ms.author: apimpm
ms.openlocfilehash: 1aba03102dcd96753ef4db57edce889a43e4e3fc
ms.sourcegitcommit: 5735491874429ba19607f5f81cd4823e4d8c8206
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/16/2017
---
# <a name="how-to-log-events-to-azure-event-hubs-in-azure-api-management"></a>Como registar eventos aos Hubs de eventos do Azure na API Management do Azure
Os Event Hubs do Azure são um serviço de entrada de dados altamente dimensionável, que pode ingerir milhões de eventos por segundo para que possa processar e analisar os quantidades enormes de dados produzidos pelos dispositivos e aplicações ligados. Os Event Hubs atuam como a "porta da frente" para um pipeline de eventos e, depois dos dados são recolhidos para um hub de eventos, podem ser transformado e armazenados através de qualquer fornecedor de análise em tempo real ou adaptadores de criação de batches/armazenamento. Os Event Hubs desacoplam a produção de um fluxo de eventos do consumo desses eventos, para que os consumidores de eventos possam aceder aos eventos de acordo com seu próprio agendamento.

Este artigo é um complemento para a [integrar a gestão de API do Azure com os Event Hubs](https://azure.microsoft.com/documentation/videos/integrate-azure-api-management-with-event-hubs/) vídeo e descreve como registar eventos de API Management com o Event Hubs do Azure.

## <a name="create-an-azure-event-hub"></a>Criar um Hub de eventos do Azure
Para criar um novo Hub de eventos, início de sessão para o [portal clássico do Azure](https://manage.windowsazure.com) e clique em **novo**->**serviços aplicacionais**->**Service Bus**->**Hub de eventos**->**criação rápida**. Introduza um nome de Hub de eventos, região, selecione uma subscrição e selecione um espaço de nomes. Se ainda não criou anteriormente um espaço de nomes pode criar um, escrevendo um nome do **espaço de nomes** caixa de texto. Depois de todas as propriedades estiverem configuradas, clique em **criar um novo Hub de eventos** para criar o Hub de eventos.

![Criar o hub de eventos][create-event-hub]

Em seguida, navegue para o **configurar** separador para o seu novo Hub de eventos e criar dois **partilhado políticas de acesso**. Nome do primeiro **Sending** e atribua-lhe **enviar** permissões.

![Política de envio][sending-policy]

Nome da segunda **receber**, atribua-lhe **escutar** permissões e clique em **guardar**.

![Receber a política][receiving-policy]

Cada política de acesso partilhado permite que aplicações enviar e receber eventos de e para o Hub de eventos. Para aceder às cadeias de ligação para estas políticas, navegue para o **Dashboard** separador do Hub de eventos e clique em **informações de ligação**.

![Cadeia de ligação][event-hub-dashboard]

O **Sending** cadeia de ligação é utilizada quando o registo de eventos e o **receber** cadeia de ligação é utilizada ao transferir eventos a partir do Event Hub.

![Cadeia de ligação][event-hub-connection-string]

## <a name="create-an-api-management-logger"></a>Criar um registo de API Management
Agora que tem um Hub de eventos, o passo seguinte consiste em configurar um [registador](https://docs.microsoft.com/rest/api/apimanagement/apimanagementrest/azure-api-management-rest-api-logger-entity) na gestão de API do serviço para que possa registar eventos para o Hub de eventos.

Registadores de API Management são configurados utilizando o [API de REST de gestão de API](http://aka.ms/smapi). Antes de utilizar a API REST pela primeira vez, reveja o [pré-requisitos](https://docs.microsoft.com/rest/api/apimanagement/apimanagementrest/api-management-rest#Prerequisites) e certifique-se de que tem [ativou o acesso à REST API](https://docs.microsoft.com/rest/api/apimanagement/apimanagementrest/api-management-rest#EnableRESTAPI).

Para criar um registo, efetue um pedido de HTTP PUT de mensagens em fila utilizando o modelo de URL seguinte.

`https://{your service}.management.azure-api.net/loggers/{new logger name}?api-version=2014-02-14-preview`

* Substitua `{your service}` com o nome da sua instância do serviço de API Management.
* Substitua `{new logger name}` com o nome pretendido para o novo registo. Irá referenciar este nome ao configurar o [registo-eventhub](https://msdn.microsoft.com/library/azure/dn894085.aspx#log-to-eventhub) política

Adicione os seguintes cabeçalhos para o pedido.

* Tipo de conteúdo: application/json
* Autorização: SharedAccessSignature 58...
  * Para obter instruções sobre a gerar o `SharedAccessSignature` consulte [autenticação de API do REST de gestão do Azure API](https://docs.microsoft.com/rest/api/apimanagement/apimanagementrest/azure-api-management-rest-api-authentication).

Especifique o corpo do pedido utilizando o modelo seguinte.

```json
{
  "type" : "AzureEventHub",
  "description" : "Sample logger description",
  "credentials" : {
    "name" : "Name of the Event Hub from the Azure Classic Portal",
    "connectionString" : "Endpoint=Event Hub Sender connection string"
    }
}
```

* `type`tem de ser definido como `AzureEventHub`.
* `description`Fornece uma descrição opcional do registo e pode ser uma cadeia de comprimento zero se assim o desejar.
* `credentials`contém o `name` e `connectionString` do seu Hub de eventos do Azure.

Quando efetua o pedido, se o registo é criado um código de estado de `201 Created` é devolvido.

> [!NOTE]
> Para outros códigos de retorno possíveis e os respetivos motivos, consulte [criar um registo](https://docs.microsoft.com/rest/api/apimanagement/apimanagementrest/azure-api-management-rest-api-logger-entity#PUT). Para ver como efetuar outras operações como a lista, update e delete, consulte o [registador](https://docs.microsoft.com/rest/api/apimanagement/apimanagementrest/azure-api-management-rest-api-logger-entity) documentação de entidade.
>
>

## <a name="configure-log-to-eventhubs-policies"></a>Configurar políticas de registo-eventhubs
Depois de ter configurado o registo na API Management, pode configurar as políticas de registo-eventhubs para registar eventos de pretendido. A política de registo-eventhubs pode ser utilizada na secção de entrada de política ou a secção de política de saída.

Para configurar as políticas, início de sessão para o [portal do Azure](https://portal.azure.com), navegue até ao seu serviço de gestão de API e clique em **portal do publicador** para aceder ao portal do publicador.

![Portal do publicador][publisher-portal]

Clique em **políticas** no menu de gestão de API no lado esquerdo, selecione o produto pretendido e a API e clique em **Adicionar política**. Neste exemplo estamos a adicionar uma política para o **API eco** no **ilimitada** produto.

![Adicionar política][add-policy]

Posicione o cursor no `inbound` secção política e clique no **registo a EventHub** política para inserir o `log-to-eventhub` modelo de política de instrução.

![Editor de políticas][event-hub-policy]

```xml
<log-to-eventhub logger-id ='logger-id'>
  @( string.Join(",", DateTime.UtcNow, context.Deployment.ServiceName, context.RequestId, context.Request.IpAddress, context.Operation.Name))
</log-to-eventhub>
```

Substitua `logger-id` com o nome do registo de API Management que configurou no passo anterior.

Pode utilizar uma expressão que devolve uma cadeia como o valor para o `log-to-eventhub` elemento. Neste exemplo, uma cadeia contendo a data e hora, nome do serviço, id do pedido, endereço de ip do pedido e o nome da operação é registada.

Clique em **guardar** para guardar a configuração de política atualizado. Assim que é guardado a política estar ativa e os eventos são registados para o Hub de eventos designado.

## <a name="next-steps"></a>Passos seguintes
* Saiba mais sobre os Event Hubs do Azure
  * [Introdução ao Event Hubs do Azure](../event-hubs/event-hubs-c-getstarted-send.md)
  * [Receber mensagens com o EventProcessorHost](../event-hubs/event-hubs-dotnet-standard-getstarted-receive-eph.md)
  * [Guia de programação dos Event Hubs](../event-hubs/event-hubs-programming-guide.md)
* Saiba mais sobre a integração de API Management e do Event Hubs
  * [Referência de entidade de registo](https://docs.microsoft.com/rest/api/apimanagement/loggers)
  * [referência de política de registo para eventhub](https://docs.microsoft.com/azure/api-management/api-management-advanced-policies#log-to-eventhub)
  * [Monitorizar as suas APIs com API Management do Azure, os Event Hubs e Runscope](api-management-log-to-eventhub-sample.md)    

## <a name="watch-a-video-walkthrough"></a>Veja um vídeo com instruções
> [!VIDEO https://channel9.msdn.com/Blogs/AzureApiMgmt/Integrate-Azure-API-Management-with-Event-Hubs/player]
>
>

[publisher-portal]: ./media/api-management-howto-log-event-hubs/publisher-portal.png
[create-event-hub]: ./media/api-management-howto-log-event-hubs/create-event-hub.png
[event-hub-connection-string]: ./media/api-management-howto-log-event-hubs/event-hub-connection-string.png
[event-hub-dashboard]: ./media/api-management-howto-log-event-hubs/event-hub-dashboard.png
[receiving-policy]: ./media/api-management-howto-log-event-hubs/receiving-policy.png
[sending-policy]: ./media/api-management-howto-log-event-hubs/sending-policy.png
[event-hub-policy]: ./media/api-management-howto-log-event-hubs/event-hub-policy.png
[add-policy]: ./media/api-management-howto-log-event-hubs/add-policy.png
