---
title: Visualizar macrodados com o Power BI no Azure HDInsight | Microsoft Docs
description: Saiba como utilizar o Microsoft Power BI para visualizar dados do Hive, processados pelo Azure HDInsight.
keywords: hdinsight, hadoop, o hive, interativa consulta hive interativa, LLAP, odbc
services: hdinsight
documentationcenter: 
author: mumian
manager: jhubbard
editor: cgronlun
ms.assetid: 
ms.service: hdinsight
ms.custom: hdinsightactive,
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/26/2017
ms.author: jgao
ms.openlocfilehash: 390342eb08ae970fa760b414674b1a6783404d80
ms.sourcegitcommit: f8437edf5de144b40aed00af5c52a20e35d10ba1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/03/2017
---
# <a name="visualize-hive-data-with-microsoft-power-bi-in-azure-hdinsight"></a>Visualizar dados do Hive com o Microsoft Power BI no Azure HDInsight

Saiba como ligar o Microsoft Power BI para o Azure HDInsight e visualizar os dados do Hive. Atualmente, o Power BI só suporta a ligação de ODBC ao HDInsight. Neste tutorial, carregar os dados de uma tabela de Hive hivesampletable ao Power BI. A tabela de Hive contém alguns dados de utilização de telemóvel. Em seguida, representar os dados de utilização num mapa mundo:

![HDInsight Power BI o relatório de mapa](./media/apache-hadoop-connect-hive-power-bi/hdinsight-power-bi-visualization.png)

As informações também se aplica ao novo [consulta interativa](../interactive-query/apache-interactive-query-get-started.md) tipo de cluster.

## <a name="prerequisites"></a>Pré-requisitos
Antes de passar neste artigo, tem de ter os seguintes itens:

* **Cluster do HDInsight**. O cluster pode ser um cluster do HDInsight com o Hive ou um cluster de consulta interativa recentemente publicado. Para criar clusters, consulte [Criar cluster](apache-hadoop-linux-tutorial-get-started.md#create-cluster).
* **[Ambiente de trabalho do Microsoft Power BI](https://powerbi.microsoft.com/desktop/)**. Pode transferir uma cópia do [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=45331).

## <a name="create-hive-odbc-data-source"></a>Criar a origem de dados do ODBC do Hive

Consulte [origem de dados criar ODBC do Hive](apache-hadoop-connect-excel-hive-odbc-driver.md#create-hive-odbc-data-source).

## <a name="load-data-from-hdinsight"></a>Carregar dados do HDInsight

A tabela de Hive hivesampletable inclui todos os clusters do HDInsight.

1. Inicie sessão no Power BI Desktop.
2. Clique em de **home page** separador, clique em **obter dados** do **dados externos** do Friso e, em seguida, selecione **mais**.

    ![Dados aberto HDInsight Power BI](./media/apache-hadoop-connect-hive-power-bi/hdinsight-power-bi-open-odbc.png)
3. Do **obter dados** painel, clique em **outros** da esquerda, clique em **ODBC** partir da direita e, em seguida, clique em **Connect** na parte inferior.
4. Do **de ODBC** painel, selecione os dados que criou na última secção do nome da origem e, em seguida, clique em **OK**.
5. Do **navegador** painel, expanda **ODBC -> HIVE -> predefinido**, selecione **hivesampletable**e, em seguida, clique em **carga**.

## <a name="visualize-date"></a>Visualizar data

Continue a partir do último procedimento.

1. A partir do painel de visualizações, selecione **mapa**.  É um ícone de globo.

    ![Relatório de customizes HDInsight Power BI](./media/apache-hadoop-connect-hive-power-bi/hdinsight-power-bi-customize.png)
2. No painel campos, selecione **país** e **devicemake**. Pode ver os dados desenhados no mapa.
3. Expanda o mapa.

## <a name="next-steps"></a>Passos seguintes
Neste artigo, aprendeu a visualizar dados do HDInsight através do Power BI.  Para obter mais informações, consulte os artigos seguintes:

* [Utilize Zeppelin para executar consultas do Hive no Azure HDInsight ](./../hdinsight-connect-hive-zeppelin.md).
* [Ligar o Excel para o HDInsight com o controlador ODBC do Microsoft Hive](./apache-hadoop-connect-excel-hive-odbc-driver.md).
* [Ligar o Excel ao Hadoop com o Power Query](apache-hadoop-connect-excel-power-query.md).
* [Ligar ao Azure HDInsight e executar consultas do Hive, utilizando ferramentas do Data Lake para Visual Studio](apache-hadoop-visual-studio-tools-get-started.md).
* [Utilizar a ferramenta do Azure HDInsight para Visual Studio Code](../hdinsight-for-vscode.md).
* [Carregar dados para o HDInsight](./../hdinsight-upload-data.md).
