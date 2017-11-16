---
title: "Ciência de dados utilizando Scala e o Spark no Azure | Microsoft Docs"
description: "Como utilizar o Scala para tarefas de aprendizagem máquina supervisionados com os Spark dimensionáveis MLlib e Spark ML pacotes num cluster do Azure HDInsight Spark."
services: machine-learning
documentationcenter: 
author: bradsev
manager: cgronlun
editor: cgronlun
ms.assetid: a7c97153-583e-48fe-b301-365123db3780
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/13/2017
ms.author: bradsev;deguhath
ms.openlocfilehash: 940911144993f30723ad395722742c81a4b0a71c
ms.sourcegitcommit: 732e5df390dea94c363fc99b9d781e64cb75e220
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/14/2017
---
# <a name="data-science-using-scala-and-spark-on-azure"></a>Utilizar o Scala e o Spark para Ciência de Dados no Azure
Este artigo mostra como utilizar o Scala para tarefas de aprendizagem máquina supervisionados com os Spark dimensionáveis MLlib Spark ML pacotes e num cluster do Azure HDInsight Spark. Explica como as tarefas que constituem o [processo de ciência de dados](http://aka.ms/datascienceprocess): ingestão de dados e exploração, visualização, funcionalidade engenharia, modelação e consumo de modelo. Os modelos no artigo incluem regressão linear e logística da, florestas aleatórias e árvores elevada de gradação (GBTs), para além das duas supervisionado tarefas de machine learning:

* Problema de regressão: predição da quantidade de sugestão ($) para uma viagem taxi
* Classificação do binária: previsão de sugestão ou nenhuma sugestão (1/0) para uma viagem taxi

O processo de modelação requer formação e avaliação num conjunto de dados de teste e métricas de precisão relevantes. Neste artigo, pode saber como armazenar estes modelos no Blob storage do Azure e como Pontuar e avaliar o seu desempenho preditivo. Este artigo abrange também os tópicos mais avançados de como otimizar modelos através da utilização de varrimento de validação cruzada e hyper parâmetro. Os dados utilizados são uma amostra de 2013 NYC taxi viagem e fare conjunto de dados está disponível no GitHub.

[Scala](http://www.scala-lang.org/), uma linguagem baseada na máquina virtual de Java, integra conceitos de linguagem orientado para objetos e funcional. É um idioma dimensionável, que é adequado para o processamento distribuído na nuvem e é executada em clusters do Spark do Azure.

[O Spark](http://spark.apache.org/) é uma arquitetura de processamento paralelo open source que suporta o processamento dentro da memória para melhorar o desempenho de aplicações de análise de macrodados. O motor de processamento do Spark foi concebido para velocidade, facilidade de utilização e efetuar análises sofisticadas. Capacidades de cálculo distribuído dentro da memória do Spark tornam uma boa opção para algoritmos iterativos na computações de machine learning e do graph. O [spark.ml](http://spark.apache.org/docs/latest/ml-guide.html) pacote fornece um conjunto de alto nível APIs desenvolvidas fotogramas que podem ajudar a criarem e otimizar prático de machine learning pipelines de dados uniforme. [MLlib](http://spark.apache.org/mllib/) biblioteca de dimensionável de aprendizagem do Spark, que capacidades de modelação para este ambiente distribuído.

[HDInsight Spark](../../hdinsight/spark/apache-spark-overview.md) é a oferta do Spark open source alojado no Azure. Também inclui suporte para blocos de notas do Jupyter Scala no cluster do Spark e pode executar consultas interativas do Spark SQL para transformar, filtrar e visualizar dados armazenados no Blob storage do Azure. Os fragmentos de código Scala neste artigo que fornecem as soluções e mostram rastreia relevante para visualizar os dados executam nos blocos de notas do Jupyter instalados nos clusters do Spark. Os passos de modelação nos seguintes tópicos tem o código que mostra como preparar, avaliar, guardar e consumir cada tipo de modelo.

Os passos de configuração e o código neste artigo são para o Azure HDInsight 3.4 Spark 1.6. No entanto, o código neste artigo e no [notas do Jupyter Scala](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/Scala/Exploration%20Modeling%20and%20Scoring%20using%20Scala.ipynb) são genéricas e deve trabalhar em qualquer cluster do Spark. Os passos de configuração e gestão de cluster podem ser ligeiramente diferentes do que é mostrado neste artigo, se não estiver a utilizar o Spark do HDInsight.

> [!NOTE]
> Para um tópico mostra-lhe como utilizar o Python, em vez de Scala para concluir tarefas para um processo de ciência de dados ponto-a-ponto, consulte [ciência de dados com o Spark no Azure HDInsight](spark-overview.md).
> 
> 

## <a name="prerequisites"></a>Pré-requisitos
* Tem de ter uma subscrição do Azure. Se já tiver um, [obtenha uma avaliação gratuita do Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
* Precisa de um cluster do Azure HDInsight 3.4 Spark 1.6 para concluir os procedimentos seguintes. Para criar um cluster, consulte as instruções no [introdução: criar o Apache Spark no Azure HDInsight](../../hdinsight/spark/apache-spark-jupyter-spark-sql.md). Definir o tipo de cluster e a versão de **selecionar tipo de Cluster** menu.

![Configuração de tipo de cluster do HDInsight](./media/scala-walkthrough/spark-cluster-on-portal.png)

> [!INCLUDE [delete-cluster-warning](../../../includes/hdinsight-delete-cluster-warning.md)]
> 
> 

Para obter uma descrição dos dados do NYC taxi viagem e obter instruções sobre como executar código a partir de um bloco de notas do Jupyter no cluster do Spark, consulte as secções relevantes [descrição geral de ciência de dados com o Spark no Azure HDInsight](spark-overview.md).  

## <a name="execute-scala-code-from-a-jupyter-notebook-on-the-spark-cluster"></a>Executar código Scala a partir de um bloco de notas do Jupyter no cluster do Spark
Pode iniciar um bloco de notas do Jupyter do portal do Azure. Localizar o cluster do Spark no seu dashboard e, em seguida, clique nele para introduzir a página de gestão para o cluster. Em seguida, clique em **Dashboards de clusters**e, em seguida, clique em **bloco de notas do Jupyter** para abrir o bloco de notas associado ao cluster do Spark.

![Dashboard de cluster e blocos de notas do Jupyter](./media/scala-walkthrough/spark-jupyter-on-portal.png)

Também pode aceder aos blocos de notas do Jupyter no https://&lt;clustername&gt;.azurehdinsight.net/jupyter. Substitua *clustername* com o nome do cluster. Terá da palavra-passe para a sua conta de administrador aceder os blocos de notas do Jupyter.

![Ir para blocos de notas do Jupyter utilizando o nome do cluster](./media/scala-walkthrough/spark-jupyter-notebook.png)

Selecione **Scala** para ver um diretório com alguns exemplos de blocos de notas prepackaged que utilizam a API do PySpark. Amostras de exploração de modelação e classificação utilizando Scala.ipynb bloco de notas que contém o código para este conjunto de tópicos de Spark não está disponível na [GitHub](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/Spark/Scala).

Pode carregar o bloco de notas diretamente a partir do GitHub para o servidor de notas do Jupyter no cluster do Spark. Na sua home page do Jupyter, clique em de **carregar** botão. No Explorador de ficheiros, cole o URL de GitHub (conteúdo em bruto) do bloco de notas Scala e, em seguida, clique em **abra**. O bloco de notas Scala está disponível no seguinte URL:

[Exploration-Modeling-and-Scoring-using-Scala.ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/Scala/Exploration-Modeling-and-Scoring-using-Scala.ipynb)

## <a name="setup-preset-spark-and-hive-contexts-spark-magics-and-spark-libraries"></a>Configuração: Contextos de predefinição Spark e o Hive, magia de Spark e bibliotecas de Spark
### <a name="preset-spark-and-hive-contexts"></a>Configuração predefinida contextos do Spark e do Hive
    # SET THE START TIME
    import java.util.Calendar
    val beginningTime = Calendar.getInstance().getTime()


Os kernels Spark que são fornecidos com blocos de notas do Jupyter tem predefinido contextos. Não precisa de definir explicitamente o Spark ou contextos de ramo de registo antes de começar a trabalhar com a aplicação estiver a desenvolver. Os contextos predefinidos são:

* `sc`para SparkContext
* `sqlContext`para HiveContext

### <a name="spark-magics"></a>O Spark magia
O kernel do Spark fornece algumas predefinidas "magia", que são comandos especiais que pode chamar com `%%`. Dois destes comandos são utilizados os seguintes exemplos de código.

* `%%local`Especifica que o código em linhas subsequentes será executado localmente. O código tem de ser código Scala válido.
* `%%sql -o <variable name>`executa uma consulta do Hive contra `sqlContext`. Se o `-o` parâmetro é transmitido, o resultado da consulta é continuado no `%%local` Scala contexto como um intervalo de dados de Spark.

Para obter mais informações sobre os kernels para blocos de notas do Jupyter e os respetivos predefinida "magics" que tem de chamar com `%%` (por exemplo, `%%local`), consulte [clusters Kernels disponíveis para blocos de notas do Jupyter com o HDInsight Spark Linux no HDInsight](../../hdinsight/spark/apache-spark-jupyter-notebook-kernels.md).

### <a name="import-libraries"></a>Bibliotecas de importação
Importe o Spark, MLlib e outras bibliotecas, terá de utilizando o seguinte código.

    # IMPORT SPARK AND JAVA LIBRARIES
    import org.apache.spark.sql.SQLContext
    import org.apache.spark.sql.functions._
    import java.text.SimpleDateFormat
    import java.util.Calendar
    import sqlContext.implicits._
    import org.apache.spark.sql.Row

    # IMPORT SPARK SQL FUNCTIONS
    import org.apache.spark.sql.types.{StructType, StructField, StringType, IntegerType, FloatType, DoubleType}
    import org.apache.spark.sql.functions.rand

    # IMPORT SPARK ML FUNCTIONS
    import org.apache.spark.ml.Pipeline
    import org.apache.spark.ml.feature.{StringIndexer, VectorAssembler, OneHotEncoder, VectorIndexer, Binarizer}
    import org.apache.spark.ml.tuning.{ParamGridBuilder, TrainValidationSplit, CrossValidator}
    import org.apache.spark.ml.regression.{LinearRegression, LinearRegressionModel, RandomForestRegressor, RandomForestRegressionModel, GBTRegressor, GBTRegressionModel}
    import org.apache.spark.ml.classification.{LogisticRegression, LogisticRegressionModel, RandomForestClassifier, RandomForestClassificationModel, GBTClassifier, GBTClassificationModel}
    import org.apache.spark.ml.evaluation.{BinaryClassificationEvaluator, RegressionEvaluator, MulticlassClassificationEvaluator}

    # IMPORT SPARK MLLIB FUNCTIONS
    import org.apache.spark.mllib.linalg.{Vector, Vectors}
    import org.apache.spark.mllib.util.MLUtils
    import org.apache.spark.mllib.classification.{LogisticRegressionWithLBFGS, LogisticRegressionModel}
    import org.apache.spark.mllib.regression.{LabeledPoint, LinearRegressionWithSGD, LinearRegressionModel}
    import org.apache.spark.mllib.tree.{GradientBoostedTrees, RandomForest}
    import org.apache.spark.mllib.tree.configuration.BoostingStrategy
    import org.apache.spark.mllib.tree.model.{GradientBoostedTreesModel, RandomForestModel, Predict}
    import org.apache.spark.mllib.evaluation.{BinaryClassificationMetrics, MulticlassMetrics, RegressionMetrics}

    # SPECIFY SQLCONTEXT
    val sqlContext = new SQLContext(sc)


## <a name="data-ingestion"></a>Ingestão de dados
O primeiro passo no processo de ciência de dados é para a ingestão de dados que pretende analisar. Coloque os dados de origens externas ou sistemas em que reside no seu ambiente de exploração e modelação de dados. Neste artigo, os dados de inserção que são um exemplo de 0.1% associados do taxi viagem e fare ficheiro (armazenado como um ficheiro de .tsv). O ambiente de exploração e modelação de dados é Spark. Esta secção contém o código para concluir a seguinte série de tarefas:

1. Definir caminhos de diretório para o armazenamento de dados e o modelo.
2. Leia o conjunto de dados de entrada (armazenada como um ficheiro de .tsv).
3. Definir um esquema para os dados e apagar os dados.
4. Criar um intervalo de dados apagado e -lo em cache na memória.
5. Registe os dados como uma tabela temporária no SQLContext.
6. A tabela de consulta e importar os resultados para um intervalo de dados.

### <a name="set-directory-paths-for-storage-locations-in-azure-blob-storage"></a>Definir caminhos de diretório para localizações de armazenamento de Blob storage do Azure
Spark pode ler e escrever no Blob storage do Azure. Pode utilizar o Spark para processar qualquer um dos seus dados existentes e, em seguida, armazenar os resultados novamente no armazenamento de Blobs.

Para guardar modelos ou ficheiros no Blob storage, tem de especificar corretamente o caminho. Referência de contentor predefinido ligado ao cluster do Spark utilizando um caminho que comece com `wasb:///`. Outras localizações de referência utilizando `wasb://`.

O exemplo de código seguinte especifica a localização dos dados de entrada a ser lido e o caminho para o Blob storage que está ligado ao cluster do Spark onde será guardado o modelo.

    # SET PATHS TO DATA AND MODEL FILE LOCATIONS
    # INGEST DATA AND SPECIFY HEADERS FOR COLUMNS
    val taxi_train_file = sc.textFile("wasb://mllibwalkthroughs@cdspsparksamples.blob.core.windows.net/Data/NYCTaxi/JoinedTaxiTripFare.Point1Pct.Train.tsv")
    val header = taxi_train_file.first;

    # SET THE MODEL STORAGE DIRECTORY PATH
    # NOTE THAT THE FINAL BACKSLASH IN THE PATH IS REQUIRED.
    val modelDir = "wasb:///user/remoteuser/NYCTaxi/Models/";


### <a name="import-data-create-an-rdd-and-define-a-data-frame-according-to-the-schema"></a>Importar dados, crie um RDD e definir um intervalo de dados, de acordo com o esquema
    # RECORD THE START TIME
    val starttime = Calendar.getInstance().getTime()

    # DEFINE THE SCHEMA BASED ON THE HEADER OF THE FILE
    val sqlContext = new SQLContext(sc)
    val taxi_schema = StructType(
        Array(
            StructField("medallion", StringType, true),
            StructField("hack_license", StringType, true),
            StructField("vendor_id", StringType, true),
            StructField("rate_code", DoubleType, true),
            StructField("store_and_fwd_flag", StringType, true),
            StructField("pickup_datetime", StringType, true),
            StructField("dropoff_datetime", StringType, true),
            StructField("pickup_hour", DoubleType, true),
            StructField("pickup_week", DoubleType, true),
            StructField("weekday", DoubleType, true),
            StructField("passenger_count", DoubleType, true),
            StructField("trip_time_in_secs", DoubleType, true),
            StructField("trip_distance", DoubleType, true),
            StructField("pickup_longitude", DoubleType, true),
            StructField("pickup_latitude", DoubleType, true),
            StructField("dropoff_longitude", DoubleType, true),
            StructField("dropoff_latitude", DoubleType, true),
            StructField("direct_distance", StringType, true),
            StructField("payment_type", StringType, true),
            StructField("fare_amount", DoubleType, true),
            StructField("surcharge", DoubleType, true),
            StructField("mta_tax", DoubleType, true),
            StructField("tip_amount", DoubleType, true),
            StructField("tolls_amount", DoubleType, true),
            StructField("total_amount", DoubleType, true),
            StructField("tipped", DoubleType, true),
            StructField("tip_class", DoubleType, true)
            )
        )

    # CAST VARIABLES ACCORDING TO THE SCHEMA
    val taxi_temp = (taxi_train_file.map(_.split("\t"))
                            .filter((r) => r(0) != "medallion")
                            .map(p => Row(p(0), p(1), p(2),
                                p(3).toDouble, p(4), p(5), p(6), p(7).toDouble, p(8).toDouble, p(9).toDouble, p(10).toDouble,
                                p(11).toDouble, p(12).toDouble, p(13).toDouble, p(14).toDouble, p(15).toDouble, p(16).toDouble,
                                p(17), p(18), p(19).toDouble, p(20).toDouble, p(21).toDouble, p(22).toDouble,
                                p(23).toDouble, p(24).toDouble, p(25).toDouble, p(26).toDouble)))


    # CREATE AN INITIAL DATA FRAME AND DROP COLUMNS, AND THEN CREATE A CLEANED DATA FRAME BY FILTERING FOR UNWANTED VALUES OR OUTLIERS
    val taxi_train_df = sqlContext.createDataFrame(taxi_temp, taxi_schema)

    val taxi_df_train_cleaned = (taxi_train_df.drop(taxi_train_df.col("medallion"))
            .drop(taxi_train_df.col("hack_license")).drop(taxi_train_df.col("store_and_fwd_flag"))
            .drop(taxi_train_df.col("pickup_datetime")).drop(taxi_train_df.col("dropoff_datetime"))
            .drop(taxi_train_df.col("pickup_longitude")).drop(taxi_train_df.col("pickup_latitude"))
            .drop(taxi_train_df.col("dropoff_longitude")).drop(taxi_train_df.col("dropoff_latitude"))
            .drop(taxi_train_df.col("surcharge")).drop(taxi_train_df.col("mta_tax"))
            .drop(taxi_train_df.col("direct_distance")).drop(taxi_train_df.col("tolls_amount"))
            .drop(taxi_train_df.col("total_amount")).drop(taxi_train_df.col("tip_class"))
            .filter("passenger_count > 0 and passenger_count < 8 AND payment_type in ('CSH', 'CRD') AND tip_amount >= 0 AND tip_amount < 30 AND fare_amount >= 1 AND fare_amount < 150 AND trip_distance > 0 AND trip_distance < 100 AND trip_time_in_secs > 30 AND trip_time_in_secs < 7200"));

    # CACHE AND MATERIALIZE THE CLEANED DATA FRAME IN MEMORY
    taxi_df_train_cleaned.cache()
    taxi_df_train_cleaned.count()

    # REGISTER THE DATA FRAME AS A TEMPORARY TABLE IN SQLCONTEXT
    taxi_df_train_cleaned.registerTempTable("taxi_train")

    # GET THE TIME TO RUN THE CELL
    val endtime = Calendar.getInstance().getTime()
    val elapsedtime =  ((endtime.getTime() - starttime.getTime())/1000).toString;
    println("Time taken to run the above cell: " + elapsedtime + " seconds.");


**Saída:**

Tempo para executar a célula: 8 segundos.

### <a name="query-the-table-and-import-results-in-a-data-frame"></a>A tabela de consulta e importar os resultados num intervalo de dados
Em seguida, consultar a tabela para fare, passenger e dados de sugestão; filtrar dados danificados e distantes; e imprimir várias linhas.

    # QUERY THE DATA
    val sqlStatement = """
        SELECT fare_amount, passenger_count, tip_amount, tipped
        FROM taxi_train
        WHERE passenger_count > 0 AND passenger_count < 7
        AND fare_amount > 0 AND fare_amount < 200
        AND payment_type in ('CSH', 'CRD')
        AND tip_amount > 0 AND tip_amount < 25
    """
    val sqlResultsDF = sqlContext.sql(sqlStatement)

    # SHOW ONLY THE TOP THREE ROWS
    sqlResultsDF.show(3)

**Saída:**

| fare_amount | passenger_count | tip_amount | tipped |
| --- | --- | --- | --- |
|        13.5 |1.0 |2.9 |1.0 |
|        16.0 |2.0 |3.4 |1.0 |
|        10.5 |2.0 |1.0 |1.0 |

## <a name="data-exploration-and-visualization"></a>Exploração de dados e visualização
Depois de resolver os dados Spark, o próximo passo no processo de ciência de dados é obter uma compreensão mais aprofundada dos dados através de exploração e visualização. Nesta secção, examine os dados de taxi utilizando as consultas SQL. Em seguida, importe os resultados para um intervalo de dados para desenhar as variáveis de destino e potenciais funcionalidades para o visual inspeção ao utilizar a funcionalidade de auto-visualização do Jupyter.

### <a name="use-local-and-sql-magic-to-plot-data"></a>Magic SQL e de utilização local a representar dados
Por predefinição, o resultado de qualquer fragmento de código que executa a partir de um bloco de notas do Jupyter está disponível no contexto de sessão que é mantida em nós de trabalho. Se pretender guardar uma viagem para os nós de trabalho para cada cálculo e se todos os dados que precisa para o cálculo está disponível localmente no nó de servidor Jupyter (que é o nó principal), pode utilizar o `%%local` magic para executar o fragmento de código no servidor do Jupyter.

* **Magic SQL** (`%%sql`). O kernel do HDInsight Spark suporta consultas de HiveQL de fácil inline contra SQLContext. O (`-o VARIABLE_NAME`) argumento persistir o resultado da consulta SQL como moldura Pandas dados no servidor do Jupyter. Isto significa que terá de estar disponível no modo de local.
* `%%local`**magic**. O `%%local` magic executa o código localmente no servidor do Jupyter, o que é o nó principal do cluster do HDInsight. Normalmente, utiliza `%%local` mágica em conjunto com o `%%sql` mágica com o `-o` parâmetro. O `-o` parâmetro seria manter o resultado da consulta SQL localmente e, em seguida, `%%local` magic acionaria o seguinte conjunto de fragmento de código para executar localmente o resultado das consultas de SQL Server que é localmente persistente.

### <a name="query-the-data-by-using-sql"></a>Consultar os dados utilizando o SQL Server
Esta consulta obtém as viagens taxi pela quantidade de fare, a contagem de passenger e a quantidade de sugestão.

    # RUN THE SQL QUERY
    %%sql -q -o sqlResults
    SELECT fare_amount, passenger_count, tip_amount, tipped FROM taxi_train WHERE passenger_count > 0 AND passenger_count < 7 AND fare_amount > 0 AND fare_amount < 200 AND payment_type in ('CSH', 'CRD') AND tip_amount > 0 AND tip_amount < 25

No seguinte código, o `%%local` magic cria um período de dados local, sqlResults. Pode utilizar sqlResults para desenhar utilizando matplotlib.

> [!TIP]
> Local magic é utilizada várias vezes neste artigo. Se o conjunto de dados for grande, de exemplo, para criar um intervalo de dados que pode caber na memória local.
> 
> 

### <a name="plot-the-data"></a>Representar os dados
Pode desenhar utilizando código Python após o intervalo de dados no contexto local como moldura Pandas dados.

    # RUN THE CODE LOCALLY ON THE JUPYTER SERVER
    %%local

    # USE THE JUPYTER AUTO-PLOTTING FEATURE TO CREATE INTERACTIVE FIGURES.
    # CLICK THE TYPE OF PLOT TO GENERATE (LINE, AREA, BAR, ETC.)
    sqlResults


 O kernel do Spark automaticamente visualiza o resultado das consultas SQL (HiveQL) depois de executar o código. Pode escolher entre vários tipos de visualizações:

* Tabela
* Circular
* Linha
* Área
* Barra

Eis o código para representar os dados:

    # RUN THE CODE LOCALLY ON THE JUPYTER SERVER AND IMPORT LIBRARIES
    %%local
    import matplotlib.pyplot as plt
    %matplotlib inline

    # PLOT TIP BY PAYMENT TYPE AND PASSENGER COUNT
    ax1 = sqlResults[['tip_amount']].plot(kind='hist', bins=25, facecolor='lightblue')
    ax1.set_title('Tip amount distribution')
    ax1.set_xlabel('Tip Amount ($)')
    ax1.set_ylabel('Counts')
    plt.suptitle('')
    plt.show()

    # PLOT TIP BY PASSENGER COUNT
    ax2 = sqlResults.boxplot(column=['tip_amount'], by=['passenger_count'])
    ax2.set_title('Tip amount by Passenger count')
    ax2.set_xlabel('Passenger count')
    ax2.set_ylabel('Tip Amount ($)')
    plt.suptitle('')
    plt.show()

    # PLOT TIP AMOUNT BY FARE AMOUNT; SCALE POINTS BY PASSENGER COUNT
    ax = sqlResults.plot(kind='scatter', x= 'fare_amount', y = 'tip_amount', c='blue', alpha = 0.10, s=5*(sqlResults.passenger_count))
    ax.set_title('Tip amount by Fare amount')
    ax.set_xlabel('Fare Amount ($)')
    ax.set_ylabel('Tip Amount ($)')
    plt.axis([-2, 80, -2, 20])
    plt.show()


**Saída:**

![Sugestão quantidade histograma](./media/scala-walkthrough/plot-tip-amount-histogram.png)

![Quantidade de sugestão por contagem de passenger](./media/scala-walkthrough/plot-tip-amount-by-passenger-count.png)

![Quantidade de sugestão pela quantidade de fare](./media/scala-walkthrough/plot-tip-amount-by-fare-amount.png)

## <a name="create-features-and-transform-features-and-then-prep-data-for-input-into-modeling-functions"></a>Criar funcionalidades e funcionalidades de transformação e, em seguida, de dados de entrada para as funções de modelação de preparação
Para funções de modelação de árvore do Spark ML e o MLlib, terá de preparar o destino e funcionalidades utilizando uma variedade de técnicas, tais como a discretização, indexação, acesso frequente uma codificação e vectorization. Eis os procedimentos a seguir nesta secção:

1. Criar uma nova funcionalidade por **discretização** horas para o tráfego de registos de tempo.
2. Aplicar **frequente de uma codificação e indexação** categórico funcionalidades.
3. **Exemplo e o conjunto de dados de divisão** em frações de formação e teste.
4. **Especifique a variável de formação e funcionalidades**e, em seguida, criar indexada ou frequente de um codificado formação e testar entrado ponto identificado resiliente distribuídas conjuntos de dados (RDDs) ou pacotes de dados.
5. Automaticamente **categorizar e vectorize funcionalidades e destinos** para utilizar como entradas para modelos de machine learning.

### <a name="create-a-new-feature-by-binning-hours-into-traffic-time-buckets"></a>Criar uma nova funcionalidade de discretização horas para registos de tempo de tráfego
Este código mostra-lhe como criar uma nova funcionalidade de discretização horas para registos de tempo de tráfego e como colocar em cache da moldura de dados resultante na memória. Onde frames RDDs e os dados são utilizados repetidamente, colocação em cache leva a melhorada os tempos de execução. Em conformidade, irá cache RDDs e pacotes de dados em várias fases nos seguintes procedimentos.

    # CREATE FOUR BUCKETS FOR TRAFFIC TIMES
    val sqlStatement = """
        SELECT *,
        CASE
         WHEN (pickup_hour <= 6 OR pickup_hour >= 20) THEN "Night"
         WHEN (pickup_hour >= 7 AND pickup_hour <= 10) THEN "AMRush"
         WHEN (pickup_hour >= 11 AND pickup_hour <= 15) THEN "Afternoon"
         WHEN (pickup_hour >= 16 AND pickup_hour <= 19) THEN "PMRush"
        END as TrafficTimeBins
        FROM taxi_train
    """
    val taxi_df_train_with_newFeatures = sqlContext.sql(sqlStatement)

    # CACHE THE DATA FRAME IN MEMORY AND MATERIALIZE THE DATA FRAME IN MEMORY
    taxi_df_train_with_newFeatures.cache()
    taxi_df_train_with_newFeatures.count()


### <a name="indexing-and-one-hot-encoding-of-categorical-features"></a>A indexação e acesso frequente uma codificação de funcionalidades categórico
A modelação e prever as funções do MLlib requerem funcionalidades com dados de entrada categórico para ser incluída num índice ou codificado antes de utilizar. Esta secção mostra como o índice ou codificar categórico funcionalidades para a entrada para as funções de modelação.

Terá de índice ou codificar seus modelos de diversas formas, consoante o modelo. Por exemplo, modelos de regressão logística da e linear necessitam de acesso frequente uma codificação. Por exemplo, uma funcionalidade com três categorias pode ser expandida para três colunas de funcionalidade. Cada coluna iria conter 0 ou 1 consoante a categoria de uma observação. MLlib fornece o [OneHotEncoder](http://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.OneHotEncoder.html#sklearn.preprocessing.OneHotEncoder) função para acesso frequente uma codificação. Neste codificador mapeia uma coluna de índices de etiqueta a uma coluna de vetores de binários no máximo um único um valor. Com esta codificação, algoritmos de esperam numéricos funcionalidades importantes, como regressão logística da, podem ser aplicados às funcionalidades categórico.

Aqui transformar apenas quatro variáveis para mostrar os exemplos que são as cadeias de carateres. Também pode indexar outras variáveis, como o dia da semana, representadas pelo numérico valores, como categórico variáveis.

Para indexação, utilize `StringIndexer()`e para acesso frequente uma codificação, utilize `OneHotEncoder()` funções no MLlib. Eis o código para o índice e codificar categórico funcionalidades:

    # CREATE INDEXES AND ONE-HOT ENCODED VECTORS FOR SEVERAL CATEGORICAL FEATURES

    # RECORD THE START TIME
    val starttime = Calendar.getInstance().getTime()

    # INDEX AND ENCODE VENDOR_ID
    val stringIndexer = new StringIndexer().setInputCol("vendor_id").setOutputCol("vendorIndex").fit(taxi_df_train_with_newFeatures)
    val indexed = stringIndexer.transform(taxi_df_train_with_newFeatures)
    val encoder = new OneHotEncoder().setInputCol("vendorIndex").setOutputCol("vendorVec")
    val encoded1 = encoder.transform(indexed)

    # INDEX AND ENCODE RATE_CODE
    val stringIndexer = new StringIndexer().setInputCol("rate_code").setOutputCol("rateIndex").fit(encoded1)
    val indexed = stringIndexer.transform(encoded1)
    val encoder = new OneHotEncoder().setInputCol("rateIndex").setOutputCol("rateVec")
    val encoded2 = encoder.transform(indexed)

    # INDEX AND ENCODE PAYMENT_TYPE
    val stringIndexer = new StringIndexer().setInputCol("payment_type").setOutputCol("paymentIndex").fit(encoded2)
    val indexed = stringIndexer.transform(encoded2)
    val encoder = new OneHotEncoder().setInputCol("paymentIndex").setOutputCol("paymentVec")
    val encoded3 = encoder.transform(indexed)

    # INDEX AND TRAFFIC TIME BINS
    val stringIndexer = new StringIndexer().setInputCol("TrafficTimeBins").setOutputCol("TrafficTimeBinsIndex").fit(encoded3)
    val indexed = stringIndexer.transform(encoded3)
    val encoder = new OneHotEncoder().setInputCol("TrafficTimeBinsIndex").setOutputCol("TrafficTimeBinsVec")
    val encodedFinal = encoder.transform(indexed)

    # GET THE TIME TO RUN THE CELL
    val endtime = Calendar.getInstance().getTime()
    val elapsedtime =  ((endtime.getTime() - starttime.getTime())/1000).toString;
    println("Time taken to run the above cell: " + elapsedtime + " seconds.");


**Saída:**

Tempo para executar a célula: 4 segundos.

### <a name="sample-and-split-the-data-set-into-training-and-test-fractions"></a>Exemplo e dividir o conjunto de dados em frações de formação e teste
Este código cria uma amostra aleatória dos dados (25%, neste exemplo). Embora não seja necessária para este exemplo, porque o tamanho do conjunto de dados da amostragem, o artigo mostra como pode apresentar exemplos para que saibam como utilizá-la para os seus próprios problemas quando necessário. Quando os exemplos são grandes, isto permite poupar tempo significativo enquanto preparar modelos. Em seguida, divida o exemplo para uma parte de formação (75%, neste exemplo) e uma parte de teste (25%, neste exemplo) para utilizar na classificação e modelação de regressão.

Adicione um número aleatório (entre 0 e 1) para cada linha (numa coluna "rand") que pode ser utilizada para selecionar subconjuntos de validação de validação cruzada durante a preparação.

    # RECORD THE START TIME
    val starttime = Calendar.getInstance().getTime()

    # SPECIFY SAMPLING AND SPLITTING FRACTIONS
    val samplingFraction = 0.25;
    val trainingFraction = 0.75;
    val testingFraction = (1-trainingFraction);
    val seed = 1234;
    val encodedFinalSampledTmp = encodedFinal.sample(withReplacement = false, fraction = samplingFraction, seed = seed)
    val sampledDFcount = encodedFinalSampledTmp.count().toInt

    val generateRandomDouble = udf(() => {
        scala.util.Random.nextDouble
    })

    # ADD A RANDOM NUMBER FOR CROSS-VALIDATION
    val encodedFinalSampled = encodedFinalSampledTmp.withColumn("rand", generateRandomDouble());

    # SPLIT THE SAMPLED DATA FRAME INTO TRAIN AND TEST, WITH A RANDOM COLUMN ADDED FOR DOING CROSS-VALIDATION (SHOWN LATER)
    # INCLUDE A RANDOM COLUMN FOR CREATING CROSS-VALIDATION FOLDS
    val splits = encodedFinalSampled.randomSplit(Array(trainingFraction, testingFraction), seed = seed)
    val trainData = splits(0)
    val testData = splits(1)

    # GET THE TIME TO RUN THE CELL
    val endtime = Calendar.getInstance().getTime()
    val elapsedtime =  ((endtime.getTime() - starttime.getTime())/1000).toString;
    println("Time taken to run the above cell: " + elapsedtime + " seconds.");


**Saída:**

Tempo para executar a célula: 2 segundos.

### <a name="specify-training-variable-and-features-and-then-create-indexed-or-one-hot-encoded-training-and-testing-input-labeled-point-rdds-or-data-frames"></a>Especifique a variável de formação e funcionalidades e, em seguida, criar indexada ou frequente de um codificado formação e testar com a etiqueta de fotogramas RDDs ponto ou dados de entrada
Esta secção contém o código que mostra como indexar os dados de texto categórico como um tipo de dados do ponto de identificados e, codificar, pelo que pode utilizá-lo a dar formação e testar regressão logística da MLlib e outros modelos de classificação. Os objetos de ponto identificados são RDDs que estejam formatados de uma forma que é necessário como dados de entrada pela maioria dos algoritmos no MLlib do machine learning. A [denominado ponto](https://spark.apache.org/docs/latest/mllib-data-types.html#labeled-point) está associado a um vetor local, em ou disperso, com uma etiqueta/resposta.

Neste código, especifique a variável (dependentes) de destino e as funcionalidades a utilizar para formação modelos. Em seguida, criar indexada ou frequente de um codificado formação e testar com a etiqueta de fotogramas RDDs ponto ou dados de entrada.

    # RECORD THE START TIME
    val starttime = Calendar.getInstance().getTime()

    # MAP NAMES OF FEATURES AND TARGETS FOR CLASSIFICATION AND REGRESSION PROBLEMS
    val featuresIndOneHot = List("paymentVec", "vendorVec", "rateVec", "TrafficTimeBinsVec", "pickup_hour", "weekday", "passenger_count", "trip_time_in_secs", "trip_distance", "fare_amount").map(encodedFinalSampled.columns.indexOf(_))
    val featuresIndIndex = List("paymentIndex", "vendorIndex", "rateIndex", "TrafficTimeBinsIndex", "pickup_hour", "weekday", "passenger_count", "trip_time_in_secs", "trip_distance", "fare_amount").map(encodedFinalSampled.columns.indexOf(_))

    # SPECIFY THE TARGET FOR CLASSIFICATION ('tipped') AND REGRESSION ('tip_amount') PROBLEMS
    val targetIndBinary = List("tipped").map(encodedFinalSampled.columns.indexOf(_))
    val targetIndRegression = List("tip_amount").map(encodedFinalSampled.columns.indexOf(_))

    # CREATE INDEXED LABELED POINT RDD OBJECTS
    val indexedTRAINbinary = trainData.rdd.map(r => LabeledPoint(r.getDouble(targetIndBinary(0).toInt), Vectors.dense(featuresIndIndex.map(r.getDouble(_)).toArray)))
    val indexedTESTbinary = testData.rdd.map(r => LabeledPoint(r.getDouble(targetIndBinary(0).toInt), Vectors.dense(featuresIndIndex.map(r.getDouble(_)).toArray)))
    val indexedTRAINreg = trainData.rdd.map(r => LabeledPoint(r.getDouble(targetIndRegression(0).toInt), Vectors.dense(featuresIndIndex.map(r.getDouble(_)).toArray)))
    val indexedTESTreg = testData.rdd.map(r => LabeledPoint(r.getDouble(targetIndRegression(0).toInt), Vectors.dense(featuresIndIndex.map(r.getDouble(_)).toArray)))

    # CREATE INDEXED DATA FRAMES THAT YOU CAN USE TO TRAIN BY USING SPARK ML FUNCTIONS
    val indexedTRAINbinaryDF = indexedTRAINbinary.toDF()
    val indexedTESTbinaryDF = indexedTESTbinary.toDF()
    val indexedTRAINregDF = indexedTRAINreg.toDF()
    val indexedTESTregDF = indexedTESTreg.toDF()

    # CREATE ONE-HOT ENCODED (VECTORIZED) DATA FRAMES THAT YOU CAN USE TO TRAIN BY USING SPARK ML FUNCTIONS
    val assemblerOneHot = new VectorAssembler().setInputCols(Array("paymentVec", "vendorVec", "rateVec", "TrafficTimeBinsVec", "pickup_hour", "weekday", "passenger_count", "trip_time_in_secs", "trip_distance", "fare_amount")).setOutputCol("features")
    val OneHotTRAIN = assemblerOneHot.transform(trainData)
    val OneHotTEST = assemblerOneHot.transform(testData)

    # GET THE TIME TO RUN THE CELL
    val endtime = Calendar.getInstance().getTime()
    val elapsedtime =  ((endtime.getTime() - starttime.getTime())/1000).toString;
    println("Time taken to run the above cell: " + elapsedtime + " seconds.");


**Saída:**

Tempo para executar a célula: 4 segundos.

### <a name="automatically-categorize-and-vectorize-features-and-targets-to-use-as-inputs-for-machine-learning-models"></a>Categorizar e vectorize funcionalidades e destinos para utilizar como entradas para modelos de machine learning automaticamente
Utilize o Spark ML a categorização de destino e funcionalidades para utilizar nas funções de modelação de árvore. O código de conclusão duas tarefas:

* Cria um destino de binário para classificação, atribuir um valor de 0 ou 1 para cada ponto de dados entre 0 e 1 utilizando um valor de limiar de 0,5.
* Automaticamente categoriza funcionalidades. Se o número de valores distintos de numérico para qualquer funcionalidade for inferior a 32, essa funcionalidade foi categorizada.

Eis o código para estas duas tarefas.

    # CATEGORIZE FEATURES AND BINARIZE THE TARGET FOR THE BINARY CLASSIFICATION PROBLEM

    # TRAIN DATA
    val indexer = new VectorIndexer().setInputCol("features").setOutputCol("featuresCat").setMaxCategories(32)
    val indexerModel = indexer.fit(indexedTRAINbinaryDF)
    val indexedTrainwithCatFeat = indexerModel.transform(indexedTRAINbinaryDF)
    val binarizer: Binarizer = new Binarizer().setInputCol("label").setOutputCol("labelBin").setThreshold(0.5)
    val indexedTRAINwithCatFeatBinTarget = binarizer.transform(indexedTrainwithCatFeat)

    # TEST DATA
    val indexerModel = indexer.fit(indexedTESTbinaryDF)
    val indexedTrainwithCatFeat = indexerModel.transform(indexedTESTbinaryDF)
    val binarizer: Binarizer = new Binarizer().setInputCol("label").setOutputCol("labelBin").setThreshold(0.5)
    val indexedTESTwithCatFeatBinTarget = binarizer.transform(indexedTrainwithCatFeat)

    # CATEGORIZE FEATURES FOR THE REGRESSION PROBLEM
    # CREATE PROPERLY INDEXED AND CATEGORIZED DATA FRAMES FOR TREE-BASED MODELS

    # TRAIN DATA
    val indexer = new VectorIndexer().setInputCol("features").setOutputCol("featuresCat").setMaxCategories(32)
    val indexerModel = indexer.fit(indexedTRAINregDF)
    val indexedTRAINwithCatFeat = indexerModel.transform(indexedTRAINregDF)

    # TEST DATA
    val indexerModel = indexer.fit(indexedTESTbinaryDF)
    val indexedTESTwithCatFeat = indexerModel.transform(indexedTESTregDF)



## <a name="binary-classification-model-predict-whether-a-tip-should-be-paid"></a>Modelo de classificação binária: prever se deve ser paga uma sugestão
Nesta secção, é criar três tipos de modelos de classificação binária para prever ou não deve ser paga uma sugestão:

* A **modelo de regressão logística da** ao utilizar o Spark ML `LogisticRegression()` função
* A **modelo de classificação de floresta aleatório** ao utilizar o Spark ML `RandomForestClassifier()` função
* A **gradação modelo de classificação de árvore aumento** utilizando o MLlib `GradientBoostedTrees()` função

### <a name="create-a-logistic-regression-model"></a>Criar um modelo de regressão logística da
Em seguida, crie um modelo de regressão logística ao utilizar o Spark ML `LogisticRegression()` função. Criar o modelo de criação de código de uma série de passos:

1. **Preparar o modelo** dados com um conjunto de parâmetros.
2. **Avalie o modelo** num conjunto de dados de teste com a métrica.
3. **Guarde o modelo** no armazenamento de BLOBs para consumo futuro.
4. **O modelo de pontuação** contra dados de teste.
5. **Desenhar os resultados** com recetor operativo curvas característica (ROC).

Eis o código para estes procedimentos:

    # CREATE A LOGISTIC REGRESSION MODEL
    val lr = new LogisticRegression().setLabelCol("tipped").setFeaturesCol("features").setMaxIter(10).setRegParam(0.3).setElasticNetParam(0.8)
    val lrModel = lr.fit(OneHotTRAIN)

    # PREDICT ON THE TEST DATA SET
    val predictions = lrModel.transform(OneHotTEST)

    # SELECT `BinaryClassificationEvaluator()` TO COMPUTE THE TEST ERROR
    val evaluator = new BinaryClassificationEvaluator().setLabelCol("tipped").setRawPredictionCol("probability").setMetricName("areaUnderROC")
    val ROC = evaluator.evaluate(predictions)
    println("ROC on test data = " + ROC)

    # SAVE THE MODEL
    val datestamp = Calendar.getInstance().getTime().toString.replaceAll(" ", ".").replaceAll(":", "_");
    val modelName = "LogisticRegression__"
    val filename = modelDir.concat(modelName).concat(datestamp)
    lrModel.save(filename);

Carregar, Pontuar e guardar os resultados.

    # RECORD THE START TIME
    val starttime = Calendar.getInstance().getTime()

    # LOAD THE SAVED MODEL AND SCORE THE TEST DATA SET
    val savedModel = org.apache.spark.ml.classification.LogisticRegressionModel.load(filename)
    println(s"Coefficients: ${savedModel.coefficients} Intercept: ${savedModel.intercept}")

    # SCORE THE MODEL ON THE TEST DATA
    val predictions = savedModel.transform(OneHotTEST).select("tipped","probability","rawPrediction")
    predictions.registerTempTable("testResults")

    # SELECT `BinaryClassificationEvaluator()` TO COMPUTE THE TEST ERROR
    val evaluator = new BinaryClassificationEvaluator().setLabelCol("tipped").setRawPredictionCol("probability").setMetricName("areaUnderROC")
    val ROC = evaluator.evaluate(predictions)

    # GET THE TIME TO RUN THE CELL
    val endtime = Calendar.getInstance().getTime()
    val elapsedtime =  ((endtime.getTime() - starttime.getTime())/1000).toString;
    println("Time taken to run the above cell: " + elapsedtime + " seconds.");

    # PRINT THE ROC RESULTS
    println("ROC on test data = " + ROC)


**Saída:**

ROC em dados de teste = 0.9827381497557599

Utilize o Python nos pacotes de dados do locais Pandas para desenhar a curva ROC.

    # QUERY THE RESULTS
    %%sql -q -o sqlResults
    SELECT tipped, probability from testResults


    # RUN THE CODE LOCALLY ON THE JUPYTER SERVER AND IMPORT LIBRARIES
    %%local
    %matplotlib inline
    from sklearn.metrics import roc_curve,auc

    sqlResults['probFloat'] = sqlResults.apply(lambda row: row['probability'].values()[0][1], axis=1)
    predictions_pddf = sqlResults[["tipped","probFloat"]]

    # PREDICT THE ROC CURVE
    # predictions_pddf = sqlResults.rename(columns={'_1': 'probability', 'tipped': 'label'})
    prob = predictions_pddf["probFloat"]
    fpr, tpr, thresholds = roc_curve(predictions_pddf['tipped'], prob, pos_label=1);
    roc_auc = auc(fpr, tpr)

    # PLOT THE ROC CURVE
    plt.figure(figsize=(5,5))
    plt.plot(fpr, tpr, label='ROC curve (area = %0.2f)' % roc_auc)
    plt.plot([0, 1], [0, 1], 'k--')
    plt.xlim([0.0, 1.0])
    plt.ylim([0.0, 1.05])
    plt.xlabel('False Positive Rate')
    plt.ylabel('True Positive Rate')
    plt.title('ROC Curve')
    plt.legend(loc="lower right")
    plt.show()


**Saída:**

![Sugestão ou nenhum curva ROC de sugestão](./media/scala-walkthrough/plot-roc-curve-tip-or-not.png)

### <a name="create-a-random-forest-classification-model"></a>Criar um modelo de classificação de floresta aleatório
Em seguida, crie um modelo de classificação de floresta aleatório ao utilizar o Spark ML `RandomForestClassifier()` funcionar e, em seguida, avalie o modelo em dados de teste.

    # RECORD THE START TIME
    val starttime = Calendar.getInstance().getTime()

    # CREATE THE RANDOM FOREST CLASSIFIER MODEL
    val rf = new RandomForestClassifier().setLabelCol("labelBin").setFeaturesCol("featuresCat").setNumTrees(10).setSeed(1234)

    # FIT THE MODEL
    val rfModel = rf.fit(indexedTRAINwithCatFeatBinTarget)
    val predictions = rfModel.transform(indexedTESTwithCatFeatBinTarget)

    # EVALUATE THE MODEL
    val evaluator = new MulticlassClassificationEvaluator().setLabelCol("label").setPredictionCol("prediction").setMetricName("f1")
    val Test_f1Score = evaluator.evaluate(predictions)
    println("F1 score on test data: " + Test_f1Score);

    # GET THE TIME TO RUN THE CELL
    val endtime = Calendar.getInstance().getTime()
    val elapsedtime =  ((endtime.getTime() - starttime.getTime())/1000).toString;
    println("Time taken to run the above cell: " + elapsedtime + " seconds.");

    # CALCULATE BINARY CLASSIFICATION EVALUATION METRICS
    val evaluator = new BinaryClassificationEvaluator().setLabelCol("label").setRawPredictionCol("probability").setMetricName("areaUnderROC")
    val ROC = evaluator.evaluate(predictions)
    println("ROC on test data = " + ROC)


**Saída:**

ROC em dados de teste = 0.9847103571552683

### <a name="create-a-gbt-classification-model"></a>Criar um modelo de classificação GBT
Em seguida, crie um modelo de classificação GBT através do MLlib `GradientBoostedTrees()` funcionar e, em seguida, avalie o modelo em dados de teste.

    # TRAIN A GBT CLASSIFICATION MODEL BY USING MLLIB AND A LABELED POINT

    # RECORD THE START TIME
    val starttime = Calendar.getInstance().getTime()

    # DEFINE THE GBT CLASSIFICATION MODEL
    val boostingStrategy = BoostingStrategy.defaultParams("Classification")
    boostingStrategy.numIterations = 20
    boostingStrategy.treeStrategy.numClasses = 2
    boostingStrategy.treeStrategy.maxDepth = 5
    boostingStrategy.treeStrategy.categoricalFeaturesInfo = Map[Int, Int]((0,2),(1,2),(2,6),(3,4))

    # TRAIN THE MODEL
    val gbtModel = GradientBoostedTrees.train(indexedTRAINbinary, boostingStrategy)

    # SAVE THE MODEL IN BLOB STORAGE
    val datestamp = Calendar.getInstance().getTime().toString.replaceAll(" ", ".").replaceAll(":", "_");
    val modelName = "GBT_Classification__"
    val filename = modelDir.concat(modelName).concat(datestamp)
    gbtModel.save(sc, filename);

    # EVALUATE THE MODEL ON TEST INSTANCES AND THE COMPUTE TEST ERROR
    val labelAndPreds = indexedTESTbinary.map { point =>
      val prediction = gbtModel.predict(point.features)
      (point.label, prediction)
    }
    val testErr = labelAndPreds.filter(r => r._1 != r._2).count.toDouble / indexedTRAINbinary.count()
    //println("Learned classification GBT model:\n" + gbtModel.toDebugString)
    println("Test Error = " + testErr)

    # USE BINARY AND MULTICLASS METRICS TO EVALUATE THE MODEL ON THE TEST DATA
    val metrics = new MulticlassMetrics(labelAndPreds)
    println(s"Precision: ${metrics.precision}")
    println(s"Recall: ${metrics.recall}")
    println(s"F1 Score: ${metrics.fMeasure}")

    val metrics = new BinaryClassificationMetrics(labelAndPreds)
    println(s"Area under PR curve: ${metrics.areaUnderPR}")
    println(s"Area under ROC curve: ${metrics.areaUnderROC}")

    # GET THE TIME TO RUN THE CELL
    val endtime = Calendar.getInstance().getTime()
    val elapsedtime =  ((endtime.getTime() - starttime.getTime())/1000).toString;
    println("Time taken to run the above cell: " + elapsedtime + " seconds.");

    # PRINT THE ROC METRIC
    println(s"Area under ROC curve: ${metrics.areaUnderROC}")


**Saída:**

Área em curva ROC: 0.9846895479241554

## <a name="regression-model-predict-tip-amount"></a>Modelo de regressão: prever a quantidade de sugestão
Nesta secção, permite criar dois tipos de modelos de regressão para prever a quantidade de sugestão:

* A **modelo de regressão linear regularized** ao utilizar o Spark ML `LinearRegression()` função. Irá guardar o modelo e avaliar o modelo em dados de teste.
* A **os aumentos gradiente modelo de regressão de árvore** ao utilizar o Spark ML `GBTRegressor()` função.

### <a name="create-a-regularized-linear-regression-model"></a>Criar um modelo de regressão linear regularized
    # RECORD THE START TIME
    val starttime = Calendar.getInstance().getTime()

    # CREATE A REGULARIZED LINEAR REGRESSION MODEL BY USING THE SPARK ML FUNCTION AND DATA FRAMES
    val lr = new LinearRegression().setLabelCol("tip_amount").setFeaturesCol("features").setMaxIter(10).setRegParam(0.3).setElasticNetParam(0.8)

    # FIT THE MODEL BY USING DATA FRAMES
    val lrModel = lr.fit(OneHotTRAIN)
    println(s"Coefficients: ${lrModel.coefficients} Intercept: ${lrModel.intercept}")

    # SUMMARIZE THE MODEL OVER THE TRAINING SET AND PRINT METRICS
    val trainingSummary = lrModel.summary
    println(s"numIterations: ${trainingSummary.totalIterations}")
    println(s"objectiveHistory: ${trainingSummary.objectiveHistory.toList}")
    trainingSummary.residuals.show()
    println(s"RMSE: ${trainingSummary.rootMeanSquaredError}")
    println(s"r2: ${trainingSummary.r2}")

    # SAVE THE MODEL IN AZURE BLOB STORAGE
    val datestamp = Calendar.getInstance().getTime().toString.replaceAll(" ", ".").replaceAll(":", "_");
    val modelName = "LinearRegression__"
    val filename = modelDir.concat(modelName).concat(datestamp)
    lrModel.save(filename);

    # PRINT THE COEFFICIENTS
    println(s"Coefficients: ${lrModel.coefficients} Intercept: ${lrModel.intercept}")

    # SCORE THE MODEL ON TEST DATA
    val predictions = lrModel.transform(OneHotTEST)

    # EVALUATE THE MODEL ON TEST DATA
    val evaluator = new RegressionEvaluator().setLabelCol("tip_amount").setPredictionCol("prediction").setMetricName("r2")
    val r2 = evaluator.evaluate(predictions)
    println("R-sqr on test data = " + r2)

    # GET THE TIME TO RUN THE CELL
    val endtime = Calendar.getInstance().getTime()
    val elapsedtime =  ((endtime.getTime() - starttime.getTime())/1000).toString;
    println("Time taken to run the above cell: " + elapsedtime + " seconds.");


**Saída:**

Tempo para executar a célula: 13 segundos.

    # LOAD A SAVED LINEAR REGRESSION MODEL FROM BLOB STORAGE AND SCORE A TEST DATA SET

    # RECORD THE START TIME
    val starttime = Calendar.getInstance().getTime()

    # LOAD A SAVED LINEAR REGRESSION MODEL FROM AZURE BLOB STORAGE
    val savedModel = org.apache.spark.ml.regression.LinearRegressionModel.load(filename)
    println(s"Coefficients: ${savedModel.coefficients} Intercept: ${savedModel.intercept}")

    # SCORE THE MODEL ON TEST DATA
    val predictions = savedModel.transform(OneHotTEST).select("tip_amount","prediction")
    predictions.registerTempTable("testResults")

    # EVALUATE THE MODEL ON TEST DATA
    val evaluator = new RegressionEvaluator().setLabelCol("tip_amount").setPredictionCol("prediction").setMetricName("r2")
    val r2 = evaluator.evaluate(predictions)
    println("R-sqr on test data = " + r2)

    # GET THE TIME TO RUN THE CELL
    val endtime = Calendar.getInstance().getTime()
    val elapsedtime =  ((endtime.getTime() - starttime.getTime())/1000).toString;
    println("Time taken to run the above cell: " + elapsedtime + " seconds.");

    # PRINT THE RESULTS
    println("R-sqr on test data = " + r2)


**Saída:**

R-sqr em dados de teste = 0.5960320470835743

Em seguida, consultar os resultados do teste como um intervalo de dados e utilizar AutoVizWidget e matplotlib para visualizá-lo.

    # RUN A SQL QUERY
    %%sql -q -o sqlResults
    select * from testResults

    # RUN THE CODE LOCALLY ON THE JUPYTER SERVER
    %%local

    # USE THE JUPYTER AUTO-PLOTTING FEATURE TO CREATE INTERACTIVE FIGURES
    # CLICK THE TYPE OF PLOT TO GENERATE (LINE, AREA, BAR, AND SO ON)
    sqlResults

O código cria um intervalo de dados locais da saída de consulta e rastreia os dados. O `%%local` magic cria um período de dados local, `sqlResults`, que pode ser utilizado para desenhar com matplotlib.

> [!NOTE]
> Este magic Spark sido utilizada várias vezes neste artigo. Se a quantidade de dados for grande, deve de exemplo para criar um intervalo de dados que pode caber na memória local.
> 
> 

Crie rastreia utilizando Python matplotlib.

    # RUN THE CODE LOCALLY ON THE JUPYTER SERVER AND IMPORT LIBRARIES
    %%local
    sqlResults
    %matplotlib inline
    import numpy as np

    # PLOT THE RESULTS
    ax = sqlResults.plot(kind='scatter', figsize = (6,6), x='tip_amount', y='prediction', color='blue', alpha = 0.25, label='Actual vs. predicted');
    fit = np.polyfit(sqlResults['tip_amount'], sqlResults['prediction'], deg=1)
    ax.set_title('Actual vs. Predicted Tip Amounts ($)')
    ax.set_xlabel("Actual")
    ax.set_ylabel("Predicted")
    #ax.plot(sqlResults['tip_amount'], fit[0] * sqlResults['prediction'] + fit[1], color='magenta')
    plt.axis([-1, 15, -1, 8])
    plt.show(ax)

**Saída:**

![Sugestão quantidade: real vs previsto](./media/scala-walkthrough/plot-actual-vs-predicted-tip-amount.png)

### <a name="create-a-gbt-regression-model"></a>Criar um modelo de regressão GBT
Criar um modelo de regressão GBT ao utilizar o Spark ML `GBTRegressor()` funcionar e, em seguida, avalie o modelo em dados de teste.

[Árvores elevada de gradação](http://spark.apache.org/docs/latest/ml-classification-regression.html#gradient-boosted-trees-gbts) (GBTs) são ensembles de árvores de decisões. GBTs preparar árvores de decisões iteratively para minimizar a uma função de perda. Pode utilizar GBTs para regressão e classificação. Pode processar categórico funcionalidades, não necessitam de dimensionamento de funcionalidade e podem capturar nonlinearities e interações de funcionalidade. Também pode utilizar-lhes uma definição de classificação de várias classes.

    # RECORD THE START TIME
    val starttime = Calendar.getInstance().getTime()

    # TRAIN A GBT REGRESSION MODEL
    val gbt = new GBTRegressor().setLabelCol("label").setFeaturesCol("featuresCat").setMaxIter(10)
    val gbtModel = gbt.fit(indexedTRAINwithCatFeat)

    # MAKE PREDICTIONS
    val predictions = gbtModel.transform(indexedTESTwithCatFeat)

    # COMPUTE TEST SET R2
    val evaluator = new RegressionEvaluator().setLabelCol("label").setPredictionCol("prediction").setMetricName("r2")
    val Test_R2 = evaluator.evaluate(predictions)


    # GET THE TIME TO RUN THE CELL
    val endtime = Calendar.getInstance().getTime()
    val elapsedtime =  ((endtime.getTime() - starttime.getTime())/1000).toString;
    println("Time taken to run the above cell: " + elapsedtime + " seconds.");

    # PRINT THE RESULTS
    println("Test R-sqr is: " + Test_R2);


**Saída:**

Teste R-sqr é: 0.7655383534596654

## <a name="advanced-modeling-utilities-for-optimization"></a>Utilitários de modelação avançadas para a otimização
Nesta secção, é utilizar utilitários de aprendizagem máquina que os programadores frequentemente a utilizar para a otimização de modelo. Especificamente, pode otimizar a modelos de machine learning três formas diferentes, utilizando varrimento de parâmetro e validação cruzada:

* Dividir os dados em conjuntos de formação e validação, otimizar o modelo utilizando hyper parâmetro varrimento num conjunto de formação e avaliar num conjunto de validação (regressão linear)
* Otimizar o modelo utilizando a validação cruzada e hyper varrimento utilizando a função de CrossValidator do Spark ML (classificação binária) parâmetro
* Otimizar o modelo utilizando o código de validação cruzada e varrimento de parâmetro personalizado para utilizar qualquer do machine learning função e o parâmetro de conjunto (regressão linear)

**Validação cruzada** é uma técnica que avalia o quão bem um modelo preparado num conjunto conhecido de dados será generalizar para prever as funcionalidades de conjuntos de dados em que esta não foi preparada. A ideia geral atrás esta técnica é que um modelo está preparado num conjunto de dados de dados conhecidos e, em seguida, a precisão do seu predições é testada em relação a um conjunto de dados independente. Uma implementação comum é a dividir um conjunto de dados em *k*-subconjuntos de validação e, em seguida, preparar o modelo em round-robin em todos os um elemento de subconjuntos de validação.

**Otimização de Hyper-parâmetro** é o problema de escolher um conjunto de hyper-parâmetros para um algoritmo de aprendizagem, normalmente, com o objetivo de otimizar uma medida de desempenho o algoritmo um conjunto de dados independente. Um parâmetro hyper é um valor que tem de especificar fora o procedimento de formação do modelo. Pressupostos sobre os valores de parâmetros hyper podem afetar a flexibilidade e a precisão do modelo. Árvores de decisões tem hyper-parâmetros, por exemplo, como a profundidade pretendida e o número de leaves na árvore. Tem de definir um prazo de penalidade misclassification para uma máquina de vetor de suporte (SVM).

Uma forma comum de efetuar a otimização de parâmetro hyper consiste em utilizar uma pesquisa de grelha, também designado por um **paramétrico parâmetro**. Numa pesquisa grelha, uma pesquisa exaustiva é efetuada através de valores de um subconjunto especificado o parâmetro hyper do espaço de para um algoritmo de aprendizagem. Validação cruzada pode fornecer uma métrica de desempenho para ordenar os resultados ideal produzidos pelo algoritmo de pesquisa de grelha. Se utilizar varrimento de hyper-parâmetro de validação cruzada, pode ajudar a problemas de limite, como overfitting um modelo de dados de preparação. Desta forma, o modelo mantém a capacidade para aplicar o conjunto de dados a partir da qual os dados de formação foi extraídos geral.

### <a name="optimize-a-linear-regression-model-with-hyper-parameter-sweeping"></a>Otimizar um modelo de regressão linear com hyper-parâmetro varrimento
Em seguida, dividir dados em conjuntos de formação e validação, utilize hyper-parâmetro varrimento num conjunto de formação para otimizar o modelo e avaliar num conjunto de validação (regressão linear).

    # RECORD THE START TIME
    val starttime = Calendar.getInstance().getTime()

    # RENAME `tip_amount` AS A LABEL
    val OneHotTRAINLabeled = OneHotTRAIN.select("tip_amount","features").withColumnRenamed(existingName="tip_amount",newName="label")
    val OneHotTESTLabeled = OneHotTEST.select("tip_amount","features").withColumnRenamed(existingName="tip_amount",newName="label")
    OneHotTRAINLabeled.cache()
    OneHotTESTLabeled.cache()

    # DEFINE THE ESTIMATOR FUNCTION: `THE LinearRegression()` FUNCTION
    val lr = new LinearRegression().setLabelCol("label").setFeaturesCol("features").setMaxIter(10)

    # DEFINE THE PARAMETER GRID
    val paramGrid = new ParamGridBuilder().addGrid(lr.regParam, Array(0.1, 0.01, 0.001)).addGrid(lr.fitIntercept).addGrid(lr.elasticNetParam, Array(0.1, 0.5, 0.9)).build()

    # DEFINE THE PIPELINE WITH A TRAIN/TEST VALIDATION SPLIT (75% IN THE TRAINING SET), AND THEN THE SPECIFY ESTIMATOR, EVALUATOR, AND PARAMETER GRID
    val trainPct = 0.75
    val trainValidationSplit = new TrainValidationSplit().setEstimator(lr).setEvaluator(new RegressionEvaluator).setEstimatorParamMaps(paramGrid).setTrainRatio(trainPct)

    # RUN THE TRAIN VALIDATION SPLIT AND CHOOSE THE BEST SET OF PARAMETERS
    val model = trainValidationSplit.fit(OneHotTRAINLabeled)

    # MAKE PREDICTIONS ON THE TEST DATA BY USING THE MODEL WITH THE COMBINATION OF PARAMETERS THAT PERFORMS THE BEST
    val testResults = model.transform(OneHotTESTLabeled).select("label", "prediction")

    # COMPUTE TEST SET R2
    val evaluator = new RegressionEvaluator().setLabelCol("label").setPredictionCol("prediction").setMetricName("r2")
    val Test_R2 = evaluator.evaluate(testResults)

    # GET THE TIME TO RUN THE CELL
    val endtime = Calendar.getInstance().getTime()
    val elapsedtime =  ((endtime.getTime() - starttime.getTime())/1000).toString;
    println("Time taken to run the above cell: " + elapsedtime + " seconds.");

    println("Test R-sqr is: " + Test_R2);


**Saída:**

Teste R-sqr é: 0.6226484708501209

### <a name="optimize-the-binary-classification-model-by-using-cross-validation-and-hyper-parameter-sweeping"></a>Otimizar o modelo de classificação binária através da utilização de varrimento de validação cruzada e hyper parâmetro
Esta secção mostra como otimizar um modelo de classificação binária através da utilização de varrimento de validação cruzada e hyper parâmetro. Esta opção utiliza o Spark ML `CrossValidator` função.

    # RECORD THE START TIME
    val starttime = Calendar.getInstance().getTime()

    # CREATE DATA FRAMES WITH PROPERLY LABELED COLUMNS TO USE WITH THE TRAIN AND TEST SPLIT
    val indexedTRAINwithCatFeatBinTargetRF = indexedTRAINwithCatFeatBinTarget.select("labelBin","featuresCat").withColumnRenamed(existingName="labelBin",newName="label").withColumnRenamed(existingName="featuresCat",newName="features")
    val indexedTESTwithCatFeatBinTargetRF = indexedTESTwithCatFeatBinTarget.select("labelBin","featuresCat").withColumnRenamed(existingName="labelBin",newName="label").withColumnRenamed(existingName="featuresCat",newName="features")
    indexedTRAINwithCatFeatBinTargetRF.cache()
    indexedTESTwithCatFeatBinTargetRF.cache()

    # DEFINE THE ESTIMATOR FUNCTION
    val rf = new RandomForestClassifier().setLabelCol("label").setFeaturesCol("features").setImpurity("gini").setSeed(1234).setFeatureSubsetStrategy("auto").setMaxBins(32)

    # DEFINE THE PARAMETER GRID
    val paramGrid = new ParamGridBuilder().addGrid(rf.maxDepth, Array(4,8)).addGrid(rf.numTrees, Array(5,10)).addGrid(rf.minInstancesPerNode, Array(100,300)).build()

    # SPECIFY THE NUMBER OF FOLDS
    val numFolds = 3

    # DEFINE THE TRAIN/TEST VALIDATION SPLIT (75% IN THE TRAINING SET)
    val CrossValidator = new CrossValidator().setEstimator(rf).setEvaluator(new BinaryClassificationEvaluator).setEstimatorParamMaps(paramGrid).setNumFolds(numFolds)

    # RUN THE TRAIN VALIDATION SPLIT AND CHOOSE THE BEST SET OF PARAMETERS
    val model = CrossValidator.fit(indexedTRAINwithCatFeatBinTargetRF)

    # MAKE PREDICTIONS ON THE TEST DATA BY USING THE MODEL WITH THE COMBINATION OF PARAMETERS THAT PERFORMS THE BEST
    val testResults = model.transform(indexedTESTwithCatFeatBinTargetRF).select("label", "prediction")

    # COMPUTE THE TEST F1 SCORE
    val evaluator = new MulticlassClassificationEvaluator().setLabelCol("label").setPredictionCol("prediction").setMetricName("f1")
    val Test_f1Score = evaluator.evaluate(testResults)

    # GET THE TIME TO RUN THE CELL
    val endtime = Calendar.getInstance().getTime()
    val elapsedtime =  ((endtime.getTime() - starttime.getTime())/1000).toString;
    println("Time taken to run the above cell: " + elapsedtime + " seconds.");


**Saída:**

Tempo para executar a célula: 33 segundos.

### <a name="optimize-the-linear-regression-model-by-using-custom-cross-validation-and-parameter-sweeping-code"></a>Otimizar o modelo de regressão, utilizando o código de validação cruzada e varrimento de parâmetro personalizado
Em seguida, otimizar o modelo utilizando código personalizado e identificar os melhores parâmetros de modelo ao utilizar o critério de maior precisão. Em seguida, crie o modelo final, avalie o modelo em dados de teste e guarde o modelo no Blob storage. Por fim, carregar o modelo, dados de teste de pontuação e avaliar exatidão.

    # RECORD THE START TIME
    val starttime = Calendar.getInstance().getTime()

    # DEFINE THE PARAMETER GRID AND THE NUMBER OF FOLDS
    val paramGrid = new ParamGridBuilder().addGrid(rf.maxDepth, Array(5,10)).addGrid(rf.numTrees, Array(10,25,50)).build()

    val nFolds = 3
    val numModels = paramGrid.size
    val numParamsinGrid = 2

    # SPECIFY THE NUMBER OF CATEGORIES FOR CATEGORICAL VARIABLES
    val categoricalFeaturesInfo = Map[Int, Int]((0,2),(1,2),(2,6),(3,4))

    var maxDepth = -1
    var numTrees = -1
    var param = ""
    var paramval = -1
    var validateLB = -1.0
    var validateUB = -1.0
    val h = 1.0 / nFolds;
    val RMSE  = Array.fill(numModels)(0.0)

    # CREATE K-FOLDS
    val splits = MLUtils.kFold(indexedTRAINbinary, numFolds = nFolds, seed=1234)


    # LOOP THROUGH K-FOLDS AND THE PARAMETER GRID TO GET AND IDENTIFY THE BEST PARAMETER SET BY LEVEL OF ACCURACY
    for (i <- 0 to (nFolds-1)) {
        validateLB = i * h
        validateUB = (i + 1) * h
        val validationCV = trainData.filter($"rand" >= validateLB  && $"rand" < validateUB)
        val trainCV = trainData.filter($"rand" < validateLB  || $"rand" >= validateUB)
        val validationLabPt = validationCV.rdd.map(r => LabeledPoint(r.getDouble(targetIndRegression(0).toInt), Vectors.dense(featuresIndIndex.map(r.getDouble(_)).toArray)));
        val trainCVLabPt = trainCV.rdd.map(r => LabeledPoint(r.getDouble(targetIndRegression(0).toInt), Vectors.dense(featuresIndIndex.map(r.getDouble(_)).toArray)));
        validationLabPt.cache()
        trainCVLabPt.cache()

        for (nParamSets <- 0 to (numModels-1)) {
            for (nParams <- 0 to (numParamsinGrid-1)) {
                param = paramGrid(nParamSets).toSeq(nParams).param.toString.split("__")(1)
                paramval = paramGrid(nParamSets).toSeq(nParams).value.toString.toInt
                if (param == "maxDepth") {maxDepth = paramval}
                if (param == "numTrees") {numTrees = paramval}
            }
            val rfModel = RandomForest.trainRegressor(trainCVLabPt, categoricalFeaturesInfo=categoricalFeaturesInfo,
                                                      numTrees=numTrees, maxDepth=maxDepth,
                                                      featureSubsetStrategy="auto",impurity="variance", maxBins=32)
            val labelAndPreds = validationLabPt.map { point =>
                                                     val prediction = rfModel.predict(point.features)
                                                     ( prediction, point.label )
                                                    }
            val validMetrics = new RegressionMetrics(labelAndPreds)
            val rmse = validMetrics.rootMeanSquaredError
            RMSE(nParamSets) += rmse
        }
        validationLabPt.unpersist();
        trainCVLabPt.unpersist();
    }
    val minRMSEindex = RMSE.indexOf(RMSE.min)

    # GET THE BEST PARAMETERS FROM A CROSS-VALIDATION AND PARAMETER SWEEP
    var best_maxDepth = -1
    var best_numTrees = -1
    for (nParams <- 0 to (numParamsinGrid-1)) {
        param = paramGrid(minRMSEindex).toSeq(nParams).param.toString.split("__")(1)
        paramval = paramGrid(minRMSEindex).toSeq(nParams).value.toString.toInt
        if (param == "maxDepth") {best_maxDepth = paramval}
        if (param == "numTrees") {best_numTrees = paramval}
    }

    # CREATE THE BEST MODEL WITH THE BEST PARAMETERS AND A FULL TRAINING DATA SET
    val best_rfModel = RandomForest.trainRegressor(indexedTRAINreg, categoricalFeaturesInfo=categoricalFeaturesInfo,
                                                      numTrees=best_numTrees, maxDepth=best_maxDepth,
                                                      featureSubsetStrategy="auto",impurity="variance", maxBins=32)

    # SAVE THE BEST RANDOM FOREST MODEL IN BLOB STORAGE
    val datestamp = Calendar.getInstance().getTime().toString.replaceAll(" ", ".").replaceAll(":", "_");
    val modelName = "BestCV_RF_Regression__"
    val filename = modelDir.concat(modelName).concat(datestamp)
    best_rfModel.save(sc, filename);

    # PREDICT ON THE TRAINING SET WITH THE BEST MODEL AND THEN EVALUATE
    val labelAndPreds = indexedTESTreg.map { point =>
                                            val prediction = best_rfModel.predict(point.features)
                                            ( prediction, point.label )
                                           }

    val test_rmse = new RegressionMetrics(labelAndPreds).rootMeanSquaredError
    val test_rsqr = new RegressionMetrics(labelAndPreds).r2

    # GET THE TIME TO RUN THE CELL
    val endtime = Calendar.getInstance().getTime()
    val elapsedtime =  ((endtime.getTime() - starttime.getTime())/1000).toString;
    println("Time taken to run the above cell: " + elapsedtime + " seconds.");


    # LOAD THE MODEL
    val savedRFModel = RandomForestModel.load(sc, filename)

    val labelAndPreds = indexedTESTreg.map { point =>
                                            val prediction = savedRFModel.predict(point.features)
                                            ( prediction, point.label )
                                           }
    # TEST THE MODEL
    val test_rmse = new RegressionMetrics(labelAndPreds).rootMeanSquaredError
    val test_rsqr = new RegressionMetrics(labelAndPreds).r2


**Saída:**

Tempo para executar a célula: 61 segundos.

## <a name="consume-spark-built-machine-learning-models-automatically-with-scala"></a>Consumir os modelos de aprendizagem máquina incorporadas do Spark automaticamente com o Scala
Para obter uma descrição geral de tópicos que descrevem as tarefas que compõem o processo de ciência de dados no Azure, consulte [o processo de ciência de dados de equipa](http://aka.ms/datascienceprocess).

[Instruções do processo de ciência de dados de equipa](walkthroughs.md) descreve outras instruções ponto-a-ponto que demonstram os passos no processo de ciência de dados de agrupamento para cenários específicos. As instruções também mostram como combinar as ferramentas de nuvem e no local e serviços para um fluxo de trabalho ou pipeline para criar uma aplicação inteligente.

[Pontuar modelos de incorporadas do Spark machine learning](spark-model-consumption.md) mostra-lhe como utilizar o Scala código para carregar e Pontuar novos conjuntos de dados com modelos de machine learning incorporada no Spark e guardou no Blob storage do Azure automaticamente. Pode seguir as instruções fornecidas não existe e simplesmente substitua o código de Python com o Scala código neste artigo para consumo automatizado.

