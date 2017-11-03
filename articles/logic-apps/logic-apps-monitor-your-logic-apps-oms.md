---
title: "Monitorizar e obter informações sobre a sua aplicação lógica é executado utilizando OMS - Azure Logic Apps | Microsoft Docs"
description: "Monitorizar as execuções de aplicação lógica com o Operations Management Suite (OMS) e de análise do registo para obter informações e detalhes de depuração mais rico para resolução de problemas e diagnóstico"
author: divyaswarnkar
manager: anneta
editor: 
services: logic-apps
documentationcenter: 
ms.assetid: 
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/9/2017
ms.author: LADocs; divswa
ms.openlocfilehash: 8da2bc9645e432ddf0e9f627c7b5e30c44fd74b6
ms.sourcegitcommit: 963e0a2171c32903617d883bb1130c7c9189d730
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/20/2017
---
# <a name="monitor-and-get-insights-about-logic-app-runs-with-operations-management-suite-oms-and-log-analytics"></a>Monitorizar e obter informações sobre a execução de aplicação lógica com o Operations Management Suite (OMS) e análise de registos

Para informações de depuração e Rica de monitorização, pode ativar análise de registos ao mesmo tempo, quando criar uma aplicação lógica. Análise de registos fornece diagnóstico de registo e monitorização para a sua aplicação lógica é executada através do portal do Operations Management Suite (OMS). Quando adiciona a solução de gestão de aplicações lógicas OMS, obter o estado agregado da sua execuções de aplicação de lógica e os detalhes específicos, como o estado, o tempo de execução, o estado de resubmission e o ID de correlação.

Este tópico mostra como ativar análise de registos ou instalar a solução de gestão de aplicações lógicas na OMS, para que possa vê eventos de tempo de execução e os dados para a sua aplicação lógica executar.

 > [!TIP]
 > Para monitorizar as logic apps existentes, siga estes passos para [ativar o registo de diagnóstico e enviar dados de tempo de execução da aplicação de lógica OMS](../logic-apps/logic-apps-monitor-your-logic-apps.md#azure-diagnostics).

## <a name="requirements"></a>Requisitos

Antes de começar, tem de ter uma área de trabalho do OMS. Saiba [como criar uma área de trabalho do OMS](../log-analytics/log-analytics-get-started.md). 

## <a name="turn-on-diagnostics-logging-when-creating-logic-apps"></a>Ativar o registo de diagnóstico ao criar as logic apps

1. No [portal do Azure](https://portal.azure.com), criar uma aplicação lógica. Escolha **novo** > **integração empresarial com** > **aplicação lógica** > **criar**.

   ![Criar uma aplicação lógica](media/logic-apps-monitor-your-logic-apps-oms/find-logic-apps-azure.png)

2. No **Criar aplicação de lógica** página, efetuar estas tarefas, conforme mostrado:

   1. Forneça um nome para a sua aplicação lógica e selecione a sua subscrição do Azure. 
   2. Crie ou selecione um grupo de recursos do Azure.
   3. Definir **Iniciar análise** para **no**. 
   Selecione a área de trabalho do OMS onde pretende enviar dados para a sua aplicação lógica é executado. 
   4. Quando estiver pronto, selecione **afixar ao dashboard** > **criar**.

      ![Criar uma aplicação lógica](./media/logic-apps-monitor-your-logic-apps-oms/create-logic-app.png)

      Depois de concluir este passo, o Azure cria a aplicação lógica, que está agora associado à sua área de trabalho do OMS. 
      Além disso, este passo instala também automaticamente a solução de gestão de aplicações lógicas na sua área de trabalho do OMS.

3. Para ver a lógica de aplicação é executada na OMS, [continuar com estes passos](#view-logic-app-runs-oms).

## <a name="install-the-logic-apps-management-solution-in-oms"></a>Instalar a solução de gestão de aplicações lógicas no OMS

Se já ativou análise de registos quando criou a sua aplicação lógica, ignore este passo. Já tem a solução de gestão de aplicações lógicas instalada no OMS.

1. No [portal do Azure](https://portal.azure.com), escolha **mais serviços**. Procure "análise de registos" como o filtro e escolha **Log Analytics** conforme mostrado:

   ![Escolha "Análise de registos"](media/logic-apps-monitor-your-logic-apps-oms/find-log-analytics.png)

2. Em **Log Analytics**, localize e selecione a sua área de trabalho do OMS. 

   ![Selecione a sua área de trabalho do OMS](media/logic-apps-monitor-your-logic-apps-oms/select-logic-app.png)

3. Em **gestão**, escolha **Portal do OMS**.

   ![Escolha "Portal do OMS"](media/logic-apps-monitor-your-logic-apps-oms/oms-portal-page.png)

4. Na sua home page do OMS, se for apresentada a faixa de atualização, escolha a faixa para que o primeiro a atualizar a área de trabalho do OMS. Em seguida, escolha **soluções galeria**.

   ![Escolha "Soluções Galeria"](media/logic-apps-monitor-your-logic-apps-oms/solutions-gallery.png)

5. Em **todas as soluções**, localizar e escolha o mosaico relativo a **de gestão de aplicações lógicas** solução.

   ![Escolha "Lógica de gestão de aplicações"](media/logic-apps-monitor-your-logic-apps-oms/logic-apps-management-tile2.png)

6. Para instalar a solução na sua área de trabalho do OMS, escolha **adicionar**.

   ![Escolha "Adicionar" para "Gestão de aplicações lógicas"](media/logic-apps-monitor-your-logic-apps-oms/add-logic-apps-management-solution.png)

<a name="view-logic-app-runs-oms"></a>

## <a name="view-your-logic-app-runs-in-your-oms-workspace"></a>Vista de que execução na sua aplicação lógica na sua área de trabalho do OMS

1. Para ver a contagem e um Estado para a execução de aplicação lógica, aceda à página de descrição geral para a sua área de trabalho do OMS. Reveja os detalhes no **de gestão de aplicações lógicas** mosaico.

   ![Mosaico de descrição geral que mostra o estado e contagem de executar de aplicação lógica](media/logic-apps-monitor-your-logic-apps-oms/overview.png)

   > [!Note]
   > Se esta faixa de atualização é apresentado em vez do mosaico de lógica de gestão de aplicações, escolha a faixa, para que o primeiro a atualizar a área de trabalho do OMS.
  
   > ![Atualizar "Área de trabalho do OMS"](media/logic-apps-monitor-your-logic-apps-oms/oms-upgrade-banner.png)

2. Para ver um resumo com mais detalhes sobre a execução de aplicação lógica, escolha o **de gestão de aplicações lógicas** mosaico.

   Aqui, a execução de aplicação lógica é agrupadas por nome ou por Estado de execução. Também pode ver detalhes sobre as falhas na ações ou acionadores para execuções de aplicação lógica.

   ![Executa o estado de resumo para a sua aplicação lógica](media/logic-apps-monitor-your-logic-apps-oms/logic-apps-runs-summary.png)
   
3. Para ver todas as execuções de uma aplicação lógica específica ou o estado, selecione a linha para um Estado ou de uma aplicação lógica.

   Eis um exemplo que mostra todas as execuções de uma aplicação lógica específica:

   ![Vista de execução para um Estado ou de uma aplicação lógica](media/logic-apps-monitor-your-logic-apps-oms/logic-app-run-details.png)

   Existem duas opções avançadas nesta página:
   * **Controlados propriedades:** esta coluna apresenta propriedades controladas, quais estão agrupadas por ações, para a aplicação lógica. Para ver as propriedades controladas, escolha **vista**. Pode procurar as propriedades controladas utilizando o filtro de coluna.
   
     ![Ver propriedades controladas para uma aplicação lógica](media/logic-apps-monitor-your-logic-apps-oms/logic-app-tracked-properties.png)

     As propriedades de controlado recentemente adicionadas poderão demorar 10 a 15 minutos antes de serem apresentados pela primeira vez. Saiba [como adicionar propriedades controladas a sua aplicação lógica](logic-apps-monitor-your-logic-apps.md#azure-diagnostics-event-settings-and-details).

   * **Volte a submeter:** pode submeter um ou mais execuções de aplicação lógica que falhou, com êxito, ou são ainda em execução. Selecione as caixas de verificação para a execução que pretende submeter e escolha **reenviar**. 

     ![Volte a submeter execuções de aplicação lógica](media/logic-apps-monitor-your-logic-apps-oms/logic-app-resubmit.png)

4. Para filtrar estes resultados, pode realizar a filtragem do lado do cliente e do lado do servidor.

   * Filtro de lado do cliente: para cada coluna, escolha os filtros que pretende. 
   Eis alguns exemplos:

     ![Filtros de coluna de exemplo](media/logic-apps-monitor-your-logic-apps-oms/filters.png)

   * Filtro de lado do servidor: escolher um intervalo de tempo específico ou para limitar o número de execuções que são apresentadas, utilize o controlo de âmbito na parte superior da página. 
   Por predefinição, apenas 1000 registos aparecem uma vez. 
   
     ![Alterar a janela de tempo](media/logic-apps-monitor-your-logic-apps-oms/change-interval.png)
 
5. Para ver todas as ações e os respetivos detalhes para uma execução específico, selecione uma linha para uma aplicação lógica de execução.

   Eis um exemplo que mostra todas as ações para executar uma aplicação de lógica específica:

   ![Executam ações de vista para uma aplicação lógica](media/logic-apps-monitor-your-logic-apps-oms/logic-app-action-details.png)
   
6. Em qualquer página de resultados, para ver a consulta atrás os resultados ou para ver todos os resultados, escolha **ver todos os**, que abre a página de pesquisa de registo.
   
   ![Ver todos os em páginas de resultados](media/logic-apps-monitor-your-logic-apps-oms/logic-app-seeall.png)
   
   Na página de pesquisa de registo,
   * Para ver os resultados da consulta numa tabela, escolha **tabela**.
   * Para alterar a consulta, pode editar a cadeia de consulta na barra de procura. 
   Para uma melhor experiência, escolha **análise avançadas**.

     ![Ver detalhes para uma aplicação lógica de execução e de ações](media/logic-apps-monitor-your-logic-apps-oms/log-search-page.png)
     
     Aqui, na página do Log Analytics do Azure, pode atualizar consultas e ver os resultados da tabela. 
     Esta consulta utiliza [idioma de consulta Kusto](https://docs.loganalytics.io/docs/Language-Reference), que pode editar se quiser ver resultados diferentes. 

     ![Análise de registos do Azure - vista de consulta](media/logic-apps-monitor-your-logic-apps-oms/query.png)

## <a name="next-steps"></a>Passos seguintes

* [Monitorizar mensagens B2B](../logic-apps/logic-apps-monitor-b2b-message.md)

