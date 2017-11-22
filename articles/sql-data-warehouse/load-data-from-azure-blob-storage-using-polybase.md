---
title: Carregamento de dados do Polybase - Blob de armazenamento do Azure ao Azure SQL Data Warehouse | Microsoft Docs
description: Um tutorial que utiliza o portal do Azure e o SQL Server Management Studio para carregar dados de Nova Iorque Taxicab do blob storage do Azure ao Azure SQL Data Warehouse.
services: sql-data-warehouse
documentationcenter: 
author: ckarst
manager: jhubbard
editor: 
tags: 
ms.assetid: 
ms.service: sql-data-warehouse
ms.custom: mvc,develop data warehouses
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: Active
ms.date: 11/17/2017
ms.author: cakarst
ms.reviewer: barbkess
ms.openlocfilehash: fe3ea6c22fafad0d0dcf611ceb365a2ebca80011
ms.sourcegitcommit: 4ea06f52af0a8799561125497f2c2d28db7818e7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/21/2017
---
# <a name="use-polybase-to-load-data-from-azure-blob-storage-to-azure-sql-data-warehouse"></a>Utilize o PolyBase para carregar dados do blob storage do Azure ao Azure SQL Data Warehouse

O PolyBase é a norma ao carregar a tecnologia de obtenção de dados para o SQL Data Warehouse. Neste tutorial, utilize o PolyBase para carregar dados de Nova Iorque Taxicab do blob storage do Azure ao Azure SQL Data Warehouse. O tutorial utiliza o [portal do Azure](https://portal.azure.com) e [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms.md) (SSMS) para: 

> [!div class="checklist"]
> * Criar um armazém de dados no portal do Azure
> * Configurar uma regra de firewall ao nível do servidor no portal do Azure
> * Ligar ao armazém de dados com o SSMS
> * Criar um utilizador designado para carregar dados
> * Criar tabelas externas para dados no armazenamento de Blobs do Azure
> * Utilize a instrução de CTAS T-SQL para carregar dados para o seu armazém de dados
> * Ver o progresso dos dados que está a carregar
> * Criar estatísticas nos dados recentemente carregados

Se não tiver uma subscrição do Azure, [criar uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="before-you-begin"></a>Antes de começar

Antes de começar este tutorial, transfira e instale a versão mais recente do [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms.md) (SSMS).


## <a name="log-in-to-the-azure-portal"></a>Iniciar sessão no portal do Azure

Inicie sessão no [Portal do Azure](https://portal.azure.com/).

## <a name="create-a-blank-sql-data-warehouse"></a>Criar um SQL data warehouse em branco

Um armazém de dados SQL do Azure é criado com um conjunto definido de [recursos de computação](performance-tiers.md). A base de dados é criado dentro de um [grupo de recursos do Azure](../azure-resource-manager/resource-group-overview.md) e num [servidor lógico SQL do Azure](../sql-database/sql-database-features.md). 

Siga estes passos para criar um SQL data warehouse em branco. 

1. Clique em de **novo** botão no canto superior esquerdo do portal do Azure.

2. Selecione **bases de dados** do **novo** página e selecione **SQL Data Warehouse** em **em destaque** no **novo**página.

    ![criar o armazém de dados](media/load-data-from-azure-blob-storage-using-polybase/create-empty-data-warehouse.png)

3. Preencha o formulário de armazém de dados do SQL Server com as seguintes informações:   

   | Definição | Valor sugerido | Descrição | 
   | ------- | --------------- | ----------- | 
   | **Nome da base de dados** | mySampleDataWarehouse | Para nomes de bases de dados válidos, veja [Database Identifiers](/sql/relational-databases/databases/database-identifiers) (Identificadores de Bases de Dados). | 
   | **Subscrição** | A sua subscrição  | Para obter detalhes sobre as suas subscrições, veja [Subscriptions](https://account.windowsazure.com/Subscriptions) (Subscrições). |
   | **Grupo de recursos** | myResourceGroup | Para nomes de grupo de recursos válidos, veja [Naming rules and restrictions](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions) (Atribuição de nomes de regras e restrições). |
   | **Selecione a origem** | Base de dados em branco | Especifica para criar uma base de dados em branco. Tenha em atenção de que o armazém de dados é um tipo de base de dados.|

    ![criar o armazém de dados](media/load-data-from-azure-blob-storage-using-polybase/create-data-warehouse.png)

4. Clique em **Servidor** para criar e configurar um novo servidor para a nova base de dados. Preencha o **novo formulário de servidor** com as seguintes informações: 

    | Definição | Valor sugerido | Descrição | 
    | ------- | --------------- | ----------- |
    | **Nome do servidor** | Qualquer nome globalmente exclusivo | Para nomes de servidores válidos, veja [Naming rules and restrictions](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions) (Atribuição de nomes de regras e restrições). | 
    | **Início de sessão de administrador do servidor** | Qualquer nome válido | Para nomes de início de sessão válidos, veja [Database Identifiers](https://docs.microsoft.com/sql/relational-databases/databases/database-identifiers) (Identificadores de Bases de Dados).|
    | **Palavra-passe** | Qualquer palavra-passe válida | A palavra-passe tem de ter, pelo menos, oito carateres e tem de conter carateres de três das seguintes categorias: carateres maiúsculos, carateres minúsculos, números e carateres não alfanuméricos. |
    | **Localização** | Nenhuma localização válida | Para obter mais informações sobre regiões, veja [Azure Regions](https://azure.microsoft.com/regions/) (Regiões do Azure). |

    ![criar o servidor de base de dados](media/load-data-from-azure-blob-storage-using-polybase/create-database-server.png)

5. Clique em **Selecionar**.

6. Clique em **camada de desempenho** para especificar se o armazém de dados está otimizado para elasticidade ou de computação e o número de dados do armazém de unidades. 

7. Para este tutorial, selecione o **otimizado para elasticidade** camada de serviço. O controlo de deslize, por predefinição, está definido como **DW400**.  Tente movê-los e reduzir verticalmente para ver como funciona. 

    ![configurar o desempenho](media/load-data-from-azure-blob-storage-using-polybase/configure-performance.png)

8. Clique em **Aplicar**.
9. Na página do armazém de dados do SQL Server, selecione um **agrupamento** para a base de dados em branco. Para este tutorial, utilize o valor predefinido. Para obter mais informações sobre agrupamentos, consulte [agrupamentos](/sql/t-sql/statements/collations.md)

11. Agora que concluiu o formulário da Base de Dados SQL do Servidor, clique em **Criar** para aprovisionar a base de dados. O aprovisionamento demora alguns minutos. 

    ![Clique em criar](media/load-data-from-azure-blob-storage-using-polybase/click-create.png)

12. Na barra de ferramentas, clique em **Notificações** para monitorizar o processo de implementação.
    
     ![notificação](media/load-data-from-azure-blob-storage-using-polybase/notification.png)

## <a name="create-a-server-level-firewall-rule"></a>Criar uma regra de firewall ao nível do servidor

O serviço do SQL Data Warehouse cria uma firewall ao nível do servidor-que impede o ligar ao servidor ou bases de dados no servidor de aplicações externas e as ferramentas. Para ativar a conetividade, pode adicionar regras de firewall que permitem uma conectividade de endereços IP específicos.  Siga estes passos para criar um [regra de firewall ao nível do servidor](../sql-database/sql-database-firewall-configure.md) para o endereço IP do cliente. 

> [!NOTE]
> O SQL Data Warehouse comunica através da porta é 1433. Se estiver a tentar ligar a partir de uma rede empresarial, o tráfego de saída através da porta 1433 não poderá permitido pela firewall da sua rede. Se assim for, não poderá ligar ao servidor da Base de Dados SQL do Azure, a menos que o departamento de TI abra a porta 1433.
>

1. Depois de concluída a implementação, clique em **Bases de dados SQL** no menu do lado esquerdo e, em seguida, clique em **mySampleDatabase** na página **Bases de dados SQL**. Abre a página de descrição geral da base de dados, que mostra o nome de servidor completamente qualificado (tais como **mynewserver 20171113.database.windows.net**) e fornece opções para continuar a configuração. 

2. Copie este nome de servidor totalmente qualificado para utilizar para ligar ao seu servidor e às respetivas bases de dados nos seguintes guias de introdução. Em seguida, clique no nome do servidor para abrir as definições do servidor.

    ![localizar o nome do servidor](media/load-data-from-azure-blob-storage-using-polybase/find-server-name.png) 

3. Clique no nome de servidor para abrir as definições do servidor.

    ![definições do servidor](media/load-data-from-azure-blob-storage-using-polybase/server-settings.png) 

5. Clique em **mostrar as definições da firewall**. É aberta a página **Definições da firewall** do servidor da Base de Dados SQL. 

    ![regra de firewall do servidor](media/load-data-from-azure-blob-storage-using-polybase/server-firewall-rule.png) 

4. Clique em **Adicionar IP de cliente**, na barra de ferramentas, para adicionar o seu endereço IP atual a uma nova regra de firewall. Uma regra de firewall consegue abrir a porta 1433 para um único endereço IP ou para um intervalo de endereços IP.

5. Clique em **Guardar**. É criada uma regra de firewall ao nível do servidor para a sua porta de abertura 1433 do endereço IP atual no servidor lógico.

6. Clique em **OK** e, em seguida, feche a página **Definições da firewall**.

Agora pode ligar ao SQL server e o respetivos armazéns de dados com este endereço IP. A ligação funciona do SQL Server Management Studio ou outra ferramenta de sua escolha. Quando se liga, utilize a conta de ServerAdmin que criou anteriormente.  

> [!IMPORTANT]
> Por predefinição, o acesso através da firewall da Base de Dados SQL está ativado para todos os serviços do Azure. Clique em **OFF** nesta página e, em seguida, clique em **guardar** para desativar a firewall para todos os serviços do Azure.

## <a name="get-the-fully-qualified-server-name"></a>Obter o nome de servidor completamente qualificado

Obter o nome de servidor completamente qualificado para o SQL server no portal do Azure. Mais tarde irá utilizar o nome completamente qualificado ao ligar ao servidor.

1. Inicie sessão no [Portal do Azure](https://portal.azure.com/).
2. Selecione **Bases de Dados SQL** a partir do menu do lado esquerdo e clique na sua base de dados na página **Bases de Dados SQL**. 
3. No painel **Essentials** na página do portal do Azure da sua base de dados, localize e, em seguida, copie o **Nome do servidor**. Neste exemplo, o nome completamente qualificado é mynewserver 20171113.database.windows.net. 

    ![informações da ligação](media/load-data-from-azure-blob-storage-using-polybase/find-server-name.png)  

## <a name="connect-to-the-server-as-server-admin"></a>Ligar ao servidor como administrador do servidor

Esta secção utiliza [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms.md) (SSMS) para estabelecer uma ligação ao servidor SQL do Azure.

1. Abra o SQL Server Management Studio.

2. Na caixa de dialogo **Ligar ao Servidor**, introduza as seguintes informações:

    | Definição      | Valor sugerido | Descrição | 
    | ------------ | --------------- | ----------- | 
    | Tipo de servidor | Motor de base de dados | Este valor é necessário |
    | Nome do servidor | O nome de servidor completamente qualificado | O nome deve ser algo semelhante ao seguinte: **mynewserver 20171113.database.windows.net**. |
    | Autenticação | Autenticação do SQL Server | A Autenticação do SQL é o único tipo de autenticação que configurámos neste tutorial. |
    | Iniciar sessão | A conta de administrador do servidor | Esta é a conta que especificou quando criou o servidor. |
    | Palavra-passe | A palavra-passe da sua conta de administrador do servidor | Esta é a palavra-passe que especificou quando criou o servidor. |

    ![ligar ao servidor](media/load-data-from-azure-blob-storage-using-polybase/connect-to-server.png)

4. Clique em **Ligar**. A janela do Object Explorer é aberta no SSMS. 

5. No Object Explorer, expanda **bases de dados**. Em seguida, expanda **bases de dados do sistema** e **mestre** para ver os objetos na base de dados mestra.  Expanda **mySampleDatabase** para ver os objetos na sua nova base de dados.

    ![Objetos de base de dados](media/load-data-from-azure-blob-storage-using-polybase/connected.png) 

## <a name="create-a-user-for-loading-data"></a>Criar um utilizador para carregar dados

A conta de administrador do servidor destina-se para efetuar operações de gestão e não é adequada para executar consultas em dados do utilizador. Carregar dados, normalmente, necessita de muita de memória. [Valores máximos de memória](performance-tiers.md#memory-maximums) estão definidas de acordo com [camada de desempenho](performance-tiers.md), e [classe de recursos](resource-classes-for-workload-management.md). 

É melhor criar um início de sessão e o utilizador que está dedicado para carregar dados. Em seguida, adicione o utilizador de carregamento para uma [classe de recursos](resource-classes-for-workload-management.md) que permite que uma alocação de memória máxima adequado.

Uma vez que estiver atualmente ligado como administrador do servidor, pode criar utilizadores e os inícios de sessão. Utilize estes passos para criar um início de sessão e o utilizador chamado **LoaderRC20**. Em seguida, atribua o utilizador a **staticrc20** classe de recursos. 

1.  No SSMS, faça duplo clique **mestre** para mostrar um menu pendente e escolha **nova consulta**. É aberta uma nova janela de consulta.

    ![Nova consulta no principal](media/load-data-from-azure-blob-storage-using-polybase/create-loader-login.png)

2. Na janela da consulta, introduza estes comandos de T-SQL para criar um início de sessão e o utilizador com o nome LoaderRC20, substituindo a sua própria palavra-passe para 'a123STRONGpassword!'. 

    ```sql
    CREATE LOGIN LoaderRC20 WITH PASSWORD = 'a123STRONGpassword!';
    CREATE USER LoaderRC20 FOR LOGIN LoaderRC20;
    ```

3. Clique em **Executar**.

4. Clique com botão direito **mySampleDataWarehouse**e escolha **nova consulta**. Uma nova consulta é aberta a janela.  

    ![Nova consulta no armazém de dados de exemplo](media/load-data-from-azure-blob-storage-using-polybase/create-loading-user.png)
 
5. Introduza os seguintes comandos de T-SQL para criar um utilizador de base de dados com o nome LoaderRC20 para o início de sessão LoaderRC20. A segunda linha atribui o novo utilizador permissões de controlo no armazém de dados de novo.  Estas permissões são semelhantes para tornar o utilizador o proprietário da base de dados. A terceira linha adiciona o novo utilizador como um membro do staticrc20 [classe de recursos](resource-classes-for-workload-management.md).

    ```sql
    CREATE USER LoaderRC20 FOR LOGIN LoaderRC20;
    GRANT CONTROL ON DATABASE::[mySampleDataWarehouse] to LoaderRC20;
    EXEC sp_addrolemember 'staticrc20', 'LoaderRC20';
    ```

6. Clique em **Executar**.

## <a name="connect-to-the-server-as-the-loading-user"></a>Ligar ao servidor, como o utilizador de carregamento

O primeiro passo na direção de carregamento de dados está a iniciar sessão como LoaderRC20.  

1. No Object Explorer, clique o **Connect** pendente menu e selecione **motor de base de dados**. O **ligar ao servidor** é apresentada a caixa de diálogo.

    ![Estabelecer ligação com o novo início de sessão](media/load-data-from-azure-blob-storage-using-polybase/connect-as-loading-user.png)

2. Introduza o nome de servidor completamente qualificado, mas desta vez de introduzir **LoaderRC20** como o início de sessão.  Introduza a palavra-passe para LoaderRC20.

3. Clique em **Ligar**.

4. Quando a ligação estiver pronta, verá duas ligações de servidor no Object Explorer. Uma ligação como ServerAdmin e uma ligação como MedRCLogin.

    ![Ligação é efetuada com êxito](media/load-data-from-azure-blob-storage-using-polybase/connected-as-new-login.png)

## <a name="create-external-tables-for-the-sample-data"></a>Criar tabelas externas para os dados de exemplo

Está pronto para iniciar o processo de carregamento de dados para o armazém de dados de novo. Este tutorial mostra como utilizar [Polybase](/sql/relational-databases/polybase/polybase-guide.md) para carregar dados de ficheiro cab de taxi de Nova Iorque cidade de um blob de armazenamento do Azure. Para referência futura e para saber como colocar os seus dados no armazenamento de blobs do Azure ou carregá-los diretamente da sua origem para o SQL Data Warehouse,veja a [descrição geral do carregamento](sql-data-warehouse-overview-load.md).

Executar o SQL seguinte scripts especificam informações sobre os dados que pretende carregar. Estas informações incluem onde os dados se encontra, o formato do conteúdo dos dados e a definição da tabela de dados. 

1. Na secção anterior, é registado para o armazém de dados como LoaderRC20. No SSMS, a ligação de LoaderRC20 com o botão direito e selecione **nova consulta**.  É apresentada uma nova janela de consulta. 

    ![Nova ao carregar a janela de consulta](media/load-data-from-azure-blob-storage-using-polybase/new-loading-query.png)

2. Compare a janela de consulta para a imagem anterior.  Certifique-se de que a nova janela de consulta está em execução como LoaderRC20 e efetuar consultas na base de dados MySampleDataWarehouse. Utilize esta janela de consulta para executar todos os passos de carregamento.

3. Crie uma chave mestra da base de dados MySampleDataWarehouse. Só tem de criar uma chave mestra uma vez por base de dados. 

    ```sql
    CREATE MASTER KEY;
    ```

4. Execute o seguinte [criar origem de dados externa](/sql/t-sql/statements/create-external-data-source-transact-sql.md) instrução para definir a localização do blob do Azure. Esta é a localização dos dados de ficheiro cab externo taxi.  Para executar um comando que tem acrescentado para a janela de consulta, realce os comandos que pretende executar e clique em **executar**.

    ```sql
    CREATE EXTERNAL DATA SOURCE NYTPublic
    WITH
    (
        TYPE = Hadoop,
        LOCATION = 'wasbs://2013@nytaxiblob.blob.core.windows.net/'
    );
    ```

5. Execute o seguinte [criar formato de ficheiro externo](/sql/t-sql/statements/create-external-file-format-transact-sql.md) instrução de T-SQL para especificar características de formatação e as opções para o ficheiro de dados externas. Esta declaração Especifica os dados externos são armazenados como texto e os valores são separados pelo pipe ('| ') caráter. O ficheiro externo é comprimido com Gzip. 

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

6.  Execute o seguinte [CREATE SCHEMA](/sql/t-sql/statements/create-schema-transact-sql.md) instrução para criar um esquema para o formato de ficheiro externo. O esquema é uma forma para organizar as tabelas externas que está prestes a criar.

    ```sql
    CREATE SCHEMA ext;
    ```

7. Crie as tabelas externas. A tabela as definições são armazenadas no SQL Data Warehouse, mas as tabelas de referenciam a dados armazenados no blob storage do Azure. Execute os comandos T-SQL seguintes para criar várias tabelas externas que apontem para o blob do Azure que definimos anteriormente na origem de dados externa.

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
    );
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
    );
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
    );
    CREATE EXTERNAL TABLE [ext].[Weather]
    (
        [DateID] int NOT NULL,
        [GeographyID] int NOT NULL,
        [PrecipitationInches] float NOT NULL,
        [AvgTemperatureFahrenheit] float NOT NULL
    )
    WITH
    (
        LOCATION = 'Weather',
        DATA_SOURCE = NYTPublic,
        FILE_FORMAT = uncompressedcsv,
        REJECT_TYPE = value,
        REJECT_VALUE = 0
    )
    ;
    ```

8. No Object Explorer, expanda mySampleDataWarehouse para ver a lista de tabelas externas que acabou de criar.

    ![Vista de tabelas externas](media/load-data-from-azure-blob-storage-using-polybase/view-external-tables.png)

## <a name="load-the-data-into-your-data-warehouse"></a>Carregar os dados para o armazém de dados

Esta secção utiliza tabelas externas que acabou de definir para carregar os dados de exemplo de Blob de armazenamento do Azure ao SQL Data Warehouse.  

O script utiliza o [criar tabela AS SELECIONE (CTAS)](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse.md) instrução de T-SQL para carregar os dados de Blob de armazenamento do Azure para novas tabelas no armazém de dados. CTAS cria uma nova tabela baseada nos resultados de uma instrução select. A nova tabela tem as mesmas colunas e tipos de dados dos resultados da instrução select. Quando a instrução select seleciona uma tabela externa, o SQL Data Warehouse importa os dados para uma tabela no armazém de dados relacional. 

1. Execute o script seguinte para carregar os dados para novas tabelas no armazém de dados.

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

2. Veja os dados à medida que são carregados. Está a carregar vários GBs de dados e a comprimi-los em Índices columnstore em cluster de elevado desempenho. Execute a seguinte consulta que utiliza vistas de gestão dinâmica (DMVs) para mostrar o estado do carregamento. Depois de iniciar a consulta, agarre num café e num snack enquanto o SQL Data Warehouse faz alguns carregamentos pesados.

    ```sql
    SELECT
        r.command,
        s.request_id,
        r.status,
        count(distinct input_name) as nbr_files,
        sum(s.bytes_processed)/1024/1024/1024 as gb_processed
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

3. Veja todas as consultas de sistema.

    ```sql
    SELECT * FROM sys.dm_pdw_exec_requests;
    ```

4. Desfrute de mais a ver os dados carregados corretamente para o armazém de dados.

    ![Ver tabelas carregadas](media/load-data-from-azure-blob-storage-using-polybase/view-loaded-tables.png)

## <a name="create-statistics-on-newly-loaded-data"></a>Criar estatísticas nos dados recentemente carregados

O SQL Data Warehouse não cria nem atualiza automaticamente as estatísticas. Por isso, para obter um desempenho de consulta elevado, é importante criar estatísticas em cada coluna de cada tabela após o primeiro carregamento. Também é importante atualizar as estatísticas após alterações substanciais nos dados.

1. Execute estes comandos para criar estatísticas em colunas que se prevê a ser utilizadas nas associações.

    ```sql
    CREATE STATISTICS [dbo.Date DateID stats] ON dbo.Date (DateID);
    CREATE STATISTICS [dbo.Trip DateID stats] ON dbo.Trip (DateID);
    ```

## <a name="clean-up-resources"></a>Limpar recursos

Estão a ser-lhe cobrados para recursos de computação e os dados que é carregado para o armazém de dados. Estes são cobradas separadamente. 

- Se pretender manter os dados no armazenamento, pode interromper a computação quando não estiverem a utilizar o armazém de dados. Ao colocar em pausa computação só será encargos de armazenamento de dados e pode retomar a computação sempre que estiver pronto para trabalhar com os dados.
- Se pretender remover os encargos futuros, é possível eliminar o armazém de dados. 

Siga estes passos para limpar os recursos que desejar.

1. Inicie sessão no [portal do Azure](https://portal.azure.com), clique no seu armazém de dados.

    ![Limpar recursos](media/load-data-from-azure-blob-storage-using-polybase/clean-up-resources.png)

2. Para colocar em pausa computação, clique em de **colocar em pausa** botão. Quando o armazém de dados está em pausa, verá um **iniciar** botão.  Para retomar a computação, clique em **iniciar**.

3. Para remover o armazém de dados, de modo que não lhe será cobrado para computação ou o armazenamento, clique em **eliminar**.

4. Para remover o servidor SQL que criou, clique em **mynewserver 20171113.database.windows.net** na imagem anterior e, em seguida, clique em **eliminar**.  Seja cuidadoso com esta opção como eliminar o servidor irá eliminar todas as bases de dados atribuídos ao servidor.

5. Para remover o grupo de recursos, clique em **myResourceGroup**e, em seguida, clique em **eliminar grupo de recursos**.

## <a name="next-steps"></a>Passos seguintes 
Neste tutorial, aprendeu a criar um armazém de dados e criar um utilizador para carregar dados. Criar tabelas externas para definir a estrutura dos dados armazenados no Blob de armazenamento do Azure e, em seguida, utilizar a instrução PolyBase CREATE TABLE AS SELECT para carregar dados para o armazém de dados. 

Fez tudo:
> [!div class="checklist"]
> * Criar um armazém de dados no portal do Azure
> * Configurar uma regra de firewall ao nível do servidor no portal do Azure
> * Ligado ao armazém de dados com o SSMS
> * Criar um utilizador designado para carregar dados
> * Criar tabelas externas para dados no Blob de armazenamento do Azure
> * Utilizada a instrução CTAS T-SQL para carregar dados para o armazém de dados
> * Ver o progresso dos dados que está a carregar
> * Criar estatísticas nos dados recentemente carregados

Avançar para a descrição geral da migração para saber como migrar uma base de dados existente para o SQL Data Warehouse.

> [!div class="nextstepaction"]
>[Saiba como migrar uma base de dados existente para o SQL Data Warehouse](sql-data-warehouse-overview-migrate.md)
