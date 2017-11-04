---
title: FAQ do ExpressRoute do Azure | Microsoft Docs
description: "FAQ do ExpressRoute contém informações sobre os serviços do Azure suportada, o custo, dados e ligações, SLA, fornecedores e localizações, largura de banda e detalhes técnicos adicionais."
documentationcenter: na
services: expressroute
author: cherylmc
manager: timlt
editor: 
ms.assetid: 09b17bc4-d0b3-4ab0-8c14-eed730e1446e
ms.service: expressroute
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/01/2017
ms.author: cherylmc
ms.openlocfilehash: 0456cde7e30e9b25f8baebdcd15e0e029f89d7ff
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="expressroute-faq"></a>FAQ do ExpressRoute

## <a name="what-is-expressroute"></a>O que é o ExpressRoute?

O ExpressRoute é um serviço Azure que permite-lhe criar ligações privadas entre os datacenters da Microsoft e a infraestrutura no local ou numa instalação de colocalização. As ligações do ExpressRoute não passam para a Internet pública e oferecem maior segurança, fiabilidade e velocidades com latências inferiores em relação a ligações típicas através da Internet.

### <a name="what-are-the-benefits-of-using-expressroute-and-private-network-connections"></a>Quais são as vantagens de utilizar o ExpressRoute e de ligações de rede privada?

As ligações do ExpressRoute não passam para a Internet pública. Oferecem maior segurança, fiabilidade e velocidades, com latências inferiores e consistentes a ligações típicas através da Internet. Em alguns casos, utilizar ligações ExpressRoute para transferir dados entre dispositivos locais e Azure pode produzir benefícios de custos significativas.

### <a name="where-is-the-service-available"></a>Em que o serviço está disponível?

Consulte esta página para a localização do serviço e disponibilidade: [ExpressRoute parceiros e localizações](expressroute-locations.md).

### <a name="how-can-i-use-expressroute-to-connect-to-microsoft-if-i-dont-have-partnerships-with-one-of-the-expressroute-carrier-partners"></a>Como posso utilizar o ExpressRoute para ligar à Microsoft, se não tiver partnerships com um dos parceiros a operadora ExpressRoute?

Pode selecionar uma operadora regional e encaminhado para ligações Ethernet para uma do exchange suportado localizações do fornecedor. Em seguida, pode elemento com a Microsoft a localização do fornecedor. Verifique a última seção do [ExpressRoute parceiros e localizações](expressroute-locations.md) para ver se o fornecedor de serviços se encontra presente das localizações do exchange. Em seguida, pode ordenar um circuito de ExpressRoute através do fornecedor de serviço para ligar ao Azure.

### <a name="how-much-does-expressroute-cost"></a>Qual é o custo de ExpressRoute?

Verifique [detalhes de preços](https://azure.microsoft.com/pricing/details/expressroute/) para obter informações sobre preços.

### <a name="if-i-pay-for-an-expressroute-circuit-of-a-given-bandwidth-does-the-vpn-connection-i-purchase-from-my-network-service-provider-have-to-be-the-same-speed"></a>Se posso pagar para um circuito de ExpressRoute de um determinado largura de banda, a ligação VPN que posso comprar os meus fornecedor de serviço de rede tem de ser a mesma velocidade?

Não. Pode comprar uma ligação de VPN de qualquer velocidade através do seu fornecedor de serviço. No entanto, a ligação ao Azure está limitada a largura de banda do circuito ExpressRoute compra.

### <a name="if-i-pay-for-an-expressroute-circuit-of-a-given-bandwidth-do-i-have-the-ability-to-burst-up-to-higher-speeds-if-necessary"></a>Se posso pagar para um circuito de ExpressRoute de um determinado largura de banda, é necessário a capacidade de impulsar até velocidades superiores, se necessário?

Sim. Circuitos ExpressRoute estão configurados para permitir a impulsar até duas vezes o largura de banda limite que lhe forneceram para sem custos adicionais. Consulte o seu fornecedor de serviço para ver se suportam esta capacidade.

### <a name="can-i-use-the-same-private-network-connection-with-virtual-network-and-other-azure-services-simultaneously"></a>Posso utilizar a mesma ligação de rede privada com a rede virtual e outros serviços do Azure em simultâneo?

Sim. Um circuito do ExpressRoute, uma vez configurado, permite-lhe aceder a outros serviços do Azure e serviços numa rede virtual em simultâneo. Ligar a redes virtuais ao longo do caminho de peering privado e a outros serviços ao longo do caminho de peering público.

### <a name="does-expressroute-offer-a-service-level-agreement-sla"></a>ExpressRoute oferece um contrato de nível de serviço (SLA)?

Para informações, consulte o [ExpressRoute SLA](https://azure.microsoft.com/support/legal/sla/) página.

## <a name="supported-services"></a>Serviços suportados

O ExpressRoute suporta [três domínios de encaminhamento](expressroute-circuit-peerings.md) para vários tipos de serviços.

### <a name="private-peering"></a>Peering privado

* Redes virtuais, incluindo todas as máquinas virtuais e serviços em nuvem

### <a name="public-peering"></a>Peering público

* Power BI
* Dynamics 365 para financeiros e operações (anteriormente conhecidas como Dynamics AX Online)
* Maioria dos serviços do Azure, com o seguinte algumas exceções:
  * CDN
  * Visual Studio Team Services teste de carga
  * Multi-Factor Authentication
  * Gestor de Tráfego

### <a name="microsoft-peering"></a>Peering da Microsoft

* [Office 365](http://aka.ms/ExpressRouteOffice365)
* Aplicações do Dynamics 365 o Engagement de cliente (anteriormente conhecidas como CRM Online)
  * Dynamics 365 de vendas
  * Dynamics 365 para o serviço de cliente
  * Dynamics 365 para o serviço do campo
  * Dynamics 365 para o projeto de serviço
* Utilizar [encaminhar filtros](#route-filters-for-microsoft-peering), obter acesso aos serviços mesmos público com o peering da Microsoft:
  * Power BI
  * Dynamics 365 para operações e financeiro
  * Maioria dos serviços do Azure, com o seguinte algumas exceções:
    * CDN
    * Visual Studio Team Services teste de carga
    * Multi-Factor Authentication
    * Gestor de Tráfego

## <a name="data-and-connections"></a>Ligações de dados e

### <a name="are-there-limits-on-the-amount-of-data-that-i-can-transfer-using-expressroute"></a>Existem limites sobre a quantidade de dados que pode transferir a com o ExpressRoute?

Não iremos definir um limite na quantidade de transferência de dados. Consulte [detalhes de preços](https://azure.microsoft.com/pricing/details/expressroute/) para obter informações sobre as taxas de largura de banda.

### <a name="what-connection-speeds-are-supported-by-expressroute"></a>As velocidades de ligação são suportadas pelo ExpressRoute?

Suportado ofertas de largura de banda:

50 Mbps, 100 Mbps, 200 Mbps, 500 Mbps, 1 Gbps, 2 Gbps, 5 Gbps, 10 Gbps

### <a name="which-service-providers-are-available"></a>Os fornecedores de serviços estão disponíveis?

Consulte [ExpressRoute parceiros e localizações](expressroute-locations.md) para a lista de fornecedores de serviços e localizações.

## <a name="technical-details"></a>Detalhes técnicos

### <a name="what-are-the-technical-requirements-for-connecting-my-on-premises-location-to-azure"></a>Quais são os requisitos técnicos para ligar o meu localização no local para o Azure?

Consulte [página de pré-requisitos do ExpressRoute](expressroute-prerequisites.md) requisitos.

### <a name="are-connections-to-expressroute-redundant"></a>Tem ligações redundantes ao ExpressRoute?

Sim. Cada circuito do ExpressRoute tem um par redundante de entre as ligações configuradas para proporcionar elevada disponibilidade.

### <a name="will-i-lose-connectivity-if-one-of-my-expressroute-links-fail"></a>Irá perder conectividade se falhar um dos meus ligações ExpressRoute?

Não perderá conectividade se uma das ligações cruzadas falhar. Uma ligação redundante está disponível para suportar a carga da rede. Além disso, pode criar vários circuitos numa localização diferente peering para alcançar a resiliência de falha.

## <a name="how-do-i-ensure-high-availability-on-a-virtual-network-connected-to-expressroute"></a>Como posso garantir a elevada disponibilidade numa rede virtual ligada ao ExpressRoute?

Pode alcançar a elevada disponibilidade, ligando-se vários circuitos ExpressRoute em diferentes localizações de peering à sua rede virtual. Por exemplo, se um site de ExpressRoute ficar inativo, conectividade serão ativadas pós-falha para outro site do ExpressRoute. Por predefinição, o tráfego que sai de rede virtual é encaminhado com base no igual custo multi-path encaminhamento (ECMP). Pode utilizar o peso de ligação para preferir uma ligação para outro. Consulte [otimizar o encaminhamento do ExpressRoute](expressroute-optimize-routing.md) para obter detalhes adicionais sobre o peso de ligação.

### <a name="onep2plink"></a>Se não sou colocalizada numa troca de nuvem e os meus fornecedor de serviço oferece ligação ponto a ponto, é necessário ordenar duas ligações físicas entre a minha rede no local e a Microsoft?

Se o fornecedor de serviços pode estabelecer dois circuitos do Ethernet virtuais através da ligação física, só precisa de uma ligação física. A ligação física (por exemplo, uma fibra optical) termina numa camada 1 (L1) dispositivos (consulte a imagem). Os dois circuitos virtuais Ethernet são etiquetados com diferentes IDs de VLAN, um para o circuito primário e outro para o secundário. Os IDs de VLAN estão a ser a 802.1 q externa cabeçalho de Ethernet. O 802.1 q interna cabeçalho Ethernet (não mostrado) está mapeado para um específico [domínio de encaminhamento do ExpressRoute](expressroute-circuit-peerings.md).

![](./media/expressroute-faqs/expressroute-p2p-ref-arch.png)

### <a name="can-i-extend-one-of-my-vlans-to-azure-using-expressroute"></a>Posso expandir a um dos meus VLANs para o Azure com o ExpressRoute?

Não. Não suportamos extensões de 2 conectividade de camada no Azure.

### <a name="can-i-have-more-than-one-expressroute-circuit-in-my-subscription"></a>Pode ter mais do que um circuito de ExpressRoute na minha subscrição?

Sim. Pode ter mais do que um circuito de ExpressRoute na sua subscrição. A predefinição limite é definido para 10. Pode contactar a Microsoft Support para aumentar o limite, se necessário.

### <a name="can-i-have-expressroute-circuits-from-different-service-providers"></a>Pode ter circuitos do ExpressRoute dos fornecedores de serviço diferente?

Sim. Pode ter circuitos ExpressRoute com muitos fornecedores de serviços. Cada circuito ExpressRoute é associado a um serviço fornecedor apenas. 

### <a name="can-i-have-multiple-expressroute-circuits-in-the-same-location"></a>Pode ter vários circuitos ExpressRoute na mesma localização?

Sim. Pode ter vários circuitos do ExpressRoute, com os fornecedores de serviços idêntica ou diferente na mesma localização. No entanto, não é possível associar mais do que um circuito de ExpressRoute à mesma rede virtual da mesma localização.

### <a name="how-do-i-connect-my-virtual-networks-to-an-expressroute-circuit"></a>Como se ligar a minha redes virtuais para um circuito ExpressRoute

Os passos básicos são:

* Estabelecer um circuito ExpressRoute e ter o fornecedor de serviços ativá-la.
* Utilizador ou o fornecedor tem de configurar o BGP peering (s).
* Ligar a rede virtual para o circuito do ExpressRoute.

Para obter mais informações, consulte [fluxos de trabalho do ExpressRoute para o aprovisionamento de circuito e Estados dos circuitos nos](expressroute-workflows.md).

### <a name="are-there-connectivity-boundaries-for-my-expressroute-circuit"></a>Existem limites de conectividade para o meu circuito de ExpressRoute?

Sim. O [ExpressRoute parceiros e localizações](expressroute-locations.md) artigo fornece uma descrição geral sobre os limites de conectividade para um circuito ExpressRoute. Conectividade para um circuito ExpressRoute está limitada a uma única região geopolítica. Conectividade pode ser expandida para regiões geopolíticas de se estender por ativar a funcionalidade de premium do ExpressRoute.

### <a name="can-i-link-to-more-than-one-virtual-network-to-an-expressroute-circuit"></a>Pode ligar a mais do que uma rede virtual para um circuito ExpressRoute?

Sim. Pode ter até 10 ligações de redes virtuais num circuito do ExpressRoute standard e até 100 num [circuito do ExpressRoute premium](#expressroute-premium). 

### <a name="i-have-multiple-azure-subscriptions-that-contain-virtual-networks-can-i-connect-virtual-networks-that-are-in-separate-subscriptions-to-a-single-expressroute-circuit"></a>Tenho várias subscrições do Azure que contêm as redes virtuais. Pode ligar redes virtuais que estão em subscrições separadas para um único circuito ExpressRoute?

Sim. Podem autorizar até 10 outras subscrições do Azure para utilizar um único circuito ExpressRoute. Este limite pode ser aumentada ao ativar a funcionalidade de premium do ExpressRoute.

Para obter mais informações, consulte [partilha um circuito ExpressRoute entre várias subscrições](expressroute-howto-linkvnet-arm.md).

### <a name="i-have-multiple-azure-subscriptions-associated-to-different-azure-active-directory-tenants-or-enterprise-agreement-enrollments-can-i-connect-virtual-networks-that-are-in-separate-tenants-and-enrollments-to-a-single-expressroute-circuit-not-in-the-same-tenant-or-enrollment"></a>Tenho várias subscrições do Azure associadas a diferentes inquilinos do Azure Active Directory ou inscrições através do contrato Enterprise. Pode ligar redes virtuais que estão a ser inquilinos separados e as inscrições para um único circuito de ExpressRoute não no mesmo inquilino ou inscrição?

Sim. Autorizações ExpressRoute podem abranger os limites de subscrição, inquilino e inscrição com nenhuma confgiruation adicional necessário. 

Para obter mais informações, consulte [partilha um circuito ExpressRoute entre várias subscrições](expressroute-howto-linkvnet-arm.md).

### <a name="are-virtual-networks-connected-to-the-same-circuit-isolated-from-each-other"></a>Redes virtuais ligadas ao mesmo circuito isolado entre si?

Não. Numa perspetiva de encaminhamento, todas as redes virtuais ligadas ao mesmo circuito de ExpressRoute fazem parte do mesmo domínio de encaminhamento e não estão isoladas umas das outras. Se precisar de isolamento de rota, terá de criar um separado circuito do ExpressRoute.

### <a name="can-i-have-one-virtual-network-connected-to-more-than-one-expressroute-circuit"></a>Pode ter uma rede virtual ligada a mais do que um circuito do ExpressRoute?

Sim. Pode ligar uma única rede virtual com até quatro circuitos do ExpressRoute. Tem de ser ordenadas através de quatro diferentes [localizações do ExpressRoute](expressroute-locations.md).

### <a name="can-i-access-the-internet-from-my-virtual-networks-connected-to-expressroute-circuits"></a>Pode aceder à Internet do meu redes virtuais ligadas aos circuitos do ExpressRoute?

Sim. Se não tiver anunciados rotas predefinidas (0.0.0.0/0) ou prefixos de rota de Internet através da sessão de BGP, pode ligar à Internet a partir de uma rede virtual ligada a um circuito ExpressRoute.

### <a name="can-i-block-internet-connectivity-to-virtual-networks-connected-to-expressroute-circuits"></a>Posso bloquear a ligação à Internet para redes virtuais ligadas aos circuitos do ExpressRoute?

Sim. Pode anunciar rotas predefinidas (0.0.0.0/0) para bloquear todos os conectividade à Internet para máquinas virtuais implementadas numa rede virtual e encaminhar todo o tráfego de saída através de circuito ExpressRoute.

Se anunciarão rotas predefinidas, iremos forçar tráfego aos serviços fornecidos através de público anterior de peering (por exemplo, o storage do Azure e a base de dados do SQL Server) para o local. Terá de configurar os routers para devolver o tráfego para o Azure através do caminho de peering público ou através da Internet. Se ativou a um ponto final do serviço (pré-visualização) para o serviço, o tráfego para o serviço não é forçado a local. O tráfego permanece dentro da rede principal do Azure. Para obter mais informações sobre pontos finais de serviço, consulte o artigo [pontos finais do serviço de rede Virtual](../virtual-network/virtual-network-service-endpoints-overview.md?toc=%2fazure%2fexpressroute%2ftoc.json)

### <a name="can-virtual-networks-linked-to-the-same-expressroute-circuit-talk-to-each-other"></a>Podem redes virtuais ligadas ao mesmo circuito de ExpressRoute comunicar entre si?

Sim. Máquinas virtuais implementadas em redes virtuais ligadas ao mesmo circuito de ExpressRoute podem comunicar entre si.

### <a name="can-i-use-site-to-site-connectivity-for-virtual-networks-in-conjunction-with-expressroute"></a>Pode utilizar conetividade site a site para redes virtuais em conjunto com o ExpressRoute?

Sim. ExpressRoute pode coexistir com VPNs de site para site.

### <a name="can-i-move-a-virtual-network-from-site-to-site--point-to-site-configuration-to-use-expressroute"></a>Pode mover uma rede virtual da configuração de site para site / ponto a site para utilizar o ExpressRoute?

Sim. Terá de criar um gateway ExpressRoute na sua rede virtual. Não há um período de indisponibilidade breves associado ao processo.

### <a name="why-is-there-a-public-ip-address-associated-with-the-expressroute-gateway-on-a-virtual-network"></a>Por que motivo é um endereço IP público associado ao gateway ExpressRoute numa rede virtual?

O endereço IP público é utilizado para gestão interna apenas. Este endereço IP público não está exposto à Internet e não constituem uma exposição de segurança da sua rede virtual.

### <a name="what-do-i-need-to-connect-to-azure-storage-over-expressroute"></a>O que é necessário ligar ao armazenamento do Azure através do ExpressRoute?

Tem de estabelecer um circuito ExpressRoute e configurar rotas para o peering público.

### <a name="are-there-limits-on-the-number-of-routes-i-can-advertise"></a>Existem limites no número de rotas que posso pode anunciar?

Sim. Aceitamos até 4000 prefixos de rota para peering privado e 200 para peering público e peering da Microsoft. Isto pode aumentar para 10 000 rotas para peering privado se ativar a funcionalidade de premium do ExpressRoute.

### <a name="are-there-restrictions-on-ip-ranges-i-can-advertise-over-the-bgp-session"></a>Existem restrições em intervalos IP que posso pode anunciar durante a sessão BGP?

Não podemos aceitar prefixos privados (RFC1918) na público e peering sessão de BGP Microsoft.

### <a name="what-happens-if-i-exceed-the-bgp-limits"></a>O que acontece se excedem o o BGP limita?

Sessões de BGP serão ignoradas. Serão repostos assim que a contagem de prefixo fica abaixo do limite.

### <a name="what-is-the-expressroute-bgp-hold-time-can-it-be-adjusted"></a>O que é o tempo de espera de BGP de ExpressRoute? Pode, ser ajustado?

O tempo de espera é 180. As mensagens keep-alive são enviadas a cada 60 segundos. Estes são fixo definições no lado da Microsoft que não pode ser alterado. É possível configurar os temporizadores diferentes e os parâmetros de sessão BGP serão ser negociados em conformidade.

### <a name="after-i-advertise-the-default-route-00000-to-my-virtual-networks-i-cant-activate-windows-running-on-my-azure-vms-how-to-i-fix-this"></a>Depois de posso anunciar a rota predefinida (0.0.0.0/0) para os meus redes virtuais, posso não é possível ativar o Windows em execução no meu VMs do Azure. Como a posso corrigir este problema?

Os seguintes passos ajudam a reconhecer o pedido de ativação do Azure:

1. Estabelece peering público para o seu circuito do ExpressRoute.
2. Efetuar uma pesquisa de DNS e encontrar o endereço IP do **kms.core.windows.net**
3. O serviço de gestão de chaves tem reconhece que o pedido de ativação provém do Azure e honor o pedido. Efetue um dos três seguintes tarefas:

   * Na sua rede no local, encaminhar o tráfego destinado o endereços IP que obteve no passo 2 para o Azure através do peering público.
   * O fornecedor NSP hair-pin de ter o tráfego de volta para o Azure através do peering público.
   * Criar uma rota definida pelo utilizador que aponta de IP que tenha Internet como um salto seguinte e aplicá-la para subnet(s) onde são estas máquinas virtuais.

### <a name="can-i-change-the-bandwidth-of-an-expressroute-circuit"></a>Pode alterar a largura de banda de um circuito ExpressRoute?

Sim, pode tentar aumentar a largura de banda do seu circuito de ExpressRoute no portal do Azure, ou utilizando o PowerShell. Se existir capacidade disponível na porta física em que foi criado o seu circuito, a sua alteração será efetuada com êxito. 

Se a sua alteração falha, significa ou não é suficiente capacidade deixado na porta atual e tem de criar um circuito de ExpressRoute novo com a largura de banda superior, ou que não existe nenhuma capacidade adicional nessa localização, caso em que não poderá aumentar a largura de banda. 

Também terá de seguir a cópia de segurança com o seu fornecedor de conectividade para se certificar de que estes atualizar limitações dentro de redes para suportar o aumento de largura de banda. No entanto, não é possível, reduzir a largura de banda do circuito do ExpressRoute. Tem de criar um circuito de ExpressRoute novo com menor largura de banda e eliminar o circuito antigo.

### <a name="how-do-i-change-the-bandwidth-of-an-expressroute-circuit"></a>Como posso alterar a largura de banda de um circuito ExpressRoute?

Pode atualizar a largura de banda do circuito ExpressRoute utilizando a REST API ou o cmdlet do PowerShell.

## <a name="expressroute-premium"></a>Premium do ExpressRoute

### <a name="what-is-expressroute-premium"></a>O que é o ExpressRoute premium?

Premium do ExpressRoute é uma coleção das seguintes funcionalidades:

* Aumento encaminhamento tabela limite de 4000 rotas para 10 000 rotas para peering privado.
* Aumentar o número de VNets que podem ser ligados ao circuito ExpressRoute (a predefinição é 10). Para obter mais informações, consulte o [ExpressRoute limites](#limits) tabela.
* Conetividade para o Office 365 e Dynamics 365.
* Conectividade global através da rede de núcleos da Microsoft. Pode agora ligar uma VNet na região geopolítica um com um circuito de ExpressRoute noutra região.<br>
    **Exemplos:**

    *  Pode ligar uma VNet criada na Europa Ocidental para um circuito ExpressRoute criado em Silicon Valley. 
    *  No peering público, os prefixos de outras regiões geopolíticas estão anunciados que pode ligar ao, por exemplo, SQL do Azure na Europa ocidental de um circuito em Silicon Valley.


### <a name="limits"></a>Quantos VNets pode posso ligar a um circuito ExpressRoute se ativar a premium do ExpressRoute?

As tabelas seguintes mostram os limites do ExpressRoute e o número de VNets por circuito do ExpressRoute:

[!INCLUDE [ExpressRoute limits](../../includes/expressroute-limits.md)]

### <a name="how-do-i-enable-expressroute-premium"></a>Como ativar a premium do ExpressRoute?

As funcionalidades do ExpressRoute premium podem ser ativadas quando a funcionalidade está ativada e podem ser encerradas atualizando o estado do circuito. Pode ativar o ExpressRoute premium no momento de criação de circuito, ou pode chamar a API REST / cmdlet do PowerShell.

### <a name="how-do-i-disable-expressroute-premium"></a>Como desativar o ExpressRoute premium

Pode desativar o ExpressRoute premium ao chamar a REST API ou o cmdlet do PowerShell. Tem de se certificar de que tem ampliada suas necessidades de conectividade para satisfazer os limites predefinidos antes de desativar premium do ExpressRoute. Se a sua utilização dimensiona que ultrapassem os limites predefinidos, falha o pedido para desativar o ExpressRoute premium.

### <a name="can-i-pick-and-choose-the-features-i-want-from-the-premium-feature-set"></a>Pode devo escolher e selecionar as funcionalidades que pretendo do conjunto de funcionalidades premium?

Não. Não é possível escolher as funcionalidades. Iremos ativar todas as funcionalidades, quando ativar o ExpressRoute premium.

### <a name="how-much-does-expressroute-premium-cost"></a>Qual é o custo de premium do ExpressRoute?

Consulte [detalhes de preços](https://azure.microsoft.com/pricing/details/expressroute/) custo.

### <a name="do-i-pay-for-expressroute-premium-in-addition-to-standard-expressroute-charges"></a>Posso pagar do ExpressRoute premium para além dos encargos padrão do ExpressRoute?

Sim. São aplicáveis encargos de premium do ExpressRoute por cima de encargos de circuito do ExpressRoute e eventuais encargos exigidos pelo fornecedor de conectividade.

## <a name="expressroute-for-office-365-and-dynamics-365"></a>ExpressRoute para o Office 365 e Dynamics 365

[!INCLUDE [expressroute-office365-include](../../includes/expressroute-office365-include.md)]

### <a name="how-do-i-create-an-expressroute-circuit-to-connect-to-office-365-services-and-dynamics-365"></a>Como criar um circuito de ExpressRoute para ligar aos serviços do Office 365 e Dynamics 365?

1. Reveja o [página de pré-requisitos do ExpressRoute](expressroute-prerequisites.md) para se certificar de que cumpre os requisitos.
2. Para garantir que as suas necessidades de conectividade são cumpridas, reveja a lista de fornecedores de serviços e as localizações no [ExpressRoute parceiros e localizações](expressroute-locations.md) artigo.
3. Planear os requisitos de capacidade, revendo [planeamento de rede e otimização de desempenho para o Office 365](http://aka.ms/tune/).
4. Siga os passos apresentados em fluxos de trabalho para configurar a conectividade [fluxos de trabalho do ExpressRoute para o aprovisionamento de circuito e Estados dos circuitos nos](expressroute-workflows.md).

> [!IMPORTANT]
> Certifique-se de que tiver ativado o suplemento ExpressRoute premium quando configurar a conectividade a serviços do Office 365 e Dynamics 365.
> 
> 

### <a name="do-i-need-to-enable-azure-public-peering-to-connect-to-office-365-services-and-dynamics-365"></a>É necessário ativar o peering público do Azure ligar aos serviços do Office 365 e Dynamics 365?

Não, apenas terá de ativar o Peering da Microsoft. Tráfego de autenticação para o Azure AD é enviado através do Peering da Microsoft. 

### <a name="can-my-existing-expressroute-circuits-support-connectivity-to-office-365-services-and-dynamics-365"></a>O meu circuito do ExpressRoute existente pode suportar a conectividade a serviços do Office 365 e Dynamics 365?

Sim. O circuito do ExpressRoute existente, pode ser configurado para suportar a conectividade dos serviços do Office 365. Certifique-se de que tem capacidade suficiente para ligar a serviços do Office 365 e se tiver ativado o suplemento premium. [Planeamento de rede e otimização de desempenho para o Office 365](http://aka.ms/tune/) precisa de ajuda a planear a conectividade. Além disso, consulte [criar e modificar um circuito ExpressRoute](expressroute-howto-circuit-classic.md).

### <a name="what-office-365-services-can-be-accessed-over-an-expressroute-connection"></a>Os serviços podem ser acedidos através de uma ligação ExpressRoute do Office 365?

Consulte [intervalos de endereços IP e URLs do Office 365](http://aka.ms/o365endpoints) página para uma lista atualizada dos serviços suportada através do ExpressRoute.

### <a name="how-much-does-expressroute-for-office-365-services-and-dynamics-365-cost"></a>Qual é o ExpressRoute para serviços do Office 365 e o custo de Dynamics 365?

Serviços do Office 365 e Dynamics 365 requerem o suplemento premium ativado. Consulte o [página de detalhes de preços](https://azure.microsoft.com/pricing/details/expressroute/) para os custos.

### <a name="what-regions-is-expressroute-for-office-365-supported-in"></a>As regiões ExpressRoute para o Office 365 é suportado no?

Consulte [ExpressRoute parceiros e localizações](expressroute-locations.md) para obter informações.

### <a name="can-i-access-office-365-over-the-internet-even-if-expressroute-was-configured-for-my-organization"></a>Posso aceder do Office 365 através da Internet, mesmo se ExpressRoute foi configurado para a minha organização?

Sim. Pontos finais de serviço do Office 365 se encontram acessíveis através da Internet, apesar de ExpressRoute foi configurado para a sua rede. Se estiver numa localização que esteja configurada para ligar a serviços do Office 365 através do ExpressRoute, irá ligar através do ExpressRoute.

### <a name="can-i-access-office-365-us-government-community-gcc-services-over-an-azure-us-government-expressroute-circuit"></a>Pode aceder serviços do Office 365 US Government Comunidade (GCC) ao longo de um US Government circuito Azure ExpressRoute?

Sim. Pontos finais de serviço do Office 365 GCC se encontram acessíveis através do ExpressRoute do Azure US Government. No entanto, primeiro terá de abrir um pedido de suporte no portal do Azure para fornecer os prefixos que pretende anunciar no Microsoft. A conectividade dos serviços do Office 365 GCC será estabelecida depois do pedido de suporte é resolvido. 

### <a name="can-dynamics-365-for-operations-formerly-known-as-dynamics-ax-online-be-accessed-over-an-expressroute-connection"></a>Dynamics 365 para operações (anteriormente conhecidas como Dynamics AX Online) pode ser acedido através de uma ligação ExpressRoute?

Sim. [Dynamics 365 para operações](https://www.microsoft.com/dynamics365/operations) está alojada no Azure. Pode ativar o peering público do Azure no seu circuito de ExpressRoute para ligar ao mesmo.

## <a name="route-filters-for-microsoft-peering"></a>Filtros de rota para peering da Microsoft

### <a name="i-am-turning-on-microsoft-peering-for-the-first-time-what-routes-will-i-see"></a>Posso estou ativar o peering da Microsoft pela primeira vez, as rotas irei ver?

Não verá as rotas. Tem de ligar um filtro de rota para o seu circuito para iniciar o prefixo anúncios. Para obter instruções, consulte [configurar filtros de rota para peering da Microsoft](how-to-routefilter-powershell.md).

### <a name="i-turned-on-microsoft-peering-and-now-i-am-trying-to-select-exchange-online-but-it-is-giving-me-an-error-that-i-am-not-authorized-to-do-it"></a>Posso ativou o peering da Microsoft e agora estou a tentar selecionar Exchange Online, mas é fornecer-me um erro que posso não estou autorizado a fazê-lo.

Quando utilizar filtros de rota, qualquer cliente pode ativar o peering da Microsoft. No entanto, para utilizar os serviços do Office 365, ainda tem de obter autorização pelo Office 365.

### <a name="do-i-need-to-get-authorization-for-turning-on-dynamics-365-over-microsoft-peering"></a>É necessário obter autorização para ativar o Dynamics 365 através de peering da Microsoft

Não, não terá autorização para o Dynamics 365. Pode criar uma regra e selecione Comunidade de Dynamics 365 sem autorização.

### <a name="i-enabled-microsoft-peering-prior-to-august-1st-2017-how-can-i-take-advantage-of-route-filters"></a>Posso ativado peering antes de 1ª de Agosto de 2017, como tirar partido de filtros de rota da Microsoft?

O circuito existente irá continuar a anunciar os prefixos para Office 365 e Dynamics 365. Se pretender adicionar anúncios de prefixos públicos do Azure através do mesmo peering da Microsoft, pode criar um filtro de rota, selecione os serviços de terá anunciados (incluindo os serviços do Office 365 que precisa e de Dynamics 365) e ligar o filtro para a Microsoft peering. Para obter instruções, consulte [configurar filtros de rota para peering da Microsoft](how-to-routefilter-powershell.md).

### <a name="i-have-microsoft-peering-at-one-location-now-i-am-trying-to-enable-it-at-another-location-and-i-am-not-seeing-any-prefixes"></a>Tenho Microsoft uma localização de peering, agora estou a tentar ativá-la na outra localização e não estou a ver os prefixos.

* Peering da Microsoft dos circuitos ExpressRoute que foram configurados antes de 1 de Agosto de 2017, terá todos os serviço prefixos anunciados através da Microsoft, peering, mesmo se os filtros de rota não estão definidos.

* Peering da Microsoft dos circuitos ExpressRoute que estão configurados em ou após 1 de Agosto de 2017 não terão qualquer prefixos anunciados até um filtro de rota está ligado ao circuito. Não verá nenhum prefixos por predefinição.
