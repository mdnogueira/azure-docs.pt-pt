---
title: "Tutorial de instâncias de contentor do Azure – implemente a aplicação"
description: "Tutorial de instâncias de contentor do Azure – implemente a aplicação"
services: container-instances
documentationcenter: 
author: seanmck
manager: timlt
editor: mmacy
tags: 
keywords: 
ms.assetid: 
ms.service: container-instances
ms.devlang: azurecli
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/07/2017
ms.author: seanmck
ms.custom: mvc
ms.openlocfilehash: 2858f20cd9da469d5983e2bef9176f5922349196
ms.sourcegitcommit: 6a6e14fdd9388333d3ededc02b1fb2fb3f8d56e5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/07/2017
---
# <a name="deploy-a-container-to-azure-container-instances"></a>Implementar um contentor para instâncias de contentor do Azure

Este é o último de um tutorial de três partes. Nas secções anteriores, [foi criada uma imagem de contentor](container-instances-tutorial-prepare-app.md) e [enviada para um registo de contentor do Azure](container-instances-tutorial-prepare-acr.md). Esta secção concluir o tutorial ao implementar o contentor de instâncias de contentor do Azure. Passos concluídos incluem:

> [!div class="checklist"]
> * Implementar o contentor do registo de contentor Azure utilizando a CLI do Azure
> * Visualizar a aplicação no browser
> * Ver os registos do contentor

## <a name="before-you-begin"></a>Antes de começar

Este tutorial requer que está a executar a CLI do Azure versão 2.0.20 ou posterior. Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar, veja [instalar o Azure CLI 2.0](/cli/azure/install-azure-cli).

Para concluir este tutorial, precisa de um ambiente de desenvolvimento do Docker. O Docker disponibiliza pacotes que o configuram facilmente em qualquer sistema [Mac](https://docs.docker.com/docker-for-mac/), [Windows](https://docs.docker.com/docker-for-windows/) ou [Linux](https://docs.docker.com/engine/installation/#supported-platforms).

Shell de nuvem do Azure não inclui os componentes de Docker necessários para concluir cada passo neste tutorial. Por conseguinte, recomendamos uma instalação local do ambiente de desenvolvimento CLI do Azure e Docker.

## <a name="deploy-the-container-using-the-azure-cli"></a>Implementar o contentor utilizando a CLI do Azure

A CLI do Azure permite a implementação de um contentor para instâncias de contentor do Azure num único comando. Uma vez que a imagem do contentor está alojada no registo de contentor do Azure privado, tem de incluir as credenciais necessárias para aceder ao mesmo. Se necessário, pode consultá-las conforme mostrado abaixo.

Servidor de início de sessão de registo de contentor (atualização com o nome do registo):

```azurecli
az acr show --name <acrName> --query loginServer
```

Palavra-passe de registo de contentor:

```azurecli
az acr credential show --name <acrName> --query "passwords[0].value"
```

Para implementar a imagem de contentor do registo de contentor com um pedido de recurso de 1 núcleo de CPU e de 1 GB de memória, execute o seguinte comando. Substitua `<acrLoginServer>` e `<acrPassword>` com os valores obtidos a partir de dois comandos anteriores.

```azurecli
az container create --name aci-tutorial-app --image <acrLoginServer>/aci-tutorial-app:v1 --cpu 1 --memory 1 --registry-password <acrPassword> --ip-address public -g myResourceGroup
```

Dentro de alguns segundos, deverá receber uma resposta inicial do Azure Resource Manager. Para ver o estado da implementação, utilize [mostrar de contentor az](/cli/azure/container#az_container_show):

```azurecli
az container show --name aci-tutorial-app --resource-group myResourceGroup --query instanceView.state
```

Repita o `az container show` comando até que o estado é alterado de *pendente* para *executar*, que deve tomar num minuto. Quando o contentor é *executar*, avance para o passo seguinte.

## <a name="view-the-application-and-container-logs"></a>Consulte os registos de aplicações e do contentor

Depois da implementação for bem sucedida, apresentar o endereço IP público do contentor com o [mostrar de contentor az](/cli/azure/container#az_container_show) comando:

```bash
az container show --name aci-tutorial-app --resource-group myResourceGroup --query ipAddress.ip
```

Exemplo de saída:`"13.88.176.27"`

Para ver a aplicação em execução, navegue para o endereço IP público no seu browser favorito.

![Olá mundo aplicação no browser][aci-app-browser]

Também pode ver a saída de registo do contentor:

```azurecli
az container logs --name aci-tutorial-app -g myResourceGroup
```

Saída:

```bash
listening on port 80
::ffff:10.240.0.4 - - [21/Jul/2017:06:00:02 +0000] "GET / HTTP/1.1" 200 1663 "-" "Mozilla/5.0 (Macintosh; Intel Mac OS X 10_12_5) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/59.0.3071.115 Safari/537.36"
::ffff:10.240.0.4 - - [21/Jul/2017:06:00:02 +0000] "GET /favicon.ico HTTP/1.1" 404 150 "http://13.88.176.27/" "Mozilla/5.0 (Macintosh; Intel Mac OS X 10_12_5) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/59.0.3071.115 Safari/537.36"
```

## <a name="clean-up-resources"></a>Limpar recursos

Se já não necessita de qualquer um dos recursos que criou neste tutorial série, pode executar o [eliminação do grupo de az](/cli/azure/group#delete) comando para remover o grupo de recursos e todos os recursos nele contidos. Este comando elimina o registo de contentor que criou, bem como o contentor em execução e todos os recursos relacionados.

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, concluir o processo de implementação os contentores para instâncias de contentor do Azure. Foram efetuados os seguintes passos:

> [!div class="checklist"]
> * Implementar o contentor do registo de contentor Azure utilizando a CLI do Azure
> * Visualizar a aplicação no browser
> * Ver os registos do contentor

<!-- LINKS -->
[prepare-app]: ./container-instances-tutorial-prepare-app.md

<!-- IMAGES -->
[aci-app-browser]: ./media/container-instances-quickstart/aci-app-browser.png
