<properties
   pageTitle="Localizações do ExpressRoute | Microsoft Azure"
   description="Este artigo fornece uma descrição geral detalhada das localizações onde os serviços são oferecidos e como ligar a regiões do Azure."
   services="expressroute"
   documentationCenter="na"
   authors="cherylmc"
   manager="carmonm"
   editor="" />
<tags
   ms.service="expressroute"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="09/07/2016"
   ms.author="cherylmc" />

# Parceiros e localizações de peering do ExpressRoute 

As tabelas neste artigo fornecem informações sobre fornecedores de conectividade ExpressRoute, cobertura geográfica ExpressRoute, serviços em nuvem da Microsoft suportados no ExpressRoute e integradores de sistema ExpressRoute (SIs).

## <a name="partners"></a>Fornecedores de conectividade do ExpressRoute

O ExpressRoute é suportado em todas as regiões e localizações do Azure. O mapa seguinte fornece uma lista de regiões do Azure e localizações do ExpressRoute. As localizações do ExpressRoute mencionam os locais onde a Microsoft se junta a vários fornecedores de serviços.

![Mapa de localização][0]

Terá acesso aos serviços do Azure em todas as regiões numa região geopolítica se estiver ligado a, pelo menos, uma localização do ExpressRoute numa região geopolítica. A tabela seguinte fornece um mapa de regiões do Azure para localizações do ExpressRoute numa região geopolítica.

|**Região geopolítica**|**Regiões do Azure**|**Localizações do ExpressRoute**|
|---|---|---|
|**América do Norte**|EUA Leste, EUA Oeste, EUA Leste 2, EUA Central, EUA Centro-Sul, EUA Centro-Norte, Canadá Central, Canadá Leste|Atlanta, Chicago, Dallas, Las Vegas, Los Angeles, Nova Iorque, Seattle, Silicon Valley, Washington D.C., Montreal+, Cidade do Quebeque+, Toronto|
|**América do Sul**|Sul do Brasil|São Paulo|
|**Europa**|Europa do Norte, Europa Ocidental, Reino Unido Oeste, Reino Unido Sul|Amesterdão, Dublin, Londres, Newport (País de Gales)+, Paris|
|**Ásia**|Ásia Oriental, Sudeste Asiático|RAE de Hong Kong, Singapura|
|**Japão**|Oeste do Japão, Leste do Japão|Osaka, Tóquio|
|**Austrália**|Sudeste da Austrália, Leste da Austrália|Melbourne, Sydney|
|**Índia**|Índia Ocidental, Índia Central, Sul da Índia|Chennai, Mumbai|



A tabela abaixo fornece informações sobre regiões e limites geopolíticos para nuvens nacionais.

|**Região geopolítica**|**Regiões do Azure**|**Localizações do ExpressRoute**|
|---|---|---|---|
|**Nuvem do Governo dos EUA**|Gov (US) – Iowa, Gov (US) – Virginia|Chicago, Dallas + Nova Iorque, Washington D.C.|
|**China**|Norte da China, Leste da China|Pequim, Xangai|
|**Alemanha**|Alemanha Central, Leste da Alemanha|Berlim, Frankfurt|


A conectividade em regiões geopolíticas não é suportada no SKU do ExpressRoute standard. Terá de ativar o suplemento ExpressRoute Premium para suportar a conetividade global. A conectividade com ambientes em nuvem nacionais não é suportada. Pode trabalhar com o seu fornecedor de conectividade, se tal for necessário.


## Localizações do fornecedor de conectividade

> [AZURE.SELECTOR]
[Localizações por Fornecedor](expressroute-locations.md#connectivity-provider-locations)
[Fornecedores por Localização](expressroute-locations-providers.md#connectivity-provider-locations)

### Azure de Produção
| **Localização**  | **Fornecedores de Serviços** |
|---------------|-----------------------|
| **Amesterdão** | Aryaka Networks, AT&T NetBond, British Telecom, Colt, Equinix, euNetworks, GÉANT+, InterCloud, Internet Solutions - Cloud Connect, Interxion, Level 3 Communications, Orange, Tata Communications, TeleCity Group, Telenor, Verizon |
| **Atlanta** | Equinix |
| **Chennai** | Tata Communications |
| **Chicago** | AT&T NetBond, Comcast, Equinix, Level 3 Communications, Zayo Group |
| **Dallas** | AT&T NetBond, Equinix, Level 3 Communications, Megaport |
| **Dublin** | Colt |
| **RAE de Hong Kong** | British Telecom, China Telecom Global, Equinix, Megaport, Orange, PCCW Global Limited, Tata Communications, Verizon |
| **Londres** | AT&T NetBond, British Telecom, Colt, Equinix, InterCloud, Internet Solutions - Cloud Connect, Interxion, Jisc+, Level 3 Communications, MTN, NTT Communications, Orange, Tata Communications, Telecity Group, Telenor, Verizon, Vodafone |
| **Las Vegas** | Level 3 Communications+, Megaport+
| **Los Angeles** | CoreSite, Equinix, Megaport, NTT, Zayo Group |
| **Melbourne** | Equinix, Megaport, NEXTDC, Telstra Corporation |
| **Nova Iorque** | Equinix, Megaport, Zayo Group |
| **Montreal** | Cologix+ |
| **Mumbai** | Tata Communications |
| **Osaka** | Equinix, Internet Initiative Japan Inc. - IIJ, NTT Communications, Softbank |
| **Paris** | Interxion |
| **São Paulo** | Equinix, Telefonica |
| **Seattle** | Equinix, Level 3 Communications, Megaport |
| **Silicon Valley** | Aryaka Networks, AT&T NetBond, British Telecom, CenturyLink+, Comcast, Equinix, Level 3 Communications, Orange, Tata Communications, Verizon, Zayo Group |
| **Singapura** | Aryaka Networks, AT&T NetBond, British Telecom, Equinix, InterCloud, Megaport, Orange, SingTel, Tata Communications, Verizon |
| **Sydney** | AT&T NetBond, British Telecom, Equinix, Megaport, NEXTDC, Orange, Telstra Corporation, Verizon |
| **Tóquio** | Aryaka Networks, British Telecom, Colt, Equinix, Internet Initiative Japan Inc. - IIJ, NTT Communications, Softbank, Verizon |
| **Toronto** | Cologix, Equinix, Zayo Group |
| **Washington DC** | Aryaka Networks, AT&T NetBond, British Telecom, Comcast, Equinix, InterCloud, Level 3 Communications, Megaport, Orange, Tata Communications, Verizon, Zayo Group |

 **+** brevemente disponível

### Ambientes em nuvem nacionais

#### Nuvem do Governo dos EUA

| **Localização**  |**Fornecedores de Serviços** |
|---------------|--------------------|
| **Chicago** | AT&T NetBond, Equinix, Level 3 Communications, Verizon |
| **Dallas** |  Equinix+, Verizon+ |
| **Nova Iorque** | Equinix, Level 3 Communications+, Verizon |
| **Washington DC** | AT&T NetBond, Equinix, Level 3 Communications, Verizon |

#### China

| **Localização**  | **Fornecedores de Serviços** |
|---------------|-----------------------|
| **Pequim** | China Telecom |
| **Xangai** |  China Telecom |
Para saber mais, veja [ExpressRoute na China](http://www.windowsazure.cn/home/features/expressroute/)

#### Alemanha

| **Localização**  | **Fornecedores de Serviços** |
|---------------|-----------------------|
| **Berlim** | Colt+, e-shelter+ |
| **Frankfurt** | Colt, Equinix+, Interxion |

## <a name="nonpartners"></a>Conectividade através de fornecedores de serviços não listados

Se o seu fornecedor de conectividade não está listado nas secções anteriores, pode criar na mesma uma ligação.

- Consulte o seu fornecedor de conectividade para saber se este está ligado a alguma das trocas na tabela acima. Pode verificar as ligações seguintes para recolher mais informações sobre os serviços disponibilizados pelos fornecedores de troca. Vários fornecedores de conectividade já estão ligados às trocas de Ethernet.

    - [Nuvem Equinix Exchange](http://www.equinix.com/services/interconnection-connectivity/cloud-exchange/)
    - [TeleCity CloudIX](http://www.telecitygroup.com/colocation-services/cloud-ix.htm)
    - [InterXion](http://www.interxion.com/)
    - [NextDC](http://www.nextdc.com/)
    - [CoreSite](http://www.coresite.com/)
    - [Cologix](http://www.cologix.com/)
- Solicite ao seu fornecedor de conectividade para expandir a sua rede para a localização de peering que pretende.
    - Certifique-se de que o fornecedor de conectividade expande a sua conectividade para um estado de elevada disponibilidade, de forma a não existirem pontos únicos de falha.
- Organize um circuito ExpressRoute com a troca como o seu fornecedor de conectividade para se ligar à Microsoft.
    - Siga os passos em [Criar um circuito ExpressRoute](expressroute-howto-circuit-classic.md) para configurar a conectividade.

|**Localização**|**Troca**|**Fornecedor de conetividade**|
|-------------|------------|-------------------------|
| **Nova Iorque** | Equinix | Lightower |
| **Seattle** | Equinix | Alaska Communications |
| **Silicon Valley** | Equinix | XO Communications |
| **Singapura** | Equinix | 1CLOUDSTAR |
| **Washington DC** | Equinix | Lightower |

## Integradores de sistemas ExpressRoute

A ativação da conectividade privada para atender as suas necessidades pode ser um desafio, com base na escala da sua rede. Pode trabalhar com qualquer um dos integradores de sistemas listados na seguinte tabela para ajudá-lo com a integração do ExpressRoute.

|**Continente**|**Integradores de sistema**|
|-------------|---------------------|
| **Ásia** | Avanade Inc., OneAs1a|
| **Europa** | Avanade Inc., Dotnet Solutions|
| **EUA** | Avanade Inc., Equinix Professional Services, Perficient, Project Leadership|

## Passos seguintes

- Para obter mais informações acerca do ExpressRoute, veja as [FAQs do ExpressRoute](expressroute-faqs.md).
- Confirme que todos os pré-requisitos são cumpridos. Veja os [Pré-requisitos do ExpressRoute](expressroute-prerequisites.md).

<!--Image References-->
[0]: ./media/expressroute-locations/expressroute-locations-map.png "Mapa de localização"



<!--HONumber=sep16_HO1-->


