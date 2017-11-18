---
title: "Início rápido: Tabela API com o Python - Cosmos BD do Azure | Microsoft Docs"
description: "Este guia de introdução mostra como utilizar a API de tabela de base de dados do Azure Cosmos para criar uma aplicação com o portal do Azure e o Python"
services: cosmos-db
documentationcenter: 
author: mimig1
manager: jhubbard
editor: 
ms.assetid: 
ms.service: cosmos-db
ms.workload: 
ms.tgt_pltfrm: na
ms.devlang: python
ms.topic: quickstart
ms.date: 11/16/2017
ms.author: mimig
ms.openlocfilehash: 5bf995cba884ff9910ce000195c8fa0e3da2d332
ms.sourcegitcommit: a036a565bca3e47187eefcaf3cc54e3b5af5b369
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/17/2017
---
# <a name="quickstart-build-a-table-api-app-with-python-and-azure-cosmos-db"></a>Início rápido: Criar uma tabela de aplicação de API com o Python e Azure Cosmos DB

Este guia de introdução mostra como utilizar o Python e a base de dados do Azure Cosmos [API de tabela](table-introduction.md) para criar uma aplicação através da clonagem de um exemplo do GitHub. Este guia de introdução mostra também como criar uma conta de base de dados do Azure Cosmos e como utilizar o Explorador de dados para criar tabelas e entidades no portal do Azure baseada na web.

O Azure Cosmos DB é um serviço de bases de dados com vários modelos e distribuído globalmente da Microsoft. Pode criar e consultar documentos, chave/valor, toda a coluna e bases de dados de gráfico, sendo todas beneficiam da distribuição global e as capacidades de dimensionamento horizontal o núcleo da BD do Cosmos Azure rapidamente. 

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]
[!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

Além disso:

* Se ainda não tiver o Visual Studio 2017 instalado, pode transferir e utilizar a [Visual Studio 2017 Community Edition](https://www.visualstudio.com/downloads/) **gratuita**. Confirme que ativa o **desenvolvimento do Azure** durante a configuração do Visual Studio.
* Ferramentas do Python para Visual Studio a partir do [GitHub](http://microsoft.github.io/PTVS/). Este tutorial utiliza as Ferramentas do Python para a VS 2015.
* Python 2.7, de [python.org](https://www.python.org/downloads/release/python-2712/)

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
    git clone https://github.com/Azure-Samples/storage-python-getting-started.git
    ```

3. Em seguida, abra o ficheiro da solução no Visual Studio. 

## <a name="update-your-connection-string"></a>Atualizar a cadeia de ligação

Agora, regresse ao portal do Azure para obter as informações da cadeia de ligação e copie-as para a aplicação. Isto permite que a aplicação comunicar com a base de dados alojada. 

1. No [portal do Azure](http://portal.azure.com/), clique em **cadeia de ligação**. 

    ![Ver e copiar a cadeia de ligação no painel de cadeia de ligação](./media/create-table-python/connection-string.png)

2. Copie o nome da conta com o botão à direita.

3. Abra o ficheiro de config.py e cole o nome da conta do portal para o valor STORAGE_ACCOUNT_NAME 19 de linha.

4. Volte ao portal e copie a chave primária.

5. Cole a chave primária no portal o valor STORAGE_ACCOUNT_KEY linha 20.

3. Guarde o ficheiro config.py.

## <a name="run-the-app"></a>Executar a aplicação

1. No Visual Studio, clique com o botão direito do rato no projeto no **Explorador de Soluções**, selecione o ambiente de Python atual e clique com o botão direito do rato.

2. Selecione Instalar pacote do Python, em seguida, escreva **tabela de armazenamento do azure**

3. Prima F5 para executar a aplicação. A aplicação é apresentada no browser. 

Agora, pode voltar ao Data Explorer e ver, consultar, modificar e trabalhar com estes dados novos. 

## <a name="review-slas-in-the-azure-portal"></a>Rever os SLAs no portal do Azure

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>Limpar recursos

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>Passos seguintes

Neste guia de introdução, aprendeu a criar uma conta do Azure Cosmos DB, a criar uma tabela com o Data Explorer e a executar uma aplicação.  Agora, pode consultar os dados com a API de Tabela.  

> [!div class="nextstepaction"]
> [Importar dados de tabela para a API de tabela](table-import.md)
