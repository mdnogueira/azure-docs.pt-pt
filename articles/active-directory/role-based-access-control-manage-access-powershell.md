---
title: "Gerir o controlo de acesso baseado em funções (RBAC) com o Azure PowerShell | Microsoft Docs"
description: "Como gerir o RBAC com o Azure PowerShell, incluindo listagem funções, atribuir funções e eliminar atribuições de funções."
services: active-directory
documentationcenter: 
author: andredm7
manager: femila
ms.assetid: 9e225dba-9044-4b13-b573-2f30d77925a9
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/12/2017
ms.author: andredm
ms.reviewer: rqureshi
ms.openlocfilehash: d7b11df21650b5cb27f9c3dd8306f8d12664185e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="manage-role-based-access-control-with-azure-powershell"></a>Manage Role-Based Access Control with Azure PowerShell (Gerir o Controlo de Acesso Baseado em Funções com o Azure PoweShell)
> [!div class="op_single_selector"]
> * [PowerShell](role-based-access-control-manage-access-powershell.md)
> * [CLI do Azure](role-based-access-control-manage-access-azure-cli.md)
> * [API REST](role-based-access-control-manage-access-rest.md)

Pode utilizar o controlo de acesso baseado em funções (RBAC) no portal do Azure e a API de gestão de recursos do Azure para gerir o acesso à sua subscrição a um nível detalhado. Com esta funcionalidade, pode conceder acesso para utilizadores, grupos ou principais de serviço do Active Directory através da atribuição de algumas funções aos mesmos num determinado âmbito.

Antes de poder utilizar o PowerShell para gerir o RBAC, terá dos seguintes pré-requisitos:

* O Azure PowerShell versão 0.8.8 ou posterior. Para instalar a versão mais recente e associá-lo à sua subscrição do Azure, consulte [como instalar e configurar o Azure PowerShell](/powershell/azure/overview).
* Cmdlets do Gestor de recursos do Azure. Instalar o [cmdlets do Azure Resource Manager](/powershell/azure/overview) no PowerShell.

## <a name="list-roles"></a>Lista de funções
### <a name="list-all-available-roles"></a>Lista todas as funções disponíveis
Para funções RBAC de lista que estão disponíveis para atribuição e inspecionar as operações para que possam conceder acesso, utilizam `Get-AzureRmRoleDefinition`.

```
Get-AzureRmRoleDefinition | FT Name, Description
```

![Captura de ecrã do RBAC PowerShell-Get AzureRmRoleDefinition-](./media/role-based-access-control-manage-access-powershell/1-get-azure-rm-role-definition1.png)

### <a name="list-actions-of-a-role"></a>Lista de ações de uma função
Para listar as ações para uma função específica, utilize `Get-AzureRmRoleDefinition <role name>`.

```
Get-AzureRmRoleDefinition Contributor | FL Actions, NotActions

(Get-AzureRmRoleDefinition "Virtual Machine Contributor").Actions
```

![Captura de ecrã do RBAC PowerShell-Get AzureRmRoleDefinition para uma função específica-](./media/role-based-access-control-manage-access-powershell/1-get-azure-rm-role-definition2.png)

## <a name="see-who-has-access"></a>Ver quem tem acesso
Para listar atribuições de acesso do RBAC, utilize `Get-AzureRmRoleAssignment`.

### <a name="list-role-assignments-at-a-specific-scope"></a>Listar atribuições de função a um âmbito específico
Pode ver todas as atribuições de acesso para uma subscrição especificada, o grupo de recursos ou o recurso. Por exemplo, para ver as todas as Active Directory atribuições de para um grupo de recursos, utilize `Get-AzureRmRoleAssignment -ResourceGroupName <resource group name>`.

```
Get-AzureRmRoleAssignment -ResourceGroupName Pharma-Sales-ProjectForcast | FL DisplayName, RoleDefinitionName, Scope
```

![Captura de ecrã do RBAC PowerShell-Get AzureRmRoleAssignment para um grupo de recursos-](./media/role-based-access-control-manage-access-powershell/4-get-azure-rm-role-assignment1.png)

### <a name="list-roles-assigned-to-a-user"></a>Funções de lista atribuídas a um utilizador
Para listar todas as funções que estão atribuídas a um utilizador especificado e as funções que estão atribuídas a grupos a que o utilizador pertence, utilize `Get-AzureRmRoleAssignment -SignInName <User email> -ExpandPrincipalGroups`.

```
Get-AzureRmRoleAssignment -SignInName sameert@aaddemo.com | FL DisplayName, RoleDefinitionName, Scope

Get-AzureRmRoleAssignment -SignInName sameert@aaddemo.com -ExpandPrincipalGroups | FL DisplayName, RoleDefinitionName, Scope
```

![Captura de ecrã do RBAC PowerShell-Get AzureRmRoleAssignment para um utilizador-](./media/role-based-access-control-manage-access-powershell/4-get-azure-rm-role-assignment2.png)

### <a name="list-classic-service-administrator-and-coadmin-role-assignments"></a>Listar atribuições de funções de administrador e coadmin do serviço clássico
Para listar atribuições de acesso para o administrador de subscrição clássica e coadministrators, utilize:

    Get-AzureRmRoleAssignment -IncludeClassicAdministrators

## <a name="grant-access"></a>Conceder acesso
### <a name="search-for-object-ids"></a>Pesquisa de IDs de objeto
Para atribuir uma função, tem de identificar o objeto (utilizador, grupo ou aplicação) e o âmbito.

Se não souber o ID de subscrição, pode encontrá-lo no **subscrições** painel no portal do Azure. Para saber como consultar o ID de subscrição, consulte [Get-AzureSubscription](/powershell/module/azure/get-azuresubscription?view=azuresmps-3.7.0) no MSDN.

Para obter o ID de objeto para um grupo do Azure AD, utilize:

    Get-AzureRmADGroup -SearchString <group name in quotes>

Para obter o ID de objeto para uma aplicação ou principal de serviço do Azure AD, utilize:

    Get-AzureRmADServicePrincipal -SearchString <service name in quotes>

### <a name="assign-a-role-to-an-application-at-the-subscription-scope"></a>Atribuir uma função a uma aplicação no âmbito de subscrição
Para conceder acesso a uma aplicação no âmbito de subscrição, utilize:

    New-AzureRmRoleAssignment -ObjectId <application id> -RoleDefinitionName <role name> -Scope <subscription id>

![Captura de ecrã do RBAC PowerShell - novo AzureRmRoleAssignment-](./media/role-based-access-control-manage-access-powershell/2-new-azure-rm-role-assignment2.png)

### <a name="assign-a-role-to-a-user-at-the-resource-group-scope"></a>Atribuir uma função a um utilizador no âmbito do grupo de recursos
Para conceder acesso a um utilizador no âmbito do grupo de recursos, utilize:

    New-AzureRmRoleAssignment -SignInName <email of user> -RoleDefinitionName <role name in quotes> -ResourceGroupName <resource group name>

![Captura de ecrã do RBAC PowerShell - novo AzureRmRoleAssignment-](./media/role-based-access-control-manage-access-powershell/2-new-azure-rm-role-assignment3.png)

### <a name="assign-a-role-to-a-group-at-the-resource-scope"></a>Atribuir uma função a um grupo no âmbito do recurso
Para conceder acesso a um grupo no âmbito de recursos, utilize:

    New-AzureRmRoleAssignment -ObjectId <object id> -RoleDefinitionName <role name in quotes> -ResourceName <resource name> -ResourceType <resource type> -ParentResource <parent resource> -ResourceGroupName <resource group name>

![Captura de ecrã do RBAC PowerShell - novo AzureRmRoleAssignment-](./media/role-based-access-control-manage-access-powershell/2-new-azure-rm-role-assignment4.png)

## <a name="remove-access"></a>Remover o acesso
Para remover o acesso dos utilizadores, grupos e aplicações, utilize:

    Remove-AzureRmRoleAssignment -ObjectId <object id> -RoleDefinitionName <role name> -Scope <scope such as subscription id>

![Captura de ecrã do RBAC PowerShell-Remove AzureRmRoleAssignment-](./media/role-based-access-control-manage-access-powershell/3-remove-azure-rm-role-assignment.png)

## <a name="create-a-custom-role"></a>Criar uma função personalizada
Para criar uma função personalizada, utilize o ```New-AzureRmRoleDefinition``` comando. Existem dois métodos para structuring a função, utilizar PSRoleDefinitionObject ou um modelo JSON. 

## <a name="get-actions-for-a-resource-provider"></a>Obter as ações de um fornecedor de recursos
Quando estiver a criar funções personalizadas a partir do zero, é importante saber de todas as operações de possíveis dos fornecedores de recursos.
Utilize o ```Get-AzureRMProviderOperation``` comando para obter estas informações.
Por exemplo, se quiser verificar todas as operações disponíveis para a máquina virtual utilizam este comando:

```
Get-AzureRMProviderOperation "Microsoft.Compute/virtualMachines/*" | FT OperationName, Operation , Description -AutoSize
```

### <a name="create-role-with-psroledefinitionobject"></a>Criar função com PSRoleDefinitionObject
Quando utilizar o PowerShell para criar uma função personalizada, pode começar do zero ou utilize um do [funções incorporadas](role-based-access-built-in-roles.md) como um ponto de partida. O exemplo nesta secção começa com uma função incorporada e, em seguida, customizes-lo com mais privilégios. Editar os atributos para adicionar o *ações*, *notActions*, ou *âmbitos* que pretende e, em seguida, guarde as alterações como uma nova função.

O exemplo seguinte começa com o *contribuinte de Máquina Virtual* função e utiliza que criar uma função personalizada chamado *operador de Máquina Virtual*. A nova função concede acesso a todas as operações de leitura de *Microsoft. Compute*, *Microsoft*, e *Network* recursos fornecedores e concede acesso a iniciar , reiniciar e monitorizar máquinas virtuais. A função personalizada pode ser utilizada em duas subscrições.

```
$role = Get-AzureRmRoleDefinition "Virtual Machine Contributor"
$role.Id = $null
$role.Name = "Virtual Machine Operator"
$role.Description = "Can monitor and restart virtual machines."
$role.Actions.Clear()
$role.Actions.Add("Microsoft.Storage/*/read")
$role.Actions.Add("Microsoft.Network/*/read")
$role.Actions.Add("Microsoft.Compute/*/read")
$role.Actions.Add("Microsoft.Compute/virtualMachines/start/action")
$role.Actions.Add("Microsoft.Compute/virtualMachines/restart/action")
$role.Actions.Add("Microsoft.Authorization/*/read")
$role.Actions.Add("Microsoft.Resources/subscriptions/resourceGroups/read")
$role.Actions.Add("Microsoft.Insights/alertRules/*")
$role.Actions.Add("Microsoft.Support/*")
$role.AssignableScopes.Clear()
$role.AssignableScopes.Add("/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e")
$role.AssignableScopes.Add("/subscriptions/e91d47c4-76f3-4271-a796-21b4ecfe3624")
New-AzureRmRoleDefinition -Role $role
```

![Captura de ecrã do RBAC PowerShell-Get AzureRmRoleDefinition-](./media/role-based-access-control-manage-access-powershell/2-new-azurermroledefinition.png)

### <a name="create-role-with-json-template"></a>Criar função com o modelo JSON
Um modelo JSON pode ser utilizado como a definição de origem para a função personalizada. O exemplo seguinte cria uma função personalizada que permite acesso de leitura aos recursos de armazenamento e computação, acesso ao suportar, e adiciona a função para duas subscrições. Criar um novo ficheiro `C:\CustomRoles\customrole1.json` com o exemplo seguinte. O Id deve ser definido como `null` na criação da função inicial como um novo ID for gerado automaticamente. 

```
{
  "Name": "Custom Role 1",
  "Id": null,
  "IsCustom": true,
  "Description": "Allows for read access to Azure storage and compute resources and access to support",
  "Actions": [
    "Microsoft.Compute/*/read",
    "Microsoft.Storage/*/read",
    "Microsoft.Support/*"
  ],
  "NotActions": [
  ],
  "AssignableScopes": [
    "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e",
    "/subscriptions/e91d47c4-76f3-4271-a796-21b4ecfe3624"
  ]
}
```
Para adicionar a função para as subscrições, execute o seguinte comando do PowerShell:
```
New-AzureRmRoleDefinition -InputFile "C:\CustomRoles\customrole1.json"
```

## <a name="modify-a-custom-role"></a>Modificar uma função personalizada
Semelhante ao criar uma função personalizada, pode modificar uma função personalizada existente utilizando o PSRoleDefinitionObject ou um modelo JSON.

### <a name="modify-role-with-psroledefinitionobject"></a>Modifique a função PSRoleDefinitionObject
Para modificar uma função personalizada, primeiro, utilize o `Get-AzureRmRoleDefinition` comando para obter a definição de função. Segundo, efetue as alterações pretendidas para a definição de função. Por último, utilize o `Set-AzureRmRoleDefinition` comando para guardar a definição de função modificadas.

O exemplo seguinte adiciona o `Microsoft.Insights/diagnosticSettings/*` operação para o *operador de Máquina Virtual* função personalizada.

```
$role = Get-AzureRmRoleDefinition "Virtual Machine Operator"
$role.Actions.Add("Microsoft.Insights/diagnosticSettings/*")
Set-AzureRmRoleDefinition -Role $role
```

![Captura de ecrã do RBAC PowerShell - Set-AzureRmRoleDefinition-](./media/role-based-access-control-manage-access-powershell/3-set-azurermroledefinition-1.png)

O exemplo seguinte adiciona uma subscrição do Azure para os âmbitos atribuíveis do *operador de Máquina Virtual* função personalizada.

```
Get-AzureRmSubscription - SubscriptionName Production3

$role = Get-AzureRmRoleDefinition "Virtual Machine Operator"
$role.AssignableScopes.Add("/subscriptions/34370e90-ac4a-4bf9-821f-85eeedead1a2")
Set-AzureRmRoleDefinition -Role $role
```

![Captura de ecrã do RBAC PowerShell - Set-AzureRmRoleDefinition-](./media/role-based-access-control-manage-access-powershell/3-set-azurermroledefinition-2.png)

### <a name="modify-role-with-json-template"></a>Modifique a função com o modelo JSON
Utilizando o modelo JSON anterior, pode facilmente modificar uma função personalizada existente para adicionar ou remover as ações. Atualizar o modelo JSON e adicionar a ação de leitura para funcionamento em rede, conforme mostrado no exemplo seguinte. As definições apresentadas no modelo cumulativamente e não são aplicadas a uma definição existente, que significa que a função aparece exatamente como especificar no modelo. Terá também de atualizar o campo Id com o ID da função. Se não tem a certeza que este valor é, pode utilizar o `Get-AzureRmRoleDefinition` cmdlet para obter estas informações.

```
{
  "Name": "Custom Role 1",
  "Id": "acce7ded-2559-449d-bcd5-e9604e50bad1",
  "IsCustom": true,
  "Description": "Allows for read access to Azure storage and compute resources and access to support",
  "Actions": [
    "Microsoft.Compute/*/read",
    "Microsoft.Storage/*/read",
    "Microsoft.Network/*/read",
    "Microsoft.Support/*"
  ],
  "NotActions": [
  ],
  "AssignableScopes": [
    "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e",
    "/subscriptions/e91d47c4-76f3-4271-a796-21b4ecfe3624"
  ]
}
```

Para atualizar a função existente, execute o seguinte comando do PowerShell:
```
Set-AzureRmRoleDefinition -InputFile "C:\CustomRoles\customrole1.json"
```

## <a name="delete-a-custom-role"></a>Eliminar uma função personalizada
Para eliminar uma função personalizada, utilize o `Remove-AzureRmRoleDefinition` comando.

O exemplo a seguir remove o *operador de Máquina Virtual* função personalizada.

```
Get-AzureRmRoleDefinition "Virtual Machine Operator"

Get-AzureRmRoleDefinition "Virtual Machine Operator" | Remove-AzureRmRoleDefinition
```

![Captura de ecrã do RBAC PowerShell - Remove-AzureRmRoleDefinition-](./media/role-based-access-control-manage-access-powershell/4-remove-azurermroledefinition.png)

## <a name="list-custom-roles"></a>Lista de funções personalizadas
Para listar as funções que estão disponíveis para atribuição a um âmbito, utilize o `Get-AzureRmRoleDefinition` comando.

O exemplo seguinte apresenta uma lista de todas as funções que estão disponíveis para atribuição na subscrição selecionada.

```
Get-AzureRmRoleDefinition | FT Name, IsCustom
```

![Captura de ecrã do RBAC PowerShell-Get AzureRmRoleDefinition-](./media/role-based-access-control-manage-access-powershell/5-get-azurermroledefinition-1.png)

No exemplo seguinte, o *operador de Máquina Virtual* função personalizada não está disponível no *Production4* subscrição porque essa subscrição não está no **AssignableScopes** da função.

![Captura de ecrã do RBAC PowerShell-Get AzureRmRoleDefinition-](./media/role-based-access-control-manage-access-powershell/5-get-azurermroledefinition2.png)

## <a name="see-also"></a>Consultar também
* [Utilizar o Azure PowerShell com o Azure Resource Manager](../powershell-azure-resource-manager.md)
  [!INCLUDE [role-based-access-control-toc.md](../../includes/role-based-access-control-toc.md)]

