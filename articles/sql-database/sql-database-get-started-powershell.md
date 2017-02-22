---
title: "PowerShell: Introdução à Base de Dados SQL do Azure | Microsoft Docs"
description: "Saiba como criar um servidor lógico da Base de Dados SQL, regras de firewall ao nível do servidor e bases de dados com o PowerShell. Saiba também como consultar bases de dados com o PowerShell."
keywords: "criar nova base de dados sql, configuração da base de dados"
services: sql-database
documentationcenter: 
author: stevestein
manager: jhubbard
editor: cgronlun
ms.assetid: 7d99869b-cec5-4583-8c1c-4c663f4afd4d
ms.service: sql-database
ms.custom: single databases
ms.devlang: NA
ms.topic: hero-article
ms.tgt_pltfrm: powershell
ms.workload: data-management
ms.date: 12/09/2016
ms.author: sstein
translationtype: Human Translation
ms.sourcegitcommit: 93efe1a08149e7c027830b03a9e426ac5a05b27b
ms.openlocfilehash: cf626be4914168d3ed3caae7f959a79324487b4e


---

# <a name="tutorial-provision-and-access-an-azure-sql-database-using-powershell"></a>Tutorial: Aprovisionar e aceder a uma base de dados SQL do Azure com o PowerShell

Neste tutorial de introdução irá aprender a utilizar o PowerShell para:

* Criar um novo grupo de recursos do Azure
* Criar um servidor lógico do SQL do Azure
* Ver as propriedades do servidor do SQL do Azure
* Criar uma regra de firewall ao nível do servidor
* Criar a base de dados de exemplo do AdventureWorksLT como base de dados única
* Ver as propriedades da base de dados de exemplo do AdventureWorksLT
* Criar uma base de dados única em branco

Neste tutorial, também irá:

* Ligar ao servidor lógico e à respetiva base de dados mestra
* Ver as propriedades da base de dados mestra
* Ligar à base de dados de exemplo
* Ver as propriedades da base de dados de utilizador

Quando terminar este tutorial, terá uma base de dados de exemplo e uma base de dados vazia em execução num grupo de recursos do Azure e ligadas a um servidor lógico. Também terá uma regra de firewall ao nível do servidor configurada para permitir que o principal ao nível de servidor inicie sessão no servidor a partir de um endereço IP especificado (ou um intervalo de endereços IP). 

**Estimativa de tempo:** este tutorial irá demorar, aproximadamente, 30 minutos (assumindo que já cumpre os pré-requisitos).


> [!TIP]
> Pode realizar estas mesmas tarefas num tutorial de introdução com [o portal do Azure](sql-database-get-started.md).
>


## <a name="prerequisites"></a>Pré-requisitos

* Precisa de uma conta do Azure. Pode [abrir uma conta do Azure gratuita](https://azure.microsoft.com/free/) ou [Ativar as vantagens de subscritor do Visual Studio](https://azure.microsoft.com/pricing/member-offers/msdn-benefits/). 

* Tem de iniciar sessão com uma conta que seja membro do proprietário da subscrição ou da função de contribuinte. Para obter mais informações sobre o controlo de acesso baseado em funções (RBAC), veja o artigo [Introdução à gestão de acesso no portal do Azure](../active-directory/role-based-access-control-what-is.md).

* Precisa do ficheiro .bacpac da base de dados de amostra do AdventureWorksLT no armazenamento de blobs do Azure

### <a name="download-the-adventureworkslt-sample-database-bacpac-file-and-save-it-in-azure-blob-storage"></a>Transfira o ficheiro .bacpac da base de dados de amostra do AdventureWorksLT e guarde-o no armazenamento de blobs do Azure

Este tutorial cria uma nova base de dados do AdventureWorksLT através da importação de um ficheiro .bacpac do Armazenamento do Azure. O primeiro passo consiste em receber uma cópia do AdventureWorksLT.bacpac e carregá-lo para o armazenamento de blobs.
Os seguintes passos preparam a base de dados de exemplo para importar:

1. [Transfira o AdventureWorksLT.bacpac](https://sqldbbacpacs.blob.core.windows.net/bacpacs/AdventureWorksLT.bacpac) e guarde-o com uma extensão de ficheiro .bacpac.
2. [Criar uma conta de armazenamento](../storage/storage-create-storage-account.md#create-a-storage-account) - pode criar a conta de armazenamento com as predefinições.
3. Crie um novo **Contentor** ao navegar para a conta de armazenamento, selecione **Blobs**e, em seguida, clique em **+ Contentor**.
4. Carregue o ficheiro .bacpac para o contentor de blobs na sua conta de armazenamento. Pode utilizar o botão **Carregar** na parte superior da página do contentor ou [utilizar o AzCopy](../storage/storage-use-azcopy.md#blob-upload). 
5. Depois de guardar o AdventureWorksLT.bacpac, precisa da chave da conta de armazenamento e o URL para o fragmento de código de importação posteriormente neste tutorial. 
   * Selecione o ficheiro bacpac e copie o URL. Será semelhante a https://{storage-account-name}.blob.core.windows.net/{container-name}/AdventureWorksLT.bacpac. Na página da conta de armazenamento, clique em **Chaves de acesso** e copie a **chave1**.


[!INCLUDE [Start your PowerShell session](../../includes/sql-database-powershell.md)]


## <a name="create-a-new-logical-sql-server-using-azure-powershell"></a>Criar um novo servidor SQL lógico com o Azure PowerShell

Precisa de um grupo de recursos para conter o servidor, pelo que o primeiro passo é criar um novo grupo de recursos e o servidor ([New-AzureRmResourceGroup](https://docs.microsoft.com/powershell/resourcemanager/azurerm.resources/v3.3.0/new-azurermresourcegroup), [New-AzureRmSqlServer](https://docs.microsoft.com/powershell/resourcemanager/azurerm.sql/v2.3.0/new-azurermsqlserver)), ou obter as referências para existentes ([Get-AzureRmResourceGroup](https://docs.microsoft.com/powershell/resourcemanager/azurerm.resources/v3.3.0/get-azurermresourcegroup), [Get-AzureRmSqlServer](https://docs.microsoft.com/powershell/resourcemanager/azurerm.sql/v2.3.0/get-azurermsqlserver)).
Os fragmentos seguintes irão criar um grupo de recursos e o servidor do SQL do Azure caso já não existam:

Para obter uma lista de localizações do Azure válidas e o formato de cadeia, consulte a secção abaixo [Fragmentos do programa auxiliar](#helper-snippets).
```
# Create new, or get existing resource group
############################################

$resourceGroupName = "{resource-group-name}"
$resourceGroupLocation = "{resource-group-location}"

$myResourceGroup = Get-AzureRmResourceGroup -Name $resourceGroupName -ea SilentlyContinue

if(!$myResourceGroup)
{
   Write-Output "Creating resource group: $resourceGroupName"
   $myResourceGroup = New-AzureRmResourceGroup -Name $resourceGroupName -Location $resourceGroupLocation
}
else
{
   Write-Output "Resource group $resourceGroupName already exists:"
}
$myResourceGroup



# Create a new, or get existing server
######################################

$serverName = "{server-name}"
$serverVersion = "12.0"
$serverLocation = $resourceGroupLocation
$serverResourceGroupName = $resourceGroupName

$serverAdmin = "{server-admin}"
$serverAdminPassword = "{server-admin-password}"

$securePassword = ConvertTo-SecureString -String $serverAdminPassword -AsPlainText -Force
$serverCreds = New-Object -TypeName System.Management.Automation.PSCredential -ArgumentList $serverAdmin, $securePassword

$myServer = Get-AzureRmSqlServer -ServerName $serverName -ResourceGroupName $serverResourceGroupName -ea SilentlyContinue
if(!$myServer)
{
   Write-Output "Creating SQL server: $serverName"
   $myServer = New-AzureRmSqlServer -ResourceGroupName $serverResourceGroupName -ServerName $serverName -Location $serverLocation -ServerVersion $serverVersion -SqlAdministratorCredentials $serverCreds
}
else
{
   Write-Output "SQL server $serverName already exists:"
}
$myServer

```


## <a name="view-the-logical-sql-server-properties-using-azure-powershell"></a>Ver as propriedades lógicas do SQL server com o Azure PowerShell

```
#$serverResourceGroupName = "{resource-group-name}"
#$serverName = "{server-name}"

$myServer = Get-AzureRmSqlServer -ServerName $serverName -ResourceGroupName $serverResourceGroupName 

Write-Host "Server name: " $myServer.ServerName
Write-Host "Fully qualified server name: $serverName.database.windows.net"
Write-Host "Server location: " $myServer.Location
Write-Host "Server version: " $myServer.ServerVersion
Write-Host "Server administrator login: " $myServer.SqlAdministratorLogin
```


## <a name="create-a-server-level-firewall-rule-using-azure-powershell"></a>Criar uma regra de firewall ao nível do servidor com o Azure PowerShell

Tem de saber o seu endereço IP público para definir a regra da firewall. Pode obter o seu endereço IP com um browser à sua escolha (perguntar "qual é o meu endereço IP). Para obter mais detalhes, consulte [regras de firewall](sql-database-firewall-configure.md).

O seguinte utiliza os cmdlets [Get-AzureRmSqlServerFirewallRule](https://docs.microsoft.com/powershell/resourcemanager/azurerm.sql/v2.3.0/get-azurermsqlserverfirewallrule) e [New-AzureRmSqlServerFirewallRule](https://docs.microsoft.com/powershell/resourcemanager/azurerm.sql/v2.3.0/new-azurermsqlserverfirewallrule) para obter uma referência ou criar uma nova regra. Para este fragmento, se a regra já existir, este apenas obtém uma referência para o mesmo e não atualiza os endereços IP de início e de fim. Pode sempre modificar a cláusula **outra** para utilizar o [Set-AzureRmSqlServerFirewallRule](https://docs.microsoft.com/powershell/resourcemanager/azurerm.sql/v2.3.0/set-azurermsqlserverfirewallrule), para criar ou atualizar a funcionalidade.

> [!NOTE] 
> Pode abrir a firewall da Base de Dados SQL no servidor para um único endereço IP ou um conjunto de endereços. Abrir a firewall permite aos utilizadores e administradores SQL iniciar sessão em qualquer base de dados no servidor para o qual tenham credenciais válidas.
>

```
#$serverName = "{server-name}"
#$serverResourceGroupName = "{resource-group-name}"
$serverFirewallRuleName = "{server-firewall-rule-name}"
$serverFirewallStartIp = "{server-firewall-rule-startIp}"
$serverFirewallEndIp = "{server-firewall-rule-endIp}"

$myFirewallRule = Get-AzureRmSqlServerFirewallRule -FirewallRuleName $serverFirewallRuleName -ServerName $serverName -ResourceGroupName $serverResourceGroupName -ea SilentlyContinue

if(!$myFirewallRule)
{
   Write-host "Creating server firewall rule: $serverFirewallRuleName"
   $myFirewallRule = New-AzureRmSqlServerFirewallRule -ResourceGroupName $serverResourceGroupName -ServerName $serverName -FirewallRuleName $serverFirewallRuleName -StartIpAddress $serverFirewallStartIp -EndIpAddress $serverFirewallEndIp
}
else
{
   Write-host "Server firewall rule $serverFirewallRuleName already exists:"
}
$myFirewallRule
```


## <a name="connect-to-sql-server-using-azure-powershell"></a>Ligar ao SQL server com o Azure PowerShell

Vamos executar uma consulta rápida na base de dados principal para verificar que podemos ligar ao servidor. O fragmento seguinte utiliza o [Fornecedor de Framework .NET para o SQL Server (System.Data.SqlClient)](https://msdn.microsoft.com/library/system.data.sqlclient(v=vs.110).aspx), para ligar e consultar a base de dados principal do servidor. Cria uma cadeia de ligação com base nas variáveis que utilizámos nos fragmentos anteriores. Substitua os marcadores de posição pela palavra-passe e o administrador do servidor SQL utilizados para criar o servidor nos passos anteriores.


```
#$serverName = "{server-name}"
#$serverAdmin = "{server-admin}"
#$serverAdminPassword = "{server-admin-password}"
$databaseName = "master"

$connectionString = "Server=tcp:" + $serverName + ".database.windows.net" + ",1433;Initial Catalog=" + $databaseName + ";Persist Security Info=False;User ID=$serverAdmin;Password=$serverAdminPassword" + ";MultipleActiveResultSets=False;Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;"



$connection = New-Object System.Data.SqlClient.SqlConnection
$connection.ConnectionString = $connectionString
$connection.Open()
$command = New-Object System.Data.SQLClient.SQLCommand("select * from sys.objects", $connection)

$command.Connection = $connection
$reader = $command.ExecuteReader()


$sysObjects = ""
while ($reader.Read()) {
    $sysObjects += $reader["name"] + "`n"
}
$sysObjects

$connection.Close()
```


## <a name="create-new-adventureworkslt-sample-database-using-azure-powershell"></a>Crie uma nova base de dados de exemplo do AdventureWorksLT através do Azure PowerShell

O fragmento seguinte importa um bacpac da utilização da base de dados de exemplo do AdventureWorksLT com o cmdlet [New-AzureRmSqlDatabaseImport](https://docs.microsoft.com/powershell/resourcemanager/azurerm.sql/v2.3.0/new-azurermsqldatabaseimport). O bacpac está localizado no armazenamento de blobs do Azure. Depois de executar o cmdlet de importação, pode monitorizar o progresso da operação de importação com o cmdlet [Get-AzureRmSqlDatabaseImportExportStatus](https://docs.microsoft.com/powershell/resourcemanager/azurerm.sql/v2.3.0/get-azurermsqldatabaseimportexportstatus).
O $storageUri é a propriedade do URL do ficheiro bacpac carregado para o portal anteriormente e deverá ser semelhante a: https://{storage-account}.blob.core.windows.net/{container}/AdventureWorksLT.bacpac

```
#$resourceGroupName = "{resource-group-name}"
#$serverName = "{server-name}"

$databaseName = "AdventureWorksLT"
$databaseEdition = "Basic"
$databaseServiceLevel = "Basic"

$storageKeyType = "StorageAccessKey"
$storageUri = "{storage-uri}" # URL of bacpac file you uploaded to your storage account
$storageKey = "{storage-key}" # key1 in the Access keys setting of your storage account

$importRequest = New-AzureRmSqlDatabaseImport -ResourceGroupName $resourceGroupName -ServerName $serverName -DatabaseName $databaseName -StorageKeytype $storageKeyType -StorageKey $storageKey -StorageUri $storageUri -AdministratorLogin $serverAdmin -AdministratorLoginPassword $securePassword -Edition $databaseEdition -ServiceObjectiveName $databaseServiceLevel -DatabaseMaxSizeBytes 5000000


Do {
     $importStatus = Get-AzureRmSqlDatabaseImportExportStatus -OperationStatusLink $importRequest.OperationStatusLink
     Write-host "Importing database..." $importStatus.StatusMessage
     Start-Sleep -Seconds 30
     $importStatus.Status
   }
   until ($importStatus.Status -eq "Succeeded")
$importStatus
```



## <a name="view-database-properties-using-azure-powershell"></a>Ver as propriedades da base de dados com o Azure PowerShell

Depois de criar a base de dados, veja algumas das respetivas propriedades.

```
#$resourceGroupName = "{resource-group-name}"
#$serverName = "{server-name}"
#$databaseName = "{database-name}"

$myDatabase = Get-AzureRmSqlDatabase -ResourceGroupName $resourceGroupName -ServerName $serverName -DatabaseName $databaseName


Write-Host "Database name: " $myDatabase.DatabaseName
Write-Host "Server name: " $myDatabase.ServerName
Write-Host "Creation date: " $myDatabase.CreationDate
Write-Host "Database edition: " $myDatabase.Edition
Write-Host "Database performance level: " $myDatabase.CurrentServiceObjectiveName
Write-Host "Database status: " $myDatabase.Status
```

## <a name="connect-and-query-the-sample-database-using-azure-powershell"></a>Ligar e consultar a base de dados de exemplo com o Azure PowerShell

Vamos executar uma consulta rápida na base de dados do AdventureWorksLT para verificar que podemos ligar. O fragmento seguinte utiliza o [Fornecedor de Framework .NET para o SQL Server (System.Data.SqlClient)](https://msdn.microsoft.com/library/system.data.sqlclient(v=vs.110).aspx), para ligar e consultar a base de dados. Cria uma cadeia de ligação com base nas variáveis que utilizámos nos fragmentos anteriores. Substitua o marcador de posição pela palavra-passe de administrador do SQL server.

```
#$serverName = {server-name}
#$serverAdmin = "{server-admin}"
#$serverAdminPassword = "{server-admin-password}"
#$databaseName = {database-name}

$connectionString = "Server=tcp:" + $serverName + ".database.windows.net" + ",1433;Initial Catalog=" + $databaseName + ";Persist Security Info=False;User ID=$serverAdmin;Password=$serverAdminPassword" + ";MultipleActiveResultSets=False;Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;"


$connection = New-Object System.Data.SqlClient.SqlConnection
$connection.ConnectionString = $connectionString
$connection.Open()
$command = New-Object System.Data.SQLClient.SQLCommand("select * from sys.objects", $connection)

$command.Connection = $connection
$reader = $command.ExecuteReader()


$sysObjects = ""
while ($reader.Read()) {
    $sysObjects += $reader["name"] + "`n"
}
$sysObjects

$connection.Close()
```

## <a name="create-a-new-blank-database-using-azure-powershell"></a>Criar nova base de dados em branco com o Azure PowerShell

```
#$resourceGroupName = {resource-group-name}
#$serverName = {server-name}

$blankDatabaseName = "blankdb"
$blankDatabaseEdition = "Basic"
$blankDatabaseServiceLevel = "Basic"


$myBlankDatabase = New-AzureRmSqlDatabase -DatabaseName $blankDatabaseName -ServerName $serverName -ResourceGroupName $resourceGroupName -Edition $blankDatabaseEdition -RequestedServiceObjectiveName $blankDatabaseServiceLevel
$myBlankDatabase
```


## <a name="complete-azure-powershell-script-to-create-a-server-firewall-rule-and-database"></a>Concluir o script do Azure PowerShell para criar um servidor, uma regra da firewall e uma base de dados



```
# Sign in to Azure and set the subscription to work with
########################################################

$SubscriptionId = "{subscription-id}"

Add-AzureRmAccount
Set-AzureRmContext -SubscriptionId $SubscriptionId

# User variables
################

$myResourceGroupName = "{resource-group-name}"
$myResourceGroupLocation = "{resource-group-location}"

$myServerName = "{server-name}"
$myServerVersion = "12.0"
$myServerLocation = $myResourceGroupLocation
$myServerResourceGroupName = $myResourceGroupName
$myServerAdmin = "{server-admin}"
$myServerAdminPassword = "{server-admin-password}" 

$myServerFirewallRuleName = "{server-firewall-rule-name}"
$myServerFirewallStartIp = "{start-ip}"
$myServerFirewallEndIp = "{end-ip}"

$myDatabaseName = "AdventureWorksLT"
$myDatabaseEdition = "Basic"
$myDatabaseServiceLevel = "Basic"

$myStorageKeyType = "StorageAccessKey"
# Get these values from your Azure storage account:
$myStorageUri = "{http://your-storage-account.blob.core.windows.net/your-container/AdventureWorksLT.bacpac}"
$myStorageKey = "{your-storage-key}"


# Create new, or get existing resource group
############################################

$resourceGroupName = $myResourceGroupName
$resourceGroupLocation = $myResourceGroupLocation

$myResourceGroup = Get-AzureRmResourceGroup -Name $resourceGroupName -ea SilentlyContinue

if(!$myResourceGroup)
{
   Write-host "Creating resource group: $resourceGroupName"
   $myResourceGroup = New-AzureRmResourceGroup -Name $resourceGroupName -Location $resourceGroupLocation
}
else
{
   Write-host "Resource group $resourceGroupName already exists:"
}
$myResourceGroup


# Create a new, or get existing server
######################################

$serverName = $myServerName
$serverVersion = $myServerVersion
$serverLocation = $myServerLocation
$serverResourceGroupName = $myServerResourceGroupName

$serverAdmin = $myServerAdmin
$serverAdminPassword = $myServerAdminPassword

$securePassword = ConvertTo-SecureString -String $serverAdminPassword -AsPlainText -Force
$serverCreds = New-Object -TypeName System.Management.Automation.PSCredential -ArgumentList $serverAdmin, $securePassword

$myServer = Get-AzureRmSqlServer -ServerName $serverName -ResourceGroupName $serverResourceGroupName -ea SilentlyContinue
if(!$myServer)
{
   Write-host "Creating SQL server: $serverName"
   $myServer = New-AzureRmSqlServer -ResourceGroupName $serverResourceGroupName -ServerName $serverName -Location $serverLocation -ServerVersion $serverVersion -SqlAdministratorCredentials $serverCreds
}
else
{
   Write-host "SQL server $serverName already exists:"
}
$myServer


# View server properties
##########################

$resourceGroupName = $myResourceGroupName
$serverName = $myServerName

$myServer = Get-AzureRmSqlServer -ServerName $serverName -ResourceGroupName $serverResourceGroupName 

Write-Host "Server name: " $myServer.ServerName
Write-Host "Fully qualified server name: $serverName.database.windows.net"
Write-Host "Server location: " $myServer.Location
Write-Host "Server version: " $myServer.ServerVersion
Write-Host "Server administrator login: " $myServer.SqlAdministratorLogin


# Create or update server firewall rule
#######################################

$serverFirewallRuleName = $myServerFirewallRuleName
$serverFirewallStartIp = $myServerFirewallStartIp
$serverFirewallEndIp = $myServerFirewallEndIp

$myFirewallRule = Get-AzureRmSqlServerFirewallRule -FirewallRuleName $serverFirewallRuleName -ServerName $serverName -ResourceGroupName $serverResourceGroupName -ea SilentlyContinue

if(!$myFirewallRule)
{
   Write-host "Creating server firewall rule: $serverFirewallRuleName"
   $myFirewallRule = New-AzureRmSqlServerFirewallRule -ResourceGroupName $serverResourceGroupName -ServerName $serverName -FirewallRuleName $serverFirewallRuleName -StartIpAddress $serverFirewallStartIp -EndIpAddress $serverFirewallEndIp
}
else
{
   Write-host "Server firewall rule $serverFirewallRuleName already exists:"
}
$myFirewallRule


# Connect to the server and master database
###########################################
$databaseName = "master"

$connectionString = "Server=tcp:" + $serverName + ".database.windows.net" + ",1433;Initial Catalog=" + $databaseName + ";Persist Security Info=False;User ID=" + $myServer.SqlAdministratorLogin + ";Password=" + $myServerAdminPassword + ";MultipleActiveResultSets=False;Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;"


$connection = New-Object System.Data.SqlClient.SqlConnection
$connection.ConnectionString = $connectionString
$connection.Open()
$command = New-Object System.Data.SQLClient.SQLCommand("select * from sys.objects", $connection)

$command.Connection = $connection
$reader = $command.ExecuteReader()

$sysObjects = ""
while ($reader.Read()) {
    $sysObjects += $reader["name"] + "`n"
}
$sysObjects

$connection.Close()


# Create the AdventureWorksLT database from a bacpac
####################################################

$resourceGroupName = $myResourceGroupName
$serverName = $myServerName

$databaseName = $myDatabaseName
$databaseEdition = $myDatabaseEdition
$databaseServiceLevel = $myDatabaseServiceLevel

$storageKeyType = $myStorageKeyType
$storageUri = $myStorageUri
$storageKey = $myStorageKey

$importRequest = New-AzureRmSqlDatabaseImport -ResourceGroupName $resourceGroupName -ServerName $serverName -DatabaseName $databaseName -StorageKeytype $storageKeyType -StorageKey $storageKey -StorageUri $storageUri -AdministratorLogin $serverAdmin -AdministratorLoginPassword $securePassword -Edition $databaseEdition -ServiceObjectiveName $databaseServiceLevel -DatabaseMaxSizeBytes 5000000

Do {
     $importStatus = Get-AzureRmSqlDatabaseImportExportStatus -OperationStatusLink $importRequest.OperationStatusLink
     Write-host "Importing database..." $importStatus.StatusMessage
     Start-Sleep -Seconds 30
     $importStatus.Status
   }
   until ($importStatus.Status -eq "Succeeded")
$importStatus


# View database properties
##########################

$resourceGroupName = $myResourceGroupName
$serverName = $myServerName
$databaseName = $myDatabaseName

$myDatabase = Get-AzureRmSqlDatabase -ResourceGroupName $resourceGroupName -ServerName $serverName -DatabaseName $databaseName

Write-Host "Database name: " $myDatabase.DatabaseName
Write-Host "Server name: " $myDatabase.ServerName
Write-Host "Creation date: " $myDatabase.CreationDate
Write-Host "Database edition: " $myDatabase.Edition
Write-Host "Database performance level: " $myDatabase.CurrentServiceObjectiveName
Write-Host "Database status: " $myDatabase.Status


# Query the database
####################

$connectionString = "Server=tcp:" + $serverName + ".database.windows.net" + ",1433;Initial Catalog=" + $databaseName + ";Persist Security Info=False;User ID=" + $myServer.SqlAdministratorLogin + ";Password=" + $myServerAdminPassword + ";MultipleActiveResultSets=False;Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;"

$connection = New-Object System.Data.SqlClient.SqlConnection
$connection.ConnectionString = $connectionString
$connection.Open()
$command = New-Object System.Data.SQLClient.SQLCommand("select * from sys.objects", $connection)

$command.Connection = $connection
$reader = $command.ExecuteReader()

$sysObjects = ""
while ($reader.Read()) {
    $sysObjects += $reader["name"] + "`n"
}
$sysObjects

$connection.Close()


# Create a blank database
#########################

$blankDatabaseName = "blankdb"
$blankDatabaseEdition = "Basic"
$blankDatabaseServiceLevel = "Basic"


$myBlankDatabase = New-AzureRmSqlDatabase -DatabaseName $blankDatabaseName -ServerName $serverName -ResourceGroupName $resourceGroupName -Edition $blankDatabaseEdition -RequestedServiceObjectiveName $blankDatabaseServiceLevel
$myBlankDatabase
```



> [!TIP]
> Pode eliminar bases de dados que não estiver a utilizar para poupar algum dinheiro enquanto está a aprender. No caso das bases de dados de edição Básica, pode restaurá-las no período de sete dias. No entanto, não elimine um servidor. Se o fizer, não poderá recuperar o servidor nem nenhuma das respetivas bases de dados eliminadas.

## <a name="helper-snippets"></a>Fragmentos do programa auxiliar

```
# Get a list of Azure regions where you can create SQL resources

$sqlRegions = (Get-AzureRmLocation | Where-Object { $_.Providers -eq "Microsoft.Sql" })
foreach ($region in $sqlRegions)
{
   $region.Location
}

# Clean up
# Delete a resource group (and all contained resources)
Remove-AzureRmResourceGroup -Name {resource-group-name}
```

> [!TIP]
> Pode eliminar bases de dados que não estiver a utilizar para poupar algum dinheiro enquanto está a aprender. No caso das bases de dados de edição Básica, pode restaurá-las no período de sete dias. No entanto, não elimine o servidor. Se o fizer, não poderá recuperar o servidor nem nenhuma das respetivas bases de dados eliminadas.
>

## <a name="next-steps"></a>Passos seguintes
Agora que concluiu este tutorial de introdução e criou uma base de dados com alguns dados de exemplo, existem alguns tutoriais adicionais que poderá explorar para cimentar o que aprendeu neste tutorial. 

- Para obter um tutorial de introdução à autenticação do SQL Server, veja [SQL authentication and authorization (Autenticação e autorização do SQL)](sql-database-control-access-sql-authentication-get-started.md)
- Para obter um tutorial de introdução à autenticação do Azure Active Directory, veja [Azure AD authentication and authorization (Autenticação e autorização do Azure AD)](sql-database-control-access-aad-authentication-get-started.md).
* Se quiser consultar a base de dados de exemplo no portal do Azure, veja [Public preview: Interactive query experience for SQL databases (Pré-visualização pública: Experiência de consultas interativas para bases de dados SQL)](https://azure.microsoft.com/en-us/updates/azure-sql-database-public-preview-t-sql-editor/)
* Se tem conhecimentos de Excel, saiba como [Ligar a uma base de dados SQL no Azure com o Excel](sql-database-connect-excel.md).
* Se estiver pronto para iniciar a codificação, escolha a linguagem de programação em [Bibliotecas de ligação para Base de Dados SQL e SQL Server](sql-database-libraries.md).
* Se quiser mover as suas bases de dados do SQL Server no local para o Azure, veja o artigo [Migrar uma base de dados para a Base de Dados SQL](sql-database-cloud-migrate.md).
* Se pretender carregar alguns dados para uma nova tabela a partir de um ficheiro CSV utilizando a ferramenta de linha de comandos BCP, veja [Carregar dados para a Base de Dados SQL a partir de um ficheiro CSV com o BCP](sql-database-load-from-csv-with-bcp.md).
* Se quiser começar a criar tabelas e outros objetos, veja o tópico "Para criar uma tabela" no artigo [Criar uma tabela](https://msdn.microsoft.com/library/ms365315.aspx).

## <a name="additional-resources"></a>Recursos adicionais
[O que é a Base de Dados SQL?](sql-database-technical-overview.md)



<!--HONumber=Feb17_HO3-->


