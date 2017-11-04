---
title: Dimensionar um cluster do Service Fabric do Azure | Microsoft Docs
description: Saiba como dimensionar rapidamente um cluster do Service Fabric.
services: service-fabric
documentationcenter: .net
author: Thraka
manager: timlt
editor: 
ms.assetid: 
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: tutorial
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 10/24/2017
ms.author: adegeo
ms.openlocfilehash: e1d35bcd51349e6460d50acec0d9706fcd291e89
ms.sourcegitcommit: f8437edf5de144b40aed00af5c52a20e35d10ba1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/03/2017
---
# <a name="scale-a-service-fabric-cluster"></a>Dimensionar um cluster do Service Fabric

Este tutorial faz parte três de uma série e mostra-lhe como dimensionar o seu cluster existente out e no. Quando tiver terminado, ficará a saber como dimensionar o seu cluster e como limpar quaisquer recursos de ativação pós-falha à esquerda.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Ler a contagem de nós de cluster
> * Adicionar nós de cluster (de escalamento horizontal)
> * Remover nós de cluster (escala em)

## <a name="prerequisites"></a>Pré-requisitos
Antes de começar este tutorial:
- Se não tiver uma subscrição do Azure, crie um [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- Instalar o [Azure Powershell versão 4.1 ou superior do módulo](https://docs.microsoft.com/powershell/azure/install-azurerm-ps) ou [Azure CLI 2.0](/cli/azure/install-azure-cli).
- Criar segura [cluster do Windows](service-fabric-tutorial-create-vnet-and-windows-cluster.md) ou [Linux cluster](service-fabric-tutorial-create-vnet-and-linux-cluster.md) no Azure
- Se implementar um cluster do Windows, configure um ambiente de desenvolvimento do Windows. Instalar [Visual Studio 2017](http://www.visualstudio.com) e **programação do Azure**, **desenvolvimento ASP.NET e web**, e **desenvolvimento de plataformas cruzadas .NET Core**cargas de trabalho.  Em seguida, configure um [ambiente de desenvolvimento de .NET](service-fabric-get-started.md).
- Se implementar um cluster do Linux, configurar um ambiente de desenvolvimento Java no [Linux](service-fabric-get-started-linux.md) ou [MacOS](service-fabric-get-started-mac.md).  Instalar o [Service Fabric CLI](service-fabric-cli.md). 

## <a name="sign-in-to-azure"></a>Iniciar sessão no Azure
Inicie sessão na sua conta do Azure selecione a sua subscrição antes de executar os comandos do Azure.

```powershell
Login-AzureRmAccount
Get-AzureRmSubscription
Set-AzureRmContext -SubscriptionId <guid>
```

## <a name="connect-to-the-cluster"></a>Ligar ao cluster

Para concluir com êxito esta parte do tutorial, precisa de ligar para o cluster do Service Fabric e o conjunto de dimensionamento de máquina virtual (que aloja o cluster). O conjunto de dimensionamento de máquina virtual é o recurso do Azure que aloja o Service Fabric no Azure.

Quando se liga a um cluster, pode consultá-la para obter informações. Pode utilizar o cluster para saber mais sobre os nós do cluster tem conhecimento de. Ligar ao cluster no seguinte código utiliza o mesmo certificado que foi criado na primeira parte desta série. Certifique-se de que define o `$endpoint` e `$thumbprint` variáveis para os seus valores.

```powershell
$endpoint = "<mycluster>.southcentralus.cloudapp.azure.com:19000"
$thumbprint = "63EB5BA4BC2A3BADC42CA6F93D6F45E5AD98A1E4"

Connect-ServiceFabricCluster -ConnectionEndpoint $endpoint `
          -KeepAliveIntervalInSec 10 `
          -X509Credential -ServerCertThumbprint $thumbprint `
          -FindType FindByThumbprint -FindValue $thumbprint `
          -StoreLocation CurrentUser -StoreName My

Get-ServiceFabricClusterHealth
```

Com o `Get-ServiceFabricClusterHealth` comando, o estado é devolvido para si com detalhes sobre o estado de funcionamento de cada nó no cluster.

## <a name="scale-out"></a>Aumentar horizontalmente

Ao aumentar horizontalmente, adicionar mais instâncias de máquina virtual para o conjunto de dimensionamento. Estas instâncias tornar-se os nós que utiliza o Service Fabric. Service Fabric sabe quando o conjunto de dimensionamento tem mais instâncias adicionadas (por ampliar) e reage automaticamente. O código seguinte obtém uma escala definida por nome e aumenta a **capacidade** da escala definida por 1.

```powershell
$scaleset = Get-AzureRmVmss -ResourceGroupName SFCLUSTERTUTORIALGROUP -VMScaleSetName nt1vm
$scaleset.Sku.Capacity += 1

Update-AzureRmVmss -ResourceGroupName SFCLUSTERTUTORIALGROUP -VMScaleSetName nt1vm -VirtualMachineScaleSet $scaleset
```

Uma vez concluída a operação de atualização, execute o `Get-ServiceFabricClusterHealth` comando para ver as novas informações de nó.

## <a name="scale-in"></a>Dimensionar de

Dimensionamento no é igual a aumentar horizontalmente, exceto que utiliza uma minúscula **capacidade** valor. Quando dimensionar no conjunto de dimensionamento, remova as instâncias de máquina virtual do conjunto de dimensionamento. Normalmente, o Service Fabric desconhece que tiver ocorrido e o pensa que um nó tornou-se em falta. Service Fabric em seguida, reporta um mau estado de funcionamento para o cluster. Para impedir que um Estado inválido, deve informar recursos de infraestrutura de serviço que espera que o nó para desaparecem.

### <a name="remove-the-service-fabric-node"></a>Remove o nó de recursos de infraestrutura de serviço

> [!NOTE]
> Esta parte aplica-se apenas a *Bronze* o escalão de durabilidade. Para obter mais informações sobre a durabilidade, consulte [planeamento de capacidade de cluster do Service Fabric][durability].

Quando dimensiona um conjunto de dimensionamento de máquina virtual, o conjunto (na maioria dos casos) de dimensionamento remove a instância de máquina virtual que foi criada pela última vez. Por isso, tem de encontrar o correspondente, pela última vez criado, o nó de recursos de infraestrutura de serviço. Pode encontrar este último nó verificando o biggest `NodeInstanceId` valor da propriedade em nós de recursos de infraestrutura de serviço. 

```powershell
Get-ServiceFabricNode | Sort-Object NodeInstanceId -Descending | Select-Object -First 1
```

O cluster do service fabric tem de saber o que este nó é removido. Existem três passos que precisa de tomar:

1. Desative o nó para que já não seja uma replicação de dados.  
`Disable-ServiceFabricNode`

2. Pare o nó para que o tempo de execução do service fabric encerra corretamente e a aplicação obtém um pedido de terminar.  
`Start-ServiceFabricNodeTransition -Stop`

2. Remove o nó do cluster.  
`Remove-ServiceFabricNodeState`

Depois de tem sido aplicados estes três passos para o nó, este pode ser removido do conjunto de dimensionamento. Se estiver a utilizar qualquer o escalão de durabilidade para além de [bronze][durability], estes passos são efetuados para que quando o dimensionamento definida instância é removida.

O bloco de código seguinte obtém o último nó criado, desativa, interrompe e remove o nó do cluster.

```powershell
# Get the node that was created last
$node = Get-ServiceFabricNode | Sort-Object NodeInstanceId -Descending | Select-Object -First 1

# Node details for the disable/stop process
$nodename = $node.NodeName
$nodeid = $node.NodeInstanceId

$loopTimeout = 10

# Run disable logic
Disable-ServiceFabricNode -NodeName $nodename -Intent RemoveNode -TimeoutSec 300 -Force

$state = Get-ServiceFabricNode | Where-Object NodeName -eq $nodename | Select-Object -ExpandProperty NodeStatus

while (($state -ne [System.Fabric.Query.NodeStatus]::Disabled) -and ($loopTimeout -ne 0))
{
    Start-Sleep 5
    $loopTimeout -= 1
    $state = Get-ServiceFabricNode | Where-Object NodeName -eq $nodename | Select-Object -ExpandProperty NodeStatus
    Write-Host "Checking state... $state found"
}

# Exit if the node was unable to be disabled
if ($state -ne [System.Fabric.Query.NodeStatus]::Disabled)
{
    Write-Error "Disable failed with state $state"
}
else
{
    # Stop node
    $stopid = New-Guid
    Start-ServiceFabricNodeTransition -Stop -OperationId $stopid -NodeName $nodename -NodeInstanceId $nodeid -StopDurationInSeconds 300
    
    $state = (Get-ServiceFabricNodeTransitionProgress -OperationId $stopid).State
    $loopTimeout = 10

    # Watch the transaction
    while (($state -eq [System.Fabric.TestCommandProgressState]::Running) -and ($loopTimeout -ne 0))
    {
        Start-Sleep 5
        $state = (Get-ServiceFabricNodeTransitionProgress -OperationId $stopid).State
        Write-Host "Checking state... $state found"
    }
    
    if ($state -ne [System.Fabric.TestCommandProgressState]::Completed)
    {
        Write-Error "Stop transaction failed with $state"
    }
    else
    {
        # Remove the node from the cluster
        Remove-ServiceFabricNodeState -NodeName $nodename -TimeoutSec 300 -Force
    }
}
```

### <a name="scale-in-the-scale-set"></a>Escalar no conjunto de dimensionamento

Agora que o nó de recursos de infraestrutura de serviço foi removido do cluster, o conjunto de dimensionamento de máquina virtual pode ser ampliado no. No exemplo abaixo, a capacidade de conjunto de dimensionamento é reduzida por 1.

```powershell
$scaleset = Get-AzureRmVmss -ResourceGroupName SFCLUSTERTUTORIALGROUP -VMScaleSetName nt1vm
$scaleset.Sku.Capacity -= 1

Update-AzureRmVmss -ResourceGroupName SFCLUSTERTUTORIALGROUP -VMScaleSetName nt1vm -VirtualMachineScaleSet $scaleset
```

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, ficou a saber como:

> [!div class="checklist"]
> * Ler a contagem de nós de cluster
> * Adicionar nós de cluster (de escalamento horizontal)
> * Remover nós de cluster (escala em)


Em seguida, avançar para o tutorial seguinte para saber como implementar uma aplicação e utilizar a API management.
> [!div class="nextstepaction"]
> [Implementar uma aplicação](service-fabric-tutorial-deploy-api-management.md)

[durability]: service-fabric-cluster-capacity.md#the-durability-characteristics-of-the-cluster