---
title: Utilizar c# com o MapReduce do Hadoop no HDInsight - Azure | Microsoft Docs
description: "Saiba como utilizar c# para criar soluções de MapReduce com o Hadoop no Azure HDInsight."
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: d83def76-12ad-4538-bb8e-3ba3542b7211
ms.custom: hdinsightactive
ms.service: hdinsight
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 10/04/2017
ms.author: larryfr
ms.openlocfilehash: 7254efa5070b06fba0dc56fbf90773b6b94f98ee
ms.sourcegitcommit: f8437edf5de144b40aed00af5c52a20e35d10ba1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/03/2017
---
# <a name="use-c-with-mapreduce-streaming-on-hadoop-in-hdinsight"></a>Utilizar c# com o MapReduce, transmissão em fluxo do Hadoop no HDInsight

Saiba como utilizar c# para criar uma solução de MapReduce no HDInsight.

> [!IMPORTANT]
> O Linux é o único sistema operativo utilizado na versão 3.4 ou superior do HDInsight. Para obter mais informações, consulte [controlo de versões do HDInsight componente](../hdinsight-component-versioning.md).

Transmissão em fluxo do Hadoop é um utilitário que lhe permite executar as tarefas do MapReduce utilizando um script ou executável. Neste exemplo, .NET é utilizado para implementar o mapeador de pontos e reducer para uma solução de contagem do word.

## <a name="net-on-hdinsight"></a>.NET no HDInsight

__HDInsight baseado em Linux__ clusters utilize [Mono (https://mono-project.com)](https://mono-project.com) para executar aplicações de .NET. Versão mono 4.2.1 está incluído com o HDInsight versão 3.5. Para obter mais informações sobre a versão do Mono incluído com o HDInsight, consulte [as versões de componentes do HDInsight](../hdinsight-component-versioning.md). Para utilizar uma versão específica do Mono, consulte o [instalação ou atualização Mono](../hdinsight-hadoop-install-mono.md) documento.

Para obter mais informações sobre Mono compatibilidade com versões do .NET Framework, consulte [compatibilidade Mono](http://www.mono-project.com/docs/about-mono/compatibility/).

## <a name="how-hadoop-streaming-works"></a>Como funciona a transmissão em fluxo do Hadoop

O processo básico utilizado para transmissão em fluxo neste documento é da seguinte forma:

1. Hadoop transmite dados para o mapeador de (mapper.exe neste exemplo) no STDIN.
2. O mapeador de processa os dados e emite pares chave/valor delimitado por separador para STDOUT.
3. A saída é lida por Hadoop e, em seguida, transmitida para o reducer (reducer.exe neste exemplo) STDIN.
4. O reducer lê os pares chave/valor delimitado por separador, processa os dados e, em seguida, emite o resultado como pares chave/valor delimitado por separador no STDOUT.
5. O resultado é lida por Hadoop e escrito para o diretório de saída.

Para obter mais informações sobre a transmissão em fluxo, consulte [Hadoop transmissão em fluxo (https://hadoop.apache.org/docs/r2.7.1/hadoop-streaming/HadoopStreaming.html)](https://hadoop.apache.org/docs/r2.7.1/hadoop-streaming/HadoopStreaming.html).

## <a name="prerequisites"></a>Pré-requisitos

* Uma familiaridade com escrever e a criação de código c# que vise o .NET Framework 4.5. Os passos neste documento utilizam o Visual Studio 2017.

* Uma forma para carregar ficheiros de .exe para o cluster. Os passos neste documento utilizam as ferramentas do Data Lake para Visual Studio para carregar os ficheiros de armazenamento primário para o cluster.

* O Azure PowerShell ou um cliente SSH.

* Um Hadoop no cluster do HDInsight. Para obter mais informações sobre como criar um cluster, consulte [criar um cluster do HDInsight](../hdinsight-hadoop-provision-linux-clusters.md).

## <a name="create-the-mapper"></a>Criar o mapeador de

No Visual Studio, crie um novo __aplicação de consola__ denominado __mapeador__. Utilize o seguinte código para a aplicação:

```csharp
using System;
using System.Text.RegularExpressions;

namespace mapper
{
    class Program
    {
        static void Main(string[] args)
        {
            string line;
            //Hadoop passes data to the mapper on STDIN
            while((line = Console.ReadLine()) != null)
            {
                // We only want words, so strip out punctuation, numbers, etc.
                var onlyText = Regex.Replace(line, @"\.|;|:|,|[0-9]|'", "");
                // Split at whitespace.
                var words = Regex.Matches(onlyText, @"[\w]+");
                // Loop over the words
                foreach(var word in words)
                {
                    //Emit tab-delimited key/value pairs.
                    //In this case, a word and a count of 1.
                    Console.WriteLine("{0}\t1",word);
                }
            }
        }
    }
}
```

Depois de criar a aplicação, crie-o para produzir o `/bin/Debug/mapper.exe` ficheiro no diretório do projeto.

## <a name="create-the-reducer"></a>Criar o reducer

No Visual Studio, crie um novo __aplicação de consola__ denominado __reducer__. Utilize o seguinte código para a aplicação:

```csharp
using System;
using System.Collections.Generic;

namespace reducer
{
    class Program
    {
        static void Main(string[] args)
        {
            //Dictionary for holding a count of words
            Dictionary<string, int> words = new Dictionary<string, int>();

            string line;
            //Read from STDIN
            while ((line = Console.ReadLine()) != null)
            {
                // Data from Hadoop is tab-delimited key/value pairs
                var sArr = line.Split('\t');
                // Get the word
                string word = sArr[0];
                // Get the count
                int count = Convert.ToInt32(sArr[1]);

                //Do we already have a count for the word?
                if(words.ContainsKey(word))
                {
                    //If so, increment the count
                    words[word] += count;
                } else
                {
                    //Add the key to the collection
                    words.Add(word, count);
                }
            }
            //Finally, emit each word and count
            foreach (var word in words)
            {
                //Emit tab-delimited key/value pairs.
                //In this case, a word and a count of 1.
                Console.WriteLine("{0}\t{1}", word.Key, word.Value);
            }
        }
    }
}
```

Depois de criar a aplicação, crie-o para produzir o `/bin/Debug/reducer.exe` ficheiro no diretório do projeto.

## <a name="upload-to-storage"></a>Carregar para o armazenamento

1. No Visual Studio, abra **Explorador de servidores**.

2. Expanda **Azure** e, em seguida, expanda **HDInsight**.

3. Se lhe for solicitado, introduza as credenciais da sua subscrição do Azure e, em seguida, clique em **sessão**.

4. Expanda o cluster do HDInsight que pretende implementar esta aplicação. Uma entrada com o texto __(conta de armazenamento predefinida)__ está listado.

    ![Explorador de servidores que mostra a conta de armazenamento para o cluster](./media/apache-hadoop-dotnet-csharp-mapreduce-streaming/storage.png)

    * Se esta entrada pode ser expandida, estiver a utilizar um __conta do Storage do Azure__ como armazenamento de predefinido para o cluster. Para ver os ficheiros de armazenamento de predefinido para o cluster, expanda a entrada e, em seguida, faça duplo clique o __(contentor predefinido)__.

    * Se esta entrada não pode ser expandida, estiver a utilizar __Azure Data Lake Store__ como armazenamento de predefinido para o cluster. Para ver os ficheiros de armazenamento de predefinido para o cluster, faça duplo clique o __(conta de armazenamento predefinida)__ entrada.

5. Para carregar os ficheiros de .exe, utilize um dos seguintes métodos:

    * Se utilizar um __conta do Storage do Azure__, clique no ícone de carregamento e, em seguida, procure o **bin\debug** pasta para o **mapeador** projeto. Por fim, selecione o **mapper.exe** do ficheiro e clique em **Ok**.

        ![Carregue ícone](./media/apache-hadoop-dotnet-csharp-mapreduce-streaming/upload.png)
    
    * Se utilizar __Azure Data Lake Store__, faça duplo clique numa área vazia na listagem de ficheiros e, em seguida, selecione __carregar__. Por fim, selecione o **mapper.exe** do ficheiro e clique em **abra**.

    Uma vez a __mapper.exe__ carregamento foi concluída, repita o processo de carregamento para o __reducer.exe__ ficheiro.

## <a name="run-a-job-using-an-ssh-session"></a>Execute uma tarefa: através de uma sessão SSH

1. Utilize o SSH para ligar ao cluster do HDInsight. Para obter mais informações, veja [Utilizar SSH com o HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

2. Utilize um dos seguintes comandos para iniciar a tarefa MapReduce:

    * Se utilizar __Data Lake Store__ como armazenamento de predefinido:

        ```bash
        yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-streaming.jar -files adl:///mapper.exe,adl:///reducer.exe -mapper mapper.exe -reducer reducer.exe -input /example/data/gutenberg/davinci.txt -output /example/wordcountout
        ```
    
    * Se utilizar __Storage do Azure__ como armazenamento de predefinido:

        ```bash
        yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-streaming.jar -files wasb:///mapper.exe,wasb:///reducer.exe -mapper mapper.exe -reducer reducer.exe -input /example/data/gutenberg/davinci.txt -output /example/wordcountout
        ```

    A lista seguinte descreve o que faz cada parâmetro:

    * `hadoop-streaming.jar`: O ficheiro jar que contém a funcionalidade de MapReduce transmissão em fluxo.
    * `-files`: Adiciona o `mapper.exe` e `reducer.exe` ficheiros para esta tarefa. O `adl:///` ou `wasb:///` antes de cada ficheiro é o caminho para a raiz de armazenamento predefinido para o cluster.
    * `-mapper`: Especifica qual o ficheiro implementa o mapeador de pontos.
    * `-reducer`: Especifica qual o ficheiro implementa o reducer.
    * `-input`: Os dados de entrada.
    * `-output`: O directório de saída.

3. Uma vez concluída a tarefa de MapReduce, utilize o seguinte para ver os resultados:

    ```bash
    hdfs dfs -text /example/wordcountout/part-00000
    ```

    O texto seguinte é um exemplo dos dados devolvidos por este comando:

        you     1128
        young   38
        younger 1
        youngest        1
        your    338
        yours   4
        yourself        34
        yourselves      3
        youth   17

## <a name="run-a-job-using-powershell"></a>Execute uma tarefa: com o PowerShell

Utilize o seguinte script do PowerShell para executar uma tarefa de MapReduce e transferir os resultados.

[!code-powershell[main](../../../powershell_scripts/hdinsight/use-csharp-mapreduce/use-csharp-mapreduce.ps1?range=5-87)]

Este script pede-lhe o nome de conta de início de sessão do cluster e a palavra-passe, juntamente com o nome de cluster do HDInsight. Uma vez concluída a tarefa, o resultado é transferido para um ficheiro denominado `output.txt`. O texto seguinte é um exemplo dos dados no `output.txt` ficheiro:

    you     1128
    young   38
    younger 1
    youngest        1
    your    338
    yours   4
    yourself        34
    yourselves      3
    youth   17

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre como utilizar o MapReduce com o HDInsight, consulte [utilizar de MapReduce com o HDInsight](hdinsight-use-mapreduce.md).

Para obter informações sobre como utilizar c# com o Hive e Pig, consulte [utilizar um c# definido pelo utilizador função com o Hive e Pig](apache-hadoop-hive-pig-udf-dotnet-csharp.md).

Para obter informações sobre como utilizar c# com o Storm no HDInsight, consulte [desenvolver topologias c# para Storm no HDInsight](../storm/apache-storm-develop-csharp-visual-studio-topology.md).