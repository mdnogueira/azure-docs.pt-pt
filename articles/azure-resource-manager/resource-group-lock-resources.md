---
title: "Recursos do Azure para impedir que as alterações de bloqueio | Microsoft Docs"
description: "Impedir que utilizadores atualizar ou eliminar os recursos do Azure críticos aplicando um bloqueio para todos os utilizadores e funções."
services: azure-resource-manager
documentationcenter: 
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: 53c57e8f-741c-4026-80e0-f4c02638c98b
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/27/2017
ms.author: tomfitz
ms.openlocfilehash: d7b091f4a437781547610624007ac1d7f22fed61
ms.sourcegitcommit: 9a61faf3463003375a53279e3adce241b5700879
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/15/2017
---
# <a name="lock-resources-to-prevent-unexpected-changes"></a>Recursos de bloqueio para impedir que as alterações inesperadas 
Como administrador, poderá ter de bloquear uma subscrição, grupo de recursos ou recursos para impedir que outros utilizadores na sua organização acidentalmente eliminem ou modificar a recursos críticos. Pode definir o bloqueio para **CanNotDelete** ou **ReadOnly**. 

* **CanNotDelete** significa que os utilizadores autorizados, podem ainda ler e modificar um recurso, mas não é possível eliminar o recurso. 
* **Só de leitura** significa que os utilizadores autorizados podem ler um recurso, mas não é possível eliminar ou atualizar o recurso. Aplicar esta bloqueio é semelhante ao restringir todos os utilizadores autorizados para as permissões concedidas à **leitor** função. 

## <a name="how-locks-are-applied"></a>Como os bloqueios são aplicados

Ao aplicar um bloqueio num âmbito principal, todos os recursos desse âmbito herdam o bloqueio do mesmo. Recursos mesmo que adicionar mais tarde herdam o bloqueio do principal. O bloqueio mais restritivo na herança tem precedência.

Ao contrário do controlo de acesso baseado em funções, utilize as bloqueios de gestão para aplicar uma restrição em todos os utilizadores e funções. Para saber mais sobre a definição de permissões de utilizadores e funções, consulte [controlo de acesso baseado em funções do Azure](../active-directory/role-based-access-control-configure.md).

Bloqueios de Gestor de recursos são aplicadas apenas a operações acontecer na plane a gestão, que consiste em operações de envio para `https://management.azure.com`. Os bloqueios restringe como recursos executar as suas próprias funções. Alterações de recurso estão limitadas, mas as operações de recurso não estão restringidas. Por exemplo, um bloqueio de só de leitura de uma base de dados do SQL Server impede-o de eliminar ou modificar a base de dados, mas não o impede de criar, atualizar ou eliminar dados na base de dados. Transações de dados permitidas por essas operações não são enviadas para `https://management.azure.com`.

Aplicar **ReadOnly** pode originar resultados inesperados porque algumas operações que parecem como leitura operações, na verdade, necessitam de ações adicionais. Por exemplo, colocar uma **ReadOnly** bloqueio numa conta de armazenamento impede que todos os utilizadores listar as chaves. A lista de operação de chaves é processada através de um pedido POST porque as chaves devolvidas estão disponíveis para operações de escrita. Para obter outro exemplo, colocar uma **ReadOnly** bloqueio de um recurso de serviço de aplicações impede o Explorador de servidores do Visual Studio a apresentação de ficheiros para o recurso porque esse interação requer acesso de escrita.

## <a name="who-can-create-or-delete-locks-in-your-organization"></a>Quem pode criar ou eliminar as bloqueios na sua organização
Para criar ou eliminar as bloqueios de gestão, tem de ter acesso a `Microsoft.Authorization/*` ou `Microsoft.Authorization/locks/*` ações. Das funções incorporadas, apenas **proprietário** e **administrador de acesso de utilizador** concedidas essas ações.

## <a name="portal"></a>Portal
[!INCLUDE [resource-manager-lock-resources](../../includes/resource-manager-lock-resources.md)]

## <a name="template"></a>Modelo
O exemplo seguinte mostra um modelo que cria um bloqueio numa conta de armazenamento. A conta de armazenamento no qual pretende aplicar o bloqueio é fornecida como parâmetro. O nome do bloqueio é criado pelo concatenar o nome de recurso com **/Microsoft.Authorization/** e o nome do bloqueio, neste caso, **myLock**.

O tipo fornecido é específico para o tipo de recurso. Para armazenamento, defina o tipo para "Microsoft.Storage/storageaccounts/providers/locks".

    {
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "lockedResource": {
          "type": "string"
        }
      },
      "resources": [
        {
          "name": "[concat(parameters('lockedResource'), '/Microsoft.Authorization/myLock')]",
          "type": "Microsoft.Storage/storageAccounts/providers/locks",
          "apiVersion": "2015-01-01",
          "properties": {
            "level": "CannotDelete"
          }
        }
      ]
    }

## <a name="powershell"></a>PowerShell
Bloqueio tiver implementado recursos com o Azure PowerShell utilizando o [New-AzureRmResourceLock](/powershell/module/azurerm.resources/new-azurermresourcelock) comando.

Para bloquear um recurso, forneça o nome de recurso, o tipo de recurso e o nome do grupo de recursos.

```powershell
New-AzureRmResourceLock -LockLevel CanNotDelete -LockName LockSite `
  -ResourceName examplesite -ResourceType Microsoft.Web/sites `
  -ResourceGroupName exampleresourcegroup
```

Para bloquear um grupo de recursos, forneça o nome do grupo de recursos.

```powershell
New-AzureRmResourceLock -LockName LockGroup -LockLevel CanNotDelete `
  -ResourceGroupName exampleresourcegroup
```

Para obter informações sobre um bloqueio, utilize [Get-AzureRmResourceLock](/powershell/module/azurerm.resources/get-azurermresourcelock). Para obter todos os bloqueios na sua subscrição, utilize:

```powershell
Get-AzureRmResourceLock
```

Para obter todos os bloqueios para um recurso, utilize:

```powershell
Get-AzureRmResourceLock -ResourceName examplesite -ResourceType Microsoft.Web/sites `
  -ResourceGroupName exampleresourcegroup
```

Para obter todos os bloqueios para um grupo de recursos, utilize:

```powershell
Get-AzureRmResourceLock -ResourceGroupName exampleresourcegroup
```

O Azure PowerShell fornece outros comandos de bloqueios de trabalho, tais como [AzureRmResourceLock conjunto](/powershell/module/azurerm.resources/set-azurermresourcelock) para atualizar um bloqueio e [AzureRmResourceLock remover](/powershell/module/azurerm.resources/remove-azurermresourcelock) para eliminar um bloqueio.

## <a name="azure-cli"></a>CLI do Azure

Bloqueio tiver implementado recursos com a CLI do Azure utilizando o [bloqueio az criar](/cli/azure/lock#create) comando.

Para bloquear um recurso, forneça o nome de recurso, o tipo de recurso e o nome do grupo de recursos.

```azurecli
az lock create --name LockSite --lock-type CanNotDelete \
  --resource-group exampleresourcegroup --resource-name examplesite \
  --resource-type Microsoft.Web/sites
```

Para bloquear um grupo de recursos, forneça o nome do grupo de recursos.

```azurecli
az lock create --name LockGroup --lock-type CanNotDelete \
  --resource-group exampleresourcegroup
```

Para obter informações sobre um bloqueio, utilize [lista de bloqueio az](/cli/azure/lock#list). Para obter todos os bloqueios na sua subscrição, utilize:

```azurecli
az lock list
```

Para obter todos os bloqueios para um recurso, utilize:

```azurecli
az lock list --resource-group exampleresourcegroup --resource-name examplesite \
  --namespace Microsoft.Web --resource-type sites --parent ""
```

Para obter todos os bloqueios para um grupo de recursos, utilize:

```azurecli
az lock list --resource-group exampleresourcegroup
```

CLI do Azure fornece outros comandos para bloqueios de trabalho, tais como [atualização de bloqueio az](/cli/azure/lock#update) para atualizar um bloqueio e [bloqueio az eliminar](/cli/azure/lock#delete) para eliminar um bloqueio.

## <a name="rest-api"></a>API REST
Pode bloquear recursos implementados com o [API REST para bloqueios gestão](https://docs.microsoft.com/rest/api/resources/managementlocks). A API REST permite-lhe criar e eliminar as bloqueios e obter informações sobre as bloqueios existentes.

Para criar um bloqueio, execute:

    PUT https://management.azure.com/{scope}/providers/Microsoft.Authorization/locks/{lock-name}?api-version={api-version}

O âmbito pode ser uma subscrição, o grupo de recursos ou o recurso. O nome de bloqueio é que pretende para chamar o bloqueio. Para uma versão de api, utilize **2015-01-01**.

O pedido de incluir um objeto JSON que especifica as propriedades para o bloqueio.

    {
      "properties": {
        "level": "CanNotDelete",
        "notes": "Optional text notes."
      }
    } 

## <a name="next-steps"></a>Passos seguintes
* Para obter mais informações sobre como trabalhar com as bloqueios de recursos, consulte [bloqueio baixo Your Azure recursos](http://blogs.msdn.com/b/cloud_solution_architect/archive/2015/06/18/lock-down-your-azure-resources.aspx)
* Para saber mais sobre como organizar logicamente os recursos, consulte [utilizando etiquetas para organizar os recursos](resource-group-using-tags.md)
* Para alterar o grupo de recursos, um recurso reside no, consulte [mover recursos para o novo grupo de recursos](resource-group-move-resources.md)
* Pode aplicar restrições e convenções a sua subscrição com políticas personalizadas. Para obter mais informações, consulte [o que é a política do Azure?](../azure-policy/azure-policy-introduction.md).
* Para obter documentação de orientação sobre como as empresas podem utilizar o Resource Manager para gerir subscrições de forma eficaz, consulte [Azure enterprise scaffold - prescriptive subscription governance (Andaime empresarial do Azure - governação de subscrições prescritivas)](resource-manager-subscription-governance.md).

