---
title: "Um cluster do Service Fabric entrada ou saída de escala | Microsoft Docs"
description: "Um cluster do Service Fabric entrada ou saída de escala para corresponder a pedido, definindo as regras de dimensionamento automático para cada conjunto de dimensionamento da Máquina Virtual/tipo nó. Adicionar ou remover nós para um cluster do Service Fabric"
services: service-fabric
documentationcenter: .net
author: ChackDan
manager: timlt
editor: 
ms.assetid: aeb76f63-7303-4753-9c64-46146340b83d
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/22/2017
ms.author: chackdan
ms.openlocfilehash: 249fb4903c7b2de3ce290850a7759a4793f10aa7
ms.sourcegitcommit: cf42a5fc01e19c46d24b3206c09ba3b01348966f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/29/2017
---
# <a name="scale-a-service-fabric-cluster-in-or-out-using-auto-scale-rules"></a>Um cluster do Service Fabric no ou utilizando regras de dimensionamento automático de escala
Conjuntos de dimensionamento de máquina virtual são um recurso de computação do Azure que pode utilizar para implementar e gerir uma coleção de máquinas virtuais como um conjunto. Cada tipo de nó que está definido num cluster de Service Fabric está configurado como um conjunto de dimensionamento de Máquina Virtual separado. Cada tipo de nó, em seguida, pode ser ampliado na ou saída de forma independente, têm conjuntos diferentes de portas abertas e pode ter as métricas de capacidade diferentes. Saiba mais sobre-na [nodetypes de Service Fabric](service-fabric-cluster-nodetypes.md) documento. Uma vez que os tipos de nó de Service Fabric no seu cluster são constituídos por conjuntos de dimensionamento de Máquina Virtual no back-end, terá de configurar regras de dimensionamento automático para cada conjunto de dimensionamento da Máquina Virtual/tipo nó.

> [!NOTE]
> A subscrição tem de ter suficiente núcleos para adicionar novas VMs que compõem este cluster. Não há nenhuma validação de modelo atualmente, para obter uma falha de tempo de implementação, se qualquer um dos limites de quota são acessos.
> 
> 

## <a name="choose-the-node-typevirtual-machine-scale-set-to-scale"></a>Escolha o nó tipo/Virtual conjunto de dimensionamento de dimensionamento de máquina
Atualmente, não são capazes de especificar as regras de dimensionamento automático para conjuntos de dimensionamento de Máquina Virtual através do portal, por isso, informe-nos utilizar o Azure PowerShell (com+ 1.0) para listar os tipos de nó e, em seguida, adicione regras de dimensionamento automático aos mesmos.

Para obter a lista de conjunto de dimensionamento de Máquina Virtual que compõem o cluster, execute os seguintes cmdlets:

```powershell
Get-AzureRmResource -ResourceGroupName <RGname> -ResourceType Microsoft.Compute/VirtualMachineScaleSets

Get-AzureRmVmss -ResourceGroupName <RGname> -VMScaleSetName <Virtual Machine scale set name>
```

## <a name="set-auto-scale-rules-for-the-node-typevirtual-machine-scale-set"></a>Definir regras de dimensionamento automático para o nó tipo/Virtual conjunto de dimensionamento da máquina
Se o cluster tem vários tipos de nó, em seguida, repita que esta ação para cada nó tipos/Virtual dimensionamento da máquina define que pretende dimensionar (entrada ou saída). Tenha em conta o número de nós que tem de ter antes de configurar o dimensionamento automático. O número mínimo de nós que tem de ter para o tipo de nó principal é conduzido pelo nível de fiabilidade que escolheu. Leia mais sobre [níveis de fiabilidade](service-fabric-cluster-capacity.md).

> [!NOTE]
> Dimensionamento para baixo do nó principal escreva para menos da disponibilizar número mínimo de cluster instável ou colocá-lo para baixo. Isto pode resultar em perda de dados para as suas aplicações e para os serviços do sistema.
> 
> 

Atualmente a funcionalidade de dimensionamento automático não é condicionada por cargas que as aplicações podem ser relatórios para o Service Fabric. Por isso, neste momento, o dimensionamento automático, obter puramente é conduzido pelos contadores de desempenho que são emitidos por cada máquina Virtual do conjunto de dimensionamento instâncias.  

Siga estas instruções [para configurar o dimensionamento automático para cada conjunto de dimensionamento da Máquina Virtual](../virtual-machine-scale-sets/virtual-machine-scale-sets-autoscale-overview.md).

> [!NOTE]
> Uma escala baixo cenário, a menos que o tipo de nó tem um nível de durabilidade de ouro ou Silver terá de chamar o [cmdlet Remove-ServiceFabricNodeState](https://docs.microsoft.com/powershell/module/servicefabric/remove-servicefabricnodestate) com o nome de nó adequado.
> 
> 

## <a name="manually-add-vms-to-a-node-typevirtual-machine-scale-set"></a>Adicionar manualmente as VMs para uma nó tipo/Virtual conjunto de dimensionamento da máquina
Siga as instruções/exemplo no [Galeria de modelo de início rápido](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-scale-existing) para alterar o número de VMs em cada Nodetype. 

> [!NOTE]
> A adição de VMs leva tempo, pelo que não espera adições ser instantânea. Por isso, planeie adicionar capacidade bem em tempo, para permitir mais de 10 minutos antes da capacidade VM está disponível para as réplicas / instâncias são colocadas de serviço.
> 
> 

## <a name="manually-remove-vms-from-the-primary-node-typevirtual-machine-scale-set"></a>Remova manualmente as VMs do nó principal tipo/Virtual conjunto de dimensionamento da máquina
> [!NOTE]
> Executam os serviços de sistema de recursos de infraestrutura de serviço no tipo de nó principal do cluster. Por isso nunca deve encerrar ou reduzir verticalmente o número de instâncias em que tipos de nó inferior que o escalão de fiabilidade warrants. Consulte [os detalhes sobre camadas de fiabilidade aqui](service-fabric-cluster-capacity.md). 
> 
> 

Terá de executar a seguinte passos uma instância de VM a uma hora. Este procedimento permite que os serviços do sistema (e os serviços com monitorização de estado) ser encerrado corretamente na instância VM que está a remover e réplicas novo criadas nos outros nós.

1. Executar [desativar ServiceFabricNode](https://docs.microsoft.com/powershell/module/servicefabric/disable-servicefabricnode?view=azureservicefabricps) com o objetivo 'RemoveNode' para desativar o nó que vai remover (a instância desse tipo de nó mais alta).
2. Executar [Get-ServiceFabricNode](https://docs.microsoft.com/powershell/module/servicefabric/get-servicefabricnode?view=azureservicefabricps) para se certificar de que o nó realmente transitou para desativado. Caso contrário, aguarde até que o nó está desativado. Não é possível hurry este passo.
3. Siga as instruções/exemplo no [Galeria de modelo de início rápido](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-scale-existing) para alterar o número de VMs por um em que Nodetype. A instância removida é a instância VM mais elevada. 
4. Repita os passos 1 a 3 conforme necessário, mas nunca reduzir verticalmente o número de instâncias nos tipos de nó principal inferior ao que warrants o escalão de fiabilidade. Consulte [os detalhes sobre camadas de fiabilidade aqui](service-fabric-cluster-capacity.md). 

## <a name="manually-remove-vms-from-the-non-primary-node-typevirtual-machine-scale-set"></a>Remova manualmente as VMs do nó não primário tipo/Virtual conjunto de dimensionamento da máquina
> [!NOTE]
> Para um serviço com estado, precisa de um determinado número de nós para ser sempre até a manter a disponibilidade e manter o estado do seu serviço. Em muito mínima, terá do número de nós igual à contagem de conjunto de réplica de destino do serviço/partição. 
> 
> 

Tem a executar a seguinte passos uma instância de VM de cada vez. Este procedimento permite que os serviços do sistema (e os serviços com monitorização de estado) ser encerrado corretamente na instância de VM que está a remover e réplicas nova criada onde pessoa.

1. Executar [desativar ServiceFabricNode](https://docs.microsoft.com/powershell/module/servicefabric/disable-servicefabricnode?view=azureservicefabricps) com o objetivo 'RemoveNode' para desativar o nó que vai remover (a instância desse tipo de nó mais alta).
2. Executar [Get-ServiceFabricNode](https://docs.microsoft.com/powershell/module/servicefabric/get-servicefabricnode?view=azureservicefabricps) para se certificar de que o nó realmente transitou para desativado. Se não for Aguarde até o nó está desativado. Não é possível hurry este passo.
3. Siga as instruções/exemplo no [Galeria de modelo de início rápido](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-scale-existing) para alterar o número de VMs por um em que Nodetype. Agora, esta ação irá remover a instância VM mais elevada. 
4. Repita os passos 1 a 3 conforme necessário, mas nunca reduzir verticalmente o número de instâncias nos tipos de nó principal inferior ao que warrants o escalão de fiabilidade. Consulte [os detalhes sobre camadas de fiabilidade aqui](service-fabric-cluster-capacity.md).

## <a name="behaviors-you-may-observe-in-service-fabric-explorer"></a>Comportamentos que pode observar no Service Fabric Explorer
Quando dimensionar um cluster do Service Fabric Explorer irá refletir o número de nós (as instâncias do conjunto de dimensionamento de Máquina Virtual), que fazem parte do cluster.  No entanto, quando dimensiona um cluster inativo, verá a instância VM/nó removido apresentada em mau estado de funcionamento, a menos que tem de chamar [remover ServiceFabricNodeState cmd](https://msdn.microsoft.com/library/mt125993.aspx) com o nome de nó adequado.   

Eis a explicação para este comportamento.

Os nós listados no Service Fabric Explorer são um reflexão de que os serviços do sistema de Service Fabric (FM especificamente) conhece o número de nós de cluster tinha/tem. Quando dimensionar o conjunto para baixo de dimensionamento de Máquina Virtual, a mV foi eliminada, mas o serviço do system FM ainda pensa que o nó (que foi mapeado para a VM que foi eliminada) regressará. Por isso, o Service Fabric Explorer continua a apresentar esse nó (embora o estado de funcionamento pode ser erro ou desconhecido).

Para se certificar de que um nó é removido quando uma VM for removida, tem duas opções:

1) Escolha o nível de ouro ou Silver durabilidade para os tipos de nó do cluster, o que lhe dá a integração de infraestrutura. Que, em seguida, removerá automaticamente os nós do nosso estado do sistema de serviços (FM) quando reduzir verticalmente.
Consulte [os detalhes sobre níveis de durabilidade aqui](service-fabric-cluster-capacity.md)

2) Depois da instância VM tem sido ampliada para baixo, tem de chamar o [cmdlet Remove-ServiceFabricNodeState](https://msdn.microsoft.com/library/mt125993.aspx).

> [!NOTE]
> Clusters de Service Fabric necessitam de um determinado número de nós cópias de segurança ao tempo para manter a disponibilidade e preservar o estado - referido como "manutenção quórum". Por isso, é normalmente não seguro para encerrar todas as máquinas do cluster, exceto se tiver efetuado primeiro um [cópia de segurança completa do seu estado](service-fabric-reliable-services-backup-restore.md).
> 
> 

## <a name="next-steps"></a>Passos seguintes
Leia o seguinte para também saber mais sobre o planeamento da capacidade de cluster, atualização de um cluster e a criação de partições de serviços:

* [Planear a capacidade de cluster](service-fabric-cluster-capacity.md)
* [Atualizações de cluster](service-fabric-cluster-upgrade.md)
* [Serviços de monitorização de estado de partição para dimensionamento máximo](service-fabric-concepts-partitioning.md)

<!--Image references-->
[BrowseServiceFabricClusterResource]: ./media/service-fabric-cluster-scale-up-down/BrowseServiceFabricClusterResource.png
[ClusterResources]: ./media/service-fabric-cluster-scale-up-down/ClusterResources.png
