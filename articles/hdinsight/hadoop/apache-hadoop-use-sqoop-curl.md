---
title: Utilizar o Sqoop de Hadoop com Curl no HDInsight - Azure | Microsoft Docs
description: Saiba como remotamente submeter tarefas de Sqoop para o HDInsight utilizando Curl.
services: hdinsight
documentationcenter: 
author: mumian
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 39798321-78ca-428c-bcfe-322e49af4059
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 09/22/2017
ms.author: jgao
ms.openlocfilehash: add96267f8afe6dcaf43e163f9d18bd7fb57682e
ms.sourcegitcommit: f8437edf5de144b40aed00af5c52a20e35d10ba1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/03/2017
---
# <a name="run-sqoop-jobs-with-hadoop-in-hdinsight-with-curl"></a>Executar tarefas de Sqoop com o Hadoop no HDInsight com o Curl
[!INCLUDE [sqoop-selector](../../../includes/hdinsight-selector-use-sqoop.md)]

Saiba como utilizar Curl para executar tarefas de Sqoop num cluster de Hadoop no HDInsight.

Curl é utilizado para demonstrar como pode interagir com o HDInsight através de pedidos de HTTP não processados para executar, monitorizar e obter os resultados das tarefas de Sqoop. Isto funciona, utilizando a API de REST do WebHCat (anteriormente conhecido como Templeton) fornecida pelo cluster do HDInsight.

## <a name="prerequisites"></a>Pré-requisitos
Para concluir os passos neste artigo, irá precisar do seguinte:

* Completa [utilize Sqoop com o Hadoop no HDInsight](hdinsight-use-sqoop.md#create-cluster-and-sql-database) para configurar um ambiente com um cluster do HDInsight e uma base de dados SQL do Azure.
* [Curl](http://curl.haxx.se/). Curl é uma ferramenta de transferência de dados de ou para um cluster do HDInsight.
* [jq](http://stedolan.github.io/jq/). O utilitário de jq é utilizado para processar os dados JSON devolvidos de pedidos REST.

## <a name="submit-sqoop-jobs-by-using-curl"></a>Submeter tarefas de Sqoop utilizando Curl
> [!NOTE]
> Quando utilizar Curl ou quaisquer outras comunicações REST com WebHCat, tem de autenticar os pedidos, indicando o nome de utilizador e palavra-passe para o administrador de cluster do HDInsight. Também tem de utilizar o nome do cluster como parte do identificador URI (Uniform Resource Identifier) utilizado para enviar os pedidos para o servidor.
> 
> Para os comandos nesta secção, substitua **USERNAME** pelo utilizador para autenticar-se o cluster e substitua **PASSWORD** pela palavra-passe da conta de utilizador. Substitua **CLUSTERNAME** pelo nome do cluster.
> 
> A API de REST está protegida por [autenticação básica](http://en.wikipedia.org/wiki/Basic_access_authentication). Deve sempre efetuar pedidos utilizando HTTP Secure (HTTPS) para ajudar a garantir que as credenciais são enviadas de forma segura para o servidor.
> 
> 

1. Numa linha de comandos, utilize o seguinte comando para verificar que se consegue ligar ao cluster do HDInsight:

    ```bash   
    curl -u USERNAME:PASSWORD -G https://CLUSTERNAME.azurehdinsight.net/templeton/v1/status
    ```

    Deverá receber uma resposta semelhante ao seguinte:

    ```json   
    {"status":"ok","version":"v1"}
    ```
   
    Os parâmetros utilizados neste comando são os seguintes:
   
   * **-u** -o nome de utilizador e palavra-passe utilizada para autenticar o pedido.
   * **-G** -indica que se trata de um pedido GET.
     
     O início do URL, **https://CLUSTERNAME.azurehdinsight.net/templeton/v1**, é o mesmo para todos os pedidos. O caminho, **/status**, indica que o pedido está a devolver um Estado de WebHCat (também conhecido como Templeton) para o servidor. 
2. Utilize o seguinte para submeter uma tarefa de sqoop:

    ```bash
    curl -u USERNAME:PASSWORD -d user.name=USERNAME -d command="export --connect jdbc:sqlserver://SQLDATABASESERVERNAME.database.windows.net;user=USERNAME@SQLDATABASESERVERNAME;password=PASSWORD;database=SQLDATABASENAME --table log4jlogs --export-dir /example/data/sample.log --input-fields-terminated-by \0x20 -m 1" -d statusdir="wasb:///example/data/sqoop/curl" https://CLUSTERNAME.azurehdinsight.net/templeton/v1/sqoop
    ```

    Os parâmetros utilizados neste comando são os seguintes:

    * **-d** - desde `-G` não for utilizado, o pedido está predefinida para o método POST. `-d`Especifica os valores de dados que são enviados com o pedido.

        * **User.name** -o utilizador que está a executar o comando.

        * **comando** -Sqoop o comando a executar.

        * **statusdir** -o diretório que o estado de tarefa será escrito.

    Este comando deverá devolver um ID de tarefa que pode ser utilizado para verificar o estado da tarefa.

        ```json
        {"id":"job_1415651640909_0026"}
        ```

3. Para verificar o estado da tarefa, utilize o seguinte comando. Substitua **JOBID** com o valor obtido no passo anterior. Por exemplo, se o valor de retorno foi `{"id":"job_1415651640909_0026"}`, em seguida, **JOBID** seria `job_1415651640909_0026`.

    ```bash
    curl -G -u USERNAME:PASSWORD -d user.name=USERNAME https://CLUSTERNAME.azurehdinsight.net/templeton/v1/jobs/JOBID | jq .status.state
    ```

    Se a tarefa estiver concluída, o estado será **com êxito**.
   
   > [!NOTE]
   > Este pedido Curl devolve um documento de JavaScript Object Notation (JSON) com informações sobre a tarefa; jq é utilizado para obter o valor de estado.
   > 
   > 
4. Depois do Estado da tarefa foi alterado para **com êxito**, pode obter os resultados da tarefa do armazenamento de Blobs do Azure. O `statusdir` parâmetro transmitido com a consulta contém a localização do ficheiro de saída; neste caso, **wasb: / / / exemplo/dados/sqoop/curl**. Este endereço armazena o resultado da tarefa no **exemplo/dados/sqoop/curl** diretório no contentor de armazenamento predefinido utilizado pelo cluster do HDInsight.
   
    Pode utilizar o portal do Azure para aceder a blobs stderr e stdout.  Também pode utilizar o Microsoft SQL Server Management Studio para verificar os dados que são carregados para a tabela de log4jlogs.

## <a name="limitations"></a>Limitações
* Exportação em massa - baseado em Linux com o HDInsight, o conector de Sqoop utilizado para exportar dados para o Microsoft SQL Server ou SQL Database do Azure não suporta atualmente inserções em massa.
* Criação de batches - com o HDInsight baseado em Linux, ao utilizar o `-batch` comutador quando efetuar inserções, Sqoop irá efetuar várias inserções em vez das operações de inserção de criação de batches.

## <a name="summary"></a>Resumo
Como é demonstrado neste documento, pode utilizar um pedido HTTP não processado para executar, monitorizar e ver os resultados das tarefas de Sqoop no cluster do HDInsight.

Para obter mais informações sobre a interface REST utilizada neste artigo, consulte o <a href="https://sqoop.apache.org/docs/1.99.3/RESTAPI.html" target="_blank">guia da API de REST Sqoop</a>.

## <a name="next-steps"></a>Passos seguintes
Para obter informações gerais sobre o Hive com o HDInsight:

* [Utilizar o Sqoop com o Hadoop no HDInsight](hdinsight-use-sqoop.md)

Para obter informações sobre outras formas pode trabalhar com o Hadoop no HDInsight:

* [Utilizar o Hive com o Hadoop no HDInsight](hdinsight-use-hive.md)
* [Utilizar o Pig com o Hadoop no HDInsight](hdinsight-use-pig.md)
* [Utilizar o MapReduce com o Hadoop no HDInsight](hdinsight-use-mapreduce.md)

Para outro HDInsight artigos que envolvem curl:
 
* [Criar clusters do Hadoop, utilizando a API de REST do Azure](../hdinsight-hadoop-create-linux-clusters-curl-rest.md)
* [Executar consultas do Hive com o Hadoop no HDInsight com REST](apache-hadoop-use-hive-curl.md)
* [Executar tarefas de MapReduce com o Hadoop no HDInsight com REST](apache-hadoop-use-mapreduce-curl.md)
* [Executar tarefas do Pig com o Hadoop no HDInsight utilizando cURL](apache-hadoop-use-pig-curl.md)



