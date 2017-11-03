---
title: Planear a capacidade de cluster do Service Fabric | Microsoft Docs
description: "Capacidade de cluster do Service Fabric considerações de planeamento. Nodetypes, operações, características de durabilidade e fiabilidade camadas"
services: service-fabric
documentationcenter: .net
author: ChackDan
manager: timlt
editor: 
ms.assetid: 4c584f4a-cb1f-400c-b61f-1f797f11c982
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/12/2017
ms.author: chackdan
ms.openlocfilehash: f7c4a00d2c9be2d6b4d3d0b4dfb152deb2d0e217
ms.sourcegitcommit: e5355615d11d69fc8d3101ca97067b3ebb3a45ef
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/31/2017
---
# <a name="service-fabric-cluster-capacity-planning-considerations"></a>Considerações de planeamento de capacidade do cluster de Service Fabric
Para qualquer implementação de produção, o planeamento de capacidade é um passo importante. Seguem-se alguns dos itens que terá de considerar como parte do processo.

* O número de tipos de nó que do cluster tem de começar a utilizar
* As propriedades de cada tipo de nó (tamanho, o servidor primário, a internet com número de VMs, etc.)
* As características de durabilidade e fiabilidade do cluster

Informe-por breves instantes Reveja cada um destes itens.

## <a name="the-number-of-node-types-your-cluster-needs-to-start-out-with"></a>O número de tipos de nó que do cluster tem de começar a utilizar
Em primeiro lugar, tem de descobrir que o cluster que estiver a criar vai ser utilizado para e os tipos de aplicações estiver a planear implementar para este cluster. Se não estiver encriptado no objetivo do cluster, não são mais provável ainda está preparado para introduzir a processo de planeamento da capacidade.

Estabelece o número de tipos de nó que do cluster tem de começar a.  Cada tipo de nó está mapeado para um conjunto de dimensionamento de Máquina Virtual. Cada tipo de nó, em seguida, pode ser escalado para cima ou para baixo de forma independente, têm conjuntos diferentes de portas abertas e pode ter as métricas de capacidade diferentes. Por isso, a decisão do número de tipos de nó essencialmente vem para baixo para as seguintes considerações:

* A sua aplicação tem vários serviços e qualquer um deles tem de ser public ou para a internet? Aplicações típicas contém um serviço de front-end do gateway que recebe a entrada de um cliente e um ou mais back-end serviços que comunicam com os serviços front-end. Por isso, neste caso, pode ficar ter, pelo menos, dois tipos de nó.
* Os serviços (que compõem a sua aplicação) dispõem de necessidades de infraestrutura diferentes, como maior RAM ou superiores ciclos da CPU? Por exemplo, vamos assuma que contém a aplicação que pretende implementar um serviço front-end e um serviço de back-end. Pode executar o serviço de front-end em VMs mais pequenas (tamanhos de VM, como D2) que tem portas abertas à internet.  O serviço de back-end, no entanto, é intensiva de cálculo e tem de executar em VMs maior (com tamanhos de VM, como D4, D6, D15) que não são internet enfrentam.
  
  Neste exemplo, embora pode optar por colocar em pausa todos os serviços de um tipo de nó, recomendamos que coloque-los num cluster com dois tipos de nó.  Isto permite que cada tipo de nó tem propriedades diferentes, como a conectividade à internet ou o tamanho da VM. Separadamente, também pode ser escalado para o número de VMs.  
* Uma vez que não é possível prever o futuro, aceda com factos que saber de e opte por utilizar o número de tipos de nó que as suas aplicações precisam para começar. Pode sempre adicionar ou remover tipos de nó mais tarde. Um cluster do Service Fabric tem de ter, pelo menos, um tipo de nó.

## <a name="the-properties-of-each-node-type"></a>As propriedades de cada tipo de nó
O **tipo de nó** podem ser vistos como equivalentes às funções nos serviços em nuvem. Tipos de nó definem os tamanhos VM, o número de VMs e as respetivas propriedades. Cada tipo de nó que está definido num cluster de Service Fabric está configurado como um conjunto de dimensionamento de máquina virtual separada. Conjunto de dimensionamento da máquina virtual é um recurso de computação do Azure, que pode utilizar para implementar e gerir uma coleção de máquinas virtuais como um conjunto. A ser definido como conjunto de dimensionamento de máquinas distintas, cada tipo de nó, em seguida, pode ser escalado para cima ou para baixo de forma independente, têm conjuntos diferentes de portas abertas e pode ter as métricas de capacidade diferentes.

Leitura [neste documento](service-fabric-cluster-nodetypes.md) para obter mais detalhes sobre a relação de Nodetypes ao conjunto de dimensionamento da máquina virtual, como para o RDP para uma das instâncias, abrir novas portas etc.

O cluster pode ter mais do que um tipo de nó, mas o tipo de nó principal (aquele primeiro por si no portal) tem de ter, pelo menos, cinco VMs para clusters utilizadas para cargas de trabalho de produção (ou, pelo menos, três VMs para clusters de teste). Se estiver a criar o cluster utilizando um modelo do Resource Manager, em seguida, procure **é principal** atributo sob a definição de tipo de nó. O tipo de nó principal é o tipo de nó em que os serviços de sistema do Service Fabric são colocados.  

### <a name="primary-node-type"></a>Tipo de nó principal
Para um cluster com vários tipos de nó, tem de escolher um para ser principal. Seguem-se as características de um tipo de nó principal:

* O **tamanho mínimo de VMs** para o nó principal tipo é determinado pelo **o escalão de durabilidade** que escolher. A predefinição para o escalão de durabilidade é Bronze. Desloque para baixo para obter detalhes sobre o que é o escalão de durabilidade e os valores pode demorar.  
* O **número mínimo de VMs** para o nó principal tipo é determinado pelo **escalão de fiabilidade** que escolher. A predefinição para o escalão de fiabilidade é Silver. Desloque para baixo para obter detalhes sobre o que é o escalão de fiabilidade e os valores pode demorar. 


* Os serviços de sistema do Service Fabric (por exemplo, o serviço do Gestor de clusters ou o serviço de arquivo de imagens) são colocados no tipo de nó principal e valor para a fiabilidade e características de durabilidade do cluster é determinado a camada de valor e durabilidade da camada de fiabilidade Selecione esta opção para o tipo de nó principal.

![Captura de ecrã de um cluster que tem dois tipos de nó ][SystemServices]

### <a name="non-primary-node-type"></a>Tipo de nó principal não
Para um cluster com vários tipos de nó, existe um tipo de nó principal e o resto dos mesmos não principal. Seguem-se as características de um tipo de nó não primário:

* O tamanho mínimo de VMs para este tipo de nó é determinado pelo escalão de durabilidade que escolher. A predefinição para o escalão de durabilidade é Bronze. Desloque para baixo para obter detalhes sobre o que é o escalão de durabilidade e os valores pode demorar.  
* O número mínimo de VMs para este tipo de nó pode ser um. No entanto, deve escolher este número com base no número de réplicas de aplicações/serviços que pretende executar neste tipo de nó. O número de VMs num tipo de nó pode ser aumentado depois de implementar o cluster.

## <a name="the-durability-characteristics-of-the-cluster"></a>As características de durabilidade do cluster
O escalão de durabilidade é utilizado para indicar ao sistema os privilégios que tenham as VMs com a infraestrutura do Azure subjacente. O tipo de nó principal, este privilégio permite que o Service Fabric colocar em pausa qualquer pedido de infraestrutura de nível de VM (por exemplo, um reinício VM, recriação de imagem de VM ou migração de VM) que afetam os requisitos de quórum para os serviços do sistema e os serviços com monitorização de estado. Os tipos de nó não principal, este privilégio permite-Service Fabric colocar em pausa quaisquer pedidos de infraestrutura de nível de VM, como o reinício da VM, recriação de imagem de VM, etc., de migração de VM que afetam os requisitos de quórum para os serviços com monitorização de estado em execução no mesmo.

Este privilégio é expresso nos seguintes valores:

* Ouro - as tarefas de infraestrutura pode ser colocado em pausa durante um período de duas horas por UD. Características de durabilidade Gold só podem ser ativadas skus VM de nó completo como D15_V2, G5 etc.
* Prata - as tarefas de infraestrutura pode ser colocado em pausa durante um período de dez minutos por UD e está disponíveis em todas as VMs padrão do único núcleo e acima.
* Bronze – sem privilégios. Esta é a predefinição. Só utilize este nível de durabilidade para os tipos de nó que execute _apenas_ cargas de trabalho sem monitorização de estado. 

> [!WARNING]
> NodeTypes em execução com Bronze durability obter _sem privilégios_. Isto significa que as tarefas de infraestrutura que afetam as cargas de trabalho sem monitorização de estado não irão ser paradas ou atrasadas. É possível que essas tarefas ainda podem afetar as cargas de trabalho, provocando um período de indisponibilidade ou outros problemas. Para qualquer tipo de carga de trabalho de produção, em execução com, pelo menos, Silver é recomendada. Tem de manter um número mínimo de 5 nós para qualquer tipo de nó que tenha uma durabilidade de ouro ou Silver. 
> 

Obter para escolher o nível de durabilidade para cada um dos seus tipos de nó. Pode escolher tipo de nó de um para ter um nível de durabilidade de ouro ou prata e outros têm Bronze no mesmo cluster. **Tem de manter um número mínimo de 5 nós para qualquer tipo de nó que tenha uma durabilidade de ouro ou silver**. 

**Vantagens da utilização de níveis de durabilidade Silver ou Gold**
 
1. Reduz o número de passos necessários numa operação de escala no (ou seja, a desativação de nós e Remove-ServiceFabricNodeState é chamado automaticamente)
2. Reduz o risco de perda de dados devido a operações de infraestrutura do Azure ou uma operação alteração iniciada pelo cliente de SKU de VM de no local.
     
**Desvantagens da utilização de níveis de durabilidade Silver ou Gold**
 
1. As implementações para o conjunto de dimensionamento de Máquina Virtual e outros recursos relacionados do Azure) podem sofrer um atraso, podem tempo ou podem ser impedidas inteiramente por problemas no seu cluster ou ao nível da infraestrutura. 
2. Aumenta o número de [eventos de ciclo de vida de réplica](service-fabric-reliable-services-advanced-usage.md#stateful-service-replica-lifecycle ) (por exemplo, primárias trocas) devido a automatizada deactivations de nó durante as operações de infraestrutura do Azure.

### <a name="recommendations-on-when-to-use-silver-or-gold-durability-levels"></a>Recomendações sobre quando utilizar os níveis de durabilidade Silver ou Gold

Utilizar Silver ou Gold durabilidade para todos os tipos de nó serviços com monitorização de estado de espera na escala de anfitrião (Reduza a contagem de instâncias VM) com frequência, e preferir que operações de implementação é atrasada favor simplificando estas operações de escala. Os cenários de escalamento horizontal (adicionar instâncias de VMs) desempenha à sua escolha do escalão de durabilidade, apenas escala-in.

### <a name="changing-durability-levels"></a>Alterar os níveis de durabilidade
- Não podem ser alterada uma versão anterior tipos de nó com níveis de durabilidade de Silver ou Gold em para Bronze.
- Atualizar a partir do Bronze Silver ou Gold pode demorar algumas horas.
- Quando alterar o nível de durabilidade, lembre-se de que a atualização em de configuração dos recursos de infraestrutura do serviço de extensão no seu recurso VMSS e na definição do tipo de nó no seu recurso de cluster do Service Fabric. Estes valores têm de corresponder.

### <a name="operational-recommendations-for-the-node-type-that-you-have-set-to-silver-or-gold-durability-level"></a>Recomendações operacionais para o nó de tipo que definiu para durabilidade prata ou gold nível.

1. Manter o cluster e aplicações em bom estado de todas as vezes e certifique-se de que as aplicações respondem a todos os [Service eventos de ciclo de vida de réplica](service-fabric-reliable-services-advanced-usage.md#stateful-service-replica-lifecycle) (como a réplica de compilação está encravada) atempadamente.
2. Adotar formas mais segura para efetuar uma alteração de VM SKU (dimensionamento cima/para baixo): a alterar o SKU de VM de um conjunto de dimensionamento de Máquina Virtual é, inerentemente, uma operação não segura e, por isso, devem ser evitado se possível. Eis o processo que pode seguir para evitar problemas comuns.
    - **Para nodetypes não principal:** é recomendado que crie o novo conjunto de dimensionamento de Máquina Virtual, modifique a restrição de posicionamento de serviço a incluir o novo tipo de conjunto de dimensionamento da Máquina Virtual/nó e, em seguida, reduzir a instância de conjunto de dimensionamento da Máquina Virtual antiga Contagem como 0, um nó numa altura (trata-se para se certificar de que a remoção de nós não afeta a fiabilidade do cluster).
    - **Para o principal nodetype:** a nossa recomendação é que não altere o SKU de VM do tipo de nó principal. A alteração do tipo de nó principal que SKU não é suportada. Se o motivo para o SKU nova capacidade, recomendamos adicionar mais instâncias. Se esse não é possível, crie um novo cluster e [restaurar o estado da aplicação](service-fabric-reliable-services-backup-restore.md) (se aplicável) do cluster antigo. Não é necessário restaurar o estado do serviço qualquer sistema, são recriadas quando implementa as aplicações para o novo cluster. Se foram apenas aplicações em execução sem monitorização de estado no seu cluster, em seguida, tudo o que fazer é implementar aplicações para o novo cluster, não tem nada para restaurar. Se optar por voltar a rota não suportada e pretender alterar o SKU de VM, em seguida, efetue as modificações à definição de modelo de conjunto de dimensionamento de Máquina Virtual para refletir o SKU de novo. Se o cluster tem apenas um nodetype, em seguida, certifique-se de que todas as suas aplicações com monitorização de estado respondem a todos os [Service eventos de ciclo de vida de réplica](service-fabric-reliable-services-advanced-usage.md#stateful-service-replica-lifecycle) (como a réplica de compilação está encravada) em tempo útil e se a réplica de serviço reconstruir duração é inferior a cinco minutos (nível de durabilidade prata). 


> [!WARNING]
> Alterar o tamanho do SKU de VM para conjuntos de dimensionamento de VM não está em execução, pelo menos, prata durabilidade não é recomendados. Alterar tamanho da VM SKU é uma operação de infraestrutura do destrutivas de dados no local. Sem, pelo menos, alguma capacidade de atraso ou monitorizar esta alteração, é possível que a operação pode fazer com que dataloss para serviços com monitorização de estado ou fazer com que outros problemas operacionais unforseen, mesmo para cargas de trabalho sem monitorização de estado. 
> 
    
3. Mantenha uma contagem mínima de cinco nós para qualquer conjunto do dimensionamento de Máquina Virtual tem um nível de durabilidade de ouro ou Silver ativado
4. Não eliminar aleatórias instâncias VM, utilize sempre a escala de conjunto de dimensionamento da Máquina Virtual para baixo de funcionalidade. A eliminação de instâncias de VM aleatórias tem um potencial de criação de imbalances na instância VM distribuídos por UD e df. Este desequilíbrio negativamente pode afetar a capacidade de sistemas corretamente para equilibrar a carga entre as réplicas de serviço/instâncias de serviço.
6. Se utilizar o dimensionamento automático, defina as regras que escala em (remoção de instâncias VM) são efetuadas apenas um nó de cada vez. Não é seguro dimensionamento pendente mais de uma instância de cada vez.
7. Se o dimensionamento para baixo de um tipo de nó principal, nunca deve dimensioná-lo para baixo mais do que permite que o escalão de fiabilidade.


## <a name="the-reliability-characteristics-of-the-cluster"></a>As características de fiabilidade do cluster
O escalão de fiabilidade é utilizado para definir o número de réplicas os sistema de serviços de que pretende executar neste cluster no tipo de nó principal. Mais o número de réplicas, mais fiável os serviços de sistema estão no seu cluster.  

O escalão de fiabilidade pode efetuar os seguintes valores:

* Platinum - executar os serviços do sistema com um destino de réplica definir contagem 9
* Ouro - executar os serviços do sistema com um destino de réplica definir a contagem de 7
* Prata - executar os serviços do sistema com um destino de réplica definir a contagem de 5 
* Bronze – executar os serviços do sistema com um destino de réplica definir a contagem de 3

> [!NOTE]
> O escalão de fiabilidade, escolha determina o número mínimo de nós que tem de ter o tipo de nó principal. 
> 
> 


### <a name="recommendations-for-the-reliability-tier"></a>Recomendações para o escalão de fiabilidade.

 Ao aumentar ou diminuir o tamanho do seu cluster (a soma de instâncias VM em todos os tipos de nó), tem de atualizar a fiabilidade do cluster de um escalão para outro. Fazê-lo aciona as atualizações de cluster necessário alterar a réplica de serviços de sistema definido contagem. Aguarde a atualização em curso para concluir antes de efetuar outras alterações para o cluster, como adicionar nós.  Pode monitorizar o progresso da atualização no Service Fabric Explorer ou executando [Get ServiceFabricClusterUpgrade](/powershell/module/servicefabric/get-servicefabricclusterupgrade?view=azureservicefabricps)

Eis a recomendação sobre como escolher o escalão de fiabilidade.

| **Tamanho do cluster** | **Escalão de fiabilidade** |
| --- | --- |
| 1 |Especifique o parâmetro de escalão de fiabilidade, o sistema calcula-la |
| 3 |Bronze |
| 5 ou 6|Silver |
| 7 ou 8 |Gold |
| 9 e até |Platinum |




## <a name="primary-node-type---capacity-guidance"></a>Tipo de nó primário - orientações de capacidade

Esta é a orientação para planear a capacidade de tipo de nó principal

1. **Número de instâncias VM para executar qualquer carga de trabalho de produção no Azure:** tem de especificar um tamanho de tipo de nó principal mínimo de 5. 
2. **Número de instâncias VM para executar cargas de trabalho de teste no Azure** pode especificar um tamanho de tipo de nó principal mínimo de 1 ou 3. O cluster de um nó, é executado com uma configuração especial e por isso, escala fora desse cluster não é suportada. O cluster de um nó não possui nenhuma fiabilidade e, por isso, no seu modelo do Resource Manager, é necessário remover/não especificar que a configuração (não definir o valor de configuração não é suficiente). Se configurar o cluster de um nó configurar através do portal, em seguida, a configuração está automaticamente tratada. não são suportados clusters de nó de 1 a 3 para executar cargas de trabalho de produção. 
3. **SKU de VM:** tipo de nó principal é onde executar os serviços do sistema, para o SKU de VM que escolher, deve tomar em consideração o horário de pico geral carregar pretende colocar em cluster. Eis uma analogia ilustrar o que devo dizer aqui-considerar o tipo de nó principal como as "Lungs", é o que fornece oxygen para o "-brain" e, por isso, se o "-brain" não consegue suficiente oxygen, o corpo sofrerá. 

Uma vez que as necessidades de capacidade de um cluster é determinado pela carga de trabalho que pretende executar no cluster, não é possível fornecemos-lhe qualitativa orientações para a carga de trabalho específica, no entanto esta é a orientação abrangente para o ajudar a começar a utilizar

Para cargas de trabalho de produção 


- O SKU de VM recomendada é D3 padrão ou D3_V2 padrão ou equivalente com um mínimo de 14 GB de local SSD.
- A utilização de suportado mínimo SKU de VM é D1 padrão ou D1_V2 padrão ou equivalente com um mínimo de 14 GB de local SSD. 
- Core parcial SKUs de VM, como A0 padrão não são suportadas para cargas de trabalho de produção.
- Standard A1 SKU não é suportado para cargas de trabalho de produção por motivos de desempenho.

> [!WARNING]
> Atualmente, a alteração do tamanho de VM SKU num cluster em execução de nó principal não é suportada. Por isso, escolha o tipo de nó principal VM SKU cuidadosamente, tendo em conta as necessidades futuras de capacidade. Neste momento, a única forma de suportados para mover o tipo de nó principal para um novo SKU de VM (inferior ou superior) está a criar um novo cluster com a capacidade adequada, implementar as suas aplicações e, em seguida, restaurar o estado da aplicação (se aplicável) a partir de [ mais recente do serviço cópias de segurança](service-fabric-reliable-services-backup-restore.md) efetuou do cluster antigo. Não é necessário restaurar o estado do serviço qualquer sistema, são recriados ao implementar aplicações para o novo cluster. Se foram apenas aplicações em execução sem monitorização de estado no seu cluster, em seguida, tudo o que fazer é implementar aplicações para o novo cluster, não tem nada para restaurar.
> 

## <a name="non-primary-node-type---capacity-guidance-for-stateful-workloads"></a>Tipo de nó de não-Primary - orientações de capacidade para cargas de trabalho com monitorização de estado

Esta orientação é para cargas de trabalho com monitorização de estado utilizando o Service fabric [coleções fiáveis ou reliable Actors](service-fabric-choose-framework.md) que estejam a executar o tipo de nó não principal.


**Número de instâncias VM:** para produção as cargas de trabalho com monitorização de estado, é recomendado executar com uma contagem de réplica e o destino da mínimo de 5. Isto significa que no estado de repouso acaba por ficar com uma réplica (a partir de um conjunto de réplicas) em cada domínio de falhas e o domínio de atualização. O conceito de escalão de fiabilidade todo para o tipo de nó principal é uma forma de especificar esta definição para serviços do sistema. Por isso, a mesma consideração aplica-se os serviços de monitorização de estado.

Por isso, para cargas de trabalho de produção, o tamanho de tipo mínimo recomendado não - nó principal é 5, se estiver a executar cargas de trabalho com monitorização de estado no mesmo.


**SKU de VM:** este é o tipo de nó em que os serviços de aplicação estiver a executar, pelo que o SKU de VM que escolher, deve ter em consideração o pico de carga que pretende colocar em cada nó. As necessidades de capacidade da nodetype, é determinado pela carga de trabalho que pretende executar no cluster, pelo que não é possível fornecemos-lhe qualitativa orientações para a carga de trabalho específica, no entanto esta é a orientação abrangente para o ajudar a começar a utilizar

Para cargas de trabalho de produção 

- O SKU de VM recomendada é D3 padrão ou D3_V2 padrão ou equivalente com um mínimo de 14 GB de local SSD.
- A utilização de suportado mínimo SKU de VM é D1 padrão ou D1_V2 padrão ou equivalente com um mínimo de 14 GB de local SSD. 
- Core parcial SKUs de VM, como A0 padrão não são suportadas para cargas de trabalho de produção.
- SKU standard de A1 especificamente não é suportado para cargas de trabalho de produção por motivos de desempenho.


## <a name="non-primary-node-type---capacity-guidance-for-stateless-workloads"></a>Tipo de nó de não-Primary - orientações de capacidade para cargas de trabalho sem monitorização de estado

Esta orientação das cargas de trabalho sem monitorização de estado que estejam a executar o nodetype não principal.

**Número de instâncias VM:** para produção as cargas de trabalho sem monitorização de estado, o tamanho de tipo de mínimo suportado não - nó principal é 2. Isto permite-lhe executar, duas instâncias sem monitorização de estado da sua aplicação e permitindo que o serviço sobreviver a perda de uma instância VM. 

> [!NOTE]
> Se o cluster estiver a executar uma serviço fabric versão inferior ao 5.6, devido a um defeito em tempo de execução (este problema ser corrigido no 5.6), dimensionamento para baixo de um tipo de nó não primário a inferior a 5, resulta no estado de funcionamento de cluster ativação mau estado de funcionamento, até chamar [ Remover ServiceFabricNodeState cmd](https://docs.microsoft.com/powershell/servicefabric/vlatest/Remove-ServiceFabricNodeState) com o nome de nó adequado. Leitura [efetuar o cluster do Service Fabric entrada ou saída](service-fabric-cluster-scale-up-down.md) para obter mais detalhes
> 
>

**SKU de VM:** este é o tipo de nó em que os serviços de aplicação estiver a executar, pelo que o SKU de VM que escolher, deve ter em consideração o pico de carga que pretende colocar em cada nó. As necessidades de capacidade da nodetype, é determinado pela carga de trabalho que pretende executar no cluster, pelo que não é possível fornecemos-lhe qualitativa orientações para a carga de trabalho específica, no entanto esta é a orientação abrangente para o ajudar a começar a utilizar

Para cargas de trabalho de produção 


- O SKU de VM recomendada é D3 padrão ou D3_V2 padrão ou equivalente. 
- A utilização de suportado mínimo SKU de VM é D1 padrão ou D1_V2 padrão ou equivalente. 
- Core parcial SKUs de VM, como A0 padrão não são suportadas para cargas de trabalho de produção.
- Standard A1 SKU não é suportado para cargas de trabalho de produção por motivos de desempenho.

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->

## <a name="next-steps"></a>Passos seguintes
Depois de concluir o seu planeamento de capacidade e configurar um cluster, leia o seguinte:

* [Segurança de cluster do Service Fabric](service-fabric-cluster-security.md)
* [Planear a recuperação após desastre](service-fabric-disaster-recovery.md)
* [Definir a relação de Nodetypes dimensionamento de Máquina Virtual](service-fabric-cluster-nodetypes.md)

<!--Image references-->
[SystemServices]: ./media/service-fabric-cluster-capacity/SystemServices.png
