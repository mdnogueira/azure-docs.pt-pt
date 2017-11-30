---
title: Utilizar o Apache Spark para analisar dados no Azure Data Lake Store | Microsoft Docs
description: Executar tarefas do Spark para analisar os dados armazenados no Azure Data Lake Store
services: hdinsight
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: 1f174323-c17b-428c-903d-04f0e272784c
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 11/28/2017
ms.author: nitinme
ms.openlocfilehash: 5a4c7d944577685182240a24c522db4542c09fc2
ms.sourcegitcommit: 29bac59f1d62f38740b60274cb4912816ee775ea
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/29/2017
---
# <a name="use-hdinsight-spark-cluster-to-analyze-data-in-data-lake-store"></a>Utilizar um cluster do Spark do HDInsight para analisar dados no Data Lake Store

Neste tutorial, utilize disponíveis de notas do Jupyter com clusters do HDInsight Spark para executar uma tarefa que lê dados de uma conta do Data Lake Store.

## <a name="prerequisites"></a>Pré-requisitos

* Conta do Azure Data Lake Store. Siga as instruções em [Get started with Azure Data Lake Store using the Azure portal (Introdução ao Azure Data Lake Store com o portal do Azure)](../../data-lake-store/data-lake-store-get-started-portal.md).

* Cluster do HDInsight Spark do Azure Data Lake Store, como armazenamento. Siga as instruções apresentadas em [criar um cluster do HDInsight com o Data Lake Store utilizando o portal do Azure](../../data-lake-store/data-lake-store-hdinsight-hadoop-use-portal.md).

    
## <a name="prepare-the-data"></a>Preparar os dados

> [!NOTE]
> Não é necessário executar este passo se tiver criado o cluster do HDInsight com o Data Lake Store, como armazenamento de predefinido. O processo de criação do cluster adiciona alguns dados de exemplo na conta do Data Lake Store que especificou ao criar o cluster. Avance para a secção [cluster utilizar o Spark do HDInsight com o Data Lake Store](#use-an-hdinsight-spark-cluster-with-data-lake-store).
>
>

Se tiver criado um cluster do HDInsight com o Data Lake Store como armazenamento adicional e BLOBs de armazenamento do Azure como armazenamento de predefinido, deve copiar primeiro através de alguns dados de exemplo para a conta de Data Lake Store. Pode utilizar o exemplo dados de Blob de armazenamento do Azure associados com o cluster do HDInsight. Pode utilizar o [ADLCopy ferramenta](http://aka.ms/downloadadlcopy) para fazê-lo. Transfira e instale a ferramenta a partir da ligação.

1. Abra uma linha de comandos e navegue para o diretório onde está instalada a AdlCopy, normalmente `%HOMEPATH%\Documents\adlcopy`.

2. Execute o seguinte comando para copiar um blob específico a partir do contentor de origem para uma Data Lake Store:

        AdlCopy /source https://<source_account>.blob.core.windows.net/<source_container>/<blob name> /dest swebhdfs://<dest_adls_account>.azuredatalakestore.net/<dest_folder>/ /sourcekey <storage_account_key_for_storage_container>

    Copiar o **HVAC.csv** ficheiros de dados de exemplo no **/HdiSamples/HdiSamples/SensorSampleData/AVAC/** para a conta do Azure Data Lake Store. O fragmento de código deve ter o seguinte aspeto:

        AdlCopy /Source https://mydatastore.blob.core.windows.net/mysparkcluster/HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv /dest swebhdfs://mydatalakestore.azuredatalakestore.net/hvac/ /sourcekey uJUfvD6cEvhfLoBae2yyQf8t9/BpbWZ4XoYj4kAS5Jf40pZaMNf0q6a8yqTxktwVgRED4vPHeh/50iS9atS5LQ==

   > [!WARNING]
   > Certifique-se de que os nomes de ficheiro e caminho são no caso de adequado.
   >
   >
3. Lhe for pedido que introduza as credenciais para a subscrição do Azure na qual tem a conta de Data Lake Store. Ver um resultado semelhante ao seguinte fragmento:

        Initializing Copy.
        Copy Started.
        100% data copied.
        Copy Completed. 1 file copied.

    O ficheiro de dados (**HVAC.csv**) serão copiados sob uma pasta **/hvac** na conta do Data Lake Store.

## <a name="use-an-hdinsight-spark-cluster-with-data-lake-store"></a>Utilizar um cluster do Spark do HDInsight com o Data Lake Store

1. No [Portal do Azure](https://portal.azure.com/), no startboard, clique no mosaico relativo ao cluster do Spark (se o tiver afixado ao startboard). Também pode navegar até ao cluster em **Procurar Tudo** > **Clusters do HDInsight**.

2. No painel do cluster do Spark, clique em **Ligações Rápidas** e, em seguida, no painel **Dashboard de Clusters**, clique em **Bloco de Notas do Jupyter**. Se lhe for solicitado, introduza as credenciais de administrador do cluster.

   > [!NOTE]
   > Também pode aceder ao Bloco de Notas do Jupyter para o cluster abrindo o seguinte URL no seu browser. Substitua **CLUSTERNAME** pelo nome do cluster:
   >
   > `https://CLUSTERNAME.azurehdinsight.net/jupyter`
   >
   >

3. Crie um novo bloco de notas. Clique em **Novo** e, em seguida, clique em **PySpark**.

    ![Criar um novo bloco de notas Jupyter](./media/apache-spark-use-with-data-lake-store/hdinsight-create-jupyter-notebook.png "Criar um novo bloco de notas Jupyter")

4. Uma vez que criou um bloco de notas com o kernel do PySpark, não é necessário criar quaisquer contextos explicitamente. Os contextos do Spark e do Hive serão criados automaticamente quando executa a primeira célula do código. Pode começar por importar os tipos necessários para este cenário. Para o fazer, cole o seguinte fragmento de código numa célula e prima **SHIFT + ENTER**.

        from pyspark.sql.types import *

    Sempre que executar uma tarefa no Jupyter, o título da janela do browser apresentará um estado **(Ocupado)** juntamente com o título do bloco de notas. Também verá um círculo sólido junto ao texto do **PySpark** no canto superior direito. Após a conclusão da tarefa, este é alterado para um círculo vazio.

     ![Estado de uma tarefa de bloco de notas Jupyter](./media/apache-spark-use-with-data-lake-store/hdinsight-jupyter-job-status.png "Estado de uma tarefa de bloco de notas Jupyter")

5. Carregar dados de exemplo para uma tabela temporária, utilizando o **HVAC.csv** ficheiro copiado para a conta de Data Lake Store. Pode aceder a dados na conta do Data Lake Store utilizando o padrão de URL seguinte.

    * Se tiver como armazenamento de predefinido do Data Lake Store, HVAC.csv será no caminho semelhante ao seguinte URL:

            adl://<data_lake_store_name>.azuredatalakestore.net/<cluster_root>/HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv

        Em alternativa, pode também utilizar um formato encurtado, tais como o seguinte:

            adl:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv

    * Se tiver como armazenamento adicional do Data Lake Store, HVAC.csv será na localização onde copiou, tais como:

            adl://<data_lake_store_name>.azuredatalakestore.net/<path_to_file>

     Numa célula vazia, cole o seguinte exemplo de código, substitua **MYDATALAKESTORE** com o seu nome de conta do Data Lake Store e prima **SHIFT + ENTER**. Este exemplo de código regista os dados numa tabela temporária denominada **hvac**.

            # Load the data. The path below assumes Data Lake Store is default storage for the Spark cluster
            hvacText = sc.textFile("adl://MYDATALAKESTORE.azuredatalakestore.net/cluster/mysparkcluster/HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")

            # Create the schema
            hvacSchema = StructType([StructField("date", StringType(), False),StructField("time", StringType(), False),StructField("targettemp", IntegerType(), False),StructField("actualtemp", IntegerType(), False),StructField("buildingID", StringType(), False)])

            # Parse the data in hvacText
            hvac = hvacText.map(lambda s: s.split(",")).filter(lambda s: s[0] != "Date").map(lambda s:(str(s[0]), str(s[1]), int(s[2]), int(s[3]), str(s[6]) ))

            # Create a data frame
            hvacdf = sqlContext.createDataFrame(hvac,hvacSchema)

            # Register the data fram as a table to run queries against
            hvacdf.registerTempTable("hvac")

6. Uma vez que está a utilizar um kernel do PySpark, agora, pode executar diretamente uma consulta de SQL na tabela temporária **hvac** que acabou de criar utilizando a magia `%%sql`. Para obter mais informações sobre a magia `%%sql`, bem como sobre outras magias disponíveis com o kernel do PySpark, consulte [Kernels disponíveis nos blocos de notas do Jupyter com clusters do Spark HDInsight](apache-spark-jupyter-notebook-kernels.md#parameters-supported-with-the-sql-magic).

        %%sql
        SELECT buildingID, (targettemp - actualtemp) AS temp_diff, date FROM hvac WHERE date = \"6/1/13\"

7. Assim que a tarefa for concluída com êxito, é apresentado, por predefinição, o seguinte resultado da tabela.

      ![Saída da tabela do resultado da consulta](./media/apache-spark-use-with-data-lake-store/jupyter-tabular-output.png "Saída da tabela do resultado da consulta")

     Também pode ver os resultados noutras visualizações. Por exemplo, um gráfico de área para o mesmo resultado teria o seguinte aspeto.

     ![Gráfico de área do resultado da consulta](./media/apache-spark-use-with-data-lake-store/jupyter-area-output.png "Gráfico de área do resultado da consulta")

8. Depois de terminar a execução da aplicação, deve encerrar o bloco de notas para libertar os recursos. Para o fazer, no menu **Ficheiro** do bloco de notas, clique em **Fechar e Parar**. Deste modo, o bloco de notas é encerrado e fechado.


## <a name="next-steps"></a>Passos seguintes

* [Criar uma aplicação de Scala para ser executada no cluster do Apache Spark de autónoma](apache-spark-create-standalone-application.md)
* [Utilizar as ferramentas do HDInsight no Toolkit do Azure para o IntelliJ para criar Spark aplicações para o cluster do HDInsight Spark Linux](apache-spark-intellij-tool-plugin.md)
* [Utilizar as ferramentas do HDInsight na Azure Toolkit para Eclipse para criar Spark aplicações para o cluster do HDInsight Spark Linux](apache-spark-eclipse-tool-plugin.md)
