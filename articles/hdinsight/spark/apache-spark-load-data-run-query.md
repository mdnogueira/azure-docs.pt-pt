---
title: Executar consultas interativas num cluster do Azure HDInsight Spark | Microsoft Docs
description: "Início rápido do HDInsight Spark sobre como criar um cluster do Apache Spark no HDInsight."
keywords: spark quickstart,interactive spark,interactive query,hdinsight spark,azure spark
services: hdinsight
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/22/2017
ms.author: nitinme
ms.openlocfilehash: ef9154b0d400ff23c53460454c886ab90e290f0c
ms.sourcegitcommit: f8437edf5de144b40aed00af5c52a20e35d10ba1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/03/2017
---
# <a name="run-interactive-queries-on-an-hdinsight-spark-cluster"></a>Executar consultas interativas num cluster do Spark do HDInsight

Neste artigo, pode utilizar um bloco de notas do Jupyter para executar consultas de Spark SQL interativas contra um cluster do Spark. Bloco de notas do Jupyter é uma aplicação baseada no browser que expande a experiência interativa consola baseada na Web. Para obter mais informações, consulte [bloco de notas do Jupyter o](http://jupyter-notebook.readthedocs.io/en/latest/notebook.html).

Para este tutorial, utilize o **PySpark** kernel no bloco de notas do Jupyter para executar uma consulta de Spark SQL interativa. Blocos de notas do Jupyter nos clusters do HDInsight também suportam dois outros kernels - **PySpark3** e **Spark**. Para obter mais informações sobre os kernels e as vantagens de utilizar **PySpark**, consulte [clusters kernels de bloco de notas do Jupyter de utilização com o Apache Spark no HDInsight](apache-spark-jupyter-notebook-kernels.md).

## <a name="prerequisites"></a>Pré-requisitos

* **Um cluster do Azure HDInsight Spark**. Para obter instruções, consulte [criar um cluster do Apache Spark no Azure HDInsight](apache-spark-jupyter-spark-sql.md).

## <a name="create-a-jupyter-notebook-to-run-interactive-queries"></a>Criar um bloco de notas do Jupyter para executar consultas interativas

Para executar consultas, utilizamos os dados de exemplo que está por predefinição, disponível no armazenamento associado o cluster. No entanto, tem primeiro carregar os dados no Spark como um dataframe. Assim que tiver o dataframe, pode executar consultas no mesmo utilizando o bloco de notas do Jupyter. Nesta secção, observe como:

* Registe um conjunto de dados de exemplo como um dataframe de Spark.
* Execute consultas no dataframe.

1. Abra o [Portal do Azure](https://portal.azure.com/). Se optou por afixar o cluster ao dashboard, clique no mosaico do cluster a partir do dashboard para iniciar o painel do cluster.

    Se não afixou o cluster ao dashboard, no painel da esquerda, clique em **Clusters do HDInsight** e, em seguida, clique no cluster que criou.

3. A partir de **Ligações rápidas**, clique em **Dashboards de clusters** e, em seguida, clique em **Bloco de Notas do Jupyter**. Se lhe for solicitado, introduza as credenciais de administrador do cluster.

   ![Abra o bloco de notas Jupyter para executar a consulta interativa do Sparl SQL](./media/apache-spark-load-data-run-query/hdinsight-spark-start-jupyter-interactive-spark-sql-query.png "Open Jupyter notebook to run interactive Spark SQL query")

   > [!NOTE]
   > Também pode abrir o URL seguinte no seu browser para aceder ao bloco de notas Jupyter do cluster. Substitua **CLUSTERNAME** pelo nome do cluster:
   >
   > `https://CLUSTERNAME.azurehdinsight.net/jupyter`
   >
   >
3. Crie um bloco de notas. Clique em **Novo** e, em seguida, clique em **PySpark**.

   ![Crie um bloco de notas Jupyter para executar a consulta interativa do Spark SQL](./media/apache-spark-load-data-run-query/hdinsight-spark-create-jupyter-interactive-Spark-SQL-query.png "Create a Jupyter notebook to run interactive Spark SQL query")

   É criado e aberto um novo bloco de notas com o nome Untitled (Untitled.pynb).

4. Clique no nome do bloco de notas na parte superior e introduza um nome amigável se assim o desejar.

    ![Indique um nome para o bloco de notas Jupyter para executar uma consulta interativa do Spark](./media/apache-spark-load-data-run-query/hdinsight-spark-jupyter-notebook-name.png "Provide a name for the Jupter notebook to run interactive Spark query from")

5. Cole o seguinte código numa célula vazia e, em seguida, prima **SHIFT + ENTER** para o executar. O código importa os tipos necessários para este cenário:

        from pyspark.sql import *
        from pyspark.sql.types import *

    Uma vez que criou um bloco de notas com o kernel do PySpark, não é necessário criar quaisquer contextos explicitamente. Os contextos do Spark e do Hive são criados automaticamente quando executa a primeira célula do código.

    ![Estado da consulta interativa do Spark SQL](./media/apache-spark-load-data-run-query/hdinsight-spark-interactive-spark-query-status.png "Status of interactive Spark SQL query")

    Sempre que executar uma consulta interativa no Jupyter, o título da janela do browser apresenta o estado **(Ocupado)**, juntamente com o nome do bloco de notas. Também vê um círculo sólido junto ao texto do **PySpark** no canto superior direito. Após a conclusão da tarefa, este é alterado para um círculo vazio.

6. Antes de carregar os dados para um cluster do Spark, informe-na procurar um instantâneo do mesmo. Os dados de exemplo utilizados neste tutorial estão disponíveis como um ficheiro CSV em todos os clusters do HDInsight Spark em **\HdiSamples\HdiSamples\SensorSampleData\hvac\hvac.csv**. Os dados de captura as variações de temperatura de um edifício. Seguem-se as primeiras linhas de alguns dos dados.

    ![Instantâneo dos dados para a consulta de Spark SQL interativa](./media/apache-spark-load-data-run-query/hdinsight-spark-sample-data-interactive-spark-sql-query.png "instantâneo dos dados para a consulta de Spark SQL interativa")

6. Criar um dataframe e uma tabela temporária (**AVAC**) executando o seguinte código. Para este tutorial, iremos não crie todas as colunas na tabela temporária em comparação com as colunas dos dados não processados do CSV. 

        # Create an RDD from sample data
        hvacText = sc.textFile("wasbs:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")

        # Create a schema for our data
        Entry = Row('Date', 'Time', 'TargetTemp', 'ActualTemp', 'BuildingID')

        # Parse the data and create a schema
        hvacParts = hvacText.map(lambda s: s.split(',')).filter(lambda s: s[0] != 'Date')
        hvac = hvacParts.map(lambda p: Entry(str(p[0]), str(p[1]), int(p[2]), int(p[3]), int(p[6])))
        
        # Infer the schema and create a table       
        hvacTable = sqlContext.createDataFrame(hvac)
        hvacTable.registerTempTable('hvactemptable')
        dfw = DataFrameWriter(hvacTable)
        dfw.saveAsTable('hvac')

7. Assim que a tabela for criada, executar consultas interativas nos dados, utilize o seguinte código.

        %%sql
        SELECT buildingID, (targettemp - actualtemp) AS temp_diff, date FROM hvac WHERE date = \"6/1/13\"

   Uma vez que está a utilizar um kernel do PySpark, pode agora executar diretamente uma consulta interativa SQL na tabela temporária **hvac** que criou com a magia `%%sql`. Para obter mais informações sobre a magia `%%sql`, bem como sobre outras magias disponíveis com o kernel do PySpark, veja [Kernels disponíveis nos blocos de notas do Jupyter com clusters do Spark HDInsight](apache-spark-jupyter-notebook-kernels.md#parameters-supported-with-the-sql-magic).

   O resultado da tabela seguinte é apresentado por predefinição.

     ![Saída da tabela do resultado da consulta interativa do Spark](./media/apache-spark-load-data-run-query/hdinsight-interactive-spark-query-result.png "Table output of interactive Spark query result")

    Também pode ver os resultados noutras visualizações. Por exemplo, um gráfico de área para o mesmo resultado teria o seguinte aspeto.

    ![Gráfico de área do resultado da consulta interativa do Spark](./media/apache-spark-load-data-run-query/hdinsight-interactive-spark-query-result-area-chart.png "Area graph of interactive Spark query result")

9. Encerre o bloco de notas para libertar os recursos do cluster depois de terminar a execução da aplicação. Para o fazer, no menu **Ficheiro** do bloco de notas, clique em **Fechar e Parar**.

## <a name="next-step"></a>Passo seguinte

Neste artigo, aprendeu como executar consultas interativas no Spark utilizando o bloco de notas do Jupyter. Avançar para o seguinte artigo para ver como os dados que registou no Spark ser solicitados para uma ferramenta de análise de BI como o Power BI e o Tableau. 

> [!div class="nextstepaction"]
>[BI Spark utilizando ferramentas de visualização de dados com o Azure HDInsight](apache-spark-use-bi-tools.md)




