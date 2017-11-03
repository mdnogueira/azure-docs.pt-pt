---
title: "Como configurar uma aplicação de inicializador de arranque a utilizar a Cache de Redis"
description: "Saiba como configurar uma aplicação de arranque de mola criada com o Initializr mola para utilizar a Cache de Redis do Azure."
services: redis-cache
documentationcenter: java
author: rmcmurray
manager: cfowler
editor: 
keywords: Mola, mola de arranque de arranque, a Cache de Redis
ms.assetid: 
ms.service: cache
ms.workload: tbd
ms.tgt_pltfrm: cache-redis
ms.devlang: java
ms.topic: article
ms.date: 08/31/2017
ms.author: robmcm;zhijzhao;yidon
ms.openlocfilehash: 7a6ec549654d00975494bac8594a6777af5ec415
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-configure-a-spring-boot-initializer-app-to-use-redis-cache"></a>Como configurar uma aplicação de inicializador de arranque a utilizar a Cache de Redis

## <a name="overview"></a>Descrição geral

O  **[mola Framework]**  é uma solução de open source que ajuda os programadores de Java criar aplicações de nível empresarial. Um dos projetos mais popular do que é criada em cima de que a plataforma é [mola arranque], que fornece uma abordagem simplificada para criar aplicações de Java autónomas. Para ajudar os programadores a começar com o arranque de mola, vários pacotes de arranque de mola de exemplo estão disponíveis em <https://github.com/spring-guides/>. Além de escolherem da lista de projetos de arranque de mola básicas, o  **[mola Initializr]**  ajuda os programadores a começar a criar aplicações de mola arranque personalizadas.

Este artigo explica como criar uma cache de Redis no portal do Azure, em seguida, utilizar o **mola Initializr** para criar uma aplicação personalizada e, em seguida, criar uma aplicação web do Java que armazena e obtém dados utilizando o Redis cache.

## <a name="prerequisites"></a>Pré-requisitos

Os seguintes pré-requisitos são necessários para poder seguir os passos neste artigo:

* Uma subscrição do Azure; Se ainda não tiver uma subscrição do Azure, pode ativar o [benefícios de subscritor do MSDN] ou inscrever-se um [conta do Azure gratuita].

* A [Kit de desenvolvimento Java (JDK)](http://www.oracle.com/technetwork/java/javase/downloads/), versão 1.7 ou posterior.

* [Apache Maven](http://maven.apache.org/), versão 3.0 ou posterior.

## <a name="create-a-redis-cache-on-azure"></a>Criar uma cache de Redis no Azure

1. Navegue para o portal do Azure em <https://portal.azure.com/> e clique no item para **+ novo**.

   ![Portal do Azure][AZ01]

1. Clique em **base de dados**e, em seguida, clique em **a Cache de Redis**.

   ![Portal do Azure][AZ02]

1. No **nova Cache de Redis** página, especifique as seguintes informações:

   * Introduza o **nome DNS** para a sua cache.
   * Especifique o **subscrição**, **grupo de recursos**, **localização**, e **escalão de preço**.
   * Para este tutorial, escolha **desbloquear porta 6379**.

   > [!NOTE]
   >
   > Pode utilizar o SSL com caches de Redis, mas terá de utilizar um cliente de Redis diferente como Jedis. Para obter mais informações, consulte [como utilizar a Cache de Redis do Azure com Java][Redis Cache with Java].
   >

   Quando especificar estas opções, clique em **criar** para criar a sua cache.

   ![Portal do Azure][AZ03]

1. Assim que a sua cache foi concluída, verá, listado no seu Azure **Dashboard**, bem como no **todos os recursos**, e **Caches de Redis** páginas. Pode clicar em cache em qualquer dessas localizações para abrir a página de propriedades para a sua cache.

   ![Portal do Azure][AZ04]

1. Quando a página que contém a lista de propriedades para a cache é apresentada, clique em **chaves de acesso** e copie as chaves de acesso para a sua cache.

   ![Portal do Azure][AZ05]

## <a name="create-a-custom-application-using-the-spring-initializr"></a>Criar uma aplicação personalizada utilizando o Initializr mola

1. Navegue até à <https://start.spring.io/>.

1. Especifique se pretende gerar um **Maven** projetar com **Java**, introduza o **grupo** e **Aritifact** nomes para a sua aplicação e, em seguida, clique na ligação para **mudar para a versão completa** do Initializr mola.

   ![Opções de Initializr Basic mola][SI01]

   > [!NOTE]
   >
   > O Initializr mola utilizará o **grupo** e **Aritifact** nomes para criar o nome do pacote; por exemplo: *com.contoso.myazuredemo*.
   >

1. Desloque para baixo até o **Web** secção e marque a caixa de **Web**, em seguida, desloque para baixo até o **NoSQL** secção e marque a caixa de **Redis**, em seguida, desloque-se na parte inferior da página e clique no botão para **gerar projeto**.

   ![Opções de Initializr completo mola][SI02]

1. Quando lhe for pedido, transfira o projeto para um caminho no seu computador local.

   ![Transferir o projeto de arranque de mola personalizado][SI03]

1. Depois de ter de extrair os ficheiros no sistema local, a aplicação de arranque de mola personalizada estará pronta para editar.

   ![Ficheiros de projeto de arranque personalizada mola][SI04]

## <a name="configure-your-custom-spring-boot-to-use-your-redis-cache"></a>Configurar o seu arranque mola personalizado para utilizar a Cache de Redis

1. Localize o *application.properties* ficheiros o *recursos* diretório da sua aplicação, ou criar o ficheiro se já existir.

   ![Localize o ficheiro application.properties][RE01]

1. Abra o *application.properties* ficheiro num editor de texto e adicione as seguintes linhas ao ficheiro e substitua os valores de exemplo com as propriedades adequadas da sua cache:

   ```yaml
   # Specify the DNS URI of your Redis cache.
   spring.redis.host=myspringbootcache.redis.cache.windows.net

   # Specify the port for your Redis cache.
   spring.redis.port=6379

   # Specify the access key for your Redis cache.
   spring.redis.password=57686f6120447564652c2049495320526f636b73=
   ```

   ![Editar o ficheiro application.properties][RE02]

   > [!NOTE]
   >
   > Se estava a utilizar um cliente de Redis diferente como Jedis ativa SSL, tem de especificar a porta 6380 na sua *application.properties* ficheiro. Para obter mais informações, consulte [como utilizar a Cache de Redis do Azure com Java][Redis Cache with Java].
   >

1. Guarde e feche o *application.properties* ficheiro.

1. Crie uma pasta denominada *controlador* sob a pasta de origem do seu pacote; por exemplo:

   `C:\SpringBoot\myazuredemo\src\main\java\com\contoso\myazuredemo\controller`

   -ou-

   `/users/example/home/myazuredemo/src/main/java/com/contoso/myazuredemo/controller`

1. Criar um novo ficheiro designado *HelloController.java* no *controlador* pasta. Abra o ficheiro num editor de texto e adicione o seguinte código:

   ```java
   package com.contoso.myazuredemo;

   import org.springframework.web.bind.annotation.RequestMapping;
   import org.springframework.web.bind.annotation.RestController;
   import org.springframework.beans.factory.annotation.Autowired;
   import org.springframework.boot.SpringApplication;
   import org.springframework.boot.autoconfigure.SpringBootApplication;
   import org.springframework.data.redis.core.StringRedisTemplate;
   import org.springframework.data.redis.core.ValueOperations;

   @RestController
   public class HelloController {
   
      @Autowired
      private StringRedisTemplate template;

      @RequestMapping("/")
      // Define the Hello World controller.
      public String hello() {
      
         ValueOperations<String, String> ops = this.template.opsForValue();

         // Add a Hello World string to your cache.
         String key = "greeting";
         if (!this.template.hasKey(key)) {
             ops.set(key, "Hello World!");
         }

         // Return the string from your cache.
         return ops.get(key);
      }
   }
   ```
   
   Onde é necessário substituir `com.contoso.myazuredemo` com o nome do pacote para o seu projeto.

1. Guarde e feche o *HelloController.java* ficheiro.

1. Criar a sua aplicação de arranque de mola com o Maven e executá-la; Por exemplo:

   ```shell
   mvn clean package
   mvn spring-boot:run
   ```

1. Testar a aplicação web, navegando até http://localhost:8080 utilizando um browser ou utilize a sintaxe semelhante ao seguinte exemplo, se tiver o curl disponível:

   ```shell
   curl http://localhost:8080
   ```

   Deverá ver o "Olá mundo!" mensagem do seu controlador de exemplo apresentado, que está a ser obtido dinamicamente a cache de Redis.

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre como utilizar aplicações de arranque de mola no Azure, consulte os artigos seguintes:

* [Implementar uma aplicação de arranque mola para o App Service do Azure](../app-service/app-service-deploy-spring-boot-web-app-on-azure.md)

* [Executar uma aplicação de arranque de mola num Cluster Kubernetes no serviço de contentor do Azure](../container-service/container-service-deploy-spring-boot-app-on-kubernetes.md)

Para obter mais informações sobre como utilizar o Azure com o Java, veja o [Centro de Programadores de Java do Azure] e [Java Tools for Visual Studio Team Services] (Ferramentas de Java para o Visual Studio Team Services).

Para obter mais informações sobre como obter iniciado com a Cache de Redis com o Java no Azure, consulte [como utilizar a Cache de Redis do Azure com Java][Redis Cache with Java].

<!-- URL List -->

[Centro de Programadores de Java do Azure]: https://azure.microsoft.com/develop/java/
[conta do Azure gratuita]: https://azure.microsoft.com/pricing/free-trial/
[Java Tools for Visual Studio Team Services]: https://java.visualstudio.com/
[benefícios de subscritor do MSDN]: https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/
[mola arranque]: http://projects.spring.io/spring-boot/
[mola Initializr]: https://start.spring.io/
[mola Framework]: https://spring.io/
[Redis Cache with Java]: cache-java-get-started.md

<!-- IMG List -->

[AZ01]: ./media/cache-java-spring-boot-initializer-with-redis-cache/AZ01.png
[AZ02]: ./media/cache-java-spring-boot-initializer-with-redis-cache/AZ02.png
[AZ03]: ./media/cache-java-spring-boot-initializer-with-redis-cache/AZ03.png
[AZ04]: ./media/cache-java-spring-boot-initializer-with-redis-cache/AZ04.png
[AZ05]: ./media/cache-java-spring-boot-initializer-with-redis-cache/AZ05.png

[SI01]: ./media/cache-java-spring-boot-initializer-with-redis-cache/SI01.png
[SI02]: ./media/cache-java-spring-boot-initializer-with-redis-cache/SI02.png
[SI03]: ./media/cache-java-spring-boot-initializer-with-redis-cache/SI03.png
[SI04]: ./media/cache-java-spring-boot-initializer-with-redis-cache/SI04.png

[RE01]: ./media/cache-java-spring-boot-initializer-with-redis-cache/RE01.png
[RE02]: ./media/cache-java-spring-boot-initializer-with-redis-cache/RE02.png
