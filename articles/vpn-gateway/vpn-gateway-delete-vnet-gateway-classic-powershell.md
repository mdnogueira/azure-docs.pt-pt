---
title: "Eliminar um gateway de rede virtual: PowerShell: clássico do Azure | Microsoft Docs"
description: "Elimine um gateway de rede virtual com o PowerShell no modelo de implementação clássica."
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: timlt
editor: 
tags: azure-service-management
ms.assetid: 
ms.service: vpn-gateway
ms.devlang: na
ms.topic: 
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/11/2017
ms.author: cherylmc
ms.openlocfilehash: b1bc18307227a728e2bc8fd95e30fdc1cbdb8c59
ms.sourcegitcommit: c7215d71e1cdeab731dd923a9b6b6643cee6eb04
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/17/2017
---
# <a name="delete-a-virtual-network-gateway-using-powershell-classic"></a>Eliminar um gateway de rede virtual através do PowerShell (clássica)
> [!div class="op_single_selector"]
> * [Resource Manager - portal do Azure](vpn-gateway-delete-vnet-gateway-portal.md)
> * [Resource Manager – PowerShell](vpn-gateway-delete-vnet-gateway-powershell.md)
> * [Clássico - PowerShell](vpn-gateway-delete-vnet-gateway-classic-powershell.md)
>
>

Este artigo ajuda-o a eliminar um gateway de VPN no modelo de implementação clássica com o PowerShell. Depois do gateway de rede virtual foi eliminado, modificar o ficheiro de configuração de rede para remover os elementos que já não está a utilizar.

##<a name="connect"></a>Passo 1: Ligar ao Azure

### <a name="1-install-the-latest-powershell-cmdlets"></a>1. Instale os cmdlets do PowerShell mais recentes.

Transfira e instale a versão mais recente dos cmdlets do PowerShell de gestão de serviço do Azure (SM). Para obter mais informações, veja [How to install and configure Azure PowerShell (Como instalar e configurar o Azure PowerShell)](/powershell/azure/overview).

### <a name="2-connect-to-your-azure-account"></a>2. Ligar à sua conta do Azure. 

Abra a consola do PowerShell com direitos elevados e ligue-se à sua conta. Utilize o exemplo seguinte para o ajudar na ligação:

```powershell
Add-AzureAccount
```

## <a name="export"></a>Passo 2: Exportar e veja o ficheiro de configuração de rede

Crie um diretório no seu computador e, em seguida, exporte o ficheiro de configuração de rede para o diretório. Utilize este ficheiro para a vista de ambas as informações de configuração atual bem como para modificar a configuração de rede.

Neste exemplo, o ficheiro de configuração de rede é exportado para C:\AzureNet.

```powershell
Get-AzureVNetConfig -ExportToFile C:\AzureNet\NetworkConfig.xml
```

Abra o ficheiro com um editor de texto e ver o nome para a sua VNet clássica. Quando cria uma VNet no portal do Azure, o nome completo que utiliza o Azure não é visível no portal. Por exemplo, uma VNet que parecem ser com 'ClassicVNet1' o nome no portal do Azure, pode ter um nome de tempo muito no ficheiro de configuração de rede. O nome poderá ter um aspeto semelhante: 'Grupo ClassicRG1 ClassicVNet1'. Os nomes de rede virtual são listados como **' VirtualNetworkSite name ='**. Utilize os nomes no ficheiro de configuração de rede ao executar os cmdlets do PowerShell.

## <a name="delete"></a>Passo 3: Elimine o gateway de rede virtual

Quando eliminar um gateway de rede virtual, todas as ligações para a VNet através do gateway estão desligadas. Se tiver clientes P2S ligados para a VNet, serão desligados sem aviso.

Neste exemplo, elimina o gateway de rede virtual. Certifique-se de que utiliza o nome completo da rede virtual a partir do ficheiro de configuração de rede.

```powershell
Remove-AzureVNetGateway -VNetName "Group ClassicRG1 ClassicVNet1"
```

Se tiver êxito, mostra o retorno:

```
Status : Successful
```

## <a name="modify"></a>Passo 4: Modificar o ficheiro de configuração de rede

Quando eliminar um gateway de rede virtual, o cmdlet não modifica o ficheiro de configuração de rede. Terá de modificar o ficheiro para remover os elementos que estão a ser utilizados. As secções seguintes ajudam a modificar o ficheiro de configuração de rede que transferiu.

### <a name="lnsref"></a>Referências de Site de rede local

Para remover as informações de referência do site, efetuar alterações de configuração para **ConnectionsToLocalNetwork/LocalNetworkSiteRef**. Remover um acionadores de referência de local site do Azure para eliminar um túnel. Dependendo da configuração que criou, pode não ter um **LocalNetworkSiteRef** listados.

```
<Gateway>
   <ConnectionsToLocalNetwork>
     <LocalNetworkSiteRef name="D1BFC9CB_Site2">
       <Connection type="IPsec" />
     </LocalNetworkSiteRef>
   </ConnectionsToLocalNetwork>
 </Gateway>
```

Exemplo:

```
<Gateway>
   <ConnectionsToLocalNetwork>
   </ConnectionsToLocalNetwork>
 </Gateway>
```

###<a name="lns"></a>Sites de rede local

Remova todos os sites locais que já não está a utilizar. Dependendo da configuração que criou, é possível que não tem um **LocalNetworkSite** listados.

```
<LocalNetworkSites>
  <LocalNetworkSite name="Site1">
    <AddressSpace>
      <AddressPrefix>192.168.0.0/16</AddressPrefix>
    </AddressSpace>
    <VPNGatewayAddress>5.4.3.2</VPNGatewayAddress>
  </LocalNetworkSite>
  <LocalNetworkSite name="Site3">
    <AddressSpace>
      <AddressPrefix>192.168.0.0/16</AddressPrefix>
    </AddressSpace>
    <VPNGatewayAddress>57.179.18.164</VPNGatewayAddress>
  </LocalNetworkSite>
 </LocalNetworkSites>
```

Neste exemplo, removemos Site3 apenas.

```
<LocalNetworkSites>
  <LocalNetworkSite name="Site1">
    <AddressSpace>
      <AddressPrefix>192.168.0.0/16</AddressPrefix>
    </AddressSpace>
    <VPNGatewayAddress>5.4.3.2</VPNGatewayAddress>
  </LocalNetworkSite>
 </LocalNetworkSites>
```

### <a name="clientaddresss"></a>O conjunto de endereços do cliente

Se tiver uma ligação de P2S a vnet, terá um **VPNClientAddressPool**. Remova os conjuntos de endereços de cliente que corresponde ao gateway de rede virtual que tenha eliminado.

```
<Gateway>
    <VPNClientAddressPool>
      <AddressPrefix>10.1.0.0/24</AddressPrefix>
    </VPNClientAddressPool>
  <ConnectionsToLocalNetwork />
 </Gateway>
```

Exemplo:

```
<Gateway>
  <ConnectionsToLocalNetwork />
 </Gateway>
```

### <a name="gwsub"></a>GatewaySubnet

Eliminar o **GatewaySubnet** que corresponde a VNet.

```
<Subnets>
   <Subnet name="FrontEnd">
     <AddressPrefix>10.11.0.0/24</AddressPrefix>
   </Subnet>
   <Subnet name="GatewaySubnet">
     <AddressPrefix>10.11.1.0/29</AddressPrefix>
   </Subnet>
 </Subnets>
```

Exemplo:

```
<Subnets>
   <Subnet name="FrontEnd">
     <AddressPrefix>10.11.0.0/24</AddressPrefix>
   </Subnet>
 </Subnets>
```

## <a name="upload"></a>Passo 5: Carregue o ficheiro de configuração de rede

Guardar as alterações e carregar o ficheiro de configuração de rede para o Azure. Certifique-se de que altera o caminho do ficheiro conforme necessário para o seu ambiente.

```powershell
Set-AzureVNetConfig -ConfigurationPath C:\AzureNet\NetworkConfig.xml
```

Se tiver êxito, o retorno mostra algo semelhante a este exemplo:

```
OperationDescription        OperationId                      OperationStatus                                                
--------------------        -----------                      ---------------                                           
Set-AzureVNetConfig         e0ee6e66-9167-cfa7-a746-7casb9   Succeeded