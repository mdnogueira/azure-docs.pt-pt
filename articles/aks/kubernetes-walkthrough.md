---
title: "Guia de início rápido – Cluster do Azure Kubernetes para Linux | Microsoft Docs"
description: Saiba mais rapidamente criar um cluster de Kubernetes para contentores de Linux no AKS com a CLI do Azure.
services: container-service
documentationcenter: 
author: neilpeterson
manager: timlt
editor: 
tags: aks, azure-container-service, kubernetes
keywords: 
ms.assetid: 8da267e8-2aeb-4c24-9a7a-65bdca3a82d6
ms.service: container-service
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/15/2017
ms.author: nepeters
ms.custom: H1Hack27Feb2017, mvc, devcenter
ms.openlocfilehash: 84f542340f62194a31817a8e358d75c0d0f103ee
ms.sourcegitcommit: c25cf136aab5f082caaf93d598df78dc23e327b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/15/2017
---
# <a name="deploy-an-azure-container-service-aks-cluster"></a>Implementar um cluster do serviço de contentor do Azure (AKS)

Este guia de introdução, um cluster AKS é implementado utilizando a CLI do Azure. Uma aplicação de contentor multi constituída front-end web e uma instância de Redis, em seguida, é executada no cluster. Depois de concluída, a aplicação está acessível através da Internet.

![Imagem de navegação para o Azure Vote](media/container-service-kubernetes-walkthrough/azure-vote.png)

Este guia de introdução pressupõe um conhecimento básico dos conceitos de Kubernetes, para obter informações detalhadas, consulte Kubernetes o [Kubernetes documentação]( https://kubernetes.io/docs/home/).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se optar por instalar e utilizar a CLI localmente, este guia de introdução requer que está a executar a CLI do Azure versão 2.0.21 ou posterior. Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar, veja [Instalar a CLI do Azure]( /cli/azure/install-azure-cli).

## <a name="enabling-aks-preview-for-your-azure-subscription"></a>Ativar a pré-visualização AKS para a sua subscrição do Azure
Enquanto AKS está em pré-visualização, a criação de novos clusters requer um sinalizador de funcionalidade na sua subscrição. Pode solicitar esta funcionalidade para qualquer número de subscrições de que pretende utilizar. Utilize o `az provider register` comando para registar o fornecedor AKS:

```azurecli-interactive
az provider register -n Microsoft.ContainerService
```

Depois de registar, agora está pronto para criar um cluster de Kubernetes com AKS.

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Crie um grupo de recursos com o comando [az group create](/cli/azure/group#create). Um grupo de recursos do Azure é um grupo lógico, no qual os recursos do Azure são implementados e geridos.

O exemplo seguinte cria um grupo de recursos com o nome *myResourceGroup* na localização *eastus*.

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

Saída:

```json
{
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup",
  "location": "eastus",
  "managedBy": null,
  "name": "myResourceGroup",
  "properties": {
    "provisioningState": "Succeeded"
  },
  "tags": null
}
```

## <a name="create-aks-cluster"></a>Criar AKS cluster

O exemplo seguinte cria um cluster com o nome *myK8sCluster* com um nó.

```azurecli-interactive
az aks create --resource-group myResourceGroup --name myK8sCluster --node-count 1 --generate-ssh-keys
```

Ao fim de vários minutos, o comando é concluído e devolve informações sobre o cluster no formato JSON.

## <a name="connect-to-the-cluster"></a>Ligar ao cluster

Para gerir um cluster de Kubernetes, utilize [kubectl](https://kubernetes.io/docs/user-guide/kubectl/), o cliente de linha de comandos do Kubernetes.

Se estiver a utilizar a Shell de nuvem do Azure, kubectl já está instalado. Se pretender instalá-la localmente, execute o seguinte comando.


```azurecli
az aks install-cli
```

Para configurar kubectl para ligar ao Kubernetes cluster, execute o seguinte comando. Este passo transfere credenciais e configura a CLI do Kubernetes para as utilizar.

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myK8sCluster
```

Para verificar a ligação ao cluster, utilize o comando [kubectl get](https://kubernetes.io/docs/user-guide/kubectl/v1.6/#get) para devolver uma lista de nós do cluster.

```azurecli-interactive
kubectl get nodes
```

Saída:

```
NAME                          STATUS    ROLES     AGE       VERSION
k8s-myk8scluster-36346190-0   Ready     agent     2m        v1.7.7
```

## <a name="run-the-application"></a>Executar a aplicação

Um ficheiro de manifesto do Kubernetes define um estado pretendido para o cluster, incluindo que imagens de contentor devem estar em execução. Neste exemplo, é utilizado um manifesto para criar todos os objetos necessários para executar a aplicação Azure Vote.

Crie um ficheiro denominado `azure-vote.yml` e copie para o seguinte código YAML. Se estiver a trabalhar no Azure Cloud Shell, este ficheiro pode ser criado através de vi ou Nano, como se estivesse a trabalhar num sistema físico ou virtual.

```yaml
apiVersion: apps/v1beta1
kind: Deployment
metadata:
  name: azure-vote-back
spec:
  replicas: 1
  template:
    metadata:
      labels:
        app: azure-vote-back
    spec:
      containers:
      - name: azure-vote-back
        image: redis
        ports:
        - containerPort: 6379
          name: redis
---
apiVersion: v1
kind: Service
metadata:
  name: azure-vote-back
spec:
  ports:
  - port: 6379
  selector:
    app: azure-vote-back
---
apiVersion: apps/v1beta1
kind: Deployment
metadata:
  name: azure-vote-front
spec:
  replicas: 1
  template:
    metadata:
      labels:
        app: azure-vote-front
    spec:
      containers:
      - name: azure-vote-front
        image: microsoft/azure-vote-front:redis-v1
        ports:
        - containerPort: 80
        env:
        - name: REDIS
          value: "azure-vote-back"
---
apiVersion: v1
kind: Service
metadata:
  name: azure-vote-front
spec:
  type: LoadBalancer
  ports:
  - port: 80
  selector:
    app: azure-vote-front
```

Utilize o comando [kubectl create](https://kubernetes.io/docs/user-guide/kubectl/v1.6/#create) para executar a aplicação.

```azurecli-interactive
kubectl create -f azure-vote.yml
```

Saída:

```
deployment "azure-vote-back" created
service "azure-vote-back" created
deployment "azure-vote-front" created
service "azure-vote-front" created
```

## <a name="test-the-application"></a>Testar a aplicação

À medida que a aplicação é executada, é criado um [serviço do Kubernetes](https://kubernetes.io/docs/concepts/services-networking/service/) que expõe o front-end da aplicação na Internet. Este processo pode demorar alguns minutos a concluir.

Para monitorizar o progresso, utilize o comando [kubectl get service](https://kubernetes.io/docs/user-guide/kubectl/v1.6/#get) com o argumento `--watch`.

```azurecli-interactive
kubectl get service azure-vote-front --watch
```

Inicialmente, o *EXTERNAL-IP* do serviço *azure-vote-front* aparece como *pendente*.

```
NAME               TYPE           CLUSTER-IP   EXTERNAL-IP   PORT(S)        AGE
azure-vote-front   LoadBalancer   10.0.37.27   <pending>     80:30572/TCP   6s
```

Uma vez a *IP externo* endereço foi alterado de *pendente* para um *endereço IP*, utilize `CTRL-C` parar o processo de veja kubectl.

```
azure-vote-front   LoadBalancer   10.0.37.27   52.179.23.131   80:30572/TCP   2m
```

Agora, pode navegar para o endereço IP externo para ver a aplicação Azure Vote.

![Imagem de navegação para o Azure Vote](media/container-service-kubernetes-walkthrough/azure-vote.png)

## <a name="delete-cluster"></a>Eliminar o cluster
Quando o cluster já não for necessário, pode utilizar o comando [az group delete](/cli/azure/group#delete) para remover o grupo de recursos, o serviço de contentores e todos os recursos relacionados.

```azurecli-interactive
az group delete --name myResourceGroup --yes --no-wait
```

## <a name="get-the-code"></a>Obter o código

Neste guia de introdução, contentor pré-criadas imagens foram utilizadas para criar uma implementação de Kubernetes. O código da aplicação relacionado, o Dockerfile, e o ficheiro de manifesto do Kubernetes, estão disponíveis no GitHub.

[https://github.com/Azure-Samples/azure-voting-app-redis](https://github.com/Azure-Samples/azure-voting-app-redis.git)

## <a name="next-steps"></a>Passos seguintes

Neste guia de início rápido, implementou um cluster do Kubernetes e implementou uma aplicação de vários contentores no mesmo.

Para saber mais sobre AKS e guiá-lo através de um código de conclusão exemplo de implementação, avance para o tutorial de cluster Kubernetes.

> [!div class="nextstepaction"]
> [Gerir um cluster AKS](./tutorial-kubernetes-prepare-app.md)
