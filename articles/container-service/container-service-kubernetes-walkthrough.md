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
ms.date: 05/08/2017
ms.author: anhowe
ms.custom: H1Hack27Feb2017
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 2ec155129374c03ba7e0ecaa5d2bf29a1d3111aa
ms.contentlocale: pt-pt
ms.lasthandoff: 05/10/2017

---

# <a name="get-started-with-a-kubernetes-cluster-in-container-service"></a>Começar a utilizar um cluster Kubernetes no Container Service


Estas instruções mostram como utilizar os comandos da CLI 2.0 do Azure para criar um cluster de Kubernetes no Azure Container Service. Em seguida, poderá utilizar a ferramenta da linha de comandos `kubectl` para começar a trabalhar com contentores no cluster.

A imagem seguinte mostra a arquitetura de um cluster de Serviço de Contentor com um mestre Linux e dois agentes Linux. O mestre serve a API REST do Kubernetes. Os nós de agente estão agrupados num conjunto de disponibilidade do Azure e executam os contentores. Todas as VMs estão na mesma rede virtual privada e totalmente acessíveis entre si.

![Imagem do cluster do Kubernetes no Azure no Azure](media/container-service-kubernetes-walkthrough/kubernetes.png)

Para ter mais contexto, veja o artigo [Introdução ao Azure Container Service](container-service-intro.md) e a [documentação do Kubernetes](https://kubernetes.io/docs/home/).

## <a name="prerequisites"></a>Pré-requisitos
Para criar um cluster do Azure Container Service com a CLI do Azure 2.0, tem de:
* ter uma conta do Azure ([obter uma avaliação gratuita](https://azure.microsoft.com/pricing/free-trial/))
* ter instalado e configurado a [CLI do Azure 2.0](/cli/azure/install-az-cli2)

Além disso, precisa de (ou pode utilizar a CLI do Azure para gerar automaticamente durante a implementação do cluster):

* **Chave pública RSA SSH**: se pretender criar chaves RSA Secure Shell (SSH) com antecedência, veja as orientações para [macOS e Linux](../virtual-machines/linux/mac-create-ssh-keys.md) ou [Windows](../virtual-machines/linux/ssh-from-windows.md). 

* **ID e segredo de cliente de principal de serviço**: para ver os passos para criar um principal de serviço do Azure Active Directory e obter informações adicionais, veja [Sobre o principal de serviço para um cluster de Kubernetes](container-service-kubernetes-service-principal.md).

 O exemplo de comando neste artigo gera automaticamente as chaves SSH e um principal de serviço.

## <a name="create-your-kubernetes-cluster"></a>Criar o cluster do Kubernetes

Eis alguns comandos breves da shell bash com o Azure CLI 2.0 para criar o cluster. 

### <a name="create-a-resource-group"></a>Criar um grupo de recursos
Para criar o seu cluster, primeiro tem de criar um grupo de recursos numa localização onde o Azure Container Service esteja [disponível](https://azure.microsoft.com/regions/services/). Execute comandos semelhantes ao seguinte:

```azurecli
RESOURCE_GROUP=my-resource-group
LOCATION=westus
az group create --name=$RESOURCE_GROUP --location=$LOCATION
```

### <a name="create-a-cluster"></a>Criar um cluster
Crie um cluster de Kubernetes no seu grupo de recursos, utilizando o comando `az acs create` com `--orchestrator-type=kubernetes`. Para ver a sintaxe do comando, veja a [ajuda](/cli/azure/acs#create) do comando `az acs create`.

Esta versão do comando gera automaticamente as chaves RSA SSH e o principal de serviço para o cluster de Kubernetes.



```azurecli
DNS_PREFIX=some-unique-value
CLUSTER_NAME=any-acs-cluster-name
az acs create --orchestrator-type=kubernetes --resource-group $RESOURCE_GROUP --name=$CLUSTER_NAME --dns-prefix=$DNS_PREFIX --generate-ssh-keys
```

Após vários minutos, o comando é concluído e deve ter um cluster de Kubernetes em funcionamento.

> [!IMPORTANT]
> Se a sua conta não tiver permissões para criar o principal de serviço do Azure AD, o comando gera um erro semelhante a `Insufficient privileges to complete the operation.`. Para obter mais informações, veja [Sobre o principal de serviço para um cluster de Kubernetes](container-service-kubernetes-service-principal.md).
> 



### <a name="connect-to-the-cluster"></a>Ligar ao cluster

Para ligar ao cluster do Kubernetes a partir do computador cliente, utiliza [`kubectl`](https://kubernetes.io/docs/user-guide/kubectl/), o cliente da linha de comandos do Kubernetes. 

Se ainda não tiver o `kubectl` instalado, pode instalá-lo com o `az acs kubernetes install-cli`. (Também pode transferi-lo a partir do [site do Kubernetes](https://kubernetes.io/docs/tasks/kubectl/install/).)

```azurecli
sudo az acs kubernetes install-cli
```

> [!TIP]
> Por predefinição, este comando instala o binário `kubectl` para `/usr/local/bin/kubectl` num sistema Linux ou macOS ou para `C:\Program Files (x86)\kubectl.exe` no Windows. Para especificar um caminho de instalação diferente, utilize o parâmetro `--install-location`.
>
> Após `kubectl` estar instalado, certifique-se de que o seu diretório está no caminho do sistema ou adicione-o ao mesmo. 
>


Em seguida, execute o comando seguinte para transferir a configuração de cluster do Kubernetes principal para o ficheiro `~/.kube/config`:

```azurecli
az acs kubernetes get-credentials --resource-group=$RESOURCE_GROUP --name=$CLUSTER_NAME
```

Para mais opções de instalação e configuração de `kubectl`, veja [Connect to an Azure Container Service cluster (Ligar a um cluster do Azure Container Service)](container-service-connect.md).

Neste momento, deve estar pronto para aceder ao cluster a partir do seu computador. Tente executar:

```bash
kubectl get nodes
```

Verifique se consegue ver uma lista das máquinas no cluster.

## <a name="create-your-first-kubernetes-service"></a>Criar o primeiro serviço do Kubernetes

Depois de concluir estas instruções, saberá como:
* implementar uma aplicação Docker e expô-la a todo o mundo
* utilizar `kubectl exec` para executar comandos num contentor 
* aceder ao dashboard do Kubernetes

### <a name="start-a-container"></a>Iniciar um contentor
Pode executar um contentor (neste caso, o servidor Web Nginx) ao executar:

```bash
kubectl run nginx --image nginx
```

Este comando inicia o contentor Docker Nginx num pod num dos nós.

Para ver o contentor em execução, execute:

```bash
kubectl get pods
```

### <a name="expose-the-service-to-the-world"></a>Expor o serviço ao mundo
Para expor o serviço ao mundo, crie um Kubernetes `Service` do tipo `LoadBalancer`:

```bash
kubectl expose deployments nginx --port=80 --type=LoadBalancer
```

Este comando faz com que o Kubernetes crie um balanceador de carga do Azure com um endereço IP público. A alteração demora alguns minutos a ser propagada para o balanceador de carga. Para obter mais informações, veja [Contentores de balanceamento de carga num cluster do Kubernetes no Azure Container Service](container-service-kubernetes-load-balancing.md).

Execute o seguinte comando para ver o serviço alterar de `pending` para apresentar um endereço IP externo:

```bash
watch 'kubectl get svc'
```

  ![Imagem da visualização da transição de pendente para endereço IP externo](media/container-service-kubernetes-walkthrough/kubernetes-nginx3.png)

Quando vir o endereço IP externo, pode navegá-lo no seu browser:

  ![Imagem de navegação para Nginx](media/container-service-kubernetes-walkthrough/kubernetes-nginx4.png)  


### <a name="browse-the-kubernetes-ui"></a>Navegar na IU do Kubernetes
Para ver a interface Web do Kubernetes, pode utilizar:

```bash
kubectl proxy
```
Este comando executa um proxy autenticado no localhost, que pode utilizar para ver a IU Web do Kubernetes em execução em [http://localhost:8001/ui](http://localhost:8001/ui). Para obter mais informações, veja [Utilizar a IU Web do Kubernetes com o Azure Container Service](container-service-kubernetes-ui.md).

![Imagem do dashboard do Kubernetes](media/container-service-kubernetes-walkthrough/kubernetes-dashboard.png)

### <a name="remote-sessions-inside-your-containers"></a>Sessões remotas nos contentores
O Kubernetes permite executar comandos num contentor Docker remoto em execução no cluster.

```bash
# Get the name of your nginx pods
kubectl get pods
```

Ao utilizar o seu nome de pod, pode executar um comando remoto no seu pod. Por exemplo:

```bash
kubectl exec <pod name> date
```

Também pode aceder uma sessão totalmente interativa através dos sinalizadores `-it`:

```bash
kubectl exec <pod name> -it bash
```

![Sessão remota dentro de um contentor](media/container-service-kubernetes-walkthrough/kubernetes-remote.png)



## <a name="next-steps"></a>Passos seguintes

Para fazer mais com o seu cluster do Kubernetes, veja os seguintes recursos:

* [Bootcamp do Kubernetes](https://katacoda.com/embed/kubernetes-bootcamp/1/) – Mostra como implementar, dimensionar, atualizar e depurar aplicações em contentores.
* [Guia de Utilizador do Kubernetes](http://kubernetes.io/docs/user-guide/) – Fornece informações sobre como executar programas num cluster do Kubernetes existente.
* [Exemplos do Kubernetes](https://github.com/kubernetes/kubernetes/tree/master/examples) – Fornece exemplos sobre como executar aplicações reais com o Kubernetes.

