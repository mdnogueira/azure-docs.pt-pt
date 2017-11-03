---
title: 'Criar e modificar um circuito ExpressRoute: PowerShell: Azure Resource Manager | Microsoft Docs'
description: Este artigo descreve como criar, aprovisionar, certifique-se, atualizar, eliminar e retirar o aprovisionamento um circuito ExpressRoute.
documentationcenter: na
services: expressroute
author: ganesr
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: f997182e-9b25-4a7a-b079-b004221dadcc
ms.service: expressroute
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/18/2017
ms.author: ganesr;cherylmc
ms.openlocfilehash: ef092a48994b68268109cb98bd6cd4526e259d5b
ms.sourcegitcommit: cf4c0ad6a628dfcbf5b841896ab3c78b97d4eafd
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/21/2017
---
# <a name="create-and-modify-an-expressroute-circuit-using-powershell"></a>Criar e modificar um circuito ExpressRoute com o PowerShell
> [!div class="op_single_selector"]
> * [Portal do Azure](expressroute-howto-circuit-portal-resource-manager.md)
> * [PowerShell](expressroute-howto-circuit-arm.md)
> * [CLI do Azure](howto-circuit-cli.md)
> * [Vídeo - portal do Azure](http://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-an-expressroute-circuit)
> * [PowerShell (clássico)](expressroute-howto-circuit-classic.md)
>

Este artigo descreve como criar um circuito ExpressRoute do Azure, utilizando os cmdlets do PowerShell e o modelo de implementação Azure Resource Manager. Este artigo também mostra como verificar o estado do circuito, a atualização, ou eliminar e retirar o aprovisionamento do mesmo.

## <a name="before-you-begin"></a>Antes de começar
* Instale a versão mais recente dos cmdlets do PowerShell do Azure Resource Manager. Para obter mais informações, consulte [descrição geral do Azure PowerShell](/powershell/azure/overview).
* Reveja o [pré-requisitos](expressroute-prerequisites.md) e [fluxos de trabalho](expressroute-workflows.md) antes de iniciar a configuração.


## <a name="create"></a>Criar e aprovisionar um circuito do ExpressRoute
### <a name="1-sign-in-to-your-azure-account-and-select-your-subscription"></a>1. Inicie sessão na sua conta do Azure e selecionar a sua subscrição
Para iniciar a configuração, inicie sessão na sua conta do Azure. Utilize os seguintes exemplos para ajudar na ligação:

```powershell
Login-AzureRmAccount
```

Verifique as subscrições da conta:

```powershell
Get-AzureRmSubscription
```

Selecione a subscrição que pretende criar um circuito de ExpressRoute para:

```powershell
Select-AzureRmSubscription -SubscriptionId "<subscription ID>"
```

### <a name="2-get-the-list-of-supported-providers-locations-and-bandwidths"></a>2. Obter a lista de fornecedores suportados, as localizações e larguras de banda
Antes de criar um circuito do ExpressRoute, terá da lista de fornecedores de conectividade suportados, as localizações e as opções de largura de banda.

O cmdlet do PowerShell **Get-AzureRmExpressRouteServiceProvider** devolve estas informações, que irá utilizar em passos posteriores:

```powershell
Get-AzureRmExpressRouteServiceProvider
```

Verifique se o seu fornecedor de conectividade é listada aqui. Anote as informações seguintes, o que precisa de mais tarde quando criar um circuito:

* Nome
* PeeringLocations
* BandwidthsOffered

Agora, está pronto para criar um circuito ExpressRoute.   

### <a name="3-create-an-expressroute-circuit"></a>3. Criar um circuito do ExpressRoute
Se ainda não tiver um grupo de recursos, tem de criar um antes de criar o seu circuito do ExpressRoute. Pode fazê-lo executando o seguinte comando:

```powershell
New-AzureRmResourceGroup -Name "ExpressRouteResourceGroup" -Location "West US"
```


O exemplo seguinte mostra como criar uma Mbps 200 circuito de ExpressRoute através de Equinix em Silicon Valley. Se estiver a utilizar um fornecedor diferente e definições diferentes, substitua essa informação quando efetua o pedido. Utilize o seguinte exemplo para solicitar uma nova chave de serviço:

```powershell
New-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup" -Location "West US" -SkuTier Standard -SkuFamily MeteredData -ServiceProviderName "Equinix" -PeeringLocation "Silicon Valley" -BandwidthInMbps 200
```

Certifique-se de que especifica a camada correta de SKU e família de SKUS:

* Camada SKU determina se a um padrão de ExpressRoute ou de um suplemento ExpressRoute premium está ativado. Pode especificar *padrão* para obter o SKU standard ou *Premium* para o suplemento premium.
* A família SKU determina o tipo de faturação. Pode especificar *Metereddata* para um plano de dados limitados e *Unlimiteddata* para um plano de dados ilimitados. Pode alterar o tipo de faturação *Metereddata* para *Unlimiteddata*, mas não é possível alterar o tipo de *Unlimiteddata* para *Metereddata*.

> [!IMPORTANT]
> O circuito ExpressRoute é faturado a partir do momento em que é emitida uma chave de serviço. Certifique-se de que efetuar esta operação quando o fornecedor de conectividade está pronto para aprovisionar o circuito.
> 
> 

A resposta contém a chave do serviço. Pode obter descrições detalhadas do todos os parâmetros executando o seguinte comando:

```powershell
get-help New-AzureRmExpressRouteCircuit -detailed
```


### <a name="4-list-all-expressroute-circuits"></a>4. Listar todos os circuitos do ExpressRoute
Para obter uma lista de todos os circuitos ExpressRoute que criou, execute o **Get-AzureRmExpressRouteCircuit** comando:

```powershell
Get-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"
```

A resposta semelhante ao seguinte exemplo:

    Name                             : ExpressRouteARMCircuit
    ResourceGroupName                : ExpressRouteResourceGroup
    Location                         : westus
    Id                               : /subscriptions/***************************/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/ExpressRouteARMCircuit
    Etag                             : W/"################################"
    ProvisioningState                : Succeeded
    Sku                              : {
                                         "Name": "Standard_MeteredData",
                                         "Tier": "Standard",
                                         "Family": "MeteredData"
                                          }
    CircuitProvisioningState          : Enabled
    ServiceProviderProvisioningState  : NotProvisioned
    ServiceProviderNotes              :
    ServiceProviderProperties         : {
                                          "ServiceProviderName": "Equinix",
                                          "PeeringLocation": "Silicon Valley",
                                          "BandwidthInMbps": 200
                                        }
    ServiceKey                        : **************************************
    Peerings                          : []

Pode obter estas informações em qualquer altura utilizando o `Get-AzureRmExpressRouteCircuit` cmdlet. Efetuar a chamada sem parâmetros apresenta uma lista de todos os circuitos. A chave do serviço está listada no *ServiceKey* campo:

```powershell
Get-AzureRmExpressRouteCircuit
```


A resposta semelhante ao seguinte exemplo:

    Name                             : ExpressRouteARMCircuit
    ResourceGroupName                : ExpressRouteResourceGroup
    Location                         : westus
    Id                               : /subscriptions/***************************/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/ExpressRouteARMCircuit
    Etag                             : W/"################################"
    ProvisioningState                : Succeeded
    Sku                              : {
                                         "Name": "Standard_MeteredData",
                                         "Tier": "Standard",
                                         "Family": "MeteredData"
                                          }
    CircuitProvisioningState         : Enabled
    ServiceProviderProvisioningState : NotProvisioned
    ServiceProviderNotes             :
    ServiceProviderProperties        : {
                                         "ServiceProviderName": "Equinix",
                                         "PeeringLocation": "Silicon Valley",
                                         "BandwidthInMbps": 200
                                          }
    ServiceKey                       : **************************************
    Peerings                         : []


Pode obter descrições detalhadas do todos os parâmetros executando o seguinte comando:

```powershell
get-help Get-AzureRmExpressRouteCircuit -detailed
```

### <a name="5-send-the-service-key-to-your-connectivity-provider-for-provisioning"></a>5. Enviar a chave do serviço para o seu fornecedor de conectividade para aprovisionamento
*ServiceProviderProvisioningState* fornece informações sobre o estado atual do aprovisionamento do lado do fornecedor de serviços. Estado fornece o estado do lado do Microsoft. Para obter mais informações sobre os Estados de aprovisionamento do circuito, consulte [fluxos de trabalho](expressroute-workflows.md#expressroute-circuit-provisioning-states).

Quando cria um novo circuito do ExpressRoute, o circuito é no seguinte estado:

    ServiceProviderProvisioningState : NotProvisioned
    CircuitProvisioningState         : Enabled



O circuito muda para o seguinte estado quando o fornecedor de conectividade está no processo de ativação-lo por si:

    ServiceProviderProvisioningState : Provisioning
    Status                           : Enabled

Para que possa ser capazes de utilizar um circuito do ExpressRoute, tem de ser no seguinte estado:

    ServiceProviderProvisioningState : Provisioned
    CircuitProvisioningState         : Enabled

### <a name="6-periodically-check-the-status-and-the-state-of-the-circuit-key"></a>6. Verificar periodicamente o estado e o estado da chave do circuito
A verificar o estado e o estado da chave do circuito permite-lhe saber quando o fornecedor tem ativado o seu circuito. Depois do circuito tiver sido configurado, *ServiceProviderProvisioningState* aparece como *aprovisionado*, conforme mostrado no exemplo seguinte:

```powershell
Get-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"
```


A resposta semelhante ao seguinte exemplo:

    Name                             : ExpressRouteARMCircuit
    ResourceGroupName                : ExpressRouteResourceGroup
    Location                         : westus
    Id                               : /subscriptions/***************************/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/ExpressRouteARMCircuit
    Etag                             : W/"################################"
    ProvisioningState                : Succeeded
    Sku                              : {
                                         "Name": "Standard_MeteredData",
                                         "Tier": "Standard",
                                         "Family": "MeteredData"
                                       }
    CircuitProvisioningState         : Enabled
    ServiceProviderProvisioningState : Provisioned
    ServiceProviderNotes             :
    ServiceProviderProperties        : {
                                         "ServiceProviderName": "Equinix",
                                         "PeeringLocation": "Silicon Valley",
                                         "BandwidthInMbps": 200
                                       }
    ServiceKey                       : **************************************
    Peerings                         : []

### <a name="7-create-your-routing-configuration"></a>7. Criar a configuração de encaminhamento
Para obter instruções passo a passo, consulte o [configuração de encaminhamento de circuito de ExpressRoute](expressroute-howto-routing-arm.md) artigo para criar e modificar peerings do circuito.

> [!IMPORTANT]
> Estas instruções aplicam-se apenas aos circuitos criados com fornecedores de serviços que oferecem serviços de 2 conectividade de camada. Se estiver a utilizar um fornecedor de serviço que oferece gerido layer 3 serviços (normalmente, uma VPN de IP, como MPLS), o seu fornecedor de conectividade configura e gere o encaminhamento por si.
> 
> 

### <a name="8-link-a-virtual-network-to-an-expressroute-circuit"></a>8. Ligar uma rede virtual a um circuito ExpressRoute
Em seguida, ligar uma rede virtual para o circuito do ExpressRoute. Utilize o [ligar redes virtuais para circuitos do ExpressRoute](expressroute-howto-linkvnet-arm.md) artigo quando trabalha com o modelo de implementação Resource Manager.

## <a name="getting-the-status-of-an-expressroute-circuit"></a>Obter o estado de um circuito ExpressRoute
Pode obter estas informações em qualquer altura utilizando o **Get-AzureRmExpressRouteCircuit** cmdlet. Efetuar a chamada sem parâmetros apresenta uma lista de todos os circuitos.

```powershell
Get-AzureRmExpressRouteCircuit
```


A resposta é semelhante ao seguinte exemplo:

    Name                             : ExpressRouteARMCircuit
    ResourceGroupName                : ExpressRouteResourceGroup
    Location                         : westus
    Id                               : /subscriptions/***************************/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/ExpressRouteARMCircuit
    Etag                             : W/"################################"
    ProvisioningState                : Succeeded
    Sku                              : {
                                         "Name": "Standard_MeteredData",
                                         "Tier": "Standard",
                                         "Family": "MeteredData"
                                       }
    CircuitProvisioningState         : Enabled
    ServiceProviderProvisioningState : Provisioned
    ServiceProviderNotes             :
    ServiceProviderProperties        : {
                                            "ServiceProviderName": "Equinix",
                                            "PeeringLocation": "Silicon Valley",
                                            "BandwidthInMbps": 200
                                          }
    ServiceKey                       : **************************************
    Peerings                         : []


Pode obter informações sobre um circuito de ExpressRoute específico transferindo o nome do grupo de recursos e o nome de circuito como um parâmetro para a chamada:

```powershell
Get-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"
```


A resposta semelhante ao seguinte exemplo:

    Name                             : ExpressRouteARMCircuit
    ResourceGroupName                : ExpressRouteResourceGroup
    Location                         : westus
    Id                               : /subscriptions/***************************/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/ExpressRouteARMCircuit
    Etag                             : W/"################################"
    ProvisioningState                : Succeeded
    Sku                              : {
                                         "Name": "Standard_MeteredData",
                                            "Tier": "Standard",
                                            "Family": "MeteredData"
                                          }
    CircuitProvisioningState         : Enabled
    ServiceProviderProvisioningState : Provisioned
    ServiceProviderNotes             :
    ServiceProviderProperties        : {
                                         "ServiceProviderName": "Equinix",
                                         "PeeringLocation": "Silicon Valley",
                                         "BandwidthInMbps": 200
                                          }
    ServiceKey                       : **************************************
    Peerings                         : []


Pode obter descrições detalhadas do todos os parâmetros executando o seguinte comando:

```powershell
get-help get-azurededicatedcircuit -detailed
```

## <a name="modify"></a>Modificar um circuito do ExpressRoute
Pode modificar algumas propriedades de um circuito ExpressRoute sem afetar a conectividade.

Pode efetuar as seguintes tarefas sem período de indisponibilidade:

* Ativar ou desativar um suplemento ExpressRoute premium para o seu circuito do ExpressRoute.
* Aumente a largura de banda do circuito do ExpressRoute fornecido está capacidade disponível na porta. Desatualização de largura de banda de um circuito não é suportada. 
* Altere o plano de medição de dados limitados para dados ilimitados. Não é suportada a alteração do plano de medição de dados ilimitados dados limitados.
* Pode ativar e desativar *permitir operações clássicas*.

Para obter mais informações sobre limites e limitações, consulte o [FAQ do ExpressRoute](expressroute-faqs.md).

### <a name="to-enable-the-expressroute-premium-add-on"></a>Para ativar o suplemento ExpressRoute premium
Pode ativar o suplemento ExpressRoute premium para o seu circuito existente utilizando o seguinte fragmento de PowerShell:

```powershell
$ckt = Get-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

$ckt.Sku.Tier = "Premium"
$ckt.sku.Name = "Premium_MeteredData"

Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt
```

O circuito tem agora as funcionalidades de suplemento ExpressRoute premium ativadas. Iremos começar de faturação para a capacidade de suplemento premium, assim que o comando foi executada com êxito.

### <a name="to-disable-the-expressroute-premium-add-on"></a>Para desativar o suplemento ExpressRoute premium
> [!IMPORTANT]
> Se estiver a utilizar recursos que são maiores que o que é permitido para o circuito standard, pode efetuar esta operação.
> 
> 

Tenha em atenção as seguintes informações:

* Antes de mudar de premium para standard, certifique-se de que o número de redes virtuais que estão ligados ao circuito é inferior a 10. Caso contrário, o pedido de atualização falha e iremos faturar-lhe às taxas de premium.
* Tem de desassociar todas as redes virtuais noutras regiões geopolíticas. Se não fizer isto, o pedido de atualização falha e iremos faturar-lhe às taxas de premium.
* A tabela de rota tem de ser inferior a 4000 rotas para peering privado. Se o tamanho da tabela de rota for superior a 4000 rotas, a sessão de BGP ignora e não será possível reenabled até que o número de prefixos anunciados fica abaixo 4,000.

Pode desativar o suplemento ExpressRoute premium para o circuito existente utilizando o cmdlet PowerShell seguinte:

```powershell
$ckt = Get-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

$ckt.Sku.Tier = "Standard"
$ckt.sku.Name = "Standard_MeteredData"

Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt
```

### <a name="to-update-the-expressroute-circuit-bandwidth"></a>Para atualizar a largura de banda do circuito do ExpressRoute
Para opções de largura de banda suportadas para o fornecedor, verifique o [FAQ do ExpressRoute](expressroute-faqs.md). Pode escolher qualquer tamanho maior do que o tamanho do seu circuito existente.

> [!IMPORTANT]
> Poderá ter de recriar o circuito do ExpressRoute, se existir capacidade inadequada na porta existente. Não é possível atualizar o circuito se não houver nenhuma capacidade adicional nessa localização.
>
> Não é possível reduzir a largura de banda de um circuito de ExpressRoute sem interrupção. Desatualização de largura de banda requer a retirar o aprovisionamento do circuito ExpressRoute e, em seguida, reaprovisionar um circuito de ExpressRoute novo.
> 

Depois de decidir que precisa de tamanho, utilize o seguinte comando para redimensionar o seu circuito:

```powershell
$ckt = Get-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

$ckt.ServiceProviderProperties.BandwidthInMbps = 1000

Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt
```


O circuito será dimensionado cópias de segurança do lado do Microsoft. Em seguida, tem de contactar o seu fornecedor de conectividade para atualizar as configurações no seu lado para fazer corresponder esta alteração. Depois de efetuar esta notificação, vamos começar a faturação é para a opção de largura de banda atualizado.

### <a name="to-move-the-sku-from-metered-to-unlimited"></a>Para mover o SKU de limitado para ilimitado
Pode alterar o SKU de um circuito ExpressRoute, utilizando o seguinte fragmento de PowerShell:

```powershell
$ckt = Get-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

$ckt.Sku.Family = "UnlimitedData"
$ckt.sku.Name = "Premium_UnlimitedData"

Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt
```

### <a name="to-control-access-to-the-classic-and-resource-manager-environments"></a>Para controlar o acesso a ambientes de Resource Manager e clássico
Reveja as instruções em [circuitos do ExpressRoute mover do clássico para o modelo de implementação Resource Manager](expressroute-howto-move-arm.md).  

## <a name="delete"></a>Desaprovisionamento e eliminar um circuito do ExpressRoute
Tenha em atenção as seguintes informações:

* Tem de desassociar todas as redes virtuais do circuito ExpressRoute. Se esta operação falhar, verifique se as redes virtuais ligadas ao circuito.
* Se o fornecedor de serviço de circuito de ExpressRoute estado de aprovisionamento é **aprovisionamento** ou **aprovisionado** tem de trabalhar com o fornecedor de serviços para retirar o aprovisionamento do circuito no seu lado. Vamos continuar a reserva de recursos e faturar-lhe até que o fornecedor do serviço concluída desaprovisionamento o circuito e notifica-nos.
* Se o fornecedor de serviços tem desaprovisionada o circuito (o fornecedor do serviço de estado de aprovisionamento está definido como **não aprovisionado**), pode eliminar o circuito. Este deixa de faturação para o circuito.

Pode eliminar o circuito do ExpressRoute, executando o seguinte comando:

```powershell
Remove-AzureRmExpressRouteCircuit -ResourceGroupName "ExpressRouteResourceGroup" -Name "ExpressRouteARMCircuit"
```

## <a name="next-steps"></a>Passos seguintes

Depois de criar o seu circuito, certifique-se de que efetua os seguintes passos:

* [Criar e modificar o encaminhamento para o seu circuito do ExpressRoute](expressroute-howto-routing-arm.md)
* [Ligar a rede virtual para o circuito do ExpressRoute](expressroute-howto-linkvnet-arm.md)