---
title: "O que é o HDInsight, a pilha tecnológica do Hadoop e os clusters? - Azure | Microsoft Docs"
description: "Uma introdução ao HDInsight e à pilha e aos componentes da tecnologia do Hadoop, incluindo Spark, Kafka, Hive, HBase para análise de macrodados."
keywords: "hadoop azure, azure hadoop, introdução ao hadoop, intro ao hadoop, pilha de tecnologia do hadoop, intro do hadoop, introdução do hadoop, o que é um cluster do hadoop, o que é o cluster do hadoop, para que é utilizado o hadoop"
services: hdinsight
documentationcenter: 
author: cjgronlund
manager: jhubbard
editor: cgronlun
ms.assetid: e56a396a-1b39-43e0-b543-f2dee5b8dd3a
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 07/20/2017
ms.author: cgronlun
ms.openlocfilehash: e5ed09ddb1556e6c76813e71bcb31cf4f792b616
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="introduction-to-azure-hdinsight-the-hadoop-technology-stack-and-hadoop-clusters"></a>Introdução ao Azure HDInsight, à pilha de tecnologia do Hadoop e aos clusters do Hadoop
 Este artigo disponibiliza uma introdução ao Azure HDInsight, uma distribuição de cloud da pilha de tecnologia do Hadoop. Também abrange a definição de clusters do Hadoop e quando os deve utilizar. 

## <a name="what-is-hdinsight-and-the-hadoop-technology-stack"></a>O que é o HDInsight e a pilha de tecnologia do Hadoop? 
O Azure HDInsight é uma distribuição de cloud dos componentes do Hadoop da [Hortonworks Data Platform (HDP)](https://hortonworks.com/products/data-center/hdp/). O [Apache Hadoop](http://hadoop.apache.org/) era a arquitetura de código aberto original para processamento distribuído e análise de conjuntos de macrodados em clusters de computadores. 


A pilha de tecnologia do Hadoop inclui software e utilitários relacionados, entre os quais Apache Hive, HBase, Spark, Kafka e muitos outros. Para ver os componentes da pilha tecnológica do Hadoop disponíveis no HDInsight, veja [Componentes e versões disponíveis com o HDInsight][component-versioning]. Para ler mais sobre o Hadoop no HDInsight, veja a [página de funcionalidades do Azure para o HDInsight](https://azure.microsoft.com/services/hdinsight/).

## <a name="what-is-a-hadoop-cluster-and-when-do-you-use-it"></a>O que é um cluster do Hadoop e quando deve utilizá-lo?
O *Hadoop* também é um tipo de cluster que tem:

* O YARN, para agendamento de tarefas e gestão de recursos
* MapReduce para processamento paralelo
* O sistema de ficheiros distribuídos do Hadoop (HDFS)
  
Os clusters do Hadoop são geralmente utilizados para processamento de lotes de dados armazenados. Outros tipos de clusters no HDInsight têm capacidades adicionais: a popularidade do Spark aumentou devido ao processamento dentro da memória mais rápido. Veja [Tipos de clusters no HDInsight](#overview) para obter detalhes.

## <a name="what-is-big-data"></a>O que são macrodados?
Os macrodados descrevem grandes corpos de informações digitais, como:

* Dados de sensores de equipamentos industriais
* Atividades de clientes recolhidas em Web sites
* Newsfeeds to Twitter

Os macrodados são recolhidos em volumes cada vez maiores, a velocidades mais rápidas e numa variedade de formatos mais ampla. Podem ser históricos (isto é, armazenados) ou em tempo real (isto é, transmitidos em fluxo a partir da origem). 

## <a name="overview"></a>Tipos de clusters no HDInsight
O HDInsight inclui tipos de clusters específicos e capacidades de personalização de clusters, como adicionar componentes, utilitários e linguagens.

### <a name="spark-kafka-interactive-query-hbase-customized-and-other-cluster-types"></a>Spark, Kafka, Interactive Query, HBase, clusters personalizados e outros tipos de cluster
O HDInsight oferece os seguintes tipos de cluster:

* O **[Apache Hadoop](https://wiki.apache.org/hadoop)**: utiliza o [HDFS](#hdfs), a gestão de recursos [YARN](#yarn) e um modelo de programação simples, o [MapReduce](#mapreduce), para processar e analisar dados de lotes em paralelo.
* **[Apache Spark](http://spark.apache.org/)**: Sendo uma arquitetura de processamento paralelo que suporta o processamento dentro da memória para melhorar o desempenho de aplicações de análise de macrodados, o Spark funciona para SQL, dados de transmissão e Machine Learning. Veja [O que é o Apache Spark no HDInsight?](hdinsight-apache-spark-overview.md)
* **[Apache HBase](http://hbase.apache.org/)**: uma base de dados NoSQL incorporada no Hadoop que fornece acesso aleatório e consistência segura para grandes quantidades de dados não estruturados e semiestruturados, potencialmente, biliões de linhas vezes milhões de colunas. Veja [O que é o HBase no HDInsight?](hdinsight-hbase-overview.md)
* **[Microsoft R Server](https://msdn.microsoft.com/microsoft-r/rserver)**: um servidor para alojar e gerir processos R paralelos distribuídos. Fornece cientistas de dados, peritos em estatística e programadores de R com acesso a pedido a métodos dimensionáveis distribuídos de análise no HDInsight. Consulte [Overview of R Server on HDInsight (Descrição geral do R Server no HDInsight)](hdinsight-hadoop-r-server-overview.md).
* **[Apache Storm](https://storm.incubator.apache.org/)**: um sistema de cálculo distribuído e em tempo real para o processamento rápido de grandes fluxos de dados. O Storm é fornecido como um cluster gerido no HDInsight. Consulte [Analisar dados de sensores em tempo real através do Storm e do Hadoop](hdinsight-storm-sensor-data-analysis.md).
* **[Pré-visualização do Apache Interactive Query (também conhecido como: Live Long and Process)](https://cwiki.apache.org/confluence/display/Hive/LLAP)**: colocação em cache dentro da memória para consultas do Hive interativas e mais rápidas. Veja [Use Interactive Query in HDInsight (Utilizar o Interactive Query no HDInsight)](hdinsight-hadoop-use-interactive-hive.md).
* **[Apache Kafka](https://kafka.apache.org/)**: uma plataforma de código aberto utilizada para a criação de aplicações e pipelines de dados de transmissão. O Kafka também fornece uma funcionalidade de fila de mensagens que lhe permite publicar e subscrever transmissões de dados. Consulte [Introduction to Apache Kafka on HDInsight (Introdução ao Apache Kafka no HDInsight)](hdinsight-apache-kafka-introduction.md).

Também pode configurar clusters com os métodos seguintes:
* **[Pré-visualização de clusters associados a um domínio](hdinsight-domain-joined-introduction.md)**: um cluster associado a um domínio do Active Directory, para que possa controlar o acesso e os dados.
* **[Clusters personalizados com ações de script](hdinsight-hadoop-customize-cluster-linux.md)**: clusters com scripts executados durante o aprovisionamento e instalam componentes adicionais.

### <a name="example-cluster-customization-scripts"></a>Scripts de personalização de clusters de exemplo
As ações de script são scripts de Bash no Linux executados durante o aprovisionamento do cluster e que podem ser utilizados para instalar componentes adicionais no cluster. 

Os seguintes scripts de exemplo seguintes são fornecidos pela equipa do HDInsight:

* **[Hue](hdinsight-hadoop-hue-linux.md)**: um conjunto de aplicações Web utilizado para interagir com um cluster. Apenas clusters do Linux.
* **[Giraph](hdinsight-hadoop-giraph-install-linux.md)**: processamento de gráficos para modelar relações entre coisas ou pessoas.
* **[Solr](hdinsight-hadoop-solr-install-linux.md)**: uma plataforma de pesquisa de nível empresarial que permite a pesquisa em texto completo em dados.

Para obter informações sobre o desenvolvimento das suas próprias Ações de Script, consulte [Desenvolvimento de Ações de Script com o HDInsight](hdinsight-hadoop-script-actions-linux.md).

## <a name="components-and-utilities-on-hdinsight-clusters"></a>Componentes e utilitários em clusters do HDInsight
Os componentes e utilitários seguintes estão incluídos nos clusters do HDInsight:

* **[Ambari](#ambari)**: Aprovisionamento, gestão, monitorização e utilitários de clusters.
* **[Avro](#avro)** (Biblioteca do Microsoft .NET para Avro): Serialização de dados para o ambiente do Microsoft .NET. 
* **[Hive e HCatalog](#hive)**: consulta de tipo SQL e uma camada de gestão de tabelas e armazenamento.
* **[Mahout](#mahout)**: para aplicações de Machine Learning dimensionáveis.
* **[MapReduce](#mapreduce)**: Arquitetura legada para a gestão distribuída de recursos e de processamento do Hadoop. Veja [YARN](#yarn).
* **[Oozie](#oozie)**: Gestão do fluxo de trabalho.
* **[Phoenix](#phoenix)**: Camada de base de dados relacional através de HBase.
* **[Pig](#pig)**: Um scripting mais simples para transformações de MapReduce.
* **[Sqoop](#sqoop)**: Importação e exportação de dados.
* **[Tez](#tez)**: Permite processos intensivos em termos de dados para executar de forma eficiente com dimensionamento.
* **[YARN](#yarn)**: gestão de recursos que faz parte da biblioteca principal do Hadoop.
* **[ZooKeeper](#zookeeper)**: Coordenação de processos em sistemas distribuídos.

> [!NOTE]
> Para obter informações sobre componentes específicos e informações das versões, veja [Hadoop components and versions in HDInsight][component-versioning] (Componentes e versões do Hadoop no HDInsight).
>
>

### <a name="ambari"></a>Ambari
O Apache Ambari destina-se ao aprovisionamento, à gestão e à monitorização de clusters do Apache Hadoop. Inclui uma coleção intuitiva de ferramentas de operador intuitiva e um conjunto robusto de APIs que ocultam a complexidade do Hadoop, simplificando a operação de clusters. Os clusters do HDInsight no Linux fornecem a IU da Web e a API REST do Ambari. As Vistas Ambari em clusters do HDInsight permitem caapacidades da IU de plug-in.
Veja [Manage HDInsight clusters using Ambari](hdinsight-hadoop-manage-ambari.md) (Gerir clusters do HDInsight com o Ambari) e a <a target="_blank" href="https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md">referência à API do Apache Ambari</a>.

### <a name="avro"></a>Avro (Biblioteca do Microsoft .NET para Avro)
A Biblioteca do Microsoft .NET para Avro implementa o formato compacto de intercâmbio de dados binários do Apache Avro para serialização para o ambiente do Microsoft .NET. Define um esquema independente de linguagem, para que os dados serializados numa linguagem possam ser lidos noutra. Pode obter informações detalhadas sobre o formato na <a target=_"blank" href="http://avro.apache.org/docs/current/spec.html">Especificação do Apache Avro</a>. O formato dos ficheiros do Avro suportam o modelo de programação distribuído do MapReduce: os ficheiros são divisíveis, o que significa que pode procurar qualquer parte de um ficheiro e começar a ler a partir daí. Para saber como, consulte [Serializar dados com a Biblioteca do Microsoft .NET para Avro](hdinsight-dotnet-avro-serialization.md). O suporte para clusters baseados em Linux chega brevemente.

### <a name="hdfs"></a>HDFS
O Sistema de Ficheiros Distribuído do Hadoop (HDFS) é um sistema de ficheiros distribuído que, com o MapReduce e o YARN, é a essência da tecnologia do Hadoop. É o sistema de ficheiros standard para clusters do Hadoop no HDInsight. Veja [Consultar dados a partir do armazenamento compatível com o HDFS](hdinsight-hadoop-use-blob-storage.md).

### <a name="hive"></a>Hive e HCatalog
O <a target="_blank" href="http://hive.apache.org/">Apache Hive</a> é um software de armazém de dados incorporado no Hadoop que lhe permite consultar e gerir grandes conjuntos de dados no armazenamento distribuído ao utilizar uma linguagem semelhante a SQL denominada HiveQL. Tal como o Pig, o HIve é uma abstração implementada por cima do MapReduce, que traduz consultas numa série de tarefas do MapReduce. O Hive é mais parecido com um sistema de gestão de bases de dados relacionais do que o Pig e é utilizado com dados mais estruturados. Para dados não estruturados, o Pig é a melhor escolha. Consulte [Utilizar o Hive com o Hadoop no HDInsight](hdinsight-use-hive.md).

O <a target="_blank" href="https://cwiki.apache.org/confluence/display/Hive/HCatalog/">Apache HCatalog</a> é uma camada de gestão de armazenamento e de tabela para o Hadoop que lhe apresenta uma vista de dados relacional. No HCatalog, pode ler e escrever ficheiros em qualquer formato que funcione para um SerDe (serializer-deserializer) do Hive.

### <a name="mahout"></a>Mahout
O <a target="_blank" href="https://mahout.apache.org/">Apache Mahout</a> é uma biblioteca de algoritmos de Machine Learning executada no Hadoop. A utilização de princípios de estatísticas, aplicações de aprendizagem máquina educam os sistemas a obter dados e a utilizar resultados anteriores para determinar o comportamento futuro. Consulte [Gerar recomendações de filmes com o Mahout no Hadoop](hdinsight-mahout.md).

### <a name="mapreduce"></a>MapReduce
O MapReduce é a arquitetura de software legada do Hadoop para a escrita de aplicações para o processamento em lote de conjuntos de macrodados em paralelo. Uma tarefa do MapReduce divide grandes conjuntos de dados e organiza os dados em pares chave-valor para processamento. As tarefas do MapReduce são executadas no [YARN](#yarn). Veja <a target="_blank" href="http://wiki.apache.org/hadoop/MapReduce">MapReduce</a> no Wiki do Hadoop.

### <a name="oozie"></a>Oozie
O <a target="_blank" href="http://oozie.apache.org/">Apache Oozie</a> é um sistema de coordenação de fluxos de trabalho que gere as tarefas do Hadoop. É integrado com a pilha do Hadoop e suporta tarefas do Hadoop para MapReduce, Pig, Hive e Sqoop. Também pode ser utilizado para agendar tarefas específicas para um sistema, como os programas de Java ou scripts de shell. Veja [Utilizar o Oozie com o Hadoop](hdinsight-use-oozie-linux-mac.md).

### <a name="phoenix"></a>Phoenix
O <a  target="_blank" href="http://phoenix.apache.org/">Apache Phoenix</a> é uma camada de base de dados relacional através de HBase. O Phoenix inclui um controlador JDBC que lhe permite consultar e gerir tabelas do SQL diretamente. O Phoenix traduz consultas e outras instruções para chamadas da API NoSQL nativas, em vez de utilizar o MapReduce, permitindo aplicações mais rápidas para além de arquivos de NoSQL. Consulte [Utilizar o Apache Phoenix e o SQuirreL com clusters do HBase](hdinsight-hbase-phoenix-squirrel.md).

### <a name="pig"></a>Pig
O <a  target="_blank" href="http://pig.apache.org/">Apache Pig</a> é uma plataforma de alto nível que lhe permite efetuar transformações do MapReduce complexas em conjuntos de dados grandes ao utilizar uma linguagem de script simples, chamada Pig Latin. O Pig traduz os scripts de Pig Latin, de modo que serão executados dentro do Hadoop. Pode criar Funções Definidas pelo Utilizador (UDFs) para expandir o Pig Latin. Veja [Utilizar o Pig com o Hadoop](hdinsight-use-pig.md).

### <a name="sqoop"></a>Sqoop
O <a  target="_blank" href="http://sqoop.apache.org/">Apache Sqoop</a> é uma ferramenta que transfere dados em massa entre o Hadoop e bases de dados relacionais, como o SQL, ou outros arquivos de dados estruturados, o mais eficientemente possível. Consulte [Utilizar o Sqoop com o Hadoop](hdinsight-use-sqoop.md).

### <a name="tez"></a>Tez
O <a  target="_blank" href="http://tez.apache.org/">Apache Tez</a> é uma arquitetura de aplicações incorporada no Hadoop YARN que executa gráficos complexos e acíclicos do processamento de dados gerais. É um sucessor mais flexível e eficaz da arquitetura do MapReduce que permite que processos intensivos em termos de dados, como o Hive, sejam executados de forma mais eficiente com dimensionamento. Consulte ["Utilizar o Apache Tez para um melhor desempenho" em Utilizar o Hive e o HiveQL](hdinsight-use-hive.md#usetez).

### <a name="yarn"></a>YARN
O Apache YARN é a próxima geração do MapReduce (MapReduce 2.0 ou MRv2) e suporta cenários de processamento de dados que ultrapassem o processamento em lote do MapReduce com maior escalabilidade e processamento em tempo real. O YARN fornece gestão de recursos e uma arquitetura de aplicações distribuída. As tarefas do MapReduce são executadas no YARN. Veja <a target="_blank" href="http://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html">Apache Hadoop NextGen MapReduce (YARN)</a> (MapReduce de NextGen do Apache Hadoop).

### <a name="zookeeper"></a>ZooKeeper
O <a  target="_blank" href="http://zookeeper.apache.org/">Apache ZooKeeper</a> coordena processos em sistemas distribuídos grandes por meio de um espaço de nomes hierárquico e partilhado de registos de dados (znodes). Os Znodes incluem pequenas quantidades de informações de metadados necessárias para coordenar processos: estado, localização, configuração, etc. Veja um exemplo do [ZooKeeper com um cluster do HBase e o Apache Phoenix](hdinsight-hbase-phoenix-squirrel-linux.md). 

## <a name="programming-languages-on-hdinsight"></a>Linguagens de programação no HDInsight
Os clusters do HDInsight – clusters do Spark, do HBase, do Kafka, do Hadoop, entre outros – suportam várias linguagens de programação, mas algumas não estão instaladas por predefinição. Para bibliotecas, módulos ou pacotes não instalados por predefinição, [utilize uma ação de script para instalar o componente](hdinsight-hadoop-script-actions-linux.md). 

### <a name="default-programming-language-support"></a>Suporte de linguagens de programação predefinidas
Por predefinição, os clusters do HDInsight suportam:

* Java
* Python

Podem ser utilizadas [ações de script](hdinsight-hadoop-script-actions-linux.md) para instalar linguagens adicionais.

### <a name="java-virtual-machine-jvm-languages"></a>Linguagens de máquina virtual de Java (JVM)
Podem ser executadas várias linguagens diferentes de Java numa máquina virtual de Java (JVM); no entanto, a execução de algumas destas linguagens pode requerer a instalação de componentes adicionais no cluster.

Estas linguagens baseadas em JVM são suportados nos clusters do HDInsight:

* Clojure
* Jython (Python para Java)
* Scala

### <a name="hadoop-specific-languages"></a>Linguagens específicas do Hadoop
Os clusters do HDInsight suportam as seguintes linguagens que são específicas da pilha de tecnologia do Hadoop:

* Pig Latin para tarefas do Pig
* HiveQL para tarefas do Hive e SparkSQL

## <a name="hdinsight-standard-and-hdinsight-premium"></a>HDInsight Standard e HDInsight Premium
O HDInsight fornece ofertas em nuvem de macrodados em duas categorias: Standard e Premium. O HDInsight Standard fornece um cluster de escala empresarial que as organizações podem utilizar para executar as respetivas cargas de trabalho de macrodados. O HDInsight Premium baseia-se nas capacidades Standard e fornece capacidades avançadas de análise e segurança para clusters do HDInsight. Para obter mais informações, consulte [Azure HDInsight Premium](hdinsight-component-versioning.md#hdinsight-standard-and-hdinsight-premium)

## <a name="microsoft-business-intelligence-and-hdinsight"></a>Microsoft Business Intelligence e o HDInsight
As ferramentas de business intelligence (BI) familiares obtêm, analisam e reportam dados integrados no HDInsight através do suplemento Power Query ou Microsoft Hive ODBC Driver:

* [Ligar o Excel ao Hadoop com o Power Query](hdinsight-connect-excel-power-query.md): saiba como utilizar o Microsoft Power Query para Excel para ligar o Excel à conta do Armazenamento do Azure que armazena os dados do seu cluster do HDInsight. Estação de trabalho do Windows necessária. 
* [Ligar o Excel ao Hadoop com o Controlador ODBC do Microsoft Hive](hdinsight-connect-excel-hive-odbc-driver.md): Saiba como importar dados do HDInsight com o Controlador ODBC do Microsoft Hive. Estação de trabalho do Windows necessária. 
* [Plataforma do Microsoft Cloud](http://www.microsoft.com/server-cloud/solutions/business-intelligence/default.aspx): Saiba mais sobre o Power BI para Office 365, transfira a versão de avaliação do SQL Server e configure o SharePoint Server 2013 e o SQL Server BI.
* [SQL Server Analysis Services](http://msdn.microsoft.com/library/hh231701.aspx)
* [SQL Server Reporting Services](http://msdn.microsoft.com/library/ms159106.aspx)


## <a name="next-steps"></a>Passos seguintes

* [Introdução ao Hadoop no HDInsight](hdinsight-hadoop-linux-tutorial-get-started.md): um tutorial de início rápido para o aprovisionamento de clusters do HDInsight Hadoop e a execução de consultas do Hive de exemplo.
* [Começar com o Spark no HDInsight](hdinsight-apache-spark-jupyter-spark-sql.md): um tutorial de início rápido para criar um cluster Spark e executar consultas de Spark SQL interativas.
* [Utilizar o R Server no HDInsight](hdinsight-hadoop-r-server-get-started.md): começar a utilizar o R Server no HDInsight Premium.
* [Aprovisionar clusters do HDInsight](hdinsight-hadoop-provision-linux-clusters.md): saiba como aprovisionar um cluster do HDInsight Hadoop através do portal do Azure, da CLI do Azure ou do Azure PowerShell.


[component-versioning]: hdinsight-component-versioning.md
[zookeeper]: http://zookeeper.apache.org/