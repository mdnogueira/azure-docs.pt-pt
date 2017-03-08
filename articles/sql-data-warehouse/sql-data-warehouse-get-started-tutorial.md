---
title: "Azure SQL Data Warehouse - tutorial de introdução | Microsoft Docs"
description: "Este tutorial ensina-lhe a aprovisionar e carregar dados para o Azure SQL Data Warehouse. Também vai aprender as noções básicas sobre o dimensionamento, a colocação em pausa e a otimização."
services: sql-data-warehouse
documentationcenter: NA
author: hirokib
manager: johnmac
editor: barbkess
ms.assetid: 52DFC191-E094-4B04-893F-B64D5828A900
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: hero-article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.date: 01/26/2017
ms.author: elbutter;barbkess
translationtype: Human Translation
ms.sourcegitcommit: 2c88c1abd2af7a1ca041cd5003fd1f848e1b311c
ms.openlocfilehash: 12f72e76ee991dfb701637847f2e406cd0f8c449
ms.lasthandoff: 02/03/2017


---
# <a name="get-started-with-sql-data-warehouse"></a>Introdução ao SQL Data Warehouse

Este tutorial mostra-lhe como aprovisionar e carregar dados para o Azure SQL Data Warehouse. Também vai aprender as noções básicas sobre o dimensionamento, a colocação em pausa e a otimização. Quando tiver terminado, estará pronto para consultar e explorar o seu armazém de dados.

**Tempo de conclusão previsto:** este é um tutorial completo com código de exemplo que demora cerca de 30 minutos a concluir depois de cumprir os pré-requisitos. 

## <a name="prerequisites"></a>Pré-requisitos

O tutorial parte do princípio de que está familiarizado com os conceitos básicos do SQL Data Warehouse. Se precisar de uma introdução, veja [O que é o Azure SQL Data Warehouse?](sql-data-warehouse-overview-what-is.md). 

### <a name="sign-up-for-microsoft-azure"></a>Inscrever-se no Microsoft Azure
Se ainda não tem uma conta do Microsoft Azure, tem de se inscrever numa para utilizar este serviço. Se já tiver uma conta, pode ignorar este passo. 

1. Navegue para as páginas de conta, [https://azure.microsoft.com/account/](https://azure.microsoft.com/account/)
2. Crie uma conta do Azure gratuita ou compre uma.
3. Siga as instruções

### <a name="install-appropriate-sql-client-drivers-and-tools"></a>Instalar o controlador e as ferramentas de cliente SQL adequadas

A maioria das ferramentas de cliente SQL podem ligar-se ao SQL Data Warehouse através de JDBC, ODBC ou ADO.NET. Devido ao grande número de funcionalidades T-SQL que o SQL Data Warehouse suporta, algumas aplicações cliente não são totalmente compatíveis com o mesmo.

Se estiver a executar um sistema operativo Windows, recomendamos utilizar o [Visual Studio] ou o [SQL Server Management Studio].

[!INCLUDE [Create a new logical server](../../includes/sql-data-warehouse-create-logical-server.md)] 

[!INCLUDE [SQL Database create server](../../includes/sql-database-create-new-server-firewall-portal.md)]

## <a name="create-a-sql-data-warehouse"></a>Criar um SQL Data Warehouse

O SQL Data Warehouse é um tipo especial de base de dados concebido para processamento em paralelo em massa. A base de dados é distribuída em vários nós e processa consultas em paralelo. O SQL Data Warehouse tem um nó de controlo que orquestra as atividades de todos os nós. Os nós em si utilizam a Base de Dados SQL para gerir os seus dados.  

> [!NOTE]
> A criação de um SQL Data Warehouse poderá resultar num novo serviço sujeito a faturação.  Para obter mais informações, veja [Preços do SQL Data Warehouse](https://azure.microsoft.com/pricing/details/sql-data-warehouse/).
>

### <a name="create-a-data-warehouse"></a>Criar um armazém de dados

1. Inicie sessão no [Portal do Azure](https://portal.azure.com).
2. Clique em **Novo** > **Bases de dados** > **SQL Data Warehouse**.

    ![NewBlade](../../includes/media/sql-data-warehouse-create-dw/blade-click-new.png)
    ![SelectDW](../../includes/media/sql-data-warehouse-create-dw/blade-select-dw.png)

3. Preencha os detalhes da implementação

    **Nome da Base de Dados**: escolha o nome que quiser. Se tiver vários armazéns de dados, recomendamos que os seus nomes incluam detalhes como a região, o ambiente, por exemplo *mydw-westus-1-test*.

    **Subscrição**: a sua subscrição do Azure

    **Grupo de recursos**: crie um grupo de recursos ou utilize um já existente.
    > [!NOTE]
    > Os grupos de recursos são úteis para a administração de recursos, como delimitar o âmbito do controlo de acesso e a implementação baseada em modelos. Leia mais sobre os grupos de recursos do Azure e as melhores práticas [aqui](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview#resource-groups).

    **Origem**: base de dados vazia

    **Servidor**: selecione o servidor que criou em [Pré-requisitos].

    **Agrupamento**: deixe o agrupamento predefinido, SQL_Latin1_General_CP1_CI_AS.

    **Selecionar o desempenho**: recomendamos começar com as 400 DWU standard.

4. Escolha **Afixar ao dashboard**
    ![Afixar ao Dashboard](./media/sql-data-warehouse-get-started-tutorial/pin-to-dashboard.png)

5. Relaxe e aguarde pela implementação do seu armazém de dados! É normal que este processo demore alguns minutos. O portal notifica-o quando o armazém de dados estiver pronto para utilização. 

## <a name="connect-to-sql-data-warehouse"></a>Ligar ao SQL Data Warehouse

Este tutorial utiliza o SQL Server Management Studio (SSMS) para ligar ao armazém de dados. Pode ligar ao SQL Data Warehouse com estes conectores suportados: ADO.NET, JDBC, ODBC e PHP. Não se esqueça de que a funcionalidade das ferramentas que não são suportadas pela Microsoft pode ser limitada.


### <a name="get-connection-information"></a>Obter informações da ligação

Para ligar ao seu armazém de dados, tem de se ligar através do servidor SQL lógico que criou em [Pré-requisitos].

1. Selecione o armazém de dados no dashboard ou procure-o nos seus recursos.

    ![Dashboard do SQL Data Warehouse](./media/sql-data-warehouse-get-started-tutorial/sql-dw-dashboard.png)

2. Localize o nome completo do servidor SQL lógico.

    ![Selecionar Nome do Servidor](./media/sql-data-warehouse-get-started-tutorial/select-server.png)

3. Abra o SSMS e utilize o Object Explorer para ligar a este servidor com as credenciais de administrador do servidor criadas em [Pré-requisitos].

    ![Ligar com o SSMS](./media/sql-data-warehouse-get-started-tutorial/ssms-connect.png)

Se tudo correr bem, deverá estar agora ligado ao seu servidor SQL lógico. Uma vez que tem sessão iniciada como o administrador do servidor, pode ligar-se a qualquer base de dados alojada pelo servidor, incluindo a base de dados mestra. 

Só existe uma conta de administrador do servidor, que tem a maioria dos privilégios de qualquer utilizador. Tenha cuidado para não deixar que muitas pessoas da sua organização saibam a palavra-passe de administrador. 

Também pode ter uma conta de administrador do Azure Active Directory. Não disponibilizamos os detalhes aqui. Se quiser saber mais sobre como utilizar a autenticação do Azure Active Directory, veja [Azure AD authentication (Autenticação do Azure AD)](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication).

Em seguida, vamos explorar a criação de inícios de sessão e utilizadores adicionais.


## <a name="create-a-database-user"></a>Criar um utilizador de base de dados

Neste passo, vai criar uma conta de utilizador para aceder ao seu armazém de dados. Também lhe mostramos como dar a esse utilizador a possibilidade de executar consultas com uma grande quantidade de memória e recursos da CPU.

### <a name="notes-about-resource-classes-for-allocating-resources-to-queries"></a>Notas sobre as classes de recursos para alocar recursos a consultas

- Para manter os seus dados seguros, não utilize o administrador de servidor para executar consultas nas suas bases de dados de produção. Este tem muito mais privilégios do que os outros utilizadores e utilizá-lo para realizar operações nos dados do utilizador compromete os seus dados. Além disso, uma vez que o administrador do servidor se destina a fazer operações de gestão, executa-as apenas com uma pequena alocação de memória e recursos da CPU. 

- O SQL Data Warehouse utiliza funções de bases de dados predefinidas, denominadas “classes de recursos”, para alocar diferentes quantidades de memória, recursos da CPU e ranhuras de simultaneidade aos utilizadores. Cada utilizador pode pertencer a uma classe de recursos pequena, média, grande ou muito grande. A classe de recursos do utilizador determina os recursos que este tem para executar consultas e operações de carregamento.

- Para otimizar a compressão de dados, geralmente, o utilizador precisa de carregamento com alocações de recursos grandes ou muito grandes. Leia mais sobre as classes de recursos [aqui](./sql-data-warehouse-develop-concurrency.md#resource-classes).

### <a name="create-an-account-that-can-control-a-database"></a>Criar uma conta que pode controlar uma base de dados

Uma vez que tem sessão iniciada como o administrador do servidor, tem permissões para criar inícios de sessão e utilizadores.

1. Utilizando o SSMS ou outro cliente de consultas, abra uma consulta nova para **mestre**.

    ![Nova Consulta na Base de Dados Mestra](./media/sql-data-warehouse-get-started-tutorial/query-on-server.png)

    ![Nova Consulta na Base de Dados Mestra1](./media/sql-data-warehouse-get-started-tutorial/query-on-master.png)

2. Na janela da consulta, execute este comando T-SQL para criar um início de sessão com o nome MedRCLogin e um utilizador com o nome LoadingUser. Este início de sessão pode ligar ao servidor SQL lógico.

    ```sql
    CREATE LOGIN MedRCLogin WITH PASSWORD = 'a123reallySTRONGpassword!';
    CREATE USER LoadingUser FOR LOGIN MedRCLogin;
    ```

3. Agora, ao consultar a *base de dados do SQL Data Warehouse*, crie um utilizador de base de dados com base no início de sessão que criou para aceder e realizar operações na base de dados.

    ```sql
    CREATE USER LoadingUser FOR LOGIN MedRCLogin;
    ```

4. Conceda ao utilizador de base de dados permissões de controlo para a base de dados chamada NYT. 

    ```sql
    GRANT CONTROL ON DATABASE::[NYT] to LoadingUser;
    ```
    > [!NOTE]
    > Se o seu nome de base de dados tiver hífenes, confirme que os coloca entre parêntesis. 
    >

### <a name="give-the-user-medium-resource-allocations"></a>Dar ao utilizador alocações de recursos médios

1. Execute este comando T-SQL para torná-lo membro da classe de recursos médios, que tem o nome mediumrc. 

    ```sql
    EXEC sp_addrolemember 'mediumrc', 'LoadingUser';
    ```
    > [!NOTE]
    > Clique [aqui](sql-data-warehouse-develop-concurrency.md#resource-classes) para saber mais sobre as classes de simultaneidade e recursos! 
    >

2. Ligar ao servidor lógico com as credenciais novas

    ![Inicie Sessão com o Início de Sessão Novo](./media/sql-data-warehouse-get-started-tutorial/new-login.png)


## <a name="load-data-from-azure-blob-storage"></a>Carregar dados a partir do Armazenamento de Blobs do Azure

Está agora pronto para carregar dados para o seu armazém de dados. Este passo mostra-lhe como carregar os dados de táxis da cidade de Nova Iorque a partir de um blob de armazenamento do Azure. 

- Uma forma comum de carregar dados para o SQL Data Warehouse é, primeiro, movê-los para o Armazenamento de Blobs do Azure e, em seguida, carregá-los para o armazém de dados. Para que seja mais fácil compreender o carregamento, temos dados de táxis de Nova Iorque já alojados num blob de armazenamento do Azure público. 

- Para referência futura e para saber como colocar os seus dados no armazenamento de blobs do Azure ou carregá-los diretamente da sua origem para o SQL Data Warehouse,veja a [descrição geral do carregamento](sql-data-warehouse-overview-load.md).


### <a name="define-external-data"></a>Definir dados externos

1. Crie uma chave mestra. Só tem de criar uma chave mestra uma vez por base de dados. 

    ```sql
    CREATE MASTER KEY;
    ```

2. Defina a localização do blob do Azure que contém os dados dos táxis.  

    ```sql
    CREATE EXTERNAL DATA SOURCE NYTPublic
    WITH
    (
        TYPE = Hadoop,
        LOCATION = 'wasbs://2013@nytpublic.blob.core.windows.net/'
    );
    ```

3. Defina os formatos dos ficheiros externos

    O comando ```CREATE EXTERNAL FILE FORMAT``` é utilizado para especificar o formato dos ficheiros que contêm os dados externos. Contêm texto separado por um ou mais carateres, que são chamados delimitadores. Para efeitos de demonstração, os dados dos táxis são armazenados como dados não comprimidos e como dados comprimidos gzip.

    Execute estes comandos T-SQL para definir dois formatos distintos: não comprimido e comprimido.

    ```sql
    CREATE EXTERNAL FILE FORMAT uncompressedcsv
    WITH (
        FORMAT_TYPE = DELIMITEDTEXT,
        FORMAT_OPTIONS ( 
            FIELD_TERMINATOR = ',',
            STRING_DELIMITER = '',
            DATE_FORMAT = '',
            USE_TYPE_DEFAULT = False
        )
    );

    CREATE EXTERNAL FILE FORMAT compressedcsv
    WITH ( 
        FORMAT_TYPE = DELIMITEDTEXT,
        FORMAT_OPTIONS ( FIELD_TERMINATOR = '|',
            STRING_DELIMITER = '',
        DATE_FORMAT = '',
            USE_TYPE_DEFAULT = False
        ),
        DATA_COMPRESSION = 'org.apache.hadoop.io.compress.GzipCodec'
    );
    ```

4.  Crie um esquema para o formato de ficheiro externo. 

    ```sql
    CREATE SCHEMA ext;
    ```
5. Crie as tabelas externas. Estas tabelas referenciam dados armazenados no Armazenamento de Blobs do Azure. Execute os comandos T-SQL seguintes para criar várias tabelas externas que apontem para o blob do Azure que definimos anteriormente na origem de dados externa.

```sql
    CREATE EXTERNAL TABLE [ext].[Date] 
    (
        [DateID] int NOT NULL,
        [Date] datetime NULL,
        [DateBKey] char(10) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [DayOfMonth] varchar(2) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [DaySuffix] varchar(4) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [DayName] varchar(9) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [DayOfWeek] char(1) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [DayOfWeekInMonth] varchar(2) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [DayOfWeekInYear] varchar(2) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [DayOfQuarter] varchar(3) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [DayOfYear] varchar(3) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [WeekOfMonth] varchar(1) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [WeekOfQuarter] varchar(2) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [WeekOfYear] varchar(2) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [Month] varchar(2) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [MonthName] varchar(9) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [MonthOfQuarter] varchar(2) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [Quarter] char(1) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [QuarterName] varchar(9) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [Year] char(4) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [YearName] char(7) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [MonthYear] char(10) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [MMYYYY] char(6) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [FirstDayOfMonth] date NULL,
        [LastDayOfMonth] date NULL,
        [FirstDayOfQuarter] date NULL,
        [LastDayOfQuarter] date NULL,
        [FirstDayOfYear] date NULL,
        [LastDayOfYear] date NULL,
        [IsHolidayUSA] bit NULL,
        [IsWeekday] bit NULL,
        [HolidayUSA] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NULL
    )
    WITH
    (
        LOCATION = 'Date',
        DATA_SOURCE = NYTPublic,
        FILE_FORMAT = uncompressedcsv,
        REJECT_TYPE = value,
        REJECT_VALUE = 0
    );
    
    CREATE EXTERNAL TABLE [ext].[Geography]
    (
        [GeographyID] int NOT NULL,
        [ZipCodeBKey] varchar(10) COLLATE SQL_Latin1_General_CP1_CI_AS NOT NULL,
        [County] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [City] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [State] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [Country] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [ZipCode] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NULL
    )
    WITH
    (
        LOCATION = 'Geography',
        DATA_SOURCE = NYTPublic,
        FILE_FORMAT = uncompressedcsv,
        REJECT_TYPE = value,
        REJECT_VALUE = 0 
    );
        
    
    CREATE EXTERNAL TABLE [ext].[HackneyLicense]
    (
        [HackneyLicenseID] int NOT NULL,
        [HackneyLicenseBKey] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NOT NULL,
        [HackneyLicenseCode] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NULL
    )
    WITH
    (
        LOCATION = 'HackneyLicense',
        DATA_SOURCE = NYTPublic,
        FILE_FORMAT = uncompressedcsv,
        REJECT_TYPE = value,
        REJECT_VALUE = 0
    )
    ;
        
    
    CREATE EXTERNAL TABLE [ext].[Medallion]
    (
        [MedallionID] int NOT NULL,
        [MedallionBKey] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NOT NULL,
        [MedallionCode] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NULL
    )
    WITH
    (
        LOCATION = 'Medallion',
        DATA_SOURCE = NYTPublic,
        FILE_FORMAT = uncompressedcsv,
        REJECT_TYPE = value,
        REJECT_VALUE = 0
    )
    ;
        
    CREATE EXTERNAL TABLE [ext].[Time]
    (
        [TimeID] int NOT NULL,
        [TimeBKey] varchar(8) COLLATE SQL_Latin1_General_CP1_CI_AS NOT NULL,
        [HourNumber] tinyint NOT NULL,
        [MinuteNumber] tinyint NOT NULL,
        [SecondNumber] tinyint NOT NULL,
        [TimeInSecond] int NOT NULL,
        [HourlyBucket] varchar(15) COLLATE SQL_Latin1_General_CP1_CI_AS NOT NULL,
        [DayTimeBucketGroupKey] int NOT NULL,
        [DayTimeBucket] varchar(100) COLLATE SQL_Latin1_General_CP1_CI_AS NOT NULL
    )
    WITH
    (
        LOCATION = 'Time',
        DATA_SOURCE = NYTPublic,
        FILE_FORMAT = uncompressedcsv,
        REJECT_TYPE = value,
        REJECT_VALUE = 0
    )
    ;
    
    
    CREATE EXTERNAL TABLE [ext].[Trip]
    (
        [DateID] int NOT NULL,
        [MedallionID] int NOT NULL,
        [HackneyLicenseID] int NOT NULL,
        [PickupTimeID] int NOT NULL,
        [DropoffTimeID] int NOT NULL,
        [PickupGeographyID] int NULL,
        [DropoffGeographyID] int NULL,
        [PickupLatitude] float NULL,
        [PickupLongitude] float NULL,
        [PickupLatLong] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [DropoffLatitude] float NULL,
        [DropoffLongitude] float NULL,
        [DropoffLatLong] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [PassengerCount] int NULL,
        [TripDurationSeconds] int NULL,
        [TripDistanceMiles] float NULL,
        [PaymentType] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [FareAmount] money NULL,
        [SurchargeAmount] money NULL,
        [TaxAmount] money NULL,
        [TipAmount] money NULL,
        [TollsAmount] money NULL,
        [TotalAmount] money NULL
    )
    WITH
    (
        LOCATION = 'Trip2013',
        DATA_SOURCE = NYTPublic,
        FILE_FORMAT = compressedcsv,
        REJECT_TYPE = value,
        REJECT_VALUE = 0
    )
    ;
    
    CREATE EXTERNAL TABLE [ext].[Weather]
    (
        [DateID] int NOT NULL,
        [GeographyID] int NOT NULL,
        [PrecipitationInches] float NOT NULL,
        [AvgTemperatureFahrenheit] float NOT NULL
    )
    WITH
    (
        LOCATION = 'Weather2013',
        DATA_SOURCE = NYTPublic,
        FILE_FORMAT = uncompressedcsv,
        REJECT_TYPE = value,
        REJECT_VALUE = 0
    )
    ;
    ```

### Import the data from Azure blob storage.

SQL Data Warehouse supports a key statement called CREATE TABLE AS SELECT (CTAS). This statement creates a new table based on the results of a select statement. The new table has the same columns and data types as the results of the select statement.  This is an elegant way to import data from Azure blob storage into SQL Data Warehouse.

1. Run this script to import your data.

    ```sql
    CREATE TABLE [dbo].[Date]
    WITH
    ( 
        DISTRIBUTION = ROUND_ROBIN,
        CLUSTERED COLUMNSTORE INDEX
    )
    AS SELECT * FROM [ext].[Date]
    OPTION (LABEL = 'CTAS : Load [dbo].[Date]')
    ;
    
    CREATE TABLE [dbo].[Geography]
    WITH
    ( 
        DISTRIBUTION = ROUND_ROBIN,
        CLUSTERED COLUMNSTORE INDEX
    )
    AS
    SELECT * FROM [ext].[Geography]
    OPTION (LABEL = 'CTAS : Load [dbo].[Geography]')
    ;
    
    CREATE TABLE [dbo].[HackneyLicense]
    WITH
    ( 
        DISTRIBUTION = ROUND_ROBIN,
        CLUSTERED COLUMNSTORE INDEX
    )
    AS SELECT * FROM [ext].[HackneyLicense]
    OPTION (LABEL = 'CTAS : Load [dbo].[HackneyLicense]')
    ;
    
    CREATE TABLE [dbo].[Medallion]
    WITH
    (
        DISTRIBUTION = ROUND_ROBIN,
        CLUSTERED COLUMNSTORE INDEX
    )
    AS SELECT * FROM [ext].[Medallion]
    OPTION (LABEL = 'CTAS : Load [dbo].[Medallion]')
    ;
    
    CREATE TABLE [dbo].[Time]
    WITH
    (
        DISTRIBUTION = ROUND_ROBIN,
        CLUSTERED COLUMNSTORE INDEX
    )
    AS SELECT * FROM [ext].[Time]
    OPTION (LABEL = 'CTAS : Load [dbo].[Time]')
    ;
    
    CREATE TABLE [dbo].[Weather]
    WITH
    ( 
        DISTRIBUTION = ROUND_ROBIN,
        CLUSTERED COLUMNSTORE INDEX
    )
    AS SELECT * FROM [ext].[Weather]
    OPTION (LABEL = 'CTAS : Load [dbo].[Weather]')
    ;
    
    CREATE TABLE [dbo].[Trip]
    WITH
    (
        DISTRIBUTION = ROUND_ROBIN,
        CLUSTERED COLUMNSTORE INDEX
    )
    AS SELECT * FROM [ext].[Trip]
    OPTION (LABEL = 'CTAS : Load [dbo].[Trip]')
    ;
    ```

2. View your data as it loads.

   You’re loading several GBs of data and compressing it into highly performant clustered columnstore indexes. Run the following query that uses a dynamic management views (DMVs) to show the status of the load. After starting the query, grab a coffee and a snack while SQL Data Warehouse does some heavy lifting.
    
    ```sql
    SELECT
        r.command,
        s.request_id,
        r.status,
        count(distinct input_name) as nbr_files,
        sum(s.bytes_processed)/1024/1024 as gb_processed
    FROM 
        sys.dm_pdw_exec_requests r
        INNER JOIN sys.dm_pdw_dms_external_work s
        ON r.request_id = s.request_id
    WHERE
        r.[label] = 'CTAS : Load [dbo].[Date]' OR
        r.[label] = 'CTAS : Load [dbo].[Geography]' OR
        r.[label] = 'CTAS : Load [dbo].[HackneyLicense]' OR
        r.[label] = 'CTAS : Load [dbo].[Medallion]' OR
        r.[label] = 'CTAS : Load [dbo].[Time]' OR
        r.[label] = 'CTAS : Load [dbo].[Weather]' OR
        r.[label] = 'CTAS : Load [dbo].[Trip]'
    GROUP BY
        r.command,
        s.request_id,
        r.status
    ORDER BY
        nbr_files desc, 
        gb_processed desc;
    ```

3. View all system queries.

    ```sql
    SELECT * FROM sys.dm_pdw_exec_requests;
    ```

4. Enjoy seeing your data nicely loaded into your Azure SQL Data Warehouse.

    ![See Data Loaded](./media/sql-data-warehouse-get-started-tutorial/see-data-loaded.png)


## Improve query performance

There are several ways to improve query performance and to achieve the high-speed performance that SQL Data Warehouse is designed to provide.  

### See the effect of scaling on query performance 

One way to improve query performance is to scale resources by changing the DWU service level for your data warehouse. Each service level costs more, but you can scale back or pause resources at any time. 

In this step, you compare performance at two different DWU settings.

First, let's scale the sizing down to 100 DWU so we can get an idea of how one compute node might perform on its own.

1. Go to the portal and select your SQL Data Warehouse.

2. Select scale in the SQL Data Warehouse blade. 

    ![Scale DW From portal](./media/sql-data-warehouse-get-started-tutorial/scale-dw.png)

3. Scale down the performance bar to 100 DWU and hit save.

    ![Scale and save](./media/sql-data-warehouse-get-started-tutorial/scale-and-save.png)

4. Wait for your scale operation to finish.

    > [!NOTE]
    > Queries cannot run while changing the scale. Scaling **kills** your currently running queries. You can restart them when the operation is finished.
    >
    
5. Do a scan operation on the trip data, selecting the top million entries for all the columns. If you're eager to move on quickly, feel free to select fewer rows. Take note of the time it takes to run this operation.

    ```sql
    SELECT TOP(1000000) * FROM dbo.[Trip]
    ```
6. Scale your data warehouse back to 400 DWU. Remember, each 100 DWU is adding another compute node to your Azure SQL Data Warehouse.

7. Run the query again! You should notice a significant difference. 

> [!NOTE]
> Since SQL Data Warehouse uses massively parallel processing. Queries that scan or perform analytic functions on millions of rows experience the true power of
> Azure SQL Data Warehouse.
>

### See the effect of statistics on query performance

1. Run a query that joins the Date table with the Trip table

    ```sql
    SELECT TOP (1000000) 
        dt.[DayOfWeek],
        tr.[MedallionID],
        tr.[HackneyLicenseID],
        tr.[PickupTimeID],
        tr.[DropoffTimeID],
        tr.[PickupGeographyID],
        tr.[DropoffGeographyID],
        tr.[PickupLatitude],
        tr.[PickupLongitude],
        tr.[PickupLatLong],
        tr.[DropoffLatitude],
        tr.[DropoffLongitude],
        tr.[DropoffLatLong],
        tr.[PassengerCount],
        tr.[TripDurationSeconds],
        tr.[TripDistanceMiles],
        tr.[PaymentType],
        tr.[FareAmount],
        tr.[SurchargeAmount],
        tr.[TaxAmount],
        tr.[TipAmount],
        tr.[TollsAmount],
        tr.[TotalAmount]
    FROM [dbo].[Trip] as tr
        JOIN dbo.[Date] as dt
        ON  tr.DateID = dt.DateID
    ```

    This query takes a while because SQL Data Warehouse has to shuffle data before it can perform the join. Joins do not have to shuffle data if they are designed to join data in the same way it is distributed. That's a deeper subject. 

2. Statistics make a difference. 
3. Run this statement to create statistics on the join columns.

    ```sql
    CREATE STATISTICS [dbo.Date DateID stats] ON dbo.Date (DateID);
    CREATE STATISTICS [dbo.Trip DateID stats] ON dbo.Trip (DateID);
    ```

    > [!NOTE]
    > SQL DW does not automatically manage statistics for you. Statistics are important for query
    > performance and it is highly recommended you create and update statistics.
    > 
    > **You gain the most benefit by having statistics on columns involved in joins, columns
    > used in the WHERE clause and columns found in GROUP BY.**
    >

3. Run the query from Prerequisites again and observe any performance differences. While the differences in query performance will not be as drastic as scaling up, you should notice a  speed-up. 

## Next steps

You're now ready to query and explore. Check out our best practices or tips.

If you're done exploring for the day, make sure to pause your instance! In production, you can experience enormous 
savings by pausing and scaling to meet your business needs.

![Pause](./media/sql-data-warehouse-get-started-tutorial/pause.png)

## Useful readings

[Concurrency and Workload Management][]

[Best practices for Azure SQL Data Warehouse][]

[Query Monitoring][]

[Top 10 Best Practices for Building a Large Scale Relational Data Warehouse][]

[Migrating Data to Azure SQL Data Warehouse][]

[Concurrency and Workload Management]: sql-data-warehouse-develop-concurrency.md#change-a-user-resource-class-example
[Best practices for Azure SQL Data Warehouse]: sql-data-warehouse-best-practices.md#hash-distribute-large-tables
[Query Monitoring]: sql-data-warehouse-manage-monitor.md
[Top 10 Best Practices for Building a Large Scale Relational Data Warehouse]: https://blogs.msdn.microsoft.com/sqlcat/2013/09/16/top-10-best-practices-for-building-a-large-scale-relational-data-warehouse/
[Migrating Data to Azure SQL Data Warehouse]: https://blogs.msdn.microsoft.com/sqlcat/2016/08/18/migrating-data-to-azure-sql-data-warehouse-in-practice/



[!INCLUDE [Additional Resources](../../includes/sql-data-warehouse-article-footer.md)]

<!-- Internal Links -->
[Prerequisites]: sql-data-warehouse-get-started-tutorial.md#prerequisites

<!--Other Web references-->
[Visual Studio]: https://www.visualstudio.com/
[SQL Server Management Studio]: https://msdn.microsoft.com/en-us/library/mt238290.aspx

