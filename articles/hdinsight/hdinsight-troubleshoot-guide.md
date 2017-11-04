---
title: "O Azure HDInsight guias de resolução de problemas | Microsoft Docs"
description: "Resolver problemas de cargas de trabalho do Hadoop, utilizando o Azure HDInsight. Documentação de passo a passo mostra como utilizar o HDInsight para resolver problemas comuns com o Hive, Spark, YARN, HBase, HDFS e Storm."
services: hdinsight
author: arijitt
manager: arijitt
layout: LandingPage
ms.assetid: 
ms.service: hdinsight
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: landing - page
ms.date: 7/06/2017
ms.author: arijitt
ms.openlocfilehash: 1a8657549e9e36ac6f876da2fd20456ba690533a
ms.sourcegitcommit: f8437edf5de144b40aed00af5c52a20e35d10ba1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/03/2017
---
# <a name="troubleshoot-by-using-azure-hdinsight"></a>Resolver problemas utilizando o Azure HDInsight

| Carga de trabalho do Apache | Perguntas superiores |
|---|---|
|![HBase](./media/hdinsight-troubleshoot-guide/HBASE.png)<br>[Resolver problemas de HBase](hbase/apache-troubleshoot-hbase.md)|<br>[Como executar relatórios de comando hbck a com várias regiões não atribuídas](hbase/apache-troubleshoot-hbase.md#how-do-i-run-hbck-command-reports-with-multiple-unassigned-regions)<br><br>[Como corrigir problemas de limite de tempo quando utilizar comandos de hbck a existência de atribuições de região](hbase/apache-troubleshoot-hbase.md#how-do-i-fix-timeout-issues-with-hbck-commands-for-region-assignments)<br><br>[Como posso force-desativar modo de segurança do HDFS num cluster](hbase/apache-troubleshoot-hbase.md#how-do-i-force-disable-hdfs-safe-mode-in-a-cluster)<br><br>[Como corrija a conectividade de JDBC ou SQLLine problemas com o Apache Phoenix](hbase/apache-troubleshoot-hbase.md#how-do-i-fix-jdbc-or-sqlline-connectivity-issues-with-apache-phoenix)<br><br>[O que faz com que um servidor principal falhar ao iniciar](hbase/apache-troubleshoot-hbase.md#what-causes-a-master-server-to-fail-to-start)<br><br>[O que causa uma falha de reinício num servidor de região](hbase/apache-troubleshoot-hbase.md#what-causes-a-restart-failure-on-a-region-server)|
|![HDFS](./media/hdinsight-troubleshoot-guide/HDFS.png)<br>[Resolver problemas do HDFS](hdinsight-troubleshoot-hdfs.md)|<br>[Como aceder a um HDFS local de dentro de um cluster](hdinsight-troubleshoot-hdfs.md#how-do-i-access-local-hdfs-from-inside-a-cluster)<br><br>[Como posso force-desativar modo de segurança do HDFS num cluster](hdinsight-troubleshoot-hdfs.md#how-do-i-force-disable-hdfs-safe-mode-in-a-cluster)|
|![Hive](./media/hdinsight-troubleshoot-guide/HIVE.png)<br>[Resolver problemas de HBase](hdinsight-troubleshoot-hive.md)|<br>[Como exportar um metastore do Hive e importá-la para outro cluster](hdinsight-troubleshoot-hive.md#how-do-i-export-a-hive-metastore-and-import-it-on-another-cluster)<br><br>[Como se localizar registos do Hive num cluster](hdinsight-troubleshoot-hive.md#how-do-i-locate-hive-logs-on-a-cluster)<br><br>[Como iniciar a shell do Hive com as configurações específicas num cluster](hdinsight-troubleshoot-hive.md#how-do-i-launch-the-hive-shell-with-specific-configurations-on-a-cluster)<br><br>[Como analisar os dados de Tez DAG num caminho críticas para o cluster](hdinsight-troubleshoot-hive.md#how-do-i-analyze-tez-dag-data-on-a-cluster-critical-path)<br><br>[Como transferir dados Tez DAG a partir de um cluster](hdinsight-troubleshoot-hive.md#how-do-i-download-tez-dag-data-from-a-cluster)|
|![Spark](./media/hdinsight-troubleshoot-guide/SPARK.png)<br>[Resolver problemas do Spark](hdinsight-troubleshoot-SPARK.md)|<br>[Como configurar a uma aplicação de Spark utilizando Ambari em clusters](spark/apache-troubleshoot-spark.md#how-do-i-configure-a-spark-application-by-using-ambari-on-clusters)<br><br>[Como configurar a uma aplicação de Spark através da utilização de um bloco de notas do Jupyter em clusters](spark/apache-troubleshoot-spark.md#how-do-i-configure-a-spark-application-by-using-a-jupyter-notebook-on-clusters)<br><br>[Como configurar uma aplicação de Spark a utilizando o Livy em clusters](spark/apache-troubleshoot-spark.md#how-do-i-configure-a-spark-application-by-using-livy-on-clusters)<br><br>[Como configurar a um aplicação através da utilização do spark-submeter o Spark nos clusters](spark/apache-troubleshoot-spark.md#how-do-i-configure-a-spark-application-by-using-spark-submit-on-clusters)<br><br>[O que faz com que um Spark exceção de aplicação de OutOfMemoryError](spark/apache-troubleshoot-spark.md#what-causes-a-spark-application-outofmemoryerror-exception)|
|![Storm](./media/hdinsight-troubleshoot-guide/STORM.png)<br>[Resolver problemas de Storm](hdinsight-troubleshoot-STORM.md)|<br>[Como acedo a IU do Storm num cluster](storm/apache-troubleshoot-storm.md#how-do-i-access-the-storm-ui-on-a-cluster)<br><br>[Como transferir as informações de ponto de verificação do Storm event hub spout da topologia de um para outro](storm/apache-troubleshoot-storm.md#how-do-i-transfer-storm-event-hub-spout-checkpoint-information-from-one-topology-to-another)<br><br>[Como localizar o binários de Storm num cluster](storm/apache-troubleshoot-storm.md#how-do-i-locate-storm-binaries-on-a-cluster)<br><br>[Como posso determinar a topologia de implementação de um cluster do Storm](storm/apache-troubleshoot-storm.md#how-do-i-determine-the-deployment-topology-of-a-storm-cluster)<br><br>[Como localizar o Storm event hub spout os binários para o desenvolvimento](storm/apache-troubleshoot-storm.md#how-do-i-locate-storm-event-hub-spout-binaries-for-development)<br><br>[Como localizar o ficheiros de configuração de Storm Log4J em clusters](storm/apache-troubleshoot-storm.md#how-do-i-locate-storm-log4j-configuration-files-on-clusters)|
|![YARN](./media/hdinsight-troubleshoot-guide/YARN.png)<br>[YARN de resolução de problemas](hdinsight-troubleshoot-YARN.md)|<br>[Como posso criar uma nova fila YARN num cluster](hdinsight-troubleshoot-yarn.md#how-do-i-create-a-new-yarn-queue-on-a-cluster)<br><br>[Como transferir registos YARN a partir de um cluster](hdinsight-troubleshoot-yarn.md#how-do-i-download-yarn-logs-from-a-cluster)|

## <a name="hdinsight-troubleshooting-resources"></a>Recursos de resolução de problemas do HDInsight

| Para obter informações sobre | Consulte estes artigos |
| --- | --- |
| HDInsight no Linux e Otimização | - [Informações sobre como utilizar o HDInsight no Linux](hdinsight-hadoop-linux-information.md)<br>- [Memória do Hadoop e a resolução de problemas de desempenho](hdinsight-hadoop-stack-trace-error-messages.md)<br>- [Desempenho das consultas do Hive](https://blogs.msdn.microsoft.com/bigdatasupport/2015/08/13/troubleshooting-hive-query-performance-in-hdinsight-hadoop-cluster/) |
| Os registos e informações de estado | - [Os registos de aplicações do Hadoop YARN de acesso no Linux](hdinsight-hadoop-access-yarn-app-logs-linux.md)<br>- [Ativar capturas de área dinâmica para dados de serviços do Hadoop no Linux](hdinsight-hadoop-collect-debug-heap-dump-linux.md)<br>- [Analisar os registos do HDInsight](hdinsight-debug-jobs.md)|
| Erros | - [Compreender e resolver erros de WebHCat](hdinsight-hadoop-templeton-webhcat-debug-errors.md)<br>- [Definições para corrigir o erro de OutofMemory de ramo de registo](hdinsight-hadoop-hive-out-of-memory-error-oom.md) |
| Ferramentas | - [Utilizar vistas do Ambari para depurar tarefas Tez](hdinsight-debug-ambari-tez-view.md)<br>- [Otimizar as consultas do Hive](hdinsight-hadoop-optimize-hive-query.md) |
