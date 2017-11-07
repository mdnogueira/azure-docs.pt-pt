---
title: Monitorizar contentores no Azure Service Fabric com o OMS Log Analytics | Microsoft Docs
description: "Utilize a análise de registos do OMS para monitorização contentores executar em clusters de Service Fabric do Azure."
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: timlt
editor: 
ms.assetid: 
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/1/2017
ms.author: dekapur
ms.openlocfilehash: 17121041520160d0d76832bbdbe74ad6a649fdd8
ms.sourcegitcommit: 295ec94e3332d3e0a8704c1b848913672f7467c8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/06/2017
---
# <a name="monitor-containers-with-oms-log-analytics"></a>Monitor de contentores com a análise de registos do OMS
 
Este artigo aborda os passos necessários para configurar a monitorização do contentor para o cluster. Para obter mais informações sobre isto, consulte [monitorização contentores no Service Fabric](service-fabric-diagnostics-event-analysis-oms.md#monitoring-containers). Para ver um tutorial passo a passo sobre isto, também pode seguir [contentores de Windows de Monitor no Service Fabric com o OMS](service-fabric-tutorial-monitoring-wincontainers.md).

## <a name="set-up-the-container-monitoring-solution"></a>Configurar o solução de monitorização do contentor

> [!NOTE]
> Necessário à análise de registos do OMS estar configurada para o cluster, bem como têm o agente do OMS implementados nos nós. Se não, siga os passos no [análise de registos do OMS configuração](service-fabric-diagnostics-oms-setup.md) e [adicionar o agente do OMS a um cluster](service-fabric-diagnostics-oms-agent.md) primeiro.

1. Depois do cluster está configurado com a análise de registos do OMS e o agente do OMS, implemente os contentores. Aguarde que os contentores ser implementada antes de mover para o passo seguinte.

2. No Azure Marketplace, procure *solução de monitorização do contentor* e clique em de **solução de monitorização do contentor** recurso que aparece na monitorização + gestão categoria.

    ![Adicionar a solução de contentores](./media/service-fabric-diagnostics-event-analysis-oms/containers-solution.png)

3. Crie a solução no interior da área de trabalho mesma que já tiver sido criada para o cluster. Esta alteração aciona automaticamente o agente para iniciar a recolha de dados de docker nos contentores. Em cerca de 15 minutos ou por isso, deverá ver a solução leve cópias de segurança com registos de entrada e de estatísticas.

## <a name="next-steps"></a>Passos seguintes
* Saiba mais sobre a orquestração do contentor no Service Fabric - [Service Fabric e os contentores](service-fabric-containers-overview.md)
* Obter familiarized com o [de registo de pesquisa e consultar](../log-analytics/log-analytics-log-searches.md) funcionalidades disponibilizadas como parte da análise de registos
* Configurar OMS configurar [automatizada alertas](../log-analytics/log-analytics-alerts.md) regras para ajudar a detetar e diagnóstico