---
title: "BI Spark utilizando ferramentas de visualização de dados no Azure HDInsight | Microsoft Docs"
description: "Utilizar ferramentas de visualização de dados para análise ao utilizar o Apache Spark BI nos clusters do HDInsight"
keywords: "bi no spark de bi, o Apache spark, visualização de dados de spark, spark intelligence de negócio"
services: hdinsight
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 1448b536-9bc8-46bc-bbc6-d7001623642a
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/21/2017
ms.author: nitinme
ms.openlocfilehash: d1d5405a635b9f990f53b2bf32c8270a71a0f344
ms.sourcegitcommit: f8437edf5de144b40aed00af5c52a20e35d10ba1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/03/2017
---
# <a name="apache-spark-bi-using-data-visualization-tools-with-azure-hdinsight"></a>Apache Spark BI utilizando ferramentas de visualização de dados com o Azure HDInsight

Saiba como utilizar as ferramentas de visualização de dados, tais como o Power BI e o Tableau para analisar um conjunto de dados de exemplo não processados através de BI do Apache Spark nos clusters do HDInsight.

> [!NOTE]
> Conectividade com ferramentas de BI descrito neste artigo não é suportada no 2.1 do Spark no Azure HDInsight 3.6 pré-visualização. Apenas versões Spark 1.6 e 2.0 (HDInsight 3.4, 3.5 respetivamente) são suportados.
>

Este tutorial também está disponível como um bloco de notas do Jupyter num cluster do HDInsight Spark. A experiência de bloco de notas permite-lhe executar os fragmentos de Python do bloco de notas. Para efetuar o tutorial do dentro de um bloco de notas, criar um cluster do Spark, inicie um bloco de notas do Jupyter (`https://CLUSTERNAME.azurehdinsight.net/jupyter`), em seguida, execute o bloco de notas **ferramentas de BI de utilização com o Apache Spark no HDInsight.ipynb** sob o **Python** pasta.

## <a name="prerequisites"></a>Pré-requisitos

* Um cluster do Apache Spark no HDInsight. Para obter instruções, consulte [clusters do Apache Spark criar no Azure HDInsight](apache-spark-jupyter-spark-sql.md).


## <a name="hivetable"></a>Preparar os dados para a visualização de dados de Spark

Nesta secção, vamos utilizar o [Jupyter](https://jupyter.org) bloco de notas de um cluster do HDInsight Spark para executar tarefas que processar os dados de exemplo não processados e guardá-lo como uma tabela. Os dados de exemplo são um ficheiro. csv (hvac.csv) disponível em todos os clusters por predefinição. Assim que os seus dados são guardados como uma tabela, na secção seguinte são utilizadas ferramentas de BI para ligar à tabela e executar visualizações de dados.

> [!NOTE]
> Se estiver a efetuar os passos neste artigo depois de concluir as instruções em [executar consultas interativas num cluster do HDInsight Spark](apache-spark-load-data-run-query.md), pode avançar para o passo 8 abaixo.
>

1. No [portal do Azure](https://portal.azure.com/), no startboard, clique no mosaico relativo ao cluster do Spark (se o tiver afixado ao startboard). Também pode navegar até ao cluster em **Procurar Tudo** > **Clusters do HDInsight**.   

2. No painel do cluster do Spark, clique em **Dashboard de Clusters** e, em seguida, clique em **Bloco de Notas do Jupyter**. Se lhe for solicitado, introduza as credenciais de administrador do cluster.

   > [!NOTE]
   > Também pode aceder ao Bloco de Notas do Jupyter para o cluster abrindo o seguinte URL no seu browser. Substitua **CLUSTERNAME** pelo nome do cluster:
   >
   > `https://CLUSTERNAME.azurehdinsight.net/jupyter`
   >
   >

3. Crie um bloco de notas. Clique em **Novo** e, em seguida, clique em **PySpark**.

    ![Criar um bloco de notas do Jupyter para o Apache Spark BI](./media/apache-spark-use-bi-tools/create-jupyter-notebook-for-spark-bi.png "criar um bloco de notas do Jupyter para o Apache Spark BI")

4. É criado e aberto um novo bloco de notas com o nome Untitled.pynb. Clique no nome do bloco de notas na parte superior e introduza um nome amigável.

    ![Forneça um nome para o bloco de notas do Apache Spark BI](./media/apache-spark-use-bi-tools/jupyter-notebook-name-for-spark-bi.png "forneça um nome para o bloco de notas do Apache Spark BI")

5. Uma vez que criou um bloco de notas com o kernel do PySpark, não é necessário criar quaisquer contextos explicitamente. Os contextos do Spark e do Hive são criados automaticamente quando executa a primeira célula do código. Pode começar por importar os tipos necessários para este cenário. Para tal, coloque o cursor na célula e prima **SHIFT + ENTER**.

        from pyspark.sql import *

6. Carregue dados de exemplo para uma tabela temporária. Quando cria um cluster do Spark no HDInsight, o ficheiro de dados de exemplo, **hvac.csv**, é copiado para a conta do Storage associada em **\HdiSamples\HdiSamples\SensorSampleData\hvac**.

    Numa célula vazia, cole o seguinte fragmento e prima **SHIFT + ENTER**. Este fragmento regista os dados numa tabela chamada **AVAC**.

        # Create an RDD from sample data
        hvacText = sc.textFile("wasb:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")

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

7. Certifique-se de que a tabela foi criada com êxito. Pode utilizar o `%%sql` magic a execução do Hive consulta diretamente. Para obter mais informações sobre a magia `%%sql`, bem como sobre outras magias disponíveis com o kernel do PySpark, veja [Kernels disponíveis nos blocos de notas do Jupyter com clusters do Spark HDInsight](apache-spark-jupyter-notebook-kernels.md#parameters-supported-with-the-sql-magic).

        %%sql
        SHOW TABLES

    Ver um resultado como mostrado abaixo:

        +---------------+-------------+
        |tableName      |isTemporary  |
        +---------------+-------------+
        |hvactemptable  |true        |
        |hivesampletable|false        |
        |hvac           |false        |
        +---------------+-------------+

    Apenas as tabelas que tenham false sob o **isTemporary** coluna são tabelas do hive, que são armazenadas no metastore e podem ser acedidas a partir de ferramentas de BI. Neste tutorial, vamos ligar para o **AVAC** tabela foi criada.

8. Certifique-se de que a tabela contém os dados pretendidos. Numa célula vazia no bloco de notas, copie o seguinte fragmento e prima **SHIFT + ENTER**.

        %%sql
        SELECT * FROM hvac LIMIT 10

9. Encerre o bloco de notas para libertar os recursos. Para o fazer, no menu **Ficheiro** do bloco de notas, clique em **Fechar e Parar**.

## <a name="powerbi"></a>Utilizar o Power BI para visualização de dados do Spark

> [!NOTE]
> Esta secção é aplicável apenas para 1.6 Spark no HDInsight 3.4 e 2.0 do Spark no HDInsight 3.5.
>
>

Depois de guardar os dados como uma tabela, pode utilizar o Power BI para se ligar aos dados e visualizá-la para criar relatórios, dashboards, etc.

1. Certifique-se de que tem acesso ao Power BI. Pode obter uma subscrição gratuita pré-visualização do Power BI da [http://www.powerbi.com/](http://www.powerbi.com/).

2. Inicie sessão no [Power BI](http://www.powerbi.com/).

3. Na parte inferior do painel esquerdo, clique em **obter dados**.

4. No **obter dados** página **importar ou ligar aos dados**, para **bases de dados**, clique em **obter**.

    ![Obter dados para o Power BI para o Apache Spark BI](./media/apache-spark-use-bi-tools/apache-spark-bi-import-data-power-bi.png "obter dados para o Power BI para o Apache Spark BI")

5. No ecrã seguinte, clique em **Spark no Azure HDInsight** e, em seguida, clique em **Connect**. Quando lhe for pedido, introduza o URL de cluster (`mysparkcluster.azurehdinsight.net`) e as credenciais para ligar ao cluster.

    ![Ligar para o Apache Spark BI](./media/apache-spark-use-bi-tools/connect-to-apache-spark-bi.png "ligar para o Apache Spark BI")

    Depois da ligação for estabelecida, o Power BI inicia a importar dados a partir do cluster do Spark no HDInsight.

6. Power BI importa os dados e adiciona uma **Spark** conjunto de dados sob a **conjuntos de dados** cabeçalho. Clique no conjunto de dados para abrir uma nova folha de cálculo para visualizar os dados. Também pode guardar a folha de cálculo como um relatório. Para guardar uma folha de cálculo do **ficheiro** menu, clique em **guardar**.

    ![Mosaico de BI do Apache Spark no dashboard do Power BI](./media/apache-spark-use-bi-tools/apache-spark-bi-tile-dashboard.png "mosaico de BI do Apache Spark no dashboard do Power BI")
7. Tenha em atenção que o **campos** lista as listas de direitos de **AVAC** tabela que criou anteriormente. Expanda a tabela para ver os campos na tabela, conforme definido anteriormente no bloco de notas.

      ![Listar as tabelas no dashboard do Apache Spark BI](./media/apache-spark-use-bi-tools/apache-spark-bi-display-tables.png "listar as tabelas no dashboard do Apache Spark BI")

8. Crie uma visualização para mostrar a variância entre temperatura de destino e temperatura real para cada edifício. Para visualizar os dados, selecione **gráfico de área** (mostrado na caixa vermelha). Para definir o eixo, arrastar e largar o **BuildingID** campo em **eixo**, e **ActualTemp**/**TargetTemp** campos em **valor**.

    ![Criar Spark visualizações de dados através do Apache Spark BI](./media/apache-spark-use-bi-tools/apache-spark-bi-add-value-columns.png "visualizações de dados de Spark criar através do Apache Spark BI")

9. Por predefinição, a visualização mostra a soma dos **ActualTemp** e **TargetTemp**. Para ambos os campos da lista pendente, selecione **médio** para obter uma média de real e temperatures de destino para ambos os edifícios.

    ![Criar Spark visualizações de dados através do Apache Spark BI](./media/apache-spark-use-bi-tools/apache-spark-bi-average-of-values.png "visualizações de dados de Spark criar através do Apache Spark BI")

10. A visualização de dados deve ser semelhante na captura de ecrã. Mova o cursor sobre a visualização para obter sugestões de ferramenta com dados relevantes.

    ![Criar Spark visualizações de dados através do Apache Spark BI](./media/apache-spark-use-bi-tools/apache-spark-bi-area-graph.png "visualizações de dados de Spark criar através do Apache Spark BI")

11. Clique em **guardar** no menu superior e forneça um nome de relatório. Também pode afixar o visual. Quando afixar uma visualização, este é armazenado no seu dashboard para acompanhar o valor mais recente rapidamente.

   Pode adicionar tantos visualizações à medida que pretende para o mesmo conjunto de dados e afixar ao dashboard para um instantâneo dos dados. Além disso, os clusters do Spark no HDInsight são ligados ao Power BI com direta ligar. Isto garante que o Power BI tem sempre os dados mais atualizados do seu cluster, pelo que não é necessário agendar atualizações para o conjunto de dados.

## <a name="tableau"></a>Utilizar o ambiente de trabalho Tableau para visualização de dados de Spark

> [!NOTE]
> Esta secção é aplicável apenas para os clusters do Spark 1.5.2 criadas no Azure HDInsight.
>
>

1. Instalar [Tableau ambiente de trabalho](http://www.tableau.com/products/desktop) no computador onde está a executar este tutorial do Apache Spark BI.

2. Certifique-se de que o computador também tem o controlador ODBC do Microsoft Spark instalado. Pode instalar o controlador do [aqui](http://go.microsoft.com/fwlink/?LinkId=616229).

1. Inicie o Tableau ambiente de trabalho. No painel esquerdo, na lista de servidor para ligar, clique em **Spark SQL**. Se o Spark SQL não está listado por predefinição no painel esquerdo, pode encontrá-lo ao clique **mais servidores**.
2. Na caixa de diálogo de ligação de Spark SQL, forneça os valores, conforme mostrado na captura de ecrã e, em seguida, clique em **OK**.

    ![Ligar a um cluster do Apache Spark BI](./media/apache-spark-use-bi-tools/connect-to-tableau-apache-spark-bi.png "ligar a um cluster do Apache Spark BI")

    A autenticação na lista pendente **serviço do Microsoft Azure HDInsight** como opção, apenas se tiver instalado o [controlador ODBC do Microsoft Spark](http://go.microsoft.com/fwlink/?LinkId=616229) no computador.
3. No ecrã seguinte, do **esquema** pendente, clique o **localizar** ícone e, em seguida, clique em **predefinido**.

    ![Localizar um esquema para o Apache Spark BI](./media/apache-spark-use-bi-tools/tableau-find-schema-apache-spark-bi.png "encontrar o esquema para o Apache Spark BI")
4. Para o **tabela** campo, clique em de **localizar** ícone novamente para listar todas as tabelas do Hive disponíveis no cluster. Deverá ver o **AVAC** tabela que criou anteriormente utilizando o bloco de notas.

    ![Localizar a tabela para o Apache Spark BI](./media/apache-spark-use-bi-tools/tableau-find-table-apache-spark-bi.png "localizar tabela para o Apache Spark BI")
5. Arrastar e largar a tabela à caixa superior à direita. Tableau importa os dados e mostra o esquema conforme realçado por caixa vermelha.

    ![Adicionar tabelas para Tableau do Apache Spark BI](./media/apache-spark-use-bi-tools/tableau-add-table-apache-spark-bi.png "adicionar tabelas para Tableau do Apache Spark BI")
6. Clique em de **Sheet1** separador na parte inferior esquerda. Efetuar uma visualização que mostra o destino de média e temperatures reais para edifícios todos os para cada data. Arraste **data** e **criação ID** para **colunas** e **Temp real**/**destino Temp** para **linhas**. Em **marcas**, selecione **área** a utilizar um mapa de área para visualização de dados de Spark.

     ![Adicione campos para visualização de dados de Spark](./media/apache-spark-use-bi-tools/spark-data-visualization-add-fields.png "adicionar campos para visualização de dados do Spark")
7. Por predefinição, os campos de temperatura são apresentados como agregado. Se pretende mostrar os temperatures média em vez disso, pode fazê-na lista pendente, conforme mostrado abaixo.

    ![Tirar médio de temperatura para visualização de dados de Spark](./media/apache-spark-use-bi-tools/spark-data-visualization-average-temperature.png "demorar médio de temperatura para visualização de dados do Spark")

8. Pode também super-impor um mapeamento de temperatura detrimento dos outros para perceber melhor de diferença entre temperatures reais e de destino. Mova o rato para o canto do mapa de área inferior até verá a forma de identificador realçada num círculo vermelho. Arraste o mapa para o mapa de outro na parte superior e libertar o rato quando vir a forma realçada na retângulo vermelho.

    ![Intercalar maps para visualização de dados de Spark](./media/apache-spark-use-bi-tools/spark-data-visualization-merge-maps.png "intercalação mapeia para visualização de dados do Spark")

     A visualização de dados deve ser alterado conforme mostrado na captura de ecrã:

    ![Saída de tableau para visualização de dados de Spark](./media/apache-spark-use-bi-tools/spark-data-visualization-tableau-output.png "saída Tableau para visualização de dados do Spark")
9. Clique em **guardar** para guardar a folha de cálculo. Pode criar dashboards e adicionar-lhe um ou mais folhas.

## <a name="next-steps"></a>Passos seguintes

Até ao momento aprendeu a criar um cluster, criar Spark frames de dados para dados de consulta e, em seguida, aceder a esses dados de ferramentas de BI. Agora pode ver as instruções sobre como gerir os recursos de cluster e depurar tarefas em execução num cluster do HDInsight Spark.

* [Gerir recursos para o cluster do Apache Spark no Azure HDInsight](apache-spark-resource-manager.md)
* [Controlar e depurar tarefas em execução num cluster do Apache Spark do HDInsight](apache-spark-job-debugging.md)

