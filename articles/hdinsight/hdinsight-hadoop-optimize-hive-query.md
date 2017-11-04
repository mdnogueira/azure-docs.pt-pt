---
title: Otimizar as consultas do Hive no Azure HDInsight | Microsoft Docs
description: Saiba como otimizar as consultas do Hive do Hadoop no HDInsight.
services: hdinsight
documentationcenter: 
author: mumian
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: d6174c08-06aa-42ac-8e9b-8b8718d9978e
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 11/03/2017
ms.author: jgao
ms.openlocfilehash: 4bd7f5b584030f9c1554b56895493837d2eac357
ms.sourcegitcommit: 3df3fcec9ac9e56a3f5282f6c65e5a9bc1b5ba22
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2017
---
# <a name="optimize-hive-queries-in-azure-hdinsight"></a>Otimizar as consultas do Hive no Azure HDInsight

Por predefinição, os clusters do Hadoop não estão otimizados para desempenho. Este artigo abrange alguns métodos de otimização desempenho mais comuns do Hive pode aplicar a suas consultas.

## <a name="scale-out-worker-nodes"></a>Aumentar horizontalmente nós de trabalho

O aumento do número de nós de trabalho num cluster pode tirar partido mais mappers e reducers para ser executado em paralelo. Existem duas formas pode aumentar de escalamento horizontal no HDInsight:

* O do momento de aprovisionamento, pode especificar o número de nós de trabalho utilizando o portal do Azure, o Azure PowerShell ou a interface de linha de comandos de várias plataformas.  Para obter mais informações, veja [Create HDInsight clusters](hdinsight-hadoop-provision-linux-clusters.md) (Criar clusters do HDInsight). A seguinte captura de ecrã mostra o técnico de configuração do nó no portal do Azure:
  
    ![scaleout_1][image-hdi-optimize-hive-scaleout_1]
* No momento de execução, também pode ampliar um cluster sem recriar uma:

    ![scaleout_1][image-hdi-optimize-hive-scaleout_2]

Para obter mais informações sobre as diferentes máquinas virtuais suportadas pelo HDInsight, consulte [preços do HDInsight](https://azure.microsoft.com/pricing/details/hdinsight/).

## <a name="enable-tez"></a>Ativar o Tez

[O Apache Tez](http://hortonworks.com/hadoop/tez/) é um motor de execução alternativo para o motor de MapReduce:

![tez_1][image-hdi-optimize-hive-tez_1]

Tez é mais rápida porque:

* **Executar direcionado Acíclico gráfico (DAG) como uma única tarefa no motor de MapReduce**. O DAG necessita de cada conjunto de mappers ser seguido de um conjunto de reducers. Isto faz com que várias tarefas de MapReduce ser puserem para cada consulta do Hive. Tez tem essa restrição e pode processar DAG complexo como uma tarefa, por conseguinte, minimizando a sobrecarga de arranque da tarefa.
* **Evita escritas desnecessárias**. Devido a várias tarefas que está a ser puserem para a mesma consulta do Hive no motor de MapReduce, o resultado de cada tarefa é escrito HDFS para dados intermédios. Uma vez que minimiza o número de tarefas para cada consulta do Hive no Tez é capaz de evitar escrita desnecessária.
* **Minimiza os atrasos de arranque**. Tez melhor é capaz de forma a minimizar o atraso de arranque ao reduzir o número de mappers tem de iniciar e também melhorar otimização ao longo.
* **Reutiliza contentores**. Sempre que possível Tez é possa reutilizar contentores para se certificar de que a latência devido a iniciar contentores é reduzida.
* **Técnicas de otimização contínua**. Tradicionalmente otimização foi efetuada durante a fase de compilação. No entanto, estão disponíveis mais informações sobre as entradas que permitem de otimização melhor durante o tempo de execução. Tez utiliza técnicas de otimização contínua, que lhe permite otimizar ainda mais o plano para a fase de tempo de execução.

Para obter mais informações sobre estes conceitos, consulte [Apache TEZ](http://hortonworks.com/hadoop/tez/).

Pode efetuar qualquer consulta do Hive no Tez ativado por lhe o prefixo a consulta com a definição abaixo:

    set hive.execution.engine=tez;

Clusters do HDInsight baseado em Linux tem Tez ativado por predefinição.


## <a name="hive-partitioning"></a>Criação de partições de ramo de registo

A operação de e/s é o estrangulamento do desempenho principais para executar consultas do Hive. O desempenho pode ser melhorado se a quantidade de dados tem de ser de leitura pode ser reduzida. Por predefinição, consultas do Hive análise completos tabelas do Hive. Esta é uma grande para consultas como análises de tabela. No entanto para consultas que apenas necessitam de analisar uma pequena quantidade de dados (por exemplo, as consultas com filtragem), este comportamento cria desnecessário sobrecarga. A criação de partições de ramo de registo permite consultas do Hive aceder apenas a quantidade necessária de dados em tabelas do Hive.

A criação de partições de ramo de registo é implementada por reorganizar os dados não processados numa nova diretórios com cada partição com o seu próprio diretório - em que a partição está definida pelo utilizador. O diagrama seguinte ilustra uma tabela do Hive de criação de partições pela coluna *ano*. É criado um diretório novo para todos os anos.

![Criação de partições][image-hdi-optimize-hive-partitioning_1]

Algumas considerações sobre a criação de partições:

* **Efetue não em partição** -criação de partições em colunas com apenas alguns valores pode causar alguns partições. Por exemplo, a criação de partições no sexo apenas cria duas partições para ser criado (male e female), deste modo, reduzir a latência apenas por um máximo de metade.
* **Efetue não através da partição** - na outro Alpine, criar uma partição numa coluna com um valor exclusivo (por exemplo, userid) faz com que várias partições. Ao longo da partição faz com que muito esforço no namenode cluster, tem de processar o número elevado de diretórios.
* **Evitar dados dissimetrias** -escolha sua chave de criação de partições de forma sensata para que todas as partições são o mesmo tamanho. Um exemplo é a criação de partições no *estado* pode fazer com que o número de registos em Califórnia ser quase 30 x que Vermont devido à diferença população.

Para criar uma tabela de partição, utilize o *particionada por* cláusula:

    CREATE TABLE lineitem_part
        (L_ORDERKEY INT, L_PARTKEY INT, L_SUPPKEY INT,L_LINENUMBER INT,
         L_QUANTITY DOUBLE, L_EXTENDEDPRICE DOUBLE, L_DISCOUNT DOUBLE,
         L_TAX DOUBLE, L_RETURNFLAG STRING, L_LINESTATUS STRING,
         L_SHIPDATE_PS STRING, L_COMMITDATE STRING, L_RECEIPTDATE            STRING, L_SHIPINSTRUCT STRING, L_SHIPMODE STRING,
         L_COMMENT STRING)
    PARTITIONED BY(L_SHIPDATE STRING)
    ROW FORMAT DELIMITED FIELDS TERMINATED BY '\t'
    STORED AS TEXTFILE;

Assim que a tabela particionada for criada, pode criar partições estático ou dinâmico de criação de partições.

* **Criação de partições estático** significa que tem dados fragmentados já nos diretórios adequados e pode pedir ao manualmente com base na localização do diretório de partições de ramo de registo. O fragmento de código seguinte é um exemplo.
  
        INSERT OVERWRITE TABLE lineitem_part
        PARTITION (L_SHIPDATE = ‘5/23/1996 12:00:00 AM’)
        SELECT * FROM lineitem 
        WHERE lineitem.L_SHIPDATE = ‘5/23/1996 12:00:00 AM’
  
        ALTER TABLE lineitem_part ADD PARTITION (L_SHIPDATE = ‘5/23/1996 12:00:00 AM’))
        LOCATION ‘wasb://sampledata@ignitedemo.blob.core.windows.net/partitions/5_23_1996/'
* **Criação dinâmica de partições** significa que pretende que o ramo de registo para criar partições automaticamente para si. Uma vez que já foi criado a tabela de partições da tabela de teste, tudo o que precisa de fazer é inserir dados para a tabela particionada:
  
        SET hive.exec.dynamic.partition = true;
        SET hive.exec.dynamic.partition.mode = nonstrict;
        INSERT INTO TABLE lineitem_part
        PARTITION (L_SHIPDATE)
        SELECT L_ORDERKEY as L_ORDERKEY, L_PARTKEY as L_PARTKEY , 
             L_SUPPKEY as L_SUPPKEY, L_LINENUMBER as L_LINENUMBER,
              L_QUANTITY as L_QUANTITY, L_EXTENDEDPRICE as L_EXTENDEDPRICE,
             L_DISCOUNT as L_DISCOUNT, L_TAX as L_TAX, L_RETURNFLAG as           L_RETURNFLAG, L_LINESTATUS as L_LINESTATUS, L_SHIPDATE as           L_SHIPDATE_PS, L_COMMITDATE as L_COMMITDATE, L_RECEIPTDATE as      L_RECEIPTDATE, L_SHIPINSTRUCT as L_SHIPINSTRUCT, L_SHIPMODE as      L_SHIPMODE, L_COMMENT as L_COMMENT, L_SHIPDATE as L_SHIPDATE FROM lineitem;

Para obter mais informações, consulte [tabelas Particionadas](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+DDL#LanguageManualDDL-PartitionedTables).

## <a name="use-the-orcfile-format"></a>Utilize o formato de ORCFile
Hive suporta formatos de ficheiro diferente. Por exemplo:

* **Texto**: Este é o formato de ficheiro predefinido e funciona com a maioria dos cenários
* **Avro**: funciona bem para cenários de interoperabilidade
* **ORC/Parquet**: mais adequada para desempenho

Formato ORC (otimizada linha Columnar) é uma forma altamente eficiente para armazenar dados do Hive. Em comparação com outros formatos, ORC tem as seguintes vantagens:

* suporte para tipos complexos, incluindo DateTime e tipos complexos e semiestruturados
* Se a compressão de 70%
* indexa a cada 10 000 linhas, o que permite que a ignorar as linhas
* uma redução significativa no tempo de execução de execução

Para ativar o formato ORC, tem primeiro de criar uma tabela com a cláusula *armazenados como ORC*:

    CREATE TABLE lineitem_orc_part
        (L_ORDERKEY INT, L_PARTKEY INT,L_SUPPKEY INT, L_LINENUMBER INT,
         L_QUANTITY DOUBLE, L_EXTENDEDPRICE DOUBLE, L_DISCOUNT DOUBLE,
         L_TAX DOUBLE, L_RETURNFLAG STRING, L_LINESTATUS STRING,
         L_SHIPDATE_PS STRING, L_COMMITDATE STRING, L_RECEIPTDATE STRING,
         L_SHIPINSTRUCT STRING, L_SHIPMODE STRING, L_COMMENT      STRING)
    PARTITIONED BY(L_SHIPDATE STRING)
    STORED AS ORC;

Em seguida, inserir dados para a tabela ORC da tabela de teste. Por exemplo:

    INSERT INTO TABLE lineitem_orc
    SELECT L_ORDERKEY as L_ORDERKEY, 
           L_PARTKEY as L_PARTKEY , 
           L_SUPPKEY as L_SUPPKEY,
           L_LINENUMBER as L_LINENUMBER,
            L_QUANTITY as L_QUANTITY, 
           L_EXTENDEDPRICE as L_EXTENDEDPRICE,
           L_DISCOUNT as L_DISCOUNT,
           L_TAX as L_TAX,
           L_RETURNFLAG as L_RETURNFLAG,
           L_LINESTATUS as L_LINESTATUS,
           L_SHIPDATE as L_SHIPDATE,
           L_COMMITDATE as L_COMMITDATE,
           L_RECEIPTDATE as L_RECEIPTDATE, 
           L_SHIPINSTRUCT as L_SHIPINSTRUCT,
           L_SHIPMODE as L_SHIPMODE,
           L_COMMENT as L_COMMENT
    FROM lineitem;

Pode ler mais sobre o formato ORC [aqui](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+ORC).

## <a name="vectorization"></a>Vectorization

Vectorization permite Hive processar um lote de linhas de 1024 em conjunto em vez de processamento de uma linha numa altura. Significa que operações simples terminadas mais rápida porque menos código interno tem de executar.

Para ativar vectorization prefixo da sua consulta do Hive com a definição seguinte:

    set hive.vectorized.execution.enabled = true;

Para obter mais informações, consulte [Vectorized a execução da consulta](https://cwiki.apache.org/confluence/display/Hive/Vectorized+Query+Execution).

## <a name="other-optimization-methods"></a>Outros métodos de otimização
Existem mais métodos de otimização que pode considerar a, por exemplo:

* **Ramo de registo bucketing:** uma técnica que permite ao cluster ou segmento grande define dos dados para otimizar o desempenho de consulta.
* **Associar otimização:** otimização de execução da consulta do Hive de planeamento melhorar a eficiência de associações e reduzir a necessidade de sugestões de utilizador. Para obter mais informações, consulte [associar otimização](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+JoinOptimization#LanguageManualJoinOptimization-JoinOptimization).
* **Aumentar Reducers**.

## <a name="next-steps"></a>Passos seguintes
Neste artigo, aprendeu a vários métodos de Otimização da consulta do Hive comuns. Para obter mais informações, consulte os artigos seguintes:

* [Utilizar o Apache Hive no HDInsight](hadoop/hdinsight-use-hive.md)
* [Analisar dados de atraso do voo utilizando o Hive no HDInsight](hdinsight-analyze-flight-delay-data.md)
* [Analisar dados do Twitter utilizando o Hive no HDInsight](hdinsight-analyze-twitter-data.md)
* [Analisar dados de sensores utilizando a consola de consulta do Hive do Hadoop no HDInsight](hadoop/apache-hive-analyze-sensor-data.md)
* [Utilizar o Hive com o HDInsight para analisar os registos de Web sites](hadoop/apache-hive-analyze-website-log.md)

[image-hdi-optimize-hive-scaleout_1]: ./media/hdinsight-hadoop-optimize-hive-query/scaleout_1.png
[image-hdi-optimize-hive-scaleout_2]: ./media/hdinsight-hadoop-optimize-hive-query/scaleout_2.png
[image-hdi-optimize-hive-tez_1]: ./media/hdinsight-hadoop-optimize-hive-query/tez_1.png
[image-hdi-optimize-hive-partitioning_1]: ./media/hdinsight-hadoop-optimize-hive-query/partitioning_1.png
