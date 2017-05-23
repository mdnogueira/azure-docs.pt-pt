---
title: "Introdução - Apache Spark e consultas interativas Spark SQL - Azure HDInsight | Microsoft Docs"
description: "Guia de introdução do HDInsight Spark sobre como criar um cluster do Apache Spark no HDInsight e utilizar blocos de notas Jupyter para executar consultas interativas."
keywords: spark quickstart,interactive spark,interactive query,hdinsight spark,azure spark
services: hdinsight
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 91f41e6a-d463-4eb4-83ef-7bbb1f4556cc
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 05/12/2017
ms.author: nitinme
ms.translationtype: Human Translation
ms.sourcegitcommit: fc4172b27b93a49c613eb915252895e845b96892
ms.openlocfilehash: 0c59792423bfe2848ab5773746db466890228ddc
ms.contentlocale: pt-pt
ms.lasthandoff: 05/12/2017


---
# <a name="get-started-create-an-apache-spark-cluster-in-hdinsight-and-run-interactive-spark-sql-queries"></a>Introdução: Criar um cluster do Apache Spark no HDInsight e executar consultas interativas do Spark SQL

Saiba como criar um cluster do [Apache Spark](hdinsight-apache-spark-overview.md) no HDInsight e utilizar o bloco de notas [Jupyter](https://jupyter.org) para executar consultas interativas do Spark SQL

   ![Diagrama de início rápido que descreve os passos para criar um cluster do Apache Spark no Azure HDInsight](./media/hdinsight-apache-spark-jupyter-spark-sql/hdinsight-spark-quickstart-interactive-spark-query-flow.png "Guia de introdução do Spark ao utilizar o Apache Spark no HDInsight. Passos ilustrados: criar um cluster; executar consulta interativa do Spark")

## <a name="prerequisites"></a>Pré-requisitos

* **Uma subscrição do Azure**. Antes de começar este tutorial, tem de ter uma subscrição do Azure. Veja o artigo [Crie hoje a sua conta do Azure gratuita](https://azure.microsoft.com/free).

## <a name="create-hdinsight-spark-cluster"></a>Criar o cluster do HDInsight Spark

Nesta secção, vai criar um cluster do HDInsight Spark com um [modelo do Azure Resource Manager](https://azure.microsoft.com/resources/templates/101-hdinsight-spark-linux/). Para obter outros métodos de criação de clusters, consulte [Criar clusters do HDInsight](hdinsight-hadoop-provision-linux-clusters.md).

1. Clique na imagem seguinte para abrir o modelo no portal do Azure.         

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-hdinsight-spark-linux%2Fazuredeploy.json" target="_blank"><img src="./media/hdinsight-apache-spark-jupyter-spark-sql/deploy-to-azure.png" alt="Deploy to Azure"></a>

2. Introduza os seguintes valores:

    ![Criar um cluster do HDInsight Spark com um modelo do Azure Resource Manager](./media/hdinsight-apache-spark-jupyter-spark-sql/create-spark-cluster-in-hdinsight-using-azure-resource-manager-template.png "Criar um cluster do Spark no HDInsight com um modelo do Azure Resource Manager")

    * **Subscrição**: Selecione a sua subscrição do Azure para este cluster.
    * **Grupo de recursos**: crie um grupo de recursos ou selecione um grupo existente. O grupo de recursos é utilizado para gerir recursos do Azure para os seus projetos.
    * **Localização**: Selecione uma localização para o grupo de recursos.  Esta localização também é utilizada para o armazenamento de cluster predefinido e o cluster do HDInsight.
    * **ClusterName**: introduza um nome para o cluster do Hadoop que quer criar.
    * **Versão do Spark**: selecione a versão do Spark que quer instalar no cluster.
    * **Nome e palavra-passe de início de sessão do cluster**: o nome de início de sessão predefinido é admin.
    * **Nome de utilizador e palavra-passe do SSH**.

   Anote estes valores.  Vai precisar deles mais tarde no tutorial.

3. Selecione **Concordo com os termos e condições indicados acima**, selecione **Afixar ao dashboard** e, em seguida, clique em **Comprar**. Verá um novo mosaico intitulado Submeter a implementação para a implementação do Modelo. A criação do cluster demora cerca de 20 minutos.

> [!NOTE]
> Este artigo cria um cluster do Spark que utiliza [Blobs de Armazenamento do Azure como armazenamento de cluster](hdinsight-hadoop-use-blob-storage.md). Também pode criar um cluster do Spark que utiliza o [Azure Data Lake Store](../data-lake-store/data-lake-store-overview.md) como armazenamento adicional, além dos Blobs de Armazenamento do Azure como armazenamento predefinido. Para obter instruções, consulte [Criar um cluster de HDInsight com o Arquivo Data Lake](../data-lake-store/data-lake-store-hdinsight-hadoop-use-portal.md).
>
>

## <a name="run-an-interactive-spark-sql-query"></a>Executar uma consulta interativa do Spark SQL

Nesta secção, vai utilizar o bloco de notas Jupyter para executar consultas interativas do Spark SQL no cluster do Spark que criou anteriormente. Os clusters do HDInsight Spark fornecem três kernels que pode utilizar com o bloco de notas do Jupyter. Nomeadamente:

* **PySpark** (para aplicações escritas no Python)
* **PySpark3** (para aplicações escritas em Python3)
* **Spark** (para aplicações escritas no Scala)

Neste artigo, vai utilizar o kernel do **PySpark** no bloco de notas a partir do qual está a executar a consulta interativa Spark SQL. Para obter mais informações sobre os kernels, veja [Utilizar kernels de blocos de notas do Jupyter com clusters do Apache Spark no HDInsight](hdinsight-apache-spark-jupyter-notebook-kernels.md). Algumas das principais vantagens de utilizar o kernel do PySpark são as seguintes:

* Os contextos do Spark e do Hive são definidos automaticamente.
* Utilize a magia de células, como `%%sql`, para executar diretamente as consultas interativas do SQL ou do Hive, sem quaisquer fragmentos de código anteriores.
* O resultado das consultas interativas é visualizado automaticamente.

### <a name="create-jupyter-notebook-with-pyspark-kernel"></a>Criar um bloco de notas do Jupyter com o kernel do PySpark

1. Abra o [Portal do Azure](https://portal.azure.com/).

2. Se optou por afixar o cluster ao dashboard, clique no mosaico do cluster a partir do dashboard para iniciar o painel do cluster.

    Se não afixou o cluster ao dashboard, no painel da esquerda, clique em **Clusters do HDInsight** e, em seguida, clique no cluster que criou.

3. A partir de **Ligações rápidas**, clique em **Dashboards de clusters** e, em seguida, clique em **Bloco de Notas do Jupyter**. Se lhe for solicitado, introduza as credenciais de administrador do cluster.

   ![Abra o bloco de notas Jupyter para executar a consulta interativa do Sparl SQL](./media/hdinsight-apache-spark-jupyter-spark-sql/hdinsight-spark-open-jupyter-interactive-spark-sql-query.png "Open Jupyter notebook to run interactive Spark SQL query")

   > [!NOTE]
   > Também pode abrir o URL seguinte no seu browser para aceder ao bloco de notas Jupyter do cluster. Substitua **CLUSTERNAME** pelo nome do cluster:
   >
   > `https://CLUSTERNAME.azurehdinsight.net/jupyter`
   >
   >
3. Crie um bloco de notas. Clique em **Novo** e, em seguida, clique em **PySpark**.

   ![Crie um bloco de notas Jupyter para executar a consulta interativa do Spark SQL](./media/hdinsight-apache-spark-jupyter-spark-sql/hdinsight-spark-create-jupyter-interactive-Spark-SQL-query.png "Create a Jupyter notebook to run interactive Spark SQL query")

   É criado e aberto um novo bloco de notas com o nome Untitled (Untitled.pynb).

4. Clique no nome do bloco de notas na parte superior e introduza um nome amigável se assim o desejar.

    ![Indique um nome para o bloco de notas Jupyter para executar uma consulta interativa do Spark](./media/hdinsight-apache-spark-jupyter-spark-sql/hdinsight-spark-jupyter-notebook-name.png "Provide a name for the Jupter notebook to run interactive Spark query from")

5. Cole o seguinte código numa célula vazia e, em seguida, prima **SHIFT + ENTER** para o executar. O código importa os tipos necessários para este cenário:

        from pyspark.sql.types import *

    Uma vez que criou um bloco de notas com o kernel do PySpark, não é necessário criar quaisquer contextos explicitamente. Os contextos do Spark e do Hive são criados automaticamente quando executa a primeira célula do código.

    ![Estado da consulta interativa do Spark SQL](./media/hdinsight-apache-spark-jupyter-spark-sql/hdinsight-spark-interactive-spark-query-status.png "Status of interactive Spark SQL query")

    Sempre que executar uma consulta interativa no Jupyter, o título da janela do browser apresenta o estado **(Ocupado)**, juntamente com o nome do bloco de notas. Também vê um círculo sólido junto ao texto do **PySpark** no canto superior direito. Após a conclusão da tarefa, este é alterado para um círculo vazio.

6. Registe um conjunto de dados de exemplo como uma tabela temporária (**hvac**) ao executar o código seguinte.

        # Load the data
        hvacText = sc.textFile("wasbs:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")

        # Create the schema
        hvacSchema = StructType([StructField("date", StringType(), False),StructField("time", StringType(), False),StructField("targettemp", IntegerType(), False),StructField("actualtemp", IntegerType(), False),StructField("buildingID", StringType(), False)])

        # Parse the data in hvacText
        hvac = hvacText.map(lambda s: s.split(",")).filter(lambda s: s[0] != "Date").map(lambda s:(str(s[0]), str(s[1]), int(s[2]), int(s[3]), str(s[6]) ))

        # Create a data frame
        hvacdf = sqlContext.createDataFrame(hvac,hvacSchema)

        # Register the data frame as a table to run queries against
        hvacdf.registerTempTable("hvac")

    Os clusters do Spark no HDInsight incluem um ficheiro de dados de exemplo, **hvac.csv**, em **\HdiSamples\HdiSamples\SensorSampleData\hvac**.

7. Para executar a consulta interativa nos dados, utilize o seguinte código.

        %%sql
        SELECT buildingID, (targettemp - actualtemp) AS temp_diff, date FROM hvac WHERE date = \"6/1/13\"

   Uma vez que está a utilizar um kernel do PySpark, pode agora executar diretamente uma consulta interativa SQL na tabela temporária **hvac** que criou com a magia `%%sql`. Para obter mais informações sobre a magia `%%sql`, bem como sobre outras magias disponíveis com o kernel do PySpark, veja [Kernels disponíveis nos blocos de notas do Jupyter com clusters do Spark HDInsight](hdinsight-apache-spark-jupyter-notebook-kernels.md#parameters-supported-with-the-sql-magic).

   O resultado da tabela seguinte é apresentado por predefinição.

     ![Saída da tabela do resultado da consulta interativa do Spark](./media/hdinsight-apache-spark-jupyter-spark-sql/hdinsight-interactive-spark-query-result.png "Table output of interactive Spark query result")

    Também pode ver os resultados noutras visualizações. Por exemplo, um gráfico de área para o mesmo resultado teria o seguinte aspeto.

    ![Gráfico de área do resultado da consulta interativa do Spark](./media/hdinsight-apache-spark-jupyter-spark-sql/hdinsight-interactive-spark-query-result-area-chart.png "Area graph of interactive Spark query result")

9. Encerre o bloco de notas para libertar os recursos do cluster depois de terminar a execução da aplicação. Para o fazer, no menu **Ficheiro** do bloco de notas, clique em **Fechar e Parar**.

## <a name="delete-the-cluster"></a>Eliminar o cluster
[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="troubleshoot-access-control"></a>Resolver problemas de controlo de acesso

Caso se depare com um problema com a criação de clusters do HDInsight, veja [aceder aos requisitos de controlo](hdinsight-administer-use-portal-linux.md#create-clusters).

## <a name="see-also"></a>Consultar também
* [Descrição geral: Apache Spark no Azure HDInsight](hdinsight-apache-spark-overview.md)

### <a name="scenarios"></a>Cenários
* [Spark com BI: Efetuar uma análise de dados interativa com o Spark no HDInsight com ferramentas do BI](hdinsight-apache-spark-use-bi-tools.md)
* [Spark com Machine Learning: Utilizar o Spark no HDInsight para analisar a temperatura do edifício com dados de AVAC](hdinsight-apache-spark-ipython-notebook-machine-learning.md)
* [Spark com Machine Learning: Utilizar o Spark no HDInsight para prever resultados de inspeções alimentares](hdinsight-apache-spark-machine-learning-mllib-ipython.md)
* [Transmissão em Fluxo do Spark: Utilizar o Spark no HDInsight para criar aplicações de transmissão em fluxo em tempo real](hdinsight-apache-spark-eventhub-streaming.md)
* [Análise de registos de sites com o Spark no HDInsight](hdinsight-apache-spark-custom-library-website-log-analysis.md)
* [Análise de dados telemétricos do Application Insight com o Spark no HDInsight](hdinsight-spark-analyze-application-insight-logs.md)

### <a name="create-and-run-applications"></a>Criar e executar aplicações
* [Criar uma aplicação autónoma com o Scala](hdinsight-apache-spark-create-standalone-application.md)
* [Executar tarefas remotamente num cluster do Spark com o Livy](hdinsight-apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>Ferramentas e extensões
* [Utilizar o Plug-in das Ferramentas do HDInsight para o IntelliJ IDEA para criar e submeter aplicações do Spark Scala](hdinsight-apache-spark-intellij-tool-plugin.md)
* [Utilizar o Plug-in das Ferramentas do HDInsight para o IntelliJ IDEA para depurar aplicações do Spark remotamente](hdinsight-apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Utilizar blocos de notas do Zeppelin com um cluster do Spark no HDInsight](hdinsight-apache-spark-use-zeppelin-notebook.md)
* [Kernels disponíveis para o bloco de notas do Jupyter no cluster do Spark para o HDInsight](hdinsight-apache-spark-jupyter-notebook-kernels.md)
* [Utilizar pacotes externos com blocos de notas do Jupyter](hdinsight-apache-spark-jupyter-notebook-use-external-packages.md)
* [Instalar o Jupyter no computador e ligar a um cluster do Spark do HDInsight](hdinsight-apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>Gerir recursos
* [Gerir recursos para o cluster do Apache Spark no Azure HDInsight](hdinsight-apache-spark-resource-manager.md)
* [Controlar e depurar tarefas em execução num cluster do Apache Spark do HDInsight](hdinsight-apache-spark-job-debugging.md)

[hdinsight-versions]: hdinsight-component-versioning.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-storage]: hdinsight-hadoop-use-blob-storage.md

[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[azure-management-portal]: https://manage.windowsazure.com/
[azure-create-storageaccount]: storage-create-storage-account.md

