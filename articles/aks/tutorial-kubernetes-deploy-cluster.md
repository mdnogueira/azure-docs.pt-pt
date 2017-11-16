---
title: "Kubernetes no tutorial do Azure – implementar Cluster | Microsoft Docs"
description: Tutorial AKS - implementar Cluster
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
ms.date: 11/15/2017
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: 69dea4ab748d88d18cf01dc9b3fc1bdddd562681
ms.sourcegitcommit: c25cf136aab5f082caaf93d598df78dc23e327b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/15/2017
---
# <a name="deploy-an-azure-container-service-aks-cluster"></a>Implementar um cluster do serviço de contentor do Azure (AKS)

O Kubernetes fornece uma plataforma distribuída para aplicações em contentores. Com AKS, o aprovisionamento de um cluster Kubernetes pronto produção é simples e rápida. Neste tutorial, parte três oito, implementação de um cluster de Kubernetes em AKS. Passos concluídos incluem:

> [!div class="checklist"]
> * Implementar um cluster de Kubernetes AKS
> * Instalação da CLI do Kubernetes (kubectl)
> * Configuração de kubectl

Nos tutoriais subsequentes, a aplicação de voto do Azure é implementada para o cluster, ampliada, atualizar e Operations Management Suite está configurado para monitorizar o cluster Kubernetes.

## <a name="before-you-begin"></a>Antes de começar

Nos tutoriais anteriores, uma imagem do contentor foi criada e carregada para uma instância de registo de contentor do Azure. Se não o fez estes passos e gostaria de acompanhar, regresse ao [Tutorial 1 – criar imagens de contentor](./tutorial-kubernetes-prepare-app.md).

## <a name="enabling-aks-preview-for-your-azure-subscription"></a>Ativar a pré-visualização AKS para a sua subscrição do Azure
Enquanto AKS está em pré-visualização, a criação de novos clusters requer um sinalizador de funcionalidade na sua subscrição. Pode solicitar esta funcionalidade para qualquer número de subscrições de que pretende utilizar. Utilize o `az provider register` comando para registar o fornecedor AKS:

```azurecli-interactive
az provider register -n Microsoft.ContainerService
```

Depois de registar, agora está pronto para criar um cluster de Kubernetes com AKS.

## <a name="create-kubernetes-cluster"></a>Criar cluster do Kubernetes

O exemplo seguinte cria um cluster com o nome `myK8sCluster` num grupo de recursos com o nome `myResourceGroup`. Este grupo de recursos foi criado no [tutorial anterior](./tutorial-kubernetes-prepare-acr.md).

```azurecli
az aks create --resource-group myResourceGroup --name myK8sCluster --node-count 1 --generate-ssh-keys
```

Após vários minutos, a implementação estiver concluída e formatado de json devolve informações sobre a implementação de AKS.

## <a name="install-the-kubectl-cli"></a>Instalar o kubectl CLI

Para ligar ao Kubernetes cluster do computador cliente, utilize [kubectl](https://kubernetes.io/docs/user-guide/kubectl/), o cliente de linha de comandos Kubernetes.

Se estiver a utilizar o Azure CloudShell, kubectl já está instalado. Se pretender instalá-la localmente, execute o seguinte comando:

```azurecli
az aks install-cli
```

## <a name="connect-with-kubectl"></a>Ligar-se com kubectl

Para configurar kubectl para ligar ao Kubernetes cluster, execute o seguinte comando:

```azurecli
az aks get-credentials --resource-group=myResourceGroup --name=myK8sCluster
```

Para verificar a ligação ao cluster, execute o [kubectl obter nós](https://kubernetes.io/docs/user-guide/kubectl/v1.6/#get) comando.

```azurecli
kubectl get nodes
```

Saída:

```
NAME                          STATUS    AGE       VERSION
k8s-myk8scluster-36346190-0   Ready     49m       v1.7.7
```

Após conclusão tutorial, tem um cluster AKS pronto para cargas de trabalho. Nos tutoriais subsequentes, uma aplicação de contentor multi é implementada para este cluster, ampliada, atualizada e monitorizada.

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, um cluster de Kubernetes foi implementado no AKS. Foram efetuados os seguintes passos:

> [!div class="checklist"]
> * Implementar um cluster de Kubernetes AKS
> * Instalar a CLI de Kubernetes (kubectl)
> * Kubectl configurado

Avançar para o próximo tutorial para saber mais sobre a execução da aplicação no cluster.

> [!div class="nextstepaction"]
> [Implementar a aplicação no Kubernetes](./tutorial-kubernetes-deploy-application.md)
