<properties
    pageTitle="Ligar à Base de Dados SQL com uma consulta C# | Microsoft Azure"
    description="Escreva um programa C# para consultar e ligar à base de dados SQL. Informações sobre endereços IP, cadeias de ligação, início de sessão seguro e Visual Studio gratuito."
    services="sql-database"
    keywords="consulta da base de dados c#, consulta c#, ligar à base de dados, SQL C#"
    documentationCenter=""
    authors="stevestein"
    manager="jhubbard"
    editor=""/>

<tags
    ms.service="sql-database"
    ms.workload="data-management"
    ms.tgt_pltfrm="na"
    ms.devlang="dotnet"
    ms.topic="get-started-article"
    ms.date="08/17/2016"
    ms.author="stevestein"/>




# Ligar a uma Base de Dados SQL com o Visual Studio

> [AZURE.SELECTOR]
- [Visual Studio](sql-database-connect-query.md)
- [SSMS](sql-database-connect-query-ssms.md)
- [Excel](sql-database-connect-excel.md)

Saiba como ligar a uma Base de Dados SQL do Azure no Visual Studio. 

## Pré-requisitos


Para ligar a uma Base de Dados SQL com o Visual Studio, precisa do seguinte: 


- Uma base de dados SQL à qual ligar. Este artigo utiliza a base de dados de exemplo **AdventureWorks**. Para obter a base de dados de exemplo AdventureWorks, consulte o artigo [Criar a base de dados de demonstração](sql-database-get-started.md).


- Visual Studio 2013 atualização 4 (ou posterior). A Microsoft fornece agora a Visual Studio Community *gratuitamente*.
 - [Visual Studio Community, transferir](http://www.visualstudio.com/products/visual-studio-community-vs)
 - [Mais opções para o Visual Studio gratuito](http://www.visualstudio.com/products/free-developer-offers-vs.aspx)




## Abra o Visual Studio através do Portal do Azure


1. Inicie sessão no [Portal do Azure](https://portal.azure.com/).

2. Clique em **Mais Serviços** > **Bases de Dados SQL**
3. Abra o painel da base de dados **AdventureWorks** localizando e clicando na base de dados *AdventureWorks*.

6. Clique no botão **Ferramentas** na parte superior do painel da base de dados:

    ![Nova consulta. Ligar ao servidor da Base de Dados SQL: SQL Server Management Studio](./media/sql-database-connect-query/tools.png)

7. Clique em **Abrir no Visual Studio** (se precisar do Visual Studio, clique na ligação de transferência):

    ![Nova consulta. Ligar ao servidor da Base de Dados SQL: SQL Server Management Studio](./media/sql-database-connect-query/open-in-vs.png)


8. O Visual Studio abre-se com a janela **Ligar ao Servidor** já definida para estabelecer ligação ao servidor e à base de dados que selecionou no portal.  (Clique em **Opções** para verificar se a ligação está definida para a base de dados correta.) Escreva a palavra-passe de administrador do servidor e clique em **Ligar**.


    ![Nova consulta. Ligar ao servidor da Base de Dados SQL: SQL Server Management Studio](./media/sql-database-connect-query/connect.png)


8. Se não tiver uma regra de firewall configurada para o endereço IP do seu computador, receberá a mensagem *Não é possível ligar*. Para criar uma regra de firewall, consulte o artigo [Configurar uma regra de firewall ao nível do servidor para a Base de Dados SQL do Azure](sql-database-configure-firewall-settings.md).


9. Depois de se ligar com êxito, a janela do **SQL Server Object Explorer** abre-se com uma ligação à sua base de dados.

    ![Nova consulta. Ligar ao servidor da Base de Dados SQL: SQL Server Management Studio](./media/sql-database-connect-query/sql-server-object-explorer.png)


## Executar uma consulta de exemplo

Agora que estabelecemos a ligação à base de dados, os passos seguintes mostram como executar uma consulta simples:

2. Clique com o botão direito do rato na base de dados e, em seguida, selecione **Nova Consulta**.

    ![Nova consulta. Ligar ao servidor da Base de Dados SQL: SQL Server Management Studio](./media/sql-database-connect-query/new-query.png)

3. Na janela da consulta, copie e cole o seguinte código.

        SELECT
        CustomerId
        ,Title
        ,FirstName
        ,LastName
        ,CompanyName
        FROM SalesLT.Customer;

4. Clique no botão **Executar** para executar a consulta:

    ![Êxito. Ligar ao servidor de Base de Dados SQL Server: Visual Studio](./media/sql-database-connect-query/run-query.png)

## Passos seguintes

- Abrir bases de dados SQL no Visual Studio com o SQL Server Data Tools. Para obter mais detalhes, consulte o artigo [SQL Server Data Tools (Ferramentas de Dados do SQL Server)](https://msdn.microsoft.com/library/hh272686.aspx).
- Para ligar a uma base de dados SQL utilizando código, consulte o artigo [Ligar à Base de Dados SQL utilizando o .NET (C#)](sql-database-develop-dotnet-simple.md).






<!--HONumber=Sep16_HO3-->


