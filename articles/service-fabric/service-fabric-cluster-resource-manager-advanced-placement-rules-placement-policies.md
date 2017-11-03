---
title: "Gestor de recursos de Cluster de recursos de infraestrutura de serviço - as políticas de colocação | Microsoft Docs"
description: "Descrição geral das políticas de colocação adicionais e regras para serviços do Service Fabric"
services: service-fabric
documentationcenter: .net
author: masnider
manager: timlt
editor: 
ms.assetid: 5c2d19c6-dd40-4c4b-abd3-5c5ec0abed38
ms.service: Service-Fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: c240643d2a7ce98ddd7f7871eeef654cced953f7
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="placement-policies-for-service-fabric-services"></a>Políticas de colocação para serviços do service fabric
Políticas de colocação são regras adicionais que podem ser utilizadas para regular posicionamento do serviço em alguns cenários específicos, menos comum. Alguns exemplos desses cenários são:

- O cluster do Service Fabric abrange as distâncias geográficas, por exemplo, vários centros de dados no local ou em regiões do Azure
- O ambiente abrange várias áreas do controlo geopolítica ou legal ou alguns outro caso em que tenha a política de limites que precisa de impor
- Existem considerações de desempenho ou latência de comunicação devido a distâncias grande ou a utilização de ligações de rede mais lentas ou menos fiável
- Tem de manter determinadas colocalizada cargas de trabalho como um melhor esforço, a com outras cargas de trabalho ou na proximidade aos clientes

A maior parte destes requisitos alinha com o esquema físico do cluster, representado como os domínios de falhas do cluster. 

As políticas de colocação avançadas que o ajudam a endereço estes cenários são:

1. Domínios inválidos
2. Domínios necessários
3. Domínios preferenciais
4. Desautorizar mais de réplica

A maioria dos seguintes controlos podiam ser configurada através de restrições de posicionamento e propriedades de nó, mas alguns são mais complicadas. Para tornar as coisas simples, o Gestor de recursos de Cluster do Service Fabric fornece estas políticas de colocação adicionais. Políticas de colocação são configuradas numa base de instância de serviço por nome. Eles também podem ser atualizados dinamicamente.

## <a name="specifying-invalid-domains"></a>Especificar domínios inválidos
O **InvalidDomain** política de colocação permite-lhe especificar que um determinado domínio de falhas é inválido para um serviço específico. Esta política assegura que nunca executa um serviço específico numa área específica, por exemplo por motivos de política geopolítica ou da empresa. É possível especificar vários domínios inválidos através de políticas diferentes.

<center>
![Exemplo de domínio inválido][Image1]
</center>

Código:

```csharp
ServicePlacementInvalidDomainPolicyDescription invalidDomain = new ServicePlacementInvalidDomainPolicyDescription();
invalidDomain.DomainName = "fd:/DCEast"; //regulations prohibit this workload here
serviceDescription.PlacementPolicies.Add(invalidDomain);
```

PowerShell:

```posh
New-ServiceFabricService -ApplicationName $applicationName -ServiceName $serviceName -ServiceTypeName $serviceTypeName –Stateful -MinReplicaSetSize 3 -TargetReplicaSetSize 3 -PartitionSchemeSingleton -PlacementPolicy @("InvalidDomain,fd:/DCEast”)
```
## <a name="specifying-required-domains"></a>Especificar domínios necessários
A política de colocação de domínio necessários requer que o serviço está presente apenas no domínio especificado. Vários domínios necessários podem ser especificados através de políticas diferentes.

<center>
![Exemplo de domínio necessários][Image2]
</center>

Código:

```csharp
ServicePlacementRequiredDomainPolicyDescription requiredDomain = new ServicePlacementRequiredDomainPolicyDescription();
requiredDomain.DomainName = "fd:/DC01/RK03/BL2";
serviceDescription.PlacementPolicies.Add(requiredDomain);
```

PowerShell:

```posh
New-ServiceFabricService -ApplicationName $applicationName -ServiceName $serviceName -ServiceTypeName $serviceTypeName –Stateful -MinReplicaSetSize 3 -TargetReplicaSetSize 3 -PartitionSchemeSingleton -PlacementPolicy @("RequiredDomain,fd:/DC01/RK03/BL2")
```

## <a name="specifying-a-preferred-domain-for-the-primary-replicas-of-a-stateful-service"></a>Especificar um domínio preferido para as réplicas primárias de um serviço com monitorização de estado
O domínio primário preferencial Especifica o domínio de falhas colocar primário no. O principal acaba por ficar neste domínio quando tudo está em bom estado. Se o domínio ou a réplica primária falha ou encerra, principal move para outra localização, idealmente no mesmo domínio. Se esta nova localização não se encontra num domínio preferencial, o Gestor de recursos de Cluster move-o novamente para o domínio preferencial logo que possível. Naturalmente esta definição só faz sentido para serviços com monitorização de estado. Esta política é mais útil em clusters que são abrangidos em regiões do Azure ou vários datacenters, mas tem os serviços que preferem colocação numa determinada localização. Manter Primaries próximo de outros serviços ou os seus utilizadores ajuda a fornece menor latência, especialmente para leituras, que são processadas pela Primaries por predefinição.

<center>
![Domínios primários preferidos e ativação pós-falha][Image3]
</center>

```csharp
ServicePlacementPreferPrimaryDomainPolicyDescription primaryDomain = new ServicePlacementPreferPrimaryDomainPolicyDescription();
primaryDomain.DomainName = "fd:/EastUS/";
serviceDescription.PlacementPolicies.Add(invalidDomain);
```

PowerShell:

```posh
New-ServiceFabricService -ApplicationName $applicationName -ServiceName $serviceName -ServiceTypeName $serviceTypeName –Stateful -MinReplicaSetSize 3 -TargetReplicaSetSize 3 -PartitionSchemeSingleton -PlacementPolicy @("PreferredPrimaryDomain,fd:/EastUS")
```

## <a name="requiring-replica-distribution-and-disallowing-packing"></a>Exigir a distribuição de réplica e desautorizar mais
Réplicas são _normalmente_ distribuídos por domínios de falhas e a atualização, quando o cluster está em bom estado. No entanto, há casos em que mais do que uma réplica de uma determinada partição poderá acabar temporariamente packed para um único domínio. Por exemplo, vamos supor que o se o cluster tem nove nós em três domínios de falhas, df: / 0 DF: / 1 e DF: / 2. Também Digamos que o serviço tem três réplicas. Digamos que os nós que estavam a ser utilizados para as réplicas no DF: / 1 e DF: / 2 ficou. Normalmente, o Gestor de recursos de Cluster prefere outros nós desses mesmos domínios de falhas. Neste caso, vamos supor que devido a problemas de capacidade nenhum dos outros nós nesses domínios eram válidas. Se o Gestor de recursos do Cluster cria substituições para as réplicas, seria necessário escolher nós DF: / 0. No entanto, se o fizer _que_ cria uma situação em que a restrição de domínio de falhas é violada. Packing aumenta de réplicas a probabilidade de definir a réplica completa foi abaixo ou perder-se. 

> [!NOTE]
> Para obter mais informações sobre as restrições e restrição prioridades geralmente, consulte [neste tópico](service-fabric-cluster-resource-manager-management-integration.md#constraint-priorities).
>

Se tiver alguma vez viu uma mensagem de estado de funcionamento, tal como "`The Load Balancer has detected a Constraint Violation for this Replica:fabric:/<some service name> Secondary Partition <some partition ID> is violating the Constraint: FaultDomain`", em seguida, tiver atingido esta condição ou algo semelhante a este. Normalmente, apenas uma ou duas réplicas são packed em conjunto temporariamente. Desde que existem menos de um quórum de réplicas num determinado domínio, está seguro. Mais raros, mas pode acontecer e, normalmente, estas situações transitórias, uma vez que os nós voltar atrás. Se a nós permaneça para baixo e o Gestor de recursos do Cluster tem de criar substituições, normalmente, existem outros nós disponíveis nos domínios de falhas ideal.

Algumas cargas de trabalho prefere ter sempre o número de destino de réplicas, mesmo que estes são packed em menos de domínios. Estas cargas de trabalho são betting contra falhas de total domínio permanente em simultâneo e, normalmente, podem recuperar o estado do local. Outras cargas de trabalho em vez de efetuar o período de indisponibilidade anteriores ao correcção de risco ou perda de dados. Executam a maioria das cargas de trabalho de produção com mais de três réplicas, mais de três domínios de falhas e muitos nós válido por domínio de falhas. Por este motivo, o comportamento predefinido permite mais de domínio por predefinição. O comportamento predefinido permite normal balanceamento e ativação pós-falha para lidar com estes casos extremos, mesmo que o que significa mais domínio temporário.

Se pretender desativar essas mais de uma carga de trabalho indicada, pode especificar o `RequireDomainDistribution` política no serviço. Quando esta política estiver definida, o Gestor de recursos de Cluster garante a que executam não duas réplicas da mesma partição no mesmo domínio de falhas ou atualização.

Código:

```csharp
ServicePlacementRequireDomainDistributionPolicyDescription distributeDomain = new ServicePlacementRequireDomainDistributionPolicyDescription();
serviceDescription.PlacementPolicies.Add(distributeDomain);
```

PowerShell:

```posh
New-ServiceFabricService -ApplicationName $applicationName -ServiceName $serviceName -ServiceTypeName $serviceTypeName –Stateful -MinReplicaSetSize 3 -TargetReplicaSetSize 3 -PartitionSchemeSingleton -PlacementPolicy @("RequiredDomainDistribution")
```

Agora, seria possível que a utilizar estas configurações para serviços em cluster que não foi geograficamente abrangido? Foi, mas não existe não é um excelente motivo demasiado. As configurações de domínio necessários, inválidos e preferencial devem ser evitadas, salvo se os cenários necessitam. -Não fazer qualquer sentido para tentar forçar uma carga de trabalho determinada para ser executada num único bastidor ou preferir algumas segmento do seu cluster local em vez de outro. As configurações de hardware diferentes devem ser distribuídas por domínios de falhas e processadas através de restrições de posicionamento normal e propriedades de nó.

## <a name="next-steps"></a>Passos seguintes
- Para obter mais informações sobre como configurar os serviços, [Saiba mais sobre a configuração de serviços](service-fabric-cluster-resource-manager-configure-services.md)

[Image1]:./media/service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies/cluster-invalid-placement-domain.png
[Image2]:./media/service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies/cluster-required-placement-domain.png
[Image3]:./media/service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies/cluster-preferred-primary-domain.png
