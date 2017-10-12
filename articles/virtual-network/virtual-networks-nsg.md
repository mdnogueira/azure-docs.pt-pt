---
title: "Grupos de segurança de rede no Azure | Microsoft Docs"
description: "Saiba como isolar e controlar o fluxo de tráfego nas suas redes virtuais com a firewall distribuída no Azure através dos Grupos de Segurança de Rede."
services: virtual-network
documentationcenter: na
author: jimdial
manager: timlt
editor: tysonn
ms.assetid: 20e850fc-6456-4b5f-9a3f-a8379b052bc9
ms.service: virtual-network
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/11/2016
ms.author: jdial
ms.openlocfilehash: fac6ee69b5f0377e0515ac9abeb28788cbef9b79
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="filter-network-traffic-with-network-security-groups"></a>Filtrar o tráfego de rede com grupos de segurança de rede

Os grupos de segurança de rede (NSG) contêm uma lista de regras de segurança que permitem ou negam o tráfego de rede para recursos ligados a Redes Virtuais do Azure (VNet). Os NSGs podem ser associados a sub-redes, VMs individuais (clássicas) ou a interfaces de rede individuais (NIC) ligadas a VMs (Resource Manager). Quando um NSG é associado a uma sub-rede, as regras são aplicadas a todos os recursos ligados à mesma. É possível restringir ainda mais o tráfego ao associar também um NSG a uma VM ou a uma NIC.

> [!NOTE]
> O Azure tem dois modelos de implementação diferentes para criar e trabalhar com os recursos: [Resource Manager e clássico](../resource-manager-deployment-model.md). Este artigo inclui os dois modelos, mas a Microsoft recomenda que a maioria das implementações novas utilizem o modelo Resource Manager.

## <a name="nsg-resource"></a>Recurso NSG
Os NSGs contêm as propriedades seguintes:

| Propriedade | Descrição | Restrições | Considerações |
| --- | --- | --- | --- |
| Nome |Nome para o NSG |Tem de ser exclusivo dentro da região.<br/>Pode conter letras, números, carateres de sublinhado, pontos finais e hífenes.<br/>Tem de começar com uma letra ou um número.<br/>Tem de terminar com uma letra, um número ou um caráter de sublinhado.<br/>Não pode exceder os 80 carateres. |Uma vez que poderá ser necessário criar vários NSGs, certifique-se de que tem uma convenção de nomenclatura que facilite a identificação da função dos NSGs. |
| Região |A [região](https://azure.microsoft.com/regions) do Azure onde é criado o NSG. |Os NSGs só podem ser associados a recursos que estejam na mesma região que os mesmos. |Para saber mais sobre quantos NSGs pode ter por região, leia o artigo [Azure Limits](../azure-subscription-service-limits.md#virtual-networking-limits-classic) (Limites do Azure).|
| Grupo de recursos |O [grupo de recursos](../azure-resource-manager/resource-group-overview.md#resource-groups) no qual o NSG existe. |Embora os NSGs existam num grupo de recursos, podem ser associados a recursos em qualquer grupo de recursos, desde que os recursos façam parte da mesma região do Azure que os NSGs. |Os grupos de recursos são utilizados para gerir vários recursos em conjunto, como uma unidade de implementação.<br/>Poderá considerar agrupar os NSGs aos recursos a que estão associados. |
| Regras |Regras de entrada ou de saída que definem qual o tráfego permitido ou negado. | |Veja a secção [Regras de NSG](#Nsg-rules) deste artigo. |

> [!NOTE]
> As ACLs baseadas em ponto final e os grupos de segurança de rede não são suportados na mesma instância de VM. Se pretender utilizar um NSG e ter uma ACL de ponto final já implementada, remova primeiro a ACL de ponto final. Para saber como remover uma ACL, leia o artigo [Managing Access Control Lists (ACLs) for Endpoints by using PowerShell](virtual-networks-acl-powershell.md) (Utilizar o PowerShell para Gerir Listas de Controlo de Acesso [ACLs] para Pontos Finais).
> 

### <a name="nsg-rules"></a>Regras do NSG
As regras do NSG contêm as seguintes propriedades:

| Propriedade | Descrição | Restrições | Considerações |
| --- | --- | --- | --- |
| **Nome** |Nome da regra. |Tem de ser exclusivo dentro da região.<br/>Pode conter letras, números, carateres de sublinhado, pontos finais e hífenes.<br/>Tem de começar com uma letra ou um número.<br/>Tem de terminar com uma letra, um número ou um caráter de sublinhado.<br/>Não pode exceder os 80 carateres. |Pode ter várias regras dentro de um NSG, por isso, certifique-se de que segue uma convenção de nomenclatura que lhe permita identificar a função da sua regra. |
| **Protocolo** |Protocolo a corresponder para a regra. |TCP, UDP ou * |Utilizar * como protocolo inclui o ICMP (apenas tráfego Este-Oeste), assim como o UDP e TCP, e pode reduzir o número de regras necessárias.<br/>Ao mesmo tempo, utilizar * poderá ser uma abordagem demasiado abrangente, pelo que é recomendado que o utilize apenas quando for necessário. |
| **Intervalo de portas de origem** |O intervalo de portas de origem a corresponder para a regra. |Número de porta individual entre 1 e 65535, intervalo de portas (por exemplo, 1 - 65535) ou * (para todas as portas). |As portas de origem podem ser efémeras. A menos que o seu programa cliente esteja a utilizar uma porta específica, utilize * na maioria dos casos.<br/>Tente utilizar intervalos de portas sempre que possível para evitar a necessidade de várias regras.<br/>Várias portas ou intervalos de portas não podem ser agrupados por uma vírgula. |
| **Intervalo de portas de destino** |O intervalo de portas de destino a corresponder para a regra. |Número de porta individual entre 1 e 65 535, intervalo de portas (por exemplo, 1 - 65 635) ou \* (para todas as portas). |Tente utilizar intervalos de portas sempre que possível para evitar a necessidade de várias regras.<br/>Várias portas ou intervalos de portas não podem ser agrupados por uma vírgula. |
| **Prefixo do endereço de origem** |Prefixo do endereço de origem ou etiqueta a corresponder para a regra. |Endereço IP único (por exemplo, 10.10.10.10), sub-rede de IP (por exemplo, 192.168.1.0/24), [etiqueta predefinida](#default-tags) ou * (para todos os endereços). |Considere a utilização de intervalos, etiquetas predefinidas e * para reduzir o número de regras. |
| **Prefixo do endereço de destino** |Prefixo do endereço de destino ou etiqueta a corresponder para a regra. | Endereço IP único (por exemplo, 10.10.10.10), sub-rede de IP (por exemplo, 192.168.1.0/24), [etiqueta predefinida](#default-tags) ou * (para todos os endereços). |Considere a utilização de intervalos, etiquetas predefinidas e * para reduzir o número de regras. |
| **Direção** |Direção do tráfego a corresponder para a regra. |Entrada ou Saída |As regras de entrada e de saída são processadas em separado, com base na direção. |
| **Prioridade** |As regras são verificadas por ordem de prioridade. Quando for aplicada uma regra, não são testadas mais regras para correspondência. | Número entre 100 e 4096. | Considere a criação de regras que saltem as prioridades em 100 para cada regra, para deixar espaço para novas regras que venha a criar no futuro. |
| **Acesso** |Tipo de acesso a aplicar se a regra corresponder. | Permitir ou negar. | Tenha em atenção que, se não for encontrada uma regra de permissão para um pacote, o pacote é ignorado. |

Os NSGs contêm dois conjuntos de regras: de entrada e de saída. A prioridade para uma regra tem de ser exclusiva dentro de cada conjunto. 

![Processamento de regras do NSG](./media/virtual-network-nsg-overview/figure3.png) 

A figura anterior mostra como as regras do NSG são processadas.

### <a name="default-tags"></a>Etiquetas Predefinidas
Etiquetas predefinidas são identificadores fornecidos pelo sistema para abordar uma categoria de endereços IP. Pode utilizar etiquetas predefinidas nas propriedades **prefixo do endereço de origem** e **prefixo do endereço de destino** de qualquer regra. Existem três etiquetas predefinidas que pode utilizar:

* **Rede Virtual** (Resource Manager) (**VIRTUAL_NETWORK** para clássica): esta etiqueta inclui o espaço de endereços da rede virtual (intervalos CIDR definidos no Azure), todos os espaços de endereços no local ligados e as VNets do Azure ligadas (redes locais).
* **AzureLoadBalancer** (Resource Manager) (**AZURE_LOADBALANCER** par clássica): esta etiqueta denota o balanceador de carga da infraestrutura do Azure. A etiqueta traduz-se num IP de datacenter do Azure onde as sondas de estado de funcionamento do Azure têm origem.
* **Internet** (Resource Manager) (**INTERNET** para clássica): esta etiqueta denota o espaço de endereços IP que está fora da rede virtual e acessível pela Internet pública. O intervalo inclui o [espaço de IP público pertencente ao Azure](https://www.microsoft.com/download/details.aspx?id=41653).

### <a name="default-rules"></a>Regras predefinidas
Todos os NSGs contêm um conjunto de regras predefinidas. As regras predefinidas não podem ser eliminadas, mas como lhes é atribuída a prioridade mais baixa, podem ser substituídas pelas regras que criar. 

As regras predefinidas permitem e recusam o tráfego da seguinte forma:
- **Rede virtual:** o tráfego que tem origem e termina numa rede virtual é permitido nas direções de entrada e de saída.
- **Internet:** o tráfego de saída é permitido, mas o tráfego de entrada é bloqueado.
- **Balanceador de carga:** permitir que o balanceador de carga do Azure sonde o estado de funcionamento das VMs e das instâncias de função. Se não estiver a utilizar um conjunto com balanceamento de carga, pode substituir esta regra.

**Regras predefinidas de entrada**

| Nome | Prioridade | IP de origem | Porta de origem | ID de destino | Porta de destino | Protocolo | Access |
| --- | --- | --- | --- | --- | --- | --- | --- |
| AllowVNetInBound |65000 | VirtualNetwork | * | VirtualNetwork | * | * | Permitir |
| AllowAzureLoadBalancerInBound | 65001 | AzureLoadBalancer | * | * | * | * | Permitir |
| DenyAllInBound |65500 | * | * | * | * | * | Negar |

**Regras predefinidas de saída**

| Nome | Prioridade | IP de origem | Porta de origem | ID de destino | Porta de destino | Protocolo | Access |
| --- | --- | --- | --- | --- | --- | --- | --- |
| AllowVnetOutBound | 65000 | VirtualNetwork | * | VirtualNetwork | * | * | Permitir |
| AllowInternetOutBound | 65001 | * | * | Internet | * | * | Permitir |
| DenyAllOutBound | 65500 | * | * | * | * | * | Negar |

## <a name="associating-nsgs"></a>Associar NSGs
Pode associar um NSG a VMs, NICs e sub-redes, dependendo do modelo de implementação que estiver a utilizar, da seguinte forma:

* **VM (apenas clássica):** as regras de segurança são aplicadas a todo o tráfego para/da VM. 
* **NIC (apenas Resource Manager):** as regras de segurança são aplicadas a todo o tráfego para/da NIC à qual o NSG está associado. Numa VM com várias NICs, pode aplicar diferentes NSGs (ou os mesmos) a cada NIC individualmente. 
* **Sub-rede (Resource Manager e clássica):** as regras de segurança são aplicadas a qualquer tráfego para/de recursos ligados à VNet.

Pode associar NSGs diferentes a uma VM (ou NIC, consoante o modelo de implementação) e à sub-rede à qual uma NIC ou uma VM está ligada. As regras de segurança são aplicadas ao tráfego, por prioridade, em cada NSG, pela seguinte ordem:

- **Tráfego de entrada**

  1. **NSG aplicado à sub-rede:** se um NSG de sub-rede tiver uma regra correspondente para recusar tráfego, o pacote é largado.

  2. **NSG aplicado à NIC** (Resource Manager) ou à VM (clássica): se o NSG da VM/NIC tiver uma regra correspondente que recusa o tráfego, os pacotes são largados na VM\NIC, apesar de o NSG da sub-rede ter uma regra correspondente que permite o tráfego.

- **Tráfego de saída**

  1. **NSG aplicado à NIC** (Resource Manager) ou à VM (clássica): se o NSG da VM/NIC tiver uma regra correspondente que recusa o tráfego, os pacotes são largados.

  2. **NSG aplicado à sub-rede:** se o NSG de sub-rede tiver uma regra correspondente que recusa o tráfego, os pacotes são largados, apesar de o NSG da VM/NIC tiver uma regra correspondente que permite o tráfego.

> [!NOTE]
> Embora só possa associar um único NSG a uma sub-rede, VM ou NIC, pode associar o mesmo NSG à quantidade de recursos que pretender.
>

## <a name="implementation"></a>Implementação
Pode utilizar as ferramentas abaixo para implementar NSGs nos modelos de implementação Resource Manager ou clássica:

| Ferramenta de implementação | Clássica | Resource Manager |
| --- | --- | --- |
| Portal do Azure   | Sim | [Sim](virtual-networks-create-nsg-arm-pportal.md) |
| PowerShell     | [Sim](virtual-networks-create-nsg-classic-ps.md) | [Sim](virtual-networks-create-nsg-arm-ps.md) |
| CLI do Azure **V1**   | [Sim](virtual-networks-create-nsg-classic-cli.md) | [Sim](virtual-networks-create-nsg-cli-nodejs.md) |
| CLI do Azure **V2**   | Não | [Sim](virtual-networks-create-nsg-arm-cli.md) |
| Modelo Azure Resource Manager   | Não  | [Sim](virtual-networks-create-nsg-arm-template.md) |

## <a name="planning"></a>Planeamento
Antes de implementar os NSGs, tem de responder às perguntas seguintes:

1. Para ou a partir de que tipos de recursos pretende filtrar o tráfego? Pode ligar recursos, como NICs (Resource Manager), VMs (clássica), Serviços Cloud, Ambientes de Serviço de Aplicações e Conjuntos de Dimensionamento de VMs. 
2. Os recursos para e a partir dos quais pretende filtrar o tráfego estão ligados a sub-redes em VNets existentes?

Para obter mais informações sobre o planeamento da segurança de rede no Azure, leia o artigo [Cloud services and network security](../best-practices-network-security.md) (Os serviços cloud e a segurança de rede). 

## <a name="design-considerations"></a>Considerações de conceção
Quando souber as respostas às perguntas da secção [Planeamento](#Planning), reveja as secções seguintes antes de definir os seus NSGs:

### <a name="limits"></a>Limites
Existem limites ao número de NSGs que pode ter numa subscrição e ao número de regras por NSG. Para saber mais sobre os limites, leia o artigo [Azure limites](../azure-subscription-service-limits.md#networking-limits) (Limites do artigo).

### <a name="vnet-and-subnet-design"></a>Conceção de VNet e sub-rede
Uma vez que os NSGs podem ser aplicados a sub-redes, pode minimizar o número de NSGs agrupando os recursos por sub-rede e aplicando os NSGs a sub-redes.  Se optar por aplicar NSGs a sub-redes, pode achar que as VNets e sub-redes existentes que tem não foram definidas a pensar nos NSGs. Poderá ter de definir VNets e sub-redes novas para suportar o design do seu VNet e implementar os recursos novos nas suas sub-redes novas. Em seguida, pode definir uma estratégia de migração para mover os recursos existentes para as novas sub-redes. 

### <a name="special-rules"></a>Regras especiais
Se bloquear o tráfego permitido pelas regras seguintes, a sua infraestrutura não conseguirá comunicar com serviços do Azure essenciais:

* **IP Virtual do nó de anfitrião:** os serviços de infraestrutura básica, tais como DHCP, DNS e monitorização do estado de funcionamento, são fornecidos através do endereço IP do anfitrião virtualizado 168.63.129.16. Este endereço IP público pertence à Microsoft e é o único endereço IP virtualizado utilizado em todas as regiões para este fim. Este endereço IP é mapeado para o endereço IP físico do computador servidor (nó de anfitrião) que está a alojar a VM. O nó de anfitrião funciona como o reencaminhamento DHCP, o resolvedor recursivo de DNS e a origem de sonda para a sonda de estado de funcionamento do balanceador de carga e a sonda de estado de funcionamento da máquina. A comunicação para este endereço IP não é um ataque.
* **Licenciamento (Key Management Service):** as imagens do Windows em execução nas VMs têm de ser licenciadas. Para garantir o licenciamento, é enviado um pedido para os servidores de anfitrião do Key Management Service que processam estas consultas. O pedido é feito através da porta 1688 de saída.

### <a name="icmp-traffic"></a>Tráfego ICMP
As atuais regras do NSG admitem apenas protocolos *TCP* ou *UDP*. Não existe uma etiqueta específica para *ICMP*. No entanto, o tráfego ICMP é permitido numa VNet pela regra predefinida AllowVNetInBound, que permite o tráfego de e para as portas e protocolos dentro da VNet.

### <a name="subnets"></a>Sub-redes
* Considere o número de camadas de que sua carga de trabalho necessita. Cada camada pode ser isolada através da utilização de uma sub-rede, com um NSG aplicado à sub-rede. 
* Se precisar de implementar uma sub-rede para um gateway de VPN, ou circuito ExpressRoute, **NÃO** aplique um NSG a essa sub-rede. Se o fizer, a conectividade em várias VNets ou em vários locais poderá falhar. 
* Se tiver de implementar uma aplicação virtual de rede (NVA), ligue-a à sua própria sub-rede e crie rotas definidas pelo utilizador (UDR) de e para a NVA. Pode implementar um NSG de nível de sub-rede para filtrar o tráfego que entra e sai desta sub-rede. Para saber mais sobre as rotas definidas pelo utilizador, leia o artigo [Rotas definidas pelo utilizador](virtual-networks-udr-overview.md).

### <a name="load-balancers"></a>Balanceadores de carga
* Considere as regras de balanceamento de carga e de tradução de endereços de rede (NAT) para cada balanceador de carga utilizado por cada uma das suas cargas de trabalho. As regras NAT estão vinculadas a um conjunto de back-end que contém NICs (Resource Manager) ou a VMs/instâncias de função dos Serviços Cloud (clássica). Considere criar um NSG para cada conjunto de back-end, permitindo apenas tráfego mapeado através das regras implementadas nos balanceadores de carga. Criar um NSG para cada conjunto de back-end garante que o tráfego que vai diretamente para o mesmo (em vez de passar pelo balanceador de carga) também é filtrado.
* Em implementações clássicas, o utilizador cria pontos finais que mapeiam portas num balanceador de carga para portas nas VMs ou instâncias de função. Também pode criar o seu próprio balanceador de carga individual destinado ao público através do Resource Manager. A porta de destino para o tráfego de entrada é a porta real na VM ou na instância de função, não a porta exposta por um balanceador de carga. A porta e o endereço de origem para a ligação à VM correspondem a uma porta e a um endereço no computador remoto na Internet e não à porta e ao endereço expostos pelo balanceador de carga.
* Quando cria NSGs para filtrar o tráfego que vem de um balanceador de carga interno (ILB), a porta de origem e o intervalo de endereços aplicados são os do computador de origem, não do balanceador de carga. A porta de destino e o intervalo de endereços são os do computador de destino, não do balanceador de carga.

### <a name="other"></a>Outros
* As listas de controlo de acesso baseadas em ponto final e os NSGs não são suportados na mesma instância de VM. Se pretender utilizar um NSG e ter uma ACL de ponto final já implementada, remova primeiro a ACL de ponto final. Para obter informações sobre como remover uma ACL de ponto final, veja o artigo [Manage endpoint ACLs](virtual-networks-acl-powershell.md) (Gerir ACLs de ponto final).
* No Resource Manager, pode utilizar um NSG associado a uma NIC para VMs com várias NICs para ativar a gestão (acesso remoto) por NIC. Associar NSGs exclusivos a cada NIC permite a separação de tipos de tráfego entre NICs.
* Tal como acontece com a utilização de balanceadores de carga, ao filtrar o tráfego de outras VNets, tem de utilizar o intervalo de endereços de origem do computador remoto e não o gateway que liga as VNets.
* Muitos serviços do Azure não podem ser ligados a VNets. Se um recurso do Azure não estiver ligado a uma VNet, não pode utilizar um NSG para filtrar tráfego para esse recurso.  Leia a documentação relativa aos serviços que utilizar para determinar se podem ou não ser ligados a VNets.

## <a name="sample-deployment"></a>Implementação de exemplo
Para ilustrar a aplicação das informações neste artigo, considere um cenário típico de uma aplicação de duas camadas, mostrado na imagem seguinte:

![NSGs](./media/virtual-network-nsg-overview/figure1.png)

Tal como ilustrado no diagrama, as VMs *Web1* e *Web2* estão ligadas à sub-rede *FrontEnd* e as VMs *DB1* e *DB2* estão ligadas à sub-rede *BackEnd*.  Ambas as sub-redes fazem parte da VNet *TestVNet*. Cada componente da aplicação é executado numa VM do Azure ligada a uma VNet. O cenário tem os requisitos seguintes:

1. Separação de tráfego entre os servidores WEB e DB.
2. As regras de balanceamento de carga reencaminham o tráfego do balanceador de cargo para todos os servidores Web na porta 80.
3. As regras NAT do balanceador de carga reencaminham o tráfego para o balanceador de carga na porta 50001 para a porta 3389 na VM WEB1.
4. Não existe acesso às VMs de front-end ou back-end a partir da Internet, com exceção dos requisitos 2 e 3.
5. Sem acesso de saída à Internet a partir dos servidores WEB ou DB.
6. É permitido o acesso a partir da sub-rede FrontEnd para a porta 3389 de qualquer servidor Web.
7. É permitido o acesso a partir da sub-rede FrontEnd para a porta 3389 de qualquer servidor DB.
8. É permitido o acesso a partir da sub-rede FrontEnd para a porta 1433 de todos os servidores DB.
9. Separação do tráfego de gestão (porta 3389) e do tráfego de base de dados (1433) em diferentes NICs nos servidores DB.

Os requisitos 1 a 6 (exceto os 3 e 4) estão confinados a espaços de sub-rede. Os NSGs seguintes cumprem os requisitos anteriores, minimizando, ao mesmo tempo, o número de NSGs necessários:

### <a name="frontend"></a>FrontEnd
**Regras de entrada**

| Regra | Access | Prioridade | Intervalo de endereços de origem | Porta de origem | Intervalo de endereços de destino | Porta de destino | Protocolo |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Allow-Inbound-HTTP-Internet | Permitir | 100 | Internet | * | * | 80 | TCP |
| Allow-Inbound-RDP-Internet | Permitir | 200 | Internet | * | * | 3389 | TCP |
| Deny-Inbound-All | Negar | 300 | Internet | * | * | * | TCP |

**Regras de saída**

| Regra | Access | Prioridade | Intervalo de endereços de origem | Porta de origem | Intervalo de endereços de destino | Porta de destino | Protocolo |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Deny-Internet-All |Negar |100 | * | * | Internet | * | * |

### <a name="backend"></a>BackEnd
**Regras de entrada**

| Regra | Access | Prioridade | Intervalo de endereços de origem | Porta de origem | Intervalo de endereços de destino | Porta de destino | Protocolo |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Deny-Internet-All | Negar | 100 | Internet | * | * | * | * |

**Regras de saída**

| Regra | Access | Prioridade | Intervalo de endereços de origem | Porta de origem | Intervalo de endereços de destino | Porta de destino | Protocolo |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Deny-Internet-All | Negar | 100 | * | * | Internet | * | * |

São criados os NSGs seguintes e associados a NICS nas VMS abaixo:

### <a name="web1"></a>WEB1
**Regras de entrada**

| Regra | Access | Prioridade | Intervalo de endereços de origem | Porta de origem | Intervalo de endereços de destino | Porta de destino | Protocolo |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Allow-Inbound-RDP-Internet | Permitir | 100 | Internet | * | * | 3389 | TCP |
| Allow-Inbound-HTTP-Internet | Permitir | 200 | Internet | * | * | 80 | TCP |

> [!NOTE]
> O intervalo de endereços de origem para as regras anteriores é **Internet**, não o endereço IP virtual do Balanceador de carga. A porta de origem é *, não 500001. As regras NAT dos balanceadores de carga não são as mesmas que as regras de segurança dos NSGs. As regras de segurança dos NSGs estão sempre relacionadas com a origem original e o destino final do tráfego, **não** com o balanceador de carga entre os dois. 
> 
> 

### <a name="web2"></a>WEB2
**Regras de entrada**

| Regra | Access | Prioridade | Intervalo de endereços de origem | Porta de origem | Intervalo de endereços de destino | Porta de destino | Protocolo |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Deny-Inbound-RDP-Internet | Negar | 100 | Internet | * | * | 3389 | TCP |
| Allow-Inbound-HTTP-Internet | Permitir | 200 | Internet | * | * | 80 | TCP |

### <a name="db-servers-management-nic"></a>Servidores DB (NIC de Gestão)
**Regras de entrada**

| Regra | Access | Prioridade | Intervalo de endereços de origem | Porta de origem | Intervalo de endereços de destino | Porta de destino | Protocolo |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Allow-Inbound-RDP-Front-end | Permitir | 100 | 192.168.1.0/24 | * | * | 3389 | TCP |

### <a name="db-servers-database-traffic-nic"></a>Servidores DB (NIC de tráfego de base de dados)
**Regras de entrada**

| Regra | Access | Prioridade | Intervalo de endereços de origem | Porta de origem | Intervalo de endereços de destino | Porta de destino | Protocolo |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Allow-Inbound-SQL-Front-end | Permitir | 100 | 192.168.1.0/24 | * | * | 1433 | TCP |

Uma vez que alguns dos NSGs estão associados a NICs individuais, as regras destinam-se a recursos implementados através do Resource Manager. As regras são combinadas para a sub-rede e a NIC, dependendo de como estão associadas. 

## <a name="next-steps"></a>Passos seguintes
* [Deploy NSGs (Resource Manager)](virtual-networks-create-nsg-arm-pportal.md) (Implementar NSGs [Resource Manager]).
* [Deploy NSGs (classic)](virtual-networks-create-nsg-classic-ps.md) (Implementar NSGs [clássica]).
* [Manage NSG logs](virtual-network-nsg-manage-log.md) (Gerir registos de NSGs).
* [Troubleshoot NSGs] (virtual-network-nsg-troubleshoot-portal.md) (Resolver problemas de NSGs)
