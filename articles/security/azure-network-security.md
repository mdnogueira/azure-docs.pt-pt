---
title: "Segurança de rede do Azure | Microsoft Docs"
description: "Saiba mais sobre serviços informáticos baseada na nuvem que incluem uma seleção grande de instâncias de computação e serviços que podem ser dimensionados acima e abaixo automaticamente para satisfazer as necessidades da sua aplicação ou a empresa."
services: security
documentationcenter: na
author: UnifyCloud
manager: swadhwa
editor: TomSh
ms.assetid: 
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/21/2017
ms.author: TomSh
ms.openlocfilehash: f0cc1716daa70bf7c860373819568774cf6f95d9
ms.sourcegitcommit: 62eaa376437687de4ef2e325ac3d7e195d158f9f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/22/2017
---
# <a name="azure-network-security"></a>Segurança de rede do Azure

Sabemos que a segurança está tarefa um na nuvem e como importante é que encontrará exatas e atempadas informações sobre a segurança do Azure. Uma das razões melhor a utilizar o Azure para as suas aplicações e serviços é tirar partido grande matriz do Azure capacidades e ferramentas de segurança. Estas ferramentas e capacidades ajudam possibilitam a criar soluções seguras na plataforma do Azure.

O Microsoft Azure oferece confidencialidade, integridade e disponibilidade dos dados de cliente, permitindo também accountability transparente. Para ajudar a compreender melhor a coleção de controlos de segurança de rede implementado no Microsoft Azure a partir da perspetiva do cliente, este artigo, "Azure" segurança de rede, é escrito para fornecer uma abrangente vista de olhos a rede controlos de segurança disponível com o Microsoft Azure.

Este documento destina-se para informar sobre a vasta gama de controlos de rede que pode configurar para melhorar a segurança das soluções de que implementar no Azure. Se estiver interessado em que a Microsoft faz a proteger os recursos de infraestrutura de rede da própria plataforma do Azure, consulte a secção de segurança do Azure no [Microsoft Trust Center](https://www.microsoft.com/trustcenter/security/azure-security).

## <a name="azure-platform"></a>Plataforma Azure

Azure é uma plataforma de serviço de nuvem pública que suporta uma seleção abrangente dos sistemas operativos, programação idiomas, estruturas, ferramentas, bases de dados e dispositivos.  Pode ser executado contentores de Linux com a integração do Docker; criar aplicações com JavaScript, Python, .NET, PHP, Java e Node.js; compilação back-ends para iOS, Android e Windows dispositivos. Cloud services do Azure suportam as mesmas tecnologias milhões de programadores e profissionais de TI já dependem e de confiança.

Quando criar ou migrar os recursos IT à, um fornecedor de serviços de nuvem pública, é depender capacidades da organização para proteger as suas aplicações e dados com os serviços e os controlos fornecem para gerir a segurança dos seus recursos baseados na nuvem.

Infraestrutura do Azure foi concebida de instalação de aplicações para o alojamento de milhões de clientes em simultâneo, e fornece uma base de confiança no qual as empresas podem cumprir os seus requisitos de segurança. Além disso, Azure fornece uma coleção de um vasto conjunto de opções de segurança configuráveis e a capacidade de controlá-las, de modo a que possa personalizar a segurança para satisfazer os requisitos exclusivos de implementações da sua organização.

## <a name="abstract"></a>Abstrato

Serviços de nuvem pública da Microsoft fornecem serviços de hiper escala e infraestrutura, as capacidades de nível empresarial e muitas opções para conectividade híbrida. Pode escolher aceder a esses serviços através da Internet ou com o ExpressRoute do Azure, que fornece a conectividade de rede privada. A plataforma Microsoft Azure permite-lhe de forma totalmente integrada alargar a infraestrutura para a nuvem e criar arquiteturas de várias camadas. Além disso, terceiros pode ativar avançadas de capacidades por oferta de serviços de segurança e de aplicações virtuais.

Serviços de rede do Azure maximizem flexibilidade, disponibilidade, resiliência, segurança e integridade por predefinição. Este documento técnico fornece detalhes sobre as funções do Azure e obter informações sobre como os clientes podem utilizar funcionalidades de segurança nativa do Azure para ajudar a proteger os ativos de informações de rede.

O público pretendido para este documento inclui:

- Gestores de técnicos, os administradores de rede e os programadores que estão a procurar soluções de segurança disponíveis e suportadas no Azure.

-   SMEs ou os executivos de processo de empresas que pretendem obter uma descrição geral de alto nível, as tecnologias de redes do Azure e os serviços que são relevantes em discussões em torno da segurança de rede na nuvem pública do Azure.

## <a name="azure-networking-big-picture"></a>Visão geral de redes do Azure
Microsoft Azure inclui uma infraestrutura robusta de rede para suportar a sua aplicação e os requisitos de conectividade do serviço. Conectividade de rede é possível entre os recursos localizados no Azure, no local e Azure alojadas recursos e de e para a Internet e o Azure.

![Visão geral de redes do Azure](media/azure-network-security/azure-network-security-fig-1.png)

O [infraestrutura de rede do Azure](https://docs.microsoft.com/azure/virtual-machines/windows/infrastructure-networking-guidelines) permite-lhe ligar de forma segura a recursos do Azure uns aos outros com redes virtuais (VNets). Uma VNet é uma representação da sua própria rede na nuvem. Uma VNet é um isolamento lógico da rede em nuvem do Azure dedicado à sua subscrição. Pode ligar VNets para as suas redes no local.

Suporte do Azure dedicada a conectividade da ligação WAN para a sua rede no local e uma rede Virtual do Azure com [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction). A ligação entre o Azure e o seu site utiliza uma ligação dedicada que passam para a Internet pública. Se a aplicação do Azure está em execução em vários datacenters, pode utilizar [Traffic Manager do Azure](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-overview) para pedidos de rota de utilizadores inteligentemente entre instâncias da aplicação. Também pode encaminhar o tráfego para serviços não em execução no Azure se que estejam acessíveis a partir da Internet.

## <a name="enterprise-view-of-azure-networking-components"></a>Vista de Enterprise dos componentes de rede do Azure
O Azure tem muitos componentes de rede que são relevantes para debates de segurança de rede. Vamos descrever estes componentes de rede e concentrar-se a problemas de segurança relacionadas com as mesmas.

> [!Note]
> Nem todos os aspetos do funcionamento em rede do Azure são descritos – Vamos discutir apenas os que são considerados como estando pivotal planear e conceber uma infraestrutura de rede segura em torno dos seus serviços e aplicações que implementar no Azure.

Neste documento, será abrange o seguinte Azure redes capacidades empresariais:

-   Conectividade de rede básica

-   Conectividade híbrida

-   Controlos de segurança

-   Validar a rede

### <a name="basic-network-connectivity"></a>Conectividade de rede básica

O [Azure Virtual Network](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview) serviço permite-lhe ligar de forma segura a recursos do Azure uns aos outros com redes virtuais (VNet). Uma VNet é uma representação da sua própria rede na nuvem. Uma VNet é um isolamento lógico da infraestrutura de rede do Azure dedicado à sua subscrição. Também pode ligar VNets entre si e para as redes no local com VPNs de site a site e dedicado [ligações WAN](https://docs.microsoft.com/azure/expressroute/expressroute-introduction).

![Conectividade de rede básica](media/azure-network-security/azure-network-security-fig-2.png)

Com a compreender que utilizam as VMs para servidores de anfitrião no Azure, a questão não é como dessas VMs ligam a uma rede. A resposta é que as VMs estabelecer ligação a um [Azure Virtual Network](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview).

Redes virtuais do Azure são como virtual redes utilizam no local com as suas próprias soluções de plataforma de virtualização, como o Microsoft Hyper-V ou VMware.

#### <a name="intra-vnet-connectivity"></a>Conectividade intra VNet

Pode ligar VNets entre si, permitindo recursos ligados a qualquer das Vnets para comunicar entre si entre VNets. Pode utilizar uma ou ambas as opções seguintes para ligar VNets entre si:

- **Peering:** permite recursos ligados a diferentes as VNets do Azure na mesma localização do Azure para comunicar entre si. A largura de banda e latência entre a VNet é o mesmo como se estivessem ligados os recursos para a mesma VNet. Para saber mais sobre o peering, leia [peering de rede Virtual](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview).

 ![Peering](media/azure-network-security/azure-network-security-fig-3.png)

- **Ligação VNet a VNet:** permite recursos ligados a VNet do Azure diferente dentro as localizações do Azure idêntica ou diferentes. Ao contrário de peering, largura de banda é limitada entre VNets porque o tráfego tem de fluir através de um Gateway de VPN do Azure.

![Ligação VNet a VNet](media/azure-network-security/azure-network-security-fig-4.png)


Para saber mais sobre como ligar VNets com uma ligação VNet a VNet, leia o [configurar um artigo de ligação de VNet a VNet](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal?toc=%2fazure%2fvirtual-network%2ftoc.json).

#### <a name="azure-virtual-network-capabilities"></a>Capacidades de rede virtual do Azure:

Como pode ver, uma rede Virtual do Azure fornece máquinas virtuais para ligar à rede para que o se possam ligar a outros recursos de rede de forma segura. No entanto, a conectividade básica é apenas o início. As seguintes funcionalidades do serviço de rede Virtual do Azure expõem as características de segurança da rede Virtual do Azure:

-   Isolamento

-   Conectividade Internet

-   Conectividade de recursos do Azure

-   Conectividade de VNet

-   Conectividade no local

-   Filtragem de tráfego

-   Encaminhamento

**Isolamento**

As VNets estão [isolado](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview) entre si. Pode criar VNets separadas para o desenvolvimento, teste e produção, que utilizam o mesmo [CIDR](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing) blocos de endereços. Por outro lado, pode criar várias VNets que utilizam diferentes blocos de endereços CIDR e ligar redes em conjunto. Pode segmentar uma VNet em várias sub-redes.

O Azure oferece resolução dos nomes internos para VMs e [serviços em nuvem](https://azure.microsoft.com/services/cloud-services/) instâncias de função ligado a uma VNet. Opcionalmente, pode configurar uma VNet para utilizar os seus próprios servidores DNS, em vez de utilizar a resolução do nome interno do Azure.

Pode implementar várias VNets dentro de cada Azure [subscrição](https://docs.microsoft.com/azure/azure-glossary-cloud-terminology?toc=%2fazure%2fvirtual-network%2ftoc.json) e o Azure [região](https://docs.microsoft.com/azure/azure-glossary-cloud-terminology?toc=%2fazure%2fvirtual-network%2ftoc.json). Cada VNet está isolado de outras VNets. Para cada VNet pode:

-   Especifique um espaço de endereços IP privado personalizado utilizando endereços (RFC 1918) públicos e privados. Recursos do Azure atribui ligado para a VNet um endereço IP privado do espaço de endereço, atribuir.

-   Segmentar a VNet numa ou mais sub-redes e atribuir uma parte do espaço de endereços VNet para cada sub-rede.

-   Utilize a resolução de nome fornecidos pelo Azure ou especificar o próprio servidor DNS para utilização por recursos ligados a uma VNet. Para obter mais informações sobre resolução de nomes em VNets, leia o [a resolução de nomes para VMs e serviços em nuvem](https://docs.microsoft.com/azure/virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances).

**Ligação à Internet**

Todos os [máquinas virtuais do Azure (VM)](https://docs.microsoft.com/azure/virtual-machines/windows/) e instâncias de função de serviços em nuvem ligadas a uma VNet têm acesso à Internet, por predefinição. Também pode ativar o acesso de entrada a recursos específicos, conforme necessário. (VM) e instâncias de função de serviços em nuvem ligadas a uma VNet têm acesso à Internet, por predefinição. Também pode ativar o acesso de entrada a recursos específicos, conforme necessário.

Todos os recursos ligados a uma VNet tem conectividade à Internet de saída por predefinição. O endereço IP privado do recurso é origem endereço de rede traduzido (realizar o SNAT) para um endereço IP público a infraestrutura do Azure. Pode alterar a conectividade da predefinição através da implementação de encaminhamento personalizado e filtragem de tráfego. Para saber mais sobre a ligação à Internet de saída, leia o [Noções sobre ligações de saída no Azure](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections?toc=%2fazure%2fvirtual-network%2ftoc.json).

Para comunicar entrada nos recursos do Azure através da Internet, ou para comunicar a saída à Internet sem realizar o SNAT, um recurso deve ser atribuído um endereço IP público. Para saber mais sobre os endereços IP públicos, leia o [endereços IP públicos](https://docs.microsoft.com/azure/virtual-network/virtual-network-public-ip-address).

**Conectividade de recursos do Azure**

[Recursos do Azure](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview) , tais como serviços em nuvem e as VMs podem ser ligadas para a mesma VNet. Os recursos podem ligar-se entre si utilizando endereços IP privados, mesmo que se encontrem em sub-redes diferentes. O Azure oferece encaminhamento predefinido entre sub-redes, VNets e redes no local, pelo que não tem de configurar e gerir as rotas.

Pode ligar a vários recursos do Azure para uma VNet, tais como máquinas virtuais (VM), Cloud Services, ambientes do App Service e conjuntos de dimensionamento de Máquina Virtual. VMs ligar a uma sub-rede dentro de uma VNet através de uma interface de rede (NIC). Para saber mais sobre NICs, leia o [interfaces de rede](https://docs.microsoft.com/azure/virtual-network/virtual-network-network-interface).

**Conectividade de VNet**

[As VNets](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview) podem ser ligadas entre si, permitindo recursos ligados a qualquer VNet para comunicar com qualquer recurso no qualquer outra VNet.

Pode ligar VNets entre si, permitindo recursos ligados a qualquer das Vnets para comunicar entre si entre VNets. Pode utilizar uma ou ambas as opções seguintes para ligar VNets entre si:

- **Peering:** permite recursos ligados a diferentes as VNets do Azure na mesma localização do Azure para comunicar entre si. A largura de banda e latência entre as VNets é o mesmo como se estivessem ligados os recursos para o mesmo VNet.To mais informações sobre peering, leia o [peering de rede Virtual](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview).

- **Ligação VNet a VNet:** permite recursos ligados a VNet do Azure diferente dentro as localizações do Azure idêntica ou diferentes. Ao contrário de peering, largura de banda é limitada entre VNets porque o tráfego tem de fluir através de um Gateway de VPN do Azure. Para obter mais informações sobre como ligar VNets com uma ligação VNet a VNet. Para obter mais informações, leia o [configurar uma ligação VNet a VNet](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal?toc=%2fazure%2fvirtual-network%2ftoc.json) .

**Conectividade no local**

As VNets podem ser ligadas para [no local](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview) redes através de ligações de rede privada entre a rede e o Azure ou através de uma ligação de VPN de site a site através da Internet.

Pode ligar a sua rede no local para uma VNet com qualquer combinação das seguintes opções:

- **Ponto a site rede privada virtual (VPN):** estabelecida entre um único PC à sua rede e a VNet. Este tipo de ligação é excelente se de que está a começar com o Azure ou para os programadores, porque necessita de pouca ou nenhuma alterações à sua rede existente. A ligação utiliza o protocolo SSTP para fornecer comunicações encriptadas através da Internet entre o computador e a VNet. A latência de uma VPN ponto a site é imprevisível, uma vez que o tráfego atravessar da Internet.

- **VPN de site para site:** estabelecida entre o dispositivo VPN e um Gateway de VPN do Azure. Este tipo de ligação permite qualquer recurso no local que está a autorizar para aceder a uma VNet. A ligação é uma VPN IPsec/IKE que fornece comunicações encriptadas através da Internet entre o dispositivo no local e o gateway de VPN do Azure. A latência de uma ligação site a site é imprevisível, uma vez que o tráfego atravessar da Internet.

- **O ExpressRoute do Azure:** estabelecida entre a rede e o Azure, através de um parceiro do ExpressRoute. Esta ligação é privada. Tráfego atravessar não da Internet. A latência de uma ligação ExpressRoute é previsível, uma vez que o tráfego não atravessar da Internet. Para saber mais sobre todas as opções de ligação anterior, leia o [diagramas de topologia de ligação](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways?toc=%2fazure%2fvirtual-network%2ftoc.json).

**Filtragem do tráfego**

Instâncias de função da VM e serviços em nuvem [tráfego de rede](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview) pode ser filtrada entrada e saída por endereço IP de origem e porta, endereço IP de destino e porta e protocolo.

Pode filtrar o tráfego de rede entre as sub-redes utilizando um ou ambos das seguintes opções:

- **Rede (NSG) de grupos de segurança:** cada NSG pode conter várias regras de segurança de entrada e saída que permitem filtrar o tráfego por endereço IP de origem e de destino, porta e protocolo. Pode aplicar um NSG para cada NIC numa VM. Também pode aplicar um NSG para sub-rede um NIC ou outros recursos do Azure, está ligado. Para saber mais sobre NSGs, leia o [grupos de segurança de rede](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg).

- **Os dispositivos de rede virtuais:** um dispositivo de rede virtual está numa VM a executar o software que executa uma função de rede, como uma firewall. Ver uma lista de NVAs disponíveis no Azure Marketplace. NVAs estão também disponíveis, que fornecem a otimização de WAN e outra rede funções de tráfego. NVAs são normalmente utilizadas com definido pelo utilizador ou as rotas BGP. Também pode utilizar uma NVA para filtrar o tráfego entre as VNets.

**Encaminhamento**

Opcionalmente, pode substituir predefinição do Azure encaminhamento por configurar as seus próprios rotas ou utilizar rotas BGP através de um gateway de rede.

O Azure cria as tabelas de rotas ativar recursos ligados a nenhuma sub-rede qualquer VNet para comunicar entre si, por predefinição. Pode implementar um ou ambos das seguintes opções para substituir as rotas predefinidas que Azure cria:

- **Rotas definidas pelo utilizador:** pode criar as tabelas de rotas personalizadas com rotas esse controlo onde o tráfego é encaminhado para cada sub-rede. Para saber mais sobre as rotas definidas pelo utilizador, leia o [rotas definidas pelo utilizador](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview).

- **Rotas BGP:** se ligar a VNet à sua rede no local através de uma ligação ExpressRoute ou de Gateway de VPN do Azure, pode propagar rotas BGP para as suas VNets.

### <a name="hybrid-internet-connectivity-connect-to-an-on-premises-network"></a>Conectividade de internet híbrida: ligar a uma rede no local
Pode ligar a sua rede no local para uma VNet com qualquer combinação das seguintes opções:

-   Conectividade Internet

-   VPN ponto a site (P2S VPN)

-   VPN de site para Site (S2S VPN)

-   ExpressRoute

#### <a name="internet-connectivity"></a>Conectividade Internet

Como o respetivo nome sugere, a conectividade à Internet faz com que as cargas de trabalho acessível a partir da Internet, fazendo com que expõem diferentes pontos finais públicos para cargas de trabalho em direto dentro da rede virtual. Estas cargas de trabalho podem ser expostas através de [Balanceador de carga para a Internet](https://docs.microsoft.com/azure/load-balancer/load-balancer-internet-overview) ou simplesmente a atribuir um endereço IP público à VM. Desta forma, torna possível para qualquer coisa na Internet para conseguir alcançar que a máquina virtual, fornecida uma firewall de anfitrião, [de rede (NSG) de grupos de segurança](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg), e [rotas definidas pelo utilizador](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview) de permitir que a acontecer.

Neste cenário, pode expor uma aplicação que tem de ser público à Internet e conseguir estabelecer ligação ao mesmo a partir de qualquer local, ou a partir de localizações específicas, dependendo da configuração das cargas de trabalho.

#### <a name="point-to-site-vpn-or-site-to-site-vpn"></a>VPN ponto a Site ou VPN Site a Site
Estes dois insere-se na mesma categoria. Ambos os têm de ter um Gateway de VPN a VNet e pode ligar ao mesmo utilizando um cliente de VPN para a estação de trabalho como parte do [configuraçãoponto a Site](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal) ou pode configurar no local [dispositivo VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpn-devices)conseguir terminar uma VPN de site para site. Desta forma, podem ligar dispositivos no local recursos dentro da VNet.

Uma configuração Ponto a Site (P2S) permite-lhe criar uma ligação segura a partir de um computador cliente individual para a sua rede virtual. Uma P2S é uma ligação VPN através de SSTP (Secure Socket Tunneling Protocol).

![VPN ponto a Site](media/azure-network-security/azure-network-security-fig-5.png)

As ligações ponto a Site são úteis quando pretende ligar a vnet a partir de uma localização remota, tal como casa ou de um centro de conferência ou quando tem apenas alguns clientes que necessitam para ligar a uma rede virtual.

As ligações P2S não precisam de nenhum dispositivo VPN ou endereço IP destinado ao público. O utilizador estabelece a ligação VPN a partir do computador cliente. Por conseguinte, P2S não é recomendada a forma de ligar para o Azure, no caso de ter uma ligação persistente de muitos computadores e dispositivos no local à sua rede do Azure.

![VPN de Site a site](media/azure-network-security/azure-network-security-fig-6.png)

> [!Note]
> Para obter mais informações sobre ligações ponto a Site, consulte o [ponto a Site FA v Q](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-point-to-site-classic-azure-portal).

Uma ligação de gateway de VPN de Site para Site é utilizada para ligar a sua rede no local a uma rede virtual do Azure através de um túnel VPN IPsec/IKE (IKEv1 ou IKEv2).

Este tipo de ligação requer um dispositivo VPN localizado no local que tenha um endereço IP público com acesso exterior atribuído ao mesmo. Esta ligação ocorre através da Internet e permite-lhe informações "túnel" dentro de uma ligação encriptada entre a rede e do Azure. VPN de site a site é uma tecnologia de segura, madura que tenha sido implementada por empresas de todos os tamanhos de decades. Encriptação de túnel é executada com [modo de túnel IPsec](https://technet.microsoft.com/library/cc786385.aspx).

Enquanto VPN site a site é uma tecnologia de fidedigna, fiável e estabelecida, o tráfego de túnel atravessar da Internet. Além disso, a largura de banda relativamente está restringida a um máximo de cerca de 200 Mbps.

Se necessitar de um nível excecional de segurança ou de desempenho para as suas ligações entre locais, recomendamos que utilize o Azure ExpressRoute para a conectividade em vários locais. O ExpressRoute é uma WAN dedicada ligação entre a sua localização no local ou num fornecedor de alojamento do Exchange. Porque se trata de uma ligação de telco, os dados não viajam através da Internet e, por conseguinte, não estão expostos a potenciais riscos inerentes em comunicações da Internet.

> [!Note]
> Para obter mais informações sobre gateways de VPN, consulte o artigo [gateway de VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways).

#### <a name="dedicated-wan-link"></a>Ligação WAN dedicada
O Microsoft Azure ExpressRoute permite-lhe expandir as suas redes no local para o Azure através de uma ligação privada dedicada facilitada por um fornecedor de conectividade.

As ligações do ExpressRoute não passam para a Internet pública. Tal permite que as ligações do ExpressRoute ofereçam mais fiabilidade, velocidades superiores, latências inferiores e uma maior segurança do que as ligações típicas através da Internet.

![ Ligação WAN dedicada](media/azure-network-security/azure-network-security-fig-7.png)

> [!Note]
> Para obter informações sobre como ligar à rede para a Microsoft com o ExpressRoute, consulte [modelos de conectividade do ExpressRoute](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways) e [descrição geral técnica do ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction).

Como com as opções de VPN de site a site, ExpressRoute também permite-lhe ligar a recursos que não são necessariamente na VNet apenas um. Na verdade, consoante o SKU, pode ligar a 10 VNets. Se tiver o [suplemento premium](https://docs.microsoft.com/azure/expressroute/expressroute-faqs), ligações de até 100 VNets são possíveis, dependendo da largura de banda. Para saber mais sobre o que gostou estes tipos de aspeto de ligações, leia [diagramas de topologia de ligação](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways?toc=%2fazure%2fvirtual-network%2ftoc.json).

### <a name="security-controls"></a>Controlos de segurança
Uma rede Virtual do Azure fornece uma rede lógica e segura que está isolada de outras redes virtuais e suporta vários controlos de segurança que utilizam nas redes no local. Os clientes criem as seus próprios estrutura utilizando: sub-redes — poderem utilizar os seus próprios intervalo de endereços IP privado, configurar tabelas de rota, grupos de segurança de rede, acesso as listas de controlo (ACLs), gateways e aplicações virtuais para executar as respetivas cargas de trabalho na nuvem.

Seguem-se os controlos de segurança que pode utilizar nas suas redes virtuais do Azure:

-   Controlos de acesso de rede

-   Rotas definidas pelo utilizador

-   Dispositivo de segurança de rede

-   Gateway de Aplicação

-   Firewall de aplicação Web do Azure

-   Controlo de disponibilidade de rede

#### <a name="network-access-controls"></a>Controlos de acesso de rede
Enquanto a rede Virtual do Azure (VNet) é fundamental para modelo de rede do Azure e fornece isolamento e proteção, o [grupos de segurança de rede (NSG)](https://blogs.msdn.microsoft.com/igorpag/2016/05/14/azure-network-security-groups-nsg-best-practices-and-lessons-learned/) são a principal ferramenta que utilizar para impor e controlar as regras de tráfego de rede no nível de rede.

![ Controlos de acesso de rede](media/azure-network-security/azure-network-security-fig-8.png)


Pode controlar o acesso ao que permite ou nega comunicação entre as cargas de trabalho dentro de uma rede virtual, de sistemas na redes do cliente através de uma conectividade entre instalações, ou direcionar comunicação da Internet.

No diagrama, as VNets e NSGs residirem numa camada específica no Azure geral segurança pilha, onde os NSGs, UDR e os dispositivos de rede virtual podem ser utilizados para criar limites de segurança para proteger as implementações de aplicações na rede protegida.

Os NSGs utilizam uma 5 cadeias de identificação para avaliar o tráfego (e são utilizados nas regras que configurar para o NSG):

-   [Endereço IP de origem e de destino](https://support.microsoft.com/help/969029/the-functionality-for-source-ip-address-selection-in-windows-server-2008-and-in-windows-vista-differs-from-the-corresponding-functionality-in-earlier-versions-of-windows)

-   [Porta de origem e de destino](https://technet.microsoft.com/library/dd197515)

-   Protocolo: [protocolo de controlo de transmissão (TCP)](https://technet.microsoft.com/library/cc940037.aspx) ou [protocolo de datagrama de utilizador (UDP)](https://technet.microsoft.com/library/cc940034.aspx)

Isto significa que pode controlar o acesso entre uma única VM e um grupo de VMs, ou uma única VM para outra VM única ou entre sub-redes completos. Novamente, tenha em atenção que este é o pacote de monitorização de estado simple filtragem, inspeção de pacotes não completa. Não há nenhuma validação de protocolo ou IDS de nível de rede ou capacidade IPS de um grupo de segurança de rede.

Um NSG é fornecido com algumas regras incorporadas que deve ter em consideração. Nomeadamente:

-   **Permitir todo o tráfego dentro de uma rede virtual específico:** todas as VMs na mesma rede Virtual do Azure podem comunicar entre si.

-   **Permitir a entrada de balanceamento de carga do Azure:** esta regra permite o tráfego de qualquer endereço de origem para qualquer endereço de destino para o Balanceador de carga do Azure.

-   **Negar toda a entrada:** esta regra bloqueia todo o tráfego sourcing a partir da Internet que ativou explicitamente.

-   **Permitir todo o tráfego de saída à Internet:** esta regra permite que as VMs iniciar as ligações à Internet. Se não pretender que estas ligações iniciadas, terá de criar uma regra para bloquear as ligações ou impor a imposição do túnel.

#### <a name="system-routes-and-user-defined-routes"></a>Rotas de sistema e as rotas definidas pelo utilizador

Quando adicionar máquinas virtuais (VMs) a uma rede virtual (VNet) no Azure, repare que as VM conseguem comunicar entre si através da rede, automaticamente. Não é necessário especificar um gateway, apesar das VM estarem em sub-redes diferentes.

O mesmo se verifica para a comunicação entre as VM e a Internet pública, e até mesmo na sua rede no local quando está presente uma ligação híbrida a partir do Azure para o seu centro de dados.

![Rotas de sistema](media/azure-network-security/azure-network-security-fig-9.png)

Este fluxo de comunicação é possível porque o Azure utiliza uma série de rotas de sistema para definir como flui o tráfego IP. As rotas de sistema controlam o fluxo de comunicação nos seguintes cenários:

-   A partir da mesma sub-rede.

-   A partir de uma sub-rede para outra numa VNet.

-   A partir das VM para a Internet.

-   A partir de uma VNet para outra VNet através de um gateway VPN.

-   A partir de uma VNet para outra VNet através de VNet Peering ([encadeamento de serviços](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview)).

-   A partir de um VNet para a sua rede no local através de um gateway VPN.

Muitas empresas têm segurança estrita e requisitos de conformidade que exigem a inspeção no local de todos os pacotes de rede para impor específicos de políticas. O Azure oferece um mecanismo chamado [imposição do túnel](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-forced-tunneling) que encaminha o tráfego entre as VM no local ao criar uma rota personalizada ou [Border Gateway Protocol (BGP)](https://docs.microsoft.com/windows-server/remote/remote-access/bgp/border-gateway-protocol-bgp) anúncios através do ExpressRoute ou VPN.

Imposição do túnel no Azure está configurado através de rotas definidas pelo utilizador da rede virtual (UDR). Redirecionar o tráfego para um site no local é expresso como uma rota predefinida para o gateway de VPN do Azure.

A secção seguinte apresenta uma lista de limitação atual de encaminhamento e tabela de rotas para uma rede Virtual do Azure:

-   Cada sub-rede da rede virtual tem uma tabela de encaminhamento incorporada, do sistema. A tabela de encaminhamento do sistema tem os seguintes três grupos de rotas:

 -  **Rotas de VNet locais:** diretamente para o destino VMs na mesma rede virtual

 - **Nas rotas local:** para o VPN gateway do Azure

 -  **Rota predefinida:** diretamente à Internet. Pacotes destinados a entidades para os endereços IP privados não abrangidos pelas rotas de dois anteriores são ignorados.

-   Com o lançamento de rotas definidas pelo utilizador, pode criar uma tabela de encaminhamento para adicionar uma rota predefinida e, em seguida, associar a tabela de encaminhamento para a sub-rede da VNet para ativar a imposição do túnel nessas sub-redes.

-   Tem de definir um "site predefinido" entre os sites locais em vários locais ligado à rede virtual.

-   Imposição do túnel tem de ser associado a uma VNet com um gateway VPN encaminhamento dinâmico (não um gateway estático).

- ExpressRoute imposição do túnel não está configurada através desta mecanismo, mas em vez disso, é ativado por uma rota predefinida através de sessões de peering de BGP de ExpressRoute de publicidade.

> [!Note]
> Para obter mais informações, consulte o [documentação do ExpressRoute](https://azure.microsoft.com/documentation/services/expressroute/) para obter mais informações.

#### <a name="network-security-appliances"></a>Aplicações de segurança de rede
Enquanto os grupos de segurança de rede e rotas definidas pelo utilizador podem fornecer medidas de segurança de rede em camadas de rede e o transporte do [modelo OSI](https://en.wikipedia.org/wiki/OSI_model), existe que vão ser situações em que pretende ou precisa que ative a segurança no níveis superiores da pilha de rede. Estas situações, recomendamos que implemente aplicações de segurança de rede virtual fornecidas pelo Azure parceiros.

![Aplicações de segurança de rede](./media/azure-network-security/azure-network-security-fig-10.png)

Aplicações de segurança de rede do Azure melhorar a segurança de VNet e funções de rede e estiverem disponíveis vários fornecedores através do [Azure Marketplace](https://azuremarketplace.microsoft.com). Estas aparelhos virtuais segurança podem ser implementados para fornecer:

-   Firewalls elevadas

-   Prevenção de intrusões

-   Deteção de intrusão

-   Firewalls de aplicação Web (WAFs)

-   Otimização de WAN

-   Encaminhamento

-   Balanceamento de carga

-   VPN

-   Gestão de certificados

-   Active Directory

-   Autenticação multifator

#### <a name="application-gateway"></a>Gateway de aplicação

[Gateway de aplicação do Microsoft Azure](https://docs.microsoft.com/azure/application-gateway/application-gateway-introduction) é uma aplicação virtual dedicada que fornece um controlador de entrega de aplicações (ADC) como um serviço.

 ![Gateway de Aplicação](./media/azure-network-security/azure-network-security-fig-11.png)

Gateway de aplicação permite-lhe otimizar o desempenho da web farm e disponibilidade ao descarregar a terminação SSL exigente em termos de CPU para o gateway de aplicação (-descarga de SSL). Também fornece outras funcionalidades de encaminhamento de camada 7, incluindo:

-   Round robin distribuição de tráfego de entrada

-   Afinidade com base no cookie de sessão

-   URL com base no caminho de encaminhamento

-   Capacidade para alojar vários sites por trás de um único Gateway de aplicação


A [firewall de aplicações web (WAF)](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-application-firewall-overview) também é fornecido como parte do gateway de aplicação. Isto fornece proteção para as aplicações web de vulnerabilidades web e explorações comuns. Gateway de aplicação pode ser configurado como um Internet com o gateway, interna apenas gateway ou uma combinação de ambos.

Gateway de aplicação WAF pode ser executado no modo de deteção ou prevenção. É um caso de utilização comuns para os administradores executar no modo de deteção para observar o tráfego de padrões maliciosos. Depois de exploits potenciais são detetadas, ativar o modo de prevenção bloqueia o tráfego de entrada suspeito.

 ![Gateway de Aplicação](./media/azure-network-security/azure-network-security-fig-12.png)

Além disso, Gateway de aplicação WAF ajuda-o a monitorizar aplicações web contra ataques em cadeia utilizando um registo de WAF em tempo real que estão integrados [Azure Monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview) e [Centro de segurança do Azure](https://azure.microsoft.com/services/security-center/) para controlar os alertas de WAF e monitorizar facilmente as tendências.

O registo de formato JSON vai diretamente para conta de armazenamento do cliente. Tem controlo total sobre estes registos e pode aplicar as suas políticas de retenção.

Também pode incorporar estes registos no seu sistema de análise utilizando [a integração de registo do Azure](https://aka.ms/AzLog). Registos de WAF também são integrados com [Operations Management Suite (OMS)](https://www.microsoft.com/cloud-platform/operations-management-suite) pelo que pode utilizar a análise de registos do OMS para executar consultas de detalhado sofisticadas.

#### <a name="azure-web-application-firewall-waf"></a>Firewall de aplicação web do Azure (WAF)

As aplicações Web são cada vez mais destinos de ataques maliciosos que explora vulnerabilidades conhecidas comuns, tais como a injeção de SQL, ataques de scripts entre sites e outros ataques que aparecem no [10 de principais OWASP](https://www.owasp.org/index.php/Category:OWASP_Top_Ten_Project). Impedir que essas exploits na aplicação requer manutenção rigorosas, monitorização em várias camadas da topologia de aplicação e aplicação de patches.

 ![Firewall de aplicação Web do Azure (WAF)](./media/azure-network-security/azure-network-security-fig-13.png)

Um centralizada [firewall de aplicações web (WAF)](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-application-firewall-overview) pode proteger contra ataques da web e simplifica a gestão de segurança sem necessidade de alterações de aplicação.

Uma solução WAF também pode reagir mais rapidamente a uma ameaça de segurança ao corrigir uma vulnerabilidade conhecida numa localização central, em vez de proteger cada uma das aplicações Web individualmente. Os gateways de aplicações existentes podem ser facilmente convertidos num gateway de aplicação com firewall de aplicações Web ativada.

#### <a name="network-availability-controls"></a>Controlos de disponibilidade de rede

Existem diferentes opções para distribuir o tráfego de rede com o Microsoft Azure. Estas opções funcionam de forma diferente entre si, tendo um conjunto de funcionalidades diferente e suportando diferentes cenários. Podem todos ser utilizados em isolamento ou em conjunto.

Seguem-se os controlos de disponibilidade de rede:

-   Azure Load Balancer

-   Gateway de Aplicação

-   Gestor de Tráfego

**Balanceador de carga do Azure**

Fornece elevado desempenho de rede e disponibilidade às suas aplicações. É um balanceador de carga de camada 4 (TCP, UDP) que distribui o tráfego de entrada entre instâncias de bom estado de funcionamento dos serviços definidos um conjunto com balanceamento de carga.

 ![Azure Load Balancer](media/azure-network-security/azure-network-security-fig-14.png)


Balanceador de carga do Azure pode ser configurado para:

-   Carregar saldo tráfego de entrada à Internet para máquinas virtuais. Esta configuração é conhecida como [balanceamento de carga para a Internet](https://docs.microsoft.com/azure/load-balancer/load-balancer-internet-overview).

-   Tráfego de balanceamento de carga entre máquinas virtuais numa rede virtual, entre as máquinas virtuais nos serviços em nuvem ou entre computadores no local e as máquinas virtuais numa rede virtual em vários locais. Esta configuração é conhecida como [balanceamento de carga interna](https://docs.microsoft.com/azure/load-balancer/load-balancer-internal-overview).

-   Encaminhar o tráfego externo para uma máquina virtual específica.

Todos os recursos na nuvem precisam de um endereço IP público para ser acessível a partir da Internet. A infraestrutura de nuvem do Azure utiliza endereços não encaminháveis internos de IP para os respetivos recursos. Azure utiliza a tradução de endereços de rede (NAT) com endereços IP públicos para comunicar com a Internet.

 **Gateway de aplicação**

 Gateway de aplicação funciona na camada da aplicação (camada 7 na pilha de referência de rede de OSI). Atua como um serviço de proxy de reversão, terminando a ligação de cliente e reencaminhando pedidos de pontos finais de back-end.

 **Gestor de tráfego**

Gestor de tráfego do Microsoft Azure permite-lhe controlar a distribuição de tráfego do utilizador para pontos finais do serviço em datacenters diferentes. Pontos finais de serviço suportados pelo Gestor de tráfego incluem as VMs do Azure, as aplicações Web e serviços em nuvem. Também pode utilizar o Gestor de Tráfego com pontos finais externos, não pertencentes ao Azure.

Gestor de tráfego utiliza o sistema de nomes de domínio (DNS) para direcionar os pedidos de cliente para o ponto de final mais adequado, com base num [método de encaminhamento de tráfego](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-routing-methods) e o estado de funcionamento dos pontos finais. Gestor de tráfego fornece uma variedade de métodos de encaminhamento de tráfego de acordo com as necessidades de outra aplicação, estado de funcionamento do ponto final [monitorização](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-monitoring)e ativação pós-falha automática. Gestor de tráfego seja resistente a falhas, incluindo a falha de uma região do Azure completa.

Traffic Manager do Azure permite-lhe controlar a distribuição de tráfego entre pontos finais da sua aplicação. Um ponto final é qualquer serviço de acesso à Internet alojada dentro ou fora do Azure.

Gestor de tráfego proporciona duas vantagens chave:

-   Distribuição de tráfego de acordo com uma das várias [métodos de encaminhamento de tráfego](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-routing-methods).

-   [Monitorização contínua de estado de funcionamento do ponto final](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-monitoring) e ativação pós-falha automática quando não conseguem pontos finais.

Quando um cliente tenta estabelecer ligação a um serviço,-tem primeiro de resolver o nome DNS do serviço para um endereço IP. O cliente liga-se, em seguida, a esse endereço IP para aceder ao serviço. Gestor de tráfego utiliza o DNS para direcionar clientes para pontos finais de serviço específicos com base nas regras do método de encaminhamento de tráfego. Os clientes ligam diretamente para o ponto final selecionado. Gestor de tráfego não é um proxy ou de um gateway. Gestor de tráfego não consegue ver o tráfego de passar entre o cliente e o serviço.

### <a name="azure-network-validation"></a>Validação de rede do Azure

Validação de rede do Azure é certificar-se de que a rede do Azure está a funcionar conforme está configurado e pode ser efetuada a validação utilizando os serviços e funcionalidades disponíveis para monitorizar a rede. Com o observador de rede do Azure, pode aceder a um plethora de registo e diagnóstico capacidades que permitem insights para compreender o estado de funcionamento e desempenho da rede. Estas funcionalidades estão acessíveis através do Portal, Power Shell, CLI, Rest API e SDK.

Segurança operacionais do Azure refere-se os serviços, controlos e funcionalidades disponíveis para os utilizadores para proteger os seus dados, aplicações e outros recursos no Microsoft Azure. Segurança operacionais do Azure é construída numa estrutura que incorpora o conhecimento adquirido através de uma várias funcionalidades que são exclusivas para a Microsoft, incluindo o Microsoft Security Development Lifecycle (SDL), o programa de Centre de resposta de segurança do Microsoft e a deteção de ameaças de segurança informático.

-   [Azure Operations Management Suite](https://docs.microsoft.com/azure/operations-management-suite/operations-management-suite-overview)

-   [Centro de Segurança do Azure](https://docs.microsoft.com/azure/security-center/security-center-intro)

-   [Azure Monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview)

-   [Observador de rede do Azure](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview)

-   [Análise de armazenamento do Azure](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics)

-   Azure Resource Manager

#### <a name="azure-resource-manager"></a>Gestor de recursos do Azure

As pessoas e os processos que operam o Microsoft Azure são talvez a funcionalidade de segurança mais importante da plataforma. Esta secção descreve as funcionalidades de infraestrutura do global datacenter da Microsoft que ajudam a melhorar e manter a segurança, continuidade e privacidade.

A infraestrutura para a sua aplicação, normalmente, é composta por vários componentes, como uma máquina virtual, conta de armazenamento e rede virtual, ou uma aplicação web, base de dados, servidor de base de dados e serviços de terceiros. Não vê estes componentes como entidades separadas. Em vez disso, vê-os como partes relacionadas e interdependentes de uma única entidade. Deve implementá-los, geri-los e monitorizá-los como um grupo. O Azure Resource Manager permite trabalhar com os recursos na sua solução como um grupo.

Pode implementar, atualizar ou eliminar todos os recursos da sua solução numa operação única e coordenada. Utiliza um modelo para a implementação e esse modelo pode funcionar para ambientes diferentes, como de teste e produção. O Resource Manager fornece funcionalidades de segurança, auditoria e etiquetagem para o ajudar a gerir os recursos após a implementação.

**As vantagens de utilizar o Gestor de recursos**

O Resource Manager oferece várias vantagens:

-   Pode implementar, gerir e monitorizar todos os recursos da sua solução como um grupo, em vez de os processar individualmente.

-   Pode implementar repetidamente a solução durante todo o ciclo de vida de desenvolvimento e ter a confiança de que os recursos são implementados num estado consistente.

-   Pode gerir a sua infraestrutura através de modelos declarativos em vez de scripts.

-   Pode definir as dependências entre os recursos, para que sejam implementados na ordem correta.

-   Pode aplicar o controlo de acesso a todos os serviços no seu grupo de recursos porque o Controlo de Acesso Baseado em Funções (RBAC) está integrado de forma nativa na plataforma de gestão.

-   Pode aplicar etiquetas a recursos para organizar logicamente todos os recursos na sua subscrição.

-   Pode clarificar a faturação da sua organização ao visualizar os custos de um grupo de recursos que partilha a etiqueta.

> [!Note]
> O Resource Manager proporciona uma nova forma de implementar e gerir as suas soluções. Se utilizou o modelo de implementação anterior e pretende obter informações sobre as alterações, consulte [Compreender a implementação do Resource Manager e a implementação clássica](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-deployment-model).

## <a name="azure-network-logging-and-monitoring"></a>Monitorização e de registo de rede do Azure

O Azure disponibiliza diversas ferramentas para monitorizar, evitar, detetar e responder a eventos de segurança de rede. Algumas das ferramentas mais poderosas disponíveis nesta área incluem:

-   Observador de Rede

-   Monitorização ao nível da rede recursos

-   Log Analytics

### <a name="network-watcher"></a>Observador de rede

[Observador de rede](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview) -baseada em cenários de monitorização é fornecido com as funcionalidades do observador de rede. Este serviço inclui a captura de pacotes, o salto seguinte, o fluxo IP verificar, a vista de grupo de segurança, os registos de fluxo NSG. A monitorização ao nível do cenário fornece uma vista de ponto a ponto dos recursos de rede, ao contrário de monitorização de recursos de rede individuais.

 ![Observador de Rede](./media/azure-network-security/azure-network-security-fig-15.png)

Observador de rede é um serviço regional que lhe permite monitorizar e diagnosticar condições a um nível de cenário rede, para e do Azure. Diagnóstico de rede e ferramentas de visualização disponíveis com o observador de rede ajudam-na compreender, diagnosticar e obter informações sobre a sua rede no Azure.

Observador de rede atualmente tem as seguintes capacidades:

#### <a name="topology"></a>Topologia

[Topologia](https://docs.microsoft.com/azure/network-watcher/network-watcher-topology-overview) devolve um gráfico dos recursos de rede numa rede virtual. O gráfico ilustra o interconnection entre os recursos para representar a conectividade de rede ponto a ponto. No portal, topologia devolve os objetos de recurso de acordo com a base de rede virtual. As relações estão representadas por linhas entre os recursos fora da região de observador de rede, mesmo que no recurso de grupo não será apresentado. Os recursos devolvidos na vista de portal são um subconjunto dos componentes de rede que são graphed. Para ver a lista completa dos recursos de rede, pode utilizar [PowerShell](https://docs.microsoft.com/azure/network-watcher/network-watcher-topology-powershell) ou [REST](https://docs.microsoft.com/azure/network-watcher/network-watcher-topology-rest).

Como são devolvidos recursos a ligação entre eles são modelados em duas relações.

- **Contenção** -rede Virtual contém uma sub-rede que contém uma NIC.

- **Associados** -A NIC está associado uma VM.

#### <a name="variable-packet-capture"></a>Captura de pacotes variável

Observador de rede [captura de pacotes variável](https://docs.microsoft.com/azure/network-watcher/network-watcher-packet-capture-overview) permite-lhe criar sessões de captura de pacotes para controlar o tráfego de e para uma máquina virtual. Ajuda a captura de pacotes para diagnosticar anomalias rede de forma reativa e proactivity. Outras utilizações incluem a recolha de estatísticas de rede, obtenha informações sobre intrusions de rede, para depurar as comunicações cliente-servidor e muito mais.

Captura de pacotes é uma extensão de máquina virtual que esteja iniciada remotamente através do observador de rede. Esta capacidade facilita o fardo de executar uma captura de pacotes manualmente na máquina virtual pretendida, que poupa tempo importante. Captura de pacotes pode ser acionada através do portal, o PowerShell, a CLI ou a REST API. Um exemplo de como pode ser acionada captura de pacotes é com alertas de Máquina Virtual.

#### <a name="ip-flow-verify"></a>Certifique-se de fluxo IP

[Certifique-se de fluxos IP](https://docs.microsoft.com/azure/network-watcher/network-watcher-ip-flow-verify-overview) verifica se um pacote é permitido ou negado para ou a partir de uma máquina virtual com base nas informações de 5 cadeias de identificação. Esta informação é constituído por direção, protocolo, local IP, remoto IP, porta local e porta remota. Se o pacote é negado por um grupo de segurança, o nome da regra que negado o pacote é devolvido. Enquanto qualquer IP de origem ou de destino pode ser selecionado, esta funcionalidade ajuda os administradores a Diagnostique rapidamente problemas de conectividade do ou à internet e de ou para o ambiente no local.

IP fluxos verificar destina-se uma interface de rede de uma máquina virtual. Fluxo de tráfego, em seguida, é verificado com base nas definições configuradas de ou para essa interface de rede. Esta funcionalidade é útil para confirmar se uma regra num grupo de segurança de rede está a bloquear o tráfego de entrada ou saída de ou para uma máquina virtual.

#### <a name="next-hop"></a>Próximo salto

Determina o [próximo salto](https://docs.microsoft.com/azure/network-watcher/network-watcher-next-hop-overview) para os pacotes que está a ser encaminhados nos recursos de infraestrutura de rede do Azure, permitindo-lhe diagnosticar qualquer configurada incorretamente rotas definidas pelo utilizador. Tráfego de uma VM é enviado para um destino com base nas rotas efetivas associadas um NIC. Salto seguinte obtém o endereço IP de um pacote e o tipo de próximo salto de uma máquina virtual específica e a NIC. Isto ajuda a determinar se o pacote está a ser direcionado para o destino ou é o tráfego a ser preto holed.

Também o salto seguinte devolve a tabela de rota associada o salto seguinte. Ao consultar um salto seguinte se a rota é definida como uma rota definida pelo utilizador, será devolvida esse rota. Caso contrário, o próximo salto devolve "Rota de sistema".

#### <a name="security-group-view"></a>vista do grupo de segurança

Obtém as regras de segurança eficaz e aplicados que são aplicadas numa VM. Grupos de segurança de rede estão associados a um nível de sub-rede ou um nível de NIC. Quando associados a um nível de sub-rede, aplica-se a todas as instâncias VM na sub-rede. Rede [vista do grupo de segurança](https://docs.microsoft.com/azure/network-watcher/network-watcher-security-group-view-overview) devolve todos os NSGs e regras que estão associadas a um nível NIC e a sub-rede para uma máquina virtual que fornecem informações sobre a configuração configurado. Além disso, as regras de segurança eficaz são devolvidas para cada um dos NICs numa VM. Vista de grupo de segurança de rede a utilizar, pode avaliar uma VM para vulnerabilidades de rede, tais como portas abertas. Também pode validar se o grupo de segurança de rede está a funcionar conforme esperado com base num [comparação entre configurada e as regras de segurança eficaz](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-auditing-powershell).

#### <a name="nsg-flow-logging"></a>Registo de fluxo do NSG

 Registos de fluxo para grupos de segurança de rede permitem-lhe capturar registos relacionados com o tráfego que são permitidas ou negadas pelas regras de segurança no grupo. O fluxo é definido por uma informação de 5 cadeias de identificação – IP de origem, IP de destino, porta de origem, porta de destino e protocolo.

[Os registos de fluxo do grupo de segurança de rede](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-overview) são uma funcionalidade do observador de rede que permite-lhe ver informações sobre o tráfego IP de entrada e de saída através de um grupo de segurança de rede.

#### <a name="virtual-network-gateway-and-connection-troubleshooting"></a>Gateway de rede virtual e a resolução de problemas de ligação

Observador de rede oferece muitas funcionalidades no que respeita à compreender os seus recursos de rede no Azure. Uma dessas funcionalidades é recursos de resolução de problemas. [Resolução de problemas de recursos](https://docs.microsoft.com/azure/network-watcher/network-watcher-troubleshoot-manage-rest) pode ser chamada por PowerShell, a CLI ou a REST API. Quando chamado, o observador de rede inspeciona o estado de funcionamento de um gateway de rede Virtual ou uma ligação e devolve conclusões.

Esta secção orienta-as tarefas de gestão diferentes que estão atualmente disponíveis para a resolução de problemas de recursos.

-   [Resolver problemas relacionados com um gateway de rede Virtual](https://docs.microsoft.com/azure/network-watcher/network-watcher-troubleshoot-manage-rest)

-   [Resolver problemas de uma ligação](https://docs.microsoft.com/azure/network-watcher/network-watcher-troubleshoot-manage-rest)

#### <a name="network-subscription-limits"></a>Limites de subscrição de rede

[Limites de subscrição de rede](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview) lhe fornecer os detalhes de utilização de cada recurso de rede numa subscrição numa região contra o número máximo de recursos disponíveis.

#### <a name="configuring-diagnostics-log"></a>Configuração de diagnósticos de registo

Observador de rede fornece um [os registos de diagnóstico](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview) vista. Esta vista contém todos os recursos de rede que suportam o registo de diagnóstico. Desta vista, pode ativar e desativar os recursos de rede rápida e convenientemente.

### <a name="network-resource-level-monitoring"></a>Monitorização ao nível da rede recursos

As seguintes funcionalidades estão disponíveis para monitorização de nível de recursos:

#### <a name="audit-log"></a>Registo de auditoria

Operações efetuadas como parte da configuração de redes são registadas. Estes registos de auditoria são essenciais para estabelecer compliances vários. Estes registos podem ser visualizados no portal do Azure ou obtidos através de ferramentas da Microsoft, tais como o Power BI ou ferramentas de terceiros. Os registos de auditoria estão disponíveis através do portal, o PowerShell, a CLI e a Rest API.

> [!Note]
> Para obter mais informações sobre os registos de auditoria, consulte [auditar operações com o Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-audit).
Os registos de auditoria estão disponíveis para operações efetuadas em todos os recursos de rede.


#### <a name="metrics"></a>Métricas

As métricas são medidas de desempenho e contadores recolhidos durante um período. Métricas estão atualmente disponíveis para o Gateway de aplicação. Métricas podem ser utilizadas para acionar alertas com base num limiar. Gateway de aplicação do Azure por predefinição monitoriza o estado de funcionamento de todos os recursos no seu conjunto de back-end e remove automaticamente qualquer recurso considerado em mau estado de funcionamento do agrupamento. Gateway de aplicação continua a monitorizar as instâncias de mau estado de funcionamento e adiciona-a novamente para o bom conjunto de back-end assim que estes fiquem disponíveis e respondem a sondas de estado de funcionamento. Gateway de aplicação envia que as sondas de estado de funcionamento com a mesma porta que está definida nas definições de HTTP de back-end. Esta configuração assegura que a sonda consiste em testar a mesma porta que os clientes estariam a utilizar para ligar ao back-end.

> [!Note]
> Consulte [diagnóstico do Gateway de aplicação](https://docs.microsoft.com/azure/application-gateway/application-gateway-probe-overview) para ver como métricas podem ser utilizadas para criar alertas.

#### <a name="diagnostic-logs"></a>Registos de diagnósticos

Eventos periódicos e spontaneous são criados pelos recursos de rede e registados em contas do storage, enviadas para um Hub de eventos ou análise de registos. Estes registos fornecem informações sobre o estado de funcionamento de um recurso. Estes registos podem ser visualizados no ferramentas como o Power BI e análise de registos. Para saber como ver os registos de diagnóstico, visite [Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-networking-analytics).

Os registos de diagnóstico estão disponíveis para [Balanceador de carga](https://docs.microsoft.com/azure/load-balancer/load-balancer-monitor-log), [grupos de segurança de rede](https://docs.microsoft.com/azure/virtual-network/virtual-network-nsg-manage-log), rotas, e [Gateway de aplicação](https://docs.microsoft.com/azure/application-gateway/application-gateway-diagnostics).

Observador de rede fornece que um diagnóstico os registos de vista. Esta vista contém todos os recursos de rede que suportam o registo de diagnóstico. Desta vista, pode ativar e desativar os recursos de rede rápida e convenientemente.

### <a name="log-analytics"></a>Log analytics

[Análise de registo](https://docs.microsoft.com/azure/log-analytics/log-analytics-overview) é um serviço no [Operations Management Suite (OMS)](https://docs.microsoft.com/azure/operations-management-suite/operations-management-suite-overview) que monitoriza a sua nuvem e no local ambientes para manter a respetiva disponibilidade e desempenho. Recolhe dados gerados por recursos nos seus ambientes na cloud e no local e de outras ferramentas de monitorização, para disponibilizar análises relativas a várias origens.

Análise de registos oferece as seguintes soluções para monitorização as redes:

-   Monitor de desempenho de rede (NPM)

-   Análise de Gateway de aplicação do Azure

-   Análise de grupo de segurança de rede do Azure

#### <a name="network-performance-monitor-npm"></a>Monitor de desempenho de rede (NPM)
O [Monitor de desempenho de rede](https://docs.microsoft.com/azure/log-analytics/log-analytics-network-performance-monitor) solução de gestão é uma solução que monitoriza o estado de funcionamento, disponibilidade e acessibilidade de redes de monitorização de rede.

É utilizado para monitorizar a conectividade entre:

-   Nuvem pública e no local

-   Os centros de dados e localizações de utilizador (sucursais)

-   Sub-redes alojar várias camadas de uma aplicação de várias camadas.


#### <a name="azure-application-gateway-analytics-in-log-analytics"></a>Análise de gateway de aplicação do Azure na análise de registos

Os seguintes registos são suportados para Gateways de aplicação:

-   ApplicationGatewayAccessLog

-   ApplicationGatewayPerformanceLog

-   ApplicationGatewayFirewallLog

As métricas seguintes são suportadas para Gateways de aplicação:

-   débito de 5 minutos

#### <a name="azure-network-security-group-analytics-in-log-analytics"></a>Análise de grupo de segurança de rede do Azure na análise de registos

São suportados os seguintes registos para [grupos de segurança de rede](https://docs.microsoft.com/azure/virtual-network/virtual-network-nsg-manage-log):

- **NetworkSecurityGroupEvent:** contém entradas para o NSG regras são aplicadas às VMs e funções com base no endereço MAC de instância. O estado para estas regras é recolhido a cada 60 segundos.

- **NetworkSecurityGroupRuleCounter:** contém entradas quantas vezes cada NSG regra é aplicada a negar e permitir o tráfego.

## <a name="next-steps"></a>Passos seguintes
Saber mais sobre a segurança ao ler alguns dos nossos tópicos de segurança aprofundados:

-   [Análise de registos para grupos de segurança de rede (NSGs)](https://docs.microsoft.com/azure/virtual-network/virtual-network-nsg-manage-log)

-   [Inovações que a interrupção de nuvem de unidade de rede](https://azure.microsoft.com/blog/networking-innovations-that-drive-the-cloud-disruption/)

-   [SONiC: O funcionamento em rede comutador software que powers nuvem Global da Microsoft](https://azure.microsoft.com/blog/sonic-the-networking-switch-software-that-powers-the-microsoft-global-cloud/)

-   [Como Microsoft baseia-se a rede rápida e fiável global](https://azure.microsoft.com/blog/how-microsoft-builds-its-fast-and-reliable-global-network/)

-   [Iluminação da cópia de segurança inovação de rede](https://azure.microsoft.com/blog/lighting-up-network-innovation/)
