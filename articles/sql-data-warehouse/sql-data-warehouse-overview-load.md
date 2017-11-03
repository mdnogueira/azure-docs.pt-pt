---
title: Carregar dados para o Azure SQL Data Warehouse | Microsoft Docs
description: "Saiba os cenários comuns para dados de carregamento para o SQL Data Warehouse. Estes incluem utilizando o PolyBase, armazenamento de Blobs do Azure, ficheiros simples e o envio de disco. Também pode utilizar ferramentas de terceiros."
services: sql-data-warehouse
documentationcenter: NA
author: ckarst
manager: jhubbard
editor: 
ms.assetid: 2253bf46-cf72-4de7-85ce-f267494d55fa
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: loading
ms.date: 10/31/2016
ms.author: cakarst;barbkess
ms.openlocfilehash: c4199a387f5cdbd477a5e348e48ba8e8b5900075
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="load-data-into-azure-sql-data-warehouse"></a>Carregar dados para o Azure SQL Data Warehouse
Um resumo das opções de cenário e recomendações para carregar dados para o SQL Data Warehouse.

A parte hardest do carregamento de dados, normalmente, está a preparar os dados para a carga. Azure simplifica a carregar a utilizar o armazenamento de Blobs do Azure como um arquivo de dados comum para muitos dos serviços e utilizando o Azure Data Factory para orquestrar o movimento de dados e de comunicação entre os serviços do Azure. Estes processos são integrados com o PolyBase tecnologia que utiliza o processamento paralelo em grande escala (MPP) para carregar dados em paralelo do blob storage do Azure para o SQL Data Warehouse. 

Para tutoriais que carregar bases de dados de exemplo, consulte [carregar bases de dados de exemplo][Load sample databases].

## <a name="load-from-azure-blob-storage"></a>Carregar a partir do blob storage do Azure
A forma mais rápida para importar dados para o armazém de dados do SQL Server está a utilizar o PolyBase para carregar dados do blob storage do Azure. O PolyBase utiliza o design de processamento paralelo em grande escala (MPP) do SQL Data Warehouse para carregar dados em paralelo do blob storage do Azure. Para utilizar o PolyBase, pode utilizar comandos de T-SQL ou um pipeline do Azure Data Factory.

### <a name="1-use-polybase-and-t-sql"></a>1. Utilize o PolyBase e T-SQL
Resumo do processo de carregamento:

1. Mover os dados para armazenamento de Blobs do Azure ou do Azure Data Lake Store e armazene-o nos ficheiros de texto.
2. Configurar objetos externos no armazém de dados do SQL Server para definir a localização e o formato dos dados
3. Execute um comando T-SQL para carregar os dados em paralelo para uma nova tabela de base de dados.

<!-- 5. Schedule and run a loading job. --> 

Para um tutorial, consulte [carregar dados do blob storage do Azure ao SQL Data Warehouse (PolyBase)][Load data from Azure blob storage to SQL Data Warehouse (PolyBase)].

### <a name="2-use-azure-data-factory"></a>2. Utilize o Azure Data Factory
Uma forma mais simples utilizar o PolyBase, pode criar um pipeline do Azure Data Factory que utiliza o PolyBase para carregar dados do blob storage do Azure para o SQL Data Warehouse. Isto é rápido para configurar uma vez que não precisa de definir os objetos de T-SQL. Se precisar de consultar os dados externos sem importá-lo, utilize o T-SQL. 

Resumo do processo de carregamento:

1. Mover os dados para armazenamento de Blobs do Azure e armazene-o nos ficheiros de texto. O Azure Data Factory não suporta atualmente conectividade ADLS com o PolyBase).
2. Crie um pipeline do Azure Data Factory para a ingestão de dados. Utilize a opção de PolyBase.
4. Agendar e executar o pipeline.

Para um tutorial, consulte [carregar dados do blob storage do Azure ao SQL Data Warehouse (Azure Data Factory)][Load data from Azure blob storage to SQL Data Warehouse (Azure Data Factory)].

## <a name="load-from-sql-server"></a>Carregar do SQL Server
Para carregar dados do SQL Server para o SQL Data Warehouse pode utilizar o Integration Services (SSIS), ficheiros simples de transferência ou discos incorporadas para a Microsoft. Leitura para ver um resumo dos diferentes carregar ligações para tutoriais e processos.

Para planear uma migração de dados completa do SQL Server para o SQL Data Warehouse, consulte o [descrição geral da migração][Migration overview]. 

### <a name="use-integration-services-ssis"></a>Utilizar o Integration Services (SSIS)
Se já estiver a utilizar pacotes do Integration Services (SSIS) para carregar para o SQL Server, pode atualizar os pacotes para utilizar o SQL Server como a origem e o SQL Data Warehouse como destino. Este é um processo rápido e fácil de o fazer, e é uma boa opção se não estiver a tentar migrar o processo de carregamento para utilizar dados já na nuvem. O compromisso é que a carga será mais lenta do que utilizando o PolyBase porque este SSIS não efetuar o carregamento em paralelo.

Resumo do processo de carregamento:

1. Rever o seu pacote de serviços de integração para apontar para a instância do SQL Server para a origem e a base de dados do armazém de dados do SQL Server para o destino.
2. Migre o esquema para o SQL Data Warehouse, se ainda não estiver não existe.
3. Altere o mapeamento nos seus pacotes utilizar apenas os tipos de dados que são suportados pelo SQL Data Warehouse.
4. Agendar e executar o pacote.

Para um tutorial, consulte [carregar dados do SQL Server do armazém de dados de SQL do Azure (SSIS)][Load data from SQL Server to Azure SQL Data Warehouse (SSIS)].

### <a name="use-azcopy-recommended-for--10-tb-data"></a>Utilizar o AZCopy (recomendado para dados de < 10 TB)
Se o tamanho de dados for < 10 TB, pode exportar os dados do SQL Server para ficheiros simples, copie os ficheiros para armazenamento de Blobs do Azure e, em seguida, utilize o PolyBase para carregar os dados para o SQL Data Warehouse

Resumo do processo de carregamento:

1. Utilize o utilitário de linha de comandos do bcp para exportar dados do SQL Server para ficheiros simples.
2. Utilize o utilitário de linha de comandos do AZCopy para copiar dados de ficheiros simples para o armazenamento de Blobs do Azure.
3. Utilize o PolyBase para carregar para o SQL Data Warehouse.

Para um tutorial, consulte [carregar dados do blob storage do Azure ao SQL Data Warehouse (PolyBase)][Load data from Azure blob storage to SQL Data Warehouse (PolyBase)].

### <a name="use-bcp"></a>Utilizar o bcp
Se tiver uma pequena quantidade de dados pode utilizar o bcp para carregar diretamente para o Azure SQL Data Warehouse.

Resumo do processo de carregamento:

1. Utilize o utilitário de linha de comandos do bcp para exportar dados do SQL Server para ficheiros simples.
2. Utilize o bcp para carregar dados de ficheiros simples diretamente ao SQL Data Warehouse.

Para um tutorial, consulte [carregar dados do SQL Server para o Azure SQL Data Warehouse (bcp)][Load data from SQL Server to Azure SQL Data Warehouse (bcp)].

### <a name="use-importexport-recommended-for--10-tb-data"></a>Utilize a ferramenta para importar/exportar (recomendada para dados > 10 TB)
Se o tamanho dos dados é > 10 TB e pretende movê-la para o Azure, recomendamos que utilize o nosso serviço de envio de disco [importar/exportar][Import/Export]. 

Resumo do processo de carregamento

1. Utilize o utilitário de linha de comandos do bcp para exportar dados do SQL Server para ficheiros simples nos discos transferíveis.
2. São enviados os discos para a Microsoft.
3. Microsoft carrega os dados para o SQL Data Warehouse

## <a name="load-from-hdinsight"></a>Carregar a partir do HDInsight
Armazém de dados do SQL Server suporta o carregamento de dados do HDInsight através do PolyBase. O processo é o mesmo que o carregamento de dados do Blob Storage do Azure - utilizando o PolyBase para estabelecer ligação ao HDInsight ao carregar os dados. 

### <a name="1-use-polybase-and-t-sql"></a>1. Utilize o PolyBase e T-SQL
Resumo do processo de carregamento:

1. Mover os dados no HDInsight e armazená-las nos ficheiros de texto, formato ORC ou Parquet.
2. Configure objetos externos no armazém de dados do SQL Server para definir a localização e o formato dos dados.
3. Execute um comando T-SQL para carregar os dados em paralelo para uma nova tabela de base de dados.

Para um tutorial, consulte [carregar dados do blob storage do Azure ao SQL Data Warehouse (PolyBase)][Load data from Azure blob storage to SQL Data Warehouse (PolyBase)].

## <a name="recommendations"></a>Recomendações
Muitos dos nossos parceiros tem carregar soluções. Para obter mais informações, consulte uma lista dos nossos [parceiros de solução][solution partners]. 

Se os seus dados for proveniente de uma origem não relacionais e pretende carregá-lo para o SQL Data Warehouse, terá de transformá-lo em linhas e colunas antes que o carregamento. Os dados transformados não tem de ser armazenados numa base de dados, pode ser armazenado em ficheiros de texto.

Crie estatísticas nos dados recentemente carregados. O Azure SQL Data Warehouse ainda não suporta a criação ou atualização automática de estatísticas.  Para obter o melhor desempenho das consultas, é importante criar estatísticas em todas as colunas de todas as tabelas após o primeiro carregamento ou ocorrerem quaisquer alterações substanciais nos dados.  Para obter mais informações, consulte [estatísticas][Statistics].

## <a name="next-steps"></a>Passos seguintes
Para mais sugestões de desenvolvimento, consulte o [descrição geral do desenvolvimento][development overview].

<!--Image references-->

<!--Article references-->
[Load data from Azure blob storage to SQL Data Warehouse (PolyBase)]: ./sql-data-warehouse-load-from-azure-blob-storage-with-polybase.md
[Load data from Azure blob storage to SQL Data Warehouse (Azure Data Factory)]: ./sql-data-warehouse-load-from-azure-blob-storage-with-data-factory.md
[Load data from SQL Server to Azure SQL Data Warehouse (SSIS)]: ./sql-data-warehouse-load-from-sql-server-with-integration-services.md
[Load data from SQL Server to Azure SQL Data Warehouse (bcp)]: ./sql-data-warehouse-load-from-sql-server-with-bcp.md
[Load data from SQL Server to Azure SQL Data Warehouse (AZCopy)]: ./sql-data-warehouse-load-from-sql-server-with-azcopy.md

[Load sample databases]: ./sql-data-warehouse-load-sample-databases.md
[Migration overview]: ./sql-data-warehouse-overview-migrate.md
[solution partners]: ./sql-data-warehouse-partner-business-intelligence.md
[development overview]: ./sql-data-warehouse-overview-develop.md
[Statistics]: ./sql-data-warehouse-tables-statistics.md

<!--MSDN references-->

<!--Other Web references-->
[Import/Export]: https://azure.microsoft.com/documentation/articles/storage-import-export-service/
