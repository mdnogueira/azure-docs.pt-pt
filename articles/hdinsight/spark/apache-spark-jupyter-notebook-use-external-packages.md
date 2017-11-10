---
title: Utilizar pacotes de Maven personalizadas com o Jupyter no Spark no Azure HDInsight | Microsoft Docs
description: "Instruções passo a passo sobre como configurar disponíveis blocos de notas do Jupyter com clusters do HDInsight Spark para utilizar pacotes de Maven personalizados."
services: hdinsight
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 2a8bc545-064e-436f-8b5f-e67c26cfbf98
ms.service: hdinsight
ms.custom: hdinsightactive
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/22/2017
ms.author: nitinme
ms.openlocfilehash: 71a64f3d23b495a3b00d36b1d4557425604a772d
ms.sourcegitcommit: 93902ffcb7c8550dcb65a2a5e711919bd1d09df9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/09/2017
---
# <a name="use-external-packages-with-jupyter-notebooks-in-apache-spark-clusters-on-hdinsight"></a>Utilizar pacotes externos com blocos de notas do Jupyter nos clusters do Apache Spark no HDInsight
> [!div class="op_single_selector"]
> * [Utilizar a magia de células](apache-spark-jupyter-notebook-use-external-packages.md)
> * [Através da ação de Script](apache-spark-python-package-installation.md)
>
>

Saiba como configurar um bloco de notas do Jupyter no cluster do Apache Spark no HDInsight ao utilizar externo, contribuíram de Comunidade **maven** pacotes que não são incluídos out of box no cluster. 

Pode pesquisar o [repositório Maven](http://search.maven.org/) para a lista completa de pacotes que estão disponíveis. Também pode obter uma lista de pacotes disponíveis a partir de outras origens. Por exemplo, uma lista completa dos pacotes contribuíram de Comunidade está disponível em [Spark pacotes](http://spark-packages.org/).

Neste artigo, ficará a saber como utilizar o [spark csv](http://search.maven.org/#artifactdetails%7Ccom.databricks%7Cspark-csv_2.10%7C1.4.0%7Cjar) pacote com o bloco de notas do Jupyter.



## <a name="prerequisites"></a>Pré-requisitos
Tem de ter o seguinte:

* Um cluster do Apache Spark no HDInsight. Para obter instruções, consulte [clusters do Apache Spark criar no Azure HDInsight](apache-spark-jupyter-spark-sql.md).

## <a name="use-external-packages-with-jupyter-notebooks"></a>Utilizar pacotes externos com blocos de notas do Jupyter
1. No [Portal do Azure](https://portal.azure.com/), no startboard, clique no mosaico relativo ao cluster do Spark (se o tiver afixado ao startboard). Também pode navegar até ao cluster em **Procurar Tudo** > **Clusters do HDInsight**.   
2. No painel do cluster do Spark, clique em **Ligações Rápidas** e, em seguida, no painel **Dashboard de Clusters**, clique em **Bloco de Notas do Jupyter**. Se lhe for solicitado, introduza as credenciais de administrador do cluster.

    > [!NOTE]
    > Também pode aceder ao Bloco de Notas do Jupyter para o cluster abrindo o seguinte URL no seu browser. Substitua **CLUSTERNAME** pelo nome do cluster:
    > 
    > `https://CLUSTERNAME.azurehdinsight.net/jupyter`
    > 

   

3. Crie um novo bloco de notas. Clique em **novo**e, em seguida, clique em **Spark**.
   
    ![Criar um novo bloco de notas Jupyter](./media/apache-spark-jupyter-notebook-use-external-packages/hdinsight-spark-create-notebook.png "Criar um novo bloco de notas Jupyter")

4. É criado e aberto um novo bloco de notas com o nome Untitled.pynb. Clique no nome do bloco de notas na parte superior e introduza um nome amigável.
   
    ![Forneça um nome para o bloco de notas](./media/apache-spark-jupyter-notebook-use-external-packages/hdinsight-spark-name-notebook.png "Forneça um nome para o bloco de notas")

5. Irá utilizar o `%%configure` magic para configurar o bloco de notas para utilizar um pacote externo. Em blocos de notas que utilizam pacotes externos, certifique-se de que chama a `%%configure` mágica na primeira célula do código. Isto garante que o kernel está configurado para utilizar o pacote antes da sessão iniciada.

    >[!IMPORTANT] 
    >Caso se esqueça configurar o kernel na primeira célula, pode utilizar o `%%configure` com o `-f` parâmetro, mas que irá reiniciar a sessão e progresso de todas as serão perdido.

    | Versão do HDInsight | Comando |
    |-------------------|---------|
    |Para o HDInsight 3.3 e o HDInsight 3.4 | `%%configure` <br>`{ "packages":["com.databricks:spark-csv_2.10:1.4.0"] }`|
    | Para o HDInsight 3.5 e o HDInsight 3.6 | `%%configure`<br>`{ "conf": {"spark.jars.packages": "com.databricks:spark-csv_2.10:1.4.0" }}`|

6. O fragmento acima espera as coordenadas do maven para o pacote externo no repositório Central Maven. Este fragmento `com.databricks:spark-csv_2.10:1.4.0` é a coordenada maven para **spark csv** pacote. Eis como pode construir as coordenadas de um pacote.
   
    a. Localize o pacote no repositório de Maven. Para este tutorial, utilizamos [spark csv](http://search.maven.org/#artifactdetails%7Ccom.databricks%7Cspark-csv_2.10%7C1.4.0%7Cjar).
   
    b. A partir dos repositório, reúna os valores para **GroupId**, **ArtifactId**, e **versão**. Certifique-se de que os valores que recolher corresponder ao seu cluster. Neste caso, estamos a utilizar um Scala 2.10 e o pacote de Spark 1.4.0, mas poderá ter de selecionar as diferentes versões para o Scala adequado ou a versão do Spark no seu cluster. Pode encontrar a versão de Scala no seu cluster executando `scala.util.Properties.versionString` no Spark Jupyter kernel ou envio de Spark. Pode encontrar a versão do Spark no seu cluster executando `sc.version` nos blocos de notas do Jupyter.
   
    ![Utilizar pacotes externos com o bloco de notas do Jupyter](./media/apache-spark-jupyter-notebook-use-external-packages/use-external-packages-with-jupyter.png "utilizar pacotes externos com o bloco de notas do Jupyter")
   
    c. Concatenar os três valores separados por ponto e vírgula (**:**).
   
        com.databricks:spark-csv_2.10:1.4.0

7. Execute a célula de código com o `%%configure` magic. Isto irá configurar a sessão de Livy subjacente para utilizar o pacote que forneceu. Nas células subsequentes no bloco de notas, agora, pode utilizar o pacote, conforme mostrado abaixo.
   
        val df = sqlContext.read.format("com.databricks.spark.csv").
        option("header", "true").
        option("inferSchema", "true").
        load("wasb:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")

    Para o HDInsight 3.6, deve utilizar o seguinte fragmento.

        val df = spark.read.format("com.databricks.spark.csv").
        option("header", "true").
        option("inferSchema", "true").
        load("wasb:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")

8. Em seguida, pode executar os fragmentos, como mostrado abaixo, para ver os dados de dataframe que criou no passo anterior.
   
        df.show()
   
        df.select("Time").count()

## <a name="seealso"></a>Ver também
* [Descrição geral: Apache Spark no Azure HDInsight](apache-spark-overview.md)

### <a name="scenarios"></a>Cenários
* [Spark com BI: Efetuar uma análise de dados interativa com o Spark no HDInsight com ferramentas do BI](apache-spark-use-bi-tools.md)
* [Spark com Machine Learning: Utilizar o Spark no HDInsight para analisar a temperatura do edifício com dados de AVAC](apache-spark-ipython-notebook-machine-learning.md)
* [Spark com Machine Learning: Utilizar o Spark no HDInsight para prever resultados de inspeções alimentares](apache-spark-machine-learning-mllib-ipython.md)
* [Transmissão em Fluxo do Spark: Utilizar o Spark no HDInsight para criar aplicações de transmissão em fluxo em tempo real](apache-spark-eventhub-streaming.md)
* [Análise de registos de sites com o Spark no HDInsight](apache-spark-custom-library-website-log-analysis.md)

### <a name="create-and-run-applications"></a>Criar e executar aplicações
* [Criar uma aplicação autónoma com o Scala](apache-spark-create-standalone-application.md)
* [Executar tarefas remotamente num cluster do Spark com o Livy](apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>Ferramentas e extensões

* [Utilizar pacotes de python externos com blocos de notas do Jupyter nos clusters do Apache Spark no HDInsight Linux](apache-spark-python-package-installation.md)
* [Utilizar o Plug-in das Ferramentas do HDInsight para o IntelliJ IDEA para criar e submeter aplicações do Spark Scala](apache-spark-intellij-tool-plugin.md)
* [Utilizar o Plug-in das Ferramentas do HDInsight para o IntelliJ IDEA para depurar aplicações do Spark remotamente](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Utilizar blocos de notas do Zeppelin com um cluster do Spark no HDInsight](apache-spark-zeppelin-notebook.md)
* [Kernels disponíveis para o bloco de notas do Jupyter no cluster do Spark para o HDInsight](apache-spark-jupyter-notebook-kernels.md)
* [Instalar o Jupyter no computador e ligar a um cluster do Spark do HDInsight](apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>Gerir recursos
* [Gerir recursos para o cluster do Apache Spark no Azure HDInsight](apache-spark-resource-manager.md)
* [Controlar e depurar tarefas em execução num cluster do Apache Spark do HDInsight](apache-spark-job-debugging.md)

