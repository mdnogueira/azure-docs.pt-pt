---
title: Cluster de Kubernetes no Azure para Windows | Microsoft Docs
description: "Implementar e começar a utilizar um cluster de Kubernetes para contentores do Windows no Azure Container Service"
services: container-service
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
ms.date: 05/04/2017
ms.author: danlep
ms.custom: H1Hack27Feb2017
ms.translationtype: Human Translation
ms.sourcegitcommit: 18d4994f303a11e9ce2d07bc1124aaedf570fc82
ms.openlocfilehash: 4e730b65a98af05ea00c5f8ebd9914e3367b66a7
ms.contentlocale: pt-pt
ms.lasthandoff: 05/09/2017


---

# <a name="get-started-with-kubernetes-and-windows-containers-in-container-service"></a>Introdução ao contentores Kubernetes e Windows no Container Service


Este artigo mostra como criar um cluster de Kubernetes no Azure Container Service que contém nós do Windows para executar contentores do Windows. Comece a utilizar os comandos do Azure CLI 2.0 `az acs` para criar o cluster de Kubernetes no Azure Container Service. Em seguida, utilize a ferramenta da linha de comandos `kubectl` de Kubernetes para começar a trabalhar com contentores do Windows criados a partir de imagens de Docker. 

> [!NOTE]
> O suporte para contentores do Windows com Kubernetes no Azure Container Service está em pré-visualização. 
>



A imagem seguinte mostra a arquitetura de um cluster de Kubernetes no Azure Container Service com um nó principal do Linux e dois nós de agente do Windows. 

![Imagem do cluster do Kubernetes no Azure no Azure](media/container-service-kubernetes-windows-walkthrough/kubernetes-windows.png)

* O nó principal do Linux serve a API REST do Kubernetes e está acessível através de SSH na porta 22 ou `kubectl` na porta 443. 
* Os nós de agente do Windows estão agrupados num conjunto de disponibilidade do Azure e executam os contentores. Os nós do Windows podem ser acedidos através de um túnel SSH de RDP através do nó principal. As regras do Balanceador de Carga do Azure são adicionadas dinamicamente ao cluster consoante os serviços expostos.



Todas as VMs estão na mesma rede virtual privada e totalmente acessíveis entre si. Todas as VMs executam um kubelet, Docker e um proxy.

Para ter mais contexto, veja o artigo [Introdução ao Azure Container Service](container-service-intro.md) e a [documentação do Kubernetes](https://kubernetes.io/docs/home/).

## <a name="prerequisites"></a>Pré-requisitos
Para criar um cluster do Azure Container Service com a CLI do Azure 2.0, tem de:
* ter uma conta do Azure ([obter uma avaliação gratuita](https://azure.microsoft.com/pricing/free-trial/))
* ter instalado e iniciado sessão no [Azure CLI 2.0](/cli/azure/install-az-cli2)

Também precisa dos seguintes elementos para o seu cluster de Kubernetes. Pode prepará-los antecipadamente ou utilizar as opções do comando `az acs create` para gerá-los automaticamente durante a implementação do cluster. 

* **Chave pública RSA SSH**: se pretender criar chaves RSA Secure Shell (SSH), veja as orientações para [macOS e Linux](../virtual-machines/linux/mac-create-ssh-keys.md) ou [Windows](../virtual-machines/linux/ssh-from-windows.md). 

* **ID e segredo de cliente de principal de serviço**: para ver os passos para criar um principal de serviço do Azure Active Directory e obter informações adicionais, veja [Sobre o principal de serviço para um cluster de Kubernetes](container-service-kubernetes-service-principal.md).

O exemplo de comando neste artigo gera automaticamente as chaves SSH e um principal de serviço.
  
## <a name="create-your-kubernetes-cluster"></a>Criar o cluster do Kubernetes

Eis os comandos do Azure CLI 2.0 para criar o cluster. 

### <a name="create-a-resource-group"></a>Criar um grupo de recursos
Crie um grupo de recursos numa localização onde o Azure Container Service esteja [disponível](https://azure.microsoft.com/regions/services/). O comando seguinte cria um grupo de recursos com o nome *myKubernetesResourceGroup* na localização *westus*:

```azurecli
az group create --name=myKubernetesResourceGroup --location=westus
```

### <a name="create-a-kubernetes-cluster-with-windows-agent-nodes"></a>Criar um cluster de Kubernetes com os nós de agente do Windows

Crie um cluster de Kubernetes no seu grupo de recursos, utilizando o comando `az acs create` com `--orchestrator-type=kubernetes` e a opção de agente `--windows`. Para ver a sintaxe do comando, veja a [ajuda](/cli/azure/acs#create) do comando `az acs create`.

O comando seguinte cria um cluster do Container Service com o nome *myKubernetesClusterName*, com um prefixo de DNS *myPrefix* para o nó de gestão e as credenciais especificadas para aceder aos nós do Windows. Esta versão do comando gera automaticamente as chaves RSA SSH e o principal de serviço para o cluster de Kubernetes.


```azurecli
az acs create --orchestrator-type=kubernetes \
    --resource-group myKubernetesResourceGroup \
    --name=myKubernetesClusterName \
    --dns-prefix=myPrefix \
    --agent-count=2 \
    --generate-ssh-keys \
    --windows --admin-username myWindowsAdminName \
    --admin-password myWindowsAdminPassword
```

Após vários minutos, o comando é concluído e deve ter um cluster de Kubernetes em funcionamento.

> [!IMPORTANT]
> Se a sua conta não tiver permissões para criar o principal de serviço do Azure AD, o comando gera um erro semelhante a `Insufficient privileges to complete the operation.`. Para obter mais informações, veja [Sobre o principal de serviço para um cluster de Kubernetes](container-service-kubernetes-service-principal.md). 
> 

## <a name="connect-to-the-cluster-with-kubectl"></a>Ligar ao cluster com kubectl

Para ligar ao cluster de Kubernetes a partir do computador cliente, utilize [`kubectl`](https://kubernetes.io/docs/user-guide/kubectl/), o cliente de linha de comandos do Kubernetes. 

Se não tiver o `kubectl` instalado localmente, pode instalá-lo com `az acs kubernetes install-cli`. (Também pode transferi-lo a partir do [site do Kubernetes](https://kubernetes.io/docs/tasks/kubectl/install/).)

**Linux ou macOS**

```azurecli
sudo az acs kubernetes install-cli
```

**Windows**
```azurecli
az acs kubernetes install-cli
```

> [!TIP]
> Por predefinição, este comando instala o binário `kubectl` em `/usr/local/bin/kubectl` num sistema Linux ou macOS ou em `C:\Program Files (x86)\kubectl.exe` no Windows. Para especificar um caminho de instalação diferente, utilize o parâmetro `--install-location`.
>
> Após `kubectl` estar instalado, certifique-se de que o seu diretório está no caminho do sistema ou adicione-o ao mesmo. 


Em seguida, execute o comando seguinte para transferir a configuração de cluster do Kubernetes principal para o ficheiro `~/.kube/config` local:

```azurecli
az acs kubernetes get-credentials --resource-group=myKubernetesResourceGroup --name=myKubernetesClusterName
```

Neste momento, está pronto para aceder ao cluster a partir do seu computador. Tente executar:

```bash
kubectl get nodes
```

Verifique se consegue ver uma lista das máquinas no cluster.

![Nós em execução num cluster do Kubernetes](media/container-service-kubernetes-windows-walkthrough/kubectl-get-nodes.png)

## <a name="create-your-first-kubernetes-service"></a>Criar o primeiro serviço do Kubernetes

Depois de criar o cluster e de ligar a `kubectl`, tente iniciar uma aplicação Windows a partir de um contentor de Docker e expô-la à Internet. Este exemplo básico utiliza um ficheiro JSON para especificar um contentor do Microsoft Internet Information Server (IIS) e, em seguida, cria-o com `kubctl apply`. 

1. Crie um ficheiro local com o nome `iis.json` e copie o seguinte. Este ficheiro indica ao Kubernetes para executar o IIS no Windows Server 2016 Server Core, com uma imagem pública a partir de [Docker Hub](https://hub.docker.com/r/microsoft/iis/). O contentor utiliza a porta 80, mas, inicialmente, só está acessível dentro da rede de cluster.

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
          "image": "microsoft/iis",
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
2. Para iniciar a aplicação, escreva:  
  
  ```bash
  kubectl apply -f iis.json
  ```  
3. Para monitorizar a implementação do contentor, escreva:  
  ```bash
  kubectl get pods
  ```
  Enquanto o contentor está a ser implementado, o estado é `ContainerCreating`. 

  ![Contentor do IIS no estado ContainerCreating](media/container-service-kubernetes-windows-walkthrough/iis-pod-creating.png)   

  Devido ao tamanho da imagem do IIS, pode demorar alguns minutos até que o contentor entre no estado `Running`.

  ![Contentor do IIS no estado Running](media/container-service-kubernetes-windows-walkthrough/iis-pod-running.png)

4. Para expor o contentor ao mundo, escreva o seguinte comando:

  ```bash
  kubectl expose pods iis --port=80 --type=LoadBalancer
  ```

  Com este comando, o Kubernetes cria uma regra do Balanceador de Carga do Azure com um endereço IP público. A alteração demora alguns minutos a ser propagada para o balanceador de carga. Para obter mais informações, veja [Contentores de balanceamento de carga num cluster do Kubernetes no Azure Container Service](container-service-kubernetes-load-balancing.md).

5. Execute o seguinte comando para ver o estado do serviço.

  ```bash
  kubectl get svc
  ```

  Inicialmente, o endereço IP é apresentado como `pending`:

  ![Endereço IP externo pendente](media/container-service-kubernetes-windows-walkthrough/iis-svc-expose.png)

  Após alguns minutos, o endereço IP é definido:
  
  ![Endereço IP externo para o IIS](media/container-service-kubernetes-windows-walkthrough/iis-svc-expose-public.png)


6. Quando o endereço IP externo estiver disponível, pode navegar para o mesmo no seu browser:

  ![Imagem de navegação para IIS](media/container-service-kubernetes-windows-walkthrough/kubernetes-iis.png)  

7. Para eliminar o pod IIS, escreva:

  ```bash
  kubectl delete pods iis
  ```

## <a name="next-steps"></a>Passos seguintes

* Para utilizar a IU do Kubernetes, execute o comando `kubectl proxy`. Em seguida, navegue até http://localhost:8001/ui.

* Para obter os passos para criar um site do IIS personalizado e executá-lo num contentor do Windows, veja as instruções em [Docker Hub](https://hub.docker.com/r/microsoft/iis/).

* Para aceder aos nós do Windows através de um túnel SSH RDP para o mestre com PuTTy, veja a [documentação do Motor ACS](https://github.com/Azure/acs-engine/blob/master/docs/ssh.md#create-port-80-tunnel-to-the-master). 

