---
title: "Criar uma aplicação .NET do Azure Cosmos DB com a API de Tabela | Microsoft Docs"
description: "Introdução à API de Tabela do Azure Cosmos DB com .NET"
services: cosmos-db
documentationcenter: 
author: arramac
manager: jhubbard
editor: 
ms.assetid: 66327041-4d5e-4ce6-a394-fee107c18e59
ms.service: cosmos-db
ms.custom: quick start connect, mvc
ms.workload: 
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 06/22/2017
ms.author: arramac
ms.openlocfilehash: 9b1d41fe185f4c3d5fdce13ab8f0136bc961f013
ms.sourcegitcommit: 295ec94e3332d3e0a8704c1b848913672f7467c8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/06/2017
---
# <a name="azure-cosmos-db-build-a-net-application-using-the-table-api"></a>Azure Cosmos DB: criar uma aplicação .NET com a API de Tabela

O Azure Cosmos DB é um serviço de bases de dados com vários modelos e distribuído globalmente da Microsoft. Pode criar e consultar rapidamente o documento, a chave/valor e as bases de dados de gráficos, que beneficiam de capacidades de escalamento horizontal e distribuição global no centro do Azure Cosmos DB. 

Este guia de introdução demonstra como criar uma conta do Azure Cosmos DB e criar uma tabela nessa conta com o portal do Azure. Em seguida, vai escrever código para inserir, atualizar e eliminar entidades, e executar algumas consultas com, o novo pacote [Windows Azure Storage Premium Table](https://aka.ms/premiumtablenuget) (pré-visualização) a partir do NuGet. Esta biblioteca tem as mesmas classes e assinaturas de método do que o [SDK do Armazenamento do Windows Azure](https://www.nuget.org/packages/WindowsAzure.Storage) público, mas também tem a capacidade de ligar a contas do Azure Cosmos DB através da [API de Tabela](table-introduction.md) (pré-visualização). 

## <a name="prerequisites"></a>Pré-requisitos

Se ainda não tiver o Visual Studio 2017 instalado, pode transferir e utilizar a [Visual Studio 2017 Community Edition](https://www.visualstudio.com/downloads/) **gratuita**. Confirme que ativa o **desenvolvimento do Azure** durante a configuração do Visual Studio.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-a-database-account"></a>Criar uma conta de base de dados

[!INCLUDE [cosmos-db-create-dbaccount-table](../../includes/cosmos-db-create-dbaccount-table.md)]

## <a name="add-a-table"></a>Adicionar uma tabela

[!INCLUDE [cosmos-db-create-table](../../includes/cosmos-db-create-table.md)]

## <a name="add-sample-data"></a>Adicionar dados de exemplo

Pode agora utilizar o Data Explorer para adicionar dados à sua tabela nova.

1. No Data Explorer, expanda **sample-table**, clique em **Entidades** e, em seguida, clique em **Adicionar Entidade**.

   ![Criar novas entidades no Data Explorer no portal do Azure](./media/create-table-dotnet/azure-cosmosdb-data-explorer-new-document.png)
2. Agora, adicione dados às caixas de valores PartitionKey e RowKey e clique em **Adicionar Entidade**.

   ![Definir a Chave de Partição e a Chave de Linha para uma nova entidade](./media/create-table-dotnet/azure-cosmosdb-data-explorer-new-entity.png)
  
    Agora, pode adicionar mais entidades à tabela e editá-las ou consultar os dados no Data Explorer. Também é no Data Explorer que pode dimensionar o débito e adicionar procedimentos armazenados, funções definidas pelo utilizador e acionadores à sua tabela.

## <a name="clone-the-sample-application"></a>Clonar a aplicação de exemplo

Agora, vamos clonar uma aplicação de Tabela a partir do GitHub, definir a cadeia de ligação e executá-la. Vai ver como é fácil trabalhar com dados programaticamente. 

1. Abra uma janela de terminal do git, tal como git bash, e `cd` para um diretório de trabalho.  

2. Execute o seguinte comando para clonar o repositório de exemplo. 

    ```bash
    git clone https://github.com/Azure-Samples/azure-cosmos-db-table-dotnet-getting-started.git
    ```

3. Em seguida, abra o ficheiro da solução no Visual Studio. 

## <a name="review-the-code"></a>Rever o código

Vamos fazer uma breve revisão do que está a acontecer à aplicação. Abra o ficheiro Program.cs e verá que estas linhas de código criam os recursos do Azure Cosmos DB. 

* O CloudTableClient é inicializado.

    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(connectionString); 
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();
    ```

* É criada uma tabela nova, caso ainda não exista uma tabela.

    ```csharp
    CloudTable table = tableClient.GetTableReference("people");
    table.CreateIfNotExists();
    ```

* Uma série de passos são executados sobre como utilizar a tabela a `TableOperation` classe.

   ```csharp
   TableOperation insertOperation = TableOperation.Insert(item);
   table.Execute(insertOperation);
   ```
   
   ```csharp
   TableOperation retrieveOperation = TableOperation.Retrieve<T>(items[i].PartitionKey, items[i].RowKey);
   table.Execute(retrieveOperation);
   ```
   
   ```csharp
   TableOperation deleteOperation = TableOperation.Delete(items[i]);
   table.Execute(deleteOperation);
   ```


## <a name="update-your-connection-string"></a>Atualizar a cadeia de ligação

Agora, vamos atualizar as informações da cadeia de ligação para que a sua aplicação possa comunicar com o Azure Cosmos DB. 

1. No Visual Studio, abra o ficheiro app.config. 

2. No [portal do Azure](http://portal.azure.com/), no menu de navegação à esquerda do Azure Cosmos DB, clique em **Cadeia de Ligação**. Em seguida, no novo painel, clique no botão de cópia da cadeia de ligação. 

    ![Ver e copiar o Ponto Final e a Chave da Conta no painel Cadeia de Ligação](./media/create-table-dotnet/keys.png)

3. Cole o valor no ficheiro de configuração da aplicação como o valor de PremiumStorageConnectionString. 

    `<add key="PremiumStorageConnectionString" 
        value="DefaultEndpointsProtocol=https;AccountName=MYSTORAGEACCOUNT;AccountKey=AUTHKEY;TableEndpoint=https://COSMOSDB.documents.azure.com" />`    

    Pode deixar StandardStorageConnectionString inalterado.

Atualizou agora a sua aplicação com todas as informações necessárias para comunicar com o Azure Cosmos DB. 

## <a name="run-the-console-app"></a>Executar a aplicação de consola

1. No Visual Studio, clique com o botão direito do rato no projeto **PremiumTableGetStarted** no **Explorador de Soluções** e clique em **Gerir Pacotes NuGet**. 

2. Na caixa **Procurar** do NuGet, escreva *WindowsAzure.Storage-PremiumTable*.

3. Selecione a caixa **Incluir pré-lançamento**. 

4. A partir dos resultados, instale a biblioteca **WindowsAzure.Storage-PremiumTable**. Esta ação instala o pacote da API de Tabela do Azure Cosmos DB de pré-visualização, bem como todas as dependências do mesmo. Note que se trata de um pacote do NuGet diferente do pacote do Armazenamento do Windows Azure utilizado pelo Armazenamento de Tabelas do Azure. 

5. Clique em CTRL + F5 para executar a aplicação.

    A janela da consola apresenta os dados que estão a ser adicionados, obtidos, consultados, substituídos e eliminados da tabela. Quando o script tiver concluído, prima qualquer tecla para fechar a janela da consola. 
    
    ![Saída da consola do início rápido](./media/create-table-dotnet/azure-cosmosdb-table-quickstart-console-output.png)

6. Se quiser ver as novas entidades no Data Explorer, basta comentar as linhas 188-208 em program.cs para que não sejam eliminadas e, em seguida, executar novamente o exemplo. 

    Agora, pode voltar ao Data Explorer, clicar em **Atualizar**, expandir a tabela **Pessoas**, clicar em **Entidades** e trabalhar com estes novos dados. 

    ![Novas entidades no Data Explorer](./media/create-table-dotnet/azure-cosmosdb-table-quickstart-data-explorer.png)

## <a name="review-slas-in-the-azure-portal"></a>Rever os SLAs no portal do Azure

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>Limpar recursos

Se não pretender continuar a utilizar esta aplicação, elimine todos os recursos criados com este guia de introdução no portal do Azure com os seguintes passos: 

1. No menu do lado esquerdo do portal do Azure, clique em **Grupos de recursos** e, em seguida, clique no nome de recurso que criou. 
2. Na página do grupo de recursos, clique em **Eliminar**, escreva o nome do recurso a eliminar na caixa de texto e, em seguida, clique em **Eliminar**.

## <a name="next-steps"></a>Passos seguintes

Neste guia de introdução, aprendeu a criar uma conta do Azure Cosmos DB, a criar uma tabela com o Data Explorer e a executar uma aplicação.  Agora, pode consultar os dados com a API de Tabela.  

> [!div class="nextstepaction"]
> [Query using the Table API](tutorial-query-table.md) (Consultar com a API de Tabela)

