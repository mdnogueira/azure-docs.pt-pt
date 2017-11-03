---
title: "CI/CD com o motor do serviço de contentor do Azure e o modo de Swarm | Microsoft Docs"
description: "Utilize o motor do serviço de contentor do Azure com o Docker Swarm modo, um registo de contentor do Azure e o Visual Studio Team Services continuamente fornecer aplicações de .NET Core um contentor multi"
services: container-service
documentationcenter: " "
author: diegomrtnzg
manager: esterdnb
tags: acs, azure-container-service, acs-engine
keywords: "Docker, contentores, microserviços, Swarm, serviços, DevOps, ACS motor de equipa do Azure, Visual Studio"
ms.service: container-service
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/27/2017
ms.author: diegomrtnzg
ms.custom: mvc
ms.openlocfilehash: 2c0e5fe4f60738fcc1aa67a78674e6f3c62e5628
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="full-cicd-pipeline-to-deploy-a-multi-container-application-on-azure-container-service-with-acs-engine-and-docker-swarm-mode-using-visual-studio-team-services"></a>Pipeline de CI/CD completa para implementar uma aplicação de várias contentor no serviço de contentor do Azure com o motor de ACS e o Docker Swarm modo utilizando o Visual Studio Team Services

*Este artigo baseia-se no [completa CI/CD pipeline para implementar uma aplicação de várias contentor no serviço de contentor do Azure com o Docker Swarm utilizando o Visual Studio Team Services](container-service-docker-swarm-setup-ci-cd.md) documentação*

Nowadays, um dos maiores desafios quando desenvolver aplicações modernas para a nuvem está a ser capaz de fornecer estas aplicações continuamente. Neste artigo, irá aprender a implementar uma integração completa contínua e, em seguida, através do pipeline de implementação (CI/CD): 
* Motor do serviço de contentor do Azure com o modo do Docker Swarm
* Registo de Contentores do Azure
* Visual Studio Team Services

Este artigo baseia-se uma aplicação simples, disponível no [GitHub](https://github.com/jcorioland/MyShop/tree/docker-linux), desenvolvidas com o ASP.NET Core. A aplicação é constituída por quatro diferentes serviços: APIs e front-end um web de três web:

![Aplicação de exemplo MyShop](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/myshop-application.png)

O objetivo consiste em fornecer esta aplicação continuamente num cluster Docker Swarm modo, com o Visual Studio Team Services. A figura seguinte fornece detalhes sobre este pipeline de entrega contínua:

![Aplicação de exemplo MyShop](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/full-ci-cd-pipeline.png)

Eis uma breve explicação dos passos:

1. Alterações de código são consolidadas para o repositório de código de origem (aqui, GitHub) 
2. GitHub aciona uma compilação no Visual Studio Team Services 
3. Visual Studio Team Services obtém a versão mais recente de origens e baseia-se a todas as imagens que compõem a aplicação 
4. Visual Studio Team Services pushes cada imagem de um registo de Docker criadas com o serviço de registo de contentor do Azure 
5. Visual Studio Team Services aciona uma nova versão 
6. A versão executa alguns comandos através do SSH no nó de principal do cluster de serviço de contentor do Azure 
7. Modo de docker Swarm no cluster obtém a versão mais recente das imagens 
8. A nova versão da aplicação é implementada através da pilha de Docker 

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar este tutorial, tem de concluir as seguintes tarefas:

- [Criar um cluster de modo Swarm no serviço de contentor do Azure com o motor de ACS](https://github.com/Azure/azure-quickstart-templates/tree/master/101-acsengine-swarmmode)
- [Ligar ao cluster Swarm no Azure Container Service](../container-service-connect.md)
- [Criar um registo de contentor do Azure](../../container-registry/container-registry-get-started-portal.md)
- [Tem um projeto de conta e a equipa de Visual Studio Team Services criado](https://www.visualstudio.com/en-us/docs/setup-admin/team-services/sign-up-for-visual-studio-team-services)
- [Copiar o repositório do GitHub à sua conta GitHub](https://github.com/jcorioland/MyShop/tree/docker-linux)

>[!NOTE]
> O orchestrator do Docker Swarm no Azure Container Service utiliza o legado autónomo do Swarm. Atualmente, o [modo Swarm](https://docs.docker.com/engine/swarm/) integrado (no Docker 1.12 e superior) não é um orchestrator suportado no Azure Container Service. Por este motivo, estamos a utilizar [ACS motor](https://github.com/Azure/acs-engine/blob/master/docs/swarmmode.md), uma Comunidade-contribuíram [modelo de início rápido](https://azure.microsoft.com/resources/templates/101-acsengine-swarmmode/), ou uma solução de Docker no [Azure Marketplace](https://azuremarketplace.microsoft.com).
>

## <a name="step-1-configure-your-visual-studio-team-services-account"></a>Passo 1: Configurar a sua conta do Visual Studio Team Services 

Nesta secção, configure a conta do Visual Studio Team Services. Para configurar pontos finais de serviços de VSTS, no seu projeto de Visual Studio Team Services, clique o **definições** ícone na barra de ferramentas e selecione **serviços**.

![Ponto final de abrir os serviços](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/services-vsts.PNG)

### <a name="connect-visual-studio-team-services-and-azure-account"></a>Ligar a conta do Visual Studio Team Services e do Azure

Configure uma ligação entre o seu projeto VSTS e a sua conta do Azure.

1. No lado esquerdo, clique em **ponto final de serviço novo** > **do Azure Resource Manager**.
2. Para autorizar VSTS para trabalhar com a sua conta do Azure, selecione o **subscrição** e clique em **OK**.

    ![Visual Studio Team Services - autorizar do Azure](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-azure.PNG)

### <a name="connect-visual-studio-team-services-and-github"></a>Ligar o Visual Studio Team Services e do GitHub

Configure uma ligação entre o seu projeto VSTS e a sua conta do GitHub.

1. No lado esquerdo, clique em **ponto final de serviço novo** > **GitHub**.
2. Para autorizar VSTS para trabalhar com a sua conta do GitHub, clique em **autorizar** e siga o procedimento na janela abre.

    ![Visual Studio Team Services - autorizar GitHub](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-github.png)

### <a name="connect-vsts-to-your-azure-container-service-cluster"></a>Ligar VSTS ao cluster do serviço de contentor do Azure

Os última passos antes de obter no pipeline de CI/CD são configurar ligações externas ao seu cluster Docker Swarm no Azure. 

1. Para o cluster Docker Swarm, adicionar um ponto final do tipo **SSH**. Em seguida, introduza as informações de ligação de SSH do seu cluster Swarm (nó principal).

    ![Visual Studio Team Services - SSH](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-ssh.png)

Toda a configuração é feita agora. Passos seguintes, vai criar o pipeline de CI/CD que cria e implementa a aplicação para o cluster Docker Swarm. 

## <a name="step-2-create-the-build-definition"></a>Passo 2: Criar a definição de compilação

Neste passo, configure uma definição de compilação para o seu projeto VSTS e definir o fluxo de trabalho de compilação para as imagens de contentor

### <a name="initial-definition-setup"></a>Configuração inicial da definição

1. Para criar uma definição de compilação, ligar ao seu projeto de Visual Studio Team Services e clique em **compilar & versão**. No **definições de compilação** secção, clique em **+ novo**. 

    ![Visual Studio Team Services, o-nova definição de compilação](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/create-build-vsts.PNG)

2. Selecione o **vazio processo**.

    ![Visual Studio Team Services, o-nova definição de compilação vazio](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/create-empty-build-vsts.PNG)

4. Em seguida, clique o **variáveis** separador e criar duas novas variáveis: **RegistryURL** e **AgentURL**. Cole os valores do registo e DNS de agentes do Cluster.

    ![Visual Studio Team Services, o-configuração de variáveis de compilação](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-build-variables.png)

5. No **definições de compilação** página, abra o **Acionadores** separador e configurar a compilação para utilizar a integração contínua com bifurcação do projeto MyShop que criou nos pré-requisitos. Em seguida, selecione **lote de alterações**. Certifique-se de que seleciona *docker linux* como o **sucursal especificação**.

    ![Visual Studio Team Services, o-configuração do repositório de compilação](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-github-repo-conf.PNG)


6. Por fim, clique em de **opções** separador e configurar a fila do agente predefinido para **alojado pré-visualização do Linux**.

    ![Visual Studio Team Services, o-configuração do agente anfitrião](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-build-agent.png)

### <a name="define-the-build-workflow"></a>Definir o fluxo de trabalho de compilação
Os passos seguintes definem o fluxo de trabalho de compilação. Em primeiro lugar, terá de configurar a origem do código. Para fazê-lo, selecione **GitHub** e os seus **repositório** e **ramo** (docker linux).

![Configurar o Visual Studio Team Services - origem de código](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-source-code.png)

Existem cinco contentor imagens para criar para o *MyShop* aplicação. Cada imagem é compilada com Dockerfile localizado nas pastas do projeto:

* ProductsApi
* Proxy
* RatingsApi
* RecommandationsApi
* ShopFront

Precisa de dois passos Docker para cada imagem, um para compilar a imagem e um para enviar a imagem no registo de contentor do Azure. 

1. Para adicionar um passo no fluxo de trabalho de compilação, clique em **+ Adicionar passo de compilação** e selecione **Docker**.

    ![Visual Studio Team Services - adicionar passos de compilação](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-build-add-task.png)

2. Para cada imagem, configure um passo que utiliza o `docker build` comando.

    ![Visual Studio Team Services, o-compilação do Docker](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-docker-build.png)

    Para a operação de compilação, selecione o seu registo de contentor do Azure, o **construir uma imagem** ação e o Dockerfile que define cada imagem. Definir o **diretório de trabalho** como o diretório de raiz Dockerfile, definir o **nome da imagem**e selecione **incluir mais recente Tag**.
    
    O nome da imagem tem de ter este formato: ```$(RegistryURL)/[NAME]:$(Build.BuildId)```. Substitua **[nome]** com o nome de imagem:
    - ```proxy```
    - ```products-api```
    - ```ratings-api```
    - ```recommendations-api```
    - ```shopfront```

3. Para cada imagem, configure um segundo passo que utiliza o `docker push` comando.

    ![Visual Studio Team Services, o-Docker Push](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-docker-push.png)

    Para a operação de push, selecione o seu registo de contentor do Azure, o **Push uma imagem** ação, introduza o **nome da imagem** que é criada no passo anterior e selecione **incluir mais recente Tag**.

4. Depois de configurar os passos de push e de compilação para cada uma das cinco imagens, adicione mais três passos no fluxo de trabalho de compilação.

   ![Visual Studio Team Services - adicionar tarefa da linha de comandos](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-build-command-task.png)

      1. Uma tarefa da linha de comandos que utiliza um script de deteção para substituir o *RegistryURL* ocorrência no ficheiro docker-Compose.yml com a variável de RegistryURL. 
    
          ```-c "sed -i 's/RegistryUrl/$(RegistryURL)/g' src/docker-compose-v3.yml"```

          ![Visual Studio Team Services - ficheiro de atualização Compose com o URL de registo](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-build-replace-registry.png)

      2. Uma tarefa da linha de comandos que utiliza um script de deteção para substituir o *AgentURL* ocorrência no ficheiro docker-Compose.yml com a variável de AgentURL.
  
          ```-c "sed -i 's/AgentUrl/$(AgentURL)/g' src/docker-compose-v3.yml"```

     3. Uma tarefa que ignora o ficheiro de Compose atualizado como um artefacto de compilação, pelo que pode ser utilizado na versão. Consulte o ecrã seguinte para obter mais detalhes.

         ![Visual Studio Team Services - publicar artefactos](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-publish.png) 

         ![Visual Studio Team Services - publicar Compose ficheiro](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-publish-compose.png) 

5. Clique em **Guardar & fila** para testar a sua definição de compilação.

   ![Visual Studio Team Services - guardar e fila](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-build-save.png) 

   ![Visual Studio Team Services, o-nova fila](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-build-queue.png) 

6. Se o **criar** está correto, de que dispõe para ver este ecrã:

  ![Visual Studio Team Services - êxito de compilação](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-build-succeeded.png) 

## <a name="step-3-create-the-release-definition"></a>Passo 3: Criar a definição da versão

Visual Studio Team Services permite-lhe [gerir versões em ambientes](https://www.visualstudio.com/team-services/release-management/). Pode ativar a implementação contínua para se certificar de que a aplicação é implementada nos seus ambientes diferentes (por exemplo, o desenvolvimento, teste, pré-produção e de produção) de forma uniforme. Pode criar um ambiente que representa o cluster de modo de Swarm de Docker serviço de contentor do Azure.

![Visual Studio Team Services, o-versão para ACS](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-release-acs.png) 

### <a name="initial-release-setup"></a>Configuração inicial de versão

1. Para criar uma definição de versão, clique em **versões** > **+ da versão**

2. Para configurar a origem de artefactos, clique em **artefactos** > **ligar uma origem de artefacto**. Ligação aqui, esta nova definição de versão para a compilação que definiu no passo anterior. Depois disso, o ficheiro docker-Compose.yml está disponível no processo de versão.

    ![Visual Studio Team Services, o-artefactos de versão](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-release-artefacts.png) 

3. Para configurar o acionador de versão, clique em **Acionadores** e selecione **a implementação contínua**. Defina o acionador na mesma origem de artefactos. Esta definição garante que uma nova versão é iniciado quando a compilação for concluída com êxito.

    ![Visual Studio Team Services, o-Acionadores de versão](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-release-trigger.png) 

4. Para configurar as variáveis de versão, clique em **variáveis** e selecione **+ variável** para criar novas variáveis de três com as informações do registo: **docker.username**, **docker.password**, e **docker.registry**. Cole os valores do registo e DNS de agentes do Cluster.

    ![Visual Studio Team Services, o-configuração do repositório de compilação](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-release-variables.png)

    >[!IMPORTANT]
    > Como é mostrado no ecrã anterior, clique em de **bloqueio** docker.password de caixa de verificação. Esta definição é importante para restringir a palavra-passe.
    >

### <a name="define-the-release-workflow"></a>Definir o fluxo de trabalho de versão

O fluxo de trabalho de versão é composto por duas tarefas que adicionar.

1. Configurar uma tarefa para copiar em segurança o ficheiro de compose para um *implementar* pasta o Docker Swarm nó mestre, utilizando a ligação de SSH que configurou anteriormente. Consulte o ecrã seguinte para obter mais detalhes.
    
    Pasta de origem:```$(System.DefaultWorkingDirectory)/MyShop-CI/drop```

    ![Visual Studio Team Services, o-versão SCP](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-release-scp.png)

2. Configurar uma tarefa para executar um comando de bash para ser executada segundo `docker` e `docker stack deploy` comandos no nó principal. Consulte o ecrã seguinte para obter mais detalhes.

    ```docker login -u $(docker.username) -p $(docker.password) $(docker.registry) && export DOCKER_HOST=:2375 && cd deploy && docker stack deploy --compose-file docker-compose-v3.yml myshop --with-registry-auth```

    ![Visual Studio Team Services, o-Bash de versão](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-release-bash.png)

    O comando executado no mestre de utiliza da CLI do Docker e a CLI do Docker Compose para efetuar as seguintes tarefas:

    - Inicie sessão no registo de contentor do Azure (utiliza três variáveis de compilação que estão definidas no **variáveis** separador)
    - Definir o **DOCKER_HOST** variável para trabalhar com o ponto final Swarm (: 2375)
    - Navegue para o *implementar* pasta que foi criada pela tarefa de cópia de segurança anterior e que contém o ficheiro docker-Compose.yml 
    - Executar `docker stack deploy` comandos que as novas imagens de extração e criar contentores.

    >[!IMPORTANT]
    > Como é mostrado no ecrã anterior, deixe o **falhar no STDERR** caixa de verificação desmarcada. Esta definição permite-nos concluir o processo de lançamento devido a `docker-compose` imprime várias mensagens de diagnóstico, tais como contentores são parar ou que está a ser eliminados, a saída de erro padrão. Se selecionar a caixa de verificação, Visual Studio Team Services relatórios que ocorreram erros durante o lançamento, mesmo se tudo correr bem.
    >
3. Guarde esta nova definição de versão.

## <a name="step-4-test-the-cicd-pipeline"></a>Passo 4: Testar o pipeline de CI/CD

Agora que são efetuadas com a configuração, está na altura para testar este novo pipeline de CI/CD. É a forma mais fácil para testá-lo para atualizar o código de origem e consolidar as alterações no repositório do GitHub. Poucos segundos depois de emitir o código, verá uma nova compilação em execução no Visual Studio Team Services. Depois de concluída com êxito, uma nova versão é acionada e implementar a nova versão da aplicação no cluster do serviço de contentor do Azure.

## <a name="next-steps"></a>Passos seguintes

* Para obter mais informações sobre CI/CD com o Visual Studio Team Services, consulte o [descrição geral de compilação VSTS](https://www.visualstudio.com/docs/build/overview).
* Para obter mais informações sobre o motor de ACS, consulte o [repositório do GitHub do motor de ACS](https://github.com/Azure/acs-engine).
* Para obter mais informações sobre o modo de Docker Swarm, consulte o [Docker Swarm descrição geral de modo](https://docs.docker.com/engine/swarm/).
