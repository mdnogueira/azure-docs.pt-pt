---
title: Utilizar consultas interativas com o Azure HDInsight | Microsoft Docs
description: Saiba como utilizar a consulta interativa (Hive LLAP) com o HDInsight.
keywords: 
services: hdinsight
documentationcenter: 
tags: azure-portal
author: mumian
manager: jhubbard
editor: cgronlun
ms.assetid: 0957643c-4936-48a3-84a3-5dc83db4ab1a
ms.service: hdinsight
ms.custom: hdinsightactive
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/11/2017
ms.author: jgao
ms.openlocfilehash: 6da89b7b63231fe5886c82e84e06abc2ee7b3e38
ms.sourcegitcommit: f8437edf5de144b40aed00af5c52a20e35d10ba1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/03/2017
---
# <a name="use-interactive-query-with-hdinsight"></a>Utilizar consultas interativas com o HDInsight
Consulta interativa (também denominado de ramo de registo LLAP, ou [longo em direto e o processo](https://cwiki.apache.org/confluence/display/Hive/LLAP)) é um Azure HDInsight [tipo de cluster](../hdinsight-hadoop-provision-linux-clusters.md#cluster-types). Consulta interativa suporta a colocação em cache na memória, que faz com que as consultas do Hive mais rápidas e interativas muito mais. 

Um cluster de consulta interativo é diferente de um cluster de Hadoop. Contém apenas o serviço de ramo de registo. 

> [!NOTE]
> Pode aceder ao serviço de ramo de registo do cluster interativa consulta apenas através de vista do Hive do Ambari, Beeline e o controlador do Microsoft Hive Open Database Connectivity (ODBC do Hive). Não é possível aceder-lhe através da consola do Hive, Templeton, a ferramenta da linha de comandos do Azure (CLI do Azure) ou do Azure PowerShell. 
> 
> 

## <a name="create-an-interactive-query-cluster"></a>Criar um cluster de consulta interativa
Para obter informações sobre como criar um cluster do HDInsight, consulte [clusters do Hadoop criar no HDInsight](../hdinsight-hadoop-provision-linux-clusters.md). Escolha o tipo de cluster de consulta interativo.

## <a name="execute-hive-queries-from-interactive-query"></a>Executar consultas do Hive a partir de consulta interativa
Para executar consultas do Hive, tem as seguintes opções:

* Utilizar o Power BI

    Consulte [visualizar macrodados com o Power BI no Azure HDInsight](../hadoop/apache-hadoop-connect-hive-power-bi.md).

* Utilizar Zeppelin

    Consulte [Zeppelin de utilização para executar consultas do Hive no Azure HDInsight ](../hdinsight-connect-hive-zeppelin.md).

* Utilizar o Visual Studio

    Consulte [ligar ao Azure HDInsight e executadas consultas do Hive, utilizando ferramentas do Data Lake para Visual Studio](../hadoop/apache-hadoop-visual-studio-tools-get-started.md#run-a-hive-query).

* Utilizar o Visual Studio Code

    Consulte [utilize Visual Studio Code para o ramo de registo, LLAP ou pySpark](../hdinsight-for-vscode.md).
* Execute o ramo de registo utilizando Ambari Hive vista.
  
    Consulte [utilizar vista do Hive com o Hadoop no HDInsight do Azure](../hadoop/apache-hadoop-use-hive-ambari-view.md).
* Execute o ramo de registo utilizando Beeline.
  
    Consulte [utilizar o Hive com o Hadoop no HDInsight com Beeline](../hadoop/apache-hadoop-use-hive-beeline.md).
  
    Pode utilizar Beeline a partir do nó principal ou de um nó de extremidade vazio. Recomendamos que utilize Beeline a partir de um nó de extremidade vazio. Para obter informações sobre como criar um cluster do HDInsight através de um nó de extremidade vazio, consulte [utilizar nós edge vazio no HDInsight](../hdinsight-apps-use-edge-node.md).
* Execute o ramo de registo através de ODBC do Hive.
  
    Consulte [ligar o Excel ao Hadoop com o controlador ODBC do Hive do Microsoft](../hadoop/apache-hadoop-connect-excel-hive-odbc-driver.md).

Para localizar a cadeia de ligação de conectividade de base de dados de Java (JDBC):

1. Inicie sessão Ambari utilizando o seguinte URL: https://\<nome do cluster\>. AzureHDInsight.net.
2. No menu à esquerda, selecione **Hive**.
3. Para copiar o URL, selecione o ícone da área de transferência:
   
   ![HDInsight Hadoop consulta interativa LLAP JDBC](./media/apache-interactive-query-get-started/hdinsight-hadoop-use-interactive-hive-jdbc.png)

## <a name="next-steps"></a>Passos seguintes

* Saiba como [criar clusters de consulta interativo no HDInsight](../hdinsight-hadoop-provision-linux-clusters.md).
* Saiba como [visualizar macrodados com o Power BI no Azure HDInsight](../hadoop/apache-hadoop-connect-hive-power-bi.md).
* Saiba como [utilizar Zeppelin para executar consultas do Hive no Azure HDInsight ](../hdinsight-connect-hive-zeppelin.md).
* Saiba como [executar consultas do Hive, utilizando ferramentas do Data Lake para Visual Studio](../hadoop/apache-hadoop-visual-studio-tools-get-started.md#run-a-hive-query).
* Saiba como [utilizar as ferramentas do HDInsight para Visual Studio Code](../hdinsight-for-vscode.md).
* Saiba como [utilizar vista do Hive com o Hadoop no HDInsight](../hadoop/apache-hadoop-use-hive-ambari-view.md)
* Saiba como [utilizar Beeline para submeter consultas do Hive no HDInsight](../hadoop/apache-hadoop-use-hive-beeline.md).
* Saiba como [ligar o Excel ao Hadoop com o controlador ODBC do Hive do Microsoft](../hadoop/apache-hadoop-connect-excel-hive-odbc-driver.md).

