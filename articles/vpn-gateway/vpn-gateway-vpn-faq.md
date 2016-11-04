---
title: FAQ do VPN Gateway da Rede Virtual | Microsoft Docs
description: FAQ do VPN Gateway. FAQ das ligações em vários locais da Rede Virtual do Microsoft Azure, das ligações da configuração híbrida e dos VPN Gateways
services: vpn-gateway
documentationcenter: na
author: yushwang
manager: rossort
editor: ''

ms.service: vpn-gateway
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/10/2016
ms.author: yushwang

---
# FAQ do VPN Gateway
## Ligar às Redes Virtuais
### Posso ligar redes virtuais em diferentes regiões do Azure?
Sim. Na verdade, não existe qualquer restrição de região. Uma rede virtual pode ligar a outra rede virtual na mesma região ou numa região do Azure diferente.

### Posso ligar redes virtuais em diferentes subscrições?
Sim.

### Posso ligar a vários sites a partir de uma única rede virtual?
Pode ligar a vários sites com o Windows PowerShell e as APIs REST do Azure. Veja a secção das FAQ [Conetividade Multilocal e VNet a VNet](#multi-site-and-vnet-to-vnet-connectivity).

## Quais são as minhas opções de ligação em vários locais?
São suportadas as seguintes ligações em vários locais:

* [Site a Site](vpn-gateway-site-to-site-create.md) – ligação VPN através de IPsec (IKE v1 e IKE v2). Este tipo de ligação requer um dispositivo VPN ou RRAS.
* [Ponto a Site](vpn-gateway-point-to-site-create.md) – ligação VPN através de SSTP (Secure Socket Tunneling Protocol). Esta ligação não requer um dispositivo VPN.
* [VNet a VNet](virtual-networks-configure-vnet-to-vnet-connection.md) – este tipo de ligação é igual a uma configuração Site a Site. A ligação VNet a VNet é uma ligação VPN através de IPsec (IKE v1 e IKE v2). Não requer um dispositivo VPN.
* [Multilocal](vpn-gateway-multi-site.md) – é uma variação de uma configuração Site a Site que lhe permite ligar vários sites no local a uma rede virtual.
* [ExpressRoute](../expressroute/expressroute-introduction.md) – o ExpressRoute é uma ligação direta ao Azure a partir da sua WAN e não através da Internet pública. Veja a [Descrição Geral Técnica do ExpressRoute](../expressroute/expressroute-introduction.md) e as [FAQ do ExpressRoute](../expressroute/expressroute-faqs.md) para obter mais informações.

Para obter mais informações sobre as ligações, veja [Acerca do Gateway de VPN](vpn-gateway-about-vpngateways.md).

### Qual é a diferença entre uma ligação Site a Site e Ponto a Site?
As ligações **Site a Site** permitem estabelecer ligação entre qualquer um dos computadores localizados no local a qualquer máquina virtual ou instância de função na sua rede virtual, dependendo de como escolher configurar o encaminhamento. É uma excelente opção para uma ligação em vários locais sempre disponível e é adequada para configurações híbridas. Este tipo de ligação depende de uma aplicação VPN IPsec (aplicação de hardware ou software), que tem de ser implementada na periferia da sua rede. Para criar este tipo de ligação, terá de ter o hardware VPN necessário e um endereço IPv4 com acesso exterior.

As ligações **Ponto a Site** permitem ligar a partir de um único computador em qualquer lugar a qualquer destino localizado na sua rede virtual. Utiliza o cliente VPN fornecido pelo Windows. Como parte da configuração Ponto a Site, instale um certificado e um pacote de configuração do cliente VPN, que contém as definições que permitem que o computador estabeleça ligação a qualquer máquina virtual ou instância de função na rede virtual. É ótimo quando pretende estabelecer ligação a uma rede virtual, mas não está no local. Também é uma boa opção quando não tiver acesso ao hardware VPN ou a um endereço IPv4 com acesso exterior, que são necessários para uma ligação Site a Site. 

Pode configurar a rede virtual para utilizar o Site a Site e o Ponto a Site em simultâneo, desde que crie a ligação Site a Site através de um tipo de VPN baseado na rota do seu gateway. Os tipos de VPN com base na rota são denominados gateways dinâmicos no modelo de implementação clássica.

### O que é o ExpressRoute?
O ExpressRoute permite-lhe criar ligações privadas entre os datacenters da Microsoft e a infraestrutura no local ou num ambiente de colocalização. Com o ExpressRoute, pode estabelecer ligações a serviços em nuvem da Microsoft, tais como o Microsoft Azure e o Office 365 numa instalação de colocalização de parceiros ExpressRoute ou ligar diretamente da sua rede WAN existente (por exemplo, uma VPN MPLS fornecida por um fornecedor de serviços de rede).

As ligações ExpressRoute oferecem maior segurança, mais fiabilidade, largura de banda superior e latências inferiores do que as ligações típicas através da Internet. Em alguns casos, utilizar ligações ExpressRoute para transferir dados entre a rede no local e o Azure também pode resultar em vantagens significativas ao nível dos custos. Se já tiver criado uma ligação em vários locais a partir da rede no local para o Azure, pode migrar para uma ligação ExpressRoute mantendo a rede virtual intacta.

Veja [FAQ do ExpressRoute](../expressroute/expressroute-faqs.md) para obter mais detalhes.

## Ligações Site a Site e dispositivos VPN
### O que devo considerar ao selecionar um dispositivo VPN?
Validamos um conjunto de dispositivos de Rede de VPNs padrão em parceria com fornecedores de dispositivos. Pode encontrar uma lista de dispositivos VPN compatíveis conhecidos, exemplos ou instruções de configuração correspondentes e especificações de dispositivo [aqui](vpn-gateway-about-vpn-devices.md). Todos os dispositivos das famílias de dispositivos listadas como compatíveis devem funcionar com a Rede Virtual. Para ajudar a configurar o dispositivo VPN, consulte o exemplo de configuração do dispositivo ou a ligação que corresponde a uma família de dispositivos adequados.

### O que posso fazer se tiver um dispositivo VPN que não esteja na lista de dispositivos compatíveis conhecidos?
Se não vir o seu dispositivo listado como um dispositivo VPN compatível conhecido e pretender utilizá-lo para a ligação de VPN, terá de verificar que cumpre os parâmetros e as opções de configuração do IPsec/IKE suportados listados [aqui](vpn-gateway-about-vpn-devices.md#devices-not-on-the-compatible-list). Os dispositivos que cumprem os requisitos mínimos devem funcionar bem com gateways de VPN. Contacte o fabricante do dispositivo para obter instruções adicionais de suporte e de configuração.

### Por que motivo o meu túnel VPN baseado em políticas diminui quando o tráfego está inativo?
Este comportamento está previsto para gateways de VPN baseados em políticas (também conhecido como encaminhamento estático). Quando o tráfego através do túnel estiver inativo durante mais de 5 minutos, o túnel é desativado. Quando o tráfego começar a fluir em qualquer direção, o túnel é restabelecido imediatamente. Se tiver um gateway de VPN baseado na rota (também conhecido como dinâmico), não se verifica este comportamento.

### Posso utilizar VPNs de software para ligar ao Azure?
Suportamos servidores de Encaminhamento e Acesso Remoto (RRAS) do Windows Server 2012 para uma configuração em vários locais Site a Site.

Outras soluções de VPN de software devem funcionar com o nosso gateway, desde que obedeçam às implementações de IPsec de norma da indústria. Contacte o fornecedor do software para as obter instruções de configuração e de suporte.

## Ligações Ponto a Site
### Que sistemas operativos posso utilizar com a ligação Ponto a Site?
São suportados os seguintes sistemas operativos:

* Windows 7 (32 e 64 bits)
* Windows Server 2008 R2 (apenas 64 bits)
* Windows 8 (32 e 64 bits)
* Windows 8.1 (32 e 64 bits)
* Windows Server 2012 (apenas 64 bits)
* Windows Server 2012 R2 (apenas 64 bits)
* Windows 10

### Posso utilizar qualquer cliente VPN de software para a ligação Ponto a Site que suporte SSTP?
Não. O suporte está limitado apenas às versões do sistema operativo Windows indicadas acima.

### Quantos pontos finais de cliente VPN posso ter na minha configuração Ponto a Site?
Suportamos até 128 clientes VPN para conseguir ligar a uma rede virtual ao mesmo tempo.

### Posso utilizar AC de raiz de PKI interna para a conetividade Ponto a Site?
Sim. Anteriormente, só podiam ser utilizados os certificados de raiz autoassinados. Ainda pode carregar 20 certificados de raiz.

### Posso atravessar proxies e firewalls com a capacidade Ponto a Site?
Sim. Utilizamos o SSTP (Secure Socket Tunneling Protocol) para atravessar as firewalls. Este túnel aparece como uma ligação HTTPs.

### Se reiniciar o computador cliente configurado para Ponto a Site, a VPN restabelece ligação automaticamente?
Por predefinição, o computador cliente não restabelece a ligação VPN automaticamente.

### A ligação Ponto a Site suporta o restabelecimento de ligação automático e DDNS nos clientes VPN?
Atualmente, o restabelecimento de ligação automático e DDNS não são suportados nas VPNs Ponto a Site.

### Posso ter configurações Site a Site e Ponto a Site coexistentes na mesma rede virtual?
Sim. Ambas as soluções funcionarão se tiver um tipo de VPN RouteBased para o gateway. No modelo de implementação clássica, precisa de um gateway dinâmico. Não suportamos a ligação Ponto a Site para gateways de VPN de encaminhamento estático ou gateways que utilizem -VpnType PolicyBased.

### Posso configurar um cliente Ponto a Site para ligar a várias redes virtuais ao mesmo tempo?
Sim, é possível. Mas as redes virtuais não podem ter prefixos IP sobrepostos e os espaços de endereços Ponto a Site não se podem sobrepor nas redes virtuais.

### Que débito posso esperar através das ligações Site a Site ou Ponto a Site?
É difícil manter o débito exato dos túneis VPN. O IPsec e SSTP são protocolos VPN extremamente encriptados. O débito também é limitado pela latência e pela largura de banda entre o local e a Internet.

## Gateways
### O que é um gateway baseado em políticas (encaminhamento estático)?
Os gateways baseados em políticas implementam VPNs baseadas em políticas. As VPNs baseadas em políticas encriptam e direcionam os pacotes através de túneis IPsec basedos nas combinações de prefixos de endereços entre a rede no local e a VNet do Azure. Normalmente, a política (ou o Seletor de Tráfego), é definido como uma lista de acesso na configuração de VPN.

### O que é um gateway baseado na rota (encaminhamento dinâmico)?
Os gateways baseados na rota implementam as VPNs baseadas na rota. As VPNs baseadas na rota utilizam “rotas” no reencaminhamento IP ou na tabela de encaminhamento para direcionar os pacotes para as respetivas interfaces de túnel. As interfaces de túnel, em seguida, encriptam ou desencriptam os pacotes dentro e fora dos túneis. A política ou o seletor de tráfego das VPNs baseadas na rota é configurado como “de qualquer ponto a qualquer ponto” (ou carateres universais).

### Posso obter o meu endereço IP do gateway de VPN antes de o criar?
Não. Tem de criar o gateway primeiro para obter o endereço IP. O endereço IP é alterado se eliminar e voltar a criar o gateway de VPN.

### Como é que o meu túnel VPN é autenticado?
A VPN do Azure utiliza a autenticação PSK (Chave Pré-partilhada). Geramos uma chave pré-partilhada (PSK) quando criamos o túnel VPN. Pode alterar a PSK gerada automaticamente pela sua com o cmdlet do PowerShell ou a API REST Definir Chave Pré-Partilhada.

### Posso utilizar a API Definir Chave Pré-Partilhada para configurar a minha VPN de gateway baseada em políticas (encaminhamento estático)?
Sim, o cmdlet do PowerShell e a API Definir Chave Pré-Partilhada podem ser utilizados para configurar VPNs (estáticas) baseadas em políticas e VPNs de encaminhamento (dinâmico) baseadas em políticas do Azure.

### Posso utilizar outras opções de autenticação?
Estamos limitados à utilização de chaves pré-partilhadas (PSK) para a autenticação.

### O que é a “sub-rede do gateway” e por que motivo é necessária?
Temos um serviço de gateway que executamos para ativar a conetividade em vários locais. 

Terá de configurar uma sub-rede do gateway para que a VNet configure um gateway de VPN. Para funcionarem corretamente, todas as sub-redes do gateway têm de ter o nome GatewaySubnet. Não atribua outro nome à sub-rede do gateway. E não implemente VMs ou quaisquer outros elementos na sub-rede do gateway.

O tamanho mínimo da sub-rede do gateway depende totalmente da configuração que pretende criar. Apesar de poder criar uma sub-rede do gateway tão pequena como /29 para algumas configurações, recomendamos que crie uma sub-rede do gateway de /28 ou superior (/ 28, /27, /26, etc.). 

### Posso implementar Virtual Machines ou instâncias de função na minha sub-rede do gateway?
Não.

### Como posso especificar que tráfego atravessa o gateway de VPN?
Se estiver a utilizar o Portal Clássico do Azure, adicione cada intervalo que pretende enviar através do gateway para a rede virtual na página Redes em Redes Locais.

### Posso configurar a Imposição do Túnel?
Sim. Veja [Configurar imposição do túnel](vpn-gateway-about-forced-tunneling.md).

### Posso configurar o meu próprio servidor VPN no Azure e utilizá-lo para estabelecer ligação à minha rede no local?
Sim, pode implementar os seus servidores ou gateways de VPN no Azure a partir do Azure Marketplace ou criar os seus próprios routers VPN. Terá de configurar rotas definidas pelo utilizador na rede virtual para garantir que o tráfego é encaminhado corretamente entre as redes no local e as sub-redes da rede virtual.

### Por que motivo determinadas portas são abertas no meu gateway de VPN?
São necessárias para a comunicação de infraestrutura do Azure. Estão protegidas (bloqueadas) pelos certificados do Azure. Sem os certificados adequados, as entidades externas, incluindo os clientes desses gateways, não terão qualquer efeito nesses pontos finais.

Um gateway de VPN é, essencialmente, um dispositivo do tipo multi-homed com uma NIC a aceder à rede privada do cliente e uma NIC voltada para a rede pública. As entidades de infraestruturas do Azure não conseguem aceder às redes privadas do cliente por motivos de conformidade, pelo que precisam de utilizar pontos finais públicos para a comunicação de infraestrutura. Os pontos finais públicos são analisados periodicamente pela auditoria de segurança do Azure.

### Mais informações sobre tipos de gateways, requisitos e débito
Para obter mais informações, veja [About VPN Gateway Settings (Acerca das Definições dos Gateways de VPN)](vpn-gateway-about-vpn gateway-settings.md).

## Conetividade Multilocal e VNet a VNet
### Que tipos de gateways podem suportar a conetividade multilocal e VNet a VNet?
Apenas as VPNs baseadas na rota (encaminhamento dinâmico).

### Posso ligar uma VNet com um Tipo de VPN RouteBased a outra VNet com um tipo de VPN PolicyBased?
Não, ambas as redes virtuais TÊM de utilizar VPNs baseadas na rota (encaminhamento dinâmico).

### O tráfego VNet a VNet é seguro?
Sim, está protegido pela encriptação IPsec/IKE.

### O tráfego VNet a VNet percorre o backbone do Azure?
Sim.

### A quantos sites no local e redes virtuais pode uma rede virtual ligar?
Um máximo de dez combinados para gateways de Encaminhamento Dinâmico Básico e Standard; 30 para gateways de VPN de Elevado Desempenho.

### Posso utilizar VPNs Ponto a Site com a minha rede virtual com vários túneis VPN?
Sim, as VPNs Ponto a Site (P2S) podem ser utilizadas com os gateways de VPN a ligar a vários sites no local e a outras redes virtuais.

### Posso configurar vários túneis entre a minha rede virtual e o meu site no local através da VPN multilocal?
Não, os túneis redundantes entre uma rede virtual do Azure e um site no local não são suportados.

### Pode existir uma sobreposição de espaços de endereços entre as redes virtuais ligadas e os sites locais no local?
Não. A sobreposição de espaços de endereços fará com que o carregamento do ficheiro de configuração de rede ou a “Criação da Rede Virtual” falhe.

### Tenho mais largura de banda com mais Redes de VPNs do que para uma única rede virtual?
Não, todos os túneis VPN, incluindo VPNs Ponto a Site, partilham o mesmo gateway de VPN do Azure e a largura de banda disponível.

### Posso utilizar o gateway de VPN do Azure para transitar o tráfego entre os meus sites no local ou para outra rede virtual?
**Modelo de implementação clássica**<br>
É possível transitar o tráfego através do gateway de VPN do Azure com o modelo de implementação clássica, mas tal depende de espaços de endereços definidos estaticamente no ficheiro de configuração de rede. O BGP ainda não é suportado com Redes Virtuais do Azure nem gateways de VPN mediante a utilização do modelo de implementação clássica. Sem o BGP, a definição manual dos espaços de endereços de trânsito é muito propensa a erros e não se recomenda.<br>
**Modelo de implementação Resource Manager**<br>
Se estiver a utilizar o modelo de implementação Resource Manager, veja a secção [BGP](#bgp) para obter mais informações.

### O Azure gera a mesma chave pré-partilhada IPsec/IKE para todas as minhas ligações VPN para a mesma rede virtual?
Não, por predefinição, o Azure gera chaves pré-partilhadas diferentes para diferentes ligações VPN. No entanto, pode utilizar o cmdlet do PowerShell ou a API REST Definir Chave de Gateway de VPN para definir o valor da chave que preferir. A chave TEM de ser uma cadeia alfanumérica com um comprimento entre 1 a 128 carateres.

### O Azure cobra o tráfego entre redes virtuais?
Para o tráfego entre várias redes virtuais do Azure, o Azure apenas cobra o tráfego entre uma região do Azure para outra. A taxa cobrada está listada na página [Preços do VPN Gateway](https://azure.microsoft.com/pricing/details/vpn-gateway/) do Azure.

### Posso ligar uma rede virtual com VPNs IPsec ao meu circuito ExpressRoute?
Sim, esta ação é suportada. Para obter mais informações, veja [Configurar as ligações de Rede de VPNs e o ExpressRoute que coexistem](../expressroute/expressroute-howto-coexist-classic.md).

## <a name="bgp"></a>BGP
[!INCLUDE [vpn-gateway-bgp-faq-include](../../includes/vpn-gateway-bpg-faq-include.md)]

## VMs e conectividade em vários locais
### Se a minha máquina virtual estiver numa rede virtual e tiver uma ligação em vários locais, como devo estabelecer ligação à VM?
Tem algumas opções. Se tiver o RDP ativado e tiver criado um ponto final, pode ligar à máquina virtual através do VIP. Nesse caso, deve especificar o VIP e a porta à qual quer ligar. Terá de configurar a porta na sua máquina virtual para o tráfego. Normalmente, acederia ao Portal Clássico do Azure e guardaria as definições da ligação do RDP no computador. As definições contêm as informações de ligação necessárias.

Se tiver uma rede virtual com a conetividade em vários locais configurada, pode ligar à máquina virtual através do endereço IP privado ou DIP interno. Também pode ligar à máquina virtual através do DIP interno de outra máquina virtual que está localizada na mesma rede virtual. Não pode fazer o RDP à máquina virtual através do DIP se estiver a ligar a partir de uma localização fora da sua rede virtual. Por exemplo, se tiver uma rede virtual Ponto a Site configurada e se não estabelecer ligação a partir do computador, não poderá ligar à máquina virtual através do DIP.

### Se a minha máquina virtual estiver numa rede virtual com conetividade em vários locais, todo o tráfego da minha VM passa por essa ligação?
Não. Apenas o tráfego que tem um IP de destino contido nos intervalos de endereços IP da Rede Local da rede virtual que especificou passará pelo gateway de rede virtual. O tráfego tem um IP de destino localizado na rede virtual e permanece na rede virtual. O outro tipo de tráfego é enviado através do balanceador de carga para as redes públicas, ou se a imposição do túnel for utilizada, é enviado através do gateway de VPN do Azure. Se estiver a resolver problemas, é importante garantir que tem todos os intervalos listados na Rede Local que quer enviar através do gateway. Verifique se os intervalos de endereços da Rede Local não se sobrepõem a qualquer intervalo de endereços na rede virtual. Além disso, deve verificar se o servidor DNS que está a utilizar está a resolver o nome para o endereço IP adequado.

## FAQ da Rede Virtual
Pode ver informações adicionais sobre a rede virtual nas [FAQ da Rede Virtual](../virtual-network/virtual-networks-faq.md).

<!--HONumber=ago16_HO5-->


