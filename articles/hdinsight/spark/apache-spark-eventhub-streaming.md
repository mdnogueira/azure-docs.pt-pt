---
title: "Utilizar o Apache Spark de transmissão em fluxo com os Event Hubs no Azure HDInsight | Microsoft Docs"
description: "Crie um exemplo sobre como enviar um fluxo de dados para o Hub de eventos do Azure e, em seguida, receber esses eventos num cluster do Spark do HDInsight utilizando uma aplicação scala de transmissão em fluxo do Apache Spark."
keywords: "Apache spark de transmissão em fluxo, transmissão em fluxo do spark, amostra do spark, o apache spark transmissão em fluxo de exemplo, o exemplo do event hub do azure, o exemplo do spark"
services: hdinsight
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 68894e75-3ffa-47bd-8982-96cdad38b7d0
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/28/2017
ms.author: nitinme
ms.openlocfilehash: d0678388fea79797c3cb4cd84deeab827981ebff
ms.sourcegitcommit: 93902ffcb7c8550dcb65a2a5e711919bd1d09df9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/09/2017
---
# <a name="apache-spark-streaming-process-data-from-azure-event-hubs-with-spark-cluster-on-hdinsight"></a>Transmissão em fluxo do Apache Spark: cluster de processamento de dados provenientes dos Hubs de eventos do Azure com o Spark no HDInsight

Neste artigo, crie uma amostra que envolve os seguintes passos de transmissão em fluxo do Apache Spark:

1. Utilizar uma aplicação autónoma para a ingestão de mensagens para um Hub de eventos do Azure.

2. Com duas abordagens diferentes, é possível obter as mensagens do Hub de eventos em tempo real, utilizando uma aplicação em execução no cluster do Spark no Azure HDInsight.

3. Pode criar pipelines de análise para manter os dados para os sistemas de armazenamento diferente de transmissão em fluxo ou obter informações a partir dos dados no momento.

## <a name="prerequisites"></a>Pré-requisitos

* Uma subscrição do Azure. Consulte [Obter uma avaliação gratuita do Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

* Um cluster do Apache Spark no HDInsight. Para obter instruções, consulte [clusters do Apache Spark criar no Azure HDInsight](apache-spark-jupyter-spark-sql.md).

## <a name="spark-streaming-concepts"></a>Conceitos de transmissão em fluxo do Spark

Para obter uma explicação detalhada de transmissão em fluxo do Spark, consulte [Apache Spark descrição geral de transmissão em fluxo](http://spark.apache.org/docs/latest/streaming-programming-guide.html#overview). HDInsight oferece as mesmas funcionalidades de transmissão em fluxo para um cluster do Spark no Azure.  

## <a name="what-does-this-solution-do"></a>O que faz esta solução?

Neste artigo, para criar um exemplo de transmissão em fluxo o Spark, execute os seguintes passos:

1. Crie um Hub de eventos do Azure que irá receber um fluxo de eventos.

2. Execute uma aplicação autónoma local que gera eventos e envia-o para o Hub de eventos do Azure. A aplicação de exemplo efetua este procedimento é publicada no [https://github.com/hdinsight/spark-streaming-data-persistence-examples](https://github.com/hdinsight/spark-streaming-data-persistence-examples).

3. Executar uma aplicação de transmissão em fluxo remotamente num cluster Spark que lê os eventos de transmissão em fluxo a partir do Hub de eventos do Azure e efetuar várias/análise de processamento de dados.

## <a name="create-an-azure-event-hub"></a>Criar um Hub de eventos do Azure

1. Inicie sessão no [Portal do Azure](https://ms.portal.azure.com)e clique em **novo** na parte superior esquerda do ecrã.

2. Clique em **Internet das Coisas** e, em seguida, clique em **Hubs de eventos**.

    ![Criar o hub de eventos de exemplo de transmissão em fluxo do Spark](./media/apache-spark-eventhub-streaming/hdinsight-create-event-hub-for-spark-streaming.png "criar o hub de eventos de exemplo de transmissão em fluxo do Spark")

3. No painel **Criar espaço de nomes**, introduza um nome de espaço de nomes. Escolha o escalão de preço (básico ou Standard). Escolha também uma subscrição do Azure, um grupo de recursos e a localização na qual pretende criar o recurso. Clique em **Criar** para criar o espaço de nome.

      ![Forneça um nome de hub de eventos para o exemplo de transmissão em fluxo do Spark](./media/apache-spark-eventhub-streaming/hdinsight-provide-event-hub-name-for-spark-streaming.png "fornecer um nome de hub de eventos para o exemplo de transmissão em fluxo do Spark")

    > [!NOTE]
    > Deve selecionar os mesmos **localização** do seu cluster do Apache Spark no HDInsight para reduzir os custos e latência.
    >
    >

4. Na lista de espaços de nomes dos Hubs de Eventos, clique no espaço de nome criado recentemente.      


5. No painel do espaço de nomes, clique em **Event Hubs**e, em seguida, clique em **+ Hub de eventos** para criar um novo Hub de eventos.
   
    ![Criar o hub de eventos de exemplo de transmissão em fluxo do Spark](./media/apache-spark-eventhub-streaming/hdinsight-open-event-hubs-blade-for-spark-streaming-example.png "criar o hub de eventos de exemplo de transmissão em fluxo do Spark")

6. Escreva um nome para o Hub de eventos, defina o número de partição para 10 e retenção de mensagem para 1. Não podemos são arquivar as mensagens nesta solução para que possa deixe as restantes como a predefinição e, em seguida, clique em **criar**.
   
    ![Forneça detalhes do hub de eventos para o exemplo de transmissão em fluxo do Spark](./media/apache-spark-eventhub-streaming/hdinsight-provide-event-hub-details-for-spark-streaming-example.png "fornecem detalhes de hub de eventos de exemplo de transmissão em fluxo do Spark")

7. O Hub de eventos recentemente criado é listado no painel do Hub de eventos.
    
     ![Ver o Hub de eventos para o Spark exemplo de transmissão em fluxo](./media/apache-spark-eventhub-streaming/hdinsight-view-event-hub-for-spark-streaming-example.png "Hub de eventos de vista para o Spark exemplo de transmissão em fluxo")

8. No painel de espaço de nomes (não no painel de Hub de Eventos específico), clique em **Políticas de acesso partilhado**, e, em seguida, clique em **RootManageSharedAccessKey**.
    
     ![Definir políticas de Hub de eventos para o Spark exemplo de transmissão em fluxo](./media/apache-spark-eventhub-streaming/hdinsight-set-event-hub-policies-for-spark-streaming-example.png "políticas de definir o Hub de eventos para o Spark exemplo de transmissão em fluxo")

9. Clique no botão Copiar para copiar o **RootManageSharedAccessKey** cadeia de ligação e a chave primária para a área de transferência. Guarde estes a utilizar mais tarde no tutorial.
    
     ![Ver as chaves de política de Hub de eventos para o Spark exemplo de transmissão em fluxo](./media/apache-spark-eventhub-streaming/hdinsight-view-event-hub-policy-keys.png "política de Hub de eventos de vista de chaves para o exemplo de transmissão em fluxo do Spark")

## <a name="send-messages-to-azure-event-hub-using-a-sample-scala-application"></a>Enviar mensagens para o Hub de eventos do Azure utilizando um exemplo de aplicação Scala

Esta secção utiliza uma local Scala aplicação autónoma que gera um fluxo de eventos e envia-a para o Hub de eventos do Azure que criou anteriormente. Esta aplicação está disponível no GitHub em [https://github.com/hdinsight/eventhubs-sample-event-producer](https://github.com/hdinsight/eventhubs-sample-event-producer). Os passos aqui descritos partem do princípio de que já tenha escolhido este repositório do GitHub.

1. Certifique-se de que tem o seguinte instalado no computador em que executar esta aplicação.

    * Kit de desenvolvimento Java Oracle. Pode instalar a [aqui](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html).
    * Apache Maven. Poderá transferi-lo de [aqui](https://maven.apache.org/download.cgi). Estão disponíveis instruções para instalar o Maven [aqui](https://maven.apache.org/install.html).

2. Abra uma linha de comandos e navegue para a localização clonou o repositório do GitHub para o exemplo de aplicação Scala e execute o seguinte comando para criar a aplicação.

        mvn package

3. O jar de saída para a aplicação, **com-microsoft-azure-eventhubs-client-example-0.2.0.jar**, é criado no **/de destino** diretório. Utilize este JAR neste artigo para testar a solução completa.

## <a name="create-application-to-receive-messages-from-event-hub-into-a-spark-cluster"></a>Criar aplicação para receber mensagens de Hub de eventos para um cluster do Spark 

Temos duas abordagens para ligar a transmissão em fluxo do Spark e Event Hubs do Azure, com base em Recetor ligação e ligação direta DStream baseada em. Direta DStream baseada em é apresentada em Janeiro de 2017, no 2.0.3 versão. É deveria para substituir a ligação original baseado no recetor porque esta está mais performant e eficiente para o recurso. Obter mais detalhes no [https://github.com/hdinsight/spark-eventhubs](https://github.com/hdinsight/spark-eventhubs). Direto DStream só suporta Spark 2.0 +.

### <a name="build-applications-with-the-dependency-to-spark-eventhubs-connector"></a>Criar aplicações com a dependência para o conector do spark eventhubs

Também vamos publicar a versão de teste do Spark EventHubs no GitHub. Para utilizar a versão de teste do Spark EventHubs, o primeiro passo é indicar o GitHub como o repositório de origem, adicionando a seguinte entrada para pom.xml:

```xml
<repository>
      <id>spark-eventhubs</id>
      <url>https://raw.github.com/hdinsight/spark-eventhubs/maven-repo/</url>
      <snapshots>
        <enabled>true</enabled>
        <updatePolicy>always</updatePolicy>
      </snapshots>
</repository>
```

Pode, em seguida, adicione a seguinte dependência ao seu projeto para executar a versão de pré-lançamento.

Dependência maven

```xml
<!-- https://mvnrepository.com/artifact/com.microsoft.azure/spark-streaming-eventhubs_2.11 -->
<dependency>
    <groupId>com.microsoft.azure</groupId>
    <artifactId>spark-streaming-eventhubs_2.11</artifactId>
    <version>2.0.4</version>
</dependency>
```

Dependência SBT

```
// https://mvnrepository.com/artifact/com.microsoft.azure/spark-streaming-eventhubs_2.11
libraryDependencies += "com.microsoft.azure" % "spark-streaming-eventhubs_2.11" % "2.0.4"
```

### <a name="direct-dstream-connection"></a>DStream ligação direta

Pode ser transferido um ficheiro de pré-criadas jar que contém os exemplos utilizam DStream direto no [http://central.maven.org/maven2/com/microsoft/azure/spark-streaming-eventhubs_2.11/2.0.4/spark-streaming-eventhubs_2.11-2.0.4.jar](http://central.maven.org/maven2/com/microsoft/azure/spark-streaming-eventhubs_2.11/2.0.4/spark-streaming-eventhubs_2.11-2.0.4.jar).

O ficheiro jar contém três exemplos cujo código de origem estão disponíveis em [https://github.com/hdinsight/spark-eventhubs/tree/master/examples/src/main/scala/com/microsoft/spark/streaming/examples/directdstream](https://github.com/hdinsight/spark-eventhubs/tree/master/examples/src/main/scala/com/microsoft/spark/streaming/examples/directdstream).

Colocar [WindowingWordCount](https://github.com/hdinsight/spark-eventhubs/blob/master/examples/src/main/scala/com/microsoft/spark/streaming/examples/directdstream/WindowingWordCount.scala) como exemplo:

```scala
private def createStreamingContext(
  sparkCheckpointDir: String,
  batchDuration: Int,
  namespace: String,
  eventHunName: String,
  eventhubParams: Map[String, String],
  progressDir: String) = {
val ssc = new StreamingContext(new SparkContext(), Seconds(batchDuration))
ssc.checkpoint(sparkCheckpointDir)
val inputDirectStream = EventHubsUtils.createDirectStreams(
  ssc,
  namespace,
  progressDir,
  Map(eventHunName -> eventhubParams))

inputDirectStream.map(receivedRecord => (new String(receivedRecord.getBody), 1)).
  reduceByKeyAndWindow((v1, v2) => v1 + v2, (v1, v2) => v1 - v2, Seconds(batchDuration * 3),
    Seconds(batchDuration)).print()

ssc
}

def main(args: Array[String]): Unit = {

if (args.length != 8) {
  println("Usage: program progressDir PolicyName PolicyKey EventHubNamespace EventHubName" +
    " BatchDuration(seconds) Spark_Checkpoint_Directory maxRate")
  sys.exit(1)
}

val progressDir = args(0)
val policyName = args(1)
val policykey = args(2)
val namespace = args(3)
val name = args(4)
val batchDuration = args(5).toInt
val sparkCheckpointDir = args(6)
val maxRate = args(7)

val eventhubParameters = Map[String, String] (
  "eventhubs.policyname" -> policyName,
  "eventhubs.policykey" -> policykey,
  "eventhubs.namespace" -> namespace,
  "eventhubs.name" -> name,
  "eventhubs.partition.count" -> "32",
  "eventhubs.consumergroup" -> "$Default",
  "eventhubs.maxRate" -> s"$maxRate"
)

val ssc = StreamingContext.getOrCreate(sparkCheckpointDir,
  () => createStreamingContext(sparkCheckpointDir, batchDuration, namespace, name,
    eventhubParameters, progressDir))

ssc.start()
ssc.awaitTermination()
}
```

No exemplo acima, `eventhubParameters` são os parâmetros específicos para uma única instância de EventHubs e tem de passar para o `createDirectStreams` API que constrói um mapeamento de objeto DStream direto para um espaço de nomes de Event Hubs. Ao longo do objeto DStream direto, pode chamar qualquer API de DStream fornecida pela arquitetura de API de transmissão em fluxo do Spark. Neste exemplo, vamos calcular a frequência de cada palavra dentro os intervalos de 3 micro batch último.

### <a name="receiver-based-connection"></a>Ligação com base em recetor

O Spark transmissão em fluxo escrita no Scala, o qual recebe eventos e encaminhar os destinos para diferentes, de aplicação de exemplo está disponível em [https://github.com/hdinsight/spark-streaming-data-persistence-examples](https://github.com/hdinsight/spark-streaming-data-persistence-examples). Siga os passos abaixo para atualizar a aplicação para a sua configuração de Hub de eventos e criar o jar de saída.

1. Inicie o IntelliJ IDEA e do ecrã inicial selecione **veja do controlo de versão** e, em seguida, clique em **Git**.
   
    ![Exemplo - get origens de Git de transmissão em fluxo do Apache Spark](./media/apache-spark-eventhub-streaming/spark-streaming-example-get-source-from-git.png "Apache Spark exemplo - get origens de Git de transmissão em fluxo")

2. No **Clone repositório** diálogo caixa, forneça o URL para o repositório de Git para clonar do, especifique o diretório para clonar a e, em seguida, clique em **Clone**.
   
    ![Exemplo - clone de Git de transmissão em fluxo do Apache Spark](./media/apache-spark-eventhub-streaming/spark-streaming-example-clone-from-git.png "Apache Spark exemplo - clone de Git de transmissão em fluxo")
3. Siga as instruções até o projeto foi clonado a completamente. Prima **Alt + 1** para abrir o **vista de projeto**. Este deve assemelhar-se a seguinte.
   
    ![Exemplo - vista de projeto de transmissão em fluxo do Apache Spark](./media/apache-spark-eventhub-streaming/spark-streaming-example-project-view.png "Apache Spark exemplo - vista de projeto de transmissão em fluxo")
4. Certifique-se que o código de aplicação é compilado com Java8. Certifique-se de isto, clique em **ficheiro**, clique em **estrutura do projeto**e no **projeto** separador, certifique-se de projeto de nível de idioma está definido como **8 - Lambdas, tipo as anotações, etc**.
   
    ![Exemplo - compilador do conjunto de transmissão em fluxo do Apache Spark](./media/apache-spark-eventhub-streaming/spark-streaming-example-java-8-compiler.png "Apache Spark exemplo - compilador do conjunto de transmissão em fluxo")
5. Abra o **pom.xml** e certifique-se a versão do Spark está correta. Em `<properties>` nós, procure o seguinte fragmento e verificar a versão de Spark.

        <scala.version>2.11.8</scala.version>
        <scala.compat.version>2.11.8</scala.compat.version>
        <scala.binary.version>2.11</scala.binary.version>
        <spark.version>2.0.0</spark.version>

6. A aplicação requer um jar de dependência chamado **jar de controlador JDBC**. Isto é necessário escrever as mensagens recebidas do Hub de eventos para uma base de dados SQL do Azure. Pode transferir esta jar (v 4.1 ou posterior) do [aqui](https://msdn.microsoft.com/sqlserver/aa937724.aspx). Adicione referência a este jar na biblioteca do projeto. Execute os seguintes passos:
     
     1. Na janela de IntelliJ IDEA em que tenha a aplicação abrir, clique em **ficheiro**, clique em **estrutura do projeto**e, em seguida, clique em **bibliotecas**. 
     2. Clique no ícone de adicionar (![ícone Adicionar](./media/apache-spark-eventhub-streaming/add-icon.png)), clique em **Java**e, em seguida, navegue para a localização onde transferiu o jar de controlador JDBC. Siga as instruções para adicionar o ficheiro jar à biblioteca do projeto.

         ![Adicione as dependências em falta](./media/apache-spark-eventhub-streaming/add-missing-dependency-jars.png "adicionar v7 de dependência em falta")
     3. Clique em **Aplicar**.

7. Crie o ficheiro jar de saída. Execute os seguintes passos.

   1. No **estrutura do projeto** caixa de diálogo, clique em **artefactos** e, em seguida, clique no símbolo de adição. Na caixa de diálogo de pop-up, clique em **JAR**e, em seguida, clique em **de módulos com dependências**.      
       
       ![Exemplo de transmissão em fluxo do Apache Spark - criar JAR](./media/apache-spark-eventhub-streaming/spark-streaming-example-create-jar.png "exemplo de transmissão em fluxo do Apache Spark - criar JAR")
   2. No **criar JAR de módulos** diálogo caixa, clique nas reticências (![reticências](./media/apache-spark-eventhub-streaming/ellipsis.png)) contra o **classe principal**.
   3. No **selecione classe de Main** diálogo caixa, selecione qualquer uma das classes disponíveis e, em seguida, clique em **OK**.
      
       ![Exemplo - selecione classe para jar de transmissão em fluxo do Apache Spark](./media/apache-spark-eventhub-streaming/spark-streaming-example-select-class-for-jar.png "Apache Spark exemplo - selecione classe para jar de transmissão em fluxo")
   4. No **criar JAR de módulos** diálogo caixa, certifique-se de que a opção de **extrair para o destino JAR** está selecionada e, em seguida, clique em **OK**. Esta ação cria um único JAR com todas as dependências.
      
       ![Exemplo de transmissão em fluxo do Apache Spark - criar jar de módulos](./media/apache-spark-eventhub-streaming/spark-streaming-example-create-jar-from-modules.png "exemplo de transmissão em fluxo do Apache Spark - criar jar de módulos")
   5. O **esquema de saída** separador lista todas as v7 que está incluídos como parte do projeto Maven. Pode selecionar e eliminar as no qual a aplicação de Scala não tem nenhum dependência direta. Para a aplicação que estamos a criar aqui, pode remover todos os mas último (**spark-transmissão em fluxo-data-persistência-exemplos compilam saída**). Selecione o v7 para eliminar e, em seguida, clique em de **eliminar** ícone (![ícone Eliminar](./media/apache-spark-eventhub-streaming/delete-icon.png)).
      
       ![Exemplo - eliminar extraído v7 de transmissão em fluxo do Apache Spark](./media/apache-spark-eventhub-streaming/spark-streaming-example-delete-output-jars.png "Apache Spark exemplo - eliminar extraído v7 de transmissão em fluxo")
      
       Certifique-se **tirar partido da marca** caixa está selecionada, que garante que o jar é criada sempre que o projeto é criado ou atualizado. Clique em **Aplicar**.
   6. No **esquema de saída** separador, à direita na parte inferior do **elementos disponíveis** caixa, tem de jar do SQL Server JDBC que adicionou anteriormente para a biblioteca de projeto. Tem de adicionar esta opção para o **esquema de saída** separador. O ficheiro jar com o botão direito e, em seguida, clique em **extrair na raiz da saída**.
      
       ![Exemplo - jar de dependência de extração de transmissão em fluxo do Apache Spark](./media/apache-spark-eventhub-streaming/spark-streaming-example-extract-dependency-jar.png "Apache Spark exemplo - jar de dependência de extração de transmissão em fluxo")  
      
       O **esquema de saída** separador deve agora ter este aspeto.
      
       ![Exemplo - separador de resultado final de transmissão em fluxo do Apache Spark](./media/apache-spark-eventhub-streaming/spark-streaming-example-final-output-tab.png "Apache Spark exemplo - separador de resultado final de transmissão em fluxo")        
      
       No **estrutura do projeto** caixa de diálogo, clique em **aplicar** e, em seguida, clique em **OK**.    
   7. Na barra de menus, clique em **criar**e, em seguida, clique em **tornar projeto**. Também pode clicar em **criar artefactos** para criar o jar. O jar de saída é criado no **\classes\artifacts**.
      
       ![Exemplo de transmissão em fluxo do Apache Spark - saída JAR](./media/apache-spark-eventhub-streaming/spark-streaming-example-output-jar.png "JAR de saída de exemplo de transmissão em fluxo do Apache Spark -")

## <a name="run-the-application-remotely-on-a-spark-cluster-using-livy"></a>Executar a aplicação remotamente num cluster do Spark com o Livy

Este artigo utiliza o Livy para executar a aplicação remotamente num cluster do Spark de transmissão em fluxo do Apache Spark. Para ver um debate detalhado sobre como utilizar o Livy com um cluster do Spark do HDInsight, consulte [cluster submeter as tarefas remotamente do Apache Spark no Azure HDInsight](apache-spark-livy-rest-interface.md). Antes de começar com o Spark aplicações de transmissão em fluxo, existem algumas coisas que deve fazer:

1. Iniciar a aplicação autónoma local para gerar eventos de e enviadas para o Hub de eventos. Utilize o seguinte comando para o fazer:

        java -cp com-microsoft-azure-eventhubs-client-example-0.2.0.jar com.microsoft.eventhubs.client.example.EventhubsClientDriver --eventhubs-namespace "mysbnamespace" --eventhubs-name "myeventhub" --policy-name "mysendpolicy" --policy-key "<policy key>" --message-length 32 --thread-count 32 --message-count -1

2. Copie o jar de transmissão em fluxo (**spark-transmissão em fluxo-data-persistência-examples.jar**) para o Blob storage do Azure associado com o cluster. Isto torna o jar acessíveis para o Livy. Pode utilizar [ **AzCopy**](../../storage/common/storage-use-azcopy.md), um utilitário de linha de comandos, para o fazer. Existem muitos outros clientes, que pode utilizar para carregar dados. Pode encontrar mais informações sobre-las em [carregar dados para tarefas do Hadoop no HDInsight](../hdinsight-upload-data.md).
3. Instale o CURL no computador onde está a executar a estas aplicações. Utilizamos o CURL para invocar os pontos finais Livy para executar as tarefas remotamente.

### <a name="run-the-spark-streaming-application-to-receive-the-events-into-an-azure-storage-blob-as-text"></a>Execute o Spark aplicações para receber os eventos para um Blob de armazenamento do Azure como texto de transmissão em fluxo

Abra uma linha de comandos, navegue para o diretório onde instalou o CURL e execute o seguinte comando (substituir nome de utilizador/palavra-passe e cluster nome):

    curl -k --user "admin:mypassword1!" -v -H "Content-Type: application/json" -X POST --data @C:\Temp\inputBlob.txt "https://mysparkcluster.azurehdinsight.net/livy/batches"

Os parâmetros no ficheiro **inputBlob.txt** são definidos do seguinte modo:

    { "file":"wasb:///example/jars/spark-streaming-data-persistence-examples.jar", "className":"com.microsoft.spark.streaming.examples.workloads.EventhubsEventCount", "args":["--eventhubs-namespace", "mysbnamespace", "--eventhubs-name", "myeventhub", "--policy-name", "myreceivepolicy", "--policy-key", "<put-your-key-here>", "--consumer-group", "$default", "--partition-count", 10, "--batch-interval-in-seconds", 20, "--checkpoint-directory", "/EventCheckpoint", "--event-count-folder", "/EventCount/EventCount10"], "numExecutors":20, "executorMemory":"1G", "executorCores":1, "driverMemory":"2G" }

Informe-nos compreenda quais são os parâmetros no ficheiro de entrada:

* **ficheiro** é o caminho para o ficheiro jar da aplicação na conta de armazenamento do Azure associada com o cluster.
* **className** é o nome da classe no jar.
* **args** se a lista de argumentos necessários pela classe
* **numExecutors** é o número de núcleos utilizada pelo Spark para executar a aplicação de transmissão em fluxo. Isto deve ser sempre, pelo menos, duas vezes o número de partições do Hub de eventos.
* **executorMemory**, **executorCores**, **driverMemory** são parâmetros utilizados para atribuir os recursos necessários para a aplicação de transmissão em fluxo.

> [!NOTE]
> Não é necessário criar as pastas de saída (EventCheckpoint, EventCount/EventCount10) que são utilizadas como parâmetros. A aplicação de transmissão em fluxo cria por si.
>
>

Quando executar o comando, deverá ver um resultado como o seguinte:

    < HTTP/1.1 201 Created
    < Content-Type: application/json; charset=UTF-8
    < Location: /18
    < Server: Microsoft-IIS/8.5
    < X-Powered-By: ARR/2.5
    < X-Powered-By: ASP.NET
    < Date: Tue, 01 Dec 2015 05:39:10 GMT
    < Content-Length: 37
    <
    {"id":1,"state":"starting","log":[]}* Connection #0 to host mysparkcluster.azurehdinsight.net left intact

Tome nota do ID de lote na última linha do resultado (neste exemplo é '1'). Para verificar que a aplicação é executada com êxito, pode observar a sua conta do storage do Azure associada com o cluster e deverá ver o **EventCount/EventCount10** pasta criada não existe. Esta pasta deve conter blobs que capture o número de eventos processados durante o período de tempo especificado para o parâmetro **batch intervalo em segundos**.

O Spark aplicações de transmissão em fluxo continuará a ser executado até que o kill. Para tal, utilize o seguinte comando:

    curl -k --user "admin:mypassword1!" -v -X DELETE "https://mysparkcluster.azurehdinsight.net/livy/batches/1"

### <a name="run-the-applications-to-receive-the-events-into-an-azure-storage-blob-as-json"></a>Executar as aplicações para receber os eventos para um Blob de armazenamento do Azure como JSON
Abra uma linha de comandos, navegue para o diretório onde instalou o CURL e execute o seguinte comando (substituir nome de utilizador/palavra-passe e cluster nome):

    curl -k --user "admin:mypassword1!" -v -H "Content-Type: application/json" -X POST --data @C:\Temp\inputJSON.txt "https://mysparkcluster.azurehdinsight.net/livy/batches"

Os parâmetros no ficheiro **inputJSON.txt** são definidos do seguinte modo:

    { "file":"wasb:///example/jars/spark-streaming-data-persistence-examples.jar", "className":"com.microsoft.spark.streaming.examples.workloads.EventhubsToAzureBlobAsJSON", "args":["--eventhubs-namespace", "mysbnamespace", "--eventhubs-name", "myeventhub", "--policy-name", "myreceivepolicy", "--policy-key", "<put-your-key-here>", "--consumer-group", "$default", "--partition-count", 10, "--batch-interval-in-seconds", 20, "--checkpoint-directory", "/EventCheckpoint", "--event-count-folder", "/EventCount/EventCount10", "--event-store-folder", "/EventStore10"], "numExecutors":20, "executorMemory":"1G", "executorCores":1, "driverMemory":"2G" }

Os parâmetros são semelhantes às que especificou para a saída de texto, no passo anterior. Novamente, não terá de criar as pastas de saída (EventCheckpoint, EventCount/EventCount10) que são utilizadas como parâmetros. A aplicação de transmissão em fluxo cria por si.

 Depois de executar o comando, pode observar a sua conta do storage do Azure associada com o cluster e deverá ver o **/EventStore10** pasta criada não existe. Abrir qualquer ficheiro prefixo **parte -** e deverá ver os eventos processados num formato JSON.

### <a name="run-the-applications-to-receive-the-events-into-a-hive-table"></a>Executar as aplicações para receber os eventos para uma tabela do Hive
Para executar o Spark aplicações fluxos de eventos para uma tabela do Hive de transmissão em fluxo tem de alguns componentes adicionais. Nomeadamente:

* datanucleus-api-jdo-3.2.6.jar
* datanucleus-rdbms-3.2.9.jar
* datanucleus-core-3.2.10.jar
* site.xml do Hive

O **. JAR** ficheiros estão disponíveis no seu cluster do HDInsight Spark em `/usr/hdp/current/spark-client/lib`. O **hive site.xml** está disponível em `/usr/hdp/current/spark-client/conf`.

Pode utilizar [WinScp](http://winscp.net/eng/download.php) para copiar, estes ficheiros do cluster para o seu computador local. Em seguida, pode utilizar ferramentas copiar estes ficheiros através à sua conta de armazenamento associada com o cluster. Para obter mais informações sobre como carregar ficheiros para a conta de armazenamento, consulte [carregar dados para tarefas do Hadoop no HDInsight](../hdinsight-upload-data.md).

Depois de ter copiado os ficheiros para a conta do storage do Azure, abra uma linha de comandos, navegue para o diretório onde instalou o CURL e execute o seguinte comando (substituir nome de utilizador/palavra-passe e cluster nome):

    curl -k --user "admin:mypassword1!" -v -H "Content-Type: application/json" -X POST --data @C:\Temp\inputHive.txt "https://mysparkcluster.azurehdinsight.net/livy/batches"

Os parâmetros no ficheiro **inputHive.txt** são definidos do seguinte modo:

    { "file":"wasb:///example/jars/spark-streaming-data-persistence-examples.jar", "className":"com.microsoft.spark.streaming.examples.workloads.EventhubsToHiveTable", "args":["--eventhubs-namespace", "mysbnamespace", "--eventhubs-name", "myeventhub", "--policy-name", "myreceivepolicy", "--policy-key", "<put-your-key-here>", "--consumer-group", "$default", "--partition-count", 10, "--batch-interval-in-seconds", 20, "--checkpoint-directory", "/EventCheckpoint", "--event-count-folder", "/EventCount/EventCount10", "--event-hive-table", "EventHiveTable10" ], "jars":["wasb:///example/jars/datanucleus-api-jdo-3.2.6.jar", "wasb:///example/jars/datanucleus-rdbms-3.2.9.jar", "wasb:///example/jars/datanucleus-core-3.2.10.jar"], "files":["wasb:///example/jars/hive-site.xml"], "numExecutors":20, "executorMemory":"1G", "executorCores":1, "driverMemory":"2G" }

Os parâmetros são semelhantes às que especificou para a saída de texto, nos passos anteriores. Novamente, não terá de criar as pastas de saída (EventCheckpoint, EventCount/EventCount10) ou o resultado da tabela do Hive (EventHiveTable10) que são utilizadas como parâmetros. A aplicação de transmissão em fluxo cria por si. Tenha em atenção que o **v7** e **ficheiros** opção inclui caminhos para os ficheiros. JAR e o ramo de registo-site.xml copiadas para a conta de armazenamento.

Para verificar que a tabela de hive foi criada com êxito, utilize o [vista Ambari Hive](../hadoop/apache-hadoop-use-hive-ambari-view.md). Pode executar uma consulta SELECT, para ver os conteúdos da tabela.

    SELECT * FROM eventhivetable10 LIMIT 10;

Deve ver um resultado como o seguinte:

    ZN90apUSQODDTx7n6Toh6jDbuPngqT4c
    sor2M7xsFwmaRW8W8NDwMneFNMrOVkW1
    o2HcsU735ejSi2bGEcbUSB4btCFmI1lW
    TLuibq4rbj0T9st9eEzIWJwNGtMWYoYS
    HKCpPlWFWAJILwR69MAq863nCWYzDEw6
    Mvx0GQOPYvPR7ezBEpIHYKTKiEhYammQ
    85dRppSBSbZgThLr1s0GMgKqynDUqudr
    5LAWkNqorLj3ZN9a2mfWr9rZqeXKN4pF
    ulf9wSFNjD7BZXCyunozecov9QpEIYmJ
    vWzM3nvOja8DhYcwn0n5eTfOItZ966pa
    Time taken: 4.434 seconds, Fetched: 10 row(s)


### <a name="run-the-applications-to-receive-the-events-into-an-azure-sql-database-table"></a>Executar as aplicações para receber os eventos para uma tabela de base de dados SQL do Azure
Antes de executar este passo, certifique-se de que tem uma base de dados SQL do Azure criado. Para obter instruções, consulte [criar uma base de dados do SQL Server em minutos](../../sql-database/sql-database-get-started-portal.md). Para concluir esta secção, terá de valores de nome de base de dados, nome do servidor de base de dados e as credenciais de administrador da base de dados como parâmetros. Não é necessário criar embora a tabela de base de dados. O Spark aplicações de transmissão em fluxo cria que para si.

Abra uma linha de comandos, navegue para o diretório onde instalou o CURL e execute o seguinte comando:

    curl -k --user "admin:mypassword1!" -v -H "Content-Type: application/json" -X POST --data @C:\Temp\inputSQL.txt "https://mysparkcluster.azurehdinsight.net/livy/batches"

Os parâmetros no ficheiro **inputSQL.txt** são definidos do seguinte modo:

    { "file":"wasb:///example/jars/spark-streaming-data-persistence-examples.jar", "className":"com.microsoft.spark.streaming.examples.workloads.EventhubsToAzureSQLTable", "args":["--eventhubs-namespace", "mysbnamespace", "--eventhubs-name", "myeventhub", "--policy-name", "myreceivepolicy", "--policy-key", "<put-your-key-here>", "--consumer-group", "$default", "--partition-count", 10, "--batch-interval-in-seconds", 20, "--checkpoint-directory", "/EventCheckpoint", "--event-count-folder", "/EventCount/EventCount10", "--sql-server-fqdn", "<database-server-name>.database.windows.net", "--sql-database-name", "mysparkdatabase", "--database-username", "sparkdbadmin", "--database-password", "<put-password-here>", "--event-sql-table", "EventContent" ], "numExecutors":20, "executorMemory":"1G", "executorCores":1, "driverMemory":"2G" }

Para verificar que a aplicação é executada com êxito, pode ligar à base de dados SQL do Azure utilizando o SQL Server Management Studio. Para obter instruções sobre como fazê-lo, consulte [ligar à base de dados SQL com o SQL Server Management Studio](../../sql-database/sql-database-connect-query-ssms.md). Assim que estiver ligado à base de dados, pode navegar para o **EventContent** tabela que foi criada pela aplicação de transmissão em fluxo. Pode executar uma consulta rápida para obter os dados da tabela. Execute a seguinte consulta:

    SELECT * FROM EventCount

Deverá ver um resultado semelhante ao seguinte:

    00046b0f-2552-4980-9c3f-8bba5647c8ee
    000b7530-12f9-4081-8e19-90acd26f9c0c
    000bc521-9c1b-4a42-ab08-dc1893b83f3b
    00123a2a-e00d-496a-9104-108920955718
    0017c68f-7a4e-452d-97ad-5cb1fe5ba81b
    001KsmqL2gfu5ZcuQuTqTxQvVyGCqPp9
    001vIZgOStka4DXtud0e3tX7XbfMnZrN
    00220586-3e1a-4d2d-a89b-05c5892e541a
    0029e309-9e54-4e1b-84be-cd04e6fce5ec
    003333cf-874f-4045-9da3-9f98c2b4ea49
    0043c07e-8d73-420a-9af7-1fcb94575356
    004a11a9-0c2c-4bc0-a7d5-2e0ebd947ab9


## <a name="seealso"></a>Ver também
* [Descrição geral: Apache Spark no Azure HDInsight](apache-spark-overview.md)
* [Estrutura de ligação com base em Recetor e DStream direta](https://www.slideshare.net/NanZhu/seattle-sparkmeetup032317)

### <a name="scenarios"></a>Cenários
* [Spark com BI: Efetuar uma análise de dados interativa com o Spark no HDInsight com ferramentas do BI](apache-spark-use-bi-tools.md)
* [Spark com Machine Learning: Utilizar o Spark no HDInsight para analisar a temperatura do edifício com dados de AVAC](apache-spark-ipython-notebook-machine-learning.md)
* [Spark com Machine Learning: Utilizar o Spark no HDInsight para prever resultados de inspeções alimentares](apache-spark-machine-learning-mllib-ipython.md)
* [Análise de registos de sites com o Spark no HDInsight](../hdinsight-apache-spark-custom-library-website-log-analysis.md)

### <a name="create-and-run-applications"></a>Criar e executar aplicações
* [Criar uma aplicação autónoma com o Scala](../hdinsight-apache-spark-create-standalone-application.md)
* [Executar tarefas remotamente num cluster do Spark com o Livy](apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>Ferramentas e extensões
* [Utilizar o Plug-in das Ferramentas do HDInsight para o IntelliJ IDEA para criar e submeter aplicações do Spark Scala](apache-spark-intellij-tool-plugin.md)
* [Utilizar o Plug-in das Ferramentas do HDInsight para o IntelliJ IDEA para depurar aplicações do Spark remotamente](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Utilizar blocos de notas do Zeppelin com um cluster do Spark no HDInsight](apache-spark-zeppelin-notebook.md)
* [Kernels disponíveis para o bloco de notas do Jupyter no cluster do Spark para o HDInsight](apache-spark-jupyter-notebook-kernels.md)
* [Utilizar pacotes externos com blocos de notas do Jupyter](apache-spark-jupyter-notebook-use-external-packages.md)
* [Instalar o Jupyter no computador e ligar a um cluster do Spark do HDInsight](apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>Gerir recursos
* [Gerir recursos para o cluster do Apache Spark no Azure HDInsight](apache-spark-resource-manager.md)
* [Controlar e depurar tarefas em execução num cluster do Apache Spark do HDInsight](apache-spark-job-debugging.md)

[hdinsight-versions]: hdinsight-component-versioning.md
[hdinsight-upload-data]: ../hdinsight-upload-data.md
[hdinsight-storage]: hdinsight-hadoop-use-blob-storage.md

[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[azure-create-storageaccount]: ../storage-create-storage-account/
