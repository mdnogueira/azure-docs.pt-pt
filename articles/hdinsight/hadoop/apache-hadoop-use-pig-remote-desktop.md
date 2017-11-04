---
title: Utilizar o Pig de Hadoop com o ambiente de trabalho remoto no HDInsight - Azure | Microsoft Docs
description: "Saiba como utilizar o comando de Pig para executar instruções Pig Latin a partir de uma ligação de ambiente de trabalho remoto para um cluster de Hadoop baseado em Windows no HDInsight."
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: e034a286-de0f-465f-8bf1-3d085ca6abed
ms.service: hdinsight
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 01/17/2017
ms.author: larryfr
ROBOTS: NOINDEX
ms.openlocfilehash: 8b5e8e7f400a4494549c997e969a46ca90eb0ba5
ms.sourcegitcommit: f8437edf5de144b40aed00af5c52a20e35d10ba1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/03/2017
---
# <a name="run-pig-jobs-from-a-remote-desktop-connection"></a>Executar tarefas do Pig a partir de uma ligação de ambiente de trabalho remoto
[!INCLUDE [pig-selector](../../../includes/hdinsight-selector-use-pig.md)]

Este documento fornece uma explicação passo a passo para utilizar o comando de Pig para executar instruções Pig Latin a partir de uma ligação de ambiente de trabalho remoto para um cluster do HDInsight baseados em Windows. PIg Latin permite-lhe criar aplicações de MapReduce por descrever transformações de dados, em vez de mapear e reduzir a funções.

> [!IMPORTANT]
> Ambiente de trabalho remoto só está disponível nos clusters do HDInsight que utilizam o Windows como o sistema operativo. O Linux é o único sistema operativo utilizado na versão 3.4 ou superior do HDInsight. Para obter mais informações, veja [HDInsight retirement on Windows](../hdinsight-component-versioning.md#hdinsight-windows-retirement) (Desativação do HDInsight no Windows).
>
> Para o HDInsight 3.4 ou superior, consulte [utilizar o Pig com o HDInsight e SSH](apache-hadoop-use-pig-ssh.md) para obter informações sobre interativamente executar tarefas do Pig diretamente o cluster de uma linha de comandos.

## <a id="prereq"></a>Pré-requisitos
Para concluir os passos neste artigo, irá precisar do seguinte.

* Um cluster de HDInsight (Hadoop no HDInsight) baseado em Windows
* Um computador cliente com Windows 10, Windows 8 ou Windows 7

## <a id="connect"></a>Estabelecer ligação com o ambiente de trabalho remoto
Ativar o ambiente de trabalho remoto para o cluster do HDInsight, em seguida, ligar-se ao mesmo, seguindo as instruções apresentadas em [ligar a clusters do HDInsight através de RDP](../hdinsight-administer-use-management-portal.md#connect-to-clusters-using-rdp).

## <a id="pig"></a>Utilize o comando de Pig
1. Depois de ter uma ligação de ambiente de trabalho remoto, inicie o **linha de comandos do Hadoop** utilizando o ícone no ambiente de trabalho.
2. Utilize o seguinte para iniciar o comando de Pig:

        %pig_home%\bin\pig

    Será apresentada com um `grunt>` linha.
3. Introduza a seguinte instrução:

        LOGS = LOAD 'wasb:///example/data/sample.log';

    Este comando carrega o conteúdo do ficheiro sample.log para o ficheiro de registos. Pode ver o conteúdo do ficheiro utilizando o seguinte comando:

        DUMP LOGS;
4. Transforme os dados através da aplicação de uma expressão regular para extrair apenas o nível de registo de cada registo:

        LEVELS = foreach LOGS generate REGEX_EXTRACT($0, '(TRACE|DEBUG|INFO|WARN|ERROR|FATAL)', 1)  as LOGLEVEL;

    Pode utilizar **informação do estado** para ver os dados depois da transformação. Neste caso, `DUMP LEVELS;`.
5. Continue a aplicar as transformações utilizando as seguintes declarações. Utilize `DUMP` para ver o resultado da transformação depois de cada passo.

    <table>
    <tr>
    <th>Instrução</th><th>O que faz</th>
    </tr>
    <tr>
    <td>FILTEREDLEVELS = níveis de filtro por LOGLEVEL não seja nulo;</td><td>Remove linhas que contêm um valor nulo para o nível de registo e armazena os resultados em FILTEREDLEVELS.</td>
    </tr>
    <tr>
    <td>GROUPEDLEVELS = FILTEREDLEVELS grupo por LOGLEVEL;</td><td>Agrupa as linhas por nível de registo e armazena os resultados em GROUPEDLEVELS.</td>
    </tr>
    <tr>
    <td>FREQUÊNCIAS = foreach GROUPEDLEVELS gerar grupo como LOGLEVEL, CONTAGEM (FILTEREDLEVELS. LOGLEVEL) como CONTAGEM;</td><td>Cria um novo conjunto de dados que contém cada registo exclusivo ocorre valor nível e quantas vezes-lo. Este é armazenado em frequências</td>
    </tr>
    <tr>
    <td>RESULTADO = ordem frequências por CONTAGEM desc;</td><td>Ordena os níveis de registo por contagem (descendente) e armazena no resultado</td>
    </tr>
    </table>
6.Também pode guardar os resultados de uma transformação utilizando o `STORE` instrução. Por exemplo, o seguinte comando guarda o `RESULT` para o **/example/data/pigout** diretório no contentor de armazenamento predefinido para o cluster:

        STORE RESULT into 'wasb:///example/data/pigout'

   > [!NOTE]
   > Os dados são armazenados no diretório especificado em ficheiros com o nome **parte nnnnn**. Se já existe um diretório, receberá uma mensagem de erro.
   >
   >
7. Para sair da linha de comandos da grunt, introduza a seguinte instrução.

        QUIT;

### <a name="pig-latin-batch-files"></a>Ficheiros de batch de PIg Latin
Também pode utilizar o comando de Pig para executar o Pig Latin que está contido num ficheiro.

1. Depois de sair da linha de comandos grunt, abra **bloco de notas** e criar um novo ficheiro designado **pigbatch.pig** no **% PIG_HOME %** diretório.
2. Escreva ou cole as seguintes linhas para o **pigbatch.pig** do ficheiro e guarde-o:

        LOGS = LOAD 'wasb:///example/data/sample.log';
        LEVELS = foreach LOGS generate REGEX_EXTRACT($0, '(TRACE|DEBUG|INFO|WARN|ERROR|FATAL)', 1)  as LOGLEVEL;
        FILTEREDLEVELS = FILTER LEVELS by LOGLEVEL is not null;
        GROUPEDLEVELS = GROUP FILTEREDLEVELS by LOGLEVEL;
        FREQUENCIES = foreach GROUPEDLEVELS generate group as LOGLEVEL, COUNT(FILTEREDLEVELS.LOGLEVEL) as COUNT;
        RESULT = order FREQUENCIES by COUNT desc;
        DUMP RESULT;
3. Utilize o seguinte para executar o **pigbatch.pig** utilizando o comando de pig de ficheiros.

        pig %PIG_HOME%\pigbatch.pig

    Quando a tarefa do batch é concluída, deverá ver o seguinte resultado, o que deve ser idêntica à que utilizou quando `DUMP RESULT;` nos passos anteriores:

        (TRACE,816)
        (DEBUG,434)
        (INFO,96)
        (WARN,11)
        (ERROR,6)
        (FATAL,2)

## <a id="summary"></a>Resumo
Como pode ver, o comando de Pig permite-lhe executar operações de MapReduce interativo ou executar tarefas do Pig Latin que estão armazenadas num ficheiro batch.

## <a id="nextsteps"></a>Passos seguintes
Para obter informações gerais sobre o Pig no HDInsight:

* [Utilizar o Pig com o Hadoop no HDInsight](hdinsight-use-pig.md)

Para obter informações sobre outras formas pode trabalhar com o Hadoop no HDInsight:

* [Utilizar o Hive com o Hadoop no HDInsight](hdinsight-use-hive.md)
* [Utilizar o MapReduce com o Hadoop no HDInsight](hdinsight-use-mapreduce.md)
