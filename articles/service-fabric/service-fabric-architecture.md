---
title: "Arquitetura dos recursos de infraestrutura de serviço do Azure | Microsoft Docs"
description: "Service Fabric é uma plataforma de sistemas distribuídos utilizada para criar aplicações dimensionáveis, fiáveis e facilmente geridos para a nuvem. Este artigo mostra a arquitetura do Service Fabric."
services: service-fabric
documentationcenter: .net
author: rishirsinha
manager: timlt
editor: rishirsinha
ms.assetid: 6b554243-70cb-4c22-9b28-1a8b4703f45e
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 10/12/2017
ms.author: rsinha
ms.openlocfilehash: 3d1f9d6136622e0e9fc1e725d8230dbedd6af24a
ms.sourcegitcommit: 804db51744e24dca10f06a89fe950ddad8b6a22d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/30/2017
---
# <a name="service-fabric-architecture"></a>Arquitetura do Service Fabric
Service Fabric baseia-se com subsistemas em camadas. Estes subsistemas permitem-lhe escrever as aplicações que são:

* Elevada disponibilidade
* Escalável
* Gerível
* Testable

O diagrama seguinte mostra os principais subsistemas de Service Fabric.

![Diagrama da arquitetura do Service Fabric](media/service-fabric-architecture/service-fabric-architecture.png)

Num sistema distribuído, a capacidade de comunicar de forma segura entre nós num cluster, é fundamental. A base da pilha de é o subsistema de transporte, que fornece uma comunicação segura entre nós. Acima do transporte subsistema se situa o subsistema de Federação, clusters de nós de diferentes para uma única entidade (com o nome clusters) para que possam detetar falhas, efetuar eleição leader e fornecem encaminhamento consistente Service Fabric. O subsistema de fiabilidade, em camadas sobre o subsistema de Federação é responsável pela fiabilidade dos serviços do Service Fabric através de mecanismos, tais como a replicação, a gestão de recursos e a ativação pós-falha. O subsistema de Federação subjacente também o subsistema de alojamento e a ativação, que gere o ciclo de vida de uma aplicação num único nó. O subsistema de gestão gere o ciclo de vida das aplicações e serviços. O subsistema de teste ajuda os programadores de aplicações testar os seus serviços através de falhas simuladas antes e depois de implementar aplicações e serviços para ambientes de produção. O Service Fabric fornece a capacidade para resolver localizações de serviço através do respetivo subsistema de comunicação. Os modelos de programação de aplicação expostos aos programadores são em camadas sobre estes subsistemas juntamente com o modelo de aplicação para ativar as ferramentas.

## <a name="transport-subsystem"></a>Subsistema de transporte
O subsistema de transporte implementa um canal de comunicação de datagrama de ponto a ponto. Este canal é utilizado para comunicação nos clusters de recursos de infraestrutura de serviço e a comunicação entre o cluster do service fabric e os clientes. Suporta unidirecional e padrões de comunicação de pedido-resposta, que fornece a base para implementar a difusão e multicast na camada de Federação. O subsistema de transporte protege comunicação utilizando X509 certificados ou de segurança do Windows. Este subsistema é utilizado internamente pelo serviço de recursos de infraestrutura e não está acessível diretamente aos programadores para a programação de aplicações.

## <a name="federation-subsystem"></a>Subsistema de Federação
Para poder pelo motivo sobre um conjunto de nós num sistema distribuído, tem de ter uma vista consistente do sistema. O subsistema de Federação utiliza as primitivas comunicação fornecidas pelo subsistema de transporte e stitches vários nós para um único cluster unificado pode pelo motivo sobre. Fornece as primitivas de sistemas distribuídos necessárias para outros subsistemas - a deteção de falha, eleição leader e encaminhamento consistente. O subsistema de Federação é desenvolvido com tabelas hash distribuída com um espaço de token de 128 bits. O subsistema cria uma topologia em anel nós, com cada nó no anel que está a ser atribuído um subconjunto do espaço de token de propriedade. Para a deteção de falha, a camada utiliza um mecanismo de locação com base em heart beating e arbitragem. O subsistema de Federação também garante que através da associação intricate e protocolos de partida se existe apenas um único proprietário de um token em qualquer altura. Isto proporciona eleição leader e consistentes garantias de encaminhamento.

## <a name="reliability-subsystem"></a>Subsistema de fiabilidade
O subsistema de fiabilidade fornece o mecanismo para que o estado de um serviço do Service Fabric altamente disponíveis através da utilização do *replicador*, *Failover Manager*, e *recursos Balanceador*.

* Replicador assegura que as alterações de estado na réplica do principal de serviço automaticamente serão replicadas em réplicas secundárias, manter a consistência entre as réplicas primários e secundários de um conjunto de réplicas do serviço. Replicador é responsável pela gestão de quórum entre as réplicas no conjunto de réplica. Interage com a unidade de ativação pós-falha ao obter a lista de operações para replicar e o agente de reconfiguração fornece-o com a configuração do conjunto de réplicas. Se a configuração indica que as réplicas das operações tem de ser replicadas. O Service Fabric fornece um replicador predefinido denominado replicador de recursos de infraestrutura, o que pode ser utilizado pela API de modelo de programação para tornar o estado do serviço de elevada disponibilidade e fiável.
* O Gestor de ativação pós-falha garante que quando nós são adicionados ou removidos do cluster, a carga é automaticamente redistribuída entre os nós disponíveis. Se um nó no cluster falhar, o cluster será automaticamente reconfigure as réplicas de serviço para manter a disponibilidade.
* O Gestor de recursos coloca réplicas serviço através de domínio de falha no cluster e assegura que todas as unidades de ativação pós-falha estão operacionais. O Gestor de recursos também equilibra a recursos do serviço em subjacente agrupamento partilhado de nós de cluster para alcançar a distribuição de carga uniform ideal.

## <a name="management-subsystem"></a>Subsistema de gestão
O subsistema de gestão fornece ponto-a-ponto serviço e gestão de ciclo de vida de aplicações. Cmdlets do PowerShell e APIs administrativas permitem-lhe aprovisionar, implementar, aplicar o patch, atualizar e anular o aprovisionamento aplicações sem perda de disponibilidade. O subsistema de gestão efetua este através dos seguintes serviços.

* **Gestor de clusters**: Este é o principal serviço que interage com o Failover Manager da fiabilidade para colocar as aplicações em nós com base nas restrições de posicionamento do serviço. O Gestor de recursos no subsistema de ativação pós-falha assegura que as restrições nunca sejam interrompidas. O Gestor de clusters gere o ciclo de vida das aplicações de aprovisionar para anular o aprovisionamento. É integrado com o Gestor de estado de funcionamento para garantir a disponibilidade de aplicações não perder-se de uma perspetiva de estado de funcionamento semântica durante as atualizações.
* **Gestor de estado de funcionamento**: este serviço permite a monitorização de estado de funcionamento de aplicações, serviços e entidades do cluster. Entidades de cluster (por exemplo, nós, partições de serviço e as réplicas) podem comunicar informações de estado de funcionamento, o que são agregadas, em seguida, para o arquivo de estado de funcionamento centralizada. Estas informações de estado de funcionamento fornecem um instantâneo do Estado de funcionamento geral ponto no tempo dos serviços e nós distribuídas por vários nós do cluster, permitindo-lhe efetuar quaisquer ações corretivas necessárias. Consultas de estado de funcionamento que APIs permitem-lhe consultar os eventos de estado de funcionamento é comunicado para o subsistema de estado de funcionamento. A consulta do Estado de funcionamento APIs devolvem os dados de estado de funcionamento não processados armazenados no arquivo de estado de funcionamento ou agregados, interpretar dados de estado de funcionamento para uma entidade de cluster específicos.
* **Arquivo de imagens**: este serviço fornece armazenamento e a distribuição dos binários de aplicação. Este serviço fornece um arquivo de ficheiros distribuído simples em que as aplicações são carregadas e transferidas a partir do.

## <a name="hosting-subsystem"></a>Subsistema de alojamento
O Gestor de clusters informa o subsistema alojamento (em execução em cada nó) que serviços tem de gerir para um determinado nó. O subsistema de alojamento, em seguida, gere o ciclo de vida da aplicação nesse nó. Interage com os componentes de fiabilidade e o estado de funcionamento para garantir que as réplicas são colocadas corretamente e que estão em bom estadas.

## <a name="communication-subsystem"></a>Subsistema de comunicação
Este subsistema fornece mensagens fiáveis dentro a deteção de cluster e o serviço através do serviço de nomes. O serviço de nomenclatura resolve os nomes de serviço para uma localização no cluster e permite aos utilizadores gerir nomes de serviço e propriedades. Utilizar o serviço de nomes, os clientes podem comunicar de forma segura com qualquer nó do cluster para resolver um nome de serviço e obter metadados do serviço. Os utilizadores do Service Fabric utilizando um cliente de nomenclatura API simple, podem desenvolver serviços e clientes com capacidade de resolver a localização de rede atual, não obstante dynamism do nó ou dimensionamento novamente do cluster.

## <a name="testability-subsystem"></a>Subsistema de teste
Teste é um conjunto de ferramentas que tenham sido concebidas especificamente para fins de teste de serviços incorporados no Service Fabric. As ferramentas de permitir que um programador facilmente induce falhas significativas e execute a cenários de teste para exercer e validar a vários Estados e transições que irá ocorrer um serviço em toda a respetiva duração, todos os de forma controlada e segura. Teste também fornece um mecanismo para executar testes de tempo que podem iterar através de vários possíveis falhas sem perda de disponibilidade. Isto fornece um ambiente de teste em produção.

