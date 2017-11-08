---
title: "Implementar uma aplicação de Web de arranque mola no Linux no serviço de contentor do Azure | Microsoft Docs"
description: "Este tutorial orienta-o embora os passos para implementar uma aplicação de arranque de mola como uma aplicação web do Linux no Microsoft Azure."
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
ms.openlocfilehash: fd071dc8ce568602db3d96d4bcaa5f421bd16513
ms.sourcegitcommit: 0930aabc3ede63240f60c2c61baa88ac6576c508
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/07/2017
---
# <a name="deploy-a-spring-boot-application-on-linux-in-the-azure-container-service"></a>Implementar uma aplicação de arranque de mola no Linux no serviço de contentor do Azure

[!INCLUDE [aks-preview-redirect.md](../../../includes/aks-preview-redirect.md)]

O  **[mola Framework]**  é uma solução de open source que ajuda os programadores de Java criar aplicações de nível empresarial. Um dos projetos mais popular do que é criada em cima de que a plataforma é [mola arranque], que fornece uma abordagem simplificada para criar aplicações de Java autónomas.

**[Docker]**  é soluções de open source que ajuda os programadores de automatizar a implementação, dimensionamento e gestão das respetivas aplicações que estão em execução nos contentores.

Este tutorial explica-lhe utilizar o Docker para desenvolver e implementar uma aplicação de arranque de mola num anfitrião Linux no [serviço de contentor do Azure (ACS)].

## <a name="prerequisites"></a>Pré-requisitos

Para concluir os passos neste tutorial, tem de ter os seguintes pré-requisitos:

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

Os seguintes passos guiá-lo pelos passos necessários para criar uma aplicação de web de arranque de mola simples e testá-lo localmente.

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

1. Clone o [arranque mola no Docker introdução] projeto de exemplo para o diretório que criou; por exemplo:
   ```
   git clone https://github.com/spring-guides/gs-spring-boot-docker.git
   ```

1. Altere o diretório para o projeto concluído; Por exemplo:
   ```
   cd gs-spring-boot-docker/complete
   ```

1. Criar o ficheiro JAR com o Maven; Por exemplo:
   ```
   mvn package
   ```

1. Quando a aplicação web tiver sido criada, altere o diretório para o `target` diretório onde o ficheiro JAR encontra-se e iniciar a aplicação web; por exemplo:
   ```
   cd target
   java -jar gs-spring-boot-docker-0.1.0.jar
   ```

1. Teste a aplicação web, navegando até-la localmente utilizando um browser. Por exemplo, se tiver curl disponível e configurado o servidor Tomcat para ser executado na porta 80:
   ```
   curl http://localhost
   ```

1. Deverá ver a mensagem seguinte apresentada: **mundo Olá Docker!**

   ![Procure a aplicação de exemplo localmente][SB01]

## <a name="create-an-azure-container-registry-to-use-as-a-private-docker-registry"></a>Criar um registo de contentor do Azure para utilizar como um registo de Docker privada

Os seguintes passos guiá-lo no portal do Azure para criar um registo de contentor do Azure.

> [!NOTE]
>
> Se pretender utilizar a CLI do Azure em vez do portal do Azure, siga os passos no [criar um registo de contentor de Docker privado utilizando o 2.0 CLI do Azure](../../container-registry/container-registry-get-started-azure-cli.md).
>

1. Navegue para o [portal do Azure] e iniciar sessão.

   Assim que tem sessão iniciada para a sua conta no portal do Azure, pode seguir os passos a [criar um registo de contentor de Docker privado utilizando o portal do Azure] artigo, o que são paraphrased nos passos seguintes para sake de expediency.

1. Clique no ícone de menu para **+ novo**, em seguida, clique em **contentores**e, em seguida, clique em **registo de contentor do Azure**.
   
   ![Criar um novo registo de contentor do Azure][AR01]

1. Quando a página de informações para o modelo de registo de contentor do Azure é apresentado, clique em **criar**. 

   ![Criar um novo registo de contentor do Azure][AR02]

1. Quando o **criar registo de contentor** é apresentada a página, introduza o **nome do registo** e **grupo de recursos**, escolha **ativar** para o **Utilizador de Admin**e, em seguida, clique em **criar**.

   ![Configurar definições de registo de contentor do Azure][AR03]

1. Quando o registo de contentor tiver sido criado, navegue para o registo de contentor no portal do Azure e, em seguida, clique em **chaves de acesso**. Tome nota do nome de utilizador e palavra-passe para os passos seguintes.

   ![Chaves de acesso de registo de contentor do Azure][AR04]

## <a name="configure-maven-to-use-your-azure-container-registry-access-keys"></a>Configurar o Maven para utilizar as chaves de acesso do registo de contentor do Azure

1. Navegue para o diretório de configuração para a sua instalação Maven e abra o *settings.xml* ficheiro com um editor de texto.

1. Adicionar as definições de acesso do registo de contentor do Azure da secção anterior deste tutorial para o `<servers>` coleção no *settings.xml* ficheiro; por exemplo:

   ```xml
   <settings>
       <pluginGroups>
           <pluginGroup>com.spotify</pluginGroup>
       </pluginGroups>
   </settings>
   <servers>
      <server>
         <id>wingtiptoysregistry</id>
         <username>wingtiptoysregistry</username>
         <password>AbCdEfGhIjKlMnOpQrStUvWxYz</password>
         <configuration>
            <email>foo@foo.bar</email>
         </configuration>
      </server>
   </servers>
   ```

1. Navegue para o diretório de projeto concluída para a sua aplicação de arranque de mola (por exemplo: "*C:\SpringBoot\gs-spring-boot-docker\complete*"ou"*/users/robert/SpringBoot/gs-spring-boot-docker/complete* ") e abra o *pom.xml* ficheiro com um editor de texto.

1. Atualização do `<properties>` coleção no *pom.xml* ficheiro com o valor do servidor de início de sessão para o registo de contentor do Azure da secção anterior deste tutorial; por exemplo:

   ```xml
   <properties>
      <docker.image.prefix>wingtiptoysregistry.azurecr.io</docker.image.prefix>
      <java.version>1.8</java.version>
   </properties>
   ```

1. Atualização do `<plugins>` coleção no *pom.xml* ficheiro para que o `<plugin>` contém o nome de registo e o endereço de servidor de início de sessão para o registo de contentor do Azure da secção anterior deste tutorial. Por exemplo:

   ```xml
   <plugin>
      <groupId>com.spotify</groupId>
      <artifactId>docker-maven-plugin</artifactId>
      <version>0.4.11</version>
      <configuration>
         <imageName>${docker.image.prefix}/${project.artifactId}</imageName>
         <dockerDirectory>${project.basedir}</dockerDirectory>
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

1. Navegue para o diretório de projeto concluída para a sua aplicação de arranque de mola e execute o seguinte comando para reconstruir a aplicação e emitir o contentor para o seu registo de contentor do Azure:

   ```
   mvn package docker:build -DpushImage 
   ```

> [!NOTE]
>
> Quando estiver a enviar o contentor do Docker para o Azure, poderá receber uma mensagem de erro que é semelhante a um dos seguintes, mesmo apesar do contentor do Docker foi criado com êxito:
>
> * `[ERROR] Failed to execute goal com.spotify:docker-maven-plugin:0.4.11:build (default-cli) on project gs-spring-boot-docker: Exception caught: no basic auth credentials`
>
> * `[ERROR] Failed to execute goal com.spotify:docker-maven-plugin:0.4.11:build (default-cli) on project gs-spring-boot-docker: Exception caught: Incomplete Docker registry authorization credentials. Please provide all of username, password, and email or none.`
>
> Se isto acontecer, poderá ter de iniciar sessão na sua conta do Azure a partir da linha de comandos do Docker; Por exemplo:
>
> `docker login -u wingtiptoysregistry -p "AbCdEfGhIjKlMnOpQrStUvWxYz" wingtiptoysregistry.azurecr.io`
>
> Em seguida, pode emitir o seu contentor na linha de comandos; Por exemplo:
>
> `docker push wingtiptoysregistry.azurecr.io/gs-spring-boot-docker`
>

## <a name="create-a-web-app-on-linux-on-azure-app-service-using-your-container-image"></a>Criar uma aplicação web no Linux no Azure App Service utilizando a imagem de contentor

1. Navegue para o [portal do Azure] e iniciar sessão.

1. Clique no ícone de menu para **+ novo**, em seguida, clique em **Web + móvel**e, em seguida, clique em **aplicação Web no Linux**.
   
   ![Criar uma nova aplicação web no portal do Azure][LX01]

1. Quando o **aplicação Web no Linux** é apresentada a página, introduza as seguintes informações:

   a. Introduza um nome exclusivo para o **nome da aplicação**; por exemplo: "*wingtiptoyslinux*."

   b. Escolha o **subscrição** na lista pendente.

   c. Selecione um existente **grupo de recursos**, ou especifique um nome para criar um novo grupo de recursos.

   d. Clique em **configurar contentor** e introduza as seguintes informações:

      * Escolha **registo privado**.

      * **Imagem e etiqueta opcional**: Especifique o nome do contentor do anteriores; por exemplo: "*wingtiptoysregistry.azurecr.io/gs-spring-boot-docker:latest*"

      * **URL do servidor**: Especifique o URL de registo de versões anteriores; por exemplo: "*https://wingtiptoysregistry.azurecr.io*"

      * **Nome de utilizador de início de sessão** e **palavra-passe**: especificar as credenciais de início de sessão da sua **chaves de acesso** que utilizou nos passos anteriores.
   
   e. Assim que tiver introduzido todas as informações acima, clique em **OK**.

   ![Configurar definições da aplicação web][LX02]

1. Clique em **Criar**.

> [!NOTE]
>
> Azure irá mapear automaticamente os pedidos de Internet para embedded servidor Tomcat que está a executar as portas padrão de 80 ou 8080. No entanto, se tiver configurado o servidor Tomcat incorporado com uma porta personalizada, terá de adicionar uma variável de ambiente para a sua aplicação web que define a porta para o servidor Tomcat incorporado. Para fazê-lo, siga os seguintes passos:
>
> 1. Navegue para o [portal do Azure] e iniciar sessão.
> 
> 2. Clique no ícone **serviços aplicacionais**. (Consulte item #1 na imagem abaixo).
>
> 3. Selecione a aplicação Web na lista. (O item de #2 na imagem abaixo.)
>
> 4. Clique em **definições da aplicação**. (Item #3 na imagem abaixo.)
>
> 5. No **as definições de aplicação** secção, adicione uma nova variável de ambiente com o nome **porta** e introduza o seu número de porta personalizado para o valor. (Item #4 na imagem abaixo.)
>
> 6. Clique em **Guardar**. (Item #5 na imagem abaixo.)
>
> ![Guardar um número de porta personalizado no portal do Azure][LX03]
>

<!--
##  OPTIONAL: Configure the embedded Tomcat server to run on a different port

The embedded Tomcat server in the sample Spring Boot application is configured to run on port 8080 by default. However, if you want to run the embedded Tomcat server to run on a different port, such as port 80 for local testing, you can configure the port by using the following steps.

1. Go to the *resources* directory (or create the directory if it does not exist); for example:
   ```shell
   cd src/main/resources
   ```

1. Open the *application.yml* file in a text editor if it exists, or create a new YAML file if it does not exist.

1. Modify the **server** setting so that the server runs on port 80; for example:
   ```yaml
   server:
      port: 80
   ```

1. Save and close the *application.yml* file.
-->

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre como utilizar aplicações de arranque de mola no Azure, consulte os artigos seguintes:

* [Implementar uma aplicação de arranque mola para o App Service do Azure](../../app-service/app-service-deploy-spring-boot-web-app-on-azure.md)
* [Implementar uma aplicação de arranque mola num Cluster Kubernetes no serviço de contentor do Azure](container-service-deploy-spring-boot-app-on-kubernetes.md)

Para obter mais informações sobre como utilizar o Azure com o Java, veja o [Centro de Programadores de Java do Azure] e [Java Tools for Visual Studio Team Services] (Ferramentas de Java para o Visual Studio Team Services).

Para obter mais detalhes sobre o arranque de mola no projeto de exemplo do Docker, consulte [arranque mola no Docker introdução].

Para obter ajuda com a introdução com as suas próprias aplicações de mola arranque, consulte o **mola Initializr** em https://start.spring.io/.

Para obter mais informações sobre como começar a criar uma aplicação de arranque de mola simples, consulte o Initializr mola em https://start.spring.io/.

Para obter exemplos adicionais sobre como utilizar imagens personalizadas do Docker com o Azure, consulte [utilizando uma imagem personalizada do Docker para aplicação de Web do Azure no Linux].

<!-- URL List -->

[Interface de linha de comandos do Azure (CLI)]: /cli/azure/overview
[serviço de contentor do Azure (ACS)]: https://azure.microsoft.com/services/container-service/
[Centro de Programadores de Java do Azure]: https://azure.microsoft.com/develop/java/
[portal do Azure]: https://portal.azure.com/
[criar um registo de contentor de Docker privado utilizando o portal do Azure]: /azure/container-registry/container-registry-get-started-portal
[utilizando uma imagem personalizada do Docker para aplicação de Web do Azure no Linux]: /azure/app-service-web/app-service-linux-using-custom-docker-image
[Docker]: https://www.docker.com/
[conta do Azure gratuita]: https://azure.microsoft.com/pricing/free-trial/
[Git]: https://github.com/
[Java Developer Kit (JDK)]: http://www.oracle.com/technetwork/java/javase/downloads/
[Java Tools for Visual Studio Team Services]: https://java.visualstudio.com/
[Maven]: http://maven.apache.org/
[benefícios de subscritor do MSDN]: https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/
[mola arranque]: http://projects.spring.io/spring-boot/
[arranque mola no Docker introdução]: https://github.com/spring-guides/gs-spring-boot-docker
[mola Framework]: https://spring.io/

<!-- IMG List -->

[SB01]: ./media/container-service-deploy-spring-boot-app-on-linux/SB01.png
[SB02]: ./media/container-service-deploy-spring-boot-app-on-linux/SB02.png

[AR01]: ./media/container-service-deploy-spring-boot-app-on-linux/AR01.png
[AR02]: ./media/container-service-deploy-spring-boot-app-on-linux/AR02.png
[AR03]: ./media/container-service-deploy-spring-boot-app-on-linux/AR03.png
[AR04]: ./media/container-service-deploy-spring-boot-app-on-linux/AR04.png

[LX01]: ./media/container-service-deploy-spring-boot-app-on-linux/LX01.png
[LX02]: ./media/container-service-deploy-spring-boot-app-on-linux/LX02.png
[LX03]: ./media/container-service-deploy-spring-boot-app-on-linux/LX03.png
