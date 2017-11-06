---
title: Ligar o Excel ao Hadoop com o Power Query - o Azure HDInsight | Microsoft Docs
description: Saiba como tirar partido dos componentes do business intelligence e utilize o Power Query para Excel para aceder a dados armazenados no Hadoop no HDInsight.
services: hdinsight
documentationcenter: 
tags: azure-portal
author: mumian
manager: jhubbard
editor: cgronlun
ms.assetid: 01ad2f90-7520-44d9-8c16-4d936faaff9b
ms.service: hdinsight
ms.custom: hdinsightactive
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: jgao
ms.openlocfilehash: e40ddd50a84dfbd8e31a13cef7e4342658bb4f49
ms.sourcegitcommit: f8437edf5de144b40aed00af5c52a20e35d10ba1
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/03/2017
---
# <a name="connect-excel-to-hadoop-by-using-power-query"></a>Ligar o Excel ao Hadoop com o Power Query
Uma funcionalidade-chave da solução de macrodados Microsoft é a integração de componentes do Microsoft business intelligence (BI) com clusters do Hadoop no Azure HDInsight. Um exemplo primário é a capacidade de ligar o Excel à conta de armazenamento do Azure que contém os dados associados com o cluster de Hadoop, utilizando o Microsoft Power Query para o suplemento do Excel. Este artigo explica como configurar e utilizar o Power Query para consultar dados associados a um cluster de Hadoop gerido com o HDInsight.

### <a name="prerequisites"></a>Pré-requisitos
Antes de começar este artigo, tem de ter os seguintes itens:

* **Um cluster do HDInsight**. Para configurar um, consulte [introdução ao Azure HDInsight] [hdinsight-get-started].
* **Uma estação de trabalho** que está a executar Windows 7, Windows Server 2008 R2 ou um sistema operativo posterior.
* **Office 2016, Office 2013 Professional Plus, Office 365 ProPlus, autónomo do Excel 2013 ou Office 2010 Professional Plus**.

## <a name="install-power-query"></a>Instalar o Power Query
Power Query pode importar os dados que tenha sido saída ou que foi gerado por uma tarefa de Hadoop em execução num cluster do HDInsight.

Excel 2016, o Power Query tem foram integrada no Friso dados na secção obter & transformação. Para versões antigas do Excel, transferir o Microsoft Power Query para Excel a partir de [Microsoft Download Center] [ powerquery-download] e instalá-la.

## <a name="import-hdinsight-data-into-excel"></a>Importar dados do HDInsight para o Excel
O suplemento Power Query para Excel torna mais fácil importar dados de cluster do HDInsight para o Excel, onde as ferramentas de BI, tais como o PowerPivot e mapa de energia podem ser utilizadas para inspecionar os, analisam e apresentam os dados.

**Para importar dados a partir de um cluster do HDInsight**

1. Abra o Excel.
2. Crie um novo livro em branco.
3. Execute os seguintes passos com base na versão do Excel:

    - Excel 2016

        - Clique em de **dados** menu, clique em **obter dados** do **Get & transformar dados** do Friso, clique em **do Azure**e, em seguida, clique em **Do Azure HDInsight(HDFS)**.

        ![HDI. PowerQuery.SelectHdiSource](./media/apache-hadoop-connect-excel-power-query/hdi.powerquery.selecthdisource.excel2016.png)

    - Excel 2013/2010

        - Clique em de **Power Query** menu, clique em **do Azure**e, em seguida, clique em **do Microsoft Azure HDInsight**.
   
        ![HDI. PowerQuery.SelectHdiSource][image-hdi-powerquery-hdi-source]
       
        **Nota:** se não vir o **Power Query** menu, aceda a **ficheiro** > **opções** > **suplementos**e selecione **suplementos COM** da lista pendente **gerir** caixa na parte inferior da página. Selecione o **aceda...**  botão e certifique-se de que a caixa para o Power Query para o suplemento do Excel tiver verificada.
       
        **Nota:** Power Query também lhe permite importar dados do HDFS clicando **de outras origens**.
4. Para **nome da conta**, introduza o nome da conta de armazenamento de Blobs do Azure associada ao seu cluster e, em seguida, clique em **OK**. Esta conta pode ser o [predefinido a conta de armazenamento](../hdinsight-administer-use-management-portal.md#find-the-default-storage-account) ou uma conta do storage ligadas.  O formato é *https://&lt;StorageAccountName >.blob.core.windows.net/*.
5. Para **chave da conta**, introduza a chave para a conta de armazenamento de Blob e, em seguida, clique em **guardar**. (Tem de introduzir o tempo de informações apenas a primeira conta que aceder a este arquivo.)
6. No **navegador** painel à esquerda do Editor de consultas, faça duplo clique o nome de contentor do Blob storage. Por predefinição, o nome do contentor é o mesmo nome que o nome do cluster.
7. Localizar **HiveSampleData.txt** no **nome** coluna (o caminho da pasta é **../ hive/armazém/hivesampletable/**) e, em seguida, clique em **binário** à esquerda do HiveSampleData.txt. HiveSampleData.txt inclui todos os clusters. Opcionalmente, pode utilizar o seu próprio ficheiro.
   
    ![HDI. PowerQuery.ImportData][image-hdi-powerquery-importdata]
8. Se quiser, pode mudar o nome dos nomes das colunas. Quando estiver pronto, clique em **feche & carregar**.  Os dados terem sido carregados ao seu livro:
   
    ![HDI. PowerQuery.ImportedTable][image-hdi-powerquery-imported-table]

## <a name="next-steps"></a>Passos seguintes
Neste artigo, aprendeu a utilizar o Power Query para obter dados do HDInsight para o Excel. Da mesma forma, poderá obter dados a partir do HDInsight numa SQL Database do Azure. Também é possível carregar dados para o HDInsight. Para obter mais informações, consulte os artigos seguintes:

* [Visualizar dados do Hive com o Microsoft Power BI no Azure HDInsight](apache-hadoop-connect-hive-power-bi.md).
* [Utilize Zeppelin para executar consultas do Hive no Azure HDInsight ](./../hdinsight-connect-hive-zeppelin.md).
* [Ligar o Excel para o HDInsight com o controlador ODBC do Microsoft Hive](apache-hadoop-connect-excel-hive-odbc-driver.md).
* [Ligar ao Azure HDInsight e executar consultas do Hive, utilizando ferramentas do Data Lake para Visual Studio](apache-hadoop-visual-studio-tools-get-started.md).
* [Utilizar a ferramenta do Azure HDInsight para Visual Studio Code](../hdinsight-for-vscode.md).
* [Carregar dados para o HDInsight](./../hdinsight-upload-data.md).

[image-hdi-powerquery-hdi-source]: ./media/apache-hadoop-connect-excel-power-query/hdi.powerquery.selecthdisource.png
[image-hdi-powerquery-importdata]: ./media/apache-hadoop-connect-excel-power-query/hdi.powerquery.importdata.png
[image-hdi-powerquery-imported-table]: ./media/apache-hadoop-connect-excel-power-query/hdi.powerquery.importedtable.PNG

[powerquery-download]: http://go.microsoft.com/fwlink/?LinkID=286689
