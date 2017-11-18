---
title: "Início rápido: Tabela API com .NET - Cosmos BD do Azure | Microsoft Docs"
description: "Este guia de introdução mostra como utilizar a API de tabela de base de dados do Azure Cosmos para criar uma aplicação com o portal do Azure e o .NET"
services: cosmos-db
documentationcenter: 
author: mimig1
manager: jhubbard
editor: 
ms.assetid: 66327041-4d5e-4ce6-a394-fee107c18e59
ms.service: cosmos-db
ms.custom: quickstart connect, mvc
ms.workload: 
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 11/16/2017
ms.author: mimig
ms.openlocfilehash: 4e59c333e14e5e21a02c3160cf6311d1182e5a5e
ms.sourcegitcommit: 933af6219266cc685d0c9009f533ca1be03aa5e9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/18/2017
---
# <a name="quickstart-build-a-table-api-app-with-net-and-azure-cosmos-db"></a>Início rápido: Criar uma tabela de aplicação de API com .NET e do Azure Cosmos DB 

Este guia de introdução mostra como utilizar o Java e a base de dados do Azure Cosmos [API de tabela](table-introduction.md) para criar uma aplicação através da clonagem de um exemplo do GitHub. Este guia de introdução mostra também como criar uma conta de base de dados do Azure Cosmos e como utilizar o Explorador de dados para criar tabelas e entidades no portal do Azure baseada na web.

O Azure Cosmos DB é um serviço de bases de dados com vários modelos e distribuído globalmente da Microsoft. Pode criar e consultar rapidamente o documento, a chave/valor e as bases de dados de gráficos, que beneficiam de capacidades de escalamento horizontal e distribuição global no centro do Azure Cosmos DB. 

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
2. Agora, adicione os dados para a caixa de valor PartitionKey e caixas de valor de RowKey e clique **adicionar entidade**.

   ![Definir a Chave de Partição e a Chave de Linha para uma nova entidade](./media/create-table-dotnet/azure-cosmosdb-data-explorer-new-entity.png)
  
    Agora, pode adicionar mais entidades à tabela e editá-las ou consultar os dados no Data Explorer. Também é no Data Explorer que pode dimensionar o débito e adicionar procedimentos armazenados, funções definidas pelo utilizador e acionadores à sua tabela.

## <a name="clone-the-sample-application"></a>Clonar a aplicação de exemplo

Agora, vamos clonar uma aplicação de Tabela a partir do GitHub, definir a cadeia de ligação e executá-la. Vai ver como é fácil trabalhar com dados programaticamente. 

1. Abra uma janela de terminal do git, tais como o git bash e utilize o `cd` comando para alterar para uma pasta para instalar a aplicação de exemplo. 

    ```bash
    cd "C:\git-samples"
    ```

2. Execute o seguinte comando para clonar o repositório de exemplo. Este comando cria uma cópia da aplicação de exemplo no seu computador. 

    ```bash
    git clone https://github.com/Azure-Samples/storage-table-dotnet-getting-started.git
    ```

3. Em seguida, abra o ficheiro de solução TableStorage no Visual Studio. 

## <a name="update-your-connection-string"></a>Atualizar a cadeia de ligação

Agora, regresse ao portal do Azure para obter as informações da cadeia de ligação e copie-as para a aplicação. Isto permite que a aplicação comunicar com a base de dados alojada. 

1. No [portal do Azure](http://portal.azure.com/), clique em **cadeia de ligação**. 

    Utilize os botões de cópia no lado direito do ecrã para copiar a cadeia de ligação principal.

    ![Ver e copiar a cadeia de ligação principal no painel de cadeia de ligação](./media/create-table-dotnet/connection-string.png)

2. No Visual Studio, abra o ficheiro App. config. 

3. Anule o comentário StorageConnectionString na linha 8 e comente a StorageConnectionString na linha 7 como este tutorial não utiliza o emulador de armazenamento. Linha 7 e 8 deve agora ter o seguinte aspeto:

    ```
    <!--key="StorageConnectionString" value="UseDevelopmentStorage=true;" />-->
    <add key="StorageConnectionString" value="DefaultEndpointsProtocol=https;AccountName=[AccountName];AccountKey=[AccountKey]" />
    ```

4. Cole a cadeia de ligação principal do portal o valor de StorageConnectionString na linha 8. Cole a cadeia dentro de aspas. Se o ponto final utiliza documents.azure.com, altere a que parte para table.cosmosdb.azure.com. 

    Linha 8 deve agora ter um aspeto semelhante a:

    ```
    <add key="StorageConnectionString" value="DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=txZACN9f...==;TableEndpoint=https://<account name>.table.cosmosdb.azure.com;" />
    ```

4. Guarde o ficheiro App. config.

Atualizou agora a sua aplicação com todas as informações necessárias para comunicar com o Azure Cosmos DB. 

## <a name="build-and-deploy-the-app"></a>Criar e implementar a aplicação

1. No Visual Studio, clique com botão direito no **TableStorage** projeto no **Explorador de soluções** e, em seguida, clique em **gerir pacotes NuGet**. 

2. Do NuGet **procurar** caixa, escreva *Microsoft.Azure.CosmosDB.Table*.

3. Na lista de resultados, instale o **Microsoft.Azure.CosmosDB.Table** biblioteca. Esta ação instala o pacote de API de tabela do Azure Cosmos DB, bem como todas as dependências.

4. Abra BasicSamples.cs e adicione um ponto de interrupção linha 30 e linha 52.

5. Clique em CTRL + F5 para executar a aplicação.

    A janela de consola apresenta os dados da tabela a ser adicionados para a nova base de dados de tabela na base de dados do Azure Cosmos. 
    
    Se obtiver um erro sobre dependências, consulte [resolução de problemas](table-sdk-dotnet.md#troubleshooting).

    Quando atingiu o primeiro ponto de interrupção, volte atrás para Explorador de dados no portal do Azure e expanda a tabela demonstração * e clique em **entidades**. O **entidades** separador no lado direito apresenta a nova entidade que tenha sido adicionada, tenha em atenção o número de telefone para o utilizador é 425-555-0101.
    
6. Feche o separador de entidades no Explorador de dados.
    
7. Continue a executar a aplicação para o ponto de interrupção seguinte.

    Quando atingiu o ponto de interrupção, mude novamente para o portal, clique em entidades novamente para abrir o separador de entidades e tenha em atenção que o número de telefone foi atualizado para 425-555-0105.

8. Na janela da consola, prima CTRL + C para terminar a execução da aplicação. 

    Agora pode voltar ao Explorador de dados e adicionar ou modificar o entitites e consultar os dados.

## <a name="review-slas-in-the-azure-portal"></a>Rever os SLAs no portal do Azure

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>Limpar recursos

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>Passos seguintes

Neste guia de introdução, aprendeu a criar uma conta do Azure Cosmos DB, a criar uma tabela com o Data Explorer e a executar uma aplicação.  Agora, pode consultar os dados com a API de Tabela.  

> [!div class="nextstepaction"]
> [Importar dados de tabela para a API de tabela](table-import.md)

