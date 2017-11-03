---
title: "Monitorizar B2B transações e configurar o registo - Azure Logic Apps | Microsoft Docs"
description: "Monitor AS2, X12 e mensagens EDIFACT, iniciar o registo de diagnóstico para a sua conta de integração"
author: padmavc
manager: anneta
editor: 
services: logic-apps
documentationcenter: 
ms.assetid: bb7d9432-b697-44db-aa88-bd16ddfad23f
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.custom: H1Hack27Feb2017
ms.date: 07/21/2017
ms.author: LADocs; padmavc
ms.openlocfilehash: f717dae9a70a96944b623f22b90cf8c5a943f382
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="monitor-and-set-up-diagnostics-logging-for-b2b-communication-in-integration-accounts"></a>Monitorizar e configurar o registo de diagnóstico para a comunicação de B2B em contas de automatização

Após configurar a comunicação de B2B entre duas a executar os processos de negócios ou aplicações através da sua conta de integração, estas entidades podem trocar mensagens entre si. Para confirmar esta comunicação funciona conforme esperado, pode configurar a monitorização para AS2, X12, e EDIFACT mensagens, juntamente com o registo de diagnóstico para a sua conta de integração através de [Log Analytics do Azure](../log-analytics/log-analytics-overview.md) serviço. Este serviço em [Operations Management Suite (OMS)](../operations-management-suite/operations-management-suite-overview.md) monitoriza a sua nuvem e no local ambientes, ajudando-o a manter a respetiva disponibilidade e desempenho e também recolhe eventos de depuração mais rico e detalhes do tempo de execução. Também pode [utilizar os dados de diagnóstico com outros serviços](#extend-diagnostic-data), como o Storage do Azure e Event Hubs do Azure.

## <a name="requirements"></a>Requisitos

* Uma aplicação lógica que esteja configurada com o registo de diagnóstico. Saiba [como configurar o registo para essa aplicação lógica](../logic-apps/logic-apps-monitor-your-logic-apps.md#azure-diagnostics).

  > [!NOTE]
  > Após cumprir este requisito, deve ter uma área de trabalho a [Operations Management Suite (OMS)](../operations-management-suite/operations-management-suite-overview.md). Deve utilizar a área de trabalho do OMS mesma quando configurou o registo para a sua conta de integração. Se não tiver uma área de trabalho do OMS, saiba [como criar uma área de trabalho do OMS](../log-analytics/log-analytics-get-started.md).

* Uma conta de integração que está ligada à sua aplicação lógica. Saiba [como criar uma conta de integração com uma ligação à sua aplicação lógica](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md).

## <a name="turn-on-diagnostics-logging-for-your-integration-account"></a>Ative os diagnósticos de registo para a sua conta de integração

Pode ativar o registo a diretamente a partir da sua conta de integração ou [através do serviço de Azure Monitor](#azure-monitor-service). Monitor do Azure fornece monitorização básica com dados de nível de infraestrutura. Saiba mais sobre [Azure Monitor](../monitoring-and-diagnostics/monitoring-overview-azure-monitor.md).

### <a name="turn-on-diagnostics-logging-directly-from-your-integration-account"></a>Ative os diagnósticos registo diretamente a partir da sua conta de integração

1. No [portal do Azure](https://portal.azure.com), localize e selecione a sua conta de integração. Em **monitorização**, escolha **registos de diagnóstico** conforme mostrado aqui:

   ![Localize e selecione a sua conta de integração, escolha "Registos de diagnóstico"](media/logic-apps-monitor-b2b-message/integration-account-diagnostics.png)

2. Depois de selecionar a conta de integração, os seguintes valores são selecionados automaticamente. Se estes valores estiverem corretos, escolha **ative os diagnósticos**. Caso contrário, selecione os valores que pretende:

   1. Em **subscrição**, selecione a subscrição do Azure que utiliza com a sua conta de integração.
   2. Em **grupo de recursos**, selecione o grupo de recursos que utilizar com a sua conta de integração.
   3. Em **tipo de recurso**, selecione **contas de automatização**. 
   4. Em **recursos**, selecione a sua conta de integração. 
   5. Escolha **ative os diagnósticos**.

   ![Configurar o diagnóstico para a sua conta de integração](media/logic-apps-monitor-b2b-message/turn-on-diagnostics-integration-account.png)

3. Em **as definições de diagnóstico**e, em seguida, **estado**, escolha **no**.

   ![Ativar o diagnóstico do Azure](media/logic-apps-monitor-b2b-message/turn-on-diagnostics-integration-account-2.png)

4. Agora pode selecione a área de trabalho OMS e os dados a utilizar para o registo, conforme mostrado:

   1. Selecione **enviar ao Log Analytics**. 
   2. Em **Log Analytics**, escolha **configurar**. 
   3. Em **áreas de trabalho do OMS**, selecione a área de trabalho do OMS a utilizar para o registo.
   4. Em **registo**, selecione o **IntegrationAccountTrackingEvents** categoria.
   5. Escolha **Guardar**.

   ![Configurar a análise de registos para que pode enviar dados de diagnóstico para um registo](media/logic-apps-monitor-b2b-message/send-diagnostics-data-log-analytics-workspace.png)

5. Agora [configurar o controlo para as mensagens B2B no OMS](../logic-apps/logic-apps-track-b2b-messages-omsportal.md).

<a name="azure-monitor-service"></a>

### <a name="turn-on-diagnostics-logging-through-azure-monitor"></a>Ativar o registo de diagnóstico por meio do Monitor do Azure

1. No [portal do Azure](https://portal.azure.com), no menu principal do Azure, escolha **Monitor**, **registos de diagnóstico**. Em seguida, selecione a sua conta de integração conforme mostrado aqui:

   ![Escolha "Monitorizar", "Registos de diagnóstico", selecione a sua conta de integração](media/logic-apps-monitor-b2b-message/monitor-service-diagnostics-logs.png)

2. Depois de selecionar a conta de integração, os seguintes valores são selecionados automaticamente. Se estes valores estiverem corretos, escolha **ative os diagnósticos**. Caso contrário, selecione os valores que pretende:

   1. Em **subscrição**, selecione a subscrição do Azure que utiliza com a sua conta de integração.
   2. Em **grupo de recursos**, selecione o grupo de recursos que utilizar com a sua conta de integração.
   3. Em **tipo de recurso**, selecione **contas de automatização**.
   4. Em **recursos**, selecione a sua conta de integração.
   5. Escolha **ative os diagnósticos**.

   ![Configurar o diagnóstico para a sua conta de integração](media/logic-apps-monitor-b2b-message/turn-on-diagnostics-integration-account.png)

3. Em **as definições de diagnóstico**, escolha **no**.

   ![Ativar o diagnóstico do Azure](media/logic-apps-monitor-b2b-message/turn-on-diagnostics-integration-account-2.png)

4. Agora selecione a categoria de área de trabalho e de eventos do OMS para o registo conforme mostrado:

   1. Selecione **enviar ao Log Analytics**. 
   2. Em **Log Analytics**, escolha **configurar**. 
   3. Em **áreas de trabalho do OMS**, selecione a área de trabalho do OMS a utilizar para o registo.
   4. Em **registo**, selecione o **IntegrationAccountTrackingEvents** categoria.
   5. Quando tiver terminado, escolha **guardar**.

   ![Configurar a análise de registos para que pode enviar dados de diagnóstico para um registo](media/logic-apps-monitor-b2b-message/send-diagnostics-data-log-analytics-workspace.png)

5. Agora [configurar o controlo para as mensagens B2B no OMS](../logic-apps/logic-apps-track-b2b-messages-omsportal.md).

## <a name="extend-how-and-where-you-use-diagnostic-data-with-other-services"></a>Expandir como e onde utiliza dados de diagnóstico com outros serviços

Juntamente com o Log Analytics do Azure, pode expandir como utilizar dados de diagnóstico da sua aplicação lógica com outros serviços do Azure, por exemplo: 

* [Arquivo de registos de diagnóstico do Azure no Storage do Azure](../monitoring-and-diagnostics/monitoring-archive-diagnostic-logs.md)
* [Transmitir os registos de diagnóstico do Azure para Event Hubs do Azure](../monitoring-and-diagnostics/monitoring-stream-diagnostic-logs-to-event-hubs.md) 

Pode, em seguida, a monitorização utilizando a telemetria e análise de outros serviços, como get em tempo real [Azure Stream Analytics](../stream-analytics/stream-analytics-introduction.md) e [Power BI](../log-analytics/log-analytics-powerbi.md). Por exemplo:

* [Dados de fluxo de Hubs de eventos para o Stream Analytics](../stream-analytics/stream-analytics-define-inputs.md)
* [Analisar dados de transmissão em fluxo com o Stream Analytics e criar um dashboard de análise em tempo real no Power BI](../stream-analytics/stream-analytics-power-bi-dashboard.md)

Com base nas opções que pretende configurar, certifique-se de que a primeira [criar uma conta de armazenamento do Azure](../storage/common/storage-create-storage-account.md) ou [criar um hub de eventos do Azure](../event-hubs/event-hubs-create.md). Em seguida, selecione as opções para onde pretende enviar dados de diagnóstico:

![Enviar dados para o hub de conta ou evento de armazenamento do Azure](./media/logic-apps-monitor-b2b-message/storage-account-event-hubs.png)

> [!NOTE]
> Períodos de retenção aplicam-se apenas quando optar por utilizar uma conta de armazenamento.

## <a name="supported-tracking-schemas"></a>Esquemas de controlo suportados

Azure suporta estes tipos de esquema, que corrigir esquemas, exceto o tipo personalizado de controlo.

* [Esquemas de controlo de AS2](../logic-apps/logic-apps-track-integration-account-as2-tracking-schemas.md)
* [Esquemas de controlo de X12](../logic-apps/logic-apps-track-integration-account-x12-tracking-schema.md)
* [Personalizar esquema de controlo](../logic-apps/logic-apps-track-integration-account-custom-tracking-schema.md)

## <a name="next-steps"></a>Passos seguintes

* [Controlar as mensagens B2B no OMS](../logic-apps/logic-apps-track-b2b-messages-omsportal.md "mensagens B2B controlar no OMS")
* [Saiba mais sobre o Enterprise Integration Pack](../logic-apps/logic-apps-enterprise-integration-overview.md "Saiba mais sobre o pacote de integração do Enterprise")

