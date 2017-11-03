---
title: "Ligar redes virtuais clássicas do Azure Resource Manager VNets: PowerShell | Microsoft Docs"
description: "Saiba como criar uma ligação VPN entre clássico VNets e as VNets do Resource Manager com o Gateway de VPN e o PowerShell"
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: timlt
editor: 
tags: azure-service-management,azure-resource-manager
ms.assetid: f17c3bf0-5cc9-4629-9928-1b72d0c9340b
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/21/2017
ms.author: cherylmc
ms.openlocfilehash: da5bddba3a1fad74b2ee08fd2f34d1b01c7345c8
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="connect-virtual-networks-from-different-deployment-models-using-powershell"></a>Ligar redes virtuais a partir de modelos de implementação diferentes com o PowerShell



Este artigo mostra como ligar VNets clássicas para VNets do Resource Manager para permitir que os recursos localizados nos modelos de implementação separado para comunicar entre si. Os passos neste artigo utilizam o PowerShell, mas também pode criar esta configuração no portal do Azure ao selecionar o artigo desta lista.

> [!div class="op_single_selector"]
> * [Portal](vpn-gateway-connect-different-deployment-models-portal.md)
> * [PowerShell](vpn-gateway-connect-different-deployment-models-powershell.md)
> 
> 

Ligar uma VNet clássica a uma VNet do Resource Manager é semelhante à ligação VNet a uma localização do site no local. Ambos os tipos de conetividade utilizam um gateway de VPN para fornecer um túnel seguro através de IPsec/IKE. Pode criar uma ligação entre VNets que estão em subscrições diferentes e em regiões diferentes. Também pode ligar VNets que já tem ligações a redes no local, enquanto o gateway que tenham sido configurados com é dinâmico ou baseado na rota. Para obter mais informações sobre ligações de VNet a VNet, consulte [FAQ sobre VNet para VNet](#faq) no final deste artigo. 

Se as suas VNets estiverem na mesma região, poderá pretender em vez disso, considere a ligá-las a utilização de VNet Peering. O VNet peering não utiliza um gateway de VPN. Para obter mais informações, veja [VNet peering](../virtual-network/virtual-network-peering-overview.md). 

## <a name="before"></a>Antes de começar

Os seguintes passos guiá-lo as definições necessárias para configurar um gateway dinâmico ou baseado na rota para cada VNet e criar uma ligação VPN entre os gateways. Esta configuração não suporta os gateways estáticos ou baseada em políticas.

### <a name="pre"></a>Pré-requisitos

* Ambas as VNets já foram criadas.
* Os intervalos de endereços para as VNets não sobrepor-se entre si, ou se sobrepõe a qualquer um dos intervalos de outras ligações que os gateways podem ser ligados à.
* Instalar os cmdlets do PowerShell mais recentes. Consulte [como instalar e configurar o Azure PowerShell](/powershell/azure/overview) para obter mais informações. Certifique-se de que instala o serviço de gestão (SM) e os cmdlets do Gestor de recursos (RM). 

### <a name="exampleref"></a>Definições de exemplo

Pode utilizar estes valores para criar um ambiente de teste ou consultá-los para compreender melhor os exemplos neste artigo.

**Definições da VNet clássicas**

Nome da VNet = ClassicVNet <br>
Localização = EUA oeste <br>
Espaços de endereços de rede virtual = 10.0.0.0/24 <br>
Sub-rede 1 = 10.0.0.0/27 <br>
GatewaySubnet = 10.0.0.32/29 <br>
Nome da rede local = RMVNetLocal <br>
GatewayType = DynamicRouting

**Definições da VNet do Resource Manager**

Nome da VNet = RMVNet <br>
Grupo de recursos = RG1 <br>
Espaços de endereços IP de rede virtual = 192.168.0.0/16 <br>
Sub-rede 1 = 192.168.1.0/24 <br>
GatewaySubnet = 192.168.0.0/26 <br>
Localização = EUA leste <br>
Nome IP público do gateway = gwpip <br>
Gateway de rede local = ClassicVNetLocal <br>
Nome do Gateway de rede virtual = RMGateway <br>
Configuração de endereçamento de IP do gateway = gwipconfig

## <a name="createsmgw"></a>Secção 1 – configurar a VNet clássica
### <a name="1-download-your-network-configuration-file"></a>1. Transfira o ficheiro de configuração de rede
1. Inicie sessão sua conta do Azure na consola do PowerShell com direitos elevados. O seguinte cmdlet pede-lhe as credenciais de início de sessão para a sua conta do Azure. Após iniciar sessão, são transferidas as definições da conta para que fiquem disponíveis para o Azure PowerShell. Utilize os cmdlets do PowerShell de SM para concluir esta parte da configuração.

  ```powershell
  Add-AzureAccount
  ```
2. Exporte o ficheiro de configuração de rede do Azure executando o seguinte comando. Pode alterar a localização do ficheiro a exportar para uma localização diferente, se necessário.

  ```powershell
  Get-AzureVNetConfig -ExportToFile C:\AzureNet\NetworkConfig.xml
  ```
3. Abra o ficheiro. XML que transferiu editá-lo. Para obter um exemplo do ficheiro de configuração de rede, consulte o [esquema de configuração de rede](https://msdn.microsoft.com/library/jj157100.aspx).

### <a name="2-verify-the-gateway-subnet"></a>2. Certifique-se a sub-rede do gateway
No **VirtualNetworkSites** elemento, adicionar uma sub-rede de gateway a sua VNet, se ainda não já foi criado. Ao trabalhar com o ficheiro de configuração de rede, a sub-rede do gateway deve ter o nome "GatewaySubnet" ou o Azure não é possível reconhecer e utilizá-la como uma sub-rede de gateway.

[!INCLUDE [vpn-gateway-no-nsg-include](../../includes/vpn-gateway-no-nsg-include.md)]

**Exemplo:**

    <VirtualNetworkSites>
      <VirtualNetworkSite name="ClassicVNet" Location="West US">
        <AddressSpace>
          <AddressPrefix>10.0.0.0/24</AddressPrefix>
        </AddressSpace>
        <Subnets>
          <Subnet name="Subnet-1">
            <AddressPrefix>10.0.0.0/27</AddressPrefix>
          </Subnet>
          <Subnet name="GatewaySubnet">
            <AddressPrefix>10.0.0.32/29</AddressPrefix>
          </Subnet>
        </Subnets>
      </VirtualNetworkSite>
    </VirtualNetworkSites>

### <a name="3-add-the-local-network-site"></a>3. Adicionar o site de rede local
Adicionar o site de rede local representa a VNet RM ao qual pretende ligar. Adicionar um **LocalNetworkSites** elemento para que o ficheiro se ainda não existir. Neste momento na configuração, o VPNGatewayAddress pode ser qualquer endereço IP público válido porque é ainda não criou o gateway para a VNet do Resource Manager. Assim que criamos o gateway, iremos substitua este endereço IP do marcador de posição o endereço IP público correto que tenha sido atribuído ao RM gateway.

    <LocalNetworkSites>
      <LocalNetworkSite name="RMVNetLocal">
        <AddressSpace>
          <AddressPrefix>192.168.0.0/16</AddressPrefix>
        </AddressSpace>
        <VPNGatewayAddress>13.68.210.16</VPNGatewayAddress>
      </LocalNetworkSite>
    </LocalNetworkSites>

### <a name="4-associate-the-vnet-with-the-local-network-site"></a>4. Associar a VNet com o site de rede local
Nesta secção, iremos especificar o site de rede local que pretende ligar a VNet. Neste caso, é a VNet do Resource Manager que referenciados anteriormente. Certifique-se os nomes de corresponder. Este passo não criar um gateway. Especifica a rede local que o gateway irá ligar ao.

        <Gateway>
          <ConnectionsToLocalNetwork>
            <LocalNetworkSiteRef name="RMVNetLocal">
              <Connection type="IPsec" />
            </LocalNetworkSiteRef>
          </ConnectionsToLocalNetwork>
        </Gateway>

### <a name="5-save-the-file-and-upload"></a>5. Guarde o ficheiro e carregar
Guarde o ficheiro e, em seguida, importá-lo para o Azure executando o seguinte comando. Certifique-se de que altera o caminho do ficheiro conforme necessário para o seu ambiente.

```powershell
Set-AzureVNetConfig -ConfigurationPath C:\AzureNet\NetworkConfig.xml
```

Irá ver um resultado semelhante que mostra que a importação foi concluída com êxito.

        OperationDescription        OperationId                      OperationStatus                                                
        --------------------        -----------                      ---------------                                                
        Set-AzureVNetConfig        e0ee6e66-9167-cfa7-a746-7casb9    Succeeded 

### <a name="6-create-the-gateway"></a>6. Criar o gateway

Antes de executar este exemplo, consulte a rede ficheiro de configuração que transferiu para os nomes exatos que espera do Azure para ver. O ficheiro de configuração de rede contém os valores para as redes virtuais clássicas. Por vezes, os nomes das VNets clássicas são alterados no ficheiro de configuração de rede quando criar definições da VNet clássicas no portal do Azure devido às diferenças em modelos de implementação. Por exemplo, se utilizou o portal do Azure para criar uma clássica VNet com o nome 'VNet clássica' e criou um grupo de recursos com o nome 'ClassicRG', o nome que está contido no ficheiro de configuração de rede é convertido para 'Grupo ClassicRG VNet clássica'. Quando especificar o nome de uma VNet que contiver espaços, utilize aspas à volta o valor.


Utilize o seguinte exemplo para criar um gateway de encaminhamento dinâmico:

```powershell
New-AzureVNetGateway -VNetName ClassicVNet -GatewayType DynamicRouting
```

Pode verificar o estado do gateway, utilizando o **Get-AzureVNetGateway** cmdlet.

## <a name="creatermgw"></a>Secção 2 - configurar o gateway de RM VNet
Para criar um gateway de VPN para a VNet RM, siga as instruções seguintes. Não inicie os passos até depois de ter obtido o endereço IP público para o gateway de VNet clássica. 

1. Inicie sessão sua conta do Azure na consola do PowerShell. O seguinte cmdlet pede-lhe as credenciais de início de sessão para a sua conta do Azure. Após iniciar sessão, as definições da sua conta são transferidas para que fiquem disponíveis para o Azure PowerShell.

  ```powershell
  Login-AzureRmAccount
  ``` 
   
  Obter uma lista das suas subscrições do Azure, se tiver mais do que uma subscrição.

  ```powershell
  Get-AzureRmSubscription
  ```
   
  Especifique a subscrição que pretende utilizar.

  ```powershell
  Select-AzureRmSubscription -SubscriptionName "Name of subscription"
  ```
2. Crie um gateway de rede local. Numa rede virtual, o gateway de rede local refere-se normalmente à sua localização no local. Neste caso, o gateway de rede local refere-se a sua VNet clássica. Atribua um nome pelo qual Azure pode fazem referência ao mesmo e também especificar o prefixo de espaço de endereço. O Azure utiliza o prefixo do endereço IP que especificar para identificar o tráfego a enviar para a sua localização no local. Se for necessário ajustar as informações aqui mais tarde, antes de criar o gateway, pode modificar os valores e execute novamente o exemplo.
   
   **-Nome** é o nome que pretende atribuir para fazer referência ao gateway de rede local.<br>
   **-AddressPrefix** é o espaço de endereço para a sua VNet clássica.<br>
   **-GatewayIpAddress** é o endereço IP público do gateway da VNet clássica. Não se esqueça de alterar o exemplo seguinte para refletir o endereço IP correto.<br>

  ```powershell
  New-AzureRmLocalNetworkGateway -Name ClassicVNetLocal `
  -Location "West US" -AddressPrefix "10.0.0.0/24" `
  -GatewayIpAddress "n.n.n.n" -ResourceGroupName RG1
  ```
3. Solicitar um endereço IP público para ser atribuído ao gateway de rede virtual para a VNet do Resource Manager. Não é possível especificar o endereço IP que pretende utilizar. O endereço IP é dinamicamente atribuído ao gateway de rede virtual. No entanto, não significa que o endereço IP é alterado. O único momento em que as alterações de endereço IP de gateway de rede virtual é quando o gateway é eliminado e recriado. Não altera em redimensionar, ao repor ou outra manutenção/atualização interna do gateway.

  Neste passo, iremos também definir uma variável que é utilizada num passo posterior.

  ```powershell
  $ipaddress = New-AzureRmPublicIpAddress -Name gwpip `
  -ResourceGroupName RG1 -Location 'EastUS' `
  -AllocationMethod Dynamic
  ```

4. Certifique-se de que a rede virtual tem uma sub-rede de gateway. Não se existir nenhuma sub-rede de gateway, tem de adicionar um. Certifique-se a sub-rede do gateway é denominada *GatewaySubnet*.
5. Obter a sub-rede utilizada para o gateway, executando o seguinte comando. Neste passo, iremos também definir uma variável a ser utilizada no próximo passo.
   
   **-Nome** é o nome da VNet do Resource Manager.<br>
   **-ResourceGroupName** é o grupo de recursos que a VNet está associada. A sub-rede do gateway tem de existir para esta VNet e tem de ter um nome *GatewaySubnet* funcione corretamente.<br>

  ```powershell
  $subnet = Get-AzureRmVirtualNetworkSubnetConfig -Name GatewaySubnet `
  -VirtualNetwork (Get-AzureRmVirtualNetwork -Name RMVNet -ResourceGroupName RG1)
  ``` 

6. Crie a configuração de endereçamento de IP de gateway. A configuração do gateway define a sub-rede e o endereço IP público a utilizar. Utilize o exemplo seguinte para criar a configuração do gateway.

  Neste passo, o **- SubnetId** e **- PublicIpAddressId** parâmetros têm de ser transmitidos a propriedade de id da sub-rede e objetos de endereço IP, respetivamente. Não é possível utilizar uma cadeia simple. Estas variáveis são definidas no passo para pedir um IP público e o passo para obter a sub-rede.

  ```powershell
  $gwipconfig = New-AzureRmVirtualNetworkGatewayIpConfig `
  -Name gwipconfig -SubnetId $subnet.id `
  -PublicIpAddressId $ipaddress.id
  ```
7. Crie o gateway de rede virtual do Gestor de recursos, executando o seguinte comando. O `-VpnType` tem de ser *RouteBased*. Pode demorar 45 minutos ou mais para o gateway criar.

  ```powershell
  New-AzureRmVirtualNetworkGateway -Name RMGateway -ResourceGroupName RG1 `
  -Location "EastUS" -GatewaySKU Standard -GatewayType Vpn `
  -IpConfigurations $gwipconfig `
  -EnableBgp $false -VpnType RouteBased
  ```
8. Copie o endereço IP público depois de criar o gateway VPN. Utilizá-la quando configurar as definições de rede local para a sua VNet clássica. Pode utilizar o cmdlet seguinte para obter o endereço IP público. O endereço IP público está listado no retorno como *IpAddress*.

  ```powershell
  Get-AzureRmPublicIpAddress -Name gwpip -ResourceGroupName RG1
  ```

## <a name="localsite"></a>Secção 3 - modifique as definições de local site VNet clássicas

Nesta secção, pode trabalha com a VNet clássica. Substituir o endereço IP do marcador de posição que utilizou quando especificar as definições de local site que serão utilizadas para ligar ao gateway de VNet do Resource Manager. 

1. Exporte o ficheiro de configuração de rede.

  ```powershell
  Get-AzureVNetConfig -ExportToFile C:\AzureNet\NetworkConfig.xml
  ```
2. Com um editor de texto, modifique o valor para VPNGatewayAddress. Substituir o endereço IP do marcador de posição com o endereço IP público do gateway do Resource Manager e, em seguida, guarde as alterações.

  ```
  <VPNGatewayAddress>13.68.210.16</VPNGatewayAddress>
  ```
3. Importe o ficheiro de configuração de rede modificado para o Azure.

  ```powershell
  Set-AzureVNetConfig -ConfigurationPath C:\AzureNet\NetworkConfig.xml
  ```

## <a name="connect"></a>Secção 4 – criar uma ligação entre os gateways
Criar uma ligação entre os gateways requer o PowerShell. Terá de adicionar a sua conta do Azure para utilizar a versão clássica dos cmdlets do PowerShell. Para tal, utilize **Add-AzureAccount**.

1. Na consola do PowerShell, defina a chave partilhada. Antes de executar os cmdlets, consulte a rede ficheiro de configuração que transferiu para os nomes exatos que espera do Azure para ver. Quando especificar o nome de uma VNet que contiver espaços, utilize único entre aspas à volta o valor.<br><br>No exemplo seguinte, **- VNetName** é o nome da VNet clássica e **- LocalNetworkSiteName** é o nome especificado para o site de rede local. O **- SharedKey** é um valor que geram e especificar. No exemplo, utilizámos 'abc123', mas pode gerar e utilizar algo mais complexo. O mais importante é que o valor que especificar aqui tem de ser o mesmo valor que especificou no passo seguinte, quando criar a ligação. Deve mostrar o retorno **Estado: bem-sucedida**.

  ```powershell
  Set-AzureVNetGatewayKey -VNetName ClassicVNet `
  -LocalNetworkSiteName RMVNetLocal -SharedKey abc123
  ```
2. Crie a ligação VPN, executando os seguintes comandos:
   
  Defina as variáveis.

  ```powershell
  $vnet01gateway = Get-AzureRMLocalNetworkGateway -Name ClassicVNetLocal -ResourceGroupName RG1
  $vnet02gateway = Get-AzureRmVirtualNetworkGateway -Name RMGateway -ResourceGroupName RG1
  ```
   
  Crie a ligação. Tenha em atenção que o **- ConnectionType** é IPsec, não Vnet2Vnet.

  ```powershell
  New-AzureRmVirtualNetworkGatewayConnection -Name RM-Classic -ResourceGroupName RG1 `
  -Location "East US" -VirtualNetworkGateway1 `
  $vnet02gateway -LocalNetworkGateway2 `
  $vnet01gateway -ConnectionType IPsec -RoutingWeight 10 -SharedKey 'abc123'
  ```

## <a name="verify"></a>Secção 5 - Certifique-se as suas ligações

### <a name="to-verify-the-connection-from-your-classic-vnet-to-your-resource-manager-vnet"></a>Para verificar a ligação da sua VNet clássica a vnet do Resource Manager

#### <a name="powershell"></a>PowerShell

[!INCLUDE [vpn-gateway-verify-connection-ps-classic](../../includes/vpn-gateway-verify-connection-ps-classic-include.md)]

#### <a name="azure-portal"></a>Portal do Azure

[!INCLUDE [vpn-gateway-verify-connection-azureportal-classic](../../includes/vpn-gateway-verify-connection-azureportal-classic-include.md)]


### <a name="to-verify-the-connection-from-your-resource-manager-vnet-to-your-classic-vnet"></a>Para verificar a ligação de VNet do Resource Manager para a sua VNet clássica

#### <a name="powershell"></a>PowerShell

[!INCLUDE [vpn-gateway-verify-ps-rm](../../includes/vpn-gateway-verify-connection-ps-rm-include.md)]

#### <a name="azure-portal"></a>Portal do Azure

[!INCLUDE [vpn-gateway-verify-connection-portal-rm](../../includes/vpn-gateway-verify-connection-portal-rm-include.md)]

## <a name="faq"></a>FAQ da ligação VNet a VNet

[!INCLUDE [vpn-gateway-vnet-vnet-faq](../../includes/vpn-gateway-faq-vnet-vnet-include.md)]

