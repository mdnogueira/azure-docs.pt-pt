---
title: "Utilizar a análise de registos para monitorizar clusters do HDInsight do Azure | Microsoft Docs"
description: "Saiba como utilizar Log Analytics do Azure para monitorizar tarefas em execução num HDInsight cluster."
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
ms.openlocfilehash: 6677b0b3ed047ce011bfbb72c25e45195859830a
ms.sourcegitcommit: 933af6219266cc685d0c9009f533ca1be03aa5e9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/18/2017
---
# <a name="use-azure-log-analytics-to-monitor-hdinsight-clusters"></a>Utilizar Log Analytics do Azure para monitorizar clusters do HDInsight

Saiba como utilizar Log Analytics do Azure para monitorizar as operações de cluster de Hadoop no HDInsight.

[Análise de registo](../log-analytics/log-analytics-overview.md) é um serviço no [Operations Management Suite (OMS)](../operations-management-suite/operations-management-suite-overview.md) que monitoriza a sua nuvem e no local ambientes para manter a respetiva disponibilidade e desempenho. Recolhe dados gerados por recursos nos seus ambientes na cloud e no local e de outras ferramentas de monitorização, para disponibilizar análises relativas a várias origens. 

## <a name="prerequisites"></a>Pré-requisitos

* **Uma subscrição do Azure**. Antes de começar este tutorial, tem de ter uma subscrição do Azure. Veja o artigo [Crie hoje a sua conta do Azure gratuita](https://azure.microsoft.com/free).

* **Um cluster do Azure HDInsight**. Atualmente, pode utilizar o Azure Operations Management Suite com os seguintes tipos de cluster do HDInsight:

    * Hadoop
    * HBase
    * Interactive Query
    * Kafka
    * Spark
    * Storm

    Para instruções sobre como criar um cluster do HDInsight, consulte [introdução ao Azure HDInsight](hadoop/apache-hadoop-linux-tutorial-get-started.md).

* **Uma área de trabalho de análise de registos**. Pode considerar esta área de trabalho como um ambiente de análise de registos exclusivo com as suas próprias repositório de dados, as origens de dados e soluções. Tem de ter uma essa área de trabalho já criada que pode associar os clusters do HDInsight do Azure. Para obter instruções, consulte [criar uma área de trabalho de análise de registos](../log-analytics/log-analytics-quick-collect-azurevm.md#create-a-workspace).

## <a name="configure-hdinsight-cluster-to-use-log-analytics"></a>Configurar o cluster do HDInsight ao utilizar a análise de registos

Nesta secção, configurar um cluster de Hadoop do HDInsight existente para utilizar uma área de trabalho do Log Analytics do Azure para monitorizar tarefas, registos de depuração, etc.

1. Abra um cluster do HDInsight no portal do Azure.
2. No painel esquerdo, clique em **monitorização**.
3. No painel direito, clique em **ativar**, selecione uma área de trabalho de análise de registos existente e, em seguida, clique em **guardar**.

    ![Ativar a monitorização de clusters do HDInsight](./media/hdinsight-hadoop-oms-log-analytics-tutorial/hdinsight-enable-monitoring.png "ativar a monitorização de clusters do HDInsight")

    Demora alguns instantes para guardar a definição.  Depois de estar concluído, pode ver um **abra OMS Dashboard** botão na parte superior. 

    ![Dashboard do Operations Management Suite abra](./media/hdinsight-hadoop-oms-log-analytics-tutorial/hdinsight-enable-monitoring-open-workspace.png "dashboard abrir OMS")

5. Clique em **dashboard abrir OMS**.
6. Se lhe for solicitado, introduza as suas credenciais do Azure.

    ![Portal do Operations Management Suite](./media/hdinsight-hadoop-oms-log-analytics-tutorial/hdinsight-enable-monitoring-oms-portal.png "portal Operations Management Suite")

## <a name="next-steps"></a>Passos seguintes
* [Adicione as soluções de gestão de cluster do HDInsight ao Log Analytics](hdinsight-hadoop-oms-log-analytics-management-solutions.md)
