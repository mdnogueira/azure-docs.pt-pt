---
title: 'Ligar uma rede virtual do Azure a outra VNet: Portal | Microsoft Docs'
description: "Crie uma ligação do Gateway de VPN entre VNets com o Gestor de Recursos e o Portal do Azure."
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: a7015cfc-764b-46a1-bfac-043d30a275df
ms.service: vpn-gateway
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/02/2017
ms.author: cherylmc
ms.openlocfilehash: cbbae06d4e5774d5fdf7bcce65dc7efddaa5f280
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="configure-a-vnet-to-vnet-vpn-gateway-connection-using-the-azure-portal"></a>Configurar uma ligação de gateway de VPN de VNet a VNet com o Portal do Azure

Este artigo mostra-lhe como criar uma ligação de gateway de VPN entre redes virtuais. As redes virtuais podem estar nas mesmas regiões ou em regiões diferentes e pertencer às mesmas subscrições ou a subscrições diferentes. Ao ligar VNets de diferentes subscrições, estas não têm de estar associadas ao mesmo inquilino do Active Directory. 

Os passos deste artigo aplicam-se ao modelo de implementação Resource Manager e utilização do portal do Azure. Também pode criar esta configuração ao utilizar uma ferramenta de implementação diferente ou modelo de implementação ao selecionar uma opção diferente da lista seguinte:

> [!div class="op_single_selector"]
> * [Portal do Azure](vpn-gateway-howto-vnet-vnet-resource-manager-portal.md)
> * [PowerShell](vpn-gateway-vnet-vnet-rm-ps.md)
> * [CLI do Azure](vpn-gateway-howto-vnet-vnet-cli.md)
> * [Portal do Azure (clássico)](vpn-gateway-howto-vnet-vnet-portal-classic.md)
> * [Ligue diferentes modelos de implementação - portal do Azure](vpn-gateway-connect-different-deployment-models-portal.md)
> * [Ligue diferentes modelos de implementação - PowerShell](vpn-gateway-connect-different-deployment-models-powershell.md)
>
>

![Diagrama v2v](./media/vpn-gateway-howto-vnet-vnet-resource-manager-portal/v2vrmps.png)

A ligação de uma rede virtual a outra rede virtual (VNet a VNet) é semelhante à ligação de uma VNet a uma localização do site no local. Ambos os tipos de conetividade utilizam um gateway de VPN para fornecer um túnel seguro através de IPsec/IKE. Se as suas VNets estiverem na mesma região, poderá ponderar ligá-las através da utilização de VNet Peering. O VNet peering não utiliza um gateway de VPN. Para obter mais informações, veja [VNet peering](../virtual-network/virtual-network-peering-overview.md).

A comunicação VNet a VNet pode ser combinada com configurações multilocal. Desta forma, pode estabelecer topologias de rede que combinam uma conectividade em vários locais com uma conectividade de rede intervirtual, conforme mostrado no diagrama seguinte:

![Acerca das ligações](./media/vpn-gateway-howto-vnet-vnet-resource-manager-portal/aboutconnections.png "Acerca das ligações")

### <a name="why-connect-virtual-networks"></a>Por que motivo ligar redes virtuais?

Poderá pretender ligar redes virtuais pelos seguintes motivos:

* **Geopresença e georredundância entre várias regiões**
  
  * Pode configurar a sua própria georreplicação ou sincronização com uma conetividade segura sem passar por pontos finais com acesso à Internet.
  * Com o Gestor de Tráfego e o Balanceador de Carga do Azure, pode configurar uma carga de trabalho de elevada disponibilidade com georredundância em várias regiões do Azure. Um exemplo importante consiste em configurar o SQL Always On com Grupos de Disponibilidade propagando-se em várias regiões do Azure.
* **Aplicações multicamadas regionais com isolamento ou limites administrativos**
  
  * Na mesma região, pode configurar aplicações de várias camadas com várias redes virtuais ligadas em conjunto devido a requisitos de isolamento ou administrativos.

Para obter mais informações sobre ligações de VNet a VNet, consulte [FAQ sobre VNet para VNet](#faq) no final deste artigo. Tenha em atenção que se a suas VNets estiverem em diferentes subscrições, não pode criar a ligação no portal. Pode utilizar o [PowerShell](vpn-gateway-vnet-vnet-rm-ps.md) ou a [CLI](vpn-gateway-howto-vnet-vnet-cli.md).

### <a name="values"></a>Definições de exemplo

Quando utiliza estes passos como um exercício, pode utilizar os valores de definição de exemplo. Para efeitos de exemplo, utilizamos vários espaços de endereço para cada VNet. No entanto, as configurações de VNet a VNet não necessitam de vários espaços de endereço.

**Valores da TestVNet1:**

* Nome da VNet: TestVNet1
* Espaço de Endereços: 10.11.0.0/16
  * Nome da sub-rede: FrontEnd
  * Intervalo de endereços da sub-rede: 10.11.0.0/24
* Grupo de Recursos: TestRG1
* Localização: EUA Leste
* Espaço de Endereços: 10.12.0.0/16
  * Nome da sub-rede: BackEnd
  * Intervalo de endereços da sub-rede: 10.12.0.0/24
* Nome da Sub-rede do Gateway: GatewaySubnet (isto irá preencher automaticamente no portal)
  * Intervalo de endereços da Sub-rede do Gateway: 10.11.255.0/27
* Servidor DNS: utilize o endereço IP do seu servidor DNS
* Nome do Gateway de Rede Virtual: TestVNet1GW
* Tipo de Gateway: VPN
* Tipo de VPN: baseado na rota
* SKU: selecione o SKU do Gateway que pretende utilizar
* Nome do endereço IP público: TestVNet1GWIP
* Valores de ligação:
  * Nome: TestVNet1toTestVNet4
  * Chave partilhada: pode criar a chave partilhada de forma independente. Para este exemplo, utilizaremos abc123. O mais importante é que quando criar a ligação entre as VNets, o valor tem de coincidir.

**Valores da TestVNet4:**

* Nome da VNet: TestVNet4
* Espaço de endereços: 10.41.0.0/16
  * Nome da sub-rede: FrontEnd
  * Intervalo de endereços da sub-rede: 10.41.0.0/24
* Grupo de Recursos: TestRG1
* Localização: EUA Oeste
* Espaço de Endereços: 10.42.0.0/16
  * Nome da sub-rede: BackEnd
  * Intervalo de endereços da sub-rede: 10.42.0.0/24
* Nome da GatewaySubnet: GatewaySubnet (isto irá preencher automáticamente no portal)
  * Intervalo de endereços da GatewaySubnet: 10.41.255.0/27
* Servidor DNS: utilize o endereço IP do seu servidor DNS
* Nome do Gateway de Rede Virtual: TestVNet4GW
* Tipo de Gateway: VPN
* Tipo de VPN: baseado na rota
* SKU: selecione o SKU do Gateway que pretende utilizar
* Nome do endereço IP público: TestVNet4GWIP
* Valores de ligação:
  * Nome: TestVNet4toTestVNet1
  * Chave partilhada: pode criar a chave partilhada de forma independente. Para este exemplo, utilizaremos abc123. O mais importante é que quando criar a ligação entre as VNets, o valor tem de coincidir.

## <a name="CreatVNet"></a>1. Criar e configurar a TestVNet1
Se já tiver uma VNet, certifique-se de que as definições são compatíveis com a conceção do seu gateway de VPN. Tenha em especial atenção as sub-redes que se possam sobrepor a outras redes. Se tiver sub-redes sobrepostas, a ligação não funcionará corretamente. Se a VNet estiver configurada com as definições corretas, pode iniciar os passos da secção [Especificar um servidor DNS](#dns).

### <a name="to-create-a-virtual-network"></a>Para criar uma rede virtual
[!INCLUDE [vpn-gateway-basic-vnet-rm-portal](../../includes/vpn-gateway-basic-vnet-rm-portal-include.md)]

## <a name="subnets"></a>2. Adicionar espaços de endereços adicionais e criar sub-redes
Pode adicionar espaços de endereços adicionais e criar sub-redes assim que a VNet tiver sido criada.

[!INCLUDE [vpn-gateway-additional-address-space](../../includes/vpn-gateway-additional-address-space-include.md)]

## <a name="gatewaysubnet"></a>3. Criar uma sub-rede do gateway
Antes de ligar a rede virtual a um gateway, primeiro tem de criar a sub-rede do gateway para a rede virtual à qual pretende ligar. Se possível, é melhor criar uma sub-rede do gateway com um bloco CIDR de /28 ou /27, de modo a fornecer endereços IP suficientes para contemplar requisitos de configuração futuros adicionais.

Se estiver a criar esta configuração como um exercício, veja estes [Definições de exemplo](#values) ao criar a sub-rede do gateway.

[!INCLUDE [vpn-gateway-no-nsg](../../includes/vpn-gateway-no-nsg-include.md)]

### <a name="to-create-a-gateway-subnet"></a>Para criar uma sub-rede do gateway
[!INCLUDE [vpn-gateway-add-gwsubnet-rm-portal](../../includes/vpn-gateway-add-gwsubnet-rm-portal-include.md)]

## <a name="dns"></a>4. Especificar um servidor DNS (opcional)
O DNS não é necessário para ligações de VNet a VNet. No entanto, se pretender ter a resolução de nomes para recursos que são implementados para a sua rede virtual, deve especificar um servidor DNS. Esta definição permite-lhe especificar o servidor DNS que pretende utilizar para a resolução de nomes desta rede virtual. Não cria um servidor DNS.

[!INCLUDE [vpn-gateway-add-dns-rm-portal](../../includes/vpn-gateway-add-dns-rm-portal-include.md)]

## <a name="VNetGateway"></a>5. Criar um gateway de rede virtual
Neste passo, vai criar o gateway de rede virtual da VNet. Criar um gateway, muitas vezes, pode demorar 45 minutos ou mais, dependendo do SKU de gateway selecionado. Se estiver a criar esta configuração como um exercício, pode ver as [Definições de exemplo](#values).

### <a name="to-create-a-virtual-network-gateway"></a>Para criar um gateway de rede virtual
[!INCLUDE [vpn-gateway-add-gw-rm-portal](../../includes/vpn-gateway-add-gw-rm-portal-include.md)]

## <a name="CreateTestVNet4"></a>6. Criar e configurar a TestVNet4
Assim que tiver configurado o TestVNet1, crie o TestVNet4 ao repetir os passos anteriores, substituindo os valores pelos do TestVNet4. Não precisa de esperar até que o gateway de rede virtual para o TestVNet1 tenha terminado de criar antes de configurar o TestVNet4. Se estiver a utilizar os seus próprios valores, certifique-se de que os espaços de endereço não ficam sobrepostos com qualquer uma das VNets a que pretende ligar.

## <a name="TestVNet1Connection"></a>7. Configure a ligação de TestVNet1
Quando os gateways de rede virtual para o TestVNet1 e o TestVNet4 estiverem concluídos, pode criar as ligações de gateway de rede virtual. Nesta secção, irá criar uma ligação da VNet1 à VNet4. Estes passos só funcionam em VNets na mesma subscrição. Se as VNets estiverem em diferentes subscrições, tem de utilizar o PowerShell para estabelecer a ligação. Consulte o artigo [PowerShell](vpn-gateway-vnet-vnet-rm-ps.md).

1. Em **Todos os recursos**, navegue para o gateway de rede virtual da sua VNet. Por exemplo, **TestVNet1GW**. Clique em **TestVNet1GW** para abrir o painel de gateway de rede virtual.
   
    ![Painel de ligações](./media/vpn-gateway-howto-vnet-vnet-resource-manager-portal/settings_connection.png "Painel de ligações")
2. Clique em **+Adicionar** para abrir o painel **Adicionar ligação**.
3. No painel **Adicionar ligação**, no campo de nome, escreva um nome para a sua ligação. Por exemplo, **TestVNet1toTestVNet4**.
   
    ![Nome da ligação](./media/vpn-gateway-howto-vnet-vnet-resource-manager-portal/v1tov4.png "Nome da ligação")
4. Para **Tipo de ligação**. selecione **VNet a VNet** na lista pendente.
5. O valor do campo **Primeiro gateway da rede virtual** é preenchido automaticamente porque está a criar esta ligação a partir do gateway de rede virtual especificado.
6. O campo **Segundo gateway da rede virtual** é o gateway de rede virtual da VNet ao qual pretende criar uma ligação. Clique em **Escolher outro gateway de rede virtual** para abrir o painel **Escolher gateway da rede virtual**.
   
    ![Adicionar ligação](./media/vpn-gateway-howto-vnet-vnet-resource-manager-portal/add_connection.png "Adicionar uma ligação")
7. Veja os gateways de rede virtual que se encontram listados neste painel. Tenha em atenção que estão listados apenas os gateways da rede virtual na sua subscrição. Se pretender ligar a um gateway de rede virtual que não se encontre na sua subscrição, utilize o [Artigo do PowerShell](vpn-gateway-vnet-vnet-rm-ps.md). 
8. Clique no gateway de rede virtual a que pretende ligar.
9. No campo **Chave partilhada**, escreva uma chave partilhada para a ligação. Pode gerar ou criar esta chave de forma independente. Numa ligação de site para site, a chave que utiliza é exatamente a mesma para o seu dispositivo no local e a sua ligação de gateway de rede virtual. O conceito é semelhante aqui, exceto que, em vez de se ligar a um dispositivo VPN, está a ligar-se a outro gateway de rede virtual.
   
    ![Chave partilhada](./media/vpn-gateway-howto-vnet-vnet-resource-manager-portal/sharedkey.png "Chave partilhada")
10. Clique em **OK** na parte inferior do painel para guardar as alterações.

## <a name="TestVNet4Connection"></a>8. Configure a ligação de TestVNet4
Em seguida, crie uma ligação da TestVNet4 à TestVNet1. Utilize o mesmo método que utilizou para criar a ligação do TestVNet1 ao TestVNet4. Certifique-se de que utiliza a mesma chave partilhada.

## <a name="VerifyConnection"></a>9. Verificar a ligação
Verifique a ligação. Para cada gateway de rede virtual, faça o seguinte:

1. Localize o painel do gateway de rede virtual. Por exemplo, **TestVNet4GW**. 
2. No painel do gateway de rede virtual, clique em **Ligações** para ver o painel de ligações do gateway de rede virtual.

Veja as ligações e verifique o estado. Quando a ligação for criada, verá **Com êxito** e **Ligado** nos valores do Estado.

![Com êxito](./media/vpn-gateway-howto-vnet-vnet-resource-manager-portal/connected.png "Com êxito")

Pode fazer duplo clique em cada ligação em separado para ver mais informações sobre a ligação.

![Essentials](./media/vpn-gateway-howto-vnet-vnet-resource-manager-portal/essentials.png "Essentials")

## <a name="faq"></a>FAQ da ligação VNet a VNet
Veja os detalhes das FAQ para obter informações adicionais sobre ligações VNet a VNet.

[!INCLUDE [vpn-gateway-vnet-vnet-faq](../../includes/vpn-gateway-faq-vnet-vnet-include.md)]

## <a name="next-steps"></a>Passos seguintes
Assim que a ligação estiver concluída, pode adicionar máquinas virtuais às redes virtuais. Veja a documentação das [Máquinas Virtuais](https://docs.microsoft.com/azure/#pivot=services&panel=Compute) para obter mais informações.
