---
title: "Azure Service Fabric programático dimensionamento | Microsoft Docs"
description: "Um cluster do Azure Service Fabric entrada ou saída de escala através de programação, de acordo com os acionadores personalizados"
services: service-fabric
documentationcenter: .net
author: mjrousos
manager: jonjung
editor: 
ms.assetid: 
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/17/2017
ms.author: mikerou
ms.openlocfilehash: 3d123a3d06420194d2918b71c98152cd2ea03457
ms.sourcegitcommit: 9c3150e91cc3075141dc2955a01f47040d76048a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/26/2017
---
# <a name="scale-a-service-fabric-cluster-programmatically"></a>Dimensionar um cluster do Service Fabric através de programação 

Noções básicas de dimensionamento de um cluster do Service Fabric no Azure são abrangidas na documentação no [dimensionamento de clusters](./service-fabric-cluster-scale-up-down.md). Este artigo abrange como clusters de Service Fabric assentes em conjuntos de dimensionamento de máquina virtual e podem ser ampliadas manualmente ou com regras de dimensionamento automático. Este documento observa programáticos métodos de coordenação Azure dimensionamento operações para cenários mais avançados. 

## <a name="reasons-for-programmatic-scaling"></a>Razões para dimensionamento programático
Em vários cenários, dimensionar manualmente ou através de regras de dimensionamento automático é boas soluções. Noutros cenários, no entanto, estes não podem ser a opção adequada. Desvantagens potenciais para estas abordagens incluem:

- Dimensionar Manualmente requer a iniciar sessão e explicitamente pedir operações de dimensionamento. Se operações de dimensionamento forem necessárias com frequência, ou em alturas imprevisíveis, esta abordagem não pode ser uma boa solução.
- Quando as regras de dimensionamento automático remover uma instância de um conjunto de dimensionamento de máquina virtual, estes não remove automaticamente dados de conhecimento desse nó do cluster de Service Fabric associado, a menos que o tipo de nó tem um nível de durabilidade de Silver ou Gold. Porque as regras de dimensionamento automático funcionam em escala definido ao nível (e não ao nível do Service Fabric), as regras de dimensionamento automático podem remover nós de Service Fabric sem encerrado-las sem problemas. Esta remoção de nó grosseiro sair "fantasma" Estado do nó de Service Fabric após operações de escala. Uma pessoa (ou um serviço) teria de limpar periodicamente o estado do nó removido no cluster de Service Fabric.
  - Um tipo de nó com um nível de durabilidade de ouro ou Silver limpa automaticamente removidas nós, pelo que não é necessária nenhuma limpar adicional.
- Apesar de existirem [métricas muitos](../monitoring-and-diagnostics/insights-autoscale-common-metrics.md) suportado pelas regras de dimensionamento automático, é ainda um conjunto limitado. Se o seu cenário chama-se em algum métrica não abrangida desse conjunto de dimensionamento, em seguida, as regras de dimensionamento automático não podem ser uma boa opção.

Com base nestas limitações, pode querer implementar mais personalizados Automáticos dimensionamento modelos. 

## <a name="scaling-apis"></a>APIs de dimensionamento
As APIs do Azure existem que permitem conjuntos de dimensionamento de aplicações para trabalhar programaticamente com a máquina virtual e clusters do Service Fabric. Se as opções de dimensionamento automático existente não funcionam para o seu cenário, estas APIs possibilitam a implementação da lógica de dimensionamento personalizada. 

Uma abordagem para implementar esta funcionalidade de dimensionamento automático efetuadas home consiste em Adicionar um novo serviço sem monitorização de estado para a aplicação de Service Fabric para gerir as operações de dimensionamento. No âmbito do serviço `RunAsync` método, um conjunto de acionadores pode determinar se o dimensionamento é necessário (incluindo a verificação de parâmetros, tais como o tamanho máximo do cluster e dimensionamento cooldowns).   

A API utilizada para o conjunto de interações de dimensionamento de máquina virtual (ambos para verificar o número atual de instâncias de máquina virtual e modificá-lo) está a [biblioteca de computação de gestão do Azure fluent](https://www.nuget.org/packages/Microsoft.Azure.Management.Compute.Fluent/). A biblioteca de computação fluent fornece uma API de fácil utilização para interagir com conjuntos de dimensionamento de máquina virtual.

Para interagir com o próprio cluster do Service Fabric, utilize [System.Fabric.FabricClient](/dotnet/api/system.fabric.fabricclient).

Obviamente, o código de dimensionamento não tem de ser executado como um serviço no cluster para ser ampliada. Ambos `IAzure` e `FabricClient` pode ligar-se aos respetivos recursos do Azure associados remotamente, pelo que o serviço de dimensionamento pode facilmente uma aplicação de consola ou o serviço Windows, executando a partir de fora da aplicação de Service Fabric. 

## <a name="credential-management"></a>Gestão de credenciais
Um desafio de escrever um serviço para processar o dimensionamento é que o serviço tem de ser capaz de aceder a recursos de conjunto de dimensionamento de máquina virtual sem um início de sessão interativo. Aceder ao cluster do Service Fabric é fácil se o serviço de dimensionamento é modificar as suas próprias aplicações de Service Fabric, mas as credenciais são necessárias para aceder ao conjunto de dimensionamento. Para iniciar sessão, pode utilizar um [principal de serviço](https://docs.microsoft.com/cli/azure/create-an-azure-service-principal-azure-cli) criado com o [Azure CLI 2.0](https://github.com/azure/azure-cli).

Um principal de serviço pode ser criado com os seguintes passos:

1. Inicie sessão na CLI do Azure (`az login`) como um utilizador com acesso para o dimensionamento da máquina virtual definido
2. Criar o serviço principal com o`az ad sp create-for-rbac`
    1. Anote o appId (denominado 'ID de cliente' noutro local), o nome, a palavra-passe e o tenant para utilização posterior.
    2. Também terá do ID de subscrição, que pode ser visualizado com`az account list`

A biblioteca de computação fluent pode iniciar sessão utilizando estas credenciais da seguinte forma (tenha em atenção que, como tipos de Azure fluent core `IAzure` estão a [Microsoft.Azure.Management.Fluent](https://www.nuget.org/packages/Microsoft.Azure.Management.Fluent/) pacote):

```C#
var credentials = new AzureCredentials(new ServicePrincipalLoginInformation {
                ClientId = AzureClientId,
                ClientSecret = 
                AzureClientKey }, AzureTenantId, AzureEnvironment.AzureGlobalCloud);
IAzure AzureClient = Azure.Authenticate(credentials).WithSubscription(AzureSubscriptionId);

if (AzureClient?.SubscriptionId == AzureSubscriptionId)
{
    ServiceEventSource.Current.ServiceMessage(Context, "Successfully logged into Azure");
}
else
{
    ServiceEventSource.Current.ServiceMessage(Context, "ERROR: Failed to login to Azure");
}
```

Assim que a sessão iniciada, a contagem de instâncias do conjunto de dimensionamento pode ser consultada através de `AzureClient.VirtualMachineScaleSets.GetById(ScaleSetId).Capacity`.

## <a name="scaling-out"></a>Aumentar horizontalmente
Utilizar o Azure fluent computação SDK, podem ser adicionadas a instâncias para o conjunto com as chamadas alguns - de dimensionamento de máquina virtual

```C#
var scaleSet = AzureClient.VirtualMachineScaleSets.GetById(ScaleSetId);
var newCapacity = (int)Math.Min(MaximumNodeCount, scaleSet.Capacity + 1);
scaleSet.Update().WithCapacity(newCapacity).Apply(); 
``` 

Em alternativa, o tamanho do conjunto de dimensionamento de máquina virtual também pode ser gerido com os cmdlets do PowerShell. [`Get-AzureRmVmss`](https://docs.microsoft.com/powershell/module/azurerm.compute/get-azurermvmss)Pode obter o objeto de conjunto de dimensionamento de máquina virtual. A capacidade atual está disponível através de `.sku.capacity` propriedade. Depois de alterar a capacidade para o valor pretendido, o conjunto no Azure de dimensionamento de máquina virtual pode ser atualizado com o [ `Update-AzureRmVmss` ](https://docs.microsoft.com/powershell/module/azurerm.compute/update-azurermvmss) comando.

Como adicionar uma escala definir instância deve ser quando adicionar um nó manualmente, tudo o que precisa para iniciar um novo nó de Service Fabric, uma vez que a escala definir modelo inclui extensões para associar automaticamente novas instâncias para o cluster do Service Fabric. 

## <a name="scaling-in"></a>Dimensionamento no

Dimensionamento no é semelhante ao aumentar horizontalmente. O máquina de virtual real do conjunto de dimensionamento alterações são praticamente os mesmos. No entanto, tal como foi mencionado anteriormente, Service Fabric só limpa automaticamente removidas nós com uma durabilidade de ouro ou Silver. Por isso, as características de durabilidade Bronze escala em maiúsculas e minúsculas, é necessário interagir com o cluster do Service Fabric para encerrar o nó a remover e, em seguida, para remover o seu estado.

Preparar o nó de encerramento envolve ao localizar o nó ser removido (o nó mais recentemente adicionado) e desativá-lo. Para nós seed não, nós mais recentes podem ser encontrados comparando `NodeInstanceId`. 

```C#
using (var client = new FabricClient())
{
    var mostRecentLiveNode = (await client.QueryManager.GetNodeListAsync())
        .Where(n => n.NodeType.Equals(NodeTypeToScale, StringComparison.OrdinalIgnoreCase))
        .Where(n => n.NodeStatus == System.Fabric.Query.NodeStatus.Up)
        .OrderByDescending(n => n.NodeInstanceId)
        .FirstOrDefault();
```

Nós seed são diferentes e não necessariamente siga a Convenção de que os IDs de instância superiores são removidos pela primeira vez.

Assim que for encontrado o nó ser removido, podem ser desativado e removida com o mesmo `FabricClient` instância e o `IAzure` instância de versões anteriores.

```C#
var scaleSet = AzureClient.VirtualMachineScaleSets.GetById(ScaleSetId);

// Remove the node from the Service Fabric cluster
ServiceEventSource.Current.ServiceMessage(Context, $"Disabling node {mostRecentLiveNode.NodeName}");
await client.ClusterManager.DeactivateNodeAsync(mostRecentLiveNode.NodeName, NodeDeactivationIntent.RemoveNode);

// Wait (up to a timeout) for the node to gracefully shutdown
var timeout = TimeSpan.FromMinutes(5);
var waitStart = DateTime.Now;
while ((mostRecentLiveNode.NodeStatus == System.Fabric.Query.NodeStatus.Up || mostRecentLiveNode.NodeStatus == System.Fabric.Query.NodeStatus.Disabling) &&
        DateTime.Now - waitStart < timeout)
{
    mostRecentLiveNode = (await client.QueryManager.GetNodeListAsync()).FirstOrDefault(n => n.NodeName == mostRecentLiveNode.NodeName);
    await Task.Delay(10 * 1000);
}

// Decrement VMSS capacity
var newCapacity = (int)Math.Max(MinimumNodeCount, scaleSet.Capacity - 1); // Check min count 

scaleSet.Update().WithCapacity(newCapacity).Apply(); 
```

Como aumentar horizontalmente, cmdlets do PowerShell para modificar o dimensionamento da máquina virtual com o conjunto de capacidade também pode ser utilizada aqui se uma abordagem de script é preferível. Assim que a instância de máquina virtual é removida, pode remover o estado de nó de Service Fabric.

```C#
await client.ClusterManager.RemoveNodeStateAsync(mostRecentLiveNode.NodeName);
```

## <a name="potential-drawbacks"></a>Desvantagens potenciais

Como é demonstrado nos fragmentos de código anterior, criar os seus próprios de dimensionamento serviço fornece que o mais elevado grau de controlo e customizability através da aplicação do dimensionamento comportamento. Isto pode ser útil para cenários que requerem controlo preciso sobre quando ou como uma aplicação dimensiona de entrada ou saída. No entanto, este controlo é fornecido com um compromisso de complexidade de código. Através desta abordagem significa que terá de próprio código dimensionamento, que é não trivial.

Como deve abordar o dimensionamento do Service Fabric depende do seu cenário. Se o dimensionamento é invulgar, a capacidade de adicionar ou remover nós manualmente é provavelmente suficiente. Para cenários mais complexos, regras de dimensionamento automático e SDKs exposição a capacidade de dimensionar programaticamente oferecem alternativas poderosas.

## <a name="next-steps"></a>Passos seguintes

Para começar a implementar a sua própria lógica de dimensionamento automático, familiarize-se com os seguintes conceitos e APIs úteis:

- [Dimensionar manualmente ou com regras de dimensionamento automático](./service-fabric-cluster-scale-up-down.md)
- [Fluent bibliotecas de gestão do Azure para .NET](https://github.com/Azure/azure-sdk-for-net/tree/Fluent) (útil para interagir com conjuntos de dimensionamento de máquina virtual do cluster do Service Fabric subjacente)
- [System.Fabric.FabricClient](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient) (útil para interagir com um cluster do Service Fabric e os respetivos nós)
