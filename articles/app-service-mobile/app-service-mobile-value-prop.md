---
title: "Sobre Aplicações Móveis no Serviço de Aplicações do Azure"
description: "Conheça as vantagens que o Serviço de Aplicações traz para as suas aplicações móveis."
services: app-service\mobile
documentationcenter: 
author: ggailey777
manager: yochayk
editor: 
ms.assetid: 4e96cb9d-a632-4cf6-8219-0810d8ade3f9
ms.service: app-service-mobile
ms.workload: na
ms.tgt_pltfrm: mobile-multiple
ms.devlang: na
ms.topic: hero-article
ms.date: 10/01/2016
ms.author: glenga
ms.translationtype: HT
ms.sourcegitcommit: 646886ad82d47162a62835e343fcaa7dadfaa311
ms.openlocfilehash: ac35ff9fe1c5f315c4de08de951f505627ec412b
ms.contentlocale: pt-pt
ms.lasthandoff: 08/24/2017

---
# <a name="getting-started"> </a>Sobre Aplicações Móveis no Serviço de Aplicações do Azure
O Serviço de Aplicações do Azure é uma oferta de [plataforma como um serviço](https://azure.microsoft.com/overview/what-is-paas/) (PaaS) completamente gerida para programadores profissionais. O serviço oferece um conjunto avançado de capacidades para cenários Web, móveis e de integração. 

A funcionalidade Aplicações Móveis do Serviço de Aplicações do Azure oferece aos programadores de empresas e aos integradores de sistema uma plataforma de programação de aplicações móveis altamente dimensionável e globalmente disponível.

![Descrição geral visual das capacidades de Aplicações Móveis](./media/app-service-mobile-value-prop/overview.png)

## <a name="why-mobile-apps"></a>Porquê Mobile Apps?
Com a funcionalidade Aplicações Móveis, pode:

* **Compilar aplicações de plataformas nativas e cruzadas**: esteja a compilar aplicações nativas Windows, Android e iOS ou aplicações de plataformas cruzadas Xamarin ou Cordova (PhoneGap), pode tirar partido do Serviço de Aplicações com SDKs nativos.
* **Ligar aos seus sistemas empresariais**: com a funcionalidade Aplicações Móveis, pode adicionar um início de sessão empresarial em minutos e ligar-se à sua empresa no local ou aos recursos na cloud.
* **Compilar aplicações preparadas para trabalho offline com sincronização de dados**: torne a sua mão-de-obra móvel mais produtiva através da compilação de aplicações que funcionam offline e utilize Mobile Apps para sincronizar dados em segundo plano quando existe conectividade com qualquer uma das suas fontes de dados empresariais ou APIs software como um serviço (SaaS).
* **Notificações push para milhões em segundos**: interaja com os seus clientes com notificações push instantâneas em qualquer dispositivo, personalizadas para as suas necessidades e enviadas no momento ideal.

## <a name="mobile-apps-features"></a>Funcionalidades das Aplicações Móveis
As seguintes funcionalidades são importantes para o desenvolvimento móvel preparados para nuvem:

* **Autenticação e autorização**: selecione a partir de uma lista crescente de fornecedores de identidade, incluindo o Azure Active Directory, uma autenticação empresarial e fornecedores de rede sociais, como as contas do Facebook, do Google, do Twitter e da Microsoft. As Aplicações Móveis oferecem um serviço OAuth 2.0 para cada fornecedor. Também é possível integrar o SDK para o fornecedor de identidade para funcionalidades específicas do fornecedor.

    Saiba mais sobre as nossas [funcionalidades de autenticação].

* **Acesso a dados**: as Aplicações Móveis oferecem uma origem de dados OData v3 compatível com dispositivos móveis que está ligada à Base de Dados SQL do Azure ou a um SQL Server no local. Uma vez que este serviço pode ser baseado em Entity Framework, pode integrar facilmente com outros fornecedores de dados NoSQL e SQL, incluindo [Armazenamento de Tabelas do Azure], MongoDB, [Azure Cosmos DB] e fornecedores de API SaaS, como o Office 365 e Salesforce.com.

* **Sincronização offline**: os nossos SDKS do cliente facilitam a compilação de aplicações móveis robustas e reativas que funcionam com um conjunto de dados offline. Pode sincronizar este conjunto de dados automaticamente com os dados de back-end, incluindo o suporte de resolução de conflitos.

  Saiba mais sobre as nossas [funcionalidades de dados].

* **Notificações push**: os nossos SDKS do cliente estão totalmente integrados com as capacidades de registo dos Hubs de Notificação do Azure, para que possa enviar notificações push para milhões de utilizadores em simultâneo.

  Saiba mais sobre as nossas [funcionalidades de notificação push].

* **SDKs do Cliente**: fornecemos um conjunto completo de SDKs do cliente que abrangem o desenvolvimento nativo ([iOS], [Android] e [Windows]), desenvolvimento de plataformas cruzadas ([Xamarin.iOS e Xamarin.Android], [Xamarin.Forms]) e desenvolvimento de aplicações híbridas ([Apache Cordova]). Cada SDK do Cliente está disponível com uma licença MIT e é open source.

## <a name="azure-app-service-features"></a>Funcionalidades do Serviço de Aplicações do Azure
As seguintes funcionalidades da plataforma são úteis para sites de produção móveis:

* **Escala automática**: com o Serviço de Aplicações pode aumentar verticalmente ou horizontalmente de forma rápida, para lidar com qualquer carga de cliente a receber. Selecione manualmente o número e tamanho das VMs ou configure a escala automática de forma a dimensionar o back-end da sua aplicação móvel com base na carga ou na agenda.

  Saiba mais sobre a [escala automática].

* **Ambientes de teste**: o Serviço de Aplicações pode executar várias versões do seu site, para que possa executar testes A/B, testar a produção como parte de um plano DevOps maior e realizar testes no local de um novo back-end.

  Saiba mais sobre os [ambientes de teste].

* **Implementação contínua**: o Serviço de Aplicações pode ser integrado com sistemas de gestão de cadeias de fornecimento (SCM) comuns, para que possa implementar automaticamente uma nova versão do seu back-end ao enviar para um ramo do seu sistema SCM.

  Saiba mais sobre as [opções de implementação].

* **Redes virtuais**: o Serviço de Aplicações pode ligar-se a recursos no local através de uma rede virtual, o Azure ExpressRoute ou ligações híbridas.

  Saiba mais sobre [ligações híbridas], [redes virtuais] e [ExpressRoute].

* **Ambientes isolados e dedicados**: pode executar o Serviço de Aplicações num ambiente completamente isolado e dedicado para uma execução segura de aplicações do Serviço de Aplicações do Azure numa escala elevada. Este ambiente é ideal para cargas de trabalho de aplicações que exijam uma escala, isolamento ou um acesso de rede elevados.

  Saiba mais sobre os [ambientes do Serviço de Aplicações].

## <a name="next-steps"></a>Passos seguintes

Para começar a utilizar as Aplicações Móveis no Serviço de Aplicações do Azure, conclua o tutorial [introdução] . O tutorial abrange as noções básicas da produção de um back-end móvel e cliente da sua preferência. Inclui também a integração de autenticação, sincronização offline e notificações push. Pode concluir o tutorial várias vezes, uma vez para cada aplicação de cliente.

Para obter mais informações sobre as Aplicações Móveis, veja o nosso [mapa de aprendizagem].
Para obter mais informações sobre a plataforma do Serviço de Aplicações do Azure, veja [Serviço de Aplicações do Azure].

<!-- URLs. -->
[Migrate your mobile service to App Service]: app-service-mobile-migrating-from-mobile-services.md
[Serviço de Aplicações do Azure]: ../app-service/app-service-value-prop-what-is.md
[introdução]: app-service-mobile-ios-get-started.md
[Armazenamento de Tabelas do Azure]:../cosmos-db/table-storage-how-to-use-dotnet.md
[Azure Cosmos DB]: ../cosmos-db/documentdb-get-started.md
[funcionalidades de autenticação]: ./app-service-mobile-auth.md
[funcionalidades de dados]: ./app-service-mobile-offline-data-sync.md
[funcionalidades de notificação push]: ../notification-hubs/notification-hubs-push-notification-overview.md
[iOS]: ./app-service-mobile-ios-how-to-use-client-library.md
[Android]: ./app-service-mobile-android-how-to-use-client-library.md
[Windows]: ./app-service-mobile-dotnet-how-to-use-client-library.md
[Xamarin.iOS e Xamarin.Android]: ./app-service-mobile-dotnet-how-to-use-client-library.md
[Xamarin.Forms]: ./app-service-mobile-xamarin-forms-get-started.md
[Apache Cordova]: ./app-service-mobile-cordova-how-to-use-client-library.md
[escala automática]: ../app-service-web/web-sites-scale.md
[ambientes de teste]: ../app-service-web/web-sites-staged-publishing.md
[opções de implementação]: ../app-service-web/web-sites-deploy.md
[ligações híbridas]: ../app-service-web/web-sites-hybrid-connection-get-started.md
[redes virtuais]: ../app-service-web/web-sites-integrate-with-vnet.md
[ExpressRoute]: ../app-service-web/app-service-app-service-environment-network-configuration-expressroute.md
[ambientes do Serviço de Aplicações]: ../app-service-web/app-service-app-service-environment-intro.md
[mapa de aprendizagem]: https://azure.microsoft.com/en-us/documentation/learning-paths/appservice-mobileapps/

