---
title: "Tutorial de serviço de contentor do Azure – implementar aplicações | Microsoft Docs"
description: "Tutorial de serviço de contentor do Azure – implementar aplicações"
services: container-service
documentationcenter: 
author: neilpeterson
manager: timlt
editor: 
tags: acs, azure-container-service
keywords: "Docker, Contentores, Microsserviços, Kubernetes, DC/OS, Azure"
ms.assetid: 
ms.service: container-service
ms.devlang: aurecli
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/14/2017
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: 2c9563a38f0aedcb6f67fd44031a47a712088ddc
ms.sourcegitcommit: c5eeb0c950a0ba35d0b0953f5d88d3be57960180
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/24/2017
---
# <a name="run-applications-in-kubernetes"></a>Executar aplicações na Kubernetes

[!INCLUDE [aks-preview-redirect.md](../../../includes/aks-preview-redirect.md)]

Neste tutorial, parte quatro de sete, um exemplo de aplicação é implementado num Kubernetes cluster. Passos concluídos incluem:

> [!div class="checklist"]
> * Atualizar os ficheiros de manifesto Kubernetes
> * Executar a aplicação no Kubernetes
> * Testar a aplicação

Nos tutoriais subsequentes, esta aplicação é ampliada, atualizar, e Operations Management Suite configurado para monitorizar o cluster Kubernetes.

Este tutorial assume uma compreensão básica sobre conceitos Kubernetes, para obter informações detalhadas, consulte Kubernetes o [Kubernetes documentação](https://kubernetes.io/docs/home/).

## <a name="before-you-begin"></a>Antes de começar

Nos tutoriais anteriores, uma aplicação foi compactada uma imagem de contentor, esta imagem foi carregada para o registo de contentor do Azure e um cluster de Kubernetes foi criado. 

Para concluir este tutorial, terá de previamente criadas `azure-vote-all-in-one-redis.yml` Kubernetes manifesto do ficheiro. Um tutorial anterior, este ficheiro foi transferido com o código fonte da aplicação. Certifique-se de que tem clonou o repositório e que foram alteradas diretórios para o repositório clonado.

Se não o fez estes passos e gostaria de acompanhar, regresse ao [Tutorial 1 – criar imagens de contentor](./container-service-tutorial-kubernetes-prepare-app.md). 

## <a name="update-manifest-file"></a>Ficheiro de manifesto de atualização

Neste tutorial, registo de contentor do Azure (ACR) foi utilizado para armazenar uma imagem de contentor. Antes de executar a aplicação, o nome de servidor de início de sessão ACR tem de ser atualizada no ficheiro de manifesto Kubernetes.

Obter o nome do servidor de início de sessão ACR com o [lista de acr az](/cli/azure/acr#list) comando.

```azurecli-interactive
az acr list --resource-group myResourceGroup --query "[].{acrLoginServer:loginServer}" --output table
```

O ficheiro de manifesto tiver sido previamente criado com um nome de servidor de início de sessão de `microsoft`. Abra o ficheiro com qualquer editor de texto. Neste exemplo, abrir o ficheiro com `vi`.

```bash
vi azure-vote-all-in-one-redis.yml
```

Substitua `microsoft` com o nome de servidor de início de sessão ACR. Este valor for encontrado na linha **47** do ficheiro de manifesto.

```yaml
containers:
- name: azure-vote-front
  image: microsoft/azure-vote-front:redis-v1
```

Guarde e feche o ficheiro.

## <a name="deploy-application"></a>Implementar a aplicação

Utilize o comando [kubectl create](https://kubernetes.io/docs/user-guide/kubectl/v1.6/#create) para executar a aplicação. Este comando analisa o ficheiro de manifesto e criar os objetos de Kubernetes definidos.

```azurecli-interactive
kubectl create -f azure-vote-all-in-one-redis.yml
```

Saída:

```bash
deployment "azure-vote-back" created
service "azure-vote-back" created
deployment "azure-vote-front" created
service "azure-vote-front" created
```

## <a name="test-application"></a>Aplicação de teste

A [Kubernetes serviço](https://kubernetes.io/docs/concepts/services-networking/service/) é criado que expõe a aplicação à internet. Este processo pode demorar alguns minutos. 

Para monitorizar o progresso, utilize o comando [kubectl get service](https://review.docs.microsoft.com/en-us/azure/container-service/container-service-kubernetes-walkthrough?branch=pr-en-us-17681) com o argumento `--watch`.

```azurecli-interactive
kubectl get service azure-vote-front --watch
```

Inicialmente, o **IP externo** para o `azure-vote-front` serviço aparece como `pending`. Depois do endereço IP externo mudou de `pending` para um `IP address`, utilize `CTRL-C` parar o processo de veja kubectl.

```bash
NAME               CLUSTER-IP    EXTERNAL-IP   PORT(S)        AGE
azure-vote-front   10.0.42.158   <pending>     80:31873/TCP   1m
azure-vote-front   10.0.42.158   52.179.23.131 80:31873/TCP   2m
```

Para ver a aplicação, navegue para o endereço IP externo.

![Imagem do cluster do Kubernetes no Azure no Azure](media/container-service-kubernetes-tutorials/azure-vote.png)

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, a aplicação de voto do Azure foi implementada para um cluster de Kubernetes de serviço de contentor do Azure. Tarefas foram completadas incluem:  

> [!div class="checklist"]
> * Transferir ficheiros de manifesto Kubernetes
> * Executar a aplicação no Kubernetes
> * Testar a aplicação

Avançar para o próximo tutorial para saber mais sobre uma aplicação Kubernetes e a infraestrutura de Kubernetes subjacente. 

> [!div class="nextstepaction"]
> [Dimensionar Kubernetes aplicação e a infraestrutura](./container-service-tutorial-kubernetes-scale.md)