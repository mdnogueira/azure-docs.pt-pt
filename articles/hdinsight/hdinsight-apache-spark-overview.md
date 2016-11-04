---
title: Uma descrição geral do Apache Spark no HDInsight | Microsoft Docs
description: Uma introdução ao Apache Spark no HDInsight e cenários nos quais pode ser utilizado o Spark no HDInsight nas aplicações.
services: hdinsight
documentationcenter: ''
author: nitinme
manager: jhubbard
editor: cgronlun
tags: azure-portal

ms.service: hdinsight
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 08/25/2016
ms.author: nitinme

---
# Descrição geral: Apache Spark no HDInsight Linux
<a href="http://spark.apache.org/" target="_blank">O Apache Spark</a> é uma arquitetura de processamento paralelo open source que suporta o processamento dentro da memória para melhorar o desempenho de aplicações de análise de macrodados. O motor de processamento do Spark foi concebido para ser rápido, fácil de utilizar e efetuar análises sofisticadas. As capacidades de computação dentro da memória do Spark transformam-no numa boa opção para algoritmos iterativos na Machine Learning e nas operações de gráficos. O Spark também é compatível com o Blob Storage do Azure (WASB), pelo que os dados existentes armazenados no Azure podem ser facilmente processados através do Spark.

Quando cria um cluster do Spark no HDInsight, está a criar recursos de computação do Azure com o Spark instalado e configurado. Demora apenas cerca de dez minutos para criar um cluster do Spark no HDInsight. Os dados a serem processados são armazenados no Blob Storage do Azure. Consulte [Utilizar o Blob Storage do Azure com o HDInsight][hdinsight-storage].

![Apache Spark no Azure HDInsight](./media/hdinsight-apache-spark-overview/hdispark.architecture.png "Apache Spark on Azure HDInsight")

**Pretende começar a utilizar o Apache Spark no Azure HDInsight?** Consulte [Início Rápido: criar um cluster do Spark no HDInsight Linux e executar aplicações de exemplo com o Jupyter](hdinsight-apache-spark-jupyter-spark-sql.md).

> [!NOTE]
> Para obter uma lista de limitações e problemas conhecidos da versão atual, consulte [Problemas conhecidos do Apache Spark no Azure HDInsight (Linux)](hdinsight-apache-spark-jupyter-spark-sql.md).
> 
> 

## Porquê utilizar o Spark no Azure HDInsight?
O Azure HDInsight oferece um serviço Spark completamente gerido. As vantagens da utilização do Spark no HDInsight são as seguintes:

| Funcionalidade | Descrição |
| --- | --- |
| Facilidade de criação de clusters |Pode criar um novo cluster do Spark no HDInsight em apenas alguns minutos com o Portal de Gestão do Azure, o Azure PowerShell ou o SDK .NET do HDInsight. Consulte [Introdução ao cluster do Spark no HDInsight](hdinsight-apache-spark-jupyter-spark-sql.md) |
| Facilidade de utilização |O Spark nos clusters do HDInsight inclui blocos de notas do Jupyter pré-configurados. Pode utilizá-los para o processamento e a visualização de dados interativos. O URLs é https://CLUSTERNAME.azurehdinsight.net/jupyter. Substitua **CLUSTERNAME** pelo nome do cluster do Spark HDInsight. |
| APIs REST |O Spark no HDInsight inclui o [Livy](https://github.com/cloudera/hue/tree/master/apps/spark/java#welcome-to-livy-the-rest-spark-server), um servidor de tarefas do Spark baseado na API REST para submeter e monitorizar remotamente tarefas em execução. |
| Suporte para o Data Lake Store do Azure |O Spark no HDInsight pode ser configurado para utilizar o Data Lake Store do Azure como um armazenamento adicional. Para obter mais informações sobre o Data Lake Store, consulte [Descrição geral do Data Lake Store do Azure](../data-lake-store/data-lake-store-overview.md). |
| Integração com os serviços do Azure |O Spark no HDInsight é fornecido com um conector para Event Hubs do Azure. Os clientes podem criar aplicações de transmissão em fluxo com os Event Hubs, para além do [Kafka](http://kafka.apache.org/), que já se encontra disponível como parte do Spark. |
| Suporte para o Servidor R |Pode configurar um Servidor R num cluster do HDInsight Spark para executar operações R distribuídas com as velocidades prometidas com um cluster do Spark. Para obter mais informações, consulte [Começar a utilizar o Servidor R no HDInsight](hdinsight-hadoop-r-server-get-started.md). |
| Integração com o IntelliJ IDEA |Pode utilizar o Plug-in do HDInsight para o IntelliJ para criar e submeter aplicações para um cluster do HDInsight Spark. Para obter mais informações, consulte [Utilizar o Plug-in de Ferramentas do HDInsight para o IntelliJ IDEA para criar Spark aplicações para o cluster do HDInsight Spark Linux](hdinsight-apache-spark-intellij-tool-plugin.md). |
| Consultas em Simultâneo |O Spark no HDInsight suporta consultas em simultâneo. Isto permite que várias consultas de um utilizador ou várias consultas de vários utilizadores e aplicações partilhem os mesmos recursos de cluster. |
| Colocação em cache em SSDs |Pode optar por colocar os dados em cache na memória ou em SSDs ligados aos nós do cluster. A colocação em cache na memória oferece o melhor desempenho da consulta, mas pode ser dispendiosa; a colocação em cache em SSDs fornece uma excelente opção para melhorar o desempenho da consulta sem a necessidade de criar um cluster de um tamanho necessário para guardar todo o conjunto de dados na memória. |
| Integração com Ferramentas de BI |O Spark para o HDInsight fornece conectores para ferramentas de BI, tais como [Power BI](http://www.powerbi.com/) e [Tableau](http://www.tableau.com/products/desktop), para análise de dados. |
| Bibliotecas Anaconda pré-carregadas |Os clusters do Spark no HDInsight incluem bibliotecas Anaconda pré-instaladas. O [Anaconda](http://docs.continuum.io/anaconda/) fornece cerca de 200 bibliotecas de Machine Learning, análise de dados, visualização, etc. |
| Escalabilidade |Embora possa especificar o número de nós do cluster durante a criação, pode querer aumentar ou diminuir o cluster para que coincida à carga de trabalho. Todos os clusters do HDInsight permitem-lhe alterar o número de nós do cluster. Além disso, é possível ignorar os clusters do Spark sem perda de dados, uma vez que todos os dados são armazenados no Blob Storage do Azure. |
| Suporte 24 horas por dia, 7 dias por semana |O Spark no HDInsight inclui suporte de nível empresarial 24 horas por dia, 7 dias por semana e um SLA de 99,9% de tempo ativo. |

## Quais são os casos de utilização do Spark no HDInsight?
O Apache Spark no HDInsight possibilita os seguintes cenários-chave.

### Análise de dados interativa e BI
[Ver um tutorial](hdinsight-apache-spark-use-bi-tools.md)

O Apache Spark no HDInsight armazena dados em Blobs do Azure. Os especialistas em negócio e os decisores-chave podem analisar e criar relatórios com base nesses dados e utilizar o Microsoft Power BI para criar relatórios interativos a partir dos dados analisados. Os analistas podem partir de dados não estruturados/semiestruturados no Storage do Azure, definir um esquema para os dados utilizando os blocos de notas e, em seguida, criar modelos de dados através do Microsoft Power BI. O Spark no HDInsight também suporta várias ferramentas BI de terceiros, como o Tableau, o Qlikview e o SAP Lumira, transformando-o numa plataforma ideal para os analistas de dados, os especialistas em negócios e os decisores-chave.

### Machine Learning Iterativa
[Ver um tutorial: Prever temperaturas de construção com dados de AVAC](hdinsight-apache-spark-ipython-notebook-machine-learning.md)

[Ver um tutorial: Prever resultados de inspeções alimentares](hdinsight-apache-spark-machine-learning-mllib-ipython.md)

O Apache Spark inclui o [MLlib](http://spark.apache.org/mllib/), uma biblioteca de Machine Learning baseada no Spark. Para além disso, o Spark no HDInsight também inclui o Anaconda, uma distribuição do Python com uma variedade de pacotes de Machine Learning. Associe a isto o suporte incorporado para blocos de notas do Jupyter e obtenha um ambiente topo de gama para a criação de aplicações de Machine Learning.  

### Análise de dados de transmissão em fluxo e em tempo real
[Ver um tutorial](hdinsight-apache-spark-eventhub-streaming.md)

A análise de dados em tempo real é utilizada para cenários desde a redução do tempo de obtenção dos dados, ao processá-los à medida que chegam, até à criação de uma verdadeira solução de transmissão em fluxo. O Spark no HDInsight oferece um suporte avançado para a criação de soluções de análise em tempo real. Embora o Spark já tenha conectores para ingerir dados de várias fontes, como o Kafka, o Flume, o Twitter, o ZeroMQ ou os sockets de TCP, o Spark no HDInsight acrescenta um suporte de primeira classe para a ingestão de dados a partir de Event Hubs do Azure. Os Event Hubs são o serviço de colocação em fila mais utilizado no Azure. O facto de ter um suporte imediato para os Event Hubs faz com que o Spark no HDInsight seja uma plataforma ideal para a criação do pipeline de análise em tempo real.

## <a name="next-steps"></a>Que componentes são incluídos como parte de um cluster do Spark?
O Spark no HDInsight inclui os seguintes componentes, que estão disponíveis nos clusters por predefinição.

* [Spark Core](https://spark.apache.org/docs/1.5.1/). Inclui o Spark Core, o Spark SQL, APIs de transmissão em fluxo do Spark, o GraphX e o MLlib.
* [Anaconda](http://docs.continuum.io/anaconda/)
* [Livy](https://github.com/cloudera/hue/tree/master/apps/spark/java#welcome-to-livy-the-rest-spark-server)
* [Bloco de Notas do Jupyter](https://jupyter.org)

O Spark no HDInsight também fornece um [controlador ODBC](http://go.microsoft.com/fwlink/?LinkId=616229) para a conectividade com clusters do Spark no HDInsight a partir de ferramentas de BI, como o Microsoft Power BI e o Tableau.

## Por onde devo começar?
Comece por criar um cluster do Spark no HDInsight Linux. Consulte [Início Rápido: criar um cluster do Spark no HDInsight Linux e executar aplicações de exemplo com o Jupyter](hdinsight-apache-spark-jupyter-spark-sql.md). 

## Passos Seguintes
### Cenários
* [Spark com BI: Efetuar uma análise de dados interativa com o Spark no HDInsight com ferramentas de BI](hdinsight-apache-spark-use-bi-tools.md)
* [Spark com Machine Learning: Utilizar o Spark no HDInsight para analisar a temperatura de construção com dados de AVAC](hdinsight-apache-spark-ipython-notebook-machine-learning.md)
* [Spark com Machine Learning: Utilizar o Spark no HDInsight para prever resultados de inspeções alimentares](hdinsight-apache-spark-machine-learning-mllib-ipython.md)
* [Transmissão em Fluxo do Spark: Utilizar o Spark no HDInsight para a criação de aplicações de transmissão em fluxo em tempo real](hdinsight-apache-spark-eventhub-streaming.md)
* [Análise de registos de Web Sites com o Spark no HDInsight](hdinsight-apache-spark-custom-library-website-log-analysis.md)

### Criar e executar aplicações
* [Criar uma aplicação autónoma com o Scala](hdinsight-apache-spark-create-standalone-application.md)
* [Executar tarefas remotamente num cluster do Spark com o Livy](hdinsight-apache-spark-livy-rest-interface.md)

### Ferramentas e extensões
* [Utilizar o Plug-in das Ferramentas do HDInsight para o IntelliJ IDEA para criar e submeter aplicações do Spark Scala](hdinsight-apache-spark-intellij-tool-plugin.md)
* [Utilizar o Plug-in das Ferramentas do HDInsight para o IntelliJ IDEA para depurar aplicações do Spark remotamente](hdinsight-apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Utilizar blocos de notas do Zeppelin com um cluster do Spark no HDInsight](hdinsight-apache-spark-use-zeppelin-notebook.md)
* [Kernels disponíveis para o bloco de notas do Jupyter no cluster do Spark para o HDInsight](hdinsight-apache-spark-jupyter-notebook-kernels.md)
* [Utilizar pacotes externos com blocos de notas do Jupyter](hdinsight-apache-spark-jupyter-notebook-use-external-packages.md)
* [Instalar o Jupyter no computador e estabelecer ligação a um cluster do Spark do HDInsight](hdinsight-apache-spark-jupyter-notebook-install-locally.md)

### Gerir recursos
* [Gerir recursos para o cluster do Apache Spark no Azure HDInsight](hdinsight-apache-spark-resource-manager.md)
* [Controlar e depurar tarefas em execução num cluster do Apache Spark do HDInsight](hdinsight-apache-spark-job-debugging.md)

[hdinsight-storage]: hdinsight-hadoop-use-blob-storage.md



<!--HONumber=Sep16_HO3-->


