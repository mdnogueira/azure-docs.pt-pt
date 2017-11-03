---
title: "Criar um Principal de serviço para a pilha do Azure | Microsoft Docs"
description: "Descreve como criar um novo principal de serviço que pode ser utilizado com o controlo de acesso baseado em funções no Gestor de recursos do Azure para gerir o acesso aos recursos."
services: azure-resource-manager
documentationcenter: na
author: heathl17
manager: byronr
ms.assetid: 7068617b-ac5e-47b3-a1de-a18c918297b6
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/25/2017
ms.author: helaw
ms.openlocfilehash: 058b01a37e2858801895fd22cf73dd6bd342ca04
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="provide-applications-access-to-azure-stack"></a>Fornecer acesso de aplicações com a pilha do Azure

*Aplica-se a: Azure pilha integrado sistemas e Kit de desenvolvimento de pilha do Azure*

Quando uma aplicação precisa de aceder para implementar ou configurar recursos através do Azure Resource Manager na pilha do Azure, criar um serviço principal, que é uma credencial para a sua aplicação.  Em seguida, pode delegar as permissões necessárias para esse principal de serviço.  

Por exemplo, pode ter uma ferramenta de gestão de configuração que utiliza o Azure Resource Manager para o inventário de recursos do Azure.  Neste cenário, pode criar um principal de serviço, conceder a função de leitor para esse principal de serviço e limitar a ferramenta de gestão de configuração para acesso só de leitura. 

Principais de serviço são preferível para executar a aplicação com as suas próprias credenciais porque:

* Pode atribuir permissões ao serviço principal que sejam diferentes a suas própria conta as permissões. Normalmente, estas permissões são restritos exatamente o que a aplicação tem de fazer.
* Não é necessário que alterar credenciais da aplicação, se alterar as suas responsabilidades.
* Pode utilizar um certificado para automatizar a autenticação ao executar um script automático.  

## <a name="getting-started"></a>Introdução

Dependendo de como tiver implementado pilha do Azure, que comece por criar um serviço principal.  Este documento orienta-o ao longo da criação de um principal de serviço para ambos [Azure Active Directory (Azure AD)](azure-stack-create-service-principals.md#create-service-principal-for-azure-ad) e [Active Directory Federação Services(AD FS)](azure-stack-create-service-principals.md#create-service-principal-for-ad-fs).  Depois de criar o principal de serviço, um conjunto de passos comuns para AD FS e o Azure Active Directory são utilizados para [delegar permissões](azure-stack-create-service-principals.md#assign-role-to-service-principal) à função.     

## <a name="create-service-principal-for-azure-ad"></a>Criar o serviço principal para o Azure AD

Se implementou a pilha do Azure com o Azure AD como o arquivo de identidade, pode criar principais de serviço, tal como, fazê-lo para o Azure.  Esta secção mostra como efetuar os passos através do portal.  Verifique se tem o [necessárias permissões do Azure AD](../../azure-resource-manager/resource-group-create-service-principal-portal.md#required-permissions) antes do início.

### <a name="create-service-principal"></a>Criar um principal de serviço
Nesta secção, vai criar uma aplicação (principal de serviço) no Azure AD que irá representar a sua aplicação.

1. Inicie sessão na sua conta do Azure através de [portal do Azure](https://portal.azure.com).
2. Selecione **do Azure Active Directory** > **registos de aplicação** > **adicionar**   
3. Forneça um nome e o URL para a aplicação. Selecione **aplicação Web / API** ou **nativo** para o tipo de aplicação que pretende criar. Depois de definir os valores, selecione **criar**.

Criou um principal de serviço para a sua aplicação.

### <a name="get-credentials"></a>Obter credenciais
Quando programaticamente iniciar sessão, utilize o ID para a sua aplicação e uma chave de autenticação. Para obter esses valores, utilize os seguintes passos:

1. De **registos de aplicação** no Active Directory, selecione a aplicação.

2. Copiar o **ID da aplicação** e armazená-las no código da aplicação. As aplicações no [aplicações de exemplo](#sample-applications) secção fazer referência a este valor como o id de cliente.

     ![id de cliente](./media/azure-stack-create-service-principal/image12.png)
3. Para gerar uma chave de autenticação, selecione **chaves**.

4. Forneça uma descrição da chave e um período de tempo para a chave. Quando terminar, selecione **guardar**.

Depois de guardar a chave, é apresentado o valor da chave. Copie este valor, porque não é possível obter a chave mais tarde. Forneça o valor de chave com o ID de aplicação para iniciar sessão como a aplicação. Armazene o valor da chave em que a aplicação o pode obtê-lo.

![guardar a chave](./media/azure-stack-create-service-principal/image15.png)


Depois de concluído, avance para [atribuir uma função a sua aplicação](azure-stack-create-service-principals.md#assign-role-to-service-principal).

## <a name="create-service-principal-for-ad-fs"></a>Criar o serviço principal para o AD FS
Se tiver implementado a pilha do Azure com o AD FS, pode utilizar o PowerShell para criar um principal de serviço, atribuir uma função de acesso e iniciar sessão a partir do PowerShell com essa identidade.

### <a name="before-you-begin"></a>Antes de começar

[Transferir as ferramentas necessárias para trabalhar com a pilha do Azure para o computador local.](azure-stack-powershell-download.md)

### <a name="import-the-identity-powershell-module"></a>Importe o módulo do PowerShell de identidade
Depois de transferir as ferramentas, navegue para a pasta transferida e importar o módulo do PowerShell de identidade, utilizando o seguinte comando:

```PowerShell
Import-Module .\Identity\AzureStack.Identity.psm1
```

Quando importar o módulo, poderá receber um erro que indica "AzureStack.Connect.psm1 não está assinado digitalmente. O script não executará no sistema". Para resolver este problema, pode definir a política de execução para permitir a execução do script com o seguinte comando numa sessão do PowerShell elevada:

```PowerShell
Set-ExecutionPolicy Unrestricted
```

### <a name="create-the-service-principal"></a>Criar o principal de serviço
Pode criar um Principal de serviço, executando o seguinte comando, certificando-se ao atualizar o *DisplayName* parâmetro:
```powershell
$servicePrincipal = New-AzSADGraphServicePrincipal `
 -DisplayName "<YourServicePrincipalName>" `
 -AdminCredential $(Get-Credential) `
 -AdfsMachineName "AZS-ADFS01" `
 -Verbose
```
### <a name="assign-a-role"></a>Atribuir uma função
Assim que for criado o Principal de serviço, deve [atribuí-la a uma função](azure-stack-create-service-principals.md#assign-role-to-service-principal)

### <a name="sign-in-through-powershell"></a>Inicie sessão através do PowerShell
Assim que tiver atribuída uma função, pode iniciar sessão pilha do Azure utilizando o principal de serviço com o seguinte comando:

```powershell
Add-AzureRmAccount -EnvironmentName "<AzureStackEnvironmentName>" `
 -ServicePrincipal `
 -CertificateThumbprint $servicePrincipal.Thumbprint `
 -ApplicationId $servicePrincipal.ApplicationId ` 
 -TenantId $directoryTenantId
```

## <a name="assign-role-to-service-principal"></a>Atribuir função principal de serviço
Para aceder a recursos na sua subscrição, tem de atribuir a aplicação a uma função. Decida qual a função representa as permissões corretas para a aplicação. Para saber mais sobre as funções disponíveis, consulte [RBAC: funções incorporadas](../../active-directory/role-based-access-built-in-roles.md).

Pode definir o âmbito ao nível da subscrição, do grupo de recursos ou do recurso. As permissões são herdadas a níveis inferiores de âmbito. Por exemplo, adicionar uma aplicação para a função de leitor para um grupo de recursos significa podem ler o grupo de recursos e todos os recursos que nele contidos.

1. No portal do Azure pilha, navegue para o nível de âmbito que pretende atribuir a aplicação. Por exemplo, para atribuir uma função no âmbito de subscrição, selecione **subscrições**. Em vez disso, pode selecionar um grupo de recursos ou um recurso.

2. Selecione a subscrição específica (grupo de recursos ou recursos) para atribuir a aplicação.

     ![Selecione a subscrição de atribuição](./media/azure-stack-create-service-principal/image16.png)

3. Selecione **(IAM) do controlo de acesso**.

     ![Selecione o acesso](./media/azure-stack-create-service-principal/image17.png)

4. Selecione **Adicionar**.

5. Selecione a função que pretende atribuir à aplicação.

6. Procure a aplicação e selecione-o.

7. Selecione **OK** para concluir a atribuição de função. Verá a aplicação na lista de utilizadores atribuídos a uma função para esse âmbito.

Agora que já criou um principal de serviço e atribuída uma função, pode começar a utilizar esta na sua aplicação para aceder a recursos de pilha do Azure.  

## <a name="next-steps"></a>Passos seguintes

[Gerir permissões de utilizador](azure-stack-manage-permissions.md)