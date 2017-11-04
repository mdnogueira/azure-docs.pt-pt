---
title: "Acionador de temporizador de funções do Azure | Microsoft Docs"
description: "Compreenda como utilizar os acionadores de temporizador nas funções do Azure."
services: functions
documentationcenter: na
author: christopheranderson
manager: cfowler
editor: 
tags: 
keywords: "das funções do Azure, funções, processamento de eventos, computação dinâmica, arquitetura sem servidor"
ms.assetid: d2f013d1-f458-42ae-baf8-1810138118ac
ms.service: functions
ms.devlang: multiple
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 02/27/2017
ms.author: glenga
ms.custom: 
ms.openlocfilehash: 12beb090a95a31c7e83ae03a920016bdfbf474e3
ms.sourcegitcommit: c5eeb0c950a0ba35d0b0953f5d88d3be57960180
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/24/2017
---
# <a name="azure-functions-timer-trigger"></a>Acionador de temporizador de funções do Azure

[!INCLUDE [functions-selector-bindings](../../includes/functions-selector-bindings.md)]

Este artigo explica como configurar e acionadores de temporizador de código em funções do Azure. As funções do Azure tem um vínculo de Acionador de temporizador que lhe permite executar o código de função com base numa agenda definida. 

O acionador de temporizador suporta várias instâncias Escalamento horizontal. Uma única instância de uma função de temporizador específico é executada em todas as instâncias.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

<a id="trigger"></a>

## <a name="timer-trigger"></a>Acionador de temporizador
O acionador de temporizador para uma função utiliza o seguinte objeto JSON no `bindings` matriz de function.json:

```json
{
    "schedule": "<CRON expression - see below>",
    "name": "<Name of trigger parameter in function signature>",
    "type": "timerTrigger",
    "direction": "in"
}
```

O valor de `schedule` é um [expressão CRON](http://en.wikipedia.org/wiki/Cron#CRON_expression) que inclua estes seis campos: 

    {second} {minute} {hour} {day} {month} {day-of-week}
&nbsp;
>[!NOTE]   
>Muitas das expressões cron encontrará online omitir o `{second}` campo. Se copiar a partir de um deles, terá de ajustar o extra `{second}` campo. Para obter exemplos específicos, consulte [agendar exemplos](#examples) abaixo.

O fuso horário predefinido utilizado com as expressões de CRON está Hora Universal Coordenada (UTC). Para que a expressão de CRON baseado no fuso horário outro, crie uma nova definição de aplicação para a sua aplicação de função com o nome `WEBSITE_TIME_ZONE`. Defina o valor para o nome do fuso horário pretendido, conforme mostrado no [Microsoft fuso horário índice](https://technet.microsoft.com/library/cc749073(v=ws.10).aspx). 

Por exemplo, *hora padrão do Leste* é UTC-05:00. Ter o temporizador acionar fire em 10:00 EST diariamente, utilize a seguinte expressão CRON que as contas do fuso horário UTC:

```json
"schedule": "0 0 15 * * *",
``` 

Em alternativa, pode adicionar uma nova definição de aplicação para a sua aplicação de função com o nome `WEBSITE_TIME_ZONE` e defina o valor **hora padrão do Leste**.  Em seguida, a seguinte expressão de CRON poderiam ser utilizada para 10:00 AM EST: 

```json
"schedule": "0 0 10 * * *",
``` 


<a name="examples"></a>

## <a name="schedule-examples"></a>Exemplos de agenda
Seguem-se alguns exemplos de expressões de CRON para pode utilizar para o `schedule` propriedade. 

Para acionar uma vez a cada cinco minutos:

```json
"schedule": "0 */5 * * * *"
```

Para acionar uma vez na parte superior de cada hora:

```json
"schedule": "0 0 * * * *",
```

Para acionar uma vez a cada duas horas:

```json
"schedule": "0 0 */2 * * *",
```

Para acionar uma vez a cada hora de 09: 00 para as 17: 00:

```json
"schedule": "0 0 9-17 * * *",
```

Para acionar às 9:30 AM todos os dias:

```json
"schedule": "0 30 9 * * *",
```

Para acionar às 9:30 AM cada dia da semana:

```json
"schedule": "0 30 9 * * 1-5",
```

<a name="usage"></a>

## <a name="trigger-usage"></a>Utilização de Acionador
Quando uma função de Acionador de temporizador é invocada, o [objeto temporizador](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions/Extensions/Timers/TimerInfo.cs) é transmitida para a função. O seguinte JSON é uma representação de exemplo do objeto temporizador. 

```json
{
    "Schedule":{
    },
    "ScheduleStatus": {
        "Last":"2016-10-04T10:15:00.012699+00:00",
        "Next":"2016-10-04T10:20:00+00:00"
    },
    "IsPastDue":false
}
```

<a name="sample"></a>

## <a name="trigger-sample"></a>Exemplo de Acionador
Suponha que tem o acionador de temporizador seguintes o `bindings` matriz de function.json:

```json
{
    "schedule": "0 */5 * * * *",
    "name": "myTimer",
    "type": "timerTrigger",
    "direction": "in"
}
```

Consulte o exemplo de específicas do idioma que lê o objeto temporizador para ver se está a ser executado tarde.

* [C#](#triggercsharp)
* [F#](#triggerfsharp)
* [Node.js](#triggernodejs)

<a name="triggercsharp"></a>

### <a name="trigger-sample-in-c"></a>Exemplo de Acionador em c# #
```csharp
public static void Run(TimerInfo myTimer, TraceWriter log)
{
    if(myTimer.IsPastDue)
    {
        log.Info("Timer is running late!");
    }
    log.Info($"C# Timer trigger function executed at: {DateTime.Now}" );  
}
```

<a name="triggerfsharp"></a>

### <a name="trigger-sample-in-f"></a>Exemplo de Acionador em F # #
```fsharp
let Run(myTimer: TimerInfo, log: TraceWriter ) =
    if (myTimer.IsPastDue) then
        log.Info("F# function is running late.")
    let now = DateTime.Now.ToLongTimeString()
    log.Info(sprintf "F# function executed at %s!" now)
```

<a name="triggernodejs"></a>

### <a name="trigger-sample-in-nodejs"></a>Exemplo de Acionador no Node.js
```JavaScript
module.exports = function (context, myTimer) {
    var timeStamp = new Date().toISOString();

    if(myTimer.isPastDue)
    {
        context.log('Node.js is running late!');
    }
    context.log('Node.js timer trigger function ran!', timeStamp);   

    context.done();
};
```

## <a name="next-steps"></a>Passos seguintes
[!INCLUDE [next steps](../../includes/functions-bindings-next-steps.md)]

