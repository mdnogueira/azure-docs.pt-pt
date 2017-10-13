---
title: "Introdução ao Spark no Azure HDInsight | Microsoft Docs"
description: "Este artigo fornece uma introdução ao Spark no HDInsight e os diferentes cenários em que pode utilizar o cluster do Spark no HDInsight."
keywords: "o que é o apache spark,cluster do spark,introdução ao spark,spark no hdinsight"
services: hdinsight
documentationcenter: 
author: maxluk
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 82334b9e-4629-4005-8147-19f875c8774e
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 09/07/2017
ms.author: maxluk
ms.openlocfilehash: 9d66931e1c855788163d92f0c3f34f55c44615dd
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="introduction-to-spark-on-hdinsight"></a>Introdução ao Spark no HDInsight

Este artigo fornece uma introdução ao Spark no HDInsight. O <a href="http://spark.apache.org/" target="_blank">Apache Spark</a> é uma arquitetura de processamento paralelo open source que suporta o processamento dentro da memória para melhorar o desempenho de aplicações de análise de macrodados. O cluster do Spark no HDInsight é compatível com o Armazenamento do Azure (WASB), bem como com o Azure Data Lake Store. Por conseguinte, os dados existentes armazenados no Azure podem ser facilmente processados através de um cluster do Spark.

Quando cria um cluster do Spark no HDInsight, está a criar recursos de computação do Azure com o Spark instalado e configurado. Demora apenas cerca de dez minutos para criar um cluster do Spark no HDInsight. Os dados a serem processados são armazenados no Armazenamento do Azure ou no Azure Data Lake Store. Veja [Use Azure Blob Storage with HDInsight (Utilizar o Armazenamento de Blobs do Azure com o HDInsight)](hdinsight-hadoop-use-blob-storage.md).

![Spark: uma arquitetura unificada](./media/hdinsight-apache-spark-overview/hdinsight-spark-overview.png)

## <a name="spark-vs-traditional-mapreduce"></a>Spark vs. MapReduce tradicional

O que faz com que o Spark seja rápido? Em que é que a arquitetura do Apache Spark é diferente do MapReduce tradicional, permitindo-lhe oferecer um desempenho superior para a partilha de dados?

![MapReduce tradicional vs. Spark](./media/hdinsight-apache-spark-overview/mapreduce-vs-spark.png)

O Spark fornece primitivos para a computação de cluster na memória. Os trabalhos do Spark podem carregar e colocar os dados em cache na memória e consultá-los repetidamente de forma muito mais rápida do que os sistemas baseados em discos. O Spark também se integra na linguagem de programação Scala, o que lhe possibilita manipular conjuntos de dados distribuídos, como coleções locais. Não é necessário estruturar tudo como operações de mapa e redução.

No Spark, a partilha de dados entre operações é mais rápida porque os dados estão na memória. Em contrapartida, o Hadoop partilha dados através do HDFS, o que demora mais tempo a processar.

## <a name="what-is-apache-spark-on-azure-hdinsight"></a>O que é o Apache Spark no Azure HDInsight?
Os clusters do Spark no HDInsight oferecem um serviço Spark completamente gerido. Os benefícios da criação de um cluster do Spark no HDInsight estão listados aqui.

| Funcionalidade | Descrição |
| --- | --- |
| Facilidade de criação de clusters do Spark |Pode criar um novo cluster do Spark no HDInsight em apenas alguns minutos com o portal do Azure, o Azure PowerShell ou o SDK .NET do HDInsight. Consulte [Introdução ao cluster do Spark no HDInsight](hdinsight-apache-spark-jupyter-spark-sql.md) |
| Facilidade de utilização |O cluster do Spark no HDInsight inclui blocos de notas do Jupyter e Zeppelin. Pode utilizar estes blocos de notas para o processamento e a visualização de dados interativos.|
| APIs REST |Os clusters do Spark no HDInsight incluem o [Livy](https://github.com/cloudera/hue/tree/master/apps/spark/java#welcome-to-livy-the-rest-spark-server), um servidor de tarefas do Spark baseado na API REST para submeter e monitorizar remotamente tarefas. |
| Suporte para o Data Lake Store do Azure | O cluster do Spark no HDInsight pode ser configurado para utilizar o Azure Data Lake Store como armazenamento adicional, bem como armazenamento primário (apenas com clusters do HDInsight 3.5). Para obter mais informações sobre o Data Lake Store, consulte [Descrição geral do Data Lake Store do Azure](../data-lake-store/data-lake-store-overview.md). |
| Integração com os serviços do Azure |O cluster do Spark no HDInsight é fornecido com um conector para Hubs de Eventos do Azure. Os clientes podem criar aplicações de transmissão em fluxo com os Event Hubs, para além do [Kafka](http://kafka.apache.org/), que já se encontra disponível como parte do Spark. |
| Suporte para o Servidor R | Pode configurar um Servidor R num cluster do HDInsight Spark para executar operações R distribuídas com as velocidades prometidas com um cluster do Spark. Para obter mais informações, consulte [Começar a utilizar o Servidor R no HDInsight](hdinsight-hadoop-r-server-get-started.md). |
| Integração com IDEs de terceiros | O HDInsight fornece plug-ins para IDEs como o IntelliJ IDEIA e Eclipse, que pode utilizar para criar e submeter aplicações para um cluster do HDInsight Spark. Para obter mais informações, veja [Use Azure Toolkit for IntelliJ IDEA](hdinsight-apache-spark-intellij-tool-plugin.md) (Utilizar o Azure Toolkit para IntelliJ IDEA) e [Use Azure Toolkit for Eclipse](hdinsight-apache-spark-eclipse-tool-plugin.md) (Utilizar o Azure Toolkit para Eclipse).|
| Consultas em Simultâneo |Os clusters do Spark no HDInsight suportam consultas em simultâneo. Isto permite que várias consultas de um utilizador ou várias consultas de vários utilizadores e aplicações partilhem os mesmos recursos de cluster. |
| Colocação em cache em SSDs |Pode optar por colocar os dados em cache na memória ou em SSDs ligados aos nós do cluster. A colocação em cache na memória oferece o melhor desempenho da consulta, mas pode ser dispendiosa; a colocação em cache em SSDs fornece uma excelente opção para melhorar o desempenho da consulta sem a necessidade de criar um cluster de um tamanho necessário para guardar todo o conjunto de dados na memória. |
| Integração com Ferramentas de BI |Os clusters do Spark no HDInsight fornecem conectores para ferramentas de BI, como o [Power BI](http://www.powerbi.com/) e [Tableau](http://www.tableau.com/products/desktop), para análise de dados. |
| Bibliotecas Anaconda pré-carregadas |Os clusters do Spark no HDInsight incluem bibliotecas Anaconda pré-instaladas. O [Anaconda](http://docs.continuum.io/anaconda/) fornece cerca de 200 bibliotecas de Machine Learning, análise de dados, visualização, etc. |
| Escalabilidade |Embora possa especificar o número de nós do cluster durante a criação, pode querer aumentar ou diminuir o cluster para que coincida à carga de trabalho. Todos os clusters do HDInsight permitem-lhe alterar o número de nós do cluster. Além disso, é possível ignorar os clusters do Spark sem perda de dados, uma vez que todos os dados são armazenados no Armazenamento do Azure ou no Azure Data Lake Store. |
| Suporte 24 horas por dia, 7 dias por semana |Os clusters do Spark no HDInsight incluem suporte de nível empresarial 24 horas por dia, 7 dias por semana e um SLA de 99,9% de tempo ativo. |

## <a name="spark-cluster-architecture"></a>Arquitetura de cluster do Spark

Eis a arquitetura de cluster do Spark e como funciona:

![Arquitetura de cluster do Spark](./media/hdinsight-apache-spark-overview/spark-architecture.png)

O nó principal tem o mestre do Spark que gere o número de aplicações; as aplicações são mapeadas para o controlador do Spark. Cada aplicação é gerida pelo mestre do Spark de várias formas. O Spark pode ser implementado por cima dos gestores de clusters Mesos, do YARN ou Spark, que aloca recursos de nó de trabalho às aplicações. No HDInsight, o Spark é executado com o gestor de clusters YARN. Os recursos no cluster são geridos pelo mestre do Spark no HDInsight. Isto significa que o mestre do Spark tem conhecimento de que recursos, como a memória, estão ocupados ou disponíveis no nó de trabalho.

O controlador executa a função principal do utilizador e executa as diversas operações paralelas nos nós de trabalho. Em seguida, recolhe os resultados das operações. Os nós de trabalho leem e escrevem dados de e para o sistema de ficheiros distribuído do Hadoop (HFDS). Também colocam em cache os dados transformados na memória como Conjuntos de Dados Distribuídos Resilientes (RDDs).

Assim que a aplicação for criada no mestre do Spark, este aloca os recursos às aplicações, criando uma execução designada “controlador do Spark”. O controlador do Spark também cria o SparkContext e também inicia a criação dos RDDs. Os metadados dos RDDs são armazenados no controlador do Spark.

O controlador do Spark liga-se ao mestre do Spark e é responsável pela conversão das aplicações em gráficos dirigidos (DAG) de tarefas individuais que são executadas num processo de executor nos nós de trabalho. Cada aplicação tem os seus próprios processos de executor, que permanecem em funcionamento ao longo da duração de toda a aplicação e executam tarefas em múltiplos threads.

## <a name="what-are-the-use-cases-for-spark-on-hdinsight"></a>Quais são os casos de utilização do Spark no HDInsight?
Os clusters do Spark no HDInsight permitem os cenários-chave seguintes:

### <a name="interactive-data-analysis-and-bi"></a>Análise de dados interativa e BI
[Ver um tutorial](hdinsight-apache-spark-use-bi-tools.md)

O Apache Spark no HDInsight armazena dados no Armazenamento do Azure ou no Azure Data Lake Store. Os especialistas em negócio e os decisores-chave podem analisar e criar relatórios com base nesses dados e utilizar o Microsoft Power BI para criar relatórios interativos a partir dos dados analisados. Os analistas podem partir de dados não estruturados/semiestruturados no armazenamento de clusters, definir um esquema para os dados com os blocos de notas e, em seguida, criar modelos de dados através do Microsoft Power BI. Os clusters do Spark no HDInsight também suportam várias ferramentas de BI de terceiros, como o Tableau, transformando-o numa plataforma ideal para os analistas de dados, os especialistas em negócios e os decisores-chave.

### <a name="spark-machine-learning"></a>Spark Machine Learning
[Look at a tutorial: Predict building temperatures using HVAC data](hdinsight-apache-spark-ipython-notebook-machine-learning.md) (Ver um tutorial: Prever temperaturas de construção com dados de AVAC)

[Ver um tutorial: Prever resultados de inspeções alimentares](hdinsight-apache-spark-machine-learning-mllib-ipython.md)

O Apache Spark inclui o [MLlib](http://spark.apache.org/mllib/), uma biblioteca de aprendizagem automática baseada no Spark, que pode utilizar a partir de um cluster do Spark no HDInsight. Além disso, o cluster do Spark no HDInsight também inclui o Anaconda, uma distribuição da Python com uma variedade de pacotes de aprendizagem automática. Associe a isto o suporte incorporado para blocos de notas do Jupyter e Zeppelin, e obtenha um ambiente topo de gama para a criação de aplicações de aprendizagem automática.

### <a name="spark-streaming-and-real-time-data-analysis"></a>Análise de dados de transmissão em fluxo e em tempo real do Spark
[Ver um tutorial](hdinsight-apache-spark-eventhub-streaming.md)

Os clusters do Spark no HDInsight oferecem um suporte avançado para a criação de soluções de análise em tempo real. Embora o Spark já tenha conectores para ingerir dados de várias fontes, como o Kafka, o Flume, o Twitter, o ZeroMQ ou os sockets de TCP, o Spark no HDInsight acrescenta um suporte de primeira classe para a ingestão de dados a partir de Event Hubs do Azure. Os Hubs de Eventos são o serviço de colocação em fila mais utilizado no Azure. O facto de ter um suporte imediato para os Hubs de Eventos faz com que os clusters do Spark no HDInsight sejam uma plataforma ideal para a criação do pipeline de análise em tempo real.

## <a name="next-steps"></a>Que componentes são incluídos como parte de um cluster do Spark?
Os clusters do Spark no HDInsight incluem os seguintes componentes, que estão disponíveis nos clusters por predefinição.

* [Spark Core](https://spark.apache.org/docs/1.5.1/). Inclui o Spark Core, o Spark SQL, APIs de transmissão em fluxo do Spark, o GraphX e o MLlib.
* [Anaconda](http://docs.continuum.io/anaconda/)
* [Livy](https://github.com/cloudera/hue/tree/master/apps/spark/java#welcome-to-livy-the-rest-spark-server)
* [Bloco de notas do Jupyter](https://jupyter.org)
* [Bloco de notas do Zeppelin](http://zeppelin-project.org/)

Os clusters do Spark no HDInsight também fornecem um [controlador ODBC](http://go.microsoft.com/fwlink/?LinkId=616229) para a conectividade com clusters do Spark no HDInsight a partir de ferramentas de BI, como o Microsoft Power BI e o Tableau.

## <a name="where-do-i-start"></a>Por onde devo começar?
Comece por criar um cluster do Spark no HDInsight. Veja o [Guia de Introdução: criar um cluster do Spark no HDInsight Linux e executar uma consulta interativa com o Jupyter](hdinsight-apache-spark-jupyter-spark-sql.md). 

## <a name="next-steps"></a>Passos Seguintes
### <a name="scenarios"></a>Cenários
* [Spark com BI: Efetuar uma análise de dados interativa com o Spark no HDInsight com ferramentas do BI](hdinsight-apache-spark-use-bi-tools.md)
* [Spark com Machine Learning: Utilizar o Spark no HDInsight para analisar a temperatura do edifício com dados de AVAC](hdinsight-apache-spark-ipython-notebook-machine-learning.md)
* [Spark com Machine Learning: Utilizar o Spark no HDInsight para prever resultados de inspeções alimentares](hdinsight-apache-spark-machine-learning-mllib-ipython.md)
* [Transmissão em Fluxo do Spark: Utilizar o Spark no HDInsight para criar aplicações de transmissão em fluxo em tempo real](hdinsight-apache-spark-eventhub-streaming.md)
* [Análise de registos de sites com o Spark no HDInsight](hdinsight-apache-spark-custom-library-website-log-analysis.md)

### <a name="create-and-run-applications"></a>Criar e executar aplicações
* [Criar uma aplicação autónoma com o Scala](hdinsight-apache-spark-create-standalone-application.md)
* [Executar tarefas remotamente num cluster do Spark com o Livy](hdinsight-apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>Ferramentas e extensões
* [Utilizar o Plug-in das Ferramentas do HDInsight para o IntelliJ IDEA para criar e submeter aplicações do Spark Scala](hdinsight-apache-spark-intellij-tool-plugin.md)
* [Utilizar o Plug-in das Ferramentas do HDInsight para o IntelliJ IDEA para depurar aplicações do Spark remotamente](hdinsight-apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Utilizar blocos de notas do Zeppelin com um cluster do Spark no HDInsight](hdinsight-apache-spark-zeppelin-notebook.md)
* [Kernels disponíveis para o bloco de notas do Jupyter no cluster do Spark para o HDInsight](hdinsight-apache-spark-jupyter-notebook-kernels.md)
* [Utilizar pacotes externos com blocos de notas do Jupyter](hdinsight-apache-spark-jupyter-notebook-use-external-packages.md)
* [Instalar o Jupyter no computador e ligar a um cluster do Spark do HDInsight](hdinsight-apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>Gerir recursos
* [Gerir recursos para o cluster do Apache Spark no Azure HDInsight](hdinsight-apache-spark-resource-manager.md)
* [Controlar e depurar tarefas em execução num cluster do Apache Spark do HDInsight](hdinsight-apache-spark-job-debugging.md)
* [Problemas conhecidos do Apache Spark no Azure HDInsight](hdinsight-apache-spark-known-issues.md).
