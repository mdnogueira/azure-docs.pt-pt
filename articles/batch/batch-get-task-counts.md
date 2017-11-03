---
title: Monitorizar o progresso da tarefa por contagem de tarefas por Estado - Azure Batch | Microsoft Docs
description: "Monitorize o progresso de uma tarefa chamando a operação de contagens de tarefa de obter a contagem de tarefas para uma tarefa. Pode obter uma contagem de tarefas do Active Directory, em execução e foi concluídas e, por tarefas que foi concluída com êxito ou falha."
services: batch
author: tamram
manager: timlt
ms.service: batch
ms.topic: article
ms.date: 08/02/2017
ms.author: tamram
ms.openlocfilehash: ceff59d7063b60a1344a47489d3d73e0e8ee07df
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="count-tasks-by-state-to-monitor-a-jobs-progress-preview"></a>Contagem de tarefas por Estado para monitorizar o progresso da tarefa (pré-visualização)

O Azure Batch proporciona uma forma eficaz para monitorizar o progresso de uma tarefa, como as respetivas tarefas é executada. Pode chamar o [obter contagens de tarefa] [ rest_get_task_counts] operação para descobrir como muitas tarefas estão num Estado em execução, Active Directory ou foi concluído e tem quantos foi concluída com êxito ou falha. Pelo número de tarefas em cada Estado de contagem, mais facilmente pode apresentar o progresso da tarefa para um utilizador ou detetar atrasos inesperados ou falhas que podem afetar a tarefa.

> [!IMPORTANT]
> A operação obter contagens de tarefa está atualmente em pré-visualização e ainda não está disponível no Azure Government, Azure China e Datacenters do Azure. 
>
>

## <a name="how-tasks-are-counted"></a>Como as tarefas são contadas

A operação de contagens de tarefa obter contagens tarefas por Estado, da seguinte forma:

- Uma tarefa é contabilizada como **Active Directory** quando é executado em fila e conseguir, mas não está atualmente atribuída a um nó de computação. Uma tarefa também é contabilizada como **Active Directory** se está dependente de uma tarefa principal que ainda não foi concluído. Para obter mais informações sobre as dependências de tarefas, consulte [criar as dependências de tarefas para executar as tarefas que dependem de outras tarefas](batch-task-dependencies.md). 
- Uma tarefa é contabilizada como **executar** quando este foi atribuído a um nó de computação, mas ainda não foi concluído. Uma tarefa é contabilizada como **executar** quando o estado é `preparing` ou `running`, conforme indicado pelo [obter informações sobre uma tarefa] [ rest_get_task] operação.
- Uma tarefa é contabilizada como **concluída** quando já não é elegível para ser executada. Uma tarefa contabilizado como **concluída** tem normalmente concluída com êxito, ou foi concluída sem êxito e também esgotou o respetivo limite de repetição. 

A operação de contagens de tarefa obter relatórios também como muitas tarefas tem êxito ou falhou. Batch determina se uma tarefa teve êxito ou falha ao verificar o **resultado** propriedade da propriedade [executionInfo] [https://docs.microsoft.com/rest/api/batchservice/get-information-about-a-task#executionInfo]:

    - Uma tarefa é contabilizada como **foi concluída com êxito** se o resultado da execução da tarefa é `success`.
    - Uma tarefa é contabilizada como **falha** se o resultado da execução da tarefa é `failure`.

Para mais informações sobre os Estados de tarefas, consulte [obter informações sobre uma tarefa][rest_get_task].

O exemplo de código .NET seguinte mostra como obter contagens de tarefas por Estado: 

```csharp
var taskCounts = await batchClient.JobOperations.GetTaskCountsAsync("job-1");

Console.WriteLine("Task count in active state: {0}", taskCounts.Active);
Console.WriteLine("Task count in preparing or running state: {0}", taskCounts.Running);
Console.WriteLine("Task count in completed state: {0}", taskCounts.Completed);
Console.WriteLine("Succeeded task count: {0}", taskCounts.Succeeded);
Console.WriteLine("Failed task count: {0}", taskCounts.Failed);
Console.WriteLine("ValidationStatus: {0}", taskCounts.ValidationStatus);
```

> [!NOTE]
> Pode utilizar um padrão semelhante para REST e outros idiomas suportados para obter contagens de tarefas para uma tarefa. 
> 
> 

## <a name="consistency-checking-for-task-counts"></a>Verificação de contagens de tarefa de consistência

O serviço Batch agrega contagens de tarefa por a recolha de dados de várias partes de um sistema distribuída assíncrona. Para garantir que as contagens de tarefas estão corretas, o Batch fornece validação adicional para contagens de estado, efetuando a consistência verifica contra vários componentes do sistema. Batch executa estas verificações de consistência, desde que existem menos de 200 000 tarefas na tarefa. Na improvável eventualidade que a verificação de consistência encontra erros, o Batch corrige o resultado da operação obter contagens de tarefas com base nos resultados da verificação de consistência. A verificação de consistência é uma medida adicional para garantir que os clientes que dependem da operação obter contagens de tarefa obtêm as informações corretas que necessitam para a solução.

O **validationStatus** propriedade na resposta indica se o Batch foi efetuada a verificação de consistência. Se o lote não foi possível verificar o estado é contabilizada nos Estados reais contidos no sistema, em seguida, a **validationStatus** propriedade está definida como `unvalidated`. Por motivos de desempenho, Batch irá não efetuar a verificação de consistência se a tarefa inclui mais de 200 000 tarefas, o **validationStatus** propriedade pode ser definida como `unvalidated` neste caso. No entanto, a contagem de tarefas não é necessariamente errada neste caso, dado que, mesmo a perda de dados muito limitado é muito pouco provável. 

Quando uma tarefa muda de estado, o pipeline de agregação processa a alteração dentro de alguns segundos. A operação de obter a contagem de tarefas reflete o número de tarefas atualizado durante esse período. No entanto, se o pipeline de agregação pedidos sem êxito uma alteração de estado de tarefas, em seguida, essa alteração não está registada até a passagem de validação seguinte. Durante este período, contagens de tarefas podem ser ligeiramente incorretas devido ao evento em falta, mas estes são corrigidas na passagem de validação seguinte.

## <a name="best-practices-for-counting-a-jobs-tasks"></a>Melhores práticas para tarefas do trabalho de contagem

Chamar a operação de obter a contagem de tarefas é a forma mais eficiente para devolver uma contagem básica de tarefas do trabalho por Estado. Se estiver a utilizar versão de serviço de Batch de 2017-06-01.5.1, recomendamos que escrever ou atualizar o seu código para utilizar a obter a contagem de tarefas.

A operação de obter a contagem de tarefas anteriormente 2017-06-01.5.1 não está disponível nas versões do serviço de Batch. Se estiver a utilizar uma versão mais antiga do serviço, em seguida, utilize uma consulta de lista para em vez disso, a contagem de tarefas numa tarefa. Para obter mais informações, consulte [consultas de criar a lista de forma eficiente recursos do Batch](batch-efficient-list-queries.md).

## <a name="next-steps"></a>Passos seguintes

* Consulte a [Batch feature overview (Descrição geral da funcionalidade do Batch)](batch-api-basics.md) para saber mais sobre conceitos e funcionalidades de serviço do Batch. O artigo aborda os recursos do Batch principais como conjuntos, nós de computação, trabalhos e tarefas e fornece uma descrição geral das funcionalidades do serviço.
* Aprenda os conceitos básicos de programação de uma aplicação compatível com o Batch ao utilizar a [biblioteca de cliente .NET do Batch](batch-dotnet-get-started.md) ou [Python](batch-python-tutorial.md). Estes artigos introdutórias ajudá-lo através de uma aplicação de trabalho que utiliza o serviço Batch para executar uma carga de trabalho em vário nós de computação.


[rest_get_task_counts]: https://docs.microsoft.com/rest/api/batchservice/get-the-task-counts-for-a-job
[rest_get_task]: https://docs.microsoft.com/rest/api/batchservice/get-information-about-a-task
[rest_list_tasks]: https://docs.microsoft.com/rest/api/batchservice/list-the-tasks-associated-with-a-job
