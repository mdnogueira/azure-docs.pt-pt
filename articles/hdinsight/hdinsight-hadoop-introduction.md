---
title: "Introdução ao Hadoop – O que é o Hadoop no HDInsight? | Microsoft Docs"
description: "Obtenha uma introdução ao Hadoop, processamento e análise de macrodados distribuídos, e os componentes do ecossistema do Hadoop na nuvem no HDInsight."
keywords: "análise de macrodados, introdução ao hadoop, o que é hadoop, pilha de tecnologia do hadoop, ecossistema do hadoop"
services: hdinsight
documentationcenter: 
author: cjgronlund
manager: jhubbard
editor: cgronlun
ms.assetid: e56a396a-1b39-43e0-b543-f2dee5b8dd3a
ms.service: hdinsight
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 10/21/2016
ms.author: cgronlun
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: cefda0778fed4ab8d502955dd8eae4bdac70a48e


---
# <a name="an-introduction-to-the-hadoop-ecosystem-on-azure-hdinsight"></a>Introdução ao ecossistema do Hadoop no Azure HDInsight
 Este artigo fornece uma introdução ao Hadoop no Azure HDInsight, o seu ecossistema e macrodados. Saiba mais sobre os componentes do Hadoop, a terminologia comum e os cenários de análise de macrodados.

## <a name="what-is-hadoop-on-hdinsight"></a>O que é o Hadoop no HDInsight?
O Hadoop refere-se a um ecossistema de software open-source que é uma estrutura para processamento, armazenamento e análise distribuídos de conjuntos de macrodados em clusters de computadores. O Azure HDInsight disponibiliza os componentes do Hadoop da distribuição **Hortonworks Data Platform (HDP)** na nuvem e implementa e aprovisiona os clusters geridos com elevada fiabilidade e disponibilidade.  

O Apache Hadoop foi o projeto open-source original para o processamento de macrodados. Em seguida, deu-se o desenvolvimento de software e utilitários relacionados considerados como parte da pilha de tecnologia do Hadoop, incluindo Apache Hive, Apache HBase, Apache Spark, etc. Veja [Descrição geral do ecossistema do Hadoop no HDInsight](#overview) para obter detalhes.

## <a name="what-is-big-data"></a>O que são macrodados?
Os macrodados descrevem um grande volume de informações digitais, desde texto num feed do Twitter, informações de sensores de equipamentos industriais, a informações sobre pesquisas de clientes e compras num site. Os macrodados podem ser históricos (isto é, dados armazenados) ou em tempo real (isto é, transmitidos em fluxo diretamente a partir da fonte). Os macrodados são recolhidos em volumes cada vez mais elevados, a velocidades cada vez maiores e numa variedade de formatos em expansão.

Para que os macrodados forneçam informações acionáveis, tem de recolher dados relevantes e fazer as perguntas certas. Também tem de assegurar que os dados estão acessíveis, limpos, analisados e, em seguida, sejam apresentados de uma forma útil. É aí que a análise de macrodados do Hadoop no HDInsight pode ser útil.

## <a name="a-nameoverviewaoverview-of-the-hadoop-ecosystem-in-hdinsight"></a><a name="overview"></a>Descrição geral do ecossistema do Hadoop no HDInsight
O HDInsight é uma distribuição na nuvem no Microsoft Azure da pilha de tecnologia do Apache Hadoop em rápida expansão para a análise de macrodados. Inclui implementações do Apache Spark, HBase, Storm, Pig, Hive, Sqoop, Oozie, Ambari, etc. O HDInsight também se integra em ferramentas de business intelligence (BI), como o Power BI, o Excel, o SQL Server Analysis Services e o SQL Server Reporting Services.

### <a name="hadoop-hbase-spark-storm-and-customized-clusters"></a>Hadoop, HBase, Spark, Storm e clusters personalizados
O HDInsight fornece configurações de cluster para Apache Hadoop, Spark, HBase ou Storm. Em alternativa, pode [personalizar clusters com ações de script](hdinsight-hadoop-customize-cluster-linux.md).

* **Hadoop**: fornece um armazenamento de dados fiável com [HDFS](#hdfs) e um modelo de programação [MapReduce](#mapreduce) simples para processamento e análise de dados em paralelo.
* **<a target="_blank" href="http://spark.apache.org/">Apache Spark</a>**: Sendo uma arquitetura de processamento paralelo que suporta o processamento dentro da memória para melhorar o desempenho de aplicações de análise de macrodados, o Spark funciona para SQL, dados de transmissão e Machine Learning. Consulte [Descrição geral: O que é o Apache Spark no HDInsight?](hdinsight-apache-spark-overview.md)
* **<a target="_blank" href="http://hbase.apache.org/">HBase</a>**: uma base de dados NoSQL incorporada no Hadoop que fornece acesso aleatório e consistência segura para grandes quantidades de dados não estruturados e semiestruturados, potencialmente, biliões de linhas vezes milhões de colunas. Consulte [Descrição geral do HBase no HDInsight](hdinsight-hbase-overview.md).
* **<a  target="_blank" href="https://storm.incubator.apache.org/">Apache Storm</a>**: um sistema de cálculo distribuído e em tempo real para o processamento rápido de grandes fluxos de dados. O Storm é fornecido como um cluster gerido no HDInsight. Consulte [Analisar dados de sensores em tempo real através do Storm e do Hadoop](hdinsight-storm-sensor-data-analysis.md).

### <a name="example-customization-scripts"></a>Scripts de personalização de exemplo
As Ações de Script são scripts executados durante o aprovisionamento do cluster e que podem ser utilizados para instalar componentes adicionais no cluster. Para clusters baseados em Linux, estes são scripts de Bash.

Os seguintes scripts de exemplo seguintes são fornecidos pela equipa do HDInsight:

* [Hue](hdinsight-hadoop-hue-linux.md): Um conjunto de Web Apps utilizado para interagir com um cluster. Apenas clusters do Linux.
* [Giraph](hdinsight-hadoop-giraph-install-linux.md): Processamento de gráficos para modelar relações entre coisas ou pessoas.
* [R](hdinsight-hadoop-r-scripts-linux.md): Um linguagem e um ambiente open source para o cálculo estatístico utilizado na Machine Learning.
* [Solr](hdinsight-hadoop-solr-install-linux.md): Uma plataforma de pesquisa de escala empresarial que permite a pesquisa em texto completo em dados.

Para obter informações sobre o desenvolvimento das suas próprias Ações de Script, consulte [Desenvolvimento de Ações de Script com o HDInsight](hdinsight-hadoop-script-actions-linux.md).

## <a name="what-are-the-hadoop-components-and-utilities"></a>Quais são os componentes e os utilitários do Hadoop?
Os seguintes componentes e utilitários estão incluídos nos clusters do HDInsight.

* **[Ambari](#ambari)**: Aprovisionamento, gestão, monitorização e utilitários de clusters.
* **[Avro](#avro)** (Biblioteca do Microsoft .NET para Avro): Serialização de dados para o ambiente do Microsoft .NET.
* **[Hive e HCatalog](#hive)**: Linguagem SQL (Structured Query Languagem) – como a consulta e uma camada de gestão de armazenamento e de tabela.
* **[Mahout](#mahout)**: para aplicações de Machine Learning dimensionáveis.
* **[MapReduce](#mapreduce)**: Arquitetura legada para a gestão distribuída de recursos e de processamento do Hadoop. Consulte [YARN](#yarn), a arquitetura de recursos da próxima geração.
* **[Oozie](#oozie)**: Gestão do fluxo de trabalho.
* **[Phoenix](#phoenix)**: Camada de base de dados relacional através de HBase.
* **[Pig](#pig)**: Um scripting mais simples para transformações de MapReduce.
* **[Sqoop](#sqoop)**: Importação e exportação de dados.
* **[Tez](#tez)**: Permite processos intensivos em termos de dados para executar de forma eficiente com dimensionamento.
* **[YARN](#yarn)**: Parte da biblioteca principal do Hadoop e a próxima geração da arquitetura de software do MapReduce.
* **[ZooKeeper](#zookeeper)**: Coordenação de processos em sistemas distribuídos.

> [!NOTE]
> Para obter informações sobre componentes específicos e informações das versões, veja [Componentes do Hadoop, versões e ofertas de serviços no HDInsight][controlo de versão dos componentes]
> 
> 

### <a name="a-nameambariaambari"></a><a name="ambari"></a>Ambari
O Apache Ambari destina-se ao aprovisionamento, à gestão e à monitorização de clusters do Apache Hadoop. Inclui uma coleção intuitiva de ferramentas de operador intuitiva e um conjunto robusto de APIs que ocultam a complexidade do Hadoop, simplificando a operação de clusters. Os clusters do HDInsight baseados em Linux fornecem a IU da Web do Ambari e API REST do Ambari, enquanto os clusters baseados em Windows fornecem um subconjunto da API REST. As Vistas Ambari em clusters do HDInsight permitem caapacidades da IU de plug-in.

Consulte [Gerir clusters do HDInsight com o Ambari](hdinsight-hadoop-manage-ambari.md) (apenas Linux), [Monitorizar clusters do Hadoop no HDInsight com a API do Ambari](hdinsight-monitor-use-ambari-api.md) e a <a target="_blank" href="https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md">referência da API do Apache Ambari</a>.

### <a name="a-nameavroaavro-microsoft-net-library-for-avro"></a><a name="avro"></a>Avro (Biblioteca do Microsoft .NET para Avro)
A Biblioteca do Microsoft .NET para Avro implementa o formato compacto de intercâmbio de dados binários do Apache Avro para serialização para o ambiente do Microsoft .NET. Utiliza <a target="_blank" href="http://www.json.org/">JavaScript Object Notation (JSON)</a> para definir um esquema de linguagem desconhecida que assegura a interoperabilidade da linguagem, ou seja, os dados serializados numa linguagem podem ser lidos noutra. Pode obter informações detalhadas sobre o formato na <a target=_"blank" href="http://avro.apache.org/docs/current/spec.html">Especificação do Apache Avro</a>.
O formato de ficheiros do Avro suporta o modelo de programação de MapReduce distribuído. Os ficheiros estão "divisíveis", o que significa que pode procurar qualquer ponto num ficheiro e iniciar a leitura a partir de um determinado bloco. Para saber como, consulte [Serializar dados com a Biblioteca do Microsoft .NET para Avro](hdinsight-dotnet-avro-serialization.md).

### <a name="a-namehdfsahdfs"></a><a name="hdfs"></a>HDFS
O Sistema de Ficheiros Distribuído Hadoop (HDFS) é um sistema de ficheiros distribuído que, com o MapReduce e o YARN, é a essência do ecossistema do Hadoop. O HDFS é o sistema de ficheiros standard para clusters do Hadoop no HDInsight.

### <a name="a-namehiveahive-hcatalog"></a><a name="hive"></a>Hive e HCatalog
O <a target="_blank" href="http://hive.apache.org/">Apache Hive</a> é um software de armazém de dados incorporado no Hadoop que lhe permite consultar e gerir grandes conjuntos de dados no armazenamento distribuído ao utilizar uma linguagem semelhante a SQL denominada HiveQL. O Hive, como o Pig, é uma abstração para além do MapReduce. Quando em execução, o Hive traduz consultas para uma série de tarefas do MapReduce. Em termos concecionais, o Hive é mais parecido com um sistema de gestão de bases de dados relacional do que o Pig, pelo que é adequado para utilização com dados mais estruturados. Para dados não estruturados, o Pig é a melhor escolha. Consulte [Utilizar o Hive com o Hadoop no HDInsight](hdinsight-use-hive.md).

O <a target="_blank" href="https://cwiki.apache.org/confluence/display/Hive/HCatalog/">Apache HCatalog</a> é uma camada de gestão de armazenamento e de tabela para o Hadoop que apresenta aos utilizadores uma vista de dados relacional. No HCatalog, pode ler e escrever ficheiros em qualquer formato para os quais um SerDe (serializador-desserializador) do Hive possa ser escrito.

### <a name="a-namemahoutamahout"></a><a name="mahout"></a>Mahout
O <a target="_blank" href="https://mahout.apache.org/">Apache Mahout</a> é uma biblioteca dimensionável de algoritmos de Machine Learning executada no Hadoop. A utilização de princípios de estatísticas, aplicações de aprendizagem máquina educam os sistemas a obter dados e a utilizar resultados anteriores para determinar o comportamento futuro. Consulte [Gerar recomendações de filmes com o Mahout no Hadoop](hdinsight-mahout.md).

### <a name="a-namemapreduceamapreduce"></a><a name="mapreduce"></a>MapReduce
O MapReduce é a arquitetura de software legada do Hadoop para a escrita de aplicações para o processamento em lote de conjuntos de macrodados em paralelo. Uma tarefa do MapReduce divide grandes conjuntos de dados e organiza os dados em pares chave-valor para processamento.

O [YARN](#yarn) é a arquitetura de aplicações e o gestor de recursos da próxima geração do Hadoop e é referido como MapReduce 2.0. As tarefas do MapReduce são executadas no YARN.

Para obter mais informações sobre o MapReduce, consulte <a target="_blank" href="http://wiki.apache.org/hadoop/MapReduce">MapReduce</a> na Wiki do Hadoop.

### <a name="a-nameoozieaoozie"></a><a name="oozie"></a>Oozie
O <a target="_blank" href="http://oozie.apache.org/">Apache Oozie</a> é um sistema de coordenação de fluxos de trabalho que gere as tarefas do Hadoop. É integrado com a pilha do Hadoop e suporta tarefas do Hadoop para MapReduce, Pig, Hive e Sqoop. Também pode ser utilizado para agendar tarefas específicas para um sistema, como os programas de Java ou scripts de shell. Veja [Utilizar o Oozie com o Hadoop](hdinsight-use-oozie.md).

### <a name="a-namephoenixaphoenix"></a><a name="phoenix"></a>Phoenix
O <a  target="_blank" href="http://phoenix.apache.org/">Apache Phoenix</a> é uma camada de base de dados relacional através de HBase. O Phoenix inclui um controlador JDBC que permite aos utilizadores consultar e gerir tabelas do SQL diretamente. O Phoenix traduz consultas e outras instruções para chamadas da API NoSQL nativas, em vez de utilizar o MapReduce, permitindo aplicações mais rápidas para além de arquivos de NoSQL. Consulte [Utilizar o Apache Phoenix e o SQuirreL com clusters do HBase](hdinsight-hbase-phoenix-squirrel.md).

### <a name="a-namepigapig"></a><a name="pig"></a>Pig
O <a  target="_blank" href="http://pig.apache.org/">Apache Pig</a> é uma plataforma de alto nível que lhe permite efetuar transformações do MapReduce complexas em conjuntos de dados muito grandes ao utilizar uma linguagem de script simples denominada Pig Latin. O Pig traduz os scripts de Pig Latin, de modo que serão executados dentro do Hadoop. Pode criar Funções Definidas pelo Utilizador (UDFs) para expandir o Pig Latin. Veja [Utilizar o Pig com o Hadoop](hdinsight-use-pig.md).

### <a name="a-namesqoopasqoop"></a><a name="sqoop"></a>Sqoop
O <a  target="_blank" href="http://sqoop.apache.org/">Apache Sqoop</a> é uma ferramenta que transfere dados em massa entre o Hadoop e bases de dados relacionais, como o SQL, ou outros arquivos de dados estruturados, o mais eficientemente possível. Consulte [Utilizar o Sqoop com o Hadoop](hdinsight-use-sqoop.md).

### <a name="a-nametezatez"></a><a name="tez"></a>Tez
O <a  target="_blank" href="http://tez.apache.org/">Apache Tez</a> é uma arquitetura de aplicações incorporada no Hadoop YARN que executa gráficos complexos e acíclicos do processamento de dados gerais. É um sucessor mais flexível e eficaz da arquitetura do MapReduce que permite que processos intensivos em termos de dados, como o Hive, sejam executados de forma mais eficiente com dimensionamento. Consulte ["Utilizar o Apache Tez para um melhor desempenho" em Utilizar o Hive e o HiveQL](hdinsight-use-hive.md#usetez).

### <a name="a-nameyarnayarn"></a><a name="yarn"></a>YARN
O Apache YARN é a próxima geração do MapReduce (MapReduce 2.0 ou MRv2) e suporta cenários de processamento de dados que ultrapassem o processamento em lote do MapReduce com maior escalabilidade e processamento em tempo real. O YARN fornece gestão de recursos e uma arquitetura de aplicações distribuída. As tarefas do MapReduce são executadas no YARN.

Para saber mais sobre o YARN, consulte <a target="_blank" href="http://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html">Apache Hadoop NextGen MapReduce (YARN)</a>.

### <a name="a-namezookeeperazookeeper"></a><a name="zookeeper"></a>ZooKeeper
O <a  target="_blank" href="http://zookeeper.apache.org/">Apache ZooKeeper</a> coordena processos em sistemas distribuídos de grande dimensão por meio de um espaço de nomes hierárquico partilhado de registos de dados (znodes). Os Znodes incluem pequenas quantidades de informações de metadados necessárias para coordenar processos: estado, localização, configuração, etc.

## <a name="programming-languages-on-hdinsight"></a>Linguagens de programação no HDInsight
Os clusters do HDInsight – clusters do Hadoop, HBase, Storm e Spark – suportam várias linguagens de programação, mas algumas não estão instaladas por predefinição. Para bibliotecas, módulos ou pacotes não instalados por predefinição, utilize uma ação de script para instalar o componente. Consulte [Desenvolvimento de açoes de script com o HDInsight](hdinsight-hadoop-script-actions-linux.md).

### <a name="default-programming-language-support"></a>Suporte de linguagens de programação predefinidas
Por predefinição, os clusters do HDInsight suportam:

* Java
* Python

É possível instalar linguagens adicionais utilizando ações de script: [Desenvolvimento de ações de script com o HDInsight](hdinsight-hadoop-script-actions-linux.md).

### <a name="java-virtual-machine-jvm-languages"></a>Linguagens de máquina virtual de Java (JVM)
Várias linguagens diferentes de Java podem ser executadas utilizando uma máquina virtual de Java (JVM); no entanto, a execução de algumas destas linguagens pode requerer a instalação de componentes adicionais no cluster.

Estas linguagens baseadas em JVM são suportados nos clusters do HDInsight:

* Clojure
* Jython (Python para Java)
* Scala

### <a name="hadoopspecific-languages"></a>Linguagens específicas do Hadoop
Os clusters do HDInsight suportam as seguintes linguagens específicas do ecossistema do Hadoop:

* Pig Latin para tarefas do Pig
* HiveQL para tarefas do Hive e SparkSQL

## <a name="a-nameadvantageaadvantages-of-hadoop-in-the-cloud"></a><a name="advantage"></a>Vantagens do Hadoop na nuvem
Como parte do ecossistema de nuvem do Azure, o Hadoop no HDInsight oferece várias vantagens, nomeadamente:

* Aprovisionamento automático de clusters do Hadoop. Os clusters do HDInsight são muito mais fáceis de criar do que a configuração manual de clusters do Hadoop. Para obter mais detalhes, consulte [Aprovisionar clusters do Hadoop no HDInsight](hdinsight-hadoop-provision-linux-clusters.md).
* Componentes do Hadoop de última geração. Para obter mais detalhes, veja o artigo [Componentes do Hadoop, controlo de versões e ofertas de serviço no HDInsight][controlo de versão dos componentes].
* Elevada disponibilidade e fiabilidade de clusters. Para obter detalhes, consulte [Disponibilidade e fiabilidade de clusters do Hadoop no HDInsight](hdinsight-high-availability-linux.md).
* Armazenamento de dados eficiente e económico com o Blob Storage do Azure, uma opção compatível com o Hadoop. Para obter detalhes, consulte [Utilizar o Blob Storage do Azure com o Hadoop no HDInsight](hdinsight-hadoop-use-blob-storage.md).
* Integração com outros serviços do Azure, incluindo [Aplicações Web](https://azure.microsoft.com/documentation/services/app-service/web/) e [Base de Dados SQL](https://azure.microsoft.com/documentation/services/sql-database/).
* Tamanhos e tipos de VM adicionais para a execução de clusters do HDInsight. Veja o artigo [Componentes do Hadoop, controlo de versões e ofertas de serviço no HDInsight][controlo de versão dos componentes] para obter mais informações.
* Dimensionamento de clusters. O dimensionamento de clusters permite-lhe alterar o número de nós de um cluster do HDInsight em execução sem ter de o eliminar ou voltar a criar.
* Suporte da Virtual Network. Os clusters do HDInsight podem ser utilizados com a Azure Virtual Network para suportar o isolamento de recursos em nuvem ou cenários híbridos que associam os recursos em nuvem aos recursos existentes no seu datacenter.
* Baixo custo inicial. Inicie uma [avaliação gratuita](https://azure.microsoft.com/free/) ou consulte [Detalhes de preços do HDInsight](https://azure.microsoft.com/pricing/details/hdinsight/).

Para ler mais sobre as vantagens do Hadoop no HDInsight, veja a [página de funcionalidades do Azure para HDInsight][marketing-page].

## <a name="hdinsight-standard-and-hdinsight-premium"></a>HDInsight Standard e HDInsight Premium
O HDInsight fornece ofertas em nuvem de macrodados em duas categorias: Standard e Premium. O HDInsight Standard fornece um cluster de escala empresarial que as organizações podem utilizar para executar as respetivas cargas de trabalho de macrodados. O HDInsight Premium baseia-se nisso e fornece capacidades avançadas de análise e segurança para um cluster do HDInsight. Para obter mais informações, consulte [Azure HDInsight Premium](hdinsight-component-versioning.md#hdinsight-standard-and-hdinsight-premium)

## <a name="a-idresourcesaresources-for-learning-more-about-bigdata-analysis-hadoop-and-hdinsight"></a><a id="resources"></a>Recursos para obter mais informações sobre a análise de macrodados, o Hadoop e o HDInsight
Efetue a compilação com base neste introdução ao Hadoop na nuvem e na análise de macrodados com os recursos abaixo.

### <a name="hadoop-documentation-for-hdinsight"></a>Documentação do Hadoop para o HDInsight
* [Documentação do HDInsight](https://azure.microsoft.com/documentation/services/hdinsight/): a página de documentação do Hadoop no Azure HDInsight com ligações a artigos, vídeos e outros recursos.
* [Introdução ao Hadoop no HDInsight](hdinsight-hadoop-linux-tutorial-get-started.md): um tutorial de início rápido para o aprovisionamento de clusters do HDInsight Hadoop e a execução de consultas do Hive de exemplo.
* [Começar com o Spark no HDInsight](hdinsight-apache-spark-jupyter-spark-sql.md): um tutorial de início rápido para criar um cluster Spark e executar consultas de Spark SQL interativas.
* [Utilizar o R Server no HDInsight](hdinsight-hadoop-r-server-get-started.md): começar a utilizar o R Server no HDInsight Premium.
* [Aprovisionar clusters do HDInsight](hdinsight-hadoop-provision-linux-clusters.md): saiba como aprovisionar um cluster do HDInsight Hadoop através do portal do Azure, da CLI do Azure ou do Azure PowerShell.

### <a name="apache-hadoop"></a>Apache Hadoop
* <a target="_blank" href="http://hadoop.apache.org/">Apache Hadoop</a>: saiba mais sobre a biblioteca de software do Apache Hadoop, uma arquitetura que permite o processamento distribuído de grandes conjuntos de dados em clusters de computadores.
* <a target="_blank" href="http://hadoop.apache.org/docs/r1.0.4/hdfs_design.html">HDFS</a>: saiba mais sobre a arquitetura e o design do Sistema de Ficheiros Distribuído Hadoop, o sistema de armazenamento primário utilizado pelas aplicações do Hadoop.
* <a target="_blank" href="http://hadoop.apache.org/docs/r1.2.1/mapred_tutorial.html">Tutorial do MapReduce</a>: saiba mais sobre a arquitetura de programação de escrita de aplicações do Hadoop que processam rapidamente grandes quantidades de dados em paralelo em grandes clusters de nós de computação.

### <a name="microsoft-business-intelligence"></a>Microsoft Business Intelligence
As ferramentas de business intelligence (BI) familiares – como o Excel, o PowerPivot, o SQL Server Analysis Services e o SQL Server Reporting Services – obtêm, analisam e comunicam dados integrados no HDInsight ao utilizar o suplemento Power Query ou o Controlador ODBC do Microsoft Hive.

Estas ferramentas de BI podem ser úteis na análise de macrodados:

* [Ligar o Excel ao Hadoop com o Power Query](hdinsight-connect-excel-power-query.md): Saiba como ligar o Excel à conta do Storage do Azure que armazena os dados associados ao cluster do HDInsight ao utilizar o Microsoft Power Query para Excel. Estação de trabalho do Windows necessária. Funciona com o cluster baseado em Windows ou Linux.
* [Ligar o Excel ao Hadoop com o Controlador ODBC do Microsoft Hive](hdinsight-connect-excel-hive-odbc-driver.md): Saiba como importar dados do HDInsight com o Controlador ODBC do Microsoft Hive. Estação de trabalho do Windows necessária. Funciona com o cluster baseado em Windows ou Linux.
* [Plataforma do Microsoft Cloud](http://www.microsoft.com/server-cloud/solutions/business-intelligence/default.aspx): Saiba mais sobre o Power BI para Office 365, transfira a versão de avaliação do SQL Server e configure o SharePoint Server 2013 e o SQL Server BI.
* [SQL Server Analysis Services](http://msdn.microsoft.com/library/hh231701.aspx).
* [SQL Server Reporting Services](http://msdn.microsoft.com/library/ms159106.aspx).

[marketing-page]: https://azure.microsoft.com/services/hdinsight/
[controlo de versão dos componentes]: hdinsight-component-versioning.md
[zookeeper]: http://zookeeper.apache.org/



<!--HONumber=Nov16_HO2-->


