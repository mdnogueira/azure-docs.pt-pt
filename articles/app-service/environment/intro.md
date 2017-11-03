---
title: "Introdução ao ambientes do App Service do Azure"
description: "Breve descrição geral dos ambientes do App Service do Azure"
services: app-service
documentationcenter: na
author: ccompy
manager: stefsch
ms.assetid: 3c7eaefa-1850-4643-8540-428e8982b7cb
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/13/2017
ms.author: ccompy
ms.openlocfilehash: 322cf2ebbe83d00fcebcec618e07141d26f4f255
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="introduction-to-app-service-environments"></a>Introdução ao ambientes do App Service #
 
## <a name="overview"></a>Descrição geral ##

Ambiente de serviço de aplicações do Azure é uma funcionalidade do App Service do Azure que fornece um ambiente completamente isolado e dedicado para execução segura de aplicações do App Service numa escala elevada. Esta capacidade pode alojar as aplicações web, [aplicações móveis][mobileapps], as API apps, e [funções][Functions].

Ambientes do App Service (ASEs) são adequados para cargas de trabalho de aplicações que requerem:

- Muito grande escala.
- Isolamento e um acesso de rede.
- Utilização elevada da memória.

Os clientes podem criar vários ASEs numa única região do Azure ou em várias regiões do Azure. Esta flexibilidade facilita ASEs ideal para aumentar horizontalmente camadas da aplicação sem monitorização de estado para suportar cargas de trabalho RPS elevadas.

ASEs isoladas-se de que apenas as aplicações de um único cliente em execução e sempre são implementadas numa rede virtual. Os clientes têm um controlo detalhado sobre o tráfego de rede de aplicação de entrada e saída. Aplicações podem estabelecer ligações seguras de alta velocidade ao longo de VPNs aos recursos da empresa no local.

* ASEs ativar alta escala aplicação aloja com acesso à rede segura. Para obter mais informações, consulte o [descrição profunda AzureCon](https://azure.microsoft.com/documentation/videos/azurecon-2015-deploying-highly-scalable-and-secure-web-and-mobile-apps/) no ASEs.
* Vários ASEs podem ser utilizados para aumentar horizontalmente. Para obter mais informações, consulte [como configurar requisitos de espaço de aplicação geo-distribuição](app-service-app-service-environment-geo-distributed-scale.md).
* ASEs podem ser utilizados para configurar a arquitetura de segurança, conforme mostrado na descrição profunda AzureCon. Para ver como a arquitetura de segurança apresentada na descrição profunda AzureCon foi configurada, consulte o [artigo sobre como implementar uma arquitetura de segurança por camadas](app-service-app-service-environment-layered-security.md) com ambientes do App Service.
* Aplicações em execução em ASEs podem ter o respetivo acesso gated pelos dispositivos a montante, tais como firewalls de aplicação web (WAFs). Para obter mais informações, consulte [configurar uma WAF para ambientes do App Service](app-service-app-service-environment-web-application-firewall.md).

## <a name="dedicated-environment"></a>Ambiente dedicado ##

ASE dedicado exclusivamente para uma única subscrição e pode alojar 100 instâncias. O intervalo pode abranger 100 instâncias de um único plano de serviço de aplicações para 100 planos de serviço de aplicações de instância única e tudo entre.

ASE é composto por front-ends e profissionais. Front-ends são responsáveis por terminação de HTTP/HTTPS e o balanceamento de carga automático de pedidos de aplicações num ASE. Front-ends são adicionados automaticamente como os planos de serviço de aplicações no ASE são ampliar.

Os funcionários são as funções que alojam aplicações de cliente. Os funcionários estão disponíveis em três tamanhos fixos:

* Um núcleo/3.5 GB de RAM
* Dois núcleos por dia, 7 GB de RAM
* Quatro core/14 GB de RAM

Os clientes não precisam de gerir o front-ends e profissionais. Todos os infraestrutura é adicionado automaticamente como clientes de ampliação os respetivos planos de serviço de aplicações. Medida planos de serviço de aplicações que são criados ou ampliados está num ASE, a infraestrutura necessária é adicionada ou removida conforme adequada.

Há uma taxa mensal flat para ASE que pays para a infraestrutura e não altera o tamanho do ASE. Além disso, não há um custo por núcleo do plano de serviço de aplicações. Todas as aplicações alojadas num ASE estão em Isolated SKU de preço. Para obter informações sobre os preços para ASE, consulte o [preços do serviço de aplicações] [ Pricing] e rever as opções disponíveis para ASEs da página.

## <a name="virtual-network-support"></a>Suporte de rede virtual ##

ASE pode ser criado apenas numa rede virtual do Azure Resource Manager. Para obter mais informações sobre redes virtuais do Azure, consulte o [virtual do Azure redes FAQ](https://azure.microsoft.com/documentation/articles/virtual-networks-faq/). ASE sempre existe numa rede virtual e, mais precisamente, uma sub-rede de uma rede virtual. Pode utilizar as funcionalidades de segurança de redes virtuais para controlar as comunicações de rede de entrada e saída para as suas aplicações.

ASE pode ser o acesso à internet com um endereço IP público ou interno com acesso com apenas um endereço de (ILB) do Balanceador de carga interno do Azure.

[Grupos de segurança de rede] [ NSGs] restringir comunicações de rede de entrada para a sub-rede onde reside ASE. Pode utilizar os NSGs para executar aplicações atrás montante dispositivos e serviços, tais como WAFs e fornecedores de SaaS de rede.

Aplicações frequentemente também tem de aceder a recursos empresariais, tais como bases de dados internos e serviços web. Se implementar o ASE numa rede virtual que tenha uma ligação VPN à rede no local, as aplicações a ASE podem aceder os recursos no local. Esta capacidade é verdadeira, independentemente se a VPN é uma [site para site](https://azure.microsoft.com/documentation/articles/vpn-gateway-site-to-site-create/) ou [Azure ExpressRoute](http://azure.microsoft.com/services/expressroute/) VPN.

Para obter mais informações sobre como ASEs trabalhar com redes virtuais e redes no local, consulte [considerações de rede do ambiente de serviço de aplicações][ASENetwork].

## <a name="app-service-environment-v1"></a>Ambiente do Serviço de Aplicações v1 ##

Ambiente de serviço de aplicações tem duas versões: ASEv1 e ASEv2. As informações foi com base no ASEv2. Esta secção mostra as diferenças entre ASEv1 e ASEv2. 

ASEv1, é necessário gerir todos os recursos manualmente. Que inclui o front-ends, funcionários e endereços IP utilizados para SSL baseado em IP. Antes de pode ampliar o plano de serviço de aplicações, terá primeiro aumentar horizontalmente o conjunto de trabalho onde pretende alojá-la.

ASEv1 utiliza um modelo de preços diferentes de ASEv2. ASEv1, paga para cada núcleos alocados. Isto inclui núcleos utilizados para front-ends ou trabalhadores que não alojam quaisquer cargas de trabalho. ASEv1, o tamanho de dimensionamento máximo predefinido de ASE é 55 total de anfitriões. Que inclui os trabalhadores e front-ends. Uma vantagem em ASEv1 é que pode ser implementada numa rede virtual clássica e uma rede virtual do Gestor de recursos. Para saber mais sobre ASEv1, consulte o artigo [introdução do ambiente de serviço de aplicações v1][ASEv1Intro].

<!--Links-->
[Intro]: ./intro.md
[MakeExternalASE]: ./create-external-ase.md
[MakeASEfromTemplate]: ./create-from-template.md
[MakeILBASE]: ./create-ilb-ase.md
[ASENetwork]: ./network-info.md
[UsingASE]: ./using-an-ase.md
[UDRs]: ../../virtual-network/virtual-networks-udr-overview.md
[NSGs]: ../../virtual-network/virtual-networks-nsg.md
[ConfigureASEv1]: app-service-web-configure-an-app-service-environment.md
[ASEv1Intro]: app-service-app-service-environment-intro.md
[webapps]: ../app-service-web-overview.md
[mobileapps]: ../../app-service-mobile/app-service-mobile-value-prop.md
[Functions]: ../../azure-functions/index.yml
[Pricing]: http://azure.microsoft.com/pricing/details/app-service/
[ARMOverview]: ../../azure-resource-manager/resource-group-overview.md
[ConfigureSSL]: ../web-sites-purchase-ssl-web-site.md
[Kudu]: http://azure.microsoft.com/resources/videos/super-secret-kudu-debug-console-for-azure-web-sites/
[ASEWAF]: app-service-app-service-environment-web-application-firewall.md
[AppGW]: ../../application-gateway/application-gateway-web-application-firewall-overview.md
