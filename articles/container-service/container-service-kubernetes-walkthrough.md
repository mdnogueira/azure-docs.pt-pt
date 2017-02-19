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
ms.date: 11/15/2016
ms.author: anhowe
translationtype: Human Translation
ms.sourcegitcommit: 7ed8fb75f057d5a7cfde5436e72e8fec52d07156
ms.openlocfilehash: f3b2fc301bf7083f192c0ec872c4e032472eef97


---

# <a name="microsoft-azure-container-service-engine---kubernetes-walkthrough"></a>Motor do Microsoft Azure Container Service – Instruções do Kubernetes

## <a name="prerequisites"></a>Pré-requisitos
Estas instruções assumem que tem a [ferramenta de linha de comandos 'azure cli'](https://github.com/azure/azure-cli#installation) instalada e a [chave pública SSH](../virtual-machines/virtual-machines-linux-mac-create-ssh-keys.md) criada em `~/.ssh/id_rsa.pub`.

## <a name="overview"></a>Descrição geral

As instruções abaixo irão criar um cluster do Kubernetes com um mestre e dois nós de trabalho.
O mestre serve a API REST do Kubernetes.  Os nós de trabalho estão agrupados num conjunto de disponibilidade do Azure e executam os contentores. Todas as VMs estão na mesma VNET privada e totalmente acessíveis entre si.

> [!NOTE]
> O suporte de Kubernetes no Azure Container Service está atualmente em pré-visualização.
>

A imagem seguinte mostra a arquitetura de um cluster de serviço de contentor com um mestre e dois agentes:

![Imagem do cluster do Kubernetes no Azure](media/container-service-kubernetes-walkthrough/kubernetes.png)

## <a name="creating-your-kubernetes-cluster"></a>Criar o cluster do Kubernetes

### <a name="create-a-resource-group"></a>Criar um grupo de recursos
Para criar o cluster, tem primeiro de criar um grupo de recursos numa localização específica. Pode fazê-lo com:
```console
RESOURCE_GROUP=my-resource-group
LOCATION=westus
az group create --name=$RESOURCE_GROUP --location=$LOCATION
```

### <a name="create-a-cluster"></a>Criar um cluster
Depois de ter um grupo de recursos, pode criar um cluster nesse grupo com:
```console
DNS_PREFIX=some-unique-value
SERVICE_NAME=any-acs-service-name
az acs create --orchestrator-type=kubernetes --resource-group $RESOURCE_GROUP --name=$SERVICE_NAME --dns-prefix=$DNS_PREFIX
```

> [!NOTE]
> azure-cli irá carregar `~/.ssh/id_rsa.pub` pas as VMs do Linux.
>

Assim que esse comando estiver concluído, deverá ter um cluster do Kubernetes em funcionamento.

### <a name="configure-kubectl"></a>Configurar kubectl
`kubectl` o cliente de linha de comandos do Kubernetes.  Se ainda não o tiver instalado, pode instalá-lo com:

```console
az acs kubernetes install-cli
```

Depois de o `kubectl` estar instalado, a execução do comando abaixo transferirá a configuração de clusters do Kubernetes principal para o ficheiro ~/.kube/config
```console
az acs kubernetes get-credentials --resource-group=$RESOURCE_GROUP --name=$SERVICE_NAME
```

Neste momento, deve estar pronto para aceder ao cluster a partir do seu computador. Tente executar:
```console
kubectl get nodes
```

E validar que consegue ver as máquinas no cluster.

## <a name="create-your-first-kubernetes-service"></a>Criar o Primeiro Serviço do Kubernetes

Depois de concluir estas instruções, saberá como:
 * implementar uma aplicação Docker e expor a todo o mundo,
 * utilizar `kubectl exec` para executar os comandos num contentor 
 * e aceder ao dashboard do Kubernetes.

### <a name="start-a-simple-container"></a>Iniciar um contentor simples
Pode executar um contentor simples (neste caso, o servidor Web `nginx`) ao executar:

```console
kubectl run nginx --image nginx
```

Este comando inicia o contentor Docker nginx num pod num dos nós.

Pode executar
```console
kubectl get pods
```

para ver o contentor em execução.

### <a name="expose-the-service-to-the-world"></a>Expor o serviço ao mundo
Para expor o serviço ao mundo.  Crie um `Service` do Kubernetes do tipo `LoadBalancer`:

```console
kubectl expose deployments nginx --port=80 --type=LoadBalancer
```

Isto fará com que o Kubernetes crie um Balanceador de Carga do Azure com um IP público. A alteração demora cerca de 2 a 3 minutos a ser propagada para o balanceador de carga.

Para ver o serviço mudar de "pendente" para um tipo de IP externo:
```console
watch 'kubectl get svc'
```

  ![Imagem a ver a transição de pendente para IP externo](media/container-service-kubernetes-walkthrough/kubernetes-nginx3.png)

Quando vir o IP externo, pode procurá-lo no seu browser:

  ![Imagem de navegar para nginx](media/container-service-kubernetes-walkthrough/kubernetes-nginx4.png)  


### <a name="browse-the-kubernetes-ui"></a>Navegar na IU do Kubernetes
Para ver a interface Web do Kubernetes, pode utilizar:

```console
kubectl proxy
```
É executado um proxy autenticado simples no localhost, que pode utilizar para ver a [IU do Kubernetes](http://localhost:8001/ui)

![Imagem do dashboard do Kubernetes](media/container-service-kubernetes-walkthrough/kubernetes-dashboard.png)

### <a name="remote-sessions-inside-your-containers"></a>Sessões remotas nos contentores
O Kubernetes permite executar comandos num contentor Docker remoto em execução no cluster.

```console
# Get the name of your nginx pod
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

![Imagem de curl para podIP](media/container-service-kubernetes-walkthrough/kubernetes-remote.png)


## <a name="details"></a>Detalhes
### <a name="installing-the-kubectl-configuration-file"></a>Instalar o ficheiro de configuração kubectl
Quando executar `az acs kubernetes get-credentials`, o ficheiro de configuração kube do acesso remoto foi armazenado no diretório raiz ~/.kube/config.

Se alguma vez precisar de o transferir diretamente, pode utilizar `ssh` no Linux ou OS X, ou `Putty` no Windows:

#### <a name="windows"></a>Windows
Para utilizar pscp a partir de [putty](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html).  Certifique-se de que tem o seu certificado exposto através de [pageant](https://github.com/Azure/acs-engine/blob/master/docs/ssh.md#key-management-and-agent-forwarding-with-windows-pageant):
  ```
  # MASTERFQDN is obtained in step1
  pscp azureuser@MASTERFQDN:.kube/config .
  SET KUBECONFIG=%CD%\config
  kubectl get nodes
  ```

#### <a name="os-x-or-linux"></a>OS X ou Linux:
  ```
  # MASTERFQDN is obtained in step1
  scp azureuser@MASTERFQDN:.kube/config .
  export KUBECONFIG=`pwd`/config
  kubectl get nodes
  ```
## <a name="learning-more"></a>Saber Mais

### <a name="azure-container-service"></a>Azure Container Service

1. [Documentação do Azure Container Service](https://azure.microsoft.com/en-us/documentation/services/container-service/)
2. [Motor Open Source do Azure Container Service](https://github.com/azure/acs-engine)

### <a name="kubernetes-community-documentation"></a>Documentação da Comunidade do Kubernetes

1. [Bootcamp do Kubernetes](https://katacoda.com/embed/kubernetes-bootcamp/1/) – Mostra como implementar, dimensionar, atualizar e depurar aplicações em contentores.
2. [Guia de Utilizador do Kubernetes](http://kubernetes.io/docs/user-guide/) – Fornece informações sobre como executar programas num cluster do Kubernetes existente.
3. [Exemplos do Kubernetes](https://github.com/kubernetes/kubernetes/tree/master/examples) – Fornece vários exemplos sobre como executar aplicações reais com o Kubernetes.



<!--HONumber=Jan17_HO4-->


