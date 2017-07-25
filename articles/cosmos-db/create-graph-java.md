---
title: "Criar uma aplicação Java do Azure Cosmos DB com a Graph API | Microsoft Docs"
description: "Apresenta um exemplo de código Java que pode utilizar para ligar e consultar dados de gráficos no Azure Cosmos DB com Gremlin."
services: cosmos-db
documentationcenter: 
author: dennyglee
manager: jhubbard
editor: 
ms.assetid: daacbabf-1bb5-497f-92db-079910703046
ms.service: cosmos-db
ms.custom: quick start connect, mvc
ms.workload: 
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: hero-article
ms.date: 07/14/2017
ms.author: denlee
ms.translationtype: HT
ms.sourcegitcommit: c999eb5d6b8e191d4268f44d10fb23ab951804e7
ms.openlocfilehash: 8eac406c6ef96d7ae8dd5f4931c7d16edb723be8
ms.contentlocale: pt-pt
ms.lasthandoff: 07/17/2017

---
# <a name="azure-cosmos-db-build-a-java-application-using-the-graph-api"></a>Azure Cosmos DB: criar uma aplicação Java com a Graph API

O Azure Cosmos DB é um serviço de bases de dados com vários modelos e distribuído globalmente. Pode criar e consultar rapidamente o documento, a chave/valor e as bases de dados de gráficos, que beneficiam de capacidades de escalamento horizontal e distribuição global no centro do Azure Cosmos DB. 

Este guia de introdução demonstra como criar uma conta do Azure Cosmos DB para o Graph API (pré-visualização), bases de dados e gráficos com o portal do Azure. Depois, vai criar e executar uma aplicação de consola com o controlador [Gremlin Java](https://mvnrepository.com/artifact/org.apache.tinkerpop/gremlin-driver) de OSS.  

## <a name="prerequisites"></a>Pré-requisitos

* Antes de poder executar este exemplo, tem de ter os pré-requisitos seguintes:
   * JDK 1.7 + (execute `apt-get install default-jdk` se não tiver o JDK) e defina as variáveis de ambiente como `JAVA_HOME`
   * Maven (Execute `apt-get install maven` se não tiver o Maven)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-a-database-account"></a>Criar uma conta de base de dados

[!INCLUDE [cosmos-db-create-dbaccount-graph](../../includes/cosmos-db-create-dbaccount-graph.md)]

## <a name="add-a-graph"></a>Adicionar um gráfico

[!INCLUDE [cosmos-db-create-graph](../../includes/cosmos-db-create-graph.md)]

## <a name="clone-the-sample-application"></a>Clonar a aplicação de exemplo

Agora, vamos clonar uma aplicação do Graph API (pré-visualização) a partir do GitHub, definir a cadeia de ligação e executá-la. Vai ver como é fácil trabalhar com dados programaticamente. 

1. Abra uma janela de terminal do git, tal como git bash, e `cd` para um diretório de trabalho.  

2. Execute o seguinte comando para clonar o repositório de exemplo. 

    ```bash
    git clone https://github.com/Azure-Samples/azure-cosmos-db-graph-java-getting-started.git
    ```

## <a name="review-the-code"></a>Rever o código

Vamos fazer uma breve revisão do que está a acontecer à aplicação. Abra o ficheiro `Program.java` e irá encontrar estas linhas de código. 

* O Gremlin `Client` é inicializado na configuração em `src/remote.yaml`.

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

1. Abra o ficheiro src/remote.yaml. 

3. Preencha as configurações de *anfitrião*, *porta*, *nome de utilizador*, *palavra-passe*, *connectionPool* e *serializador* no ficheiro src/remote.yaml.

    Definição|Valor sugerido|Descrição
    ---|---|---
    Anfitriões|[***.graphs.azure.com]|Veja a captura de ecrã abaixo. Este é o valor de Gremlin URI na página Descrição Geral do portal do Azure, entre parênteses, com :443/ à direita removido.<br><br>Este valor também pode ser obtido a partir do separador Chaves, com o valor do URI, removendo https://, alterando os documentos para gráficos e removendo :443/ à direita.
    Porta|443|Defina como 443.
    Nome de utilizador|*O seu nome de utilizador*|O recurso do formulário `/dbs/<db>/colls/<coll>`, em que `<db>` é o nome da base de dados e `<coll>` é o nome da coleção.
    Palavra-passe|*A chave mestra principal*|Veja a segunda captura de ecrã abaixo. Esta é a chave primária, que pode ser obtida na página Chaves do portal do Azure, na caixa Chave Primária. Utilize o botão de copiar, no lado esquerdo da caixa, para copiar o valor.
    ConnectionPool|{enableSsl: true}|A definição do conjunto de ligações para SSL.
    Serializador|{ className: org.apache.tinkerpop.gremlin.<br>driver.ser.GraphSONMessageSerializerV1d0,<br> config: { serializeResultToString: true }}|Defina como este valor e elimine eventuais quebras de linha `\n` quando colar o valor.

    No valor Anfitriões, copie o valor **Gremlin URI** a partir da página **Descrição Geral**: ![Ver e copiar o valor do URI de Gremlin na página Descrição Geral no portal do Azure](./media/create-graph-java/gremlin-uri.png)

    No valor Palavra-passe, copie a **Chave Primária** a partir da página **Chaves**: ![Ver e copiar a chave primária no portal do Azure, página Chaves](./media/create-graph-java/keys.png)

## <a name="run-the-console-app"></a>Executar a aplicação de consola

1. Execute `mvn package` num terminal para instalar os pacotes Java necessários.

2. Execute `mvn exec:java -D exec.mainClass=GetStarted.Program` num terminal para iniciar a aplicação Java.

Agora, pode voltar ao Data Explorer e ver, consultar, modificar e trabalhar com estes dados novos. 

## <a name="browse-using-the-data-explorer"></a>Utilizar o Data Explorer para pesquisar

Agora, pode voltar ao Data Explorer no portal do Azure e procurar e consultar os dados do gráfico novo.

* No Data Explorer, a base de dados nova aparece no painel Coleções. Expanda **graphdb**, **graphcoll** e clique em **Gráfico**.

    Os dados gerados pela aplicação de exemplo são apresentados no painel Gráficos.

## <a name="review-slas-in-the-azure-portal"></a>Rever os SLAs no portal do Azure

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>Limpar recursos

Se não pretender continuar a utilizar esta aplicação, elimine todos os recursos criados com este guia de introdução no portal do Azure com os seguintes passos: 

1. No menu do lado esquerdo do portal do Azure, clique em **Grupos de recursos** e, em seguida, clique no nome de recurso que criou. 
2. Na página do grupo de recursos, clique em **Eliminar**, escreva o nome do recurso a eliminar na caixa de texto e, em seguida, clique em **Eliminar**.

## <a name="next-steps"></a>Passos seguintes

Neste guia rápido, aprendeu a criar uma conta do Azure Cosmos DB, a criar um gráfico com o Data Explorer e a executar uma aplicação. Agora, pode criar consultas mais complexas e implementar lógica poderosa para percorrer gráficos com Gremlin. 

> [!div class="nextstepaction"]
> [Query using Gremlin](tutorial-query-graph.md) (Utilizar Gremlin para consultar)


