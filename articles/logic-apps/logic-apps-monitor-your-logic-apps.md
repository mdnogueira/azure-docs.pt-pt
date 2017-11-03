---
title: Verificar o estado, configurar o registo e obtenha alertas - Azure Logic Apps | Microsoft Docs
description: "Monitorizar o estado e desempenho para as logic apps, registar dados de diagnóstico e configurar alertas"
author: jeffhollan
manager: anneta
editor: 
services: logic-apps
documentationcenter: 
ms.assetid: 5c1b1e15-3b6c-49dc-98a6-bdbe7cb75339
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.custom: H1Hack27Feb2017
ms.date: 07/21/2017
ms.author: LADocs; jehollan
ms.openlocfilehash: 4795f5728d4ce6ff21b97bc3fefd6a53e0c6a11b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="monitor-status-set-up-diagnostics-logging-and-turn-on-alerts-for-azure-logic-apps"></a>Monitorizar o estado, configurar o registo de diagnóstico e ativar alertas para o Azure Logic Apps

Depois de [criar e executar uma aplicação lógica](../logic-apps/logic-apps-create-a-logic-app.md), pode verificar o respetivo histórico de execuções, o histórico de Acionador, o estado e o desempenho. Para monitorização de eventos em tempo real e mais rico de depuração, configurar [registo de diagnóstico](#azure-diagnostics) para a sua aplicação lógica. Dessa forma, pode [localizar e visualizar eventos](#find-events), como o acionador eventos, execução de eventos e os eventos de ação. Também pode utilizar este [dados de diagnóstico com outros serviços](#extend-diagnostic-data), como o Storage do Azure e Event Hubs do Azure. 

Para obter notificações sobre falhas ou outros informações sobre problemas possíveis, configurar [alertas](#add-azure-alerts). Por exemplo, pode criar um alerta que Deteta "quando é executada mais de cinco falha numa hora." Também pode configurar monitorização, registo e registo através de programação utilizando [propriedades e definições de eventos de diagnóstico do Azure](#diagnostic-event-properties).

## <a name="view-runs-and-trigger-history-for-your-logic-app"></a>Execuções de vista e o histórico de acionamento para a sua aplicação lógica

1. Para localizar a sua aplicação lógica no [portal do Azure](https://portal.azure.com), no menu principal do Azure, escolha **mais serviços**. Na caixa de pesquisa, localize "logic apps" e escolha **as Logic apps**.

   ![Localizar a sua aplicação lógica](./media/logic-apps-monitor-your-logic-apps/find-your-logic-app.png)

   O portal do Azure mostra todas as aplicações lógicas que estão associadas a sua subscrição do Azure. 

2. Selecione a sua aplicação lógica, em seguida, escolha **descrição geral**.

   O portal do Azure mostra o histórico de execução e o histórico de acionamento para a sua aplicação lógica. Por exemplo:

   ![Histórico de histórico e acionador em execução na aplicação lógica](media/logic-apps-monitor-your-logic-apps/overview.png)

   * **Executa histórico** mostra todas as execuções para a sua aplicação lógica. 
   * **Acionar histórico** mostra todas as atividades de acionamento para a sua aplicação lógica.

   Para descrições de estado, consulte [resolver problemas da aplicação lógica](../logic-apps/logic-apps-diagnosing-failures.md).

   > [!TIP]
   > Se não encontrar os dados que prevê que, na barra de ferramentas, escolha **atualizar**.

3. Para ver os passos de uma execução específico, em **executa histórico**, selecione de que são executados. 

   A vista de monitor mostra cada passo em que são executados. Por exemplo:

   ![Ações para uma execução específica](media/logic-apps-monitor-your-logic-apps/monitor-view-updated.png)

4. Para obter mais detalhes sobre a execução, escolha **detalhes da execução**. Estas informações resume os passos, estado, entradas e saídas para a execução. 

   ![Escolha "Detalhes da execução"](media/logic-apps-monitor-your-logic-apps/run-details.png)

   Por exemplo, pode obter a execução **ID de correlação**, que poderá ser necessário quando utiliza o [API REST para Logic Apps](https://docs.microsoft.com/rest/api/logic).

5. Para obter detalhes sobre um passo específico, selecione esse passo. Agora pode rever detalhes como entradas, saídas e quaisquer erros que ocorreram para esse passo. Por exemplo:

   ![Detalhes de passo](media/logic-apps-monitor-your-logic-apps/monitor-view-details.png)
   
   > [!NOTE]
   > Todos os detalhes do tempo de execução e eventos são encriptados no âmbito do serviço de aplicações lógicas. Estes são desencriptados apenas quando um utilizador pede para ver os dados. Também pode controlar o acesso a estes eventos com [controlo de acesso em funções do Azure (RBAC)](../active-directory/role-based-access-control-what-is.md).

6. Para obter detalhes sobre um evento específico, volte atrás para o **descrição geral** painel. Em **acionar histórico**, selecione o evento acionador. Agora pode rever os detalhes como entradas e saídas, por exemplo:

   ![Detalhes de saída do evento acionador](media/logic-apps-monitor-your-logic-apps/trigger-details.png)

<a name="azure-diagnostics"></a>

## <a name="turn-on-diagnostics-logging-for-your-logic-app"></a>Ative os diagnósticos de registo para a sua aplicação lógica

Para mais rico de depuração com detalhes de tempo de execução e de eventos, pode configurar com o registo de diagnóstico [Log Analytics do Azure](../log-analytics/log-analytics-overview.md). Análise de registos é um serviço no [Operations Management Suite (OMS)](../operations-management-suite/operations-management-suite-overview.md) que monitoriza a sua nuvem e no local ambientes para o ajudar a manter a respetiva disponibilidade e desempenho. 

Antes de começar, tem de ter uma área de trabalho do OMS. Saiba [como criar uma área de trabalho do OMS](../log-analytics/log-analytics-get-started.md).

1. No [portal do Azure](https://portal.azure.com), localize e selecione a sua aplicação lógica. 

2. No menu de painel de aplicação lógica, sob **monitorização**, escolha **diagnóstico** > **definições de diagnóstico**.

   ![Aceda a monitorização, diagnóstico, definições de diagnóstico](media/logic-apps-monitor-your-logic-apps/logic-app-diagnostics.png)

3. Em **as definições de diagnóstico**, escolha **no**.

   ![Ativar registos de diagnóstico](media/logic-apps-monitor-your-logic-apps/turn-on-diagnostics-logic-app.png)

4. Agora selecione a categoria de área de trabalho e de eventos do OMS para o registo conforme mostrado:

   1. Selecione **enviar ao Log Analytics**. 
   2. Em **Log Analytics**, escolha **configurar**. 
   3. Em **áreas de trabalho do OMS**, selecione a área de trabalho do OMS a utilizar para o registo.
   4. Em **registo**, selecione o **WorkflowRuntime** categoria.
   5. Escolha o intervalo de métrico.
   6. Quando tiver terminado, escolha **guardar**.

   ![Selecione a área de trabalho OMS e dados de registo](media/logic-apps-monitor-your-logic-apps/send-diagnostics-data-log-analytics-workspace.png)

Agora, pode encontrar eventos e outros dados de eventos de Acionador, execute os eventos e os eventos de ação.

<a name="find-events"></a>

## <a name="find-events-and-data-for-your-logic-app"></a>Localizar os eventos e os dados para a sua aplicação lógica

Para localizar e visualizar eventos na sua aplicação lógica, incluindo a acionar eventos, executados os eventos e os eventos de ação, siga estes passos.

1. No [portal do Azure](https://portal.azure.com), escolha **mais serviços**. Procure "análise de registos", em seguida, escolha **Log Analytics** conforme mostrado aqui:

   ![Escolha "Análise de registos"](media/logic-apps-monitor-your-logic-apps/browseloganalytics.png)

2. Em **Log Analytics**, localize e selecione a sua área de trabalho do OMS. 

   ![Selecione a sua área de trabalho do OMS](media/logic-apps-monitor-your-logic-apps/selectla.png)

3. Em **gestão**, escolha **Portal do OMS**.

   ![Escolha "Portal do OMS"](media/logic-apps-monitor-your-logic-apps/omsportalpage.png)

4. Na sua home page do OMS, escolha **pesquisa registo**.

   ![Na sua home page do OMS, escolha "Registo de pesquisa"](media/logic-apps-monitor-your-logic-apps/logsearch.png)

   -ou-

   ![No menu do OMS, escolha "Registo de pesquisa"](media/logic-apps-monitor-your-logic-apps/logsearch-2.png)

5. Na caixa de pesquisa, especifique um campo que pretende localizar e prima **Enter**. Quando começar a escrever, OMS mostra-lhe correspondências possíveis e as operações que pode utilizar. 

   Por exemplo, para localizar os eventos de 10 principais que tenham acontecido, introduza e selecionar esta consulta de pesquisa: **categoria = WorkflowRuntime | principais 10**

   ![Introduza a cadeia de procura](media/logic-apps-monitor-your-logic-apps/oms-start-query.png)

   Saiba mais sobre [como localizar dados na análise de registos](../log-analytics/log-analytics-log-searches.md).

6. Na página de resultados, na barra da esquerda, escolha o período de tempo que pretende visualizar.
Para otimizar a consulta ao adicionar um filtro, escolha **+ adicionar**.

   ![Escolha o limite temporal dos resultados da consulta](media/logic-apps-monitor-your-logic-apps/query-results.png)

7. Em **adicionar filtros**, introduza o nome do filtro, pelo que pode encontrar o filtro que pretende. Selecione o filtro e escolha **+ adicionar**.

   Este exemplo utiliza a palavra "status", para encontrar eventos de falha em **AzureDiagnostics**.
   Aqui, o filtro para **status_s** já está selecionada.

   ![Selecione o filtro](media/logic-apps-monitor-your-logic-apps/log-search-add-filter.png)

8. Na barra da esquerda, selecione o valor do filtro que pretende utilizar e escolha **aplicar**.

   ![Selecione o valor do filtro, escolha "Aplicar"](media/logic-apps-monitor-your-logic-apps/log-search-apply-filter.png)

9. Agora, volte para a consulta que está a criar. A consulta é atualizada com o filtro selecionado e o valor. Os resultados anteriores agora são filtrados demasiado.

   ![Regressar à sua consulta com resultados filtrados](media/logic-apps-monitor-your-logic-apps/log-search-query-filtered-results.png)

10. Para guardar a consulta para utilização futura, escolha **guardar**. Saiba [como guardar a consulta](../logic-apps/logic-apps-track-b2b-messages-omsportal-query-filter-control-number.md#save-oms-query).

<a name="extend-diagnostic-data"></a>

## <a name="extend-how-and-where-you-use-diagnostic-data-with-other-services"></a>Expandir como e onde utiliza dados de diagnóstico com outros serviços

Juntamente com o Log Analytics do Azure, pode expandir como utilizar dados de diagnóstico da sua aplicação lógica com outros serviços do Azure, por exemplo: 

* [Arquivo de registos de diagnóstico do Azure no Storage do Azure](../monitoring-and-diagnostics/monitoring-archive-diagnostic-logs.md)
* [Transmitir os registos de diagnóstico do Azure para Event Hubs do Azure](../monitoring-and-diagnostics/monitoring-stream-diagnostic-logs-to-event-hubs.md) 

Pode, em seguida, a monitorização utilizando a telemetria e análise de outros serviços, como get em tempo real [Azure Stream Analytics](../stream-analytics/stream-analytics-introduction.md) e [Power BI](../log-analytics/log-analytics-powerbi.md). Por exemplo:

* [Dados de fluxo de Hubs de eventos para o Stream Analytics](../stream-analytics/stream-analytics-define-inputs.md)
* [Analisar dados de transmissão em fluxo com o Stream Analytics e criar um dashboard de análise em tempo real no Power BI](../stream-analytics/stream-analytics-power-bi-dashboard.md)

Com base nas opções que pretende configurar, certifique-se de que a primeira [criar uma conta de armazenamento do Azure](../storage/common/storage-create-storage-account.md) ou [criar um hub de eventos do Azure](../event-hubs/event-hubs-create.md). Em seguida, selecione as opções para onde pretende enviar dados de diagnóstico:

![Enviar dados para o hub de conta ou evento de armazenamento do Azure](./media/logic-apps-monitor-your-logic-apps/storage-account-event-hubs.png)

> [!NOTE]
> Períodos de retenção aplicam-se apenas quando optar por utilizar uma conta de armazenamento.

<a name="add-azure-alerts"></a>

## <a name="set-up-alerts-for-your-logic-app"></a>Configurar alertas para a sua aplicação lógica

Para monitorizar métricas específicas ou excedidos limiares para a sua aplicação lógica, configure o [alertas no Azure](../monitoring-and-diagnostics/monitoring-overview-alerts.md). Saiba mais sobre [métricas no Azure](../monitoring-and-diagnostics/monitoring-overview-metrics.md). 

Para configurar alertas sem [Log Analytics do Azure](../log-analytics/log-analytics-overview.md), siga estes passos. Para mais avançadas critérios de alertas e ações, [configurar a análise de registos](#azure-diagnostics) demasiado.

1. No menu de painel de aplicação lógica, sob **monitorização**, escolha **diagnóstico** > **regras de alerta** > **Adicionar alerta** conforme mostrado aqui:

   ![Adicionar um alerta para a sua aplicação lógica](media/logic-apps-monitor-your-logic-apps/set-up-alerts.png)

2. No **adicionar uma regra de alerta** painel, criar o alerta, conforme mostrado:

   1. Em **recursos**, selecione a sua aplicação lógica, se não estiver já selecionada. 
   2. Dê um nome e descrição para o alerta.
   3. Selecione um **métrica** ou evento que pretende controlar.
   4. Selecione um **condição**, especifique um **limiar** para a métrica e selecione o **período** para esta métrica de monitorização.
   5. Selecione se pretende enviar correio eletrónico para o alerta. 
   6. Especifique outros endereços de correio eletrónico para enviar o alerta. 
   Também pode especificar um URL do webhook onde pretende enviar o alerta.

   Por exemplo, esta regra envia um alerta quando cinco ou mais executa falha numa hora:

   ![Criar regra de alerta métrica](media/logic-apps-monitor-your-logic-apps/create-alert-rule.png)

> [!TIP]
> Para executar uma aplicação lógica a partir de um alerta, pode incluir o [acionador pedido](../connectors/connectors-native-reqres.md) no fluxo de trabalho, que permite-lhe efetuar tarefas, como estes exemplos:
> 
> * [Post ao Slack](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-slack-with-logic-app)
> * [Enviar um texto](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-text-message-with-logic-app)
> * [Adicionar uma mensagem para uma fila](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-queue-with-logic-app)

<a name="diagnostic-event-properties"></a>

## <a name="azure-diagnostics-event-settings-and-details"></a>As definições de eventos de diagnóstico do Azure e os detalhes

Cada eventos de diagnóstico contém os detalhes sobre a sua aplicação lógica e esse evento, por exemplo, o estado, inicie o tempo, a hora de fim e assim sucessivamente. Através de programação configurar a monitorização, o controlo e o registo, UO possa utilizar esses dados com o [API REST para Azure Logic Apps](https://docs.microsoft.com/rest/api/logic) e o [API REST para o Azure Diagnostics](../monitoring-and-diagnostics/monitoring-supported-metrics.md#microsoftlogicworkflows).

Por exemplo, o `ActionCompleted` evento tem o `clientTrackingId` e `trackedProperties` propriedades que pode utilizar para a monitorização e controlo:

``` json
{
    "time": "2016-07-09T17:09:54.4773148Z",
    "workflowId": "/SUBSCRIPTIONS/<subscription-ID>/RESOURCEGROUPS/MYRESOURCEGROUP/PROVIDERS/MICROSOFT.LOGIC/WORKFLOWS/MYLOGICAPP",
    "resourceId": "/SUBSCRIPTIONS/<subscription-ID>/RESOURCEGROUPS/MYRESOURCEGROUP/PROVIDERS/MICROSOFT.LOGIC/WORKFLOWS/MYLOGICAPP/RUNS/08587361146922712057/ACTIONS/HTTP",
    "category": "WorkflowRuntime",
    "level": "Information",
    "operationName": "Microsoft.Logic/workflows/workflowActionCompleted",
    "properties": {
        "$schema": "2016-06-01",
        "startTime": "2016-07-09T17:09:53.4336305Z",
        "endTime": "2016-07-09T17:09:53.5430281Z",
        "status": "Succeeded",
        "code": "OK",
        "resource": {
            "subscriptionId": "<subscription-ID>",
            "resourceGroupName": "MyResourceGroup",
            "workflowId": "cff00d5458f944d5a766f2f9ad142553",
            "workflowName": "MyLogicApp",
            "runId": "08587361146922712057",
            "location": "westus",
            "actionName": "Http"
        },
        "correlation": {
            "actionTrackingId": "e1931543-906d-4d1d-baed-dee72ddf1047",
            "clientTrackingId": "<my-custom-tracking-ID>"
        },
        "trackedProperties": {
            "myTrackedProperty": "<value>"
        }
    }
}
```

* `clientTrackingId`: Se não for fornecido, automaticamente, o Azure gera este ID e está correlacionada com eventos através de uma aplicação lógica de execução, incluindo quaisquer fluxos de trabalho aninhados que são chamados a partir da aplicação lógica. Pode especificar manualmente este ID de um acionador transferindo uma `x-ms-client-tracking-id` cabeçalho com o valor de ID personalizado no pedido de Acionador. Pode utilizar um acionador de pedido, o acionador HTTP ou o acionador de webhook.

* `trackedProperties`: Para controlar as entradas ou saídas nos dados de diagnóstico, pode adicionar propriedades controladas para ações na definição de JSON da sua aplicação lógica. Propriedades controladas acompanhar apenas uma única ação entradas e saídas, mas pode utilizar o `correlation` propriedades de eventos para correlacionar em ações numa execução.

  Para monitorizar uma ou mais propriedades, adicione o `trackedProperties` secção e as propriedades que pretende que a definição de ação. Por exemplo, suponha que pretende controlar os dados, como um "ID de ordem" na sua telemetria:

  ``` json
  "myAction": {
    "type": "http",
    "inputs": {
        "uri": "http://uri",
        "headers": {
            "Content-Type": "application/json"
        },
        "body": "@triggerBody()"
    },
    "trackedProperties": {
        "myActionHTTPStatusCode": "@action()['outputs']['statusCode']",
        "myActionHTTPValue": "@action()['outputs']['body']['<content>']",
        "transactionId": "@action()['inputs']['body']['<content>']"
    }
  }
  ```

## <a name="next-steps"></a>Passos seguintes

* [Criar modelos de implementação da aplicação lógica e gestão de versões](../logic-apps/logic-apps-create-deploy-template.md)
* [Cenários B2B com Enterprise Integration Pack](../logic-apps/logic-apps-enterprise-integration-overview.md)
* [Monitorizar mensagens B2B](../logic-apps/logic-apps-monitor-b2b-message.md)