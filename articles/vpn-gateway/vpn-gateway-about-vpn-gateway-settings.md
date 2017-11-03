---
title: "As definições do gateway VPN para ligações do Azure em vários locais | Microsoft Docs"
description: "Saiba mais sobre as definições do Gateway de VPN para gateways de rede virtual do Azure."
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: timlt
editor: 
tags: azure-resource-manager,azure-service-management
ms.assetid: ae665bc5-0089-45d0-a0d5-bc0ab4e79899
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/12/2017
ms.author: cherylmc
ms.openlocfilehash: d265c72cfc02710afb630f3b8258602c936d1ebc
ms.sourcegitcommit: 1131386137462a8a959abb0f8822d1b329a4e474
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/13/2017
---
# <a name="about-vpn-gateway-configuration-settings"></a>Sobre as definições de configuração do Gateway de VPN

Um gateway de VPN é um tipo de gateway de rede virtual que envia o tráfego encriptado entre a rede virtual e a sua localização no local através de uma ligação pública. Também pode utilizar um gateway de VPN para enviar tráfego entre redes virtuais em toda a estrutura principal do Azure.

Uma ligação de gateway VPN baseia-se na configuração de vários recursos, cada um dos quais contém as definições configuráveis. As secções neste artigo abordam os recursos e as definições relacionadas com um gateway de VPN para uma rede virtual criado no modelo de implementação do Resource Manager. Pode encontrar descrições e diagramas de topologia para cada solução de ligação no [sobre o Gateway de VPN](vpn-gateway-about-vpngateways.md) artigo.

## <a name="gwtype"></a>Tipos de gateway

Cada rede virtual só pode ter um gateway de rede virtual de cada tipo. Quando estiver a criar um gateway de rede virtual, tem de se certificar de que o tipo de gateway está correto para a sua configuração.

Os valores disponíveis para - GatewayType são:

* Vpn
* ExpressRoute

Necessita de um gateway de VPN a `-GatewayType` *Vpn*.

Exemplo:

```powershell
New-AzureRmVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg `
-Location 'West US' -IpConfigurations $gwipconfig -GatewayType Vpn `
-VpnType RouteBased
```

## <a name="gwsku"></a>SKUs de Gateway

[!INCLUDE [vpn-gateway-gwsku-include](../../includes/vpn-gateway-gwsku-include.md)]

### <a name="configure-the-gateway-sku"></a>Configurar o SKU de gateway

#### <a name="azure-portal"></a>Portal do Azure

Se utilizar o portal do Azure para criar um gateway de rede virtual do Gestor de recursos, pode selecionar o SKU de gateway com a lista pendente. As opções que são apresentadas com correspondem para o tipo de Gateway e o tipo VPN que selecionar.

#### <a name="powershell"></a>PowerShell

O exemplo do PowerShell seguinte especifica o `-GatewaySku` como VpnGw1.

```powershell
New-AzureRmVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg `
-Location 'West US' -IpConfigurations $gwipconfig -GatewaySku VpnGw1 `
-GatewayType Vpn -VpnType RouteBased
```

#### <a name="resize"></a>Alteração (redimensionamento) um SKU de gateway

Se pretender atualizar o SKU de gateway para um SKU mais poderoso, pode utilizar o `Resize-AzureRmVirtualNetworkGateway` cmdlet do PowerShell. Também pode alterar o tamanho do SKU utilizar este cmdlet do gateway.

O exemplo do PowerShell seguinte mostra um gateway que está a ser redimensionada para VpnGw2 SKU.

```powershell
$gw = Get-AzureRmVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg
Resize-AzureRmVirtualNetworkGateway -VirtualNetworkGateway $gw -GatewaySku VpnGw2
```

## <a name="connectiontype"></a>Tipos de ligação

No modelo de implementação Resource Manager, cada configuração exige um tipo de ligação de gateway de rede virtual específico. Os valores disponíveis do PowerShell do Resource Manager para `-ConnectionType` são:

* IPsec
* Vnet2Vnet
* ExpressRoute
* VPNClient

No exemplo seguinte do PowerShell, iremos criar uma ligação S2S que requer o tipo de ligação *IPsec*.

```powershell
New-AzureRmVirtualNetworkGatewayConnection -Name localtovon -ResourceGroupName testrg `
-Location 'West US' -VirtualNetworkGateway1 $gateway1 -LocalNetworkGateway2 $local `
-ConnectionType IPsec -RoutingWeight 10 -SharedKey 'abc123'
```

## <a name="vpntype"></a>Tipos de VPN

Quando criar o gateway de rede virtual para uma configuração de gateway VPN, tem de especificar um tipo de VPN. O tipo de VPN que escolher depende da topologia de ligação que pretende criar. Por exemplo, uma ligação de P2S exige um tipo de RouteBased VPN. Também pode dependem de um tipo de VPN no hardware que está a utilizar. Configurações de S2S requerem um dispositivo VPN. Alguns dispositivos VPN só suportam um determinado tipo VPN.

Selecionar o tipo de VPN tem de satisfazer todas os requisitos de ligação para a solução que pretende criar. Por exemplo, se pretender criar uma ligação de gateway de S2S VPN e uma ligação de gateway de P2S VPN para a mesma rede virtual, teria de utilizar o tipo VPN *RouteBased* porque P2S requer um tipo de RouteBased VPN. Também terá de verificar que o seu dispositivo VPN suportada uma ligação de RouteBased VPN. 

Depois de criar um gateway de rede virtual, não é possível alterar o tipo de VPN. Tem de eliminar o gateway de rede virtual e crie um novo. Há dois tipos de VPN:

[!INCLUDE [vpn-gateway-vpntype](../../includes/vpn-gateway-vpntype-include.md)]

O exemplo do PowerShell seguinte especifica o `-VpnType` como *RouteBased*. Quando estiver a criar um gateway, tem de confirmar se o -VpnType está correto para a sua configuração.

```powershell
New-AzureRmVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg `
-Location 'West US' -IpConfigurations $gwipconfig `
-GatewayType Vpn -VpnType RouteBased
```

## <a name="requirements"></a>Requisitos do gateway

[!INCLUDE [vpn-gateway-table-requirements](../../includes/vpn-gateway-table-requirements-include.md)]

## <a name="gwsub"></a>Sub-rede de gateway

Antes de criar um gateway de VPN, terá de criar uma sub-rede de gateway. A sub-rede do gateway contém os endereços IP que utilizam os serviços e VMs de gateway de rede virtual. Ao criar o gateway de rede virtual, VMs de gateway são implementadas para a sub-rede do gateway e configuradas com as definições do gateway VPN necessárias. Nunca deve implementar mais nada (por exemplo, VMs adicionais) para a sub-rede do gateway. A sub-rede do gateway tem de ser o nome "GatewaySubnet" para funcionar corretamente. A sub-rede do gateway de nomenclatura "GatewaySubnet" permite saber que se trata de sub-rede para implementar as VMs de gateway de rede virtual e os serviços do Azure.

Quando cria a sub-rede do gateway, especifica o número de endereços IP que a sub-rede contém. Os endereços IP na sub-rede de gateway são atribuídos às VMs de gateway e os serviços de gateway. Algumas configurações exigem mais endereços IP que outros. Observe as instruções para a configuração que pretende criar e certifique-se de que a sub-rede do gateway que pretende criar cumpre os requisitos. Além disso, pode querer Certifique-se que à sub-rede do gateway contém endereços IP suficientes para acomodar possíveis configurações adicionais futuras. Embora seja possível criar uma sub-rede do gateway tão pequena como/29, recomendamos que crie uma sub-rede de gateway de/28 ou superior (/ 28, / 27, /26 etc.). Dessa forma, se adicionar a funcionalidade no futuro, não terá de fechar as suas gateway, em seguida, elimine e recrie a sub-rede do gateway para permitir mais endereços IP.

O exemplo de Gestor de recursos do PowerShell seguinte mostra uma sub-rede de gateway com o nome GatewaySubnet. Pode ver que a notação CIDR Especifica/27, que permite obter endereços IP suficientes para a maioria das configurações de que existe atualmente.

```powershell
Add-AzureRmVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -AddressPrefix 10.0.3.0/27
```

[!INCLUDE [vpn-gateway-no-nsg](../../includes/vpn-gateway-no-nsg-include.md)]

## <a name="lng"></a>Gateways de rede local

Ao criar uma configuração de gateway VPN, o gateway de rede local representa com frequência a sua localização no local. No modelo de implementação clássica, o gateway de rede local foi referido como Site Local. 

Dê um nome, o endereço IP público do dispositivo VPN no local, de gateway de rede local e especificar os prefixos de endereço que se encontram na localização no local. Azure analisa os prefixos de endereço de destino para o tráfego de rede, consulta a configuração que especificou para o gateway de rede local e encaminha os pacotes em conformidade. Também especificar gateways de rede local para configurações de VNet a VNet que utilizam uma ligação de gateway VPN.

O exemplo do PowerShell seguinte cria um novo gateway de rede local:

```powershell
New-AzureRmLocalNetworkGateway -Name LocalSite -ResourceGroupName testrg `
-Location 'West US' -GatewayIpAddress '23.99.221.164' -AddressPrefix '10.5.51.0/24'
```

Por vezes, terá de modificar as definições do gateway de rede local. Por exemplo, quando adiciona ou modifica o intervalo de endereços, ou se o endereço IP do dispositivo VPN é alterado. Consulte [modificar as definições do gateway de rede local com o PowerShell](vpn-gateway-modify-local-network-gateway.md).

## <a name="resources"></a>Cmdlets do PowerShell e REST APIs

Para obter recursos técnicos adicionais e requisitos de sintaxe específica quando utilizar REST APIs, os cmdlets do PowerShell ou o CLI do Azure para configurações de Gateway de VPN, consulte as seguintes páginas:

| **Clássico** | **Resource Manager** |
| --- | --- |
| [PowerShell](/powershell/module/azure#networking) |[PowerShell](/powershell/module/azurerm.network#vpn) |
| [API REST](https://msdn.microsoft.com/library/jj154113) |[API REST](/rest/api/network/virtualnetworkgateways) |
| Não suportado | [CLI do Azure](/cli/azure/network/vnet-gateway)|

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre configurações de ligação disponíveis, consulte [sobre o Gateway de VPN](vpn-gateway-about-vpngateways.md).
