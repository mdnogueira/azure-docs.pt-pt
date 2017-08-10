---
title: 'Ligar a rede no local a uma rede virtual do Azure: Rede de VPNs: PowerShell | Microsoft Docs'
description: "Passos para criar uma ligação IPsec da sua rede no local a uma rede virtual do Azure através da Internet pública. Estes passos ajudam-no a criar uma ligação de Gateway de Rede de VPNs em vários sites com o PowerShell."
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: fcc2fda5-4493-4c15-9436-84d35adbda8e
ms.service: vpn-gateway
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/09/2017
ms.author: cherylmc
ms.translationtype: HT
ms.sourcegitcommit: 9afd12380926d4e16b7384ff07d229735ca94aaa
ms.openlocfilehash: 9edaa81111d9439bfbad4775e49c3e29454ad31f
ms.contentlocale: pt-pt
ms.lasthandoff: 07/15/2017

---
# <a name="create-a-vnet-with-a-site-to-site-vpn-connection-using-powershell"></a>Criar uma VNet com uma ligação de Rede de VPNs com o PowerShell

Este artigo mostra-lhe como utilizar o PowerShell para criar uma ligação de gateway de VPN de Site para Site a partir da sua rede no local para a VNet. Os passos deste artigo aplicam-se ao modelo de implementação Resource Manager. Também pode criar esta configuração ao utilizar uma ferramenta de implementação diferente ou modelo de implementação ao selecionar uma opção diferente da lista seguinte:

> [!div class="op_single_selector"]
> * [Portal do Azure](vpn-gateway-howto-site-to-site-resource-manager-portal.md)
> * [PowerShell](vpn-gateway-create-site-to-site-rm-powershell.md)
> * [CLI](vpn-gateway-howto-site-to-site-resource-manager-cli.md)
> * [Portal do Azure (clássico)](vpn-gateway-howto-site-to-site-classic-portal.md)
> * [Portal clássico (clássico)](vpn-gateway-site-to-site-create.md)
> 
>


Uma ligação de gateway de VPN de Site para Site é utilizada para ligar a sua rede no local a uma rede virtual do Azure através de um túnel VPN IPsec/IKE (IKEv1 ou IKEv2). Este tipo de ligação requer um dispositivo VPN localizado no local que tenha um endereço IP público com acesso exterior atribuído ao mesmo. Para obter mais informações sobre o gateways de VPN, veja [About VPN gateway (Acerca do gateway de VPN)](vpn-gateway-about-vpngateways.md).

![Diagrama da ligação de Gateway de Rede de VPNs em vários sites](./media/vpn-gateway-create-site-to-site-rm-powershell/site-to-site-diagram.png)

## <a name="before-you-begin"></a>Antes de começar

Antes de iniciar a configuração, verifique se cumpre os seguintes critérios:

* Certifique-se de que tem um dispositivo VPN compatível e alguém que o possa configurar. Para obter mais informações sobre os dispositivos VPN compatíveis e a configuração do dispositivo, consulte [About VPN Devices (Acerca dos Dispositivos VPN)](vpn-gateway-about-vpn-devices.md).
* Verifique se tem um endereço IP IPv4 público com acesso exterior para o seu dispositivo VPN. Este endereço IP não pode estar localizado atrás de um NAT.
* Se não estiver familiarizado com os intervalos de endereços IP localizados na configuração de rede no local, tem de se coordenar com alguém que consiga fornecer esses detalhes. Ao criar esta configuração, tem de especificar prefixos de intervalo de endereços IP que o Azure irá encaminhar para a sua localização no local. Nenhuma das sub-redes da rede local pode sobrepor as sub-redes de rede virtual a que pretende ligar.
* Instale a versão mais recente dos cmdlets do PowerShell do Azure Resource Manager. Os cmdlets do PowerShell são atualizados frequentemente e, regra geral, tem de os atualizar para poder tirar partido das últimas funcionalidades. Se não os atualizar, os valores especificados poderão falhar. Veja [How to install and configure Azure PowerShell](/powershell/azure/overview) (Como instalar e configurar o Azure PowerShell) para obter mais informações sobre como transferir e instalar os cmdlets do PowerShell.

### <a name="example-values"></a>Valores de exemplo

Os exemplos neste artigo utilizam os seguintes valores. Pode utilizar estes valores para criar um ambiente de teste ou consultá-los para compreender melhor os exemplos neste artigo.

```
#Example values

VnetName                = TestVNet1
ResourceGroup           = TestRG1
Location                = East US 
AddressSpace            = 10.11.0.0/16 
SubnetName              = Subnet1 
Subnet                  = 10.11.1.0/28 
GatewaySubnet           = 10.11.0.0/27
LocalNetworkGatewayName = Site2
LNG Public IP           = <VPN device IP address> 
Local Address Prefixes  = 10.0.0.0/24, 20.0.0.0/24
Gateway Name            = VNet1GW
PublicIP                = VNet1GWIP
Gateway IP Config       = gwipconfig1 
VPNType                 = RouteBased 
GatewayType             = Vpn 
ConnectionName          = VNet1toSite2

```


## <a name="Login"></a>1. Estabelecer a ligação à subscrição

[!INCLUDE [PowerShell login](../../includes/vpn-gateway-ps-login-include.md)]

## <a name="VNet"></a>2. Criar uma rede virtual e uma sub-rede de gateway

Se ainda não tiver uma rede virtual, crie uma. Quando criar uma rede virtual, confirme que os espaços de endereços que especificar não se sobrepõem a nenhum dos espaços de endereços que tem na sua rede no local.

[!INCLUDE [About gateway subnets](../../includes/vpn-gateway-about-gwsubnet-include.md)]

[!INCLUDE [No NSG warning](../../includes/vpn-gateway-no-nsg-include.md)]

### <a name="to-create-a-virtual-network-and-a-gateway-subnet"></a>Para criar uma rede virtual e uma sub-rede de gateway

Este exemplo cria uma rede virtual e uma sub-rede de gateway. Se já tiver uma rede virtual à qual precisa de adicionar uma sub-rede de gateway, veja [Para adicionar uma sub-rede de gateway a uma rede virtual que já criou](#gatewaysubnet).

Criar um grupo de recursos:

```powershell
New-AzureRmResourceGroup -Name TestRG1 -Location 'East US'
```

Criar a sua rede virtual.

1. Defina as variáveis.

  ```powershell
  $subnet1 = New-AzureRmVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -AddressPrefix 10.11.0.0/27
  $subnet2 = New-AzureRmVirtualNetworkSubnetConfig -Name 'Subnet1' -AddressPrefix 10.11.1.0/28
  ```
2. Crie a VNet.

  ```powershell
  New-AzureRmVirtualNetwork -Name TestVNet1 -ResourceGroupName TestRG1 `
  -Location 'East US' -AddressPrefix 10.11.0.0/16 -Subnet $subnet1, $subnet2
  ```

### <a name="gatewaysubnet"></a>Para adicionar uma sub-rede de gateway a uma rede virtual já criada

1. Defina as variáveis.

  ```powershell
  $vnet = Get-AzureRmVirtualNetwork -ResourceGroupName TestRG1 -Name TestVet1
  ```
2. Crie a sub-rede de gateway.

  ```powershell
  Add-AzureRmVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -AddressPrefix 10.11.0.0/27 -VirtualNetwork $vnet
  ```
3. Defina a configuração.

  ```powershell
  Set-AzureRmVirtualNetwork -VirtualNetwork $vnet
  ```

## 3. <a name="localnet"></a>Criar o gateway de rede local

O gateway de rede local refere-se normalmente à sua localização no local. Dê um nome ao site pelo qual o Azure se possa referir ao mesmo e especifique o endereço IP do dispositivo VPN no local para o qual vai criar uma ligação. Também pode especificar os prefixos do endereço IP que vai ser encaminhado através do gateway de VPN para o dispositivo VPN. Os prefixos do endereço que especificar são os que estão localizados na sua rede no local. Se a rede no local se alterar, pode atualizar facilmente os prefixos.

Utilize os seguintes valores:

* O *GatewayIPAddress* é o endereço IP do seu dispositivo VPN no local. O dispositivo VPN não pode estar localizado atrás de um NAT.
* O *AddressPrefix* é o seu espaço de endereços no local.

Para adicionar um gateway de rede local com um prefixo de endereço único:

  ```powershell
  New-AzureRmLocalNetworkGateway -Name Site2 -ResourceGroupName TestRG1 `
  -Location 'East US' -GatewayIpAddress '23.99.221.164' -AddressPrefix '10.0.0.0/24'
  ```

Para adicionar um gateway de rede local com vários prefixos de endereço:

  ```powershell
  New-AzureRmLocalNetworkGateway -Name Site2 -ResourceGroupName TestRG1 `
  -Location 'East US' -GatewayIpAddress '23.99.221.164' -AddressPrefix @('10.0.0.0/24','20.0.0.0/24')
  ```

Para modificar prefixos de endereços IP do gateway da sua rede local:<br>
Às vezes, os prefixos de gateway da sua rede local mudam. Os passos a efetuar para modificar os prefixos de endereços IP dependem de se criou ou não uma ligação de gateway de VPN. Veja a secção [Modificar os prefixos de endereços IP para um gateway de rede local](#modify) deste artigo.

## <a name="PublicIP"></a>4. Solicitar um endereço IP Público

Um gateway de VPN deve ter um endereço IP público. Primeira, requeira o recurso de endereço IP e, em seguida, faça referência ao mesmo ao criar o gateway de rede virtual. O endereço IP é dinamicamente atribuído ao recurso quando o gateway de VPN é criado. O Gateway de VPN, atualmente, apenas suporta a alocação de endereços IP públicos *dinâmicos*. Não é possível pedir uma atribuição de endereço IP Público Estático. No entanto, isto não significa que o endereço IP é alterado após ser atribuído ao gateway de VPN. O endereço IP Público só é alterado quando o gateway é eliminado e recriado. Não é alterado ao redimensionar, repor ou ao realizar qualquer outra manutenção/atualização interna do gateway de VPN.

Peça um endereço IP Público que será atribuído ao gateway de VPN da rede virtual.

```powershell
$gwpip= New-AzureRmPublicIpAddress -Name gwpip -ResourceGroupName TestRG1 -Location 'East US' -AllocationMethod Dynamic
```

## <a name="GatewayIPConfig"></a>5. Criar a configuração de endereçamento IP do gateway

A configuração do gateway define a sub-rede e o endereço IP público a utilizar. Utilize o exemplo seguinte para criar a configuração do seu gateway:

```powershell
$vnet = Get-AzureRmVirtualNetwork -Name TestVNet1 -ResourceGroupName TestRG1
$subnet = Get-AzureRmVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -VirtualNetwork $vnet
$gwipconfig = New-AzureRmVirtualNetworkGatewayIpConfig -Name gwipconfig1 -SubnetId $subnet.Id -PublicIpAddressId $gwpip.Id
```

## <a name="CreateGateway"></a>6. Criar o gateway de VPN

Crie o gateway de VPN da rede virtual. A criação de um gateway de VPN pode demorar 45 minutos ou mais a ser concluída.

Utilize os seguintes valores:

* O *-GatewayType* para uma configuração Site a Site é *Vpn*. O tipo de gateway é sempre específico da configuração que estiver a implementar. Por exemplo, outras configurações de gateway poderão exigir o ExpressRoute -GatewayType.
* O *-VpnType* pode ser *RouteBased* (conhecido como Gateway Dinâmico em alguma documentação) ou *PolicyBased* (referido como Gateway Estático em alguma documentação). Para obter mais informações sobre os tipos de gateways de VPN, veja [Acerca dos Gateways de VPN](vpn-gateway-about-vpngateways.md).
* Selecione o SKU do Gateway que pretende utilizar. Existem limitações de configuração para determinados SKUs. Para obter mais informações, veja [SKUs de gateway](vpn-gateway-about-vpn-gateway-settings.md#gwsku). Se obtiver um erro ao criar o gateway de VPN relativamente a -GatewaySku, confirme que instalou a versão mais recente dos cmdlets do PowerShell.

```powershell
New-AzureRmVirtualNetworkGateway -Name VNet1GW -ResourceGroupName TestRG1 `
-Location 'East US' -IpConfigurations $gwipconfig -GatewayType Vpn `
-VpnType RouteBased -GatewaySku VpnGw1
```

## <a name="ConfigureVPNDevice"></a>7. Configurar o dispositivo VPN

As ligações de Site a Site para uma rede no local requerem um dispositivo VPN. Neste passo, configure o seu dispositivo VPN. Quando configurar o dispositivo VPN, irá precisar do seguinte:

- Uma chave partilhada. Esta é a mesma chave partilhada que especifica ao criar a ligação VPN de Site a Site. Nos nossos exemplos, iremos utilizar uma chave partilhada básica. Deve gerar uma chave mais complexa para utilizar.
- O endereço IP Público do gateway de rede virtual. Pode ver o endereço IP público através do portal do Azure, do PowerShell ou da CLI. Para encontrar o endereço IP público do seu gateway de rede virtual com o PowerShell, utilize o exemplo seguinte:

  ```powershell
  Get-AzureRmPublicIpAddress -Name GW1PublicIP -ResourceGroupName TestRG1
  ```

[!INCLUDE [Configure VPN device](../../includes/vpn-gateway-configure-vpn-device-rm-include.md)]


## <a name="CreateConnection"></a>8. Criar a ligação VPN

Em seguida, crie a ligação de Rede de VPNs entre o gateway de rede virtual e o dispositivo VPN. Não se esqueça de substituir os valores pelos seus. A chave partilhada tem de corresponder ao valor utilizado na configuração do dispositivo VPN. Repare que “-ConnectionType” relativo a Site para Site é *IPsec*.

1. Defina as variáveis.
  ```powershell
  $gateway1 = Get-AzureRmVirtualNetworkGateway -Name VNet1GW -ResourceGroupName TestRG1
  $local = Get-AzureRmLocalNetworkGateway -Name Site2 -ResourceGroupName TestRG1
  ```

2. Crie a ligação.
  ```powershell
  New-AzureRmVirtualNetworkGatewayConnection -Name VNet1toSite2 -ResourceGroupName TestRG1 `
  -Location 'East US' -VirtualNetworkGateway1 $gateway1 -LocalNetworkGateway2 $local `
  -ConnectionType IPsec -RoutingWeight 10 -SharedKey 'abc123'
  ```

Após uns breves instantes, a ligação será estabelecida.

## <a name="toverify"></a>9. Verificar a ligação VPN

A verificação da ligação VPN pode ser feita de várias formas.

[!INCLUDE [Verify connection](../../includes/vpn-gateway-verify-connection-ps-rm-include.md)]

## <a name="connectVM"></a>Ligar a uma máquina virtual

[!INCLUDE [Connect to a VM](../../includes/vpn-gateway-connect-vm-s2s-include.md)]


## <a name="modify"></a>Modificar os prefixos de endereços IP de um gateway de rede local

Se os prefixos de endereço IP que pretende encaminhar para a sua localização no local forem alterados, pode modificar o gateway de rede local. São fornecidos dois conjuntos de instruções. As instruções a escolher dependem se já criou ou não a ligação de gateway.

[!INCLUDE [Modify prefixes](../../includes/vpn-gateway-modify-ip-prefix-rm-include.md)]

## <a name="modifygwipaddress"></a>Modificar o endereço IP do gateway de um gateway de rede local

[!INCLUDE [Modify gateway IP address](../../includes/vpn-gateway-modify-lng-gateway-ip-rm-include.md)]

## <a name="next-steps"></a>Passos seguintes

*  Assim que a ligação estiver concluída, pode adicionar máquinas virtuais às redes virtuais. Para obter mais informações, veja [Máquinas Virtuais](https://docs.microsoft.com/azure/#pivot=services&panel=Compute).
* Para obter informações sobre o BGP, veja a [Descrição Geral do BGP](vpn-gateway-bgp-overview.md) e [Como configurar o BGP](vpn-gateway-bgp-resource-manager-ps.md).

