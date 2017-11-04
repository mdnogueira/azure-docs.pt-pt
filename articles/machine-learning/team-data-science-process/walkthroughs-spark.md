---
title: "Instruções de Spark do HDInsight utilizando PySpark e Scala no Azure | Microsoft Docs"
description: "Exemplos do processo de ciência de dados de equipa percurso através da utilização de PySpark e Scala num Azure HDInsight Spark para realizar a Análise Preditiva."
services: machine-learning
documentationcenter: 
author: bradsev
manager: jhubbard
editor: cgronlun
ms.assetid: 
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/04/2017
ms.author: bradsev
ms.openlocfilehash: 76c9382cc5210bb5ac8551ebb7b16ecabe0dcd39
ms.sourcegitcommit: f8437edf5de144b40aed00af5c52a20e35d10ba1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/03/2017
---
# <a name="hdinsight-spark-data-science-walkthroughs-using-pyspark-and-scala-on-azure"></a>Instruções de ciência de dados de Spark do HDInsight utilizando PySpark e Scala no Azure

Estas instruções utilizam PySpark e Scala num cluster do Spark do Azure para realizar a Análise Preditiva. Siga os passos descritos no processo de ciência de dados de equipa. Para obter uma descrição geral do processo de ciência de dados de equipa, consulte [o processo de ciência de dados](overview.md). Para obter uma descrição geral do Spark no HDInsight, consulte [introdução para o Spark no HDInsight](../../hdinsight/spark/apache-spark-overview.md).

Instruções de ciência de dados adicionais que executar o processo de ciência de dados do agrupamento estão agrupadas pelo **plataforma** que utilizam. Consulte [instruções ao executar o processo de ciência de dados de equipa](walkthroughs.md) para um itemization destes exemplos.

## <a name="predict-taxi-tips-using-pyspark-on-azure-spark"></a>Prever sugestões taxi utilizando PySpark no Azure Spark

O [utilizar o Spark no Azure HDInsight](spark-overview.md) instruções utilizam dados da Nova Iorque taxis ao prever se uma sugestão é pagas e o intervalo de quantidades esperado paga. Utiliza o processo de ciência de dados de equipa num cenário com um [cluster Azure HDInsight Spark](https://azure.microsoft.com/services/hdinsight/) para armazenar, explore, funcionalidade dados engenheiro a viagem de taxi NYC publicamente disponível e fare conjunto de dados. Este tópico de descrição geral define a cópia de segurança com um cluster do HDInsight Spark e o Jupyter PySpark notebooks utilizados no resto das instruções. Estes blocos de notas mostram-lhe como explore os seus dados e, em seguida, como criar e consumir modelos. O bloco de notas do exploração e modelação de dados avançadas mostra como a incluir validação cruzada, hyper varrimento, parâmetro e modelo de avaliação.

### <a name="data-exploration-and-modeling-with-spark"></a>Exploração de dados e modelação com o Spark 
Explorar o conjunto de dados e criar, Pontuar e avaliar os modelos de machine learning ao trabalhar com o [criar modelos de classificação e regressão binários de dados com o toolkit de Spark MLlib](spark-data-exploration-modeling.md) tópico.

### <a name="model-consumption"></a>Consumo de modelo
Para saber como Pontuar os modelos de classificação e regressão criados neste tópico, consulte [classificação e avaliar os modelos de incorporadas do Spark machine learning](spark-model-consumption.md).

### <a name="cross-validation-and-hyperparameter-sweeping"></a>Validação cruzada e hyperparameter varrimento
Consulte [avançada de exploração de dados e modelação com o Spark](spark-advanced-data-exploration-modeling.md) na forma como os modelos podem ser preparado varrimento de validação cruzada e hyper parâmetro a utilizar.


## <a name="predict-taxi-tips-using-scala-on-azure-spark"></a>Prever sugestões taxi com o Scala no Azure Spark

O [Scala de utilização com o Spark no Azure](scala-walkthrough.md) instruções utilizam dados da Nova Iorque taxis ao prever se uma sugestão é pagas e o intervalo de quantidades esperado paga. Mostra como utilizar o Scala para supervisionado do machine learning tarefas com o Spark do machine learning biblioteca (MLlib) e pacotes de SparkML num cluster do Azure HDInsight Spark. Explica como as tarefas que constituem o [o processo de ciência de dados](http://aka.ms/datascienceprocess): ingestão de dados e exploração, visualização, funcionalidade engenharia, modelação e consumo de modelo. Os modelos criados incluem regressão linear e logística da, florestas aleatórias e gradação árvores elevadas.


## <a name="next-steps"></a>Passos seguintes

Para ver um debate os componentes chave que compõem o processo de ciência de dados de equipa, consulte [descrição geral do processo de ciência de dados de equipa](overview.md).

Para um debate sobre o ciclo de vida do processo de ciência de dados de equipa que pode utilizar para estruturar seus projetos de ciência de dados, consulte [ciclo de vida do processo de ciência de dados de equipa](lifecycle.md). O ciclo de vida descreve os passos, do início ao fim, que projetos siga, normalmente, quando estes são executados. 

