<properties
    pageTitle="O que é o Hadoop na nuvem? Introdução ao HDInsight | Microsoft Azure"
    description="O que é o Hadoop na nuvem e como é gerido no HDInsight? Uma introdução aos componentes do Hadoop e à análise de macrodados."
    keywords="big data analysis,introduction to hadoop,what is hadoop,hadoop in the cloud"
    services="hdinsight"
    documentationCenter=""
    authors="cjgronlund"
    manager="paulettm"
    editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="03/29/2016"
   ms.author="cgronlun"/>


# O que é o Hadoop na nuvem? Uma introdução aos componentes do Hadoop no HDInsight para a análise de macrodados

Obtenha uma introdução ao Hadoop, ao seu ecossistema e aos macrodados no Azure HDInsight: O que é Hadoop no HDInsight e quais são os componentes do Hadoop, terminologia comum e cenários para a análise de macrodados? Além disso, saiba mais sobre tutoriais do Hadoop, documentação e recursos para a utilização do Hadoop na nuvem no HDInsight.

## O que é o Hadoop na nuvem no HDInsight?

O Azure HDInsight implementa e aprovisiona clusters do Apache Hadoop na nuvem geridos, fornecendo uma arquitetura de software concebida para processar, analisar e elaborar relatórios sobre macrodados com elevada fiabilidade e disponibilidade. O HDInsight utiliza a distribuição Hadoop da **Plataforma de Dados do Hortonworks (HDP)**. O Hadoop refere-se frequentemente a todo o ecossistema de componentes do Hadoop, que inclui o Apache HBase, o Apache Spark e o Apache Storm, bem como outras tecnologias pertencentes ao Hadoop. Consulte [Descrição geral do ecossistema do Hadoop no HDInsight](#overview) abaixo para obter detalhes.


## O que são macrodados?
Por macrodados, entende-se dados recolhidos em volumes em constante crescendo, a velocidades cada vez maiores e numa variedade em expansão de formatos não estruturados e contextos semânticos variáveis.

Os macrodados descrevem qualquer grande corpo de informações digitais, desde texto num feed do Twitter, informações de sensores de equipamentos industriais, a informações sobre pesquisas de clientes e compras num catálogo online. Os macrodados podem ser históricos (isto é, dados armazenados) ou em tempo real (isto é, transmitidos em fluxo diretamente a partir da fonte).

Para que os macrodados forneçam informações acionáveis, é necessário não só que o utilizador recolha dados relevantes e faça as perguntas certas, como também que os dados estejam acessíveis, limpos, analisados e, em seguida, sejam apresentados de uma forma útil. É aí que a análise de macrodados do Hadoop no HDInsight pode ser útil.


## <a name="overview"></a>Descrição geral do ecossistema do Hadoop no HDInsight

O HDInsight é uma implementação em nuvem no Microsoft Azure da pilha de tecnologia do Apache Hadoop em rápida expansão que é a solução preferida para a análise de macrodados. Inclui implementações do Apache Spark, HBase, Storm, Pig, Hive, Sqoop, Oozie, Ambari, etc. O HDInsight também se integra em ferramentas de business intelligence (BI), como o Power BI, o Excel, o SQL Server Analysis Services e o SQL Server Reporting Services.

### Clusters no Linux

O Azure HDInsight implementa e aprovisiona clusters do Hadoop na nuvem no **Linux**. Consulte a tabela abaixo para obter detalhes.

Categoria | Hadoop no Linux
---------| -------------------
**SO do Cluster** | Ubuntu 12.04 Long Term Support (LTS)
**Tipo de Cluster** | Hadoop, Spark, HBase, Storm
**Implementação** | Portal do Azure, CLI do Azure, Azure PowerShell
**IU do Cluster** | Ambari
**Acesso Remoto** | Secure Shell (SSH), API REST, ODBC, JDBC



### Hadoop, HBase, Spark, Storm e clusters personalizados

O HDInsight fornece configurações de cluster para Apache Hadoop, Spark, HBase ou Storm. Em alternativa, pode [personalizar clusters com ações de script](hdinsight-hadoop-customize-cluster-linux.md).

* **Hadoop** (a carga de trabalho "Consulta"): Fornece um armazenamento de dados fiável com [HDFS](#HDFS) e um modelo de programação [MapReduce](#mapreduce) simples para processamento e análise de dados em paralelo.

* **<a target="_blank" href="http://spark.apache.org/">Apache Spark</a>**: Sendo uma arquitetura de processamento paralelo que suporta o processamento dentro da memória para melhorar o desempenho de aplicações de análise de macrodados, o Spark funciona para SQL, dados de transmissão e Machine Learning. Consulte [Descrição geral: O que é o Apache Spark no HDInsight?](hdinsight-apache-spark-overview.md)

* **<a target="_blank" href="http://hbase.apache.org/">HBase</a>** (a carga de trabalho "NoSQL"): uma base de dados NoSQL incorporada no Hadoop que fornece acesso aleatório e consistência segura para grandes quantidades de dados não estruturados e semiestruturados, potencialmente, biliões de linhas vezes milhões de colunas. Consulte [Descrição geral do HBase no HDInsight](hdinsight-hbase-overview.md).

* **<a  target="_blank" href="https://storm.incubator.apache.org/">Apache Storm</a>** (a carga de trabalho "Transmissão"): Um sistema de cálculo distribuído e em tempo real para o processamento rápido de grandes fluxos de dados. O Storm é fornecido como um cluster gerido no HDInsight. Consulte [Analisar dados de sensores em tempo real através do Storm e do Hadoop](hdinsight-storm-sensor-data-analysis.md).

#### Scripts de personalização de exemplo

As Ações de Script são scripts executados durante o aprovisionamento do cluster e que podem ser utilizados para instalar componentes adicionais no cluster. Para clusters baseados em Linux, estes são scripts de Bash.

Os seguintes scripts de exemplo seguintes são fornecidos pela equipa do HDInsight:

* [Hue](hdinsight-hadoop-hue-linux.md): Um conjunto de Web Apps utilizado para interagir com um cluster. Apenas clusters do Linux.

* [Giraph](hdinsight-hadoop-giraph-install-linux.md): Processamento de gráficos para modelar relações entre coisas ou pessoas.

* [R](hdinsight-hadoop-r-scripts-linux.md): Um linguagem e um ambiente open source para o cálculo estatístico utilizado na Machine Learning.

* [Solr](hdinsight-hadoop-solr-install-linux.md): Uma plataforma de pesquisa de escala empresarial que permite a pesquisa em texto completo em dados.

Para obter informações sobre o desenvolvimento das suas próprias Ações de Script, consulte [Desenvolvimento de Ações de Script com o HDInsight](hdinsight-hadoop-script-actions-linux.md).

## HDInsight Standard e HDInsight Premium

O HDInsight fornece ofertas em nuvem de macrodados em duas categorias: Standard e Premium. O HDInsight Standard fornece um cluster de escala empresarial que as organizações podem utilizar para executar as respetivas cargas de trabalho de macrodados. O HDInsight Premium baseia-se nisso e fornece capacidades avançadas de análise e segurança para um cluster do HDInsight. Para obter mais informações, consulte [Azure HDInsight Premium](hdinsight-component-versioning.md#hdinsight-standard-and-hdinsight-premium)

## Quais são os componentes e os utilitários do Hadoop?

Os seguintes componentes e utilitários estão incluídos nos clusters do HDInsight.

* **[Ambari](#ambari)**: Aprovisionamento, gestão, monitorização e utilitários de clusters.

* **[Avro](#avro)** (Biblioteca do Microsoft .NET para Avro): Serialização de dados para o ambiente do Microsoft .NET.

* **[Hive e HCatalog](#hive)**: Linguagem SQL (Structured Query Languagem) – como a consulta e uma camada de gestão de armazenamento e de tabela.

* **[Mahout](#mahout)**: Machine Learning.

* **[MapReduce](#mapreduce)**: Arquitetura legada para a gestão distribuída de recursos e de processamento do Hadoop. Consulte [YARN](#yarn), a arquitetura de recursos da próxima geração.

* **[Oozie](#oozie)**: Gestão do fluxo de trabalho.

* **[Phoenix](#phoenix)**: Camada de base de dados relacional através de HBase.

* **[Pig](#pig)**: Um scripting mais simples para transformações de MapReduce.

* **[Sqoop](#sqoop)**: Importação e exportação de dados.

* **[Tez](#tez)**: Permite processos intensivos em termos de dados para executar de forma eficiente com dimensionamento.

* **[YARN](#yarn)**: Parte da biblioteca principal do Hadoop e a próxima geração da arquitetura de software do MapReduce.

* **[ZooKeeper](#zookeeper)**: Coordenação de processos em sistemas distribuídos.

> [AZURE.NOTE] Para obter informações sobre os componentes específicos e informações das versões, consulte [Novidades nas versões do cluster do Hadoop fornecidas pelo HDInsight?][component-versioning]

### <a name="ambari"></a>Ambari

O Apache Ambari destina-se ao aprovisionamento, à gestão e à monitorização de clusters do Apache Hadoop. Inclui uma coleção intuitiva de ferramentas de operador intuitiva e um conjunto robusto de APIs que ocultam a complexidade do Hadoop, simplificando a operação de clusters. Os clusters do HDInsight baseados em Linux fornecem a IU da Web do Ambari e API REST do Ambari, enquanto os clusters baseados em Windows fornecem um subconjunto da API REST. As Vistas Ambari em clusters do HDInsight permitem caapacidades da IU de plug-in.

Consulte [Gerir clusters do HDInsight com o Ambari](hdinsight-hadoop-manage-ambari.md) (apenas Linux), [Monitorizar clusters do Hadoop no HDInsight com a API do Ambari](hdinsight-monitor-use-ambari-api.md) e a <a target="_blank" href="https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md">referência da API do Apache Ambari</a>.

### <a name="avro"></a>Avro (Biblioteca do Microsoft .NET para Avro)

A Biblioteca do Microsoft .NET para Avro implementa o formato compacto de intercâmbio de dados binários do Apache Avro para serialização para o ambiente do Microsoft .NET. Utiliza <a target="_blank" href="http://www.json.org/">JavaScript Object Notation (JSON)</a> para definir um esquema de linguagem desconhecida que assegura a interoperabilidade da linguagem, ou seja, os dados serializados numa linguagem podem ser lidos noutra. Pode obter informações detalhadas sobre o formato na <a target=_"blank" href="http://avro.apache.org/docs/current/spec.html">Especificação do Apache Avro</a>.
O formato de ficheiros do Avro suporta o modelo de programação de MapReduce distribuído. Os ficheiros estão "divisíveis", o que significa que pode procurar qualquer ponto num ficheiro e iniciar a leitura a partir de um determinado bloco. Para saber como, consulte [Serializar dados com a Biblioteca do Microsoft .NET para Avro](hdinsight-dotnet-avro-serialization.md).


### <a name="hdfs"></a>HDFS

O Sistema de Ficheiros Distribuído Hadoop (HDFS) é um sistema de ficheiros distribuído que, com o MapReduce e o YARN, é a essência do ecossistema do Hadoop. O HDFS é o sistema de ficheiros standard para clusters do Hadoop no HDInsight.

### <a name="hive"></a>Hive e HCatalog

<a target="_blank" href="http://hive.apache.org/">Apache Hive</a> é um software de armazém de dados incorporado no Hadoop que lhe permite consultar e gerir grandes conjuntos de dados no armazenamento distribuído ao utilizar uma linguagem semelhante a SQL denominada HiveQL. O Hive, como o Pig, é uma abstração para além do MapReduce. Quando em execução, o Hive traduz consultas para uma série de tarefas do MapReduce. Em termos concecionais, o Hive é mais parecido com um sistema de gestão de bases de dados relacional do que o Pig, pelo que é adequado para utilização com dados mais estruturados. Para dados não estruturados, o Pig é a melhor escolha. Consulte [Utilizar o Hive com o Hadoop no HDInsight](hdinsight-use-hive.md).

<a target="_blank" href="https://cwiki.apache.org/confluence/display/Hive/HCatalog/">O Apache HCatalog</a> é uma camada de gestão de armazenamento e de tabela para o Hadoop que apresenta aos utilizadores uma vista de dados relacional. No HCatalog, pode ler e escrever ficheiros em qualquer formato para os quais um SerDe (serializador-desserializador) do Hive possa ser escrito.

### <a name="mahout"></a>Mahout

<a target="_blank" href="https://mahout.apache.org/">O Apache Mahout</a> é uma biblioteca dimensionável de algoritmos de Machine Learning executada no Hadoop. A utilização de princípios de estatísticas, aplicações de aprendizagem máquina educam os sistemas a obter dados e a utilizar resultados anteriores para determinar o comportamento futuro. Consulte [Gerar recomendações de filmes com o Mahout no Hadoop](hdinsight-mahout.md).

### <a name="mapreduce"></a>MapReduce
O MapReduce é a arquitetura de software legada do Hadoop para a escrita de aplicações para o processamento em lote de conjuntos de macrodados em paralelo. Uma tarefa do MapReduce divide grandes conjuntos de dados e organiza os dados em pares chave-valor para processamento.

O [YARN](#yarn) é a arquitetura de aplicações e o gestor de recursos da próxima geração do Hadoop e é referido como MapReduce 2.0. As tarefas do MapReduce serão executadas no YARN.

Para obter mais informações sobre o MapReduce, consulte <a target="_blank" href="http://wiki.apache.org/hadoop/MapReduce">MapReduce</a> na Wiki do Hadoop.

### <a name="oozie"></a>Oozie
<a target="_blank" href="http://oozie.apache.org/">O Apache Oozie</a> é um sistema de coordenação de fluxos de trabalho que gere as tarefas do Hadoop. É integrado com a pilha do Hadoop e suporta tarefas do Hadoop para MapReduce, Pig, Hive e Sqoop. Também pode ser utilizado para agendar tarefas específicas para um sistema, como os programas de Java ou scripts de shell. Consulte [Utilizar um Coordenador de Oozie baseado no tempo com o Hadoop](hdinsight-use-oozie-coordinator-time.md).

### <a name="phoenix"></a>Phoenix
<a  target="_blank" href="http://phoenix.apache.org/">O Apache Phoenix</a> é uma camada de base de dados relacional através de HBase. O Phoenix inclui um controlador JDBC que permite aos utilizadores consultar e gerir tabelas do SQL diretamente. O Phoenix traduz consultas e outras instruções para chamadas da API NoSQL nativas, em vez de utilizar o MapReduce, permitindo aplicações mais rápidas para além de arquivos de NoSQL. Consulte [Utilizar o Apache Phoenix e o SQuirreL com clusters do HBase](hdinsight-hbase-phoenix-squirrel.md).


### <a name="pig"></a>Pig
<a  target="_blank" href="http://pig.apache.org/">O Apache Pig</a> é uma plataforma de alto nível que lhe permite efetuar transformações do MapReduce complexas em conjuntos de dados muito grandes ao utilizar uma linguagem de script simples denominada Pig Latin. O Pig traduz os scripts de Pig Latin, de modo que serão executados dentro do Hadoop. Pode criar Funções Definidas pelo Utilizador (UDFs) para expandir o Pig Latin. Consulte [Utilizar o Pig com o Hadoop para analisar um ficheiro de registo do Apache](hdinsight-use-pig.md).

### <a name="sqoop"></a>Sqoop
<a  target="_blank" href="http://sqoop.apache.org/">O Apache Sqoop</a> é uma ferramenta que transfere dados em massa entre o Hadoop e bases de dados relacionais, como o SQL, ou outros arquivos de dados estruturados, o mais eficientemente possível. Consulte [Utilizar o Sqoop com o Hadoop](hdinsight-use-sqoop.md).

### <a name="tez"></a>Tez
<a  target="_blank" href="http://tez.apache.org/">O Apache Tez</a> é uma arquitetura de aplicações incorporada no Hadoop YARN que executa gráficos complexos e acíclicos do processamento de dados gerais. É um sucessor mais flexível e eficaz da arquitetura do MapReduce que permite que processos intensivos em termos de dados, como o Hive, sejam executados de forma mais eficiente com dimensionamento. Consulte ["Utilizar o Apache Tez para um melhor desempenho" em Utilizar o Hive e o HiveQL](hdinsight-use-hive.md#usetez).

### <a name="yarn"></a>YARN
O Apache YARN é a próxima geração do MapReduce (MapReduce 2.0 ou MRv2) e suporta cenários de processamento de dados que ultrapassem o processamento em lote do MapReduce com maior escalabilidade e processamento em tempo real. O YARN fornece gestão de recursos e uma arquitetura de aplicações distribuída. As tarefas do MapReduce serão executadas no YARN.

Para saber mais sobre o YARN, consulte <a target="_blank" href="http://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html">Apache Hadoop NextGen MapReduce (YARN)</a>.


### <a name="zookeeper"></a>ZooKeeper
<a  target="_blank" href="http://zookeeper.apache.org/">O Apache ZooKeeper</a> coordena processos sistemas distribuídos de grande dimensão por meio de um espaço de nomes hierárquico partilhado de registos de dados (znodes). Os Znodes incluem pequenas quantidades de informações de metadados necessárias para coordenar processos: estado, localização, configuração, etc.

## Linguagens de programação no HDInsight

Os clusters do HDInsight – clusters do Hadoop, HBase, Storm e Spark – suportam várias linguagens de programação, mas algumas não estão instaladas por predefinição. Para bibliotecas, módulos ou pacotes não instalados por predefinição, utilize uma ação de script para instalar o componente. Consulte [Desenvolvimento de açoes de script com o HDInsight](hdinsight-hadoop-script-actions-linux.md).

### Suporte de linguagens de programação predefinidas

Por predefinição, os clusters do HDInsight suportam:

* Java

* Python

É possível instalar linguagens adicionais utilizando ações de script: [Desenvolvimento de ações de script com o HDInsight](hdinsight-hadoop-script-actions-linux.md).

### Linguagens de máquina virtual de Java (JVM)

Várias linguagens diferentes de Java podem ser executadas utilizando uma máquina virtual de Java (JVM); no entanto, a execução de algumas destas linguagens pode requerer a instalação de componentes adicionais no cluster.

Estas linguagens baseadas em JVM são suportados nos clusters do HDInsight:

* Clojure

* Jython (Python para Java)

* Scala

### Linguagens específicas do Hadoop

Os clusters do HDInsight oferecem suporte para as seguintes linguagens específicas do ecossistema do Hadoop:

* Pig Latin para tarefas do Pig

* HiveQL para tarefas do Hive e SparkSQL


## <a name="advantage"></a>Vantagens do Hadoop na nuvem

Como parte do ecossistema de nuvem do Azure, o Hadoop no HDInsight oferece várias vantagens, nomeadamente:

* Aprovisionamento automático de clusters do Hadoop. Os clusters do HDInsight são muito mais fáceis de criar do que a configuração manual de clusters do Hadoop. Para obter mais detalhes, consulte [Aprovisionar clusters do Hadoop no HDInsight](hdinsight-hadoop-provision-linux-clusters.md).

* Componentes do Hadoop de última geração. Para obter detalhes, consulte [Novidades nas versões de cluster do Hadoop fornecidas pelo HDInsight][component-versioning].

* Elevada disponibilidade e fiabilidade de clusters.  Foi adicionado um segundo nó principal aos clusters do Hadoop implementados pelo HDInsight para aumentar a disponibilidade do serviço. As implementações Standard de clusters do Hadoop têm, normalmente, um único nó principal. O HDInsight remove este ponto único de falha com a adição de um nó principal secundário. A mudança para uma nova configuração de cluster do HA não altera o preço do cluster, a menos que os clientes criem os clusters com um nó principal muito grande em vez do nó de grande tamanho predefinido.

    Para obter detalhes, consulte [Disponibilidade e fiabilidade de clusters do Hadoop no HDInsight](hdinsight-high-availability-linux.md).

* Armazenamento de dados eficiente e económico com o Blob Storage do Azure, uma opção compatível com o Hadoop. Para obter detalhes, consulte [Utilizar o Blob Storage do Azure com o Hadoop no HDInsight](hdinsight-hadoop-use-blob-storage.md).

* Integração com outros serviços do Azure, incluindo [Web Apps](../documentation/services/app-service/web/) e [SQL Database](../documentation/services/sql-database/).

* Tamanhos de VM adicionais. Os clusters do HDInsight estão disponíveis em diferentes tipos e tamanhos de VM. Agora, os clusters do HDInsight podem utilizar tamanhos A2 a A7 criados para fins gerais; Nós de série D que incluem unidades de estado sólido (SSDs) e processadores 60% mais rápidos; e tamanhos A8 e A9 que tenham suporte de InfiniBand para um funcionamento em rede rápido. Os clientes do Apache HBase no Azure HDInsight podem beneficiar das configurações de memória superiores da série de D para melhorar o desempenho. Os clientes do Apache Storm no Azure HDInsight clientes também podem beneficiar da memória adicional para carregar conjuntos mais vastos de dados de referência, bem como CPUs mais rápidas para maior débito.

* Dimensionamento de clusters. O dimensionamento de clusters permite-lhe alterar o número de nós de um cluster do HDInsight em execução sem ter de o eliminar ou voltar a criar.

* Suporte da Virtual Network. Os clusters do HDInsight podem ser utilizados com a Azure Virtual Network para suportar o isolamento de recursos em nuvem ou cenários híbridos que associam os recursos em nuvem aos recursos existentes no seu datacenter.

* Baixo custo inicial. Inicie uma [avaliação gratuita](/pricing/free-trial/) ou consulte [Detalhes de preços do HDInsight](/pricing/details/hdinsight/).


Para ler mais sobre as vantagens do Hadoop no HDInsight, consulte a [página de funcionalidades do Azure para HDInsight][marketing-page].



## <a id="resources"></a>Recursos para obter mais informações sobre a análise de macrodados, o Hadoop e o HDInsight

Efetue a compilação com base neste introdução ao Hadoop na nuvem e na análise de macrodados com os recursos abaixo.


### Documentação do Hadoop para o HDInsight

* [Documentação do HDInsight](https://azure.microsoft.com/documentation/services/hdinsight/): A página de documentação do Azure HDInsight com ligações para artigos, vídeos e mais recursos.

* [Introdução ao HDInsight no Linux](hdinsight-hadoop-linux-tutorial-get-started.md): Um tutorial de início rápido para o aprovisionamento de clusters do HDInsight Hadoop no Linux e a execução de consultas do Hive de exemplo.

* [Introdução ao Storm baseado em Linux no HDInsight](hdinsight-apache-storm-tutorial-get-started-linux.md): Um tutorial de início rápido para o aprovisionamento de um Storm no cluster do HDInsight e a execução de topologias do Storm de exemplo.

* [Aprovisionar o HDInsight no Linux](hdinsight-hadoop-provision-linux-clusters.md): Saiba como aprovisionar um cluster do HDInsight Hadoop no Linux através do Portal do Azure, da CLI do Azure CLI ou do Azure PowerShell.

* [Trabalhar com o HDInsight no Linux](hdinsight-hadoop-linux-information.md): Obtenha algumas sugestões rápidas sobre como trabalhar com clusters do Hadoop Linux aprovisionados no Azure.

* [Gerir clusters do HDInsight com o Ambari](hdinsight-hadoop-manage-ambari.md): Saiba como monitorizar e gerir o seu Hadoop baseado em Linux num cluster do HDInsight utilizando a Web do Ambari ou a API REST do Ambari.


### Apache Hadoop

* <a target="_blank" href="http://hadoop.apache.org/">Apache Hadoop</a>: Saiba mais sobre a biblioteca de software do Apache Hadoop, uma arquitetura que permite o processamento distribuído de grandes conjuntos de dados em clusters de computadores.

* <a target="_blank" href="http://hadoop.apache.org/docs/r1.0.4/hdfs_design.html">HDFS</a>: Saiba mais sobre a arquitetura e o design do Sistema de Ficheiros Distribuído Hadoop, o sistema de armazenamento primário utilizado pelas aplicações do Hadoop.

* <a target="_blank" href="http://hadoop.apache.org/docs/r1.2.1/mapred_tutorial.html">Tutorial do MapReduce</a>: Saiba mais sobre a arquitetura de programação de escrita de aplicações do Hadoop que processam rapidamente grandes quantidades de dados em paralelo em grandes clusters de nós de computação.

### SQL Database no Azure

* [SQL Database do Azure](/documentation/services/sql-database/): Documentação, tutoriais e vídeos sobre a SQL Database.

* [SQL Database no Portal do Azure](../sql-database/sql-database-manage-portal.md): Uma ferramenta de gestão de bases de dados simples e fácil de utilizar para gerir a SQL Database na nuvem.

* [Adventure Works para SQL Database](http://msftdbprodsamples.codeplex.com/releases/view/37304): Página de transferência para uma base de dados de exemplo da SQL Database.

### Microsoft business intelligence (para o HDInsight no Windows)

As ferramentas de business intelligence (BI) familiares – como o Excel, o PowerPivot, o SQL Server Analysis Services e o SQL Server Reporting Services – obtêm, analisam e comunicam dados integrados no HDInsight ao utilizar o suplemento Power Query ou o Controlador ODBC do Microsoft Hive.

Estas ferramentas de BI podem ser úteis na análise de macrodados:

* [Ligar o Excel ao Hadoop com o Power Query](hdinsight-connect-excel-power-query.md): Saiba como ligar o Excel à conta do Storage do Azure que armazena os dados associados ao cluster do HDInsight ao utilizar o Microsoft Power Query para Excel.

* [Ligar o Excel ao Hadoop com o Controlador ODBC do Microsoft Hive](hdinsight-connect-excel-hive-ODBC-driver.md): Saiba como importar dados do HDInsight com o Controlador ODBC do Microsoft Hive.

* [Plataforma do Microsoft Cloud](http://www.microsoft.com/server-cloud/solutions/business-intelligence/default.aspx): Saiba mais sobre o Power BI para Office 365, transfira a versão de avaliação do SQL Server e configure o SharePoint Server 2013 e o SQL Server BI.

* <a target="_blank" href="http://msdn.microsoft.com/library/hh231701.aspx">Saiba mais sobre o SQL Server Analysis Services</a>.

* <a target="_blank" href="http://msdn.microsoft.com/library/ms159106.aspx">Saiba mais sobre o SQL Server Reporting Services</a>.


### Experimente soluções de Hadoop para análise de macrodados (para o HDInsight no Windows)

Utilize a análise de macrodados nos dados da sua organização para obter informações acerca da sua empresa. Eis alguns exemplos:

* [Analisar dados de sensores de AVAC](hdinsight-hive-analyze-sensor-data.md): Saiba como analisar dados de sensores utilizando o Hive com o HDInsight (Hadoop) e, em seguida, visualize os dados no Microsoft Excel. Neste exemplo, irá utilizar o Hive para processar os dados históricos produzidos por sistemas de AVAC para ver que sistemas não conseguem manter uma temperatura definida de forma fiável.

* [Utilizar o Hive com o HDInsight para analisar registos de Web Sites](hdinsight-hive-analyze-website-log.md): Saiba como utilizar o HiveQL no HDInsight para analisar os registos de Web Sites, de modo a obter informações sobre a frequência das visitas durante um dia a partir de Web Sites externos e um resumo dos erros de Web Sites com que os utilizadores se deparam.

* [Analisar dados de sensores em tempo real com o Storm e o HBase no HDInsight (Hadoop)](hdinsight-storm-sensor-data-analysis.md): Saiba como criar uma solução que utiliza um cluster do Storm no HDInsight para processar dados de sensores a partir de Event Hubs do Azure Hubs e, em seguida, apresenta os dados de sensores processados como informações quase em tempo real num dashboard baseado na Web.


[marketing-page]: ../services/hdinsight/
[component-versioning]: hdinsight-component-versioning.md
[zookeeper]: http://zookeeper.apache.org/



<!--HONumber=Jun16_HO2-->


