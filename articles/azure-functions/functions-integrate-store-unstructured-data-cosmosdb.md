---
title: "Armazenar dados não estruturados usando as funções do Azure e o Cosmos DB"
description: "Armazenar dados não estruturados usando as funções do Azure e o Cosmos DB"
services: functions
documentationcenter: functions
author: rachelappel
manager: erikre
editor: 
tags: 
keywords: "funções do azure, funções, processamento de eventos, Cosmos DB, computação dinâmica, arquitetura sem servidor"
ms.assetid: 
ms.service: functions
ms.devlang: multiple
ms.topic: get-started-article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 05/08/2017
ms.author: rachelap
ms.custom: mvc
ms.translationtype: Human Translation
ms.sourcegitcommit: 6adaf7026d455210db4d7ce6e7111d13c2b75374
ms.openlocfilehash: 785bd144805a472ae457f9a3323d512b5cbf055d
ms.contentlocale: pt-pt
ms.lasthandoff: 06/22/2017

---
<a id="store-unstructured-data-using-azure-functions-and-cosmos-db" class="xliff"></a>

# Armazenar dados não estruturados usando as funções do Azure e o Cosmos DB

O Azure Cosmos DB é uma ótima forma de armazenar dados não estruturados e JSON. Combinado com funções do Azure, o Cosmos DB torna o armazenamento de dados rápido e fácil, sendo necessário menos códigos para armazenar dados numa base de dados relacional.

Este tutorial explica como usar o Portal do Azure para criar uma função do Azure que armazena dados não estruturados num documento do Cosmos DB. 

<a id="prerequisites" class="xliff"></a>

## Pré-requisitos

[!INCLUDE [Previous quickstart note](../../includes/functions-quickstart-previous-topics.md)]

[!INCLUDE [functions-portal-favorite-function-apps](../../includes/functions-portal-favorite-function-apps.md)]

<a id="create-a-function" class="xliff"></a>

## Criar uma função

Crie um novo WebHook genérico C# com o nome `MyTaskList`.

1. Expanda a lista de funções existentes e, em seguida, clique no sinal + para criar uma nova função
1. Selecione GenericWebHook-CSharp e atribua-lhe um nome `MyTaskList`

![Adicione a nova aplicação de funções C# Generic WebHook](./media/functions-integrate-store-unstructured-data-cosmosdb/functions-create-new-functionapp.png)

<a id="add-an-output-binding" class="xliff"></a>

## Adicionar um enlace de saída

Uma função do Azure pode ter um acionador e qualquer número de enlaces de entrada ou saída. Neste exemplo, iremos usar um acionador de pedidos HTTP e o documento do Cosmos DB como enlace de saída.

1. Clique no separador *Integrar* da função para ver ou modificar o acionador e os enlaces da função.
1. Escolha a hiperligação *Nova saída* que se encontra na parte superior direita da página.

Nota: O acionador do pedido de HTTP já está configurado; no entanto, tem de adicionar o enlace do documento do Cosmos DB.

![Adicionar enlace de saída do Cosmos DB](./media/functions-integrate-store-unstructured-data-cosmosdb/functions-integrate-tab-add-new-output-binding.png)

1. Introduza as informações necessárias para criar o enlace. Use a tabela seguinte para determinar os valores.

![Configurar o enlace de saída do Cosmos DB](./media/functions-integrate-store-unstructured-data-cosmosdb/functions-integrate-tab-configure-cosmosdb-binding.png)

|  Campo | Valor  |
|---|---|
| Nome do parâmetro do documento | Nome que se refere ao objeto do Cosmos DB em código |
| Nome da base de dados | Nome da base de dados para guardar os documentos |
| Nome da coleção | Nome do agrupamento de bases de dados do Cosmos DB |
| Gostaria que o Cosmos DB e a coleção criados para si | Sim ou Não |
| Ligação à conta do Cosmos DB | Cadeia de ligação que remete para a base de dados do Cosmos DB |

Também deve configurar a ligação à base de dados do Cosmos DB.

1. Clique na hiperligação "Novo" junto à etiqueta *Ligação ao documento do Cosmos DB".
1. Preencha os campos e selecione as opções apropriadas necessárias para criar o documento do Cosmos DB.

![Configurar a ligação ao Cosmos DB](./media/functions-integrate-store-unstructured-data-cosmosdb/functions-create-CosmosDB.png)

|  Campo | Valor  |
|---|---|
| Id | ID exclusiva para a base de dados do Cosmos DB  |
| NoSQL API | Cosmos DB ou MongoDB  |
| Subscrição | Subscrição MSDN  |
| Grupo de Recursos  | Crie um novo grupo ou selecione um existente.  |
| Localização  | WestEurope  |

1. Clique no botão *OK*. Talvez seja necessário aguardar alguns minutos enquanto o Azure cria os recursos.
1. Clique no botão *Guardar*.

<a id="update-the-function-code" class="xliff"></a>

## Atualizar o código da função

Substitua o código de modelo da função com o seguinte:

Note que o código deste exemplo está apenas em C#.

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

Este exemplo de código lê as cadeias de consulta dos pedidos de HTTP e atribui-as como membros de uma objeto `taskDocument`. O objeto `taskDocument` guarda automaticamente os dados na base de dados do Cosmos DB e cria ainda a base de dados na primeira utilização.

<a id="test-the-function-and-database" class="xliff"></a>

## Testar a função e a base de dados

1. No separador de funções, clique na hiperligação *Teste* à direita do portal e introduza as seguintes cadeias de consulta de HTTP:

| Cadeias de consulta | Valor |
|---|---|
| nome | Chris P. Bacon |
| tarefa | Fazer uma sanduíche BLT |
| prazo | 05/12/2017 |

1. Clique na hiperligação *Executar*.
1. Verifique se a função devolveu um código de resposta *HTTP 200 OK*.

![Configurar o enlace de saída do Cosmos DB](./media/functions-integrate-store-unstructured-data-cosmosdb/functions-test-function.png)

Confirme se foi feita uma entrada na base de dados do Cosmos DB.

1. Localize a base de dados no portal do Azure e selecione-a.
1. Selecione a opção *Data Explorer*.
1. Expanda os nós até alcançar as entradas do documento.
1. Confirme a entrada na base de dados. Haverá metadados adicionais na base de dados juntamente com seus dados.

![Verifique a entrada no Cosmos DB](./media/functions-integrate-store-unstructured-data-cosmosdb/functions-verify-cosmosdb-output.png)

Se os dados estiverem incluídos no documento, então criou com êxito uma função do Azure que armazena dados não estruturados numa base de dados do Cosmos DB.

<a id="clean-up-resources" class="xliff"></a>

## Limpar recursos

[!INCLUDE [Next steps note](../../includes/functions-quickstart-cleanup.md)]

<a id="next-steps" class="xliff"></a>

## Passos seguintes

Para obter mais informações sobre Funções do Azure, veja os seguintes tópicos:

[!INCLUDE [Getting help note](../../includes/functions-get-help.md)]

[!INCLUDE [functions-quickstart-next-steps](../../includes/functions-quickstart-next-steps.md)]

