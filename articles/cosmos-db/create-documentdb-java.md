---
title: Criar uma base de dados de documentos do Azure Cosmos DB com Java | Microsoft Docs | Microsoft Docs
description: "Apresenta um exemplo de código Java que pode utilizar para ligar e consultar a Azure Cosmos DB DocumentDB API"
services: cosmos-db
documentationcenter: 
author: mimig1
manager: jhubbard
editor: 
ms.assetid: 89ea62bb-c620-46d5-baa0-eefd9888557c
ms.service: cosmos-db
ms.custom: quick start connect, mvc, devcenter
ms.workload: 
ms.tgt_pltfrm: na
ms.devlang: java
ms.topic: quickstart
ms.date: 11/20/2017
ms.author: mimig
ms.openlocfilehash: b36de6bce597569b4e1eaa615860acdf28dfa798
ms.sourcegitcommit: 4ea06f52af0a8799561125497f2c2d28db7818e7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/21/2017
---
# <a name="azure-cosmos-db-create-a-document-database-using-java-and-the-azure-portal"></a>Azure Cosmos DB: Criar uma base de dados de documentos com Java e o portal do Azure

O Azure Cosmos DB é um serviço de bases de dados com vários modelos e distribuído globalmente da Microsoft. Utilizar base de dados do Azure Cosmos, pode rapidamente criar e consultar documento gerido, tabela e bases de dados do gráfico.

Este guia de início rápido cria uma base de dados de documentos através de ferramentas do portal do Azure para o Azure Cosmos DB. Este guia de início rápido mostra também como criar rapidamente uma aplicação de consola de Java através da [DocumentDB Java API](documentdb-sdk-java.md). As instruções deste guia de início rápido podem ser seguidas em qualquer sistema operativo capaz de executar Java. Por concluir este guia de introdução estará familiarizado com a criar e modificar recursos de base de dados de documento na IU ou através de programação, optando-se a sua preferência.

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)] 
[!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

Além disso: 

* [Java Development Kit (JDK) 1.7+](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html)
    * No Ubuntu, execute `apt-get install default-jdk` para instalar o JDK.
    * Certifique-se de que define a variável de ambiente JAVA_HOME para apontar para a pasta onde está instalado o JDK.
* [Transferir](http://maven.apache.org/download.cgi) e [instalar](http://maven.apache.org/install.html) um arquivo binário [Maven](http://maven.apache.org/)
    * No Ubuntu, pode executar `apt-get install maven` para instalar o Maven.
* [Git](https://www.git-scm.com/)
    * No Ubuntu, pode executar `sudo apt-get install git` para instalar o Git.

## <a name="create-a-database-account"></a>Criar uma conta de base de dados

Antes de poder criar uma base de dados de documentos, tem de criar uma conta de base de dados SQL (DocumentDB) com o Azure Cosmos DB.

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

## <a name="add-a-collection"></a>Adicionar uma coleção

[!INCLUDE [cosmos-db-create-collection](../../includes/cosmos-db-create-collection.md)]

<a id="add-sample-data"></a>
## <a name="add-sample-data"></a>Adicionar dados de exemplo

Pode agora utilizar o Data Explorer para adicionar dados à sua coleção nova.

1. Expanda o **itens** coleção, clique em **documentos** > **novo documento**.

   ![Criar documentos novos no Data Explorer no portal do Azure](./media/create-documentdb-java/azure-cosmosdb-data-explorer-new-document.png)
  
2. Agora, adicione um documento para a coleção com a estrutura seguinte e clique **guardar**.

     ```json
     {
         "id": "1",
         "category": "personal",
         "name": "groceries",
         "description": "Pick up apples and strawberries.",
         "isComplete": false
     }
     ```

    ![Copie os dados json e clique em Guardar no Data Explorer no portal do Azure](./media/create-documentdb-java/azure-cosmosdb-data-explorer-save-document.png)

3.  Criar e guardar um documento mais onde alterar `id` 2 e alterar as outras propriedades como ver ajustam. Agora, os documentos podem ter qualquer estrutura que queira criar, uma vez que o Azure Cosmos DB não impõe qualquer esquema aos seus dados.

## <a name="query-your-data"></a>Consultar os dados

Agora, pode utilizar consultas no Explorador de dados para obter e filtre os dados.

1. Consulte o artigo que, por predefinição, a consulta está definida para `SELECT * FROM c`. Esta consulta predefinida obtém e apresenta todos os documentos na coleção. 

    ![A consulta no Explorador de dados predefinido é "SELECIONAR * do c'](./media/create-documentdb-java/azure-cosmosdb-data-explorer-query.png)

2. Alterar a consulta, clicando a **Editar filtro** no botão Adicionar `ORDER BY c._ts DESC` para a caixa de predicado de consulta e, em seguida, clicando em **aplicar filtro**.

    ![Alterar a consulta predefinida adicionando ORDER BY c. TS DESC e clicar em filtro aplicar](./media/create-documentdb-java/azure-cosmosdb-data-explorer-edit-query.png)

Alterar esta definição de listas de consulta os documentos por ordem, com base no respetivo carimbo de hora, por isso, agora o segundo documento descendente está listado primeiro. Se estiver familiarizado com a sintaxe do SQL Server, pode introduzir qualquer um dos suportado [as consultas SQL](documentdb-sql-query.md) nesta caixa. 

Que conclui o nosso trabalho no Explorador de dados. Antes de iremos avançar para trabalhar com o código, tenha em atenção que também pode utilizar o Explorador de dados para criar procedimentos armazenados, UDFs e acionadores para efetuar a lógica de negócio do lado do servidor, bem como aumentar o débito. O Data Explorer expõe todos os acessos a dados programáticos incorporados que estão disponíveis nas APIs, mas disponibiliza acesso fácil aos seus dados no portal do Azure.

## <a name="clone-the-sample-application"></a>Clonar a aplicação de exemplo

Agora, vamos trabalhar com código. Vamos clonar uma aplicação de DocumentDB API a partir do GitHub, definir a cadeia de ligação e executá-la. Vai ver como é fácil trabalhar com dados programaticamente. 

1. Abra uma linha de comandos, crie uma nova pasta designada git-samples e, em seguida, feche a linha de comandos.

    ```bash
    md "C:\git-samples"
    ```

2. Abra uma janela de terminal do git, tais como o git bash e utilize o `cd` comando para alterar para a nova pasta para instalar a aplicação de exemplo. 

    ```bash
    cd "C:\git-samples"
    ```

3. Execute o seguinte comando para clonar o repositório de exemplo. Este comando cria uma cópia da aplicação de exemplo no seu computador.

    ```bash
    git clone https://github.com/Azure-Samples/azure-cosmos-db-documentdb-java-getting-started.git
    ```

## <a name="review-the-code"></a>Rever o código

Este passo é opcional. Se estiver interessado em aprender a forma como os recursos de base de dados são criados no código, pode rever os seguintes fragmentos. Os fragmentos são obtidos a partir do `Program.java` ficheiro instalado na pasta C:\git-samples\azure-cosmos-db-documentdb-java-getting-started\src\GetStarted. Caso contrário, pode avançar diretamente para [atualizar a cadeia de ligação](#update-your-connection-string). 

* `DocumentClient`inicialização. O [DocumentClient](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb._document_client) fornece representação lógica do lado do cliente para o serviço de base de dados de base de dados do Azure Cosmos. Este cliente é utilizado para configurar e executar pedidos contra o serviço.

    ```java
    this.client = new DocumentClient("https://FILLME.documents.azure.com",
            "FILLME", 
            new ConnectionPolicy(),
            ConsistencyLevel.Session);
    ```

* [Base de dados](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb._database) criação.

    ```java
    Database database = new Database();
    database.setId(databaseName);
    
    this.client.createDatabase(database, null);
    ```

* [DocumentCollection](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb._document_collection) criação.

    ```java
    DocumentCollection collectionInfo = new DocumentCollection();
    collectionInfo.setId(collectionName);

    ...

    this.client.createCollection(databaseLink, collectionInfo, requestOptions);
    ```

* Criação de documentos utilizando o [createDocument](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb._document_client.createdocument) método.

    ```java
    // Any Java object within your code can be serialized into JSON and written to Azure Cosmos DB
    Family andersenFamily = new Family();
    andersenFamily.setId("Andersen.1");
    andersenFamily.setLastName("Andersen");
    // More properties

    String collectionLink = String.format("/dbs/%s/colls/%s", databaseName, collectionName);
    this.client.createDocument(collectionLink, family, new RequestOptions(), true);
    ```

* As consultas SQL através de JSON são efetuadas utilizando o [queryDocuments](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb._document_client.querydocuments) método.

    ```java
    FeedOptions queryOptions = new FeedOptions();
    queryOptions.setPageSize(-1);
    queryOptions.setEnableCrossPartitionQuery(true);

    String collectionLink = String.format("/dbs/%s/colls/%s", databaseName, collectionName);
    FeedResponse<Document> queryResults = this.client.queryDocuments(
        collectionLink,
        "SELECT * FROM Family WHERE Family.lastName = 'Andersen'", queryOptions);

    System.out.println("Running SQL query...");
    for (Document family : queryResults.getQueryIterable()) {
        System.out.println(String.format("\tRead %s", family));
    }
    ```    

## <a name="update-your-connection-string"></a>Atualizar a cadeia de ligação

Agora, regresse ao portal do Azure para obter as informações da cadeia de ligação e copie-as para a aplicação. Isto permite que a aplicação comunicar com a base de dados alojada.

1. No [portal do Azure](http://portal.azure.com/), clique em **chaves**. 

    Utilize os botões de cópia no lado direito do ecrã para copiar o valor superior, o URI.

    ![Ver e copiar uma chave de acesso na página do portal, as chaves do Azure](./media/create-documentdb-java/keys.png)

2. Abra o `Program.java` ficheiro a partir da pasta C:\git-samples\azure-cosmos-db-documentdb-java-getting-started\src\GetStarted. 

3. Colar o valor do URI do portal através de `https://FILLME.documents.azure.com` linha 45.

4. Volte ao portal e copie o valor de chave primária, conforme mostrado na captura de ecrã. Colar o valor de chave primária do portal através de `FILLME` linha 46.

    O método getStartedDemo deve agora ter um aspeto semelhante a isto: 
    
    ```java
    private void getStartedDemo() throws DocumentClientException, IOException {
        this.client = new DocumentClient("https://youraccountname.documents.azure.com:443/",
                "your-primary-key...RJhQrqQ5QQ==", 
                new ConnectionPolicy(),
                ConsistencyLevel.Session);
    ```

5. Guarde o ficheiro Program.java.

## <a name="run-the-app"></a>Executar a aplicação

1. Na janela de terminal do git, `cd` na pasta azure-cosmos-db-documentdb-java-getting-started.

    ```git
    cd "C:\git-samples\azure-cosmos-db-documentdb-java-getting-started"
    ```

2. Na janela de terminal do git, utilize o seguinte comando para instalar os pacotes necessários do Java.

    ```
    mvn package
    ```

3. Na janela de terminal do git, utilize o seguinte comando para iniciar a aplicação de Java.

    ```
    mvn exec:java -D exec.mainClass=GetStarted.Program
    ```

    A janela de terminal apresenta uma notificação de que a base de dados FamilyDB foi criado. 
    
4. Prima uma tecla para criar a coleção. 

5. Voltar ao Explorador de dados e verá que agora contém uma base de dados FamilyDB.
    
6. Continue a prima chaves na janela da consola com o código criar documentos e executar uma consulta.
    
    No final do programa, todos os recursos desta aplicação são eliminados da sua conta para que não cobrado qualquer custo. 

    ![Saída da consola](./media/create-documentdb-java/console-output.png)


## <a name="review-slas-in-the-azure-portal"></a>Rever os SLAs no portal do Azure

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>Limpar recursos

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>Passos seguintes

Neste guia de início rápido, aprendeu a criar uma conta do Azure Cosmos DB, uma base de dados de documentos e uma coleção com o Data Explorer, bem como a executar uma aplicação para fazer o mesmo programaticamente. Agora pode importar dados adicionais para a coleção de BD do Cosmos do Azure. 

> [!div class="nextstepaction"]
> [Import data into Azure Cosmos DB](import-data.md) (Importar dados para o Azure Cosmos DB).


