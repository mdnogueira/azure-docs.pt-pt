---
title: "Criar aplicações de aprendizagem máquina Apache Spark no Azure HDInsight | Microsoft Docs"
description: "Instruções passo a passo sobre como criar aplicações de aprendizagem do Apache Spark no HDInsight Spark clusters utilizando o bloco de notas do Jupyter"
services: hdinsight
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: f584ca5e-abee-4b7c-ae58-2e45dfc56bf4
ms.service: hdinsight
ms.custom: hdinsightactive
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/28/2017
ms.author: nitinme
ms.openlocfilehash: 8d4f29cab5e548c6ba4780e08c7c2a5b0c9fcefe
ms.sourcegitcommit: f8437edf5de144b40aed00af5c52a20e35d10ba1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/03/2017
---
# <a name="build-apache-spark-machine-learning-applications-on-azure-hdinsight"></a>Criar aplicações de aprendizagem máquina Apache Spark no Azure HDInsight

Saiba como criar um Apache Spark de machine learning aplicação utilizando um cluster do Spark no HDInsight. Este artigo mostra como utilizar o bloco de notas do Jupyter disponível com o cluster para criar e testar esta aplicação. A aplicação utiliza os dados de HVAC.csv de exemplo que está disponíveis em todos os clusters por predefinição.

**Pré-requisitos:**

Tem de ter o seguinte:

* Um cluster do Apache Spark no HDInsight. Para obter instruções, consulte [clusters do Apache Spark criar no Azure HDInsight](apache-spark-jupyter-spark-sql.md). 

## <a name="data"></a>Compreender o conjunto de dados
Antes de Vamos começar a construir a aplicação, informe-nos compreenda a estrutura dos dados para o qual vamos construir a aplicação e o tipo de análise que será efetuado nos dados. 

Neste artigo, utilizamos o exemplo **HVAC.csv** ficheiro de dados que está disponível na conta do Storage do Azure que que tenha associado ao cluster do HDInsight. Dentro da conta de armazenamento, o ficheiro está em **\HdiSamples\HdiSamples\SensorSampleData\hvac**. Transfira e abra o ficheiro CSV para obter um instantâneo dos dados.  

![Instantâneo dos dados utilizados por exemplo, o Spark machine learning](./media/apache-spark-ipython-notebook-machine-learning/spark-machine-learning-understand-data.png "instantâneo dos dados utilizados por exemplo, o Spark machine learning")

Mostram os dados a temperatura de destino e a temperatura real de um edifício com sistemas de AVAC instalado. Vamos supor que o **sistema** coluna representa o ID de sistema e o **SystemAge** coluna representa o número de anos, o sistema de AVAC foi implementado em criar.

Estes dados são utilizadas para prever se um edifício vai ser hotter ou colder com base na temperatura de destino, com um ID de sistema e a idade do sistema.

## <a name="app"></a>Escrever uma aplicação de aprendizagem do Spark máquina com o Spark MLlib
Esta aplicação Utilizamos um pipeline de Spark ML para executar uma classificação de documento. No pipeline, iremos dividir o documento em palavras, converter as palavras um vetor de funcionalidade numéricos e, finalmente, criar um modelo de previsão a utilizar o vetores de funcionalidade e as etiquetas. Execute os seguintes passos para criar a aplicação.

1. No [Portal do Azure](https://portal.azure.com/), no startboard, clique no mosaico relativo ao cluster do Spark (se o tiver afixado ao startboard). Também pode navegar até ao cluster em **Procurar Tudo** > **Clusters do HDInsight**.   
2. No painel do cluster do Spark, clique em **Dashboard de Clusters** e, em seguida, clique em **Bloco de Notas do Jupyter**. Se lhe for solicitado, introduza as credenciais de administrador do cluster.
   
   > [!NOTE]
   > Também pode aceder ao Bloco de Notas do Jupyter para o cluster abrindo o seguinte URL no seu browser. Substitua **CLUSTERNAME** pelo nome do cluster:
   > 
   > `https://CLUSTERNAME.azurehdinsight.net/jupyter`
   > 
   > 
3. Crie um novo bloco de notas. Clique em **Novo** e, em seguida, clique em **PySpark**.
   
    ![Criar um bloco de notas do Jupyter para o exemplo do Spark machine learning](./media/apache-spark-ipython-notebook-machine-learning/spark-machine-learning-create-notebook.png "criar um bloco de notas do Jupyter para o exemplo do Spark machine learning")
4. É criado e aberto um novo bloco de notas com o nome Untitled.pynb. Clique no nome do bloco de notas na parte superior e introduza um nome amigável.
   
    ![Forneça um nome de bloco de notas, por exemplo, o Spark machine learning](./media/apache-spark-ipython-notebook-machine-learning/spark-machine-learning-notebook-name.png "forneça um nome de bloco de notas, por exemplo, o Spark machine learning")
5. Uma vez que criou um bloco de notas com o kernel do PySpark, não é necessário criar quaisquer contextos explicitamente. Os contextos do Spark e do Hive serão criados automaticamente quando executa a primeira célula do código. Pode começar por importar os tipos que são necessários para este cenário. Cole o seguinte fragmento numa célula vazia e, em seguida, prima **SHIFT + ENTER**. 
   
        from pyspark.ml import Pipeline
        from pyspark.ml.classification import LogisticRegression
        from pyspark.ml.feature import HashingTF, Tokenizer
        from pyspark.sql import Row
   
        import os
        import sys
        from pyspark.sql.types import *
   
        from pyspark.mllib.classification import LogisticRegressionWithSGD
        from pyspark.mllib.regression import LabeledPoint
        from numpy import array
6. Tem agora carregar os dados (hvac.csv), analisá-lo e utilizá-la para preparar o modelo. Para tal, é possível definir uma função que verifica se a temperatura real de criar é maior do que a temperatura de destino. Se for superior a temperatura real, criar é frequente, a designação pelo valor **1.0**. Se a temperatura real é menor, criar é amovíveis, a designação pelo valor **0,0**. 
   
    Cole o seguinte fragmento numa célula vazia e prima **SHIFT + ENTER**.

        # List the structure of data for better understanding. Because the data will be
        # loaded as an array, this structure makes it easy to understand what each element
        # in the array corresponds to

        # 0 Date
        # 1 Time
        # 2 TargetTemp
        # 3 ActualTemp
        # 4 System
        # 5 SystemAge
        # 6 BuildingID

        LabeledDocument = Row("BuildingID", "SystemInfo", "label")

        # Define a function that parses the raw CSV file and returns an object of type LabeledDocument

        def parseDocument(line):
            values = [str(x) for x in line.split(',')]
            if (values[3] > values[2]):
                hot = 1.0
            else:
                hot = 0.0        

            textValue = str(values[4]) + " " + str(values[5])

            return LabeledDocument((values[6]), textValue, hot)

        # Load the raw HVAC.csv file, parse it using the function
        data = sc.textFile("wasb:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")

        documents = data.filter(lambda s: "Date" not in s).map(parseDocument)
        training = documents.toDF()


1. Configurar o pipeline de aprendizagem do Spark máquina que consiste em três fases: atomizador, hashingTF e lr. Para obter mais informações sobre o que é um pipeline e como funciona consulte <a href="http://spark.apache.org/docs/latest/ml-guide.html#how-it-works" target="_blank">pipeline do Spark machine learning</a>.
   
    Cole o seguinte fragmento numa célula vazia e prima **SHIFT + ENTER**.
   
        tokenizer = Tokenizer(inputCol="SystemInfo", outputCol="words")
        hashingTF = HashingTF(inputCol=tokenizer.getOutputCol(), outputCol="features")
        lr = LogisticRegression(maxIter=10, regParam=0.01)
        pipeline = Pipeline(stages=[tokenizer, hashingTF, lr])
2. Ajustar o pipeline o documento de formação. Cole o seguinte fragmento numa célula vazia e prima **SHIFT + ENTER**.
   
        model = pipeline.fit(training)
3. Certifique-se o documento de formação para o ponto de verificação do progresso com a aplicação. Cole o seguinte fragmento numa célula vazia e prima **SHIFT + ENTER**.
   
        training.show()
   
    Isto deve dar o resultado semelhante ao seguinte:
   
        +----------+----------+-----+
        |BuildingID|SystemInfo|label|
        +----------+----------+-----+
        |         4|     13 20|  0.0|
        |        17|      3 20|  0.0|
        |        18|     17 20|  1.0|
        |        15|      2 23|  0.0|
        |         3|      16 9|  1.0|
        |         4|     13 28|  0.0|
        |         2|     12 24|  0.0|
        |        16|     20 26|  1.0|
        |         9|      16 9|  1.0|
        |        12|       6 5|  0.0|
        |        15|     10 17|  1.0|
        |         7|      2 11|  0.0|
        |        15|      14 2|  1.0|
        |         6|       3 2|  0.0|
        |        20|     19 22|  0.0|
        |         8|     19 11|  0.0|
        |         6|      15 7|  0.0|
        |        13|      12 5|  0.0|
        |         4|      8 22|  0.0|
        |         7|      17 5|  0.0|
        +----------+----------+-----+

    Retroceda e verifique se a saída contra o ficheiro raw de CSV. Por exemplo, a primeira linha do ficheiro CSV tem este tipo de dados:

    ![Saída de dados de instantâneos de exemplo do Spark machine learning](./media/apache-spark-ipython-notebook-machine-learning/spark-machine-learning-output-data.png "instantâneo dos dados de saída de exemplo do Spark machine learning")

    Repare como a temperatura real é menor do que a temperatura de destino, sugerindo que criar é amovíveis. Por conseguinte, na formação de saída, o valor para **etiqueta** na primeira linha é **0,0**, não é frequente que significa que criar.

1. Prepare um conjunto de dados para executar o modelo treinado contra. Para tal, iremos seria transmitir um ID de sistema e a idade do sistema (assinalados como **SystemInfo** na saída de formação), e o modelo seria prever se seria hotter edifício com esse ID de sistema e a idade do sistema (a designação por 1.0) ou cooler (designação por 0,0).
   
   Cole o seguinte fragmento numa célula vazia e prima **SHIFT + ENTER**.
   
       # SystemInfo here is a combination of system ID followed by system age
       Document = Row("id", "SystemInfo")
       test = sc.parallelize([(1L, "20 25"),
                     (2L, "4 15"),
                     (3L, "16 9"),
                     (4L, "9 22"),
                     (5L, "17 10"),
                     (6L, "7 22")]) \
           .map(lambda x: Document(*x)).toDF() 
2. Por fim, fazer predições nos dados de teste. Cole o seguinte fragmento numa célula vazia e prima **SHIFT + ENTER**.
   
        # Make predictions on test documents and print columns of interest
        prediction = model.transform(test)
        selected = prediction.select("SystemInfo", "prediction", "probability")
        for row in selected.collect():
            print row
3. Deverá ver um resultado semelhante ao seguinte:
   
       Row(SystemInfo=u'20 25', prediction=1.0, probability=DenseVector([0.4999, 0.5001]))
       Row(SystemInfo=u'4 15', prediction=0.0, probability=DenseVector([0.5016, 0.4984]))
       Row(SystemInfo=u'16 9', prediction=1.0, probability=DenseVector([0.4785, 0.5215]))
       Row(SystemInfo=u'9 22', prediction=1.0, probability=DenseVector([0.4549, 0.5451]))
       Row(SystemInfo=u'17 10', prediction=1.0, probability=DenseVector([0.4925, 0.5075]))
       Row(SystemInfo=u'7 22', prediction=0.0, probability=DenseVector([0.5015, 0.4985]))
   
   Da primeira linha na predição, pode ver que para um sistema de AVAC com ID 20 e duração de sistema de 25 anos, criar será acesso frequente (**predição = 1.0**). O primeiro valor para DenseVector (0.49999) corresponde da predição 0,0 e o segundo valor (0.5001) corresponde da predição 1.0. Na saída, apesar do segundo valor só é aumenta ligeiramente superior, o modelo mostra **predição = 1.0**.
4. Depois de terminar a execução da aplicação, deve encerrar o bloco de notas para libertar os recursos. Para o fazer, no menu **Ficheiro** do bloco de notas, clique em **Fechar e Parar**. Deste modo, o bloco de notas é encerrado e fechado.

## <a name="anaconda"></a>Utilizar Anaconda scikit-saiba biblioteca para o machine learning do Spark
Clusters do Apache Spark no HDInsight incluem bibliotecas Anaconda. Isto também inclui o **scikit-Saiba** biblioteca para o machine learning. A biblioteca também inclui vários conjuntos de dados que pode utilizar para criar aplicações de exemplo diretamente a partir de um bloco de notas do Jupyter. Para obter exemplos sobre como utilizar o scikit-saber biblioteca, consulte [http://scikit-learn.org/stable/auto_examples/index.html](http://scikit-learn.org/stable/auto_examples/index.html).

## <a name="seealso"></a>Ver também
* [Descrição geral: Apache Spark no Azure HDInsight](apache-spark-overview.md)

### <a name="scenarios"></a>Cenários
* [Spark com BI: Efetuar uma análise de dados interativa com o Spark no HDInsight com ferramentas do BI](apache-spark-use-bi-tools.md)
* [Spark com Machine Learning: Utilizar o Spark no HDInsight para prever resultados de inspeções alimentares](apache-spark-machine-learning-mllib-ipython.md)
* [Transmissão em Fluxo do Spark: Utilizar o Spark no HDInsight para criar aplicações de transmissão em fluxo em tempo real](apache-spark-eventhub-streaming.md)
* [Análise de registos de sites com o Spark no HDInsight](apache-spark-custom-library-website-log-analysis.md)

### <a name="create-and-run-applications"></a>Criar e executar aplicações
* [Criar uma aplicação autónoma com o Scala](apache-spark-create-standalone-application.md)
* [Executar tarefas remotamente num cluster do Spark com o Livy](apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>Ferramentas e extensões
* [Utilizar o Plug-in das Ferramentas do HDInsight para o IntelliJ IDEA para criar e submeter aplicações do Spark Scala](apache-spark-intellij-tool-plugin.md)
* [Utilizar o Plug-in das Ferramentas do HDInsight para o IntelliJ IDEA para depurar aplicações do Spark remotamente](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Utilizar blocos de notas do Zeppelin com um cluster do Spark no HDInsight](apache-spark-zeppelin-notebook.md)
* [Kernels disponíveis para o bloco de notas do Jupyter no cluster do Spark para o HDInsight](apache-spark-jupyter-notebook-kernels.md)
* [Utilizar pacotes externos com blocos de notas do Jupyter](apache-spark-jupyter-notebook-use-external-packages.md)
* [Instalar o Jupyter no computador e ligar a um cluster do Spark do HDInsight](apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>Gerir recursos
* [Gerir recursos para o cluster do Apache Spark no Azure HDInsight](apache-spark-resource-manager.md)
* [Controlar e depurar tarefas em execução num cluster do Apache Spark do HDInsight](apache-spark-job-debugging.md)

[hdinsight-versions]: hdinsight-component-versioning.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-storage]: hdinsight-hadoop-use-blob-storage.md

[hdinsight-weblogs-sample]:../hadoop/apache-hive-analyze-website-log.md
[hdinsight-sensor-data-sample]:../hadoop/apache-hive-analyze-sensor-data.md

[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[azure-create-storageaccount]:../../storage/common/storage-create-storage-account.md
