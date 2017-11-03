---
title: "Exploração de dados e modelação com o Spark | Microsoft Docs"
description: "Showcases as capacidades de exploração e modelação de dados do toolkit MLlib do Spark no Azure."
services: machine-learning
documentationcenter: 
author: bradsev
manager: jhubbard
editor: cgronlun
ms.assetid: b989b918-5ba5-4696-b8d0-76ae510a23f4
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/15/2017
ms.author: deguhath;bradsev;gokuma
ms.openlocfilehash: e9c7ae58825d640a33c7d76eb5016faeb3de2849
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="data-exploration-and-modeling-with-spark"></a>Exploração e modelação de dados com o Spark
[!INCLUDE [machine-learning-spark-modeling](../../../includes/machine-learning-spark-modeling.md)]

Estas instruções utilizam do HDInsight Spark para fazer a exploração de dados e classificação binária regressão modelação tarefas numa amostra do NYC taxi viagem e fare 2013 conjunto de dados.  -Explica os passos para o [o processo de ciência de dados](http://aka.ms/datascienceprocess)ponto-a- ponto, utilizando um cluster do HDInsight Spark para blobs de processamento e do Azure para armazenar os dados e os modelos. O processo explicar e visualiza dados colocados de um Blob de armazenamento do Azure e, em seguida, prepara os dados para criar modelos preditivos. Estes modelos são compilação através do toolkit de Spark MLlib para efetuar tarefas de modelação de classificação binária e regressão.

* O **classificação binária** a tarefa está ou não uma sugestão é paga para a viagem de prever. 
* O **regressão** tarefa é prever a quantidade de sugestão com base nas outras funcionalidades de sugestão. 

Os modelos que utilizamos incluem regressão linear e logística da, florestas aleatórias e gradação árvores elevadas:

* [Regressão linear com SGD](https://spark.apache.org/docs/latest/api/python/pyspark.mllib.html#pyspark.mllib.regression.LinearRegressionWithSGD) é um modelo de regressão linear que utiliza um método Stochastic gradação descendente (SGD) e para Otimização e a funcionalidade de dimensionamento para prever as quantidades de sugestão paga. 
* [Regressão logística com LBFGS](https://spark.apache.org/docs/latest/api/python/pyspark.mllib.html#pyspark.mllib.classification.LogisticRegressionWithLBFGS) ou regressão "logit", é um modelo de regressão que pode ser utilizado quando a variável de dependentes é categórico para fazer a classificação de dados. LBFGS é um algoritmo de otimização quasi-Newton approximates que o algoritmo de Broyden – Fletcher – Goldfarb – Shanno (BFGS) com uma quantidade limitada de memória do computador e que é amplamente utilizado no machine learning.
* [Florestas aleatórias](http://spark.apache.org/docs/latest/mllib-ensembles.html#Random-Forests) são ensembles de árvores de decisões.  Se combinam várias árvores de decisões para reduzir o risco de overfitting. Florestas aleatórias são utilizadas para regressão e classificação e podem processar funcionalidades categórico e podem ser expandidas para a definição de classificação de várias classes. Estes não necessitam de dimensionamento de funcionalidade e são capazes de captura não linearities e interações de funcionalidade. Florestas aleatórias são um do mais bem-sucedida modelos de machine learning para classificação e regressão.
* [Gradação elevada árvores](http://spark.apache.org/docs/latest/ml-classification-regression.html#gradient-boosted-trees-gbts) (GBTs) são ensembles de árvores de decisões. GBTs preparar árvores de decisões iteratively para minimizar a uma função de perda. GBTs são utilizadas para regressão e classificação pode processar categórico funcionalidades, não necessitam de dimensionamento de funcionalidade e são capazes de captura não linearities e interações de funcionalidade. Também pode ser utilizados uma definição de classificação de várias classes.

Os passos de modelação também contenham código que mostra como preparar, avaliar e guardar cada tipo de modelo. Python foi utilizado para a solução de código e para mostrar o rastreia relevante.   

> [!NOTE]
> Embora o toolkit de Spark MLlib foi concebido para funcionar no grandes conjuntos de dados, uma amostra relativamente pequena (Mb de ~ 30 utilizando 170K linhas, cerca de 0.1% do conjunto de dados original NYC) é utilizada aqui para sua comodidade. Exercício fornecido aqui executa forma eficiente (em cerca de 10 minutos) num cluster do HDInsight com 2 nós de trabalho. O mesmo código, com pequenas alterações, pode ser utilizado para processar conjuntos de dados maiores, com as alterações adequadas para a colocação em cache dados na memória e alterar o tamanho do cluster.
> 
> 

## <a name="prerequisites"></a>Pré-requisitos
Precisa de uma conta do Azure e um Spark 1.6 (ou Spark 2.0) cluster do HDInsight para concluir estas instruções. Consulte o [descrição geral de ciência de dados com o Spark no Azure HDInsight](spark-overview.md) para obter instruções sobre como satisfazer estes requisitos. Este tópico também contém uma descrição dos dados NYC 2013 Taxi utilizados aqui e obter instruções sobre como executar código a partir de um bloco de notas do Jupyter no cluster do Spark. 

## <a name="spark-clusters-and-notebooks"></a>Clusters do Spark e blocos de notas
Passos de configuração e de código são fornecidos esta explicação passo a passo para utilizar um 1.6 Spark do HDInsight. Mas blocos de notas do Jupyter são fornecidos para os clusters do HDInsight Spark 1.6 e 2.0 do Spark. Uma descrição dos blocos de notas e ligações para os mesmos são fornecidos no [Readme.md](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Readme.md) para o repositório do GitHub que contém-los. Além disso, o código aqui e em blocos de notas ligados é genérico e deve trabalhar em qualquer cluster do Spark. Se não estiver a utilizar o Spark do HDInsight, os passos de configuração e gestão de cluster podem ser ligeiramente diferentes das que é mostrado aqui. Para sua comodidade, seguem-se as ligações de blocos de notas do Jupyter para 1.6 Spark (para ser executada no kernel do pySpark do servidor de notas do Jupyter) e 2.0 do Spark (para ser executada na kernel pySpark3 do servidor de notas do Jupyter):

### <a name="spark-16-notebooks"></a>Blocos de notas do Spark 1.6

[pySpark-machine-learning-data-science-spark-data-exploration-modeling.ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Spark1.6/pySpark-machine-learning-data-science-spark-data-exploration-modeling.ipynb): fornece informações sobre como efetuar a exploração de dados, modelação e a classificação com vários algoritmos diferentes.

### <a name="spark-20-notebooks"></a>Blocos de notas do Spark 2.0
As tarefas de regressão e a classificação que são implementadas através de um cluster do Spark 2.0 estão em separado blocos de notas e o bloco de notas classificação utiliza um conjunto de dados diferentes:

- [Spark2.0-pySpark3-Machine-Learning-data-Science-spark-Advanced-Data-Exploration-Modeling.ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Spark2.0/Spark2.0-pySpark3-machine-learning-data-science-spark-advanced-data-exploration-modeling.ipynb): este ficheiro fornece informações sobre como efetuar a exploração de dados, modelação de e classificação no Spark 2.0 clusters com a viagem NYC Taxi e fare-conjunto de dados descrito [aqui](https://docs.microsoft.com/en-us/azure/machine-learning/machine-learning-data-science-spark-overview#the-nyc-2013-taxi-data). Este bloco de notas pode ser um ponto de partida para explorar rapidamente o código que forneceu para Spark 2.0. Para um bloco de notas mais detalhado analisa os dados de NYC Taxi, consulte o seguinte bloco de notas nesta lista. Consulte as notas desta lista a seguir e comparam estes blocos de notas. 
- [Spark2.0 pySpark3_NYC_Taxi_Tip_Regression.ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Spark2.0/Spark2.0_pySpark3_NYC_Taxi_Tip_Regression.ipynb): este ficheiro mostra como efetuar dados wrangling (operações de Spark SQL e dataframe), exploração, modelação e a classificação utilizando a viagem NYC Taxi e fare conjunto de dados descritos [aqui ](https://docs.microsoft.com/en-us/azure/machine-learning/machine-learning-data-science-spark-overview#the-nyc-2013-taxi-data).
- [Spark2.0 pySpark3_Airline_Departure_Delay_Classification.ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Spark2.0/Spark2.0_pySpark3_Airline_Departure_Delay_Classification.ipynb): este ficheiro mostra como efetuar dados wrangling (operações de Spark SQL e dataframe), exploração, modelação e a classificação utilizando o distanciamento de tempo companhia aérea bem conhecido conjunto de dados de 2011 e 2012. É integrado o conjunto de dados companhia aérea com os dados de Meteorologia airport (por exemplo, windspeed temperatura, altitude etc.) antes da modelação de para que estas funcionalidades Meteorologia podem ser incluídas no modelo.

<!-- -->

> [!NOTE]
> O conjunto de dados companhia aérea foi adicionado a blocos de notas do Spark 2.0 para ilustrar melhor a utilização de algoritmos de classificação. Consulte as seguintes ligações para informações sobre companhia aérea em tempo de conjunto de dados de partida e meteorologia conjunto de dados:

>- Dados de partida de tempo companhia aérea: [http://www.transtats.bts.gov/ONTIME/](http://www.transtats.bts.gov/ONTIME/)

>- Dados de Meteorologia AIRport: [https://www.ncdc.noaa.gov/](https://www.ncdc.noaa.gov/) 
> 
> 

<!-- -->

<!-- -->

> [!NOTE]
Os blocos de notas do Spark 2.0 os NYC taxi e companhia aérea voo atraso-conjuntos de dados podem demorar 10 minutos ou mais para ser executada (dependendo do tamanho do seu cluster HDI). O bloco de notas primeiro na lista acima mostra muitos aspetos da exploração de dados, formação num bloco de notas demora menos tempo para executar com o conjunto de dados do NYC amostragem em baixo, na qual os ficheiros taxi e fare tem sido previamente associado a um modelo de visualização e ML: [ Spark2.0-pySpark3-Machine-Learning-data-Science-spark-Advanced-Data-Exploration-Modeling.ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Spark2.0/Spark2.0-pySpark3-machine-learning-data-science-spark-advanced-data-exploration-modeling.ipynb) este bloco de notas assume um tempo muito mais curto (2 a 3 minutos) e pode ser um bom ponto de partida para explorar rapidamente o código fornecemos para o Spark 2.0. 

<!-- -->

[!INCLUDE [delete-cluster-warning](../../../includes/hdinsight-delete-cluster-warning.md)]

<!-- -->

> [!NOTE]
As descrições abaixo estão relacionados com a utilização do Spark 1.6. Para versões de Spark 2.0, utilize os blocos de notas descrito e ligação acima. 

<!-- -->

## <a name="setup-storage-locations-libraries-and-the-preset-spark-context"></a>Programa de configuração: localizações de armazenamento, bibliotecas e o contexto de Spark predefinido
O Spark é capaz de leitura e escrita para o Blob de armazenamento do Azure (também conhecido como WASB). Para que qualquer um dos seus dados existentes armazenados existe podem ser processados utilizando Spark e os resultados novamente armazenados na WASB.

Para guardar modelos ou ficheiros em WASB, o caminho tem de ser especificadas corretamente. O contentor predefinido ligado ao cluster do Spark pode ser referenciado utilizando um início de caminho com: "wasb: / / /". Outras localizações são referenciadas por "wasb: / /".

### <a name="set-directory-paths-for-storage-locations-in-wasb"></a>Caminhos de diretório de conjunto para localizações de armazenamento no WASB
O exemplo de código seguinte especifica a localização dos dados a ser lido e o caminho para o diretório de armazenamento de modelo ao qual o resultado de modelo é guardado:

    # SET PATHS TO FILE LOCATIONS: DATA AND MODEL STORAGE

    # LOCATION OF TRAINING DATA
    taxi_train_file_loc = "wasb://mllibwalkthroughs@cdspsparksamples.blob.core.windows.net/Data/NYCTaxi/JoinedTaxiTripFare.Point1Pct.Train.tsv";

    # SET THE MODEL STORAGE DIRECTORY PATH 
    # NOTE THAT THE FINAL BACKSLASH IN THE PATH IS NEEDED.
    modelDir = "wasb:///user/remoteuser/NYCTaxi/Models/" 


### <a name="import-libraries"></a>Bibliotecas de importação
Configurar também requer a importação de bibliotecas necessárias. Definir o contexto de spark e importe necessárias bibliotecas com o seguinte código:

    # IMPORT LIBRARIES
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
Os PySpark kernels que são fornecidos com blocos de notas do Jupyter têm um contexto predefinido. Por isso, não terá de definir o Spark ou Hive contextos explicitamente antes de começar a trabalhar com a aplicação estiver a desenvolver. Estes contextos estão disponíveis para si por predefinição. Estes contextos são:

* SC - para Spark 
* sqlContext - para o ramo de registo

O kernel do PySpark fornece algumas predefinidas "magia", que são comandos especiais que pode chamar com % %. Existem dois esses comandos que são utilizados nestes exemplos de código.

* **% % local** Especifica que o código de linhas subsequentes é para ser executada localmente. Código tem de ser código Python válido.
* **% % sql -o <variable name>**  executa uma consulta do Hive contra o sqlContext. Se o parâmetro -o é transmitido, o resultado da consulta é continuado no % % contexto de Python local como uma DataFrame Pandas.

Para obter mais informações sobre os kernels para blocos de notas do Jupyter e predefinidos "magics" que fornecem, consulte [clusters Kernels disponíveis para blocos de notas do Jupyter com o HDInsight Spark Linux no HDInsight](../../hdinsight/hdinsight-apache-spark-jupyter-notebook-kernels.md).

## <a name="data-ingestion-from-public-blob"></a>Ingestão de dados a partir do blob público
O primeiro passo no processo de ciência de dados é para a ingestão de dados a ser analisado a partir de origens onde é reside no seu ambiente de exploração e modelação de dados. O ambiente é Spark nestas instruções. Esta secção contém o código de conclusão de uma série de tarefas:

* o exemplo de dados para ser modelada de inserção
* ler o conjunto de dados de entrada (armazenado como um ficheiro de .tsv)
* Formatar e limpar os dados
* criar e colocar em cache objetos (RDDs ou pacotes de dados) na memória
* Registe-o como uma tabela temporária no contexto SQL.

Eis o código para a ingestão de dados.

    # INGEST DATA

    # RECORD START TIME
    timestart = datetime.datetime.now()

    # IMPORT FILE FROM PUBLIC BLOB
    taxi_train_file = sc.textFile(taxi_train_file_loc)

    # GET SCHEMA OF THE FILE FROM HEADER
    schema_string = taxi_train_file.first()
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

    # PARSE FIELDS AND CONVERT DATA TYPE FOR SOME FIELDS
    taxi_header = taxi_train_file.filter(lambda l: "medallion" in l)
    taxi_temp = taxi_train_file.subtract(taxi_header).map(lambda k: k.split("\t"))\
            .map(lambda p: (p[0],p[1],p[2],p[3],p[4],p[5],p[6],int(p[7]),int(p[8]),int(p[9]),int(p[10]),
                            float(p[11]),float(p[12]),p[13],p[14],p[15],p[16],p[17],p[18],float(p[19]),
                            float(p[20]),float(p[21]),float(p[22]),float(p[23]),float(p[24]),int(p[25]),int(p[26])))


    # CREATE DATA FRAME
    taxi_train_df = sqlContext.createDataFrame(taxi_temp, taxi_schema)

    # CREATE A CLEANED DATA-FRAME BY DROPPING SOME UN-NECESSARY COLUMNS & FILTERING FOR UNDESIRED VALUES OR OUTLIERS
    taxi_df_train_cleaned = taxi_train_df.drop('medallion').drop('hack_license').drop('store_and_fwd_flag').drop('pickup_datetime')\
        .drop('dropoff_datetime').drop('pickup_longitude').drop('pickup_latitude').drop('dropoff_latitude')\
        .drop('dropoff_longitude').drop('tip_class').drop('total_amount').drop('tolls_amount').drop('mta_tax')\
        .drop('direct_distance').drop('surcharge')\
        .filter("passenger_count > 0 and passenger_count < 8 AND payment_type in ('CSH', 'CRD') AND tip_amount >= 0 AND tip_amount < 30 AND fare_amount >= 1 AND fare_amount < 150 AND trip_distance > 0 AND trip_distance < 100 AND trip_time_in_secs > 30 AND trip_time_in_secs < 7200" )


    # CACHE DATA-FRAME IN MEMORY & MATERIALIZE DF IN MEMORY
    taxi_df_train_cleaned.cache()
    taxi_df_train_cleaned.count()

    # REGISTER DATA-FRAME AS A TEMP-TABLE IN SQL-CONTEXT
    taxi_df_train_cleaned.registerTempTable("taxi_train")

    # PRINT HOW MUCH TIME IT TOOK TO RUN THE CELL
    timeend = datetime.datetime.now()
    timedelta = round((timeend-timestart).total_seconds(), 2) 
    print "Time taken to execute above cell: " + str(timedelta) + " seconds";

**SAÍDA:**

Tempo decorrido para executar acima célula: 51.72 segundos

## <a name="data-exploration--visualization"></a>Exploração de dados & visualização
Depois dos dados tem sido colocados em Spark, o próximo passo no processo de ciência de dados é obter uma compreensão mais aprofundada dos dados através de exploração e visualização. Nesta secção, vamos examinar os dados de taxi através de consultas SQL e desenhar as variáveis de destino e potenciais funcionalidades para inspecção visual. Mais concretamente, podemos desenhar a frequência das contagens de passenger taxi viagens, a frequência de quantidades de sugestão e como sugestões variam consoante o tipo e a quantidade de pagamento.

### <a name="plot-a-histogram-of-passenger-count-frequencies-in-the-sample-of-taxi-trips"></a>Desenhar um histograma de frequências de contagem de passenger na amostra do viagens taxi
Este código e subsequentes fragmentos utilizam magic SQL para consultar a magia local para representar os dados de exemplo e.

* **Magic SQL (`%%sql`)** kernel do PySpark de HDInsight suporta consultas de HiveQL de fácil inline contra o sqlContext. O (-ão VARIABLE_NAME) argumento persistir o resultado da consulta SQL como um DataFrame Pandas no servidor do Jupyter. Isto significa que está disponível no modo de local.
* O  **`%%local` magic** é utilizada para executar código localmente no servidor do Jupyter, o que é o headnode de cluster do HDInsight. Normalmente, utiliza `%%local` mágica em conjunto com o `%%sql` mágica com o parâmetro -o. O parâmetro -o seria manter o resultado da consulta SQL localmente e, em seguida, % % local magic acionaria o seguinte conjunto de fragmento de código para executar localmente o resultado das consultas de SQL Server que é localmente persistente

O resultado é visualizado automaticamente depois de executar o código.

Esta consulta obtém as viagens por contagem de passenger. 

    # PLOT FREQUENCY OF PASSENGER COUNTS IN TAXI TRIPS

    # HIVEQL QUERY AGAINST THE sqlContext
    %%sql -q -o sqlResults
    SELECT passenger_count, COUNT(*) as trip_counts 
    FROM taxi_train 
    WHERE passenger_count > 0 and passenger_count < 7 
    GROUP BY passenger_count 

Este código cria um intervalo de dados local da saída de consulta e rastreia os dados. O `%%local` magic cria um período de dados local, `sqlResults`, que pode ser utilizado para representar com matplotlib. 

> [!NOTE]
> Este magic PySpark sido utilizada várias vezes nestas instruções. Se a quantidade de dados for grande, deve de exemplo para criar um intervalo de dados que pode caber na memória local.
> 
> 

    #CREATE LOCAL DATA-FRAME AND USE FOR MATPLOTLIB PLOTTING

    # RUN THE CODE LOCALLY ON THE JUPYTER SERVER
    %%local

    # USE THE JUPYTER AUTO-PLOTTING FEATURE TO CREATE INTERACTIVE FIGURES. 
    # CLICK ON THE TYPE OF PLOT TO BE GENERATED (E.G. LINE, AREA, BAR ETC.)
    sqlResults

Eis o código para desenhar viagens por contagens passenger

    # PLOT PASSENGER NUMBER VS. TRIP COUNTS
    %%local
    import matplotlib.pyplot as plt
    %matplotlib inline

    x_labels = sqlResults['passenger_count'].values
    fig = sqlResults[['trip_counts']].plot(kind='bar', facecolor='lightblue')
    fig.set_xticklabels(x_labels)
    fig.set_title('Counts of trips by passenger count')
    fig.set_xlabel('Passenger count in trips')
    fig.set_ylabel('Trip counts')
    plt.show()

**SAÍDA:**

![Frequência de viagem por contagem de passenger](./media/spark-data-exploration-modeling/trip-freqency-by-passenger-count.png)

Pode selecionar entre os vários tipos diferentes de visualizações (tabela, circular, linha, área ou barra) utilizando o **tipo** botões de menu no bloco de notas. O desenho de barra é mostrado aqui.

### <a name="plot-a-histogram-of-tip-amounts-and-how-tip-amount-varies-by-passenger-count-and-fare-amounts"></a>Desenhar um histograma de sugestão quantidades e como a quantidade de sugestão varia consoante o quantidades de contagem e fare passenger.
Utilize uma consulta SQL para dados de exemplo.

    #PLOT HISTOGRAM OF TIP AMOUNTS AND VARIATION BY PASSENGER COUNT AND PAYMENT TYPE

    # HIVEQL QUERY AGAINST THE sqlContext
    %%sql -q -o sqlResults
    SELECT fare_amount, passenger_count, tip_amount, tipped 
    FROM taxi_train 
    WHERE passenger_count > 0 
    AND passenger_count < 7 
    AND fare_amount > 0 
    AND fare_amount < 200 
    AND payment_type in ('CSH', 'CRD') 
    AND tip_amount > 0 
    AND tip_amount < 25


Esta célula do código utiliza a consulta SQL para criar três rastreia os dados.

    # RUN THE CODE LOCALLY ON THE JUPYTER SERVER
    %%local

    # HISTOGRAM OF TIP AMOUNTS AND PASSENGER COUNT
    ax1 = sqlResults[['tip_amount']].plot(kind='hist', bins=25, facecolor='lightblue')
    ax1.set_title('Tip amount distribution')
    ax1.set_xlabel('Tip Amount ($)')
    ax1.set_ylabel('Counts')
    plt.suptitle('')
    plt.show()

    # TIP BY PASSENGER COUNT
    ax2 = sqlResults.boxplot(column=['tip_amount'], by=['passenger_count'])
    ax2.set_title('Tip amount by Passenger count')
    ax2.set_xlabel('Passenger count')
    ax2.set_ylabel('Tip Amount ($)')
    plt.suptitle('')
    plt.show()

    # TIP AMOUNT BY FARE AMOUNT, POINTS ARE SCALED BY PASSENGER COUNT
    ax = sqlResults.plot(kind='scatter', x= 'fare_amount', y = 'tip_amount', c='blue', alpha = 0.10, s=5*(sqlResults.passenger_count))
    ax.set_title('Tip amount by Fare amount')
    ax.set_xlabel('Fare Amount ($)')
    ax.set_ylabel('Tip Amount ($)')
    plt.axis([-2, 100, -2, 20])
    plt.show()


**SAÍDA:** 

![Distribuição da quantidade de sugestão](./media/spark-data-exploration-modeling/tip-amount-distribution.png)

![Quantidade de sugestão por contagem de passenger](./media/spark-data-exploration-modeling/tip-amount-by-passenger-count.png)

![Quantidade de sugestão pela quantidade de fare](./media/spark-data-exploration-modeling/tip-amount-by-fare-amount.png)

## <a name="feature-engineering-transformation-and-data-preparation-for-modeling"></a>Preparação de engenharia, transformação e dados para a modelação de funcionalidade
Esta secção descreve e fornece o código para os procedimentos utilizados para preparar os dados para utilização num modelação de ML. Mostra como efetuar as seguintes tarefas:

* Criar uma nova funcionalidade de discretização horas para registos de tempo de tráfego
* O índice e codificar funcionalidades categórico
* Criar objetos com nome de ponto de entrada para as funções de ML
* Criar uma amostra aleatória secundárias dos dados e dividi-lo em formação e testar conjuntos
* Dimensionamento de funcionalidade
* Objetos da cache na memória

### <a name="create-a-new-feature-by-binning-hours-into-traffic-time-buckets"></a>Criar uma nova funcionalidade de discretização horas para registos de tempo de tráfego
Este código mostra como criar uma nova funcionalidade de discretização horas para registos de tempo de tráfego e, em seguida, como colocar em cache da moldura de dados resultante na memória. Onde Resiliente distribuídas conjuntos de dados (RDDs) e pacotes de dados são utilizados repetidamente, colocação em cache leva a melhorada os tempos de execução. Em conformidade, iremos cache RDDs e pacotes de dados em várias fases de instruções. 

    # CREATE FOUR BUCKETS FOR TRAFFIC TIMES
    sqlStatement = """
        SELECT *,
        CASE
         WHEN (pickup_hour <= 6 OR pickup_hour >= 20) THEN "Night" 
         WHEN (pickup_hour >= 7 AND pickup_hour <= 10) THEN "AMRush" 
         WHEN (pickup_hour >= 11 AND pickup_hour <= 15) THEN "Afternoon"
         WHEN (pickup_hour >= 16 AND pickup_hour <= 19) THEN "PMRush"
        END as TrafficTimeBins
        FROM taxi_train 
    """
    taxi_df_train_with_newFeatures = sqlContext.sql(sqlStatement)

    # CACHE DATA-FRAME IN MEMORY & MATERIALIZE DF IN MEMORY
    # THE .COUNT() GOES THROUGH THE ENTIRE DATA-FRAME,
    # MATERIALIZES IT IN MEMORY, AND GIVES THE COUNT OF ROWS.
    taxi_df_train_with_newFeatures.cache()
    taxi_df_train_with_newFeatures.count()

**SAÍDA:** 

126050

### <a name="index-and-encode-categorical-features-for-input-into-modeling-functions"></a>O índice e codificar categórico funcionalidades para a entrada para a modelação de funções
Esta secção mostra como o índice ou codificar categórico funcionalidades para a entrada para as funções de modelação. A modelação e prever as funções do MLlib requerem funcionalidades com dados de entrada categórico para ser incluída num índice ou codificado antes de utilizar. Consoante o modelo, terá de índice ou codificá-los de formas diferentes:  

* **Com base em árvore modelação** necessita de categorias devem ser codificados como valores de numérico (por exemplo, uma funcionalidade com três categorias pode ser codificada com 0, 1, 2). Isto é fornecido do MLlib [StringIndexer](http://spark.apache.org/docs/latest/ml-features.html#stringindexer) função. Esta função codifica uma coluna de cadeia de etiquetas a uma coluna de índices de etiqueta ordenadas frequências de etiqueta. Embora indexado com numérico valores de entrada e de processamento de dados, os algoritmos de árvore podem ser especificados para tratá-los adequadamente como categorias. 
* **Modelos de logística da e regressão Linear** necessitam de acesso frequente uma codificação, onde, por exemplo, uma funcionalidade com três categorias pode ser expandida para três colunas de funcionalidade, com cada contentor 0 ou 1 consoante a categoria de uma observação. Fornece MLlib [OneHotEncoder](http://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.OneHotEncoder.html#sklearn.preprocessing.OneHotEncoder) função para acesso frequente uma codificação. Neste codificador mapeia uma coluna de índices de etiqueta a uma coluna de vetores de binários, no máximo um único um valor. Esta codificação permite que os algoritmos que esperam numéricos funcionalidades importantes, como regressão logística da, a aplicar às funcionalidades categórico.

Eis o código para o índice e codificar categórico funcionalidades:

    # INDEX AND ENCODE CATEGORICAL FEATURES

    # RECORD START TIME
    timestart = datetime.datetime.now()

    # LOAD PYSPARK LIBRARIES    
    from pyspark.ml.feature import OneHotEncoder, StringIndexer, VectorAssembler, VectorIndexer

    # INDEX AND ENCODE VENDOR_ID
    stringIndexer = StringIndexer(inputCol="vendor_id", outputCol="vendorIndex")
    model = stringIndexer.fit(taxi_df_train_with_newFeatures) # Input data-frame is the cleaned one from above
    indexed = model.transform(taxi_df_train_with_newFeatures)
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

    # INDEX AND TRAFFIC TIME BINS
    stringIndexer = StringIndexer(inputCol="TrafficTimeBins", outputCol="TrafficTimeBinsIndex")
    model = stringIndexer.fit(encoded3)
    indexed = model.transform(encoded3)
    encoder = OneHotEncoder(dropLast=False, inputCol="TrafficTimeBinsIndex", outputCol="TrafficTimeBinsVec")
    encodedFinal = encoder.transform(indexed)

    # PRINT ELAPSED TIME
    timeend = datetime.datetime.now()
    timedelta = round((timeend-timestart).total_seconds(), 2) 
    print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 

**SAÍDA:**

Tempo decorrido para executar acima célula: 1.28 segundos

### <a name="create-labeled-point-objects-for-input-into-ml-functions"></a>Criar objetos com nome de ponto de entrada para as funções de ML
Esta secção contém o código que mostra como dados de texto categórico como um tipo de dados do ponto de com nome de índice e codificá-la para que possa ser utilizado para dar formação e testar regressão logística da MLlib e outros modelos de classificação. Os objetos de ponto identificados são formatado de uma forma que é necessário como dados de entrada pela maioria dos algoritmos de ML no MLlib com a conjuntos de dados de distribuídas Resiliente (RDD). A [denominado ponto](https://spark.apache.org/docs/latest/mllib-data-types.html#labeled-point) está associado a um vetor local, em ou disperso, com uma etiqueta/resposta.  

Esta secção contém o código que mostra como índice dados categórico texto como um [denominado ponto](https://spark.apache.org/docs/latest/mllib-data-types.html#labeled-point) codificá-la para que possa ser utilizado para dar formação e testar e outros modelos de classificação de regressão logística da MLlib e tipo de dados. Os objetos de ponto identificados são constituídas por uma etiqueta (variável de destino/resposta) e o vetor de funcionalidade com a conjuntos de dados de distribuídas Resiliente (RDD). Este formato é necessário como entrada muitos algoritmos de ML no MLlib.

Eis o código para o índice e codificar texto as funcionalidades para classificação binária.

    # FUNCTIONS FOR BINARY CLASSIFICATION

    # LOAD LIBRARIES
    from pyspark.mllib.regression import LabeledPoint
    from numpy import array

    # INDEXING CATEGORICAL TEXT FEATURES FOR INPUT INTO TREE-BASED MODELS
    def parseRowIndexingBinary(line):
        features = np.array([line.paymentIndex, line.vendorIndex, line.rateIndex, line.TrafficTimeBinsIndex,
                             line.pickup_hour, line.weekday, line.passenger_count, line.trip_time_in_secs, 
                             line.trip_distance, line.fare_amount])
        labPt = LabeledPoint(line.tipped, features)
        return  labPt

    # ONE-HOT ENCODING OF CATEGORICAL TEXT FEATURES FOR INPUT INTO LOGISTIC RERESSION MODELS
    def parseRowOneHotBinary(line):
        features = np.concatenate((np.array([line.pickup_hour, line.weekday, line.passenger_count,
                                            line.trip_time_in_secs, line.trip_distance, line.fare_amount]), 
                                            line.vendorVec.toArray(), line.rateVec.toArray(), 
                                            line.paymentVec.toArray(), line.TrafficTimeBinsVec.toArray()), axis=0)
        labPt = LabeledPoint(line.tipped, features)
        return  labPt


Eis o código para codificar e funcionalidades de texto categórico para análise de regressão linear de índice.

    # FUNCTIONS FOR REGRESSION WITH TIP AMOUNT AS TARGET VARIABLE

    # ONE-HOT ENCODING OF CATEGORICAL TEXT FEATURES FOR INPUT INTO TREE-BASED MODELS
    def parseRowIndexingRegression(line):
        features = np.array([line.paymentIndex, line.vendorIndex, line.rateIndex, line.TrafficTimeBinsIndex, 
                             line.pickup_hour, line.weekday, line.passenger_count, line.trip_time_in_secs, 
                             line.trip_distance, line.fare_amount])

        labPt = LabeledPoint(line.tip_amount, features)
        return  labPt

    # INDEXING CATEGORICAL TEXT FEATURES FOR INPUT INTO LINEAR REGRESSION MODELS
    def parseRowOneHotRegression(line):
        features = np.concatenate((np.array([line.pickup_hour, line.weekday, line.passenger_count,
                                            line.trip_time_in_secs, line.trip_distance, line.fare_amount]), 
                                            line.vendorVec.toArray(), line.rateVec.toArray(), 
                                            line.paymentVec.toArray(), line.TrafficTimeBinsVec.toArray()), axis=0)
        labPt = LabeledPoint(line.tip_amount, features)
        return  labPt


### <a name="create-a-random-sub-sampling-of-the-data-and-split-it-into-training-and-testing-sets"></a>Criar uma amostra aleatória secundárias dos dados e dividi-lo em formação e testar conjuntos
Este código cria uma amostra aleatória dos dados (25% é utilizado aqui). Embora não seja necessário para este exemplo, porque o tamanho do conjunto de dados, vamos demonstrar como pode apresentar exemplos aqui para saber como utilizá-la para o seu próprio problema quando necessário. Quando os exemplos são grandes, isto permite poupar muito tempo ao modelos de formação. Junto a amostra é dividido numa parte de formação (75% aqui) e uma parte de teste (25% aqui) para utilizar na classificação e modelação de regressão.

    # RECORD START TIME
    timestart = datetime.datetime.now()

    # LOAD PYSPARK LIBRARIES
    from pyspark.sql.functions import rand

    # SPECIFY SAMPLING AND SPLITTING FRACTIONS
    samplingFraction = 0.25;
    trainingFraction = 0.75; testingFraction = (1-trainingFraction);
    seed = 1234;
    encodedFinalSampled = encodedFinal.sample(False, samplingFraction, seed=seed)

    # SPLIT SAMPLED DATA-FRAME INTO TRAIN/TEST
    # INCLUDE RAND COLUMN FOR CREATING CROSS-VALIDATION FOLDS (FOR USE LATER IN AN ADVANCED TOPIC)
    dfTmpRand = encodedFinalSampled.select("*", rand(0).alias("rand"));
    trainData, testData = dfTmpRand.randomSplit([trainingFraction, testingFraction], seed=seed);

    # FOR BINARY CLASSIFICATION TRAINING AND TESTING
    indexedTRAINbinary = trainData.map(parseRowIndexingBinary)
    indexedTESTbinary = testData.map(parseRowIndexingBinary)
    oneHotTRAINbinary = trainData.map(parseRowOneHotBinary)
    oneHotTESTbinary = testData.map(parseRowOneHotBinary)

    # FOR REGRESSION TRAINING AND TESTING
    indexedTRAINreg = trainData.map(parseRowIndexingRegression)
    indexedTESTreg = testData.map(parseRowIndexingRegression)
    oneHotTRAINreg = trainData.map(parseRowOneHotRegression)
    oneHotTESTreg = testData.map(parseRowOneHotRegression)

    # PRINT ELAPSED TIME
    timeend = datetime.datetime.now()
    timedelta = round((timeend-timestart).total_seconds(), 2) 
    print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 

**SAÍDA:**

Tempo decorrido para executar acima célula: 0.24 segundos

### <a name="feature-scaling"></a>Dimensionamento de funcionalidade
Funcionalidade de dimensionamento, também conhecido como normalização de dados, forma, assegura que funcionalidades com valores amplamente disbursed são não fornecido excessivo pesar na função objetivo. O código para a funcionalidade da receção utiliza o [StandardScaler](https://spark.apache.org/docs/latest/api/python/pyspark.mllib.html#pyspark.mllib.feature.StandardScaler) para as funcionalidades a variância de unidade de escala. É fornecido pelo MLlib para utilização num regressão linear com Stochastic gradação descendente (SGD), um algoritmo mais popular para uma vasta gama de outros modelos de machine learning como regressões regularized ou máquinas de vetor de suporte (SVM) de preparação.

> [!NOTE]
> Ter encontrámos o algoritmo de LinearRegressionWithSGD ser sensível a funcionalidade de dimensionamento.
> 
> 

Eis o código para variáveis de dimensionamento para utilização com o algoritmo SGD regularized linear.

    # FEATURE SCALING

    # RECORD START TIME
    timestart = datetime.datetime.now()

    # LOAD PYSPARK LIBRARIES
    from pyspark.mllib.regression import LabeledPoint
    from pyspark.mllib.linalg import Vectors
    from pyspark.mllib.feature import StandardScaler, StandardScalerModel
    from pyspark.mllib.util import MLUtils

    # SCALE VARIABLES FOR REGULARIZED LINEAR SGD ALGORITHM
    label = oneHotTRAINreg.map(lambda x: x.label)
    features = oneHotTRAINreg.map(lambda x: x.features)
    scaler = StandardScaler(withMean=False, withStd=True).fit(features)
    dataTMP = label.zip(scaler.transform(features.map(lambda x: Vectors.dense(x.toArray()))))
    oneHotTRAINregScaled = dataTMP.map(lambda x: LabeledPoint(x[0], x[1]))

    label = oneHotTESTreg.map(lambda x: x.label)
    features = oneHotTESTreg.map(lambda x: x.features)
    scaler = StandardScaler(withMean=False, withStd=True).fit(features)
    dataTMP = label.zip(scaler.transform(features.map(lambda x: Vectors.dense(x.toArray()))))
    oneHotTESTregScaled = dataTMP.map(lambda x: LabeledPoint(x[0], x[1]))

    # PRINT ELAPSED TIME
    timeend = datetime.datetime.now()
    timedelta = round((timeend-timestart).total_seconds(), 2) 
    print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 

**SAÍDA:**

Tempo decorrido para executar acima célula: 13.17 segundos

### <a name="cache-objects-in-memory"></a>Objetos da cache na memória
O tempo decorrido para formação e testar dos algoritmos de ML pode ser reduzido através da moldura de dados de entrada objetos utilizado para classificação, regressão e ampliada funcionalidades a colocação em cache.

    # RECORD START TIME
    timestart = datetime.datetime.now()

    # FOR BINARY CLASSIFICATION TRAINING AND TESTING
    indexedTRAINbinary.cache()
    indexedTESTbinary.cache()
    oneHotTRAINbinary.cache()
    oneHotTESTbinary.cache()

    # FOR REGRESSION TRAINING AND TESTING
    indexedTRAINreg.cache()
    indexedTESTreg.cache()
    oneHotTRAINreg.cache()
    oneHotTESTreg.cache()

    # SCALED FEATURES
    oneHotTRAINregScaled.cache()
    oneHotTESTregScaled.cache()

    # PRINT ELAPSED TIME
    timeend = datetime.datetime.now()
    timedelta = round((timeend-timestart).total_seconds(), 2) 
    print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 

**SAÍDA:** 

Tempo decorrido para executar acima célula: 0.15 segundos

## <a name="predict-whether-or-not-a-tip-is-paid-with-binary-classification-models"></a>Prever ou não uma sugestão é paga com os modelos de classificação binária
Esta secção mostra como utilizar três modelos para a tarefa de classificação binária de prever se ou não uma sugestão é paga para uma viagem taxi. Os modelos apresentados são:

* Regressão logística da regularized 
* Modelo de floresta aleatório
* Árvores de aumento gradação

Cada modelo de criação de secção de código é dividido em passos: 

1. **Modelo de formação** dados com um conjunto de parâmetros
2. **Modelo de avaliação** num conjunto de dados de teste com métricas
3. **Guardar modelo** num blob para consumo futuro

### <a name="classification-using-logistic-regression"></a>Classificação de regressão logística da
O código nesta secção mostra como preparar, avaliar e guardar um modelo de regressão logística com [LBFGS](https://en.wikipedia.org/wiki/Broyden%E2%80%93Fletcher%E2%80%93Goldfarb%E2%80%93Shanno_algorithm) que prevê ou não uma sugestão é paga para uma viagem no NYC taxi viagem e fare conjunto de dados.

**Preparar o modelo de regressão logística da utilização CV e hyperparameter varrimento**

    # LOGISTIC REGRESSION CLASSIFICATION WITH CV AND HYPERPARAMETER SWEEPING

    # GET ACCURACY FOR HYPERPARAMETERS BASED ON CROSS-VALIDATION IN TRAINING DATA-SET

    # RECORD START TIME
    timestart = datetime.datetime.now()

    # LOAD LIBRARIES
    from pyspark.mllib.classification import LogisticRegressionWithLBFGS 
    from sklearn.metrics import roc_curve,auc
    from pyspark.mllib.evaluation import BinaryClassificationMetrics
    from pyspark.mllib.evaluation import MulticlassMetrics


    # CREATE MODEL WITH ONE SET OF PARAMETERS
    logitModel = LogisticRegressionWithLBFGS.train(oneHotTRAINbinary, iterations=20, initialWeights=None, 
                                                   regParam=0.01, regType='l2', intercept=True, corrections=10, 
                                                   tolerance=0.0001, validateData=True, numClasses=2)

    # PRINT COEFFICIENTS AND INTERCEPT OF THE MODEL
    # NOTE: There are 20 coefficient terms for the 10 features, 
    #       and the different categories for features: vendorVec (2), rateVec, paymentVec (6), TrafficTimeBinsVec (4)
    print("Coefficients: " + str(logitModel.weights))
    print("Intercept: " + str(logitModel.intercept))

    # PRINT ELAPSED TIME
    timeend = datetime.datetime.now()
    timedelta = round((timeend-timestart).total_seconds(), 2) 
    print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 


**SAÍDA:** 

Coefficients: [0.0082065285375-0.0223675576104,-0.0183812028036, - 3.48124578069e-05,-0.00247646947233,-0.00165897881503, 0.0675394837328,-0.111823113101,-0.324609912762,-0.204549780032,-1.36499216354, 0.591088507921,-0.664263411392,-1.00439726852, 3.46567827545,-3.51025855172,-0.0471341112232,-0.043521833294, 0.000243375810385, 0.054518719222]

Intercept:-0.0111216486893

Tempo decorrido para executar acima célula: 14.43 segundos

**Avalie o modelo de classificação binária com métricas padrão**

    #EVALUATE LOGISTIC REGRESSION MODEL WITH LBFGS

    # RECORD START TIME
    timestart = datetime.datetime.now()

    # PREDICT ON TEST DATA WITH MODEL
    predictionAndLabels = oneHotTESTbinary.map(lambda lp: (float(logitModel.predict(lp.features)), lp.label))

    # INSTANTIATE METRICS OBJECT
    metrics = BinaryClassificationMetrics(predictionAndLabels)

    # AREA UNDER PRECISION-RECALL CURVE
    print("Area under PR = %s" % metrics.areaUnderPR)

    # AREA UNDER ROC CURVE
    print("Area under ROC = %s" % metrics.areaUnderROC)
    metrics = MulticlassMetrics(predictionAndLabels)

    # OVERALL STATISTICS
    precision = metrics.precision()
    recall = metrics.recall()
    f1Score = metrics.fMeasure()
    print("Summary Stats")
    print("Precision = %s" % precision)
    print("Recall = %s" % recall)
    print("F1 Score = %s" % f1Score)


    ## SAVE MODEL WITH DATE-STAMP
    datestamp = unicode(datetime.datetime.now()).replace(' ','').replace(':','_');
    logisticregressionfilename = "LogisticRegressionWithLBFGS_" + datestamp;
    dirfilename = modelDir + logisticregressionfilename;
    logitModel.save(sc, dirfilename);

    # OUTPUT PROBABILITIES AND REGISTER TEMP TABLE
    logitModel.clearThreshold(); # This clears threshold for classification (0.5) and outputs probabilities
    predictionAndLabelsDF = predictionAndLabels.toDF()
    predictionAndLabelsDF.registerTempTable("tmp_results");

    # PRINT ELAPSED TIME
    timeend = datetime.datetime.now()
    timedelta = round((timeend-timestart).total_seconds(), 2) 
    print "Time taken to execute above cell: " + str(timedelta) + " seconds";

**SAÍDA:** 

Área em ELI = 0.985297691373

Área em ROC = 0.983714670256

Estatísticas de resumidas

Precisão = 0.984304060189

Recuperar = 0.984304060189

F1 Pontuar = 0.984304060189

Tempo decorrido para executar acima célula: 57.61 segundos

**Desenhar a curva ROC.**

O *predictionAndLabelsDF* está registado como uma tabela, *tmp_results*, na célula anterior. *tmp_results* podem ser utilizadas para consultas de efetue e resultados de saída para o intervalo de dados sqlResults para representar. Eis o código.

    # QUERY RESULTS                              
    %%sql -q -o sqlResults
    SELECT * from tmp_results


Eis o código para fazer predições e desenhar a curva ROC.

    # MAKE PREDICTIONS AND PLOT ROC-CURVE

    # RUN THE CODE LOCALLY ON THE JUPYTER SERVER AND IMPORT LIBRARIES
    %%local
    %matplotlib inline
    from sklearn.metrics import roc_curve,auc

    # MAKE PREDICTIONS
    predictions_pddf = test_predictions.rename(columns={'_1': 'probability', '_2': 'label'})
    prob = predictions_pddf["probability"] 
    fpr, tpr, thresholds = roc_curve(predictions_pddf['label'], prob, pos_label=1);
    roc_auc = auc(fpr, tpr)

    # PLOT ROC CURVE
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


**SAÍDA:**

![Regressão logística da ROC curve.png](./media/spark-data-exploration-modeling/logistic-regression-roc-curve.png)

### <a name="random-forest-classification"></a>Classificação de floresta aleatório
O código nesta secção mostra como preparar, avaliar e guardar um modelo de floresta aleatório que prevê ou não uma sugestão é paga para uma viagem no NYC taxi viagem e fare conjunto de dados.

    #PREDICT WHETHER A TIP IS PAID OR NOT USING RANDOM FOREST

    # RECORD START TIME
    timestart = datetime.datetime.now()

    # LOAD PYSPARK LIBRARIES
    from pyspark.mllib.tree import RandomForest, RandomForestModel
    from pyspark.mllib.util import MLUtils
    from pyspark.mllib.evaluation import BinaryClassificationMetrics
    from pyspark.mllib.evaluation import MulticlassMetrics

    # SPECIFY NUMBER OF CATEGORIES FOR CATEGORICAL FEATURES. FEATURE #0 HAS 2 CATEGORIES, FEATURE #2 HAS 2 CATEGORIES, AND SO ON
    categoricalFeaturesInfo={0:2, 1:2, 2:6, 3:4}

    # TRAIN RANDOMFOREST MODEL
    rfModel = RandomForest.trainClassifier(indexedTRAINbinary, numClasses=2, 
                                           categoricalFeaturesInfo=categoricalFeaturesInfo,
                                           numTrees=25, featureSubsetStrategy="auto",
                                           impurity='gini', maxDepth=5, maxBins=32)
    ## UN-COMMENT IF YOU WANT TO PRINT TREES
    #print('Learned classification forest model:')
    #print(rfModel.toDebugString())

    # PREDICT ON TEST DATA AND EVALUATE
    predictions = rfModel.predict(indexedTESTbinary.map(lambda x: x.features))
    predictionAndLabels = indexedTESTbinary.map(lambda lp: lp.label).zip(predictions)

    # AREA UNDER ROC CURVE
    metrics = BinaryClassificationMetrics(predictionAndLabels)
    print("Area under ROC = %s" % metrics.areaUnderROC)

    # PERSIST MODEL IN BLOB
    datestamp = unicode(datetime.datetime.now()).replace(' ','').replace(':','_');
    rfclassificationfilename = "RandomForestClassification_" + datestamp;
    dirfilename = modelDir + rfclassificationfilename;

    rfModel.save(sc, dirfilename);

    # PRINT ELAPSED TIME
    timeend = datetime.datetime.now()
    timedelta = round((timeend-timestart).total_seconds(), 2) 
    print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 

**SAÍDA:**

Área em ROC = 0.985297691373

Tempo decorrido para executar acima célula: 31.09 segundos

### <a name="gradient-boosting-trees-classification"></a>Classificação de árvores de aumento gradação
O código nesta secção mostra como preparar, avaliar e guardar um modelo de árvores de aumento gradação que prevê ou não uma sugestão é paga para uma viagem em viagem de taxi NYC e fare conjunto de dados.

    #PREDICT WHETHER A TIP IS PAID OR NOT USING GRADIENT BOOSTING TREES

    # RECORD START TIME
    timestart = datetime.datetime.now()

    # LOAD PYSPARK LIBRARIES
    from pyspark.mllib.tree import GradientBoostedTrees, GradientBoostedTreesModel

    # SPECIFY NUMBER OF CATEGORIES FOR CATEGORICAL FEATURES. FEATURE #0 HAS 2 CATEGORIES, FEATURE #2 HAS 2 CATEGORIES, AND SO ON
    categoricalFeaturesInfo={0:2, 1:2, 2:6, 3:4}

    gbtModel = GradientBoostedTrees.trainClassifier(indexedTRAINbinary, categoricalFeaturesInfo=categoricalFeaturesInfo, numIterations=5)
    ## UNCOMMENT IF YOU WANT TO PRINT TREE DETAILS
    #print('Learned classification GBT model:')
    #print(bgtModel.toDebugString())

    # PREDICT ON TEST DATA AND EVALUATE
    predictions = gbtModel.predict(indexedTESTbinary.map(lambda x: x.features))
    predictionAndLabels = indexedTESTbinary.map(lambda lp: lp.label).zip(predictions)

    # AREA UNDER ROC CURVE
    metrics = BinaryClassificationMetrics(predictionAndLabels)
    print("Area under ROC = %s" % metrics.areaUnderROC)

    # PERSIST MODEL IN A BLOB
    datestamp = unicode(datetime.datetime.now()).replace(' ','').replace(':','_');
    btclassificationfilename = "GradientBoostingTreeClassification_" + datestamp;
    dirfilename = modelDir + btclassificationfilename;

    gbtModel.save(sc, dirfilename)

    # PRINT ELAPSED TIME
    timeend = datetime.datetime.now()
    timedelta = round((timeend-timestart).total_seconds(), 2) 
    print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 


**SAÍDA:**

Área em ROC = 0.985297691373

Tempo decorrido para executar acima célula: 19.76 segundos

## <a name="predict-tip-amounts-for-taxi-trips-with-regression-models"></a>Prever quantidades de sugestão para viagens taxi com os modelos de regressão
Esta secção mostra como utilizar três modelos para a tarefa de regressão de prever a quantidade da sugestão paga para uma viagem taxi com base nas outras funcionalidades de sugestão. Os modelos apresentados são:

* Regressão linear regularized
* Floresta aleatória
* Árvores de aumento gradação

Estes modelos foram descritos na introdução. Cada modelo de criação de secção de código é dividido em passos: 

1. **Modelo de formação** dados com um conjunto de parâmetros
2. **Modelo de avaliação** num conjunto de dados de teste com métricas
3. **Guardar modelo** num blob para consumo futuro

### <a name="linear-regression-with-sgd"></a>Regressão linear com SGD
O código nesta secção mostra como utilizar funcionalidades dimensionadas para preparar um regressão linear que utiliza descendente gradação stochastic (SGD) para a Otimização e como Pontuar, avaliar e guarde o modelo no armazenamento de Blobs do Azure (WASB).

> [!TIP]
> Na nossa experiência, podem existir problemas com a convergência de LinearRegressionWithSGD modelos e os parâmetros têm de ser alterada/otimizada cuidadosamente para obtenção de um modelo válido. Dimensionamento significativamente das variáveis ajuda-o com convergência. 
> 
> 

    #PREDICT TIP AMOUNTS USING LINEAR REGRESSION WITH SGD

    # RECORD START TIME
    timestart = datetime.datetime.now()

    # LOAD LIBRARIES
    from pyspark.mllib.regression import LabeledPoint, LinearRegressionWithSGD, LinearRegressionModel
    from pyspark.mllib.evaluation import RegressionMetrics
    from scipy import stats

    # USE SCALED FEATURES TO TRAIN MODEL
    linearModel = LinearRegressionWithSGD.train(oneHotTRAINregScaled, iterations=100, step = 0.1, regType='l2', regParam=0.1, intercept = True)

    # PRINT COEFFICIENTS AND INTERCEPT OF THE MODEL
    # NOTE: There are 20 coefficient terms for the 10 features, 
    #       and the different categories for features: vendorVec (2), rateVec, paymentVec (6), TrafficTimeBinsVec (4)
    print("Coefficients: " + str(linearModel.weights))
    print("Intercept: " + str(linearModel.intercept))

    # SCORE ON SCALED TEST DATA-SET & EVALUATE
    predictionAndLabels = oneHotTESTregScaled.map(lambda lp: (float(linearModel.predict(lp.features)), lp.label))
    testMetrics = RegressionMetrics(predictionAndLabels)

    # PRINT TEST METRICS
    print("RMSE = %s" % testMetrics.rootMeanSquaredError)
    print("R-sqr = %s" % testMetrics.r2)

    # SAVE MODEL WITH DATE-STAMP IN THE DEFAULT BLOB FOR THE CLUSTER
    datestamp = unicode(datetime.datetime.now()).replace(' ','').replace(':','_');
    linearregressionfilename = "LinearRegressionWithSGD_" + datestamp;
    dirfilename = modelDir + linearregressionfilename;

    linearModel.save(sc, dirfilename)

    # PRINT ELAPSED TIME
    timeend = datetime.datetime.now()
    timedelta = round((timeend-timestart).total_seconds(), 2) 
    print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 

**SAÍDA:**

Coefficients: [0.00457675809917-0.0226314167349,-0.0191910355236, 0.246793409578, 0.312047890459, 0.359634405999, 0.00928692253981,-0.000987181489428,-0.0888306617845, 0.0569376211553, 0.115519551711, 0.149250164995,- 0.00990211159703-0.00637410344522, 0.545083566179,-0.536756072402, 0.0105762393099,-0.0130117577055, 0.0129304737772,-0.00171065945959]

Intercetar: 0.853872718283

RMSE = 1.24190115863

R sqr = 0.608017146081

Tempo decorrido para executar acima célula: 58.42 segundos

### <a name="random-forest-regression"></a>Regressão aleatório floresta
O código nesta secção mostra como preparar, avaliar e guardar um regressão de floresta aleatório que prevê a quantidade de sugestão de dados NYC taxi viagem.

    #PREDICT TIP AMOUNTS USING RANDOM FOREST

    # RECORD START TIME
    timestart= datetime.datetime.now()

    # LOAD PYSPARK LIBRARIES
    from pyspark.mllib.tree import RandomForest, RandomForestModel
    from pyspark.mllib.util import MLUtils
    from pyspark.mllib.evaluation import RegressionMetrics


    ## TRAIN MODEL
    categoricalFeaturesInfo={0:2, 1:2, 2:6, 3:4}
    rfModel = RandomForest.trainRegressor(indexedTRAINreg, categoricalFeaturesInfo=categoricalFeaturesInfo,
                                        numTrees=25, featureSubsetStrategy="auto",
                                        impurity='variance', maxDepth=10, maxBins=32)
    ## UN-COMMENT IF YOU WANT TO PRING TREES
    #print('Learned classification forest model:')
    #print(rfModel.toDebugString())

    ## PREDICT AND EVALUATE ON TEST DATA-SET
    predictions = rfModel.predict(indexedTESTreg.map(lambda x: x.features))
    predictionAndLabels = oneHotTESTreg.map(lambda lp: lp.label).zip(predictions)

    # TEST METRICS
    testMetrics = RegressionMetrics(predictionAndLabels)
    print("RMSE = %s" % testMetrics.rootMeanSquaredError)
    print("R-sqr = %s" % testMetrics.r2)

    # SAVE MODEL IN BLOB
    datestamp = unicode(datetime.datetime.now()).replace(' ','').replace(':','_');
    rfregressionfilename = "RandomForestRegression_" + datestamp;
    dirfilename = modelDir + rfregressionfilename;

    rfModel.save(sc, dirfilename);

    # PRINT ELAPSED TIME
    timeend = datetime.datetime.now()
    timedelta = round((timeend-timestart).total_seconds(), 2) 
    print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 

**SAÍDA:**

RMSE = 0.891209218139

R sqr = 0.759661334921

Tempo decorrido para executar acima célula: 49.21 segundos

### <a name="gradient-boosting-trees-regression"></a>Regressão de árvores de aumento gradação
O código nesta secção mostra como preparar, avaliar e guardar um modelo de árvores de aumento gradação que prevê a quantidade de sugestão de dados NYC taxi viagem.

* * Dar formação e avaliar * *

    #PREDICT TIP AMOUNTS USING GRADIENT BOOSTING TREES

    # RECORD START TIME
    timestart= datetime.datetime.now()

    # LOAD PYSPARK LIBRARIES
    from pyspark.mllib.tree import GradientBoostedTrees, GradientBoostedTreesModel
    from pyspark.mllib.util import MLUtils

    ## TRAIN MODEL
    categoricalFeaturesInfo={0:2, 1:2, 2:6, 3:4}
    gbtModel = GradientBoostedTrees.trainRegressor(indexedTRAINreg, categoricalFeaturesInfo=categoricalFeaturesInfo, 
                                                    numIterations=10, maxBins=32, maxDepth = 4, learningRate=0.1)

    ## EVALUATE A TEST DATA-SET
    predictions = gbtModel.predict(indexedTESTreg.map(lambda x: x.features))
    predictionAndLabels = indexedTESTreg.map(lambda lp: lp.label).zip(predictions)

    # TEST METRICS
    testMetrics = RegressionMetrics(predictionAndLabels)
    print("RMSE = %s" % testMetrics.rootMeanSquaredError)
    print("R-sqr = %s" % testMetrics.r2)

    # SAVE MODEL IN BLOB
    datestamp = unicode(datetime.datetime.now()).replace(' ','').replace(':','_');
    btregressionfilename = "GradientBoostingTreeRegression_" + datestamp;
    dirfilename = modelDir + btregressionfilename;
    gbtModel.save(sc, dirfilename)

    # CONVER RESULTS TO DF AND REGISER TEMP TABLE
    test_predictions = sqlContext.createDataFrame(predictionAndLabels)
    test_predictions.registerTempTable("tmp_results");

    # PRINT ELAPSED TIME
    timeend = datetime.datetime.now()
    timedelta = round((timeend-timestart).total_seconds(), 2) 
    print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 

**SAÍDA:**

RMSE = 0.908473148639

R sqr = 0.753835096681

Tempo decorrido para executar acima célula: 34.52 segundos

**Desenhar**

*tmp_results* está registado como uma tabela do Hive na célula anterior. Resultados da tabela são de saída para o *sqlResults* moldura de dados para representar. Eis o código

    # PLOT SCATTER-PLOT BETWEEN ACTUAL AND PREDICTED TIP VALUES

    # SELECT RESULTS
    %%sql -q -o sqlResults
    SELECT * from tmp_results

Eis o código para representar os dados utilizando o servidor do Jupyter.

    # RUN THE CODE LOCALLY ON THE JUPYTER SERVER AND IMPORT LIBRARIES
    %%local
    %matplotlib inline
    import numpy as np

    # PLOT 
    ax = test_predictions_pddf.plot(kind='scatter', figsize = (6,6), x='_1', y='_2', color='blue', alpha = 0.25, label='Actual vs. predicted');
    fit = np.polyfit(test_predictions_pddf['_1'], test_predictions_pddf['_2'], deg=1)
    ax.set_title('Actual vs. Predicted Tip Amounts ($)')
    ax.set_xlabel("Actual")
    ax.set_ylabel("Predicted")
    ax.plot(test_predictions_pddf['_1'], fit[0] * test_predictions_pddf['_1'] + fit[1], color='magenta')
    plt.axis([-1, 20, -1, 20])
    plt.show(ax)


**SAÍDA:**

![Real-vs-prever-sugestão-quantidades](./media/spark-data-exploration-modeling/actual-vs-predicted-tips.png)

## <a name="clean-up-objects-from-memory"></a>Limpar objetos da memória
Utilize `unpersist()` eliminar objetos em cache na memória.

    # REMOVE ORIGINAL DFs
    taxi_df_train_cleaned.unpersist()
    taxi_df_train_with_newFeatures.unpersist()

    # FOR BINARY CLASSIFICATION TRAINING AND TESTING
    indexedTRAINbinary.unpersist()
    indexedTESTbinary.unpersist()
    oneHotTRAINbinary.unpersist()
    oneHotTESTbinary.unpersist()

    # FOR REGRESSION TRAINING AND TESTING
    indexedTRAINreg.unpersist()
    indexedTESTreg.unpersist()
    oneHotTRAINreg.unpersist()
    oneHotTESTreg.unpersist()

    # SCALED FEATURES
    oneHotTRAINregScaled.unpersist()
    oneHotTESTregScaled.unpersist()


## <a name="record-storage-locations-of-the-models-for-consumption-and-scoring"></a>Localizações de armazenamento de registo dos modelos de consumo e classificação
Para consumir e Pontuar um conjunto de dados independente descrito a [pontuação e avaliar os modelos de incorporadas do Spark machine learning](spark-model-consumption.md) tópico, tem de copiar e colar estes nomes de ficheiro que contém os modelos guardados criados aqui para o consumo de Bloco de notas do Jupyter. Eis o código para imprimir os caminhos para os ficheiros de modelo que tem de existir.

    # MODEL FILE LOCATIONS FOR CONSUMPTION
    print "logisticRegFileLoc = modelDir + \"" + logisticregressionfilename + "\"";
    print "linearRegFileLoc = modelDir + \"" + linearregressionfilename + "\"";
    print "randomForestClassificationFileLoc = modelDir + \"" + rfclassificationfilename + "\"";
    print "randomForestRegFileLoc = modelDir + \"" + rfregressionfilename + "\"";
    print "BoostedTreeClassificationFileLoc = modelDir + \"" + btclassificationfilename + "\"";
    print "BoostedTreeRegressionFileLoc = modelDir + \"" + btregressionfilename + "\"";


**SAÍDA**

logisticRegFileLoc = modelDir + "LogisticRegressionWithLBFGS_2016-05-0317_03_23.516568"

linearRegFileLoc = modelDir + "LinearRegressionWithSGD_2016-05-0317_05_21.577773"

randomForestClassificationFileLoc = modelDir + "RandomForestClassification_2016-05-0317_04_11.950206"

randomForestRegFileLoc = modelDir + "RandomForestRegression_2016-05-0317_06_08.723736"

BoostedTreeClassificationFileLoc = modelDir + "GradientBoostingTreeClassification_2016-05-0317_04_36.346583"

BoostedTreeRegressionFileLoc = modelDir + "GradientBoostingTreeRegression_2016-05-0317_06_51.737282"

## <a name="whats-next"></a>Passos seguintes?
Agora que criou os modelos de regressão e classificação com o Spark MlLib, está pronto para saber como Pontuar e avaliar estes modelos. A exploração de dados avançadas e modelação de bloco de notas dives mais aprofundadas para incluindo validação cruzada, hyper varrimento, parâmetro e modelo de avaliação. 

**Consumo de modelo:** para saber como Pontuar e avaliar os modelos de classificação e regressão criados neste tópico, consulte o artigo [classificação e avaliar os modelos de incorporadas do Spark machine learning](spark-model-consumption.md).

**Validação cruzada e hyperparameter varrimento**: consulte [avançada de exploração de dados e modelação com o Spark](spark-advanced-data-exploration-modeling.md) na forma como os modelos podem ser preparado varrimento de validação cruzada e hyper parâmetro a utilizar

