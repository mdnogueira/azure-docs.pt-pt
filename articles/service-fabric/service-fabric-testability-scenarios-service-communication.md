---
title: "Teste: Comunicação de serviço | Microsoft Docs"
description: "Comunicação de serviço a serviço é um ponto de integração críticos de uma aplicação de Service Fabric. Este artigo aborda considerações de design e técnicas de teste."
services: service-fabric
documentationcenter: .net
author: vturecek
manager: timlt
editor: 
ms.assetid: 017557df-fb59-4e4a-a65d-2732f29255b8
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/02/2017
ms.author: vturecek
ms.openlocfilehash: 4c6b53673dd4c79ce435c1593e7d08c7dd4a1cb0
ms.sourcegitcommit: 3df3fcec9ac9e56a3f5282f6c65e5a9bc1b5ba22
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2017
---
# <a name="service-fabric-testability-scenarios-service-communication"></a>Cenários de teste de Service Fabric: comunicação de serviço
Micro-serviços e superfície de estilos de arquitetura orientada para serviços naturalmente no Service Fabric do Azure. Nestes tipos de arquiteturas distribuídas, aplicações de microsserviço componentized são normalmente compostas por vários serviços que têm de comunicar entre si. No mesmo cenários mais simples, geralmente, é ter, pelo menos, um serviço web sem monitorização de estado e um serviço de armazenamento de dados de monitorização de estado têm de comunicar.

Comunicação de serviço a serviço é um ponto de integração críticos de uma aplicação, porque cada serviço expõe uma API remota a outros serviços. Trabalhar com um conjunto de limites de API que envolve a e/s geralmente requer alguns cuidado, com uma quantidade boa de validação e de teste.

Existem várias considerações a tomar quando estes limites de serviço são com fios em conjunto num sistema distribuído:

* *Protocolo de transporte*. Irá utilizar HTTP para interoperabilidade de aumento ou um protocolo personalizado de binário para um débito máximo?
* *Processamento de erros*. Como serão tratados erros transitórios e permanentes? O que acontece quando move um serviço num nó diferente?
* *Tempos limite e latência*. Nas aplicações multitiered, como cada camada de serviço processará latência através da pilha de e para o utilizador?

Se utilizar um dos componentes de comunicação de serviço incorporado fornecidos pelo Service Fabric ou criar a sua própria, as interações entre os serviços de teste é fundamental para assegurar a resiliência na sua aplicação.

## <a name="prepare-for-services-to-move"></a>Preparar para mover os serviços
Instâncias de serviço podem mover-se ao longo do tempo. Isto é particularmente verdadeiro quando são configurados com a métrica de carga para balanceamento e adaptada personalizada recursos ideal. Service Fabric move as instâncias de serviço para maximizar a respetiva disponibilidade, mesmo durante atualizações, as ativações pós-falha, escalável e outras situações que ocorrem durante a duração de um sistema distribuída.

Como serviços mover-se no cluster, os clientes e outros serviços devem estar preparados para processar os dois cenários ao comunicar com um serviço:

* A réplica de partição ou instância de serviço foi movido desde a última vez que talked ao mesmo. Esta é uma parte de um ciclo de vida do serviço normal e deve ser esperada acontecer durante a duração da sua aplicação.
* A réplica de partição ou instância de serviço está em vias de mudar. Embora a ativação pós-falha de um serviço de um nó para outro muito rapidamente ocorre no Service Fabric, pode existir um atraso na disponibilidade se o componente de comunicação do seu serviço estiver lento iniciar.

Estes cenários de processamento normalmente é importante para um sistema de execução uniforme. Para tal, tenha em atenção que:

* Tem de cada serviço que pode ser ligado a um *endereço* que escuta em (por exemplo, HTTP ou WebSockets). Quando move de uma instância de serviço ou partição, altera o seu ponto final de endereço. (, É movido para um nó diferente com um endereço IP diferente.) Se estiver a utilizar os componentes de comunicação incorporada, estes irão processar novamente ao resolver endereços de serviço para si.
* Pode haver um aumento temporário na latência de serviço que a instância de serviço ser iniciada se o serviço de escuta novamente. Isto depende rapidez o serviço abre-se o serviço de escuta depois da instância de serviço é movida.
* Todas as ligações existentes têm de ser fechado e reaberto depois do serviço abre num novo nó. Um encerramento de nó correto ou reinício permite tempo de ligações existentes a ser encerrado corretamente.

### <a name="test-it-move-service-instances"></a>Testá-lo: mover instâncias do serviço
Ao utilizar ferramentas de teste de Service Fabric, pode criar um cenário de teste para testar estas situações de formas diferentes:

1. Mova a réplica primária com monitorização de estado de um serviço.
   
    A réplica primária de uma partição de serviço com estado pode ser movida por diversas razões. Utilize esta opção para a réplica primária de uma partição específica para ver como os serviços de reagir à mudança de uma forma muito controlada de destino.
   
    ```powershell
   
    PS > Move-ServiceFabricPrimaryReplica -PartitionId 6faa4ffa-521a-44e9-8351-dfca0f7e0466 -ServiceName fabric:/MyApplication/MyService
   
    ```
2. Pare um nó.
   
    Quando um nó estiver parado, o Service Fabric move todas as instâncias de serviço ou as partições que foram nesse nó a um dos outros nós disponíveis no cluster. Utilize esta opção para testar uma situação em que um nó for perdido do seu cluster e todas as instâncias do serviço e as réplicas nesse nó tem de mover.
   
    Pode parar um nó com o PowerShell **Stop-ServiceFabricNode** cmdlet:
   
    ```powershell
   
    PS > Restart-ServiceFabricNode -NodeName Node_1
   
    ```

## <a name="maintain-service-availability"></a>Manter a disponibilidade do serviço
Como uma plataforma, o Service Fabric foi concebida para fornecer elevada disponibilidade dos seus serviços. Mas, em casos extremos, problemas de infraestrutura subjacentes ainda podem causar indisponibilidade. É importante testar os cenários, demasiado.

Serviços com monitorização de estado utilizam um sistema baseado em quórum para replicar o estado de elevada disponibilidade. Isto significa que um quórum de réplicas tem de estar disponível para executar operações de escrita. Em casos raros, tais como uma falha de hardware ampla, um quórum de réplicas pode não estar disponível. Nestes casos, não será possível efetuar operações de escrita, mas ainda será capaz de executar as operações de leitura.

### <a name="test-it-write-operation-unavailability"></a>Testá-lo: indisponibilidade de operação de escrita
Ao utilizar as ferramentas de teste no Service Fabric, pode inserir uma falha que induces perda de quórum como um teste. Embora seja raro cenário, é importante que os clientes e serviços que dependem de um serviço com estado estão preparados para processar situações onde não é possível efetuam os pedidos de escrita ao mesmo. Também é importante que o próprio serviço de monitorização de estado é em consideração esta possibilidade e pode lidar graciosamente comunicá-la para os chamadores.

Pode induce perda de quórum, utilizando o PowerShell **Invoke-ServiceFabricPartitionQuorumLoss** cmdlet:

```powershell

PS > Invoke-ServiceFabricPartitionQuorumLoss -ServiceName fabric:/Myapplication/MyService -QuorumLossMode QuorumReplicas -QuorumLossDurationInSeconds 20

```

Neste exemplo, vamos definir `QuorumLossMode` para `QuorumReplicas` para indicar que queremos induce perda de quórum sem colocar baixo todas as réplicas. Desta forma, as operações de leitura são ainda possíveis. Para testar um cenário em que uma partição completa está disponível, pode definir este comutador `AllReplicas`.

## <a name="next-steps"></a>Passos seguintes
[Saiba mais sobre as ações de teste](service-fabric-testability-actions.md)

[Saiba mais sobre os cenários de teste](service-fabric-testability-scenarios.md)

