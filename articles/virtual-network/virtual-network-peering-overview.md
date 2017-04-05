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
ms.date: 10/17/2016
ms.author: narayan
translationtype: Human Translation
ms.sourcegitcommit: eeb56316b337c90cc83455be11917674eba898a3
ms.openlocfilehash: 6fbcdcf77f46a3c643e8fedc1d112588cbd7befc
ms.lasthandoff: 04/03/2017


---
# <a name="virtual-network-peering"></a>Peering de rede virtual
O peering de rede virtual (VNet peering) permite-lhe ligar duas VNets na mesma região através da rede principal do Azure. Uma vez executado o peering, as duas VNets aparecem como uma única, para fins de conectividade. Continuam a ser geridas como recursos separados, mas as máquinas virtuais (VM) nas VNets em modo de peering podem comunicar entre si diretamente através de endereços IP privados.

O tráfego entre VMs nas VNets em modo de peering é encaminhado através da Infraestrutura do Azure, à semelhança de como o tráfego é encaminhado entre VMs na mesma VNet. Seguem-se algumas das vantagens da utilização de VNet peering:

* Baixa latência, ligação de largura de banda alta entre recursos em VNets diferentes.
* A capacidade de utilizar recursos, como aparelhos de rede e gateways VPN como pontos de trânsito numa VNet no modo de peering.
* A capacidade de configurar o peering entre duas VNets criadas através do modelo de implementação Azure Resource Manager ou o peering entre uma VNT criada com o Resource Manager e outra através do modelo de implementação clássica. Para saber mais sobre as diferenças entre os dois modelos de implementação do Azure, leia [Understand Azure deployment models (Compreender os modelos de implementação do Azure)](../azure-resource-manager/resource-manager-deployment-model.md).

Requisitos e aspetos fundamentais da VNet peering:

* As VNets no modo de peering têm de estar na mesma região do Azure.
* As VNets no modo de peering têm de ter espaços de endereços IP não sobrepostos.
* O VNet peering é feito entre duas VNets, mas não existe nenhuma relação transitiva derivada entre peerings. Por exemplo, se a VNetA e a VNetB estiverem no mesmo peer e esta última e a VNetC também, a VNetA e a VNetC *não* estão no mesmo peer.
* Pode configurar o peering em VNets que existam em duas subscrições diferentes, desde que um utilizador com privilégios em ambas as subscrições o autorize e que essas subscrições estejam associadas ao mesmo inquilino do Active Directory.
* O peering pode ser configurado nas VNets se ambas forem criadas com o modelo de implementação Resource Manager ou se uma for criada com este modelo e a outra com o modelo de implementação clássica. Contudo, não é possível configurar o peering entre duas VNets criadas através do modelo de implementação clássica. Ao configurar o peering entre VNets criadas com modelos de implementação diferentes, estas têm de estar na *mesma* subscrição. A capacidade de configurar o peering entre VNets criadas com modelos de implementação diferentes e que estejam em subscrições *diferentes* está na versão de **pré-visualização**. Leia o artigo [Create a virtual network peering using Powershell (Criar um peering de rede virtual com o PowerShell)](virtual-networks-create-vnetpeering-arm-ps.md) para obter mais detalhes.
* Embora a comunicação entre VMs em VNets no modo de peering não tenha restrições de largura de banda adicionais, existe uma largura de banda de rede máxima, dependendo do tamanho das VMs que ainda se aplicar. Para saber mais sobre a largura de banda de rede máxima para os diferentes tamanhos de VMs, leia os artigos sobre os tamanhos de VMs do [Windows](../virtual-machines/windows/sizes.md) ou do [Linux](../virtual-machines/linux/sizes.md).

![VNet peering básico](./media/virtual-networks-peering-overview/figure01.png)

## <a name="connectivity"></a>Conectividade
Depois de criado o peering entre duas VNets, as funções de VMs ou de Serviços Cloud na VNet podem ligar-se a outros recursos ligados à VNet no modo de peering. Ambas as VNets têm conectividade completa a nível de IP.

A latência de rede de um percurso de ida e volta entre duas VMs em VNets no modo de peering é igual à de um percurso de ida e volta numa VNet individual. O débito de rede baseia-se na largura de banda que é permitida para a VM, de forma proporcional ao respetivo tamanho. Não existe qualquer restrição adicional na largura de banda no peering.

O tráfego entre as VMs nas VNets em modo de peering é encaminhado diretamente através da infraestrutura de back-end do Azure e não através de um gateway.

As VMs ligadas a uma VNet podem aceder aos pontos finais com balanceamento de carga interno (ILB) na VNet no modo de peering. Os grupos de segurança de rede (NSG) podem ser aplicados a uma das VNets para bloquear o acesso a outras VNets ou sub-redes, se assim o desejar.

Ao configurar o peering, pode abrir ou fechar as regras de NSG entre as VNets. Se abrir a conectividade completa entre VNets em modo de peering (que é a opção predefinida), pode aplicar NSGs a sub-redes ou VMs específicas para bloquear ou recusar acesso específico. Leia o artigo [Grupos de segurança de rede](virtual-networks-nsg.md) para saber mais sobre os NSGs.

A resolução de nomes DNS internos fornecida pelo Azure para as VMs não funciona em VNets em modo de peering. As VMs têm nomes DNS internos que só podem ser resolvidos na VNet local. No entanto, pode configurar as VMs ligadas às VNets em modo de peering como servidores DNS para essas VNets. Leia o artigo [Name resolution using your own DNS server (Utilizar o seu próprio servidor DNS para a resolução de nomes)](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-using-your-own-dns-server) para obter mais detalhes.

## <a name="service-chaining"></a>Encadeamento de serviços
Pode configurar rotas definidas pelo utilizador (UDR) que apontem para VMs em VNets em modo de peering como o endereço IP de "próximo salto", conforme mostrado no diagrama deste artigo. Isto permite o encadeamento de serviços, através do qual pode direcionar o tráfego de uma VNet para uma aplicação virtual que esteja em execução numa VNet em modo de peering através UDRs.

Também pode criar, eficazmente, ambientes de tipo “hub-and-spoke”, onde o concentrador pode alojar componentes da infraestrutura, como um aplicação virtual de rede. Depois, é possível configurar o peering de todas as VNets indicadas com a mesma, bem como um subconjunto de tráfego para aplicações que estejam em execução na VNet do concentrador. Em suma, o VNet peering permite que o endereço IP de próximo salto no UDR seja o endereço IP de uma VM na VNet em modo de peering. Leia o artigo sobre as [rotas definidas pelo utilizador](virtual-networks-udr-overview.md) para obter informações adicionais sobre as UDRs.

## <a name="gateways-and-on-premises-connectivity"></a>Gateways e conetividade no local
Cada VNet, independentemente de estar ou não em modo de peering com outra VNet, pode continuar a ter um gateway próprio e a utilizá-lo para se ligar a uma rede no local. Os utilizadores também podem configurar [ligações VNet a VNet](../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md) através de gateways, mesmo que as VNets estejam em modo de peering.

Quando ambas as opções de interconectividade entre VNets estiverem configuradas, o tráfego entre estas flui através da configuração de peering (ou seja, através da estrutura principal do Azure).

Quando as VNets estão em modo de peering, os utilizadores também podem configurar o gateway na VNet em modo de peering como um ponto de trânsito para uma rede no local. Neste caso, a VNet que está a utilizar um gateway remoto não pode ter o seu próprio gateway. Cada VNet só pode ter um gateway. O gateway pode ser local ou remoto (na VNet em modo de peering), conforme mostrado na imagem seguinte:

![Trânsito do VNet peering](./media/virtual-networks-peering-overview/figure02.png)

O trânsito de gateway não é suportado na relação de peering entre VNets criadas através de modelos de implementação diferentes. Para que o trânsito do gateway funcione, é necessário que ambas as VNets na relação de peering tenham sido criadas com o Resource Manager.

Quando as VNets que partilham uma única ligação Azure ExpressRoute estão em modo de peering, o tráfego entre as mesmas passa pela relação de peering (ou seja, pela rede principal do Azure). Pode continuar a utilizar gateways locais em cada VNet para ligar ao circuito no local. Em alternativa, pode utilizar um gateway partilhado e configurar o trânsito para conectividade no local.

## <a name="provisioning"></a>Aprovisionamento
O VNet peering é uma operação com privilégios. É uma função separada no espaço de nomes de Redes Virtuais. Podem ser concedidos direitos específicos a um utilizador para autorizar o peering. Um utilizador que tenha acesso de leitura/escrita à rede virtual herda automaticamente estes direitos.

Um utilizador que seja um administrador ou um utilizador com privilégios da capacidade de peering pode iniciar uma operação de peering para outra VNet. Se houver um pedido de correspondência para peering no outro lado e forem cumpridos outros requisitos, o peering será estabelecido.

Veja os artigos disponíveis na secção [Passos seguintes](#next-steps) deste artigo para saber mais sobre como estabelecer o VNet Peering entre duas VNets.

## <a name="limits"></a>Limites
Existem limites em relação ao número de peerings que são permitidos para uma única rede virtual. Consulte [Limites de rede do Azure](../azure-subscription-service-limits.md#networking-limits) para obter mais informações.

## <a name="pricing"></a>Preços
Existe uma cobrança nominal para o tráfego de entrada e de saída que utilize um VNet peering. Para obter mais informações, consulte a [página de preços](https://azure.microsoft.com/pricing/details/virtual-network).

## <a name="next-steps"></a>Passos seguintes
Saiba como criar um peering de VNets com:

* [O portal do Azure](virtual-networks-create-vnetpeering-arm-portal.md)
* [O Azure PowerShell](virtual-networks-create-vnetpeering-arm-ps.md)
* [Um Modelo do Azure Resource Manager](virtual-networks-create-vnetpeering-arm-template-click.md)

