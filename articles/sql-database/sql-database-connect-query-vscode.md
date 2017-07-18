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
ms.custom: mvc,DBs & servers
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 06/20/2017
ms.author: carlrab
ms.translationtype: Human Translation
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.openlocfilehash: 4076b1e7ab3a70009217a1deff72da4bff0dc871
ms.contentlocale: pt-pt
ms.lasthandoff: 07/08/2017


---
# <a name="azure-sql-database-use-visual-studio-code-to-connect-and-query-data"></a>Base de Dados SQL do Azure: utilize o Visual Studio Code para ligar e consultar dados

O [Visual Studio Code](https://code.visualstudio.com/docs) é um editor de código gráfico para Linux, macOS e Windows que suporta extensões, incluindo a [extensão mssql](https://aka.ms/mssql-marketplace) para consultar o Microsoft SQL Server, a Base de Dados SQL do Azure e o SQL Data Warehouse. Este guia de introdução demonstra como utilizar o Visual Studio Code para ligar a uma base de dados SQL do Azure e, em seguida, utilizar as declarações de Transact-SQL para consultar, inserir, atualizar e eliminar dados na base de dados.

## <a name="prerequisites"></a>Pré-requisitos

Este guia de introdução utiliza como ponto de partida os recursos criados num destes guias de introdução:

- [Criar BD - Portal](sql-database-get-started-portal.md)
- [Criar BD - CLI](sql-database-get-started-cli.md)
- [Criar BD - PowerShell](sql-database-get-started-powershell.md)

Antes de começar, certifique-se de que instalou a versão mais recente do [Visual Studio Code](https://code.visualstudio.com/Download) e carregou a [extensão mssql](https://aka.ms/mssql-marketplace). Para obter orientações sobre a instalação da extensão mssql, consulte [Install VS Code (Instalar VS Code)](https://docs.microsoft.com/sql/linux/sql-server-linux-develop-use-vscode#install-vs-code) e [mssql for Visual Studio Code (mssql para Visual Studio Code)](https://marketplace.visualstudio.com/items?itemName=ms-mssql.mssql). 

## <a name="configure-vs-code"></a>Configurar Código VS 

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

### <a name="linux-ubuntu"></a>**Linux (Ubuntu)**

Nenhuma configuração especial necessária.

### <a name="windows"></a>**Windows**

Nenhuma configuração especial necessária.

## <a name="sql-server-connection-information"></a>Informações de ligação do servidor SQL

Obtenha as informações de ligação necessárias para ligar à base de dados SQL do Azure. Irá precisar do nome de servidor totalmente qualificado, do nome de base de dados e das informações de início de sessão nos seguintes procedimentos.

1. Inicie sessão no [Portal do Azure](https://portal.azure.com/).
2. Selecione **Bases de Dados SQL** a partir do menu do lado esquerdo e clique na sua base de dados na página **Bases de Dados SQL**. 
3. Na página **Overview (Descrição geral)** da sua base de dados, reveja o nome de servidor totalmente qualificado, como mostrado na imagem seguinte. Pode pairar sobre o nome do servidor para aparecer a opção **Clique para copiar**.

   ![informações da ligação](./media/sql-database-connect-query-dotnet/server-name.png) 

4. Se se tiver esquecido das informações de início de sessão do seu servidor da Base de Dados SQL do Azure, navegue para a página de servidor da Base de Dados SQL para visualizar o nome de administrador do servidor e, se necessário, repor a palavra-passe. 

## <a name="set-language-mode-to-sql"></a>Definir modo do idioma para SQL

Defina o modo de idioma para **SQL** no Visual Studio Code, para ativar comandos mssql e IntelliSense T-SQL.

1. Abra uma nova janela do Visual Studio Code. 

2. Clique em **Texto Simples** no canto inferior direito da barra de estado.
3. No menu pendente **Selecionar modo de idioma** que se abre, escreva **SQL** e, em seguida, prima **ENTER** para definir o modo de idioma para o SQL. 

   ![Modo de idioma do SQL](./media/sql-database-connect-query-vscode/vscode-language-mode.png)

## <a name="connect-to-your-database"></a>Ligar à base de dados

Utilize o Visual Studio Code para estabelecer uma ligação ao servidor da Base de Dados SQL do Azure.

> [!IMPORTANT]
> Antes de continuar, certifique-se de que tem as informações do servidor, da base de dados e de início de sessão prontas. Assim que começar a introduzir as informações do perfil de ligação, se alterar o foco do Visual Studio Code, tem de reiniciar a criação do perfil de ligação.
>

1. No VS Code, prima **CTRL+SHIFT+P** (ou **F1**) para abrir a Paleta de Comandos.

2. Escreva **sqlcon** e prima **ENTER**.

3. Prima **ENTER** para selecionar **Criar Perfil de Ligação**. Esta ação cria um perfil de ligação para a sua instância do SQL Server.

4. Siga as indicações para especificar as propriedades de ligação do novo perfil de ligação. Depois de especificar cada valor, prima **ENTER** para continuar. 

   | Definição       | Valor sugerido | Descrição |
   | ------------ | ------------------ | ------------------------------------------------- | 
   | **Nome do servidor | O nome de servidor completamente qualificado | O nome deve ser semelhante a: **mynewserver20170313.database.windows.net**. |
   | **Nome da base de dados** | mySampleDatabase | O nome da base de dados à qual se pretende ligar. |
   | **Autenticação** | Início de sessão do SQL| A Autenticação do SQL é o único tipo de autenticação que configurámos neste tutorial. |
   | **Nome de utilizador** | A conta de administrador do servidor | Esta é a conta que especificou quando criou o servidor. |
   | **Palavra-passe (Início de Sessão do SQL)** | A palavra-passe da sua conta de administrador do servidor | Esta é a palavra-passe que especificou quando criou o servidor. |
   | **Guardar Palavra-passe?** | Sim ou Não | Selecione Sim se não deseja escrever a palavra-passe sempre que inicia sessão. |
   | **Introduza um nome para este perfil** | Um nome de perfil, como **mySampleDatabase** | Um nome de perfil guardado acelera a sua ligação nos inícios de sessão subsequentes. | 

5. Prima a tecla **ESC** para fechar a mensagem que informa que o perfil está criado e ligado.

6. Verifique a ligação na barra de estado.

   ![Estado da ligação](./media/sql-database-connect-query-vscode/vscode-connection-status.png)

## <a name="query-data"></a>Consultar dados

Utilize o seguinte código para consultar os 20 melhores produtos por categoria com uma declaração Transact-SQL [SELECIONAR](https://msdn.microsoft.com/library/ms189499.aspx).

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

Utilize o seguinte código para inserir um novo produto na tabela SalesLT.Product com uma declaração Transact-SQL [INSERIR](https://msdn.microsoft.com/library/ms174335.aspx).

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

Utilize o seguinte código para atualizar o produto novo que foi adicionado anteriormente com uma declaração Transact-SQL [ATUALIZAR](https://msdn.microsoft.com/library/ms177523.aspx).

1.  Na janela **Editor**, elimine a consulta anterior e introduza a seguinte consulta:

   ```sql
   UPDATE [SalesLT].[Product]
   SET [ListPrice] = 125
   WHERE Name = 'myNewProduct';
   ```

2. Prima **CTRL+SHIFT+E** para atualizar a linha especificada na tabela Produto.

## <a name="delete-data"></a>Eliminar dados

Utilize o seguinte código para eliminar o produto novo que foi adicionado anteriormente com uma declaração Transact-SQL [ELIMINAR](https://msdn.microsoft.com/library/ms189835.aspx).

1. Na janela **Editor**, elimine a consulta anterior e introduza a seguinte consulta:

   ```sql
   DELETE FROM [SalesLT].[Product]
   WHERE Name = 'myNewProduct';
   ```

2. Prima **CTRL+SHIFT+E** para eliminar a linha especificada na tabela Produto.

## <a name="next-steps"></a>Passos seguintes

- Para ligar e consultar com o SQL Server Management Studio, veja [Connect and query with SSMS](sql-database-connect-query-ssms.md) (Ligar e consultar com o SSMS).
- Para ler um artigo de revista MSDN sobre a utilização do Visual Studio Code, veja [Create a database IDE with MSSQL extension blog post](https://msdn.microsoft.com/magazine/mt809115) (Criar uma base de dados IDE com a mensagem de blogue de extensão do MSSQL).

