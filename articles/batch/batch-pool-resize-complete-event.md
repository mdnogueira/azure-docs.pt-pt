---
title: Evento completa de redimensionar conjunto do Azure Batch | Microsoft Docs
description: "Referência para o conjunto do Batch redimensionar evento completa."
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
ms.openlocfilehash: 7072293d98526812cb42ce9c2f8e33bfcafaa149
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="pool-resize-complete-event"></a>Evento de conclusão de redimensionamento de conjunto

 Este evento é emitido quando um conjunto de redimensionamento foi concluída ou falhada.

 O exemplo seguinte mostra o corpo de um evento de conclusão de redimensionamento de agrupamento para um conjunto maior de tamanho e foi concluída com êxito.

```
{
    "id": "p_1_0_01503750-252d-4e57-bd96-d6aa05601ad8",
    "nodeDeallocationOption": "invalid",
    "currentDedicated": 4,
    "targetDedicated": 4,
    "enableAutoScale": false,
    "isAutoPool": false,
    "startTime": "2016-09-09T22:13:06.573Z",
    "endTime": "2016-09-09T22:14:01.727Z",
    "result": "Success",
    "resizeError": "The operation succeeded"
}
```

|Elemento|Tipo|Notas|
|-------------|----------|-----------|
|ID|Cadeia|O id do conjunto.|
|nodeDeallocationOption|Cadeia|Especifica quando nós podem ser removidos do conjunto, se estiver a diminuir o tamanho do conjunto.<br /><br /> Os valores possíveis são:<br /><br /> **requeue** – terminar as tarefas em execução e requeue-los. As tarefas serão executadas novamente quando a tarefa está ativada. Remova nós assim que as tarefas forem terminadas.<br /><br /> **terminar** – terminar as tarefas em execução. As tarefas não serão executadas novamente. Remova nós assim que as tarefas forem terminadas.<br /><br /> **taskcompletion** – permitir que as tarefas atualmente em execução para concluir. Não agende novas tarefas ao aguardar a resposta. Remova nós quando todas as tarefas estiverem concluídas.<br /><br /> **Retaineddata** -permitir atualmente em execução das tarefas de conclusão, em seguida, aguarde que todas as tarefas períodos de retenção de dados a expirar. Não agende novas tarefas ao aguardar a resposta. Remova nós quando todos os períodos de retenção de tarefas tem expirado.<br /><br /> O valor predefinido é requeue.<br /><br /> Se estiver a aumentar o tamanho do conjunto, em seguida, o valor é definido como **inválido**.|
|currentDedicated|Int32|O número de nós de computação atualmente atribuídos ao agrupamento.|
|targetDedicated|Int32|O número de nós de computação que são pedidos para o conjunto.|
|enableAutoScale|bool|Especifica se o tamanho do conjunto ajusta automaticamente ao longo do tempo.|
|isAutoPool|bool|Especifica se o conjunto foi criado através do mecanismo de AutoPool de uma tarefa.|
|startTime|DateTime|O tempo redimensionar o conjunto foi iniciado.|
|endTime|DateTime|A hora de redimensionar o conjunto foi concluída.|
|resultCode|Cadeia|O resultado do redimensionamento.|
|resultMessage|Cadeia|O erro de redimensionamento inclui os detalhes do resultado.<br /><br /> Se o redimensionamento foi concluída com êxito-Estados de que a operação foi concluída com êxito.|