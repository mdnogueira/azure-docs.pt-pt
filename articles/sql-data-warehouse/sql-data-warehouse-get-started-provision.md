---
title: Criar um SQL Data Warehouse no Portal do Azure | Microsoft Docs
description: Saiba como criar um Azure SQL Data Warehouse no Portal do Azure
services: sql-data-warehouse
documentationcenter: NA
author: barbkess
manager: jhubbard
editor: 
tags: azure-sql-data-warehouse
ms.assetid: 552e496e-d560-419c-9996-6bbc80c521cb
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: hero-article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.date: 10/31/2016
ms.author: barbkess
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 2cf6e81d6a74c4a5beff91a3df90780a83bf1327


---
# <a name="create-an-azure-sql-data-warehouse"></a>Criar um Azure SQL Data Warehouse
> [!div class="op_single_selector"]
> * [Portal do Azure](sql-data-warehouse-get-started-provision.md)
> * [TSQL](sql-data-warehouse-get-started-create-database-tsql.md)
> * [PowerShell](sql-data-warehouse-get-started-provision-powershell.md)
> 
> 

Este tutorial utiliza o Portal do Azure para criar um SQL Data Warehouse que contém uma base de dados de exemplo AdventureWorksDW.

## <a name="prerequisites"></a>Pré-requisitos
Para começar, precisa do seguinte:

* **Conta Azure**: aceda a [Versão de Avaliação Gratuita do Azure][Versão de Avaliação Gratuita do Azure] ou [Créditos do MSDN Azure][Créditos do MSDN Azure] para criar uma conta.
* **Azure SQL Server**: consulte [Create an Azure SQL Database logical server with the Azure Portal (Criar um servidor lógico de Base de Dados SQL do Azure com o Portal do Azure)][Create an Azure SQL Database logical server with the Azure portal (Criar um servidor lógico de Base de Dados SQL do Azure com o Portal do Azure)] para obter mais detalhes.

> [!NOTE]
> A criação de um SQL Data Warehouse poderá resultar num novo serviço sujeito a faturação.  Consulte [Preços do SQL Data Warehouse][Preços do SQL Data Warehouse], para mais detalhes.
> 
> 

## <a name="create-a-sql-data-warehouse"></a>Criar um SQL Data Warehouse
1. Inicie sessão no [Portal do Azure](https://portal.azure.com).
2. Clique em **+ Novo** > **Dados + Armazenamento** > **SQL Data Warehouse**.
   
    ![Criar](./media/sql-data-warehouse-get-started-provision/create-sample.gif)
3. No painel **SQL Data Warehouse**, preencha as informações necessárias e, em seguida, prima “Criar” para criar.
   
    ![Criar base de dados](./media/sql-data-warehouse-get-started-provision/create-database.png)
   
   * **Servidor**: recomendamos que selecione primeiro o servidor.  
   * **Nome da base de dados**: o nome que é utilizado para referenciar o SQL Data Warehouse.  Tem de ser exclusivo para o servidor.
   * **Desempenho**: recomendamos que comece com 400 [DWUs][DWU]. Pode mover o controlo de deslize para a esquerda ou direita para ajustar o desempenho do armazém de dados ou aumentar ou reduzir verticalmente após a criação.  Para saber mais sobre DWUs, consulte a nossa documentação sobre [dimensionamento](sql-data-warehouse-manage-compute-overview.md) ou a nossa [página de preços][Preços do SQL Data Warehouse]. 
   * **Subscrição**: selecione a [subscrição] na qual será faturado este SQL Data Warehouse.
   * **Grupo de recursos**: os [Grupos de recursos][Grupo de recursos] são contentores concebidos para ajudar a gerir uma coleção de recursos do Azure. Saiba mais sobre [grupos de recursos](../azure-resource-manager/resource-group-overview.md).
   * **Selecionar origem**: clique em **Selecionar origem** > **Exemplo**. O Azure preenche automaticamente a opção **Selecionar exemplo** com AdventureWorksDW.

> [!NOTE]
> O agrupamento predefinido de um SQL Data Warehouse é SQL_Latin1_General_CP1_CI_AS. Se for necessário um agrupamento diferente, pode ser utilizado [T-SQL][T-SQL] para criar a base de dados com outro agrupamento.
> 
> 

1. Clique em **Criar** para criar o SQL Data Warehouse.
2. Aguarde alguns minutos. Quando o armazém de dados estiver pronto, deve regressar ao [Portal do Azure](https://portal.azure.com). Pode localizar o SQL Data Warehouse no seu dashboard, listado nas Bases de Dados SQL ou no grupo de recursos que utilizou para criá-lo. 
   
    ![vista de portal](./media/sql-data-warehouse-get-started-provision/database-portal-view.png)

[!INCLUDE [SQL Database create server](../../includes/sql-database-create-new-server-firewall-portal.md)]

## <a name="next-steps"></a>Passos seguintes
Agora que criou um SQL Data Warehouse, está pronto para [Ligar](sql-data-warehouse-connect-overview.md) e começar a consultar.

Para carregar dados para o SQL Data Warehouse, consulte a [vista de carregamento](sql-data-warehouse-overview-load.md).

Se estiver a tentar migrar uma base de dados existente para o SQL Data Warehouse, consulte [Descrição geral da migração](sql-data-warehouse-overview-migrate.md) ou utilize o [Utilitário de Migração](sql-data-warehouse-migrate-migration-utility.md).

As regras da firewall também podem ser configuradas com o Transact-SQL. Para obter mais informações, consulte [sp_set_firewall_rule][sp_set_firewall_rule] e [sp_set_database_firewall_rule][sp_set_database_firewall_rule].

Também é uma excelente ideia consultar as [Melhores práticas][Melhores práticas].

<!--Article references-->
[Create an Azure SQL Database logical server with the Azure Portal (Criar um servidor lógico de Base de Dados SQL do Azure com o Portal do Azure)]: ../sql-database/sql-database-get-started.md#create-an-azure-sql-database-logical-server
[Create an Azure SQL Database logical server with PowerShell (Criar um servidor lógico de Base de Dados SQL do Azure com o PowerShell)]: ../sql-database/sql-database-get-started-powershell.md#database-setup-create-a-resource-group-server-and-firewall-rule
[grupos de recursos]: ../resource-group-template-deploy-portal.md
[Melhores práticas]: sql-data-warehouse-best-practices.md
[DWU]: sql-data-warehouse-overview-what-is.md#data-warehouse-units
[subscrição]: ../azure-glossary-cloud-terminology.md#subscription
[grupo de recursos]: ../azure-glossary-cloud-terminology.md#resource-group
[T-SQL]: ./sql-data-warehouse-get-started-create-database-tsql.md

<!--MSDN references-->
[sp_set_firewall_rule]: https://msdn.microsoft.com/library/dn270017.aspx
[sp_set_database_firewall_rule]: https://msdn.microsoft.com/library/dn270010.aspx

<!--Other Web references-->
[Preços do SQL Data Warehouse]: https://azure.microsoft.com/pricing/details/sql-data-warehouse/
[Versão de Avaliação Gratuita do Azure]: https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F
[Créditos do MSDN Azure]: https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F




<!--HONumber=Nov16_HO2-->


