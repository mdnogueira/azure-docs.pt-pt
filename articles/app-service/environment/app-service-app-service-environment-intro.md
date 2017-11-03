---
title: "Introdução à aplicação v1 de ambiente de serviço"
description: "Saiba mais sobre a funcionalidade de v1 de ambiente de serviço de aplicações fornece unidades de escala segura, associados a VNet, dedicada para executar todas as suas aplicações."
services: app-service
documentationcenter: 
author: stefsch
manager: erikre
editor: 
ms.assetid: 78e6d4f5-da46-4eb5-a632-b5fdc17d2394
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: ccompy
ms.openlocfilehash: b13f726ada6deba67d5ed484eca769fed354e400
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="introduction-to-app-service-environment-v1"></a>Introdução à aplicação v1 de ambiente de serviço

> [!NOTE]
> Este artigo é sobre o ambiente de serviço de aplicações v1.  Há uma versão mais recente do ambiente de serviço de aplicações que são mais fáceis de utilizar e é executada na infraestrutura mais poderosa. Para saber mais sobre o novo início de versão com o [introdução ao ambiente de serviço de aplicações](intro.md).
> 

## <a name="overview"></a>Descrição geral
Um ambiente de serviço de aplicações é um [Premium] [ PremiumTier] opção plano do serviço [App Service do Azure](../app-service-web-overview.md) que fornece um ambiente completamente isolado e dedicado para a execução de forma segura Aplicações do App Service do Azure numa escala elevada, incluindo Web Apps, aplicações móveis e API Apps.  

Ambientes de serviço de aplicações são ideais para cargas de trabalho de aplicações que requerem:

* Muito grande escala
* Isolamento e de acesso à rede segura

Os clientes podem criar vários ambientes do App Service numa única região do Azure, bem como em várias regiões do Azure.  Isto faz com que ambientes do App Service ideal para aumentar horizontalmente camadas de aplicações sem estado para suportar cargas de trabalho RPS elevadas.

Ambientes do App Service isoladas-se de que apenas as aplicações de um único cliente em execução e sempre são implementados numa rede virtual.  Os clientes não têm controlo detalhado sobre o tráfego de rede de aplicação de entrada e saída e as aplicações podem estabelecer ligações seguras de alta velocidade através de redes virtuais aos recursos da empresa no local.

Para uma descrição geral de como ambientes do App Service permitem escala elevada e proteger o acesso de rede, consulte o [descrição profunda AzureCon] [ AzureConDeepDive] em ambientes do App Service!

Para uma descrição profunda horizontalmente dimensionamento utilizando vários ambientes do App Service, consulte o artigo sobre como configurar um [requisitos de espaço de aplicação geo-distribuição][GeodistributedAppFootprint].

Para ver como a arquitetura de segurança apresentada na descrição profunda AzureCon foi configurada, consulte o artigo na implementação de um [em camadas a arquitetura de segurança](app-service-app-service-environment-layered-security.md) com ambientes do App Service.

Aplicações em execução em ambientes do App Service podem ter o respetivo acesso gated pelos dispositivos a montante, tais como firewalls de aplicação web (WAF).  O artigo [configurar uma WAF para ambientes do App Service](app-service-app-service-environment-web-application-firewall.md) abrange neste cenário. 

[!INCLUDE [app-service-web-to-api-and-mobile](../../../includes/app-service-web-to-api-and-mobile.md)]

## <a name="dedicated-compute-resources"></a>Recursos de computação dedicada
Todos os recursos de computação num ambiente de serviço de aplicações estão dedicados exclusivamente para uma única subscrição e um ambiente de serviço de aplicações pode ser configurado com até cinquenta (50) recursos de computação para utilização exclusiva por uma única aplicação.

Um ambiente de serviço de aplicações é composto por um agrupamento de recursos de computação de front-end, bem como agrupamentos de recursos de computação de trabalho de uma a três. 

O conjunto de front-end contém recursos de computação responsável por terminação de SSL como balanceamento de carga bem automático de pedidos de aplicações dentro de um ambiente de serviço de aplicações. 

Cada conjunto de trabalho contém recursos de computação alocados ao [planos do App Service][AppServicePlan], que por sua vez, conter uma ou mais aplicações do App Service do Azure.  Uma vez que podem existir até três conjuntos de trabalho diferentes num ambiente de serviço de aplicações, tem a flexibilidade para escolher os recursos de computação diferentes para cada conjunto de trabalho.  

Por exemplo, isto permite-lhe criar um conjunto de trabalho com menos eficiente recursos de computação para planos do App Service pretendido para aplicações de desenvolvimento ou teste.  Um conjunto de trabalho segundo (ou mesmo terceiro) utilizar recursos de computação mais poderosos, concebidos para planos de serviço de aplicações a executar aplicações de produção.

Para obter mais detalhes sobre a quantidade de recursos de computação disponíveis para os conjuntos de front-end e de trabalho, consulte [como configurar um ambiente de serviço de aplicações][HowToConfigureanAppServiceEnvironment].  

Para obter detalhes sobre os tamanhos de recursos de computação disponíveis suportados num ambiente de serviço de aplicações, consulte o [preços do App Service] [ AppServicePricing] página e rever as opções disponíveis para ambientes do App Service no escalão de preços Premium.

## <a name="virtual-network-support"></a>Suporte de rede virtual
Um ambiente de serviço de aplicações podem ser criado na **ou** uma rede virtual do Azure Resource Manager, **ou** uma rede virtual do modelo de implementação clássica ([obter mais informações sobre redes virtuais][MoreInfoOnVirtualNetworks]).  Uma vez que um ambiente de serviço de aplicações sempre existe uma rede virtual e, mais precisamente dentro de uma sub-rede de uma rede virtual, pode tirar partido das funcionalidades de segurança de redes virtuais para controlar as comunicações de rede de entrada e saída.  

Um ambiente de serviço de aplicações pode ser qualquer um dos Internet com um endereço IP público ou interno com acesso com apenas um endereço de Azure Balanceador de carga interno (ILB).

Pode utilizar [grupos de segurança de rede] [ NetworkSecurityGroups] para restringir comunicações de rede de entrada para a sub-rede onde reside um ambiente de serviço de aplicações.  Isto permite-lhe executar aplicações atrás montante dispositivos e serviços, tais como firewalls de aplicação web e fornecedores de SaaS de rede.

Aplicações frequentemente também tem de aceder a recursos empresariais, tais como bases de dados internos e serviços web.  Uma abordagem comum é que estes pontos finais disponíveis apenas para tráfego de rede interno que circulam dentro de uma rede virtual do Azure.  Depois de um ambiente de serviço de aplicações é associado à mesma rede virtual que os serviços internos, aplicações em execução no ambiente podem aceder aos mesmos, incluindo acessíveis através de pontos finais [Site a Site] [ SiteToSite] e [Azure ExpressRoute] [ ExpressRoute] ligações.

Para obter mais detalhes sobre como funcionam os ambientes do App Service com redes virtuais e redes no local Consulte os artigos seguintes no [arquitetura de rede][NetworkArchitectureOverview], [controlar o tráfego de entrada][ControllingInboundTraffic], e [em segurança a ligar ao back-ends][SecurelyConnectingToBackends]. 

## <a name="getting-started"></a>Introdução
Para começar com ambientes do App Service, consulte o artigo [como para criar uma aplicação ambiente de serviço][HowToCreateAnAppServiceEnvironment]

Para obter uma descrição geral da arquitetura de rede do ambiente de serviço de aplicações, consulte o [descrição geral da arquitetura de rede] [ NetworkArchitectureOverview] artigo.

Para obter detalhes sobre como utilizar um ambiente de serviço de aplicações com o ExpressRoute, consulte o seguinte artigo no [Express Route e ambientes do App Service][NetworkConfigDetailsForExpressRoute].

[!INCLUDE [app-service-web-try-app-service](../../../includes/app-service-web-try-app-service.md)]

<!-- LINKS -->
[PremiumTier]: http://azure.microsoft.com/pricing/details/app-service/
[MoreInfoOnVirtualNetworks]: https://azure.microsoft.com/documentation/articles/virtual-networks-faq/
[AppServicePlan]: ../azure-web-sites-web-hosting-plans-in-depth-overview.md
[HowToCreateAnAppServiceEnvironment]: app-service-web-how-to-create-an-app-service-environment.md
[LogicApps]: http://azure.microsoft.com/documentation/articles/app-service-logic-what-are-logic-apps/
[AzureConDeepDive]:  https://azure.microsoft.com/documentation/videos/azurecon-2015-deploying-highly-scalable-and-secure-web-and-mobile-apps/
[GeodistributedAppFootprint]:  app-service-app-service-environment-geo-distributed-scale.md
[NetworkSecurityGroups]: https://azure.microsoft.com/documentation/articles/virtual-networks-nsg/
[SiteToSite]: https://azure.microsoft.com/documentation/articles/vpn-gateway-site-to-site-create/
[ExpressRoute]: http://azure.microsoft.com/services/expressroute/
[HowToConfigureanAppServiceEnvironment]:  app-service-web-configure-an-app-service-environment.md
[ControllingInboundTraffic]:  app-service-app-service-environment-control-inbound-traffic.md
[SecurelyConnectingToBackends]:  app-service-app-service-environment-securely-connecting-to-backend-resources.md
[NetworkArchitectureOverview]:  app-service-app-service-environment-network-architecture-overview.md
[NetworkConfigDetailsForExpressRoute]:  app-service-app-service-environment-network-configuration-expressroute.md
[AppServicePricing]: http://azure.microsoft.com/pricing/details/app-service/ 

<!-- IMAGES -->


