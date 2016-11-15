---
title: Consultar o Azure SQL Data Warehouse (Visual Studio) | Microsoft Docs
description: Consultar o SQL Data Warehouse com o Visual Studio.
services: sql-data-warehouse
documentationcenter: NA
author: barbkess
manager: jhubbard
editor: 
ms.assetid: daace889-95e5-4826-b2fc-047eac9d6d95
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.date: 10/31/2016
ms.author: barbkess
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 5e97fefdf3cc13f2fd3e060da901e90ef5ef9a29


---
# <a name="query-azure-sql-data-warehouse-visual-studio"></a>Consultar o Azure SQL Data Warehouse (Visual Studio)
> [!div class="op_single_selector"]
> * [Power BI](sql-data-warehouse-get-started-visualize-with-power-bi.md)
> * [Azure Machine Learning](sql-data-warehouse-get-started-analyze-with-azure-machine-learning.md)
> * [Visual Studio](sql-data-warehouse-query-visual-studio.md)
> * [sqlcmd](sql-data-warehouse-get-started-connect-sqlcmd.md) 
> * [SSMS](sql-data-warehouse-query-ssms.md)
> 
> 

Utilize o Visual Studio para consultar o Azure SQL Data Warehouse em apenas alguns minutos. Este método utiliza a extensão SQL Server Data Tools (SSDT) no Visual Studio. 

## <a name="prerequisites"></a>Pré-requisitos
Para utilizar este tutorial, precisa do seguinte:

* Um SQL Data Warehouse existente. Para criar um, consulte [Criar um SQL Data Warehouse][Criar um SQL Data Warehouse].
* SSDT para Visual Studio. Se tiver o Visual Studio, provavelmente já os tem. Para instruções e opções de instalação, consulte [Instalar o Visual Studio e SSDT][Instalar o Visual Studio e SSDT].
* O nome de servidor SQL completamente qualificado. Para o descobrir, veja [Ligar ao SQL Data Warehouse][Ligar ao SQL Data Warehouse].

## <a name="1-connect-to-your-sql-data-warehouse"></a>1. Ligar ao seu SQL Data Warehouse
1. Abra o Visual Studio 2013 ou 2015.
2. Abra o SQL Server Object Explorer. Para tal, selecione **Vista** > **SQL Server Object Explorer**.
   
    ![SQL Server Object Explorer][1]
3. Clique no ícone **Adicionar SQL Server**.
   
    ![Adicionar SQL Server][2]
4. Preencha os campos na janela Ligar ao Servidor.
   
    ![Ligar ao Servidor][3]
   
   * **Nome do servidor**. Introduza o **nome do servidor** que identificou anteriormente.
   * **Autenticação**. Selecione **Autenticação do SQL Server** ou **Autenticação Integrada do Active Directory**.
   * **Nome de Utilizador** e **Palavra-passe**. Introduza o nome de utilizador e a palavra-passe, se a Autenticação do SQL Server tiver sido selecionada acima.
   * Clique em **Ligar**.
5. Para explorar, expanda o servidor SQL do Azure. Pode ver as bases de dados associadas ao servidor. Expanda AdventureWorksDW para ver as tabelas na sua base de dados de exemplo.
   
    ![Explorar AdventureWorksDW][4]

## <a name="2-run-a-sample-query"></a>2. Executar uma consulta de exemplo
Agora que foi estabelecida uma ligação à base de dados, vamos escrever uma consulta.

1. Clique com o botão direito do rato na base de dados no SQL Server Object Explorer.
2. Selecione **Nova Consulta**. É aberta uma nova janela de consulta.
   
    ![Nova consulta][5]
3. Copie esta consulta TSQL para a janela de consulta:
   
    ```sql
    SELECT COUNT(*) FROM dbo.FactInternetSales;
    ```
4. Execute a consulta. Para tal, clique na seta verde ou utilize o atalho seguinte: `CTRL`+`SHIFT`+`E`.
   
    ![Executar consulta][6]
5. Veja os resultados da consulta. Neste exemplo, a tabela FactInternetSales tem 60398 linhas.
   
    ![Resultados da consulta][7]

## <a name="next-steps"></a>Passos seguintes
Agora que pode ligar e efetuar consultas, experimente [visualizar os dados com o PowerBI][visualizar os dados com o PowerBI].

Para configurar o ambiente para a autenticação do Azure Active Directory, veja [Authenticate to SQL Data Warehouse (Autenticação no SQL Data Warehouse)][Authenticate to SQL Data Warehouse (Autenticação no SQL Data Warehouse)].

<!--Arcticles-->
[Ligar ao SQL Data Warehouse]: sql-data-warehouse-connect-overview.md
[Criar um SQL Data Warehouse]: sql-data-warehouse-get-started-provision.md
[Instalar o Visual Studio e SSDT]: sql-data-warehouse-install-visual-studio.md
[Authenticate to SQL Data Warehouse (Autenticação no SQL Data Warehouse)]: sql-data-warehouse-authentication.md
[visualizar os dados com o PowerBI]: sql-data-warehouse-get-started-visualize-with-power-bi.md  

<!--Other-->
[Portal do Azure]: https://portal.azure.com

<!--Image references-->

[1]: media/sql-data-warehouse-query-visual-studio/open-ssdt.png
[2]: media/sql-data-warehouse-query-visual-studio/add-server.png
[3]: media/sql-data-warehouse-query-visual-studio/connection-dialog.png
[4]: media/sql-data-warehouse-query-visual-studio/explore-sample.png
[5]: media/sql-data-warehouse-query-visual-studio/new-query2.png
[6]: media/sql-data-warehouse-query-visual-studio/run-query.png
[7]: media/sql-data-warehouse-query-visual-studio/query-results.png



<!--HONumber=Nov16_HO2-->


