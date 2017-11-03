---
title: "Implementar em instâncias de contentor do Azure a partir do registo de contentor do Azure | Documentos do Azure"
description: "Implementar em instâncias de contentor do Azure a partir do registo de contentor do Azure"
services: container-instances
documentationcenter: 
author: seanmck
manager: timlt
editor: 
tags: 
keywords: 
ms.assetid: 
ms.service: container-instances
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/02/2017
ms.author: seanmck
ms.custom: mvc
ms.openlocfilehash: aa1c4ea379c10dff246e2f924a345f9fa444aa64
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="deploy-to-azure-container-instances-from-the-azure-container-registry"></a>Implementar em instâncias de contentor do Azure a partir do registo de contentor do Azure

O registo de contentor do Azure é um registo privado baseado no Azure, para imagens de contentor do Docker. Este artigo abrange como implementar imagens do contentor armazenadas no registo de contentor do Azure para instâncias de contentor do Azure.

## <a name="using-the-azure-cli"></a>Com a CLI do Azure

A CLI do Azure inclui comandos para criar e gerir contentores em instâncias de contentor do Azure. Se especificar uma imagem de privada no `create` comando, também pode especificar o registo imagem palavra-passe necessária para autenticar com o registo de contentor.

```azurecli-interactive
az container create --name myprivatecontainer --image mycontainerregistry.azurecr.io/mycontainerimage:v1 --registry-password myRegistryPassword --resource-group myresourcegroup
```

O `create` comando também suporta a especificação de `registry-login-server` e `registry-username`. No entanto, o servidor de início de sessão para o registo de contentor do Azure está sempre *registryname*. azurecr.io e o nome de utilizador predefinido é *registryname*, por isso, estes valores são inferidos a partir do nome da imagem se não explicitamente fornecido.

## <a name="using-an-azure-resource-manager-template"></a>Utilizar um modelo Azure Resource Manager

Pode especificar as propriedades do seu registo de contentor do Azure num modelo Azure Resource Manager, incluindo o `imageRegistryCredentials` propriedade na definição de grupo do contentor:

```json
"imageRegistryCredentials": [
  {
    "server": "imageRegistryLoginServer",
    "username": "imageRegistryUsername",
    "password": "imageRegistryPassword"
  }
]
```

Para evitar a armazenar a palavra-passe de registo do contentor diretamente no modelo, recomendamos que armazene-lo como um segredo no [Cofre de chaves do Azure](../key-vault/key-vault-manage-with-cli2.md) e referencie-o em modelo, utilizando o [integração nativa entre o Azure Gestor de recursos e o Cofre de chaves](../azure-resource-manager/resource-manager-keyvault-parameter.md).

## <a name="using-the-azure-portal"></a>Utilizar o portal do Azure

Se manter a imagens de contentor no registo de contentor do Azure, pode facilmente criar um contentor em instâncias de contentor do Azure no portal do Azure.

1. No portal do Azure, navegue para o registo de contentor.

2. Escolha repositórios.

    ![O menu de registo de contentor do Azure no portal do Azure][acr-menu]

3. Escolha o repositório de que pretende implementar a partir de.

4. Clique com botão direito a etiqueta para a imagem de contentor que pretende implementar.

    ![Menu de contexto para iniciar o contentor com instâncias de contentor do Azure][acr-runinstance-contextmenu]

5. Introduza um nome para o contentor e um nome para o grupo de recursos. Também pode alterar os valores predefinidos se desejar.

    ![Criar o menu para instâncias de contentor do Azure][acr-create-deeplink]

6. Uma vez concluída a implementação, pode navegar para o grupo de contentor do painel de notificações para localizar o respetivo endereço IP e outras propriedades.

    ![Vista de detalhes para o grupo do contentor de instâncias de contentor do Azure][aci-detailsview]

## <a name="next-steps"></a>Passos seguintes

Saiba como criar contentores, emiti-las para um registo de contentor privada e implementá-las para instâncias de contentor do Azure por [concluir o tutorial](container-instances-tutorial-prepare-app.md).

<!-- IMAGES -->
[acr-menu]: ./media/container-instances-using-azure-container-registry/acr-menu.png

[acr-runinstance-contextmenu]: ./media/container-instances-using-azure-container-registry/acr-runinstance-contextmenu.png

[acr-create-deeplink]: ./media/container-instances-using-azure-container-registry/acr-create-deeplink.png

[aci-detailsview]: ./media/container-instances-using-azure-container-registry/aci-detailsview.png
