---
title: "Desempenho e dimensionamento em funções durável - Azure"
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
ms.openlocfilehash: 10ce74097388a0283797e4692126c5039e8d4dd0
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="performance-and-scale-in-durable-functions-azure-functions"></a>Desempenho e dimensionamento em funções durável (funções do Azure)

Para otimizar o desempenho e escalabilidade, é importante compreender as características de dimensionamento exclusivas de [funções durável](durable-functions-overview.md).

Para compreender o comportamento de dimensionamento, tem de compreender alguns dos detalhes do fornecedor de armazenamento do Azure subjacente utilizado pelas funções durável.

## <a name="history-table"></a>Tabela de histórico

A tabela de histórico é uma tabela de armazenamento do Azure que contém os eventos de histórico para todas as instâncias de orquestração. Como executam instâncias, novas linhas são adicionadas a esta tabela. A chave de partição desta tabela é derivada do ID de instância da orquestração. Estes valores são aleatórios na maioria dos casos, que garante uma distribuição ideal de partições internas no armazenamento do Azure.

## <a name="internal-queue-triggers"></a>Acionadores de filas interno

Atividade e as funções do Orchestrator são acionadas por internas filas na conta do storage predefinida da aplicação de função. Existem dois tipos de filas em funções durável: o **fila controlo** e **fila de item de trabalho**.

### <a name="the-work-item-queue"></a>A fila de item de trabalho

Há uma fila de item de trabalho por hub tarefas nas funções durável. Esta é uma fila básica e funciona de forma semelhante a qualquer outro `queueTrigger` fila das funções do Azure. Esta fila é utilizada para acionar sem monitorização de estado *funções de atividade*. Quando uma aplicação de funções durável aumenta horizontalmente de forma a várias VMs, estas VMs todos os competem ter a aquisição de trabalho da fila de item de trabalho.

### <a name="control-queues"></a>Filas de controlo

O *fila controlo* é mais sofisticadas que a fila de item de trabalho mais simples. É utilizado para acionar as funções de monitorização de estado do orchestrator. Porque as instâncias de função do orchestrator são singletons com monitorização de estado, não é possível utilizar um modelo de consumidor concorrente distribuam a carga entre VMs. Em vez disso, as mensagens do orchestrator são com balanceamento de carga em várias filas de controlo. Obter mais detalhes sobre este nas secções subsequentes.

Filas de controlo contêm uma variedade de tipos de mensagens de ciclo de vida de orquestração. Os exemplos incluem [mensagens de controlo do orchestrator](durable-functions-instance-management.md), a função de atividade *resposta* mensagens e mensagens do temporizador.

## <a name="orchestrator-scale-out"></a>Escalamento horizontal do Orchestrator

Funções de atividade são sem monitorização de estado e aumentar horizontalmente automaticamente através da adição de VMs. As funções do Orchestrator, por outro lado, são *particionada* por um ou mais filas de controlo. O número de filas do controlo é fixo e não pode ser alterado depois de iniciar a criação da carga.

Durante a ampliação várias instâncias de anfitrião de função (normalmente, num VMs diferentes), cada instância adquire um bloqueio das filas de controlo. Este bloqueio garante que uma instância de orquestração só é executada numa única VM cada vez. Isto significa que se um concentrador de tarefas estiver configurado com três filas de controlo, instâncias de orquestração podem ser com balanceamento de carga entre VMs máximo de três. VMs adicionais podem ser adicionadas para aumentar a capacidade de execução da função de atividade.  Mas os recursos adicionais, não serão utilizados para executar as funções do orchestrator.

O diagrama seguinte ilustra como o anfitrião de funções do Azure interage com as entidades de armazenamento num ambiente expandido terminar.

![Diagrama de escala](media/durable-functions-perf-and-scale/scale-diagram.png)

Como pode ver, todas as VMs podem competem ter mensagens na fila de item de trabalho. No entanto, apenas três VMs podem adquirir mensagens de filas de controlo e cada VM bloqueia uma fila de controlo único.

Instâncias de orquestração estão distribuídas instâncias de fila de controlo através da execução de uma função de hash interno no ID de instância. de orquestração Os IDs de instância são geradas automaticamente e aleatório por predefinição, que garante que as instâncias são equilibradas entre todas as filas de controlo disponíveis. O número predefinido atual de partições de fila de controlo suportado é **4**.

> [!NOTE]
> Não é atualmente possível configurar o número de partições das funções do Azure. [Trabalho para suportar esta opção de configuração está a ser controlado](https://github.com/Azure/azure-functions-durable-extension/issues/73).

Em geral, as funções do orchestrator são se destina a ser simples e não deverá necessitar de muito de energia informático. Por este motivo, não é necessário criar um grande número de partições de fila de controlo para obter o débito elevado. Em vez disso, a maioria do trabalho pesada é feita nas funções de atividade sem monitorização de estado, o que podem ser ampliadas infinitamente.

## <a name="auto-scale"></a>Dimensionamento Automático

Como todas as funções do Azure em execução no plano de consumo, dimensionamento automático através de suporte de funções durável o [controlador de dimensionamento das funções do Azure](https://docs.microsoft.com/azure/azure-functions/functions-scale#runtime-scaling). O controlador de escala monitoriza o comprimento da fila de item de trabalho e cada uma das filas de controlo, adicionar ou remover recursos VM em conformidade. Se os comprimentos fila do controlo estão aumentar ao longo do tempo, o controlador de escala irá continuar a adicionar instâncias até atingir o número de partição de fila de controlo. Se comprimentos de fila de item de trabalho estão aumentar ao longo do tempo, o controlador de escala irá continuar a adição de recursos VM até que pode corresponder a carga, independentemente do número de partição de fila de controlo.

## <a name="thread-usage"></a>Utilização de thread

As funções do Orchestrator são executadas num único thread. Isto é necessário para garantir que a execução de função do orchestrator é determinística. Com isto em mente, é importante nunca mantenha o orchestrator funcionar thread desnecessariamente ocupado com tarefas, tais como a e/s (que é proibido por diversas razões), bloquear ou girar operações. Qualquer conta que pode exigir a e/s, bloquear, ou devem ser movidos de vários threads para funções de atividade.

As funções de atividade ter as mesmas comportamentos do regulares funções acionadas de fila. Isto significa que em segurança que podem fazer e/s, executar operações intensivas de CPU e utilizar vários threads. Dado que os acionadores de atividade sem monitorização de estado, estes podem livremente aumentar horizontalmente para um número de VMs unbounded.

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Instale a extensão de funções durável e amostras](durable-functions-install.md)
