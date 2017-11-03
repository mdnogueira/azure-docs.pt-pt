---
title: "Especificar as definições de métricas e o posicionamento no Azure micro-serviços | Microsoft Docs"
description: "Que descrevem um serviço do Service Fabric, especificando métricas, restrições de posicionamento e outras políticas de colocação."
services: service-fabric
documentationcenter: .net
author: masnider
manager: timlt
editor: 
ms.assetid: 16e135c1-a00a-4c6f-9302-6651a090571a
ms.service: Service-Fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: 084fdf2f9a65eed3e444851afb88390aacf33738
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="configuring-cluster-resource-manager-settings-for-service-fabric-services"></a>Configurar definições de Gestor de recursos do cluster para serviços do Service Fabric
O Gestor de recursos de Cluster do serviço de recursos de infraestrutura permite um controlo detalhado sobre as regras que regem cada indivíduo com o nome de serviço. Cada serviço com o nome pode especificar regras para a forma como deve ser alocada no cluster. Cada serviço com nome também pode definir o conjunto de métricas pretende para o relatório, incluindo a forma como estes são importantes para esse serviço. Configurar os serviços divide em três tarefas diferentes:

1. Configurar restrições de posicionamento
2. Configurar as métricas
3. Configurar políticas de colocação avançadas e outras regras (menos comum)

## <a name="placement-constraints"></a>Restrições de posicionamento
Restrições de posicionamento são utilizadas para controlar que nós no cluster de um serviço, na verdade, podem executar em. Normalmente, um determinado nome instância de serviço ou de todos os serviços de um determinado tipo restringida para executar um determinado tipo de nó. Restrições de posicionamento estão extensíveis. Pode definir qualquer conjunto de propriedades de cada tipo de nó e, em seguida, selecione para os mesmos com restrições de durante a criação de serviços. Também pode alterar as restrições de posicionamento de um serviço enquanto estiver em execução. Isto permite-lhe responder a alterações no cluster ou os requisitos do serviço. Também é possível atualizar as propriedades de um determinado nó dinamicamente no cluster. Podem encontrar mais informações sobre restrições de posicionamento e como configurá-las no [neste artigo](service-fabric-cluster-resource-manager-cluster-description.md#node-properties-and-placement-constraints)

## <a name="metrics"></a>Métricas
As métricas são o conjunto de recursos que necessita de um determinado serviço com nome. Configuração da métrica de um serviço inclui a quantidade de recurso de cada réplica de monitorização de estado ou sem monitorização de estado instância desse serviço consome por predefinição. As métricas incluem também uma ponderação que indica a importância balanceamento esse métrica é a esse serviço, caso fala é necessárias.

## <a name="advanced-placement-rules"></a>Regras de posicionamento avançadas
Existem outros tipos de regras de posicionamento que são úteis em menos cenários comuns. Alguns exemplos incluem:
- Restrições que ajudar com clusters distribuídos geograficamente
- Determinados arquiteturas de aplicações

Outras regras de posicionamento são configuradas através de correlações ou políticas.

## <a name="next-steps"></a>Passos seguintes
- As métricas são como o serviço de recursos de infraestrutura Cluster Resource Manager gere consumo e capacidade do cluster. Para saber mais sobre as métricas e como configurá-las, consulte [neste artigo](service-fabric-cluster-resource-manager-metrics.md)
- Afinidade é um modo, que pode configurar para os serviços. Não é comum, mas se for necessário, pode saber [aqui](service-fabric-cluster-resource-manager-advanced-placement-rules-affinity.md)
- Existem várias regras de posicionamento diferentes que podem ser configuradas no seu serviço para processar cenários adicionais. Pode descobrir sobre as políticas de colocação diferentes [aqui](service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies.md)
- Iniciar a partir do início e [obtenha uma introdução para o serviço de recursos de infraestrutura Cluster Gestor de recursos](service-fabric-cluster-resource-manager-introduction.md)
- Para obter informações sobre a forma como o Gestor de recursos de Cluster gere e equilibra a carga no cluster, consulte o artigo em [balanceamento de carga](service-fabric-cluster-resource-manager-balancing.md)
- O Gestor de recursos do Cluster tem muitas opções para descrever o cluster. Para saber mais sobre os mesmos, consulte este artigo no [que descrevem um cluster do Service Fabric](service-fabric-cluster-resource-manager-cluster-description.md)
