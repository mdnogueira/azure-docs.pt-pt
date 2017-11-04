---
title: Rede Virtual do Azure | Microsoft Docs
description: Saiba mais sobre conceitos de rede Virtual do Azure e funcionalidades.
services: virtual-network
documentationcenter: na
author: jimdial
manager: jeconnoc
editor: 
tags: azure-resource-manager
ms.assetid: 9633de4b-a867-4ddf-be3c-a332edf02e24
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/03/2017
ms.author: jdial
ms.openlocfilehash: dc6916bd25c5a020fdcef0707fe28a1e34fb0f88
ms.sourcegitcommit: 3df3fcec9ac9e56a3f5282f6c65e5a9bc1b5ba22
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2017
---
# <a name="azure-virtual-network"></a>Rede Virtual do Azure

O serviço de rede Virtual do Microsoft Azure permite que os recursos do Azure comunicar de forma segura com outros, numa rede virtual. Uma rede virtual é uma representação da sua própria rede na nuvem. Uma rede virtual é um isolamento lógico da nuvem do Azure dedicado à sua subscrição. Pode ligar redes virtuais para outras redes virtuais ou à sua rede no local. A imagem seguinte mostra algumas das funcionalidades do serviço de rede Virtual do Azure:

![Diagrama de rede](./media/virtual-networks-overview/virtual-network-overview.png)

Para saber mais sobre as seguintes capacidades de rede Virtual do Azure, clique a capacidade:
- **[Isolamento:](#isolation)**  redes virtuais são isoladas entre si. Pode criar separado blocos de endereços de redes virtuais para o desenvolvimento, teste e produção, que utilizam o mesmo CIDR (10.0.0.0/0, por exemplo). Por outro lado, pode criar várias redes virtuais que utilizam diferentes blocos de endereços CIDR e ligar as redes em conjunto. Pode segmentar uma rede virtual em várias sub-redes. O Azure oferece resolução dos nomes internos para máquinas virtuais e instâncias de função de Cloud Services do Azure implementadas numa rede virtual. Opcionalmente, pode configurar uma rede virtual para utilizar os seus próprios servidores DNS, em vez de utilizar a resolução do nome interno do Azure.
- **[Comunicação com a Internet:](#internet)**  instâncias de função de todas as máquinas virtuais do Azure e serviços em nuvem numa rede virtual tem acesso à Internet, por predefinição. Também pode ativar o acesso de entrada a recursos específicos, conforme necessário.
- **[Comunicação de recursos do Azure:](#within-vnet)**  recursos do Azure, tais como serviços em nuvem e máquinas virtuais podem ser implementados na mesma rede virtual. Os recursos podem comunicar entre si utilizando endereços IP privados, mesmo que se encontrem em sub-redes diferentes. O Azure oferece encaminhamento predefinido entre sub-redes, VNets e redes no local, pelo que não tem de configurar e gerir as rotas. Pode personalizar do Azure encaminhamento, se assim o desejar.
- **[Conectividade de rede virtual:](#connect-vnets)**  redes virtuais podem ser ligadas entre si, ativar a recursos na rede virtual para comunicar com os recursos em qualquer outra rede virtual.
- **[Conectividade no local:](#connect-on-premises)**  uma rede virtual pode estar em privado ligada a uma rede no local ou utilizando uma ligação de VPN de site a site através da Internet.
- **[Filtragem de tráfego:](#filtering)**  máquinas virtuais e serviços em nuvem tráfego de rede de instância de função pode ser filtrado entrada e saída por endereço IP de origem e porta, endereço IP de destino e porta e protocolo.
- **[Encaminhamento:](#routing)**  , opcionalmente, pode substituir a predefinição do Azure encaminhamento configurando as suas próprias rotas ou ao propagar BGP encaminha através de um gateway de rede.

## <a name = "isolation"></a>Isolamento de rede e a segmentação de

Pode implementar várias redes virtuais dentro de cada Azure [subscrição](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#subscription) e o Azure [região](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#region). Cada uma das redes virtuais está isolada das restantes. Para cada rede virtual, pode:
- Especifique um espaço de endereços IP privado personalizado utilizando endereços (RFC 1918) públicos e privados. Azure atribui recursos numa rede virtual de um endereço IP privado do espaço de endereço que atribuir.
- Segmentar a rede virtual numa ou mais sub-redes e atribuir uma parte do espaço de endereços da rede virtual para cada sub-rede.
- Utilize a resolução de nome fornecidos pelo Azure ou especificar o próprio servidor DNS para utilização pelos recursos numa rede virtual. Para obter mais informações sobre resolução de nomes em redes virtuais, consulte o artigo [a resolução de nomes para VMs e serviços em nuvem](virtual-networks-name-resolution-for-vms-and-role-instances.md) artigo.

## <a name = "internet"></a>Comunicação com a Internet
Todos os recursos numa rede virtual podem comunicar saída à Internet, por predefinição. O endereço IP privado do recurso é origem endereço de rede traduzido (realizar o SNAT) para um endereço IP público selecionado pela infraestrutura do Azure. Para saber mais sobre a ligação à Internet de saída, leia o [Noções sobre ligações de saída no Azure](..\load-balancer\load-balancer-outbound-connections.md?toc=%2fazure%2fvirtual-network%2ftoc.json#standalone-vm-with-no-instance-level-public-ip-address) artigo. Para impedir a conectividade de Internet de saída, pode implementar rotas personalizadas ou de filtragem de tráfego.

Para comunicar entrada nos recursos do Azure através da Internet, ou para comunicar a saída à Internet sem realizar o SNAT, um recurso deve ser atribuído um endereço IP público. Para saber mais sobre os endereços IP públicos, leia o [endereços IP públicos](virtual-network-public-ip-address.md) artigo.

## <a name="within-vnet"></a>Proteger a comunicação entre os recursos do Azure

Pode implementar máquinas virtuais dentro de uma rede virtual. Máquinas virtuais comuniquem com outros recursos numa rede virtual através de uma interface de rede. Para saber mais sobre as interfaces de rede, consulte o artigo [interfaces de rede](virtual-network-network-interface.md).

Também pode implementar vários outros tipos de recursos do Azure a uma rede virtual, tais como máquinas virtuais do Azure, Cloud Services do Azure, os ambientes de serviço de aplicações do Azure e conjuntos de dimensionamento de Máquina Virtual do Azure. Para obter uma lista completa dos recursos do Azure, pode implementar numa rede virtual, consulte [integração de serviço de rede Virtual para os serviços do Azure](virtual-network-for-azure-services.md).

Alguns recursos não podem ser implementados numa rede virtual, mas permitem-lhe restringir a comunicação a partir dos recursos dentro de apenas uma rede virtual. Para obter mais informações sobre como restringir o acesso aos recursos, consulte [pontos finais do serviço de rede Virtual](virtual-network-service-endpoints-overview.md). 

## <a name="connect-vnets"></a>Ligar redes virtuais

Pode ligar redes virtuais entre si, ativar a recursos em qualquer rede virtual para comunicar entre si utilizando o peering de rede virtual. A largura de banda e latência de comunicação entre os recursos na redes virtuais diferentes é o mesmo como se os recursos foram na mesma rede virtual. Para saber mais sobre o peering, leia o [peering de rede Virtual](virtual-network-peering-overview.md) artigo.

## <a name="connect-on-premises"></a>Ligar a uma rede no local

Pode ligar a sua rede no local a uma rede virtual, utilizando qualquer combinação das seguintes opções:
- **Ponto a site rede privada virtual (VPN):** estabelecida entre uma rede virtual e um único computador na sua rede. Cada computador que pretende estabelecer conectividade, com uma rede virtual tem de configurar as suas ligações de forma independente. Este tipo de ligação é excelente se de que está a começar com o Azure ou para os programadores, porque necessita de pouca ou nenhuma alterações à sua rede existente. A ligação utiliza o protocolo SSTP para fornecer comunicações encriptadas através da Internet entre o computador e uma rede virtual. A latência de uma VPN ponto a site é imprevisível, uma vez que o tráfego atravessar da Internet.
- **VPN de site para site:** estabelecida entre o dispositivo VPN e um Gateway de VPN do Azure implementadas numa rede virtual. Este tipo de ligação permite qualquer recurso no local que está a autorizar para aceder a uma rede virtual. A ligação é uma VPN IPSec/IKE que fornece comunicações encriptadas através da Internet entre o dispositivo no local e o gateway de VPN do Azure. A latência de uma ligação site a site é imprevisível, uma vez que o tráfego atravessar da Internet.
- **O ExpressRoute do Azure:** estabelecida entre a rede e o Azure, através de um parceiro do ExpressRoute. Esta ligação é privada. Tráfego atravessar não da Internet. A latência de uma ligação ExpressRoute é previsível, uma vez que o tráfego não atravessar da Internet.

Para saber mais sobre todas as opções de ligação anterior, leia o [diagramas de topologia de ligação](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json#diagrams) artigo.

## <a name="filtering"></a>Filtrar o tráfego de rede
Pode filtrar o tráfego de rede entre as sub-redes utilizando um ou ambos das seguintes opções:
- **Grupos de segurança de rede:** um grupo de segurança de rede pode conter várias regras de segurança de entrada e saída que permitem filtrar o tráfego por endereço IP de origem e de destino, porta e protocolo. Pode aplicar um grupo de segurança de rede para cada interface de rede numa máquina virtual. Também pode aplicar um grupo de segurança de rede para a sub-rede uma interface de rede ou outros recursos do Azure, está a ser. Para obter mais informações sobre grupos de segurança de rede, consulte [grupos de segurança de rede](security-overview.md#network-security-groups).
- **Aplicações virtuais de rede:** uma aplicação virtual de rede é uma máquina virtual com o software que executa uma função de rede, como uma firewall. Ver uma lista de aplicações virtuais de rede disponível no [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/networking?page=1&subcategories=appliances). Os dispositivos de rede virtuais também estão disponíveis que fornecer otimização de WAN e outra rede tráfego funções. Os dispositivos de rede virtuais são normalmente utilizados com definido pelo utilizador ou as rotas BGP. Também pode utilizar uma aplicação virtual de rede para filtrar o tráfego entre redes virtuais.

## <a name="routing"></a>Encaminhar o tráfego de rede

O Azure cria as tabelas de rotas ativar recursos ligados a nenhuma sub-rede da rede virtual para comunicar entre si, por predefinição. Pode implementar um ou ambos das seguintes opções para substituir as rotas predefinidas que Azure cria:
- **Rotas definidas pelo utilizador:** pode criar as tabelas de rotas personalizadas com rotas esse controlo onde o tráfego é encaminhado para cada sub-rede. Para saber mais sobre as rotas definidas pelo utilizador, consulte o artigo [rotas definidas pelo utilizador](virtual-networks-udr-overview.md#user-defined).
- **Rotas BGP:** se ligar a rede virtual à sua rede no local através de uma ligação ExpressRoute ou de Gateway de VPN do Azure, pode propagar rotas BGP para as redes virtuais.

## <a name="pricing"></a>Preços

Não há sem qualquer encargo para redes virtuais, sub-redes, tabelas de rotas ou rede grupos de segurança. Saída de utilização da largura de banda de Internet, pública IP endereços, peering de rede virtual, Gateways de VPN e ExpressRoute têm as suas próprias estruturas de preço. Ver o [rede Virtual](https://azure.microsoft.com/pricing/details/virtual-network), [Gateway de VPN](https://azure.microsoft.com/pricing/details/vpn-gateway), e [ExpressRoute](https://azure.microsoft.com/pricing/details/expressroute) preços páginas para obter mais informações.

## <a name="faq"></a>FAQ

Para rever as perguntas mais frequentes sobre a rede Virtual do Azure, consulte o [FAQ da rede Virtual](virtual-networks-faq.md) artigo.


## <a name="next-steps"></a>Passos seguintes

- Criar a primeira rede virtual e implementar algumas máquinas virtuais para a mesma, seguindo os passos no [criar a primeira rede virtual](virtual-network-get-started-vnet-subnet.md).
- Criar uma ligação de ponto a site a uma rede virtual, efetuando os passos em [configurar uma ligação ponto a site](../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
- Saiba mais sobre algumas da outra chave [capacidades de rede](../networking/networking-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) do Azure.
