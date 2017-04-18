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
ms.sourcegitcommit: 6ea03adaabc1cd9e62aa91d4237481d8330704a1
ms.openlocfilehash: 73e524bf9e7d51cee841a3c5599ab33aa31cbc37
ms.lasthandoff: 04/06/2017


---
# <a name="introduction-to-apache-storm-on-hdinsight-real-time-analytics-for-hadoop"></a>Introdução ao Apache Storm no HDInsight: Análise em tempo real para o Hadoop

Com o Apache Storm no Azure HDInsight, pode criar soluções de análise em tempo real distribuídas.

O Storm é um sistema de computação distribuído com tolerância a falhas e de código aberto. Pode utilizar o Storm para processar dados em tempo real com o Hadoop. As soluções do Storm também podem fornecer processamento garantido de dados, dada a capacidade de reprodução de dados que não foram processados com êxito à primeira vez.

## <a name="how-does-storm-work"></a>Como funciona o Storm?

O Storm executa topologias em vez das tarefas de MapReduce com que poderá estar familiarizado no HDInsight ou Hadoop. As topologias são compostas por múltiplos componentes que são dispostos num grafo dirigido acíclico (DAG). O diagrama seguinte ilustra a forma como os dados fluem entre componentes numa topologia de contagem de palavras básica:

![Exemplo da disposição dos componentes numa topologia Storm](./media/hdinsight-storm-overview/wordcount-topology.png)

* Os componentes spout trazem dados para uma topologia. Emitem um ou mais fluxos para a topologia.

* Os componentes bolt consomem fluxos emitidos por spouts ou outros bolts. Opcionalmente, os bolts podem emitir novos fluxos para a topologia. Os bolts também são responsáveis pela escrita de dados no armazenamento persistente, como o HDFS ou o HBase.

## <a name="why-use-storm-on-hdinsight"></a>Porquê utilizar o Storm no HDInsight?

O Storm no HDInsight oferece as seguintes grandes vantagens:

* Funciona como um serviço gerido com um SLA de 99,9 por cento de tempo de disponibilidade.

* Suporta a personalização fácil ao executar scripts face a um cluster durante ou após a criação. Para obter mais informações, veja [Customize HDInsight clusters using script action (Personalizar clusters do HDInsight com ações de script)](hdinsight-hadoop-customize-cluster-linux.md).

* Utiliza várias linguagens. Pode escrever os componentes do Storm na linguagem que preferir, como Java, C# e Python.

    * Integra o Visual Studio no HDInsight para o desenvolvimento, a gestão e a monitorização das topologias C#. Para obter mais informações, veja [Develop C# Storm topologies with the HDInsight Tools for Visual Studio (Desenvolver topologias do Strom C# com as Ferramentas do HDInsight para Visual Studio)](hdinsight-storm-develop-csharp-visual-studio-topology.md).

    * Suporta a interface de Java Trident. Pode criar topologias Storm que suportam o processamento de mensagens "exatamente uma vez", persistência do arquivo de dados transacional e um conjunto de operações de análise de fluxo comuns.

*  Aumenta e reduz os clusters verticalmente com facilidade. Pode adicionar ou remover nós de trabalho sem qualquer impacto nas topologias Storm em execução.

* Integra-se nos seguintes serviços do Azure:

    * Azure Event Hubs

    * Rede Virtual do Azure

    * Base de Dados SQL do Azure

    * Storage do Azure

    * Azure DocumentDB

* Combina com segurança as capacidades de vários clusters do HDInsight através da Rede Virtual. Pode criar pipelines de análise que utilizem clusters do HDInsight, HBase ou Hadoop.

Para obter uma lista das empresas que utilizam o Storm como solução de análise em tempo real, veja o artigo [Empresas que utilizam o Apache Storm](https://storm.apache.org/documentation/Powered-By.html).

Para começar a utilizar o Storm, consulte o artigo [Get started with Storm on HDInsight (Introdução ao Storm no HDInsight)][gettingstarted].

## <a name="ease-of-creation"></a>Facilidade de criação

Pode aprovisionar um novo cluster do Storm no HDInsight em minutos. Para obter mais informações sobre como criar clusters do Storm, veja [Introdução ao Storm no HDInsight](hdinsight-apache-storm-tutorial-get-started-linux.md).

## <a name="ease-of-use"></a>Facilidade de utilização

* __Conectividade Secure Shell (SSH)__: pode aceder ao nós principais do cluster do HDInsight através da Internet ao utilizar SSH. Pode executar comandos diretamente no seu cluster através de SSH.

  Para obter mais informações, veja [Utilizar SSH com o HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).

* __Conectividade Web__: os clusters do HDInsight fornecem a IU da Web do Ambari. Pode monitorizar, configurar e gerir serviços facilmente no seu cluster através da IU Web do Ambari. O Storm no HDInsight também fornece a IU do Storm. Pode monitorizar e gerir topologias Storm em execução a partir do seu browser na IU do Storm.

  Para obter mais informações, veja [Manage HDInsight using the Ambari Web UI (Gerir o HDInsight com a IU da Web do Ambari)](hdinsight-hadoop-manage-ambari.md) e [Monitor and manage using the Storm UI (Monitorizar e gerir com a IU do Storm)](hdinsight-storm-deploy-monitor-topology-linux.md#monitor-and-manage-storm-ui).

* __Azure PowerShell e CLI do Azure__: tanto o PowerShell como a CLI do Azure disponibilizam utilitários de linha de comandos que pode utilizar a partir do sistema cliente para trabalhar com o HDInsight e outros serviços do Azure.

* __Integração do Visual Studio__: as Ferramentas do Azure Data Lake para Visual Studio incluem modelos de projetos para criar topologias Storm em C# através da arquitetura SCP.Net. As Ferramentas do Data Lake também proporcionam ferramentas para implementar, monitorizar e gerir soluções com o Storm no HDInsight.

  Para obter mais informações, veja [Develop C# Storm topologies with the HDInsight Tools for Visual Studio (Desenvolver topologias do Strom C# com as Ferramentas do HDInsight para Visual Studio)](hdinsight-storm-develop-csharp-visual-studio-topology.md).

## <a name="integration-with-other-azure-services"></a>Integração noutros serviços do Azure

* __Azure Data Lake Store__: para obter um exemplo de como utilizar o Data Lake Store com o Storm, veja [Use Azure Data Lake Store with Apache Storm on HDInsight](hdinsight-storm-write-data-lake-store.md) (Utilizar o Azure Data Lake Store com o Apache Storm no HDInsight).

* __Hubs de Eventos__: para obter um exemplo de como utilizar os Hubs de Eventos com o Storm, veja os documentos seguintes:

    * [Develop a Java-based topology for Storm on HDInsight](hdinsight-storm-develop-java-topology.md) (Desenvolver uma topologia baseada em Java para o Storm no HDInsight)

    * [Process events from Azure Event Hubs with Storm on HDInsight (C#)](hdinsight-storm-develop-csharp-event-hub-topology.md) (Processar eventos dos Hubs de Eventos do Azure com o Storm no HDInsight [C#])

* __Base de Dados SQL__, __DocumentDB__, __Hubs de Eventos__ e __HBase__: exemplos do modelos estão incluídos nas Ferramentas do Data Lake para Visual Studio. Para obter mais informações, veja [Desenvolver topologias C# para Storm no HDInsight](hdinsight-storm-develop-csharp-visual-studio-topology.md).

## <a name="reliability"></a>Fiabilidade

O Storm garante que cada mensagem a receber é sempre totalmente processada, mesmo quando a análise de dados está distribuída por centenas de nós.

O nó Nimbus fornece uma funcionalidade semelhante ao JobTracker do Hadoop e atribui tarefas a outros nós num cluster através do Zookeeper. Os nós do Zookeeper asseguram a coordenação de um cluster e facilitam a comunicação entre o Nimbus e o processo do Supervisor nos nós de trabalho. Se um nó de processamento deixa de funcionar, o nó Nimbus é informado e atribui a tarefa e os dados associados a um outro nó.

A configuração predefinida do Storm é com apenas um nó do Nimbus. O Storm no HDInsight executa dois nós Nimbus. Se o nó principal falhar, o cluster do HDInsight muda para o nó secundário enquanto o nó principal se recupera. O seguinte diagrama ilustra a configuração do fluxo de tarefas para o Storm no HDInsight:

![Diagrama de nimbus, zookeeper e supervisor](./media/hdinsight-storm-overview/nimbus.png)

## <a name="scale"></a>Escala

Embora possa especificar o número de nós do cluster durante a criação, pode querer aumentar ou diminuir o cluster para que corresponda à carga de trabalho. Pode alterar o número de nós de todos os clusters do HDInsight, mesmo durante o processamento de dados.

> [!NOTE]
> Para tirar partido dos novos nós adicionados através do dimensionamento, tem de reequilibrar as topologias iniciadas antes do aumento do tamanho de cluster.

## <a name="support"></a>Suporte

O Storm no HDInsight inclui suporte contínuo e completo de nível empresarial. O Storm no HDInsight tem também um SLA de 99,9 por cento. Isto significa que podemos garantir que um cluster tem conectividade externa, pelo menos, 99,9 por cento das vezes.

Para obter mais informações, veja o [suporte do Azure](https://azure.microsoft.com/support/options/).

## <a name="common-use-cases"></a>Casos de utilização comuns

Seguem-se alguns cenários comuns nos quais poderá utilizar o Storm no HDInsight. 

* Internet das Coisas (IoT)
* Deteção de fraudes
* Análise social
* Extração, transformação e carregamento (ETL)
* Monitorização de rede
* Pesquisa
* Mobile Engagement

Para obter informações sobre cenários reais, veja o documento [How companies are using Storm](https://storm.apache.org/documentation/Powered-By.html) (Como as empresas utilizam o Storm).

## <a name="development"></a>Desenvolvimento

Os programadores em .NET podem conceber e implementar topologias em C# com as Ferramentas do Data Lake para Visual Studio. Também pode criar topologias híbridas que utilizam componentes de Java e C#.

Para obter mais informações, veja [Develop C# topologies for Storm on HDInsight using Visual Studio](hdinsight-storm-develop-csharp-visual-studio-topology.md) (Desenvolver topologias em C# para o Storm no HDInsight com o Visual Studio).

Também pode desenvolver soluções Java com o IDE da sua preferência. Para obter mais informações, veja [Develop Java topologies for Storm on HDInsight](hdinsight-storm-develop-java-topology.md) (Desenvolver topologias em Java para Storm no HDInsight).

Também pode ser utilizado Python para desenvolver componentes do Storm. Para obter mais informações, veja [Develop Storm topologies using Python on HDInsight](hdinsight-storm-develop-python-topology.md) (Desenvolver topologias Storm com Python no HDInsight).

## <a name="common-development-patterns"></a>Padrões comuns de desenvolvimento

### <a name="guaranteed-message-processing"></a>Processamento de mensagens garantido

O Storm pode fornecer diferentes níveis de processamento garantido de mensagens . Por exemplo, uma aplicação Storm básica pode garantir o processamento pelo-menos-uma vez e o Trident pode garantir o processamento exatamente uma vez.

Para obter mais informações, consulte o artigo [Garantias no processamento de dados](https://storm.apache.org/about/guarantees-data-processing.html) em apache.org.

### <a name="ibasicbolt"></a>IBasicBolt

O padrão de ler uma tupla de entrada, emitir zero ou várias tuplas e, em seguida, confirmar a tupla de entrada imediatamente no final do método de execução é muito comum. O Storm fornece a interface [IBasicBolt](https://storm.apache.org/apidocs/backtype/storm/topology/IBasicBolt.html) para automatizar este padrão.

### <a name="joins"></a>Associações

O modo como os fluxos de dados são associados varia consoante as aplicações. Por exemplo, pode associar cada cadeia de identificação de vários fluxos a um novo fluxo ou pode associar apenas lotes de cadeias de identificação de uma janela específica. De qualquer forma, a associação pode efetuar-se através de [fieldsGrouping](http://javadox.com/org.apache.storm/storm-core/0.9.1-incubating/backtype/storm/topology/InputDeclarer.html#fieldsGrouping%28java.lang.String,%20backtype.storm.tuple.Fields%29), que é um modo de definir como as tuplas são encaminhadas para bolts.

No exemplo de Java a seguir, utiliza-se fieldsGrouping para encaminhar cadeias de identificação que têm origem nos componentes "1", "2" e "3" para o bolt MyJoiner:

    builder.setBolt("join", new MyJoiner(), parallelism) .fieldsGrouping("1", new Fields("joinfield1", "joinfield2")) .fieldsGrouping("2", new Fields("joinfield1", "joinfield2")) .fieldsGrouping("3", new Fields("joinfield1", "joinfield2"));

### <a name="batches"></a>Lotes

O Storm fornece um mecanismo de temporização interno, conhecido como "cadeia de identificação de sinalização", que pode ser utilizado para emitir um lote a cada X segundos.

Para obter um exemplo de como utilizar uma cadeia de identificação de sinalização a partir de um componente em C#, veja [PartialBoltCount.cs](https://github.com/hdinsight/hdinsight-storm-examples/blob/3b2c960549cac122e8874931df4801f0934fffa7/EventCountExample/EventCountTopology/src/main/java/com/microsoft/hdinsight/storm/examples/PartialCountBolt.java).

O Trident baseia-se no processamento de lotes de cadeias de identificação.

### <a name="caches"></a>Caches

A colocação em cache de memória utiliza-se frequentemente como um mecanismo para acelerar o processamento porque mantém os itens utilizados com maior frequência na memória. Como uma topologia é distribuída por múltiplos nós e processos em cada nó, deve considerar a utilização de [fieldsGrouping](http://javadox.com/org.apache.storm/storm-core/0.9.1-incubating/backtype/storm/topology/InputDeclarer.html#fieldsGrouping%28java.lang.String,%20backtype.storm.tuple.Fields%29). Utilize `fieldsGrouping` para garantir que as cadeias de identificação que contêm os campos utilizados na pesquisa de cache são sempre encaminhados para o mesmo processo. Esta funcionalidade de agrupamento evita a duplicação de entradas em cache entre os processos.

### <a name="stream-top-n"></a>Transmitir o "valor N principal"

Quando a sua topologia depende do cálculo de um valor N principal, calcule-o em paralelo. Em seguida, intercale o resultado desses cálculos num valor global. Esta operação pode ser efetuada com [fieldsGrouping](http://javadox.com/org.apache.storm/storm-core/0.9.1-incubating/backtype/storm/topology/InputDeclarer.html#fieldsGrouping%28java.lang.String,%20backtype.storm.tuple.Fields%29) para encaminhar por campo para processamento paralelo e, em seguida, encaminhar para um bolt que determina globalmente o valor N principal.

Para obter um exemplo de cálculo de um valor N principal, veja o exemplo [RollingTopWords](https://github.com/nathanmarz/storm-starter/blob/master/src/jvm/storm/starter/RollingTopWords.java).

## <a name="logging"></a>Registo

O Storm utiliza o Apache Log4j para registar informações. Por predefinição, é registada uma grande quantidade de dados, podendo ser difícil organizar-se nas informações. Pode incluir um ficheiro de configuração de registo como parte da topologia do Storm para controlar o comportamento dos registos.

Para uma topologia de exemplo que demonstra como configurar o registo, veja o exemplo [WordCount baseado em Java](hdinsight-storm-develop-java-topology.md) para Storm no HDInsight.

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre as soluções de análise em tempo real com o Storm no HDInsight:

* [Introdução ao Storm no HDInsight][gettingstarted]
* [Topologias de exemplo para Storm no HDInsight](hdinsight-storm-example-topology.md)

[stormtrident]: https://storm.apache.org/documentation/Trident-API-Overview.html
[samoa]: http://yahooeng.tumblr.com/post/65453012905/introducing-samoa-an-open-source-platform-for-mining
[apachetutorial]: https://storm.apache.org/documentation/Tutorial.html
[gettingstarted]: hdinsight-apache-storm-tutorial-get-started-linux.md

