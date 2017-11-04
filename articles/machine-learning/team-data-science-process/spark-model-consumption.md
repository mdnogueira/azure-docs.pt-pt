---
title: Operacionalizar modelos de incorporadas do Spark machine learning | Microsoft Docs
description: Como carregar e Pontuar modelos learning armazenados no armazenamento de BLOBs de Azure (WASB) com o Python.
services: machine-learning
documentationcenter: 
author: bradsev
manager: jhubbard
editor: cgronlun
ms.assetid: 626305a2-0abf-4642-afb0-dad0f6bd24e9
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/15/2017
ms.author: deguhath;bradsev;gokuma
ms.openlocfilehash: 9ff633b4543fbc537ffdb721756706e8de5e8e88
ms.sourcegitcommit: f8437edf5de144b40aed00af5c52a20e35d10ba1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/03/2017
---
# <a name="operationalize-spark-built-machine-learning-models"></a>Operacionalizar modelos de incorporadas do Spark machine learning
[!INCLUDE [machine-learning-spark-modeling](../../../includes/machine-learning-spark-modeling.md)]

Este tópico mostra como operacionalizar um modelo de aprendizagem guardado (ML) com o Python nos clusters do HDInsight Spark. Descreve como carregar modelos de machine learning que foram criados com o Spark MLlib e armazenados no armazenamento de Blobs do Azure (WASB) e como pontuá-los com conjuntos de dados também foram armazenados na WASB. Mostra como pré-processar os dados de entrada, funcionalidades, utilizando as funções de codificação e indexação no MLlib toolkit de transformação e como criar um objeto de dados do ponto de identificados que pode ser utilizado como entrada para classificação com os modelos de ML. Os modelos utilizados para classificação incluem regressão Linear, regressão logística da, aleatória de floresta de modelos e modelos de árvore os aumentos gradação.

## <a name="spark-clusters-and-jupyter-notebooks"></a>Clusters do Spark e blocos de notas do Jupyter
Passos de configuração e o código para operacionalizar um modelo de ML são fornecidos esta explicação passo a passo para utilizar um cluster do HDInsight Spark 1.6, bem como um cluster do Spark 2.0. O código para estes procedimentos também é fornecido em blocos de notas do Jupyter.

### <a name="notebook-for-spark-16"></a>Bloco de notas para Spark 1.6
O [pySpark-machine-learning-data-science-spark-model-consumption.ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Spark1.6/pySpark-machine-learning-data-science-spark-model-consumption.ipynb) bloco de notas do Jupyter mostra como operacionalizar um modelo guardado com o Python nos clusters do HDInsight. 

### <a name="notebook-for-spark-20"></a>Bloco de notas para Spark 2.0
Para modificar o bloco de notas do Jupyter para 1.6 Spark utilizar com um cluster do HDInsight Spark 2.0, substitua o ficheiro de código do Python com [este ficheiro](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/Python/Spark2.0_ConsumeRFCV_NYCReg.py). Este código mostra como consumir os modelos criados no Spark 2.0.


## <a name="prerequisites"></a>Pré-requisitos

1. Precisa de uma conta do Azure e um Spark 1.6 (ou Spark 2.0) cluster do HDInsight para concluir estas instruções. Consulte o [descrição geral de ciência de dados com o Spark no Azure HDInsight](spark-overview.md) para obter instruções sobre como satisfazer estes requisitos. Este tópico também contém uma descrição dos dados NYC 2013 Taxi utilizados aqui e obter instruções sobre como executar código a partir de um bloco de notas do Jupyter no cluster do Spark. 
2. Também tem de criar os modelos de machine learning para classificar aqui por trabalhar com o [exploração de dados e modelação com o Spark](spark-data-exploration-modeling.md) tópico para o cluster do Spark 1.6 ou os blocos de notas do Spark 2.0. 
3. Os blocos de notas do Spark 2.0 utilizam um conjunto de dados adicionais para a tarefa de classificação, conhecido companhia aérea no tempo distanciamento conjunto de dados de 2011 e 2012. Uma descrição dos blocos de notas e ligações para os mesmos são fornecidos no [Readme.md](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Readme.md) para o repositório do GitHub que contém-los. Além disso, o código aqui e em blocos de notas ligados é genérico e deve trabalhar em qualquer cluster do Spark. Se não estiver a utilizar o Spark do HDInsight, os passos de configuração e gestão de cluster podem ser ligeiramente diferentes das que é mostrado aqui. 

[!INCLUDE [delete-cluster-warning](../../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="setup-storage-locations-libraries-and-the-preset-spark-context"></a>Programa de configuração: localizações de armazenamento, bibliotecas e o contexto de Spark predefinido
O Spark é capaz de leitura e escrita para um Blob de armazenamento do Azure (WASB). Para que qualquer um dos seus dados existentes armazenados existe podem ser processados utilizando Spark e os resultados novamente armazenados na WASB.

Para guardar modelos ou ficheiros em WASB, o caminho tem de ser especificadas corretamente. O contentor predefinido ligado ao cluster do Spark pode ser referenciado utilizando um início de caminho com: *"wasb / / /"*. O exemplo de código seguinte especifica a localização dos dados a ser lido e o caminho para o diretório de armazenamento de modelo ao qual o resultado de modelo é guardado. 

### <a name="set-directory-paths-for-storage-locations-in-wasb"></a>Caminhos de diretório de conjunto para localizações de armazenamento no WASB
Modelos são guardados em: "wasb: / / / utilizador/remoteuser/NYCTaxi/modelos". Se este caminho não está definido corretamente, os modelos não estão carregados para classificação.

Os resultados classificados tem sido guardados numa: "wasb: / / / utilizador/remoteuser/NYCTaxi/ScoredResults". Se o caminho de pasta estiver incorreto, os resultados não são guardados nessa pasta.   

> [!NOTE]
> As localizações de caminho do ficheiro podem ser copiadas e coladas os marcadores de posição este código de saída da célula da última o **machine-learning-data-science-spark-data-exploration-modeling.ipynb** bloco de notas.   
> 
> 

Eis o código para definir caminhos de diretório: 

    # LOCATION OF DATA TO BE SCORED (TEST DATA)
    taxi_test_file_loc = "wasb://mllibwalkthroughs@cdspsparksamples.blob.core.windows.net/Data/NYCTaxi/JoinedTaxiTripFare.Point1Pct.Test.tsv";

    # SET THE MODEL STORAGE DIRECTORY PATH 
    # NOTE THE LAST BACKSLASH IN THIS PATH IS NEEDED
    modelDir = "wasb:///user/remoteuser/NYCTaxi/Models/" 

    # SET SCORDED RESULT DIRECTORY PATH
    # NOTE THE LAST BACKSLASH IN THIS PATH IS NEEDED
    scoredResultDir = "wasb:///user/remoteuser/NYCTaxi/ScoredResults/"; 

    # FILE LOCATIONS FOR THE MODELS TO BE SCORED
    logisticRegFileLoc = modelDir + "LogisticRegressionWithLBFGS_2016-04-1817_40_35.796789"
    linearRegFileLoc = modelDir + "LinearRegressionWithSGD_2016-04-1817_44_00.993832"
    randomForestClassificationFileLoc = modelDir + "RandomForestClassification_2016-04-1817_42_58.899412"
    randomForestRegFileLoc = modelDir + "RandomForestRegression_2016-04-1817_44_27.204734"
    BoostedTreeClassificationFileLoc = modelDir + "GradientBoostingTreeClassification_2016-04-1817_43_16.354770"
    BoostedTreeRegressionFileLoc = modelDir + "GradientBoostingTreeRegression_2016-04-1817_44_46.206262"

    # RECORD START TIME
    import datetime
    datetime.datetime.now()

**SAÍDA:**

DateTime.DateTime (2016, 4, 25, 23, 56, 19, 229403)

### <a name="import-libraries"></a>Bibliotecas de importação
Definir o contexto de spark e importar necessárias bibliotecas com o seguinte código

    #IMPORT LIBRARIES
    import pyspark
    from pyspark import SparkConf
    from pyspark import SparkContext
    from pyspark.sql import SQLContext
    import matplotlib
    import matplotlib.pyplot as plt
    from pyspark.sql import Row
    from pyspark.sql.functions import UserDefinedFunction
    from pyspark.sql.types import *
    import atexit
    from numpy import array
    import numpy as np
    import datetime


### <a name="preset-spark-context-and-pyspark-magics"></a>Contexto de Spark e PySpark magia da configuração predefinida
Os PySpark kernels que são fornecidos com blocos de notas do Jupyter têm um contexto predefinido. Por isso, não terá de definir o Spark ou Hive contextos explicitamente antes de começar a trabalhar com a aplicação estiver a desenvolver. Estes estão disponíveis para si por predefinição. Estes contextos são:

* SC - para Spark 
* sqlContext - para o ramo de registo

O kernel do PySpark fornece algumas predefinidas "magia", que são comandos especiais que pode chamar com % %. Existem dois esses comandos que são utilizados nestes exemplos de código.

* **% % local** especificado que o código em linhas subsequentes é executado localmente. Código tem de ser código Python válido.
* **% % -o SQL Server<variable name>** 
* Executa uma consulta do Hive contra o sqlContext. Se o parâmetro -o é transmitido, o resultado da consulta é continuado no % % contexto de Python local como uma dataframe Pandas.

Para obter mais informações sobre os kernels para blocos de notas do Jupyter e predefinidos "magics" que fornecem, consulte [clusters Kernels disponíveis para blocos de notas do Jupyter com o HDInsight Spark Linux no HDInsight](../../hdinsight/spark/apache-spark-jupyter-notebook-kernels.md).

## <a name="ingest-data-and-create-a-cleaned-data-frame"></a>Ingestão de dados e criar um intervalo de dados apagado
Esta secção contém o código para uma série de tarefas necessárias para incorporar os dados para classificar. Ler um exemplo de 0.1% associados do taxi viagem e fare ficheiro (armazenado como um ficheiro de .tsv), formato os dados e, em seguida, cria um período de limpar dados.

Os ficheiros de viagem e fare taxi foram associados a um com base no procedimento fornecido a: [o processo de ciência de dados de equipa em ação: com clusters do HDInsight Hadoop](hive-walkthrough.md) tópico.

    # INGEST DATA AND CREATE A CLEANED DATA FRAME

    # RECORD START TIME
    timestart = datetime.datetime.now()

    # IMPORT FILE FROM PUBLIC BLOB
    taxi_test_file = sc.textFile(taxi_test_file_loc)

    # GET SCHEMA OF THE FILE FROM HEADER
    taxi_header = taxi_test_file.filter(lambda l: "medallion" in l)

    # PARSE FIELDS AND CONVERT DATA TYPE FOR SOME FIELDS
    taxi_temp = taxi_test_file.subtract(taxi_header).map(lambda k: k.split("\t"))\
            .map(lambda p: (p[0],p[1],p[2],p[3],p[4],p[5],p[6],int(p[7]),int(p[8]),int(p[9]),int(p[10]),
                            float(p[11]),float(p[12]),p[13],p[14],p[15],p[16],p[17],p[18],float(p[19]),
                            float(p[20]),float(p[21]),float(p[22]),float(p[23]),float(p[24]),int(p[25]),int(p[26])))

    # GET SCHEMA OF THE FILE FROM HEADER
    schema_string = taxi_test_file.first()
    fields = [StructField(field_name, StringType(), True) for field_name in schema_string.split('\t')]
    fields[7].dataType = IntegerType() #Pickup hour
    fields[8].dataType = IntegerType() # Pickup week
    fields[9].dataType = IntegerType() # Weekday
    fields[10].dataType = IntegerType() # Passenger count
    fields[11].dataType = FloatType() # Trip time in secs
    fields[12].dataType = FloatType() # Trip distance
    fields[19].dataType = FloatType() # Fare amount
    fields[20].dataType = FloatType() # Surcharge
    fields[21].dataType = FloatType() # Mta_tax
    fields[22].dataType = FloatType() # Tip amount
    fields[23].dataType = FloatType() # Tolls amount
    fields[24].dataType = FloatType() # Total amount
    fields[25].dataType = IntegerType() # Tipped or not
    fields[26].dataType = IntegerType() # Tip class
    taxi_schema = StructType(fields)

    # CREATE DATA FRAME
    taxi_df_test = sqlContext.createDataFrame(taxi_temp, taxi_schema)

    # CREATE A CLEANED DATA-FRAME BY DROPPING SOME UN-NECESSARY COLUMNS & FILTERING FOR UNDESIRED VALUES OR OUTLIERS
    taxi_df_test_cleaned = taxi_df_test.drop('medallion').drop('hack_license').drop('store_and_fwd_flag').drop('pickup_datetime')\
        .drop('dropoff_datetime').drop('pickup_longitude').drop('pickup_latitude').drop('dropoff_latitude')\
        .drop('dropoff_longitude').drop('tip_class').drop('total_amount').drop('tolls_amount').drop('mta_tax')\
        .drop('direct_distance').drop('surcharge')\
        .filter("passenger_count > 0 and passenger_count < 8 AND payment_type in ('CSH', 'CRD') AND tip_amount >= 0 AND tip_amount < 30 AND fare_amount >= 1 AND fare_amount < 150 AND trip_distance > 0 AND trip_distance < 100 AND trip_time_in_secs > 30 AND trip_time_in_secs < 7200" )

    # CACHE DATA-FRAME IN MEMORY & MATERIALIZE DF IN MEMORY
    taxi_df_test_cleaned.cache()
    taxi_df_test_cleaned.count()

    # REGISTER DATA-FRAME AS A TEMP-TABLE IN SQL-CONTEXT
    taxi_df_test_cleaned.registerTempTable("taxi_test")

    # PRINT HOW MUCH TIME IT TOOK TO RUN THE CELL
    timeend = datetime.datetime.now()
    timedelta = round((timeend-timestart).total_seconds(), 2) 
    print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 

**SAÍDA:**

Tempo decorrido para executar acima célula: 46.37 segundos

## <a name="prepare-data-for-scoring-in-spark"></a>Preparar os dados para classificação no Spark
Esta secção mostra como de índice, codificar e dimensionar funcionalidades categórico para prepará-los para utilização num algoritmos MLlib supervisionado learning para classificação e regressão.

### <a name="feature-transformation-index-and-encode-categorical-features-for-input-into-models-for-scoring"></a>Funcionalidade de transformação: índice e codificar categórico funcionalidades para a entrada em modelos de classificação
Esta secção mostra como índice dados categórico utilizando um `StringIndexer` e codificar funcionalidades com `OneHotEncoder` de entrada para os modelos.

O [StringIndexer](http://spark.apache.org/docs/latest/ml-features.html#stringindexer) codifica uma coluna de cadeia de etiquetas a uma coluna de índices de etiqueta. Os índices ordenados frequências de etiqueta. 

O [OneHotEncoder](http://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.OneHotEncoder.html#sklearn.preprocessing.OneHotEncoder) uma coluna de índices de etiqueta é mapeado para uma coluna de vetores de binários, no máximo um único um valor. Esta codificação permite que os algoritmos que esperam contínuas funcionalidades importantes, como regressão logística da, a aplicar às funcionalidades categórico.

    #INDEX AND ONE-HOT ENCODE CATEGORICAL FEATURES

    # RECORD START TIME
    timestart = datetime.datetime.now()

    # LOAD PYSPARK LIBRARIES
    from pyspark.ml.feature import OneHotEncoder, StringIndexer, VectorAssembler, VectorIndexer

    # CREATE FOUR BUCKETS FOR TRAFFIC TIMES
    sqlStatement = """
        SELECT *,
        CASE
         WHEN (pickup_hour <= 6 OR pickup_hour >= 20) THEN "Night" 
         WHEN (pickup_hour >= 7 AND pickup_hour <= 10) THEN "AMRush" 
         WHEN (pickup_hour >= 11 AND pickup_hour <= 15) THEN "Afternoon"
         WHEN (pickup_hour >= 16 AND pickup_hour <= 19) THEN "PMRush"
        END as TrafficTimeBins
        FROM taxi_test 
    """
    taxi_df_test_with_newFeatures = sqlContext.sql(sqlStatement)

    # CACHE DATA-FRAME IN MEMORY & MATERIALIZE DF IN MEMORY
    taxi_df_test_with_newFeatures.cache()
    taxi_df_test_with_newFeatures.count()

    # INDEX AND ONE-HOT ENCODING
    stringIndexer = StringIndexer(inputCol="vendor_id", outputCol="vendorIndex")
    model = stringIndexer.fit(taxi_df_test_with_newFeatures) # Input data-frame is the cleaned one from above
    indexed = model.transform(taxi_df_test_with_newFeatures)
    encoder = OneHotEncoder(dropLast=False, inputCol="vendorIndex", outputCol="vendorVec")
    encoded1 = encoder.transform(indexed)

    # INDEX AND ENCODE RATE_CODE
    stringIndexer = StringIndexer(inputCol="rate_code", outputCol="rateIndex")
    model = stringIndexer.fit(encoded1)
    indexed = model.transform(encoded1)
    encoder = OneHotEncoder(dropLast=False, inputCol="rateIndex", outputCol="rateVec")
    encoded2 = encoder.transform(indexed)

    # INDEX AND ENCODE PAYMENT_TYPE
    stringIndexer = StringIndexer(inputCol="payment_type", outputCol="paymentIndex")
    model = stringIndexer.fit(encoded2)
    indexed = model.transform(encoded2)
    encoder = OneHotEncoder(dropLast=False, inputCol="paymentIndex", outputCol="paymentVec")
    encoded3 = encoder.transform(indexed)

    # INDEX AND ENCODE TRAFFIC TIME BINS
    stringIndexer = StringIndexer(inputCol="TrafficTimeBins", outputCol="TrafficTimeBinsIndex")
    model = stringIndexer.fit(encoded3)
    indexed = model.transform(encoded3)
    encoder = OneHotEncoder(dropLast=False, inputCol="TrafficTimeBinsIndex", outputCol="TrafficTimeBinsVec")
    encodedFinal = encoder.transform(indexed)

    # PRINT HOW MUCH TIME IT TOOK TO RUN THE CELL
    timeend = datetime.datetime.now()
    timedelta = round((timeend-timestart).total_seconds(), 2) 
    print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 

**SAÍDA:**

Tempo decorrido para executar acima célula: 5.37 segundos

### <a name="create-rdd-objects-with-feature-arrays-for-input-into-models"></a>Criar objetos RDD com matrizes de funcionalidade para a entrada em modelos
Esta secção contém o código que mostra como dados de texto categórico como um objeto RDD de índice e frequente de um codificar pelo que pode ser utilizado para dar formação e testar regressão logística da MLlib e modelos de árvore. Os dados indexados são armazenados no [Resiliente distribuídas conjunto de dados (RDD)](http://spark.apache.org/docs/latest/api/java/org/apache/spark/rdd/RDD.html) objetos. Estas são a abstração básica no Spark. Um objeto RDD representa uma coleção particionada, imutável dos elementos que pode ser manipulado em paralelo com o Spark.

Também contém código que mostra como dimensionar dados com o `StandardScalar` fornecida pelo MLlib para utilização num regressão linear com Stochastic gradação descendente (SGD), um algoritmo mais popular para uma vasta gama de modelos de machine learning de preparação. O [StandardScaler](https://spark.apache.org/docs/latest/api/python/pyspark.mllib.html#pyspark.mllib.feature.StandardScaler) é utilizado para as funcionalidades a variância de unidade de escala. Funcionalidade de dimensionamento, também conhecido como normalização de dados, forma, assegura que funcionalidades com valores amplamente disbursed são não fornecido excessivo pesar na função objetivo. 

    # CREATE RDD OBJECTS WITH FEATURE ARRAYS FOR INPUT INTO MODELS

    # RECORD START TIME
    timestart = datetime.datetime.now()

    # IMPORT LIBRARIES
    from pyspark.mllib.linalg import Vectors
    from pyspark.mllib.feature import StandardScaler, StandardScalerModel
    from pyspark.mllib.util import MLUtils
    from numpy import array

    # INDEXING CATEGORICAL TEXT FEATURES FOR INPUT INTO TREE-BASED MODELS
    def parseRowIndexingBinary(line):
        features = np.array([line.paymentIndex, line.vendorIndex, line.rateIndex, line.TrafficTimeBinsIndex,
                             line.pickup_hour, line.weekday, line.passenger_count, line.trip_time_in_secs, 
                             line.trip_distance, line.fare_amount])
        return  features

    # ONE-HOT ENCODING OF CATEGORICAL TEXT FEATURES FOR INPUT INTO LOGISTIC RERESSION MODELS
    def parseRowOneHotBinary(line):
        features = np.concatenate((np.array([line.pickup_hour, line.weekday, line.passenger_count,
                                            line.trip_time_in_secs, line.trip_distance, line.fare_amount]), 
                                            line.vendorVec.toArray(), line.rateVec.toArray(), 
                                            line.paymentVec.toArray(), line.TrafficTimeBinsVec.toArray()), axis=0)
        return  features

    # ONE-HOT ENCODING OF CATEGORICAL TEXT FEATURES FOR INPUT INTO TREE-BASED MODELS
    def parseRowIndexingRegression(line):
        features = np.array([line.paymentIndex, line.vendorIndex, line.rateIndex, line.TrafficTimeBinsIndex, 
                             line.pickup_hour, line.weekday, line.passenger_count, line.trip_time_in_secs, 
                             line.trip_distance, line.fare_amount])
        return  features

    # INDEXING CATEGORICAL TEXT FEATURES FOR INPUT INTO LINEAR REGRESSION MODELS
    def parseRowOneHotRegression(line):
        features = np.concatenate((np.array([line.pickup_hour, line.weekday, line.passenger_count,
                                            line.trip_time_in_secs, line.trip_distance, line.fare_amount]), 
                                            line.vendorVec.toArray(), line.rateVec.toArray(), 
                                            line.paymentVec.toArray(), line.TrafficTimeBinsVec.toArray()), axis=0)
        return  features

    # FOR BINARY CLASSIFICATION TRAINING AND TESTING
    indexedTESTbinary = encodedFinal.map(parseRowIndexingBinary)
    oneHotTESTbinary = encodedFinal.map(parseRowOneHotBinary)

    # FOR REGRESSION CLASSIFICATION TRAINING AND TESTING
    indexedTESTreg = encodedFinal.map(parseRowIndexingRegression)
    oneHotTESTreg = encodedFinal.map(parseRowOneHotRegression)

    # SCALING FEATURES FOR LINEARREGRESSIONWITHSGD MODEL
    scaler = StandardScaler(withMean=False, withStd=True).fit(oneHotTESTreg)
    oneHotTESTregScaled = scaler.transform(oneHotTESTreg)

    # CACHE RDDS IN MEMORY
    indexedTESTbinary.cache();
    oneHotTESTbinary.cache();
    indexedTESTreg.cache();
    oneHotTESTreg.cache();
    oneHotTESTregScaled.cache();

    # PRINT HOW MUCH TIME IT TOOK TO RUN THE CELL
    timeend = datetime.datetime.now()
    timedelta = round((timeend-timestart).total_seconds(), 2) 
    print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 

**SAÍDA:**

Tempo decorrido para executar acima célula: 11.72 segundos

## <a name="score-with-the-logistic-regression-model-and-save-output-to-blob"></a>Pontuar o modelo de regressão logística da e guardar a saída para o blob
O código nesta secção mostra como carregar um modelo de regressão logística da que foi guardado no blob storage do Azure e utilizá-la para prever ou não uma sugestão é paga em viagem um taxi, pontuação-lo com a métrica de classificação padrão e guarde-o e desenhar os resultados para o blob stora ge. Os resultados classificados são armazenados em objetos RDD. 

    # SCORE AND EVALUATE LOGISTIC REGRESSION MODEL

    # RECORD START TIME
    timestart = datetime.datetime.now()

    # IMPORT LIBRARIES
    from pyspark.mllib.classification import LogisticRegressionModel

    ## LOAD SAVED MODEL
    savedModel = LogisticRegressionModel.load(sc, logisticRegFileLoc)
    predictions = oneHotTESTbinary.map(lambda features: (float(savedModel.predict(features))))

    ## SAVE SCORED RESULTS (RDD) TO BLOB
    datestamp = unicode(datetime.datetime.now()).replace(' ','').replace(':','_');
    logisticregressionfilename = "LogisticRegressionWithLBFGS_" + datestamp + ".txt";
    dirfilename = scoredResultDir + logisticregressionfilename;
    predictions.saveAsTextFile(dirfilename)


    # PRINT HOW MUCH TIME IT TOOK TO RUN THE CELL
    timeend = datetime.datetime.now()
    timedelta = round((timeend-timestart).total_seconds(), 2) 
    print "Time taken to execute above cell: " + str(timedelta) + " seconds";

**SAÍDA:**

Tempo decorrido para executar acima célula: 19.22 segundos

## <a name="score-a-linear-regression-model"></a>Um modelo de regressão Linear de pontuação
Utilizámos [LinearRegressionWithSGD](https://spark.apache.org/docs/latest/api/python/pyspark.mllib.html#pyspark.mllib.regression.LinearRegressionWithSGD) para preparar um modelo de regressão linear utilizando Stochastic gradação descendente (SGD) para a otimização para prever a quantidade de sugestão paga. 

O código nesta secção mostra como carregar um modelo de regressão Linear do armazenamento de Blobs do Azure, utilizando variáveis de dimensionado de pontuação e, em seguida, guardar os resultados novamente para o blob.

    #SCORE LINEAR REGRESSION MODEL

    # RECORD START TIME
    timestart = datetime.datetime.now()

    #LOAD LIBRARIES
    from pyspark.mllib.regression import LinearRegressionWithSGD, LinearRegressionModel

    # LOAD MODEL AND SCORE USING ** SCALED VARIABLES **
    savedModel = LinearRegressionModel.load(sc, linearRegFileLoc)
    predictions = oneHotTESTregScaled.map(lambda features: (float(savedModel.predict(features))))

    # SAVE RESULTS
    datestamp = unicode(datetime.datetime.now()).replace(' ','').replace(':','_');
    linearregressionfilename = "LinearRegressionWithSGD_" + datestamp;
    dirfilename = scoredResultDir + linearregressionfilename;
    predictions.saveAsTextFile(dirfilename)

    # PRINT HOW MUCH TIME IT TOOK TO RUN THE CELL
    timeend = datetime.datetime.now()
    timedelta = round((timeend-timestart).total_seconds(), 2) 
    print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 


**SAÍDA:**

Tempo decorrido para executar acima célula: 16.63 segundos

## <a name="score-classification-and-regression-random-forest-models"></a>Classificação e regressão aleatório floresta os modelos de pontuação
O código nesta secção mostra como carregar a classificação guardada e modelos de floresta aleatório regressão guardados no armazenamento de Blobs do Azure, Pontuar o seu desempenho com classificador padrão e medidas de regressão e, em seguida, guardar os resultados para o blob storage.

[Florestas aleatórias](http://spark.apache.org/docs/latest/mllib-ensembles.html#Random-Forests) são ensembles de árvores de decisões.  Se combinam várias árvores de decisões para reduzir o risco de overfitting. Florestas aleatórias podem processar categórico funcionalidades, expandir a definição de classificação de várias classes, não necessitam de dimensionamento de funcionalidade e são capazes de captura não linearities e interações de funcionalidade. Florestas aleatórias são um do mais bem-sucedida modelos de machine learning para classificação e regressão.

[spark.mllib](http://spark.apache.org/mllib/) suporta florestas aleatórias para classificação do binária e várias classes e de regressão, utilizando as funcionalidades de categórico e contínuas. 

    # SCORE RANDOM FOREST MODELS FOR CLASSIFICATION AND REGRESSION

    # RECORD START TIME
    timestart = datetime.datetime.now()

    #IMPORT MLLIB LIBRARIES    
    from pyspark.mllib.tree import RandomForest, RandomForestModel


    # CLASSIFICATION: LOAD SAVED MODEL, SCORE AND SAVE RESULTS BACK TO BLOB
    savedModel = RandomForestModel.load(sc, randomForestClassificationFileLoc)
    predictions = savedModel.predict(indexedTESTbinary)

    # SAVE RESULTS
    datestamp = unicode(datetime.datetime.now()).replace(' ','').replace(':','_');
    rfclassificationfilename = "RandomForestClassification_" + datestamp + ".txt";
    dirfilename = scoredResultDir + rfclassificationfilename;
    predictions.saveAsTextFile(dirfilename)


    # REGRESSION: LOAD SAVED MODEL, SCORE AND SAVE RESULTS BACK TO BLOB
    savedModel = RandomForestModel.load(sc, randomForestRegFileLoc)
    predictions = savedModel.predict(indexedTESTreg)

    # SAVE RESULTS
    datestamp = unicode(datetime.datetime.now()).replace(' ','').replace(':','_');
    rfregressionfilename = "RandomForestRegression_" + datestamp + ".txt";
    dirfilename = scoredResultDir + rfregressionfilename;
    predictions.saveAsTextFile(dirfilename)

    # PRINT HOW MUCH TIME IT TOOK TO RUN THE CELL
    timeend = datetime.datetime.now()
    timedelta = round((timeend-timestart).total_seconds(), 2) 
    print "Time taken to execute above cell: " + str(timedelta) + " seconds";

**SAÍDA:**

Tempo decorrido para executar acima célula: 31.07 segundos

## <a name="score-classification-and-regression-gradient-boosting-tree-models"></a>Classificação e regressão gradação os aumentos árvore os modelos de pontuação
O código nesta secção mostra como carregar classificação regressão gradação os aumentos árvore modelos e do armazenamento de Blobs do Azure, Pontuar o seu desempenho com classificador padrão e medidas de regressão e, em seguida, guardar os resultados para o blob storage. 

**spark.mllib** suporta GBTs para classificação binária e de regressão, utilizando as funcionalidades de categórico e contínuas. 

[Gradação árvores os aumentos](http://spark.apache.org/docs/latest/ml-classification-regression.html#gradient-boosted-trees-gbts) (GBTs) são ensembles de árvores de decisões. GBTs preparar árvores de decisões iteratively para minimizar a uma função de perda. GBTs pode processar categórico funcionalidades, não necessitam de dimensionamento de funcionalidade e são capazes de captura não linearities e interações de funcionalidade. Também pode ser utilizados uma definição de classificação de várias classes.

    # SCORE GRADIENT BOOSTING TREE MODELS FOR CLASSIFICATION AND REGRESSION

    # RECORD START TIME
    timestart = datetime.datetime.now()

    #IMPORT MLLIB LIBRARIES
    from pyspark.mllib.tree import GradientBoostedTrees, GradientBoostedTreesModel

    # CLASSIFICATION: LOAD SAVED MODEL, SCORE AND SAVE RESULTS BACK TO BLOB

    #LOAD AND SCORE THE MODEL
    savedModel = GradientBoostedTreesModel.load(sc, BoostedTreeClassificationFileLoc)
    predictions = savedModel.predict(indexedTESTbinary)

    # SAVE RESULTS
    datestamp = unicode(datetime.datetime.now()).replace(' ','').replace(':','_');
    btclassificationfilename = "GradientBoostingTreeClassification_" + datestamp + ".txt";
    dirfilename = scoredResultDir + btclassificationfilename;
    predictions.saveAsTextFile(dirfilename)


    # REGRESSION: LOAD SAVED MODEL, SCORE AND SAVE RESULTS BACK TO BLOB

    # LOAD AND SCORE MODEL 
    savedModel = GradientBoostedTreesModel.load(sc, BoostedTreeRegressionFileLoc)
    predictions = savedModel.predict(indexedTESTreg)

    # SAVE RESULTS
    datestamp = unicode(datetime.datetime.now()).replace(' ','').replace(':','_');
    btregressionfilename = "GradientBoostingTreeRegression_" + datestamp + ".txt";
    dirfilename = scoredResultDir + btregressionfilename;
    predictions.saveAsTextFile(dirfilename)


    # PRINT HOW MUCH TIME IT TOOK TO RUN THE CELL
    timeend = datetime.datetime.now()
    timedelta = round((timeend-timestart).total_seconds(), 2) 
    print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 

**SAÍDA:**

Tempo decorrido para executar acima célula: 14.6 segundos

## <a name="clean-up-objects-from-memory-and-print-scored-file-locations"></a>Limpar objetos da memória e localizações de ficheiros classificadas de impressão
    # UNPERSIST OBJECTS CACHED IN MEMORY
    taxi_df_test_cleaned.unpersist()
    indexedTESTbinary.unpersist();
    oneHotTESTbinary.unpersist();
    indexedTESTreg.unpersist();
    oneHotTESTreg.unpersist();
    oneHotTESTregScaled.unpersist();


    # PRINT OUT PATH TO SCORED OUTPUT FILES
    print "logisticRegFileLoc: " + logisticregressionfilename;
    print "linearRegFileLoc: " + linearregressionfilename;
    print "randomForestClassificationFileLoc: " + rfclassificationfilename;
    print "randomForestRegFileLoc: " + rfregressionfilename;
    print "BoostedTreeClassificationFileLoc: " + btclassificationfilename;
    print "BoostedTreeRegressionFileLoc: " + btregressionfilename;


**SAÍDA:**

logisticRegFileLoc: LogisticRegressionWithLBFGS_2016-05-0317_22_38.953814.txt

linearRegFileLoc: LinearRegressionWithSGD_2016-05-0317_22_58.878949

randomForestClassificationFileLoc: RandomForestClassification_2016-05-0317_23_15.939247.txt

randomForestRegFileLoc: RandomForestRegression_2016-05-0317_23_31.459140.txt

BoostedTreeClassificationFileLoc: GradientBoostingTreeClassification_2016-05-0317_23_49.648334.txt

BoostedTreeRegressionFileLoc: GradientBoostingTreeRegression_2016-05-0317_23_56.860740.txt

## <a name="consume-spark-models-through-a-web-interface"></a>Consumir Spark modelos através de uma interface web
O Spark fornece um mecanismo remotamente submeter tarefas de lote ou consultas interativas através de uma interface REST com um componente denominado Livy. O Livy está ativado por predefinição no seu cluster do HDInsight Spark. Para obter mais informações sobre o Livy, consulte: [tarefas submeter Spark remotamente com o Livy](../../hdinsight/spark/apache-spark-livy-rest-interface.md). 

Pode utilizar o Livy remotamente submeter uma tarefa de lote pontuações um ficheiro que é armazenado num blob do Azure e, em seguida, escreve os resultados para o blob de outro. Para tal, carregar o script de Python do  
[GitHub](https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/Spark/Python/ConsumeGBNYCReg.py) para o blob do cluster do Spark. Pode utilizar uma ferramenta como o **Explorador de armazenamento do Microsoft Azure** ou **AzCopy** para copiar o script para o blob de cluster. No nosso caso, iremos carregado o script para ***wasb:///example/python/ConsumeGBNYCReg.py***.   

> [!NOTE]
> As chaves de acesso que é necessário pode ser encontrado no portal para a conta de armazenamento associado ao cluster do Spark. 
> 
> 

Assim que seja carregado nesta localização, este script é executado dentro do cluster do Spark num contexto distribuído. É carrega o modelo e executado predições ficheiros de entrada com base no modelo.  

Pode invocar este script remotamente ao efetuar um pedido HTTPS/REST simple no Livy.  Eis um comando curl para construir o pedido HTTP para invocar o script de Python remotamente. Substitua os valores adequados para o cluster do Spark CLUSTERLOGIN, CLUSTERPASSWORD, CLUSTERNAME.

    # CURL COMMAND TO INVOKE PYTHON SCRIPT WITH HTTP REQUEST

    curl -k --user "CLUSTERLOGIN:CLUSTERPASSWORD" -X POST --data "{\"file\": \"wasb:///example/python/ConsumeGBNYCReg.py\"}" -H "Content-Type: application/json" https://CLUSTERNAME.azurehdinsight.net/livy/batches

Pode utilizar qualquer idioma no sistema remoto para invocar a tarefa de Spark através de Livy ao efetuar uma chamada HTTPS simple com a autenticação básica.   

> [!NOTE]
> Seria conveniente utilizar a biblioteca de pedidos de Python quando efectuar esta chamada HTTP, mas não está atualmente instalado por predefinição nas funções do Azure. Para que bibliotecas HTTP mais antigas são utilizadas em vez disso.   
> 
> 

Eis o código do Python para a chamada HTTP:

    #MAKE AN HTTPS CALL ON LIVY. 

    import os

    # OLDER HTTP LIBRARIES USED HERE INSTEAD OF THE REQUEST LIBRARY AS THEY ARE AVAILBLE BY DEFAULT
    import httplib, urllib, base64

    # REPLACE VALUE WITH ONES FOR YOUR SPARK CLUSTER
    host = '<spark cluster name>.azurehdinsight.net:443'
    username='<username>'
    password='<password>'

    #AUTHORIZATION
    conn = httplib.HTTPSConnection(host)
    auth = base64.encodestring('%s:%s' % (username, password)).replace('\n', '')
    headers = {'Content-Type': 'application/json', 'Authorization': 'Basic %s' % auth}

    # SPECIFY THE PYTHON SCRIPT TO RUN ON THE SPARK CLUSTER
    # IN THE FILE PARAMETER OF THE JSON POST REQUEST BODY
    r=conn.request("POST", '/livy/batches', '{"file": "wasb:///example/python/ConsumeGBNYCReg.py"}', headers )
    response = conn.getresponse().read()
    print(response)
    conn.close()


Também pode adicionar este código de Python para [das funções do Azure](https://azure.microsoft.com/documentation/services/functions/) para acionar uma submissão de tarefa de Spark pontua um blob com base em vários eventos como um temporizador, a criação ou atualização de um blob. 

Se preferir uma experiência de cliente livre de código, utilize o [Azure Logic Apps](https://azure.microsoft.com/documentation/services/app-service/logic/) para invocar o Spark de classificação de lote através da definição de uma ação de HTTP no **Designer de aplicações lógicas** e definir os respetivos parâmetros. 

* A partir do portal do Azure, criar uma nova aplicação lógica selecionando **+ novo** -> **Web + móvel** -> **aplicação lógica**. 
* Para fazer aparecer o **Designer de aplicações lógicas**, introduza o nome da aplicação lógica e plano do App Service.
* Selecione uma ação de HTTP e introduza os parâmetros mostrados na figura seguinte:

![Designer de aplicações lógicas](./media/spark-model-consumption/spark-logica-app-client.png)

## <a name="whats-next"></a>Passos seguintes?
**Validação cruzada e hyperparameter varrimento**: consulte [avançada de exploração de dados e modelação com o Spark](spark-advanced-data-exploration-modeling.md) na forma como os modelos podem ser preparado varrimento de validação cruzada e hyper parâmetro a utilizar.

