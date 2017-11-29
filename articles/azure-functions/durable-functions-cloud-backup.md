---
title: "Fan-out/fan-em cenários de funções durável - Azure"
description: "Saiba como implementar um cenário fan-out-ventoinha-na extensão de funções durável para as funções do Azure."
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
ms.openlocfilehash: 85484b79012243afd374a97e7f518e9a8b1043ea
ms.sourcegitcommit: cf42a5fc01e19c46d24b3206c09ba3b01348966f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/29/2017
---
# <a name="fan-outfan-in-scenario-in-durable-functions---cloud-backup-example"></a>Cenário de fan-out/fan-em funções durável - exemplo de cópia de segurança de nuvem

*Fan-out/fan-em* refere-se o padrão de executar várias funções em simultâneo e, em seguida, efetuar algumas agregação nos resultados. Este artigo explica um exemplo que utiliza [funções durável](durable-functions-overview.md) para implementar um cenário fan-em/fan-out. O exemplo é uma função durável que efetua cópias de segurança todos ou alguns dos conteúdos do site de uma aplicação para o armazenamento do Azure.

## <a name="prerequisites"></a>Pré-requisitos

* Siga as instruções em [instalar funções durável](durable-functions-install.md) para configurar o exemplo.
* Este artigo pressupõe que já leu o [Hello sequência](durable-functions-sequence.md) instruções de exemplo.

## <a name="scenario-overview"></a>Descrição geral do cenário

Neste exemplo, as funções de carregar todos os ficheiros em recursivamente um diretório especificado para o armazenamento de Blobs. Estes contagem também o número total de bytes que foram carregados.

É possível escrever uma única função que trata da tudo. É o problema principal, deverá executar para **escalabilidade**. Uma execução única função só pode ser executada numa única VM, pelo que o débito será limitado pelo débito dessa VM única. Outro problema está **fiabilidade**. Se não houver um meio de falhas através de, ou se todo o processo demora mais de 5 minutos, a cópia de segurança poderá falhar num Estado parcialmente concluída. Em seguida, teria de ser reiniciado.

Uma abordagem mais robusta seria escrever duas funções regulares: um seria enumerar os ficheiros e adicionar os nomes de ficheiro para uma fila, e outro teria de leitura da fila e carregar os ficheiros para o blob storage. Esta é uma melhor em termos de débito e a fiabilidade, mas requer a aprovisionar e gerir uma fila. Mais importante ainda, a complexidade significativa é apresentada em termos de **Estado gestão** e **coordenação** se pretender fazer mais nada, como o relatório o número total de bytes carregado.

Uma abordagem de funções durável dá-lhe todas as vantagens mencionadas com sobrecarga muito baixa.

## <a name="the-functions"></a>As funções

Este artigo explica as seguintes funções na aplicação de exemplo:

* `E2_BackupSiteContent`
* `E2_GetFileList`
* `E2_CopyFileToBlob`

As secções seguintes explicam o código que são utilizados para programação portal do Azure e configuração. O código para o desenvolvimento de Visual Studio é apresentado no final do artigo.

## <a name="the-cloud-backup-orchestration"></a>A orquestração de cópia de segurança de nuvem

O `E2_BackupSiteContent` função utiliza a norma *function.json* para funções do orchestrator.

[!code-json[Main](~/samples-durable-functions/samples/csx/E2_BackupSiteContent/function.json)]

Eis o código que implementa a função do orchestrator:

[!code-csharp[Main](~/samples-durable-functions/samples/csx/E2_BackupSiteContent/run.csx)]

Esta função do orchestrator, essencialmente, faz o seguinte:

1. Assume um `rootDirectory` valor como um parâmetro de entrada.
2. Chama uma função para obter uma lista de recursiva dos ficheiros em `rootDirectory`.
3. Faz com que várias chamadas de função paralelas para carregar cada ficheiro para o Blob Storage do Azure.
4. Aguarda para todos os carregamentos concluir.
5. Devolve a soma total de bytes que foram carregados para o Blob Storage do Azure.

Tenha em atenção o `await Task.WhenAll(tasks);` linha. Todas as chamadas para o `E2_CopyFileToBlob` função foram *não* aguardado. Isto é intencional para permitir que sejam executadas em paralelo. Quando é passar esta matriz de tarefas para `Task.WhenAll`, vamos voltar a uma tarefa que não concluir *até que concluíram todas as operações de cópia*. Se estiver familiarizado com a tarefa paralela biblioteca (TPL) no .NET, em seguida, este não é novidade para si. A diferença é que estas tarefas poderão estar em execução em várias VMs em simultâneo, e a extensão de funções durável garante que a execução de ponto a ponto seja resistente a Reciclagem de processo.

Depois de aguardar de `Task.WhenAll`, sabemos que todas as chamadas de função concluíram e de tem devolvido fazer uma cópia de valores para nós. Cada chamada para `E2_CopyFileToBlob` devolve o número de bytes carregado, pelo que a calcular a contagem de total de bytes de soma um fim de adição de todos os os valores de retorno em conjunto.

## <a name="helper-activity-functions"></a>Funções de atividade de programa auxiliar

As funções de atividade de programa auxiliar, tal como acontece com outros exemplos, são apenas regulares funções que utilizam o `activityTrigger` acionar o enlace. Por exemplo, o *function.json* de ficheiros para `E2_GetFileList` se parece com o seguinte:

[!code-json[Main](~/samples-durable-functions/samples/csx/E2_GetFileList/function.json)]

E Eis a implementação:

[!code-csharp[Main](~/samples-durable-functions/samples/csx/E2_GetFileList/run.csx)]

> [!NOTE]
> Poderá estar a pensar por que motivo apenas não foi possível colocar este código diretamente para a função do orchestrator. Foi, mas isto iria interromper as regras fundamentais de funções do orchestrator, que é que nunca deve fazer e/s, incluindo acesso de sistema de ficheiros local.

O *function.json* de ficheiros para `E2_CopyFileToBlob` é da mesma forma simples:

[!code-json[Main](~/samples-durable-functions/samples/csx/E2_CopyFileToBlob/function.json)]

A implementação também é pretty simples. Ocorre utilizar algumas funcionalidades de enlaces de funções do Azure de avançadas (ou seja, a utilização do `Binder` parâmetro), mas não precisa de preocupar com os detalhes para fins destas instruções.

[!code-csharp[Main](~/samples-durable-functions/samples/csx/E2_CopyFileToBlob/run.csx)]

A implementação carrega o ficheiro de disco e no modo assíncrono as sequências de conteúdo para um blob com o mesmo nome no contentor "cópias de segurança". O valor de retorno é o número de bytes copiadas para o armazenamento, o que é utilizado pela função do orchestrator para a soma de agregação de computação.

> [!NOTE]
> Este é um exemplo perfeito de mover as operações de e/s para um `activityTrigger` função. Não só o trabalho pode ser distribuído por várias VMs diferentes, mas o utilizador também obtém as vantagens de pontos de verificação o progresso. Se o processo de anfitrião obtém terminado por qualquer motivo, sabe que carregamentos já tem concluído.

## <a name="run-the-sample"></a>Executar o exemplo

Pode começar a orquestração enviando o seguinte pedido de HTTP POST.

```
POST http://{host}/orchestrators/E2_BackupSiteContent
Content-Type: application/json
Content-Length: 20

"D:\\home\\LogFiles"
```

> [!NOTE]
> O `HttpStart` função que está a invocar só funciona com conteúdo formatado em JSON. Por este motivo, o `Content-Type: application/json` cabeçalho é necessário e o caminho do diretório está codificado como uma cadeia JSON.

Este HTTP do pedido acionadores a `E2_BackupSiteContent` orchestrator e passa a cadeia `D:\home\LogFiles` como parâmetro. A resposta fornece uma ligação para obter o estado da operação de cópia de segurança:

```
HTTP/1.1 202 Accepted
Content-Length: 719
Content-Type: application/json; charset=utf-8
Location: http://{host}/admin/extensions/DurableTaskExtension/instances/b4e9bdcc435d460f8dc008115ff0a8a9?taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}

(...trimmed...)
```

Dependendo de quantos ficheiros de registo na sua aplicação de função, esta operação pode demorar vários minutos a concluir. Pode obter o estado mais recente através da consulta no URL no `Location` cabeçalho de resposta de HTTP 202 anterior.

```
GET http://{host}/admin/extensions/DurableTaskExtension/instances/b4e9bdcc435d460f8dc008115ff0a8a9?taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}
```

```
HTTP/1.1 202 Accepted
Content-Length: 148
Content-Type: application/json; charset=utf-8
Location: http://{host}/admin/extensions/DurableTaskExtension/instances/b4e9bdcc435d460f8dc008115ff0a8a9?taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}

{"runtimeStatus":"Running","input":"D:\\home\\LogFiles","output":null,"createdTime":"2017-06-29T18:50:55Z","lastUpdatedTime":"2017-06-29T18:51:16Z"}
```

Neste caso, a função ainda está em execução. É capaz de ver a entrada que foi guardada o estado do orchestrator e a última vez atualizada. Pode continuar a utilizar o `Location` os valores de cabeçalho para consultar de conclusão. Quando o estado é "concluído", será apresentado um valor de resposta HTTP semelhante ao seguinte:

```
HTTP/1.1 200 OK
Content-Length: 152
Content-Type: application/json; charset=utf-8

{"runtimeStatus":"Completed","input":"D:\\home\\LogFiles","output":452071,"createdTime":"2017-06-29T18:50:55Z","lastUpdatedTime":"2017-06-29T18:51:26Z"}
```

Agora, pode ver que o orchestration estiver concluído e aproximadamente quanto tempo demorou a concluir. Consulte também um valor para o `output` campo, o que indica que cerca de 450 KB de registos foram carregados.

## <a name="visual-studio-sample-code"></a>Código de exemplo do Visual Studio

Eis o orchestration como um único c# ficheiro um projeto do Visual Studio:

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/BackupSiteContent.cs)]

## <a name="next-steps"></a>Passos seguintes

Este exemplo foi apresentado como implementar o padrão fan-out/fan-em. O exemplo seguinte mostra como implementar o [singleton com monitorização de estado](durable-functions-singletons.md) padrão num [eternal orchestration](durable-functions-eternal-orchestrations.md).

> [!div class="nextstepaction"]
> [Executar o exemplo de singleton com monitorização de estado](durable-functions-counter.md)
