---
title: Resolver problemas do ramo de registo utilizando o Azure HDInsight | Microsoft Docs
description: "Obtenha respostas a questões recorrentes sobre como trabalhar com Apache Hive e o Azure HDInsight."
keywords: "FAQ do HDInsight, Hive, do Azure, manual, perguntas comuns de resolução de problemas"
services: Azure HDInsight
documentationcenter: na
author: dharmeshkakadia
manager: 
editor: 
ms.assetid: 15B8D0F3-F2D3-4746-BDCB-C72944AA9252
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/2/2017
ms.author: dharmeshkakadia
ms.openlocfilehash: 398cc7cd0fa815f12ba5b503a1328688e1159eee
ms.sourcegitcommit: 3df3fcec9ac9e56a3f5282f6c65e5a9bc1b5ba22
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2017
---
# <a name="troubleshoot-hive-by-using-azure-hdinsight"></a>Resolver problemas do ramo de registo utilizando o Azure HDInsight

Saiba mais sobre as questões superiores e as resoluções ao trabalhar com Apache Hive payloads no Apache Ambari.


## <a name="how-do-i-export-a-hive-metastore-and-import-it-on-another-cluster"></a>Como exportar um metastore do Hive e importá-la para outro cluster?


### <a name="resolution-steps"></a>Passos de resolução

1. Liga ao cluster do HDInsight utilizando um cliente Secure Shell (SSH). Para obter mais informações, consulte [leitura adicional](#additional-reading-end).

2. Execute o seguinte comando no cluster do HDInsight a partir do qual pretende exportar o metastore:

    ```apache
    for d in `hive -e "show databases"`; do echo "create database $d; use $d;" >> alltables.sql ; for t in `hive --database $d -e "show tables"` ; do ddl=`hive --database $d -e "show create table $t"`; echo "$ddl ;" >> alltables.sql ; echo "$ddl" | grep -q "PARTITIONED\s*BY" && echo "MSCK REPAIR TABLE $t ;" >> alltables.sql ; done; done
    ```

  Este comando gera um ficheiro denominado allatables.sql.

3. Copie o ficheiro alltables.sql para o novo cluster do HDInsight e, em seguida, execute o seguinte comando:

  ```apache
  hive -f alltables.sql
  ```

O código nos passos de resolução parte do princípio de que os caminhos de dados no novo cluster são os mesmos que os caminhos de dados no cluster antigo. Se os caminhos de dados forem diferentes, pode editar o ficheiro de alltables.sql gerado para refletir as alterações manualmente.

### <a name="additional-reading"></a>Leitura adicional

- [Ligar a um cluster do HDInsight através de SSH](hdinsight-hadoop-linux-use-ssh-unix.md)


## <a name="how-do-i-locate-hive-logs-on-a-cluster"></a>Como se localizar registos do Hive num cluster?

### <a name="resolution-steps"></a>Passos de resolução

1. Liga ao cluster do HDInsight através de SSH. Para obter mais informações, consulte **leitura adicional**.

2. Para ver registos de cliente do ramo de registo, utilize o seguinte comando:

  ```apache
  /tmp/<username>/hive.log 
  ```

3. Para ver registos de metastore do Hive, utilize o seguinte comando:

  ```apache
  /var/log/hive/hivemetastore.log 
  ```

4. Para ver registos Hiveserver, utilize o seguinte comando:

  ```apache
  /var/log/hive/hiveserver2.log 
  ```

### <a name="additional-reading"></a>Leitura adicional

- [Ligar a um cluster do HDInsight através de SSH](hdinsight-hadoop-linux-use-ssh-unix.md)


## <a name="how-do-i-launch-the-hive-shell-with-specific-configurations-on-a-cluster"></a>Como iniciar a shell do Hive com as configurações específicas num cluster?

### <a name="resolution-steps"></a>Passos de resolução

1. Especifique um par chave-valor de configuração quando iniciar a shell do Hive. Para obter mais informações, consulte [leitura adicional](#additional-reading-end).

  ```apache
  hive -hiveconf a=b 
  ```

2. Para listar todas as configurações efetivas na shell do Hive, utilize o seguinte comando:

  ```apache
  hive> set;
  ```

  Por exemplo, utilize o seguinte comando para iniciar a shell do Hive com o registo de depuração ativados na consola:

  ```apache
  hive -hiveconf hive.root.logger=ALL,console 
  ```

### <a name="additional-reading"></a>Leitura adicional

- [Propriedades de configuração do ramo de registo](https://cwiki.apache.org/confluence/display/Hive/Configuration+Properties)


## <a name="how-do-i-analyze-tez-dag-data-on-a-cluster-critical-path"></a>Como analisar os dados de Tez DAG num caminho críticas para o cluster?


### <a name="resolution-steps"></a>Passos de resolução
 
1. Para analisar um Apache Tez acíclicos direcionados (DAG) num gráfico de crítico para o cluster, estabeleça ligação ao cluster do HDInsight utilizando SSH. Para obter mais informações, consulte [leitura adicional](#additional-reading-end).

2. Numa linha de comandos, execute o seguinte comando:
   
  ```apache
  hadoop jar /usr/hdp/current/tez-client/tez-job-analyzer-*.jar CriticalPath --saveResults --dagId <DagId> --eventFileName <DagData.zip> 
  ```

3. Lista de outros analisadores que podem ser utilizadas para analisar Tez DAG, utilize o seguinte comando:

  ```apache
  hadoop jar /usr/hdp/current/tez-client/tez-job-analyzer-*.jar
  ```

  Tem de fornecer um programa de exemplo como o primeiro argumento.

  Os nomes dos programas válidos incluem:
    - **ContainerReuseAnalyzer**: imprimir detalhes de reutilização de contentor num DAG
    - **CriticalPath**: localizar o caminho de um DAG crítico
    - **LocalityAnalyzer**: imprimir detalhes localidade num DAG
    - **ShuffleTimeAnalyzer**: analisar os detalhes do tempo de shuffle num DAG
    - **SkewAnalyzer**: analisar os detalhes dissimetrias num DAG
    - **SlowNodeAnalyzer**: imprimir detalhes de nó num DAG
    - **SlowTaskIdentifier**: detalhes da tarefa lenta de impressão num DAG
    - **SlowestVertexAnalyzer**: imprimir mais lentos detalhes de vértice num DAG
    - **SpillAnalyzer**: detalhes de impressão transbordam num DAG
    - **TaskConcurrencyAnalyzer**: imprimir os detalhes de simultaneidade de tarefas num DAG
    - **VertexLevelCriticalPathAnalyzer**: localizar o caminho de crítico a nível de vértice num DAG


### <a name="additional-reading"></a>Leitura adicional

- [Ligar a um cluster do HDInsight através de SSH](hdinsight-hadoop-linux-use-ssh-unix.md)


## <a name="how-do-i-download-tez-dag-data-from-a-cluster"></a>Como transferir dados Tez DAG a partir de um cluster?


#### <a name="resolution-steps"></a>Passos de resolução

Existem duas formas para recolher os dados de Tez DAG:

- Na linha de comandos:
 
    Liga ao cluster do HDInsight através de SSH. Na linha de comandos, execute o seguinte comando:

  ```apache
  hadoop jar /usr/hdp/current/tez-client/tez-history-parser-*.jar org.apache.tez.history.ATSImportTool -downloadDir . -dagId <DagId> 
  ```

- Utilize a vista Ambari Tez:
   
  1. Aceda à Ambari. 
  2. Aceda à vista de Tez (sob o ícone de mosaicos no canto superior direito). 
  3. Selecione o DAG em que pretende visualizar.
  4. Selecione **transferir dados**.

### <a name="additional-reading-end"></a>Leitura adicional

[Ligar a um cluster do HDInsight através de SSH](hdinsight-hadoop-linux-use-ssh-unix.md)


### <a name="see-also"></a>Veja Também
[Resolver problemas utilizando o Azure HDInsight](hdinsight-troubleshoot-guide.md)




