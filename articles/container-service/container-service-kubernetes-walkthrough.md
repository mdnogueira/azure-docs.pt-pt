---
title: "Guia de início rápido – Cluster do Azure Kubernetes para Linux | Microsoft Docs"
description: Aprenda rapidamente a criar um cluster do Kubernetes para contentores do Linux no Azure Container Service com a CLI do Azure.
services: container-service
documentationcenter: 
author: neilpeterson
manager: timlt
editor: 
tags: acs, azure-container-service, kubernetes
keywords: 
ms.assetid: 8da267e8-2aeb-4c24-9a7a-65bdca3a82d6
ms.service: container-service
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/18/2017
ms.author: nepeters
ms.custom: H1Hack27Feb2017
ms.translationtype: HT
ms.sourcegitcommit: 26c07d30f9166e0e52cb396cdd0576530939e442
ms.openlocfilehash: 3be2079d205d6bfd4c796e5f6abcd7ac5fe595a2
ms.contentlocale: pt-pt
ms.lasthandoff: 07/19/2017

---

# <a name="deploy-kubernetes-cluster-for-linux-containers"></a>Implementar o cluster do Kubernetes para contentores do Linux

A CLI do Azure é utilizada para criar e gerir recursos do Azure a partir da linha de comandos ou em scripts. Este guia explica detalhadamente como deve utilizar a CLI do Azure para implementar um cluster do [Kubernetes](https://kubernetes.io/docs/home/) no [Azure Container Service](container-service-intro.md). Depois de o cluster ser implementado, ligue-se à ferramenta da linha de comandos do Kubernetes `kubectl` e implemente o seu primeiro contentor do Linux.

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se optar por instalar e usar a CLI localmente, este tópico requer a execução da versão 2.0.4 ou posterior da CLI do Azure. Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar, veja [instalar o Azure CLI 2.0]( /cli/azure/install-azure-cli). 

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Crie um grupo de recursos com o comando [az group create](/cli/azure/group#create). Um grupo de recursos do Azure é um grupo lógico, no qual os recursos do Azure são implementados e geridos. 

O exemplo seguinte cria um grupo de recursos com o nome *myResourceGroup* na localização *eastus*.

```azurecli-interactive 
az group create --name myResourceGroup --location eastus
```

## <a name="create-kubernetes-cluster"></a>Criar cluster do Kubernetes
Crie um cluster do Kubernetes no Azure Container Service com o comando [az acs create](/cli/azure/acs#create). 

O exemplo seguinte cria um cluster denominado *myK8sCluster* com um nó principal do Linux e dois nós de agente do Linux. Este exemplo cria chaves SSH, caso ainda não existam nas localizações predefinidas. Para utilizar um conjunto específico de chaves, utilize a opção `--ssh-key-value`. Atualize o nome do cluster para algo adequado ao ambiente. 



```azurecli-interactive 
az acs create --orchestrator-type=kubernetes \
    --resource-group myResourceGroup \
    --name=myK8sCluster \
    --generate-ssh-keys 
```

Após alguns minutos, o comando é concluído e apresenta informações sobre a implementação.

## <a name="install-kubectl"></a>Instalar o kubectl

Para ligar ao cluster de Kubernetes a partir do computador cliente, utilize [`kubectl`](https://kubernetes.io/docs/user-guide/kubectl/), o cliente de linha de comandos do Kubernetes. 

Se estiver a utilizar o Azure CloudShell, `kubectl` já está instalado. Se pretender instalá-lo localmente, pode utilizar o comando [az acs kubernetes install-cli](/cli/azure/acs/kubernetes#install-cli).

O exemplo seguinte da CLI do Azure instala `kubectl` no seu sistema. Se estiver a executar a CLI do Azure no macOS ou Linux, é necessário executar o comando com `sudo`.

```azurecli-interactive 
az acs kubernetes install-cli 
```

## <a name="connect-with-kubectl"></a>Ligar-se com kubectl

Para configurar `kubectl` para se ligar ao cluster do Kubernetes, execute o comando [az acs kubernetes get-credentials](/cli/azure/acs/kubernetes#get-credentials) . O exemplo seguinte transfere a configuração do cluster para o cluster do Kubernetes.

```azurecli-interactive 
az acs kubernetes get-credentials --resource-group=myResourceGroup --name=myK8sCluster
```

Para verificar a ligação ao seu cluster a partir do seu computador, tente executar:

```azurecli-interactive
kubectl get nodes
```

`kubectl` lista os nós principais e do agente.

```azurecli-interactive
NAME                    STATUS                     AGE       VERSION
k8s-agent-98dc3136-0    Ready                      5m        v1.5.3
k8s-agent-98dc3136-1    Ready                      5m        v1.5.3
k8s-master-98dc3136-0   Ready,SchedulingDisabled   5m        v1.5.3

```


## <a name="deploy-an-nginx-container"></a>Implementar o contentor do NGINX

Pode executar um contentor do Docker dentro de um *pod* do Kubernetes, que contém um ou mais contentores. 

O comando seguinte inicia o contentor Docker NGINX num pod do Kubernetes, num dos nós. Neste caso, o contentor executa o servidor Web NGINX extraído de uma imagem no [Hub do Docker](https://hub.docker.com/_/nginx/).

```azurecli-interactive
kubectl run nginx --image nginx
```
Para verificar se o contentor está a ser executado, execute:

```azurecli-interactive
kubectl get pods
```

## <a name="view-the-nginx-welcome-page"></a>Ver a página de boas-vindas do NGINX
Para expor o servidor NGINX ao mundo com um endereço IP público, digite o seguinte comando:

```azurecli-interactive
kubectl expose deployments nginx --port=80 --type=LoadBalancer
```

Com este comando, o Kubernetes cria um serviço e uma [regra do balanceador de carga do Azure](container-service-kubernetes-load-balancing.md) com um endereço IP público para o serviço. 

Execute o seguinte comando para ver o estado do serviço.

```azurecli-interactive
kubectl get svc
```

Inicialmente, o endereço IP é apresentado como `pending`. Depois de alguns minutos, o endereço IP externo do serviço é definido:
  
```azurecli-interactive
NAME         CLUSTER-IP     EXTERNAL-IP     PORT(S)        AGE       
kubernetes   10.0.0.1       <none>          443/TCP        21h       
nginx        10.0.111.25    52.179.3.96     80/TCP         22m
```

Pode utilizar um browser à sua escolha para ver a página de boas-vinda predefinida do NGINX no endereço IP externo:

![Imagem de navegação para Nginx](media/container-service-kubernetes-walkthrough/kubernetes-nginx4.png)  


## <a name="delete-cluster"></a>Eliminar o cluster
Quando o cluster já não for necessário, pode utilizar o comando [az group delete](/cli/azure/group#delete) para remover o grupo de recursos, o serviço de contentores e todos os recursos relacionados.

```azurecli-interactive 
az group delete --name myResourceGroup
```


## <a name="next-steps"></a>Passos seguintes

Neste guia de início rápido, implementou um cluster do Kubernetes, ligado a `kubectl`, e implementou um pod com um contentor NGINX. Para saber mais sobre o Azure Container Service, continue o tutorial do cluster do Kubernetes.

> [!div class="nextstepaction"]
> [Gerir um cluster do ACS Kubernetes](./container-service-tutorial-kubernetes-prepare-app.md)

