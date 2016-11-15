---
title: Visualizar dados do SQL Data Warehouse com o Power BI Microsoft Azure
description: Visualizar dados do SQL Data Warehouse com o Power BI
services: sql-data-warehouse
documentationcenter: NA
author: barbkess
manager: jhubbard
editor: 
ms.assetid: d7fb89d1-da1d-4788-a111-68d0e3fda799
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.date: 10/31/2016
ms.author: barbkess
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: f498f4546e8c23f2141d8d30160a360fa0fc2514


---
# <a name="visualize-data-with-power-bi"></a>Visualizar dados com o Power BI
> [!div class="op_single_selector"]
> * [Power BI](sql-data-warehouse-get-started-visualize-with-power-bi.md)
> * [Azure Machine Learning](sql-data-warehouse-get-started-analyze-with-azure-machine-learning.md)
> * [Visual Studio](sql-data-warehouse-query-visual-studio.md)
> * [sqlcmd](sql-data-warehouse-get-started-connect-sqlcmd.md) 
> * [SSMS](sql-data-warehouse-query-ssms.md)
> 
> 

Este tutorial mostra como utilizar o Power BI para ligar ao SQL Data Warehouse e criar algumas visualizações básicas.

> [!VIDEO https://channel9.msdn.com/Blogs/Windows-Azure/Azure-SQL-Data-Warehouse-Sample-Data-and-PowerBI/player]
> 
> 

## <a name="prerequisites"></a>Pré-requisitos
Para seguir este tutorial, é necessário:

* Um SQL Data Warehouse pré-carregado com a base de dados da AdventureWorksDW. Para proceder ao aprovisionamento, consulte [Create a SQL Data Warehouse (Criar um SQL Data Warehouse)][Create a SQL Data Warehouse (Criar um SQL Data Warehouse)] e opte por carregar os dados de exemplo. Se já tiver um armazém de dados, mas não tiver dados de exemplo, pode [carregar dados de exemplo manualmente][carregar dados de exemplo manualmente].

## <a name="1-connect-to-your-database"></a>1. Ligar à base de dados
Para abrir o Power BI e ligar à base de dados AdventureWorksDW:

1. Inicie sessão no [Portal do Azure][Portal do Azure].
2. Clique em **Bases de dados SQL** e selecione a sua base de dados AdventureWorks do SQL Data Warehouse.
   
    ![Localizar a base de dados][1]
3. Clique no botão “Abrir no Power BI”.
   
    ![Botão do Power BI][2]
4. Deverá agora ver a página de ligação do SQL Data Warehouse, apresentando o seu endereço Web da base de dados. Clique em seguinte.
   
    ![Ligação ao Power BI][3]
5. Introduza o seu nome de utilizador do Azure SQL server e a palavra-passe, e ficará completamente ligado à base de dados SQL Data Warehouse.
   
    ![Início de sessão no Power BI][4]
6. Depois de ter iniciado sessão no Power BI, clique no conjunto de dados AdventureWorksDW no painel esquerdo. Esta ação irá abrir a base de dados.
   
    ![No Power BI, abrir AdventureWorksDW][5]

## <a name="2-create-a-report"></a>2. Criar um relatório
Está agora pronto para utilizar o Power BI para analisar os dados de exemplo da AdventureWorksDW. Para efetuar a análise, a AdventureWorksDW tem uma vista denominada AggregateSales. Esta vista contém algumas das métricas principais para analisar as vendas da empresa.

1. Para criar um mapa do montante de vendas, de acordo com o código postal, no painel direito de campos, clique na vista AggregateSales para expandi-la. Clique nas colunas PostalCode e SalesAmount para as selecionar.
   
    ![No Power BI, selecionar AggregateSales][6]
   
    O Power BI reconhece automaticamente estes dados geográficos e coloca-os num mapa para si.
   
    ![Mapa do Power BI][7]
2. Este passo cria um gráfico de barras que mostra a quantidade de vendas por receitas de cliente. Para criar isto, vá para a vista AggregateSales expandida. Clique no campo SalesAmount. Arraste o campo Receitas de Cliente para a esquerda e solte-o no Eixo.
   
    ![No Power BI, selecionar eixo][8]
   
    Movemos o gráfico de barras para o lado esquerdo.
   
    ![Barra do Power BI][9]
3. Este passo cria um gráfico de linhas que mostra o montante de vendas por data de encomenda. Para criar isto, vá para a vista AggregateSales expandida. Clique em SalesAmount e OrderDate. Na coluna Visualizações, clique no ícone Gráfico de Linhas; é o primeiro ícone na segunda linha em visualizações.
   
    ![No Power BI, selecionar gráfico de linhas][10]
   
    Tem agora um relatório que mostra as três visualizações diferentes dos dados.
   
    ![Linha do Power BI][11]

Pode guardar o seu progresso em qualquer altura, clicando em **Ficheiro** e selecionando **Guardar**.

## <a name="next-steps"></a>Passos seguintes
Agora que lhe demos algum tempo para entender os dados de exemplo, consulte como [programar][programar], [carregar][carregar], [migrar][migrar]. Em alternativa, consulte o [site do Power BI][site do Power BI].

<!--Image references-->
[1]: media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-find-database.png
[2]: media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-button.png
[3]: media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-connect-to-azure.png
[4]: media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-sign-in.png
[5]: media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-open-adventureworks.png
[6]: media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-aggregatesales.png
[7]: media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-map.png
[8]: media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-chooseaxis.png
[9]: media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-bar.png
[10]: media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-prepare-line.png
[11]: media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-line.png
[12]: media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-save.png

<!--Article references-->
[migrar]: sql-data-warehouse-overview-migrate.md
[programar]: sql-data-warehouse-overview-develop.md
[carregar]: sql-data-warehouse-overview-load.md
[carregar dados de exemplo manualmente]: sql-data-warehouse-load-sample-databases.md
[Ligar ao SQL Data Warehouse]: sql-data-warehouse-integrate-power-bi.md
[Criar um SQL Data Warehouse]: sql-data-warehouse-get-started-provision.md

<!--Other-->
[Portal do Azure]: https://portal.azure.com/
[site do Power BI]: http://www.powerbi.com/



<!--HONumber=Nov16_HO2-->


