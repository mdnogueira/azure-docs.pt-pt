---
title: "CI/CD com o serviço de contentor do Azure e Swarm | Microsoft Docs"
description: "Utilizar o serviço de contentor do Azure com o Docker Swarm, um registo de contentor do Azure e Visual Studio Team Services continuamente fornecer aplicações de .NET Core um contentor multi"
services: container-service
documentationcenter: " "
author: jcorioland
manager: pierlag
tags: acs, azure-container-service
keywords: "Docker, contentores, microserviços, Swarm, do Azure, Visual Studio Team Services, DevOps"
ms.service: container-service
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/08/2016
ms.author: jucoriol
ms.custom: mvc
ms.openlocfilehash: 99c27c37218a35d2a3416d6edd5e0a871cd5c011
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="full-cicd-pipeline-to-deploy-a-multi-container-application-on-azure-container-service-with-docker-swarm-using-visual-studio-team-services"></a>Pipeline de CI/CD completa para implementar uma aplicação de várias contentor no serviço de contentor do Azure com o Docker Swarm utilizando o Visual Studio Team Services

Um dos maiores desafios quando desenvolver aplicações modernas para a nuvem está a ser capaz de fornecer estas aplicações continuamente. Neste artigo, irá aprender a implementar uma integração completa contínua e o pipeline de implementação (CI/CD) utilizando o serviço de contentor do Azure com o Docker Swarm, o registo de contentor do Azure e o Visual Studio Team Services compilação e a gestão de versões.

Este artigo baseia-se uma aplicação simples, disponível no [GitHub](https://github.com/jcorioland/MyShop/tree/acs-docs), desenvolvidas com o ASP.NET Core. A aplicação é constituída por quatro diferentes serviços: APIs e front-end um web de três web:

![Aplicação de exemplo MyShop](./media/container-service-docker-swarm-setup-ci-cd/myshop-application.png)

O objetivo consiste em fornecer esta aplicação continuamente num cluster Docker Swarm, utilizar o Visual Studio Team Services. A figura seguinte fornece detalhes sobre este pipeline de entrega contínua:

![Aplicação de exemplo MyShop](./media/container-service-docker-swarm-setup-ci-cd/full-ci-cd-pipeline.png)

Eis uma breve explicação dos passos:

1. Alterações de código são consolidadas para o repositório de código de origem (aqui, GitHub) 
2. GitHub aciona uma compilação no Visual Studio Team Services 
3. Visual Studio Team Services obtém a versão mais recente de origens e baseia-se a todas as imagens que compõem a aplicação 
4. Visual Studio Team Services pushes cada imagem de um registo de Docker criadas com o serviço de registo de contentor do Azure 
5. Visual Studio Team Services aciona uma nova versão 
6. A versão executa alguns comandos através do SSH no nó de principal do cluster de serviço de contentor do Azure 
7. O docker Swarm no cluster obtém a versão mais recente das imagens 
8. A nova versão da aplicação é implementada utilizando o Docker Compose 

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar este tutorial, tem de concluir as seguintes tarefas:

- [Criar um cluster Swarm no Azure Container Service](container-service-deployment.md)
- [Ligar ao cluster Swarm no Azure Container Service](../container-service-connect.md)
- [Criar um registo de contentor do Azure](../../container-registry/container-registry-get-started-portal.md)
- [Tem um projeto de conta e a equipa de Visual Studio Team Services criado](https://www.visualstudio.com/en-us/docs/setup-admin/team-services/sign-up-for-visual-studio-team-services)
- [Copiar o repositório do GitHub à sua conta GitHub](https://github.com/jcorioland/MyShop/)

[!INCLUDE [container-service-swarm-mode-note](../../../includes/container-service-swarm-mode-note.md)]

Também precisa de uma máquina Ubuntu (14.04 ou 16.04) com o Docker instalado. Esta máquina é utilizada pelo Visual Studio Team Services durante os processos de compilação e a versão. Uma forma de criar esta máquina é utilizar a imagem disponível no [Azure Marketplace](https://azure.microsoft.com/marketplace/partners/canonicalandmsopentech/dockeronubuntuserver1404lts/). 

## <a name="step-1-configure-your-visual-studio-team-services-account"></a>Passo 1: Configurar a sua conta do Visual Studio Team Services 

Nesta secção, configure a conta do Visual Studio Team Services.

### <a name="configure-a-visual-studio-team-services-linux-build-agent"></a>Configura um agente de compilação do Visual Studio Team Services com Linux

Para criar imagens de Docker e emitir estas imagens para um registo de contentor do Azure a partir de uma compilação do Visual Studio Team Services, terá de registar um agente do Linux. Tem estas opções de instalação:

* [Implementar um agente no Linux](https://www.visualstudio.com/docs/build/admin/agents/v2-linux)

* [Utilizar o Docker para executar o agente VSTS](https://hub.docker.com/r/microsoft/vsts-agent)

### <a name="install-the-docker-integration-vsts-extension"></a>Instale a extensão de VSTS de integração do Docker

A Microsoft fornece uma extensão VSTS para trabalhar com o Docker na compilação e processos de versão. Esta extensão está disponível no [VSTS Marketplace](https://marketplace.visualstudio.com/items?itemName=ms-vscs-rm.docker). Clique em **instalar** para adicionar esta extensão para a sua conta VSTS:

![Instalar a integração do Docker](./media/container-service-docker-swarm-setup-ci-cd/install-docker-vsts.png)

É-lhe pedido para ligar à sua conta VSTS com as suas credenciais. 

### <a name="connect-visual-studio-team-services-and-github"></a>Ligar o Visual Studio Team Services e do GitHub

Configure uma ligação entre o seu projeto VSTS e a sua conta do GitHub.

1. No projeto do Visual Studio Team Services, clique em de **definições** ícone na barra de ferramentas e selecione **serviços**.

    ![Visual Studio Team Services, o-ligação externa](./media/container-service-docker-swarm-setup-ci-cd/vsts-services-menu.png)

2. No lado esquerdo, clique em **ponto final de serviço novo** > **GitHub**.

    ![Visual Studio Team Services, o-GitHub](./media/container-service-docker-swarm-setup-ci-cd/vsts-github.png)

3. Para autorizar VSTS para trabalhar com a sua conta do GitHub, clique em **autorizar** e siga o procedimento na janela abre.

    ![Visual Studio Team Services - autorizar GitHub](./media/container-service-docker-swarm-setup-ci-cd/vsts-github-authorize.png)

### <a name="connect-vsts-to-your-azure-container-registry-and-azure-container-service-cluster"></a>Ligar VSTS ao seu registo de contentor do Azure e o cluster do serviço de contentor do Azure

Os última passos antes de obter no pipeline de CI/CD são configurar ligações externas ao seu registo de contentor e o cluster Docker Swarm no Azure. 

1. No **serviços** definições do seu projeto de Visual Studio Team Services, adicionar um ponto final de serviço do tipo **Docker registo**. 

2. No pop-up é aberta, introduza o URL e as credenciais do seu registo de contentor do Azure.

    ![Visual Studio Team Services, o-registo de Docker](./media/container-service-docker-swarm-setup-ci-cd/vsts-registry.png)

3. Para o cluster Docker Swarm, adicionar um ponto final do tipo **SSH**. Em seguida, introduza as informações de ligação de SSH do seu cluster Swarm.

    ![Visual Studio Team Services - SSH](./media/container-service-docker-swarm-setup-ci-cd/vsts-ssh.png)

Toda a configuração é feita agora. Passos seguintes, vai criar o pipeline de CI/CD que cria e implementa a aplicação para o cluster Docker Swarm. 

## <a name="step-2-create-the-build-definition"></a>Passo 2: Criar a definição de compilação

Neste passo, configure um definitionfor de compilação do projeto VSTS e definir o fluxo de trabalho de compilação para as imagens de contentor

### <a name="initial-definition-setup"></a>Configuração inicial da definição

1. Para criar uma definição de compilação, ligar ao seu projeto de Visual Studio Team Services e clique em **compilar & versão**. 

2. No **definições de compilação** secção, clique em **+ novo**. Selecione o **vazio** modelo.

    ![Visual Studio Team Services, o-nova definição de compilação](./media/container-service-docker-swarm-setup-ci-cd/create-build-vsts.png)

3. Configurar a nova compilação com uma origem de repositório do GitHub, verificação **integração contínua**e selecione a fila de agente onde que registou o agente do Linux. Clique em **criar** para criar a definição de compilação.

    ![Visual Studio Team Services - criar a definição de compilação](./media/container-service-docker-swarm-setup-ci-cd/vsts-create-build-github.png)

4. No **definições de compilação** página, abre pela primeira vez o **repositório** separador e configurar a compilação para utilizar a bifurcação do projeto MyShop que criou nos pré-requisitos. Certifique-se de que seleciona *acs docs* como o **ramo predefinido**.

    ![Visual Studio Team Services, o-configuração do repositório de compilação](./media/container-service-docker-swarm-setup-ci-cd/vsts-github-repo-conf.png)

5. No **Acionadores** separador, configure a compilação sejam acionadas após cada consolidação. Selecione **integração contínua** e **lote de alterações**.

    ![Visual Studio Team Services, o-configuração de Acionador de compilação](./media/container-service-docker-swarm-setup-ci-cd/vsts-github-trigger-conf.png)

### <a name="define-the-build-workflow"></a>Definir o fluxo de trabalho de compilação
Os passos seguintes definem o fluxo de trabalho de compilação. Existem cinco contentor imagens para criar para o *MyShop* aplicação. Cada imagem é compilada com Dockerfile localizado nas pastas do projeto:

* ProductsApi
* Proxy
* RatingsApi
* RecommandationsApi
* ShopFront

Tem de adicionar duas Docker passos para cada imagem, um para compilar a imagem e um para enviar a imagem no registo de contentor do Azure. 

1. Para adicionar um passo no fluxo de trabalho de compilação, clique em **+ Adicionar passo de compilação** e selecione **Docker**.

    ![Visual Studio Team Services - adicionar passos de compilação](./media/container-service-docker-swarm-setup-ci-cd/vsts-build-add-task.png)

2. Para cada imagem, configure um passo que utiliza o `docker build` comando.

    ![Visual Studio Team Services, o-compilação do Docker](./media/container-service-docker-swarm-setup-ci-cd/vsts-docker-build.png)

    Para a operação de compilação, selecione o seu registo de contentor do Azure, o **construir uma imagem** ação e o Dockerfile que define cada imagem. Definir o **contexto de compilação** como o Dockerfile diretório de raiz e definir o **nome da imagem**. 
    
    Como é mostrado no ecrã anterior, inicie o nome da imagem com o URI do seu registo de contentor do Azure. (Pode também utilizar uma variável de compilação parametrizar a tag de imagem, tais como o identificador de compilação neste exemplo.)

3. Para cada imagem, configure um segundo passo que utiliza o `docker push` comando.

    ![Visual Studio Team Services, o-Docker Push](./media/container-service-docker-swarm-setup-ci-cd/vsts-docker-push.png)

    Para a operação de push, selecione o seu registo de contentor do Azure, o **Push uma imagem** ação e introduza o **nome da imagem** que é criada no passo anterior.

4. Depois de configurar os passos de push e de compilação para cada uma das cinco imagens, adicione duas mais passos no fluxo de trabalho de compilação.

    a. Uma tarefa da linha de comandos que utiliza um script de deteção para substituir o *BuildNumber* ocorrência no ficheiro docker-Compose.yml com atual criar ID. Consulte o ecrã seguinte para obter mais detalhes.

    ![Visual Studio Team Services - ficheiro de atualização Compose](./media/container-service-docker-swarm-setup-ci-cd/vsts-build-replace-build-number.png)

    b. Uma tarefa que ignora o ficheiro de Compose atualizado como um artefacto de compilação, pelo que pode ser utilizado na versão. Consulte o ecrã seguinte para obter mais detalhes.

    ![Visual Studio Team Services - publicar Compose ficheiro](./media/container-service-docker-swarm-setup-ci-cd/vsts-publish-compose.png) 

5. Clique em **guardar** e atribua o nome da definição de compilação.

## <a name="step-3-create-the-release-definition"></a>Passo 3: Criar a definição da versão

Visual Studio Team Services permite-lhe [gerir versões em ambientes](https://www.visualstudio.com/team-services/release-management/). Pode ativar a implementação contínua para se certificar de que a aplicação é implementada nos seus ambientes diferentes (por exemplo, o desenvolvimento, teste, pré-produção e de produção) de forma uniforme. Pode criar um novo ambiente que representa o seu cluster Swarm Docker de serviço de contentor do Azure.

![Visual Studio Team Services, o-versão para ACS](./media/container-service-docker-swarm-setup-ci-cd/vsts-release-acs.png) 

### <a name="initial-release-setup"></a>Configuração inicial de versão

1. Para criar uma definição de versão, clique em **versões** > **+ da versão**

2. Para configurar a origem de artefactos, clique em **artefactos** > **ligar uma origem de artefacto**. Ligação aqui, esta nova definição de versão para a compilação que definiu no passo anterior. Ao fazê-lo, o ficheiro docker-Compose.yml está disponível no processo de versão.

    ![Visual Studio Team Services, o-artefactos de versão](./media/container-service-docker-swarm-setup-ci-cd/vsts-release-artefacts.png) 

3. Para configurar o acionador de versão, clique em **Acionadores** e selecione **a implementação contínua**. Defina o acionador na mesma origem de artefactos. Esta definição garantirá que inicia uma nova versão, assim que a compilação for concluída com êxito.

    ![Visual Studio Team Services, o-Acionadores de versão](./media/container-service-docker-swarm-setup-ci-cd/vsts-release-trigger.png) 

### <a name="define-the-release-workflow"></a>Definir o fluxo de trabalho de versão

O fluxo de trabalho de versão é composto por duas tarefas que adicionar.

1. Configurar uma tarefa para copiar em segurança o ficheiro de compose para um *implementar* pasta o Docker Swarm nó mestre, utilizando a ligação de SSH que configurou anteriormente. Consulte o ecrã seguinte para obter mais detalhes.

    ![Visual Studio Team Services, o-versão SCP](./media/container-service-docker-swarm-setup-ci-cd/vsts-release-scp.png)

2. Configurar uma tarefa para executar um comando de bash para ser executada segundo `docker` e `docker-compose` comandos no nó principal. Consulte o ecrã seguinte para obter mais detalhes.

    ![Visual Studio Team Services, o-Bash de versão](./media/container-service-docker-swarm-setup-ci-cd/vsts-release-bash.png)

    O comando executado no principal, utilize o CLI do Docker e a CLI do Docker Compose para fazer as seguintes tarefas:

    - Início de sessão no registo de contentor do Azure (utiliza três variab'les de compilação que estão definidos no **variáveis** separador)
    - Definir o **DOCKER_HOST** variável para trabalhar com o ponto final Swarm (: 2375)
    - Navegue para o *implementar* pasta que foi criada pela tarefa de cópia de segurança anterior e que contém o ficheiro docker-Compose.yml 
    - Executar `docker-compose` comandos que as novas imagens de extração parar os serviços, remova os serviços e criar contentores.

    >[!IMPORTANT]
    > Como é mostrado no ecrã anterior, deixe o **falhar no STDERR** caixa de verificação desmarcada. Esta é uma definição importante, porque `docker-compose` imprime várias mensagens de diagnóstico, tais como contentores são parar ou que está a ser eliminados, a saída de erro padrão. Se selecionar a caixa de verificação, Visual Studio Team Services relatórios que ocorreram erros durante o lançamento, mesmo se tudo correr bem.
    >
3. Guarde esta nova definição de versão.


>[!NOTE]
>Esta implementação inclui algum período de indisponibilidade, uma vez que são parar os serviços antigos e executar de novo. É possível evitar o problema efetuando uma implementação de blue verde.
>

## <a name="step-4-test-the-cicd-pipeline"></a>Passo 4. Testar o pipeline de CI/CD

Agora que são efetuadas com a configuração, está na altura para testar este novo pipeline de CI/CD. É a forma mais fácil para testá-lo para atualizar o código de origem e consolidar as alterações no repositório do GitHub. Poucos segundos depois de emitir o código, verá uma nova compilação em execução no Visual Studio Team Services. Depois de concluída com êxito, uma nova versão será acionada e irá implementar a nova versão da aplicação no cluster do serviço de contentor do Azure.

## <a name="next-steps"></a>Passos Seguintes

* Para obter mais informações sobre CI/CD com o Visual Studio Team Services, consulte o [descrição geral de compilação VSTS](https://www.visualstudio.com/docs/build/overview).
