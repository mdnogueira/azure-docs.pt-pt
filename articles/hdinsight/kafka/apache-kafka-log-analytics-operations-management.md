---
title: "Análise de registo para Apache Kafka - Azure HDInsight | Microsoft Docs"
description: Saiba como utilizar o Operations Management Suite para analisar os registos do cluster do Apache Kafka no Azure HDInsight.
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: 
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 11/07/2017
ms.author: larryfr
ms.openlocfilehash: 440133da32f4565aa247b273984b3affe18379ec
ms.sourcegitcommit: 9a61faf3463003375a53279e3adce241b5700879
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/15/2017
---
# <a name="analyze-logs-for-apache-kafka-on-hdinsight"></a>Analisar os registos para Apache Kafka no HDInsight

Saiba como utilizar o Microsoft Operations Management Suite para analisar os registos gerados por Apache Kafka no HDInsight.

## <a name="enable-oms-for-kafka"></a>Ativar OMS para Kafka

Os passos para ativar a análise de registos para o HDInsight são os mesmos para todos os clusters do HDInsight. Utilize as seguintes hiperligações para compreender como criar e configurar os serviços necessários:

1. Crie uma área de trabalho de análise de registos. Para obter mais informações, consulte o [começar com uma área de trabalho de análise de registos](https://docs.microsoft.com/azure/log-analytics) documento.

2. Crie um Kafka num cluster do HDInsight. Para obter mais informações, consulte o [começar a utilizar o Apache Kafka no HDInsight](apache-kafka-get-started.md) documento.

3. Configure o cluster de Kafka para utilizar a análise de registos. Para obter mais informações, consulte o [análise de registos de utilização para monitorizar HDInsight](../hdinsight-hadoop-oms-log-analytics-tutorial.md) documento.

    > [!NOTE]
    > Também pode configurar o cluster para utilizar a análise de registos, utilizando o `Enable-AzureRmHDInsightOperationsManagementSuite` cmdlet. Este cmdlet requer as seguintes informações:
    >
    > * O nome de cluster do HDInsight.
    > * O ID da área de trabalho de análise de registos. Pode encontrar o ID da área de trabalho na área de trabalho OMS para a sua área de trabalho de análise do registo.
    > * A chave primária para a ligação do OMS. Para localizar a chave primária, selecione a instância de análise do registo e, em seguida, __Portal do OMS__. A partir do Portal do OMS, selecione __definições__, __origens ligadas__e, em seguida, __servidores Linux__.


> [!IMPORTANT]
> pode demorar cerca de 20 minutos antes dos dados estão disponíveis para análise de registos.

## <a name="query-logs"></a>Registos de consulta

1. Do [portal do Azure](https://portal.azure.com), selecione a sua área de trabalho de análise do registo.

2. Selecione __Iniciar pesquisa__. Aqui, pode procurar os dados recolhidos a partir de Kafka. Seguem-se alguns exemplos de procura:

    * Utilização do disco:`Type=Perf ObjectName="Logical Disk" (CounterName="Free Megabytes")  InstanceName="_Total" Computer='hn*-*' or Computer='wn*-*' | measure avg(CounterValue) by   Computer interval 1HOUR`
    * Utilização da CPU:`Type:Perf CounterName="% Processor Time" InstanceName="_Total" Computer='hn*-*' or Computer='wn*-*' | measure avg(CounterValue) by Computer interval 1HOUR`
    * Receber mensagens em fila por segundo:`Type=metrics_kafka_CL ClusterName_s="your_kafka_cluster_name" InstanceName_s="kafka-BrokerTopicMetrics-MessagesInPerSec-Count" | measure avg(kafka_BrokerTopicMetrics_MessagesInPerSec_Count_value_d) by HostName_s interval 1HOUR`
    * Bytes recebidos por segundo:`Type=metrics_kafka_CL HostName_s="wn0-kafka" InstanceName_s="kafka-BrokerTopicMetrics-BytesInPerSec-Count" | measure avg(kafka_BrokerTopicMetrics_BytesInPerSec_Count_value_d) interval 1HOUR`
    * Bytes enviados por segundo:`Type=metrics_kafka_CL ClusterName_s="your_kafka_cluster_name" InstanceName_s="kafka-BrokerTopicMetrics-BytesOutPerSec-Count" |  measure avg(kafka_BrokerTopicMetrics_BytesOutPerSec_Count_value_d) interval 1HOUR`

    > [!IMPORTANT]
    > Substitua os valores de consulta com as informações específicas do cluster. Por exemplo, `ClusterName_s` tem de ser definida para o nome do cluster. `HostName_s`tem de ser definida para o nome de domínio de um nó de trabalho no cluster.

    Também pode introduzir `*` para procurar todos os tipos com sessão iniciados. Atualmente os seguintes registos estão disponíveis para consultas:

    | Tipo de registo | Descrição |
    | ---- | ---- |
    | registo\_kafkaserver\_CL | Kafka mediador server.log |
    | registo\_kafkacontroller\_CL | Kafka mediador controller.log |
    | métricas\_kafka\_CL | Métricas de Kafka JMX |

    ![Imagem da pesquisa de utilização da CPU](./media/apache-kafka-log-analytics-operations-management/kafka-cpu-usage.png)
 
 ## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre a análise de registos, consulte o [começar com uma área de trabalho de análise de registos](../../log-analytics/log-analytics-get-started.md) documento.

Para obter mais informações sobre como trabalhar com Kafka, consulte os seguintes documentos:

 * [Espelho Kafka entre clusters do HDInsight](apache-kafka-mirroring.md)
 * [Aumentar a escalabilidade do Kafka no HDInsight](apache-kafka-scalability.md)
 * [Utilizar o Spark transmissão em fluxo (DStreams) com Kafka](../hdinsight-apache-spark-with-kafka.md)
 * [Utilizar o Spark estruturado de transmissão em fluxo com Kafka](../hdinsight-apache-kafka-spark-structured-streaming.md)