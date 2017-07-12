---
title: "Guia de início rápido - Cluster do Azure Kubernetes para Windows | Microsoft Docs"
description: Aprenda rapidamente a criar um cluster do Kubernetes para contentores do Windows no Azure Container Service com a CLI do Azure.
documentationcenter: 
author: dlepow
manager: timlt
editor: 
tags: acs, azure-container-service, kubernetes
keywords: 
ms.assetid: 
ms.service: container-service
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/31/2017
ms.author: danlep
ms.custom: H1Hack27Feb2017
ms.translationtype: Human Translation
ms.sourcegitcommit: 7948c99b7b60d77a927743c7869d74147634ddbf
ms.openlocfilehash: 929a4dec638da9488dd0b43fd123ed0cce77bcf3
ms.contentlocale: pt-pt
ms.lasthandoff: 06/20/2017


---

<a id="deploy-kubernetes-cluster-for-windows-containers" class="xliff"></a>

# Implementar o cluster do Kubernetes para contentores do Windows

A CLI do Azure é utilizada para criar e gerir recursos do Azure a partir da linha de comandos ou em scripts. Este guia explica detalhadamente como deve utilizar a CLI do Azure para implementar um cluster do [Kubernetes](https://kubernetes.io/docs/home/) no [Azure Container Service](container-service-intro.md). Depois de o cluster ser implementado, ligue-se à ferramenta da linha de comandos do Kubernetes `kubectl` e implemente o seu primeiro contentor do Windows.

Este tutorial requer a versão do módulo 2.0.4 ou posterior da CLI do Azure. Executar `az --version` para localizar a versão. Se precisar de atualizar, veja [instalar o Azure CLI 2.0]( /cli/azure/install-azure-cli). 

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se não tiver uma subscrição do Azure, crie uma conta [gratuita](https://azure.microsoft.com/free/) antes de começar.

> [!NOTE]
> O suporte para contentores do Windows com Kubernetes no Azure Container Service está em pré-visualização. 
>

<a id="log-in-to-azure" class="xliff"></a>

## Iniciar sessão no Azure 

Inicie sessão na sua subscrição do Azure com o comando [az login](/cli/azure/#login) e siga as instruções no ecrã.

```azurecli-interactive 
az login
```

<a id="create-a-resource-group" class="xliff"></a>

## Criar um grupo de recursos

Crie um grupo de recursos com o comando [az group create](/cli/azure/group#create). Um grupo de recursos do Azure é um grupo lógico, no qual os recursos do Azure são implementados e geridos. 

O exemplo seguinte cria um grupo de recursos com o nome *myResourceGroup* na localização *eastus*.

```azurecli-interactive 
az group create --name myResourceGroup --location eastus
```

<a id="create-kubernetes-cluster" class="xliff"></a>

## Criar cluster do Kubernetes
Crie um cluster do Kubernetes no Azure Container Service com o comando [az acs create](/cli/azure/acs#create). 

O exemplo seguinte cria um cluster denominado *myK8sCluster* com um nó principal do Linux e dois nós de agente do Linux. Este exemplo cria chaves de SSH necessárias para se ligar ao Linux Master. Este exemplo utiliza *azureuser* para um nome de utilizador administrativo e *myPassword12* como palavra-passe nos nós do Windows. Atualize estes valores para algo adequado ao ambiente. 



```azurecli-interactive 
az acs create --orchestrator-type=kubernetes \
    --resource-group myResourceGroup \
    --name=myK8sCluster \
    --agent-count=2 \
    --generate-ssh-keys \
    --windows --admin-username azureuser \
    --admin-password myPassword12
```

Após alguns minutos, o comando é concluído e apresenta informações sobre a implementação.

<a id="install-kubectl" class="xliff"></a>

## Instalar o kubectl

Para ligar ao cluster de Kubernetes a partir do computador cliente, utilize [`kubectl`](https://kubernetes.io/docs/user-guide/kubectl/), o cliente de linha de comandos do Kubernetes. 

Se estiver a utilizar o Azure CloudShell, `kubectl` já está instalado. Se pretender instalá-lo localmente, pode utilizar o comando [az acs kubernetes install-cli](/cli/azure/acs/kubernetes#install-cli).

O exemplo seguinte da CLI do Azure instala `kubectl` no seu sistema. No Windows, execute este comando como administrador.

```azurecli-interactive 
az acs kubernetes install-cli
```


<a id="connect-with-kubectl" class="xliff"></a>

## Ligar-se com kubectl

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

<a id="deploy-a-windows-iis-container" class="xliff"></a>

## Implementar um contentor do IIS do Windows

Pode executar um contentor do Docker dentro de um *pod* do Kubernetes, que contém um ou mais contentores. 

Este exemplo básico utiliza um ficheiro JSON para especificar um contentor do Microsoft Internet Information Server (IIS) e, em seguida, cria o pod com o comando `kubctl apply`. 

Crie um ficheiro local com o nome `iis.json` e copie o seguinte texto. Este ficheiro indica ao Kubernetes para executar o IIS no Windows Server 2016 Nano Server, com uma imagem pública do contentor a partir do [Hub do Docker](https://hub.docker.com/r/nanoserver/iis/). O contentor utiliza a porta 80, mas, inicialmente, só está acessível dentro da rede de cluster.

 ```JSON
 {
  "apiVersion": "v1",
  "kind": "Pod",
  "metadata": {
    "name": "iis",
    "labels": {
      "name": "iis"
    }
  },
  "spec": {
    "containers": [
      {
        "name": "iis",
        "image": "nanoserver/iis",
        "ports": [
          {
          "containerPort": 80
          }
        ]
      }
    ],
    "nodeSelector": {
     "beta.kubernetes.io/os": "windows"
     }
   }
 }
 ```

Para iniciar o pod, introduza:
  
```azurecli-interactive
kubectl apply -f iis.json
```  

Para controlar a implantação, introduza:
  
```azurecli-interactive
kubectl get pods
```

Enquanto o pod está a ser implementado, o estado é `ContainerCreating`. Pode levar alguns minutos até o contentor entrar no estado `Running`.

```azurecli-interactive
NAME     READY        STATUS        RESTARTS    AGE
iis      1/1          Running       0           32s
```

<a id="view-the-iis-welcome-page" class="xliff"></a>

## Ver a página de boas-vindas do IIS

Para expor o pod ao mundo com um endereço IP público, introduza o seguinte comando:

```azurecli-interactive
kubectl expose pods iis --port=80 --type=LoadBalancer
```

Com este comando, o Kubernetes cria um serviço e uma [regra do balanceador de carga do Azure](container-service-kubernetes-load-balancing.md) com um endereço IP público para o serviço. 

Execute o seguinte comando para ver o estado do serviço.

```azurecli-interactive
kubectl get svc
```

Inicialmente, o endereço IP é apresentado como `pending`. Depois de alguns minutos, o endereço IP externo do pod `iis` é definido:
  
```azurecli-interactive
NAME         CLUSTER-IP     EXTERNAL-IP     PORT(S)        AGE       
kubernetes   10.0.0.1       <none>          443/TCP        21h       
iis          10.0.111.25    13.64.158.233   80/TCP         22m
```

Pode utilizar um browser à sua escolha para ver a página de boas-vindas predefinida do IIS no endereço IP externo:

![Imagem de navegação para IIS](media/container-service-kubernetes-windows-walkthrough/kubernetes-iis.png)  


<a id="delete-cluster" class="xliff"></a>

## Eliminar o cluster
Quando o cluster já não for necessário, pode utilizar o comando [az group delete](/cli/azure/group#delete) para remover o grupo de recursos, o serviço de contentores e todos os recursos relacionados.

```azurecli-interactive 
az group delete --name myResourceGroup
```


<a id="next-steps" class="xliff"></a>

## Passos seguintes

Neste guia de início rápido, implementou um cluster do Kubernetes, ligado a `kubectl`, e implementou um pod com um contentor IIS. Para saber mais sobre o Azure Container Service, continue o tutorial do Kubernetes.

> [!div class="nextstepaction"]
> [Gerir um cluster do ACS Kubernetes](./container-service-tutorial-kubernetes-prepare-app.md)

