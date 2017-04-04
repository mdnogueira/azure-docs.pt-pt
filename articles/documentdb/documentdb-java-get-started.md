---
title: 'Tutorial NoSQL: SDK do Java Azure DocumentDB | Microsoft Docs'
description: "Um tutorial NoSQL que cria uma base de dados online e uma aplicação de consola Java com o SDK do Java DocumentDB. O Azure DocumentDB é uma base de dados NoSQL para JSON."
keywords: "tutorial nosql, base de dados online, aplicação de consola java"
services: documentdb
documentationcenter: Java
author: arramac
manager: jhubbard
editor: monicar
ms.assetid: 75a9efa1-7edd-4fed-9882-c0177274cbb2
ms.service: documentdb
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: java
ms.topic: hero-article
ms.date: 01/05/2017
ms.author: arramac
translationtype: Human Translation
ms.sourcegitcommit: 503f5151047870aaf87e9bb7ebf2c7e4afa27b83
ms.openlocfilehash: da7907ffc515ea2e3040075c93bcd53840cf3ff5
ms.lasthandoff: 03/28/2017


---
# <a name="nosql-tutorial-build-a-documentdb-java-console-application"></a>Tutorial NoSQL: criar uma aplicação de consola Java DocumentDB
> [!div class="op_single_selector"]
> * [.NET](documentdb-get-started.md)
> * [.NET Core](documentdb-dotnetcore-get-started.md)
> * [Node.js para MongoDB](documentdb-mongodb-samples.md)
> * [Node.js](documentdb-nodejs-get-started.md)
> * [Java](documentdb-java-get-started.md)
> * [C++](documentdb-cpp-get-started.md)
>  
> 

Bem-vindo ao tutorial NoSQL para o SDK Java do Azure DocumentDB! Depois de seguir este tutorial, terá de uma aplicação de consola que cria e consulta recursos do DocumentDB.

Abordamos:

* Criação e ligação a uma conta DocumentDB
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

* Uma conta ativa do Azure. Se não tiver uma, pode inscrever-se numa [conta gratuita](https://azure.microsoft.com/free/). Em alternativa, pode utilizar o [Emulador do Azure DocumentDB](documentdb-nosql-local-emulator.md) para este tutorial.
* [Git](https://git-scm.com/downloads)
* [Kit de desenvolvimento do Java (JDK) 7 +](http://www.oracle.com/technetwork/java/javase/downloads/index.html).
* [Maven](http://maven.apache.org/download.cgi).

## <a name="step-1-create-a-documentdb-account"></a>Passo 1: Criar uma conta DocumentDB
Criemos uma conta DocumentDB. Se já tiver uma conta que pretende utilizar, pode avançar diretamente para [Clonar o projeto GitHub](#GitClone). Se estiver a utilizar o Emulador do DocumentDB, siga os passos em [Azure DocumentDB Emulator (Emulador do Azure DocumentDB)](documentdb-nosql-local-emulator.md) para configurar o emulador e avance para [Clonar o projeto GitHub](#GitClone).

[!INCLUDE [documentdb-create-dbaccount](../../includes/documentdb-create-dbaccount.md)]

## <a id="GitClone"></a>Passo 2: Clonar o projeto GitHub
Pode começar clonando o repositório do GitHub para [Get Started with DocumentDB and Java (Começar a utilizar o DocumentDB e Java)](https://github.com/Azure-Samples/documentdb-java-getting-started). Por exemplo, a partir de um diretório local, execute o seguinte para obter o projeto exemplo localmente.

    git clone git@github.com:Azure-Samples/documentdb-java-getting-started.git

    cd documentdb-java-getting-started

O diretório contém um `pom.xml` para o projeto e uma pasta `src` que contém o código fonte de Java, incluindo `Program.java` que mostra como executar operações simples com o Azure DocumentDB, como criar documentos e consultar dados dentro de uma coleção. O `pom.xml` inclui uma dependência no [DocumentDB Java SDK no Maven](https://mvnrepository.com/artifact/com.microsoft.azure/azure-documentdb).

    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-documentdb</artifactId>
        <version>LATEST</version>
    </dependency>

## <a id="Connect"></a>Passo 3: Ligar a uma conta do DocumentDB
Em seguida, regresse ao [Portal do Azure](https://portal.azure.com) para obter o seu ponto final e a chave mestra primária. O ponto final do DocumentDB e a chave primária são necessários para que a sua aplicação saiba onde ligar e para que o DocumentDB confie na ligação da sua aplicação.

No Portal do Azure, navegue até à sua conta do DocumentDB e clique em **Chaves**. Copie o URI do portal e cole-o em `<your endpoint URI>`, no ficheiro Program.java. Em seguida, copie A CHAVE PRIMÁRIA do portal e cole-a em `<your key>`.

    this.client = new DocumentClient(
        "<your endpoint URI>",
        "<your key>"
        , new ConnectionPolicy(),
        ConsistencyLevel.Session);

![Captura de ecrã do Portal do Azure utilizado pelo tutorial NoSQL para criar uma aplicação de consola Java. Mostra uma conta DocumentDB, com o ACTIVE hub realçado, o botão CHAVES realçado no painel de conta DocumentDB e os valores URI, CHAVE PRIMÁRIA e CHAVE SECUNDÁRIA realçados no painel de Chaves][keys]

## <a name="step-4-create-a-database"></a>Passo 4: Criar uma base de dados
Pode criar a sua [base de dados](documentdb-resources.md#databases) do DocumentDB através do método [createDatabase](http://azure.github.io/azure-documentdb-java/com/microsoft/azure/documentdb/DocumentClient.html#createDatabase-com.microsoft.azure.documentdb.Database-com.microsoft.azure.documentdb.RequestOptions-) da classe **DocumentClient**. Uma base de dados é o contentor lógico do armazenamento de documentos JSON particionado em coleções.

    Database database = new Database();
    database.setId("familydb");
    this.client.createDatabase(database, null);

## <a id="CreateColl"></a>Passo 5: Criar uma coleção
> [!WARNING]
> **createCollection** cria uma nova coleção com débito reservado, tendo repercussões sobre os preços. Para obter mais detalhes, visite a nossa [página de preços](https://azure.microsoft.com/pricing/details/documentdb/).
> 
> 

Pode criar uma [coleção](documentdb-resources.md#collections) utilizando o método [createCollection](http://azure.github.io/azure-documentdb-java/com/microsoft/azure/documentdb/DocumentClient.html#createCollection-java.lang.String-com.microsoft.azure.documentdb.DocumentCollection-com.microsoft.azure.documentdb.RequestOptions-) da classe **DocumentClient**. Uma coleção é um contentor de documentos JSON e a lógica da aplicação associada JavaScript.


    DocumentCollection collectionInfo = new DocumentCollection();
    collectionInfo.setId("familycoll");

    // DocumentDB collections can be reserved with throughput specified in request units/second. 
    // Here we create a collection with 400 RU/s.
    RequestOptions requestOptions = new RequestOptions();
    requestOptions.setOfferThroughput(400);

    this.client.createCollection("/dbs/familydb", collectionInfo, requestOptions);

## <a id="CreateDoc"></a>Passo 6: Criar documentos JSON
Pode criar um [documento](documentdb-resources.md#documents) utilizando o método [createDocument](http://azure.github.io/azure-documentdb-java/com/microsoft/azure/documentdb/DocumentClient.html#createDocument-java.lang.String-java.lang.Object-com.microsoft.azure.documentdb.RequestOptions-boolean-) da classe **DocumentClient**. Os documentos são conteúdos JSON (arbitrários) definidos pelo utilizador. Podemos agora inserir um ou mais documentos. Se já tiver dados que pretende armazenar na sua base de dados, pode utilizar a [ferramenta de Migração de Dados](documentdb-import-data.md) do DocumentDB, para importar os dados para uma base de dados.

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

## <a id="Query"></a>Passo 7: Consultar os recursos do DocumentDB
O DocumentDB suporta [consultas](documentdb-sql-query.md) extensas contra documentos JSON armazenados em cada coleção.  O código de exemplo seguinte mostra como consultar documentos no DocumentDB utilizando sintaxe SQL com o método [queryDocuments](http://azure.github.io/azure-documentdb-java/com/microsoft/azure/documentdb/DocumentClient.html#queryDocuments-java.lang.String-com.microsoft.azure.documentdb.SqlQuerySpec-com.microsoft.azure.documentdb.FeedOptions-).

    FeedResponse<Document> queryResults = this.client.queryDocuments(
        "/dbs/familydb/colls/familycoll",
        "SELECT * FROM Family WHERE Family.lastName = 'Andersen'", 
        null);

    System.out.println("Running SQL query...");
    for (Document family : queryResults.getQueryIterable()) {
        System.out.println(String.format("\tRead %s", family));
    }

## <a id="ReplaceDocument"></a>Passo 8: Substituir um documento JSON
O DocumentDB suporta a atualização de documentos JSON com o método [replaceDocument](http://azure.github.io/azure-documentdb-java/com/microsoft/azure/documentdb/DocumentClient.html#replaceDocument-com.microsoft.azure.documentdb.Document-com.microsoft.azure.documentdb.RequestOptions-).

    // Update a property
    andersenFamily.Children[0].Grade = 6;

    this.client.replaceDocument(
        "/dbs/familydb/colls/familycoll/docs/Andersen.1", 
        andersenFamily,
        null);

## <a id="DeleteDocument"></a>Passo 9: Eliminar um documento JSON
Da mesma forma, o DocumentDB suporta a eliminação de documentos JSON com o método [deleteDocument](http://azure.github.io/azure-documentdb-java/com/microsoft/azure/documentdb/DocumentClient.html#deleteDocument-java.lang.String-com.microsoft.azure.documentdb.RequestOptions-).  

    this.client.delete("/dbs/familydb/colls/familycoll/docs/Andersen.1", null);

## <a id="DeleteDatabase"></a>Passo 10: Eliminar a base de dados
Eliminar a base de dados criada remove a base de dados e todos os recursos subordinados (coleções, documentos, etc.).

    this.client.deleteDatabase("/dbs/familydb", null);

## <a id="Run"></a>Passo 11: Executar a sua aplicação de consola Java em conjunto!
Para executar a aplicação a partir da consola, compile primeiro com o Maven:
    
    mvn package

Executar `mvn package` transfere a biblioteca do DocumentDB mais recente a partir do Maven e produz `GetStarted-0.0.1-SNAPSHOT.jar`. Em seguida, execute a aplicação executando:

    mvn exec:java -D exec.mainClass=GetStarted.Program

Parabéns! Concluiu este tutorial NoSQL e a sua aplicação de consola Java está a funcionar!

## <a name="next-steps"></a>Passos seguintes
* Quer um tutorial de aplicação Web de Java? Veja [Criar uma aplicação Web de Java utilizando o DocumentDB](documentdb-java-application.md).
* Saiba como [monitorizar uma conta DocumentDB](documentdb-monitor-accounts.md).
* Execute consultas no nosso conjunto de dados de exemplo no [Query Playground](https://www.documentdb.com/sql/demo).
* Saiba mais sobre o modelo de programação na secção Desenvolver da [página de documentação do DocumentDB](https://azure.microsoft.com/documentation/services/documentdb/).

[documentdb-create-account]: documentdb-create-account.md
[keys]: media/documentdb-get-started/nosql-tutorial-keys.png

