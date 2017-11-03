---
title: "Gerir o controlo de acesso baseado em funções (RBAC) com a CLI do Azure | Microsoft Docs"
description: "Saiba como gerir baseada em funções controlo de acesso (RBAC) com a interface de linha de comandos do Azure através da lista de funções e as ações de função e atribuir funções para os âmbitos de subscrição e a aplicação."
services: active-directory
documentationcenter: 
author: andredm7
manager: femila
ms.assetid: 3483ee01-8177-49e7-b337-4d5cb14f5e32
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/12/2017
ms.author: andredm
ms.reviewer: rqureshi
ms.openlocfilehash: 77315171754304c965f296670fbba3a4751a3656
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="manage-role-based-access-control-with-the-azure-command-line-interface"></a>Gerir o controlo de acesso baseado em funções com a interface de linha de comandos do Azure
> [!div class="op_single_selector"]
> * [PowerShell](role-based-access-control-manage-access-powershell.md)
> * [CLI do Azure](role-based-access-control-manage-access-azure-cli.md)
> * [API REST](role-based-access-control-manage-access-rest.md)


Pode utilizar o controlo de acesso baseado em funções (RBAC) no portal do Azure e a API do Azure Resource Manager para gerir o acesso à sua subscrição e os recursos de um nível detalhado. Com esta funcionalidade, pode conceder acesso para utilizadores, grupos ou principais de serviço do Active Directory através da atribuição de algumas funções aos mesmos num determinado âmbito.

Antes de poder utilizar a interface de linha de comandos (CLI) do Azure para gerir o RBAC, tem de ter os seguintes pré-requisitos:

* CLI do Azure versão 0.8.8 ou posterior. Para instalar a versão mais recente e associá-lo à sua subscrição do Azure, consulte [instalar e configurar a CLI do Azure](../cli-install-nodejs.md).
* O Azure Resource Manager, na CLI do Azure. Aceda a [utilizando a CLI do Azure com o Gestor de recursos](../xplat-cli-azure-resource-manager.md) para obter mais detalhes.

## <a name="list-roles"></a>Lista de funções
### <a name="list-all-available-roles"></a>Lista todas as funções disponíveis
Para listar todas as funções disponíveis, utilize:

        azure role list

O exemplo seguinte mostra a lista de *todas as funções disponíveis*.

```
azure role list --json | jq '.[] | {"roleName":.properties.roleName, "description":.properties.description}'
```

![Captura de ecrã de linha de comandos - lista de funções do azure - de RBAC Azure](./media/role-based-access-control-manage-access-azure-cli/1-azure-role-list.png)

### <a name="list-actions-of-a-role"></a>Lista de ações de uma função
Para listar as ações de uma função, utilize:

    azure role show "<role name>"

O exemplo seguinte mostra as ações do *contribuinte* e *contribuinte de Máquina Virtual* funções.

```
azure role show "contributor" --json | jq '.[] | {"Actions":.properties.permissions[0].actions,"NotActions":properties.permissions[0].notActions}'

azure role show "virtual machine contributor" --json | jq '.[] | .properties.permissions[0].actions'
```

![Captura de ecrã de linha de comandos - mostrar de função do azure - de RBAC Azure](./media/role-based-access-control-manage-access-azure-cli/1-azure-role-show.png)

## <a name="list-access"></a>Acesso de lista
### <a name="list-role-assignments-effective-on-a-resource-group"></a>Listar atribuições de função Efetivo num grupo de recursos
Para listar as atribuições de função que existem num grupo de recursos, utilize:

    azure role assignment list --resource-group <resource group name>

O exemplo seguinte mostra as atribuições de função no *pharma-vendas-projecforcast* grupo.

```
azure role assignment list --resource-group pharma-sales-projecforcast --json | jq '.[] | {"DisplayName":.properties.aADObject.displayName,"RoleDefinitionName":.properties.roleName,"Scope":.properties.scope}'
```

![Captura de ecrã de linha de comandos - lista de atribuição de função do azure por grupo de - de RBAC Azure](./media/role-based-access-control-manage-access-azure-cli/4-azure-role-assignment-list-1.png)

### <a name="list-role-assignments-for-a-user"></a>Listar atribuições de função para um utilizador
Para listar as atribuições de funções para um utilizador específico e as atribuições que estão atribuídas a grupos de um utilizador, utilize:

    azure role assignment list --signInName <user email>

Também pode ver as atribuições de funções que são herdadas de grupos ao modificar o comando:

    azure role assignment list --expandPrincipalGroups --signInName <user email>

O exemplo seguinte mostra as atribuições de funções que são concedidas para o  *sameert@aaddemo.com*  utilizador. Isto inclui as funções que são atribuídas diretamente para o utilizador e funções que são herdadas de grupos.

```
azure role assignment list --signInName sameert@aaddemo.com --json | jq '.[] | {"DisplayName":.properties.aADObject.DisplayName,"RoleDefinitionName":.properties.roleName,"Scope":.properties.scope}'

azure role assignment list --expandPrincipalGroups --signInName sameert@aaddemo.com --json | jq '.[] | {"DisplayName":.properties.aADObject.DisplayName,"RoleDefinitionName":.properties.roleName,"Scope":.properties.scope}'
```

![Captura de ecrã de linha de comandos - lista de atribuição de função do azure por utilizador - de RBAC Azure](./media/role-based-access-control-manage-access-azure-cli/4-azure-role-assignment-list-2.png)

## <a name="grant-access"></a>Conceder acesso
Para conceder acesso depois de ter identificado a função que pretende atribuir, utilize:

    azure role assignment create

### <a name="assign-a-role-to-group-at-the-subscription-scope"></a>Atribuir uma função ao grupo de âmbito de subscrição
Para atribuir uma função a um grupo no âmbito de subscrição, utilize:

    azure role assignment create --objectId  <group object id> --roleName <name of role> --subscription <subscription> --scope <subscription/subscription id>

O seguinte exemplo atribui o *leitor* função *equipa de Christine Koch* no *subscrição* âmbito.

![Linha de comandos do Azure RBAC - atribuição de função do azure crie ao grupo - captura de ecrã](./media/role-based-access-control-manage-access-azure-cli/2-azure-role-assignment-create-1.png)

### <a name="assign-a-role-to-an-application-at-the-subscription-scope"></a>Atribuir uma função a uma aplicação no âmbito de subscrição
Para atribuir uma função a uma aplicação no âmbito de subscrição, utilize:

    azure role assignment create --objectId  <applications object id> --roleName <name of role> --subscription <subscription> --scope <subscription/subscription id>

O exemplo seguinte concede o *contribuinte* função para uma *do Azure AD* aplicação na subscrição selecionada.

 ![Criação da atribuição de função do azure de linha de comandos do Azure RBAC - por aplicação](./media/role-based-access-control-manage-access-azure-cli/2-azure-role-assignment-create-2.png)

### <a name="assign-a-role-to-a-user-at-the-resource-group-scope"></a>Atribuir uma função a um utilizador no âmbito do grupo de recursos
Para atribuir uma função a um utilizador no âmbito do grupo de recursos, utilize:

    azure role assignment create --signInName  <user email address> --roleName "<name of role>" --resourceGroup <resource group name>

O exemplo seguinte concede o *contribuinte de Máquina Virtual* função  *samert@aaddemo.com*  utilizador à *Pharma-vendas-ProjectForcast* âmbito do grupo de recursos.

![Criação da atribuição de função do azure de linha de comandos do Azure RBAC - pelo utilizador - captura de ecrã](./media/role-based-access-control-manage-access-azure-cli/2-azure-role-assignment-create-3.png)

### <a name="assign-a-role-to-a-group-at-the-resource-scope"></a>Atribuir uma função a um grupo no âmbito do recurso
Para atribuir uma função a um grupo no âmbito de recursos, utilize:

    azure role assignment create --objectId <group id> --role "<name of role>" --resource-name <resource group name> --resource-type <resource group type> --parent <resource group parent> --resource-group <resource group>

O exemplo seguinte concede o *contribuinte de Máquina Virtual* função para uma *do Azure AD* grupo um *sub-rede*.

![Linha de comandos do Azure RBAC - atribuição de função do azure crie ao grupo - captura de ecrã](./media/role-based-access-control-manage-access-azure-cli/2-azure-role-assignment-create-4.png)

## <a name="remove-access"></a>Remover o acesso
Para remover uma atribuição de função, utilize:

    azure role assignment delete --objectId <object id to from which to remove role> --roleName "<role name>"

O exemplo a seguir remove o *contribuinte de Máquina Virtual* atribuição de função do  *sammert@aaddemo.com*  utilizador no *Pharma-vendas-ProjectForcast* grupo de recursos.
O exemplo, em seguida, remove a atribuição de função de um grupo na subscrição.

![Captura de ecrã de linha de comandos - eliminação da atribuição de função do azure - de RBAC Azure](./media/role-based-access-control-manage-access-azure-cli/3-azure-role-assignment-delete.png)

## <a name="create-a-custom-role"></a>Criar uma função personalizada
Para criar uma função personalizada, utilize:

    azure role definition create --role-definition <file path>

O exemplo seguinte cria uma função personalizada denominada *operador de Máquina Virtual*. Esta função personalizada concede acesso a todas as operações de leitura de *Microsoft. Compute*, *Microsoft*, e *Network* recursos fornecedores e concede acesso a iniciar, reiniciar e monitorizar máquinas virtuais. Esta função personalizada pode ser utilizada em duas subscrições. Este exemplo utiliza um ficheiro JSON como entrada.

![O JSON - definição de função personalizada - captura de ecrã](./media/role-based-access-control-manage-access-azure-cli/2-azure-role-create-1.png)

![Linha de comandos do Azure RBAC - função do azure criar - captura de ecrã](./media/role-based-access-control-manage-access-azure-cli/2-azure-role-create-2.png)

## <a name="modify-a-custom-role"></a>Modificar uma função personalizada
Para modificar uma função personalizada, primeiro use o `azure role definition list` comando para obter a definição de função. Segundo, efetue as alterações pretendidas para o ficheiro de definição de função. Por último, utilize `azure role definition update` para guardar a definição de função modificadas.

    azure role definition update --role-definition <file path>

O exemplo seguinte adiciona o *Microsoft.Insights/diagnosticSettings/* operação para o **ações**e uma subscrição do Azure para o **AssignableScopes** da Máquina Virtual função operador de personalizado.

![JSON - modificar a definição de função personalizada - captura de ecrã](./media/role-based-access-control-manage-access-azure-cli/3-azure-role-set-1.png)

![Captura de ecrã de linha de comandos - conjunto de função do azure - de RBAC Azure](./media/role-based-access-control-manage-access-azure-cli/3-azure-role-set2.png)

## <a name="delete-a-custom-role"></a>Eliminar uma função personalizada
Para eliminar uma função personalizada, utilize o `azure role definition list` o comando para determinar o **ID** da função. Em seguida, utilize o `azure role definition delete` comando para eliminar a função especificando o **ID**.

O exemplo a seguir remove o *operador de Máquina Virtual* função personalizada.

![Captura de ecrã de linha de comandos - eliminação de funções do azure - de RBAC Azure](./media/role-based-access-control-manage-access-azure-cli/4-azure-role-delete.png)

## <a name="list-custom-roles"></a>Lista de funções personalizadas
Para listar as funções que estão disponíveis para atribuição a um âmbito, utilize o `azure role list` comando.

O comando seguinte apresenta uma lista de todas as funções que estão disponíveis para atribuição na subscrição selecionada.

```
azure role definition list --json | jq '.[] | {"name":.properties.roleName, type:.properties.type}'
```

![Captura de ecrã de linha de comandos - lista de funções do azure - de RBAC Azure](./media/role-based-access-control-manage-access-azure-cli/5-azure-role-list1.png)

No exemplo seguinte, o *operador de Máquina Virtual* função personalizada não está disponível no *Production4* subscrição porque essa subscrição não está no **AssignableScopes** da função.

```
azure role definition list --json | jq '.[] | if .properties.type == "CustomRole" then .properties.roleName else empty end'
```

![Captura de ecrã de linha de comandos - lista de funções do azure para funções personalizadas - de RBAC Azure](./media/role-based-access-control-manage-access-azure-cli/5-azure-role-list2.png)

## <a name="next-steps"></a>Passos seguintes
[!INCLUDE [role-based-access-control-toc.md](../../includes/role-based-access-control-toc.md)]

