---
title: "Azure Cosmos DB: Efetuar análises de gráfico com o Spark e o Apache TinkerPop Gremlin | Microsoft Docs"
description: "Este artigo apresenta as instruções para configurar e executar o cálculo de paralelo e de análise do gráfico na base de dados do Azure Cosmos com o Spark e TinkerPop SparkGraphComputer."
services: cosmosdb
documentationcenter: 
author: khdang
manager: shireest
editor: 
ms.assetid: 89ea62bb-c620-46d5-baa0-eefd9888557c
ms.service: cosmos-db
ms.custom: quick start connect
ms.workload: 
ms.tgt_pltfrm: na
ms.devlang: gremlin
ms.topic: article
ms.date: 09/08/2017
ms.author: khdang
ms.openlocfilehash: 473cc23c73a721c54bc87c03069f4f3688cde11f
ms.sourcegitcommit: 9c3150e91cc3075141dc2955a01f47040d76048a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/26/2017
---
# <a name="azure-cosmos-db-perform-graph-analytics-by-using-spark-and-apache-tinkerpop-gremlin"></a>Azure Cosmos DB: Efetuar análises de gráfico com o Spark e o Apache TinkerPop Gremlin

[BD do Azure do Cosmos](introduction.md) é o serviço de base de dados globalmente distribuída e múltiplos modelo da Microsoft. Pode criar e consultar documentos, chave/valor e bases de dados de gráfico, sendo todas beneficiam das capacidades de distribuição global e dimensionamento horizontal o núcleo da BD do Cosmos do Azure. BD do Azure do Cosmos suporta cargas de trabalho do gráfico (OLTP) que utilizam de processamento de transações online [Apache TinkerPop Gremlin](graph-introduction.md).

[O Spark](http://spark.apache.org/) for um projeto de Apache Software Foundation concentra-se no processamento de dados de processamento analítico online (OLAP) para fins gerais. Spark fornece uma híbrida no-memória/baseada em disco distribuída informático modelo que é semelhante ao modelo de MapReduce do Hadoop. Pode implementar o Apache Spark na nuvem utilizando [Azure HDInsight](https://azure.microsoft.com/services/hdinsight/apache-spark/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

Ao combinar a base de dados do Azure Cosmos e Spark, pode executar cargas de trabalho OLTP e OLAP quando utilizar Gremlin. Este artigo de início rápido demonstra como executar consultas de Gremlin no Azure Cosmos DB num cluster do Azure HDInsight Spark.

## <a name="prerequisites"></a>Pré-requisitos

Antes de poder executar este exemplo, tem de ter os pré-requisitos seguintes:
* Cluster do HDInsight Spark Azure 2.0
* JDK 1.8 + (se não tiver JDK, execute `apt-get install default-jdk`.)
* Maven (se não tiver o Maven, execute `apt-get install maven`.)
* Uma subscrição do Azure ([!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)])

Para obter informações sobre como configurar um cluster do Azure HDInsight Spark, consulte [clusters do HDInsight aprovisionamento](../hdinsight/hdinsight-hadoop-provision-linux-clusters.md).

## <a name="create-an-azure-cosmos-db-database-account"></a>Criar uma conta de base de dados de base de dados do Azure Cosmos

Em primeiro lugar, crie uma conta de base de dados com a Graph API efetuando o seguinte procedimento:

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount-graph.md)]

## <a name="add-a-collection"></a>Adicionar uma coleção

[!INCLUDE [cosmos-db-create-collection](../../includes/cosmos-db-create-graph.md)]

## <a name="get-apache-tinkerpop"></a>Obter o Apache TinkerPop

Obter o Apache TinkerPop efetuando o seguinte procedimento:

1. Remoto ao nó principal do cluster do HDInsight `ssh tinkerpop3-cosmosdb-demo-ssh.azurehdinsight.net`.

2. Clonar o código de origem TinkerPop3, criação localmente e instalá-lo para a cache do Maven.

    ```bash
    git clone https://github.com/apache/tinkerpop.git
    cd tinkerpop
    mvn clean install
    ```

3. Instalar o plug-in de Spark-Gremlin 

    a. A instalação do plug-in é processada pelo Grape. Preencha as informações de repositórios de Grape, pelo que pode transferir o plug-in e as respetivas dependências. 

      Criar o ficheiro de configuração grape se não estiver presente no `~/.groovy/grapeConfig.xml`. Utilize as seguintes definições:

    ```xml
    <ivysettings>
    <settings defaultResolver="downloadGrapes"/>
    <resolvers>
        <chain name="downloadGrapes">
        <filesystem name="cachedGrapes">
            <ivy pattern="${user.home}/.groovy/grapes/[organisation]/[module]/ivy-[revision].xml"/>
            <artifact pattern="${user.home}/.groovy/grapes/[organisation]/[module]/[type]s/[artifact]-[revision].[ext]"/>
        </filesystem>
        <ibiblio name="codehaus" root="http://repository.codehaus.org/" m2compatible="true"/>
        <ibiblio name="central" root="http://central.maven.org/maven2/" m2compatible="true"/>
        <ibiblio name="jitpack" root="https://jitpack.io" m2compatible="true"/>
        <ibiblio name="java.net2" root="http://download.java.net/maven/2/" m2compatible="true"/>
        <ibiblio name="apache-snapshots" root="http://repository.apache.org/snapshots/" m2compatible="true"/>
        <ibiblio name="local" root="file:${user.home}/.m2/repository/" m2compatible="true"/>
        </chain>
    </resolvers>
    </ivysettings>
    ``` 

    b. Inicie a consola de Gremlin `bin/gremlin.sh`.
        
    c. Instalar o plug-in de Spark-Gremlin com a versão 3.3.0-SNAPSHOT, que criou nos passos anteriores:

    ```bash
    $ bin/gremlin.sh

            \,,,/
            (o o)
    -----oOOo-(3)-oOOo-----
    plugin activated: tinkerpop.server
    plugin activated: tinkerpop.utilities
    plugin activated: tinkerpop.tinkergraph
    gremlin> :install org.apache.tinkerpop spark-gremlin 3.3.0-SNAPSHOT
    ==>loaded: [org.apache.tinkerpop, spark-gremlin, 3.3.0-SNAPSHOT] - restart the console to use [tinkerpop.spark]
    gremlin> :q
    $ bin/gremlin.sh

            \,,,/
            (o o)
    -----oOOo-(3)-oOOo-----
    plugin activated: tinkerpop.server
    plugin activated: tinkerpop.utilities
    plugin activated: tinkerpop.tinkergraph
    gremlin> :plugin use tinkerpop.spark
    ==>tinkerpop.spark activated
    ```

4. Verifique se `Hadoop-Gremlin` está ativado com `:plugin list`. Desativar este plug-in, porque este foi interferir com o plug-in de Spark-Gremlin `:plugin unuse tinkerpop.hadoop`.

## <a name="prepare-tinkerpop3-dependencies"></a>Preparar TinkerPop3 dependências

Quando incorporado TinkerPop3 no passo anterior, o processo solicitados também todas as dependências de jar para Spark e o Hadoop no diretório de destino. Utilize o v7 que pré-instaladas com HDI e extraia dependências adicionais apenas conforme necessário.

1. Vá para o diretório de destino Gremlin consola em `tinkerpop/gremlin-console/target/apache-tinkerpop-gremlin-console-3.3.0-SNAPSHOT-standalone`. 

2. Mover todos os v7 em `ext/` para `lib/`: `find ext/ -name '*.jar' -exec mv {} lib/ \;`.

3. Remover todos os jar bibliotecas em `lib/` que não estejam na lista seguinte:

    ```bash
    # TinkerPop3
    gremlin-console-3.3.0-SNAPSHOT.jar
    gremlin-core-3.3.0-SNAPSHOT.jar       
    gremlin-groovy-3.3.0-SNAPSHOT.jar     
    gremlin-shaded-3.3.0-SNAPSHOT.jar     
    hadoop-gremlin-3.3.0-SNAPSHOT.jar     
    spark-gremlin-3.3.0-SNAPSHOT.jar      
    tinkergraph-gremlin-3.3.0-SNAPSHOT.jar

    # Gremlin depedencies
    asm-3.2.jar                                
    avro-1.7.4.jar                             
    caffeine-2.3.1.jar                         
    cglib-2.2.1-v20090111.jar                  
    gbench-0.4.3-groovy-2.4.jar                
    gprof-0.3.1-groovy-2.4.jar                 
    groovy-2.4.9-indy.jar                      
    groovy-2.4.9.jar                           
    groovy-console-2.4.9.jar                   
    groovy-groovysh-2.4.9-indy.jar             
    groovy-json-2.4.9-indy.jar                 
    groovy-jsr223-2.4.9-indy.jar               
    groovy-sql-2.4.9-indy.jar                  
    groovy-swing-2.4.9.jar                     
    groovy-templates-2.4.9.jar                 
    groovy-xml-2.4.9.jar                       
    hadoop-yarn-server-nodemanager-2.7.2.jar   
    hppc-0.7.1.jar                             
    javatuples-1.2.jar                         
    jaxb-impl-2.2.3-1.jar                      
    jbcrypt-0.4.jar                            
    jcabi-log-0.14.jar                         
    jcabi-manifests-1.1.jar                    
    jersey-core-1.9.jar                        
    jersey-guice-1.9.jar                       
    jersey-json-1.9.jar                        
    jettison-1.1.jar                           
    scalatest_2.11-2.2.6.jar                   
    servlet-api-2.5.jar                        
    snakeyaml-1.15.jar                         
    unused-1.0.0.jar                           
    xml-apis-1.3.04.jar                        
    ```

## <a name="get-the-azure-cosmos-db-spark-connector"></a>Obter o conector Azure Cosmos DB Spark

1. Obter o conector Azure Cosmos DB Spark `azure-documentdb-spark-0.0.3-SNAPSHOT.jar` e Cosmos DB Java SDK `azure-documentdb-1.10.0.jar` de [conector Spark de base de dados de Cosmos do Azure no GitHub](https://github.com/Azure/azure-cosmosdb-spark/tree/master/releases/azure-cosmosdb-spark-0.0.3_2.0.2_2.11).

2. Em alternativa, pode criá-la localmente. Porque a versão mais recente do Spark Gremlin foi criada com o Spark 1.6.1 e não é compatível com o Spark 2.0.2, que é utilizado atualmente no conector Azure Cosmos DB Spark, pode compilar o código de TinkerPop3 mais recente e instalar manualmente o v7. Faça o seguinte:

    a. Clone o conector Azure Cosmos DB Spark.

    b. Crie TinkerPop3 (o tiver feito nos passos anteriores). Instale todas as v7 de TinkerPop 3.3.0-SNAPSHOT localmente.

    ```bash
    mvn install:install-file -Dfile="gremlin-core-3.3.0-SNAPSHOT.jar" -DgroupId=org.apache.tinkerpop -DartifactId=gremlin-core -Dversion=3.3.0-SNAPSHOT -Dpackaging=jar
    mvn install:install-file -Dfile="gremlin-groovy-3.3.0-SNAPSHOT.jar" -DgroupId=org.apache.tinkerpop -DartifactId=gremlin-groovy -Dversion=3.3.0-SNAPSHOT -Dpackaging=jar`
    mvn install:install-file -Dfile="gremlin-shaded-3.3.0-SNAPSHOT.jar" -DgroupId=org.apache.tinkerpop -DartifactId=gremlin-shaded -Dversion=3.3.0-SNAPSHOT -Dpackaging=jar`
    mvn install:install-file -Dfile="hadoop-gremlin-3.3.0-SNAPSHOT.jar" -DgroupId=org.apache.tinkerpop -DartifactId=hadoop-gremlin -Dversion=3.3.0-SNAPSHOT -Dpackaging=jar`
    mvn install:install-file -Dfile="spark-gremlin-3.3.0-SNAPSHOT.jar" -DgroupId=org.apache.tinkerpop -DartifactId=spark-gremlin -Dversion=3.3.0-SNAPSHOT -Dpackaging=jar`
    mvn install:install-file -Dfile="tinkergraph-gremlin-3.3.0-SNAPSHOT.jar" -DgroupId=org.apache.tinkerpop -DartifactId=tinkergraph-gremlin -Dversion=3.3.0-SNAPSHOT -Dpackaging=jar`
    ```

    c. Atualização `tinkerpop.version` `azure-documentdb-spark/pom.xml` para `3.3.0-SNAPSHOT`.
    
    d. Crie com o Maven. O v7 necessários é colocados em `target` e `target/alternateLocation`.

    ```bash
    git clone https://github.com/Azure/azure-cosmosdb-spark.git
    cd azure-documentdb-spark
    mvn clean package
    ```

3. Copie o v7 mencionadas anteriormente para um diretório local no ~ / azure-documentdb-spark:

    ```bash
    $ azure-documentdb-spark:
    mkdir ~/azure-documentdb-spark
    cp target/azure-documentdb-spark-0.0.3-SNAPSHOT.jar ~/azure-documentdb-spark
    cp target/alternateLocation/azure-documentdb-1.10.0.jar ~/azure-documentdb-spark
    ```

## <a name="distribute-the-dependencies-to-the-spark-worker-nodes"></a>Distribuir as dependências para os nós de trabalho do Spark 

1. Porque a transformação de dados de gráfico depende TinkerPop3, terá de distribuir as dependências relacionadas para todos os nós de trabalho de Spark.

2. Copie as dependências de Gremlin mencionadas anteriormente, jar de conector CosmosDB Spark e CosmosDB Java SDK para nós de trabalho efetuando o seguinte procedimento:

    a. Copiar todos os v7 para `~/azure-documentdb-spark`.

    ```bash
    $ /home/sshuser/tinkerpop/gremlin-console/target/apache-tinkerpop-gremlin-console-3.3.0-SNAPSHOT-standalone:
    cp lib/* ~/azure-documentdb-spark
    ```

    b. Obter a lista de todos os nós de trabalho de Spark, que pode encontrar no Dashboard do Ambari, além de `Spark2 Clients` lista o `Spark2` secção.

    c. Copie o diretório para cada um de nós.

    ```bash
    scp -r ~/azure-documentdb-spark sshuser@wn0-cosmos:/home/sshuser
    scp -r ~/azure-documentdb-spark sshuser@wn1-cosmos:/home/sshuser
    ...
    ```
    
## <a name="set-up-the-environment-variables"></a>Configurar as variáveis de ambiente

1. Localize a versão HDP do cluster do Spark. É o nome de diretório em `/usr/hdp/` (por exemplo, 2.5.4.2-7).

2. Defina hdp.version para todos os nós. No Dashboard do Ambari, aceda a **secção YARN** > **folhas** > **avançadas**, e, em seguida, efetue o seguinte: 
 
    a. No `Custom yarn-site`, adicione uma nova propriedade `hdp.version` com o valor da versão HDP no nó principal. 
     
    b. Guarde as configurações. Existem avisos que pode ignorar. 
     
    c. Reinicie os serviços YARN e Oozie como indicam os ícones de notificação.

3. Definir as seguintes variáveis de ambiente no nó principal (substituir os valores conforme adequado):

    ```bash
    export HADOOP_GREMLIN_LIBS=/home/sshuser/tinkerpop/gremlin-console/target/apache-tinkerpop-gremlin-console-3.3.0-SNAPSHOT-standalone/ext/spark-gremlin/lib
    export CLASSPATH=$CLASSPATH:$HADOOP_CONF_DIR:/usr/hdp/current/spark2-client/jars/*:/home/sshuser/azure-documentdb-spark/*
    export HDP_VERSION=2.5.4.2-7
    export HADOOP_HOME=${HADOOP_HOME:-/usr/hdp/current/hadoop-client}
    ```

## <a name="prepare-the-graph-configuration"></a>Preparar a configuração do gráfico

1. Criar um ficheiro de configuração com as definições de Spark e os parâmetros de ligação de base de dados do Azure Cosmos e colocá-la em `tinkerpop/gremlin-console/target/apache-tinkerpop-gremlin-console-3.3.0-SNAPSHOT-standalone/conf/hadoop/gremlin-spark.properties`.

    ```
    gremlin.graph=org.apache.tinkerpop.gremlin.hadoop.structure.HadoopGraph
    gremlin.hadoop.jarsInDistributedCache=true
    gremlin.hadoop.defaultGraphComputer=org.apache.tinkerpop.gremlin.spark.process.computer.SparkGraphComputer

    gremlin.hadoop.graphReader=com.microsoft.azure.documentdb.spark.gremlin.DocumentDBInputRDD
    gremlin.hadoop.graphWriter=com.microsoft.azure.documentdb.spark.gremlin.DocumentDBOutputRDD

    ####################################
    # SparkGraphComputer Configuration #
    ####################################
    spark.master=yarn
    spark.executor.memory=3g
    spark.executor.instances=6
    spark.serializer=org.apache.spark.serializer.KryoSerializer
    spark.kryo.registrator=org.apache.tinkerpop.gremlin.spark.structure.io.gryo.GryoRegistrator
    gremlin.spark.persistContext=true

    # Classpath for the driver and executors
    spark.driver.extraClassPath=/usr/hdp/current/spark2-client/jars/*:/home/sshuser/azure-documentdb-spark/*
    spark.executor.extraClassPath=/usr/hdp/current/spark2-client/jars/*:/home/sshuser/azure-documentdb-spark/*
    
    ######################################
    # DocumentDB Spark connector         #
    ######################################
    spark.documentdb.connectionMode=Gateway
    spark.documentdb.schema_samplingratio=1.0
    spark.documentdb.Endpoint=https://FILLIN.documents.azure.com:443/
    spark.documentdb.Masterkey=FILLIN
    spark.documentdb.Database=FILLIN
    spark.documentdb.Collection=FILLIN
    spark.documentdb.preferredRegions=FILLIN
    ```

2. Atualização do `spark.driver.extraClassPath` e `spark.executor.extraClassPath` para incluir o directório de v7 que é distribuída no passo anterior, neste caso `/home/sshuser/azure-documentdb-spark/*`.

3. Forneça os detalhes seguintes para Azure Cosmos DB:

    ```
    spark.documentdb.Endpoint=https://FILLIN.documents.azure.com:443/
    spark.documentdb.Masterkey=FILLIN
    spark.documentdb.Database=FILLIN
    spark.documentdb.Collection=FILLIN
    # Optional
    #spark.documentdb.preferredRegions=West\ US;West\ US\ 2
    ```
   
## <a name="load-the-tinkerpop-graph-and-save-it-to-azure-cosmos-db"></a>Carregar o gráfico de TinkerPop e guardá-lo a BD do Cosmos do Azure
Para demonstrar como manter um gráfico para a BD do Cosmos do Azure, este exemplo utiliza o TinkerPop predefinidas gráfico moderno TinkerPop. O gráfico é armazenado num formato de Kryo e é fornecido no repositório de TinkerPop.

1. Porque Gremlin estiver a executar no modo YARN, tem de se os dados de gráfico disponíveis no sistema de ficheiros Hadoop. Utilize os seguintes comandos para tornar um diretório e copie o ficheiro de gráfico local para a mesma. 

    ```bash
    $ tinkerpop:
    hadoop fs -mkdir /graphData
    hadoop fs -copyFromLocal ~/tinkerpop/data/tinkerpop-modern.kryo /graphData/tinkerpop-modern.kryo
    ```

2. Atualizar temporariamente o `gremlin-spark.properties` ficheiro a utilizar `GryoInputFormat` para ler o gráfico. Também indicar `inputLocation` como o diretório de criar, como o seguinte:

    ```
    gremlin.hadoop.graphReader=org.apache.tinkerpop.gremlin.hadoop.structure.io.gryo.GryoInputFormat
    gremlin.hadoop.inputLocation=/graphData/tinkerpop-modern.kryo
    ```

3. Inicie a consola de Gremlin e, em seguida, crie os seguintes passos de cálculo para manter os dados para a coleção de base de dados do Azure Cosmos configurado:  

    a. Criar o gráfico `graph = GraphFactory.open("conf/hadoop/gremlin-spark.properties")`.

    b. Utilizar SparkGraphComputer para escrita `graph.compute(SparkGraphComputer.class).result(GraphComputer.ResultGraph.NEW).persist(GraphComputer.Persist.EDGES).program(TraversalVertexProgram.build().traversal(graph.traversal().withComputer(Computer.compute(SparkGraphComputer.class)),"gremlin-groovy","g.V()").create(graph)).submit().get()`.

    ```bash
    gremlin> graph = GraphFactory.open("conf/hadoop/gremlin-spark.properties")
    ==>hadoopgraph[gryoinputformat->documentdboutputrdd]
    gremlin> hg = graph.
                compute(SparkGraphComputer.class).
                result(GraphComputer.ResultGraph.NEW).
                persist(GraphComputer.Persist.EDGES).
                program(TraversalVertexProgram.build().
                    traversal(graph.traversal().withComputer(Computer.compute(SparkGraphComputer.class)), "gremlin-groovy", "g.V()").
                    create(graph)).
                submit().
                get() 
    ==>result[hadoopgraph[documentdbinputrdd->documentdboutputrdd],memory[size:1]]
    ```

4. A partir do Explorador de dados, pode verificar que os dados foram continuados BD do Cosmos do Azure.

## <a name="load-the-graph-from-azure-cosmos-db-and-run-gremlin-queries"></a>O gráfico de carga da base de dados do Azure Cosmos e executar consultas Gremlin

1. Para carregar o gráfico, editar `gremlin-spark.properties` definir `graphReader` para `DocumentDBInputRDD`:

    ```
    gremlin.hadoop.graphReader=com.microsoft.azure.documentdb.spark.gremlin.DocumentDBInputRDD
    ```

2. Carregar o gráfico, atravessar os dados e executar consultas Gremlin com o mesmo efetuando o seguinte procedimento:

    a. Inicie a consola Gremlin `bin/gremlin.sh`.

    b. Criar o gráfico com a configuração `graph = GraphFactory.open('conf/hadoop/gremlin-spark.properties')`.

    c. Criar um transversal de gráfico com SparkGraphComputer `g = graph.traversal().withComputer(SparkGraphComputer)`.

    d. Execute as seguintes consultas de gráfico de Gremlin:

    ```bash
    gremlin> graph = GraphFactory.open("conf/hadoop/gremlin-spark.properties")
    ==>hadoopgraph[documentdbinputrdd->documentdboutputrdd]
    gremlin> g = graph.traversal().withComputer(SparkGraphComputer)
    ==>graphtraversalsource[hadoopgraph[documentdbinputrdd->documentdboutputrdd], sparkgraphcomputer]
    gremlin> g.V().count()
    ==>6
    gremlin> g.E().count()
    ==>6
    gremlin> g.V(1).out().values('name')
    ==>josh
    ==>vadas
    ==>lop
    gremlin> g.V().hasLabel('person').coalesce(values('nickname'), values('name'))
    ==>josh
    ==>peter
    ==>vadas
    ==>marko
    gremlin> g.V().hasLabel('person').
            choose(values('name')).
                option('marko', values('age')).
                option('josh', values('name')).
                option('vadas', valueMap()).
                option('peter', label())
    ==>josh
    ==>person
    ==>[name:[vadas],age:[27]]
    ==>29
    ```

> [!NOTE]
> Para ver um registo mais detalhado, defina o registo na `conf/log4j-console.properties` para um nível mais verboso.
>

## <a name="next-steps"></a>Passos seguintes

Este artigo de início rápido, aprendeu como trabalhar com gráficos ao combinar a base de dados do Azure Cosmos e Spark.

> [!div class="nextstepaction"]
