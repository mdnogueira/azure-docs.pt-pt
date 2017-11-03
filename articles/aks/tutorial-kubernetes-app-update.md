---
title: "Kubernertes no tutorial do Azure – a aplicação de atualização | Microsoft Docs"
description: "Tutorial de AKS – a aplicação de atualização"
services: container-service
documentationcenter: 
author: neilpeterson
manager: timlt
editor: 
tags: aks, azure-container-service
keywords: "Docker, Contentores, Microsserviços, Kubernetes, DC/OS, Azure"
ms.assetid: 
ms.service: container-service
ms.devlang: aurecli
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/24/2017
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: 76fd3665fdbc2faa023452f3f7f25e0b67b8fac8
ms.sourcegitcommit: c5eeb0c950a0ba35d0b0953f5d88d3be57960180
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/24/2017
---
# <a name="update-an-application-in-azure-container-service-aks"></a>Atualizar uma aplicação no serviço de contentor do Azure (AKS)

Depois de uma aplicação tiver sido implementada no Kubernetes, podem ser atualizada, especificando uma nova imagem do contentor ou uma versão de imagem. Ao fazê-lo, a atualização é testada, para que apenas uma parte da implementação é atualizada em simultâneo. Esta atualização testada permite que a aplicação continuará a ser executada durante a atualização. Também fornece um mecanismo de reversão se ocorrer uma falha de implementação. 

Neste tutorial, parte seis oito, a aplicação de voto do Azure de exemplo é atualizada. As tarefas que concluir incluem:

> [!div class="checklist"]
> * Atualizar o código de front-end da aplicação
> * Criar uma imagem do contentor atualizadas
> * Enviar a imagem do contentor para o registo de contentor do Azure
> * Implementar a imagem do contentor atualizadas

Nos tutoriais subsequentes, o Operations Management Suite está configurado para monitorizar o cluster Kubernetes.

## <a name="before-you-begin"></a>Antes de começar

Tutoriais anteriores, uma aplicação foi compactada uma imagem de contentor, a imagem carregada para o registo de contentor do Azure e um cluster de Kubernetes criada. A aplicação, em seguida, foi executada no Kubernetes cluster. 

Um repositório de aplicações também foi clonado que inclui o código fonte da aplicação e um ficheiro de Docker Compose pré-criadas utilizado neste tutorial. Certifique-se de que criou um clone do repositório e que foram alteradas diretórios para o diretório clonado. Interior é um diretório com o nome `azure-vote` e um ficheiro denominado `docker-compose.yml`.

Se ainda não concluir estes passos e pretender acompanhar, regresse ao [Tutorial 1 – criar imagens de contentor](./tutorial-kubernetes-prepare-app.md). 

## <a name="update-application"></a>Aplicação de atualização

Para este tutorial, é efetuada uma alteração para a aplicação e a aplicação atualizada implementadas para o cluster Kubernetes. 

O código fonte da aplicação pode ser encontrado no interior do `azure-vote` diretório. Abra o `config_file.cfg` ficheiros com qualquer editor de texto ou de código. Neste exemplo `vi` é utilizado.

```console
vi azure-vote/azure-vote/config_file.cfg
```

Alterar os valores para `VOTE1VALUE` e `VOTE2VALUE`e, em seguida, guarde o ficheiro.

```console
# UI Configurations
TITLE = 'Azure Voting App'
VOTE1VALUE = 'Blue'
VOTE2VALUE = 'Purple'
SHOWHOST = 'false'
```

Guarde e feche o ficheiro.

## <a name="update-container-image"></a>Atualizar imagem de contentor

Utilize [compor o docker](https://docs.docker.com/compose/) para voltar a criar a imagem de front-end e executar a aplicação atualizada. O `--build` argumento é utilizado para instruir o Docker Compose para voltar a criar a imagem de aplicação.

```console
docker-compose up --build -d
```

## <a name="test-application-locally"></a>Testar a aplicação localmente

Procure http://localhost:8080 para ver a aplicação atualizada.

![Imagem do cluster do Kubernetes no Azure no Azure](media/container-service-kubernetes-tutorials/vote-app-updated.png)

## <a name="tag-and-push-images"></a>Imagens de tag e push

Etiqueta de `azure-vote-front` imagem com o loginServer do registo de contentor. 

Obter o nome do servidor de início de sessão com a [lista de acr az](/cli/azure/acr#list) comando.

```azurecli
az acr list --resource-group myResourceGroup --query "[].{acrLoginServer:loginServer}" --output table
```

Utilize [etiquetas de docker](https://docs.docker.com/engine/reference/commandline/tag/) para marcar a imagem. Substitua `<acrLoginServer>` com o nome do servidor de início de sessão de registo de contentor do Azure ou o nome de anfitrião público do registo. Também tenha em atenção que a versão da imagem é atualizada para `redis-v2`.

```console
docker tag azure-vote-front <acrLoginServer>/azure-vote-front:redis-v2
```

Utilize [docker push](https://docs.docker.com/engine/reference/commandline/push/) para carregar a imagem para o seu registo. Substitua `<acrLoginServer>` com o nome de servidor de início de sessão do registo de contentor do Azure.

```console
docker push <acrLoginServer>/azure-vote-front:redis-v2
```

## <a name="deploy-update-application"></a>Implementar a aplicação de atualização

Para garantir a máxima disponibilidade, tem de executar várias instâncias do pod a aplicação. Certifique-se esta configuração com o [kubectl obter pod](https://kubernetes.io/docs/user-guide/kubectl/v1.6/#get) comando.

```
kubectl get pod
```

Saída:

```
NAME                               READY     STATUS    RESTARTS   AGE
azure-vote-back-217588096-5w632    1/1       Running   0          10m
azure-vote-front-233282510-b5pkz   1/1       Running   0          10m
azure-vote-front-233282510-dhrtr   1/1       Running   0          10m
azure-vote-front-233282510-pqbfk   1/1       Running   0          10m
```

Se tiver vários pods com a imagem de frente de voto de azure, dimensionar o `azure-vote-front` implementação.


```azurecli
kubectl scale --replicas=3 deployment/azure-vote-front
```

Para atualizar a aplicação, utilize o [kubectl conjunto](https://kubernetes.io/docs/user-guide/kubectl/v1.6/#set) comando. Atualização `<acrLoginServer>` com o nome de anfitrião ou servidor de início de sessão do seu registo de contentor.

```azurecli
kubectl set image deployment azure-vote-front azure-vote-front=<acrLoginServer>/azure-vote-front:redis-v2
```

Para monitorizar a implementação, utilize o [kubectl obter pod](https://kubernetes.io/docs/user-guide/kubectl/v1.6/#get) comando. Como a aplicação atualizada é implementada, as pods são terminadas e recriados com a nova imagem do contentor.

```azurecli
kubectl get pod
```

Saída:

```
NAME                               READY     STATUS    RESTARTS   AGE
azure-vote-back-2978095810-gq9g0   1/1       Running   0          5m
azure-vote-front-1297194256-tpjlg   1/1       Running   0         1m
azure-vote-front-1297194256-tptnx   1/1       Running   0         5m
azure-vote-front-1297194256-zktw9   1/1       Terminating   0         1m
```

## <a name="test-updated-application"></a>Aplicação de teste atualizado

Obter o endereço IP externo do `azure-vote-front` serviço.

```azurecli
kubectl get service azure-vote-front
```

Navegue para o endereço IP para ver a aplicação atualizada.

![Imagem do cluster do Kubernetes no Azure no Azure](media/container-service-kubernetes-tutorials/vote-app-updated-external.png)

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, pode atualizar uma aplicação e implementado esta atualização para um cluster de Kubernetes. As tarefas seguintes foram concluídas:

> [!div class="checklist"]
> * Atualizar o código de front-end da aplicação
> * Criar uma imagem do contentor atualizadas
> * A imagem do contentor é feito o Push de registo do contentor do Azure
> * Implementar a aplicação atualizada

Avançar para o próximo tutorial para saber mais sobre como monitorizar Kubernetes com o Operations Management Suite.

> [!div class="nextstepaction"]
> [Monitorizar o Kubernetes com o Log Analytics (Monitor Kubernetes with Log Analytics)](./tutorial-kubernetes-monitor.md)