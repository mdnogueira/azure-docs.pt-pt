---
title: "Armazenar dados não estruturados e funções de base de dados do Azure Cosmos | Microsoft Docs"
description: "Armazenar dados não estruturados usando as funções do Azure e o Cosmos DB"
services: functions
documentationcenter: functions
author: ggailey777
manager: cfowler
editor: 
tags: 
keywords: "funções do azure, funções, processamento de eventos, Cosmos DB, computação dinâmica, arquitetura sem servidor"
ms.assetid: 
ms.service: functions
ms.devlang: csharp
ms.topic: quickstart
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 09/19/2017
ms.author: glenga
ms.custom: mvc
ms.openlocfilehash: b64d994dbc8f53418981e33a1dcd3cf513838b92
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="store-unstructured-data-using-azure-functions-and-azure-cosmos-db"></a>Armazenar dados não estruturados com as funções do Azure e Azure Cosmos DB

O [Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) é uma ótima forma de armazenar dados não estruturados e dados JSON. Combinado com funções do Azure, o Cosmos DB torna o armazenamento de dados rápido e fácil, sendo necessário menos códigos para armazenar dados numa base de dados relacional.

Nas Funções do Azure, os enlaces de entrada e saída proporcionam uma forma declarativa para ligar aos dados do serviço externo a partir da sua função. Neste tópico, saiba como atualizar uma função C# existente para adicionar um enlace de saída que armazena dados não estruturados num documento do Cosmos DB. 

![BD do Cosmos](./media/functions-integrate-store-unstructured-data-cosmosdb/functions-cosmosdb.png)

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial:

[!INCLUDE [Previous quickstart note](../../includes/functions-quickstart-previous-topics.md)]

## <a name="add-an-output-binding"></a>Adicionar um enlace de saída

1. Expanda a aplicação Function App e a função.

1. Selecione **Integrar** e **+ Nova Saída**, que pode encontrar no canto superior direito da página. Escolha **Azure Cosmos DB** e clique em **Selecionar**.

    ![Adicionar um enlace de saída do Cosmos DB](./media/functions-integrate-store-unstructured-data-cosmosdb/functions-integrate-tab-add-new-output-binding.png)

3. Utilize as definições da **Saída do Azure Cosmos DB** conforme especificado na tabela: 

    ![Configurar o enlace de saída do Cosmos DB](./media/functions-integrate-store-unstructured-data-cosmosdb/functions-integrate-tab-configure-cosmosdb-binding.png)

    | Definição      | Valor sugerido  | Descrição                                |
    | ------------ | ---------------- | ------------------------------------------ |
    | **Nome do parâmetro do documento** | taskDocument | Nome que se refere ao objeto do Cosmos DB no código. |
    | **Nome da base de dados** | taskDatabase | Nome da base de dados para guardar os documentos. |
    | **Nome da coleção** | TaskCollection | Nome da coleção da base de dados. |
    | **Se o valor for verdadeiro, cria a coleção e a base de dados do Cosmos DB** | Assinalado | A coleção ainda não existe, como tal, deve ser criada. |

4. Selecione **novo** junto a **ligação de documento de base de dados do Azure Cosmos** etiqueta e selecione **+ criar nova**. 

5. Utilize as definições de **Nova conta** conforme especificado na tabela: 

    ![Configurar a ligação ao Cosmos DB](./media/functions-integrate-store-unstructured-data-cosmosdb/functions-create-CosmosDB.png)

    | Definição      | Valor sugerido  | Descrição                                |
    | ------------ | ---------------- | ------------------------------------------ |
    | **ID** | Nome da base de dados | ID exclusivo para a base de dados de base de dados do Azure Cosmos  |
    | **API** | SQL (DocumentDB) | Selecione a API da base de dados de documentos.  |
    | **Subscrição** | Subscrição do Azure | Subscrição do Azure  |
    | **Grupo de Recursos** | myResourceGroup |  Utilize o grupo de recursos existente que contém a aplicação de funções. |
    | **Localização**  | WestEurope | Selecione uma localização perto da sua aplicação de funções ou de outras aplicações que utilizam os documentos armazenados.  |

6. Clique em **OK** para criar a base de dados. A criação da base de dados pode demorar alguns minutos. Depois de a base de dados ser criada, a cadeia de ligação da base de dados é armazenada como uma definição da aplicação de funções. O nome desta definição de aplicação é inserido no **ligação de conta de base de dados do Azure Cosmos**. 
 
8. Depois de a cadeia de ligação ser definida, selecione **Guardar** para criar o enlace.

## <a name="update-the-function-code"></a>Atualizar o código da função

Substitua o código da função C# existente pelo seguinte código:

```csharp
using System.Net;

public static HttpResponseMessage Run(HttpRequestMessage req, out object taskDocument, TraceWriter log)
{
    string name = req.GetQueryNameValuePairs()
        .FirstOrDefault(q => string.Compare(q.Key, "name", true) == 0)
        .Value;

    string task = req.GetQueryNameValuePairs()
        .FirstOrDefault(q => string.Compare(q.Key, "task", true) == 0)
        .Value;

    string duedate = req.GetQueryNameValuePairs()
        .FirstOrDefault(q => string.Compare(q.Key, "duedate", true) == 0)
        .Value;

    taskDocument = new {
        name = name,
        duedate = duedate.ToString(),
        task = task
    };

    if (name != "" && task != "") {
        return req.CreateResponse(HttpStatusCode.OK);
    }
    else {
        return req.CreateResponse(HttpStatusCode.BadRequest);
    }
}

```
Este exemplo de código lê as cadeias de consulta do Pedido de HTTP e atribui-as a campos no objeto `taskDocument`. O enlace `taskDocument` envia os dados do objeto deste parâmetro de enlace para serem armazenados na base de dados de documentos vinculada. A base de dados é criada quando a função for executada pela primeira vez.

## <a name="test-the-function-and-database"></a>Testar a função e a base de dados

1. Expanda a janela direita e selecione **Testar**. Em **Consulta**, clique em **+ Adicionar parâmetro** e adicione os seguintes parâmetros à cadeia de consulta:

    + `name`
    + `task`
    + `duedate`

2. Clique em **Executar** e certifique-se de que é devolvido um estado 200.

    ![Configurar o enlace de saída do Cosmos DB](./media/functions-integrate-store-unstructured-data-cosmosdb/functions-test-function.png)

1. No lado esquerdo do portal do Azure, expanda a barra de ícones, escreva `cosmos` no campo de pesquisa e selecione **Azure Cosmos DB**.

    ![Procurar o serviço Cosmos DB](./media/functions-integrate-store-unstructured-data-cosmosdb/functions-search-cosmos-db.png)

2. Escolha a sua conta de base de dados do Azure Cosmos, em seguida, selecione o **Explorador de dados**. 

3. Expanda os nós **Coleções**, selecione o novo documento e confirme se o documento contém os valores da cadeia de consulta, juntamente com alguns metadados adicionais. 

    ![Verifique a entrada no Cosmos DB](./media/functions-integrate-store-unstructured-data-cosmosdb/functions-verify-cosmosdb-output.png)

Adicionou com êxito um enlace para o acionador HTTP que armazena dados não estruturados numa base de dados do Azure Cosmos.

[!INCLUDE [Clean-up section](../../includes/clean-up-section-portal.md)]

## <a name="next-steps"></a>Passos seguintes

[!INCLUDE [functions-quickstart-next-steps](../../includes/functions-quickstart-next-steps.md)]

Para obter mais informações sobre o enlace a uma base de dados do Cosmos DB, veja [Enlaces do Cosmos DB das Funções do Azure](functions-bindings-documentdb.md).
