---
title: 'VS Code: ligar e consultar dados na Base de Dados SQL do Azure | Microsoft Docs'
description: "Saiba como ligar à Base de Dados SQL no Azure com o Visual Studio Code. Em seguida, execute declarações do Transact-SQL (T-SQL) para consultar e editar dados."
metacanonical: 
keywords: "ligar à base de dados SQL"
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: 676bd799-a571-4bb8-848b-fb1720007866
ms.service: sql-database
ms.custom: manage
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 03/17/2017
ms.author: carlrab
translationtype: Human Translation
ms.sourcegitcommit: 4f2230ea0cc5b3e258a1a26a39e99433b04ffe18
ms.openlocfilehash: 86471fe29bbc9076624d96b83f7001d8755363bc
ms.lasthandoff: 03/25/2017


---
# <a name="azure-sql-database-use-visual-studio-code-to-connect-and-query-data"></a>Base de Dados SQL do Azure: utilize o Visual Studio Code para ligar e consultar dados

O [Visual Studio Code](https://code.visualstudio.com/docs) é um editor de código gráfico para Linux, macOS e Windows que suporta extensões. Utilize o Visual Studio Code com a [extensão mssql](https://aka.ms/mssql-marketplace) para ligar e consultar uma base de dados SQL do Azure. Este guia detalha a utilização do Visual Studio Code para ligar a uma base de dados SQL do Azure e, em seguida, executar declarações de consulta, inserção, atualização e eliminação.

Este guia de introdução utiliza como ponto de partida os recursos criados num destes guias de introdução:

- [Criar BD - Portal](sql-database-get-started-portal.md)
- [Criar BD - CLI](sql-database-get-started-cli.md)

Antes de começar, certifique-se de que instalou a versão mais recente do [Visual Studio Code](https://code.visualstudio.com/Download) e carregou a [extensão mssql](https://aka.ms/mssql-marketplace). Para obter orientações sobre a instalação da extensão mssql, consulte [Install VS Code (Instalar VS Code)](https://docs.microsoft.com/sql/linux/sql-server-linux-develop-use-vscode#install-vs-code). 

## <a name="get-connection-information"></a>Obter informações da ligação

Obtenha o nome de servidor totalmente qualificado para o servidor da Base de Dados SQL do Azure no portal do Azure. Utilize o nome de servidor totalmente qualificado para ligar ao seu servidor com o Visual Studio Code.

1. Inicie sessão no [Portal do Azure](https://portal.azure.com/).
2. Selecione **Bases de Dados SQL** a partir do menu do lado esquerdo e clique na sua base de dados na página **Bases de Dados SQL**. 
3. No painel **Essentials** na página do portal do Azure da sua base de dados, localize e, em seguida, copie o **Nome do servidor** para utilizar mais tarde neste guia de introdução.

    <img src="./media/sql-database-connect-query-ssms/connection-information.png" alt="connection information" style="width: 780px;" />

## <a name="set-language-mode-to-sql"></a>Definir modo do idioma para SQL

Defina o modo de idioma para **SQL** no Visual Studio Code, para ativar comandos mssql e IntelliSense T-SQL.

1. Abra uma nova janela do Visual Studio Code. 

2. Prima **CTRL+K,M**, escreva **SQL** e prima **ENTER** para definir o modo de idioma para SQL. 

<img src="./media/sql-database-connect-query-vscode/vscode-language-mode.png" alt="SQL language mode" style="width: 780px;" />

## <a name="connect-to-the-server"></a>Ligar ao servidor

Utilize o Visual Studio Code para estabelecer uma ligação ao servidor da Base de Dados SQL do Azure.

1. No VS Code, prima **CTRL+SHIFT+P** (ou **F1**) para abrir a Paleta de Comandos.

2. Escreva **sqlcon** e prima **ENTER**.

3. Clique em **Sim** para definir o idioma para **SQL**.

4. Prima **ENTER** para selecionar **Criar Perfil de Ligação**. Esta ação cria um perfil de ligação para a sua instância do SQL Server.

5. Siga as indicações para especificar as propriedades de ligação do novo perfil de ligação. Depois de especificar cada valor, prima **ENTER** para continuar. 

   A tabela seguinte descreve as propriedades do Perfil de Ligação.

   | Definição | Descrição |
   |-----|-----|
   | **Nome do servidor** | Introduza o seu nome de servidor totalmente qualificado, como **mynewserver20170313.database.windows.net** |
   | **Nome da base de dados** | Introduza o seu nome da base de dados, como **mySampleDatabase** |
   | **Autenticação** | Selecione o Início de Sessão SQL |
   | **Nome de utilizador** | Introduza a sua conta de administrador do servidor |
   | **Palavra-passe (Início de Sessão do SQL)** | Introduza a palavra-passe para a sua conta de administrador do servidor | 
   | **Guardar Palavra-passe?** | Selecione **Sim** ou **Não** |
   | **[Opcional] Introduza um nome para este perfil** | Introduza um nome de perfil de ligação, como **mySampleDatabase**. 

6. Prima a tecla **ESC** para fechar a mensagem que informa que o perfil está criado e ligado.

7. Verifique a ligação na barra de estado.

   <img src="./media/sql-database-connect-query-vscode/vscode-connection-status.png" alt="Connection status" style="width: 780px;" />

## <a name="query-data"></a>Consultar dados

Utilize a declaração do Transact-SQL [SELECIONAR](https://msdn.microsoft.com/library/ms189499.aspx) para consultar dados na base de dados SQL do Azure.

1. Na janela **Editor**, introduza a consulta seguinte na janela de consulta vazia:

   ```sql
   SELECT pc.Name as CategoryName, p.name as ProductName
   FROM [SalesLT].[ProductCategory] pc
   JOIN [SalesLT].[Product] p
   ON pc.productcategoryid = p.productcategoryid;
   ```

3. Prima **CTRL+SHIFT+E** para obter dados das tabelas Produto e ProductCategory.

    <img src="./media/sql-database-connect-query-vscode/query.png" alt="Query" style="width: 780px;" />

## <a name="insert-data"></a>Inserir dados

Utilize a declaração do Transact-SQL [INSERIR](https://msdn.microsoft.com/library/ms174335.aspx) para inserir dados na sua base de dados SQL do Azure.

1. Na janela **Editor**, elimine a consulta anterior e introduza a seguinte consulta:

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

3. Prima **CTRL+SHIFT+E** para inserir uma nova linha na tabela Produto.

## <a name="update-data"></a>Atualizar dados

Utilize a declaração do Transact-SQL [ATUALIZAR](https://msdn.microsoft.com/library/ms177523.aspx) para atualizar dados na base de dados SQL do Azure.

1.  Na janela **Editor**, elimine a consulta anterior e introduza a seguinte consulta:

   ```sql
   UPDATE [SalesLT].[Product]
   SET [ListPrice] = 125
   WHERE Name = 'myNewProduct';
   ```

3. Prima **CTRL+SHIFT+E** para atualizar a linha especificada na tabela Produto.

## <a name="delete-data"></a>Eliminar dados

Utilize a declaração do Transact-SQL [ELIMINAR](https://msdn.microsoft.com/library/ms189835.aspx) para eliminar dados na base de dados SQL do Azure.

1. Na janela **Editor**, elimine a consulta anterior e introduza a seguinte consulta:

   ```sql
   DELETE FROM [SalesLT].[Product]
   WHERE Name = 'myNewProduct';
   ```

3. Prima **CTRL+SHIFT+E** para eliminar a linha especificada na tabela Produto.

## <a name="next-steps"></a>Passos seguintes

- Para obter informações sobre o Visual Studio Code, consulte [Visual Studio Code](https://code.visualstudio.com/docs)
- Para obter informações sobre a consulta e edição de dados com o SQL Server Management Studio, consulte [SSMS](https://msdn.microsoft.com/library/ms174173.aspx).

