---
title: "Ligação MapReduce e o SSH com Hadoop no HDInsight - Azure | Microsoft Docs"
description: Saiba como utilizar o SSH para executar tarefas de MapReduce com o Hadoop no HDInsight.
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 844678ba-1e1f-4fda-b9ef-34df4035d547
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 10/23/2017
ms.author: larryfr
ms.openlocfilehash: 1ce635486c00bb4d2e5589d4c35ef6dff593b069
ms.sourcegitcommit: f8437edf5de144b40aed00af5c52a20e35d10ba1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/03/2017
---
# <a name="use-mapreduce-with-hadoop-on-hdinsight-with-ssh"></a>Utilizar o MapReduce com o Hadoop no HDInsight com SSH

[!INCLUDE [mapreduce-selector](../../../includes/hdinsight-selector-use-mapreduce.md)]

Saiba como submeter as tarefas do MapReduce de uma ligação Secure Shell (SSH) para o HDInsight.

> [!NOTE]
> Se já estiver familiarizado com a utilização de servidores de Hadoop baseado em Linux, mas estiver familiarizado com o HDInsight, consulte [sugestões do HDInsight baseado em Linux](../hdinsight-hadoop-linux-information.md).

## <a id="prereq"></a>Pré-requisitos

* Um cluster do HDInsight baseado em Linux (Hadoop no HDInsight)

  > [!IMPORTANT]
  > O Linux é o único sistema operativo utilizado na versão 3.4 ou superior do HDInsight. Para obter mais informações, veja [HDInsight retirement on Windows](../hdinsight-component-versioning.md#hdinsight-windows-retirement) (Desativação do HDInsight no Windows).

* Um cliente SSH. Para obter mais informações, consulte [utilizar o SSH com o HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md)

## <a id="ssh"></a>Ligar com SSH

Ligar ao cluster através de SSH. Por exemplo, o seguinte comando liga a um cluster com o nome **myhdinsight**:

```bash
ssh admin@myhdinsight-ssh.azurehdinsight.net
```

**Se utilizar uma chave de certificado de autenticação SSH**, terá de especificar a localização da chave privada no seu sistema de cliente, por exemplo:

```bash
ssh -i ~/mykey.key admin@myhdinsight-ssh.azurehdinsight.net
```

**Se utilizar uma palavra-passe de autenticação SSH**, tem de fornecer a palavra-passe quando lhe for pedido.

Para obter mais informações sobre como utilizar o SSH com o HDInsight, consulte [utilizar o SSH com o HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

## <a id="hadoop"></a>Utilizar comandos de Hadoop

1. Depois de ligar ao cluster do HDInsight, utilize o seguinte comando para iniciar uma tarefa de MapReduce:

    ```bash
    yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar wordcount /example/data/gutenberg/davinci.txt /example/data/WordCountOutput
    ```

    Este comando inicia o `wordcount` classe, que faz a `hadoop-mapreduce-examples.jar` ficheiro. Utiliza o `/example/data/gutenberg/davinci.txt` documento como entrada e saída é armazenado em `/example/data/WordCountOutput`.

    > [!NOTE]
    > Para obter mais informações sobre esta tarefa de MapReduce e os dados de exemplo, consulte [MapReduce de utilização no Hadoop no HDInsight](hdinsight-use-mapreduce.md).

2. A tarefa emite detalhes que processa e devolve informações semelhante para o seguinte texto quando a tarefa é concluída:

        File Input Format Counters
        Bytes Read=1395666
        File Output Format Counters
        Bytes Written=337623

3. Quando a tarefa estiver concluída, utilize o seguinte comando para listar os ficheiros de saída:

    ```bash
    hdfs dfs -ls /example/data/WordCountOutput
    ```

    Este comando apresenta dois ficheiros, `_SUCCESS` e `part-r-00000`. O `part-r-00000` ficheiro contém a saída para esta tarefa.

    > [!NOTE]
    > Algumas tarefas de MapReduce podem dividir os resultados por vários **parte-r-# # #** ficheiros. Se assim for, utilize o # # # sufixo para indicar a ordem dos ficheiros.

4. Para ver o resultado, utilize o seguinte comando:

    ```bash
    hdfs dfs -cat /example/data/WordCountOutput/part-r-00000
    ```

    Este comando apresenta uma lista das palavras que estão contidas no **wasb://example/data/gutenberg/davinci.txt** de ficheiros e o número de vezes que ocorreu de cada palavra. O texto seguinte é um exemplo dos dados que estão contidos no ficheiro:

        wreathed        3
        wreathing       1
        wreaths         1
        wrecked         3
        wrenching       1
        wretched        6
        wriggling       1

## <a id="summary"></a>Resumo

Como pode ver, comandos de Hadoop fornecem uma forma fácil para executar as tarefas do MapReduce em cluster do HDInsight e, em seguida, ver o resultado da tarefa.

## <a id="nextsteps"></a>Passos seguintes

Para obter informações gerais sobre as tarefas do MapReduce no HDInsight:

* [Utilizar o MapReduce do HDInsight Hadoop](hdinsight-use-mapreduce.md)

Para obter informações sobre outras formas pode trabalhar com o Hadoop no HDInsight:

* [Utilizar o Hive com o Hadoop no HDInsight](hdinsight-use-hive.md)
* [Utilizar o Pig com o Hadoop no HDInsight](hdinsight-use-pig.md)
