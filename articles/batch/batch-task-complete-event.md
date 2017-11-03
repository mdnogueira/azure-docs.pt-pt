---
title: Evento completa do Azure Batch tarefas | Microsoft Docs
description: "Referência para o evento de conclusão de tarefas do Batch."
services: batch
author: tamram
manager: timlt
ms.assetid: 
ms.service: batch
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: big-compute
ms.date: 04/20/2017
ms.author: tamram
ms.openlocfilehash: 015adf7dbc47c29a78df4e4889b2ee1ddcccdd8e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="task-complete-event"></a>Evento de tarefa concluído

 Este evento é emitido depois de uma tarefa é concluída, independentemente do código de saída. Este evento pode ser utilizado para determinar a duração de uma tarefa, onde a tarefa foi executada e se foi repetida.


 O exemplo seguinte mostra o corpo de um evento de conclusão de tarefas.

```
{
    "jobId": "job-0000000001",
    "id": "task-5",
    "taskType": "User",
    "systemTaskVersion": 0,
    "nodeInfo": {
        "poolId": "pool-001",
        "nodeId": "tvm-257509324_1-20160908t162728z"
    },
    "multiInstanceSettings": {
        "numberOfInstances": 1
    },
    "constraints": {
        "maxTaskRetryCount": 2
    },
    "executionInfo": {
        "startTime": "2016-09-08T16:32:23.799Z",
        "endTime": "2016-09-08T16:34:00.666Z",
        "exitCode": 0,
        "retryCount": 0,
        "requeueCount": 0
    }
}
```

|Nome do elemento|Tipo|Notas|
|------------------|----------|-----------|
|JobId|Cadeia|O id da tarefa que contém a tarefa.|
|ID|Cadeia|O id da tarefa.|
|taskType|Cadeia|O tipo da tarefa. Isto pode ser 'JobManager', indicando que é uma tarefa de gestão ou 'User', que indica que não se trata de uma tarefa de gestão. Este evento não é emitido para tarefas de preparação, tarefas de lançamento ou tarefas de início.|
|systemTaskVersion|Int32|Este é o contador de repetições internas uma tarefa. Internamente o serviço Batch pode tentar novamente uma tarefa a problemas temporários. Estes problemas podem incluir erros de agendamento internos ou tenta recuperar a partir de nós de computação num Estado incorreto.|
|[nodeInfo](#nodeInfo)|Tipo complexo|Contém informações sobre o nó de computação em que a tarefa foi executada.|
|[multiInstanceSettings](#multiInstanceSettings)|Tipo complexo|Especifica que a tarefa é uma tarefa de várias instâncias que necessitam de vários nós de computação.  Consulte [multiInstanceSettings](https://docs.microsoft.com/rest/api/batchservice/get-information-about-a-task) para obter mais detalhes.|
|[restrições](#constraints)|Tipo complexo|As restrições de execução que se aplicam a esta tarefa.|
|[executionInfo](#executionInfo)|Tipo complexo|Contém informações sobre a execução da tarefa.|

###  <a name="nodeInfo"></a>nodeInfo

|Nome do elemento|Tipo|Notas|
|------------------|----------|-----------|
|poolId|Cadeia|O id do conjunto no qual a tarefa foi executada.|
|nodeId|Cadeia|O id do nó no qual a tarefa foi executada.|

###  <a name="multiInstanceSettings"></a>multiInstanceSettings

|Nome do elemento|Tipo|Notas|
|------------------|----------|-----------|
|numberOfInstances|Int32|O número de nós de computação necessária para a tarefa.|

###  <a name="constraints"></a>restrições

|Nome do elemento|Tipo|Notas|
|------------------|----------|-----------|
|maxTaskRetryCount|Int32|O número máximo de vezes que a tarefa pode ser repetida. O serviço Batch repete uma tarefa se o código de saída é diferente de zero.<br /><br /> Tenha em atenção que este valor controla especificamente o número de tentativas. O serviço Batch irá tentar a tarefa uma vez e, em seguida, pode voltar a tentar até este limite. Por exemplo, se a contagem de repetições máxima for 3, tenta Batch uma tarefa até 4 vezes (um tente inicial e 3 tentativas).<br /><br /> Se a contagem de repetições máxima for 0, o serviço Batch não repete a ação tarefas.<br /><br /> Se a contagem de repetições máxima for -1, o serviço Batch repete tarefas sem limite.<br /><br /> O valor predefinido é 0 (nenhum tentativas).|

###  <a name="executionInfo"></a>executionInfo

|Nome do elemento|Tipo|Notas|
|------------------|----------|-----------|
|startTime|DateTime|A hora em que a tarefa foi iniciada em execução. 'Executar' corresponde à **executar** Estado, pelo que o se a tarefa Especifica os ficheiros de recursos ou pacotes de aplicações, em seguida, a hora de início reflete a hora em que a tarefa foi iniciada a transferir ou implementar estes.  Se a tarefa foi reiniciada ou repetida, este é o tempo mais recente que a tarefa foi iniciada em execução.|
|endTime|DateTime|A hora em que a tarefa foi concluída.|
|exitCode|Int32|O código de saída da tarefa.|
|retryCount|Int32|O número de vezes que a tarefa foi repetiu pelo serviço Batch. A tarefa é repetida se sai com um código de saída diferente de zero, até o MaxTaskRetryCount especificado.|
|requeueCount|Int32|O número de vezes que a tarefa tem foi recolocado em fila pelo serviço Batch como resultado de um pedido de utilizador.<br /><br /> Quando o utilizador remove nós de um agrupamento (ao redimensionar ou reduzir o conjunto) ou quando a tarefa está a ser desativada, o utilizador pode especificar que as tarefas em execução em nós de ser colocadas em fila para execução. Esta contagem controla o número de vezes a tarefa foi recolocado em fila por estes motivos.|
