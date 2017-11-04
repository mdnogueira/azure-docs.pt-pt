---
title: "Utilizar o Hive com o Hadoop para análise de registos de Web site - Azure HDInsight | Microsoft Docs"
description: "Saiba como utilizar o Hive com o HDInsight para analisar os registos de Web site. Irá utilizar um ficheiro de registo como entrada para uma tabela do HDInsight e utilizar o HiveQL para consultar os dados."
services: hdinsight
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 6fb7b5c2-8df4-40b1-a9e2-6815080004f9
ms.service: hdinsight
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/17/2016
ms.author: nitinme
ROBOTS: NOINDEX
ms.openlocfilehash: 5aabb69dc233dfd927c1d6cc1b131115e2d096d4
ms.sourcegitcommit: f8437edf5de144b40aed00af5c52a20e35d10ba1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/03/2017
---
# <a name="use-hive-with-windows-based-hdinsight-to-analyze-logs-from-websites"></a>Utilizar o Hive com o HDInsight baseado em Windows para analisar os registos de Web sites
Saiba como utilizar o HiveQL com o HDInsight para analisar os registos de um Web site. Análise de registos de Web site pode ser utilizado para segmentar o público-alvo com base em atividades semelhantes, categorizar os visitantes do site por dados demográficos e para saber o conteúdo vista, Web sites entram do e assim sucessivamente.

> [!IMPORTANT]
> Os passos neste documento apenas trabalham com clusters do HDInsight baseados em Windows. HDInsight só está disponível no Windows para as versões inferiores ao HDInsight 3.4. O Linux é o único sistema operativo utilizado na versão 3.4 ou superior do HDInsight. Para obter mais informações, veja [HDInsight retirement on Windows](../hdinsight-component-versioning.md#hdinsight-windows-retirement) (Desativação do HDInsight no Windows).

Neste exemplo, pode utilizar um cluster do HDInsight para analisar ficheiros de registo do Web site para obter informações sobre a frequência das visitas durante para o Web site de Web sites externos por dia. Também gerar um resumo dos erros de Web sites que os utilizadores se deparam. Saiba como:

* Ligar a um armazenamento de Blobs do Azure, que contém ficheiros de registo do Web site.
* Crie as tabelas do HIVE para consultar esses registos.
* Crie consultas do HIVE para analisar os dados.
* Utilizar o Microsoft Excel para ligar ao HDInsight (utilizando a conectividade de base de dados aberta (ODBC) para obter os dados analisados.

![HDI. Samples.Website.Log.Analysis](./media/apache-hive-analyze-website-log/hdinsight-weblogs-sample.png)

## <a name="prerequisites"></a>Pré-requisitos
* Tem de ter aprovisionado um cluster do Hadoop no Azure HDInsight. Para obter instruções, consulte [aprovisionar Clusters do HDInsight](../hdinsight-hadoop-provision-linux-clusters.md).
* Tem de ter o Microsoft Excel 2013 ou o Excel 2010 instalado.
* Tem de ter [controlador ODBC do Hive Microsoft](http://www.microsoft.com/download/details.aspx?id=40886) para importar dados a partir do ramo de registo para o Excel.

## <a name="to-run-the-sample"></a>Para executar o exemplo
1. Do [portal do Azure](https://portal.azure.com/), partir do Startboard (se o tiver afixado cluster existe), clique no mosaico de cluster no qual pretende executar o exemplo.
2. Do painel do cluster, em **ligações rápidas**, clique em **Cluster Dashboard**e, em seguida, o **Cluster Dashboard** painel, clique em **Cluster do HDInsight Dashboard**. Em alternativa, pode abrir o dashboard diretamente, utilizando o seguinte URL:

         https://<clustername>.azurehdinsight.net

    Quando lhe for pedido, autentique-se utilizando o nome de utilizador de administrador e a palavra-passe que utilizou quando o cluster de aprovisionamento.
3. Da página web que se abre, clique em de **Getting iniciado galeria** separador e, no **soluções com dados de exemplo** categoria, clique em de **análise de registos de Web site** exemplo.
4. Siga as instruções fornecidas na página web para concluir o exemplo.

## <a name="next-steps"></a>Passos seguintes
Tente o seguinte exemplo: [analisar dados de sensores utilizando o Hive com o HDInsight](apache-hive-analyze-sensor-data.md).

[hdinsight-sensor-data-sample]: ../hdinsight-use-hive-sensor-data-analysis.md
