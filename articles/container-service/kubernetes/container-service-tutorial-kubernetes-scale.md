---
title: "Tutorial de serviço de contentor do Azure – Dimensionar aplicação | Microsoft Docs"
description: "Tutorial de serviço de contentor do Azure – Dimensionar aplicação"
services: container-service
documentationcenter: 
author: dlepow
manager: timlt
editor: 
tags: acs, azure-container-service
keywords: "Docker, contentores, microserviços, Kubernetes, Azure"
ms.assetid: 
ms.service: container-service
ms.devlang: aurecli
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/14/2017
ms.author: danlep
ms.custom: mvc
ms.openlocfilehash: 25d7e56c7d7755e44fdb16e3b5585a112e495e77
ms.sourcegitcommit: c5eeb0c950a0ba35d0b0953f5d88d3be57960180
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/24/2017
---
# <a name="scale-kubernetes-pods-and-kubernetes-infrastructure"></a>Escala Kubernetes pods e a infraestrutura de Kubernetes

[!INCLUDE [aks-preview-redirect.md](../../../includes/aks-preview-redirect.md)]

Se tiver sido a seguir os tutoriais, tem de ter um Kubernetes cluster no serviço de contentor do Azure e implementou a aplicação de voto do Azure. 

Neste tutorial, parte cinco sete, aumentar horizontalmente pods na aplicação e tente o dimensionamento automático de pod. Também irá aprender a dimensionar o número de nós de agente da VM do Azure para alterar a capacidade do cluster para alojar cargas de trabalho. Tarefas foram completadas incluem:

> [!div class="checklist"]
> * Dimensionar Manualmente Kubernetes pods
> * Configurar pods de dimensionamento automático com o front-end da aplicação
> * Dimensionar os nós de agente do Kubernetes Azure

Nos tutoriais subsequentes, a aplicação de voto do Azure está atualizada e Operations Management Suite configurado para monitorizar o cluster Kubernetes.

## <a name="before-you-begin"></a>Antes de começar

Tutoriais anteriores, uma aplicação foi compactada uma imagem de contentor, esta imagem carregada para o registo de contentor do Azure e um cluster de Kubernetes criada. A aplicação, em seguida, foi executada no Kubernetes cluster. 

Se não o fez estes passos e gostaria de acompanhar, voltar para o [Tutorial 1 – criar imagens de contentor](./container-service-tutorial-kubernetes-prepare-app.md). 

## <a name="manually-scale-pods"></a>Dimensionar Manualmente pods

Deste modo, extremidade, o front-end de voto do Azure e a instância de Redis tem sido implementado, cada um com uma única réplica. Para verificar, execute o [kubectl obter](https://kubernetes.io/docs/user-guide/kubectl/v1.6/#get) comando.

```azurecli-interactive
kubectl get pods
```

Saída:

```bash
NAME                               READY     STATUS    RESTARTS   AGE
azure-vote-back-2549686872-4d2r5   1/1       Running   0          31m
azure-vote-front-848767080-tf34m   1/1       Running   0          31m
```

O número de pods de alterar manualmente o `azure-vote-front` implementação utilizando o [kubectl escala](https://kubernetes.io/docs/user-guide/kubectl/v1.6/#scale) comando. Neste exemplo aumenta o número a 5.

```azurecli-interactive
kubectl scale --replicas=5 deployment/azure-vote-front
```

Executar [kubectl obter pods](https://kubernetes.io/docs/user-guide/kubectl/v1.6/#get) para verificar que Kubernetes está a criar os pods. Depois de um minuto ou, estiver a executar os pods adicionais:

```azurecli-interactive
kubectl get pods
```

Saída:

```bash
NAME                                READY     STATUS    RESTARTS   AGE
azure-vote-back-2606967446-nmpcf    1/1       Running   0          15m
azure-vote-front-3309479140-2hfh0   1/1       Running   0          3m
azure-vote-front-3309479140-bzt05   1/1       Running   0          3m
azure-vote-front-3309479140-fvcvm   1/1       Running   0          3m
azure-vote-front-3309479140-hrbf2   1/1       Running   0          15m
azure-vote-front-3309479140-qphz8   1/1       Running   0          3m
```

## <a name="autoscale-pods"></a>Pods de dimensionamento automático

Suporta Kubernetes [dimensionamento automático de horizontal pod](https://kubernetes.io/docs/tasks/run-application/horizontal-pod-autoscale/) para ajustar o número de pods numa implementação consoante a utilização da CPU ou outros selecionar métricas. 

Para utilizar o autoscaler, sua pods tem de ter os pedidos de CPU e limites definidos. No `azure-vote-front` de implementação, o contentor de front-end CPU pedidos 0.25, com um limite de 0,5 CPU. As definições de ter o seguinte aspeto:

```YAML
resources:
  requests:
     cpu: 250m
  limits:
     cpu: 500m
```

O exemplo seguinte utiliza o [dimensionamento automático de kubectl](https://kubernetes.io/docs/user-guide/kubectl/v1.6/#autoscale) comando para dimensionar automaticamente o número de pods no `azure-vote-front` implementação. Aqui, se a utilização da CPU exceder 50%, o autoscaler aumenta pods para um máximo de 10.


```azurecli-interactive
kubectl autoscale deployment azure-vote-front --cpu-percent=50 --min=3 --max=10
```

Para ver o estado do autoscaler, execute o seguinte comando:

```azurecli-interactive
kubectl get hpa
```

Saída:

```bash
NAME               REFERENCE                     TARGETS    MINPODS   MAXPODS   REPLICAS   AGE
azure-vote-front   Deployment/azure-vote-front   0% / 50%   3         10        3          2m
```

Após alguns minutos, com carga mínima na aplicação de voto do Azure, o número de réplicas pod diminui automaticamente para 3.

## <a name="scale-the-agents"></a>Dimensionar os agentes

Se tiver criado o seu cluster Kubernetes utilizando comandos de predefinido no tutorial anterior, tem três nós de agente. Pode ajustar o número de agentes manualmente, se pretender mais ou menos contentor as cargas de trabalho no seu cluster. Utilize o [az acs Dimensionar](/cli/azure/acs#scale) de comandos e especificar o número de agentes com o `--new-agent-count` parâmetro.

O exemplo seguinte aumenta o número de nós de agente para 4 no cluster denominado Kubernetes *myK8sCluster*. O comando demora alguns minutos a concluir.

```azurecli-interactive
az acs scale --resource-group=myResourceGroup --name=myK8SCluster --new-agent-count 4
```

A saída do comando mostra o número de agente de nós no valor da `agentPoolProfiles:count`:

```azurecli
{
  "agentPoolProfiles": [
    {
      "count": 4,
      "dnsPrefix": "myK8SCluster-myK8SCluster-e44f25-k8s-agents",
      "fqdn": "",
      "name": "agentpools",
      "vmSize": "Standard_D2_v2"
    }
  ],
...

```

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, utilizou diferentes funcionalidades de dimensionamento no seu cluster Kubernetes. Tarefas abrangidos incluídos:

> [!div class="checklist"]
> * Dimensionar Manualmente Kubernetes pods
> * Configurar pods de dimensionamento automático com o front-end da aplicação
> * Dimensionar os nós de agente do Kubernetes Azure

Avançar para o próximo tutorial para saber mais sobre como atualizar a aplicação no Kubernetes.

> [!div class="nextstepaction"]
> [Atualizar uma aplicação num Kubernetes](./container-service-tutorial-kubernetes-app-update.md)

