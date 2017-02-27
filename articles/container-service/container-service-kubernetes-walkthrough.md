---
title: "Início rápido do cluster do Kubernetes no Azure | Microsoft Docs"
description: "Implementar e começar a utilizar um cluster do Kubernetes no Azure Container Service"
services: container-service
documentationcenter: 
author: anhowe
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
ms.date: 02/21/2017
ms.author: anhowe
translationtype: Human Translation
ms.sourcegitcommit: 2a381431acb6436ddd8e13c69b05423a33cd4fa6
ms.openlocfilehash: 1742a6d4d99b81509564696e6faaf9e6fbf8f604


---

# <a name="azure-container-service---kubernetes-walkthrough"></a>Azure Container Service - instruções do Kubernetes


As instruções neste artigo mostram como utilizar os comandos do Azure CLI 2.0 para criar um cluster do Kubernetes. Em seguida, poderá utilizar a ferramenta da linha de comandos `kubectl` para começar a trabalhar com contentores no cluster.

A imagem seguinte mostra a arquitetura de um cluster de serviço de contentor com um mestre e dois agentes. O mestre serve a API REST do Kubernetes. Os nós de agente estão agrupados num conjunto de disponibilidade do Azure e executam os contentores. Todas as VMs estão na mesma rede virtual privada e totalmente acessíveis entre si.

![Imagem do cluster do Kubernetes no Azure no Azure](media/container-service-kubernetes-walkthrough/kubernetes.png)

## <a name="prerequisites"></a>Pré-requisitos
Estas instruções partem dos princípio de quem instalado e configurado o [Azure CLI v. 2.0](/cli/azure/install-az-cli2). Também tem de ter uma chave pública RSA SSH em `~/.ssh/id_rsa.pub`. Se não tiver uma, veja os passos para [OS X e Linux](../virtual-machines/virtual-machines-linux-mac-create-ssh-keys.md) ou [Windows](../virtual-machines/virtual-machines-linux-ssh-from-windows.md).






## <a name="create-your-kubernetes-cluster"></a>Criar o cluster do Kubernetes

Eis alguns comandos breves da shell utilizando o Azure CLI 2.0 para criar o cluster. Para obter mais informações, veja [Utilizar o Azure CLI 2.0 para criar um cluster do Azure Container Service](container-service-create-acs-cluster-cli.md).

### <a name="create-a-resource-group"></a>Criar um grupo de recursos
Para criar o cluster, tem primeiro de criar um grupo de recursos numa localização específica. Execute comandos semelhantes ao seguinte:

```console
RESOURCE_GROUP=my-resource-group
LOCATION=westus
az group create --name=$RESOURCE_GROUP --location=$LOCATION
```

### <a name="create-a-cluster"></a>Criar um cluster
Depois de ter um grupo de recursos, pode criar um cluster nesse grupo:

```console
DNS_PREFIX=some-unique-value
CLUSTER_NAME=any-acs-cluster-name
az acs create --orchestrator-type=kubernetes --resource-group $RESOURCE_GROUP --name=$CLUSTER_NAME --dns-prefix=$DNS_PREFIX
```

> [!NOTE]
> Durante a implementação, a CLI carrega `~/.ssh/id_rsa.pub` para as VMs do Linux.
>

Assim que esse comando estiver concluído, deverá ter um cluster do Kubernetes em funcionamento.

### <a name="connect-to-the-cluster"></a>Ligar ao cluster

Seguem-se os comandos CLI do Azure para ligar ao cluster do Kubernetes a partir do computador cliente, utilizando `kubectl`, o cliente da linha de comandos do Kubernetes. Para mais informações, consulte [Connect to an Azure Container Service cluster (Ligar a um cluster do Azure Container Service)](container-service-connect.md).

Se ainda não tiver o `kubectl` instalado, pode instalá-lo com:

```console
az acs kubernetes install-cli
```

Depois de o `kubectl` estar instalado, execute o comando seguinte para transferir a configuração de cluster do Kubernetes principal para o ficheiro ~/.kube/config:

```console
az acs kubernetes get-credentials --resource-group=$RESOURCE_GROUP --name=$SERVICE_NAME
```

Neste momento, deve estar pronto para aceder ao cluster a partir do seu computador. Tente executar:
```console
kubectl get nodes
```

Verifique se consegue ver uma lista das máquinas no cluster.

## <a name="create-your-first-kubernetes-service"></a>Criar o primeiro serviço do Kubernetes

Depois de concluir estas instruções, saberá como:
 * implementar uma aplicação Docker e expô-la a todo o mundo
 * utilizar `kubectl exec` para executar comandos num contentor 
 * aceder ao dashboard do Kubernetes

### <a name="start-a-simple-container"></a>Iniciar um contentor simples
Pode executar um contentor simples (neste caso, o servidor Web Nginx) ao executar:

```console
kubectl run nginx --image nginx
```

Este comando inicia o contentor Docker Nginx num pod num dos nós.

Para ver o contentor em execução, execute:

```console
kubectl get pods
```

### <a name="expose-the-service-to-the-world"></a>Expor o serviço ao mundo
Para expor o serviço ao mundo, crie um Kubernetes `Service` do tipo `LoadBalancer`:

```console
kubectl expose deployments nginx --port=80 --type=LoadBalancer
```

Isto faz com que o Kubernetes crie um balanceador de carga do Azure com um endereço IP público. A alteração demora alguns minutos a ser propagada para o balanceador de carga. Para obter mais informações, veja [Contentores de balanceamento de carga num cluster do Kubernetes no Azure Container Service](container-service-kubernetes-load-balancing.md).

Execute o seguinte comando para ver o serviço alterar de `pending` para apresentar um endereço IP externo:

```console
watch 'kubectl get svc'
```

  ![Imagem da visualização da transição de pendente para endereço IP externo](media/container-service-kubernetes-walkthrough/kubernetes-nginx3.png)

Quando vir o endereço IP externo, pode navegá-lo no seu browser:

  ![Imagem de navegação para Nginx](media/container-service-kubernetes-walkthrough/kubernetes-nginx4.png)  


### <a name="browse-the-kubernetes-ui"></a>Navegar na IU do Kubernetes
Para ver a interface Web do Kubernetes, pode utilizar:

```console
kubectl proxy
```
É executado um proxy autenticado simples no localhost, que pode utilizar para ver a [IU Web do Kubernetes](http://localhost:8001/ui). Para obter mais informações, veja [Utilizar a IU Web do Kubernetes com o Azure Container Service](container-service-kubernetes-ui.md).

![Imagem do dashboard do Kubernetes](media/container-service-kubernetes-walkthrough/kubernetes-dashboard.png)

### <a name="remote-sessions-inside-your-containers"></a>Sessões remotas nos contentores
O Kubernetes permite executar comandos num contentor Docker remoto em execução no cluster.

```console
# Get the name of your nginx pods
kubectl get pods
```

Ao utilizar o seu nome de pod, pode executar um comando remoto no seu pod.  Por exemplo:

```console
kubectl exec nginx-701339712-retbj date
```

Também pode aceder uma sessão totalmente interativa através dos sinalizadores `-it`:

```console
kubectl exec nginx-701339712-retbj -it bash
```

![Sessão remota dentro de um contentor](media/container-service-kubernetes-walkthrough/kubernetes-remote.png)



## <a name="next-steps"></a>Passos seguintes

Para fazer mais com o seu cluster do Kubernetes, veja os seguintes recursos:

* [Bootcamp do Kubernetes](https://katacoda.com/embed/kubernetes-bootcamp/1/) – Mostra como implementar, dimensionar, atualizar e depurar aplicações em contentores.
* [Guia de Utilizador do Kubernetes](http://kubernetes.io/docs/user-guide/) – Fornece informações sobre como executar programas num cluster do Kubernetes existente.
* [Exemplos do Kubernetes](https://github.com/kubernetes/kubernetes/tree/master/examples) – Fornece exemplos sobre como executar aplicações reais com o Kubernetes.



<!--HONumber=Feb17_HO4-->


