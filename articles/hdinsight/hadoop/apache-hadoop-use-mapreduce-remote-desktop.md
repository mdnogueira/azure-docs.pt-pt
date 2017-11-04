---
title: MapReduce e o ambiente de trabalho remoto com o Hadoop no HDInsight - Azure | Microsoft Docs
description: Saiba como utilizar o ambiente de trabalho remoto para ligar ao Hadoop no HDInsight e executar tarefas de MapReduce.
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 9d3a7b34-7def-4c2e-bb6c-52682d30dee8
ms.service: hdinsight
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 01/12/2017
ms.author: larryfr
ROBOTS: NOINDEX
ms.openlocfilehash: e0dd2c0c0eeeedda00d73f697897582a48d0fc04
ms.sourcegitcommit: f8437edf5de144b40aed00af5c52a20e35d10ba1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/03/2017
---
# <a name="use-mapreduce-in-hadoop-on-hdinsight-with-remote-desktop"></a>Utilizar o MapReduce no Hadoop no HDInsight com o ambiente de trabalho remoto
[!INCLUDE [mapreduce-selector](../../../includes/hdinsight-selector-use-mapreduce.md)]

Neste artigo, irá aprender como ligar a um Hadoop no HDInsight cluster utilizando o ambiente de trabalho remoto e, em seguida, executar tarefas de MapReduce, utilizando o comando de Hadoop.

> [!IMPORTANT]
> Ambiente de trabalho remoto só está disponível nos clusters do HDInsight baseados em Windows. O Linux é o único sistema operativo utilizado na versão 3.4 ou superior do HDInsight. Para obter mais informações, veja [HDInsight retirement on Windows](../hdinsight-component-versioning.md#hdinsight-windows-retirement) (Desativação do HDInsight no Windows).
>
> Para o HDInsight 3.4 ou superior, consulte [utilizar o MapReduce com o SSH](apache-hadoop-use-mapreduce-ssh.md) para obter informações sobre a ligação ao cluster do HDInsight e executar tarefas de MapReduce.

## <a id="prereq"></a>Pré-requisitos
Para concluir os passos neste artigo, irá precisar do seguinte:

* Um cluster de HDInsight (Hadoop no HDInsight) baseado em Windows
* Um computador cliente com Windows 10, Windows 8 ou Windows 7

## <a id="connect"></a>Estabelecer ligação com o ambiente de trabalho remoto
Ativar o ambiente de trabalho remoto para o cluster do HDInsight, em seguida, ligar-se ao mesmo, seguindo as instruções apresentadas em [ligar a clusters do HDInsight através de RDP](../hdinsight-administer-use-management-portal.md#connect-to-clusters-using-rdp).

## <a id="hadoop"></a>Utilize o comando de Hadoop
Quando estiver ligado ao ambiente de trabalho para o cluster do HDInsight, utilize os seguintes passos para executar uma tarefa de MapReduce, utilizando o comando de Hadoop:

1. No ambiente de trabalho HDInsight, inicie o **linha de comandos do Hadoop**. Esta ação abre uma nova linha de comandos no **c:\apps\dist\hadoop-&lt;número de versão >** diretório.

   > [!NOTE]
   > O número da versão alterado como Hadoop é atualizado. O **HADOOP_HOME** variável de ambiente pode ser utilizada para localizar o caminho. Por exemplo, `cd %HADOOP_HOME%` diretórios é alterado para o diretório de Hadoop, sem necessidade de saber o número de versão.
   >
   >
2. Para utilizar o **Hadoop** comandos para executar uma tarefa de MapReduce de exemplo, utilize o seguinte comando:

        hadoop jar hadoop-mapreduce-examples.jar wordcount wasb:///example/data/gutenberg/davinci.txt wasb:///example/data/WordCountOutput

    Esta ação inicia o **wordcount** classe, que faz a **hadoop-examples.JAR mapreduce** ficheiro no diretório atual. Como entrada, utiliza o **wasb://example/data/gutenberg/davinci.txt** documento e o resultado é armazenado em: **wasb: / / / exemplo/dados/WordCountOutput**.

   > [!NOTE]
   > Para obter mais informações sobre esta tarefa de MapReduce e os dados de exemplo, consulte <a href="hdinsight-use-mapreduce.md">MapReduce de utilização no HDInsight Hadoop</a>.
   >
   >
3. A tarefa emite detalhes como é processada, e devolve informações semelhante ao seguinte quando a tarefa estiver concluída:

        File Input Format Counters
        Bytes Read=1395666
        File Output Format Counters
        Bytes Written=337623
4. Quando a tarefa estiver concluída, utilize o seguinte comando para listar os ficheiros de saída armazenados no **wasb://example/data/WordCountOutput**:

        hadoop fs -ls wasb:///example/data/WordCountOutput

    Isto deve apresentar dois ficheiros, **_SUCCESS** e **parte-r-00000**. O **parte-r-00000** ficheiro contém a saída para esta tarefa.

   > [!NOTE]
   > Algumas tarefas de MapReduce podem dividir os resultados por vários **parte-r-# # #** ficheiros. Se assim for, utilize o # # # sufixo para indicar a ordem dos ficheiros.
   >
   >
5. Para ver o resultado, utilize o seguinte comando:

        hadoop fs -cat wasb:///example/data/WordCountOutput/part-r-00000

    Esta ação apresenta uma lista das palavras que estão contidas no **wasb://example/data/gutenberg/davinci.txt** ficheiro, juntamente com o número de vezes que ocorreu de cada palavra. Segue-se um exemplo dos dados que irão ser contidos no ficheiro:

        wreathed        3
        wreathing       1
        wreaths         1
        wrecked         3
        wrenching       1
        wretched        6
        wriggling       1

## <a id="summary"></a>Resumo
Como pode ver, o comando de Hadoop fornece uma forma fácil de executar as tarefas do MapReduce num cluster do HDInsight e, em seguida, ver o resultado da tarefa.

## <a id="nextsteps"></a>Passos seguintes
Para obter informações gerais sobre as tarefas do MapReduce no HDInsight:

* [Utilizar o MapReduce do HDInsight Hadoop](hdinsight-use-mapreduce.md)

Para obter informações sobre outras formas pode trabalhar com o Hadoop no HDInsight:

* [Utilizar o Hive com o Hadoop no HDInsight](hdinsight-use-hive.md)
* [Utilizar o Pig com o Hadoop no HDInsight](hdinsight-use-pig.md)
