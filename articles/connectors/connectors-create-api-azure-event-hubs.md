---
title: Configurar o monitor de eventos com o Event Hubs do Azure para o Azure Logic Apps | Microsoft Docs
description: Monitorizar os fluxos de dados para receber eventos e enviar eventos para o Azure Logic Apps com o Event Hubs do Azure
services: logic-apps
keywords: fluxo de dados, o monitor de eventos, os event hubs
author: ecfan
manager: anneta
editor: 
documentationcenter: 
tags: connectors
ms.assetid: 
ms.service: logic-apps
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/31/2017
ms.author: estfan; LADocs
ms.openlocfilehash: 2ca27fb8269d1796fb1181fc4d0a8744a592d548
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="monitor-receive-and-send-events-with-the-event-hubs-connector"></a>Monitorizar, receber e enviar eventos com o conector de Event Hubs

Para configurar um monitor de eventos, para que a aplicação lógica pode detetar eventos, receber eventos e enviar eventos, ligar a um [Hub de eventos do Azure](https://azure.microsoft.com/services/event-hubs) da sua aplicação lógica. Saiba mais sobre [Event Hubs do Azure](../event-hubs/event-hubs-what-is-event-hubs.md).

## <a name="requirements"></a>Requisitos

* Tem de ter um [Hub de eventos e espaço de nomes de Event Hubs](../event-hubs/event-hubs-create.md) no Azure. Saiba [como criar um espaço de nomes de Event Hubs e o Hub de eventos](../event-hubs/event-hubs-create.md). 

* Para utilizar [qualquer conector](https://docs.microsoft.com/azure/connectors/apis-list) na sua aplicação lógica, tem de criar uma aplicação lógica pela primeira vez. Saiba [como criar uma aplicação lógica](../logic-apps/logic-apps-create-a-logic-app.md).

<a name="permissions-connection-string"></a>
## <a name="check-event-hubs-namespace-permissions-and-find-the-connection-string"></a>Verifique as permissões de espaço de nomes de Event Hubs e localize a cadeia de ligação

Para a sua aplicação lógica aceder a qualquer serviço, tem de criar um [ *ligação* ](./connectors-overview.md) entre a sua aplicação lógica e o serviço, se ainda não o fez. Esta ligação autoriza a sua aplicação lógica para aceder a dados.
Para a sua aplicação lógica para o Hub de eventos de acesso, tem de ter **gerir** permissões e a cadeia de ligação para o seu espaço de nomes de Event Hubs.

Para verificar as suas permissões e obter a cadeia de ligação, siga estes passos.

1.  Inicie sessão no [portal do Azure](https://portal.azure.com "portal do Azure"). 

2.  Aceda ao seu Event Hubs *espaço de nomes*, não o Hub de eventos específico. No painel de espaço de nomes, em **definições**, escolha **políticas de acesso partilhado**. Em **afirmações**, verifique se tem **gerir** permissões para esse espaço de nomes.

    ![Gerir permissões do seu espaço de nomes do Hub de eventos](./media/connectors-create-api-azure-event-hubs/event-hubs-namespace.png)

3.  Para copiar a cadeia de ligação para o espaço de nomes de Event Hubs, escolha **RootManageSharedAccessKey**. Junto a cadeia de ligação de chave primária, escolha o botão Copiar.

    ![Copie a cadeia de ligação de espaço de nomes de Event Hubs](media/connectors-create-api-azure-event-hubs/find-event-hub-namespace-connection-string.png)

    > [!TIP]
    > Para confirmar se a cadeia de ligação é associada o espaço de nomes de Event Hubs ou com um Hub de eventos específicos, verifique a cadeia de ligação para o `EntityPath` parâmetro. Se encontrar este parâmetro, a cadeia de ligação é para um Hub de eventos específico "entidade" e não é a cadeia correta a utilizar com a sua aplicação lógica.

4.  Agora quando lhe for pedida credenciais depois de adicionar um acionador de Event Hubs ou ação à sua aplicação lógica, pode ligar ao seu espaço de nomes de Event Hubs. Atribua a ligação de um nome, introduza a cadeia de ligação que copiou e escolha **criar**.

    ![Introduza a cadeia de ligação para o espaço de nomes de Event Hubs](./media/connectors-create-api-azure-event-hubs/event-hubs-connection.png)

    Depois de criar a ligação, o nome da ligação deverão ser apresentados de eventos acionador Hubs ou ação. 
    Em seguida, pode continuar com os outros passos na sua aplicação lógica.

    ![Ligação de espaço de nomes de Hubs de eventos criada](./media/connectors-create-api-azure-event-hubs/event-hubs-connection-created.png)

## <a name="start-workflow-when-your-event-hub-receives-new-events"></a>Iniciar o fluxo de trabalho quando o seu Hub de eventos recebe eventos novos.

A [ *acionador* ](../logic-apps/logic-apps-what-are-logic-apps.md#logic-app-concepts) é um evento que inicia a um fluxo de trabalho na sua aplicação lógica. Para iniciar um fluxo de trabalho quando novos eventos são enviados para o Hub de eventos, siga estes passos para adicionar o acionador que Deteta este evento.

1.  No [portal do Azure](https://portal.azure.com "portal do Azure"), aceda à sua aplicação lógica existente ou criar uma aplicação lógica em branco.

2.  Na caixa de pesquisa para o Designer de aplicação lógica, introduza `event hubs` para o filtro. Selecione este acionador: **quando os eventos estão disponíveis no Hub de eventos**

    ![Selecione o acionador para quando o seu Hub de eventos recebe eventos novos.](./media/connectors-create-api-azure-event-hubs/find-event-hubs-trigger.png)

    Se ainda não tiver uma ligação para o espaço de nomes de Event Hubs, é-lhe pedido que criar agora esta ligação. Dê um nome da ligação e introduza a cadeia de ligação para o espaço de nomes de Event Hubs. 
    Se necessário, saiba [como encontrar a cadeia de ligação](#permissions-connection-string).

    ![Introduza a cadeia de ligação para o espaço de nomes de Event Hubs](./media/connectors-create-api-azure-event-hubs/event-hubs-connection.png)

    Depois de criar a ligação, as definições para o **quando um evento no disponíveis num Hub de eventos** aparecer acionar.

    ![Definições de Acionador para quando o seu Hub de eventos recebe eventos novos.](./media/connectors-create-api-azure-event-hubs/event-hubs-trigger.png)

3.  Introduza ou selecione o nome para o Hub de eventos que pretende monitorizar. Selecione a frequência e o intervalo de frequência pretende verificar o Hub de eventos.

    > [!TIP]
    > Para, opcionalmente, selecione um grupo de consumidores para ler eventos, escolha **Mostrar opções avançadas**. 

    ![Especifique o Hub de eventos ou grupo de consumidores](./media/connectors-create-api-azure-event-hubs/event-hubs-trigger-details.png)

    Agora que tiver configurado a um acionador para iniciar um fluxo de trabalho para a sua aplicação lógica. 
    A aplicação lógica verifica o Hub de eventos especificado com base numa agenda definida. 
    Se a aplicação encontrar eventos novos de eventos, o acionador executa outras ações ou concentrador aciona na sua aplicação lógica.

## <a name="send-events-to-your-event-hub-from-your-logic-app"></a>Enviar eventos para o Hub de eventos a partir da sua aplicação lógica

Uma [*ação*](../logic-apps/logic-apps-what-are-logic-apps.md#logic-app-concepts) é uma tarefa realizada pelo fluxo de trabalho da sua aplicação lógica. Depois de adicionar um acionador à aplicação lógica, pode adicionar uma ação para realizar operações com dados gerados por esse acionador. Para enviar um evento para o Hub de eventos da sua aplicação lógica, siga estes passos.

1.  No Designer de aplicação lógica, sob o acionador de aplicação lógica, escolha **novo passo** > **adicionar uma ação**.

    ![Selecione "Novo passo", em seguida, "Adicionar uma ação"](./media/connectors-create-api-azure-event-hubs/add-action.png)

    Agora, pode localizar e selecionar uma ação a executar. 
    Embora possa selecionar qualquer ação, neste exemplo, queremos a ação de Event Hubs para enviar eventos.

2.  Na caixa de pesquisa, introduza `event hubs` para o filtro.
Selecione esta ação: **enviar eventos**

    ![Selecione "Os Hubs de eventos - eventos de envio" ação](./media/connectors-create-api-azure-event-hubs/find-event-hubs-action.png)

3.  Introduza os detalhes necessários para o evento, tal como o nome para o Hub de eventos em que pretende enviar o evento. Introduza quaisquer outros detalhes opcionais sobre o evento, tais como conteúdo para esse evento.

    > [!TIP]
    > Para especificar opcionalmente a partição do Hub de eventos para onde enviar o evento, escolha **Mostrar opções avançadas**. 

    ![Introduza o nome de Hub de eventos e detalhes de evento opcional](./media/connectors-create-api-azure-event-hubs/event-hubs-send-event-action.png)

6.  Guarde as alterações.

    ![Guardar a aplicação lógica](./media/connectors-create-api-azure-event-hubs/save-logic-app.png)

    Agora que tiver configurado a uma ação para enviar eventos a partir da sua aplicação lógica. 

## <a name="connector-specific-details"></a>Detalhes específicos do conector

Ver todos os acionadores e ações definidas no swagger e consulte também os limites no [detalhes do conector](/connectors/eventhubs/). 

## <a name="get-help"></a>Obter ajuda

Para fazer perguntas, responder a perguntas e ver o que os outros utilizadores do Azure Logic Apps estão a fazer, visite o [fórum do Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).

Para ajudar a melhorar o Logic Apps e os conectores, vote ou submeta ideais no [site de comentários dos utilizadores do Logic Apps](http://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Passos seguintes

*  [Localizar outros conectores para aplicações lógicas do Azure](./apis-list.md)