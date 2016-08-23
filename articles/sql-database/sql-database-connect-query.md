<properties
    pageTitle="Ligar à Base de Dados SQL com uma consulta C# | Microsoft Azure"
    description="Escreva um programa C# para consultar e ligar à base de dados SQL. Informações sobre endereços IP, cadeias de ligação, início de sessão seguro e Visual Studio gratuito."
    services="sql-database"
    keywords="consulta da base de dados c#, consulta c#, ligar à base de dados, SQL C#"
    documentationCenter=""
    authors="MightyPen"
    manager="jhubbard"
    editor=""/>

<tags
    ms.service="sql-database"
    ms.workload="data-management"
    ms.tgt_pltfrm="na"
    ms.devlang="dotnet"
    ms.topic="get-started-article"
    ms.date="04/25/2016"
    ms.author="annemill"/>


# Ligar a uma Base de Dados SQL com o Visual Studio

> [AZURE.SELECTOR]
- [Visual Studio](sql-database-connect-query.md)
- [SSMS](sql-database-connect-query-ssms.md)
- [Excel](sql-database-connect-excel.md)

Saiba como ligar a uma Base de Dados SQL do Azure no Visual Studio. 

## Pré-requisitos


Para ligar à Base de Dados SQL com o Visual Studio, tem de ter: 


- Uma conta e subscrição do Azure. Pode inscrever-se para obter uma [versão de avaliação gratuita](https://azure.microsoft.com/pricing/free-trial/).


- Uma base de dados **AdventureWorksLT** de demonstração para o serviço de Base de Dados SQL do Azure.
 - [Crie a base de dados de demonstração](sql-database-get-started.md) em minutos.


- Visual Studio 2013 atualização 4 (ou posterior). A Microsoft fornece agora a Visual Studio Community *gratuitamente*.
 - [Visual Studio Community, transferir](http://www.visualstudio.com/products/visual-studio-community-vs)
 - [Mais opções para o Visual Studio gratuito](http://www.visualstudio.com/products/free-developer-offers-vs.aspx)
 - Ou, permitir que o [passo](#InstallVSForFree) posteriormente neste tópico descreva como o [Portal do Azure](https://portal.azure.com/) o irá orientar na instalação do Visual Studio.


<a name="InstallVSForFree" id="InstallVSForFree"></a>

&nbsp;

## Passo 1: Instalar a Visual Studio Community gratuitamente


Se precisar de instalar o Visual Studio, pode:

- Instalar a Visual Studio Community gratuitamente através da navegação do seu browser para páginas Web do produto Visual Studio, que fornece transferências gratuitas e outras opções; ou
- Permitir que o [Portal do Azure](https://portal.azure.com/) o oriente para a página de transferência Web, descrito a seguir.


### Visual Studio através do Portal do Azure


1. Inicie sessão no [Portal do Azure](https://portal.azure.com/), http://portal.azure.com/.

2. Clique em **BROWSE* ALL** > **Bases de dados SQL**. É aberto um painel para pesquisa de bases de dados.

3. Na caixa para filtrar texto perto da parte superior, comece a escrever o nome da sua base de dados **AdventureWorksLT**.

4. Quando vir a linha para a base de dados no servidor, clique na linha. É aberto um painel para a base de dados.

5. Para sua comodidade, clique no controlo de minimizar em cada painel anterior.

6. Clique no botão **Abrir no Visual Studio** perto da parte superior no painel da base de dados. É aberto um novo painel sobre o Visual Studio, com ligações para instalar localizações para o Visual Studio.

    ![Botão Abrir no Visual Studio][20-OpenInVisualStudioButton]

7. Clique na ligação **Comunidade (gratuita)** ou numa ligação semelhante. É adicionada uma nova página Web.

8. Utilize as ligações na nova página Web para instalar o Visual Studio.

9. Depois de o Visual Studio estar instalado, no painel **Abrir no Visual Studio**, clique no botão **Abrir no Visual Studio**. É aberto o Visual Studio.

10. Para o benefício do painel **SQL Server Object Explorer**, o Visual Studio solicita-lhe o preenchimento de campos de cadeia de ligação numa caixa de diálogo.
 - Escolha **Autenticação do SQL Server**, não **Autenticação do Windows**.
 - Lembre-se de especificar a sua base de dados **AdventureWorksLT** (**Opções** > **Propriedades da Ligação** na caixa de diálogo).

11. No **SQL Server Object Explorer**, expanda o nó para a base de dados.


## Passo 2: executar consultas de exemplo

Depois de ligar ao seu servidor lógico, pode ligar a uma base de dados e executar uma consulta de exemplo. 

1. No **Object Explorer**, navegue para uma base de dados no servidor para o qual tem permissão, como a base de dados de exemplo **AdventureWorks**.
2. Clique com o botão direito do rato na base de dados e, em seguida, selecione **Nova Consulta**.

    ![Nova consulta. Ligar ao servidor da Base de Dados SQL: SQL Server Management Studio](./media/sql-database-connect-query-ssms/4-run-query.png)

3. Na janela da consulta, copie e cole o seguinte código.

        SELECT
        CustomerId
        ,Title
        ,FirstName
        ,LastName
        ,CompanyName
        FROM SalesLT.Customer;

4. Clique no botão **Executar**.  A seguinte captura de ecrã mostra uma consulta com êxito.

    ![Êxito. Ligar ao servidor de Base de Dados SQL Server: Visual Studio](./media/sql-database-connect-query-ssms/5-success.png)

## Passos seguintes

[Ligar à Base de Dados SQL com o .NET (C#)](sql-database-develop-dotnet-simple.md) 


<!-- Image references. -->

[20-OpenInVisualStudioButton]: ./media/sql-database-connect-query/connqry-free-vs-e.png




<!--HONumber=Aug16_HO1-->


