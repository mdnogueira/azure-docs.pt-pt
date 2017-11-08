---
title: "Tutorial de instâncias de contentor do Azure – preparar o registo de contentor do Azure"
description: "Tutorial de instâncias de contentor do Azure – preparar o registo de contentor do Azure"
services: container-instances
documentationcenter: 
author: neilpeterson
manager: timlt
editor: mmacy
tags: acs, azure-container-service
keywords: "Docker, Contentores, Microsserviços, Kubernetes, DC/OS, Azure"
ms.assetid: 
ms.service: container-instances
ms.devlang: azurecli
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/07/2017
ms.author: seanmck
ms.custom: mvc
ms.openlocfilehash: 848f6cbde49efdcfe96fc58ebc4160e0ea39f3f2
ms.sourcegitcommit: 6a6e14fdd9388333d3ededc02b1fb2fb3f8d56e5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/07/2017
---
# <a name="deploy-and-use-azure-container-registry"></a>Implementar e utilizar o registo de contentor do Azure

Esta é a parte dois de um tutorial de três partes. No [passo anterior](container-instances-tutorial-prepare-app.md), foi criada uma imagem de contentor para uma aplicação web simples escrita em [Node.js](http://nodejs.org). Neste tutorial, push a imagem para um registo de contentor do Azure. Se não tiver criado a imagem de contentor, regressar à [Tutorial 1 – criar imagem de contentor](container-instances-tutorial-prepare-app.md).

O registo de contentor do Azure é um registo privado baseado no Azure, para imagens de contentor do Docker. Este tutorial explica a implementar uma instância de registo de contentor do Azure e enviar uma imagem de contentor ao mesmo. Passos concluídos incluem:

> [!div class="checklist"]
> * Implementar uma instância de registo de contentor do Azure
> * A marcação de imagem de contentor para o registo de contentor do Azure
> * Carregar imagem para o registo de contentor do Azure

Nos tutoriais subsequentes, implementa o contentor a partir do seu registo privado para instâncias de contentor do Azure.

## <a name="before-you-begin"></a>Antes de começar

Este tutorial requer que está a executar a CLI do Azure versão 2.0.20 ou posterior. Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar, veja [instalar o Azure CLI 2.0](/cli/azure/install-azure-cli).

Para concluir este tutorial, precisa de um ambiente de desenvolvimento do Docker. O Docker disponibiliza pacotes que o configuram facilmente em qualquer sistema [Mac](https://docs.docker.com/docker-for-mac/), [Windows](https://docs.docker.com/docker-for-windows/) ou [Linux](https://docs.docker.com/engine/installation/#supported-platforms).

Shell de nuvem do Azure não inclui os componentes de Docker necessários para concluir cada passo neste tutorial. Por conseguinte, recomendamos uma instalação local do ambiente de desenvolvimento CLI do Azure e Docker.

## <a name="deploy-azure-container-registry"></a>Implementar o registo de contentor do Azure

Quando implementar um registo de contentor do Azure, primeiro precisa de um grupo de recursos. Um grupo de recursos do Azure é uma coleção lógica na qual os recursos estão implementados e geridos.

Crie um grupo de recursos com o comando [az group create](/cli/azure/group#create). Neste exemplo, um grupo de recursos denominado *myResourceGroup* é criado no *eastus* região.

```azurecli
az group create --name myResourceGroup --location eastus
```

Criar um registo de contentor do Azure com o [az acr criar](/cli/azure/acr#create) comando. O nome do registo de contentor **têm de ser exclusivos** no Azure e pode conter carateres alfanuméricos de 5 a 50. Substitua `<acrName>` com um nome exclusivo para o registo:

```azurecli
az acr create --resource-group myResourceGroup --name <acrName> --sku Basic
```

Por exemplo criar um registo de contentor do Azure com o nome *mycontainerregistry082*:

```azurecli
az acr create --resource-group myResourceGroup --name mycontainerregistry082 --sku Basic --admin-enabled true
```

Em todo o resto deste tutorial, utilizamos `<acrName>` como um marcador de posição para o nome do registo de contentor que escolheu.

## <a name="container-registry-login"></a>Início de sessão de registo de contentor

Tem de iniciar sessão sua instância ACR antes de enviar imagens à mesma. Utilize o [início de sessão do az acr](/cli/azure/acr#az_acr_login) comando para concluir a operação. Tem de fornecer o nome exclusivo especificado no registo do contentor quando foi criado.

```azurecli
az acr login --name <acrName>
```

O comando devolve um `Login Succeeded` mensagem depois de concluir.

## <a name="tag-container-image"></a>Imagem de contentor da etiqueta

Para implementar uma imagem de contentor de um registo privada, a imagem tem de ser etiquetados com o `loginServer` nome do registo.

Para ver uma lista de imagens atuais, utilize o `docker images` comando.

```bash
docker images
```

Saída:

```bash
REPOSITORY                   TAG                 IMAGE ID            CREATED              SIZE
aci-tutorial-app             latest              5c745774dfa9        39 seconds ago       68.1 MB
```

Para obter o nome de loginServer, execute o seguinte comando. Substitua `<acrName>` com o nome do seu registo de contentor.

```azurecli
az acr show --name <acrName> --query loginServer --output table
```

Exemplo de saída:

```
Result
------------------------
mycontainerregistry082.azurecr.io
```

Etiqueta de *aci-tutorial-aplicação* imagem com o loginServer do seu registo de contentor. Além disso, adicionar `:v1` ao fim do nome de imagem. Esta etiqueta indica o número de versão da imagem. Substitua `<acrLoginServer>` com o resultado do `az acr show` comando que acabou de ser executado.

```bash
docker tag aci-tutorial-app <acrLoginServer>/aci-tutorial-app:v1
```

Depois de etiquetados, executar `docker images` para verificar a operação.

```bash
docker images
```

Saída:

```bash
REPOSITORY                                                TAG                 IMAGE ID            CREATED             SIZE
aci-tutorial-app                                          latest              5c745774dfa9        39 seconds ago      68.1 MB
mycontainerregistry082.azurecr.io/aci-tutorial-app        v1                  a9dace4e1a17        7 minutes ago       68.1 MB
```

## <a name="push-image-to-azure-container-registry"></a>Imagem de push para o registo de contentor do Azure

Push de *aci-tutorial-aplicação* imagem para o registo com o `docker push` comando. Substitua `<acrLoginServer>` com o nome do servidor de início de sessão completa obter no passo anterior.

```bash
docker push <acrLoginServer>/aci-tutorial-app:v1
```

O `push` operação deve demorar alguns segundos a alguns minutos, consoante a ligação à Internet e o resultado é semelhante ao seguinte:

```bash
The push refers to a repository [mycontainerregistry082.azurecr.io/aci-tutorial-app]
3db9cac20d49: Pushed
13f653351004: Pushed
4cd158165f4d: Pushed
d8fbd47558a8: Pushed
44ab46125c35: Pushed
5bef08742407: Pushed
v1: digest: sha256:ed67fff971da47175856505585dcd92d1270c3b37543e8afd46014d328f05715 size: 1576
```

## <a name="list-images-in-azure-container-registry"></a>Lista de imagens no registo de contentor do Azure

Para obter uma lista de imagens que tiver sido feito o push para o registo de contentor do Azure, utilize o [lista de repositório az acr](/cli/azure/acr/repository#list) comando. Atualize o comando com o nome do registo de contentor.

```azurecli
az acr repository list --name <acrName> --output table
```

Saída:

```azurecli
Result
----------------
aci-tutorial-app
```

E, em seguida, para ver as etiquetas para uma imagem específica, utilize o [az acr repositório Mostrar-etiquetas](/cli/azure/acr/repository#show-tags) comando.

```azurecli
az acr repository show-tags --name <acrName> --repository aci-tutorial-app --output table
```

Saída:

```azurecli
Result
--------
v1
```

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, um registo de contentor do Azure foi preparado para ser utilizado com instâncias de contentor do Azure e a imagem do contentor foi instalada. Foram efetuados os seguintes passos:

> [!div class="checklist"]
> * Implementada uma instância de registo de contentor do Azure
> * Etiquetados uma imagem de contentor para o registo de contentor do Azure
> * Carregar uma imagem no registo de contentor do Azure

Avançar para o próximo tutorial para saber mais sobre a implementar o contentor do Azure utilizando as instâncias de contentor do Azure.

> [!div class="nextstepaction"]
> [Implementar contentores para instâncias de contentor do Azure](./container-instances-tutorial-deploy-app.md)
