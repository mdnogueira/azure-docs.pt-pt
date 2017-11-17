---
title: "Descrição geral da segurança de rede do Azure | Microsoft Docs"
description: "Saiba mais sobre as opções de segurança para controlar o fluxo do tráfego de rede entre recursos do Azure."
services: virtual-network
documentationcenter: na
author: jimdial
manager: jeconnoc
editor: 
ms.assetid: 
ms.service: virtual-network
ms.devlang: NA
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/19/2017
ms.author: jdial
ms.openlocfilehash: d4a216b612274ff1de499bd4892ff7422c66b4d0
ms.sourcegitcommit: afc78e4fdef08e4ef75e3456fdfe3709d3c3680b
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/16/2017
---
# <a name="network-security"></a>Segurança da rede

Pode utilizar um grupo de segurança de rede para limitar o tráfego de rede para os recursos numa rede virtual. Os grupos de segurança de rede contêm uma lista das regras de segurança que permitem ou recusam o tráfego de rede de entrada ou saída com base no endereço IP de destino, na porta e no protocolo. 

## <a name="network-security-groups"></a>Grupos de segurança de rede

Cada interface de rede tem zero ou um grupo de segurança de rede associado. Cada interface da rede existe numa sub-rede da [rede virtual](virtual-networks-overview.md). As sub-redes também podem ter zero ou um grupo de segurança de rede associado. 

Quando aplicadas a uma sub-rede, as regras de segurança são aplicadas a todos os recursos na mesma. Para além das interfaces de rede, poderá ter instâncias de outros serviços do Azure, como o HDInsight, os Conjuntos de Dimensionamento de Máquinas Virtuais e Ambientes do Serviço de Aplicações, implementados na sub-rede.

Quando um grupo de segurança de rede está associado à interface de rede e à sub-rede em que esta se encontra, os grupos de segurança são aplicados da seguinte forma.

- **Tráfego de entrada**: O grupo de segurança de rede associado à sub-rede na qual a interface de rede se encontra é avaliado primeiro. Qualquer tráfego permitido através do grupo de segurança de rede associado à sub-rede é, então, avaliado pelo grupo de segurança de rede associado à interface de rede. Por exemplo, poderá precisar de acesso de entrada a uma máquina virtual pela porta 80 na Internet. Se associar um grupo de segurança de rede à interface de rede e à sub-rede na qual a interface se encontra, esse grupo tem de permitir a porta 80. Se tiver permitido apenas esta porta através do grupo associado à sub-rede ou à interface de rede na qual a sub-rede está, a comunicação falha devido às regras de segurança predefinidas. Veja as [regras de segurança predefinidas](#default-security-rules) para obter mais detalhes. Se só tiver aplicado o grupo de segurança de rede à sub-rede ou à interface de rede e esse grupo contiver uma regra que permite o tráfego de entrada na porta 80, por exemplo, a comunicação é feita. 
- **Tráfego de saída**: o grupo de segurança de rede associado à interface de rede é avaliado primeiro. Qualquer tráfego permitido através do grupo de segurança de rede associado à interface de rede é, então, avaliado pelo grupo de segurança de rede associado à sub-rede.

É possível que não esteja sempre ciente de que os grupos de segurança de rede estão aplicados, quer à interface de rede, quer à sub-rede. Pode ver as [regras de segurança em vigor](virtual-network-manage-nsg-arm-portal.md) numa interface de rede para ver facilmente as regras agregadas que estão aplicadas à mesma. Também pode utilizar a capacidade [Verificação de fluxo de IP](../network-watcher/network-watcher-check-ip-flow-verify-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) do Observador de Rede do Azure para saber se a comunicação é permitida de ou para uma interface de rede. A ferramenta indica se é permitida a comunicação e que regra de segurança de rede permite ou nega o tráfego.
 
> [!NOTE]
> Os grupos de segurança de rede são associados a sub-redes ou a máquinas virtuais e a serviços cloud implementados com o modelo de implementação clássica e não a interfaces de rede no modelo de implementação do Resource Manager. Para saber mais sobre os modelos de implementação do Azure, veja [Understand Azure deployment models](../azure-resource-manager/resource-manager-deployment-model.md?toc=%2fazure%2fvirtual-network%2ftoc.json) (Compreender os modelos de implementação do Azure).

O mesmo grupo de segurança de rede pode ser aplicado a tantas interfaces de rede individuais e sub-redes que escolher.

## <a name="security-rules"></a>Regras de segurança

Os grupos de segurança de rede contêm zero ou tantas regras conforme pretender, dentro dos [limites](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits) das subscrições do Azure. Cada regra especifica as propriedades seguintes:

|Propriedade  |Explicação  |
|---------|---------|
|Nome|Um nome exclusivo no grupo de segurança de rede.|
|Prioridade | Um número entre 100 e 4096. As regras são processadas por ordem de prioridade, sendo os números mais baixos processados antes dos mais elevados, uma vez que têm uma prioridade superior. Quando o tráfego corresponder a uma regra, o processamento para. Como resultado, qualquer regra que exista com prioridades inferiores (números mais elevados) e que tenham os mesmos atributos das regras com prioridades superiores não é processada.|
|Origem ou destino| Qualquer ou um endereço IP individual, um bloco CIDR (por exemplo, 10.0.0.0/24), etiqueta de serviço ou grupo de segurança de aplicações. Saiba mais sobre as [etiquetas de serviço](#service-tags) e os [grupos de segurança de aplicações](#application-security-groups). Especificar um intervalo, uma etiqueta de serviço ou um grupo de segurança de aplicações permite-lhe criar menos regras de segurança. A capacidade de especificar vários endereços IP individuais e intervalos de endereços IP (não é possível indicar etiquetas de serviço ou grupos de aplicações) numa regra está na versão de pré-visualização e é denominada de regras de segurança aumentadas. As regras de segurança aumentadas só podem ser criadas em grupos de segurança de rede gerados através do modelo de implementação do Resource Manager. Não pode especificar vários endereços IP nem intervalos de endereços IP em grupos de segurança de rede criados com o modelo de implementação clássica.|
|Protocolo     | TCP, UDP ou Qualquer, o que inclui TCP, UDP e ICMP. Não pode especificar ICMP individualmente, pelo que, se precisar deste, tem de utilizar Qualquer. |
|Direção| Indica se a regra se aplica a tráfego de entrada ou de saída.|
|Intervalo de portas     |Pode especificar uma porta individual ou um intervalo de portas. Por exemplo, pode indicar 80 ou 10000-10005. Especificar intervalos permite-lhe criar menos regras de segurança. A capacidade de especificar várias portas individuais e intervalos de portas numa regra na versão de pré-visualização e é denominada de regras de segurança aumentadas. Antes de utilizar as regras de segurança aumentadas, leia [Preview features](#preview-features) (Funcionalidades em pré-visualização) para ver informações importantes. As regras de segurança aumentadas só podem ser criadas em grupos de segurança de rede gerados através do modelo de implementação do Resource Manager. Não pode especificar várias portas nem intervalos de portas na mesma regra de segurança em grupos de segurança de rede criados com o modelo de implementação clássica.   |
|Ação     | Permitir ou negar        |

As regras de segurança têm monitorização de estado. Se especificar uma regra de segurança de saída para qualquer endereço através da porta 80, por exemplo, não é necessário especificar uma regra de segurança de entrada para a resposta ao tráfego de saída. Só tem de especificar uma regra de segurança de entrada se a comunicação for iniciada externamente. O oposto também se aplica. Se o tráfego de entrada for permitido numa porta, não é necessário especificar uma regra de segurança de saída para responder ao tráfego através da porta. Para saber mais sobre os limites à criação de regras de segurança, veja [Azure limits](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits) (Limites do Azure).

## <a name="augmented-security-rules"></a>Regras de segurança aumentadas

As regras aumentadas simplificam a definição de segurança das redes virtuais, permitindo-lhe definir políticas de segurança de rede maiores e mais complexas com menos regras. Pode combinar várias portas, vários endereços IP explícitos, etiquetas de serviço e grupos de segurança de aplicações numa única regra de segurança e facilmente compreendida. Utilize as regras aumentadas nos campos de origem, destino e porta das regras. Ao criar uma regra, pode especificar múltiplos endereços IP explícitos, intervalos CIDR e portas. Para simplificar a manutenção da definição de segurança de rede, combine regras de segurança aumentadas com etiquetas de serviço ou grupos de segurança de aplicações. 

As regras de segurança aumentadas estão disponíveis na versão de pré-visualização. Para saber mais sobre os limites à criação de regras de segurança aumentadas, veja [Azure limits](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits) (Limites do Azure). As funcionalidades em pré-visualização não têm o mesmo nível de disponibilidade e fiabilidade do que as funcionalidades em versão geral. As funcionalidades estão disponíveis apenas nas seguintes regiões: E.U.A. Leste, E.U.A. Oeste, E.U.A. Oeste 2, E.U.A. Centro-Oeste, Leste da Austrália, Sudeste da Austrália e Sul do Reino Unido.

## <a name="default-security-rules"></a>Regras de segurança predefinidas

Se não for associado um grupo de segurança de rede a uma sub-rede ou a uma interface de rede, é permitida a entrada ou a saída de todo o tráfego a partir da sub-rede ou da interface. Assim que um grupo é criado, o Azure cria as regras predefinidas seguintes no mesmo:

### <a name="inbound"></a>Entrada

#### <a name="allowvnetinbound"></a>AllowVNetInBound

|Prioridade|Origem|Portas de origem|Destino|Portas de destino|Protocolo|Access|
|---|---|---|---|---|---|---|
|65000|VirtualNetwork|0-65535|VirtualNetwork|0-65535|Todos|Permitir|

#### <a name="allowazureloadbalancerinbound"></a>AllowAzureLoadBalancerInBound

|Prioridade|Origem|Portas de origem|Destino|Portas de destino|Protocolo|Access|
|---|---|---|---|---|---|---|
|65001|AzureLoadBalancer|0-65535|0.0.0.0/0|0-65535|Todos|Permitir|

#### <a name="denyallinbound"></a>DenyAllInbound

|Prioridade|Origem|Portas de origem|Destino|Portas de destino|Protocolo|Access|
|---|---|---|---|---|---|---|
|65500|0.0.0.0/0|0-65535|0.0.0.0/0|0-65535|Todos|Negar|

### <a name="outbound"></a>Saída

#### <a name="allowvnetoutbound"></a>AllowVnetOutBound

|Prioridade|Origem|Portas de origem| Destino | Portas de destino | Protocolo | Access |
|---|---|---|---|---|---|---|
| 65000 | VirtualNetwork | 0-65535 | VirtualNetwork | 0-65535 | Todos | Permitir |

#### <a name="allowinternetoutbound"></a>AllowInternetOutBound

|Prioridade|Origem|Portas de origem| Destino | Portas de destino | Protocolo | Access |
|---|---|---|---|---|---|---|
| 65001 | 0.0.0.0/0 | 0-65535 | Internet | 0-65535 | Todos | Permitir |

#### <a name="denyalloutbound"></a>DenyAllOutBound

|Prioridade|Origem|Portas de origem| Destino | Portas de destino | Protocolo | Access |
|---|---|---|---|---|---|---|
| 65500 | 0.0.0.0/0 | 0-65535 | 0.0.0.0/0 | 0-65535 | Todos | Negar |

Nas colunas **Source** (Origem) e **Destination** (Destino), *VirtualNetwork*, *AzureLoadBalancer* e *Internet* são [etiquetas de serviço](#tags) e não endereços IP. Na coluna Protocol (Protocolo), **All** (Todos) abrange TCP, UDP e ICMP. Ao criar uma regra, pode especificar TCP, UDP ou Todos, mas não pode especificar ICMP individualmente. Por conseguinte, se a sua regra exigir ICMP, tem de selecionar *Todos* para o protocolo. *0.0.0.0/0* nas colunas **Source** e **Destination** representa todos os endereços.
 
Não pode remover as regras predefinidas, mas pode criar regras com prioridades superiores para substituí-las.

## <a name="service-tags"></a>Etiquetas de serviço

 As etiquetas de serviço representam um grupo de prefixos de endereços IP, que ajudam a minimizar a complexidade da criação de regras de segurança. Não pode criar as suas próprias etiquetas de serviço nem especificar que endereços IP estão incluídos nas mesmas. A Microsoft gere os prefixos de endereços que as etiquetas abrangem e atualiza-as automaticamente à medida que os endereços são alterados. Ao criar regras de segurança, pode utilizar etiquetas de serviço em vez de endereços IP específicos. Estão disponíveis as etiquetas de serviço seguintes para utilização em definições de regras de segurança. Os nomes variam ligeiramente entre os [modelos de implementação do Azure](../azure-resource-manager/resource-manager-deployment-model.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

* **VirtualNetwork** (*Resource Manager) (**VIRTUAL_NETWORK** para a clássica): esta etiqueta inclui o espaço de endereços das redes virtuais (todos os intervalos de CIDR definidos para as redes virtuais), todos os espaços de endereços no local ligados e as redes em [modo de peering](virtual-network-peering-overview.md) ou as redes virtuais ligadas a um [gateway de rede virtual](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
* **AzureLoadBalancer** (Resource Manager) (**AZURE_LOADBALANCER** par clássica): esta etiqueta denota o balanceador de carga da infraestrutura do Azure. A etiqueta traduz-se num [endereço IP de um datacenter do Azure](https://www.microsoft.com/download/details.aspx?id=41653), onde as sondas de estado de funcionamento do Azure têm origem. Se não estiver a utilizar o balanceador de carga do Azure, pode substituir esta regra.
* **Internet** (Gestor de Recursos) (**INTERNET** para clássica): esta etiqueta denota o espaço de endereços IP que está fora da rede virtual e acessível pela Internet pública. O intervalo de endereços inclui o [espaço de endereço IP público pertencente ao Azure](https://www.microsoft.com/download/details.aspx?id=41653).
* **AzureTrafficManager** (apenas Resource Manager): esta etiqueta denota o espaço de endereços IP do serviço Gestor de Tráfego do Azure.
* **Armazenamento** (apenas Resource Manager): esta etiqueta denota o espaço de endereços IP do serviço Armazenamento do Azure. Se especificar *Storage* no valor, o tráfego é permitido ou negado para o armazenamento. Se quiser apenas permitir o acesso ao armazenamento numa determinada [região](https://azure.microsoft.com/regions), pode especificá-la. Por exemplo, se pretender permitir o acesso ao Armazenamento do Azure apenas na região E.U.A. Leste, pode especificar *Storage.EastUS* como a etiqueta de serviço. Etiquetas de serviço regionais adicionais disponíveis: Storage.AustraliaEast, Storage.AustraliaSoutheast, Storage.EastUS, Storage.UKSouth, Storage.WestCentralUS, Storage.WestUS e Storage.WestUS2. A etiqueta representa o serviço, mas não instâncias específicas do mesmos. Por exemplo, representa o serviço Armazenamento do Azure, mas não uma conta do Armazenamento do Azure específica.
* **Sql** (apenas Resource Manager): esta etiqueta denota os prefixos de endereços dos serviços Base de Dados SQL do Azure e Azure SQL Data Warehouse. Só pode indicar regiões específicas nesta etiqueta de serviço. Por exemplo, se pretender permitir o acesso à Base de Dados SQL do Azure apenas na região E.U.A. Leste, pode especificar *Sql.EastUS* como a etiqueta de serviço. Não pode especificar Sql apenas para todas as regiões do Azure; tem de especificar todas as regiões individualmente. Outras etiquetas do serviço regional disponíveis: Sql.AustraliaEast, Sql.AustraliaSoutheast, Sql.EastUS, Sql.UKSouth, Sql.WestCentralUS, Sql.WestUS e Sql.WestUS2. A etiqueta representa o serviço, mas não instâncias específicas do mesmos. Por exemplo, representa o serviço Base de Dados SQL do Azure, mas não uma conta de Base de Dados SQL do Azure específica.

> [!WARNING]
> As etiquetas de serviço AzureTrafficManager, Storage e Sql estão disponíveis na versão de pré-visualização. As funcionalidades em pré-visualização não têm o mesmo nível de disponibilidade e fiabilidade do que as funcionalidades em versão geral. As etiquetas de serviço estão disponíveis apenas nas seguintes regiões: E.U.A. Leste, E.U.A. Oeste, E.U.A. Oeste 2, E.U.A. Centro-Oeste, Leste da Austrália, Sudeste da Austrália e Sul do Reino Unido.

> [!NOTE]
> Se implementar um ponto final de serviço de rede virtual relativo a um serviço, como o Armazenamento do Azure ou a Base de Dados SQL do Azure, o Azure adiciona uma rota para uma sub-rede da rede virtual do serviço. Os prefixos de endereço da rota são os mesmos prefixos de endereço, ou intervalos CIDR, da etiqueta de serviço correspondente.

## <a name="application-security-groups"></a>Grupos de segurança de aplicações

Os grupos de segurança de aplicações permitem-lhe configurar a segurança de rede como uma extensão natural da estrutura de uma aplicação, possibilitando o agrupamento de máquinas virtuais e a definição de políticas de segurança de rede com base nesses grupos. Esta funcionalidade permite-lhe reutilizar a política de segurança em escala sem manutenção manual de endereços IP explícitos. A plataforma lida com a complexidade dos endereços IP explícitos e dos múltiplos conjuntos de regras, permitindo-lhe focar-se na lógica de negócio.

Pode especificar um grupo de segurança de aplicações como origem e destino numa regra de segurança. Quando a política de segurança estiver definida, pode criar máquinas virtuais e atribuir as interfaces de rede na máquina virtual a um grupo de segurança de aplicações. A política é aplicada com base na associação do grupo de segurança de aplicações de cada interface de rede numa máquina virtual. O exemplo seguinte ilustra a forma como pode utilizar um grupo de segurança de aplicações para todos os servidores Web na sua subscrição:

1. Crie um grupo de segurança de aplicações com o nome *WebServers*.
2. Crie um grupo de segrança de rede com o nome *MyNSG*.
3. Crie uma regra de segurança de entrada no grupo de segurança de rede, especificando a etiqueta de serviço *Internet* como o endereço de origem e o grupo de segurança de aplicações *WebServers* como o endereço de destino, e permita as portas 80 e 443.
4. Implemente uma máquina virtual que esteja a executar uma aplicação de servidor Web. Especifique que a interface de rede na máquina virtual é membro do grupo de segurança de aplicações *WebServers*. As portas 80 e 443 são, depois, permitidas para a máquina virtual. Também são permitidas para quaisquer servidores Web subsequentes que crie e torne membros do grupo de segurança de aplicações *WebServers*. 

Se criar outras regras, especificando outros grupos de segurança de aplicações como o destino, essas regras não são aplicadas aos servidores Web, no exemplo anterior. As regras que especifiquem grupos de segurança de aplicações só são aplicadas às interfaces de rede que são membros desses grupos. Estes grupos, combinados com as regras de segurança aumentadas e as etiquetas de serviço, permitem criar um número mínimo de grupos de segurança de rede para gerir a segurança da rede na sua subscrição.
 
Para saber mais sobre os limites durante a criação de grupos de segurança de aplicações e como especificá-los em regras de segurança, veja [Azure limits](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits) (Limites do Azure).

Os grupos de segurança de aplicações estão disponíveis na versão de pré-visualização. Antes de utilizar os grupos de segurança de aplicação, tem de se registar para utilizá-los ao concluir os passos 1 a 5 em [Create a network security group with application security groups](create-network-security-group-preview.md#powershell) (Criar um grupo de segurança de rede com grupos de segurança de aplicações) e leia [Preview features](#preview-features) (Funcionalidades de pré-visualização) para obter informações importantes. Os grupos de segurança de aplicação têm as seguintes restrições:

-   Todas as interfaces de rede dentro de um grupo de segurança de aplicação têm de existir na mesma rede virtual. Não é possível adicionar interfaces de rede de redes virtuais diferentes ao mesmo grupo de segurança de aplicação. A rede virtual em que se encontra a primeira interface de rede atribuída ao grupo de segurança da aplicação define a rede virtual em que todas as interfaces de rede posteriormente atribuídas têm de existir.
- Se especificar grupos de segurança de aplicação como a origem e o destino numa regra de segurança, as interfaces de rede em ambos os grupos de segurança de aplicação têm de existir na mesma rede virtual. Por exemplo, se ASG1 contivesse interfaces de rede da VNet1 e ASG2 contivesse interfaces de rede da VNet2, não poderia atribuir ASG1 como a origem e ASG2 como o destino de uma regra, pois todas as interfaces de rede têm de existir na VNet1. 

As funcionalidades em pré-visualização não têm o mesmo nível de disponibilidade e fiabilidade do que as funcionalidades em versão geral. Antes de utilizar os grupos de segurança de aplicações, tem, primeiro, de se registar para utilizá-los. As funcionalidades estão disponíveis apenas nas regiões seguintes: E.U.A. Centro-Oeste.


## <a name="azure-platform-considerations"></a>Considerações sobre a plataforma do Azure

- **IP Virtual do nó de anfitrião:** os serviços de infraestrutura básica, tais como DHCP, DNS e monitorização do estado de funcionamento, são fornecidos através dos endereços IP do anfitrião virtualizado 168.63.129.16 e 169.254.169.254. Estes endereços IP públicos pertencem à Microsoft e são os únicos endereços IP virtualizados utilizados em todas as regiões para este fim. O mapa de endereços para o endereço IP físico do computador servidor (nó de anfitrião) que está a alojar a máquina virtual. O nó de anfitrião funciona como o reencaminhamento DHCP, o resolvedor recursivo de DNS e a origem de sonda para a sonda de estado de funcionamento do balanceador de carga e a sonda de estado de funcionamento da máquina. A comunicação para estes endereços IP não é um ataque. Se bloquear o tráfego de ou para estes endereços IP, uma máquina virtual poderá não funcionar corretamente.
- **Licenciamento (Serviço de Gestão de Chaves):** as imagens do Windows em execução nas máquinas virtuais têm de ser licenciadas. Para garantir o licenciamento, é enviado um pedido para os servidores de anfitrião do Key Management Service que processam estas consultas. O pedido é feito através da porta 1688 de saída.
- **Máquinas virtuais em conjuntos com balanceamento de carga**: a porta de destino e o intervalo de endereços aplicados são provenientes do computador de origem, não do balanceador de carga. A porta de destino e o intervalo de endereços destinam-se ao computador de destino, não ao balanceador de carga.
- **Instâncias de serviço do Azure**: as instâncias de vários serviços do Azure, como o HDInsight, os Ambientes de Serviço de Aplicações e os Conjuntos de Dimensionamento de Máquinas Virtuais, são implementados em sub-redes da rede virtual. Para obter uma lista completa dos serviços que pode implementar em redes virtuais, veja [Rede virtual para os serviços do Azure](virtual-network-for-azure-services.md#services-that-can-be-deployed-into-a-virtual-network). Certifique-se de que se familiariza com os requisitos de portas de cada serviço antes de aplicar um grupo de segurança de rede à sub-rede na qual o recurso está implementado. Se negar portas de que os serviços precisam, estes não funcionarão corretamente.
- **Enviar e-mails de saída**: a Microsoft recomenda que utilize serviços de reencaminhamento de SMTP autenticados (normalmente ligados através da porta TCP 587, mas, muitas vezes, também através de outras portas) para enviar e-mails a partir de Máquinas Virtuais do Azure. Os serviços de reencaminhamento de SMTP especializam-se na reputação do remetente, para minimizar a possibilidade de os fornecedores de e-mail de terceiros rejeitarem mensagens. Esses serviços de reencaminhamento de SMTP incluem, sem estarem limitados, o Exchange Online Protection e o SendGrid. A utilização dos serviços de reencaminhamento de SMTP não está de forma alguma restringida no Azure, independentemente do seu tipo de subscrição. 

  Se tiver criado a sua subscrição do Azure antes de 15 de novembro de 2017, para além de poder utilizar os serviços de reencaminhamento de SMTP, também pode enviar e-mails diretamente através da porta TCP 25. Se tiver criado a sua subscrição após 15 de novembro de 2017, poderá não conseguir enviar e-mails diretamente através da porta 25. O comportamento da comunicação de saída através da porta 25 depende do seu tipo de subscrição, da seguinte forma:

     - **Contrato Enterprise**: a comunicação de saída através da porta 25 é permitida. Pode enviar e-mails de saída diretamente a partir de máquinas virtuais para fornecedores de e-mail externos, sem restrições impostas pela plataforma do Azure. 
     - **Pay as you go:** a comunicação de saída através da porta 25 está bloqueada a partir de todos os recursos. Se tiver de enviar e-mails a partir de uma máquina virtual diretamente para fornecedores de e-mail externos (sem utilizar um reencaminhamento de SMTP autenticado), pode fazer um pedido para remover a restrição. Os pedidos são analisados e aprovados à discrição da Microsoft, apenas sendo concedidos após as verificações contra fraudes. Para fazer um pedido, abra um processo de suporte com o tipo de problema *Técnico*, *Conectividade da Rede Virtual*, *Não é possível enviar e-mails (SMTP/Porta 25)*. No processo de suporte, inclua detalhes sobre o motivo pelo qual a subscrição tem de enviar e-mails diretamente para fornecedores de e-mail, em vez de utilizar um reencaminhamento de SMTP autenticado. Se a sua subscrição for isenta, apenas as máquinas virtuais criadas após a data de isenção conseguem realizar comunicações de saída através da porta 25.
     - **MSDN, Azure Pass, Azure no Open, Education, BizSpark e Avaliação gratuita**: a comunicação de saída através da porta 25 está bloqueada a partir de todos os recursos. Não pode fazer pedidos para remover a restrição, porque os pedidos não são concedidos. Se tiver obrigatoriamente de enviar e-mails a partir da sua máquina virtual, tem de utilizar um serviço de reencaminhamento de SMTP.

  Se o Azure lhe permitir enviar e-mails através da porta 25, a Microsoft não pode garantir que os fornecedores de e-mail aceitarão e-mails enviados a partir da sua máquina virtual. Se um fornecedor específico rejeitar e-mails da sua máquina virtual, tem de trabalhar diretamente com o fornecedor para resolver quaisquer problemas relacionados com a entrega de mensagens ou com a filtragem de spam, ou tem de utilizar um serviço de reencaminhamento de SMTP autenticado. 


## <a name="next-steps"></a>Passos seguintes

* Concluir o tutorial [Create a network security group](virtual-networks-create-nsg-arm-pportal.md) (Criar grupos de segurança de rede)
* Concluir o tutorial [Create a network security group with application security groups](create-network-security-group-preview.md) (Criar grupos de segurança de rede com grupos de segurança de aplicações)
