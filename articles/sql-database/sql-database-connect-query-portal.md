---
title: 'Portal do Azure: Consultar a Base de Dados SQL do Azure com o Editor de Consultas | Microsoft Docs'
description: "Saiba como ligar à Base de Dados SQL no portal do Azure através do Editor de Consultas SQL. Em seguida, execute declarações do Transact-SQL (T-SQL) para consultar e editar dados."
metacanonical: 
keywords: "ligar à base de dados sql, portal do azure, portal, editor de consultas"
services: sql-database
documentationcenter: 
author: ayoolubeko
manager: jhubbard
editor: 
ms.assetid: 
ms.service: sql-database
ms.custom: mvc,DBs & servers
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 08/01/2017
ms.author: ayolubek
ms.openlocfilehash: 788b9d2e7ff9e1dba2aca5eca982e4ddf045a6c4
ms.sourcegitcommit: dfd49613fce4ce917e844d205c85359ff093bb9c
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/31/2017
---
# <a name="azure-portal-use-the-sql-query-editor-to-connect-and-query-data"></a>Portal do Azure: Utilizar o Editor de Consultas SQL para ligar e consultar dados

O Editor de Consultas SQL é uma ferramenta de consulta de browser que oferece uma forma simples e eficiente de executar consultas SQL na Base de Dados SQL do Azure ou no Azure SQL Data Warehouse sem sair do portal do Azure. Este guia de introdução demonstra como utilizar o Editor de Consultas para ligar a uma base de dados SQL e, em seguida, utilizar as instruções de Transact-SQL para consultar, inserir, atualizar e eliminar dados na base de dados. 

## <a name="prerequisites"></a>Pré-requisitos

Este guia de introdução utiliza como ponto de partida os recursos criados num destes guias de introdução:

- [Criar BD - Portal](sql-database-get-started-portal.md)
- [Criar BD - CLI](sql-database-get-started-cli.md)
- [Criar BD - PowerShell](sql-database-get-started-powershell.md)


## <a name="log-in-to-the-azure-portal"></a>Iniciar sessão no portal do Azure

Inicie sessão no [Portal do Azure](https://portal.azure.com/).


## <a name="connect-using-sql-authentication"></a>Ligar com a Autenticação SQL
> [!NOTE]
> Certifique-se de que a opção "Permitir acesso aos serviços do Azure" está definida como "ON" nas definições de firewall do SQL Server. Esta opção dá ao Editor de Consultas SQL acesso às suas bases de dados e armazéns de dados.

1. Clique em **Bases de dados SQL** no menu da esquerda e clique na base de dados que pretende consultar.

2. Na página Base de Dados SQL da sua base de dados, clique em **Ferramentas** na barra de ferramentas. A página Ferramentas abre.

    ![menu ferramentas](./media/sql-database-connect-query-portal/tools-menu.png)

3. Clique em **Editor de consultas (pré-visualização)**, clique na caixa de verificação **Termos de pré-visualização** e, em seguida, clique em **OK**. É aberta a página Editor de consultas.

4. Clique em **Iniciar sessão** e, em seguida, quando lhe for pedido, selecione **Autenticação do SQL Server** e forneça o início de sessão e a palavra-passe de administrador do servidor que indicou quando criou a base de dados.

    ![início de sessão](./media/sql-database-connect-query-portal/login-menu.png) 

5. Clique em **OK** para iniciar sessão.


## <a name="connect-using-azure-ad"></a>Ligar com o Azure AD

Configurar um administrador do Active Directory permite-lhe utilizar uma identidade única para iniciar sessão no portal do Azure e na base de dados SQL. Siga os passos abaixo para configurar um administrador do Active Directory para o SQL Server que criou.

> [!NOTE]
> As contas de e-mail (por exemplo, outlook.com, hotmail.com, live.com, gmail.com, yahoo.com) ainda não são suportadas como administradores do Active Directory. Certifique-se de que escolhe um utilizador que tenha sido criado nativamente no Azure Active Directory ou federado no Azure Active Directory.

1. Selecione **SQL Servers** no menu da esquerda e selecione o seu SQL Server na lista de servidores.

2. Selecione a definição **Administrador do Active Directory** no menu de definições do SQL Server.

3. No painel de administrador do Active Directory, clique no comando **Definir administrador** e selecione o utilizador ou grupo que será o administrador do Active Directory.

    ![selecionar o active directory](./media/sql-database-connect-query-portal/select-active-directory.png) 

4. Na parte superior do painel de administrador do Active Directory, clique no comando **Guardar** para definir o seu administrador do Active Directory.

Navegue até à base de dados SQL que gostaria de consultar, clique no comando **Ferramentas** na barra de ferramentas e selecione a opção **Editor de consultas (pré-visualização)**. A página do Editor de consultas abre e liga automaticamente à sua base de dados.


## <a name="run-query-using-query-editor"></a>Executar uma consulta com o Editor de Consultas

Quando estiver autenticado, escreva a consulta seguinte no painel do Editor de consultas para consultar os 20 principais produtos por categoria.

```sql
 SELECT TOP 20 pc.Name as CategoryName, p.name as ProductName
 FROM SalesLT.ProductCategory pc
 JOIN SalesLT.Product p
 ON pc.productcategoryid = p.productcategoryid;
```

Clique em **Executar** e, em seguida, reveja os resultados da consulta no painel **Resultados**.

![Resultados do editor de consultas](./media/sql-database-connect-query-portal/query-editor-results.png)

## <a name="insert-data-using-query-editor"></a>Inserir dados com o Editor de Consultas

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

2. Na barra de ferramentas, clique em **Executar** para inserir uma nova linha na tabela Produto.

## <a name="update-data-using-query-editor"></a>Atualizar dados com o Editor de Consultas

Utilize o seguinte código para atualizar o produto novo que foi adicionado anteriormente com uma declaração Transact-SQL [ATUALIZAR](https://msdn.microsoft.com/library/ms177523.aspx).

1. Na janela da consulta, substitua a consulta anterior pela seguinte consulta:

   ```sql
   UPDATE [SalesLT].[Product]
   SET [ListPrice] = 125
   WHERE Name = 'myNewProduct';
   ```

2. Na barra de ferramentas, clique em **Executar** para atualizar a linha especificada na tabela Produto.

## <a name="delete-data-using-query-editor"></a>Eliminar dados com o Editor de Consultas

Utilize o seguinte código para eliminar o produto novo que foi adicionado anteriormente com uma declaração Transact-SQL [ELIMINAR](https://msdn.microsoft.com/library/ms189835.aspx).

1. Na janela da consulta, substitua a consulta anterior pela seguinte consulta:

   ```sql
   DELETE FROM [SalesLT].[Product]
   WHERE Name = 'myNewProduct';
   ```

2. Na barra de ferramentas, clique em **Executar** para eliminar a linha especificada na tabela Produto.


## <a name="query-editor-considerations"></a>Considerações sobre o Editor de Consultas

Existem alguns aspetos a saber ao trabalhar com o Editor de Consultas enquanto permanecer no estado de pré-visualização:

1. Certifique-se de que definiu a opção "Permitir acesso aos serviços do Azure" nas definições de firewall do SQL Server como "ON". Esta opção dá ao Editor de Consultas SQL acesso às suas bases de dados SQL e armazéns de dados.

2. O início de sessão de Administrador do Azure Active Directory não funciona com contas que têm a autenticação de dois fatores ativada. 

3. As contas de e-mail (por exemplo, outlook.com, hotmail.com, live.com, gmail.com, yahoo.com) ainda não são suportadas como administradores do Active Directory. Certifique-se de que escolhe um utilizador que tenha sido criado nativamente no Azure Active Directory ou federado no Azure Active Directory.

4. As consultas de tipos de dados geográficos ainda não são suportadas no Editor de consultas. Consultar colunas de dados geográficos resultará num erro «System.IO.FileNotFoundException».

5. Não existe suporte de IntelliSense para vistas e tabelas de bases de dados. No entanto, o editor suporta a conclusão automática de nomes que já tenham sido escritos. 

6. Premir a tecla F5 atualiza a página do Editor de consultas e perde a consulta que está a ser executada. Utilize o botão Executar na barra de ferramentas para executar consultas.


## <a name="next-steps"></a>Passos seguintes

- Para obter mais informações sobre o Transact-SQL suportado em bases de dados SQL do Azure, veja [Diferenças de Transact-SQL na base de dados SQL](sql-database-transact-sql-information.md).
