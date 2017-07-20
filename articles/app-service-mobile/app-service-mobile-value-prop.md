---
title: "O que são Mobile Apps"
description: "Conheça as vantagens que o App Service traz para as suas Mobile Apps."
services: app-service\mobile
documentationcenter: 
author: dhei
manager: yochayk
editor: 
ms.assetid: 4e96cb9d-a632-4cf6-8219-0810d8ade3f9
ms.service: app-service-mobile
ms.workload: na
ms.tgt_pltfrm: mobile-multiple
ms.devlang: na
ms.topic: hero-article
ms.date: 10/01/2016
ms.author: adrianha
ms.translationtype: Human Translation
ms.sourcegitcommit: db034a8151495fbb431f3f6969c08cb3677daa3e
ms.openlocfilehash: 8ce0a0e7b06f85090c07052056ddd67b97b2ee8b
ms.contentlocale: pt-pt
ms.lasthandoff: 04/29/2017

---
# <a name="getting-started"> </a>O que são as Aplicações Móveis?
O Serviço de Aplicações do Azure é uma [Plataforma como Serviço](https://azure.microsoft.com/overview/what-is-paas/) (PaaS) para programadores profissionais que fornece um conjunto avançado de capacidades para cenários Web, móveis e de integração. As *Mobile Apps* no *App Service do Azure* oferecem uma plataforma de desenvolvimento de aplicações móveis altamente dimensionável e globalmente disponíveis para Programadores Empresariais e Integradores de Sistemas que fornece um conjunto avançado de capacidades para programadores móveis.

![Mobile Apps](./media/app-service-mobile-value-prop/overview.png)

## <a name="why-mobile-apps"></a>Porquê Mobile Apps?
As *Mobile Apps* no *App Service do Azure* oferecem uma plataforma de desenvolvimento de aplicações móveis altamente dimensionável e globalmente disponíveis para Programadores Empresariais e Integradores de Sistemas que fornece um conjunto avançado de capacidades para programadores móveis. As Mobile Apps permitem:

* **Compilação de aplicações de plataformas nativas e cruzadas** – esteja a compilar aplicações nativas Windows, Android e iOS ou aplicações de plataformas cruzadas Xamarin ou Cordova (Phonegap), pode tirar partido do App Service utilizando SDKs nativos.
* **Ligação aos seus sistemas empresariais** – com as Mobile Apps, é possível adicionar um início de sessão empresarial em minutos e ligar-se à sua empresa no local ou aos recursos na nuvem.
* **Compilação de aplicações preparadas para trabalho offline com sincronização de dados** – torne a sua mão-de-obra móvel produtiva através da compilação de aplicações que funcionam offline e utilize Mobile Apps para sincronizar dados em segundo plano quando existe conectividade com qualquer uma das suas fontes de dados empresariais ou APIs SaaS.
* **Notificações Push para milhões em segundos** – interaja com os seus clientes com notificações push instantâneas em qualquer dispositivo, personalizadas para as suas necessidades, enviadas no momento ideal.

## <a name="mobile-app-features"></a>Funcionalidades das Mobile Apps
As seguintes funcionalidades são importantes para o desenvolvimento móvel preparados para nuvem:

* **Autenticação e Autorização** - Selecione a partir de uma lista crescente de fornecedores de identidade, incluindo o Azure Active Directory, uma autenticação empresarial e fornecedores de rede sociais, como o Facebook, Google, Twitter e uma Conta Microsoft.  As Mobile Apps do Azure fornecem um serviço OAuth 2.0 para cada fornecedor.  Também é possível integrar o SDK para o fornecedor de identidade para funcionalidades específicas do fornecedor.

  Saiba mais sobre as nossas [funcionalidades de autenticação].
* **Acesso a Dados** – As Aplicações Móveis do Azure fornecem uma origem de dados OData v3 compatível com dispositivos móveis, ligada ao SQL Azure ou a um SQL Server no local.  Este serviço pode ser baseado em Entity Framework, permitindo uma fácil integração com outros fornecedores de dados NoSQL e SQL, incluindo [Table Storage do Azure], MongoDB, [DocumentDB] e fornecedores de API SaaS, como o Office 365 e Salesforce.com.
* **Sincronização Offline** - Os nossos SDKs do Cliente tornam mais fácil a compilação de aplicações robustas e reativas que funcionam com um conjunto de dados offline, que podem ser sincronizados automaticamente com os dados de back-end, incluindo suporte para resolução de conflitos.

  Saiba mais sobre as nossas [funcionalidades de dados].
* **Notificações Push** - Os nossos SDKS do Cliente estão totalmente integrados com as capacidades de registo perfeitamente integrar com as funcionalidades de registo dos Notification Hubs do Azure, permitindo enviar notificações push para milhões de utilizadores em simultâneo.

  Saiba mais sobre as nossas [funcionalidades de notificação push].
* **SDKs do Cliente** - Fornecemos um conjunto completo de SDKs do Cliente que abrangem o desenvolvimento nativo ([iOS], [Android] e [Windows]), desenvolvimento de plataformas cruzadas ([Xamarin para iOS e Android], [Formulários Xamarin]) e desenvolvimento de aplicações híbridas ([Apache Cordova]).  Cada SDK do Cliente está disponível com uma licença MIT e é open source.

## <a name="azure-app-service-features"></a>Funcionalidades do App Service do Azure.
As seguintes funcionalidades da plataforma são geralmente úteis para sites de produção móveis.

* **Escala automática** - O App Service permite um rápido aumento vertical ou horizontal de forma a lidar com qualquer carga de cliente a receber. Selecione manualmente o número e tamanho das VMs ou configure a escala automática de forma a dimensionar o back-end da sua aplicação móvel com base na carga ou na agenda.

  Saiba mais sobre a [escala automática].
* **Ambientes de Teste** - O App Service pode executar várias versões do seu site, permitindo executar testes A/B, testar a produção como parte de um plano DevOps maior e realizar testes no local de um novo back-end.

  Saiba mais sobre os [ambientes de teste].
* **Implementação Contínua** - O App Service pode ser integrado com sistemas SCM comuns, permitindo a implementação automática de uma nova versão do seu back-end ao enviar para um ramo do seu sistema SCM.

  Saiba mais sobre as [opções de implementação].
* **Redes Virtuais** - O Serviço de Aplicações pode ligar-se a recursos no local através de uma rede virtual, ExpressRoute ou ligações híbridas.

  Saiba mais sobre [ligações híbridas], [redes virtuais] e [ExpressRoute].
* **Ambientes Isolados/Dedicados** – O App Service pode ser executado num ambiente completamente isolado e dedicado para uma execução segura de aplicações do App Service do Azure numa escala elevada.  Este sistema é ideal para cargas de trabalho de aplicações que exijam uma escala, isolamento ou um acesso de rede muito elevados.

  Saiba mais sobre os [Ambientes do App Service].

## <a name="getting-started"></a>Introdução
Para começar a utilizar as Mobile Apps, siga o tutorial [Introdução].  Este irá cobrir o básico sobre a produção de um back-end móvel e um cliente à sua escolha e, depois, a integração da autenticação, sincronização offline e notificações push.  Pode consultar várias vezes o tutorial [Introdução] – uma vez para cada aplicação de cliente.

Para obter mais informações sobre as Mobile Apps do Azure, consulte o nosso [mapa de aprendizagem].
Para obter mais informações sobre a plataforma do App Service do Azure, consulte [App Service do Azure].

> [!NOTE]
> Se pretender começar a utilizar o App Service do Azure antes de se inscrever numa conta do Azure, aceda a [Experimentar o App Service](https://azure.microsoft.com/try/app-service/mobile/), onde pode criar de imediato uma aplicação Web de arranque de curta duração no App Service. Sem cartões de crédito; sem compromissos.
>
>

<!-- URLs. -->
[Migrate your Mobile Service to App Service]: app-service-mobile-migrating-from-mobile-services.md
[App Service do Azure]: ../app-service/app-service-value-prop-what-is.md
[Introdução]: app-service-mobile-ios-get-started.md
[Table Storage do Azure]: ../storage/storage-dotnet-how-to-use-tables.md
[DocumentDB]: ../documentdb/documentdb-get-started.md
[funcionalidades de autenticação]: ./app-service-mobile-auth.md
[funcionalidades de dados]: ./app-service-mobile-offline-data-sync.md
[funcionalidades de notificação push]: ../notification-hubs/notification-hubs-push-notification-overview.md
[iOS]: ./app-service-mobile-ios-how-to-use-client-library.md
[Android]: ./app-service-mobile-android-how-to-use-client-library.md
[Windows]: ./app-service-mobile-dotnet-how-to-use-client-library.md
[Xamarin para iOS e Android]: ./app-service-mobile-dotnet-how-to-use-client-library.md
[Formulários Xamarin]: ./app-service-mobile-xamarin-forms-get-started.md
[Apache Cordova]: ./app-service-mobile-cordova-how-to-use-client-library.md
[escala automática]: ../app-service-web/web-sites-scale.md
[ambientes de teste]: ../app-service-web/web-sites-staged-publishing.md
[opções de implementação]: ../app-service-web/web-sites-deploy.md
[ligações híbridas]: ../app-service-web/web-sites-hybrid-connection-get-started.md
[redes virtuais]: ../app-service-web/web-sites-integrate-with-vnet.md
[ExpressRoute]: ../app-service-web/app-service-app-service-environment-network-configuration-expressroute.md
[Ambientes do App Service]: ../app-service-web/app-service-app-service-environment-intro.md
[mapa de aprendizagem]: https://azure.microsoft.com/en-us/documentation/learning-paths/appservice-mobileapps/

