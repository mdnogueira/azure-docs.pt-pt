---
title: "Arquivo Data Lake a integração com outros serviços do Azure | Microsoft Docs"
description: "Compreender a forma como a Data Lake Store integra-se com outros serviços do Azure"
documentationcenter: 
services: data-lake-store
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: 48a5d1f4-3850-4c22-bbc4-6d1d394fba8a
ms.service: data-lake-store
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 11/28/2017
ms.author: nitinme
ms.openlocfilehash: d43459b900232612d83506438e6a70daa893eb80
ms.sourcegitcommit: 651a6fa44431814a42407ef0df49ca0159db5b02
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/28/2017
---
# <a name="integrating-data-lake-store-with-other-azure-services"></a>Integrating Data Lake Store with other Azure Services (Integrar o Data Lake Store noutros Serviços do Azure)
O Azure Data Lake Store pode ser utilizado em conjunto com outros serviços do Azure para ativar uma vasta gama de cenários. O artigo seguinte lista os serviços de Data Lake Store pode ser integrado.

## <a name="use-data-lake-store-with-azure-hdinsight"></a>Utilizar o Data Lake Store com o Azure HDInsight
Pode aprovisionar um [Azure HDInsight](https://azure.microsoft.com/documentation/learning-paths/hdinsight-self-guided-hadoop-training/) cluster que utiliza o Data Lake Store como o armazenamento compatível com HDFS. Nesta versão, para clusters do Hadoop e Storm no Windows e Linux, pode utilizar Data Lake Store apenas como um armazenamento adicional. Esses clusters continuar a utilizar armazenamento do Azure (WASB) como armazenamento de predefinido. No entanto, para os clusters de HBase no Windows e Linux, pode utilizar Data Lake Store que o armazenamento de predefinido, armazenamento adicional ou ambos.

Para obter instruções sobre como aprovisionar um cluster do HDInsight com o Data Lake Store, consulte:

* [Aprovisionar um cluster do HDInsight com o Data Lake Store utilizando o Portal do Azure](data-lake-store-hdinsight-hadoop-use-portal.md)
* [Aprovisionar um cluster do HDInsight com o Data Lake Store, como armazenamento de predefinido com o Azure PowerShell](data-lake-store-hdinsight-hadoop-use-powershell-for-default-storage.md)
* [Aprovisionar um cluster do HDInsight com o Data Lake Store, como armazenamento adicional com o Azure PowerShell](data-lake-store-hdinsight-hadoop-use-powershell.md)

## <a name="use-data-lake-store-with-azure-data-lake-analytics"></a>Utilizar o Data Lake Store do Azure Data Lake Analytics
[Azure Data Lake Analytics](../data-lake-analytics/data-lake-analytics-overview.md) permite-lhe trabalhar com macrodados na escala da nuvem. Dinamicamente Aprovisiona recursos e permite-lhe fazer análises sobre terabytes ou até mesmo exabytes de dados que podem ser armazenados num número de origens de dados suportadas, um dos atributos que está a ser Data Lake Store. O Data Lake Analytics está otimizado especialmente para funcionar com o Azure Data Lake Store, fornecendo o nível mais elevado de desempenho, débito e paralelização para as suas cargas de trabalho de macrodados.

Para obter instruções sobre como utilizar o Data Lake Analytics com o Data Lake Store, consulte [introdução ao Data Lake Analytics com o Data Lake Store](../data-lake-analytics/data-lake-analytics-get-started-portal.md).

## <a name="use-data-lake-store-with-azure-data-factory"></a>Utilizar o Data Lake Store com o Azure Data Factory
Pode utilizar [do Azure Data Factory](https://azure.microsoft.com/services/data-factory/) para a ingestão de dados a partir de tabelas do Azure, SQL Database do Azure, o armazém de dados do Azure SQL, Blobs Storage do Azure e bases de dados no local. Ser um citizen primeira classe do ecossistema do Azure do, do Azure Data Factory podem ser utilizado para orquestrar a ingestão de dados a partir destes origem para o Azure Data Lake Store.

Para obter instruções sobre como utilizar o Azure Data Factory com o Data Lake Store, consulte [mover dados para e do Data Lake Store utilizando o Data Factory](../data-factory/connector-azure-data-lake-store.md).

## <a name="copy-data-from-azure-storage-blobs-into-data-lake-store"></a>Copiar dados de Blobs de armazenamento do Azure para o Data Lake Store
O Azure Data Lake Store fornece uma ferramenta da linha de comandos, AdlCopy, que permite copiar dados do Blob Storage do Azure para uma conta do Data Lake Store. Para obter mais informações, consulte [copiar dados de Blobs de armazenamento do Azure para o Data Lake Store](data-lake-store-copy-data-azure-storage-blob.md).

## <a name="copy-data-between-azure-sql-database-and-data-lake-store"></a>Copiar dados entre SQL Database do Azure e a Data Lake Store
Pode utilizar o Apache Sqoop para importar e exportar dados entre SQL Database do Azure e o Data Lake Store. Para obter mais informações, consulte [copiar dados entre o Data Lake Store e a SQL database do Azure utilizando o Sqoop](data-lake-store-data-transfer-sql-sqoop.md).

## <a name="use-data-lake-store-with-stream-analytics"></a>Utilizar o Data Lake Store Stream Analytics
Pode utilizar o Data Lake Store como um das saídas para armazenar dados de transmissão em fluxo utilizando o Azure Stream Analytics. Para obter mais informações, consulte [transmitir dados de Blob de armazenamento do Azure para o Data Lake Store utilizando o Azure Stream Analytics](data-lake-store-stream-analytics.md).

## <a name="use-data-lake-store-with-power-bi"></a>Utilizar o Data Lake Store com o Power BI
Pode utilizar o Power BI para importar dados a partir de uma conta de Data Lake Store para analisar e visualizar os dados. Para obter mais informações, consulte [analisar os dados no Data Lake Store através do Power BI](data-lake-store-power-bi.md).

## <a name="use-data-lake-store-with-data-catalog"></a>Utilizar o Data Lake Store com o catálogo de dados
Pode registar os dados do Data Lake Store para o catálogo de dados do Azure para tornar os dados detetável em toda a organização. Para obter mais informações consulte [registar dados do Data Lake Store no catálogo de dados do Azure](data-lake-store-with-data-catalog.md).

## <a name="use-data-lake-store-with-sql-server-integration-services-ssis"></a>Utilizar o Data Lake Store com o SQL Server Integration Services (SSIS)
Pode utilizar o Gestor de ligações do Azure Data Lake Store no SSIS para ligar um pacote SSIS com o Azure Data Lake Store. Para obter mais informações, consulte [utilize Data Lake Store com SSIS](https://docs.microsoft.com/sql/integration-services/connection-manager/azure-data-lake-store-connection-manager).

## <a name="use-data-lake-store-with-sql-data-warehouse"></a>Utilizar o Data Lake Store com o SQL Data Warehouse
Pode utilizar o PolyBase para carregar dados do Azure Data Lake Store para o SQL Data Warehouse. Para obter mais informações consulte [utilize Data Lake Store com o SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-load-from-azure-data-lake-store.md).

## <a name="use-data-lake-store-with-azure-event-hubs"></a>Utilizar o Data Lake Store com o Event Hubs do Azure
Pode utilizar o Azure Data Lake Store para arquivo e a captura de dados recebidos pelo Event Hubs do Azure. Para obter mais informações consulte [utilize Data Lake Store com o Event Hubs do Azure](data-lake-store-archive-eventhub-capture.md).

## <a name="see-also"></a>Consultar também
* [Descrição geral do Azure Data Lake Store](data-lake-store-overview.md)
* [Introdução ao Data Lake Store através do Portal](data-lake-store-get-started-portal.md)
* [Introdução ao Data Lake Store através do PowerShell](data-lake-store-get-started-powershell.md)  

