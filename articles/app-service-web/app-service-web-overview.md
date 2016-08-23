<properties
    pageTitle="Descrição geral das Web Apps | Microsoft Azure"
    description="Saiba como o App Service do Azure o ajuda a desenvolver e alojar Web Apps"
    services="app-service\web"
    documentationCenter=""
    authors="jaime-espinosa"
    manager="wpickett"
    editor=""/>

<tags
    ms.service="app-service-web"
    ms.workload="web"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="05/25/2016"
    ms.author="rachelap"/>

# Descrição geral das Web Apps

As *Web Apps do App Service* são uma plataforma de computação completamente gerida, está otimizada para o alojamento de Web sites e de Web Apps. Esta oferta de [plataforma como serviço](https://en.wikipedia.org/wiki/Platform_as_a_service) (PaaS) do Microsoft Azure permite-lhe concentrar-se na lógica do seu negócio, enquanto o Azure gere a infraestrutura de execução e dimensionamento das suas aplicações.

O seguinte vídeo de 5 minutos apresenta as Web Apps do App Service do Azure.

[AZURE.VIDEO azure-app-service-web-apps-with-yochay-kiriaty]

## O que é uma aplicação Web no App Service?

No App Service, uma *aplicação Web* são os recursos de computação fornecidos pelo Azure para alojar um Web site ou uma aplicação Web.  

Os recursos de computação podem estar em máquinas virtuais (VMs) partilhadas ou dedicadas, consoante o escalão de preço que escolher. O código da aplicação é executado numa VM gerida que está isolada de outros clientes.

O código pode estar em qualquer linguagem ou arquitetura suportada pelo [App Service do Azure](../app-service/app-service-value-prop-what-is.md), tal como ASP.NET, Node.js, Java, PHP ou Python. Também pode executar scripts que utilizam o [PowerShell e outras linguagens de script](web-sites-create-web-jobs.md#acceptablefiles) numa aplicação Web.

Para obter exemplos de cenários típicos de aplicações para os quais possa utilizar as Web Apps, consulte [Cenários de Web Apps](https://azure.microsoft.com/documentation/scenarios/web-app/) e a secção **Cenários e recomendações** da [Comparação entre o App Service do Azure, as Virtual Machines, o Service Fabric e os Cloud Services](choose-web-site-cloud-service-vm.md#scenarios).

## Porquê utilizar Web Apps?

Apresentamos a seguir algumas das principais funcionalidades do App Service que se aplicam às Web Apps:

- **Várias linguagens e arquiteturas** – o App Service tem suporte de primeira classe para ASP.NET, Node.js, Java, PHP e Python. Também pode executar o [PowerShell e outros scripts ou executáveis](../app-service-web/web-sites-create-web-jobs.md) em VMs do App Service.

- **Otimização de DevOps** – configure [a integração e a implementação contínuas](../app-service-web/app-service-continuous-deployment.md) com os Visual Studio Team Services, o GitHub ou o BitBucket. Promova atualizações através de [ambientes de teste](../app-service-web/web-sites-staged-publishing.md). Realize [testes A/B](../app-service-web/app-service-web-test-in-production-get-start.md). Faça a gestão das suas aplicações no App Service utilizando o [Azure PowerShell](../powershell-install-configure.md) ou a [interface de linha de comandos (CLI) de várias plataformas](../xplat-cli-install.md).
 
- **Dimensionamento global com elevada disponibilidade** – aumente [verticalmente](../app-service-web/web-sites-scale.md) ou [horizontalmente](../azure-portal/insights-how-to-scale.md) de forma manual ou automática. Aloje as aplicações em qualquer lugar da infraestrutura do datacenter global da Microsoft e o App Service [SLA](https://azure.microsoft.com/support/legal/sla/app-service/) promete elevada disponibilidade.

- **Ligações a plataformas SaaS e dados no local** – escolha entre mais de 50 [conectores](../connectors/apis-list.md) para sistemas empresariais (como SAP, Siebel e Oracle), serviços SaaS (como o Salesforce e o Office 365) e serviços Internet (como o Facebook e o Twitter). Aceda a dados no local ao utilizar [Ligações Híbridas](../biztalk-services/integration-hybrid-connection-overview.md) e [Azure Virtual Networks](../app-service-web/web-sites-integrate-with-vnet.md).

- **Segurança e conformidade** – o App Service está [em conformidade com ISO, SOC e PCI](https://www.microsoft.com/TrustCenter/).

- **Modelos de aplicação** – escolha entre uma lista extensa de modelos de aplicação no [Azure Marketplace](https://azure.microsoft.com/marketplace/) que lhe permitem utilizar um assistente para instalar software open source popular, como o WordPress, o Joomla e o Drupal.

- **Integração do Visual Studio** – as ferramentas dedicadas do Visual Studio simplificam o trabalho de criar, implementar e depurar.

Além disso, uma aplicação Web pode tirar partido das funcionalidades oferecidas pelas [API Apps](../app-service-api/app-service-api-apps-why-best-platform.md) (tais como suporte de CORS) e pelas [Mobile Apps](../app-service-mobile/app-service-mobile-value-prop.md) (tais como notificações push). Para obter mais informações acerca de tipos de aplicação no App Service, consulte [Descrição geral do App Service do Azure](../app-service/app-service-value-prop-what-is.md).

Para além das Web Apps no App Service, o Azure oferece outros serviços que podem ser utilizados para alojar sites e Web Apps. Para a maioria dos cenários, as Web Apps são a melhor escolha.  Para uma arquitetura de microsserviço, considere o [Service Fabric](https://azure.microsoft.com/documentation/services/service-fabric) e, se necessitar de mais controlo sobre as VMs em que o seu código é executado, considere as [Virtual Machines do Azure](https://azure.microsoft.com/documentation/services/virtual-machines/). Para obter mais informações sobre como escolher entre estes serviços do Azure, consulte a [Comparação entre o App Service do Azure, as Virtual Machines, o Service Fabric e os Cloud Services](choose-web-site-cloud-service-vm.md).

## Introdução

Para começar a implementar código de exemplo numa nova aplicação Web no App Service, siga o tutorial [Implementar a primeira aplicação Web no Azure em 5 minutos](app-service-web-get-started.md). Necessitará de uma conta do Azure gratuita.

Se pretender começar a utilizar o App Service do Azure antes de se inscrever numa conta do Azure, aceda a [Experimentar o App Service](http://go.microsoft.com/fwlink/?LinkId=523751), onde pode criar de imediato uma aplicação Web de arranque de curta duração no App Service. Sem cartões de crédito; sem compromissos.



<!--HONumber=Aug16_HO1-->


