---
title: "Conjuntos de dimensionamento de tipos de nó de Service Fabric do Azure e a máquina virtual | Microsoft Docs"
description: "Saiba como define o nó de Azure Service Fabric tipos relacionados com o dimensionamento de máquina virtual e como ligar remotamente a uma escala definir instância ou nó de cluster."
services: service-fabric
documentationcenter: .net
author: ChackDan
manager: timlt
editor: 
ms.assetid: 5441e7e0-d842-4398-b060-8c9d34b07c48
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/05/2017
ms.author: chackdan
ms.openlocfilehash: 2bd3053d645d9acd4850fddf7f27237ff954e8c7
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="azure-service-fabric-node-types-and-virtual-machine-scale-sets"></a>Conjuntos de dimensionamento de tipos de nó de Service Fabric do Azure e a máquina virtual
Conjuntos de dimensionamento de máquina virtual são um recurso de computação do Azure. Pode utilizar conjuntos de dimensionamento para implementar e gerir uma coleção de máquinas virtuais como um conjunto. Configure uma escala separada definido para cada tipo de nó que definem a um cluster do Service Fabric do Azure. Independentemente pode dimensionar cada tipo de nó ou reduzir verticalmente, têm conjuntos diferentes de portas abertas e utilizar as métricas de capacidade diferentes.

A figura seguinte mostra um cluster que tem dois tipos de nó, com o nome de front-end e back-end. Cada tipo de nó tem cinco nós.

![Um cluster que tem dois tipos de nó][NodeTypes]

## <a name="map-virtual-machine-scale-set-instances-to-nodes"></a>Mapear instâncias de conjunto de dimensionamento de máquina virtual para nós
Conforme mostrado na imagem anterior, as instâncias do conjunto de dimensionamento começam em 0 de instância e, em seguida, aumentam, 1. A numeração é refletida nos nomes de nó. Por exemplo, o nó BackEnd_0 é 0 uma instância do conjunto de dimensionamento de back-end. Este conjunto de dimensionamento específica tem cinco instâncias, com o nome BackEnd_0, BackEnd_1, BackEnd_2, BackEnd_3 e BackEnd_4.

Quando dimensionar um conjunto de dimensionamento, é criada uma nova instância. O novo nome de instância do conjunto de dimensionamento é, normalmente, que a escala definir nome e o seguinte número de instância. No nosso exemplo, é BackEnd_5.

## <a name="map-scale-set-load-balancers-to-node-types-and-scale-sets"></a>Mapear balanceadores de carga do conjunto de dimensionamento para tipos de nó e conjuntos de dimensionamento
Se implementar o seu cluster no portal do Azure ou utilizar o modelo Azure Resource Manager de exemplo, são listados todos os recursos de um grupo de recursos. Pode ver os balanceadores de carga para cada tipo de conjunto ou nó de escala. O nome do Balanceador de carga utiliza o seguinte formato: **LB -&lt;nome do tipo de nó&gt;**. Um exemplo é LB-sfcluster4doc-0, conforme mostrado na figura seguinte:

![Recursos][Resources]
## <a name="remote-connect-to-a-virtual-machine-scale-set-instance-or-a-cluster-node"></a>Remoto ligar a uma instância de conjunto de dimensionamento de máquina virtual ou um nó de cluster
Configure um conjunto para cada tipo de nó definido num cluster de dimensionamento separado. Independentemente pode dimensionar os tipos de nó ou reduzir verticalmente. Também pode utilizar os SKUs de VM diferente. Ao contrário das VMs de instância única, as instâncias do conjunto de dimensionamento não tem os seus próprios endereços IP virtuais. Isto pode ser um desafio quando está a procurar um endereço IP e a porta que pode utilizar para ligar remotamente a uma instância específica.

Para localizar um endereço IP e a porta que pode utilizar para ligar remotamente a uma instância específica, conclua os passos seguintes.

**Passo 1**: localizar o endereço IP virtual para o tipo de nó por obter regras NAT de entrada para o protocolo de ambiente de trabalho remoto (RDP).

Em primeiro lugar, obter os valores de regras NAT entrados que foram definidos como parte da definição do recurso para `Microsoft.Network/loadBalancers`.

No portal do Azure, na página de Balanceador de carga, selecione **definições** > **regras NAT de entrada**. Isto fornece o endereço IP e definir a porta que pode utilizar para ligar remotamente à escala primeira instância. 

![Load balancer][LBBlade]

Na figura seguinte, o endereço IP e a porta são **104.42.106.156** e **3389**.

![Regras de NAT][NATRules]

**Passo 2**: localizar a porta que pode utilizar para ligar remotamente para a instância do conjunto de dimensionamento específico ou o nó.

Conjunto de dimensionamento mapa de instâncias para nós. Utilize as informações de conjunto de dimensionamento para determinar a porta exata a utilizar.

As portas são alocadas por uma ordem ascendente que corresponda a instância do conjunto de dimensionamento. Para obter o anterior exemplo o tipo de nó de front-end, a tabela seguinte lista as portas para cada uma das instâncias do cinco nó. Aplica o mesmo mapeamento à sua instância do conjunto de dimensionamento.

| **Instância do conjunto de dimensionamento de máquina virtual** | **Porta** |
| --- | --- |
| FrontEnd_0 |3389 |
| FrontEnd_1 |3390 |
| FrontEnd_2 |3391 |
| FrontEnd_3 |3392 |
| FrontEnd_4 |3393 |
| FrontEnd_5 |3394 |

**Passo 3**: ligar remotamente à instância do conjunto de dimensionamento específico.

A figura seguinte demonstra utilizando a ligação ao ambiente de trabalho remoto para ligar à instância de conjunto de dimensionamento do FrontEnd_1:

![Ligação de ambiente de trabalho remota][RDP]

## <a name="change-the-rdp-port-range-values"></a>Alterar os valores de intervalo de portas RDP

### <a name="before-cluster-deployment"></a>Antes da implementação de cluster
Quando configurar o cluster utilizando um modelo do Resource Manager, especifique o intervalo no `inboundNatPools`.

Aceda a definição do recurso para `Microsoft.Network/loadBalancers`. Localize a descrição do `inboundNatPools`.  Substitua o `frontendPortRangeStart` e `frontendPortRangeEnd` valores.

![valores de inboundNatPools][InboundNatPools]

### <a name="after-cluster-deployment"></a>Após a implementação de cluster
Alterar os valores de intervalo de portas RDP após ter sido implementado o cluster for mais complexo. Para garantir que não reciclar as VMs, utilize o Azure PowerShell para definir novos valores. 

> [!NOTE]
> Certifique-se de que tem o Azure PowerShell 1.0 ou uma versão posterior instalada no seu computador. Se não tiver o Azure Powershell 1.0 ou uma versão posterior, recomendamos que siga os passos descritos no [como instalar e configurar o Azure PowerShell.](/powershell/azure/overview)

1. Inicie sessão sua conta do Azure. Se o seguinte comando do PowerShell falhar, certifique-se de que instalou corretamente o PowerShell.

    ```
    Login-AzureRmAccount
    ```

2. Para obter detalhes sobre o seu Balanceador de carga e para ver os valores para a descrição do `inboundNatPools`, execute o seguinte código:

    ```
    Get-AzureRmResource -ResourceGroupName <resource group name> -ResourceType Microsoft.Network/loadBalancers -ResourceName <load balancer name>
    ```

3. Definir `frontendPortRangeEnd` e `frontendPortRangeStart` para os valores que pretende.

    ```
    $PropertiesObject = @{
        #Property = value;
    }
    Set-AzureRmResource -PropertyObject $PropertiesObject -ResourceGroupName <resource group name> -ResourceType Microsoft.Network/loadBalancers -ResourceName <load balancer name> -ApiVersion <use the API version that is returned> -Force
    ```

## <a name="change-the-rdp-user-name-and-password-for-nodes"></a>Alterar o nome de utilizador do RDP e a palavra-passe para nós

Para alterar a palavra-passe para todos os nós de um tipo de nó específico, execute os seguintes passos. Estas alterações serão aplicadas a todos os nós atuais e futuros no conjunto de dimensionamento.

1. Abra o PowerShell como um Administrador. 
2. Para iniciar sessão e selecione a sua subscrição para a sessão, execute os seguintes comandos. Alterar o `SUBSCRIPTIONID` parâmetro para o ID de subscrição. 

    ```powershell
    Login-AzureRmAccount
    Get-AzureRmSubscription -SubscriptionId 'SUBSCRIPTIONID' | Select-AzureRmSubscription
    ```

3. Execute o seguinte script. Utilize o relevante `NODETYPENAME`, `RESOURCEGROUP`, `USERNAME`, e `PASSWORD` valores. O `USERNAME` e `PASSWORD` os valores são as novas credenciais que utilize sessões RDP no futuro. 

    ```powershell
    $nodeTypeName = 'NODETYPENAME'
    $resourceGroup = 'RESOURCEGROUP'
    $publicConfig = @{'UserName' = 'USERNAME'}
    $privateConfig = @{'Password' = 'PASSWORD'}
    $extName = 'VMAccessAgent'
    $publisher = 'Microsoft.Compute'
    $node = Get-AzureRmVmss -ResourceGroupName $resourceGroup -VMScaleSetName $nodeTypeName
    $node = Add-AzureRmVmssExtension -VirtualMachineScaleSet $node -Name $extName -Publisher $publisher -Setting $publicConfig -ProtectedSetting $privateConfig -Type $extName -TypeHandlerVersion '2.0' -AutoUpgradeMinorVersion $true

    Update-AzureRmVmss -ResourceGroupName $resourceGroup -Name $nodeTypeName -VirtualMachineScaleSet $node
    ```

## <a name="next-steps"></a>Passos seguintes
* Consulte o [descrição geral da funcionalidade "Implementar em qualquer local" e uma comparação com clusters geridos pelo Azure](service-fabric-deploy-anywhere.md).
* Saiba mais sobre [cluster segurança](service-fabric-cluster-security.md).
* Saiba mais sobre o [SDK de Service Fabric e da introdução](service-fabric-get-started.md).

<!--Image references-->
[NodeTypes]: ./media/service-fabric-cluster-nodetypes/NodeTypes.png
[Resources]: ./media/service-fabric-cluster-nodetypes/Resources.png
[InboundNatPools]: ./media/service-fabric-cluster-nodetypes/InboundNatPools.png
[LBBlade]: ./media/service-fabric-cluster-nodetypes/LBBlade.png
[NATRules]: ./media/service-fabric-cluster-nodetypes/NATRules.png
[RDP]: ./media/service-fabric-cluster-nodetypes/RDP.png
