---
title: "Criar uma aplicação Java do Azure Cosmos DB com a Graph API | Microsoft Docs"
description: "Apresenta um exemplo de código Java que pode utilizar para ligar e consultar dados de gráficos no Azure Cosmos DB com Gremlin."
services: cosmosdb
documentationcenter: 
author: mimig1
manager: jhubbard
editor: 
ms.assetid: daacbabf-1bb5-497f-92db-079910703046
ms.service: cosmosdb
ms.custom: quick start connect
ms.workload: 
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: hero-article
ms.date: 05/10/2017
ms.author: arramac
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 8279ffc8dc69f0899ad7b5d3a528393fc2165b77
ms.contentlocale: pt-pt
ms.lasthandoff: 05/10/2017


---
# <a name="azure-cosmos-db-build-a-java-application-using-the-graph-api"></a>Azure Cosmos DB: criar uma aplicação Java com a Graph API

O Azure Cosmos DB é um serviço de bases de dados com vários modelos e distribuído globalmente. Pode criar e consultar rapidamente o documento, a chave/valor e as bases de dados de gráficos, que beneficiam de capacidades de escalamento horizontal e distribuição global no centro do Azure Cosmos DB. 

Este guia de introdução demonstra como criar uma conta do Azure Cosmos DB para o Graph API (pré-visualização), bases de dados e gráficos com o portal do Azure. Depois, vai criar e executar uma aplicação de consola com o controlador [Gremlin Java](https://mvnrepository.com/artifact/com.tinkerpop.gremlin/gremlin-java) de OSS.  

## <a name="prerequisites"></a>Pré-requisitos

* Antes de poder executar este exemplo, tem de ter os pré-requisitos seguintes:
   * JDK 1.7+ (Execute `apt-get install default-jdk` se não tiver o JDK)
   * Maven (Execute `apt-get install maven` se não tiver o Maven)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-a-database-account"></a>Criar uma conta de base de dados

[!INCLUDE [cosmosdb-create-dbaccount-graph](../../includes/cosmosdb-create-dbaccount-graph.md)]

## <a name="add-a-graph"></a>Adicionar um gráfico

[!INCLUDE [cosmosdb-create-graph](../../includes/cosmosdb-create-graph.md)]

## <a name="clone-the-sample-application"></a>Clonar a aplicação de exemplo

Agora, vamos clonar uma aplicação do Graph API (pré-visualização) a partir do GitHub, definir a cadeia de ligação e executá-la. Vai ver como é fácil trabalhar com dados programaticamente. 

1. Abra uma janela de terminal do git, tal como git bash, e `cd` para um diretório de trabalho.  

2. Execute o seguinte comando para clonar o repositório de exemplo. 

    ```bash
    git clone https://github.com/Azure-Samples/azure-cosmos-db-graph-java-getting-started.git
    ```

## <a name="review-the-code"></a>Rever o código

Vamos fazer uma breve revisão do que está a acontecer à aplicação. Abra o ficheiro `Program.java` e irá encontrar estas linhas de código. 

* O `Client` Gremlin é inicializado na configuração em `src/remote-secure.yaml` que definiu anteriormente.

    ```java
    Cluster cluster = Cluster.build(new File("src/remote.yaml")).create();
    
    Client client = cluster.connect();
    ```

* São executados uma série de passos do Gremlin com o método `client.submit`.

    ```java
    ResultSet results = client.submit("g.V()");

    CompletableFuture<List<Result>> completableFutureResults = results.all();
    List<Result> resultList = completableFutureResults.get();

    for (Result result : resultList) {
        System.out.println(result.toString());
    }
    ```
## <a name="update-your-connection-string"></a>Atualizar a cadeia de ligação

Agora, regresse ao portal do Azure para obter as informações da cadeia de ligação e copie-as para a aplicação.

1. No [portal do Azure](http://portal.azure.com/), na sua conta do Azure Cosmos DB, na navegação da esquerda, clique em **Chaves** e em **Chaves de leitura/escrita**. Vai utilizar os botões de copiar no lado direito do ecrã para copiar o URI e a Chave Primária para o ficheiro `Program.java` no próximo passo.

    ![Ver e copiar uma chave de acesso no portal do Azure, painel Chaves](./media/create-documentdb-dotnet/keys.png)

2. Abra o ficheiro `src/remote-secure.yaml`. 

3. Preencha as configurações de *anfitrião*, *porta*, *nome de utilizador*, *palavra-passe*, *connectionPool* e *serializador* no ficheiro `src/remote-secure.yaml`:

    Definição|Valor sugerido|Descrição
    ---|---|---
    Anfitriões|***.graphs.azure.com|O URI do seu serviço de gráficos, que pode obter no portal do Azure
    Porta|443|Defina como 443
    Nome de utilizador|*O seu nome de utilizador*|O recurso com a forma `/dbs/<db>/colls/<coll>`.
    Palavra-passe|*A chave mestra principal*|A chave mestra principal do Azure Cosmos DB
    ConnectionPool|{enableSsl: true}|A definição de conjunto de ligações para SSL
    Serializador|{ className:org.apache.tinkerpop.gremlin.<br>driver.ser.GraphSONMessageSerializerV1d0,<br> config: { serializeResultToString: true }}|Defina como este valor

## <a name="run-the-console-app"></a>Executar a aplicação de consola

1. Execute `mvn package` num terminal para instalar os módulos npm necessários

2. Execute `mvn exec:java -D exec.mainClass=GetStarted.Program` num terminal para iniciar a aplicação Java.

Agora, pode voltar ao Data Explorer e ver, consultar, modificar e trabalhar com estes dados novos. 

## <a name="browse-using-the-data-explorer"></a>Utilizar o Data Explorer para pesquisar

Agora, pode voltar ao Data Explorer no portal do Azure e procurar e consultar os dados do gráfico novo.

* No Data Explorer, a base de dados nova aparece no painel Coleções. Expanda **graphdb**, **graphcoll** e clique em **Gráfico**.

    Os dados gerados pela aplicação de exemplo são apresentados no painel Gráficos.

## <a name="review-slas-in-the-azure-portal"></a>Rever os SLAs no portal do Azure

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmosdb-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>Limpar recursos

Se não pretender continuar a utilizar esta aplicação, elimine todos os recursos criados com este guia de introdução no portal do Azure com os seguintes passos: 

1. No menu do lado esquerdo do portal do Azure, clique em **Grupos de recursos** e, em seguida, clique no nome de recurso que criou. 
2. Na página do grupo de recursos, clique em **Eliminar**, escreva o nome do recurso a eliminar na caixa de texto e, em seguida, clique em **Eliminar**.

## <a name="next-steps"></a>Passos seguintes

Neste guia rápido, aprendeu a criar uma conta do Azure Cosmos DB, a criar um gráfico com o Data Explorer e a executar uma aplicação. Agora, pode criar consultas mais complexas e implementar lógica poderosa para percorrer gráficos com Gremlin. 

> [!div class="nextstepaction"]
> [Query using Gremlin](tutorial-query-graph.md) (Utilizar Gremlin para consultar)


