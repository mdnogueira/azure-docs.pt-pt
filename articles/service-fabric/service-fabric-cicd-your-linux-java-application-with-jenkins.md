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
ms.sourcegitcommit: c1cd1450d5921cf51f720017b746ff9498e85537
ms.openlocfilehash: 335f41e2e4a40e64e87382ea338673de3ab79c27
ms.lasthandoff: 03/14/2017


---
# <a name="build-and-deploy-your-linux-java-application-using-jenkins"></a>Compilar e implementar a sua aplicação Java do Linux com o Jenkins
O Jenkins é uma ferramenta popular para integração e implementação contínuas. Este documento descreve como criar e implementar a sua aplicação do Service Fabric com o Jenkins.

## <a name="general-prerequisites"></a>Pré-requisitos Gerais
1. Ter o git instalado localmente. Pode instalar a versão adequada do git a partir de [aqui](https://git-scm.com/downloads), com base no seu SO. Se não estiver familiarizado com o git, siga os passos mencionados na [documentação](https://git-scm.com/docs) para ficar a conhecê-lo.
2. Ter pronto o plug-in Jenkins do Service Fabric. Transfira o plug-in Jenkins do Service Fabric a partir de [aqui](https://servicefabricdownloads.blob.core.windows.net/jenkins/serviceFabric.hpi).

## <a name="setting-up-jenkins-inside-a-service-fabric-cluster"></a>Configurar o Jenkins num cluster do Service Fabric

### <a name="prerequisites"></a>Pré-requisitos
1. Ter pronto um cluster Linux do Service Fabric. O cluster do Service Fabric criado a partir do portal do Azure já tem o Docker instalado. Se estiver a executar o cluster localmente, verifique se o Docker está ou não instalado, com o comando ``docker info`` e, se não estiver instalado, instale-o em conformidade com os seguintes comandos:

  ```sh
  sudo apt-get install wget
  wget -qO- https://get.docker.io/ | sh
  ```
2. Ter a aplicação de contentor do Service Fabric implementada no cluster através dos seguintes passos:

  ```sh
git clone https://github.com/Azure-Samples/service-fabric-java-getting-started.git -b JenkinsDocker
cd service-fabric-java-getting-started/Services/JenkinsDocker/
azure servicefabric cluster connect http://PublicIPorFQDN:19080   # Azure CLI cluster connect command
bash Scripts/install.sh
```
Isto instala o contentor do Jenkins no cluster e pode ser monitorizado com o Service Fabric Explorer.

### <a name="steps"></a>Passos
1. Aceda ao URL ``http://PublicIPorFQDN:8081`` a partir do browser. Fornece o caminho da palavra-passe de administrador inicial necessária para iniciar sessão. Pode continuar a utilizar o Jenkins como utilizador administrador ou pode criar e alterar o utilizador, assim que iniciar sessão com a conta de administrador inicial.

  > [!NOTE]
  > Certifique-se de que a porta 8081 está especificada como a porta de ponto final da aplicação ao criar o cluster
  >

2. Obtenha a id de instância do contentor com o ``docker ps -a``.
3. Inicie uma sessão SSH para o contentor e colar o caminho que lhe foi mostrado no portal do Jenkins. Por exemplo, se no portal for mostrado o caminho `PATH_TO_INITIAL_ADMIN_PASSWORD`, pode -

  ```sh
  docker exec -t -i [first-four-digits-of-container-ID] /bin/bash   # This takes you inside Docker shell
  cat PATH_TO_INITIAL_ADMIN_PASSWORD
  ```

4. Configure o GitHub para funcionar com o Jenkins através dos passos mencionados na [ligação](https://help.github.com/articles/generating-a-new-ssh-key-and-adding-it-to-the-ssh-agent/).
    * Utilize as instruções fornecidas pelo GitHub para gerar a chave SSH e adicionar a chave SSH à conta GitHub que está a alojar o repositório.
    * Execute os comandos mencionados na ligação anterior, na shell Docker do Jenkins (e não no seu anfitrião)
    * Para iniciar sessão na shell do Jenkins a partir do seu anfitrião, utilize o seguinte comando:

  ```sh
  docker exec -t -i [first-four-digits-of-container-ID] /bin/bash
  ```

## <a name="setting-up-jenkins-outside-a-service-fabric-cluster"></a>Configurar o Jenkins fora de um cluster do Service Fabric

### <a name="prerequisites"></a>Pré-requisitos
Tem de ter o Docker instalado. Os comandos seguintes podem ser utilizados para instalar o Docker a partir do terminal:

  ```sh
  sudo apt-get install wget
  wget -qO- https://get.docker.io/ | sh
  ```

Agora, quando executar o ``docker info`` no terminal, deverá ver na saída que o serviço Docker está em execução.

### <a name="steps"></a>Passos
  1. Extraia a imagem do contentor do Jenkins do Service Fabric: ``docker pull servicefabric-microsoft.azurecr.io/jenkins:v1``
  2. Execute a imagem do contentor:``docker run -itd -p 8080:8080 servicefabric-microsoft.azurecr.io/jenkins:v1``
  3. Obtenha o ID da instância de imagem do contentor. Pode listar todos os contentores de Docker com o comando ``docker ps –a``
  4. Inicie sessão no portal do Jenkins através dos seguintes passos:

    * ```sh
    docker exec [first-four-digits-of-container-ID] cat /var/jenkins_home/secrets/initialAdminPassword
    ```
    Se o ID do contentor for 2d24a73b5964, utilize 2d24.
    * Esta palavra-passe é necessária para iniciar sessão no dashboard do Jenkins a partir do portal, que é ``http://<HOST-IP>:8080``
    * Quando iniciar sessão pela primeira vez, pode criar a sua própria conta de utilizador e utilizá-la para fins futuros ou pode continuar a utilizar a conta de administrador. Depois de criar um utilizador, deve continuar com o mesmo.
  5. Configure o GitHub para funcionar com o Jenkins através dos passos mencionados na [ligação](https://help.github.com/articles/generating-a-new-ssh-key-and-adding-it-to-the-ssh-agent/).
      * Utilize as instruções fornecidas pelo GitHub para gerar a chave SSH e adicionar a chave ssh à conta GitHub que está (estaria) a alojar o repositório.
      * Execute os comandos mencionados na ligação anterior, na shell Docker do Jenkins (e não no seu anfitrião)
      * Para iniciar sessão na shell do Jenkins a partir do seu anfitrião, utilize os seguintes comandos:

      ```sh
      docker exec -t -i [first-four-digits-of-container-ID] /bin/bash
      ```

    > [!NOTE]
    > Certificar-se de que o cluster/máquina onde a imagem de contentor do Jenkins está alojada tem um IP destinado ao público, de forma a que as notificações do GitHub sejam recebidas pela instância do Jenkins.
    >

## <a name="install-the-service-fabric-jenkins-plugin-from-portal"></a>Instalar o plug-in Jenkins do Service Fabric a partir do portal

1. Ir para ``http://PublicIPorFQDN:8081``
2. A partir do dashboard do Jenkins, selecione **Gerir Jenkins** -> **Gerir Plug-ins** -> **Avançadas**.
Aqui, pode carregar um plug-in. Selecione a opção **Escolher ficheiro** e, em seguida, selecione o ficheiro serviceFabric.hpi que transferiu nos pré-requisitos. Depois de selecionar o carregamento, o Jenkins instala automaticamente o plug-in. Reinicie, se solicitado.

## <a name="creating-and-configuring-a-jenkins-job"></a>Criar e configurar uma tarefa do Jenkins

1. Crie um **novo item** a partir do dashboard
2. Introduza um nome de item - por exemplo **MinhaTarefa**, selecione o projeto de estilo livre e clique em OK
3. Em seguida, vá para a página da tarefa e clique em **Configurar** -
  * Na secção geral, em **Projeto Github**, especifique o URL de projeto do GitHub que aloja a aplicação Java do Service Fabric que quer integrar no fluxo CI/CD do Jenkins (por exemplo, - ``https://github.com/sayantancs/SFJenkins``).
  * Na secção **Gestão de Código Fonte**, selecione **Git**. Especifique o URL do repositório que aloja a aplicação Java do Service Fabric que quer integrar no fluxo CI/CD do Jenkins (por exemplo, - ``https://github.com/sayantancs/SFJenkins.git``). Também pode especificar aqui o ramo a compilar, por exemplo - ***/master**.
4. Configure o seu *GitHub* (o que está a alojar o repositório) para que seja capaz de comunicar com o Jenkins, através dos passos seguintes -
  1. Aceda à sua página de repositório do GitHub. Aceda a **Definições** -> **Integrações e Serviços**.
  2. Selecione **Adicionar Serviço**, escreva no Jenkins e selecione o **Plug-in Jenkins-Github**.
  3. Introduza o URL do webhook do Jenkins (por predefinição, deve ser ``http://<PublicIPorFQDN>:8081/github-webhook/``). Clique em adicionar/atualizar serviço.
  4. É enviado um evento de teste para a instância do Jenkins. Deverá ver uma verificação a verde através do webhook do GitHub, e o seu projeto será compilado!
  5. Na secção **Compilar Acionadores**, selecione a opção de compilação que quer - neste caso de utilização, planeamos acionar uma compilação sempre que algo é emitido para o repositório - por conseguinte, a opção correspondente seria **Acionador hook do GitHub para consulta GITScm** (anteriormente era “Compilar quando uma alteração for emitida para o GitHub”)
  6. Em **Compilar secção**, na lista pendente **Adicionar passo de compilação**, selecione a opção **Invocar Gradle Script**. No widget fornecido, especifique o caminho para o **Script de compilação de raiz** da sua aplicação. Este obtém o build.gradle a partir do caminho especificado e funciona em conformidade. Se criar um projeto com o nome ``MyActor``(ao utilizar o plug-in do Eclipse ou o gerador Yeoman), o script de compilação de raiz deve conter - ``${WORKSPACE}/MyActor``. Como exemplo, esta secção assemelha-lhe principalmente a -

    ![Ação de Compilação do Jenkins do Service Fabric][build-step]
  7. No menu pendente **Ações de Pós-compilação**, selecione **Implementar Projeto do Service Fabric**. Aqui, tem de fornecer detalhes do cluster no qual a aplicação do Service Fabric compilada com o Jenkins seria implementada, bem como detalhes adicionais da aplicação utilizados para implementar a aplicação. A captura de ecrã seguinte pode ser utilizada como referência:

    ![Ação de Compilação do Jenkins do Service Fabric][post-build-step]

 > [!NOTE]
 > Este cluster seria igual ao que aloja a aplicação de contentor do Jenkins, no caso de estar a utilizar o Service Fabric para implementar a imagem de contentor do Jenkins.
 >

### <a name="end-to-end-scenario"></a>Cenário ponto a ponto
O seu GitHub e Jenkins estão agora configurados e pode efetuar algumas alterações de exemplo no projeto do ``MyActor`` no repositório, por exemplo, https://github.com/sayantancs/SFJenkins e emitir as suas alterações para o ramo remoto ``master`` (ou qualquer ramo que tenha configurado para trabalho). Isto irá acionar a tarefa ``MyJob`` do Jenkins, que configurou. O que faz é basicamente - obter as alterações a partir do GitHub, compilá-las e implementar a aplicação no ponto final de cluster que especificou nas ações de pós-compilação.  

  <!-- Images -->
  [build-step]: ./media/service-fabric-cicd-your-linux-java-application-with-jenkins/build-step.png
  [post-build-step]: ./media/service-fabric-cicd-your-linux-java-application-with-jenkins/post-build-step.png

