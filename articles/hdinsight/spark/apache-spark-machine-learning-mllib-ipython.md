---
title: "Exemplo de aprendizagem máquina com o MLlib do Spark no HDInsight - Azure | Microsoft Docs"
description: "Saiba como utilizar o Spark MLlib para criar uma aplicação de aprendizagem máquina que analisa um conjunto de dados a utilizar a classificação através de regressão logística da."
keywords: "o spark aprendizagem automática, exemplo do spark machine learning"
services: hdinsight
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: c0fd4baa-946d-4e03-ad2c-a03491bd90c8
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/28/2017
ms.author: nitinme
ms.openlocfilehash: 75fc097013251eb839cb53dc2558c8f52a745e38
ms.sourcegitcommit: f8437edf5de144b40aed00af5c52a20e35d10ba1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/03/2017
---
# <a name="use-spark-mllib-to-build-a-machine-learning-application-and-analyze-a-dataset"></a>Utilizar o Spark MLlib para criar uma aplicação de aprendizagem e analisar um conjunto de dados

Saiba como utilizar o Spark **MLlib** para criar uma aplicação para efetuar a Análise Preditiva simple um conjunto de dados aberta de aprendizagem. De incorporada aprendizagem do Spark bibliotecas, este exemplo utiliza *classificação* através de regressão logística da. 

> [!TIP]
> Este exemplo também está disponível como um bloco de notas do Jupyter num cluster do Spark (Linux) que criar no HDInsight. A experiência de bloco de notas permite-lhe executar os fragmentos de Python do bloco de notas. Para seguir o tutorial do dentro de um bloco de notas, crie um cluster do Spark e iniciar um bloco de notas do Jupyter (`https://CLUSTERNAME.azurehdinsight.net/jupyter`). Em seguida, execute o bloco de notas **Spark Machine Learning - Análise Preditiva nos dados de inspeção de prato utilizando MLlib.ipynb** sob o **Python** pasta.
>
>

MLlib é uma biblioteca de Spark core que fornece utilitários muitos útil para tarefas de machine learning, incluindo utilitários que são adequados para:

* Classificação
* Regressão
* Clustering
* Modelação de tópico
* Decomposição de valor único (o programa) e análise de componente principal (PCA)
* Hipótese de teste e calcular estatísticas de exemplo

## <a name="what-are-classification-and-logistic-regression"></a>Quais são classificação e regressão logística da?
*Classificação*, aprendizagem popular tarefa, é o processo de ordenação de dados de entrada em categorias. É a tarefa de um algoritmo de classificação para descobrir como atribuir "etiquetas" para dados que fornece de entrada. Por exemplo, pode considerar um algoritmo do machine learning que aceita as cotações informações como entrada e divide o gráfico de cotações em duas categorias: ações que deve propor e ações que deve manter.

Regressão logística da é o algoritmo que utiliza para classificação. A regressão logística do Spark API é útil para *classificação binária*, ou classificar os dados de entrada para um dos dois grupos. Para mais informações sobre regressões logística da, consulte [Wikipedia](https://en.wikipedia.org/wiki/Logistic_regression).

Em resumo, o processo de regressão logística da produz um *logística da função* que podem ser utilizados para prever a probabilidade de que um vetor de entrada pertence um grupo ou outros.  

## <a name="predictive-analysis-example-on-food-inspection-data"></a>Exemplo de Análise Preditiva prato inspeção de dados
Neste exemplo, utilizar o Spark para executar algumas Análise Preditiva nos dados de inspeção de prato (**Food_Inspections1.csv**) que foi adquirido através de [portal de dados de cidade de Chicago](https://data.cityofchicago.org/). Este conjunto de dados contém informações sobre inspections de estabelecimento de prato que foram efetuadas em Chicago, incluindo informações sobre cada estabelecimento, violações encontradas (se aplicável) e os resultados da inspeção da. O ficheiro de dados CSV já se encontra disponível na conta de armazenamento associada ao cluster **/HdiSamples/HdiSamples/FoodInspectionData/Food_Inspections1.csv**.

Os passos abaixo, desenvolver um modelo para ver que demora a transmitir ou efetuar uma inspeção prato.

## <a name="start-building-a-spark-mmlib-machine-learning-app"></a>Iniciar criação de um Spark MMLib de machine learning aplicação
1. No [portal do Azure](https://portal.azure.com/), no startboard, clique no mosaico relativo ao cluster do Spark (se o tiver afixado ao startboard). Também pode navegar até ao cluster em **Procurar Tudo** > **Clusters do HDInsight**.   
1. No painel do cluster do Spark, clique em **Dashboard de Clusters** e, em seguida, clique em **Bloco de Notas do Jupyter**. Se lhe for solicitado, introduza as credenciais de administrador do cluster.

   > [!NOTE]
   > Também pode aceder ao Bloco de Notas do Jupyter para o cluster abrindo o seguinte URL no seu browser. Substitua **CLUSTERNAME** pelo nome do cluster:
   >
   > `https://CLUSTERNAME.azurehdinsight.net/jupyter`
   >
   >
1. Crie um bloco de notas. Clique em **Novo** e, em seguida, clique em **PySpark**.

    ![Criar um bloco de notas do Jupyter](./media/apache-spark-machine-learning-mllib-ipython/spark-machine-learning-create-jupyter.png "criar um novo bloco de notas do Jupyter")
1. É criado e aberto um novo bloco de notas com o nome Untitled.pynb. Clique no nome do bloco de notas na parte superior e introduza um nome amigável.

    ![Forneça um nome para o bloco de notas](./media/apache-spark-machine-learning-mllib-ipython/spark-machine-learning-name-jupyter.png "Forneça um nome para o bloco de notas")
1. Uma vez que criou um bloco de notas com o kernel do PySpark, não é necessário criar quaisquer contextos explicitamente. Os contextos do Spark e do Hive são criados automaticamente quando executa a primeira célula do código. Pode começar a criar o machine learning aplicação através da importação os tipos necessários para este cenário. Para tal, coloque o cursor na célula e prima **SHIFT + ENTER**.

        from pyspark.ml import Pipeline
        from pyspark.ml.classification import LogisticRegression
        from pyspark.ml.feature import HashingTF, Tokenizer
        from pyspark.sql import Row
        from pyspark.sql.functions import UserDefinedFunction
        from pyspark.sql.types import *

## <a name="construct-an-input-dataframe"></a>Construir uma entrada dataframe
Podemos utilizar `sqlContext` para efetuar transformações em dados estruturados. A primeira tarefa está a carregar os dados de exemplo ((**Food_Inspections1.csv**)) para um Spark SQL *dataframe*.

1. Porque os dados não processados estão num formato CSV, é necessário utilizar o contexto de Spark extraia cada linha do ficheiro para a memória como texto não estruturado; em seguida, pode utilizar bibliotecas CSV do Python para analisar individualmente cada linha.

        def csvParse(s):
            import csv
            from StringIO import StringIO
            sio = StringIO(s)
            value = csv.reader(sio).next()
            sio.close()
            return value

        inspections = sc.textFile('wasb:///HdiSamples/HdiSamples/FoodInspectionData/Food_Inspections1.csv')\
                        .map(csvParse)
1. Agora temos o ficheiro CSV como uma RDD.  Para compreender o esquema dos dados, vamos obter uma linha a RDD.

        inspections.take(1)

    Deve ver um resultado como o seguinte:

        # -----------------
        # THIS IS AN OUTPUT
        # -----------------

        [['413707',
          'LUNA PARK INC',
          'LUNA PARK  DAY CARE',
          '2049789',
          "Children's Services Facility",
          'Risk 1 (High)',
          '3250 W FOSTER AVE ',
          'CHICAGO',
          'IL',
          '60625',
          '09/21/2010',
          'License-Task Force',
          'Fail',
          '24. DISH WASHING FACILITIES: PROPERLY DESIGNED, CONSTRUCTED, MAINTAINED, INSTALLED, LOCATED AND OPERATED - Comments: All dishwashing machines must be of a type that complies with all requirements of the plumbing section of the Municipal Code of Chicago and Rules and Regulation of the Board of Health. OBSEVERD THE 3 COMPARTMENT SINK BACKING UP INTO THE 1ST AND 2ND COMPARTMENT WITH CLEAR WATER AND SLOWLY DRAINING OUT. INST NEED HAVE IT REPAIR. CITATION ISSUED, SERIOUS VIOLATION 7-38-030 H000062369-10 COURT DATE 10-28-10 TIME 1 P.M. ROOM 107 400 W. SURPERIOR. | 36. LIGHTING: REQUIRED MINIMUM FOOT-CANDLES OF LIGHT PROVIDED, FIXTURES SHIELDED - Comments: Shielding to protect against broken glass falling into food shall be provided for all artificial lighting sources in preparation, service, and display facilities. LIGHT SHIELD ARE MISSING UNDER HOOD OF  COOKING EQUIPMENT AND NEED TO REPLACE LIGHT UNDER UNIT. 4 LIGHTS ARE OUT IN THE REAR CHILDREN AREA,IN THE KINDERGARDEN CLASS ROOM. 2 LIGHT ARE OUT EAST REAR, LIGHT FRONT WEST ROOM. NEED TO REPLACE ALL LIGHT THAT ARE NOT WORKING. | 35. WALLS, CEILINGS, ATTACHED EQUIPMENT CONSTRUCTED PER CODE: GOOD REPAIR, SURFACES CLEAN AND DUST-LESS CLEANING METHODS - Comments: The walls and ceilings shall be in good repair and easily cleaned. MISSING CEILING TILES WITH STAINS IN WEST,EAST, IN FRONT AREA WEST, AND BY THE 15MOS AREA. NEED TO BE REPLACED. | 32. FOOD AND NON-FOOD CONTACT SURFACES PROPERLY DESIGNED, CONSTRUCTED AND MAINTAINED - Comments: All food and non-food contact equipment and utensils shall be smooth, easily cleanable, and durable, and shall be in good repair. SPLASH GUARDED ARE NEEDED BY THE EXPOSED HAND SINK IN THE KITCHEN AREA | 34. FLOORS: CONSTRUCTED PER CODE, CLEANED, GOOD REPAIR, COVING INSTALLED, DUST-LESS CLEANING METHODS USED - Comments: The floors shall be constructed per code, be smooth and easily cleaned, and be kept clean and in good repair. INST NEED TO ELEVATE ALL FOOD ITEMS 6INCH OFF THE FLOOR 6 INCH AWAY FORM WALL.  ',
          '41.97583445690982',
          '-87.7107455232781',
          '(41.97583445690982, -87.7107455232781)']]
1. A saída anterior nos dá uma ideia do esquema do ficheiro de entrada. Inclui o nome de cada estabelecimento, o tipo de estabelecimento, o endereço, os dados dos inspections e a localização, entre outros. Vamos selecione algumas colunas que são úteis para o nosso Análise Preditiva e os resultados de grupo como um dataframe, que, em seguida, são utilizadas para criar uma tabela temporária.

        schema = StructType([
        StructField("id", IntegerType(), False),
        StructField("name", StringType(), False),
        StructField("results", StringType(), False),
        StructField("violations", StringType(), True)])

        df = sqlContext.createDataFrame(inspections.map(lambda l: (int(l[0]), l[1], l[12], l[13])) , schema)
        df.registerTempTable('CountResults')
1. Agora temos um *dataframe*, `df` no qual vamos pode executar uma análise do nosso. Além disso, temos uma chamada de tabela temporária **CountResults**. Iremos tiver incluído quatro colunas de interesse no dataframe: **id**, **nome**, **resultados**, e **violações**.

    Vamos obter um exemplo dos dados pequeno:

        df.show(5)

    Deve ver um resultado como o seguinte:

        # -----------------
        # THIS IS AN OUTPUT
        # -----------------

        +------+--------------------+-------+--------------------+
        |    id|                name|results|          violations|
        +------+--------------------+-------+--------------------+
        |413707|       LUNA PARK INC|   Fail|24. DISH WASHING ...|
        |391234|       CAFE SELMARIE|   Fail|2. FACILITIES TO ...|
        |413751|          MANCHU WOK|   Pass|33. FOOD AND NON-...|
        |413708|BENCHMARK HOSPITA...|   Pass|                    |
        |413722|           JJ BURGER|   Pass|                    |
        +------+--------------------+-------+--------------------+

## <a name="understand-the-data"></a>Compreender os dados
1. Vamos começar a ter uma noção do que contém o nosso conjunto de dados. Por exemplo, quais são os valores diferentes no **resultados** coluna?

        df.select('results').distinct().show()

    Deve ver um resultado como o seguinte:

        # -----------------
        # THIS IS AN OUTPUT
        # -----------------

        +--------------------+
        |             results|
        +--------------------+
        |                Fail|
        |Business Not Located|
        |                Pass|
        |  Pass w/ Conditions|
        |     Out of Business|
        +--------------------+
1. Uma visualização rápida pode ajudar-na razão sobre a distribuição estes resultados. Temos já os dados numa tabela temporária **CountResults**. Pode executar a seguinte consulta SQL na tabela para obter uma melhor compreensão da forma como os resultados são distribuídos.

        %%sql -o countResultsdf
        SELECT results, COUNT(results) AS cnt FROM CountResults GROUP BY results

    O `%%sql` magic seguido `-o countResultsdf` assegura que o resultado da consulta é persistente localmente no servidor do Jupyter (normalmente o headnode do cluster). O resultado é persistente como um [Pandas](http://pandas.pydata.org/) dataframe com o nome especificado **countResultsdf**.

    Deve ver um resultado como o seguinte:

    ![Resultado da consulta SQL](./media/apache-spark-machine-learning-mllib-ipython/spark-machine-learning-query-output.png "resultado de consulta SQL")

    Para obter mais informações sobre a magia `%%sql`, bem como sobre outras magias disponíveis com o kernel do PySpark, veja [Kernels disponíveis nos blocos de notas do Jupyter com clusters do Spark HDInsight](apache-spark-jupyter-notebook-kernels.md#parameters-supported-with-the-sql-magic).
1. Também pode utilizar Matplotlib, uma biblioteca utilizada para construir a visualização de dados, para criar um desenho. Porque o desenho tem de ser criado de localmente persistente **countResultsdf** dataframe, o fragmento de código tem de começar a `%%local` magic. Isto garante que o código é executado localmente no servidor do Jupyter.

        %%local
        %matplotlib inline
        import matplotlib.pyplot as plt

        labels = countResultsdf['results']
        sizes = countResultsdf['cnt']
        colors = ['turquoise', 'seagreen', 'mediumslateblue', 'palegreen', 'coral']
        plt.pie(sizes, labels=labels, autopct='%1.1f%%', colors=colors)
        plt.axis('equal')

    Deve ver um resultado como o seguinte:

    ![O Spark aprendizagem automática aplicação de saída - gráfico circular com os resultados da inspeção distintos cinco](./media/apache-spark-machine-learning-mllib-ipython/spark-machine-learning-result-output-1.png "Spark aprendizagem automática saída de resultado")
1. Pode ver que existem 5 distintos os resultados que pode ter uma inspeção:

   * Empresas não localizada
   * Falhar
   * Passagem
   * Suporte técnico com condições
   * Fora de negócio

     Informe-nos desenvolva um modelo que pode adivinhar o resultado de uma inspeção prato, dada as violações. Uma vez que regressão logística de um método de classificação binária, faz sentido para agrupar os nossos dados em duas categorias: **falhar** e **passar**. Um "passar com condições" ainda está a ser uma passagem de, pelo que, quando estamos a preparar o modelo, iremos considerar os dois resultados equivalente. Dados com os resultados ("Empresas não localizada" ou "fora do horário comercial") não são úteis para Removemos do nosso conjunto de preparação. Isto deve ser pode, uma vez que estas duas categorias constituem uma percentagem muito pequena dos resultados mesmo assim.
1. Informe-nos avançar e converter nosso dataframe existente (`df`) para um novo dataframe onde cada inspeção é representada como um par de violações de etiqueta. No nosso caso, uma etiqueta de `0.0` representa uma falha, uma etiqueta de `1.0` representa concluído com êxito e uma etiqueta de `-1.0` representa alguns resultados para além dos dois. Iremos filtrar esses outros resultados quando informática moldura de dados de novo.

        def labelForResults(s):
            if s == 'Fail':
                return 0.0
            elif s == 'Pass w/ Conditions' or s == 'Pass':
                return 1.0
            else:
                return -1.0
        label = UserDefinedFunction(labelForResults, DoubleType())
        labeledData = df.select(label(df.results).alias('label'), df.violations).where('label >= 0')

    Para ver quais os dados identificados parece, vamos obter uma linha.

        labeledData.take(1)

    Deve ver um resultado como o seguinte:

        # -----------------
        # THIS IS AN OUTPUT
        # -----------------

        [Row(label=0.0, violations=u"41. PREMISES MAINTAINED FREE OF LITTER, UNNECESSARY ARTICLES, CLEANING  EQUIPMENT PROPERLY STORED - Comments: All parts of the food establishment and all parts of the property used in connection with the operation of the establishment shall be kept neat and clean and should not produce any offensive odors.  REMOVE MATTRESS FROM SMALL DUMPSTER. | 35. WALLS, CEILINGS, ATTACHED EQUIPMENT CONSTRUCTED PER CODE: GOOD REPAIR, SURFACES CLEAN AND DUST-LESS CLEANING METHODS - Comments: The walls and ceilings shall be in good repair and easily cleaned.  REPAIR MISALIGNED DOORS AND DOOR NEAR ELEVATOR.  DETAIL CLEAN BLACK MOLD LIKE SUBSTANCE FROM WALLS BY BOTH DISH MACHINES.  REPAIR OR REMOVE BASEBOARD UNDER DISH MACHINE (LEFT REAR KITCHEN). SEAL ALL GAPS.  REPLACE MILK CRATES USED IN WALK IN COOLERS AND STORAGE AREAS WITH PROPER SHELVING AT LEAST 6' OFF THE FLOOR.  | 38. VENTILATION: ROOMS AND EQUIPMENT VENTED AS REQUIRED: PLUMBING: INSTALLED AND MAINTAINED - Comments: The flow of air discharged from kitchen fans shall always be through a duct to a point above the roofline.  REPAIR BROKEN VENTILATION IN MEN'S AND WOMEN'S WASHROOMS NEXT TO DINING AREA. | 32. FOOD AND NON-FOOD CONTACT SURFACES PROPERLY DESIGNED, CONSTRUCTED AND MAINTAINED - Comments: All food and non-food contact equipment and utensils shall be smooth, easily cleanable, and durable, and shall be in good repair.  REPAIR DAMAGED PLUG ON LEFT SIDE OF 2 COMPARTMENT SINK.  REPAIR SELF CLOSER ON BOTTOM LEFT DOOR OF 4 DOOR PREP UNIT NEXT TO OFFICE.")]

## <a name="create-a-logistic-regression-model-from-the-input-dataframe"></a>Criar um modelo de regressão logística da partir da entrada dataframe
É converter os dados identificados num formato que pode ser analisado por regressão logística da nossa tarefa final. A entrada de um algoritmo de regressão logística da deve ser um conjunto de *pares de vetor de etiqueta funcionalidade*, onde "vetor de funcionalidade" é um vetor de números que representa o ponto de entrada. Por isso, é necessário converter a coluna "violações", que é semiestruturados e contém muitas comentários no texto de gratuita, a uma matriz de números de real que uma máquina foi compreender facilmente.

É um padrão aprendizagem abordagem para o processamento de linguagem natural atribuir a cada palavra distinta "index" e, então, passe um vetor de para o algoritmo de aprendizagem, de modo a que o valor de cada índice contém a frequência relativa essa palavra na cadeia de texto.

MLlib fornece uma forma fácil para efetuar esta operação. Em primeiro lugar, "tokenize" cada cadeia violações ao obter as palavras individuais na cadeia de cada. Em seguida, utilize um `HashingTF` para converter um vetor de funcionalidade que, em seguida, pode ser transferido para o algoritmo de regressão logística para construir um modelo de cada conjunto de tokens. Podemos realizar todos estes passos numa sequência com um "pipeline".

    tokenizer = Tokenizer(inputCol="violations", outputCol="words")
    hashingTF = HashingTF(inputCol=tokenizer.getOutputCol(), outputCol="features")
    lr = LogisticRegression(maxIter=10, regParam=0.01)
    pipeline = Pipeline(stages=[tokenizer, hashingTF, lr])

    model = pipeline.fit(labeledData)

## <a name="evaluate-the-model-on-a-separate-test-dataset"></a>Avaliar o modelo de um conjunto de dados de teste separada
Podemos utilizar o modelo que criámos anteriormente a *prever* quais os resultados da nova inspections serão, com base nas violações que foram observadas. Iremos preparado neste modelo no conjunto de dados **Food_Inspections1.csv**. Informe-nos utilizar um segundo conjunto de dados, **Food_Inspections2.csv**ao *avaliar* à força deste modelo em novos dados. Este segundo conjunto de dados (**Food_Inspections2.csv**) já deve estar no contentor de armazenamento de predefinido associado com o cluster.

1. O fragmento seguinte cria um novo dataframe, **predictionsDf** que contém a predição gerada pelo modelo. O fragmento também cria uma tabela temporária denominada **Predições** com base no dataframe.

        testData = sc.textFile('wasb:///HdiSamples/HdiSamples/FoodInspectionData/Food_Inspections2.csv')\
                 .map(csvParse) \
                 .map(lambda l: (int(l[0]), l[1], l[12], l[13]))
        testDf = sqlContext.createDataFrame(testData, schema).where("results = 'Fail' OR results = 'Pass' OR results = 'Pass w/ Conditions'")
        predictionsDf = model.transform(testDf)
        predictionsDf.registerTempTable('Predictions')
        predictionsDf.columns

    Deve ver um resultado como o seguinte:

        # -----------------
        # THIS IS AN OUTPUT
        # -----------------

        ['id',
         'name',
         'results',
         'violations',
         'words',
         'features',
         'rawPrediction',
         'probability',
         'prediction']
1. Observe um serão as predições. Execute este fragmento:

        predictionsDf.take(1)

   Há uma predição a entrada primeiro o conjunto de dados de teste.
1. O `model.transform()` aplica-se a mesma transformação a quaisquer novos dados com o mesmo esquema sendo que chegam a uma predição de como classificar os dados. Podemos fazer algumas estatísticas simples para ter uma noção do foram como exata do nosso predições:

        numSuccesses = predictionsDf.where("""(prediction = 0 AND results = 'Fail') OR
                                              (prediction = 1 AND (results = 'Pass' OR
                                                                   results = 'Pass w/ Conditions'))""").count()
        numInspections = predictionsDf.count()

        print "There were", numInspections, "inspections and there were", numSuccesses, "successful predictions"
        print "This is a", str((float(numSuccesses) / float(numInspections)) * 100) + "%", "success rate"

    O resultado tem o seguinte aspeto:

        # -----------------
        # THIS IS AN OUTPUT
        # -----------------

        There were 9315 inspections and there were 8087 successful predictions
        This is a 86.8169618894% success rate

    Através de regressão logística com o Spark dá-na um modelo da relação entre as descrições de violações em inglês e se um determinado negócio seria passar ou efetuar uma inspeção prato preciso.

## <a name="create-a-visual-representation-of-the-prediction"></a>Criar uma representação visual da predição
Iremos pode agora criar uma visualização final para nos ajudar a razão sobre os resultados de teste, este.

1. Vamos começar por extrair o predições diferentes e os resultados do **Predições** tabela temporária criada anteriormente. As seguintes consultas de separam o resultado como *true_positive*, *false_positive*, *true_negative*, e *false_negative*. Nas consultas abaixo, iremos desativar visualização utilizando `-q` e também guardar a saída (utilizando `-o`) como dataframes que podem ser utilizadas com o `%%local` magic.

        %%sql -q -o true_positive
        SELECT count(*) AS cnt FROM Predictions WHERE prediction = 0 AND results = 'Fail'

        %%sql -q -o false_positive
        SELECT count(*) AS cnt FROM Predictions WHERE prediction = 0 AND (results = 'Pass' OR results = 'Pass w/ Conditions')

        %%sql -q -o true_negative
        SELECT count(*) AS cnt FROM Predictions WHERE prediction = 1 AND results = 'Fail'

        %%sql -q -o false_negative
        SELECT count(*) AS cnt FROM Predictions WHERE prediction = 1 AND (results = 'Pass' OR results = 'Pass w/ Conditions')
1. Por fim, utilize o seguinte fragmento para gerar o desenho utilizando **Matplotlib**.

        %%local
        %matplotlib inline
        import matplotlib.pyplot as plt

        labels = ['True positive', 'False positive', 'True negative', 'False negative']
        sizes = [true_positive['cnt'], false_positive['cnt'], false_negative['cnt'], true_negative['cnt']]
        colors = ['turquoise', 'seagreen', 'mediumslateblue', 'palegreen', 'coral']
        plt.pie(sizes, labels=labels, autopct='%1.1f%%', colors=colors)
        plt.axis('equal')

    Deverá ver o seguinte resultado:

    ![O Spark aprendizagem saída da aplicação - percentagens de gráfico circular de inspections prato falhada. ] (./media/apache-spark-machine-learning-mllib-ipython/spark-machine-learning-result-output-2.png "Spark aprendizagem automática saída de resultado")

    Este gráfico, um resultado "positivo" refere-se ao inspeção prato falhada, enquanto um resultado negativo refere-se para uma inspeção transmitida.

## <a name="shut-down-the-notebook"></a>Encerrar o bloco de notas
Depois de terminar de executar a aplicação, deve encerrar o bloco de notas para libertar os recursos. Para o fazer, no menu **Ficheiro** do bloco de notas, clique em **Fechar e Parar**. Isto será encerrado e fecha o bloco de notas.

## <a name="seealso"></a>Ver também
* [Descrição geral: Apache Spark no Azure HDInsight](apache-spark-overview.md)

### <a name="scenarios"></a>Cenários
* [Spark com BI: Efetuar uma análise de dados interativa com o Spark no HDInsight com ferramentas do BI](apache-spark-use-bi-tools.md)
* [Spark com Machine Learning: Utilizar o Spark no HDInsight para analisar a temperatura do edifício com dados de AVAC](apache-spark-ipython-notebook-machine-learning.md)
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
