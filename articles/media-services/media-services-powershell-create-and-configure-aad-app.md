---
title: "Utilizar o PowerShell para criar uma aplicação do Azure AD para aceder à API de serviços de suporte de dados do Azure | Microsoft Docs"
description: "Saiba como utilizar o PowerShell para criar uma aplicação do Azure Active Directory (Azure AD) e defina-o até o acesso a API de serviços de suporte de dados do Azure."
services: media-services
documentationcenter: 
author: Juliako
manager: cfowler
editor: 
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/17/2017
ms.author: juliako
ms.openlocfilehash: eea0f3a03dd77ce56484f32b192299bd97c05300
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="use-powershell-to-create-an-azure-ad-app-to-use-with-the-azure-media-services-api"></a>Utilizar o PowerShell para criar uma aplicação do Azure AD para utilizar com a API de serviços de suporte de dados do Azure

Saiba como utilizar um script do PowerShell para criar uma aplicação do Azure Active Directory (Azure AD) e o principal de serviço para aceder a recursos de Media Services do Azure.  

## <a name="prerequisites"></a>Pré-requisitos

- Uma conta do Azure. Se não tiver uma conta, comece por um [avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/). 
- Uma conta dos Media Services. Para obter mais informações, consulte [criar uma conta de Media Services do Azure no portal do Azure](media-services-portal-create-account.md).
- Azure PowerShell versão 0.8.8 ou uma versão posterior. Para obter mais informações, consulte [como utilizar o Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview).
- Cmdlets do Gestor de recursos do Azure.  

## <a name="create-an-azure-ad-app-by-using-powershell"></a>Criar uma aplicação do Azure AD utilizando o PowerShell  

```powershell
Login-AzureRmAccount
Import-Module AzureRM.Resources
Set-AzureRmContext -SubscriptionId $SubscriptionId
$ServicePrincipal = New-AzureRMADServicePrincipal -DisplayName $ApplicationDisplayName -Password $Password

Get-AzureRmADServicePrincipal -ObjectId $ServicePrincipal.Id 
$NewRole = $null
$Scope = "/subscriptions/your subscription id/resourceGroups/userresourcegroup/providers/microsoft.media/mediaservices/your media account"

$Retries = 0;While ($NewRole -eq $null -and $Retries -le 6)
{
    # Sleep here for a few seconds to allow the service principal application to become active (usually, it will take only a couple of seconds)
    Sleep 15
    New-AzureRMRoleAssignment -RoleDefinitionName Contributor -ServicePrincipalName $ServicePrincipal.ApplicationId -Scope $Scope | Write-Verbose -ErrorAction SilentlyContinue
    $NewRole = Get-AzureRMRoleAssignment -ServicePrincipalName $ServicePrincipal.ApplicationId -ErrorAction SilentlyContinue
    $Retries++;
}
```

Para obter mais informações, veja os artigos seguintes:

- [Utilizar o Azure PowerShell para criar um principal de serviço para aceder aos recursos](../azure-resource-manager/resource-group-authenticate-service-principal.md)
- [Gerir o controlo de acesso baseado em funções utilizando o Azure PowerShell](../active-directory/role-based-access-control-manage-access-powershell.md)
- [Como configurar manualmente o daemon de aplicações através da utilização de certificados](https://github.com/Azure-Samples/active-directory-dotnet-daemon-certificate-credential/blob/master/Manual-Configuration-Steps.md#add-the-certificate-as-a-key-for-the-todolistdaemonwithcert-application-in-azure-ad)

## <a name="next-steps"></a>Passos seguintes

Introdução ao [carregar ficheiros para a conta](media-services-portal-upload-files.md).
