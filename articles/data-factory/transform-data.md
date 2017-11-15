---
title: Transformar dados utilizando o Azure Data Factory | Microsoft Docs
description: Saiba como transformar dados ou processamento de dados no Azure Data Factory com o Hadoop, o Machine Learning ou o Azure Data Lake Analytics.
services: data-factory
documentationcenter: 
author: shengcmsft
manager: jhubbard
editor: monicar
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/09/2017
ms.author: shengc
ms.openlocfilehash: 88ff71956c82d79fb244f1a687debeb726291ae8
ms.sourcegitcommit: bc8d39fa83b3c4a66457fba007d215bccd8be985
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/10/2017
---
# <a name="transform-data-in-azure-data-factory"></a>Transformar dados no Azure Data Factory
> [!div class="op_single_selector"]
> * [Hive](transform-data-using-hadoop-hive.md)  
> * [Pig](transform-data-using-hadoop-pig.md)  
> * [MapReduce](transform-data-using-hadoop-map-reduce.md)  
> * [Hadoop Streaming](transform-data-using-hadoop-streaming.md)
> * [Spark](transform-data-using-spark.md)
> * [Machine Learning](transform-data-using-machine-learning.md) 
> * [Procedimento Armazenado](transform-data-using-stored-procedure.md)
> * [Data Lake Analytics U-SQL](transform-data-using-data-lake-analytics.md)
> * [.NET personalizado](transform-data-using-dotnet-custom-activity.md)

## <a name="overview"></a>Descrição geral
Este artigo explica as atividades de transformação de dados no Azure Data Factory que pode utilizar para transformar e processa dados não processados para predições e das informações. Executa uma atividade de transformação num ambiente informático, como o Azure HDInsight cluster ou um lote do Azure. Fornece ligações para artigos com informações detalhadas sobre cada atividade de transformação.

Fábrica de dados suporta as atividades de transformação de dados seguintes que podem ser adicionadas a [pipelines](concepts-pipelines-activities.md) o individualmente ou encadeados com outra atividade.

> [!NOTE]
> Este artigo aplica-se à versão 2 do Data Factory, que está atualmente em pré-visualização. Se estiver a utilizar a versão 1 do serviço do Data Factory, o que é geralmente disponível (DG), consulte [atividades de transformação de versão 1 do Data Factory](v1/data-factory-data-transformation-activities.md).
 

## <a name="hdinsight-hive-activity"></a>Atividade do ramo de registo do HDInsight
A atividade do ramo de registo do HDInsight num pipeline Data Factory executa consultas do Hive no seu próprio ou cluster de HDInsight baseado em Windows/Linux a pedido. Consulte [atividade do Hive](transform-data-using-hadoop-hive.md) artigo para obter detalhes sobre esta atividade. 

## <a name="hdinsight-pig-activity"></a>Atividade do HDInsight Pig
A atividade do HDInsight Pig num pipeline Data Factory executa as consultas de Pig por si ou cluster de HDInsight baseado em Windows/Linux a pedido. Consulte [Pig atividade](transform-data-using-hadoop-pig.md) artigo para obter detalhes sobre esta atividade. 

## <a name="hdinsight-mapreduce-activity"></a>Atividade de HDInsight MapReduce
A atividade de HDInsight MapReduce no pipeline fábrica de dados executa programas de MapReduce na sua própria ou cluster de HDInsight baseado em Windows/Linux a pedido. Consulte [atividade de MapReduce](transform-data-using-hadoop-map-reduce.md) artigo para obter detalhes sobre esta atividade.

## <a name="hdinsight-streaming-activity"></a>Atividade de transmissão em fluxo do HDInsight
A atividade de transmissão em fluxo do HDInsight num pipeline Data Factory executa programas de transmissão em fluxo do Hadoop na sua própria ou cluster de HDInsight baseado em Windows/Linux a pedido. Consulte [atividade de transmissão em fluxo do HDInsight](transform-data-using-hadoop-streaming.md) para obter detalhes sobre esta atividade.

## <a name="hdinsight-spark-activity"></a>Atividade do HDInsight Spark
A atividade do HDInsight Spark num pipeline Data Factory executa programas do Spark no seu próprio cluster do HDInsight. Para obter mais informações, consulte [programas invocar Spark de Azure Data Factory](transform-data-using-spark.md). 

## <a name="machine-learning-activities"></a>Atividades do Machine Learning
O Azure Data Factory permite-lhe facilmente criar pipelines que utilizam um serviço web do Azure Machine Learning publicado para Análise Preditiva. Utilizar o [atividade de execução de lote](transform-data-using-machine-learning.md) um pipeline do Azure Data Factory, pode invocar um serviço web do Machine Learning para tornar as predições nos dados no batch.

Ao longo do tempo, os modelos preditivos no Machine Learning experimentações da classificação tem de ser retrained utilizando conjuntos de dados de entrada novo. Depois de terminar com reparametrização, que pretende atualizar o serviço web de classificação com o modelo de Machine Learning retrained. Pode utilizar o [atividade de atualizar o recurso](update-machine-learning-models.md) para atualizar o serviço web com o modelo treinado recentemente.  

Consulte [atividades de utilização Machine Learning](transform-data-using-machine-learning.md) para obter detalhes sobre estas atividades de Machine Learning. 

## <a name="stored-procedure-activity"></a>Atividade de procedimento armazenado
Pode utilizar a atividade de procedimento de armazenado do SQL Server num pipeline Data Factory para invocar um procedimento armazenado dos arquivos de dados seguintes: SQL Database do Azure, Azure SQL Data Warehouse, base de dados do SQL Server na sua empresa ou uma VM do Azure. Consulte [atividade de procedimento armazenado](transform-data-using-stored-procedure.md) artigo para obter detalhes.  

## <a name="data-lake-analytics-u-sql-activity"></a>Atividade do Data Lake U-SQL de análise
Atividade do Data Lake U-SQL de análise executa um script de U-SQL num cluster do Azure Data Lake Analytics. Consulte [atividade U-SQL de análise de dados](transform-data-using-data-lake-analytics.md) artigo para obter detalhes. 

## <a name="net-custom-activity"></a>Atividade personalizada do .NET
Se precisar de transformar dados de uma forma que não é suportada pela fábrica de dados, pode criar uma atividade personalizada com a suas próprias lógica de processamento de dados e utilize a atividade no pipeline. Pode configurar a atividade personalizada do .NET a serem executados utilizando um serviço Azure Batch ou um cluster do Azure HDInsight. Consulte [utilizar atividades personalizadas](transform-data-using-dotnet-custom-activity.md) artigo para obter detalhes. 

Pode criar uma atividade personalizada para executar scripts R no seu cluster do HDInsight com R instalado. Veja [Run R Script using Azure Data Factory (Executar Script R com o Azure Data Factory)](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/RunRScriptUsingADFSample). 

## <a name="compute-environments"></a>Ambientes de computação
Criar um serviço ligado para o ambiente de computação e, em seguida, utilizar o serviço ligado quando se definem uma atividade de transformação. Existem dois tipos de ambientes de computação suportadas pela fábrica de dados. 

- **A pedido**: neste caso, o ambiente informático completamente gerido pela fábrica de dados. É automaticamente criada pelo serviço Data Factory antes de uma tarefa é submetida para processar dados e removida quando a tarefa estiver concluída. Pode configurar e controlar as definições do granulares do ambiente de computação a pedido de execução da tarefa, gestão de clusters e bootstrapping ações. 
- **Traga a sua própria**: neste caso, pode registar o seu ambiente informático (por exemplo o cluster de HDInsight) como um serviço ligado no Factory de dados. O ambiente informático é gerido por si e o serviço fábrica de dados utiliza-o para executar as atividades. 

Consulte [serviços ligados de computação](compute-linked-services.md) artigo para saber mais sobre os serviços de computação suportados pela fábrica de dados. 

## <a name="next-steps"></a>Passos seguintes
Consulte o tutorial seguinte para obter um exemplo de utilização de uma atividade de transformação: [Tutorial: transformar dados de utilização do Spark](tutorial-transform-data-spark-powershell.md)