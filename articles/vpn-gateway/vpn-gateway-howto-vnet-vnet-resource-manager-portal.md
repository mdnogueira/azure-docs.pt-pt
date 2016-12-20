---
title: "Ligar VNets com o modelo de implementação do Resource Manager e o Portal do Azure | Microsoft Docs"
description: "Crie uma ligação do Gateway de VPN entre VNets com o Gestor de Recursos e o Portal do Azure."
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: carmonm
editor: 
tags: azure-resource-manager
ms.assetid: a7015cfc-764b-46a1-bfac-043d30a275df
ms.service: vpn-gateway
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/25/2016
ms.author: cherylmc
translationtype: Human Translation
ms.sourcegitcommit: 28d81fe312195b9a9094e1ed066f5cba57c76933
ms.openlocfilehash: b85017913316a450fe19f1760abff6a86f933e2e


---
# <a name="configure-a-vnet-to-vnet-connection-using-the-azure-portal"></a>Configurar uma ligação de VNet a VNet com o Portal do Azure
> [!div class="op_single_selector"]
> * [Resource Manager - Portal do Azure](vpn-gateway-howto-vnet-vnet-resource-manager-portal.md)
> * [Resource Manager – PowerShell](vpn-gateway-vnet-vnet-rm-ps.md)
> * [Clássica – Portal Clássico](virtual-networks-configure-vnet-to-vnet-connection.md)
> 
> 

Este artigo descreve os passos para criar uma ligação entre VNets no modelo de implementação Resource Manager com a utilização de um Gateway de VPN e o Portal do Azure.

Quando utilizar o Portal do Azure para ligar redes virtuais, as VNets têm de estar na mesma subscrição. Se as redes virtuais estiverem em diferentes subscrições, pode ligá-las através dos passos do [PowerShell](vpn-gateway-vnet-vnet-rm-ps.md).

![Diagrama v2v](./media/vpn-gateway-howto-vnet-vnet-resource-manager-portal/v2vrmps.png)

### <a name="deployment-models-and-methods-for-vnet-to-vnet-connections"></a>Modelos de implementação e métodos para ligações VNet a VNet
[!INCLUDE [deployment models](../../includes/vpn-gateway-deployment-models-include.md)]

A tabela seguinte mostra os modelos e métodos de implementação atualmente disponíveis para configurações VNet a VNet. Quando estiver disponível um artigo com passos de configuração, criamos uma ligação direta para o mesmo nesta tabela.

[!INCLUDE [vpn-gateway-table-vnet-vnet](../../includes/vpn-gateway-table-vnet-to-vnet-include.md)]

**VNet peering**

[!INCLUDE [vpn-gateway-vnetpeeringlink](../../includes/vpn-gateway-vnetpeeringlink-include.md)]

## <a name="about-vnet-to-vnet-connections"></a>Acerca das ligações VNet a VNet
A ligação de uma rede virtual a outra rede virtual (VNet a VNet) é semelhante à ligação de uma VNet a uma localização do site no local. Ambos os tipos de conetividade utilizam um gateway de VPN do Azure para fornecer um túnel seguro através de IPsec/IKE. As VNets que liga podem ser em regiões diferentes ou em diferentes subscrições.

Pode, inclusive, combinar uma comunicação VNet a VNet com configurações multilocal. Desta forma, pode estabelecer topologias de rede que combinam uma conectividade em vários locais com uma conectividade de rede intervirtual, conforme mostrado no diagrama seguinte:

![Acerca das ligações](./media/vpn-gateway-howto-vnet-vnet-resource-manager-portal/aboutconnections.png "About connections")

### <a name="why-connect-virtual-networks"></a>Por que motivo ligar redes virtuais?
Poderá pretender ligar redes virtuais pelos seguintes motivos:

* **Geopresença e georredundância entre várias regiões**
  
  * Pode configurar a sua própria georreplicação ou sincronização com uma conetividade segura sem passar por pontos finais com acesso à Internet.
  * Com o Gestor de Tráfego e o Balanceador de Carga do Azure, pode configurar uma carga de trabalho de elevada disponibilidade com georredundância em várias regiões do Azure. Um exemplo importante consiste em configurar o SQL Always On com Grupos de Disponibilidade propagando-se em várias regiões do Azure.
* **Aplicações multicamadas regionais com isolamento ou limites administrativos**
  
  * Na mesma região, pode configurar aplicações de várias camadas com várias redes virtuais ligadas em conjunto devido a requisitos de isolamento ou administrativos.

Para obter mais informações sobre ligações de VNet a VNet, consulte [FAQ sobre VNet para VNet](#faq) no final deste artigo.

### <a name="a-namevaluesaexample-settings"></a><a name="values"></a>Definições de exemplo
Quando utiliza estes passos como um exercício, pode utilizar os valores de configuração de exemplo. Para efeitos de exemplo, utilizamos vários espaços de endereço para cada VNet. No entanto, as configurações de VNet a VNet não necessitam de vários espaços de endereço.

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

## <a name="a-namecreatvneta1-create-and-configure-testvnet1"></a><a name="CreatVNet"></a>1. Criar e configurar a TestVNet1
Se já tiver uma VNet, certifique-se de que as definições são compatíveis com a conceção do seu gateway de VPN. Tenha em especial atenção as sub-redes que se possam sobrepor a outras redes. Se tiver sub-redes sobrepostas, a ligação não funcionará corretamente. Se a VNet estiver configurada com as definições corretas, pode iniciar os passos da secção [Especificar um servidor DNS](#dns).

### <a name="to-create-a-virtual-network"></a>Para criar uma rede virtual
[!INCLUDE [vpn-gateway-basic-vnet-rm-portal](../../includes/vpn-gateway-basic-vnet-rm-portal-include.md)]

## <a name="a-namesubnetsa2-add-additional-address-space-and-create-subnets"></a><a name="subnets"></a>2. Adicionar espaços de endereços adicionais e criar sub-redes
Pode adicionar espaços de endereços adicionais e criar sub-redes assim que a VNet tiver sido criada.

[!INCLUDE [vpn-gateway-additional-address-space](../../includes/vpn-gateway-additional-address-space-include.md)]

## <a name="a-namegatewaysubneta3-create-a-gateway-subnet"></a><a name="gatewaysubnet"></a>3. Criar uma sub-rede do gateway
Antes de ligar a rede virtual a um gateway, primeiro tem de criar a sub-rede do gateway para a rede virtual à qual pretende ligar. Se possível, é melhor criar uma sub-rede do gateway com um bloco CIDR de /28 ou /27, de modo a fornecer endereços IP suficientes para contemplar requisitos de configuração futuros adicionais.

Se estiver a criar esta configuração como um exercício, veja estes [Definições de exemplo](#values) ao criar a sub-rede do gateway.

[!INCLUDE [vpn-gateway-no-nsg](../../includes/vpn-gateway-no-nsg-include.md)]

### <a name="to-create-a-gateway-subnet"></a>Para criar uma sub-rede do gateway
[!INCLUDE [vpn-gateway-add-gwsubnet-rm-portal](../../includes/vpn-gateway-add-gwsubnet-rm-portal-include.md)]

## <a name="a-namednsservera4-specify-a-dns-server-optional"></a><a name="DNSServer"></a>4. Especificar um servidor DNS (opcional)
Se pretender ter a resolução de nomes para máquinas virtuais que são implementadas para as suas VNets, deve especificar um servidor DNS.

[!INCLUDE [vpn-gateway-add-dns-rm-portal](../../includes/vpn-gateway-add-dns-rm-portal-include.md)]

## <a name="a-namevnetgatewaya5-create-a-virtual-network-gateway"></a><a name="VNetGateway"></a>5. Criar um gateway de rede virtual
Neste passo, vai criar o gateway de rede virtual da VNet. Este passo pode demorar até 45 minutos a concluir. Se estiver a criar esta configuração como um exercício, pode ver as [Definições de exemplo](#values).

### <a name="to-create-a-virtual-network-gateway"></a>Para criar um gateway de rede virtual
[!INCLUDE [vpn-gateway-add-gw-rm-portal](../../includes/vpn-gateway-add-gw-rm-portal-include.md)]

## <a name="a-namecreatetestvnet4a6-create-and-configure-testvnet4"></a><a name="CreateTestVNet4"></a>6. Criar e configurar a TestVNet4
Assim que tiver configurado o TestVNet1, crie o TestVNet4 ao repetir os passos anteriores, substituindo os valores pelos do TestVNet4. Não precisa de esperar até que o gateway de rede virtual para o TestVNet1 tenha terminado de criar antes de configurar o TestVNet4. Se estiver a utilizar os seus próprios valores, certifique-se de que os espaços de endereço não ficam sobrepostos com qualquer uma das VNets a que pretende ligar.

## <a name="a-nametestvnet1connectiona7-configure-the-testvnet1-connection"></a><a name="TestVNet1Connection"></a>7. Configure a ligação de TestVNet1
Quando os gateways de rede virtual para o TestVNet1 e o TestVNet4 estiverem concluídos, pode criar as ligações de gateway de rede virtual. Nesta secção, irá criar uma ligação da VNet1 à VNet4.

1. Em **Todos os recursos**, navegue para o gateway de rede virtual da sua VNet. Por exemplo, **TestVNet1GW**. Clique em **TestVNet1GW** para abrir o painel de gateway de rede virtual.
   
    ![Painel de ligações](./media/vpn-gateway-howto-vnet-vnet-resource-manager-portal/settings_connection.png "Connections blade")
2. Clique em **+Adicionar** para abrir o painel **Adicionar ligação**.
3. No painel **Adicionar ligação**, no campo de nome, escreva um nome para a sua ligação. Por exemplo, **TestVNet1toTestVNet4**.
   
    ![Nome da ligação](./media/vpn-gateway-howto-vnet-vnet-resource-manager-portal/v1tov4.png "Connection name")
4. Para **Tipo de ligação**. selecione **VNet a VNet** na lista pendente.
5. O valor do campo **Primeiro gateway da rede virtual** é preenchido automaticamente porque está a criar esta ligação a partir do gateway de rede virtual especificado.
6. O campo **Segundo gateway da rede virtual** é o gateway de rede virtual da VNet ao qual pretende criar uma ligação. Clique em **Escolher outro gateway de rede virtual** para abrir o painel **Escolher gateway da rede virtual**.
   
    ![Adicionar ligação](./media/vpn-gateway-howto-vnet-vnet-resource-manager-portal/add_connection.png "Add a connection")
7. Veja os gateways de rede virtual que se encontram listados neste painel. Tenha em atenção que estão listados apenas os gateways da rede virtual na sua subscrição. Se pretender ligar a um gateway de rede virtual que não se encontre na sua subscrição, utilize o [Artigo do PowerShell](vpn-gateway-vnet-vnet-rm-ps.md). 
8. Clique no gateway de rede virtual a que pretende ligar.
9. No campo **Chave partilhada**, escreva uma chave partilhada para a ligação. Pode gerar ou criar esta chave de forma independente. Numa ligação de site para site, a chave que utiliza é exatamente a mesma para o seu dispositivo no local e a sua ligação de gateway de rede virtual. O conceito é semelhante aqui, exceto que, em vez de se ligar a um dispositivo VPN, está a ligar-se a outro gateway de rede virtual.
   
    ![Chave partilhada](./media/vpn-gateway-howto-vnet-vnet-resource-manager-portal/sharedkey.png "Shared key")
10. Clique em **OK** na parte inferior do painel para guardar as alterações.

## <a name="a-nametestvnet4connectiona8-configure-the-testvnet4-connection"></a><a name="TestVNet4Connection"></a>8. Configure a ligação de TestVNet4
Em seguida, crie uma ligação da TestVNet4 à TestVNet1. Utilize o mesmo método que utilizou para criar a ligação do TestVNet1 ao TestVNet4. Certifique-se de que utiliza a mesma chave partilhada.

## <a name="a-nameverifyconnectiona9-verify-your-connection"></a><a name="VerifyConnection"></a>9. Verificar a ligação
Verifique a ligação. Para cada gateway de rede virtual, faça o seguinte:

1. Localize o painel do gateway de rede virtual. Por exemplo, **TestVNet4GW**. 
2. No painel do gateway de rede virtual, clique em **Ligações** para ver o painel de ligações do gateway de rede virtual.

Veja as ligações e verifique o estado. Quando a ligação for criada, verá **Com êxito** e **Ligado** nos valores do Estado.

![Bem-sucedido](./media/vpn-gateway-howto-vnet-vnet-resource-manager-portal/connected.png "Succeeded")

Pode fazer duplo clique em cada ligação em separado para ver mais informações sobre a ligação.

![Essentials](./media/vpn-gateway-howto-vnet-vnet-resource-manager-portal/essentials.png "Essentials")

## <a name="a-namefaqavnet-to-vnet-faq"></a><a name="faq"></a>FAQ da ligação VNet a VNet
Veja os detalhes das FAQ para obter informações adicionais sobre ligações VNet a VNet.

[!INCLUDE [vpn-gateway-vnet-vnet-faq](../../includes/vpn-gateway-vnet-vnet-faq-include.md)]

## <a name="next-steps"></a>Passos seguintes
Assim que a ligação estiver concluída, pode adicionar máquinas virtuais às redes virtuais. Veja a documentação das [Máquinas Virtuais](https://docs.microsoft.com/azure/#pivot=services&panel=Compute) para obter mais informações.



<!--HONumber=Nov16_HO4-->


