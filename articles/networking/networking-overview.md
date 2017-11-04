---
title: Redes do Azure | Microsoft Docs
description: "Saiba mais sobre os serviços de rede do Azure e capacidades."
services: networking
documentationcenter: na
author: jimdial
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/19/2017
ms.author: jdial
ms.openlocfilehash: 7ed018c8c9759bc497c5fea129257486f6128531
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="azure-networking"></a>Redes do Azure

O Azure oferece uma variedade de capacidades de rede que podem ser utilizadas em conjunto ou separadamente. Clique em qualquer uma das principais funcionalidades seguintes para obter mais informações sobre os mesmos:
- [Conectividade entre os recursos do Azure](#connectivity): recursos Azure ligar em conjunto numa rede virtual privada, segura na nuvem.
- [Conectividade Internet](#internet-connectivity): comunicar e de recursos do Azure através da Internet.
- [No local conectividade](#on-premises-connectivity): ligar a uma rede no local para recursos do Azure através de uma rede privada virtual (VPN) através da Internet ou através de uma ligação dedicada para o Azure.
- [Direção do tráfego e balanceamento de carga](#load-balancing): o tráfego de balanceamento de carga para servidores na mesma localização e o tráfego direto para servidores em diferentes localizações.
- [Segurança](#security): filtrar o tráfego de rede entre sub-redes da rede ou de máquinas virtuais individuais (VM).
- [Encaminhamento](#routing): utilizar o encaminhamento predefinido ou controlar totalmente o encaminhamento entre os recursos do Azure e no local.
- [Capacidade de gestão](#manageability): monitorizar e gerir os recursos de rede do Azure.
- [Ferramentas de implementação e configuração](#tools): utilizar um portal baseado na web ou ferramentas de linha de comandos de várias plataformas para implementar e configurar recursos de rede.

## <a name="Connectivity"></a>Conectividade entre os recursos do Azure

Recursos do Azure como máquinas virtuais, Cloud Services, conjuntos de dimensionamento de máquinas virtuais e ambientes do App Service do Azure podem comunicar em privado entre si através de uma rede Virtual do Azure (VNet). Uma VNet é um isolamento lógico da nuvem do Azure dedicado à sua [subscrição](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fnetworking%2ftoc.json). Pode implementar várias VNets dentro de cada subscrição do Azure e Azure [região](https://azure.microsoft.com/regions). Cada VNet está isolado de outras VNets. Para cada VNet pode:

- Especifique um espaço de endereços IP privado personalizado utilizando endereços (RFC 1918) públicos e privados. Recursos do Azure atribui ligado para a VNet um endereço IP privado o espaço de endereços que atribuir.
- Segmentar a VNet numa ou mais sub-redes e atribuir uma parte do espaço de endereços VNet para cada sub-rede.
- Utilize a resolução de nome fornecidos pelo Azure ou especificar o próprio servidor DNS para utilização por recursos ligados a uma VNet.

Para saber mais sobre o serviço de rede Virtual do Azure, leia o [descrição geral de rede Virtual](../virtual-network/virtual-networks-overview.md?toc=%2fazure%2fnetworking%2ftoc.json) artigo. Pode ligar VNets entre si, permitindo recursos ligados a qualquer das Vnets para comunicar entre si entre VNets. Pode utilizar uma ou ambas as opções seguintes para ligar VNets entre si:

- **Peering:** permite recursos ligados a diferentes as VNets do Azure na mesma região do Azure para comunicar entre si. A largura de banda e latência entre as VNets é o mesmo como se estivessem ligados os recursos para a mesma VNet. Para saber mais sobre o peering, leia o [descrição geral de peering da rede Virtual](../virtual-network/virtual-network-peering-overview.md?toc=%2fazure%2fnetworking%2ftoc.json) artigo.
- **Gateway de VPN:** permite recursos ligados a VNets do Azure diferente dentro de diferentes regiões do Azure para comunicar entre si. Fluxos de tráfego entre as VNets através de um Gateway de VPN do Azure. Largura de banda entre as VNets está limitada a largura de banda do gateway. Para saber mais sobre como ligar VNets com um Gateway de VPN, leia o [configurar uma ligação VNet a VNet em regiões](../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md?toc=%2fazure%2fnetworking%2ftoc.json) artigo.

## <a name="internet-connectivity"></a>Conectividade Internet

Todos os recursos do Azure ligados a uma VNet tem conectividade à Internet de saída por predefinição. O endereço IP privado do recurso é origem endereço de rede traduzido (realizar o SNAT) para um endereço IP público a infraestrutura do Azure. Para saber mais sobre a ligação à Internet de saída, leia o [Noções sobre ligações de saída no Azure](../load-balancer/load-balancer-outbound-connections.md?toc=%2fazure%2fnetworking%2ftoc.json) artigo.

Para comunicar entrada nos recursos do Azure através da Internet, ou para comunicar a saída à Internet sem realizar o SNAT, um recurso deve ser atribuído um endereço IP público. Para saber mais sobre os endereços IP públicos, leia o [endereços IP públicos](../virtual-network/virtual-network-public-ip-address.md?toc=%2fazure%2fnetworking%2ftoc.json) artigo.

## <a name="on-premises-connectivity"></a>Conectividade no local

Pode aceder a recursos na sua VNet em segurança através de uma ligação VPN ou uma ligação privada direta. Para enviar tráfego de rede entre a rede virtual do Azure e a sua rede no local, tem de criar um gateway de rede virtual. Configurar definições para o gateway para criar o tipo de ligação que pretende, VPN ou ExpressRoute.

Pode ligar a sua rede no local para uma VNet com qualquer combinação das seguintes opções:

**Ponto a site (VPN através de SSTP)**

A imagem seguinte mostra ponto separado para ligações de site entre vários computadores e uma VNet:

![Ponto a site](./media/networking-overview/point-to-site.png)

Esta ligação é estabelecida entre um computador único e uma VNet. Este tipo de ligação é excelente se de que está a começar com o Azure ou para os programadores, porque necessita de pouca ou nenhuma alterações à sua rede existente. Também é conveniente quando estiverem a ligar a partir de uma localização remota, como numa conferência ou doméstica. As ligações ponto a site são, muitas vezes, conjugadas com uma ligação site a site através do mesmo gateway de rede virtual. A ligação utiliza o protocolo SSTP para fornecer comunicações encriptadas através da Internet entre o computador e a VNet. A latência de uma VPN ponto a site é imprevisível, uma vez que o tráfego atravessar da Internet.

**Site a site (túnel VPN IPsec/IKE)**

![Site a Site](./media/networking-overview/site-to-site.png)

Esta ligação é estabelecida entre o dispositivo VPN no local e um Gateway de VPN do Azure. Este tipo de ligação permite qualquer recurso no local que está a autorizar para aceder a VNet. A ligação é uma VPN IPSec/IKE que fornece comunicações encriptadas através da Internet entre o dispositivo no local e o gateway de VPN do Azure. Pode ligar vários sites no local para o mesmo gateway VPN. O dispositivo VPN no local em cada site tem de ter um com acesso exterior endereço IP público que não estiver protegido por um NAT. A latência de uma ligação site a site é imprevisível, uma vez que o tráfego atravessar da Internet.

**ExpressRoute (ligação privada dedicada)**

![ExpressRoute](./media/networking-overview/expressroute.png)

Este tipo de ligação é estabelecido entre a rede e o Azure, através de um parceiro do ExpressRoute. Esta ligação é privada. Tráfego atravessar não da Internet. A latência de uma ligação ExpressRoute é previsível, uma vez que o tráfego não atravessar da Internet. ExpressRoute pode ser combinado com uma ligação site a site.

Para saber mais sobre todas as opções de ligação anterior, leia o [diagramas de topologia de ligação](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fnetworking%2ftoc.json) artigo.

## <a name="load-balancing"></a>Direção do tráfego e balanceamento de carga

O Microsoft Azure oferece vários serviços para gerir a forma como o tráfego de rede é distribuído e balanceamento de carga. Pode utilizar qualquer uma das seguintes capacidades em separado ou em conjunto:

**Balanceamento de carga de DNS**

O serviço do Gestor de tráfego do Azure fornece balanceamento de carga de global DNS. Gestor de tráfego responde aos clientes com o endereço IP de um ponto final em bom estado, com base dos seguintes métodos de encaminhamento:
- **Geográfica:** os clientes são direcionados para os pontos finais (Azure externo ou aninhado) com base no que localização geográfica as respetivas consultas DNS tem origem. Este método permite cenários onde saberem a região geográfica do cliente e o encaminhamento-los com base no mesmo, são importante. Os exemplos incluem complying com indica a soberania dos dados, localização de experiência de utilizador e de conteúdo e medir o tráfego de regiões diferentes.
- **Desempenho:** o endereço IP devolvido para o cliente é o "mais próximo" para o cliente. O ponto final 'mais próximo' não é necessariamente mais próximo, medido pela distância geográfica. Em vez disso, este método determina o ponto final mais próximo, medindo a latência de rede. Gestor de tráfego mantém uma tabela de latência de Internet para rastrear o tempo reportadas round-trip entre intervalos de endereços IP e cada datacenter do Azure.
- **Prioridade:** tráfego é direcionado para o ponto final principal da (mais alta prioridade). Se o ponto final principal não estiver disponível, o Gestor de tráfego encaminha o tráfego para o segundo ponto final. Se ambos os principais e secundários pontos finais não estão disponíveis, passa o tráfego de terceiros e assim sucessivamente. Disponibilidade do ponto final baseia o estado configurado (ativado ou desativado) e a monitorização do ponto final em curso.
- **Round robin ponderado:** para cada pedido, o Gestor de tráfego escolhe aleatoriamente um ponto de final disponível. A probabilidade de escolher um ponto final é baseada em ponderações atribuídas para todos os pontos finais disponíveis. Em todos os resultados de pontos finais uma distribuição de tráfego, mesmo que a utilizar a mesma ponderação. Utilizar ponderações superiores ou inferiores em pontos finais específicos, faz com que esses pontos finais deve ser devolvida mais ou menos frequência nas respostas DNS.

A imagem seguinte mostra um pedido para uma aplicação web direcionada para um ponto final da aplicação Web. Pontos finais também podem ser outros serviços do Azure, tais como VMs e serviços em nuvem.

![Gestor de Tráfego](./media/networking-overview/traffic-manager.png)

O cliente liga-se diretamente para esse ponto final. Traffic Manager do Azure Deteta quando um ponto final está danificado e, em seguida, redireciona os clientes para um ponto final de diferente, bom estado de funcionamento. Para saber mais sobre o Gestor de tráfego, leia o [descrição geral do Gestor de tráfego do Azure](../traffic-manager/traffic-manager-overview.md?toc=%2fazure%2fnetworking%2ftoc.json) artigo.

**Balanceamento de carga de aplicação**

O serviço de Gateway de aplicação do Azure fornece o controlador de entrega de aplicações (ADC) como um serviço. Gateway de aplicação oferece vários grupos de capacidades de balanceamento de carga de 7 camadas (HTTP/HTTPS) para as suas aplicações, incluindo uma firewall de aplicação web para proteger as suas aplicações web de vulnerabilidades e explorações. Gateway de aplicação também permite-lhe otimizar a produtividade do web farm ao descarregar a terminação de SSL de intensivas em CPU para o gateway de aplicação. 

Outras funcionalidades de encaminhamento de 7 camadas incluem round robin distribuição de tráfego de entrada, afinidade com base no cookie de sessão, URL com base no caminho de encaminhamento e a capacidade de alojar vários sites por trás de um gateway de aplicação único. Gateway de aplicação pode ser configurado como um gateway de acesso à Internet, um gateway meramente internos ou uma combinação de ambos. Gateway de aplicação é Azure totalmente gerido, escalável e altamente disponível. Proporciona um conjunto avançado de capacidades de registo e diagnóstico, para uma melhor capacidade de gestão. Para saber mais sobre o Gateway de aplicação, leia o [descrição geral do Gateway de aplicação](../application-gateway/application-gateway-introduction.md?toc=%2fazure%2fnetworking%2ftoc.json) artigo.

A imagem seguinte mostra o URL de encaminhamento com o Gateway de aplicação com base no caminho:

![Gateway de Aplicação](./media/networking-overview/application-gateway.png)

**Balanceamento de carga de rede**

O Balanceador de carga do Azure fornece elevado desempenho, baixa latência camada 4 balanceamento de carga para todos os protocolos UDP e TCP. Gere ligações de entrada e saídas. Pode configurar públicos e internos com balanceamento de carga pontos finais. Pode definir regras para mapear ligações de entrada para destinos de conjunto back-end através de TCP e HTTP opções de pesquisa de estado de funcionamento para gerir a disponibilidade do serviço. Para saber mais sobre o Balanceador de carga, leia o [descrição geral do Balanceador de carga](../load-balancer/load-balancer-overview.md?toc=%2fazure%2fnetworking%2ftoc.json) artigo.

A imagem seguinte mostra uma aplicação de várias camada de acesso à Internet que utiliza ambos os balanceadores de carga internos e externos:

![Load balancer](./media/networking-overview/load-balancer.png)

## <a name="security"></a>Segurança

Pode filtrar o tráfego de e para os recursos do Azure utilizando as seguintes opções:

- **Rede:** pode implementar grupos de segurança de rede do Azure (NSGs) para filtrar o tráfego de entrada e saído para recursos do Azure. Cada NSG contém uma ou mais regras de entrada e saídas. Cada regra especifica os endereços IP de origem, endereços IP de destino, porta e protocolo que o tráfego é filtrado com. Os NSGs podem ser aplicados a sub-redes individuais e VMs individuais. Para saber mais sobre NSGs, leia o [descrição geral de grupos de segurança de rede](../virtual-network/virtual-networks-nsg.md?toc=%2fazure%2fnetworking%2ftoc.json) artigo.
- **Aplicação:** utilizando um Gateway de aplicação com firewall de aplicação web pode proteger as suas aplicações web de vulnerabilidades e explorações. Exemplos comuns são ataques de injeção SQL, processamento de scripts entre sites e cabeçalhos com formato incorreto. Gateway de aplicação filtra este tráfego e deixa de chegar aos seus servidores web. É possível configurar as regras que pretende ativado. A capacidade de configurar as políticas de negociação de SSL é fornecida para permitir que determinadas políticas ser desativado. Para saber mais sobre a firewall de aplicação web, leia o [firewall de aplicações Web](../application-gateway/application-gateway-web-application-firewall-overview.md?toc=%2fazure%2fnetworking%2ftoc.json) artigo.

Se precisar de capacidade de rede Azure não fornecer ou pretende utilizar aplicações de rede a utilizar no local, pode implementar os produtos nas VMs e ligue-a sua VNet. O [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/networking?page=1&subcategories=appliances) contém várias VMs diferentes pré-configurados com aplicações de rede poderá a utilizam atualmente. Estas VMs previamente configuradas são normalmente denominados de aparelhos virtuais de rede (NVA). NVAs estão disponíveis com aplicações, tais como a firewall e a otimização de WAN.

## <a name="routing"></a>Encaminhamento

O Azure cria predefinido tabelas de rota que permitem recursos ligados a nenhuma sub-rede qualquer VNet para comunicar entre si. Pode implementar um ou ambos os seguintes tipos de rotas para substituir as rotas predefinidas que Azure cria:
- **Definido pelo utilizador:** pode criar as tabelas de rotas personalizadas com rotas esse controlo onde o tráfego é encaminhado para cada sub-rede. Para saber mais sobre as rotas definidas pelo utilizador, veja o artigo [Rotas definidas pelo utilizador](../virtual-network/virtual-networks-udr-overview.md?toc=%2fazure%2fnetworking%2ftoc.json).
- **Protocolo de gateway Protocol (BGP):** se ligar a VNet à sua rede no local através de uma ligação ExpressRoute ou de Gateway de VPN do Azure, pode propagar rotas BGP para as suas VNets. O BGP é o protocolo de encaminhamento padrão utilizado normalmente na Internet para trocar informações de encaminhamento e acessibilidade entre duas ou mais redes. Quando utilizado no contexto das redes virtuais do Azure, o BGP permite que os Gateways de VPN do Azure e os dispositivos VPN no local, chamados elementos BGP ou vizinhos, troquem "rotas" que o informam ambas as gateways da disponibilidade e acessibilidade para esses prefixos ir através de gateways ou routers envolvidos. BGP também pode ativar o encaminhamento de tráfego entre várias redes ao propagar rotas que um gateway BGP aprende de um elemento de rede BGP para todos os outros elementos BGP. Para saber mais sobre o BGP, veja o [BGP descrição geral de Gateways de VPN do Azure](../vpn-gateway/vpn-gateway-bgp-overview.md?toc=%2fazure%2fnetworking%2ftoc.json) artigo.

## <a name="manageability"></a>Capacidade de gestão

O Azure oferece as seguintes ferramentas para monitorizar e gerir redes:
- **Registos de atividade:** tem de recursos do Azure todos os registos de atividade que fornecem informações sobre as operações executadas, o estado das operações e quem iniciou a operação. Para saber mais sobre os registos de atividade, leia o [descrição geral de registos de atividade](../monitoring-and-diagnostics/monitoring-overview-activity-logs.md?toc=%2fazure%2fnetworking%2ftoc.json) artigo.
- **Os registos de diagnóstico:** Periodic e spontaneous eventos são criados pelos recursos de rede e registados em contas do storage do Azure, enviados para um Hub de eventos do Azure ou enviados para análise de registos do Azure. Os registos de diagnóstico fornecem informações aprofundadas sobre o estado de funcionamento de um recurso. Os registos de diagnóstico são fornecidos para o Balanceador de carga (através da Internet), os grupos de segurança de rede, as rotas e Gateway de aplicação. Para saber mais sobre os registos de diagnóstico, leia o [descrição geral de registos de diagnóstico](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md?toc=%2fazure%2fnetworking%2ftoc.json) artigo.
- **Métricas:** métricas são medidas de desempenho e contadores recolhidos durante um período de tempo nos recursos. Métricas podem ser utilizadas para acionar alertas com base nos limiares. Atualmente as métricas estão disponíveis no Gateway de aplicação. Para saber mais sobre as métricas, leia o [descrição geral de métricas](../monitoring-and-diagnostics/monitoring-overview-metrics.md?toc=%2fazure%2fnetworking%2ftoc.json) artigo.
- **Resolução de problemas:** informações de resolução de problemas está acessível diretamente no portal do Azure. As informações de ajuda a diagnosticar problemas comuns com o ExpressRoute, Gateway de VPN, Gateway de aplicação, registos de segurança de rede, rotas, DNS, Balanceador de carga e do Traffic Manager.
- **O controlo de acesso baseado em funções (RBAC):** controlar quem pode criar e gerir recursos de rede com controlo de acesso baseado em funções (RBAC). Saiba mais sobre o RBAC o lendo o [começar a utilizar o RBAC](../active-directory/role-based-access-control-what-is.md?toc=%2fazure%2fnetworking%2ftoc.json) artigo. 
- **Captura de pacotes:** serviço o observador de rede do Azure fornece a capacidade de executar uma captura de pacotes numa VM através de uma extensão dentro da VM. Esta capacidade está disponível para Linux e VMs do Windows. Para saber mais sobre a captura de pacotes, leia o [descrição geral de captura de pacotes](../network-watcher/network-watcher-packet-capture-overview.md?toc=%2fazure%2fnetworking%2ftoc.json) artigo.
- **Certifique-se de fluxos IP:** observador de rede permite-lhe verificar fluxos IP entre uma VM do Azure e um recurso remoto para determinar se os pacotes são permitidos ou negados. Esta capacidade permite aos administradores Diagnostique rapidamente problemas de conectividade. Para obter mais informações sobre como verificar fluxos IP, leia o [fluxo IP verificar descrição geral](../network-watcher/network-watcher-ip-flow-verify-overview.md?toc=%2fazure%2fnetworking%2ftoc.json) artigo.
- **Resolver problemas de conectividade VPN:** capacidade de troubleshooter a VPN do observador de rede fornece a capacidade de consultar uma ligação ou o gateway e verificar o estado de funcionamento dos recursos. Para obter mais informações sobre resolução de problemas de ligações VPN, leia o [descrição geral de resolução de problemas de conectividade VPN](../network-watcher/network-watcher-troubleshoot-overview.md?toc=%2fazure%2fnetworking%2ftoc.json) artigo.
- **Visualizar a topologia de rede:** ver uma representação gráfica dos recursos de rede numa VNet com o observador de rede. Para saber mais sobre a topologia de rede de visualização, leia o [descrição geral da topologia](../network-watcher/network-watcher-topology-overview.md?toc=%2fazure%2fnetworking%2ftoc.json) artigo.

## <a name="tools"></a>Ferramentas de configuração e implementação

Pode implementar e configurar recursos de rede do Azure com qualquer uma das seguintes ferramentas:

- **Portal do Azure:** uma interface gráfica do utilizador que é executado num browser. Abra o [Portal do Azure](http://portal.azure.com).
- **O Azure PowerShell:** ferramentas da linha de comandos para gerir o Azure a partir de computadores Windows. Saiba mais sobre o Azure PowerShell ao ler o [descrição geral do Azure PowerShell](/powershell/azure/overview?view=azurermps-3.8.0?toc=%2fazure%2fnetworking%2ftoc.json) artigo.
- **Interface de linha de comandos (CLI) do Azure:** ferramentas da linha de comandos para gerir o Azure a partir de computadores Linux, macOS ou do Windows. Saiba mais sobre a CLI do Azure o lendo o [descrição geral da CLI do Azure](/cli/azure/get-started-with-azure-cli?toc=%2fazure%2fnetworking%2ftoc.json) artigo.
- **Modelos Azure Resource Manager:** um ficheiro (no formato JSON) que define a infraestrutura e a configuração de uma solução do Azure. Ao utilizar um modelo, pode implementar repetidamente a solução durante o ciclo de vida da mesma e ter a confiança de que os recursos são implementados num estado consistente. Para saber mais sobre a criação de modelos, leia o [melhores práticas para a criação de modelos](../azure-resource-manager/resource-manager-template-best-practices.md?toc=%2fazure%2fnetworking%2ftoc.json) artigo. Modelos podem ser implementados com o portal do Azure, CLI ou PowerShell. Para começar com modelos de imediato, implemente um dos muitos modelos previamente configurados no [modelos de início rápido do Azure](https://azure.microsoft.com/resources/templates/?term=network) biblioteca. 

## <a name="pricing"></a>Preços

Alguns dos serviços de rede do Azure tenham um custo, enquanto outras são gratuitas. Ver o [rede Virtual](https://azure.microsoft.com/pricing/details/virtual-network), [Gateway de VPN](https://azure.microsoft.com/pricing/details/vpn-gateway), [Gateway de aplicação](https://azure.microsoft.com/en-us/pricing/details/application-gateway/), [Balanceador de carga](https://azure.microsoft.com/pricing/details/load-balancer), [doobservadorderede](https://azure.microsoft.com/pricing/details/network-watcher), [DNS](https://azure.microsoft.com/pricing/details/dns), [Gestor de tráfego](https://azure.microsoft.com/pricing/details/traffic-manager) e [ExpressRoute](https://azure.microsoft.com/pricing/details/expressroute) preços páginas para obter mais informações.

## <a name="next-steps"></a>Passos seguintes

- Criar a primeira VNet e ligar-se algumas VMs, concluindo os passos a [criar a primeira rede virtual](../virtual-network/virtual-network-get-started-vnet-subnet.md?toc=%2fazure%2fnetworking%2ftoc.json) artigo.
- Ligar o seu computador para uma VNet, efetuando os passos a [configurar uma ligação ponto a site](../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md?toc=%2fazure%2fnetworking%2ftoc.json) artigo.
- Balanceamento de carga de tráfego de Internet para servidores públicos, efetuando os passos a [criar um balanceador de carga para a Internet](../load-balancer/load-balancer-get-started-internet-portal.md?toc=%2fazure%2fnetworking%2ftoc.json) artigo.
