---
title: Descrição geral da Rede Virtual do Azure (VNet)
description: Saiba mais sobre redes virtuais (VNets) no Azure.
services: virtual-network
documentationcenter: na
author: jimdial
manager: carmonm
editor: tysonn

ms.service: virtual-network
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/15/2016
ms.author: jdial

---
# Descrição Geral da Rede Virtual
Uma rede virtual do Azure (VNet) é uma representação da sua própria rede na nuvem.  É um isolamento lógico da nuvem do Azure dedicado à sua subscrição. Pode controlar totalmente os blocos de endereços IP, as definições de DNS, as políticas de segurança e as tabelas de rotas dentro desta rede. Pode também segmentar ainda mais a sua VNet em sub-redes e iniciar máquinas virtuais (VMs) IaaS do Azure e/ou [serviços em nuvem (instâncias de função PaaS)](../cloud-services/cloud-services-choose-me.md). Além disso, pode ligar a rede virtual à sua rede no local através de uma das [opções de conectividade](../vpn-gateway/vpn-gateway-cross-premises-options.md) disponíveis no Azure. Essencialmente, pode expandir a sua rede para o Azure, com controlo total sobre blocos de endereços IP com a vantagem da escala empresarial que o Azure oferece.

Para melhor compreender as VNets, observe a figura abaixo, que mostra uma rede no local simplificada.

![Rede no local](./media/virtual-networks-overview/figure01.png)

A figura acima mostra uma rede no local ligada à Internet pública através de um router. Também pode ver uma firewall entre o router e um DMZ que aloja um servidor DNS e um farm de servidores Web. O farm de servidores Web tem balanceamento de carga, utilizando um balanceador de carga de hardware que é exposto à Internet, e consome recursos da sub-rede interna. A sub-rede interna está separada do DMZ por outra firewall e aloja servidores de Controlador de Domínio do Active Directory, servidores de base de dados e servidores de aplicações.

A mesma rede pode ser alojada no Azure tal como ilustrado na imagem abaixo.

![Rede virtual do Azure](./media/virtual-networks-overview/figure02.png)

Repare como a infraestrutura do Azure assume a função de router, que lhe permitirá aceder a partir da sua VNet à Internet pública sem necessitar de qualquer configuração. As firewalls podem ser substituídas por Grupos de Segurança de Rede (NSGs) aplicados a cada sub-rede individual. Os balanceadores de carga físicos são substituídos por balanceadores de carga internos e com acesso à Internet no Azure.

> [!NOTE]
> Existem dois modos de implementação no Azure: clássica (também conhecida como a Gestão de Serviço) e o Azure Resource Manager (ARM). As VNets clássicas podem ser adicionadas a um grupo de afinidades ou criadas como uma VNet regional. Se tiver uma VNet num grupo de afinidades, é recomendado que a [migre para uma VNet regional](virtual-networks-migrate-to-regional-vnet.md).
> 
> 

## Vantagens da Rede Virtual
* **Isolamento**. As VNets estão completamente isoladas entre si. Isto permite-lhe criar redes não contíguas para desenvolvimento, teste e produção, que utilizam os mesmos blocos de endereços CIDR.
* **Acesso à Internet pública**. Todas as VMs de IaaS e as instâncias de função de PaaS numa VNet podem aceder à Internet pública por predefinição. Pode controlar o acesso através de Grupos de Segurança de Rede (NSGs).
* **Acesso a VMs dentro da VNet**. As instâncias de função de PaaS e as VMs de IaaS podem ser iniciadas na mesma rede virtual e podem ligar-se entre si utilizando endereços IP privados, mesmo que estejam em sub-redes diferentes, sem ser necessário configurar um gateway ou utilizar endereços IP públicos.
* **Resolução de nomes**. O Azure oferece resolução de nomes internos para VMs de IaaS e instâncias de função de PaaS implementadas na sua VNet. Também pode implementar os seus próprios servidores DNS e configurar a VNet para utilizá-los.
* **Segurança**. O tráfego que entra e sai das máquinas virtuais e das instâncias de função de PaaS numa VNet pode ser controlado através de Grupos de Segurança de Rede.
* **Conectividade**. As VNets podem ser ligadas entre si, e até mesmo ao datacenter no local, através de uma ligação de rede de VPNs ou de uma ligação ExpressRoute. Para obter mais informações sobre gateways de VPN, consulte o artigo [Sobre os gateways de VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md). Para saber mais sobre o ExpressRoute, consulte o artigo [Descrição geral técnica do ExpressRoute](../expressroute/expressroute-introduction.md).
  
  > [!NOTE]
  > Certifique-se de que cria uma VNet antes de implementar quaisquer VMs de IaaS ou instâncias de função de PaaS no seu ambiente do Azure. As VMs baseadas no ARM precisam de uma VNet e, se não especificar uma VNet existente, o Azure cria uma VNet predefinida que poderá ter um conflito de blocos de endereços CIDR com a sua rede no local, impossibilitando a ligação da VNet à sua rede no local.
  > 
  > 

## Sub-redes
Uma sub-rede é um intervalo de endereços IP na VNet. Pode dividir uma VNet em várias sub-redes por questões de organização e de segurança. As VMs e as instâncias de função de PaaS implementadas em sub-redes (nas mesmas ou em diferentes) dentro de uma VNet podem comunicar entre si sem qualquer configuração adicional. Também pode configurar tabelas de rotas e NSGs para uma sub-rede.

## Endereços IP
Existem dois tipos de endereços IP atribuídos a recursos no Azure: *públicos* e *privados*. Os Endereços IP Públicos permitem que os recursos do Azure comuniquem com a Internet e com outros serviços do Azure destinados ao público, como a [Cache de Redis do Azure](https://azure.microsoft.com/services/cache/) e os [Event Hubs do Azure](https://azure.microsoft.com/documentation/services/event-hubs/). Os Endereços IP Privados permitem a comunicação entre os recursos numa rede virtual, juntamente com os recursos ligados através de uma VPN, sem utilizar endereços IP encaminháveis para a Internet.

Para saber mais sobre os endereços IP no Azure, consulte o artigo [Endereços IP na rede virtual](virtual-network-ip-addresses-overview-arm.md)

## Balanceadores de carga do Azure
As máquinas virtuais e os serviços em nuvem numa rede virtual podem ser expostos à Internet com balanceadores de carga do Azure. As aplicações de linha de negócio destinadas apenas a utilização interna podem ter balanceamento de carga, utilizando o balanceador de carga interno.

* **Balanceador de carga externo**. Pode utilizar um balanceador de carga externo para fornecer elevada disponibilidade a VMs de IaaS e a instâncias de função de PaaS acedidas a partir da Internet pública.
* **Balanceador de carga interno**. Pode utilizar um balanceador de carga interno para fornecer elevada disponibilidade a VMs de IaaS e a instâncias de função de PaaS acedidas a partir de outros serviços na sua VNet.

Para obter mais informações sobre o balanceamento de carga no Azure, consulte o artigo [Descrição geral do balanceador de carga](../load-balancer/load-balancer-overview.md).

## Grupo de Segurança de Rede (NSG)
Pode criar NSGs para controlar o acesso de entrada e de saída a interfaces de rede (NICs), VMs e sub-redes. Cada NSG contém uma ou mais regras que especificam se o tráfego é ou não aprovado ou negado com base no endereço IP de origem, na porta de origem, no endereço IP de destino e na porta de destino. Para saber mais sobre NSGs, consulte o artigo [O que é um Grupo de Segurança de Rede](virtual-networks-nsg.md).

## Aplicações virtuais
Uma aplicação virtual é apenas outra VM na sua VNet, que executa uma função de aplicação baseada em software, como uma firewall, otimização de WAN ou deteção de intrusões. Pode criar uma rota no Azure para encaminhar o tráfego da VNet através de uma aplicação virtual para utilizar as suas capacidades.

Por exemplo, podem ser utilizados NSGs para fornecer segurança na sua VNet. No entanto, os NSGs fornecem uma Lista de Controlo de Acesso (ACL) de camada 4 aos pacotes de entrada e de saída. Se pretender utilizar um modelo de segurança de camada 7, terá de utilizar uma aplicação de firewall.

As aplicações virtuais dependem de [rotas definidas pelo utilizador e de reencaminhamento IP](virtual-networks-udr-overview.md).

## Limites
Existem limites sobre o número de Redes Virtuais permitidas numa subscrição. Consulte os [limites de redes do Azure](../azure-subscription-service-limits.md#networking-limits) para obter mais informações.

## Preços
Não existe um custo extra para utilizar Redes Virtuais no Azure. As instâncias de computação iniciadas dentro da Vnet serão cobradas a taxas padrão, tal como descrito em [Preços de VM do Azure](https://azure.microsoft.com/pricing/details/virtual-machines/). Os [Gateways de VPN](https://azure.microsoft.com/pricing/details/vpn-gateway/) e os [Endereços IP Públicos](https://azure.microsoft.com/pricing/details/ip-addresses/) utilizados na VNet também serão cobrados a taxas padrão.

## Passos seguintes
* [Criar uma VNet](virtual-networks-create-vnet-arm-pportal.md) e sub-redes.
* [Criar uma VM numa VNet](../virtual-machines/virtual-machines-windows-hero-tutorial.md).
* Saiba mais sobre [NSGs](virtual-networks-nsg.md).
* Saiba mais sobre [rotas definidas pelo utilizador e reencaminhamento IP](virtual-networks-udr-overview.md).

<!--HONumber=ago16_HO4-->


