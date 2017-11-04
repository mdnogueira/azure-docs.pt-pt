---
title: "Permitir que as aplicações obter segredos do Cofre de chaves de pilha do Azure | Microsoft Docs"
description: "Utilizar uma aplicação de exemplo para trabalhar com o Cofre de chaves de pilha do Azure"
services: azure-stack
documentationcenter: 
author: SnehaGunda
manager: byronr
editor: 
ms.assetid: 3748b719-e269-4b48-8d7d-d75a84b0e1e5
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 08/26/2017
ms.author: sngun
ms.openlocfilehash: 7cfb78cc5219d4adab5ceddc9d7eb8d1fc71b678
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="sample-application-that-uses-keys-and-secrets-stored-in-a-key-vault"></a>Exemplo de aplicação que utiliza as chaves e segredos armazenados no Cofre de chaves

Neste artigo, vamos mostrar-lhe como executar uma aplicação de exemplo (HelloKeyVault) que obtém as chaves e segredos de um cofre de chaves na pilha do Azure.

## <a name="prerequisites"></a>Pré-requisitos 

Execute os seguintes pré-requisitos a partir de [Development Kit](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-remote-desktop), ou baseados em Windows externo cliente se estiver [ligado através de VPN](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-vpn):

* Instalar [módulos do Azure pilha compatível com o Azure PowerShell](azure-stack-powershell-install.md).  
* Transferir o [ferramentas necessárias para trabalhar com a pilha de Azure](azure-stack-powershell-download.md). 

## <a name="create-and-get-the-key-vault-and-application-settings"></a>Criar e obter o Cofre de chaves e as definições da aplicação

Em primeiro lugar, deve criar um cofre de chaves na pilha do Azure e registar uma aplicação no Azure Active Directory (Azure AD). Pode criar e registar os cofres de chaves utilizando o portal do Azure ou do PowerShell. Este artigo mostra-lhe a forma de PowerShell para efetuar as tarefas. Por predefinição, este script do PowerShell cria uma nova aplicação no Active Directory. No entanto, também pode utilizar uma das suas aplicações existentes. Certifique-se de fornecer um valor para o `aadTenantName` e `applicationPassword` variáveis. Se não especificar um valor para o `applicationPassword` variável, este script gera uma palavra-passe aleatória. 

```powershell
$vaultName           = 'myVault'
$resourceGroupName   = 'myResourceGroup'
$applicationName     = 'myApp'
$location            = 'local' 

# Password for the application. If not specified, this script will generate a random password during app creation.
$applicationPassword = '' 
                         
# Function to generate a random password for the application.
Function GenerateSymmetricKey()
{
    $key = New-Object byte[](32)
    $rng = [System.Security.Cryptography.RNGCryptoServiceProvider]::Create()
    $rng.GetBytes($key)
    return [System.Convert]::ToBase64String($key)
}

Write-Host 'Please log into your Azure Stack user environment' -foregroundcolor Green

$tenantARM = "https://management.local.azurestack.external"
$aadTenantName = "PLEASE FILL THIS IN WITH YOUR AAD TENANT NAME. FOR EXAMPLE: myazurestack.onmicrosoft.com"

# Configure the Azure Stack operator’s PowerShell environment.
Add-AzureRMEnvironment `
  -Name "AzureStackUser" `
  -ArmEndpoint $tenantARM

Set-AzureRmEnvironment `
  -Name "AzureStackAdmin" `
  -GraphAudience "https://graph.windows.net/"

$TenantID = Get-AzsDirectoryTenantId `
  -AADTenantName $aadTenantName `
  -EnvironmentName AzureStackUser

# Sign in to the user portal.
Login-AzureRmAccount `
  -EnvironmentName "AzureStackUser" `
  -TenantId $TenantID `
  
$now = [System.DateTime]::Now
$oneYearFromNow = $now.AddYears(1)

$applicationPassword = GenerateSymmetricKey
    
# Create a new Azure AD application.
$identifierUri = [string]::Format("http://localhost:8080/{0}",[Guid]::NewGuid().ToString("N"))
$homePage = "http://contoso.com"

Write-Host "Creating a new AAD Application"
$ADApp = New-AzureRmADApplication `
  -DisplayName $applicationName `
  -HomePage $homePage `
  -IdentifierUris $identifierUri `
  -StartDate $now `
  -EndDate $oneYearFromNow `
  -Password $applicationPassword

Write-Host "Creating a new AAD service principal"
$servicePrincipal = New-AzureRmADServicePrincipal `
  -ApplicationId $ADApp.ApplicationId

# Create a new resource group and a key vault within that resource group.
New-AzureRmResourceGroup `
  -Name $resourceGroupName `
  -Location $location   

Write-Host "Creating vault $vaultName"
$vault = New-AzureRmKeyVault -VaultName $vaultName `
  -ResourceGroupName $resourceGroupName `
  -Sku standard `
  -Location $location

# Specify full privileges to the vault for the application.
Write-Host "Setting access policy"
Set-AzureRmKeyVaultAccessPolicy -VaultName $vaultName `
  -ObjectId $servicePrincipal.Id `
  -PermissionsToKeys all `
  -PermissionsToSecrets all

Write-Host "Paste the following settings into the app.config file for the HelloKeyVault project:"
'<add key="VaultUrl" value="' + $vault.VaultUri + '"/>'
'<add key="AuthClientId" value="' + $servicePrincipal.ApplicationId + '"/>'
'<add key="AuthClientSecret" value="' + $applicationPassword + '"/>'
Write-Host

``` 

A seguinte captura de ecrã mostra a saída do script anterior:

![Configuração de aplicação](media/azure-stack-kv-sample-app/settingsoutput.png)

Anote o **VaultUrl**, **AuthClientId**, e **AuthClientSecret** valores devolvidos pelo script anterior. Utilize estes valores para executar a aplicação de HelloKeyVault.

## <a name="download-and-run-the-sample-application"></a>Transferir e executar a aplicação de exemplo

Transferir o exemplo do Cofre de chaves do Azure [amostras de cliente do Cofre de chaves](https://www.microsoft.com/en-us/download/details.aspx?id=45343) página. Extraia os conteúdos do ficheiro. zip para a estação de trabalho de desenvolvimento. Existem dois exemplos dentro da pasta de amostras. Utilizamos o exemplo de HellpKeyVault neste tópico. Navegue para o **Microsoft.Azure.KeyVault.Samples** > **amostras** > **HelloKeyVault** pasta e abra a aplicação de HelloKeyVault no Visual Studio. 

Abra o ficheiro HelloKeyVault\App.config e substitua os valores do <appSettings> elemento com o **VaultUrl**, **AuthClientId**, e **AuthClientSecret** valores devolvido pelo script anterior. Tenha em atenção que, por predefinição, o App. config contém um marcador de posição para *AuthCertThumbprint*, mas utilizar *AuthClientSecret* em vez disso. Depois de as substituir as definições, reconstruir a solução e iniciar a aplicação.

![Definições de aplicação](media/azure-stack-kv-sample-app/appconfig.png)
 
A aplicação inicia sessão com o Azure AD e, em seguida, utiliza esse token para autenticar para o Cofre de chaves na pilha do Azure. A aplicação executa operações como criar, encriptar, moldar e eliminar nas chaves e segredos do Cofre de chaves. Pode também passar parâmetros específicos, tais como *encriptar* e *desencriptar* à aplicação, que certifica-se de que a aplicação executa apenas essas operações contra o cofre. 


## <a name="next-steps"></a>Passos seguintes
[Implementar uma VM com uma palavra-passe do Cofre de Chaves](azure-stack-kv-deploy-vm-with-secret.md)

[Implementar uma VM com um certificado do Cofre de chaves](azure-stack-kv-push-secret-into-vm.md)



