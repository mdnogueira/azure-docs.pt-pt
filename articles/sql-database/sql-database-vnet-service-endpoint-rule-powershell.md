---
title: "PowerShell para pontos finais do serviço de rede Virtual e as regras do SQL Server | Microsoft Docs"
description: "Fornece scripts do PowerShell para criar e gerir pontos finais do serviço Virtual para a base de dados do SQL do Azure."
services: sql-database
documentationcenter: 
author: MightyPen
manager: jhubbard
editor: 
tags: 
ms.assetid: 
ms.service: sql-database
ms.custom: VNet Service endpoints
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: Inactive
ms.date: 09/15/2017
ms.author: genemi
ms.openlocfilehash: 9f58ea34dad5d4436c13b64653040bd2a57c299e
ms.sourcegitcommit: e5355615d11d69fc8d3101ca97067b3ebb3a45ef
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/31/2017
---
# <a name="use-powershell-to-create-a-virtual-service-endpoint-and-rule-for-azure-sql-database"></a>Utilizar o PowerShell para criar um ponto final do serviço Virtual e uma regra para a SQL Database do Azure

Este artigo fornece e explica um script do PowerShell que efetua as seguintes ações:

1. Cria um Microsoft Azure *ponto final do serviço Virtual* na sua sub-rede.
2. Adiciona o ponto final para a firewall do seu servidor de base de dados do Azure SQL, para criar um *regra de rede virtual*.

As motivações para criar uma regra são explicadas em: [pontos finais de serviço virtuais para a SQL Database do Azure][sql-db-vnet-service-endpoint-rule-overview-735r].

> [!TIP]
> Se tudo o que precisa avaliar ou adicionar ponto final do serviço Virtual *nome do tipo* da base de dados do SQL Server para a sub-rede, pode avançar diretamente para o nosso mais [direcionar o script do PowerShell](#a-verify-subnet-is-endpoint-ps-100).

#### <a name="major-cmdlets"></a>Cmdlets de principais

Este artigo cmdlet com o nome de enfatizar **New-AzureRmSqlServerVirtualNetworkRule**, que adiciona o ponto final de sub-rede para a lista de controlo de acesso (ACL) do seu servidor da SQL Database do Azure, criando uma regra.

A lista seguinte mostra a sequência de outras *principais* cmdlets que tem de executar para se preparar para a chamada **New-AzureRmSqlServerVirtualNetworkRule**. Neste artigo, destas chamadas ocorrerem no [script 3 "Virtual rede regra"](#a-script-30):

1. [Novo AzureRmVirtualNetworkSubnetConfig](https://docs.microsoft.com/powershell/module/azurerm.network/new-azurermvirtualnetworksubnetconfig): cria um objeto de sub-rede.

2. [Novo-AzureRmVirtualNetwork](https://docs.microsoft.com/powershell/module/azurerm.network/new-azurermvirtualnetwork): cria a rede virtual, atribuir a sub-rede.

3. [Conjunto AzureRmVirtualNetworkSubnetConfig](https://docs.microsoft.com/powershell/module/azurerm.network/Set-AzureRmVirtualNetworkSubnetConfig): atribui um ponto final de serviço virtuais para a sub-rede.

4. [Set-AzureRmVirtualNetwork](https://docs.microsoft.com/powershell/module/azurerm.network/Set-AzureRmVirtualNetwork): persistir as atualizações efetuadas à sua rede virtual.

5. **Novo AzureRmSqlServerVirtualNetworkRule**: depois da sub-rede é um ponto final, adiciona a sub-rede como uma regra de rede virtual, a ACL do seu servidor de SQL Database do Azure.

#### <a name="prerequisites-for-running-powershell"></a>Pré-requisitos para executar o PowerShell

- Pode já iniciar sessão no Azure, tal como através de [portal do Azure][http-azure-portal-link-ref-477t].
- Já pode executar scripts do PowerShell.

#### <a name="one-script-divided-into-four-chunks"></a>Um script dividido em quatro segmentos

A nossa demonstração script do PowerShell está dividida numa sequência de scripts mais pequenos. A divisão facilita learning e proporciona flexibilidade. Os scripts devem ser executados na respetiva sequência indicada. Se não tiver agora tempo para executar os scripts, nosso resultado do teste real é apresentado ao script 4.






<a name="a-script-10" />

## <a name="script-1-variables"></a>Script 1: variáveis

Este script do PowerShell primeiro atribui valores de variáveis. Os scripts subsequentes dependem estas variáveis.

> [!IMPORTANT]
> Antes de executar este script, pode editar os valores, se assim o desejar. Por exemplo, se já tiver um grupo de recursos, poderá editar o nome do grupo de recursos como o valor atribuído.
>
>  O nome da sua subscrição deve ser editado para o script.

#### <a name="powershell-script-1-source-code"></a>Código de origem de 1 de script do PowerShell

```powershell
######### Script 1 ########################################
##   LOG into to your Azure account.                     ##
##   (Needed only one time per powershell.exe session.)  ##
###########################################################

$yesno = Read-Host 'Do you need to log into Azure (only one time per powershell.exe session)?  [yes/no]';
if ('yes' -eq $yesno) { Login-AzureRmAccount; }

###########################################################
##  Assignments to variables used by the later scripts.  ##
###########################################################

# You can edit these values, if necessary.

$SubscriptionName = 'yourSubscriptionName';
Select-AzureRmSubscription -SubscriptionName $SubscriptionName;

$ResourceGroupName = 'RG-YourNameHere';
$Region            = 'westcentralus';

$VNetName            = 'myVNet';
$SubnetName          = 'mySubnet';
$VNetAddressPrefix   = '10.1.0.0/16';
$SubnetAddressPrefix = '10.1.1.0/24';
$VNetRuleName        = 'myFirstVNetRule-ForAcl';

$SqlDbServerName         = 'mysqldbserver-forvnet';
$SqlDbAdminLoginName     = 'ServerAdmin';
$SqlDbAdminLoginPassword = 'ChangeYourAdminPassword1';

$ServiceEndpointTypeName_SqlDb = 'Microsoft.Sql';  # Official type name.

Write-Host 'Completed script 1, the "Variables".';
```





<a name="a-script-20" />

## <a name="script-2-prerequisites"></a>Script 2: pré-requisitos

Prepara este script para o script seguinte, em que é a ação de ponto final. Este script cria automaticamente o seguinte listado itens, mas apenas se estes ainda não existir. Pode ignorar o script 2 se tiver a certeza de que estes itens que já existem:

- Grupo de recursos do Azure
- Servidor de base de dados SQL do Azure

#### <a name="powershell-script-2-source-code"></a>Código de origem do script 2 do PowerShell

```powershell
######### Script 2 ########################################
##   Ensure your Resource Group already exists.          ##
###########################################################

Write-Host "Check whether your Resource Group already exists.";

$gottenResourceGroup = $null;

$gottenResourceGroup = Get-AzureRmResourceGroup `
  -Name        $ResourceGroupName `
  -ErrorAction SilentlyContinue;

if ($null -eq $gottenResourceGroup)
{
    Write-Host "Creating your missing Resource Group - $ResourceGroupName.";

    $gottenResourceGroup = New-AzureRmResourceGroup `
      -Name $ResourceGroupName `
      -Location $Region;

    $gottenResourceGroup;
}
else { Write-Host "Good, your Resource Group already exists - $ResourceGroupName."; }

$gottenResourceGroup = $null;

###########################################################
## Ensure your Azure SQL Database server already exists. ##
###########################################################

Write-Host "Check whether your Azure SQL Database server already exists.";

$sqlDbServer = $null;

$sqlDbServer = Get-AzureRmSqlServer `
  -ResourceGroupName $ResourceGroupName `
  -ServerName        $SqlDbServerName `
  -ErrorAction       SilentlyContinue;

if ($null -eq $sqlDbServer)
{
    Write-Host "Creating the missing Azure SQL Database server - $SqlDbServerName.";

    Write-Host "Gather the credentials necessary to next create an Azure SQL Database server.";

    $sqlAdministratorCredentials = New-Object `
      -TypeName     System.Management.Automation.PSCredential `
      -ArgumentList `
        $SqlDbAdminLoginName, `
        $(ConvertTo-SecureString `
            -String      $SqlDbAdminLoginPassword `
            -AsPlainText `
            -Force `
         );

    if ($null -eq $sqlAdministratorCredentials)
    {
        Write-Host "ERROR, unable to create SQL administrator credentials.  Now ending.";
        return;
    }

    Write-Host "Create your Azure SQL Database server.";

    $sqlDbServer = New-AzureRmSqlServer `
      -ResourceGroupName $ResourceGroupName `
      -ServerName        $SqlDbServerName `
      -Location          $Region `
      -SqlAdministratorCredentials $sqlAdministratorCredentials;

    $sqlDbServer;
}
else { Write-Host "Good, your Azure SQL Database server already exists - $SqlDbServerName."; }

$sqlAdministratorCredentials = $null;
$sqlDbServer                 = $null;

Write-Host 'Completed script 2, the "Prerequisites".';
```






<a name="a-script-30" />

## <a name="script-3-create-an-endpoint-and-a-rule"></a>Script 3: Criar um ponto final e uma regra

Este script cria uma rede virtual com uma sub-rede. Em seguida, atribui o script a **Microsoft.Sql** tipo de ponto final para a sub-rede. Por fim, o script adiciona a sub-rede para a lista de controlo de acesso (ACL) do seu servidor de base de dados SQL, criando uma regra.

#### <a name="powershell-script-3-source-code"></a>Código de origem do script 3 do PowerShell

```powershell
######### Script 3 ########################################
##   Create your virtual network, and give it a subnet.  ##
###########################################################

Write-Host "Define a subnet '$SubnetName', to be given soon to a virtual network.";

$subnet = New-AzureRmVirtualNetworkSubnetConfig `
  -Name            $SubnetName `
  -AddressPrefix   $SubnetAddressPrefix `
  -ServiceEndpoint $ServiceEndpointTypeName_SqlDb;

Write-Host "Create a virtual network '$VNetName'." `
  "  Give the subnet to the virtual network that we created.";

$vnet = New-AzureRmVirtualNetwork `
  -Name              $VNetName `
  -AddressPrefix     $VNetAddressPrefix `
  -Subnet            $subnet `
  -ResourceGroupName $ResourceGroupName `
  -Location          $Region;

###########################################################
##   Create a Virtual Service endpoint on the subnet.    ##
###########################################################

Write-Host "Assign a Virtual Service endpoint 'Microsoft.Sql' to the subnet.";

$vnet = Set-AzureRmVirtualNetworkSubnetConfig `
  -Name            $SubnetName `
  -AddressPrefix   $SubnetAddressPrefix `
  -VirtualNetwork  $vnet `
  -ServiceEndpoint $ServiceEndpointTypeName_SqlDb;

Write-Host "Persist the updates made to the virtual network > subnet.";

$vnet = Set-AzureRmVirtualNetwork `
  -VirtualNetwork $vnet;

$vnet.Subnets[0].ServiceEndpoints;  # Display the first endpoint.

###########################################################
##   Add the Virtual Service endpoint Id as a rule,      ##
##   into SQL Database ACLs.                             ##
###########################################################

Write-Host "Get the subnet object.";

$vnet = Get-AzureRmVirtualNetwork `
  -ResourceGroupName $ResourceGroupName `
  -Name              $VNetName;

$subnet = Get-AzureRmVirtualNetworkSubnetConfig `
  -Name           $SubnetName `
  -VirtualNetwork $vnet;

Write-Host "Add the subnet .Id as a rule, into the ACLs for your Azure SQL Database server.";

$vnetRuleObject1 = New-AzureRmSqlServerVirtualNetworkRule `
  -ResourceGroupName      $ResourceGroupName `
  -ServerName             $SqlDbServerName `
  -VirtualNetworkRuleName $VNetRuleName `
  -VirtualNetworkSubnetId $subnet.Id;

$vnetRuleObject1;

Write-Host "Verify that the rule is in the SQL DB ACL.";

$vnetRuleObject2 = Get-AzureRmSqlServerVirtualNetworkRule `
  -ResourceGroupName      $ResourceGroupName `
  -ServerName             $SqlDbServerName `
  -VirtualNetworkRuleName $VNetRuleName;

$vnetRuleObject2;

Write-Host 'Completed script 3, the "Virtual-Netowrk-Rule".';
```






<a name="a-script-40" />

## <a name="script-4-clean-up"></a>Script 4: Limpeza

Este script final elimina os recursos de que os scripts de anteriores criados para a demonstração. No entanto, o script pede-lhe confirmação antes de elimina o seguinte:

- Servidor de base de dados SQL do Azure
- Grupo de recursos do Azure

Pode executar o script 4 a qualquer altura após a conclusão do script 1.

#### <a name="powershell-script-4-source-code"></a>Código de origem do script 4 do PowerShell

```powershell
######### Script 4 ########################################
##   Clean-up phase A:  Unconditional deletes.           ##
##                                                       ##
##   1. The test rule is deleted from SQL DB ACL.        ##
##   2. The test endpoint is deleted from the subnet.    ##
##   3. The test virtual network is deleted.             ##
###########################################################

Write-Host "Delete the rule from the SQL DB ACL.";

Remove-AzureRmSqlServerVirtualNetworkRule `
  -ResourceGroupName      $ResourceGroupName `
  -ServerName             $SqlDbServerName `
  -VirtualNetworkRuleName $VNetRuleName `
  -ErrorAction            SilentlyContinue;

Write-Host "Delete the endpoint from the subnet.";

$vnet = Get-AzureRmVirtualNetwork `
  -ResourceGroupName $ResourceGroupName `
  -Name              $VNetName;

Remove-AzureRmVirtualNetworkSubnetConfig `
  -Name $SubnetName `
  -VirtualNetwork $vnet;

Write-Host "Delete the virtual network (thus also deletes the subnet).";

Remove-AzureRmVirtualNetwork `
  -Name              $VNetName `
  -ResourceGroupName $ResourceGroupName `
  -ErrorAction       SilentlyContinue;

###########################################################
##   Clean-up phase B:  Conditional deletes.             ##
##                                                       ##
##   These might have already existed, so user might     ##
##   want to keep.                                       ##
##                                                       ##
##   1. Azure SQL Database server                        ##
##   2. Azure resource group                             ##
###########################################################

$yesno = Read-Host 'CAUTION !: Do you want to DELETE your Azure SQL Database server AND your Resource Group?  [yes/no]';
if ('yes' -eq $yesno)
{
    Write-Host "Remove the Azure SQL DB server.";
    
    Remove-AzureRmSqlServer `
      -ServerName        $SqlDbServerName `
      -ResourceGroupName $ResourceGroupName `
      -ErrorAction       SilentlyContinue;
    
    Write-Host "Remove the Azure Resource Group.";
    
    Remove-AzureRmResourceGroup `
      -Name        $ResourceGroupName `
      -ErrorAction SilentlyContinue;
}
else
{
    Write-Host "Skipped over the DELETE of SQL Database and resource group.";
}

Write-Host 'Completed script 4, the "Clean-Up".';
```






<a name="a-actual-output" />

## <a name="actual-output-from-scripts-1-through-4"></a>Saída real de scripts de 1 a 4

É apresentado em seguida, o resultado do nosso teste executar num formato abreviado. O resultado poderá ser útil no caso de não pretende realmente agora a executar os scripts do PowerShell.

```
[C:\WINDOWS\system32\]
0 >> C:\Demo\PowerShell\sql-database-vnet-service-endpoint-powershell-s1-variables.ps1
Do you need to log into Azure (only one time per powershell.exe session)?  [yes/no]: yes


Environment           : AzureCloud
Account               : xx@microsoft.com
TenantId              : 11111111-1111-1111-1111-111111111111
SubscriptionId        : 22222222-2222-2222-2222-222222222222
SubscriptionName      : MySubscriptionName
CurrentStorageAccount : 



[C:\WINDOWS\system32\]
0 >> C:\Demo\PowerShell\sql-database-vnet-service-endpoint-powershell-s2-prerequisites.ps1
Check whether your Resource Group already exists.
Creating your missing Resource Group - RG-YourNameHere.


ResourceGroupName : RG-YourNameHere
Location          : westcentralus
ProvisioningState : Succeeded
Tags              : 
ResourceId        : /subscriptions/22222222-2222-2222-2222-222222222222/resourceGroups/RG-YourNameHere

Check whether your Azure SQL Database server already exists.
Creating the missing Azure SQL Database server - mysqldbserver-forvnet.
Gather the credentials necessary to next create an Azure SQL Database server.
Create your Azure SQL Database server.

ResourceGroupName        : RG-YourNameHere
ServerName               : mysqldbserver-forvnet
Location                 : westcentralus
SqlAdministratorLogin    : ServerAdmin
SqlAdministratorPassword : 
ServerVersion            : 12.0
Tags                     : 
Identity                 : 

Completed script 2, the "Prerequisites".



[C:\WINDOWS\system32\]
0 >> C:\Demo\PowerShell\sql-database-vnet-service-endpoint-powershell-s3-vnet-rule.ps1
Define a subnet 'mySubnet', to be given soon to a virtual network.
Create a virtual network 'myVNet'.   Give the subnet to the virtual network that we created.
WARNING: The output object type of this cmdlet will be modified in a future release.
Assign a Virtual Service endpoint 'Microsoft.Sql' to the subnet.
Persist the updates made to the virtual network > subnet.

Get the subnet object.
Add the subnet .Id as a rule, into the ACLs for your Azure SQL Database server.
ProvisioningState Service       Locations      
----------------- -------       ---------      
Succeeded         Microsoft.Sql {westcentralus}
                                               
Verify that the rule is in the SQL DB ACL.
                                               
Completed script 3, the "Virtual-Network-Rule".



[C:\WINDOWS\system32\]
0 >> C:\Demo\PowerShell\sql-database-vnet-service-endpoint-powershell-s4-clean-up.ps1
Delete the rule from the SQL DB ACL.

Delete the endpoint from the subnet.


Delete the virtual network (thus also deletes the subnet).
CAUTION !: Do you want to DELETE your Azure SQL Database server AND your Resource Group?  [yes/no]: yes
Remove the Azure SQL DB server.

ResourceGroupName        : RG-YourNameHere
ServerName               : mysqldbserver-forvnet
Location                 : westcentralus
SqlAdministratorLogin    : ServerAdmin
SqlAdministratorPassword : 
ServerVersion            : 12.0
Tags                     : 
Identity                 : 

Remove the Azure Resource Group.
True
Completed script 4, the "Clean-Up".
```

Este é o fim do nosso principal script do PowerShell.





<a name="a-verify-subnet-is-endpoint-ps-100" />

## <a name="verify-your-subnet-is-an-endpoint"></a>Certifique-se de que a sub-rede é um ponto final

Poderá ter uma sub-rede que já foi atribuída o **Microsoft.Sql** nome de tipo, que significa que já se encontra um ponto final de serviço virtuais. Pode utilizar o [portal do Azure] [ http-azure-portal-link-ref-477t] para criar uma regra de rede virtual do ponto final.

Em alternativa, poderá ser não souber se a sua sub-rede tem de **Microsoft.Sql** nome de tipo. Pode executar o seguinte script do PowerShell para realizar estas ações:

1. Se a sub-rede tem de ascertain o **Microsoft.Sql** nome de tipo.
2. Opcionalmente, atribua o nome do tipo se está ausente.
    - O script pede-lhe para *confirmar*, antes de esta aplica-se o tipo de ausente nome.

#### <a name="phases-of-the-script"></a>Fases do script

Seguem-se as fases do script do PowerShell:

1. Iniciar sessão na sua conta do Azure, necessário apenas uma vez por sessão PS.  Atribua variáveis.
2. Pesquisa para a rede virtual e, em seguida, para a sub-rede.
3. A sub-rede está marcada como **Microsoft.Sql** tipo de servidor de ponto final?
4. Adicionar um ponto final de serviço virtuais do nome de tipo **Microsoft.Sql**, na sua sub-rede.

> [!IMPORTANT]
> Antes de executar este script, tem de editar os valores atribuídos para as variáveis $, perto da parte superior do script.

#### <a name="direct-powershell-source-code"></a>Direcionar o código de origem do PowerShell

Este PowerShell script não atualizar tudo, exceto se responder Sim, se é pede-lhe confirmação. O script pode adicionar o nome do tipo **Microsoft.Sql** para a sub-rede. Mas o script tenta adicionar apenas se a sub-rede não possui o nome do tipo.

```powershell
### 1. LOG into to your Azure account, needed only once per PS session.  Assign variables.

$yesno = Read-Host 'Do you need to log into Azure (only one time per powershell.exe session)?  [yes/no]';
if ('yes' -eq $yesno) { Login-AzureRmAccount; }

# Assignments to variables used by the later scripts.
# You can EDIT these values, if necessary.

$SubscriptionName  = 'yourSubscriptionName';
Select-AzureRmSubscription -SubscriptionName "$SubscriptionName";

$ResourceGroupName   = 'yourRGName';
$VNetName            = 'yourVNetName';
$SubnetName          = 'yourSubnetName';
$SubnetAddressPrefix = 'Obtain this value from the Azure portal.'; # Looks roughly like: '10.0.0.0/24'

$ServiceEndpointTypeName_SqlDb = 'Microsoft.Sql';  # Do NOT edit. Is official value.

### 2. Search for your virtual network, and then for your subnet.

# Search for the virtual network.
$vnet = $null;
$vnet = Get-AzureRmVirtualNetwork `
  -ResourceGroupName $ResourceGroupName `
  -Name              $VNetName;

if ($vnet -eq $null)
{
    Write-Host "Caution: No virtual network found by the name '$VNetName'.";
    Return;
}

$subnet = $null;
for ($nn=0; $nn -lt $vnet.Subnets.Count; $nn++)
{
    $subnet = $vnet.Subnets[$nn];
    if ($subnet.Name -eq $SubnetName)
    { break; }
    $subnet = $null;
}

if ($subnet -eq $null)
{
    Write-Host "Caution: No subnet found by the name '$SubnetName'";
    Return;
}

### 3. Is your subnet tagged as 'Microsoft.Sql' endpoint server type?

$endpointMsSql = $null;
for ($nn=0; $nn -lt $subnet.ServiceEndpoints.Count; $nn++)
{
    $endpointMsSql = $subnet.ServiceEndpoints[$nn];
    if ($endpointMsSql.Service -eq $ServiceEndpointTypeName_SqlDb)
    {
        $endpointMsSql;
        break;
    }
    $endpointMsSql = $null;
}

if ($endpointMsSql -ne $null)
{
    Write-Host "Good: Subnet found, and is already tagged as an endpoint of type '$ServiceEndpointTypeName_SqlDb'.";
    Return;
}
else
{
    Write-Host "Caution: Subnet found, but not yet tagged as an endpoint of type '$ServiceEndpointTypeName_SqlDb'.";

    # Ask the user for confirmation.
    $yesno = Read-Host 'Do you want the PS script to apply the endpoint type name to your subnet?  [yes/no]';
    if ('no' -eq $yesno) { Return; }
}

### 4. Add a Virtual Service endpoint of type name 'Microsoft.Sql', on your subnet.

$vnet = Set-AzureRmVirtualNetworkSubnetConfig `
  -Name            $SubnetName `
  -AddressPrefix   $SubnetAddressPrefix `
  -VirtualNetwork  $vnet `
  -ServiceEndpoint $ServiceEndpointTypeName_SqlDb;

# Persist the subnet update.
$vnet = Set-AzureRmVirtualNetwork `
  -VirtualNetwork $vnet;

for ($nn=0; $nn -lt $vnet.Subnets.Count; $nn++)
{ $vnet.Subnets[0].ServiceEndpoints; }  # Display.
```

#### <a name="actual-output"></a>Saída real

O bloco seguinte apresenta o nosso comentários real (com as edições cosmético).

```powershell
<# Our output example (with cosmetic edits), when the subnet was already tagged:

Do you need to log into Azure (only one time per powershell.exe session)?  [yes/no]: no


Environment           : AzureCloud
Account               : xx@microsoft.com
TenantId              : 11111111-1111-1111-1111-111111111111
SubscriptionId        : 22222222-2222-2222-2222-222222222222
SubscriptionName      : MySubscriptionName
CurrentStorageAccount : 


ProvisioningState : Succeeded
Service           : Microsoft.Sql
Locations         : {westcentralus}

Good: Subnet found, and is already tagged as an endpoint of type 'Microsoft.Sql'.
#>
```




<!-- Link references: -->

[sql-db-vnet-service-endpoint-rule-overview-735r]: sql-database-vnet-service-endpoint-rule-overview.md

[http-azure-portal-link-ref-477t]: https://portal.azure.com/

