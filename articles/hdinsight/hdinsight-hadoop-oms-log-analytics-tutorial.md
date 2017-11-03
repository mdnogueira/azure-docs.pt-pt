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
ms.date: 09/12/2017
ms.author: nitinme
ms.openlocfilehash: c1c74c797449c2fa6d76438f9ec33eaf0fe51af4
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="use-azure-log-analytics-to-monitor-hdinsight-clusters-preview"></a>Utilizar Log Analytics do Azure para monitorizar clusters do HDInsight (pré-visualização)

Neste artigo, irá aprender a utilizar Log Analytics do Azure para monitorizar as operações de clusters do HDInsight Hadoop.

Análise de registos é um serviço no [Operations Management Suite (OMS)](../operations-management-suite/operations-management-suite-overview.md) que monitoriza a sua nuvem e no local ambientes para manter a respetiva disponibilidade e desempenho. Recolhe dados gerados por recursos nos seus ambientes na cloud e no local e de outras ferramentas de monitorização, para disponibilizar análises relativas a várias origens. 

## <a name="prerequisites"></a>Pré-requisitos

* **Uma subscrição do Azure**. Antes de começar este tutorial, tem de ter uma subscrição do Azure. Veja o artigo [Crie hoje a sua conta do Azure gratuita](https://azure.microsoft.com/free).

* **Um cluster do Azure HDInsight**. Atualmente, pode utilizar o Azure OMS com os seguintes tipos de cluster do HDInsight:
    * Hadoop
    * Spark
    * HBase
    * Storm
    * Kafka
    * Ramo de registo interativo

    Para obter instruções sobre como criar um cluster do HDInsight, consulte [introdução ao Azure HDInsight](hdinsight-hadoop-linux-tutorial-get-started.md).


* **Uma área de trabalho de análise de registos**. Pode considerar esta área de trabalho como um ambiente de análise de registos exclusivo com as suas próprias repositório de dados, as origens de dados e soluções. Tem de ter uma essa área de trabalho já criada que pode associar os clusters do HDInsight do Azure. Para obter instruções, consulte [criar uma área de trabalho de análise de registos](../log-analytics/log-analytics-quick-collect-azurevm.md#create-a-workspace).

## <a name="configure-hdinsight-cluster-to-use-azure-log-analytics"></a>Configurar o cluster do HDInsight ao utilizar a análise de registos do Azure

Nesta secção, configurar um cluster de Hadoop do HDInsight existente para utilizar uma área de trabalho do Log Analytics do Azure para monitorizar tarefas, registos de depuração, etc.

1. No portal do Azure, no painel esquerdo, clique em **clusters do HDInsight**e, em seguida, clique no nome do cluster que pretende configurar com Log Analytics do Azure.

2. No painel do cluster, no painel esquerdo, clique em **monitorização**.

3. No painel direito, clique em **ativar**e, em seguida, selecione uma área de trabalho de análise de registos existente. Clique em **Guardar**.

    ![Ativar a monitorização de clusters do HDInsight](./media/hdinsight-hadoop-oms-log-analytics-tutorial/hdinsight-enable-monitoring.png "ativar a monitorização de clusters do HDInsight")

4. Depois do cluster está configurado para utilizar a análise de registos para monitorização, verá um **abra OMS Dashboard** opção na parte superior do separador. Clique no botão.

    ![Dashboard OMS abra](./media/hdinsight-hadoop-oms-log-analytics-tutorial/hdinsight-enable-monitoring-open-workspace.png "dashboard abrir OMS")

5. Se lhe for solicitado, introduza as suas credenciais do Azure. O utilizador é direcionado para o dashboard de Microsoft OMS.

    ![Portal do Operations Management Suite](./media/hdinsight-hadoop-oms-log-analytics-tutorial/hdinsight-enable-monitoring-oms-portal.png "portal Operations Management Suite")

## <a name="next-steps"></a>Passos seguintes
* [Adicione as soluções de gestão de cluster do HDInsight ao Log Analytics](hdinsight-hadoop-oms-log-analytics-management-solutions.md)
