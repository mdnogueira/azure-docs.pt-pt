---
title: Peering da Rede Virtual do Azure | Microsoft Docs
description: Saiba mais sobre o peering de rede virtual no Azure.
services: virtual-network
documentationcenter: na
author: NarayanAnnamalai
manager: jefco
editor: tysonn
ms.assetid: eb0ba07d-5fee-4db0-b1cb-a569b7060d2a
ms.service: virtual-network
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/06/2017
ms.author: narayan
ms.translationtype: Human Translation
ms.sourcegitcommit: 138f04f8e9f0a9a4f71e43e73593b03386e7e5a9
ms.openlocfilehash: bfba85160cd02baa337881653dbe6582d10ba073
ms.contentlocale: pt-pt
ms.lasthandoff: 06/28/2017


---
<a id="virtual-network-peering" class="xliff"></a>

# Peering de rede virtual
O peering de rede virtual permite-lhe ligar duas redes virtuais na mesma região através da rede principal do Azure. Uma vez executado o peering, as duas redes virtuais aparecem como uma única, para fins de conetividade. Continuam a ser geridas como recursos separados, mas as máquinas virtuais nestas redes virtuais em modo de peering podem comunicar diretamente entre si através de endereços IP privados.

O tráfego entre máquinas virtuais nas redes virtuais em modo de peering será encaminhado através da Infraestrutura do Azure, tal como o tráfego é encaminhado entre máquinas virtuais na mesma rede virtual. Seguem-se algumas das vantagens da utilização do peering de redes virtuais:

* Uma ligação de baixa latência e largura de banda alta entre os recursos em redes virtuais diferentes.
* A capacidade de utilizar recursos, como aparelhos de rede e gateways de VPN como pontos de trânsito numa rede virtual no modo de peering.
* A capacidade de configurar o peering entre duas redes virtuais criadas através do modelo de implementação Azure Resource Manager ou o peering entre uma rede virtual criada com o Resource Manager e outra criada através do modelo de implementação clássica. Para saber mais sobre as diferenças entre os dois modelos de implementação do Azure, leia [Understand Azure deployment models (Compreender os modelos de implementação do Azure)](../azure-resource-manager/resource-manager-deployment-model.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

Requisitos e aspetos fundamentais do peering de rede virtual:

* As redes virtuais no modo de peering têm de estar na mesma região do Azure. Pode utilizar um [Gateway de VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json#V2V) para ligar redes virtuais em diferentes regiões do Azure.
* As redes virtuais no modo de peering têm de ter espaços de endereços IP não sobrepostos.
* Não é possível adicionar nem remover espaços de endereços a partir de uma rede virtual quando cria o peering entre duas redes virtuais.
* O peering de rede virtual é feito entre duas redes virtuais. Não existe nenhuma relação transitiva derivada entre peerings. Por exemplo, se virtualNetworkA e virtualNetworkB estiverem no mesmo peer e esta última e a virtualNetworkC também, virtualNetworkA *não* está no mesmo peer que virtualNetworkC.
* Pode configurar o peering em redes virtuais que existam em duas subscrições diferentes, desde que um utilizador com privilégios em ambas as subscrições o autorize e que essas subscrições estejam associadas ao mesmo inquilino do Azure Active Directory. Pode utilizar um [Gateway de VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json#V2V) para ligar redes virtuais em subscrições associadas a diferentes inquilinos do Active Directory.
* O peering pode ser configurado nas redes virtuais se ambas forem criadas com o modelo de implementação Resource Manager ou se uma for criada com este modelo e a outra com o modelo de implementação clássica. Contudo, não é possível configurar o peering entre duas redes virtuais criadas através do modelo de implementação clássica. Ao configurar o peering entre redes virtuais criadas com modelos de implementação diferentes, estas têm de estar na *mesma* subscrição. A capacidade de configurar o peering entre redes virtuais criadas com modelos de implementação diferentes e que estejam em subscrições *diferentes* está na versão de **pré-visualização**. Leia o artigo [Create a virtual network peering](virtual-network-create-peering.md#different-subscriptions-different-deployment-models) (Criar um peering de rede virtual) para obter mais detalhes.
* Embora a comunicação entre máquinas virtuais em redes virtuais no modo de peering não tenha restrições de largura de banda adicionais, existe uma largura de banda de rede máxima, dependendo do tamanho da máquina virtual que ainda se aplicar. Para saber mais sobre a largura de banda de rede máxima para os diferentes tamanhos de máquinas virtuais, leia os artigos sobre os tamanhos de máquinas virtuais do [Windows](../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) ou do [Linux](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

![Peering de rede virtual básico](./media/virtual-networks-peering-overview/figure01.png)

<a id="connectivity" class="xliff"></a>

## Conectividade
Depois de duas redes virtuais serem colocadas em modo de peering, as funções de máquinas virtuais ou Serviços Cloud na rede virtual podem ligar-se diretamente aos outros recursos ligados à rede virtual em modo de peering. Estas duas redes virtuais têm conectividade completa ao nível de IP.

A latência de rede de um percurso de ida e volta entre duas máquinas virtuais em redes virtuais no modo de peering é igual à de um percurso de ida e volta numa rede virtual individual. O débito de rede baseia-se na largura de banda que é permitida para a máquina virtual proporcionalmente ao respetivo tamanho. Não existe qualquer restrição adicional na largura de banda no peering.

O tráfego entre as máquinas virtuais nas redes virtuais em modo de peering é encaminhado diretamente através da infraestrutura de back-end do Azure e não através de um gateway.

As máquinas virtuais ligadas a uma rede virtual podem aceder aos pontos finais internos com balanceamento de carga da rede virtual em modo de peering. Os grupos de segurança de rede podem ser aplicados a qualquer rede virtual para bloquear o acesso a outras redes virtuais ou sub-redes, se assim o desejar.

Ao configurar o peering de rede virtual, pode abrir ou fechar as regras do grupo de segurança de rede entre as redes virtuais. Se abrir a conectividade total entre as redes virtuais em modo de peering (que é a opção predefinida), pode aplicar grupos de segurança de rede a sub-redes ou máquinas virtuais específicas, para bloquear ou negar o acesso específico. Para saber mais sobre os grupos de segurança de rede, leia o artigo [Filtrar o tráfego de rede com grupos de segurança de rede](virtual-networks-nsg.md).

A resolução de nomes DNS internos fornecida pelo Azure para máquinas virtuais não funciona em redes virtuais em modo de peering. As máquinas virtuais têm nomes DNS internos que podem ser apenas resolvidos na rede virtual local. No entanto, pode configurar as máquinas virtuais ligadas às redes virtuais em modo de peering como servidores DNS de uma rede virtual. Para obter mais detalhes, leia o artigo [Name resolution using your own DNS server](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-using-your-own-dns-server) (Utilizar o seu próprio servidor DNS para a resolução de nomes).

<a id="service-chaining" class="xliff"></a>

## Encadeamento de serviços
Para ativar o encadeamento de serviços, pode configurar rotas definidas pelo utilizador que apontem para máquinas virtuais em redes virtuais em modo de peering como o endereço IP de "próximo salto". O encadeamento se serviços permite-lhe encaminhar o tráfego de uma rede virtual para um dispositivo de rede numa rede virtual em modo de peering através de rotas definidas pelo utilizador.

Também pode criar, eficazmente, ambientes de tipo “hub-and-spoke”, onde o concentrador pode alojar componentes da infraestrutura, como um aplicação virtual de rede. Todas as redes virtuais “spoke” podem, então, configurar o peering com a rede virtual do concentrador. O tráfego pode fluir dos dispositivos de rede virtual que estão em execução na rede virtual do concentrador. Em suma, o peering de rede virtual permite que o endereço IP de próximo salto na rota definida pelo utilizador seja o endereço IP de uma máquina virtual na rede virtual em modo de peering. Para saber mais sobre as rotas definidas pelo utilizador, leia o artigo [Descrição geral das rotas definidas pelo utilizador](virtual-networks-udr-overview.md).

<a id="gateways-and-on-premises-connectivity" class="xliff"></a>

## Gateways e conetividade no local
Cada rede virtual, independentemente de estar ou não em modo de peering com outra, pode continuar a ter um gateway próprio e a utilizá-lo para se ligar a uma rede no local. Também pode configurar [ligações de rede virtual a rede virtual](../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md?toc=%2fazure%2fvirtual-network%2ftoc.json) através de gateways, mesmo que as redes virtuais estejam em modo de peering.

Quando ambas as opções de inter-conetividade de rede virtual estiverem configuradas, o tráfego entre as redes virtuais irá fluir através da configuração em modo de peering (ou seja, através da estrutura principal do Azure).

Quando as redes virtuais estão em modo de peering, pode, igualmente, configurar o gateway na rede virtual em modo de peering como um ponto de trânsito para uma rede no local. Neste caso, a rede virtual que está a utilizar um gateway remoto não pode ter um gateway próprio. Uma rede virtual pode ter apenas um gateway. O gateway pode ser local ou remoto (na rede virtual em modo de peering), conforme mostrado na imagem seguinte:

![Trânsito do VNet peering](./media/virtual-networks-peering-overview/figure02.png)

O trânsito de gateway não é suportado na relação de peering entre redes virtuais criadas através de modelos de implementação diferentes. Para que o trânsito do gateway funcione, é necessário que ambas as redes virtuais na relação de peering tenham sido criadas com o Resource Manager.

Quando as redes virtuais que partilham uma única ligação ExpressRoute do Azure estão em modo de peering, o tráfego entre as mesmas passa pela relação de peering (ou seja, pela rede principal do Azure). Pode continuar a utilizar gateways locais em cada rede virtual para ligar ao circuito no local. Em alternativa, pode utilizar um gateway partilhado e configurar o trânsito para conectividade no local.

<a id="provisioning" class="xliff"></a>

## Aprovisionamento
O peering de rede virtual é uma operação com privilégios. É uma função separada no espaço de nomes de Redes Virtuais. Podem ser concedidos direitos específicos a um utilizador para autorizar o peering. Um utilizador que tenha acesso de leitura/escrita à rede virtual herda automaticamente estes direitos.

Um utilizador que seja administrador ou utilizador com privilégios da capacidade de peering pode iniciar uma operação de peering noutra rede virtual. Se houver um pedido de correspondência para peering no outro lado e forem cumpridos outros requisitos, o peering é estabelecido.

<a id="limits" class="xliff"></a>

## Limites
Existem limites em relação ao número de peerings que são permitidos para uma única rede virtual. Para obter mais informações, veja [Azure networking limits](../azure-subscription-service-limits.md#networking-limits) (Limites de rede do Azure).

<a id="pricing" class="xliff"></a>

## Preços
Existe uma cobrança nominal para o tráfego de entrada e de saída que utilize um peering de rede virtual. Para obter mais informações, veja a [página de preços](https://azure.microsoft.com/pricing/details/virtual-network).

## <a name="next-steps"></a>Passos seguintes

* Concluir o [tutorial de peering rede virtual](virtual-network-create-peering.md)
* Saiba tudo sobre [as definições de peering de rede virtual e como alterá-las](virtual-network-manage-peering.md).

