---
title: Utilizar o Pig de Hadoop com REST no HDInsight - Azure | Microsoft Docs
description: Saiba como utilizar REST para executar tarefas de Pig Latin num cluster de Hadoop no Azure HDInsight.
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: ed5e10d1-4f47-459c-a0d6-7ff967b468c4
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 10/23/2017
ms.author: larryfr
ms.openlocfilehash: a9fb0c69020f0b24187b68aa4bfda8b36d5b928e
ms.sourcegitcommit: f8437edf5de144b40aed00af5c52a20e35d10ba1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/03/2017
---
# <a name="run-pig-jobs-with-hadoop-on-hdinsight-by-using-rest"></a>Executar tarefas do Pig com o Hadoop no HDInsight ao utilizar REST

[!INCLUDE [pig-selector](../../../includes/hdinsight-selector-use-pig.md)]

Saiba como executar tarefas do Pig Latin ao efetuar pedidos REST para um cluster do Azure HDInsight. Curl é utilizado para demonstrar como pode interagir com o HDInsight utilizando a API de REST de WebHCat.

> [!NOTE]
> Se já estiver familiarizado com a utilização de servidores de Hadoop baseado em Linux, mas se estiver familiarizado com o HDInsight, consulte [sugestões de HDInsight baseado em Linux](../hdinsight-hadoop-linux-information.md).

## <a id="prereq"></a>Pré-requisitos

* Um cluster do Azure HDInsight (Hadoop no HDInsight) (baseado em Linux ou baseadas no Windows)

  > [!IMPORTANT]
  > O Linux é o único sistema operativo utilizado na versão 3.4 ou superior do HDInsight. Para obter mais informações, veja [HDInsight retirement on Windows](../hdinsight-component-versioning.md#hdinsight-windows-retirement) (Desativação do HDInsight no Windows).

* [Curl](http://curl.haxx.se/)

* [jq](http://stedolan.github.io/jq/)

## <a id="curl"></a>Executar tarefas do Pig utilizando Curl

> [!NOTE]
> A API de REST está protegida por [autenticação de acesso básico](http://en.wikipedia.org/wiki/Basic_access_authentication). Sempre efetue pedidos utilizando HTTP Secure (HTTPS) para se certificar de que as suas credenciais são enviadas de forma segura para o servidor.
>
> Quando utilizar os comandos nesta secção, substitua `USERNAME` com o utilizador para autenticar para o cluster e substitua `PASSWORD` com a palavra-passe da conta de utilizador. Substitua `CLUSTERNAME` pelo nome do cluster.
>


1. Numa linha de comandos, utilize o seguinte comando para verificar que se consegue ligar ao cluster do HDInsight:

    ```bash
    curl -u USERNAME:PASSWORD -G https://CLUSTERNAME.azurehdinsight.net/templeton/v1/status
    ```

    Deverá receber a resposta JSON seguinte:

        {"status":"ok","version":"v1"}

    Os parâmetros utilizados neste comando são os seguintes:

    * **-u**: nome de utilizador e palavra-passe utilizada para autenticar o pedido
    * **-G**: indica que este pedido é um pedido GET

     O início do URL, **https://CLUSTERNAME.azurehdinsight.net/templeton/v1**, é o mesmo para todos os pedidos. O caminho, **/status**, indica que o pedido está a devolver o estado de WebHCat (também conhecido como Templeton) para o servidor.

2. Utilize o seguinte código para submeter uma tarefa Pig Latin para o cluster:

    ```bash
    curl -u USERNAME:PASSWORD -d user.name=USERNAME -d execute="LOGS=LOAD+'/example/data/sample.log';LEVELS=foreach+LOGS+generate+REGEX_EXTRACT($0,'(TRACE|DEBUG|INFO|WARN|ERROR|FATAL)',1)+as+LOGLEVEL;FILTEREDLEVELS=FILTER+LEVELS+by+LOGLEVEL+is+not+null;GROUPEDLEVELS=GROUP+FILTEREDLEVELS+by+LOGLEVEL;FREQUENCIES=foreach+GROUPEDLEVELS+generate+group+as+LOGLEVEL,COUNT(FILTEREDLEVELS.LOGLEVEL)+as+count;RESULT=order+FREQUENCIES+by+COUNT+desc;DUMP+RESULT;" -d statusdir="/example/pigcurl" https://CLUSTERNAME.azurehdinsight.net/templeton/v1/pig
    ```

    Os parâmetros utilizados neste comando são os seguintes:

    * **-d**: porque `-G` não for utilizado, o pedido está predefinida para o método POST. `-d`Especifica os valores de dados que são enviados com o pedido.

    * **User.name**: O utilizador que está a executar o comando
    * **executar**: O Pig Latin instruções para executar
    * **statusdir**: O diretório que o estado desta tarefa é escrito

    > [!NOTE]
    > Tenha em atenção que os espaços em declarações de Pig Latin são substituídos pelo `+` caráter quando utilizado com Curl.

    Este comando deverá devolver um ID de tarefa que pode ser utilizado para verificar o estado da tarefa, por exemplo:

        {"id":"job_1415651640909_0026"}

3. Para verificar o estado da tarefa, utilize o seguinte comando

     ```bash
    curl -G -u USERNAME:PASSWORD -d user.name=USERNAME https://CLUSTERNAME.azurehdinsight.net/templeton/v1/jobs/JOBID | jq .status.state
    ```

     Substitua `JOBID` com o valor obtido no passo anterior. Por exemplo, se o valor de retorno foi `{"id":"job_1415651640909_0026"}`, em seguida, `JOBID` é `job_1415651640909_0026`.

    Se a tarefa estiver concluída, o estado é **com êxito**.

    > [!NOTE]
    > Este pedido Curl devolve um documento de JavaScript Object Notation (JSON) com informações sobre a tarefa e jq é utilizado para obter o valor de estado.

## <a id="results"></a>Ver os resultados

Quando o estado da tarefa foi alterado para **com êxito**, pode obter os resultados da tarefa. O `statusdir` parâmetro transmitido com a consulta contém a localização do ficheiro de saída; neste caso, `/example/pigcurl`.

HDInsight pode utilizar o armazenamento do Azure ou o Azure Data Lake Store, como o arquivo de dados predefinido. Existem várias formas de obter dados consoante qual delas utilizar. Para obter mais informações, consulte a secção de armazenamento do [informações do HDInsight baseado em Linux](../hdinsight-hadoop-linux-information.md#hdfs-azure-storage-and-data-lake-store) documento.

## <a id="summary"></a>Resumo

Como é demonstrado neste documento, pode utilizar um pedido HTTP não processado para executar, monitorizar e ver os resultados de tarefas do Pig no cluster do HDInsight.

Para obter mais informações sobre a interface REST utilizada neste artigo, consulte o [WebHCat referência](https://cwiki.apache.org/confluence/display/Hive/WebHCat+Reference).

## <a id="nextsteps"></a>Passos seguintes

Para obter informações gerais sobre o Pig no HDInsight:

* [Utilizar o Pig com o Hadoop no HDInsight](hdinsight-use-pig.md)

Para obter informações sobre outras formas pode trabalhar com o Hadoop no HDInsight:

* [Utilizar o Hive com o Hadoop no HDInsight](hdinsight-use-hive.md)
* [Utilizar o MapReduce com o Hadoop no HDInsight](hdinsight-use-mapreduce.md)
