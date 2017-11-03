---
title: "Controlo de acesso baseado em funções com REST - do Azure AD | Microsoft Docs"
description: "Gerir o controlo de acesso baseado em funções com a API REST"
services: active-directory
documentationcenter: na
author: andredm7
manager: femila
editor: 
ms.assetid: 1f90228a-7aac-4ea7-ad82-b57d222ab128
ms.service: active-directory
ms.workload: multiple
ms.tgt_pltfrm: rest-api
ms.devlang: na
ms.topic: article
ms.date: 05/16/2017
ms.author: andredm
ms.openlocfilehash: a5c19fd87ce1ae3e199bf1dfc8cf82f5653baac2
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="manage-role-based-access-control-with-the-rest-api"></a>Gerir o controlo de acesso baseado em funções com a API REST
> [!div class="op_single_selector"]
> * [PowerShell](role-based-access-control-manage-access-powershell.md)
> * [CLI do Azure](role-based-access-control-manage-access-azure-cli.md)
> * [API REST](role-based-access-control-manage-access-rest.md)

Baseada em funções controlo de acesso (RBAC) no portal do Azure e a API do Gestor de recursos do Azure ajuda-o a gerir o acesso à sua subscrição e os recursos de um nível detalhado. Com esta funcionalidade, pode conceder acesso para utilizadores, grupos ou principais de serviço do Active Directory através da atribuição de algumas funções aos mesmos num determinado âmbito.

## <a name="list-all-role-assignments"></a>Lista todas as atribuições de função
Apresenta uma lista de todas as atribuições de função no âmbito especificado e subscopes.

Para listar atribuições de função, tem de ter acesso a `Microsoft.Authorization/roleAssignments/read` operação no âmbito. Todas as funções incorporadas são concedidas acesso a esta operação. Para obter mais informações sobre atribuições de funções e gestão de acesso para recursos do Azure, consulte [controlo de acesso em funções do Azure](role-based-access-control-configure.md).

### <a name="request"></a>Pedir
Utilize o **obter** método com o URI seguinte:

    https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleAssignments?api-version={api-version}&$filter={filter}

No URI, efetue as seguintes substituições para personalizar o seu pedido:

1. Substitua *{âmbito}* com o âmbito para o qual pretende listar as atribuições de funções. Os exemplos seguintes mostram como especificar o âmbito de diferentes níveis:

   * Subscrição: /subscriptions/{targetsubscriptionid}/resourcegroups/{targetresourcegroupname} {id de subscrição}  
   * Grupo de recursos: /subscriptions/{targetsubscriptionid}/resourcegroups/{targetresourcegroupname} {id de subscrição} / resourceGroups/myresourcegroup1  
   * Recurso: /subscriptions/{subscription-id}/resourceGroups/myresourcegroup1/providers/Microsoft.Web/sites/mysite1  
2. Substitua *{api-version}* com 2015-07-01.
3. Substitua *{filtro}* com a condição que pretende aplicar para filtrar a lista de atribuição de função:

   * Listar atribuições de funções para apenas o âmbito especificado, não incluindo as atribuições de função no subscopes:`atScope()`    
   * Listar atribuições de funções para um utilizador específico, grupo ou aplicação:`principalId%20eq%20'{objectId of user, group, or service principal}'`  
   * Listar atribuições de funções para um utilizador específico, incluindo aqueles herdada a partir de grupos |`assignedTo('{objectId of user}')`

### <a name="response"></a>Resposta
Código de estado: 200

```
{
  "value": [
    {
      "properties": {
        "roleDefinitionId": "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e/providers/Microsoft.Authorization/roleDefinitions/acdd72a7-3385-48ef-bd42-f606fba81ae7",
        "principalId": "2f9d4375-cbf1-48e8-83c9-2a0be4cb33fb",
        "scope": "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e",
        "createdOn": "2015-10-08T07:28:24.3905077Z",
        "updatedOn": "2015-10-08T07:28:24.3905077Z",
        "createdBy": "877f0ab8-9c5f-420b-bf88-a1c6c7e2643e",
        "updatedBy": "877f0ab8-9c5f-420b-bf88-a1c6c7e2643e"
      },
      "id": "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e/providers/Microsoft.Authorization/roleAssignments/baa6e199-ad19-4667-b768-623fde31aedd",
      "type": "Microsoft.Authorization/roleAssignments",
      "name": "baa6e199-ad19-4667-b768-623fde31aedd"
    }
  ],
  "nextLink": null
}

```

## <a name="get-information-about-a-role-assignment"></a>Obter informações sobre uma atribuição de função
Obtém informações sobre uma atribuição de função única especificada pelo identificador de atribuição de função.

Para obter informações sobre uma atribuição de função, tem de ter acesso a `Microsoft.Authorization/roleAssignments/read` operação. Todas as funções incorporadas são concedidas acesso a esta operação. Para obter mais informações sobre atribuições de funções e gestão de acesso para recursos do Azure, consulte [controlo de acesso em funções do Azure](role-based-access-control-configure.md).

### <a name="request"></a>Pedir
Utilize o **obter** método com o URI seguinte:

    https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleAssignments/{role-assignment-id}?api-version={api-version}

No URI, efetue as seguintes substituições para personalizar o seu pedido:

1. Substitua *{âmbito}* com o âmbito para o qual pretende listar as atribuições de funções. Os exemplos seguintes mostram como especificar o âmbito de diferentes níveis:

   * Subscrição: /subscriptions/{targetsubscriptionid}/resourcegroups/{targetresourcegroupname} {id de subscrição}  
   * Grupo de recursos: /subscriptions/{targetsubscriptionid}/resourcegroups/{targetresourcegroupname} {id de subscrição} / resourceGroups/myresourcegroup1  
   * Recurso: /subscriptions/{subscription-id}/resourceGroups/myresourcegroup1/providers/Microsoft.Web/sites/mysite1  
2. Substitua *{id de atribuição de função}* com o identificador GUID de atribuição de função.
3. Substitua *{api-version}* com 2015-07-01.

### <a name="response"></a>Resposta
Código de estado: 200

```
{
  "properties": {
    "roleDefinitionId": "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e/providers/Microsoft.Authorization/roleDefinitions/b24988ac-6180-42a0-ab88-20f7382dd24c",
    "principalId": "672f1afa-526a-4ef6-819c-975c7cd79022",
    "scope": "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e",
    "createdOn": "2015-10-05T08:36:26.4014813Z",
    "updatedOn": "2015-10-05T08:36:26.4014813Z",
    "createdBy": "877f0ab8-9c5f-420b-bf88-a1c6c7e2643e",
    "updatedBy": "877f0ab8-9c5f-420b-bf88-a1c6c7e2643e"
  },
  "id": "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e/providers/Microsoft.Authorization/roleAssignments/196965ae-6088-4121-a92a-f1e33fdcc73e",
  "type": "Microsoft.Authorization/roleAssignments",
  "name": "196965ae-6088-4121-a92a-f1e33fdcc73e"
}

```

## <a name="create-a-role-assignment"></a>Criar uma atribuição de função
Crie uma atribuição de função no âmbito especificado para o principal especificado conceder a função especificada.

Para criar uma atribuição de função, tem de ter acesso a `Microsoft.Authorization/roleAssignments/write` operação. Das funções incorporadas, apenas *proprietário* e *administrador de acesso de utilizador* é concedido o acesso a esta operação. Para obter mais informações sobre atribuições de funções e gestão de acesso para recursos do Azure, consulte [controlo de acesso em funções do Azure](role-based-access-control-configure.md).

### <a name="request"></a>Pedir
Utilize o **colocar** método com o URI seguinte:

    https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleAssignments/{role-assignment-id}?api-version={api-version}

No URI, efetue as seguintes substituições para personalizar o seu pedido:

1. Substitua *{âmbito}* com âmbito no qual pretende criar as atribuições de funções. Quando cria uma atribuição de função num âmbito principal, todos os âmbitos subordinados herdam a mesma atribuição de função. Os exemplos seguintes mostram como especificar o âmbito de diferentes níveis:

   * Subscrição: /subscriptions/{targetsubscriptionid}/resourcegroups/{targetresourcegroupname} {id de subscrição}  
   * Grupo de recursos: /subscriptions/{targetsubscriptionid}/resourcegroups/{targetresourcegroupname} {id de subscrição} / resourceGroups/myresourcegroup1   
   * Recurso: /subscriptions/{subscription-id}/resourceGroups/myresourcegroup1/providers/Microsoft.Web/sites/mysite1  
2. Substitua *{id de atribuição de função}* com um novo GUID, que torna-se o identificador GUID da nova atribuição de função.
3. Substitua *{api-version}* com 2015-07-01.

Para o corpo do pedido, forneça os valores no seguinte formato:

```
{
  "properties": {
    "roleDefinitionId": "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e/resourceGroups/Network/providers/Microsoft.Network/virtualNetworks/EASTUS-VNET-01/subnets/Devices-Engineering-ProjectRND/providers/Microsoft.Authorization/roleDefinitions/9980e02c-c2be-4d73-94e8-173b1dc7cf3c",
    "principalId": "5ac84765-1c8c-4994-94b2-629461bd191b"
  }
}

```

| Nome do elemento | Necessário | Tipo | Descrição |
| --- | --- | --- | --- |
| em roleDefinitionId |Sim |Cadeia |O identificador da função. O formato do identificador é:`{scope}/providers/Microsoft.Authorization/roleDefinitions/{role-definition-id-guid}` |
| principalId |Sim |Cadeia |objectId do Azure AD principal (utilizador, grupo ou principal de serviço) para os quais a função é atribuída. |

### <a name="response"></a>Resposta
Código de estado: 201

```
{
  "properties": {
    "roleDefinitionId": "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e/providers/Microsoft.Authorization/roleDefinitions/9980e02c-c2be-4d73-94e8-173b1dc7cf3c",
    "principalId": "5ac84765-1c8c-4994-94b2-629461bd191b",
    "scope": "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e/resourceGroups/Network/providers/Microsoft.Network/virtualNetworks/EASTUS-VNET-01/subnets/Devices-Engineering-ProjectRND",
    "createdOn": "2015-12-16T00:27:19.6447515Z",
    "updatedOn": "2015-12-16T00:27:19.6447515Z",
    "createdBy": null,
    "updatedBy": "877f0ab8-9c5f-420b-bf88-a1c6c7e2643e"
  },
  "id": "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e/resourceGroups/Network/providers/Microsoft.Network/virtualNetworks/EASTUS-VNET-01/subnets/Devices-Engineering-ProjectRND/providers/Microsoft.Authorization/roleAssignments/2e9e86c8-0e91-4958-b21f-20f51f27bab2",
  "type": "Microsoft.Authorization/roleAssignments",
  "name": "2e9e86c8-0e91-4958-b21f-20f51f27bab2"
}

```

## <a name="delete-a-role-assignment"></a>Elimina uma atribuição de função
Elimina uma atribuição de função no âmbito especificado.

Para eliminar uma atribuição de função, tem de ter acesso para o `Microsoft.Authorization/roleAssignments/delete` operação. Das funções incorporadas, apenas *proprietário* e *administrador de acesso de utilizador* é concedido o acesso a esta operação. Para obter mais informações sobre atribuições de funções e gestão de acesso para recursos do Azure, consulte [controlo de acesso em funções do Azure](role-based-access-control-configure.md).

### <a name="request"></a>Pedir
Utilize o **eliminar** método com o URI seguinte:

    https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleAssignments/{role-assignment-id}?api-version={api-version}

No URI, efetue as seguintes substituições para personalizar o seu pedido:

1. Substitua *{âmbito}* com âmbito no qual pretende criar as atribuições de funções. Os exemplos seguintes mostram como especificar o âmbito de diferentes níveis:

   * Subscrição: /subscriptions/{targetsubscriptionid}/resourcegroups/{targetresourcegroupname} {id de subscrição}  
   * Grupo de recursos: /subscriptions/{targetsubscriptionid}/resourcegroups/{targetresourcegroupname} {id de subscrição} / resourceGroups/myresourcegroup1  
   * Recurso: /subscriptions/{subscription-id}/resourceGroups/myresourcegroup1/providers/Microsoft.Web/sites/mysite1  
2. Substitua *{id de atribuição de função}* com o id de atribuição de função GUID.
3. Substitua *{api-version}* com 2015-07-01.

### <a name="response"></a>Resposta
Código de estado: 200

```
{
  "properties": {
    "roleDefinitionId": "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e/providers/Microsoft.Authorization/roleDefinitions/9980e02c-c2be-4d73-94e8-173b1dc7cf3c",
    "principalId": "5ac84765-1c8c-4994-94b2-629461bd191b",
    "scope": "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e/resourceGroups/Network/providers/Microsoft.Network/virtualNetworks/EASTUS-VNET-01/subnets/Devices-Engineering-ProjectRND",
    "createdOn": "2015-12-17T23:21:40.8921564Z",
    "updatedOn": "2015-12-17T23:21:40.8921564Z",
    "createdBy": "877f0ab8-9c5f-420b-bf88-a1c6c7e2643e",
    "updatedBy": "877f0ab8-9c5f-420b-bf88-a1c6c7e2643e"
  },
  "id": "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e/resourceGroups/Network/providers/Microsoft.Network/virtualNetworks/EASTUS-VNET-01/subnets/Devices-Engineering-ProjectRND/providers/Microsoft.Authorization/roleAssignments/5eec22ee-ea5c-431e-8f41-82c560706fd2",
  "type": "Microsoft.Authorization/roleAssignments",
  "name": "5eec22ee-ea5c-431e-8f41-82c560706fd2"
}

```

## <a name="list-all-roles"></a>Lista todas as funções
Apresenta uma lista de todas as funções que estão disponíveis para atribuição no âmbito especificado.

Para funções de lista, tem de ter acesso a `Microsoft.Authorization/roleDefinitions/read` operação no âmbito. Todas as funções incorporadas são concedidas acesso a esta operação. Para obter mais informações sobre atribuições de funções e gestão de acesso para recursos do Azure, consulte [controlo de acesso em funções do Azure](role-based-access-control-configure.md).

### <a name="request"></a>Pedir
Utilize o **obter** método com o URI seguinte:

    https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleDefinitions?api-version={api-version}&$filter={filter}

No URI, efetue as seguintes substituições para personalizar o seu pedido:

1. Substitua *{âmbito}* com o âmbito para o qual pretende listar as funções. Os exemplos seguintes mostram como especificar o âmbito de diferentes níveis:

   * Subscrição: /subscriptions/{targetsubscriptionid}/resourcegroups/{targetresourcegroupname} {id de subscrição}  
   * Grupo de recursos: /subscriptions/{targetsubscriptionid}/resourcegroups/{targetresourcegroupname} {id de subscrição} / resourceGroups/myresourcegroup1  
   * Recurso /subscriptions/{subscription-id}/resourceGroups/myresourcegroup1/providers/Microsoft.Web/sites/mysite1  
2. Substitua *{api-version}* com 2015-07-01.
3. Substitua *{filtro}* com a condição que pretende aplicar para filtrar a lista de funções:

   * Lista de funções disponíveis para atribuição no âmbito especificado e nenhum dos respetivos âmbitos subordinados:`atScopeAndBelow()`
   * Pesquisa para uma função com o nome a apresentar exato: `roleName%20eq%20'{role-display-name}'`. Utilize o formato de URL codificado do nome de apresentação exato da função. Por exemplo,`$filter=roleName%20eq%20'Virtual%20Machine%20Contributor'` |

### <a name="response"></a>Resposta
Código de estado: 200

```
{
  "value": [
    {
      "properties": {
        "roleName": "Virtual Machine Contributor",
        "type": "BuiltInRole",
        "description": "Lets you manage virtual machines, but not access to them, and not the virtual network or storage account they\u2019re connected to.",
        "assignableScopes": [
          "/"
        ],
        "permissions": [
          {
            "actions": [
              "Microsoft.Authorization/*/read",
              "Microsoft.Compute/availabilitySets/*",
              "Microsoft.Compute/locations/*",
              "Microsoft.Compute/virtualMachines/*",
              "Microsoft.Compute/virtualMachineScaleSets/*",
              "Microsoft.Insights/alertRules/*",
              "Microsoft.Network/applicationGateways/backendAddressPools/join/action",
              "Microsoft.Network/loadBalancers/backendAddressPools/join/action",
              "Microsoft.Network/loadBalancers/inboundNatPools/join/action",
              "Microsoft.Network/loadBalancers/inboundNatRules/join/action",
              "Microsoft.Network/loadBalancers/read",
              "Microsoft.Network/locations/*",
              "Microsoft.Network/networkInterfaces/*",
              "Microsoft.Network/networkSecurityGroups/join/action",
              "Microsoft.Network/networkSecurityGroups/read",
              "Microsoft.Network/publicIPAddresses/join/action",
              "Microsoft.Network/publicIPAddresses/read",
              "Microsoft.Network/virtualNetworks/read",
              "Microsoft.Network/virtualNetworks/subnets/join/action",
              "Microsoft.Resources/deployments/*",
              "Microsoft.Resources/subscriptions/resourceGroups/read",
              "Microsoft.Storage/storageAccounts/listKeys/action",
              "Microsoft.Storage/storageAccounts/read",
              "Microsoft.Support/*"
            ],
            "notActions": []
          }
        ],
        "createdOn": "2015-06-02T00:18:27.3542698Z",
        "updatedOn": "2015-12-08T03:16:55.6170255Z",
        "createdBy": null,
        "updatedBy": null
      },
      "id": "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e/providers/Microsoft.Authorization/roleDefinitions/9980e02c-c2be-4d73-94e8-173b1dc7cf3c",
      "type": "Microsoft.Authorization/roleDefinitions",
      "name": "9980e02c-c2be-4d73-94e8-173b1dc7cf3c"
    }
  ],
  "nextLink": null
}

```

## <a name="get-information-about-a-role"></a>Obter informações sobre uma função
Obtém informações sobre uma única função especificada pelo identificador de definição de função. Para obter informações sobre uma única função utilizando o respetivo nome a apresentar, consulte o artigo [lista todas as funções](role-based-access-control-manage-access-rest.md#list-all-roles).

Para obter informações sobre uma função, tem de ter acesso a `Microsoft.Authorization/roleDefinitions/read` operação. Todas as funções incorporadas são concedidas acesso a esta operação. Para obter mais informações sobre atribuições de funções e gestão de acesso para recursos do Azure, consulte [controlo de acesso em funções do Azure](role-based-access-control-configure.md).

### <a name="request"></a>Pedir
Utilize o **obter** método com o URI seguinte:

    https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleDefinitions/{role-definition-id}?api-version={api-version}

No URI, efetue as seguintes substituições para personalizar o seu pedido:

1. Substitua *{âmbito}* com o âmbito para o qual pretende listar as atribuições de funções. Os exemplos seguintes mostram como especificar o âmbito de diferentes níveis:

   * Subscrição: /subscriptions/{targetsubscriptionid}/resourcegroups/{targetresourcegroupname} {id de subscrição}  
   * Grupo de recursos: /subscriptions/{targetsubscriptionid}/resourcegroups/{targetresourcegroupname} {id de subscrição} / resourceGroups/myresourcegroup1  
   * Recurso: /subscriptions/{subscription-id}/resourceGroups/myresourcegroup1/providers/Microsoft.Web/sites/mysite1  
2. Substitua *{id de definição de função}* com o identificador GUID da definição de função.
3. Substitua *{api-version}* com 2015-07-01.

### <a name="response"></a>Resposta
Código de estado: 200

```
{
  "value": [
    {
      "properties": {
        "roleName": "Virtual Machine Contributor",
        "type": "BuiltInRole",
        "description": "Lets you manage virtual machines, but not access to them, and not the virtual network or storage account they\u2019re connected to.",
        "assignableScopes": [
          "/"
        ],
        "permissions": [
          {
            "actions": [
              "Microsoft.Authorization/*/read",
              "Microsoft.Compute/availabilitySets/*",
              "Microsoft.Compute/locations/*",
              "Microsoft.Compute/virtualMachines/*",
              "Microsoft.Compute/virtualMachineScaleSets/*",
              "Microsoft.Insights/alertRules/*",
              "Microsoft.Network/applicationGateways/backendAddressPools/join/action",
              "Microsoft.Network/loadBalancers/backendAddressPools/join/action",
              "Microsoft.Network/loadBalancers/inboundNatPools/join/action",
              "Microsoft.Network/loadBalancers/inboundNatRules/join/action",
              "Microsoft.Network/loadBalancers/read",
              "Microsoft.Network/locations/*",
              "Microsoft.Network/networkInterfaces/*",
              "Microsoft.Network/networkSecurityGroups/join/action",
              "Microsoft.Network/networkSecurityGroups/read",
              "Microsoft.Network/publicIPAddresses/join/action",
              "Microsoft.Network/publicIPAddresses/read",
              "Microsoft.Network/virtualNetworks/read",
              "Microsoft.Network/virtualNetworks/subnets/join/action",
              "Microsoft.Resources/deployments/*",
              "Microsoft.Resources/subscriptions/resourceGroups/read",
              "Microsoft.Storage/storageAccounts/listKeys/action",
              "Microsoft.Storage/storageAccounts/read",
              "Microsoft.Support/*"
            ],
            "notActions": []
          }
        ],
        "createdOn": "2015-06-02T00:18:27.3542698Z",
        "updatedOn": "2015-12-08T03:16:55.6170255Z",
        "createdBy": null,
        "updatedBy": null
      },
      "id": "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e/providers/Microsoft.Authorization/roleDefinitions/9980e02c-c2be-4d73-94e8-173b1dc7cf3c",
      "type": "Microsoft.Authorization/roleDefinitions",
      "name": "9980e02c-c2be-4d73-94e8-173b1dc7cf3c"
    }
  ],
  "nextLink": null
}

```

## <a name="create-a-custom-role"></a>Criar uma função personalizada
Crie uma função personalizada.

Para criar uma função personalizada, tem de ter acesso a `Microsoft.Authorization/roleDefinitions/write` operação em todos os o `AssignableScopes`. Das funções incorporadas, apenas *proprietário* e *administrador de acesso de utilizador* é concedido o acesso a esta operação. Para obter mais informações sobre atribuições de funções e gestão de acesso para recursos do Azure, consulte [controlo de acesso em funções do Azure](role-based-access-control-configure.md).

### <a name="request"></a>Pedir
Utilize o **colocar** método com o URI seguinte:

    https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleDefinitions/{role-definition-id}?api-version={api-version}

No URI, efetue as seguintes substituições para personalizar o seu pedido:

1. Substitua *{âmbito}* com o primeiro *AssignableScope* da função personalizada. Os exemplos seguintes mostram como especificar o âmbito de diferentes níveis.

   * Subscrição: /subscriptions/{targetsubscriptionid}/resourcegroups/{targetresourcegroupname} {id de subscrição}  
   * Grupo de recursos: /subscriptions/{targetsubscriptionid}/resourcegroups/{targetresourcegroupname} {id de subscrição} / resourceGroups/myresourcegroup1  
   * Recurso: /subscriptions/{subscription-id}/resourceGroups/myresourcegroup1/providers/Microsoft.Web/sites/mysite1  
2. Substitua *{id de definição de função}* com um novo GUID, que torna-se o identificador GUID da nova função personalizada.
3. Substitua *{api-version}* com 2015-07-01.

Para o corpo do pedido, forneça os valores no seguinte formato:

```
{
  "name": "7c8c8ccd-9838-4e42-b38c-60f0bbe9a9d7",
  "properties": {
    "roleName": "Virtual Machine Operator",
    "description": "Lets you monitor virtual machines and restart them.",
    "type": "CustomRole",
    "permissions": [
      {
        "actions": [
          "Microsoft.Authorization/*/read",
          "Microsoft.Compute/*/read",
          "Microsoft.Insights/alertRules/*",
          "Microsoft.Network/*/read",
          "Microsoft.Resources/subscriptions/resourceGroups/read",
          "Microsoft.Storage/*/read",
          "Microsoft.Support/*",
          "Microsoft.Compute/virtualMachines/start/action",
          "Microsoft.Compute/virtualMachines/restart/action"
        ],
        "notActions": []
      }
    ],
    "assignableScopes": [
      "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e"
    ]
  }
}

```

| Nome do elemento | Necessário | Tipo | Descrição |
| --- | --- | --- | --- |
| nome |Sim |Cadeia |Identificador GUID da função personalizada. |
| properties.roleName |Sim |Cadeia |Nome a apresentar da função personalizada. Caracteres de tamanho máximo de 128. |
| Properties.Description |Não |Cadeia |Descrição da função personalizada. Caracteres de tamanho máximo 1024. |
| Properties.Type |Sim |Cadeia |Definido como "CustomRole." |
| Properties.Permissions.Actions |Sim |String] |Uma matriz de cadeias de ação especificando as operações concedidas pela função personalizada. |
| properties.permissions.notActions |Não |String] |Uma matriz de cadeias de ação especificando as operações para excluir das operações concedidas pela função personalizada. |
| properties.assignableScopes |Sim |String] |Uma matriz de âmbitos nos quais a função personalizada pode ser utilizada. |

### <a name="response"></a>Resposta
Código de estado: 201

```
{
  "properties": {
    "roleName": "Virtual Machine Operator",
    "type": "CustomRole",
    "description": "Lets you monitor virtual machines and restart them.",
    "assignableScopes": [
      "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e"
    ],
    "permissions": [
      {
        "actions": [
          "Microsoft.Authorization/*/read",
          "Microsoft.Compute/*/read",
          "Microsoft.Insights/alertRules/*",
          "Microsoft.Network/*/read",
          "Microsoft.Resources/subscriptions/resourceGroups/read",
          "Microsoft.Storage/*/read",
          "Microsoft.Support/*",
          "Microsoft.Compute/virtualMachines/start/action",
          "Microsoft.Compute/virtualMachines/restart/action"
        ],
        "notActions": []
      }
    ],
    "createdOn": "2015-12-18T00:10:51.4662695Z",
    "updatedOn": "2015-12-18T00:10:51.4662695Z",
    "createdBy": "877f0ab8-9c5f-420b-bf88-a1c6c7e2643e",
    "updatedBy": "877f0ab8-9c5f-420b-bf88-a1c6c7e2643e"
  },
  "id": "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e/providers/Microsoft.Authorization/roleDefinitions/7c8c8ccd-9838-4e42-b38c-60f0bbe9a9d7",
  "type": "Microsoft.Authorization/roleDefinitions",
  "name": "7c8c8ccd-9838-4e42-b38c-60f0bbe9a9d7"
}

```

## <a name="update-a-custom-role"></a>Atualizar uma função personalizada
Modificar uma função personalizada.

Para modificar uma função personalizada, tem de ter acesso a `Microsoft.Authorization/roleDefinitions/write` operação em todos os o `AssignableScopes`. Das funções incorporadas, apenas *proprietário* e *administrador de acesso de utilizador* é concedido o acesso a esta operação. Para obter mais informações sobre atribuições de funções e gestão de acesso para recursos do Azure, consulte [controlo de acesso em funções do Azure](role-based-access-control-configure.md).

### <a name="request"></a>Pedir
Utilize o **colocar** método com o URI seguinte:

    https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleDefinitions/{role-definition-id}?api-version={api-version}

No URI, efetue as seguintes substituições para personalizar o seu pedido:

1. Substitua *{âmbito}* com o primeiro *AssignableScope* da função personalizada. Os exemplos seguintes mostram como especificar o âmbito de diferentes níveis:

   * Subscrição: /subscriptions/{targetsubscriptionid}/resourcegroups/{targetresourcegroupname} {id de subscrição}  
   * Grupo de recursos: /subscriptions/{targetsubscriptionid}/resourcegroups/{targetresourcegroupname} {id de subscrição} / resourceGroups/myresourcegroup1  
   * Recurso: /subscriptions/{subscription-id}/resourceGroups/myresourcegroup1/providers/Microsoft.Web/sites/mysite1  
2. Substitua *{id de definição de função}* com o identificador GUID da função personalizada.
3. Substitua *{api-version}* com 2015-07-01.

Para o corpo do pedido, forneça os valores no seguinte formato:

```
{
  "name": "7c8c8ccd-9838-4e42-b38c-60f0bbe9a9d7",
  "properties": {
    "roleName": "Virtual Machine Operator",
    "description": "Lets you monitor virtual machines and restart them.",
    "type": "CustomRole",
    "permissions": [
      {
        "actions": [
          "Microsoft.Authorization/*/read",
          "Microsoft.Compute/*/read",
          "Microsoft.Insights/alertRules/*",
          "Microsoft.Network/*/read",
          "Microsoft.Resources/subscriptions/resourceGroups/read",
          "Microsoft.Storage/*/read",
          "Microsoft.Support/*",
          "Microsoft.Compute/virtualMachines/start/action",
          "Microsoft.Compute/virtualMachines/restart/action"
        ],
        "notActions": []
      }
    ],
    "assignableScopes": [
      "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e"
    ]
  }
}

```

| Nome do elemento | Necessário | Tipo | Descrição |
| --- | --- | --- | --- |
| nome |Sim |Cadeia |Identificador GUID da função personalizada. |
| properties.roleName |Sim |Cadeia |Nome a apresentar da função personalizada atualizada. |
| Properties.Description |Não |Cadeia |Descrição da função personalizada atualizada. |
| Properties.Type |Sim |Cadeia |Definido como "CustomRole." |
| Properties.Permissions.Actions |Sim |String] |Uma matriz de cadeias de ação especificando as operações para os quais a função personalizada atualizada conceda acesso. |
| properties.permissions.notActions |Não |String] |Uma matriz de cadeias de ação especificando as operações para excluir das operações que atribui a função personalizada atualizada. |
| properties.assignableScopes |Sim |String] |Uma matriz de âmbitos nos quais a função personalizada atualizada pode ser utilizada. |

### <a name="response"></a>Resposta
Código de estado: 201

```
{
  "properties": {
    "roleName": "Virtual Machine Operator",
    "type": "CustomRole",
    "description": "Lets you monitor virtual machines and restart them.",
    "assignableScopes": [
      "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e"
    ],
    "permissions": [
      {
        "actions": [
          "Microsoft.Authorization/*/read",
          "Microsoft.Compute/*/read",
          "Microsoft.Insights/alertRules/*",
          "Microsoft.Network/*/read",
          "Microsoft.Resources/subscriptions/resourceGroups/read",
          "Microsoft.Storage/*/read",
          "Microsoft.Support/*",
          "Microsoft.Compute/virtualMachines/start/action",
          "Microsoft.Compute/virtualMachines/restart/action"
        ],
        "notActions": []
      }
    ],
    "createdOn": "2015-12-18T00:10:51.4662695Z",
    "updatedOn": "2015-12-18T00:10:51.4662695Z",
    "createdBy": "877f0ab8-9c5f-420b-bf88-a1c6c7e2643e",
    "updatedBy": "877f0ab8-9c5f-420b-bf88-a1c6c7e2643e"
  },
  "id": "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e/providers/Microsoft.Authorization/roleDefinitions/7c8c8ccd-9838-4e42-b38c-60f0bbe9a9d7",
  "type": "Microsoft.Authorization/roleDefinitions",
  "name": "7c8c8ccd-9838-4e42-b38c-60f0bbe9a9d7"
}

```

## <a name="delete-a-custom-role"></a>Eliminar uma função personalizada
Elimine uma função personalizada.

Para eliminar uma função personalizada, tem de ter acesso a `Microsoft.Authorization/roleDefinitions/delete` operação em todos os o `AssignableScopes`. Das funções incorporadas, apenas *proprietário* e *administrador de acesso de utilizador* é concedido o acesso a esta operação. Para obter mais informações sobre atribuições de funções e gestão de acesso para recursos do Azure, consulte [controlo de acesso em funções do Azure](role-based-access-control-configure.md).

### <a name="request"></a>Pedir
Utilize o **eliminar** método com o URI seguinte:

    https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleDefinitions/{role-definition-id}?api-version={api-version}

No URI, efetue as seguintes substituições para personalizar o seu pedido:

1. Substitua *{âmbito}* com o âmbito em que pretende eliminar a definição de função. Os exemplos seguintes mostram como especificar o âmbito de diferentes níveis:

   * Subscrição: /subscriptions/{targetsubscriptionid}/resourcegroups/{targetresourcegroupname} {id de subscrição}  
   * Grupo de recursos: /subscriptions/{targetsubscriptionid}/resourcegroups/{targetresourcegroupname} {id de subscrição} / resourceGroups/myresourcegroup1  
   * Recurso: /subscriptions/{subscription-id}/resourceGroups/myresourcegroup1/providers/Microsoft.Web/sites/mysite1  
2. Substitua *{id de definição de função}* com o id de definição de função GUID da função personalizada.
3. Substitua *{api-version}* com 2015-07-01.

### <a name="response"></a>Resposta
Código de estado: 200

```
{
  "properties": {
    "roleName": "Virtual Machine Operator",
    "type": "CustomRole",
    "description": "Lets you monitor virtual machines and restart them.",
    "assignableScopes": [
      "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e"
    ],
    "permissions": [
      {
        "actions": [
          "Microsoft.Authorization/*/read",
          "Microsoft.Compute/*/read",
          "Microsoft.Insights/alertRules/*",
          "Microsoft.Network/*/read",
          "Microsoft.Resources/subscriptions/resourceGroups/read",
          "Microsoft.Storage/*/read",
          "Microsoft.Support/*",
          "Microsoft.Compute/virtualMachines/start/action",
          "Microsoft.Compute/virtualMachines/restart/action"
        ],
        "notActions": []
      }
    ],
    "createdOn": "2015-12-16T00:07:02.9236555Z",
    "updatedOn": "2015-12-16T00:07:02.9236555Z",
    "createdBy": "877f0ab8-9c5f-420b-bf88-a1c6c7e2643e",
    "updatedBy": "877f0ab8-9c5f-420b-bf88-a1c6c7e2643e"
  },
  "id": "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e/providers/Microsoft.Authorization/roleDefinitions/0bd62a70-e1b8-4e0b-a7c2-75cab365c95b",
  "type": "Microsoft.Authorization/roleDefinitions",
  "name": "0bd62a70-e1b8-4e0b-a7c2-75cab365c95b"
}

```

## <a name="next-steps"></a>Passos seguintes

[!INCLUDE [role-based-access-control-toc.md](../../includes/role-based-access-control-toc.md)]
