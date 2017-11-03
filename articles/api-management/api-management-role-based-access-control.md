---
title: "Como utilizar o controlo de acesso baseado em funções na API Management do Azure | Microsoft Docs"
description: "Saiba como utilizar as funções incorporadas e criar funções personalizadas na API Management do Azure"
services: api-management
documentationcenter: 
author: vladvino
manager: erikre
editor: 
ms.assetid: 364cd53e-88fb-4301-a093-f132fa1f88f5
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/19/2017
ms.author: apimpm
ms.openlocfilehash: a3872aae3a9eb8da0b881ec9388f54546e84b08b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-use-role-based-access-control-in-azure-api-management"></a>Como utilizar o controlo de acesso baseado em funções na API Management do Azure
Gestão de API do Azure baseia-se no controlo de acesso de em funções do Azure (RBAC) para ativar a gestão de acesso detalhada para serviços de gestão de API e entidades (por exemplo, APIs e políticas). Este artigo fornece uma descrição geral das funções incorporadas e personalizadas na API Management. Para obter mais informações sobre a gestão de acesso no portal do Azure, consulte [introdução à gestão de acesso no portal do Azure](https://azure.microsoft.com/documentation/articles/role-based-access-control-what-is/).

## <a name="built-in-roles"></a>Funções incorporadas
Gestão de API atualmente fornece três funções incorporadas e irá adicionar duas funções mais num futuro próximo. Estas funções podem ser atribuídas em âmbitos diferentes, incluindo a subscrição, o grupo de recursos e instância de API Management individuais. Por exemplo, se atribuir a função "Leitor do serviço de gestão de API do Azure" para um utilizador ao nível do grupo de recursos, o utilizador tem acesso de leitura para todas as instâncias de gestão de API no interior do grupo de recursos. 

A tabela seguinte fornece breves descrições das funções incorporadas. Pode atribuir estas funções utilizando o portal do Azure ou outras ferramentas, incluindo ao Azure [PowerShell](https://docs.microsoft.com/azure/active-directory/role-based-access-control-manage-access-powershell), [CLI do Azure](https://docs.microsoft.com/azure/active-directory/role-based-access-control-manage-access-azure-cli), e [REST API](https://docs.microsoft.com/azure/active-directory/role-based-access-control-manage-access-rest). Para obter mais informações sobre como atribuir funções incorporadas, consulte [utilize atribuições de funções para gerir o acesso aos recursos da sua subscrição do Azure](https://azure.microsoft.com/documentation/articles/role-based-access-control-what-is/).

| Função          | Acesso de leitura<sup>[1]</sup> | Acesso de escrita<sup>[2]</sup> | Criação do serviço, eliminação, dimensionamento, VPN e a configuração de domínio personalizado | Acesso ao portal do publicador legado | Descrição
| ------------- | ---- | ---- | ---- | ---- | ---- | ---- |
| Contribuinte de serviço de gestão de API do Azure | ✓ | ✓ | ✓ | ✓ | Superutilizador. Tem acesso total de CRUD nos serviços de gestão de API e entidades (por exemplo, APIs e políticas). Tem acesso ao portal do publicador legado. |
| Leitor de serviço de gestão de API do Azure | ✓ | | || Tem acesso só de leitura para os serviços de gestão de API e as entidades. |
| Operador de serviço de gestão de API do Azure | ✓ | | ✓ | | Pode gerir os serviços de gestão de API, mas não entidades.|
| Editor de serviço de gestão de API do Azure<sup>*</sup> | ✓ | ✓ | |  | Pode gerir as entidades de API Management, mas não os serviços.|
| Gestor de conteúdos de gestão de API do Azure<sup>*</sup> | ✓ | | | ✓ | Pode gerir o portal do programador. Acesso só de leitura para os serviços e as entidades.|

<sup>[1] acesso de leitura para os serviços de gestão de API e entidades (por exemplo, APIs e políticas).</sup>

<sup>[2] o acesso de escrita para serviços de gestão de API e entidades, exceto as seguintes operações: instância de criação, eliminação e dimensionamento; Configuração de VPN e a configuração do domínio personalizado.</sup>

<sup>\*A função de Editor do serviço estará disponível depois de iremos migrar todos os admin da IU do portal do publicador existente ao portal do Azure. A função de Gestor de conteúdos estará disponível depois do portal do publicador é refatorizado para conter apenas as funcionalidades relacionadas com a gestão o portal do programador.</sup>  

## <a name="custom-roles"></a>Funções personalizadas
Se nenhuma das funções incorporadas as suas necessidades específicas, funções personalizadas podem ser criadas para fornecer gestão de acesso mais granular para entidades de API Management. Por exemplo, pode criar uma função personalizada que tem acesso só de leitura para um serviço de API Management, mas só tem acesso de escrita para uma API específica. Para saber mais sobre as funções personalizadas, consulte [funções personalizadas no Azure RBAC](https://docs.microsoft.com/azure/active-directory/role-based-access-control-custom-roles). 

Quando cria uma função personalizada, é mais fácil começar a utilizar uma das funções incorporadas. Editar os atributos a adicionar **ações**, **NotActions**, ou **AssignableScopes**e, em seguida, guarde as alterações como uma nova função. O exemplo seguinte começa com a função "Leitor do serviço de gestão de API do Azure" e cria uma função personalizada denominada "Editor de API de calculadora." Pode atribuir a função personalizada a uma API específica. Por conseguinte, esta função só tem acesso a essa API. 

```
$role = Get-AzureRmRoleDefinition "API Management Service Reader Role"
$role.Id = $null
$role.Name = 'Calculator API Contributor'
$role.Description = 'Has read access to Contoso APIM instance and write access to the Calculator API.'
$role.Actions.Add('Microsoft.ApiManagement/service/apis/write')
$role.AssignableScopes.Clear()
$role.AssignableScopes.Add('/subscriptions/<subscription ID>/resourceGroups/<resource group name>/providers/Microsoft.ApiManagement/service/<service name>/apis/<api ID>')
New-AzureRmRoleDefinition -Role $role
New-AzureRmRoleAssignment -ObjectId <object ID of the user account> -RoleDefinitionName 'Calculator API Contributor' -Scope '/subscriptions/<subscription ID>/resourceGroups/<resource group name>/providers/Microsoft.ApiManagement/service/<service name>/apis/<api ID>'
```

O [operações de fornecedor de recursos do Azure Resource Manager](../active-directory/role-based-access-control-resource-provider-operations.md#microsoftapimanagement) artigo contém a lista de permissões que podem ser concedidas ao nível da API Management.

## <a name="watch-a-video-overview"></a>Veja uma descrição geral do vídeo

Para obter mais informações, pode ver o [controlo de acesso baseado em funções na API Management](https://channel9.msdn.com/Blogs/AzureApiMgmt/Role-Based-Access-Control-in-API-Management/player) vídeo.

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre o controlo de acesso baseado em funções no Azure, consulte os artigos seguintes:
  * [Get started with access management in the Azure portal (Introdução à gestão do acesso no portal do Azure)](https://azure.microsoft.com/documentation/articles/role-based-access-control-what-is/)
  * [Utilize atribuições de funções para gerir o acesso aos recursos de subscrição do Azure](https://azure.microsoft.com/documentation/articles/role-based-access-control-what-is/)
  * [Funções personalizadas no Azure RBAC](https://docs.microsoft.com/azure/active-directory/role-based-access-control-custom-roles)
  * [Operações de fornecedor de recursos do Azure Resource Manager](../active-directory/role-based-access-control-resource-provider-operations.md#microsoftapimanagement)

