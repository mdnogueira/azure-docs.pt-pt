---
title: "Desenvolver Python transmissão em fluxo de tarefas de MapReduce com o HDInsight - Azure | Microsoft Docs"
description: "Saiba como utilizar o Python na transmissão em sequência de tarefas de MapReduce. Hadoop fornece uma API de transmissão em fluxo para MapReduce para escrever em idiomas diferentes de Java."
services: hdinsight
keyword: mapreduce python,python map reduce,python mapreduce
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 7631d8d9-98ae-42ec-b9ec-ee3cf7e57fb3
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 10/23/2017
ms.author: larryfr
ms.openlocfilehash: 76cf2556f911948689c96deb1cbab350f2559817
ms.sourcegitcommit: f8437edf5de144b40aed00af5c52a20e35d10ba1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/03/2017
---
# <a name="develop-python-streaming-mapreduce-programs-for-hdinsight"></a>Desenvolver Python MapReduce programas transmissão em fluxo para o HDInsight

Saiba como utilizar o Python nas operações de MapReduce de transmissão em fluxo. Hadoop fornece uma API de transmissão em fluxo para MapReduce que permite-lhe escrever mapa e reduzir as funções em idiomas diferentes de Java. Os passos neste documento o mapa de implementar e reduzir os componentes no Python.

## <a name="prerequisites"></a>Pré-requisitos

* Um Hadoop baseado em Linux num cluster do HDInsight

  > [!IMPORTANT]
  > Os passos neste documento exigem um cluster do HDInsight que utiliza o Linux. O Linux é o único sistema operativo utilizado na versão 3.4 ou superior do HDInsight. Para obter mais informações, veja [HDInsight retirement on Windows](../hdinsight-component-versioning.md#hdinsight-windows-retirement) (Desativação do HDInsight no Windows).

* Editor de texto

  > [!IMPORTANT]
  > O editor de texto tem de utilizar LF como o fim de linha. Utilizar um fim de linha de CRLF faz com que erros ao executar a tarefa de MapReduce nos clusters do HDInsight baseado em Linux.

* O `ssh` e `scp` comandos, ou [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview?view=azurermps-3.8.0)

## <a name="word-count"></a>Contagem de Word

Neste exemplo é que uma contagem de word básico implementada uma python um mapeador de pontos e reducer. O mapeador de quebras de frases no palavras individuais e o reducer contagens produzir o resultado e agrega as palavras.

O fluxograma a seguir ilustra o que acontece durante o mapa e reduzir fases.

![Ilustração do processo de mapreduce](./media/apache-hadoop-streaming-python/HDI.WordCountDiagram.png)

## <a name="streaming-mapreduce"></a>MapReduce transmissão em fluxo

Hadoop permite-lhe especificar um ficheiro que contém o mapa e reduzir a lógica que é utilizada por uma tarefa. Os requisitos específicos para o mapa e reduzir a lógica são:

* **Entrada**: O mapa e reduzir componentes devem ler os dados de entrada do STDIN.
* **Saída**: O mapa e reduzir componentes tem de escrever dados de saída STDOUT.
* **Formato de dados**: os dados consumidos e produzidos tem de ser um par chave/valor, separado por um caráter de separador.

Python pode facilmente lidar com estes requisitos, utilizando o `sys` módulo lida STDIN e utilizar `print` imprimam em STDOUT. A tarefa restantes é simplesmente os dados com um separador de formatação (`\t`) caráter entre a chave e valor.

## <a name="create-the-mapper-and-reducer"></a>Criar o mapeador de pontos e reducer

1. Crie um ficheiro denominado `mapper.py` e utilize o seguinte código como o conteúdo:

   ```python
   #!/usr/bin/env python

   # Use the sys module
   import sys

   # 'file' in this case is STDIN
   def read_input(file):
       # Split each line into words
       for line in file:
           yield line.split()

   def main(separator='\t'):
       # Read the data using read_input
       data = read_input(sys.stdin)
       # Process each word returned from read_input
       for words in data:
           # Process each word
           for word in words:
               # Write to STDOUT
               print '%s%s%d' % (word, separator, 1)

   if __name__ == "__main__":
       main()
   ```

2. Crie um ficheiro denominado **reducer.py** e utilize o seguinte código como o conteúdo:

   ```python
   #!/usr/bin/env python

   # import modules
   from itertools import groupby
   from operator import itemgetter
   import sys

   # 'file' in this case is STDIN
   def read_mapper_output(file, separator='\t'):
       # Go through each line
       for line in file:
           # Strip out the separator character
           yield line.rstrip().split(separator, 1)

   def main(separator='\t'):
       # Read the data using read_mapper_output
       data = read_mapper_output(sys.stdin, separator=separator)
       # Group words and counts into 'group'
       #   Since MapReduce is a distributed process, each word
       #   may have multiple counts. 'group' will have all counts
       #   which can be retrieved using the word as the key.
       for current_word, group in groupby(data, itemgetter(0)):
           try:
               # For each word, pull the count(s) for the word
               #   from 'group' and create a total count
               total_count = sum(int(count) for current_word, count in group)
               # Write to stdout
               print "%s%s%d" % (current_word, separator, total_count)
           except ValueError:
               # Count was not a number, so do nothing
               pass

   if __name__ == "__main__":
       main()
   ```

## <a name="run-using-powershell"></a>Executar com o PowerShell

Para garantir que os ficheiros de tem o direito de linha endings, utilize o seguinte script do PowerShell:

[!code-powershell[main](../../../powershell_scripts/hdinsight/streaming-python/streaming-python.ps1?range=138-140)]

Utilize o seguinte script do PowerShell para carregar os ficheiros, execute a tarefa e ver o resultado:

[!code-powershell[main](../../../powershell_scripts/hdinsight/streaming-python/streaming-python.ps1?range=5-134)]

## <a name="run-from-an-ssh-session"></a>Executar a partir de uma sessão SSH

1. Do seu ambiente de desenvolvimento, no mesmo diretório que `mapper.py` e `reducer.py` ficheiros, utilize o seguinte comando:

    ```bash
    scp mapper.py reducer.py username@clustername-ssh.azurehdinsight.net:
    ```

    Substitua `username` com o nome de utilizador do SSH para o cluster, e `clustername` com o nome do cluster.

    Este comando copia os ficheiros do sistema local para o nó principal.

    > [!NOTE]
    > Se utilizou uma palavra-passe para proteger a sua conta do SSH, é-lhe pedida a palavra-passe. Se tiver utilizado uma chave SSH, poderá ter de utilizar o `-i` parâmetro e o caminho para a chave privada. Por exemplo, `scp -i /path/to/private/key mapper.py reducer.py username@clustername-ssh.azurehdinsight.net:`.

2. Ligar ao cluster através de SSH:

    ```bash
    ssh username@clustername-ssh.azurehdinsight.net`
    ```

    Para obter mais informações sobre, consulte [utilizar o SSH com o HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

3. Para garantir que a mapper.py e reducer.py têm os endings linha correto, utilize os seguintes comandos:

    ```bash
    perl -pi -e 's/\r\n/\n/g' mapper.py
    perl -pi -e 's/\r\n/\n/g' reducer.py
    ```

4. Utilize o seguinte comando para iniciar a tarefa de MapReduce.

    ```bash
    yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-streaming.jar -files mapper.py,reducer.py -mapper mapper.py -reducer reducer.py -input /example/data/gutenberg/davinci.txt -output /example/wordcountout
    ```

    Este comando tem as seguintes partes:

   * **hadoop streaming.jar**: utilizada quando executar operações de MapReduce transmissão em fluxo. -Das interfaces Hadoop com o código de MapReduce externo que fornecer.

   * **-ficheiros**: adiciona os ficheiros especificados para a tarefa de MapReduce.

   * **-Mapeador**: indica qual o ficheiro a utilizar como o mapeador de Hadoop.

   * **-reducer**: indica qual o ficheiro a utilizar como o reducer ao Hadoop.

   * **-entrada**: O ficheiro de entrada que iremos deve contar palavras do.

   * **-saída**: O diretório que o resultado é escrito.

    Como funciona a tarefa de MapReduce, o processo é apresentado como percentagens.

        15/02/05 19:01:04 mapreduce de informações. Tarefa: reduzir o mapa 0% 0% 15/02/05 19:01:16 mapreduce de informações. Tarefa: reduzir o mapa 100% 0% 15/02/05 19:01:27 mapreduce de informações. Tarefa: o mapa 100% reduzir 100%


5. Para ver o resultado, utilize o seguinte comando:

    ```bash
    hdfs dfs -text /example/wordcountout/part-00000
    ```

    Este comando apresenta uma lista de palavras e quantas vezes o word ocorreu.

## <a name="next-steps"></a>Passos seguintes

Agora que aprendeu como utilizar tarefas de MapRedcue transmissão em fluxo com o HDInsight, utilize as hiperligações seguintes para explorar as outras formas de trabalhar com o Azure HDInsight.

* [Utilizar o Hive com o HDInsight](hdinsight-use-hive.md)
* [Utilizar o Pig com o HDInsight](hdinsight-use-pig.md)
* [Utilizar tarefas de MapReduce com o HDInsight](hdinsight-use-mapreduce.md)
