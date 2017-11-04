---
title: "Azure circuitos ExpressRoute e domínios de encaminhamento | Microsoft Docs"
description: "Esta página fornece uma descrição geral dos circuitos ExpressRoute e domínios de encaminhamento."
documentationcenter: na
services: expressroute
author: cherylmc
manager: timlt
editor: 
ms.assetid: 6f0c5d8e-cc60-4a04-8641-2c211bda93d9
ms.service: expressroute
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/30/2017
ms.author: ganesr,cherylmc
ms.openlocfilehash: c8f3c0e87a052b327e9949acd3e7db1d28c1eb46
ms.sourcegitcommit: 804db51744e24dca10f06a89fe950ddad8b6a22d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/30/2017
---
# <a name="expressroute-circuits-and-routing-domains"></a>Circuitos ExpressRoute e domínios de encaminhamento
 Deve ordenar um *circuito ExpressRoute* para ligar a sua infraestrutura no local para a Microsoft através de um fornecedor de conectividade. A figura seguinte mostra uma representação lógica da conetividade entre a sua WAN e a Microsoft.

![](./media/expressroute-circuit-peerings/expressroute-basic.png)

## <a name="expressroute-circuits"></a>Circuitos ExpressRoute
Um *circuito ExpressRoute* representa uma ligação lógica entre a sua infraestrutura no local e os cloud services da Microsoft através de um fornecedor de conectividade. Pode ordenar vários circuitos do ExpressRoute. Cada circuito pode estar em regiões do idêntica ou diferentes e pode ser ligado ao local através de fornecedores de conectividade diferentes. 

Circuitos ExpressRoute não mapeiam para qualquer entidades físicas. Um circuito é identificado de forma exclusiva por uma norma que GUID denominado como uma chave de serviço (s-chave). A chave de serviço é a única informação de informações trocadas entre a Microsoft, o fornecedor de conectividade e o utilizador. A chave de s não é um segredo por motivos de segurança. Não há um mapeamento de 1:1 entre um circuito ExpressRoute e a chave de s.

Um circuito ExpressRoute pode ter até três peerings independentes: privada pública, Azure do Azure e da Microsoft. Cada peering é um par de BGP independente sessões, cada um deles redundantly configurada para elevada disponibilidade. Há um 1:N (1 < = N < = 3) mapeamento entre um circuito ExpressRoute e domínios de encaminhamento. Um circuito ExpressRoute pode ter qualquer um, dois ou todos os três peerings ativados por circuito ExpressRoute.

Cada circuito tem uma largura de banda fixa (50 Mbps, 100 Mbps, 200 Mbps, 500 Mbps, 1 Gbps, 10 Gbps) e está mapeado para um fornecedor de conectividade e uma localização de peering. A largura de banda que selecionar é partilhada entre todos os peerings para o circuito. 

### <a name="quotas-limits-and-limitations"></a>Quotas, os limites e limitações
Quotas predefinidas e limites aplicam-se para cada circuito do ExpressRoute. Consulte o [subscrição do Azure e limites de serviço, Quotas e restrições](../azure-subscription-service-limits.md) página para obter informações atualizadas sobre quotas.

## <a name="expressroute-routing-domains"></a>Domínios de encaminhamento do ExpressRoute
Um circuito ExpressRoute tem diversos domínios de encaminhamento com associados: privada pública, Azure do Azure e da Microsoft. Cada um dos domínios de encaminhamento está configurada de forma idêntica num par de routers (ativo-ativo ou partilha de carga configuração) para elevada disponibilidade. Serviços do Azure são categorizados como *público do Azure* e *privado do Azure* para representar o esquemas de endereçamento IP.

![](./media/expressroute-circuit-peerings/expressroute-peerings.png)

### <a name="azure-private-peering"></a>Privado do Azure peering
Serviços, nomeadamente as máquinas virtuais (IaaS) de computação do Azure e serviços de cloud (PaaS), que são implementados numa rede virtual podem ser ligados através do domínio de peering privado. O domínio de peering privado é considerado uma extensão fidedigna da sua rede principal para o Microsoft Azure. Pode configurar a conectividade bidirecional entre a rede principal e redes virtuais do Azure (VNets). Este peering permite-lhe ligar a máquinas virtuais e serviços diretamente nos respetivos endereços IP privados em nuvem.  

Pode ligar mais de uma rede virtual para o domínio de peering privado. Reveja o [página de FAQ](expressroute-faqs.md) para obter informações sobre limites e limitações. Pode visitar o [subscrição do Azure e limites de serviço, Quotas e restrições](../azure-subscription-service-limits.md) página para obter informações atualizadas nos limites especificados.  Consulte o [encaminhamento](expressroute-routing.md) página para obter informações detalhadas sobre a configuração de encaminhamento.

### <a name="azure-public-peering"></a>Peering público do Azure

> [!IMPORTANT]
> Todos os serviços do Azure PaaS também estão acessíveis através do peering da Microsoft. Recomendamos a criação do peering da Microsoft e ligação aos serviços PaaS do Azure através do peering da Microsoft.  
>   


Os serviços, tais como Web sites, bases de dados SQL e Storage do Azure são disponibilizados em endereços IP públicos. Em privado pode ligar a serviços alojados em endereços IP públicos, incluindo VIPs dos seus serviços em nuvem, através do domínio de encaminhamento de peering público. Pode ligar o domínio de peering público à sua rede de Perímetro e ligar a todos os serviços do Azure dos respetivos endereços IP públicos da sua WAN sem terem de se ligar através da internet. 

Conectividade é sempre iniciada a partir da sua WAN para serviços do Microsoft Azure. Serviços do Microsoft Azure não será possível iniciar ligações na sua rede através de domínio de encaminhamento. Depois de peering público é ativado, pode ligar a todos os serviços do Azure. Não podemos permitem-lhe seletivamente escolha para a qual podemos anunciar rotas para os serviços.

Pode definir filtros de rotas personalizadas dentro da sua rede para consumir apenas as rotas que terá. Consulte o [encaminhamento](expressroute-routing.md) página para obter informações detalhadas sobre a configuração de encaminhamento. 

Consulte o [página de FAQ](expressroute-faqs.md) para obter mais informações sobre serviços suportada através do domínio de encaminhamento de peering público. 

### <a name="microsoft-peering"></a>Peering da Microsoft
[!INCLUDE [expressroute-office365-include](../../includes/expressroute-office365-include.md)]

Conectividade a todos os outros Microsoft online services (serviços do Office 365, o Dynamics 365 e o Azure PaaS) é através do peering da Microsoft. Iremos ativar a conectividade bidirecional entre os seus serviços em nuvem WAN e a Microsoft através do domínio de encaminhamento de peering de Microsoft. Tem de ligar aos serviços em nuvem da Microsoft apenas através de endereços IP públicos que pertencem ao utilizador ou o fornecedor de conectividade e terá de cumprir todas as regras definidas. Consulte o [pré-requisitos do ExpressRoute](expressroute-prerequisites.md) página para obter mais informações.

Consulte o [página de FAQ](expressroute-faqs.md) para obter mais informações sobre serviços suportados, os custos e detalhes de configuração. Consulte o [localizações do ExpressRoute](expressroute-locations.md) página para obter informações sobre a lista de fornecedores de conectividade oferta suporte de peering da Microsoft.

## <a name="routing-domain-comparison"></a>Comparação de domínio de encaminhamento
A tabela seguinte compara os três domínios de encaminhamento:

|  | **Peering privado** | **Peering público** | **Peering da Microsoft*** |
| --- | --- | --- | --- |
| **Máx. prefixos de # suportados por peering** |4000 por predefinição, 10 000 com o ExpressRoute Premium |200 |200 |
| **Intervalos de endereços IP suportados** |Qualquer endereço IP válido do sua WAN. |Endereços IP públicos pertencentes ao utilizador ou o fornecedor de conectividade. |Endereços IP públicos pertencentes ao utilizador ou o fornecedor de conectividade. |
| **COMO o número de requisitos** |Públicas e privadas como números. Tem de ser proprietário público como número, se optar por utilizar um. |Públicas e privadas como números. No entanto, tem de provar propriedade dos endereços IP públicos. |Públicas e privadas como números. No entanto, tem de provar propriedade dos endereços IP públicos. |
| **Protocolos IP suportados**| IPv4 | IPv4 | IPv4, IPv6 |
| **Encaminhamento endereços de IP da Interface** |Endereços IP público e de RFC1918 |Endereços IP públicos registados para si nos registos do encaminhamento. |Endereços IP públicos registados para si nos registos do encaminhamento. |
| **Suporte de MD5 Hash** |Sim |Sim |Sim |

(*) Requer o escalão SKU de suplemento Premium

Pode optar por ativar uma ou mais domínios de encaminhamento como parte do seu circuito do ExpressRoute. Pode optar por todos os domínios de encaminhamento colocados o mesmo VPN se pretender combiná-las num único domínio de encaminhamento. Também pode colocá-los com domínios de encaminhamento diferentes, semelhantes ao diagrama. A configuração recomendada é que o peering privado está ligado diretamente à rede principal e o público e ligações de peering da Microsoft estão ligadas à sua rede de Perímetro.

Se optar por ter todas as sessões de peering três, tem de ter pares de três das sessões de BGP (um par de cada tipo de peering). Os pares de sessão BGP fornecem uma ligação de elevada disponibilidade. Se estiver a ligar através de fornecedores de conectividade 2 camada, é responsável por configurar e gerir o encaminhamento. Pode saber mais, revendo o [fluxos de trabalho](expressroute-workflows.md) para configurar o ExpressRoute.

## <a name="next-steps"></a>Passos seguintes
* Encontre um fornecedor de serviços. Consulte [fornecedores e localizações do ExpressRoute service](expressroute-locations.md).
* Confirme que todos os pré-requisitos são cumpridos. Veja os [Pré-requisitos do ExpressRoute](expressroute-prerequisites.md).
* Configurar a ligação do ExpressRoute.
  * [Criar e gerir circuitos do ExpressRoute](expressroute-howto-circuit-portal-resource-manager.md)
  * [Configurar o encaminhamento (peering) dos circuitos do ExpressRoute](expressroute-howto-routing-portal-resource-manager.md)

