---
title: 'Eliminar um gateway de rede virtual: PowerShell: Azure Resource Manager | Microsoft Docs'
description: "Elimine um gateway de rede virtual com o PowerShell no modelo de implementação Resource Manager."
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: vpn-gateway
ms.devlang: na
ms.topic: 
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/20/2017
ms.author: cherylmc
ms.openlocfilehash: 4d0f085423d5bd60b24d88649ee1d77bcd1d009f
ms.sourcegitcommit: c7215d71e1cdeab731dd923a9b6b6643cee6eb04
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/17/2017
---
# <a name="delete-a-virtual-network-gateway-using-powershell"></a>Eliminar um gateway de rede virtual com o PowerShell
> [!div class="op_single_selector"]
> * [Portal do Azure](vpn-gateway-delete-vnet-gateway-portal.md)
> * [PowerShell](vpn-gateway-delete-vnet-gateway-powershell.md)
> * [PowerShell (clássico)](vpn-gateway-delete-vnet-gateway-classic-powershell.md)
>
>

Existem duas abordagens diferentes que pode tomar quando pretender eliminar um gateway de rede virtual para uma configuração de gateway VPN.

- Se pretender eliminar tudo e comece de novo, como no caso de um ambiente de teste, pode eliminar o grupo de recursos. Quando elimina um grupo de recursos, elimina todos os recursos dentro do grupo. Este é o método só é recomendada se não pretender manter qualquer um dos recursos no grupo de recursos. Não é possível eliminar seletivamente apenas alguns recursos através desta abordagem.

- Se pretender manter alguns dos recursos no seu grupo de recursos, ao eliminar um gateway de rede virtual fica ligeiramente mais complicado. Antes de poder eliminar o gateway de rede virtual, primeiro tem de eliminar todos os recursos dependentes no gateway. Os passos a que seguir dependem do tipo de ligações que criou e os recursos dependentes para cada ligação.

## <a name="before-beginning"></a>Antes de começar

### <a name="1-download-the-latest-azure-resource-manager-powershell-cmdlets"></a>1. Transferir os mais recentes cmdlets do PowerShell do Azure Resource Manager.

Transfira e instale a versão mais recente dos cmdlets do PowerShell do Azure Resource Manager. Para obter mais informações sobre como transferir e instalar os cmdlets do PowerShell, consulte [como instalar e configurar o Azure PowerShell](/powershell/azure/overview).

### <a name="2-connect-to-your-azure-account"></a>2. Ligar à sua conta do Azure.

Abra a consola do PowerShell e ligue-se à sua conta. Utilize o exemplo seguinte para o ajudar na ligação:

```powershell
Login-AzureRmAccount
```

Verifique as subscrições da conta.

```powershell
Get-AzureRmSubscription
```

Se tiver mais do que uma subscrição, especifique a subscrição que pretende utilizar.

```powershell
Select-AzureRmSubscription -SubscriptionName "Replace_with_your_subscription_name"
```

## <a name="S2S"></a>Eliminar um gateway de VPN de Site a Site

Para eliminar um gateway de rede virtual para uma configuração de S2S, deve primeiro eliminar cada recurso que diz respeito ao gateway de rede virtual. Recursos tem de ser eliminados por uma ordem determinada devido a dependências. Quando trabalhar com os exemplos abaixo, alguns dos valores devem ser especificados, enquanto outros valores num resultado de saída. Utilizamos os seguintes valores específicos nos exemplos para fins de demonstração:

Nome da VNet: VNet1<br>
Nome do grupo de recursos: RG1<br>
O nome de gateway de rede virtual: GW1<br>

Os seguintes passos aplicam-se ao modelo de implementação Resource Manager.

### <a name="1-get-the-virtual-network-gateway-that-you-want-to-delete"></a>1. Obter o gateway de rede virtual que pretende eliminar.

```powershell
$Gateway=get-azurermvirtualnetworkgateway -Name "GW1" -ResourceGroupName "RG1"
```

### <a name="2-check-to-see-if-the-virtual-network-gateway-has-any-connections"></a>2. Verifique se o gateway de rede virtual tem todas as ligações.

```powershell
get-azurermvirtualnetworkgatewayconnection -ResourceGroupName "RG1" | where-object {$_.VirtualNetworkGateway1.Id -eq $GW.Id}
$Conns=get-azurermvirtualnetworkgatewayconnection -ResourceGroupName "RG1" | where-object {$_.VirtualNetworkGateway1.Id -eq $GW.Id}
```

### <a name="3-delete-all-connections"></a>3. Elimine todas as ligações.

Poderá ser-lhe pedido para confirmar a eliminação de cada uma das ligações.

```powershell
$Conns | ForEach-Object {Remove-AzureRmVirtualNetworkGatewayConnection -Name $_.name -ResourceGroupName $_.ResourceGroupName}
```

### <a name="4-delete-the-virtual-network-gateway"></a>4. Elimine o gateway de rede virtual.

Poderá ser-lhe pedido para confirmar a eliminação do gateway. Se tiver uma configuração de P2S para esta VNet, além da sua configuração de S2S, a eliminação do gateway de rede virtual desligará automaticamente todos os clientes de P2S sem aviso.


```powershell
Remove-AzureRmVirtualNetworkGateway -Name "GW1" -ResourceGroupName "RG1"
```

Neste momento, o gateway de rede virtual foi eliminado. Pode utilizar os passos seguintes para eliminar todos os recursos que já não estão a ser utilizados.

### <a name="5-delete-the-local-network-gateways"></a>5 eliminar os gateways de rede local.

Obter a lista de gateways de rede local correspondente.

```powershell
$LNG=Get-AzureRmLocalNetworkGateway -ResourceGroupName "RG1" | where-object {$_.Id -In $Conns.LocalNetworkGateway2.Id}
```

Elimine os gateways de rede local. Poderá ser-lhe pedido para confirmar a eliminação de cada um gateway de rede local.

```powershell
$LNG | ForEach-Object {Remove-AzureRmLocalNetworkGateway -Name $_.Name -ResourceGroupName $_.ResourceGroupName}
```

### <a name="6-delete-the-public-ip-address-resources"></a>6. Elimine os recursos de endereço IP público.

Obter as configurações de IP do gateway de rede virtual.

```powershell
$GWIpConfigs = $Gateway.IpConfigurations
```

Obter a lista de recursos de endereço IP público utilizado para este gateway de rede virtual. Se o gateway de rede virtual foi ativo-ativo, verá dois endereços IP públicos.

```powershell
$PubIP=Get-AzureRmPublicIpAddress | where-object {$_.Id -In $GWIpConfigs.PublicIpAddress.Id}
```

Elimine os recursos do IP público.

```powershell
$PubIP | foreach-object {remove-azurermpublicIpAddress -Name $_.Name -ResourceGroupName "RG1"}
```

### <a name="7-delete-the-gateway-subnet-and-set-the-configuration"></a>7. Eliminar a sub-rede do gateway e definir a configuração.

```powershell
$GWSub = Get-AzureRmVirtualNetwork -ResourceGroupName "RG1" -Name "VNet1" | Remove-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet"
Set-AzureRmVirtualNetwork -VirtualNetwork $GWSub
```

## <a name="v2v"></a>Eliminar um gateway de VPN de VNet a VNet

Para eliminar um gateway de rede virtual para uma configuração de V2V, deve primeiro eliminar cada recurso que diz respeito ao gateway de rede virtual. Recursos tem de ser eliminados por uma ordem determinada devido a dependências. Quando trabalhar com os exemplos abaixo, alguns dos valores devem ser especificados, enquanto outros valores num resultado de saída. Utilizamos os seguintes valores específicos nos exemplos para fins de demonstração:

Nome da VNet: VNet1<br>
Nome do grupo de recursos: RG1<br>
O nome de gateway de rede virtual: GW1<br>

Os seguintes passos aplicam-se ao modelo de implementação Resource Manager.

### <a name="1-get-the-virtual-network-gateway-that-you-want-to-delete"></a>1. Obter o gateway de rede virtual que pretende eliminar.

```powershell
$Gateway=get-azurermvirtualnetworkgateway -Name "GW1" -ResourceGroupName "RG1"
```

### <a name="2-check-to-see-if-the-virtual-network-gateway-has-any-connections"></a>2. Verifique se o gateway de rede virtual tem todas as ligações.

```powershell
get-azurermvirtualnetworkgatewayconnection -ResourceGroupName "RG1" | where-object {$_.VirtualNetworkGateway1.Id -eq $GW.Id}
```
 
Poderão existir outras ligações para o gateway de rede virtual que façam parte de um grupo de recursos diferentes. Verifique a existência de ligações adicionais em cada grupo de recursos adicionais. Neste exemplo, estamos a verificar a para ligações de RG2. Execute isto para cada grupo de recursos que têm o que pode ter uma ligação para o gateway de rede virtual.

```powershell
get-azurermvirtualnetworkgatewayconnection -ResourceGroupName "RG2" | where-object {$_.VirtualNetworkGateway2.Id -eq $GW.Id}
```

### <a name="3-get-the-list-of-connections-in-both-directions"></a>3. Obter a lista de ligações em ambas as direções.

Porque se trata de uma configuração de VNet a VNet, terá da lista de ligações em ambas as direções.

```powershell
$ConnsL=get-azurermvirtualnetworkgatewayconnection -ResourceGroupName "RG1" | where-object {$_.VirtualNetworkGateway1.Id -eq $GW.Id}
```
 
Neste exemplo, estamos a verificar a para ligações de RG2. Execute isto para cada grupo de recursos que têm o que pode ter uma ligação para o gateway de rede virtual.

```powershell
 $ConnsR=get-azurermvirtualnetworkgatewayconnection -ResourceGroupName "<NameOfResourceGroup2>" | where-object {$_.VirtualNetworkGateway2.Id -eq $GW.Id}
 ```

### <a name="4-delete-all-connections"></a>4. Elimine todas as ligações.

Poderá ser-lhe pedido para confirmar a eliminação de cada uma das ligações.

```powershell
$ConnsL | ForEach-Object {Remove-AzureRmVirtualNetworkGatewayConnection -Name $_.name -ResourceGroupName $_.ResourceGroupName}
$ConnsR | ForEach-Object {Remove-AzureRmVirtualNetworkGatewayConnection -Name $_.name -ResourceGroupName $_.ResourceGroupName}
```

### <a name="5-delete-the-virtual-network-gateway"></a>5. Elimine o gateway de rede virtual.

Poderá ser-lhe pedido para confirmar a eliminação do gateway de rede virtual. Se tiver P2S configurações para as suas VNets para além da sua configuração de V2V, os gateways de rede virtual a eliminar o irá desligar automaticamente todos os clientes de P2S sem aviso.

```powershell
Remove-AzureRmVirtualNetworkGateway -Name "GW1" -ResourceGroupName "RG1"
```

Neste momento, o gateway de rede virtual foi eliminado. Pode utilizar os passos seguintes para eliminar todos os recursos que já não estão a ser utilizados.

### <a name="6-delete-the-public-ip-address-resources"></a>6. Eliminar os recursos de endereço IP público

Obter as configurações de IP do gateway de rede virtual.

```powershell
$GWIpConfigs = $Gateway.IpConfigurations
```

Obter a lista de recursos de endereço IP público utilizado para este gateway de rede virtual. Se o gateway de rede virtual foi ativo-ativo, verá dois endereços IP públicos.

```powershell
$PubIP=Get-AzureRmPublicIpAddress | where-object {$_.Id -In $GWIpConfigs.PublicIpAddress.Id}
```

Elimine os recursos do IP público. Poderá ser-lhe pedido para confirmar a eliminação do IP público.

```powershell
$PubIP | foreach-object {remove-azurermpublicIpAddress -Name $_.Name -ResourceGroupName "<NameOfResourceGroup1>"}
```

### <a name="7-delete-the-gateway-subnet-and-set-the-configuration"></a>7. Eliminar a sub-rede do gateway e definir a configuração.

```powershell
$GWSub = Get-AzureRmVirtualNetwork -ResourceGroupName "RG1" -Name "VNet1" | Remove-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet"
Set-AzureRmVirtualNetwork -VirtualNetwork $GWSub
```

## <a name="deletep2s"></a>Eliminar um gateway de VPN de ponto a Site

Para eliminar um gateway de rede virtual para uma configuração de P2S, deve primeiro eliminar cada recurso que diz respeito ao gateway de rede virtual. Recursos tem de ser eliminados por uma ordem determinada devido a dependências. Quando trabalhar com os exemplos abaixo, alguns dos valores devem ser especificados, enquanto outros valores num resultado de saída. Utilizamos os seguintes valores específicos nos exemplos para fins de demonstração:

Nome da VNet: VNet1<br>
Nome do grupo de recursos: RG1<br>
O nome de gateway de rede virtual: GW1<br>

Os seguintes passos aplicam-se ao modelo de implementação Resource Manager.


>[!NOTE]
> Ao eliminar o gateway VPN, todos os clientes ligados serão desligados da VNet sem aviso.
>
>

### <a name="1-get-the-virtual-network-gateway-that-you-want-to-delete"></a>1. Obter o gateway de rede virtual que pretende eliminar.

```powershell
$Gateway=get-azurermvirtualnetworkgateway -Name "GW1" -ResourceGroupName "RG1"
```

### <a name="2-delete-the-virtual-network-gateway"></a>2. Elimine o gateway de rede virtual.

Poderá ser-lhe pedido para confirmar a eliminação do gateway de rede virtual.

```powershell
Remove-AzureRmVirtualNetworkGateway -Name "GW1" -ResourceGroupName "RG1"
```

Neste momento, o gateway de rede virtual foi eliminado. Pode utilizar os passos seguintes para eliminar todos os recursos que já não estão a ser utilizados.

### <a name="3-delete-the-public-ip-address-resources"></a>3. Eliminar os recursos de endereço IP público

Obter as configurações de IP do gateway de rede virtual.

```powershell
$GWIpConfigs = $Gateway.IpConfigurations
```

Obter a lista de endereços IP públicos utilizados para este gateway de rede virtual. Se o gateway de rede virtual foi ativo-ativo, verá dois endereços IP públicos.

```powershell
$PubIP=Get-AzureRmPublicIpAddress | where-object {$_.Id -In $GWIpConfigs.PublicIpAddress.Id}
```

Elimine os IPs públicos. Poderá ser-lhe pedido para confirmar a eliminação do IP público.

```powershell
$PubIP | foreach-object {remove-azurermpublicIpAddress -Name $_.Name -ResourceGroupName "<NameOfResourceGroup1>"}
```

### <a name="4-delete-the-gateway-subnet-and-set-the-configuration"></a>4. Eliminar a sub-rede do gateway e definir a configuração.

```powershell
$GWSub = Get-AzureRmVirtualNetwork -ResourceGroupName "RG1" -Name "VNet1" | Remove-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet"
Set-AzureRmVirtualNetwork -VirtualNetwork $GWSub
```

## <a name="delete"></a>Eliminar um gateway de VPN, ao eliminar o grupo de recursos

Se não estiver preocupado sobre manter qualquer um dos seus recursos no grupo de recursos e pretender começar de novo, pode eliminar um grupo de recursos completo. Esta é uma forma rápida de remover tudo. Os seguintes passos aplicam-se apenas ao modelo de implementação Resource Manager.

### <a name="1-get-a-list-of-all-the-resource-groups-in-your-subscription"></a>1. Obter uma lista de todos os grupos de recursos na sua subscrição.

```powershell
Get-AzureRmResourceGroup
```

### <a name="2-locate-the-resource-group-that-you-want-to-delete"></a>2. Localize o grupo de recursos que pretende eliminar.

Localize o grupo de recursos que pretende eliminar e ver a lista de recursos nesse grupo de recursos. No exemplo, o nome do grupo de recursos é RG1. Modificar o exemplo para obter uma lista de todos os recursos.

```powershell
Find-AzureRmResource -ResourceGroupNameContains RG1
```

### <a name="3-verify-the-resources-in-the-list"></a>3. Certifique-se os recursos na lista.

Quando é devolvida a lista, reveja a certifique-se de que pretende eliminar todos os recursos no grupo de recursos, bem como o grupo de recursos em si. Se pretender manter alguns dos recursos no grupo de recursos, utilize os passos nas secções anteriores deste artigo para eliminar o gateway.

### <a name="4-delete-the-resource-group-and-resources"></a>4. Elimine o grupo de recursos e recursos.

Para eliminar o grupo de recursos e todos os recursos contidos no grupo de recursos, modifique o exemplo e executar.

```powershell
Remove-AzureRmResourceGroup -Name RG1
```

### <a name="5-check-the-status"></a>5. Verifique o estado.

Demora algum tempo para o Azure eliminar todos os recursos. Pode verificar o estado do grupo de recursos com este cmdlet.

```powershell
Get-AzureRmResourceGroup -ResourceGroupName RG1
```

O resultado é devolvido mostra "Com êxito".

```
ResourceGroupName : RG1
Location          : eastus
ProvisioningState : Succeeded
```