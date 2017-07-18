---
title: Criar registo privado de contentor Docker - CLI do Azure | Microsoft Docs
description: "Introdução à criação e gestão de registos privados de contentores Docker com a CLI 2.0 do Azure"
services: container-registry
documentationcenter: 
author: neilpeterson
manager: timlt
editor: na
tags: 
keywords: 
ms.assetid: 29e20d75-bf39-4f7d-815f-a2e47209be7d
ms.service: container-registry
ms.devlang: azurecli
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/11/2017
ms.author: nepeters
ms.custom: na
ms.translationtype: HT
ms.sourcegitcommit: 2ad539c85e01bc132a8171490a27fd807c8823a4
ms.openlocfilehash: c7cdb1b13bf32388d18c2a25af28337a81861c1e
ms.contentlocale: pt-pt
ms.lasthandoff: 07/12/2017

---

# <a name="create-a-managed-container-registry-using-the-azure-cli"></a>Criar um registo de contentores com a CLI do Azure

O Azure Container Registry é um serviço de registo do contentor Docker gerido, utilizado para armazenar imagens de contentor do Docker privadas. Este guia detalha a criação de uma instância do Azure Container Registry com a CLI do Azure.

Os registos de contentores do Azure geridos estão em pré-visualização e não estão disponível em todas as regiões.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se optar por instalar e usar a CLI localmente, este tópico requer a execução da versão 2.0.4 ou posterior da CLI do Azure. Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar, veja [instalar o Azure CLI 2.0]( /cli/azure/install-azure-cli). 

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Crie um grupo de recursos com o comando [az group create](/cli/azure/group#create). Um grupo de recursos do Azure é um contentor lógico no qual os recursos do Azure são implementados e geridos. 

O exemplo seguinte cria um grupo de recursos com o nome *myResourceGroup* na localização *westcentralus*.

```azurecli-interactive 
az group create --name myResourceGroup --location westcentralus
```

## <a name="create-a-container-registry"></a>Criar um registo de contentores

Crie uma instância do ACR com o comando [az acr create](/cli/azure/acr#create).

> [!NOTE]
> Quando criar um registo, especifique um nome de domínio de nível superior globalmente exclusivo, que contenha apenas letras e números.

 O nome do registo no exemplo é *myContainerRegistry1*, substitua-o por um nome exclusivo seu.

```azurecli
az acr create --name myContainerRegistry1 --resource-group myResourceGroup --sku Managed_Standard
```

Quando o registo é criado, o resultado é semelhante ao seguinte:

```azurecli
{
  "adminUserEnabled": false,
  "creationDate": "2017-06-29T04:50:28.607134+00:00",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.ContainerRegistry/registries/myContainerRegistry1",
  "location": "westcentralus",
  "loginServer": "mycontainerregistry1.azurecr.io",
  "name": "myContainerRegistry1",
  "provisioningState": "Succeeded",
  "resourceGroup": "myResourceGroup",
  "sku": {
    "name": "Managed_Standard",
    "tier": "Managed"
  },
  "storageAccount": null,
  "tags": {},
  "type": "Microsoft.ContainerRegistry/registries"
}
```

## <a name="log-in-to-acr-instance"></a>Iniciar sessão na instância do ACR

Antes de emitir e solicitar imagens de contentor, tem de iniciar sessão na instância do ACR. Para tal, utilize o comando [az acr login](/cli/azure/acr#login).

```azurecli-interactive
az acr login --name myAzureContainerRegistry1
```

O comando devolve uma mensagem “Início de sessão com êxito” depois de concluir.

## <a name="use-azure-container-registry"></a>Utilizar o Azure Container Registry

### <a name="list-container-images"></a>Listar imagens de contentor

Utilize os comandos da CLI `az acr` para consultar as imagens e etiquetas num repositório.

> [!NOTE]
> Atualmente, o Registo de Contentores não suporta o comando `docker search` para consultar imagens e etiquetas.

### <a name="list-repositories"></a>Listar repositórios

Os exemplos seguintes listam os repositórios num registo, no formato JSON (JavaScript Object Notation):

```azurecli
az acr repository list -n myContainerRegistry1 -o json
```

### <a name="list-tags"></a>Listar etiquetas

O exemplo seguinte lista as etiquetas no repositório **samples/nginx**, no formato JSON:

```azurecli
az acr repository show-tags -n myContainerRegistry1 --repository samples/nginx -o json
```

## <a name="next-steps"></a>Passos seguintes

Neste guia de introdução, criou uma instância gerida do Azure Container Registry com a CLI do Azure.

> [!div class="nextstepaction"]
> [Push your first image using the Docker CLI (Enviar a sua primeira imagem com a CLI do Docker)](container-registry-get-started-docker-cli.md)

