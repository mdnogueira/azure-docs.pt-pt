---
title: "Começar a utilizar consultas de base de dados em vários locais (criação de partições vertical) | Microsoft Docs"
description: "como utilizar a consulta de base de dados elásticas com verticalmente particionada bases de dados"
services: sql-database
documentationcenter: 
manager: jhubbard
author: torsteng
ms.assetid: e5b44b10-c432-4f96-b20e-08615ff4d5dd
ms.service: sql-database
ms.custom: scale out apps
ms.workload: On Demand
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/23/2016
ms.author: torsteng
ms.openlocfilehash: ca715c0ef41ccb4aed2924898d7729bed659b0ca
ms.sourcegitcommit: dfd49613fce4ce917e844d205c85359ff093bb9c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/31/2017
---
# <a name="get-started-with-cross-database-queries-vertical-partitioning-preview"></a>Começar a utilizar consultas de base de dados em vários locais (criação de partições vertical) (pré-visualização)
A consulta de base de dados elásticas (pré-visualização) para a SQL Database do Azure permite-lhe executar consultas de T-SQL que abrangem várias bases de dados a utilizar um ponto de ligação única. Este tópico aplica-se a [verticalmente particionada bases de dados](sql-database-elastic-query-vertical-partitioning.md).  

Quando concluído, irá: Saiba como configurar e utilizar uma base de dados do SQL do Azure para executar consultas que abrangem várias bases de dados relacionados. 

Para obter mais informações sobre a funcionalidade de consulta de base de dados elásticas, consulte [descrição geral de consulta de base de dados elástica do SQL Database do Azure](sql-database-elastic-query-overview.md). 

## <a name="prerequisites"></a>Pré-requisitos

Precisa de ter permissão ALTER qualquer origem de dados externa. Esta permissão está incluído com a permissão ALTER DATABASE. São necessárias permissões de ALTER qualquer origem de dados externa para fazer referência à origem de dados subjacente.

## <a name="create-the-sample-databases"></a>Criar as bases de dados de exemplo
Para começar, temos de criar duas bases de dados, **clientes** e **ordens**, tem o mesmo ou lógica num servidor diferente.   

Execute as seguintes consultas no **ordens** base de dados para criar o **OrderInformation** uma tabela e os dados de exemplo de entrada. 

    CREATE TABLE [dbo].[OrderInformation]( 
        [OrderID] [int] NOT NULL, 
        [CustomerID] [int] NOT NULL 
        ) 
    INSERT INTO [dbo].[OrderInformation] ([OrderID], [CustomerID]) VALUES (123, 1) 
    INSERT INTO [dbo].[OrderInformation] ([OrderID], [CustomerID]) VALUES (149, 2) 
    INSERT INTO [dbo].[OrderInformation] ([OrderID], [CustomerID]) VALUES (857, 2) 
    INSERT INTO [dbo].[OrderInformation] ([OrderID], [CustomerID]) VALUES (321, 1) 
    INSERT INTO [dbo].[OrderInformation] ([OrderID], [CustomerID]) VALUES (564, 8) 

Agora, execute a seguinte consulta no **clientes** base de dados para criar o **CustomerInformation** uma tabela e os dados de exemplo de entrada. 

    CREATE TABLE [dbo].[CustomerInformation]( 
        [CustomerID] [int] NOT NULL, 
        [CustomerName] [varchar](50) NULL, 
        [Company] [varchar](50) NULL 
        CONSTRAINT [CustID] PRIMARY KEY CLUSTERED ([CustomerID] ASC) 
    ) 
    INSERT INTO [dbo].[CustomerInformation] ([CustomerID], [CustomerName], [Company]) VALUES (1, 'Jack', 'ABC') 
    INSERT INTO [dbo].[CustomerInformation] ([CustomerID], [CustomerName], [Company]) VALUES (2, 'Steve', 'XYZ') 
    INSERT INTO [dbo].[CustomerInformation] ([CustomerID], [CustomerName], [Company]) VALUES (3, 'Lylla', 'MNO') 

## <a name="create-database-objects"></a>Criar objetos de base de dados
### <a name="database-scoped-master-key-and-credentials"></a>Chave mestra do âmbito e as credenciais da base de dados
1. Abra o SQL Server Management Studio ou o SQL Server Data Tools no Visual Studio.
2. Ligar à base de dados ordens e execute os seguintes comandos de T-SQL:
   
        CREATE MASTER KEY ENCRYPTION BY PASSWORD = '<password>'; 
        CREATE DATABASE SCOPED CREDENTIAL ElasticDBQueryCred 
        WITH IDENTITY = '<username>', 
        SECRET = '<password>';  
   
    O "nomedeutilizador" e "password" devem ser o nome de utilizador e palavra-passe utilizada para iniciar sessão na base de dados de clientes.
    A autenticação utilizando o Azure Active Directory com consultas elásticas não é atualmente suportada.

### <a name="external-data-sources"></a>Origens de dados externas
Para criar uma origem de dados externa, execute o seguinte comando na base de dados ordens: 

    CREATE EXTERNAL DATA SOURCE MyElasticDBQueryDataSrc WITH 
        (TYPE = RDBMS, 
        LOCATION = '<server_name>.database.windows.net', 
        DATABASE_NAME = 'Customers', 
        CREDENTIAL = ElasticDBQueryCred, 
    ) ;

### <a name="external-tables"></a>Tabelas externas
Crie uma tabela externa na base de dados ordens, que corresponde à definição da tabela CustomerInformation:

    CREATE EXTERNAL TABLE [dbo].[CustomerInformation] 
    ( [CustomerID] [int] NOT NULL, 
      [CustomerName] [varchar](50) NOT NULL, 
      [Company] [varchar](50) NOT NULL) 
    WITH 
    ( DATA_SOURCE = MyElasticDBQueryDataSrc) 

## <a name="execute-a-sample-elastic-database-t-sql-query"></a>Executar uma consulta de T-SQL da base de dados elástica de exemplo
Assim que definiu a sua origem de dados externos e as tabelas externas, agora pode utilizar o T-SQL para consultar as tabelas externas. Execute esta consulta na base de dados ordens: 

    SELECT OrderInformation.CustomerID, OrderInformation.OrderId, CustomerInformation.CustomerName, CustomerInformation.Company 
    FROM OrderInformation 
    INNER JOIN CustomerInformation 
    ON CustomerInformation.CustomerID = OrderInformation.CustomerID 

## <a name="cost"></a>Custo
Atualmente, a funcionalidade de consulta de base de dados elástica está incluída no custo da base de dados SQL do Azure.  

Para obter informações sobre preços, consulte [preços da SQL Database](https://azure.microsoft.com/pricing/details/sql-database). 

## <a name="next-steps"></a>Passos seguintes

* Para obter uma descrição geral da consulta elástica, consulte [descrição geral de consulta elástico](sql-database-elastic-query-overview.md).
* Para consultas de sintaxe e exemplos de dados verticalmente particionadas, consulte [consultar verticalmente particionada dados)](sql-database-elastic-query-vertical-partitioning.md)
* Para um tutorial horizontal criação de partições (fragmentação), consulte [como começar a consultar elástico (fragmentação) de partições horizontais](sql-database-elastic-query-getting-started.md).
* Para consultas de sintaxe e exemplos de dados particionadas horizontalmente, consulte [consultar horizontalmente particionada dados)](sql-database-elastic-query-horizontal-partitioning.md)
* Consulte [sp\_executar \_remoto](https://msdn.microsoft.com/library/mt703714) para um procedimento armazenado que executa uma instrução de Transact-SQL num único remoto SQL Database do Azure ou um conjunto de bases de dados que servem como shards num esquema de partições horizontal.
