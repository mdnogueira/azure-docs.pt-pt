---
title: "Implementar uma aplicação de arranque mola para o App Service do Azure | Microsoft Docs"
description: "Este tutorial irá guiá-os programadores os passos para implementar a aplicação de web mola começar a utilizar o arranque do serviço de aplicações do Azure."
services: app-service\web
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
ms.date: 08/04/2017
ms.author: asirveda;robmcm
ms.openlocfilehash: 8776142d5452bf5057990702c89aa1a541382ffc
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="deploy-a-spring-boot-application-to-the-azure-app-service"></a>Implementar uma Aplicação Spring Boot no Serviço de Aplicações do Azure

O  **[mola Framework]**  é uma solução de open source que ajuda os programadores de Java criar aplicações de nível empresarial, não sendo um dos projetos do mais populares baseia-se de que plataforma [ Mola arranque], que fornece uma abordagem simplificada para criar aplicações de Java autónomas.

Este tutorial irá guiá-lo embora criar a aplicação de web de mola começar a utilizar o arranque de exemplo e respetiva implementação [App Service do Azure].

### <a name="prerequisites"></a>Pré-requisitos

Para concluir os passos neste tutorial, terá de ter o seguinte:

* Uma subscrição do Azure; Se ainda não tiver uma subscrição do Azure, pode ativar o [benefícios de subscritor do MSDN] ou inscrever-se um [conta do Azure gratuita].
* Um atualizados [Java Developer Kit (JDK)].
* Do Apache [Maven] criar ferramenta (versão 3).
* A [Git] cliente.

## <a name="create-the-spring-boot-getting-started-web-app"></a>Criar a aplicação de web mola arranque introdução

Os passos seguintes irão guiá-lo pelos passos necessários para criar uma aplicação de web de arranque de mola simples e testá-lo localmente.

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

1. Clone o [mola arranque introdução] projeto de exemplo para o diretório que acabou de criar; por exemplo:
   ```
   git clone https://github.com/spring-guides/gs-spring-boot.git
   ```

1. Altere o diretório para o projeto concluído; Por exemplo:
   ```
   cd gs-spring-boot
   cd complete
   ```

1. Criar o ficheiro JAR com o Maven; Por exemplo:
   ```
   mvn package
   ```

1. Quando a aplicação web tiver sido criada, altere o diretório para o ficheiro JAR e iniciar a aplicação web; Por exemplo:
   ```
   cd target
   java -jar gs-spring-boot-0.1.0.jar
   ```

1. Testar a aplicação web, navegando até http://localhost:8080 utilizando um browser ou utilize a sintaxe semelhante ao seguinte exemplo, se tiver o curl disponível:
   ```
   curl http://localhost:8080
   ```

1. Deverá ver a mensagem seguinte apresentada: **saudações de arranque de mola!**

   ![Procure a aplicação de exemplo][SB01]

## <a name="create-an-azure-web-app-for-use-with-java"></a>Criar uma aplicação web do Azure para utilização com Java

Os passos seguintes irão guiá-lo pelos passos para criar uma aplicação Web do Azure, configure as definições necessárias para Java e configurar as suas credenciais FTP.

1. Navegue para o [portal do Azure] e iniciar sessão.

1. Depois de ter a sessão iniciada na sua conta no portal do Azure, clique no ícone de menu para **serviços aplicacionais**:
   
   ![Portal do Azure][AZ01]

1. Quando o **serviços aplicacionais** é apresentada a página, clique em **+ adicionar** para criar um novo serviço de aplicações.

   ![Criar o serviço de aplicações][AZ02]

1. Quando for apresentada a lista de modelos de aplicação web, clique na ligação para a aplicação de Web do Microsoft básico.

   ![Modelos de aplicação Web][AZ03]

1. Quando a página de informações para o modelo de aplicação Web é apresentado, clique em **criar**.

   ![Criar Aplicação Web][AZ04]

1. Forneça um nome exclusivo para a sua aplicação web e especifique quaisquer definições adicionais e, em seguida, **criar**.

   ![Criar definições da aplicação Web][AZ05]

1. Quando tiver sido criada a sua aplicação web, clique no ícone de menu para **serviços aplicacionais**e, em seguida, clique em sua aplicação web recentemente criada:

   ![Aplicações Web de lista][AZ06]

1. Quando for apresentada a sua aplicação web, especifique a versão de Java utilizando os seguintes passos:

   a. Clique em de **definições da aplicação** item de menu.

   b. Escolha **Java 8** para a versão de Java.

   c. Escolha **mais recente** para a versão secundária do Java.

   d. Escolha **mais recentes Tomcat 8.5** para o contentor web. (Este contentor não serão realmente utilizado; Azure irá utilizar o contentor da sua aplicação de arranque de mola.)

   e. Clique em **Guardar**.

   ![Definições da aplicação][AZ07]

1. Especifique as credenciais de implementação de FTP utilizando os seguintes passos:

   a. Clique em de **as credenciais de implementação** item de menu.

   b. Especifique o nome de utilizador e palavra-passe.

   c. Clique em **Guardar**.

   ![Especifique as credenciais de implementação][AZ08]

1. Obter as informações de ligação de FTP utilizando os seguintes passos:

   a. Clique em de **as credenciais de implementação** item de menu.

   b. Copie o seu nome de utilizador FTP completo e o URL e guardá-las para a secção seguinte deste tutorial.

   ![URL de FTP e as credenciais][AZ09]

## <a name="deploy-your-spring-boot-web-app-to-azure"></a>Implementar a aplicação de web de arranque de mola no Azure

Os passos seguintes irão guiá-lo pelos passos para implementar a aplicação de web de arranque de mola no Azure.

1. Abra um editor de texto como o bloco de notas do Windows e cole o seguinte texto para um documento novo, em seguida, guarde o ficheiro como *Web. config*:
   ```xml
   <?xml version="1.0" encoding="UTF-8"?>
   <configuration>
     <system.webServer>
       <handlers>
         <add name="httpPlatformHandler" path="*" verb="*" modules="httpPlatformHandler" resourceType="Unspecified" />
       </handlers>
       <httpPlatform processPath="%JAVA_HOME%\bin\java.exe"
           arguments="-Djava.net.preferIPv4Stack=true -Dserver.port=%HTTP_PLATFORM_PORT% -jar &quot;%HOME%\site\wwwroot\gs-spring-boot-0.1.0.jar&quot;">
       </httpPlatform>
     </system.webServer>
   </configuration>
   ```

1. Depois de guardar o *Web. config* ao seu sistema de ficheiros, ligar à sua aplicação web através de FTP utilizando o URL, nome de utilizador e palavra-passe da secção anterior deste tutorial. Por exemplo:
   ```
   ftp
   open waws-prod-sn0-000.ftp.azurewebsites.windows.net
   user wingtiptoys-springboot\wingtiptoysuser
   pass ********
   ```

1. Altere o diretório remoto para a pasta raiz da sua aplicação web, (que é */site/wwwroot*), em seguida, copie o ficheiro JAR da sua aplicação de arranque de mola e o *Web. config* de versões anteriores. Por exemplo:
   ```
   cd site/wwwroot
   put gs-spring-boot-0.1.0.jar
   put web.config
   ```

1. Depois de ter implementado o JAR e *Web. config* ficheiros à sua aplicação web, terá de reiniciar a aplicação web no portal do Azure:

   ![][AZ10]

1. Testar a aplicação web ao navegar para o URL da aplicação web utilizando um browser ou utilize a sintaxe semelhante ao seguinte exemplo, se tiver o curl disponível:
   ```
   curl http://wingtiptoys-springboot.azurewebsites.net/
   ```

1. Deverá ver a mensagem seguinte apresentada: **saudações de arranque de mola!**

   ![Procure a aplicação de exemplo][SB02]

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre como utilizar aplicações de arranque de mola no Azure, consulte os artigos seguintes:

* [Implementar uma aplicação de arranque mola no Linux no serviço de contentor do Azure](../container-service/kubernetes/container-service-deploy-spring-boot-app-on-linux.md)

* [Implementar uma aplicação de arranque mola num Cluster Kubernetes no serviço de contentor do Azure](../container-service/kubernetes/container-service-deploy-spring-boot-app-on-kubernetes.md)

Para obter mais informações sobre como utilizar o Azure com o Java, veja o [Centro de Programadores de Java do Azure] e [Java Tools for Visual Studio Team Services] (Ferramentas de Java para o Visual Studio Team Services).

Para obter informações adicionais sobre depoying web apps do Azure a utilizar FTP, consulte o artigo [implementar a aplicação no serviço de aplicações do Azure através de FTP/S].

Para obter mais detalhes sobre o projeto de exemplo mola arranque, consulte [mola arranque introdução].

Para obter ajuda com a introdução com as suas próprias aplicações de mola arranque, consulte o **mola Initializr** em https://start.spring.io/.

Para obter mais informações sobre como configurar definições adicionais para a sua aplicação web, consulte [configurar web apps no App Service do Azure].

<!-- URL List -->

[App Service do Azure]: https://azure.microsoft.com/services/app-service/
[Azure Container Service]: https://azure.microsoft.com/services/container-service/
[Centro de Programadores de Java do Azure]: https://azure.microsoft.com/develop/java/
[portal do Azure]: https://portal.azure.com/
[configurar web apps no App Service do Azure]: /azure/app-service/web-sites-configure
[implementar a aplicação no serviço de aplicações do Azure através de FTP/S]: https://docs.microsoft.com/azure/app-service/app-service-deploy-ftp
[conta do Azure gratuita]: https://azure.microsoft.com/pricing/free-trial/
[Git]: https://github.com/
[Java Developer Kit (JDK)]: http://www.oracle.com/technetwork/java/javase/downloads/
[Java Tools for Visual Studio Team Services]: https://java.visualstudio.com/
[Maven]: http://maven.apache.org/
[benefícios de subscritor do MSDN]: https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/
[ Mola arranque]: http://projects.spring.io/spring-boot/
[mola arranque introdução]: https://github.com/spring-guides/gs-spring-boot
[mola Framework]: https://spring.io/

<!-- IMG List -->

[SB01]: ./media/app-service-deploy-spring-boot-web-app-on-azure/SB01.png
[SB02]: ./media/app-service-deploy-spring-boot-web-app-on-azure/SB02.png

[AZ01]: ./media/app-service-deploy-spring-boot-web-app-on-azure/AZ01.png
[AZ02]: ./media/app-service-deploy-spring-boot-web-app-on-azure/AZ02.png
[AZ03]: ./media/app-service-deploy-spring-boot-web-app-on-azure/AZ03.png
[AZ04]: ./media/app-service-deploy-spring-boot-web-app-on-azure/AZ04.png
[AZ05]: ./media/app-service-deploy-spring-boot-web-app-on-azure/AZ05.png
[AZ06]: ./media/app-service-deploy-spring-boot-web-app-on-azure/AZ06.png
[AZ07]: ./media/app-service-deploy-spring-boot-web-app-on-azure/AZ07.png
[AZ08]: ./media/app-service-deploy-spring-boot-web-app-on-azure/AZ08.png
[AZ09]: ./media/app-service-deploy-spring-boot-web-app-on-azure/AZ09.png
[AZ10]: ./media/app-service-deploy-spring-boot-web-app-on-azure/AZ10.png
