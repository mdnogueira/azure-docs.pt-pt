---
title: Gerir os recursos com a CLI do Azure | Microsoft Docs
description: Utilize a Interface de linha de comandos (CLI do Azure) para gerir recursos do Azure e grupos
editor: 
manager: timlt
documentationcenter: 
author: tfitzmac
services: azure-resource-manager
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: vm-multiple
ms.devlang: na
ms.topic: article
ms.date: 10/06/2017
ms.author: tomfitz
ms.openlocfilehash: c68f2a8b6e18dc2d51d8bbb5cd05bc037dc2fadb
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="use-the-azure-cli-to-manage-azure-resources-and-resource-groups"></a>Utilizar a CLI do Azure para gerir recursos do Azure e os grupos de recursos

Neste artigo, irá aprender a gerir as suas soluções com a CLI do Azure e o Azure Resource Manager. Se não estiver familiarizado com o Resource Manager, consulte [descrição geral do Gestor de recursos](resource-group-overview.md). Este artigo incida no tarefas de gestão. Irá:

1. Criar um grupo de recursos
2. Adicione um recurso para o grupo de recursos
3. Adicionar uma etiqueta para o recurso
4. Recursos com base nos valores de etiqueta ou nomes de consulta
5. Aplicar e remover um bloqueio num recurso
6. Eliminar um grupo de recursos

Este artigo mostra como implementar um modelo do Resource Manager para a sua subscrição. Para essas informações, consulte [implementar recursos com modelos do Resource Manager e a CLI do Azure](resource-group-template-deploy-cli.md).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Para instalar e utilizar a CLI localmente, consulte [instalar o Azure CLI 2.0](/cli/azure/install-azure-cli).

## <a name="set-subscription"></a>Conjunto de subscrição

Se tiver mais do que uma subscrição, pode mudar para uma subscrição diferente. Em primeiro lugar, vamos ver todas as subscrições para a sua conta.

```azurecli-interactive
az account list
```

Devolve uma lista das suas subscrições ativadas e desativadas.

```json
[
  {
    "cloudName": "AzureCloud",
    "id": "<guid>",
    "isDefault": true,
    "name": "Example Subscription One",
    "registeredProviders": [],
    "state": "Enabled",
    "tenantId": "<guid>",
    "user": {
      "name": "example@contoso.org",
      "type": "user"
    }
  },
  ...
]
```

Tenha em atenção que uma subscrição está marcada como predefinição. Esta subscrição é o contexto atual para as operações. Para mudar para uma subscrição diferente, forneça o nome de subscrição com o **az conta conjunto** comando.

```azurecli-interactive
az account set -s "Example Subscription Two"
```

Para mostrar o contexto de subscrição atual, utilize **mostrar de conta az** sem um parâmetro:

```azurecli-interactive
az account show
```

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Antes de implementar quaisquer recursos na sua subscrição, tem de criar um grupo de recursos que irá conter os recursos.

Para criar um grupo de recursos, utilize o **criar grupo az** comando. O comando utiliza o **nome** parâmetro para especificar um nome para o grupo de recursos e o **localização** parâmetro para especificar a localização.

```azurecli-interactive
az group create --name TestRG1 --location "South Central US"
```

O resultado é o seguinte formato:

```json
{
  "id": "/subscriptions/<subscription-id>/resourceGroups/TestRG1",
  "location": "southcentralus",
  "managedBy": null,
  "name": "TestRG1",
  "properties": {
    "provisioningState": "Succeeded"
  },
  "tags": null
}
```

Se precisar de obter o grupo de recursos mais tarde, utilize o seguinte comando:

```azurecli-interactive
az group show --name TestRG1
```

Para obter todos os grupos de recursos na sua subscrição, utilize:

```azurecli-interactive
az group list
```

## <a name="add-resources-to-a-resource-group"></a>Adicionar recursos a um grupo de recursos

Para adicionar um recurso para o grupo de recursos, pode utilizar o **criar recursos az** comando ou um comando que é específico para o tipo de recurso que está a criar (como **criar conta de armazenamento az**). Pode encontrar mais fáceis de utilizar um comando que é específico para um tipo de recurso, pois inclui os parâmetros para as propriedades que são necessários para o novo recurso. Para utilizar **criar recursos az**, tem de saber todas as propriedades para definir sem pedidos para os mesmos.

No entanto, a adição de um recurso através do script poderá causar confusão futura porque o novo recurso não existe um modelo do Resource Manager. Modelos permitem-lhe implementar repetidamente e fiável a sua solução.

O comando seguinte cria uma conta de armazenamento. Em vez de utilizar o nome apresentado no exemplo, forneça um nome exclusivo para a conta de armazenamento. O nome tem de ter entre 3 e 24 carateres de comprimento e utilizar apenas números e letras minúsculas. Se utilizar o nome apresentado no exemplo, receberá um erro porque este nome já se encontra em utilização.

```azurecli-interactive
az storage account create -n myuniquestorage -g TestRG1 -l westus --sku Standard_LRS
```

Se precisar de obter este recurso mais tarde, utilize o seguinte comando:

```azurecli-interactive
az storage account show --name myuniquestorage --resource-group TestRG1
```

## <a name="add-a-tag"></a>Adicionar uma etiqueta

As etiquetas permitem-lhe organizar os recursos, de acordo com as propriedades diferentes. Por exemplo, pode ter vários recursos em grupos de recursos diferente que pertencem ao mesmo departamento. Pode aplicar uma etiqueta de departamento e o valor a esses recursos marcá-los como pertencentes à mesma categoria. Em alternativa, pode marcá-se um recurso é utilizado num ambiente de teste ou de produção. Neste artigo, aplicar etiquetas a apenas um recurso, mas no seu ambiente provavelmente faz sentido aplicar etiquetas a todos os seus recursos.

O comando seguinte aplica-se duas etiquetas à sua conta de armazenamento:

```azurecli-interactive
az resource tag --tags Dept=IT Environment=Test -g TestRG1 -n myuniquestorage --resource-type "Microsoft.Storage/storageAccounts"
```

As etiquetas são atualizadas como um único objeto. Para adicionar uma etiqueta a um recurso que já inclua etiquetas, obtenha primeiro as etiquetas existentes. Adicionar a nova tag de objeto que contém as etiquetas existentes e volte a aplicar todas as etiquetas ao recurso.

```azurecli-interactive
jsonrtag=$(az resource show -g TestRG1 -n myuniquestorage --resource-type "Microsoft.Storage/storageAccounts" --query tags)
rt=$(echo $jsonrtag | tr -d '"{},' | sed 's/: /=/g')
az resource tag --tags $rt Project=Redesign -g TestRG1 -n myuniquestorage --resource-type "Microsoft.Storage/storageAccounts"
```

## <a name="search-for-resources"></a>Procurar recursos

Utilize o **lista de recursos de az** comando para obter recursos para condições de pesquisa diferentes.

* Para obter um recurso de por nome, forneça o **nome** parâmetro:

  ```azurecli-interactive
  az resource list -n myuniquestorage
  ```

* Para obter todos os recursos num grupo de recursos, forneça o **grupo de recursos** parâmetro:

  ```azurecli-interactive
  az resource list --resource-group TestRG1
  ```

* Para obter todos os recursos com um nome de tag e um valor, forneça o **tag** parâmetro:

  ```azurecli-interactive
  az resource list --tag Dept=IT
  ```

* Para todos os recursos com um tipo de recurso específico, forneça o **tipo de recurso** parâmetro:

  ```azurecli-interactive
  az resource list --resource-type "Microsoft.Storage/storageAccounts"
  ```

## <a name="get-resource-id"></a>Obter ID de recurso

Vários comandos demorar um ID de recurso como um parâmetro. Para obter o ID de para um recurso e arquivo numa variável, utilize:

```azurecli-interactive
webappID=$(az resource show -g exampleGroup -n exampleSite --resource-type "Microsoft.Web/sites" --query id --output tsv)
```

## <a name="lock-a-resource"></a>Um recurso de bloqueio

Quando precisar de certificar-se de um recurso crítico não acidentalmente eliminado ou modificado, aplicar um bloqueio para o recurso. Pode especificar um um **CanNotDelete** ou **ReadOnly**.

Para criar ou eliminar as bloqueios de gestão, tem de ter acesso a `Microsoft.Authorization/*` ou `Microsoft.Authorization/locks/*` ações. Das funções incorporadas, apenas o proprietário e o administrador de acesso de utilizador são concedidas essas ações.

Para aplicar um bloqueio, utilize o seguinte comando:

```azurecli-interactive
az lock create --lock-type CanNotDelete --resource-name myuniquestorage --resource-group TestRG1 --resource-type Microsoft.Storage/storageAccounts --name storagelock
```

Não é possível eliminar o recurso bloqueado no exemplo anterior até que o bloqueio é removido. Para remover um bloqueio, utilize:

```azurecli-interactive
az lock delete --name storagelock --resource-group TestRG1 --resource-type Microsoft.Storage/storageAccounts --resource-name myuniquestorage
```

Para obter mais informações sobre as bloqueios de definição, consulte [bloquear recursos com o Azure Resource Manager](resource-group-lock-resources.md).

## <a name="remove-resources-or-resource-group"></a>Remover recursos ou um grupo de recursos
Pode remover um recurso ou grupo de recursos. Quando remove um grupo de recursos, tem também de remover todos os recursos nesse grupo de recursos.

* Para eliminar um recurso do grupo de recursos, utilize o comando de eliminação para o tipo de recurso que serão eliminados. O comando elimina os recursos, mas não eliminar o grupo de recursos.

  ```azurecli-interactive
  az storage account delete -n myuniquestorage -g TestRG1
  ```

* Para eliminar um grupo de recursos e todos os respetivos recursos, utilize o **eliminação do grupo de az** comando.

  ```azurecli-interactive
  az group delete -n TestRG1
  ```

Para ambos os comandos, é-lhe pedido para confirmar que pretende remover o recurso ou grupo de recursos.

## <a name="next-steps"></a>Passos seguintes
* Para saber mais sobre como criar modelos do Resource Manager, consulte [criação de modelos do Azure Resource Manager](resource-group-authoring-templates.md).
* Para saber mais sobre a implementação de modelos, consulte [implementar uma aplicação com o modelo do Azure Resource Manager](resource-group-template-deploy-cli.md).
* Pode mover recursos existentes para um novo grupo de recursos. Para obter exemplos, consulte [mover recursos para o novo grupo de recursos ou subscrição](resource-group-move-resources.md).
* Para obter documentação de orientação sobre como as empresas podem utilizar o Resource Manager para gerir subscrições de forma eficaz, consulte [Azure enterprise scaffold - prescriptive subscription governance (Andaime empresarial do Azure - governação de subscrições prescritivas)](resource-manager-subscription-governance.md).