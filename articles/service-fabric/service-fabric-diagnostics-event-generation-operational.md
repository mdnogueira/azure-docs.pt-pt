---
title: "Canal de operacional de recursos de infraestrutura de serviço do Azure | Microsoft Docs"
description: Lista completa dos registos gerados nos clusters de operacional canal do Service Fabric do Azure.
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
ms.date: 11/02/2017
ms.author: dekapur
ms.openlocfilehash: 712679b8fae9059df602881f28a1b74f7244fca3
ms.sourcegitcommit: 295ec94e3332d3e0a8704c1b848913672f7467c8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/06/2017
---
# <a name="operational-channel"></a>Canal operacional 

O canal operacional consiste em registos de alto nível ações executadas pelo Service Fabric nos nós e o cluster. Quando "diagnóstico" está ativado para um cluster, o agente de diagnóstico do Azure está implementado no seu cluster, não sendo por predefinição, configuradas para ler nos registos do canal operacional. Leia mais sobre como configurar o [agente de diagnóstico do Azure](service-fabric-diagnostics-event-aggregation-wad.md) para modificar a configuração de diagnósticos do cluster para recolher mais registos ou os contadores de desempenho. 

## <a name="operational-channel-logs"></a>Registos de canal operacional 

Eis uma lista completa de registos fornecidos pelo Service Fabric no canal operacional. 

| EventId | Nome | Origem (tarefas) | Nível |
| --- | --- | --- | --- |
| 25620 | NodeOpening | FabricNode | Informativo |
| 25621 | NodeOpenedSuccess | FabricNode | Informativo |
| 25622 | NodeOpenedFailed | FabricNode | Informativo |
| 25623 | NodeClosing | FabricNode | Informativo |
| 25624 | NodeClosed | FabricNode | Informativo |
| 25625 | NodeAborting | FabricNode | Informativo |
| 25626 | NodeAborted | FabricNode | Informativo |
| 29627 | ClusterUpgradeStart | CM | Informativo |
| 29628 | ClusterUpgradeComplete | CM | Informativo |
| 29629 | ClusterUpgradeRollback | CM | Informativo |
| 29630 | ClusterUpgradeRollbackComplete | CM | Informativo |
| 29631 | ClusterUpgradeDomainComplete | CM | Informativo |
| 23074 | ContainerActivated | Alojamento | Informativo |
| 23075 | ContainerDeactivated | Alojamento | Informativo |
| 29620 | ApplicationCreated | CM | Informativo |
| 29621 | ApplicationUpgradeStart | CM | Informativo |
| 29622 | ApplicationUpgradeComplete | CM | Informativo |
| 29623 | ApplicationUpgradeRollback | CM | Informativo |
| 29624 | ApplicationUpgradeRollbackComplete | CM | Informativo |
| 29625 | ApplicationDeleted | CM | Informativo |
| 29626 | ApplicationUpgradeDomainComplete | CM | Informativo |
| 18566 | ServiceCreated | FM | Informativo |
| 18567 | ServiceDeleted | FM | Informativo |

## <a name="next-steps"></a>Passos seguintes

* Saiba mais sobre o global [geração de eventos ao nível da plataforma](service-fabric-diagnostics-event-generation-infra.md) no Service Fabric
* Modificar o [diagnósticos do Azure](service-fabric-diagnostics-event-aggregation-wad.md) configuração para recolher registos mais
* [Configurar o Application Insights](service-fabric-diagnostics-event-analysis-appinsights.md) para ver o seu operacional em registos de canal
