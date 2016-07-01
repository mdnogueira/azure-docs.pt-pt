<properties
   pageTitle="Criar um SQL Data Warehouse no Portal do Azure | Microsoft Azure"
   description="Saiba como criar um Azure SQL Data Warehouse no Portal do Azure"
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="barbkess"
   manager="jhubbard"
   editor=""
   tags="azure-sql-data-warehouse"/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="05/05/2016"
   ms.author="lodipalm;barbkess;sonyama"/>

# Criar um Azure SQL Data Warehouse

> [AZURE.SELECTOR]
- [Portal do Azure](sql-data-warehouse-get-started-provision.md)
- [TSQL](sql-data-warehouse-get-started-create-database-tsql.md)
- [PowerShell](sql-data-warehouse-get-started-provision-powershell.md)

Neste tutorial, irá utilizar o Portal do Azure para criar um SQL Data Warehouse que contém uma base de dados de exemplo AdventureWorksDW.


[AZURE.INCLUDE [free-trial-note](../../includes/free-trial-note.md)]


1. Inicie sessão no [Portal do Azure](https://portal.azure.com).

2. Clique em **+ Novo** > **Dados + Armazenamento** > **SQL Data Warehouse**.

    ![Criar](./media/sql-data-warehouse-get-started-provision/create-sample.gif)

3. No painel **SQL Data Warehouse**, preencha as informações necessárias e, em seguida, prima “Criar” para criar.

    ![Criar base de dados](./media/sql-data-warehouse-get-started-provision/create-database.png)

    - **Servidor**: recomendamos que selecione primeiro o servidor.  Pode selecionar um servidor existente ou [criar um novo](./sql-data-warehouse-get-started-new-server.md). 

    - **Nome da base de dados**: o nome que será utilizado para referenciar o SQL Data Warehouse.  Tem de ser exclusivo para o servidor.
    
    - **Desempenho**: recomendamos que comece com 400 DWUs. Pode mover o controlo de deslize para a esquerda ou direita para ajustar o desempenho do armazém de dados ou aumentar ou reduzir verticalmente após a criação.  Para saber mais sobre DWUs, consulte a nossa documentação sobre [dimensionamento](./sql-data-warehouse-manage-compute-overview.md) ou a [página de preços](https://azure.microsoft.com/en-us/pricing/details/sql-data-warehouse/). 

    - **Subscrição**: selecione a subscrição à qual este SQL Data Warehouse irá cobrar.

    - **Grupo de recursos**: os grupos de recursos são contentores concebidos para ajudar a gerir uma coleção de recursos do Azure. Saiba mais sobre [grupos de recursos](../azure-portal/resource-group-portal.md).

    - **Selecionar origem**: clique em **Selecionar origem** > **Exemplo**. Uma vez que, neste momento, apenas está disponível uma base de dados de exemplo, quando seleciona Exemplo, o Azure preenche automaticamente a opção **Selecionar exemplo** com AdventureWorksDW.

4. Clique em **Criar** para criar o SQL Data Warehouse.

5. Aguarde alguns minutos e o SQL Data Warehouse estará pronto. Quando terminar, deve ser reencaminhado para o [portal do Azure](https://portal.azure.com). Pode localizar o SQL Data Warehouse no seu dashboard, listado nas Bases de Dados SQL ou no grupo de recursos que utilizou para criá-lo. 

    ![Vista de portal](./media/sql-data-warehouse-get-started-provision/database-portal-view.png)

[AZURE.INCLUDE [SQL DataBase create server](../../includes/sql-database-create-new-server-firewall-portal.md)] 

## Passos seguintes

Agora que criou um SQL Data Warehouse, está pronto para [ligar](./sql-data-warehouse-get-started-connect.md) e começar a consultar.

Para carregar dados para o SQL Data Warehouse, consulte a [vista de carregamento](./sql-data-warehouse-overview-load.md).

Se estiver a tentar migrar uma base de dados existente para o SQL Data Warehouse, consulte [Descrição geral da migração](./sql-data-warehouse-overview-migrate.md) ou utilize o [Utilitário de Migração](./sql-data-warehouse-migrate-migration-utility.md).




<!--HONumber=Jun16_HO2-->


