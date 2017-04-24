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
ms.custom: quick start manage
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 04/17/2017
ms.author: carlrab
translationtype: Human Translation
ms.sourcegitcommit: db7cb109a0131beee9beae4958232e1ec5a1d730
ms.openlocfilehash: 5b623c78f8b8eac846c5ca244f1e0b25ee4f400f
ms.lasthandoff: 04/18/2017


---
# <a name="azure-sql-database-use-visual-studio-code-to-connect-and-query-data"></a>Base de Dados SQL do Azure: utilize o Visual Studio Code para ligar e consultar dados

O [Visual Studio Code](https://code.visualstudio.com/docs) é um editor de código gráfico para Linux, macOS e Windows que suporta extensões, incluindo a [extensão mssql](https://aka.ms/mssql-marketplace) para consultar o Microsoft SQL Server, a Base de Dados SQL do Azure e o SQL Data Warehouse. Este guia de introdução demonstra como utilizar o Visual Studio Code para ligar a uma base de dados SQL do Azure e, em seguida, utilizar as declarações de Transact-SQL para consultar, inserir, atualizar e eliminar dados na base de dados.

Este guia de introdução utiliza como ponto de partida os recursos criados num destes guias de introdução:

- [Criar BD - Portal](sql-database-get-started-portal.md)
- [Criar BD - CLI](sql-database-get-started-cli.md)

Antes de começar, certifique-se de que instalou a versão mais recente do [Visual Studio Code](https://code.visualstudio.com/Download) e carregou a [extensão mssql](https://aka.ms/mssql-marketplace). Para obter orientações sobre a instalação da extensão mssql, consulte [Install VS Code (Instalar VS Code)](https://docs.microsoft.com/sql/linux/sql-server-linux-develop-use-vscode#install-vs-code) e [mssql for Visual Studio Code (mssql para Visual Studio Code)](https://marketplace.visualstudio.com/items?itemName=ms-mssql.mssql). 

## <a name="configure-vs-code-mac-os-only"></a>Configurar VS Code (apenas para Mac OS)

### <a name="mac-os"></a>**Mac OS**
Para macOS, tem de instalar o OpenSSL que é um pré-requisito do DotNet Core que essa extensão do mssql utiliza. Abra o terminal e introduza os comandos seguintes para instalar **brew**, **OpenSSL**. 

```bash
ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
brew update
brew install openssl
mkdir -p /usr/local/lib
ln -s /usr/local/opt/openssl/lib/libcrypto.1.0.0.dylib /usr/local/lib/
ln -s /usr/local/opt/openssl/lib/libssl.1.0.0.dylib /usr/local/lib/
```

## <a name="get-connection-information"></a>Obter informações da ligação

Obtenha o nome de servidor totalmente qualificado para o servidor da Base de Dados SQL do Azure no portal do Azure. Utilize o nome de servidor totalmente qualificado para ligar ao seu servidor com o Visual Studio Code.

1. Inicie sessão no [Portal do Azure](https://portal.azure.com/).
2. Selecione **Bases de Dados SQL** a partir do menu do lado esquerdo e clique na sua base de dados na página **Bases de Dados SQL**. 
3. Na página **Overview (Descrição geral)** da sua base de dados, reveja o nome de servidor totalmente qualificado, como mostrado na imagem seguinte. Pode pairar sobre o nome do servidor para aparecer a opção **Clique para copiar**.

   ![informações da ligação](./media/sql-database-connect-query-ssms/connection-information.png) 

4. Se se tiver esquecido das informações de início de sessão do seu servidor da Base de Dados SQL do Azure, navegue para a página de servidor da Base de Dados SQL para visualizar o nome de administrador do servidor e, se necessário, repor a palavra-passe. 

## <a name="set-language-mode-to-sql"></a>Definir modo do idioma para SQL

Defina o modo de idioma para **SQL** no Visual Studio Code, para ativar comandos mssql e IntelliSense T-SQL.

1. Abra uma nova janela do Visual Studio Code. 

2. Clique em **Texto Simples** no canto inferior direito da barra de estado.
3. No menu pendente **Selecionar modo de idioma** que se abre, escreva **SQL** e, em seguida, prima **ENTER** para definir o modo de idioma para o SQL. 

   ![Modo de idioma do SQL](./media/sql-database-connect-query-vscode/vscode-language-mode.png)

## <a name="connect-to-your-database-in-the-sql-database-logical-server"></a>Ligue à base de dados no servidor lógico da Base de Dados SQL

Utilize o Visual Studio Code para estabelecer uma ligação ao servidor da Base de Dados SQL do Azure.

> [!IMPORTANT]
> Antes de continuar, certifique-se de que tem as informações do servidor, da base de dados e de início de sessão prontas. Assim que começar a introduzir as informações do perfil de ligação, se alterar o foco do Visual Studio Code, tem de reiniciar a criação do perfil de ligação.
>

1. No VS Code, prima **CTRL+SHIFT+P** (ou **F1**) para abrir a Paleta de Comandos.

2. Escreva **sqlcon** e prima **ENTER**.

3. Prima **ENTER** para selecionar **Criar Perfil de Ligação**. Esta ação cria um perfil de ligação para a sua instância do SQL Server.

4. Siga as indicações para especificar as propriedades de ligação do novo perfil de ligação. Depois de especificar cada valor, prima **ENTER** para continuar. 

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

5. Prima a tecla **ESC** para fechar a mensagem que informa que o perfil está criado e ligado.

6. Verifique a ligação na barra de estado.

   ![Estado da ligação](./media/sql-database-connect-query-vscode/vscode-connection-status.png)

## <a name="query-data"></a>Consultar dados

Utilize a declaração do Transact-SQL [SELECIONAR](https://msdn.microsoft.com/library/ms189499.aspx) para consultar dados na base de dados SQL do Azure.

1. Na janela **Editor**, introduza a consulta seguinte na janela de consulta vazia:

   ```sql
   SELECT pc.Name as CategoryName, p.name as ProductName
   FROM [SalesLT].[ProductCategory] pc
   JOIN [SalesLT].[Product] p
   ON pc.productcategoryid = p.productcategoryid;
   ```

2. Prima **CTRL+SHIFT+E** para obter dados das tabelas Produto e ProductCategory.

    ![Consulta](./media/sql-database-connect-query-vscode/query.png)

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

2. Prima **CTRL+SHIFT+E** para inserir uma nova linha na tabela Produto.

## <a name="update-data"></a>Atualizar dados

Utilize a declaração do Transact-SQL [ATUALIZAR](https://msdn.microsoft.com/library/ms177523.aspx) para atualizar dados na base de dados SQL do Azure.

1.  Na janela **Editor**, elimine a consulta anterior e introduza a seguinte consulta:

   ```sql
   UPDATE [SalesLT].[Product]
   SET [ListPrice] = 125
   WHERE Name = 'myNewProduct';
   ```

2. Prima **CTRL+SHIFT+E** para atualizar a linha especificada na tabela Produto.

## <a name="delete-data"></a>Eliminar dados

Utilize a declaração do Transact-SQL [ELIMINAR](https://msdn.microsoft.com/library/ms189835.aspx) para eliminar dados na base de dados SQL do Azure.

1. Na janela **Editor**, elimine a consulta anterior e introduza a seguinte consulta:

   ```sql
   DELETE FROM [SalesLT].[Product]
   WHERE Name = 'myNewProduct';
   ```

2. Prima **CTRL+SHIFT+E** para eliminar a linha especificada na tabela Produto.

## <a name="next-steps"></a>Passos seguintes

- Para ligar e consultar com o SQL Server Management Studio, veja [Ligar e consultar com SSMS](sql-database-connect-query-ssms.md)
- Para ligar e consultar com .NET, consulte [Connect and query with .NET (Ligar e consultar com .NET)](sql-database-connect-query-dotnet.md).
- Para ligar e consultar com PHP, consulte [Connect and query with PHP (Ligar e consultar com PHP)](sql-database-connect-query-php.md).
- Para ligar e consultar com Node.js, consulte [Connect and query with Node.js (Ligar e consultar com Node.js)](sql-database-connect-query-nodejs.md).
- Para ligar e consultar com Java, consulte [Connect and query with Java (Ligar e consultar com Java)](sql-database-connect-query-java.md).
- Para ligar e consultar com Python, consulte [Connect and query with Python (Ligar e consultar com Python)](sql-database-connect-query-python.md).
- Para ligar e consultar com Ruby, consulte [Connect and query with Ruby (Ligar e consultar com Ruby)](sql-database-connect-query-ruby.md).

