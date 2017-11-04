---
title: Utilize MapReduce e Curl com o Hadoop no HDInsight - Azure | Microsoft Docs
description: Saiba como executar remotamente as tarefas do MapReduce com o Hadoop no HDInsight utilizando Curl.
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: bc6daf37-fcdc-467a-a8a8-6fb2f0f773d1
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 10/03/2017
ms.author: larryfr
ms.openlocfilehash: f6d30b531562e657b3dab2c80637959186a09774
ms.sourcegitcommit: f8437edf5de144b40aed00af5c52a20e35d10ba1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/03/2017
---
# <a name="run-mapreduce-jobs-with-hadoop-on-hdinsight-using-rest"></a>Executar tarefas de MapReduce com o Hadoop no HDInsight com REST

Saiba como utilizar a API de REST de WebHCat para executar tarefas de MapReduce num Hadoop no cluster do HDInsight. Curl é utilizado para demonstrar como pode interagir com o HDInsight através da utilização de pedidos de HTTP não processados para executar tarefas de MapReduce.

> [!NOTE]
> Se já estiver familiarizado com a utilização de servidores de Hadoop baseado em Linux, mas estiver familiarizado com o HDInsight, consulte o [o que precisa de saber sobre o Hadoop baseado em Linux no HDInsight](../hdinsight-hadoop-linux-information.md) documento.


## <a id="prereq"></a>Pré-requisitos

* Um Hadoop no HDInsight cluster
* [Curl](http://curl.haxx.se/)
* [jq](http://stedolan.github.io/jq/)

## <a id="curl"></a>Executar tarefas de MapReduce utilizando Curl

> [!NOTE]
> Quando utilizar Curl ou quaisquer outras comunicações REST com WebHCat, tem de autenticar os pedidos, fornecendo o nome de utilizador do administrador de cluster do HDInsight e a palavra-passe. Tem de utilizar o nome do cluster como parte do URI que é utilizado para enviar pedidos para o servidor.
>
> Para os comandos nesta secção, substitua **admin** com o utilizador para autenticar para o cluster. Substitua **CLUSTERNAME** pelo nome do cluster. Quando solicitado, forneça a palavra-passe da conta de utilizador.
>
> A API de REST está protegida pela utilização [autenticação de acesso básico](http://en.wikipedia.org/wiki/Basic_access_authentication). Deve sempre efetuar pedidos utilizando HTTPS para se certificar de que as suas credenciais são enviadas de forma segura para o servidor.


1. Numa linha de comandos, utilize o seguinte comando para verificar que se consegue ligar ao cluster do HDInsight:

    ```bash
    curl -u admin -G https://CLUSTERNAME.azurehdinsight.net/templeton/v1/status
    ```

    Receberá uma resposta semelhante ao seguinte JSON:

        {"status":"ok","version":"v1"}

    Os parâmetros utilizados neste comando são os seguintes:

   * **-u**: indica o nome de utilizador e palavra-passe utilizada para autenticar o pedido
   * **-G**: indica que esta operação é um pedido GET

   O início do URI, **https://CLUSTERNAME.azurehdinsight.net/templeton/v1**, é o mesmo para todos os pedidos.

2. Para submeter uma tarefa de MapReduce, utilize o seguinte comando:

    ```bash
    curl -u admin -d user.name=admin -d jar=/example/jars/hadoop-mapreduce-examples.jar -d class=wordcount -d arg=/example/data/gutenberg/davinci.txt -d arg=/example/data/CurlOut https://CLUSTERNAME.azurehdinsight.net/templeton/v1/mapreduce/jar
    ```

    O fim do URI (mapreduce/jar) indica WebHCat que este pedido inicia uma tarefa de MapReduce de uma classe num ficheiro jar. Os parâmetros utilizados neste comando são os seguintes:

   * **-d**: `-G` não for utilizado, pelo que o pedido está predefinida para o método POST. `-d`Especifica os valores de dados que são enviados com o pedido.
    * **User.name**: O utilizador que está a executar o comando
    * **JAR**: A localização do ficheiro jar que contém a classe para ser executado
    * **classe**: A classe que contém a lógica de MapReduce
    * **arg**: argumentos transmitido para a tarefa de MapReduce. Neste caso, o ficheiro de texto de entrada e o diretório que são utilizadas para a saída

   Este comando deverá devolver um ID de tarefa que pode ser utilizado para verificar o estado da tarefa:

       {"id":"job_1415651640909_0026"}

3. Para verificar o estado da tarefa, utilize o seguinte comando:

    ```bash
    curl -G -u admin -d user.name=admin https://CLUSTERNAME.azurehdinsight.net/templeton/v1/jobs/JOBID | jq .status.state
    ```

    Substitua o **JOBID** com o valor obtido no passo anterior. Por exemplo, se o valor de retorno foi `{"id":"job_1415651640909_0026"}`, em seguida, o JOBID seria `job_1415651640909_0026`.

    Se a tarefa estiver concluída, o estado devolvido é `SUCCEEDED`.

   > [!NOTE]
   > Este pedido Curl devolve um documento JSON com informações sobre a tarefa. Jq é utilizado para obter o valor de estado.

4. Quando o estado da tarefa foi alterado para `SUCCEEDED`, pode obter os resultados da tarefa do armazenamento de Blobs do Azure. O `statusdir` parâmetro que é transmitido com a consulta contém a localização do ficheiro de saída. Neste exemplo, a localização é `/example/curl`. Este endereço armazena o resultado da tarefa no armazenamento de predefinido de clusters em `/example/curl`.

Pode listar e transferir estes ficheiros utilizando o [Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli). Para obter mais informações sobre como trabalhar com blobs a partir da CLI do Azure, consulte o [utilizando o 2.0 CLI do Azure com o Storage do Azure](../../storage/common/storage-azure-cli.md#create-and-manage-blobs) documento.

## <a id="nextsteps"></a>Passos seguintes

Para obter informações gerais sobre as tarefas do MapReduce no HDInsight:

* [Utilizar o MapReduce com o Hadoop no HDInsight](hdinsight-use-mapreduce.md)

Para obter informações sobre outras formas pode trabalhar com o Hadoop no HDInsight:

* [Utilizar o Hive com o Hadoop no HDInsight](hdinsight-use-hive.md)
* [Utilizar o Pig com o Hadoop no HDInsight](hdinsight-use-pig.md)

Para obter mais informações sobre a interface REST que é utilizada neste artigo, consulte o [WebHCat referência](https://cwiki.apache.org/confluence/display/Hive/WebHCat+Reference).