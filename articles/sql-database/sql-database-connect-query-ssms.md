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
ms.custom: development
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 03/15/2017
ms.author: carlrab
translationtype: Human Translation
ms.sourcegitcommit: 4f2230ea0cc5b3e258a1a26a39e99433b04ffe18
ms.openlocfilehash: ba56eaa154116edbe1dd8962049535cfa57551ac
ms.lasthandoff: 03/25/2017


---
# <a name="azure-sql-database-use-sql-server-management-studio-to-connect-and-query-data"></a>Base de Dados SQL do Azure: utilize o SQL Server Management Studio para ligar e consultar dados

Utilize o [SQL Server Management Studio](https://msdn.microsoft.com/library/ms174173.aspx) (SSMS) para criar e gerir recursos do SQL Server a partir da interface de utilizador ou em scripts. Este guia detalha a utilização do SSMS para ligar a uma base de dados SQL do Azure e, em seguida, executar declarações de consulta, inserção, atualização e eliminação.

Este guia de introdução utiliza como ponto de partida os recursos criados num destes guias de introdução:

- [Criar BD - Portal](sql-database-get-started-portal.md)
- [Criar BD - CLI](sql-database-get-started-cli.md)

Antes de começar, certifique-se de que instalou a versão mais recente do [SSMS](https://msdn.microsoft.com/library/mt238290.aspx). 

## <a name="get-connection-information"></a>Obter informações da ligação

Obtenha o nome de servidor totalmente qualificado para o servidor da Base de Dados SQL do Azure no portal do Azure. Utilize o nome de servidor totalmente qualificado para ligar ao seu servidor com o SQL Server Management Studio.

1. Inicie sessão no [Portal do Azure](https://portal.azure.com/).
2. Selecione **Bases de Dados SQL** a partir do menu do lado esquerdo e clique na sua base de dados na página **Bases de Dados SQL**. 
3. No painel **Essentials** na página do portal do Azure da sua base de dados, localize e, em seguida, copie o **Nome do servidor**.

    <img src="./media/sql-database-connect-query-ssms/connection-information.png" alt="connection information" style="width: 780px;" />

## <a name="connect-to-the-server"></a>Ligar ao servidor

Utilize o SQL Server Management Studio para estabelecer uma ligação ao servidor da Base de Dados SQL do Azure.

1. Escreva **SSMS** na caixa de pesquisa do Windows e, em seguida, clique em **Enter** para abrir o SSMS.

2. Na caixa de dialogo **Ligar ao Servidor**, introduza as seguintes informações:
   - **Tipo de servidor**: especificar Base de Dados do motor
   - **Nome do servidor**: Introduza o seu nome de servidor totalmente qualificado, como **mynewserver20170313.database.windows.net**
   - **Autenticação**: especifique a autenticação do SQL Server
   - **Início de sessão**: introduza a sua conta de administrador do servidor
   - **Palavra-passe**: introduza a palavra-passe para a sua conta de administrador do servidor
 
    <img src="./media/sql-database-connect-query-ssms/connect.png" alt="connect to server" style="width: 780px;" />

3. Clique em **Ligar**. A janela do Object Explorer é aberta no SSMS. 

    <img src="./media/sql-database-connect-query-ssms/connected.png" alt="connected to server" style="width: 780px;" />

4. No Object Explorer, expanda as **Bases de Dados** e, em seguida, expanda **mySampleDatabase**, para ver os objetos na base de dados de exemplo.

## <a name="query-data"></a>Consultar dados

Utilize a declaração do Transact-SQL [SELECIONAR](https://msdn.microsoft.com/library/ms189499.aspx) para consultar dados na base de dados SQL do Azure.

1. No Object Explorer, clique com o botão direito do rato em **mySampleDatabase** e, em seguida, clique em **Nova Consulta**. É aberta uma janela de consulta em branco que está ligada à sua base de dados.
2. Na janela de consulta, introduza a consulta seguinte na janela de consulta:

   ```sql
   SELECT pc.Name as CategoryName, p.name as ProductName
   FROM [SalesLT].[ProductCategory] pc
   JOIN [SalesLT].[Product] p
   ON pc.productcategoryid = p.productcategoryid;
   ```

3. Na barra de ferramentas, clique em **Executar** para obter dados das tabelas Produto e ProductCategory.

    <img src="./media/sql-database-connect-query-ssms/query.png" alt="query" style="width: 780px;" />

## <a name="insert-data"></a>Inserir dados

Utilize a declaração do Transact-SQL [INSERIR](https://msdn.microsoft.com/library/ms174335.aspx) para inserir dados na sua base de dados SQL do Azure.

1. Na barra de ferramentas, clique em **Nova Consulta**. Uma janela de consulta em branco abre-se ligada à sua base de dados.
2. Na janela de consulta, introduza a consulta seguinte na janela de consulta:

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

3. Na barra de ferramentas, clique em **Executar**, para inserir uma nova linha na tabela Produto.

    <img src="./media/sql-database-connect-query-ssms/insert.png" alt="insert" style="width: 780px;" />

## <a name="update-data"></a>Atualizar dados

Utilize a declaração do Transact-SQL [ATUALIZAR](https://msdn.microsoft.com/library/ms177523.aspx) para atualizar dados na base de dados SQL do Azure.

1. Na barra de ferramentas, clique em **Nova Consulta**. Uma janela de consulta em branco abre-se ligada à sua base de dados.
2. Na janela de consulta, introduza a consulta seguinte na janela de consulta:

   ```sql
   UPDATE [SalesLT].[Product]
   SET [ListPrice] = 125
   WHERE Name = 'myNewProduct';
   ```

3. Na barra de ferramentas, clique em **Executar**, para atualizar a linha especificada na tabela Produto.

    <img src="./media/sql-database-connect-query-ssms/update.png" alt="update" style="width: 780px;" />

## <a name="delete-data"></a>Eliminar dados

Utilize a declaração do Transact-SQL [ELIMINAR](https://msdn.microsoft.com/library/ms189835.aspx) para eliminar dados na base de dados SQL do Azure.

1. Na barra de ferramentas, clique em **Nova Consulta**. Uma janela de consulta em branco abre-se ligada à sua base de dados.
2. Na janela de consulta, introduza a consulta seguinte na janela de consulta:

   ```sql
   DELETE FROM [SalesLT].[Product]
   WHERE Name = 'myNewProduct';
   ```

3. Na barra de ferramentas, clique em **Executar**, para eliminar a linha especificada na tabela Produto.

    <img src="./media/sql-database-connect-query-ssms/delete.png" alt="delete" style="width: 780px;" />

## <a name="next-steps"></a>Passos seguintes

- Para obter informações sobre o SSMS, consulte o artigo [Use SQL Server Management Studio (Utilizar o SQL Server Management Studio)](https://msdn.microsoft.com/library/ms174173.aspx).
- Para obter informações sobre a consulta e edição de dados com o Visual Studio Code, consulte [Visual Studio Code](https://code.visualstudio.com/docs)

