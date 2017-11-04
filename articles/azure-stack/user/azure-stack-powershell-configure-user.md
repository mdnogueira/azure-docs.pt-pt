---
title: Configurar o Azure pilha ambiente do utilizador do PowerShell | Microsoft Docs
description: Configurar o Azure pilha ambiente do utilizador do PowerShell
services: azure-stack
documentationcenter: 
author: SnehaGunda
manager: byronr
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/16/2017
ms.author: sngun
ms.openlocfilehash: e0ad968cac50ebb1e9ca0a4ff228c748f2da5f28
ms.sourcegitcommit: a7c01dbb03870adcb04ca34745ef256414dfc0b3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/17/2017
---
# <a name="configure-the-azure-stack-users-powershell-environment"></a>Configurar o Azure pilha ambiente do utilizador do PowerShell

Como um utilizador de pilha do Azure, pode configurar o ambiente do seu Azure pilha Kit de desenvolvimento PowerShell. Depois de configurar, pode utilizar o PowerShell para gerir a pilha do Azure, recursos, tais como subscrevem ofertas, criar máquinas virtuais, implementar modelos Azure Resource Manager, etc. Este tópico tem um âmbito para utilizar com o utilizador ambientes apenas, se pretender configurar o PowerShell para o ambiente de operador da nuvem, consulte o [configurar o ambiente de PowerShell o operador de pilha do Azure](../azure-stack-powershell-configure-admin.md) tópico. 

## <a name="prerequisites"></a>Pré-requisitos 

Execute os seguintes pré-requisitos a partir de [kit de desenvolvimento](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-remote-desktop), ou de um baseados em Windows externo cliente se estiver [ligado através de VPN](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-vpn):

* Instalar [módulos do Azure pilha compatível com o Azure PowerShell](azure-stack-powershell-install.md).  
* Transferir o [ferramentas necessárias para trabalhar com a pilha de Azure](azure-stack-powershell-download.md). 

## <a name="configure-the-user-environment-and-sign-in-to-azure-stack"></a>Configurar o ambiente de utilizador e inicie sessão na pilha do Azure

Com base no tipo de implementação (Azure AD ou AD FS), execute um do script seguinte para configurar o PowerShell para a pilha do Azure (Certifique-se de que substitua o AAD tenantName, GraphAudience endpoint e ArmEndpoint valores de acordo com a configuração do ambiente):

### <a name="azure-active-directory-aad-based-deployments"></a>Implementações baseadas em do Azure Active Directory (AAD)
       
  ```powershell
  # Navigate to the downloaded folder and import the **Connect** PowerShell module
  Set-ExecutionPolicy RemoteSigned
  Import-Module .\Connect\AzureStack.Connect.psm1

  # For Azure Stack development kit, this value is set to https://management.local.azurestack.external. To get this value for Azure Stack integrated systems, contact your service provider.
  $ArmEndpoint = "<Resource Manager endpoint for your environment>"

  # For Azure Stack development kit, this value is set to https://graph.windows.net/. To get this value for Azure Stack integrated systems, contact your service provider.
  $GraphAudience = "<GraphAudience endpoint for your environment>"

  # Register an AzureRM environment that targets your Azure Stack instance
  Add-AzureRMEnvironment `
    -Name "AzureStackUser" `
    -ArmEndpoint $ArmEndpoint

  # Set the GraphEndpointResourceId value
  Set-AzureRmEnvironment `
    -Name "AzureStackUser" `
    -GraphAudience $GraphAudience

  # Get the Active Directory tenantId that is used to deploy Azure Stack
  $TenantID = Get-AzsDirectoryTenantId `
    -AADTenantName "<myDirectoryTenantName>.onmicrosoft.com" `
    -EnvironmentName "AzureStackUser"

  # Sign in to your environment
  Login-AzureRmAccount `
    -EnvironmentName "AzureStackUser" `
    -TenantId $TenantID 
   ```

### <a name="active-directory-federation-services-ad-fs-based-deployments"></a>Implementações baseadas em serviços de Federação do Active Directory (AD FS) 
          
  ```powershell
  # Navigate to the downloaded folder and import the **Connect** PowerShell module
  Set-ExecutionPolicy RemoteSigned
  Import-Module .\Connect\AzureStack.Connect.psm1

  # For Azure Stack development kit, this value is set to https://management.local.azurestack.external. To get this value for Azure Stack integrated systems, contact your service provider.
  $ArmEndpoint = "<Resource Manager endpoint for your environment>"

  # For Azure Stack development kit, this value is set to https://graph.local.azurestack.external/. To get this value for Azure Stack integrated systems, contact your service provider.
  $GraphAudience = "<GraphAudience endpoint for your environment>"

  # Register an AzureRM environment that targets your Azure Stack instance
  Add-AzureRMEnvironment `
    -Name "AzureStackUser" `
    -ArmEndpoint $ArmEndpoint

  # Set the GraphEndpointResourceId value
  Set-AzureRmEnvironment `
    -Name "AzureStackUser" `
    -GraphAudience $GraphAudience `
    -EnableAdfsAuthentication:$true

  # Get the Active Directory tenantId that is used to deploy Azure Stack     
  $TenantID = Get-AzsDirectoryTenantId `
    -ADFS `
    -EnvironmentName "AzureStackUser"

  # Sign in to your environment
  Login-AzureRmAccount `
    -EnvironmentName "AzureStackUser" `
    -TenantId $TenantID 
  ```

## <a name="register-resource-providers"></a>Registar fornecedores de recursos

Quando utilizar uma subscrição de utilizador recentemente criado não tem quaisquer recursos implementados através do portal, os fornecedores de recursos não são registados automaticamente. Deve registá-los explicitamente utilizando o script seguinte:

```powershell
foreach($s in (Get-AzureRmSubscription)) {
        Select-AzureRmSubscription -SubscriptionId $s.SubscriptionId | Out-Null
        Write-Progress $($s.SubscriptionId + " : " + $s.SubscriptionName)
Get-AzureRmResourceProvider -ListAvailable | Register-AzureRmResourceProvider -Force
    } 
```

## <a name="test-the-connectivity"></a>Testar a conectividade

Agora que já obtivemos tudo configurado, vamos utilizar o PowerShell para criar recursos na pilha do Azure. Por exemplo, pode criar um grupo de recursos para uma aplicação e adicionar uma máquina virtual. Utilize o seguinte comando para criar um grupo de recursos com o nome "MyResourceGroup":

```powershell
New-AzureRmResourceGroup -Name "MyResourceGroup" -Location "Local"
```

## <a name="next-steps"></a>Passos seguintes
* [Desenvolver modelos para a pilha do Azure](azure-stack-develop-templates.md)
* [Implementar modelos com o PowerShell](azure-stack-deploy-template-powershell.md)
