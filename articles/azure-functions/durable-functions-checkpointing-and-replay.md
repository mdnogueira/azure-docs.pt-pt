---
title: "Pontos de verificação e repetição nas funções durável - Azure"
description: "Saiba como pontos de verificação e resposta funciona na extensão do durável funções para as funções do Azure."
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
ms.openlocfilehash: d8a5f3c915b1e3b6e11cec9c5540fa192f5f85dd
ms.sourcegitcommit: 5d772f6c5fd066b38396a7eb179751132c22b681
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/13/2017
---
# <a name="checkpoints-and-replay-in-durable-functions-azure-functions"></a>Pontos de verificação e repetição nas funções durável (funções do Azure)

Um dos atributos chaves de funções durável é **uma execução fiável**. Atividade e as funções do Orchestrator poderá estar em execução em VMs diferentes dentro de um centro de dados, não sendo dessas VMs ou a infraestrutura de rede subjacente não fiável de 100%.

Mantenha apesar dos, funções durável garante uma execução fiável dos orchestrations. Isto é feito ao utilizar as filas de armazenamento para a invocação de função de unidade e por periodicamente histórico de execução do ponto de verificação em tabelas de armazenamento (através de um padrão de conceção de nuvem conhecido como [eventos Sourcing](https://docs.microsoft.com/azure/architecture/patterns/event-sourcing)). Em seguida, pode ser reproduzido histórico reconstruir automaticamente o estado de memória de uma função do orchestrator.

## <a name="orchestration-history"></a>Histórico de orquestração

Suponha que tem a seguinte função do orchestrator.

```csharp
[FunctionName("E1_HelloSequence")]
public static async Task<List<string>> Run(
    [OrchestrationTrigger] DurableOrchestrationContext context)
{
    var outputs = new List<string>();

    outputs.Add(await context.CallActivityAsync<string>("E1_SayHello", "Tokyo"));
    outputs.Add(await context.CallActivityAsync<string>("E1_SayHello", "Seattle"));
    outputs.Add(await context.CallActivityAsync<string>("E1_SayHello", "London"));

    // returns ["Hello Tokyo!", "Hello Seattle!", "Hello London!"]
    return outputs;
}
```

Em cada `await` declaração, os pontos de verificação de estrutura de tarefas durável o estado de execução da função para o armazenamento de tabela. Este estado é o que é referido como o *histórico de orquestração*.

## <a name="history-table"></a>Tabela de histórico

Um modo geral, a estrutura de tarefas durável faz o seguinte em cada ponto de verificação:

1. Guarda o histórico de execução nas tabelas de armazenamento do Azure.
2. Mensagens Enqueues funciona para o orchestrator pretende invocar.
3. Mensagens de Enqueues para o orchestrator próprio &mdash; por exemplo, as mensagens de temporizador durável.

Assim que o ponto de verificação estiver concluído, a função do orchestrator é gratuita a serem removidos da memória enquanto houver mais tarefas para executar.

> [!NOTE]
> Armazenamento do Azure não fornece quaisquer garantias transacionais entre os dados no armazenamento de tabelas e filas. Para processar falhas, utiliza o fornecedor de armazenamento de funções durável *consistência eventual* padrões. Estes padrões Certifique-se de que não se tenha perdido nenhum dado se existir uma falha ou perda de conectividade no meio de um ponto de verificação.

Após a conclusão, o histórico da função apresentada anteriormente procura algo semelhante ao seguinte no armazenamento de tabelas do Azure (abreviado para fins de ilustração):

| PartitionKey (InstanceId)                     | EventType             | Timestamp               | Input | Nome             | resultado                                                    | Estado | 
|----------------------------------|-----------------------|----------|--------------------------|-------|------------------|-----------------------------------------------------------|---------------------| 
| eaee885b | OrchestratorStarted   | 2017-05-05T18:45:32.362Z |       |                  |                                                           |                     | 
| eaee885b | ExecutionStarted      | 2017-05-05T18:45:28.852Z | Valor nulo  | E1_HelloSequence |                                                           |                     | 
| eaee885b | TaskScheduled         | 2017-05-05T18:45:32.670Z |       | E1_SayHello      |                                                           |                     | 
| eaee885b | OrchestratorCompleted | 2017-05-05T18:45:32.670Z |       |                  |                                                           |                     | 
| eaee885b | OrchestratorStarted   | 2017-05-05T18:45:34.232Z |       |                  |                                                           |                     | 
| eaee885b | TaskCompleted         | 2017-05-05T18:45:34.201Z |       |                  | "" "Olá Tóquio!" ""                                        |                     | 
| eaee885b | TaskScheduled         | 2017-05-05T18:45:34.435Z |       | E1_SayHello      |                                                           |                     | 
| eaee885b | OrchestratorCompleted | 2017-05-05T18:45:34.435Z |       |                  |                                                           |                     | 
| eaee885b | OrchestratorStarted   | 2017-05-05T18:45:34.857Z |       |                  |                                                           |                     | 
| eaee885b | TaskCompleted         | 2017-05-05T18:45:34.763Z |       |                  | "" "Olá, Seattle!" ""                                      |                     | 
| eaee885b | TaskScheduled         | 2017-05-05T18:45:34.857Z |       | E1_SayHello      |                                                           |                     | 
| eaee885b | OrchestratorCompleted | 2017-05-05T18:45:34.857Z |       |                  |                                                           |                     | 
| eaee885b | OrchestratorStarted   | 2017-05-05T18:45:35.032Z |       |                  |                                                           |                     | 
| eaee885b | TaskCompleted         | 2017-05-05T18:45:34.919Z |       |                  | "" "Olá Londres!" ""                                       |                     | 
| eaee885b | ExecutionCompleted    | 2017-05-05T18:45:35.044Z |       |                  | "[""Olá, Tóquio!" ",""Olá Coimbra!" ",""Olá Londres!" "]" | Foi concluída           | 
| eaee885b | OrchestratorCompleted | 2017-05-05T18:45:35.044Z |       |                  |                                                           |                     | 

Algumas notas sobre os valores da coluna:
* **PartitionKey**: contém o ID de instância da orquestração.
* **O EventType**: representa o tipo de evento. Pode ser um dos seguintes tipos:
    * **OrchestrationStarted**: A função do orchestrator retomado a partir de um await ou está em execução para a primeira vez. O `Timestamp` coluna é utilizada para povoar o valor determinista para o [CurrentUtcDateTime](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_CurrentUtcDateTime) API.
    * **ExecutionStarted**: A função do orchestrator foi iniciado pela primeira vez a executar. Este evento também contém a entrada de função no `Input` coluna.
    * **TaskScheduled**: foi agendada uma função de atividade. O nome da função de atividade é capturado no `Name` coluna.
    * **TaskCompleted**: uma função de atividade foi concluída. O resultado da função está no `Result` coluna.
    * **TimerCreated**: foi criado um temporizador durável. O `FireAt` coluna contém a hora UTC agendada no qual o temporizador de expira.
    * **TimerFired**: expirou um temporizador durável.
    * **Evento gerado**: foi enviado um evento externo para a instância de orquestração. O `Name` coluna captura o nome do evento e o `Input` coluna captura o payload do evento.
    * **OrchestratorCompleted**: A função do orchestrator aguardada.
    * **ContinueAsNew**: A função do orchestrator foi concluída e reiniciado próprio com estado novo. O `Result` coluna contém o valor, que é utilizado como entrada na instância reiniciada.
    * **ExecutionCompleted**: A função do orchestrator tenha sido executada para conclusão (ou falha). As saídas da função ou os detalhes do erro são armazenadas no `Result` coluna.
* **Timestamp**: timestamp o UTC do evento de histórico.
* **Nome**: O nome da função que foi invocado.
* **Entrada**: formatada em JSON a entrada da função.
* **Saída**: O resultado da função, ou seja, o valor de retorno.

> [!WARNING]
> Embora seja útil como uma ferramenta de depuração, não efetuar qualquer dependência nesta tabela. -Pode alterar como a extensão de funções durável medida que evolui.

Sempre que a função é retomado após um `await`, a estrutura de tarefas durável volta a executar a função do orchestrator a partir do zero. Em cada voltar a executar que esta consulta o histórico de execução para determinar se a operação assíncrona atual realizou colocar.  Se a operação demorou local, a arquitetura, partirá resultado dessa operação imediatamente e passa para a próxima `await`. Este processo continua até o histórico completo tem foi reproduzido, altura em que todas as variáveis locais na função do orchestrator são restauradas para os respetivos valores anteriores.

## <a name="orchestrator-code-constraints"></a>Restrições de código do Orchestrator

O comportamento de repetição cria restrições do tipo de código que pode ser escrito numa função do orchestrator:

* Código do Orchestrator tem de ser **determinista**. Isto irá ser reproduzido várias vezes e tem produzir sempre o mesmo resultado. Por exemplo, não direta chama-se para obter a data/hora atual, obter números aleatórios, gerar GUIDs aleatórios ou chamar para pontos finais remotos.

  Se o código do orchestrator tem de obter a data/hora atual, deve utilizar o [CurrentUtcDateTime](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_CurrentUtcDateTime) API, que é seguro para repetição.

  Operações não determinística tem de ser efetuadas nas funções de atividade. Isto inclui qualquer interação com outros enlaces de entrada ou saídas. Isto garante que os valores não determinística serão gerados uma vez na primeira execução e guardados no histórico de execução. As execuções subsequentes, em seguida, irão utilizar o valor guardado automaticamente.

* Código do Orchestrator deve ser **não**. Por exemplo, isto significa que nenhum e/s e nenhum chamadas para `Thread.Sleep` ou APIs equivalentes.

  Se for um orchestrator atraso, pode utilizar o [CreateTimer](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_CreateTimer_) API.

* Código do Orchestrator tem **nunca iniciar qualquer operação assíncrona** exceto utilizando o [DurableOrchestrationContext](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html) API. Por exemplo, não `Task.Run`, `Task.Delay` ou `HttpClient.SendAsync`. A estrutura de durável de tarefas executa o código do orchestrator num único thread e não pode interagir com quaisquer outros threads foi possível agendar por outro async APIs.

* **Devem ser evitados ciclos infinito** no código do orchestrator. Porque a estrutura de tarefas durável guarda o histórico de execução como avança de ser de função orchestration, um ciclo infinito pode fazer com que uma instância do orchestrator ficar com memória esgotada. Para cenários de ciclo infinito, utilizar APIs como [ContinueAsNew](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_ContinueAsNew_) para reiniciar a execução de função e eliminar histórico de execução anterior.

Enquanto destas restrições podem parecer hercúleas em primeiro lugar, na prática, não estão hard a seguir. A estrutura de tarefas durável tenta detetar as violações das regras acima e emite um `NonDeterministicOrchestrationException`. No entanto, este comportamento de deteção é melhor esforço, e não deve dependem dele.

> [!NOTE]
> Todas estas regras são aplicadas apenas a funções acionadas pelo `orchestrationTrigger` enlace. Funções de atividade acionada pelo `activityTrigger` enlace e as funções que utilizam o `orchestrationClient` não enlace, ter nenhum dessas limitações.

## <a name="durable-tasks"></a>Tarefas duráveis

> [!NOTE]
> Esta secção descreve os detalhes de implementação interno do Framework tarefas durável. Pode utilizar funções durável sem saberem a estas informações. Destina-se apenas para o ajudar a compreender o comportamento de repetição.

Tarefas que podem ser aguardadas em segurança nas funções do orchestrator ocasionalmente são referidas como *tarefas duráveis*. Estas são tarefas que são criadas e geridas pelo Framework tarefas durável. Os exemplos são as tarefas devolvidas pelo `CallActivityAsync`, `WaitForExternalEvent`, e `CreateTimer`.

Estes *tarefas duráveis* internamente são geridos através da utilização de uma lista de `TaskCompletionSource` objetos. Durante a reprodução, estas tarefas obterem criadas como parte da execução de código do orchestrator e são executadas como o dispatcher enumera os eventos de histórico correspondente. Isto é tudo feito através de um único thread em sincronia até que todos os histórico foi reproduzido. Todas as tarefas duráveis, o que não estão concluídas no final de reprodução de histórico tem as ações adequadas levadas a cabo. Por exemplo, uma mensagem pode ser colocados em fila para chamar uma função de atividade.

O comportamento de execução descrito aqui deve ajudar a compreender por que motivo o código de função do orchestrator tem nunca `await` uma tarefa não durável: o thread do emissor não é possível aguardar pela respetiva conclusão e quaisquer chamada de retorno por essa tarefa pode danificar o controlo Estado da função do orchestrator. Algumas verificações de tempo de execução são implementados para experimentar evitar esta situação.

Se quiser obter mais informações sobre como o Framework de tarefa durável executa funções do orchestrator, a melhor coisa a fazer é consultar o [código de origem de tarefas durável no GitHub](https://github.com/Azure/durabletask). Em particular, consulte [TaskOrchestrationExecutor.cs](https://github.com/Azure/durabletask/blob/master/src/DurableTask.Core/TaskOrchestrationExecutor.cs) e [TaskOrchestrationContext.cs](https://github.com/Azure/durabletask/blob/master/src/DurableTask.Core/TaskOrchestrationContext.cs)

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Saiba mais sobre a gestão de instância](durable-functions-instance-management.md)