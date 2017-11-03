---
title: "Implementar uma aplicação de arranque mola no Kubernetes no serviço de contentor do Azure | Microsoft Docs"
description: "Este tutorial irá guiá-lo embora os passos para implementar uma aplicação de arranque de mola um Kubernetes cluster no Microsoft Azure."
services: container-service
documentationcenter: java
author: rmcmurray
manager: cfowler
editor: 
ms.assetid: 
ms.service: container-service
ms.workload: na
ms.tgt_pltfrm: multiple
ms.devlang: Java
ms.topic: article
ms.date: 08/04/2017
ms.author: asirveda;robmcm
ms.custom: mvc
ms.openlocfilehash: 0f06d2e3f9550c2afce2d52fdd060a5069629a63
ms.sourcegitcommit: c5eeb0c950a0ba35d0b0953f5d88d3be57960180
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/24/2017
---
# <a name="deploy-a-spring-boot-application-on-a-kubernetes-cluster-in-the-azure-container-service"></a>Implementar uma Aplicação Spring Boot num Cluster do Kubernetes no Azure Container Service

[!INCLUDE [aks-preview-redirect.md](../../../includes/aks-preview-redirect.md)]

O  **[mola Framework]**  é uma arquitetura de open source popular, que ajuda os programadores de Java criar web, móveis e aplicações API. Este tutorial utiliza uma aplicação de exemplo criada utilizando [mola arranque], uma abordagem condicionada por Convenção para começar a trabalhar rapidamente utilizando mola.

**[Kubernetes]**  e  **[Docker]**  são as soluções de open source que ajudam os programadores a automatizar a implementação, dimensionamento e gestão das respetivas aplicações em execução no contentores.

Este tutorial orienta-o Se combinar estas duas tecnologias populares, open source para desenvolver e implementar uma aplicação de arranque de mola ao Microsoft Azure. Mais especificamente, utilize  *[mola arranque]*  para desenvolvimento de aplicações,  *[Kubernetes]*  para a implementação do contentor e o [ Serviço de contentor do Azure (ACS)] para alojar a aplicação.

### <a name="prerequisites"></a>Pré-requisitos

* Uma subscrição do Azure; Se ainda não tiver uma subscrição do Azure, pode ativar o [benefícios de subscritor do MSDN] ou inscrever-se um [conta do Azure gratuita].
* O [Interface de linha de comandos do Azure (CLI)].
* Um atualizados [Java Developer Kit (JDK)].
* Do Apache [Maven] criar ferramenta (versão 3).
* A [Git] cliente.
* A [Docker] cliente.

> [!NOTE]
>
> Devido às necessidades de Virtualização deste tutorial, não é possível seguir os passos neste artigo numa máquina virtual; tem de utilizar um computador físico com funcionalidades de Virtualização ativadas.
>

## <a name="create-the-spring-boot-on-docker-getting-started-web-app"></a>Criar o arranque de mola na aplicação de web de introdução do Docker

Os seguintes passos guiá-lo através da criação de uma aplicação web de arranque de mola e testá-lo localmente.

1. Abra uma linha de comandos e criar um diretório local para manter a sua aplicação e alterar a esse diretório; Por exemplo:
   ```
   md C:\SpringBoot
   cd C:\SpringBoot
   ```
   - ou -
   ```
   md /users/robert/SpringBoot
   cd /users/robert/SpringBoot
   ```

1. Clone o [arranque mola no Docker introdução] projeto de exemplo para o diretório.
   ```
   git clone https://github.com/spring-guides/gs-spring-boot-docker.git
   ```

1. Altere o diretório para o projeto foi concluído.
   ```
   cd gs-spring-boot-docker
   cd complete
   ```

1. Utilize Maven para criar e executar a aplicação de exemplo.
   ```
   mvn package spring-boot:run
   ```

1. Testar a aplicação web, navegando para http://localhost:8080 ou com o seguinte `curl` comando:
   ```
   curl http://localhost:8080
   ```

1. Deverá ver a mensagem seguinte apresentada: **mundo Olá Docker**

   ![Procure a aplicação de exemplo localmente][SB01]

## <a name="create-an-azure-container-registry-using-the-azure-cli"></a>Criar um registo de contentor Azure utilizando a CLI do Azure

1. Abra uma linha de comandos.

1. Inicie sessão sua conta do Azure:
   ```azurecli
   az login
   ```

1. Crie um grupo de recursos para os recursos do Azure utilizados neste tutorial.
   ```azurecli
   az group create --name=wingtiptoys-kubernetes --location=eastus
   ```

1. Crie um registo de contentor do Azure privado no grupo de recursos. O tutorial pushes a aplicação de exemplo como uma imagem de Docker para este registo em passos posteriores. Substitua `wingtiptoysregistry` com um nome exclusivo para o registo.
   ```azurecli
   az acr create --admin-enabled --resource-group wingtiptoys-kubernetes--location eastus \
    --name wingtiptoysregistry --sku Basic
   ```

## <a name="push-your-app-to-the-container-registry"></a>Push a sua aplicação no registo de contentor

1. Navegue para o diretório de configuração para a sua instalação Maven (predefinição ~/.m2/ ou C:\Users\username\.m2) e abra o *settings.xml* ficheiro com um editor de texto.

1. Obter a palavra-passe para o registo de contentor a partir da CLI do Azure.
   ```azurecli
   az acr credential show --name wingtiptoysregistry --query passwords[0]
   ```

   ```json
   {
  "name": "password",
  "value": "AbCdEfGhIjKlMnOpQrStUvWxYz"
   }
   ```

1. Adicionar o seu id de registo de contentor do Azure e a palavra-passe para um novo `<server>` coleção no *settings.xml* ficheiro.
O `id` e `username` são o nome do registo. Utilize o `password` valor do comando anterior (sem aspas).

   ```xml
   <servers>
      <server>
         <id>wingtiptoysregistry</id>
         <username>wingtiptoysregistry</username>
         <password>AbCdEfGhIjKlMnOpQrStUvWxYz</password>
      </server>
   </servers>
   ```

1. Navegue para o diretório de projeto concluída para a sua aplicação de arranque de mola (por exemplo, "*C:\SpringBoot\gs-spring-boot-docker\complete*"ou"*/users/robert/SpringBoot/gs-spring-boot-docker/complete* ") e abra o *pom.xml* ficheiro com um editor de texto.

1. Atualização do `<properties>` coleção no *pom.xml* ficheiro com o valor do servidor de início de sessão para o registo de contentor do Azure.

   ```xml
   <properties>
      <docker.image.prefix>wingtiptoysregistry.azurecr.io</docker.image.prefix>
      <java.version>1.8</java.version>
   </properties>
   ```

1. Atualização do `<plugins>` coleção no *pom.xml* ficheiro para que o `<plugin>` contém o nome de registo e o endereço de servidor de início de sessão para o registo de contentor do Azure.

   ```xml
   <plugin>
      <groupId>com.spotify</groupId>
      <artifactId>docker-maven-plugin</artifactId>
      <version>0.4.11</version>
      <configuration>
         <imageName>${docker.image.prefix}/${project.artifactId}</imageName>
         <dockerDirectory>src/main/docker</dockerDirectory>
         <resources>
            <resource>
               <targetPath>/</targetPath>
               <directory>${project.build.directory}</directory>
               <include>${project.build.finalName}.jar</include>
            </resource>
         </resources>
         <serverId>wingtiptoysregistry</serverId>
         <registryUrl>https://wingtiptoysregistry.azurecr.io</registryUrl>
      </configuration>
   </plugin>
   ```

1. Navegue para o diretório de projeto concluída para a sua aplicação de arranque de mola e execute o seguinte comando para criar o contentor de Docker e enviar a imagem para o registo:

   ```
   mvn package docker:build -DpushImage
   ```

> [!NOTE]
>
>  Poderá receber uma mensagem de erro que é semelhante a um dos seguintes quando Maven pushes a imagem para o Azure:
>
> * `[ERROR] Failed to execute goal com.spotify:docker-maven-plugin:0.4.11:build (default-cli) on project gs-spring-boot-docker: Exception caught: no basic auth credentials`
>
> * `[ERROR] Failed to execute goal com.spotify:docker-maven-plugin:0.4.11:build (default-cli) on project gs-spring-boot-docker: Exception caught: Incomplete Docker registry authorization credentials. Please provide all of username, password, and email or none.`
>
> Se obtiver este erro, inicie sessão no Azure na linha de comandos de Docker.
>
> `docker login -u wingtiptoysregistry -p "AbCdEfGhIjKlMnOpQrStUvWxYz" wingtiptoysregistry.azurecr.io`
>
> Em seguida, envie o contentor:
>
> `docker push wingtiptoysregistry.azurecr.io/gs-spring-boot-docker`

## <a name="create-a-kubernetes-cluster-on-acs-using-the-azure-cli"></a>Criar um Cluster de Kubernetes no ACS utilizando a CLI do Azure

1. Crie um cluster de Kubernetes no serviço de contentor do Azure. O comando seguinte cria um *kubernetes* cluster no *wingtiptoys kubernetes* recursos grupo, com *wingtiptoys containerservice* como o nome do cluster, e *wingtiptoys kubernetes* como o DNS do prefixo:
   ```azurecli
   az acs create --orchestrator-type=kubernetes --resource-group=wingtiptoys-kubernetes \ 
    --name=wingtiptoys-containerservice --dns-prefix=wingtiptoys-kubernetes
   ```
   Este comando pode demorar algum tempo a concluir.

1. Instalar `kubectl` utilizando a CLI do Azure. Os utilizadores do Linux poderão ter de prefixo deste comando com `sudo` , uma vez que implementa o CLI Kubernetes para `/usr/local/bin`.
   ```azurecli
   az acs kubernetes install-cli
   ```

1. Transferir as informações de configuração de cluster para que consiga gerir o cluster a partir da interface de web do Kubernetes e `kubectl`. 
   ```azurecli
   az acs kubernetes get-credentials --resource-group=wingtiptoys-kubernetes  \ 
    --name=wingtiptoys-containerservice
   ```

## <a name="deploy-the-image-to-your-kubernetes-cluster"></a>Implementar a imagem para o cluster Kubernetes

Este tutorial, implementa a aplicação através de `kubectl`, em seguida, permitem-lhe explorar a implementação através da interface de web Kubernetes.

### <a name="deploy-with-the-kubernetes-web-interface"></a>Implementar a interface de web Kubernetes

1. Abra uma linha de comandos.

1. Abra o site de configuração para o seu cluster Kubernetes no seu browser predefinido:
   ```
   az acs kubernetes browse --resource-group=wingtiptoys-kubernetes --name=wingtiptoys-containerservice
   ```

1. Quando o site do configuration Kubernetes abre no browser, clique na ligação para **implementar uma aplicação de**:

   ![Web site do Configuration Kubernetes][KB01]

1. Quando o **implementar uma aplicação de** é apresentada a página, especifique as seguintes opções:

   a. Selecione **especificar detalhes da aplicação abaixo**.

   b. Introduza o nome de aplicação de arranque de mola para o **nome da aplicação**; por exemplo: "*gs-mola arranque docker*".

   c. Introduza o seu início de sessão servidor e um contentor imagem a partir do anteriormente para o **imagem contentor**; por exemplo: "*wingtiptoysregistry.azurecr.io/gs-spring-boot-docker:latest*".

   d. Escolha **externo** para o **serviço**.

   e. Especificar as portas internas e externas a **porta** e **porta de destino** caixas de texto.

   ![Web site do Configuration Kubernetes][KB02]


1. Clique em **implementar** para implementar o contentor.

   ![Implementar o contentor][KB05]

1. Assim que a aplicação foi implementada, verá a aplicação de arranque de mola listada na **serviços**.

   ![Serviços de Kubernetes][KB06]

1. Se clicar na ligação para **pontos finais externos**, pode ver a aplicação de arranque de mola em execução no Azure.

   ![Serviços de Kubernetes][KB07]

   ![Procure a aplicação de exemplo no Azure][SB02]


### <a name="deploy-with-kubectl"></a>Implementar com kubectl

1. Abra uma linha de comandos.

1. Execute o contentor no Kubernetes cluster utilizando o `kubectl run` comando. Dê um nome de serviço para a sua aplicação no Kubernetes e o nome de imagem completa. Por exemplo:
   ```
   kubectl run gs-spring-boot-docker --image=wingtiptoysregistry.azurecr.io/gs-spring-boot-docker:latest
   ```
   Neste comando:

   * O nome do contentor `gs-spring-boot-docker` especificado imediatamente após o `run` comando

   * O `--image` parâmetro especifica o nome de servidor e de imagem de início de sessão combinado como`wingtiptoysregistry.azurecr.io/gs-spring-boot-docker:latest`

1. Expor o seu cluster Kubernetes externamente utilizando o `kubectl expose` comando. Especifique o nome do serviço, a porta TCP destinado ao público utilizado para aceder à aplicação e a porta de destino interno que escuta a sua aplicação. Por exemplo:
   ```
   kubectl expose deployment gs-spring-boot-docker --type=LoadBalancer --port=80 --target-port=8080
   ```
   Neste comando:

   * O nome do contentor `gs-spring-boot-docker` especificado imediatamente após o `expose deployment` comando

   * O `--type` parâmetro especifica que o cluster utiliza o Balanceador de carga

   * O `--port` parâmetro especifica a porta TCP destinado ao público de 80. Aceder a aplicação esta porta.

   * O `--target-port` parâmetro especifica a porta TCP interna 8080. O Balanceador de carga reencaminha os pedidos para a sua aplicação esta porta.

1. Depois da aplicação for implementada para o cluster, o endereço IP externo de consulta e abri-lo no seu browser:

   ```
   kubectl get services -o jsonpath={.items[*].status.loadBalancer.ingress[0].ip} --namespace=${namespace}
   ```

   ![Procure a aplicação de exemplo no Azure][SB02]


## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre como utilizar o arranque de mola no Azure, consulte os artigos seguintes:

* [Implementar uma aplicação de arranque mola para o App Service do Azure](../../app-service/app-service-deploy-spring-boot-web-app-on-azure.md)
* [Implementar uma aplicação de arranque de mola no Linux no serviço de contentor do Azure](container-service-deploy-spring-boot-app-on-linux.md)

Para obter mais informações sobre como utilizar o Azure com o Java, veja o [Centro de Programadores de Java do Azure] e [Java Tools for Visual Studio Team Services] (Ferramentas de Java para o Visual Studio Team Services).

Para obter mais informações sobre o arranque de mola no projeto de exemplo do Docker, consulte [arranque mola no Docker introdução].

As hiperligações seguintes fornecem informações adicionais sobre como criar aplicações de arranque de mola:

* Para obter mais informações sobre como criar uma aplicação de arranque de mola simples, consulte o Initializr mola em https://start.spring.io/.

As hiperligações seguintes fornecem informações adicionais sobre como utilizar Kubernetes com o Azure:

* [Começar com um cluster de Kubernetes no serviço de contentor](https://docs.microsoft.com/azure/container-service/container-service-kubernetes-walkthrough)
* [Utilizando a IU da web de Kubernetes com o serviço de contentor do Azure](https://docs.microsoft.com/azure/container-service/container-service-kubernetes-ui)

Obter mais informações sobre como utilizar a interface de linha de comandos Kubernetes estão disponíveis no **kubectl** Guia do utilizador em <https://kubernetes.io/docs/user-guide/kubectl/>.

O Web site Kubernetes tem vários artigos que abordam a utilização de imagens no privada registos:

* [Configurar serviço de contas para Pods]
* [Espaços de nomes]
* [Extrair uma imagem de um registo privado]

Para obter exemplos adicionais sobre como utilizar imagens personalizadas do Docker com o Azure, consulte [utilizando uma imagem personalizada do Docker para aplicação de Web do Azure no Linux].

<!-- URL List -->

[Interface de linha de comandos do Azure (CLI)]: /cli/azure/overview
[ Serviço de contentor do Azure (ACS)]: https://azure.microsoft.com/services/container-service/
[Centro de Programadores de Java do Azure]: https://azure.microsoft.com/develop/java/
[Azure portal]: https://portal.azure.com/
[Create a private Docker container registry using the Azure portal]: /azure/container-registry/container-registry-get-started-portal
[utilizando uma imagem personalizada do Docker para aplicação de Web do Azure no Linux]: /azure/app-service-web/app-service-linux-using-custom-docker-image
[Docker]: https://www.docker.com/
[conta do Azure gratuita]: https://azure.microsoft.com/pricing/free-trial/
[Git]: https://github.com/
[Java Developer Kit (JDK)]: http://www.oracle.com/technetwork/java/javase/downloads/
[Java Tools for Visual Studio Team Services]: https://java.visualstudio.com/
[Kubernetes]: https://kubernetes.io/
[Kubernetes Command-Line Interface (kubectl)]: https://kubernetes.io/docs/user-guide/kubectl-overview/
[Maven]: http://maven.apache.org/
[benefícios de subscritor do MSDN]: https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/
[mola arranque]: http://projects.spring.io/spring-boot/
[arranque mola no Docker introdução]: https://github.com/spring-guides/gs-spring-boot-docker
[mola Framework]: https://spring.io/
[Configurar serviço de contas para Pods]: https://kubernetes.io/docs/tasks/configure-pod-container/configure-service-account/
[Espaços de nomes]: https://kubernetes.io/docs/concepts/overview/working-with-objects/namespaces/
[Extrair uma imagem de um registo privado]: https://kubernetes.io/docs/tasks/configure-pod-container/pull-image-private-registry/

<!-- IMG List -->

[SB01]: ./media/container-service-deploy-spring-boot-app-on-kubernetes/SB01.png
[SB02]: ./media/container-service-deploy-spring-boot-app-on-kubernetes/SB02.png

[AR01]: ./media/container-service-deploy-spring-boot-app-on-kubernetes/AR01.png
[AR02]: ./media/container-service-deploy-spring-boot-app-on-kubernetes/AR02.png
[AR03]: ./media/container-service-deploy-spring-boot-app-on-kubernetes/AR03.png
[AR04]: ./media/container-service-deploy-spring-boot-app-on-kubernetes/AR04.png

[KB01]: ./media/container-service-deploy-spring-boot-app-on-kubernetes/KB01.png
[KB02]: ./media/container-service-deploy-spring-boot-app-on-kubernetes/KB02.png
[KB03]: ./media/container-service-deploy-spring-boot-app-on-kubernetes/KB03.png
[KB04]: ./media/container-service-deploy-spring-boot-app-on-kubernetes/KB04.png
[KB05]: ./media/container-service-deploy-spring-boot-app-on-kubernetes/KB05.png
[KB06]: ./media/container-service-deploy-spring-boot-app-on-kubernetes/KB06.png
[KB07]: ./media/container-service-deploy-spring-boot-app-on-kubernetes/KB07.png
