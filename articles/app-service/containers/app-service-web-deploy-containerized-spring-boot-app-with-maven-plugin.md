---
title: "Como utilizar o plug-in do Maven para a aplicação Web para contentores para implementar uma aplicação de arranque de mola de para o Azure"
description: "Saiba como utilizar o plug-in do Maven para a aplicação Web para contentores para implementar uma aplicação de arranque de mola no Azure."
services: app-service\web
documentationcenter: java
author: rmcmurray
manager: cfowler
editor: 
ms.assetid: 
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: java
ms.topic: article
ms.date: 08/07/2017
ms.author: robmcm;kevinzha
ms.openlocfilehash: 0329aa9b88c7542ab3235a104a0652cd217ff872
ms.sourcegitcommit: b979d446ccbe0224109f71b3948d6235eb04a967
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/25/2017
---
# <a name="how-to-use-the-maven-plugin-for-web-app-for-containers-to-deploy-a-containerized-spring-boot-app-to-azure"></a>Como utilizar o plug-in do Maven para a aplicação Web para contentores para implementar uma aplicação de arranque de mola de para o Azure

O [Plug-in do Maven para a aplicação Web para contentores](https://github.com/Microsoft/azure-maven-plugins/tree/master/azure-webapp-maven-plugin) para [Apache Maven](http://maven.apache.org/) fornece uma integração perfeita do App Service do Azure em projetos Maven e simplifica o processo para programadores implementar as aplicações web App Service do Azure.

Este artigo demonstra utilizando o plug-in do Maven para a aplicação Web para contentores para implementar um exemplo de aplicação de arranque de mola num contentor Docker à aplicação Web para contentores.

> [!NOTE]
>
> O plug-in do Maven para a aplicação Web para contentores está atualmente disponível como uma pré-visualização. Por agora, a publicação de FTP só é suportada, embora as funcionalidades adicionais que estejam planeadas para o futuro.
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

## <a name="clone-the-sample-spring-boot-application"></a>Clonar o exemplo de aplicação de arranque da mola

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

1. Clonar o projeto de exemplo [mola arranque com o Docker] para o diretório que criou; Por exemplo:
   ```shell
   git clone https://github.com/microsoft/gs-spring-boot-docker
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

## <a name="create-an-azure-service-principal"></a>Criar um Azure principal de serviço

Nesta secção, vai criar um Azure principal de serviço que o plug-in do Maven utiliza quando implementar o contentor do Azure.

1. Abra uma linha de comandos.

1. Inicie sessão na sua conta do Azure utilizando a CLI do Azure:
   ```shell
   az login
   ```
   Siga as instruções para concluir o processo de início de sessão.

1. Crie um Azure principal de serviço:
   ```shell
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

## <a name="configure-maven-to-use-your-azure-service-principal"></a>Configurar o Maven para utilizar o principal de serviço do Azure

Nesta secção, utilize os valores do seu principal de serviço do Azure para configurar a autenticação Maven irá utilizar quando implementar o contentor do Azure.

1. Abra o Maven `settings.xml` ficheiro num editor de texto; este ficheiro pode ser um caminho como os exemplos seguintes:
   * `/etc/maven/settings.xml`
   * `%ProgramFiles%\apache-maven\3.5.0\conf\settings.xml`
   * `$HOME/.m2/settings.xml`

1. Adicionar as definições de principal de serviço do Azure da secção anterior deste tutorial para o `<servers>` coleção no *settings.xml* ficheiro; por exemplo:

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
   `<environment>` | Define o ambiente de nuvem do Azure de destino, o que é `AZURE` neste exemplo. (Uma lista completa dos ambientes está disponível no [Plug-in do Maven para a aplicação Web para contentores] documentação)

1. Guarde e feche o *settings.xml* ficheiro.

## <a name="optional-deploy-your-local-docker-file-to-docker-hub"></a>OPCIONAL: Implementar o ficheiro de Docker local para o Hub de Docker

Se tiver uma conta de Docker, pode compilar o Docker localmente a imagem do contentor e enviá-lo para o Hub de Docker. Para tal, utilize os seguintes passos.

1. Abra o `pom.xml` ficheiro para a sua aplicação de arranque de mola num editor de texto.

1. Localize o `<imageName>` elemento subordinado do `<containerSettings>` elemento.

1. Atualização do `${docker.image.prefix}` valor com o nome da sua conta de Docker:
   ```xml
   <containerSettings>
      <imageName>mydockeraccountname/${project.artifactId}</imageName>
   </containerSettings>
   ```

1. Escolha um dos seguintes métodos de implementação:

   * Criar a imagem de contentor localmente com o Maven e, em seguida, utilizar o Docker para emitir o contentor no Docker Hub:
      ```shell
      mvn clean package docker:build
      docker push
      ```

   * Se tiver o [Plug-in do Docker para Maven] instalado, pode compilar automaticamente e o contentor de imagem para Docker Hub, utilizando o `-DpushImage` parâmetro:
      ```shell
      mvn clean package docker:build -DpushImage
      ```

## <a name="optional-customize-your-pomxml-before-deploying-your-container-to-azure"></a>OPCIONAL: Personalizar o pom.xml antes de implementar o contentor do Azure

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
         <resourceGroup>maven-plugin</resourceGroup>
         <appName>maven-linux-app-${maven.build.timestamp}</appName>
         <region>westus</region>
         <containerSettings>
            <imageName>${docker.image.prefix}/${project.artifactId}</imageName>
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

Existem vários valores que pode modificar para o plug-in do Maven e uma descrição detalhada para cada um destes elementos está disponível no [Plug-in do Maven para a aplicação Web para contentores] documentação. Que a ser consiga aceder tal, existem vários valores, realçando neste artigo:

Elemento | Descrição
---|---|---
`<version>` | Especifica a versão do [Plug-in do Maven para a aplicação Web para contentores]. Deve verificar a versão indicada no [Respository Maven Central](http://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-webapp-maven-plugin%22) para se certificar de que está a utilizar a versão mais recente.
`<authentication>` | Especifica as informações de autenticação do Azure, que, neste exemplo, contém um `<serverId>` elemento que contenha `azure-auth`; Maven utiliza esse valor para procurar o principal de serviço do Azure valores no seu Maven *settings.xml* ficheiro, que é definida uma secção anterior deste artigo.
`<resourceGroup>` | Especifica o grupo de recursos de destino, o que é `maven-plugin` neste exemplo. O grupo de recursos será criado durante a implementação, se já existir.
`<appName>` | Especifica o nome de destino para a sua aplicação web. Neste exemplo, o nome de destino é `maven-linux-app-${maven.build.timestamp}`, onde o `${maven.build.timestamp}` é acrescentado um sufixo neste exemplo, para evitar conflitos. (O timestamp é opcional; pode especificar qualquer cadeia exclusiva para o nome da aplicação).
`<region>` | Especifica a região de destino, que, neste exemplo, é `westus`. (Uma lista completa está no [Plug-in do Maven para a aplicação Web para contentores] documentação.)
`<appSettings>` | Especifica as definições de exclusivas para o Maven utilizar quando implementar a aplicação web no Azure. Neste exemplo, um `<property>` elemento contém um par nome/valor de elementos subordinados que especifique a porta para a sua aplicação.

> [!NOTE]
>
> As definições para alterar o número da porta neste exemplo, apenas são necessárias quando está a alterar a porta da predefinição.
>

## <a name="build-and-deploy-your-container-to-azure"></a>Criar e implementar o contentor do Azure

Assim que tiver configurado todas as definições nas secções anteriores deste artigo, estará pronto implementar o contentor do Azure. Para fazê-lo, siga os seguintes passos:

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
> ```bash
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

Para obter mais informações sobre as várias tecnologias abordadas neste artigo, consulte os artigos seguintes:

* [Plug-in do Maven para a aplicação Web para contentores]

* [Inicie sessão no Azure a partir da CLI do Azure](/azure/xplat-cli-connect)

* [Como utilizar o plug-in do Maven para a aplicação Web para contentores para implementar uma aplicação de arranque de mola no serviço de aplicações do Azure no Linux](../app-service-web-deploy-spring-boot-app-with-maven-plugin.md)

* [Criar um Azure principal de serviço com o Azure CLI 2.0](/cli/azure/create-an-azure-service-principal-azure-cli)

* [Referência de definições do maven](https://maven.apache.org/settings.html)

* [Plug-in do Docker para Maven]

<!-- URL List -->

[Interface de linha de comandos do Azure (CLI)]: /cli/azure/overview
[Azure Java Developer Center]: https://azure.microsoft.com/develop/java/
[portal do Azure]: https://portal.azure.com/
[Docker]: https://www.docker.com/
[Plug-in do Docker para Maven]: https://github.com/spotify/docker-maven-plugin
[conta do Azure gratuita]: https://azure.microsoft.com/pricing/free-trial/
[Git]: https://github.com/
[Java Developer Kit (JDK)]: http://www.oracle.com/technetwork/java/javase/downloads/
[Java Tools for Visual Studio Team Services]: https://java.visualstudio.com/
[Maven]: http://maven.apache.org/
[benefícios de subscritor do MSDN]: https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/
[Spring Boot]: http://projects.spring.io/spring-boot/
[Spring Boot on Docker Getting Started]: https://github.com/spring-guides/gs-spring-boot-docker
[Spring Framework]: https://spring.io/
[Plug-in do Maven para a aplicação Web para contentores]: https://github.com/Microsoft/azure-maven-plugins/tree/master/azure-webapp-maven-plugin

<!-- IMG List -->

[AP01]: ./media/app-service-web-deploy-containerized-spring-boot-app-with-maven-plugin/AP01.png
[AP02]: ./media/app-service-web-deploy-containerized-spring-boot-app-with-maven-plugin/AP02.png
