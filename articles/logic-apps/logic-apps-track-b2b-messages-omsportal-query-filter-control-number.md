---
title: Consulta para mensagens B2B no Operations Management Suite - Azure Logic Apps | Microsoft Docs
description: Criar consultas para controlar AS2, X12 e mensagens EDIFACT no Operations Management Suite
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
ms.date: 07/21/2017
ms.author: LADocs; padmavc
ms.openlocfilehash: 2748d3d3daf7c13dca05f663a4a088598e1b3605
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="query-for-as2-x12-and-edifact-messages-in-the-microsoft-operations-management-suite-oms"></a>Consultar AS2, X12 e EDIFACT mensagens no Microsoft Operations Management Suite (OMS)

Para localizar o AS2, X12 ou EDIFACT mensagens que está a controlar com [Log Analytics do Azure](../log-analytics/log-analytics-overview.md) no [Operations Management Suite (OMS)](../operations-management-suite/operations-management-suite-overview.md), pode criar consultas que filtrar ações com base em critérios específicos. Por exemplo, pode encontrar as mensagens com base num número específico de intercâmbio de controlo.

## <a name="requirements"></a>Requisitos

* Uma aplicação lógica que esteja configurada com o registo de diagnóstico. Saiba [como criar uma aplicação lógica](../logic-apps/logic-apps-create-a-logic-app.md) e [como configurar o registo para essa aplicação lógica](../logic-apps/logic-apps-monitor-your-logic-apps.md#azure-diagnostics).

* Uma conta de integração que está configurada com a monitorização e registo. Saiba [como criar uma conta de integração](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) e [como configurar a monitorização e o registo para essa conta](../logic-apps/logic-apps-monitor-b2b-message.md).

* Se ainda não o fez, [publicar dados de diagnóstico ao Log Analytics](../logic-apps/logic-apps-track-b2b-messages-omsportal.md) e [configurar a mensagem de controlo no OMS](../logic-apps/logic-apps-track-b2b-messages-omsportal.md).

> [!NOTE]
> Após cumprir os requisitos anteriores, deve ter uma área de trabalho a [Operations Management Suite (OMS)](../operations-management-suite/operations-management-suite-overview.md). Deve utilizar o mesmo espaço de trabalho do OMS para controlar a sua comunicação B2B no OMS. 
>  
> Se não tiver uma área de trabalho do OMS, saiba [como criar uma área de trabalho do OMS](../log-analytics/log-analytics-get-started.md).

## <a name="create-message-queries-with-filters-in-the-operations-management-suite-portal"></a>Criar consultas de mensagens com filtros no portal do Operations Management Suite

Este exemplo mostra como pode encontrar com base no respetivo número de controlo de intercâmbio de mensagens.

> [!TIP] 
> Se souber o nome de área de trabalho do OMS, aceda à sua home page da área de trabalho (`https://{your-workspace-name}.portal.mms.microsoft.com`) e iniciar o passo 4. Caso contrário, comece no passo 1.

1. No [portal do Azure](https://portal.azure.com), escolha **mais serviços**. Procure "análise de registos" e, em seguida, escolha **Log Analytics** conforme mostrado aqui:

   ![Localizar a análise de registos](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/browseloganalytics.png)

2. Em **Log Analytics**, localize e selecione a sua área de trabalho do OMS.

   ![Selecione a sua área de trabalho do OMS](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/selectla.png)

3. Em **gestão**, escolha **Portal do OMS**.

   ![Escolha o portal do OMS](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/omsportalpage.png)

4. Na sua home page do OMS, escolha **pesquisa registo**.

   ![Na sua home page do OMS, escolha "Registo de pesquisa"](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/logsearch.png)

   -ou-

   ![No menu do OMS, escolha "Registo de pesquisa"](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/logsearch-2.png)

5. Na caixa de pesquisa, introduza um campo que pretende localizar e prima **Enter**. Quando começar a escrever, OMS mostra-lhe correspondências possíveis e as operações que pode utilizar. Saiba mais sobre [como localizar dados na análise de registos](../log-analytics/log-analytics-log-searches.md).

   Neste exemplo procura para eventos com **tipo = AzureDiagnostics**.

   ![Comece a escrever a cadeia de consulta](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/oms-start-query.png)

6. Na barra da esquerda, escolha o período de tempo que pretende visualizar. Para adicionar um filtro à consulta, escolha **+ adicionar**.

   ![Adicionar filtro de consulta](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/query1.png)

7. Em **adicionar filtros**, introduza o nome do filtro, pelo que pode encontrar o filtro que pretende. Selecione o filtro e escolha **+ adicionar**.

   Para determinar o número de controlo do intercâmbio, neste exemplo procura para a palavra "intercâmbio" e seleciona **event_record_messageProperties_interchangeControlNumber_s** como o filtro.

   ![Selecione o filtro](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/oms-query-add-filter.png)

9. Na barra da esquerda, selecione o valor do filtro que pretende utilizar e escolha **aplicar**.

   Neste exemplo seleciona o número de controlo de intercâmbio de mensagens que queremos.

   ![Selecione o valor do filtro](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/oms-query-select-filter-value.png)

10. Agora, volte para a consulta que está a criar. A consulta foi atualizada com o evento de filtro selecionado e o valor. Os resultados anteriores agora são filtrados demasiado.

    ![Regressar à sua consulta com resultados filtrados](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/oms-query-filtered-results.png)

<a name="save-oms-query"></a>

## <a name="save-your-query-for-future-use"></a>Guardar a consulta para utilização futura

1. Da sua consulta no **pesquisa registo** página, escolha **guardar**. Dê um nome a sua consulta, selecione uma categoria e escolha **guardar**.

   ![A consulta de dar um nome e categoria](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/oms-query-save.png)

2. Para ver a consulta, escolha **Favoritos**.

   ![Escolha "Favoritos"](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/oms-query-favorites.png)

3. Em **pesquisas guardadas**, selecione a consulta para que possa visualizar os resultados. Para atualizar a consulta para que possa encontrar resultados diferentes, edite a consulta.

   ![Selecione a sua consulta](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/oms-log-search-find-favorites.png)

## <a name="find-and-run-saved-queries-in-the-operations-management-suite-portal"></a>Localizar e executar consultas guardadas no portal do Operations Management Suite

1. Abra a sua home page de área de trabalho do OMS (`https://{your-workspace-name}.portal.mms.microsoft.com`) e escolha **pesquisa registo**.

   ![Na sua home page do OMS, escolha "Registo de pesquisa"](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/logsearch.png)

   -ou-

   ![No menu do OMS, escolha "Registo de pesquisa"](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/logsearch-2.png)

2. No **pesquisa registo** página inicial, escolha **Favoritos**.

   ![Escolha "Favoritos"](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/oms-log-search-favorites.png)

3. Em **pesquisas guardadas**, selecione a consulta para que possa visualizar os resultados. Para atualizar a consulta para que possa encontrar resultados diferentes, edite a consulta.

   ![Selecione a sua consulta](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/oms-log-search-find-favorites.png)

## <a name="next-steps"></a>Passos seguintes

* [Esquemas de controlo de AS2](../logic-apps/logic-apps-track-integration-account-as2-tracking-schemas.md)
* [Esquemas de controlo de X12](../logic-apps/logic-apps-track-integration-account-x12-tracking-schema.md)
* [Esquemas de controlo personalizado](../logic-apps/logic-apps-track-integration-account-custom-tracking-schema.md)