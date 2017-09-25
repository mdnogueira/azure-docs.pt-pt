---
title: "Tipos de endereços IP no Azure | Microsoft Docs"
description: "Saiba mais sobre os endereços IP públicos e privados no Azure."
services: virtual-network
documentationcenter: na
author: jimdial
manager: carmonm
editor: tysonn
tags: azure-resource-manager
ms.assetid: 610b911c-f358-4cfe-ad82-8b61b87c3b7e
ms.service: virtual-network
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/27/2016
ms.author: jdial
ms.translationtype: HT
ms.sourcegitcommit: 83f19cfdff37ce4bb03eae4d8d69ba3cbcdc42f3
ms.openlocfilehash: 144f4ea213b8ed0a3530495e185f489155c474c9
ms.contentlocale: pt-pt
ms.lasthandoff: 08/21/2017

---
# <a name="ip-address-types-and-allocation-methods-in-azure"></a>Tipos de endereços IP e métodos de alocação no Azure
Pode atribuir endereços IP a recursos do Azure para comunicar com outros recursos do Azure, a rede no local e a Internet. Existem dois tipos de endereços IP que pode utilizar no Azure:

* **Endereços IP públicos**: utilizados para comunicação com a Internet, incluindo os serviços do Azure destinados ao público
* **Endereços IP privados**: utilizados para comunicação dentro de uma rede virtual do Azure (VNet) e da sua rede no local quando utiliza um gateway de VPN ou o circuito do ExpressRoute para expandir a sua rede para o Azure.

> [!NOTE]
> O Azure tem dois modelos de implementação diferentes para criar e trabalhar com os recursos: [Resource Manager e clássico](../resource-manager-deployment-model.md).  Este artigo explica como utilizar o modelo de implementação Resource Manager, o que é recomendado pela Microsoft para a maioria das novas implementações em vez do [modelo de implementação clássica](virtual-network-ip-addresses-overview-classic.md).
> 

Se estiver familiarizado com o modelo de implementação clássica, veja as [diferenças dos endereços IP entre a implementação Clássica e Resource Manager](virtual-network-ip-addresses-overview-classic.md#differences-between-resource-manager-and-classic-deployments).

## <a name="public-ip-addresses"></a>Endereços IP públicos
Os endereços IP públicos permitem aos recursos do Azure comunicar com a Internet e com serviços destinados ao cliente do Azure, como a [Azure Redis Cache](https://azure.microsoft.com/services/cache/), os [Hubs de Eventos do Azure](https://azure.microsoft.com/services/event-hubs/), as [Bases de Dados SQL](../sql-database/sql-database-technical-overview.md) e o [Armazenamento do Azure](../storage/common/storage-introduction.md).

No Azure Resource Manager, os endereços [IP públicos](resource-groups-networking.md#public-ip-address) são recursos que tem as suas próprias propriedades. Pode associar um recurso de endereço IP público a qualquer um dos seguintes recursos:

* Máquinas virtuais (VM)
* Balanceadores de carga com acesso à Internet
* Gateways de VPN
* Gateways de aplicação

### <a name="allocation-method"></a>Método de alocação
Os endereços IP podem ser alocados a um recurso IP *público* de duas formas - *dinâmica* ou *estática*. O método de alocação predefinido é o *dinâmico*, no qual o endereço IP **não** é alocado no momento da criação. Em vez disso, o endereço IP público é alocado quando inicia (ou cria) o recurso associado (como uma VM ou um balanceador de carga). O endereço IP é libertado quando para (ou elimina) o recurso. Isto faz com que o endereço IP seja alterado quando para e inicia um recurso.

Para garantir que o endereço IP do recurso associado fica o mesmo, pode definir o método de alocação explicitamente como *estático*. Neste caso, é atribuído imediatamente um endereço IP. Só é libertado quando elimina o recurso ou altera o método de alocação para *dinâmico*.

> [!NOTE]
> Mesmo se definir o método de alocação como *estático*, não pode especificar o endereço IP real atribuído ao *recurso de IP público*. Em vez disso, o endereço é alocado a partir de um conjunto de endereços IP disponíveis na localização do Azure na qual o recurso é criado.
>

Normalmente, os endereços IP públicos são utilizados nos cenários seguintes:

* Os utilizadores finais têm de atualizar as regras de firewall para comunicar com os seus recursos do Azure.
* Resolução de nomes DNS, em que a alteração de um endereço IP exigiria que os registos A fossem atualizados.
* Os seus recursos do Azure comunicam com outros serviços ou aplicações que utilizam um modelo de segurança baseado no endereço IP.
* Utiliza certificados SSL associados a um endereço IP.

> [!NOTE]
> A lista de intervalos de IP a partir da qual os endereços IP públicos (dinâmicos/estáticos) são alocados para os recursos do Azure está publicada em [Azure Datacenter IP ranges (Intervalos de IP de Datacenters do Azure)](https://www.microsoft.com/download/details.aspx?id=41653).
>

### <a name="dns-hostname-resolution"></a>Resolução de nomes de anfitrião DNS
Pode especificar uma etiqueta de nome de domínio DNS par um recurso IP público, que cria um mapeamento para *domainnamelabel*.*location*.cloudapp.azure.com para o endereço IP público nos servidores DNS geridos pelo Azure. Por exemplo, se criar um recurso de IP público com **contoso** como uma *domainnamelabel* na **localização** do Azure *E.U.A. Oeste*, o nome de domínio completamente qualificado (FQDN) **contoso.westus.cloudapp.azure.com** será resolvido para o endereço IP público do recurso. Pode utilizar este FQDN para criar um registo CNAME de domínio personalizado que aponte para o endereço IP público no Azure.

> [!IMPORTANT]
> Cada etiqueta de nome de domínio criada tem de ser exclusiva no âmbito da respetiva localização do Azure.  
>

### <a name="virtual-machines"></a>Máquinas virtuais
Pode associar um endereço IP público a uma VM do [Windows](../virtual-machines/windows/overview.md) ou do [Linux](../virtual-machines/virtual-machines-linux-about.md) ao atribuí-la para a respetiva **interface de rede**. No caso de VMs com várias interfaces de rede, pode atribuí-lo à interface de rede *primária* apenas. Pode atribuir um endereço IP público dinâmico ou estático a uma VM.

### <a name="internet-facing-load-balancers"></a>Balanceadores de carga com acesso à Internet
Pode associar um endereço IP público a um [Balanceador de Carga do Azure](../load-balancer/load-balancer-overview.md) ao atribuí-lo à configuração do **front-end** do balanceador. Este endereço IP serve como um endereço IP virtual (VIP) com balanceamento de carga. Pode atribuir um endereço IP público dinâmico ou estático a um front-end de balanceador de carga. Também pode atribuir múltiplos endereços IP públicos a um front-end de balanceador de carga, que permite cenários de [vários VIPs](../load-balancer/load-balancer-multivip.md), como um ambiente multi-inquilinos com Web sites baseados em SSL.

### <a name="vpn-gateways"></a>Gateways de VPN
O [Gateway de VPNs do Azure](../vpn-gateway/vpn-gateway-about-vpngateways.md) é utilizado para ligar uma rede virtual do Azure (VNet) a outras VNets do Azure ou a uma rede no local. Tem de atribuir um endereço IP público à respetiva **configuração de IP**, para que possa comunicar com a rede remota. Atualmente, só pode atribuir um endereço IP público *dinâmico* a um gateway de VPN.

### <a name="application-gateways"></a>Gateways de aplicação
Pode associar um endereço IP público a um [Gateway de Aplicação](../application-gateway/application-gateway-introduction.md) do Azure, ao atribuí-lo à configuração do **front-end** do gateway. Este endereço IP público funciona como um VIP com balanceamento de carga. Atualmente, só pode atribuir um endereço IP público *dinâmico* à configuração de front-end de um gateway de aplicação.

### <a name="at-a-glance"></a>De relance
A tabela abaixo mostra a propriedade específica através da qual os endereços IP públicos podem ser associados a recursos de nível superior e os métodos de alocação possíveis (dinâmica ou estática) que podem ser utilizados.

| Recurso de nível superior | Associação de endereço IP | Dinâmica | Estático |
| --- | --- | --- | --- |
| Máquina virtual |Interface de rede |Sim |Sim |
| Load balancer |Configuração de front-end |Sim |Sim |
| Gateway de VPN |Configuração de IP do gateway |Sim |Não |
| Gateway de aplicação |Configuração de front-end |Sim |Não |

## <a name="private-ip-addresses"></a>Endereços IP Privados
Os endereços IP privados permitem aos recursos do Azure comunicar com outros recursos numa [rede virtual](virtual-networks-overview.md) ou numa rede no local através de um gateway de VPN ou de um circuito do ExpressRoute, sem utilizar um endereço IP acessível pela Interne.

No modelo de implementação Azure Resource Manager, os endereços IP privados são associados aos tipos de recursos do Azure seguintes:

* VMs
* Balanceadores de carga internos (ILBs)
* Gateways de aplicação

### <a name="allocation-method"></a>Método de alocação
Os endereços IP privados são alocados a partir do intervalo de endereços da sub-rede à qual o recurso está ligado. O próprio intervalo de endereços da sub-rede faz parte do intervalo de endereços da VNet.

Os endereços IP privados podem ser alocados de duas formas - *dinâmica* ou *estática*. O método de alocação predefinido é o *dinâmico*, no qual o endereço IP é alocado automaticamente a partir da sub-rede do recurso (mediante a utilização de DHCP). Este endereço IP pode ser alterado quando o recurso é parado e iniciado.

Pode definir o método de alocação como *estático*, para garantir que o endereço IP permanece o mesmo. Neste caso, também tem de indicar um endereço IP válido que faça parte da sub-rede do recurso.

Geralmente, os endereços IP privados estáticos são utilizados para:

* VMs que funcionam como controladores de domínio ou servidores DNS.
* Recursos que precisam de regras de firewall que utilizem endereços IP.
* Recursos acedidos por outros recursos/aplicações através de um endereço IP.

### <a name="virtual-machines"></a>Máquinas virtuais
É atribuído um endereço IP privado à **interface de rede** de uma VM do [Windows](../virtual-machines/windows/overview.md) ou do [Linux](../virtual-machines/virtual-machines-linux-about.md). No caso de VMs de várias interfaces de rede, é atribuído um endereço IP privado a cada interface. Pode especificar o método de alocação como dinâmico ou estático para as interfaces de rede.

#### <a name="internal-dns-hostname-resolution-for-vms"></a>Resolução de nomes de anfitrião DNS interna (para VMs)
Todas as VMs do Azure são configuradas com [servidores DNS geridos pelo Azure](virtual-networks-name-resolution-for-vms-and-role-instances.md#azure-provided-name-resolution) por predefinição, a não ser que configure explicitamente servidores DNS personalizados. Estes servidores DNS fornecem resolução de nomes interna para as VMs que residam na mesma VNet.

Quando cria uma VM, é adicionado aos servidores DNS geridos pelo Azure um mapeamento do nome de anfitrião para o respetivo endereço IP privado. Nos casos de VMs com várias interfaces de rede, o nome de anfitrião é mapeado para o endereço IP privado da interface de rede primária.

As VMs configuradas com servidores DNS geridos pelo Azure conseguirão resolver os nomes de anfitrião de todas as VMs dentro da VNet para os respetivos endereços IP privados.

### <a name="internal-load-balancers-ilb--application-gateways"></a>Balanceadores de carga internos (ILBs) e gateways de aplicação
Pode atribuir um endereço IP privado à configuração de **front-end** de um [Balanceador de Carga Interno do Azure](../load-balancer/load-balancer-internal-overview.md) (ILB) ou de um [Gateway de Aplicação](../application-gateway/application-gateway-introduction.md). Este endereço IP privado funciona como o ponto final interno, que só é acessível pelos recursos dentro da respetiva rede virtual (VNet) e pelas redes remotas ligadas à VNet. Pode atribuir um endereço IP privado dinâmico ou estático à configuração de front-end.

### <a name="at-a-glance"></a>De relance
A tabela abaixo mostra a propriedade específica através da qual os endereços IP privados podem ser associados a recursos de nível superior e os métodos de alocação possíveis (dinâmica ou estática) que podem ser utilizados.

| Recurso de nível superior | Associação de endereço IP | Dinâmica | Estático |
| --- | --- | --- | --- |
| Máquina virtual |Interface de rede |Sim |Sim |
| Load balancer |Configuração de front-end |Sim |Sim |
| Gateway de aplicação |Configuração de front-end |Sim |Sim |

## <a name="limits"></a>Limites
Os limites impostos no endereçamento IP estão indicados no conjunto completo de [limites para redes](../azure-subscription-service-limits.md#networking-limits) no Azure. Estes limites são por região e por subscrição. Pode [contactar o suporte](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade) para aumentar os limites predefinidos até aos limites máximos, com base nas necessidades da sua atividade.

## <a name="pricing"></a>Preços
Os endereços IP públicos podem ter custos nominais. Para saber mais sobre os preços dos endereços IP no Azure, reveja a página [Preços de Endereços IP](https://azure.microsoft.com/pricing/details/ip-addresses).

## <a name="next-steps"></a>Passos seguintes
* [Deploy a VM with a static public IP using the Azure portal (Implementar uma VM com IP público estático através do portal do Azure)](virtual-network-deploy-static-pip-arm-portal.md)
* [Deploy a VM with a static public IP using a template (Implementar uma VM com um IP estático público através de um modelo)](virtual-network-deploy-static-pip-arm-template.md)
* [Deploy a VM with a static private IP address using the Azure portal (Implementar uma VM com um endereço IP privado estático através do portal do Azure)](virtual-networks-static-private-ip-arm-pportal.md)
