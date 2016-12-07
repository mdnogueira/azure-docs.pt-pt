---
title: Criar SQL Data Warehouse com o PowerShell | Microsoft Docs
description: Criar SQL Data Warehouse com o PowerShell
services: sql-data-warehouse
documentationcenter: NA
author: barbkess
manager: jhubbard
editor: 
ms.assetid: 97434863-7938-4129-8949-5a119f5949e3
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.date: 10/31/2016
ms.author: barbkess
translationtype: Human Translation
ms.sourcegitcommit: 5a101aa78dbac4f1a0edb7f414b44c14db392652
ms.openlocfilehash: 7107c64a25b3f60d7789cec57181eec9ce844f6b


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

* **Conta Azure**: aceda a [Versão de Avaliação Gratuita do Azure][Versão de Avaliação Gratuita do Azure] ou [Créditos do Azure MSDN][Créditos do Azure MSDN] para criar uma conta.
* **Azure SQL Server**: veja [Criar um servidor lógico de Base de Dados SQL do Azure com o Portal do Azure][Criar um servidor lógico de Base de Dados SQL do Azure com o Portal do Azure] ou [Criar um servidor lógico de Base de Dados SQL do Azure com o PowerShell][Criar um servidor lógico de Base de Dados SQL do Azure com o PowerShell] para obter mais detalhes.
* **Grupo de recursos**: utilize o mesmo grupo de recursos do Azure SQL Server ou veja [como criar um grupo de recursos][como criar um grupo de recursos].
* **PowerShell, versão 1.0.3 ou superior**: pode verificar a sua versão, executando **Get-Module -ListAvailable -Name Azure**.  A versão mais recente pode ser instalada a partir do [Instalador de Plataforma Web da Microsoft][Instalador de Plataforma Web da Microsoft].  Para obter mais informações sobre como instalar a versão mais recente, veja [Como instalar e configurar o Azure PowerShell][Como instalar e configurar o Azure PowerShell].

> [!NOTE]
> A criação de um SQL Data Warehouse poderá resultar num novo serviço sujeito a faturação.  Veja [Preços do SQL Data Warehouse][Preços do SQL Data Warehouse], para obter mais detalhes sobre preços.
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

Para obter mais detalhes sobre as opções de parâmetros, veja [New-AzureRmSqlDatabase][New-AzureRmSqlDatabase] e [Criar Base de Dados (Azure SQL Data Warehouse)][Criar Base de Dados (Azure SQL Data Warehouse)].

## <a name="next-steps"></a>Passos seguintes
Após terminar o aprovisionamento do SQL Data Warehouse, poderá querer experimentar [carregar os dados de exemplo][carregar os dados de exemplo] ou verificar como [programar][programar], [carregar][carregar], ou [migrar][migrar].

Se estiver interessado em obter mais informações sobre como gerir o SQL Data Warehouse através de programação, consulte o nosso artigo sobre como utilizar [Cmdlets do PowerShell e APIs REST][Cmdlets do PowerShell e APIs REST].

<!--Image references-->

<!--Article references-->
[DWU]: ./sql-data-warehouse-overview-what-is.md#data-warehouse-units
[migrar]: ./sql-data-warehouse-overview-migrate.md
[programar]: ./sql-data-warehouse-overview-develop.md
[carregar]: ./sql-data-warehouse-load-with-bcp.md
[carregar os dados de exemplo]: ./sql-data-warehouse-load-sample-databases.md
[Cmdlets do PowerShell e APIs REST]: ./sql-data-warehouse-reference-powershell-cmdlets.md
[regras de firewall]: ../sql-database-configure-firewall-settings.md

[Como instalar e configurar o Azure PowerShell]: ../powershell-install-configure.md
[como criar um SQL Data Warehouse a partir do Portal do Azure]: ./sql-data-warehouse-get-started-provision.md
[Criar um servidor lógico de Base de Dados SQL do Azure com o Portal do Azure]: ../sql-database/sql-database-get-started.md#create-logical-server-bk
[Criar um servidor lógico de Base de Dados SQL do Azure com o PowerShell]: ../sql-database/sql-database-get-started-powershell.md#database-setup-create-a-resource-group-server-and-firewall-rule
[como criar um grupo de recursos]: ../resource-group-template-deploy-portal.md#create-resource-group

<!--MSDN references-->
[MSDN]: https://msdn.microsoft.com/library/azure/dn546722.aspx
[New-AzureRmSqlDatabase]: https://msdn.microsoft.com/library/mt619339.aspx
[Criar Base de Dados (Azure SQL Data Warehouse)]: https://msdn.microsoft.com/library/mt204021.aspx

<!--Other Web references-->
[Instalador de Plataforma Web da Microsoft]: https://aka.ms/webpi-azps
[Preços do SQL Data Warehouse]: https://azure.microsoft.com/pricing/details/sql-data-warehouse/
[Versão de Avaliação Gratuita do Azure]: https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F
[Créditos do Azure MSDN]: https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F



<!--HONumber=Nov16_HO2-->


