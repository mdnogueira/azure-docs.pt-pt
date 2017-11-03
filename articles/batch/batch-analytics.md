---
title: "Análise de lote do Azure | Microsoft Docs"
description: "Referência para a análise de lote do Azure."
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
ms.openlocfilehash: 7d634e1bb595a84b8af339e5bc5a483a7849e7f7
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="batch-analytics"></a>Análise de lote
Os tópicos na análise de lote contêm informações de referência para os eventos e alertas disponíveis para recursos do serviço Batch.

Consulte [registo de diagnóstico do Azure Batch](https://azure.microsoft.com/documentation/articles/batch-diagnostics/) para obter mais informações sobre a ativação e o consumo de registos de diagnóstico do Batch.

## <a name="diagnostic-logs"></a>Registos de diagnósticos

O serviço Azure Batch emite os seguintes eventos de registo de diagnóstico durante a duração de certos recursos do Batch.

**Eventos de registo do serviço**
* [Criar conjunto](batch-pool-create-event.md)
* [Início de eliminação do conjunto](batch-pool-delete-start-event.md)
* [Eliminar conjunto concluída](batch-pool-delete-complete-event.md)
* [Início de redimensionamento de conjunto](batch-pool-resize-start-event.md)
* [Redimensionamento de agrupamento completo](batch-pool-resize-complete-event.md)
* [Início da tarefa](batch-task-start-event.md)
* [Tarefa concluída](batch-task-complete-event.md)
* [Falha de tarefa](batch-task-fail-event.md)