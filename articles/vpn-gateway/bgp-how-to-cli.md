---
title: 'Configurar o BGP no gateway de VPN do Azure: o Gestor de recursos e a CLI | Microsoft Docs'
description: Este artigo explica como configurar o BGP com um gateway de VPN do Azure utilizando o Azure Resource Manager e a CLI.
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: cherylmc
ms.openlocfilehash: 98cd606ce930624ec5c591ffd8f13e0feae1a6c4
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-configure-bgp-on-an-azure-vpn-gateway-by-using-cli"></a>Como configurar o BGP no gateway de VPN do Azure ao utilizar a CLI

Este artigo ajuda-o a ativar o BGP numa ligação de VPN de Site a Site (S2S) em vários locais e uma ligação VNet a VNet (ou seja, uma ligação entre redes virtuais) utilizando o modelo de implementação Azure Resource Manager e a CLI do Azure.

## <a name="about-bgp"></a>Sobre o BGP

BGP é o protocolo de encaminhamento padrão utilizado normalmente na internet para trocar informações de encaminhamento e acessibilidade entre duas ou mais redes. BGP permite que os gateways de VPN e os dispositivos VPN no local, chamados elementos BGP ou vizinhos, para trocar rotas. As rotas informam ambas as gateways sobre a disponibilidade e acessibilidade para prefixos percorrerem os gateways ou routers envolvidos. BGP também pode ativar o encaminhamento de tráfego entre várias redes ao propagar as rotas que um gateway BGP aprende de um elemento de rede, para todos os outros elementos BGP.

Para obter mais informações sobre as vantagens do BGP e compreender os requisitos técnicos e as considerações de utilizar o BGP, consulte [descrição geral do BGP com gateways de VPN do Azure](vpn-gateway-bgp-overview.md).

Este artigo ajuda-o com as seguintes tarefas:

* [Ativar o BGP para o gateway de VPN](#enablebgp) (obrigatório)

  Em seguida, pode concluir qualquer uma das seguintes secções ou ambas:

* [Estabelecer uma ligação em vários locais com o BGP](#crossprembgp)
* [Estabelecer uma ligação VNet a VNet com o BGP](#v2vbgp)

Cada um destes três secções constitui um bloco modular básico para ativar o BGP na sua conectividade de rede. Se concluir todas as três secções, criar a topologia conforme mostrado no diagrama seguinte:

![Topologia BGP](./media/vpn-gateway-bgp-resource-manager-ps/bgp-crosspremv2v.png)

Pode combinar estas secções para criar uma rede de trânsito multihop mais complexa que satisfaça as suas necessidades.

## <a name ="enablebgp"></a>Ativar o BGP para o gateway de VPN

Esta secção é necessária antes de efetuar os passos das outras secções de configuração de dois. Os seguintes passos de configuração configurar os parâmetros BGP do gateway de VPN do Azure, conforme mostrado no diagrama seguinte:

![Gateway do BGP](./media/vpn-gateway-bgp-resource-manager-ps/bgp-gateway.png)

### <a name="before-you-begin"></a>Antes de começar

Instale a versão mais recente dos comandos da CLI (2.0 ou posteriores). Para obter informações sobre como instalar os comandos da CLI, veja [Instalar a CLI 2.0 do Azure](/cli/azure/install-azure-cli) e [Introdução à CLI 2.0 do Azure](/cli/azure/get-started-with-azure-cli).

### <a name="step-1-create-and-configure-testvnet1"></a>Passo 1: Criar e configurar a TestVNet1

#### <a name="Login"></a>1. Estabelecer a ligação à subscrição

[!INCLUDE [CLI login](../../includes/vpn-gateway-cli-login-include.md)]

#### <a name="2-create-a-resource-group"></a>2. Criar um grupo de recursos

O exemplo seguinte cria um grupo de recursos denominado TestRG1 na localização "eastus". Se já tiver um grupo de recursos na região onde pretende criar a rede virtual, pode utilizar em vez disso, o que uma.

```azurecli
az group create --name TestBGPRG1 --location eastus
```

#### <a name="3-create-testvnet1"></a>3. Criar a TestVNet1

O exemplo seguinte cria uma rede virtual denominada TestVNet1 e três sub-redes: GatewaySubnet, front-end e back-end. Quando estiver a substituindo os valores, é importante que sempre nome à sub-rede do gateway específico GatewaySubnet. Se der outro nome, a criação da gateway falha.

O primeiro comando cria o espaço de endereço front-end e a sub-rede do front-end. O segundo comando cria um espaço de endereços adicionais para a sub-rede de back-end. Os comandos terceiros e quarto criam a sub-rede de back-end e GatewaySubnet.

```azurecli
az network vnet create -n TestVNet1 -g TestBGPRG1 --address-prefix 10.11.0.0/16 -l eastus --subnet-name FrontEnd --subnet-prefix 10.11.0.0/24 
 
az network vnet update -n TestVNet1 --address-prefixes 10.11.0.0/16 10.12.0.0/16 -g TestBGPRG1 
 
az network vnet subnet create --vnet-name TestVNet1 -n BackEnd -g TestBGPRG1 --address-prefix 10.12.0.0/24 
 
az network vnet subnet create --vnet-name TestVNet1 -n GatewaySubnet -g TestBGPRG1 --address-prefix 10.12.255.0/27 
```

### <a name="step-2-create-the-vpn-gateway-for-testvnet1-with-bgp-parameters"></a>Passo 2: Criar o VPN gateway da TestVNet1 com parâmetros BGP

#### <a name="1-create-the-public-ip-address"></a>1. Criar o endereço IP público

Solicitar um endereço IP público. O endereço IP público será atribuído para o gateway VPN que criar para a rede virtual.

```azurecli
az network public-ip create -n GWPubIP -g TestBGPRG1 --allocation-method Dynamic 
```

#### <a name="2-create-the-vpn-gateway-with-the-as-number"></a>2. Criar o gateway VPN com o número AS

Crie o gateway de rede virtual para TestVNet1. BGP requer um gateway de VPN baseado na rota. É também necessário o parâmetro adicional `-Asn` para definir o número de sistema autónomo (ASN) da TestVNet1. Criar um gateway pode demorar algum tempo (45 minutos ou mais) para concluir. 

Se executar este comando utilizando a `--no-wait` parâmetro, não vir quaisquer comentários ou saída. O `--no-wait` parâmetro permite que o gateway ser criado em segundo plano. Não significa que o gateway de VPN criado imediatamente.

```azurecli
az network vnet-gateway create -n VNet1GW -l eastus --public-ip-address GWPubIP -g TestBGPRG1 --vnet TestVNet1 --gateway-type Vpn --sku HighPerformance --vpn-type RouteBased --asn 65010 --no-wait
```

#### <a name="3-obtain-the-azure-bgp-peer-ip-address"></a>3. Obter o elemento BGP do Azure para o endereço IP

Depois de criado o gateway, terá de obter o elemento BGP para o endereço IP no gateway de VPN do Azure. Este endereço é necessária para configurar o gateway VPN como um elemento de rede BGP para os seus dispositivos VPN no local.

Execute o seguinte comando e verifique o `bgpSettings` secção na parte superior do resultado:

```azurecli
az network vnet-gateway list -g TestBGPRG1 
 
  
"bgpSettings": { 
      "asn": 65010, 
      "bgpPeeringAddress": "10.12.255.30", 
      "peerWeight": 0 
    }
```

Depois de criado o gateway, pode utilizar este gateway para estabelecer uma ligação em vários locais ou de uma ligação VNet a VNet com o BGP.

## <a name ="crossprembgp"></a>Estabelecer uma ligação em vários locais com o BGP

Para estabelecer uma ligação entre locais, terá de criar um gateway de rede local para representar o dispositivo VPN no local. Em seguida, ligue o gateway de VPN do Azure com o gateway de rede local. Embora estes passos são semelhantes a criação de outras ligações, incluem as propriedades adicionais necessárias para especificar os parâmetros de configuração de BGP.

![BGP para vários locais](./media/vpn-gateway-bgp-resource-manager-ps/bgp-crossprem.png)


### <a name="step-1-create-and-configure-the-local-network-gateway"></a>Passo 1: Criar e configurar o gateway de rede local

Neste exercício continua a criar a configuração de mostrado no diagrama. Confirme que substitui os valores pelos que pretende utilizar para a configuração. Quando estiver a trabalhar com gateways de rede local, tenha em consideração os seguintes procedimentos:

* O gateway de rede local pode estar na mesma localização e grupo de recursos como o gateway VPN ou pode ser numa localização diferente e o grupo de recursos. Este exemplo mostra os gateways em grupos de recursos diferentes em diferentes localizações.
* O prefixo mínimo que tem de indicar para o gateway de rede local é o endereço do anfitrião do seu endereço IP do elemento de rede BGP no seu dispositivo VPN. Neste caso, é um /32 prefixo de 10.52.255.254/32.
* Como um lembrete, tem de utilizar ASNs BGP diferentes entre as redes no local e a rede virtual do Azure. Se estes são os mesmos, terá de alterar o ASN de VNet se os dispositivos VPN no local que já utilizam o ASN para peering com outros vizinhos BGP.

Antes de continuar, certifique-se de que concluiu o [ativar o BGP para o gateway de VPN](#enablebgp) secção neste exercício, e que o se ainda estiver ligado à subscrição 1. Tenha em atenção que neste exemplo, crie um novo grupo de recursos. Além disso, tenha em atenção os dois parâmetros adicionais para o gateway de rede local: `Asn` e `BgpPeerAddress`.

```azurecli
az group create -n TestBGPRG5 -l eastus2 
 
az network local-gateway create --gateway-ip-address 23.99.221.164 -n Site5 -g TestBGPRG5 --local-address-prefixes 10.51.255.254/32 --asn 65050 --bgp-peering-address 10.51.255.254
```

### <a name="step-2-connect-the-vnet-gateway-and-local-network-gateway"></a>Passo 2: Ligar o gateway de VNet e o gateway de rede local

Neste passo, criará a ligação da TestVNet1 para Site5. Tem de especificar o `--enable-bgp` parâmetro para ativar o BGP para esta ligação. 

Neste exemplo, o gateway de rede virtual e o gateway de rede local estão em grupos de recursos diferente. Quando os gateways estão em grupos de recursos diferente, tem de especificar o ID de recurso completo dos dois gateways para configurar uma ligação entre as redes virtuais.

#### <a name="1-get-the-resource-id-of-vnet1gw"></a>1. Obter o recurso do ID de VNet1GW

Utilize o resultado do comando seguinte para obter o ID de recurso do VNet1GW:

```azurecli
az network vnet-gateway show -n VNet1GW -g TestBGPRG1
```

O resultado, localize o `"id":` linha. Terá dos valores entre aspas para criar a ligação na secção seguinte.

Exemplo de saída:

```
{ 
  "activeActive": false, 
  "bgpSettings": { 
    "asn": 65010, 
    "bgpPeeringAddress": "10.12.255.30", 
    "peerWeight": 0 
  }, 
  "enableBgp": true, 
  "etag": "W/\"<your etag number>\"", 
  "gatewayDefaultSite": null, 
  "gatewayType": "Vpn", 
  "id": "/subscriptions/<subscription ID>/resourceGroups/TestBGPRG1/providers/Microsoft.Network/virtualNetworkGateways/VNet1GW",
```

Copie os valores após `"id":` para um editor de texto, como o Notepad, para que pode facilmente cole-os ao criar a ligação. 

```
"id": "/subscriptions/<subscription ID>/resourceGroups/TestRG1/providers/Microsoft.Network/virtualNetworkGateways/VNet1GW"
```

#### <a name="2-get-the-resource-id-of-site5"></a>2. Obter o ID de Site5 de recurso

Utilize o seguinte comando para obter o recurso do ID de Site5 da saída:

```azurecli
az network local-gateway show -n Site5 -g TestBGPRG5
```

#### <a name="3-create-the-testvnet1-to-site5-connection"></a>3. Criar a ligação da TestVNet1 para Site5

Neste passo, criará a ligação da TestVNet1 para Site5. Tal como abordado anteriormente, é possível ter ligações BGP e não BGP para o mesmo gateway de VPN do Azure. A menos que o BGP está ativado na propriedade de ligação, Azure não permitirão o BGP para esta ligação, apesar dos parâmetros BGP já estão configurados em ambas as gateways. Substitua a ID de subscrição com os seus próprios.

```azurecli
az network vpn-connection create -n VNet1ToSite5 -g TestBGPRG1 --vnet-gateway1 /subscriptions/<subscription ID>/resourceGroups/TestBGPRG1/providers/Microsoft.Network/virtualNetworkGateways/VNet1GW --enable-bgp -l eastus --shared-key "abc123" --local-gateway2 /subscriptions/<subscription ID>/resourceGroups/TestBGPRG5/providers/Microsoft.Network/localNetworkGateways/Site5 --no-wait
```

Para este exercício, o exemplo seguinte lista os parâmetros para introduzir na secção de configuração de BGP do seu dispositivo VPN no local:

```
Site5 ASN            : 65050
Site5 BGP IP         : 10.52.255.254
Prefixes to announce : (for example) 10.51.0.0/16 and 10.52.0.0/16
Azure VNet ASN       : 65010
Azure VNet BGP IP    : 10.12.255.30
Static route         : Add a route for 10.12.255.30/32, with nexthop being the VPN tunnel interface on your device
eBGP Multihop        : Ensure the "multihop" option for eBGP is enabled on your device if needed
```

Após alguns minutos, a ligação deverá ser estabelecida. A sessão de peering de BGP é iniciado depois de é estabelecida a ligação IPsec.

## <a name ="v2vbgp"></a>Estabelecer uma ligação VNet a VNet com o BGP

Esta secção adiciona uma ligação VNet a VNet com o BGP, conforme mostrado no diagrama seguinte: 

![BGP para VNet a VNet](./media/vpn-gateway-bgp-resource-manager-ps/bgp-vnet2vnet.png)

As seguintes instruções continuam dos passos nas secções anteriores. Para criar e configurar a TestVNet1 e o gateway VPN com o BGP, tem de concluir o [ativar o BGP para o gateway de VPN](#enablebgp) secção.

### <a name="step-1-create-testvnet2-and-the-vpn-gateway"></a>Passo 1: Criar TestVNet2 e o gateway VPN

É importante certificar-se de que o espaço de endereços IP da rede virtual novo, TestVNet2, não se sobreponha a nenhum dos intervalos sua VNet.

Neste exemplo, as redes virtuais pertencem à mesma subscrição. Pode configurar ligações VNet a VNet entre subscrições diferentes. Para obter mais informações, consulte [configurar uma ligação VNet a VNet](vpn-gateway-howto-vnet-vnet-cli.md). Certifique-se de que adiciona `-EnableBgp $True` ao criar as ligações para ativar o BGP.

#### <a name="1-create-a-new-resource-group"></a>1. Criar um novo grupo de recursos

```azurecli
az group create -n TestBGPRG2 -l westus
```

#### <a name="2-create-testvnet2-in-the-new-resource-group"></a>2. Criar TestVNet2 no novo grupo de recursos

O primeiro comando cria o espaço de endereço front-end e a sub-rede do front-end. O segundo comando cria um espaço de endereços adicionais para a sub-rede de back-end. Os comandos terceiros e quarto criam a sub-rede de back-end e GatewaySubnet.

```azurecli
az network vnet create -n TestVNet2 -g TestBGPRG2 --address-prefix 10.21.0.0/16 -l westus --subnet-name FrontEnd --subnet-prefix 10.21.0.0/24 
 
az network vnet update -n TestVNet2 --address-prefixes 10.21.0.0/16 10.22.0.0/16 -g TestBGPRG2 
 
az network vnet subnet create --vnet-name TestVNet2 -n BackEnd -g TestBGPRG2 --address-prefix 10.22.0.0/24 
 
az network vnet subnet create --vnet-name TestVNet2 -n GatewaySubnet -g TestBGPRG2 --address-prefix 10.22.255.0/27
```

#### <a name="3-create-the-public-ip-address"></a>3. Criar o endereço IP público

Solicitar um endereço IP público. O endereço IP público será atribuído para o gateway VPN que criar para a rede virtual.

```azurecli
az network public-ip create -n GWPubIP2 -g TestBGPRG2 --allocation-method Dynamic
```

#### <a name="4-create-the-vpn-gateway-with-the-as-number"></a>4. Criar o gateway VPN com o número AS

Crie o gateway de rede virtual para TestVNet2. Tem de substituir a predefinição ASN em gateways de VPN do Azure. Os ASNs para as redes virtuais ligadas têm de ser diferentes para ativar o BGP e o encaminhamento de trânsito.
 
```azurecli
az network vnet-gateway create -n VNet2GW -l westus --public-ip-address GWPubIP2 -g TestBGPRG2 --vnet TestVNet2 --gateway-type Vpn --sku Standard --vpn-type RouteBased --asn 65020 --no-wait
```

### <a name="step-2-connect-the-testvnet1-and-testvnet2-gateways"></a>Passo 2: Ligar os gateways TestVNet1 e TestVNet2

Neste passo, criará a ligação da TestVNet1 para Site5. Para ativar o BGP para esta ligação, tem de especificar o `--enable-bgp` parâmetro.

No exemplo seguinte, o gateway de rede virtual e o gateway de rede local estão em grupos de recursos diferente. Quando os gateways estão em grupos de recursos diferente, tem de especificar o ID de recurso completo dos dois gateways para configurar uma ligação entre as redes virtuais. 

#### <a name="1-get-the-resource-id-of-vnet1gw"></a>1. Obter o recurso do ID de VNet1GW 

Obter o recurso do ID de VNet1GW da saída do comando seguinte:

```azurecli
az network vnet-gateway show -n VNet1GW -g TestBGPRG1
```

#### <a name="2-get-the-resource-id-of-vnet2gw"></a>2. Obter o ID de VNet2GW de recurso

Obter o recurso do ID de VNet2GW da saída do comando seguinte:

```azurecli
az network vnet-gateway show -n VNet2GW -g TestBGPRG2
```

#### <a name="3-create-the-connections"></a>3. Criar ligações

Crie a ligação da TestVNet1 para TestVNet2 e a ligação de TestVNet2 para a TestVNet1. Substitua a ID de subscrição com os seus próprios.

```azurecli
az network vpn-connection create -n VNet1ToVNet2 -g TestBGPRG1 --vnet-gateway1 /subscriptions/<subscription ID>/resourceGroups/TestBGPRG1/providers/Microsoft.Network/virtualNetworkGateways/VNet1GW --enable-bgp -l eastus --shared-key "efg456" --vnet-gateway2 /subscriptions/<subscription ID>/resourceGroups/TestBGPRG2/providers/Microsoft.Network/virtualNetworkGateways/VNet2GW
```

```azurecli
az network vpn-connection create -n VNet2ToVNet1 -g TestBGPRG2 --vnet-gateway1 /subscriptions/<subscription ID>/resourceGroups/TestBGPRG2/providers/Microsoft.Network/virtualNetworkGateways/VNet2GW --enable-bgp -l westus --shared-key "efg456" --vnet-gateway2 /subscriptions/<subscription ID>/resourceGroups/TestBGPRG1/providers/Microsoft.Network/virtualNetworkGateways/VNet1GW
```

> [!IMPORTANT]
> Ativar o BGP para *ambos* ligações.
> 
> 

Depois de concluir estes passos, a ligação será estabelecida dentro de alguns minutos. A sessão de peering de BGP será cópias de segurança depois de concluída a ligação VNet a VNet.

## <a name="next-steps"></a>Passos seguintes

A ligação esteja concluída, pode adicionar máquinas virtuais para as redes virtuais. Para obter os passos, consulte [criar uma máquina virtual](../virtual-machines/virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
