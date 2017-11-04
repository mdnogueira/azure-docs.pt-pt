---
title: Utilizar o Hive do Hadoop com Curl no HDInsight - Azure | Microsoft Docs
description: Saiba como remotamente submeter tarefas do Pig para o HDInsight utilizando Curl.
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 6ce18163-63b5-4df6-9bb6-8fcbd4db05d8
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 10/03/2017
ms.author: larryfr
ms.openlocfilehash: fd35020c00852f2119a3ba2356a08ff0c2093c04
ms.sourcegitcommit: f8437edf5de144b40aed00af5c52a20e35d10ba1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/03/2017
---
# <a name="run-hive-queries-with-hadoop-in-hdinsight-using-rest"></a>Executar consultas do Hive com o Hadoop no HDInsight com REST

[!INCLUDE [hive-selector](../../../includes/hdinsight-selector-use-hive.md)]

Saiba como utilizar a API de REST de WebHCat para executar consultas do Hive com o Hadoop no Azure HDInsight cluster.

[Curl](http://curl.haxx.se/) é utilizado para demonstrar como pode interagir com o HDInsight através da utilização de pedidos de HTTP não processados. O [jq](http://stedolan.github.io/jq/) utilitário é utilizado para processar os dados JSON devolvidos de pedidos REST.

> [!NOTE]
> Se já estiver familiarizado com a utilização de servidores de Hadoop baseado em Linux, mas se estiver familiarizado com o HDInsight, consulte o [o que precisa de saber sobre o Hadoop no HDInsight baseado em Linux](../hdinsight-hadoop-linux-information.md) documento.

## <a id="curl"></a>Executar consultas do Hive

> [!NOTE]
> Quando utilizar cURL ou quaisquer outras comunicações REST com WebHCat, tem de autenticar os pedidos fornecendo um nome de utilizador e palavra-passe para o administrador de cluster do HDInsight.
>
> Para os comandos nesta secção, substitua **admin** com o utilizador para autenticar para o cluster. Substitua **CLUSTERNAME** pelo nome do cluster. Quando lhe for pedido, introduza a palavra-passe da conta de utilizador.
>
> A API de REST está protegida por [autenticação básica](http://en.wikipedia.org/wiki/Basic_access_authentication). Para ajudar a garantir que as suas credenciais são enviadas de forma segura para o servidor, sempre efetue pedidos utilizando HTTP Secure (HTTPS).

1. Numa linha de comandos, utilize o seguinte comando para verificar que se consegue ligar ao cluster do HDInsight:

    ```bash
    curl -u admin -G https://CLUSTERNAME.azurehdinsight.net/templeton/v1/status
    ```

    Receberá uma resposta semelhante para o seguinte texto:

        {"status":"ok","version":"v1"}

    Os parâmetros utilizados neste comando são os seguintes:

    * **-u** -o nome de utilizador e palavra-passe utilizada para autenticar o pedido.
    * **-G** -indica que este pedido é uma operação GET.

   O início do URL, **https://CLUSTERNAME.azurehdinsight.net/templeton/v1**, é o mesmo para todos os pedidos. O caminho, **/status**, indica que o pedido está a devolver um Estado de WebHCat (também conhecido como Templeton) para o servidor. Também pode solicitar a versão do ramo de registo utilizando o seguinte comando:

    ```bash
    curl -u admin -G https://CLUSTERNAME.azurehdinsight.net/templeton/v1/version/hive
    ```

    Este pedido devolve uma resposta semelhante ao seguinte texto:

        {"module":"hive","version":"0.13.0.2.1.6.0-2103"}

2. Utilize o seguinte para criar uma tabela com o nome **log4jLogs**:

    ```bash
    curl -u admin -d user.name=admin -d execute="set+hive.execution.engine=tez;DROP+TABLE+log4jLogs;CREATE+EXTERNAL+TABLE+log4jLogs(t1+string,t2+string,t3+string,t4+string,t5+string,t6+string,t7+string)+ROW+FORMAT+DELIMITED+FIELDS+TERMINATED+BY+' '+STORED+AS+TEXTFILE+LOCATION+'/example/data/';SELECT+t4+AS+sev,COUNT(*)+AS+count+FROM+log4jLogs+WHERE+t4+=+'[ERROR]'+AND+INPUT__FILE__NAME+LIKE+'%25.log'+GROUP+BY+t4;" -d statusdir="/example/curl" https://CLUSTERNAME.azurehdinsight.net/templeton/v1/hive
    ```

    Os seguintes parâmetros utilizados com este pedido:

   * **-d** - desde `-G` não for utilizado, o pedido está predefinida para o método POST. `-d`Especifica os valores de dados que são enviados com o pedido.

     * **User.name** -o utilizador que está a executar o comando.
     * **executar** -declarações HiveQL a executar.
     * **statusdir** -o diretório que o estado desta tarefa é escrito.

   As declarações de efetuar as seguintes ações:
   
   * **LARGAR a tabela** -se a tabela já existir, é eliminado.
   * **Criar tabela externa** -cria uma nova tabela 'externa' no ramo de registo. As tabelas externas armazenam a definição de tabela no ramo de registo. Os dados for deixados na localização original.

     > [!NOTE]
     > As tabelas externas devem ser utilizadas ao espera os dados subjacentes ser atualizados por uma origem externa. Por exemplo, um processo de carregamento de dados automática ou outra operação de MapReduce.
     >
     > Remover uma tabela externa **não** eliminar os dados, a definição de tabela.

   * **FORMATO de linha** - a forma como os dados que estão formatados. Os campos no registo de cada são separados por um espaço.
   * **LOCALIZAÇÃO de TEXTFILE AS ARMAZENADOS** - armazenar os dados (o diretório de dados de exemplo /) e de que esta está armazenada como texto.
   * **SELECIONE** -seleciona uma contagem de todas as linhas onde coluna **t4** contém o valor **[erro]**. Esta declaração devolve um valor **3** porque existem três linhas que contêm este valor.

     > [!NOTE]
     > Tenha em atenção que os espaços entre declarações HiveQL são substituídos pelo `+` caráter quando utilizado com Curl. Delimitado por aspas valores que contêm um espaço, tais como o delimitador, não devem ser substituídos pelo `+`.

   * **INPUT__FILE__NAME como '% 25.log'** -esta instrução limita a pesquisa para utilizar apenas os ficheiros que termina em. registo.

     > [!NOTE]
     > O `%25` é o formato de URL codificado %, pelo que a condição real é `like '%.log'`. O % tem de ser o URL codificado, como esta é tratada como um caráter especial no URL.

   Este comando devolve um ID de tarefa que pode ser utilizado para verificar o estado da tarefa.

       {"id":"job_1415651640909_0026"}

3. Para verificar o estado da tarefa, utilize o seguinte comando:

    ```bash
    curl -G -u admin -d user.name=admin https://CLUSTERNAME.azurehdinsight.net/templeton/v1/jobs/JOBID | jq .status.state
    ```

    Substitua **JOBID** com o valor obtido no passo anterior. Por exemplo, se o valor de retorno foi `{"id":"job_1415651640909_0026"}`, em seguida, **JOBID** seria `job_1415651640909_0026`.

    Se a tarefa estiver concluída, o estado é **com êxito**.

   > [!NOTE]
   > Este pedido Curl devolve um documento de JavaScript Object Notation (JSON) com informações sobre a tarefa. Jq é utilizado para obter o valor de estado.

4. Depois do Estado da tarefa foi alterado para **com êxito**, pode obter os resultados da tarefa do armazenamento de Blobs do Azure. O `statusdir` parâmetro transmitido com a consulta contém a localização do ficheiro de saída; neste caso, **/exemplo/curl**. Este endereço armazena os resultados no **exemplo/curl** diretório no armazenamento de predefinido de clusters.

    Pode listar e transferir estes ficheiros utilizando o [CLI do Azure](https://docs.microsoft.com/en-us/cli/azure/install-azure-cli). Para obter mais informações sobre como utilizar a CLI do Azure com o Storage do Azure, consulte o [utilização do Azure CLI 2.0 com armazenamento do Azure](https://docs.microsoft.com/en-us/azure/storage/storage-azure-cli#create-and-manage-blobs) documento.

5. Utilize as seguintes instruções para criar uma nova tabela 'interna' com o nome **foram**:

    ```bash
    curl -u admin -d user.name=admin -d execute="set+hive.execution.engine=tez;CREATE+TABLE+IF+NOT+EXISTS+errorLogs(t1+string,t2+string,t3+string,t4+string,t5+string,t6+string,t7+string)+STORED+AS+ORC;INSERT+OVERWRITE+TABLE+errorLogs+SELECT+t1,t2,t3,t4,t5,t6,t7+FROM+log4jLogs+WHERE+t4+=+'[ERROR]'+AND+INPUT__FILE__NAME+LIKE+'%25.log';SELECT+*+from+errorLogs;" -d statusdir="/example/curl" https://CLUSTERNAME.azurehdinsight.net/templeton/v1/hive
    ```

    As declarações de efetuar as seguintes ações:

   * **Criar tabela não se existe** -cria uma tabela, se já existir. Esta instrução cria uma tabela interna, o que é armazenada no armazém de dados do Hive. Esta tabela é gerida pelo ramo de registo.

     > [!NOTE]
     > Ao contrário das tabelas externas, remover uma tabela interna elimina dados subjacentes.

   * **ARMAZENADOS ORC de AS** -armazena os dados no formato otimizada linha Columnar (ORC). ORC é um formato altamente otimizado e eficiente para armazenar dados do Hive.
   * **SUBSTITUIR INSERT... SELECIONE** -seleciona as linhas do **log4jLogs** tabela que contenham **[erro]**, insere, em seguida, os dados para o **foram** tabela.
   * **SELECIONE** -seleciona todas as linhas da nova **foram** tabela.

6. Utilize o ID da tarefa devolvido para verificar o estado da tarefa. Assim que foi bem sucedida, utilize a CLI do Azure, tal como descrito anteriormente para transferir e ver os resultados. O resultado deve conter três linhas, que contêm **[erro]**.

## <a id="nextsteps"></a>Passos seguintes

Para obter informações gerais sobre o Hive com o HDInsight:

* [Utilizar o Hive com o Hadoop no HDInsight](hdinsight-use-hive.md)

Para obter informações sobre outras formas pode trabalhar com o Hadoop no HDInsight:

* [Utilizar o Pig com o Hadoop no HDInsight](hdinsight-use-pig.md)
* [Utilizar o MapReduce com o Hadoop no HDInsight](hdinsight-use-mapreduce.md)

Se estiver a utilizar com o Hive no Tez, consulte os seguintes documentos para obter informações de depuração:

* [Utilize a vista Ambari Tez no HDInsight baseado em Linux](../hdinsight-debug-ambari-tez-view.md)

Para obter mais informações sobre a API de REST utilizado neste documento, consulte o [WebHCat referência](https://cwiki.apache.org/confluence/display/Hive/WebHCat+Reference) documento.

[hdinsight-sdk-documentation]: http://msdnstage.redmond.corp.microsoft.com/library/dn479185.aspx

[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[apache-tez]: http://tez.apache.org
[apache-hive]: http://hive.apache.org/
[apache-log4j]: http://en.wikipedia.org/wiki/Log4j
[hive-on-tez-wiki]: https://cwiki.apache.org/confluence/display/Hive/Hive+on+Tez
[import-to-excel]: http://azure.microsoft.com/documentation/articles/hdinsight-connect-excel-power-query/


[hdinsight-use-oozie]: hdinsight-use-oozie.md
[hdinsight-analyze-flight-data]: hdinsight-analyze-flight-delay-data.md




[hdinsight-provision]: hdinsight-hadoop-provision-linux-clusters.md
[hdinsight-submit-jobs]:submit-apache-hadoop-jobs-programmatically.md
[hdinsight-upload-data]: hdinsight-upload-data.md

[powershell-here-strings]: http://technet.microsoft.com/library/ee692792.aspx


