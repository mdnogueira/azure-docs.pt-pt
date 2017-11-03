---
title: "Controlo de versões nas funções duráveis - Azure"
description: "Saiba como implementar o controlo de versões na extensão do durável funções para as funções do Azure."
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
ms.openlocfilehash: 0a86e4a87f5ec23c284aa4e5cfb2c67622b3ebe9
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="versioning-in-durable-functions-azure-functions"></a>Controlo de versões nas funções duráveis (funções do Azure)

É inevitável que funções serão adicionadas, removidas e alteradas ao longo da duração de uma aplicação. [Funções duráveis](durable-functions-overview.md) permite encadeamento funciona em conjunto de formas que não foram anteriormente possíveis e este encadeamento afeta a forma como pode processar o controlo de versões.

## <a name="how-to-handle-breaking-changes"></a>Como processar alterações

Existem vários exemplos de alterações a ter em consideração. Este artigo aborda as mais comuns. O tema principal por trás de todos eles é que ambos os orchestrations função novas e existentes são afetadas por alterações ao código de função.

### <a name="changing-activity-function-signatures"></a>Assinaturas de função de atividade de alteração

Uma alteração de assinatura refere-se a uma alteração no nome, entrada ou saída de uma função. Se este tipo de alteração é feito para uma função de atividade, poderá interromper a função do orchestrator que depende dele. Se atualizar a função do orchestrator para contemplar esta alteração, poderá interromper existentes instâncias em trânsito.

Por exemplo, suponha que temos a seguinte função.

```csharp
[FunctionName("FooBar")]
public static Task Run([OrchestrationTrigger] DurableOrchestrationContext context)
{
    bool result = await context.CallActivityAsync<bool>("Foo");
    await context.CallActivityAsync("Bar", result);
}
```

Esta função simplistic aceita os resultados da **Foo** e passa-o para **barra**. Vamos supor que temos de alterar o valor de retorno de **Foo** de `bool` para `int` para suportar uma vasta variedade de valores de resultado. O resultado tem o seguinte aspeto:

```csharp
[FunctionName("FooBar")]
public static Task Run([OrchestrationTrigger] DurableOrchestrationContext context)
{
    int result = await context.CallActivityAsync<int>("Foo");
    await context.CallActivityAsync("Bar", result);
}
```

Esta alteração funciona bem para todas as novas instâncias da função do orchestrator mas interrompe a quaisquer instâncias em trânsito. Por exemplo, considere o caso em que chama uma instância de orquestração **Foo**, volta obtém um valor Booleano e, em seguida, os pontos de verificação. Se a alteração de assinatura, for implementada neste momento, a instância checkpointed falhará imediatamente quando retoma e, partirá a chamada para `context.CallActivityAsync<int>("Foo")`. Isto acontece porque o resultado na tabela de histórico `bool` , mas o novo código tenta anular a serialização para `int`.

Isto é apenas uma das muitas formas diferentes que uma alteração de assinatura pode causar uma falha instâncias existentes. Em geral, se um orchestrator tem de alterar a forma como aquele invoca uma função, em seguida, a alteração é provável que seja problemáticas.

### <a name="changing-orchestrator-logic"></a>Alterar a lógica do orchestrator

A outra classe de problemas de controlo de versões provenientes de alterar o código de função de forma confuses a lógica de repetição para instâncias em trânsito do orchestrator.

Considere a seguinte função do orchestrator:

```csharp
[FunctionName("FooBar")]
public static Task Run([OrchestrationTrigger] DurableOrchestrationContext context)
{
    bool result = await context.CallActivityAsync<bool>("Foo");
    await context.CallActivityAsync("Bar", result);
}
```

Agora vamos partem do princípio de que pretende efetuar uma alteração seemingly inofensivo adicionar outra chamada de função.

```csharp
[FunctionName("FooBar")]
public static Task Run([OrchestrationTrigger] DurableOrchestrationContext context)
{
    bool result = await context.CallActivityAsync<bool>("Foo");
    if (result)
    {
        await context.CallActivityAsync("SendNotification");
    }

    await context.CallActivityAsync("Bar", result);
}
```

Esta alteração adiciona uma nova chamada de função para **SendNotification** entre **Foo** e **barra**. Não foram efetuadas alterações de assinatura. O problema acontecer quando uma instância existente é retomado após a chamada para **barra**. Durante a reprodução, se a chamada original para **Foo** devolvido `true`, em seguida, irá chamar a reprodução do orchestrator para **SendNotification** que não se encontra no respetivo histórico de execução. Como resultado, a estrutura de tarefas durável falha com um `NonDeterministicOrchestrationException` porque encontrou uma chamada para **SendNotification** quando era esperado ver uma chamada para **barra**.

## <a name="mitigation-strategies"></a>Estratégias de mitigação

Seguem-se algumas estratégias para lidar com desafios de controlo de versões:

* Não fazer nada
* Parar todas as instâncias em trânsito
* Implementações lado a lado

### <a name="do-nothing"></a>Não fazer nada

É a forma mais fácil para processar uma alteração inovadora para permitir a orquestração em trânsito instâncias falharem. Novas instâncias de executam o código alterado com êxito.

Se se trata de um problema depende a importância das suas instâncias em trânsito. Se estiverem em desenvolvimento Active Directory e não se preocupar instâncias em trânsito, isto poderá ser boa suficiente. No entanto, terá de lidar com exceções e erros no seu pipeline de diagnóstico. Se quiser evitar esses problemas, considere as outras opções de controlo de versões.

### <a name="stop-all-in-flight-instances"></a>Parar todas as instâncias em trânsito

Outra opção consiste em Parar todas as instâncias em trânsito. Isto pode ser feito ao desmarcar o conteúdo do interna **controlo fila** e **fila de item de trabalho** filas. As instâncias serão bloqueadas indefinidamente onde estão, mas estas não serão clutter a telemetria com mensagens de falha. Isto é ideal de desenvolvimento de protótipo rápida.

> [!WARNING]
> Podem alterar os detalhes destas filas ao longo do tempo, pelo que não dependem esta técnica para cargas de trabalho de produção.

### <a name="side-by-side-deployments"></a>Implementações lado a lado

É a forma mais falhar prova Certifique-se de que alterações são implementadas em segurança implementando-os do lado do lado a lado com as versões anteriores. Isto pode ser feito utilizando qualquer um das técnicas seguintes:

* Implementar todas as atualizações como funções completamente novas (nomes de novo).
* Implemente todas as atualizações como uma nova aplicação de função com uma conta de armazenamento diferente.
* Implementar uma nova cópia da aplicação de função, mas com um atualizado `TaskHub` nome. Esta é a técnica recomendada.

### <a name="how-to-change-task-hub-name"></a>Como alterar o nome do hub de tarefas

O hub de tarefas pode ser configurado no *host.json* ficheiro da seguinte forma:

```json
{
    "durableTask": {
        "HubName": "MyTaskHubV2"
    }
}
```

O valor predefinido é `DurableFunctionsHub`.

Todas as entidades de armazenamento do Azure são denominadas com base no `HubName` valor de configuração. Concedendo um novo nome de hub de tarefas, certifique-se de que separado filas e tabela de histórico são criados para a nova versão da aplicação.

Recomendamos que implemente a nova versão da aplicação de função para um novo [ranhura de implementação](https://blogs.msdn.microsoft.com/appserviceteam/2017/06/13/deployment-slots-preview-for-azure-functions/). As ranhuras de implementação permitem-lhe executar várias cópias da sua função aplicação do lado do lado a lado com apenas um deles como o Active Directory *produção* ranhura. Quando estiver pronto para expor a lógica de orquestração novo à sua infraestrutura existente, pode ser tão simple como trocar a nova versão para a ranhura de produção.

> [!NOTE]
> Esta estratégia funciona melhor quando utilizar HTTP e webhook acionadores para funções do orchestrator. Para acionadores não HTTP, por exemplo, filas ou Event Hubs, a definição do acionador deve derivar uma definição de aplicação que obtém atualizada como parte da operação de troca.

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Saiba como lidar com o desempenho e reduzir problemas](durable-functions-perf-and-scale.md)
