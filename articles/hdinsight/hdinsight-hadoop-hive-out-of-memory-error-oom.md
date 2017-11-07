---
title: "Corrigir um ramo de registo fora de erro de memória no Azure HDInsight | Microsoft Docs"
description: "Corrigir um ramo de registo fora de erro de memória no HDInsight. O cenário de cliente é uma consulta em várias tabelas grandes."
keywords: "sem definições de ramo de registo de erros, OOM, de memória"
services: hdinsight
documentationcenter: 
author: mumian
manager: jhubbard
editor: cgronlun
ms.assetid: 7bce3dff-9825-4fa0-a568-c52a9f7d1dad
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 11/06/2017
ms.author: jgao
ms.openlocfilehash: 3149147a95ac6e12b8866fbd03dd159de97c43cb
ms.sourcegitcommit: 295ec94e3332d3e0a8704c1b848913672f7467c8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/06/2017
---
# <a name="fix-a-hive-out-of-memory-error-in-azure-hdinsight"></a>Corrigir um ramo de registo fora de erro de memória no Azure HDInsight

Saber como corrigir um ramo de registo fora de erro de memória ao processar tabelas grandes ao configurar as definições da memória do Hive.

## <a name="run-hive-query-against-large-tables"></a>Executar a consulta do Hive contra tabelas grandes

Um cliente executou uma consulta do Hive:

    SELECT
        COUNT (T1.COLUMN1) as DisplayColumn1,
        …
        …
        ….
    FROM
        TABLE1 T1,
        TABLE2 T2,
        TABLE3 T3,
        TABLE5 T4,
        TABLE6 T5,
        TABLE7 T6
    where (T1.KEY1 = T2.KEY1….
        …
        …

Alguns nuances desta consulta:

* T1 é um alias a uma tabela grande, TABLE1, que tem muitos tipos de coluna de cadeia.
* Outras tabelas são não que grande mas ter demasiadas colunas.
* Todas as tabelas são associar cada um dos outros, em alguns casos com várias colunas na tabela1 e outras pessoas.

A consulta do Hive demorou 26 minutos a concluir num nó de cluster do A3 HDInsight 24. O cliente reparado as mensagens de aviso seguinte:

    Warning: Map Join MAPJOIN[428][bigTable=?] in task 'Stage-21:MAPRED' is a cross product
    Warning: Shuffle Join JOIN[8][tables = [t1933775, t1932766]] in Stage 'Stage-4:MAPRED' is a cross product

Utilizando o motor de execução Tez. Da mesma consulta foi executada durante 15 minutos e, em seguida, apresentou o seguinte erro:

    Status: Failed
    Vertex failed, vertexName=Map 5, vertexId=vertex_1443634917922_0008_1_05, diagnostics=[Task failed, taskId=task_1443634917922_0008_1_05_000006, diagnostics=[TaskAttempt 0 failed, info=[Error: Failure while running task:java.lang.RuntimeException: java.lang.OutOfMemoryError: Java heap space
        at
    org.apache.hadoop.hive.ql.exec.tez.TezProcessor.initializeAndRunProcessor(TezProcessor.java:172)
        at org.apache.hadoop.hive.ql.exec.tez.TezProcessor.run(TezProcessor.java:138)
        at
    org.apache.tez.runtime.LogicalIOProcessorRuntimeTask.run(LogicalIOProcessorRuntimeTask.java:324)
        at
    org.apache.tez.runtime.task.TezTaskRunner$TaskRunnerCallable$1.run(TezTaskRunner.java:176)
        at
    org.apache.tez.runtime.task.TezTaskRunner$TaskRunnerCallable$1.run(TezTaskRunner.java:168)
        at java.security.AccessController.doPrivileged(Native Method)
        at javax.security.auth.Subject.doAs(Subject.java:415)
        at org.apache.hadoop.security.UserGroupInformation.doAs(UserGroupInformation.java:1628)
        at
    org.apache.tez.runtime.task.TezTaskRunner$TaskRunnerCallable.call(TezTaskRunner.java:168)
        at
    org.apache.tez.runtime.task.TezTaskRunner$TaskRunnerCallable.call(TezTaskRunner.java:163)
        at java.util.concurrent.FutureTask.run(FutureTask.java:262)
        at java.util.concurrent.ThreadPoolExecutor.runWorker(ThreadPoolExecutor.java:1145)
        at java.util.concurrent.ThreadPoolExecutor$Worker.run(ThreadPoolExecutor.java:615)
        at java.lang.Thread.run(Thread.java:745)
    Caused by: java.lang.OutOfMemoryError: Java heap space

O erro permanece quando utilizar uma máquina virtual maior (por exemplo, D12).


## <a name="debug-the-out-of-memory-error"></a>Depurar o fora de erro de memória

Encontrar nosso suporte e as equipas de engenharia em conjunto um dos problemas causar a saída de erro de memória foi um [conhecido problema descrito na JIRA Apache](https://issues.apache.org/jira/browse/HIVE-8306):

    When hive.auto.convert.join.noconditionaltask = true we check noconditionaltask.size and if the sum  of tables sizes in the map join is less than noconditionaltask.size the plan would generate a Map join, the issue with this is that the calculation doesnt take into account the overhead introduced by different HashTable implementation as results if the sum of input sizes is smaller than the noconditionaltask size by a small margin queries will hit OOM.

O **hive.auto.convert.join.noconditionaltask** no ramo de registo-site.xml ficheiro foi definido como **verdadeiro**:

    <property>
        <name>hive.auto.convert.join.noconditionaltask</name>
        <value>true</value>
        <description>
              Whether Hive enables the optimization about converting common join into mapjoin based on the input file size.
              If this parameter is on, and the sum of size for n-1 of the tables/partitions for a n-way join is smaller than the
              specified size, the join is directly converted to a mapjoin (there is no conditional task).
        </description>
      </property>

É provável que a associação de mapa tiver sido a causa do espaço de área dinâmica para dados de Java nosso erro de memória. Conforme explicado na mensagem de blogue [as definições da memória Yarn de Hadoop no HDInsight](http://blogs.msdn.com/b/shanyu/archive/2014/07/31/hadoop-yarn-memory-settings-in-hdinsigh.aspx), quando Tez motor de execução é utilizada a pilha de espaço utilizado, na verdade, pertence ao contentor Tez. Ver a imagem seguinte, que descreve a memória de contentor Tez.

![Diagrama de memória de contentor Tez: fora do erro de memória de ramo de registo](./media/hdinsight-hadoop-hive-out-of-memory-error-oom/hive-out-of-memory-error-oom-tez-container-memory.png)

Como a mensagem de blogue sugere, as seguintes definições de dois memória definem a memória do contentor para a área dinâmica para dados: **hive.tez.container.size** e **hive.tez.java.opts**. Da nossa experiência fora de exceção de memória não significa que o tamanho do contentor é demasiado pequeno. Significa que o tamanho da pilha de Java (hive.tez.java.opts) é demasiado pequeno. Modo sempre que for apresentada a memória esgotada, pode tentar aumentar **hive.tez.java.opts**. Se for necessário poderá ter de aumentar **hive.tez.container.size**. O **java.opts** definição deve ser cerca de 80% da **container.size**.

> [!NOTE]
> A definição **hive.tez.java.opts** tem de ser sempre menor **hive.tez.container.size**.
> 
> 

Como uma máquina D12 tem 28GB de memória, decidimos utilizar um tamanho de contentor de 10GB (10240MB) e atribuir 80% para java.opts:

    SET hive.tez.container.size=10240
    SET hive.tez.java.opts=-Xmx8192m

Com as novas definições, a consulta executou com êxito em 10 minutos.

## <a name="next-steps"></a>Passos seguintes

Obter um erro OOM não significam necessariamente que o tamanho do contentor é demasiado pequeno. Em vez disso, deve configurar as definições de memória para que o tamanho da área dinâmica para dados é aumentado e é, pelo menos, 80% do tamanho de memória do contentor. Para otimizar as consultas do Hive, consulte [consultas otimizar Hive do Hadoop no HDInsight](hdinsight-hadoop-optimize-hive-query.md).