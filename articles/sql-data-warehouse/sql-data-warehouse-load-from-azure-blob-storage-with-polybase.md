---
title: "A carga do Azure blob para o armazém de dados do Azure | Microsoft Docs"
description: "Saiba como utilizar o PolyBase para carregar dados do blob storage do Azure para o SQL Data Warehouse. Carregar tabelas alguns dos dados públicos para o esquema do armazém de dados de revenda Contoso."
services: sql-data-warehouse
documentationcenter: NA
author: barbkess
manager: barbkess
editor: 
ms.assetid: faca0fe7-62e7-4e1f-a86f-032b4ffcb06e
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: loading
ms.date: 10/31/2016
ms.author: barbkess
ms.openlocfilehash: 4221bcd5a50fad680427a500e32837c1e75dd990
ms.sourcegitcommit: f67f0bda9a7bb0b67e9706c0eb78c71ed745ed1d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/20/2017
---
# <a name="load-data-from-azure-blob-storage-into-sql-data-warehouse-polybase"></a>Carregar dados do blob storage do Azure para o SQL Data Warehouse (PolyBase)
> [!div class="op_single_selector"]
> * [Data Factory](sql-data-warehouse-load-from-azure-blob-storage-with-data-factory.md)
> * [PolyBase](sql-data-warehouse-load-from-azure-blob-storage-with-polybase.md)
> 
> 

Utilize comandos PolyBase e T-SQL para carregar dados do blob storage do Azure para o Azure SQL Data Warehouse. 

Para manter simples, este tutorial carrega duas tabelas de um Blob de armazenamento do Azure público para o esquema do armazém de dados de revenda Contoso. Para carregar o conjunto completo de dados, execute o exemplo [carregar o armazém de dados de revenda completa do Contoso] [ Load the full Contoso Retail Data Warehouse] do repositório amostras do Microsoft SQL Server.

Neste tutorial, irá:

1. Configurar o PolyBase para carregar a partir do blob storage do Azure
2. Carregar dados públicos para a base de dados
3. Efetue otimizações depois de concluída a carga.

## <a name="before-you-begin"></a>Antes de começar
Para executar este tutorial, precisa de uma conta do Azure que já tenha uma base de dados do armazém de dados do SQL Server. Se ainda não tiver isto, consulte o artigo [criar um SQL Data Warehouse][Create a SQL Data Warehouse].

## <a name="1-configure-the-data-source"></a>1. Configurar a origem de dados
O PolyBase utiliza objetos externos de T-SQL para definir os atributos dos dados externos e localização. As definições de objeto externo são armazenadas no SQL Data Warehouse. Os dados são armazenados externamente.

### <a name="11-create-a-credential"></a>1.1. Criar uma credencial
**Ignore este passo** se estão a carregar os dados de públicos da Contoso. Não precisa de acesso seguro a dados públicos, uma vez que já se encontra acessível a qualquer pessoa.

**Não ignore este passo** se estiver a utilizar neste tutorial como um modelo para carregar os seus próprios dados. Para aceder aos dados através de uma credencial, utilize o seguinte script para criar uma credencial com âmbito de base de dados e, em seguida, utilizá-lo quando definir a localização da origem de dados.

```sql
-- A: Create a master key.
-- Only necessary if one does not already exist.
-- Required to encrypt the credential secret in the next step.

CREATE MASTER KEY;


-- B: Create a database scoped credential
-- IDENTITY: Provide any string, it is not used for authentication to Azure storage.
-- SECRET: Provide your Azure storage account key.


CREATE DATABASE SCOPED CREDENTIAL AzureStorageCredential
WITH
    IDENTITY = 'user',
    SECRET = '<azure_storage_account_key>'
;


-- C: Create an external data source
-- TYPE: HADOOP - PolyBase uses Hadoop APIs to access data in Azure blob storage.
-- LOCATION: Provide Azure storage account name and blob container name.
-- CREDENTIAL: Provide the credential created in the previous step.

CREATE EXTERNAL DATA SOURCE AzureStorage
WITH (
    TYPE = HADOOP,
    LOCATION = 'wasbs://<blob_container_name>@<azure_storage_account_name>.blob.core.windows.net',
    CREDENTIAL = AzureStorageCredential
);
```

Avance para o passo 2.

### <a name="12-create-the-external-data-source"></a>1.2. Criar a origem de dados externas
Utilize esta opção [criar origem de dados externa] [ CREATE EXTERNAL DATA SOURCE] comando para armazenar a localização dos dados e o tipo de dados. 

```sql
CREATE EXTERNAL DATA SOURCE AzureStorage_west_public
WITH 
(  
    TYPE = Hadoop 
,   LOCATION = 'wasbs://contosoretaildw-tables@contosoretaildw.blob.core.windows.net/'
); 
```

> [!IMPORTANT]
> Se optar por efetuar o blob do azure contentores de armazenamento públicas, lembre-se de que como o proprietário dos dados será cobrada para dados de encargos associados à saída quando dados deixa o Centro de dados. 
> 
> 

## <a name="2-configure-data-format"></a>2. Configurar o formato de dados
Os dados são armazenados nos ficheiros de texto no armazenamento de Blobs do Azure e cada campo é separado com um delimitador. Execute este [criar formato de ficheiro externo] [ CREATE EXTERNAL FILE FORMAT] comandos para especificar o formato dos dados nos ficheiros de texto. Os dados da Contoso são descomprimidos e delimitada de pipe.

```sql
CREATE EXTERNAL FILE FORMAT TextFileFormat 
WITH 
(   FORMAT_TYPE = DELIMITEDTEXT
,    FORMAT_OPTIONS    (   FIELD_TERMINATOR = '|'
                    ,    STRING_DELIMITER = ''
                    ,    DATE_FORMAT         = 'yyyy-MM-dd HH:mm:ss.fff'
                    ,    USE_TYPE_DEFAULT = FALSE 
                    )
);
``` 

## <a name="3-create-the-external-tables"></a>3. Criar as tabelas externas
Agora que tiver especificado o formato de ficheiro e de origem de dados, está pronto para criar as tabelas externas. 

### <a name="31-create-a-schema-for-the-data"></a>3.1. Crie um esquema para os dados.
Para criar um local para armazenar os dados da Contoso na base de dados, crie um esquema.

```sql
CREATE SCHEMA [asb]
GO
```

### <a name="32-create-the-external-tables"></a>3.2. Crie as tabelas externas.
Execute este script para criar as tabelas externas DimProduct e FactOnlineSales. Todos os que estão a fazer aqui é que define os nomes das colunas e tipos de dados e de enlace-los para a localização e o formato dos ficheiros de armazenamento de Blobs do Azure. A definição é armazenada no armazém de dados do SQL Server e os dados ainda estão no Blob de armazenamento do Azure.

O **localização** parâmetro é a pasta na pasta raiz no Blob Storage do Azure. Cada tabela é numa pasta diferente.

```sql

--DimProduct
CREATE EXTERNAL TABLE [asb].DimProduct (
    [ProductKey] [int] NOT NULL,
    [ProductLabel] [nvarchar](255) NULL,
    [ProductName] [nvarchar](500) NULL,
    [ProductDescription] [nvarchar](400) NULL,
    [ProductSubcategoryKey] [int] NULL,
    [Manufacturer] [nvarchar](50) NULL,
    [BrandName] [nvarchar](50) NULL,
    [ClassID] [nvarchar](10) NULL,
    [ClassName] [nvarchar](20) NULL,
    [StyleID] [nvarchar](10) NULL,
    [StyleName] [nvarchar](20) NULL,
    [ColorID] [nvarchar](10) NULL,
    [ColorName] [nvarchar](20) NOT NULL,
    [Size] [nvarchar](50) NULL,
    [SizeRange] [nvarchar](50) NULL,
    [SizeUnitMeasureID] [nvarchar](20) NULL,
    [Weight] [float] NULL,
    [WeightUnitMeasureID] [nvarchar](20) NULL,
    [UnitOfMeasureID] [nvarchar](10) NULL,
    [UnitOfMeasureName] [nvarchar](40) NULL,
    [StockTypeID] [nvarchar](10) NULL,
    [StockTypeName] [nvarchar](40) NULL,
    [UnitCost] [money] NULL,
    [UnitPrice] [money] NULL,
    [AvailableForSaleDate] [datetime] NULL,
    [StopSaleDate] [datetime] NULL,
    [Status] [nvarchar](7) NULL,
    [ImageURL] [nvarchar](150) NULL,
    [ProductURL] [nvarchar](150) NULL,
    [ETLLoadID] [int] NULL,
    [LoadDate] [datetime] NULL,
    [UpdateDate] [datetime] NULL
)
WITH
(
    LOCATION='/DimProduct/' 
,   DATA_SOURCE = AzureStorage_west_public
,   FILE_FORMAT = TextFileFormat
,   REJECT_TYPE = VALUE
,   REJECT_VALUE = 0
)
;

--FactOnlineSales
CREATE EXTERNAL TABLE [asb].FactOnlineSales 
(
    [OnlineSalesKey] [int]  NOT NULL,
    [DateKey] [datetime] NOT NULL,
    [StoreKey] [int] NOT NULL,
    [ProductKey] [int] NOT NULL,
    [PromotionKey] [int] NOT NULL,
    [CurrencyKey] [int] NOT NULL,
    [CustomerKey] [int] NOT NULL,
    [SalesOrderNumber] [nvarchar](20) NOT NULL,
    [SalesOrderLineNumber] [int] NULL,
    [SalesQuantity] [int] NOT NULL,
    [SalesAmount] [money] NOT NULL,
    [ReturnQuantity] [int] NOT NULL,
    [ReturnAmount] [money] NULL,
    [DiscountQuantity] [int] NULL,
    [DiscountAmount] [money] NULL,
    [TotalCost] [money] NOT NULL,
    [UnitCost] [money] NULL,
    [UnitPrice] [money] NULL,
    [ETLLoadID] [int] NULL,
    [LoadDate] [datetime] NULL,
    [UpdateDate] [datetime] NULL
)
WITH
(
    LOCATION='/FactOnlineSales/' 
,   DATA_SOURCE = AzureStorage_west_public
,   FILE_FORMAT = TextFileFormat
,   REJECT_TYPE = VALUE
,   REJECT_VALUE = 0
)
;
```

## <a name="4-load-the-data"></a>4. Carregar os dados
Há várias formas de aceder aos dados externos.  Pode consultar dados diretamente a partir da tabela externa, carregar os dados para tabelas de base de dados novas ou adicionar dados externos a tabelas de base de dados existente.  

### <a name="41-create-a-new-schema"></a>4.1. Criar um novo esquema
CTAS cria uma nova tabela que contém dados.  Em primeiro lugar, crie um esquema para os dados da contoso.

```sql
CREATE SCHEMA [cso]
GO
```

### <a name="42-load-the-data-into-new-tables"></a>4.2. Carregar os dados para novas tabelas
Para carregar dados do blob storage do Azure e guardá-lo numa tabela no interior da base de dados, utilize o [CREATE TABLE AS SELECT (Transact-SQL)] [ CREATE TABLE AS SELECT (Transact-SQL)] instrução. A carregar com CTAS aproveita as tabelas externas com tipo seguro que acabou de criar. Para carregar os dados para novas tabelas, utilize um [CTAS] [ CTAS] instrução por tabela. 
 
CTAS cria uma nova tabela e preenche-lo com os resultados de uma instrução select. CTAS define a tabela de novo para ter a mesma colunas e tipos de dados como os resultados da instrução select. Se selecionar todas as colunas a partir de uma tabela externa, a nova tabela será uma réplica das colunas e tipos de dados na tabela externa.

Neste exemplo, vamos criar a dimensão e a tabela de factos distribuídas tabelas de hash. 

```sql
SELECT GETDATE();
GO

CREATE TABLE [cso].[DimProduct]            WITH (DISTRIBUTION = HASH([ProductKey]  ) ) AS SELECT * FROM [asb].[DimProduct]             OPTION (LABEL = 'CTAS : Load [cso].[DimProduct]             ');
CREATE TABLE [cso].[FactOnlineSales]       WITH (DISTRIBUTION = HASH([ProductKey]  ) ) AS SELECT * FROM [asb].[FactOnlineSales]        OPTION (LABEL = 'CTAS : Load [cso].[FactOnlineSales]        ');
```

### <a name="43-track-the-load-progress"></a>4.3 acompanhar o progresso de carga
Pode controlar o progresso da sua carga utilizar vistas de gestão dinâmica (DMVs). 

```sql
-- To see all requests
SELECT * FROM sys.dm_pdw_exec_requests;

-- To see a particular request identified by its label
SELECT * FROM sys.dm_pdw_exec_requests as r
WHERE r.[label] = 'CTAS : Load [cso].[DimProduct]             '
      OR r.[label] = 'CTAS : Load [cso].[FactOnlineSales]        '
;

-- To track bytes and files
SELECT
    r.command,
    s.request_id,
    r.status,
    count(distinct input_name) as nbr_files, 
    sum(s.bytes_processed)/1024/1024/1024 as gb_processed
FROM
    sys.dm_pdw_exec_requests r
    inner join sys.dm_pdw_dms_external_work s
        on r.request_id = s.request_id
WHERE 
    r.[label] = 'CTAS : Load [cso].[DimProduct]             '
    OR r.[label] = 'CTAS : Load [cso].[FactOnlineSales]        '
GROUP BY
    r.command,
    s.request_id,
    r.status
ORDER BY
    nbr_files desc,
    gb_processed desc;
```

## <a name="5-optimize-columnstore-compression"></a>5. Otimizar a compressão columnstore
Por predefinição, o SQL Data Warehouse armazena a tabela como um índice columnstore em cluster. Após a conclusão de uma carga, algumas das linhas de dados não podem ser comprimidas para o columnstore.  Há diversos motivos por que motivo esta situação pode ocorrer. Para obter mais informações, consulte [Gerir índices columnstore][manage columnstore indexes].

Para otimizar o desempenho das consultas e compressão columnstore depois de uma carga, reconstrua a tabela para forçar o índice columnstore para comprimir todas as linhas. 

```sql
SELECT GETDATE();
GO

ALTER INDEX ALL ON [cso].[DimProduct]               REBUILD;
ALTER INDEX ALL ON [cso].[FactOnlineSales]          REBUILD;
```

Para obter mais informações sobre a manutenção dos índices columnstore, consulte o [Gerir índices columnstore] [ manage columnstore indexes] artigo.

## <a name="6-optimize-statistics"></a>6. Otimizar as estatísticas
É melhor criar estatísticas de coluna única imediatamente após uma carga. Existem algumas opções de estatísticas. Por exemplo, se criar estatísticas de coluna única em cada coluna poderá demorar muito tempo a recriar todas as estatísticas. Se souber que determinadas colunas não vai ser em predicados de consulta, pode ignorar criar estatísticas nessas colunas.

Se optar por criar estatísticas de coluna única em cada coluna de cada tabela, pode utilizar o exemplo de código do procedimento armazenado `prc_sqldw_create_stats` no [estatísticas] [ statistics] artigo.

O exemplo seguinte é um ponto de partida para criar estatísticas. Cria estatísticas de coluna única cada coluna na tabela de dimensão e cada coluna joining nas tabelas de factos. Pode sempre adicionar estatísticas únicas ou várias colunas para outras colunas da tabela de factos mais tarde.

```sql
CREATE STATISTICS [stat_cso_DimProduct_AvailableForSaleDate] ON [cso].[DimProduct]([AvailableForSaleDate]);
CREATE STATISTICS [stat_cso_DimProduct_BrandName] ON [cso].[DimProduct]([BrandName]);
CREATE STATISTICS [stat_cso_DimProduct_ClassID] ON [cso].[DimProduct]([ClassID]);
CREATE STATISTICS [stat_cso_DimProduct_ClassName] ON [cso].[DimProduct]([ClassName]);
CREATE STATISTICS [stat_cso_DimProduct_ColorID] ON [cso].[DimProduct]([ColorID]);
CREATE STATISTICS [stat_cso_DimProduct_ColorName] ON [cso].[DimProduct]([ColorName]);
CREATE STATISTICS [stat_cso_DimProduct_ETLLoadID] ON [cso].[DimProduct]([ETLLoadID]);
CREATE STATISTICS [stat_cso_DimProduct_ImageURL] ON [cso].[DimProduct]([ImageURL]);
CREATE STATISTICS [stat_cso_DimProduct_LoadDate] ON [cso].[DimProduct]([LoadDate]);
CREATE STATISTICS [stat_cso_DimProduct_Manufacturer] ON [cso].[DimProduct]([Manufacturer]);
CREATE STATISTICS [stat_cso_DimProduct_ProductDescription] ON [cso].[DimProduct]([ProductDescription]);
CREATE STATISTICS [stat_cso_DimProduct_ProductKey] ON [cso].[DimProduct]([ProductKey]);
CREATE STATISTICS [stat_cso_DimProduct_ProductLabel] ON [cso].[DimProduct]([ProductLabel]);
CREATE STATISTICS [stat_cso_DimProduct_ProductName] ON [cso].[DimProduct]([ProductName]);
CREATE STATISTICS [stat_cso_DimProduct_ProductSubcategoryKey] ON [cso].[DimProduct]([ProductSubcategoryKey]);
CREATE STATISTICS [stat_cso_DimProduct_ProductURL] ON [cso].[DimProduct]([ProductURL]);
CREATE STATISTICS [stat_cso_DimProduct_Size] ON [cso].[DimProduct]([Size]);
CREATE STATISTICS [stat_cso_DimProduct_SizeRange] ON [cso].[DimProduct]([SizeRange]);
CREATE STATISTICS [stat_cso_DimProduct_SizeUnitMeasureID] ON [cso].[DimProduct]([SizeUnitMeasureID]);
CREATE STATISTICS [stat_cso_DimProduct_Status] ON [cso].[DimProduct]([Status]);
CREATE STATISTICS [stat_cso_DimProduct_StockTypeID] ON [cso].[DimProduct]([StockTypeID]);
CREATE STATISTICS [stat_cso_DimProduct_StockTypeName] ON [cso].[DimProduct]([StockTypeName]);
CREATE STATISTICS [stat_cso_DimProduct_StopSaleDate] ON [cso].[DimProduct]([StopSaleDate]);
CREATE STATISTICS [stat_cso_DimProduct_StyleID] ON [cso].[DimProduct]([StyleID]);
CREATE STATISTICS [stat_cso_DimProduct_StyleName] ON [cso].[DimProduct]([StyleName]);
CREATE STATISTICS [stat_cso_DimProduct_UnitCost] ON [cso].[DimProduct]([UnitCost]);
CREATE STATISTICS [stat_cso_DimProduct_UnitOfMeasureID] ON [cso].[DimProduct]([UnitOfMeasureID]);
CREATE STATISTICS [stat_cso_DimProduct_UnitOfMeasureName] ON [cso].[DimProduct]([UnitOfMeasureName]);
CREATE STATISTICS [stat_cso_DimProduct_UnitPrice] ON [cso].[DimProduct]([UnitPrice]);
CREATE STATISTICS [stat_cso_DimProduct_UpdateDate] ON [cso].[DimProduct]([UpdateDate]);
CREATE STATISTICS [stat_cso_DimProduct_Weight] ON [cso].[DimProduct]([Weight]);
CREATE STATISTICS [stat_cso_DimProduct_WeightUnitMeasureID] ON [cso].[DimProduct]([WeightUnitMeasureID]);
CREATE STATISTICS [stat_cso_FactOnlineSales_CurrencyKey] ON [cso].[FactOnlineSales]([CurrencyKey]);
CREATE STATISTICS [stat_cso_FactOnlineSales_CustomerKey] ON [cso].[FactOnlineSales]([CustomerKey]);
CREATE STATISTICS [stat_cso_FactOnlineSales_DateKey] ON [cso].[FactOnlineSales]([DateKey]);
CREATE STATISTICS [stat_cso_FactOnlineSales_OnlineSalesKey] ON [cso].[FactOnlineSales]([OnlineSalesKey]);
CREATE STATISTICS [stat_cso_FactOnlineSales_ProductKey] ON [cso].[FactOnlineSales]([ProductKey]);
CREATE STATISTICS [stat_cso_FactOnlineSales_PromotionKey] ON [cso].[FactOnlineSales]([PromotionKey]);
CREATE STATISTICS [stat_cso_FactOnlineSales_StoreKey] ON [cso].[FactOnlineSales]([StoreKey]);
```

## <a name="achievement-unlocked"></a>Achievement desbloqueado!
Carregou com êxito dados públicos no Azure SQL Data Warehouse. Excelente trabalho!

Agora pode começar a consultar tabelas utilizando consultas como o seguinte:

```sql
SELECT  SUM(f.[SalesAmount]) AS [sales_by_brand_amount]
,       p.[BrandName]
FROM    [cso].[FactOnlineSales] AS f
JOIN    [cso].[DimProduct]      AS p ON f.[ProductKey] = p.[ProductKey]
GROUP BY p.[BrandName]
```

## <a name="next-steps"></a>Passos seguintes
Para carregar os dados de armazém de dados de revenda Contoso completos, utilize o script em para mais sugestões de desenvolvimento, consulte [descrição geral do desenvolvimento SQL Data Warehouse][SQL Data Warehouse development overview].

<!--Image references-->

<!--Article references-->
[Create a SQL Data Warehouse]: sql-data-warehouse-get-started-provision.md
[Load data into SQL Data Warehouse]: sql-data-warehouse-overview-load.md
[SQL Data Warehouse development overview]: sql-data-warehouse-overview-develop.md
[manage columnstore indexes]: sql-data-warehouse-tables-index.md
[Statistics]: sql-data-warehouse-tables-statistics.md
[CTAS]: sql-data-warehouse-develop-ctas.md
[label]: sql-data-warehouse-develop-label.md

<!--MSDN references-->
[CREATE EXTERNAL DATA SOURCE]: https://msdn.microsoft.com/en-us/library/dn935022.aspx
[CREATE EXTERNAL FILE FORMAT]: https://msdn.microsoft.com/en-us/library/dn935026.aspx
[CREATE TABLE AS SELECT (Transact-SQL)]: https://msdn.microsoft.com/library/mt204041.aspx
[sys.dm_pdw_exec_requests]: https://msdn.microsoft.com/library/mt203887.aspx
[REBUILD]: https://msdn.microsoft.com/library/ms188388.aspx

<!--Other Web references-->
[Microsoft Download Center]: http://www.microsoft.com/download/details.aspx?id=36433
[Load the full Contoso Retail Data Warehouse]: https://github.com/Microsoft/sql-server-samples/tree/master/samples/databases/contoso-data-warehouse/readme.md
