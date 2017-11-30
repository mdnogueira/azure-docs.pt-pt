---
title: Criar um cluster do Apache Spark no Azure HDInsight | Microsoft Docs
description: "Início rápido do HDInsight Spark sobre como criar um cluster do Apache Spark no HDInsight."
keywords: spark quickstart,interactive spark,interactive query,hdinsight spark,azure spark
services: hdinsight
documentationcenter: 
author: mumian
manager: cgronlun
editor: cgronlun
tags: azure-portal
ms.assetid: 91f41e6a-d463-4eb4-83ef-7bbb1f4556cc
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 09/07/2017
ms.author: jgao
ms.openlocfilehash: 20952702ee9dbe9880c80dddc0d75f39e53b6659
ms.sourcegitcommit: 29bac59f1d62f38740b60274cb4912816ee775ea
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/29/2017
---
# <a name="create-an-apache-spark-cluster-in-azure-hdinsight"></a>Criar um cluster do Apache Spark no Azure HDInsight

Neste artigo, vai aprender a criar um cluster do Apache Spark no Azure HDInsight e a executar, depois, uma consulta Spark SQL numa tabela do Hive. Para obter informações sobre o Spark no HDInsight, consulte [descrição geral: Apache Spark no Azure HDInsight](apache-spark-overview.md).

   ![Diagrama de início rápido que descreve os passos para criar um cluster do Apache Spark no Azure HDInsight](./media/apache-spark-jupyter-spark-sql/hdinsight-spark-quickstart-interactive-spark-query-flow.png "Guia de introdução do Spark ao utilizar o Apache Spark no HDInsight. Passos ilustrados: criar um cluster; executar consulta interativa do Spark")

## <a name="prerequisites"></a>Pré-requisitos

* **Uma subscrição do Azure**. Antes de começar este tutorial, tem de ter uma subscrição do Azure. Veja o artigo [Crie hoje a sua conta do Azure gratuita](https://azure.microsoft.com/free).

## <a name="create-hdinsight-spark-cluster"></a>Criar o cluster do HDInsight Spark

Nesta secção, vai criar um cluster do HDInsight Spark com um [modelo do Azure Resource Manager](https://azure.microsoft.com/resources/templates/101-hdinsight-spark-linux/). Para obter outros métodos de criação de clusters, consulte [Criar clusters do HDInsight](../hdinsight-hadoop-provision-linux-clusters.md).

1. Clique na imagem seguinte para abrir o modelo no portal do Azure.         

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-hdinsight-spark-linux%2Fazuredeploy.json" target="_blank"><img src="./media/apache-spark-jupyter-spark-sql/deploy-to-azure.png" alt="Deploy to Azure"></a>

2. Introduza os seguintes valores:

    ![Criar um cluster do HDInsight Spark com um modelo do Azure Resource Manager](./media/apache-spark-jupyter-spark-sql/create-spark-cluster-in-hdinsight-using-azure-resource-manager-template.png "Criar um cluster do Spark no HDInsight com um modelo do Azure Resource Manager")

    * **Subscrição**: Selecione a sua subscrição do Azure para este cluster.
    * **Grupo de recursos**: crie um grupo de recursos ou selecione um grupo existente. O grupo de recursos é utilizado para gerir recursos do Azure para os seus projetos.
    * **Localização**: Selecione uma localização para o grupo de recursos. O modelo utiliza esta localização para criar o cluster, bem como para o armazenamento predefinido do mesmo.
    * **ClusterName**: introduza um nome para o cluster do HDInsight que pretende criar.
    * **Versão do Spark**: selecione **2.0** como a versão do Spark que quer instalar no cluster.
    * **Nome e palavra-passe de início de sessão do cluster**: o nome de início de sessão predefinido é admin.
    * **Nome de utilizador e palavra-passe do SSH**.

   Anote estes valores.  Vai precisar deles mais tarde no tutorial.

3. Selecione **Concordo com os termos e condições indicados acima**, selecione **Afixar ao dashboard** e, em seguida, clique em **Comprar**. Verá um novo mosaico intitulado Submeter a implementação para a implementação do Modelo. A criação do cluster demora cerca de 20 minutos.

Caso se depare com um problema ao criar clusters do HDInsight, estes poderão dever-se ao facto de não ter as permissões certas para o fazer. Para obter mais informações, veja [Access control requirements](../hdinsight-administer-use-portal-linux.md#create-clusters) (Requisitos do controlo de acesso).

> [!NOTE]
> Este artigo cria um cluster do Spark que utiliza [Blobs de Armazenamento do Azure como armazenamento de cluster](../hdinsight-hadoop-use-blob-storage.md). Também pode criar um cluster do Spark que utiliza o [Azure Data Lake Store](../hdinsight-hadoop-use-data-lake-store.md) como o armazenamento predefinido. Para obter instruções, consulte [Criar um cluster de HDInsight com o Arquivo Data Lake](../../data-lake-store/data-lake-store-hdinsight-hadoop-use-portal.md).
>
>

## <a name="run-spark-sql-statements-on-a-hive-table"></a>Executar instruções do Spark SQL numa tabela do Hive

SQL (Structured Query Language) é a linguagem mais comum e mais utilizada para consultar e definir dados. Os fundadores do Spark quiseram tirar partido destes conhecimentos e abrir esta famosa linguagem de consulta de dados a um público mais vasto de analistas que querem trabalhar com dados que residem no Sistema de Ficheiros Distribuídos do Hadoop (HDFS). Essa oferta é o Spark SQL. Funciona como uma extensão do Apache Spark para o processamento de dados estruturados e utiliza a sintaxe familiar do SQL Server.

O Spark SQL suporta o SQL e o HiveQL como idiomas de consulta. As capacidades da oferta incluem o enlace em Python, Scala e Java. Com o Spark SQL, pode consultar dados armazenados em muitas localizações, como bases de dados externas, ficheiros de dados estruturados (por exemplo, JSON) e tabelas do Hive.

### <a name="running-spark-sql-on-an-hdinsight-cluster"></a>Executar o Spark SQL num cluster do HDInsight

Quando utiliza um bloco de notas do Jupyter configurado para o seu cluster do HDInsight Spark, obtém um `sqlContext` predefinido que pode utilizar para executar consultas do Hive com o Spark SQL. Nesta secção, vai aprender a iniciar um bloco de notas do Jupyter e, em seguida, a executar uma consulta do Spark SQL básica numa tabela do Hive já existente (**hivesampletable**) e que está disponível em todos os clusters do HDInsight.

1. Abra o [Portal do Azure](https://portal.azure.com/).

2. Se optou por afixar o cluster ao dashboard, clique no mosaico do cluster a partir do dashboard para iniciar o painel do cluster.

    Se não afixou o cluster ao dashboard, no painel da esquerda, clique em **Clusters do HDInsight** e, em seguida, clique no cluster que criou.

3. A partir de **Ligações rápidas**, clique em **Dashboards de clusters** e, em seguida, clique em **Bloco de Notas do Jupyter**. Se lhe for solicitado, introduza as credenciais de administrador do cluster.

   ![Abra o bloco de notas Jupyter para executar a consulta interativa do Sparl SQL](./media/apache-spark-jupyter-spark-sql/hdinsight-spark-open-jupyter-interactive-spark-sql-query.png "Open Jupyter notebook to run interactive Spark SQL query")

   > [!NOTE]
   > Também pode abrir o URL seguinte no seu browser para aceder ao bloco de notas Jupyter do cluster. Substitua **CLUSTERNAME** pelo nome do cluster:
   >
   > `https://CLUSTERNAME.azurehdinsight.net/jupyter`
   >
   >
3. Crie um bloco de notas. Clique em **Novo** e, em seguida, clique em **PySpark**.

   ![Crie um bloco de notas Jupyter para executar a consulta interativa do Spark SQL](./media/apache-spark-jupyter-spark-sql/hdinsight-spark-create-jupyter-interactive-spark-sql-query.png "Create a Jupyter notebook to run interactive Spark SQL query")

   É criado e aberto um novo bloco de notas com o nome Untitled (Untitled.pynb).

4. Clique no nome do bloco de notas na parte superior e introduza um nome amigável se assim o desejar.

    ![Indique um nome para o bloco de notas do Jupyter para executar uma consulta interativa do Spark](./media/apache-spark-jupyter-spark-sql/hdinsight-spark-jupyter-notebook-name.png "Provide a name for the Jupter notebook to run interactive Spark query from")

5.  Cole o seguinte código numa célula vazia e, em seguida, prima **SHIFT + ENTER** para o executar. No código abaixo, `%%sql` (chamado “sql magic”) indica ao bloco de notas do Jupyter que utilize o `sqlContext` predefinido para executar a consulta do Hive. A consulta devolve as primeiras dez linhas de uma tabela do Hive (**hivesampletable**) que está disponível por predefinição em todos os clusters do HDInsight.

        %%sql
        SELECT * FROM hivesampletable LIMIT 10

    ![Consulta do Hive no HDInsight Spark](./media/apache-spark-jupyter-spark-sql/hdinsight-spark-get-started-hive-query.png "Consulta do Hive no HDInsight Spark")

    Para obter mais informações sobre o `%%sql` magic e os contextos predefinidos, veja [Jupyter kernels available for an HDInsight cluster](apache-spark-jupyter-notebook-kernels.md) (Kernels do Jupyter disponíveis para clusters do HDInsight).

    > [!NOTE]
    > Sempre que executar uma consulta no Jupyter, o título da janela do browser apresenta o estado **(Ocupado)** juntamente com o título do bloco de notas. Também vê um círculo sólido junto ao texto do **PySpark** no canto superior direito. Após a conclusão da tarefa, este é alterado para um círculo vazio.
    >
    >
    
6. O ecrã deverá atualizar-se e mostrar o resultado da consulta.

    ![Hive query output in HDInsight Spark](./media/apache-spark-jupyter-spark-sql/hdinsight-spark-get-started-hive-query-output.png "(Saída de consulta do Hive no HDInsight Spark)")

7. Encerre o bloco de notas para libertar os recursos do cluster depois de terminar a execução da aplicação. Para o fazer, no menu **Ficheiro** do bloco de notas, clique em **Fechar e Parar**.

8. Se quiser concluir os passos seguintes mais tarde, não se esqueça de eliminar o cluster do HDInsight que criou neste artigo. 

[!INCLUDE [delete-cluster-warning](../../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="next-step"></a>Passo seguinte 

Neste artigo, aprendeu a criar um cluster do HDInsight Spark e a executar uma consulta do Spark SQL básica. Avance para o artigo seguinte para saber como utilizar um cluster do HDInsight Spark para executar consultas interativas nos dados de exemplo.

> [!div class="nextstepaction"]
>[Executar consultas interativas num cluster do Spark do HDInsight](apache-spark-load-data-run-query.md)



