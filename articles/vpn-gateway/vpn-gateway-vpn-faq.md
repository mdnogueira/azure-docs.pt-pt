---
title: FAQ do Gateway de VPN da Rede Virtual | Microsoft Docs
description: "FAQ do VPN Gateway. FAQ das ligações em vários locais da Rede Virtual do Microsoft Azure, das ligações da configuração híbrida e dos VPN Gateways"
services: vpn-gateway
documentationcenter: na
author: yushwang
manager: rossort
editor: 
ms.assetid: 6ce36765-250e-444b-bfc7-5f9ec7ce0742
ms.service: vpn-gateway
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/10/2016
ms.author: yushwang
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 5528e1c53e58d4b9c70f022290160ac9d6a1a986


---
# <a name="vpn-gateway-faq"></a>FAQ do VPN Gateway
## <a name="connecting-to-virtual-networks"></a>Ligar às Redes Virtuais
### <a name="can-i-connect-virtual-networks-in-different-azure-regions"></a>Posso ligar redes virtuais em diferentes regiões do Azure?
Sim. Na verdade, não existe qualquer restrição de região. Uma rede virtual pode ligar a outra rede virtual na mesma região ou numa região do Azure diferente.

### <a name="can-i-connect-virtual-networks-in-different-subscriptions"></a>Posso ligar redes virtuais em diferentes subscrições?
Sim.

### <a name="can-i-connect-to-multiple-sites-from-a-single-virtual-network"></a>Posso ligar a vários sites a partir de uma única rede virtual?
Pode ligar a vários sites com o Windows PowerShell e as APIs REST do Azure. Veja a secção das FAQ [Conetividade Multilocal e VNet a VNet](#multi-site-and-vnet-to-vnet-connectivity).

## <a name="what-are-my-crosspremises-connection-options"></a>Quais são as minhas opções de ligação em vários locais?
São suportadas as seguintes ligações em vários locais:

* [Site a Site](vpn-gateway-site-to-site-create.md) – ligação VPN através de IPsec (IKE v1 e IKE v2). Este tipo de ligação requer um dispositivo VPN ou RRAS.
* [Ponto a Site](vpn-gateway-point-to-site-create.md) – ligação VPN através de SSTP (Secure Socket Tunneling Protocol). Esta ligação não requer um dispositivo VPN.
* [VNet a VNet](virtual-networks-configure-vnet-to-vnet-connection.md) – este tipo de ligação é igual a uma configuração Site a Site. A ligação VNet a VNet é uma ligação VPN através de IPsec (IKE v1 e IKE v2). Não requer um dispositivo VPN.
* [Multilocal](vpn-gateway-multi-site.md) – é uma variação de uma configuração Site a Site que lhe permite ligar vários sites no local a uma rede virtual.
* [ExpressRoute](../expressroute/expressroute-introduction.md) – o ExpressRoute é uma ligação direta ao Azure a partir da sua WAN e não através da Internet pública. Veja a [Descrição Geral Técnica do ExpressRoute](../expressroute/expressroute-introduction.md) e as [FAQ do ExpressRoute](../expressroute/expressroute-faqs.md) para obter mais informações.

Para obter mais informações sobre as ligações, veja [Acerca do Gateway de VPN](vpn-gateway-about-vpngateways.md).

### <a name="what-is-the-difference-between-a-sitetosite-connection-and-pointtosite"></a>Qual é a diferença entre uma ligação Site a Site e Ponto a Site?
As ligações **Site a Site** permitem estabelecer ligação entre qualquer um dos computadores localizados no local a qualquer máquina virtual ou instância de função na sua rede virtual, dependendo de como escolher configurar o encaminhamento. É uma excelente opção para uma ligação em vários locais sempre disponível e é adequada para configurações híbridas. Este tipo de ligação depende de uma aplicação VPN IPsec (aplicação de hardware ou software), que tem de ser implementada na periferia da sua rede. Para criar este tipo de ligação, terá de ter o hardware VPN necessário e um endereço IPv4 com acesso exterior.

As ligações **Ponto a Site** permitem ligar a partir de um único computador em qualquer lugar a qualquer destino localizado na sua rede virtual. Utiliza o cliente VPN fornecido pelo Windows. Como parte da configuração Ponto a Site, instale um certificado e um pacote de configuração do cliente VPN, que contém as definições que permitem que o computador estabeleça ligação a qualquer máquina virtual ou instância de função na rede virtual. É ótimo quando pretende estabelecer ligação a uma rede virtual, mas não está no local. Também é uma boa opção quando não tiver acesso ao hardware VPN ou a um endereço IPv4 com acesso exterior, que são necessários para uma ligação Site a Site. 

Pode configurar a rede virtual para utilizar o Site a Site e o Ponto a Site em simultâneo, desde que crie a ligação Site a Site através de um tipo de VPN baseado na rota do seu gateway. Os tipos de VPN com base na rota são denominados gateways dinâmicos no modelo de implementação clássica.

### <a name="what-is-expressroute"></a>O que é o ExpressRoute?
O ExpressRoute permite-lhe criar ligações privadas entre os datacenters da Microsoft e a infraestrutura no local ou num ambiente de colocalização. Com o ExpressRoute, pode estabelecer ligações a serviços em nuvem da Microsoft, tais como o Microsoft Azure e o Office 365 numa instalação de colocalização de parceiros ExpressRoute ou ligar diretamente da sua rede WAN existente (por exemplo, uma VPN MPLS fornecida por um fornecedor de serviços de rede).

As ligações ExpressRoute oferecem maior segurança, mais fiabilidade, largura de banda superior e latências inferiores do que as ligações típicas através da Internet. Em alguns casos, utilizar ligações ExpressRoute para transferir dados entre a rede no local e o Azure também pode resultar em vantagens significativas ao nível dos custos. Se já tiver criado uma ligação em vários locais a partir da rede no local para o Azure, pode migrar para uma ligação ExpressRoute mantendo a rede virtual intacta.

Veja [FAQ do ExpressRoute](../expressroute/expressroute-faqs.md) para obter mais detalhes.

## <a name="sitetosite-connections-and-vpn-devices"></a>Ligações Site a Site e dispositivos VPN
### <a name="what-should-i-consider-when-selecting-a-vpn-device"></a>O que devo considerar ao selecionar um dispositivo VPN?
Validamos um conjunto de dispositivos de Rede de VPNs padrão em parceria com fornecedores de dispositivos. Pode encontrar uma lista de dispositivos VPN compatíveis conhecidos, exemplos ou instruções de configuração correspondentes e especificações de dispositivo [aqui](vpn-gateway-about-vpn-devices.md). Todos os dispositivos das famílias de dispositivos listadas como compatíveis devem funcionar com a Rede Virtual. Para ajudar a configurar o dispositivo VPN, consulte o exemplo de configuração do dispositivo ou a ligação que corresponde a uma família de dispositivos adequados.

### <a name="what-do-i-do-if-i-have-a-vpn-device-that-isnt-in-the-known-compatible-device-list"></a>O que posso fazer se tiver um dispositivo VPN que não esteja na lista de dispositivos compatíveis conhecidos?
Se não vir o seu dispositivo listado como um dispositivo VPN compatível conhecido e pretender utilizá-lo para a ligação de VPN, terá de verificar que cumpre os parâmetros e as opções de configuração do IPsec/IKE suportados listados [aqui](vpn-gateway-about-vpn-devices.md#devices-not-on-the-compatible-list). Os dispositivos que cumprem os requisitos mínimos devem funcionar bem com gateways de VPN. Contacte o fabricante do dispositivo para obter instruções adicionais de suporte e de configuração.

### <a name="why-does-my-policybased-vpn-tunnel-go-down-when-traffic-is-idle"></a>Por que motivo o meu túnel VPN baseado em políticas diminui quando o tráfego está inativo?
Este comportamento está previsto para gateways de VPN baseados em políticas (também conhecido como encaminhamento estático). Quando o tráfego através do túnel estiver inativo durante mais de 5 minutos, o túnel é desativado. Quando o tráfego começar a fluir em qualquer direção, o túnel é restabelecido imediatamente.

### <a name="can-i-use-software-vpns-to-connect-to-azure"></a>Posso utilizar VPNs de software para ligar ao Azure?
Suportamos servidores de Encaminhamento e Acesso Remoto (RRAS) do Windows Server 2012 para uma configuração em vários locais Site a Site.

Outras soluções de VPN de software devem funcionar com o nosso gateway, desde que obedeçam às implementações de IPsec de norma da indústria. Contacte o fornecedor do software para as obter instruções de configuração e de suporte.

## <a name="pointtosite-connections"></a>Ligações Ponto a Site
### <a name="what-operating-systems-can-i-use-with-pointtosite"></a>Que sistemas operativos posso utilizar com a ligação Ponto a Site?
São suportados os seguintes sistemas operativos:

* Windows 7 (32 e 64 bits)
* Windows Server 2008 R2 (apenas 64 bits)
* Windows 8 (32 e 64 bits)
* Windows 8.1 (32 e 64 bits)
* Windows Server 2012 (apenas 64 bits)
* Windows Server 2012 R2 (apenas 64 bits)
* Windows 10

### <a name="can-i-use-any-software-vpn-client-for-pointtosite-that-supports-sstp"></a>Posso utilizar qualquer cliente VPN de software para a ligação Ponto a Site que suporte SSTP?
Não. O suporte está limitado apenas às versões do sistema operativo Windows indicadas acima.

### <a name="how-many-vpn-client-endpoints-can-i-have-in-my-pointtosite-configuration"></a>Quantos pontos finais de cliente VPN posso ter na minha configuração Ponto a Site?
Suportamos até 128 clientes VPN para conseguir ligar a uma rede virtual ao mesmo tempo.

### <a name="can-i-use-my-own-internal-pki-root-ca-for-pointtosite-connectivity"></a>Posso utilizar AC de raiz de PKI interna para a conetividade Ponto a Site?
Sim. Anteriormente, só podiam ser utilizados os certificados de raiz autoassinados. Ainda pode carregar 20 certificados de raiz.

### <a name="can-i-traverse-proxies-and-firewalls-using-pointtosite-capability"></a>Posso atravessar proxies e firewalls com a capacidade Ponto a Site?
Sim. Utilizamos o SSTP (Secure Socket Tunneling Protocol) para atravessar as firewalls. Este túnel aparece como uma ligação HTTPs.

### <a name="if-i-restart-a-client-computer-configured-for-pointtosite-will-the-vpn-automatically-reconnect"></a>Se reiniciar o computador cliente configurado para Ponto a Site, a VPN restabelece ligação automaticamente?
Por predefinição, o computador cliente não restabelece a ligação VPN automaticamente.

### <a name="does-pointtosite-support-autoreconnect-and-ddns-on-the-vpn-clients"></a>A ligação Ponto a Site suporta o restabelecimento de ligação automático e DDNS nos clientes VPN?
Atualmente, o restabelecimento de ligação automático e DDNS não são suportados nas VPNs Ponto a Site.

### <a name="can-i-have-sitetosite-and-pointtosite-configurations-coexist-for-the-same-virtual-network"></a>Posso ter configurações Site a Site e Ponto a Site coexistentes na mesma rede virtual?
Sim. Ambas as soluções funcionarão se tiver um tipo de VPN RouteBased para o gateway. No modelo de implementação clássica, precisa de um gateway dinâmico. Não suportamos a ligação Ponto a Site para gateways de VPN de encaminhamento estático ou gateways que utilizem -VpnType PolicyBased.

### <a name="can-i-configure-a-pointtosite-client-to-connect-to-multiple-virtual-networks-at-the-same-time"></a>Posso configurar um cliente Ponto a Site para ligar a várias redes virtuais ao mesmo tempo?
Sim, é possível. Mas as redes virtuais não podem ter prefixos IP sobrepostos e os espaços de endereços Ponto a Site não se podem sobrepor nas redes virtuais.

### <a name="how-much-throughput-can-i-expect-through-sitetosite-or-pointtosite-connections"></a>Que débito posso esperar através das ligações Site a Site ou Ponto a Site?
É difícil manter o débito exato dos túneis VPN. O IPsec e SSTP são protocolos VPN extremamente encriptados. O débito também é limitado pela latência e pela largura de banda entre o local e a Internet.

## <a name="gateways"></a>Gateways
### <a name="what-is-a-policybased-staticrouting-gateway"></a>O que é um gateway baseado em políticas (encaminhamento estático)?
Os gateways baseados em políticas implementam VPNs baseadas em políticas. As VPNs baseadas em políticas encriptam e direcionam os pacotes através de túneis IPsec basedos nas combinações de prefixos de endereços entre a rede no local e a VNet do Azure. Normalmente, a política (ou o Seletor de Tráfego), é definido como uma lista de acesso na configuração de VPN.

### <a name="what-is-a-routebased-dynamicrouting-gateway"></a>O que é um gateway baseado na rota (encaminhamento dinâmico)?
Os gateways baseados na rota implementam as VPNs baseadas na rota. As VPNs baseadas na rota utilizam “rotas” no reencaminhamento IP ou na tabela de encaminhamento para direcionar os pacotes para as respetivas interfaces de túnel. As interfaces de túnel, em seguida, encriptam ou desencriptam os pacotes dentro e fora dos túneis. A política ou o seletor de tráfego das VPNs baseadas na rota é configurado como “de qualquer ponto a qualquer ponto” (ou carateres universais).

### <a name="can-i-get-my-vpn-gateway-ip-address-before-i-create-it"></a>Posso obter o meu endereço IP do gateway de VPN antes de o criar?
Não. Tem de criar o gateway primeiro para obter o endereço IP. O endereço IP é alterado se eliminar e voltar a criar o gateway de VPN.

### <a name="how-does-my-vpn-tunnel-get-authenticated"></a>Como é que o meu túnel VPN é autenticado?
A VPN do Azure utiliza a autenticação PSK (Chave Pré-partilhada). Geramos uma chave pré-partilhada (PSK) quando criamos o túnel VPN. Pode alterar a PSK gerada automaticamente pela sua com o cmdlet do PowerShell ou a API REST Definir Chave Pré-Partilhada.

### <a name="can-i-use-the-set-preshared-key-api-to-configure-my-policybased-static-routing-gateway-vpn"></a>Posso utilizar a API Definir Chave Pré-Partilhada para configurar a minha VPN de gateway baseada em políticas (encaminhamento estático)?
Sim, o cmdlet do PowerShell e a API Definir Chave Pré-Partilhada podem ser utilizados para configurar VPNs (estáticas) baseadas em políticas e VPNs de encaminhamento (dinâmico) baseadas em políticas do Azure.

### <a name="can-i-use-other-authentication-options"></a>Posso utilizar outras opções de autenticação?
Estamos limitados à utilização de chaves pré-partilhadas (PSK) para a autenticação.

### <a name="what-is-the-gateway-subnet-and-why-is-it-needed"></a>O que é a “sub-rede do gateway” e por que motivo é necessária?
Temos um serviço de gateway que executamos para ativar a conetividade em vários locais. 

Terá de configurar uma sub-rede do gateway para que a VNet configure um gateway de VPN. Para funcionarem corretamente, todas as sub-redes do gateway têm de ter o nome GatewaySubnet. Não atribua outro nome à sub-rede do gateway. E não implemente VMs ou quaisquer outros elementos na sub-rede do gateway.

O tamanho mínimo da sub-rede do gateway depende totalmente da configuração que pretende criar. Apesar de poder criar uma sub-rede do gateway tão pequena como /29 para algumas configurações, recomendamos que crie uma sub-rede do gateway de /28 ou superior (/ 28, /27, /26, etc.). 

### <a name="can-i-deploy-virtual-machines-or-role-instances-to-my-gateway-subnet"></a>Posso implementar Virtual Machines ou instâncias de função na minha sub-rede do gateway?
Não.

### <a name="how-do-i-specify-which-traffic-goes-through-the-vpn-gateway"></a>Como posso especificar que tráfego atravessa o gateway de VPN?
Se estiver a utilizar o Portal Clássico do Azure, adicione cada intervalo que pretende enviar através do gateway para a rede virtual na página Redes em Redes Locais.

### <a name="can-i-configure-forced-tunneling"></a>Posso configurar a Imposição do Túnel?
Sim. Veja [Configurar imposição do túnel](vpn-gateway-about-forced-tunneling.md).

### <a name="can-i-set-up-my-own-vpn-server-in-azure-and-use-it-to-connect-to-my-onpremises-network"></a>Posso configurar o meu próprio servidor VPN no Azure e utilizá-lo para estabelecer ligação à minha rede no local?
Sim, pode implementar os seus servidores ou gateways de VPN no Azure a partir do Azure Marketplace ou criar os seus próprios routers VPN. Terá de configurar rotas definidas pelo utilizador na rede virtual para garantir que o tráfego é encaminhado corretamente entre as redes no local e as sub-redes da rede virtual.

### <a name="why-are-certain-ports-opened-on-my-vpn-gateway"></a>Por que motivo determinadas portas são abertas no meu gateway de VPN?
São necessárias para a comunicação de infraestrutura do Azure. Estão protegidas (bloqueadas) pelos certificados do Azure. Sem os certificados adequados, as entidades externas, incluindo os clientes desses gateways, não terão qualquer efeito nesses pontos finais.

Um gateway de VPN é, essencialmente, um dispositivo do tipo multi-homed com uma NIC a aceder à rede privada do cliente e uma NIC voltada para a rede pública. As entidades de infraestruturas do Azure não conseguem aceder às redes privadas do cliente por motivos de conformidade, pelo que precisam de utilizar pontos finais públicos para a comunicação de infraestrutura. Os pontos finais públicos são analisados periodicamente pela auditoria de segurança do Azure.

### <a name="more-information-about-gateway-types-requirements-and-throughput"></a>Mais informações sobre tipos de gateways, requisitos e débito
Para obter mais informações, veja [About VPN Gateway Settings (Acerca das Definições dos Gateways de VPN)](vpn-gateway-about-vpn-gateway-settings.md).

## <a name="multisite-and-vnettovnet-connectivity"></a>Conetividade Multilocal e VNet a VNet
### <a name="which-type-of-gateways-can-support-multisite-and-vnettovnet-connectivity"></a>Que tipos de gateways podem suportar a conetividade multilocal e VNet a VNet?
Apenas as VPNs baseadas na rota (encaminhamento dinâmico).

### <a name="can-i-connect-a-vnet-with-a-routebased-vpn-type-to-another-vnet-with-a-policybased-vpn-type"></a>Posso ligar uma VNet com um Tipo de VPN RouteBased a outra VNet com um tipo de VPN PolicyBased?
Não, ambas as redes virtuais TÊM de utilizar VPNs baseadas na rota (encaminhamento dinâmico).

### <a name="is-the-vnettovnet-traffic-secure"></a>O tráfego VNet a VNet é seguro?
Sim, está protegido pela encriptação IPsec/IKE.

### <a name="does-vnettovnet-traffic-travel-over-the-azure-backbone"></a>O tráfego VNet a VNet percorre o backbone do Azure?
Sim.

### <a name="how-many-onpremises-sites-and-virtual-networks-can-one-virtual-network-connect-to"></a>A quantos sites no local e redes virtuais pode uma rede virtual ligar?
Um máximo de dez combinados para gateways de Encaminhamento Dinâmico Básico e Standard; 30 para gateways de VPN de Elevado Desempenho.

### <a name="can-i-use-pointtosite-vpns-with-my-virtual-network-with-multiple-vpn-tunnels"></a>Posso utilizar VPNs Ponto a Site com a minha rede virtual com vários túneis VPN?
Sim, as VPNs Ponto a Site (P2S) podem ser utilizadas com os gateways de VPN a ligar a vários sites no local e a outras redes virtuais.

### <a name="can-i-configure-multiple-tunnels-between-my-virtual-network-and-my-onpremises-site-using-multisite-vpn"></a>Posso configurar vários túneis entre a minha rede virtual e o meu site no local através da VPN multilocal?
Não, os túneis redundantes entre uma rede virtual do Azure e um site no local não são suportados.

### <a name="can-there-be-overlapping-address-spaces-among-the-connected-virtual-networks-and-onpremises-local-sites"></a>Pode existir uma sobreposição de espaços de endereços entre as redes virtuais ligadas e os sites locais no local?
Não. A sobreposição de espaços de endereços fará com que o carregamento do ficheiro de configuração de rede ou a “Criação da Rede Virtual” falhe.

### <a name="do-i-get-more-bandwidth-with-more-sitetosite-vpns-than-for-a-single-virtual-network"></a>Tenho mais largura de banda com mais Redes de VPNs do que para uma única rede virtual?
Não, todos os túneis VPN, incluindo VPNs Ponto a Site, partilham o mesmo gateway de VPN do Azure e a largura de banda disponível.

### <a name="can-i-use-azure-vpn-gateway-to-transit-traffic-between-my-onpremises-sites-or-to-another-virtual-network"></a>Posso utilizar o gateway de VPN do Azure para transitar o tráfego entre os meus sites no local ou para outra rede virtual?
**Modelo de implementação clássica**<br>
É possível transitar o tráfego através do gateway de VPN do Azure com o modelo de implementação clássica, mas tal depende de espaços de endereços definidos estaticamente no ficheiro de configuração de rede. O BGP ainda não é suportado com Redes Virtuais do Azure nem gateways de VPN mediante a utilização do modelo de implementação clássica. Sem o BGP, a definição manual dos espaços de endereços de trânsito é muito propensa a erros e não se recomenda.<br>
**Modelo de implementação Resource Manager**<br>
Se estiver a utilizar o modelo de implementação Resource Manager, veja a secção [BGP](#bgp) para obter mais informações.

### <a name="does-azure-generate-the-same-ipsecike-preshared-key-for-all-my-vpn-connections-for-the-same-virtual-network"></a>O Azure gera a mesma chave pré-partilhada IPsec/IKE para todas as minhas ligações VPN para a mesma rede virtual?
Não, por predefinição, o Azure gera chaves pré-partilhadas diferentes para diferentes ligações VPN. No entanto, pode utilizar o cmdlet do PowerShell ou a API REST Definir Chave de Gateway de VPN para definir o valor da chave que preferir. A chave TEM de ser uma cadeia alfanumérica com um comprimento entre 1 a 128 carateres.

### <a name="does-azure-charge-for-traffic-between-virtual-networks"></a>O Azure cobra o tráfego entre redes virtuais?
Para o tráfego entre várias redes virtuais do Azure, o Azure apenas cobra o tráfego entre uma região do Azure para outra. A taxa cobrada está listada na página [Preços do VPN Gateway](https://azure.microsoft.com/pricing/details/vpn-gateway/) do Azure.

### <a name="can-i-connect-a-virtual-network-with-ipsec-vpns-to-my-expressroute-circuit"></a>Posso ligar uma rede virtual com VPNs IPsec ao meu circuito ExpressRoute?
Sim, esta ação é suportada. Para obter mais informações, veja [Configurar as ligações de Rede de VPNs e o ExpressRoute que coexistem](../expressroute/expressroute-howto-coexist-classic.md).

## <a name="a-namebgpabgp"></a><a name="bgp"></a>BGP
[!INCLUDE [vpn-gateway-bgp-faq-include](../../includes/vpn-gateway-bpg-faq-include.md)]

## <a name="crosspremises-connectivity-and-vms"></a>VMs e conectividade em vários locais
### <a name="if-my-virtual-machine-is-in-a-virtual-network-and-i-have-a-crosspremises-connection-how-should-i-connect-to-the-vm"></a>Se a minha máquina virtual estiver numa rede virtual e tiver uma ligação em vários locais, como devo estabelecer ligação à VM?
Tem algumas opções. Se tiver o RDP ativado e tiver criado um ponto final, pode ligar à máquina virtual através do VIP. Nesse caso, deve especificar o VIP e a porta à qual quer ligar. Terá de configurar a porta na sua máquina virtual para o tráfego. Normalmente, acederia ao Portal Clássico do Azure e guardaria as definições da ligação do RDP no computador. As definições contêm as informações de ligação necessárias.

Se tiver uma rede virtual com a conetividade em vários locais configurada, pode ligar à máquina virtual através do endereço IP privado ou DIP interno. Também pode ligar à máquina virtual através do DIP interno de outra máquina virtual que está localizada na mesma rede virtual. Não pode fazer o RDP à máquina virtual através do DIP se estiver a ligar a partir de uma localização fora da sua rede virtual. Por exemplo, se tiver uma rede virtual Ponto a Site configurada e se não estabelecer ligação a partir do computador, não poderá ligar à máquina virtual através do DIP.

### <a name="if-my-virtual-machine-is-in-a-virtual-network-with-crosspremises-connectivity-does-all-the-traffic-from-my-vm-go-through-that-connection"></a>Se a minha máquina virtual estiver numa rede virtual com conetividade em vários locais, todo o tráfego da minha VM passa por essa ligação?
Não. Apenas o tráfego que tem um IP de destino contido nos intervalos de endereços IP da Rede Local da rede virtual que especificou passará pelo gateway de rede virtual. O tráfego tem um IP de destino localizado na rede virtual e permanece na rede virtual. O outro tipo de tráfego é enviado através do balanceador de carga para as redes públicas, ou se a imposição do túnel for utilizada, é enviado através do gateway de VPN do Azure. Se estiver a resolver problemas, é importante garantir que tem todos os intervalos listados na Rede Local que quer enviar através do gateway. Verifique se os intervalos de endereços da Rede Local não se sobrepõem a qualquer intervalo de endereços na rede virtual. Além disso, deve verificar se o servidor DNS que está a utilizar está a resolver o nome para o endereço IP adequado.

## <a name="virtual-network-faq"></a>FAQ da Rede Virtual
Pode ver informações adicionais sobre a rede virtual nas [FAQ da Rede Virtual](../virtual-network/virtual-networks-faq.md).




<!--HONumber=Nov16_HO2-->


