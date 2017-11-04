---
title: Topologia de Java de exemplo do Apache Storm - Azure HDInsight | Microsoft Docs
description: "Saiba como criar topologias do Apache Storm em Java através da criação de uma topologia de contagem de palavras de exemplo."
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
keywords: o Apache storm, o exemplo do apache storm, o java de storm, o exemplo de topologia do storm
ms.assetid: a8838f29-9c08-4fd9-99ef-26655d1bf6d7
ms.service: hdinsight
ms.devlang: java
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 09/28/2017
ms.author: larryfr
ms.custom: H1Hack27Feb2017,hdinsightactive,hdiseo17may2017
ms.openlocfilehash: 07cdc3d37bd845b054b4019d4823d39a1bc74079
ms.sourcegitcommit: f8437edf5de144b40aed00af5c52a20e35d10ba1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/03/2017
---
# <a name="create-an-apache-storm-topology-in-java"></a>Cria uma topologia do Apache Storm em Java

Saiba como criar uma topologia baseada em Java para o Apache Storm. Criar uma topologia do Storm que implementa uma aplicação word contagem. Utilizar o Maven para criar e o projeto do pacote. Em seguida, irá aprender a definir a topologia utilizando a estrutura do Flux.

> [!NOTE]
> A estrutura do Flux está disponível no Storm 0.10.0 ou posterior. O Storm 0.10.0 está disponível com o HDInsight 3.3 e 3.4.

Depois de concluir os passos neste documento, pode implementar a topologia ao Apache Storm no HDInsight.

> [!NOTE]
> Uma versão completa dos exemplos de topologia do Storm criada neste documento está disponível em [https://github.com/Azure-Samples/hdinsight-java-storm-wordcount](https://github.com/Azure-Samples/hdinsight-java-storm-wordcount).

## <a name="prerequisites"></a>Pré-requisitos

* [Kit de programação Java (JDK) versão 8](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html)

* [Maven (https://maven.apache.org/download.cgi)](https://maven.apache.org/download.cgi): Maven é um sistema de compilação do projeto para projetos de Java.

* Num editor de texto ou IDE.

## <a name="configure-environment-variables"></a>Configurar variáveis de ambiente

As seguintes variáveis de ambiente podem ser definidas quando instalar o Java e o JDK. No entanto, deve verificar que existe e que contêm os valores corretos para o seu sistema.

* **JAVA_HOME** -devem apontar para o diretório onde o ambiente de tempo de execução Java (JRE) está instalado. Por exemplo, uma distribuição Unix ou Linux, deve ter um valor semelhante a `/usr/lib/jvm/java-8-oracle`. No Windows, terá um valor semelhante a`c:\Program Files (x86)\Java\jre1.8`

* **CAMINHO** -deve conter os seguintes caminhos:

  * **JAVA_HOME** (ou o caminho equivalente)

  * **JAVA_HOME\bin** (ou o caminho equivalente)

  * O diretório onde está instalado o Maven

## <a name="create-a-maven-project"></a>Criar um projeto Maven

Na linha de comandos, utilize o seguinte comando para criar um projeto Maven designado **WordCount**:

```bash
mvn archetype:generate -DarchetypeArtifactId=maven-archetype-quickstart -DgroupId=com.microsoft.example -DartifactId=WordCount -DinteractiveMode=false
```

> [!NOTE]
> Se estiver a utilizar o PowerShell, tem de coloque o`-D` parâmetros com aspas duplas.
>
> `mvn archetype:generate "-DarchetypeArtifactId=maven-archetype-quickstart" "-DgroupId=com.microsoft.example" "-DartifactId=WordCount" "-DinteractiveMode=false"`

Este comando cria um diretório com o nome `WordCount` no local atual, que contém um projeto Maven básico. O `WordCount` diretório contém os seguintes itens:

* `pom.xml`: Contém definições para o projeto Maven.
* `src\main\java\com\microsoft\example`: Contém o código da aplicação.
* `src\test\java\com\microsoft\example`: Contém testes para a sua aplicação. 

### <a name="remove-the-generated-example-code"></a>Remover o código de exemplo gerado

Elimine o teste gerado e os ficheiros da aplicação:

* **src\test\java\com\microsoft\example\AppTest.Java**
* **src\main\java\com\microsoft\example\App.Java**

## <a name="add-maven-repositories"></a>Adicionar repositórios de Maven

HDInsight baseia-se na Hortonworks Data Platform (HDP), pelo que recomendamos que utilize o repositório de Hortonworks para transferir as dependências para os seus projetos do Apache Storm. No __pom.xml__ ficheiro, adicione o seguinte XML depois do `<url>http://maven.apache.org</url>` linha:

```xml
<repositories>
    <repository>
        <releases>
            <enabled>true</enabled>
            <updatePolicy>always</updatePolicy>
            <checksumPolicy>warn</checksumPolicy>
        </releases>
        <snapshots>
            <enabled>false</enabled>
            <updatePolicy>never</updatePolicy>
            <checksumPolicy>fail</checksumPolicy>
        </snapshots>
        <id>HDPReleases</id>
        <name>HDP Releases</name>
        <url>http://repo.hortonworks.com/content/repositories/releases/</url>
        <layout>default</layout>
    </repository>
    <repository>
        <releases>
            <enabled>true</enabled>
            <updatePolicy>always</updatePolicy>
            <checksumPolicy>warn</checksumPolicy>
        </releases>
        <snapshots>
            <enabled>false</enabled>
            <updatePolicy>never</updatePolicy>
            <checksumPolicy>fail</checksumPolicy>
        </snapshots>
        <id>HDPJetty</id>
        <name>Hadoop Jetty</name>
        <url>http://repo.hortonworks.com/content/repositories/jetty-hadoop/</url>
        <layout>default</layout>
    </repository>
</repositories>
```

## <a name="add-properties"></a>Adicionar propriedades

Maven permite-lhe definir os valores ao nível do projecto chamados propriedades. No __pom.xml__, adicione o seguinte texto após o `</repositories>` linha:

```xml
<properties>
    <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
    <!--
    This is a version of Storm from the Hortonworks repository that is compatible with HDInsight 3.5.
    -->
    <storm.version>1.1.0.2.6.1.9-1</storm.version>
</properties>
```

Agora, pode utilizar este valor noutras secções da `pom.xml`. Por exemplo, quando especificar a versão dos componentes Storm, pode utilizar `${storm.version}` em vez de disco rígido de um valor de codificação.

## <a name="add-dependencies"></a>Adicione as dependências

Adicione uma dependência para os componentes Storm. Abra o `pom.xml` de ficheiros e adicione o seguinte código no `<dependencies>` secção:

```xml
<dependency>
    <groupId>org.apache.storm</groupId>
    <artifactId>storm-core</artifactId>
    <version>${storm.version}</version>
    <!-- keep storm out of the jar-with-dependencies -->
    <scope>provided</scope>
</dependency>
```

No momento da compilação, Maven utiliza estas informações para procurar `storm-core` no repositório de Maven. Procura primeiro o repositório no seu computador local. Se os ficheiros não existe, o Maven transfere-los a partir do repositório de Maven público e armazena-os no repositório local.

> [!NOTE]
> Tenha em atenção o `<scope>provided</scope>` linha nesta secção. Esta definição indica o Maven para excluir **storm-core** de todos os ficheiros JAR que são criados, porque é fornecido pelo sistema.

## <a name="build-configuration"></a>Configuração de compilação

Maven plug-ins permitem-lhe personalizar as fases de compilação do projeto. Por exemplo, como o projeto é compilado ou como compactá-la num ficheiro JAR. Abra o `pom.xml` de ficheiros e adicione o seguinte código acima diretamente o `</project>` linha.

```xml
<build>
    <plugins>
    </plugins>
    <resources>
    </resources>
</build>
```

Esta secção é utilizada para adicionar plug-ins, recursos e outras opções de configuração de compilação. Para obter uma referência completa do **pom.xml** de ficheiros, consulte [http://maven.apache.org/pom.html](http://maven.apache.org/pom.html).

### <a name="add-plug-ins"></a>Adicionar plug-ins

Para topologias do Apache Storm implementadas em Java, o [Plug-in do Maven Exec](http://www.mojohaus.org/exec-maven-plugin/) é útil porque permite-lhe executar facilmente a topologia localmente no seu ambiente de desenvolvimento. Adicione o seguinte para o `<plugins>` secção o `pom.xml` ficheiro para incluir o plug-in de Exec Maven:

```xml
<plugin>
    <groupId>org.codehaus.mojo</groupId>
    <artifactId>exec-maven-plugin</artifactId>
    <version>1.4.0</version>
    <executions>
    <execution>
    <goals>
        <goal>exec</goal>
    </goals>
    </execution>
    </executions>
    <configuration>
    <executable>java</executable>
    <includeProjectDependencies>true</includeProjectDependencies>
    <includePluginDependencies>false</includePluginDependencies>
    <classpathScope>compile</classpathScope>
    <mainClass>${storm.topology}</mainClass>
    <cleanupDaemonThreads>false</cleanupDaemonThreads> 
    </configuration>
</plugin>
```

Outro útil de plug-in é o [Plug-in do Apache Maven compilador](http://maven.apache.org/plugins/maven-compiler-plugin/), que é utilizado para alterar as opções de compilação. As alterações de Java versão que utiliza o Maven para a origem e destino para a sua aplicação.

* Para o HDInsight __3.4 ou anterior__, definir a origem e destino versão de Java para __1.7__.

* Para o HDInsight __3.5__, definir a origem e destino versão de Java para __1.8__.

Adicione o seguinte texto no `<plugins>` secção o `pom.xml` ficheiro para incluir o plug-in do compilador do Apache Maven. Este exemplo Especifica 1.8, pelo que a versão do HDInsight de destino é 3.5.

```xml
<plugin>
    <groupId>org.apache.maven.plugins</groupId>
    <artifactId>maven-compiler-plugin</artifactId>
    <version>3.3</version>
    <configuration>
    <source>1.8</source>
    <target>1.8</target>
    </configuration>
</plugin>
```

### <a name="configure-resources"></a>Configurar recursos

A secção de recursos permite a inclusão de código não recursos, tais como ficheiros de configuração necessários pelos componentes na topologia. Neste exemplo, adicione o seguinte texto no `<resources>` secção a ' ficheiro pom.xml.

```xml
<resource>
    <directory>${basedir}/resources</directory>
    <filtering>false</filtering>
    <includes>
        <include>log4j2.xml</include>
    </includes>
</resource>
```

Este exemplo adiciona o diretório de recursos na raiz do projeto (`${basedir}`) como uma localização que contém os recursos e inclui o ficheiro denominado `log4j2.xml`. Este ficheiro é utilizado para configurar as informações que são registadas pela topologia.

## <a name="create-the-topology"></a>Criar a topologia

Uma topologia do Apache Storm baseado em Java consiste em três componentes, tem de criar (ou referência) como uma dependência.

* **Spouts**: lê origens de dados externos e emite fluxos de dados para a topologia.

* **Bolts**: efetua processamento nas fluxos emitidos pelo spouts ou outros bolts e emite um ou mais fluxos.

* **Topologia**: define a forma como o spouts bolts são dispostos e fornece o ponto de entrada para a topologia.

### <a name="create-the-spout"></a>Criar o spout

Para reduzir os requisitos para a configuração de origens de dados externas, o seguinte spout emite simplesmente frases aleatórios. É uma versão modificada de um spout que é fornecido com o [exemplos de Storm-Starter](https://github.com/apache/storm/blob/0.10.x-branch/examples/storm-starter/src/jvm/storm/starter).

> [!NOTE]
> Para obter um exemplo de um spout que lê a partir de uma origem de dados externas, consulte um dos seguintes exemplos:
>
> * [TwitterSampleSPout](https://github.com/apache/storm/blob/0.10.x-branch/examples/storm-starter/src/jvm/storm/starter/spout/TwitterSampleSpout.java): um spout de exemplo que lê a partir do Twitter
> * [Storm Kafka](https://github.com/apache/storm/tree/0.10.x-branch/external/storm-kafka): um spout que lê a partir da Kafka

Para spout, crie um ficheiro denominado `RandomSentenceSpout.java` no `src\main\java\com\microsoft\example` diretório e utilize o Java seguinte código como o conteúdo:

```java
package com.microsoft.example;

import org.apache.storm.spout.SpoutOutputCollector;
import org.apache.storm.task.TopologyContext;
import org.apache.storm.topology.OutputFieldsDeclarer;
import org.apache.storm.topology.base.BaseRichSpout;
import org.apache.storm.tuple.Fields;
import org.apache.storm.tuple.Values;
import org.apache.storm.utils.Utils;

import java.util.Map;
import java.util.Random;

//This spout randomly emits sentences
public class RandomSentenceSpout extends BaseRichSpout {
  //Collector used to emit output
  SpoutOutputCollector _collector;
  //Used to generate a random number
  Random _rand;

  //Open is called when an instance of the class is created
  @Override
  public void open(Map conf, TopologyContext context, SpoutOutputCollector collector) {
  //Set the instance collector to the one passed in
    _collector = collector;
    //For randomness
    _rand = new Random();
  }

  //Emit data to the stream
  @Override
  public void nextTuple() {
  //Sleep for a bit
    Utils.sleep(100);
    //The sentences that are randomly emitted
    String[] sentences = new String[]{ "the cow jumped over the moon", "an apple a day keeps the doctor away",
        "four score and seven years ago", "snow white and the seven dwarfs", "i am at two with nature" };
    //Randomly pick a sentence
    String sentence = sentences[_rand.nextInt(sentences.length)];
    //Emit the sentence
    _collector.emit(new Values(sentence));
  }

  //Ack is not implemented since this is a basic example
  @Override
  public void ack(Object id) {
  }

  //Fail is not implemented since this is a basic example
  @Override
  public void fail(Object id) {
  }

  //Declare the output fields. In this case, an sentence
  @Override
  public void declareOutputFields(OutputFieldsDeclarer declarer) {
    declarer.declare(new Fields("sentence"));
  }
}
```

> [!NOTE]
> Embora esta topologia utilize apenas um spout, outros utilizadores podem ter vários que feed de dados de diferentes origens para a topologia.

### <a name="create-the-bolts"></a>Criar os bolts

Bolts processam o processamento de dados. Esta topologia utiliza dois bolts:

* **SplitSentence**: divide frases emitidos pelo **RandomSentenceSpout** para palavras individuais.

* **WordCount**: contagens de cada palavra foi excedido o número de vezes.

> [!NOTE]
> Bolts podem fazer nada, por exemplo, computação, persistência ou falar com componentes externos.

Criar dois novos ficheiros, `SplitSentence.java` e `WordCount.java` no `src\main\java\com\microsoft\example` diretório. Utilize o seguinte texto como o conteúdo para os ficheiros:

#### <a name="splitsentence"></a>SplitSentence

```java
package com.microsoft.example;

import java.text.BreakIterator;

import org.apache.storm.topology.BasicOutputCollector;
import org.apache.storm.topology.OutputFieldsDeclarer;
import org.apache.storm.topology.base.BaseBasicBolt;
import org.apache.storm.tuple.Fields;
import org.apache.storm.tuple.Tuple;
import org.apache.storm.tuple.Values;

//There are a variety of bolt types. In this case, use BaseBasicBolt
public class SplitSentence extends BaseBasicBolt {

  //Execute is called to process tuples
  @Override
  public void execute(Tuple tuple, BasicOutputCollector collector) {
    //Get the sentence content from the tuple
    String sentence = tuple.getString(0);
    //An iterator to get each word
    BreakIterator boundary=BreakIterator.getWordInstance();
    //Give the iterator the sentence
    boundary.setText(sentence);
    //Find the beginning first word
    int start=boundary.first();
    //Iterate over each word and emit it to the output stream
    for (int end=boundary.next(); end != BreakIterator.DONE; start=end, end=boundary.next()) {
      //get the word
      String word=sentence.substring(start,end);
      //If a word is whitespace characters, replace it with empty
      word=word.replaceAll("\\s+","");
      //if it's an actual word, emit it
      if (!word.equals("")) {
        collector.emit(new Values(word));
      }
    }
  }

  //Declare that emitted tuples contain a word field
  @Override
  public void declareOutputFields(OutputFieldsDeclarer declarer) {
    declarer.declare(new Fields("word"));
  }
}
```

#### <a name="wordcount"></a>WordCount

```java
package com.microsoft.example;

import java.util.HashMap;
import java.util.Map;
import java.util.Iterator;

import org.apache.storm.Constants;
import org.apache.storm.topology.BasicOutputCollector;
import org.apache.storm.topology.OutputFieldsDeclarer;
import org.apache.storm.topology.base.BaseBasicBolt;
import org.apache.storm.tuple.Fields;
import org.apache.storm.tuple.Tuple;
import org.apache.storm.tuple.Values;
import org.apache.storm.Config;

// For logging
import org.apache.logging.log4j.Logger;
import org.apache.logging.log4j.LogManager;

//There are a variety of bolt types. In this case, use BaseBasicBolt
public class WordCount extends BaseBasicBolt {
  //Create logger for this class
  private static final Logger logger = LogManager.getLogger(WordCount.class);
  //For holding words and counts
  Map<String, Integer> counts = new HashMap<String, Integer>();
  //How often to emit a count of words
  private Integer emitFrequency;

  // Default constructor
  public WordCount() {
      emitFrequency=5; // Default to 60 seconds
  }

  // Constructor that sets emit frequency
  public WordCount(Integer frequency) {
      emitFrequency=frequency;
  }

  //Configure frequency of tick tuples for this bolt
  //This delivers a 'tick' tuple on a specific interval,
  //which is used to trigger certain actions
  @Override
  public Map<String, Object> getComponentConfiguration() {
      Config conf = new Config();
      conf.put(Config.TOPOLOGY_TICK_TUPLE_FREQ_SECS, emitFrequency);
      return conf;
  }

  //execute is called to process tuples
  @Override
  public void execute(Tuple tuple, BasicOutputCollector collector) {
    //If it's a tick tuple, emit all words and counts
    if(tuple.getSourceComponent().equals(Constants.SYSTEM_COMPONENT_ID)
            && tuple.getSourceStreamId().equals(Constants.SYSTEM_TICK_STREAM_ID)) {
      for(String word : counts.keySet()) {
        Integer count = counts.get(word);
        collector.emit(new Values(word, count));
        logger.info("Emitting a count of " + count + " for word " + word);
      }
    } else {
      //Get the word contents from the tuple
      String word = tuple.getString(0);
      //Have we counted any already?
      Integer count = counts.get(word);
      if (count == null)
        count = 0;
      //Increment the count and store it
      count++;
      counts.put(word, count);
    }
  }

  //Declare that this emits a tuple containing two fields; word and count
  @Override
  public void declareOutputFields(OutputFieldsDeclarer declarer) {
    declarer.declare(new Fields("word", "count"));
  }
}
```

### <a name="define-the-topology"></a>Definir a topologia

A topologia vincula os spouts e bolts em conjunto para um gráfico que define a forma como os dados fluem entre os componentes. Também fornece sugestões de paralelismo que utiliza o Storm ao criar instâncias dos componentes do cluster.

A imagem seguinte é um diagrama do gráfico de componentes para esta topologia básico.

![diagrama que mostra a disposição spouts e bolts](./media/apache-storm-develop-java-topology/wordcount-topology.png)

Para implementar a topologia, crie um ficheiro denominado `WordCountTopology.java` no `src\main\java\com\microsoft\example` diretório. Utilize o seguinte código Java como o conteúdo do ficheiro:

```java
package com.microsoft.example;

import org.apache.storm.Config;
import org.apache.storm.LocalCluster;
import org.apache.storm.StormSubmitter;
import org.apache.storm.topology.TopologyBuilder;
import org.apache.storm.tuple.Fields;

import com.microsoft.example.RandomSentenceSpout;

public class WordCountTopology {

  //Entry point for the topology
  public static void main(String[] args) throws Exception {
  //Used to build the topology
    TopologyBuilder builder = new TopologyBuilder();
    //Add the spout, with a name of 'spout'
    //and parallelism hint of 5 executors
    builder.setSpout("spout", new RandomSentenceSpout(), 5);
    //Add the SplitSentence bolt, with a name of 'split'
    //and parallelism hint of 8 executors
    //shufflegrouping subscribes to the spout, and equally distributes
    //tuples (sentences) across instances of the SplitSentence bolt
    builder.setBolt("split", new SplitSentence(), 8).shuffleGrouping("spout");
    //Add the counter, with a name of 'count'
    //and parallelism hint of 12 executors
    //fieldsgrouping subscribes to the split bolt, and
    //ensures that the same word is sent to the same instance (group by field 'word')
    builder.setBolt("count", new WordCount(), 12).fieldsGrouping("split", new Fields("word"));

    //new configuration
    Config conf = new Config();
    //Set to false to disable debug information when
    // running in production on a cluster
    conf.setDebug(false);

    //If there are arguments, we are running on a cluster
    if (args != null && args.length > 0) {
      //parallelism hint to set the number of workers
      conf.setNumWorkers(3);
      //submit the topology
      StormSubmitter.submitTopology(args[0], conf, builder.createTopology());
    }
    //Otherwise, we are running locally
    else {
      //Cap the maximum number of executors that can be spawned
      //for a component to 3
      conf.setMaxTaskParallelism(3);
      //LocalCluster is used to run locally
      LocalCluster cluster = new LocalCluster();
      //submit the topology
      cluster.submitTopology("word-count", conf, builder.createTopology());
      //sleep
      Thread.sleep(10000);
      //shut down the cluster
      cluster.shutdown();
    }
  }
}
```

### <a name="configure-logging"></a>Configurar o registo

O Storm utiliza o Apache Log4j para registar informações. Se não configurar o registo, a topologia emite informações de diagnóstico. Para controlar o que é registado, crie um ficheiro denominado `log4j2.xml` no `resources` diretório. Utilize o seguinte XML como o conteúdo do ficheiro.

```xml
<?xml version="1.0" encoding="UTF-8"?>
<Configuration>
<Appenders>
    <Console name="STDOUT" target="SYSTEM_OUT">
        <PatternLayout pattern="%d{HH:mm:ss} [%t] %-5level %logger{36} - %msg%n"/>
    </Console>
</Appenders>
<Loggers>
    <Logger name="com.microsoft.example" level="trace" additivity="false">
        <AppenderRef ref="STDOUT"/>
    </Logger>
    <Root level="error">
        <Appender-Ref ref="STDOUT"/>
    </Root>
</Loggers>
</Configuration>
```

Este XML configura um novo registo para o `com.microsoft.example` classe, que inclui os componentes nesta topologia de exemplo. O nível está definido para rastreio para este registo, o que captura todas as informações de registo emitidas pelo componentes nesta topologia.

O `<Root level="error">` secção configura o nível de raiz de registo (tudo não `com.microsoft.example`) para registar apenas informações de erro.

Para obter mais informações sobre como configurar o registo de Log4j, consulte [http://logging.apache.org/log4j/2.x/manual/configuration.html](http://logging.apache.org/log4j/2.x/manual/configuration.html).

> [!NOTE]
> Storm versão 0.10.0 e superior utilizar Log4j 2. x. As versões mais antigas do storm utilizado Log4j 1. x, o que é utilizado um formato diferente para a configuração de registo. Para obter informações sobre a configuração anterior, consulte [http://wiki.apache.org/logging-log4j/Log4jXmlFormat](http://wiki.apache.org/logging-log4j/Log4jXmlFormat).

## <a name="test-the-topology-locally"></a>Testar a topologia localmente

Depois de guardar os ficheiros, utilize o seguinte comando para testar a topologia localmente.

```bash
mvn compile exec:java -Dstorm.topology=com.microsoft.example.WordCountTopology
```

Como é executado, a topologia apresenta informações sobre o arranque. O texto seguinte é um exemplo de saída de contagem palavra:

    17:33:27 [Thread-12-count] INFO  com.microsoft.example.WordCount - Emitting a count of 56 for word snow
    17:33:27 [Thread-12-count] INFO  com.microsoft.example.WordCount - Emitting a count of 56 for word white
    17:33:27 [Thread-12-count] INFO  com.microsoft.example.WordCount - Emitting a count of 112 for word seven
    17:33:27 [Thread-16-count] INFO  com.microsoft.example.WordCount - Emitting a count of 195 for word the
    17:33:27 [Thread-30-count] INFO  com.microsoft.example.WordCount - Emitting a count of 113 for word and
    17:33:27 [Thread-30-count] INFO  com.microsoft.example.WordCount - Emitting a count of 57 for word dwarfs
    17:33:27 [Thread-12-count] INFO  com.microsoft.example.WordCount - Emitting a count of 57 for word snow

Este registo de exemplo indica que a palavra "e" tiver sido emitidos 113 vezes. A contagem continua a aceder cópias de segurança, desde que a topologia executa porque o spout continuamente emite as mesmas frases.

Não há um intervalo de 5 segundos entre emission das palavras e contagens. O **WordCount** componente está configurado para emitir apenas informações quando chega uma tupla de sinalização. Pedidos de marcas de escala que cadeias de identificação só são entregues a cada cinco segundos.

## <a name="convert-the-topology-to-flux"></a>Converter a topologia Flux

Flux é uma arquitetura nova disponível com o Storm 0.10.0 e superior, que lhe permite separar a configuração da implementação. Os componentes ainda estão definidos em Java, mas a topologia está definida através de um ficheiro YAML. Pode uma definição de topologia predefinido do pacote com o seu projeto ou utilizar um ficheiro autónomo ao submeter a topologia. Ao submeter a topologia para Storm, pode utilizar variáveis de ambiente ou ficheiros de configuração para preencher os valores na definição de topologia do YAML.

O ficheiro YAML define os componentes a utilizar para a topologia e os dados de fluxo entre eles. Pode incluir um ficheiro YAML como parte do ficheiro jar ou pode utilizar um ficheiro YAML externo.

Para obter mais informações sobre Flux, consulte [framework Flux (https://storm.apache.org/releases/0.10.0/flux.html)](https://storm.apache.org/releases/0.10.0/flux.html).

> [!WARNING]
> Devido a um [erros (https://issues.apache.org/jira/browse/STORM-2055)](https://issues.apache.org/jira/browse/STORM-2055) Storm 1.0.1, poderá ter de instalar um [ambiente de desenvolvimento do Storm](https://storm.apache.org/releases/1.0.1/Setting-up-development-environment.html) executar topologias Flux localmente.

1. Mover o `WordCountTopology.java` ficheiro fora do projeto. Anteriormente, este ficheiro definida a topologia, mas não é necessário, com Flux.

2. No `resources` directory, crie um ficheiro denominado `topology.yaml`. Utilize o seguinte texto como o conteúdo deste ficheiro.

    ```yaml
    name: "wordcount"       # friendly name for the topology

    config:                 # Topology configuration
      topology.workers: 1     # Hint for the number of workers to create

    spouts:                 # Spout definitions
    - id: "sentence-spout"
      className: "com.microsoft.example.RandomSentenceSpout"
      parallelism: 1      # parallelism hint

    bolts:                  # Bolt definitions
    - id: "splitter-bolt"
      className: "com.microsoft.example.SplitSentence"
      parallelism: 1
        
    - id: "counter-bolt"
      className: "com.microsoft.example.WordCount"
      constructorArgs:
        - 10
      parallelism: 1

    streams:                # Stream definitions
    - name: "Spout --> Splitter" # name isn't used (placeholder for logging, UI, etc.)
      from: "sentence-spout"       # The stream emitter
      to: "splitter-bolt"          # The stream consumer
      grouping:                    # Grouping type
        type: SHUFFLE
    
    - name: "Splitter -> Counter"
      from: "splitter-bolt"
      to: "counter-bolt"
      grouping:
        type: FIELDS
        args: ["word"]           # field(s) to group on
    ```

3. Efetue as seguintes alterações para o `pom.xml` ficheiro.
   
   * Adicione a seguinte dependência novo no `<dependencies>` secção:
     
        ```xml
        <!-- Add a dependency on the Flux framework -->
        <dependency>
            <groupId>org.apache.storm</groupId>
            <artifactId>flux-core</artifactId>
            <version>${storm.version}</version>
        </dependency>
        ```
   * Adicione o seguinte Plug-in para o `<plugins>` secção. Este plug-in processa a criação de um pacote (ficheiro jar) para o projeto e aplica-se algumas transformações específicas a Flux ao criar o pacote.
     
        ```xml
        <!-- build an uber jar -->
        <plugin>
            <groupId>org.apache.maven.plugins</groupId>
            <artifactId>maven-shade-plugin</artifactId>
            <version>2.3</version>
            <configuration>
                <transformers>
                    <!-- Keep us from getting a "can't overwrite file error" -->
                    <transformer implementation="org.apache.maven.plugins.shade.resource.ApacheLicenseResourceTransformer" />
                    <transformer implementation="org.apache.maven.plugins.shade.resource.ServicesResourceTransformer" />
                    <!-- We're using Flux, so refer to it as main -->
                    <transformer implementation="org.apache.maven.plugins.shade.resource.ManifestResourceTransformer">
                        <mainClass>org.apache.storm.flux.Flux</mainClass>
                    </transformer>
                </transformers>
                <!-- Keep us from getting a bad signature error -->
                <filters>
                    <filter>
                        <artifact>*:*</artifact>
                        <excludes>
                            <exclude>META-INF/*.SF</exclude>
                            <exclude>META-INF/*.DSA</exclude>
                            <exclude>META-INF/*.RSA</exclude>
                        </excludes>
                    </filter>
                </filters>
            </configuration>
            <executions>
                <execution>
                    <phase>package</phase>
                    <goals>
                        <goal>shade</goal>
                    </goals>
                </execution>
            </executions>
        </plugin>
        ```

   * No **exec-maven-Plug-in** `<configuration>` secção, altere o valor para `<mainClass>` para `org.apache.storm.flux.Flux`. Esta definição permite Flux processar localmente a executar a topologia de desenvolvimento.

   * No `<resources>` secção, adicione o seguinte para o `<includes>`. Este XML inclui o ficheiro YAML que define a topologia como parte do projeto.

        ```xml
        <include>topology.yaml</include>
        ```

## <a name="test-the-flux-topology-locally"></a>Testar a topologia de flux localmente

1. Utilize o seguinte para compilar e executar a topologia de Flux com o Maven:

    ```bash
    mvn compile exec:java -Dexec.args="--local -R /topology.yaml"
    ```

    Se estiver a utilizar o PowerShell, utilize o seguinte comando:

    ```bash
    mvn compile exec:java "-Dexec.args=--local -R /topology.yaml"
    ```

    > [!WARNING]
    > Se a sua topologia utiliza bits de Storm 1.0.1, este comando irá falhar. Esta falha é causada por [https://issues.apache.org/jira/browse/STORM-2055](https://issues.apache.org/jira/browse/STORM-2055). Em vez disso, [instalar Storm no seu ambiente de desenvolvimento](http://storm.apache.org/releases/0.10.0/Setting-up-development-environment.html) e utilize os seguintes passos:
    >
    > Se tiver [instalado Storm no seu ambiente de desenvolvimento](http://storm.apache.org/releases/0.10.0/Setting-up-development-environment.html), pode utilizar em vez disso, os seguintes comandos:
    >
    > ```bash
    > mvn compile package
    > storm jar target/WordCount-1.0-SNAPSHOT.jar org.apache.storm.flux.Flux --local -R /topology.yaml
    > ```

    O `--local` parâmetro executa a topologia no modo local no seu ambiente de desenvolvimento. O `-R /topology.yaml` parâmetro utiliza o `topology.yaml` recurso do ficheiro do ficheiro jar para definir a topologia.

    Como é executado, a topologia apresenta informações sobre o arranque. O texto seguinte é um exemplo de saída:

        17:33:27 [Thread-12-count] INFO  com.microsoft.example.WordCount - Emitting a count of 56 for word snow
        17:33:27 [Thread-12-count] INFO  com.microsoft.example.WordCount - Emitting a count of 56 for word white
        17:33:27 [Thread-12-count] INFO  com.microsoft.example.WordCount - Emitting a count of 112 for word seven
        17:33:27 [Thread-16-count] INFO  com.microsoft.example.WordCount - Emitting a count of 195 for word the
        17:33:27 [Thread-30-count] INFO  com.microsoft.example.WordCount - Emitting a count of 113 for word and
        17:33:27 [Thread-30-count] INFO  com.microsoft.example.WordCount - Emitting a count of 57 for word dwarfs

    Não há um atraso de 10 segundo entre lotes de informações com sessão iniciada.

2. Faça uma cópia do `topology.yaml` ficheiros do projeto. Nome do novo ficheiro `newtopology.yaml`. No `newtopology.yaml` ficheiro, localize a secção seguinte e altere o valor de `10` para `5`. Esta modificação altera o intervalo entre lotes de emissão de contagens de palavra de 10 segundos para 5.

    ```yaml
    - id: "counter-bolt"
    className: "com.microsoft.example.WordCount"
    constructorArgs:
    - 5
    parallelism: 1
    ```yaml

3. To run the topology, use the following command:

    ```bash
    mvn exec:java -Dexec.args="--local /path/to/newtopology.yaml"
    ```

    Em alternativa, se tiver o Storm no seu ambiente de desenvolvimento:

    ```bash
    storm jar target/WordCount-1.0-SNAPSHOT.jar org.apache.storm.flux.Flux --local /path/to/newtopology.yaml
    ```

    Alterar o `/path/to/newtopology.yaml` para o caminho para o ficheiro de newtopology.yaml que criou no passo anterior. Este comando utiliza o newtopology.yaml como a definição de topologia. Uma vez que não tenha incluímos o `compile` parâmetro Maven utiliza a versão do projeto foi compilado nos passos anteriores.

    Assim que for iniciada a topologia, deve reparar que o tempo entre emitidos lotes foi alterada para refletir o valor no newtopology.yaml. Para que possa ver o que pode alterar a configuração através de um ficheiro YAML sem ter de recompilar a topologia.

Para obter mais informações sobre estas e outras funcionalidades do Flux framework, consulte [Flux (https://storm.apache.org/releases/0.10.0/flux.html)](https://storm.apache.org/releases/0.10.0/flux.html).

## <a name="trident"></a>Trident

Trident é uma abstração de alto nível que é fornecida pela Storm. Suporta o processamento de estado monitorizado. A principal vantagem Trident é que pode garantir que cada mensagem que aciona a topologia é processada apenas uma vez. Sem utilizar Trident, a topologia só pode garantir que as mensagens são processadas pelo menos uma vez. Existem também outras diferenças, tais como componentes incorporados que podem ser utilizados em vez de criar bolts. Na verdade, bolts são substituídos pelos componentes de menor genérico, tais como filtros, projeções e funções.

Trident aplicações podem ser criadas utilizando projetos do Maven. Utilize os mesmos passos básicos, conforme apresentado anteriormente no artigo — apenas o código é diferente. Trident também não é possível (atualmente) ser utilizado com o framework Flux.

Para obter mais informações sobre Trident, consulte o [descrição geral da API de Trident](http://storm.apache.org/documentation/Trident-API-Overview.html).

## <a name="next-steps"></a>Passos Seguintes

Aprendeu como criar uma topologia do Storm utilizando Java. Agora saber como:

* [Implementar e gerir topologias Apache Storm no HDInsight](apache-storm-deploy-monitor-topology.md)

* [Desenvolver topologias c# para Apache Storm no HDInsight com o Visual Studio](apache-storm-develop-csharp-visual-studio-topology.md)

Pode encontrar mais exemplo topologias Storm, visitando [topologias de exemplo para Storm no HDInsight](apache-storm-example-topology.md).

