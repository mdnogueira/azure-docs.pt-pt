<properties
    pageTitle="Introdução ao Apache Storm no HDInsight | Microsoft Azure"
    description="Obter uma introdução ao Apache Storm e aprender como pode utilizar o Storm no HDInsight para criar soluções de análise de dados em tempo real na nuvem."
    services="hdinsight"
    documentationCenter=""
    authors="Blackmist"
    manager="jhubbard"
    editor="cgronlun"
    tags="azure-portal"/>

<tags
   ms.service="hdinsight"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="07/27/2016"
   ms.author="larryfr"/>

#Introdução ao Apache Storm no HDInsight: Análise em tempo real para o Hadoop

O Apache Storm no HDInsight permite-lhe criar soluções de análise em tempo real, distribuídas no ambiente do Azure, utilizando o [Apache Hadoop](http://hadoop.apache.org).

##O que é Apache Storm?

O Apache Storm é um sistema de computação distribuída, com tolerância a falhas, open source, que permite processar os dados em tempo real com o Hadoop. As soluções do Storm também podem fornecer processamento garantido de dados, dada a capacidade de reprodução de dados que não foram processados com êxito à primeira vez.

##Porquê utilizar o Storm no HDInsight?

O Apache Storm no HDInsight é um cluster gerido, integrado no ambiente do Azure. Fornece as principais vantagens seguintes:

* Funciona como um serviço gerido com um SLA de tempo de disponibilidade de 99,9% 

* Pode escolher o idioma: fornece suporte para os componentes Storm escritos em **Java**, **C#** e **Python**

    * Suporta a mistura de linguagens de programação: ler os dados utilizando Java e em seguida, processá-los utilizando C#
    
        > [AZURE.NOTE] As topologias de C# só são suportadas em clusters do HDInsight baseados no Windows.

    * Utilize a interface **Trident** de Java para criar topologias Storm que suportam o processamento de mensagens "exatamente uma vez", persistência do arquivo de dados "transacional" e um conjunto de operações de análise de fluxo comuns

* Inclui funcionalidades integradas de escala vertical e redução vertical: Escalar um cluster do HDInsight sem afetar a execução de topologias Storm

* Integração com outros serviços do Azure, incluindo o Hub de Eventos, Azure Virtual Network, SQL Database, Blob Storage e DocumentDB

    * Combinar as capacidades de vários clusters do HDInsight através da utilização da Azure Virtual Network: criar pipelines de análise que utilizem clusters do HDInsight, do HBase ou do Hadoop

Para obter uma lista de empresas que utiliza o Apache Storm como solução de análise em tempo real, consulte o artigo [Empresas que Utilizam o Apache Storm](https://storm.apache.org/documentation/Powered-By.html).

Para começar a utilizar o Storm, consulte o artigo [Introdução ao Storm no HDInsight][gettingstarted].

###Facilidade de aprovisionamento

Pode aprovisionar um novo Storm no cluster do HDInsight em minutos. Especifique o nome do cluster, tamanho, conta de administrador e conta do Storage. O Azure criará o cluster, incluindo as topologias de exemplo e um dashboard de gestão Web.

> [AZURE.NOTE] Também pode aprovisionar de clusters de Storm através do [CLI do Azure ](../xplat-cli-install.md) ou [Azure PowerShell](../powershell-install-configure.md).

Em menos de 15 minutos após submeter o pedido, terá um novo cluster Storm em execução e pronto para o primeiro pipeline de análise em tempo real.

###Facilidade de utilização

__Para clusters Storm baseados em Linux no HDInsight__, pode ligar para o cluster através de SSH e utilizar o comando `storm` para iniciar e gerir topologias. Além disso, pode utilizar Ambari para monitorizar o serviço Storm e a interface de utilizador Storm para monitorizar e gerir topologias em execução.

Para obter mais informações sobre como trabalhar com clusters Storm baseados em Linux, consulte o artigo [Introdução ao Apache Storm no HDInsight baseado em Linux](hdinsight-apache-storm-tutorial-get-started-linux.md).

__Para clusters Storm baseados em Windows no HDInsight__, as Ferramentas do HDInsight para Visual Studio permitem-lhe criar topologias C# e C# híbrido/Java e depois submetê-las ao cluster Storm no HDInsight.  

![Criação do projeto Storm](./media/hdinsight-storm-overview/createproject.png)

As Ferramentas do HDInsight para Visual Studio também fornecem uma interface que permitem monitorizar e gerir topologias Storm num cluster.

![Gestão Storm](./media/hdinsight-storm-overview/stormview.png)

Para obter um exemplo de como utilizar as Ferramentas do HDInsight para criar uma aplicação Storm, consulte o artigo [Desenvolver topologias de C# com as Ferramentas HDInsight para Visual Studio](hdinsight-storm-develop-csharp-visual-studio-topology.md).

Para obter mais informações sobre as Ferramentas HDInsight para Visual Studio, consulte o artigo [Introdução às ferramentas HDInsight para Visual Studio](../HDInsight/hdinsight-hadoop-visual-studio-tools-get-started.md).

Cada Storm num cluster do HDInsight também fornece um dashboard Storm baseado na Web que permite submeter, monitorizar e gerir topologias Storm em execução no cluster.

![Dashboard Storm](./media/hdinsight-storm-overview/dashboard.png)

Para obter mais informações sobre como utilizar o Dashboard Storm, consulte o artigo [Implementar e gerir topologias Apache Storm no HDInsight](hdinsight-storm-deploy-monitor-topology.md).

O Storm no HDInsight também proporciona a integração fácil com os Event Hubs do Azure através de **Event Hub Spout**. A versão mais recente deste componente está disponível em [https://github.com/hdinsight/hdinsight-storm-examples/tree/master/lib/eventhubs](https://github.com/hdinsight/hdinsight-storm-examples/tree/master/lib/eventhubs). Para obter mais informações sobre como utilizar este componente, consulte os seguintes documentos.

* [Desenvolver uma topologia de C# que utiliza Event Hubs do Azure](hdinsight-storm-develop-csharp-event-hub-topology.md)

* [Desenvolver uma topologia de Java que utiliza Event Hubs do Azure](hdinsight-storm-develop-java-event-hub-topology.md)

###Fiabilidade

O Apache Storm garante sempre que cada mensagem recebida será totalmente processada, mesmo quando a análise de dados está distribuída por centenas de nós.

O **nó Nimbus** fornece uma funcionalidade semelhante ao JobTracker do Hadoop e atribui tarefas a outros nós do cluster através do **Zookeeper**. Os nós de Zookeeper fornecem coordenação de problemas para o cluster e facilitam a comunicação entre Nimbus e o processo do **Supervisor** nos nós de trabalho. Se um nó de processamento deixa de funcionar, o nó Nimbus é informado e atribui a tarefa e os dados associados a um outro nó.

A configuração predefinida para o Apache Storm é com apenas um nó Nimbus. O Storm no HDInsight executa dois nós Nimbus. Se o nó principal falhar, o cluster do HDInsight muda para o nó secundário enquanto o nó principal se recupera.

![Diagrama de nimbus, zookeeper e supervisor](./media/hdinsight-storm-overview/nimbus.png)

###Escala

Embora possa especificar o número de nós do cluster durante a criação, pode querer aumentar ou diminuir o cluster para que coincida à carga de trabalho. Todos os clusters do HDInsight permitem alterar o número de nós do cluster, mesmo durante o processamento de dados.

> [AZURE.NOTE] Para tirar partido dos novos nós adicionados através do dimensionamento, terá de reequilibrar as topologias iniciadas antes do aumento do tamanho de cluster.

###Suporte

Storm no HDInsight vem com suporte completo de nível empresarial 24 horas por dia, 7 dias por semana. Storm no HDInsight tem também um SLA de 99,9%. Isto significa que podemos garantir que o cluster terá conectividade externa, pelo menos, 99,9% do tempo.

##Casos de utilização comuns para análise em tempo real

Seguem-se alguns cenários comuns nos quais poderá utilizar o Apache Storm no HDInsight. Para obter informações sobre cenários no mundo real, leia o artigo [Como empresas estão a utilizar o Storm](https://storm.apache.org/documentation/Powered-By.html).

* Internet das Coisas (IoT)
* Deteção de fraudes
* Análise social
* Extrair, Transformar, Carregar (ETL)
* Monitorização de rede
* Pesquisa
* Mobile Engagement

##Como são processados os dados Storm no HDInsight?

O Apache Storm executa **topologias** em vez dos trabalhos de MapReduce com que poderá estar familiarizado no HDInsight ou Hadoop. Um cluster Storm no HDInsight contêm dois tipos de nós: nós principais que executam **Nimbus** e nós de trabalho que executam **Supervisor**.

* **Nimbus**: Semelhante ao JobTracker no Hadoop, é responsável pela distribuição de código por todo o cluster, atribuir tarefas a máquinas virtuais e monitorizar falhas. HDInsight fornece dois nós Nimbus, por isso, não há nenhum ponto único de falha de Storm no HDInsight

* **Supervisor**: O supervisor para cada nó de trabalho é responsável por iniciar e parar **processos de trabalho** no nó.

* **Processo de trabalho**: executa um subconjunto de uma **topologia**. Uma topologia de execução distribuiu-se por vários processos de trabalho em todo o cluster.

* **Topologia**: Define um gráfico de computação que processa **fluxos** de dados. Ao contrário dos trabalhos de MapReduce, topologias estão em execução até as pararmos.

* **Fluxo**: Uma coleção não ligada de **tuplas**.  **Spouts** e **bolts**produzem fluxos e os **bolts** consomem-los.

* **Tupla**: uma lista com nomes de valores escritos de forma dinâmica.

* **Spout**: consome dados de uma origem de dados e emite um ou vários **fluxos**.

    > [AZURE.NOTE] Em muitos casos, leem-se os dados de uma fila, como Kafka, filas de Service Bus do Azure ou Event Hubs. A fila garante que os dados persistem se houver uma falha.

* **Bolt**: Consome **fluxos**, efetua processamento nas **tuplas** e pode emitir **fluxos**. Os bolts também são responsáveis pela escrita de dados no armazenamento externo, como uma fila, HDInsight, HBase, um blob ou outro arquivo de dados.

* **Apache Thrift**: Uma estrutura de software para desenvolvimento dimensionável de serviços em várias linguagens. Permite-lhe criar serviços que funcionam entre C++, Java, Python, PHP, Ruby, Erlang, Perl, Haskell, c#, Cocoa, JavaScript, Node.js, Smalltalk, etc.

    * **Nimbus** é um serviço Thrift e uma **topologia** é uma definição de Thrift, por isso, é possível desenvolver topologias através de uma variedade de linguagens de programação.

Para obter mais informações sobre os componentes Storm, consulte o [Tutorial Storm][apachetutorial] em apache.org.


##Que linguagens de programação posso utilizar?

O Storm num cluster do HDInsight fornece suporte para C#, Java e Python.

### C&#35;

As Ferramentas HDInsight para Visual Studio permitem que os programadores de .NET concebam e implementem uma topologia em C#. Também pode criar topologias híbridas que utilizam componentes de Java e C#.

Para obter mais informações, consulte o artigo [Desenvolver topologias C# para Apache Storm no HDInsight com Visual Studio](hdinsight-storm-develop-csharp-visual-studio-topology.md).

###Java

A maioria dos exemplos de Java que encontra será em Java simples ou Trident. Trident é uma abstração de alto nível que facilita a criação de coisas como associações, agregações, agrupamentos e filtragens. No entanto, o Trident funciona em lotes de tuplas, enquanto que uma solução de Java sem processamento processará uma tupla de cada vez.

Para mais informações sobre Trident, consulte o [tutorial do Trident ](https://storm.apache.org/documentation/Trident-tutorial.html) em apache.org.

Para obter exemplos de topologias de Java e Trident, consulte a [lista das topologias de exemplo de Storm](hdinsight-storm-example-topology.md) ou os exemplos de storm-starter no cluster do HDInsight.

Os exemplos de storm-starter encontram-se no diretório __ /usr/hdp/current/storm-client/contrib/storm-starter__ em clusters baseados em Linux e no diretório **%storm_home%\contrib\storm-starter** em clusters baseados no Windows.

##Quais são alguns padrões comuns de desenvolvimento?

###Processamento de mensagens garantido

O Storm pode fornecer diferentes níveis de processamento garantido de mensagens . Por exemplo, uma aplicação Storm básica pode garantir o processamento pelo-menos-uma vez e o Trident pode garantir o processamento exatamente uma vez.

Para obter mais informações, consulte o artigo [Garantias no processamento de dados](https://storm.apache.org/about/guarantees-data-processing.html) em apache.org.

###IBasicBolt

O padrão de ler uma tupla entrada, emitir zero ou várias tuplas e, em seguida, confirmar a tupla entrada imediatamente no final do método de execução é muito comum e Storm fornece a interface [IBasicBolt](https://storm.apache.org/apidocs/backtype/storm/topology/IBasicBolt.html) para automatizar este padrão.

###Associações

A associação de dois fluxos de dados variará segundo a aplicação. Por exemplo, pode associar cada tupla de vários fluxos num novo fluxo ou pode associar apenas lotes de tuplas numa janela específica. De qualquer forma, a associação pode efetuar-se através de [fieldsGrouping](http://javadox.com/org.apache.storm/storm-core/0.9.1-incubating/backtype/storm/topology/InputDeclarer.html#fieldsGrouping%28java.lang.String,%20backtype.storm.tuple.Fields%29), que é um modo de definir como as tuplas são encaminhadas para bolts.

No exemplo de Java a seguir, utiliza-se o fieldsGrouping para encaminhar tuplas que têm origem nos componentes "1", "2" e "3" para o bolt **MyJoiner**.

    builder.setBolt("join", new MyJoiner(), parallelism) .fieldsGrouping("1", new Fields("joinfield1", "joinfield2")) .fieldsGrouping("2", new Fields("joinfield1", "joinfield2")) .fieldsGrouping("3", new Fields("joinfield1", "joinfield2"));

###Lotes

O processamento por lotes pode fazer-se de várias formas. Com uma topologia básica de Java de Storm, poderia utilizar um contador simple para processar por lote X número de tuplas antes de emiti-los ou então utilizar um mecanismo de temporização interno conhecido como uma "tupla de sinalização" para emitir um lote de X e X segundos.

Para obter um exemplo de como utilizar tuplas de sinalização, consulte o artigo [Analisar dados de sensor com o Storm e HBase no HDInsight](hdinsight-storm-sensor-data-analysis.md).

Se estiver a utilizar Trident, este baseia-se no processamento de lotes de tuplas.

###Colocação em cache

A colocação em cache de memória utiliza-se frequentemente como um mecanismo para acelerar o processamento porque mantém os itens utilizados com maior frequência na memória. Dado que uma topologia é distribuída por vários nós e há vários processos dentro de cada nó, deve considerar a utilização de [fieldsGrouping](http://javadox.com/org.apache.storm/storm-core/0.9.1-incubating/backtype/storm/topology/InputDeclarer.html#fieldsGrouping%28java.lang.String,%20backtype.storm.tuple.Fields%29) para garantir que as tuplas contêm os campos utilizados para pesquisa de cache são sempre encaminhados para o mesmo processo. Isto evita a duplicação de entradas em cache entre os processos.

###Transmissão os principais valores N

Quando a topologia depende do cálculo de um valor "N principal", como os 5 principais tópicos no Twitter, deve calcular o valor N principal em paralelo e depois intercalar o resultado desses cálculos num valor global. Isto pode ser efetuado utilizando [fieldsGrouping](http://javadox.com/org.apache.storm/storm-core/0.9.1-incubating/backtype/storm/topology/InputDeclarer.html#fieldsGrouping%28java.lang.String,%20backtype.storm.tuple.Fields%29) para encaminhar por campo para os bolts paralelos (que particiona os dados pelo valor do campo) e depois encaminhar para um bolt que determina globalmente o valor N principal.

Para obter um exemplo disto, consulte o exemplo [RollingTopWords](https://github.com/nathanmarz/storm-starter/blob/master/src/jvm/storm/starter/RollingTopWords.java).

##Que tipo de registo utiliza o Storm?

O Storm utiliza o Apache Log4j para registar informações. Por predefinição, é registada uma grande quantidade de dados, podendo ser difícil organizar-se nas informações. Pode incluir um ficheiro de configuração de registo como parte da topologia do Storm para controlar o comportamento dos registos.

Para uma topologia de exemplo que demonstra como configurar o registo, veja o exemplo [WordCount baseado em Java](hdinsight-storm-develop-java-topology.md) para Storm no HDInsight.

##Passos seguintes

Saiba mais sobre as soluções de análise em tempo real com Apache Storm no HDInsight:

* [Introdução ao Storm no HDInsight][gettingstarted]

* [Topologias de exemplo para Storm no HDInsight](hdinsight-storm-example-topology.md)

[stormtrident]: https://storm.apache.org/documentation/Trident-API-Overview.html
[samoa]: http://yahooeng.tumblr.com/post/65453012905/introducing-samoa-an-open-source-platform-for-mining
[apachetutorial]: https://storm.apache.org/documentation/Tutorial.html
[gettingstarted]: hdinsight-apache-storm-tutorial-get-started-linux.md



<!--HONumber=sep16_HO2-->


