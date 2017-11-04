---
title: Analisar dados de sensores utilizando o Hive e o Hadoop - Azure HDInsight | Microsoft Docs
description: Saiba como analisar dados de sensores utilizando a consola de consulta do Hive com o HDInsight (Hadoop), em seguida, visualizar os dados no Microsoft Excel com PowerView.
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: a8ac160c-1cef-45d9-bf36-7beb5a439105
ms.service: hdinsight
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/14/2017
ms.author: larryfr
ROBOTS: NOINDEX
ms.openlocfilehash: d4d216cd1d8de7c86f71d0181270607e12f761cf
ms.sourcegitcommit: f8437edf5de144b40aed00af5c52a20e35d10ba1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/03/2017
---
# <a name="analyze-sensor-data-using-the-hive-query-console-on-hadoop-in-hdinsight"></a>Analisar dados de sensores utilizando a consola de consulta do Hive do Hadoop no HDInsight

Saiba como analisar dados de sensores utilizando a consola de consulta do Hive com o HDInsight (Hadoop), em seguida, visualizar os dados no Microsoft Excel, utilizando o Power View.

> [!IMPORTANT]
> Os passos neste documento apenas trabalham com clusters do HDInsight baseados em Windows. HDInsight só está disponível no Windows para as versões inferiores ao HDInsight 3.4. O Linux é o único sistema operativo utilizado na versão 3.4 ou superior do HDInsight. Para obter mais informações, veja [HDInsight retirement on Windows](../hdinsight-component-versioning.md#hdinsight-windows-retirement) (Desativação do HDInsight no Windows).


Neste exemplo, utilizar o Hive para processar os dados históricos e identificar problemas com os sistemas de aquecimento e ar condicionado. Especificamente, que identifica os sistemas não são capazes de forma fiável manter uma temperatura definida, efetuando as seguintes tarefas:

* Crie as tabelas do HIVE para consultar dados armazenados nos ficheiros de ficheiro de valores separados por vírgulas (CSV).
* Crie consultas do HIVE para analisar os dados.
* Para obter os dados analisados, utilize o Microsoft Excel para ligar ao HDInsight.
* Para visualizar os dados, utilize o Power View.

![Um diagrama de arquitetura da solução](./media/apache-hive-analyze-sensor-data/hvac-architecture.png)

## <a name="prerequisites"></a>Pré-requisitos

* Um cluster do HDInsight (Hadoop): consulte [clusters do Hadoop criar no HDInsight](../hdinsight-hadoop-provision-linux-clusters.md) para obter informações sobre como criar um cluster.
* Microsoft Excel 2013

  > [!NOTE]
  > Microsoft Excel é utilizado para visualização de dados com [Power View](https://support.office.com/Article/Power-View-Explore-visualize-and-present-your-data-98268d31-97e2-42aa-a52b-a68cf460472e?ui=en-US&rs=en-US&ad=US).

* [Controlador ODBC do Microsoft Hive](http://www.microsoft.com/download/details.aspx?id=40886)

## <a name="to-run-the-sample"></a>Para executar o exemplo

1. O web browser, navegue para o seguinte URL: 

         https://<clustername>.azurehdinsight.net

    Substitua `<clustername>` pelo nome do seu cluster do HDInsight.

    Quando lhe for pedido, autentique-se utilizando o nome de utilizador de administrador e a palavra-passe que utilizou quando este cluster de aprovisionamento.

2. Da página web que se abre, clique no **Getting iniciado galeria** separador e, no **soluções com dados de exemplo** categoria, clique em de **análise de dados de Sensor** exemplo.

    ![Obter a imagem de introdução da Galeria](./media/apache-hive-analyze-sensor-data/getting-started-gallery.png)

3. Siga as instruções fornecidas na página web para concluir o exemplo.
