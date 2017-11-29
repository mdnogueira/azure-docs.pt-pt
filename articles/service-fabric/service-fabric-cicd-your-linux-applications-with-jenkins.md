---
title: "Compilação contínua e a integração para as suas aplicações do Azure Service Fabric Linux utilizando Jenkins | Microsoft Docs"
description: "Compilação contínua e a integração para a sua aplicação de Service Fabric Linux utilizando Jenkins"
services: service-fabric
documentationcenter: java
author: sayantancs
manager: timlt
editor: 
ms.assetid: 02b51f11-5d78-4c54-bb68-8e128677783e
ms.service: service-fabric
ms.devlang: java
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/27/2017
ms.author: saysa
ms.openlocfilehash: e9422745de1f46098f1a1b0605c2560f44c02f3c
ms.sourcegitcommit: 310748b6d66dc0445e682c8c904ae4c71352fef2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/28/2017
---
# <a name="use-jenkins-to-build-and-deploy-your-linux-applications"></a>Utilize Jenkins para criar e implementar as aplicações do Linux
O Jenkins é uma ferramenta popular para integração e implementação contínuas de aplicações. Eis como pode utilizar o Jenkins para compilar e implementar a sua aplicação do Azure Service Fabric.

## <a name="general-prerequisites"></a>Pré-requisitos gerais
- Ter o Git instalado localmente. Pode instalar a versão adequada do Git na [página de transferências do Git](https://git-scm.com/downloads), com base no seu sistema operativo. Se estiver familiarizado com o Git, saiba mais sobre o mesmo na [documentação do Git](https://git-scm.com/docs).
- Ter pronto o plug-in Jenkins do Service Fabric. Pode transferi-lo a partir [das transferências do Service Fabric](https://servicefabricdownloads.blob.core.windows.net/jenkins/serviceFabric.hpi).

## <a name="set-up-jenkins-inside-a-service-fabric-cluster"></a>Configurar o Jenkins num cluster do Service Fabric

Pode configurar o Jenkins dentro ou fora de um cluster do Service Fabric. As secções seguintes mostram como configurá-lo dentro de um cluster ao utilizar uma conta de armazenamento do Azure para guardar o estado da instância do contentor.

### <a name="prerequisites"></a>Pré-requisitos
1. Ter pronto um cluster Linux do Service Fabric. Os clusters do Service Fabric criados a partir do portal do Azure já tem o Docker instalado. Se estiver a executar o cluster localmente, utilize o comando ``docker info`` para verificar se o Docker está instalado. Se não estiver, utilize os comandos seguintes para instalá-lo:

  ```sh
  sudo apt-get install wget
  wget -qO- https://get.docker.io/ | sh
  ``` 

   > [!NOTE]
   > Certifique-se de que a porta 8081 é especificada como um ponto final personalizado no cluster.
   >

2. Clone a aplicação, utilizando os seguintes passos:
  ```sh
  git clone https://github.com/Azure-Samples/service-fabric-java-getting-started.git
  cd service-fabric-java-getting-started/Services/JenkinsDocker/
  ```

3. Manter o estado do contentor Jenkins numa partilha de ficheiros:
  * Criar uma conta de armazenamento do Azure no **mesma região** do seu cluster com um nome, tal como ``sfjenkinsstorage1``.
  * Criar um **partilha de ficheiros** sob o armazenamento de conta com um nome, tal como ``sfjenkins``.
  * Clique em **Connect** para a partilha de ficheiros e tenha em atenção os valores apresenta em **ligar a partir de Linux**, o valor deve ter um aspeto semelhante ao abaixo:
  ```sh
  sudo mount -t cifs //sfjenkinsstorage1.file.core.windows.net/sfjenkins [mount point] -o vers=3.0,username=sfjenkinsstorage1,password=<storage_key>,dir_mode=0777,file_mode=0777
  ```

  > [!NOTE]
  > Para partilhas de montagem cifs, tem de ter o pacote de cifs utils instalado em nós de cluster.       
  >

4. Atualize os valores de marcador de posição no ```setupentrypoint.sh``` script com os detalhes de armazenamento do azure a partir do passo 3.
```sh
vi JenkinsSF/JenkinsOnSF/Code/setupentrypoint.sh
```
  * Substitua ``[REMOTE_FILE_SHARE_LOCATION]`` com o valor ``//sfjenkinsstorage1.file.core.windows.net/sfjenkins`` da saída do connect no passo 3 acima.
  * Substitua ``[FILE_SHARE_CONNECT_OPTIONS_STRING]`` com o valor ``vers=3.0,username=sfjenkinsstorage1,password=GB2NPUCQY9LDGeG9Bci5dJV91T6SrA7OxrYBUsFHyueR62viMrC6NIzyQLCKNz0o7pepGfGY+vTa9gxzEtfZHw==,dir_mode=0777,file_mode=0777`` do passo 3 acima.

5. **Apenas clusters segura:** para configurar a implementação de aplicações num cluster de Jenkins seguro, o certificado tem de estar acessível no contentor Jenkins. Nos clusters do Linux, o certificates(PEM) simplesmente é copiado através da loja especificada pelo X509StoreName no contentor. No ApplicationManifest em ContainerHostPolicies adicionar esta referência de certificado e atualize o valor de thumbprint. O valor de thumbprint tem de ser que de um certificados que se encontra localizada no nó.
  ```xml
  <CertificateRef Name="MyCert" X509FindValue="[Thumbprint]"/>
  ```
  > [!NOTE]
  > O valor de thumbprint tem de ser o mesmo que o certificado que é utilizado para ligar ao cluster seguro. 
  >

6. Ligar ao cluster e instalar a aplicação de contentor.

  **Proteger o Cluster**
  ```sh
  sfctl cluster select --endpoint https://PublicIPorFQDN:19080  --pem [Pem] --no-verify # cluster connect command
  bash Scripts/install.sh
  ```

  **Clusters não seguros**
  ```sh
  sfctl cluster select --endpoint http://PublicIPorFQDN:19080 # cluster connect command
  bash Scripts/install.sh
  ```

  Instala o contentor do Jenkins no cluster e pode ser monitorizado com o Service Fabric Explorer.

    > [!NOTE]
    > Pode demorar alguns minutos para a imagem de Jenkins a ser transferido no cluster.
    >

### <a name="steps"></a>Passos
1. A partir do browser, aceda a ``http://PublicIPorFQDN:8081``. Fornece o caminho da palavra-passe de administrador inicial necessária para iniciar sessão. 
2. Observe o Service Fabric Explorer para determinar o nó que o contentor de Jenkins está a ser executado. Secure Shell (SSH) de início de sessão para este nó.
```sh
ssh user@PublicIPorFQDN -p [port]
``` 
3. Obtenha o ID de instância do contentor com ``docker ps -a``.
4. Inicie uma sessão Secure Shell (SSH) no contentor e cole o caminho que lhe foi mostrado no portal do Jenkins. Por exemplo, se no portal for mostrado o caminho `PATH_TO_INITIAL_ADMIN_PASSWORD`, execute:

  ```sh
  docker exec -t -i [first-four-digits-of-container-ID] /bin/bash   # This takes you inside Docker shell
  ```
  ```sh
  cat PATH_TO_INITIAL_ADMIN_PASSWORD # This displays the pasword value
  ```
5. Na página Jenkins Gettting iniciado, escolha o plug-ins selecione a opção de instalar, selecione o **nenhum** caixa de verificação e clique em instalar.
6. Criar um utilizador ou optar por continuar como um administrador.

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
  1. Extraia a imagem do contentor do Jenkins do Service Fabric: ``docker pull sayantancs/jenkins:v9``
  2. Execute a imagem do contentor:``docker run -itd -p 8080:8080 sayantancs/jenkins:v9``
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
Aqui, pode carregar um plug-in. Selecione **Escolher ficheiro**e, em seguida, selecione o **serviceFabric.hpi** de ficheiros, que transferiu em pré-requisitos ou pode transferir [aqui](https://servicefabricdownloads.blob.core.windows.net/jenkins/serviceFabric.hpi). Depois de selecionar **Upload** (Carregar), o Jenkins instala automaticamente o plug-in. Reinicie, se solicitado.

## <a name="create-and-configure-a-jenkins-job"></a>Criar e configurar uma tarefa do Jenkins

1. Crie um **new item** (item novo) a partir do dashboard.
2. Introduza um nome para o item (por exemplo, **MyJob**). Selecione **free-style project** (projeto de estilo livre) e clique em **OK**.
3. Vá para a página da tarefa e clique em **Configure** (Configurar).

   a. Na secção geral, selecione a caixa de verificação **GitHub projeto**, e especifique o URL de projeto do GitHub. Este URL aloja a aplicação Java do Service Fabric que pretende integrar no fluxo de integração contínua e implementação contínua (CI/CD) (por exemplo, ``https://github.com/sayantancs/SFJenkins``).

   b. Na secção **Gestão de Código Fonte**, selecione **Git**. Especifique o URL do repositório que aloja a aplicação Java do Service Fabric que quer integrar no fluxo CI/CD do Jenkins (por exemplo, ``https://github.com/sayantancs/SFJenkins.git``). Também pode especificar aqui o ramo a compilar, (por exemplo, **/master**).
4. Configure o seu *GitHub* (o que está a alojar o repositório) para que seja capaz de comunicar com o Jenkins. Utilize os passos seguintes:

   a. Aceda à sua página de repositório do GitHub. Aceda a **Definições** > **Integrações e Serviços**.

   b. Selecione **Add Service** (Adicionar Serviço), escreva **Jenkins** e selecione **Jenkins-GitHub plugin** (Plug-in Jenkins-GitHub).

   c. Introduza o URL do webhook do Jenkins (por predefinição, deve ser ``http://<PublicIPorFQDN>:8081/github-webhook/``). Clique em **add/update service** (adicionar/atualizar serviço).

   d. É enviado um evento de teste para a instância do Jenkins. Deverá ver uma verificação a verde através do webhook do GitHub, e o seu projeto será compilado.

   e. Na secção **Build Triggers** (Criar Acionadores), selecione a opção de compilação que pretende. Neste exemplo, pretende-se acionar uma compilação sempre que se dá um envio para o repositório. Para isso, selecionamos **GitHub hook trigger for GITScm polling** (Acionador de hook do GitHub para consulta GITScm). (Anteriormente, esta opção era denominada **Build when a change is pushed to GitHub** [Compilar quando é enviada uma alteração para o GitHub]).

   f. **Secção de compilação de aplicações Java:** sob o **Criar secção**, da lista pendente **Adicionar passo de compilação**, selecione a opção **invocar o Gradle Script**. No widget que vem a abrir o menu avançado, especifique o caminho para **raiz compilar o script** para a sua aplicação. Este escolherá gradle do caminho especificado e funciona em conformidade. Se criar um projeto com o nome ``MyActor``(ao utilizar o plug-in do Eclipse ou o gerador Yeoman), o script de compilação de raiz deve conter ``${WORKSPACE}/MyActor``. Veja a captura de ecrã seguinte para obter um exemplo do aspeto:

    ![Ação de Compilação do Jenkins do Service Fabric][build-step]

   g. **Secção de compilação para aplicações do .net Core:** no **Criar secção**, da lista pendente **Adicionar passo de compilação**, selecione a opção **executar Shell**. Na caixa de comando que aparece, o diretório primeiro tem de ser alterada para o caminho onde o ficheiro de build.sh está localizado. Depois do diretório foi alterado o script de build.sh pode ser executado e irá criar a aplicação.

      ```sh
      cd /var/jenkins_home/workspace/[Job Name]/[Path to build.sh]  # change directory to location of build.sh file
      ./build.sh
      ```

    Segue-se um exemplo dos comandos que são utilizados para construir o [contador serviço](https://github.com/Azure-Samples/service-fabric-dotnet-core-getting-started/tree/master/Services/CounterService) exemplo com um nome de tarefa Jenkins de CounterServiceApplication.

    ![Ação de Compilação do Jenkins do Service Fabric][build-step-dotnet]
  
   h. No menu pendente **Post-Build Actions** (Ações de Pós-compilação), selecione **Deploy Service Fabric Project** (Implementar Projeto do Service Fabric). Aqui, tem de indicar os detalhes do cluster no qual a aplicação do Service Fabric compilada com o Jenkins seria implementada. O caminho para o certificado pode ser encontrado ao echoing o valor do variável de ambiente de Certificates_JenkinsOnSF_Code_MyCert_PEM no contentor de eco. Este caminho pode ser utilizado para a chave de cliente e os campos de certificados de cliente.

      ```sh
      echo $Certificates_JenkinsOnSF_Code_MyCert_PEM
      ```
   
    Também tem de indicar os detalhes adicionais da aplicação utilizados para implementá-la. Veja a captura de ecrã seguinte para obter um exemplo do aspeto:

    ![Ação de Compilação do Jenkins do Service Fabric][post-build-step]

      > [!NOTE]
      > Este cluster seria igual ao que aloja a aplicação de contentor do Jenkins, no caso de estar a utilizar o Service Fabric para implementar a imagem de contentor do Jenkins.
      >

## <a name="next-steps"></a>Passos seguintes
O GitHub e o Jenkins estão agora configurados. Considere fazer algumas alterações de exemplo ao seu projeto ``MyActor`` no exemplo de repositório, https://github.com/sayantancs/SFJenkins. Envie as alterações para um ramo ``master`` remoto (ou para qualquer ramo que tenha configurado para funcionar com o mesmo). Isto aciona a tarefa ``MyJob`` do Jenkins, que configurou. Obtém as alterações a partir do GitHub, compila-as e implementa a aplicação no ponto final do cluster que especificou nas ações de pós-compilação.  

  <!-- Images -->
  [build-step]: ./media/service-fabric-cicd-your-linux-application-with-jenkins/build-step.png
  [build-step-dotnet]: ./media/service-fabric-cicd-your-linux-application-with-jenkins/build-step-dotnet.png
  [post-build-step]: ./media/service-fabric-cicd-your-linux-application-with-jenkins/post-build-step.png

