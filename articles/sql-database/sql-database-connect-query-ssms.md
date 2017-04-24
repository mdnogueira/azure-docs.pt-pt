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
ms.custom: quick start manage
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 03/15/2017
ms.author: carlrab
translationtype: Human Translation
ms.sourcegitcommit: 0d6f6fb24f1f01d703104f925dcd03ee1ff46062
ms.openlocfilehash: a08fe566f4962684bc4aad220687e9cd36fc4abf
ms.lasthandoff: 04/17/2017


---
# <a name="azure-sql-database-use-sql-server-management-studio-to-connect-and-query-data"></a>Base de Dados SQL do Azure: utilize o SQL Server Management Studio para ligar e consultar dados

O [SQL Server Management Studio](https://msdn.microsoft.com/library/ms174173.aspx) (SSMS) é uma ferramenta de gestão para criar e gerir recursos do SQL Server a partir da interface de utilizador ou em scripts. Este guia de introdução demonstra como utilizar SSMS para ligar a uma base de dados SQL do Azure e, em seguida, utilizar as declarações de Transact-SQL para consultar, inserir, atualizar e eliminar dados na base de dados. 

Este guia de introdução utiliza como ponto de partida os recursos criados num destes guias de introdução:

- [Criar BD - Portal](sql-database-get-started-portal.md)
- [Criar BD - CLI](sql-database-get-started-cli.md)

Antes de começar, certifique-se de que instalou a versão mais recente do [SSMS](https://msdn.microsoft.com/library/mt238290.aspx). 

## <a name="get-connection-information"></a>Obter informações da ligação

Obtenha o nome de servidor totalmente qualificado para o servidor da Base de Dados SQL do Azure no portal do Azure. Utilize o nome de servidor totalmente qualificado para ligar ao seu servidor com o SQL Server Management Studio.

1. Inicie sessão no [Portal do Azure](https://portal.azure.com/).
2. Selecione **Bases de Dados SQL** a partir do menu do lado esquerdo e clique na sua base de dados na página **Bases de Dados SQL**. 
3. Na página **Overview (Descrição geral)** da sua base de dados, reveja o nome de servidor totalmente qualificado, como mostrado na imagem abaixo. Pode pairar sobre o nome do servidor para aparecer a opção **Clique para copiar**.

   ![informações da ligação](./media/sql-database-connect-query-ssms/connection-information.png) 

4. Se se tiver esquecido das informações de início de sessão do seu servidor da Base de Dados SQL do Azure, navegue para a página de servidor da Base de Dados SQL para visualizar o nome de administrador do servidor e, se necessário, repor a palavra-passe. 

## <a name="connect-to-your-database-in-the-sql-database-logical-server"></a>Ligue à base de dados no servidor lógico da Base de Dados SQL

Utilize o SQL Server Management Studio para estabelecer uma ligação ao servidor da Base de Dados SQL do Azure. 

> [!IMPORTANT]
> Um servidor lógico da Base de Dados SQL do Azure ouve na porta 1433. Se está a tentar ligar a um servidor lógico da Base de Dados SQL do Azure a partir de uma firewall empresarial, esta porta tem de estar aberta na firewall empresarial para se ligar com êxito.
>

1. Abra o SQL Server Management Studio.

2. Na caixa de dialogo **Ligar ao Servidor**, introduza as seguintes informações:
   - **Tipo de servidor**: especificar Base de Dados do motor
   - **Nome do servidor**: Introduza o seu nome de servidor totalmente qualificado, como **mynewserver20170313.database.windows.net**
   - **Autenticação**: especifique a autenticação do SQL Server
   - **Início de sessão**: introduza a sua conta de administrador do servidor
   - **Palavra-passe**: introduza a palavra-passe para a sua conta de administrador do servidor

   ![ligar ao servidor](./media/sql-database-connect-query-ssms/connect.png)  

3. Clique em **Opções** na caixa de diálogo **Ligar ao servidor**. Na secção **Ligar à base de dados**, introduza **mySampleDatabase** para ligar a esta base de dados.

   ![ligar à base de dados no servidor](./media/sql-database-connect-query-ssms/options-connect-to-db.png)  

4. Clique em **Ligar**. A janela do Object Explorer é aberta no SSMS. 

   ![ligado ao servidor](./media/sql-database-connect-query-ssms/connected.png)  

5. No Object Explorer, expanda as **Bases de Dados** e, em seguida, expanda **mySampleDatabase**, para ver os objetos na base de dados de exemplo.

## <a name="query-data"></a>Consultar dados

Utilize a declaração do Transact-SQL [SELECIONAR](https://msdn.microsoft.com/library/ms189499.aspx) para consultar dados na base de dados SQL do Azure.

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

Utilize a declaração do Transact-SQL [INSERIR](https://msdn.microsoft.com/library/ms174335.aspx) para inserir dados na sua base de dados SQL do Azure.

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

Utilize a declaração do Transact-SQL [ATUALIZAR](https://msdn.microsoft.com/library/ms177523.aspx) para atualizar dados na base de dados SQL do Azure.

1. Na janela da consulta, substitua a consulta anterior pela seguinte consulta:

   ```sql
   UPDATE [SalesLT].[Product]
   SET [ListPrice] = 125
   WHERE Name = 'myNewProduct';
   ```

2. Na barra de ferramentas, clique em **Executar**, para atualizar a linha especificada na tabela Produto.

    <img src="./media/sql-database-connect-query-ssms/update.png" alt="update" style="width: 780px;" />

## <a name="delete-data"></a>Eliminar dados

Utilize a declaração do Transact-SQL [ELIMINAR](https://msdn.microsoft.com/library/ms189835.aspx) para eliminar dados na base de dados SQL do Azure.

1. Na janela da consulta, substitua a consulta anterior pela seguinte consulta:

   ```sql
   DELETE FROM [SalesLT].[Product]
   WHERE Name = 'myNewProduct';
   ```

2. Na barra de ferramentas, clique em **Executar**, para eliminar a linha especificada na tabela Produto.

    <img src="./media/sql-database-connect-query-ssms/delete.png" alt="delete" style="width: 780px;" />

## <a name="next-steps"></a>Passos seguintes

- Para obter informações sobre o SSMS, consulte o artigo [Use SQL Server Management Studio (Utilizar o SQL Server Management Studio)](https://msdn.microsoft.com/library/ms174173.aspx).
- Para ligar e consultar com o Visual Studio Code, veja [Connect and query with Visual Studio Code (Ligar e consultar com o Visual Studio Code)](sql-database-connect-query-vscode.md).
- Para ligar e consultar com .NET, consulte [Connect and query with .NET (Ligar e consultar com .NET)](sql-database-connect-query-dotnet.md).
- Para ligar e consultar com PHP, consulte [Connect and query with PHP (Ligar e consultar com PHP)](sql-database-connect-query-php.md).
- Para ligar e consultar com Node.js, consulte [Connect and query with Node.js (Ligar e consultar com Node.js)](sql-database-connect-query-nodejs.md).
- Para ligar e consultar com Java, consulte [Connect and query with Java (Ligar e consultar com Java)](sql-database-connect-query-java.md).
- Para ligar e consultar com Python, consulte [Connect and query with Python (Ligar e consultar com Python)](sql-database-connect-query-python.md).
- Para ligar e consultar com Ruby, consulte [Connect and query with Ruby (Ligar e consultar com Ruby)](sql-database-connect-query-ruby.md).

