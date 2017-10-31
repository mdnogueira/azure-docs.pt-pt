---
title: Requisitos de encaminhamento do Azure ExpressRoute | Microsoft Docs
description: "Esta página apresenta os requisitos detalhados para configurar e gerir o encaminhamento para circuitos do ExpressRoute."
documentationcenter: na
services: expressroute
author: osamazia
manager: ganesr
editor: 
ms.assetid: 5b382e79-fa3f-495a-a764-c5ff86af66a2
ms.service: expressroute
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/31/2017
ms.author: osamam
ms.openlocfilehash: a7d1e177e08d37913afa3cb203f0e4085c171f70
ms.sourcegitcommit: ccb84f6b1d445d88b9870041c84cebd64fbdbc72
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/14/2017
---
# <a name="expressroute-routing-requirements"></a>Requisitos de encaminhamento do ExpressRoute
Para ligar aos serviços em nuvem da Microsoft com o ExpressRoute, terá de configurar e gerir o encaminhamento. Alguns fornecedores de conectividade oferecem a configuração e a gestão do encaminhamento como um serviço gerido. Contacte o seu fornecedor de conectividade para ver se oferece este serviço. Caso contrário, terá de cumprir os seguintes requisitos:

Veja o artigo [Circuits and routing domains (Circuitos e domínios de encaminhamento)](expressroute-circuit-peerings.md) para obter uma descrição das sessões de encaminhamento que têm de ser configuradas para facilitar a conectividade.

> [!NOTE]
> A Microsoft não suporta protocolos de redundância do router (por exemplo, HSRP, VRRP) para configurações de elevada disponibilidade. Precisamos de um par de sessões BGP redundante por peering para elevada disponibilidade.
> 
> 

## <a name="ip-addresses-used-for-peerings"></a>Endereços IP utilizados para peerings
Tem de reservar alguns blocos de endereços IP para configurar o encaminhamento entre a sua rede e os routers Enterprise Edge da Microsoft (MSEEs). Esta secção fornece uma lista de requisitos e descreve as regras relativas ao modo como estes endereços IP têm de ser obtidos e utilizados.

### <a name="ip-addresses-used-for-azure-private-peering"></a>Endereços IP utilizados para peering privado do Azure
Pode utilizar endereços IP privados ou endereços IP públicos para configurar os peerings. O intervalo de endereços utilizado para configurar as rotas não pode sobrepor-se aos intervalos de endereços utilizados para criar redes virtuais no Azure. 

* Tem de reservar uma sub-rede /29 ou duas sub-redes /30 para as interfaces de encaminhamento.
* As sub-redes utilizadas para encaminhamento podem ser endereços IP privados ou endereços IP públicos.
* As sub-redes não podem estar em conflito com o intervalo reservado pelo cliente para utilização no Microsoft Cloud.
* Se for utilizada uma sub-rede /29, está dividida em duas sub-redes /30. 
  * A primeira sub-rede /30 é utilizada para a ligação primária e a segunda sub-rede /30 é utilizada para a ligação secundária.
  * Para cada uma das sub-redes /30, tem de utilizar o primeiro endereço IP da sub-rede /30 do router. A Microsoft utiliza o segundo endereço IP da sub-rede /30 para configurar uma sessão de BGP.
  * É necessário configurar as duas sessões de BGP para que o nosso [SLA de disponibilidade](https://azure.microsoft.com/support/legal/sla/) seja válido.  

#### <a name="example-for-private-peering"></a>Exemplo de peering privado
Se optar por utilizar a.b.c.d/29 para configurar o peering, este está dividido em duas sub-redes /30. No exemplo abaixo, vemos como é utilizada a sub-rede a.b.c.d/29. 

A a.b.c.d/29 está dividida em a.b.c.d/30 e a.b.c.d+4/30 e transmitida à Microsoft através de APIs de aprovisionamento. Utilize a a.b.c.d+1 como IP VRF do PE primário e a Microsoft consumirá a a.b.c.d+2 como IP VRF do MSEE primário. Utilize a a.b.c.d+5 como IP VRF do PE secundário e a Microsoft consumirá a a.b.c.d+6 como IP VRF do MSEE secundário.

Considere o caso em que selecionou 192.168.100.128/29 para configurar o peering privado. 192.168.100.128/29 inclui os endereços de 192.168.100.128 a 192.168.100.135, dos quais:

* o 192.168.100.128/30 será atribuído ao link1, com o fornecedor a utilizar o 192.168.100.129 e a Microsoft a utilizar o 192.168.100.130.
* o 192.168.100.132/30 será atribuído ao link2, com o fornecedor a utilizar o 192.168.100.133 e a Microsoft a utilizar o 192.168.100.134.

### <a name="ip-addresses-used-for-azure-public-peering"></a>Endereços IP utilizados para peering público do Azure
Tem de utilizar endereços IP públicos da sua propriedade para configurar as sessões de BGP. A Microsoft tem de poder verificar a propriedade dos endereços IP através dos Registos de Internet de Encaminhamento e dos Registos de Encaminhamento de Internet. 

* Tem de utilizar uma sub-rede /29 exclusiva ou duas sub-redes /30 para configurar o peering de BGP para cada peering por circuito do ExpressRoute (se tiver mais do que um). 
* Se for utilizada uma sub-rede /29, está dividida em duas sub-redes /30. 
  * A primeira sub-rede /30 é utilizada para a ligação primária e a segunda sub-rede /30 é utilizada para a ligação secundária.
  * Para cada uma das sub-redes /30, tem de utilizar o primeiro endereço IP da sub-rede /30 do router. A Microsoft utiliza o segundo endereço IP da sub-rede /30 para configurar uma sessão de BGP.
  * É necessário configurar as duas sessões de BGP para que o nosso [SLA de disponibilidade](https://azure.microsoft.com/support/legal/sla/) seja válido.

### <a name="ip-addresses-used-for-microsoft-peering"></a>Endereços IP utilizados para peering da Microsoft
Tem de utilizar endereços IP públicos da sua propriedade para configurar as sessões de BGP. A Microsoft tem de poder verificar a propriedade dos endereços IP através dos Registos de Internet de Encaminhamento e dos Registos de Encaminhamento de Internet.

* Tem de utilizar uma sub-rede /29 (IPv4) ou /125 (IPv6) exclusiva ou duas sub-redes /30 (IPv4) ou /126 (IPv6) sub-redes para configurar o peering de BGP para cada peering por circuito ExpressRoute (se tiver mais do que um).
* Se for utilizada uma sub-rede /29, está dividida em duas sub-redes /30.
* A primeira sub-rede /30 é utilizada para a ligação primária e a segunda sub-rede /30 será utilizada para a ligação secundária.
* Para cada uma das sub-redes /30, tem de utilizar o primeiro endereço IP da sub-rede /30 do router. A Microsoft utiliza o segundo endereço IP da sub-rede /30 para configurar uma sessão de BGP.
* Se for utilizada uma sub-rede /125, está dividida em duas sub-redes /126.
* A primeira sub-rede /126 é utilizada para a ligação primária e a segunda sub-rede /126 será utilizada para a ligação secundária.
* Para cada uma das sub-redes /126, tem de utilizar o primeiro endereço IP da sub-rede /126 do router. A Microsoft utiliza o segundo endereço IP da sub-rede /126 para configurar uma sessão de BGP.
* É necessário configurar as duas sessões de BGP para que o nosso [SLA de disponibilidade](https://azure.microsoft.com/support/legal/sla/) seja válido.

## <a name="public-ip-address-requirement"></a>Requisito de endereço IP público

### <a name="private-peering"></a>Peering privado
Pode optar por utilizar endereços IPv4 públicos ou privados para o peering privado. Oferecemos um isolamento do tráfego ponto a ponto, de modo a que a sobreposição de endereços com outros clientes não seja possível em caso de peering privado. Estes endereços não são anunciados na Internet. 


### <a name="public-peering"></a>Peering público
O caminho de peering público Azure permite-lhe ligar a todos os serviços alojados no Azure ao longo dos respetivos endereços IP públicos. Estes incluem os serviços listados nas [FAQ do ExpressRoute](expressroute-faqs.md) e quaisquer serviços alojados pelos ISVs no Microsoft Azure. A conectividade aos serviços do Microsoft Azure em peering público é sempre iniciada a partir da sua rede para a rede da Microsoft. Tem de utilizar endereços IP Públicos para o tráfego destinado à rede da Microsoft.

> [!IMPORTANT]
> Todos os serviços do Azure PaaS também estão acessíveis através do peering da Microsoft. Recomendamos a criação do peering da Microsoft e ligação aos serviços PaaS do Azure através do peering da Microsoft.  
>   


É permitido um Número AS Privado com Peering Público.

### <a name="microsoft-peering"></a>Peering da Microsoft
O caminho de peering da Microsoft permite-lhe ligar aos serviços em nuvem da Microsoft que não são suportados através do caminho de peering público do Azure. A lista de serviços inclui os serviços do Office 365, como o Exchange Online, SharePoint Online, Skype para Empresas e o Dynamics 365. A Microsoft suporta a conetividade bidirecional no peering da Microsoft. O tráfego destinado aos serviços em nuvem da Microsoft tem de utilizar endereços IPv4 públicos válidos antes de serem introduzidos na rede da Microsoft.

Certifique-se de que o endereço IP e o número AS estão registados em seu nome num dos registos seguintes:


* [ARIN](https://www.arin.net/)
* [APNIC](https://www.apnic.net/)
* [AFRINIC](https://www.afrinic.net/)
* [LACNIC](http://www.lacnic.net/)
* [RIPENCC](https://www.ripe.net/)
* [RADB](http://www.radb.net/)
* [ALTDB](http://altdb.net/)

Se os prefixos e o número SA não estiverem atribuídos a si nos registos anteriores, terá de abrir um pedido de suporte para a validação manual dos seus prefixos e ASN. O suporte requer documentação, como uma Carta de Autorização, que prova que está autorizado a utilizar os recursos.

É permitido um Número AS privado com Peering da Microsoft, mas também irá precisar de validação manual.

> [!IMPORTANT]
> Os endereços IP públicos anunciados à Microsoft através do ExpressRoute não devem ser anunciados à Internet. Tal poderá interromper a conectividade a outros serviços Microsoft. No entanto, os endereços IP Públicos utilizados pelos servidores da rede que comunicam com pontos finais do O365 dentro da Microsoft poderão ser anunciados através do ExpressRoute. 
> 
> 

## <a name="dynamic-route-exchange"></a>Troca de rotas dinâmicas
A troca do encaminhamento será feita através do protocolo eBGP. São estabelecidas sessões de EBGP entre os MSEEs e os seus routers. A autenticação das sessões de BGP não é um requisito. Se necessário, pode ser configurado um hash MD5. Veja [Configurar encaminhamento](expressroute-howto-routing-classic.md) e [Fluxos de trabalho de aprovisionamento dos circuitos e estados dos circuitos](expressroute-workflows.md) para obter informações sobre como configurar sessões de BGP.

## <a name="autonomous-system-numbers"></a>Números de Sistema Autónomos
A Microsoft utiliza AS 12076 para o peering público do Azure, o peering privado do Azure e o peering da Microsoft. Reservamos os ASNs do 65515 ao 65520 para utilização interna. São suportados números AS de 16 e de 32 bits.

Não há requisitos quanto à simetria da transferência de dados. Os caminhos de reencaminhamento e do remetente podem atravessar pares de routers diferentes. As rotas idênticas têm de ser anunciadas nos dois lados nos vários pares de circuito que lhe pertençam. Não é necessário que a métrica das rotas seja idêntica.

## <a name="route-aggregation-and-prefix-limits"></a>Agregação de rotas e limites de prefixo
Suportamos até 4000 prefixos que nos sejam anunciados através do peering privado do Azure. Poderá ser aumentado até 10 000 prefixos se o suplemento premium do ExpressRoute estiver ativado. Aceitamos até 200 prefixos por sessão de BGP para o peering público do Azure e o peering da Microsoft. 

A sessão de BGP é ignorada se o número de prefixos exceder o limite. Só aceitamos rotas predefinidas na ligação do peering privado. O fornecedor tem de filtrar a rota predefinida e os endereços IP privados (RFC 1918) nos caminhos do peering público do Azure e do peering da Microsoft. 

## <a name="transit-routing-and-cross-region-routing"></a>Encaminhamento de trânsito e encaminhamento por várias regiões
Não é possível encaminhar o ExpressRoute como router de trânsito. Precisará do seu fornecedor de conectividade para os serviços de encaminhamento de trânsito.

## <a name="advertising-default-routes"></a>Anunciar rotas predefinidas
As rotas predefinidas só são permitidas em sessões do peering privado do Azure. Numa situação dessas, encaminharemos para a sua rede todo o tráfego das redes virtuais associadas. O anúncio de rotas predefinidas no peering privado resultará no bloqueio do caminho da Internet a partir do Azure. Precisará da sua periferia empresarial para encaminhar o tráfego de e para a Internet para serviços alojados no Azure. 

 Para ativar a conectividade para outros serviços do Azure e outros serviços de infraestrutura, tem de se certificar de que implementou um dos seguintes itens:

* O peering público Azure está ativado para encaminhar o tráfego para pontos finais públicos
* Utiliza o encaminhamento definido pelo utilizador para permitir a ligação à Internet para cada sub-rede que necessite de ligação à Internet.

> [!NOTE]
> O anúncio de rotas predefinidas interrompe a ativação de licenças do Windows e de outras licenças de VMs. Siga as instruções [aqui](http://blogs.msdn.com/b/mast/archive/2015/05/20/use-azure-custom-routes-to-enable-kms-activation-with-forced-tunneling.aspx) para contornar este problema.
> 
> 

## <a name="bgp"></a>Suporte para comunidades de BGP
Esta secção apresenta uma descrição geral de como as comunidades de BGP serão utilizadas com o ExpressRoute. A Microsoft anuncia as rotas nos caminhos do peering público e do peering da Microsoft com rotas etiquetadas com valores de comunidade apropriados. A lógica por detrás disto e os detalhes sobre os valores das comunidades são descritos abaixo. A Microsoft não irá, no entanto, honrar os valores das comunidades marcadas em rotas anunciadas para a Microsoft.

Se estiver a ligar à Microsoft através do ExpressRoute numa localização de peering dentro de uma região geopolítica, terá acesso a todos os serviços em nuvem da Microsoft em todas as regiões dentro dos limites geopolíticos. 

Por exemplo, se ligar à Microsoft em Amesterdão através do ExpressRoute, terá acesso a todos os serviços em nuvem da Microsoft alojados na Europa do Norte e na Europa Ocidental. 

Veja a página [Parceiros e localizações de peering do ExpressRoute ](expressroute-locations.md) para obter uma lista detalhada das regiões geopolíticas, das regiões do Azure associadas e das localizações de peering do ExpressRoute correspondentes.

Pode comprar mais do que um circuito do ExpressRoute por região geopolítica. Ter várias ligações oferece vantagens significativas de elevada disponibilidade, devido à georredundância. Caso tenha vários circuitos do ExpressRoute, receberá o mesmo conjunto de prefixos anunciados da Microsoft nos caminhos do peering público e do peering da Microsoft, o que significa que terá vários caminhos da sua rede para a Microsoft. Esta situação pode, potencialmente, levar a decisões de encaminhamento inferiores às ideais na sua rede. Sendo assim, poderá ter experiências de conectividade aos diferentes serviços inferiores às ideais. Também pode utilizar os valores das comunidades para tomar decisões de encaminhamento adequadas para oferecer [encaminhamento ideal aos utilizadores](expressroute-optimize-routing.md).

| **Região do Microsoft Azure** | **Valor da comunidade BGP** |
| --- | --- |
| **América do Norte** | |
| EUA Leste | 12076:51004 |
| EUA Leste 2 | 12076:51005 |
| EUA Oeste | 12076:51006 |
| EUA Oeste 2 | 12076:51026 |
| EUA Centro-Oeste | 12076:51027 |
| EUA Centro-Norte | 12076:51007 |
| EUA Centro-Sul | 12076:51008 |
| EUA Central | 12076:51009 |
| Canadá Central | 12076:51020 |
| Leste do Canadá | 12076:51021 |
| **América do Sul** | |
| Sul do Brasil | 12076:51014 |
| **Europa** | |
| Europa do Norte | 12076:51003 |
| Europa Ocidental | 12076:51002 |
| Reino Unido Sul | 12076:51024 |
| Reino Unido Oeste | 12076:51025 |
| **Ásia-Pacífico** | |
| Ásia Oriental | 12076:51010 |
| Sudeste Asiático | 12076:51011 |
| **Japão** | |
| Leste do Japão | 12076:51012 |
| Oeste do Japão | 12076:51013 |
| **Austrália** | |
| Leste da Austrália | 12076:51015 |
| Sudeste da Austrália | 12076:51016 |
| **Índia** | |
| Índia do Sul | 12076:51019 |
| Índia Ocidental | 12076:51018 |
| Índia Central | 12076:51017 |
| **Coreia** | |
| Coreia do Sul | 12076:51028 |
| Coreia Central | 12076:51029 |


Todas as rotas anunciadas a partir da Microsoft serão etiquetadas com o valor da comunidade adequado. 

> [!IMPORTANT]
> Os prefixos globais são etiquetados com um valor da comunidade adequado e serão anunciados apenas quando o suplemento premium do ExpressRoute estiver ativado.
> 
> 

Para além do que foi dito acima, a Microsoft também marcará prefixos baseados no serviço a que pertencem. Este caso aplica-se apenas ao peering da Microsoft. A tabela abaixo fornece um mapeamento de serviço para o valor das comunidades de BGP.

| **Serviço** | **Valor da comunidade BGP** |
| --- | --- |
| Exchange Online | 12076:5010 |
| SharePoint Online | 12076:5020 |
| Skype para Empresas Online | 12076:5030 |
| Dynamics 365 | 12076:5040 |
| Outros serviços do Office 365 Online | 12076:5100 |

> [!NOTE]
> A Microsoft não honra valores das comunidades de BGP definidos por si nas rotas anunciadas para a Microsoft.
> 
> 

### <a name="bgp-community-support-in-national-clouds-preview"></a>Suporte da Comunidade BGP nas Clouds Nationais (Pré-visualização)

| **Região do Azure de Clouds Nacionais**| **Valor da comunidade BGP** |
| --- | --- |
| **Governo dos Estados Unidos da América** |  |
| Gov (US) - Arizona | 12076:51106 |
| Gov (US) - Iowa | 12076:51109 |
| Gov (US) - Virginia | 12076:51105 |
| Gov (US) - Texas | 12076:51108 |
| US DoD Centro | 12076:51209 |
| US DoD Leste | 12076:51205 |


| **Serviço nas Clouds Nacionais** | **Valor da comunidade BGP** |
| --- | --- |
| **Governo dos Estados Unidos da América** |  |
| Exchange Online |12076:5110 |
| SharePoint Online |12076:5120 |
| Skype para Empresas Online |12076:5130 |
| Dynamics 365 |12076:5140 |
| Outros serviços do Office 365 Online |12076:5200 |

## <a name="next-steps"></a>Passos seguintes
* Configurar a ligação do ExpressRoute.
  
  * [Criar e modificar um circuito](expressroute-howto-circuit-arm.md)
  * [Criar e modificar a configuração de peering](expressroute-howto-routing-arm.md)
  * [Ligar uma VNet a um circuito do ExpressRoute](expressroute-howto-linkvnet-arm.md)