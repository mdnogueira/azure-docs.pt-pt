---
title: "Configurar a imposição do túnel para ligações do Azure Site a Site: Resource Manager | Microsoft Docs"
description: "Como redirecionar ou \"forçar\" todo o tráfego vinculado à Internet voltar à sua localização no local."
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: cbe58db8-b598-4c9f-ac88-62c865eb8721
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/31/2017
ms.author: cherylmc
ms.openlocfilehash: cc8a3e7f2a907b1eea4ecf39df2b291b0fb8b207
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="configure-forced-tunneling-using-the-azure-resource-manager-deployment-model"></a>Configurar o túnel forçado utilizando o modelo de implementação Azure Resource Manager

Imposição do túnel permite-lhe redirecionamento ou "forçar" todo o tráfego vinculado à Internet de volta para a sua localização no local através de um túnel VPN Site a Site para inspeção e auditoria. Este é um requisito de segurança críticas para TI empresariais de maioria das políticas. Sem forçado tráfego de túnel, vinculado à Internet da sua VMs no Azure sempre traverses da infraestrutura de rede do Azure diretamente saída à Internet, sem a opção para permitir a inspecionar ou o tráfego de auditoria. Acesso à Internet não autorizado, potencialmente, pode levar a divulgação de informações ou outros tipos de falhas de segurança.

[!INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)] 

Este artigo explica como configurar forçado túnel para redes virtuais criadas com o modelo de implementação Resource Manager. Imposição do túnel pode ser configurado utilizando o PowerShell, não através do portal. Se pretender configurar a imposição do túnel para o modelo de implementação clássica, seleccione o artigo clássico seguinte na lista pendente:

> [!div class="op_single_selector"]
> * [PowerShell – Clássica](vpn-gateway-about-forced-tunneling.md)
> * [PowerShell – Resource Manager](vpn-gateway-forced-tunneling-rm.md)
> 
> 

## <a name="about-forced-tunneling"></a>Prestes imposição do túnel

O diagrama seguinte ilustra funciona como imposição de túnel. 

![Túnel Forçado](./media/vpn-gateway-forced-tunneling-rm/forced-tunnel.png)

No exemplo acima, o front-end sub-rede não está a ser forçada em túnel. As cargas de trabalho da sub-rede do front-end podem continuar a aceitar e responder a pedidos de cliente da Internet diretamente. As sub-redes de camada média dimensão que e back-end são forçadas em túnel. Quaisquer ligações de saída destas duas sub-redes para a Internet serão forçadas ou redirecionadas novamente para um site no local através de um dos túneis S2S VPN.

Isto permite-lhe restringir e inspecionar o acesso à Internet das suas máquinas virtuais ou serviços em nuvem na Azure, ao continuar ativar a arquitetura do serviço de várias camadas necessárias. Se não houver nenhuma cargas de trabalho para a Internet nas suas redes virtuais, também pode aplicar a imposição do túnel para as redes virtuais completos.

## <a name="requirements-and-considerations"></a>Requisitos e considerações

Imposição do túnel no Azure está configurado através de rotas definidas pelo utilizador de rede virtual. Redirecionar o tráfego para um site no local é expresso como uma rota predefinida para o gateway de VPN do Azure. Para obter mais informações sobre o encaminhamento definido pelo utilizador e redes virtuais, consulte [rotas definidas pelo utilizador e reencaminhamento IP](../virtual-network/virtual-networks-udr-overview.md).

* Cada sub-rede da rede virtual tem uma tabela de encaminhamento incorporada, do sistema. A tabela de encaminhamento do sistema tem os seguintes três grupos de rotas:
  
  * **Rotas de VNet locais:** diretamente para o destino VMs na mesma rede virtual.
  * **No local rotas:** para o VPN gateway do Azure.
  * **Rota predefinida:** diretamente à Internet. Pacotes destinados a entidades para os endereços IP privados não abrangidos pelas rotas de dois anteriores são ignorados.
* Este procedimento utiliza as rotas definidas pelo utilizador (UDR) para criar uma tabela de encaminhamento para adicionar uma rota predefinida e, em seguida, associar a tabela de encaminhamento para sua subnet(s) VNet para ativar a imposição do túnel nessas sub-redes.
* Imposição do túnel tem de ser associado a uma VNet com um gateway de VPN baseado na rota. Tem de definir um "site predefinido" entre os sites locais em vários locais ligado à rede virtual. Além disso, o dispositivo VPN no local tem de ser configurado utilizando 0.0.0.0/0 como seletores de tráfego. 
* ExpressRoute imposição do túnel não está configurada através desta mecanismo, mas em vez disso, é ativado por uma rota predefinida através de sessões de peering de BGP de ExpressRoute de publicidade. Para obter mais informações, consulte o [documentação do ExpressRoute](https://azure.microsoft.com/documentation/services/expressroute/).

## <a name="configuration-overview"></a>Descrição geral de configuração

O procedimento seguinte ajuda-o a criar um grupo de recursos e uma VNet. Em seguida, irá criar um gateway de VPN e configurar a imposição do túnel. Neste procedimento, a rede virtual 'MultiTier VNet' tem três sub-redes: 'Front-end', 'Midtier' e 'Back-end', com quatro em vários locais ligações: 'DefaultSiteHQ' e três ramos.

Os passos do procedimento definir o 'DefaultSiteHQ' como a ligação do site predefinido para a imposição do túnel e configurar 'Midtier' e 'Back-end' sub-redes para utilizar a imposição do túnel.

## <a name="before"></a>Antes de começar

Instale a versão mais recente dos cmdlets do PowerShell do Azure Resource Manager. Veja [Como instalar e configurar o Azure PowerShell](/powershell/azure/overview) para obter mais informações sobre como instalar os cmdlets PowerShell.

> [!IMPORTANT]
> É necessário instalar a versão mais recente dos cmdlets do PowerShell. Caso contrário, poderá receber erros de validação quando a execução de alguns dos cmdlets.
>
>

### <a name="to-log-in"></a>Para iniciar sessão

[!INCLUDE [To log in](../../includes/vpn-gateway-ps-login-include.md)]

## <a name="configure-forced-tunneling"></a>Configurar túnel forçado

> [!NOTE]
> Poderá ver avisos indicar "o tipo de objeto de resultado deste cmdlet será modificado numa versão futura". Este comportamento está previsto e pode ignorar com segurança estes avisos.
>
>


1. Crie um grupo de recursos.

  ```powershell
  New-AzureRmResourceGroup -Name 'ForcedTunneling' -Location 'North Europe'
  ```
2. Criar uma rede virtual e especificar sub-redes.

  ```powershell 
  $s1 = New-AzureRmVirtualNetworkSubnetConfig -Name "Frontend" -AddressPrefix "10.1.0.0/24"
  $s2 = New-AzureRmVirtualNetworkSubnetConfig -Name "Midtier" -AddressPrefix "10.1.1.0/24"
  $s3 = New-AzureRmVirtualNetworkSubnetConfig -Name "Backend" -AddressPrefix "10.1.2.0/24"
  $s4 = New-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -AddressPrefix "10.1.200.0/28"
  $vnet = New-AzureRmVirtualNetwork -Name "MultiTier-VNet" -Location "North Europe" -ResourceGroupName "ForcedTunneling" -AddressPrefix "10.1.0.0/16" -Subnet $s1,$s2,$s3,$s4
  ```
3. Crie os gateways de rede local.

  ```powershell
  $lng1 = New-AzureRmLocalNetworkGateway -Name "DefaultSiteHQ" -ResourceGroupName "ForcedTunneling" -Location "North Europe" -GatewayIpAddress "111.111.111.111" -AddressPrefix "192.168.1.0/24"
  $lng2 = New-AzureRmLocalNetworkGateway -Name "Branch1" -ResourceGroupName "ForcedTunneling" -Location "North Europe" -GatewayIpAddress "111.111.111.112" -AddressPrefix "192.168.2.0/24"
  $lng3 = New-AzureRmLocalNetworkGateway -Name "Branch2" -ResourceGroupName "ForcedTunneling" -Location "North Europe" -GatewayIpAddress "111.111.111.113" -AddressPrefix "192.168.3.0/24"
  $lng4 = New-AzureRmLocalNetworkGateway -Name "Branch3" -ResourceGroupName "ForcedTunneling" -Location "North Europe" -GatewayIpAddress "111.111.111.114" -AddressPrefix "192.168.4.0/24"
  ```
4. Crie a tabela de rotas e a regra de rota.

  ```powershell
  New-AzureRmRouteTable –Name "MyRouteTable" -ResourceGroupName "ForcedTunneling" –Location "North Europe"
  $rt = Get-AzureRmRouteTable –Name "MyRouteTable" -ResourceGroupName "ForcedTunneling" 
  Add-AzureRmRouteConfig -Name "DefaultRoute" -AddressPrefix "0.0.0.0/0" -NextHopType VirtualNetworkGateway -RouteTable $rt
  Set-AzureRmRouteTable -RouteTable $rt
  ```
5. Associe a tabela de rotas para sub-redes Midtier e back-end.

  ```powershell
  $vnet = Get-AzureRmVirtualNetwork -Name "MultiTier-Vnet" -ResourceGroupName "ForcedTunneling"
  Set-AzureRmVirtualNetworkSubnetConfig -Name "MidTier" -VirtualNetwork $vnet -AddressPrefix "10.1.1.0/24" -RouteTable $rt
  Set-AzureRmVirtualNetworkSubnetConfig -Name "Backend" -VirtualNetwork $vnet -AddressPrefix "10.1.2.0/24" -RouteTable $rt
  Set-AzureRmVirtualNetwork -VirtualNetwork $vnet
  ```
6. Crie o Gateway com um site predefinido. Este passo demora algum tempo a concluir, por vezes, 45 minutos ou mais, porque está a criar e configurar o gateway.<br> O **- GatewayDefaultSite** é o parâmetro de cmdlet permite que a configuração de encaminhamento forçada funcionem, por isso, asseguramos para configurar esta definição corretamente. Se observar erros de ValidateSet sobre o valor de GatewaySKU, certifique-se de que tem instalado o [versão mais recente dos cmdlets do PowerShell](#before). A versão mais recente dos cmdlets do PowerShell contém novos valores validados para os SKUs de Gateway mais recente.

  ```powershell
  $pip = New-AzureRmPublicIpAddress -Name "GatewayIP" -ResourceGroupName "ForcedTunneling" -Location "North Europe" -AllocationMethod Dynamic
  $gwsubnet = Get-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet
  $ipconfig = New-AzureRmVirtualNetworkGatewayIpConfig -Name "gwIpConfig" -SubnetId $gwsubnet.Id -PublicIpAddressId $pip.Id
  New-AzureRmVirtualNetworkGateway -Name "Gateway1" -ResourceGroupName "ForcedTunneling" -Location "North Europe" -IpConfigurations $ipconfig -GatewayType Vpn -VpnType RouteBased -GatewaySku VpnGw1 -GatewayDefaultSite $lng1 -EnableBgp $false
  ```
7. Estabelece as ligações de VPN de Site para Site.

  ```powershell
  $gateway = Get-AzureRmVirtualNetworkGateway -Name "Gateway1" -ResourceGroupName "ForcedTunneling"
  $lng1 = Get-AzureRmLocalNetworkGateway -Name "DefaultSiteHQ" -ResourceGroupName "ForcedTunneling" 
  $lng2 = Get-AzureRmLocalNetworkGateway -Name "Branch1" -ResourceGroupName "ForcedTunneling" 
  $lng3 = Get-AzureRmLocalNetworkGateway -Name "Branch2" -ResourceGroupName "ForcedTunneling" 
  $lng4 = Get-AzureRmLocalNetworkGateway -Name "Branch3" -ResourceGroupName "ForcedTunneling" 
    
  New-AzureRmVirtualNetworkGatewayConnection -Name "Connection1" -ResourceGroupName "ForcedTunneling" -Location "North Europe" -VirtualNetworkGateway1 $gateway -LocalNetworkGateway2 $lng1 -ConnectionType IPsec -SharedKey "preSharedKey"
  New-AzureRmVirtualNetworkGatewayConnection -Name "Connection2" -ResourceGroupName "ForcedTunneling" -Location "North Europe" -VirtualNetworkGateway1 $gateway -LocalNetworkGateway2 $lng2 -ConnectionType IPsec -SharedKey "preSharedKey"
  New-AzureRmVirtualNetworkGatewayConnection -Name "Connection3" -ResourceGroupName "ForcedTunneling" -Location "North Europe" -VirtualNetworkGateway1 $gateway -LocalNetworkGateway2 $lng3 -ConnectionType IPsec -SharedKey "preSharedKey"
  New-AzureRmVirtualNetworkGatewayConnection -Name "Connection4" -ResourceGroupName "ForcedTunneling" -Location "North Europe" -VirtualNetworkGateway1 $gateway -LocalNetworkGateway2 $lng4 -ConnectionType IPsec -SharedKey "preSharedKey"
    
  Get-AzureRmVirtualNetworkGatewayConnection -Name "Connection1" -ResourceGroupName "ForcedTunneling"
  ```
