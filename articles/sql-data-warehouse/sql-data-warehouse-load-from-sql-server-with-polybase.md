<properties
   pageTitle="Carregar dados do SQL Server para o Azure SQL Data Warehouse (PolyBase) | Microsoft Azure"
   description="Utiliza o bcp para exportar dados do SQL Server para ficheiros simples, o AZCopy para importar dados para o armazenamento de blobs do Azure e o PolyBase para incorporar os dados no Azure SQL Data Warehouse."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="lodipalm"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="06/30/2016"
   ms.author="lodipalm;barbkess;sonyama"/>


# Carregar os dados com o PolyBase para o SQL Data Warehouse

> [AZURE.SELECTOR]
- [SSIS](sql-data-warehouse-load-from-sql-server-with-integration-services.md)
- [PolyBase](sql-data-warehouse-load-from-sql-server-with-polybase.md)
- [bcp](sql-data-warehouse-load-from-sql-server-with-bcp.md)

Este tutorial mostra como carregar dados para o SQL Data Warehouse, utilizando o AzCopy e o PolyBase. Quando terminar, ficará a saber como:

- Utilizar o AzCopy para copiar dados para o armazenamento de blobs do Azure
- Criar objetos de base de dados para definir os dados
- Executar uma consulta de T-SQL para carregar os dados

>[AZURE.VIDEO loading-data-with-polybase-in-azure-sql-data-warehouse]

## Pré-requisitos

Para seguir este tutorial, é necessário

- Uma base de dados SQL Data Warehouse.
- Uma conta de armazenamento do Azure do tipo Armazenamento Localmente Redundante Standard (Standard-LRS), Armazenamento Georredundante Standard (Standard-GRS) ou Armazenamento Georredundante Standard com Acesso de Leitura (Standard-RAGRS).
- Utilitário de Linha de Comandos do AzCopy. Transfira e instale a [versão mais recente do AzCopy][], que é instalada com as Ferramentas de Armazenamento do Microsoft Azure.

    ![Ferramentas de Armazenamento do Azure](./media/sql-data-warehouse-get-started-load-with-polybase/install-azcopy.png)


## Passo 1: adicionar dados de exemplo ao armazenamento de blobs do Azure

Para carregar dados, é necessário colocar alguns dados de exemplo num armazenamento de blobs do Azure. Neste passo, vamos preencher um blob de Armazenamento do Azure com dados de exemplo. Mais tarde, iremos utilizar o PolyBase para carregar estes dados de exemplo para a sua base de dados SQL Data Warehouse.

### A. Preparar um ficheiro de texto de exemplo

Para preparar um ficheiro de texto de exemplo:

1. Abra o Bloco de Notas e copie as seguintes linhas de dados para um novo ficheiro. Guarde-o no seu diretório temporário local como %temp%\DimDate2.txt.

```
20150301,1,3
20150501,2,4
20151001,4,2
20150201,1,3
20151201,4,2
20150801,3,1
20150601,2,4
20151101,4,2
20150401,2,4
20150701,3,1
20150901,3,1
20150101,1,3
```

### B. Localizar o ponto final de serviço blob

Para localizar o ponto final de serviço blob:

1. No Portal do Azure, selecione **Procurar** > **Contas de Armazenamento**.
2. Clique na conta de armazenamento que pretende utilizar.
3. No painel da conta de Armazenamento, clique em Blobs

    ![Clicar em Blobs](./media/sql-data-warehouse-get-started-load-with-polybase/click-blobs.png)

1. Guarde o URL do seu ponto final de serviço blob para utilizar mais tarde.

    ![Ponto final de serviço blob](./media/sql-data-warehouse-get-started-load-with-polybase/blob-service.png)

### C. Localizar a chave de armazenamento do Azure

Para localizar a chave de armazenamento do Azure:

1. No Portal do Azure, selecione **Procurar** > **Contas de Armazenamento**.
2. Clique na conta de armazenamento que pretende utilizar.
3. Selecione **Todas as definições** > **Chaves de acesso**.
4. Clique na caixa de cópia para copiar uma das suas chaves de acesso para a área de transferência.

    ![Copiar a chave de armazenamento do Azure](./media/sql-data-warehouse-get-started-load-with-polybase/access-key.png)

### D. Copiar o ficheiro de exemplo para o armazenamento de blobs do Azure

Para copiar os dados para o armazenamento de blobs do Azure:

1. Abra uma linha de comandos e mude de diretório para o diretório de instalação do AzCopy. Este comando altera-se para o diretório de instalação predefinido num cliente Windows de 64 bits.

    ```
    cd /d "%ProgramFiles(x86)%\Microsoft SDKs\Azure\AzCopy"
    ```

1. Execute o seguinte comando para carregar o ficheiro. Especifique o URL do ponto final de serviço blob para <blob service endpoint URL> e a chave da conta de armazenamento do Azure para <azure_storage_account_key>.

    ```
    .\AzCopy.exe /Source:C:\Temp\ /Dest:<blob service endpoint URL> /datacontainer/datedimension/ /DestKey:<azure_storage_account_key> /Pattern:DimDate2.txt
    ```

Consulte também a [Getting Started with the AzCopy Command-Line Utility (Introdução ao Utilitário de Linha de Comandos AzCopy)][versão mais recente do AzCopy].

### E. Explorar o contentor do armazenamento de blobs

Para ver o ficheiro que carregou para o armazenamento de blobs:

1. Volte ao painel do serviço Blob.
2. Em Contentores, faça duplo clique em **datacontainer**.
3. Para explorar o caminho para os dados, clique na pasta **datedimension** e irá ver o ficheiro carregado **DimDate2.txt**.
4. Para ver as propriedades, clique em **DimDate2.txt**.
5. Tenha em atenção que, no painel de propriedades Blob, pode transferir ou eliminar o ficheiro.

    ![Ver o blob de armazenamento do Azure](./media/sql-data-warehouse-get-started-load-with-polybase/view-blob.png)


## Passo 2: criar uma tabela externa para os dados de exemplo

Nesta secção, iremos criar uma tabela externa que define os dados de exemplo.

O PolyBase utiliza tabelas externas para aceder aos dados no armazenamento de blobs do Azure. Uma vez que os dados não são armazenados no SQL Data Warehouse, o PolyBase processa a autenticação dos dados externos através de uma credencial com âmbito de base de dados.

O exemplo neste passo utiliza estas instruções Transact-SQL para criar uma tabela externa.

- [Criar Chave Mestra (Transact-SQL)][] para encriptar o segredo da credencial com âmbito de base de dados.
- [Criar Credencial com Âmbito de Base de Dados (Transact-SQL)][] para especificar as informações de autenticação para a sua conta de armazenamento do Azure.
- [Criar Origem de Dados Externa (Transact-SQL)][] para especificar a localização do armazenamento de blobs do Azure.
- [Criar Formato de Ficheiro Externo (Transact-SQL)][] para especificar o formato dos seus dados.
- [Criar Tabela Externa (Transact-SQL)][] para especificar a definição da tabela e a localização dos dados.

Execute esta consulta na base de dados SQL Data Warehouse. Irá ser criada uma tabela externa com o nome DimDate2External no esquema dbo que aponta para os dados de exemplo DimDate2.txt no armazenamento de blobs do Azure.


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


-- D: Create an external file format
-- FORMAT_TYPE: Type of file format in Azure storage (supported: DELIMITEDTEXT, RCFILE, ORC, PARQUET).
-- FORMAT_OPTIONS: Specify field terminator, string delimiter, date format etc. for delimited text files.
-- Specify DATA_COMPRESSION method if data is compressed.

CREATE EXTERNAL FILE FORMAT TextFile
WITH (
    FORMAT_TYPE = DelimitedText,
    FORMAT_OPTIONS (FIELD_TERMINATOR = ',')
);


-- E: Create the external table
-- Specify column names and data types. This needs to match the data in the sample file.
-- LOCATION: Specify path to file or directory that contains the data (relative to the blob container).
-- To point to all files under the blob container, use LOCATION='.'

CREATE EXTERNAL TABLE dbo.DimDate2External (
    DateId INT NOT NULL,
    CalendarQuarter TINYINT NOT NULL,
    FiscalQuarter TINYINT NOT NULL
)
WITH (
    LOCATION='/datedimension/',
    DATA_SOURCE=AzureStorage,
    FILE_FORMAT=TextFile
);


-- Run a query on the external table

SELECT count(*) FROM dbo.DimDate2External;

```


No SQL Server Object Explorer do Visual Studio, pode ver o formato de ficheiro externo, a origem de dados externa e a tabela DimDate2External.

![Ver tabela externa](./media/sql-data-warehouse-get-started-load-with-polybase/external-table.png)

## Passo 3: carregar dados para o SQL Data Warehouse

Depois da criação da tabela externa, pode carregar os dados para uma nova tabela ou inseri-los numa tabela existente.

- Para carregar os dados para uma nova tabela, execute a instrução [CREATE TABLE AS SELECT (Transact-SQL)][]. A nova tabela terá as colunas nomeadas na consulta. Os tipos de dados das colunas irão corresponder aos tipos de dados na definição da tabela externa.
- Para carregar os dados para uma tabela existente, utilize a instrução [INSERT...SELECT (Transact-SQL)][].

```sql
-- Load the data from Azure blob storage to SQL Data Warehouse

CREATE TABLE dbo.DimDate2
WITH
(   
    CLUSTERED COLUMNSTORE INDEX,
    DISTRIBUTION = ROUND_ROBIN
)
AS
SELECT * FROM [dbo].[DimDate2External];
```

## Passo 4: criar estatísticas nos dados recentemente carregados

O SQL Data Warehouse não cria nem atualiza automaticamente as estatísticas. Por isso, para obter um desempenho de consulta elevado, é importante criar estatísticas em cada coluna de cada tabela após o primeiro carregamento. Também é importante atualizar as estatísticas após alterações substanciais nos dados.

Este exemplo cria estatísticas de coluna única na nova tabela DimDate2.

```sql
CREATE STATISTICS [DateId] on [DimDate2] ([DateId]);
CREATE STATISTICS [CalendarQuarter] on [DimDate2] ([CalendarQuarter]);
CREATE STATISTICS [FiscalQuarter] on [DimDate2] ([FiscalQuarter]);
```

Para obter mais informações, consulte [Estatísticas][].  


## Passos seguintes
Consulte o [Guia do PolyBase][] para obter informações adicionais que deve conhecer ao desenvolver uma solução que utiliza o PolyBase.

<!--Image references-->


<!--Article references-->
[Tutorial do PolyBase no SQL Data Warehouse]: ./sql-data-warehouse-get-started-load-with-polybase.md
[Carregar dados com o bcp]: ./sql-data-warehouse-load-with-bcp.md
[Estatísticas]: ./sql-data-warehouse-tables-statistics.md
[Guia do PolyBase]: ./sql-data-warehouse-load-polybase-guide.md
[versão mais recente do AzCopy]: ../storage/storage-use-azcopy.md

<!--External references-->
[origem suportada/sink]: https://msdn.microsoft.com/library/dn894007.aspx
[atividade de cópia]: https://msdn.microsoft.com/library/dn835035.aspx
[adaptador de destino do SQL Server]: https://msdn.microsoft.com/library/ms141095.aspx
[SSIS]: https://msdn.microsoft.com/library/ms141026.aspx


[Criar Origem de Dados Externa (Transact-SQL)]:https://msdn.microsoft.com/library/dn935022.aspx
[Criar Formato de Ficheiro Externo (Transact-SQL)]:https://msdn.microsoft.com/library/dn935026.aspx
[Criar Tabela Externa (Transact-SQL)]:https://msdn.microsoft.com/library/dn935021.aspx

[DROP EXTERNAL DATA SOURCE (Transact-SQL)]:https://msdn.microsoft.com/library/mt146367.aspx
[DROP EXTERNAL FILE FORMAT (Transact-SQL)]:https://msdn.microsoft.com/library/mt146379.aspx
[DROP EXTERNAL TABLE (Transact-SQL)]:https://msdn.microsoft.com/library/mt130698.aspx

[CREATE TABLE AS SELECT (Transact-SQL)]:https://msdn.microsoft.com/library/mt204041.aspx
[INSERT...SELECT (Transact-SQL)]:https://msdn.microsoft.com/library/ms174335.aspx
[Criar Chave Mestra (Transact-SQL)]:https://msdn.microsoft.com/library/ms174382.aspx
[CREATE CREDENTIAL (Transact-SQL)]:https://msdn.microsoft.com/library/ms189522.aspx
[Criar Credencial com Âmbito de Base de Dados (Transact-SQL)]:https://msdn.microsoft.com/library/mt270260.aspx
[DROP CREDENTIAL (Transact-SQL)]:https://msdn.microsoft.com/library/ms189450.aspx



<!--HONumber=Aug16_HO1-->


