---
title: App Service do Azure para Web Apps e aplicações móveis| Microsoft Docs
description: Saiba como o App Service do Azure o pode ajudar a desenvolver, implementar e gerir Web Apps e móveis.
keywords: serviço de aplicações, serviço de aplicações do azure, custo do serviço de aplicações, dimensionamento, dimensionável, implementação de aplicações, implementação de aplicações do azure, paas, plataforma-como-serviço
services: app-service
documentationcenter: ''
author: omarkmsft
manager: dwrede
editor: jimbe

ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 05/25/2016
ms.author: omark

---
# O que é o Serviço de Aplicações do Azure?
O *App Service* é uma oferta [plataforma-como-um-serviço](https://en.wikipedia.org/wiki/Platform_as_a_service) (PaaS) da Microsoft Azure. Crie Web Apps e móveis para qualquer plataforma ou dispositivo. Integrar as suas aplicações com soluções SaaS, ligue-se com as aplicações no local e automatize os processos empresariais. O Azure executa as suas aplicações em máquinas virtuais (VMs) completamente geridas, com os recursos de MV partilhados ou VMs dedicadas que escolher. 

O App Service inclui as capacidades Web e móveis que disponibilizámos anteriormente em separado como Web sites do Azure e Mobile Services do Azure.  Também inclui novas capacidades para automatizar processos de negócio e o alojar APIs da nuvem. Como um serviço integrado único, o App Service permite-lhe compor vários componentes – sites, back-ends de aplicações móveis, APIs de RESTful e processos de negócio – numa solução única.

O vídeo de 4 minutos que se segue fornece uma breve explicação de como o App Service se relaciona com ofertas do Azure anteriores e o que há de novo no mesmo.

+[AZURE.VIDEO app-service-history-lesson] 

## Porquê utilizar o App Service?
São a seguir apresentadas algumas funcionalidades-chave e capacidades do App Service: 

* **Várias linguagens e arquiteturas** – o App Service tem suporte de primeira classe para ASP.NET, Node.js, Java, PHP e Python. Também pode executar [o Windows PowerShell e outros scripts ou executáveis](../app-service-web/web-sites-create-web-jobs.md) em VMs do App Service.
* **Otimização de DevOps** – configure [a integração e a implementação contínuas](../app-service-web/app-service-continuous-deployment.md) com os Visual Studio Team Services, o GitHub ou o BitBucket. Promova atualizações através de [ambientes de teste](../app-service-web/web-sites-staged-publishing.md). Realize [testes A/B](../app-service-web/app-service-web-test-in-production-get-start.md). Faça a gestão das suas aplicações no App Service utilizando o [Azure PowerShell](../powershell-install-configure.md) ou a [interface de linha de comandos (CLI) de várias plataformas](../xplat-cli-install.md).
* **Dimensionamento global com elevada disponibilidade** – aumente [verticalmente](../app-service-web/web-sites-scale.md) ou [horizontalmente](../azure-portal/insights-how-to-scale.md) de forma manual ou automática. Aloje as aplicações em qualquer lugar da infraestrutura do datacenter global da Microsoft e o App Service [SLA](https://azure.microsoft.com/support/legal/sla/app-service/) promete elevada disponibilidade.
* **Ligações a plataformas SaaS e dados no local** – escolha entre mais de 50 [conectores](../connectors/apis-list.md) para sistemas empresariais (como SAP, Siebel e Oracle), serviços SaaS (como o Salesforce e o Office 365) e serviços Internet (como o Facebook e o Twitter). Aceda a dados no local ao utilizar [Ligações Híbridas](../biztalk-services/integration-hybrid-connection-overview.md) e [Azure Virtual Networks](../app-service-web/web-sites-integrate-with-vnet.md).
* **Segurança e conformidade** – o App Service está [em conformidade com ISO, SOC e PCI](https://www.microsoft.com/TrustCenter/).
* **Modelos de aplicação** – escolha entre uma lista extensa de modelos de aplicação no [Azure Marketplace](https://azure.microsoft.com/marketplace/) que lhe permitem utilizar um assistente para instalar software open source popular, como o WordPress, o Joomla e o Drupal.
* **Integração do Visual Studio** – as ferramentas dedicadas do Visual Studio simplificam o trabalho de criar, implementar e depurar.

## Tipos de aplicação no App Service
O App Service oferece várias *tipos de aplicação*, cada uma concebida para alojar um tipo de carga de trabalho específico:

* [**Web Apps**](../app-service-web/app-service-web-overview.md) – para o alojamento de Web sites e as Web Apps.
* [**Aplicações móveis**](../app-service-mobile/app-service-mobile-value-prop.md) – para o alojamento de back-ends de aplicações móveis.
* [**API Apps**](../app-service-api/app-service-api-apps-why-best-platform.md) – para o alojamento de APIs da nuvem. 
* [**Logic Apps**](../app-service-logic/app-service-logic-what-are-logic-apps.md) – para automatizar o acesso e a utilização de dados em nuvens sem escrever código.

A palavra *aplicação* aqui refere-se ao alojamento de recursos dedicados à execução de uma carga de trabalho. Tendo a “aplicação Web” como exemplo, está provavelmente habituado a pensar numa aplicação Web como os recursos de computação e o código de aplicação que em conjunto fornecem a funcionalidade a um browser. Mas no App Service, uma *aplicação Web* diz respeito aos recursos de computação fornecidos pelo Azure para alojar o seu código de aplicação. Se a aplicação for composta por um front-end Web a um back-end da API RESTful, pode implementar ambos numa aplicação Web ou pode implementar o código de front-end numa aplicação Web e o código de back-end numa aplicação API. A aplicação pode ser composta por várias aplicações de App Service de diferentes tipos.

## Planos do App Service
Os [Planos de App Service](azure-web-sites-web-hosting-plans-in-depth-overview.md) especificam o tipo de recursos de computação nos quais as suas aplicações são executadas. Se necessitar de cargas de tráfego simplificadas, pode utilizar máquinas virtuais (VMs) partilhadas. Para cargas superiores, pode escolher entre vários tamanhos de VMs dedicadas. Várias aplicações do App Service podem partilhar o mesmo plano e podem aumentar vertical ou horizontalmente juntamente com o plano.

Se precisar de mais escalabilidade e o isolamento de rede, pode executar as suas aplicações num [Ambiente do App Service](../app-service-web/app-service-app-service-environment-intro.md). 

## Preços
Para obter informações sobre os custos do App Service, consulte o artigo [Preços do App Service](https://azure.microsoft.com/pricing/details/app-service/). 

## Introdução ao App Service
[Crie uma aplicação Web, aplicação móvel ou aplicação lógica temporária](http://go.microsoft.com/fwlink/?LinkId=523751) de imediato gratuitamente, sem necessidade de um cartão de crédito, sem compromissos, sem problemas.

Ou abra uma [conta do Azure gratuita](https://azure.microsoft.com/pricing/free-trial/), e experimente um dos nossos tutoriais de introdução:

* [Tutorial: Criar uma aplicação Web](../app-service-web/app-service-web-get-started.md)
* [Tutorial: Criar uma aplicação móvel](../app-service-mobile/app-service-mobile-android-get-started.md)
* [Tutorial: Criar uma aplicação API](../app-service-api/app-service-api-dotnet-get-started.md)
* [Tutorial: Criar uma aplicação lógica](../app-service-logic/app-service-logic-create-a-logic-app.md)

<!--HONumber=Sep16_HO3-->


