---
title: Como utilizar o arranque de arranque mola com uma API de DocumentDB do Azure Cosmos DB
description: "Saiba como configurar uma aplicação criada com o inicializador de arranque mola com a API de DocumentDB do Azure Cosmos DB."
services: cosmos-db
documentationcenter: java
author: rmcmurray
manager: cfowler
editor: 
keywords: Cosmos mola, mola de arranque de arranque, base de dados
ms.assetid: 
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: java
ms.topic: article
ms.date: 08/08/2017
ms.author: robmcm;yungez;kevinzha
ms.openlocfilehash: 273cc750857c5e466882060a38ac0f3475811e98
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-use-the-spring-boot-starter-with-azure-cosmos-db-documentdb-api"></a>Como utilizar o arranque de arranque de mola com a API de DocumentDB do Azure Cosmos DB

## <a name="overview"></a>Descrição geral

O  **[mola Framework]**  é uma solução de open source que ajuda os programadores de Java criar aplicações de nível empresarial. Um dos projetos mais popular do que é criada em cima de que a plataforma é [mola arranque], que fornece uma abordagem simplificada para criar aplicações de Java autónomas. Para ajudar os programadores a começar com o arranque de mola, vários pacotes de arranque de mola de exemplo estão disponíveis em <https://github.com/spring-guides/>. Além de escolherem da lista de projetos de arranque de mola básicas, o  **[mola Initializr]**  ajuda os programadores a começar a criar aplicações de mola arranque personalizadas.

BD do Cosmos do Azure é um serviço de base de dados globalmente distribuído que permite aos programadores trabalhar com dados utilizando uma variedade de APIs padrão, tais como o DocumentDB, MongoDB, gráfico e APIs de tabela. Arranque de arranque de mola da Microsoft permite aos programadores utilizar aplicações de arranque a que se integram facilmente Azure Cosmos DB utilizando APIs do DocumentDB.

Este artigo demonstra a criar uma BD do Cosmos Azure no portal do Azure, em seguida, utilizar o **mola Initializr** para criar uma aplicação java personalizada e, em seguida, adicione a funcionalidade de arranque de arranque mola à sua aplicação personalizada para armazenar dados numa e obter dados da sua base de dados do Azure Cosmos utilizando a API do DocumentDB.

## <a name="prerequisites"></a>Pré-requisitos

Os seguintes pré-requisitos são necessários para poder seguir os passos neste artigo:

* Uma subscrição do Azure; Se ainda não tiver uma subscrição do Azure, pode ativar o [benefícios de subscritor do MSDN] ou inscrever-se um [conta do Azure gratuita].

* A [Kit de desenvolvimento Java (JDK)](http://www.oracle.com/technetwork/java/javase/downloads/), versão 1.7 ou posterior.

* [Apache Maven](http://maven.apache.org/), versão 3.0 ou posterior.

## <a name="create-an-azure-cosmos-db-by-using-the-azure-portal"></a>Criar uma base de dados do Cosmos do Azure utilizando o portal do Azure

1. Navegue para o portal do Azure em <https://portal.azure.com/> e clique em **+ novo**.

   ![Portal do Azure][AZ01]

1. Clique em **bases de dados**e, em seguida, clique em **Azure Cosmos DB**.

   ![Portal do Azure][AZ02]

1. No **Azure Cosmos DB** página, introduza as seguintes informações:

   * Introduza um único **ID**, que irá utilizar como o URI da base de dados. Por exemplo: *wingtiptoysdata.documents.azure.com*.
   * Escolha **SQL (documento DB)** para a API.
   * Escolha o **subscrição** que pretende utilizar para a base de dados.
   * Especifique se pretende criar uma nova **grupo de recursos** da base de dados, ou escolha um grupo de recursos existente.
   * Especifique o **localização** da base de dados.
   
   Quando especificar estas opções, clique em **criar** para criar a base de dados.

   ![Portal do Azure][AZ03]

1. Quando a base de dados tiver sido criado, está listado no seu Azure **Dashboard**, bem como no **todos os recursos** e **Azure Cosmos DB** páginas. Pode clicar na base de dados em qualquer dessas localizações para abrir a página de propriedades para a sua cache.

   ![Portal do Azure][AZ04]

1. Quando a página de propriedades para a base de dados é apresentado, clique em **chaves de acesso** e copie as chaves de acesso e de URI para a base de dados; se irá utilizar estes valores na sua aplicação de arranque de mola.

   ![Portal do Azure][AZ05]

## <a name="create-a-simple-spring-boot-application-with-the-spring-initializr"></a>Criar uma aplicação de arranque de mola simples com o Initializr mola

1. Navegue até à <https://start.spring.io/>.

1. Especifique se pretende gerar um **Maven** projetar com **Java**, introduza o **grupo** e **artefactos** nomes para a sua aplicação e, em seguida, clique no botão para **gerar projeto**.

   ![Opções de Initializr Basic mola][SI01]

   > [!NOTE]
   >
   > O Initializr mola utiliza o **grupo** e **artefactos** nomes para criar o nome do pacote; por exemplo: *com.example.wintiptoys*.
   >

1. Quando lhe for pedido, transfira o projeto para um caminho no seu computador local.

   ![Transferir o projeto de arranque de mola personalizado][SI02]

1. Depois de ter de extrair os ficheiros no sistema local, a aplicação de arranque de mola simple estará pronta para editar.

   ![Ficheiros de projeto de arranque personalizada mola][SI03]

## <a name="configure-your-spring-boot-app-to-use-the-azure-spring-boot-starter"></a>Configure a sua aplicação de arranque a utilizar o arranque de arranque de mola do Azure

1. Localize o *pom.xml* ficheiro no diretório da sua aplicação; por exemplo:

   `C:\SpringBoot\wingtiptoys\pom.xml`

   -ou-

   `/users/example/home/wingtiptoys/pom.xml`

   ![Localize o ficheiro pom.xml][PM01]

1. Abra o *pom.xml* ficheiro num editor de texto e adicione as seguintes linhas a lista de `<dependencies>`:

   ```xml
   <dependency>
      <groupId>com.microsoft.azure</groupId>
      <artifactId>azure-documentdb-spring-boot-starter</artifactId>
      <version>0.1.4</version>
   </dependency>
   ```

   ![Editar o ficheiro pom.xml][PM02]

1. Guarde e feche o *pom.xml* ficheiro.

## <a name="configure-your-spring-boot-app-to-use-your-azure-cosmos-db"></a>Configure a sua aplicação de arranque de mola para utilizar a base de dados do Azure Cosmos

1. Localize o *application.properties* ficheiros o *recursos* diretório da sua aplicação; por exemplo:

   `C:\SpringBoot\wingtiptoys\src\main\resources\application.properties`

   -ou-

   `/users/example/home/wingtiptoys/src/main/resources/application.properties`

   ![Localize o ficheiro application.properties][RE01]

1. Abra o *application.properties* ficheiro num editor de texto e adicione as seguintes linhas ao ficheiro e substitua os valores de exemplo com as propriedades adequadas para a base de dados:

   ```yaml
   # Specify the DNS URI of your Azure Cosmos DB.
   azure.documentdb.uri=https://wingtiptoys.documents.azure.com:443/

   # Specify the access key for your database.
   azure.documentdb.key=57686f6120447564652c20426f6220526f636b73==

   # Specify the name of your database.
   azure.documentdb.database=wingtiptoysdata
   ```

   ![Editar o ficheiro application.properties][RE02]

1. Guarde e feche o *application.properties* ficheiro.

## <a name="add-sample-code-to-implement-basic-database-functionality"></a>Adicione o código de exemplo para implementar a funcionalidade de base de dados básica

Esta secção cria duas classes de Java para armazenar dados de utilizador e, em seguida, modificar a classe de aplicação principal para criar uma instância da classe de utilizador e guarde-o à sua base de dados.

### <a name="define-a-basic-class-for-storing-user-data"></a>Definir uma classe para armazenar dados de utilizador básica

1. Criar um novo ficheiro designado *User.java* no mesmo diretório do ficheiro de Java a aplicação principal.

1. Abra o *User.java* ficheiro num editor de texto e adicione as seguintes linhas ao ficheiro para definir uma classe de utilizador genérico que armazena e obter os valores na base de dados:

   ```java
   package com.example.wingtiptoys;

   public class User {
      private String id;
      private String firstName;
      private String lastName;
 
      public User(String id, String firstName, String lastName) {
         this.id = id;
         this.firstName = firstName;
         this.lastName = lastName;
      }
   
      public String getId() {
         return this.id;
      }

      public void setId(String id) {
         this.id = id;
      }

      public String getFirstName() {
         return firstName;
      }

      public void setFirstName(String firstName) {
         this.firstName = firstName;
      }

      public String getLastName() {
         return lastName;
      }

      public void setLastName(String lastName) {
         this.lastName = lastName;
      }

      @Override
      public String toString() {
         return String.format("User: %s %s", firstName, lastName);
      }
   }
   ```

1. Guarde e feche o *User.java* ficheiro.

### <a name="define-a-data-repository-interface"></a>Defina uma interface de repositório de dados

1. Criar um novo ficheiro designado *UserRepository.java* no mesmo diretório do ficheiro de Java a aplicação principal.

1. Abra o *UserRepository.java* ficheiro num editor de texto e adicione as seguintes linhas ao ficheiro para definir uma interface de utilizador do repositório que expande a interface de repositório do DocumentDB:

   ```java
   package com.example.wingtiptoys;

   import com.microsoft.azure.spring.data.documentdb.repository.DocumentDbRepository;
   import org.springframework.stereotype.Repository;

   @Repository
   public interface UserRepository extends DocumentDbRepository<User, String> {}   
   ```

1. Guarde e feche o *UserRepository.java* ficheiro.

### <a name="modify-the-main-application-class"></a>Modificar a classe de aplicação principal

1. Localize o ficheiro de Java de aplicação principal no diretório do pacote da sua aplicação; Por exemplo:

   `C:\SpringBoot\wingtiptoys\src\main\java\com\example\wingtiptoys\WingtiptoysApplication.java`

   -ou-

   `/users/example/home/wingtiptoys/src/main/java/com/example/wingtiptoys/WingtiptoysApplication.java`

   ![Localize o ficheiro da aplicação Java][JV01]

1. Abra o ficheiro de Java de aplicação principal num editor de texto e adicione as seguintes linhas ao ficheiro:

   ```java
   package com.example.wingtiptoys;

   import org.springframework.boot.SpringApplication;
   import org.springframework.boot.autoconfigure.SpringBootApplication;
   import org.springframework.beans.factory.annotation.Autowired;
   import org.springframework.boot.CommandLineRunner;

   @SpringBootApplication
   public class WingtiptoysApplication implements CommandLineRunner {

      @Autowired
      private UserRepository repository;
    
      public static void main(String[] args) {
         SpringApplication.run(WingtiptoysApplication.class, args);
      }

      public void run(String... var1) throws Exception {
         final User testUser = new User("testId", "testFirstName", "testLastName");

         repository.deleteAll();
         repository.save(testUser);

         final User result = repository.findOne(testUser.getId());

         System.out.printf("\n\n%s\n\n",result.toString());
      }
   }
   ```

1. Guarde e feche o ficheiro de Java aplicação principal.

## <a name="build-and-test-your-app"></a>Criar e testar a sua aplicação

1. Abra uma linha de comandos e altere o diretório para a pasta onde o *pom.xml* ficheiro for localizado; por exemplo:

   `cd C:\SpringBoot\wingtiptoys`

   -ou-

   `cd /users/example/home/wingtiptoys`

1. Criar a sua aplicação de arranque de mola com o Maven e executá-la; Por exemplo:

   ```shell
   mvn package
   java -jar target/wingtiptoys-0.0.1-SNAPSHOT.jar
   ```

1. A aplicação irá apresentar várias mensagens de tempo de execução e deverá ver a mensagem `User: testFirstName testLastName` apresentada para indicar os valores foi armazenados e obtidos a base de dados com êxito.

   ![Resultados com êxito da aplicação][JV02]

1. OPCIONAL: Pode utilizar o portal do Azure para ver os conteúdos do seu BD do Cosmos Azure da página de propriedades para a base de dados clicando **Explorador de documentos**e, em seguida, selecionar e item da lista apresentada para ver os conteúdos.

   ![Utilizar o Explorador de documentos para ver os seus dados][JV03]

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre como utilizar a base de dados do Azure Cosmos e Java, consulte os artigos seguintes:

* [Documentação do Azure Cosmos DB].

* [Azure Cosmos DB: Criar uma aplicação de API do DocumentDB com o Java e o portal do Azure][Build a DocumentDB API app with Java]

Para obter mais informações sobre como utilizar aplicações de arranque de mola no Azure, consulte os artigos seguintes:

* [Mola arranque DocumenDB arranque para o Azure](https://github.com/Microsoft/azure-spring-boot-starters/tree/master/azure-documentdb-spring-boot-starter-sample)

* [Implementar uma aplicação de arranque mola para o App Service do Azure](../app-service/app-service-deploy-spring-boot-web-app-on-azure.md)

* [Executar uma aplicação de arranque de mola num Cluster Kubernetes no serviço de contentor do Azure](../container-service/container-service-deploy-spring-boot-app-on-kubernetes.md)

Para obter mais informações sobre como utilizar o Azure com o Java, veja o [Centro de Programadores de Java do Azure] e [Java Tools for Visual Studio Team Services] (Ferramentas de Java para o Visual Studio Team Services).

<!-- URL List -->

[Documentação do Azure Cosmos DB]: /azure/cosmos-db/
[Centro de Programadores de Java do Azure]: https://azure.microsoft.com/develop/java/
[Build a DocumentDB API app with Java]: https://docs.microsoft.com/azure/cosmos-db/create-documentdb-java
[conta do Azure gratuita]: https://azure.microsoft.com/pricing/free-trial/
[Java Tools for Visual Studio Team Services]: https://java.visualstudio.com/
[benefícios de subscritor do MSDN]: https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/
[mola arranque]: http://projects.spring.io/spring-boot/
[mola Initializr]: https://start.spring.io/
[mola Framework]: https://spring.io/

<!-- IMG List -->

[AZ01]: ./media/documentdb-java-spring-boot-starter-with-cosmos-db/AZ01.png
[AZ02]: ./media/documentdb-java-spring-boot-starter-with-cosmos-db/AZ02.png
[AZ03]: ./media/documentdb-java-spring-boot-starter-with-cosmos-db/AZ03.png
[AZ04]: ./media/documentdb-java-spring-boot-starter-with-cosmos-db/AZ04.png
[AZ05]: ./media/documentdb-java-spring-boot-starter-with-cosmos-db/AZ05.png

[SI01]: ./media/documentdb-java-spring-boot-starter-with-cosmos-db/SI01.png
[SI02]: ./media/documentdb-java-spring-boot-starter-with-cosmos-db/SI02.png
[SI03]: ./media/documentdb-java-spring-boot-starter-with-cosmos-db/SI03.png

[RE01]: ./media/documentdb-java-spring-boot-starter-with-cosmos-db/RE01.png
[RE02]: ./media/documentdb-java-spring-boot-starter-with-cosmos-db/RE02.png

[PM01]: ./media/documentdb-java-spring-boot-starter-with-cosmos-db/PM01.png
[PM02]: ./media/documentdb-java-spring-boot-starter-with-cosmos-db/PM02.png

[JV01]: ./media/documentdb-java-spring-boot-starter-with-cosmos-db/JV01.png
[JV02]: ./media/documentdb-java-spring-boot-starter-with-cosmos-db/JV02.png
[JV03]: ./media/documentdb-java-spring-boot-starter-with-cosmos-db/JV03.png
