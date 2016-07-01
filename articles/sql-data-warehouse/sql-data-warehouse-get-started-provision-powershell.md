<properties
   pageTitle="Criar SQL Data Warehouse com o Powershell | Microsoft Azure"
   description="Criar SQL Data Warehouse com o Powershell"
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
   ms.date="06/04/2016"
   ms.author="lodipalm;barbkess;sonyama"/>

# Criar SQL Data Warehouse com o Powershell

> [AZURE.SELECTOR]
- [Portal do Azure](sql-data-warehouse-get-started-provision.md)
- [TSQL](sql-data-warehouse-get-started-create-database-tsql.md)
- [PowerShell](sql-data-warehouse-get-started-provision-powershell.md)

## Pré-requisitos
Antes de começar, certifique-se de que tem os seguintes pré-requisitos.

- **Conta Azure**: consulte [Versão de Avaliação Gratuita do Azure][] ou [Créditos do MSDN Azure][] para criar uma conta.
- **V12 Azure SQL Server**: consulte [Criar um servidor lógico de Base de Dados SQL do Azure com o Portal do Azure][] ou [Criar um servidor lógico de Base de Dados SQL do Azure com o PowerShell][].
- **Nome do grupo de recursos**: utilize o mesmo grupo de recursos do V12 Azure SQL Server ou consulte [grupos de recursos][] para criar um novo grupo de recursos.
- **PowerShell, versão 1.0.3 ou superior**: pode verificar a sua versão, executando **Get-Module -ListAvailable -Name Azure**.  A versão mais recente pode ser instalada a partir do [Instalador de Plataforma Web da Microsoft][].  Para mais informações sobre como instalar a versão mais recente, consulte [Como instalar e configurar o Azure PowerShell][].

> [AZURE.NOTE] A criação de um novo SQL Data Warehouse poderá resultar num novo serviço sujeito a faturação.  Consulte [Preços do SQL Data Warehouse][], para mais detalhes sobre preços.

## Criar uma base de dados SQL Data Warehouse
1. Abra o Windows PowerShell.
2. Execute este cmdlet para iniciar sessão no Azure Resource Manager.

    ```Powershell
    Login-AzureRmAccount
    ```
    
3. Selecione a subscrição que pretende utilizar para a sua sessão atual.

    ```Powershell
    Get-AzureRmSubscription -SubscriptionName "MySubscription" | Select-AzureRmSubscription
    ```

4.  Crie a base de dados. Este exemplo cria uma nova base de dados com o nome "mynewsqldw", com o objetivo de nível de serviço "DW400", para o servidor com o nome "sqldwserver1" que está no grupo de recursos com o nome "mywesteuroperesgp1".

    ```Powershell
    New-AzureRmSqlDatabase -RequestedServiceObjectiveName "DW400" -DatabaseName "mynewsqldw" -ServerName "sqldwserver1" -ResourceGroupName "mywesteuroperesgp1" -Edition "DataWarehouse"
    ```

Os parâmetros necessários para este cmdlet são:

- **RequestedServiceObjectiveName**: a quantidade de DWU que está a solicitar, sob a forma "DWXXX". DWU representa uma alocação de CPU e memória.  Cada valor de DWU representa um aumento linear nestes recursos.  Os valores atualmente suportados são: 100, 200, 300, 400, 500, 600, 1.000, 1.200, 1.500, 2.000.
- **DatabaseName**: o nome do SQL Data Warehouse que está a criar.
- **ServerName**: o nome do servidor que está a utilizar para a criação (tem de ser V12).
- **ResourceGroupName**: o grupo de recursos que está a utilizar.  Para localizar grupos de recursos disponíveis na sua subscrição, utilize Get-AzureResource.
- **Edição**: tem de definir a edição para "DataWarehouse", para criar um SQL Data Warehouse.

Para mais detalhes sobre as opções de parâmetros, consulte [Criar Base de Dados (Azure SQL Data Warehouse)][].
Para a referência de comandos, consulte [New-AzureRmSqlDatabase][]

## Passos seguintes
Após terminar o aprovisionamento do SQL Data Warehouse, poderá querer experimentar [carregar dados de exemplo][] ou verificar como [desenvolver][], [carregar][], ou [migrar][].

Se estiver interessado em obter mais informações sobre como gerir o SQL Data Warehouse através de programação, consulte o nosso artigo sobre como utilizar [Cmdlets do PowerShell e APIs REST][].

<!--Image references-->

<!--Article references-->
[migrar]: ./sql-data-warehouse-overview-migrate.md
[desenvolver]: ./sql-data-warehouse-overview-develop.md
[carregar]: ./sql-data-warehouse-load-with-bcp.md
[carregar dados de exemplo]: ./sql-data-warehouse-get-started-manually-load-samples.md
[Cmdlets do PowerShell e APIs REST]: ./sql-data-warehouse-reference-powershell-cmdlets.md
[regras de firewall]: ./sql-database-configure-firewall-settings.md
[Como instalar e configurar o Azure PowerShell]: ../powershell/powershell-install-configure.md
[Como criar um SQL Data Warehouse a partir do Portal do Azure]: ./sql-data-warehouse-get-started-provision.md
[Criar um servidor lógico de Base de Dados SQL do Azure com o Portal do Azure]: ../sql-database/sql-database-get-started.md#create-an-azure-sql-database-logical-server
[Criar um servidor lógico de Base de Dados SQL do Azure com o PowerShell]: ../sql-database/sql-database-get-started-powershell.md#database-setup-create-a-resource-group-server-and-firewall-rule
[grupos de recursos]: ../azure-portal/resource-group-portal.md

<!--MSDN references--> 
[MSDN]:https://msdn.microsoft.com/library/azure/dn546722.aspx
[New-AzureRmSqlDatabase]: https://msdn.microsoft.com/library/mt619339.aspx
[Criar Base de Dados (Azure SQL Data Warehouse)]: https://msdn.microsoft.com/library/mt204021.aspx

<!--Other Web references-->
[Instalador de Plataforma Web da Microsoft]: https://aka.ms/webpi-azps
[Preços do SQL Data Warehouse]: https://azure.microsoft.com/pricing/details/sql-data-warehouse/
[Versão de Avaliação Gratuita do Azure]: https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F
[Créditos do MSDN Azure]: https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F



<!--HONumber=Jun16_HO2-->


