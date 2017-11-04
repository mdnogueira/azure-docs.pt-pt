---
title: Utilizar um Shell interativas do Spark no HDInsight do Azure | Microsoft Docs
description: "Uma Shell interativas do Spark fornece um processo de impressão leitura executar para executar o Spark comandos um cada vez e ver os resultados."
services: hdinsight
documentationcenter: 
tags: azure-portal
author: maxluk
manager: jhubbard
editor: cgronlun
ms.assetid: 
ms.service: hdinsight
ms.custom: hdinsightactive
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/01/2017
ms.author: maxluk
ms.openlocfilehash: 6a88a4e8f6e562a23ad1b7f6152f7fc1df4a1992
ms.sourcegitcommit: f8437edf5de144b40aed00af5c52a20e35d10ba1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/03/2017
---
# <a name="run-spark-from-the-spark-shell"></a>Executar Spark a partir da Shell do Spark

Uma Shell interativas do Spark disponibiliza um ambiente de (impressão leitura executar loop) de REPL para executar o Spark comandos um cada vez e ver os resultados. Este processo é útil para desenvolvimento e depuração. Spark fornece uma shell para cada um dos respetivos idiomas suportados: Scala, Python e R.

## <a name="get-to-a-spark-shell-with-ssh"></a>Obter uma shell de Spark com SSH

Aceder a uma Shell do Spark no HDInsight ao ligar ao nó principal do principal do cluster utilizando o SSH:

     ssh <sshusername>@<clustername>-ssh.azurehdinsight.net

Pode obter o comando SSH completado para o cluster do portal do Azure:

1. Inicie sessão no [Portal do Azure](https://portal.azure.com).
2. Navegue para o painel de cluster do HDInsight Spark.
3. Selecione Secure Shell (SSH).

    ![Painel do HDInsight no portal do Azure](./media/apache-spark-shell/hdinsight-spark-blade.png)

4. Copie o comando SSH apresentado e executá-lo no seu terminal.

    ![SSH do HDInsight painel no portal do Azure](./media/apache-spark-shell/hdinsight-spark-ssh-blade.png)

Para obter mais informações sobre como utilizar o SSH para ligar para o HDInsight, consulte [utilizar o SSH com o HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

## <a name="run-a-spark-shell"></a>Executar uma Shell do Spark

O Spark fornece shells para Scala (shell do spark), o Python (pyspark) e o R (sparkR). Na sua sessão SSH no nó principal do cluster do HDInsight, introduza um dos seguintes comandos:

    ./bin/spark-shell
    ./bin/pyspark
    ./bin/sparkR

Agora pode introduzir os comandos do Spark no idioma adequado.

## <a name="sparksession-and-sparkcontext-instances"></a>Instâncias de SparkSession e SparkContext

Por predefinição quando executa a Shell do Spark, instâncias de SparkSession e SparkContext são automaticamente com instâncias criadas por si.

Para aceder a instância de SparkSession, introduza `spark`. Para aceder a instância de SparkContext, introduza `sc`.

## <a name="important-shell-parameters"></a>Parâmetros de shell importantes

O comando da Shell do Spark (`spark-shell`, `pyspark`, ou `sparkR`) suporta vários parâmetros da linha de comandos. Para ver uma lista completa de parâmetros, inicie a Shell do Spark com o comutador `--help`. Tenha em atenção que alguns destes parâmetros podem apenas se aplicam a `spark-submit`, que encapsula num wrapper do Shell do Spark.

| comutador | descrição | Exemplo |
| --- | --- | --- |
| -mestre MASTER_URL | Especifica o URL principal. No HDInsight, este valor é sempre `yarn`. | `--master yarn`|
| -jars JAR_LIST | Lista separada por vírgulas de v7 local para incluir na classpaths de controlador e o executor. No HDInsight, esta lista é composta por caminhos para o sistema de ficheiros predefinido no Storage do Azure ou de Data Lake Store. | `--jars /path/to/examples.jar` |
| -pacotes MAVEN_COORDS | Lista separada por vírgulas de coordenadas maven de v7 para incluir na classpaths de controlador e o executor. Procura o repositório de local maven, em seguida, maven central, em seguida, quaisquer repositórios remotos adicionais especificados com `--repositories`. O formato para as coordenadas é *groupId*:*artifactId*:*versão*. | `--packages "com.microsoft.azure:azure-eventhubs:0.14.0"`|
| LISTA de ficheiros – py | Para Python apenas, uma lista separada por vírgulas dos ficheiros. zip, .egg ou. PY para colocar no PYTHONPATH. | `--pyfiles "samples.py"` |

## <a name="next-steps"></a>Passos seguintes

- Consulte [introdução para o Spark no Azure HDInsight](apache-spark-overview.md) para uma descrição geral.
- Consulte [criar um cluster do Apache Spark no Azure HDInsight](apache-spark-jupyter-spark-sql.md) para trabalhar com clusters do Spark e SparkSQL.
- Consulte [Novidades Spark estruturados de transmissão em fluxo?](apache-spark-streaming-overview.md) escrever as aplicações que processam os dados de transmissão em fluxo com o Spark.

