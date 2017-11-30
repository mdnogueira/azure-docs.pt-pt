---
title: "Criar o tempo de execução de integração personalizada alojada no Azure Data Factory | Microsoft Docs"
description: Saiba como pode utilizar a atividade de procedimento armazenado do SQL Server para invocar um procedimento armazenado numa SQL Database do Azure ou do Azure SQL Data Warehouse a partir de um pipeline do Data Factory.
services: data-factory
documentationcenter: 
author: spelluru
manager: jhubbard
editor: monicar
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/10/2017
ms.author: spelluru
ms.openlocfilehash: fcd2547112eb966420f33cec4939c83606029444
ms.sourcegitcommit: 29bac59f1d62f38740b60274cb4912816ee775ea
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/29/2017
---
# <a name="create-an-azure-ssis-integration-runtime-in-azure-data-factory"></a>Criar um tempo de execução de integração do Azure-SSIS no Azure Data Factory
Este artigo fornece os passos para o aprovisionamento de um tempo de execução de integração do Azure-SSIS no Azure Data Factory. Em seguida, pode utilizar o SQL Server Data Tools (SSDT) ou o SQL Server Management Studio (SSMS) para implementar pacotes de SQL Server Integration Services (SSIS) neste runtime no Azure.

> [!NOTE]
> Este artigo aplica-se à versão 2 do Data Factory, que está atualmente em pré-visualização. Se estiver a utilizar a versão 1 do serviço Data Factory, que está disponível em geral (GA), veja a [documentação da versão 1 do Data Factory](v1/data-factory-introduction.md).

Tutorial: [Tutorial: implementar pacotes de SQL Server Integration Services (SSIS) para o Azure](tutorial-deploy-ssis-packages-azure.md) mostra-lhe como criar um tempo de execução de integração do Azure SSIS (IR) ao utilizar a SQL Database do Azure como o arquivo para o catálogo SSIS. Este artigo expande o tutorial e mostra como fazer o seguinte: 

- Utilize o Azure geridos instância do SQL (pré-visualização privada) para alojar um catálogo SSIS (base de dados SSISDB).
- Associação do Azure-SSIS IR a uma rede virtual do Azure (VNet). 

Para obter informações concetuais sobre associar uma resposta a incidentes SSIS do Azure para uma VNet e como configurar uma VNet no portal do Azure, consulte [associar IR Azure SSIS vnet](join-azure-ssis-integration-runtime-virtual-network.md). 

## <a name="prerequisites"></a>Pré-requisitos

- **Subscrição do Azure**. Se não tiver uma subscrição, pode criar uma conta de [avaliação gratuita](http://azure.microsoft.com/pricing/free-trial/).
- **Servidor de Base de Dados SQL do Azure** ou **Instância Gerida do SQL Server (pré-visualização privada) (Pré-visualização Privada Expandida)**. Se ainda não tiver um servidor de base de dados, crie um no portal do Azure antes de começar. Este servidor aloja a base de dados do Catálogo de SSIS (SSISDB). Recomendamos que crie o servidor de base de dados na mesma região do Azure que o integration runtime. Esta configuração permite que o integration runtime escreva registos de execução no SSISDB sem cruzamento entre regiões do Azure. Tenha em atenção para baixo o escalão de preço do seu servidor SQL do Azure. Para obter uma lista dos escalões de preços suportados para a SQL Database do Azure, consulte [dos limites de recursos de base de dados SQL](../sql-database/sql-database-resource-limits.md).
- **Rede Virtual Clássica (VNet) (opcional)**. Tem de ter uma Rede Virtual do Azure (VNet) se, pelo menos, uma das seguintes condições for verdadeira:
    - Está a alojar a base de dados do Catálogo de SSIS numa Instância Gerida do SQL Server (pré-visualização privada) que faz parte de uma VNet.
    - Pretende ligar a arquivos de dados no local a partir de pacotes de SSIS em execução no integration runtime do Azure-SSIS.
- **Azure PowerShell**. Siga as instruções em [How to install and configure Azure PowerShell (Como instalar e configurar o Azure PowerShell)](/powershell/azure/install-azurerm-ps). Vai utilizar o PowerShell para executar um script para aprovisionar um integration runtime do Azure-SSIS que executa pacotes de SSIS na cloud. 

> [!NOTE]
> Para obter uma lista das regiões suportadas pelo Azure Data Factory V2 e o Integration Runtime Azure-SSIS, veja [Products available by region](https://azure.microsoft.com/regions/services/) (Produtos disponíveis por região). Expanda **Dados + Análise** para ver **Data Factory V2** e **Integration Runtime do SSIS**.


## <a name="create-variables"></a>Criar variáveis
Defina variáveis para utilizar nos scripts neste tutorial:

```powershell
# Azure Data Factory version 2 information 
# If your input contains a PSH special character, e.g. "$", precede it with the escape character "`" like "`$".
$SubscriptionName = "[your Azure subscription name]"
$ResourceGroupName = "[your Azure resource group name]"
$DataFactoryName = "[your data factory name]"
$DataFactoryLocation = "EastUS" 

# Azure-SSIS integration runtime information - This is the Data Factory compute resource for running SSIS packages
$AzureSSISName = "[your Azure-SSIS integration runtime name]"
$AzureSSISDescription = "This is my Azure-SSIS integration runtime"
$AzureSSISLocation = "EastUS" 
$AzureSSISNodeSize = "Standard_A4_v2" # In public preview, only Standard_A4_v2|Standard_A8_v2|Standard_D1_v2|Standard_D2_v2|Standard_D3_v2|Standard_D4_v2 are supported.
$AzureSSISNodeNumber = 2 # In public preview, only 1-10 nodes are supported.
$AzureSSISMaxParallelExecutionsPerNode = 2 # In public preview, only 1-8 parallel executions per node are supported.

# SSISDB info
$SSISDBServerEndpoint = "[your Azure SQL Database server name.database.windows.net or your Azure SQL Managed Instance (private preview) server endpoint]"
$SSISDBServerAdminUserName = "[your server admin username]"
$SSISDBServerAdminPassword = "[your server admin password]"

# Remove the SSISDBPricingTier variable if you are using Azure SQL Managed Instance (private preview)
# This parameter applies only to Azure SQL Database. For the basic pricing tier, specify "Basic", not "B". For standard tiers, specify "S0", "S1", "S2", 'S3", etc.
$SSISDBPricingTier = "[your Azure SQL Database pricing tier. Examples: Basic, S0, S1, S2, S3, etc.]"

## Remove these two variables if you are using Azure SQL Database. 
## These two parameters apply if you are using VNet and Azure SQL Managed Instance (private preview). 
$VnetId = "[your VNet resource ID or leave it empty]" # OPTIONAL: In public preview, only classic virtual network (VNet) is supported.
$SubnetName = "[your subnet name or leave it empty]" # OPTIONAL: In public preview, only classic VNet is supported.

```

## <a name="validate-the-connection-to-database"></a>Validar a ligação à base de dados
Adicione o seguinte script para validar a sua server.database.windows.net de servidor da SQL Database do Azure ou o ponto final do servidor gerido instância do SQL do Azure (pré-visualização privada). 

```powershell
$SSISDBConnectionString = "Data Source=" + $SSISDBServerEndpoint + ";User ID="+ $SSISDBServerAdminUserName +";Password="+ $SSISDBServerAdminPassword
$sqlConnection = New-Object System.Data.SqlClient.SqlConnection $SSISDBConnectionString;
Try
{
    $sqlConnection.Open();
}
Catch [System.Data.SqlClient.SqlException]
{
    Write-Warning "Cannot connect to your Azure SQL DB logical server/Azure SQL MI server, exception: $_"  ;
    Write-Warning "Please make sure the server you specified has already been created. Do you want to proceed? [Y/N]"
    $yn = Read-Host
    if(!($yn -ieq "Y"))
    {
        Return;
    } 
}
```

## <a name="log-in-and-select-subscription"></a>Iniciar sessão e selecionar a subscrição
Adicione o seguinte código o script para iniciar sessão e selecione a sua subscrição do Azure: 

```powershell
Login-AzureRmAccount
Select-AzureRmSubscription -SubscriptionName $SubscriptionName
```

## <a name="configure-virtual-network"></a>Configurar uma rede virtual
Adicione o seguinte script para configurar automaticamente permissões/definições de rede virtual para o integration runtime do Azure-SSIS para associar.

```powershell
# Register to Azure Batch resource provider
if(![string]::IsNullOrEmpty($VnetId) -and ![string]::IsNullOrEmpty($SubnetName))
{
    $BatchObjectId = (Get-AzureRmADServicePrincipal -ServicePrincipalName "MicrosoftAzureBatch").Id
    Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Batch
    while(!(Get-AzureRmResourceProvider -ProviderNamespace "Microsoft.Batch").RegistrationState.Contains("Registered"))
    {
    Start-Sleep -s 10
    }
    # Assign VM contributor role to Microsoft.Batch
    New-AzureRmRoleAssignment -ObjectId $BatchObjectId -RoleDefinitionName "Classic Virtual Machine Contributor" -Scope $VnetId
}
```

## <a name="create-a-resource-group"></a>Criar um grupo de recursos
Crie um [grupo de recursos do Azure](../azure-resource-manager/resource-group-overview.md) com o comando [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup). Um grupo de recursos é um contentor lógico no qual os recursos do Azure são implementados e geridos como um grupo. O exemplo seguinte cria um grupo de recursos com o nome `myResourceGroup` na localização `westeurope`.

```powershell
New-AzureRmResourceGroup -Location $DataFactoryLocation -Name $ResourceGroupName
```

## <a name="create-a-data-factory"></a>Criar uma fábrica de dados
Execute o comando seguinte para criar uma fábrica de dados:

```powershell
Set-AzureRmDataFactoryV2 -ResourceGroupName $ResourceGroupName `
                         -Location $DataFactoryLocation `
                         -Name $DataFactoryName
```

## <a name="create-an-integration-runtime"></a>Criar um integration runtime
Execute o seguinte comando para criar um integration runtime do Azure-SSIS que execute pacotes de SSIS no Azure: 

Se estiver a utilizar **SQL Database do Azure** para alojar a base de dados do SSISDB (catálogo SSIS): 


```powershell
$secpasswd = ConvertTo-SecureString $SSISDBServerAdminPassword -AsPlainText -Force
$serverCreds = New-Object System.Management.Automation.PSCredential($SSISDBServerAdminUserName, $secpasswd)
Set-AzureRmDataFactoryV2IntegrationRuntime  -ResourceGroupName $ResourceGroupName `
                                            -DataFactoryName $DataFactoryName `
                                            -Name $AzureSSISName `
                                            -Type Managed `
                                            -CatalogServerEndpoint $SSISDBServerEndpoint `
                                            -CatalogAdminCredential $serverCreds `
                                            -CatalogPricingTier $SSISDBPricingTier `
                                            -Description $AzureSSISDescription `
                                            -Location $AzureSSISLocation `
                                            -NodeSize $AzureSSISNodeSize `
                                            -NodeCount $AzureSSISNodeNumber `
                                            -MaxParallelExecutionsPerNode $AzureSSISMaxParallelExecutionsPerNode
```

Não é necessário transmitir valores para VNetId e a sub-rede, a menos que precisa de acesso a dados no local, ou seja, tem as origens de dados no local, os destinos nos seus pacotes SSIS. Tem de transmitir o valor do parâmetro CatalogPricingTier. Para obter uma lista dos escalões de preços suportados para a SQL Database do Azure, consulte [dos limites de recursos de base de dados SQL](../sql-database/sql-database-resource-limits.md).

Se estiver a utilizar **Azure geridos instância do SQL (pré-visualização privada)** para alojar a base de dados do SSISDB:

```powershell
$secpasswd = ConvertTo-SecureString $SSISDBServerAdminPassword -AsPlainText -Force
$serverCreds = New-Object System.Management.Automation.PSCredential($SSISDBServerAdminUserName, $secpasswd)
Set-AzureRmDataFactoryV2IntegrationRuntime  -ResourceGroupName $ResourceGroupName `
                                            -DataFactoryName $DataFactoryName `
                                            -Name $AzureSSISName `
                                            -Type Managed `
                                            -CatalogServerEndpoint $SSISDBServerEndpoint `
                                            -CatalogAdminCredential $serverCreds `
                                            -Description $AzureSSISDescription `
                                            -Location $AzureSSISLocation `
                                            -NodeSize $AzureSSISNodeSize `
                                            -NodeCount $AzureSSISNodeNumber `
                                            -MaxParallelExecutionsPerNode $AzureSSISMaxParallelExecutionsPerNode `
                                            -VnetId $VnetId `
                                            -Subnet $SubnetName
```

Tem de passar valores para parâmetros VnetId e a sub-rede com o Azure geridos instância do SQL (pré-visualização privada) que associa uma VNet. O parâmetro CatalogPricingTier aplicável geridos instância do SQL do Azure. 

## <a name="start-integration-runtime"></a>Iniciar o integration runtime
Execute o seguinte comando para iniciar o integration runtime do Azure-SSIS: 

```powershell
write-host("##### Starting #####")
Start-AzureRmDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                             -DataFactoryName $DataFactoryName `
                                             -Name $AzureSSISName `
                                             -Force

write-host("##### Completed #####")
write-host("If any cmdlet is unsuccessful, please consider using -Debug option for diagnostics.")                                  
```
Este comando demora entre **20 e 30 minutos** a concluir. 

## <a name="deploy-ssis-packages"></a>Implementar pacotes de SSIS
Agora, utilize o SQL Server Data Tools (SSDT) ou o SQL Server Management Studio (SSMS) para implementar os pacotes de SSIS no Azure. Ligue ao seu SQL server do Azure que aloja o catálogo de SSIS (SSISDB). O nome do SQL server do Azure está no formato: &lt;servername&gt;.database.windows.net (para a Base de Dados SQL do Azure). Veja o artigo [Implementar pacotes](/sql/integration-services/packages/deploy-integration-services-ssis-projects-and-packages#deploy-packages-to-integration-services-server) para obter instruções. 

## <a name="next-steps"></a>Passos seguintes
Consulte os outros tópicos do Azure-SSIS IR nesta documentação:

- [Tempo de execução do Azure-SSIS integração](concepts-integration-runtime.md#azure-ssis-integration-runtime). Este artigo fornece informações concetuais sobre tempos de execução de integração em geral, incluindo o IR. SSIS do Azure 
- [Tutorial: implementar pacotes do SSIS no Azure](tutorial-deploy-ssis-packages-azure.md). Este artigo disponibiliza instruções passo a passo para criar um IR Azure-SSIS e utiliza uma base de dados da Base de Dados SQL do Azure para alojar o catálogo do SSIS. 
- [Monitor an Azure-SSIS IR](monitor-integration-runtime.md#azure-ssis-integration-runtime) (Monitorizar um IR Azure-SSIS) Este artigo mostra como obter informações sobre um IR Azure-SSIS e descrições dos estados nas informações devolvidas. 
- [Manage an Azure-SSIS IR](manage-azure-ssis-integration-runtime.md) (Gerir um IR Azure-SSIS). Este artigo mostra-lhe como parar, iniciar ou remover um IR Azure-SSIS. Mostra também como aumentá-lo horizontalmente mediante a adição de mais nós ao mesmo. 
- [Join an Azure-SSIS IR to a VNet](join-azure-ssis-integration-runtime-virtual-network.md) (Associar um IR Azure-SSIS a uma VNet). Este artigo disponibiliza informações concetuais sobre como associar um IR Azure-SSIS a uma rede virtual do Azure (VNet). Também descreve os passos para utilizar o portal do Azure para configurar a VNet, de modo a que o IR Azure-SSIS se possa associar à mesma. 