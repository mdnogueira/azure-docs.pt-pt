<properties
   pageTitle="Ligar ao SQL Data Warehouse com o Visual Studio | Microsoft Azure"
   description="Introdução à ligação ao SQL Data Warehouse e execução de algumas consultas."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="sonyam"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="05/13/2016"
   ms.author="sonyama;barbkess"/>

# Ligar ao SQL Data Warehouse com o Visual Studio

> [AZURE.SELECTOR]
- [Visual Studio](sql-data-warehouse-get-started-connect.md)
- [SQLCMD](sql-data-warehouse-get-started-connect-sqlcmd.md)
- [AAD](sql-data-warehouse-get-started-connect-aad-authentication.md)

Estas instruções mostram como ligar a um Azure SQL Data Warehouse em apenas alguns minutos, utilizando a extensão SQL Server Data Tools (SSDT) no Visual Studio. Assim que estiver ligado, irá executar uma consulta simples.

## Pré-requisitos

+ Os dados de exemplo da AdventureWorksDW no SQL Data Warehouse. Para criá-lo, consulte [Criar um SQL Data Warehouse][].
+ SQL Server Data Tools para Visual Studio. Para instruções e opções de instalação, consulte [Instalar o Visual Studio e SSDT][].

## Passo 1: localizar o seu nome de servidor completamente qualificado do SQL do Azure

A sua Base de Dados SQL Data Warehouse está associada a um Azure SQL Server. Para ligar à base de dados, é necessário o nome completamente qualificado do servidor (**servername**. database.windows.net*).

Descubra o nome de servidor completamente qualificado.

1. Aceda ao [Portal do Azure][].
2. Clique em **bases de dados SQL** e clique na base de dados à qual pretende ligar. Este exemplo utiliza a base de dados de exemplo AdventureWorksDW.
3. Localize o nome do servidor completo.

    ![Nome de servidor completo][1]

## Passo 2: ligar ao SQL Data Warehouse

1. Abra o Visual Studio 2013 ou 2015.
2. Abra o SQL Server Object Explorer. Para tal, selecione **Vista** > **SQL Server Object Explorer**.

    ![SQL Server Object Explorer][2]

3. Clique no ícone **Adicionar SQL Server**.

    ![Adicionar SQL Server][3]

4. Preencha os campos na janela Ligar ao Servidor.

    ![Ligar ao Servidor][4]

    - **Nome do servidor**. Introduza o **nome do servidor** que identificou anteriormente.
    - **Autenticação**. Selecione **Autenticação do SQL Server** ou **Autenticação Integrada do Active Directory**.
    - **Nome de Utilizador** e **Palavra-passe**. Introduza o nome de utilizador e a palavra-passe, se a Autenticação do SQL Server tiver sido selecionada acima.
    - Clique em **Ligar**.

5. Para explorar, expanda o servidor SQL do Azure. Pode ver as bases de dados associadas ao servidor. Expanda AdventureWorksDW para ver as tabelas na sua base de dados de exemplo.

    ![Explorar AdventureWorksDW][5]

## Passo 3: Executar uma consulta de exemplo

Agora que foi estabelecida uma ligação à base de dados, vamos escrever uma consulta.

1. Clique com o botão direito do rato na base de dados no SQL Server Object Explorer.

2. Selecione **Nova Consulta**. É aberta uma nova janela de consulta.

    ![Nova consulta][6]

3. Copie esta consulta TSQL para a janela de consulta:

    ```sql
    SELECT COUNT(*) FROM dbo.FactInternetSales;
    ```

4. Execute a consulta. Para tal, clique na seta verde ou utilize o atalho seguinte: `CTRL`+`SHIFT`+`E`.

    ![Executar consulta][7]

5. Veja os resultados da consulta. Neste exemplo, a tabela FactInternetSales tem 60398 linhas.

    ![Resultados da consulta][8]

## Passos seguintes

Agora que pode ligar e efetuar consultas, experimente [visualizar os dados com o PowerBI][].

Para configurar o ambiente para a autenticação do Windows, consulte [Ligar à Base de Dados SQL ou SQL Data Warehouse, utilizando a Autenticação do Azure Active Directory][].

<!--Arcticles-->
[Criar um SQL Data Warehouse]: sql-data-warehouse-get-started-provision.md
[Instalar o Visual Studio e SSDT]: sql-data-warehouse-install-visual-studio.md
[Ligar à Base de Dados SQL ou SQL Data Warehouse, utilizando a Autenticação do Azure Active Directory]: ../sql-data-warehouse/sql-data-warehouse-get-started-connect-aad-authentication.md
[visualizar os dados com o PowerBI]: ./sql-data-warehouse-get-started-visualize-with-power-bi.md  

<!--Other-->
[Portal do Azure]: https://portal.azure.com

<!--Image references-->

[1]: ./media/sql-data-warehouse-get-started-connect/get-server-name.png
[2]: ./media/sql-data-warehouse-get-started-connect/open-ssdt.png
[3]: ./media/sql-data-warehouse-get-started-connect/add-server.png
[4]: ./media/sql-data-warehouse-get-started-connect/connection-dialog.png
[5]: ./media/sql-data-warehouse-get-started-connect/explore-sample.png
[6]: ./media/sql-data-warehouse-get-started-connect/new-query2.png
[7]: ./media/sql-data-warehouse-get-started-connect/run-query.png
[8]: ./media/sql-data-warehouse-get-started-connect/query-results.png



<!--HONumber=Jun16_HO2-->


