---
title: "Azure Cosmos DB: Desenvolver com o gráfico de API no .NET | Microsoft Docs"
description: "Saiba como desenvolver com a API do Azure Cosmos base de dados DocumentDB através do .NET"
services: cosmos-db
documentationcenter: 
author: dennyglee
manager: jhubbard
editor: 
ms.assetid: cc8df0be-672b-493e-95a4-26dd52632261
ms.service: cosmos-db
ms.workload: 
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: tutorial
ms.date: 05/10/2017
ms.author: denlee
ms.custom: mvc
ms.openlocfilehash: b1419e5aad9446b9d96450cfad79b200cda9a518
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="azure-cosmos-db-develop-with-the-graph-api-in-net"></a>Azure Cosmos DB: Desenvolver com o gráfico de API no .NET
BD do Azure do Cosmos é serviço de base de dados com múltiplos modelo global distribuída da Microsoft. Pode criar e consultar rapidamente o documento, a chave/valor e as bases de dados de gráficos, que beneficiam de capacidades de escalamento horizontal e distribuição global no centro do Azure Cosmos DB. 

Este tutorial demonstra como criar uma conta de base de dados do Azure Cosmos no portal do Azure e como criar uma base de dados do gráfico e um contentor. A aplicação, em seguida, cria uma rede social simple com quatro pessoas utilizando o [Graph API](graph-sdk-dotnet.md) (pré-visualização), em seguida, atravessa e consulta o gráfico utilizando Gremlin.

Este tutorial abrange as seguintes tarefas:

> [!div class="checklist"]
> * Criar uma conta do Azure Cosmos DB 
> * Criar uma base de dados do gráfico e um contentor
> * Serializar vértices e margens para objetos .NET
> * Adicionar vértices e contornos
> * Consultar o gráfico utilizando Gremlin

## <a name="graphs-in-azure-cosmos-db"></a>Gráficos no Azure Cosmos DB
Pode utilizar a BD do Cosmos do Azure para criar, atualizar e consultar gráficos utilizando o [Microsoft.Azure.Graphs](graph-sdk-dotnet.md) biblioteca. A biblioteca de Microsoft.Azure.Graph fornece um método de extensão único `CreateGremlinQuery<T>` do `DocumentClient` classe para executar consultas Gremlin.

Gremlin é uma linguagem de programação funcional que suporta escrever operações (DML) e as operações de consulta e transversal. Iremos abranger alguns exemplos neste artigo para obter a introdução com Gremlin. Consulte [consultas Gremlin](gremlin-support.md) para instruções detalhadas das capacidades de Gremlin disponíveis do BD Azure Cosmos. 

## <a name="prerequisites"></a>Pré-requisitos
Certifique-se de que tem o seguinte:

* Uma conta ativa do Azure. Se não tiver uma, pode inscrever-se numa [conta gratuita](https://azure.microsoft.com/free/). 
    * Em alternativa, pode utilizar o [Emulador do Azure DocumentDB](local-emulator.md) para este tutorial.
* [Visual Studio](http://www.visualstudio.com/).

## <a name="create-database-account"></a>Criar conta de base de dados

Vamos começar por criar uma conta de base de dados do Azure Cosmos no portal do Azure.  

> [!TIP]
> * Já tem uma conta de base de dados do Azure Cosmos? Se assim for, avançar diretamente para [configurar a sua solução Visual Studio](#SetupVS)
> * Tem uma conta do Azure DocumentDB? Se Sim, agora a sua conta é uma conta de base de dados do Azure Cosmos e pode avançar diretamente para [configurar a sua solução Visual Studio](#SetupVS).  
> * Se estiver a utilizar o emulador de BD do Cosmos do Azure, siga os passos indicados em [emulador de BD do Azure Cosmos](local-emulator.md) para configurar o emulador e avançar diretamente para [configurar a sua solução do Visual Studio](#SetupVS). 
>
> 

[!INCLUDE [cosmos-db-create-dbaccount-graph](../../includes/cosmos-db-create-dbaccount-graph.md)]

## <a id="SetupVS"></a>Configurar a sua solução Visual Studio
1. Abra o **Visual Studio** no seu computador.
2. No menu **Ficheiro**, selecione **Novo**, e, em seguida, escolha **Projeto**.
3. No **novo projeto** caixa de diálogo, selecione **modelos** / **Visual c#** / **aplicação de consola (.NET Framework)**, nomeie o projeto e, em seguida, clique em **OK**.
4. No **Explorador de Soluções**, clique com o botão direito do rato na sua nova aplicação de consola, que está sob a sua solução Visual Studio e, em seguida, clique em **Gerir Pacotes NuGet...**
5. No **NuGet** separador, clique em **procurar**e escreva **Microsoft.Azure.Graphs** na caixa de pesquisa e verifique o **incluem as versões de pré-lançamento**.
6. Nos resultados, localizar **Microsoft.Azure.Graphs** e clique em **instalar**.
   
   Se obtiver uma mensagem sobre a revisão das alterações para a solução, clique em **OK**. Se obtiver uma mensagem sobre a aceitação de licença, clique em **Aceito**.
   
    O `Microsoft.Azure.Graphs` biblioteca fornece um método de extensão único `CreateGremlinQuery<T>` para executar operações de Gremlin. Gremlin é uma linguagem de programação funcional que suporta escrever operações (DML) e as operações de consulta e transversal. Iremos abranger alguns exemplos neste artigo para obter a introdução com Gremlin. [Consultas de gremlin](gremlin-support.md) tem instruções detalhadas sobre as capacidades de Gremlin do BD Azure Cosmos.

## <a id="add-references"></a>Ligar a aplicação

Adicione estas duas constantes e a sua *cliente* variável na sua aplicação. 

```csharp
string endpoint = ConfigurationManager.AppSettings["Endpoint"]; 
string authKey = ConfigurationManager.AppSettings["AuthKey"]; 
``` 
Em seguida, head novamente para o [portal do Azure](https://portal.azure.com) para obter o URL de ponto final e a chave primária. O URL de ponto final e a chave primária são necessários para que a sua aplicação saiba onde ligar e para que o Azure Cosmos DB confie na ligação da sua aplicação. 

No portal do Azure, navegue até à sua conta de base de dados do Azure Cosmos, clique em **chaves**e, em seguida, clique em **chaves de leitura e escrita**. 

Copie o URI do portal e cole-o ao longo do `Endpoint` na propriedade endpoint acima. Em seguida, copie a chave primária do portal e cole-o para o `AuthKey` propriedade acima. 

! [Captura de ecrã do portal do Azure utilizado pelo tutorial para criar uma aplicação c#. Mostra o botão chaves realçado no painel de navegação BD do Cosmos do Azure e os valores URI e a chave primária realçados no painel chaves de conta de uma base de dados do Cosmos do Azure] [chaves] 
 
## <a id="instantiate"></a>Instanciar o DocumentClient 
Em seguida, crie uma nova instância do **DocumentClient**.  

```csharp 
DocumentClient client = new DocumentClient(new Uri(endpoint), authKey); 
``` 

## <a id="create-database"></a>Criar uma base de dados 

Agora, crie uma base de dados do Azure Cosmos [base de dados](documentdb-resources.md#databases) utilizando o [CreateDatabaseAsync](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.documentclient.createdatabaseasync.aspx) método ou [CreateDatabaseIfNotExistsAsync](https://msdn.microsoft.com/library/microsoft.azure.documents.client.documentclient.createdatabaseifnotexistsasync.aspx) método o  **DocumentClient** classe a partir de [SDK do .NET DocumentDB](documentdb-sdk-dotnet.md).  

```csharp 
Database database = await client.CreateDatabaseIfNotExistsAsync(new Database { Id = "graphdb" }); 
``` 
 
## <a name="create-a-graph"></a>Criar um gráfico 

Em seguida, criar um contentor de gráfico com o utilizando o [CreateDocumentCollectionAsync](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.documentclient.createdocumentcollectionasync.aspx) método ou [CreateDocumentCollectionIfNotExistsAsync](https://msdn.microsoft.com/library/microsoft.azure.documents.client.documentclient.createdocumentcollectionifnotexistsasync.aspx) método o **DocumentClient** classe. Uma coleção é um contentor de entidades de gráfico. 

```csharp 
DocumentCollection graph = await client.CreateDocumentCollectionIfNotExistsAsync( 
    UriFactory.CreateDatabaseUri("graphdb"), 
    new DocumentCollection { Id = "graphcollz" }, 
    new RequestOptions { OfferThroughput = 1000 }); 
``` 

## <a id="serializing"></a>Serializar vértices e margens para objetos .NET
BD do Cosmos do Azure utiliza o [formato wire de GraphSON](gremlin-support.md), que define um esquema JSON para vértices, margens e propriedades. O SDK .NET da Azure Cosmos DB inclui JSON.NET como uma dependência e Isto permite-nos para serialização/desserialização GraphSON em objetos de .NET que iremos pode trabalhar com o código.

Por exemplo, vamos trabalhar com uma rede social simple com quatro pessoas. Vamos ver como criar `Person` vértices, adicionar `Knows` relações entre eles, em seguida, consultar e atravessar o gráfico de modo a encontrar "amigo do amigo" relações. 

O `Microsoft.Azure.Graphs.Elements` espaço de nomes fornece `Vertex`, `Edge`, `Property` e `VertexProperty` classes para anular a serialização de respostas GraphSON definidas especificamente objetos de .NET.

## <a name="run-gremlin-using-creategremlinquery"></a>Executar Gremlin utilizando CreateGremlinQuery
Gremlin, como o SQL, suporta a leitura, escrita e operações de consulta. Por exemplo, o fragmento seguinte mostra como criar vértices, margens, executar algumas consultas de exemplo com `CreateGremlinQuery<T>`e de forma assíncrona itere através destes resultados utilizando `ExecuteNextAsync` e ' HasMoreResults.

```cs
Dictionary<string, string> gremlinQueries = new Dictionary<string, string>
{
    { "Cleanup",        "g.V().drop()" },
    { "AddVertex 1",    "g.addV('person').property('id', 'thomas').property('firstName', 'Thomas').property('age', 44)" },
    { "AddVertex 2",    "g.addV('person').property('id', 'mary').property('firstName', 'Mary').property('lastName', 'Andersen').property('age', 39)" },
    { "AddVertex 3",    "g.addV('person').property('id', 'ben').property('firstName', 'Ben').property('lastName', 'Miller')" },
    { "AddVertex 4",    "g.addV('person').property('id', 'robin').property('firstName', 'Robin').property('lastName', 'Wakefield')" },
    { "AddEdge 1",      "g.V('thomas').addE('knows').to(g.V('mary'))" },
    { "AddEdge 2",      "g.V('thomas').addE('knows').to(g.V('ben'))" },
    { "AddEdge 3",      "g.V('ben').addE('knows').to(g.V('robin'))" },
    { "UpdateVertex",   "g.V('thomas').property('age', 44)" },
    { "CountVertices",  "g.V().count()" },
    { "Filter Range",   "g.V().hasLabel('person').has('age', gt(40))" },
    { "Project",        "g.V().hasLabel('person').values('firstName')" },
    { "Sort",           "g.V().hasLabel('person').order().by('firstName', decr)" },
    { "Traverse",       "g.V('thomas').outE('knows').inV().hasLabel('person')" },
    { "Traverse 2x",    "g.V('thomas').outE('knows').inV().hasLabel('person').outE('knows').inV().hasLabel('person')" },
    { "Loop",           "g.V('thomas').repeat(out()).until(has('id', 'robin')).path()" },
    { "DropEdge",       "g.V('thomas').outE('knows').where(inV().has('id', 'mary')).drop()" },
    { "CountEdges",     "g.E().count()" },
    { "DropVertex",     "g.V('thomas').drop()" },
};

foreach (KeyValuePair<string, string> gremlinQuery in gremlinQueries)
{
    Console.WriteLine($"Running {gremlinQuery.Key}: {gremlinQuery.Value}");

    // The CreateGremlinQuery method extensions allow you to execute Gremlin queries and iterate
    // results asychronously
    IDocumentQuery<dynamic> query = client.CreateGremlinQuery<dynamic>(graph, gremlinQuery.Value);
    while (query.HasMoreResults)
    {
        foreach (dynamic result in await query.ExecuteNextAsync())
        {
            Console.WriteLine($"\t {JsonConvert.SerializeObject(result)}");
        }
    }

    Console.WriteLine();
}
```

## <a name="add-vertices-and-edges"></a>Adicionar vértices e contornos

Vamos ver as instruções de Gremlin mostradas na secção anterior mais detalhes. Primeiro vamos algumas vértices através do Gremlin `addV` método. Por exemplo, o fragmento seguinte cria um vértice "Blogue ou seja" do tipo "Pessoa", com propriedades de nome próprio, apelido e idade.

```cs
// Create a vertex
IDocumentQuery<Vertex> createVertexQuery = client.CreateGremlinQuery<Vertex>(
    graphCollection, 
    "g.addV('person').property('firstName', 'Thomas')");

while (createVertexQuery.HasMoreResults)
{
    Vertex thomas = (await create.ExecuteNextAsync<Vertex>()).First();
}
```

Em seguida, vamos criar alguns contornos entre estes vértices através do Gremlin `addE` método. 

```cs
// Add a "knows" edge
IDocumentQuery<Edge> createEdgeQuery = client.CreateGremlinQuery<Edge>(
    graphCollection, 
    "g.V('thomas').addE('knows').to(g.V('mary'))");

while (create.HasMoreResults)
{
    Edge thomasKnowsMaryEdge = (await create.ExecuteNextAsync<Edge>()).First();
}
```

Iremos pode atualizar um vértice existente ao utilizar `properties` passo na Gremlin. Vamos ignorar a chamada para executar a consulta através de `HasMoreResults` e `ExecuteNextAsync` para o resto dos exemplos.

```cs
// Update a vertex
client.CreateGremlinQuery<Vertex>(
    graphCollection, 
    "g.V('thomas').property('age', 45)");
```

Pode colocar margens e vértices através do Gremlin `drop` passo. Eis um fragmento que mostra como eliminar um vértice e um limite. Tenha em atenção que remover de um vértice efetua um eliminar em cascata de margens associadas.

```cs
// Drop an edge
client.CreateGremlinQuery(graphCollection, "g.E('thomasKnowsRobin').drop()");

// Drop a vertex
client.CreateGremlinQuery(graphCollection, "g.V('robin').drop()");
```

## <a name="query-the-graph"></a>O gráfico de consulta

Pode executar consultas e traversals também utilizando Gremlin. Por exemplo, o fragmento seguinte mostra como contabilizar o número de vértices no gráfico de:

```cs
// Run a query to count vertices
IDocumentQuery<int> countQuery = client.CreateGremlinQuery<int>(graphCollection, "g.V().count()");
```
Pode efetuar filtros através do Gremlin `has` e `hasLabel` passos e combine-os utilizando `and`, `or`, e `not` para criar filtros mais complexos:

```cs
// Run a query with filter
IDocumentQuery<Vertex> personsByAge = client.CreateGremlinQuery<Vertex>(
  graphCollection, 
  "g.V().hasLabel('person').has('age', gt(40))");
```

Pode projetar a algumas propriedades nos resultados da consulta utilizando o `values` passo:

```cs
// Run a query with projection
IDocumentQuery<string> firstNames = client.CreateGremlinQuery<string>(
  graphCollection, 
  $"g.V().hasLabel('person').values('firstName')");
```

Até ao momento, estamos apenas viu operadores de consulta que funcionam em qualquer base de dados. Gráficos são rápidos e eficientes para operações de transversal quando precisar navegar para contornos relacionados e vértices. Vamos localizar todos os amigos de blogue. Podemos fazê-lo através do Gremlin `outE` passo para localizar todas as de out-extremidades do blogue, em seguida, que atravessa no-vértices desses contornos através do Gremlin `inV` passo:

```cs
// Run a traversal (find friends of Thomas)
IDocumentQuery<Vertex> friendsOfThomas = client.CreateGremlinQuery<Vertex>(
  graphCollection,
  "g.V('thomas').outE('knows').inV().hasLabel('person')");
```

A consulta seguinte executa duas saltos para localizar todos os "amigos de blogue de amigos", chamando `outE` e `inV` duas vezes. 

```cs
// Run a traversal (find friends of friends of Thomas)
IDocumentQuery<Vertex> friendsOfFriendsOfThomas = client.CreateGremlinQuery<Vertex>(
  graphCollection,
  "g.V('thomas').outE('knows').inV().hasLabel('person').outE('knows').inV().hasLabel('person')");
```

Pode construir consultas mais complexas e implementar a lógica do gráfico poderosas transversal utilizando Gremlin, incluindo a mistura de expressões de filtro, efetuar a utilizar o ciclo de `loop` passo e a utilização de navegação condicional implementar o `choose` passo. Saiba mais sobre o que pode fazer com [suporte Gremlin](gremlin-support.md)!

Já está, este tutorial de base de dados do Azure Cosmos está concluído! 

## <a name="clean-up-resources"></a>Limpar recursos

Se não pretender continuar a utilizar esta aplicação, siga os passos abaixo para eliminar todos os recursos criados por este tutorial no portal do Azure.  

1. No menu do lado esquerdo do portal do Azure, clique em **Grupos de recursos** e, em seguida, clique no nome de recurso que criou. 
2. Na página do grupo de recursos, clique em **Eliminar**, escreva o nome do recurso a eliminar na caixa de texto e, em seguida, clique em **Eliminar**.

## <a name="next-steps"></a>Passos Seguintes

Neste tutorial, fez o seguinte:

> [!div class="checklist"]
> * Criar uma conta de base de dados do Azure Cosmos 
> * Criar uma base de dados do gráfico e um contentor
> * Vértices serializados e margens para objetos .NET
> * Foram adicionadas vértices e contornos
> * Consultar o gráfico utilizando Gremlin

Agora, pode criar consultas mais complexas e implementar lógica poderosa para percorrer gráficos com Gremlin. 

> [!div class="nextstepaction"]
> [Query using Gremlin](tutorial-query-graph.md) (Utilizar Gremlin para consultar)
