---
title: "referência de Host.JSON para as funções do Azure"
description: "Documentação de referência para o ficheiro de host.json das funções do Azure."
services: functions
author: tdykstra
manager: cfowler
editor: 
tags: 
keywords: 
ms.service: functions
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 11/09/2017
ms.author: tdykstra
ms.openlocfilehash: 522d0590595b0fc0fef503599f1677658f223bd8
ms.sourcegitcommit: cfd1ea99922329b3d5fab26b71ca2882df33f6c2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/30/2017
---
# <a name="hostjson-reference-for-azure-functions"></a>referência de Host.JSON para as funções do Azure

O *host.json* ficheiro de metadados contém opções de configuração globais que afetam todas as funções para uma aplicação de função. Este artigo apresenta as definições que estão disponíveis. O esquema JSON é http://json.schemastore.org/host.

Existem outras opções de configuração globais no [as definições de aplicação](functions-app-settings.md) e no [local.settings.json](functions-run-local.md#local-settings-file) ficheiro.

## <a name="sample-hostjson-file"></a>Ficheiro de host.json de exemplo

O exemplo seguinte *host.json* ficheiro tem todas as possíveis opções especificadas.

```json
{
    "aggregator": {
        "batchSize": 1000,
        "flushTimeout": "00:00:30"
    },
    "applicationInsights": {
        "sampling": {
          "isEnabled": true,
          "maxTelemetryItemsPerSecond" : 5
        }
    },
    "eventHub": {
      "maxBatchSize": 64,
      "prefetchCount": 256,
      "batchCheckpointFrequency": 1
    },
    "functions": [ "QueueProcessor", "GitHubWebHook" ],
    "functionTimeout": "00:05:00",
    "http": {
        "routePrefix": "api",
        "maxOutstandingRequests": 20,
        "maxConcurrentRequests": 10,
        "dynamicThrottlesEnabled": false
    },
    "id": "9f4ea53c5136457d883d685e57164f08",
    "logger": {
        "categoryFilter": {
            "defaultLevel": "Information",
            "categoryLevels": {
                "Host": "Error",
                "Function": "Error",
                "Host.Aggregator": "Information"
            }
        }
    },
    "queues": {
      "maxPollingInterval": 2000,
      "visibilityTimeout" : "00:00:30",
      "batchSize": 16,
      "maxDequeueCount": 5,
      "newBatchThreshold": 8
    },
    "serviceBus": {
      "maxConcurrentCalls": 16,
      "prefetchCount": 100,
      "autoRenewTimeout": "00:05:00"
    },
    "singleton": {
      "lockPeriod": "00:00:15",
      "listenerLockPeriod": "00:01:00",
      "listenerLockRecoveryPollingInterval": "00:01:00",
      "lockAcquisitionTimeout": "00:01:00",
      "lockAcquisitionPollingInterval": "00:00:03"
    },
    "tracing": {
      "consoleLevel": "verbose",
      "fileLoggingMode": "debugOnly"
    },
    "watchDirectories": [ "Shared" ],
}
```

As secções seguintes deste artigo explicam cada propriedade de nível superior. Todos os são opcionais, a menos que indicado em contrário.

## <a name="aggregator"></a>agregador

Especifica quantas invocações de função são agregados quando [calcular métricas para o Application Insights](functions-monitoring.md#configure-the-aggregator). 

```json
{
    "aggregator": {
        "batchSize": 1000,
        "flushTimeout": "00:00:30"
    }
}
```

|Propriedade  |Predefinição | Descrição |
|---------|---------|---------| 
|batchSize|1000|Número máximo de pedidos a agregar.| 
|flushTimeout|00:00:30|Tempo máximo período a agregar.| 

Invocações de função são agregadas quando o primeiro dos dois limita são atingidos.

## <a name="applicationinsights"></a>applicationInsights

Controlos a [funcionalidade de amostragem do Application Insights](functions-monitoring.md#configure-sampling).

```json
{
    "applicationInsights": {
        "sampling": {
          "isEnabled": true,
          "maxTelemetryItemsPerSecond" : 5
        }
    }
}
```

|Propriedade  |Predefinição | Descrição |
|---------|---------|---------| 
|IsEnabled|False|Ativa ou desativa a amostragem.| 
|maxTelemetryItemsPerSecond|5|O limiar em que a amostragem começa.| 

## <a name="eventhub"></a>EventHub

Definições de configuração para [Hub de eventos de acionadores e enlaces](functions-bindings-event-hubs.md).

[!INCLUDE [functions-host-json-event-hubs](../../includes/functions-host-json-event-hubs.md)]

## <a name="functions"></a>funções

Uma lista de funções que o anfitrião de tarefa será executada.  Uma matriz vazia significa executar todas as funções.  Concebidos para utilização apenas quando [executar localmente](functions-run-local.md). Nas aplicações de função, utilize o *function.json* `disabled` propriedade em vez desta propriedade no *host.json*.

```json
{
    "functions": [ "QueueProcessor", "GitHubWebHook" ]
}
```

## <a name="functiontimeout"></a>functionTimeout

Indica a duração de tempo limite para todas as funções. Planos de consumo, o intervalo válido é entre 1 segundo para 10 minutos e o valor predefinido é de 5 minutos. Planos do App Service, não existe nenhum limite e o valor predefinido é nulo, que indica que não há tempo limite.

```json
{
    "functionTimeout": "00:05:00"
}
```

## <a name="http"></a>http

Definições de configuração para [http acionadores e enlaces](functions-bindings-http-webhook.md).

[!INCLUDE [functions-host-json-http](../../includes/functions-host-json-http.md)]

## <a name="id"></a>ID

O ID exclusivo de um anfitrião de tarefa. Pode ser uma minúscula GUID com traços removida. É necessário quando executar localmente. Ao executar as funções do Azure, é gerado automaticamente um ID se `id` for omitido.

```json
{
    "id": "9f4ea53c5136457d883d685e57164f08"
}
```

## <a name="logger"></a>Registo

Controlos de filtragem para os registos escritos por um [ILogger objeto](functions-monitoring.md#write-logs-in-c-functions) ou pelo [context.log](functions-monitoring.md#write-logs-in-javascript-functions).

```json
{
    "logger": {
        "categoryFilter": {
            "defaultLevel": "Information",
            "categoryLevels": {
                "Host": "Error",
                "Function": "Error",
                "Host.Aggregator": "Information"
            }
        }
    }
}
```

|Propriedade  |Predefinição | Descrição |
|---------|---------|---------| 
|categoryFilter|n/d|Especifica a filtragem por categoria| 
|defaultLevel|Informações|Para qualquer categorias não especificadas no `categoryLevels` matriz, enviar registos de neste nível e superior para o Application Insights.| 
|categoryLevels|n/d|Uma matriz de categorias que especifica o nível de registo mínimo para enviar para o Application Insights para cada categoria. A categoria especificada aqui controla todas as categorias que começam com o mesmo valor e os valores mais têm precedência. No exemplo anterior *host.json* ficheiro, todas as categorias que começam por "Host.Aggregator" registo `Information` nível. Todas as outras categorias que começam por "Anfitrião", tais como "Host.Executor", inicie sessão em `Error` nível.| 

## <a name="queues"></a>Filas

Definições de configuração para [armazenamento fila acionadores e enlaces](functions-bindings-storage-queue.md).

```json
{
    "queues": {
      "maxPollingInterval": 2000,
      "visibilityTimeout" : "00:00:30",
      "batchSize": 16,
      "maxDequeueCount": 5,
      "newBatchThreshold": 8
    }
}
```

|Propriedade  |Predefinição | Descrição |
|---------|---------|---------| 
|maxPollingInterval|60000|O intervalo máximo em milissegundos entre inquéritos de fila de mensagens em fila.| 
|visibilityTimeout|0|O intervalo de tempo entre tentativas durante o processamento de uma mensagem de falha.| 
|batchSize|16|O número de mensagens de fila para obter e processar em paralelo. O valor máximo é 32.| 
|maxDequeueCount|5|O número de vezes para tentar processar uma mensagem antes de passar para a fila nocivas.| 
|newBatchThreshold|batchSize/2|O limiar em que um novo lote de mensagens em fila são obtidas.| 

## <a name="servicebus"></a>Barramento de serviço

Definição de configuração para [Service Bus acionadores e enlaces](functions-bindings-service-bus.md).

[!INCLUDE [functions-host-json-service-bus](../../includes/functions-host-json-service-bus.md)]

## <a name="singleton"></a>singleton

Definições de configuração para o comportamento de bloqueio de Singleton. Para obter mais informações, consulte [problema no GitHub sobre o suporte de singleton](https://github.com/Azure/azure-webjobs-sdk-script/issues/912).

```json
    "singleton": {
      "lockPeriod": "00:00:15",
      "listenerLockPeriod": "00:01:00",
      "listenerLockRecoveryPollingInterval": "00:01:00",
      "lockAcquisitionTimeout": "00:01:00",
      "lockAcquisitionPollingInterval": "00:00:03"
    }
}
```

|Propriedade  |Predefinição | Descrição |
|---------|---------|---------| 
|lockPeriod|00:00:15|O período de bloqueios de nível de função efetuadas para. Os bloqueios a renovação automática.| 
|listenerLockPeriod|00:01:00|O período de bloqueios de serviço de escuta efetuadas para.| 
|listenerLockRecoveryPollingInterval|00:01:00|O intervalo de tempo utilizado para a recuperação de bloqueio do serviço de escuta, se não foi possível adquirir um bloqueio de serviço de escuta no arranque.| 
|lockAcquisitionTimeout|00:01:00|O período de tempo máximo de tempo de execução irá tentar adquirir um bloqueio.| 
|lockAcquisitionPollingInterval|n/d|O intervalo entre tentativas de aquisição do bloqueio.| 

## <a name="tracing"></a>rastreio

Definições de configuração para os registos que criar utilizando um `TraceWriter` objeto. Consulte [c# registo](functions-reference-csharp.md#logging) e [Node.js registo](functions-reference-node.md#writing-trace-output-to-the-console). 

```json
{
    "tracing": {
      "consoleLevel": "verbose",
      "fileLoggingMode": "debugOnly"
    }
}
```

|Propriedade  |Predefinição | Descrição |
|---------|---------|---------| 
|consoleLevel|informações|O nível de rastreio para o registo de consola. As opções são: `off`, `error`, `warning`, `info`, e `verbose`.|
|fileLoggingMode|debugOnly|O nível de rastreio para o registo de ficheiros. As opções são `never`, `always`, `debugOnly`.| 

## <a name="watchdirectories"></a>watchDirectories

Um conjunto de [partilhado diretórios do código](functions-reference-csharp.md#watched-directories) que devem ser monitorizados para as alterações.  Garante que quando um código no diretórios é alterado, as alterações são captadas pelas suas funções.

```json
{
    "watchDirectories": [ "Shared" ]
}
```

## <a name="durabletask"></a>durableTask

[Hub de tarefa](durable-functions-task-hubs.md) nome para [funções durável](durable-functions-overview.md).

```json
{
  "durableTask": {
    "HubName": "MyTaskHub"
  }
}
```

Os nomes de hub de tarefas tem de começar com uma letra e incluir apenas letras e números. Se não for especificado, o nome de hub de tarefa de predefinido para uma aplicação de função é **DurableFunctionsHub**. Para obter mais informações, consulte [tarefas hubs](durable-functions-task-hubs.md).


## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Saiba como atualizar o ficheiro host.json](functions-reference.md#fileupdate)

> [!div class="nextstepaction"]
> [Consulte definições globais de variáveis de ambiente](functions-app-settings.md)
