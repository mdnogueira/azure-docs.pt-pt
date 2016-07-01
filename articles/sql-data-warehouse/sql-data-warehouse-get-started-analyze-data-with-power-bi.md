<properties
   pageTitle="Visualizar dados do SQL Data Warehouse com o Power BI | Microsoft Azure"
   description="Visualizar dados do SQL Data Warehouse com o Power BI"
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="lodipalm"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="03/03/2016"
   ms.author="lodipalm;barbkess;sonyama"/>

# Visualizar dados com o Power BI

Este tutorial mostra como utilizar o Power BI para ligar ao SQL Data Warehouse e criar algumas visualizações básicas.

> [AZURE.NOTE] Para concluir este tutorial, precisa de uma base de dados SQL Data Warehouse que tenha sido previamente carregada com a base de dados de exemplo AdventureWorksDW. [Criar um SQL Data Warehouse](sql-data-warehouse-get-started-provision.md) mostra como criar um.
>
> Se já tiver uma base de dados SQL Data Warehouse mas não tiver dados de exemplo, pode [carregar dados de exemplo manualmente][].

> [AZURE.VIDEO azure-sql-data-warehouse-sample-data-and-powerbi]

## Ligar à AdventureWorksDW

Para abrir o Power BI e ligar à base de dados AdventureWorksDW:

1. Inicie sessão no [Portal do Azure][].
2. Clique em **Bases de dados SQL** e selecione a sua base de dados AdventureWorks do SQL Data Warehouse.

    ![Localizar a base de dados][1]

3. Clique no botão “Abrir no Power BI”.

    ![Botão do Power BI][2]

4. Deverá agora ver a página de ligação do SQL Data Warehouse, apresentando o seu endereço Web da base de dados. Clique em seguinte.

    ![Ligação ao Power BI][3]

6. Introduza o seu nome de utilizador do Azure SQL server e a palavra-passe, e ficará completamente ligado à base de dados SQL Data Warehouse.

    ![Início de sessão no Power BI][4]

1. Depois de ter iniciado sessão no Power BI, clique no conjunto de dados AdventureWorksDW no painel esquerdo. Esta ação irá abrir a base de dados.

    ![No Power BI, abrir AdventureWorksDW][5]



## Criar um relatório do Power BI para analisar os dados de exemplo

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

## Passos seguintes
Agora que lhe demos algum tempo para entender os dados de exemplo, consulte como [programar][], [carregar][], ou [migrar][].

<!--Image references-->
[1]:./media/sql-data-warehouse-get-started-analyze-data-with-power-bi/pbi-find-database.png
[2]:./media/sql-data-warehouse-get-started-analyze-data-with-power-bi/pbi-button.png
[3]:./media/sql-data-warehouse-get-started-analyze-data-with-power-bi/pbi-connect-to-azure.png
[4]:./media/sql-data-warehouse-get-started-analyze-data-with-power-bi/pbi-sign-in.png
[5]:./media/sql-data-warehouse-get-started-analyze-data-with-power-bi/pbi-open-adventureworks.png
[6]:./media/sql-data-warehouse-get-started-analyze-data-with-power-bi/pbi-aggregatesales.png
[7]:./media/sql-data-warehouse-get-started-analyze-data-with-power-bi/pbi-map.png
[8]:./media/sql-data-warehouse-get-started-analyze-data-with-power-bi/pbi-chooseaxis.png
[9]:./media/sql-data-warehouse-get-started-analyze-data-with-power-bi/pbi-bar.png
[10]:./media/sql-data-warehouse-get-started-analyze-data-with-power-bi/pbi-prepare-line.png
[11]:./media/sql-data-warehouse-get-started-analyze-data-with-power-bi/pbi-line.png
[12]:./media/sql-data-warehouse-get-started-analyze-data-with-power-bi/pbi-save.png

<!--Article references-->
[migrar]: ./sql-data-warehouse-overview-migrate.md
[programar]: ./sql-data-warehouse-overview-develop.md
[carregar]: ./sql-data-warehouse-overview-load.md
[carregar dados de exemplo manualmente]: ./sql-data-warehouse-get-started-manually-load-samples.md
[Portal do Azure]: https://portal.azure.com/
[Power BI]: http://www.powerbi.com/
[Ligar ao SQL Data Warehouse]: ./sql-data-warehouse-integrate-power-bi.md
[Criar um SQL Data Warehouse]: ./sql-data-warehouse-get-started-provision.md



<!--HONumber=Jun16_HO2-->


