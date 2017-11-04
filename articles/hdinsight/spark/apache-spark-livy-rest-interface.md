---
title: Utilize o Livy Spark para submeter tarefas spark cluster no Azure HDInsight | Microsoft Docs
description: Saiba como utilizar a API de REST do Apache Spark para submeter tarefas do Spark remotamente a um cluster do Azure HDInsight.
keywords: o Apache spark rest api, livy spark
services: hdinsight
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 2817b779-1594-486b-8759-489379ca907d
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/25/2017
ms.author: nitinme
ms.openlocfilehash: 7b6b551fa1009da744598715b09f13355ded2884
ms.sourcegitcommit: f8437edf5de144b40aed00af5c52a20e35d10ba1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/03/2017
---
# <a name="use-apache-spark-rest-api-to-submit-remote-jobs-to-an-hdinsight-spark-cluster"></a>Utilize a API de REST do Apache Spark para submeter as tarefas remotas para um cluster do Spark do HDInsight

Saiba como utilizar o Livy, a API de REST do Apache Spark, que é utilizado para submeter as tarefas remotas para um cluster do Azure HDInsight Spark. Para obter documentação detalhada, consulte [Livy](https://github.com/cloudera/hue/tree/master/apps/spark/java#welcome-to-livy-the-rest-spark-server).

Pode utilizar o Livy executar Spark interativo shells ou submeter as tarefas de lote para ser executado no Spark. Este artigo aborda com o Livy para submeter tarefas de lote. Os fragmentos neste artigo utilizam cURL para efetuar chamadas de REST API para o ponto final Livy Spark.

**Pré-requisitos:**

* Um cluster do Apache Spark no HDInsight. Para obter instruções, consulte [clusters do Apache Spark criar no Azure HDInsight](apache-spark-jupyter-spark-sql.md).

* [cURL](http://curl.haxx.se/). Este artigo utiliza o cURL para demonstrar como fazer chamadas da REST API em relação a um cluster do HDInsight Spark.

## <a name="submit-a-livy-spark-batch-job"></a>Submeter uma tarefa de lote do Livy Spark
Antes de submeter uma tarefa de lote, tem de carregar a aplicação jar no armazenamento de cluster associado com o cluster. Pode utilizar [ **AzCopy**](../../storage/common/storage-use-azcopy.md), um utilitário da linha de comandos, para o fazer. Existem vários outros clientes, que pode utilizar para carregar dados. Pode encontrar mais informações sobre-las em [carregar dados para tarefas do Hadoop no HDInsight](../hdinsight-upload-data.md).

    curl -k --user "<hdinsight user>:<user password>" -v -H <content-type> -X POST -d '{ "file":"<path to application jar>", "className":"<classname in jar>" }' 'https://<spark_cluster_name>.azurehdinsight.net/livy/batches'

**Exemplos**:

* Se o ficheiro jar no armazenamento de cluster (WASB)
  
        curl -k --user "admin:mypassword1!" -v -H 'Content-Type: application/json' -X POST -d '{ "file":"wasb://mycontainer@mystorageaccount.blob.core.windows.net/data/SparkSimpleTest.jar", "className":"com.microsoft.spark.test.SimpleFile" }' "https://mysparkcluster.azurehdinsight.net/livy/batches"
* Se pretender passar o nome de ficheiro jar e o classname como parte de um ficheiro de entrada (neste exemplo, input.txt)
  
        curl -k  --user "admin:mypassword1!" -v -H "Content-Type: application/json" -X POST --data @C:\Temp\input.txt "https://mysparkcluster.azurehdinsight.net/livy/batches"

## <a name="get-information-on-livy-spark-batches-running-on-the-cluster"></a>Obter informações sobre os lotes de Livy Spark em execução no cluster
    curl -k --user "<hdinsight user>:<user password>" -v -X GET "https://<spark_cluster_name>.azurehdinsight.net/livy/batches"

**Exemplos**:

* Se pretender obter todos os lotes de Livy Spark em execução no cluster:
  
        curl -k --user "admin:mypassword1!" -v -X GET "https://mysparkcluster.azurehdinsight.net/livy/batches"
* Se pretender obter um lote específico com uma determinada batchId
  
        curl -k --user "admin:mypassword1!" -v -X GET "https://mysparkcluster.azurehdinsight.net/livy/batches/{batchId}"

## <a name="delete-a-livy-spark-batch-job"></a>Eliminar uma tarefa de lote do Livy Spark
    curl -k --user "<hdinsight user>:<user password>" -v -X DELETE "https://<spark_cluster_name>.azurehdinsight.net/livy/batches/{batchId}"

**Exemplo**:

    curl -k --user "admin:mypassword1!" -v -X DELETE "https://mysparkcluster.azurehdinsight.net/livy/batches/{batchId}"

## <a name="livy-spark-and-high-availability"></a>O Livy Spark e elevada disponibilidade
O Livy fornece elevada disponibilidade para o Spark tarefas em execução no cluster. Eis alguns exemplos.

* Se o serviço de Livy ficar inativo, depois de ter submetido uma tarefa remota para um cluster do Spark, a tarefa continua em execução em segundo plano. Quando o Livy é uma cópia de segurança, restaura o estado da tarefa e relatórios, fazer uma cópia.
* Blocos de notas do Jupyter para o HDInsight são ligados à corrente por Livy no back-end. Se um bloco de notas está em execução uma tarefa de Spark e o serviço de Livy obtém reiniciado, o bloco de notas continua a executar as células de código. 

## <a name="show-me-an-example"></a>Mostrar um exemplo
Nesta secção, vamos ver exemplos para utilizar o Livy Spark para submeter a tarefa do batch, monitorizar o progresso da tarefa e, em seguida, eliminá-lo. A aplicação que iremos utilizar neste exemplo é o desenvolvidos no artigo [criar um autónomo Scala aplicação e para executar no cluster do HDInsight Spark](apache-spark-create-standalone-application.md). Os passos aqui descritos partem do princípio de que:

* Já tem copiadas jar a aplicação para a conta de armazenamento associada com o cluster.
* Ter CuRL instalado no computador onde está a tentar estes passos.

Execute os seguintes passos:

1. Informe-nos primeiro certifique-se de que o Livy Spark está em execução no cluster. Pode fazemo-lo por obter uma lista da execução de lotes. Se estiver a executar uma tarefa com o Livy pela primeira vez, a saída deverá devolver zero.
   
        curl -k --user "admin:mypassword1!" -v -X GET "https://mysparkcluster.azurehdinsight.net/livy/batches"
   
    Deve obter um resultado semelhante ao seguinte fragmento:
   
        < HTTP/1.1 200 OK
        < Content-Type: application/json; charset=UTF-8
        < Server: Microsoft-IIS/8.5
        < X-Powered-By: ARR/2.5
        < X-Powered-By: ASP.NET
        < Date: Fri, 20 Nov 2015 23:47:53 GMT
        < Content-Length: 34
        <
        {"from":0,"total":0,"sessions":[]}* Connection #0 to host mysparkcluster.azurehdinsight.net left intact
   
    Repare como a última linha na saída indica **total: 0**, sugere que não existem lotes em execução.

2. Informe-nos agora submeta uma tarefa de lote. O fragmento seguinte utiliza um ficheiro de entrada (input.txt) para o nome de jar e o nome da classe de passar como parâmetros. Se estiver a executar estes passos de um computador Windows, utilizando um ficheiro de entrada é a abordagem recomendada.
   
        curl -k --user "admin:mypassword1!" -v -H "Content-Type: application/json" -X POST --data @C:\Temp\input.txt "https://mysparkcluster.azurehdinsight.net/livy/batches"
   
    Os parâmetros no ficheiro **input.txt** são definidos do seguinte modo:
   
        { "file":"wasb:///example/jars/SparkSimpleApp.jar", "className":"com.microsoft.spark.example.WasbIOTest" }
   
    Deverá ver um resultado semelhante ao seguinte fragmento:
   
        < HTTP/1.1 201 Created
        < Content-Type: application/json; charset=UTF-8
        < Location: /0
        < Server: Microsoft-IIS/8.5
        < X-Powered-By: ARR/2.5
        < X-Powered-By: ASP.NET
        < Date: Fri, 20 Nov 2015 23:51:30 GMT
        < Content-Length: 36
        <
        {"id":0,"state":"starting","log":[]}* Connection #0 to host mysparkcluster.azurehdinsight.net left intact
   
    Repare como indica a última linha do resultado **Estado: a iniciar**. Também indica **id: 0**. Aqui, **0** é o ID de lote.

3. Agora pode obter o estado deste batch específico utilizando o ID de lote.
   
        curl -k --user "admin:mypassword1!" -v -X GET "https://mysparkcluster.azurehdinsight.net/livy/batches/0"
   
    Deverá ver um resultado semelhante ao seguinte fragmento:
   
        < HTTP/1.1 200 OK
        < Content-Type: application/json; charset=UTF-8
        < Server: Microsoft-IIS/8.5
        < X-Powered-By: ARR/2.5
        < X-Powered-By: ASP.NET
        < Date: Fri, 20 Nov 2015 23:54:42 GMT
        < Content-Length: 509
        <
        {"id":0,"state":"success","log":["\t diagnostics: N/A","\t ApplicationMaster host: 10.0.0.4","\t ApplicationMaster RPC port: 0","\t queue: default","\t start time: 1448063505350","\t final status: SUCCEEDED","\t tracking URL: http://hn0-myspar.lpel1gnnvxne3gwzqkfq5u5uzh.jx.internal.cloudapp.net:8088/proxy/application_1447984474852_0002/","\t user: root","15/11/20 23:52:47 INFO Utils: Shutdown hook called","15/11/20 23:52:47 INFO Utils: Deleting directory /tmp/spark-b72cd2bf-280b-4c57-8ceb-9e3e69ac7d0c"]}* Connection #0 to host mysparkcluster.azurehdinsight.net left intact
   
    Mostra o momento de saída **Estado: êxito**, que sugere que a tarefa foi concluída com êxito.

4. Se quiser, pode agora eliminar o batch.
   
        curl -k --user "admin:mypassword1!" -v -X DELETE "https://mysparkcluster.azurehdinsight.net/livy/batches/0"
   
    Deverá ver um resultado semelhante ao seguinte fragmento:
   
        < HTTP/1.1 200 OK
        < Content-Type: application/json; charset=UTF-8
        < Server: Microsoft-IIS/8.5
        < X-Powered-By: ARR/2.5
        < X-Powered-By: ASP.NET
        < Date: Sat, 21 Nov 2015 18:51:54 GMT
        < Content-Length: 17
        <
        {"msg":"deleted"}* Connection #0 to host mysparkcluster.azurehdinsight.net left intact
   
    A última linha do resultado mostra que o batch foi eliminado com êxito. Também eliminar uma tarefa, enquanto está em execução, inutilizam a tarefa. Se eliminar uma tarefa que foi concluída com êxito ou caso contrário, elimina as informações de tarefa completamente.

## <a name="using-livy-spark-on-hdinsight-35-clusters"></a>Utilizar o Livy Spark nos clusters do HDInsight 3.5

Cluster de HDInsight 3.5, por predefinição, desativa a utilização de caminhos de ficheiro local para acesso aos ficheiros de dados de exemplo ou v7. Aconselhamo-lo para utilizar o `wasb://` caminho em vez disso aceder a v7 ou dados de exemplo de ficheiros do cluster. Se pretender utilizar o caminho local, tem de atualizar a configuração do Ambari em conformidade. Para tal:

1. Aceda ao portal do Ambari para o cluster. IU da Web do Ambari está disponível no seu cluster do HDInsight em https://**CLUSTERNAME**. azurehdidnsight.net, em que CLUSTERNAME é o nome do cluster.

2. No painel de navegação esquerdo, clique em **Livy**e, em seguida, clique em **folhas**.

3. Em **livy predefinido** adicionar o nome da propriedade `livy.file.local-dir-whitelist` e defina o respetivo valor para **"/"** se pretende permitir o acesso total ao sistema de ficheiros. Se pretender permitir o acesso apenas a um diretório específico, forneça o caminho para diretório como o valor.

## <a name="submitting-livy-jobs-for-a-cluster-within-an-azure-virtual-network"></a>Submeter tarefas Livy para um cluster dentro de uma rede virtual do Azure

Se ligar a um cluster Spark do HDInsight a partir de uma rede Virtual do Azure, pode ligar diretamente ao Livy no cluster. Nesse caso, o URL para o ponto final de Livy é `http://<IP address of the headnode>:8998/batches`. Aqui, **8998** é a porta em que o Livy é executado no headnode cluster. Para obter mais informações sobre a aceder aos serviços em portas não público, consulte [portas utilizadas pelos serviços do Hadoop no HDInsight](../hdinsight-hadoop-port-settings-for-services.md).

## <a name="troubleshooting"></a>Resolução de problemas

Seguem-se alguns problemas que possam ser executadas com o Livy para submissão de trabalho remoto para os clusters do Spark.

### <a name="using-an-external-jar-from-the-additional-storage-is-not-supported"></a>Não é suportado utilizar um jar externo do armazenamento adicional

**Problema:** se a tarefa de Livy Spark referencia um jar externo da conta do storage adicionais associada com o cluster, a tarefa falha.

**Resolução:** Certifique-se de que o jar que pretende utilizar está disponível no armazenamento de predefinido associado o cluster do HDInsight.





## <a name="next-step"></a>Passo seguinte

* [Gerir recursos para o cluster do Apache Spark no Azure HDInsight](apache-spark-resource-manager.md)
* [Controlar e depurar tarefas em execução num cluster do Apache Spark do HDInsight](apache-spark-job-debugging.md)

