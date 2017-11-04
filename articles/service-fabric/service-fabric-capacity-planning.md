---
title: "Planeamento da capacidade para aplicações de Service Fabric | Microsoft Docs"
description: "Descreve como identificar o número de nós de computação necessária para uma aplicação de Service Fabric"
services: service-fabric
documentationcenter: .net
author: mani-ramaswamy
manager: markfuss
editor: 
ms.assetid: 9fa47be0-50a2-4a51-84a5-20992af94bea
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 8/9/2017
ms.author: subramar
ms.openlocfilehash: fc98bdd8b3597810b0c07563af507e93c611f769
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="capacity-planning-for-service-fabric-applications"></a>Planeamento da capacidade para aplicações de Service Fabric
Este documento informa como estimar a quantidade de recursos (CPU, RAM, armazenamento de disco), que tem de executar as aplicações do Azure Service Fabric. É comum para os seus requisitos de recursos alterar o ao longo do tempo. Normalmente, é necessário alguns recursos à medida que o serviço de desenvolve/teste e, em seguida, requerem mais recursos, vá para a produção e a sua aplicação cresce no popularidade. Quando criar uma aplicação, analisar os requisitos de longa duração e efetue as opções que permitem que o serviço de dimensionamento para satisfazer o pedido de cliente elevado.

 Quando cria um cluster do Service Fabric, decida que tipos de máquinas virtuais (VMs) constituem o cluster. Cada VM vem com uma quantidade limitada de recursos sob a forma de CPU (núcleos e velocidade), a largura de banda, RAM e armazenamento em disco. À medida que cresce o serviço ao longo do tempo, pode atualizar para VMs que oferecem maiores recursos e/ou adicionar mais VMs para o cluster. Para fazer a última opção, tem architect seu serviço inicialmente, de modo que pode tirar partido das novas VMs dinamicamente obter adicionado ao cluster.

Alguns serviços gerem pouca como não existem dados nas VMs próprios. Por conseguinte, capacidade de planeamento para estes serviços deve focar-se principalmente no desempenho, o que significa selecionar as CPUs adequadas (núcleos e velocidade) das VMs. Além disso, deve considerar a largura de banda de rede, incluindo a frequência das transferências de rede estão a ocorrer e está a ser transferida a quantidade de dados. Se o serviço precisa de realizar bem à medida que aumenta de utilização do serviço, pode adicionar mais VMs para o equilíbrio de cluster e a carga de pedidos de rede em todas as VMs.

Para os serviços que gerem grandes quantidades de dados nas VMs, planeamento de capacidade deve focar-se principalmente nas tamanho. Assim, deve considerar atentamente a capacidade da VM RAM e armazenamento em disco. O sistema de gestão de memória virtual no Windows faz com que o espaço em disco aspeto RAM ao código da aplicação. Além disso, o tempo de execução do Service Fabric fornece a paginação inteligente manter apenas os dados na memória e mover os dados amovíveis para disco. As aplicações, por conseguinte, podem utilizar mais memória do que é fisicamente disponível na VM. Basta ter mais RAM aumenta o desempenho, uma vez que a VM pode manter mais armazenamento em disco na RAM. A VM que selecionar deve ter um disco grande o suficiente para armazenar os dados que pretende na VM. Da mesma forma, a VM deve ter suficientes para lhe fornecer o desempenho que pretendidos ao nível de RAM. Se o crescimentos de dados do serviço ao longo do tempo, pode adicionar mais VMs para o cluster e os dados de partição em todas as VMs.

## <a name="determine-how-many-nodes-you-need"></a>Determinar quantos nós é necessário
O serviço de criação de partições permite-lhe aumentar horizontalmente dados do seu serviço. Para obter mais informações sobre a criação de partições, consulte [criação de partições de Service Fabric](service-fabric-concepts-partitioning.md). Cada partição deve encaixar-se dentro de uma única VM, mas várias partições (pequenas) podem ser colocadas numa única VM. Por isso, ter mais partições pequenas dá-lhe uma maior flexibilidade ao ter alguns partições maior. O compromisso é o facto ter muitos partições aumenta a sobrecarga de Service Fabric não é possível efetuar operações transacionadas em partições. É também mais tráfego de rede potenciais se código do serviço com frequência tem de aceder a tipos de dados que residem no partições diferentes. Ao conceber o seu serviço, deve considerar cuidadosamente estas profissionais de TI e contras para chegam a uma estratégia de criação de partições efetiva.

Vamos assumir que a aplicação tem um único serviço com monitorização de estado que tem um tamanho de armazenamento que pretende aumentar a DB_Size GB num ano. Que está disposto a adicionar mais aplicações (e as partições) como a experiência de crescimento para além dos que ano.  O fator de replicação (RF), que determina o número de réplicas para o serviço tem impacto sobre o DB_Size total. O DB_Size em todas as réplicas total é o fator de replicação multiplicado pelo DB_Size.  Node_Size representa o espaço de disco/RAM por nó que pretende utilizar para o seu serviço. Para melhor desempenho, a DB_Size deve caber na memória no cluster e deve ser selecionado um Node_Size que está em torno da RAM da VM. Atribuindo um Node_Size que é maior do que a capacidade de RAM, são depender paginação fornecida pelo runtime Service Fabric. Assim, o desempenho poderá não ser ideal se os dados completos são considerados frequente (desde então os dados é bloco paginados entrada/saída). No entanto, para vários serviços onde é frequente apenas uma fração de dados, é mais económico.

O número de nós é necessário para o máximo desempenho pode ser calculado da seguinte forma:

```
Number of Nodes = (DB_Size * RF)/Node_Size

```


## <a name="account-for-growth"></a>Conta para o crescimento
Pode querer calcule o número de nós com base nos DB_Size que espera que o seu serviço para aumentar, além de DB_Size que começou com. Em seguida, aumente o número de nós à medida que cresce o serviço para que lhe é não sobre-aprovisionar o número de nós. Mas o número de partições deve basear-se no número de nós que são necessários quando estiver a executar o serviço em crescimento máximo.

É boa ideia ter algumas máquinas adicionais disponíveis em qualquer altura, para que pode processar qualquer picos inesperados ou a falha (por exemplo, se algumas VMs ficarem Inativos).  Enquanto a capacidade extra deve ser determinada ao utilizar os picos de esperado, é de um ponto de partida para reservar alguns VMs Extras (5-10 por cento adicional).

Precedente assume um único serviço com monitorização de estado. Se tiver mais do que um serviço com estado, terá de adicionar DB_Size associados os outros serviços para a equação. Em alternativa, pode calcular o número de nós em separado para cada serviço com monitorização de estado.  O serviço poderá ter as réplicas ou as partições que não são balanced. Tenha em atenção que as partições podem também ter mais de dados que outras pessoas. Para obter mais informações sobre a criação de partições, consulte [artigo sobre as melhores práticas de criação de partições](service-fabric-concepts-partitioning.md). No entanto, a equação anterior é partição e réplica agnóstico relativamente, porque o Service Fabric assegura que as réplicas são distribuídas entre os nós de forma otimizada.

## <a name="use-a-spreadsheet-for-cost-calculation"></a>Utilize uma folha de cálculo de cálculo do custo
Agora vamos colocar alguns números real na fórmula de. Um [folha de cálculo de exemplo](https://servicefabricsdkstorage.blob.core.windows.net/publicrelease/SF%20VM%20Cost%20calculator-NEW.xlsx) mostra como planear a capacidade de uma aplicação que contém três tipos de objetos de dados. Para cada objeto, iremos aproximada tamanho e objetos quantos que Esperamos ter. Podemos também selecionar réplicas quantos queremos de cada tipo de objeto. A folha de cálculo calcula a quantidade total de memória para ser armazenada no cluster.

Em seguida, iremos introduzir um tamanho da VM e o custo mensal. Com base no tamanho VM, a folha de cálculo indica o número mínimo de partições que tem de utilizar para dividir os dados para caber fisicamente os nós. Pode pretendidos ao nível um grande número de partições para acomodar cálculo específicas da sua aplicação e necessidades de tráfego de rede. A folha de cálculo mostra o número de partições que estiver a gerir os objetos de perfil de utilizador aumentou a partir de um para seis.

Agora, com base em todas estas informações, a folha de cálculo mostra que fisicamente foi possível obter todos os dados com o pretendido partições e réplicas num cluster de nó de 26. No entanto, este cluster seria possível densely packed, portanto, alguns nós adicionais para acomodar as falhas de nó e atualizações. A folha de cálculo também mostra que ter mais do que 57 nós fornece nenhum valor adicional porque terá de nós vazios. Novamente, poderá ir acima 57 nós mesmo assim para acomodar as falhas de nó e atualizações. Pode otimizar a folha de cálculo para que corresponda ao necessidades específicas da sua aplicação.   

![Folha de cálculo de cálculo do custo][Image1]

## <a name="next-steps"></a>Passos seguintes
Veja [serviços de criação de partições de Service Fabric] [ 10] para saber mais sobre o serviço de criação de partições.

<!--Image references-->
[Image1]: ./media/SF-Cost.png

<!--Link references--In actual articles, you only need a single period before the slash-->
[10]: service-fabric-concepts-partitioning.md
