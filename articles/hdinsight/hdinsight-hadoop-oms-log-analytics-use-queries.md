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
ms.date: 11/17/2017
ms.author: nitinme
ms.openlocfilehash: da7b83846418bfe5f95b126d4f5f7b34d3a7b35d
ms.sourcegitcommit: 933af6219266cc685d0c9009f533ca1be03aa5e9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/18/2017
---
# <a name="query-azure-log-analytics-to-monitor-hdinsight-clusters"></a>Análise de registos do Azure de consulta para monitorizar clusters do HDInsight

Aprender alguns cenários básicos sobre como utilizar Log Analytics do Azure para monitorizar clusters do HDInsight do Azure:

* [Analisar as métricas de cluster do HDInsight](#analyze-hdinsight-cluster-metrics)
* [Procure mensagens de registo específico](#search-for-specific-log-messages)
* [Criar alertas de eventos](#create-alerts-for-tracking-events)

## <a name="prerequisites"></a>Pré-requisitos

* Tem de ter configurado um cluster do HDInsight ao utilizar a análise de registos do Azure. Para obter instruções, consulte [Log Analytics do Azure utilizada com clusters do HDInsight](hdinsight-hadoop-oms-log-analytics-tutorial.md).

* Tem de ter adicionado as soluções de gestão de específicas do cluster do HDInsight para o [Operations Management Suite (OMS)](../operations-management-suite/operations-management-suite-overview.md) área de trabalho, tal como descrito no [soluções de gestão de cluster do HDInsight adicionar ao Log Analytics](hdinsight-hadoop-oms-log-analytics-management-solutions.md).

## <a name="analyze-hdinsight-cluster-metrics"></a>Analisar as métricas de cluster do HDInsight

Saiba como procurar métricas específicas para o seu cluster HDInsight.

1. Abra um cluster do HDInsight que tiver associado Log Analytics do Azure no portal do Azure.
2. Clique em **monitorização**e, em seguida, clique em **abra OMS Dashboard**.

    ![Dashboard OMS abra](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-open-oms-dashboard.png "dashboard abrir OMS")

2. Clique em **pesquisa registo** no menu da esquerda.

    ![Abra pesquisa de registo](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-click-log-search.png "abra pesquisa de registo")

3. Escreva a consulta seguinte na caixa de procura para procurar em todas as métricas para todas as métricas disponíveis para todos os clusters do HDInsight configurados para utilizar a análise de registos do Azure e, em seguida, prima **ENTER**.

        `search *` 

    ![Procurar em todas as métricas](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-search-all-metrics.png "todas as métricas de pesquisa")

    O resultado deverá ter o seguinte aspeto:

    ![Procurar em todas as saídas de métricas](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-search-all-metrics-output.png "procurar todas as saídas de métricas")

5. No painel esquerdo, em **tipo**, selecione uma métrica que pretende aprofundar aprofundadamente e, em seguida, clique em **aplicar**. A seguinte captura de ecrã mostra o `metrics_resourcemanager_queue_root_default_CL` é selecionado o tipo. 

    > [!NOTE]
    > Poderá ter de clicar no **[+] mais** botão para localizar a métrica que procura. Além disso, o **aplicar** botão está na parte inferior da lista de, pelo que tem desloque para baixo para vê-lo.
    > 
    >    

    Repare que a consulta na caixa de texto é alterado para mostrado na caixa de realçado na captura de ecrã seguinte:

    ![Pesquisa de métricas específicas](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-search-specific-metrics.png "procure métricas específicas")

6. Para aprofundar esta métrica específica. Por exemplo, pode refinar a saída existente com base em média dos recursos utilizados num intervalo de 10 minutos, categorizado pelo nome do cluster utilizando a seguinte consulta:

        search in (metrics_resourcemanager_queue_root_default_CL) * | summarize AggregatedValue = avg(UsedAMResourceMB_d) by ClusterName_s, bin(TimeGenerated, 10m)

7. Em vez de limitar com base em média dos recursos utilizados, pode utilizar a seguinte consulta para refinar os resultados com base na quando os recursos máximos foram utilizados (bem como percentil 90th e 95th) numa janela de 10 minutos:

        search in (metrics_resourcemanager_queue_root_default_CL) * | summarize ["max(UsedAMResourceMB_d)"] = max(UsedAMResourceMB_d), ["pct95(UsedAMResourceMB_d)"] = percentile(UsedAMResourceMB_d, 95), ["pct90(UsedAMResourceMB_d)"] = percentile(UsedAMResourceMB_d, 90) by ClusterName_s, bin(TimeGenerated, 10m)

## <a name="search-for-specific-log-messages"></a>Procure mensagens de registo específico

Saiba como consultar mensagens de erro durante uma janela de tempo específico. Os passos aqui descritos são apenas um exemplo de como pode chegar à mensagem de erro estão interessadas nas. Pode utilizar qualquer propriedade que está disponível para procurar erros que está a tentar encontrar.

1. Abra um cluster do HDInsight que tiver associado Log Analytics do Azure no portal do Azure.
2. Clique em **monitorização**e clique em **abra OMS Dashboard**.

    ![Dashboard OMS abra](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-open-oms-dashboard.png "dashboard abrir OMS")

2. No dashboard do OMS, a partir do ecrã principal, clique em **pesquisa registo**.

    ![Abra pesquisa de registo](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-click-log-search.png "abra pesquisa de registo")

3. Tipo a seguinte consulta para procurar em todas as mensagens de erro para todos os clusters do HDInsight configurados para utilizar a análise de registos do Azure e, em seguida, prima **ENTER**. 

         search "Error"

    Deverá ver um resultado como o seguinte resultado:

    ![Procurar em todas as saídas de erros](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-search-all-errors-output.png "procurar todas as saídas de erros")

5. No painel esquerdo, em **tipo** categoria, selecione um tipo de erro que pretende aprofundar aprofundadamente e, em seguida, clique em **aplicar**.  Tenha em atenção de que os resultados são foi refinados para mostrar apenas o erro do tipo selecionado.
7. -Pode aprofundar esta lista de erro específico utilizando as opções disponíveis no painel esquerdo. Por exemplo, 

    - Para ver mensagens de erro a partir de um nó de trabalho específico:

        ![Pesquisa para o resultado de erros específicos](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-search-specific-error-refined.png "pesquisa para o resultado de erros específicos")

    - Para ver que ocorreu um erro num determinado momento:

        ![Pesquisa para o resultado de erros específicos](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-search-specific-error-time.png "pesquisa para o resultado de erros específicos")

9. Para ver o erro específico. Pode clicar em **[+] Mostrar mais** para ver a mensagem de erro real.

    ![Pesquisa para o resultado de erros específicos](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-search-specific-error-arrived.png "pesquisa para o resultado de erros específicos")

## <a name="create-alerts-for-tracking-events"></a>Criar alertas para o registo de eventos

O primeiro passo para criar um alerta é chegam a uma consulta com base na qual o alerta é acionado. Simplicidade, vamos utilizar a seguinte consulta, que fornece a lista de aplicações com falhas em execução nos clusters do HDInsight.

    metrics_resourcemanager_queue_root_default_CL | where AppsFailed_d > 0

Pode utilizar qualquer consulta que pretende criar um alerta.

1. Abra um cluster do HDInsight que tiver associado Log Analytics do Azure no portal do Azure.
2. Clique em **monitorização**e, em seguida, clique em **abra OMS Dashboard**.

    ![Dashboard OMS abra](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-open-oms-dashboard.png "dashboard abrir OMS")

2. No dashboard do OMS, a partir do ecrã principal, clique em **pesquisa registo**.

    ![Abra pesquisa de registo](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-click-log-search.png "abra pesquisa de registo")

3. Execute a seguinte consulta no qual pretende criar um alerta e, em seguida, prima **ENTER**.

        metrics_resourcemanager_queue_root-default-CL | where AppsFailed_d > 0

4. Clique em **alerta** no topo da página.

    ![Consulta de ENTER para criar um alerta](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-create-alert-query.png "Enter consulta para criar um alerta")

4. No **Adicionar regra de alerta** janela, introduza a consulta e outros detalhes para criar um alerta e, em seguida, clique em **guardar**.

    ![Consulta de ENTER para criar um alerta](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-create-alert.png "Enter consulta para criar um alerta")

    A captura de ecrã mostra a configuração para enviar uma notificação por e-mail quando a consulta de alerta devolve um resultado.

5. Também pode editar ou eliminar um alerta existente. Para tal, a partir de qualquer página no portal do OMS, clique em de **definições** ícone.

    ![Consulta de ENTER para criar um alerta](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-edit-alert.png "Enter consulta para criar um alerta")

6. Do **definições** página, clique em **alertas** para ver os alertas que criou. Também pode ativar ou desativar um alerta, editar ou eliminá-lo. Para obter mais informações, consulte [trabalhar com regras de alertas na análise de registos](../log-analytics/log-analytics-alerts-creating.md).

## <a name="see-also"></a>Consultar também

* [Trabalhar com a análise de registos do Operations Management Suite](https://blogs.msdn.microsoft.com/wei_out_there_with_system_center/2016/07/03/oms-log-analytics-create-tiles-drill-ins-and-dashboards-with-the-view-designer/)
* [Criar regras de alertas na análise de registos](../log-analytics/log-analytics-alerts-creating.md)
