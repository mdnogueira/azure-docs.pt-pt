---
title: Gerir o Azure Data Lake Analytics com o Azure PowerShell | Microsoft Docs
description: "Saiba como gerir contas de Data Lake Analytics, origens de dados, tarefas e itens de catálogo. "
services: data-lake-analytics
documentationcenter: 
author: matt1883
manager: jhubbard
editor: cgronlun
ms.assetid: ad14d53c-fed4-478d-ab4b-6d2e14ff2097
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 07/23/2017
ms.author: mahi
ms.openlocfilehash: 65bf5928428b21e98c893a9de8ca596329329411
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="manage-azure-data-lake-analytics-using-azure-powershell"></a>Gerir a Análise do Azure Data Lake com o Azure PowerShell
[!INCLUDE [manage-selector](../../includes/data-lake-analytics-selector-manage.md)]

Saiba como gerir contas do Azure Data Lake Analytics, origens de dados, tarefas e itens de catálogo com o Azure PowerShell. 

## <a name="prerequisites"></a>Pré-requisitos

Quando criar uma conta de Data Lake Analytics, tem de saber:

* **ID de subscrição**: ID de subscrição do Azure no qual reside a conta de Data Lake Analytics.
* **Grupo de recursos**: O nome do grupo de recursos do Azure que contém a sua conta do Data Lake Analytics.
* **O nome de conta do Data Lake Analytics**: O nome da conta tem de conter apenas letras minúsculas e números.
* **Conta do Data Lake Store predefinida**: cada conta do Data Lake Analytics tem uma conta do Data Lake Store predefinida. Estas contas têm de estar na mesma localização.
* **Localização**: A localização da sua conta do Data Lake Analytics, por exemplo, "EUA Leste 2" ou outro suportado localizações. Localizações suportadas podem ser vistas no nosso [página de preços](https://azure.microsoft.com/pricing/details/data-lake-analytics/).

Os fragmentos de PowerShell neste tutorial utilizam estas variáveis para armazenar estas informações

```powershell
$subId = "<SubscriptionId>"
$rg = "<ResourceGroupName>"
$adla = "<DataLakeAnalyticsAccountName>"
$adls = "<DataLakeStoreAccountName>"
$location = "<Location>"
```

## <a name="log-in"></a>Iniciar sessão

Inicie sessão com um id de subscrição.

```powershell
Login-AzureRmAccount -SubscriptionId $subId
```

Inicie sessão com um nome de subscrição.

```
Login-AzureRmAccount -SubscriptionName $subname 
```

O `Login-AzureRmAccount` cmdlet sempre pede-lhe credenciais. Pode evitar que está a ser solicitado, utilizando os seguintes cmdlets:

```powershell
# Save login session information
Save-AzureRmProfile -Path D:\profile.json  

# Load login session information
Select-AzureRmProfile -Path D:\profile.json 
```

## <a name="managing-accounts"></a>Gerir contas

### <a name="create-a-data-lake-analytics-account"></a>Criar uma conta de Data Lake Analytics

Se ainda não tiver um [grupo de recursos](../azure-resource-manager/resource-group-overview.md#resource-groups) para utilizar, criar um. 

```powershell
New-AzureRmResourceGroup -Name  $rg -Location $location
```

Cada conta de Data Lake Analytics requer uma conta de Data Lake Store predefinida que utiliza para armazenar registos. Pode reutilizar uma conta existente ou criar uma conta. 

```powershell
New-AdlStore -ResourceGroupName $rg -Name $adls -Location $location
```

Assim que um Grupo de Recursos e uma conta de Data Lake Store estiverem disponíveis, crie uma conta de Data Lake Analytics.

```powershell
New-AdlAnalyticsAccount -ResourceGroupName $rg -Name $adla -Location $location -DefaultDataLake $adls
```

### <a name="get-information-about-an-account"></a>Obter informações sobre uma conta

Obter informações sobre a uma conta.

```powershell
Get-AdlAnalyticsAccount -Name $adla
```

Verificar a existência de uma conta de Data Lake Analytics específica. O cmdlet devolve um `True` ou `False`.

```powershell
Test-AdlAnalyticsAccount -Name $adla
```

Verificar a existência de uma conta de Data Lake Store específica. O cmdlet devolve um `True` ou `False`.

```powershell
Test-AdlStoreAccount -Name $adls
```

### <a name="listing-accounts"></a>Contas de listagem

Contas de análise do Data Lake da lista dentro da subscrição atual.

```powershell
Get-AdlAnalyticsAccount
```

Contas de análise do Data Lake da lista dentro de um grupo de recursos específico.

```powershell
Get-AdlAnalyticsAccount -ResourceGroupName $rg
```

## <a name="managing-firewall-rules"></a>Gerir regras de firewall

Lista de regras de firewall.

```powershell
Get-AdlAnalyticsFirewallRule -Account $adla
```

Adicione uma regra de firewall.

```powershell
$ruleName = "Allow access from on-prem server"
$startIpAddress = "<start IP address>"
$endIpAddress = "<end IP address>"

Add-AdlAnalyticsFirewallRule -Account $adla -Name $ruleName -StartIpAddress $startIpAddress -EndIpAddress $endIpAddress
```

Altere uma regra de firewall.

```powershell
Set-AdlAnalyticsFirewallRule -Account $adla -Name $ruleName -StartIpAddress $startIpAddress -EndIpAddress $endIpAddress
```

Remova uma regra de firewall.

```powershell
Remove-AdlAnalyticsFirewallRule -Account $adla -Name $ruleName
```



Permitir que os endereços IP do Azure.

```powershell
Set-AdlAnalyticsAccount -Name $adla -AllowAzureIpState Enabled
```

```powershell
Set-AdlAnalyticsAccount -Name $adla -FirewallState Enabled
Set-AdlAnalyticsAccount -Name $adla -FirewallState Disabled
```

## <a name="managing-data-sources"></a>Gerir origens de dados
Atualmente, o Azure Data Lake Analytics suporta as seguintes origens de dados:

* [Azure Data Lake Store](../data-lake-store/data-lake-store-overview.md)
* [Armazenamento do Azure](../storage/common/storage-introduction.md)

Quando cria uma conta de análise, tem de designar uma conta de Data Lake Store seja a origem de dados predefinido. A conta de Data Lake Store predefinida é utilizada para armazenar os registos de auditoria de tarefa e os metadados de tarefa. Depois de criar uma conta de Data Lake Analytics, pode adicionar mais contas de Data Lake Store e/ou as contas de armazenamento. 

### <a name="find-the-default-data-lake-store-account"></a>Localizar a conta de Data Lake Store predefinida

```powershell
$adla_acct = Get-AdlAnalyticsAccount -Name $adla
$dataLakeStoreName = $adla_acct.DefaultDataLakeAccount
```

Pode encontrar a conta de Data Lake Store predefinida por filtrar a lista de origens de dados pelo `IsDefault` propriedade:

```powershell
Get-AdlAnalyticsDataSource -Account $adla  | ? { $_.IsDefault } 
```

### <a name="add-a-data-source"></a>Adicionar uma origem de dados

```powershell

# Add an additional Storage (Blob) account.
$AzureStorageAccountName = "<AzureStorageAccountName>"
$AzureStorageAccountKey = "<AzureStorageAccountKey>"
Add-AdlAnalyticsDataSource -Account $adla -Blob $AzureStorageAccountName -AccessKey $AzureStorageAccountKey

# Add an additional Data Lake Store account.
$AzureDataLakeStoreName = "<AzureDataLakeStoreAccountName"
Add-AdlAnalyticsDataSource -Account $adla -DataLakeStore $AzureDataLakeStoreName 
```

### <a name="list-data-sources"></a>Origens de dados de lista

```powershell
# List all the data sources
Get-AdlAnalyticsDataSource -Name $adla

# List attached Data Lake Store accounts
Get-AdlAnalyticsDataSource -Name $adla | where -Property Type -EQ "DataLakeStore"

# List attached Storage accounts
Get-AdlAnalyticsDataSource -Name $adla | where -Property Type -EQ "Blob"
```

## <a name="submit-u-sql-jobs"></a>Submeter tarefas U-SQL

### <a name="submit-a-string-as-a-u-sql-script"></a>Submeter uma cadeia como um script U-SQL

```powershell
$script = @"
@a  = 
    SELECT * FROM 
        (VALUES
            ("Contoso", 1500.0),
            ("Woodgrove", 2700.0)
        ) AS D( customer, amount );
OUTPUT @a
    TO "/data.csv"
    USING Outputters.Csv();
"@

$scriptpath = "d:\test.usql"
$script | Out-File $scriptpath 

Submit-AdlJob -AccountName $adla -Script $script -Name "Demo"
```


### <a name="submit-a-file-as-a-u-sql-script"></a>Submeter um ficheiro como um script U-SQL

```powershell
$scriptpath = "d:\test.usql"
$script | Out-File $scriptpath 
Submit-AdlJob -AccountName $adla –ScriptPath $scriptpath -Name "Demo"
```

## <a name="list-jobs-in-an-account"></a>Lista de tarefas numa conta

### <a name="list-all-the-jobs-in-the-account"></a>Faça uma lista de todas as tarefas na conta. 

A saída inclui as tarefas atualmente em execução e as tarefas que foram concluídas recentemente.

```powershell
Get-AdlJob -Account $adla
```


### <a name="list-a-specific-number-of-jobs"></a>Lista de um número específico de tarefas

Por predefinição, que a lista de tarefas é ordenada no envio de tempo. Por isso, as tarefas submetidas mais recentemente são apresentados primeiro. Por predefinição, a conta de ADLA o memorizou tarefas durante 180 dias, mas o cmdlet Ge AdlJob por predefinição devolve apenas o primeiro 500. Utilize o - parâmetro superior para listar um número específico de tarefas.

```powershell
$jobs = Get-AdlJob -Account $adla -Top 10
```


### <a name="list-jobs-based-on-the-value-of-job-property"></a>Tarefas de lista com base no valor da propriedade de tarefa

Utilizar o `-State` parâmetro. Pode combinar qualquer um destes valores:

* `Accepted`
* `Compiling`
* `Ended`
* `New`
* `Paused`
* `Queued`
* `Running`
* `Scheduling`
* `Start`

```powershell
# List the running jobs
Get-AdlJob -Account $adla -State Running

# List the jobs that have completed
Get-AdlJob -Account $adla -State Ended

# List the jobs that have not started yet
Get-AdlJob -Account $adla -State Accepted,Compiling,New,Paused,Scheduling,Start
```

Utilize o `-Result` parâmetro para detetar se as tarefas terminadas foi concluída com êxito. Tem estes valores:

* Foi cancelada
* Falha
* Nenhuma
* Bem-sucedido

``` powershell
# List Successful jobs.
Get-AdlJob -Account $adla -State Ended -Result Succeeded

# List Failed jobs.
Get-AdlJob -Account $adla -State Ended -Result Failed
```


O `-Submitter` parâmetro ajuda-o a identificar quem submetida uma tarefa.

```powershell
Get-AdlJob -Account $adla -Submitter "joe@contoso.com"
```

O `-SubmittedAfter` é útil para filtrar para um intervalo de tempo.


```powershell
# List  jobs submitted in the last day.
$d = [DateTime]::Now.AddDays(-1)
Get-AdlJob -Account $adla -SubmittedAfter $d

# List  jobs submitted in the last seven day.
$d = [DateTime]::Now.AddDays(-7)
Get-AdlJob -Account $adla -SubmittedAfter $d
```

### <a name="analyzing-job-history"></a>Analisar o histórico de tarefas

Com o Azure PowerShell para analisar o histórico de tarefas que foram executados no Data Lake analytics é uma técnica de elevado desempenho. Pode utilizá-lo para obter informações acerca da utilização e o custo. Pode saber mais observando a [repositório de exemplo de análise de histórico da tarefa](https://github.com/Azure-Samples/data-lake-analytics-powershell-job-history-analysis)  

## <a name="get-information-about-pipelines-and-recurrences"></a>Obter informações sobre pipelines e recurrences

Utilize o `Get-AdlJobPipeline` cmdlet para ver as informações de pipeline previamente submetido tarefas.

```powershell
$pipelines = Get-AdlJobPipeline -Account $adla

$pipeline = Get-AdlJobPipeline -Account $adla -PipelineId "<pipeline ID>"
```

Utilize o `Get-AdlJobRecurrence` cmdlet para ver as informações de periodicidade para anteriormente submetido tarefas.

```powershell
$recurrences = Get-AdlJobRecurrence -Account $adla

$recurrence = Get-AdlJobRecurrence -Account $adla -RecurrenceId "<recurrence ID>"
```

## <a name="get-information-about-a-job"></a>Obter informações sobre uma tarefa

### <a name="get-job-status"></a>Obter estado da tarefa

Obtenha o estado de uma tarefa específica.

```powershell
Get-AdlJob -AccountName $adla -JobId $job.JobId
```

### <a name="examine-the-job-outputs"></a>Examine as saídas da tarefa

Depois da tarefa foi terminado, verifique se o ficheiro de saída existe por listar os ficheiros numa pasta.

```powershell
Get-AdlStoreChildItem -Account $adls -Path "/"
```

## <a name="manage-running-jobs"></a>Gerir tarefas em execução

### <a name="cancel-a-job"></a>Cancelar uma tarefa

```powershell
Stop-AdlJob -Account $adls -JobID $jobID
```

### <a name="wait-for-a-job-to-finish"></a>Aguarde a conclusão de conclusão

Em vez de repetir `Get-AdlAnalyticsJob` até que seja uma tarefa concluída, pode utilizar o `Wait-AdlJob` cmdlet para aguardar a tarefa terminar.

```powershell
Wait-AdlJob -Account $adla -JobId $job.JobId
```

## <a name="manage-compute-policies"></a>Gerir políticas de computação

### <a name="list-existing-compute-policies"></a>Lista as políticas existentes de computação

O `Get-AdlAnalyticsComputePolicy` cmdlet obtém informações sobre as políticas de computação para uma conta de Data Lake Analytics.

```powershell
$policies = Get-AdlAnalyticsComputePolicy -Account $adla
```

### <a name="create-a-compute-policy"></a>Criar uma política de computação

O `New-AdlAnalyticsComputePolicy` cmdlet cria uma nova política de computação para uma conta de Data Lake Analytics. Neste exemplo define os AUs máximas disponíveis para o utilizador especificado para 50 e a prioridade do trabalho mínimo para 250.

```powershell
$userObjectId = (Get-AzureRmAdUser -SearchString "garymcdaniel@contoso.com").Id

New-AdlAnalyticsComputePolicy -Account $adla -Name "GaryMcDaniel" -ObjectId $objectId -ObjectType User -MaxDegreeOfParallelismPerJob 50 -MinPriorityPerJob 250
```

## <a name="check-for-the-existence-of-a-file"></a>Verifique a existência de um ficheiro.

```powershell
Test-AdlStoreItem -Account $adls -Path "/data.csv"
```

## <a name="uploading-and-downloading"></a>Carregar e transferir

Carregar um ficheiro.

```powershell
Import-AdlStoreItem -AccountName $adls -Path "c:\data.tsv" -Destination "/data_copy.csv" 
```

Carregar um recursivamente pasta inteira.

```powershell
Import-AdlStoreItem -AccountName $adls -Path "c:\myData\" -Destination "/myData/" -Recurse
```

Transferir um ficheiro.

```powershell
Export-AdlStoreItem -AccountName $adls -Path "/data.csv" -Destination "c:\data.csv"
```

Transferir um recursivamente pasta inteira.

```powershell
Export-AdlStoreItem -AccountName $adls -Path "/" -Destination "c:\myData\" -Recurse
```

> [!NOTE]
> Se o processo de carregamento ou transferência for interrompido, pode tentar retomar o processo executando o cmdlet novamente com o ``-Resume`` sinalizador.

## <a name="manage-catalog-items"></a>Gerir itens de catálogo

O catálogo U-SQL é utilizado para a estrutura de dados e o código para que possa ser partilhados por scripts U-SQL. O catálogo permite que o desempenho mais elevado possível com os dados no Azure Data Lake. Para obter mais informações, veja [Utilizar catálogo U-SQL](data-lake-analytics-use-u-sql-catalog.md).

### <a name="list-items-in-the-u-sql-catalog"></a>Itens de lista no catálogo U-SQL

```powershell
# List U-SQL databases
Get-AdlCatalogItem -Account $adla -ItemType Database 

# List tables within a database
Get-AdlCatalogItem -Account $adla -ItemType Table -Path "database"

# List tables within a schema.
Get-AdlCatalogItem -Account $adla -ItemType Table -Path "database.schema"
```

Liste todas as assemblagens em todas as bases de dados numa conta ADLA.

```powershell
$dbs = Get-AdlCatalogItem -Account $adla -ItemType Database

foreach ($db in $dbs)
{
    $asms = Get-AdlCatalogItem -Account $adla -ItemType Assembly -Path $db.Name

    foreach ($asm in $asms)
    {
        $asmname = "[" + $db.Name + "].[" + $asm.Name + "]"
        Write-Host $asmname
    }
}
```

### <a name="get-details-about-a-catalog-item"></a>Obter detalhes sobre um item de catálogo

```powershell
# Get details of a table
Get-AdlCatalogItem  -Account $adla -ItemType Table -Path "master.dbo.mytable"

# Test existence of a U-SQL database.
Test-AdlCatalogItem  -Account $adla -ItemType Database -Path "master"
```

### <a name="create-credentials-in-a-catalog"></a>Criar as credenciais no catálogo

Dentro de uma base de dados do U-SQL, crie um objeto de credencial para uma base de dados alojado no Azure. Atualmente, as credenciais de U-SQL são o único tipo de item de catálogo que pode criar através do PowerShell.

```powershell
$dbName = "master"
$credentialName = "ContosoDbCreds"
$dbUri = "https://contoso.database.windows.net:8080"

New-AdlCatalogCredential -AccountName $adla `
          -DatabaseName $db `
          -CredentialName $credentialName `
          -Credential (Get-Credential) `
          -Uri $dbUri
```

### <a name="get-basic-information-about-an-adla-account"></a>Obter informações básicas sobre uma conta ADLA

Fornecido um nome de conta, o seguinte código procura informações básicas sobre a conta

```
$adla_acct = Get-AdlAnalyticsAccount -Name "saveenrdemoadla"
$adla_name = $adla_acct.Name
$adla_subid = $adla_acct.Id.Split("/")[2]
$adla_sub = Get-AzureRmSubscription -SubscriptionId $adla_subid
$adla_subname = $adla_sub.Name
$adla_defadls_datasource = Get-AdlAnalyticsDataSource -Account $adla_name  | ? { $_.IsDefault } 
$adla_defadlsname = $adla_defadls_datasource.Name

Write-Host "ADLA Account Name" $adla_name
Write-Host "Subscription Id" $adla_subid
Write-Host "Subscription Name" $adla_subname
Write-Host "Defautl ADLS Store" $adla_defadlsname
Write-Host 

Write-Host '$subname' " = ""$adla_subname"" "
Write-Host '$subid' " = ""$adla_subid"" "
Write-Host '$adla' " = ""$adla_name"" "
Write-Host '$adls' " = ""$adla_defadlsname"" "
```

## <a name="working-with-azure"></a>Trabalhar com o Azure

### <a name="get-details-of-azurerm-errors"></a>Obter os detalhes de erros de AzureRm

```powershell
Resolve-AzureRmError -Last
```

### <a name="verify-if-you-are-running-as-an-administrator"></a>Certifique-se de que se estiver a executar como administrador

```powershell
function Test-Administrator  
{  
    $user = [Security.Principal.WindowsIdentity]::GetCurrent();
    $p = New-Object Security.Principal.WindowsPrincipal $user
    $p.IsInRole([Security.Principal.WindowsBuiltinRole]::Administrator)  
}
```

### <a name="find-a-tenantid"></a>Localizar um TenantID

A partir de um nome de subscrição:

```powershell
function Get-TenantIdFromSubcriptionName( [string] $subname )
{
    $sub = (Get-AzureRmSubscription -SubscriptionName $subname)
    $sub.TenantId
}

Get-TenantIdFromSubcriptionName "ADLTrainingMS"
```

A partir de um id de subscrição:

```powershell
function Get-TenantIdFromSubcriptionId( [string] $subid )
{
    $sub = (Get-AzureRmSubscription -SubscriptionId $subid)
    $sub.TenantId
}

$subid = "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
Get-TenantIdFromSubcriptionId $subid
```

De um endereço de domínio, como "contoso.com"


```powershell
function Get-TenantIdFromDomain( $domain )
{
    $url = "https://login.windows.net/" + $domain + "/.well-known/openid-configuration"
    return (Invoke-WebRequest $url|ConvertFrom-Json).token_endpoint.Split('/')[3]
}

$domain = "contoso.com"
Get-TenantIdFromDomain $domain
```

### <a name="list-all-your-subscriptions-and-tenant-ids"></a>Listar todas as subscrições e ids de inquilino

```powershell
$subs = Get-AzureRmSubscription
foreach ($sub in $subs)
{
    Write-Host $sub.Name "("  $sub.Id ")"
    Write-Host "`tTenant Id" $sub.TenantId
}
```

## <a name="create-a-data-lake-analytics-account-using-a-template"></a>Criar uma conta de Data Lake Analytics utilizando um modelo

Também pode utilizar um modelo de grupo de recursos do Azure utilizando o seguinte script do PowerShell:

```powershell
$subId = "<Your Azure Subscription ID>"

$rg = "<New Azure Resource Group Name>"
$location = "<Location (such as East US 2)>"
$adls = "<New Data Lake Store Account Name>"
$adla = "<New Data Lake Analytics Account Name>"

$deploymentName = "MyDataLakeAnalyticsDeployment"
$armTemplateFile = "<LocalFolderPath>\azuredeploy.json"  # update the JSON template path 

# Log in to Azure
Login-AzureRmAccount -SubscriptionId $subId

# Create the resource group
New-AzureRmResourceGroup -Name $rg -Location $location

# Create the Data Lake Analytics account with the default Data Lake Store account.
$parameters = @{"adlAnalyticsName"=$adla; "adlStoreName"=$adls}
New-AzureRmResourceGroupDeployment -Name $deploymentName -ResourceGroupName $rg -TemplateFile $armTemplateFile -TemplateParameterObject $parameters 
```

Para obter mais informações, consulte [implementar uma aplicação com o modelo Azure Resource Manager](../azure-resource-manager/resource-group-template-deploy.md) e [modelos Authoring Azure Resource Manager](../azure-resource-manager/resource-group-authoring-templates.md).

**Modelo de exemplo**

Guarde o seguinte texto como um `.json` de ficheiros e, em seguida, utilize o script do PowerShell anterior para utilizar o modelo. 

```json
{
  "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "adlAnalyticsName": {
      "type": "string",
      "metadata": {
        "description": "The name of the Data Lake Analytics account to create."
      }
    },
    "adlStoreName": {
      "type": "string",
      "metadata": {
        "description": "The name of the Data Lake Store account to create."
      }
    }
  },
  "resources": [
    {
      "name": "[parameters('adlStoreName')]",
      "type": "Microsoft.DataLakeStore/accounts",
      "location": "East US 2",
      "apiVersion": "2015-10-01-preview",
      "dependsOn": [ ],
      "tags": { }
    },
    {
      "name": "[parameters('adlAnalyticsName')]",
      "type": "Microsoft.DataLakeAnalytics/accounts",
      "location": "East US 2",
      "apiVersion": "2015-10-01-preview",
      "dependsOn": [ "[concat('Microsoft.DataLakeStore/accounts/',parameters('adlStoreName'))]" ],
      "tags": { },
      "properties": {
        "defaultDataLakeStoreAccount": "[parameters('adlStoreName')]",
        "dataLakeStoreAccounts": [
          { "name": "[parameters('adlStoreName')]" }
        ]
      }
    }
  ],
  "outputs": {
    "adlAnalyticsAccount": {
      "type": "object",
      "value": "[reference(resourceId('Microsoft.DataLakeAnalytics/accounts',parameters('adlAnalyticsName')))]"
    },
    "adlStoreAccount": {
      "type": "object",
      "value": "[reference(resourceId('Microsoft.DataLakeStore/accounts',parameters('adlStoreName')))]"
    }
  }
}
```

## <a name="next-steps"></a>Passos seguintes
* [Descrição geral do Microsoft Azure Data Lake Analytics](data-lake-analytics-overview.md)
* Introdução ao Data Lake Analytics com [portal do Azure](data-lake-analytics-get-started-portal.md) | [Azure PowerShell](data-lake-analytics-get-started-powershell.md) | [CLI 2.0](data-lake-analytics-get-started-cli2.md)
* Gerir o Azure Data Lake Analytics com [portal do Azure](data-lake-analytics-manage-use-portal.md) | [Azure PowerShell](data-lake-analytics-manage-use-powershell.md) | [CLI](data-lake-analytics-manage-use-cli.md) 
