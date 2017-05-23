---
title: "Criar uma aplicação .NET do Azure Cosmos DB com a Table API | Microsoft Docs"
description: "Introdução à Table API do Azure Cosmos DB com .NET"
services: cosmosdb
documentationcenter: 
author: arramac
manager: jhubbard
editor: 
ms.assetid: 66327041-4d5e-4ce6-a394-fee107c18e59
ms.service: cosmosdb
ms.custom: quick start connect
ms.workload: 
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: hero-article
ms.date: 05/10/2017
ms.author: arramac
ms.translationtype: Human Translation
ms.sourcegitcommit: 97fa1d1d4dd81b055d5d3a10b6d812eaa9b86214
ms.openlocfilehash: cba0b278d84e25876a8b73cedb7e35f84500fc5e
ms.contentlocale: pt-pt
ms.lasthandoff: 05/11/2017


---
# <a name="azure-cosmos-db-build-a-net-application-using-the-table-api"></a>Azure Cosmos DB: criar uma aplicação .NET com a Table API

O Azure Cosmos DB é um serviço de bases de dados com vários modelos e distribuído globalmente da Microsoft. Pode criar e consultar rapidamente o documento, a chave/valor e as bases de dados de gráficos, que beneficiam de capacidades de escalamento horizontal e distribuição global no centro do Azure Cosmos DB. 

Este guia de introdução demonstra como criar uma conta do Azure Cosmos DB e criar uma tabela nessa conta com o portal do Azure. Depois, vai escrever código para inserir, atualizar e eliminar entidades e executar algumas consultas. Pode transferir o [SDK de Pré-visualização do Armazenamento do Azure](https://aka.ms/premiumtablenuget) a partir do NuGet que tenha as mesmas classes e assinaturas de método do que o [SDK do Armazenamento do Azure](https://www.nuget.org/packages/WindowsAzure.Storage) público, mas que tenha também a capacidade de ligar a contas do Azure Cosmos DB através da [Table API](table-introduction.md) (pré-visualização). 

## <a name="prerequisites"></a>Pré-requisitos

Se ainda não tiver o Visual Studio 2017 instalado, pode transferir e utilizar a [Visual Studio 2017 Community Edition](https://www.visualstudio.com/downloads/) **gratuita**. Confirme que ativa o **desenvolvimento do Azure** durante a configuração do Visual Studio.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-a-database-account"></a>Criar uma conta de base de dados

[!INCLUDE [cosmosdb-create-dbaccount-table](../../includes/cosmosdb-create-dbaccount-table.md)]

## <a name="add-a-table"></a>Adicionar uma tabela

[!INCLUDE [cosmosdb-create-table](../../includes/cosmosdb-create-table.md)]

## <a name="add-sample-data"></a>Adicionar dados de exemplo

Pode agora utilizar o Data Explorer para adicionar dados à sua tabela nova.

1. No Data Explorer, expanda **sample-database**, **sample-table**, clique em **Entidades** e clique em **Adicionar Entidade**.
2. Agora, adicione dados às caixas de valores PartitionKey e RowKey e clique em **Adicionar Entidade**.

   ![Criar documentos novos no Data Explorer no portal do Azure](./media/create-table-dotnet/azure-cosmosdb-data-explorer-new-document.png)
  
    Agora, pode adicionar mais entidades à tabela e editá-las ou consultar os dados no Data Explorer. Também é no Data Explorer que pode dimensionar o débito e adicionar procedimentos armazenados, funções definidas pelo utilizador e acionadores à sua tabela.

## <a name="clone-the-sample-application"></a>Clonar a aplicação de exemplo

Agora, vamos clonar uma aplicação de DocumentDB a partir do GitHub, definir a cadeia de ligação e executá-la. Vai ver como é fácil trabalhar com dados programaticamente. 

1. Abra uma janela de terminal do git, tal como git bash, e `cd` para um diretório de trabalho.  

2. Execute o seguinte comando para clonar o repositório de exemplo. 

    ```bash
    git clone https://github.com/Azure-Samples/azure-cosmos-db-table-dotnet-getting-started.git
    ```

3. Em seguida, abra o ficheiro da solução no Visual Studio. 

## <a name="review-the-code"></a>Rever o código

Vamos fazer uma breve revisão do que está a acontecer à aplicação. Abra o ficheiro Program.cs e verá que estas linhas de código criam os recursos do Azure Cosmos DB. 

* O DocumentClient é inicializado.

    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(connectionString); 
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();
    ```

* É criada uma tabela nova, caso ainda não exista uma tabela.

    ```csharp
    CloudTable table = tableClient.GetTableReference("people");
    table.CreateIfNotExists();
    ```

* É criado um contentor de Tabelas novo. Vai reparar que este código é muito parecido com o SDK de armazenamento de Tabelas do Azure 

    ```csharp
    CustomerEntity item = new CustomerEntity()
                {
                    PartitionKey = Guid.NewGuid().ToString(),
                    RowKey = Guid.NewGuid().ToString(),
                    Email = $"{GetRandomString(6)}@contoso.com",
                    PhoneNumber = "425-555-0102",
                    Bio = GetRandomString(1000)
                };
    ```

## <a name="update-your-connection-string"></a>Atualizar a cadeia de ligação

Agora, regresse ao portal do Azure para obter as informações da cadeia de ligação e copie-as para a aplicação.

1. No [portal do Azure](http://portal.azure.com/), na sua conta do Azure Cosmos DB, na navegação da esquerda, clique em **Chaves** e em **Chaves de leitura/escrita**. Vai utilizar os botões de copiar no lado direito do ecrã para copiar o URI e a Chave Primária para o ficheiro app.config no próximo passo.

    ![Ver e copiar uma chave de acesso no portal do Azure, painel Chaves](./media/create-documentdb-dotnet-core/keys.png)

2. No Visual Studio, abra o ficheiro app.config. 

3. Copie o nome da sua conta do Azure Cosmos DB do portal e faça deste o valor de AccountName na cadeia PremiumStorageConnection em app.config. Na captura de ecrã acima, o nome da conta é cosmos-db-quickstart. O nome da sua conta é apresentado na parte superior do portal.

    `<add key="PremiumStorageConnectionString" 
        value="DefaultEndpointsProtocol=https;AccountName=MYSTORAGEACCOUNT;AccountKey=AUTHKEY;TableEndpoint=https://COSMOSDB.documents.azure.com" />`

4. Em seguida, copie o valor de CHAVE PRIMÁRIA do portal e faça do mesmo o valor de AccountKey em app.config. 

    `AccountKey=AUTHKEY`

5. Por fim, copie o valor de URL da página Chaves do portal (com o botão Copiar) e faça deste o valor de TableEndpoint de PremiumStorageConnectionString.

    `TableEndpoint=https://COSMOSDB.documents.azure.com`

    Pode deixar StandardStorageConnectionString inalterado.

Atualizou agora a sua aplicação com todas as informações necessárias para comunicar com o Azure Cosmos DB. 

## <a name="run-the-web-app"></a>Executar a aplicação Web

1. No Visual Studio, clique com o botão direito do rato no projeto no **Explorador de Soluções** e clique em **Gerir Pacotes NuGet**. 

2. Na caixa **Procurar** do NuGet, escreva *WindowsAzure.Storage* e assinale a caixa **Incluir pré-lançamento**. 

3. A partir dos resultados, instale a biblioteca **WindowsAzure.Storage**. Esta ação instala o pacote da Table API do Azure Cosmos DB de pré-visualização, bem como todas as dependências do mesmo.

4. Clique em CTRL + F5 para executar a aplicação.

    A janela da consola apresenta os dados que estão a ser adicionados à tabela. Quando o script tiver concluído, feche a janela da consola. 

Agora, pode voltar ao Data Explorer e ver, consultar, modificar e trabalhar com estes dados novos. 

## <a name="review-slas-in-the-azure-portal"></a>Rever os SLAs no portal do Azure

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmosdb-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>Limpar recursos

Se não pretender continuar a utilizar esta aplicação, elimine todos os recursos criados com este guia de introdução no portal do Azure com os seguintes passos: 

1. No menu do lado esquerdo do portal do Azure, clique em **Grupos de recursos** e, em seguida, clique no nome de recurso que criou. 
2. Na página do grupo de recursos, clique em **Eliminar**, escreva o nome do recurso a eliminar na caixa de texto e, em seguida, clique em **Eliminar**.

## <a name="next-steps"></a>Passos seguintes

Neste guia rápido, aprendeu a criar uma conta do Azure Cosmos DB, a criar uma tabela com o Data Explorer e a executar uma aplicação.  Agora, pode consultar os dados com a Table API.  

> [!div class="nextstepaction"]
> [Query using the Table API](tutorial-query-table.md) (Consultar com a Table API)


