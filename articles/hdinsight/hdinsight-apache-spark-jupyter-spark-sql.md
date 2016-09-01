<properties
    pageTitle="Criar um cluster do Spark no HDInsight Linux e utilizar o Spark SQL a partir do Jupyter para análises interativas | Microsoft Azure"
    description="Instruções passo-a-passo sobre como criar rapidamente um cluster do Apache Spark no HDInsight e, em seguida, utilizar o Spark SQL a partir de blocos de notas do Jupyter para executar consultas interativas."
    services="hdinsight"
    documentationCenter=""
    authors="nitinme"
    manager="paulettm"
    editor="cgronlun"
    tags="azure-portal"/>

<tags
    ms.service="hdinsight"
    ms.workload="big-data"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="07/25/2016"
    ms.author="nitinme"/>


# Introdução: Criar o cluster do Apache Spark no HDInsight Linux e executar consultas interativas com o Spark SQL

Saiba como criar um cluster do Apache Spark no HDInsight e, em seguida, utilizar o bloco de notas do [Jupyter](https://jupyter.org) para executar consultas interativas do Spark SQL no cluster do Spark.

   ![Introdução à utilização do Apache Spark no HDInsight](./media/hdinsight-apache-spark-jupyter-spark-sql/hdispark.getstartedflow.png  "Get started using Apache Spark in HDInsight tutorial. Steps illustrated: create a storage account; create a cluster; run Spark SQL statements")

[AZURE.INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

**Pré-requisitos:**

- **Uma subscrição do Azure**. Antes de começar este tutorial, tem de ter uma subscrição do Azure. Consulte [Obter uma avaliação gratuita do Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

- **Um cliente Secure Shell (SSH)**: Os sistemas Linux, Unix e OS X fornecem um cliente SSH através do comando `ssh`. Para os sistemas Windows, recomendamos o [PuTTY](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html).
    
- **Chaves Secure Shell (SSH) (opcionais)**: Pode proteger a conta do SSH utilizada para a ligação ao cluster com uma palavra-passe ou uma chave pública. A utilização de uma palavra-passe permite-lhe começar rapidamente, sendo que esta deve ser a sua opção se pretender criar rapidamente um cluster e executar algumas tarefas de teste. A utilização de uma chave é mais segura, contudo, requer uma configuração adicional. Poderá utilizar esta abordagem ao criar um cluster de produção. Neste artigo, utilizamos a abordagem com a palavra-passe. Para obter instruções sobre como criar e utilizar chaves SSH com o HDInsight, consulte os artigos seguintes:

    -  Num computador com Linux – [Utilizar o SSH com o HDInsight (Hadoop) baseado em Linux a partir do Linux, Unix ou OS X](hdinsight-hadoop-linux-use-ssh-unix.md).
    
    -  Num computador com Windows – [Utilizar o SSH com o HDInsight (Hadoop) baseado em Linux a partir do Windows](hdinsight-hadoop-linux-use-ssh-windows.md).

>[AZURE.NOTE] Este artigo utiliza um modelo ARM para criar um cluster Spark que utiliza [Blobs de armazenamento do Azure como armazenamento de cluster](hdinsight-hadoop-use-blob-storage.md). Também pode criar um cluster Spark que utiliza [Arquivo azure Data Lake](../data-lake-store/data-lake-store-overview.md) como um armazenamento adicional, além de Blobs de armazenamento do Azure como armazenamento de predefinido. Para obter instruções, consulte [Criar um cluster de HDInsight com o Arquivo Data Lake](../data-lake-store/data-lake-store-hdinsight-hadoop-use-portal.md).


## Criar um cluster do Spark

Nesta secção, criará um cluster do HDInsight versão 3.4 (versão 1.6.1 do Spark) utilizando um modelo ARM do Azure. Para obter informações sobre as versões do HDInsight e os respetivos SLAs, consulte [Controlo de versões de componentes do HDInsight](hdinsight-component-versioning.md). Para obter outros métodos de criação de clusters, consulte [Criar clusters do HDInsight](hdinsight-hadoop-provision-linux-clusters.md).

1. Clique na imagem seguinte para abrir um modelo ARM no Portal do Azure.         

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fhditutorialdata.blob.core.windows.net%2Farmtemplates%2Fcreate-linux-based-spark-cluster-in-hdinsight.json" target="_blank"><img src="https://acom.azurecomcdn.net/80C57D/cdn/mediahandler/docarticles/dpsmedia-prod/azure.microsoft.com/en-us/documentation/articles/hdinsight-hbase-tutorial-get-started-linux/20160201111850/deploy-to-azure.png" alt="Deploy to Azure"></a>
    
    O modelo ARM está localizado num contentor de blobs público, *https://hditutorialdata.blob.core.windows.net/armtemplates/create-linux-based-spark-cluster-in-hdinsight.json*. 
   
2. No painel Parâmetros, introduza o seguinte:

    - **ClusterName**: introduza um nome para o cluster do Hadoop que irá criar.
    - **Nome e palavra-passe de início de sessão do cluster**: o nome de início de sessão predefinido é admin.
    - **Nome de utilizador e palavra-passe do SSH**.
    
    Anote estes valores.  Serão necessários mais tarde no tutorial.

    > [AZURE.NOTE] O SSH é utilizado para aceder remotamente ao cluster do HDInsight com uma linha de comandos. O nome de utilizador e a palavra-passe que utilizar aqui são utilizados aquando da ligação ao cluster através de SSH. Além disso, o nome de utilizador do SSH tem de ser exclusivo, pois cria uma conta de utilizador em todos os nós do cluster do HDInsight. Seguem-se alguns dos nomes de conta reservados para utilização pelos serviços do cluster e que não podem ser utilizados como nomes de utilizador do SSH:
    >
    > root, hdiuser, storm, hbase, ubuntu, zookeeper, hdfs, yarn, mapred, hbase, hive, oozie, falcon, sqoop, admin, tez, hcat, hdinsight-zookeeper.

    > Para obter mais informações sobre a utilização do SSH com o HDInsight, consulte um dos seguintes artigos:

    > * [Utilizar o SSH com o Hadoop baseado em Linux no HDInsight a partir do Linux, Unix ou OS X](hdinsight-hadoop-linux-use-ssh-unix.md)
    > * [Utilizar o SSH com o Hadoop baseado em Linux no HDInsight a partir do Windows](hdinsight-hadoop-linux-use-ssh-windows.md)

    
3.Clique em **OK** para guardar os parâmetros.

4.No painel **Implementação personalizada**, clique na caixa pendente **Grupo de recursos** e, em seguida, clique em **Novo** para criar um novo grupo de recursos. O grupo de recursos é um contentor que agrupa o cluster, a conta do Storage dependente e outros recursos associados.

5.Clique em **Termos legais** e em **Criar**.

6.Clique em **Criar**. Verá um novo mosaico intitulado Submeter a implementação para a implementação do Modelo. A criação do cluster e da SQL Database demora cerca de 20 minutos.



## Executar consultas do Spark SQL com um bloco de notas do Jupyter

Nesta secção, utilizará o bloco de notas do Jupyter para executar consultas do Spark SQL face ao cluster do Spark. Os clusters do HDInsight Spark fornecem dois kernels que pode utilizar com o bloco de notas do Jupyter. Nomeadamente:

* **PySpark** (para aplicações escritas no Python)
* **Spark** (para aplicações escritas no Scala)

Neste artigo, utilizará o kernel do PySpark. No artigo [Kernels disponíveis nos blocos de notas do Jupyter com clusters do Spark HDInsight](hdinsight-apache-spark-jupyter-notebook-kernels.md#why-should-i-use-the-new-kernels), pode obter detalhes acerca das vantagens da utilização do kernel do PySpark. No entanto, algumas das principais vantagens de utilizar o kernel do PySpark são as seguintes:

* Não é necessário definir os contextos para o Spark e o Hive. Estes são definidos automaticamente.
* Pode utilizar a magia de células, como `%%sql`, para executar diretamente as suas consultas de SQL ou Hive, sem quaisquer fragmentos de código anteriores.
* O resultado das consultas de SQL ou Hive é visualizado automaticamente.

### Criar um bloco de notas do Jupyter com o kernel do PySpark 

1. No [Portal do Azure](https://portal.azure.com/), no startboard, clique no mosaico relativo ao cluster do Spark (se o tiver afixado ao startboard). Também pode navegar até ao cluster em **Procurar Tudo** > **Clusters do HDInsight**.   

2. No painel do cluster do Spark, clique em **Ligações Rápidas** e, em seguida, no painel **Dashboard de Clusters**, clique em **Bloco de Notas do Jupyter**. Se lhe for solicitado, introduza as credenciais de administrador do cluster.

    > [AZURE.NOTE] Também pode aceder ao Bloco de Notas do Jupyter para o cluster abrindo o seguinte URL no seu browser. Substitua __CLUSTERNAME__ pelo nome do cluster:
    >
    > `https://CLUSTERNAME.azurehdinsight.net/jupyter`

2. Crie um novo bloco de notas. Clique em **Novo** e, em seguida, clique em **PySpark**.

    ![Criar um novo bloco de notas do Jupyter](./media/hdinsight-apache-spark-jupyter-spark-sql/hdispark.note.jupyter.createnotebook.png "Create a new Jupyter notebook")

3. É criado e aberto um novo bloco de notas com o nome Untitled.pynb. Clique no nome do bloco de notas na parte superior e introduza um nome amigável.

    ![Fornecer um nome para o bloco de notas](./media/hdinsight-apache-spark-jupyter-spark-sql/hdispark.note.jupyter.notebook.name.png "Provide a name for the notebook")

4. Uma vez que criou um bloco de notas com o kernel do PySpark, não é necessário criar quaisquer contextos explicitamente. Os contextos do Spark e do Hive serão criados automaticamente quando executa a primeira célula do código. Pode começar por importar os tipos necessários para este cenário. Para o fazer, cole o seguinte fragmento de código numa célula e prima **SHIFT + ENTER**.

        from pyspark.sql.types import *
        
    Sempre que executar uma tarefa no Jupyter, o título da janela do browser apresentará um estado **(Ocupado)** juntamente com o título do bloco de notas. Também verá um círculo sólido junto ao texto do **PySpark** no canto superior direito. Após a conclusão da tarefa, este é alterado para um círculo vazio.

     ![Estado de uma tarefa do bloco de notas do Jupyter](./media/hdinsight-apache-spark-jupyter-spark-sql/hdispark.jupyter.job.status.png "Status of a Jupyter notebook job")

4. Carregue dados de exemplo para uma tabela temporária. Quando cria um cluster do Spark no HDInsight, o ficheiro de dados de exemplo, **hvac.csv**, é copiado para a conta do Storage associada em **\HdiSamples\HdiSamples\SensorSampleData\hvac**.

    Numa célula vazia, cole o seguinte exemplo de código e prima **SHIFT + ENTER**. Este exemplo de código regista os dados numa tabela temporária denominada **hvac**.

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

5. Uma vez que está a utilizar um kernel do PySpark, agora, pode executar diretamente uma consulta de SQL na tabela temporária **hvac** que acabou de criar utilizando a magia `%%sql`. Para obter mais informações sobre a magia `%%sql`, bem como sobre outras magias disponíveis com o kernel do PySpark, consulte [Kernels disponíveis nos blocos de notas do Jupyter com clusters do Spark HDInsight](hdinsight-apache-spark-jupyter-notebook-kernels.md#why-should-i-use-the-new-kernels).
        
        %%sql
        SELECT buildingID, (targettemp - actualtemp) AS temp_diff, date FROM hvac WHERE date = \"6/1/13\"

5. Assim que a tarefa for concluída com êxito, é apresentado, por predefinição, o seguinte resultado da tabela.

    ![Saída do tabela do resultado da consulta](./media/hdinsight-apache-spark-jupyter-spark-sql/tabular.output.png "Table output of query result")

    Também pode ver os resultados noutras visualizações. Por exemplo, um gráfico de área para o mesmo resultado teria o seguinte aspeto.

    ![Gráfico de área do resultado da consulta](./media/hdinsight-apache-spark-jupyter-spark-sql/area.output.png "Area graph of query result")


6. Depois de terminar a execução da aplicação, deve encerrar o bloco de notas para libertar os recursos. Para o fazer, no menu **Ficheiro** do bloco de notas, clique em **Fechar e Parar**. Deste modo, o bloco de notas é encerrado e fechado.

##Eliminar o cluster

[AZURE.INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]


## Consultar também


* [Descrição geral: Apache Spark no Azure HDInsight](hdinsight-apache-spark-overview.md)

### Cenários

* [Spark com BI: Efetuar uma análise de dados interativa com o Spark no HDInsight com ferramentas de BI](hdinsight-apache-spark-use-bi-tools.md)

* [Spark com Machine Learning: Utilizar o Spark no HDInsight para analisar a temperatura de construção com dados de AVAC](hdinsight-apache-spark-ipython-notebook-machine-learning.md)

* [Spark com Machine Learning: Utilizar o Spark no HDInsight para prever resultados de inspeções alimentares](hdinsight-apache-spark-machine-learning-mllib-ipython.md)

* [Transmissão em Fluxo do Spark: Utilizar o Spark no HDInsight para a criação de aplicações de transmissão em fluxo em tempo real](hdinsight-apache-spark-eventhub-streaming.md)

* [Análise de registos de Web Sites com o Spark no HDInsight](hdinsight-apache-spark-custom-library-website-log-analysis.md)

* [Análise de dados de telemetria do Application Insight com o Spark no HDInsight](hdinsight-spark-analyze-application-insight-logs.md)

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


[hdinsight-versions]: hdinsight-component-versioning.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-storage]: hdinsight-hadoop-use-blob-storage.md

[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[azure-management-portal]: https://manage.windowsazure.com/
[azure-create-storageaccount]: storage-create-storage-account.md



<!--HONumber=ago16_HO4-->


