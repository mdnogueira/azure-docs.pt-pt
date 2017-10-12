---
title: "Utilizar a API de Relatórios do Azure AD com certificados para obter dados | Microsoft Docs"
description: "Explica como utilizar a API de Relatórios do Azure AD com credenciais de certificados para obter dados de diretórios sem intervenção do utilizador."
services: active-directory
documentationcenter: 
author: ramical
writer: v-lorisc
manager: kannar
ms.assetid: 
ms.service: active-directory
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 09/08/2017
ms.author: ramical
ms.openlocfilehash: 38c240ed1608b2e99bde78f3633e722f8e2fa30b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="get-data-using-the-azure-ad-reporting-api-with-certificates"></a>Utilizar a API de Relatórios do Azure AD com certificados para obter dados
Este artigo explica como utilizar a API de Relatórios do Azure AD com credenciais de certificados para obter dados de diretórios sem intervenção do utilizador. 

## <a name="use-the-azure-ad-reporting-api"></a>Utilizar a API de Relatórios do Azure AD 
A API de Relatórios do Azure AD requer que conclua os passos seguintes:
 *  Pré-requisitos da instalação
 *  Definir o certificado na sua aplicação
 *  Obter um token de acesso
 *  Utilizar o token de acesso para chamar a Graph API

Para obter informações sobre o código de origem, veja [Leverage Report API Module (Tirar Partido do Módulo da API de Relatório)](https://github.com/AzureAD/azure-activedirectory-powershell/tree/gh-pages/Modules/AzureADUtils). 

### <a name="install-prerequisites"></a>Pré-requisitos da instalação
Tem de ter instalado o Azure AD PowerShell V2 e o módulo AzureADUtils.

1. Siga as instruções em [Azure Active Directory PowerShell](https://github.com/Azure/azure-docs-powershell-azuread/blob/master/Azure AD Cmdlets/AzureAD/index.md) para transferir e instalar o Azure AD PowerShell V2.
2. Transfira o módulo Utils do Azure AD em [AzureAD/azure-activedirectory-powershell](https://github.com/AzureAD/azure-activedirectory-powershell/blob/gh-pages/Modules/AzureADUtils/AzureADUtils.psm1). 
  Este módulo disponibiliza vários cmdlets de utilitário, incluindo:
   * A versão mais recente da ADAL através de Nuget
   * Tokens de acesso de utilizador, chaves de aplicação e certificados, através da ADAL
   * Graph API que processa resultados paginados

**Para instalar o módulo Utils do Azure AD:**

1. Crie um diretório para guardar o módulo do utilitário (por exemplo, c:\azureAD) e transfira o módulo a partir do GitHub.
2. Abra uma sessão do PowerShell e aceda ao diretório que acabou de criar. 
3. Importe o módulo e instale-o no caminho do módulo do PowerShell, utilizando o cmdlet Install-AzureADUtilsModule. 

A sessão deverá ter um aspeto semelhante a este ecrã:

  ![Windows Powershell](./media/active-directory-report-api-with-certificates/windows-powershell.png)

### <a name="set-the-certificate-in-your-app"></a>Definir o certificado na sua aplicação
1. Se já tiver uma aplicação, obtenha o ID de Objeto da mesma a partir do Portal do Azure. 

  ![Portal do Azure](./media/active-directory-report-api-with-certificates/azure-portal.png)

2. Abra uma sessão do PowerShell e utilize o cmdlet Connect-AzureAD para ligar ao Azure AD.

  ![Portal do Azure](./media/active-directory-report-api-with-certificates/connect-azuaread-cmdlet.png)

3. Utilize o cmdlet New-AzureADApplicationCertificateCredential a partir de AzureADUtils para adicionar uma credencial de certificado ao mesmo. 

>[!Note]
>Tem de indicar o ID de Objeto da aplicação que capturou anteriormente, bem como o objeto do certificado (para o obter, utilize a unidade Cert:).
>


  ![Portal do Azure](./media/active-directory-report-api-with-certificates/add-certificate-credential.png)
  
### <a name="get-an-access-token"></a>Obter um token de acesso

Para obter um token de acesso, utilize o cmdlet Get-AzureADGraphAPIAccessTokenFromCert a partir de AzureADUtils. 

>[!NOTE]
>Tem de utilizar o ID da Aplicação em vez do ID de Objeto que utilizou na secção anterior.
>

 ![Portal do Azure](./media/active-directory-report-api-with-certificates/application-id.png)

### <a name="use-the-access-token-to-call-the-graph-api"></a>Utilizar o token de acesso para chamar a Graph API

Agora, pode criar o script. Segue-se um exemplo que utiliza o cmdlet Invoke-AzureADGraphAPIQuery a partir de AzureADUtils. Este cmdlet processa resultados paginados múltiplos e, em seguida, envia-os para o pipeline do PowerShell. 

 ![Portal do Azure](./media/active-directory-report-api-with-certificates/script-completed.png)

Agora, está pronto para exportar para um CSV e guardar num sistema SIEM. Também pode encapsular o script numa tarefa agendada para obter dados do Azure AD a partir do seu inquilino periodicamente, sem ter de armazenar chaves de aplicação no código de origem. 

## <a name="next-steps"></a>Passos seguintes
[The fundamentals of Azure identity management](https://docs.microsoft.com/en-us/azure/active-directory/fundamentals-identity) (As noções básicas da gestão de identidades do Azure)<br>



