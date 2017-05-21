---
title: "Azure CosmosDB: criar uma aplicação Web com .NET Core e a DocumentDB API | Microsoft Docs"
description: "Apresenta um exemplo de código .NET Core que pode utilizar para ligar e consultar a Azure Cosmos DB DocumentDB API"
services: cosmosdb
documentationcenter: 
author: mimig1
manager: jhubbard
editor: 
ms.assetid: 
ms.service: cosmosdb
ms.custom: quick start connect
ms.workload: 
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: hero-article
ms.date: 05/10/2017
ms.author: mimig
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 52cf1a729e4e86f764f9ded3712eaba558f1fc7f
ms.contentlocale: pt-pt
ms.lasthandoff: 05/10/2017


---
# <a name="azure-cosmos-db-build-a-documentdb-api-web-app-with-net-core-and-the-azure-portal"></a>Azure Cosmos DB: criar uma aplicação Web da DocumentDB API com .NET Core e o portal do Azure

O Azure Cosmos DB é um serviço de bases de dados com vários modelos e distribuído globalmente. Pode criar e consultar rapidamente o documento, a chave/valor e as bases de dados de gráficos, que beneficiam de capacidades de escalamento horizontal e distribuição global no centro do Azure Cosmos DB. 

Este guia de introdução demonstra como criar uma conta do Azure Cosmos DB, bases de dados de documentos e coleções com o portal do Azure. Depois, vai criar e implementar uma aplicação Web de Lista A Fazer criada com a [DocumentDB .NET Core API](../documentdb/documentdb-introduction.md), conforme mostrado na captura de ecrã seguinte. 

![Aplicação de Lista A Fazer com dados de exemplo](./media/create-documentdb-dotnet-core/azure-cosmosdb-todo-app-list.png)

## <a name="prerequisites"></a>Pré-requisitos

Se ainda não tiver o Visual Studio 2017 instalado, pode transferir e utilizar a [Visual Studio 2017 Community Edition](https://www.visualstudio.com/downloads/) **gratuita**. Confirme que ativa o **desenvolvimento do Azure** durante a configuração do Visual Studio.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="install-net-core"></a>Instalar .NET Core

Siga as instruções da página [.NET Core SDK](https://www.microsoft.com/net/download/core) para instalar o Net.Core. Os SDKs estão disponíveis para Windows, macOs e Linux.

## <a name="create-a-database-account"></a>Criar uma conta de base de dados

[!INCLUDE [documentdb-create-dbaccount](../../includes/documentdb-create-dbaccount.md)]

## <a name="add-a-collection"></a>Adicionar uma coleção

[!INCLUDE [cosmosdb-create-collection](../../includes/cosmosdb-create-collection.md)]

## <a name="add-sample-data"></a>Adicionar dados de exemplo

[!INCLUDE [cosmosdb-create-sample-data](../../includes/cosmosdb-create-sample-data.md)]

## <a name="clone-the-sample-application"></a>Clonar a aplicação de exemplo

Agora, vamos clonar uma aplicação de DocumentDB a partir do GitHub, definir a cadeia de ligação e executá-la. Vai ver como é fácil trabalhar com dados programaticamente.

1. Abra uma janela de terminal do git, tal como git bash, e `CD` para um diretório de trabalho.  

2. Execute o seguinte comando para clonar o repositório de exemplo. 

    ```bash
    git clone https://github.com/Azure-Samples/documentdb-dotnet-todo-app.git
    ```

3. Em seguida, abra o ficheiro da solução no Visual Studio. 
    
## <a name="review-the-code"></a>Rever o código

[!INCLUDE [cosmosdb-tutorial-review-code-dotnet](../../includes/cosmosdb-tutorial-review-code-dotnet.md)]

## <a name="update-your-connection-string"></a>Atualizar a cadeia de ligação

Agora, regresse ao portal do Azure para obter as informações da cadeia de ligação e copie-as para a aplicação.

1. No [portal do Azure](http://portal.azure.com/), na sua conta do Azure Cosmos DB, na navegação da esquerda, clique em **Chaves** e em **Chaves de leitura/escrita**. Vai utilizar os botões de copiar no lado direito do ecrã para copiar o URI e a Chave Primária para o ficheiro web.config no próximo passo.

    ![Ver e copiar uma chave de acesso no portal do Azure, painel Chaves](./media/create-documentdb-dotnet-core/keys.png)

2. No Visual Studio 2017, abra o ficheiro web.config. 

3. Copie o valor do URI a partir do portal (com o botão Copiar) e faça deste o valor da chave do ponto final em web.config. 

    `<add key="endpoint" value="FILLME" />`

4. Em seguida, copie o valor de CHAVE PRIMÁRIA do portal e faça do mesmo o valor de authKey em web.config. 

    `<add key="authKey" value="FILLME" />`

Atualizou agora a sua aplicação com todas as informações necessárias para comunicar com o Azure Cosmos DB. 

## <a name="run-the-web-app"></a>Executar a aplicação Web

1. No Visual Studio, clique com o botão direito do rato no projeto no **Explorador de Soluções** e clique em **Gerir Pacotes NuGet**. 

2. Na caixa **Procurar** do NuGet, escreva *DocumentDB*.

3. Nos resultados, instale a biblioteca **Microsoft.Azure.DocumentDB**. Esta ação instala o pacote do Azure Cosmos DB, bem como todas as dependências do mesmo.

4. Clique em CTRL + F5 para executar a aplicação. A aplicação é apresentada no browser. 

5. Clique em **Criar nova** no browser e crie algumas tarefas novas na aplicação Lista a Fazer.

   ![Aplicação de Lista A Fazer com dados de exemplo](./media/create-documentdb-dotnet-core/azure-cosmosdb-todo-app-list.png)

Agora, pode voltar ao Data Explorer e ver, consultar, modificar e trabalhar com estes dados novos. 

## <a name="review-slas-in-the-azure-portal"></a>Rever os SLAs no portal do Azure

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmosdb-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>Limpar recursos

Se não pretender continuar a utilizar esta aplicação, elimine todos os recursos criados com este guia de introdução no portal do Azure com os seguintes passos:

1. No menu do lado esquerdo do portal do Azure, clique em **Grupos de recursos** e, em seguida, clique no nome de recurso que criou. 
2. Na página do grupo de recursos, clique em **Eliminar**, escreva o nome do recurso a eliminar na caixa de texto e, em seguida, clique em **Eliminar**.

## <a name="next-steps"></a>Passos seguintes

Neste guia rápido, aprendeu a criar uma conta do Azure Cosmos DB, a criar uma coleção com o Data Explorer e a executar uma aplicação Web. Agora, pode importar dados adicionais à sua conta do Cosmos DB. 

> [!div class="nextstepaction"]
> [Import data into Azure Cosmos DB](../documentdb/documentdb-import-data.md) (Importar dados para o Azure Cosmos DB).

