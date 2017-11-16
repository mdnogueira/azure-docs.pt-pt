---
title: "Adicione as soluções de gestão de cluster do HDInsight ao Log Analytics do Azure | Microsoft Docs"
description: Saiba como utilizar Log Analytics do Azure para criar vistas personalizadas para os clusters do HDInsight.
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
ms.date: 11/08/2017
ms.author: nitinme
ms.openlocfilehash: dc959f763e9a84199130bae845cb62c493676977
ms.sourcegitcommit: 9a61faf3463003375a53279e3adce241b5700879
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/15/2017
---
# <a name="add-hdinsight-cluster-management-solutions-to-log-analytics"></a>Adicione as soluções de gestão de cluster do HDInsight ao Log Analytics

O HDInsight fornece soluções de gestão de cluster específicos que pode adicionar para Log Analytics do Azure. As [soluções de gestão](../log-analytics/log-analytics-add-solutions.md) acrescentam funcionalidades ao OMS, proporcionando dados e ferramentas de análises adicionais ao Log Analytics. Estas soluções recolher métricas de desempenho importantes do seus clusters do HDInsight e fornecem as ferramentas para procurar as métricas. Estas soluções também fornecem visualizações e dashboards para a maioria dos tipos de cluster suportados no HDInsight. Ao utilizar as métricas que recolher com a solução, pode criar regras personalizadas de monitorização e alertas. 

Neste artigo, irá aprender a adicionar as soluções de gestão de cluster específicos para uma área de trabalho do OMS.

## <a name="prerequisites"></a>Pré-requisitos

* Tem de ter configurado um cluster do HDInsight ao utilizar a análise de registos do Azure. Para obter instruções, consulte [Log Analytics do Azure utilizada com clusters do HDInsight](hdinsight-hadoop-oms-log-analytics-tutorial.md).

## <a name="add-cluster-specific-management-solutions"></a>Adicione as soluções de gestão de cluster específicos

Nesta secção, é possível adicionar uma solução de gestão de clusters do HBase para uma área de trabalho existente do OMS.

1. Clique de um cluster de HDInsigt no portal do Azure, abra **monitorização**e, em seguida, clique em **abra OMS Dashboard**.

    ![Dashboard OMS abra](./media/hdinsight-hadoop-oms-log-analytics-management-solutions/hdinsight-log-analytics-open-oms-dashboard.png "dashboard abrir OMS")

1. No dashboard do OMS, clique em **soluções galeria** ou **estruturador de vistas** ícone no painel esquerdo.

    ![Adicionar a solução de gestão no OMS](./media/hdinsight-hadoop-oms-log-analytics-management-solutions/hdinsight-add-management-solution-oms-portal.png "Adicionar solução de gestão no OMS")

2. Na Galeria de soluções, clique das seguintes mosaicos:

    - Hadoop do HDInsight monitorização
    - HBase do HDInsight monitorização (pré-visualização)
    - HDInsight Kafka monitorização
    - Storm HDInsight monitorização
    - Spark do HDInsight monitorização

3. No ecrã seguinte, clique em **adicionar**.  A seguinte captura de ecrã mostra o botão Adicionar para a monitorização de HBase.

     ![Adicionar a solução de gestão do HBase](./media/hdinsight-hadoop-oms-log-analytics-management-solutions/add-hbase-management-solution.png "adicionar HBase solução de gestão")

4. Deverá ver um mosaico no dashboard do OMS para a solução de gestão do HBase. Se o cluster associado OMS (como parte dos pré-requisitos para este artigo) é um cluster HBase, o mosaico mostra o nome do cluster e o número de nós no cluster.

    ![A solução de gestão de HBase adicionada](./media/hdinsight-hadoop-oms-log-analytics-management-solutions/added-hbase-management-solution.png "solução de gestão de HBase adicionada")

## <a name="next-steps"></a>Passos seguintes

* [Análise de registos do Azure de consulta para monitorizar clusters do HDInsight](hdinsight-hadoop-oms-log-analytics-use-queries.md)

## <a name="see-also"></a>Consultar também

* [Trabalhar com a análise de registos do OMS](https://blogs.msdn.microsoft.com/wei_out_there_with_system_center/2016/07/03/oms-log-analytics-create-tiles-drill-ins-and-dashboards-with-the-view-designer/)
* [Criar regras de alertas na análise de registos](../log-analytics/log-analytics-alerts-creating.md)
