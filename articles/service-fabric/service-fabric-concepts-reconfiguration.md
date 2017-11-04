---
title: "Reconfiguração nos recursos de infraestrutura de serviço do Azure | Microsoft Docs"
description: "Compreender a reconfiguração de partições no Service Fabric"
services: service-fabric
documentationcenter: .net
author: appi101
manager: anuragg
editor: 
ms.assetid: d5ab75ff-98b9-4573-a2e5-7f5ab288157a
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/23/2017
ms.author: aprameyr
ms.openlocfilehash: ee2808286012720210070817d7ee6c2349247858
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="reconfiguration-in-azure-service-fabric"></a>Reconfiguração nos recursos de infraestrutura de serviço do Azure
A *configuração* está definida como as réplicas e as respetivas funções para uma partição de um serviço com monitorização de estado.

A *reconfiguração* é o processo de mover uma configuração para outra configuração. Faz uma alteração à réplica definido para uma partição de um serviço com monitorização de estado. A configuração antiga é denominada o *configuração anterior (PC)*, e denomina-se a nova configuração a *configuração atual (CC)*. O protocolo de reconfiguração no Azure Service Fabric preserva a consistência e mantém a disponibilidade durante as alterações ao conjunto de réplicas.

Gestor de ativação pós-falha inicia Reconfigurações em resposta a eventos diferentes no sistema. Por exemplo, se o principal falhar, em seguida, uma reconfiguração é iniciada para promover um secundário Active Directory para um site primário. Outro exemplo é em resposta a atualizações de aplicações quando poderá ser necessário mover o principal para outro nó para atualizar o nó.

## <a name="reconfiguration-types"></a>Tipos de reconfiguração
Reconfigurações podem ser classificadas em dois tipos:

- Reconfigurações onde está a alterar o principal:
    - **Ativação pós-falha**: as ativações pós-falha são Reconfigurações em resposta a falha de uma execução primária.
    - **SwapPrimary**: trocas são Reconfigurações onde necessita de Service Fabric para mover uma execução principal de um nó para outro, normalmente em resposta ao balanceamento de carga ou uma atualização.

- Reconfigurações onde o principal não está a mudar.

## <a name="reconfiguration-phases"></a>Fases de reconfiguração
Uma reconfiguração continua em várias fases:

- **Phase0**: nesta fase ocorre Reconfigurações de principais de comutação onde o principal atual transfere o estado para o novo site primário e transições para secundário Active Directory.

- **Phase1**: nesta fase acontece durante Reconfigurações onde está a alterar o site primário. Durante esta fase, o Service Fabric identifica primário correto entre as réplicas atuais. Nesta fase não é necessária durante a troca primário Reconfigurações porque a nova principal já foi escolhido. 

- **Phase2**: durante esta fase, o Service Fabric garante que todos os dados estão disponíveis em a maioria das réplicas da configuração atual.

Existem várias fases são apenas para utilização interna.

## <a name="stuck-reconfigurations"></a>Reconfigurações bloqueadas
Podem obter Reconfigurações *bloqueada* por diversas razões. Algumas das razões comuns incluem:

- **Pendente réplicas**: alguns fases de reconfiguração requerem a maioria das réplicas na configuração ser cópias de segurança.
- **Problemas de rede ou outras comunicações**: Reconfigurações necessitam de conectividade de rede entre os nós de diferentes.
- **Falhas de API**: O protocolo de reconfiguração requer que as implementações de serviço concluir algumas APIs. Por exemplo, não para respeitar o token de cancelamento num serviço fiável faz com que SwapPrimary Reconfigurações ficar bloqueado.

Utilizar relatórios de estado de funcionamento dos componentes de sistema, tais como System.FM, System.RA e System.RAP diagnosticar onde uma reconfiguração está bloqueada. O [página do relatório de estado de funcionamento de sistema](service-fabric-understand-and-troubleshoot-with-system-health-reports.md) descreve estes relatórios de estado de funcionamento.

## <a name="next-steps"></a>Passos seguintes
Para obter mais informações sobre os conceitos de Service Fabric, consulte os artigos seguintes:

- [Ciclo de vida do Reliable Services - C#](service-fabric-reliable-services-lifecycle.md)
- [Relatórios de estado de funcionamento do sistema](service-fabric-understand-and-troubleshoot-with-system-health-reports.md)
- [Réplicas e instâncias](service-fabric-concepts-replica-lifecycle.md)
