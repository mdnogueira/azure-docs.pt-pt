---
title: "Compilação e implementação contínuas para a sua aplicação Java do Linux com o Jenkins | Microsoft Docs"
description: "Compilação e implementação contínuas para a sua aplicação Java do Linux com o Jenkins"
services: service-fabric
documentationcenter: java
author: sayantancs
manager: raunakp
editor: 
ms.assetid: 02b51f11-5d78-4c54-bb68-8e128677783e
ms.service: service-fabric
ms.devlang: java
ms.topic: hero-article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 02/27/2017
ms.author: sayantancs
translationtype: Human Translation
ms.sourcegitcommit: 72b2d9142479f9ba0380c5bd2dd82734e370dee7
ms.openlocfilehash: d3c64e3ed78a5379592b5e4276c83d26f04d2e1c
ms.lasthandoff: 03/08/2017


---
# <a name="build-and-deploy-your-linux-java-application-using-jenkins"></a>Compilar e implementar a sua aplicação Java do Linux com o Jenkins
O Jenkins é uma ferramenta popular para integração e implementação contínuas. Este documento descreve como criar e implementar a sua aplicação do Service Fabric com o Jenkins.

## <a name="general-prerequisites"></a>Pré-requisitos Gerais
1. Tem de ter o git instalado localmente. Pode instalar a versão adequada do git a partir de [aqui](https://git-scm.com/downloads), com base no seu SO. Se não estiver familiarizado com o git, siga os passos mencionados na [documentação](https://git-scm.com/docs) para ficar a conhecê-lo.
2. Tem de ter pronto o plug-in Jenkins do Service Fabric. Transfira o plug-in Jenkins do Service Fabric a partir de [aqui](https://servicefabricdownloads.blob.core.windows.net/jenkins/serviceFabric.hpi).

## <a name="setting-up-jenkins-inside-a-service-fabric-cluster"></a>Configurar o Jenkins num Cluster do Service Fabric

### <a name="prerequisites"></a>Pré-requisitos
1. Ter pronto um cluster Linux do Service Fabric. Os clusters do Service Fabric criados a partir do portal do Azure já têm o Docker instalado. Se estiver a executar o cluster local, verifique se o Docker está ou não instalado, com o comando ``docker info`` e, se não estiver instalado, instale-o em conformidade, com -
```sh
sudo apt-get install wget
wget -qO- https://get.docker.io/ | sh
```
2. Ter a aplicação de contentor do Service Fabric implementada no cluster ``http://PublicIPorFQDN:19080``. Pode seguir os passos abaixo -
```sh
git clone https://github.com/Azure-Samples/service-fabric-java-getting-started.git -b JenkinsDocker && cd service-fabric-java-getting-started/Services/JenkinsDocker/
azure servicefabric cluster connect http://PublicIPorFQDN:19080 # Azure CLI cluster connect command
bash Scripts/install.sh
```
  Esta ação irá instalar o contentor Jenkins no cluster que ligou. Pode monitorizar a aplicação de contentor utilizando o Service Fabric Explorer.

### <a name="steps"></a>Passos
1. Aceda ao URL ``http://PublicIPorFQDN:8081`` a partir do browser. Irá fornecer-lhe o caminho da palavra-passe de administrador inicial necessária para iniciar sessão. Pode continuar a utilizar o Jenkins como utilizador administrador ou pode criar e alterar o utilizador, assim que iniciar sessão com a conta de administrador inicial.
> [!NOTE]
> Certifique-se de que a porta 8081 está especificada como a porta de ponto final da aplicação ao criar o cluster
>

2. Obtenha a id de instância do contentor com o ``docker ps -a``.
3. Inicie uma sessão SSH para o contentor, ao utilizar e colar o caminho que lhe foi mostrado no portal do Jenkins. Por exemplo, se no portal for mostrado o caminho `PATH_TO_INITIAL_ADMIN_PASSWORD`, pode -  
  ```sh
  docker exec -t -i [first-four-digits-of-container-ID] /bin/bash # This takes you inside docker shell
  cat PATH_TO_INITIAL_ADMIN_PASSWORD
  ```

4. Configurar o GitHub para trabalhar com o Jenkins ao utilizar [isto](https://help.github.com/articles/generating-a-new-ssh-key-and-adding-it-to-the-ssh-agent/).
    * Utilize as instruções fornecidas pelo GitHub para gerar a chave SSH e adicionar a chave ssh à conta GitHub que está (estaria) a alojar o repositório.
    * Execute os comandos mencionados na ligação acima, na shell docker do Jenkins (e não no seu anfitrião)
    * Para iniciar sessão na shell do Jenkins a partir do seu anfitrião, tem de -
  ```sh
  docker exec -t -i [first-four-digits-of-container-ID] /bin/bash
  ```

## <a name="setting-up-jenkins-outside-a-service-fabric-cluster"></a>Configurar o Jenkins fora de um Cluster do Service Fabric

### <a name="prerequisites"></a>Pré-requisitos
1. Tem de ter o docker instalado. Se não o tiver, pode escrever os seguintes comandos no terminal para instalar o docker.
```sh
sudo apt-get install wget
wget -qO- https://get.docker.io/ | sh
```
Agora, ao executar ``docker info`` no terminal, irá ver na saída que o serviço de docker está a executar.

### <a name="steps"></a>Passos
  1. Extraia o contentor Jenkins do Service Fabric: ``docker pull IMAGE_NAME ``
  2. Execute a imagem do contentor:``docker run -itd -p 8080:8080 IMAGE_NAME``
  3. Obtenha o id do seu contentor de docker que inclui a imagem do jenkins (que acabou de instalar). Pode listar todos os contentores de docker com o comando ``docker ps –a``
  4. Obtenha a palavra-passe de administrador para a conta do Jenkins. Para tal, pode fazer o seguinte -
    * ```sh
    docker exec [first-four-digits-of-container-ID] cat /var/jenkins_home/secrets/initialAdminPassword
    ```
    Aqui, se o id de contentor for 2d24a73b5964, terá de inserir apenas 2d24
    * Esta palavra-passe será necessária para iniciar sessão no dashboard do Jenkins a partir do portal, que é ``http://<HOST-IP>:8080``
    * Quando iniciar sessão pela primeira vez, pode criar a sua própria conta de utilizador e utilizá-la para fins futuros ou pode continuar a utilizar a conta de administrador. Depois de criar um novo utilizador, deve continuar com o mesmo.
  5. Configurar o GitHub para trabalhar com o Jenkins ao utilizar [isto](https://help.github.com/articles/generating-a-new-ssh-key-and-adding-it-to-the-ssh-agent/).
      * Utilize as instruções fornecidas pelo GitHub para gerar a chave SSH e adicionar a chave ssh à conta GitHub que está (estaria) a alojar o repositório.
      * Execute os comandos mencionados na ligação acima, na shell docker do Jenkins (e não no seu anfitrião)
      * Para iniciar sessão na shell do Jenkins a partir do seu anfitrião, tem de -
    ```sh
    docker exec -t -i [first-four-digits-of-container-ID] /bin/bash
    ```

> [!NOTE]
> Certificar-se de que o cluster/máquina onde a imagem de contentor do Jenkins está alojada tem um IP destinado ao público, de forma a que as notificações do GitHub sejam recebidas pela instância do Jenkins.
>

## <a name="install-the-service-fabric-jenkins-plugin-from-portal"></a>Instalar o plug-in Jenkins do Service Fabric a partir do portal

1. Ir para ``http://PublicIPorFQDN:8081``
2. No dashboard do Jenkins, selecione ``Manage Jenkins`` -> ``Manage Plugins`` -> ``Advanced``.
Aqui, pode carregar um plug-in. Selecione a opção ``Choose file`` e, em seguida, selecione o ficheiro serviceFabric.hpi que transferiu em Pré-requisitos. Depois de selecionar o carregamento, o Jenkins irá instalar automaticamente o plug-in. Também poderá pedir um reinício, permita-o.

## <a name="creating-and-configuring-a-jenkins-job"></a>Criar e configurar uma tarefa do Jenkins

1. Criar um ``new item`` a partir do dashboard
2. Introduza um novo nome de item - por exemplo ``MyJob``, selecione projeto de estilo livre e clique em OK
3. Em seguida, vá para a página da tarefa e clique em ``Configure`` -
  * Na secção geral, em ``Github project`` especifique o URL de projeto do GitHub que aloja a aplicação Java do Service Fabric que pretende integrar com o fluxo CI/CD do Jenkins (por exemplo, - ``https://github.com/sayantancs/SFJenkins``).
  * Na Secção ``Source Code Management``, selecione ``Git``. Especifique o URL do repositório que aloja a aplicação Java do Service Fabric que pretende integrar com o fluxo CI/CD do Jenkins (por exemplo, - ``https://github.com/sayantancs/SFJenkins.git``). Também pode especificar aqui o ramo a compilar, por exemplo, - ``*/master``.
4. Configure o seu *Github* (ou seja, o que está a alojar o repositório) para que seja capaz de comunicar com o Jenkins, com os passos seguintes -
  - Aceda à sua página de repositório do GitHub. Vá para ``Settings`` -> ``Integrations and Services``.
  - Selecione ``Add Service``, escreva Jenkins, selecione o ``Jenkins-Github plugin``.
  - Introduza o URL do webhook do Jenkins (por predefinição, deve ser ``http://<PublicIPorFQDN>:8081/github-webhook/``). Clique em adicionar/atualizar serviço.
  - Será enviado um evento de teste para a instância do Jenkins. Deverá ver uma verificação a verde através do webhook do GitHub, e o seu projeto será compilado!
  - Na secção ``Build Triggers``, selecione a opção de compilação pretendida - neste caso de utilização, planeamos acionar uma compilação sempre que algo é emitido para o repositório - por conseguinte, a opção correspondente seria - ``GitHub hook trigger for GITScm polling`` (anteriormente era “Compilar quando uma alteração for emitida para o GitHub”)
  - Em ``Build section`` – no menu pendente ``Add build step``, selecione a opção ``Invoke Gradle Script``. No widget fornecido, especifique o caminho do ``Root build script`` para a sua aplicação. Este obtém o build.gradle a partir do caminho especificado e funciona em conformidade. Tenha em atenção que - se criar um projeto com o nome ``MyActor``(ao utilizar o plug-in do Eclipse ou o gerador Yeoman), então o script de compilação de raiz deve conter - ``${WORKSPACE}/MyActor``. Como exemplo, esta secção assemelha-lhe principalmente a -

    ![Ação de Compilação do Jenkins do Service Fabric][build-step]
  - No menu pendente ``Post-Build Actions``, selecione ``Deploy Service Fabric Project``. Aqui, tem de fornecer detalhes do cluster no qual a aplicação do Service Fabric compilada com o Jenkins seria implementada, bem como detalhes adicionais da aplicação utilizados para implementar a aplicação. A captura de ecrã seguinte pode ser utilizada como referência:

    ![Ação de Compilação do Jenkins do Service Fabric][post-build-step]

 >[!Note]
 > Este cluster seria igual ao que aloja a aplicação de contentor do Jenkins, no caso de estar a utilizar o Service Fabric para implementar a imagem de contentor do Jenkins.
 >

### <a name="end-to-end-scenario"></a>Cenário ponto a ponto
O seu GitHub e Jenkins estão agora configurados e pode efetuar algumas alterações de exemplo no projeto do ``MyActor`` no repositório, por exemplo, https://github.com/sayantancs/SFJenkins e emitir as suas alterações para o ramo remoto ``master`` (ou qualquer ramo que tenha configurado para trabalho). Isto irá agora acionar a tarefa ``MyJob`` do Jenkins, que configurou. O que faz é basicamente - obter as alterações a partir do GitHub, compilá-las e implementar a aplicação no ponto final de cluster que especificou nas ações de pós-compilação.  

  <!-- Images -->
  [build-step]: ./media/service-fabric-cicd-your-linux-java-application-with-jenkins/build-step.png
  [post-build-step]: ./media/service-fabric-cicd-your-linux-java-application-with-jenkins/post-build-step.png

