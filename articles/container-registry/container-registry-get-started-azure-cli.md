---
title: "Guia de introdução - criar um registo de Docker privado no Azure com a CLI do Azure"
description: Saiba mais rapidamente criar um registo de contentor do Docker privado com a CLI do Azure.
services: container-registry
documentationcenter: 
author: neilpeterson
manager: timlt
editor: tysonn
tags: 
keywords: 
ms.assetid: 29e20d75-bf39-4f7d-815f-a2e47209be7d
ms.service: container-registry
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/16/2017
ms.author: nepeters
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 6b3fb9a3ea090f0083e8f113ddf13312fe42b59a
ms.sourcegitcommit: 9c3150e91cc3075141dc2955a01f47040d76048a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/26/2017
---
# <a name="create-a-container-registry-using-the-azure-cli"></a>Criar um registo de contentores com a CLI do Azure

O Azure Container Registry é um serviço de registo do contentor Docker gerido, utilizado para armazenar imagens de contentor do Docker privadas. Este detalhes guia criar uma instância de registo de contentor Azure utilizando a CLI do Azure.

Este guia de introdução requer que está a executar a CLI do Azure versão 2.0.20 ou posterior. Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar, veja [instalar o Azure CLI 2.0](/cli/azure/install-azure-cli).

Também tem de ter Docker instalado localmente. O Docker disponibiliza pacotes que o configuram facilmente em qualquer sistema [Mac](https://docs.docker.com/docker-for-mac/), [Windows](https://docs.docker.com/docker-for-windows/) ou [Linux](https://docs.docker.com/engine/installation/#supported-platforms).

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Crie um grupo de recursos com o comando [az group create](/cli/azure/group#create). Um grupo de recursos do Azure é um contentor lógico no qual os recursos do Azure são implementados e geridos.

O exemplo seguinte cria um grupo de recursos com o nome *myResourceGroup* na localização *eastus*.

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

## <a name="create-a-container-registry"></a>Criar um registo de contentores

Este guia de introdução, criamos um *básico* registo. Registo de contentor do Azure está disponível em vários SKUs diferentes, brevemente descritos na seguinte tabela. Para obter detalhes expandidos em cada um, consulte [registo de contentor SKUs](container-registry-skus.md).

[!INCLUDE [container-registry-sku-matrix](../../includes/container-registry-sku-matrix.md)]

Crie uma instância do ACR com o comando [az acr create](/cli/azure/acr#create).

O nome do registo **têm de ser exclusivos**. No exemplo seguinte *myContainerRegistry007* é utilizado. Atualize esta para um valor exclusivo.

```azurecli
az acr create --name myContainerRegistry007 --resource-group myResourceGroup --sku Basic
```

Quando o registo é criado, o resultado é semelhante ao seguinte:

```json
{
  "adminUserEnabled": false,
  "creationDate": "2017-09-08T22:32:13.175925+00:00",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.ContainerRegistry/registries/myContainerRegistry007",
  "location": "eastus",
  "loginServer": "myContainerRegistry007.azurecr.io",
  "name": "myContainerRegistry007",
  "provisioningState": "Succeeded",
  "resourceGroup": "myResourceGroup",
  "sku": {
    "name": "Basic",
    "tier": "Basic"
  },
  "storageAccount": {
    "name": "mycontainerregistr223140"
  },
  "tags": {},
  "type": "Microsoft.ContainerRegistry/registries"
}
```

Em todo o resto deste guia de introdução, utilizamos `<acrname>` como um marcador de posição para o nome do registo de contentor.

## <a name="log-in-to-acr"></a>Inicie sessão no ACR

Antes de emitir e solicitar imagens de contentor, tem de iniciar sessão na instância do ACR. Para tal, utilize o comando [az acr login](/cli/azure/acr#login).

```azurecli
az acr login --name <acrname>
```

O comando devolve uma mensagem “Início de sessão com êxito” depois de concluir.

## <a name="push-image-to-acr"></a>Imagem de push para o ACR

Para enviar uma imagem para um registo de contentor do Azure, primeiro tem de ter uma imagem. Se for necessário, execute o seguinte comando para extrair uma imagem pré-criadas do Hub de Docker.

```bash
docker pull microsoft/aci-helloworld
```

A imagem tem de ser etiquetados com o nome de servidor de início de sessão ACR. Execute o seguinte comando para devolver o nome do servidor de início de sessão da instância ACR.

```azurecli
az acr list --resource-group myResourceGroup --query "[].{acrLoginServer:loginServer}" --output table
```

Marcar a imagem a utilizar o [tag de docker](https://docs.docker.com/engine/reference/commandline/tag/) comando. Substitua  *<acrLoginServer>*  com o nome do servidor de início de sessão da sua instância ACR.

```bash
docker tag microsoft/aci-helloworld <acrLoginServer>/aci-helloworld:v1
```

Por último, utilize [docker push](https://docs.docker.com/engine/reference/commandline/push/) para enviar a imagem para a instância ACR. Substitua  *<acrLoginServer>*  com o nome do servidor de início de sessão da sua instância ACR.

```bash
docker push <acrLoginServer>/aci-helloworld:v1
```

## <a name="list-container-images"></a>Listar imagens de contentor

O exemplo seguinte lista os repositórios de um registo:

```azurecli
az acr repository list -n <acrname> -o table
```

Saída:

```bash
Result
----------------
aci-helloworld
```

O exemplo seguinte lista as etiquetas no **aci olámundo** repositório.

```azurecli
az acr repository show-tags -n <acrname> --repository aci-helloworld -o table
```

Saída:

```bash
Result
--------
v1
```

## <a name="clean-up-resources"></a>Limpar recursos

Quando já não é necessário, pode utilizar o [eliminação do grupo de az](/cli/azure/group#delete) comando para remover o grupo de recursos, instância ACR e todas as imagens de contentor.

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Passos seguintes

Este guia de introdução, criou um registo de contentor do Azure com a CLI do Azure. Se gostaria de utilizar o registo de contentor do Azure com instâncias de contentor do Azure, continue para o tutorial de instâncias de contentor do Azure.

> [!div class="nextstepaction"]
> [Tutorial de instâncias de contentor do Azure](../container-instances/container-instances-tutorial-prepare-app.md)