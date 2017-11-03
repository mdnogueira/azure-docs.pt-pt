---
title: "Como utilizar o plug-in do Maven para Web Apps do Azure para implementar uma aplicação de arranque de mola no registo de contentor do Azure App Service do Azure"
description: "Este tutorial irá guiá-lo embora os passos para implementar uma aplicação de arranque de mola no registo de contentor do Azure para o serviço de aplicações do Azure para o Azure, utilizando um plug-in do Maven."
services: 
documentationcenter: java
author: rmcmurray
manager: cfowler
editor: 
ms.assetid: 
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: multiple
ms.devlang: Java
ms.topic: article
ms.date: 08/07/2017
ms.author: robmcm;kevinzha
ms.openlocfilehash: b087003b3a1e236e4a306678904107b8bf99395e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-use-the-maven-plugin-for-azure-web-apps-to-deploy-a-spring-boot-app-in-azure-container-registry-to-azure-app-service"></a>Como utilizar o plug-in do Maven para Web Apps do Azure para implementar uma aplicação de arranque de mola no registo de contentor do Azure App Service do Azure

O  **[mola Framework]**  é uma arquitetura de open source popular, que ajuda os programadores de Java criar web, móveis e aplicações API. Este tutorial utiliza uma aplicação de exemplo criada utilizando [mola arranque], uma abordagem condicionada por Convenção para começar a trabalhar rapidamente utilizando mola.

Este artigo demonstra como implementar um exemplo de aplicação de arranque de mola no registo de contentor do Azure e, em seguida, utilizar o plug-in do Maven para Web Apps do Azure para implementar a aplicação no App Service do Azure.

> [!NOTE]
>
> O plug-in do Maven para Web Apps do Azure está atualmente disponível como uma pré-visualização. Por agora, a publicação de FTP só é suportada, embora as funcionalidades adicionais que estejam planeadas para o futuro.
>

## <a name="prerequisites"></a>Pré-requisitos

Para concluir os passos neste tutorial, tem de ter os seguintes pré-requisitos:

* Uma subscrição do Azure; Se ainda não tiver uma subscrição do Azure, pode ativar o [benefícios de subscritor do MSDN] ou inscrever-se um [conta do Azure gratuita].
* O [Interface de linha de comandos do Azure (CLI)].
* Um atualizados [Java Development Kit (JDK)], versão 1.7 ou posterior.
* Do Apache [Maven] criar ferramenta (versão 3).
* A [Git] cliente.
* A [Docker] cliente.

> [!NOTE]
>
> Devido às necessidades de Virtualização deste tutorial, não é possível seguir os passos neste artigo numa máquina virtual; tem de utilizar um computador físico com funcionalidades de Virtualização ativadas.
>

## <a name="clone-the-sample-spring-boot-on-docker-web-app"></a>Clonar o arranque de mola de exemplo na aplicação web de Docker

Nesta secção, clonar uma de aplicação de arranque de mola e testá-lo localmente.

1. Abra uma linha de comandos ou janela de terminal e criar um diretório local para manter a sua aplicação de arranque de mola e alterar a esse diretório; Por exemplo:
   ```shell
   md C:\SpringBoot
   cd C:\SpringBoot
   ```
   - ou -
   ```shell
   md /users/robert/SpringBoot
   cd /users/robert/SpringBoot
   ```

1. Clone o [arranque mola no Docker introdução] projeto de exemplo para o diretório que criou; por exemplo:
   ```shell
   git clone -b private-registry https://github.com/Microsoft/gs-spring-boot-docker
   ```

1. Altere o diretório para o projeto concluído; Por exemplo:
   ```shell
   cd gs-spring-boot-docker/complete
   ```

1. Criar o ficheiro JAR com o Maven; Por exemplo:
   ```shell
   mvn clean package
   ```

1. Quando a aplicação web tiver sido criada, inicie a aplicação web com o Maven; Por exemplo:
   ```shell
   mvn spring-boot:run
   ```

1. Teste a aplicação web, navegando até-la localmente utilizando um browser. Por exemplo, pode utilizar o seguinte comando, se tiver o curl disponível:
   ```shell
   curl http://localhost:8080
   ```

1. Deverá ver a mensagem seguinte apresentada: **mundo Olá Docker**

   ![Procure a aplicação de exemplo localmente][SB01]

## <a name="create-an-azure-service-principal"></a>Criar um Azure principal de serviço

Nesta secção, vai criar um Azure principal de serviço que o plug-in do Maven utiliza quando implementar o contentor do Azure.

1. Abra uma linha de comandos.

1. Inicie sessão na sua conta do Azure utilizando a CLI do Azure:
   ```azurecli
   az login
   ```
   Siga as instruções para concluir o processo de início de sessão.

1. Crie um Azure principal de serviço:
   ```azurecli
   az ad sp create-for-rbac --name "uuuuuuuu" --password "pppppppp"
   ```
   Onde `uuuuuuuu` é o nome de utilizador e `pppppppp` é a palavra-passe para o principal de serviço.

1. Azure responde com JSON que se assemelha ao seguinte exemplo:
   ```json
   {
      "appId": "aaaaaaaa-aaaa-aaaa-aaaa-aaaaaaaaaaaa",
      "displayName": "uuuuuuuu",
      "name": "http://uuuuuuuu",
      "password": "pppppppp",
      "tenant": "tttttttt-tttt-tttt-tttt-tttttttttttt"
   }
   ```

   > [!NOTE]
   >
   > Irá utilizar os valores desta resposta JSON quando configurar o plug-in do Maven para implementar o contentor do Azure. O `aaaaaaaa`, `uuuuuuuu`, `pppppppp`, e `tttttttt` são valores de marcador de posição, o que são utilizados neste exemplo, para facilitar a mapear estes valores para os respetivos elementos quando configura o Maven `settings.xml` ficheiros na secção seguinte .
   >
   >

## <a name="create-an-azure-container-registry-using-the-azure-cli"></a>Criar um registo de contentor Azure utilizando a CLI do Azure

1. Abra uma linha de comandos.

1. Inicie sessão sua conta do Azure:
   ```azurecli
   az login
   ```

1. Crie um grupo de recursos para os recursos do Azure que irá utilizar este artigo:
   ```azurecli
   az group create --name=wingtiptoysresources --location=westus
   ```
   Substitua `wingtiptoysresources` neste exemplo, com um nome exclusivo para o grupo de recursos.

1. Crie um registo de contentor do Azure privado no grupo de recursos para a sua aplicação de arranque de mola: 
   ```azurecli
   az acr create --admin-enabled --resource-group wingtiptoysresources --location westus --name wingtiptoysregistry --sku Basic
   ```
   Substitua `wingtiptoysregistry` neste exemplo, com um nome exclusivo para o registo de contentor.

1. Obter a palavra-passe para o registo do contentor:
   ```azurecli
   az acr credential show --name wingtiptoysregistry --query passwords[0]
   ```
   Azure irá responder com a palavra-passe; Por exemplo:
   ```json
   {
      "name": "password",
      "value": "xxxxxxxxxx"
   }
   ```

## <a name="add-your-azure-container-registry-and-azure-service-principal-to-your-maven-settings"></a>Adicionar o registo de contentor do Azure e o principal de serviço do Azure para as definições de Maven

1. Abra o Maven `settings.xml` ficheiro num editor de texto; este ficheiro pode ser um caminho como os exemplos seguintes:
   * `/etc/maven/settings.xml`
   * `%ProgramFiles%\apache-maven\3.5.0\conf\settings.xml`
   * `$HOME/.m2/settings.xml`

1. Adicionar as definições de acesso do registo de contentor do Azure da secção anterior deste artigo para o `<servers>` coleção no *settings.xml* ficheiro; por exemplo:

   ```xml
   <servers>
      <server>
         <id>wingtiptoysregistry</id>
         <username>wingtiptoysregistry</username>
         <password>xxxxxxxxxx</password>
      </server>
   </servers>
   ```
   Em que:
   Elemento | Descrição
   ---|---|---
   `<id>` | Contém o nome do seu registo de contentor do Azure privado.
   `<username>` | Contém o nome do seu registo de contentor do Azure privado.
   `<password>` | Contém a palavra-passe que obteve na secção anterior deste artigo.

1. Adicionar as definições de principal de serviço do Azure a partir de uma secção anterior deste artigo para o `<servers>` coleção no *settings.xml* ficheiro; por exemplo:

   ```xml
   <servers>
      <server>
        <id>azure-auth</id>
         <configuration>
            <client>aaaaaaaa-aaaa-aaaa-aaaa-aaaaaaaaaaaa</client>
            <tenant>tttttttt-tttt-tttt-tttt-tttttttttttt</tenant>
            <key>pppppppp</key>
            <environment>AZURE</environment>
         </configuration>
      </server>
   </servers>
   ```
   Em que:
   Elemento | Descrição
   ---|---|---
   `<id>` | Especifica um nome exclusivo que utiliza o Maven para procurar as definições de segurança quando implementar a aplicação web no Azure.
   `<client>` | Contém o `appId` valor do seu principal de serviço.
   `<tenant>` | Contém o `tenant` valor do seu principal de serviço.
   `<key>` | Contém o `password` valor do seu principal de serviço.
   `<environment>` | Define o ambiente de nuvem do Azure de destino, o que é `AZURE` neste exemplo. (Uma lista completa dos ambientes está disponível no [Plug-in do Maven para Web Apps do Azure] documentação)

1. Guarde e feche o *settings.xml* ficheiro.

## <a name="build-your-docker-container-image-and-push-it-to-your-azure-container-registry"></a>Compilar o Docker imagem do contentor e enviá-lo para o registo de contentor do Azure

1. Navegue para o diretório de projeto concluída para a sua aplicação de arranque de mola (por exemplo "*C:\SpringBoot\gs-spring-boot-docker\complete*"ou"*/users/robert/SpringBoot/gs-spring-boot-docker/complete*") e abra o *pom.xml* ficheiro com um texto Editor.

1. Atualização do `<properties>` coleção no *pom.xml* ficheiro com o valor do servidor de início de sessão para o registo de contentor do Azure da secção anterior deste tutorial; por exemplo:

   ```xml
   <properties>
      <azure.containerRegistry>wingtiptoysregistry</azure.containerRegistry>
      <docker.image.prefix>${azure.containerRegistry}.azurecr.io</docker.image.prefix>
      <java.version>1.8</java.version>
      <maven.build.timestamp.format>yyyyMMddHHmmssSSS</maven.build.timestamp.format>
   </properties>
   ```
   Em que:
   Elemento | Descrição
   ---|---|---
   `<azure.containerRegistry>` | Especifica o nome do seu registo de contentor do Azure privado.
   `<docker.image.prefix>` | Especifica o URL do seu registo de contentor do Azure privado, que é derivado acrescentando ". azurecr.io" com o nome do seu registo de contentor privada.

1. Certifique-se de que `<plugin>` para o plug-in do Docker no seu *pom.xml* ficheiro contém as propriedades corretas para o início de sessão e registo de endereço nome do servidor do passo anterior neste tutorial. Por exemplo:

   ```xml
   <plugin>
      <groupId>com.spotify</groupId>
      <artifactId>docker-maven-plugin</artifactId>
      <version>0.4.11</version>
      <configuration>
         <imageName>${docker.image.prefix}/${project.artifactId}</imageName>
         <registryUrl>https://${docker.image.prefix}</registryUrl>
         <serverId>${azure.containerRegistry}</serverId>
         <dockerDirectory>src/main/docker</dockerDirectory>
         <resources>
            <resource>
               <targetPath>/</targetPath>
               <directory>${project.build.directory}</directory>
               <include>${project.build.finalName}.jar</include>
            </resource>
         </resources>
      </configuration>
   </plugin>
   ```
   Em que:
   Elemento | Descrição
   ---|---|---
   `<serverId>` | Especifica a propriedade que contém o nome do seu registo de contentor do Azure privado.
   `<registryUrl>` | Especifica a propriedade que contém o URL do seu registo de contentor do Azure privado.

1. Navegue para o diretório de projeto concluída para a sua aplicação de arranque de mola e execute o seguinte comando para reconstruir a aplicação e emitir o contentor para o seu registo de contentor do Azure:

   ```
   mvn package docker:build -DpushImage 
   ```

1. OPCIONAL: Procure o [portal do Azure] e verifique se existe com o nome de imagem do contentor de Docker **gs-mola arranque docker** no seu registo de contentor.

   ![Certifique-se contentor no portal do Azure][CR01]

## <a name="customize-your-pomxml-then-build-and-deploy-your-container-to-azure"></a>Personalizar a sua pom.xml, em seguida, criar e implementar o contentor do Azure

Abra o `pom.xml` de ficheiros para a sua aplicação de arranque de mola num editor de texto e, em seguida, localize o `<plugin>` elemento para `azure-webapp-maven-plugin`. Este elemento deve assemelhar-se ao seguinte exemplo:

   ```xml
   <plugin>
      <groupId>com.microsoft.azure</groupId>
      <artifactId>azure-webapp-maven-plugin</artifactId>
      <version>0.1.3</version>
      <configuration>
         <authentication>
            <serverId>azure-auth</serverId>
         </authentication>
         <resourceGroup>wingtiptoysresources</resourceGroup>
         <appName>maven-linux-app-${maven.build.timestamp}</appName>
         <region>westus</region>
         <containerSettings>
            <imageName>${docker.image.prefix}/${project.artifactId}</imageName>
            <registryUrl>https://${docker.image.prefix}</registryUrl>
            <serverId>${azure.containerRegistry}</serverId>
         </containerSettings>
         <appSettings>
            <property>
               <name>PORT</name>
               <value>8080</value>
            </property>
         </appSettings>
      </configuration>
   </plugin>
   ```

Existem vários valores que pode modificar para o plug-in do Maven e uma descrição detalhada para cada um destes elementos está disponível no [Plug-in do Maven para Web Apps do Azure] documentação. Que a ser consiga aceder tal, existem vários valores, realçando neste artigo:

Elemento | Descrição
---|---|---
`<version>` | Especifica a versão do [Plug-in do Maven para Web Apps do Azure]. Deve verificar a versão indicada no [Respository Maven Central](http://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-webapp-maven-plugin%22) para se certificar de que está a utilizar a versão mais recente.
`<authentication>` | Especifica as informações de autenticação do Azure, que, neste exemplo, contém um `<serverId>` elemento que contenha `azure-auth`; Maven utiliza esse valor para procurar o principal de serviço do Azure valores no seu Maven *settings.xml* ficheiro, que é definida uma secção anterior deste artigo.
`<resourceGroup>` | Especifica o grupo de recursos de destino, o que é `wingtiptoysresources` neste exemplo. O grupo de recursos será criado durante a implementação, se já existir.
`<appName>` | Especifica o nome de destino para a sua aplicação web. Neste exemplo, o nome de destino é `maven-linux-app-${maven.build.timestamp}`, onde o `${maven.build.timestamp}` é acrescentado um sufixo neste exemplo, para evitar conflitos. (O timestamp é opcional; pode especificar qualquer cadeia exclusiva para o nome da aplicação).
`<region>` | Especifica a região de destino, que, neste exemplo, é `westus`. (Uma lista completa está no [Plug-in do Maven para Web Apps do Azure] documentação.)
`<containerSettings>` | Especifica as propriedades que contêm o nome e o URL do seu contentor.
`<appSettings>` | Especifica as definições de exclusivas para o Maven utilizar quando implementar a aplicação web no Azure. Neste exemplo, um `<property>` elemento contém um par nome/valor de elementos subordinados que especifique a porta para a sua aplicação.

> [!NOTE]
>
> As definições para alterar o número da porta neste exemplo, apenas são necessárias quando está a alterar a porta da predefinição.
>

1. Na linha de comandos ou janela de terminal que estava a utilizar anteriormente, reconstrua o ficheiro JAR com o Maven se tiver efetuado alterações para o *pom.xml* ficheiro; por exemplo:
   ```shell
   mvn clean package
   ```

1. Implementar a aplicação web no Azure utilizando o Maven; Por exemplo:
   ```shell
   mvn azure-webapp:deploy
   ```

Maven irá implementar a aplicação web no Azure; Se a aplicação web não já existir, será criado.

> [!NOTE]
>
> Se a região que especificou no `<region>` elemento do seu *pom.xml* ficheiro não tem suficiente servidores disponíveis quando iniciar a implementação, poderá ver um erro semelhante ao seguinte exemplo:
>
> ```
> [INFO] Start deploying to Web App maven-linux-app-20170804...
> [INFO] ------------------------------------------------------------------------
> [INFO] BUILD FAILURE
> [INFO] ------------------------------------------------------------------------
> [INFO] Total time: 31.059 s
> [INFO] Finished at: 2017-08-04T12:15:47-07:00
> [INFO] Final Memory: 51M/279M
> [INFO] ------------------------------------------------------------------------
> [ERROR] Failed to execute goal com.microsoft.azure:azure-webapp-maven-plugin:0.1.3:deploy (default-cli) on project gs-spring-boot-docker: null: MojoExecutionException: CloudException: OnError while emitting onNext value: retrofit2.Response.class
> ```
>
> Se isto acontecer, pode especificar noutra região e volte a executar o comando Maven para implementar a sua aplicação.
>
>

Quando a web tiver sido implementada, será possível geri-lo utilizando o [portal do Azure].

* A aplicação web será listada no **serviços aplicacionais**:

   ![Aplicação Web listada no portal do Azure serviços aplicacionais][AP01]

* E o URL para a sua aplicação web será listado no **descrição geral** para a sua aplicação web:

   ![Determinar o URL para a sua aplicação web][AP02]

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre as várias tecnologias abordadas neste artigo, consulte os artigos seguintes:

* [Plug-in do Maven para Web Apps do Azure]

* [Inicie sessão no Azure a partir da CLI do Azure](/azure/xplat-cli-connect)

* [Criar um Azure principal de serviço com o Azure CLI 2.0](/cli/azure/create-an-azure-service-principal-azure-cli)

* [Referência de definições do maven](https://maven.apache.org/settings.html)

* [Plug-in do docker para Maven]

<!-- URL List -->

[Interface de linha de comandos do Azure (CLI)]: /cli/azure/overview
[Azure Container Service (ACS)]: https://azure.microsoft.com/services/container-service/
[Azure Java Developer Center]: https://azure.microsoft.com/develop/java/
[portal do Azure]: https://portal.azure.com/
[Plug-in do Maven para Web Apps do Azure]: https://github.com/Microsoft/azure-maven-plugins/tree/master/azure-webapp-maven-plugin
[Create a private Docker container registry using the Azure portal]: /azure/container-registry/container-registry-get-started-portal
[Using a custom Docker image for Azure Web App on Linux]: tutorial-custom-docker-image.md
[Docker]: https://www.docker.com/
[Plug-in do docker para Maven]: https://github.com/spotify/docker-maven-plugin
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

[SB01]: ./media/app-service-web-deploy-spring-boot-app-from-container-registry-using-maven-plugin/SB01.png
[CR01]: ./media/app-service-web-deploy-spring-boot-app-from-container-registry-using-maven-plugin/CR01.png
[AP01]: ./media/app-service-web-deploy-spring-boot-app-from-container-registry-using-maven-plugin/AP01.png
[AP02]: ./media/app-service-web-deploy-spring-boot-app-from-container-registry-using-maven-plugin/AP02.png
