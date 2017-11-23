---
title: "Rede conceitos de segurança & requisitos no Azure | Microsoft Docs"
description: " Este artigo torna mais fácil para si compreender o que o Microsoft Azure tem que oferecer na área de segurança de rede. Fornecemos explicações básicas para conceitos de segurança de rede principal e os requisitos e as informações em que o Azure tem que oferecer em cada uma destas áreas. "
services: security
documentationcenter: na
author: TomShinder
manager: MBaldwin
editor: TomSh
ms.assetid: bedf411a-0781-47b9-9742-d524cf3dbfc1
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/21/2017
ms.author: terrylan
ms.openlocfilehash: cefc15e7df0dabd9229196d0175dcf6546a6ebce
ms.sourcegitcommit: 62eaa376437687de4ef2e325ac3d7e195d158f9f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/22/2017
---
# <a name="azure-network-security-overview"></a>Descrição geral de segurança de rede do Azure
Microsoft Azure inclui uma infraestrutura robusta de rede para suportar a sua aplicação e os requisitos de conectividade do serviço. Conectividade de rede é possível entre os recursos localizados no Azure, no local e Azure alojadas recursos e de e para a Internet e o Azure.

O objetivo deste artigo é tornar mais fácil para si compreender o que o Microsoft Azure tem que oferecer na área de segurança de rede. Aqui fornecemos explicações básicas dos conceitos de segurança de rede principal e requisitos. Podemos também fornecer informações em que o Azure tem que oferecer em cada uma destas áreas, bem como ligações para o ajudar a obter uma compreensão mais aprofundada das áreas interessantes.

Este artigo de descrição geral de segurança de rede de Azure concentra-se nas seguintes áreas:

* Redes do Azure
* Controlo de acesso de rede
* Proteger a conectividade em vários locais e de acesso remoto
* Disponibilidade
* Resolução de nomes
* Arquitetura de rede de Perímetro
* Monitorização e deteção de ameaças


## <a name="azure-networking"></a>Redes do Azure
Máquinas virtuais precisa de conectividade de rede. Para suportar este requisito, o Azure requer que as máquinas virtuais ligadas a uma rede Virtual do Azure. Uma rede Virtual do Azure é uma construção lógica desenvolvida com os recursos de infraestrutura de rede do Azure física. Cada rede Virtual do Azure lógica está isolada da todas as outras redes virtuais do Azure. Isto ajuda a assegurar que o tráfego de rede das implementações não está acessível para outros clientes do Microsoft Azure.

Saiba mais:

* [Descrição geral da rede virtual](../virtual-network/virtual-networks-overview.md)


## <a name="network-access-control"></a>Controlo de acesso de rede
Controlo de acesso de rede é o ato de limitação de conectividade e de dispositivos específicos ou sub-redes dentro de uma rede Virtual do Azure. O objetivo do controlo de acesso de rede é limitar o acesso às máquinas virtuais e serviços para os utilizadores aprovados e dispositivos. Controlos de acesso baseados em permitir ou negar decisões para ligações de e para a máquina virtual ou serviço.

Azure suporta vários tipos de controlo de acesso à rede, tais como:

* Controlo de camada de rede
* Controlo de rotas e imposição do túnel
* Aplicações de segurança de rede virtual

### <a name="network-layer-control"></a>Controlo de camada de rede
Qualquer implementação segura requer algumas medidas de controlo de acesso de rede. O objetivo de controlo de acesso de rede é restringir a comunicação de máquina virtual para os sistemas necessários e que outras tentativas de comunicação estão bloqueadas.

Se precisar de controlo de acesso de nível de rede básica (com base no endereço IP e os protocolos TCP ou UDP), em seguida, pode utilizar grupos de segurança de rede. Um grupo de segurança de rede (NSG) é um pacote de monitorização de estado básico filtragem firewall e permite-lhe controlar o acesso com base num [5 cadeias de identificação](https://www.techopedia.com/definition/28190/5-tuple). Os NSGs não fornecem inspeção de camada de aplicação ou autenticado controlos de acesso.

Saiba mais:

* [Grupos de segurança de rede](../virtual-network/virtual-networks-nsg.md)

### <a name="route-control-and-forced-tunneling"></a>Controlo de rota e a imposição do túnel
A capacidade para controlar o comportamento de encaminhamento nas redes virtuais do Azure é uma capacidade de controlo de acesso e segurança da rede críticos. Se o encaminhamento é configurado incorretamente, aplicações e serviços alojados na sua máquina virtual podem ligar a dispositivos não autorizados, incluindo sistemas pertencentes à empresa e operada por potenciais atacantes.

Redes do Azure suporta a capacidade para personalizar o comportamento de encaminhamento de tráfego de rede nas redes virtuais do Azure. Isto permite-lhe alterar as entradas do tabela de encaminhamento predefinido na sua rede Virtual do Azure. Controlo do comportamento de encaminhamento ajuda-o a certificar-se de que todo o tráfego de um determinado dispositivo ou grupo de dispositivos entra ou sai da rede virtual através de uma localização específica.

Por exemplo, poderá ter uma aplicação de segurança de rede virtual na sua rede Virtual do Azure. Pretende Certifique-se de que todo o tráfego de e para a rede Virtual do Azure passa através desse dispositivo virtual segurança. Pode fazê-lo através da configuração [rotas definidas pelo utilizador](../virtual-network/virtual-networks-udr-overview.md) no Azure.

[Imposição do túnel](https://www.petri.com/azure-forced-tunneling) é um mecanismo que pode utilizar para se certificar de que os seus serviços não são permitidos para iniciar uma ligação para dispositivos na Internet. Tenha em atenção que isto é diferente do aceitar ligações de entrada e, em seguida, responder aos mesmos. Servidores web front-end tem de responder a pedidos de anfitriões da Internet e, por isso, obtido de Internet o tráfego é permitido de entrada a estes servidores web e os servidores web estão autorizados a responder.

O que não a pretende permitir que é um servidor web front-end para iniciar um pedido de saída. Esses pedidos podem representar um risco de segurança porque podem ser utilizadas estas ligações para transferir o software maligno. Mesmo se quiser que estes servidores front-end para iniciar pedidos de saída à Internet, pode querer forçá-los para percorrer os proxies de web no local para que pode tirar partido do URL de filtragem e registo.

Em vez disso, iria querer utilizar a imposição do túnel para evitar esta situação. Quando ativar a imposição do túnel, todas as ligações à Internet são forçadas através do gateway no local. Pode configurar a imposição do túnel, tirando partido de rotas definidas pelo utilizador.

Saiba mais:

* [O que são rotas definidas pelo utilizador e reencaminhamento IP](../virtual-network/virtual-networks-udr-overview.md)

### <a name="virtual-network-security-appliances"></a>Aplicações de segurança de rede virtual
Enquanto os grupos de segurança de rede, rotas definidas pelo utilizador e a imposição do túnel fornecem-lhe um nível de segurança em camadas de rede e o transporte do [modelo OSI](https://en.wikipedia.org/wiki/OSI_model), poderão existir horas em que pretende ativar segurança níveis superiores a rede.

Por exemplo, os requisitos de segurança poderão incluir:

* Autenticação e autorização antes de permitir o acesso à sua aplicação
* Deteção de intrusão e resposta de intrusões
* Inspeção de camada de aplicação para protocolos de alto nível
* Filtragem de URL
* Antivírus de nível de rede e antimalware
* Proteção de software bot
* Controlo de acesso de aplicação
* Proteção DDoS adicional (acima de proteção fornecido os recursos de infraestrutura do Azure próprio DDoS distribuídos)

Pode aceder a estas funcionalidades de segurança de rede avançada, utilizando uma solução de parceiros do Azure. Pode encontrar a rede de parceiros do Azure mais recente soluções de segurança, visitando o [Azure Marketplace](https://azure.microsoft.com/marketplace/) e procurar por "segurança" e "" segurança de rede.

## <a name="secure-remote-access-and-cross-premises-connectivity"></a>Acesso remoto seguro e a conectividade entre locais
A configuração, configuração e gestão das suas necessidades de recursos do Azure para ser feita remotamente. Além disso, pode querer implementar [TI híbridos](http://social.technet.microsoft.com/wiki/contents/articles/18120.hybrid-cloud-infrastructure-design-considerations.aspx) soluções que tem componentes no local e na nuvem pública do Azure. Estes cenários necessitam de acesso remoto seguro.

Redes do Azure suportam os seguintes cenários de acesso remoto seguro:

* Ligar as estações de trabalho individuais a uma rede Virtual do Azure
* Ligar a sua rede no local a uma rede Virtual do Azure com uma VPN
* Ligar a sua rede no local a uma rede Virtual do Azure com uma ligação WAN dedicada
* Ligar redes virtuais do Azure para si

### <a name="connect-individual-workstations-to-an-azure-virtual-network"></a>Ligar as estações de trabalho individuais a uma rede Virtual do Azure
É possível que existam horas em que pretende ativar o pessoal de operações ou os programadores individuais gerir máquinas virtuais e serviços no Azure. Por exemplo, precisa de acesso a uma máquina virtual numa rede Virtual do Azure e a política de segurança não permite o RDP ou SSH acesso remoto a máquinas virtuais individuais. Neste caso, pode utilizar uma ligação de VPN ponto a site.

A ligação de VPN ponto a site utiliza o [SSTP VPN](https://technet.microsoft.com/library/cc731352.aspx) protocolo que lhe permite configurar uma ligação privada e segura entre o utilizador e a rede Virtual do Azure. Assim que a ligação VPN é estabelecida, o utilizador será capaz de RDP ou SSH através da ligação VPN para qualquer máquina virtual na rede Virtual do Azure (partindo do princípio que o utilizador pode autenticar e está autorizado).

Saiba mais:

* [Configurar uma ligação ponto a Site a uma rede Virtual com o PowerShell](../vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps.md)

### <a name="connect-your-on-premises-network-to-an-azure-virtual-network-with-a-vpn"></a>Ligar a sua rede no local a uma rede Virtual do Azure com uma VPN
Pretende estabelecer ligação a toda a rede empresarial, ou partes do mesmo, a uma rede Virtual do Azure. Isto é comum em TI híbridos cenários em que as empresas [expandir os seus centros de dados no local para Azure](https://gallery.technet.microsoft.com/Datacenter-extension-687b1d84). Em muitos casos, as empresas alojará partes de um serviço no Azure e peças no local, por exemplo, quando uma solução inclui servidores web front-end no Azure e bases de dados de back-end no local. Estes tipos de ligações "locais" também tornam a gestão do Azure localizado recursos mais seguro e ativar cenários tais como expandir os controladores de domínio do Active Directory no Azure.

Uma forma de realizar esta é a utilizar um [VPN site a site](https://www.techopedia.com/definition/30747/site-to-site-vpn). A diferença entre uma VPN de site a site e uma VPN ponto a site é que uma VPN ponto a site liga-se um único dispositivo a uma rede Virtual do Azure, enquanto uma VPN de site para site liga uma toda a rede (por exemplo, a sua rede no local) a uma rede Virtual do Azure. As VPNs de site para site a uma rede Virtual do Azure utilizam o modo de túnel IPsec altamente seguro protocolo VPN.

Saiba mais:

* [Criar uma VNet do Resource Manager com uma ligação de VPN de site a site através do Portal do Azure](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md)
* [Planear e conceber para o gateway de VPN](../vpn-gateway/vpn-gateway-plan-design.md)

### <a name="connect-your-on-premises-network-to-an-azure-virtual-network-with-a-dedicated-wan-link"></a>Ligar a sua rede no local a uma rede Virtual do Azure com uma ligação WAN dedicada
Ligações de VPN ponto a site e site a site são eficazes para ativar a conetividade em vários locais. No entanto, algumas organizações considerar que tenham as seguintes desvantagens:

* Ligações VPN movem dados através da Internet – isto expõe estas ligações para potenciais problemas de segurança ao mover dados através de uma rede pública. Além disso, fiabilidade e disponibilidade para ligações da Internet não poderá ser garantidos.
* Ligações de VPN para redes virtuais do Azure podem ser consideradas largura de banda restringida para algumas aplicações e fins, à medida que máximo saída cerca de 200 Mbps.

As organizações que têm o nível mais elevado de disponibilidade e de segurança para as suas ligações entre locais, normalmente, utilizar ligações WAN dedicadas para ligar a sites remotos. O Azure oferece a capacidade de utilizar uma ligação WAN dedicada que pode utilizar para ligar a sua rede no local a uma rede Virtual do Azure. Esta opção é ativada através do ExpressRoute do Azure.

Saiba mais:

* [Descrição geral técnica do ExpressRoute](../expressroute/expressroute-introduction.md)

### <a name="connect-azure-virtual-networks-to-each-other"></a>Ligar redes virtuais do Azure para si
É possível utilizar várias redes virtuais do Azure para as implementações. Existem muitos motivos por que motivo, poderá fazê-lo. Uma das razões poderá ser simplificar a gestão; outro poderão por motivos de segurança. Independentemente da motivação ou a lógica por detrás para colocar recursos em redes virtuais do Azure diferentes, poderá ser horas em que pretende recursos em cada uma das redes para estabelecer ligação com outro.

Uma opção seria para serviços de uma rede Virtual do Azure para ligar aos serviços de outra rede Virtual do Azure por "volta criar ciclos" através da Internet. A ligação deverá iniciar numa rede Virtual do Azure, aceda através da Internet e, em seguida, volte para o destino da rede Virtual do Azure. Esta opção expõe a ligação para os problemas de segurança inerentes qualquer comunicação baseado na Internet.

Poderá ser uma opção melhor criar ao Virtual uma Azure Virtual de rede para o Azure rede VPN de site para site. Ao Virtual este Azure VPN de site para site de rede de Virtual de rede para o Azure utiliza o mesmo [modo de túnel IPsec](https://technet.microsoft.com/library/cc786385.aspx) protocolo como a ligação de VPN de site para site em vários locais mencionado acima.

A vantagem de utilizar ao Virtual uma Azure Virtual de rede para o Azure rede VPN de site a site é que a ligação VPN é estabelecida através de recursos de infraestrutura de rede do Azure em vez de ligar através da Internet. Isto fornece uma camada adicional de segurança em comparação comparada VPNs de site a site que se ligam através da Internet.

Saiba mais:

* [Configurar uma ligação VNet a VNet através do Azure Resource Manager e o PowerShell](../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md)

## <a name="availability"></a>Disponibilidade
Disponibilidade é um componente fundamental de qualquer programa de segurança. Se os utilizadores e os sistemas não é possível aceder a que precisam de aceder através da rede, o serviço pode ser considerado comprometidos. O Azure tem tecnologias de rede que suportam os seguintes mecanismos de elevada disponibilidade:

* Balanceamento de carga baseado em HTTP
* Balanceamento de carga nível na rede
* Balanceamento de carga global

Balanceamento de carga é um mecanismo concebido para igualmente distribuir as ligações entre vários dispositivos. Os objetivos de balanceamento de carga são:

* Aumentar a disponibilidade – quando carregar saldo ligações em vários dispositivos, um ou mais dos dispositivos podem tornar-se indisponível e os serviços em execução nos restantes dispositivos online podem continuar a servir o conteúdo do serviço
* Aumentar o desempenho – quando carregar saldo ligações em vários dispositivos, um único dispositivo não tem de efetuar os acessos de processador. Em vez disso, os pedidos de processamento e a memória para que serve o conteúdo é distribuído por em vários dispositivos.

### <a name="http-based-load-balancing"></a>Balanceamento de carga baseado em HTTP
As organizações que executem serviços baseados na web, muitas vezes, pretendidos ao nível ter um balanceador de carga baseado em HTTP à frente esses serviços web para o ajudar a assegurar níveis adequados de desempenho e elevada disponibilidade. Contrariamente balanceadores de carga tradicional baseada na rede, o decisões tomadas por baseado em HTTP de balanceamento de carga balanceadores de carga são com base em caraterísticas do protocolo HTTP, não nos protocolos de camada de transporte e de rede.

Para lhe fornecer baseado em HTTP balanceamento de carga para os serviços baseados na web, Azure fornece-lhe o Gateway de aplicação do Azure. O Gateway de aplicação do Azure suporta:

* Baseado em HTTP balanceamento de carga – decisões de balanceamento de carga são efetuadas com base no característica especiais para o protocolo HTTP
* Afinidade com base no cookie de sessão – Esta capacidade certifica-se de que ligações estabelecidas para um dos servidores por trás do Balanceador de carga que permanece intacta entre o cliente e servidor. Desta forma, assegura estabilidade de transações.
* Descarga de SSL – quando é estabelecida uma ligação de cliente com o Balanceador de carga, que sessão entre o cliente e o Balanceador de carga é encriptado utilizando o HTTPS (SSL /) protocolo. No entanto, para aumentar o desempenho, tem a opção para que a ligação entre o Balanceador de carga e o servidor web atrás a utilização de Balanceador de carga o protocolo HTTP (não encriptada). Isto é referido como "Descarga de SSL" porque os servidores web por trás do Balanceador de carga não ocorre a sobrecarga de processador envolvida com a encriptação e, por conseguinte, devem ser capazes de pedidos de serviço mais rapidamente.
* URL de encaminhamento baseado em conteúdo – esta funcionalidade torna possível para o Balanceador de carga tomar decisões sobre onde reencaminhar as ligações com base no URL de destino. Isto proporciona flexibilidade muito mais do que soluções que efetuar com base em endereços IP de decisões de balanceamento de carga.

Saiba mais:

* [Descrição geral do Gateway de aplicação](../application-gateway/application-gateway-introduction.md)

### <a name="network-level-load-balancing"></a>Balanceamento de carga nível na rede
Ao contrário de balanceamento de carga baseado em HTTP, o balanceamento de carga de nível de rede torna decisões de balanceamento de carga baseadas em IP endereço e a porta (TCP ou UDP) números.
Pode obter os benefícios de rede ao nível de balanceamento de carga no Azure utilizando o Balanceador de carga do Azure. Algumas chaves características de Balanceador de carga do Azure incluem:

* Balanceamento de carga de nível de rede com base em números de porta e o endereço IP
* Suporte para qualquer protocolo de camada de aplicação
* Equilibra a carga para máquinas virtuais do Azure e instâncias de função dos serviços de nuvem
* Pode ser utilizado para acesso à Internet (Balanceamento de carga externo) e não à Internet com as máquinas virtuais e aplicações (Balanceamento de carga interno)
* Ponto final de monitorização, que é utilizado para determinar se qualquer um dos serviços atrás do Balanceador de carga ficou indisponível

Saiba mais:

* [Balanceador de carga com acesso à Internet entre várias Máquinas Virtuais ou serviços (Internet Facing load balancer between multiple Virtual Machines or services)](../load-balancer/load-balancer-internet-overview.md)
* [Descrição geral do Balanceador de carga interno](../load-balancer/load-balancer-internal-overview.md)

### <a name="global-load-balancing"></a>Balanceamento de carga global
Algumas organizações irão pretender o nível mais elevado de disponibilidade possíveis. Uma forma de alcançar este objetivo é aplicações nos centros de dados globalmente distribuídos. Quando uma aplicação está alojada nos centros de dados localizados em todo o mundo, é possível que uma região geopolítica completa para se tornar indisponível e continuará a ter a aplicação de cópia de segurança e em execução.

Para além das vantagens de disponibilidade que obtém ao alojar aplicações nos centros de dados globalmente distribuídas, também pode obter os benefícios de desempenho. Estes benefícios de desempenho podem ser obtidos utilizando um mecanismo que direciona pedidos para o Centro de dados é nearest to o dispositivo que está a efetuar o pedido do serviço.

Balanceamento de carga global pode fornecer ambos estes benefícios. No Azure, pode obter os benefícios global de balanceamento de carga utilizando o Gestor de tráfego do Azure.

Saiba mais:

* [O que é o Gestor de Tráfego?](../traffic-manager/traffic-manager-overview.md)


## <a name="name-resolution"></a>Resolução de nomes
Resolução de nomes é uma função crítica para todos os serviços de que anfitrião no Azure. Numa perspetiva de segurança, o compromisso da função de resolução de nome pode levar a um atacante redirecionar pedidos dos sites para o site de um atacante. Resolução de nome seguro é um requisito para todos os seus serviços em nuvem alojado.

Existem dois tipos de resolução do nome que tem de endereço:

* Resolução dos nomes internos – resolução dos nomes internos é utilizada pelos serviços de redes virtuais do Azure, as redes no local ou ambos. Nomes utilizados para a resolução do nome interno não estão acessíveis através da Internet. Para segurança ideal, é importante que o esquema de resolução do nome interno não está acessível a utilizadores externos.
* Resolução de nome externo – resolução de nome externo é utilizada pelas pessoas e dispositivos fora da sua no local e redes virtuais do Azure. Estes são os nomes que estão visíveis para a Internet e são utilizados para direcionar ligação aos seus serviços baseados na nuvem.

Para a resolução do nome interno, tem duas opções:

* Um servidor DNS de rede Virtual do Azure – quando criar um novo Azure Virtual Network, um servidor DNS é criado por si. Este servidor DNS pode resolver os nomes das máquinas localizados na rede Virtual do Azure. Este servidor DNS não é configurável e for gerido pelo Gestor de recursos de infraestrutura do Azure, facilitando deste modo, uma solução de resolução de nome seguro.
* Traga o seu próprio servidor DNS – tem a opção de colocação de um servidor DNS da sua escolha na sua rede Virtual do Azure. Este servidor DNS pode ser que um Active Directory integrado servidor DNS, ou uma solução de servidor dedicada DNS fornecido por um parceiro do Azure, pode obter no Azure Marketplace.

Saiba mais:

* [Descrição geral da rede virtual](../virtual-network/virtual-networks-overview.md)
* [Gerir servidores de DNS utilizados por uma rede Virtual (VNet)](../virtual-network/virtual-network-manage-network.md#dns-servers)

Para a resolução DNS externa, tem duas opções:

* Aloje as suas próprias externo DNS server no local
* Alojar o seu próprio servidor DNS externo com um fornecedor de serviços

Muitas organizações grandes irão alojar os seus próprios DNS servidores no local. Estes podem fazê-lo porque têm o funcionamento em rede conhecimentos e presença global para o fazer.

Na maioria dos casos, é melhor alojar os serviços de resolução de nome DNS com um fornecedor de serviços. Estes fornecedores de serviço tem os conhecimentos de rede e a presença global para garantir a disponibilidade muito elevada para os serviços de resolução do nome. Disponibilidade é essencial para os serviços DNS porque se falharem os serviços de resolução de nome, ninguém será capaz de alcançar a Internet com serviços.

Azure fornece-lhe uma elevada disponibilidade e a solução DNS externa performant sob a forma de DNS do Azure. Esta solução de resolução de nome externo tira partido da infraestrutura de DNS do Azure em todo o mundo. Permite-lhe alojar o domínio no Azure utilizando as ferramentas de credenciais, APIs, mesmo e, de faturação como outros serviços do Azure. Como parte do Azure, também herda controlos de segurança forte construídos a plataforma.

Saiba mais:

* [Descrição geral do DNS do Azure](../dns/dns-overview.md)

## <a name="dmz-architecture"></a>Arquitetura de rede de Perímetro
Muitas organizações empresariais utilizam DMZ para segmentar as redes para criar uma zona de memória intermédia entre os seus serviços e a Internet. A parte da rede de Perímetro da rede é considerada uma zona de segurança baixa e não existem recursos de elevado valor são colocados em desse segmento de rede. Normalmente, irá ver dispositivos de segurança de rede que tenha uma interface de rede num segmento de rede de Perímetro e a outra interface de rede ligado a uma rede que tenha máquinas virtuais e serviços que aceitam ligações de entrada da Internet.

Existem um número de variações de design de rede de Perímetro e a decisão de implementar uma rede de Perímetro, e, em seguida, o tipo de rede de Perímetro a utilizar se optar por utilizar um, baseia-se nos seus requisitos de segurança de rede.

Saiba mais:

* [Serviços Cloud da Microsoft e de segurança de rede](../best-practices-network-security.md)


## <a name="monitoring-and-threat-detection"></a>Monitorização e deteção de ameaças

O Azure oferece capacidades para o ajudar nesta área chave antecipada deteção, monitorização e a capacidade de recolher e analisar o tráfego de rede.

### <a name="azure-network-watcher"></a>Observador de rede do Azure
Observador de rede do Azure inclui um número elevado de capacidades que ajudar a resolver problemas, bem como para fornecer um todo novo conjunto de ferramentas para ajudá-lo com a identificação de problemas de segurança.

[Vista de grupo de segurança ](/network-watcher/network-watcher-security-group-view-overview.md) ajuda a conformidade da auditoria e segurança de máquinas virtuais e podem ser utilizados para efetuar as auditorias programáticas a comparação com as políticas de linhas de base definidas pela sua organização às regras em vigor para cada uma das suas VMs. Isto pode ajudar a identificar que se desviam qualquer configuração.

[Captura de pacotes](/network-watcher/network-watcher-packet-capture-overview.md) permite-lhe capturar o tráfego de rede de e para a máquina virtual. Para além de ajuda, permitindo-lhe recolher estatísticas de rede e com a resolução de problemas da aplicação, captura de pacotes pode ser valiosas na investigação de intrusions de rede. Também pode utilizar esta funcionalidade em conjunto com as funções do Azure para iniciar a captura de rede em resposta a alertas de Azure específicas.

Para mais informações sobre o observador de rede do Azure e como iniciar o teste algumas das funcionalidades no seu laboratórios observe o [descrição geral da monitorização de observador de rede do Azure](/network-watcher/network-watcher-monitoring-overview.md)

>[!NOTE]
Observador de rede do Azure ainda está em pré-visualização pública para que não pode ter o mesmo nível de disponibilidade e fiabilidade como serviços que são, em geral, disponibilidade de versão. Algumas funcionalidades poderão não ser suportadas, poderão ter restrita capacidades e poderão não estar disponíveis em todas as localizações do Azure. Para as notificações mais atualizadas à sua disponibilidade e o estado deste serviço, verifique o [página atualizações do Azure](https://azure.microsoft.com/updates/?product=network-watcher)

### <a name="azure-security-center"></a>Centro de Segurança do Azure
Centro de segurança ajuda-o a evitar, detetar e responder a ameaças e fornece que maior visibilidade e controlo sobre, a segurança dos seus recursos Azure. Fornece gestão de políticas e monitorização de segurança integrada nas suas subscrições do Azure, ajuda a detetar ameaças que caso contrário podem passar despercebidas e funciona com um grande conjunto de soluções de segurança.

Centro de segurança do Azure ajuda-o a otimizar e monitorizar a segurança de rede por:

* Fornecer recomendações de segurança de rede
* Monitorizar o estado da sua configuração de segurança de rede
* Alertar baseados em rede ameaças ambos os níveis do ponto final e de rede

Saiba mais:

* [Introdução ao centro de segurança do Azure](../security-center/security-center-intro.md)


### <a name="logging"></a>Registo
Registo de um nível de rede é uma função de chave para qualquer cenário de segurança de rede. No Azure, pode iniciar sessão informações obtidas para grupos de segurança de rede obter informações de registo de nível de rede. Com o registo de NSG, obter informações a partir de:

* [Registos de atividade](../monitoring-and-diagnostics/monitoring-overview-activity-logs.md) – estes registos são utilizados para ver todas as operações submetidas para as suas subscrições do Azure. Estes registos estão ativados por predefinição e podem ser utilizados no portal do Azure. Estes foram anteriormente conhecidas como "Registos de auditoria" ou "Registos operacionais".
* Registos de eventos – estes registos fornecem informações sobre as regras do NSG foram aplicadas.
* Estes registos de registos de contador – informá-lo quantas vezes a cada regra NSG foi aplicada a negar e permitir o tráfego.

Também pode utilizar [Microsoft Power BI](https://powerbi.microsoft.com/what-is-power-bi/), uma ferramenta de visualização de dados de elevado desempenho, para ver e analisar estes registos.

Saiba mais:

* [Análise de registos para grupos de segurança de rede (NSGs)](../virtual-network/virtual-network-nsg-manage-log.md)
