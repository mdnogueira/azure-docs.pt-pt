---
title: Executar exemplos de MapReduce do Hadoop no HDInsight - Azure | Microsoft Docs
description: "Começar a utilizar o MapReduce amostras nos ficheiros jar incluídos no HDInsight. Utilizar o SSH para ligar ao cluster e, em seguida, utilize o comando de Hadoop para executar tarefas de exemplo."
keywords: jar de exemplo do hadoop, hadoop exemplos jar, exemplos de mapreduce do hadoop, exemplos de mapreduce
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: e1d2a0b9-1659-4fab-921e-4a8990cbb30a
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/26/2017
ms.author: larryfr
ms.openlocfilehash: 3e52fc6ee18faa9b8a04ad19db52dd48505782ae
ms.sourcegitcommit: f8437edf5de144b40aed00af5c52a20e35d10ba1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/03/2017
---
# <a name="run-the-mapreduce-examples-included-in-hdinsight"></a>Executar os exemplos de MapReduce incluídos no HDInsight

[!INCLUDE [samples-selector](../../../includes/hdinsight-run-samples-selector.md)]

Saiba como executar os exemplos de MapReduce incluídos com o Hadoop no HDInsight.

## <a name="prerequisites"></a>Pré-requisitos

* **Um cluster do HDInsight**: consulte [começar a utilizar o Hadoop com o Hive no HDInsight no Linux](apache-hadoop-linux-tutorial-get-started.md)

    > [!IMPORTANT]
    > O Linux é o único sistema operativo utilizado na versão 3.4 ou superior do HDInsight. Para obter mais informações, veja [HDInsight retirement on Windows](../hdinsight-component-versioning.md#hdinsight-windows-retirement) (Desativação do HDInsight no Windows).

* **Um cliente SSH**: para obter mais informações, consulte [utilizar o SSH com o HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

## <a name="the-mapreduce-examples"></a>Os exemplos de MapReduce

**Localização**: exemplos estão localizados no cluster do HDInsight em `/usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar`.

**Conteúdo**: os exemplos seguintes estão contidos neste arquivo:

* `aggregatewordcount`: Um agregado com a base de programa de mapreduce que contagens palavras nos ficheiros de entrada.
* `aggregatewordhist`: Um agregado com a base de programa de mapreduce que calcula a histograma das palavras nos ficheiros de entrada.
* `bbp`: Um programa de mapreduce que utiliza Bailey-Borwein-Plouffe para dígitos exatos do instalador de plataforma de computação.
* `dbcount`: Uma tarefa de exemplo que contagens registos pageview armazenados numa base de dados.
* `distbbp`: Um programa de mapreduce que utiliza uma fórmula de tipo BBP para bits exatos de instalador de plataforma de computação.
* `grep`: Um programa de mapreduce que contagens de correspondências de um regex na entrada.
* `join`: Uma tarefa que efetua uma junção através de ordenada, igualmente particionada conjuntos de dados.
* `multifilewc`: Uma tarefa que contagens de palavras de vários ficheiros de mensagens em fila.
* `pentomino`: Um mapreduce mosaico disposição dos programa para encontrar soluções para problemas de pentomino.
* `pi`: Um programa de mapreduce que calcula Pi utilizando um quasi-Monte método Carlo.
* `randomtextwriter`: Um programa de mapreduce que escreve 10 GB de dados de texto aleatórios por nó.
* `randomwriter`: Um programa de mapreduce que escreve 10 GB de dados aleatórios por nó.
* `secondarysort`: Um exemplo de definir uma ordenação secundária para a fase de reduzir.
* `sort`: Um programa de mapreduce que ordena os dados escritos pelo escritor de aleatório.
* `sudoku`: Um solver sudoku.
* `teragen`: Gere dados para o terasort.
* `terasort`: O terasort execute.
* `teravalidate`: A verificar os resultados da terasort.
* `wordcount`: Um programa de mapreduce que contagens palavras nos ficheiros de entrada.
* `wordmean`: Um programa de mapreduce que contagens a duração média das palavras nos ficheiros de entrada.
* `wordmedian`: Um programa de mapreduce que conta o comprimento mediano das palavras nos ficheiros de entrada.
* `wordstandarddeviation`: Um programa de mapreduce que conta o desvio-padrão do comprimento de palavras nos ficheiros de entrada.

**Código fonte**: código de origem para estes exemplos está incluído no cluster do HDInsight em `/usr/hdp/2.2.4.9-1/hadoop/src/hadoop-mapreduce-project/hadoop-mapreduce-examples`.

> [!NOTE]
> O `2.2.4.9-1` no caminho é a versão de plataforma de dados do Hortonworks para o cluster do HDInsight e pode ser diferente para o cluster.

## <a name="run-the-wordcount-example"></a>Executar o exemplo de wordcount

1. Ligar ao HDInsight através de SSH. Para obter mais informações, veja [Utilizar SSH com o HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

2. Do `username@#######:~$` linha de comandos, utilize o seguinte comando para listar os exemplos:

    ```bash
    yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar
    ```

    Este comando cria a lista de exemplo da secção anterior deste documento.

3. Utilize o seguinte comando para obter ajuda sobre uma amostra específica. Neste caso, o **wordcount** exemplo:

    ```bash
    yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar wordcount
    ```

    Receber a mensagem seguinte:

        Usage: wordcount <in> [<in>...] <out>

    Esta mensagem indica que pode fornecer vários caminhos de entrada para os documentos de origem. O caminho final é onde está armazenada a saída (contagem de palavras nos documentos origem).

4. Utilize o seguinte para contagem de todas as palavras nos blocos de notas do Leonardo Da Vinci, que são fornecidos como dados de exemplo com o cluster:

    ```bash
    yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar wordcount /example/data/gutenberg/davinci.txt /example/data/davinciwordcount
    ```

    Entrada para esta tarefa é lida `/example/data/gutenberg/davinci.txt`. Saída para este exemplo está armazenado num `/example/data/davinciwordcount`. Os dois caminhos estão localizados no armazenamento de predefinido para o cluster, não o sistema de ficheiros local.

   > [!NOTE]
   > Conforme indicado na ajuda para o exemplo de wordcount, pode também especificar vários ficheiros de entrada. Por exemplo, `hadoop jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar wordcount /example/data/gutenberg/davinci.txt /example/data/gutenberg/ulysses.txt /example/data/twowordcount` contabilizaria palavras no davinci.txt e ulysses.txt.

5. Uma vez concluída a tarefa, utilize o seguinte comando para ver o resultado:

    ```bash
    hdfs dfs -cat /example/data/davinciwordcount/*
    ```

    Este comando concatena todos os ficheiros de saída produzidos pela tarefa. Apresenta a saída para a consola. O resultado é semelhante ao seguinte texto:

        zum     1
        zur     1
        zwanzig 1
        zweite  1

    Cada linha representa um word e o número de vezes ocorreu nos dados de entrada.

## <a name="the-sudoku-example"></a>O exemplo de Sudoku

[Sudoku](https://en.wikipedia.org/wiki/Sudoku) é um puzzle lógica composta por 3 x nove 3 grelhas. Alguns células na grelha têm números, enquanto outros estão em branco e o objetivo é resolver das células em branco. A hiperligação anterior tem mais informações sobre puzzle, mas o objetivo deste exemplo consiste em resolver para as células em branco. Por isso, o nosso entrada deve ser um ficheiro que é o seguinte formato:

* Nove linhas de nove colunas
* Cada coluna pode conter um número ou `?` (que indica uma célula em branco)
* Células são separadas por um espaço

Há uma forma de determinados construir Sudoku de pequenos quebra-cabeças; Não é possível repetir um número numa coluna ou linha. Não há um exemplo no cluster do HDInsight que foi construído corretamente. Está localizado em `/usr/hdp/*/hadoop/src/hadoop-mapreduce-project/hadoop-mapreduce-examples/src/main/java/org/apache/hadoop/examples/dancing/puzzle1.dta` e contém o seguinte texto:

    8 5 ? 3 9 ? ? ? ?
    ? ? 2 ? ? ? ? ? ?
    ? ? 6 ? 1 ? ? ? 2
    ? ? 4 ? ? 3 ? 5 9
    ? ? 8 9 ? 1 4 ? ?
    3 2 ? 4 ? ? 8 ? ?
    9 ? ? ? 8 ? 5 ? ?
    ? ? ? ? ? ? 2 ? ?
    ? ? ? ? 4 5 ? 7 8

Para executar este problema de exemplo através do exemplo Sudoku, utilize o seguinte comando:

```bash
yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar sudoku /usr/hdp/*/hadoop/src/hadoop-mapreduce-project/hadoop-mapreduce-examples/src/main/java/org/apache/hadoop/examples/dancing/puzzle1.dta
```

Os resultados são apresentados semelhantes para o seguinte texto:

    8 5 1 3 9 2 6 4 7
    4 3 2 6 7 8 1 9 5
    7 9 6 5 1 4 3 8 2
    6 1 4 8 2 3 7 5 9
    5 7 8 9 6 1 4 2 3
    3 2 9 4 5 7 8 1 6
    9 4 7 2 8 6 5 3 1
    1 8 5 7 3 9 2 6 4
    2 6 3 1 4 5 9 7 8

## <a name="pi--example"></a>Exemplo de instalador de plataforma (π)

O exemplo de pi utiliza uma estatística (quasi-Monte Carlo) método para calcular o valor de pi. Pontos são colocados aleatoriamente um quadrado de unidade. O quadrado também contém um círculo. A probabilidade de que os pontos de coincidir com círculo são iguais para a área do círculo, pi/4. O valor de pi, pode ser estimado do valor 4R. R é o rácio do número de pontos que estão dentro do círculo para o número total de pontos que estão dentro de parênteses. Quanto maior for o exemplo de pontos de utilizados, melhor a estimativa é.

Utilize o seguinte comando para executar este exemplo. Este comando utiliza os mapas de 16 10,000,000 exemplos para calcular o valor de pi:

```bash
yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar pi 16 10000000
```

O valor devolvido por este comando é semelhante à **3.14159155000000000000**. Existência de referências, as primeiras 10 casas decimais de pi são 3.1415926535.

## <a name="10-gb-greysort-example"></a>Exemplo de Greysort de 10 GB

GraySort é uma ordenação benchmark. A métrica é a taxa de ordenação (TB por minuto) que é conseguida ao ordenar grandes quantidades de dados, normalmente, um 100 TB Mínimo.

Este exemplo utiliza um modest 10 GB de dados para que possam executar relativamente rapidamente. Utiliza as aplicações de MapReduce desenvolvidas pela Owen O'Malley e Arun Murthy. Estas aplicações won o benchmark de ordenação de terabyte para fins gerais ("daytona") anuais no 2009, com uma velocidade de 0.578 TB minuto (100 TB no 173 minutos). Para obter mais informações sobre esta e outras ordenação testes de desempenho, consulte o [Sortbenchmark](http://sortbenchmark.org/) site.

Este exemplo utiliza três conjuntos de MapReduce programas:

* **TeraGen**: MapReduce de um programa que gera linhas de dados para ordenar

* **TeraSort**: amostras de dados de entrada e utiliza o MapReduce para ordenar os dados para uma ordem total

    TeraSort é uma ordenação padrão do MapReduce, exceto um particionador personalizado. O particionador utiliza uma lista ordenada de chaves N-1 amostragem que definem o intervalo de chave para cada reduza. Em especial, todas as chaves esses que exemplo [i-1] < = chave < exemplo [i] são enviados para reduzir i. Este particionador são todas as garantias de que as saídas de reduzir i menor que o resultado de reduzir i + 1.

* **TeraValidate**: MapReduce de um programa que valida que o resultado global é ordenado

    Cria um mapeamento por ficheiro no diretório de saída e cada mapa assegura que cada chave é inferior ou igual à anterior. A função de mapa gera registos das chaves primeiro e últimos de cada ficheiro. A função de reduza garante que a primeira chave de ficheiro i é maior do que a chave última do i-1 de ficheiro. Quaisquer problemas são reportados como uma saída da fase de reduza, com as chaves que estão fora de ordem.

Utilize os seguintes passos para gerar dados, ordenar e, em seguida, validar a saída:

1. Gerar 10 GB de dados, o que são armazenados para o armazenamento de predefinido do cluster do HDInsight em `/example/data/10GB-sort-input`:

    ```bash
    yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar teragen -Dmapred.map.tasks=50 100000000 /example/data/10GB-sort-input
    ```

    O `-Dmapred.map.tasks` indica Hadoop quantos de mapa de tarefas a utilizar para esta tarefa. Os dois parâmetros finais instrui a tarefa para criar 10 GB de dados e para armazená-la em `/example/data/10GB-sort-input`.

2. Utilize o seguinte comando para ordenar os dados:

    ```bash
    yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar terasort -Dmapred.map.tasks=50 -Dmapred.reduce.tasks=25 /example/data/10GB-sort-input /example/data/10GB-sort-output
    ```

    O `-Dmapred.reduce.tasks` indica Hadoop quantos reduzir as tarefas a utilizar para a tarefa. Os dois parâmetros finais são apenas de entrada e de saída localizações de dados.

3. Utilize o seguinte para validar os dados gerados pela ordenação:

    ```bash
    yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar teravalidate -Dmapred.map.tasks=50 -Dmapred.reduce.tasks=25 /example/data/10GB-sort-output /example/data/10GB-sort-validate
    ```

## <a name="next-steps"></a>Passos seguintes

Do artigo, aprendeu a executar as amostras incluídas com os clusters do HDInsight baseado em Linux. Para tutoriais sobre como utilizar o Pig, Hive e MapReduce com o HDInsight, consulte os tópicos seguintes:

* [Utilizar o Pig com o Hadoop no HDInsight](hdinsight-use-pig.md)
* [Utilizar o Hive com o Hadoop no HDInsight](hdinsight-use-hive.md)
* [Utilizar o MapReduce com o Hadoop no HDInsight](hdinsight-use-mapreduce.md)

[hdinsight-sdk-documentation]: https://msdn.microsoft.com/library/azure/dn479185.aspx

[hdinsight-submit-jobs]:submit-apache-hadoop-jobs-programmatically.md
[hdinsight-introduction]:apache-hadoop-introduction.md



[hdinsight-samples]: hdinsight-run-samples.md

