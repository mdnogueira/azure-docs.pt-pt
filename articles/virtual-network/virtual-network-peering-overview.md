
---
title: Peering da rede virtual do Azure | Microsoft Docs
description: Saiba mais sobre VNet peering no Azure.
services: virtual-network
documentationcenter: na
author: NarayanAnnamalai
manager: jefco
editor: tysonn

ms.service: virtual-network
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/28/2016
ms.author: narayan

---
# VNet peering
O VNet peering é um mecanismo que liga duas redes virtuais na mesma região através da rede principal do Azure. Uma vez executado o peering, as duas redes virtuais aparecem como uma única para todos os fins de conetividade. Vão continuar a ser geridas como recursos separados, mas as máquinas virtuais nestas redes virtuais podem comunicar diretamente entre si através de endereços IP privados.

O tráfego entre máquinas virtuais nas redes virtuais em modo de peering será encaminhado através da Infraestrutura do Azure, tal como o tráfego é encaminhado entre VMs na mesma rede virtual. Seguem-se algumas das vantagens da utilização de VNet peering:

* Uma ligação de baixa latência e largura de banda alta entre os recursos em redes virtuais diferentes.
* A capacidade de utilizar recursos, como aparelhos de rede e gateways VPN como pontos de trânsito numa VNet no modo de peering.
* A capacidade de ligar uma rede virtual que utiliza o modelo do Azure Resource Manager para uma rede virtual que adota o modelo de implementação clássico e permite a conetividade total entre os recursos nestas redes virtuais.

Requisitos e aspetos fundamentais da VNet peering:

* As duas redes virtuais que são colocadas em modo de peering devem estar na mesma região do Azure.
* As redes virtuais colocadas em modo de peering devem ter espaços de endereços IP não sobrepostos.
* A VNet peering ocorre entre duas redes virtuais e não existe nenhuma relação transitiva derivada. Por exemplo, se a rede virtual A estiver no modo de peering com a rede virtual B e se a rede virtual B estiver no modo de peering com a rede virtual C, não converte para a rede virtual A que está a ser colocada no modo de peering com a rede virtual C.
* O peering pode ser estabelecido entre redes virtuais em duas subscrições diferentes, desde que um utilizador com privilégios em ambas as subscrições o autorize e que essas subscrições estejam associadas ao mesmo inquilino do Active Directory. 
* Uma rede virtual que utiliza o modelo de implementação do Resource Manager pode ser colocado no modo de peering com outra rede virtual que utilize este modelo ou com uma rede virtual que utiliza o modelo de implementação clássico. No entanto, as redes virtuais que utilizam o modelo de implementação clássica não podem ser colocadas no modo de peering entre si.
* Apesar de a comunicação entre as máquinas virtuais em redes virtuais em modo de peering não ter restrições de largura de banda adicionais, continua a aplicar-se o limite de largura de banda baseado no tamanho das VM.

![VNet peering básico](./media/virtual-networks-peering-overview/figure01.png)

## Conectividade
Depois de duas redes virtuais serem colocadas em modo de peering, uma máquina virtual (função da Web/de trabalho) na rede virtual consegue ligar-se diretamente a outras máquinas virtuais na rede virtual em modo de peering. Estas duas redes têm conetividade de nível IP completa.

A latência de rede de um percurso de ida e volta entre duas máquinas virtuais em redes virtuais no modo de peering é igual à de um percurso de ida e volta numa rede virtual local. O débito de rede baseia-se a largura de banda que é permitida para a máquina virtual proporcional ao respetivo tamanho. Não existe qualquer restrição adicional na largura de banda.

O tráfego entre as máquinas virtuais nas redes virtuais em modo de peering é encaminhado diretamente através da infraestrutura de back-end do Azure e não através de um gateway.

As máquinas virtuais numa rede virtual podem aceder os pontos finais internos com balanceamento de carga (ILB) da rede virtual em modo de peering. Os grupos de segurança de rede (NSGs) podem ser aplicados a qualquer rede virtual para bloquear o acesso a outras redes virtuais ou sub-redes, se assim o desejar.

Quando os utilizadores configuram o peering, podem abrir ou fechar as regras NSG entre as redes virtuais. Se o utilizador optar por uma conectividade total entre as redes virtuais em modo de peering (que é a opção predefinida) e depois utilizar NSGs em sub-redes ou máquinas virtuais específicas para bloquear ou negar o acesso específico.

A resolução de nomes DNS internos fornecida pelo Azure para máquinas virtuais não funciona em redes virtuais em modo de peering. As máquinas virtuais têm nomes DNS internos que podem ser apenas resolvidos na rede virtual local. No entanto, os utilizadores podem configurar máquinas virtuais em execução em redes virtuais em modo de peering como servidores DNS para uma rede virtual.

## Encadeamento de serviços
Os utilizadores podem configurar tabelas de rota definidas pelo utilizador que apontam para máquinas virtuais em redes virtuais em modo de peering como o endereço IP de "próximo salto", conforme mostrado no diagrama deste artigo. Isto permite aos utilizadores obter encadeamento de serviços, através do qual podem direcionar o tráfego de uma rede virtual para uma aplicação virtual em execução numa rede virtual em modo de peering através de tabelas de rota definidas pelo utilizador.

Os utilizadores podem também efetivamente a criar ambientes de tipo «hub-and-spoke» onde o concentrador pode alojar componentes de infraestrutura como um aplicação virtual de rede. Todas as redes virtuais indicadas podem comunicar com ela, bem como um subconjunto de tráfego para dispositivos em execução na rede virtual do concentrador. Em suma, o VNet peering permite que o endereço IP do próximo salto na «Tabela de rotas definidas pelo utilizador» seja o endereço IP de uma máquina virtual na rede virtual em modo de peering.

## Gateways e conetividade no local
Cada rede virtual, independentemente de estar ou não em modo de peering com outra rede virtual, pode continuar a ter um gateway próprio e a utilizá-lo para se ligar no local. Os utilizadores também podem configurar [ligações VNet a VNet](../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md) através de gateways, mesmo que as redes virtuais estejam em modo de peering.

Quando ambas as opções de inter-conetividade de rede virtual estiverem configuradas, o tráfego entre as redes virtuais irá fluir através da configuração em modo de peering (ou seja, através da estrutura principal do Azure).

Quando as redes virtuais estão em modo de peering, os utilizadores também podem configurar para utilizar o gateway na rede virtual em modo de peering como um ponto de trânsito para o local. Neste caso, a rede virtual que está a utilizar um gateway remoto não pode ter um gateway próprio. Uma rede virtual pode ter apenas um gateway. Pode ser um gateway local ou um gateway remoto (na rede virtual em modo de peering), conforme mostrado na seguinte imagem.

O trânsito de gateway não é suportado na relação de modo de peering entre as redes virtuais que utilizam o modelo do Resource Manager e as que utilizam o modelo de implementação clássico. Ambas as redes virtuais na relação de modo de peering têm de utilizar o modelo de implementação do Resource Manager para que o trânsito de gateway funcione.

Quando as redes virtuais que partilham uma única ligação ExpressRoute do Azure estão em modo de peering, o tráfego entre as mesmas passa pela relação de peering (ou seja, pela rede principal do Azure). Os utilizadores podem continuar a utilizar gateways locais em cada rede virtual para ligar ao circuito no local. Em alternativa, podem utilizar um gateway partilhado e configurar trânsito para conetividade no local.

![Trânsito do VNet peering](./media/virtual-networks-peering-overview/figure02.png)

## Aprovisionamento
O VNet peering é uma operação com privilégios. É uma função separada no espaço de nomes de Redes Virtuais. Podem ser concedidos direitos específicos a um utilizador para autorizar o peering. Um utilizador que tenha acesso de leitura/escrita à rede virtual herda automaticamente estes direitos.

Um utilizador que seja um administrador ou um utilizador com privilégios da capacidade de peering pode iniciar uma operação de peering para outra VNet. Se houver um pedido de correspondência para peering no outro lado e forem cumpridos outros requisitos, o peering será estabelecido.

Consulte os artigos disponíveis na secção «Passos seguintes» para saber mais sobre como estabelecer VNet peering entre duas redes virtuais.

## Limites
Existem limites em relação ao número de peerings que são permitidos para uma única rede virtual. Consulte [Limites de rede do Azure](../azure-subscription-service-limits.md#networking-limits) para obter mais informações.

## Preços
O VNet peering não será cobrado durante o período de avaliação. Uma vez lançado, haverá uma cobrança nominal sobre o tráfego de entrada e saída que utiliza o peering. Para obter mais informações, consulte a [página de preços](https://azure.microsoft.com/pricing/details/virtual-network).

## Passos seguintes
* [Configurar peering entre redes virtuais](virtual-networks-create-vnetpeering-arm-portal.md).
* Saiba mais sobre [NSGs](virtual-networks-nsg.md).
* Saiba mais sobre [rotas definidas pelo utilizador e reencaminhamento IP](virtual-networks-udr-overview.md).

<!--HONumber=Sep16_HO4-->


