---
title: "O que é a transmissão em fluxo do Spark no Azure HDInsight | Microsoft Docs"
description: "Como utilizar aplicações de transmissão em fluxo do Spark nos clusters do HDInsight Spark."
services: hdinsight
documentationcenter: 
tags: azure-portal
author: maxluk
manager: jhubbard
editor: cgronlun
ms.assetid: 
ms.service: hdinsight
ms.custom: hdinsightactive
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/08/2017
ms.author: nitinme
ms.openlocfilehash: 4699673255706a69d597ce28b47ab05a5f8eb957
ms.sourcegitcommit: 93902ffcb7c8550dcb65a2a5e711919bd1d09df9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/09/2017
---
# <a name="overview-of-spark-streaming"></a>Descrição geral da transmissão em fluxo do Spark

Transmissão em fluxo do Spark fornece processamento de fluxo de dados no HDInsight Spark clusters, com a garantia de que nenhum evento de entrada é processado exatamente uma vez, mesmo se ocorrer uma falha de nó. Um fluxo do Spark é uma tarefa de longa execução que recebe dados de entrada a partir de uma ampla variedade de origens, incluindo Event Hubs do Azure, um IoT Hub do Azure, Kafka, Flume, Twitter, ZeroMQ, sockets em bruto de TCP, ou de sistemas de ficheiros instalados HDFS de monitorização. Ao contrário de um processo unicamente condicionada por eventos, um fluxo do Spark lotes de dados de entrada em intervalos de tempo, por exemplo, um setor de 2 segundo e, em seguida, transforma cada lote de dados utilizando o mapa, reduzir, associação e extrair operações. Em seguida, escreve os dados transformados para sistemas de ficheiros instalados, bases de dados, dashboards e a consola.

![Fluxo de processamento com o HDInsight e de transmissão em fluxo do Spark](./media/apache-spark-streaming-overview/hdinsight-spark-streaming.png)

Aplicações de transmissão em fluxo do Spark tem de aguardar alguns frações de um segundo para recolher cada *micro batch* de eventos antes de enviar esse batch para processamento. Em contrapartida, uma aplicação condicionada por eventos processa cada evento imediatamente. Latência de transmissão em fluxo do Spark é normalmente em alguns segundos. Os benefícios da abordagem micro batch são mais eficiente de processamento de dados e cálculos de agregação mais simples.

## <a name="introducing-the-dstream"></a>Introduzir o DStream

Transmissão em fluxo do Spark representa um fluxo contínuo de dados recebidos com um *fluxo discretizado* chamado um DStream. Um DStream pode ser criado a partir de origens de entrada como Event Hubs ou Kafka ou aplicando transformações DStream outro.

Um DStream fornece uma camada de abstração para além dos dados de eventos não processados. 

Começar com um único evento, diga uma temperatura ler a partir de um thermostat ligado. Quando este evento chega à sua aplicação de transmissão em fluxo do Spark, o evento é armazenado de forma fiável, ou seja, é replicado em vários nós. Este tolerância garante que a falha de qualquer nó único não resultará na perda do evento. O Spark core utiliza uma estrutura de dados que distribui dados de vários nós do cluster, onde cada nó, geralmente, mantém a seus próprios dados em memória para um melhor desempenho. Esta estrutura de dados é chamada um *resiliente conjunto de dados distribuído* (RDD).

Cada RDD representa eventos recolhidos ao longo de um período de tempo definido pelo utilizador chamado o intervalo de lote. Como cada intervalo de lote decorrido, é produzido um novo RDD que contém todos os dados desse intervalo. O conjunto de RDDs são recolhidas para um DStream. Por exemplo, se o intervalo de lote longo de um segundo, o seu DStream emite um lote cada segundo RDD de um contentor que contém todos os dados ingeridos durante esse segundo. Ao processar o DStream, é apresentado o evento de temperatura de uma destas lotes. Uma aplicação de transmissão em fluxo do Spark processa lotes que contêm os eventos e, em última análise age em dados armazenados em cada RDD.

![Exemplo DStream com eventos de temperatura ](./media/apache-spark-streaming-overview/hdinsight-spark-streaming-example.png)

## <a name="structure-of-a-spark-streaming-application"></a>Estrutura de uma aplicação de transmissão em fluxo do Spark
Uma aplicação de transmissão em fluxo do Spark é uma aplicação de longa execução que recebe dados a partir de origens de inserção, aplica transformações para processar os dados e, em seguida, envia os dados para um ou mais destinos. A estrutura de uma aplicação de transmissão em fluxo do Spark tem duas partes principais. Em primeiro lugar, definir onde os dados vêm, o processamento efetuar nos dados e, em que devem passar os resultados. Segundo, execute o indefinidamente, a aplicação a aguardar um sinal de paragem.

Por exemplo, é uma aplicação simples que recebe uma linha de texto ao longo de um socket TCP e conta o número de vezes que cada palavra aparece aqui.

### <a name="define-the-application"></a>Definir a aplicação

A definição de lógica de aplicação consiste em quatro passos:

1. Crie um StreamingContext.
2. Crie um DStream a partir de StreamingContext.
3. Aplica as transformações para o DStream.
4. Os resultados de saída.

Esta definição é estática, e não é de processamento de dados até que executa a aplicação.

#### <a name="create-a-streamingcontext"></a>Criar um StreamingContext
Crie um StreamingContext da SparkContext que aponta para o cluster. Ao criar um StreamingContext, especificar o tamanho do lote em segundos, por exemplo:

    val ssc = new StreamingContext(spark, Seconds(1))

#### <a name="create-a-dstream"></a>Criar um DStream
Utilizar a instância de StreamingContext que criou, crie uma entrada DStream para a origem de entrada. Neste caso, iremos são observar para o aspeto de novos ficheiros de armazenamento predefinido ligado ao cluster do HDInsight.

    val lines = ssc.textFileStream("/uploads/2017/01/")

#### <a name="apply-transformations"></a>Aplicar as transformações
Implementar o processamento aplicando transformações no DStream. A nossa aplicação recebe uma linha de texto num momento a partir do ficheiro, divide cada linha numa palavras e, em seguida, utiliza um padrão de map-reduce para contabilizar o número de vezes que cada palavra aparece.

    val words = lines.flatMap(_.split(" "))
    val pairs = words.map(word => (word, 1))
    val wordCounts = pairs.reduceByKey(_ + _)

#### <a name="output-results"></a>Resultados de saída
Emitir os resultados de transformação para os sistemas de destino, aplicando as operações de saída. Neste caso, vamos mostrar o resultado de cada percorrer a computação no resultado da consola.

    wordCounts.print()

### <a name="run-the-application"></a>Executar a aplicação
Inicie a aplicação de transmissão em fluxo e execute até que é recebido um sinal de terminação.

    ssc.start()            
    ssc.awaitTermination()

Para obter detalhes sobre a API de fluxo do Spark, juntamente com as origens de eventos, transformações e operações de saída suporta consulte [guia de programação de transmissão em fluxo de Spark](https://people.apache.org/~pwendell/spark-releases/latest/streaming-programming-guide.html).

A aplicação de exemplo seguinte é autónomo, pelo que pode ser executado dentro de um [bloco de notas do Jupyter](apache-spark-jupyter-notebook-kernels.md). No exemplo, vamos criar uma origem de dados mock na classe DummySource que produz o valor de um contador e a hora atual em milissegundos cada cinco segundos. Vamos criar um novo objeto de StreamingContext que tem um intervalo de lote de 30 segundos. Sempre que é criado um lote, este examina RDD produzido, converte-o para um DataFrame Spark e cria uma tabela temporária através de DataFrame.

    class DummySource extends org.apache.spark.streaming.receiver.Receiver[(Int, Long)](org.apache.spark.storage.StorageLevel.MEMORY_AND_DISK_2) {

        /** Start the thread that simulates receiving data */
        def onStart() {
            new Thread("Dummy Source") { override def run() { receive() } }.start()
        }

        def onStop() {  }

        /** Periodically generate a random number from 0 to 9, and the timestamp */
        private def receive() {
            var counter = 0  
            while(!isStopped()) {
            store(Iterator((counter, System.currentTimeMillis)))
            counter += 1
            Thread.sleep(5000)
            }
        }
    }

    // A batch is created every 30 seconds
    val ssc = new org.apache.spark.streaming.StreamingContext(spark.sparkContext, org.apache.spark.streaming.Seconds(30))

    // Set the active SQLContext so that we can access it statically within the foreachRDD
    org.apache.spark.sql.SQLContext.setActive(spark.sqlContext)

    // Create the stream
    val stream = ssc.receiverStream(new DummySource())

    // Process RDDs in the batch
    stream.foreachRDD { rdd =>

        // Access the SQLContext and create a table called demo_numbers we can query
        val _sqlContext = org.apache.spark.sql.SQLContext.getOrCreate(rdd.sparkContext)
        _sqlContext.createDataFrame(rdd).toDF("value", "time")
            .registerTempTable("demo_numbers")
    } 

    // Start the stream processing
    ssc.start()

Em seguida, iremos pode consultar DataFrame periodicamente para ver o conjunto de valores presentes no lote atual. Neste caso, podemos utilizar a seguinte consulta SQL:

    %%sql
    SELECT * FROM demo_numbers

A saída resultante tem o seguinte aspeto:

| valor | hora |
| --- | --- |
|10 | 1497314465256 |
|11 | 1497314470272 |
|12 | 1497314475289 |
|13 | 1497314480310 |
|14 | 1497314485327 |
|15 | 1497314490346 |

Esperamos ver os valores de seis, uma vez que o DummySource cria um valor a cada cinco segundos e é emitir um lote de cada 30 segundos.

## <a name="sliding-windows"></a>Windows deslizante
Se pretender efetuar cálculos de agregação no seu DStream algum período de tempo, por exemplo, para obter uma temperatura média ao longo dos último segundos 2, pode utilizar as operações de janela deslizante, incluídas com transmissão em fluxo do Spark. Uma janela deslizante está definida como tendo um período de tempo (da duração da janela) e o intervalo durante os quais o conteúdo da janela são avaliadas (o intervalo de gradualmente).

Estes deslizante windows podem sobrepor-se, por exemplo, pode definir uma janela com um comprimento de 2 segundos, que slides cada 1 segundo. Isto significa sempre que efetuar um cálculo de agregação, a janela incluirá dados a partir do último 1 segundo da janela anterior, bem como quaisquer novos dados no seguinte 1 segundo.

![Exemplo de janela inicial com eventos de temperatura](./media/apache-spark-streaming-overview/hdinsight-spark-streaming-window-01.png)

![Janela de exemplo com eventos de temperatura após a deslizante](./media/apache-spark-streaming-overview/hdinsight-spark-streaming-window-02.png)

No exemplo seguinte, iremos atualizar o código que utiliza o DummySource recolher de lotes para uma janela com uma duração de um minuto e um gradualmente de um minuto.

    // A batch is created every 30 seconds
    val ssc = new org.apache.spark.streaming.StreamingContext(spark.sparkContext, org.apache.spark.streaming.Seconds(30))

    // Set the active SQLContext so that we can access it statically within the foreachRDD
    org.apache.spark.sql.SQLContext.setActive(spark.sqlContext)

    // Create the stream
    val stream = ssc.receiverStream(new DummySource())

    // Process batches in 1 minute windows
    stream.window(org.apache.spark.streaming.Minutes(1)).foreachRDD { rdd =>

        // Access the SQLContext and create a table called demo_numbers we can query
        val _sqlContext = org.apache.spark.sql.SQLContext.getOrCreate(rdd.sparkContext)
        _sqlContext.createDataFrame(rdd).toDF("value", "time")
        .registerTempTable("demo_numbers")
    } 

    // Start the stream processing
    ssc.start()

Após o primeiro minuto, este irá produzir 12 entradas - seis entradas de cada lotes dois recolhidos na janela.

| valor | hora |
| --- | --- |
| 1 | 1497316294139 |
| 2 | 1497316299158
| 3 | 1497316304178
| 4 | 1497316309204
| 5 | 1497316314224
| 6 | 1497316319243
| 7 | 1497316324260
| 8 | 1497316329278
| 9 | 1497316334293
| 10 | 1497316339314
| 11 | 1497316344339
| 12 | 1497316349361

As funções de janela deslizante disponíveis na API de transmissão em fluxo do Spark incluem janela, countByWindow, reduceByWindow e countByValueAndWindow. Para obter detalhes sobre estas funções, consulte [transformações nas DStreams](https://people.apache.org/~pwendell/spark-releases/latest/streaming-programming-guide.html#transformations-on-dstreams).

## <a name="checkpointing"></a>Ponto de verificação
Para fornecer resiliência e tolerância a falhas, transmissão em fluxo do Spark depende do ponto de verificação para assegurar que o processamento de fluxo pode continuar sem interrupção, mesmo face a falhas de nó. No HDInsight, o Spark cria pontos de verificação para o armazenamento durável (Storage do Azure ou Data Lake Store). Estes pontos de verificação armazenam os metadados sobre a aplicação de transmissão em fluxo, tais como a configuração, as operações definidas pela aplicação e quaisquer lotes que foram colocados em fila, mas ainda não foi processadas. Em alguns casos, os pontos de verificação também irão incluir guardar os dados no RDDs para bastante o tempo que demora a reconstruir o estado dos dados de que se encontra presente na RDDs geridos pelo Spark.

## <a name="deploying-spark-streaming-applications"></a>Implementar aplicações de transmissão em fluxo do Spark
Normalmente, criar a aplicação transmissão em fluxo do Spark localmente e, em seguida, implementá-la para o Spark no HDInsight ao copiar o ficheiro JAR que contém a aplicação para o armazenamento de predefinição ligado ao cluster do HDInsight. Em seguida, pode começar a sua aplicação utilizando as APIs REST do LIVY disponível a partir do seu cluster. Esta é uma operação POST em que o corpo da publicação inclui um documento JSON que fornece o caminho para o seu JAR, o nome da classe cujo método principal define e executa a aplicação de transmissão em fluxo e, opcionalmente, os requisitos de recursos da tarefa (por exemplo, o número de ex ecutors, memória e núcleos), e as definições de configuração necessita de código da aplicação.

![Janela de exemplo com eventos de temperatura após a deslizante](./media/apache-spark-streaming-overview/hdinsight-spark-streaming-livy.png)

Também pode ser verificado o estado de todas as aplicações com um pedido GET relativamente um ponto final LIVY. Por fim, pode terminar uma aplicação em execução ao emitir um pedido de eliminação relativamente ao ponto final em DIRETO. Para obter detalhes sobre a API de LIVY, consulte [tarefas remotas com o LIVY](apache-spark-livy-rest-interface.md)

## <a name="next-steps"></a>Passos seguintes

* [Criar um Cluster do Apache Spark no HDInsight](../hdinsight-hadoop-create-linux-clusters-portal.md)
* [Guia de programação de transmissão em fluxo do Spark](https://people.apache.org/~pwendell/spark-releases/latest/streaming-programming-guide.html)
* [Iniciar tarefas do Spark remotamente com o LIVY](apache-spark-livy-rest-interface.md)