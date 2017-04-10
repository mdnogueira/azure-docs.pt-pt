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
ms.date: 03/31/2017
ms.author: larryfr
translationtype: Human Translation
ms.sourcegitcommit: 5cce99eff6ed75636399153a846654f56fb64a68
ms.openlocfilehash: 1155f534869f240c1567fa8b791dbcb7750c8a40
ms.lasthandoff: 03/31/2017


---
# <a name="introduction-to-apache-storm-on-hdinsight-real-time-analytics-for-hadoop"></a>Introdução ao Apache Storm no HDInsight: Análise em tempo real para o Hadoop

O Apache Storm no HDInsight permite-lhe criar soluções de análise em tempo real distribuídas no Azure.

O Apache Storm é um sistema de computação distribuída, com tolerância a falhas, open source, que permite processar os dados em tempo real com o Hadoop. As soluções do Storm também podem fornecer processamento garantido de dados, dada a capacidade de reprodução de dados que não foram processados com êxito à primeira vez.

## <a name="how-does-storm-work"></a>Como funciona o Storm

O Apache Storm executa **topologias** em vez dos trabalhos de MapReduce com que poderá estar familiarizado no HDInsight ou Hadoop. As topologias são compostas por múltiplos componentes que são dispostos num grafo dirigido acíclico (DAG). O diagrama seguinte ilustra a forma como os dados fluem entre componentes numa topologia de contagem de palavras básica

![Exemplo da disposição dos componentes numa topologia Storm](./media/hdinsight-storm-overview/wordcount-topology.png)

* Os componentes __spout__ trazem dados para uma topologia. Emitem um ou mais fluxos para a topologia

* Os componentes __bolt__ consomem fluxos emitidos por spouts ou por outros bolts. Opcionalmente, os bolts podem emitir fluxos novos para a topologia. Também são responsáveis pela escrita de dados no armazenamento persistente, como o HDFS ou o HBase.

## <a name="why-use-storm-on-hdinsight"></a>Porquê utilizar o Storm no HDInsight

O Apache Storm no HDInsight oferece as seguintes grandes vantagens:

* Funciona como um serviço gerido com um SLA de tempo de disponibilidade de 99,9% .

* Personalização fácil, ao executar scripts face ao cluster durante ou após a criação. Para obter mais informações, veja [Customize HDInsight clusters using script action (Personalizar clusters do HDInsight com ações de script)](hdinsight-hadoop-customize-cluster-linux.md).

* Utilize uma linguagem à sua escolha: os componentes do Storm podem ser escritos em diversas linguagens, tais como **Java**, **c#** e **Python**.

    * Integração do Visual Studio com HDInsight para o desenvolvimento, a gestão e a monitorização das topologias C#. Para obter mais informações, veja [Develop C# Storm topologies with the HDInsight Tools for Visual Studio (Desenvolver topologias do Strom C# com as Ferramentas do HDInsight para Visual Studio)](hdinsight-storm-develop-csharp-visual-studio-topology.md).

    * Suporta a interface de Java **Trident**. Esta interface permite-lhe criar topologias Storm que suportam o processamento de mensagens "exatamente uma vez", persistência do arquivo de dados transacional e um conjunto de operações de análise de fluxo comuns.

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

## <a name="ease-of-creation"></a>Facilidade de criação

Pode aprovisionar um novo Storm no cluster do HDInsight em minutos. Para obter mais informações sobre como criar clusters do Storm, veja [Introdução ao Storm no HDInsight](hdinsight-apache-storm-tutorial-get-started-linux.md).

## <a name="ease-of-use"></a>Facilidade de utilização

* __Conectividade Secure Shell__: pode aceder ao nós principais do cluster do HDInsight através da Internet ao utilizar SSH. O SSH permite executar comandos diretamente no cluster.

  Para obter mais informações, veja [Utilizar SSH com o HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).

* __Conectividade Web__: os clusters do HDInsight fornecem a IU da Web do Ambari. A IU Web do Ambari permite monitorizar, configurar e gerir facilmente serviços no seu cluster. O Storm no HDInsight também fornece a IU do Storm, através da qual pode monitorizar e gerir topologias Storm em execução a partir do browser.

  Para obter mais informações, veja [Manage HDInsight using the Ambari Web UI (Gerir o HDInsight com a IU da Web do Ambari)](hdinsight-hadoop-manage-ambari.md) e [Monitor and manage using the Storm UI (Monitorizar e gerir com a IU do Storm)](hdinsight-storm-deploy-monitor-topology-linux.md#monitor-and-manage-storm-ui).

* __Azure PowerShell e CLI d Azure__: quer o Azure PowerShell, quer a CLI do Azure, disponibilizam utilitários de linha de comandos que podem ser utilizados a partir do sistema cliente para funcionar com o HDInsight e com outros serviços do Azure.

* __Integração do Visual Studio__: as Ferramentas do Data Lake para Visual Studio incluem modelos de projetos para criar topologias Storm em C# através da utilização da arquitetura SCP.Net. As Ferramentas do Data Lake também proporcionam ferramentas para implementar, monitorizar e gerir soluções com o Storm no HDInsight.

  Para obter mais informações, veja [Develop C# Storm topologies with the HDInsight Tools for Visual Studio (Desenvolver topologias do Strom C# com as Ferramentas do HDInsight para Visual Studio)](hdinsight-storm-develop-csharp-visual-studio-topology.md).

## <a name="integration-with-other-azure-services"></a>Integração noutros serviços do Azure

* __Azure Data Lake Store__: para obter um exemplo de como utilizar o Data Lake Store com o Storm, veja [Use Azure Data Lake Store with Apache Storm on HDInsight](hdinsight-storm-write-data-lake-store.md) (Utilizar o Azure Data Lake Store com o Apache Storm no HDInsight).

* __Hubs de Eventos do Azure__: para obter um exemplo de como utilizar os Hubs de Eventos do Azure com o Storm, veja os documentos seguintes:

    * [Develop a Java-based topology for Storm on HDInsight](hdinsight-storm-develop-java-topology.md) (Desenvolver uma topologia baseada em Java para o Storm no HDInsight)

    * [Process events from Azure Event Hubs with Storm on HDInsight (C#)](hdinsight-storm-develop-csharp-event-hub-topology.md) (Processar eventos dos Hubs de Eventos do Azure com o Storm no HDInsight [C#])

* Estão incluídos exemplos de como trabalhar com a __BD SQL__, o __DocumentDB__, os __Hubs de Eventos__ e o __HBase__ como modelos nas Ferramentas do Azure Data Lake para Visual Studio. Para obter mais informações, veja [Desenvolver topologias C# para Storm no HDInsight](hdinsight-storm-develop-csharp-visual-studio-topology.md).

## <a name="reliability"></a>Fiabilidade

O Apache Storm garante sempre que cada mensagem a receber é totalmente processada, mesmo quando a análise de dados está distribuída por centenas de nós.

O **nó Nimbus** fornece uma funcionalidade semelhante ao JobTracker do Hadoop e atribui tarefas a outros nós do cluster através do **Zookeeper**. Os nós de Zookeeper fornecem coordenação de problemas para o cluster e facilitam a comunicação entre Nimbus e o processo do **Supervisor** nos nós de trabalho. Se um nó de processamento deixa de funcionar, o nó Nimbus é informado e atribui a tarefa e os dados associados a um outro nó.

A configuração predefinida para o Apache Storm é com apenas um nó Nimbus. O Storm no HDInsight executa dois nós Nimbus. Se o nó principal falhar, o cluster do HDInsight muda para o nó secundário enquanto o nó principal se recupera.

![Diagrama de nimbus, zookeeper e supervisor](./media/hdinsight-storm-overview/nimbus.png)

## <a name="scale"></a>Escala

Embora possa especificar o número de nós do cluster durante a criação, pode querer aumentar ou diminuir o cluster para que coincida à carga de trabalho. Todos os clusters do HDInsight permitem alterar o número de nós do cluster, mesmo durante o processamento de dados.

> [!NOTE]
> Para tirar partido dos novos nós adicionados através do dimensionamento, tem de reequilibrar as topologias iniciadas antes do aumento do tamanho de cluster.

## <a name="support"></a>Suporte

Storm no HDInsight vem com suporte completo de nível empresarial 24 horas por dia, 7 dias por semana. Storm no HDInsight tem também um SLA de 99,9%. Isto significa que podemos garantir que o cluster tem conectividade externa, pelo menos, 99,9% do tempo.

Para obter mais informações, veja o [suporte do Azure](https://azure.microsoft.com/support/options/).

## <a name="common-use-cases"></a>Casos de utilização comuns

Seguem-se alguns cenários comuns nos quais poderá utilizar o Apache Storm no HDInsight. 

* Internet das Coisas (IoT)
* Deteção de fraudes
* Análise social
* Extrair, Transformar, Carregar (ETL)
* Monitorização de rede
* Pesquisa
* Mobile Engagement

Para obter informações sobre cenários reais, veja o documento [How companies are using Storm](https://storm.apache.org/documentation/Powered-By.html) (Como as empresas utilizam o Storm).

## <a name="development"></a>Desenvolvimento

As __Ferramentas do Data Lake para Visual Studio__ permitem que os programadores de .NET concebam e implementem topologias em __C#__. Também pode criar topologias híbridas que utilizam componentes de Java e C#.

Para obter mais informações, veja [Develop C# topologies for Storm on HDInsight using Visual Studio](hdinsight-storm-develop-csharp-visual-studio-topology.md) (Desenvolver topologias em C# para o Storm no HDInsight com o Visual Studio).

Também pode desenvolver soluções __Java__ com o IDE à sua escolha. Para obter mais informações, veja [Develop Java topologies for Storm on HDInsight](hdinsight-storm-develop-java-topology.md) (Desenvolver topologias em Java para Storm no HDInsight).

Também pode ser utilizado Python para desenvolver componentes do Storm. Para obter mais informações, veja [Develop Storm topologies using Python on HDInsight](hdinsight-storm-develop-python-topology.md) (Desenvolver topologias Storm com Python no HDInsight).

## <a name="common-development-patterns"></a>Padrões comuns de desenvolvimento

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

Quando a sua topologia depende do cálculo de valores “principais N”, deve calculá-los em paralelo. Em seguida, intercale o resultado desses cálculos num valor global. Esta operação pode ser efetuada com [fieldsGrouping](http://javadox.com/org.apache.storm/storm-core/0.9.1-incubating/backtype/storm/topology/InputDeclarer.html#fieldsGrouping%28java.lang.String,%20backtype.storm.tuple.Fields%29) para encaminhar por campo para processamento paralelo e, em seguida, encaminhar para um bolt que determina globalmente o valor N principal.

Para obter um exemplo de cálculo de um valor "N principal", veja o exemplo [RollingTopWords](https://github.com/nathanmarz/storm-starter/blob/master/src/jvm/storm/starter/RollingTopWords.java).

## <a name="logging"></a>Registo

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

