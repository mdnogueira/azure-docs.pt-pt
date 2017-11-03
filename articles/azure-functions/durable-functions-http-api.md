---
title: "APIs de HTTP nas funções duráveis - Azure"
description: "Saiba como implementar HTTP APIs na extensão do durável funções para as funções do Azure."
services: functions
author: cgillum
manager: cfowler
editor: 
tags: 
keywords: 
ms.service: functions
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 09/29/2017
ms.author: azfuncdf
ms.openlocfilehash: bb5361022e4c9693812753ae33df5aeb037b5aaa
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="http-apis-in-durable-functions-azure-functions"></a>APIs de HTTP nas funções duráveis (funções do Azure)

A extensão de tarefa durável expõe um conjunto de APIs de HTTP que podem ser utilizados para efetuar as seguintes tarefas:

* Obter o estado de uma instância de orquestração.
* Envie um evento para uma instância de orquestração de espera.
* Termine uma instância de orquestração em execução.

Cada um destas APIs de HTTP são as operações de webhook que são processadas diretamente pela extensão de tarefa durável. Não são específicas para qualquer função na aplicação de função.

> [!NOTE]
> Estas operações também podem ser invocadas diretamente utilizando as APIs de gestão de instância no [DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html) classe. Para obter mais informações, consulte [instância gestão](durable-functions-instance-management.md).

## <a name="http-api-url-discovery"></a>Deteção de URL de HTTP da API

O [DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html) classe expõe um [CreateCheckStatusResponse](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_CreateCheckStatusResponse_) API que pode ser utilizada para gerar um payload de resposta HTTP que contém ligações para todas as operações suportadas. Eis um exemplo de função de Acionador de HTTP que demonstra como utilizar esta API:

[!code-csharp[Main](~/samples-durable-functions/samples/csx/HttpStart/run.csx)]

Esta função de exemplo produz os dados da resposta JSON seguintes. O tipo de dados de todos os campos é `string`.

| Campo             |Descrição                           |
|-------------------|--------------------------------------|
| ID                |O ID da instância de orquestração. |
| statusQueryGetUri |O URL de estado da instância de orquestração. |
| sendEventPostUri  |O URL "emitir um evento" da instância de orquestração. |
| terminatePostUri  |O URL da instância do orchestration "fechado". |

Eis um exemplo de resposta:

```http
HTTP/1.1 202 Accepted
Content-Length: 923
Content-Type: application/json; charset=utf-8
Location: https://{host}/webhookextensions/handler/DurableTaskExtension/instances/34ce9a28a6834d8492ce6a295f1a80e2?taskHub=DurableFunctionsHub&connection=Storage&code=XXX

{
    "id":"34ce9a28a6834d8492ce6a295f1a80e2",
    "statusQueryGetUri":"https://{host}/webhookextensions/handler/DurableTaskExtension/instances/34ce9a28a6834d8492ce6a295f1a80e2?taskHub=DurableFunctionsHub&connection=Storage&code=XXX",
    "sendEventPostUri":"https://{host}/webhookextensions/handler/DurableTaskExtension/instances/34ce9a28a6834d8492ce6a295f1a80e2/raiseEvent/{eventName}?taskHub=DurableFunctionsHub&connection=Storage&code=XXX",
    "terminatePostUri":"https://{host}/webhookextensions/handler/DurableTaskExtension/instances/34ce9a28a6834d8492ce6a295f1a80e2/terminate?reason={text}&taskHub=DurableFunctionsHub&connection=Storage&code=XXX"
}
```
> [!NOTE]
> O formato do URL do webhook pode divergir, consoante a versão de anfitrião das funções do Azure está a executar. O exemplo acima é para o anfitrião 2.0 de funções do Azure.

## <a name="async-operation-tracking"></a>Operação assíncrona de controlo

A resposta HTTP que foi mencionada anteriormente foi concebida para ajudar a implementar assíncrona HTTP de execução longa APIs com funções durável. Isto é por vezes denominado o *consulta padrão de consumidor*. O fluxo de cliente/servidor funciona da seguinte forma:

1. O cliente emite um pedido HTTP ao iniciar um processo de execução longa, tal como uma função do orchestrator.
2. O acionador de destino HTTP devolve uma resposta de HTTP 202 com um `Location` cabeçalho com o `statusQueryGetUri` valor.
3. O cliente consulta no URL no `Location` cabeçalho. Este continua a ver respostas de HTTP 202 com um `Location` cabeçalho.
4. Quando a instância é concluído (ou falhas), o ponto final no `Location` cabeçalho devolve HTTP 200.

Este protocolo permite que a coordenação de processos de execução longa com clientes externos ou serviços que suportam um ponto final de HTTP de consulta e seguir o `Location` cabeçalho. As peças fundamentais já estão incorporadas nas APIs de HTTP de funções durável.

> [!NOTE]
> Por predefinição, todas as ações baseado em HTTP fornecida pelo [Azure Logic Apps](https://azure.microsoft.com/services/logic-apps/) suportam o padrão de operação assíncrona padrão. Isto torna possível incorporar uma função durável de longa execução como parte de um fluxo de trabalho Logic Apps. Obter mais detalhes sobre as Logic Apps suportem para padrões HTTP assíncronos podem ser encontrados no [documentação de ações de acionadores de fluxo de trabalho do Azure Logic Apps](../logic-apps/logic-apps-workflow-actions-triggers.md#asynchronous-patterns).

## <a name="http-api-reference"></a>Referência da API de HTTP

Todos os APIs de HTTP implementado por tomar a extensão os seguintes parâmetros. O tipo de dados de todos os parâmetros é `string`.

| Parâmetro  | Tipo de parâmetro  | Descrição |
|------------|-----------------|-------------|
| instanceId | URL             | O ID da instância de orquestração. |
| taskHub    | Cadeia de consulta    | O nome do [hub tarefas](durable-functions-task-hubs.md). Se não for especificado, é assumido o nome de hub de tarefas da aplicação de função atual. |
| ligação | Cadeia de consulta    | O **nome** da cadeia de ligação para a conta de armazenamento. Se não for especificado, é assumida a cadeia de ligação predefinido para a aplicação de função. |
| systemKey  | Cadeia de consulta    | A chave de autorização necessária para invocar a API. |

`systemKey`é uma chave de autorização gerada automaticamente pelo anfitrião das funções do Azure. Conceda acesso para a extensão de tarefa durável APIs especificamente e podem ser gerido da mesma forma que [outras chaves de autorização](https://github.com/Azure/azure-webjobs-sdk-script/wiki/Key-management-API). A forma mais simples para detetar o `systemKey` valor é utilizando o `CreateCheckStatusResponse` API mencionado anteriormente.

As secções seguintes alguns cobrir específicos APIs de HTTP suportada pela extensão e fornecem exemplos de como pode ser utilizados.

### <a name="get-instance-status"></a>Obter o estado de instância

Obtém o estado de uma instância de orquestração especificado.

#### <a name="request"></a>Pedir

Para as funções 1.0, o formato do pedido é:

```http
GET /admin/extensions/DurableTaskExtension/instances/{instanceId}?taskHub={taskHub}&connection={connection}&code={systemKey}
```

O formato de funções 2.0 tem as mesmas parâmetros, mas tem um prefixo de URL ligeiramente diferente:

```http
GET /webhookextensions/handler/DurableTaskExtension/instances/{instanceId}?taskHub={taskHub}&connection={connection}&code={systemKey}
```

#### <a name="response"></a>Resposta

É possível devolver vários valores de código de estado possíveis.

* **HTTP 200 (OK)**: A instância especificada está num estado concluído.
* **HTTP 202 (aceite)**: A instância especificada está em curso.
* **HTTP 400 (pedido incorreto)**: A instância especificada falhou ou foi terminada.
* **HTTP 404 (não for encontrado)**: A instância especificada não existe ou não iniciado em execução.

O payload de resposta para o **HTTP 200** e **HTTP 202** casos é um objeto JSON com os seguintes campos.

| Campo           | Tipo de dados | Descrição |
|-----------------|-----------|-------------|
| runtimeStatus   | Cadeia    | O estado do tempo de execução da instância. Os valores incluem *executar*, *pendente*, *falha*, *cancelado*, *Terminated*, *Concluída*. |
| Entrada           | JSON      | Os dados JSON utilizados para inicializar a instância. |
| Saída          | JSON      | A saída JSON da instância. Este campo é `null` se a instância não é um estado concluído. |
| createdTime     | Cadeia    | A hora em que foi criada a instância. Utiliza ISO 8601 expandido notação. |
| LastUpdatedTime | Cadeia    | A hora em que a instância persistente pela última vez. Utiliza ISO 8601 expandido notação. |

Eis um exemplo de respostas o payload (formatado para legibilidade):

```json
{
  "runtimeStatus": "Completed",
  "input": null,
  "output": [
    "Hello Tokyo!",
    "Hello Seattle!",
    "Hello London!"
  ],
  "createdTime": "2017-10-06T18:30:24Z",
  "lastUpdatedTime": "2017-10-06T18:30:30Z"
}
```

O **HTTP 202** resposta também inclui um **localização** cabeçalho de resposta que referencia o mesmo URL que o `statusQueryGetUri` campo mencionado anteriormente.

### <a name="raise-event"></a>Emitir um evento

Envia uma mensagem de notificação de evento para uma instância de orquestração em execução.

#### <a name="request"></a>Pedir

Para as funções 1.0, o formato do pedido é:

```http
POST /admin/extensions/DurableTaskExtension/instances/{instanceId}/raiseEvent/{eventName}?taskHub=DurableFunctionsHub&connection={connection}&code={systemKey}
```

O formato de funções 2.0 tem as mesmas parâmetros, mas tem um prefixo de URL ligeiramente diferente:

```http
POST /webhookextensions/handler/DurableTaskExtension/instances/{instanceId}/raiseEvent/{eventName}?taskHub=DurableFunctionsHub&connection={connection}&code={systemKey}
```

Os parâmetros para esta API incluem o conjunto predefinido mencionado anteriormente, bem como os seguintes parâmetros exclusivos do pedido.

| Campo       | Tipo de parâmetro  | TType de dados | Descrição |
|-------------|-----------------|-----------|-------------|
| EventName   | URL             | Cadeia    | O nome do evento que a instância de orquestração de destino está a aguardar no. |
| {conteúdo}   | Conteúdo do pedido | JSON      | O payload de evento formatada em JSON. |

#### <a name="response"></a>Resposta

É possível devolver vários valores de código de estado possíveis.

* **HTTP 202 (aceite)**: O evento gerado foi aceite para processamento.
* **HTTP 400 (pedido incorreto)**: O conteúdo do pedido não era do tipo `application/json` ou não era um JSON válido.
* **HTTP 404 (não for encontrado)**: A instância especificada não foi encontrada.
* **HTTP 410 (Gone)**: A instância especificada foi concluída ou falhada e não pode processar quaisquer eventos gerados.

Eis um exemplo de pedido que envia a cadeia JSON `"incr"` para uma instância de esperar que um evento com o nome **operação** (obtidas a partir de [contador](durable-functions-counter.md) exemplo):

```
POST /admin/extensions/DurableTaskExtension/instances/bcf6fb5067b046fbb021b52ba7deae5a/raiseEvent/operation?taskHub=DurableFunctionsHub&connection=Storage&code=XXX
Content-Type: application/json
Content-Length: 6

"incr"
```

As respostas para esta API não contém qualquer conteúdo.

### <a name="terminate-instance"></a>Terminar a instância

Termina uma instância de orquestração em execução.

#### <a name="request"></a>Pedir

Para as funções 1.0, o formato do pedido é:

```http
DELETE /admin/extensions/DurableTaskExtension/instances/{instanceId}/terminate?reason={reason}&taskHub={taskHub}&connection={connection}&code={systemKey}
```

O formato de funções 2.0 tem as mesmas parâmetros, mas tem um prefixo de URL ligeiramente diferente:

```http
DELETE /webhookextensions/handler/DurableTaskExtension/instances/{instanceId}/terminate?reason={reason}&taskHub={taskHub}&connection={connection}&code={systemKey}
```

O pedido parâmetros para esta API incluem o conjunto predefinido mencionado anteriormente, bem como o seguinte parâmetro exclusivo.

| Campo       | Tipo de parâmetro  | Tipo de dados | Descrição |
|-------------|-----------------|-----------|-------------|
| Razão      | Cadeia de consulta    | Cadeia    | Opcional. O motivo para terminar a instância de orquestração. |

#### <a name="response"></a>Resposta

É possível devolver vários valores de código de estado possíveis.

* **HTTP 202 (aceite)**: O pedido de terminação foi aceite para processamento.
* **HTTP 404 (não for encontrado)**: A instância especificada não foi encontrada.
* **HTTP 410 (Gone)**: A instância especificada foi concluída ou falhada.

Eis um exemplo de pedido que termina uma instância em execução e especifica um motivo de **buggy**:

```
DELETE /admin/extensions/DurableTaskExtension/instances/bcf6fb5067b046fbb021b52ba7deae5a/terminate?reason=buggy&taskHub=DurableFunctionsHub&connection=Storage&code=XXX
```

As respostas para esta API não contém qualquer conteúdo.

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Saber como lidar com erros](durable-functions-error-handling.md)
