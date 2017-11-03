---
title: "Consulta de análise de registos do Azure para monitorizar clusters do HDInsight do Azure | Microsoft Docs"
description: "Saiba como executar consultas na análise de registos do Azure para monitorizar tarefas em execução num HDInsight cluster."
services: hdinsight
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.service: hdinsight
ms.custom: hdinsightactive
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/11/2017
ms.author: nitinme
ms.openlocfilehash: 8fe91bed69a1c06367346041d8caba4aaee4c82a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="query-azure-log-analytics-to-monitor-hdinsight-clusters-preview"></a>Análise de registos do Azure de consulta para monitorizar clusters do HDInsight (pré-visualização)

Neste artigo, observe o alguns cenários sobre como utilizar Log Analytics do Azure com clusters do HDInsight do Azure. Três cenários mais comuns são:

* Analisar as métricas de cluster do HDInsight no OMS
* Procure mensagens de registo específicas para clusters do HDInsight
* Criar alertas com base em eventos que ocorrem os clusters

## <a name="prerequisites"></a>Pré-requisitos

* Tem de ter configurado um cluster do HDInsight ao utilizar a análise de registos do Azure. Para obter instruções, consulte [Log Analytics do Azure utilizada com clusters do HDInsight](hdinsight-hadoop-oms-log-analytics-tutorial.md).

* Tem de ter adicionado as soluções de gestão de específicas do cluster do HDInsight para a área de trabalho do OMS conforme descrito em [soluções de gestão de cluster do HDInsight adicionar ao Log Analytics](hdinsight-hadoop-oms-log-analytics-management-solutions.md).

## <a name="analyze-hdinsight-cluster-metrics-in-oms"></a>Analisar as métricas de cluster do HDInsight no OMS

Nesta secção, vamos percorrer os passos para procurar as métricas específicas para o seu cluster HDInsight.

1. Abra o dashboard do OMS. No portal do Azure, abra o painel de cluster do HDInsight que que tenha associado às Log Analytics do Azure, clique no separador monitorização e clique em **abrir o Dashboard do OMS**.

    ![Dashboard OMS abra](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-open-oms-dashboard.png "dashboard abrir OMS")

2. No dashboard do OMS, a partir do ecrã principal, clique em **pesquisa registo**.

    ![Abra pesquisa de registo](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-click-log-search.png "abra pesquisa de registo")

3. Na janela pesquisa de registo, no **Begin pesquisa aqui** caixa de texto, escreva `*` para procurar em todas as métricas para todas as métricas disponíveis para todos os clusters do HDInsight configurados para utilizar a análise de registos do Azure. Prima ENTER.

    ![Procurar em todas as métricas](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-search-all-metrics.png "todas as métricas de pesquisa")

4. Deverá ver um resultado como o seguinte.

    ![Procurar em todas as saídas de métricas](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-search-all-metrics-output.png "procurar todas as saídas de métricas")

5. No painel esquerdo, em **tipo** categoria, a pesquisa a métrica que pretende aprofundar aprofundadamente. Para este tutorial, vamos escolher `metrics_resourcemanager_queue_root_default_CL`. Selecione a caixa de verificação correspondente para a métrica e, em seguida, clique em **aplicar**.

    > [!NOTE]
    > Poderá ter de clicar o **[+] mais** botão para localizar a métrica que procura. Além disso, o **aplicar** botão está na parte inferior da lista de, pelo que tem desloque para baixo para vê-lo.
    > 
    >    
    Repare que a consulta na caixa de texto agora alterações mostrado na caixa de realçado na captura de ecrã seguinte:

    ![Pesquisa de métricas específicas](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-search-specific-metrics.png "procure métricas específicas")

6. -Pode agora aprofundar esta métrica específica. Por exemplo, agora pode refinar a saída existente com base em média dos recursos utilizados num intervalo de 10 minutos, categorizado pelo nome do cluster. Escreva a consulta seguinte na caixa de texto de consulta.

        * (Type=metrics_resourcemanager_queue_root_default_CL) | measure avg(UsedAMResourceMB_d) by ClusterName_s interval 10minute

    ![Pesquisa de métricas específicas](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-search-more-specific-metrics.png "procure métricas específicas")

7. Em vez de limitar com base em média dos recursos utilizados, pode utilizar a seguinte consulta para refinar os resultados com base na quando os recursos máximos foram utilizados (bem como percentil 90th e 95th) numa janela de 10 minutos.

        * (Type=metrics_resourcemanager_queue_root_default_CL) | measure max(UsedAMResourceMB_d) , pct95(UsedAMResourceMB_d), pct90(UsedAMResourceMB_d)  by ClusterName_s interval 10minute

    ![Pesquisa de métricas específicas](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-search-more-specific-metrics-1.png "procure métricas específicas")

## <a name="search-for-specific-log-messages-in-hdinsight-clusters"></a>Procure mensagens de registo específico nos clusters do HDInsight

Nesta secção, vamos percorrer os passos para ver mensagens de erro durante uma janela de tempo específico. Os passos aqui descritos são apenas um exemplo de como pode chegar à mensagem de erro estão interessadas nas. Pode utilizar qualquer propriedade que está disponível para procurar erros que está a tentar encontrar.

1. Abra o dashboard do OMS. No portal do Azure, abra o painel de cluster do HDInsight que que tenha associado às Log Analytics do Azure, clique no separador monitorização e clique em **abrir o Dashboard do OMS**.

    ![Dashboard OMS abra](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-open-oms-dashboard.png "dashboard abrir OMS")

2. No dashboard do OMS, a partir do ecrã principal, clique em **pesquisa registo**.

    ![Abra pesquisa de registo](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-click-log-search.png "abra pesquisa de registo")

3. Na janela pesquisa de registo, no **Begin pesquisa aqui** caixa de texto, escreva `"Error"` (com as aspas) para procurar em todas as mensagens de erro para todos os clusters do HDInsight configurados para utilizar a análise de registos do Azure. Prima ENTER.

    ![Todos os erros de pesquisa](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-search-all-errors.png "todos os erros de pesquisa")

4. Deverá ver um resultado como o seguinte.

    ![Procurar em todas as saídas de erros](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-search-all-errors-output.png "procurar todas as saídas de erros")

5. No painel esquerdo, em **tipo** categoria, procure um tipo de erro que pretende aprofundar aprofundadamente. Para este tutorial, vamos escolher `log_sparkappsexecutors_CL`. Selecione a caixa de verificação correspondente para a métrica e, em seguida, clique em **aplicar**.

    ![Procure erros específicos](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-search-specific-error.png "procure o erro específico")

        
6. Tenha em atenção que a consulta na caixa de texto agora alterações mostrado na caixa de realçado abaixo e os resultados são avançadas para mostrar apenas o erro do tipo selecionado.

    ![Pesquisa para o resultado de erros específicos](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-search-specific-error-output.png "pesquisa para o resultado de erros específicos")

7. -Pode agora aprofundar esta lista de erro específico utilizando as opções disponíveis no painel esquerdo. Por exemplo, pode refinar a consulta para ver apenas as mensagens de erro de um nó de trabalho específico.

    ![Pesquisa para o resultado de erros específicos](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-search-specific-error-refined.png "pesquisa para o resultado de erros específicos")

8. Pode ainda mais zona na hora que pensa que o erro ocorreu ao selecionar o valor de tempo relevantes no painel esquerdo.

    ![Pesquisa para o resultado de erros específicos](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-search-specific-error-time.png "pesquisa para o resultado de erros específicos")

9. Estão agora para baixo até o erro específico que está a procurar. Pode clicar em **[+] Mostrar mais** para ver a mensagem de erro real.

    ![Pesquisa para o resultado de erros específicos](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-search-specific-error-arrived.png "pesquisa para o resultado de erros específicos")

## <a name="create-alerts-to-track-events"></a>Criar alertas para controlar eventos

O primeiro passo para criar um alerta é chegam a uma consulta com base na qual o alerta é acionado. Simplicidade, vamos utilizar a seguinte consulta, que fornece a lista de aplicações com falhas em execução nos clusters do HDInsight.

    * (Tipo = metrics_resourcemanager_queue_root_default_CL) AppsFailed_d > 0 

Pode utilizar qualquer consulta que pretende criar um alerta.

1. Abra o dashboard do OMS. No portal do Azure, abra o painel de cluster do HDInsight que que tenha associado às Log Analytics do Azure, clique no separador monitorização e clique em **abrir o Dashboard do OMS**.

    ![Dashboard OMS abra](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-open-oms-dashboard.png "dashboard abrir OMS")

2. No dashboard do OMS, a partir do ecrã principal, clique em **pesquisa registo**.

    ![Abra pesquisa de registo](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-click-log-search.png "abra pesquisa de registo")

3. Na janela pesquisa de registo, no **Begin pesquisa aqui** texto caixa, cole a consulta no qual pretende criar um alerta, prima ENTER e, em seguida, clique o **alerta** botão.

    ![Consulta de ENTER para criar um alerta](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-create-alert-query.png "Enter consulta para criar um alerta")

4. No **Adicionar regra de alerta** janela, introduza a consulta e outros detalhes para criar um alerta e, em seguida, clique em **guardar**.

    ![Consulta de ENTER para criar um alerta](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-create-alert.png "Enter consulta para criar um alerta")

    Nesta captura de ecrã, apenas Enviámos uma notificação de correio eletrónico se a consulta de alerta obtém um resultado.

5. Também pode editar ou eliminar um alerta existente. Para tal, a partir de qualquer página no portal do OMS, clique em de **definições** ícone.

    ![Consulta de ENTER para criar um alerta](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-edit-alert.png "Enter consulta para criar um alerta")

6. Do **definições** página, clique em **alertas** para ver os alertas que criou. Também pode ativar ou desativar um alerta, editar ou eliminá-lo. Para obter mais informações, consulte [trabalhar com regras de alertas na análise de registos](../log-analytics/log-analytics-alerts-creating.md).

## <a name="see-also"></a>Consultar também

* [Trabalhar com a análise de registos do OMS](https://blogs.msdn.microsoft.com/wei_out_there_with_system_center/2016/07/03/oms-log-analytics-create-tiles-drill-ins-and-dashboards-with-the-view-designer/)
* [Criar regras de alertas na análise de registos](../log-analytics/log-analytics-alerts-creating.md)
