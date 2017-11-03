---
title: "Descrição geral das Web Apps | Microsoft Docs"
description: Saiba como o App Service do Azure o ajuda a desenvolver e alojar Web Apps
services: app-service\web
documentationcenter: 
author: cephalin
manager: erikre
editor: 
ms.assetid: 94af2caf-a2ec-4415-a097-f60694b860b3
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.date: 01/04/2017
ms.author: cephalin
ms.custom: mvc
ms.openlocfilehash: f8510bb6b412e9af8aad30ba32bc74206c22042f
ms.sourcegitcommit: 804db51744e24dca10f06a89fe950ddad8b6a22d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/30/2017
---
# <a name="web-apps-overview"></a>Descrição geral das Web Apps

*Serviço Web Apps do App Azure* (ou apenas as aplicações Web) é um serviço para o alojamento de aplicações web, as APIs REST, e termina mobile novamente. Pode desenvolver no seu idioma favorito, ser-.NET, .NET Core, Java, Ruby, Node.js, PHP ou Python. Pode executar e dimensionar as aplicações com facilidade no Windows ou Linux VMs (consulte [do serviço de aplicações no Linux](containers/app-service-linux-intro.md)). 

As Web Apps não só adiciona a capacidade do Microsoft Azure para a sua aplicação, tais como segurança, o balanceamento de carga, dimensionamento automático e automatizar a gestão. Também pode tirar partido das respetivas capacidades de DevOps, tais como a implementação contínua provém de VSTS, GitHub, Docker Hub e outras origens, gestão de pacotes, certificados SSL, domínio personalizado e ambientes de teste. 

Com o App Service, paga para os recursos de computação do Azure que utilizar. Os recursos de computação utiliza é determinado pelo _plano do App Service_ que execute as suas aplicações Web. Para obter mais informações, consulte [planos do App Service nas Web Apps do Azure](azure-web-sites-web-hosting-plans-in-depth-overview.md).

O seguinte vídeo de 5 minutos apresenta as Web Apps do App Service do Azure.

>[!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Azure-App-Service-Web-Apps-with-Yochay-Kiriaty/player]
>
>

## <a name="why-use-web-apps"></a>Porquê utilizar Web Apps?
Seguem-se algumas funcionalidades-chaves das Web Apps do App Service:

* **Várias linguagens e arquiteturas** -as Web Apps tem suporte de primeira classe para ASP.NET, ASP.NET Core, Java, Ruby, Node.js, PHP ou Python. Também pode executar [PowerShell e outros scripts ou executáveis](web-sites-create-web-jobs.md) como serviços em segundo plano.
* **Otimização de DevOps** -configurar [integração contínua e a implementação](app-service-continuous-deployment.md) com o Visual Studio Team Services, o GitHub, o BitBucket, o Docker Hub ou o serviço de contentor do Azure. Promova atualizações através de [ambientes de teste](web-sites-staged-publishing.md). Gerir as suas aplicações nas Web Apps utilizando [Azure PowerShell](/powershell/azureps-cmdlets-docs) ou [interface de linha de comandos de várias plataformas (CLI)](/cli/azure/install-azure-cli).
* **Dimensionamento global com elevada disponibilidade** – aumente [verticalmente](web-sites-scale.md) ou [horizontalmente](../monitoring-and-diagnostics/insights-how-to-scale.md) de forma manual ou automática. Aloje as aplicações em qualquer lugar da infraestrutura do datacenter global da Microsoft e o App Service [SLA](https://azure.microsoft.com/support/legal/sla/app-service/) promete elevada disponibilidade.
* **As ligações a dados no local e de plataformas de SaaS** -escolha entre mais de 50 [conectores](../connectors/apis-list.md) para sistemas empresariais (como SAP), serviços SaaS (por exemplo, Salesforce) e serviços internet (como o Facebook). Aceda a dados no local ao utilizar [Ligações Híbridas](../biztalk-services/integration-hybrid-connection-overview.md) e [Azure Virtual Networks](web-sites-integrate-with-vnet.md).
* **Segurança e conformidade** – o App Service está [em conformidade com ISO, SOC e PCI](https://www.microsoft.com/TrustCenter/). Autenticar os utilizadores com [do Azure Active Directory](app-service-mobile-how-to-configure-active-directory-authentication.md) ou com redes social início de sessão ([Google](app-service-mobile-how-to-configure-google-authentication.md), [Facebook](app-service-mobile-how-to-configure-facebook-authentication.md), [Twitter](app-service-mobile-how-to-configure-twitter-authentication.md), e [ Microsoft](app-service-mobile-how-to-configure-microsoft-authentication.md)). Criar [restrições de endereço IP](app-service-ip-restrictions.md) e [gerir identidades de serviço](app-service-managed-service-identity.md).
* **Modelos de aplicação** -escolha entre uma lista extensa de modelos de aplicação no [Azure Marketplace](https://azure.microsoft.com/marketplace/), tais como o WordPress, o Joomla e o Drupal.
* **Integração do Visual Studio** – as ferramentas dedicadas do Visual Studio simplificam o trabalho de criar, implementar e depurar.
* **API e funcionalidades móveis** -as aplicações Web fornece suporte de CORS desative-chave para cenários de RESTful API e simplifica a cenários de aplicações móveis ao ativar a autenticação, sincronização de dados offline, as notificações push e muito mais.
* **Código sem servidor** - executar um fragmento de código ou de script sem ter de aprovisionar ou gerir a infraestrutura explicitamente a pedido e paga apenas o tempo de computação código, na verdade, utiliza (consulte [das funções do Azure](/azure/azure-functions/)).

Para além das Web Apps no App Service, o Azure oferece outros serviços que podem ser utilizados para alojar sites e Web Apps. Para a maioria dos cenários, as Web Apps são a melhor escolha.  Para uma arquitetura de microsserviço, considere [Service Fabric](https://azure.microsoft.com/documentation/services/service-fabric). Se precisar de mais controlo sobre as VMs que o seu código é executado, considere [Virtual Machines do Azure](https://azure.microsoft.com/documentation/services/virtual-machines/). Para obter mais informações sobre como escolher entre estes serviços do Azure, consulte a [Comparação entre o App Service do Azure, as Virtual Machines, o Service Fabric e os Cloud Services](choose-web-site-cloud-service-vm.md).

## <a name="next-steps"></a>Passos seguintes

Crie a sua primeira aplicação web.

> [!div class="nextstepaction"]
> [ASP.NET](app-service-web-get-started-dotnet.md)

> [!div class="nextstepaction"]
> [PHP](app-service-web-get-started-php.md)

> [!div class="nextstepaction"]
> [Node.js](app-service-web-get-started-nodejs.md)

> [!div class="nextstepaction"]
> [Java](app-service-web-get-started-java.md)

> [!div class="nextstepaction"]
> [python](app-service-web-get-started-python.md)

> [!div class="nextstepaction"]
> [HTML](app-service-web-get-started-html.md)

