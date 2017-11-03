---
title: "Criar funções personalizadas para RBAC do Azure | Microsoft Docs"
description: "Saiba como definir funções personalizadas com controlo de acesso em funções do Azure mais precisa para gestão de identidades na sua subscrição do Azure."
services: active-directory
documentationcenter: 
author: andredm7
manager: femila
ms.assetid: e4206ea9-52c3-47ee-af29-f6eef7566fa5
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/11/2017
ms.author: andredm
ms.reviewer: rqureshi
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 8e72f2c8095d13c4b6df3c6576bd58806a3c0f2f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="create-custom-roles-for-azure-role-based-access-control"></a>Criar funções personalizadas para controlo de acesso em funções do Azure
Crie uma função personalizada no controlo de acesso em funções do Azure (RBAC), se nenhuma das funções incorporadas satisfazer as suas necessidades de acesso específicas. Funções personalizadas podem ser criadas utilizando [Azure PowerShell](role-based-access-control-manage-access-powershell.md), [Interface de linha de comandos do Azure](role-based-access-control-manage-access-azure-cli.md) (CLI) e o [REST API](role-based-access-control-manage-access-rest.md). Tal como funções incorporadas, pode atribuir funções personalizadas para os utilizadores, grupos e aplicações na subscrição, o grupo de recursos e âmbitos de recursos. Funções personalizadas são armazenadas num inquilino do Azure AD e podem ser partilhadas entre subscrições.

Cada inquilino, pode criar até 2000 funções personalizadas. 

O exemplo seguinte mostra uma função personalizada de monitorização e reiniciar as máquinas virtuais:

```
{
  "Name": "Virtual Machine Operator",
  "Id": "cadb4a5a-4e7a-47be-84db-05cad13b6769",
  "IsCustom": true,
  "Description": "Can monitor and restart virtual machines.",
  "Actions": [
    "Microsoft.Storage/*/read",
    "Microsoft.Network/*/read",
    "Microsoft.Compute/*/read",
    "Microsoft.Compute/virtualMachines/start/action",
    "Microsoft.Compute/virtualMachines/restart/action",
    "Microsoft.Authorization/*/read",
    "Microsoft.Resources/subscriptions/resourceGroups/read",
    "Microsoft.Insights/alertRules/*",
    "Microsoft.Insights/diagnosticSettings/*",
    "Microsoft.Support/*"
  ],
  "NotActions": [

  ],
  "AssignableScopes": [
    "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e",
    "/subscriptions/e91d47c4-76f3-4271-a796-21b4ecfe3624",
    "/subscriptions/34370e90-ac4a-4bf9-821f-85eeedeae1a2"
  ]
}
```
## <a name="actions"></a>Ações
O **ações** propriedade de uma função personalizada especifica as operações do Azure para os quais a função concede acesso. É uma coleção de cadeias de operação que identificam as operações com capacidade de segurança de fornecedores de recursos do Azure. Cadeias de operação seguem o formato do `Microsoft.<ProviderName>/<ChildResourceType>/<action>`. Cadeias de operação que contêm carateres universais (\*) conceder acesso a todas as operações que corresponde à cadeia de operação. Por exemplo:

* `*/read`concede acesso de leitura de operações para todos os tipos de recursos de todos os fornecedores de recursos do Azure.
* `Microsoft.Compute/*`concede acesso a todas as operações para todos os tipos de recurso no fornecedor de recursos Microsoft. Compute.
* `Microsoft.Network/*/read`concede acesso de leitura de operações para todos os tipos de recurso no fornecedor de recursos de Network do Azure.
* `Microsoft.Compute/virtualMachines/*`concede acesso a todas as operações de máquinas virtuais e respetivos subordinados tipos de recursos.
* `Microsoft.Web/sites/restart/Action`concede acesso ao reiniciar os Web sites.

Utilize `Get-AzureRmProviderOperation` (no PowerShell) ou `azure provider operations show` (na CLI do Azure) para operações de lista de fornecedores de recursos do Azure. Também pode utilizar estes comandos para verificar se uma cadeia de operação é válida e, para expandir as cadeias de operação de caráter universal.

```
Get-AzureRMProviderOperation Microsoft.Compute/virtualMachines/*/action | FT Operation, OperationName

Get-AzureRMProviderOperation Microsoft.Network/*
```

![Captura de ecrã do PowerShell - Get-AzureRMProviderOperation](./media/role-based-access-control-configure/1-get-azurermprovideroperation-1.png)

```
azure provider operations show "Microsoft.Compute/virtualMachines/*/action" --js on | jq '.[] | .operation'

azure provider operations show "Microsoft.Network/*"
```

![Azure mostrar de operações do fornecedor de captura de ecrã - azure do CLI "Microsoft.Compute/virtualMachines/\*/action" ](./media/role-based-access-control-configure/1-azure-provider-operations-show.png)

## <a name="notactions"></a>NotActions
Utilize o **NotActions** propriedade se o conjunto de operações que pretende permitir mais facilmente é definido excluindo operações restritas. O acesso concedido por uma função personalizada é calculado subtraindo o **NotActions** operações a partir do **ações** operações.

> [!NOTE]
> Se um utilizador é atribuído uma função que exclui a gravação de uma operação em **NotActions**e é atribuído uma segunda função concede acesso à mesma operação, o utilizador tem permissão para executar essa operação. **NotActions** não é uma negação regra – é simplesmente uma maneira conveniente para criar um conjunto de operações permitidas quando precisam de operações específicas a serem excluídos.
>
>

## <a name="assignablescopes"></a>AssignableScopes
O **AssignableScopes** propriedade da função personalizada Especifica os âmbitos (subscrições, grupos de recursos ou recursos) no qual a função personalizada está disponível para atribuição. Pode disponibilizar a função personalizada para atribuição no apenas subscrições ou grupos de recursos que precisem da mesma e não clutter experiência de utilizador para o resto do subscrições ou grupos de recursos.

Os exemplos de âmbitos atribuíveis válidos incluem:

* "subscrições/c276fc76-9cd4-44c9-99a7-4fd71546436e", "subscrições/e91d47c4-76f3-4271-a796-21b4ecfe3624" - faz com que a função disponíveis para atribuição em duas subscrições.
* "subscrições/c276fc76-9cd4-44c9-99a7-4fd71546436e" - faz com que a função disponíveis para atribuição de uma única subscrição.
* "/ subscrições/c276fc76-9cd4-44c9-99a7-4fd71546436e/resourceGroups/rede" - faz com que a função disponíveis para atribuição apenas no grupo de recursos de rede.

> [!NOTE]
> Tem de utilizar pelo menos uma subscrição, o grupo de recursos ou o ID de recurso.
>
>

## <a name="custom-roles-access-control"></a>Controlo de acesso de funções personalizadas
O **AssignableScopes** propriedade da função personalizada também controla quem pode ver, modificar e eliminar a função.

* Quem pode criar uma função personalizada?
    Os proprietários (e administradores do acesso de utilizador) das subscrições, grupos de recursos e recursos, podem criar funções personalizadas para utilização nesses âmbitos.
    O criação da função de utilizador tem de ser capazes de efetuar `Microsoft.Authorization/roleDefinition/write` operação em todos os o **AssignableScopes** da função.
* Quem pode modificar uma função personalizada?
    Os proprietários (e administradores do acesso de utilizador) das subscrições, grupos de recursos e recursos, podem modificar as funções personalizadas nesses âmbitos. Os utilizadores têm de ser capazes de efetuar o `Microsoft.Authorization/roleDefinition/write` operação em todos os o **AssignableScopes** de uma função personalizada.
* Quem pode ver funções personalizadas?
    Todas as funções incorporadas no RBAC do Azure permitem a visualização das funções que estão disponíveis para atribuição. Os utilizadores que podem realizar a `Microsoft.Authorization/roleDefinition/read` operação num âmbito pode ver as funções do RBAC que estão disponíveis para atribuição a esse âmbito.

## <a name="see-also"></a>Consultar também
* [Controlo de acesso baseado em funções](role-based-access-control-configure.md): começar a utilizar o RBAC no portal do Azure.
* Saiba como gerir o acesso com:
  * [PowerShell](role-based-access-control-manage-access-powershell.md)
  * [CLI do Azure](role-based-access-control-manage-access-azure-cli.md)
  * [API REST](role-based-access-control-manage-access-rest.md)
* [Funções incorporadas](role-based-access-built-in-roles.md): obter detalhes sobre as funções que vêm padrão no RBAC.
