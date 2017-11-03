---
title: Arquitetura do Gestor de recursos | Microsoft Docs
description: "Uma descrição geral da arquitetura do Service Fabric Cluster Gestor de recursos."
services: service-fabric
documentationcenter: .net
author: masnider
manager: timlt
editor: 
ms.assetid: 6c4421f9-834b-450c-939f-1cb4ff456b9b
ms.service: Service-Fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: f0d2202c17bf4d378a625a61e941edf7f3f24636
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="cluster-resource-manager-architecture-overview"></a>Descrição geral da arquitetura de Gestor de recursos de cluster
O Gestor de recursos de Cluster do serviço de recursos de infraestrutura é um serviço central que é executado no cluster. Gere o estado pretendido de serviços do cluster, particularmente relativamente a quaisquer regras de posicionamento e consumo de recursos. 

Para gerir os recursos do cluster, o Gestor de recursos de Cluster do Service Fabric tem de ter vários tipos de informações:

- Serviços de que existem atualmente
- Cada serviço actual do (ou predefinido) consumo de recursos 
- A capacidade de cluster restante 
- A capacidade de nós no cluster 
- A quantidade de recursos que são consumidos em cada nó

Pode alterar o consumo de recursos de um determinado serviço ao longo do tempo e serviços, normalmente, se preocupar com mais do que um tipo de recurso. Nos vários serviços diferentes, podem existir ambos real recursos físicos e físicos a ser medidos. Serviços podem monitorizar as métricas físicas, como o consumo de memória e o disco. Mais frequentemente, os serviços podem se preocupar com métricas lógicas - coisas como "WorkQueueDepth" ou "TotalRequests". Métricas de lógicas e físicas podem ser utilizadas no mesmo cluster. Métricas podem ser partilhadas em vários serviços ou estar específicas para um serviço específico.

## <a name="other-considerations"></a>Outras considerações
Os operadores do cluster e os proprietários podem ser diferentes dos autores de serviços e de aplicações ou, no mínimo são os mesmo hats diferentes wearing pessoas. Quando desenvolver a sua aplicação sabe alguns aspetos sobre o que necessita. Tiver uma estimativa de recursos irá consumir e a forma como os diferentes serviços devem ser implementados. Por exemplo, a camada web tem de ser executado em nós exposto à Internet, enquanto os serviços de base de dados não devem. Outro exemplo, os serviços web provavelmente estão restritos pelo CPU e da rede, enquanto o cuidado de serviços de camada de dados mais informações sobre o consumo de memória e o disco. No entanto, a pessoa que o processamento de um incidente de site em direto para que o serviço na produção ou que está a gerir uma atualização para o serviço tem uma tarefa diferente para o fazer e necessita de ferramentas diferentes. 

O cluster e os serviços são dinâmicos:

- O número de nós do cluster pode aumentar e diminuir
- Nós de diferentes tamanhos e tipos podem ficar e aceda
- Os serviços podem ser criados, remover e alterar as respetivas alocações de recursos desejados e regras de colocação
- Outras operações de gestão de atualizações ou podem implementar através do cluster, a aplicação nos níveis de infraestrutura
- Falhas podem acontecer em qualquer altura.

## <a name="cluster-resource-manager-components-and-data-flow"></a>Componentes de Gestor de recursos do cluster e o fluxo de dados
O Gestor de recursos do Cluster tem de controlar os requisitos de cada serviço e o consumo de recursos por cada objeto de serviço dentro desses serviços. O Gestor de recursos do Cluster tem duas partes concetuais: agentes que são executadas em cada nó e um serviço com tolerância a falhas. Os agentes em cada nó de carga de controlar relatórios a partir de serviços, agregação-los e, periodicamente comunicá-los. O serviço do Gestor de recursos de Cluster agrega todas as informações de agentes locais e reage com base na configuração atual.

Vamos ver o diagrama a seguir:

<center>
![Arquitetura de Balanceador de recursos][Image1]
</center>

Durante o tempo de execução, existem várias alterações que pode ter ocorrido. Por exemplo, vamos diga a quantidade de recursos que consumam de alguns serviços for alterada, falham alguns serviços e algumas associação de nós e deixe o cluster. Todas as alterações num nó são agregadas e enviadas periodicamente para o serviço de Gestor de recursos de Cluster (1,2) onde são agregados novamente, analisados e armazenados. Cada alguns segundos, que analisa as alterações de serviço e determina se são necessárias quaisquer ações (3). Por exemplo, foi tenha em atenção que alguns nós vazios foram adicionados ao cluster. Como resultado, decide de mover alguns serviços para os nós. O Gestor de recursos de Cluster também foi tenha em atenção que um determinado nó estiver sobrecarregado ou que determinados serviços tem falhou ou foi eliminados, libertando recursos noutro local.

Vamos observar o diagrama a seguir e ver o que acontece em seguida. Imaginemos que o Gestor de recursos de Cluster determina que são necessárias alterações. Coordena com outros serviços do sistema (em especial o Gestor de ativação pós-falha) para efetuar as alterações necessárias. Em seguida, os comandos necessários são enviados para nós adequado (4). Por exemplo, vamos supor que o Gestor de recursos reparado Nó5 foi sobrecarregado e, por isso, decidiu mover o serviço B da Nó5 para Nó4. No final da reconfiguração (5), o cluster tem este aspeto:

<center>
![Arquitetura de Balanceador de recursos][Image2]
</center>

## <a name="next-steps"></a>Passos seguintes
- O Gestor de recursos do Cluster tem muitas opções para descrever o cluster. Para saber mais sobre os mesmos, consulte este artigo no [que descrevem um cluster do Service Fabric](./service-fabric-cluster-resource-manager-cluster-description.md)
- Deveres principal do Gestor de recursos de Cluster são reequilíbrio o cluster e impor regras de posicionamento. Para obter mais informações sobre como configurar estas comportamentos, consulte [balanceamento de cluster do Service Fabric](./service-fabric-cluster-resource-manager-balancing.md)

[Image1]:./media/service-fabric-cluster-resource-manager-architecture/Service-Fabric-Resource-Manager-Architecture-Activity-1.png
[Image2]:./media/service-fabric-cluster-resource-manager-architecture/Service-Fabric-Resource-Manager-Architecture-Activity-2.png
