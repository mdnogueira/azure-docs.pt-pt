---
title: "Criar uma função acionada por base de dados do Azure Cosmos | Microsoft Docs"
description: "Utilize as funções do Azure para criar uma função sem servidor que é invocada quando dados são adicionados a uma base de dados na base de dados do Azure Cosmos."
services: azure-functions
documentationcenter: na
author: ggailey777
manager: cfowler
editor: 
tags: 
ms.assetid: bc497d71-75e7-47b1-babd-a060a664adca
ms.service: functions; cosmos-db
ms.devlang: multiple
ms.topic: quickstart
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 10/02/2017
ms.author: glenga
ms.custom: 
ms.openlocfilehash: 1ff4c2e024faba777fc479b3cd5864e097bbfce1
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="create-a-function-triggered-by-azure-cosmos-db"></a>Criar uma função acionada por base de dados do Azure Cosmos

Saiba como criar uma função acionada quando sendo adicionados dados ao ou alterados na base de dados do Azure Cosmos. Para saber mais sobre a BD do Cosmos do Azure, consulte [BD do Cosmos do Azure: através das funções do Azure de computação de base de dados sem servidor](..\cosmos-db\serverless-computing-database.md).

![Ver mensagem nos registos.](./media/functions-create-cosmos-db-triggered-function/quickstart-completed.png)

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial:

+ Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

[!INCLUDE [functions-portal-favorite-function-apps](../../includes/functions-portal-favorite-function-apps.md)]

## <a name="create-an-azure-function-app"></a>Criar uma aplicação de Funções do Azure

[!INCLUDE [Create function app Azure portal](../../includes/functions-create-function-app-portal.md)]

Em seguida, vai criar uma função na aplicação Function App nova.

<a name="create-function"></a>

## <a name="create-azure-cosmos-db-trigger"></a>Criar o acionador de base de dados do Azure Cosmos

1. Expanda a aplicação de funções e clique no botão **+**, junto a **Funções**. Se esta for a primeira função na sua aplicação de funções, selecione **Função personalizada**. É apresentado o conjunto completo de modelos de função.

    ![Início rápido das funções no portal do Azure](./media/functions-create-cosmos-db-triggered-function/add-first-function.png)

2. Localize e selecione o **Azure CosmosDBTrigger** modelo para o idioma pretendido.

    ![Criar a função de base de dados do Cosmos Azure acionada](./media/functions-create-cosmos-db-triggered-function/select-cosmos-db-trigger-portal.png)

3. Configure o novo acionador com as definições conforme especificado na tabela abaixo a imagem.

    ![Criar a função de base de dados do Cosmos Azure acionada](./media/functions-create-cosmos-db-triggered-function/functions-cosmosdb-trigger-settings.png)
    
    | Definição      | Valor sugerido  | Descrição                                |
    | ------------ | ---------------- | ------------------------------------------ |
    | **Dar um nome à função** | Predefinição | Utilize o nome de função predefinido sugerido pelo modelo. |
    | **Nome da base de dados** | Tarefas | Nome da base de dados com a coleção para ser monitorizada. |
    | **Nome da coleção** | Itens | Nome da coleção para ser monitorizada. |
    | **Criar coleção de concessão, se não existir** | Assinalado | A coleção ainda não existe, como tal, deve ser criada. |

4. Selecione **novo** junto a **ligação de conta de base de dados do Azure Cosmos** etiqueta e escolha uma conta de base de dados do Cosmos existente ou **+ criar nova**. 
 
    ![Configurar a ligação de base de dados do Azure Cosmos](./media/functions-create-cosmos-db-triggered-function/functions-create-CosmosDB.png)

6. Quando criar uma nova conta de base de dados do Cosmos, utilize o **nova conta** definições conforme especificado na tabela.

    | Definição      | Valor sugerido  | Descrição                                |
    | ------------ | ---------------- | ------------------------------------------ |
    | **ID** | Nome da base de dados | ID exclusivo para a base de dados de base de dados do Azure Cosmos  |
    | **API** | SQL (DocumentDB) | Este tópico utiliza a base de dados de documento API.  |
    | **Subscrição** | Subscrição do Azure | Subscrição do Azure  |
    | **Grupo de Recursos** | myResourceGroup |  Utilize o grupo de recursos existente que contém a aplicação de funções. |
    | **Localização**  | WestEurope | Selecione uma localização perto da sua aplicação de funções ou de outras aplicações que utilizam os documentos armazenados.  |

6. Clique em **OK** para criar a base de dados. A criação da base de dados pode demorar alguns minutos. Depois de a base de dados ser criada, a cadeia de ligação da base de dados é armazenada como uma definição da aplicação de funções. O nome desta definição de aplicação é inserido no **ligação de conta de base de dados do Azure Cosmos**. 

7. Clique em **criar** criar a base de dados do Azure Cosmos acionada função. Depois da função é criada, é apresentado o código de função baseada no modelo.  

    ![Modelo de função do cosmos DB em c#](./media/functions-create-cosmos-db-triggered-function/function-cosmosdb-template.png)

    Este modelo de função escreve o número de documentos e o ID do documento primeiro os registos. 

Em seguida, ligar à sua conta de base de dados do Azure Cosmos e criar o **tarefas** coleção na base de dados. 

## <a name="create-the-items-collection"></a>Criar a coleção de itens

1. Inicie uma segunda instância do [portal do Azure](https://portal.azure.com) num novo separador no browser. 

2. No lado esquerdo do portal, expanda a barra de ícone, tipo `cosmos` no campo de procura e selecione **Azure Cosmos DB**.

    ![Pesquisa para o serviço de base de dados do Azure Cosmos](./media/functions-create-cosmos-db-triggered-function/functions-search-cosmos-db.png)

2. Escolha a sua conta de base de dados do Azure Cosmos, em seguida, selecione o **Explorador de dados**. 
 
3. No **coleções**, escolha **taskDatabase** e selecione **nova coleção**.

    ![Criar uma coleção](./media/functions-create-cosmos-db-triggered-function/cosmosdb-create-collection.png)

4. No **adicionar coleção**, utilize as definições apresentadas na tabela abaixo a imagem. 
 
    ![Definir o taskCollection](./media/functions-create-cosmos-db-triggered-function/cosmosdb-create-collection2.png)
 
    | Definição|Valor sugerido|Descrição |
    | ---|---|--- |
    | **ID de base de dados** | Tarefas |O nome da base de dados nova. Isto deve corresponder ao nome definido no enlace de função. |
    | **ID de coleção** | Itens | O nome para a nova coleção. Isto deve corresponder ao nome definido no enlace de função.  |
    | **Capacidade de armazenamento** | Fixa (10 GB)|Utilize o valor predefinido. Este valor é a capacidade de armazenamento da base de dados. |
    | **Débito** |400 RU| Utilize o valor predefinido. Se pretender reduzir a latência, pode aumentar o débito mais tarde. |
    | **[Chave de partição](../cosmos-db/partition-data.md#design-for-partitioning)** | /categoria|Uma chave de partição que distribui uniformemente os dados para cada partição. É importante selecionar a chave de partição correta para criar coleções com bom desempenho. | 

1. Clique em **OK** para criar o **tarefas** coleção. Pode demorar um curto período de tempo para a coleção a ser criados.

Depois da coleção especificada no enlace de função existe, pode testar a função adicionando documentos para esta nova coleção.

## <a name="test-the-function"></a>Testar a função

1. Expanda o novo **taskCollection** coleção no Explorador de dados, escolha **documentos**, em seguida, selecione **novo documento**.

    ![Criar um documento no taskCollection](./media/functions-create-cosmos-db-triggered-function/create-document-in-collection.png)

2. Substitua o conteúdo do documento novo com o seguinte conteúdo, em seguida, escolha **guardar**.

        {
            "id": "task1",
            "category": "general",
            "description": "some task"
        }

1. Mudar para o primeiro separador do browser que contém a função no portal. Expanda os registos de função e certifique-se de que o novo documento foi acionada a função. Ver o `task1` valor de ID do documento é escrito nos registos. 

    ![Ver mensagem nos registos.](./media/functions-create-cosmos-db-triggered-function/functions-cosmosdb-trigger-view-logs.png)

4. (Opcional) Aceda ao seu documento, efetuar uma alteração e clique em **atualização**. Em seguida, volte atrás para os registos de função e certifique-se de que a atualização também foi acionada a função.

## <a name="clean-up-resources"></a>Limpar recursos

[!INCLUDE [Next steps note](../../includes/functions-quickstart-cleanup.md)]

## <a name="next-steps"></a>Passos seguintes

Foi criada com uma função que é executado quando um documento é adicionado ou modificado na sua base de dados do Azure Cosmos.

[!INCLUDE [Next steps note](../../includes/functions-quickstart-next-steps.md)]

Para obter mais informações sobre os acionadores do Armazenamento de filas, veja [Azure Functions Storage queue bindings](functions-bindings-storage-queue.md) (Enlaces da fila de Armazenamento das Funções do Azure).
