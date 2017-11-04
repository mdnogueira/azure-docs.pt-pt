---
title: "Evitar interrupções do serviço com tarefas do Azure Stream Analytics | Microsoft Docs"
description: "Orientações sobre a efetuar o Stream Analytics tarefas de atualização resiliente."
services: stream-analytics
documentationCenter: 
authors: samacha
manager: jhubbard
editor: cgronlun
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 03/28/2017
ms.author: samacha
ms.openlocfilehash: 76e347ee62ffc07db1d8e74cf0ac5327a154fe4f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="guarantee-stream-analytics-job-reliability-during-service-updates"></a>Garante fiabilidade de tarefa do Stream Analytics durante as atualizações de serviço

Parte do que está a ser um serviço completamente gerido é a capacidade para introduzir novas funcionalidades de serviço e melhoramentos a um ritmo rápido. Como resultado, o Stream Analytics pode ter uma atualização de serviço implementar numa base semanal (ou mais frequentes). Independentemente da quantidade de teste é efetuada há ainda um risco de uma tarefa existente, em execução poderá interromper devido à introdução de um erro. Para os clientes que executam tarefas de processamento de transmissão em fluxo críticos estes riscos tem de ser evitadas. Um mecanismo que os clientes podem utilizar para reduzir este risco é do Azure  **[região emparelhado](https://docs.microsoft.com/azure/best-practices-availability-paired-regions)**  modelo. 

## <a name="how-do-azure-paired-regions-address-this-concern"></a>Como é que regiões do Azure de emparelhado lidar com esta preocupação?

Do Stream Analytics garante que as tarefas em regiões emparelhadas são atualizadas em lotes separados. Como resultado é um intervalo de tempo suficiente entre as atualizações para identificar potenciais erros de quebra e remedeiam-nos.

_Índia Central com a exceção_ (cujo região emparelhada, Sul da Índia e não tiver a presença de Stream Analytics), a implementação de uma atualização do Stream Analytics não ocorreriam ao mesmo tempo num conjunto de regiões emparelhados. Implementações em várias regiões **no mesmo grupo** podem ocorrer **ao mesmo tempo**.

O artigo  **[disponibilidade e regiões emparelhadas](https://docs.microsoft.com/azure/best-practices-availability-paired-regions)**  tem as informações mais atualizadas em que regiões estão emparelhadas.

Os clientes aconselhados implementar tarefas idênticas em ambas as regiões emparelhadas. Para além do Stream Analytics interno capacidades de monitorização, os clientes também aconselha-se para monitorizar as tarefas como se **ambos** tarefas de produção. Se for identificada uma quebra de ser um resultado da atualização de serviço do Stream Analytics, escalar corretamente e efetuar a ativação pós-falha de quaisquer consumidores a jusante para o resultado da tarefa em bom estado. Escalamento de suporte irá impedir que a região emparelhada sejam afetados pela nova implementação e manter a integridade das tarefas de emparelhado.
