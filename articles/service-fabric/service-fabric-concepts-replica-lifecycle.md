---
title: "As réplicas e de instâncias no Azure Service Fabric | Microsoft Docs"
description: "Compreender as réplicas e instâncias – da respetiva função e ciclos de vida"
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
ms.openlocfilehash: b4a01752cf2658bcc8dea663462336ca5c610d70
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="replicas-and-instances"></a>As réplicas e de instâncias 
Este artigo fornece uma descrição geral do ciclo de vida das réplicas de serviços com monitorização de estado e as instâncias de serviços sem monitorização de estado.

## <a name="instances-of-stateless-services"></a>Instâncias de serviços sem monitorização de estado
Uma instância de um serviço sem monitorização de estado é uma cópia da lógica de serviço que é executada num de nós do cluster. Uma instância dentro de uma partição é identificada de forma exclusiva pelo respetivo **InstanceId**. O ciclo de vida de uma instância é modelado no diagrama seguinte:

![Ciclo de vida de instância](./media/service-fabric-concepts-replica-lifecycle/instance.png)

### <a name="inbuild-ib"></a>InBuild (IB)
Depois do Gestor de recursos de Cluster determina um posicionamento para a instância, entra neste estado de ciclo de vida. A instância está iniciada no nó. O anfitrião de aplicação é iniciado, a instância é criada e, em seguida, aberta. Após o arranque, a instância passa para o estado preparado. 

Se o anfitrião de aplicação ou o nó para esta instância falhar, passa para o estado ignorado.

### <a name="ready-rd"></a>Pronto (RD)
No estado pronto, a instância está a funcionar no nó. Se esta instância é um serviço fiável, **runasync com** ter sido invocado. 

Se o anfitrião de aplicação ou o nó para esta instância falhar, passa para o estado ignorado.

### <a name="closing-cl"></a>Fecho (CL)
O estado de fecho do Azure Service Fabric está no processo de encerramento a instância neste nó. Este encerramento pode dever muitos motivos – por exemplo, uma atualização da aplicação, balanceamento de carga ou o serviço que está a ser eliminado. Após a conclusão do encerramento, passa para o estado ignorado.

### <a name="dropped-dd"></a>Removido (DD)
No estado de ignorados, a instância já não está em execução no nó. Neste momento, o Service Fabric mantém os metadados sobre desta instância, que, eventualmente, é também eliminada.

> [!NOTE]
> É possível efetuar a transição de qualquer Estado para o estado removido utilizando o **ForceRemove** opção `Remove-ServiceFabricReplica`.
>

## <a name="replicas-of-stateful-services"></a>Réplicas de serviços com monitorização de estado
Uma réplica de um serviço com monitorização de estado é uma cópia da lógica de serviço em execução num de nós do cluster. Além disso, a réplica mantém uma cópia do Estado do serviço. Dois conceitos relacionados descrevem o ciclo de vida e o comportamento das réplicas com monitorização de estado:
- Ciclo de vida de réplica
- Função de réplica

O debate da seguinte descreve os serviços com monitorização de estado persistentes. Para serviços voláteis (ou na memória) com monitorização de estado, os Estados de baixo e ignorados são equivalentes.

![Ciclo de vida de réplica](./media/service-fabric-concepts-replica-lifecycle/replica.png)

### <a name="inbuild-ib"></a>InBuild (IB)
Uma réplica InBuild é uma réplica que tenha criado ou preparado para associar o conjunto de réplicas. Consoante a função de réplica, o IB tem semânticas diferentes. 

Se o anfitrião de aplicação ou o nó para uma réplica InBuild falhar, passa para o estado de indisponibilidade.

   - **Primárias réplicas InBuild**: InBuild primário são as primeiro réplicas para uma partição. Esta réplica ocorre normalmente quando a partição está a ser criada. Primário InBuild réplicas também surgem quando todas as réplicas de uma partição reiniciar ou são ignorados.

   - **Réplicas IdleSecondary InBuild**: estes são ambos réplicas novas que são criadas pelo Gestor de recursos de Cluster ou réplicas existentes que ocorreu pendente e tem de ser adicionado novamente para o conjunto. Estas réplicas são pré-propagadas ou criadas pelo principal antes de serem podem associar o conjunto de réplicas como ActiveSecondary e participar no confirmação de quórum de operações.

   - **Réplicas ActiveSecondary InBuild**: este estado é observado em algumas consultas. É uma otimização onde definir a réplica não está a alterar, mas tem de ser criada uma réplica. A réplica próprio segue as transições de máquina de estado normal (conforme descrito na secção nas funções de réplica).

### <a name="ready-rd"></a>Pronto (RD)
Uma réplica pronta é uma réplica que está a participar na replicação e quórum confirmação de operações. O estado preparado é aplicável a primários e Active Directory réplicas secundárias.

Se o anfitrião de aplicação ou o nó para uma réplica pronto falhar, passa para o estado de indisponibilidade.

### <a name="closing-cl"></a>Fecho (CL)
Uma réplica ativa o estado de fecho nos seguintes cenários:

- **Encerrar o código para a réplica**: Service Fabric poderá ter de encerrar o executar um código para uma réplica. Este encerramento poderá ser por muitos motivos. Por exemplo, pode acontecer devido a uma aplicação, recursos de infraestrutura ou atualização da infraestrutura, ou devido a uma falha comunicado pela réplica. Quando a réplica fechar depois de concluída, a réplica passa para o estado de indisponibilidade. O estado persistente associado a esta réplica armazenados no disco não é limpa.

- **Remover a réplica do cluster**: Service Fabric poderá ter de remover o estado persistente e encerrar o código em execução para uma réplica. Este encerramento poderá ser por muitos motivos, por exemplo, balanceamento de carga.

### <a name="dropped-dd"></a>Removido (DD)
No estado de ignorados, a instância já não está em execução no nó. Também é sem estado deixado no nó. Neste momento, o Service Fabric mantém os metadados sobre desta instância, que, eventualmente, é também eliminada.

### <a name="down-d"></a>Baixo (D)
O estado de indisponibilidade, o código de réplica não está em execução, mas o estado persistente para essa réplica existe nesse nó. Uma réplica pode ser para baixo por muitos motivos – por exemplo, o nó a ser baixo, uma falha no código de réplica, uma atualização da aplicação ou falhas de réplica.

Uma réplica baixo está aberta por Service Fabric conforme necessário, por exemplo, quando a conclusão da atualização no nó.

A função de réplica não é relevante no estado de indisponibilidade.

### <a name="opening-op"></a>Abrir (OP)
Uma réplica baixo aciona o estado de abertura quando Service Fabric tem de colocar a réplica de cópia de segurança novamente. Por exemplo, este estado pode ser após a conclusão de uma atualização de código para a aplicação num nó. 

Se o anfitrião de aplicação ou o nó para uma réplica de abertura falhar, passa para o estado de indisponibilidade.

A função de réplica não é relevante no estado de abertura.

### <a name="standby-sb"></a>Modo de espera (SB)
Uma réplica de modo de espera é uma réplica de um serviço persistente que ocorreu para baixo e, em seguida, foi aberto. Esta réplica pode ser utilizada por recursos de infraestrutura de serviço se precisar de adicionar outra réplica para a réplica definir (porque a réplica já tem uma parte do Estado e o processo de compilação é mais rápido). Depois do StandByReplicaKeepDuration expira, a réplica de reserva dinâmica é rejeitada.

Se o anfitrião de aplicação ou o nó para uma réplica em modo de espera falhar, passa para o estado de indisponibilidade.

A função de réplica não é relevante no estado de espera.

> [!NOTE]
> Qualquer réplica que não estiver desativado ou removido é considerada *segurança*.
>

> [!NOTE]
> É possível efetuar a transição de qualquer Estado para o estado removido utilizando o **ForceRemove** opção `Remove-ServiceFabricReplica`.
>

## <a name="replica-role"></a>Função de réplica 
A função da réplica determina a respetiva função no conjunto de réplica:

- **Site primário (P)**: há um principal no conjunto de réplica que é responsável pela execução de ler e operações de escrita. 
- **ActiveSecondary (S)**: estes são réplicas que recebem atualizações de estado a partir de principal, aplicá-las e, em seguida, enviar confirmações anterior. Existem várias bases de dados secundárias Active Directory no conjunto de réplicas. O número destas bases de dados secundárias Active Directory determina o número de falhas que pode processar o serviço.
- **IdleSecondary (I)**: estas réplicas estão a ser criadas pelo principal. Que está a receber Estado partir de principal antes de pode ser promovidos para secundário Active Directory. 
- **Nenhum (N)**: estas réplicas não tem uma responsabilidade no conjunto de réplica.
- **Desconhecido (U)**: Esta é a função inicial de uma réplica antes de receber quaisquer **ChangeRole** na chamada à API do Service Fabric.

O diagrama seguinte ilustra as transições de função de réplica e alguns cenários de exemplo em que pode ocorrer:

![Função de réplica](./media/service-fabric-concepts-replica-lifecycle/role.png)

- U -> p: a criação de uma nova réplica primária.
- U -> i: criar uma nova réplica inativa.
- U -> N: eliminação de uma réplica em modo de espera.
- Posso -> s: promoção de secundário inativo para secundário Active Directory para que o respetivas confirmações contribuem na direção de quórum.
- Posso -> p: promoção de secundário inativo para principal. Isto pode acontecer em reconfigurações especiais quando inativo secundário é o correto candidato ser principal.
- Posso -> N: eliminação da réplica secundária inativa.
- S -> p: promoção de secundário Active Directory para principal. Isto pode dever-se a ativação pós-falha do primário ou um movimento principal iniciada pelo Gestor de recursos de Cluster. Por exemplo, poderá ser em resposta a uma atualização da aplicação ou o balanceamento de carga.
- S -> N: eliminação da réplica secundária Active Directory.
- P -> s: despromoção da réplica primária. Isto pode dever-se um movimento principal iniciado pelo Gestor de recursos de Cluster. Por exemplo, poderá ser em resposta a uma atualização da aplicação ou o balanceamento de carga.
- P -> N: eliminação da réplica primária.

> [!NOTE]
> Modelos de programação do nível mais elevada, tais como [Reliable Actors](service-fabric-reliable-actors-introduction.md) e [Reliable Services](service-fabric-reliable-services-introduction.md), ocultar o conceito de funções de réplica do programador. Atores, a noção de uma função é desnecessária. Nos serviços,-lo é simplificado amplamente para a maioria dos cenários.
>

## <a name="next-steps"></a>Passos seguintes
Para obter mais informações sobre os conceitos de Service Fabric, consulte o artigo seguinte:

[Ciclo de vida do Reliable Services - C#](service-fabric-reliable-services-lifecycle.md)

