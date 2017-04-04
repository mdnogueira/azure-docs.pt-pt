---
title: "Compilação e integração contínuas para a sua aplicação Java do Linux do Azure Service Fabric com o Jenkins | Microsoft Docs"
description: "Compilação e integração contínuas para a sua aplicação Java do Linux com o Jenkins"
services: service-fabric
documentationcenter: java
author: sayantancs
manager: timlt
editor: 
ms.assetid: 02b51f11-5d78-4c54-bb68-8e128677783e
ms.service: service-fabric
ms.devlang: java
ms.topic: hero-article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 02/27/2017
ms.author: saysa
translationtype: Human Translation
ms.sourcegitcommit: 4f2230ea0cc5b3e258a1a26a39e99433b04ffe18
ms.openlocfilehash: 71e3d130f22515d22dc7f486f3dede936b874049
ms.lasthandoff: 03/25/2017


---
# <a name="use-jenkins-to-build-and-deploy-your-linux-java-application"></a>Utilize o Jenkins para compilar e implementar a sua aplicação Java do Linux
O Jenkins é uma ferramenta popular para integração e implementação contínuas de aplicações. Eis como pode utilizar o Jenkins para compilar e implementar a sua aplicação do Azure Service Fabric.

## <a name="general-prerequisites"></a>Pré-requisitos gerais
- Ter o Git instalado localmente. Pode instalar a versão adequada do Git na [página de transferências do Git](https://git-scm.com/downloads), com base no seu sistema operativo. Se estiver familiarizado com o Git, saiba mais sobre o mesmo na [documentação do Git](https://git-scm.com/docs).
- Ter pronto o plug-in Jenkins do Service Fabric. Pode transferi-lo a partir [das transferências do Service Fabric](https://servicefabricdownloads.blob.core.windows.net/jenkins/serviceFabric.hpi).

## <a name="set-up-jenkins-inside-a-service-fabric-cluster"></a>Configurar o Jenkins num cluster do Service Fabric

Pode configurar o Jenkins dentro ou fora de um cluster do Service Fabric. As secções seguintes mostram como configurá-lo num cluster.

### <a name="prerequisites"></a>Pré-requisitos
1. Ter pronto um cluster Linux do Service Fabric. Os clusters do Service Fabric criados a partir do portal do Azure já tem o Docker instalado. Se estiver a executar o cluster localmente, utilize o comando ``docker info`` para verificar se o Docker está instalado. Se não estiver, utilize os comandos seguintes para instalá-lo:

  ```sh
  sudo apt-get install wget
  wget -qO- https://get.docker.io/ | sh
  ```
2. Ter a aplicação de contentor do Service Fabric implementada no cluster, utilizando os seguintes passos:

  ```sh
git clone https://github.com/Azure-Samples/service-fabric-java-getting-started.git -b JenkinsDocker
cd service-fabric-java-getting-started/Services/JenkinsDocker/
azure servicefabric cluster connect http://PublicIPorFQDN:19080   # Azure CLI cluster connect command
bash Scripts/install.sh
```
Instala o contentor do Jenkins no cluster e pode ser monitorizado com o Service Fabric Explorer.

### <a name="steps"></a>Passos
1. A partir do browser, aceda a ``http://PublicIPorFQDN:8081``. Fornece o caminho da palavra-passe de administrador inicial necessária para iniciar sessão. Pode continuar a utilizar o Jenkins como utilizador administrador. Ou pode criar e alterar o utilizador, depois de iniciar sessão com a conta de administrador inicial.

   > [!NOTE]
   > Certifique-se de que a porta 8081 está especificada como a porta de ponto final da aplicação enquanto estiver a criar o cluster.
   >

2. Obtenha o ID de instância do contentor com ``docker ps -a``.
3. Inicie uma sessão Secure Shell (SSH) no contentor e cole o caminho que lhe foi mostrado no portal do Jenkins. Por exemplo, se no portal for mostrado o caminho `PATH_TO_INITIAL_ADMIN_PASSWORD`, execute:

  ```sh
  docker exec -t -i [first-four-digits-of-container-ID] /bin/bash   # This takes you inside Docker shell
  cat PATH_TO_INITIAL_ADMIN_PASSWORD
  ```

4. Utilize os passos mencionados em [Generating a new SSH key and adding it to the SSH agent](https://help.github.com/articles/generating-a-new-ssh-key-and-adding-it-to-the-ssh-agent/) (Gerar uma chave SSH nova e adicioná-la ao agente SSH) para configurar o GitHub para funcionar com o Jenkins.
    * Utilize as instruções fornecidas pelo GitHub para gerar a chave SSH e adicioná-la à conta GitHub que está a alojar o repositório.
    * Execute os comandos mencionados na ligação anterior na shell Docker do Jenkins (e não no seu anfitrião).
    * Para iniciar sessão na shell do Jenkins a partir do seu anfitrião, utilize o seguinte comando:

  ```sh
  docker exec -t -i [first-four-digits-of-container-ID] /bin/bash
  ```

## <a name="set-up-jenkins-outside-a-service-fabric-cluster"></a>Configurar o Jenkins fora de um cluster do Service Fabric

Pode configurar o Jenkins dentro ou fora de um cluster do Service Fabric. As secções seguintes mostram como configurá-lo fora de um cluster.

### <a name="prerequisites"></a>Pré-requisitos
Tem de ter o Docker instalado. Os comandos seguintes podem ser utilizados para instalar o Docker a partir do terminal:

  ```sh
  sudo apt-get install wget
  wget -qO- https://get.docker.io/ | sh
  ```

Agora, quando executar o ``docker info`` no terminal, deverá ver na saída que o serviço Docker está em execução.

### <a name="steps"></a>Passos
  1. Extraia a imagem do contentor do Jenkins do Service Fabric: ``docker pull raunakpandya/jenkins:v1``
  2. Execute a imagem do contentor:``docker run -itd -p 8080:8080 raunakpandya/jenkins:v1``
  3. Obtenha o ID da instância de imagem do contentor. Pode listar todos os contentores de Docker com o comando ``docker ps –a``
  4. Utilize os passos seguintes para iniciar sessão no portal do Jenkins:

    * ```sh
    docker exec [first-four-digits-of-container-ID] cat /var/jenkins_home/secrets/initialAdminPassword
    ```
    Se o ID do contentor for 2d24a73b5964, utilize 2d24.
    * Esta palavra-passe é necessária para iniciar sessão no dashboard do Jenkins a partir do portal, que é ``http://<HOST-IP>:8080``
    * Depois de iniciar sessão pela primeira vez, pode criar a sua própria conta de utilizador e utilizá-la para fins futuros ou pode continuar a utilizar a conta de administrador. Depois de criar um utilizador, tem de continuar com o mesmo.
  5. Utilize os passos mencionados em [Generating a new SSH key and adding it to the SSH agent](https://help.github.com/articles/generating-a-new-ssh-key-and-adding-it-to-the-ssh-agent/) (Gerar uma chave SSH nova e adicioná-la ao agente SSH) para configurar o GitHub para funcionar com o Jenkins.
        * Utilize as instruções fornecidas pelo GitHub para gerar a chave SSH e adicioná-la à conta GitHub que está a alojar o repositório.
        * Execute os comandos mencionados na ligação anterior na shell Docker do Jenkins (e não no seu anfitrião).
        * Para iniciar sessão na shell do Jenkins a partir do seu anfitrião, utilize os comandos seguintes:

      ```sh
      docker exec -t -i [first-four-digits-of-container-ID] /bin/bash
      ```

Verifique se o cluster ou a máquina em que a imagem de contentor do Jenkins está alojada tem um IP destinado ao público. Isto permite que a instância do Jenkins receba notificações do GitHub.

## <a name="install-the-service-fabric-jenkins-plug-in-from-the-portal"></a>Instalar o plug-in Jenkins do Service Fabric a partir do portal

1. Ir para ``http://PublicIPorFQDN:8081``
2. A partir do dashboard do Jenkins, selecione **Gerir Jenkins** > **Gerir Plug-ins** > **Avançadas**.
Aqui, pode carregar um plug-in. Selecione a opção **Choose file** (Escolher ficheiro) e, em seguida, selecione o ficheiro **serviceFabric.hpi**, que transferiu nos pré-requisitos. Depois de selecionar **Upload** (Carregar), o Jenkins instala automaticamente o plug-in. Reinicie, se solicitado.

## <a name="create-and-configure-a-jenkins-job"></a>Criar e configurar uma tarefa do Jenkins

1. Crie um **new item** (item novo) a partir do dashboard.
2. Introduza um nome para o item (por exemplo, **MyJob**). Selecione **free-style project** (projeto de estilo livre) e clique em **OK**.
3. Vá para a página da tarefa e clique em **Configure** (Configurar).

   a. Na secção geral, em **GitHub project** (Projeto do GitHub), especifique o URL do seu projeto do GitHub. Este URL aloja a aplicação Java do Service Fabric que pretende integrar no fluxo de integração contínua e implementação contínua (CI/CD) (por exemplo, ``https://github.com/sayantancs/SFJenkins``).

   b. Na secção **Gestão de Código Fonte**, selecione **Git**. Especifique o URL do repositório que aloja a aplicação Java do Service Fabric que quer integrar no fluxo CI/CD do Jenkins (por exemplo, ``https://github.com/sayantancs/SFJenkins.git``). Também pode especificar aqui o ramo a compilar, (por exemplo, **/master**).
4. Configure o seu *GitHub* (o que está a alojar o repositório) para que seja capaz de comunicar com o Jenkins. Utilize os passos seguintes:

   a. Aceda à sua página de repositório do GitHub. Aceda a **Definições** > **Integrações e Serviços**.

   b. Selecione **Add Service** (Adicionar Serviço), escreva **Jenkins** e selecione **Jenkins-GitHub plugin** (Plug-in Jenkins-GitHub).

   c. Introduza o URL do webhook do Jenkins (por predefinição, deve ser ``http://<PublicIPorFQDN>:8081/github-webhook/``). Clique em **add/update service** (adicionar/atualizar serviço).

   d. É enviado um evento de teste para a instância do Jenkins. Deverá ver uma verificação a verde através do webhook do GitHub, e o seu projeto será compilado.

   e. Na secção **Build Triggers** (Criar Acionadores), selecione a opção de compilação que pretende. Neste exemplo, pretende-se acionar uma compilação sempre que se dá um envio para o repositório. Para isso, selecionamos **GitHub hook trigger for GITScm polling** (Acionador de hook do GitHub para consulta GITScm). (Anteriormente, esta opção era denominada **Build when a change is pushed to GitHub** [Compilar quando é enviada uma alteração para o GitHub]).

   f. Na **secção Build** (Compilar), na lista pendente **Add build step** (Adicionar passo de compilação), selecione a opção **Invoke Gradle Script** (Invocar Gradle Script). No widget fornecido, especifique o caminho para o **Root build script** (Script de compilação de raiz) da sua aplicação. Este obtém o build.gradle a partir do caminho especificado e funciona em conformidade. Se criar um projeto com o nome ``MyActor``(ao utilizar o plug-in do Eclipse ou o gerador Yeoman), o script de compilação de raiz deve conter ``${WORKSPACE}/MyActor``. Veja a captura de ecrã seguinte para obter um exemplo do aspeto:

    ![Ação de Compilação do Jenkins do Service Fabric][build-step]

   g. No menu pendente **Post-Build Actions** (Ações de Pós-compilação), selecione **Deploy Service Fabric Project** (Implementar Projeto do Service Fabric). Aqui, tem de indicar os detalhes do cluster no qual a aplicação do Service Fabric compilada com o Jenkins seria implementada. Também tem de indicar os detalhes adicionais da aplicação utilizados para implementá-la. Veja a captura de ecrã seguinte para obter um exemplo do aspeto:

    ![Ação de Compilação do Jenkins do Service Fabric][post-build-step]

   > [!NOTE]
   > Este cluster seria igual ao que aloja a aplicação de contentor do Jenkins, no caso de estar a utilizar o Service Fabric para implementar a imagem de contentor do Jenkins.
   >

## <a name="next-steps"></a>Passos seguintes
O GitHub e o Jenkins estão agora configurados. Considere fazer algumas alterações de exemplo ao seu projeto ``MyActor`` no exemplo de repositório, https://github.com/sayantancs/SFJenkins. Envie as alterações para um ramo ``master`` remoto (ou para qualquer ramo que tenha configurado para funcionar com o mesmo). Isto aciona a tarefa ``MyJob`` do Jenkins, que configurou. Obtém as alterações a partir do GitHub, compila-as e implementa a aplicação no ponto final do cluster que especificou nas ações de pós-compilação.  

  <!-- Images -->
  [build-step]: ./media/service-fabric-cicd-your-linux-java-application-with-jenkins/build-step.png
  [post-build-step]: ./media/service-fabric-cicd-your-linux-java-application-with-jenkins/post-build-step.png

