---
title: "Criar uma aplicação .NET do Azure Cosmos DB com a Graph API | Microsoft Docs"
description: "Apresenta um exemplo de código .NET que pode utilizar para ligar e consultar o Azure Cosmos DB"
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
ms.openlocfilehash: 1794341ed0d4519eef7f065d04ccf86a7e48a4a4
ms.contentlocale: pt-pt
ms.lasthandoff: 07/17/2017

---
# <a name="azure-cosmos-db-build-a-net-application-using-the-graph-api"></a>Azure Cosmos DB: criar uma aplicação .NET com a Graph API

O Azure Cosmos DB é um serviço de bases de dados com vários modelos e distribuído globalmente. Pode criar e consultar rapidamente o documento, a chave/valor e as bases de dados de gráficos, que beneficiam de capacidades de escalamento horizontal e distribuição global no centro do Azure Cosmos DB. 

Este guia de introdução demonstra como criar uma conta do Azure Cosmos DB, bases de dados e gráficos (contentores) com o portal do Azure. Depois, vai criar e executar uma aplicação de consola criada na [Graph API](graph-sdk-dotnet.md) (pré-visualização).  

## <a name="prerequisites"></a>Pré-requisitos

Se ainda não tiver o Visual Studio 2017 instalado, pode transferir e utilizar a [Visual Studio 2017 Community Edition](https://www.visualstudio.com/downloads/) **gratuita**. Confirme que ativa o **desenvolvimento do Azure** durante a configuração do Visual Studio.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-a-database-account"></a>Criar uma conta de base de dados

[!INCLUDE [cosmos-db-create-dbaccount-graph](../../includes/cosmos-db-create-dbaccount-graph.md)]

## <a name="add-a-graph"></a>Adicionar um gráfico

[!INCLUDE [cosmos-db-create-graph](../../includes/cosmos-db-create-graph.md)]

## <a name="clone-the-sample-application"></a>Clonar a aplicação de exemplo

Agora, vamos clonar uma aplicação do Graph API a partir do GitHub, definir a cadeia de ligação e executá-la. Vai ver como é fácil trabalhar com dados programaticamente. 

1. Abra uma janela de terminal do git, tal como git bash, e `cd` para um diretório de trabalho.  

2. Execute o seguinte comando para clonar o repositório de exemplo. 

    ```bash
    git clone https://github.com/Azure-Samples/azure-cosmos-db-graph-dotnet-getting-started.git
    ```

3. Em seguida, abra o ficheiro da solução no Visual Studio. 

## <a name="review-the-code"></a>Rever o código

Vamos fazer uma breve revisão do que está a acontecer à aplicação. Abra o ficheiro Program.cs e verá que estas linhas de código criam os recursos do Azure Cosmos DB. 

* O DocumentClient é inicializado. Na pré-visualização, adicionámos uma API de extensão de gráficos no cliente do Azure Cosmos DB. Estamos a trabalhar num cliente de gráficos autónomo que não esteja acoplado ao cliente e aos recursos do Azure Cosmos DB.

    ```csharp
    using (DocumentClient client = new DocumentClient(
        new Uri(endpoint),
        authKey,
        new ConnectionPolicy { ConnectionMode = ConnectionMode.Direct, ConnectionProtocol = Protocol.Tcp }))
    ```

* É criada uma nova base de dados.

    ```csharp
    Database database = await client.CreateDatabaseIfNotExistsAsync(new Database { Id = "graphdb" });
    ```

* É criado um novo gráfico.

    ```csharp
    DocumentCollection graph = await client.CreateDocumentCollectionIfNotExistsAsync(
        UriFactory.CreateDatabaseUri("graphdb"),
        new DocumentCollection { Id = "graph" },
        new RequestOptions { OfferThroughput = 1000 });
    ```
* São executados uma série de passos do Gremlin com o método `CreateGremlinQuery`.

    ```csharp
    // The CreateGremlinQuery method extensions allow you to execute Gremlin queries and iterate
    // results asychronously
    IDocumentQuery<dynamic> query = client.CreateGremlinQuery<dynamic>(graph, "g.V().count()");
    while (query.HasMoreResults)
    {
        foreach (dynamic result in await query.ExecuteNextAsync())
        {
            Console.WriteLine($"\t {JsonConvert.SerializeObject(result)}");
        }
    }

    ```

## <a name="update-your-connection-string"></a>Atualizar a cadeia de ligação

Agora, regresse ao portal do Azure para obter as informações da cadeia de ligação e copie-as para a aplicação.

1. No portal do Azure, na sua conta do Azure Cosmos DB, clique em **Descrição Geral**, na navegação do lado esquerdo. Vai copiar o valor **Gremlin URI** no ficheiro App.config no passo seguinte. 

    ![Ver e copiar uma chave de acesso no portal do Azure, painel Chaves](./media/create-graph-dotnet/gremlin-uri.png)

    Se o valor **URI do Gremlin** estiver em branco, pode gerá-lo na página **Chaves** do portal, utilizando o valor **URI**, removendo https:// e alterando os documentos para gráficos. 

2. No Visual Studio 2017, abra o ficheiro App.config. 

3. Copie o valor **Gremlin URI** a partir do portal e faça deste o valor da chave do Ponto Final no ficheiro App.config. 

    `<add key="Endpoint" value="FILLME.graphs.azure.com:443" />`

4. Novamente no portal do Azure, clique em **Chaves**, no menu de navegação do lado esquerdo, copie o valor **CHAVE PRIMÁRIA** do portal e faça deste o valor da chave AuthKey no ficheiro App.config. Em seguida, guarde as alterações. 

    `<add key="AuthKey" value="FILLME" />`

    ![Ver e copiar uma chave primária no portal do Azure, na página Chaves](./media/create-graph-dotnet/keys.png)

Atualizou agora a sua aplicação com todas as informações necessárias para comunicar com o Azure Cosmos DB. 

## <a name="run-the-console-app"></a>Executar a aplicação de consola

1. No Visual Studio, clique com o botão direito do rato no projeto **GraphGetStarted** no **Explorador de Soluções** e clique em **Gerir Pacotes NuGet**. 

2. Na caixa **Procurar** do NuGet, escreva *Microsoft.Azure.Graphs* e assinale a caixa **Inclui pré-lançamento**. 

3. Nos resultados, instale a biblioteca **Microsoft.Azure.Graphs**. Esta ação instala o pacote de biblioteca da extensão de gráficos do Azure Cosmos DB e todas as dependências do mesmo.

4. Clique em CTRL + F5 para executar a aplicação.

   A janela de consola apresenta os vértices e as margens a adicionar ao gráfico. Quando o script tiver concluído, prima ENTER duas vezes para fechar a janela da consola. 

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


