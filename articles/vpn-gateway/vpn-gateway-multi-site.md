---
title: "Ligar uma rede virtual a vários sites com Gateway de VPN e o PowerShell: clássico | Microsoft Docs"
description: "Este artigo irá guiá-lo através de ligar vários sites locais no local a uma rede virtual com um Gateway de VPN para o modelo de implementação clássica."
services: vpn-gateway
documentationcenter: na
author: yushwang
manager: rossort
editor: 
tags: azure-service-management
ms.assetid: b043df6e-f1e8-4a4d-8467-c06079e2c093
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/20/2017
ms.author: yushwang
ms.openlocfilehash: 434f84dc6244eddce9b172a617722b218360ffc2
ms.sourcegitcommit: 1131386137462a8a959abb0f8822d1b329a4e474
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/13/2017
---
# <a name="add-a-site-to-site-connection-to-a-vnet-with-an-existing-vpn-gateway-connection-classic"></a>Adicionar uma ligação Site a Site para uma VNet com uma ligação de gateway VPN existente (clássica)

[!INCLUDE [deployment models](../../includes/vpn-gateway-classic-deployment-model-include.md)]

> [!div class="op_single_selector"]
> * [Portal do Azure](vpn-gateway-howto-multi-site-to-site-resource-manager-portal.md)
> * [PowerShell (clássico)](vpn-gateway-multi-site.md)
>
>

Este artigo explica como utilizar o PowerShell para adicionar as ligações Site a Site (S2S) para um gateway VPN que tenha uma ligação existente. Este tipo de ligação é frequentemente referido como um configuration "multilocal". Os passos neste artigo aplicam-se a redes virtuais criadas com o modelo de implementação clássica (também conhecido como a gestão de serviço). Estes passos não se aplicam a configurações de ligação coexistentes ExpressRoute/Site a Site.

### <a name="deployment-models-and-methods"></a>Métodos e modelos de implementação

[!INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)]

Atualizamos esta tabela medida que novos artigos e ferramentas adicionais ficam disponíveis para esta configuração. Quando estiver disponível um artigo, criamos uma ligação direta desta tabela.

[!INCLUDE [vpn-gateway-table-multi-site](../../includes/vpn-gateway-table-multisite-include.md)]

## <a name="about-connecting"></a>Sobre a ligação

Pode ligar vários sites no local para uma única rede virtual. Isto é especialmente apelativo para a criação de soluções de nuvem híbrida. Criar uma ligação de vários site para o gateway de rede virtual do Azure é semelhante à criação de outras ligações Site a Site. Na verdade, pode utilizar um gateway de VPN do Azure existente, desde que o gateway é dinâmico (baseado na rota).

Se já tiver um gateway estático ligado à rede virtual, pode alterar o tipo de gateway para dinâmica sem ser necessário reconstruir a rede virtual para acomodar multilocal. Antes de alterar o tipo de encaminhamento, certifique-se de que o gateway de VPN no local suporta configurações de VPN baseado na rota.

![diagrama multilocal](./media/vpn-gateway-multi-site/multisite.png "multilocal")

## <a name="points-to-consider"></a>Pontos a considerar

**Não poderá utilizar o portal para efetuar alterações a esta rede virtual.** Terá de efetuar alterações ao ficheiro de configuração de rede em vez de utilizar o portal. Se efetuar alterações no portal, estas irá substituir as definições de referência de vários sites para esta rede virtual.

Devem sentir-se confortáveis ao utilizar o ficheiro de configuração de rede dentro do tempo a concluir o procedimento de vários site. No entanto, se tiver várias pessoas na sua configuração de rede, terá de certificar-se de que todas as pessoas conhece esta limitação. Isto não significa que não é possível utilizar o portal de todo. Pode utilizá-lo para tudo o resto, exceto alterações de configuração para esta rede virtual específico.

## <a name="before-you-begin"></a>Antes de começar

Antes de iniciar a configuração, certifique-se de que tem o seguinte:

* Hardware VPN compatíveis para cada localização no local. Verifique [sobre dispositivos VPN para conectividade de rede Virtual](vpn-gateway-about-vpn-devices.md) para verificar se o dispositivo que pretende utilizar é algo que é conhecida como sendo compatível.
* Um com acesso exterior IPv4 endereço IP público para cada dispositivo VPN. O endereço IP não pode estar localizado atrás de um NAT. Este é o requisito.
* Terá de instalar a versão mais recente dos cmdlets do Azure PowerShell. Certifique-se que instale a versão de gestão de serviço (SM) para além da versão do Gestor de recursos. Consulte [como instalar e configurar o Azure PowerShell](/powershell/azure/overview) para obter mais informações.
* Alguém que proficient em configurar o hardware VPN. Terá de ter uma compreensão sobre como configurar o dispositivo VPN ou trabalhar com alguém que does forte.
* Os intervalos de endereços IP que pretende utilizar para a rede virtual (se não tiver já criado uma).
* Os intervalos de endereços IP para cada um dos locais de rede local que irá ser ligar. Tem de certificar-se de que o endereço IP intervalos para cada um dos sites de rede local que pretende ligar-se sobrepor a não. Caso contrário, o portal ou a API REST irão rejeitar a configuração a ser carregada.<br>Por exemplo, se tiver dois sites de rede local que ambos contêm o IP endereço intervalo 10.2.3.0/24 e tiver um pacote com um endereço de destino 10.2.3.3, o Azure não sabe que pretende enviar o pacote porque são sobreposição de intervalos de endereços de site. Para evitar problemas de encaminhamento, Azure não permite-lhe carregar um ficheiro de configuração que tenha intervalos sobrepostos.

## <a name="1-create-a-site-to-site-vpn"></a>1. Criar uma Rede de VPNs
Se já tiver uma VPN de Site para Site com um gateway de encaminhamento dinâmico, great! Para poder continuar para [exportar as definições de configuração de rede virtual](#export). Caso contrário, efetue o seguinte:

### <a name="if-you-already-have-a-site-to-site-virtual-network-but-it-has-a-static-policy-based-routing-gateway"></a>Se já tiver uma rede virtual Site a Site, mas tem um gateway de encaminhamento estático (baseado em políticas):
1. Altere o tipo de gateway para encaminhamento dinâmico. Uma VPN multilocal requer um gateway de encaminhamento dinâmico (também conhecido como baseado na rota). Para alterar o tipo de gateway, terá de primeiro eliminar o gateway existente e crie um novo.
2. Configure o novo gateway e crie o túnel VPN. Para obter instruções, para obter instruções, consulte [especificar o tipo SKU e a VPN](vpn-gateway-howto-site-to-site-classic-portal.md#sku). Certifique-se de que especifica o tipo de encaminhamento de mensagens em fila 'Dinâmica' como.

### <a name="if-you-dont-have-a-site-to-site-virtual-network"></a>Se não tiver uma rede virtual do Site para Site:
1. Criar a rede virtual do Site para Site a utilizar estas instruções: [criar uma rede Virtual com uma ligação de VPN de Site para Site](vpn-gateway-site-to-site-create.md).  
2. Configurar um gateway de encaminhamento dinâmico a utilizar estas instruções: [configurar um Gateway de VPN](vpn-gateway-configure-vpn-gateway-mp.md). Lembre-se de que selecione **encaminhamento dinâmico** para o seu tipo de gateway.

## <a name="export"></a>2. Exportar o ficheiro de configuração de rede
Exporte o ficheiro de configuração de rede do Azure executando o seguinte comando. Pode alterar a localização do ficheiro a exportar para uma localização diferente, se necessário.

```powershell
Get-AzureVNetConfig -ExportToFile C:\AzureNet\NetworkConfig.xml
```

## <a name="3-open-the-network-configuration-file"></a>3. Abra o ficheiro de configuração de rede
Abra o ficheiro de configuração de rede que transferiu no último passo. Utilize qualquer editor de xml que pretender. O ficheiro deve ter um aspeto semelhante ao seguinte:

        <NetworkConfiguration xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/ServiceHosting/2011/07/NetworkConfiguration">
          <VirtualNetworkConfiguration>
            <LocalNetworkSites>
              <LocalNetworkSite name="Site1">
                <AddressSpace>
                  <AddressPrefix>10.0.0.0/16</AddressPrefix>
                  <AddressPrefix>10.1.0.0/16</AddressPrefix>
                </AddressSpace>
                <VPNGatewayAddress>131.2.3.4</VPNGatewayAddress>
              </LocalNetworkSite>
              <LocalNetworkSite name="Site2">
                <AddressSpace>
                  <AddressPrefix>10.2.0.0/16</AddressPrefix>
                  <AddressPrefix>10.3.0.0/16</AddressPrefix>
                </AddressSpace>
                <VPNGatewayAddress>131.4.5.6</VPNGatewayAddress>
              </LocalNetworkSite>
            </LocalNetworkSites>
            <VirtualNetworkSites>
              <VirtualNetworkSite name="VNet1" AffinityGroup="USWest">
                <AddressSpace>
                  <AddressPrefix>10.20.0.0/16</AddressPrefix>
                  <AddressPrefix>10.21.0.0/16</AddressPrefix>
                </AddressSpace>
                <Subnets>
                  <Subnet name="FE">
                    <AddressPrefix>10.20.0.0/24</AddressPrefix>
                  </Subnet>
                  <Subnet name="BE">
                    <AddressPrefix>10.20.1.0/24</AddressPrefix>
                  </Subnet>
                  <Subnet name="GatewaySubnet">
                    <AddressPrefix>10.20.2.0/29</AddressPrefix>
                  </Subnet>
                </Subnets>
                <Gateway>
                  <ConnectionsToLocalNetwork>
                    <LocalNetworkSiteRef name="Site1">
                      <Connection type="IPsec" />
                    </LocalNetworkSiteRef>
                  </ConnectionsToLocalNetwork>
                </Gateway>
              </VirtualNetworkSite>
            </VirtualNetworkSites>
          </VirtualNetworkConfiguration>
        </NetworkConfiguration>

## <a name="4-add-multiple-site-references"></a>4. Adicionar várias referências de site
Quando adicionar ou remover informações de referência de site, terá de efetuar alterações de configuração para ConnectionsToLocalNetwork/LocalNetworkSiteRef. Adicionar um novo acionadores de referência do local site do Azure para criar um túnel de novo. No exemplo abaixo, a configuração de rede é para uma ligação de site único. Guarde o ficheiro depois de terminar de efetuar as alterações.

```
  <Gateway>
    <ConnectionsToLocalNetwork>
      <LocalNetworkSiteRef name="Site1"><Connection type="IPsec" /></LocalNetworkSiteRef>
    </ConnectionsToLocalNetwork>
  </Gateway>
```

Para adicionar referências de site adicionais (criar uma configuração de vários site), adicione simplesmente linhas adicionais de "LocalNetworkSiteRef", conforme mostrado no exemplo abaixo:

```
  <Gateway>
    <ConnectionsToLocalNetwork>
      <LocalNetworkSiteRef name="Site1"><Connection type="IPsec" /></LocalNetworkSiteRef>
      <LocalNetworkSiteRef name="Site2"><Connection type="IPsec" /></LocalNetworkSiteRef>
    </ConnectionsToLocalNetwork>
  </Gateway>
```

## <a name="5-import-the-network-configuration-file"></a>5. Importar o ficheiro de configuração de rede
Importe o ficheiro de configuração de rede. Quando importar este ficheiro com as alterações, serão adicionados os túneis de novo. Os túneis irão utilizar o gateway dinâmico que criou anteriormente. Pode utilizar o PowerShell para importar o ficheiro.

## <a name="6-download-keys"></a>6. Transferir as chaves
Assim que a sua nova túneis foram adicionados, utilize o cmdlet do PowerShell 'Get-AzureVNetGatewayKey' para obter as chaves pré-partilhada IPsec/IKE para cada túnel.

Por exemplo:

```powershell
Get-AzureVNetGatewayKey –VNetName "VNet1" –LocalNetworkSiteName "Site1"
Get-AzureVNetGatewayKey –VNetName "VNet1" –LocalNetworkSiteName "Site2"
```

Se preferir, pode também utilizar o *obter Virtual rede Gateway chave partilhada* API REST para obter as chaves pré-partilhadas.

## <a name="7-verify-your-connections"></a>7. Verifique as suas ligações
Verifique o estado de túnel de vários sites. Depois de transferir as chaves para cada túnel, poderá ser útil verificar as ligações. Utilize 'Get-AzureVnetConnection' para obter uma lista de túneis de rede virtual, conforme mostrado no exemplo abaixo. VNet1 é o nome da VNet.

```powershell
Get-AzureVnetConnection -VNetName VNET1
```

Exemplo de retorno:

```
    ConnectivityState         : Connected
    EgressBytesTransferred    : 661530
    IngressBytesTransferred   : 519207
    LastConnectionEstablished : 5/2/2014 2:51:40 PM
    LastEventID               : 23401
    LastEventMessage          : The connectivity state for the local network site 'Site1' changed from Not Connected to Connected.
    LastEventTimeStamp        : 5/2/2014 2:51:40 PM
    LocalNetworkSiteName      : Site1
    OperationDescription      : Get-AzureVNetConnection
    OperationId               : 7f68a8e6-51e9-9db4-88c2-16b8067fed7f
    OperationStatus           : Succeeded

    ConnectivityState         : Connected
    EgressBytesTransferred    : 789398
    IngressBytesTransferred   : 143908
    LastConnectionEstablished : 5/2/2014 3:20:40 PM
    LastEventID               : 23401
    LastEventMessage          : The connectivity state for the local network site 'Site2' changed from Not Connected to Connected.
    LastEventTimeStamp        : 5/2/2014 2:51:40 PM
    LocalNetworkSiteName      : Site2
    OperationDescription      : Get-AzureVNetConnection
    OperationId               : 7893b329-51e9-9db4-88c2-16b8067fed7f
    OperationStatus           : Succeeded
```

## <a name="next-steps"></a>Passos seguintes

Para mais informações sobre Gateways de VPN, consulte o artigo [acerca dos VPN Gateways](vpn-gateway-about-vpngateways.md).
