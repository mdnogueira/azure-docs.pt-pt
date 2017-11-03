---
title: "Como utilizar o plug-in do Maven para Web Apps do Azure para implementar uma aplicação de arranque de mola no Azure"
description: "Saiba como utilizar o plug-in do Maven para Web Apps do Azure para implementar uma aplicação de arranque de mola no Azure."
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
ms.openlocfilehash: f22089349e149d8e71df88d5c173869d2a5abad7
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-use-the-maven-plugin-for-azure-web-apps-to-deploy-a-spring-boot-app-to-azure"></a>Como utilizar o plug-in do Maven para Web Apps do Azure para implementar uma aplicação de arranque de mola no Azure

O [Plug-in do Maven para Web Apps do Azure](https://github.com/Microsoft/azure-maven-plugins/tree/master/azure-webapp-maven-plugin) para [Apache Maven](http://maven.apache.org/) fornece uma integração perfeita do App Service do Azure em projetos Maven e simplifica o processo para programadores implementar as aplicações web para a aplicação do Azure Serviço.

Este artigo demonstra utilizando o plug-in do Maven para Web Apps do Azure para implementar um exemplo de aplicação de arranque de mola aos serviços de aplicações do Azure.

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

## <a name="clone-the-sample-spring-boot-web-app"></a>Clone a aplicação de web de arranque de mola de exemplo

Nesta secção, clonar uma aplicação de arranque de mola concluída e testá-lo localmente.

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

1. Clone o [mola arranque introdução] projeto de exemplo para o diretório que criou; por exemplo:
   ```shell
   git clone https://github.com/microsoft/gs-spring-boot
   ```

1. Altere o diretório para o projeto concluído; Por exemplo:
   ```shell
   cd gs-spring-boot/complete
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

1. Deverá ver a mensagem seguinte apresentada: **saudações de arranque de mola!**

## <a name="create-an-azure-service-principal"></a>Criar um Azure principal de serviço

Nesta secção, vai criar um Azure principal de serviço que o plug-in do Maven utiliza ao implementar a aplicação web no Azure.

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
   > Irá utilizar os valores desta resposta JSON quando configurar o plug-in do Maven para implementar a aplicação web no Azure. O `aaaaaaaa`, `uuuuuuuu`, `pppppppp`, e `tttttttt` são valores de marcador de posição, o que são utilizados neste exemplo, para facilitar a mapear estes valores para os respetivos elementos quando configura o Maven `settings.xml` ficheiros na secção seguinte .
   >
   >

## <a name="configure-maven-to-use-your-azure-service-principal"></a>Configurar o Maven para utilizar o principal de serviço do Azure

Nesta secção, utilize os valores do seu principal de serviço do Azure para configurar a autenticação Maven quando implementar a aplicação web no Azure.

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
   `<environment>` | Define o ambiente de nuvem do Azure de destino, o que é `AZURE` neste exemplo. (Uma lista completa dos ambientes está disponível no [Plug-in do Maven para Web Apps do Azure] documentação)

1. Guarde e feche o *settings.xml* ficheiro.

## <a name="optional-customize-your-pomxml-before-deploying-your-web-app-to-azure"></a>OPCIONAL: Personalizar o pom.xml antes de implementar a aplicação web no Azure

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
         <appName>maven-web-app-${maven.build.timestamp}</appName>
         <region>westus</region>
         <javaVersion>1.8</javaVersion>
         <deploymentType>ftp</deploymentType>
         <resources>
            <resource>
               <directory>${project.basedir}/target</directory>
               <targetPath>/</targetPath>
               <includes>
                  <include>*.jar</include>
               </includes>
            </resource>
            <resource>
               <directory>${project.basedir}</directory>
               <targetPath>/</targetPath>
               <includes>
                  <include>web.config</include>
               </includes>
            </resource>
         </resources>
      </configuration>
   </plugin>
   ```

Existem vários valores que pode modificar para o plug-in do Maven e uma descrição detalhada para cada um destes elementos está disponível no [Plug-in do Maven para Web Apps do Azure] documentação. Que a ser consiga aceder tal, existem vários valores, realçando neste artigo:

Elemento | Descrição
---|---|---
`<version>` | Especifica a versão do [Plug-in do Maven para Web Apps do Azure]. Deve verificar a versão indicada no [Respository Maven Central](http://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-webapp-maven-plugin%22) para se certificar de que está a utilizar a versão mais recente.
`<authentication>` | Especifica as informações de autenticação do Azure, que, neste exemplo, contém um `<serverId>` elemento que contenha `azure-auth`; Maven utiliza esse valor para procurar o principal de serviço do Azure valores no seu Maven *settings.xml* ficheiro, que é definida uma secção anterior deste artigo.
`<resourceGroup>` | Especifica o grupo de recursos de destino, o que é `maven-plugin` neste exemplo. O grupo de recursos é criado durante a implementação, se já existir.
`<appName>` | Especifica o nome de destino para a sua aplicação web. Neste exemplo, o nome de destino é `maven-web-app-${maven.build.timestamp}`, onde o `${maven.build.timestamp}` é acrescentado um sufixo neste exemplo, para evitar conflitos. (O timestamp é opcional; pode especificar qualquer cadeia exclusiva para o nome da aplicação).
`<region>` | Especifica a região de destino, que, neste exemplo, é `westus`. (Uma lista completa está no [Plug-in do Maven para Web Apps do Azure] documentação.)
`<javaVersion>` | Especifica a versão de tempo de execução Java para a sua aplicação web. (Uma lista completa está no [Plug-in do Maven para Web Apps do Azure] documentação.)
`<deploymentType>` | Especifica o tipo de implementação para a sua aplicação web. Por agora, apenas `ftp` é suportada, embora suporte para outros tipos de implementação é de desenvolvimento.
`<resources>` | Especifica os recursos e destinos de destino que utiliza o Maven quando implementar a aplicação web no Azure. Neste exemplo, dois `<resource>` elementos especificar que o Maven irá implementar o ficheiro JAR da sua aplicação web e o *Web. config* ficheiros do projeto de arranque de mola.

## <a name="build-and-deploy-your-web-app-to-azure"></a>Criar e implementar a aplicação web no Azure

Assim que tiver configurado todas as definições nas secções anteriores deste artigo, estará pronto implementar a aplicação web no Azure. Para fazê-lo, siga os seguintes passos:

1. Na linha de comandos ou janela de terminal que estava a utilizar anteriormente, reconstrua o ficheiro JAR com o Maven se tiver efetuado alterações para o *pom.xml* ficheiro; por exemplo:
   ```shell
   mvn clean package
   ```

1. Implementar a aplicação web no Azure utilizando o Maven; Por exemplo:
   ```shell
   mvn azure-webapp:deploy
   ```

Maven irá implementar a aplicação web no Azure; Se a aplicação web não já existir, será criado.

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

* [Plug-in do Maven para Web Apps do Azure]

* [Inicie sessão no Azure a partir da CLI do Azure](/azure/xplat-cli-connect)

* [Como utilizar o plug-in do Maven para Web Apps do Azure para implementar uma aplicação de arranque de mola de para o Azure](containers/app-service-web-deploy-containerized-spring-boot-app-with-maven-plugin.md)

* [Criar um Azure principal de serviço com o Azure CLI 2.0](/cli/azure/create-an-azure-service-principal-azure-cli)

* [Referência de definições do maven](https://maven.apache.org/settings.html)

<!-- URL List -->

[Interface de linha de comandos do Azure (CLI)]: /cli/azure/overview
[Azure Java Developer Center]: https://azure.microsoft.com/develop/java/
[portal do Azure]: https://portal.azure.com/
[conta do Azure gratuita]: https://azure.microsoft.com/pricing/free-trial/
[Git]: https://github.com/
[Java Developer Kit (JDK)]: http://www.oracle.com/technetwork/java/javase/downloads/
[Java Tools for Visual Studio Team Services]: https://java.visualstudio.com/
[Maven]: http://maven.apache.org/
[benefícios de subscritor do MSDN]: https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/
[Spring Boot]: http://projects.spring.io/spring-boot/
[mola arranque introdução]: https://github.com/microsoft/gs-spring-boot
[Spring Framework]: https://spring.io/
[Plug-in do Maven para Web Apps do Azure]: https://github.com/Microsoft/azure-maven-plugins/tree/master/azure-webapp-maven-plugin

<!-- IMG List -->

[AP01]: ./media/app-service-web-deploy-spring-boot-app-with-maven-plugin/AP01.png
[AP02]: ./media/app-service-web-deploy-spring-boot-app-with-maven-plugin/AP02.png
