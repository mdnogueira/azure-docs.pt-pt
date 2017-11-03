---
title: "Diagnóstico nas funções duráveis - Azure"
description: "Saiba como diagnosticar problemas com a extensão de funções durável para as funções do Azure."
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
ms.openlocfilehash: 5ebab8660dfe21984e1a7f9a1cb925aea60de213
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="diagnostics-in-durable-functions-azure-functions"></a>Diagnóstico nas funções duráveis (funções do Azure)

Existem várias opções para diagnosticar problemas com [funções durável](durable-functions-overview.md). Algumas destas opções são os mesmos para funções regulares e alguns deles são exclusivos para funções durável.

## <a name="application-insights"></a>Application Insights

[Application Insights](../application-insights/app-insights-overview.md) é a forma recomendada de diagnóstico e monitorização das funções do Azure. O mesmo se aplica a funções durável. Para obter uma descrição geral de como tirar partido do Application Insights na sua aplicação de função, consulte [das funções do Azure do Monitor](functions-monitoring.md).

A extensão de durável de funções do Azure também emite *eventos de controlo* que permitem a execução de ponto a ponto de uma orquestração de rastreio. Estes podem ser encontrados e consultados utilizando o [Application Insights Analytics](../application-insights/app-insights-analytics.md) ferramenta no portal do Azure.

### <a name="tracking-data"></a>Dados de controlo

Cada evento do ciclo de vida de uma instância de orquestração faz com que um evento de controlo para serem escritos para o **rastreios** coleção no Application Insights. Este evento contém um **customDimensions** payload com vários campos.  Os nomes de campo são prepended com `prop__`.

* **hubName**: O nome do hub de tarefa na qual o seu orchestrations estão em execução.
* **appName**: O nome da aplicação de função. Isto é útil quando tem várias aplicações de função partilha a mesma instância do Application Insights.
* **slotName**: O [ranhura de implementação](https://blogs.msdn.microsoft.com/appserviceteam/2017/06/13/deployment-slots-preview-for-azure-functions/) no qual a aplicação de função atual está em execução. Isto é útil quando, tirar partido das ranhuras de implementação para a versão dos orchestrations.
* **functionName**: O nome da função do orchestrator ou de atividade.
* **functionType**: O tipo de função, tal como **Orchestrator** ou **atividade**.
* **instanceId**: O ID exclusivo da instância de orquestração.
* **estado**: O estado de execução do ciclo de vida da instância. Os valores válidos incluem:
    * **Agendada**: A função foi agendada para execução, mas ainda não começou ainda a ser executado.
    * **Iniciado**: A função começar a ser executada, mas não foi ainda aguardado ou foi concluída.
    * **Aguardado**: O orchestrator tem agendada algum trabalho e está a aguardar a sua conclusão.
    * **Está à escuta**: O orchestrator está à escuta de uma notificação de evento externo.
    * **Concluir**: A função foi concluída com êxito.
    * **Não foi possível**: A função falhou com um erro.
* **motivo**: dados adicionais associados com o evento de controlo. Por exemplo, se uma instância está a aguardar uma notificação de evento externo, este campo indica o nome do evento que está a aguardar. Se uma função falhou, esta irá conter os detalhes do erro.
* **isReplay**: valor booleano que indica se o evento de controlo é para reproduzido pela execução.
* **extensionVersion**: A versão da extensão de tarefa durável. Isto é especialmente importantes dados ao reportar possíveis erros na extensão. Instâncias de execução longa podem reportar várias versões, se uma atualização ocorre enquanto estiver em execução. 

Pode ser configurada verbosidade de controlar dados emitidos para o Application Insights no `logger` secção o `host.json` ficheiro.

```json
{
    "logger": {
        "categoryFilter": {
            "categoryLevels": {
                "Host.Triggers.DurableTask": "Information"
            }
        }
    }
}
```

Por predefinição, todos os eventos de rastreio são emitidos. O volume de dados pode ser reduzido através da definição `Host.Triggers.DurableTask` para `"Warning"` ou `"Error"` caso em que eventos de controlo será apenas possível emitido para situações excecionais.

> [!WARNING]
> Por predefinição, a telemetria do Application Insights é amostragem pelo tempo de execução das funções do Azure para evitar a emitir dados com demasiada frequência. Isto pode fazer com que as informações de registo ser perdidas quando ocorrem muitos eventos de ciclo de vida num curto período de tempo. O [artigo de monitorização de funções do Azure](functions-monitoring.md#configure-sampling) explica como configurar este comportamento.

### <a name="single-instance-query"></a>Consulta de instância única

A consulta seguinte mostra os dados de histórico de controlo para uma única instância de [Hello sequência](durable-functions-sequence.md) funcionar orchestration. Que é escrito utilizando o [idioma de consulta de informações de aplicação (AIQL)](https://docs.loganalytics.io/docs/Language-Reference). -Filtra reprodução de execução para que apenas o *lógica* é apresentado o caminho de execução.

```AIQL
let targetInstanceId = "bf71335b26564016a93860491aa50c7f";
let start = datetime(2017-09-29T00:00:00);
traces
| where timestamp > start and timestamp < start + 30m
| where customDimensions.Category == "Host.Triggers.DurableTask"
| extend functionName = customDimensions["prop__functionName"]
| extend instanceId = customDimensions["prop__instanceId"]
| extend state = customDimensions["prop__state"]
| extend isReplay = tobool(tolower(customDimensions["prop__isReplay"]))
| where isReplay == false
| where instanceId == targetInstanceId
| project timestamp, functionName, state, instanceId, appName = cloud_RoleName
```
O resultado é uma lista de eventos que mostram o caminho de execução da orquestração, incluindo quaisquer funções de atividade de controlo.

![Consulta do Application Insights](media/durable-functions-diagnostics/app-insights-single-instance-query.png)

> [!NOTE]
> Alguns destes eventos de controlo podem ser fora de ordem devido à falta de precisão no `timestamp` coluna. Isto está a ser controlado no GitHub como [emitir #71](https://github.com/Azure/azure-functions-durable-extension/issues/71).

### <a name="instance-summary-query"></a>Consulta de resumo de instância

A consulta seguinte mostra o estado de todas as instâncias de orquestração com um intervalo de tempo especificado.

```AIQL
let start = datetime(2017-09-30T04:30:00);
traces
| where timestamp > start and timestamp < start + 1h
| where customDimensions.Category == "Host.Triggers.DurableTask"
| extend functionName = tostring(customDimensions["prop__functionName"])
| extend instanceId = tostring(customDimensions["prop__instanceId"])
| extend state = tostring(customDimensions["prop__state"])
| extend isReplay = tobool(tolower(customDimensions["prop__isReplay"]))
| extend output = tostring(customDimensions["prop__output"])
| where isReplay == false
| summarize arg_max(timestamp, *) by instanceId
| project timestamp, instanceId, functionName, state, output, appName = cloud_RoleName
| order by timestamp asc
```
O resultado é uma lista de IDs de instância e o respetivo estado atual do tempo de execução.

![Consulta do Application Insights](media/durable-functions-diagnostics/app-insights-single-summary-query.png)

## <a name="logging"></a>Registo

É importante manter o orchestrator comportamento de repetição em consideração ao escrever os registos diretamente a partir de uma função do orchestrator. Por exemplo, considere a seguinte função do orchestrator:

```cs
public static async Task Run(
    DurableOrchestrationContext ctx,
    TraceWriter log)
{
    log.Info("Calling F1.");
    await ctx.CallActivityAsync("F1");
    log.Info("Calling F2.");
    await ctx.CallActivityAsync("F2");
    log.Info("Calling F3");
    await ctx.CallActivityAsync("F3");
    log.Info("Done!");
}
```

Os dados de registo resultante vai ter um aspeto semelhante a seguinte:

```txt
Calling F1.
Calling F1.
Calling F2.
Calling F1.
Calling F2.
Calling F3.
Calling F1.
Calling F2.
Calling F3.
Done!
```

> [!NOTE]
> Lembre-se de que enquanto os registos de afirmação chamar F1, F2 e F3, essas funções só são *realmente* chamado pela primeira vez que forem encontrados. As chamadas subsequentes que ocorrem durante a reprodução são ignoradas e as saídas são reproduzidas para a lógica do orchestrator.

Se pretender apenas iniciar a execução de não repetição, pode escrever uma expressão condicional registo apenas se `IsReplaying` é `false`. Considere o exemplo acima, mas desta vez com verificações de reprodução.

```cs
public static async Task Run(
    DurableOrchestrationContext ctx,
    TraceWriter log)
{
    if (!ctx.IsReplaying) log.Info("Calling F1.");
    await ctx.CallActivityAsync("F1");
    if (!ctx.IsReplaying) log.Info("Calling F2.");
    await ctx.CallActivityAsync("F2");
    if (!ctx.IsReplaying) log.Info("Calling F3");
    await ctx.CallActivityAsync("F3");
    log.Info("Done!");
}
```
Com esta alteração, a saída de registo é o seguinte:

```txt
Calling F1.
Calling F2.
Calling F3.
Done!
```

## <a name="debugging"></a>Depuração

Funções do Azure suporta a depuração diretamente do código de função e que suportam as mesma reencaminhar acarreta durável funções, independentemente de em execução no Azure ou localmente. No entanto, existem alguns comportamentos a ter em consideração quando depuração:

* **Reprodução**: funções Orchestrator regularmente reprodução quando novas entradas são recebidas. Isto significa que um único *lógica* execução de uma função do orchestrator pode resultar em atingir o mesmo ponto de interrupção várias vezes, especialmente se estiver definido numa fase inicial no código da função.
* **Await**: sempre que um `await` é encontrado, gera o controlo para o distribuidor de estrutura de tarefas durável. Se esta for a primeira vez que um determinado `await` foi encontrado, a tarefa associada é *nunca* retomado. Porque nunca retoma a tarefa, avance *através de* o await (F10 no Visual Studio) não é possível que realmente. Avance através de só funciona quando uma tarefa está a ser reproduzida.
* **Tempos limite de mensagens**: funções durável internamente utiliza mensagens de fila para execução de unidade de funções do orchestrator e funções de atividade. Num ambiente de várias VMS, interrompendo para a depuração para períodos de tempo prolongados pode fazer com que outra VM recolher a mensagem, resultando em execução duplicada. Este comportamento não existe para funções de Acionador de fila regular bem, mas é importante para realçar neste contexto uma vez que as filas são um detalhe de implementação.

> [!TIP]
> Ao definir pontos de interrupção, se pretender apenas interromper a execução de não repetição, poderá definir um ponto de interrupção condicional que quebras apenas se for `IsReplaying` é `false`.

## <a name="storage"></a>Armazenamento

Por predefinição, as funções durável armazena o estado no armazenamento do Azure. Isto significa que pode inspecionar o estado da sua orchestrations através de ferramentas, como [Explorador de armazenamento do Microsoft Azure](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer).

![Captura de ecrã de Explorador de armazenamento do Azure](media/durable-functions-diagnostics/storage-explorer.png)

Isto é útil para depuração porque, ver exatamente o estado de uma orquestração pode estar a ser. Também podem ser examinadas mensagens nas filas de para saber o trabalho está pendente (ou bloqueada em alguns casos).

> [!WARNING]
> Embora seja conveniente ver o histórico de execução no table storage, evite colocar qualquer dependência nesta tabela. -Pode alterar como a extensão de funções durável medida que evolui.

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Saiba como utilizar os temporizadores duráveis](durable-functions-timers.md)