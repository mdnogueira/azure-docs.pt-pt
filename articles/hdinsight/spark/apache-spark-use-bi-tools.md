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
ms.date: 10/24/2017
ms.author: nitinme
ms.openlocfilehash: 3886923639be8a7bd8167f10db503d7ebf8c1657
ms.sourcegitcommit: 6a6e14fdd9388333d3ededc02b1fb2fb3f8d56e5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/07/2017
---
# <a name="apache-spark-bi-using-data-visualization-tools-with-azure-hdinsight"></a>Apache Spark BI utilizando ferramentas de visualização de dados com o Azure HDInsight

Saiba como utilizar o Power BI e o Tableau para visualizar dados de um cluster do Apache Spark no Azure HDInsight.

## <a name="prerequisites"></a>Pré-requisitos

* Um cluster do Apache Spark no HDInsight. Para obter instruções, consulte [clusters do Apache Spark criar no Azure HDInsight](apache-spark-jupyter-spark-sql.md).
* Dados de exemplo no cluster. Para obter instruções, consulte [executar consultas interativas num cluster do HDInsight Spark](apache-spark-load-data-run-query.md).
* Power BI: [Power BI Desktop](https://powerbi.microsoft.com/en-us/desktop/) e [subscrição de avaliação do Power BI](https://app.powerbi.com/signupredirect?pbi_source=web) (opcional).
* Tableau: [Tableau ambiente de trabalho](http://www.tableau.com/products/desktop) e [o controlador ODBC do Microsoft Spark](http://go.microsoft.com/fwlink/?LinkId=616229).


## <a name="hivetable"></a>Reveja os dados de exemplo

O bloco de notas do Jupyter que criou no [tutorial anterior](apache-spark-load-data-run-query.md) inclui código para criar um `hvac` tabela. Esta tabela é baseada no ficheiro CSV disponível em todos os clusters do HDInsight Spark em **\HdiSamples\HdiSamples\SensorSampleData\hvac\hvac.csv**. Vamos rever os dados no cluster do Spark antes de criar visualizações.

1. Certifique-se de que existem as tabelas esperadas. Numa célula vazia no bloco de notas, copie o seguinte fragmento e prima **SHIFT + ENTER**.

        %%sql
        SHOW TABLES

    Poderá ver um resultado semelhante ao apresentado abaixo:

    ![Mostrar a tabelas no Spark](./media/apache-spark-use-bi-tools/show-tables.png)

    Se tiver fechado o bloco de notas antes de iniciar este tutorial, `hvactemptable` é limpa, pelo que não está incluída na saída.
    Apenas tabelas que estão armazenadas no metastore de ramo de registo (indicado pelo **falso** sob o **isTemporary** coluna) pode ser acedido a partir de ferramentas de BI. Neste tutorial, vamos ligar para o **AVAC** tabela foi criada.

2. Certifique-se de que a tabela contém os dados esperados. Numa célula vazia no bloco de notas, copie o seguinte fragmento e prima **SHIFT + ENTER**.

        %%sql
        SELECT * FROM hvac LIMIT 10

    Poderá ver um resultado semelhante ao apresentado abaixo:

    ![Mostrar linhas da tabela de AVAC no Spark](./media/apache-spark-use-bi-tools/select-limit.png)

3. Encerre o bloco de notas para libertar os recursos. Para o fazer, no menu **Ficheiro** do bloco de notas, clique em **Fechar e Parar**.

## <a name="powerbi"></a>Utilizar o Power BI para visualização de dados do Spark

Agora que tiver confirmado que os dados esperados existem, pode utilizar o Power BI para criar visualizações, relatórios e dashboards a partir destes dados. Neste artigo, iremos irá mostrar um exemplo simples com dados estáticos, mas indique nos comentários se gostaria de ver mais complexos exemplos de transmissão em fluxo.

### <a name="create-a-report-in-power-bi-desktop"></a>Criar um relatório no Power BI Desktop
Os passos primeiro trabalhar com o Spark estão a ligar ao cluster no Power BI Desktop, carregar dados do cluster e criar uma visualização básica com base nesses dados.

> [!NOTE]
> O conector demonstrado neste artigo está atualmente em pré-visualização, mas Aconselhamo-lo para utilizá-la e quaisquer comentários tiver através de [Comunidade do Power BI](https://community.powerbi.com/) site ou [Power BI ideias](https://ideas.powerbi.com/forums/265200-power-bi-ideas).

1. No Power BI Desktop, no **home page** separador, clique em **obter dados**, em seguida, **mais**.

2. Procurar `Spark`, selecione **Azure HDInsight Spark**, em seguida, clique em **Connect**.

    ![Obter dados para o Power BI a partir do Apache Spark BI](./media/apache-spark-use-bi-tools/apache-spark-bi-import-data-power-bi.png "obter dados para o Power BI a partir do Apache Spark BI")

3. Introduza o URL de cluster (no formato `mysparkcluster.azurehdinsight.net`), selecione **DirectQuery**, em seguida, clique em **OK**.

    ![Ligar para o Apache Spark BI](./media/apache-spark-use-bi-tools/connect-to-apache-spark-bi.png "ligar para o Apache Spark BI")

    > [!NOTE]
    > Pode utilizar o modo de conectividade com o Spark. Se utilizar DirectQuery, as alterações são refletidas em relatórios sem atualizar o conjunto de dados completo. Se importar dados, tem de atualizar o conjunto de dados para ver as alterações. Para obter mais informações sobre como e quando utilizar DirectQuery, consulte [utilizando DirectQuery no Power BI](https://powerbi.microsoft.com/documentation/powerbi-desktop-directquery-about/). 

4. Introduza as informações de conta de início de sessão do HDInsight para **nome de utilizador** e **palavra-passe** (a conta predefinida é `admin`), em seguida, clique em **Connect**.

    ![Nome do cluster de utilizador e palavra-passe de spark](./media/apache-spark-use-bi-tools/user-password.png "Spark nome de utilizador do cluster e a palavra-passe")

5. Selecione o `hvac` tabela e espere para ver uma pré-visualização dos dados. Em seguida, clique em **carga**.

    ![Nome do cluster de utilizador e palavra-passe de spark](./media/apache-spark-use-bi-tools/apache-spark-bi-select-table.png "Spark nome de utilizador do cluster e a palavra-passe")

    Ambiente de trabalho do Power BI tem agora todas as informações de que tem de estabelecer ligação com os Spark cluster e carregar dados do `hvac` tabela. A tabela e as colunas são apresentadas no **campos** painel.

    ![Listar as tabelas no dashboard do Apache Spark BI](./media/apache-spark-use-bi-tools/apache-spark-bi-display-tables.png "listar as tabelas no dashboard do Apache Spark BI")

7. Crie uma visualização para mostrar a variância entre temperatura de destino e temperatura real para cada criação: 

    1. No **VISUALIZAÇÕES** painel, selecione **gráfico de área**. Arraste o **BuildingID** campo para **eixo**e arraste o **ActualTemp** e **TargetTemp** campos para **valor**.

        ![Criar Spark visualizações de dados através do Apache Spark BI](./media/apache-spark-use-bi-tools/apache-spark-bi-add-value-columns.png "visualizações de dados de Spark criar através do Apache Spark BI")

    2. Por predefinição, a visualização mostra a soma dos **ActualTemp** e **TargetTemp**. Para ambos os campos, na lista pendente, selecione **médio** para obter uma média de real e temperatures de destino para cada edifício.

        ![Criar Spark visualizações de dados através do Apache Spark BI](./media/apache-spark-use-bi-tools/apache-spark-bi-average-of-values.png "visualizações de dados de Spark criar através do Apache Spark BI")

    3. A visualização de dados deve ser semelhante na captura de ecrã. Mova o cursor sobre a visualização para obter sugestões de ferramenta com dados relevantes.

        ![Criar Spark visualizações de dados através do Apache Spark BI](./media/apache-spark-use-bi-tools/apache-spark-bi-area-graph.png "visualizações de dados de Spark criar através do Apache Spark BI")

11. Clique em **ficheiro** , em seguida, **guardar**e introduza o nome `spark.pbix` para o ficheiro. 

### <a name="publish-the-report-to-the-power-bi-service-optional"></a>Publicar o relatório no serviço Power BI (opcional)
Tem agora um relatório totalmente funcional no Power BI Desktop e pode parar existe mas, muitas pessoas pretendem tirar partido do serviço do Power BI, o que torna mais fácil partilhar relatórios e dashboards na sua organização. 

Nesta secção, publicar o conjunto de dados e relatório que está contido no ficheiro do Power BI Desktop que criou. Em seguida, afixar a um dashboard de visualização do relatório. Dashboards são normalmente utilizados para se focarem num subconjunto dos dados num relatório; tem apenas uma visualização no relatório, mas é ainda útil seguir os passos.

1. No Power BI Desktop, no **home page** separador, clique em **publicar**.

    ![Publicação a partir do ambiente de trabalho do Power BI](./media/apache-spark-use-bi-tools/apache-spark-bi-publish.png "publicação a partir do ambiente de trabalho do Power BI")

2. Selecione uma área de trabalho para publicar o conjunto de dados e relatórios para, em seguida, clique em **selecione**. Na imagem seguinte, a predefinição **minha área de trabalho** está selecionada.

    ![Selecione a área de trabalho para publicar o conjunto de dados e relatórios para](./media/apache-spark-use-bi-tools/apache-spark-bi-select-workspace.png "selecione área de trabalho para publicar o conjunto de dados e relatórios para") 

3. Depois do Power BI Desktop devolve uma mensagem de êxito, clique em **abrir 'spark.pbix' no Power BI**.

    ![Publicar com êxito, clique para introduzir credenciais](./media/apache-spark-use-bi-tools/apache-spark-bi-publish-success.png "publicar com êxito, clique para introduzir credenciais") 

4. No serviço Power BI, clique em **introduza credenciais**.

    ![Introduzir as credenciais no serviço Power BI](./media/apache-spark-use-bi-tools/apache-spark-bi-enter-credentials.png "introduzir as credenciais no serviço Power BI")

5. Clique em **editar credenciais**.

    ![Editar as credenciais no serviço Power BI](./media/apache-spark-use-bi-tools/apache-spark-bi-edit-credentials.png "editar credenciais no serviço Power BI")

6. Introduza as informações de conta de início de sessão do HDInsight (normalmente, a predefinição `admin` conta que utilizou no Power BI Desktop), em seguida, clique em **iniciar sessão**.

    ![Inicie sessão no cluster do Spark](./media/apache-spark-use-bi-tools/apache-spark-bi-sign-in.png "inicie sessão no cluster do Spark")

7. No painel esquerdo, aceda a **áreas de trabalho** > **minha área de trabalho** > **relatórios**, em seguida, clique em **spark**.

    ![Relatório listado em relatórios no painel esquerdo](./media/apache-spark-use-bi-tools/apache-spark-bi-service-left-pane.png "relatório listado em relatórios no painel esquerdo")

    Também deverá ver **spark** listados na **conjuntos de dados** no painel esquerdo.

8. O visual que criou no Power BI Desktop está agora disponível no serviço. Para afixar este elemento visual para um dashboard, coloque o cursor sobre o visual e clique no ícone de pin.

    ![Relatório no serviço Power BI](./media/apache-spark-use-bi-tools/apache-spark-bi-service-report.png "relatório no serviço Power BI")

9. Selecione "Novo dashboard", introduza o nome `SparkDemo`, em seguida, clique em **Pin**.

    ![Afixar ao dashboard novo](./media/apache-spark-use-bi-tools/apache-spark-bi-pin-dashboard.png "afixar ao dashboard novo")

10. No relatório, clique em **aceder ao dashboard**. 

    ![Aceda ao dashboard](./media/apache-spark-use-bi-tools/apache-spark-bi-open-dashboard.png "aceder ao dashboard")

O visual está afixado ao dashboard - pode adicionar outros elementos visuais para o relatório e afixar ao dashboard do mesmo. Para obter mais informações sobre relatórios e dashboards, consulte [relatórios no Power BI](https://powerbi.microsoft.com/documentation/powerbi-service-reports/)e [Dashboards no Power BI](https://powerbi.microsoft.com/documentation/powerbi-service-dashboards/).

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

