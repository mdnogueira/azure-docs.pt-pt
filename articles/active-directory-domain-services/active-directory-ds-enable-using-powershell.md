---
title: Ativar o Azure Active Directory Domain Services com o PowerShell | Microsoft Docs
description: Ativar o Azure Active Directory Domain Services com o PowerShell
services: active-directory-ds
documentationcenter: 
author: mahesh-unnikrishnan
manager: mahesh-unnikrishnan
editor: curtand
ms.assetid: d4bc5583-6537-4cd9-bc4b-7712fdd9272a
ms.service: active-directory-ds
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/16/2017
ms.author: maheshu
ms.openlocfilehash: 667e68ad444386a5fe29f9909042fdfa7ca66581
ms.sourcegitcommit: c25cf136aab5f082caaf93d598df78dc23e327b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/15/2017
---
# <a name="enable-azure-active-directory-domain-services-using-powershell"></a>Ativar o Azure Active Directory Domain Services com o PowerShell
Este artigo mostra como ativar os serviços de domínio do Azure Active Directory (AD) através do PowerShell.

## <a name="task-1-install-the-required-powershell-modules"></a>Tarefa 1: Instalar os módulos do PowerShell necessários

### <a name="install-and-configure-azure-ad-powershell"></a>Instalar e configurar o Azure AD PowerShell
Siga as instruções no artigo para [instalar o módulo Azure AD PowerShell e ligar ao Azure AD](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2?toc=%2fazure%2factive-directory-domain-services%2ftoc.json).

### <a name="install-and-configure-azure-powershell"></a>Instalar e configurar o Azure PowerShell
Siga as instruções no artigo para [instalar o módulo Azure PowerShell e ligue à sua subscrição do Azure](https://docs.microsoft.com/powershell/azure/install-azurerm-ps?toc=%2fazure%2factive-directory-domain-services%2ftoc.json).


## <a name="task-2-create-the-required-service-principal-in-your-azure-ad-directory"></a>Tarefa 2: Criar o principal de serviço necessário no diretório do Azure AD
Escreva o seguinte comando do PowerShell para criar o principal de serviço necessário para os serviços de domínio do Azure AD no seu diretório do Azure AD.
```powershell
# Create the service principal for Azure AD Domain Services.
New-AzureADServicePrincipal -AppId “2565bd9d-da50-47d4-8b85-4c97f669dc36”
```

## <a name="task-3-create-and-configure-the-aad-dc-administrators-group"></a>Tarefa 3: Criar e configurar o grupo 'AAD DC administradores'
A tarefa seguinte consiste em criar o grupo de administrador que será utilizado para delegar tarefas de administração no seu domínio gerido.
```powershell
# Create the delegated administration group for AAD Domain Services.
New-AzureADGroup -DisplayName "AAD DC Administrators" `
  -Description "Delegated group to administer Azure AD Domain Services" `
  -SecurityEnabled $true -MailEnabled $false `
  -MailNickName "AADDCAdministrators"
```

Agora que criou o grupo, adicione alguns utilizadores ao grupo.
```powershell
# First, retrieve the object ID of the newly created 'AAD DC Administrators' group.
$GroupObjectId = Get-AzureADGroup `
  -Filter "DisplayName eq 'AAD DC Administrators'" | `
  Select-Object ObjectId

# Now, retrieve the object ID of the user you'd like to add to the group.
$UserObjectId = Get-AzureADUser `
  -Filter "UserPrincipalName eq 'admin@contoso100.onmicrosoft.com'" | `
  Select-Object ObjectId

# Add the user to the 'AAD DC Administrators' group.
Add-AzureADGroupMember -ObjectId $GroupObjectId -RefObjectId $UserObjectId
```

## <a name="task-4-register-the-azure-ad-domain-services-resource-provider"></a>Tarefa 4: Registar o fornecedor de recursos de serviços de domínio do Azure AD
Escreva o seguinte comando do PowerShell para registar o fornecedor de recursos para os serviços de domínio do Azure AD:
```powershell
# Register the resource provider for Azure AD Domain Services with Resource Manager.
Register-AzureRmResourceProvider -ProviderNamespace Microsoft.AAD
```

## <a name="task-5-create-a-resource-group"></a>Tarefa 5: Criar um grupo de recursos
Escreva o seguinte comando do PowerShell para criar um grupo de recursos:
```powershell
$ResourceGroupName = "ContosoAaddsRg"

# Create the resource group.
New-AzureRmResourceGroup `
  -Name $ResourceGroupName `
  -Location westus
```

Pode criar a rede virtual e o domínio gerido dos serviços de domínio do Azure AD deste grupo de recursos.


## <a name="task-6-create-and-configure-the-virtual-network"></a>Tarefa 6: Criar e configurar a rede virtual
Agora, crie a rede virtual na qual ativar os serviços de domínio do Azure AD. Certifique-se que crie uma sub-rede dedicada para os serviços de domínio do Azure AD. Não implemente VMs de cargas de trabalho para esta sub-rede dedicado.

Escreva os seguintes comandos do PowerShell para criar uma rede virtual com uma sub-rede dedicada para os serviços de domínio do Azure AD.

```powershell
$ResourceGroupName = "ContosoAaddsRg"
$VnetName = "DomainServicesVNet_WUS"

# Create the dedicated subnet for AAD Domain Services.
$AaddsSubnet = New-AzureRmVirtualNetworkSubnetConfig `
  -Name DomainServices `
  -AddressPrefix 10.0.0.0/24

$WorkloadSubnet = New-AzureRmVirtualNetworkSubnetConfig `
  -Name Workloads `
  -AddressPrefix 10.0.1.0/24

# Create the virtual network in which you will enable Azure AD Domain Services.
$Vnet=New-AzureRmVirtualNetwork `
  -ResourceGroupName $ResourceGroupName `
  -Location westus `
  -Name $VnetName `
  -AddressPrefix 10.0.0.0/16 `
  -Subnet $AaddsSubnet,$WorkloadSubnet
```


## <a name="task-7-provision-the-azure-ad-domain-services-managed-domain"></a>Tarefa 7: Aprovisionar domínio gerido dos serviços de domínio do Azure AD
Escreva o seguinte comando do PowerShell para ativar os serviços de domínio do Azure AD para o diretório:

```powershell
# Enable Azure AD Domain Services for the directory.
New-AzureRmResource -ResourceId "/subscriptions/$AzureSubscriptionId/resourceGroups/$ResourceGroupName/providers/Microsoft.AAD/DomainServices/$ManagedDomainName" `
  -Location "westus" `
  -Properties @{"DomainName"=$ManagedDomainName; `
    "SubnetId"="/subscriptions/$AzureSubscriptionId/resourceGroups/$ResourceGroupName/providers/Microsoft.Network/virtualNetworks/$VnetName/subnets/DomainServices"} `
  -ApiVersion 2017-06-01 -Force -Verbose
```

> [!WARNING]
> **Não se esqueça de passos de configuração adicionais depois de aprovisionar o seu domínio gerido.**
> Depois do seu domínio gerido é aprovisionado, ainda tem de concluir as seguintes tarefas:
> * **[Atualizar as definições de DNS](active-directory-ds-getting-started-dns.md)**  para a rede virtual para máquinas virtuais pode encontrar o domínio gerido para associação a um domínio ou a autenticação.
* **[Ativar a sincronização de palavra-passe para os serviços de domínio do Azure AD](active-directory-ds-getting-started-password-sync.md)**, pelo que os utilizadores finais podem iniciar sessão no domínio gerido utilizando as respetivas credenciais empresariais.
>


## <a name="powershell-script"></a>Script do PowerShell
O script do PowerShell utilizado para executar todas as tarefas apresentadas neste artigo está abaixo. Copie o script e guardá-lo para um ficheiro com uma extensão '. ps1'. Execute o script do PowerShell ou utilizando o PowerShell Integrated Scripting Environment (ISE).

> [!NOTE]
> **Permissões necessárias para executar este script** para ativar os serviços de domínio do Azure AD, tem de ser administrador global do diretório do Azure AD. Além disso, terá de menos privilégios de "Contribuinte" na rede virtual na qual ativar os serviços de domínio do Azure AD.
>

```powershell
# Change the following values to match your deployment.
$AaddsAdminUserUpn = "admin@contoso100.onmicrosoft.com"
$AzureSubscriptionId = "YOUR_AZURE_SUBSCRIPTION_ID"
$ManagedDomainName = "contoso100.com"
$ResourceGroupName = "ContosoAaddsRg"
$VnetName = "DomainServicesVNet_WUS"
$AzureLocation = "westus"

# Connect to your Azure AD directory.
Connect-AzureAD

# Login to your Azure subscription.
Login-AzureRmAccount

# Create the service principal for Azure AD Domain Services.
New-AzureADServicePrincipal -AppId “2565bd9d-da50-47d4-8b85-4c97f669dc36”

# Create the delegated administration group for AAD Domain Services.
New-AzureADGroup -DisplayName "AAD DC Administrators" `
  -Description "Delegated group to administer Azure AD Domain Services" `
  -SecurityEnabled $true -MailEnabled $false `
  -MailNickName "AADDCAdministrators"

# First, retrieve the object ID of the newly created 'AAD DC Administrators' group.
$GroupObjectId = Get-AzureADGroup `
  -Filter "DisplayName eq 'AAD DC Administrators'" | `
  Select-Object ObjectId

# Now, retrieve the object ID of the user you'd like to add to the group.
$UserObjectId = Get-AzureADUser `
  -Filter "UserPrincipalName eq '$AaddsAdminUserUpn'" | `
  Select-Object ObjectId

# Add the user to the 'AAD DC Administrators' group.
Add-AzureADGroupMember -ObjectId $GroupObjectId.ObjectId -RefObjectId $UserObjectId.ObjectId

# Register the resource provider for Azure AD Domain Services with Resource Manager.
Register-AzureRmResourceProvider -ProviderNamespace Microsoft.AAD

# Create the resource group.
New-AzureRmResourceGroup `
  -Name $ResourceGroupName `
  -Location $AzureLocation

# Create the dedicated subnet for AAD Domain Services.
$AaddsSubnet = New-AzureRmVirtualNetworkSubnetConfig `
  -Name DomainServices `
  -AddressPrefix 10.0.0.0/24

$WorkloadSubnet = New-AzureRmVirtualNetworkSubnetConfig `
  -Name Workloads `
  -AddressPrefix 10.0.1.0/24

# Create the virtual network in which you will enable Azure AD Domain Services.
$Vnet=New-AzureRmVirtualNetwork `
  -ResourceGroupName $ResourceGroupName `
  -Location $AzureLocation `
  -Name $VnetName `
  -AddressPrefix 10.0.0.0/16 `
  -Subnet $AaddsSubnet,$WorkloadSubnet

# Enable Azure AD Domain Services for the directory.
New-AzureRmResource -ResourceId "/subscriptions/$AzureSubscriptionId/resourceGroups/$ResourceGroupName/providers/Microsoft.AAD/DomainServices/$ManagedDomainName" `
  -Location $AzureLocation `
  -Properties @{"DomainName"=$ManagedDomainName; `
    "SubnetId"="/subscriptions/$AzureSubscriptionId/resourceGroups/$ResourceGroupName/providers/Microsoft.Network/virtualNetworks/$VnetName/subnets/DomainServices"} `
  -ApiVersion 2017-06-01 -Force -Verbose
```

> [!WARNING]
> **Não se esqueça de passos de configuração adicionais depois de aprovisionar o seu domínio gerido.**
> Depois do seu domínio gerido é aprovisionado, ainda tem de concluir as seguintes tarefas:
> * Atualize as definições de DNS para a rede virtual para que máquinas virtuais pode encontrar o domínio gerido para associação a um domínio ou a autenticação.
* Ative a sincronização de palavra-passe para os serviços de domínio do Azure AD para que os utilizadores finais podem iniciar sessão no domínio gerido utilizando as respetivas credenciais empresariais.
>

## <a name="next-steps"></a>Passos seguintes
Depois de criado o seu domínio gerido, execute as seguintes tarefas de configuração para poder utilizar o domínio gerido:

* [Atualizar as definições do servidor DNS para a rede virtual para apontar para o seu domínio gerido](active-directory-ds-getting-started-dns.md)
* [Ativar a sincronização de palavra-passe para o seu domínio gerido](active-directory-ds-getting-started-password-sync.md)
