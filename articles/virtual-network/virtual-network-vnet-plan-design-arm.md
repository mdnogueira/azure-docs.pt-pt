---
title: Guia de Design e de plano de rede Virtual do Azure (VNet) | Microsoft Docs
description: "Saiba como planear e estruturar as redes virtuais no Azure com base nos seus requisitos de isolamento, conectividade e localização."
services: virtual-network
documentationcenter: na
author: jimdial
manager: carmonm
editor: tysonn
ms.assetid: 3a4a9aea-7608-4d2e-bb3c-40de2e537200
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/08/2016
ms.author: jdial
ms.openlocfilehash: 9a0126235c9ff3fec05d7709bdee95ab4832a33b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="plan-and-design-azure-virtual-networks"></a>Planear e estruturar as redes virtuais do Azure
Criar uma VNet para experimentar fácil suficiente, mas possibilidades são, irá implementar várias VNets ao longo do tempo para suportar as necessidades de produção da sua organização. Com algum planeamento e conceção, poderá implementar as VNets e ligar os recursos que necessita de forma mais eficaz. Se não estiver familiarizado com as VNets, é recomendado que lhe [Saiba mais sobre as VNets](virtual-networks-overview.md) e [como implementar](virtual-networks-create-vnet-arm-pportal.md) um antes de continuar.

## <a name="plan"></a>Planear
Uma compreensão profunda sobre as subscrições do Azure, regiões e recursos de rede é essencial para êxito. Pode utilizar a lista de considerações abaixo como um ponto de partida. Assim que compreender as considerações, pode definir os requisitos para o design de rede.

### <a name="considerations"></a>Considerações
Antes de atender o planeamento questões abaixo, considere o seguinte:

* Tudo o que cria no Azure é composto por um ou mais recursos. Uma máquina virtual (VM) é um recurso, a interface da placa de rede (NIC) utilizada por uma VM é um recurso, o endereço IP público utilizado por um NIC é um recurso, a VNet a NIC está ligada a é um recurso.
* Criar recursos dentro de um [região do Azure](https://azure.microsoft.com/regions/#services) e subscrição. Recursos só podem ser ligados a uma rede virtual que existe na mesma região e subscrição o recurso.
* Pode ligar redes virtuais entre si utilizando:
    * **[Peering de rede virtual](virtual-network-peering-overview.md)**: as redes virtuais têm de existir na mesma região do Azure. Largura de banda entre os recursos na redes virtuais em modo de peering é o mesmo como se os recursos foram ligados à mesma rede virtual.
    * **Um Azure [Gateway de VPN](../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md)**: as redes virtuais podem existir em regiões do Azure idêntica ou diferentes. Largura de banda entre os recursos na redes virtuais ligadas através de um Gateway de VPN é limitada pela largura de banda do Gateway de VPN.
* Pode ligar VNets à sua rede no local utilizando um do [opções de conectividade](../vpn-gateway/vpn-gateway-about-vpngateways.md#s2smulti) disponíveis no Azure.
* Recursos diferentes podem ser agrupados num [grupos de recursos](../azure-resource-manager/resource-group-overview.md#resource-groups), tornando mais fácil de gerir o recurso como uma unidade. Um grupo de recursos pode conter recursos a partir de várias regiões, desde que os recursos de pertencer à mesma subscrição.

### <a name="define-requirements"></a>Definir os requisitos
Utilize as perguntas abaixo como um ponto de partida para o design de rede do Azure.    

1. As localizações do Azure irá utilizar para alojar as VNets?
2. É necessário fornecer comunicação entre estas localizações do Azure?
3. É necessário fornecer comunicação entre o VNet(s) do Azure e o datacenter(s) no local?
4. Quantos infraestrutura como VMs de serviço (IaaS), cloud e dos serviços de funções, efetue de aplicações web que precisa para a sua solução?
5. É necessário isolar o tráfego com base nos grupos de VMs (servidores de web ou seja, front-end e servidores de base de dados de back-end)?
6. Precisa de controlar o fluxo de tráfego através de aplicações virtuais?
7. Os utilizadores precisam de diferentes conjuntos de permissões para diferentes recursos do Azure?

### <a name="understand-vnet-and-subnet-properties"></a>Compreender as propriedades de VNet e sub-rede
Recursos de VNet e sub-redes ajudam a definir um limite de segurança para cargas de trabalho em execução no Azure. Uma VNet é caracterizada por uma coleção de espaços de endereços, definida como blocos CIDR.

> [!NOTE]
> Os administradores de rede estiver familiarizados com notação CIDR. Se não estiver familiarizado com CIDR, [saber mais acerca do mesmo](http://whatismyipaddress.com/cidr).
>
>

As VNets contêm as seguintes propriedades.

| Propriedade | Descrição | Restrições |
| --- | --- | --- |
| **nome** |Nome da VNet |Cadeia de até 80 carateres. Pode conter letras, números, caráter de sublinhado, pontos ou hífenes. Tem de começar com uma letra ou um número. Tem de terminar com uma letra, um número ou um caráter de sublinhado. Pode contém em maiúsculas ou letras minúsculas. |
| **localização** |Localização do Azure (também referida como região). |Tem de ser uma das localizações do Azure válidas. |
| **addressSpace** |Coleção de prefixos de endereços que compõem a VNet em notação CIDR. |Tem de ser uma matriz de blocos de endereços CIDR válidos, incluindo intervalos de endereços IP públicos. |
| **sub-redes** |Coleção de sub-redes que compõem a VNet |consulte a tabela de propriedades da sub-rede abaixo. |
| **dhcpOptions** |Objeto que contém uma propriedade necessária único com o nome **dnsServers**. | |
| **dnsServers** |Matriz de servidores DNS utilizados pela VNet. Não se for especificado nenhum servidor, é utilizada a resolução do nome interno do Azure. |Tem de ser uma matriz de até 10 servidores DNS, por endereço IP. |

Uma sub-rede é um recurso de subordinados de uma VNet e ajuda a definir segmentos dos espaços de endereços dentro de um bloco CIDR utilizar prefixos de endereços IP. NICs podem ser adicionados a sub-redes e ligados a VMs, fornecer conectividade de várias cargas de trabalho.

Sub-redes contêm as seguintes propriedades.

| Propriedade | Descrição | Restrições |
| --- | --- | --- |
| **nome** |Nome da sub-rede |Cadeia de até 80 carateres. Pode conter letras, números, caráter de sublinhado, pontos ou hífenes. Tem de começar com uma letra ou um número. Tem de terminar com uma letra, um número ou um caráter de sublinhado. Pode contém em maiúsculas ou letras minúsculas. |
| **localização** |Localização do Azure (também referida como região). |Tem de ser uma das localizações do Azure válidas. |
| **addressPrefix** |Prefixo de endereço único que compõem a sub-rede na notação CIDR |Tem de ser um bloco CIDR único que faz parte de um dos espaços de endereços da VNet. |
| **networkSecurityGroup** |NSG aplicado à sub-rede | |
| **routeTable** |Tabela de rotas aplicada à sub-rede | |
| **ipConfigurations** |Coleção de objetos de configuração de IP utilizados pelo NICs ligadas à sub-rede | |

### <a name="name-resolution"></a>Resolução de nomes
Por predefinição, utiliza a sua VNet [resolução de nome fornecidos pelo Azure](virtual-networks-name-resolution-for-vms-and-role-instances.md) para resolver nomes dentro da VNet e na Internet pública. No entanto, se ligar as suas VNets para os centros de dados no local, tem de fornecer [o próprio servidor DNS](virtual-networks-name-resolution-for-vms-and-role-instances.md) para resolver nomes entre as redes.  

### <a name="limits"></a>Limites
Reveja os limites de rede no [Azure limita](../azure-subscription-service-limits.md#networking-limits) artigo para se certificar de que a estrutura não entra em conflito com qualquer uma dos limites. Alguns limites podem ser aumentados ao abrir um pedido de suporte.

### <a name="role-based-access-control-rbac"></a>Controlo de Acesso Baseado em Funções (RBAC)
Pode utilizar [Azure RBAC](../active-directory/role-based-access-built-in-roles.md) para controlar o nível de acesso de utilizadores distintos poderão ter de diferentes recursos no Azure. Dessa forma, que pode segregar o trabalho feito pela sua equipa com base nas suas necessidades.

Preocupações como para as redes virtuais, os utilizadores a **contribuinte de rede** função tem controlo total sobre recursos de rede virtual do Azure Resource Manager. Da mesma forma, os utilizadores a **clássico contribuinte de rede** função tem controlo total sobre recursos de rede virtual clássica.

> [!NOTE]
> Também pode [criar as suas próprias funções](../active-directory/role-based-access-control-configure.md) para separar as suas necessidades administrativas.
>
>

## <a name="design"></a>Design
Quando souber as respostas às questões no [planear](#Plan) secção, reveja o seguinte antes de definir as suas VNets.

### <a name="number-of-subscriptions-and-vnets"></a>Número de subscrições e as VNets
Deve considerar a criação de várias VNets nos seguintes cenários:

* **As VMs que necessitam de ser colocado em diferentes localizações do Azure**. As VNets no Azure são regionais. Estes não podem abranger localizações. Motivo precisa de, pelo menos, uma VNet para cada localização do Azure que pretende VMs de anfitrião no.
* **Cargas de trabalho que têm de ser completamente isoladas entre si**. Pode criar as VNets separadas, que utilizam o mesmos espaços de endereços IP, mesmo para isolar as cargas de trabalho diferentes entre si.

Tenha em atenção que os limites de que consulte acima são por região por subscrição. Isto significa que pode utilizar várias subscrições para aumentar o limite de recursos que pode manter no Azure. Pode utilizar uma VPN de site para site ou um circuito de ExpressRoute para ligar VNets em subscrições diferentes.

### <a name="subscription-and-vnet-design-patterns"></a>Subscrição e padrões de conceção de VNet
A tabela abaixo mostra alguns padrões de conceção comuns para a utilização de subscrições e as VNets.

| Cenário | Diagrama | Profissionais de TI | Contras |
| --- | --- | --- | --- |
| Subscrição único, duas VNets por aplicação |![Subscrição único](./media/virtual-network-vnet-plan-design-arm/figure1.png) |Apenas uma subscrição para gerir. |Número máximo de VNets por região do Azure. Precisa de mais subscrições depois disso. Reveja o [Azure limita](../azure-subscription-service-limits.md#networking-limits) artigo para obter detalhes. |
| Uma subscrição por aplicação, duas VNets por aplicação |![Subscrição único](./media/virtual-network-vnet-plan-design-arm/figure2.png) |Utiliza apenas duas VNets por subscrição. |Mais difícil de gerir quando existem demasiadas aplicações. |
| Uma subscrição por unidade de negócio, duas VNets por aplicação. |![Subscrição único](./media/virtual-network-vnet-plan-design-arm/figure3.png) |Balancear entre o número de subscrições e as VNets. |Número máximo de VNets por unidade de negócio (subscrição). Reveja o [Azure limita](../azure-subscription-service-limits.md#networking-limits) artigo para obter detalhes. |
| Uma subscrição por unidade de negócio, duas VNets por grupo de aplicações. |![Subscrição único](./media/virtual-network-vnet-plan-design-arm/figure4.png) |Balancear entre o número de subscrições e as VNets. |Aplicações tem de ser isoladas através da utilização de sub-redes e NSGs. |

### <a name="number-of-subnets"></a>Número de sub-redes
Deve considerar a várias sub-redes numa VNet nos seguintes cenários:

* **Insuficiente endereços IP privados para todas as NICs numa sub-rede**. Se o seu espaço de endereços da sub-rede não contém endereços IP suficientes para o número de NICs na sub-rede, terá de criar várias sub-redes. Tenha em atenção que o Azure reserva 5 de cada sub-rede que não é possível utilizar a endereços IP privados: os endereços e o apelido do espaço de endereços (para o endereço de sub-rede e multicast) e 3 endereços a ser utilizado internamente (para fins de DHCP e DNS).
* **Segurança**. Pode utilizar sub-redes para separar os grupos de VMs entre si para cargas de trabalho que têm uma camada multi estrutura e aplicar diferentes [(NSGs) de grupos de segurança de rede](virtual-networks-nsg.md#subnets) para essas sub-redes.
* **Conectividade híbrida**. Pode utilizar gateways de VPN e circuitos do ExpressRoute para [ligar](../vpn-gateway/vpn-gateway-about-vpngateways.md#s2smulti) as suas VNets entre si e a center(s) de dados no local. Gateways de VPN e circuitos ExpressRoute necessitam de uma sub-rede os seus próprios a ser criado.
* **Aplicações virtuais**. Pode utilizar uma aplicação virtual, tal como uma firewall, o acelerador WAN ou o gateway de VPN uma VNet do Azure. Se o fizer, terá de [encaminhar o tráfego](virtual-networks-udr-overview.md) para essas aplicações e isolá-los na sua própria sub-rede.

### <a name="subnet-and-nsg-design-patterns"></a>Sub-rede e padrões de conceção do NSG
A tabela abaixo mostra alguns padrões de conceção comuns para a utilização de sub-redes.

| Cenário | Diagrama | Profissionais de TI | Contras |
| --- | --- | --- | --- |
| Sub-rede única, NSGs por camada de aplicação, por aplicação |![Sub-rede única](./media/virtual-network-vnet-plan-design-arm/figure5.png) |Apenas uma sub-rede para gerir. |Vários NSGs necessários para isolar cada aplicação. |
| Uma sub-rede por aplicação, os NSGs por camada da aplicação |![Sub-rede por aplicação](./media/virtual-network-vnet-plan-design-arm/figure6.png) |Menos NSGs para gerir. |Várias sub-redes para gerir. |
| Uma sub-rede por camada de aplicação, os NSGs por aplicação. |![Sub-rede por camada](./media/virtual-network-vnet-plan-design-arm/figure7.png) |Balancear entre o número de sub-redes e NSGs. |Número máximo de NSGs por subscrição. Reveja o [Azure limita](../azure-subscription-service-limits.md#networking-limits) artigo para obter detalhes. |
| Uma sub-rede por camada de aplicação, por aplicação, os NSGs por sub-rede |![Sub-rede por camada por aplicação](./media/virtual-network-vnet-plan-design-arm/figure8.png) |Possivelmente menor número de NSGs. |Várias sub-redes para gerir. |

## <a name="sample-design"></a>Estrutura de exemplo
Para ilustrar a aplicação as informações neste artigo, considere o seguinte cenário.

Funciona para uma empresa que tenha 2 centros de dados na América do Norte e Europa de centros de dados de dois. Identificou 6 cliente diferentes voltado as aplicações mantidas por 2 diferentes unidades de negócio que pretende migrar para o Azure como um piloto. A arquitetura básica para as aplicações são os seguintes:

* App1, App2, App3 e App4 são aplicações web alojadas em servidores Linux em execução Ubuntu. Cada aplicação estabelece ligação a um servidor de aplicação separado que aloja serviços RESTful em servidores Linux. Os serviços RESTful ligar à base de dados MySQL back-end.
* App5 e App6 são aplicações web alojadas em servidores Windows com o Windows Server 2012 R2. Cada aplicação estabelece ligação a uma base de dados do SQL Server de back-end.
* Todas as aplicações estão alojadas atualmente dos centros de dados da empresa na América do Norte.
* Os centros de dados no local utilizam o espaço de endereços 10.0.0.0/8.

É necessário conceber uma solução de rede virtual que cumpra os requisitos seguintes:

* Cada unidade de negócio não deve ser afetada por consumo de recursos de outras unidades de negócio.
* Deve minimiza a quantidade de VNets e sub-redes para facilitar a gestão.
* Cada unidade de negócio deve ter um teste/desenvolvimento único VNet utilizado para todas as aplicações.
* Cada aplicação estiver alojada num 2 do Azure data centers diferentes por continente (América do Norte e Europa).
* Cada aplicação é completamente isolada umas das outras.
* Cada aplicação pode ser acedida pelos clientes através da Internet através de HTTP.
* Cada aplicação pode ser acedida por utilizadores ligados a centros de dados no local utilizando um túnel encriptado.
* Ligação para os centros de dados no local deve utilizar dispositivos VPN existentes.
* Grupo de rede da empresa deve ter controlo total sobre a configuração de VNet.
* Os programadores em cada unidade de negócio só devem ser possível implementar VMs às sub-redes existentes.
* Todas as aplicações serão migradas conforme forem para o Azure (comparação de precisão e shift).
* As bases de dados em cada localização devem replicar para outras localizações do Azure, uma vez por dia.
* Cada aplicação deve utilizar 5 servidores de web de front-end, 2 servidores de aplicações (quando for necessário) e 2 servidores de base de dados.

### <a name="plan"></a>Planear
Deve começar a sua estrutura, planeamento, a resposta à pergunta no [definir requisitos](#Define-requirements) secção conforme mostrado abaixo.

1. As localizações do Azure irá utilizar para alojar as VNets?

    2 localizações na América do Norte, 2 localizações e na Europa. Escolha os com base na localização física dos seus centros de dados no local existente. Dessa forma, a ligação do seu localizações físicas para o Azure terá uma melhor latência.
2. É necessário fornecer comunicação entre estas localizações do Azure?

    Sim. Uma vez que as bases de dados têm de ser replicadas para todas as localizações.
3. É necessário fornecer dados center(s) a comunicação entre o VNet(s) do Azure e no local?

    Sim. Uma vez que os utilizadores ligados a centros de dados no local tem de ser capazes de aceder às aplicações através de um túnel encriptado.
4. Quantas VMs de IaaS necessita para a sua solução?

    200 VMs de IaaS. App1, App2, App3 e App4 requerem 5 servidores web cada, 2 servidores aplicacionais cada e 2 servidores de base de dados. Este é um total de 9 VMs de IaaS por aplicação ou 36 VMs de IaaS. App5 e App6 necessitam de 5 servidores web e 2 servidores de base de dados. Este é um total de 7 VMs de IaaS por aplicação ou 14 VMs de IaaS. Por conseguinte, terá de 50 VMs de IaaS para todas as aplicações em cada região do Azure. Uma vez que é necessário utilizar 4 regiões, será 200 VMs de IaaS.

    Também terá de fornecer os servidores DNS em cada VNet ou nos seus centros de dados no local para resolver o nome entre as VMs de IaaS do Azure e a sua rede no local.
5. É necessário isolar o tráfego com base nos grupos de VMs (servidores de web ou seja, front-end e servidores de base de dados de back-end)?

    Sim. Cada aplicação deve ser completamente isolada umas das outras e cada camada de aplicações também deve ser isolada.
6. Precisa de controlar o fluxo de tráfego através de aplicações virtuais?

    Não. Aplicações virtuais podem ser utilizadas para fornecer mais controlo sobre o fluxo de tráfego, incluindo registo mais detalhado de plane de dados.
7. Os utilizadores precisam de diferentes conjuntos de permissões para diferentes recursos do Azure?

    Sim. A equipa de rede tem controlo total sobre as definições de rede virtuais, enquanto os programadores devem apenas poder implementar as respetivas VMs em sub-redes já existentes.

### <a name="design"></a>Design
Deve seguir a estrutura de especificação de subscrições, VNets, sub-redes e NSGs. Vamos abordar os NSGs aqui, mas deve Saiba mais sobre [NSGs](virtual-networks-nsg.md) antes de concluir a estrutura.

**Número de subscrições e as VNets**

Os seguintes requisitos estão relacionadas com as subscrições e as VNets:

* Cada unidade de negócio não deve ser afetada por consumo de recursos de outras unidades de negócio.
* Deve minimiza a quantidade de VNets e sub-redes.
* Cada unidade de negócio deve ter um teste/desenvolvimento único VNet utilizado para todas as aplicações.
* Cada aplicação estiver alojada num 2 do Azure data centers diferentes por continente (América do Norte e Europa).

Com base nesses requisitos, precisa de uma subscrição para cada unidade de negócio. Dessa forma, consumo de recursos a partir de uma unidade de negócio não contabilizará limites para outras unidades de negócio. E uma vez que pretendem minimizar o número de VNets, deve considerar a utilização de **uma subscrição por unidade de negócio, duas VNets por grupo de aplicações** padrão, como mostrado abaixo.

![Subscrição único](./media/virtual-network-vnet-plan-design-arm/figure9.png)

Terá também de especificar o espaço de endereços para cada VNet. Uma vez que terá de centros de conectividade entre os dados no local e regiões do Azure, o espaço de endereços utilizado para as VNets do Azure não é possível clash com a rede no local e o espaço de endereços utilizado por cada VNet não deve clash com outras VNets existentes. Pode utilizar os espaços de endereços na tabela abaixo para satisfazer estes requisitos.  

| **Subscrição** | **VNet** | **Região do Azure** | **Espaço de endereços** |
| --- | --- | --- | --- |
| BU1 |ProdBU1US1 |EUA Oeste |172.16.0.0/16 |
| BU1 |ProdBU1US2 |EUA Leste |172.17.0.0/16 |
| BU1 |ProdBU1EU1 |Europa do Norte |172.18.0.0/16 |
| BU1 |ProdBU1EU2 |Europa Ocidental |172.19.0.0/16 |
| BU1 |TestDevBU1 |EUA Oeste |172.20.0.0/16 |
| BU2 |TestDevBU2 |EUA Oeste |172.21.0.0/16 |
| BU2 |ProdBU2US1 |EUA Oeste |172.22.0.0/16 |
| BU2 |ProdBU2US2 |EUA Leste |172.23.0.0/16 |
| BU2 |ProdBU2EU1 |Europa do Norte |172.24.0.0/16 |
| BU2 |ProdBU2EU2 |Europa Ocidental |172.25.0.0/16 |

**Número de sub-redes e NSGs**

Os seguintes requisitos estão relacionadas com sub-redes e NSGs:

* Deve minimiza a quantidade de VNets e sub-redes.
* Cada aplicação é completamente isolada umas das outras.
* Cada aplicação pode ser acedida pelos clientes através da Internet através de HTTP.
* Cada aplicação pode ser acedida por utilizadores ligados a centros de dados no local utilizando um túnel encriptado.
* Ligação para os centros de dados no local deve utilizar dispositivos VPN existentes.
* As bases de dados em cada localização devem replicar para outras localizações do Azure, uma vez por dia.

Com base nesses requisitos, pode utilizar uma sub-rede por camada da aplicação e utilizar NSGs para filtrar o tráfego por aplicação. Dessa forma, só tem de 3 sub-redes em cada VNet (front-end, a camada de aplicação e a camada de dados) e um NSG por aplicação por sub-rede. Neste caso, deve considerar a utilização de **uma sub-rede por camada de aplicação, os NSGs por aplicação** padrão de conceção. A figura abaixo mostra a utilização do padrão de conceção que representa o **ProdBU1US1** VNet.

![Uma sub-rede por camada, um NSG por aplicação por camada](./media/virtual-network-vnet-plan-design-arm/figure11.png)

No entanto, também terá de criar uma sub-rede para a conectividade VPN entre as VNets e seus centros de dados no local adicional. E tem de especificar o espaço de endereços para cada sub-rede. A figura abaixo mostra uma solução de exemplo para **ProdBU1US1** VNet. Iriam replicar neste cenário para cada VNet. Cada cor representa uma aplicação diferente.

![Exemplo VNet](./media/virtual-network-vnet-plan-design-arm/figure10.png)

**Controlo de acesso**

Os seguintes requisitos estão relacionadas com controlo de acesso:

* Grupo de rede da empresa deve ter controlo total sobre a configuração de VNet.
* Os programadores em cada unidade de negócio só devem ser possível implementar VMs às sub-redes existentes.

Com base nesses requisitos, pode adicionar utilizadores da equipa de rede incorporada **contribuinte de rede** função em cada subscrição; e criar uma função personalizada para os programadores da aplicação em cada subscrição conceder direitos Para adicionar VMs a sub-redes existentes.

## <a name="next-steps"></a>Passos seguintes
* [Implementar uma rede virtual](virtual-networks-create-vnet-arm-template-click.md) com base no cenário.
* Compreender como [o balanceamento de carga](../load-balancer/load-balancer-overview.md) VMs de IaaS e [Gerir encaminhamento por várias regiões do Azure](../traffic-manager/traffic-manager-overview.md).
* Saiba mais sobre [NSGs e sobre como planear e estruturar](virtual-networks-nsg.md) uma solução NSG.
* Saiba mais sobre o [em vários locais e opções de conectividade de VNet](../vpn-gateway/vpn-gateway-about-vpngateways.md#s2smulti).
