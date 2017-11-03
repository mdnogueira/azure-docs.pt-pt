---
title: Criar atividade registo alertas | Microsoft Docs
description: "Seja notificado através de SMS, o webhook e o e-mail quando determinados eventos ocorrem no registo de atividade."
author: johnkemnetz
manager: orenr
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: 
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/03/2017
ms.author: johnkem
ms.openlocfilehash: 3885469ec0e1fcc31386dd0ad7fe6cb5d03ab28e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="create-activity-log-alerts"></a>Criar alertas de registo de atividade

## <a name="overview"></a>Descrição geral
Alertas de registo de atividade são alertas que ativar quando ocorre a um novo registo de eventos de atividade que satisfaça as condições especificadas no alerta. São recursos do Azure, pelo que pode ser criadas utilizando um modelo Azure Resource Manager. Também podem ser criados, atualizar ou eliminados no portal do Azure. Este artigo apresenta os conceitos atrás de alertas de registo de atividade. Em seguida, mostra como utilizar o portal do Azure para configurar um alerta sobre eventos de registo de atividade.

Normalmente, criar atividade alertas de registo para receber notificações quando:

* Alterações específicas ocorrerem em recursos na sua subscrição do Azure, muitas vezes, no âmbito de grupos de recurso específico ou de recursos. Por exemplo, poderá ser notificado quando qualquer máquina virtual na myProductionResourceGroup é eliminada. Em alternativa, poderá pretender ser notificado se quaisquer novas funções são atribuídas a um utilizador na sua subscrição.
* Ocorre um evento de estado de funcionamento do serviço. Eventos de estado de funcionamento de serviço incluem a notificação de incidentes e eventos de manutenção que se aplicam aos recursos na sua subscrição.

Em ambos os casos, um alerta de registo de atividade monitoriza apenas eventos na subscrição na qual o alerta é criado.

Pode configurar um alerta de registo de atividade com base em qualquer propriedade de nível superior no objeto JSON para um registo de eventos de atividade. No entanto, o portal mostra as opções mais comuns:

- **Categoria**: administrativo, serviço de estado de funcionamento, dimensionamento automático e recomendação. Para obter mais informações, consulte [descrição geral do registo de atividade do Azure](./monitoring-overview-activity-logs.md#categories-in-the-activity-log). Para saber mais sobre eventos de estado de funcionamento do serviço, consulte o artigo [receber alertas de registo de atividade em notificações de serviço](./monitoring-activity-log-alerts-on-service-notifications.md).
- **Grupo de recursos**
- **Recurso**
- **Tipo de recurso**
- **Nome da operação**: nome de operação de controlo de acesso de The Resource Manager Role-Based.
- **Nível**: O nível de gravidade do evento (verboso, informativo, aviso, erro ou crítico).
- **Estado**: O estado do evento, normalmente iniciada, falhou ou foi concluída com êxito.
- **Evento iniciadas pelo**: também conhecido como "autor da chamada." O endereço de e-mail ou o identificador do Azure Active Directory do utilizador que executou a operação.

>[!NOTE]
>Tem de especificar, pelo menos, dois dos critérios anteriores no seu alerta, com um que está a ser a categoria. Não pode criar um alerta que activa sempre que é criado um evento nos registos de atividade.
>
>

Quando um alerta de registo de atividade é ativado, utiliza um grupo de ação para gerar as ações ou notificações. Um grupo de ação é um conjunto reutilizável de recetores de notificação, tais como endereços de correio eletrónico, números de telefone de URLs de webhook ou SMS. Os recetores podem ser referenciados a partir de vários alertas para centralizar e os canais de notificação de grupo. Quando definir o alerta de registo de atividade, tem duas opções. Pode:

* Utilize um grupo de ação existente no seu alerta de registo de atividade. 
* Crie um novo grupo de ação. 

Para mais informações sobre grupos de ação, consulte o artigo [criar e gerir grupos de ação no portal do Azure](monitoring-action-groups.md).

Para obter mais informações sobre notificações de estado de funcionamento do serviço, consulte o artigo [receber alertas de registo de atividade em notificações do Estado de funcionamento do serviço](monitoring-activity-log-alerts-on-service-notifications.md).

## <a name="create-an-alert-on-an-activity-log-event-with-a-new-action-group-by-using-the-azure-portal"></a>Criar um alerta um evento de registo de atividade com um novo grupo de ação através do portal do Azure
1. No [portal](https://portal.azure.com), selecione **Monitor**.

    ![O serviço de "Monitor de"](./media/monitoring-activity-log-alerts/home-monitor.png)
2. No **registo de atividade** secção, selecione **alertas**.

    ![O separador "Alertas"](./media/monitoring-activity-log-alerts/alerts-blades.png)
3. Selecione **Adicionar alerta de registo de atividade**e preencha os campos.

4. Introduza um nome no **nome de alerta de registo de atividade** caixa e selecione um **Descrição**.

    ![O comando "Adicionar alerta de registo de atividade"](./media/monitoring-activity-log-alerts/add-activity-log-alert.png)

5. O **subscrição** caixa autofills com a sua subscrição atual. Esta subscrição é o que o grupo de ação é guardado. O recurso de alerta for implementado para esta subscrição e monitoriza os eventos de registo de atividade do mesmo.

    ![A caixa de diálogo "Adicionar o alerta de registo de atividade"](./media/monitoring-activity-log-alerts/activity-log-alert-new-action-group.png)

6. Selecione o **grupo de recursos** no qual o recurso de alerta é criado. Não é o grupo de recursos que é monitorizado pelo alerta. Em vez disso, é o grupo de recursos onde está localizado o recurso de alerta.

7. Opcionalmente, selecione um **categoria de evento** para modificar os filtros adicionais que são apresentados. Para eventos administrativos, os filtros incluem **grupo de recursos**, **recursos**, **tipo de recurso**, **nome da operação**, **Nível**, **estado**, e **eventos iniciadas pelo**. Estes valores identificam os eventos este alerta deve monitorizar.

    >[!NOTE]
    >Tem de especificar, pelo menos, um dos critérios de anteriores o alerta. Não pode criar um alerta que activa sempre que é criado um evento nos registos de atividade.
    >
    >

8. Introduza um nome no **nome do grupo de ação** caixa e introduza um nome no **nome abreviado** caixa. O nome abreviado é utilizado em vez de um nome de grupo ação completa, quando as notificações são enviadas através deste grupo.

9.  Defina uma lista de ações, fornecendo a ação:

    a. **Nome**: introduza o nome, o alias ou o identificador da ação.

    b. **Tipo de ação**: selecione SMS, o e-mail ou o webhook.

    c. **Detalhes**: com base no tipo de ação, introduza um número de telefone, o endereço de e-mail ou o webhook URI.

10. Selecione **OK** para criar o alerta.

O alerta demora alguns minutos para propagar totalmente e, em seguida, ficar ativo. Este é acionado quando novos eventos correspondem aos critérios do alerta.

Para obter mais informações, consulte [compreender o esquema de webhook utilizado nos alertas do registo de atividade](monitoring-activity-log-alerts-webhook.md).

>[!NOTE]
>O grupo de ação definido nestes passos está reutilizável como um grupo de ação existente para todas as definições de alerta futuras.
>
>

## <a name="create-an-alert-on-an-activity-log-event-for-an-existing-action-group-by-using-the-azure-portal"></a>Criar um alerta um evento de registo de atividade para um grupo de ação existente utilizando o portal do Azure
1. Siga os passos 1 a 7 na secção anterior para criar o alerta de registo de atividade.

2. Em **notificar através de**, selecione o **existentes** botão do grupo de ação. Selecione um grupo de ação existente na lista.

3. Selecione **OK** para criar o alerta.

O alerta demora alguns minutos para propagar totalmente e, em seguida, ficar ativo. Este é acionado quando novos eventos correspondem aos critérios do alerta.

## <a name="manage-your-alerts"></a>Gerir os alertas

Depois de criar um alerta, é visível na secção alertas do painel do Monitor. Selecione o alerta que pretende gerir:

* Editá-lo.
* Elimine-o.
* Desactivar ou activar, se pretender temporariamente interromper ou retomar a receção de notificações para o alerta.

## <a name="next-steps"></a>Passos seguintes
- Obter um [descrição geral dos alertas](monitoring-overview-alerts.md).
- Saiba mais sobre [limitação de taxa de notificação](monitoring-alerts-rate-limiting.md).
- Reveja o [esquema de webhook alerta de registo de atividade](monitoring-activity-log-alerts-webhook.md).
- Saiba mais sobre [grupos ação](monitoring-action-groups.md).  
- Saiba mais sobre [notificações de estado de funcionamento do serviço](monitoring-service-notifications.md).
- Criar um [alerta de registo de atividade para monitorizar todas as operações de motor de dimensionamento automático na sua subscrição](https://github.com/Azure/azure-quickstart-templates/tree/master/monitor-autoscale-alert).
- Criar um [alerta de registo de atividade para monitorizar todas as operações de escala-na/escalável de dimensionamento automático falhou na sua subscrição](https://github.com/Azure/azure-quickstart-templates/tree/master/monitor-autoscale-failed-alert).
