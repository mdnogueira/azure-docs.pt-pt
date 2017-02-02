---
title: Ligar a um cluster do Azure Container Service | Microsoft Docs
description: Ligue a um cluster do Kubernetes, de DC/OS ou do Docker Swarm no Azure Container Service a partir de um computador remoto
services: container-service
documentationcenter: 
author: dlepow
manager: timlt
editor: 
tags: acs, azure-container-service
keywords: "Docker, Contentores, Microsserviços, Kubernetes, DC/OS, Azure"
ms.assetid: ff8d9e32-20d2-4658-829f-590dec89603d
ms.service: container-service
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/12/2017
ms.author: rogardle
translationtype: Human Translation
ms.sourcegitcommit: ea59ff3f527d051e01baf12f596ff44af8a0dfc1
ms.openlocfilehash: 7fe3bc6a5eab1d1b9a8b73ab3c88f9808817369a


---
# <a name="connect-to-an-azure-container-service-cluster"></a>Ligar a um cluster do Serviço de Contentor Azure
Depois de criar um cluster do Azure Container Service, tem de ligar ao mesmo para implementar e gerir cargas de trabalho. Este artigo descreve como ligar à VM principal do cluster a partir de um computador remoto. Os clusters do Kubernetes, de DC/OS e do Docker Swarm expõem pontos finais REST. No Kubernetes, este ponto final é exposto em segurança na Internet e pode aceder ao mesmo ao executar a ferramenta de linha de comandos `kubectl` em qualquer máquina ligada à Internet. Em DC/OS e no Docker Swarm, tem de criar um túnel de secure shell (SSH) para ligar ao ponto final RESTE em segurança. 

> [!NOTE]
> O suporte de Kubernetes no Azure Container Service está atualmente em pré-visualização.
>

## <a name="prerequisites"></a>Pré-requisitos

* Um cluster do Kubernetes, de DC/OS ou do Swarm [implementado no Azure Container Service](container-service-deployment.md).
* Um ficheiro de chave privada, que corresponda à chave pública adicionada ao cluster durante a implementação. Estes comandos partem do princípio de que a chave pública SSH está em `$HOME/.ssh/id_rsa` no seu computador. Veja estas instruções para [OS X e Linux](../virtual-machines/virtual-machines-linux-mac-create-ssh-keys.md) ou [Windows](../virtual-machines/virtual-machines-linux-ssh-from-windows.md), para obter mais informações. Se a ligação SSH não estiver a funcionar, poderá ter de [repor as chaves SSH](../virtual-machines/virtual-machines-linux-troubleshoot-ssh-connection.md).

## <a name="connect-to-a-kubernetes-cluster"></a>Ligar a um cluster do Kubernetes

Siga estes passos para instalar e configurar `kubectl` no seu computador.

> [!NOTE] 
> Em Linux ou OS X, poderá ter de executar os comandos desta secção com `sudo`.
> 

### <a name="install-kubectl"></a>Instalar o kubectl
Uma forma de instalar esta ferramenta é utilizar o comando `az acs kubernetes install cli` da CLI 2.0 do Azure (Pré-visualização). Para executar este comando, confirme que [instalou](/cli/azure/install-az-cli2) a mais recente CLI 2.0 do Azure (Pré-visualização) e que tem sessão iniciada numa conta do Azure (`az login`).

```azurecli
# Linux or OS X
az acs kubernetes install-cli [--install-location=/some/directory/kubectl]

# Windows
az acs kubernetes install-cli [--install-location=C:\some\directory\kubectl.exe]
```

Em alternativa, pode transferir o cliente diretamente a partir da [página de lançamentos](https://github.com/kubernetes/kubernetes/blob/master/CHANGELOG.md#downloads-for-v146).

### <a name="download-cluster-credentials"></a>Transferir as credenciais do cluster
Assim que tiver o `kubectl` instalado, deve copiar as credenciais de cluster para a máquina. Uma forma de obter as credenciais é utilizar o comando `az acs kubernetes get-credentials`. Transmita o nome do grupo de recursos e o nome do recurso do serviço de contentores:


```azurecli
az acs kubernetes get-credentials --resource-group=<cluster-resource-group> --name=<cluster-name>
```

Este comando transfere as credenciais do cluster para `$HOME/.kube/config`, em que `kubectl` espera que estejam localizadas.

Em alternativa, pode utilizar `scp` para copiar de forma segura o ficheiro a partir de `$HOME/.kube/config` na VM principal para o seu computador local. Por exemplo:

```console
mkdir $HOME/.kube/config
scp azureuser@<master-dns-name>:.kube/config $HOME/.kube/config
```

Se estiver no Windows, tem de utilizar Bash on Ubuntu no Windows, o cliente de cópia de ficheiros segura PuTTY ou uma ferramenta semelhante.



### <a name="use-kubectl"></a>Utilizar o kubectl

Assim que tiver configurado o `kubectl`, pode listar os nós do cluster para testar a ligação:

```console
kubectl get nodes
```

Pode experimentar outros comandos do `kubectl`. Por exemplo, pode ver o Dashboard do Kubernetes. Em primeiro lugar, execute um proxy para o servidor de API do Kubernetes:

```console
kubectl proxy
```

A IU do Kubernetes está agora disponível em `http://localhost:8001/ui`.

Para obter mais informações, veja o [início rápido do Kubernetes](http://kubernetes.io/docs/user-guide/quick-start/).

## <a name="connect-to-a-dcos-or-swarm-cluster"></a>Ligar a um cluster de DC/OS ou do Swarm

Os clusters DC/OS e do Docker Swarm que são implementados pelo Serviço de Contentor do Azure expõem pontos finais REST. No entanto, estes pontos finais não estão abertos ao mundo externo. Para gerir estes pontos finais, tem de criar um túnel Secure Shell (SSH). Depois de estabelecer um túnel SSH, pode executar comandos nos pontos finais de cluster e ver a IU do cluster através de um browser no seu próprio sistema. As secções seguintes orientam-no ao longo da criação de um túnel SSH a partir de computadores que executem os sistemas operativos Linux, OS X e Windows.

> [!NOTE]
> Pode criar uma sessão SSH com um sistema de gestão de clusters. No entanto, não é recomendável. Trabalhar diretamente num sistema de gestão expõe o risco de alterações de configuração inadvertidas.
> 

### <a name="create-an-ssh-tunnel-on-linux-or-os-x"></a>Criar um túnel SSH no Linux ou OS X
A primeira coisa que faz quando cria um túnel SSH no Linux ou OS X é localizar o nome DNS público dos modelos de estrutura mestres com balanceamento de carga. Siga estes passos.


1. No [portal do Azure](https://portal.azure.com), navegue para o grupo de recursos que contém o cluster do serviço do contentor. Expanda o grupo de recursos, para que cada recurso seja apresentado. 

2. Localize e selecione a máquina virtual do recurso mestre. Nos clusters de DC/OS, o nome deste recurso começa por **dcos-master-**. 

    O painel **Máquina virtual** contém informações sobre o endereço IP público, que inclui o nome DNS. Guarde este nome para utilização posterior. 

    ![Nome DNS público](media/pubdns.png)

3. Agora, abra a shell e execute o comando `ssh`, ao especificar os valores seguintes: 

    **PORT** é a porta do ponto final que pretende expor. Para Swarm, utilize a porta 2375. Para o DC/OS, utilize a porta 80.  
    **USERNAME** é o nome de utilizador que foi fornecido quando implementou o cluster.  
    **DNSPREFIX** é o prefixo DNS que forneceu quando implementou o cluster.  
    **REGION** é a região no qual o grupo de recursos está localizado.  
    **PATH_TO_PRIVATE_KEY** [OPCIONAL] é o caminho para a chave privada que corresponde à chave pública que indicou quando criou o cluster. Utilize esta opção com o sinalizador `-i`.

    ```bash
    ssh -L PORT:localhost:PORT -f -N [USERNAME]@[DNSPREFIX]mgmt.[REGION].cloudapp.azure.com -p 2200
    ```
    > [!NOTE]
    > A porta de ligação SSH é 2200 – não a porta padrão 22. Em cluster com mais de uma VM principal, esta é a porta de ligação para a primeira VM principal.
    > 

Veja os exemplos relativos a DC/OS e Swarm nas secções seguintes.    

### <a name="dcos-tunnel"></a>Túnel do DC/OS
Para abrir um túnel para os pontos finais relacionados com DC/OS, execute um comando semelhante ao seguinte:

```bash
sudo ssh -L 80:localhost:80 -f -N azureuser@acsexamplemgmt.japaneast.cloudapp.azure.com -p 2200
```

Agora, pode aceder aos pontos finais relacionados ao DC/OS em:

* DC/OS: `http://localhost/`
* Marathon: `http://localhost/marathon`
* Mesos: `http://localhost/mesos`

Da mesma forma, pode aceder ao resto das APIs de cada aplicação através deste túnel.

### <a name="swarm-tunnel"></a>Túnel do Swarm
Para abrir um túnel para o ponto final do Swarm, execute um comando semelhante ao seguinte:

```bash
ssh -L 2375:localhost:2375 -f -N azureuser@acsexamplemgmt.japaneast.cloudapp.azure.com -p 2200
```

Agora pode definir a variável de ambiente de DOCKER_HOST da seguinte forma. Pode continuar a utilizar a interface de linha de comandos (CLI) Docker normalmente.

```bash
export DOCKER_HOST=:2375
```

### <a name="create-an-ssh-tunnel-on-windows"></a>Criar um túnel SSH no Windows
Existem várias opções para criar túneis SSH no Windows. Esta secção descreve como utilizar o PuTTY para criar o túnel.

1. [Transfira o PuTTY](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html) para o seu sistema Windows.

2. Execute a aplicação.

3. Introduza um nome de anfitrião composto pelo nome de utilizador administrador do cluster e o nome DNS público do primeiro modelo de estrutura mestre no cluster. O **Nome de Anfitrião** é semelhante a `adminuser@PublicDNSName`. Introduza 2200 para a **Porta**.

    ![Configuração do puTTY 1](media/putty1.png)

4. Selecione **SSH > Auth**. Adicione um caminho para o ficheiro da chave privada (formato .ppk) para autenticação. Pode utilizar uma ferramenta como [PuTTYgen](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html) para gerar este ficheiro a partir da chave SSH utilizada para criar o cluster.

    ![Configuração do puTTY 2](media/putty2.png)

5. Selecione **SSH > Túneis** e configure as portas reencaminhadas seguintes:

    * **Porta de Origem:** utilize a porta 80 para DC/OS ou a 2375 para Swarm.
    * **Destino:** utilize localhost:80 para DC/OS ou localhost:2375 para Swarm.

    O exemplo seguinte foi configurado para o DC/OS, mas será semelhante para o Docker Swarm.

    > [!NOTE]
    > A porta 80 não deve ser utilizada quando cria este túnel.
    > 

    ![Configuração do puTTY 3](media/putty3.png)

6. Quando tiver terminado, clique em **Sessão > Guardar**, para guardar a configuração da ligação.

7. Para ligar à sessão PuTTY, clique em **Abrir**. Quando se liga, pode ver a configuração da porta no registo de eventos do PuTTY.

    ![Registo de eventos do puTTY](media/putty4.png)

Depois de configurar o túnel para DC/OS, pode aceder ao ponto final relacionado em:

* DC/OS: `http://localhost/`
* Marathon: `http://localhost/marathon`
* Mesos: `http://localhost/mesos`

Depois de configurar o túnel para Docker Swarm, abra as definições do Windows para configurar uma variável de ambiente do sistema com o nome `DOCKER_HOST` e um valor de `:2375`. Depois, pode aceder ao cluster do Swarm através da CLI do Docker.

## <a name="next-steps"></a>Passos seguintes
Implementar e gerir contentores no seu cluster:

* [Trabalhar com o Azure Container Service e o Kubernetes](container-service-kubernetes-ui.md)
* [Trabalhar com o Azure Container Service e o DC/OS](container-service-mesos-marathon-rest.md)
* [Trabalhar com o Azure Container Service e o Docker Swarm](container-service-docker-swarm.md)




<!--HONumber=Jan17_HO3-->


