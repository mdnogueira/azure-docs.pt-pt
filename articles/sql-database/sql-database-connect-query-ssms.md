---
title: 'SSMS: ligar e consultar dados na Base de Dados SQL do Azure | Microsoft Docs'
description: "Saiba como ligar à Base de Dados SQL no Azure, utilizando o SQL Server Management Studio (SSMS). Em seguida, execute declarações do Transact-SQL (T-SQL) para consultar e editar dados."
metacanonical: 
keywords: "ligar à base de dados sql, sql server management studio"
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: 7cd2a114-c13c-4ace-9088-97bd9d68de12
ms.service: sql-database
ms.custom: mvc,DBs & servers
ms.workload: Active
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 05/26/2017
ms.author: carlrab
ms.openlocfilehash: f67ba82c3aaf312662096964b7b7942efa108607
ms.sourcegitcommit: dfd49613fce4ce917e844d205c85359ff093bb9c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/31/2017
---
# <a name="azure-sql-database-use-sql-server-management-studio-to-connect-and-query-data"></a>Base de Dados SQL do Azure: utilize o SQL Server Management Studio para ligar e consultar dados

O [SQL Server Management Studio](https://msdn.microsoft.com/library/ms174173.aspx) (SSMS) é um ambiente integrado para gerir qualquer infraestrutura SQL, do SQL Server à Base de Dados SQL do Microsoft Windows. Este guia de introdução demonstra como utilizar SSMS para ligar a uma base de dados SQL do Azure e, em seguida, utilizar as declarações de Transact-SQL para consultar, inserir, atualizar e eliminar dados na base de dados. 

## <a name="prerequisites"></a>Pré-requisitos

Este guia de introdução utiliza como ponto de partida os recursos criados num destes guias de introdução:

- [Criar BD - Portal](sql-database-get-started-portal.md)
- [Criar BD - CLI](sql-database-get-started-cli.md)
- [Criar BD - PowerShell](sql-database-get-started-powershell.md)

Antes de começar, certifique-se de que instalou a versão mais recente do [SSMS](https://msdn.microsoft.com/library/mt238290.aspx). 

## <a name="sql-server-connection-information"></a>Informações de ligação do servidor SQL

Obtenha as informações de ligação necessárias para ligar à base de dados SQL do Azure. Irá precisar do nome de servidor totalmente qualificado, do nome de base de dados e das informações de início de sessão nos seguintes procedimentos.

1. Inicie sessão no [Portal do Azure](https://portal.azure.com/).
2. Selecione **Bases de Dados SQL** a partir do menu do lado esquerdo e clique na sua base de dados na página **Bases de Dados SQL**. 
3. Na página **Overview (Descrição geral)** da sua base de dados, reveja o nome de servidor totalmente qualificado, como mostrado na imagem abaixo. Pode pairar sobre o nome do servidor para aparecer a opção **Clique para copiar**.

   ![informações da ligação](./media/sql-database-connect-query-dotnet/server-name.png) 

4. Se se tiver esquecido das informações de início de sessão do seu servidor da Base de Dados SQL do Azure, navegue para a página de servidor da Base de Dados SQL para visualizar o nome de administrador do servidor e, se necessário, repor a palavra-passe. 

## <a name="connect-to-your-database"></a>Ligar à base de dados

Utilize o SQL Server Management Studio para estabelecer uma ligação ao servidor da Base de Dados SQL do Azure. 

> [!IMPORTANT]
> Um servidor lógico da Base de Dados SQL do Azure ouve na porta 1433. Se está a tentar ligar a um servidor lógico da Base de Dados SQL do Azure a partir de uma firewall empresarial, esta porta tem de estar aberta na firewall empresarial para se ligar com êxito.
>

1. Abra o SQL Server Management Studio.

2. Na caixa de dialogo **Ligar ao Servidor**, introduza as seguintes informações:

   | Definição       | Valor sugerido | Descrição | 
   | ------------ | ------------------ | ------------------------------------------------- | 
   | **Tipo de servidor** | Motor de base de dados | Este valor é preciso. |
   | **Nome do servidor** | O nome de servidor completamente qualificado | O nome deve ser semelhante a: **mynewserver20170313.database.windows.net**. |
   | **Autenticação** | Autenticação do SQL Server | A Autenticação do SQL é o único tipo de autenticação que configurámos neste tutorial. |
   | **Início de sessão** | A conta de administrador do servidor | Esta é a conta que especificou quando criou o servidor. |
   | **Palavra-passe** | A palavra-passe da sua conta de administrador do servidor | Esta é a palavra-passe que especificou quando criou o servidor. |

   ![ligar ao servidor](./media/sql-database-connect-query-ssms/connect.png)  

3. Clique em **Opções** na caixa de diálogo **Ligar ao servidor**. Na secção **Ligar à base de dados**, introduza **mySampleDatabase** para ligar a esta base de dados.

   ![ligar à base de dados no servidor](./media/sql-database-connect-query-ssms/options-connect-to-db.png)  

4. Clique em **Ligar**. A janela do Object Explorer é aberta no SSMS. 

   ![ligado ao servidor](./media/sql-database-connect-query-ssms/connected.png)  

5. No Object Explorer, expanda as **Bases de Dados** e, em seguida, expanda **mySampleDatabase**, para ver os objetos na base de dados de exemplo.

## <a name="query-data"></a>Consultar dados

Utilize o seguinte código para consultar os 20 melhores produtos por categoria com uma declaração Transact-SQL [SELECIONAR](https://msdn.microsoft.com/library/ms189499.aspx).

1. No Object Explorer, clique com o botão direito do rato em **mySampleDatabase** e, em seguida, clique em **Nova Consulta**. É aberta uma janela de consulta em branco que está ligada à sua base de dados.
2. Na janela da consulta, introduza a seguinte consulta:

   ```sql
   SELECT pc.Name as CategoryName, p.name as ProductName
   FROM [SalesLT].[ProductCategory] pc
   JOIN [SalesLT].[Product] p
   ON pc.productcategoryid = p.productcategoryid;
   ```

3. Na barra de ferramentas, clique em **Executar** para obter dados das tabelas Produto e ProductCategory.

    ![consulta](./media/sql-database-connect-query-ssms/query.png)

## <a name="insert-data"></a>Inserir dados

Utilize o seguinte código para inserir um novo produto na tabela SalesLT.Product com uma declaração Transact-SQL [INSERIR](https://msdn.microsoft.com/library/ms174335.aspx).

1. Na janela da consulta, substitua a consulta anterior pela seguinte consulta:

   ```sql
   INSERT INTO [SalesLT].[Product]
           ( [Name]
           , [ProductNumber]
           , [Color]
           , [ProductCategoryID]
           , [StandardCost]
           , [ListPrice]
           , [SellStartDate]
           )
     VALUES
           ('myNewProduct'
           ,123456789
           ,'NewColor'
           ,1
           ,100
           ,100
           ,GETDATE() );
   ```

2. Na barra de ferramentas, clique em **Executar**, para inserir uma nova linha na tabela Produto.

    <img src="./media/sql-database-connect-query-ssms/insert.png" alt="insert" style="width: 780px;" />

## <a name="update-data"></a>Atualizar dados

Utilize o seguinte código para atualizar o produto novo que foi adicionado anteriormente com uma declaração Transact-SQL [ATUALIZAR](https://msdn.microsoft.com/library/ms177523.aspx).

1. Na janela da consulta, substitua a consulta anterior pela seguinte consulta:

   ```sql
   UPDATE [SalesLT].[Product]
   SET [ListPrice] = 125
   WHERE Name = 'myNewProduct';
   ```

2. Na barra de ferramentas, clique em **Executar**, para atualizar a linha especificada na tabela Produto.

    <img src="./media/sql-database-connect-query-ssms/update.png" alt="update" style="width: 780px;" />

## <a name="delete-data"></a>Eliminar dados

Utilize o seguinte código para eliminar o produto novo que foi adicionado anteriormente com uma declaração Transact-SQL [ELIMINAR](https://msdn.microsoft.com/library/ms189835.aspx).

1. Na janela da consulta, substitua a consulta anterior pela seguinte consulta:

   ```sql
   DELETE FROM [SalesLT].[Product]
   WHERE Name = 'myNewProduct';
   ```

2. Na barra de ferramentas, clique em **Executar**, para eliminar a linha especificada na tabela Produto.

    <img src="./media/sql-database-connect-query-ssms/delete.png" alt="delete" style="width: 780px;" />

## <a name="next-steps"></a>Passos seguintes

- Para obter informações sobre a criação e a gestão de servidores e bases de dados com o Transact-SQL, veja [Learn about Azure SQL Database servers and databases](sql-database-servers-databases.md) (Saiba mais sobre servidores e bases de dados da Base de Dados SQL do Azure).
- Para obter informações sobre o SSMS, consulte o artigo [Use SQL Server Management Studio (Utilizar o SQL Server Management Studio)](https://msdn.microsoft.com/library/ms174173.aspx).
- Para ligar e fazer consultas com o portal do Azure, veja [Ligar e fazer consultas com o editor de consultas SQL do portal do Azure](sql-database-connect-query-portal.md).
- Para ligar e consultar com o Visual Studio Code, veja [Connect and query with Visual Studio Code (Ligar e consultar com o Visual Studio Code)](sql-database-connect-query-vscode.md).
- Para ligar e consultar com .NET, consulte [Connect and query with .NET (Ligar e consultar com .NET)](sql-database-connect-query-dotnet.md).
- Para ligar e consultar com PHP, consulte [Connect and query with PHP (Ligar e consultar com PHP)](sql-database-connect-query-php.md).
- Para ligar e consultar com Node.js, consulte [Connect and query with Node.js (Ligar e consultar com Node.js)](sql-database-connect-query-nodejs.md).
- Para ligar e consultar com Java, consulte [Connect and query with Java (Ligar e consultar com Java)](sql-database-connect-query-java.md).
- Para ligar e consultar com Python, consulte [Connect and query with Python (Ligar e consultar com Python)](sql-database-connect-query-python.md).
- Para ligar e consultar com Ruby, consulte [Connect and query with Ruby (Ligar e consultar com Ruby)](sql-database-connect-query-ruby.md).
