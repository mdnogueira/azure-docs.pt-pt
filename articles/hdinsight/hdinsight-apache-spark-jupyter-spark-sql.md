---
title: "Introdução ao cluster do Apache Spark no Azure HDInsight | Microsoft Docs"
description: "Instruções passo-a-passo sobre como criar rapidamente um cluster do Apache Spark no HDInsight e, em seguida, utilizar o Spark SQL a partir de blocos de notas do Jupyter para executar consultas interativas."
services: hdinsight
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 91f41e6a-d463-4eb4-83ef-7bbb1f4556cc
ms.service: hdinsight
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 02/22/2017
ms.author: nitinme
translationtype: Human Translation
ms.sourcegitcommit: a3bdeb6fea306babc9358134c37044843b9bdd1c
ms.openlocfilehash: d8d9c5111a19bb165c25d2796d6b6e933d75042a


---
# <a name="get-started-create-apache-spark-cluster-in-azure-hdinsight-and-run-interactive-queries-using-spark-sql"></a>Introdução: Criar um cluster do Apache Spark no Azure HDInsight e executar consultas interativas com o Spark SQL

Saiba como criar um cluster do [Apache Spark](hdinsight-apache-spark-overview.md) no HDInsight e, em seguida, utilize o bloco de notas do [Jupyter](https://jupyter.org) para executar consultas interativas do Spark SQL no cluster do Spark.

   ![Introdução à utilização do Apache Spark no HDInsight](./media/hdinsight-apache-spark-jupyter-spark-sql/hdispark.getstartedflow.png "Introdução à utilização do Apache Spark no HDInsight. Passos ilustrados: criar uma conta de armazenamento; criar um cluster; executar declarações do Spark SQL")

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="prerequisites"></a>Pré-requisitos
* **Uma subscrição do Azure**. Antes de começar este tutorial, tem de ter uma subscrição do Azure. Veja o artigo [Crie hoje a sua conta do Azure gratuita](https://azure.microsoft.com/free).

* **Um cliente Secure Shell (SSH)**: Os sistemas Linux, Unix e OS X fornecem um cliente SSH através do comando `ssh`. Para os clientes Windows, veja [Utilizar o SSH com Hadoop no HDInsight a partir do Windows com PuTTY](hdinsight-hadoop-linux-use-ssh-windows.md). Para o Linux, Unix ou OS X, veja [Utilizar o SSH com Hadoop no HDInsight a partir do Linux, Unix ou OS X](hdinsight-hadoop-linux-use-ssh-unix.md)

> [!NOTE]
> Este artigo utiliza um modelo do Azure Resource Manager para criar um cluster do Spark que utiliza os [Azure Storage Blobs como o armazenamento do cluster](hdinsight-hadoop-use-blob-storage.md). Também pode criar um cluster Spark que utiliza [Arquivo azure Data Lake](../data-lake-store/data-lake-store-overview.md) como um armazenamento adicional, além de Blobs de armazenamento do Azure como armazenamento de predefinido. Para obter instruções, consulte [Criar um cluster de HDInsight com o Arquivo Data Lake](../data-lake-store/data-lake-store-hdinsight-hadoop-use-portal.md).
>
>

### <a name="access-control-requirements"></a>Requisitos do controlo de acesso
[!INCLUDE [access-control](../../includes/hdinsight-access-control-requirements.md)]

## <a name="create-spark-cluster"></a>Criar um cluster do Spark
Nesta secção, vai criar um cluster do Spark no HDInsight com um [modelo do Azure Resource Manager](https://azure.microsoft.com/resources/templates/101-hdinsight-spark-linux/). Para obter informações sobre as versões do HDInsight e os respetivos SLAs, consulte [Controlo de versões de componentes do HDInsight](hdinsight-component-versioning.md). Para obter outros métodos de criação de clusters, consulte [Criar clusters do HDInsight](hdinsight-hadoop-provision-linux-clusters.md).

1. Clique na imagem seguinte para abrir o modelo no portal do Azure.         

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-hdinsight-spark-linux%2Fazuredeploy.json" target="_blank"><img src="./media/hdinsight-apache-spark-jupyter-spark-sql/deploy-to-azure.png" alt="Deploy to Azure"></a>

2. Introduza os seguintes valores:

    ![Criar um cluster do Spark no HDInsight com um modelo do Azure Resource Manager](./media/hdinsight-apache-spark-jupyter-spark-sql/create-spark-cluster-in-hdinsight-using-azure-resource-manager-template.png "Criar um cluster do Spark no HDInsight com um modelo do Azure Resource Manager")

   * **Subscrição**: Selecione a sua subscrição do Azure para este cluster.
   * **Grupo de recursos**: Crie um novo grupo de recursos ou selecione um grupo existente. O grupo de recursos é utilizado para gerir recursos do Azure para os seus projetos.
   * **Localização**: Selecione uma localização para o grupo de recursos.  Esta localização também é utilizada para o armazenamento de cluster predefinido e o cluster do HDInsight.
   * **ClusterName**: introduza um nome para o cluster do Hadoop que irá criar.
   * **Nome e palavra-passe de início de sessão do cluster**: o nome de início de sessão predefinido é admin.
   * **Nome de utilizador e palavra-passe do SSH**.

   Anote estes valores.  Serão necessários mais tarde no tutorial.

3. Selecione **Concordo com os termos e condições indicados acima**, selecione **Afixar ao dashboard** e, em seguida, clique em **Comprar**. Verá um novo mosaico intitulado Submeter a implementação para a implementação do Modelo. A criação de um cluster demora cerca de 20 minutos.

## <a name="run-spark-sql-queries-using-a-jupyter-notebook"></a>Executar consultas do Spark SQL com um bloco de notas do Jupyter
Nesta secção, utilizará o bloco de notas do Jupyter para executar consultas do Spark SQL face ao cluster do Spark. Os clusters do HDInsight Spark fornecem dois kernels que pode utilizar com o bloco de notas do Jupyter. Nomeadamente:

* **PySpark** (para aplicações escritas no Python)
* **Spark** (para aplicações escritas no Scala)

Neste artigo, utiliza o kernel do PySpark. Para mais informações sobre os dois kernels, consulte [Use Jupyter notebooks kernels with Apache Spark clusters in HDInsight (Utilizar kernels de blocos de notas Jupyter com clusters do Apache Spark no HDInsight)](hdinsight-apache-spark-jupyter-notebook-kernels.md). Algumas das principais vantagens de utilizar o kernel do PySpark são as seguintes:

* Os contextos do Spark e do Hive são definidos automaticamente.
* Utilize a magia de células, como `%%sql`, para executar diretamente as consultas de SQL ou Hive, sem quaisquer fragmentos de código anteriores.
* O resultado das consultas de SQL ou Hive é visualizado automaticamente.

### <a name="create-jupyter-notebook-with-pyspark-kernel"></a>Criar um bloco de notas do Jupyter com o kernel do PySpark

1. Abra o [Portal do Azure](https://portal.azure.com/).
2. No menu à esquerda, clique em **Grupos de recursos**.
3. Clique no grupo de recursos que criou na última secção. Pode utilizar a função de pesquisa se existirem demasiados grupos de recursos. Pode ver dois recursos no grupo, o cluster do HDInsight e a conta de armazenamento predefinida.
4. Clique no cluster para abri-lo.

2. A partir de **Ligações rápidas**, clique em **Dashboards de clusters** e, em seguida, clique em **Bloco de Notas do Jupyter**. Se lhe for solicitado, introduza as credenciais de administrador do cluster.

   ![Dashboards de clusters do HDInsight](./media/hdinsight-apache-spark-jupyter-spark-sql/hdinsight-azure-portal-cluster-dashboards.png "Dashboards de clusters do HDInsight")

   > [!NOTE]
   > Também pode aceder ao Bloco de Notas do Jupyter para o cluster abrindo o seguinte URL no seu browser. Substitua **CLUSTERNAME** pelo nome do cluster:
   >
   > `https://CLUSTERNAME.azurehdinsight.net/jupyter`
   >
   >
3. Crie um novo bloco de notas. Clique em **Novo** e, em seguida, clique em **PySpark**.

   ![Criar um novo bloco de notas Jupyter](./media/hdinsight-apache-spark-jupyter-spark-sql/hdispark.note.jupyter.createnotebook.png "Criar um novo bloco de notas Jupyter")

   É criado e aberto um novo bloco de notas com o nome Untitled (Untitled.pynb).

4. Clique no nome do bloco de notas na parte superior e introduza um nome amigável se assim o desejar.

    ![Forneça um nome para o bloco de notas](./media/hdinsight-apache-spark-jupyter-spark-sql/hdispark.note.jupyter.notebook.name.png "Forneça um nome para o bloco de notas")
5. Cole o seguinte código numa célula vazia e, em seguida, prima **SHIFT + ENTER** para executar o código. O código importa os tipos necessários para este cenário:

        from pyspark.sql.types import *

    Uma vez que criou um bloco de notas com o kernel do PySpark, não é necessário criar quaisquer contextos explicitamente. Os contextos do Spark e do Hive serão criados automaticamente quando executa a primeira célula do código.

    ![Estado de uma tarefa de bloco de notas Jupyter](./media/hdinsight-apache-spark-jupyter-spark-sql/hdispark.jupyter.job.status.png "Estado de uma tarefa de bloco de notas Jupyter")

    Sempre que executar uma tarefa no Jupyter, o título da janela do browser apresentará um estado **(Ocupado)** juntamente com o título do bloco de notas. Também verá um círculo sólido junto ao texto do **PySpark** no canto superior direito. Após a conclusão da tarefa, este é alterado para um círculo vazio.

6. Execute o seguinte código para registar alguns dados de exemplo numa tabela temporária denominada **avac**.

        # Load the data
        hvacText = sc.textFile("wasbs:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")
        
        # Create the schema
        hvacSchema = StructType([StructField("date", StringType(), False),StructField("time", StringType(), False),StructField("targettemp", IntegerType(), False),StructField("actualtemp", IntegerType(), False),StructField("buildingID", StringType(), False)])
        
        # Parse the data in hvacText
        hvac = hvacText.map(lambda s: s.split(",")).filter(lambda s: s[0] != "Date").map(lambda s:(str(s[0]), str(s[1]), int(s[2]), int(s[3]), str(s[6]) ))
        
        # Create a data frame
        hvacdf = sqlContext.createDataFrame(hvac,hvacSchema)
        
        # Register the data fram as a table to run queries against
        hvacdf.registerTempTable("hvac")

    Os clusters do Spark no HDInsight incluem um ficheiro de dados de exemplo, **hvac.csv**, em **\HdiSamples\HdiSamples\SensorSampleData\hvac**.

7. Execute o seguinte código para consultar os dados:

        %%sql
        SELECT buildingID, (targettemp - actualtemp) AS temp_diff, date FROM hvac WHERE date = \"6/1/13\"

   Uma vez que está a utilizar um kernel do PySpark, agora, pode executar diretamente uma consulta de SQL na tabela temporária **hvac** que acabou de criar utilizando a magia `%%sql`. Para obter mais informações sobre a magia `%%sql`, bem como sobre outras magias disponíveis com o kernel do PySpark, consulte [Kernels disponíveis nos blocos de notas do Jupyter com clusters do Spark HDInsight](hdinsight-apache-spark-jupyter-notebook-kernels.md#choose-between-the-kernels).

   O resultado da tabela seguinte é apresentado por predefinição.

     ![Saída da tabela do resultado da consulta](./media/hdinsight-apache-spark-jupyter-spark-sql/tabular.output.png "Saída da tabela do resultado da consulta")

    Também pode ver os resultados noutras visualizações. Por exemplo, um gráfico de área para o mesmo resultado teria o seguinte aspeto.

    ![Gráfico de área do resultado da consulta](./media/hdinsight-apache-spark-jupyter-spark-sql/area.output.png "Gráfico de área do resultado da consulta")

9. Depois de terminar a execução da aplicação, pode encerrar o bloco de notas para libertar os recursos. Para o fazer, no menu **Ficheiro** do bloco de notas, clique em **Fechar e Parar**. Deste modo, o bloco de notas é encerrado e fechado.

## <a name="delete-the-cluster"></a>Eliminar o cluster
[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

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



<!--HONumber=Feb17_HO1-->


