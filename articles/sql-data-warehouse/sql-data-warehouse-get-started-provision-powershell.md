---
title: Criar SQL Data Warehouse com o PowerShell | Microsoft Docs
description: Criar SQL Data Warehouse com o PowerShell
services: sql-data-warehouse
documentationcenter: NA
author: hirokib
manager: jhubbard
editor: 
ms.assetid: 97434863-7938-4129-8949-5a119f5949e3
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: create
ms.date: 10/31/2016
ms.author: elbutter;barbkess
ms.openlocfilehash: a763f1c600c1a3f37cb565a8eb7db3c3f27dcf75
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="create-sql-data-warehouse-using-powershell"></a>Criar SQL Data Warehouse com o PowerShell
> [!div class="op_single_selector"]
> * [Portal do Azure](sql-data-warehouse-get-started-provision.md)
> * [TSQL](sql-data-warehouse-get-started-create-database-tsql.md)
> * [PowerShell](sql-data-warehouse-get-started-provision-powershell.md)
>
>

Este artigo mostra-lhe como criar um SQL Data Warehouse com o PowerShell.

## <a name="prerequisites"></a>Pré-requisitos
Para começar, precisa do seguinte:

* **Conta Azure**: aceda a [Versão de Avaliação Gratuita do Azure][Azure Free Trial] ou [Créditos do MSDN Azure][MSDN Azure Credits] para criar uma conta.
* **Azure SQL Server**: veja [Create an Azure SQL database in the Azure Portal (Criar uma base de dados SQL do Azure com o Portal do Azure)][Create an Azure SQL database in the Azure Portal] ou [Create an Azure SQL database with PowerShell (Criar uma base de dados SQL do Azure com o PowerShell)][Create an Azure SQL database with PowerShell] para obter mais detalhes.
* **Grupo de recursos**: utilize o mesmo grupo de recursos do Azure SQL Server ou veja [como criar um grupo de recursos](../azure-resource-manager/resource-group-portal.md).
* **PowerShell, versão 1.0.3 ou superior**: pode verificar a sua versão, executando **Get-Module -ListAvailable -Name Azure**.  A versão mais recente pode ser instalada a partir do [Instalador de Plataforma Web da Microsoft][Microsoft Web Platform Installer].  Para mais informações sobre como instalar a versão mais recente, consulte [How to install and configure Azure PowerShell (Como instalar e configurar o Azure PowerShell)][How to install and configure Azure PowerShell].

> [!NOTE]
> A criação de um SQL Data Warehouse poderá resultar num novo serviço sujeito a faturação.  Consulte [SQL Data Warehouse pricing (Preços do SQL Data Warehouse)][SQL Data Warehouse pricing], para mais detalhes sobre preços.
>
>

## <a name="create-a-sql-data-warehouse"></a>Criar um SQL Data Warehouse
1. Abra o Windows PowerShell.
2. Execute este cmdlet para iniciar sessão no Azure Resource Manager.

    ```Powershell
    Login-AzureRmAccount
    ```
3. Selecione a subscrição que pretende utilizar para a sua sessão atual.

    ```Powershell
    Get-AzureRmSubscription    -SubscriptionName "MySubscription" | Select-AzureRmSubscription
    ```
4. Crie a base de dados. Este exemplo cria uma base de dados com o nome "mynewsqldw", com o nível de objetivo de serviço "DW400", para o servidor com o nome "sqldwserver1" que está no grupo de recursos chamado "mywesteuroperesgp1".

   ```Powershell
   New-AzureRmSqlDatabase -RequestedServiceObjectiveName "DW400" -DatabaseName "mynewsqldw" -ServerName "sqldwserver1" -ResourceGroupName "mywesteuroperesgp1" -Edition "DataWarehouse" -CollationName "SQL_Latin1_General_CP1_CI_AS" -MaxSizeBytes 10995116277760
   ```

Os parâmetros necessários são:

* **RequestedServiceObjectiveName**: a quantidade de [DWU][DWU] que está a solicitar.  Os valores suportados são DW100, DW200, DW300, DW400, DW500, DW600, DW1000, DW1200, DW1500, DW2000, DW3000 e DW6000.
* **DatabaseName**: o nome do SQL Data Warehouse que está a criar.
* **ServerName**: o nome do servidor que está a utilizar para a criação (tem de ser V12).
* **ResourceGroupName**: o grupo de recursos que está a utilizar.  Para localizar grupos de recursos disponíveis na sua subscrição, utilize Get-AzureResource.
* **Edição**: tem de ser "DataWarehouse", para criar um SQL Data Warehouse.

Os parâmetros opcionais são:

* **CollationName**: o agrupamento predefinido quando não é especificado é SQL_Latin1_General_CP1_CI_AS.  Não é possível alterar o agrupamento numa base de dados.
* **MaxSizeBytes**: o tamanho máximo predefinido das bases de dados é 10 GB.

Para obter mais detalhes sobre as opções de parâmetros, veja [New-AzureRmSqlDatabase][New-AzureRmSqlDatabase] e [Criar Base de Dados (Azure SQL Data Warehouse)][Create Database (Azure SQL Data Warehouse)].

## <a name="next-steps"></a>Passos seguintes
Após terminar o aprovisionamento do SQL Data Warehouse, poderá querer experimentar [carregar dados de exemplo][loading sample data] ou [verificar como programar][develop], [carregar][load], ou [migrar][migrate].

Se estiver interessado em obter mais informações sobre como gerir o SQL Data Warehouse através de programação, consulte o nosso artigo sobre como utilizar [PowerShell cmdlets and REST APIs (Cmdlets do PowerShell e APIs REST)][PowerShell cmdlets and REST APIs].

<!--Image references-->

<!--Article references-->
[DWU]: ./sql-data-warehouse-overview-what-is.md
[migrate]: ./sql-data-warehouse-overview-migrate.md
[develop]: ./sql-data-warehouse-overview-develop.md
[load]: ./sql-data-warehouse-load-with-bcp.md
[loading sample data]: ./sql-data-warehouse-load-sample-databases.md
[PowerShell cmdlets and REST APIs]: ./sql-data-warehouse-reference-powershell-cmdlets.md
[firewall rules]: ../sql-database-configure-firewall-settings.md

[How to install and configure Azure PowerShell]: /powershell/azureps-cmdlets-docs
[how to create a SQL Data Warehouse from the Azure Portal]: ./sql-data-warehouse-get-started-provision.md
[Create an Azure SQL database in the Azure Portal]: ../sql-database/sql-database-get-started.md
[Create an Azure SQL database with PowerShell]: ../sql-database/sql-database-get-started-powershell.md
[how to create a resource group]: ../azure-resource-manager/resource-group-template-deploy-portal.md#create-resource-group

<!--MSDN references-->
[MSDN]: https://msdn.microsoft.com/library/azure/dn546722.aspx
[New-AzureRmSqlDatabase]: https://msdn.microsoft.com/library/mt619339.aspx
[Create Database (Azure SQL Data Warehouse)]: https://msdn.microsoft.com/library/mt204021.aspx

<!--Other Web references-->
[Microsoft Web Platform Installer]: https://aka.ms/webpi-azps
[SQL Data Warehouse pricing]: https://azure.microsoft.com/pricing/details/sql-data-warehouse/
[Azure Free Trial]: https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F
[MSDN Azure Credits]: https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F
