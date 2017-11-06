---
title: "Criar aplicações geridas do Azure com a CLI do Azure | Microsoft Docs"
description: "Mostra como criar uma aplicação gerida do Azure que se destina aos membros da sua organização."
services: azure-resource-manager
author: tfitzmac
manager: timlt
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.date: 10/20/2017
ms.author: tomfitz
ms.openlocfilehash: e104778db445d078ffca7a9ddb9d28fc84a93c81
ms.sourcegitcommit: 3ab5ea589751d068d3e52db828742ce8ebed4761
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/27/2017
---
# <a name="create-and-deploy-an-azure-managed-application-with-azure-cli"></a>Criar e implementar uma aplicação gerida do Azure com a CLI do Azure

Este artigo disponibiliza uma introdução ao funcionamento das aplicações geridas. Começa por adicionar uma definição de aplicação gerida a um catálogo interno de utilizadores da sua organização. Depois, implementa-a na sua subscrição. Para simplificar a introdução, já criámos os ficheiros para a aplicação gerida. Um ficheiro define a infraestrutura da solução. Um segundo ficheiro define a interface de utilizador para implementar a aplicação gerida através do portal. Estes ficheiros estão disponíveis através do GitHub. Pode aprender a criar estes ficheiros no tutorial [Criar uma aplicação de catálogo de serviço](publish-service-catalog-app.md).

Quando tiver terminado, terá três grupos de recursos que contêm as diferentes partes da aplicação gerida.

| Grupo de recursos | Contains | Descrição |
| -------------- | -------- | ----------- |
| appDefinitionGroup | A definição da aplicação gerida. | O publicador cria este grupo de recursos e a definição da aplicação gerida. Qualquer pessoa com acesso à definição da aplicação gerida pode implementá-la. |
| applicationGroup | A instância da aplicação gerida. | O consumidor cria este grupo de recursos e a instância da aplicação gerida. O consumidor pode atualizar a aplicação gerida através desta instância. |
| infrastructureGroup | Os recursos para a aplicação gerida. | Este grupo de recursos é criado automaticamente quando é criada a aplicação gerida. O publicador tem acesso a este grupo de recursos para gerir a aplicação. O consumidor tem acesso limitado ao grupo de recursos. |

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-a-resource-group-for-definition"></a>Criar um grupo de recursos para a definição

A definição da sua aplicação gerida existe num grupo de recursos. Um grupo de recursos é uma coleção lógica na qual os recursos do Azure são implementados e geridos.

Para criar um grupo de recursos, utilize o comando seguinte:

```azurecli-interactive
az group create --name appDefinitionGroup --location westcentralus
```

## <a name="create-the-managed-application-definition"></a>Criar a definição da aplicação gerida

Ao definir a aplicação gerida, é selecionado um utilizador, um grupo ou uma aplicação que gere os recursos em nome do consumidor. Esta identidade tem permissões no grupo de recursos gerido, de acordo com a função que é atribuída. Normalmente, para gerir os recursos, é criado um grupo do Azure Active Directory. No entanto, neste artigo, utilize a sua própria identidade.

Para obter o ID de objeto da sua identidade, indique o seu nome principal de utilizador no comando seguinte:

```azurecli-interactive
userid=$(az ad user show --upn-or-object-id example@contoso.org --query objectId --output tsv)
```

Em seguida, precisa do ID da definição da função da função incorporada RBAC a que pretende conceder acesso ao utilizador. O comando seguinte mostra como obter o ID da definição da função da função Proprietário:

```azurecli-interactive
roleid=$(az role definition list --name Owner --query [].name --output tsv)
```

Agora, crie o recurso de definição da aplicação gerida. A aplicação gerida contém apenas uma conta de armazenamento.

```azurecli-interactive
az managedapp definition create \
  --name "ManagedStorage" \
  --location "westcentralus" \
  --resource-group appDefinitionGroup \
  --lock-level ReadOnly \
  --display-name "Managed Storage Account" \
  --description "Managed Azure Storage Account" \
  --authorizations "$userid:$roleid" \
  --package-file-uri "https://raw.githubusercontent.com/Azure/azure-managedapp-samples/master/samples/201-managed-storage-account/managedstorage.zip"
```

Após a conclusão do comando, terá uma definição de aplicação gerida no seu grupo de recursos. 

Alguns dos parâmetros utilizados no exemplo anterior são:

* **resource-group**: O nome do grupo de recursos em que a definição da aplicação gerida é criada.
* **lock-level**: O tipo de bloqueio imposto no grupo de recursos gerido. Impede o cliente de realizar operações não desejadas neste grupo. Atualmente, o único nível de bloqueio suportado é ReadOnly. Quando é especificado ReadOnly, o cliente só consegue ler os recursos presentes no grupo de recursos gerido.
* **authorizations**: descreve o ID de principal e o ID da definição da função utilizados para conceder permissões ao grupo de recursos gerido. É especificado com formato `<principalId>:<roleDefinitionId>`. Também podem ser indicados vários valores para esta propriedade. Se forem necessários vários valores, devem ser especificados no formato `<principalId1>:<roleDefinitionId1> <principalId2>:<roleDefinitionId2>`. Os vários valores são separados por espaço.
* **package-file-uri**: a localização de um pacote .zip que contém os ficheiros necessários. No mínimo, o pacote contém os ficheiros **mainTemplate.json** e **createUiDefinition.json**. **mainTemplate.json** define os recursos do Azure que são aprovisionados como parte da aplicação gerida. O modelo é igual aos modelos normais do Resource Manager. **createUiDefinition.json** gera a interface de utilizador para os utilizadores que criem a aplicação gerida através do portal.

## <a name="create-resource-group-for-managed-application"></a>Criar grupo de recursos para aplicação gerida

Agora, está pronto para implementar a aplicação gerida. 

Para conter a aplicação gerida implementada, precisa de um grupo de recursos. Utilize **westcentralus** para a localização.

```azurecli-interactive
az group create --name applicationGroup --location westcentralus
```

## <a name="deploy-the-managed-application"></a>Implementar a aplicação gerida

A aplicação é implementada com os comandos seguintes:

```azurecli-interactive
appid=$(az managedapp definition show --name ManagedStorage --resource-group appDefinitionGroup --query id --output tsv)
subid=$(az account show --query id --output tsv)
managedGroupId=/subscriptions/$subid/resourceGroups/infrastructureGroup

az managedapp create \
  --name storageApp \
  --location "westcentralus" \
  --kind "Servicecatalog" \
  --resource-group applicationGroup \
  --managedapp-definition-id $appid \
  --managed-rg-id $managedGroupId \
  --parameters "{\"storageAccountNamePrefix\": {\"value\": \"<your-prefix>\"}, \"storageAccountType\": {\"value\": \"Standard_LRS\"}}"
```

Alguns dos parâmetros utilizados no exemplo anterior são:

* **managedapp-definition-id**: o ID da definição que criou anteriormente neste artigo.
* **managed-rg-id**: o ID do grupo de recursos relativo aos recursos associados à aplicação gerida. O comando cria este grupo de recursos. **Não pode existir antes da execução do comando**. O grupo é gerido pelo publicador. 
* **resource-group**: o grupo de recursos em que o recurso da aplicação gerida é criado.
* **parameters**: os parâmetros que são necessários para os recursos associados à aplicação gerida.

Após a conclusão da implementação, vai ver que a aplicação gerida é criada em applicationGroup. O recurso storageAccount é criado em infrastructureGroup.

## <a name="next-steps"></a>Passos seguintes

* Para obter uma introdução às aplicações geridas, veja [Descrição geral das aplicações geridas](overview.md).
* Para obter exemplos dos ficheiros, veja [Managed application samples](https://github.com/Azure/azure-managedapp-samples/tree/master/samples) (Exemplos de aplicações geridas).
* Para obter informações sobre a publicação de aplicações geridas no Azure Marketplace, veja [Criar aplicação do Marketplace](publish-marketplace-app.md).
* Para saber como criar um ficheiro de definição de IU para uma aplicação gerida, veja [Introdução a CreateUiDefinition](create-uidefinition-overview.md).
