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
ms.date: 09/25/2017
ms.author: narayan;anavin
ms.openlocfilehash: 082cd8a6cf50f76c89fe5995047396c734f83034
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="virtual-network-peering"></a>Peering de rede virtual

A [Rede Virtual do Azure (VNet)](virtual-networks-overview.md) é um espaço de rede privado no Azure que permite ao utilizador ligar em segurança recursos do Azure entre si.

O peering de rede virtual permite ao utilizador ligar redes virtuais de forma totalmente integrada. Uma vez executado o peering, as redes virtuais aparecem como uma única, para fins de conectividade. As máquinas virtuais nas redes virtuais em modo de peering podem comunicar diretamente entre si.
O tráfego entre máquinas virtuais nas redes virtuais em modo de peering será encaminhado através da infraestrutura principal da Microsoft, tal como o tráfego é encaminhado entre máquinas virtuais na mesma rede virtual através apenas de endereços IP *privados*.

>[!IMPORTANT]
> Pode ligar em modo de peering redes virtuais em diferentes regiões do Azure. Esta funcionalidade encontra-se em pré-visualização. Pode [registar a sua subscrição na pré-visualização.](virtual-network-create-peering.md) O peering de redes virtuais nas mesmas regiões encontra-se em disponibilidade geral.
>

As vantagens da utilização do peering de redes virtuais incluem:

* O tráfego que passa por peerings de redes virtuais é completamente privado. Passa pela rede principal da Microsoft, sem o envolvimento da Internet pública ou de gateways.
* Uma ligação de baixa latência e largura de banda alta entre os recursos em redes virtuais diferentes.
* A capacidade de utilizar recursos numa rede virtual a partir de outra rede virtual, uma vez executado o peering.
* O peering de redes virtuais ajuda-o a transferir dados entre subscrições do Azure, modelos de implementação e entre regiões do Azure (pré-visualização).
* A capacidade de configurar o peering entre redes virtuais criadas através do Azure Resource Manager ou o peering entre uma rede virtual criada através do Resource Manager e outra criada através do modelo de implementação clássica. Para saber mais sobre as diferenças entre os dois modelos de implementação do Azure, leia [Understand Azure deployment models (Compreender os modelos de implementação do Azure)](../azure-resource-manager/resource-manager-deployment-model.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

## <a name="requirements-constraints"></a>Requisitos e limitações

* O peering de redes virtuais na mesma região encontra-se em disponibilidade geral. O peering de redes virtuais em diferentes regiões encontra-se atualmente em pré-visualização nas regiões E.U.A. Centro-Oeste, Canadá Central e E.U.A. Oeste 2. Pode [registar a sua subscrição na pré-visualização.](virtual-network-create-peering.md)
    > [!WARNING]
    > Os peerings de redes virtuais criados neste cenário podem não ter o mesmo nível de disponibilidade e fiabilidade do que em cenários de versão de disponibilidade geral. Os peerings de redes virtuais podem ter capacidades restringidas e não estar disponíveis em todas as regiões do Azure. Para obter as notificações mais atualizadas sobre a disponibilidade e o estado desta funcionalidade, veja a página [Atualizações de Rede Virtual do Azure](https://azure.microsoft.com/updates/?product=virtual-network).

* As redes virtuais no modo de peering têm de ter espaços de endereços IP não sobrepostos.
* Não é possível adicionar nem eliminar espaços de endereços de uma rede virtual quando cria o peering entre duas redes virtuais.
* O peering de rede virtual é feito entre duas redes virtuais. Não existe nenhuma relação transitiva derivada entre peerings. Por exemplo, se virtualNetworkA e virtualNetworkB estiverem no mesmo peer e esta última e a virtualNetworkC também, virtualNetworkA *não* está no mesmo peer que virtualNetworkC.
* Pode configurar o peering em redes virtuais que existam em duas subscrições diferentes, desde que um utilizador com privilégios (veja [Specific Permissions](create-peering-different-deployment-models-subscriptions.md#permissions) [Permissões Específicas]) em ambas as subscrições o autorize e que essas subscrições estejam associadas ao mesmo inquilino do Azure Active Directory. Pode utilizar um [Gateway de VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json#V2V) para ligar redes virtuais em subscrições associadas a diferentes inquilinos do Active Directory.
* O peering pode ser configurado nas redes virtuais se ambas forem criadas com o modelo de implementação Resource Manager ou se uma for criada com este modelo e a outra com o modelo de implementação clássica. Contudo, não é possível configurar o peering entre redes virtuais criadas através do modelo de implementação clássica. Pode utilizar um [Gateway de VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json#V2V) para ligar redes virtuais criadas através do modelo de implementação clássica.
* Embora a comunicação entre máquinas virtuais em redes virtuais no modo de peering não tenha restrições de largura de banda adicionais, existe uma largura de banda de rede máxima, dependendo do tamanho da máquina virtual que ainda se aplicar. Para saber mais sobre a largura de banda de rede máxima para os diferentes tamanhos de máquinas virtuais, leia os artigos sobre os tamanhos de máquinas virtuais do [Windows](../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) ou do [Linux](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

     ![Peering de rede virtual básico](./media/virtual-networks-peering-overview/figure03.png)

## <a name="connectivity"></a>Conectividade

Depois das redes virtuais serem colocadas em modo de peering, os recursos em cada uma destas podem ligar-se diretamente a recursos na rede virtual em modo de peering.

A latência de rede entre máquinas virtuais em redes virtuais no modo de peering na mesma região é igual à de uma rede virtual individual. O débito de rede baseia-se na largura de banda que é permitida para a máquina virtual proporcionalmente ao respetivo tamanho. Não existe qualquer restrição adicional na largura de banda no peering.

O tráfego entre as máquinas virtuais nas redes virtuais em modo de peering é encaminhado diretamente através da infraestrutura principal da Microsoft e não através de um gateway ou numa Internet pública.

As máquinas virtuais numa rede virtual podem aceder ao balanceador de carga interno na rede virtual em modo de peering na mesma região. O suporte do balanceador de carga interno não se estende por várias redes virtuais em modo de peering global em pré-visualização. A versão de disponibilidade geral do peering de rede virtual global terá suporte do balanceador de carga interno.

Os grupos de segurança de rede podem ser aplicados a qualquer uma das redes virtuais para bloquear o acesso a outras redes virtuais ou sub-redes, se assim o desejar.
Ao configurar o peering de rede virtual, pode abrir ou fechar as regras do grupo de segurança de rede entre as redes virtuais. Se abrir a conectividade total entre as redes virtuais em modo de peering (que é a opção predefinida), pode aplicar grupos de segurança de rede a sub-redes ou máquinas virtuais específicas, para bloquear ou negar o acesso específico. Para saber mais sobre os grupos de segurança de rede, leia o artigo [Filtrar o tráfego de rede com grupos de segurança de rede](virtual-networks-nsg.md).

## <a name="service-chaining"></a>Encadeamento de serviços

Para ativar o encadeamento de serviços, pode configurar rotas definidas pelo utilizador que apontem para máquinas virtuais em redes virtuais em modo de peering como o endereço IP de "próximo salto". O encadeamento se serviços permite-lhe encaminhar o tráfego de uma rede virtual para um dispositivo de rede numa rede virtual em modo de peering através de rotas definidas pelo utilizador.

Também pode criar, eficazmente, ambientes de tipo “hub-and-spoke”, onde o concentrador pode alojar componentes da infraestrutura, como um aplicação virtual de rede. Todas as redes virtuais “spoke” podem, então, configurar o peering com a rede virtual do concentrador. O tráfego pode fluir dos dispositivos de rede virtual que estão em execução na rede virtual do concentrador. Em suma, o peering de rede virtual permite que o endereço IP de próximo salto na rota definida pelo utilizador seja o endereço IP de uma máquina virtual na rede virtual em modo de peering. Para saber mais sobre as rotas definidas pelo utilizador, leia o artigo [Descrição geral das rotas definidas pelo utilizador](virtual-networks-udr-overview.md). Para saber como criar uma [topologia de rede hub-and-spoke](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke?toc=%2fazure%2fvirtual-network%2ftoc.json#virtual network-peering)

## <a name="gateways-and-on-premises-connectivity"></a>Gateways e conetividade no local

Cada rede virtual, independentemente de estar ou não em modo de peering com outra, pode continuar a ter um gateway próprio e a utilizá-lo para se ligar a uma rede no local. Também pode configurar [ligações de rede virtual a rede virtual](../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md) através de gateways, mesmo que as redes virtuais estejam em modo de peering.

Quando ambas as opções de inter-conetividade de rede virtual estiverem configuradas, o tráfego entre as redes virtuais irá fluir através da configuração em modo de peering (ou seja, através da estrutura principal do Azure).

Quando as redes virtuais estão em modo de peering na mesma região, também pode configurar o gateway na rede virtual em modo de peering como um ponto de trânsito para uma rede no local. Neste caso, a rede virtual que está a utilizar um gateway remoto não pode ter um gateway próprio. Uma rede virtual pode ter apenas um gateway. O gateway pode ser local ou remoto (na rede virtual em modo de peering), conforme mostrado na imagem seguinte:

![trânsito de peering de rede virtual](./media/virtual-networks-peering-overview/figure04.png)

O trânsito de gateway não é suportado na relação de peering entre redes virtuais criadas através de modelos de implementação diferentes ou regiões diferentes. Para que o trânsito de gateway funcione, é necessário que ambas as redes virtuais na relação de peering tenham sido criadas através do Resource Manager e estejam na mesma região. As redes virtuais global em modo de peering não suportam atualmente o trânsito de gateway.

Quando as redes virtuais que partilham uma única ligação ExpressRoute do Azure estão em modo de peering, o tráfego entre as mesmas passa pela relação de peering (ou seja, pela rede principal do Azure). Pode continuar a utilizar gateways locais em cada rede virtual para ligar ao circuito no local. Em alternativa, pode utilizar um gateway partilhado e configurar o trânsito para conectividade no local.

## <a name="permissions"></a>Permissões

O peering de rede virtual é uma operação com privilégios. É uma função separada no espaço de nomes de Redes Virtuais. Podem ser concedidos direitos específicos a um utilizador para autorizar o peering. Um utilizador que tenha acesso de leitura/escrita à rede virtual herda automaticamente estes direitos.

Um utilizador que seja administrador ou utilizador com privilégios da capacidade de peering pode iniciar uma operação de peering noutra rede virtual. O nível mínimo de permissão necessário é Contribuidor de Rede. Se houver um pedido de correspondência para peering no outro lado e forem cumpridos outros requisitos, o peering é estabelecido.

Por exemplo, se estiver a configurar o peering de redes virtuais com o nome myvirtual networkA e myvirtual networkB, deve ser atribuída à sua conta a seguinte função mínima ou permissões para cada rede virtual:

|Rede virtual|Modelo de implementação|Função|Permissões|
|---|---|---|---|
|myvirtual networkA|Resource Manager|[Contribuidor de Rede](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)|Microsoft.Network/virtualNetworks/virtualNetworkPeerings/write|
| |Clássica|[Contribuidor de Rede Clássica](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#classic-network-contributor)|N/D|
|myvirtual networkB|Resource Manager|[Contribuidor de Rede](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)|Microsoft.Network/virtualNetworks/peer|
||Clássica|[Contribuidor de Rede Clássica](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#classic-network-contributor)|Microsoft.ClassicNetwork/virtualNetworks/peer|

## <a name="monitor"></a>Monitorizar

Ao configurar o peering de duas redes virtuais criadas através do Resource Manager, tem de ser configurado um peering para cada rede virtual no peering.
Pode monitorizar o estado da sua ligação de peering. O estado do peering pode ser um dos seguintes:

* **Iniciado**: quando cria o peering para a segunda rede virtual a partir da primeira rede virtual, o estado do peering é Iniciado.

* **Ligado**: quando cria o peering a partir da segunda rede virtual para a primeira rede virtual, o estado do peering é Ligado. Se visualizar o estado do peering para a primeira rede virtual, verá que o estado mudou de Iniciado para Ligado. O peering não é estabelecido com êxito até que o estado do peering para ambos os peerings de rede virtual seja Ligado.

* **Desligado**: se uma das ligações de peering for eliminada após ter sido estabelecida uma ligação, o estado do peering é Desligado.

## <a name="troubleshoot"></a>Resolução de problemas

Para resolver problemas de fluxo de tráfego na sua ligação de peering, pode [verificar as rotas eficientes.](virtual-network-routes-troubleshoot-portal.md)

Também pode resolver problemas relacionados com a conectividade a uma máquina virtual numa rede virtual em modo de peering através da [verificação de conectividade](../network-watcher/network-watcher-connectivity-portal.md) do Observador de Rede. A verificação de conectividade permite ver como é encaminhado diretamente da interface de rede da sua VM de origem para a interface de rede da sua VM de destino.

## <a name="limits"></a>Limites

Existem limites em relação ao número de peerings que são permitidos para uma única rede virtual. O número predefinido de peerings é 50. Pode aumentar o número de peerings. Para obter mais informações, veja [Azure networking limits](../azure-subscription-service-limits.md#networking-limits) (Limites de rede do Azure).

## <a name="pricing"></a>Preços

Existe uma cobrança nominal para o tráfego de entrada e de saída que utilize uma ligação de peering de rede virtual. Para obter mais informações, veja a [página de preços](https://azure.microsoft.com/pricing/details/virtual-network).

## <a name="next-steps"></a>Passos seguintes

* Concluir o tutorial de peering de rede virtual. É criado um peering de rede virtual entre redes virtuais criadas com o mesmo ou com diferentes modelos de implementação que existam na mesma ou em diferentes subscrições. Conclua um tutorial para um dos cenários seguintes:

    |Modelo de implementação do Azure  | Subscrição  |
    |---------|---------|
    |Ambas com Resource Manager |[Mesma](virtual-network-create-peering.md)|
    | |[Diferente](create-peering-different-subscriptions.md)|
    |Uma com Resource Manager, outra com clássica     |[Mesma](create-peering-different-deployment-models.md)|
    | |[Diferente](create-peering-different-deployment-models-subscriptions.md)|

* Saiba como criar uma [topologia de rede hub-and-spoke.](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke?toc=%2fazure%2fvirtual-network%2ftoc.json#virtual network-peering)
* Saiba tudo sobre [as definições de peering de rede virtual e como alterá-las](virtual-network-manage-peering.md)
