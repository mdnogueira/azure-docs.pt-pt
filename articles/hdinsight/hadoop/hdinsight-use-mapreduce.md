---
title: MapReduce com o Hadoop no HDInsight | Microsoft Docs
description: Saiba como executar as tarefas do MapReduce no Hadoop em clusters do HDInsight.
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 7f321501-d62c-4ffc-b5d6-102ecba6dd76
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 09/20/2017
ms.author: larryfr
ms.openlocfilehash: b267f5ce5ec76a89327ac58ca76895f8bcc4696b
ms.sourcegitcommit: f8437edf5de144b40aed00af5c52a20e35d10ba1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/03/2017
---
# <a name="use-mapreduce-in-hadoop-on-hdinsight"></a>Utilizar o MapReduce no Hadoop no HDInsight

Saiba como executar as tarefas do MapReduce nos clusters do HDInsight. Utilize a tabela seguinte para detetar as várias formas, que pode ser utilizado em MapReduce com o HDInsight:

| **Utilize esta opção**... | **… .to fazê-lo** | … .with isto **sistema de operativo de cluster** | … .from isto **sistema operativo do cliente** |
|:--- |:--- |:--- |:--- |
| [SSH](apache-hadoop-use-mapreduce-ssh.md) |Utilize o comando de Hadoop através de **SSH** |Linux |Linux, Unix, Mac OS X ou Windows |
| [REST](apache-hadoop-use-mapreduce-curl.md) |Submeter a tarefa remotamente utilizando **REST** (exemplos utilizam cURL) |Linux ou do Windows |Linux, Unix, Mac OS X ou Windows |
| [Windows PowerShell](apache-hadoop-use-mapreduce-powershell.md) |Submeter a tarefa remotamente utilizando **do Windows PowerShell** |Linux ou do Windows |Windows |
| [Ambiente de trabalho remoto](apache-hadoop-use-mapreduce-remote-desktop.md) (HDInsight 3.2 e 3.3) |Utilize o comando de Hadoop através de **ambiente de trabalho remoto** |Windows |Windows |

> [!IMPORTANT]
> O Linux é o único sistema operativo utilizado na versão 3.4 ou superior do HDInsight. Para obter mais informações, veja [HDInsight retirement on Windows](../hdinsight-component-versioning.md#hdinsight-windows-retirement) (Desativação do HDInsight no Windows).

## <a id="whatis"></a>O que é o MapReduce

Hadoop MapReduce é uma arquitetura de software para escrever as tarefas que processam grandes quantidades de dados. Dados de entrada são divididos em segmentos independentes. Cada segmento é processado em paralelo em todos os nós do cluster. Uma tarefa de MapReduce é composto por duas funções:

* **Mapeador de**: consome dados de entrada, analisa-(normalmente com a filtrar e ordenar operações) e emite cadeias de identificação (pares chave-valor)

* **Reducer**: consome emitidas pelo mapeador de cadeias de identificação e efetua uma operação de resumo que cria um resultado mais pequeno e combinado a partir dos dados do mapeador

Um exemplo de tarefa de MapReduce de contagem do word básico é ilustrado no diagrama seguinte:

![HDI. WordCountDiagram][image-hdi-wordcountdiagram]

O resultado da tarefa é uma contagem de quantas vezes que ocorreu de cada palavra no texto.

* O mapeador de demora a cada linha a partir do texto de entrada como entrada e interrompe a para palavras. -Emite um chave/valor par sempre uma palavra ocorre da word é seguido um 1. O resultado é ordenado antes de a enviar para reducer.
* O reducer soma estas contagens individuais para cada palavra e emite um único par chave/valor que contenha a palavra seguida pela soma do respetivas ocorrências.

MapReduce pode ser implementado em vários idiomas. Java é a implementação mais comuns e é utilizada para fins de demonstração neste documento.

## <a name="development-languages"></a>Linguagens de programação

Idiomas ou estruturas baseadas em Java e a Máquina Virtual de Java podem ser executadas diretamente como uma tarefa de MapReduce. O exemplo utilizado neste documento é uma aplicação de Java MapReduce. Idiomas não Java, como c#, Python ou executáveis autónomo, tem de utilizar transmissão em fluxo do Hadoop.

Transmissão em fluxo do Hadoop comunica com o mapeador de pontos e reducer através de STDIN e STDOUT. O mapeador de reducer ler os dados de uma linha de cada vez do STDIN e escrever a saída STDOUT. Cada linha de leitura ou emitidos pelo mapeador de pontos e reducer tem de estar no formato de um par chave/valor, delimitado pelo caráter separador:

    [key]/t[value]

Para obter mais informações, consulte [transmissão em fluxo do Hadoop](http://hadoop.apache.org/docs/r1.2.1/streaming.html).

Para obter exemplos de utilização do Hadoop de transmissão em fluxo com o HDInsight, consulte os seguintes documentos:

* [Desenvolver as tarefas do MapReduce c#](apache-hadoop-dotnet-csharp-mapreduce-streaming.md)

* [Desenvolver as tarefas do Python MapReduce](apache-hadoop-streaming-python.md)

## <a id="data"></a>Dados de exemplo

O HDInsight fornece vários conjuntos de dados de exemplo, que são armazenados no `/example/data` e `/HdiSamples` diretório. Estes diretórios são no armazenamento de predefinido para o cluster. Neste documento, utilizamos o `/example/data/gutenberg/davinci.txt` ficheiro. Este ficheiro contém os blocos de notas do Leonardo Da Vinci.

## <a id="job"></a>MapReduce de exemplo

Um exemplo de aplicação de contagem do MapReduce word está incluído no cluster do HDInsight. Este exemplo está localizado em `/example/jars/hadoop-mapreduce-examples.jar` no armazenamento de predefinido para o cluster.

O seguinte código Java é a origem da aplicação MapReduce contida no `hadoop-mapreduce-examples.jar` ficheiro:

```java
package org.apache.hadoop.examples;

import java.io.IOException;
import java.util.StringTokenizer;

import org.apache.hadoop.conf.Configuration;
import org.apache.hadoop.fs.Path;
import org.apache.hadoop.io.IntWritable;
import org.apache.hadoop.io.Text;
import org.apache.hadoop.mapreduce.Job;
import org.apache.hadoop.mapreduce.Mapper;
import org.apache.hadoop.mapreduce.Reducer;
import org.apache.hadoop.mapreduce.lib.input.FileInputFormat;
import org.apache.hadoop.mapreduce.lib.output.FileOutputFormat;
import org.apache.hadoop.util.GenericOptionsParser;

public class WordCount {

    public static class TokenizerMapper
        extends Mapper<Object, Text, Text, IntWritable>{

    private final static IntWritable one = new IntWritable(1);
    private Text word = new Text();

    public void map(Object key, Text value, Context context
                    ) throws IOException, InterruptedException {
        StringTokenizer itr = new StringTokenizer(value.toString());
        while (itr.hasMoreTokens()) {
        word.set(itr.nextToken());
        context.write(word, one);
        }
    }
    }

    public static class IntSumReducer
        extends Reducer<Text,IntWritable,Text,IntWritable> {
    private IntWritable result = new IntWritable();

    public void reduce(Text key, Iterable<IntWritable> values,
                        Context context
                        ) throws IOException, InterruptedException {
        int sum = 0;
        for (IntWritable val : values) {
        sum += val.get();
        }
        result.set(sum);
        context.write(key, result);
    }
    }

    public static void main(String[] args) throws Exception {
    Configuration conf = new Configuration();
    String[] otherArgs = new GenericOptionsParser(conf, args).getRemainingArgs();
    if (otherArgs.length != 2) {
        System.err.println("Usage: wordcount <in> <out>");
        System.exit(2);
    }
    Job job = new Job(conf, "word count");
    job.setJarByClass(WordCount.class);
    job.setMapperClass(TokenizerMapper.class);
    job.setCombinerClass(IntSumReducer.class);
    job.setReducerClass(IntSumReducer.class);
    job.setOutputKeyClass(Text.class);
    job.setOutputValueClass(IntWritable.class);
    FileInputFormat.addInputPath(job, new Path(otherArgs[0]));
    FileOutputFormat.setOutputPath(job, new Path(otherArgs[1]));
    System.exit(job.waitForCompletion(true) ? 0 : 1);
    }
}
```

Para obter instruções escrever as suas próprias aplicações de MapReduce, consulte os seguintes documentos:

* [Desenvolver aplicações de Java MapReduce para o HDInsight](apache-hadoop-develop-deploy-java-mapreduce-linux.md)

* [Desenvolver aplicações de MapReduce do Python para o HDInsight](apache-hadoop-streaming-python.md)

## <a id="run"></a>Execute o MapReduce

HDInsight pode executar tarefas de HiveQL utilizando vários métodos. Utilize a tabela seguinte para decidir qual o método é mais adequado para si e, em seguida, siga a ligação para obter instruções.

| **Utilize esta opção**... | **… .to fazê-lo** | … .with isto **sistema de operativo de cluster** | … .from isto **sistema operativo do cliente** |
|:--- |:--- |:--- |:--- |
| [SSH](apache-hadoop-use-mapreduce-ssh.md) |Utilize o comando de Hadoop através de **SSH** |Linux |Linux, Unix, Mac OS X ou Windows |
| [Curl](apache-hadoop-use-mapreduce-curl.md) |Submeter a tarefa remotamente utilizando **REST** |Linux ou do Windows |Linux, Unix, Mac OS X ou Windows |
| [Windows PowerShell](apache-hadoop-use-mapreduce-powershell.md) |Submeter a tarefa remotamente utilizando **do Windows PowerShell** |Linux ou do Windows |Windows |
| [Ambiente de trabalho remoto](apache-hadoop-use-mapreduce-remote-desktop.md) (HDInsight 3.2 e 3.3) |Utilize o comando de Hadoop através de **ambiente de trabalho remoto** |Windows |Windows |

> [!IMPORTANT]
> O Linux é o único sistema operativo utilizado na versão 3.4 ou superior do HDInsight. Para obter mais informações, veja [HDInsight retirement on Windows](../hdinsight-component-versioning.md#hdinsight-windows-retirement) (Desativação do HDInsight no Windows).

## <a id="nextsteps"></a>Passos seguintes

Para obter mais informações sobre como trabalhar com dados no HDInsight, consulte os seguintes documentos:

* [Desenvolver programas de Java MapReduce para o HDInsight](apache-hadoop-develop-deploy-java-mapreduce-linux.md)

* [Desenvolver Python MapReduce programas transmissão em fluxo para o HDInsight](apache-hadoop-streaming-python.md)

* [Use Hive with HDInsight (Utilizar o Hive com o HDInsight)][hdinsight-use-hive]

* [Use Pig with HDInsight (Utilizar o Pig com o HDInsight)][hdinsight-use-pig]


[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-get-started]:apache-hadoop-linux-tutorial-get-started.md
[hdinsight-develop-mapreduce-jobs]: apache-hadoop-develop-deploy-java-mapreduce-linux.md
[hdinsight-use-hive]:../hdinsight-use-hive.md
[hdinsight-use-pig]:hdinsight-use-pig.md


[powershell-install-configure]: /powershell/azureps-cmdlets-docs

[image-hdi-wordcountdiagram]: ./media/hdinsight-use-mapreduce/HDI.WordCountDiagram.gif
