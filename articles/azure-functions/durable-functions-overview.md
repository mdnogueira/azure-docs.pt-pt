---
title: "Descrição geral de funções durável - Azure"
description: "Introdução à extensão de funções durável para as funções do Azure."
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
ms.openlocfilehash: 04d660d5fdd878788c09e46b078b2e2b043b7dbb
ms.sourcegitcommit: 5d772f6c5fd066b38396a7eb179751132c22b681
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/13/2017
---
# <a name="durable-functions-overview-azure-functions"></a>Descrição geral das funções durável (funções do Azure)

*Funções duráveis* é uma extensão da [das funções do Azure](functions-overview.md) e [WebJobs do Azure](../app-service/web-sites-create-web-jobs.md) que lhe permite escrever funções de monitorização de estado num ambiente sem servidor. A extensão de estado, os pontos de verificação e reinícios gere de automaticamente.

A extensão permite-lhe definir fluxos de trabalho com monitorização de estado um novo tipo de função chamada um *função orchestrator*. Seguem-se algumas das vantagens de funções do orchestrator:

* Se definirem fluxos de trabalho no código. Sem esquemas JSON ou designers são necessárias.
* Estes podem chamar noutras funções de forma síncrona e assíncrona. Resultado da chamada funções pode ser guardado variáveis locais.
* Estes automaticamente ponto de verificação progresso em sempre que a função awaits. Estado local nunca é perdido se o processo recicla ou a VM é reiniciado.

> [!NOTE]
> Funções duráveis é uma extensão avançada para as funções do Azure e não é adequadas para todas as aplicações. O resto deste artigo pressupõe que tem uma forte familiaridade com [das funções do Azure](functions-overview.md) conceitos e desafios envolvidos no desenvolvimento da aplicação sem servidor.

O principal caso de utilização funções durável é simplificar problemas de coordenação complexas, com monitorização de estado nas aplicações sem servidor. As secções seguintes descrevem algumas padrões de aplicação típica podem beneficiar das funções durável.

## <a name="pattern-1-function-chaining"></a>Padrão #1: Encadeamento de função

*Encadeamento de função* refere-se o padrão de executar uma sequência de funções por uma ordem específica. Muitas vezes, o resultado de uma função tem de ser aplicada para a entrada de outra função.

![Diagrama de encadeamento de função](media/durable-functions-overview/function-chaining.png)

Funções duráveis permite-lhe implementar neste padrão de forma concisa no código.

```cs
public static async Task<object> Run(DurableOrchestrationContext ctx)
{
    try
    {
        var x = await ctx.CallActivityAsync<object>("F1");
        var y = await ctx.CallActivityAsync<object>("F2", x);
        var z = await ctx.CallActivityAsync<object>("F3", y);
        return  await ctx.CallActivityAsync<object>("F4", z);
    }
    catch (Exception)
    {
        // error handling/compensation goes here
    }
}
```

Os valores "F1", "F2", "F3" e "F4" são os nomes de outras funções na aplicação de função. Fluxo de controlo é implementado utilização normal construções de codificação. Ou seja, o código executa descendente e pode envolver existente semântica de fluxo de controlo idioma, como conditionals e ciclos.  Lógica de processamento de erros podem ser incluídos em blocos try/catch/finally.

O `ctx` parâmetro ([DurableOrchestrationContext](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html)) fornece métodos para invocar noutras funções de por nome, transmitir parâmetros e devolver o resultado da função. Cada vez que as chamadas de código `await`, a arquitetura de funções durável *pontos de verificação* o progresso da instância de função atual. Se o processo ou VM recicla meio através da execução, a instância de função é retomado após anterior `await` chamada. Obter mais informações sobre este comportamento de reinício mais tarde.

## <a name="pattern-2-fan-outfan-in"></a>Padrão #2: Fan-out/fan-em

*Fan-out/fan-em* refere-se o padrão de executar várias funções em paralelo e, em seguida, a aguardar para concluir.  Muitas vezes, alguns cálculos de agregação procedimento é efetuado nos resultados devolvidos pelas funções.

![Diagrama de fan-out/fan-em](media/durable-functions-overview/fan-out-fan-in.png)

Com as funções normais, pode ser feito fanning out, fazendo com que a função enviar várias mensagens para uma fila. No entanto, fanning regresso é muito mais difícil. Terá de escrever código para controlar quando as funções acionadas de fila de fim e armazenam as saídas da função. A extensão de funções durável processa este padrão com o código relativamente simple.

```cs
public static async Task Run(DurableOrchestrationContext ctx)
{
    var parallelTasks = new List<Task<int>>();
 
    // get a list of N work items to process in parallel
    object[] workBatch = await ctx.CallActivityAsync<object[]>("F1");
    for (int i = 0; i < workBatch.Length; i++)
    {
        Task<int> task = ctx.CallActivityAsync<int>("F2", workBatch[i]);
        parallelTasks.Add(task);
    }
 
    await Task.WhenAll(parallelTasks);
 
    // aggregate all N outputs and send result to F3
    int sum = parallelTasks.Sum(t => t.Result);
    await ctx.CallActivityAsync("F3", sum);
}
```

O trabalho fan-out é distribuído para várias instâncias da função `F2`, e o trabalho é controlado através da utilização de uma lista dinâmica de tarefas. O .NET `Task.WhenAll` API denomina-se a aguardar para todas as funções de chamada para concluir. Em seguida, a `F2`função produz são agregados na lista de tarefas dinâmica e transmitidos para o `F3` função.

O ponto de verificação automático que ocorre à `await` chamar `Task.WhenAll` garante que qualquer meio de falhas ou um reinício através de não necessita de um reinício de qualquer já concluir tarefas.

## <a name="pattern-3-async-http-apis"></a>Padrão #3: APIs de HTTP assíncrona

O terceiro padrão é tudo sobre o problema de coordenação o estado das operações de execução longa com clientes externos. Uma forma comum para implementar este padrão é ter a ação de execução longa acionada por uma chamada HTTP e, em seguida, redirecionar o cliente para um ponto final de estado, podem consultar para saber quando a operação for concluída.

![Diagrama de API de HTTP](media/durable-functions-overview/async-http-api.png)

Funções duráveis fornece APIs incorporadas que simplificar o código de escrita para interagir com execuções de função de execução longa. O [amostras](durable-functions-install.md) mostrar um simple comando REST que pode ser utilizado para iniciar novas instâncias de função do orchestrator. Depois de uma instância é iniciada, a extensão expõe webhook HTTP APIs que consulta o estado da função do orchestrator. O exemplo seguinte mostra os comandos REST para iniciar um orchestrator e para consultar o seu estado. Para efeitos de clareza, alguns detalhes estão omitidos do exemplo.

```
> curl -X POST https://myfunc.azurewebsites.net/orchestrators/DoWork -H "Content-Length: 0" -i
HTTP/1.1 202 Accepted
Content-Type: application/json
Location: https://myfunc.azurewebsites.net/admin/extensions/DurableTaskExtension/b79baf67f717453ca9e86c5da21e03ec

{"id":"b79baf67f717453ca9e86c5da21e03ec", ...}

> curl https://myfunc.azurewebsites.net/admin/extensions/DurableTaskExtension/b79baf67f717453ca9e86c5da21e03ec -i
HTTP/1.1 202 Accepted
Content-Type: application/json
Location: https://myfunc.azurewebsites.net/admin/extensions/DurableTaskExtension/b79baf67f717453ca9e86c5da21e03ec

{"runtimeStatus":"Running","lastUpdatedTime":"2017-03-16T21:20:47Z", ...}

> curl https://myfunc.azurewebsites.net/admin/extensions/DurableTaskExtension/b79baf67f717453ca9e86c5da21e03ec -i
HTTP/1.1 200 OK
Content-Length: 175
Content-Type: application/json

{"runtimeStatus":"Completed","lastUpdatedTime":"2017-03-16T21:20:57Z", ...}
```

Porque o estado é gerido pelo tempo de execução durável funções, não tem de implementar o seus próprios Estado mecanismo de controlo.

Apesar da extensão de funções durável tem webhooks incorporada para gerir orchestrations de longa execução, que pode implementar este padrão utilizando as suas próprias acionadores de função (por exemplo, HTTP, fila ou Hub de eventos) e o `orchestrationClient` enlace. Por exemplo, pode utilizar uma mensagem de fila para acionar a terminação.  Ou pode utilizar um acionador HTTP protegido por uma política de autenticação do Azure Active Directory em vez dos webhooks incorporados que utilizam uma chave gerada para autenticação. 

```cs
// HTTP-triggered function to start a new orchestrator function instance.
public static async Task<HttpResponseMessage> Run(
    HttpRequestMessage req,
    DurableOrchestrationClient starter,
    string functionName,
    TraceWriter log)
{
    // Function name comes from the request URL.
    // Function input comes from the request content.
    dynamic eventData = await req.Content.ReadAsAsync<object>();
    string instanceId = await starter.StartNewAsync(functionName, eventData);
    
    log.Info($"Started orchestration with ID = '{instanceId}'.");
    
    return starter.CreateCheckStatusResponse(req, instanceId);
}
```

O [DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html) `starter` parâmetro é um valor entre o `orchestrationClient` de saída de enlace, que faz parte da extensão de funções durável. Fornece métodos para iniciar, enviando eventos, terminar e de consulta para instâncias de função do orchestrator novo ou existente. No exemplo acima, uma HTTP acionada-função aceita um `functionName` valor do URL de entrada e transmite o valor para [StartNewAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_StartNewAsync_). Esta API de enlace, em seguida, devolve uma resposta que contém um `Location` cabeçalho e informações adicionais sobre a instância que pode ser utilizada mais tarde para ver o estado da instância tiver sido iniciado ou de terminá-la.

## <a name="pattern-4-stateful-singletons"></a>Padrão #4: Singletons com monitorização de estado

A maioria das funções tem um início explícito e de fim e não diretamente interagem com origens de eventos externo. No entanto, orchestrations suporta um [singleton com monitorização de estado](durable-functions-singletons.md) padrão que lhe permita comportar-se como fiável [atores](https://en.wikipedia.org/wiki/Actor_model) da informática distribuída.

O diagrama seguinte ilustra uma função que é executada num ciclo infinito ao processar eventos recebidos a partir de origens externas.

![Diagrama de monitorização de estado singleton](media/durable-functions-overview/stateful-singleton.png)

Enquanto as funções durável não é uma implementação do modelo de ator, as funções do orchestrator tem muitas das mesmas características de tempo de execução. Por exemplo, são longa execução (possivelmente endless), com monitorização de estado, fiável, single-threaded, transparente para a localização e globalmente endereçável. Isto faz com que as funções do orchestrator útil para "ator"-, como cenários.

Funções comum são sem monitorização de estado e, por conseguinte, não adequado para implementar um padrão de singleton com monitorização de estado. No entanto, a extensão de funções durável torna o padrão de singleton com monitorização de estado relativamente trivial para implementar. O seguinte código é uma função do orchestrator simples que implementa um contador.

```cs
public static async Task Run(DurableOrchestrationContext ctx)
{
    int counterState = ctx.GetInput<int>();

    string operation = await ctx.WaitForExternalEvent<string>("operation");
    if (operation == "incr")
    {
        counterState++;
    }
    else if (operation == "decr")
    {
        counterState--;
    }

    ctx.ContinueAsNew(counterState);
}
```

Este código é o que poderá descrever como "eternal orchestration" &mdash; ou seja, um que inicia e termina nunca. Esta executa os seguintes passos:

* Começa com um valor de entrada no `counterState`.
* Aguarda indefinidamente de uma mensagem chamado `operation`.
* Efetua algumas lógica para atualizar o respetivo estado local.
* "For reiniciado" próprio chamando `ctx.ContinueAsNew`.
* Awaits novamente indefinidamente para a próxima operação.

## <a name="pattern-5-human-interaction"></a>Padrão #5: Interação humana

Muitos processos envolvem algum tipo de interação humana. A coisa tricky sobre que envolvem humans num processo automatizado é que as pessoas não são sempre como de elevada disponibilidade e reativa como serviços na nuvem. Tem de permitir os processos automatizados para este e são, muitas vezes, tal através da utilização de tempos limite e compensação lógica.

Um exemplo de um processo empresarial que envolve a interação humana é um processo de aprovação. Por exemplo, de um gestor pode ser exigida a aprovação para um relatório de despesa excede um determinado número. Se o gestor não aprovar dentro de 72 horas (maybe que ocorreu um erro de férias), inicia um processo de escalamento para obter a aprovação de outra pessoa (talvez Gestor do manager).

![Diagrama de interação humana](media/durable-functions-overview/approval.png)

Este padrão pode ser implementado através de uma função do orchestrator. O orchestrator teria de utilizar um [temporizador durável](durable-functions-timers.md) para solicitar a aprovação e escalar em caso de tempo limite. Deverá aguardar um [evento externo](durable-functions-external-events.md), qual seria a notificação gerada por algumas interação humana.

```cs
public static async Task Run(DurableOrchestrationContext ctx)
{
    await ctx.CallActivityAsync("RequestApproval");
    using (var timeoutCts = new CancellationTokenSource())
    {
        DateTime dueTime = ctx.CurrentUtcDateTime.AddHours(72);
        Task durableTimeout = ctx.CreateTimer(dueTime, timeoutCts.Token);

        Task<bool> approvalEvent = ctx.WaitForExternalEvent<bool>("ApprovalEvent");
        if (approvalEvent == await Task.WhenAny(approvalEvent, durableTimeout))
        {
            timeoutCts.Cancel();
            await ctx.CallActivityAsync("HandleApproval", approvalEvent.Result);
        }
        else
        {
            await ctx.CallActivityAsync("Escalate");
        }
    }
}
```

O temporizador durável é criado chamando `ctx.CreateTimer`. A notificação é recebida pelo `ctx.WaitForExternalEvent`. E `Task.WhenAny` é chamado para decidir se pretende escalar (tempo limite ocorre primeiro) ou processos de aprovação (aprovação é recebida antes de tempo limite).

## <a name="the-technology"></a>A tecnologia

Nos bastidores, a extensão de funções durável está incorporada do [durável tarefas Framework](https://github.com/Azure/durabletask), uma biblioteca de código aberto no GitHub para a criação de orchestrations tarefas durável. Muito como como as funções do Azure é a evolução sem servidor de WebJobs do Azure, funções durável é a evolução sem servidor do Framework tarefas durável. A estrutura de durável de tarefas é utilizada descontos elevados no Microsoft e fora, bem como automatizar processos fundamentais. É uma opção para o ambiente das funções do Azure sem servidor natural.

### <a name="event-sourcing-checkpointing-and-replay"></a>Evento sourcing, pontos de verificação e repetição

As funções do Orchestrator fiável manter o respetivo estado de execução utilizando um padrão de conceção de nuvem conhecido como [eventos Sourcing](https://docs.microsoft.com/azure/architecture/patterns/event-sourcing). Em vez de armazenar diretamente o *atual* estado de uma orquestração, a extensão durável utiliza um arquivo só de acréscimo para registar o *completa série de ações* executadas pela orquestração de função. Isto tem muitas vantagens, incluindo a melhorar o desempenho, escalabilidade e capacidade de resposta em comparação com "captura" o estado de runtime completa. Outras vantagens incluem que fornece a consistência eventual para dados transacionais e manter registos de auditoria completa e histórico. Os registos de auditoria próprios ativar ações de compensação fiáveis.

A utilização de evento Sourcing por esta extensão é transparente. Nos bastidores, a `await` operador numa função orchestrator gera o controlo do thread do orchestrator para o distribuidor de estrutura de tarefas durável. O dispatcher consolida, em seguida, as novas ações de que a função do orchestrator agendada (como chamar as funções de subordinados de um ou mais ou agendar um temporizador durável) para o armazenamento. Esta ação de consolidação transparente acrescenta ao *histórico de execução* da instância de orquestração. O histórico é armazenado no armazenamento durável. A ação de consolidação em seguida, adiciona as mensagens a uma fila de agendar o trabalho real. Neste momento, a função do orchestrator pode ser descarregada da memória. Faturação para o mesmo deixa de se estiver a utilizar o plano de consumo de funções do Azure.  Quando existe mais de trabalho para o fazer, a função é reiniciada e o estado é é reconstruir.

Depois de uma função de orquestração é dado o trabalho mais fazer (por exemplo, é recebida uma mensagem de resposta ou um temporizador durável expira), o orchestrator reativado novamente e executa novamente a função completa desde o início para reconstruir o estado do local. Se, durante este reproduzir o código tenta chamar uma função (ou efetuar quaisquer outra async trabalho), a estrutura de tarefas durável consulta com o *histórico de execução* da orquestração da atual. Se encontrar que a função de atividade já tiver sido executada geraram algumas resultado,-lo, partirá resultado que funcionam e o código do orchestrator continua em execução. Isto continua a ocorrer até que o código de função obtém a um ponto onde estiver concluída ou que tem trabalho async novo agendado.

### <a name="orchestrator-code-constraints"></a>Restrições de código do Orchestrator

O comportamento de repetição cria restrições do tipo de código que pode ser escrito numa função do orchestrator. Por exemplo, código do orchestrator tem de ser determinista, à medida que irá ser reproduzido várias vezes e tem produzir sempre o mesmo resultado. A lista completa de restrições pode ser encontrada no [restrições de código do Orchestrator](durable-functions-checkpointing-and-replay.md#orchestrator-code-constraints) secção o **pontos de verificação e reinício** artigo.

## <a name="language-support"></a>Suporte de idiomas

Atualmente c# é o idioma suportado apenas para funções durável. Isto inclui a atividade e as funções do orchestrator. No futuro, iremos adicionar suporte para todos os idiomas que suporte das funções do Azure. Consulte as funções do Azure [lista de problemas de repositório do GitHub](https://github.com/Azure/azure-functions-durable-extension/issues) para ver o estado mais recente do nosso idiomas adicionais, suporta o trabalho.

## <a name="monitoring-and-diagnostics"></a>Monitorização e diagnóstico

A extensão de funções durável emite automaticamente dados de controlo estruturados para [Application Insights](functions-monitoring.md) quando a aplicação de função está configurada com uma chave do Application Insights. Dados de controlo podem ser utilizados para monitorizar o comportamento e o progresso da sua orchestrations.

Eis um exemplo do aspeto as funções durável eventos de controlo no Application Insights portal com [Application Insights Analytics](https://docs.microsoft.com/azure/application-insights/app-insights-analytics):

![Resultados de consulta de informações de aplicação](media/durable-functions-overview/app-insights-1.png)

Há uma grande quantidade de dados estruturados útil packed para o `customDimensions` campo em cada entrada de registo. Eis um exemplo de uma essa entrada totalmente expandida.

![campo de customDimensions na consulta de informações de aplicação](media/durable-functions-overview/app-insights-2.png)

Devido ao comportamento de repetição do dispatcher durável tarefas Framework, que pode esperava entradas de registo redundante de ações reproduzidas. Isto pode ser útil compreender o comportamento de repetição do motor principal. O [diagnóstico](durable-functions-diagnostics.md) artigo mostra exemplos de consultas que filtragem os registos de repetição para que possa ver apenas os registos de "em tempo real".

## <a name="storage-and-scalability"></a>Armazenamento e a escalabilidade

A extensão de funções durável utiliza as filas de armazenamento do Azure, tabelas e os blobs para manter a execução histórico acionador e estado de execução de função. A conta de armazenamento predefinido para a aplicação de função pode ser utilizada ou pode configurar uma conta de armazenamento separada. Pode querer uma conta separada devido a limites de débito de armazenamento. O código de orchestrator escrita não precisa (e não devem) interagir com as entidades nestas contas de armazenamento. As entidades são geridas diretamente pelo Framework tarefas durável, como um detalhe de implementação.

As funções do Orchestrator agendar as funções de atividade e recebem as respetivas respostas através de mensagens da fila interna. Quando uma aplicação de função é executada no plano de consumo de funções do Azure, estas filas são monitorizadas pelo [controlador de escala de funções do Azure](functions-scale.md#how-the-consumption-plan-works) e nova computação de instâncias são adicionadas, conforme necessário. Quando ampliar várias VMs, uma função do orchestrator pode executar uma VM durante a execução de funções de atividade chama em várias VMs diferentes. Pode encontrar mais detalhes sobre o comportamento de dimensionamento das funções durável no [desempenho e dimensionamento](durable-functions-perf-and-scale.md).

O Table storage é utilizado para armazenar o histórico de execução para contas do orchestrator. Sempre que uma instância rehydrates numa VM específica, o histórico de execução-obtém do armazenamento de tabela para que pode restituir estado local. Uma das ações prática relacionada com o histórico disponível no Table storage é que pode ver e ver o histórico do seu orchestrations através de ferramentas, como [Explorador de armazenamento do Microsoft Azure](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer).

![Captura de ecrã de Explorador de armazenamento do Azure](media/durable-functions-overview/storage-explorer.png)

> [!WARNING]
> Embora seja mais fácil e conveniente ver o histórico de execução no table storage, evite colocar qualquer dependência nesta tabela. -Pode alterar como a extensão de funções durável medida que evolui.

## <a name="known-issues-and-faq"></a>Problemas conhecidos e FAQ

Em geral, todos os problemas conhecidos devem ser controlados no [GitHub problemas](https://github.com/Azure/azure-functions-durable-extension/issues) lista. Se um problema e não é possível localizar o problema no GitHub, abra um novo problema e incluem uma descrição detalhada do problema. Mesmo se pretender fazer uma pergunta simplesmente, não hesite em abrir um problema no GitHub e marcá-la como uma pergunta.

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Continuar a ler a documentação de funções durável](durable-functions-bindings.md)

> [!div class="nextstepaction"]
> [Instale a extensão de funções durável e amostras](durable-functions-install.md)

