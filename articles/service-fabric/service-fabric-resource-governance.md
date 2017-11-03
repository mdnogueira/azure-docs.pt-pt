---
title: "Governação de recursos do Service Fabric do Azure para contentores e serviços | Microsoft Docs"
description: "Recursos de infraestrutura de serviço do Azure permite-lhe especificar limites de recursos para serviços em execução dentro ou fora contentores."
services: service-fabric
documentationcenter: .net
author: masnider
manager: timlt
editor: 
ms.assetid: ab49c4b9-74a8-4907-b75b-8d2ee84c6d90
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 8/9/2017
ms.author: subramar
ms.openlocfilehash: ada26a303013139f182721360aaf125ac5b94310
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="resource-governance"></a>Governação de recursos 

Quando estiver a executar vários serviços no mesmo nó ou cluster, é possível que um serviço pode consumir mais recursos, starving outros serviços no processo. Este problema é referido como o problema "vizinho inúteis". Azure Service Fabric permite que o programador especificar as reservas e limites por serviço para garantir a recursos e limitar a utilização de recursos.

> Antes de continuar com este artigo, recomendamos que obtém familiarizado com o [modelo de aplicação de Service Fabric](service-fabric-application-model.md) e [modelo de alojamento de Service Fabric](service-fabric-hosting-model.md).
>

## <a name="resource-governance-metrics"></a>Métricas de governação de recursos 

Governação de recursos é suportada no Service Fabric em conformidade com a [pacote de serviço](service-fabric-application-model.md). Os recursos que estão atribuídos ao pacote de serviço podem ainda ser divididos entre pacotes de código. Os limites de recursos que são especificados também significa que a reserva dos recursos. Service Fabric suporta a especificação de CPU e memória por pacote de serviço, com dois incorporado [métricas](service-fabric-cluster-resource-manager-metrics.md):

* *CPU* (o nome da métrica `servicefabric:/_CpuCores`): um núcleo lógico que está disponível no computador anfitrião. Todos os núcleos em todos os nós são ponderados da mesma.

* *Memória* (o nome da métrica `servicefabric:/_MemoryInMB`): memória é expresso em megabytes, e mapeia para a memória física que está disponível na máquina.

Para estas duas métricas, [Gestor de recursos do Cluster](service-fabric-cluster-resource-manager-cluster-description.md) controla a capacidade total do cluster, a carga em cada nó do cluster e os restantes recursos no cluster. Estas duas métricas são equivalentes a qualquer utilizador ou métrica personalizada. Todas as funcionalidades existentes podem ser utilizadas com os mesmos:
* O cluster pode ser [balanceamento](service-fabric-cluster-resource-manager-balancing.md) , de acordo com estas duas métricas (comportamento predefinido).
* O cluster pode ser [defragmented](service-fabric-cluster-resource-manager-defragmentation-metrics.md) , de acordo com estas duas métricas.
* Quando [que descrevem um cluster](service-fabric-cluster-resource-manager-cluster-description.md), colocados em memória intermédia capacidade pode ser definida para estas duas métricas.

[Relatórios de carga dinâmico](service-fabric-cluster-resource-manager-metrics.md) não é suportada para estas métricas e carrega para estas métricas são definidas no momento de criação.

## <a name="resource-governance-mechanism"></a>Mecanismo de governação de recursos

O tempo de execução do Service Fabric atualmente não fornecer uma reserva de recursos. Quando um processo ou um contentor é aberto, o tempo de execução define os limites de recursos para as cargas que foram definidas no momento de criação. Além disso, o tempo de execução rejeita a abertura de novos pacotes de serviço que estão disponíveis quando os recursos são foi excedidos. Para compreender melhor como funciona o processo, vamos um exemplo de um nó com dois núcleos de CPU (mecanismo de governação de memória é equivalente a maiúsculas e minúsculas):

1. Em primeiro lugar, um contentor é colocado no nó, pedir um núcleo de CPU. O tempo de execução é aberto o contentor e define o limite de CPU para um núcleo. O contentor não conseguirão utilizar mais do que um principal.

2. Em seguida, uma réplica de um serviço é colocada no nó e o pacote de serviço correspondentes Especifica um limite de núcleo de CPU. O tempo de execução abre-se o pacote do código e define o limite de CPU para um núcleo.

Neste momento, a soma dos limites de é igual à capacidade do nó. Um processo e um contentor estão em execução com um núcleo e não que interfiram entre si. Service Fabric não coloque qualquer mais contentores ou réplicas quando estiver a especificar o limite de CPU.

No entanto, existem duas situações em que poderão disputam os outros processos da CPU. Nestas situações, um processo e um contentor do nosso exemplo podem ocorrer o problema de vizinho inúteis:

* *A combinação de serviços regidos e não regida e contentores*: se um utilizador cria um serviço sem qualquer governação de recursos especificada, o tempo de execução vê-lo como não existem recursos de consumo e pode colocá-lo no nó no nosso exemplo. Neste caso, este novo processo de forma eficaz consome algumas CPU em detrimento os serviços que já estão em execução no nó. Existem duas soluções para este problema. Não misture regidos e não regida serviços no mesmo cluster, ou utilizar [restrições de posicionamento](service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies.md) para que estes dois tipos de serviços não fim no mesmo conjunto de nós.

* *Quando o outro processo está iniciado no nó, fora do Service Fabric (por exemplo, um serviço do SO)*: nesta situação, o processo fora do Service Fabric também contends para a CPU com os serviços existentes. A solução para este problema é configurar as capacidades de nó corretamente a conta para a sobrecarga do SO, conforme mostrado na secção seguinte.

## <a name="cluster-setup-for-enabling-resource-governance"></a>Configuração do cluster para ativar a governação de recursos

Quando um nó é iniciado e associa o cluster, o Service Fabric Deteta a quantidade de memória disponível e o número de núcleos disponível e, em seguida, define as capacidades nó para esses recursos de dois. 

Para deixar espaço de memória intermédia para o sistema operativo e para outros processos pode estar em execução no nó, o Service Fabric utiliza apenas 80% dos recursos disponíveis no nó. Esta percentagem é configurável e pode ser alterada no manifesto do cluster. 

Eis um exemplo de como ao instruir o Service Fabric para utilizar a 50% de CPU disponível e 70% de memória disponível: 

```xml
<Section Name="PlacementAndLoadBalancing">
    <!-- 0.0 means 0%, and 1.0 means 100%-->
    <Parameter Name="CpuPercentageNodeCapacity" Value="0.5" />
    <Parameter Name="MemoryPercentageNodeCapacity" Value="0.7" />
</Section>
```

Se precisar de configuração manual completa das capacidades de nó, pode utilizar o mecanismo de regular para descrever os nós do cluster. Eis um exemplo de como configurar o nó com quatro núcleos e 2 GB de memória: 

```xml
    <NodeType Name="MyNodeType">
      <Capacities>
        <Capacity Name="servicefabric:/_CpuCores" Value="4"/>
        <Capacity Name="servicefabric:/_MemoryInMB" Value="2048"/>
      </Capacities>
    </NodeType>
```

Quando a deteção automática de recursos disponíveis está ativada e as capacidades de nó são definidas manualmente no manifesto do cluster, o Service Fabric verifica que o nó tem recursos suficientes para suportar a capacidade de que o utilizador tiver definido:
* Se as capacidades de nó que estão definidas no manifesto são menor ou igual aos recursos disponíveis no nó, o Service Fabric utiliza as capacidades que são especificadas no manifesto.

* Se as capacidades de nó que estão definidas no manifesto são maiores que recursos disponíveis, o Service Fabric utiliza os recursos disponíveis como as capacidades de nó.

A deteção automática de recursos disponíveis pode ser desativada se não for necessária. Para desativar, altere a definição seguinte:

```xml
<Section Name="PlacementAndLoadBalancing">
    <Parameter Name="AutoDetectAvailableResources" Value="false" />
</Section>
```

Para um desempenho ideal, a seguinte definição deverá também ser ativada no manifesto do cluster: 

```xml
<Section Name="PlacementAndLoadBalancing">
    <Parameter Name="PreventTransientOvercommit" Value="true" /> 
    <Parameter Name="AllowConstraintCheckFixesDuringApplicationUpgrade" Value="true" />
</Section>
```


## <a name="specify-resource-governance"></a>Especifique a governação de recursos 

Os limites de governação de recursos estão especificados no manifesto da aplicação (secção ServiceManifestImport), conforme mostrado no exemplo seguinte:

```xml
<?xml version='1.0' encoding='UTF-8'?>
<ApplicationManifest ApplicationTypeName='TestAppTC1' ApplicationTypeVersion='vTC1' xsi:schemaLocation='http://schemas.microsoft.com/2011/01/fabric ServiceFabricServiceModel.xsd' xmlns='http://schemas.microsoft.com/2011/01/fabric' xmlns:xsi='http://www.w3.org/2001/XMLSchema-instance'>
  <Parameters>
  </Parameters>
  <!--
  ServicePackageA has the number of CPU cores defined, but doesn't have the MemoryInMB defined.
  In this case, Service Fabric sums the limits on code packages and uses the sum as 
  the overall ServicePackage limit.
  -->
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName='ServicePackageA' ServiceManifestVersion='v1'/>
    <Policies>
      <ServicePackageResourceGovernancePolicy CpuCores="1"/>
      <ResourceGovernancePolicy CodePackageRef="CodeA1" CpuShares="512" MemoryInMB="1000" />
      <ResourceGovernancePolicy CodePackageRef="CodeA2" CpuShares="256" MemoryInMB="1000" />
    </Policies>
  </ServiceManifestImport>
```
  
Neste exemplo, o pacote de serviço chamado **ServicePackageA** obtém um núcleo em nós de onde está colocada. Este pacote de serviço contém dois pacotes de código (**CodeA1** e **CodeA2**), e especifique ambos os `CpuShares` parâmetro. A proporção do CpuShares 512:256 divide as principais em todos os pacotes de dois código. 

Assim, neste exemplo, CodeA1 obtém dois-thirds de um núcleo e CodeA2 obtém um terço de um núcleo (e uma reserva de garantia de forma recuperável do mesmo). Se CpuShares não forem especificados para os pacotes de código, o Service Fabric divide os núcleos equitativamente entre elas.

Os limites de memória são absolutos, pelo que ambos os pacotes de código são limitados a 1024 MB de memória (e uma reserva de garantia de forma recuperável do mesmo). Pacotes de código (contentores ou processos) não é possível alocar mais memória a este limite e tentar fazer Sim resulta numa exceção de memória esgotada. Para que a imposição dos limites de recursos funcione, todos os pacotes do código dentro de um pacote de serviço devem ter limites de memória especificados.

## <a name="other-resources-for-containers"></a>Outros recursos para contentores
Para além da CPU e memória, é possível especificar limites outros recursos para contentores. Estes limites são especificadas ao nível do pacote do código e são aplicadas quando o contentor é iniciado. Ao contrário com a CPU e memória, o Gestor de recursos do Cluster não está ciente destes recursos e não não as verificações de capacidade ou -los balanceamento de carga. 

* *MemorySwapInMB*: A quantidade de memória de comutação que pode utilizar um contentor.
* *MemoryReservationInMB*: O limite de forma recuperável para governação de memória que é aplicado apenas quando é detetada contenção de memória no nó.
* *CpuPercent*: A percentagem de CPU que pode utilizar o contentor. Se forem especificados limites de CPU para o pacote de serviço, este parâmetro eficazmente é ignorado.
* *MaximumIOps*: O IOPS máximo que pode utilizar um contentor (leitura e escrita).
* *MaximumIOBytesps*: O máximo e/s (bytes por segundo) que pode utilizar um contentor (leitura e escrita).
* *BlockIOWeight*: O bloco e/s de importância para relativamente de outros contentores.

Estes recursos podem ser combinados com a CPU e memória. Eis um exemplo de como especificar recursos adicionais para contentores:

```xml
    <ServiceManifestImport>
        <ServiceManifestRef ServiceManifestName="FrontendServicePackage" ServiceManifestVersion="1.0"/>
        <Policies>
            <ResourceGovernancePolicy CodePackageRef="FrontendService.Code" CpuPercent="5"
            MemorySwapInMB="4084" MemoryReservationInMB="1024" MaximumIOPS="20" />
        </Policies>
    </ServiceManifestImport>
```

## <a name="next-steps"></a>Passos seguintes
* Para saber mais sobre o Gestor de recursos do Cluster, leia [introduzindo o Gestor de recursos de cluster do Service Fabric](service-fabric-cluster-resource-manager-introduction.md).
* Para obter mais informações sobre o modelo de aplicação, pacotes de serviços e os pacotes de código – e como mapeiam réplicas-lhes - ler [modelar uma aplicação no Service Fabric](service-fabric-application-model.md).
