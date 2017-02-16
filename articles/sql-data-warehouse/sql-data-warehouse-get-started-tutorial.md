---
title: "Azure SQL Data Warehouse - Tutorial de introdução | Microsoft Docs"
description: "Tutorial de Introdução ao Azure SQL Data Warehouse."
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
ms.date: 12/21/2016
ms.author: elbutter
translationtype: Human Translation
ms.sourcegitcommit: fe9de0ffad3fe5d4acbf3caf2f08101f6a13daaf
ms.openlocfilehash: 12f500c01671799612b0d1988e0d07bbfda600da


---
# <a name="get-started-with-sql-data-warehouse"></a>Introdução ao SQL Data Warehouse

Tutorial de Introdução ao Azure SQL Data Warehouse. Este tutorial ensina-lhe as noções básicas de como aprovisionar e carregar dados para um SQL Data Warehouse, bem como algumas noções básicas sobre dimensionar, colocar em pausa e otimizar. 

**Tempo estimado para concluir:** 75 minutos

## <a name="prerequisites"></a>Pré-requisitos


### <a name="sign-up-for-microsoft-azure"></a>Inscrever-se no Microsoft Azure
Se ainda não tem uma conta do Microsoft Azure, tem de se inscrever numa para utilizar este serviço. Se já tiver uma conta, pode ignorar este passo. 

1. Navegue para as páginas de conta, [https://azure.microsoft.com/account/](https://azure.microsoft.com/account/)
2. Crie uma conta do Azure gratuita ou compre uma.
3. Siga as instruções

### <a name="install-appropriate-sql-client-driver-and-tools"></a>Instalar o Controlador e as ferramentas Cliente do SQL adequados

A maioria das ferramentas cliente do SQL podem ligar-se ao Azure SQL Data Warehouse através de JDBC, ODBC ou ADO.net. Devido à complexidade do produto e ao grande número de funcionalidades de T-SQL que o SQL Data Warehouse suporta, é possível que nem todas as aplicações cliente sejam totalmente compatíveis com o mesmo.

Se estiver a executar um Sistema Operativo Windows, recomendamos utilizar o [Visual Studio] ou o [SQL Server Management Studio].


[!INCLUDE [Create a new logical server](../../includes/sql-data-warehouse-create-logical-server.md)] 

[!INCLUDE [SQL Database create server](../../includes/sql-database-create-new-server-firewall-portal.md)]

## <a name="create-an-azure-sql-data-warehouse"></a>Criar um Azure SQL Data Warehouse

> [!NOTE]
> A criação de um SQL Data Warehouse poderá resultar num novo serviço sujeito a faturação.  Para obter mais informações, veja [Preços do SQL Data Warehouse](https://azure.microsoft.com/pricing/details/sql-data-warehouse/).
>


### <a name="create-a-sql-data-warehouse"></a>Criar um SQL Data Warehouse
1. Inicie sessão no [Portal do Azure](https://portal.azure.com).
2. Clique em **Novo** > **Bases de dados** > **SQL Data Warehouse**.

    ![NewBlade](../../includes/media/sql-data-warehouse-create-dw/blade-click-new.png)
    ![SelectDW](../../includes/media/sql-data-warehouse-create-dw/blade-select-dw.png)

3. Preencha os detalhes da implementação

    **Nome da Base de Dados**: escolha o nome que quiser. Se tiver várias instâncias do SQL DW, recomendamos que os nomes incluam detalhes como a região, o ambiente, etc., por exemplo, *omeudw-euaoe-1-teste*

    **Subscrição**: a sua subscrição do Azure

    **Grupo de Recursos**: crie um novo (ou utilize um já existente se pretender utilizar o SQL Data Warehouse com outros serviços)
    > [!NOTE]
    > Os serviços que pertençam a um grupo de recursos devem ter o mesmo ciclo de vida. Os grupos de recursos são úteis para a administração de recursos, como delimitar o âmbito do controlo de acesso e a implementação baseada em modelos. Leia mais sobre os grupos de recursos do Azure e as melhores práticas [aqui](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview#resource-groups).
    >

    **Origem**: base de dados vazia

    **Servidor**: selecione o servidor que criou em [Pré-requisitos].

    **Agrupamento**: deixe o agrupamento predefinido, SQL_Latin1_General_CP1_CI_AS

    **Selecionar o desempenho**: recomendamos manter as 400 DWU.

4. Escolha **Afixar ao dashboard**
    ![Afixar ao Dashboard](./media/sql-data-warehouse-get-started-tutorial/pin-to-dashboard.png)

5. Relaxe e aguarde pela implementação do Azure SQL Data Warehouse! É normal que este processo demore alguns minutos. O portal notificá-lo-á da conclusão da implementação da sua instância. 

## <a name="connect-to-azure-sql-data-warehouse-through-sql-server-logical-server"></a>Ligar ao Azure SQL Data Warehouse através do SQL Server (servidor lógico)

Este tutorial utiliza o SQL Server Management Studio para ligar ao nosso SQL Data Warehouse. Podem ser utilizadas outras ferramentas para ligar ao SQL Data Warehouse através dos nossos conectores suportados, ADO.NET, JDBC, ODBC e PHP. Tenha em atenção que a funcionalidade das ferramentas não suportadas pela Microsoft pode ser limitada.


### <a name="get-connection-information"></a>Obter informações da ligação

Para ligar ao seu SQL Data Warehouse, tem de se ligar através do SQL Server (servidor lógico) que criou em [Pré-requisitos].

1. Selecione o SQL Data Warehouse no dashboard ou procure-o nos seus recursos.

    ![Dashboard do SQL Data Warehouse](./media/sql-data-warehouse-get-started-tutorial/sql-dw-dashboard.png)

2. Localize o nome completo do servidor lógico.

    ![Selecionar Nome do Servidor](./media/sql-data-warehouse-get-started-tutorial/select-server.png)

3. Abra o SSMS e utilize o Object Explorer para ligar a este servidor com as credenciais criadas em [Pré-requisitos].

    ![Ligar com o SSMS](./media/sql-data-warehouse-get-started-tutorial/ssms-connect.png)

Se tudo correr bem, deverá estar agora ligado à sua instância do SQL Server (servidor lógico). Pode utilizar as credenciais do servidor para se autenticar em qualquer base de dados no servidor como o proprietário da mesma. No entanto, como melhor prática, deve criar inícios de sessão e utilizadores separados para cada base de dados. Vamos explorar a criação de utilizadores em [Criar Utilizadores para o SQL Data Warehouse](./sql-data-warehouse-get-started-tutorial.md#create-a-user-for-sql-data-warehouse). 

## <a name="create-a-user-for-sql-data-warehouse"></a>Criar Utilizadores para o SQL Data Warehouse

### <a name="why-create-a-separate-user"></a>Porquê criar um utilizador em separado?

Para criar um utilizador novo para o nosso SQL Data Warehouse, utilizamos a ligação ao SQL Server (servidor lógico), com as credenciais do servidor do passo anterior. Há dois motivos principais que recomendam a criação de utilizadores/inícios de sessão separados para o SQL DW.

1.  Os utilizadores da sua organização devem utilizar uma conta diferente para autenticar. Desta forma, pode limitar as permissões concedidas à aplicação e reduzir os riscos de atividades maliciosas.

2. Por predefinição, o início de sessão do administrador de servidor ao qual está atualmente ligado utiliza uma classe de recursos mais pequena. As classes de recursos ajudam-no a controlar a alocação de memória e os ciclos de CPU dados a uma consulta. Os utilizadores em **smallrc** recebem uma quantidade de memória mais pequena e podem tirar partido de simultaneidade superior. Em contrapartida, os utilizadores atribuídos a **xlargerc** recebem grandes quantidades de memória e, por conseguinte, menos consultas deles podem ser executadas em simultâneo. Para carregar os dados de uma forma que otimize a compressão, é útil confirmar que o utilizador que está a carregar dados faz parte de uma classe de recursos maior. Leia mais sobre as classes de recursos [aqui](./sql-data-warehouse-develop-concurrency.md#resource-classes).

### <a name="creating-a-user-of-a-larger-resource-class"></a>Criar um utilizador de uma classe de recursos maior

1. Consulte a base de dados **mestra** do seu servidor

    ![Nova Consulta na Base de Dados Mestra](./media/sql-data-warehouse-get-started-tutorial/query-on-server.png)

    ![Nova Consulta na Base de Dados Mestra1](./media/sql-data-warehouse-get-started-tutorial/query-on-master.png)

2. Crie um início de sessão do servidor e um utilizador

    ```sql
    CREATE LOGIN XLRCLOGIN WITH PASSWORD = 'a123reallySTRONGpassword!';
    CREATE USER LoadingUser FOR LOGIN XLRCLOGIN;
    ```

3. Consultando a base de dados SQL DataWarehouse, crie um utilizador da base de dados novo com base no início de sessão do servidor 
    ```sql
    CREATE USER LoadingUser FOR LOGIN XLRCLOGIN;
    ```

4. Conceder controlo da DB ao utilizador
    ```sql
    GRANT CONTROL ON DATABASE::[NYT] to LoadingUser;
    ```
    > [!NOTE]
    > Se o seu nome de base de dados tiver hífenes, confirme que os coloca entre parêntesis. 
    >

5. Adicione o utilizador da base de dados à função de classe de recursos **xlargerc**.
    ```sql
    EXEC sp_addrolemember 'xlargerc', 'LoadingUser';
    ```

6. Inicie sessão na sua base de dados com as credenciais novas

    ![Inicie Sessão com o Início de Sessão Novo](./media/sql-data-warehouse-get-started-tutorial/new-login.png)


## <a name="loading-data"></a>Carregar dados

### <a name="defining-external-data"></a>Definir dados externos
1. Crie uma Chave Mestra e defina uma origem de dados externa

    ```sql
    CREATE MASTER KEY;

    CREATE EXTERNAL DATA SOURCE NYTPublic
    WITH
    (
    TYPE = Hadoop
    , LOCATION = 'wasbs://2013@nytpublic.blob.core.windows.net/'
    );
    ```


2. Defina os formatos dos ficheiros externos

    O comando ```CREATE EXTERNAL FILE FORMAT``` é utilizado para especificar o formato dos dados externos que está a carregar. Para os dados relativos a táxis de Nova Iorque, utilizámos os dois formatos para armazenar os dados no Armazenamento de Blobs do Azure

    ```sql
    CREATE EXTERNAL FILE FORMAT uncompressedcsv
    WITH
    ( FORMAT_TYPE = DELIMITEDTEXT
    , FORMAT_OPTIONS ( FIELD_TERMINATOR = ','
    , STRING_DELIMITER = ''
    , DATE_FORMAT = ''
    , USE_TYPE_DEFAULT = False
    )
    );

    CREATE EXTERNAL FILE FORMAT compressedcsv
    WITH
    ( FORMAT_TYPE = DELIMITEDTEXT
    , FORMAT_OPTIONS ( FIELD_TERMINATOR = '|'
    , STRING_DELIMITER = ''
    , DATE_FORMAT = ''
    , USE_TYPE_DEFAULT = False
    )
    , DATA_COMPRESSION = 'org.apache.hadoop.io.compress.GzipCodec'
    );
    ```

3.  Crie um esquema para o formato de ficheiro externo

    ```sql
    CREATE SCHEMA ext;
    GO
    ```

4. Crie as tabelas externas. Estas tabelas referenciam dados armazenados no HDFS ou no Armazenamento de Blobs do Azure. 

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
    LOCATION = 'Date'
    , DATA_SOURCE = NYTPublic
    , FILE_FORMAT = uncompressedcsv
    , REJECT_TYPE = value
    , REJECT_VALUE = 0
    )
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
    LOCATION = 'Geography'
    , DATA_SOURCE = NYTPublic
    , FILE_FORMAT = uncompressedcsv
    , REJECT_TYPE = value
    , REJECT_VALUE = 0 
    )
    ;
    CREATE EXTERNAL TABLE [ext].[HackneyLicense]
    (
    [HackneyLicenseID] int NOT NULL,
    [HackneyLicenseBKey] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NOT NULL,
    [HackneyLicenseCode] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NULL
    )
    WITH
    (
    LOCATION = 'HackneyLicense'
    , DATA_SOURCE = NYTPublic
    , FILE_FORMAT = uncompressedcsv
    , REJECT_TYPE = value
    , REJECT_VALUE = 0
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
    LOCATION = 'Medallion'
    , DATA_SOURCE = NYTPublic
    , FILE_FORMAT = uncompressedcsv
    , REJECT_TYPE = value
    , REJECT_VALUE = 0
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
    LOCATION = 'Time'
    , DATA_SOURCE = NYTPublic
    , FILE_FORMAT = uncompressedcsv
    , REJECT_TYPE = value
    , REJECT_VALUE = 0
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
    LOCATION = 'Trip2013'
    , DATA_SOURCE = NYTPublic
    , FILE_FORMAT = compressedcsv
    , REJECT_TYPE = value
    , REJECT_VALUE = 0
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
    LOCATION = 'Weather2013'
    , DATA_SOURCE = NYTPublic
    , FILE_FORMAT = uncompressedcsv
    , REJECT_TYPE = value
    , REJECT_VALUE = 0
    )
    ;
    ```

### <a name="create-table-as-select-ctas"></a>Criar Tabela como Select (CTAS)

5. Carregue os dados a partir das tabelas externas para a sua instância do SQL Data Warehouse. 
    ```sql
    CREATE TABLE [dbo].[Date]
    WITH
    ( DISTRIBUTION = ROUND_ROBIN
    , CLUSTERED COLUMNSTORE INDEX
    )
    AS
    SELECT *
    FROM [ext].[Date]
    OPTION (LABEL = 'CTAS : Load [dbo].[Date]')
    ;
    CREATE TABLE [dbo].[Geography]
    WITH
    ( DISTRIBUTION = ROUND_ROBIN
    , CLUSTERED COLUMNSTORE INDEX
    )
    AS
    SELECT *
    FROM [ext].[Geography]
    OPTION (LABEL = 'CTAS : Load [dbo].[Geography]')
    ;
    CREATE TABLE [dbo].[HackneyLicense]
    WITH
    ( DISTRIBUTION = ROUND_ROBIN
    , CLUSTERED COLUMNSTORE INDEX
    )
    AS
    SELECT *
    FROM [ext].[HackneyLicense]
    OPTION (LABEL = 'CTAS : Load [dbo].[HackneyLicense]')
    ;
    CREATE TABLE [dbo].[Medallion]
    WITH
    ( DISTRIBUTION = ROUND_ROBIN
    , CLUSTERED COLUMNSTORE INDEX
    )
    AS
    SELECT *
    FROM [ext].[Medallion]
    OPTION (LABEL = 'CTAS : Load [dbo].[Medallion]')
    ;
    CREATE TABLE [dbo].[Time]
    WITH
    ( DISTRIBUTION = ROUND_ROBIN
    , CLUSTERED COLUMNSTORE INDEX
    )
    AS
    SELECT *
    FROM [ext].[Time]
    OPTION (LABEL = 'CTAS : Load [dbo].[Time]')
    ;
    CREATE TABLE [dbo].[Weather]
    WITH
    ( DISTRIBUTION = ROUND_ROBIN
    , CLUSTERED COLUMNSTORE INDEX
    )
    AS
    SELECT *
    FROM [ext].[Weather]
    OPTION (LABEL = 'CTAS : Load [dbo].[Weather]')
    ;
    CREATE TABLE [dbo].[Trip]
    WITH
    ( DISTRIBUTION = ROUND_ROBIN
    , CLUSTERED COLUMNSTORE INDEX
    )
    AS
    SELECT *
    FROM [ext].[Trip]
    OPTION (LABEL = 'CTAS : Load [dbo].[Trip]')
    ;
    ```

    > [!NOTE]
    > Está a carregar vários GBs de dados e a comprimi-los em Índices Columnstore em Cluster de elevado desempenho. Execute a consulta DMV seguinte e faça uma pausa enquanto o Azure SQL Data Warehouse faz o trabalho pesado.
    >

6. Crie uma consulta nova e veja os seus dados a aparecer com esta vista de gestão dinâmica (Dynamic Management View, DMV)

    ```sql
    SELECT
    r.command,
    s.request_id,
    r.status,
    count(distinct input_name) as nbr_files,
    sum(s.bytes_processed)/1024/1024 as gb_processed
    FROM
    sys.dm_pdw_exec_requests r
    inner join sys.dm_pdw_dms_external_work s
    on r.request_id = s.request_id
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
    nbr_files desc, gb_processed desc;
    ```

7. Veja todas as consultas de sistema

    ```sql
    SELECT * FROM sys.dm_pdw_exec_requests;
    ```

8. Veja os seus dados devidamente carregados para o SQL Data Warehouse e deleite-se.

    ![Ver os Dados Carregados](./media/sql-data-warehouse-get-started-tutorial/see-data-loaded.png)



## <a name="querying-data"></a>Consultar dados 

### <a name="scan-query-with-scaling"></a>Analisar a consulta com o dimensionamento

Vamos ter uma ideia clara de como o dimensionamento afeta a velocidade das suas consultas.

Antes de começar, vamos dimensionar a nossa operação para 100 DWUs, para termos uma ideia de como o nó de computação se vai comportar sozinho.

1. Aceda ao portal e selecione a sua instância do SQL Data Warehouse.

2. Selecione Dimensionamento no painel do SQL Data Warehouse. 

    ![Portal Dimensionar DW a Partir de](./media/sql-data-warehouse-get-started-tutorial/scale-dw.png)

3. Dimensione a barra de desempenho para 100 DWUs e clique em Guardar.

    ![Dimensionar e guardar](./media/sql-data-warehouse-get-started-tutorial/scale-and-save.png)

4. Aguarde pela conclusão da operação de dimensionamento.

    > [!NOTE]
    > Tenha em atenção que o dimensionamento de operações **mata** as consultas que estejam a ser executadas atualmente e impede a execução de novas.
    >
    
5. Faça uma operação de análise aos dados das viagens, selecionando o primeiro milhão de entradas de todas as colunas. Se quiser avançar mais depressa, pode selecionar menos linhas.

    ```sql
    SELECT TOP(1000000) * FROM dbo.[Trip]
    ```

Aponte o tempo que a execução desta operação demorou.

6. Aumente verticalmente a sua instância para 400 DWUs. Não se esqueça de que cada 100 DWUs acrescentam outro nó de computação ao seu Azure SQL Data Warehouse.

7. Execute a consulta outra vez! Deverá notar uma grande diferença. 

> [!NOTE]
> O Azure SQL Data Warehouse é uma plataforma de Processamento Paralelo em Massa (Massively Parallel Processing, MPP). As consultas e as operações que podem paralelizar trabalhos em vários nós beneficiam do verdadeiro poder do Azure SQL Data Warehouse.
>

### <a name="join-query-with-statistics"></a>Consulta de associação a estatísticas

1. Execute uma consulta que associada a tabela Dados à tabela Viagem

    ```sql
    SELECT TOP (1000000) dt.[DayOfWeek]
    ,tr.[MedallionID]
    ,tr.[HackneyLicenseID]
    ,tr.[PickupTimeID]
    ,tr.[DropoffTimeID]
    ,tr.[PickupGeographyID]
    ,tr.[DropoffGeographyID]
    ,tr.[PickupLatitude]
    ,tr.[PickupLongitude]
    ,tr.[PickupLatLong]
    ,tr.[DropoffLatitude]
    ,tr.[DropoffLongitude]
    ,tr.[DropoffLatLong]
    ,tr.[PassengerCount]
    ,tr.[TripDurationSeconds]
    ,tr.[TripDistanceMiles]
    ,tr.[PaymentType]
    ,tr.[FareAmount]
    ,tr.[SurchargeAmount]
    ,tr.[TaxAmount]
    ,tr.[TipAmount]
    ,tr.[TollsAmount]
    ,tr.[TotalAmount]
    FROM [dbo].[Trip] as tr
    join
    dbo.[Date] as dt
    on tr.DateID = dt.DateID
    ```

    Como seria de esperar, a consulta demora muito mais tempo quando mistura dados entre os nós, especialmente num cenário de associação, como é o caso desta consulta.

2. Vamos ver de que forma é que esta consulta é alterada quando criamos estatísticas na coluna que vamos adicionar, executando, para tal, o seguinte:

    ```sql
    CREATE STATISTICS [dbo.Date DateID stats] ON dbo.Date (DateID);
    CREATE STATISTICS [dbo.Trip DateID stats] ON dbo.Trip (DateID);
    ```

    > [!NOTE]
    > O SQL DW não gere as estatísticas automaticamente por si. Estas são importantes para o desempenho da consulta e é vivamente recomendado que as crie e atualize.
    > 
    > **Vai beneficiar mais com estatísticas em colunas envolvidas em associações, colunas utilizadas na cláusula WHERE e colunas que se encontram em GROUP BY.**
    >

3. Execute novamente a consulta dos Pré-requisitos e repare nas diferenças no desempenho. Embora as diferenças no desempenho da consulta não sejam tão significativas como no aumento vertical, deverá notar uma aceleração percetível. 

## <a name="next-steps"></a>Passos seguintes

Está agora pronto para consultar e explorar. Veja as nossas melhores práticas ou sugestões.

Se já tiver explorado tudo por hoje, não se esqueça de colocar a sua instância em pausa! Em produção, pode beneficiar de enormes poupanças ao colocar em pausa e dimensionar de forma a satisfazer as necessidades da sua atividade.

![Colocar em pausa](./media/sql-data-warehouse-get-started-tutorial/pause.png)

## <a name="useful-readings"></a>Leituras úteis

[Concurrency and Workload Management (Simultaneidade e Gestão de Cargas de Trabalho)]

[Melhores práticos do SQL Data Warehouse]

[Query Monitoring (Monitorização de Consultas)]

[Top 10 Best Practices for Building a Large Scale Relational Data Warehouse (As Dez Melhores Práticas para Criar um Data Warehouse Relacional de Grande Escala)]

[Migrating Data to Azure SQL Data Warehouse (Migrar Dados para o Azure SQL Data Warehouse)]


[Concurrency and Workload Management (Simultaneidade e Gestão de Cargas de Trabalho)]: sql-data-warehouse-develop-concurrency.md#change-a-user-resource-class-example
[Melhores práticos do SQL Data Warehouse]: sql-data-warehouse-best-practices.md#hash-distribute-large-tables
[Query Monitoring (Monitorização de Consultas)]: sql-data-warehouse-manage-monitor.md
[Top 10 Best Practices for Building a Large Scale Relational Data Warehouse (As Dez Melhores Práticas para Criar um Data Warehouse Relacional de Grande Escala)]: https://blogs.msdn.microsoft.com/sqlcat/2013/09/16/top-10-best-practices-for-building-a-large-scale-relational-data-warehouse/
[Migrating Data to Azure SQL Data Warehouse (Migrar Dados para o Azure SQL Data Warehouse)]: https://blogs.msdn.microsoft.com/sqlcat/2016/08/18/migrating-data-to-azure-sql-data-warehouse-in-practice/



[!INCLUDE [Additional Resources](../../includes/sql-data-warehouse-article-footer.md)]

<!-- Internal Links -->
[Pré-requisitos]: sql-data-warehouse-get-started-tutorial.md#prerequisites

<!--Other Web references-->
[Visual Studio]: https://www.visualstudio.com/
[SQL Server Management Studio]: https://msdn.microsoft.com/en-us/library/mt238290.aspx



<!--HONumber=Jan17_HO3-->


