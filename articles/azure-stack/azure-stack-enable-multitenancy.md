---
title: "Ativar vários inquilinos na pilha do Azure | Microsoft Docs"
description: "Saiba como suportar vários diretórios do Azure Active Directory na pilha do Azure"
services: azure-stack
documentationcenter: 
author: HeathL17
manager: byronr
editor: 
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/25/2017
ms.author: helaw
ms.openlocfilehash: 3a90057b43e3f2074e72f3d0f896b35b4884368b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="enable-multi-tenancy-in-azure-stack"></a>Ativar vários inquilinos na pilha do Azure

*Aplica-se a: Azure pilha integrado sistemas e Kit de desenvolvimento de pilha do Azure*

Pode configurar a pilha do Azure para suportar os utilizadores a partir de múltiplos inquilinos do Azure Active Directory (Azure AD) para utilizar os serviços na pilha do Azure. Por exemplo, considere o seguinte cenário:

 - É o administrador de serviço da contoso.onmicrosoft.com, onde a pilha do Azure está instalada.
 - Maria é o administrador de diretório de fabrikam.onmicrosoft.com, onde estão localizados os utilizadores convidados. 
 - Empresa de Maria recebe IaaS e PaaS serviços a partir da sua empresa e tem de permitir que os utilizadores a partir do diretório de convidado (fabrikam.onmicrosoft.com) iniciar sessão e utilizar recursos de pilha do Azure no contoso.onmicrosoft.com.

Este guia fornece os passos necessários para configurar vários inquilinos na pilha do Azure, no contexto deste cenário.  Neste cenário, a e a Maria tem de concluir os passos para permitir que os utilizadores da Fabrikam para iniciar sessão e consumir os serviços de implementação de pilha do Azure no Contoso.  

## <a name="before-you-begin"></a>Antes de começar
Existem alguns pré-requisitos para a conta antes de configurar vários inquilinos na pilha do Azure:
  
 - Utilizador e a Maria tem coordenar passos administrativos entre o diretório de que pilha do Azure está instalada no (Contoso) e o diretório de convidado (Fabrikam).  
 - Certifique-se de que já [instalado](azure-stack-powershell-install.md) e [configurado](azure-stack-powershell-configure-admin.md) PowerShell para a pilha do Azure.
 - [Transferir as ferramentas de pilha do Azure](azure-stack-powershell-download.md)e importe os módulos de ligar e identidade:

    ````PowerShell
        Import-Module .\Connect\AzureStack.Connect.psm1
        Import-Module .\Identity\AzureStack.Identity.psm1
    ```` 
 - Maria exigirá [VPN](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-vpn) acesso a pilha do Azure. 

## <a name="configure-azure-stack-directory"></a>Configure o diretório de pilha do Azure
Nesta secção, irá configurar pilha do Azure para permitir inícios de sessão a partir da Fabrikam do Azure AD directory inquilinos.

### <a name="onboard-guest-directory-tenant"></a>Carregar de convidado diretório inquilino
Em seguida, integrar o inquilino do convidado (Fabrikam) à pilha do Azure.  Este passo configura o Azure Resource Manager para aceitar os utilizadores e de principais de serviço de inquilino do convidado.

````PowerShell
$adminARMEndpoint = "https://adminmanagement.local.azurestack.external"

## Replace the value below with the Azure Stack directory
$azureStackDirectoryTenant = "contoso.onmicrosoft.com"

## Replace the value below with the guest tenant directory. 
$guestDirectoryTenantToBeOnboarded = "fabrikam.onmicrosoft.com"

Register-AzSGuestDirectoryTenant -AdminResourceManagerEndpoint $adminARMEndpoint `
 -DirectoryTenantName $azureStackDirectoryTenant `
 -GuestDirectoryTenantName $guestDirectoryTenantToBeOnboarded `
 -Location "local"
````



## <a name="configure-guest-directory"></a>Configure o diretório de convidado
Depois de concluir os passos no diretório de pilha do Azure, a Maria tem de fornecer consentimento para acederem ao diretório de convidado de pilha do Azure e registar a pilha do Azure com o diretório de convidado. 

### <a name="registering-azure-stack-with-the-guest-directory"></a>Registar a pilha do Azure com o diretório de convidado
Depois do administrador do diretório de convidado forneceu consentimento para pilha do Azure para aceder ao diretório da Fabrikam, tem de registar pilha do Azure com o inquilino de diretório da Fabrikam.

````PowerShell
$tenantARMEndpoint = "https://management.local.azurestack.external"
    
## Replace the value below with the guest tenant directory. 
$guestDirectoryTenantName = "fabrikam.onmicrosoft.com"

Register-AzSWithMyDirectoryTenant `
 -TenantResourceManagerEndpoint $tenantARMEndpoint `
 -DirectoryTenantName $guestDirectoryTenantName ` 
 -Verbose 
````
## <a name="direct-users-to-sign-in"></a>Utilizadores diretamente para iniciar sessão
Agora que tem e a Maria tem concluído os passos para o diretório de carregar Maria, Maria direcionar os utilizadores da Fabrikam para iniciar sessão.  Fabrikam utilizadores (ou seja, utilizadores com o sufixo de fabrikam.onmicrosoft.com) a iniciar sessão, visitando https://portal.local.azurestack.external.  

Maria irão direcionar qualquer [principais externas](../active-directory/active-directory-understanding-resource-access.md) no diretório Fabrikam (ou seja, utilizadores no diretório Fabrikam sem o sufixo de fabrikam.onmicrosoft.com) para iniciar sessão com https://portal.local.azurestack.external/fabrikam.onmicrosoft.com.  Se não utilizar este URL, estes são enviados para o respetivo diretório predefinido (Fabrikam) e recebem um erro que indica o que seu administrador não se consentiu.

## <a name="next-steps"></a>Passos Seguintes

- [Gerir fornecedores de delegado](azure-stack-delegated-provider.md)
- [Conceitos chaves de pilha do Azure](azure-stack-key-features.md)
