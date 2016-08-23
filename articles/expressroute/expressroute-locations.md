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
   ms.date="07/28/2016"
   ms.author="cherylmc" />

# Parceiros e localizações de peering do ExpressRoute 

As tabelas neste artigo fornecem informações sobre fornecedores de conectividade ExpressRoute, cobertura geográfica ExpressRoute, serviços em nuvem da Microsoft suportados no ExpressRoute e integradores de sistema ExpressRoute (SIs).

## <a name="partners"></a>Fornecedores de conectividade do ExpressRoute

O ExpressRoute é suportado em todas as regiões e localizações do Azure. O mapa seguinte fornece uma lista de regiões do Azure e localizações do ExpressRoute. As localizações do ExpressRoute mencionam os locais onde a Microsoft se junta a vários fornecedores de serviços.

![Mapa de localização][0]

Terá acesso aos serviços do Azure em todas as regiões numa região geopolítica se estiver ligado a, pelo menos, uma localização do ExpressRoute numa região geopolítica. A tabela seguinte fornece um mapa de regiões do Azure para localizações do ExpressRoute numa região geopolítica.

|**Região geopolítica**|**Regiões do Azure**|**Localizações do ExpressRoute**|
|---|---|---|
|**América do Norte**|EUA Leste, EUA Oeste, EUA Leste 2, EUA Central, EUA Centro-Sul, EUA Centro-Norte, Canadá Central, Canadá Leste|Atlanta, Chicago, Dallas, Las Vegas+, Los Angeles, Nova Iorque, Seattle, Silicon Valley, Washington D.C., Montreal+, Cidade do Quebeque+, Toronto|
|**América do Sul**|Sul do Brasil|São Paulo|
|**Europa**|Europa do Norte, Europa Ocidental|Amesterdão, Dublin, Londres, Newport (País de Gales)+, Paris|
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

| **Fornecedor de serviços**  |**Microsoft Azure** | **Office 365 e CRM Online** | **Localizações** |
|-----------------------|--------------------|----------------|---------------|
| **[Redes Aryaka]( http://www.aryaka.com/)** | Suportado | Suportado | Amesterdão, Silicon Valley, Singapura, Tóquio, Washington D.C. |
| **[AT & T NetBond]( https://www.synaptic.att.com/clouduser/html/productdetail/ATT_NetBond.htm)** | Suportado | Suportado | Amesterdão, Chicago, Dallas, Londres, Silicon Valley, Singapura, Sydney, Washington D.C. |
| **[British Telecom]( http://www.globalservices.bt.com/uk/en/news/bt_to_provide_connectivity_to_microsoft_azure)** | Suportado | Suportado | Amesterdão, RAE de Hong Kong, Londres, Silicon Valley, Singapura, Sydney, Tóquio, Washington D.C. |
|**CenturyLink** | Brevemente | Brevemente| Silicon Valley |
|**China Telecom Global** | Suportado | Não suportado | RAE de Hong Kong |
|**[Cologix](http://www.cologix.com/solutions/cloud-connect/public-clouds/microsoft-cloud/)** | Suportado | Brevemente | Montreal +, Toronto |
| **[Colt]( http://www.colt.net/uk/en/news/colt-announces-dedicated-cloud-access-for-microsoft-azure-services-en.htm)**  |  Suportado | Suportado | Amesterdão, Dublin, Londres, Tóquio |
| **Comcast** | Suportado | Suportado | Chicago, Silicon Valley, Washington D.C. |
| **[CoreSite](http://www.coresite.com/solutions/cloud-services/public-cloud-providers/microsoft-azure-expressroute)** | Suportado | Suportado | Los Angeles | 
| **[Equinix](http://www.equinix.com/partners/microsoft-azure/)** | Suportado | Suportado | Amesterdão, Atlanta, Chicago, Dallas, RAE de Hong Kong, Londres, Los Angeles, Melbourne, Nova Iorque, Osaka, são Paulo, Seattle, Silicon Valley, Singapura, Sydney, Tóquio, Toronto, Washington D.C. |
| **euNetworks** |  Suportado | Suportado | Amesterdão |
| **GÉANT** | Brevemente | Brevemente | Amesterdão + |
| **[Internet Initiative Japan Inc. – IIJ](http://www.iij.ad.jp/en/news/pressrelease/2015/1216-2.html)** |  Suportado | Suportado | Osaka, Tóquio |
| **[InterCloud]( https://www.intercloud.com/)** | Suportado | Suportado | Amesterdão, Londres, Singapura, Washington D.C. |
| **Internet Solutions – Cloud Connect** | Suportado | Suportado | Amesterdão, Londres |
| **Interxion** | Suportado | Suportado | Amesterdão, Londres, Paris |
| **Jisc** | Brevemente | Brevemente | Londres+ | 
| **[Comunicações de nível 3]( http://your.level3.com/LP=882?WT.tsrc=02192014LP882AzureVanityAzureText)** | Suportado | Suportado | Amesterdão, Chicago, Dallas, Las Vegas+, Londres, Seattle, Silicon Valley, Washington D.C. |
| **Megaport** | Suportado | Suportado | Dallas, Hong Kong, Las Vegas+, Los Angeles, Melbourne, Nova Iorque, Seattle, Singapura, Sydney, Washington D.C. |
| **MTN** | Suportado | Suportado | Londres |
| **NEXTDC** | Suportado | Suportado | Melbourne, Sydney |
| **Comunicações NTT** | Suportado | Suportado | Londres, Osaka, Tóquio |
| **[Orange]( http://www.orange-business.com/en/products/business-vpn-galerie)** | Suportado | Suportado | Amesterdão, RAE de Hong Kong, Londres, Silicon Valley, Singapura, Washington D.C. |
| **PCCW Global Limited** | Suportado | Suportado | RAE de Hong Kong |
| **[SingTel]( http://info.singtel.com/about-us/news-releases/singtel-provide-secure-private-access-microsoft-azure-public-cloud)** |  Suportado | Suportado | Singapura |
| **Softbank** | Suportado | Suportado | Osaka, Tóquio | 
| **[Tata Communications](http://www.tatacommunications.com/lp/izo/azure/azure_index.html)** | Suportado | Suportado | Amesterdão, Chennai, RAE de Hong Kong, Londres, Mumbai, Silicon Valley, Singapura, Washington D.C. |
| **[TeleCity Group]( http://www.telecitygroup.com/investor-centre/news_details.htm?locid=03100500400b00d&xml)** | Suportado | Suportado | Amesterdão, Londres |
| **Telefonica** | Brevemente | Brevemente | São Paulo+ |
| **Telenor** | Suportado | Suportado | Amesterdão, Londres |
| **[Telstra Corporation]( http://www.telstra.com.au/business-enterprise/network-services/networks/cloud-direct-connect/)** | Suportado | Brevemente | Melbourne, Sydney |
| **[Verizon](http://www.verizonenterprise.com/products/networking/secure-cloud-interconnect/)** | Suportado | Suportado | Amesterdão, RAE de Hong Kong, Londres, Silicon Valley, Singapura, Sydney, Tóquio, Washington D.C. |
| **Vodafone** | Suportado | Não suportado | Londres | 
| **[Zayo Group]( http://www.zayo.com/solutions/industries/connect-to-cloud-data-centers/cloud-connectivity/microsoft-expressroute/)** | Suportado | Suportado | Chicago, Los Angeles, Nova Iorque, Silicon Valley, Toronto, Washington D.C. |

 **+** brevemente disponível

### Ambientes em nuvem nacionais

#### Nuvem do Governo dos EUA

| **Fornecedor de serviços**  |**Microsoft Azure** | **Office 365** | **Localizações** |
|-----------------------|--------------------|----------------|---------------|
| **[AT & T NetBond]( https://www.synaptic.att.com/clouduser/html/productdetail/ATT_NetBond.htm)** | Suportado | Suportado | Chicago, Washington D.C. |
| **[Equinix](http://www.equinix.com/partners/microsoft-azure/)** | Suportado | Suportado | Chicago, Dallas + Nova Iorque, Washington D.C. |
| **[Comunicações de nível 3]( http://your.level3.com/LP=882?WT.tsrc=02192014LP882AzureVanityAzureText)** | Suportado | Brevemente | Chicago, Nova Iorque+, Washington D.C. |
| **[Verizon](http://news.verizonenterprise.com/2014/04/secure-cloud-interconnect-solutions-enterprise/)** | Suportado | Suportado | Chicago, Dallas + Nova Iorque, Washington D.C. |

#### China

| **Fornecedor de serviços**  |**Microsoft Azure** | **Office 365** | **Localizações** |
|-----------------------|--------------------|----------------|---------------|
| **China Telecom** | Suportado | Não suportado | Pequim, Xangai|
Para saber mais, veja [ExpressRoute na China](http://www.windowsazure.cn/home/features/expressroute/).

#### Alemanha

| **Fornecedor de serviços**  |**Microsoft Azure** | **Office 365** | **Localizações** |
|-----------------------|--------------------|----------------|---------------|
| **[Colt]( http://www.colt.net/uk/en/news/colt-announces-dedicated-cloud-access-for-microsoft-azure-services-en.htm)** | Suportado | Não suportado | Berlim+, Frankfurt|
| **[Equinix](http://www.equinix.com/partners/microsoft-azure/)** | Brevemente | Não suportado | Frankfurt+|
| **e-shelter** | Brevemente | Não suportado | Berlim+|
| **Interxion** | Suportado | Não suportado | Frankfurt|

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

|**Fornecedor de conectividade**|**Troca**|**Localizações**|
|---|---|---|
|**[1CLOUDSTAR](http://www.1cloudstar.com/service/cloudconnect-azure-expressroute/)**|Equinix|Singapura|
|**Alaska Communications**|Equinix|Seattle|
|**[Lightower](http://www.lightower.com/network-solutions/cloud-connect/#microsoft-azure )**|Equinix|Nova Iorque, Washington D.C.|
|**[XO Communications](http://www.xo.com/)**|Equinix|Silicon Valley|


## Integradores de sistemas ExpressRoute

A ativação da conectividade privada para atender as suas necessidades pode ser um desafio, com base na escala da sua rede. Pode trabalhar com qualquer um dos integradores de sistemas listados na seguinte tabela para ajudá-lo com a integração do ExpressRoute.

|**Integrador de sistema**|**Continente**|
|---|---|
|**[Avanade Inc.](http://www.avanade.com/)**| Ásia, Europa, EUA |
|**[Dotnet Solutions](http://www.dotnetsolutions.co.uk/)**| Europa |
|**[Serviços Profissionais Equinix](http://www.equinix.com/services/consulting/)**|EUA|
|**[OneAs1a](http://www.oneas1a.com/express-connect-any-cloud-ecac)** | Ásia |
|**[Perficient](http://www.perficient.com/Partners/Microsoft/Cloud/Azure-ExpressRoute)** | EUA |
|**[Project Leadership](http://www.projectleadership.net/azure)** | EUA |

## Passos seguintes

- Para obter mais informações acerca do ExpressRoute, veja as [FAQs do ExpressRoute](expressroute-faqs.md).
- Confirme que todos os pré-requisitos são cumpridos. Veja os [Pré-requisitos do ExpressRoute](expressroute-prerequisites.md).

<!--Image References-->
[0]: ./media/expressroute-locations/expressroute-locations-map.png "Mapa de localização"



<!--HONumber=Aug16_HO1-->


