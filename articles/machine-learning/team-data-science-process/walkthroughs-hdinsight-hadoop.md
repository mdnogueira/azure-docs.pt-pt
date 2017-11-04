---
title: "Instruções de ciência de dados de Hadoop do HDInsight utilizando o Hive no Azure | Microsoft Docs"
description: "Exemplos do processo de ciência de dados de equipa percurso através da utilização de ramo de registo no Azure HDInsight Hadoop para Análise Preditiva."
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
ms.openlocfilehash: cef2dac8651933765c83410579eafb23460960f9
ms.sourcegitcommit: f8437edf5de144b40aed00af5c52a20e35d10ba1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/03/2017
---
# <a name="hdinsight-hadoop-data-science-walkthroughs-using-hive-on-azure"></a>Instruções de ciência de dados de Hadoop do HDInsight utilizando o Hive no Azure 

Estas instruções utilizam o Hive com um cluster do HDInsight Hadoop para Análise Preditiva. Siga os passos descritos no processo de ciência de dados de equipa. Para obter uma descrição geral do processo de ciência de dados de equipa, consulte [o processo de ciência de dados](overview.md). Para uma introdução ao Azure HDInsight, consulte [introdução ao Azure HDInsight, a pilha de tecnologia de Hadoop e clusters do Hadoop](../../hdinsight/hadoop/apache-hadoop-introduction.md).

Instruções de ciência de dados adicionais que executar o processo de ciência de dados do agrupamento estão agrupadas pelo **plataforma** que utilizam. Consulte [instruções ao executar o processo de ciência de dados de equipa](walkthroughs.md) para um itemization destes exemplos.


## <a name="predict-taxi-tips-using-hive-with-hdinsight-hadoop"></a>Prever sugestões taxi utilizando o Hive com o Hadoop do HDInsight

O [clusters do Hadoop do HDInsight de utilização](hive-walkthrough.md) instruções utilizam dados da Nova Iorque taxis para prever: 

- Indica se uma sugestão é paga 
- A distribuição de quantidades de sugestão

O cenário é implementado utilizando o Hive com uma [cluster Azure HDInsight Hadoop](https://azure.microsoft.com/services/hdinsight/). Saiba como armazenar, explore e dados da funcionalidade engenheiro de um NYC publicamente disponível taxi viagem e fare conjunto de dados. Também é utilizar o Azure Machine Learning para criar e implementar os modelos.

## <a name="predict-advertisement-clicks-using-hive-with-hdinsight-hadoop"></a>Prever cliques de anúncios utilizando o Hive com o Hadoop do HDInsight

O [utilização do Azure HDInsight Hadoop Clusters um conjunto de dados de 1 TB](hive-criteo-walkthrough.md) instruções utilizam um publicamente disponíveis [Criteo](http://labs.criteo.com/downloads/download-terabyte-click-logs/) clique em conjunto de dados para prever se uma sugestão é pagas e o intervalo de quantidades esperado. O cenário é implementado utilizando o Hive com uma [cluster Azure HDInsight Hadoop](https://azure.microsoft.com/services/hdinsight/) para armazenar, explore, engenheiro de funcionalidade e pendente dados de exemplo. Utiliza o Azure Machine Learning para criar, dar formação e Pontuar um modelo de classificação binária prever se um utilizador clica num anúncio. Conclui as instruções que mostra como publicar um destes modelos como um serviço Web.


## <a name="next-steps"></a>Passos seguintes

Para ver um debate os componentes chave que compõem o processo de ciência de dados de equipa, consulte [descrição geral do processo de ciência de dados de equipa](overview.md).

Para um debate sobre o ciclo de vida do processo de ciência de dados de equipa que pode utilizar para estruturar seus projetos de ciência de dados, consulte [ciclo de vida do processo de ciência de dados de equipa](lifecycle.md). O ciclo de vida descreve os passos, do início ao fim, que projetos siga, normalmente, quando estes são executados. 

