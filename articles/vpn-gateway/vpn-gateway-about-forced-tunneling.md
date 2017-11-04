---
title: "Configurar a imposição do túnel para ligações do Azure Site a Site: clássico | Microsoft Docs"
description: "Como redirecionar ou \"forçar\" todo o tráfego vinculado à Internet voltar à sua localização no local."
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: timlt
editor: 
tags: azure-service-management
ms.assetid: 5c0177f1-540c-4474-9b80-f541fa44240b
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/01/2017
ms.author: cherylmc
ms.openlocfilehash: 79bf6892c823da282c3e763921e830f986419854
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="configure-forced-tunneling-using-the-classic-deployment-model"></a>Configurar o túnel forçado utilizando o modelo de implementação clássico

Imposição do túnel permite-lhe redirecionamento ou "forçar" todo o tráfego vinculado à Internet de volta para a sua localização no local através de um túnel VPN Site a Site para inspeção e auditoria. Este é um requisito de segurança críticas para TI empresariais de maioria das políticas. Sem a imposição do túnel, o tráfego vinculado à Internet do seu VMs no Azure serão atravessar sempre da infraestrutura de rede do Azure diretamente saída à Internet, sem a opção para permitir a inspecionar ou o tráfego de auditoria. Acesso à Internet não autorizado, potencialmente, pode levar a divulgação de informações ou outros tipos de falhas de segurança.

[!INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)]

Este artigo explica como configurar forçado túnel para redes virtuais criadas com o modelo de implementação clássica. Imposição do túnel pode ser configurado utilizando o PowerShell, não através do portal. Se pretende configurar a imposição do túnel para o modelo de implementação Resource Manager, selecione clássico artigo seguinte na lista pendente:

> [!div class="op_single_selector"]
> * [PowerShell – Clássica](vpn-gateway-about-forced-tunneling.md)
> * [PowerShell – Resource Manager](vpn-gateway-forced-tunneling-rm.md)
> 
> 

## <a name="requirements-and-considerations"></a>Requisitos e considerações
Imposição do túnel no Azure está configurado através de rotas definidas pelo utilizador da rede virtual (UDR). Redirecionar o tráfego para um site no local é expresso como uma rota predefinida para o gateway de VPN do Azure. A secção seguinte apresenta uma lista de limitação atual de encaminhamento e tabela de rotas para uma rede Virtual do Azure:

* Cada sub-rede da rede virtual tem uma tabela de encaminhamento incorporada, do sistema. A tabela de encaminhamento do sistema tem os seguintes três grupos de rotas:

  * **Rotas de VNet locais:** diretamente para o destino VMs na mesma rede virtual.
  * **No local rotas:** para o VPN gateway do Azure.
  * **Rota predefinida:** diretamente à Internet. Pacotes destinados a entidades para os endereços IP privados não abrangidos pelas rotas de dois anteriores serão ignorados.
* Com o lançamento de rotas definidas pelo utilizador, pode criar uma tabela de encaminhamento para adicionar uma rota predefinida e, em seguida, associar a tabela de encaminhamento para sua subnet(s) VNet para ativar a imposição do túnel nessas sub-redes.
* Tem de definir um "site predefinido" entre os sites locais em vários locais ligado à rede virtual.
* Imposição do túnel tem de ser associado a uma VNet com um gateway VPN encaminhamento dinâmico (não um gateway estático).
* ExpressRoute imposição do túnel não está configurada através desta mecanismo, mas em vez disso, é ativado por uma rota predefinida através de sessões de peering de BGP de ExpressRoute de publicidade. Consulte o [documentação do ExpressRoute](https://azure.microsoft.com/documentation/services/expressroute/) para obter mais informações.

## <a name="configuration-overview"></a>Descrição geral de configuração
No exemplo seguinte, o front-end sub-rede não está a ser forçada em túnel. As cargas de trabalho da sub-rede do front-end podem continuar a aceitar e responder a pedidos de cliente da Internet diretamente. As sub-redes de camada média dimensão que e back-end são forçadas em túnel. Quaisquer ligações de saída destas duas sub-redes para a Internet serão forçadas ou redirecionadas novamente para um site no local através de um dos túneis S2S VPN.

Isto permite-lhe restringir e inspecionar o acesso à Internet das suas máquinas virtuais ou serviços em nuvem na Azure, ao continuar ativar a arquitetura do serviço de várias camadas necessárias. Também pode aplicar a imposição do túnel para as redes virtuais completos se existirem não cargas de trabalho para a Internet nas suas redes virtuais.

![Túnel Forçado](./media/vpn-gateway-about-forced-tunneling/forced-tunnel.png)

## <a name="before-you-begin"></a>Antes de começar
Antes de iniciar a configuração, verifique se tem os seguintes itens.

* Uma subscrição do Azure. Se ainda não tiver uma subscrição do Azure, pode ativar os [Benefícios de subscritor do MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) ou inscrever-se numa [conta gratuita](https://azure.microsoft.com/pricing/free-trial/).
* Uma rede virtual configurada. 
* A versão mais recente dos cmdlets do PowerShell do Azure. Veja [Como instalar e configurar o Azure PowerShell](/powershell/azure/overview) para obter mais informações sobre como instalar os cmdlets PowerShell.

## <a name="configure-forced-tunneling"></a>Configurar túnel forçado
O procedimento seguinte irá ajudar a especificar a imposição do túnel para uma rede virtual. Os passos de configuração correspondem ao ficheiro de configuração de rede VNet.

```
<VirtualNetworkSite name="MultiTier-VNet" Location="North Europe">
     <AddressSpace>
      <AddressPrefix>10.1.0.0/16</AddressPrefix>
        </AddressSpace>
        <Subnets>
          <Subnet name="Frontend">
            <AddressPrefix>10.1.0.0/24</AddressPrefix>
          </Subnet>
          <Subnet name="Midtier">
            <AddressPrefix>10.1.1.0/24</AddressPrefix>
          </Subnet>
          <Subnet name="Backend">
            <AddressPrefix>10.1.2.0/23</AddressPrefix>
          </Subnet>
          <Subnet name="GatewaySubnet">
            <AddressPrefix>10.1.200.0/28</AddressPrefix>
          </Subnet>
        </Subnets>
        <Gateway>
          <ConnectionsToLocalNetwork>
            <LocalNetworkSiteRef name="DefaultSiteHQ">
              <Connection type="IPsec" />
            </LocalNetworkSiteRef>
            <LocalNetworkSiteRef name="Branch1">
              <Connection type="IPsec" />
            </LocalNetworkSiteRef>
            <LocalNetworkSiteRef name="Branch2">
              <Connection type="IPsec" />
            </LocalNetworkSiteRef>
            <LocalNetworkSiteRef name="Branch3">
              <Connection type="IPsec" />
            </LocalNetworkSiteRef>
        </Gateway>
      </VirtualNetworkSite>
    </VirtualNetworkSite>
```

Neste exemplo, a rede virtual 'MultiTier VNet' tem três sub-redes: 'Front-end', 'Midtier' e 'Back-end' sub-redes, com ligações quatro locais cruzado: 'DefaultSiteHQ' e três ramos. 

Os passos irão definir o DefaultSiteHQ como a ligação de site predefinido para a imposição do túnel e configurar o Midtier e sub-redes de back-end para utilizar a imposição do túnel.

1. Crie uma tabela de encaminhamento. Utilize o cmdlet seguinte para criar a tabela de rota.

  ```powershell
  New-AzureRouteTable –Name "MyRouteTable" –Label "Routing Table for Forced Tunneling" –Location "North Europe"
  ```
2. Adicione uma rota predefinida para a tabela de encaminhamento. 

  O exemplo seguinte adiciona uma rota predefinida para a tabela de encaminhamento criada no passo 1. Tenha em atenção que a rota apenas suportada é o prefixo de destino de "0.0.0.0/0" para o NextHop "O VPNGateway".

  ```powershell
  Get-AzureRouteTable -Name "MyRouteTable" | Set-AzureRoute –RouteTable "MyRouteTable" –RouteName "DefaultRoute" –AddressPrefix "0.0.0.0/0" –NextHopType VPNGateway
  ```
3. Associe a tabela de encaminhamento para as sub-redes. 

  Depois de uma tabela de encaminhamento é criada e adicionado uma rota, utilize o exemplo seguinte para adicionar ou associar a tabela de rotas para uma sub-rede da VNet. O exemplo adiciona a tabela de rotas "MyRouteTable" para as sub-redes da VNet MultiTier VNet Midtier e back-end.

  ```powershell
  Set-AzureSubnetRouteTable -VirtualNetworkName "MultiTier-VNet" -SubnetName "Midtier" -RouteTableName "MyRouteTable"
  Set-AzureSubnetRouteTable -VirtualNetworkName "MultiTier-VNet" -SubnetName "Backend" -RouteTableName "MyRouteTable"
  ```
4. Atribua um site predefinido para a imposição do túnel. 

  No passo anterior, os scripts de cmdlet de exemplo criado a tabela de encaminhamento em associadas a tabela de rotas para duas das sub-redes da VNet. O passo restantes consiste em selecionar um site local entre as ligações de vários sites de rede virtual como o site predefinido ou túnel.

  ```powershell
  $DefaultSite = @("DefaultSiteHQ")
  Set-AzureVNetGatewayDefaultSite –VNetName "MultiTier-VNet" –DefaultSite "DefaultSiteHQ"
  ```

## <a name="additional-powershell-cmdlets"></a>Cmdlets do PowerShell adicionais
### <a name="to-delete-a-route-table"></a>Para eliminar uma tabela de rota

```powershell
Remove-AzureRouteTable -Name <routeTableName>
```
  
### <a name="to-list-a-route-table"></a>A lista uma tabela de rota

```powershell
Get-AzureRouteTable [-Name <routeTableName> [-DetailLevel <detailLevel>]]
```

### <a name="to-delete-a-route-from-a-route-table"></a>Para eliminar uma rota de uma tabela de rota

```powershell
Remove-AzureRouteTable –Name <routeTableName>
```

### <a name="to-remove-a-route-from-a-subnet"></a>Para remover uma rota de sub-rede

```powershell
Remove-AzureSubnetRouteTable –VirtualNetworkName <virtualNetworkName> -SubnetName <subnetName>
```

### <a name="to-list-the-route-table-associated-with-a-subnet"></a>Para listar a tabela de rota associada uma sub-rede

```powershell
Get-AzureSubnetRouteTable -VirtualNetworkName <virtualNetworkName> -SubnetName <subnetName>
```

### <a name="to-remove-a-default-site-from-a-vnet-vpn-gateway"></a>Para remover um site predefinido de um gateway de VPN de VNet

```powershell
Remove-AzureVnetGatewayDefaultSite -VNetName <virtualNetworkName>
```