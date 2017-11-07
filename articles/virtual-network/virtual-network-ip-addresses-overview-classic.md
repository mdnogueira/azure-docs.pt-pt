---
title: "Tipos de endereços IP no Azure (clássica) | Microsoft Docs"
description: "Saiba mais sobre públicos e privados endereços IP (clássica) no Azure."
services: virtual-network
documentationcenter: na
author: jimdial
manager: carmonm
editor: tysonn
tags: azure-service-management
ms.assetid: 2f8664ab-2daf-43fa-bbeb-be9773efc978
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/11/2016
ms.author: jdial
ms.openlocfilehash: d5eea5e4499b9de40002ce2fc6aac39239c41b19
ms.sourcegitcommit: 295ec94e3332d3e0a8704c1b848913672f7467c8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/06/2017
---
# <a name="ip-address-types-and-allocation-methods-classic-in-azure"></a>Tipos de endereços IP e os métodos de alocação no Azure (clássico)
Pode atribuir endereços IP a recursos do Azure para comunicar com outros recursos do Azure, a rede no local e a Internet. Existem dois tipos de endereços IP, pode utilizar no Azure: públicas e privadas.

Endereços IP públicos são utilizados para comunicação com a Internet, incluindo os serviços de destinado ao público do Azure.

Endereços IP privados são utilizados para comunicação dentro de uma rede virtual do Azure (VNet), um serviço em nuvem e a sua rede no local ao utilizar um gateway de VPN ou pelo circuito ExpressRoute para expandir a sua rede para o Azure.

> [!IMPORTANT]
> O Azure tem dois modelos de implementação diferentes para criar e trabalhar com os recursos: [Resource Manager e clássico](../resource-manager-deployment-model.md).  Este artigo cobre a utilização do modelo de implementação clássica. A Microsoft recomenda que implementações mais novas utilizem o Gestor de recursos. Saiba mais sobre os endereços IP no Gestor de recursos ao ler o [endereços IP](virtual-network-ip-addresses-overview-arm.md) artigo.

## <a name="public-ip-addresses"></a>Endereços IP públicos
Os endereços IP públicos permitem aos recursos do Azure comunicar com a Internet e com serviços destinados ao cliente do Azure, como a [Azure Redis Cache](https://azure.microsoft.com/services/cache/), os [Hubs de Eventos do Azure](https://azure.microsoft.com/services/event-hubs/), as [Bases de Dados SQL](../sql-database/sql-database-technical-overview.md) e o [Armazenamento do Azure](../storage/common/storage-introduction.md).

Um endereço IP público está associado com os seguintes tipos de recursos:

* Serviços em nuvem
* Máquinas virtuais (VMs) IaaS
* Instâncias de função de PaaS
* Gateways de VPN
* Gateways de aplicação

### <a name="allocation-method"></a>Método de alocação
Quando um endereço IP público tem de ser atribuídos a um recurso do Azure, é *dinamicamente* alocados a partir de um agrupamento de endereço IP público disponível na localização do recurso é criado. Este endereço IP é lançado quando o recurso está parado. No caso de um serviço em nuvem, esta situação acontece quando todas as instâncias de função são paradas, que pode ser evitada, utilizando um *estático* endereço IP (reservado) (consulte [serviços em nuvem](#Cloud-services)).

> [!NOTE]
> A lista de intervalos de IP a partir da qual os endereços IP públicos são atribuídos para recursos do Azure está publicada no [intervalos de IP de Datacenter do Azure](https://www.microsoft.com/download/details.aspx?id=41653).
> 
> 

### <a name="dns-hostname-resolution"></a>Resolução de nomes de anfitrião DNS
Quando criar um serviço em nuvem ou de uma VM do IaaS, terá de fornecer um nome de DNS do serviço de nuvem que é o único em todos os recursos no Azure. Esta ação cria um mapeamento nos servidores DNS geridos pelo Azure para *dnsname*. cloudapp.net para o endereço IP público do recurso. Por exemplo, quando criar um serviço em nuvem com um nome DNS do serviço de nuvem de **contoso**, o nome de domínio completamente qualificado (FQDN) **contoso.cloudapp.net** irá resolver para um endereço IP público (VIP) do o serviço em nuvem. Pode utilizar este FQDN para criar um registo CNAME de domínio personalizado que aponte para o endereço IP público no Azure.

### <a name="cloud-services"></a>Serviços em nuvem
Um serviço em nuvem tem sempre um endereço IP público que referida como um endereço IP virtual (VIP). Pode criar pontos finais num serviço em nuvem para associar portas diferentes no VIP portas internas na VMs e instâncias de função no âmbito do serviço de nuvem. 

Um serviço em nuvem pode conter várias VMs de IaaS ou instâncias de função de PaaS, todos os expostas através do mesmo VIP de serviço de nuvem. Também pode atribuir [vários VIPs para um serviço em nuvem](../load-balancer/load-balancer-multivip.md), que permite cenários de vários VIP como ambiente de multi-inquilino com Web sites baseados em SSL.

Pode garantir o endereço IP público de um serviço em nuvem permanece com os mesmos, mesmo quando todas as instâncias de função são paradas, utilizando um *estático* endereço IP público, referido como [IP reservado](virtual-networks-reserved-public-ip.md). Pode criar um recurso IP estático (reservado) numa localização específica e atribuí-la a qualquer serviço de nuvem nessa localização. Não é possível especificar o endereço IP real para o IP reservado, é atribuído a partir de conjunto de endereços IP disponíveis na localização que é criado. Este endereço IP não é libertado até eliminar explicitamente.

Estáticos (reservados) os endereços IP públicos são frequentemente utilizados nos cenários em que um serviço em nuvem:

* necessita de regras de firewall para ser definido por utilizadores finais.
* depende da resolução do nome DNS externa, e um IP dinâmico precisaria de atualizar um registos.
* consome serviços web externo que utilizam o modelo de segurança IP com base.
* utiliza certificados SSL associados a um endereço IP.

> [!NOTE]
> Quando cria uma VM clássica, um contentor *serviço em nuvem* é criado pelo Azure, o que tem um endereço IP virtual (VIP). Quando a criação é feita através do portal, uma predefinição RDP ou SSH *endpoint* é configurado pelo portal para que possa ligar à VM através do VIP de serviço em nuvem. Pode ser reservada este VIP do serviço de nuvem, que fornece efetivamente um endereço IP reservado para ligar à VM. Pode abrir portas adicionais configurando pontos finais mais.
> 
> 

### <a name="iaas-vms-and-paas-role-instances"></a>VMs de IaaS e PaaS instâncias de função
Pode atribuir um endereço IP público diretamente a uma IaaS [VM](../virtual-machines/virtual-machines-linux-about.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) ou instância de função de PaaS dentro de um serviço em nuvem. Isto é referido como um endereço IP público de nível de instância ([ILPIP](virtual-networks-instance-level-public-ip.md)). Este endereço IP público só pode ser dinâmico.

> [!NOTE]
> Isto é diferente do VIP do serviço de nuvem, o que é um contentor para as VMs de IaaS ou PaaS instâncias da função, uma vez que um serviço em nuvem pode conter várias VMs de IaaS ou instâncias de função de PaaS, todos os exposta através do mesmo VIP de serviço de nuvem.
> 
> 

### <a name="vpn-gateways"></a>Gateways de VPN
A [gateway de VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md) podem ser utilizadas para ligar uma VNet do Azure a outras redes das VNets do Azure ou no local. Um gateway de VPN é atribuído um endereço IP público *dinamicamente*, que permite a comunicação com a rede remota.

### <a name="application-gateways"></a>Gateways de aplicação
Um Azure [gateway de aplicação](../application-gateway/application-gateway-introduction.md) pode ser utilizado para Layer7 balanceamento de carga para encaminhar o tráfego de rede com base em HTTP. Gateway de aplicação é atribuído um endereço IP público *dinamicamente*, que funciona como o VIP com balanceamento de carga.

### <a name="at-a-glance"></a>Síntese
A tabela abaixo mostra cada tipo de recurso com os métodos possíveis alocação (dinâmico/estático) e a capacidade de atribuir vários endereços IP públicos.

| Recurso | Dinâmica | Estático | Vários endereços IP |
| --- | --- | --- | --- |
| Serviço em nuvem |Sim |Sim |Sim |
| Instância de função de VM do IaaS ou PaaS |Sim |Não |Não |
| Gateway de VPN |Sim |Não |Não |
| Gateway de aplicação |Sim |Não |Não |

## <a name="private-ip-addresses"></a>Endereços IP Privados
Endereços IP privados permitem que os recursos do Azure comunicar com outros recursos num serviço em nuvem ou um [rede virtual](virtual-networks-overview.md)(VNet), ou a rede no local (através de um gateway de VPN ou um circuito ExpressRoute), sem utilizar um Endereço IP acessível para a Internet.

No modelo de implementação clássica do Azure, pode ser atribuído um endereço IP privado para os seguintes recursos do Azure:

* VMs de IaaS e PaaS instâncias de função
* Balanceador de carga interno
* Gateway de aplicação

### <a name="iaas-vms-and-paas-role-instances"></a>VMs de IaaS e PaaS instâncias de função
Máquinas virtuais (VMs) com o modelo de implementação clássica são sempre colocadas num serviço em nuvem, semelhante a instâncias de função de PaaS. O comportamento de endereços IP privados, deste modo, são semelhantes para estes recursos.

É importante ter em atenção que um serviço em nuvem pode ser implementado em duas formas:

* Como um *autónomo* serviço, onde não é dentro de uma rede virtual em nuvem.
* Como parte de uma rede virtual.

#### <a name="allocation-method"></a>Método de alocação
Em case de um *autónomo* serviço, atribuir um endereço IP privado de get de recursos em nuvem *dinamicamente* intervalo de endereços de IP privado datacenter do Azure. Pode ser utilizado apenas para comunicação com as outras VMs no mesmo serviço em nuvem. Este endereço IP pode ser alteradas quando o recurso está parado e iniciado.

Em caso de um serviço em nuvem implementado dentro de uma rede virtual, recursos obter endereços IP privado alocado no intervalo de endereços do subnet(s) associado (conforme especificado na configuração da rede). Este endereços IP privados pode ser utilizado para comunicação entre todas as VMs dentro da VNet.

Além disso, no caso de serviços em nuvem numa VNet, um endereço IP privado atribuído *dinamicamente* (utilizando DHCP) por predefinição. -Pode alterar quando o recurso está parado e iniciado. Para garantir que o endereço IP permanece o mesmo, tem de definir o método de alocação *estático*e forneça um endereço IP válido no intervalo de endereços correspondentes.

Geralmente, os endereços IP privados estáticos são utilizados para:

* VMs que funcionam como controladores de domínio ou servidores DNS.
* VMs que requerem regras de firewall utilizando endereços IP.
* VMs com serviços acedidos por outras aplicações através de um endereço IP.

#### <a name="internal-dns-hostname-resolution"></a>Resolução de nome de anfitrião DNS interna
Todas as instâncias de função de PaaS e VMs do Azure estão configuradas com [servidores DNS geridos pelo Azure](virtual-networks-name-resolution-for-vms-and-role-instances.md#azure-provided-name-resolution) por predefinição, a menos que configurar explicitamente servidores DNS personalizados. Estes servidores DNS fornecem a resolução dos nomes internos para VMs e instâncias de função que residem no mesmo serviço de nuvem ou de VNet.

Quando cria uma VM, é adicionado aos servidores DNS geridos pelo Azure um mapeamento do nome de anfitrião para o respetivo endereço IP privado. Em caso de uma VM de várias NIC, o nome do anfitrião está mapeado para o endereço IP privado de NIC primário. No entanto, estas informações de mapeamento são restringidas aos recursos no mesmo serviço em nuvem ou VNet.

Em case de um *autónomo* , o serviço em nuvem será capaz de resolver os nomes de anfitrião de todas as VMs/instâncias de função no mesmo serviço em nuvem apenas. Em caso de um serviço em nuvem numa VNet, será capaz de resolver os nomes de anfitrião de todas as instâncias de VMs/função dentro da VNet.

### <a name="internal-load-balancers-ilb--application-gateways"></a>Balanceadores de carga internos (ILBs) e gateways de aplicação
Pode atribuir um endereço IP privado à configuração de **front-end** de um [Balanceador de Carga Interno do Azure](../load-balancer/load-balancer-internal-overview.md) (ILB) ou de um [Gateway de Aplicação](../application-gateway/application-gateway-introduction.md). Este endereço IP privado funciona como o ponto final interno, que só é acessível pelos recursos dentro da respetiva rede virtual (VNet) e pelas redes remotas ligadas à VNet. Pode atribuir um endereço IP privado dinâmico ou estático à configuração de front-end. Também pode atribuir vários endereços IP privados para ativar cenários de vários vip.

### <a name="at-a-glance"></a>Síntese
A tabela abaixo mostra cada tipo de recurso com os métodos de alocação possíveis (dinâmico/estático) e a capacidade de atribuir vários endereços IP privados.

| Recurso | Dinâmica | Estático | Vários endereços IP |
| --- | --- | --- | --- |
| VM (num *autónomo* VNet ou o serviço de nuvem) |Sim |Sim |Sim |
| Instância de função de PaaS (num *autónomo* VNet ou o serviço de nuvem) |Sim |Não |Não |
| Fim de front-de Balanceador de carga interno |Sim |Sim |Sim |
| Front end gateway de aplicação |Sim |Sim |Sim |

## <a name="limits"></a>Limites
A tabela abaixo mostra os limites impostos no IP endereçamento no Azure por subscrição. Pode [contactar o suporte](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade) para aumentar os limites predefinidos até aos limites máximos, com base nas necessidades da sua atividade.

|  | Limite predefinido | Limite máximo |
| --- | --- | --- |
| Endereços IP públicos (dinâmico) |5 |contactar o suporte |
| Endereços IP públicos reservados |20 |contactar o suporte |
| VIP público por implementação (serviço de nuvem) |5 |contactar o suporte |
| Privada VIP (ILB) por implementação (serviço de nuvem) |1 |1 |

Certifique-se de que leia o conjunto completo de [limita para funcionamento em rede](../azure-subscription-service-limits.md#networking-limits) no Azure.

## <a name="pricing"></a>Preços
Na maioria dos casos, os endereços IP públicos são gratuitos. Há uma cobrança nominal para utilizar endereços IP públicos adicionais e/ou estáticos. Certifique-se de que compreende o [preços estrutura para IPs públicos](https://azure.microsoft.com/pricing/details/ip-addresses/).

## <a name="differences-between-resource-manager-and-classic-deployments"></a>Diferenças entre implementações clássicas e do Resource Manager
Segue-se uma comparação das funcionalidades de endereçamento de IP no modelo de implementação clássica e Resource Manager.

|  | Recurso | Clássica | Resource Manager |
| --- | --- | --- | --- |
| **Endereço IP público** |***VM*** |Referido como um ILPIP (apenas dinâmico) |Referido como um IP público (dinâmico ou estático) |
|  ||Atribuído a uma VM do IaaS ou uma instância de função de PaaS |Associado a NIC da VM | |
|  |***Balanceador de carga com acesso à Internet*** |Referido como VIP (dinâmico) ou o IP reservado (estático) |Referido como um IP público (dinâmico ou estático) | |
|  ||Atribuído a um serviço em nuvem |Associado a configuração de front-end do Balanceador de carga | |
|  | | | |
| **Endereço IP privado** |***VM*** |Referido como um DIP |Referido como um endereço IP privado |
|  ||Atribuído a uma VM do IaaS ou uma instância de função de PaaS |Atribuído a NIC da VM | |
|  |***Balanceador de carga interno (ILB)*** |Atribuído para o ILB (dinâmico ou estático) |Atribuído à configuração de front-end do ILB (dinâmica ou estática) | |

## <a name="next-steps"></a>Passos seguintes
* [Implementar uma VM com um endereço IP privado estático](virtual-networks-static-private-ip-classic-pportal.md) no portal do Azure.

