---
title: "Tutorial de serviço de contentor do Azure – implementar Cluster | Microsoft Docs"
description: "Tutorial de serviço de contentor do Azure – implementar Cluster"
services: container-service
documentationcenter: 
author: neilpeterson
manager: timlt
editor: 
tags: acs, azure-container-service
keywords: "Docker, Contentores, Microsserviços, Kubernetes, DC/OS, Azure"
ms.assetid: 
ms.service: container-service
ms.devlang: azurecli
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/14/2017
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: dcced32925ecd109be8dde6324cca4899923679b
ms.sourcegitcommit: c5eeb0c950a0ba35d0b0953f5d88d3be57960180
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/24/2017
---
# <a name="deploy-a-kubernetes-cluster-in-azure-container-service"></a>Implementar um cluster de Kubernetes no serviço de contentor do Azure

[!INCLUDE [aks-preview-redirect.md](../../../includes/aks-preview-redirect.md)]

O Kubernetes fornece uma plataforma distribuída para aplicações em contentores. Com o serviço de contentor do Azure, o aprovisionamento de um cluster Kubernetes pronto produção é simples e rápida. Neste tutorial, parte 3 a 7, é implementado num cluster de Kubernetes de serviço de contentor do Azure. Passos concluídos incluem:

> [!div class="checklist"]
> * Implementar um cluster de Kubernetes ACS
> * Instalação da CLI do Kubernetes (kubectl)
> * Configuração de kubectl

Nos tutoriais subsequentes, a aplicação de voto do Azure é implementada para o cluster, ampliada, atualizar e Operations Management Suite está configurado para monitorizar o cluster Kubernetes.

## <a name="before-you-begin"></a>Antes de começar

Nos tutoriais anteriores, uma imagem do contentor foi criada e carregada para uma instância de registo de contentor do Azure. Se não o fez estes passos e gostaria de acompanhar, regresse ao [Tutorial 1 – criar imagens de contentor](./container-service-tutorial-kubernetes-prepare-app.md).

## <a name="create-kubernetes-cluster"></a>Criar cluster do Kubernetes

Crie um cluster do Kubernetes no Azure Container Service com o comando [az acs create](/cli/azure/acs#create). 

O exemplo seguinte cria um cluster com o nome `myK8sCluster` num grupo de recursos com o nome `myResourceGroup`. Este grupo de recursos foi criado no [tutorial anterior](./container-service-tutorial-kubernetes-prepare-acr.md).

```azurecli-interactive 
az acs create --orchestrator-type kubernetes --resource-group myResourceGroup --name myK8SCluster --generate-ssh-keys 
```

Em alguns casos, como numa versão de avaliação limitada, uma subscrição do Azure tem acesso limitado aos recursos do Azure. Caso a implementação falhe devido a um número limitado de núcleos disponíveis, reduza a contagem de agentes pré-definida ao adicionar `--agent-count 1` ao comando [az acs create](/cli/azure/acs#create). 

Após vários minutos, a implementação estiver concluída e formatado de json devolve informações sobre a implementação dos ACS.

## <a name="install-the-kubectl-cli"></a>Instalar o kubectl CLI

Para ligar ao Kubernetes cluster do computador cliente, utilize [kubectl](https://kubernetes.io/docs/user-guide/kubectl/), o cliente de linha de comandos Kubernetes. 

Se estiver a utilizar o Azure CloudShell, kubectl já está instalado. Se pretender instalá-la localmente, utilize o [az acs kubernetes install-cli](/cli/azure/acs/kubernetes#install-cli) comando.

Se em execução no Linux ou macOS, poderá ter de executar com sudo. No Windows, certifique-se de que a shell tiver sido executado como administrador.

```azurecli-interactive 
az acs kubernetes install-cli 
```

No Windows, a instalação predefinida é *c:\program files (x86)\kubectl.exe*. Terá de adicionar este ficheiro para o caminho do Windows. 

## <a name="connect-with-kubectl"></a>Ligar-se com kubectl

Para configurar kubectl para se ligar ao seu cluster do Kubernetes, execute o comando [az acs kubernetes get-credentials](/cli/azure/acs/kubernetes#get-credentials).

```azurecli-interactive 
az acs kubernetes get-credentials --resource-group myResourceGroup --name myK8SCluster
```

Para verificar a ligação ao cluster, execute o [kubectl obter nós](https://kubernetes.io/docs/user-guide/kubectl/v1.6/#get) comando.

```azurecli-interactive
kubectl get nodes
```

Saída:

```bash
NAME                    STATUS                     AGE       VERSION
k8s-agent-98dc3136-0    Ready                      5m        v1.6.2
k8s-agent-98dc3136-1    Ready                      5m        v1.6.2
k8s-agent-98dc3136-2    Ready                      5m        v1.6.2
k8s-master-98dc3136-0   Ready,SchedulingDisabled   5m        v1.6.2
```

Após conclusão tutorial, tem um cluster de ACS Kubernetes pronto para cargas de trabalho. Nos tutoriais subsequentes, uma aplicação de contentor multi é implementada para este cluster, ampliada, atualizada e monitorizada.

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, um cluster de Kubernetes de serviço de contentor do Azure foi implementado. Foram efetuados os seguintes passos:

> [!div class="checklist"]
> * Implementar um cluster de Kubernetes ACS
> * Instalar a CLI de Kubernetes (kubectl)
> * Kubectl configurado

Avançar para o próximo tutorial para saber mais sobre a execução da aplicação no cluster.

> [!div class="nextstepaction"]
> [Implementar a aplicação no Kubernetes](./container-service-tutorial-kubernetes-deploy-application.md)
