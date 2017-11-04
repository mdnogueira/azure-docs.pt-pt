---
title: "Jenkins CI/CD com Kubernetes no serviço de contentor do Azure | Microsoft Docs"
description: "Como automatizar um processo de CI/CD com Jenkins para implementar e atualizar uma aplicação no Kubernetes no serviço de contentor do Azure"
services: container-service
documentationcenter: 
author: chzbrgr71
manager: johny
editor: 
tags: acs, azure-container-service, jenkins
keywords: Docker, contentores, Kubernetes, do Azure, Jenkins
ms.assetid: 
ms.service: container-service
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/23/2017
ms.author: briar
ms.custom: mvc
ms.openlocfilehash: c4b833b4d3234adc3f44a84f253ff9b8d78b1e23
ms.sourcegitcommit: c5eeb0c950a0ba35d0b0953f5d88d3be57960180
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/24/2017
---
# <a name="jenkins-integration-with-azure-container-service-and-kubernetes"></a>Jenkins integração com o serviço de contentor do Azure e Kubernetes 

[!INCLUDE [aks-preview-redirect.md](../../../includes/aks-preview-redirect.md)]

Neste tutorial, iremos guiá-lo durante o processo para configurar a integração contínua de uma aplicação de contentor multi para Kubernetes de serviço de contentor do Azure com a plataforma de Jenkins. O fluxo de trabalho a imagem do contentor no Docker Hub de atualizações as pods Kubernetes utilizando uma implementação de implementação. 

## <a name="high-level-process"></a>Processo de nível elevado
Os passos básicos detalhados neste artigo são: 
- Instalar um cluster de Kubernetes no serviço de contentor
- Definir Jenkins e configurar o acesso ao serviço de contentor
- Criar um fluxo de trabalho Jenkins
- Teste o processo de CI/CD ponto a ponto

## <a name="install-a-kubernetes-cluster"></a>Instalar um cluster de Kubernetes
    
Implemente o cluster de Kubernetes no serviço de contentor Azure utilizando os seguintes passos. Documentação completa está localizada [aqui](container-service-kubernetes-walkthrough.md).

### <a name="step-1-create-a-resource-group"></a>Passo 1: Criar um grupo de recursos
```azurecli
RESOURCE_GROUP=my-resource-group
LOCATION=westus

az group create --name=$RESOURCE_GROUP --location=$LOCATION
```

### <a name="step-2-deploy-the-cluster"></a>Passo 2: Implementar o cluster
> [!NOTE]
> Os seguintes passos requerem uma chave pública SSH local armazenada na pasta ~/.ssh.
>

```azurecli
RESOURCE_GROUP=my-resource-group
DNS_PREFIX=some-unique-value
CLUSTER_NAME=any-acs-cluster-name

az acs create \
--orchestrator-type=kubernetes \
--resource-group $RESOURCE_GROUP \
--name=$CLUSTER_NAME \
--dns-prefix=$DNS_PREFIX \
--ssh-key-value ~/.ssh/id_rsa.pub \
--admin-username=azureuser \
--master-count=1 \
--agent-count=5 \
--agent-vm-size=Standard_D1_v2
```

## <a name="set-up-jenkins-and-configure-access-to-container-service"></a>Definir Jenkins e configurar o acesso ao serviço de contentor

### <a name="step-1-install-jenkins"></a>Passo 1: Instalar Jenkins
1. Criar uma VM do Azure com Ubuntu 16.04 LTS.  Uma vez que mais tarde nos passos, terá de ligar a esta VM com bash no seu computador local, defina o 'tipo de autenticação' para 'A chave pública SSH' e cole a chave pública SSH que esteja armazenada localmente na sua pasta ~/.ssh.  Também, de ter nota do nome especiais de utilizador que especificou, uma vez que este nome de utilizador serão necessárias para ver o dashboard Jenkins e para ligar à VM Jenkins em passos posteriores.
2. Instalar Jenkins através destes [instruções](https://wiki.jenkins-ci.org/display/JENKINS/Installing+Jenkins+on+Ubuntu). É um tutorial mais detalhado [howtoforge.com](https://www.howtoforge.com/tutorial/how-to-install-jenkins-with-apache-on-ubuntu-16-04).
3. Para ver o dashboard de Jenkins no seu computador local, atualize o grupo de segurança de rede do Azure para permitir que a porta 8080 ao adicionar uma regra de entrada que permite o acesso à porta 8080.  Em alternativa, pode configurar o reencaminhamento de porta ao executar o comando:`ssh -i ~/.ssh/id_rsa -L 8080:localhost:8080 <your_jenkins_user>@<your_jenkins_public_ip`
4. Ligar ao seu servidor de Jenkins utilizando o browser, navegando para o IP público (http:// < your_jenkins_public_ip >: 8080) e desbloquear o dashboard Jenkins pela primeira vez com a palavra-passe de administrador inicial.  A palavra-passe de admin é armazenada em /var/lib/jenkins/secrets/initialAdminPassword na Jenkins VM.  É uma forma fácil de obter esta palavra-passe SSH para a VM Jenkins: `ssh <your_jenkins_user>@<your_jenkins_public_ip>`.  Em seguida, execute: `sudo cat /var/lib/jenkins/secrets/initialAdminPassword`.
5. Instale o Docker na máquina Jenkins através destes [instruções](https://docs.docker.com/cs-engine/1.13/#install-on-ubuntu-1404-lts-or-1604-lts). Este procedimento permite que os comandos de Docker ser executada na Jenkins tarefas.
6. Configure permissões de Docker para permitir que Jenkins aceder ao ponto final de Docker.

    ```bash
    sudo chmod 777 /run/docker.sock
    ```
8. Instalar `kubectl` CLI no Jenkins. Obter mais detalhes tenham [instalar e configurar kubectl](https://kubernetes.io/docs/tasks/kubectl/install/).  Tarefas de Jenkins utilizarão 'kubectl' para gerir e implementar o cluster Kubernetes.

    ```bash
    curl -LO https://storage.googleapis.com/kubernetes-release/release/$(curl -s https://storage.googleapis.com/kubernetes-release/release/stable.txt)/bin/linux/amd64/kubectl

    chmod +x ./kubectl

    sudo mv ./kubectl /usr/local/bin/kubectl
    ```

### <a name="step-2-set-up-access-to-the-kubernetes-cluster"></a>Passo 2: Configurar o acesso ao Kubernetes cluster

> [!NOTE]
> Existem várias abordagens para realizar os seguintes passos. Utilize a abordagem que é mais fácil para si.
>

1. Copiar o `kubectl` do ficheiro de configuração para a máquina Jenkins para que as tarefas de Jenkins tenham acesso ao Kubernetes cluster. Estas instruções partem do princípio de que está a utilizar bash a partir de uma máquina que a VM Jenkins diferente e que um local a chave pública SSH é armazenada na pasta de ~/.ssh da máquina.

```bash
export KUBE_MASTER=<your_cluster_master_fqdn>
export JENKINS_USER=<your_jenkins_user>
export JENKINS_SERVER=<your_jenkins_public_ip>
sudo ssh $JENKINS_USER@$JENKINS_SERVER sudo mkdir -m 777 /home/$JENKINS_USER/.kube/ \
&& sudo ssh $JENKINS_USER@$JENKINS_SERVER sudo mkdir /var/lib/jenkins/.kube/ \
&& sudo scp -3 -i ~/.ssh/id_rsa azureuser@$KUBE_MASTER:.kube/config $JENKINS_USER@$JENKINS_SERVER:~/.kube/config \
&& sudo ssh -i ~/.ssh/id_rsa $JENKINS_USER@$JENKINS_SERVER sudo cp /home/$JENKINS_USER/.kube/config /var/lib/jenkins/.kube/config \
```
        
2. Valide de Jenkins que o cluster Kubernetes está acessível.  Para tal, o SSH para a VM Jenkins: `ssh <your_jenkins_user>@<your_jenkins_public_ip>`.  Em seguida, certifique-se Jenkins pode ligar com êxito para o cluster: `kubectl cluster-info`.
    

## <a name="create-a-jenkins-workflow"></a>Criar um fluxo de trabalho Jenkins

### <a name="prerequisites"></a>Pré-requisitos

- Conta GitHub do repositório de código.
- Conta de Hub de docker para armazenar e atualizar imagens.
- Aplicação de que pode ser reconstruída e atualizada. Pode utilizar esta aplicação de contentor de exemplo escrita em Golang: https://github.com/chzbrgr71/go-web 

> [!NOTE]
> Os seguintes passos devem ser executados na sua própria conta do GitHub. À vontade clonar o repositório acima, mas tem de utilizar a seus próprios conta para configurar os webhooks e Jenkins aceder.
>

### <a name="step-1-deploy-initial-v1-of-application"></a>Passo 1: Implementar v1 inicial da aplicação
1. Crie a aplicação a partir da máquina de programador com os seguintes comandos. Substitua `myrepo` com os seus próprios.
    
    ```bash
    git clone https://github.com/chzbrgr71/go-web.git
    cd go-web
    docker build -t myrepo/go-web .
    ```

2. Imagem de push ao Hub de Docker.

    ```bash
    docker login
    docker push myrepo/go-web
    ```

3. Implemente o cluster Kubernetes.
    
    > [!NOTE] 
    > Editar o `go-web.yaml` ficheiro para atualizar a imagem de contentor e um repositório.
    >
        
    ```bash
    kubectl create -f ./go-web.yaml --record
    ```
### <a name="step-2-configure-jenkins-system"></a>Passo 2: Configurar o sistema Jenkins
1. Clique em **gerir Jenkins** > **Configurar sistema**.
2. Em **GitHub**, selecione **Adicionar servidor de GitHub**.
3. Deixe **API URL** como predefinição.
4. Em **credenciais**, adicione uma credencial de Jenkins utilizando **texto secreto**. Recomendamos a utilização de tokens de acesso pessoal do GitHub, que estão configurados nas definições de conta de utilizador do GitHub. Obter mais detalhes sobre este [aqui.](https://help.github.com/articles/creating-a-personal-access-token-for-the-command-line/)
5. Clique em **Testar ligação** para garantir que este está configurado corretamente.
6. Em **propriedades globais**, adicione uma variável de ambiente `DOCKER_HUB` e forneça a palavra-passe do Hub de Docker. (Isto é útil nesta demonstração, mas um cenário de produção iria exigir uma abordagem mais segura).
7. Guarde.

![Acesso de Jenkins GitHub](./media/container-service-kubernetes-jenkins/jenkins-github-access.png)

### <a name="step-3-create-the-jenkins-workflow"></a>Passo 3: Criar o fluxo de trabalho Jenkins
1. Crie um item de Jenkins.
2. Forneça um nome (por exemplo, "go web") e selecione **Freestyle projeto**. 
3. Verifique **GitHub projeto** e forneça o URL para o seu repositório do GitHub.
4. No **gestão da origem de código**, forneça o URL de repositório do GitHub e as credenciais. 
5. Adicionar um **passo Criar** do tipo **executar shell** e utilize o seguinte texto:

    ```bash
    WEB_IMAGE_NAME="myrepo/go-web:kube${BUILD_NUMBER}"
    docker build -t $WEB_IMAGE_NAME .
    docker login -u <your-dockerhub-username> -p ${DOCKER_HUB}
    docker push $WEB_IMAGE_NAME
    ```

6. Adicionar outro **passo Criar** do tipo **executar shell** e utilize o seguinte texto:

    ```bash
    WEB_IMAGE_NAME="myrepo/go-web:kube${BUILD_NUMBER}"
    kubectl set image deployment/go-web go-web=$WEB_IMAGE_NAME --kubeconfig /var/lib/jenkins/config
    ```

![Passos de compilação Jenkins](./media/container-service-kubernetes-jenkins/jenkins-build-steps.png)
    
7. Guardar o item de Jenkins e testar com **compilar agora**.

### <a name="step-4-connect-github-webhook"></a>Passo 4: Ligar o GitHub webhook
1. No item de Jenkins que criou, clique em **configurar**.
2. Em **criar Acionadores**, selecione **acionador de rotina do GitHub para consulta GITScm** e **guardar**. Esta ação configura automaticamente o webhook do GitHub.
3. No seu repositório do GitHub para ir web, clique em **definições > Webhooks**.
4. Certifique-se de que o URL do webhook Jenkins foi adicionado com êxito. O URL deve terminar em "github webhook".

![Configuração de webhook Jenkins](./media/container-service-kubernetes-jenkins/jenkins-webhook.png)

## <a name="test-the-cicd-process-end-to-end"></a>Teste o processo de CI/CD ponto a ponto

1. Atualize o código para o repositório e push/sincronização com o repositório do GitHub.
2. Jenkins na consola do, verifique o **criar histórico** e confirme se a tarefa foi executada. Resultado da consola de vista para ver detalhes.
3. De Kubernetes, ver os detalhes da implementação atualizado:

    ```bash
    kubectl rollout history deployment/go-web
    ```

## <a name="next-steps"></a>Passos seguintes

- Implementar o registo de contentor do Azure e armazenar imagens num repositório seguro. Consulte [docs do registo de contentor do Azure](https://docs.microsoft.com/azure/container-registry).
- Crie um fluxo de trabalho mais complexo que inclui a implementação lado a lado e testes automatizados em Jenkins.
- Para mais informações sobre CI/CD com Jenkins e Kubernetes, consulte o [Jenkins blogue](https://jenkins.io/blog/2015/07/24/integrating-kubernetes-and-jenkins/).
