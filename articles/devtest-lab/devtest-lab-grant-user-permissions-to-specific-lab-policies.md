---
title: "Conceder permissões de utilizador para políticas de laboratório específico | Microsoft Docs"
description: "Saiba como conceder permissões de utilizador para políticas específicas de laboratório no DevTest Labs com base nas necessidades de cada utilizador"
services: devtest-lab,virtual-machines,visual-studio-online
documentationcenter: na
author: tomarcher
manager: douge
editor: 
ms.assetid: 5ca829f0-eb69-40a1-ae26-03a629db1d7e
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/25/2016
ms.author: tarcher
ms.openlocfilehash: 155debf5fea4439c8273d2518856952fbf0f871a
ms.sourcegitcommit: 62eaa376437687de4ef2e325ac3d7e195d158f9f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/22/2017
---
# <a name="grant-user-permissions-to-specific-lab-policies"></a>Conceder permissões de utilizador para políticas de laboratório específico
## <a name="overview"></a>Descrição geral
Este artigo ilustra como utilizar o PowerShell para conceder permissões de utilizadores a uma política de específica de laboratório. Dessa forma, as permissões podem ser aplicadas com base nas necessidades de cada utilizador. Por exemplo, poderá conceder a capacidade de alterar as definições de política VM, mas não as políticas de custo de um utilizador específico.

## <a name="policies-as-resources"></a>Políticas de recursos
Tal como explicado o [controlo de acesso baseado em funções do Azure](../active-directory/role-based-access-control-configure.md) artigo, RBAC permite a gestão de acesso detalhada de recursos do Azure. Utilizar o RBAC, pode segregar funções na sua equipa de DevOps e conceder apenas a quantidade de acesso aos utilizadores que precisam para desempenhar as suas funções.

No DevTest Labs, uma política é um tipo de recurso que permite que a ação de RBAC **Microsoft.DevTestLab/labs/policySets/policies/**. Cada política de laboratório é um recurso no tipo de recurso de política e pode ser atribuída como um âmbito de uma função RBAC.

Por exemplo, para conceder permissão de leitura/escrita de utilizadores para o **permitido tamanhos de VM** política, poderá criar uma função personalizada que funciona com o **Microsoft.DevTestLab/labs/policySets/policies/*** ação e, em seguida, atribuir os utilizadores adequados para esta função personalizada no âmbito do **Microsoft.DevTestLab/labs/policySets/policies/AllowedVmSizesInLab**.

Para saber mais sobre as funções personalizadas no RBAC, veja o [controlo de acesso de funções personalizadas](../active-directory/role-based-access-control-custom-roles.md).

## <a name="creating-a-lab-custom-role-using-powershell"></a>Criar uma função personalizada de laboratório com o PowerShell
Para começar, terá de ler o seguinte artigo, irá explicam como instalar e configurar os cmdlets do PowerShell do Azure: [https://azure.microsoft.com/blog/azps-1-0-pre](https://azure.microsoft.com/blog/azps-1-0-pre).

Assim que tiver configurado os cmdlets do PowerShell do Azure, pode realizar as seguintes tarefas:

* Listar todas as operações/ações para um fornecedor de recursos
* Lista as ações numa função específica:
* Criar uma função personalizada

O script do PowerShell seguinte ilustra os exemplos de como efetuar estas tarefas:

    ‘List all the operations/actions for a resource provider.
    Get-AzureRmProviderOperation -OperationSearchString "Microsoft.DevTestLab/*"

    ‘List actions in a particular role.
    (Get-AzureRmRoleDefinition "DevTest Labs User").Actions

    ‘Create custom role.
    $policyRoleDef = (Get-AzureRmRoleDefinition "DevTest Labs User")
    $policyRoleDef.Id = $null
    $policyRoleDef.Name = "Policy Contributor"
    $policyRoleDef.IsCustom = $true
    $policyRoleDef.AssignableScopes.Clear()
    $policyRoleDef.AssignableScopes.Add("/subscriptions/<SubscriptionID> ")
    $policyRoleDef.Actions.Add("Microsoft.DevTestLab/labs/policySets/policies/*")
    $policyRoleDef = (New-AzureRmRoleDefinition -Role $policyRoleDef)

## <a name="assigning-permissions-to-a-user-for-a-specific-policy-using-custom-roles"></a>Atribuir permissões a um utilizador para uma política específica utilizando funções personalizadas
Uma vez que definiu as funções personalizadas, pode atribui-las aos utilizadores. Para atribuir uma função personalizada a um utilizador, tem de obter primeiro o **ObjectId** que representa esse utilizador. Para tal, utilize o **Get-AzureRmADUser** cmdlet.

No exemplo seguinte, o **ObjectId** do *SomeUser* utilizador é 05DEFF7B-0AC3-4ABF-B74D-6A72CD5BF3F3.

    PS C:\>Get-AzureRmADUser -SearchString "SomeUser"

    DisplayName                    Type                           ObjectId
    -----------                    ----                           --------
    someuser@hotmail.com                                          05DEFF7B-0AC3-4ABF-B74D-6A72CD5BF3F3

Assim que tiver o **ObjectId** para o utilizador e um nome de função personalizada, pode atribuir essa função ao utilizador com o **New-AzureRmRoleAssignment** cmdlet:

    PS C:\>New-AzureRmRoleAssignment -ObjectId 05DEFF7B-0AC3-4ABF-B74D-6A72CD5BF3F3 -RoleDefinitionName "Policy Contributor" -Scope /subscriptions/<SubscriptionID>/resourceGroups/<ResourceGroupName>/providers/Microsoft.DevTestLab/labs/<LabName>/policySets/default/policies/AllowedVmSizesInLab

No exemplo anterior, o **AllowedVmSizesInLab** política é utilizada. Pode utilizar qualquer um dos seguintes políticas:

* MaxVmsAllowedPerUser
* MaxVmsAllowedPerLab
* AllowedVmSizesInLab
* LabVmsShutdown

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="next-steps"></a>Passos seguintes
Uma vez tiver conceder permissões de utilizador para políticas específicas de laboratório, seguem-se alguns passos a ter em consideração:

* [Proteger o acesso a um laboratório](devtest-lab-add-devtest-user.md)
* [Conjunto de políticas de laboratório](devtest-lab-set-lab-policy.md)
* [Criar um modelo de laboratório](devtest-lab-create-template.md)
* [Criar artefactos personalizados para as suas VMs](devtest-lab-artifact-author.md)
* [Adicionar uma VM para um laboratório](devtest-lab-add-vm.md)

