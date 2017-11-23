---
title: "Práticas recomendadas de segurança de rede do Azure | Microsoft Docs"
description: "Este artigo fornece um conjunto de melhores práticas para utilizar de segurança de rede incorporada capacidades do Azure."
services: security
documentationcenter: na
author: TomShinder
manager: swadhwa
editor: TomShinder
ms.assetid: 7f6aa45f-138f-4fde-a611-aaf7e8fe56d1
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/21/2017
ms.author: TomSh
ms.openlocfilehash: 3dee3411dadbca5e88951dec2ed1836d440423c4
ms.sourcegitcommit: 8aa014454fc7947f1ed54d380c63423500123b4a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/23/2017
---
# <a name="azure-network-security-best-practices"></a>Práticas recomendadas de segurança de rede do Azure
Microsoft Azure permite-lhe ligar máquinas virtuais e aplicações para outros dispositivos de rede, colocando-los em redes virtuais do Azure. Uma rede Virtual do Azure é uma construção que permite ligar placas de interface de rede virtual a uma rede virtual para permitir a comunicação baseada em TCP/IP entre dispositivos de rede ativada. Máquinas virtuais do Azure ligado a uma rede Virtual do Azure são capazes de estabelecer a ligação para dispositivos em que a mesma rede Virtual do Azure, Azure redes virtuais diferentes, na Internet ou até mesmo as suas próprias redes no local.

Neste artigo, vamos abordar uma coleção de práticas recomendadas de segurança de rede do Azure. Estas melhores práticas são derivadas da nossa experiência com redes do Azure e as experiências dos clientes, como por si.

Para cada melhor prática, vamos explicar:

* O que é a melhor prática
* Por que motivo que pretende ativar essa recomendado
* Se falhar ativar a melhor prática, que poderá ser o resultado
* Possíveis alternativas à melhor prática
* Como pode saber mais ativar a melhor prática

Este artigo de melhores práticas de segurança do Azure rede baseia-se no opinião um consenso e capacidades da plataforma do Azure e conjuntos de funcionalidades, tal como existem no momento que este artigo foi escrito. Opinions e tecnologias alteram ao longo do tempo e este artigo será atualizado regularmente para refletir essas alterações.

Azure rede melhores práticas de segurança abordadas neste artigo incluem:

* Logicamente as sub-redes de segmento
* Controlar o comportamento de encaminhamento
* Ativar a imposição do túnel
* Utilizar dispositivos de rede Virtual
* Implementar o DMZ para zonas de segurança
* Evitar a exposição à Internet com ligações WAN dedicadas
* Otimizar o desempenho e disponibilidade
* Utilizar o balanceamento de carga global
* Desativar o acesso RDP a máquinas virtuais do Azure
* Centro de segurança do Azure de ativação
* Expandir o seu centro de dados no Azure

## <a name="logically-segment-subnets"></a>Logicamente as sub-redes de segmento
[Redes virtuais do Azure](https://azure.microsoft.com/documentation/services/virtual-network/) são semelhantes a uma rede local na sua rede no local. A ideia atrás de uma rede Virtual do Azure é a criação de uma único IP endereço com base no espaço rede privada em que pode colocar todos os seus [Virtual Machines do Azure](https://azure.microsoft.com/services/virtual-machines/). Privada espaços de endereços IP disponíveis são na classe (10.0.0.0/8), classe B (172.16.0.0/12), e intervalos de classe C (192.168.0.0/16).

Semelhante à que fizer no local, deve segmentar o maior espaço de endereços em sub-redes. Pode utilizar [CIDR](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing) com base princípios de sub-redes para criar as sub-redes.

Encaminhamento entre sub-redes acontecerá automaticamente e não terá de configurar manualmente as tabelas de encaminhamento. No entanto, a predefinição é que não existem sem controlos de acesso de rede entre as sub-redes que cria na rede Virtual do Azure. Para criar controlos de acesso de rede entre sub-redes, terá de colocar algo entre as sub-redes.

Uma das ações que pode utilizar para realizar esta tarefa é um [grupo de segurança de rede](../virtual-network/virtual-networks-nsg.md) (NSG). Os NSGs são dispositivos de inspeção de pacotes de monitorização de estado simples que utilizam a 5 cadeias de identificação (o IP de origem, porta de origem, IP de destino, porta de destino e protocolo de camada 4) abordagem para criar permitir/recusar regras para o tráfego de rede. Pode permitir ou negar o tráfego de e para o endereço IP único, para e de vários endereços IP ou mesmo e de sub-redes de todos.

Utilizar os NSGs para controlo de acesso de rede entre sub-redes permite-lhe colocar recursos que pertencem à mesma zona de segurança ou função nas suas próprias sub-redes. Por exemplo, considere uma simples aplicação de 3 camadas que tem uma camada web, uma camada de lógica de aplicação e uma camada de base de dados. Colocar as máquinas virtuais que pertencem a cada uma destas camadas para as suas próprias sub-redes. Em seguida, utilizar os NSGs para controlar o tráfego entre as sub-redes:

* Máquinas de virtuais de camada Web só pode iniciar ligações para as máquinas de lógica de aplicação e só pode aceitar ligações a partir da Internet
* Máquinas de virtuais de lógica de aplicação apenas podem iniciar ligações com camadas de base de dados e só pode aceitar ligações a partir da camada web
* Máquinas de virtuais de camada de base de dados não é possível iniciar a ligação com qualquer coisa fora da sua própria sub-rede e só pode aceitar ligações a partir da camada de lógica de aplicação

Para obter mais informações sobre grupos de segurança de rede e como pode utilizá-las para segmentar logicamente as redes virtuais do Azure, leia o artigo [que é um grupo de segurança de rede](../virtual-network/virtual-networks-nsg.md) (NSG).

## <a name="control-routing-behavior"></a>Controlar o comportamento de encaminhamento
Ao colocar uma máquina virtual numa rede Virtual do Azure, irá reparar que a máquina virtual pode ligar a outra máquina virtual na mesma rede Virtual do Azure, mesmo se outras máquinas virtuais estiverem em sub-redes diferentes. O motivo por que motivo é possíveis é que haja uma coleção de rotas de sistema que estão ativadas por predefinição que permita este tipo de comunicação. Estas rotas predefinidas permitem a máquinas virtuais no mesmo Azure Virtual Network para iniciar as ligações entre si e com a Internet (para comunicações de saída para a Internet apenas).

Enquanto as rotas de sistema predefinida são úteis para vários cenários de implementação, há horas em que pretende personalizar a configuração de encaminhamento para as implementações. Estas personalizações irão permitir-lhe configurar o endereço do próximo salto para aceder a determinados destinos.

Recomendamos que configure rotas definidas pelo utilizador ao implementar uma aplicação de segurança de rede virtual, que iremos falar sobre no posterior melhor prática.

> [!NOTE]
> Rotas definidas pelo utilizador não são necessárias e as rotas de sistema predefinido funciona na maioria das instâncias.
>
>

Pode saber mais sobre rotas definidas pelo utilizador e como configurá-las ao ler o artigo [que são rotas definidas pelo utilizador e reencaminhamento IP](../virtual-network/virtual-networks-udr-overview.md).

## <a name="enable-forced-tunneling"></a>Ativar a imposição do túnel
Para compreender melhor a imposição do túnel, é útil para entender que "a divisão de túnel" é.
O exemplo mais comuns de divisão de túnel é utilizado com ligações VPN. Imagine que estabeleça uma ligação VPN do seu espaço de átrios à sua rede empresarial. Esta ligação permite-lhe aceder a recursos empresariais e todas as comunicações à sua rede empresarial aceda através do túnel VPN.

O que acontece quando pretende ligar a recursos na Internet? Quando a divisão do túnel estiver ativada, essas ligações aceda diretamente à Internet e não através do túnel VPN. Alguns especialistas de segurança, considere esta opção para ser um risco potencial e, por conseguinte, recomendamos que divisão de túnel desativada e todas as ligações, os destinado à Internet e os destinado a recursos da empresa, aceda a através do túnel VPN. A vantagem de fazer isto é que as ligações à Internet, em seguida, são forçadas através de dispositivos de segurança de rede empresarial, que não ser o caso, se o cliente VPN ligado à Internet fora do túnel VPN.

Agora vamos colocar este volta a máquinas virtuais numa rede Virtual do Azure. As rotas predefinidas para uma rede Virtual do Azure permitem que as máquinas virtuais iniciar tráfego à Internet. Isto demasiado pode representar um risco de segurança, como estas ligações de saída foi possível aumentar a superfície de ataque de uma máquina virtual e ser aproveitadas pelas atacantes.
Por este motivo, recomendamos que ative a imposição do túnel nas suas máquinas virtuais quando tiver uma conectividade entre instalações entre a rede Virtual do Azure e a sua rede no local. Serão abordadas cruzada conetividade mais tarde neste documento de práticas de melhor de rede do Azure.

Se não tiver uma ligação entre locais, certifique-se, tirar partido de grupos de segurança de rede (debatidas anteriormente) ou virtual do Azure aplicações de segurança (referido seguinte) para impedir ligações de saída à Internet a partir de máquinas virtuais do Azure de rede.

Para saber mais sobre forçado túnel e como ativá-la, leia o artigo [configurar a imposição do túnel com o PowerShell e do Azure Resource Manager](../vpn-gateway/vpn-gateway-forced-tunneling-rm.md).

## <a name="use-virtual-network-appliances"></a>Utilizar dispositivos de rede virtual
Enquanto os grupos de segurança de rede e o encaminhamento de definido pelo utilizador podem fornecer medidas de segurança de rede em camadas de rede e o transporte do [modelo OSI](https://en.wikipedia.org/wiki/OSI_model), existe que vão ser situações onde irá pretende ou precisa que ative a segurança elevada níveis da pilha. Estas situações, recomendamos que implemente aplicações de segurança de rede virtual fornecidas pelo Azure parceiros.

Aplicações de segurança de rede do Azure podem proporcionar significativamente melhorados níveis de segurança ao longo do que é fornecido por controlos de nível de rede. Algumas das funcionalidades de segurança de rede fornecidas por aplicações de segurança de rede virtual incluem:

* Firewalling
* Deteção de intrusões/intrusões prevenção
* Gestão de vulnerabilidade
* Controlo de aplicação
* Deteção de anomalias com base na rede
* A filtragem da Web
* Antivírus
* Proteção de Botnet

Se necessitar de um nível mais elevado de segurança de rede que pode obter com controlos de nível de acesso de rede, em seguida, recomendamos que analise e implementar aplicações de segurança de rede virtual do Azure.

Para saber mais sobre que rede virtual do Azure estão disponíveis aplicações de segurança e sobre as respetivas capacidades, visitam o [Azure Marketplace](https://azure.microsoft.com/marketplace/) e procure "segurança" e "segurança de rede".

## <a name="deploy-dmzs-for-security-zoning"></a>Implementar o DMZ para zonas de segurança
Uma rede de Perímetro ou "rede de perímetro" é um segmento de rede física ou lógica concebida para fornecer uma camada adicional de segurança entre os elementos e a Internet. A intenção de rede de Perímetro é colocar dispositivos de controlo de acesso de rede especializados na margem da rede DMZ para que apenas pretendido o tráfego é permitido passado o dispositivo de segurança de rede e na rede Virtual do Azure.

DMZ é úteis porque lhe pode concentrar-se a gestão de controlo de acesso de rede, monitorização, registo e relatórios sobre os dispositivos na periferia da sua rede Virtual do Azure. Aqui, normalmente, seria ativar DDoS prevenção, sistemas de prevenção de intrusões/deteção de intrusões (IDS/IPS), as regras de firewall e políticas, web filtragem, antimalware de rede e muito mais. Os dispositivos de segurança de rede manter-se entre a Internet e a rede Virtual do Azure e tem uma interface em ambas as redes.

Enquanto esta é a estrutura básica de uma rede de Perímetro, existem muitas estruturas diferentes de rede de Perímetro, tal como back, duplamente, o tri, multihomed entre outros.

Recomenda-se para todas as implementações de alta segurança que considerar implementar uma rede de Perímetro para melhorar o nível de segurança de rede para os seus recursos do Azure.

Para saber mais sobre DMZ e como implementá-las no Azure, leia o artigo [Cloud Services da Microsoft e de segurança de rede](../best-practices-network-security.md).

## <a name="avoid-exposure-to-the-internet-with-dedicated-wan-links"></a>Evitar a exposição à Internet com ligações WAN dedicadas
Muitas organizações tem escolhido a rota de TI híbridos. TI híbridos, alguns dos recursos de informações da empresa estão no Azure, enquanto outros permanecem no local. Em muitos casos alguns componentes de um serviço irão ser em execução no Azure enquanto outros componentes permanecem no local.

No híbrida cenário IT, existe é normalmente algum tipo de conectividade em vários locais. Isto em vários locais conectividade permite que a empresa estabelecer ligação a redes no local para redes virtuais do Azure. Existem duas soluções de conectividade em vários locais disponíveis:

* VPN de site a site
* ExpressRoute

[VPN de site para site](../vpn-gateway/vpn-gateway-site-to-site-create.md) representa uma ligação privada virtual entre a rede no local e uma rede Virtual do Azure. Esta ligação ocorre através da Internet e permite-lhe informações "túnel" dentro de uma ligação encriptada entre a rede e do Azure. VPN de site a site é uma tecnologia de segura, madura que tenha sido implementada por empresas de todos os tamanhos de decades. Encriptação de túnel é executada com [modo de túnel IPsec](https://technet.microsoft.com/library/cc786385.aspx).

Enquanto VPN site a site é uma tecnologia de fidedigna, fiável e estabelecida, o tráfego de túnel atravessar da Internet. Além disso, a largura de banda relativamente está restringida a um máximo de sobre 200 Mbps.

Se necessitar de um nível excecional de segurança ou de desempenho para as suas ligações entre locais, recomendamos que utilize o Azure ExpressRoute para a conectividade em vários locais. O ExpressRoute é uma WAN dedicada ligação entre a sua localização no local ou num fornecedor de alojamento do Exchange. Porque se trata de uma ligação de telco, os dados não viajam através da Internet e, por conseguinte, não estão expostos a potenciais riscos inerentes em comunicações da Internet.

Para obter mais informações sobre como funciona o Azure ExpressRoute e de como implementar, leia o artigo [descrição geral técnica do ExpressRoute](../expressroute/expressroute-introduction.md).

## <a name="optimize-uptime-and-performance"></a>Otimizar o desempenho e disponibilidade
Confidencialidade, integridade e disponibilidade (CIA) compõem triad do modelo de segurança mais influential hoje. Confidencialidade é sobre a encriptação e privacidade, integridade está prestes a certificar-se de que dados não são alterados por parte do pessoal não autorizada, não sendo disponibilidade sobre certificando-se de que pessoas autorizadas são capazes de aceder às informações que estiverem autorizadas a aceder. Falha em qualquer uma das seguintes áreas representa uma potencial violação em segurança.

Disponibilidade pode considerar como sendo sobre desempenho e disponibilidade. Se um serviço estiver desativado, não não possível aceder a informações. Se, por isso, fraco relativamente inviabilizar a utilização de dados de desempenho, em seguida, iremos pode considerar a inacessível a dados. Por conseguinte, numa perspetiva de segurança, é necessário fazer que podemos para se certificar de que os nossos serviços têm de desempenho e disponibilidade ideal.
Um método popular e mais eficaz utilizado para melhorar o desempenho e disponibilidade consiste em utilizar o balanceamento de carga. Balanceamento de carga é um método de distribuição de tráfego de rede nos servidores que fazem parte de um serviço. Por exemplo, se tiver servidores web front-end como parte do seu serviço, pode utilizar o balanceamento de carga para distribuir o tráfego entre os vários servidores web front-end.

Esta distribuição de tráfego aumenta a disponibilidade dos porque se um dos servidores web ficar indisponível, o Balanceador de carga deixa de envio de tráfego para que o servidor e redireciona-lo para os servidores que ainda estão online. Balanceamento de carga também ajuda-o desempenho, porque o processador, a rede e a sobrecarga para servir pedidos é distribuída por todos os a carga de memória com balanceamento de servidores.

Recomendamos que utilize o balanceamento de carga sempre que pode e, conforme adequado para os serviços. Terá de endereços appropriateness nas secções seguintes: ao nível da rede Virtual do Azure, Azure fornece-lhe primário três as opções de balanceamento de carga:

* Balanceamento de carga baseado em HTTP
* Balanceamento de carga externa
* Balanceamento de carga interno

## <a name="http-based-load-balancing"></a>Balanceamento de carga baseado em HTTP
Balanceamento de carga baseado em HTTP bases decisões sobre o servidor para enviar ligações utilizando características do protocolo HTTP. O Azure tem um balanceador de carga HTTP passa pelo nome do Gateway de aplicação.

Recomendamos que, Gateway de aplicação do Azure dos EUA quando:

* As aplicações que requerem pedidos a partir da mesma sessão de utilizador/cliente para alcançarem a mesma máquina virtual de back-end. Exemplos de isto iriam ser compras carrinho aplicações e servidores de correio eletrónico de web.
* As aplicações que pretendem libertar farms de servidores web de terminação de SSL sobrecarga, tirando partido de Gateway de aplicação [descarga de SSL](https://f5.com/glossary/ssl-offloading) funcionalidade.
* As aplicações, como uma rede de entrega de conteúdos, que requerem balanceamento de vários pedidos HTTP na mesma ligação de TCP de longa execução ser encaminhados ou carregar para diferentes servidores de back-end.

Para obter mais informações sobre como funciona o Gateway de aplicação do Azure e como pode utilizá-lo das implementações, leia o artigo [descrição geral do Gateway de aplicação](../application-gateway/application-gateway-introduction.md).

## <a name="external-load-balancing"></a>Balanceamento de carga externa
Balanceamento de carga externo ocorre quando as ligações recebidas da Internet são com entre os servidores localizados numa rede Virtual do Azure de balanceamento de carga. O Balanceador de carga externo do Azure pode fornecer esta capacidade e recomendamos que o utilize quando não precisa de sessões temporária ou a descarga de SSL.

Ao contrário de balanceamento de carga baseado em HTTP, o Balanceador de carga externo utiliza informações em camadas e o transporte de rede do modelo OSI rede para tomar decisões que servidor carregar saldo ligação.

Recomendamos que utilize o balanceamento de carga externo, sempre que tiver [as aplicações sem estado](http://whatis.techtarget.com/definition/stateless-app) aceitar pedidos recebidos através da Internet.

Para saber mais sobre como funciona o Balanceador de carga externos do Azure e como implementá-la, leia o artigo [introdução à criação de um balanceador de carga com acesso à Internet no Gestor de recursos com o PowerShell](../load-balancer/load-balancer-get-started-internet-arm-ps.md).

## <a name="internal-load-balancing"></a>Balanceamento do Carregamento Interno
Balanceamento de carga interna é semelhante ao balanceamento de carga externo e utiliza o mesmo mecanismo para carregar saldo ligações para os servidores protegidos por eles. A única diferença é que o Balanceador de carga neste caso, está a aceitar ligações a partir de máquinas virtuais que não estão na Internet. Na maioria dos casos, as ligações que são considerados aceites para balanceamento de carga são iniciadas por dispositivos numa rede Virtual do Azure.

Recomendamos que utilize para cenários que beneficiam desta capacidade, por exemplo, quando tiver de carregar saldo ligações aos servidores de SQL ou servidores web interna de balanceamento de carga interna.

Para obter mais informações sobre como funciona o Azure interno balanceamento de carga e como implementá-la, leia o artigo [introdução à criação de um balanceador de carga internos através do PowerShell](../load-balancer/load-balancer-get-started-internet-arm-ps.md#update-an-existing-load-balancer).

## <a name="use-global-load-balancing"></a>Utilizar o balanceamento de carga global
Torna de informática em nuvem pública possível implementar globalmente distribuídas as aplicações que têm componentes localizados nos centros de dados em todo o mundo. Isto é possível no Microsoft Azure devido à presença de centro de dados global do Azure. Contrariamente tecnologias mencionadas anteriormente de balanceamento de carga, o balanceamento de carga global possibilita a fazer com que serviços disponíveis, mesmo quando centros de dados completos podem ficar indisponíveis.

Pode obter este tipo global de balanceamento de carga no Azure, tirando partido de [Traffic Manager do Azure](https://azure.microsoft.com/documentation/services/traffic-manager/). Gestor de tráfego torna é possível carregar saldo ligações aos seus serviços com base na localização do utilizador.

Por exemplo, se o utilizador está a efetuar um pedido ao seu serviço do EU, a ligação é direcionada para os serviços localizados no Centro de dados EU. Esta parte do Gestor de tráfego global carregar balanceamento ajuda a melhorar o desempenho porque a ligação ao centro de dados mais próximo é mais rapidamente do que a ligar ao centros de dados que estão agora ausente.

No lado de disponibilidade, o balanceamento de carga global certifica-se de que o serviço está disponível, mesmo se todo o datacenter deve ficar disponível.

Por exemplo, se um datacenter Azure deve ficar indisponível por motivos ambiental ou devido a falhas (como falhas de rede regional), as ligações ao seu serviço seriam são redirecionadas para o mais próximo do Centro de dados online. Este tipo de balanceamento de carga global é conseguido ao tirar partido das políticas DNS que pode criar no Traffic Manager.

Recomendamos que utilize o Gestor de tráfego para qualquer solução de nuvem desenvolver que tem um âmbito amplamente distribuído em várias regiões e requer o nível mais elevado de disponibilidade possíveis.

Para saber mais sobre o Gestor de tráfego do Azure e como implementá-la, leia o artigo [que é o Gestor de tráfego](../traffic-manager/traffic-manager-overview.md).

## <a name="disable-rdpssh-access-to-azure-virtual-machines"></a>Desativar o acesso RDP/SSH para máquinas virtuais do Azure
É possível aceder a máquinas virtuais do Azure utilizando o [protocolo de ambiente de trabalho remoto](https://en.wikipedia.org/wiki/Remote_Desktop_Protocol) (RDP) e o [Secure Shell](https://en.wikipedia.org/wiki/Secure_Shell) protocolos (SSH). Estes protocolos possibilitam a gerir máquinas virtuais a partir de localizações remotas e são padrão da informática do Centro de dados.

O potencial problema de segurança com utilizando estes protocolos através da Internet é que os atacantes podem utilizar vários [força bruta](https://en.wikipedia.org/wiki/Brute-force_attack) técnicas para obter acesso a máquinas virtuais do Azure. Depois dos atacantes obterem acesso, pode utilizar a máquina virtual como um ponto de iniciação para comprometer a outras máquinas na sua rede Virtual do Azure ou mesmo ataques dispositivos de rede fora do Azure.

Por este motivo, recomendamos que desative acesso direto RDP e SSH para as máquinas de virtuais do Azure através da Internet. Depois de ter acesso direto de RDP e SSH através da Internet é desativado, tiver outras opções que pode utilizar para aceder a estas máquinas virtuais para a gestão remota:

* VPN ponto a site
* VPN de site a site
* ExpressRoute

[VPN ponto a site](../vpn-gateway/vpn-gateway-point-to-site-create.md) é outro termo para uma ligação de cliente/servidor VPN de acesso remoto. Uma VPN ponto a site permite que um único utilizador ligar a uma rede Virtual do Azure através da Internet. Depois de é estabelecida a ligação de ponto a site, o utilizador poderá utilizar o RDP ou SSH para ligar a quaisquer máquinas virtuais localizadas na rede Virtual do Azure que o utilizador ligado através de VPN de ponto a site. Esta parte do princípio de que o utilizador está autorizado a aceder essas máquinas virtuais.

VPN ponto a site é mais segura do que as ligações RDP ou SSH diretas porque o utilizador tem de autenticar duas vezes antes de ligar a uma máquina virtual. Em primeiro lugar, o utilizador tem de autenticar (e ser autorizado) para estabelecer a ligação de VPN ponto a site; segundo, o utilizador tem de autenticar (e ser autorizado) para estabelecer a sessão do RDP ou SSH.

A [VPN site a site](../vpn-gateway/vpn-gateway-site-to-site-create.md) liga uma rede de toda a outra rede através da Internet. Pode utilizar uma VPN de site a site para ligar a sua rede no local a uma rede Virtual do Azure. Se implementar uma VPN de site a site, os utilizadores na sua rede no local será capaz de ligar a máquinas virtuais na sua rede Virtual do Azure utilizando o protocolo RDP ou SSH através da ligação de VPN de site para site e não tem de permitir o acesso direto RDP ou SSH através da Internet.

Também pode utilizar uma ligação WAN dedicada para fornecer a funcionalidade semelhante à VPN site a site. As principais diferenças são 1. a ligação WAN dedicada não atravessam a Internet e 2. as ligações WAN dedicadas são, normalmente, mais estável e performant. O Azure oferece uma solução da ligação WAN dedicado sob a forma de [ExpressRoute](https://azure.microsoft.com/documentation/services/expressroute/).

## <a name="enable-azure-security-center"></a>Centro de segurança do Azure de ativação
Centro de segurança do Azure ajuda-o a evitar, detetar e responder a ameaças e fornece que maior visibilidade e controlo sobre, a segurança dos seus recursos Azure. Fornece gestão de políticas e monitorização de segurança integrada nas suas subscrições do Azure, ajuda a detetar ameaças que caso contrário podem passar despercebidas e funciona com um ecossistema abrangente de soluções de segurança.

Centro de segurança do Azure ajuda-o a otimizar e monitorizar a segurança de rede por:

* Fornecer recomendações de segurança de rede
* Monitorizar o estado da sua configuração de segurança de rede
* Alertar baseados em rede ameaças ambos os níveis do ponto final e de rede

Recomendamos vivamente que ative o Centro de segurança do Azure para todas as implementações do Azure.

Para saber mais sobre o Centro de segurança do Azure e como ativá-la para as implementações, leia o artigo [introdução ao centro de segurança do Azure](../security-center/security-center-intro.md).

## <a name="securely-extend-your-datacenter-into-azure"></a>Expandir o seu centro de dados em segurança no Azure
TI empresariais de muitas organizações estiver à procura de expandir para a nuvem em vez dos respetivos centros de dados no local a crescer. Esta expansão representa uma extensão da infraestrutura de TI existente para a nuvem pública. Ao tirar partido em vários locais Opções de conectividade é possível processar as redes virtuais do Azure como apenas outra sub-rede na sua infraestrutura de rede no local.

No entanto, há muitos dos problemas de planeamento e conceção que têm de ser resolvidos pela primeira vez. Isto é especialmente importante na área de segurança de rede. Uma das formas melhor compreender como abordar a essa uma estrutura é ver um exemplo.

Microsoft criou o [diagrama de arquitetura de referência de extensão de Datacenter](https://gallery.technet.microsoft.com/Datacenter-extension-687b1d84#content) e collateral para ajudar a compreender a extensão do Centro de dados iria aspeto de suporte. Isto fornece uma implementação de referência de exemplo que pode utilizar para planear e estruturar uma extensão de centro de dados empresariais seguros para a nuvem. Recomendamos que consulte o artigo neste documento para obter uma ideia dos componentes principais de uma solução segura.

Para saber mais sobre como expandir o seu centro de dados em segurança no Azure, veja o vídeo [expandir o Datacenter para o Microsoft Azure](https://www.youtube.com/watch?v=Th1oQQCb2KA).
