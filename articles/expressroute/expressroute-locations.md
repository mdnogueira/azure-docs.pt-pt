---
title: "Localizações e os fornecedores de conectividade: Azure ExpressRoute | Microsoft Docs"
description: "Este artigo fornece uma descrição geral detalhada das localizações onde os serviços são oferecidos e como ligar a regiões do Azure. Ordenado pelo fornecedor de conectividade."
services: expressroute
documentationcenter: na
author: cherylmc
manager: timlt
editor: 
ms.assetid: c878513a-d594-42ad-8b0e-403efd0c4b25
ms.service: expressroute
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/14/2017
ms.author: cherylmc
translationtype: Human Translation
ms.sourcegitcommit: e851a3e1b0598345dc8bfdd4341eb1dfb9f6fb5d
ms.openlocfilehash: 15c47d6641e6f5198f88dbe83980f098510916f8
ms.lasthandoff: 04/15/2017


---
# <a name="expressroute-partners-and-peering-locations"></a>Parceiros e localizações de peering do ExpressRoute 

> [!div class="op_single_selector"]
> * [Localizações por Fornecedor](expressroute-locations.md)
> * [Fornecedores por Localização](expressroute-locations-providers.md)


As tabelas neste artigo fornecem informações sobre fornecedores de conectividade ExpressRoute, cobertura geográfica ExpressRoute, serviços em nuvem da Microsoft suportados no ExpressRoute e integradores de sistema ExpressRoute (SIs).

## <a name="partners"></a>Fornecedores de conectividade do ExpressRoute
O ExpressRoute é suportado em todas as regiões e localizações do Azure. O mapa seguinte fornece uma lista de regiões do Azure e localizações do ExpressRoute. As localizações do ExpressRoute mencionam os locais onde a Microsoft se junta a vários fornecedores de serviços.

![Mapa de localização][0]

Terá acesso aos serviços do Azure em todas as regiões numa região geopolítica se estiver ligado a, pelo menos, uma localização do ExpressRoute numa região geopolítica.

### <a name="azure-regions-to-expressroute-locations-within-a-geopolitical-region"></a>Regiões do Azure para localizações do ExpressRoute numa região geopolítica.
A tabela seguinte fornece um mapa de regiões do Azure para localizações do ExpressRoute numa região geopolítica.

| **Região geopolítica** | **Regiões do Azure** | **Localizações do ExpressRoute** |
| --- | --- | --- |
| **América do Norte** |E.U.A. Leste, E.U.A. Oeste, E.U.A. Leste 2, E.U.A. Oeste 2, E.U.A. Central, E.U.A. Centro-Sul, E.U.A. Centro-Norte, E.U.A. Centro-Oeste, Canadá Central, Canadá Leste |Atlanta, Chicago, Dallas, Las Vegas, Los Angeles, Nova Iorque, Seattle, Silicon Valley, Washington D.C., Montreal, Cidade do Quebeque, Toronto |
| **América do Sul** |Sul do Brasil |São Paulo |
| **Europa** |Europa do Norte, Europa Ocidental, Reino Unido Oeste, Reino Unido Sul |Amesterdão, Dublin, Londres, Newport (País de Gales), Paris |
| **Ásia** |Ásia Oriental, Sudeste Asiático |RAE de Hong Kong, Singapura |
| **Japão** |Oeste do Japão, Leste do Japão |Osaka, Tóquio |
| **Austrália** |Sudeste da Austrália, Leste da Austrália |Melbourne, Sydney |
| **Índia** |Índia Ocidental, Índia Central, Sul da Índia |Chennai, Mumbai |
| **Coreia do Sul** |Centro da Coreia, Sul da Coreia |Busan, Seul |

### <a name="regions-and-geopolitical-boundaries-for-national-clouds"></a>Regiões e limites geopolíticos para nuvens nacionais
A tabela abaixo fornece informações sobre regiões e limites geopolíticos para nuvens nacionais.

| **Região geopolítica** | **Regiões do Azure** | **Localizações do ExpressRoute** |
| --- | --- | --- | --- |
| **Nuvem para a Administração Pública dos EUA** |Gov (US) - Iowa, Gov (US) - Virginia, US DoD Central, US DoD East  |Chicago, Dallas, Nova Iorque, Silicon Valley, Washington DC |
| **China** |Norte da China, Leste da China |Pequim, Xangai |
| **Alemanha** |Alemanha Central, Leste da Alemanha |Berlim, Frankfurt |

A conectividade em regiões geopolíticas não é suportada no SKU do ExpressRoute standard. Terá de ativar o suplemento ExpressRoute Premium para suportar a conetividade global. A conectividade com ambientes em nuvem nacionais não é suportada. Pode trabalhar com o seu fornecedor de conectividade, se tal for necessário.

## <a name="locations"></a>Localizações dos fornecedores de conectividade

A tabela seguinte mostra as localizações por fornecedor de serviços. Se quiser ver os fornecedores disponíveis por localização, veja [Fornecedores de serviços por localização](expressroute-locations-providers.md#locations).


### <a name="production-azure"></a>Azure de Produção
| **Fornecedor de serviços** | **Microsoft Azure** | **Office 365 e CRM Online** | **Localizações** |
| --- | --- | --- | --- |
| **[AARNet](https://www.aarnet.edu.au/network-and-services/cloud-services-applications/azure-expressroute/)** |Suportado |Suportado |Melbourne, Sydney |
| **Airtel** | Brevemente | Brevemente | Chennai, Mumbai |
| **[Redes Aryaka](http://www.aryaka.com/)** |Suportado |Suportado |Amesterdão, Dallas, Hong Kong, Silicon Valley, Singapura, Tóquio, Washington D.C. |
| **[AT&T NetBond](https://www.synaptic.att.com/clouduser/html/productdetail/ATT_NetBond.htm)** |Suportado |Suportado |Amesterdão, Chicago, Dallas, Londres, Silicon Valley, Singapura, Sydney, Washington D.C. |
| **[Bell Canada](https://business.bell.ca/shop/enterprise/cloud-connect-access-to-cloud-partner-services)** |Suportado |Suportado |Montreal, Toronto |
| **[British Telecom](http://www.globalservices.bt.com/uk/en/news/bt_to_provide_connectivity_to_microsoft_azure)** |Suportado |Suportado |Amesterdão, RAE de Hong Kong, Londres, Silicon Valley, Singapura, Sydney, Tóquio, Washington D.C. |
| **[CenturyLink](http://www.centurylink.com/business/enterprise/services/data-network/mpls-vpn.html)** |Brevemente |Brevemente |Silicon Valley |
| **China Telecom Global** |Suportado |Não suportado |RAE de Hong Kong |
| **[Cologix](http://www.cologix.com/solutions/cloud-connect/public-clouds/microsoft-cloud/)** |Suportado |Suportado |Dallas, Montreal, Toronto |
| **[Colt](http://www.colt.net/uk/en/news/colt-announces-dedicated-cloud-access-for-microsoft-azure-services-en.htm)** |Suportado |Suportado |Amesterdão, Dublin, Londres, Tóquio |
| **Comcast** |Suportado |Suportado |Chicago, Silicon Valley, Washington D.C. |
| **Console**| Suportado | Suportado |Silicon Valley, Toronto |
| **[CoreSite](http://www.coresite.com/solutions/cloud-services/public-cloud-providers/microsoft-azure-expressroute)** |Suportado |Suportado |Los Angeles, Nova Iorque |
| **[Equinix](http://www.equinix.com/partners/microsoft-azure/)** |Suportado |Suportado |Amesterdão, Atlanta, Chicago, Dallas, Hong Kong, Londres, Los Angeles, Melbourne, Nova Iorque, Osaka, Paris+, São Paulo, Seattle, Silicon Valley, Singapura, Sydney, Tóquio, Toronto, Washington, D.C. |
| **euNetworks** |Suportado |Suportado |Amesterdão |
| **GÉANT** |Suportado |Suportado |Amesterdão |
| **[Cloud Global do Xchange (GCX)] (http://globalcloudxchange.com/cloud-platform/cloud-x-fusion/cloud-x-fusion-for-azure/)** | Suportado| Suportado | Chennai |
| **[InterCloud](https://www.intercloud.com/)** |Suportado |Suportado |Amesterdão, Londres, Singapura, Washington D.C. |
| **[Internet Initiative Japan Inc. – IIJ](http://www.iij.ad.jp/en/news/pressrelease/2015/1216-2.html)** |Suportado |Suportado |Osaka, Tóquio |
| **Internet Solutions – Cloud Connect** |Suportado |Suportado |Amesterdão, Londres |
| **[Interxion](http://www.interxion.com/why-interxion/colocate-with-the-clouds/Microsoft-Azure/)** |Suportado |Suportado |Amesterdão, Londres, Paris |
| **Jisc** |Suportado |Suportado |Londres |
| **KINX** |Suportado |Suportado |Seoul |
| **[KPN](http://www.kpn.com/cloudconnect)** | Suportado | Suportado | Amesterdão | 
| **[Level 3 Communications](http://your.level3.com/LP=882?WT.tsrc=02192014LP882AzureVanityAzureText)** |Suportado |Suportado |Amesterdão, Chicago, Dallas, Las Vegas+, Londres, Seattle, Silicon Valley, Singapura Washington D.C. |
| **LG CNS** |Suportado |Suportado |Busan |
| **[Megaport](https://www.megaport.com/services/microsoft-expressroute/)** |Suportado |Suportado |Dallas, Hong Kong, Las Vegas, Los Angeles, Melbourne, Nova Iorque, Cidade do Quebeque, Seattle, Singapura, Sydney, Toronto, Washington D.C. |
| **MTN** |Suportado |Suportado |Londres |
| **[Next Generation Data](http://www.nextgenerationdata.co.uk/ngd-cloud-gateway/)** |Suportado |Suportado |Newport (País de Gales) |
| **NEXTDC** |Suportado |Suportado |Melbourne, Sydney |
| **[NTT Communications](http://www.ntt.com/en/services/network/virtual-private-network.html)** |Suportado |Suportado |Londres, Los Angeles, Osaca, Singapura, Tóquio, Washington DC |
| **[Orange](http://www.orange-business.com/en/products/business-vpn-galerie)** |Suportado |Suportado |Amesterdão, Hong Kong, Londres, Silicon Valley, Singapura, Sydney, Washington D.C. |
| **PCCW Global Limited** |Suportado |Suportado |RAE de Hong Kong |
| **Sejong Telecom** |Suportado |Suportado |Seoul |
| **[SIFY](http://telecom.sify.com/azure-expressroute.html)** |Suportado |Suportado |Chennai |
| **[SingTel](http://info.singtel.com/about-us/news-releases/singtel-provide-secure-private-access-microsoft-azure-public-cloud)** |Suportado |Suportado |Singapura |
| **[Softbank](http://www.softbank.jp/biz/cloud/cloud_access/direct_access_for_az/)** |Suportado |Suportado |Osaka, Tóquio |
| **[Tata Communications](http://www.tatacommunications.com/lp/izo/azure/azure_index.html)** |Suportado |Suportado |Amesterdão, Chennai, RAE de Hong Kong, Londres, Mumbai, Silicon Valley, Singapura, Washington D.C. |
| **[TeleCity Group](http://www.telecitygroup.com/investor-centre/news_details.htm?locid=03100500400b00d&xml)** |Suportado |Suportado |Amesterdão, Dublin, Londres |
| **[Telefonica](https://www.business-solutions.telefonica.com/es/enterprise/solutions/efficient-infrastructure/managed-voice-data-connectivity/)** |Suportado |Suportado |Amesterdão +, São Paulo |
| **[Telehouse - KDDI](http://www.telehouse.net/solutions/cloud-services/cloud-link)** |Suportado |Suportado |Londres |
| **Telenor** |Suportado |Suportado |Amesterdão, Londres |
| **[Telstra Corporation](http://www.telstra.com.au/business-enterprise/network-services/networks/cloud-direct-connect/)** |Suportado |Suportado |Melbourne, Sydney |
| **[Verizon](http://www.verizonenterprise.com/products/networking/secure-cloud-interconnect/)** |Suportado |Suportado |Amesterdão, Chicago, Dallas, Hong Kong, Londres, Silicon Valley, Singapura, Sydney, Tóquio, Washington D.C |
| **[Vodafone](http://www.vodafone.com/business/global-enterprise/global-connectivity/vodafone-ip-vpn-cloud-connect)** |Suportado |Não suportado |Londres |
| **[Zayo Group](http://www.zayo.com/solutions/industries/cloud-connectivity/microsoft-expressroute)** |Suportado |Suportado |Chicago, Dallas+, Londres+, Los Angeles, Nova Iorque, Silicon Valley, Toronto, Washington D.C. |

 **+** brevemente disponível

### <a name="national-cloud-environment"></a>Ambientes em nuvens nacionais

### <a name="us-government-cloud"></a>Nuvem do Governo dos EUA
| **Fornecedor de serviços** | **Microsoft Azure** | **Office 365** | **Localizações** |
| --- | --- | --- | --- |
| **[AT&T NetBond](https://www.synaptic.att.com/clouduser/html/productdetail/ATT_NetBond.htm)** |Suportado |Suportado |Chicago, Washington D.C. |
| **[Equinix](http://www.equinix.com/partners/microsoft-azure/)** |Suportado |Suportado |Chicago, Dallas, Nova Iorque, Seattle+, Silicon Valley, Washington DC |
| **[Level 3 Communications](http://your.level3.com/LP=882?WT.tsrc=02192014LP882AzureVanityAzureText)** |Suportado |Suportado |Chicago, Nova Iorque+, Washington D.C. |
| **[Megaport](https://www.megaport.com/services/microsoft-expressroute/)** |Suportado | Suportado | Chicago, Dallas |
| **[Verizon](http://news.verizonenterprise.com/2014/04/secure-cloud-interconnect-solutions-enterprise/)** |Suportado |Suportado |Chicago, Dallas, Nova Iorque, Washington D.C. |

### <a name="china"></a>China
| **Fornecedor de serviços** | **Microsoft Azure** | **Office 365** | **Localizações** |
| --- | --- | --- | --- |
| **China Telecom** |Suportado |Não suportado |Pequim, Xangai |

Para saber mais, veja [ExpressRoute na China](http://www.windowsazure.cn/home/features/expressroute/).

### <a name="germany"></a>Alemanha
| **Fornecedor de serviços** | **Microsoft Azure** | **Office 365** | **Localizações** |
| --- | --- | --- | --- |
| **[Colt](http://www.colt.net/uk/en/news/colt-announces-dedicated-cloud-access-for-microsoft-azure-services-en.htm)** |Suportado |Não suportado |Berlim+, Frankfurt |
| **[Equinix](http://www.equinix.com/partners/microsoft-azure/)** |Suportado |Não suportado |Frankfurt |
| **[e-shelter](https://www.e-shelter.de/en/microsoft-expressroutetm)** |Suportado |Não Suportado |Berlim |
| **Interxion** |Suportado |Não suportado |Frankfurt |
| **[Megaport](https://www.megaport.com/services/microsoft-expressroute/)** |Suportado  | Não Suportado | Berlim |

## <a name="c1partners"></a>Conectividade através de fornecedores de serviços não listados
Se o seu fornecedor de conectividade não está listado nas secções anteriores, pode criar na mesma uma ligação.

* Consulte o seu fornecedor de conectividade para saber se este está ligado a alguma das trocas na tabela acima. Pode verificar as ligações seguintes para recolher mais informações sobre os serviços disponibilizados pelos fornecedores de troca. Vários fornecedores de conectividade já estão ligados às trocas de Ethernet.
  * [Cologix](http://www.cologix.com/)
  * [CoreSite](http://www.coresite.com/)
  * [Equinix Cloud Exchange](http://www.equinix.com/services/interconnection-connectivity/cloud-exchange/)
  * [Interxion](http://www.interxion.com/products/interconnection/cloud-connect/)
  * [Megaport](https://www.megaport.com/services/microsoft-expressroute/)
  * [NextDC](http://www.nextdc.com/)
  * [TeleCity CloudIX](http://www.telecitygroup.com/colocation-services/cloud-ix.htm)
* Solicite ao seu fornecedor de conectividade para expandir a sua rede para a localização de peering que pretende.
  * Certifique-se de que o fornecedor de conectividade expande a sua conectividade para um estado de elevada disponibilidade, de forma a não existirem pontos únicos de falha.
* Organize um circuito ExpressRoute com a troca como o seu fornecedor de conectividade para se ligar à Microsoft.
  * Siga os passos em [Criar um circuito ExpressRoute](expressroute-howto-circuit-classic.md) para configurar a conectividade.

| **Fornecedor de conectividade** | **Exchange** | **Localizações** |
| --- | --- | --- |
| **[1CLOUDSTAR](http://www.1cloudstar.com/service/cloudconnect-azure-expressroute/)** |Equinix |Singapura |
| **[Airgate Technologies, Inc.](http://airgate.ca/cloud-express/)** | Equinix, Cologix | Toronto, Montreal |
| **[Alaska Communications](http://www.alaskacommunications.com/For-Your-Business/Direct-Cloud-Service)** |Equinix |Seattle |
| **[Arteria Networks Corporation](https://arteria-net.com/business/service/cloud_access/sca/)** |Equinix |Tóquio |
| **[Bezeq International Ltd.](https://www.bezeqint.net/english)** | euNetworks | Londres |
| **[C3ntro](http://www.c3ntro.com/data/cloud-conectivity/)** | Equinix, Megaport | Dallas |
| **[Cogeco Peer 1](https://www.cogecopeer1.com/en/)**| Equinix | Montreal, Toronto |
| **[Data Foundry](https://www.datafoundry.com/services/cloud-connect)** | Megaport | Dallas |
| **[Epsilon Telecommunications Limited](http://www.epsilontel.com/data-connectivity/cloud-access/)** | Equinix | Singapura |
| **[Eurofiber](https://eurofiber.nl/microsoft-azure/)** | Equinix | Amesterdão |
| **[Exponential E](http://www.exponential-e.com/services/connectivity-services/cloud-connect-exchange)** | Equinix | Londres |
| **[Fastweb S.p.A](http://www.fastweb.it/grandi-aziende/connessione-voce-e-wifi/scheda-prodotto/rete-privata-virtuale/)** | Equinix | Amesterdão |
| **[HSO](http://www.hso.co.uk/products/cloud-direct)** |Equinix | Londres, Slough |
| **[Lightower](http://www.lightower.com/network-solutions/cloud-connect/#microsoft-azure)** |Equinix |Nova Iorque, Washington D.C. |
| **[Macquarie Telecom Group](https://macquariegovernment.com/secure-cloud/secure-cloud-exchange/)** | Megaport | Sydney |
| **[Masergy](https://www.masergy.com/solutions/hybrid-networking/cloud-marketplace/microsoft-azure)** | Equinix | Washington DC |
| **[NexGen Networks](http://www.nexgen-net.com/nexgen-networks-direct-connect-microsoft-azure-expressroute.html)** | Interxion | Londres |
| **[Nianet](https://nianet.dk/produkter/internet/microsoft-expressroute)** |Telecity | Amesterdão, Frankfurt |  
| **[QSC AG](https://www.qsc.de/de/produkte-loesungen/cloud-services-und-it-outsourcing/pure-enterprise-cloud/multi-cloud-management/azure-expressroute/)** |Interxion | Frankfurt |  
| **[Tamares Telecom](http://www.tamarestelecom.com/our-services/#Connectivity)** | Telecity | Londres
| **[Transtelco](http://www.transtelco.net/tcloud/microsoft)** |Equinix | Dallas, Los Angeles |  
| **[Windstream](http://www.windstreambusiness.com/solutions/cloud-services/cloud-and-managed-hosting-services)**| Equinix | Chicago, Silicon Valley, Washington D.C. |
| **[Zertia](http://www.zertia.es/index.php/novedades)**| Level 3 | Madrid |


## <a name="expressroute-system-integrators"></a>Integradores de sistemas ExpressRoute
A ativação da conectividade privada para atender as suas necessidades pode ser um desafio, com base na escala da sua rede. Pode trabalhar com qualquer um dos integradores de sistemas listados na seguinte tabela para ajudá-lo com a integração do ExpressRoute.

| **Integrador de sistemas** | **Continente** |
| --- | --- |
| **[Altogee](http://www.altogee.be/expressroute)** | Europa |
| **[Avanade Inc.](http://www.avanade.com/)** | Ásia, Europa, América do Norte, América do Sul |
| **[Bright Skies GmbH](http://bskies.io/expressroute)** | Europa
| **[Dotnet Solutions](http://www.dotnetsolutions.co.uk/)** | Europa |
| **[Serviços Profissionais Equinix](http://www.equinix.com/services/consulting/)** | América do Norte |
| **[The IT Consultancy Group](http://itconsult.com.au/microsoft-expressroute)** | Austrália |
| **[MSG Services](https://www.msg-services.de/it-services/managed-services/cloud-outsourcing/)** | Europa (Alemanha) |
| **[Nelite](http://nelite.com/)** | Europa |
| **[OneAs1a](http://www.oneas1a.com/express-connect-any-cloud-ecac)** | Ásia |
| **[Orange Networks](https://orange-networks.com/blog/88-azureexpressroute)** | Europa |
| **[Perficient](http://www.perficient.com/Partners/Microsoft/Cloud/Azure-ExpressRoute)** | América do Norte |
| **[Presidio](http://info.presidio.com/microsoft-azure-expressroute)** | América do Norte |
| **[Project Leadership](http://www.projectleadership.net/azure)** | América do Norte |
| **[sol-tec](http://www.sol-tec.com/Technologies)** | Europa |


## <a name="next-steps"></a>Passos seguintes
* Para obter mais informações acerca do ExpressRoute, veja as [FAQs do ExpressRoute](expressroute-faqs.md).
* Confirme que todos os pré-requisitos são cumpridos. Veja os [Pré-requisitos do ExpressRoute](expressroute-prerequisites.md).

<!--Image References-->
[0]: ./media/expressroute-locations/expressroute-locations-map.png "Mapa de localização"

