---
title: "Módulo do serviço de migração de base de dados do Azure de utilização do Microsoft Azure PowerShell para migrar o SQL Server no local para a BD SQL do Azure | Microsoft Docs"
description: Saiba como migrar do SQL Server no local para o Azure SQL com o Azure PowerShell.
services: database-migration
author: HJToland3
ms.author: jtoland
manager: 
ms.reviewer: 
ms.service: database-migration
ms.workload: data-services
ms.custom: mvc
ms.topic: article
ms.date: 11/10/2017
ms.openlocfilehash: ad6469fcf86aeb7a0076ab5909fbe593596df695
ms.sourcegitcommit: afc78e4fdef08e4ef75e3456fdfe3709d3c3680b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/16/2017
---
# <a name="migrate-sql-server-on-premises-to-azure-sql-db-using-azure-powershell"></a>Migrar do SQL Server no local para a BD SQL do Azure com o Azure PowerShell
Neste artigo, migra a **Adventureworks2012** base de dados restaurada para uma instância no local do SQL Server 2016 ou posterior para uma base de dados do SQL do Azure utilizando o Microsoft Azure PowerShell.  Pode migrar bases de dados de uma instância do SQL Server no local para a SQL Database do Azure, utilizando o `AzureRM.DataMigration` módulo no Microsoft Azure PowerShell.

Neste artigo, saiba como:
> [!div class="checklist"]
> * Crie um grupo de recursos.
> * Crie uma instância do serviço de migração de base de dados do Azure.
> * Crie um projeto de migração numa instância de serviço de migração de base de dados do Azure.
> * Execute a migração.


## <a name="prerequisites"></a>Pré-requisitos
Para concluir estes passos, tem de:

- [SQL Server 2016 ou posterior](https://www.microsoft.com/sql-server/sql-server-downloads) (qualquer edição)
- Protocolo de TCP/IP está desativado por predefinição com a instalação do SQL Server Express. Ativá-la seguindo o [instruções deste artigo](https://docs.microsoft.com/sql/database-engine/configure-windows/enable-or-disable-a-server-network-protocol#SSMSProcedure).
- Para configurar o seu [Firewall do Windows para acesso ao motor de base de dados](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access).
- Uma instância de SQL Database do Azure. Pode criar uma instância de SQL Database do Azure, seguindo o detalhe no artigo [criar uma base de dados SQL do Azure no portal do Azure](https://docs.microsoft.com/azure/sql-database/sql-database-get-started-portal).
- [Assistente de migração de dados](https://www.microsoft.com/download/details.aspx?id=53595) v3.3 ou posterior.
- Serviço de migração de base de dados do Azure requer uma VNET criada utilizando o modelo de implementação Azure Resource Manager, que fornece a conectividade de site a site para os seus servidores de origem no local através de um [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) ou [ VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways).
- Concluída a avaliação da sua migração no local da base de dados e o esquema utilizando o Assistente de migração de dados, tal como descrito no artigo [ efetuar uma avaliação de migração do SQL Server](https://docs.microsoft.com/sql/dma/dma-assesssqlonprem)
- Transfira e instale o módulo de AzureRM.DataMigration da utilização da galeria do PowerShell [cmdlet do PowerShell do módulo de instalação](https://docs.microsoft.com/powershell/module/powershellget/Install-Module?view=powershell-5.1)
- As credenciais utilizadas para ligar à instância do SQL Server de origem tem de ter [controlo servidor](https://docs.microsoft.com/sql/t-sql/statements/grant-server-permissions-transact-sql) permissões.
- As credenciais utilizadas para ligar à instância de BD SQL do Azure de destino tem de ter permissão controlar base de dados nas bases de dados de BD SQL do Azure de destino.

## <a name="log-in-to-your-microsoft-azure-subscription"></a>Inicie sessão na sua subscrição do Microsoft Azure
Utilize as instruções no artigo [iniciar sessão com o Azure PowerShell](https://docs.microsoft.com/powershell/azure/authenticate-azureps?view=azurermps-4.4.1) para iniciar sessão sua subscrição do Azure utilizando o PowerShell.


## <a name="create-a-resource-group"></a>Criar um grupo de recursos
Um grupo de recursos do Azure é um contentor lógico no qual os recursos do Azure são implementados e geridos. Crie um grupo de recursos antes de poder criar uma máquina virtual.

Criar um grupo de recursos utilizando o [New-AzureRmResourceGroup](https://docs.microsoft.com/powershell/module/azurerm.resources/new-azurermresourcegroup?view=azurermps-4.4.1) comando. 

O exemplo seguinte cria um grupo de recursos denominado *myResourceGroup* no *EastUS* região.

```powershell
New-AzureRmResourceGroup -ResourceGroupName myResourceGroup -Location EastUS
```

## <a name="create-an-azure-database-migration-service-instance"></a>Criar uma instância de serviço de migração de base de dados do Azure 
Pode criar nova instância do serviço de migração de base de dados do Azure utilizando o `New-AzureRmDataMigrationService` cmdlet. Este cmdlet espera os seguintes parâmetros necessários:
- *Nome do grupo de recursos do Azure*. Pode utilizar [New-AzureRmResourceGroup](https://docs.microsoft.com/powershell/module/azurerm.resources/new-azurermresourcegroup?view=azurermps-4.4.1) comando para criar o grupo de recursos do Azure mostrado anteriormente e forneça o nome como um parâmetro.
- *Nome do serviço*. Cadeia que corresponda ao nome do serviço exclusivo pretendido para o serviço de migração de base de dados do Azure 
- *Localização*. Especifica a localização do serviço. Especifique uma localização de centro de dados do Azure, como EUA Oeste ou Sudeste asiático
- *SKU*. Este parâmetro corresponde ao nome do DMS Sku. Os nomes de Sku atualmente suportados são *Basic_1vCore*, *Basic_2vCores*, *GeneralPurpose_4vCores*
- *Identificador da sub-rede virtual*. Pode utilizar o cmdlet [New-AzureRmVirtualNetworkSubnetConfig](https://docs.microsoft.com/powershell/module/azurerm.network/new-azurermvirtualnetworksubnetconfig?view=azurermps-4.4.1) para criar uma sub-rede. 

O exemplo seguinte cria um serviço com o nome *MyDMS* no grupo de recursos *MyDMSResourceGroup*, que está localizado na *EUA Leste* região com uma sub-rede virtual chamado *MySubnet*.

```powershell
$service = New-AzureRmDms -ResourceGroupName myResourceGroup `
  -ServiceName MyDMS `
  -Location EastUS `
  -Sku Basic_2vCores `  
  -VirtualSubnetId
$vnet.Id`
```

## <a name="create-a-migration-project"></a>Criar um projeto de migração
Depois de criar uma instância de serviço de migração de base de dados do Azure, crie um projeto de migração. Um projeto de serviço de migração de base de dados do Azure necessita de informações de ligação para ambas as instâncias de origem e de destino, bem como uma lista de bases de dados que pretende migrar como parte do projeto.

### <a name="create-a-database-connection-info-object-for-the-source-and-target-connections"></a>Criar um objeto de informações de ligação de base de dados para as ligações de origem e de destino
Pode criar um objeto de informações de ligação de base de dados utilizando o `New-AzureRmDmsConnInfo` cmdlet.  Este cmdlet espera os seguintes parâmetros:
- *Tipo*. O tipo de ligação de base de dados, por exemplo, SQL Server, Oracle ou pedido MySQL. Utilize o SQL Server para SQL Server e SQL Azure.
- *Origem de dados*. O nome ou o IP de uma instância do SQL Server ou o servidor de SQL Azure.
- *AuthType*. O tipo de autenticação para a ligação, que pode ser SqlAuthentication ou WindowsAuthentication.
- *TrustServerCertificate* parâmetro define um valor que indica se o canal é encriptado ao ignorando walking a cadeia de certificados para validar a confiança. Valor pode ser true ou false.

O exemplo seguinte cria o objeto de informações de ligação para a origem de SQL Server chamado MySQLSourceServer utilizando a autenticação sql 

```powershell
$sourceConnInfo = New-AzureRmDmsConnInfo -ServerType SQL `
  -DataSource MySQLSourceServer `
  -AuthType SqlAuthentication `
  -TrustServerCertificate:$true
```

Exemplo seguinte mostra a criação das informações de ligação para o servidor de base de dados SQL Azure chamado MySQLAzureTarget utilizando a autenticação sql

```powershell
$targetConnInfo = New-AzureRmDmsConnInfo -ServerType SQL `
  -DataSource "mysqlazuretarget.database.windows.net" `
  -AuthType SqlAuthentication `
  -TrustServerCertificate:$false
```

### <a name="provide-databases-for-the-migration-project"></a>Fornecer as bases de dados para o projeto de migração
Criar uma lista de `AzureRmDataMigrationDatabaseInfo` objetos que especifica o projeto de bases de dados como parte da migração de base de dados do Azure que podem ser fornecidos como parâmetro para a criação do projeto. Cmdlet `New-AzureRmDataMigrationDatabaseInfo` pode ser utilizado para criar AzureRmDataMigrationDatabaseInfo. 

O exemplo seguinte cria `AzureRmDataMigrationDatabaseInfo` projeto para a base de dados AdventureWorks2016 e adiciona-o à lista de ser fornecido como parâmetro para a criação do projeto.

```powershell
$dbInfo1 = New-AzureRmDataMigrationDatabaseInfo -SourceDatabaseName AdventureWorks2016
$dbList = @($dbInfo1)
```

### <a name="create-a-project-object"></a>Criar um objeto de projeto
Por fim, pode criar projeto de migração de base de dados do Azure designado *MyDMSProject* localizados em *EUA Leste* utilizando `New-AzureRmDataMigrationProject` e adicionar as ligações de origem e destino criadas anteriormente e a lista de bases de dados para migrar.

```powershell
$project = New-AzureRmDataMigrationProject -ResourceGroupName myResourceGroup `
  -ServiceName $service.Name `
  -ProjectName MyDMSProject `
  -Location EastUS `
  -SourceType SQL `
  -TargetType SQLDB `
  -SourceConnection $sourceConnInfo `
  -TargetConnection $targetConnInfo `
  -DatabaseInfos $dbList
```

## <a name="create-and-start-a-migration-task"></a>Criar e iniciar uma tarefa de migração

Por fim, crie e inicie a tarefa de migração de base de dados do Azure. Tarefa de migração de base de dados do Azure requer informações de credenciais de ligação de origem e destino e de lista de tabelas de base de dados a serem migradas além das informações de que já são fornecidas com o projeto criado como um pré-requisito. 

### <a name="create-credential-parameters-for-source-and-target"></a>Criar credencial parâmetros para a origem e de destino

Credenciais de segurança de ligação podem ser criadas como [PSCredential](https://docs.microsoft.com/dotnet/api/system.management.automation.pscredential?redirectedfrom=MSDN&view=powershellsdk-1.1.0) objeto. 

O exemplo seguinte mostra a criação de *PSCredential* objetos para ligações de origem e destino fornecer palavras-passe como variáveis de cadeia *$sourcePassword* e *$ targetPassword*. 

```powershell
secpasswd = ConvertTo-SecureString -String $sourcePassword -AsPlainText -Force
$sourceCred = New-Object System.Management.Automation.PSCredential ($sourceUserName, $secpasswd)
$secpasswd = ConvertTo-SecureString -String $targetPassword -AsPlainText -Force
$targetCred = New-Object System.Management.Automation.PSCredential ($targetUserName, $secpasswd)
```

### <a name="create-a-table-map-and-select-source-and-target-parameters-for-migration"></a>Criar um mapa de tabela e selecionados parâmetros de origem e destino para migração
Outro parâmetro necessário para a migração é mapeamento de tabelas de origem para destino a ser migrada. Crie dicionário de tabelas que fornece um mapeamento entre as tabelas de origem e de destino para migração. O exemplo a seguir ilustra o mapeamento entre as tabelas de origem e destino esquema de recursos humanos para a base de dados AdventureWorks 2016.

```powershell
$tableMap = New-Object 'system.collections.generic.dictionary[string,string]'
$tableMap.Add("HumanResources.Department", "HumanResources.Department")
$tableMap.Add("HumanResources.Employee","HumanResources.Employee")
$tableMap.Add("HumanResources.EmployeeDepartmentHistory","HumanResources.EmployeeDepartmentHistory")
$tableMap.Add("HumanResources.EmployeePayHistory","HumanResources.EmployeePayHistory")
$tableMap.Add("HumanResources.JobCandidate","HumanResources.JobCandidate")
$tableMap.Add("HumanResources.Shift","HumanResources.Shift")
```

O passo seguinte consiste em selecionar as bases de dados de origem e de destino e fornecer o mapeamento de tabela para migrar como um parâmetro utilizando o `New-AzureRmDmsSqlServerSqlDbSelectedDB` cmdlet, conforme mostrado no exemplo seguinte:

```powershell
$selectedDbs = New-AzureRmDmsSqlServerSqlDbSelectedDB -Name AdventureWorks2016 `
  -TargetDatabaseName AdventureWorks2016 `
  -TableMap $tableMap
```

### <a name="create-and-start-a-migration-task"></a>Criar e iniciar uma tarefa de migração

Utilize o `New-AzureRmDataMigrationTask` cmdlet para criar e iniciar uma tarefa de migração. Este cmdlet espera os seguintes parâmetros:
- *TaskType*.  Tipo de tarefa de migração para criar para o SQL Server para o tipo de migração do SQL Azure *MigrateSqlServerSqlDb* é esperado. 
- *Nome do grupo de recursos*. Nome do grupo de recursos do Azure na qual pretende criar a tarefa.
- *ServiceName*.  Instância de serviço de migração de base de dados do Azure na qual pretende criar a tarefa.
- *ProjectName*. Nome do projeto de migração de base de dados do Azure na qual pretende criar a tarefa. 
- *TaskName*. Nome de tarefa a ser criado. 
- *Ligação de origem*. Objeto de AzureRmDmsConnInfo que representa a ligação de origem.
- *Ligação de destino*. Objeto de AzureRmDmsConnInfo que representa a ligação de destino.
- *SourceCred*. [PSCredential](https://docs.microsoft.com/dotnet/api/system.management.automation.pscredential?redirectedfrom=MSDN&view=powershellsdk-1.1.0) objeto para ligar ao servidor de origem.
- *TargetCred*. [PSCredential](https://docs.microsoft.com/dotnet/api/system.management.automation.pscredential?redirectedfrom=MSDN&view=powershellsdk-1.1.0) objeto para ligar ao servidor de destino.
- *SelectedDatabase*. Objeto de AzureRmDmsSqlServerSqlDbSelectedDB que representa o mapeamento de base de dados de origem e de destino.

O exemplo seguinte cria e inicia uma tarefa de migração com o nome myDMSTask:

```powershell
$migTask = New-AzureRmDataMigrationTask -TaskType MigrateSqlServerSqlDb `
  -ResourceGroupName myResourceGroup `
  -ServiceName $service.Name `
  -ProjectName $project.Name `
  -TaskName myDMSTask `
  -SourceConnection $sourceConnInfo `
  -SourceCred $sourceCred `
  -TargetConnection $targetConnInfo `
  -TargetCred $targetCred `
  -SelectedDatabase  $selectedDbs`
```

## <a name="monitor-the-migration"></a>Monitorizar a migração
Pode monitorizar a tarefa de migração em execução ao consultar a propriedade de estado da tarefa, conforme mostrado no exemplo seguinte:

```powershell
if (($task.Properties.State -eq "Running") -or ($task.Properties.State -eq "Queued"))
{
  write-host "migration task running"
}
```

## <a name="next-steps"></a>Passos seguintes
- Reveja as orientações de migração no [guia de migração de base de dados do Microsoft](https://datamigration.microsoft.com/)