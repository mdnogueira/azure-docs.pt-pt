---
title: "Criar a identidade da aplicação do Azure com o PowerShell | Microsoft Docs"
description: "Descreve como utilizar o Azure PowerShell para criar uma aplicação do Azure Active Directory e um principal de serviço e conceder acesso a recursos através do controlo de acesso baseado em funções. Mostra como a aplicação com uma palavra-passe ou um certificado de autenticação."
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: d2caf121-9fbe-4f00-bf9d-8f3d1f00a6ff
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 08/28/2017
ms.author: tomfitz
ms.openlocfilehash: 57eec4277e584c3c2828e0fe029b9db10428934e
ms.sourcegitcommit: c7215d71e1cdeab731dd923a9b6b6643cee6eb04
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/17/2017
---
# <a name="use-azure-powershell-to-create-a-service-principal-to-access-resources"></a>Utilize o Azure PowerShell para criar um principal de serviço para aceder aos recursos

Quando tiver uma aplicação ou script que precisa de aceder a recursos, pode configurar uma identidade para a aplicação e autenticar a aplicação com as suas próprias credenciais. Esta identidade é conhecida como um principal de serviço. Esta abordagem permite-lhe:

* Atribua permissões para a identidade de aplicação que sejam diferentes a suas própria permissões. Normalmente, estas permissões são restritos exatamente o que a aplicação tem de fazer.
* Utilize um certificado para autenticação ao executar um script automático.

Este tópico mostra como utilizar [Azure PowerShell](/powershell/azure/overview) configurar tudo o que precisa de uma aplicação a ser executado sob as suas próprias credenciais e identidade.

## <a name="required-permissions"></a>Permissões necessárias
Para concluir este tópico, tem de ter permissões suficientes na sua subscrição do Azure e o Azure Active Directory. Especificamente, tem de ser capaz de criar uma aplicação no Azure Active Directory e atribua o principal de serviço a uma função. 

A forma mais fácil de verificar se a sua conta tem permissões adequadas é utilizar o portal. Consulte [verificar a permissão necessária](resource-group-create-service-principal-portal.md#required-permissions).

Agora, avance para uma secção para autenticar com:

* [palavra-passe](#create-service-principal-with-password)
* [certificado autoassinado](#create-service-principal-with-self-signed-certificate)
* [certificado de autoridade de certificação](#create-service-principal-with-certificate-from-certificate-authority)

## <a name="powershell-commands"></a>Comandos do PowerShell

Para configurar um principal de serviço, utilize:

| Comando | Descrição |
| ------- | ----------- | 
| [Novo AzureRmADServicePrincipal](/powershell/module/azurerm.resources/new-azurermadserviceprincipal) | Cria um Azure Active Directory principal de serviço |
| [New-AzureRmRoleAssignment](/powershell/module/azurerm.resources/new-azurermroleassignment) | Atribui a função especificada do RBAC para o principal especificado, no âmbito especificado. |


## <a name="create-service-principal-with-password"></a>Criar o serviço principal com palavra-passe

Para criar um principal de serviço com a função de contribuinte da sua subscrição, utilize: 

```powershell
Login-AzureRmAccount
$password = convertto-securestring {provide-password} -asplaintext -force
$sp = New-AzureRmADServicePrincipal -DisplayName exampleapp -Password $password
Sleep 20
New-AzureRmRoleAssignment -RoleDefinitionName Contributor -ServicePrincipalName $sp.ApplicationId
```

O exemplo permanecerá suspenso durante 20 segundos permitir a algum tempo para o novo serviço principal para propagar ao longo do Azure Active Directory. Se o script não aguarda suficientemente longa, verá um erro a indicar: "PrincipalNotFound: Principal {ID} não existe no diretório."

O script seguinte permite-lhe especificar um âmbito que não seja a subscrição predefinida e repete a atribuição de função se ocorrer um erro:

```powershell
Param (

 # Use to set scope to resource group. If no value is provided, scope is set to subscription.
 [Parameter(Mandatory=$false)]
 [String] $ResourceGroup,

 # Use to set subscription. If no value is provided, default subscription is used. 
 [Parameter(Mandatory=$false)]
 [String] $SubscriptionId,

 [Parameter(Mandatory=$true)]
 [String] $ApplicationDisplayName,

 [Parameter(Mandatory=$true)]
 [String] $Password
)

 Login-AzureRmAccount
 Import-Module AzureRM.Resources

 if ($SubscriptionId -eq "") 
 {
    $SubscriptionId = (Get-AzureRmContext).Subscription.Id
 }
 else
 {
    Set-AzureRmContext -SubscriptionId $SubscriptionId
 }

 if ($ResourceGroup -eq "")
 {
    $Scope = "/subscriptions/" + $SubscriptionId
 }
 else
 {
    $Scope = (Get-AzureRmResourceGroup -Name $ResourceGroup -ErrorAction Stop).ResourceId
 }

 
 # Create Service Principal for the AD app
 $ServicePrincipal = New-AzureRMADServicePrincipal -DisplayName $ApplicationDisplayName -Password $Password
 Get-AzureRmADServicePrincipal -ObjectId $ServicePrincipal.Id 

 $NewRole = $null
 $Retries = 0;
 While ($NewRole -eq $null -and $Retries -le 6)
 {
    # Sleep here for a few seconds to allow the service principal application to become active (should only take a couple of seconds normally)
    Sleep 15
    New-AzureRMRoleAssignment -RoleDefinitionName Contributor -ServicePrincipalName $ServicePrincipal.ApplicationId -Scope $Scope | Write-Verbose -ErrorAction SilentlyContinue
    $NewRole = Get-AzureRMRoleAssignment -ObjectId $ServicePrincipal.Id -ErrorAction SilentlyContinue
    $Retries++;
 }
```

Alguns itens a ter em atenção sobre o script:

* Para conceder o acesso de identidade para a subscrição predefinida, não terá de fornecer os parâmetros do ResourceGroup ou SubscriptionId.
* Especifique o parâmetro de ResourceGroup apenas quando pretende limitar o âmbito de atribuição de função a um grupo de recursos.
*  Neste exemplo, adicione o principal de serviço para a função de contribuinte. Para outras funções, consulte [RBAC: funções incorporadas](../active-directory/role-based-access-built-in-roles.md).
* O script permanecerá suspenso durante 15 segundos permitir a algum tempo para o novo serviço principal para propagar ao longo do Azure Active Directory. Se o script não aguarda suficientemente longa, verá um erro a indicar: "PrincipalNotFound: Principal {ID} não existe no diretório."
* Se precisar de conceder o acesso de principal de serviço para obter mais subscrições ou grupos de recursos, execute o `New-AzureRMRoleAssignment` cmdlet novamente com âmbitos diferentes.


### <a name="provide-credentials-through-powershell"></a>Forneça as credenciais através do PowerShell
Agora, tem de iniciar sessão como a aplicação para efetuar operações. Para o nome de utilizador, utilize o `ApplicationId` que criou para a aplicação. A palavra-passe, utilize especificado ao criar a conta. 

```powershell   
$creds = Get-Credential
Login-AzureRmAccount -Credential $creds -ServicePrincipal -TenantId {tenant-ID}
```

O ID de inquilino não está confidencial, pelo que pode ser incorporar diretamente no seu script. Se precisar de obter o ID do inquilino, utilize:

```powershell
(Get-AzureRmSubscription -SubscriptionName "Contoso Default").TenantId
```

## <a name="create-service-principal-with-self-signed-certificate"></a>Criar o serviço principal com o certificado autoassinado

Para criar um principal de serviço com um certificado autoassinado e a função de contribuinte da sua subscrição, utilize: 

```powershell
Login-AzureRmAccount
$cert = New-SelfSignedCertificate -CertStoreLocation "cert:\CurrentUser\My" -Subject "CN=exampleappScriptCert" -KeySpec KeyExchange
$keyValue = [System.Convert]::ToBase64String($cert.GetRawCertData())

$sp = New-AzureRMADServicePrincipal -DisplayName exampleapp -CertValue $keyValue -EndDate $cert.NotAfter -StartDate $cert.NotBefore
Sleep 20
New-AzureRmRoleAssignment -RoleDefinitionName Contributor -ServicePrincipalName $sp.ApplicationId
```

O exemplo permanecerá suspenso durante 20 segundos permitir a algum tempo para o novo serviço principal para propagar ao longo do Azure Active Directory. Se o script não aguarda suficientemente longa, verá um erro a indicar: "PrincipalNotFound: Principal {ID} não existe no diretório."

O script seguinte permite-lhe especificar um âmbito que não seja a subscrição predefinida e repete a atribuição de função se ocorrer um erro. Tem de ter o Azure PowerShell 2.0 no Windows 10 ou Windows Server 2016.

```powershell
Param (

 # Use to set scope to resource group. If no value is provided, scope is set to subscription.
 [Parameter(Mandatory=$false)]
 [String] $ResourceGroup,

 # Use to set subscription. If no value is provided, default subscription is used. 
 [Parameter(Mandatory=$false)]
 [String] $SubscriptionId,

 [Parameter(Mandatory=$true)]
 [String] $ApplicationDisplayName
 )

 Login-AzureRmAccount
 Import-Module AzureRM.Resources

 if ($SubscriptionId -eq "") 
 {
    $SubscriptionId = (Get-AzureRmContext).Subscription.Id
 }
 else
 {
    Set-AzureRmContext -SubscriptionId $SubscriptionId
 }

 if ($ResourceGroup -eq "")
 {
    $Scope = "/subscriptions/" + $SubscriptionId
 }
 else
 {
    $Scope = (Get-AzureRmResourceGroup -Name $ResourceGroup -ErrorAction Stop).ResourceId
 }

 $cert = New-SelfSignedCertificate -CertStoreLocation "cert:\CurrentUser\My" -Subject "CN=exampleappScriptCert" -KeySpec KeyExchange
 $keyValue = [System.Convert]::ToBase64String($cert.GetRawCertData())

 $ServicePrincipal = New-AzureRMADServicePrincipal -DisplayName $ApplicationDisplayName -CertValue $keyValue -EndDate $cert.NotAfter -StartDate $cert.NotBefore
 Get-AzureRmADServicePrincipal -ObjectId $ServicePrincipal.Id 

 $NewRole = $null
 $Retries = 0;
 While ($NewRole -eq $null -and $Retries -le 6)
 {
    # Sleep here for a few seconds to allow the service principal application to become active (should only take a couple of seconds normally)
    Sleep 15
    New-AzureRMRoleAssignment -RoleDefinitionName Contributor -ServicePrincipalName $ServicePrincipal.ApplicationId -Scope $Scope | Write-Verbose -ErrorAction SilentlyContinue
    $NewRole = Get-AzureRMRoleAssignment -ObjectId $ServicePrincipal.Id -ErrorAction SilentlyContinue
    $Retries++;
 }
```

Alguns itens a ter em atenção sobre o script:

* Para conceder o acesso de identidade para a subscrição predefinida, não terá de fornecer os parâmetros do ResourceGroup ou SubscriptionId.
* Especifique o parâmetro de ResourceGroup apenas quando pretende limitar o âmbito de atribuição de função a um grupo de recursos.
* Neste exemplo, adicione o principal de serviço para a função de contribuinte. Para outras funções, consulte [RBAC: funções incorporadas](../active-directory/role-based-access-built-in-roles.md).
* O script permanecerá suspenso durante 15 segundos permitir a algum tempo para o novo serviço principal para propagar ao longo do Azure Active Directory. Se o script não aguarda suficientemente longa, verá um erro a indicar: "PrincipalNotFound: Principal {ID} não existe no diretório."
* Se precisar de conceder o acesso de principal de serviço para obter mais subscrições ou grupos de recursos, execute o `New-AzureRMRoleAssignment` cmdlet novamente com âmbitos diferentes.

Se lhe **não tem o Windows 10 ou Windows Server 2016 Technical Preview**, tem de transferir o [gerador do certificado autoassinado](https://gallery.technet.microsoft.com/scriptcenter/Self-signed-certificate-5920a7c6/) do Microsoft Script Center. Extrair o respetivo conteúdo e importe-o cmdlet que tem.

```powershell  
# Only run if you could not use New-SelfSignedCertificate
Import-Module -Name c:\ExtractedModule\New-SelfSignedCertificateEx.ps1
```
  
O script, substitua as seguintes duas linhas para gerar o certificado.
  
```powershell
New-SelfSignedCertificateEx  -StoreLocation CurrentUser -StoreName My -Subject "CN=exampleapp" -KeySpec "Exchange" -FriendlyName "exampleapp"
$cert = Get-ChildItem -path Cert:\CurrentUser\my | where {$PSitem.Subject -eq 'CN=exampleapp' }
```

### <a name="provide-certificate-through-automated-powershell-script"></a>Forneça o certificado através do script do PowerShell automatizada
Sempre que iniciar sessão como um principal de serviço, tem de fornecer o ID de inquilino do diretório para a sua aplicação AD. Um inquilino é uma instância do Azure Active Directory. Se tiver apenas uma subscrição, pode utilizar:

```powershell
Param (
 
 [Parameter(Mandatory=$true)]
 [String] $CertSubject,
 
 [Parameter(Mandatory=$true)]
 [String] $ApplicationId,

 [Parameter(Mandatory=$true)]
 [String] $TenantId
 )

 $Thumbprint = (Get-ChildItem cert:\CurrentUser\My\ | Where-Object {$_.Subject -match $CertSubject }).Thumbprint
 Login-AzureRmAccount -ServicePrincipal -CertificateThumbprint $Thumbprint -ApplicationId $ApplicationId -TenantId $TenantId
```

O ID da aplicação e o ID de inquilino não são sensíveis, pelo que pode incorporá-los diretamente no seu script. Se precisar de obter o ID do inquilino, utilize:

```powershell
(Get-AzureRmSubscription -SubscriptionName "Contoso Default").TenantId
```

Se precisar de obter o ID da aplicação, utilize:

```powershell
(Get-AzureRmADApplication -DisplayNameStartWith {display-name}).ApplicationId
```

## <a name="create-service-principal-with-certificate-from-certificate-authority"></a>Criar o serviço principal com o certificado de autoridade de certificação
Para utilizar um certificado emitido por uma autoridade de certificação para criar o serviço principal, utilize o seguinte script:

```powershell
Param (
 [Parameter(Mandatory=$true)]
 [String] $ApplicationDisplayName,

 [Parameter(Mandatory=$true)]
 [String] $SubscriptionId,

 [Parameter(Mandatory=$true)]
 [String] $CertPath,

 [Parameter(Mandatory=$true)]
 [String] $CertPlainPassword
 )

 Login-AzureRmAccount
 Import-Module AzureRM.Resources
 Set-AzureRmContext -SubscriptionId $SubscriptionId
 
 $CertPassword = ConvertTo-SecureString $CertPlainPassword -AsPlainText -Force

 $PFXCert = New-Object -TypeName System.Security.Cryptography.X509Certificates.X509Certificate2 -ArgumentList @($CertPath, $CertPassword)
 $KeyValue = [System.Convert]::ToBase64String($PFXCert.GetRawCertData())

 $ServicePrincipal = New-AzureRMADServicePrincipal -DisplayName $ApplicationDisplayName
 New-AzureRmADSpCredential -ObjectId $ServicePrincipal.Id -CertValue $KeyValue -StartDate $PFXCert.NotBefore -EndDate $PFXCert.NotAfter
 Get-AzureRmADServicePrincipal -ObjectId $ServicePrincipal.Id 

 $NewRole = $null
 $Retries = 0;
 While ($NewRole -eq $null -and $Retries -le 6)
 {
    # Sleep here for a few seconds to allow the service principal application to become active (should only take a couple of seconds normally)
    Sleep 15
    New-AzureRMRoleAssignment -RoleDefinitionName Contributor -ServicePrincipalName $ServicePrincipal.ApplicationId | Write-Verbose -ErrorAction SilentlyContinue
    $NewRole = Get-AzureRMRoleAssignment -ObjectId $ServicePrincipal.Id -ErrorAction SilentlyContinue
    $Retries++;
 }
 
 $NewRole
```

Alguns itens a ter em atenção sobre o script:

* Acesso é confinado à subscrição.
* Neste exemplo, adicione o principal de serviço para a função de contribuinte. Para outras funções, consulte [RBAC: funções incorporadas](../active-directory/role-based-access-built-in-roles.md).
* O script permanecerá suspenso durante 15 segundos permitir a algum tempo para o novo serviço principal para propagar ao longo do Azure Active Directory. Se o script não aguarda suficientemente longa, verá um erro a indicar: "PrincipalNotFound: Principal {ID} não existe no diretório."
* Se precisar de conceder o acesso de principal de serviço para obter mais subscrições ou grupos de recursos, execute o `New-AzureRMRoleAssignment` cmdlet novamente com âmbitos diferentes.

### <a name="provide-certificate-through-automated-powershell-script"></a>Forneça o certificado através do script do PowerShell automatizada
Sempre que iniciar sessão como um principal de serviço, tem de fornecer o ID de inquilino do diretório para a sua aplicação AD. Um inquilino é uma instância do Azure Active Directory.

```powershell
Param (
 
 [Parameter(Mandatory=$true)]
 [String] $CertPath,

 [Parameter(Mandatory=$true)]
 [String] $CertPlainPassword,
 
 [Parameter(Mandatory=$true)]
 [String] $ApplicationId,

 [Parameter(Mandatory=$true)]
 [String] $TenantId
 )

 $CertPassword = ConvertTo-SecureString $CertPlainPassword -AsPlainText -Force
 $PFXCert = New-Object -TypeName System.Security.Cryptography.X509Certificates.X509Certificate2 -ArgumentList @($CertPath, $CertPassword)
 $Thumbprint = $PFXCert.Thumbprint

 Login-AzureRmAccount -ServicePrincipal -CertificateThumbprint $Thumbprint -ApplicationId $ApplicationId -TenantId $TenantId
```

O ID da aplicação e o ID de inquilino não são sensíveis, pelo que pode incorporá-los diretamente no seu script. Se precisar de obter o ID do inquilino, utilize:

```powershell
(Get-AzureRmSubscription -SubscriptionName "Contoso Default").TenantId
```

Se precisar de obter o ID da aplicação, utilize:

```powershell
(Get-AzureRmADApplication -DisplayNameStartWith {display-name}).ApplicationId
```

## <a name="change-credentials"></a>Alterar as credenciais

Para alterar as credenciais para uma aplicação AD, devido a um comprometimento de segurança ou expiração de credencial, utilize o [remover AzureRmADAppCredential](/powershell/resourcemanager/azurerm.resources/v3.3.0/remove-azurermadappcredential) e [New-AzureRmADAppCredential](/powershell/module/azurerm.resources/new-azurermadappcredential) cmdlets.

Para remover todas as credenciais para uma aplicação, utilize:

```powershell
Remove-AzureRmADAppCredential -ApplicationId 8bc80782-a916-47c8-a47e-4d76ed755275 -All
```

Para adicionar uma palavra-passe, utilize:

```powershell
New-AzureRmADAppCredential -ApplicationId 8bc80782-a916-47c8-a47e-4d76ed755275 -Password p@ssword!
```

Para adicionar um valor de certificado, crie um certificado autoassinado, conforme mostrado neste tópico. Em seguida, utilize:

```powershell
New-AzureRmADAppCredential -ApplicationId 8bc80782-a916-47c8-a47e-4d76ed755275 -CertValue $keyValue -EndDate $cert.NotAfter -StartDate $cert.NotBefore
```

## <a name="save-access-token-to-simplify-log-in"></a>Guardar o token de acesso para simplificar o início de sessão
Para evitar a fornecer as credenciais de principal de serviço, sempre que tem de iniciar sessão, pode guardar o token de acesso.

Para utilizar o token de acesso atual numa sessão posterior, guarde o perfil.
   
```powershell
Save-AzureRmProfile -Path c:\Users\exampleuser\profile\exampleSP.json
```
   
Abra o perfil e examine o respetivo conteúdo. Tenha em atenção que contém um token de acesso. Em vez de manualmente iniciar sessão novamente, carregar o perfil.
   
```powershell
Select-AzureRmProfile -Path c:\Users\exampleuser\profile\exampleSP.json
```

> [!NOTE]
> O token de acesso expira, de modo a utilizar um perfil guardado só funciona para, desde que o token é válido.
>  

Em alternativa, pode invocar as operações REST do PowerShell para iniciar sessão. Da resposta de autenticação, pode obter o token de acesso para utilização com outras operações. Para obter um exemplo de obter o token de acesso ao invocar as operações REST, consulte [gerar um Token de acesso](resource-manager-rest-api.md#generating-an-access-token).

## <a name="debug"></a>Depurar

Pode encontrar os seguintes erros ao criar um principal de serviço:

* **"Authentication_Unauthorized"** ou **"nenhuma subscrição encontrado no contexto."** -Vir este erro quando a sua conta não tem o [as permissões necessárias](#required-permissions) no Azure Active Directory para registar uma aplicação. Normalmente, este erro ocorrer quando apenas os utilizadores de administração do seu Azure Active Directory podem registar aplicações e a sua conta não é um administrador. Peça ao seu administrador para lhe atribuir ou para uma função de administrador ou permitir que os utilizadores registar as aplicações.

* A conta **"não tem autorização para executar a ação 'Microsoft.Authorization/roleAssignments/write' no âmbito"subscrições / {guid}"."**  -Ver este erro quando a sua conta não tem permissões suficientes para atribuir uma função para uma identidade. Peça ao seu administrador de subscrição para adicioná-lo à função de administrador de acesso de utilizador.

## <a name="sample-applications"></a>Exemplos de aplicações
Para informações sobre como iniciar sessão como a aplicação através de plataformas diferentes, consulte:

* [.NET](/dotnet/azure/dotnet-sdk-azure-authenticate?view=azure-dotnet)
* [Java](/java/azure/java-sdk-azure-authenticate)
* [Node.js](/nodejs/azure/node-sdk-azure-get-started?view=azure-node-2.0.0)
* [Python](/python/azure/python-sdk-azure-authenticate?view=azure-python)
* [Ruby](https://azure.microsoft.com/documentation/samples/resource-manager-ruby-resources-and-groups/)

## <a name="next-steps"></a>Passos seguintes
* Para obter passos detalhados sobre como integrar a uma aplicação no Azure para gerir recursos, consulte [guia para programadores para autorização com a API do Azure Resource Manager](resource-manager-api-authentication.md).
* Para obter uma explicação mais detalhada de principais de serviço e de aplicações, consulte [objectos da aplicação e objetos de principais de serviço](../active-directory/active-directory-application-objects.md). 
* Para obter mais informações sobre a autenticação do Azure Active Directory, consulte [cenários de autenticação para o Azure AD](../active-directory/active-directory-authentication-scenarios.md).
* Para obter uma lista de ações disponíveis que pode ser concedeu ou negou aos utilizadores, consulte [operações de fornecedor de recursos do Azure Resource Manager](../active-directory/role-based-access-control-resource-provider-operations.md).

