---
title: Conceber consultas eficiente de lista - Azure Batch | Microsoft Docs
description: "Aumentar o desempenho ao filtras as suas consultas quando solicitar informações sobre recursos do Batch, como conjuntos, tarefas, tarefas e nós de computação."
services: batch
documentationcenter: .net
author: tamram
manager: timlt
editor: 
ms.assetid: 031fefeb-248e-4d5a-9bc2-f07e46ddd30d
ms.service: batch
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: big-compute
ms.date: 08/02/2017
ms.author: tamram
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: a80b207f591bd888d4749287527013c5e554fb6e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="create-queries-to-list-batch-resources-efficiently"></a>Criar consultas para recursos do Batch de lista de forma eficiente

Aqui poderá aprender como aumentar o desempenho da aplicação do Azure Batch ao reduzir a quantidade de dados que são devolvidos pelo serviço, quando consultar tarefas, tarefas e computação nós com o [.NET do Batch] [ api_net]biblioteca.

Quase todas as aplicações do Batch tem de efetuar algum tipo de monitorização ou outra operação que consulta o serviço Batch, muitas vezes em intervalos regulares. Por exemplo, para determinar se existem quaisquer tarefas em fila restantes numa tarefa, tem de obter dados de todas as tarefas na tarefa. Para determinar o estado de nós do conjunto, tem de obter dados em cada nó no conjunto. Este artigo explica como realizar estas consultas da forma mais eficiente.

> [!NOTE]
> O serviço Batch fornece suporte de APIS especial para o cenário comum de tarefas numa tarefa de contagem. Em vez de utilizar uma consulta de lista para estes, pode chamar o [obter contagens de tarefa] [ rest_get_task_counts] operação. Contagens de tarefa Get indica quantos tarefas estão pendentes, executar ou concluam e como muitas tarefas tem êxito ou falhou. Obter contagens de tarefas é mais eficiente do que uma consulta de lista. Para obter mais informações, consulte [contagem de tarefas para uma tarefa pelo Estado (pré-visualização)](batch-get-task-counts.md). 
>
> A operação de obter a contagem de tarefas anteriormente 2017-06-01.5.1 não está disponível nas versões do serviço de Batch. Se estiver a utilizar uma versão mais antiga do serviço, em seguida, utilize uma consulta de lista para em vez disso, a contagem de tarefas numa tarefa.
>
> 

## <a name="meet-the-detaillevel"></a>Cumpre o DetailLevel
Uma aplicação do Batch de produção, as entidades, como trabalhos, tarefas e nós de computação podem número em milhares. Quando solicitar informações sobre estes recursos, uma potencialmente grande quantidade de dados tem "cruzada da transmissão" do serviço Batch à sua aplicação em cada consulta. Ao limitar o número de itens e o tipo de informação que é devolvido por uma consulta, pode aumentar a velocidade das suas consultas e, por conseguinte, o desempenho da sua aplicação.

Isto [.NET do Batch] [ api_net] listas de fragmento de código API *cada* tarefa que está associada uma tarefa, juntamente com *todos os* das propriedades de cada tarefa:

```csharp
// Get a collection of all of the tasks and all of their properties for job-001
IPagedEnumerable<CloudTask> allTasks =
    batchClient.JobOperations.ListTasks("job-001");
```

No entanto, pode executar uma consulta de lista muito mais eficiente, aplicando um "nível de detalhe" a sua consulta. Fazê-lo, fornecendo um [ODATADetailLevel] [ odata] de objeto para o [JobOperations.ListTasks] [ net_list_tasks] método. Este fragmento devolve apenas o ID, linha de comandos e propriedades de informações do nó de computação de tarefas foi concluídas:

```csharp
// Configure an ODATADetailLevel specifying a subset of tasks and
// their properties to return
ODATADetailLevel detailLevel = new ODATADetailLevel();
detailLevel.FilterClause = "state eq 'completed'";
detailLevel.SelectClause = "id,commandLine,nodeInfo";

// Supply the ODATADetailLevel to the ListTasks method
IPagedEnumerable<CloudTask> completedTasks =
    batchClient.JobOperations.ListTasks("job-001", detailLevel);
```

Neste cenário de exemplo, se existirem milhares de tarefas na tarefa, os resultados da consulta do segundo serão normalmente devolvidos muito mais rápida do que o primeiro. Obter mais informações sobre como utilizar ODATADetailLevel quando listar itens com a API .NET do Batch estão incluídas [abaixo](#efficient-querying-in-batch-net).

> [!IMPORTANT]
> Recomendamos vivamente que lhe *sempre* fornecer um objeto de ODATADetailLevel para as chamadas de lista de .NET API para garantir o desempenho da aplicação e eficiência máxima. Ao especificar um nível de detalhe, pode ajudar a reduzir os tempos de resposta do serviço Batch, melhorar a utilização de rede e minimizar a utilização de memória por aplicações cliente.
> 
> 

## <a name="filter-select-and-expand"></a>Filtrar, selecione e expanda
O [.NET do Batch] [ api_net] e [REST do Batch] [ api_rest] APIs fornecem a capacidade para reduzir o número de itens que são devolvidos numa lista, bem como a quantidade de informações que são devolvidas para cada. Fazê-lo especificando **filtro**, **selecione**, e **expanda cadeias** quando efetuar consultas de lista.

### <a name="filter"></a>Filtro
A cadeia de filtro é uma expressão que reduz o número de itens que são devolvidos. Por exemplo, lista apenas as tarefas em execução para uma tarefa ou listar apenas nós de computação que estão prontos para executar tarefas.

* A cadeia de filtro é composta por uma ou mais expressões com uma expressão que é composta por um nome de propriedade, um operador e um valor. As propriedades que podem ser especificadas são específicas para cada tipo de entidade consultar, como são os operadores que são suportados para cada propriedade.
* Várias expressões podem ser combinadas utilizando os operadores lógicos `and` e `or`.
* Listas de cadeia de filtro neste exemplo, apenas o executado "compor" tarefas: `(state eq 'running') and startswith(id, 'renderTask')`.

### <a name="select"></a>Selecione
A cadeia selecione limita os valores de propriedade que são devolvidos para cada item. Especifique uma lista de nomes de propriedade e apenas esses valores de propriedade para os itens nos resultados da consulta são devolvidos.

* A cadeia selecione é composta por uma lista separada por vírgulas dos nomes de propriedades. Pode especificar qualquer uma das propriedades para o tipo de entidade que estiver a consultar.
* Esta cadeia selecione exemplo Especifica que apenas três valores de propriedade devem ser devolvidos para cada tarefa: `id, state, stateTransitionTime`.

### <a name="expand"></a>Expandir
A cadeia de expansão reduz o número de chamadas API que são necessários para obter algumas informações. Quando utiliza uma cadeia de expansão, podem obter mais informações sobre cada item com uma única chamada de API. Em vez de primeiro de obter a lista de entidades, em seguida, pedir informações para cada item na lista, utilize uma cadeia de expansão para obter as mesmas informações numa única chamada de API. Menos chamadas de API significa um melhor desempenho.

* Semelhante à cadeia de selecionar, os controlos de cadeia de expansão se certos dados estão incluídos nos resultados de consulta da lista.
* A cadeia de expansão só é suportada quando é utilizado na lista de tarefas, as agendas de tarefas, tarefas e agrupamentos. Atualmente, suporta apenas informações de estatísticas.
* Quando todas as propriedades são necessárias e nenhuma cadeia select for especificada, a cadeia de expansão *tem* ser utilizado para obter informações de estatísticas. Se uma cadeia selecione é utilizada para obter um subconjunto de propriedades, em seguida, `stats` pode ser especificado na cadeia de selecione e a cadeia de expansão não precisa de ser especificado.
* Neste exemplo expanda cadeia Especifica que as informações de estatísticas devem ser devolvidas para cada item na lista: `stats`.

> [!NOTE]
> Ao construir a nenhum dos tipos de cadeia de três consulta (filtrar, selecione e expanda), tem de garantir que os nomes de propriedade e caso corresponde ao que os seus homólogos de elemento de REST API. Por exemplo, quando trabalhar com o .NET [CloudTask](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask) classe, tem de especificar **estado** em vez de **estado**, mesmo que a propriedade de .NET é [ CloudTask.State](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask.state). Consulte as tabelas abaixo para mapeamentos de propriedades entre o .NET e REST APIs.
> 
> 

### <a name="rules-for-filter-select-and-expand-strings"></a>Regras de filtro, selecione e expanda cadeias
* Os nomes de propriedades num filtro, selecione e expanda cadeias deve aparecer como fazem [REST do Batch] [ api_rest] API-, mesmo quando utilize [.NET do Batch] [ api_net]ou um dos outros SDKs do Batch.
* Todos os nomes de propriedade são sensíveis a maiúsculas, mas valores de propriedade são sensível a maiúsculas e minúsculas.
* Data/hora cadeias pode ser um dos dois formatos e tem de ser precedidas com `DateTime`.
  
  * Exemplo de formato de W3C DTF:`creationTime gt DateTime'2011-05-08T08:49:37Z'`
  * Exemplo de formato de RFC 1123:`creationTime gt DateTime'Sun, 08 May 2011 08:49:37 GMT'`
* São cadeias booleanos `true` ou `false`.
* Se for especificado uma propriedade inválida ou o operador, um `400 (Bad Request)` ocorrerá erro.

## <a name="efficient-querying-in-batch-net"></a>Eficiente consultar no .NET do Batch
Dentro do [.NET do Batch] [ api_net] API, o [ODATADetailLevel] [ odata] classe é utilizada para fornecer o filtro, selecione e expanda cadeias para listar operações. A classe de ODataDetailLevel tem três propriedades de cadeia público que podem ser especificadas no construtor ou definir diretamente o objeto. Em seguida, passa o objeto de ODataDetailLevel como um parâmetro para várias operações de lista, tais como [ListPools][net_list_pools], [ListJobs][net_list_jobs], e [ListTasks][net_list_tasks].

* [ODATADetailLevel][odata].[ FilterClause][odata_filter]: limitar o número de itens que são devolvidos.
* [ODATADetailLevel][odata].[ SelectClause][odata_select]: especificar os valores de propriedade são devolvidos com cada item.
* [ODATADetailLevel][odata].[ ExpandClause][odata_expand]: obter dados para todos os itens de uma única API chamar em vez de chamadas separadas para cada item.

O fragmento de código seguinte utiliza API .NET do Batch para consultar o serviço Batch para as estatísticas de um conjunto específico de conjuntos de forma eficaz. Neste cenário, o utilizador do Batch tem conjuntos de teste e produção. O conjunto de teste IDs têm o prefixo "test" e o conjunto de produção IDs têm o prefixo "prod". No fragmento, *myBatchClient* é uma instância de foi inicializada correctamente o [BatchClient](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.batchclient) classe.

```csharp
// First we need an ODATADetailLevel instance on which to set the filter, select,
// and expand clause strings
ODATADetailLevel detailLevel = new ODATADetailLevel();

// We want to pull only the "test" pools, so we limit the number of items returned
// by using a FilterClause and specifying that the pool IDs must start with "test"
detailLevel.FilterClause = "startswith(id, 'test')";

// To further limit the data that crosses the wire, configure the SelectClause to
// limit the properties that are returned on each CloudPool object to only
// CloudPool.Id and CloudPool.Statistics
detailLevel.SelectClause = "id, stats";

// Specify the ExpandClause so that the .NET API pulls the statistics for the
// CloudPools in a single underlying REST API call. Note that we use the pool's
// REST API element name "stats" here as opposed to "Statistics" as it appears in
// the .NET API (CloudPool.Statistics)
detailLevel.ExpandClause = "stats";

// Now get our collection of pools, minimizing the amount of data that is returned
// by specifying the detail level that we configured above
List<CloudPool> testPools =
    await myBatchClient.PoolOperations.ListPools(detailLevel).ToListAsync();
```

> [!TIP]
> Uma instância de [ODATADetailLevel] [ odata] que está configurado com selecione e expanda cláusulas também podem transmitidas para os métodos Get adequados, tais como [PoolOperations.GetPool](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.getpool.aspx), para limitar a quantidade de dados que são devolvidos.
> 
> 

## <a name="batch-rest-to-net-api-mappings"></a>REST para mapeamentos de .NET API do batch
Os nomes das propriedades num filtro, selecione e expanda cadeias *tem* refletir os seus homólogos de REST API, o nome e a maiúsculas e minúsculas. As tabelas a seguir fornecem mapeamentos entre os homólogos .NET e a REST API.

### <a name="mappings-for-filter-strings"></a>Mapeamentos de cadeias de filtro
* **Métodos de lista de .NET**: cada um dos métodos .NET API nesta coluna aceita um [ODATADetailLevel] [ odata] objeto como parâmetro.
* **Pedidos de lista REST**: página de API de REST cada associada a esta coluna contém uma tabela que especifica as propriedades e as operações que são permitidas em *filtro* cadeias. Irá utilizar estes nomes de propriedade e operações quando constrói um [ODATADetailLevel.FilterClause] [ odata_filter] cadeia.

| Métodos de lista de .NET | Pedidos de lista REST |
| --- | --- |
| [CertificateOperations.ListCertificates][net_list_certs] |[Lista os certificados numa conta][rest_list_certs] |
| [CloudTask.ListNodeFiles][net_list_task_files] |[Lista os ficheiros associados uma tarefa][rest_list_task_files] |
| [JobOperations.ListJobPreparationAndReleaseTaskStatus][net_list_jobprep_status] |[Lista o estado da tarefa de preparação e tarefas de lançamento para uma tarefa][rest_list_jobprep_status] |
| [JobOperations.ListJobs][net_list_jobs] |[Lista as tarefas numa conta][rest_list_jobs] |
| [JobOperations.ListNodeFiles][net_list_nodefiles] |[Lista os ficheiros num nó][rest_list_nodefiles] |
| [JobOperations.ListTasks][net_list_tasks] |[Lista as tarefas associadas uma tarefa][rest_list_tasks] |
| [JobScheduleOperations.ListJobSchedules][net_list_job_schedules] |[Lista as agendas de trabalho numa conta][rest_list_job_schedules] |
| [JobScheduleOperations.ListJobs][net_list_schedule_jobs] |[Lista de tarefas associadas um agendamento da tarefa][rest_list_schedule_jobs] |
| [PoolOperations.ListComputeNodes][net_list_compute_nodes] |[Lista de nós de computação num conjunto][rest_list_compute_nodes] |
| [PoolOperations.ListPools][net_list_pools] |[Listar os conjuntos de uma conta][rest_list_pools] |

### <a name="mappings-for-select-strings"></a>Mapeamentos de cadeias selecione
* **Tipos de .NET do batch**: tipos de API .NET do Batch.
* **Entidades de REST API**: cada página nesta coluna contém uma ou mais tabelas que listam os nomes das propriedades da REST API para o tipo. Estes nomes de propriedade são utilizados quando constrói *selecione* cadeias. Irá utilizar estes mesmos nomes de propriedade quando constrói um [ODATADetailLevel.SelectClause] [ odata_select] cadeia.

| Tipos de .NET do batch | Entidades de REST API |
| --- | --- |
| [Certificado][net_cert] |[Obter informações sobre um certificado][rest_get_cert] |
| [CloudJob][net_job] |[Obter informações sobre uma tarefa][rest_get_job] |
| [CloudJobSchedule][net_schedule] |[Obter informações sobre uma agenda de tarefas][rest_get_schedule] |
| [ComputeNode][net_node] |[Obter informações sobre um nó][rest_get_node] |
| [CloudPool][net_pool] |[Obter informações sobre um conjunto][rest_get_pool] |
| [CloudTask][net_task] |[Obter informações sobre uma tarefa][rest_get_task] |

## <a name="example-construct-a-filter-string"></a>Exemplo: construir uma string de filtragem
Quando constrói uma cadeia de filtro para [ODATADetailLevel.FilterClause][odata_filter], consulte a tabela acima em "Mapeamentos para cadeias de filtro de" a página de documentação de localizar a API REST que corresponde do operação de lista que pretende executar. Encontrará as propriedades filtráveis e os operadores suportados na primeira tabela multirow nessa página. Se pretender obter todas as tarefas cujo código de saída era diferente de zero, por exemplo, esta linha no [lista as tarefas associadas uma tarefa] [ rest_list_tasks] Especifica a cadeia de propriedade aplicável e operadores permitidos:

| Propriedade | Operações permitidas | Tipo |
|:--- |:--- |:--- |
| `executionInfo/exitCode` |`eq, ge, gt, le , lt` |`Int` |

Assim, a cadeia de filtro para listar todas as tarefas com um código de saída diferente de zero seria:

`(executionInfo/exitCode lt 0) or (executionInfo/exitCode gt 0)`

## <a name="example-construct-a-select-string"></a>Exemplo: construir uma cadeia selecione
Para construir [ODATADetailLevel.SelectClause][odata_select], consulte a tabela acima em "Mapeamentos para cadeias selecionadas" e navegue para a página de REST API que corresponde ao tipo de entidade que está a listar. Encontrará as propriedades selecionáveis e os operadores suportados na primeira tabela multirow nessa página. Se pretender obter apenas o ID e a linha de comandos para cada tarefa numa lista, por exemplo, irá encontrar estas linhas na tabela aplicável no [obter informações sobre uma tarefa][rest_get_task]:

| Propriedade | Tipo | Notas |
|:--- |:--- |:--- |
| `id` |`String` |`The ID of the task.` |
| `commandLine` |`String` |`The command line of the task.` |

A cadeia Selecione para incluindo apenas o ID e a linha de comandos com cada tarefa listada, em seguida, seria:

`id, commandLine`

## <a name="code-samples"></a>Exemplos de código
### <a name="efficient-list-queries-code-sample"></a>Exemplo de código de consultas de lista eficiente
Veja o [EfficientListQueries] [ efficient_query_sample] projeto de exemplo no GitHub para ver a consulta de lista de forma eficiente pode afetar o desempenho de uma aplicação. Esta aplicação de consola c# cria e adiciona um grande número de tarefas para uma tarefa. Em seguida, faz várias chamadas para o [JobOperations.ListTasks] [ net_list_tasks] método e transmite [ODATADetailLevel] [ odata] objetos que estão configuradas com valores de propriedade de diferentes para variar a quantidade de dados a ser devolvido. Produz o resultado semelhante ao seguinte:

```
Adding 5000 tasks to job jobEffQuery...
5000 tasks added in 00:00:47.3467587, hit ENTER to query tasks...

4943 tasks retrieved in 00:00:04.3408081 (ExpandClause:  | FilterClause: state eq 'active' | SelectClause: id,state)
0 tasks retrieved in 00:00:00.2662920 (ExpandClause:  | FilterClause: state eq 'running' | SelectClause: id,state)
59 tasks retrieved in 00:00:00.3337760 (ExpandClause:  | FilterClause: state eq 'completed' | SelectClause: id,state)
5000 tasks retrieved in 00:00:04.1429881 (ExpandClause:  | FilterClause:  | SelectClause: id,state)
5000 tasks retrieved in 00:00:15.1016127 (ExpandClause:  | FilterClause:  | SelectClause: id,state,environmentSettings)
5000 tasks retrieved in 00:00:17.0548145 (ExpandClause: stats | FilterClause:  | SelectClause: )

Sample complete, hit ENTER to continue...
```

Como é mostrado no tempo decorrido, pode reduzir significativamente os tempos de resposta de consulta ao limitar as propriedades e o número de itens que são devolvidos. Pode encontrar esta e outros projetos de exemplo a [azure-batch-samples] [ github_samples] repositório no GitHub.

### <a name="batchmetrics-library-and-code-sample"></a>BatchMetrics biblioteca e o código de exemplo
Para além de EfficientListQueries código de exemplo acima, pode encontrar o [BatchMetrics] [ batch_metrics] projeto no [azure-batch-samples] [ github_samples]Repositório do GitHub. O projeto de exemplo BatchMetrics demonstra como monitorizar eficazmente o progresso da tarefa de lote do Azure utilizando a API do Batch.

O [BatchMetrics] [ batch_metrics] exemplo inclui um projeto de biblioteca de classe de .NET que poderá incorporar nos seus projetos e um programa de linha de comandos simple para exercer e demonstram a utilização da biblioteca.

A aplicação de exemplo no projeto demonstra as seguintes operações:

1. Selecionar atributos específicos para transferir apenas as propriedades que precisa
2. Filtragem em tempos de transição de estado para transferir apenas alterações desde a última consulta

Por exemplo, é apresentado o seguinte método na biblioteca BatchMetrics. Devolve um ODATADetailLevel que especifica que apenas o `id` e `state` propriedades devem ser obtidas para as entidades que estão a ser consultadas. Também especifica que apenas as entidades cujo estado foi alterada desde a especificado `DateTime` parâmetro deve ser devolvido.

```csharp
internal static ODATADetailLevel OnlyChangedAfter(DateTime time)
{
    return new ODATADetailLevel(
        selectClause: "id, state",
        filterClause: string.Format("stateTransitionTime gt DateTime'{0:o}'", time)
    );
}
```

## <a name="next-steps"></a>Passos seguintes
### <a name="parallel-node-tasks"></a>Tarefas de nó paralela
[Maximizar a utilização de recursos de computação do Azure Batch com tarefas de nó simultâneas](batch-parallel-node-tasks.md) outro artigo relacionado com o desempenho de aplicações de Batch. Alguns tipos de cargas de trabalho podem beneficiar da execução de tarefas paralelas em grande – mas menos – nós de computação. Veja o [cenário de exemplo](batch-parallel-node-tasks.md#example-scenario) no artigo para obter detalhes sobre o cenário.

### <a name="batch-forum"></a>Fórum do batch
O [fórum do Azure Batch] [ forum] no MSDN é um excelente local para discutir Batch e de fazer perguntas sobre o serviço. HEAD na ativação pós-falha para mensagens "temporária" úteis e publique as suas perguntas que possam surgir ao criar as soluções do Batch.

[api_net]: http://msdn.microsoft.com/library/azure/mt348682.aspx
[api_net_listjobs]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.joboperations.listjobs.aspx
[api_rest]: http://msdn.microsoft.com/library/azure/dn820158.aspx
[batch_metrics]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/BatchMetrics
[efficient_query_sample]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/EfficientListQueries
[forum]: https://social.msdn.microsoft.com/forums/azure/en-US/home?forum=azurebatch
[github_samples]: https://github.com/Azure/azure-batch-samples
[odata]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.odatadetaillevel.aspx
[odata_ctor]: https://msdn.microsoft.com/library/azure/dn866178.aspx
[odata_expand]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.odatadetaillevel.expandclause.aspx
[odata_filter]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.odatadetaillevel.filterclause.aspx
[odata_select]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.odatadetaillevel.selectclause.aspx

[net_list_certs]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.certificateoperations.listcertificates.aspx
[net_list_compute_nodes]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.listcomputenodes.aspx
[net_list_job_schedules]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.jobscheduleoperations.listjobschedules.aspx
[net_list_jobprep_status]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.joboperations.listjobpreparationandreleasetaskstatus.aspx
[net_list_jobs]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.joboperations.listjobs.aspx
[net_list_nodefiles]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.joboperations.listnodefiles.aspx
[net_list_pools]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.listpools.aspx
[net_list_schedule_jobs]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.jobscheduleoperations.listjobs.aspx
[net_list_task_files]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask.listnodefiles.aspx
[net_list_tasks]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.joboperations.listtasks.aspx

[rest_list_certs]: https://msdn.microsoft.com/library/azure/dn820154.aspx
[rest_list_compute_nodes]: https://msdn.microsoft.com/library/azure/dn820159.aspx
[rest_list_job_schedules]: https://msdn.microsoft.com/library/azure/mt282174.aspx
[rest_list_jobprep_status]: https://msdn.microsoft.com/library/azure/mt282170.aspx
[rest_list_jobs]: https://msdn.microsoft.com/library/azure/dn820117.aspx
[rest_list_nodefiles]: https://msdn.microsoft.com/library/azure/dn820151.aspx
[rest_list_pools]: https://msdn.microsoft.com/library/azure/dn820101.aspx
[rest_list_schedule_jobs]: https://msdn.microsoft.com/library/azure/mt282169.aspx
[rest_list_task_files]: https://msdn.microsoft.com/library/azure/dn820142.aspx
[rest_list_tasks]: https://msdn.microsoft.com/library/azure/dn820187.aspx

[rest_get_cert]: https://msdn.microsoft.com/library/azure/dn820176.aspx
[rest_get_job]: https://msdn.microsoft.com/library/azure/dn820106.aspx
[rest_get_node]: https://msdn.microsoft.com/library/azure/dn820168.aspx
[rest_get_pool]: https://msdn.microsoft.com/library/azure/dn820165.aspx
[rest_get_schedule]: https://msdn.microsoft.com/library/azure/mt282171.aspx
[rest_get_task]: https://msdn.microsoft.com/library/azure/dn820133.aspx

[net_cert]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.certificate.aspx
[net_job]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudjob.aspx
[net_node]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.computenode.aspx
[net_pool]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.aspx
[net_schedule]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudjobschedule.aspx
[net_task]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask.aspx

[rest_get_task_counts]: https://docs.microsoft.com/rest/api/batchservice/get-the-task-counts-for-a-job