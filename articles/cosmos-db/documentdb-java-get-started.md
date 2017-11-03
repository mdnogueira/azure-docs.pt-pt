---
title: 'NoSQL tutorial: DocumentDB API para o Azure Cosmos DB Java SDK | Microsoft Docs'
description: "Um tutorial NoSQL que cria uma base de dados online e a aplicação de consola Java utilizando a API do DocumentDB para a base de dados do Azure Cosmos. O Azure DocumentDB é uma base de dados NoSQL para JSON."
keywords: "tutorial nosql, base de dados online, aplicação de consola java"
services: cosmos-db
documentationcenter: Java
author: arramac
manager: jhubbard
editor: monicar
ms.assetid: 75a9efa1-7edd-4fed-9882-c0177274cbb2
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: java
ms.topic: article
ms.date: 05/22/2017
ms.author: arramac
ms.openlocfilehash: ea667a889bc6eaed08619ee5e141d8e3e9add7bf
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="nosql-tutorial-build-a-documentdb-api-java-console-application"></a>NoSQL tutorial: criar uma aplicação de consola Java de API do DocumentDB
> [!div class="op_single_selector"]
> * [.NET](documentdb-get-started.md)
> * [.NET Core](documentdb-dotnetcore-get-started.md)
> * [Node.js para MongoDB](mongodb-samples.md)
> * [Node.js](documentdb-nodejs-get-started.md)
> * [Java](documentdb-java-get-started.md)
> * [C++](documentdb-cpp-get-started.md)
>  
> 

Bem-vindo ao NoSQL tutorial para a API de DocumentDB do Azure Cosmos DB Java SDK! Depois de seguir este tutorial, terá de uma aplicação de consola que cria e consulta recursos do Cosmos DB.

Abordamos:

* Criar e ligar a uma conta do Azure Cosmos DB
* Configuração da sua Solução Visual Studio
* Criação de uma base de dados online
* Criação de uma coleção
* Criação de documentos JSON
* Consulta da coleção
* Criação de documentos JSON
* Consulta da coleção
* Substituir um documento
* Eliminar um documento
* Eliminar a base de dados

Agora comecemos!

## <a name="prerequisites"></a>Pré-requisitos
Certifique-se de que tem o seguinte:

* Uma conta ativa do Azure. Se não tiver uma, pode inscrever-se numa [conta gratuita](https://azure.microsoft.com/free/). 

  [!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

* [Git](https://git-scm.com/downloads).
* [Kit de desenvolvimento do Java (JDK) 7 +](http://www.oracle.com/technetwork/java/javase/downloads/index.html).
* [Maven](http://maven.apache.org/download.cgi).

## <a name="step-1-create-an-azure-cosmos-db-account"></a>Passo 1: Criar uma conta do Azure Cosmos DB
Vamos criar uma conta do Azure Cosmos DB. Se já tiver uma conta que pretende utilizar, pode avançar diretamente para [Clonar o projeto GitHub](#GitClone). Se estiver a utilizar o Emulador do Azure Cosmos DB, siga os passos em [Azure Cosmos DB Emulator](local-emulator.md) (Emulador do Azure Cosmos DB) para configurar o emulador e avance para [Clonar o projeto GitHub](#GitClone).

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

## <a id="GitClone"></a>Passo 2: Clonar o projeto GitHub
Pode começar clonando o repositório do GitHub para [Get Started with Azure Cosmos DB and Java](https://github.com/Azure-Samples/documentdb-java-getting-started) (Começar a utilizar o Azure Cosmos DB e Java). Por exemplo, a partir de um diretório local, execute o seguinte para obter o projeto exemplo localmente.

    git clone git@github.com:Azure-Samples/azure-cosmos-db-documentdb-java-getting-started.git

    cd azure-cosmos-db-documentdb-java-getting-started

O diretório contém um `pom.xml` para o projeto e um `src` pasta que contém o código de origem de Java, incluindo `Program.java` que mostra como realizar operações simples com base de dados do Azure Cosmos como criar documentos e consultar os dados dentro de uma coleção . O `pom.xml` inclui uma dependência no [DocumentDB Java SDK no Maven](https://mvnrepository.com/artifact/com.microsoft.azure/azure-documentdb).

    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-documentdb</artifactId>
        <version>LATEST</version>
    </dependency>

## <a id="Connect"></a>Passo 3: Ligar a uma conta do Azure Cosmos DB
Em seguida, regresse ao [Portal do Azure](https://portal.azure.com) para obter o seu ponto final e a chave mestra primária. O ponto final e a chave primária do Azure Cosmos DB são necessários para que a sua aplicação saiba onde ligar e para que o Azure Cosmos DB confie na ligação da sua aplicação.

No Portal do Azure, navegue até à sua conta do Azure Cosmos DB e clique em **Chaves**. Copie o URI do portal e cole-o em `https://FILLME.documents.azure.com`, no ficheiro Program.java. Em seguida, copie A CHAVE PRIMÁRIA do portal e cole-a em `FILLME`.

    this.client = new DocumentClient(
        "https://FILLME.documents.azure.com",
        "FILLME"
        , new ConnectionPolicy(),
        ConsistencyLevel.Session);

![Captura de ecrã do Portal do Azure utilizado pelo tutorial NoSQL para criar uma aplicação de consola Java. Mostra uma conta do Azure Cosmos DB, com o ACTIVE hub realçado, o botão CHAVES realçado no painel de conta do Azure Cosmos DB e os valores URI, CHAVE PRIMÁRIA e CHAVE SECUNDÁRIA realçados no painel de Chaves][keys]

## <a name="step-4-create-a-database"></a>Passo 4: Criar uma base de dados
Pode criar a sua [base de dados](documentdb-resources.md#databases) do Azure Cosmos DB através do método [createDatabase](/java/api/com.microsoft.azure.documentdb._document_client.createdatabase) da classe **DocumentClient**. Uma base de dados é o contentor lógico do armazenamento de documentos JSON particionado em coleções.

    Database database = new Database();
    database.setId("familydb");
    this.client.createDatabase(database, null);

## <a id="CreateColl"></a>Passo 5: Criar uma coleção
> [!WARNING]
> **createCollection** cria uma nova coleção com débito reservado, tendo repercussões sobre os preços. Para obter mais detalhes, visite a nossa [página de preços](https://azure.microsoft.com/pricing/details/cosmos-db/).
> 
> 

Pode criar uma [coleção](documentdb-resources.md#collections) utilizando o método [createCollection](/java/api/com.microsoft.azure.documentdb._document_client.createcollection) da classe **DocumentClient**. Uma coleção é um contentor de documentos JSON e a lógica da aplicação associada JavaScript.


    DocumentCollection collectionInfo = new DocumentCollection();
    collectionInfo.setId("familycoll");

    // Azure Cosmos DB collections can be reserved with throughput specified in request units/second. 
    // Here we create a collection with 400 RU/s.
    RequestOptions requestOptions = new RequestOptions();
    requestOptions.setOfferThroughput(400);

    this.client.createCollection("/dbs/familydb", collectionInfo, requestOptions);

## <a id="CreateDoc"></a>Passo 6: Criar documentos JSON
Pode criar um [documento](documentdb-resources.md#documents) utilizando o método [createDocument](/java/api/com.microsoft.azure.documentdb._document_client.createdocument) da classe **DocumentClient**. Os documentos são conteúdos JSON (arbitrários) definidos pelo utilizador. Podemos agora inserir um ou mais documentos. Se já tiver dados que pretende armazenar na sua base de dados, pode utilizar da BD do Azure Cosmos [ferramenta de migração de dados](import-data.md) para importar os dados para uma base de dados.

    // Insert your Java objects as documents 
    Family andersenFamily = new Family();
    andersenFamily.setId("Andersen.1");
    andersenFamily.setLastName("Andersen")

    // More initialization skipped for brevity. You can have nested references
    andersenFamily.setParents(new Parent[] { parent1, parent2 });
    andersenFamily.setDistrict("WA5");
    Address address = new Address();
    address.setCity("Seattle");
    address.setCounty("King");
    address.setState("WA");

    andersenFamily.setAddress(address);
    andersenFamily.setRegistered(true);

    this.client.createDocument("/dbs/familydb/colls/familycoll", family, new RequestOptions(), true);

![Diagrama que ilustra a relação hierárquica entre a conta, a base de dados online, a coleção e os documentos utilizados pelo tutorial NoSQL na criação da aplicação de consola Java](./media/documentdb-get-started/nosql-tutorial-account-database.png)

## <a id="Query"></a>Passo 7: Consultar recursos do Azure Cosmos DB
O Azure Cosmos DB suporta [consultas](documentdb-sql-query.md) extensas de documentos JSON armazenados em cada coleção.  O código de exemplo seguinte mostra como consultar documentos no Azure Cosmos DB utilizando sintaxe SQL com o método [queryDocuments](/java/api/com.microsoft.azure.documentdb._document_client.querydocuments).

    FeedResponse<Document> queryResults = this.client.queryDocuments(
        "/dbs/familydb/colls/familycoll",
        "SELECT * FROM Family WHERE Family.lastName = 'Andersen'", 
        null);

    System.out.println("Running SQL query...");
    for (Document family : queryResults.getQueryIterable()) {
        System.out.println(String.format("\tRead %s", family));
    }

## <a id="ReplaceDocument"></a>Passo 8: Substituir um documento JSON
O Azure Cosmos DB suporta a atualização de documentos JSON com o método [replaceDocument](/java/api/com.microsoft.azure.documentdb._document_client.replacedocument).

    // Update a property
    andersenFamily.Children[0].Grade = 6;

    this.client.replaceDocument(
        "/dbs/familydb/colls/familycoll/docs/Andersen.1", 
        andersenFamily,
        null);

## <a id="DeleteDocument"></a>Passo 9: Eliminar um documento JSON
De forma semelhante, o Azure Cosmos DB suporta a eliminação de documentos JSON com o método [deleteDocument](/java/api/com.microsoft.azure.documentdb._document_client.deletedocument).  

    this.client.delete("/dbs/familydb/colls/familycoll/docs/Andersen.1", null);

## <a id="DeleteDatabase"></a>Passo 10: Eliminar a base de dados
Eliminar a base de dados criada remove a base de dados e todos os recursos subordinados (coleções, documentos, etc.).

    this.client.deleteDatabase("/dbs/familydb", null);

## <a id="Run"></a>Passo 11: Executar a sua aplicação de consola Java em conjunto!
Para executar a aplicação a partir da consola, navegue para a pasta do projeto e compilação com o Maven:
    
    mvn package

Executar `mvn package` transfere a biblioteca do Azure Cosmos DB mais recente a partir do Maven e produz `GetStarted-0.0.1-SNAPSHOT.jar`. Em seguida, execute a aplicação executando:

    mvn exec:java -D exec.mainClass=GetStarted.Program

Parabéns! Concluiu este tutorial NoSQL e a sua aplicação de consola Java está a funcionar!

## <a name="next-steps"></a>Passos seguintes
* Quer um tutorial de aplicação Web de Java? Veja [Criar uma aplicação Web de Java utilizando o Azure Cosmos DB](documentdb-java-application.md).
* Saiba como [monitorizar uma conta do Azure Cosmos DB](monitor-accounts.md).
* Execute consultas no nosso conjunto de dados de exemplo no [Query Playground](https://www.documentdb.com/sql/demo).
* Saiba mais sobre o modelo de programação na secção Desenvolver da [página de documentação do Azure Cosmos DB](https://azure.microsoft.com/documentation/services/documentdb/).

[keys]: media/documentdb-get-started/nosql-tutorial-keys.png
