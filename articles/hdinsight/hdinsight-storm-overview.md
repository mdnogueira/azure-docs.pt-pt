---
title: "Introdução ao Apache Storm no HDInsight | Microsoft Docs"
description: "Obtenha uma introdução ao Apache Storm no HDInsight."
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 72d54080-1e48-4a5e-aa50-cce4ffc85077
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 01/11/2017
ms.author: larryfr
translationtype: Human Translation
ms.sourcegitcommit: 5e6ffbb8f1373f7170f87ad0e345a63cc20f08dd
ms.openlocfilehash: 0aa2a7075f64b353f6b052ab6b973a06622a9339
ms.lasthandoff: 03/24/2017


---
# <a name="introduction-to-apache-storm-on-hdinsight-real-time-analytics-for-hadoop"></a>Introdução ao Apache Storm no HDInsight: Análise em tempo real para o Hadoop

O Apache Storm no HDInsight permite-lhe criar soluções de análise em tempo real distribuídas no Azure.

O Apache Storm é um sistema de computação distribuída, com tolerância a falhas, open source, que permite processar os dados em tempo real com o Hadoop. As soluções do Storm também podem fornecer processamento garantido de dados, dada a capacidade de reprodução de dados que não foram processados com êxito à primeira vez.

## <a name="why-use-storm-on-hdinsight"></a>Porquê utilizar o Storm no HDInsight

O Apache Storm no HDInsight é um cluster gerido, integrado no ambiente do Azure. O Storm e outros componentes do Hadoop no HDInsight baseiam-se no Hortonworks Data Platform (HDP), ao passo que o sistema operativo do cluster é o Ubuntu (uma distribuição do Linux). Esta configuração proporciona uma plataforma compatível com ferramentas e serviços populares no ecossistema do Hadoop.

> [!IMPORTANT]
> O Linux é o único sistema operativo utilizado na versão 3.4 ou superior do HDInsight. Para obter mais informações, veja [HDInsight Deprecation on Windows (Descontinuação do HDInsight no Windows)](hdinsight-component-versioning.md#hdi-version-32-and-33-nearing-deprecation-date).

O Apache Storm no HDInsight oferece as seguintes grandes vantagens:

* Funciona como um serviço gerido com um SLA de tempo de disponibilidade de 99,9% .

* Personalização fácil, ao executar scripts face ao cluster durante ou após a criação. Para obter mais informações, veja [Customize HDInsight clusters using script action (Personalizar clusters do HDInsight com ações de script)](hdinsight-hadoop-customize-cluster-linux.md).

* Utilize uma linguagem à sua escolha: os componentes do Storm podem ser escritos em diversas linguagens, tais como **Java**, **c#** e **Python**.

  * Integração do Visual Studio com HDInsight para o desenvolvimento, a gestão e a monitorização das topologias C#. Para obter mais informações, veja [Develop C# Storm topologies with the HDInsight Tools for Visual Studio (Desenvolver topologias do Strom C# com as Ferramentas do HDInsight para Visual Studio)](hdinsight-storm-develop-csharp-visual-studio-topology.md).

  * Suporta a interface de Java **Trident**. Esta interface permite-lhe criar topologias Storm que suportam o processamento de mensagens "exatamente uma vez", persistência do arquivo de dados "transacional" e um conjunto de operações de análise de fluxo comuns.

* Aumentar e reduzir verticalmente o cluster de forma fácil: adicione ou remova nós de trabalho sem qualquer impacto nas topologias Storm em execução.

* Integração nos seguintes serviços do Azure:

    * Event Hubs
    * Rede Virtual
    * SQL Database
    * Storage do Azure
    * DocumentDB.

  * Combinar em segurança as capacidades de vários clusters do HDInsight através da utilização da Rede Virtual do Azure: crie pipelines de análise que utilizem clusters do HDInsight, do HBase ou do Hadoop.

Para obter uma lista de empresas que utiliza o Apache Storm como solução de análise em tempo real, consulte o artigo [Empresas que Utilizam o Apache Storm](https://storm.apache.org/documentation/Powered-By.html).

Para começar a utilizar o Storm, consulte o artigo [Get started with Storm on HDInsight (Introdução ao Storm no HDInsight)][gettingstarted].

### <a name="ease-of-creation"></a>Facilidade de criação

Pode aprovisionar um novo Storm no cluster do HDInsight em minutos. Especifique o nome do cluster, tamanho, conta de administrador e conta do Storage. O Azure cria o cluster, incluindo as topologias de exemplo e um dashboard de gestão Web.

> [!NOTE]
> Também pode aprovisionar de clusters de Storm através do [CLI do Azure ](../cli-install-nodejs.md) ou [Azure PowerShell](/powershell/azureps-cmdlets-docs).

Em menos de 15 minutos após submeter o pedido, tem um novo cluster Storm em execução e pronto para o primeiro pipeline de análise em tempo real.

### <a name="ease-of-use"></a>Facilidade de utilização

* __Conectividade Secure Shell__: pode aceder ao nós principais do cluster do HDInsight através da Internet ao utilizar SSH. O SSH permite executar comandos diretamente no cluster.

  Para obter mais informações, veja [Utilizar SSH com o HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).

* __Conectividade Web__: os clusters do HDInsight fornecem a IU da Web do Ambari. A IU Web do Ambari permite monitorizar, configurar e gerir facilmente serviços no seu cluster. O Storm no HDInsight também fornece a IU do Storm, através da qual pode monitorizar e gerir topologias Storm em execução a partir do browser.

  Para obter mais informações, veja [Manage HDInsight using the Ambari Web UI (Gerir o HDInsight com a IU da Web do Ambari)](hdinsight-hadoop-manage-ambari.md) e [Monitor and manage using the Storm UI (Monitorizar e gerir com a IU do Storm)](hdinsight-storm-deploy-monitor-topology-linux.md#monitor-and-manage-storm-ui).

* __Azure PowerShell e CLI d Azure__: quer o Azure PowerShell, quer a CLI do Azure, disponibilizam utilitários de linha de comandos que podem ser utilizados a partir do sistema cliente para funcionar com o HDInsight e com outros serviços do Azure.

* __Integração do Visual Studio__: as Ferramentas do Data Lake para Visual Studio incluem modelos de projetos para criar topologias Storm C#, bem como ferramentas para monitorizar o Storm no HDInsight. Pode criar, implementar, monitorizar e gerir as suas topologias C# a partir do Visual Studio.

  Para obter mais informações, veja [Develop C# Storm topologies with the HDInsight Tools for Visual Studio (Desenvolver topologias do Strom C# com as Ferramentas do HDInsight para Visual Studio)](hdinsight-storm-develop-csharp-visual-studio-topology.md).

* __Integração total com outros serviços do Azure__

  * Para desenvolvimento em __Java__, a Microsoft utiliza os componentes do Storm existentes para integração noutros serviços do Azure, sempre que possível. Em alguns casos, poderá ser necessário um componente ou solução específico para um serviço.

    * __Azure Data Lake Store__: as topologias baseadas em Java podem aceder ao Data Lake Store através do bolt do Storm-HDFS com um esquema de URI de `adl://`. Para obter um exemplo de como utilizar o bolt do Storm-HDFS, veja [Use Azure Data Lake Store with Apache Storm on HDInsight (Utilizar o Azure Data Lake Store com o Apache Storm no HDInsight)](https://docs.microsoft.com/en-us/azure/hdinsight/hdinsight-storm-write-data-lake-store).

    * __Armazenamento do Azure__ (quando utilizado como armazenamento do HDInsight): as topologias baseadas em Java podem aceder ao Armazenamento do Azure através do bolt do Storm-HDFS com um esquema de URI de `wasb://`.

    * __Eventos de Hubs do Azure__: podem ser acedidos através dos componentes EventHubSpout e EventHubBolt fornecidos pela Microsoft. Estes componentes são escritos em Java e fornecidos como um ficheiro .jar autónomo.

    Para obter mais informações sobre como desenvolver soluções em Java, veja [Develop a Java-based topology for Storm on HDInsight (Desenvolver uma topologia baseada em Java para o Storm no HDInsight)](hdinsight-storm-develop-java-topology.md).

  * Para desenvolvimento em __C#__, pode, geralmente, utilizar o .NET SDK do serviço do Azure. Em alguns casos, o SDK pode basear-se em arquiteturas que não estão disponíveis no Linux (o SO anfitrião do HDInsight 3.4 ou superior). Neste caso, pode utilizar componentes em Java a partir da solução em C#.

    * Estão incluídos exemplos de como trabalhar com a __BD SQL__, o __DocumentDB__, os __Hubs de Eventos__ e o __HBase__ como modelos nas Ferramentas do Azure Data Lake para Visual Studio. Para obter mais informações, veja [Desenvolver topologias C# para Storm no HDInsight](hdinsight-storm-develop-csharp-visual-studio-topology.md).

    * __Hubs de Eventos do Azure__: para obter um exemplo da utilização de componentes em Java de uma solução em C#, veja [Process events from Azure Event Hubs with Storm on HDInsight [C#] (Processar eventos dos Hubs de Eventos do Azure com Storm no HDInsight [C#])](hdinsight-storm-develop-csharp-event-hub-topology.md).

    Para obter mais informações sobre como desenvolver soluções em C#, veja [Develop a C# topology for Storm on HDInsight (Desenvolver uma topologia C# para o Storm no HDInsight)](hdinsight-storm-develop-csharp-visual-studio-topology.md).

### <a name="reliability"></a>Fiabilidade

O Apache Storm garante sempre que cada mensagem a receber é totalmente processada, mesmo quando a análise de dados está distribuída por centenas de nós.

O **nó Nimbus** fornece uma funcionalidade semelhante ao JobTracker do Hadoop e atribui tarefas a outros nós do cluster através do **Zookeeper**. Os nós de Zookeeper fornecem coordenação de problemas para o cluster e facilitam a comunicação entre Nimbus e o processo do **Supervisor** nos nós de trabalho. Se um nó de processamento deixa de funcionar, o nó Nimbus é informado e atribui a tarefa e os dados associados a um outro nó.

A configuração predefinida para o Apache Storm é com apenas um nó Nimbus. O Storm no HDInsight executa dois nós Nimbus. Se o nó principal falhar, o cluster do HDInsight muda para o nó secundário enquanto o nó principal se recupera.

![Diagrama de nimbus, zookeeper e supervisor](./media/hdinsight-storm-overview/nimbus.png)

### <a name="scale"></a>Escala

Embora possa especificar o número de nós do cluster durante a criação, pode querer aumentar ou diminuir o cluster para que coincida à carga de trabalho. Todos os clusters do HDInsight permitem alterar o número de nós do cluster, mesmo durante o processamento de dados.

> [!NOTE]
> Para tirar partido dos novos nós adicionados através do dimensionamento, tem de reequilibrar as topologias iniciadas antes do aumento do tamanho de cluster.

### <a name="support"></a>Suporte

Storm no HDInsight vem com suporte completo de nível empresarial 24 horas por dia, 7 dias por semana. Storm no HDInsight tem também um SLA de 99,9%. Isto significa que podemos garantir que o cluster tem conectividade externa, pelo menos, 99,9% do tempo.

## <a name="common-use-cases-for-real-time-analytics"></a>Casos de utilização comuns para análise em tempo real

Seguem-se alguns cenários comuns nos quais poderá utilizar o Apache Storm no HDInsight. Para obter informações sobre cenários reais, veja [Como as empresas estão a utilizar o Storm](https://storm.apache.org/documentation/Powered-By.html).

* Internet das Coisas (IoT)
* Deteção de fraudes
* Análise social
* Extrair, Transformar, Carregar (ETL)
* Monitorização de rede
* Pesquisa
* Mobile Engagement

## <a name="how-is-data-in-hdinsight-storm-processed"></a>Como são processados os dados do Storm no HDInsight

O Apache Storm executa **topologias** em vez dos trabalhos de MapReduce com que poderá estar familiarizado no HDInsight ou Hadoop. Um cluster Storm no HDInsight contêm dois tipos de nós: nós principais que executam **Nimbus** e nós de trabalho que executam **Supervisor**.

* **Nimbus**: Semelhante ao JobTracker no Hadoop, é responsável pela distribuição de código por todo o cluster, atribuir tarefas a máquinas virtuais e monitorizar falhas. HDInsight fornece dois nós Nimbus, por isso, não há nenhum ponto único de falha de Storm no HDInsight
* **Supervisor**: O supervisor para cada nó de trabalho é responsável por iniciar e parar **processos de trabalho** no nó.
* **Processo de trabalho**: executa um subconjunto de uma **topologia**. Uma topologia de execução distribuiu-se por vários processos de trabalho em todo o cluster.
* **Topologia**: Define um gráfico de computação que processa **fluxos** de dados. Ao contrário dos trabalhos de MapReduce, topologias estão em execução até as pararmos.
* **Fluxo**: Uma coleção não ligada de **tuplas**. **Spouts** e **bolts**produzem fluxos e os **bolts** consomem-los.
* **Tupla**: uma lista com nomes de valores escritos de forma dinâmica.
* **Spout**: consome dados de uma origem de dados e emite um ou vários **fluxos**.

  > [!NOTE]
  > Muitas vezes, os dados são lidos a partir de filas, como o Kafka ou os hubs de Eventos do Azure. Em caso de falha, a fila garante que os dados persistem.

* **Bolt**: Consome **fluxos**, efetua processamento nas **tuplas** e pode emitir **fluxos**. Os bolts também são responsáveis pela escrita de dados no armazenamento externo, como uma fila, HDInsight, HBase, um blob ou outro arquivo de dados.
* **Apache Thrift**: Uma estrutura de software para desenvolvimento dimensionável de serviços em várias linguagens. Permite-lhe criar serviços que funcionam entre C++, Java, Python, PHP, Ruby, Erlang, Perl, Haskell, c#, Cocoa, JavaScript, Node.js, Smalltalk, etc.

Para obter mais informações sobre os componentes Storm, consulte o [Storm tutorial (Tutorial Storm)][apachetutorial] em apache.org.

## <a name="what-programming-languages-can-i-use"></a>Que linguagens de programação posso utilizar

### <a name="c35"></a>C&#35;

As Ferramentas do Data Lake para Visual Studio permitem que os programadores de .NET concebam e implementem uma topologia em C#. Também pode criar topologias híbridas que utilizam componentes de Java e C#.

Para obter mais informações, consulte o artigo [Desenvolver topologias C# para Apache Storm no HDInsight com Visual Studio](hdinsight-storm-develop-csharp-visual-studio-topology.md).

### <a name="java"></a>Java

A maioria dos exemplos de Java que encontra é em Java simples ou Trident. Trident é uma abstração de alto nível que facilita a criação de coisas como associações, agregações, agrupamentos e filtragens. No entanto, o Trident funciona em lotes de tuplas, enquanto que uma solução de Java sem processamento processará uma tupla de cada vez.

Para mais informações sobre Trident, consulte o [tutorial do Trident ](https://storm.apache.org/documentation/Trident-tutorial.html) em apache.org.

Para obter exemplos de topologias de Java e Trident, consulte a [lista das topologias de exemplo de Storm](hdinsight-storm-example-topology.md) ou os exemplos de storm-starter no cluster do HDInsight.

Os exemplos para começar com o Storm estão localizados no diretório **/usr/hdp/current/storm-client/contrib/storm-starter** do seu cluster do HDInsight.

### <a name="python"></a>Python

Para obter um exemplo de utilização de componentes de Python, veja [Desenvolver topologias do Storm com Python no HDInsight](hdinsight-storm-develop-python-topology.md).

## <a name="what-are-some-common-development-patterns"></a>Quais são alguns padrões comuns de desenvolvimento

### <a name="guaranteed-message-processing"></a>Processamento de mensagens garantido

O Storm pode fornecer diferentes níveis de processamento garantido de mensagens . Por exemplo, uma aplicação Storm básica pode garantir o processamento pelo-menos-uma vez e o Trident pode garantir o processamento exatamente uma vez.

Para obter mais informações, consulte o artigo [Garantias no processamento de dados](https://storm.apache.org/about/guarantees-data-processing.html) em apache.org.

### <a name="ibasicbolt"></a>IBasicBolt

O padrão de ler uma tupla de entrada, emitir zero ou várias tuplas e, em seguida, confirmar a tupla de entrada imediatamente no final do método de execução é muito comum. O Storm fornece a interface [IBasicBolt](https://storm.apache.org/apidocs/backtype/storm/topology/IBasicBolt.html) para automatizar este padrão.

### <a name="joins"></a>Associações

O modo como os fluxos de dados são associados varia consoante as aplicações. Por exemplo, pode associar cada tupla de vários fluxos num novo fluxo ou pode associar apenas lotes de tuplas numa janela específica. De qualquer forma, a associação pode efetuar-se através de [fieldsGrouping](http://javadox.com/org.apache.storm/storm-core/0.9.1-incubating/backtype/storm/topology/InputDeclarer.html#fieldsGrouping%28java.lang.String,%20backtype.storm.tuple.Fields%29), que é um modo de definir como as tuplas são encaminhadas para bolts.

No exemplo de Java a seguir, utiliza-se o fieldsGrouping para encaminhar tuplas que têm origem nos componentes "1", "2" e "3" para o bolt **MyJoiner**.

    builder.setBolt("join", new MyJoiner(), parallelism) .fieldsGrouping("1", new Fields("joinfield1", "joinfield2")) .fieldsGrouping("2", new Fields("joinfield1", "joinfield2")) .fieldsGrouping("3", new Fields("joinfield1", "joinfield2"));

### <a name="batching"></a>Lotes

O Storm fornece um mecanismo de temporização interno, conhecido como "tupla de sinalização", que pode ser utilizado para emitir um lote a cada X segundos.

Para obter um exemplo de como utilizar uma tupla de sinalização a partir de um componente em C#, veja [PartialBoltCount.cs](https://github.com/hdinsight/hdinsight-storm-examples/blob/3b2c960549cac122e8874931df4801f0934fffa7/EventCountExample/EventCountTopology/src/main/java/com/microsoft/hdinsight/storm/examples/PartialCountBolt.java).

Se estiver a utilizar Trident, este baseia-se no processamento de lotes de tuplas.

### <a name="caching"></a>Colocação em cache

A colocação em cache de memória utiliza-se frequentemente como um mecanismo para acelerar o processamento porque mantém os itens utilizados com maior frequência na memória. Como uma topologia é distribuída por múltiplos nós e processos em cada nó, deve considerar a utilização de [fieldsGrouping](http://javadox.com/org.apache.storm/storm-core/0.9.1-incubating/backtype/storm/topology/InputDeclarer.html#fieldsGrouping%28java.lang.String,%20backtype.storm.tuple.Fields%29). O `fieldsGrouping` garante que as tuplas contém os campos utilizados na pesquisa de cache são sempre encaminhados para o mesmo processo. Esta funcionalidade de agrupamento evita a duplicação de entradas em cache entre os processos.

### <a name="streaming-top-n"></a>Transmissão os principais valores N

Quando a topologia depende do cálculo de um valor "N principal", deve calcular o valor N principal em paralelo e, em seguida, intercalar o resultado desses cálculos num valor global. Isto pode ser efetuado com [fieldsGrouping](http://javadox.com/org.apache.storm/storm-core/0.9.1-incubating/backtype/storm/topology/InputDeclarer.html#fieldsGrouping%28java.lang.String,%20backtype.storm.tuple.Fields%29) para encaminhar por campo para processamento paralelo e, em seguida, encaminhar para um bolt que determina globalmente o valor N principal.

Para obter um exemplo de cálculo de um valor "N principal", veja o exemplo [RollingTopWords](https://github.com/nathanmarz/storm-starter/blob/master/src/jvm/storm/starter/RollingTopWords.java).

## <a name="what-type-of-logging-does-storm-use"></a>Que tipo de registo utiliza o Storm

O Storm utiliza o Apache Log4j para registar informações. Por predefinição, é registada uma grande quantidade de dados, podendo ser difícil organizar-se nas informações. Pode incluir um ficheiro de configuração de registo como parte da topologia do Storm para controlar o comportamento dos registos.

Para uma topologia de exemplo que demonstra como configurar o registo, veja o exemplo [WordCount baseado em Java](hdinsight-storm-develop-java-topology.md) para Storm no HDInsight.

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre as soluções de análise em tempo real com Apache Storm no HDInsight:

* [Introdução ao Storm no HDInsight][gettingstarted]
* [Topologias de exemplo para Storm no HDInsight](hdinsight-storm-example-topology.md)

[stormtrident]: https://storm.apache.org/documentation/Trident-API-Overview.html
[samoa]: http://yahooeng.tumblr.com/post/65453012905/introducing-samoa-an-open-source-platform-for-mining
[apachetutorial]: https://storm.apache.org/documentation/Tutorial.html
[gettingstarted]: hdinsight-apache-storm-tutorial-get-started-linux.md

