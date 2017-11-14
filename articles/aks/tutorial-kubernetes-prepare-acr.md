---
title: "Kubernertes no tutorial do Azure – preparar ACR | Microsoft Docs"
description: "Tutorial de AKS – preparar ACR"
services: container-service
documentationcenter: 
author: neilpeterson
manager: timlt
editor: 
tags: aks, azure-container-service
keywords: "Docker, Contentores, Microsserviços, Kubernetes, DC/OS, Azure"
ms.assetid: 
ms.service: container-service
ms.devlang: azurecli
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/11/2017
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: fabfbe51f9486c69d795b04bd668b50971157820
ms.sourcegitcommit: e38120a5575ed35ebe7dccd4daf8d5673534626c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/13/2017
---
# <a name="deploy-and-use-azure-container-registry"></a>Implementar e utilizar o registo de contentor do Azure

Registo de contentor do Azure (ACR) é um registo privado baseado no Azure, para imagens de contentor do Docker. Este tutorial, parte dois de oito, explica a implementar uma instância de registo de contentor do Azure e enviar uma imagem de contentor ao mesmo. Passos concluídos incluem:

> [!div class="checklist"]
> * Implementar uma instância de registo de contentor do Azure (ACR)
> * A marcação de uma imagem de contentor para o ACR
> * Carregar a imagem para o ACR

Nos tutoriais subsequentes, esta instância ACR está integrada com um cluster de Kubernetes AKS. 

## <a name="before-you-begin"></a>Antes de começar

No [tutorial anterior](./tutorial-kubernetes-prepare-app.md), foi criada uma imagem de contentor para uma aplicação de voto do Azure simple. Se não tiver criado a imagem de aplicação de voto do Azure, regressar à [Tutorial 1 – criar imagens de contentor](./tutorial-kubernetes-prepare-app.md).

Este tutorial requer que está a executar a CLI do Azure versão 2.0.20 ou posterior. Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar, veja [Instalar a CLI do Azure]( /cli/azure/install-azure-cli). 

## <a name="deploy-azure-container-registry"></a>Implementar o registo de contentor do Azure

Quando implementar um registo de contentor do Azure, primeiro precisa de um grupo de recursos. Um grupo de recursos do Azure é um contentor lógico no qual os recursos do Azure são implementados e geridos.

Crie um grupo de recursos com o comando [az group create](/cli/azure/group#create). Neste exemplo, um grupo de recursos denominado `myResourceGroup` é criado no `eastus` região.

```azurecli
az group create --name myResourceGroup --location eastus
```

Criar um registo de contentor do Azure com o [az acr criar](/cli/azure/acr#create) comando. O nome de um registo de contentor **têm de ser exclusivos**.

```azurecli
az acr create --resource-group myResourceGroup --name <acrName> --sku Basic
```

Em todo o resto deste tutorial, utilizamos `<acrname>` como um marcador de posição para o nome do registo de contentor.

## <a name="container-registry-login"></a>Início de sessão de registo de contentor

Utilize o [início de sessão do az acr](https://docs.microsoft.com/cli/azure/acr#az_acr_login) comando para iniciar sessão para a instância ACR. Tem de fornecer o nome exclusivo especificado no registo do contentor quando foi criado.

```azurecli
az acr login --name <acrName>
```

O comando devolve uma mensagem 'Início de sessão com êxito' depois de concluir.

## <a name="tag-container-images"></a>Imagens de contentor da etiqueta

Para ver uma lista de imagens atuais, utilize o [imagens docker](https://docs.docker.com/engine/reference/commandline/images/) comando.

```console
docker images
```

Saída:

```
REPOSITORY                   TAG                 IMAGE ID            CREATED             SIZE
azure-vote-front             latest              4675398c9172        13 minutes ago      694MB
redis                        latest              a1b99da73d05        7 days ago          106MB
tiangolo/uwsgi-nginx-flask   flask               788ca94b2313        9 months ago        694MB
```

Cada imagem de contentor tem de ser etiquetados com o nome de loginServer do registo. Esta etiqueta é utilizada para o encaminhamento quando enviar imagens do contentor para um registo de imagem.

Para obter o nome de loginServer, execute o seguinte comando.

```azurecli
az acr list --resource-group myResourceGroup --query "[].{acrLoginServer:loginServer}" --output table
```

Agora, marcar a `azure-vote-front` imagem com o loginServer do registo de contentor. Além disso, adicionar `:redis-v1` ao fim do nome de imagem. Esta etiqueta indica a versão da imagem.

```console
docker tag azure-vote-front <acrLoginServer>/azure-vote-front:redis-v1
```

Depois de etiquetados, executar [imagens docker] (https://docs.docker.com/engine/reference/commandline/images/) para verificar a operação.

```console
docker images
```

Saída:

```
REPOSITORY                                           TAG                 IMAGE ID            CREATED             SIZE
azure-vote-front                                     latest              eaf2b9c57e5e        8 minutes ago       716 MB
mycontainerregistry082.azurecr.io/azure-vote-front   redis-v1            eaf2b9c57e5e        8 minutes ago       716 MB
redis                                                latest              a1b99da73d05        7 days ago          106MB
tiangolo/uwsgi-nginx-flask                           flask               788ca94b2313        8 months ago        694 MB
```

## <a name="push-images-to-registry"></a>Imagens de push para registo

Push de `azure-vote-front` imagem ao registo. 

Utilizando o exemplo seguinte, substitua o nome de loginServer ACR loginServer do seu ambiente.

```console
docker push <acrLoginServer>/azure-vote-front:redis-v1
```

Esta ação demora alguns minutos a concluir.

## <a name="list-images-in-registry"></a>Lista de imagens no registo

Para obter uma lista de imagens que tiver sido feito o push para o registo de contentor do Azure, utilizador de [lista de repositório az acr](/cli/azure/acr/repository#list) comando. Atualize o comando com o nome da instância ACR.

```azurecli
az acr repository list --name <acrName> --output table
```

Saída:

```azurecli
Result
----------------
azure-vote-front
```

E, em seguida, para ver as etiquetas para uma imagem específica, utilize o [az acr repositório Mostrar-etiquetas](/cli/azure/acr/repository#show-tags) comando.

```azurecli
az acr repository show-tags --name <acrName> --repository azure-vote-front --output table
```

Saída:

```azurecli
Result
--------
redis-v1
```

Após conclusão tutorial, a imagem de contentor tenha sido armazenada numa instância de registo de contentor do Azure privada. Esta imagem é implementada de ACR para um cluster de Kubernetes nos tutoriais subsequentes.

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, um registo de contentor do Azure foi preparado para utilização num AKS cluster. Foram efetuados os seguintes passos:

> [!div class="checklist"]
> * Implementada uma instância de registo de contentor do Azure
> * Etiquetados uma imagem de contentor para o ACR
> * Carregada a imagem para o ACR

Avançar para o próximo tutorial para saber mais sobre como implementar um cluster de Kubernetes no Azure.

> [!div class="nextstepaction"]
> [Implementar Kubernetes cluster](./tutorial-kubernetes-deploy-cluster.md)
