---
title: Rede Virtual do Azure | Microsoft Docs
description: Saiba mais sobre conceitos de rede Virtual do Azure e funcionalidades.
services: virtual-network
documentationcenter: na
author: jimdial
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 9633de4b-a867-4ddf-be3c-a332edf02e24
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/23/2017
ms.author: jdial
ms.openlocfilehash: 6d6afd2b9b956138ed400fbd6cabd3b480fde0f0
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="azure-virtual-network"></a>Rede Virtual do Azure

O serviço de rede Virtual do Azure permite-lhe ligar de forma segura a recursos do Azure uns aos outros com redes virtuais (VNets). Uma VNet é uma representação da sua própria rede na nuvem. Uma VNet é um isolamento lógico da nuvem do Azure dedicado à sua subscrição. Também pode ligar VNets à sua rede no local. A imagem seguinte mostra algumas das funcionalidades do serviço de rede Virtual do Azure:

![Diagrama de rede](./media/virtual-networks-overview/virtual-network-overview.png)

Para saber mais sobre as seguintes capacidades de rede Virtual do Azure, clique a capacidade:
- **[Isolamento:](#isolation)**  as VNets estão isoladas entre si. Pode criar VNets separadas para o desenvolvimento, teste e produção, que utilizam os mesmos blocos de endereços CIDR. Por outro lado, pode criar várias VNets que utilizam diferentes blocos de endereços CIDR e ligar redes em conjunto. Pode segmentar uma VNet em várias sub-redes. O Azure oferece resolução dos nomes internos para VMs e serviços em nuvem instâncias de função ligadas a uma VNet. Opcionalmente, pode configurar uma VNet para utilizar os seus próprios servidores DNS, em vez de utilizar a resolução do nome interno do Azure.
- **[A conectividade à Internet:](#internet)**  instâncias de função de todas as do Azure máquinas virtuais (VMS) e serviços em nuvem ligadas a uma VNet tem acesso à Internet, por predefinição. Também pode ativar o acesso de entrada a recursos específicos, conforme necessário.
- **[Conectividade de recursos do Azure:](#within-vnet)**  recursos do Azure, tais como serviços em nuvem e as VMs podem ser ligados para a mesma VNet. Os recursos podem ligar-se entre si utilizando endereços IP privados, mesmo que se encontrem em sub-redes diferentes. O Azure oferece encaminhamento predefinido entre sub-redes, VNets e redes no local, pelo que não tem de configurar e gerir as rotas.
- **[Conectividade da VNet:](#connect-vnets)**  VNets podem ser ligadas entre si, permitindo recursos ligados a qualquer VNet para comunicar com qualquer recurso no qualquer outra VNet.
- **[Conectividade no local:](#connect-on-premises)**  VNets podem ser ligadas a redes no local através de ligações de rede privada entre a rede e o Azure ou através de uma ligação de VPN de site a site através da Internet.
- **[Filtragem de tráfego:](#filtering)**  VM e serviços em nuvem tráfego de rede de instâncias de função pode ser filtrado entrada e saída por endereço IP de origem e porta, endereço IP de destino e porta e protocolo.
- **[Encaminhamento:](#routing)**  , opcionalmente, pode substituir predefinição do Azure encaminhamento por configurar as seus próprios rotas ou utilizar rotas BGP através de um gateway de rede.

## <a name = "isolation"></a>Isolamento de rede e a segmentação de

Pode implementar várias VNets dentro de cada Azure [subscrição](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#subscription) e o Azure [região](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#region). Cada VNet está isolado de outras VNets. Para cada VNet pode:
- Especifique um espaço de endereços IP privado personalizado utilizando endereços (RFC 1918) públicos e privados. Recursos do Azure atribui ligado para a VNet um endereço IP privado o espaço de endereços que atribuir.
- Segmentar a VNet numa ou mais sub-redes e atribuir uma parte do espaço de endereços VNet para cada sub-rede.
- Utilize a resolução de nome fornecidos pelo Azure ou especificar o próprio servidor DNS para utilização por recursos ligados a uma VNet. Para obter mais informações sobre resolução de nomes em VNets, leia o [a resolução de nomes para VMs e serviços em nuvem](virtual-networks-name-resolution-for-vms-and-role-instances.md) artigo.

## <a name = "internet"></a>Ligar à Internet
Todos os recursos ligados a uma VNet tem conectividade à Internet de saída por predefinição. O endereço IP privado do recurso é origem endereço de rede traduzido (realizar o SNAT) para um endereço IP público a infraestrutura do Azure. Para saber mais sobre a ligação à Internet de saída, leia o [Noções sobre ligações de saída no Azure](..\load-balancer\load-balancer-outbound-connections.md?toc=%2fazure%2fvirtual-network%2ftoc.json#standalone-vm-with-no-instance-level-public-ip-address) artigo. Pode alterar a conectividade da predefinição através da implementação de encaminhamento personalizado e filtragem de tráfego.

Para comunicar entrada nos recursos do Azure através da Internet, ou para comunicar a saída à Internet sem realizar o SNAT, um recurso deve ser atribuído um endereço IP público. Para saber mais sobre os endereços IP públicos, leia o [endereços IP públicos](virtual-network-public-ip-address.md) artigo.

## <a name="within-vnet"></a>Ligar recursos do Azure
Pode ligar a vários recursos do Azure para uma VNet, tais como máquinas virtuais (VM), Cloud Services, ambientes do App Service e conjuntos de dimensionamento de Máquina Virtual. VMs ligar a uma sub-rede dentro de uma VNet através de uma interface de rede (NIC). Para saber mais sobre NICs, leia o [interfaces de rede](virtual-network-network-interface.md) artigo.

## <a name="connect-vnets"></a>Ligar redes virtuais

Pode ligar VNets entre si, permitindo recursos ligados a qualquer das Vnets para comunicar entre si entre VNets. Pode utilizar uma ou ambas as opções seguintes para ligar VNets entre si:
- **Peering:** permite recursos ligados a diferentes as VNets do Azure na mesma localização do Azure para comunicar entre si. A largura de banda e latência entre as VNets é o mesmo como se estivessem ligados os recursos para a mesma VNet. Para saber mais sobre o peering, leia o [peering de rede Virtual](virtual-network-peering-overview.md) artigo.
- **Ligação VNet a VNet:** permite recursos ligados a VNet do Azure diferente dentro as localizações do Azure idêntica ou diferentes. Ao contrário de peering, largura de banda é limitada entre VNets porque o tráfego tem de fluir através de um Gateway de VPN do Azure. Para saber mais sobre como ligar VNets com uma ligação VNet a VNet, leia o [configurar uma ligação VNet a VNet](../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) artigo.

## <a name="connect-on-premises"></a>Ligar a uma rede no local

Pode ligar a sua rede no local para uma VNet com qualquer combinação das seguintes opções:
- **Ponto a site rede privada virtual (VPN):** estabelecida entre um único PC à sua rede e a VNet. Este tipo de ligação é excelente se de que está a começar com o Azure ou para os programadores, porque necessita de pouca ou nenhuma alterações à sua rede existente. A ligação utiliza o protocolo SSTP para fornecer comunicações encriptadas através da Internet entre o computador e a VNet. A latência de uma VPN ponto a site é imprevisível, uma vez que o tráfego atravessar da Internet.
- **VPN de site para site:** estabelecida entre o dispositivo VPN e um Gateway de VPN do Azure. Este tipo de ligação permite qualquer recurso no local que está a autorizar para aceder a uma VNet. A ligação é uma VPN IPSec/IKE que fornece comunicações encriptadas através da Internet entre o dispositivo no local e o gateway de VPN do Azure. A latência de uma ligação site a site é imprevisível, uma vez que o tráfego atravessar da Internet.
- **O ExpressRoute do Azure:** estabelecida entre a rede e o Azure, através de um parceiro do ExpressRoute. Esta ligação é privada. Tráfego atravessar não da Internet. A latência de uma ligação ExpressRoute é previsível, uma vez que o tráfego não atravessar da Internet.

Para saber mais sobre todas as opções de ligação anterior, leia o [diagramas de topologia de ligação](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json#diagrams) artigo.

## <a name="filtering"></a>Filtrar o tráfego de rede
Pode filtrar o tráfego de rede entre as sub-redes utilizando um ou ambos das seguintes opções:
- **Rede (NSG) de grupos de segurança:** cada NSG pode conter várias regras de segurança de entrada e saída que permitem filtrar o tráfego por endereço IP de origem e de destino, porta e protocolo. Pode aplicar um NSG para cada NIC numa VM. Também pode aplicar um NSG para sub-rede um NIC ou outros recursos do Azure, está ligado. Para saber mais sobre NSGs, leia o [grupos de segurança de rede](virtual-networks-nsg.md) artigo.
- **Aplicações virtuais (NVA) de rede:** uma NVA é uma VM com o software que executa uma função de rede, como uma firewall. Ver uma lista de NVAs disponíveis no [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/networking?page=1&subcategories=appliances). NVAs estão também disponíveis, que fornecem a otimização de WAN e outra rede funções de tráfego. NVAs são normalmente utilizadas com definido pelo utilizador ou as rotas BGP. Também pode utilizar uma NVA para filtrar o tráfego entre as VNets.

## <a name="routing"></a>Encaminhar o tráfego de rede

O Azure cria as tabelas de rotas ativar recursos ligados a nenhuma sub-rede qualquer VNet para comunicar entre si, por predefinição. Pode implementar um ou ambos das seguintes opções para substituir as rotas predefinidas que Azure cria:
- **Rotas definidas pelo utilizador:** pode criar as tabelas de rotas personalizadas com rotas esse controlo onde o tráfego é encaminhado para cada sub-rede. Para saber mais sobre as rotas definidas pelo utilizador, veja o artigo [Rotas definidas pelo utilizador](virtual-networks-udr-overview.md).
- **Rotas BGP:** se ligar a VNet à sua rede no local através de uma ligação ExpressRoute ou de Gateway de VPN do Azure, pode propagar rotas BGP para as suas VNets.

## <a name="pricing"></a>Preços

Não há sem qualquer encargo para redes virtuais, sub-redes, tabelas de rotas ou rede grupos de segurança. Saída de utilização da largura de banda de Internet, pública IP endereços, peering de rede virtual, Gateways de VPN e ExpressRoute têm as suas próprias estruturas de preço. Ver o [rede Virtual](https://azure.microsoft.com/pricing/details/virtual-network), [Gateway de VPN](https://azure.microsoft.com/pricing/details/vpn-gateway), e [ExpressRoute](https://azure.microsoft.com/pricing/details/expressroute) preços páginas para obter mais informações.

## <a name="faq"></a>FAQ

Para rever as perguntas mais frequentes sobre a rede Virtual, consulte o [FAQ da rede Virtual](virtual-networks-faq.md) artigo.


## <a name="next-steps"></a>Passos seguintes

- Criar a primeira VNet e ligar-se algumas VMs, concluindo os passos a [criar a primeira rede virtual](virtual-network-get-started-vnet-subnet.md) artigo.
- Criar uma ligação de ponto a site para uma VNet, efetuando os passos a [configurar uma ligação ponto a site](../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) artigo.
- Saiba mais sobre algumas da outra chave [capacidades de rede](../networking/networking-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) do Azure.
